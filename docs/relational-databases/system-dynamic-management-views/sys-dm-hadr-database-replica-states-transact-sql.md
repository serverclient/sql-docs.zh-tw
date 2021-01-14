---
description: sys.dm_hadr_database_replica_states (Transact-SQL)
title: sys.dm_hadr_database_replica_states (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/26/2018
ms.prod: sql
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.dm_hadr_database_states_TSQL
- sys.dm_hadr_database_states
- dm_hadr_database_states
- dm_hadr_database_states_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- Availability Groups [SQL Server], monitoring
- sys.dm_hadr_database_replica_states dynamic management view
ms.assetid: 1a17b0c9-2535-4f3d-8013-cd0a6d08f773
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 8287eab936b7f95cc0cbaba78d4d7a90e403c42c
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98171160"
---
# <a name="sysdm_hadr_database_replica_states-transact-sql"></a>sys.dm_hadr_database_replica_states (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  針對參與 Always On 可用性群組的每一個資料庫，各傳回一個資料列，其中的本機實例 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 正在裝載可用性複本。 此動態管理檢視會公開主要和次要複本的相關狀態資訊。 在次要複本上，這個檢視會針對伺服器執行個體上的每個次要資料庫各傳回一個資料列。 在主要複本上，這個檢視會針對每個主要資料庫各傳回一個資料列，並針對對應的次要資料庫傳回額外的資料列。  
  
> [!IMPORTANT]
> 根據動作及更高層級的狀態而定，資料庫狀態資訊可能無法使用或是過期了。 此外，這些值只有本機關聯性。 例如，在主要複本上， **last_hardened_lsn** 資料行的值會反映目前可用於主要複本之給定次要資料庫的相關資訊，而不是次要複本目前可能擁有的實際強行寫入 lsn 值。  
   
|資料行名稱|資料類型|有關主要複本的說明|  
|-----------------|---------------|----------------------------------------|  
|**database_id**|**int**|資料庫的識別碼，在 SQL Server 執行個體內是唯一的。 這與 [sys. 資料庫](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中所顯示的值相同。|  
|**group_id**|**uniqueidentifier**|資料庫所屬之可用性群組的識別碼。|  
|**replica_id**|**uniqueidentifier**|可用性群組中可用性複本的識別碼。|  
|**group_database_id**|**uniqueidentifier**|可用性群組中資料庫的識別碼。 這個識別碼在此資料庫聯結的每個複本上都相同。|  
|**is_local**|**bit**|可用性資料庫是否為本機，下列其中一個值：<br /><br /> 0 = 資料庫不在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體的本機。<br /><br /> 1 = 資料庫在伺服器執行個體的本機。|  
|**is_primary_replica**|**bit**|如果複本是主要，則會傳回 1，或者如果它是次要複本，則會傳回 0。<br /><br />**適用於：** [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 及更新版本。|  
|**synchronization_state**|**tinyint**|資料移動狀態，下列其中一個值。<br /><br /> 0 = 未同步處理。 如果是主要資料庫，表示資料庫尚未準備好要將其交易記錄與對應的次要資料庫同步處理。 如果是次要資料庫，表示資料庫尚未開始記錄同步處理，原因是因為連接問題、處於暫停狀態，或在啟動或角色切換期間正在移轉狀態。<br /><br /> 1 = 正在同步處理。 如果是主要資料庫，表示此資料庫準備好接受次要資料庫的掃描要求。 如果是次要資料庫，表示資料庫正在進行資料移動作業。<br /><br /> 2 = 已同步處理。 主要資料庫會顯示 SYNCHRONIZED 而非 SYNCHRONIZING。 當本機快取表示資料庫已做好容錯移轉的準備而且正在同步處理時，同步認可次要資料庫就會顯示已同步處理。<br /><br /> 3 = 還原。 表示當次要資料庫積極取得主要資料庫的頁面時，復原程序中的階段。<br />**注意：** 當次要複本上的資料庫處於還原中狀態時，強制容錯移轉至次要複本會將資料庫保留在無法以主資料庫的形式啟動的狀態。 資料庫需要當做次要資料庫重新連接，或者您需要從記錄備份套用新的記錄檔記錄。<br /><br /> 4 = 正在初始化。 表示次要資料庫跟上復原 LSN 所需的交易記錄正在傳送而且在次要複本上強行寫入時的復原階段。<br />**注意：** 當次要複本上的資料庫處於初始化狀態時，強制容錯移轉至次要複本會將資料庫保留在無法以主資料庫的形式啟動的狀態。 資料庫需要當做次要資料庫重新連接，或者您需要從記錄備份套用新的記錄檔記錄。|  
|**synchronization_state_desc**|**nvarchar(60)**|資料移動狀態的描述，下列其中一個值：<br /><br /> NOT SYNCHRONIZING<br /><br /> SYNCHRONIZING<br /><br /> SYNCHRONIZED<br /><br /> REVERTING<br /><br /> INITIALIZING|  
|**is_commit_participant**|**bit**|0 = 未根據此資料庫同步處理交易認可。<br /><br /> 1 = 根據此資料庫同步處理交易認可。<br /><br /> 如果是非同步認可可用性複本上的資料庫，這個值一定是 0。<br /><br /> 如果是同步認可可用性複本上的資料庫，這個值僅在主要資料庫上才正確。|  
|**synchronization_health**|**tinyint**|反映加入可用性複本上之可用性群組的資料庫同步處理狀態的交集，以及可用性複本的可用性模式 (同步認可或非同步認可模式) ，下列其中一個值。<br /><br /> 0 = 狀況不良。 資料庫的 **synchronization_state** 為 0 (不會) 同步處理。<br /><br /> 1 = 部分狀況良好。 如果 **synchronization_state** 是 1 () 同步處理，則同步認可哥用性複本上的資料庫會被視為部分狀況良好。<br /><br /> 2 = 狀況良好。 如果 **synchronization_state** 是 2 (同步處理) ，同步認可哥用性複本上的資料庫會被視為狀況良好，而且如果 **synchronization_state** 是 1 (同步處理) ，則會將非同步認可哥用性複本上的資料庫視為狀況良好。|  
|**synchronization_health_desc**|**nvarchar(60)**|可用性資料庫的 **synchronization_health** 描述。<br /><br /> NOT_HEALTHY<br /><br /> PARTIALLY_HEALTHY<br /><br /> HEALTHY|  
|**database_state**|**tinyint**|0 = 線上<br /><br /> 1 = 還原中<br /><br /> 2 = 復原中<br /><br /> 3 = 復原暫止<br /><br /> 4 = 可疑<br /><br /> 5 = 緊急<br /><br /> 6 = 離線<br /><br /> **注意：** 與 sys. 資料庫中的 **state** 資料行相同。|  
|**database_state_desc**|**nvarchar(60)**|可用性複本之 **database_state** 的描述。<br /><br /> ONLINE<br /><br /> RESTORING<br /><br /> RECOVERING<br /><br /> RECOVERY_PENDING<br /><br /> SUSPECT<br /><br /> EMERGENCY<br /><br /> OFFLINE<br /><br /> **注意：** 與 sys. 資料庫中的 **state_desc** 資料行相同。|  
|**is_suspended**|**bit**|資料庫狀態，下列其中一個值：<br /><br /> 0 = 已繼續<br /><br /> 1 = 已暫停|  
|**suspend_reason**|**tinyint**|如果資料庫已暫停，則為已暫停狀態的原因，由下列其中一個值表示：<br /><br /> 0 = 使用者動作<br /><br /> 1 = 暫停協力廠商<br /><br /> 2 = 重做<br /><br /> 3 = 擷取<br /><br /> 4 = 套用<br /><br /> 5 = 重新啟動<br /><br /> 6 = 恢復<br /><br /> 7 = 重新驗證<br /><br /> 8 = 計算次要複本同步處理點時發生錯誤|  
|**suspend_reason_desc**|**nvarchar(60)**|資料庫暫停狀態原因的描述，由下列其中一個值表示：<br /><br /> SUSPEND_FROM_USER = 使用者手動暫停資料移動<br /><br /> SUSPEND_FROM_PARTNER = 資料庫複本在強制容錯移轉之後暫停<br /><br /> SUSPEND_FROM_REDO = 取消復原階段發生錯誤<br /><br /> SUSPEND_FROM_APPLY = 將記錄寫入檔案時發生錯誤 (請查看錯誤記錄檔)<br /><br /> SUSPEND_FROM_CAPTURE = 在主要複本上擷取記錄時發生錯誤<br /><br /> SUSPEND_FROM_RESTART = 資料庫複本在資料庫重新啟動之前已暫停 (請查看錯誤記錄檔)<br /><br /> SUSPEND_FROM_UNDO = 復原階段發生錯誤 (請查看錯誤記錄檔)<br /><br /> SUSPEND_FROM_REVALIDATION = 重新連接時偵測到記錄變更不一致 (請查看錯誤記錄檔)<br /><br /> SUSPEND_FROM_XRF_UPDATE = 找不到通用記錄點 (請查看錯誤記錄檔)|  
|**recovery_lsn**|**numeric(25,0)**|在主要複本上，主要資料庫在復原或容錯移轉後、寫入任何新記錄檔記錄前，交易記錄的結尾。 對於給定的次要資料庫而言，如果這個值小於目前強行寫入的 LSN (last_hardened_lsn)，recovery_lsn 是這個次要資料庫需要同步處理 (亦即還原及重新初始化) 的值。 如果這個值大於或等於目前強行寫入的 LSN，則不需要且不會發生重新同步處理。<br /><br /> **recovery_lsn** 反映以零填補的記錄檔區塊識別碼。 這不是實際的記錄序號 (LSN)。 如需有關如何衍生此值的詳細資訊，請參閱本主題稍後的 [瞭解 LSN 資料行值](#LSNcolumns)。|  
|**truncation_lsn**|**numeric(25,0)**|在主要複本上，若為主要資料庫，則反映所有對應次要資料庫之間的最小記錄截斷 LSN。 如果已封鎖本機記錄截斷 (例如由備份作業封鎖)，則此 LSN 可能會高於本機截斷 LSN。<br /><br /> 若為給定的次要資料庫，則反映該資料庫的截斷點。<br /><br /> **truncation_lsn** 反映以零填補的記錄檔區塊識別碼。 這不是實際的記錄序號。|  
|**last_sent_lsn**|**numeric(25,0)**|記錄檔區塊識別碼，指示主要複本已傳送所有記錄檔區塊到哪一點。 這是將傳送之下一個記錄檔區塊的識別碼，而不是最近傳送之記錄檔區塊的識別碼。<br /><br /> **last_sent_lsn** 反映以零填補的記錄檔區塊識別碼，不是實際的記錄序號。|  
|**last_sent_time**|**datetime**|上次傳送記錄檔區塊的時間。|  
|**last_received_lsn**|**numeric(25,0)**|記錄檔區塊識別碼，可識別裝載此次要資料庫的次要複本已經接收所有記錄檔區塊到哪一點。<br /><br /> **last_received_lsn** 反映以零填補的記錄檔區塊識別碼。 這不是實際的記錄序號。|  
|**last_received_time**|**datetime**|取得在次要複本上讀取上一個接收訊息內之記錄檔區塊識別碼的時間。|  
|**last_hardened_lsn**|**numeric(25,0)**|記錄檔區塊的開頭，其中包含次要資料庫之上次強行寫入 LSN 的記錄檔記錄。<br /><br /> 在非同步認可主要資料庫或目前原則為「延遲」的同步認可資料庫上，此值是 NULL。 若為其他同步認可主資料庫， **last_hardened_lsn** 表示所有次要資料庫之間的強化 lsn 的最小值。<br /><br /> **注意： last_hardened_lsn** 反映以零填補的記錄檔區塊識別碼。 這不是實際的記錄序號。 如需詳細資訊，請參閱本主題稍後的 [瞭解 LSN 資料行值](#LSNcolumns)。|  
|**last_hardened_time**|**datetime**|在次要資料庫上，上次強行寫入 LSN 之記錄檔區塊識別碼的時間 (**last_hardened_lsn**) 。 在主要資料庫上，則反映相對於最小強行寫入 LSN 的時間。|  
|**last_redone_lsn**|**numeric(25,0)**|在次要資料庫上重做之最後一個記錄檔記錄的實際記錄序號。 **last_redone_lsn** 一律小於 **last_hardened_lsn**。|  
|**last_redone_time**|**datetime**|在次要資料庫上重做上一個記錄檔記錄的時間。|  
|**log_send_queue_size**|**bigint**|尚未傳送至次要資料庫的主要資料庫記錄檔記錄數量 (以 KB 為單位)。|  
|**log_send_rate**|**bigint**|主要複本實例在上一個作用期間（以 kb 為單位）傳送資料的平均速率，以 (kb 為單位) 。|  
|**redo_queue_size**|**bigint**|次要複本記錄檔中尚未重做的記錄檔記錄數量 (以 KB 為單位)。|  
|**redo_rate**|**bigint**|在給定次要資料庫上重做記錄檔記錄的平均速率，以 kb (KB) 。|  
|**filestream_send_rate**|**bigint**|FILESTREAM 檔案傳送到次要複本所使用的速率 (以每秒鐘的 KB 數為單位)。|  
|**end_of_log_lsn**|**numeric(25,0)**|本機記錄檔結束 LSN。 實際 LSN 會對應到主要和次要資料庫上記錄檔快取中的上一個記錄檔記錄。 在主要複本上，次要資料列會根據次要複本傳送至主要複本的最新進度訊息反映記錄檔結束 LSN。<br /><br /> **end_of_log_lsn** 反映以零填補的記錄檔區塊識別碼。 這不是實際的記錄序號。 如需詳細資訊，請參閱本主題稍後的 [瞭解 LSN 資料行值](#LSNcolumns)。|  
|**last_commit_lsn**|**數值 (25，0)**|實際記錄序號，對應到交易記錄中的上一個認可記錄。<br /><br /> 在主要資料庫上，這會對應到上一次處理的認可記錄。 次要資料庫的資料列會顯示次要複本傳送至主要複本的記錄序號。<br /><br /> 在次要複本上，這是上一次重做的認可記錄。|  
|**last_commit_time**|**datetime**|對應到上一個認可記錄的時間。<br /><br /> 在次要資料庫上，此時間與主要資料庫上的時間相同。<br /><br /> 在主要複本上，每一個次要資料庫資料列都會顯示裝載該次要資料庫的次要複本回報給主要複本的時間。 主資料庫資料列與給定的次要資料庫資料列之間的時間差異，大約代表復原點目標 (RPO) ，假設重做進程已被攔截，且進度已由次要複本回報給主要複本。|  
|**low_water_mark_for_ghosts**|**bigint**|資料庫的一個單純遞增的數字，表示主要資料庫上的準刪除清除所使用的下限標準。 如果這個數字不會隨著時間而遞增，則表示可能不會進行準刪除清除作業。 為了決定所要清除的準刪除資料列，主要複本會針對所有可用性複本 (包括主要複本) 中的這個資料庫，使用這個資料行的最小值。|  
|**secondary_lag_seconds**|**bigint**|在同步處理期間，次要複本在主要複本後方的秒數。<br /><br />**適用於：** [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 及更新版本。|  
  
##  <a name="understanding-the-lsn-column-values"></a><a name="LSNcolumns"></a> 瞭解 LSN 資料行值  
 **End_of_log_lsn**、 **last_hardened_lsn**、 **last_received_lsn**、 **last_sent_lsn**、 **recovery_lsn** 和 **truncation_lsn** 資料行的值不是實際的記錄序號 (lsn) 。 每一個值都會反映填滿零的記錄檔區塊識別碼。  
  
 **end_of_log_lsn**、 **last_hardened_lsn** 和 **recovery_lsn** 都是 flush lsn。 例如， **last_hardened_lsn** 表示已在磁片上的區塊之後的下一個區塊開始。  因此 < **last_hardened_lsn** 值的任何 LSN 都會在磁片上。  不會清除 >= 的 LSN。  
  
 在 **sys.dm_hadr_database_replica_states** 所傳回的 lsn 值中，只有 **last_redone_lsn** 是真正的 LSN。  
  
## <a name="security"></a>安全性  
  
### <a name="permissions"></a>權限  
 需要伺服器的 VIEW SERVER STATE 權限。  
  
## <a name="see-also"></a>另請參閱  
 [AlwaysOn 可用性群組 &#40;SQL Server&#41;](../../database-engine/availability-groups/windows/always-on-availability-groups-sql-server.md)   
 [監視可用性群組 &#40;Transact-SQL&#41;](../../database-engine/availability-groups/windows/monitor-availability-groups-transact-sql.md)  
  
  
