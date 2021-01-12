---
description: CREATE XML INDEX (選擇性 XML 索引)
title: CREATE XML INDEX (選擇性 XML 索引) | Microsoft Docs
ms.custom: ''
ms.date: 08/10/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
dev_langs:
- TSQL
ms.assetid: 1f510151-41d5-45c2-9cd0-b1ca0246fffe
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: f6f0033a1fb5944fa61460fa3945b0ebdaf0bce3
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98099466"
---
# <a name="create-xml-index-selective-xml-indexes"></a>CREATE XML INDEX (選擇性 XML 索引)
[!INCLUDE [SQL Server Azure SQL Database ](../../includes/applies-to-version/sql-asdb.md)]

  在已由現有選擇性 XML 索引建立索引的單一路徑上，建立新的次要選擇性 XML 索引。 您也可以建立主要選擇性 XML 索引。 如相關資訊，請參閱[建立、修改和卸除選擇性 XML 索引](../../relational-databases/xml/create-alter-and-drop-selective-xml-indexes.md)。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
CREATE XML INDEX index_name  
    ON <table_object> ( xml_column_name )  
    USING XML INDEX sxi_index_name  
    FOR ( <xquery_or_sql_values_path> )  
    [WITH ( <index_options> )]  
  
<table_object> ::=   
{ database_name.schema_name.table_name | schema_name.table_name | table_name }  
  
<xquery_or_sql_values_path>::=   
<path_name>   
  
<path_name> ::=   
character string literal  
  
<xmlnamespace_list> ::=   
<xmlnamespace_item> [, <xmlnamespace_list>]  
  
<xmlnamespace_item> ::=   
xmlnamespace_uri AS xmlnamespace_prefix  
  
<index_options> ::=   
(    
  | PAD_INDEX  = { ON | OFF }  
  | FILLFACTOR = fillfactor  
  | SORT_IN_TEMPDB = { ON | OFF }  
  | IGNORE_DUP_KEY = OFF  
  | DROP_EXISTING = { ON | OFF }  
  | ONLINE = OFF  
  | ALLOW_ROW_LOCKS = { ON | OFF }  
  | ALLOW_PAGE_LOCKS = { ON | OFF }  
  | MAXDOP = max_degree_of_parallelism  
)  
```  
  
##  <a name="arguments"></a><a name="Arguments"></a> 引數  
 *index_name*  
 這是要建立之新索引的名稱。 索引名稱在資料表中必須是唯一的，但是在資料庫中不一定要是唯一的。 索引名稱必須遵照[識別碼](../../relational-databases/databases/database-identifiers.md)的規則。  
  
 ON *\<table_object>* 是資料表，其包含要索引的 XML 資料行。 您可以使用下列格式：  
  
-   `database_name.schema_name.table_name`  
  
-   `database_name..table_name`  
  
-   `schema_name.table_name`  
  
 *xml_column_name*  
 這是包含要索引之路徑的 XML 資料行名稱。  
  
 USING XML INDEX *sxi_index_name*  
 這是現有選擇性 XML 索引的名稱。  
  
 FOR **(** \<xquery_or_sql_values_path> **)** 是要在其中建立次要選擇性 XML 索引的索引路徑名稱。 索引路徑是來自 CREATE SELECTIVE XML INDEX 陳述式的指派名稱。 如需詳細資訊，請參閱 [CREATE SELECTIVE XML INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/create-selective-xml-index-transact-sql.md)。  
  
 WITH \<index_options> 如需索引選項的資訊，請參閱 [CREATE XML INDEX](../../t-sql/statements/create-xml-index-selective-xml-indexes.md)。  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="remarks"></a>備註
 基底資料表中的每個 XML 資料行可以擁有多個次要選擇性 XML 索引。  
  
## <a name="limitations-and-restrictions"></a>限制事項  
 XML 資料行上必須先有選擇性 XML 索引，才能在該資料行上建立次要選擇性 XML 索引。  
  
## <a name="security"></a>安全性  
  
### <a name="permissions"></a>權限  
 需要資料表或檢視表的 ALTER 權限。 使用者必須是 **系統管理員** 固定伺服器角色的成員，或是 **db_ddladmin** 和 **db_owner** 固定資料庫角色的成員。  
  
## <a name="examples"></a>範例  
 下列範例會在 `pathabc`路徑上建立次要選擇性 XML 索引。 索引路徑是來自 [CREATE SELECTIVE XML INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/create-selective-xml-index-transact-sql.md) 的指派名稱。  
  
```sql  
CREATE XML INDEX filt_sxi_index_c  
ON Tbl(xmlcol)  
USING XML INDEX sxi_index  
FOR ( pathabc );  
```  
  
## <a name="see-also"></a>另請參閱  
 [選擇性 XML 索引 &#40;SXI&#41;](../../relational-databases/xml/selective-xml-indexes-sxi.md)   
 [建立、修改和卸除次要選擇性 XML 索引](../../relational-databases/xml/create-alter-and-drop-secondary-selective-xml-indexes.md)  
  
  

