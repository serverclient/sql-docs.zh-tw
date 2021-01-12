---
description: 'sys.query_store_query (Transact-sql) '
title: sys.query_store_query (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 01/23/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- QUERY_STORE_QUERY
- SYS.QUERY_STORE_QUERY_TSQL
- SYS.QUERY_STORE_QUERY
- QUERY_STORE_QUERY_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- query_store_query catalog view
- sys.query_store_query catalog view
ms.assetid: bdee149e-7556-4fc3-8242-925dd4b7b6ac
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: =azuresqldb-current||>=sql-server-2016||= azure-sqldw-latest||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: e767ee7ceae909637e7675d536cbac7e47715ccd
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98095454"
---
# <a name="sysquery_store_query-transact-sql"></a>sys.query_store_query (Transact-sql) 
[!INCLUDE [sqlserver2016-asdb-asdbmi-asa](../../includes/applies-to-version/sqlserver2016-asdb-asdbmi-asa.md)]

  包含查詢的相關資訊，以及其相關的整體匯總執行時間執行統計資料。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**query_id**|**bigint**|主索引鍵。|  
|**query_text_id**|**bigint**|外鍵。 [Sys.query_store_query_text &#40;transact-sql&#41;](../../relational-databases/system-catalog-views/sys-query-store-query-text-transact-sql.md)的聯結|  
|**coNtext_settings_id**|**bigint**|外鍵。 [Sys.query_coNtext_settings &#40;transact-sql&#41;](../../relational-databases/system-catalog-views/sys-query-context-settings-transact-sql.md)的聯結。<br/>**注意：** Azure Synapse Analytics 一律會傳回零 (0) 。|  
|object_id|**bigint**|查詢屬於 (預存程式、觸發程式、CLR UDF/UDAgg 等 ) 之資料庫物件的識別碼。 0：如果查詢未以資料庫物件的一部分執行， (臨機操作查詢) 。<br/>**注意：** Azure Synapse Analytics 一律會傳回零 (0) 。|  
|**batch_sql_handle**|**varbinary(64)**|查詢所屬的語句批次識別碼。 只有在查詢參考臨時表或資料表變數時才會填入。<br/>**注意：** Azure Synapse Analytics 一律會傳回 *Null*。|  
|**query_hash**|**二元 (8)**|根據邏輯查詢樹狀結構之個別查詢的 MD5 雜湊。 包含優化工具提示。|  
|**is_internal_query**|**bit**|查詢是在內部產生的。<br/>**注意：** Azure Synapse Analytics 一律會傳回零 (0) 。|  
|**query_parameterization_type**|**tinyint**|參數化的種類：<br /><br /> 0 - 無<br /><br /> 1-使用者<br /><br /> 2-簡單<br /><br /> 3-強制<br/>**注意：** Azure Synapse Analytics 一律會傳回零 (0) 。|  
|**query_parameterization_type_desc**|**nvarchar(60)**|參數型別的文字描述。<br/>**注意：** Azure Synapse Analytics 一律會傳回 *None*。|  
|**initial_compile_start_time**|**datetimeoffset**|編譯開始時間。|  
|**last_compile_start_time**|**datetimeoffset**|編譯開始時間。|  
|**last_execution_time**|**datetimeoffset**|上次執行時間指的是查詢/計畫的最後結束時間。|  
|**last_compile_batch_sql_handle**|**varbinary(64)**|上次使用查詢之最後一個 SQL 批次的控制碼。 您可以將它提供為 [sys.dm_exec_sql_text &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-sql-text-transact-sql.md) 的輸入，以取得批次的完整文字。<br/>**注意：** Azure Synapse Analytics 一律會傳回 *Null*。|  
|**last_compile_batch_offset_start**|**bigint**|可提供給 sys.dm_exec_sql_text 以及 last_compile_batch_sql_handle 的資訊。<br/>**注意：** Azure Synapse Analytics 一律會傳回零 (0) 。|
|**last_compile_batch_offset_end**|**bigint**|可提供給 sys.dm_exec_sql_text 以及 last_compile_batch_sql_handle 的資訊。<br/>**注意：** Azure Synapse Analytics 一律會傳回零 (0) 。|  
|**count_compiles**|**bigint**|編譯統計資料。<br/>**注意：** Azure Synapse Analytics 一律會傳回一個 (1) 。|  
|**avg_compile_duration**|**float**|編譯統計資料（以微秒為單位）。|  
|**last_compile_duration**|**bigint**|編譯統計資料（以微秒為單位）。|  
|**avg_bind_duration**|**float**|系結統計資料（以微秒為單位）。<br/>**注意：** Azure Synapse Analytics 一律會傳回零 (0) 。|  
|**last_bind_duration**|**bigint**|系結統計資料。<br/>**注意：** Azure Synapse Analytics 一律會傳回零 (0) 。|  
|**avg_bind_cpu_time**|**float**|系結統計資料。<br/>**注意：** Azure Synapse Analytics 一律會傳回零 (0) 。|  
|**last_bind_cpu_time**|**bigint**|系結統計資料。<br/>**注意：** Azure Synapse Analytics 一律會傳回零 (0) 。|  
|**avg_optimize_duration**|**float**|優化統計資料（以微秒為單位）。<br/>**注意：** Azure Synapse Analytics 一律會傳回零 (0) 。|
|**last_optimize_duration**|**bigint**|優化統計資料。<br/>**注意：** Azure Synapse Analytics 一律會傳回零 (0) 。|
|**avg_optimize_cpu_time**|**float**|優化統計資料（以微秒為單位）。<br/>**注意：** Azure Synapse Analytics 一律會傳回零 (0) 。|
|**last_optimize_cpu_time**|**bigint**|優化統計資料。<br/>**注意：** Azure Synapse Analytics 一律會傳回零 (0) 。|
|**avg_compile_memory_kb**|**float**|編譯記憶體統計資料。<br/>**注意：** Azure Synapse Analytics 一律會傳回零 (0) 。|
|**last_compile_memory_kb**|**bigint**|編譯記憶體統計資料。<br/>**注意：** Azure Synapse Analytics 一律會傳回零 (0) 。|
|**max_compile_memory_kb**|**bigint**|編譯記憶體統計資料。<br/>**注意：** Azure Synapse Analytics 一律會傳回零 (0) 。|
|**is_clouddb_internal_query**|**bit**|在內部部署中一律為 0 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。<br/>**注意：** Azure Synapse Analytics 一律會傳回零 (0) 。|
  
## <a name="permissions"></a>權限  
 需要 **VIEW DATABASE STATE** 許可權。  
  
## <a name="see-also"></a>另請參閱  
 [sys.database_query_store_options &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-database-query-store-options-transact-sql.md)   
 [sys.query_coNtext_settings &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-query-context-settings-transact-sql.md)   
 [sys.query_store_plan &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-query-store-plan-transact-sql.md)   
 [sys.query_store_query_text &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-query-store-query-text-transact-sql.md)   
 [sys.query_store_wait_stats &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql.md)  
 [sys.query_store_runtime_stats &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql.md)   
 [sys.query_store_runtime_stats_interval &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-query-store-runtime-stats-interval-transact-sql.md)   
 [相關檢視、函數與程序](../../relational-databases/performance/monitoring-performance-by-using-the-query-store.md)   
 [目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/catalog-views-transact-sql.md)   
 [查詢存放區預存程序 &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/query-store-stored-procedures-transact-sql.md)   
 [sys.fn_stmt_sql_handle_from_sql_stmt &#40;Transact-SQL&#41;](../../relational-databases/system-functions/sys-fn-stmt-sql-handle-from-sql-stmt-transact-sql.md)  
  
  
