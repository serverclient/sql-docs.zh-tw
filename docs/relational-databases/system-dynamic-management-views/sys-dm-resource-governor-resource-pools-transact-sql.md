---
description: sys.dm_resource_governor_resource_pools (Transact-SQL)
title: sys.dm_resource_governor_resource_pools (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 04/24/2018
ms.prod: sql
ms.prod_service: database-engine, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.dm_resource_governor_resource_pools_TSQL
- dm_resource_governor_resource_pools_TSQL
- sys.dm_resource_governor_resource_pools
- dm_resource_governor_resource_pools
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_resource_governor_resource_pools dynamic management view
ms.assetid: 9bfc926e-d8bc-40f8-9229-ab1f8a1e69c5
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 2ac359e1b73d963e7c2d7f880be7443118f8bd2c
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98101448"
---
# <a name="sysdm_resource_governor_resource_pools-transact-sql"></a>sys.dm_resource_governor_resource_pools (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  傳回目前資源集區狀態的相關資訊、資源集區的目前組態和資源集區統計資料。  
  
> [!NOTE]  
>  若要從或呼叫這個 [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] ，請使用 **sys.dm_pdw_nodes_resource_governor_resource_pools** 名稱。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|pool_id|**int**|資源集區的識別碼。 不可為 Null。|  
|NAME|**sysname**|資源集區的名稱。 不可為 Null。|  
|statistics_start_time|**datetime**|重設此集區統計資料時的時間。 不可為 Null。|  
|total_cpu_usage_ms|**bigint**|重設資源管理員統計資料之後的累計 CPU 使用量 (以毫秒為單位)。 不可為 Null。|  
|cache_memory_kb|**bigint**|目前的快取記憶體總使用量 (以 KB 為單位)。 不可為 Null。|  
|compile_memory_kb|**bigint**|目前奪取的記憶體總使用量 (以 KB 為單位)。 這個使用量大部分用於編譯和最佳化，但是也可以包含其他記憶體使用者。 不可為 Null。|  
|used_memgrant_kb|**bigint**|記憶體授與的目前已使用 (奪取) 記憶體總量。 不可為 Null。|  
|total_memgrant_count|**bigint**|在此資源集區中的累計記憶體授與數量。 不可為 Null。|  
|total_memgrant_timeout_count|**bigint**|在此資源集區中的累計記憶體授與逾時數量。 不可為 Null。|  
|active_memgrant_count|**int**|記憶體授與的目前計數。 不可為 Null。|  
|active_memgrant_kb|**bigint**|目前記憶體授與的總和 (以 KB 為單位)。 不可為 Null。|  
|memgrant_waiter_count|**int**|目前在記憶體授與暫止的查詢計數。 不可為 Null。|  
|max_memory_kb|**bigint**|資源集區可以擁有的最大記憶體數量 (以 KB 為單位)。 這是以目前的設定與伺服器狀態為基礎。 不可為 Null。|  
|used_memory_kb|**bigint**|資源集區所使用的記憶體數量 (以 KB 為單位)。 不可為 Null。|  
|target_memory_kb|**bigint**|資源集區嘗試佔用的目標記憶體數量 (以 KB 為單位)。 這是以目前的設定與伺服器狀態為基礎。 不可為 Null。|  
|out_of_memory_count|**bigint**|重設資源管理員統計資料之後，集區中失敗的記憶體配置數目。 不可為 Null。|  
|min_cpu_percent|**int**|當發生 CPU 競爭時，資源集區中所有要求之保證平均 CPU 頻寬的目前組態。 不可為 Null。|  
|max_cpu_percent|**int**|當 CPU 出現競爭時，資源集區中所有要求允許之最大平均 CPU 頻寬的目前組態。 不可為 Null。|  
|min_memory_percent|**int**|當記憶體出現競爭時，資源集區中所有要求之保證記憶體數量的目前組態。 這不會與其他資源集區共用。 不可為 Null。|  
|max_memory_percent|**int**|在此資源集區中，可供要求所用之伺服器記憶體總量百分比的目前組態。 不可為 Null。|  
|cap_cpu_percent|**int**|**適用對象**：[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 及更新版本。<br /><br /> 資源集區中所有要求都將接收的 CPU 頻寬硬體上限。 將最大 CPU 頻寬層級限制為指定的層級。 允許的值範圍從 1 至 100。 不可為 Null。|  
|min_iops_per_volume|**int**|**適用對象**：[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 及更新版本。<br /><br /> 這個集區之每個磁碟區設定的每秒 IO (IOPS) 最小值。 可為 Null。 如果資源集區不受 IO 管制，則為 NULL。 也就是說，資源集區的 MIN_IOPS_PER_VOLUME 和 MAX_IOPS_PER_VOLUME 設定為 0。|  
|max_iops_per_volume|**int**|**適用對象**：[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 及更新版本。<br /><br /> 這個集區之每個磁碟區設定的每秒 IO (IOPS) 最大值。 可為 Null。 如果資源集區不受 IO 管制，則為 NULL。 也就是說，資源集區的 MIN_IOPS_PER_VOLUME 和 MAX_IOPS_PER_VOLUME 設定為 0。|  
|read_io_queued_total|**int**|**適用對象**：[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 及更新版本。<br /><br /> 重設資源管理員之後加入佇列的讀取 IO 總數。 可為 Null。 如果資源集區不受 IO 管制，則為 NULL。 也就是說，資源集區的 MIN_IOPS_PER_VOLUME 和 MAX_IOPS_PER_VOLUME 設定為 0。|  
|read_io_issued_total|**int**|**適用對象**：[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 及更新版本。<br /><br /> 重設資源管理員統計資料之後發出的讀取 IO 總數。 可為 Null。 如果資源集區不受 IO 管制，則為 NULL。 也就是說，資源集區的 MIN_IOPS_PER_VOLUME 和 MAX_IOPS_PER_VOLUME 設定為 0。|  
|read_io_completed_total|**int**|**適用對象**：[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 及更新版本。<br /><br /> 重設資源管理員統計資料之後完成的讀取 IO 總數。 不可為 Null。|  
|read_io_throttled_total|**int**|重設資源管理員統計資料之後節流的讀取 IO 總數。 可為 Null。 如果資源集區不受 IO 管制，則為 NULL。 也就是說，資源集區的 MIN_IOPS_PER_VOLUME 和 MAX_IOPS_PER_VOLUME 設定為 0。|  
|read_bytes_total|**bigint**|**適用對象**：[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 及更新版本。<br /><br /> 重設資源管理員統計資料之後讀取的總位元組數。 不可為 Null。|  
|read_io_stall_total_ms|**bigint**|**適用對象**：[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 及更新版本。<br /><br /> 讀取 IO 從抵達到完成的總時間 (以毫秒為單位)。 不可為 Null。 |  
|read_io_stall_queued_ms|**bigint**|**適用對象**：[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 及更新版本。<br /><br /> 讀取 IO 從抵達到發行的總時間 (以毫秒為單位)。 可為 Null。 如果資源集區不受 IO 管制，則為 NULL。 也就是說，資源集區的 MIN_IOPS_PER_VOLUME 和 MAX_IOPS_PER_VOLUME 設定為 0。<br /><br /> 若要判斷集區的 IO 設定是否造成延遲，請從 **read_io_stall_total_ms** 減去 **read_io_stall_queued_ms** 。|  
|write_io_queued_total|**int**|**適用對象**：[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 及更新版本。<br /><br /> 重設資源管理員統計資料之後加入佇列的寫入 IO 總數。 可為 Null。 如果資源集區不受 IO 管制，則為 NULL。 也就是說，資源集區的 MIN_IOPS_PER_VOLUME 和 MAX_IOPS_PER_VOLUME 設定為 0。|  
|write_io_issued_total|**int**|**適用對象**：[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 及更新版本。<br /><br /> 重設資源管理員統計資料之後發出的寫入 IO 總數。 可為 Null。 如果資源集區不受 IO 管制，則為 NULL。 也就是說，資源集區的 MIN_IOPS_PER_VOLUME 和 MAX_IOPS_PER_VOLUME 設定為 0。|  
|write_io_completed_total|**int**|**適用對象**：[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 及更新版本。<br /><br /> 重設資源管理員統計資料之後完成的寫入 IO 總數。 不可為 Null|  
|write_io_throttled_total|**int**|**適用對象**：[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 及更新版本。<br /><br /> 重設資源管理員統計資料之後節流的寫入 IO 總數。 不可為 Null|  
|write_bytes_total|**bigint**|**適用對象**：[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 及更新版本。<br /><br /> 重設資源管理員統計資料之後寫入的總位元組數。 不可為 Null。|  
|write_io_stall_total_ms|**bigint**|**適用對象**：[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 及更新版本。<br /><br /> 寫入 IO 從抵達到完成的總時間 (以毫秒為單位)。 不可為 Null。 |  
|write_io_stall_queued_ms|**bigint**|**適用對象**：[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 及更新版本。<br /><br /> 寫入 IO 從抵達到發行的總時間 (以毫秒為單位)。 可為 Null。 如果資源集區不受 IO 管制，則為 NULL。 也就是說，資源集區的 MIN_IOPS_PER_VOLUME 和 MAX_IOPS_PER_VOLUME 設定為 0。<br /><br /> 這是 IO 資源管理導入的延遲。|  
|io_issue_violations_total|**int**|**適用對象**：[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 及更新版本。<br /><br /> IO 發出違規總數。 也就是說，在 IO 發出率低於保留率時的次數。 可為 Null。 如果資源集區不受 IO 管制，則為 NULL。 也就是說，資源集區的 MIN_IOPS_PER_VOLUME 和 MAX_IOPS_PER_VOLUME 設定為 0。|  
|io_issue_delay_total_ms|**bigint**|**適用對象**：[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 及更新版本。<br /><br /> 排程發出 IO 到實際發出 IO 的總時間 (以毫秒為單位)。 可為 Null。 如果資源集區不受 IO 管制，則為 NULL。 也就是說，資源集區的 MIN_IOPS_PER_VOLUME 和 MAX_IOPS_PER_VOLUME 設定為 0。|  
|pdw_node_id|**int**|**適用** 于： [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 、 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]<br /><br /> 此散發所在之節點的識別碼。|  
  
## <a name="remarks"></a>備註  
 資源管理員工作負載群組和資源管理員資源集區擁有多對一的對應。 因此，許多資源集區統計資料會從工作負載群組統計資料衍生。  
  
 這個動態管理檢視會顯示記憶體中組態。 若要查看儲存的設定中繼資料，請使用 sys.resource_governor_resource_pools 目錄] 視圖。  
  
## <a name="permissions"></a>權限  
 需要 VIEW SERVER STATE 權限。  
  
## <a name="see-also"></a>另請參閱  
 [動態管理檢視與函數 &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)   
 [sys.dm_resource_governor_workload_groups &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-transact-sql.md)   
 [sys.resource_governor_resource_pools &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-resource-governor-resource-pools-transact-sql.md)   
 [ALTER RESOURCE GOVERNOR &#40;Transact-SQL&#41;](../../t-sql/statements/alter-resource-governor-transact-sql.md)  
  
  



