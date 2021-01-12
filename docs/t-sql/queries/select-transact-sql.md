---
title: SELECT (Transact-SQL)
description: SELECT (Transact-SQL)
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- SELECT_TSQL
- SELECT
dev_langs:
- TSQL
helpviewer_keywords:
- retrieving rows
- SELECT statement [SQL Server]
- SELECT statement [SQL Server], about SELECT statement
- row retrieval [SQL Server], SELECT statement
- DML [SQL Server], SELECT statement
- data manipulation language [SQL Server], SELECT statement
- row retrieval [SQL Server]
- queries [SQL Server], results
author: VanMSFT
ms.author: vanto
ms.custom: ''
ms.date: 10/24/2017
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: dff1dc7f969435e4a4f112e995fe092e8b4da8be
ms.sourcegitcommit: d681796e8c012eca2d9629d3b816749e9f50f868
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98005367"
---
# <a name="select-transact-sql"></a>SELECT (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  從資料庫中擷取資料列，並可讓您從 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中的一個或多個資料表選取一個或多個資料列或資料行。 SELECT 陳述式的完整語法很複雜，但主要子句可摘要如下：  
  
[ WITH { [[ XMLNAMESPACES ,]](../../t-sql/xml/with-xmlnamespaces.md) [[ \<common_table_expression> ]](../../t-sql/queries/with-common-table-expression-transact-sql.md) } ]
  
 [SELECT *select_list*](../../t-sql/queries/select-clause-transact-sql.md) [ [INTO *new_table*](../../t-sql/queries/select-into-clause-transact-sql.md) ]  
  
 [ [FROM *table_source*](../../t-sql/queries/from-transact-sql.md) ] [ [WHERE *search_condition*](../../t-sql/queries/where-transact-sql.md) ]  
  
 [ [GROUP BY *group_by_expression*](../../t-sql/queries/select-group-by-transact-sql.md) ]  
  
 [ [HAVING *search_condition*](../../t-sql/queries/select-having-transact-sql.md) ]  
  
 [ [ORDER BY *order_expression* [ ASC | DESC ]](../../t-sql/queries/select-order-by-clause-transact-sql.md)]  
  
 您可在查詢之間使用 [UNION](../../t-sql/language-elements/set-operators-union-transact-sql.md)、[EXCEPT 及 INTERSECT](../../t-sql/language-elements/set-operators-except-and-intersect-transact-sql.md) 運算子來將其結果結合成單一結果集或加以比較。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
-- Syntax for SQL Server and Azure SQL Database  
  
<SELECT statement> ::=    
    [ WITH { [ XMLNAMESPACES ,] [ <common_table_expression> [,...n] ] } ]  
    <query_expression>   
    [ ORDER BY <order_by_expression> ] 
    [ <FOR Clause>]   
    [ OPTION ( <query_hint> [ ,...n ] ) ]   
<query_expression> ::=   
    { <query_specification> | ( <query_expression> ) }   
    [  { UNION [ ALL ] | EXCEPT | INTERSECT }  
        <query_specification> | ( <query_expression> ) [...n ] ]   
<query_specification> ::=   
SELECT [ ALL | DISTINCT ]   
    [TOP ( expression ) [PERCENT] [ WITH TIES ] ]   
    < select_list >   
    [ INTO new_table ]   
    [ FROM { <table_source> } [ ,...n ] ]   
    [ WHERE <search_condition> ]   
    [ <GROUP BY> ]   
    [ HAVING < search_condition > ]   
```  
  
```syntaxsql
-- Syntax for Azure Synapse Analytics and Parallel Data Warehouse  
  
[ WITH <common_table_expression> [ ,...n ] ]  
SELECT <select_criteria>  
[;]  
  
<select_criteria> ::=  
    [ TOP ( top_expression ) ]   
    [ ALL | DISTINCT ]   
    { * | column_name | expression } [ ,...n ]   
    [ FROM { table_source } [ ,...n ] ]  
    [ WHERE <search_condition> ]   
    [ GROUP BY <group_by_clause> ]   
    [ HAVING <search_condition> ]   
    [ ORDER BY <order_by_expression> ]  
    [ OPTION ( <query_option> [ ,...n ] ) ]  
  
```  

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="remarks"></a>備註  
 由於 SELECT 陳述式十分複雜，因此將會依子句顯示詳細的語法元素及引數：  

:::row:::
    :::column:::
        [WITH XMLNAMESPACES](../../t-sql/xml/with-xmlnamespaces.md)
    :::column-end:::
    :::column:::
        [HAVING](../../t-sql/queries/select-having-transact-sql.md)
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [WITH common_table_expression](../../t-sql/queries/with-common-table-expression-transact-sql.md)
    :::column-end:::
    :::column:::
        [UNION](../../t-sql/language-elements/set-operators-union-transact-sql.md)
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [SELECT 子句](../../t-sql/queries/select-clause-transact-sql.md)
    :::column-end:::
    :::column:::
        [EXCEPT 和 INTERSECT](../../t-sql/language-elements/set-operators-except-and-intersect-transact-sql.md)
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [INTO 子句](../../t-sql/queries/select-into-clause-transact-sql.md)
    :::column-end:::
    :::column:::
        [ORDER BY](../../t-sql/queries/select-order-by-clause-transact-sql.md)
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [FROM](../../t-sql/queries/from-transact-sql.md)
    :::column-end:::
    :::column:::
        [FOR 子句](../../t-sql/queries/select-for-clause-transact-sql.md)
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [WHERE](../../t-sql/queries/where-transact-sql.md)
    :::column-end:::
    :::column:::
        [OPTION 子句](../../t-sql/queries/option-clause-transact-sql.md)
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [GROUP BY](../../t-sql/queries/select-group-by-transact-sql.md)
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

 子句順序對 SELECT 陳述式而言十分重要。 您可以省略任何選擇性的子句，但當使用選擇性的子句時，它們必須以適當的順序顯示。  
  
 只有在這些 SELECT 陳述式的選取清單包含指派使用者自訂函數之本機變數值的運算式時，才能在使用者自訂函數中，允許 SELECT 陳述式。  
  
 利用 OPENDATASOURCE 函數來建構成伺服器名稱部分的四部分名稱，每當 SELECT 陳述式中出現資料表名稱時，都可用來做為資料表來源。 針對 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]，不可指定四部分名稱。  
  
 部分語法限制只適用於牽涉到遠端資料表的 SELECT 陳述式。  
  
## <a name="logical-processing-order-of-the-select-statement"></a>SELECT 陳述式的邏輯處理順序  
 下列步驟顯示 SELECT 陳述式的邏輯處理順序或繫結順序。 此順序會決定在某個步驟中定義的物件提供給後續步驟之子句使用的時間。 例如，如果查詢處理器可繫結至 (存取) FROM 子句中定義的資料表或檢視表，這些物件及其資料行就會提供給所有後續步驟使用。 反之，因為 SELECT 子句是步驟 8，所以前面的子句無法參考該子句中定義的任何資料行別名或衍生資料行。 不過，ORDER BY 子句等後續子句都可以參考它們。 陳述式的實際執行方式由查詢處理器決定，其順序可能與此清單不同。  
  
1.  FROM  
2.  開啟  
3.  JOIN  
4.  WHERE  
5.  GROUP BY  
6.  WITH CUBE 或 WITH ROLLUP  
7.  HAVING  
8.  SELECT  
9. DISTINCT  
10. 排序依據  
11. 頂端  

> [!WARNING]
> 通常會按照上述順序。 不過，在一些不常見的情況下，順序可能會有不同。
>
> 例如，假設您在檢視表上有叢集索引，而該檢視表排除某些資料表資料列，且檢視表的 SELECT 資料行清單使用 CONVERT 將資料類型從 *varchar* 變更為 *integer*。 在此情況下，CONVERT 的執行順序可能會在 WHERE 子句之前。 這確實是不常見的情況。 如果在您的案例中順序相當重要，通常可以修改您的檢視表來避免順序不同。 

## <a name="permissions"></a>權限  
 選取資料需要資料表或檢視的 **SELECT** 權限；此權限可從較高的範圍繼承而來，例如結構描述的 **SELECT** 權限或資料表的 **CONTROL** 權限。 或是需要 **db_datareader** 或 **db_owner** 固定資料庫角色中的成員資格，或 **sysadmin** 固定伺服器角色中的成員資格。 使用 **SELECTINTO** 來建立新資料表也需要 **CREATE TABLE** 權限，以及擁有新資料表結構描述上的 **ALTERSCHEMA** 權限。  
  
## <a name="examples"></a>範例：   
下列範例使用 [!INCLUDE[ssawPDW](../../includes/ssawpdw-md.md)] 資料庫。
  
### <a name="a-using-select-to-retrieve-rows-and-columns"></a>A. 使用 SELECT 擷取資料列和資料行  
 本節將示範三個程式碼範例。 第一個程式碼範例會從 `DimEmployee` 資料表中，傳回所有資料列 (未指定 WHERE 子句) 和所有資料行 (使用 `*`)。  
  
```sql  
SELECT *  
FROM DimEmployee  
ORDER BY LastName;  
```  
  
 這個接下來的範例會使用資料表別名來達到相同的結果。  
  
```sql  
SELECT e.*  
FROM DimEmployee AS e  
ORDER BY LastName;  
```  
  
 此範例會從 `AdventureWorksPDW2012` 資料庫的 `DimEmployee` 資料表中，傳回所有資料列 (未指定 WHERE 子句)，以及資料行子集 (`FirstName`、`LastName`、`StartDate`)。 第三個資料行標題會重新命名為 `FirstDay`。  
  
```sql  
SELECT FirstName, LastName, StartDate AS FirstDay  
FROM DimEmployee   
ORDER BY LastName;  
```  
  
 此範例只會傳回 `EndDate` 不是 NULL 且 `MaritalStatus` 是 ‘M’ (已婚) 的 `DimEmployee` 資料列。  
  
```sql  
SELECT FirstName, LastName, StartDate AS FirstDay  
FROM DimEmployee   
WHERE EndDate IS NOT NULL   
AND MaritalStatus = 'M'  
ORDER BY LastName;  
```  
  
### <a name="b-using-select-with-column-headings-and-calculations"></a>B. 使用 SELECT 與資料行標題及計算  
 下列範例會從 `DimEmployee` 資料表中傳回所有資料列，並根據每位員工的 `BaseRate` 和 40 小時工作週來計算其總薪資。  
  
```sql  
SELECT FirstName, LastName, BaseRate, BaseRate * 40 AS GrossPay  
FROM DimEmployee  
ORDER BY LastName;  
```  
  
### <a name="c-using-distinct-with-select"></a>C. 使用 DISTINCT 與 SELECT  
 下列範例會使用 `DISTINCT`來產生 `DimEmployee` 資料表中所有唯一職稱的清單。  
  
```sql  
SELECT DISTINCT Title  
FROM DimEmployee  
ORDER BY Title;  
```  
  
### <a name="d-using-group-by"></a>D. 使用 GROUP BY  
 下列範例會尋找每天的所有銷售總額。  
  
```sql  
SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales  
FROM FactInternetSales  
GROUP BY OrderDateKey  
ORDER BY OrderDateKey;  
```  
  
 由於 `GROUP BY` 子句的緣故，因此只會針對每天，傳回一個包含所有銷售總和的資料列。  
  
### <a name="e-using-group-by-with-multiple-groups"></a>E. 使用 GROUP BY 與多個群組  
 下列範例會尋找每天網際網路銷售的平均價格和總和，並依照訂單日期和促銷索引鍵分組。  
  
```sql  

SELECT OrderDateKey, PromotionKey, AVG(SalesAmount) AS AvgSales, SUM(SalesAmount) AS TotalSales  
FROM FactInternetSales  
GROUP BY OrderDateKey, PromotionKey  
ORDER BY OrderDateKey;   
```  
  
### <a name="f-using-group-by-and-where"></a>F. 使用 GROUP BY 與 WHERE  
 下列範例會在只擷取訂單日期晚於 2002 年 8 月 1 日的資料列之後，將結果分組。  
  
```sql  
SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales  
FROM FactInternetSales  
WHERE OrderDateKey > '20020801'  
GROUP BY OrderDateKey  
ORDER BY OrderDateKey;  
```  
  
### <a name="g-using-group-by-with-an-expression"></a>G. 使用 GROUP BY 與運算式  
 下列範例會依運算式來分組。 如果運算式不包括彙總函式，您可以依運算式來進行分組。  
  
```sql  
SELECT SUM(SalesAmount) AS TotalSales  
FROM FactInternetSales  
GROUP BY (OrderDateKey * 10);  
```  
  
### <a name="h-using-group-by-with-order-by"></a>H. 使用 GROUP BY 與 ORDER BY  
 下列範例會尋找每天的銷售總和，然後依照日期排序。  
  
```sql  
SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales  
FROM FactInternetSales  
GROUP BY OrderDateKey  
ORDER BY OrderDateKey;  
```  
  
### <a name="i-using-the-having-clause"></a>I. 使用 HAVING 子句  
 此查詢會使用 `HAVING` 子句來限制結果。  
  
```sql  
SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales  
FROM FactInternetSales  
GROUP BY OrderDateKey  
HAVING OrderDateKey > 20010000  
ORDER BY OrderDateKey;  
```  
  
## <a name="see-also"></a>另請參閱  
 [SELECT 範例 &#40;Transact-SQL&#41;](../../t-sql/queries/select-examples-transact-sql.md)  
 [提示 &#40;Transact-SQL&#41;](../../t-sql/queries/hints-transact-sql.md)
  

