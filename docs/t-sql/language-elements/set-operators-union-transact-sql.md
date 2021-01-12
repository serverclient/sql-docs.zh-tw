---
description: Set 運算子 - UNION (Transact-SQL)
title: UNION (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 08/07/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- UNION
- UNION_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- UNION queries
- combining query results
- UNION operator [SQL Server]
ms.assetid: 607c296f-8a6a-49bc-975a-b8d0c0914df7
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 980dfe20df5c3909e8b35a6e5b460db2568b0fe3
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98097077"
---
# <a name="set-operators---union-transact-sql"></a>Set 運算子 - UNION (Transact-SQL)

[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

將兩個查詢的結果串連成單一結果集。 您可以控制結果集是否會包含重複的資料列：

- **UNION ALL**：包含重複項目。
- **UNION**：排除重複項目。

**UNION** 作業和 **[JOIN](../queries/from-transact-sql.md)** 並不相同：

- **UNION** 會串連來自兩個查詢的結果集。 但 **UNION** 不會從收集自兩個資料表的資料行建立個別的資料列。
- **JOIN** 會比較來自兩個資料表的資料行，以建立由來自兩個資料表的資料行所組成的結果資料列。
  
以下是利用 **UNION** 來組合兩個查詢之結果集的基本規則：  
  
-   在所有查詢中，資料行的數目和順序都必須相同。  
  
-   資料類型必須相容。  
  
![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
{ <query_specification> | ( <query_expression> ) }   
{ UNION [ ALL ]   
  { <query_specification> | ( <query_expression> ) } 
  [ ...n ] }
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
\<query_specification> | ( \<query_expression> ) 是查詢規格或查詢運算式，其會傳回資料以便與另一個查詢規格或查詢運算式中的資料結合。 UNION 作業中的資料行定義不必相同，但必須能夠透過隱含的轉換而相容。 當資料類型不同時，產生的資料類型取決於[資料類型優先順序](../../t-sql/data-types/data-type-precedence-transact-sql.md)的規則。 當類型相同，但有效位數、小數位數或長度不同時，結果取決於相同的運算式組合規則。 如需詳細資訊，請參閱[有效位數、小數位數和長度 (Transact-SQL)](../../t-sql/data-types/precision-scale-and-length-transact-sql.md)。  
  
**XML** 資料類型的資料行必須相等。 所有資料行都必須是 XML 結構描述類型，或不具類型。 如果具備類型，它們的類型必須是相同的 XML 結構描述集合。  
  
UNION  
指定組合多個結果集，以及當做單一結果集傳回。  
  
ALL  
將所有資料列納入結果中，包含重複的資料列。 若未指定，就會移除資料列複本。  
  
## <a name="examples"></a>範例  
  
### <a name="a-using-a-simple-union"></a>A. 使用簡單 UNION  
在下列範例中，結果集包括 `ProductModelID` 和 `Name` 資料表之 `ProductModel` 和 `Gloves` 資料行的內容。  
 
```sql
-- Uses AdventureWorks  
  
IF OBJECT_ID ('dbo.Gloves', 'U') IS NOT NULL  
DROP TABLE dbo.Gloves;  
GO  
-- Create Gloves table.  
SELECT ProductModelID, Name  
INTO dbo.Gloves  
FROM Production.ProductModel  
WHERE ProductModelID IN (3, 4);  
GO  
  
-- Here is the simple union.  
-- Uses AdventureWorks  
  
SELECT ProductModelID, Name  
FROM Production.ProductModel  
WHERE ProductModelID NOT IN (3, 4)  
UNION  
SELECT ProductModelID, Name  
FROM dbo.Gloves  
ORDER BY Name;  
GO  
```  
  
### <a name="b-using-select-into-with-union"></a>B. 搭配 UNION 使用 SELECT INTO  
在下列範例中，第二個 `SELECT` 陳述式中的 `INTO` 子句指定名為 `ProductResults` 的資料表，保留 `ProductModel` 和 `Gloves` 資料表所選資料行聯集的最終結果集。 `Gloves` 資料表是建立在第一個 `SELECT` 陳述式中。  
  
```sql
-- Uses AdventureWorks  
  
IF OBJECT_ID ('dbo.ProductResults', 'U') IS NOT NULL  
DROP TABLE dbo.ProductResults;  
GO  
IF OBJECT_ID ('dbo.Gloves', 'U') IS NOT NULL  
DROP TABLE dbo.Gloves;  
GO  
-- Create Gloves table.  
SELECT ProductModelID, Name  
INTO dbo.Gloves  
FROM Production.ProductModel  
WHERE ProductModelID IN (3, 4);  
GO  
  
-- Uses AdventureWorks  
  
SELECT ProductModelID, Name  
INTO dbo.ProductResults  
FROM Production.ProductModel  
WHERE ProductModelID NOT IN (3, 4)  
UNION  
SELECT ProductModelID, Name  
FROM dbo.Gloves;  
GO  
  
SELECT ProductModelID, Name   
FROM dbo.ProductResults;  
```  
  
### <a name="c-using-union-of-two-select-statements-with-order-by"></a>C. 搭配 ORDER BY 使用兩個 SELECT 陳述式的 UNION  
搭配 UNION 子句使用之特定參數的順序非常重要。 下列範例會顯示在兩個 `UNION` 陳述式中的輸出重新命名一個資料行時，`SELECT` 的正確和不正確用法。  
  
```sql
-- Uses AdventureWorks  
  
IF OBJECT_ID ('dbo.Gloves', 'U') IS NOT NULL  
DROP TABLE dbo.Gloves;  
GO  
-- Create Gloves table.  
SELECT ProductModelID, Name  
INTO dbo.Gloves  
FROM Production.ProductModel  
WHERE ProductModelID IN (3, 4);  
GO  
  
/* INCORRECT */  
-- Uses AdventureWorks  
  
SELECT ProductModelID, Name  
FROM Production.ProductModel  
WHERE ProductModelID NOT IN (3, 4)  
ORDER BY Name  
UNION  
SELECT ProductModelID, Name  
FROM dbo.Gloves;  
GO  
  
/* CORRECT */  
-- Uses AdventureWorks  
  
SELECT ProductModelID, Name  
FROM Production.ProductModel  
WHERE ProductModelID NOT IN (3, 4)  
UNION  
SELECT ProductModelID, Name  
FROM dbo.Gloves  
ORDER BY Name;  
GO  
```  
  
### <a name="d-using-union-of-three-select-statements-to-show-the-effects-of-all-and-parentheses"></a>D. 利用三個 SELECT 陳述式的 UNION 來顯示 ALL 和括號的作用  
下列範例會利用 `UNION` 來結合有 5 個相同資料列的三份資料表的結果。 第一個範例利用 `UNION ALL` 來顯示重複的記錄，以及傳回所有的 15 個資料列。 第二個範例利用不含 `UNION` 的 `ALL` 來刪除三個 `SELECT` 陳述式之組合結果中重複的資料列，並傳回 5 個資料列。  
  
第三個範例搭配第一個 `UNION` 來使用 `ALL`，並用括弧括住未使用 `ALL` 的第二個 `UNION`。 系統會先處理第二個 `UNION`，因為它在括弧中；由於未使用 `ALL` 選項，並已移除重複項目，因此會傳回 5 個資料列。 這 5 個資料列利用 `SELECT` 關鍵字，與第一個 `UNION ALL` 的結果結合起來。 此範例並不會移除這兩組 5 個資料列之間的重複項目。 最終結果有 10 個資料列。  
  
```sql
-- Uses AdventureWorks  
  
IF OBJECT_ID ('dbo.EmployeeOne', 'U') IS NOT NULL  
DROP TABLE dbo.EmployeeOne;  
GO  
IF OBJECT_ID ('dbo.EmployeeTwo', 'U') IS NOT NULL  
DROP TABLE dbo.EmployeeTwo;  
GO  
IF OBJECT_ID ('dbo.EmployeeThree', 'U') IS NOT NULL  
DROP TABLE dbo.EmployeeThree;  
GO  
  
SELECT pp.LastName, pp.FirstName, e.JobTitle   
INTO dbo.EmployeeOne  
FROM Person.Person AS pp JOIN HumanResources.Employee AS e  
ON e.BusinessEntityID = pp.BusinessEntityID  
WHERE LastName = 'Johnson';  
GO  
SELECT pp.LastName, pp.FirstName, e.JobTitle   
INTO dbo.EmployeeTwo  
FROM Person.Person AS pp JOIN HumanResources.Employee AS e  
ON e.BusinessEntityID = pp.BusinessEntityID  
WHERE LastName = 'Johnson';  
GO  
SELECT pp.LastName, pp.FirstName, e.JobTitle   
INTO dbo.EmployeeThree  
FROM Person.Person AS pp JOIN HumanResources.Employee AS e  
ON e.BusinessEntityID = pp.BusinessEntityID  
WHERE LastName = 'Johnson';  
GO  
-- Union ALL  
SELECT LastName, FirstName, JobTitle  
FROM dbo.EmployeeOne  
UNION ALL  
SELECT LastName, FirstName ,JobTitle  
FROM dbo.EmployeeTwo  
UNION ALL  
SELECT LastName, FirstName,JobTitle   
FROM dbo.EmployeeThree;  
GO  
  
SELECT LastName, FirstName,JobTitle  
FROM dbo.EmployeeOne  
UNION   
SELECT LastName, FirstName, JobTitle   
FROM dbo.EmployeeTwo  
UNION   
SELECT LastName, FirstName, JobTitle   
FROM dbo.EmployeeThree;  
GO  
  
SELECT LastName, FirstName,JobTitle   
FROM dbo.EmployeeOne  
UNION ALL  
(  
SELECT LastName, FirstName, JobTitle   
FROM dbo.EmployeeTwo  
UNION  
SELECT LastName, FirstName, JobTitle   
FROM dbo.EmployeeThree  
);  
GO  
  
```  
  
## <a name="examples-sssdwfull-and-sspdw"></a>範例：[!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 和 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
### <a name="e-using-a-simple-union"></a>E. 使用簡單 UNION  
在下列範例中，結果集包括 `FactInternetSales` 和 `DimCustomer` 資料表之 `CustomerKey` 資料行的內容。 因為沒有使用 ALL 關鍵字，所以重複項目會從結果中排除。  
  
```sql
-- Uses AdventureWorks  
  
SELECT CustomerKey   
FROM FactInternetSales    
UNION   
SELECT CustomerKey   
FROM DimCustomer   
ORDER BY CustomerKey;  
```  
  
### <a name="f-using-union-of-two-select-statements-with-order-by"></a>F. 搭配 ORDER BY 使用兩個 SELECT 陳述式的 UNION  
 當 UNION 陳述式中的任何 SELECT 陳述式包含 ORDER BY 子句時，該子句應置於所有 SELECT 陳述式之後。 下列範例在兩個 `SELECT` 陳述式 (其中有使用 ORDER BY 進行排序的資料行) 中顯示不正確的和正確的 `UNION` 用法。  
  
```sql
-- Uses AdventureWorks  
  
-- INCORRECT  
SELECT CustomerKey   
FROM FactInternetSales    
ORDER BY CustomerKey  
UNION   
SELECT CustomerKey   
FROM DimCustomer  
ORDER BY CustomerKey;  
  
-- CORRECT   
USE AdventureWorksPDW2012;  
  
SELECT CustomerKey   
FROM FactInternetSales    
UNION   
SELECT CustomerKey   
FROM DimCustomer   
ORDER BY CustomerKey;  
```  
  
### <a name="g-using-union-of-two-select-statements-with-where-and-order-by"></a>G. 使用含 WHERE 和 ORDER BY 之 SELECT 陳述式的 UNION  
以下範例說明在需要 WHERE 和 ORDER BY 的兩個 `SELECT` 陳述式中，不正確的和正確的 `UNION` 用法。  
  
```sql
-- Uses AdventureWorks  
  
-- INCORRECT   
SELECT CustomerKey   
FROM FactInternetSales   
WHERE CustomerKey >= 11000  
ORDER BY CustomerKey   
UNION   
SELECT CustomerKey   
FROM DimCustomer   
ORDER BY CustomerKey;  
  
-- CORRECT  
USE AdventureWorksPDW2012;  
  
SELECT CustomerKey   
FROM FactInternetSales   
WHERE CustomerKey >= 11000  
UNION   
SELECT CustomerKey   
FROM DimCustomer   
ORDER BY CustomerKey;  
```  
  
### <a name="h-using-union-of-three-select-statements-to-show-effects-of-all-and-parentheses"></a>H. 利用三個 SELECT 陳述式的 UNION 來顯示 ALL 和括號的作用效果  
下列範例使用 `UNION` 來合併 **同一個資料表** 的結果，以示範在使用 `UNION` 時 ALL 和括弧的效果。  
  
第一個範例使用 `UNION ALL` 顯示重複的記錄，並將來源資料表中的每個資料列都傳回三次。 第二個範例使用不含 `ALL` 的 `UNION`，以排除三個 `SELECT` 陳述式的結合結果中的重複資料列，並只傳回來源資料表中未重複的資料列。  
  
第三個範例搭配第一個 `UNION` 來使用 `ALL`，並用括弧括住未使用 `ALL` 的第二個 `UNION`。 系統會先處理第二個 `UNION`，因為它在括號中。 由於未使用 `ALL` 選項，並已移除重複項目，因此它只會傳回資料表中未重複的資料列。 這些資料列利用 `UNION ALL` 關鍵字，與第一個 `SELECT` 的結果合併。 此範例並不會移除這兩組之間的重複項目。  
  
```sql
-- Uses AdventureWorks  
  
SELECT CustomerKey, FirstName, LastName  
FROM DimCustomer  
UNION ALL   
SELECT CustomerKey, FirstName, LastName  
FROM DimCustomer  
UNION ALL   
SELECT CustomerKey, FirstName, LastName  
FROM DimCustomer;  
  
SELECT CustomerKey, FirstName, LastName  
FROM DimCustomer  
UNION   
SELECT CustomerKey, FirstName, LastName  
FROM DimCustomer  
UNION   
SELECT CustomerKey, FirstName, LastName  
FROM DimCustomer;  
  
SELECT CustomerKey, FirstName, LastName  
FROM DimCustomer  
UNION ALL  
(  
SELECT CustomerKey, FirstName, LastName  
FROM DimCustomer  
UNION   
SELECT CustomerKey, FirstName, LastName  
FROM DimCustomer  
);  
```  
  
## <a name="see-also"></a>另請參閱  
[SELECT (Transact-SQL)](../../t-sql/queries/select-transact-sql.md)   
[SELECT 範例 (Transact-SQL)](../../t-sql/queries/select-examples-transact-sql.md)  
