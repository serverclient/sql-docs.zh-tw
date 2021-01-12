---
title: COALESCE (Transact-SQL) | Microsoft Docs
description: COALESCE 的 Transact-SQL 參考，可傳回未評估為 Null 的第一個運算式值。
ms.date: 08/30/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- COALESCE
- COALESCE_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- expressions [SQL Server], nonnull
- COALESCE function
- first nonnull expressions [SQL Server]
- nonnull expressions
ms.assetid: fafc0dba-f8a8-4aad-9b7f-908e34b74d88
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 041f5b1b87a5d6ee92cd67fe17c6fd45f682dd95
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98093579"
---
# <a name="coalesce-transact-sql"></a>COALESCE (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

依序評估引數，並傳回一開始未評估為 `NULL` 之第一個運算式的目前值。 例如，`SELECT COALESCE(NULL, NULL, 'third_value', 'fourth_value');` 會傳回第三個值，因為第三個值是第一個非 Null 的值。 
  
![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
COALESCE ( expression [ ,...n ] )   
```  
  
## <a name="arguments"></a>引數  
_expression_  
這是任何類型的[運算式](../../t-sql/language-elements/expressions-transact-sql.md)。  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="return-types"></a>傳回型別
傳回具有最高資料類型優先順序的 _expression_ 資料類型。 如果所有運算式都不可為 Null，結果的類型也是不可為 Null。  
  
## <a name="remarks"></a>備註  
如果所有引數均為 `NULL`，`COALESCE` 就會傳回 `NULL`。 至少其中一個 Null 值必須是 `NULL` 類型。  
  
## <a name="comparing-coalesce-and-case"></a>比較 COALESCE 和 CASE  
`COALESCE` 運算式是 `CASE` 運算式的語法捷徑。  也就是說，查詢最佳化工具會將程式碼 `COALESCE`(_expression1_, _...n_) 重寫為下列 `CASE` 運算式：  
  
```sql  
CASE  
WHEN (expression1 IS NOT NULL) THEN expression1  
WHEN (expression2 IS NOT NULL) THEN expression2  
...  
ELSE expressionN  
END  
```  
  
如此，會多次評估輸入值 (_expression1_、_expression2_、_expressionN_ 等等)。 包含子查詢的值運算式會視為不具決定性，且會評估子查詢兩次。 此結果符合 SQL 標準。 不論是哪一種情況，第一次評估和後續評估之間都會傳回不同的結果。  
  
例如，執行程式碼 `COALESCE((subquery), 1)` 時，會評估子查詢兩次。 因此，您會根據查詢的隔離等級取得不同的結果。 例如，程式碼在多使用者環境的 `READ COMMITTED` 隔離等級下，會傳回 `NULL`。 為了確保會傳回穩定的結果，請使用 `SNAPSHOT ISOLATION` 隔離等級，或以 `ISNULL` 函數取代 `COALESCE`。 或者，您可以重寫查詢，將子查詢推送至子選擇，如下列範例所示：  
  
```sql  
SELECT CASE WHEN x IS NOT NULL THEN x ELSE 1 END  
FROM  
(  
SELECT (SELECT Nullable FROM Demo WHERE SomeCol = 1) AS x  
) AS T;  
  
```  
  
## <a name="comparing-coalesce-and-isnull"></a>比較 COALESCE 和 ISNULL  
`ISNULL` 函數和 `COALESCE` 運算式具有相似的目的，但運作方式可能不同。  
  
1.  因為 `ISNULL` 是函式，所以只會評估一次。  如上所述，可能會評估多次 `COALESCE` 運算式的輸入值。  
  
2.  結果運算式所決定的資料類型會不同。 `ISNULL` 使用第一個參數的資料類型，而 `COALESCE` 會遵循 `CASE` 運算式規則，並傳回具有最高優先順序之值的資料類型。  
  
3.  針對 `ISNULL` 和 `COALESCE`，結果運算式的可 NULL 性不同。 `ISNULL` 傳回值一律視為不可為 NULL (假設傳回值是不可為 Null 的值)。 相反地，具有非 Null 參數的 `COALESCE` 會被視為 `NULL`。 因此，運算式 `ISNULL(NULL, 1)` 和 `COALESCE(NULL, 1)` 雖然相等，卻有不同的可為 Null 值。 當您在計算資料行中使用這些運算式、建立索引鍵條件約束，或使純量 UDF 的傳回值具確定性時，這些值會產生差異，以便編製索引，如下列範例所示：  
  
    ```sql  
    USE tempdb;  
    GO  
    -- This statement fails because the PRIMARY KEY cannot accept NULL values  
    -- and the nullability of the COALESCE expression for col2   
    -- evaluates to NULL.  
    CREATE TABLE #Demo   
    (   
      col1 INTEGER NULL,   
      col2 AS COALESCE(col1, 0) PRIMARY KEY,   
      col3 AS ISNULL(col1, 0)   
    );   
  
    -- This statement succeeds because the nullability of the   
    -- ISNULL function evaluates AS NOT NULL.  
  
    CREATE TABLE #Demo   
    (   
      col1 INTEGER NULL,   
      col2 AS COALESCE(col1, 0),   
      col3 AS ISNULL(col1, 0) PRIMARY KEY   
    );  
    ```  
  
4.  適用於 `ISNULL` 和 `COALESCE` 的驗證也不一樣。 例如，`ISNULL` 的 `NULL` 值會轉換為 **int**；而針對 `COALESCE`，您卻必須提供資料類型。  
  
5.  `ISNULL` 只接受兩個參數。 相較之下，`COALESCE` 接受數目不定的參數。  
  
## <a name="examples"></a>範例  
  
### <a name="a-running-a-simple-example"></a>A. 執行簡單範例  
下列範例示範  `COALESCE` 如何從具有非  Null  值的第一個資料行選取資料。 這個範例會使用 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 資料庫。  
  
```sql  
SELECT Name, Class, Color, ProductNumber,  
COALESCE(Class, Color, ProductNumber) AS FirstNotNull  
FROM Production.Product;  
```  
  
### <a name="b-running-a-complex-example"></a>B. 執行複雜範例  
在下列範例中，`wages` 資料表有三個含員工年薪 (時薪、月薪加上分紅) 相關資訊的資料行。 不過，員工只會收到其中一種款項。 若要算出支付給所有員工的總金額，請使用 `COALESCE`，只接收 `hourly_wage`、`salary` 和 `commission` 中的非 Null 值。  
  
```sql  
SET NOCOUNT ON;  
GO  
USE tempdb;  
IF OBJECT_ID('dbo.wages') IS NOT NULL  
    DROP TABLE wages;  
GO  
CREATE TABLE dbo.wages  
(  
    emp_id        TINYINT   IDENTITY,  
    hourly_wage   DECIMAL   NULL,  
    salary        DECIMAL   NULL,  
    commission    DECIMAL   NULL,  
    num_sales     TINYINT   NULL  
);  
GO  
INSERT dbo.wages (hourly_wage, salary, commission, num_sales)  
VALUES  
    (10.00, NULL, NULL, NULL),  
    (20.00, NULL, NULL, NULL),  
    (30.00, NULL, NULL, NULL),  
    (40.00, NULL, NULL, NULL),  
    (NULL, 10000.00, NULL, NULL),  
    (NULL, 20000.00, NULL, NULL),  
    (NULL, 30000.00, NULL, NULL),  
    (NULL, 40000.00, NULL, NULL),  
    (NULL, NULL, 15000, 3),  
    (NULL, NULL, 25000, 2),  
    (NULL, NULL, 20000, 6),  
    (NULL, NULL, 14000, 4);  
GO  
SET NOCOUNT OFF;  
GO  
SELECT CAST(COALESCE(hourly_wage * 40 * 52,   
   salary,   
   commission * num_sales) AS money) AS 'Total Salary'   
FROM dbo.wages  
ORDER BY 'Total Salary';  
GO  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```   
Total Salary  
------------  
10000.00  
20000.00  
20800.00  
30000.00  
40000.00  
41600.00  
45000.00  
50000.00  
56000.00  
62400.00  
83200.00  
120000.00  
  
(12 row(s) affected)
```  
  
### <a name="c-simple-example"></a>C.簡單範例  
下列範例示範 `COALESCE` 如何從具有非 Null 值的第一個資料行選取資料。 此範例假設 `Products` 資料表包含此資料：  
  
```  
Name         Color      ProductNumber  
------------ ---------- -------------  
Socks, Mens  NULL       PN1278  
Socks, Mens  Blue       PN1965  
NULL         White      PN9876
```  
 
我們接著執行下列 COALESCE 查詢：  
  
```sql  
SELECT Name, Color, ProductNumber, COALESCE(Color, ProductNumber) AS FirstNotNull   
FROM Products ;  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
Name         Color      ProductNumber  FirstNotNull  
------------ ---------- -------------  ------------  
Socks, Mens  NULL       PN1278         PN1278  
Socks, Mens  Blue       PN1965         Blue  
NULL         White      PN9876         White
```  
  
請注意，在第一個資料列中，`FirstNotNull` 值是 `PN1278`，而非 `Socks, Mens`。 這個值會這樣，是因為在範例中，未將 `Name` 資料行指定為 `COALESCE` 的參數。  
  
### <a name="d-complex-example"></a>D.複雜範例  
下列範例會使用 `COALESCE` 來比較三個資料行中的值，並且只傳回在資料行中找到的非 Null 值。  
  
```sql  
CREATE TABLE dbo.wages  
(  
    emp_id        TINYINT   NULL,  
    hourly_wage   DECIMAL   NULL,  
    salary        DECIMAL   NULL,  
    commission    DECIMAL   NULL,  
    num_sales     TINYINT   NULL  
);  
INSERT INTO dbo.wages (emp_id, hourly_wage, salary, commission, num_sales)  
VALUES (1, 10.00, NULL, NULL, NULL);  
  
INSERT INTO dbo.wages (emp_id, hourly_wage, salary, commission, num_sales)  
VALUES (2, 20.00, NULL, NULL, NULL);  
  
INSERT INTO dbo.wages (emp_id, hourly_wage, salary, commission, num_sales)  
VALUES (3, 30.00, NULL, NULL, NULL);  
  
INSERT INTO dbo.wages (emp_id, hourly_wage, salary, commission, num_sales)  
VALUES (4, 40.00, NULL, NULL, NULL);  
  
INSERT INTO dbo.wages (emp_id, hourly_wage, salary, commission, num_sales)  
VALUES (5, NULL, 10000.00, NULL, NULL);  
  
INSERT INTO dbo.wages (emp_id, hourly_wage, salary, commission, num_sales)  
VALUES (6, NULL, 20000.00, NULL, NULL);  
  
INSERT INTO dbo.wages (emp_id, hourly_wage, salary, commission, num_sales)  
VALUES (7, NULL, 30000.00, NULL, NULL);  
  
INSERT INTO dbo.wages (emp_id, hourly_wage, salary, commission, num_sales)  
VALUES (8, NULL, 40000.00, NULL, NULL);  
  
INSERT INTO dbo.wages (emp_id, hourly_wage, salary, commission, num_sales)  
VALUES (9, NULL, NULL, 15000, 3);  
  
INSERT INTO dbo.wages (emp_id, hourly_wage, salary, commission, num_sales)  
VALUES (10,NULL, NULL, 25000, 2);  
  
INSERT INTO dbo.wages (emp_id, hourly_wage, salary, commission, num_sales)  
VALUES (11, NULL, NULL, 20000, 6);  
  
INSERT INTO dbo.wages (emp_id, hourly_wage, salary, commission, num_sales)  
VALUES (12, NULL, NULL, 14000, 4);  
  
SELECT CAST(COALESCE(hourly_wage * 40 * 52,   
   salary,   
   commission * num_sales) AS DECIMAL(10,2)) AS TotalSalary   
FROM dbo.wages  
ORDER BY TotalSalary;  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```
Total Salary  
------------  
10000.00  
20000.00  
20800.00  
30000.00  
40000.00  
41600.00  
45000.00  
50000.00  
56000.00  
62400.00  
83200.00  
120000.00
```  
  
## <a name="see-also"></a>另請參閱  
[ISNULL &#40;Transact-SQL&#41;](../../t-sql/functions/isnull-transact-sql.md)   
[CASE &#40;Transact-SQL&#41;](../../t-sql/language-elements/case-transact-sql.md)  
  
  
