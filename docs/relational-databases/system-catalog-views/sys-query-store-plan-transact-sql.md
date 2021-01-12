---
description: sys.query_store_plan (Transact-SQL)
title: sys.query_store_plan (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 01/23/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- QUERY_STORE_PLAN_TSQL
- SYS.QUERY_STORE_PLAN
- SYS.QUERY_STORE_PLAN_TSQL
- QUERY_STORE_PLAN
dev_langs:
- TSQL
helpviewer_keywords:
- query_store_plan catalog view
- sys.query_store_plan catalog view
ms.assetid: b4d05439-6360-45db-b1cd-794f4a64935e
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: =azuresqldb-current||>=sql-server-2016||= azure-sqldw-latest||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 1b13c6f42eb967c6ef5d01e189860c9de3204646
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98095463"
---
# <a name="sysquery_store_plan-transact-sql"></a>sys.query_store_plan (Transact-SQL)
[!INCLUDE [sqlserver2016-asdb-asdbmi-asa](../../includes/applies-to-version/sqlserver2016-asdb-asdbmi-asa.md)]

  包含與查詢相關聯之每個執行計畫的相關資訊。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|
|**plan_id**|**bigint**|主索引鍵。|  
|**query_id**|**bigint**|外鍵。 [Sys.query_store_query &#40;transact-sql&#41;](../../relational-databases/system-catalog-views/sys-query-store-query-transact-sql.md)的聯結。|  
|**plan_group_id**|**bigint**|方案群組的識別碼。 資料指標查詢通常需要多個 (填入和提取) 計畫。 填入和提取一起編譯的計畫會在相同的群組中。<br /><br /> 0表示方案不在群組中。|  
|**engine_version**|**nvarchar(32)**|用來以 **' 主要.** ........................。|  
|**compatibility_level**|**smallint**|查詢中所參考資料庫的資料庫相容性層級。|  
|**query_plan_hash**|**二元 (8)**|個別計畫的 MD5 雜湊。|  
|**query_plan**|**nvarchar(max)**|查詢計劃的執行程式表 XML。|  
|**is_online_index_plan**|**bit**|在線上索引建立期間使用了方案。 <br/>**注意：** Azure Synapse Analytics 一律會傳回零 (0) 。|  
|**is_trivial_plan**|**bit**|計畫是查詢最佳化工具) 第0階段的簡單計畫 (輸出。 <br/>**注意：** Azure Synapse Analytics 一律會傳回零 (0) 。|  
|**is_parallel_plan**|**bit**|計畫為平行。 <br/>**注意：** Azure Synapse Analytics 一律會傳回一個 (1) 。|  
|**is_forced_plan**|**bit**|當使用者執行預存程式 **sys.sp_query_store_force_plan** 時，計畫會標示為強制。 強制機制 *無法保證* **query_id** 所參考的查詢都將使用此計畫。 強制執行計畫會重新編譯查詢，而且通常會對 **plan_id** 所參考的計畫產生完全相同或類似的計畫。 如果強制執行計畫失敗， **force_failure_count** 會遞增，而 **last_force_failure_reason** 會填入失敗原因。 <br/>**注意：** Azure Synapse Analytics 一律會傳回零 (0) 。|  
|**is_natively_compiled**|**bit**|方案包含原生編譯的記憶體優化程式。  (0 = FALSE，1 = TRUE) 。 <br/>**注意：** Azure Synapse Analytics 一律會傳回零 (0) 。|  
|**force_failure_count**|**bigint**|強制執行此計畫的次數已失敗。 它只有在重新編譯查詢時才會遞增 (*不會在每次執行*) 。 每次 **is_plan_forced** 從 **FALSE** 變更為 **TRUE** 時，它就會重設為0。 <br/>**注意：** Azure Synapse Analytics 一律會傳回零 (0) 。|  
|**last_force_failure_reason**|**int**|強制執行計畫失敗的原因。<br /><br /> 0：沒有失敗，否則造成強制失敗的錯誤錯誤號碼<br /><br /> 8637： ONLINE_INDEX_BUILD<br /><br /> 8683： INVALID_STARJOIN<br /><br /> 8684： TIME_OUT<br /><br /> 8689： NO_DB<br /><br /> 8690： HINT_CONFLICT<br /><br /> 8691： SETOPT_CONFLICT<br /><br /> 8694： DQ_NO_FORCING_SUPPORTED<br /><br /> 8698： NO_PLAN<br /><br /> 8712： NO_INDEX<br /><br /> 8713： VIEW_COMPILE_FAILED<br /><br /> \<other value>： GENERAL_FAILURE <br/>**注意：** Azure Synapse Analytics 一律會傳回零 (0) 。|  
|**last_force_failure_reason_desc**|**nvarchar(128)**|Last_force_failure_reason_desc 的文字描述。<br /><br /> ONLINE_INDEX_BUILD：查詢嘗試修改資料，而目標資料表具有正在線上建立的索引<br /><br /> INVALID_STARJOIN：方案包含不正確 StarJoin 規格<br /><br /> TIME_OUT：在搜尋強制計畫所指定的計畫時，優化工具超過允許的作業數目<br /><br /> NO_DB：方案中指定的資料庫不存在<br /><br /> HINT_CONFLICT：無法編譯查詢，因為計畫與查詢提示衝突<br /><br /> DQ_NO_FORCING_SUPPORTED：無法執行查詢，因為計畫與分散式查詢或全文檢索作業的使用發生衝突。<br /><br /> NO_PLAN：查詢處理器無法產生查詢計劃，因為無法驗證強制計畫是否適用于查詢<br /><br /> NO_INDEX：在方案中指定的索引已不存在<br /><br /> VIEW_COMPILE_FAILED：無法強制執行查詢計劃，因為計畫中參考的索引視圖有問題<br /><br /> GENERAL_FAILURE：未涵蓋上述原因的一般強制錯誤 ()  <br/>**注意：** Azure Synapse Analytics 一律會傳回 *NONE*。|  
|**count_compiles**|**bigint**|規劃編譯統計資料。|  
|**initial_compile_start_time**|**datetimeoffset**|規劃編譯統計資料。|  
|**last_compile_start_time**|**datetimeoffset**|規劃編譯統計資料。|  
|**last_execution_time**|**datetimeoffset**|上次執行時間指的是查詢/計畫的最後結束時間。|  
|**avg_compile_duration**|**float**|規劃編譯統計資料。 <br/>**注意：** Azure Synapse Analytics 一律會傳回零 (0) 。|  
|**last_compile_duration**|**bigint**|規劃編譯統計資料。 <br/>**注意：** Azure Synapse Analytics 一律會傳回零 (0) 。|  
|**plan_forcing_type**|**int**|計畫強制型別。<br /><br />0：無<br /><br />1：手動<br /><br />2：自動|  
|**plan_forcing_type_desc**|**nvarchar(60)**|Plan_forcing_type 的文字描述。<br /><br />無：強制執行計畫<br /><br />手動：由使用者強制執行計畫<br /><br />AUTO：自動調整所強制的計畫|  

## <a name="plan-forcing-limitations"></a>強制執行計劃的限制
查詢存放區有強制查詢最佳化工具使用特定執行計劃的機制。 不過，也有避免強制執行計劃的一些限制。 

首先，如果計劃包含下列建構：
* INSERT BULK 陳述式。
* 參考外部資料表
* 分散式查詢或全文檢索作業
* 使用全域查詢 
* 動態或索引鍵集資料指標 
* 無效的星型聯結規格 

> [!NOTE]
> Azure SQL Database 和 SQL Server 2019 支援計畫強制執行靜態和向前快轉資料指標。

其次，無法再使用計劃依賴的物件時：
* 資料庫 (如果計劃的來源資料庫不再存在)
* 索引 (不再存在或停用)

最後，計劃本身的問題：
* 查詢不合法
* 查詢最佳化工具超過允許的作業數目
* 計劃 XML 格式不正確

## <a name="permissions"></a>權限  
 需要 **VIEW DATABASE STATE** 許可權。  
  
## <a name="see-also"></a>另請參閱  
 [sys.database_query_store_options &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-database-query-store-options-transact-sql.md)   
 [sys.query_coNtext_settings &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-query-context-settings-transact-sql.md)   
 [sys.query_store_query &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-query-store-query-transact-sql.md)   
 [sys.query_store_query_text &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-query-store-query-text-transact-sql.md)   
 [sys.query_store_runtime_stats &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql.md)   
 [sys.query_store_wait_stats &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql.md)  
 [sys.query_store_runtime_stats_interval &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-query-store-runtime-stats-interval-transact-sql.md)   
 [相關檢視、函數與程序](../../relational-databases/performance/monitoring-performance-by-using-the-query-store.md)   
 [目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/catalog-views-transact-sql.md)   
 [查詢存放區預存程序 &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/query-store-stored-procedures-transact-sql.md)  
  
  
