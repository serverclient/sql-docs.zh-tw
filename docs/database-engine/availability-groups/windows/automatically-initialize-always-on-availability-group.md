---
title: 使用自動植入將可用性群組初始化
description: 使用自動植入來自動為 Always On 可用性群組中的每個資料庫建立次要複本，而不需要手動備份及還原。
ms.custom: seo-lt-2019
ms.date: 03/26/2018
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: how-to
ms.assetid: 67c6a601-677a-402b-b3d1-8c65494e9e96
author: cawrites
ms.author: chadam
ms.openlocfilehash: a4b992ab522145959cb4e3fe4e4b978f15b11e9c
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97644151"
---
# <a name="use-automatic-seeding-to-initialize-an-always-on-availability-group"></a>使用自動植入將 Always On 可用性群組初始化
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]

SQL Server 2016 引進了自動植入可用性群組。 當您使用自動植入建立可用性群組時，SQL Server 會自動為群組中的每個資料庫建立次要複本。 您不必再手動備份與還原次要複本。 若要啟用自動植入，請利用 T-SQL 或使用最新版的 SQL Server Management Studio 建立可用性群組。

如需背景資訊，請參閱[自動植入次要複本](automatic-seeding-secondary-replicas.md)。
 
## <a name="prerequisites"></a>Prerequisites

在 SQL Server 2016 中，自動植入要求參與可用性群組之每個 SQL Server 執行個體上的資料和記錄檔案路徑都必須相同。 在 SQL Server 2017 中，您可以使用不同的路徑，不過當所有複本都裝載於相同平台時 (例如 Windows 或 Linux)，Microsoft 建議使用相同的路徑。 跨平台可用性群組為複本使用不同的路徑。 如需詳細資料，請參閱[磁碟配置](automatic-seeding-secondary-replicas.md#disklayout)。

可用性群組植入會透過資料庫鏡像端點進行通訊。 請開啟每部伺服器上之鏡像端點連接埠的輸入防火牆規則。

可用性群組中的資料庫必須處於完整復原模式。 此資料庫必須有目前的完整備份和交易記錄備份。 這些備份檔案雖然不會用於自動植入，但必須具備，才能將資料庫加入可用性群組。 
 
## <a name="create-availability-group-with-automatic-seeding"></a>使用自動植入建立可用性群組

若要使用自動植入建立可用性群組，請設定 `SEEDING_MODE=AUTOMATIC`。 

下列範例會在兩個 Windows Server 容錯移轉叢集節點上建立可用性群組。 執行指令碼之前，請更新您環境的值。

1. 建立端點。 每一部伺服器都需要端點。 下列指令碼會建立針對接聽程式使用 TCP 通訊埠 5022 的端點。 設定 `<endpoint_name>` 和 `LISTENER_PORT` 使符合您的環境，然後在兩部伺服器上執行下列指令碼：

    ```sql
    CREATE ENDPOINT [<endpoint_name>] 
        STATE=STARTED
        AS TCP (LISTENER_PORT = 5022, LISTENER_IP = ALL)
        FOR DATA_MIRRORING (
            ROLE = ALL, 
            AUTHENTICATION = WINDOWS NEGOTIATE, 
            ENCRYPTION = REQUIRED ALGORITHM AES
            )
    GO
    ```

1. 建立可用性群組。 下列指令碼會建立可用性群組。 更新角括弧 `<>` 中群組名稱、伺服器名稱和網域名稱的值，並在 SQL Server 的主要執行個體上執行。  

    ```sql
    CREATE AVAILABILITY GROUP [<availability_group_name>]
        FOR DATABASE db1
        REPLICA ON'<*primary_server*>'
        WITH (ENDPOINT_URL = N'TCP://<primary_server>.<fully_qualified_domain_name>:5022', 
            FAILOVER_MODE = AUTOMATIC, 
            AVAILABILITY_MODE = SYNCHRONOUS_COMMIT, 
            BACKUP_PRIORITY = 50, 
            SECONDARY_ROLE(ALLOW_CONNECTIONS = NO), 
            SEEDING_MODE = AUTOMATIC),
        N'<secondary_server>' WITH (ENDPOINT_URL = N'TCP://<secondary_server>.<fully_qualified_domain_name>:5022', 
            FAILOVER_MODE = AUTOMATIC, 
            AVAILABILITY_MODE = SYNCHRONOUS_COMMIT, 
            BACKUP_PRIORITY = 50, 
            SECONDARY_ROLE(ALLOW_CONNECTIONS = NO), 
            SEEDING_MODE = AUTOMATIC);
    GO
    ``` 

1. 將次要伺服器執行個體聯結至可用性群組，並將建立資料庫的權限授與可用性群組。 更新下列指令碼，針對您的環境取代角括號 `<>` 中的值，然後在 SQL Server 的次要複本執行個體上執行： 
 
    ```sql
    ALTER AVAILABILITY GROUP [<availability_group_name>] JOIN
    GO  
    ALTER AVAILABILITY GROUP [<availability_group_name>] GRANT CREATE ANY DATABASE
    GO
    ```

SQL Server 會在次要伺服器上自動建立資料庫複本。 如果資料庫很大，則可能需要一些時間才能完成資料庫的同步處理。 如果資料庫位於已設定為自動植入的可用性群組中，您可以查詢 `sys.dm_hadr_automatic_seeding` 系統檢視表來監視植入進度。 下列查詢會為已設定為自動植入之可用性群組中的每個資料庫，各傳回一個資料列。

```sql 
SELECT start_time,
    ag.name,
    db.database_name,
    current_state,
    performed_seeding,
    failure_state,
    failure_state_desc
FROM sys.dm_hadr_automatic_seeding autos 
    JOIN sys.availability_databases_cluster db 
        ON autos.ag_db_id = db.group_database_id
    JOIN sys.availability_groups ag 
        ON autos.ag_id = ag.group_id
```

## <a name="prevent-automatic-seeding-after-an-availability-group"></a>避免在可用性群組之後自動植入

若要暫時防止主要複本將多個資料庫植入次要複本，您可以拒絕授權可用性群組建立資料庫。 在裝載次要複本的執行個體上執行下列查詢，以拒絕授權可用性群組建立複本資料庫。

```sql
ALTER AVAILABILITY GROUP [<availability_group_name>] 
    DENY CREATE ANY DATABASE
GO
```


## <a name="enable-automatic-seeding-on-an-existing-availability-group"></a>在現有的可用性群組上啟用自動植入

您可以在現有的資料庫上設定自動植入。 下列命令會變更可用性群組以使用自動植入。 在主要複本上執行下列命令。

```sql
ALTER AVAILABILITY GROUP [<availability_group_name>] 
    MODIFY REPLICA ON '<secondary_node>' 
    WITH (SEEDING_MODE = AUTOMATIC)
GO
```

如有必要，上述命令會強制資料庫重新啟動植入。 例如，如果由於次要複本上的磁碟空間不足而導致植入失敗，請在增加可用空間之後，執行 `ALTER AVAILABILITY GROUP ... WITH (SEEDING_MODE=AUTOMATIC)` 以重新啟動植入。

## <a name="stop-automatic-seeding"></a>停止自動植入

若要停止對可用性群組進行自動植入，請在主要複本上執行下列指令碼：

```sql
ALTER AVAILABILITY GROUP [<availability_group_name>] 
    MODIFY REPLICA ON '<secondary_node>'   
    WITH (SEEDING_MODE = MANUAL)
GO
```

上述指令碼會取消目前正在植入的任何複本，並防止 SQL Server 自動初始化此可用性群組中的任何複本。 它不會停止已初始化之任何複本的同步處理。 


## <a name="monitor-automatic-seeding-availability-group"></a>監視自動植入可用性群組

### <a name="use-system-dynamic-management-views-to-monitor-seeding"></a>使用系統動態管理檢視來監視植入

下列系統檢視表顯示 SQL Server 自動植入的狀態。

**sys.dm_hadr_automatic_seeding** 

在主要複本上，查詢 `sys.dm_hadr_automatic_seeding` 以檢查自動植入處理序的狀態。 此檢視會為每個植入處理序，各傳回一個資料列。 例如：

```sql
SELECT start_time, 
    completion_time
    is_source,
    current_state,
    failure_state,
    failure_state_desc
FROM sys.dm_hadr_automatic_seeding
```
 
**sys.dm_hadr_physical_seeding_stats** 

在主要複本上，查詢 `sys.dm_hadr_physical_seeding_stats` DMV，以查看目前執行中之每個植入處理序的實體統計資料。 下列查詢會傳回正在執行植入時的資料列：

```sql
SELECT * FROM sys.dm_hadr_physical_seeding_stats;
```

兩個資料行 *total_disk_io_wait_time_ms* 和 *total_network_wait_time_ms* 皆可用於判斷自動植入流程中的效能瓶頸。 這兩個資料行也會出現在 *hadr_physical_seeding_progress* 擴充事件中。

**total_disk_io_wait_time_ms** 代表備份/還原執行緒在等待磁碟期間所花費的時間。 此值從開始植入作業即開始累積。 如果磁碟未就緒可讀取或寫入備份串流，則備份/還原執行緒會轉為睡眠狀態，且會每隔一秒喚醒一次來檢查磁碟是否已備妥。
        
**total_network_wait_time_ms** 對於主要複本和次要複本來說意義不同。 對於主要複本來說，此計數器代表網路流量控制時間。 對於次要複本來說，則代表還原執行緒等待訊息可以寫入磁碟的時間。

### <a name="diagnose-database-initialization-using-automatic-seeding-in-the-error-log"></a>使用錯誤記錄檔中的自動植入來診斷資料庫初始化

當您將資料庫加入已設定為自動植入的可用性群組時，SQL Server 會透過可用性群組端點執行 VDI 備份。 如需何時完成備份及同步處理次要複本的相關資訊，請參閱 SQL Server 錯誤記錄檔。

### <a name="diagnose-database-level-health-with-extended-events"></a>使用擴充事件來診斷資料庫層級健全狀況

自動植入具有新的擴充事件，可追蹤初始化期間的狀態變更、失敗和效能統計資料。 

例如，下列指令碼會建立擴充事件工作階段，以擷取與自動植入相關的事件： 

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


下表列出與自動植入相關的擴充事件： 

| 名稱 | 描述|
|------------ |---------------| 
|hadr_db_manager_seeding_request_msg |  正在植入要求訊息。
|hadr_physical_seeding_backup_state_change |    實體植入備份端的狀態變更。
|hadr_physical_seeding_restore_state_change |實體植入還原端的狀態變更。
|hadr_physical_seeding_forwarder_state_change | 實體植入轉寄站端的狀態變更。
|hadr_physical_seeding_forwarder_target_state_change |  實體植入轉寄站目標端的狀態變更。
|hadr_physical_seeding_submit_callback  |實體植入的提交回撥事件。
|hadr_physical_seeding_failure  |實體植入失敗事件。
|hadr_physical_seeding_progress |   實體植入進度事件。
|hadr_physical_seeding_schedule_long_task_failure   |實體植入排程作業時間太長失敗事件。
|hadr_automatic_seeding_start   |提交自動植入作業時發生。
|hadr_automatic_seeding_state_transition    |自動植入作業變更狀態時發生。
|hadr_automatic_seeding_success |自動植入作業成功時發生。
|hadr_automatic_seeding_failure |自動植入作業失敗時發生。
|hadr_automatic_seeding_timeout |自動植入作業逾時時發生。

### <a name="other-troubleshooting-considerations"></a>其他疑難排解考量

**自動植入時進行監視**

針對目前執行中的自動植入處理序查詢 `sys.dm_hadr_physical_seeding_stats` 。 此檢視會為每個資料庫，各傳回一個資料列。 例如：

```sql
SELECT local_database_name, 
    role_desc, 
    internal_state_desc, 
    transfer_rate_bytes_per_second, 
    transferred_size_bytes, 
    database_size_bytes, 
    start_time_utc, 
    end_time_utc, estimate_time_complete_utc, 
    total_disk_io_wait_time_ms, 
    total_network_wait_time_ms, 
    is_compression_enabled 
FROM sys.dm_hadr_physical_seeding_stats
```

**針對已設定為自動植入之可用性群組中未顯示資料庫的原因進行疑難排解**


當啟用自動植入的可用性群組中未顯示資料庫時，自動植入可能會失敗。 這可防止將資料庫加入主要和次要複本上的可用性群組。 在主要和次要複本上查詢 `sys.dm_hadr_automatic_seeding` 。 例如，執行下列查詢以識別自動植入的失敗狀態。

```sql
SELECT start_time, 
    completion_time, 
    is_source, 
    current_state, 
    failure_state, 
    failure_state_desc, 
    error_code 
FROM sys.dm_hadr_automatic_seeding
```

## <a name="automatic-seeding-and-performance-considerations"></a>自動植入和效能考量

SQL Server 使用固定的執行緒數目來進行自動植入。 在主要執行個體上，SQL Server 會針對每個 LUN 使用一個執行緒來讀取變更。 在次要執行個體上，SQL Server 會針對每個 LUN 使用一個執行緒來初始化資料庫。

將主要複本上的追蹤旗標設定為 9567，以允許壓縮自動植入期間的資料流。 這可大幅縮短自動植入的傳輸時間，但也會增加 CPU 使用量。 如需詳細資訊，請參閱 [微調可用性群組的壓縮](../../../database-engine/availability-groups/windows/tune-compression-for-availability-group.md)。 


## <a name="when-not-to-use-automatic-seeding"></a>何時不要使用自動植入

在某些情況下，自動植入對初始化次要複本可能不是最理想的做法。 在自動植入期間，SQL Server 會透過網路執行備份以進行初始化。 如果資料庫很大，或次要複本位於遠端，此程序可能會很慢。 在備份過程中，您無法截斷這些資料庫的交易記錄，因此忙碌資料庫上的長時間初始化程序，可能會導致交易記錄大幅成長。
使用自動植入將資料庫加入可用性群組之前，請評估資料庫大小、負載，以及複本之間的網站距離。

## <a name="resources"></a>資源

[CREATE AVAILABILITY GROUP (Transact-SQL)](../../../t-sql/statements/create-availability-group-transact-sql.md)

[AlwaysOn 可用性群組疑難排解和監視指南](/previous-versions/sql/sql-server-guides/dn135328(v=sql.110))