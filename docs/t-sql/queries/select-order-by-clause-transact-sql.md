---
description: SELECT - ORDER BY 子句 (Transact-SQL)
title: ORDER BY 子句 (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 12/24/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- ORDER_TSQL
- BY
- ORDER_BY_TSQL
- BY_TSQL
- ORDER
- ORDER BY
dev_langs:
- TSQL
helpviewer_keywords:
- ad-hoc query paging
- OFFSET clause
- SELECT statement [SQL Server], FETCH clause
- clauses [SQL Server], ORDER BY
- SELECT statement [SQL Server], limiting the rows returned
- data [SQL Server], limiting the rows returned
- data [SQL Server], ad-hoc query paging
- sort orders [SQL Server]
- SELECT statement [SQL Server], OFFSET clause
- ORDER BY clause [Transact-SQL]
- LIMIT
- limiting result sets
- SELECT statement [SQL Server], ORDER BY clause
- query paging
- data [SQL Server], sorting
- limiting data returned in a query
- sort orders [SQL Server], ORDER BY clause
- FETCH clause
ms.assetid: bb394abe-cae6-4905-b5c6-8daaded77742
author: VanMSFT
ms.author: vanto
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: a7903d3a208bd08c9ba22d251a0e4124e81725ea
ms.sourcegitcommit: 2bdf1f1ee88f4fe3e872227d025e965e95d1b2b4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98711984"
---
# <a name="select---order-by-clause-transact-sql"></a>SELECT - ORDER BY 子句 (Transact-SQL)

[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  排序 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 查詢所傳回的資料。 此子句可用於：  
  
-   依據指定的資料行清單排序查詢的結果集，並選擇性地將傳回的資料列限制在指定範圍內。 除非指定 ORDER BY 子句，否則不保證結果集中傳回資料列的順序。  
  
-   決定[次序函數](../../t-sql/functions/ranking-functions-transact-sql.md)值套用至結果集的順序。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
> [!NOTE]  
>  [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 或 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] 中的 SELECT/INTO 或 CREATE TABLE AS SELECT (CTAS) 陳述式不支援 ORDER BY。

## <a name="syntax"></a>語法  
  
```syntaxsql
-- Syntax for SQL Server and Azure SQL Database  
  
ORDER BY order_by_expression  
    [ COLLATE collation_name ]   
    [ ASC | DESC ]   
    [ ,...n ]   
[ <offset_fetch> ]  
  
<offset_fetch> ::=  
{   
    OFFSET { integer_constant | offset_row_count_expression } { ROW | ROWS }  
    [  
      FETCH { FIRST | NEXT } {integer_constant | fetch_row_count_expression } { ROW | ROWS } ONLY  
    ]  
}  
```  
  
```syntaxsql
-- Syntax for Azure Synapse Analytics and Parallel Data Warehouse  
  
[ ORDER BY   
    {  
    order_by_expression   
    [ ASC | DESC ]   
    } [ ,...n ]   
]   
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *order_by_expression*  
 指定要排序查詢結果集的資料行或運算式。 排序資料行可以指定為名稱或資料行別名，或代表選取清單中資料行位置的非負整數。  
  
 您可以指定多個排序資料行。 資料行名稱必須是唯一名稱。 ORDER BY 子句中的排序資料行順序用來定義排序結果集的組織。 也就是說，結果集依據第一個資料行來排序，然後該排序的清單依據第二個資料行來排序，依此類推。  
  
 ORDER BY 子句中所參考資料行名稱必須對應到選取清單中的資料行，或語意明確之 FROM 子句中所指定資料表的已定義資料行。 如果 ORDER BY 子句參考選取清單中的資料行別名，資料行別名必須單獨使用，而不是在 ORDER BY 子句中作為某個運算式的一部分，例如：
 
```sql
SELECT SCHEMA_NAME(schema_id) AS SchemaName FROM sys.objects 
ORDER BY SchemaName; -- correct 
SELECT SCHEMA_NAME(schema_id) AS SchemaName FROM sys.objects 
ORDER BY SchemaName + ''; -- wrong
```
  
 COLLATE *collation_name*  
 指定應依據 *collation_name* 所指定的定序執行 ORDER BY 作業，而不應根據資料表或檢視表所定義的資料行定序執行。 *collation_name* 可以是 Windows 定序名稱或 SQL 定序名稱。 如需詳細資訊，請參閱 [Collation and Unicode Support](../../relational-databases/collations/collation-and-unicode-support.md)。 COLLATE 只適用於下列類型的資料行：**char**、**varchar**、**nchar** 及 **nvarchar**。  
  
 **ASC** | DESC  
 指定指定之資料行的值應該以遞增或遞減順序排序。 ASC 從最低值到最高值進行排序。 DESC 從最高值到最低值進行排序。 ASC 是預設排序次序。 Null 值會當做最低的可能值來處理。  
  
 OFFSET { *integer_constant* | *offset_row_count_expression* } { ROW | ROWS }  
 指定要略過的資料列數目，然後才開始從查詢運算式傳回資料列。 值可以是大於或等於零的整數常數或運算式。  
  
**適用於**：[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 及更新版本和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。  
  
 *offset_row_count_expression* 可以是變數、參數或常數純量子查詢。 在使用子查詢時，它無法參考定義在外部查詢範圍中的任何資料行。 也就是，它不能與外部查詢相互關聯。  
  
 ROW 和 ROWS 是同義字，基於 ANSI 相容性提供它們。  
  
 在查詢執行計畫中，位移資料列計數值會顯示在 TOP 查詢運算子的 **Offset** 屬性中。  
  
 FETCH { FIRST \| NEXT } { *integer_constant* \| *fetch_row_count_expression* } { ROW \| ROWS } ONLY  
 指定要在已處理 OFFSET 子句之後傳回的資料列數目。 值可以是大於或等於一的整數常數或運算式。  
  
**適用於**：[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 及更新版本和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。  
  
 *fetch_row_count_expression* 可以是變數、參數或常數純量子查詢。 在使用子查詢時，它無法參考定義在外部查詢範圍中的任何資料行。 也就是，它不能與外部查詢相互關聯。  
  
 FIRST 和 NEXT 是同義字，基於 ANSI 相容性提供它們。  
  
 ROW 和 ROWS 是同義字，基於 ANSI 相容性提供它們。  
  
 在查詢執行計畫中，位移資料列計數值會顯示在 TOP 查詢運算子的 **Rows** 或 **Top** 屬性中。  
  
## <a name="best-practices"></a>最佳做法  
 避免將 ORDER BY 子句中的整數指定為選取清單中資料行的位置表示。 例如，雖然如 `SELECT ProductID, Name FROM Production.Production ORDER BY 2` 的陳述式有效，但相較於指定實際資料行名稱，此陳述式比較不容易為其他人了解。 此外，對選取清單的變更，例如變更資料行順序或加入新資料行，需要修改 ORDER BY 子句，以避免非預期的結果。  
  
 在 SELECT TOP (*N*) 陳述式中，永遠使用 ORDER BY 子句。 這是以預測的方式指示哪些資料列受到 TOP 影響的唯一方法。 如需詳細資訊，請參閱 [TOP &#40;Transact-SQL&#41;](../../t-sql/queries/top-transact-sql.md)。  
  
## <a name="interoperability"></a>互通性  
 搭配 SELECT .。。INTO 或 INSERT .。。SELECT 語句：若要插入其他來源的資料列，ORDER BY 子句並不保證會以指定的順序插入資料列。  
  
 檢視表中使用 OFFSET 和 FETCH，並不會變更檢視表的可更新性屬性。  
  
## <a name="limitations-and-restrictions"></a>限制事項  
 ORDER BY 子句中的資料行數目沒有限制；不過，ORDER BY 子句中所指定之資料行的大小總計不能超過 8,060 位元組。  
  
 ORDER BY 子句中不能使用 **ntext**、**text**、**image**、**geography**、**geometry** 和 **xml** 類型的資料行。  
  
 當次序函數中出現 *order_by_expression* 時不能指定整數或常數。 如需詳細資訊，請參閱 [OVER 子句 &#40;Transact-SQL&#41;](../../t-sql/queries/select-over-clause-transact-sql.md)。  
  
 如果資料表名稱在 FROM 子句中使用的是別名，則只能使用別名來限定其在 ORDER BY 子句中的資料行。  
  
 如果 SELECT 陳述式包含下列其中一個子句或運算子，ORDER BY 子句中指定的資料行名稱和別名必須定義在選取清單中：  
  
-   UNION 運算子  
  
-   EXCEPT 運算子  
  
-   INTERSECT 運算子  
  
-   SELECT DISTINCT  
  
 另外，當此陳述式包括 UNION、EXCEPT 或 INTERSECT 運算子時，必須在第一個 (左邊) 查詢的選取清單中指定資料行名稱或資料行別名。  
  
 在使用 UNION、EXCEPT 或 INTERSECT 運算子的查詢中，只在陳述式結尾處允許 ORDER BY。 此限制只適用於在最上層查詢 (而非子查詢) 中指定 UNION、EXCEPT 和 INTERSECT 時。 請參閱稍後的＜範例＞一節。  
  
 除非同時也指定 TOP 或 OFFSET 和 FETCH 子句，否則 ORDER BY 子句在檢視表、內嵌函數、衍生資料表和子查詢中為無效。 在這些物件中使用 ORDER BY 子句時，這個子句只能用來判斷 TOP 子句或 OFFSET 和 FETCH 子句傳回的資料列。 除非同時在查詢本身指定 ORDER BY 子句，否則 ORDER BY 子句並不保證在查詢上述建構時會傳回排序的結果。  
  
 索引檢視表中或透過 CHECK OPTION 子句定義的檢視表中，不支援 OFFSET 和 FETCH。  
  
 在允許 TOP 和 ORDER BY 的任何查詢中，都可以使用 OFFSET 和 FETCH，但有下列限制：  
  
-   OVER 子句不支援 OFFSET 和 FETCH。  
  
-   OFFSET 和 FETCH 不能直接在 INSERT、UPDATE、MERGE 和 DELETE 陳述式中指定，但是可以在這些陳述式中所定義的子查詢中指定。 例如，在 INSERT INTO SELECT 陳述式中，OFFSET 和 FETCH 可以在 SELECT 陳述式中指定。  
  
-   在使用 UNION、EXCEPT 或 INTERSECT 運算子的查詢中，只能在指定查詢結果順序的最後查詢中指定 OFFSET 和 FETCH。  
  
-   TOP 無法與相同查詢運算式 (相同查詢範圍) 中的 OFFSET 和 FETCH 結合。  
  
## <a name="using-offset-and-fetch-to-limit-the-rows-returned"></a>使用 OFFSET 和 FETCH 限制傳回的資料列  
 我們建議您使用 OFFSET 和 FETCH 子句 (而不要使用 TOP 子句)，來實作查詢分頁方案，並限制傳送給用戶端應用程式的資料列數目。  
  
 如果使用 OFFSET 和 FETCH 做為分頁方案，需要針對傳回給用戶端應用程式的每一「頁」資料執行查詢一次。 例如，若要以 10 個資料列的增量傳回查詢結果，您必須執行一次查詢傳回 1 到 10 的資料列，然後再執行一次查詢傳回 11 到 20 的資料列，依此類推。 每個查詢各自獨立，無論如何都不相關。 這表示，不同於使用資料指標執行查詢一次，並在伺服器上維護狀態，用戶端應用程式會負責追蹤狀態。 若要使用 OFFSET 和 FETCH，在查詢要求之間達到穩定結果，必須符合下列條件：  
  
1.  查詢所使用的基礎資料不可以變更。 也就是說，查詢接觸的資料列不會更新，或者對網頁的所有查詢要求都是在使用快照集或可序列化交易隔離的單一交易中執行。 如需這些交易隔離等級的詳細資訊，請參閱 [SET TRANSACTION ISOLATION LEVEL &#40;Transact-SQL&#41;](../../t-sql/statements/set-transaction-isolation-level-transact-sql.md)。  
  
2.  ORDER BY 子句包含的資料行或資料行組合保證是唯一。  
  
 請參閱本主題稍後的＜範例＞一節中的範例＜在單一交易中執行多個查詢＞。  
  
 如果一致的執行計畫是分頁方案的要素，請考慮為 OFFSET 和 FETCH 參數使用 OPTIMIZE FOR 查詢提示。 請參閱本主題稍後的＜範例＞一節中的＜為 OFFSET 和 FETCH 值指定運算式＞。 如需 OPTIMIZE FOR 的詳細資訊，請參閱[查詢提示 &#40;Transact-SQL&#41;](../../t-sql/queries/hints-transact-sql-query.md)。  
  
## <a name="examples"></a>範例  
  
|類別|代表性語法元素|  
|--------------|------------------------------|  
|[基本語法](#BasicSyntax)|排序依據|  
|[指定遞增和遞減順序](#SortOrder)|DESC • ASC|  
|[指定定序](#Collation)|COLLATE|  
|[指定條件順序](#Case)|CASE 運算式|  
|[在次序函數中使用 ORDER BY](#Rank)|排名函數|  
|[限制傳回的資料列數目](#Offset)|OFFSET • FETCH|  
|[搭配 UNION、EXCEPT 和 INTERSECT 使用 ORDER BY](#Union)|UNION|  
  
###  <a name="basic-syntax"></a><a name="BasicSyntax"></a> 基本語法  
 本節的範例會使用所需的最少語法來示範 ORDER BY 子句的基本功能。  
  
#### <a name="a-specifying-a-single-column-defined-in-the-select-list"></a>A. 指定選取清單中所定義的單一資料行  
 下列範例會依據數值 `ProductID` 資料行來排序結果集。 因為未指定特定的排序次序，所以會使用預設值 (遞增順序)。  
  
```sql
USE AdventureWorks2012;  
GO  
SELECT ProductID, Name FROM Production.Product  
WHERE Name LIKE 'Lock Washer%'  
ORDER BY ProductID;  
```  
  
#### <a name="b-specifying-a-column-that-is-not-defined-in-the-select-list"></a>B. 指定選取清單中未定義的資料行  
 在下列範例中，結果集排序依據的資料行並不包含在選取清單中，而是定義在 FROM 子句中所指定的資料表中。  
  
```sql
USE AdventureWorks2012;  
GO  
SELECT ProductID, Name, Color  
FROM Production.Product  
ORDER BY ListPrice;  
```  
  
#### <a name="c-specifying-an-alias-as-the-sort-column"></a>C. 指定別名做為排序資料行  
 下列範例會指定資料行別名 `SchemaName` 做為排序次序資料行。  
  
```sql
USE AdventureWorks2012;  
GO  
SELECT name, SCHEMA_NAME(schema_id) AS SchemaName  
FROM sys.objects  
WHERE type = 'U'  
ORDER BY SchemaName;  
```  
  
#### <a name="d-specifying-an-expression-as-the-sort-column"></a>D. 指定運算式做為排序資料行  
 下列範例會使用運算式做為排序資料行。 運算式是透過使用 DATEPART 函數來定義，以依據員工雇用年度來排序結果集。  
  
```sql
USE AdventureWorks2012;  
GO  
SELECT BusinessEntityID, JobTitle, HireDate  
FROM HumanResources.Employee  
ORDER BY DATEPART(year, HireDate);  
```  
  
###  <a name="specifying-ascending-and-descending-sort-order"></a><a name="SortOrder"></a> 指定遞增和遞減排序次序  
  
#### <a name="a-specifying-a-descending-order"></a>A. 指定遞減順序  
 下列範例會依據數值資料行 `ProductID` 以遞減順序來排序結果集。  
  
```sql
USE AdventureWorks2012;  
GO  
SELECT ProductID, Name FROM Production.Product  
WHERE Name LIKE 'Lock Washer%'  
ORDER BY ProductID DESC;  
```  
  
#### <a name="b-specifying-an-ascending-order"></a>B. 指定遞增順序  
 下列範例會依據資料行 `Name` 以遞增順序來排序結果集。 字元會以字母順序排序，而不是以數值順序排序。 也就是說，10 會排序在 2 之前。  
  
```sql
USE AdventureWorks2012;  
GO  
SELECT ProductID, Name FROM Production.Product  
WHERE Name LIKE 'Lock Washer%'  
ORDER BY Name ASC ;  
```  
  
#### <a name="c-specifying-both-ascending-and-descending-order"></a>C. 指定遞增和遞減順序  
 下列範例會依據兩個資料行來排序結果集。 查詢的結果集會先依據 `FirstName` 資料行以遞增順序排序，然後再依據 `LastName` 資料行以遞減順序排序。  
  
```sql
USE AdventureWorks2012;  
GO  
SELECT LastName, FirstName FROM Person.Person  
WHERE LastName LIKE 'R%'  
ORDER BY FirstName ASC, LastName DESC ;  
```  
  
###  <a name="specifying-a-collation"></a><a name="Collation"></a> 指定定序  
 下列範例示範 ORDER BY 子句中指定定序會如何變更傳回查詢結果的順序。 建立的資料表中包含一個資料行，這個資料行是透過使用不區分大小寫、不區分腔調字的定序來定義。 插入的值有各種不同的大小寫和腔調字。 因為 ORDER BY 子句中未指定定序，所以第一個查詢會在排序值時使用資料行的定序。 在第二個查詢中，ORDER BY 子句中指定區分大小寫、區分腔調字的定序，這變更傳回資料列的順序。  
  
```sql
USE tempdb;  
GO  
CREATE TABLE #t1 (name NVARCHAR(15) COLLATE Latin1_General_CI_AI)  
GO  
INSERT INTO #t1 VALUES(N'Sánchez'),(N'Sanchez'),(N'sánchez'),(N'sanchez');  
  
-- This query uses the collation specified for the column 'name' for sorting.  
SELECT name  
FROM #t1  
ORDER BY name;  
-- This query uses the collation specified in the ORDER BY clause for sorting.  
SELECT name  
FROM #t1  
ORDER BY name COLLATE Latin1_General_CS_AS;  
```  
  
###  <a name="specifying-a-conditional-order"></a><a name="Case"></a> 指定條件順序  
 下列範例在 ORDER BY 子句中使用 CASE 運算式，以根據給定的資料行值，有條件地決定資料列的排序次序。 在第一則範例中，系統會評估 `SalariedFlag` 資料表之 `HumanResources.Employee` 資料行的值。 將 `SalariedFlag` 設定為 1 的員工會以 `BusinessEntityID` 的遞減順序傳回。 將 `SalariedFlag` 設定為 0 的員工會以 `BusinessEntityID` 的遞增順序傳回。 在第二則範例中，結果集會依照資料行 `TerritoryName` 排序 (當資料行 `CountryRegionName` 等於 'United States' 時) 以及依照 `CountryRegionName` 排序 (針對所有其他資料列)。  
  
```sql
SELECT BusinessEntityID, SalariedFlag  
FROM HumanResources.Employee  
ORDER BY CASE SalariedFlag WHEN 1 THEN BusinessEntityID END DESC  
        ,CASE WHEN SalariedFlag = 0 THEN BusinessEntityID END;  
GO  
```  
  
```sql
SELECT BusinessEntityID, LastName, TerritoryName, CountryRegionName  
FROM Sales.vSalesPerson  
WHERE TerritoryName IS NOT NULL  
ORDER BY CASE CountryRegionName WHEN 'United States' THEN TerritoryName  
         ELSE CountryRegionName END;  
```  
  
###  <a name="using-order-by-in-a-ranking-function"></a><a name="Rank"></a> 在次序函數中使用 ORDER BY  
 下列範例在 ROW_NUMBER、RANK、DENSE_RANK 和 NTILE 次序函數中使用 ORDER BY 子句。  
  
```sql
USE AdventureWorks2012;  
GO  
SELECT p.FirstName, p.LastName  
    ,ROW_NUMBER() OVER (ORDER BY a.PostalCode) AS "Row Number"  
    ,RANK() OVER (ORDER BY a.PostalCode) AS "Rank"  
    ,DENSE_RANK() OVER (ORDER BY a.PostalCode) AS "Dense Rank"  
    ,NTILE(4) OVER (ORDER BY a.PostalCode) AS "Quartile"  
    ,s.SalesYTD, a.PostalCode  
FROM Sales.SalesPerson AS s   
    INNER JOIN Person.Person AS p   
        ON s.BusinessEntityID = p.BusinessEntityID  
    INNER JOIN Person.Address AS a   
        ON a.AddressID = p.BusinessEntityID  
WHERE TerritoryID IS NOT NULL AND SalesYTD <> 0;  
```  
  
###  <a name="limiting-the-number-of-rows-returned"></a><a name="Offset"></a> 限制傳回的資料列數目  
 下列範例使用 OFFSET 和 FETCH 來限制查詢所傳回的資料列數目。  
  
**適用於**：[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 及更新版本和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。  
  
#### <a name="a-specifying-integer-constants-for-offset-and-fetch-values"></a>A. 為 OFFSET 和 FETCH 值指定整數常數  
 下列範例會指定整數常數做為 OFFSET 和 FETCH 子句的值。 第一個查詢傳回依資料行 `DepartmentID` 排序的所有資料列。 比較這個查詢所傳回的結果與後面兩個查詢的結果。 下一個查詢使用子句 `OFFSET 5 ROWS` 略過前 5 個資料列，並傳回所有其餘的資料列。 最後查詢使用子句 `OFFSET 0 ROWS` 從第一個資料列開始，然後再使用 `FETCH NEXT 10 ROWS ONLY`，將傳回的資料列限制在已排序結果集內的 10 個資料列。  
  
```sql
USE AdventureWorks2012;  
GO  
-- Return all rows sorted by the column DepartmentID.  
SELECT DepartmentID, Name, GroupName  
FROM HumanResources.Department  
ORDER BY DepartmentID;  
  
-- Skip the first 5 rows from the sorted result set and return all remaining rows.  
SELECT DepartmentID, Name, GroupName  
FROM HumanResources.Department  
ORDER BY DepartmentID OFFSET 5 ROWS;  
  
-- Skip 0 rows and return only the first 10 rows from the sorted result set.  
SELECT DepartmentID, Name, GroupName  
FROM HumanResources.Department  
ORDER BY DepartmentID   
    OFFSET 0 ROWS  
    FETCH NEXT 10 ROWS ONLY;  
```  
  
#### <a name="b-specifying-variables-for-offset-and-fetch-values"></a>B. 為 OFFSET 和 FETCH 值指定變數  
 下列範例會宣告變數 `@RowsToSkip` 和 `@FetchRows`，並在 OFFSET 和 FETCH 子句中指定這些變數。  
  
```sql
USE AdventureWorks2012;  
GO  
-- Specifying variables for OFFSET and FETCH values    
DECLARE @RowsToSkip TINYINT = 2
      , @FetchRows TINYINT = 8;  
SELECT DepartmentID, Name, GroupName  
FROM HumanResources.Department  
ORDER BY DepartmentID ASC   
    OFFSET @RowsToSkip ROWS   
    FETCH NEXT @FetchRows ROWS ONLY;  
```  
  
#### <a name="c-specifying-expressions-for-offset-and-fetch-values"></a>C. 為 OFFSET 和 FETCH 值指定運算式  
 下列範例使用 `@StartingRowNumber - 1` 運算式來指定 OFFSET 值，並使用 `@EndingRowNumber - @StartingRowNumber + 1` 運算式來指定 FETCH 值。 此外，也指定 OPTIMIZE FOR 查詢提示。 在查詢進行編譯和最佳化時，此提示可用來提供區域變數的特定值。 只有在查詢最佳化期間，才使用這個值，在查詢執行期間，不使用這個值。 如需詳細資訊，請參閱[查詢提示 &#40;Transact-SQL&#41;](../../t-sql/queries/hints-transact-sql-query.md)。  
  
```sql
USE AdventureWorks2012;  
GO  
  
-- Specifying expressions for OFFSET and FETCH values      
DECLARE @StartingRowNumber TINYINT = 1  
      , @EndingRowNumber TINYINT = 8;  
SELECT DepartmentID, Name, GroupName  
FROM HumanResources.Department  
ORDER BY DepartmentID ASC   
    OFFSET @StartingRowNumber - 1 ROWS   
    FETCH NEXT @EndingRowNumber - @StartingRowNumber + 1 ROWS ONLY  
OPTION ( OPTIMIZE FOR (@StartingRowNumber = 1, @EndingRowNumber = 20) );  
```  
  
#### <a name="d-specifying-a-constant-scalar-subquery-for-offset-and-fetch-values"></a>D. 為 OFFSET 和 FETCH 值指定常數純量子查詢  
 下列範例使用常數純量子查詢來定義 FETCH 子句的值。 子查詢會從 `PageSize` 資料表中的 `dbo.AppSettings` 資料行傳回單一值。  
  
```sql
-- Specifying a constant scalar subquery  
USE AdventureWorks2012;  
GO  
CREATE TABLE dbo.AppSettings (AppSettingID INT NOT NULL, PageSize INT NOT NULL);  
GO  
INSERT INTO dbo.AppSettings VALUES(1, 10);  
GO  
DECLARE @StartingRowNumber TINYINT = 1;  
SELECT DepartmentID, Name, GroupName  
FROM HumanResources.Department  
ORDER BY DepartmentID ASC   
    OFFSET @StartingRowNumber ROWS   
    FETCH NEXT (SELECT PageSize FROM dbo.AppSettings WHERE AppSettingID = 1) ROWS ONLY;  
```  
  
#### <a name="e-running-multiple-queries-in-a-single-transaction"></a>E. 在單一交易中執行多個查詢  
 下列範例示範實作分頁方案的其中一種方法，可確保所有查詢要求中傳回穩定的結果。 在使用快照集隔離等級的單一交易中執行查詢，而且 ORDER BY 子句中指定的資料行可確保資料行唯一性。  
  
```sql
USE AdventureWorks2012;  
GO  
  
-- Ensure the database can support the snapshot isolation level set for the query.  
IF (SELECT snapshot_isolation_state FROM sys.databases WHERE name = N'AdventureWorks2012') = 0  
    ALTER DATABASE AdventureWorks2012 SET ALLOW_SNAPSHOT_ISOLATION ON;  
GO  
  
-- Set the transaction isolation level  to SNAPSHOT for this query.  
SET TRANSACTION ISOLATION LEVEL SNAPSHOT;  
GO  
  
-- Beginning the transaction.
BEGIN TRANSACTION;  
GO  
-- Declare and set the variables for the OFFSET and FETCH values.  
DECLARE @StartingRowNumber INT = 1  
      , @RowCountPerPage INT = 3;  
  
-- Create the condition to stop the transaction after all rows have been returned.  
WHILE (SELECT COUNT(*) FROM HumanResources.Department) >= @StartingRowNumber  
BEGIN  
  
-- Run the query until the stop condition is met.  
SELECT DepartmentID, Name, GroupName  
FROM HumanResources.Department  
ORDER BY DepartmentID ASC   
    OFFSET @StartingRowNumber - 1 ROWS   
    FETCH NEXT @RowCountPerPage ROWS ONLY;  
  
-- Increment @StartingRowNumber value.  
SET @StartingRowNumber = @StartingRowNumber + @RowCountPerPage;  
CONTINUE  
END;  
GO  
COMMIT TRANSACTION;  
GO  
```  
  
###  <a name="using-order-by-with-union-except-and-intersect"></a><a name="Union"></a> 搭配 UNION、EXCEPT 和 INTERSECT 使用 ORDER BY  
 當查詢使用 UNION、EXCEPT 或 INTERSECT 運算子時，ORDER BY 子句必須在陳述式結尾處指定，合併的查詢結果才會排序。 下列範例會傳回紅色或黃色的所有產品，並依據資料行 `ListPrice` 排序此組合清單。  
  
```sql
USE AdventureWorks2012;  
GO  
SELECT Name, Color, ListPrice  
FROM Production.Product  
WHERE Color = 'Red'  
-- ORDER BY cannot be specified here.  
UNION ALL  
SELECT Name, Color, ListPrice  
FROM Production.Product  
WHERE Color = 'Yellow'  
ORDER BY ListPrice ASC;  
```  
  
## <a name="examples-sssdwfull-and-sspdw"></a>範例：[!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 和 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
 下列範例示範依據數值的 `EmployeeKey` 資料行以遞增順序排序結果集。  
  
```sql
-- Uses AdventureWorks  
  
SELECT EmployeeKey, FirstName, LastName FROM DimEmployee  
WHERE LastName LIKE 'A%'  
ORDER BY EmployeeKey;  
```  
  
 下列範例依據數值的 `EmployeeKey` 資料行以遞減順序排序結果集。  
  
```sql
-- Uses AdventureWorks  
  
SELECT EmployeeKey, FirstName, LastName FROM DimEmployee  
WHERE LastName LIKE 'A%'  
ORDER BY EmployeeKey DESC;  
```  
  
 下列範例依據 `LastName` 資料行排序結果集。  
  
```sql
-- Uses AdventureWorks  
  
SELECT EmployeeKey, FirstName, LastName FROM DimEmployee  
WHERE LastName LIKE 'A%'  
ORDER BY LastName;  
```  
  
 下列範例依據兩個資料行排序。 此查詢會先依據 `FirstName` 資料行以遞增順序排序，然後再依據 `LastName` 資料行以遞減順序排序共同的 `FirstName` 值。  
  
```sql
-- Uses AdventureWorks  
  
SELECT EmployeeKey, FirstName, LastName FROM DimEmployee  
WHERE LastName LIKE 'A%'  
ORDER BY LastName, FirstName;  
```  
  
## <a name="see-also"></a>另請參閱  
 [運算式 &#40;Transact-SQL&#41;](../../t-sql/language-elements/expressions-transact-sql.md)   
 [SELECT &#40;Transact-SQL&#41;](../../t-sql/queries/select-transact-sql.md)   
 [FROM &#40;Transact-SQL&#41;](../../t-sql/queries/from-transact-sql.md)   
 [次序函式 &#40;Transact-SQL&#41;](../../t-sql/functions/ranking-functions-transact-sql.md)   
 [TOP &#40;Transact-SQL&#41;](../../t-sql/queries/top-transact-sql.md)   
 [查詢提示 &#40;Transact-SQL&#41;](../../t-sql/queries/hints-transact-sql-query.md)   
 [EXCEPT 和 INTERSECT &#40;Transact-SQL&#41;](../../t-sql/language-elements/set-operators-except-and-intersect-transact-sql.md)   
 [UNION &#40;Transact-SQL&#41;](../../t-sql/language-elements/set-operators-union-transact-sql.md)   
 [CASE &#40;Transact-SQL&#41;](../../t-sql/language-elements/case-transact-sql.md)  
  
  

