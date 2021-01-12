---
description: sys.fn_stmt_sql_handle_from_sql_stmt (Transact-SQL)
title: sys.fn_stmt_sql_handle_from_sql_stmt (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
dev_langs:
- TSQL
ms.assetid: 6794e073-0895-4507-aba3-c3545acc843f
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: a2a23c3fffb8bfffa1cd9343d36dfd4c12bf5f1f
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98094963"
---
# <a name="sysfn_stmt_sql_handle_from_sql_stmt-transact-sql"></a>sys.fn_stmt_sql_handle_from_sql_stmt (Transact-SQL)

[!INCLUDE [sqlserver2016-asdb-asdbmi](../../includes/applies-to-version/sqlserver2016-asdb-asdbmi.md)]

  取得指定之 [!INCLUDE[tsql](../../includes/tsql-md.md)] 參數型別 (simple 或強制) 之語句的 stmt_sql_handle。 如此一來，當您知道查詢中的文字時，就可以使用它們的 **stmt_sql_handle** 來參考儲存在查詢存放區中的查詢。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```  
sys.fn_stmt_sql_handle_from_sql_stmt   
(  
    'query_sql_text',   
    [ query_param_type   
) [;]  
```  
  
## <a name="arguments"></a>引數  
 *query_sql_text*  
 這是您想要其控制碼之查詢存放區中的查詢文字。 *query_sql_text* 是 **Nvarchar (max)**，沒有預設值。  
  
 *query_param_type*  
 這是查詢的參數類型。 *query_param_type* 是 **Tinyint**。 可能的值包括：  
  
-   Null-預設值為0  
  
-   0 - 無  
  
-   1-使用者  
  
-   2-簡單  
  
-   3-強制  
  
## <a name="columns-returned"></a>傳回的資料行  
 下表列出 sys.fn_stmt_sql_handle_from_sql_stmt 傳回的資料行。  
  
|資料行名稱|類型|描述|  
|-----------------|----------|-----------------|  
|**statement_sql_handle**|**varbinary(64)**|SQL 控制碼。|  
|**query_sql_text**|**nvarchar(max)**|語句的文字 [!INCLUDE[tsql](../../includes/tsql-md.md)] 。|  
|**query_parameterization_type**|**tinyint**|查詢參數化型別。|  
  
## <a name="return-code-values"></a>傳回碼值  
 0 (成功) 或 1 (失敗)  
  
## <a name="remarks"></a>備註  
  
## <a name="permissions"></a>權限  
 需要資料庫的 **EXECUTE** 許可權，以及查詢存放區目錄檢視的 **DELETE** 許可權。  
  
## <a name="examples"></a>範例  
 下列範例會執行語句，然後使用傳回 `sys.fn_stmt_sql_handle_from_sql_stmt` 該語句的 SQL 控制碼。  
  
```  
SELECT * FROM sys.databases;   
SELECT * FROM sys.fn_stmt_sql_handle_from_sql_stmt('SELECT * FROM sys.databases', NULL);  
```  
  
 使用函數可將查詢存放區資料與其他動態管理檢視相互關聯。 下列範例將：  
  
```  
SELECT qt.query_text_id, q.query_id, qt.query_sql_text, qt.statement_sql_handle,  
q.context_settings_id, qs.statement_context_id   
FROM sys.query_store_query_text AS qt  
JOIN sys.query_store_query AS q   
    ON qt.query_text_id = q.query_id  
CROSS APPLY sys.fn_stmt_sql_handle_from_sql_stmt (qt.query_sql_text, null) AS fn_handle_from_stmt  
JOIN sys.dm_exec_query_stats AS qs   
    ON fn_handle_from_stmt.statement_sql_handle = qs.statement_sql_handle;  
```  
  
## <a name="see-also"></a>另請參閱  
 [sp_query_store_force_plan &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-query-store-force-plan-transact-sql.md)   
 [sp_query_store_remove_plan &#40;Transct-sql-SQL&#41;](../../relational-databases/system-stored-procedures/sp-query-store-remove-plan-transct-sql.md)   
 [sp_query_store_unforce_plan &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-query-store-unforce-plan-transact-sql.md)   
 [sp_query_store_reset_exec_stats &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-query-store-reset-exec-stats-transact-sql.md)   
 [sp_query_store_flush_db &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-query-store-flush-db-transact-sql.md)   
 [sp_query_store_remove_query &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-query-store-remove-query-transact-sql.md)   
 [查詢存放區 &#40;Transact-sql&#41;的目錄檢視 ](../../relational-databases/system-catalog-views/query-store-catalog-views-transact-sql.md)   
 [使用查詢存放區監視效能](../../relational-databases/performance/monitoring-performance-by-using-the-query-store.md)  
  
  
