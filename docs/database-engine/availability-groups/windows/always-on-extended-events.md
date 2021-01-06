---
title: 設定可用性群組的延伸事件
description: SQL Server 定義特定於 Always On 可用性群組的延伸事件。 當您對可用性群組進行疑難排解時，可以在某個工作階段中監視這些擴充事件來協助診斷根本原因。
ms.custom: ag-guide, seodec18
ms.date: 06/13/2017
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: how-to
ms.assetid: 5950f98a-3950-473d-95fd-cde3557b8fc2
author: rothja
ms.author: jroth
ms.openlocfilehash: 28eab9dae04e0268c6dd7827aab8be71220e4218
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97643733"
---
# <a name="configure-extended-events-for-always-on-availability-groups"></a>設定 Always On 可用性群組的延伸事件
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]
  SQL Server 定義特定於 Always On 可用性群組的延伸事件。 當您對可用性群組進行疑難排解時，可以在某個工作階段中監視這些擴充事件來協助診斷根本原因。 您可以使用下列查詢檢視可用性群組擴充事件：  
  
```sql  
SELECT * FROM sys.dm_xe_objects WHERE name LIKE '%hadr%'  
```  
   
##  <a name="alwayson_health-session"></a><a name="BKMK_alwayson_health"></a> Alwayson_health 工作階段  
 Alwayson_health 擴充事件工作階段是在您建立可用性群組時自動建立，會擷取可用性群組相關事件的子集。 此工作階段已預先設定為實用且方便的工具，可協助您對可用性群組進行疑難排解時快速開始。 「建立可用性群組精靈」會自動在精靈中設定的每個參與可用性複本上啟動工作階段。  
  
> [!IMPORTANT]  
>  如果您未使用 **新增可用性群組精靈** 建立可用性群組，alwayson_health 工作階段可能無法自動啟動。 如果工作階段未啟動，就無法在發生未預期的問題時擷取事件資料。 您應該手動啟動工作階段，並設定工作階段內容，將工作階段設定為自動啟動。  
  
 檢視 alwayson_health 工作階段的定義：  
  
1.  在 [物件總管]  中，依序展開 [管理]  、[擴充事件]  和 [工作階段]  。  
  
2.  以滑鼠右鍵按一下 [Alwayson_health]  ，然後指向 [編寫工作階段的指令碼為]  ，然後指向 [CREATE 至]  ，然後按一下 [新增查詢編輯器視窗]  。  

如需 alwayson_health 所涵蓋部分事件的資訊，請參閱[擴充事件參考](always-on-extended-events.md#BKMK_Reference)。  


##  <a name="extended-events-for-debugging"></a><a name="BKMK_Debugging"></a> 偵錯的擴充事件  
 除了 Alwayson_health 工作階段涵蓋的擴充事件，SQL Server 也為可用性群組定義一組廣泛的偵錯事件。 若要在工作階段中利用這些額外的擴充事件，請遵循下列程序：  
  
1.  在 [物件總管]  中，依序展開 [管理]  、[擴充事件]  和 [工作階段]  。  
  
2.  以滑鼠右鍵按一下 [工作階段]  ，然後選取 [新增工作階段]  。 或者，以滑鼠右鍵按一下 [Alwayson_health]  ，選取 [屬性]  。  
  
3.  在 [選取頁面]  窗格中，按一下 [事件]  。  
  
4.  在事件程式庫的 [類別目錄]  欄中，選取 [alwayson]  並清除所有其他類別目錄。  
  
5.  在 [通道]  欄中，選取 [偵錯]  。 尚未選取的所有可用性群組相關事件現在會顯示在事件程式庫中。  
  
6.  反白顯示事件程式庫中的某個事件，然後按一下 [ **>** ] 按鈕為工作階段選取事件。  
  
7.  完成該工作階段之後，請按一下 [確定]  關閉。 請確定工作階段已啟動，才能擷取您所選取的事件。  
  
##  <a name="always-on-availability-groups-extended-events-reference"></a><a name="BKMK_Reference"></a> Always On 可用性群組擴充事件參考  
 本節描述一些可用來監視可用性群組的擴充事件。  
  
 [availability_replica_state_change](#BKMK_availability_replica_state_change)  
  
 [availability_group_lease_expired](#BKMK_availability_group_lease_expired)  
  
 [availability_replica_automatic_failover_validation](#BKMK_availability_replica_automatic_failover_validation)  
  
 [error_reported (多個錯誤號碼)：適用於傳輸或連線問題](#BKMK_error_reported)  
  
 [data_movement_suspend_resume](#BKMK_data_movement_suspend_resume)  
  
 [alwayson_ddl_executed](#BKMK_alwayson_ddl_executed)  
  
 [availability_replica_manager_state](#BKMK_availability_replica_manager_state)  
  
 [error_reported (1480)：資料庫複本角色變更](#BKMK_error_reported_1480)  
  
###  <a name="availability_replica_state_change"></a><a name="BKMK_availability_replica_state_change"></a> availability_replica_state_change  
 在可用性複本的狀態變更時發生。 建立可用性群組或加入可用性複本會觸發此事件。 它對於診斷失敗的自動容錯移轉很好用。 它也可以用來追蹤容錯移轉步驟。  
  
#### <a name="event-information"></a>事件資訊  
  
|資料行|描述|  
|------------|-----------------|  
|名稱|availability_replica_state_change|  
|類別|永遠開啟|  
|通路|運作|  
  
#### <a name="event-fields"></a>事件欄位  
  
|名稱|Type_name|描述|  
|----------|----------------|-----------------|  
|availability_group_id|guid|可用性群組的識別碼。|  
|availability_group_name|unicode_string|可用性群組的名稱。|  
|availability_replica_id|guid|可用性複本的識別碼。|  
|previous_state|availability_replica_state|複本在變更之前的角色。<br /><br /> **可能的值為：**<br /><br /> Primary_Normal<br /><br /> Secondary_Normal<br /><br /> Resolving_Pending_Failover<br /><br /> Resolving_Normal<br /><br /> Primary_Pending<br /><br /> Not_Available|  
|current_state|availability_replica_state|複本在變更之後的角色。<br /><br /> **可能的值為：**<br /><br /> Primary_Normal<br /><br /> Secondary_Normal<br /><br /> Resolving_Pending_Failover<br /><br /> Resolving_Normal<br /><br /> Primary_Pending<br /><br /> Not_Available|  
  
#### <a name="alwayson_health-session-definition"></a>alwayson_health 工作階段定義  
  
```sql  
CREATE EVENT SESSION [alwayson_health] ON SERVER   
ADD EVENT availability_replica_state_change  
ADD TARGET package0.event_file(SET filename=N'alwayson_health.xel',max_file_size=(5),max_rollover_files=(4),metadatafile=N'alwayson_health.xem')  
WITH (MAX_MEMORY=4096 KB,EVENT_RETENTION_MODE=ALLOW_SINGLE_EVENT_LOSS,MAX_DISPATCH_LATENCY=30 SECONDS,MAX_EVENT_SIZE=0 KB,MEMORY_PARTITION_MODE=NONE,TRACK_CAUSALITY=OFF,STARTUP_STATE=ON)  
GO  
```  
  
###  <a name="availability_group_lease_expired"></a><a name="BKMK_availability_group_lease_expired"></a> availability_group_lease_expired  
 當叢集和可用性群組有連線問題和租用到期時發生。 這個事件表示可用性群組與基礎 WSFC 叢集之間的連線已中斷。 如果主要複本上發生連線問題，事件可能會造成自動容錯移轉，或造成可用性群組離線。  
  
#### <a name="event-information"></a>事件資訊  
  
|資料行|描述|  
|------------|-----------------|  
|名稱|availability_group_lease_expired|  
|類別|永遠開啟|  
|通路|運作|  
  
#### <a name="event-fields"></a>事件欄位  
  
|名稱|Type_name|描述|  
|----------|----------------|-----------------|  
|availability_group_id|guid|可用性群組的識別碼。|  
|availability_group_name|unicode_string|可用性群組的名稱。|  
  
#### <a name="alwayson_health-session-definition"></a>alwayson_health 工作階段定義  
  
```sql  
CREATE EVENT SESSION [alwayson_health] ON SERVER   
ADD EVENT availability_group_lease_expired  
ADD TARGET package0.event_file(SET filename=N'alwayson_health.xel',max_file_size=(5),max_rollover_files=(4),metadatafile=N'alwayson_health.xem')  
WITH (MAX_MEMORY=4096 KB,EVENT_RETENTION_MODE=ALLOW_SINGLE_EVENT_LOSS,MAX_DISPATCH_LATENCY=30 SECONDS,MAX_EVENT_SIZE=0 KB,MEMORY_PARTITION_MODE=NONE,TRACK_CAUSALITY=OFF,STARTUP_STATE=ON)  
GO  
```  
  
###  <a name="availability_replica_automatic_failover_validation"></a><a name="BKMK_availability_replica_automatic_failover_validation"></a> availability_replica_automatic_failover_validation  
 當自動容錯移轉將可用性複本的整備度驗證為主要複本時發生，會顯示目標可用性複本是否已準備好成為新的主要複本。 例如，容錯移轉驗證會在並非所有資料庫都同步處理或未聯結時傳回 false。 此事件是設計來提供容錯移轉期間的失敗點。 這項資訊是特別針對自動容錯移轉，對資料庫管理員很重要，因為自動容錯移轉是自動的作業。 資料庫管理員可以檢閱事件，以查看自動容錯移轉失敗的原因。  
  
#### <a name="event-information"></a>事件資訊  
  
|名稱|描述|  
|----------|-----------------|  
|availability_replica_automatic _failover_validation||  
|類別|永遠開啟|  
|通路|分析|  
  
#### <a name="event-fields"></a>事件欄位  
  
|名稱|Type_name|描述|  
|----------|----------------|-----------------|  
|availability_group_id|guid|可用性群組的識別碼。|  
|availability_group_name|unicode_string|可用性群組的名稱。|  
|availability_replica_id|guid|可用性複本的識別碼。|  
|forced_quorum|validation_result_type|如果值為 TRUE，則這個可用性複本上的自動容錯移轉已失效。<br /><br /> TRUE<br /><br /> FALSE|  
|joined_and_synchronized|validation_result_type|如果值為 FALSE，則這個可用性複本上的自動容錯移轉已失效。<br /><br /> TRUE<br /><br /> FALSE|  
|previous_primary_or_automatic_failover_target|validation_result_type|如果值為 FALSE，則這個可用性複本上的自動容錯移轉已失效。<br /><br /> TRUE<br /><br /> FALSE|  
  
#### <a name="alwayson_health-session-definition"></a>alwayson_health 工作階段定義  
  
```sql  
CREATE EVENT SESSION [alwayson_health] ON SERVER   
  
ADD EVENT availability_replica_automatic_failover_validation (  
WHERE (  
[forced_quorum]=(TRUE) OR [joined_and_synchronized]=(FALSE) OR [previous_primary_or_automatic_failover_target]=(TRUE)  
)  
)  
  
ADD TARGET package0.event_file(SET filename=N'alwayson_health.xel',max_file_size=(5),max_rollover_files=(4),metadatafile=N'alwayson_health.xem')  
WITH (MAX_MEMORY=4096 KB,EVENT_RETENTION_MODE=ALLOW_SINGLE_EVENT_LOSS,MAX_DISPATCH_LATENCY=30 SECONDS,MAX_EVENT_SIZE=0 KB,MEMORY_PARTITION_MODE=NONE,TRACK_CAUSALITY=OFF,STARTUP_STATE=ON)  
GO  
  
```  
  
###  <a name="error_reported-multiple-error-numbers-for-transport-or-connection-issues"></a><a name="BKMK_error_reported"></a> error_reported (多個錯誤號碼)：適用於傳輸或連線問題  
 每個篩選的事件指出在可用性群組所依賴的傳輸或資料庫鏡像端點中發生的連線問題。  
  
|資料行|描述|  
|------------|-----------------|  
|名稱|error_reported<br /><br /> 要篩選的數字：35201、35202、35206、35204、35207、9642、9666、9691、9692、9693、28034、28036、28080、28091、33309|  
|類別|錯誤|  
|通路|管理|  
  
#### <a name="error-numbers-to-filter"></a>要篩選的錯誤號碼  
  
|錯誤號碼|描述|  
|------------------|-----------------|  
|35201|嘗試建立可用性複本 '%ls' 的連線時發生連線逾時。|  
|35202|已成功建立可用性群組 '%ls' 從可用性複本 '%ls' (識別碼為 [%ls]) 到 '%ls' (識別碼為 [%ls]) 的連線。  此為參考用訊息， 使用者不必採取任何動作。|  
|35206|先前建立到可用性複本 '%ls' 的連線發生連線逾時。|  
|35204|執行個體 '%ls' 與 '%ls' 之間的連線因為端點關閉而已停用。|  
|逾時 + 已連線|  
|35207|在可用性群組識別碼 '%ls' 上試圖從複本識別碼 '%ls' 連線至複本識別碼 '%ls' 失敗，因為發生錯誤 %d，嚴重性 %d，狀態 %d。  嚴重性 %d，狀態 %d。 (這可能不是很好的 DBA 用途。 請檢查，如果是該狀況則稍後移除)|  
|9642|Service Broker/資料庫鏡像傳輸連線端點發生錯誤，錯誤: %i，狀態: %i。 (近的端點角色: %S_MSG，遠的端點位址: '%.*hs') 錯誤: %i，狀態: %i。 (近的端點角色: %S_MSG，遠的端點位址: '%.\*hs')|  
|9666|%S_MSG 端點處於停用或已停止狀態。|  
|9691|%S_MSG 端點已停止接聽連線。|  
|9692|%S_MSG 端點無法接聽連接埠 %d，因為它正由另一個程序使用。|  
|9693|由於發生下列錯誤，所以 %S_MSG 端點無法接聽連線: '%.*ls'。|  
|28034|連接交握失敗。 登入 '%.*ls' 在端點上沒有 CONNECT 權限。 狀態 %d。|  
|28036|連接交握失敗。 找不到此端點使用的憑證: %S_MSG。 請在 master 資料庫執行 DBCC CHECKDB，驗證端點的中繼資料完整性。 狀態 %d。|  
|28080|連接交握失敗。 %S_MSG 端點尚未設定。 狀態 %d。|  
|28091|不支援在沒有驗證的情況下啟動 %S_MSG 的端點。|  
|33309|因為尚未載入預設的 %S_MSG 端點組態，所以無法啟動叢集端點。|  
  
#### <a name="alwayson_health-session-definition"></a>alwayson_health 工作階段定義  
  
```sql  
CREATE EVENT SESSION [alwayson_health] ON SERVER   
ADD EVENT sqlserver.error_reported(  
    WHERE   
(  
--Connectivity Error Messages  
[error_number]=(35201)   
OR [error_number]=(35202)   
OR [error_number]=(35204)   
OR [error_number]=(35206)   
OR [error_number]=(35207)   
OR [error_number]=(9642)   
--OR [error_number]=(9666)   
OR [error_number]=(9691)   
OR [error_number]=(9692)   
OR [error_number]=(9693)   
OR [error_number]=(28034)   
OR [error_number]=(28036)   
OR [error_number]=(28080)   
OR [error_number]=(28091)   
OR [error_number]=(33309)  
)  
)  
  
ADD TARGET package0.event_file(SET filename=N'alwayson_health.xel',max_file_size=(5),max_rollover_files=(4),metadatafile=N'alwayson_health.xem')  
WITH (MAX_MEMORY=4096 KB,EVENT_RETENTION_MODE=ALLOW_SINGLE_EVENT_LOSS,MAX_DISPATCH_LATENCY=30 SECONDS,MAX_EVENT_SIZE=0 KB,MEMORY_PARTITION_MODE=NONE,TRACK_CAUSALITY=OFF,STARTUP_STATE=ON)  
GO  
```  
  
###  <a name="data_movement_suspend_resume"></a><a name="BKMK_data_movement_suspend_resume"></a> data_movement_suspend_resume  
 資料庫複本的資料庫移動暫止或繼續時發生。  
  
#### <a name="event-information"></a>事件資訊  
  
|資料行|描述|  
|------------|-----------------|  
|名稱|data_movement_suspend_resume|  
|類別|一律開啟|  
|通路|運作|  
  
#### <a name="event-fields"></a>事件欄位  
  
|名稱|Type_name|描述|  
|-|-|-|  
|availability_group_id|guid|可用性群組的識別碼。|  
|availability_group_name|unicode_string|可用性群組的名稱 (如果有)。|  
|availability_replica_id|guid|可用性複本的識別碼。|  
|database_replica_id|guid|可用性資料庫的識別碼。|  
|database_replica_name|unicode_string|可用性資料庫的名稱。|  
|database_id|uint32|可用性資料庫的識別碼。|  
|suspend_status|suspend_status_type|暫止狀態值。<br /><br /> SUSPEND_NULL<br /><br /> RESUMED<br /><br /> SUSPENDED<br /><br /> SUSPENDED_INVALID|  
|suspend_source|suspend_source_type|暫止或繼續動作的來源。|  
|suspend_reason|unicode_string|在資料庫複本管理員中擷取到的暫止原因。|  
  
#### <a name="alwayson_health-session-definition"></a>alwayson_health 工作階段定義  
  
```sql  
CREATE EVENT SESSION [alwayson_health] ON SERVER   
  
ADD EVENT data_movement_suspend_resume (  
WHERE (  
[suspend_status]=(SUSPENDED)or [suspend_status]=(SUSPENDED_INVALID) or   
[suspend_status]=(SUSPEND_NULL)  
)  
)  
  
ADD TARGET package0.event_file(SET filename=N'alwayson_health.xel',max_file_size=(5),max_rollover_files=(4),metadatafile=N'alwayson_health.xem')  
WITH (MAX_MEMORY=4096 KB,EVENT_RETENTION_MODE=ALLOW_SINGLE_EVENT_LOSS,MAX_DISPATCH_LATENCY=30 SECONDS,MAX_EVENT_SIZE=0 KB,MEMORY_PARTITION_MODE=NONE,TRACK_CAUSALITY=OFF,STARTUP_STATE=ON)  
GO  
```  
  
###  <a name="alwayson_ddl_executed"></a><a name="BKMK_alwayson_ddl_executed"></a> alwayson_ddl_executed  
 可用性群組資料定義語言 (DDL) 陳述式 (包括 CREATE、ALTER 或 DROP) 執行時發生。 事件的主要目的是指出可用性複本上使用者動作的問題，或指出作業動作的起點，後面接執行階段問題，例如手動容錯移轉、強制容錯移轉，暫止的資料移動，或繼續的資料移動。  
  
#### <a name="event-information"></a>事件資訊  
  
|資料行|描述|  
|------------|-----------------|  
|名稱|alwayson_ddl_execution|  
|類別|永遠開啟|  
|通路|分析|  
  
#### <a name="event-fields"></a>事件欄位  
  
|名稱|Type_name|描述|  
|----------|----------------|-----------------|  
|availability_group_id|Guid|可用性群組的識別碼。|  
|availability_group_name|unicode_string|可用性群組的名稱。|  
|ddl_action|alwayson_ddl_action|表示 DDL 動作的類型：CREATE、ALTER 或 DROP。|  
|ddl_phase|ddl_opcode|表示 DDL 作業的階段：BEGIN、COMMIT 或 ROLLBACK。|  
|引數|unicode_string|所執行陳述式的文字。|  
  
#### <a name="alwayson_health-session-definition"></a>alwayson_health 工作階段定義  
  
```sql  
CREATE EVENT SESSION [alwayson_health] ON SERVER   
  
ADD EVENT alwayson_ddl_executed  
  
ADD TARGET package0.event_file(SET filename=N'alwayson_health.xel',max_file_size=(5),max_rollover_files=(4),metadatafile=N'alwayson_health.xem')  
WITH (MAX_MEMORY=4096 KB,EVENT_RETENTION_MODE=ALLOW_SINGLE_EVENT_LOSS,MAX_DISPATCH_LATENCY=30 SECONDS,MAX_EVENT_SIZE=0 KB,MEMORY_PARTITION_MODE=NONE,TRACK_CAUSALITY=OFF,STARTUP_STATE=ON)  
GO  
```  
  
###  <a name="availability_replica_manager_state"></a><a name="BKMK_availability_replica_manager_state"></a> availability_replica_manager_state  
 在可用性複本管理員的狀態變更時發生。 此事件表示可用性複本管理員的活動訊號。 當可用性複本管理員不是處於狀況良好狀態時，SQL Server 執行個體中的所有可用性複本都將會關閉。  
  
#### <a name="event-information"></a>事件資訊  
  
|資料行|描述|  
|------------|-----------------|  
|名稱|availability_replica_manager_state_change|  
|類別|永遠開啟|  
|通路|運作|  
  
#### <a name="event-fields"></a>事件欄位  
  
|名稱|Type_name|描述|  
|----------|----------------|-----------------|  
|current_state|manager_state|可用性複本管理員的目前狀態。<br /><br /> 線上<br /><br /> 離線<br /><br /> WaitingForClusterCommunication|  
  
#### <a name="alwayson_health-session-definition"></a>Alwayson_health 工作階段定義  
  
```sql  
CREATE EVENT SESSION [alwayson_health] ON SERVER   
  
ADD EVENT availability_replica_manager_state (  
WHERE ([current_state]=(OFFLINE))  
)  
  
ADD TARGET package0.event_file(SET filename=N'alwayson_health.xel',max_file_size=(5),max_rollover_files=(4),metadatafile=N'alwayson_health.xem')  
WITH (MAX_MEMORY=4096 KB,EVENT_RETENTION_MODE=ALLOW_SINGLE_EVENT_LOSS,MAX_DISPATCH_LATENCY=30 SECONDS,MAX_EVENT_SIZE=0 KB,MEMORY_PARTITION_MODE=NONE,TRACK_CAUSALITY=OFF,STARTUP_STATE=ON)  
GO  
```  
  
###  <a name="error_reported-1480-database-replica-role-change"></a><a name="BKMK_error_reported_1480"></a> error_reported (1480)：資料庫複本角色變更  
 此篩選的 error_reported 事件會在可用性複本角色變更之後以非同步方式發生。 它指出可用性資料庫在容錯移轉程序期間無法變更其預期的角色。  
  
#### <a name="event-information"></a>事件資訊  
  
|資料行|描述|  
|------------|-----------------|  
|名稱|error_reported<br /><br /> 錯誤號碼 1480：REPLICATION_TYPE_MSG 資料庫 "DATABASE_NAME" 因為 REASON_MSG 從 "OLD_ROLE" 角色變更為 "NEW_ROLE"|  
|類別|錯誤|  
|通路|管理|  
  
#### <a name="alwayson_health-session-definition"></a>alwayson_health 工作階段定義  
  
```sql  
CREATE EVENT SESSION [alwayson_health] ON SERVER   
ADD EVENT sqlserver.error_reported(  
    WHERE   
(  
--database replica role change message  
OR [error_number] = (1480)  
  
--database replica runtime error messages  
OR [error_number]=(823)   
OR [error_number]=(824)   
OR [error_number]=(829)  
)  
)  
  
ADD TARGET package0.event_file(SET filename=N'alwayson_health.xel',max_file_size=(5),max_rollover_files=(4),metadatafile=N'alwayson_health.xem')  
WITH (MAX_MEMORY=4096 KB,EVENT_RETENTION_MODE=ALLOW_SINGLE_EVENT_LOSS,MAX_DISPATCH_LATENCY=30 SECONDS,MAX_EVENT_SIZE=0 KB,MEMORY_PARTITION_MODE=NONE,TRACK_CAUSALITY=OFF,STARTUP_STATE=ON)  
GO  
```  
  
## <a name="next-steps"></a>後續步驟  
 [檢視事件工作階段資料](https://msdn.microsoft.com/library/hh710068(v=sql.110).aspx)   
