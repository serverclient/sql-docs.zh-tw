---
description: CREATE SELECTIVE XML INDEX (Transact-SQL)
title: CREATE SELECTIVE XML INDEX (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 08/10/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
dev_langs:
- TSQL
ms.assetid: 1d769f62-f646-4057-b93a-bf5f90e935ed
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 6431c70d94e106a30afeee2ee36b396c53a8c55f
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98093452"
---
# <a name="create-selective-xml-index-transact-sql"></a>CREATE SELECTIVE XML INDEX (Transact-SQL)
[!INCLUDE [SQL Server Azure SQL Database ](../../includes/applies-to-version/sql-asdb.md)]

  在指定的資料表和 XML 資料行上建立新的選擇性 XML 索引。 選擇性 XML 索引會藉由只為您通常查詢的節點子集編制索引，提升 XML 索引和查詢的效能。 您也可以建立次要選擇性 XML 索引。 如相關資訊，請參閱[建立、修改和卸除次要選擇性 XML 索引](../../relational-databases/xml/create-alter-and-drop-secondary-selective-xml-indexes.md)。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
CREATE SELECTIVE XML INDEX index_name  
    ON <table_object> (xml_column_name)  
    [WITH XMLNAMESPACES (<xmlnamespace_list>)]  
    FOR (<promoted_node_path_list>)  
    [WITH (<index_options>)]  
  
<table_object> ::=  
 { database_name.schema_name.table_name | schema_name.table_name | table_name }  
  
<promoted_node_path_list> ::=   
<named_promoted_node_path_item> [, <promoted_node_path_list>]  
  
<named_promoted_node_path_item> ::=   
<path_name> = <promoted_node_path_item>  
  
<promoted_node_path_item>::=  
<xquery_node_path_item> | <sql_values_node_path_item>  
  
<xquery_node_path_item> ::=   
<node_path> [AS XQUERY <xsd_type_or_node_hint>] [SINGLETON]  
  
<xsd_type_or_node_hint> ::=   
[<xsd_type>] [MAXLENGTH(x)] | node()  
  
<sql_values_node_path_item> ::=  
<node_path> AS SQL <sql_type> [SINGLETON]  
  
<node_path> ::=   
character_string_literal  
  
<xsd_type> ::=   
character_string_literal  
  
<sql_type> ::=   
identifier  
  
<path_name> ::=   
identifier  
  
<xmlnamespace_list> ::=   
<xmlnamespace_item> [, <xmlnamespace_list>]  
  
<xmlnamespace_item> ::=   
<xmlnamespace_uri> AS <xmlnamespace_prefix>  
  
<xml_namespace_uri> ::=   
character_string_literal  
  
<xml_namespace_prefix> ::=   
identifier  
  
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
  
 *\<table_object>* 這是包含所要編製索引 XML 資料行的資料表。 請使用下列其中一個格式：  
  
-   `database_name.schema_name.table_name`  
  
-   `database_name..table_name`  
  
-   `schema_name.table_name`  
  
-   `table_name`  
  
 *xml_column_name*  
 這是包含要索引之路徑的 XML 資料行名稱。  
  
 [WITH XMLNAMESPACES **(** \<xmlnamespace_list> **)** ] 是所要編製索引路徑使用的命名空間清單。 如需有關 WITH XMLNAMESPACES 子句語法的相關資訊，請參閱 [WITH XMLNAMESPACES &#40;Transact-SQL&#41;](../../t-sql/xml/with-xmlnamespaces.md)。  
  
 FOR **(** \<promoted_node_path_list> **)** 這是所要編製索引路徑的清單，包含選用最佳化提示。 如需有關可以在 CREATE 或 ALTER 陳述式中指定之路徑的資訊和最佳化提示，請參閱[指定選擇性 XML 索引的路徑和最佳化提示](../../relational-databases/xml/specify-paths-and-optimization-hints-for-selective-xml-indexes.md)。  
  
 WITH *\<index_options>* 如需索引選項的詳細資訊，請參閱 [CREATE XML INDEX &#40;Selective XML Indexes&#41;](../../t-sql/statements/create-xml-index-selective-xml-indexes.md)。  
  
## <a name="best-practices"></a>最佳做法  
 在大部分情況下，建立選擇性 XML 索引會比一般 XML 索引獲得更佳的效能和更有效率的儲存。 但是，下列任一情況為 true 時，不建議使用選擇性 XML 索引：  
  
-   您需要對應大量節點路徑。  
  
-   您需要支援未知元素或未知位置中元素的查詢。  
  
## <a name="limitations-and-restrictions"></a>限制事項  
 如需限制事項的相關資訊，請參閱[選擇性 XML 索引 &#40;SXI&#41;](../../relational-databases/xml/selective-xml-indexes-sxi.md)。  
  
## <a name="security"></a>安全性  
  
### <a name="permissions"></a>權限  
 需要資料表或檢視表的 ALTER 權限。 使用者必須是 **系統管理員** 固定伺服器角色的成員，或是 **db_ddladmin** 和 **db_owner** 固定資料庫角色的成員。  
  
## <a name="examples"></a>範例  
 下列範例會顯示建立選擇性 XML 索引的語法。 另外還會顯示描述要索引之路徑的多種語法變化，包含選用的最佳化提示。  
  
```sql  
CREATE TABLE Tbl ( id INT PRIMARY KEY, xmlcol XML );  
GO  
CREATE SELECTIVE XML INDEX sxi_index  
ON Tbl(xmlcol)  
FOR(  
    pathab   = '/a/b' as XQUERY 'node()',  
    pathabc  = '/a/b/c' as XQUERY 'xs:double',   
    pathdtext = '/a/b/d/text()' as XQUERY 'xs:string' MAXLENGTH(200) SINGLETON,  
    pathabe = '/a/b/e' as SQL NVARCHAR(100)  
);  
```  
  
 下列範例包括 WITH XMLNAMESPACES 子句。  
  
```sql  
CREATE SELECTIVE XML INDEX on T1(C1)  
WITH XMLNAMESPACES ('https://www.tempuri.org/' as myns)  
FOR ( path1 = '/myns:book/myns:author/text()' );  
```  
  
## <a name="see-also"></a>另請參閱  
 [選擇性 XML 索引 &#40;SXI&#41;](../../relational-databases/xml/selective-xml-indexes-sxi.md)   
 [建立、修改和卸除選擇性 XML 索引](../../relational-databases/xml/create-alter-and-drop-selective-xml-indexes.md)   
 [指定選擇性 XML 索引的路徑和最佳化提示](../../relational-databases/xml/specify-paths-and-optimization-hints-for-selective-xml-indexes.md)  
  
  

