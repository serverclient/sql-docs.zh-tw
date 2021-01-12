---
description: 'sys.query_coNtext_settings (Transact-sql) '
title: sys.query_coNtext_settings (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 11/29/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- QUERY_CONTEXT_SETTINGS_TSQL
- SYS.QUERY_CONTEXT_SETTINGS_TSQL
- SYS.QUERY_CONTEXT_SETTINGS
- QUERY_CONTEXT_SETTINGS
dev_langs:
- TSQL
helpviewer_keywords:
- sys.query_context_settings catalog view
ms.assetid: 3c1887df-6bd8-491e-82fc-d25ad9589faf
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: =azuresqldb-current||>=sql-server-2016||= azure-sqldw-latest||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 510f508b8d0048b1e13aa544a3c07f20f3a1d44c
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98098328"
---
# <a name="sysquery_context_settings-transact-sql"></a>sys.query_coNtext_settings (Transact-sql) 
[!INCLUDE [sqlserver2016-asdb-asdbmi-asa](../../includes/applies-to-version/sqlserver2016-asdb-asdbmi-asa.md)]

  包含影響與查詢相關聯之內容設定之語義的相關資訊。 中有許多可用的內容設定，會 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 影響查詢的語法 (定義查詢) 的正確結果。 在不同設定下編譯的相同查詢文字，可能會產生不同的結果 (視基礎資料) 而定。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**coNtext_settings_id**|**bigint**|主索引鍵。 此值會在查詢的執行程式表 XML 中公開。|  
|**set_options**|**varbinary(8)**|用來反映數個 SET 選項狀態的位元遮罩。 如需詳細資訊，請參閱 [sys.dm_exec_plan_attributes &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-plan-attributes-transact-sql.md)。|  
|**language_id**|**smallint**|語言的識別碼。 如需詳細資訊，請參閱 [ &#40;transact-sql&#41;的sys.sys語言 ](../../relational-databases/system-compatibility-views/sys-syslanguages-transact-sql.md)。|  
|**date_format**|**smallint**|日期格式。 如需詳細資訊，請參閱 [SET DATEFIRST &#40;Transact-SQL&#41;](../../t-sql/statements/set-dateformat-transact-sql.md)。|  
|**date_first**|**tinyint**|日期第一個值。 如需詳細資訊，請參閱 [SET DATEFIRST &#40;Transact-SQL&#41;](../../t-sql/statements/set-datefirst-transact-sql.md)。|  
|**status**|**Varbinary (2)**|位元遮罩欄位，表示執行查詢的查詢或內容類型。 <br />資料行值可以結合多個旗標， (以十六進位) 表示：<br /><br /> 0x0-一般查詢 (沒有特定旗標) <br /><br /> 0x1-透過其中一個資料指標 Api 預存程式執行的查詢<br /><br /> 0x2-查詢通知<br /><br /> 0x4-內部查詢<br /><br /> 0x8-沒有通用參數化的自動參數化查詢<br /><br /> 0x10-資料指標提取重新整理查詢<br /><br /> 0x20-正在用於資料指標更新要求的查詢<br /><br /> 0x40-當 (資料指標自動提取開啟資料指標時，就會傳回初始結果集) <br /><br /> 0x80-加密查詢<br /><br /> 0x100-在資料列層級安全性述詞的內容中查詢|  
|**required_cursor_options**|**int**|使用者指定的資料指標選項，例如資料指標類型。|  
|**acceptable_cursor_options**|**int**|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 可能會隱含轉換的目標資料指標選項，以支援執行陳述式。|  
|**merge_action_type**|**smallint**|當做 **MERGE** 語句結果使用的觸發程式執行計畫類型。<br /><br /> 0表示非觸發程式計畫、不會作為 **merge** 語句結果執行的觸發程式計畫，或是作為 **merge** 語句結果執行的觸發程式計畫，只指定 **刪除** 動作。<br /><br /> 1表示以 **MERGE** 語句的結果形式執行的 **INSERT** 觸發程式計畫。<br /><br /> 2表示以 **MERGE** 語句的結果形式執行的 **更新** 觸發程式計畫。<br /><br /> 3表示 **刪除** 觸發程式計畫，該計畫會以包含對應 **INSERT** 或 **UPDATE** 動作的 **MERGE** 語句結果形式執行。<br /><br /> <br /><br /> 如果是串聯式動作所執行的嵌套觸發程式，這個值就是造成 cascade 的 **MERGE** 語句的動作。|  
|**default_schema_id**|**int**|預設架構的識別碼，用來解析不是完整限定名稱。|  
|**is_replication_specific**|**bit**|用於複寫。|  
|**is_contained**|**Varbinary (1)**|1表示自主資料庫。|  
  
## <a name="permissions"></a>權限  
 需要 **VIEW DATABASE STATE** 許可權。  
  
## <a name="see-also"></a>另請參閱  
 [sys.database_query_store_options &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-database-query-store-options-transact-sql.md)   
 [sys.query_store_plan &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-query-store-plan-transact-sql.md)   
 [sys.query_store_query &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-query-store-query-transact-sql.md)   
 [sys.query_store_query_text &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-query-store-query-text-transact-sql.md)   
 [sys.query_store_runtime_stats &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql.md)   
 [sys.query_store_wait_stats &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql.md)   
 [sys.query_store_runtime_stats_interval &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-query-store-runtime-stats-interval-transact-sql.md)   
 [相關檢視、函數與程序](../../relational-databases/performance/monitoring-performance-by-using-the-query-store.md)   
 [目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/catalog-views-transact-sql.md)   
 [查詢存放區預存程序 &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/query-store-stored-procedures-transact-sql.md)   
 [sys.fn_stmt_sql_handle_from_sql_stmt &#40;Transact-SQL&#41;](../../relational-databases/system-functions/sys-fn-stmt-sql-handle-from-sql-stmt-transact-sql.md)  
  
  
