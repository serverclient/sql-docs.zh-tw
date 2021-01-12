---
description: ALTER INDEX (選擇性 XML 索引)
title: ALTER INDEX (選擇性 XML 索引) | Microsoft Docs
ms.custom: ''
ms.date: 05/01/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
dev_langs:
- TSQL
ms.assetid: cca96a8f-7737-42d2-bbcc-03d5f858dcc1
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 47a934c2e8c89562e3aea7971df72da1b95dba3a
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98082159"
---
# <a name="alter-index-selective-xml-indexes"></a>ALTER INDEX (選擇性 XML 索引)
[!INCLUDE [SQL Server Azure SQL Database ](../../includes/applies-to-version/sql-asdb.md)]

  修改現有的選擇性 XML 索引。 ALTER INDEX 陳述式會變更下列一個或多個項目：  
  
-   索引路徑的清單 (FOR 子句)。  
  
-   命名空間的清單 (WITH XMLNAMESPACES 子句)。  
  
-   索引選項 (WITH 子句)。  
  
 您無法修改次要選擇性 XML 索引。 如需詳細資訊，請參閱[建立、修改和卸除次要選擇性 XML 索引](../../relational-databases/xml/create-alter-and-drop-secondary-selective-xml-indexes.md)。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
ALTER INDEX index_name  
    ON <table_object>   
    [WITH XMLNAMESPACES ( <xmlnamespace_list> )]  
    FOR ( <promoted_node_path_action_list> )  
    [WITH ( <index_options> )]  
  
<table_object> ::=   
{ database_name.schema_name.table_name | schema_name.table_name | table_name }  
<promoted_node_path_action_list> ::=   
<promoted_node_path_action_item> [, <promoted_node_path_action_list>]  
  
<promoted_node_path_action_item>::=   
<add_node_path_item_action> | <remove_node_path_item_action>  
  
<add_node_path_item_action> ::=  
ADD <path_name> = <promoted_node_path_item>  
  
<promoted_node_path_item>::=  
<xquery_node_path_item> | <sql_values_node_path_item>  
  
<remove_node_path_item_action> ::= REMOVE <path_name>   
  
<path_name_or_typed_node_path>::=   
<path_name> | <typed_node_path>  
  
<typed_node_path> ::=   
<node_path> [[AS XQUERY <xsd_type_ext>] | [AS SQL <sql_type>]]  
  
<xquery_node_path_item> ::=   
<node_path> [AS XQUERY <xsd_type_or_node_hint>] [SINGLETON]  
  
<xsd_type_or_node_hint> ::=   
[<xsd_type>] [MAXLENGTH(x)] | 'node()'  
  
<sql_values_node_path_item> ::=   
<node_path> AS SQL <sql_type> [SINGLETON]  
  
<node_path> ::=   
character_string_literal  
  
<xsd_type_ext> ::=   
character_string_literal  
  
<sql_type> ::=   
identifier  
  
<path_name> ::=   
identifier  
  
<xmlnamespace_list> ::=   
<xmlnamespace_item> [, <xmlnamespace_list>]  
  
<xmlnamespace_item> ::=   
<xmlnamespace_uri> AS <xmlnamespace_prefix>  
  
<xml_namespace_uri> ::= character_string_literal  
<xml_namespace_prefix> ::= identifier  
  
<index_options> ::=   
(   
  | PAD_INDEX  = { ON | OFF }  
  | FILLFACTOR = fillfactor  
  | SORT_IN_TEMPDB = { ON | OFF }  
  | IGNORE_DUP_KEY =OFF  
  | DROP_EXISTING = { ON | OFF }  
  | ONLINE =OFF  
  | ALLOW_ROW_LOCKS = { ON | OFF }  
  | ALLOW_PAGE_LOCKS = { ON | OFF }  
  | MAXDOP = max_degree_of_parallelism  
)  
```  
  
##  <a name="arguments"></a><a name="Arguments"></a> 引數  
 *index_name*  
 這是要修改之現有索引的名稱。  
  
 *\<table_object>*  
 這是包含要索引之 XML 資料行的資料表。 請使用下列其中一個格式：  
  
-   `database_name.schema_name.table_name`  
  
-   `database_name..table_name`  
  
-   `schema_name.table_name`  
  
-   `table_name`  
  
 [WITH XMLNAMESPACES **(** \<xmlnamespace_list> **)** ]  
 這是要索引之路徑使用的命名空間清單。 如需有關 WITH XMLNAMESPACES 子句語法的相關資訊，請參閱 [WITH XMLNAMESPACES &#40;Transact-SQL&#41;](../../t-sql/xml/with-xmlnamespaces.md)。  
  
 FOR **(** \<promoted_node_path_action_list> **)**  
 這是要加入或移除的索引路徑清單。  
  
-   **新增路徑。** 當您 ADD 路徑時，會使用透過 CREATE SELECTIVE XML INDEX 陳述式建立路徑的相同語法。 如需有關可以在 CREATE 或 ALTER 陳述式中指定之路徑的詳細資訊，請參閱[指定選擇性 XML 索引的路徑和最佳化提示](../../relational-databases/xml/specify-paths-and-optimization-hints-for-selective-xml-indexes.md)。  
  
-   **移除路徑。** 當您 REMOVE 路徑時，會提供建立路徑時指定的名稱。  
  
 [WITH **(** \<index_options> **)** ]  
 使用未包含 FOR 子句的 ALTER INDEX 時，只能指定 \<index_options>。 當您使用 ALTER INDEX 加入或移除索引中的路徑時，索引選項不是有效的引數。 如需索引選項的詳細資訊，請參閱 [CREATE XML INDEX &#40;Selective XML Indexes&#41;](../../t-sql/statements/create-xml-index-selective-xml-indexes.md)。  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="remarks"></a>備註
  
> [!IMPORTANT]  
>  當您執行 ALTER INDEX 陳述式時，一律會重建選擇性 XML 索引。 請務必考慮這個程序對於伺服器資源的影響。  
  
## <a name="security"></a>安全性  
  
### <a name="permissions"></a>權限  
 需要有資料表或檢視的 ALTER 權限才能執行 ALTER INDEX。  
  
## <a name="examples"></a>範例  
 下列範例顯示 ALTER INDEX 陳述式。 此陳述式會將路徑 `'/a/b/m'` 加入索引的 XQuery 部分，並且從 [CREATE SELECTIVE XML INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/create-selective-xml-index-transact-sql.md) 主題的範例中所建立索引的 SQL 部分刪除路徑 `'/a/b/e'`。 要刪除的路徑是以建立時提供的名稱識別。  
  
```sql  
ALTER INDEX sxi_index  
ON Tbl  
FOR   
(  
    ADD pathm = '/a/b/m' as XQUERY 'node()' ,  
    REMOVE pathabe  
);  
```  
  
 下列範例顯示指定索引選項的 ALTER INDEX 陳述式。 索引選項可以使用，因為陳述式不會使用 FOR 子句加入或移除路徑。  
  
```sql  
ALTER INDEX sxi_index  
ON Tbl  
PAD_INDEX = ON;  
```  
  
## <a name="see-also"></a>另請參閱  
 [選擇性 XML 索引 &#40;SXI&#41;](../../relational-databases/xml/selective-xml-indexes-sxi.md)   
 [建立、修改和卸除選擇性 XML 索引](../../relational-databases/xml/create-alter-and-drop-selective-xml-indexes.md)   
 [指定選擇性 XML 索引的路徑和最佳化提示](../../relational-databases/xml/specify-paths-and-optimization-hints-for-selective-xml-indexes.md)  
  
  
