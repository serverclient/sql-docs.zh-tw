---
description: sys.dm_exec_query_profiles (Transact-SQL)
title: sys.dm_exec_query_profiles (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 10/25/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- dm_exec_query_profiles_TSQL
- sys.dm_exec_query_profiles_TSQL
- dm_exec_query_profiles
- sys.dm_exec_query_profiles
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_exec_query_profiles dynamic management view
ms.assetid: 54efc6cb-eea8-4f6d-a4d0-aa05eeb54081
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: =azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 80c59436a83795f4111dfae2997b8678b94e617e
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98096591"
---
# <a name="sysdm_exec_query_profiles-transact-sql"></a>sys.dm_exec_query_profiles (Transact-SQL)
[!INCLUDE[sql-asdb-asdbmi](../../includes/applies-to-version/sql-asdb-asdbmi.md)]

監視查詢執行時的即時查詢進度。 例如，使用此 DMV 判斷查詢的哪個部份執行得很慢。 使用描述欄位中識別的資料行可將此 DMV 與其他系統 DMV 聯結。 或者，使用時間戳記資料行，將此 DMV 與其他效能計數器 (例如效能監視器 xperf) 聯結在一起。  
  
## <a name="table-returned"></a>傳回的資料表  
傳回的計數器是以每個執行緒的每個運算子為基礎。 結果是動態的，且不符合現有選項的結果，例如 `SET STATISTICS XML ON` 在查詢完成時只會建立輸出。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|session_id|**smallint**|識別此查詢執行所在的工作階段。 參考 dm_exec_sessions.session_id。|  
|request_id|**int**|識別目標要求。 參考 dm_exec_sessions.request_id。|  
|sql_handle|**varbinary(64)**|這是可唯一識別查詢所屬批次或預存程式的 token。 參考 dm_exec_query_stats.sql_handle。|  
|plan_handle|**varbinary(64)**|這是一種權杖，可唯一識別已執行之批次的查詢執行計畫，而且其計畫位於計畫快取或目前正在執行中。 參考 dm_exec_query_stats. plan_handle。|  
|physical_operator_name|**nvarchar(256)**|實體運算子名稱。|  
|node_id|**int**|識別查詢樹狀結構中的運算子節點。|  
|thread_id|**int**|區分屬於相同查詢運算子節點的執行緒 (針對平行查詢)。|  
|task_address|**varbinary(8)**|識別此執行緒使用的 SQLOS 工作。 參考 dm_os_tasks.task_address。|  
|row_count|**bigint**|目前為止運算子傳回的資料列數目。|  
|rewind_count|**bigint**|目前為止的倒轉數目。|  
|rebind_count|**bigint**|目前為止的重新繫結數目。|  
|end_of_scan_count|**bigint**|目前為止結束的掃描數目。|  
|estimate_row_count|**bigint**|估計的資料列數目。 將 estimated_row_count 與實際的 row_count 比較可能會很實用。|  
|first_active_time|**bigint**|初次呼叫運算子的時間 (以毫秒為單位)。|  
|last_active_time|**bigint**|最後呼叫運算子的時間 (以毫秒為單位)。|  
|open_time|**bigint**|開啟的時間戳記 (以毫秒為單位)。|  
|first_row_time|**bigint**|開啟第一個資料列的時間戳記 (以毫秒為單位)。|  
|last_row_time|**bigint**|開啟最後一個資料列的時間戳記 (以毫秒為單位)。|  
|close_time|**bigint**|關閉的時間戳記 (以毫秒為單位)。|  
|elapsed_time_ms|**bigint**|目標節點作業到目前為止所使用的總經過時間（以毫秒為單位） () 。|  
|cpu_time_ms|**bigint**|目標節點作業到目前為止， (的總 CPU 時間（以毫秒為單位）) 使用。|  
|database_id|**smallint**|包含在上面執行讀取和寫入之物件的資料庫識別碼。|  
|object_id|**int**|正在上面執行讀取和寫入之物件的識別碼。 參考 sys.objects.object_id。|  
|index_id|**int**|資料列集開啟所依據的索引 (如果有的話)。|  
|scan_count|**bigint**|目前為止的資料表/索引掃描數目。|  
|logical_read_count|**bigint**|目前為止的邏輯讀取數目。|  
|physical_read_count|**bigint**|目前為止的實體讀取數目。|  
|read_ahead_count|**bigint**|目前為止的預先讀取數目。|  
|write_page_count|**bigint**|目前為止因為溢出的頁面寫入數目。|  
|lob_logical_read_count|**bigint**|目前為止的 LOB 邏輯讀取數目。|  
|lob_physical_read_count|**bigint**|目前為止的 LOB 實體讀取數目。|  
|lob_read_ahead_count|**bigint**|目前為止的 LOB 預先讀取數目。|  
|segment_read_count|**int**|目前為止的區段預先讀取數目。|  
|segment_skip_count|**int**|目前為止略過的區段數目。| 
|actual_read_row_count|**bigint**|套用剩餘的述詞之前，由運算子讀取的資料列數目。| 
|estimated_read_row_count|**bigint**|**適用于：** 從 [!INCLUDE[ssSQL15_md](../../includes/sssql15-md.md)] SP1 開始。 <br/>在套用剩餘的述詞之前，要由運算子讀取的資料列數目。|  
  
## <a name="general-remarks"></a>一般備註  
 如果查詢計劃節點沒有任何 i/o，則所有 i/o 相關的計數器都會設定為 Null。  
  
 這個 DMV 所報告的 i/o 相關計數器比 `SET STATISTICS IO` 下列兩個方式所報告的計數器更細微：  
  
-   `SET STATISTICS IO` 將所有 i/o 的計數器群組在一起。 使用此 DMV，您將會針對查詢計劃中每個執行資料表 i/o 的節點，取得個別的計數器。  
  
-   如果有平行掃描，此 DMV 會針對掃描上運作的每個平行執行緒報告計數器。
 
從 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] SP1 開始， *標準的查詢執行統計資料分析基礎結構* 會與 *輕量查詢執行統計資料分析基礎結構* 並存存在。 `SET STATISTICS XML ON` 而且 `SET STATISTICS PROFILE ON` 一律使用 *標準的查詢執行統計資料分析基礎結構*。 `sys.dm_exec_query_profiles`若要填入，必須啟用其中一個查詢分析基礎結構。 如需詳細資訊，請參閱[查詢分析基礎結構](../../relational-databases/performance/query-profiling-infrastructure.md)。    

>[!NOTE]
> 正在進行調查的查詢必須在啟用查詢分析基礎結構 **之後** 啟動，而在查詢開始之後啟用它將不會在中產生結果 `sys.dm_exec_query_profiles` 。 如需如何啟用查詢分析基礎結構的詳細資訊，請參閱 [查詢分析基礎結構](../../relational-databases/performance/query-profiling-infrastructure.md)。

## <a name="permissions"></a>權限  
在 [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] 和 AZURE SQL 受控執行個體上，需要 `VIEW DATABASE STATE` 資料庫角色的許可權和成員資格 `db_owner` 。   
在進階層中 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] ，需要 `VIEW DATABASE STATE` 資料庫中的許可權。 在 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] SQL Database Basic、S0 和 S1 服務目標上，以及針對彈性集區中的資料庫，則 `Server admin` `Azure Active Directory admin` 需要或帳戶。 在所有其他 SQL Database 服務目標上， `VIEW DATABASE STATE` 資料庫中都需要有許可權。   
   
## <a name="examples"></a>範例  
 步驟1：登入您想要在其中執行您將流量分析的查詢之會話 `sys.dm_exec_query_profiles` 。 若要設定查詢以進行分析，請使用 `SET STATISTICS PROFILE ON` 。 在此相同工作階段中執行查詢。  
  
```sql  
--Configure query for profiling with sys.dm_exec_query_profiles  
SET STATISTICS PROFILE ON;  
GO  

--Or enable query profiling globally under SQL Server 2016 SP1 or above (not needed in SQL Server 2019)  
DBCC TRACEON (7412, -1);  
GO 
  
--Next, run your query in this session, or in any other session if query profiling has been enabled globally 
```  
  
 步驟2：登入與您的查詢執行所在會話不同的第二個會話。  
  
 下列陳述式摘要目前正在工作階段 54 中執行的查詢進度。 為了達成目的，它會計算每個節點所有執行緒的輸出資料列總數，並且將它和該節點的輸出資料列預估數比較。  
  
```sql  
--Run this in a different session than the session in which your query is running. 
--Note that you may need to change session id 54 below with the session id you want to monitor.
SELECT node_id,physical_operator_name, SUM(row_count) row_count, 
  SUM(estimate_row_count) AS estimate_row_count, 
  CAST(SUM(row_count)*100 AS float)/SUM(estimate_row_count)  
FROM sys.dm_exec_query_profiles   
WHERE session_id=54
GROUP BY node_id,physical_operator_name  
ORDER BY node_id;  
```  
  
## <a name="see-also"></a>另請參閱  
 [動態管理檢視與函數 &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)   
 [執行相關的動態管理檢視和函式 &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/execution-related-dynamic-management-views-and-functions-transact-sql.md)  
 
