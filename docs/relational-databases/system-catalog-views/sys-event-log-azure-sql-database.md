---
description: sys.event_log (Azure SQL Database)
title: sys.event_log (Azure SQL Database) Microsoft Docs
ms.custom: ''
ms.date: 01/28/2019
ms.service: sql-database
ms.prod_service: sql-database
ms.reviewer: ''
ms.topic: language-reference
f1_keywords:
- event_log
- sys.event_log_TSQL
- event_log_TSQL
- sys.event_log
dev_langs:
- TSQL
helpviewer_keywords:
- event_log
- sys.event_log
ms.assetid: ad5496b5-e5c7-4a18-b5a0-3f985d7c4758
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: = azuresqldb-current
ms.openlocfilehash: c3a358e23d9428440166dd98a0f29ca8e4ea11c5
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98093095"
---
# <a name="sysevent_log-azure-sql-database"></a>sys.event_log (Azure SQL Database)

[!INCLUDE[Azure SQL Database](../../includes/applies-to-version/asdb.md)]

  傳回成功的 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 資料庫連接、連接失敗和鎖死。 您可以使用這項資訊追蹤 [!INCLUDE[ssSDS](../../includes/sssds-md.md)] 的資料庫活動或進行疑難排解。  
  
> [!CAUTION]  
> 如果安裝有大量的資料庫或大量的登入，sys.event_log 中的活動可能會導致效能限制、高 CPU 使用率，而且可能會導致登入失敗。 Sys.event_log 的查詢可能會對問題造成影響。 Microsoft 正致力於解決此問題。 在此同時，若要降低此問題的影響，請限制 sys.event_log 的查詢。 NewRelic SQL Server 外掛程式的使用者應該流覽 [Microsoft Azure SQL Database 外掛程式調整 & 效能調整](https://discuss.newrelic.com/t/microsoft-azure-sql-database-plugin-tuning-performance-tweaks/30729) ，以取得額外的設定資訊。  
  
 `sys.event_log` 檢視包含以下資料行。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**database_name**|**sysname**|資料庫的名稱。 如果連接失敗且使用者未指定資料庫名稱，則這個資料行會是空白。|  
|**start_time**|**datetime2**|彙總間隔開始的 UTC 日期和時間。 對於彙總的事件，這個時間永遠是 5 分鐘的倍數。 例如：<br /><br /> '2011-09-28 16:00:00'<br />'2011-09-28 16:05:00'<br />'2011-09-28 16:10:00'|  
|**end_time**|**datetime2**|彙總間隔結束的 UTC 日期和時間。 對於匯總的事件， **End_time** 一律會比同一資料列中對應的 **start_time** 晚5分鐘。 對於未匯總的事件， **start_time** 和 **end_time** 等於事件的實際 UTC 日期和時間。|  
|**event_category**|**Nvarchar (64)**|產生這個事件的高階元件。<br /><br /> 如需可能值的清單，請參閱 [事件種類](../../relational-databases/system-catalog-views/sys-event-log-azure-sql-database.md#EventTypes) 。|  
|**event_type**|**Nvarchar (64)**|事件的類型。<br /><br /> 如需可能值的清單，請參閱 [事件種類](../../relational-databases/system-catalog-views/sys-event-log-azure-sql-database.md#EventTypes) 。|  
|**event_subtype**|**int**|所發生事件的子類型。<br /><br /> 如需可能值的清單，請參閱 [事件種類](../../relational-databases/system-catalog-views/sys-event-log-azure-sql-database.md#EventTypes) 。|  
|**event_subtype_desc**|**Nvarchar (64)**|事件子類型的描述。<br /><br /> 如需可能值的清單，請參閱 [事件種類](../../relational-databases/system-catalog-views/sys-event-log-azure-sql-database.md#EventTypes) 。|  
|**severity**|**int**|錯誤的嚴重性。 可能的值包括：<br /><br /> 0 = 資訊<br />1 = 警告<br />2 = 錯誤|  
|**event_count**|**int**|指定資料庫在指定的時間間隔內，針對指定的資料庫發生的次數 (**start_time** 和 **end_time**) 。|  
|**description**|**nvarchar(max)**|事件的詳細描述。<br /><br /> 如需可能值的清單，請參閱 [事件種類](../../relational-databases/system-catalog-views/sys-event-log-azure-sql-database.md#EventTypes) 。|  
|**additional_data**|**XML**|*注意： Azure SQL Database V12 的這個值一律是 Null。請參閱 [範例](#Deadlock) 一節，以瞭解如何取出 V12 的鎖死事件。*<br /><br /> 針對 **鎖死** 事件，此資料行包含鎖死圖形。 對於其他事件類型，這個資料行為 NULL。 |  
  
##  <a name="event-types"></a><a name="EventTypes"></a> 事件種類

 此視圖中的每個資料列所記錄的事件都是由類別 (**event_category**) 、事件種類 (**event_type**) 和子類型 **(event_subtype) 。** 下表列出這個檢視中所收集事件的類型。  
  
 針對 [連線 **能力** ] 類別中的事件，sys.database_connection_stats 視圖提供摘要資訊。  
  
> [!NOTE]  
> 這個檢視不包括所有可能發生的 [!INCLUDE[ssSDS](../../includes/sssds-md.md)] 資料庫事件，只包括這裡列出的事件。 其他類別目錄、事件類型和子類型會在未來的 [!INCLUDE[ssSDS](../../includes/sssds-md.md)] 版本中加入。  
  
|**event_category**|**event_type**|**event_subtype**|**event_subtype_desc**|**severity**|**description**|  
|-------------------------|---------------------|------------------------|------------------------------|------------------|---------------------|  
|**連接**|**connection_successful**|0|**connection_successful**|0|已成功連接資料庫。|  
|**連接**|**connection_failed**|0|**invalid_login_name**|2|登入名稱在這個版本的 SQL Server 中無效。|  
|**連接**|**connection_failed**|1|**windows_auth_not_supported**|2|這個版本的 SQL Server 不支援 Windows 登入。|  
|**連接**|**connection_failed**|2|**attach_db_not_supported**|2|使用者要求附加不支援的資料庫檔案。|  
|**連接**|**connection_failed**|3|**change_password_not_supported**|2|不支援使用者要求變更使用者登入的密碼。|  
|**連接**|**connection_failed**|4|**login_failed_for_user**|2|使用者登入失敗。|  
|**連接**|**connection_failed**|5|**login_disabled**|2|登入已停用。|  
|**連接**|**connection_failed**|6|**failed_to_open_db**|2|*注意：僅適用于 Azure SQL Database V11。*<br /><br /> 資料庫無法開啟。 可能原因是資料庫不存在，或缺少開啟資料庫的驗證。|  
|**連接**|**connection_failed**|7|**blocked_by_firewall**|2|不允許用戶端 IP 位址存取伺服器。|  
|**連接**|**connection_failed**|8|**client_close**|2|*注意：僅適用于 Azure SQL Database V11。*<br /><br /> 用戶端可能在建立連接時逾時。 嘗試建立連接時發生逾時。|  
|**連接**|**connection_failed**|9|**重 構**|2|*注意：僅適用于 Azure SQL Database V11。*<br /><br /> 連接失敗，因為資料庫當時正在進行重新組態。|  
|**連接**|**connection_terminated**|0|**idle_connection_timeout**|2|*注意：僅適用于 Azure SQL Database V11。*<br /><br /> 連接已閒置超過系統定義的臨界值。|  
|**連接**|**connection_terminated**|1|**重 構**|2|*注意：僅適用于 Azure SQL Database V11。*<br /><br /> 由於資料庫重新設定，已終止工作階段。|  
|**連接**|**節流**|*\<reason code>*|**reason_code**|2|*注意：僅適用于 Azure SQL Database V11。*<br /><br /> 要求已節流。  節流原因代碼： *\<reason code>* 。 如需詳細資訊，請參閱 [引擎節流](/previous-versions/azure/dn338079(v=azure.100))。|  
|**連接**|**throttling_long_transaction**|40549|**long_transaction**|2|*注意：僅適用于 Azure SQL Database V11。*<br /><br /> 工作階段已終止，因為您有長時間執行的交易。 請嘗試縮短您的交易。 如需詳細資訊，請參閱 [資源限制](/previous-versions/azure/dn338081(v=azure.100))。|  
|**連接**|**throttling_long_transaction**|40550|**excessive_lock_usage**|2|*注意：僅適用于 Azure SQL Database V11。*<br /><br /> 已終止工作階段，因為它取得太多鎖定。 請嘗試在單一交易中讀取或修改較少的資料列。 如需詳細資訊，請參閱 [資源限制](/previous-versions/azure/dn338081(v=azure.100))。|  
|**連接**|**throttling_long_transaction**|40551|**excessive_tempdb_usage**|2|*注意：僅適用于 Azure SQL Database V11。*<br /><br /> 已終止工作階段，因為它過度使用 TEMPDB。 請嘗試修改查詢，減少使用暫存資料表空間。 如需詳細資訊，請參閱 [資源限制](/previous-versions/azure/dn338081(v=azure.100))。|  
|**連接**|**throttling_long_transaction**|40552|**excessive_log_space_usage**|2|*注意：僅適用于 Azure SQL Database V11。*<br /><br /> 已終止工作階段，因為它過度使用交易記錄檔空間。 請嘗試在單一交易中修改較少的資料列。 如需詳細資訊，請參閱 [資源限制](/previous-versions/azure/dn338081(v=azure.100))。|  
|**連接**|**throttling_long_transaction**|40553|**excessive_memory_usage**|2|*注意：僅適用于 Azure SQL Database V11。*<br /><br /> 已終止工作階段，因為它過度使用記憶體。 請嘗試修改查詢以處理較少的資料列。 如需詳細資訊，請參閱 [資源限制](/previous-versions/azure/dn338081(v=azure.100))。|  
|**發動機**|**僵局**|0|**僵局**|2|發生死結。|  
  
## <a name="permissions"></a>權限

 擁有存取 **master** 資料庫之許可權的使用者，擁有此視圖的唯讀存取權。  
  
## <a name="remarks"></a>備註  
  
### <a name="event-aggregation"></a>事件彙總

 這個檢視的事件資訊會在 5 分鐘間隔內收集和彙總。 **Event_count** 資料行代表在給定時間間隔內，特定資料庫的特定 **event_type** 和 **event_subtype** 發生的次數。  
  
> [!NOTE]  
> 某些事件不會彙總，像是死結。 對於這些事件， **event_count** 將會是1並 **start_time** ，而且 **end_time** 會等於事件發生時實際的 UTC 日期和時間。  
  
 例如，如果使用者因為登入名稱無效，而在連接到 Database1 資料庫時於 2012 年 2 月 5 日 11:00 到 11:05 (UTC) 之間失敗七次，這項資訊會在這個檢視的單一資料列中提供：  
  
|**database_name**|**start_time**|**end_time**|**event_category**|**event_type**|**event_subtype**|**event_subtype_desc**|**severity**|**event_count**|**description**|**additional_data**|  
|------------------------|---------------------|-------------------|-------------------------|---------------------|------------------------|------------------------------|------------------|----------------------|---------------------|--------------------------|  
|`Database1`|`2012-02-05 11:00:00`|`2012-02-05 11:05:00`|`connectivity`|`connection_failed`|`4`|`login_failed_for_user`|`2`|`7`|`Login failed for user.`|`NULL`|  
  
### <a name="interval-start_time-and-end_time"></a>間隔的 start_time 和 end_time  
 當事件發生在 **start_time** 之後，或 _在_ 該間隔 **end_time** _之前_，事件 *就會包含* 在匯總間隔內。 例如，正巧發生在 `2012-10-30 19:25:00.0000000` 的事件只會納入到以下所示的第二段間隔：  
  
```
start_time                    end_time  
2012-10-30 19:20:00.0000000   2012-10-30 19:25:00.0000000  
2012-10-30 19:25:00.0000000   2012-10-30 19:30:00.0000000  
```

### <a name="data-updates"></a>資料更新

 這個檢視中的資料會累積一段時間。 通常資料會在彙總間隔開始的 1 小時內累積，但是最長可能需要 24 小時，所有資料才會出現在檢視中。 在這段期間，單一資料列內的資訊會定期更新。  
  
### <a name="data-retention"></a>資料保留

 此視圖中的資料最多會保留30天，或根據資料庫數目和每個資料庫所產生之唯一事件的數目而定，可能會較少。 若要保留這項資訊更長的時間，請將資料複製到另一個資料庫。 在您製作檢視的初始副本之後，檢視中的資料列可能會隨資料累積而更新。 為了讓資料副本保持最新狀態，請定期執行資料列的資料表掃描，查看現有資料列的事件計數是否增加，並且識別新資料列 (您可以使用開始和結束時間識別唯一資料列)，然後用這些變更來更新您的資料副本。  
  
### <a name="errors-not-included"></a>不包括錯誤

 這個檢視可能不會包括所有連接和錯誤資訊：  
  
- 此視圖不包括所有 [!INCLUDE[ssSDS](../../includes/sssds-md.md)] 可能發生的資料庫錯誤，只包括本主題的 [事件種類](../../relational-databases/system-catalog-views/sys-event-log-azure-sql-database.md#EventTypes) 中所指定的錯誤。  
- 如果資料中心內發生機器故障 [!INCLUDE[ssSDS](../../includes/sssds-md.md)] ，則事件資料表中可能會遺失少量的資料。  
- 如果已透過 DoSGuard 封鎖 IP 位址，則來自該 IP 位址的連接嘗試事件就無法收集，也不會出現在這個檢視中。  
  
## <a name="examples"></a>範例  
  
### <a name="simple-examples"></a>簡單範例

 以下查詢會傳回 2011 年 9 月 25 日中午到 2011 年 9 月 28 日中午 (UTC) 之間發生的所有事件。 根據預設，查詢結果會依 **start_time** (遞增順序) 排序。  

```sql
SELECT * FROM sys.event_log
WHERE start_time >= '2011-09-25 12:00:00'
    AND end_time <= '2011-09-28 12:00:00';  
```

下列查詢會傳回資料庫 Database1 的所有鎖死事件 (只適用于 Azure SQL Database V11) 。  

```sql
SELECT * FROM sys.event_log
WHERE event_type = 'deadlock'
    AND database_name = 'Database1';  
```

<a name="Deadlock"></a> 下列查詢會傳回資料庫 Database1 的所有鎖死事件 (僅適用于 Azure SQL Database V12) 。  

```sql
WITH CTE AS (  
       SELECT CAST(event_data AS XML)  AS [target_data_XML]  
   FROM sys.fn_xe_telemetry_blob_target_read_file('dl', null, null, null)  
)  
SELECT target_data_XML.value('(/event/@timestamp)[1]', 'DateTime2') AS Timestamp,  
target_data_XML.query('/event/data[@name=''xml_report'']/value/deadlock') AS deadlock_xml,  
target_data_XML.query('/event/data[@name=''database_name'']/value').value('(/value)[1]', 'nvarchar(100)') AS db_name  
FROM CTE  
```

以下查詢會傳回 2011 年 9 月 25 日上午 10:00 到 11:00 (UTC) 之間在發生的 SQL 工作者執行緒事件上發生的硬節流。  

```sql
SELECT * FROM sys.event_log
WHERE event_type = 'throttling'
    AND event_subtype = 4194307
    AND start_time >= '2011-09-25 10:00:00'
    AND end_time <= '2011-09-25 11:00:00';  
```

### <a name="db-scoped-extended-event"></a>DB-Scoped 擴充的事件

 使用下列範例程式碼，將資料庫範圍的擴充事件設定 (XEvent) 會話：  

```sql
IF EXISTS  
    (SELECT * from sys.database_event_sessions  
        WHERE name = 'azure_monitor_deadlock_session')  
BEGIN  
    ALTER EVENT SESSION azure_monitor_deadlock_session  
        ON DATABASE  
        DROP TARGET package0.ring_buffer;  
  
    DROP EVENT SESSION azure_monitor_deadlock_session  
        ON DATABASE;  
END  
  
CREATE EVENT SESSION azure_monitor_deadlock_session  
    ON DATABASE  
    ADD EVENT sqlserver.database_xml_deadlock_report  
    ADD TARGET package0.ring_buffer  
    (  
        SET max_memory = 2048, max_events_limit = 10  
    )  
    WITH (STARTUP_STATE = ON,  
          EVENT_RETENTION_MODE = ALLOW_SINGLE_EVENT_LOSS);  
  
ALTER EVENT SESSION azure_monitor_deadlock_session  
    ON DATABASE  
    STATE = START;  
```

### <a name="check-for-deadlock"></a>檢查是否有鎖死

使用下列查詢來檢查是否有鎖死。  

```sql
WITH CTE AS (  
    SELECT CAST(xet.target_data AS XML)  AS [target_data_XML]  
        FROM            sys.dm_xe_database_session_targets AS xet  
             INNER JOIN sys.dm_xe_database_sessions        AS xe  
                 ON (xe.address = xet.event_session_address)  
        WHERE xe.name = 'azure_monitor_deadlock_session'  
)  
, CTE2 AS (  
    SELECT  
            T2.EventData.query('.').value(  
                '(/event/@timestamp)[1]', 'DateTime2') AS Timestamp,  
            T2.EventData.query('.').query(  
                '(/event/data/value/deadlock)[1]')     AS deadlock_xml  
        FROM CTE  
            CROSS Apply [target_data_XML].nodes(  
                '/RingBufferTarget/event') AS T2(EventData)  
)  
SELECT * FROM CTE2;  
```

## <a name="see-also"></a>另請參閱

 [Azure SQL Database 中的擴充事件](/azure/azure-sql/database/xevent-db-diff-from-svr)  
