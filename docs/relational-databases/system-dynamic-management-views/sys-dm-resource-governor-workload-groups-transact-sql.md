---
description: sys.dm_resource_governor_workload_groups (Transact-SQL)
title: sys.dm_resource_governor_workload_groups (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/15/2020
ms.prod: sql
ms.prod_service: database-engine, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.dm_resource_governor_workload_groups
- sys.dm_resource_governor_workload_groups_TSQL
- dm_resource_governor_workload_groups
- dm_resource_governor_workload_groups_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_resource_governor_workload_groups dynamic management view
ms.assetid: f63c4914-1272-43ef-b135-fe1aabd953e0
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 2db4a28a9199cc090f18cc1f79d8e755c621c7fd
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98172140"
---
# <a name="sysdm_resource_governor_workload_groups-transact-sql"></a>sys.dm_resource_governor_workload_groups (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  傳回工作負載群組統計資料以及工作負載群組的目前記憶體中組態。 這個檢視可以使用 sys.dm_resource_governor_resource_pools 加入，以取得資源集區名稱。  
  
> [!NOTE]  
>  若要從或呼叫這個 [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] ，請使用 **sys.dm_pdw_nodes_resource_governor_workload_groups** 名稱。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|group_id|**int**|工作負載群組的識別碼。 不可為 Null。|  
|NAME|**sysname**|工作負載群組的名稱。 不可為 Null。|  
|pool_id|**int**|資源集區的識別碼。 不可為 Null。|  
|external_pool_id|**int**|**適用** 于：開頭為 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 。<br /><br /> 外部資源集區的識別碼。 不可為 Null。|  
|statistics_start_time|**datetime**|針對工作負載群組重設之統計資料集合的時間。 不可為 Null。|  
|total_request_count|**bigint**|已在工作負載群組中完成之要求的累計計數。 不可為 Null。|  
|total_queued_request_count|**bigint**|到達 GROUP_MAX_REQUESTS 限制後所佇列之要求的累計計數。 不可為 Null。|  
|active_request_count|**int**|目前的要求計數。 不可為 Null。|  
|queued_request_count|**int**|目前佇列的要求計數。 不可為 Null。|  
|total_cpu_limit_violation_count|**bigint**|超過 CPU 限制之要求的累計計數。 不可為 Null。|  
|total_cpu_usage_ms|**bigint**|此工作負載群組的累計 CPU 使用量 (以毫秒為單位)。 不可為 Null。|  
|max_request_cpu_time_ms|**bigint**|單一要求的最大 CPU 使用量 (以毫秒為單位)。 不可為 Null。<br /><br /> **注意：** 這是測量值，不同于 request_max_cpu_time_sec，這是可設定的設定。 如需詳細資訊，請參閱[超過 CPU 閾值事件類別](../../relational-databases/event-classes/cpu-threshold-exceeded-event-class.md)。|  
|blocked_task_count|**int**|封鎖之工作的目前計數。 不可為 Null。|  
|total_lock_wait_count|**bigint**|發生之鎖定等候的累計計數。 不可為 Null。|  
|total_lock_wait_time_ms|**bigint**|保留鎖定之經過時間的累計總和 (以毫秒為單位)。 不可為 Null。|  
|total_query_optimization_count|**bigint**|查詢最佳化在此工作負載群組中的累計計數。 不可為 Null。|  
|total_suboptimal_plan_generation_count|**bigint**|由於記憶體不足的壓力，在此工作負載群組中產生之次佳計畫的累計計數。 不可為 Null。|  
|total_reduced_memgrant_count|**bigint**|到達最大查詢大小限制之記憶體授與的累計計數。 不可為 Null。|  
|max_request_grant_memory_kb|**bigint**|自重設統計資料以來，單一要求的最大記憶體授與大小 (以 KB 為單位)。 不可為 Null。|  
|active_parallel_thread_count|**bigint**|平行執行緒使用量的目前計數。 不可為 Null。|  
|importance|**sysname**|要求在此工作負載群組中之相對重要性的目前組態值。 重要性是下列其中一項，預設值為 [低]、[中] 或 [高]。<br /><br /> 不可為 Null。|  
|request_max_memory_grant_percent|**int**|單一要求之最大記憶體授與的目前設定 (以百分比為單位)。 不可為 Null。|  
|request_max_cpu_time_sec|**int**|單一要求之最大 CPU 使用限制的目前設定 (以秒為單位)。 不可為 Null。|  
|request_memory_grant_timeout_sec|**int**|單一要求記憶體授與逾時的目前設定 (以秒為單位)。 不可為 Null。|  
|group_max_requests|**int**|並行要求之最大數目的目前設定。 不可為 Null。|  
|max_dop|**int**|已針對工作負載群組設定平行處理原則的最大程度。 預設值為 0 時，使用全域設定。 不可為 Null。| 
|effective_max_dop|**int**|**適用** 于：開頭為 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 。<br /><br />工作負載群組的有效平行處理原則最大程度。 不可為 Null。| 
|total_cpu_usage_preemptive_ms|**bigint**|**適用** 于：開頭為 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 。<br /><br />工作負載群組的先占式模式排程時，所使用的總 CPU 時間（以毫秒為單位）。 不可為 Null。<br /><br />若要執行 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 外部的程式碼 (例如，擴充預存程序和分散式查詢)，執行緒必須在非先佔式排程器的控制之外執行。 若要這麼做，工作者必須切換到先佔式模式。| 
|request_max_memory_grant_percent_numeric|**float**|**適用** 于：開頭為 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 。<br /><br />單一要求之最大記憶體授與的目前設定 (以百分比為單位)。 不可為 Null。| 
|pdw_node_id|**int**|**適用** 于： [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 、 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]<br /><br /> 此散發所在之節點的識別碼。|  
  
## <a name="remarks"></a>備註  
 這個動態管理檢視會顯示記憶體中組態。 若要查看儲存的設定中繼資料，請使用 [sys.resource_governor_workload_groups &#40;transact-sql&#41;](../../relational-databases/system-catalog-views/sys-resource-governor-workload-groups-transact-sql.md) 類別目錄檢視。  
  
 當 `ALTER RESOURCE GOVERNOR RESET STATISTICS` 成功執行時，會重設下列計數器： `statistics_start_time` 、 `total_request_count` 、 `total_queued_request_count` 、 `total_cpu_limit_violation_count` 、 `total_cpu_usage_ms` 、 `max_request_cpu_time_ms` 、 `total_lock_wait_count` `total_lock_wait_time_ms` `total_query_optimization_count` `total_suboptimal_plan_generation_count` `total_reduced_memgrant_count` `max_request_grant_memory_kb` 、、、、和。 計數器 `statistics_start_time` 會設定為目前的系統日期和時間，而其他計數器會設定為零 (0) 。  
  
## <a name="permissions"></a>權限  
 需要 `VIEW SERVER STATE` 權限。  
  
## <a name="see-also"></a>另請參閱  
 [動態管理檢視與函數 &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)   
 [sys.dm_resource_governor_resource_pools &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-transact-sql.md)   
 [sys.resource_governor_workload_groups &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-resource-governor-workload-groups-transact-sql.md)   
 [ALTER RESOURCE GOVERNOR &#40;Transact-SQL&#41;](../../t-sql/statements/alter-resource-governor-transact-sql.md)  
  
