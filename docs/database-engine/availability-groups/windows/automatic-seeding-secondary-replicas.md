---
title: 自動植入次要複本
description: 了解自動植入如何在 SQL 2016 和更新版本中，將次要複本作為 Always On 可用性群組的一部分來初始化。
ms.custom: seo-lt-2019
ms.date: 11/27/2018
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: how-to
helpviewer_keywords:
- Automatic seeding [SQL Server], secondary replica
ms.assetid: ''
author: cawrites
ms.author: chadam
ms.openlocfilehash: 75aa499fff65f9b7bf6ab10b6cdc501c172fbc03
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97644354"
---
# <a name="use-automatic-seeding-to-initialize-a-secondary-replica-for-an-always-on-availability-group"></a>使用自動植入將 Always On 可用性群組的次要複本初始化
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]

在 SQL Server 2012 和 2014 中，初始化 SQL Server AlwaysOn 可用性群組次要複本的唯一方式是使用備份、複製和還原。 SQL Server 2016 引進的新功能是初始化次要複本：「自動植入」  。 自動植入使用記錄資料流傳輸，將使用 VDI 的備份串流到使用已設定端點的可用性群組的每個資料庫次要複本。 初始建立可用性群組期間，或將資料庫新增至某個可用性群組時，可以使用這項新功能。 所有支援 AlwaysOn 可用性群組的 SQL Server 版本都有自動植入功能，可以搭配傳統的可用性群組和[分散式可用性群組](distributed-availability-groups.md)使用。

## <a name="security"></a>安全性

安全性權限隨複本初始化的類型而不同：

* 若為傳統的可用性群組，權限必須授予次要複本上的可用性群組，因為它加入的是可用性群組。 在 Transact-SQL 中，使用命令 `ALTER AVAILABILITY GROUP [<AGName>] GRANT CREATE ANY DATABASE`。
* 若為分散式可用性群組，它是正在建立複本資料庫的位置，且這些資料庫位在第二個可用性群組的主要複本上，則不需要任何額外的權限，因為它已經是主要複本。
* 若為分散式可用性群組的第二個可用性群組上的次要複本，您必須使用命令 `ALTER AVAILABILITY GROUP [<2ndAGName>] GRANT CREATE ANY DATABASE`。 這個次要複本是從第二個可用性群組的主要複本植入。

## <a name="performance-and-transaction-log-impact-on-the-primary-replica"></a>對主要複本的效能和交易記錄影響

自動植入對初始化次要複本可能實用，也可能不實用，視資料庫大小、網路速度和主要與次要複本之間的距離而定。 例如，假設：

* 資料庫的大小是 5 TB
* 網路速度為 1 Gb/秒
* 兩個網站之間的距離是 1000 英哩

如有完整的頻寬可用，1 Gb/秒的網路可以提供 125 MB/秒的持續輸送量。在本例中，自動植入只需要 11 個小時多。 在實務上，自動植入程序較慢，因為網路訊號會隨著距離而降低，而連結會與網路上的其他資源共用。 在植入期間，主要複本資料庫的交易記錄會繼續成長，在資料庫的自動植入完成之前無法截斷。  接著，使用交易記錄備份截斷交易記錄。

自動植入是單一執行緒的程序，最多可以處理五個資料庫。 單一執行緒會影響效能，特別是當可用性群組有多個資料庫時。

壓縮可用於自動植入，但預設停用。 開啟壓縮會減少網路頻寬，並可能加速程序，但代價是額外的處理器額外負荷。 若要在自動植入期間使用壓縮，請啟用追蹤旗標 9567，請參閱[微調可用性群組的壓縮](tune-compression-for-availability-group.md)。

## <a name="disk-layout"></a><a name = "disklayout"></a> 磁碟配置

在 SQL Server 2016 和舊版中，自動植入所建立資料庫的資料夾必須已經存在，而且和主要複本位在同一路徑下。 

在 SQL Server 2017 中，Microsoft 建議所有加入可用性群組的複本都使用相同的資料和記錄檔路徑，但如有必要，您可以使用不同的路徑。 例如，在跨平台的可用性群組中，一個 SQL Server 執行個體在 Windows，而另一個 SQL Server 執行個體在 Linux。 不同的平台有不同的預設路徑。 SQL Server 2017 支援 SQL Server 的執行個體可用性群組複本可使用不同的預設路徑。

下表顯示的範例，是支援的資料磁碟配置可以支援自動植入：

|主要執行個體</br>預設資料路徑|次要執行個體</br>預設資料路徑|主要執行個體</br>來源檔案位置|次要執行個體</br> 目標檔案位置
|:------|:------|:------|:------
|c:\\data\\ |/var/opt/mssql/data/ |c:\\data\\ |/var/opt/mssql/data/|
|c:\\data\\ |/var/opt/mssql/data/ |c:\\data\\group1\\ |/var/opt/mssql/data/group1/|
|c:\\data\\ |d:\\data\\ |c:\\data\\ |d:\\data\\
|c:\\data\\ |d:\\data\\ |c:\\data\\group1\\ |d:\\data\\group1\

主要和次要複本資料庫位置不是執行個體預設路徑的案例，不受此變更影響。 次要複本檔案路徑符合主要複本檔案路徑的需求維持不變。

|主要執行個體</br>預設資料路徑|次要執行個體</br>預設資料路徑|主要執行個體</br>檔案位置|次要執行個體</br> 檔案位置
|:------|:------|:------|:------
|c:\\data\\ |c:\\data\\ |d:\\group1\\ |d:\\group1\\
|c:\\data\\ |c:\\data\\ |d:\\data\\ |d:\\data\\
|c:\\data\\ |c:\\data\\ |d:\\data\\group1\\ |d:\\data\\group1\\

如果您混用了主要和次要複本的預設和非預設路徑，SQL Server 2017 的行為表現會和舊版不同。 下表說明 SQL Server 2017 的行為。

|主要執行個體</br>預設資料路徑 |次要執行個體</br>預設資料路徑 |主要執行個體</br>檔案位置 |SQL Server 2016 </br>次要執行個體</br>檔案位置 |SQL Server 2017 </br>次要執行個體</br>檔案位置
|:------|:------|:------|:------|:------
|c:\\data\\ |d:\\data\\ |c:\\data\\ |c:\\data\\ |d:\\data\\ 
|c:\\data\\ |d:\\data\\ |c:\\data\\group1\\ |c:\\data\\group1\\ |d:\\data\\group1\\

若要還原到 SQL Server 2016 和舊版的行為，請啟用追蹤旗標 9571。 如需如何啟用追蹤旗標的資訊，請參閱 [DBCC TRACEON (Transact-SQL)](../../../t-sql/database-console-commands/dbcc-traceon-transact-sql.md)。


## <a name="create-an-availability-group-with-automatic-seeding"></a>使用自動植入建立可用性群組

您使用 Transact-SQL 或 SQL Server Management Studio (SSMS，17 版或更新版本)，以自動植入建立可用性群組。 若要使用 SSMS 的可用性群組精靈，請遵循[這些指示](use-the-availability-group-wizard-sql-server-management-studio.md)：到步驟 9 時，請注意自動植入是第一個且為預設選項。

![選取初始資料同步處理][1]

下列範例使用 Transact-SQL 建立有自動植入功能的可用性群組。 另請參閱[建立可用性群組 (Transact-SQL)](create-an-availability-group-transact-sql.md) 主題。 將 `SEEDING_MODE` 選項設定為 `AUTOMATIC`，啟用次要複本的植入功能。 預設行為是 `MANUAL`，這是 SQL Server 2016 前置行為，需要在主要複本上備份資料庫、將備份檔複製至次要複本，以及使用 `WITH NORECOVERY` 還原備份。

```sql
CREATE AVAILABILITY GROUP [<AGName>]
  FOR DATABASE db1
  REPLICA ON N'Primary_Replica'
WITH (
  ENDPOINT_URL = N'TCP://Primary_Replica.Contoso.com:5022', 
  FAILOVER_MODE = AUTOMATIC, 
  AVAILABILITY_MODE = SYNCHRONOUS_COMMIT, 
),
  N'Secondary_Replica' WITH (
    ENDPOINT_URL = N'TCP://Secondary_Replica.Contoso.com:5022', 
    FAILOVER_MODE = AUTOMATIC, 
    SEEDING_MODE = AUTOMATIC);
 GO
```

於 `CREATE AVAILABILITY GROUP` 陳述式執行期間在主要複本上設定 `SEEDING_MODE` 沒有效用，因為主要複本已經包含資料庫的主要讀取/寫入複本。 只有將另一個複本變成主要，而且新增資料庫時，才會套用 `SEEDING_MODE`。 您可於稍後變更植入模式，請參閱[變更複本的植入模式](#change-the-seeding-mode-of-a-replica)。

在變成次要複本的執行個體上，一旦加入該執行個體，下列訊息就會新增至 SQL Server 記錄檔：

>可用性群組 'AGName' 的本機可用性複本並未被授與建立資料庫的權限，但有 `AUTOMATIC` 的 `SEEDING_MODE`。 使用 `ALTER AVAILABILITY GROUP ... GRANT CREATE ANY DATABASE` 命令可建立主要可用性複本植入的資料庫。

### <a name="grant-create-database-permission-on-secondary-replica-to-availability-group"></a><a name = "grantCreate"></a> 授與可用性群組在次要複本上建立資料庫的權限

加入之後，授與可用性群組在 SQL Server 次要複本執行個體上建立資料庫的權限。 為使自動植入運作，可用性群組需要有建立資料庫的權限。 

>[!TIP]
>當可用性群組在次要複本上建立資料庫時，它會將 "sa" (更明確來說具有 sid 0x01 的帳戶) 設定為資料庫擁有者。 
>
>若要在次要複本自動建立資料庫之後變更資料庫擁有者，請使用 `ALTER AUTHORIZATION`。 請參閱 [ALTER AUTHORIZATION (Transact-SQL)](../../../t-sql/statements/alter-authorization-transact-sql.md)。
 
下列範例會將此權限授與名為 AGName 的可用性群組。

```sql
ALTER AVAILABILITY GROUP [<AGName>] 
    GRANT CREATE ANY DATABASE
 GO
```

如有需要，請在次要複本上設定資料庫的擁有者。 

### <a name="verify-automatic-seeding"></a>確認自動植入

如果成功，會在次要複本上自動建立資料庫，狀態為二者之一：

* SYNCHRONIZED，如果次要複本設定為同步，且資料已同步處理。
* SYNCHRONIZING，如果次要複本設定了非同步資料移動，或設定同步但尚未與主要複本同步處理時。

<a name="sql-server-log"></a> 除了後文所述的[動態管理檢視](#dynamic-management-views)，SQL Server 記錄檔中還可以看到開始和完成自動植入：

![SQL 伺服器記錄][2]

## <a name="combine-backup-and-restore-with-automatic-seeding"></a>使用自動植入合併備份並還原

有可能使用自動植入合併傳統備份、複製和還原。 在本例中，首先在次要複本上還原資料庫，包括所有的可用交易記錄。 接下來，在建立可用性群組以「趕上」次要複本的資料庫時，啟用自動植入，如同還原結尾記錄備份 (請參閱[結尾記錄備份 (SQL Server)](../../../relational-databases/backup-restore/tail-log-backups-sql-server.md))。

## <a name="add-a-database-to-an-availability-group-with-automatic-seeding"></a>使用自動植入將資料庫新增至可用性群組

您可以使用 Transact-SQL 或 SQL Server Management Studio (SSMS，17 版或更新版本)，以自動植入將資料庫新增至可用性群組。
如果次要複本在新增至可用性群組時使用了自動植入，就不需要任何額外作業。 如果次要複本使用了備份、複製和還原，請先變更植入模式 (參閱下節)，接著在新增資料庫時使用 `GRANT` 陳述式，請參閱[可用性群組 - 新增資料庫](availability-group-add-a-database.md)。

## <a name="change-the-seeding-mode-of-a-replica"></a>變更複本的植入模式

建立可用性群組之後，可以改變複本的植入模式，所以可以啟用或停用自動植入。 如果資料庫是以備份、複製和還原所建立，建立後啟用自動植入，可使用自動植入將資料庫新增至可用性群組。 例如：

```sql
ALTER AVAILABILITY GROUP [AGName]
  MODIFY REPLICA ON 'Replica_Name'
  WITH (SEEDING_MODE = AUTOMATIC)
```

若要停用自動植入，請使用 MANUAL 值。

## <a name="prevent-automatic-seeding-after-an-availability-group-is-created"></a>避免在建立可用性群組之後自動植入

如果完全不想要停用次要複本的自動植入，但又想要暫時防止次要複本能夠自動建立資料庫，請拒絕可用性群組 CREATE 權限。 當新資料庫新增至可用性群組，但不應該允許可用性群組在次要複本上建立資料庫時，就是這種情況。

```sql
ALTER AVAILABILITY GROUP [AGName] 
    DENY CREATE ANY DATABASE
GO
```

## <a name="monitor-automatic-seeding"></a>監視自動植入

有四種方式可以監視和為自動植入進行疑難排解：

* [SQL Server 記錄檔](#sql-server-log)如前文所述
* [動態管理檢視](#dynamic-management-views)
* [備份記錄資料表](#backup-history-tables)
* [擴充事件](#extended-events)

### <a name="dynamic-management-views"></a>動態管理檢視

有兩個動態管理檢視 (DMV) 可監視植入：`sys.dm_hadr_automatic_seeding` 和 `sys.dm_hadr_physical_seeding_stats`。

* `sys.dm_hadr_automatic_seeding` 包含自動植入的一般狀態，並保留每次執行的記錄 (無論成功與否)。 資料行 `current_state` 會有 COMPLETED 或 FAILED 值。 如果是 FAILED 值，請使用 `failure_state_desc` 中的值協助診斷問題。 您可能需要結合 [SQL Server 記錄檔](#sql-server-log)中的內容，才知道發生什麼問題。 主要複本和所有次要複本會填入此 DMV。

* `sys.dm_hadr_physical_seeding_stats` 會顯示自動植入作業執行時的狀態。 如同 `sys.dm_hadr_automatic_seeding`，這會傳回主要複本和次要複本的值，但不儲存此記錄。 這些值僅供目前執行之用，不會保留。 您感興趣的資料行包括 `start_time_utc`、`end_time_utc`、`estimate_time_complete_utc`、`total_disk_io_wait_time_ms`、`total_network_wait_time_ms`，以及如果植入作業失敗的 failure_message。

### <a name="backup-history-tables"></a>備份記錄資料表

自動植入還可將項目放入 `msdb` 資料表，此表儲存備份和還原的記錄。 在接收自動植入的次要複本上，`backupmediafamily` 資料表的 physical_device_name 資料行有其值的 GUID，而 `backupset` 中的對應項目則有 server_name and machine_name 的主要複本名稱。

### <a name="extended-events"></a>擴充事件

自動植入新增新的擴充事件，可追蹤初始化期間的狀態變更、失敗和效能統計資料。
例如，下列指令碼會建立擴充事件工作階段，以擷取與自動植入相關的事件。

```sql
CREATE EVENT SESSION [AlwaysOn_autoseed] ON SERVER 
    ADD EVENT sqlserver.hadr_automatic_seeding_state_transition,
    ADD EVENT sqlserver.hadr_automatic_seeding_timeout,
    ADD EVENT sqlserver.hadr_db_manager_seeding_request_msg,
    ADD EVENT sqlserver.hadr_physical_seeding_backup_state_change,
    ADD EVENT sqlserver.hadr_physical_seeding_failure,
    ADD EVENT sqlserver.hadr_physical_seeding_forwarder_state_change,
    ADD EVENT sqlserver.hadr_physical_seeding_forwarder_target_state_change,
    ADD EVENT sqlserver.hadr_physical_seeding_progress,
    ADD EVENT sqlserver.hadr_physical_seeding_restore_state_change,
    ADD EVENT sqlserver.hadr_physical_seeding_submit_callback
    ADD TARGET package0.event_file(
        SET filename=N'autoseed.xel',
        max_file_size=(5),
        max_rollover_files=(4)
        )
    WITH (
        MAX_MEMORY=4096 KB,
        EVENT_RETENTION_MODE=ALLOW_SINGLE_EVENT_LOSS,
        MAX_DISPATCH_LATENCY=30 SECONDS,
        MAX_EVENT_SIZE=0 KB,
        MEMORY_PARTITION_MODE=NONE,
        TRACK_CAUSALITY=OFF,
        STARTUP_STATE=ON
        )
GO

ALTER EVENT SESSION AlwaysOn_autoseed ON SERVER STATE=START
GO
```

下表列出與自動植入相關的擴充事件。

|名稱|描述|
|----|-----------|
|hadr_db_manager_seeding_request_msg|正在植入要求訊息。|
|hadr_physical_seeding_backup_state_change|實體植入備份端的狀態變更。|
|hadr_physical_seeding_restore_state_change|實體植入還原端的狀態變更。|
|hadr_physical_seeding_forwarder_state_change|實體植入轉寄站端的狀態變更。|
|hadr_physical_seeding_forwarder_target_state_change|實體植入轉寄站目標端的狀態變更。|
|hadr_physical_seeding_submit_callback|實體植入的提交回撥事件。|
|hadr_physical_seeding_failure|實體植入失敗事件。|
|hadr_physical_seeding_progress|實體植入進度事件。|
|hadr_physical_seeding_schedule_long_task_failure|實體植入排程作業時間太長失敗事件。|
|hadr_automatic_seeding_start|提交自動植入作業時發生。|
|hadr_automatic_seeding_state_transition|自動植入作業變更狀態時發生。|
|hadr_automatic_seeding_success|自動植入作業成功時發生。|
|hadr_automatic_seeding_failure|自動植入作業失敗時發生。|
|hadr_automatic_seeding_timeout|自動植入作業逾時時發生。|

## <a name="see-also"></a>另請參閱

[ALTER AVAILABILITY GROUP (Transact-SQL)](../../../t-sql/statements/alter-availability-group-transact-sql.md)

[CREATE AVAILABILITY GROUP (Transact-SQL)](../../../t-sql/statements/create-availability-group-transact-sql.md)

[AlwaysOn 可用性群組疑難排解和監視指南](/previous-versions/sql/sql-server-guides/dn135328(v=sql.110))

<!--Image references-->
[1]: ./media/auto-seed-new-availability-group.png
[2]: ./media/auto-seed-sql-server-log.png