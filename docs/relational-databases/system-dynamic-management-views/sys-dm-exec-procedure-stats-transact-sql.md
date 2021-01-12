---
description: sys.dm_exec_procedure_stats (Transact-SQL)
title: sys.dm_exec_procedure_stats (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/03/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.dm_exec_procedure_stats_TSQL
- dm_exec_procedure_stats_TSQL
- dm_exec_procedure_stats
- sys.dm_exec_procedure_stats
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_exec_procedure_stats dynamic management view
ms.assetid: ab8ddde8-1cea-4b41-a7e4-697e6ddd785a
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: c1090498ce8e2af5e65cc9509ec9bd7fa84d61cd
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98092900"
---
# <a name="sysdm_exec_procedure_stats-transact-sql"></a>sys.dm_exec_procedure_stats (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  傳回快取預存程序的彙總效能統計資料。 此檢視會針對每個快取預存程序計畫傳回一個資料列，而且資料列的存留期間與預存程序維持快取狀態的時間一樣長。 從快取中移除預存程序時，對應的資料列也會從這個檢視中刪除。 此時，就會引發效能統計資料 SQL 追蹤事件 (與 **sys.dm_exec_query_stats** 很相似)。  
  
 在 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]，動態管理檢視不可以公開可能會影響資料庫內含項目的資訊或公開有關使用者可存取之其他資料庫的資訊。 為了避免公開此資訊，每個包含不屬於已連線租使用者之資料的資料列都會被篩選掉。  
  
> [!NOTE]
> **Sys.dm_exec_procedure_stats** 的結果可能會隨著每次執行而有所不同，因為資料只會反映完成的查詢，而不是仍在進行中的查詢。
> 若要從或呼叫這個 [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] ，請使用 **sys.dm_pdw_nodes_exec_procedure_stats** 名稱。 

  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------| 
|**database_id**|**int**|預存程序所在的資料庫識別碼。|  
|object_id|**int**|預存程序的物件識別碼。|  
|**type**|**char(2)**|物件的類型：<br /><br /> P = SQL 預存程序<br /><br /> PC = 組件 (CLR) 預存程序<br /><br /> X = 擴充預存程序|  
|**type_desc**|**nvarchar(60)**|物件類型的描述：<br /><br /> SQL_STORED_PROCEDURE<br /><br /> CLR_STORED_PROCEDURE<br /><br /> EXTENDED_STORED_PROCEDURE|  
|**sql_handle**|**varbinary(64)**|這可以用來與 **sys.dm_exec_query_stats** 中從這個預存程式內執行的查詢相互關聯。|  
|**plan_handle**|**varbinary(64)**|記憶體中計畫的識別碼。 這個識別碼是暫時性的，只有當計畫留在快取時才會保留。 此值可搭配 **sys.dm_exec_cached_plans** 動態管理檢視使用。<br /><br /> 當原生編譯的預存程序查詢記憶體最佳化的資料表時，一律為 0x000。|  
|**cached_time**|**datetime**|在快取中加入預存程序的時間。|  
|**last_execution_time**|**datetime**|上次執行預存程序的時間。|  
|**execution_count**|**bigint**|自上次編譯預存程式以來，已執行的次數。|  
|**total_worker_time**|**bigint**|這個預存程式在編譯以來執行所耗用的 CPU 時間總量（以微秒為單位）。<br /><br /> 如果原生編譯預存程序執行數次的時間都少於 1 毫秒， **total_worker_time** 可能就不準確。|  
|**last_worker_time**|**bigint**|預存程序上次執行所耗用的 CPU 時間 (以微秒為單位)。 <sup>1</sup>|  
|**min_worker_time**|**bigint**|這個預存程式在單次執行期間曾耗用的最小 CPU 時間（以微秒為單位）。 <sup>1</sup>|  
|**max_worker_time**|**bigint**|這個預存程式在單次執行期間曾耗用的最大 CPU 時間（以微秒為單位）。 <sup>1</sup>|  
|**total_physical_reads**|**bigint**|這個預存程式在編譯以來執行所執行的實體讀取總數。<br /><br /> 查詢記憶體最佳化的資料表時一律為 0。|  
|**last_physical_reads**|**bigint**|上次執行預存程式時所執行的實體讀取數。<br /><br /> 查詢記憶體最佳化的資料表時一律為 0。|  
|**min_physical_reads**|**bigint**|這個預存程式在單次執行期間曾執行的最小實體讀取數。<br /><br /> 查詢記憶體最佳化的資料表時一律為 0。|  
|**max_physical_reads**|**bigint**|這個預存程式在單次執行期間曾執行的最大實體讀取數。<br /><br /> 查詢記憶體最佳化的資料表時一律為 0。|  
|**total_logical_writes**|**bigint**|這個預存程式在編譯以來執行所執行的邏輯寫入總數。<br /><br /> 查詢記憶體最佳化的資料表時一律為 0。|  
|**last_logical_writes**|**bigint**|上次執行計畫時變動總數的緩衝集區頁面數。 如果已修改頁面，則不會計算寫入。<br /><br /> 查詢記憶體最佳化的資料表時一律為 0。|  
|**min_logical_writes**|**bigint**|這個預存程式在單次執行期間曾執行的最小邏輯寫入數。<br /><br /> 查詢記憶體最佳化的資料表時一律為 0。|  
|**max_logical_writes**|**bigint**|這個預存程式在單次執行期間曾執行的最大邏輯寫入數。<br /><br /> 查詢記憶體最佳化的資料表時一律為 0。|  
|**total_logical_reads**|**bigint**|這個預存程式在編譯以來執行所執行的邏輯讀取總數。<br /><br /> 查詢記憶體最佳化的資料表時一律為 0。|  
|**last_logical_reads**|**bigint**|上次執行預存程式時所執行的邏輯讀取數。<br /><br /> 查詢記憶體最佳化的資料表時一律為 0。|  
|**min_logical_reads**|**bigint**|這個預存程式在單次執行期間曾執行的最小邏輯讀取數。<br /><br /> 查詢記憶體最佳化的資料表時一律為 0。|  
|**max_logical_reads**|**bigint**|這個預存程式在單次執行期間曾執行的最大邏輯讀取數。<br /><br /> 查詢記憶體最佳化的資料表時一律為 0。|  
|**total_elapsed_time**|**bigint**|這個預存程式完成執行的總經過時間（以微秒為單位）。|  
|**last_elapsed_time**|**bigint**|這個預存程式最近完成執行的經過時間（以微秒為單位）。|  
|**min_elapsed_time**|**bigint**|這個預存程式已完成執行的最小經過時間（以微秒為單位）。|  
|**max_elapsed_time**|**bigint**|這個預存程式已完成執行的最大經過時間（以微秒為單位）。|  
|**total_spills**|**bigint**|這個預存程式在編譯後的執行溢出的總頁數。<br /><br /> **適用** 于：開頭為 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] CU3|  
|**last_spills**|**bigint**|上次執行預存程式時溢出的頁面數目。<br /><br /> **適用** 于：開頭為 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] CU3|  
|**min_spills**|**bigint**|這個預存程式在單次執行期間已溢出的最小頁面數目。<br /><br /> **適用** 于：開頭為 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] CU3|  
|**max_spills**|**bigint**|這個預存程式在單次執行期間已溢出的最大頁面數目。<br /><br /> **適用** 于：開頭為 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] CU3|  
|**pdw_node_id**|**int**|此散發所在之節點的識別碼。<br /><br />**適用** 于： [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 、 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]|  
|**total_page_server_reads**|**bigint**|這個預存程式在編譯以來執行所執行的頁面伺服器讀取總數。<br /><br /> **適用于**： Azure SQL Database 超大規模|  
|**last_page_server_reads**|**bigint**|上次執行預存程式時所執行的頁面伺服器讀取數。<br /><br /> **適用于**： Azure SQL Database 超大規模|  
|**min_page_server_reads**|**bigint**|這個預存程式在單次執行期間曾執行的最小頁面伺服器讀取數。<br /><br /> **適用于**： Azure SQL Database 超大規模|  
|**max_page_server_reads**|**bigint**|這個預存程式在單次執行期間曾執行的最大頁面伺服器讀取數。<br /><br /> **適用于**： Azure SQL Database 超大規模|  
  
 <sup>1</sup> 針對原生編譯預存程式，在啟用統計資料收集時，會收集背景工作時間（以毫秒為單位）。 若查詢的執行時間少於一毫秒，其值將會是 0。  
  
## <a name="permissions"></a>權限  

在上 [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] ，需要 `VIEW SERVER STATE` 許可權。   
在 SQL Database Basic、S0 和 S1 服務目標上，以及針對彈性集區中的資料庫，則 `Server admin` `Azure Active Directory admin` 需要或帳戶。 在所有其他 SQL Database 服務目標上， `VIEW DATABASE STATE` 資料庫中都需要有許可權。   
   
## <a name="remarks"></a>備註  
 預存程序執行完成時，就會更新檢視中的統計資料。  
  
## <a name="examples"></a>範例  
 下列範例會傳回平均經過時間所識別之前 10 項預存程序的相關資訊。  
  
```sql  
SELECT TOP 10 d.object_id, d.database_id, OBJECT_NAME(object_id, database_id) 'proc name',   
    d.cached_time, d.last_execution_time, d.total_elapsed_time,  
    d.total_elapsed_time/d.execution_count AS [avg_elapsed_time],  
    d.last_elapsed_time, d.execution_count  
FROM sys.dm_exec_procedure_stats AS d  
ORDER BY [total_worker_time] DESC;  
```  
  
## <a name="see-also"></a>另請參閱  
[執行相關的動態管理檢視和函數 &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/execution-related-dynamic-management-views-and-functions-transact-sql.md)   
[sys.dm_exec_sql_text &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-sql-text-transact-sql.md)   
[sys.dm_exec_query_plan &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-query-plan-transact-sql.md)    
[sys.dm_exec_query_stats &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql.md)    
[sys.dm_exec_trigger_stats &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-trigger-stats-transact-sql.md)    
[sys.dm_exec_cached_plans &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-cached-plans-transact-sql.md)    
  
  


