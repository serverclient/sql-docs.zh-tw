---
description: 'sys.dm_exec_function_stats (Transact-sql) '
title: sys.dm_exec_function_stats (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 05/30/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse
ms.reviewer: ''
ms.technology: system-objects
ms.topic: conceptual
f1_keywords:
- sys.dm_exec_function_stats
- sys.dm_exec_function_stats_tsql
- dm_exec_function_stats
- dm_exec_function_stats_tsql
helpviewer_keywords:
- sys.dm_exec_function_stats dynamic management view
ms.assetid: 4c3d6a02-08e4-414b-90be-36b89a0e5a3a
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: =azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 788596936cf6ce9c8d2ca0a618b441fcbd0f7624
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98099788"
---
# <a name="sysdm_exec_function_stats-transact-sql"></a>sys.dm_exec_function_stats (Transact-sql) 
[!INCLUDE [sqlserver2016-asdb-asdbmi-asa](../../includes/applies-to-version/sqlserver2016-asdb-asdbmi-asa.md)]

  傳回快取函數的匯總效能統計資料。 此視圖會針對每個快取的函式計畫傳回一個資料列，而且只要函數維持快取狀態，資料列的存留期就會傳回。 從快取中移除函式時，會從這個視圖中刪除對應的資料列。 此時，就會引發效能統計資料 SQL 追蹤事件 (與 **sys.dm_exec_query_stats** 很相似)。 傳回純量函數的相關資訊，包括記憶體內建函式和 CLR 純量函數。 不會傳回資料表值函數的相關資訊。  
  
 在 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]，動態管理檢視不可以公開可能會影響資料庫內含項目的資訊或公開有關使用者可存取之其他資料庫的資訊。 為了避免公開此資訊，每個包含不屬於已連線租使用者之資料的資料列都會被篩選掉。  
  
> [!NOTE]
> **Sys.dm_exec_function_stats** 的結果可能會隨著每次執行而有所不同，因為資料只會反映完成的查詢，而不是仍在進行中的查詢。 

  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**database_id**|**int**|函數所在的資料庫識別碼。|  
|object_id|**int**|函數的物件識別碼。|  
|**type**|**char(2)**|物件的類型： FN = 純量值函式|  
|**type_desc**|**nvarchar(60)**|物件類型的描述： SQL_SCALAR_FUNCTION|  
|**sql_handle**|**varbinary(64)**|這可以用來與 **sys.dm_exec_query_stats** 中從這個函數內執行的查詢相互關聯。|  
|**plan_handle**|**varbinary(64)**|記憶體中計畫的識別碼。 這個識別碼是暫時性的，只有當計畫留在快取時才會保留。 此值可搭配 **sys.dm_exec_cached_plans** 動態管理檢視使用。<br /><br /> 當原生編譯的函數查詢記憶體優化資料表時，一律會0x000。|  
|**cached_time**|**datetime**|將函數新增至快取的時間。|  
|**last_execution_time**|**datetime**|上次執行函數的時間。|  
|**execution_count**|**bigint**|自上次編譯函式之後，該函式已執行的次數。|  
|**total_worker_time**|**bigint**|這個函式在編譯以來執行所耗用的 CPU 時間總量（以微秒為單位）。<br /><br /> 對於原生編譯的函式，如果有多個執行花費的時間少於1毫秒， **total_worker_time** 可能不正確。|  
|**last_worker_time**|**bigint**|上次執行函數所耗用的 CPU 時間（以微秒為單位）。 <sup>1</sup>|  
|**min_worker_time**|**bigint**|此函數在單次執行期間曾經耗用的最小 CPU 時間（以微秒為單位）。 <sup>1</sup>|  
|**max_worker_time**|**bigint**|此函數在單次執行期間曾耗用的最大 CPU 時間（以微秒為單位）。 <sup>1</sup>|  
|**total_physical_reads**|**bigint**|此函式自編譯以來執行所執行的實體讀取總數。<br /><br /> 查詢記憶體最佳化的資料表時一律為 0。|  
|**last_physical_reads**|**bigint**|上次執行函數時所執行的實體讀取數。<br /><br /> 查詢記憶體最佳化的資料表時一律為 0。|  
|**min_physical_reads**|**bigint**|這個函式在單次執行期間曾執行的最小實體讀取數。<br /><br /> 查詢記憶體最佳化的資料表時一律為 0。|  
|**max_physical_reads**|**bigint**|此函數在單次執行期間曾執行的最大實體讀取數。<br /><br /> 查詢記憶體最佳化的資料表時一律為 0。|  
|**total_logical_writes**|**bigint**|這個函式在編譯以來執行所執行的邏輯寫入總數。<br /><br /> 查詢記憶體最佳化的資料表時一律為 0。|  
|**last_logical_writes**|**bigint**|上次執行計畫時修改的緩衝集區頁數。 如果已修改頁面，則不會計算寫入。<br /><br /> 查詢記憶體最佳化的資料表時一律為 0。|  
|**min_logical_writes**|**bigint**|此函數在單次執行期間曾執行的最小邏輯寫入數。<br /><br /> 查詢記憶體最佳化的資料表時一律為 0。|  
|**max_logical_writes**|**bigint**|此函數在單次執行期間曾執行的最大邏輯寫入數。<br /><br /> 查詢記憶體最佳化的資料表時一律為 0。|  
|**total_logical_reads**|**bigint**|這個函式在編譯以來執行所執行的邏輯讀取總數。<br /><br /> 查詢記憶體最佳化的資料表時一律為 0。|  
|**last_logical_reads**|**bigint**|上次執行函數時所執行的邏輯讀取數。<br /><br /> 查詢記憶體最佳化的資料表時一律為 0。|  
|**min_logical_reads**|**bigint**|此函數在單次執行期間曾執行的最小邏輯讀取數。<br /><br /> 查詢記憶體最佳化的資料表時一律為 0。|  
|**max_logical_reads**|**bigint**|此函數在單次執行期間曾執行的最大邏輯讀取數。<br /><br /> 查詢記憶體最佳化的資料表時一律為 0。|  
|**total_elapsed_time**|**bigint**|此函數完成執行的總經過時間（以微秒為單位）。|  
|**last_elapsed_time**|**bigint**|此函數最近完成執行的經過時間（以微秒為單位）。|  
|**min_elapsed_time**|**bigint**|此函數完成執行的最少經過時間（以微秒為單位）。|  
|**max_elapsed_time**|**bigint**|此函數完成執行的最大經過時間（以微秒為單位）。|  
|**total_page_server_reads**|**bigint**|此函式自編譯以來執行所執行的頁面伺服器讀取總數。<br /><br /> **適用于：** Azure SQL Database 超大規模。|  
|**last_page_server_reads**|**bigint**|上次執行函數時所執行的頁面伺服器讀取數。<br /><br /> **適用于：** Azure SQL Database 超大規模。|  
|**min_page_server_reads**|**bigint**|這個函式在單次執行期間曾執行的最小頁面伺服器讀取數。<br /><br /> **適用于：** Azure SQL Database 超大規模。|  
|**max_page_server_reads**|**bigint**|這個函式在單次執行期間曾執行的最大頁面伺服器讀取數。<br /><br /> **適用于：** Azure SQL Database 超大規模。|
  
## <a name="permissions"></a>權限  

在上 [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] ，需要 `VIEW SERVER STATE` 許可權。   
在 SQL Database Basic、S0 和 S1 服務目標上，以及針對彈性集區中的資料庫，則 `Server admin` `Azure Active Directory admin` 需要或帳戶。 在所有其他 SQL Database 服務目標上， `VIEW DATABASE STATE` 資料庫中都需要有許可權。   
  
## <a name="examples"></a>範例  
 下列範例會傳回平均經過時間所識別的前十個函式的相關資訊。  
  
```  
SELECT TOP 10 d.object_id, d.database_id, OBJECT_NAME(object_id, database_id) 'function name',   
    d.cached_time, d.last_execution_time, d.total_elapsed_time,  
    d.total_elapsed_time/d.execution_count AS [avg_elapsed_time],  
    d.last_elapsed_time, d.execution_count  
FROM sys.dm_exec_function_stats AS d  
ORDER BY [total_worker_time] DESC;  
```  
  
## <a name="see-also"></a>另請參閱  
 [執行相關的動態管理檢視和函數 &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/execution-related-dynamic-management-views-and-functions-transact-sql.md)   
 [sys.dm_exec_sql_text &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-sql-text-transact-sql.md)   
 [sys.dm_exec_query_stats &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql.md)   
 
 [sys.dm_exec_trigger_stats &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-trigger-stats-transact-sql.md)   
 [sys.dm_exec_procedure_stats &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-procedure-stats-transact-sql.md)  
  
  
