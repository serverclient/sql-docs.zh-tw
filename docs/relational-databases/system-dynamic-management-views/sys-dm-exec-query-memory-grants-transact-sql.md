---
description: sys.dm_exec_query_memory_grants (Transact-SQL)
title: sys.dm_exec_query_memory_grants (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 05/19/2020
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- dm_exec_query_memory_grants_TSQL
- sys.dm_exec_query_memory_grants
- sys.dm_exec_query_memory_grants_TSQL
- dm_exec_query_memory_grants
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_exec_query_memory_grants dynamic management view
ms.assetid: 2c417747-2edd-4e0d-8a9c-e5f445985c1a
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: d76ff951e165c7ea2787588891d739b112fefc25
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98095135"
---
# <a name="sysdm_exec_query_memory_grants-transact-sql"></a>sys.dm_exec_query_memory_grants (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  傳回已要求且正在等候記憶體授與或已獲得記憶體授與的所有查詢的相關資訊。 不需要記憶體授與的查詢將不會出現在此視圖中。 例如，排序和雜湊聯結作業具有記憶體授與來執行查詢，而沒有 **ORDER by** 子句的查詢將不會有記憶體授與。  
  
 在 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]，動態管理檢視不可以公開可能會影響資料庫內含項目的資訊或公開有關使用者可存取之其他資料庫的資訊。 為了避免公開此資訊，每個包含不屬於已連線租使用者之資料的資料列都會被篩選掉。此外，資料行 **scheduler_id**、 **wait_order**、 **pool_id** **group_id** 中的值會經過篩選;資料行值設定為 Null。  
  
> [!NOTE]  
> 若要從或呼叫這個 [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] ，請使用 **sys.dm_pdw_nodes_exec_query_memory_grants** 名稱。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**session_id**|**smallint**|執行此查詢的工作階段識別碼 (SPID)。|  
|**request_id**|**int**|要求的識別碼。 在工作階段的內容中是唯一的。|  
|**scheduler_id**|**int**|排程此查詢的排程器識別碼。|  
|**dop**|**smallint**|此查詢之平行處理原則的程度。|  
|**request_time**|**datetime**|此查詢要求記憶體授權的日期和時間。|  
|**grant_time**|**datetime**|授與記憶體給此查詢的日期和時間。 如果尚未授與記憶體，則為 NULL。|  
|**requested_memory_kb**|**bigint**|要求的記憶體總數 (以 KB 為單位)。|  
|**granted_memory_kb**|**bigint**|實際授與的記憶體總數 (以 KB 為單位)。 如果尚未授與記憶體，則可能為 NULL。 就一般情況而言，此值應該與 **requested_memory_kb** 相同。 對於索引建立，除了一開始授與的記憶體之外，伺服器還可以視需求額外授與記憶體。|  
|**required_memory_kb**|**bigint**|執行此查詢所需的記憶體下限 (以 KB 為單位)。 **requested_memory_kb** 等於或大於此數量。|  
|**used_memory_kb**|**bigint**|目前使用的實體記憶體 (以 KB 為單位)。|  
|**max_used_memory_kb**|**bigint**|到目前為止使用的最大實體記憶體 (以 KB 為單位)。|  
|**query_cost**|**float**|估計的查詢成本。|  
|**timeout_sec**|**int**|此查詢放棄記憶體授權要求之前的逾時秒數。|  
|**resource_semaphore_id**|**smallint**|此查詢正在等候之資源信號的非唯一識別碼。<br /><br /> **注意：** 在之前的版本中，此識別碼是唯一的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 。 這項變更可以影響疑難排解的查詢執行。 如需詳細資訊，請參閱本主題後面的＜備註＞一節。|  
|**queue_id**|**smallint**|此查詢等候記憶體授權時所在的等候中佇列識別碼。 如果已經授與記憶體，則為 NULL。|  
|**wait_order**|**int**|在指定的 **queue_id** 內等候中查詢的順序。 如果有其他查詢取得記憶體授與或超時，則指定查詢的這個值可能會變更。如果已經授與記憶體，則為 Null。|  
|**is_next_candidate**|**bit**|下一個記憶體授權的候選。<br /><br /> 1 = 是<br /><br /> 0 = 否<br /><br /> NULL = 已經授與記憶體。|  
|**wait_time_ms**|**bigint**|等候時間 (以毫秒為單位)。 如果已經授與記憶體，則為 NULL。|  
|**plan_handle**|**varbinary(64)**|此查詢計畫的識別碼。 使用 **sys.dm_exec_query_plan** 來擷取實際的 XML 計畫。|  
|**sql_handle**|**varbinary(64)**|此查詢的 [!INCLUDE[tsql](../../includes/tsql-md.md)] 文字識別碼。 使用 **sys.dm_exec_sql_text** 取得實際的 [!INCLUDE[tsql](../../includes/tsql-md.md)] 文字。|  
|**group_id**|**int**|這個查詢執行所在工作負載群組的識別碼。|  
|**pool_id**|**int**|這個工作負載群組所屬資源集區的識別碼。|  
|**is_small**|**tinyint**|設定為 1 時，表示此授與使用小型資源信號。 設定為 0 時，表示使用一般信號。|  
|**ideal_memory_kb**|**bigint**|授與的記憶體大小 (以 KB 為單位)，可將所有東西配置到實體記憶體。 這是以基數估計值為基礎。|  
|**pdw_node_id**|**int**|此散發所在之節點的識別碼。<br /><br /> **適用** 于： [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 、 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] |  
|**reserved_worker_count**|**bigint**|保留的背景 [工作執行緒](../../relational-databases/thread-and-task-architecture-guide.md#sql-server-task-scheduling)數目。<br /><br />**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] |  
|**used_worker_count**|**bigint**|目前使用的背景 [工作執行緒](../../relational-databases/thread-and-task-architecture-guide.md#sql-server-task-scheduling) 數目。<br /><br />**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]|  
|**max_used_worker_count**|**bigint**|目前為止使用的背景 [工作執行緒](../../relational-databases/thread-and-task-architecture-guide.md#sql-server-task-scheduling) 數目上限。<br /><br />**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]|  
|**reserved_node_bitmap**|**bigint**|保留背景 [工作執行緒](../../relational-databases/thread-and-task-architecture-guide.md#sql-server-task-scheduling) 之 NUMA 節點的點陣圖。<br /><br />**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]|  
  
## <a name="permissions"></a>權限  

在上 [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] ，需要 `VIEW SERVER STATE` 許可權。   
在 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 上，需要資料庫中的 `VIEW DATABASE STATE` 權限。   
   
## <a name="remarks"></a>備註  
 查詢逾時的一般偵錯狀況可能如下所示：  
  
-   使用 [sys.dm_os_memory_clerks](../../relational-databases/system-dynamic-management-views/sys-dm-os-memory-clerks-transact-sql.md)、[sys.dm_os_sys_info](../../relational-databases/system-dynamic-management-views/sys-dm-os-sys-info-transact-sql.md) 以及各種效能計數器檢查整個系統記憶體狀態。  
  
-   在 `type = 'MEMORYCLERK_SQLQERESERVATIONS'` 所在的 **sys.dm_os_memory_clerks** 中檢查執行查詢的記憶體保留。  
  
-   使用 **sys.dm_exec_query_memory_grants** 檢查等候 <sup>1</sup>的查詢是否已獲得授權。  
  
    ```sql  
    --Find all queries waiting in the memory queue  
    SELECT * FROM sys.dm_exec_query_memory_grants where grant_time is null  
    ```  
    
    <sup>1</sup> 在此情況下，等候類型通常是 RESOURCE_SEMAPHORE。 如需詳細資訊，請參閱 [sys.dm_os_wait_stats &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql.md)。 
  
-   使用[sys.dm_exec_cached_plans &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-cached-plans-transact-sql.md)和[sys.dm_exec_query_plan &#40;transact-sql](../../relational-databases/system-dynamic-management-views/sys-dm-exec-query-plan-transact-sql.md)的記憶體授與的查詢搜尋快取&#41;  
  
    ```sql  
    -- retrieve every query plan from the plan cache  
    USE master;  
    GO  
    SELECT * FROM sys.dm_exec_cached_plans cp CROSS APPLY sys.dm_exec_query_plan(cp.plan_handle);  
    GO  
    ```  
  
-   使用 [sys.dm_exec_requests](../../relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql.md) 進一步檢查需要大量記憶體的查詢。  
  
    ```sql  
    --Find top 5 queries by average CPU time  
    SELECT TOP 5 total_worker_time/execution_count AS [Avg CPU Time],  
      plan_handle, query_plan   
    FROM sys.dm_exec_query_stats AS qs  
    CROSS APPLY sys.dm_exec_query_plan(qs.plan_handle)  
    ORDER BY total_worker_time/execution_count DESC;  
    GO  
    ```  
  
-   如果懷疑有失控查詢，請從 [sys.dm_exec_query_plan](../../relational-databases/system-dynamic-management-views/sys-dm-exec-query-plan-transact-sql.md) 檢查執行程序表，並從 [sys.dm_exec_sql_text](../../relational-databases/system-dynamic-management-views/sys-dm-exec-sql-text-transact-sql.md) 檢查批次文字。  
  
 使用包含或匯總之動態管理檢視的查詢， `ORDER BY` 可能會增加記憶體耗用量，進而導致它們正在進行疑難排解的問題。  
  
 資源管理員功能可讓資料庫管理員在資源集區間散發伺服器資源，最多可達 64 個集區。 從開始 [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] ，每個集區的行為都像是小型的獨立伺服器實例，而且需要2個信號。 從 **sys.dm_exec_query_resource_semaphores** 傳回的資料列數目最多可達20倍以上所傳回的資料列 [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] 。  
  
## <a name="see-also"></a>另請參閱  
 [sys.dm_exec_query_resource_semaphores &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-query-resource-semaphores-transact-sql.md)     
 [sys.dm_os_wait_stats &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql.md)     
 [執行相關的動態管理檢視和函數 &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/execution-related-dynamic-management-views-and-functions-transact-sql.md)    
 [執行緒和工作架構指南](../../relational-databases/thread-and-task-architecture-guide.md)   
  
