---
description: sys.dm_hadr_database_replica_cluster_states (Transact-SQL)
title: sys.dm_hadr_database_replica_cluster_states (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.dm_hadr_database_replica_cluster_states
- dm_hadr_database_replica_cluster_states_TSQL
- sys.dm_hadr_database_replica_cluster_states_TSQL
- dm_hadr_database_replica_cluster_states
dev_langs:
- TSQL
helpviewer_keywords:
- Availability Groups [SQL Server], monitoring
- Availability Groups [SQL Server], WSFC clusters
- sys.dm_hadr_database_replica_cluster_states dynamic management view
ms.assetid: 6f719071-ebce-470d-aebd-1f55ee8cd70a
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: f97172a1f417b237a8e1ee4d16fc573cd94f571d
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98097660"
---
# <a name="sysdm_hadr_database_replica_cluster_states-transact-sql"></a>sys.dm_hadr_database_replica_cluster_states (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  傳回包含資訊的資料列，該資訊的目的是為了提供您 Windows Server 容錯移轉叢集 (WSFC) 叢集中每個 Always On 可用性群組內 AlwaysOn 可用性群組的可用性資料庫健康情況見解。 查詢 **sys.dm_hadr_database_replica_states** 來回答下列問題：  
  
-   可用性群組中的所有資料庫都已準備好可進行容錯移轉嗎？  
  
-   強制容錯移轉之後，次要資料庫是否已在本機暫停它自己，並將其暫停狀態認可到新的主要複本？  
  
-   如果主要複本目前無法使用，哪一個次要複本會在成為主要複本時允許最少的資料遺失？  
  
-   當 [sys.databases](~/relational-databases/system-catalog-views/sys-databases-transact-sql.md)   **log_reuse_wait_desc** 資料行的值為 "AVAILABILITY_REPLICA" 時，可用性群組中的次要複本會在指定的主資料庫上保留記錄截斷？  
   
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**replica_id**|**uniqueidentifier**|可用性群組中可用性複本的識別碼。|  
|**group_database_id**|**uniqueidentifier**|可用性群組中資料庫的識別碼。 這個識別碼在此資料庫聯結的每個複本上都相同。|  
|**database_name**|**sysname**|屬於可用性群組的資料庫名稱。|  
|**is_failover_ready**|**bit**|指出次要資料庫是否與對應的主要資料庫同步處理。 下列其中一個值：<br /><br /> 0 = 資料庫不會標示為已在叢集中同步處理。 資料庫尚未做好容錯移轉的準備。<br /><br /> 1 = 資料庫標示為已在叢集中同步處理。 資料庫已做好容錯移轉的準備。|  
|**is_pending_secondary_suspend**|**bit**|指出在強制容錯移轉之後，資料庫是否會暫止暫停，可為下列其中一個值：<br /><br /> 0 = HADR_SYNCHRONIZED_ SUSPENDED 除外的任何狀態。<br /><br /> 1 = HADR_SYNCHRONIZED_ SUSPENDED。 當強制容錯移轉完成時，每一個次要資料庫都會設定為 HADR_SYNCHONIZED_SUSPENDED 並持續保留在這個狀態中，直到新的主要複本接收到從該次要資料庫到 SUSPEND 訊息的認可為止。<br /><br /> NULL = 未知 (無仲裁)|  
|**is_database_joined**|**bit**|指出此可用性複本上的資料庫是否已聯結可用性群組，可為下列其中一個值：<br /><br /> 0 = 資料庫尚未聯結此可用性複本上的可用性群組。<br /><br /> 1 = 資料庫已聯結此可用性複本上的可用性群組。<br /><br /> NULL = 未知 (可用性複本缺少仲裁)。|  
|**recovery_lsn**|**numeric(25,0)**|在主要複本上，此複本在復原或容錯移轉後、寫入任何新記錄檔記錄前，交易記錄的結尾。 在主要複本上，給定次要資料庫的資料列將會擁有主要複本需要將次要複本同步成為 (也就是還原及重新初始化) 的值。<br /><br /> 在次要複本上，這個值為 NULL。 請注意，每一個次要複本都會擁有主要複本已告知次要複本要還原成的最大值或較低值。|  
|**truncation_lsn**|**numeric(25,0)**|[!INCLUDE[ssHADR](../../includes/sshadr-md.md)] 記錄截斷值，如果已封鎖本機記錄截斷 (例如由備份作業封鎖)，則此值可能會高於本機截斷 LSN。|  
  
## <a name="security"></a>安全性  
  
### <a name="permissions"></a>權限  
 需要伺服器的 VIEW SERVER STATE 權限。  
  
## <a name="see-also"></a>另請參閱  
 [Always On 可用性群組動態管理檢視和函數 &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/always-on-availability-groups-dynamic-management-views-functions.md)   
 [AlwaysOn 可用性群組目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/always-on-availability-groups-catalog-views-transact-sql.md)   
 [監視可用性群組 &#40;Transact-sql&#41;](../../database-engine/availability-groups/windows/monitor-availability-groups-transact-sql.md)   
 [AlwaysOn 可用性群組 &#40;SQL Server&#41;](../../database-engine/availability-groups/windows/always-on-availability-groups-sql-server.md)   
 [sys.dm_hadr_database_replica_states &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-hadr-database-replica-states-transact-sql.md)  
  
  
