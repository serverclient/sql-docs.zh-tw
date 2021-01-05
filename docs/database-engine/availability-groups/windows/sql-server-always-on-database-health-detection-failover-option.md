---
title: 資料庫層級健康情況偵測
description: 了解可供 SQL Server Always On 可用性群組使用的資料庫層級健康情況偵測功能。
ms.custom: seo-lt-2019
ms.date: 01/19/2019
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: conceptual
helpviewer_keywords:
- AlwaysOn
- DB_FAILOVER
- Always On
- High Availability
- SQL Server
ms.assetid: d74afd28-25c3-48a1-bc3f-e353bee615c2
author: cawrites
ms.author: chadam
ms.openlocfilehash: 1d0abb2a078c9dd2e7f540ee05b70b60dd756a34
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97639045"
---
# <a name="availability-group-database-level-health-detection-failover-option"></a>可用性群組資料庫層級健全狀況偵測容錯移轉選項
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]
從 SQL Server 2016 開始，設定 AlwaysOn 可用性群組時，可以使用資料庫層級健全狀況偵測 (DB_FAILOVER) 選項。 資料庫層級健全狀況偵測注意到資料庫不再處於線上狀態、發生錯誤，以及觸發可用性群組的自動容錯移轉。

整體啟用可用性群組的資料庫層級健全狀況偵測，因此資料庫層級健全狀況偵測會監視可用性群組中的每個資料庫。 無法針對可用性群組中的特定資料庫選擇性地予以啟用。

## <a name="benefits-of-database-level-health-detection-option"></a>資料庫層級健全狀況偵測選項的優點
---
可用性群組資料庫層級健全狀況偵測選項經廣泛建議為協助保證資料庫高可用性的不錯選擇。 您應該考慮針對所有可用性群組開啟它。 如果您的應用程式相依於多個高度可用的資料庫，請將它們分組為已開啟資料庫健全狀況選項的可用性群組。

例如，啟用資料庫層級健全狀況偵測選項時，如果 SQL Server 無法寫入其中一個資料庫的交易記錄檔，則該資料庫的狀態會變更以指出失敗，以及可用性群組即將容錯移轉，而且在資料庫再度上線之後，您的應用程式可以重新連接，並在最少中斷的情況下繼續處理。

### <a name="enabling-database-level-health-detection"></a>啟用資料庫層級健全狀況偵測

雖然通常會建議使用它，但是資料庫健全狀況選項 **預設為關閉**，以保留與舊版預設設定的回溯相容性。

有幾種簡單的方法可以啟用資料庫層級健全狀況偵測設定：

1. 在 SQL Server Management Studio 中，連接到 SQL Server 資料庫引擎。 使用 [物件總管] 視窗，並以滑鼠右鍵按一下 [AlwaysOn 高可用性] 節點，然後執行 [新增可用性群組精靈]  。 核取 [指定名稱] 頁面上的 [資料庫層級健全狀況偵測]  核取方塊。 然後完成精靈中頁面的其餘部分。

   ![AlwaysOn 啟用資料庫健全狀況核取方塊](../../../database-engine/availability-groups/windows/media/always-on-enable-database-health-checkbox.png)

2. 在 SQL Server Management Studio 中檢視現有可用性群組的 [屬性]  。 連接到 SQL Server。 使用 [物件總管] 視窗，展開 [AlwaysOn 高可用性] 節點。 展開 [可用性群組]。 以滑鼠右鍵按一下可用性群組，然後選擇 [屬性]。 核取 [資料庫層級健全狀況偵測]  選項，然後按一下 [確定] 或 [Script the change]\(指令碼變更)。

   ![AlwaysOn AG 屬性資料庫層級健全狀況偵測](../../../database-engine/availability-groups/windows/media/always-on-ag-properties-database-level-health-detection.png)


3. **CREATE AVAILABILITY GROUP** 的 Transact-SQL 語法。 DB_FAILOVER 參數接受值 ON 或 OFF。

   ```sql
   CREATE AVAILABILITY GROUP [Contoso-ag]
   WITH (DB_FAILOVER=ON)
   FOR DATABASE [AutoHa-Sample]
   REPLICA ON
       N'SQLSERVER-0' WITH (ENDPOINT_URL = N'TCP://SQLSERVER-0.DOMAIN.COM:5022',
         FAILOVER_MODE = AUTOMATIC, AVAILABILITY_MODE = SYNCHRONOUS_COMMIT),
       N'SQLSERVER-1' WITH (ENDPOINT_URL = N'TCP://SQLSERVER-1.DOMAIN.COM:5022',
        FAILOVER_MODE = AUTOMATIC, AVAILABILITY_MODE = SYNCHRONOUS_COMMIT);
    ```

4. **ALTER AVAILABILITY GROUP** 的 Transact-SQL 語法。 DB_FAILOVER 參數接受值 ON 或 OFF。

   ```sql
   ALTER AVAILABILITY GROUP [Contoso-ag] SET (DB_FAILOVER = ON);

   ALTER AVAILABILITY GROUP [Contoso-ag] SET (DB_FAILOVER = OFF);
   ```

### <a name="caveats"></a>警示

請務必注意，[資料庫層級健全狀況偵測] 選項目前不會造成 SQL Server 監視磁碟執行時間，而且 SQL Server 不會直接監視資料庫檔案可用性。 如果磁碟機失敗或無法使用，則不一定會觸發可用性群組的自動容錯移轉。

例如，資料庫因沒有使用中交易以及未發生實體寫入而閒置時，如果部分資料庫檔案變成無法存取，則 SQL Server 可能不會對檔案執行任何讀取或寫入 IO，並且可能不會立即變更該資料庫的狀態，因此不會觸發容錯移轉。 稍後，如果發生資料庫檢查點，或基於完成查詢而發生實體讀取或寫入，則 SQL Server 接著可能會注意到檔案問題，並藉由變更資料庫狀態進行反映，其後已開啟資料庫層級健全狀況偵測的可用性群組會因資料庫健全狀況變更而容錯移轉。

另一個範例是，SQL Server 資料庫引擎需要讀取資料頁面以完成查詢時，如果在緩衝集區記憶體中快取資料頁面，則不可能需要具有實體存取權的讀取磁碟，即可完成查詢要求。 因此，因為資料庫狀態不是立即的，所以即使啟用資料庫健全狀況選項，遺失或無法使用資料檔案還是可能不會立即觸發自動容錯移轉。


## <a name="database-failover-is-separate-from-flexible-failover-policy"></a>資料庫容錯移轉與彈性容錯移轉原則不同
資料庫層級健全狀況偵測會實作彈性容錯移轉原則，以設定容錯移轉原則的 SQL Server 處理序健全狀況臨界值。 資料庫層級健全狀況偵測是使用 DB_FAILOVER 參數所設定，而可用性群組選項 FAILURE_CONDITION_LEVEL 與設定 SQL Server 處理序健全狀況偵測不同。 兩個選項無相關。

## <a name="managing-and-monitoring-database-level-health-detection"></a>管理和監視資料庫層級健全狀況偵測

### <a name="dynamic-management-views"></a>動態管理檢視

系統 DMV sys.availability_groups 顯示資料行 db_failover，以指出資料庫層級健全狀況偵測選項是關閉 (0) 還是開啟 (1)。

```sql
select name, db_failover from sys.availability_groups
```


範例 dmv 輸出：

|NAME  |  db_failover|
|---------|---------|
| Contoso-ag | 1  |

### <a name="errorlog"></a>ErrorLog
可用性群組已因資料庫層級健全狀況偵測檢查而容錯移轉時，SQL Server 錯誤記錄檔 (或來自 sp_readerrorlog 的文字) 會顯示錯誤訊息 41653。

例如，此錯誤記錄檔摘要會顯示交易記錄寫入因磁碟問題而失敗，接著會關閉名為 AutoHa-Sample 的資料庫，以觸發資料庫層級健全狀況偵測來容錯移轉可用性群組。

>2016-04-25 12:20:21.08 spid1s      錯誤：17053，嚴重性：16，狀態：1.
>
>2016-04-25 12:20:21.08 spid1s      SQLServerLogMgr::LogWriter:遇到作業系統錯誤 21(裝置未就緒。)。
>2016-04-25 12:20:21.08 spid1s      記錄排清期間發生寫入錯誤。
>
>2016-04-25 12:20:21.08 spid79      錯誤：9001，嚴重性：21，狀態：4.
>
>2016-04-25 12:20:21.08 spid79      資料庫 'AutoHa-Sample' 的記錄檔無法使用。 相關錯誤訊息請查閱事件記錄檔。 解決任何錯誤，並重新啟動資料庫。
>
>**2016-04-25 12:20:21.15 spid79      錯誤：41653，嚴重性：21，狀態：1.**
>
>**2016-04-25 12:20:21.15 spid79      資料庫 'AutoHa-Sample' 遇到錯誤 (錯誤類型：2 'DB_SHUTDOWN') 導致可用性群組 'Contoso-ag' 失敗。如需發現之錯誤的資訊，請參考 SQL Server 錯誤記錄檔。如果此狀況持續發生，請連絡系統管理員。**
>
>2016-04-25 12:20:21.17 spid79      資料庫 'AutoHa-Sample' 的狀態資訊 - Hardened Lsn：'(34:664:1)'    認可 LSN：'(34:656:1)'    認可時間：'Apr 25 2016 12:19PM'
>
>2016-04-25 12:20:21.19 spid15s     已終止複本識別碼為 {c4ad5ea4-8a99-41fa-893e-189154c24b49} 之可用性複本 'SQLServer-0' 上的主要資料庫 'AutoHa-Sample' 與次要資料庫的 AlwaysOn 可用性群組連接。 此為參考用訊息， 使用者不必採取任何動作。
>
>2016-04-25 12:20:21.21 spid75      Always On：可用性群組 'Contoso-ag' 的本機複本正在準備轉換成解析角色，以回應 Windows Server 容錯移轉叢集 (WSFC) 叢集的要求。 此為參考用訊息， 使用者不必採取任何動作。
>
>2016-04-25 12:20:21.21 spid75      在可用性群組 'ag' 中，本機可用性複本的狀態已經從 'PRIMARY_NORMAL' 變更為 'RESOLVING_NORMAL'。  狀態因可用性群組即將離線而變更。  複本即將離線，因為已刪除相關聯的可用性群組，或使用者讓 Windows Server 容錯移轉叢集 (WSFC) 管理主控台中的相關聯可用性群組離線，或者可用性群組會容錯移轉至另一個 SQL Server 執行個體。  如需詳細資訊，請參閱 SQL Server 錯誤記錄檔、Windows Server 容錯移轉叢集 (WSFC) 管理主控台或 WSFC 記錄檔。

### <a name="extended-event-sqlserveravailability_replica_database_fault_reporting"></a>擴充事件 sqlserver.availability_replica_database_fault_reporting

有從 SQL Server 2016 開始定義的新擴充事件，且其是由資料庫層級健全狀況偵測所觸發。  事件名稱是 **sqlserver.availability_replica_database_fault_reporting**

只會在主要複本上觸發這個 XEvent。 偵測裝載於可用性群組之資料庫的資料庫層級健全狀況問題時，會觸發這個 XEvent。

以下是建立可擷取此事件之 XEvent 工作階段的範例。 未指定路徑時，XEvent 輸出檔案應該位於預設 SQL Server 錯誤記錄檔路徑中。 在可用性群組的主要複本上執行這個項目：

範例擴充事件工作階段指令碼

```sql
CREATE EVENT SESSION [AlwaysOn_dbfault] ON SERVER
ADD EVENT sqlserver.availability_replica_database_fault_reporting
ADD TARGET package0.event_file(SET filename=N'dbfault.xel',max_file_size=(5),max_rollover_files=(4))
WITH (MAX_MEMORY=4096 KB,EVENT_RETENTION_MODE=ALLOW_SINGLE_EVENT_LOSS,MAX_DISPATCH_LATENCY=30 SECONDS,
    MAX_EVENT_SIZE=0 KB,MEMORY_PARTITION_MODE=NONE,TRACK_CAUSALITY=OFF,STARTUP_STATE=ON)
GO
ALTER EVENT SESSION AlwaysOn_dbfault ON SERVER STATE=START
GO
```

#### <a name="extended-event-output"></a>擴充事件輸出
使用 SQL Server Management Studio 連接到主要 SQL Server，並依序展開 [管理] 節點和 [擴充事件]。 找到工作階段 (AlwaysOn_dbfault 是上述範例中的名稱)，並將其展開以查看輸出檔案。 按一下輸出檔案，就會在新的索引標籤中開啟事件檔案。

欄位的說明：

|資料行資料 | 描述|
|---------|---------|
|availability_group_id |可用性群組的識別碼。|
|availability_group_name |可用性群組的名稱。|
|availability_replica_id |可用性複本的識別碼。|
|availability_replica_name |可用性複本的名稱。|
|database_name |報告錯誤的資料庫名稱。|
|database_replica_id |可用性複本資料庫的識別碼。|
|failover_ready_replicas |已同步處理的自動容錯移轉次要複本數目。|
|fault_type  | 所報告的錯誤識別碼。 可能的值：  <br/> 0 - 無 <br/>1 - 未知<br/>2 - 關機|
|is_critical | 從 SQL Server 2016 開始，XEvent 的這個值應該一律傳回 True。|


在此範例輸出中，fault_type 會因資料庫名稱 AutoHa-Sample2 而顯示可用性群組 Contoso-ag、名為 SQLSERVER-1 之複本上發生的嚴重事件，錯誤類型為「2- 關機」。

|欄位  | 值|
|---------|---------|
|availability_group_id | 24E6FE58-5EE8-4C4E-9746-491CFBB208C1|
|availability_group_name | Contoso-ag|
|availability_replica_id | 3EAE74D1-A22F-4D9F-8E9A-DEFF99B1F4D1|
|availability_replica_name | SQLSERVER-1|
|database_name | AutoHa-Sample2|
|database_replica_id | 39971379-8161-4607-82E7-098590E5AE00|
|failover_ready_replicas | 1|
|fault_type | 2|
|is_critical | True|


### <a name="related-references"></a>相關的參考資料

* [CREATE AVAILABILITY GROUP](../../../t-sql/statements/create-availability-group-transact-sql.md)

* [ALTER AVAILABILITY GROUP](../../../t-sql/statements/alter-availability-group-transact-sql.md)

* [可用性群組自動容錯移轉的彈性容錯移轉原則 (SQL Server)](./configure-flexible-automatic-failover-policy.md)

* [讓 AlwaysOn 容錯移轉原則測試 SQL Server Database Data 和記錄磁碟機](/archive/blogs/alwaysonpro/enhance-alwayson-failover-policy-to-test-sql-server-database-data-and-log-drives)

* [擴充事件](../../../relational-databases/extended-events/extended-events.md)