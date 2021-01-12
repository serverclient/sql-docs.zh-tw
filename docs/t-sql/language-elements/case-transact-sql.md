---
title: CASE (Transact-SQL)
description: CASE 運算式的 Transact-SQL 參考。 CASE 會評估要傳回特定結果的條件清單。
ms.date: 06/28/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- CASE_TSQL
- CASE
dev_langs:
- TSQL
helpviewer_keywords:
- CASE expression [Transact-SQL]
- simple CASE expression
- comparing expressions
- searched CASE expression
ms.assetid: 658039ec-8dc2-4251-bc82-30ea23708cee
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 0b845dd7b9ca9fcd78ccef5f9ebe55ac2bae7749
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98085832"
---
# <a name="case-transact-sql"></a>CASE (Transact-SQL)

[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

評估一份條件清單，並傳回多個可能的結果運算式之一。  
  
 CASE 運算式有兩種格式：  
  
-   簡單的 CASE 運算式會比較運算式和一組簡單運算式來得出結果。  
  
-   搜尋 CASE 運算會評估一組布林運算式來得出結果。  
  
 兩種格式都支援選用的 ELSE 引數。  
  
 CASE 可以用在允許有效運算式的任何陳述式或子句中。 例如，您可以在 SELECT、UPDATE、DELETE 和 SET 之類的陳述式，以及 select_list、IN、WHERE、ORDER BY 和 HAVING 之類的子句中使用 CASE。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
-- Syntax for SQL Server and Azure SQL Database  
  
--Simple CASE expression:   
CASE input_expression   
     WHEN when_expression THEN result_expression [ ...n ]   
     [ ELSE else_result_expression ]   
END   

--Searched CASE expression:  
CASE  
     WHEN Boolean_expression THEN result_expression [ ...n ]   
     [ ELSE else_result_expression ]   
END  
```  
  
```syntaxsql
-- Syntax for Azure Synapse Analytics and Parallel Data Warehouse  
  
CASE  
     WHEN when_expression THEN result_expression [ ...n ]   
     [ ELSE else_result_expression ]   
END
```

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數  
 *input_expression*  
 這是使用簡單的 CASE 格式時，所評估的運算式。 *input_expression* 是任何有效的 [運算式](../../t-sql/language-elements/expressions-transact-sql.md)。  
  
 WHEN *when_expression*  
 這是使用簡單的 CASE 格式時，要與 *input_expression* 進行比較的簡單運算式。 *when_expression* 是任何有效的運算式。 *input_expression* 和每個 *when_expression* 的資料類型都必須相同，或必須能夠進行隱含轉換。  
  
 THEN *result_expression*  
 這是當 *input_expression* 等於 *when_expression* 評估為 TRUE，或 *Boolean_expression* 評估為 TRUE 時所傳回的運算式。 *result expression* 是任何有效的 [運算式](../../t-sql/language-elements/expressions-transact-sql.md)。  
  
 ELSE *else_result_expression*  
 這是沒有比較運算的評估結果是 TRUE 時，所傳回的運算式。 如果省略這個引數，且沒有比較運算得出 TRUE，CASE 就會傳回 NULL。 *else_result_expression* 是任何有效的運算式。 *else_result_expression* 和任何 *result_expression* 的資料類型都必須相同，或必須能夠進行隱含轉換。  
  
 WHEN *Boolean_expression*  
 這是使用搜尋的 CASE 格式時，所評估的布林運算式。 *Boolean_expression* 是任何有效的布林值運算式。  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="return-types"></a>傳回型別
 從 *result_expressions* 和選擇性 *else_result_expression* 的類型集傳回優先順序最高的類型。 如需詳細資訊，請參閱[資料類型優先順序 &#40;Transact-SQL&#41;](../../t-sql/data-types/data-type-precedence-transact-sql.md)。  
  
### <a name="return-values"></a>傳回值  
 **簡單的 CASE 運算式：**  
  
 簡單的 CASE 運算式會透過比較第一個運算式與每個 WHEN 子句中的運算式是否相等來運算。 如果這些運算式相等，將會傳回 THEN 子句中的運算式。  
  
-   僅允許相等檢查。  
  
-   依指定的順序來評估每個 WHEN 子句的 input_expression = when_expression。  
  
-   傳回第一個評估為 TRUE 之 *input_expression* = *when_expression* 的 *result_expression*。  
  
-   如果沒有任何 *input_expression* = *when_expression* 評估為 TRUE，若指定了 ELSE 子句，[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 就會傳回 *else_result_expression*，若未指定 ELSE 子句，則會傳回 NULL 值。  
  
 **搜尋的 CASE 運算式：**  
  
-   依指定的順序來評估每個 WHEN 子句的 *Boolean_expression*。  
  
-   傳回第一個評估為 TRUE 之 *Boolean_expression* 的 *result_expression*。  
  
-   如果沒有任何 *Boolean_expression* 評估為 TRUE，若指定了 ELSE 子句，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 就會傳回 *else_result_expression*，若未指定 ELSE 子句，則會傳回 NULL 值。  
  
## <a name="remarks"></a>備註  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 在 CASE 運算式中只允許 10 層的巢狀層級。  
  
 CASE 運算式無法用於控制 Transact-SQL 陳述式、陳述式區塊、使用者定義函數以及預存程序的執行流程。 如需流程控制方法的清單，請參閱[流程控制語言 &#40;Transact-SQL&#41;](~/t-sql/language-elements/control-of-flow.md)。  
  
 CASE 運算式會依序評估其條件，並在滿足其條件的第一個條件時停止。 在某些情況下，運算式會在 CASE 運算式收到運算式的結果作為其輸入之前進行評估。 評估這些運算式是否可能時發生錯誤。 針對 CASE 運算式，會先評估出現在 WHEN 引數中的彙總運算式，再將其提供給 CASE 運算式。 例如，下列查詢會在產生 MAX 彙總的值時，產生除以零的錯誤。 這個情況會在評估 CASE 運算式之前發生。  
  
```sql  
WITH Data (value) AS   
(   
SELECT 0   
UNION ALL   
SELECT 1   
)   
SELECT   
   CASE   
      WHEN MIN(value) <= 0 THEN 0   
      WHEN MAX(1/value) >= 100 THEN 1   
   END   
FROM Data ;  
```  
  
 針對純量運算式 (包括傳回純量的非相互關聯子查詢，而非針對彙總運算式)，您應該僅相依於 WHEN 條件的評估順序。  
  
## <a name="examples"></a>範例  
  
### <a name="a-using-a-select-statement-with-a-simple-case-expression"></a>A. 使用 SELECT 陳述式搭配簡單的 CASE 運算式  
 在 `SELECT` 陳述式內，只允許相等檢查使用簡單的 `CASE` 運算式，不能進行任何其他比較。 下列範例利用 `CASE` 運算式來變更產品線類別目錄的顯示方式，使它們更容易了解。  
  
```sql  
USE AdventureWorks2012;  
GO  
SELECT   ProductNumber, Category =  
      CASE ProductLine  
         WHEN 'R' THEN 'Road'  
         WHEN 'M' THEN 'Mountain'  
         WHEN 'T' THEN 'Touring'  
         WHEN 'S' THEN 'Other sale items'  
         ELSE 'Not for sale'  
      END,  
   Name  
FROM Production.Product  
ORDER BY ProductNumber;  
GO  
```  
  
### <a name="b-using-a-select-statement-with-a-searched-case-expression"></a>B. 使用 SELECT 陳述式搭配搜尋的 CASE 運算式  
 在 `SELECT` 陳述式內，搜尋的 `CASE` 運算式允許以比較值為基礎來取代結果集中的值。 下列範例以產品的價格範圍為基礎，將標價顯示為文字註解。  
  
```sql  
USE AdventureWorks2012;  
GO  
SELECT   ProductNumber, Name, "Price Range" =   
      CASE   
         WHEN ListPrice =  0 THEN 'Mfg item - not for resale'  
         WHEN ListPrice < 50 THEN 'Under $50'  
         WHEN ListPrice >= 50 and ListPrice < 250 THEN 'Under $250'  
         WHEN ListPrice >= 250 and ListPrice < 1000 THEN 'Under $1000'  
         ELSE 'Over $1000'  
      END  
FROM Production.Product  
ORDER BY ProductNumber ;  
GO  
```  
  
### <a name="c-using-case-in-an-order-by-clause"></a>C. 在 ORDER BY 子句中使用 CASE  
 下列範例在 ORDER BY 子句中使用 CASE 運算式，根據給定的資料行值，決定資料列的排序次序。 在第一則範例中，系統會評估 `SalariedFlag` 資料表之 `HumanResources.Employee` 資料行的值。 將 `SalariedFlag` 設定為 1 的員工會以 `BusinessEntityID` 的遞減順序傳回。 將 `SalariedFlag` 設定為 0 的員工會以 `BusinessEntityID` 的遞增順序傳回。 在第二則範例中，結果集會依照資料行 `TerritoryName` 排序 (當資料行 `CountryRegionName` 等於 'United States' 時) 以及依照 `CountryRegionName` 排序 (針對所有其他資料列)。  
  
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
  
### <a name="d-using-case-in-an-update-statement"></a>D. 在 UPDATE 陳述式中使用 CASE  
 下列範例在 UPDATE 陳述式中使用 CASE 運算式來決定 `VacationHours` 設定為 0 時，針對員工之 `SalariedFlag` 資料行設定的值。 從 `VacationHours` 減去 10 小時變成負值時，`VacationHours` 會加上 40 小時，否則 `VacationHours` 會加上 20 小時。 OUTPUT 子句用於顯示假期值之前和之後。  
  
```sql  
USE AdventureWorks2012;  
GO  
UPDATE HumanResources.Employee  
SET VacationHours =   
    ( CASE  
         WHEN ((VacationHours - 10.00) < 0) THEN VacationHours + 40  
         ELSE (VacationHours + 20.00)  
       END  
    )  
OUTPUT Deleted.BusinessEntityID, Deleted.VacationHours AS BeforeValue,   
       Inserted.VacationHours AS AfterValue  
WHERE SalariedFlag = 0;   
```  
  
### <a name="e-using-case-in-a-set-statement"></a>E. 在 SET 陳述式中使用 CASE  
 下列範例會在資料表值函式 `dbo.GetContactInfo` 的 SET 陳述式中使用 CASE 運算式。 在 [!INCLUDE[ssSampleDBobject](../../includes/sssampledbobject-md.md)] 資料庫中，與人員相關的所有資料都會儲存在 `Person.Person` 資料表中。 例如，該人員可能是員工、廠商代表或客戶。 此函數會傳回給定之 `BusinessEntityID` 的姓名，以及該人員的連絡類型。SET 陳述式中的 CASE 運算式會根據 `Employee`、`Vendor` 或 `Customer` 資料表中的 `BusinessEntityID` 資料行是否存在，來決定要針對 `ContactType` 資料行顯示的值。  
  
```sql  
USE AdventureWorks2012;  
GO  
CREATE FUNCTION dbo.GetContactInformation(@BusinessEntityID INT)  
    RETURNS @retContactInformation TABLE   
(  
BusinessEntityID INT NOT NULL,  
FirstName NVARCHAR(50) NULL,  
LastName NVARCHAR(50) NULL,  
ContactType NVARCHAR(50) NULL,  
    PRIMARY KEY CLUSTERED (BusinessEntityID ASC)  
)   
AS   
-- Returns the first name, last name and contact type for the specified contact.  
BEGIN  
    DECLARE   
        @FirstName NVARCHAR(50),   
        @LastName NVARCHAR(50),   
        @ContactType NVARCHAR(50);  
  
    -- Get common contact information  
    SELECT   
        @BusinessEntityID = BusinessEntityID,   
@FirstName = FirstName,   
        @LastName = LastName  
    FROM Person.Person   
    WHERE BusinessEntityID = @BusinessEntityID;  
  
    SET @ContactType =   
        CASE   
            -- Check for employee  
            WHEN EXISTS(SELECT * FROM HumanResources.Employee AS e   
                WHERE e.BusinessEntityID = @BusinessEntityID)   
                THEN 'Employee'  
  
            -- Check for vendor  
            WHEN EXISTS(SELECT * FROM Person.BusinessEntityContact AS bec  
                WHERE bec.BusinessEntityID = @BusinessEntityID)   
                THEN 'Vendor'  
  
            -- Check for store  
            WHEN EXISTS(SELECT * FROM Purchasing.Vendor AS v            
                WHERE v.BusinessEntityID = @BusinessEntityID)   
                THEN 'Store Contact'  
  
            -- Check for individual consumer  
            WHEN EXISTS(SELECT * FROM Sales.Customer AS c   
                WHERE c.PersonID = @BusinessEntityID)   
                THEN 'Consumer'  
        END;  
  
    -- Return the information to the caller  
    IF @BusinessEntityID IS NOT NULL   
    BEGIN  
        INSERT @retContactInformation  
        SELECT @BusinessEntityID, @FirstName, @LastName, @ContactType;  
    END;  
  
    RETURN;  
END;  
GO  
  
SELECT BusinessEntityID, FirstName, LastName, ContactType  
FROM dbo.GetContactInformation(2200);  
GO  
SELECT BusinessEntityID, FirstName, LastName, ContactType  
FROM dbo.GetContactInformation(5);  
```  
  
### <a name="f-using-case-in-a-having-clause"></a>F. 在 HAVING 子句中使用 CASE  
 下列範例會在 HAVING 子句中使用 CASE 運算式來限制 SELECT 陳述式所傳回的資料列。 此陳述式會傳回 `HumanResources.Employee` 資料表中每個職稱的最高時薪。 HAVING 子句會將職稱限制為薪水上限大於 40 美金之男士所持有的職稱，以及薪水上限大於 42 美金之女士所持有的職稱。  
  
```sql 
USE AdventureWorks2012;  
GO  
SELECT JobTitle, MAX(ph1.Rate)AS MaximumRate  
FROM HumanResources.Employee AS e  
JOIN HumanResources.EmployeePayHistory AS ph1 ON e.BusinessEntityID = ph1.BusinessEntityID  
GROUP BY JobTitle  
HAVING (MAX(CASE WHEN Gender = 'M'   
        THEN ph1.Rate   
        ELSE NULL END) > 40.00  
     OR MAX(CASE WHEN Gender  = 'F'   
        THEN ph1.Rate    
        ELSE NULL END) > 42.00)  
ORDER BY MaximumRate DESC;  
```  
  
## <a name="examples-sssdwfull-and-sspdw"></a>範例：[!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 和 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
### <a name="g-using-a-select-statement-with-a-case-expression"></a>G. 使用 SELECT 陳述式搭配 CASE 運算式  
 在 SELECT 陳述式內，CASE 運算式允許以比較值為基礎來取代結果集中的值。 下列範例使用 CASE 運算式來變更產品線類別目錄的顯示方式，使它們更容易被了解。 若值並不存在，即會顯示 "Not for sale' 的字樣。  
  
```sql 
-- Uses AdventureWorks  
  
SELECT   ProductAlternateKey, Category =  
      CASE ProductLine  
         WHEN 'R' THEN 'Road'  
         WHEN 'M' THEN 'Mountain'  
         WHEN 'T' THEN 'Touring'  
         WHEN 'S' THEN 'Other sale items'  
         ELSE 'Not for sale'  
      END,  
   EnglishProductName  
FROM dbo.DimProduct  
ORDER BY ProductKey;  
```  
  
### <a name="h-using-case-in-an-update-statement"></a>H. 在 UPDATE 陳述式中使用 CASE  
 下列範例在 UPDATE 陳述式中使用 CASE 運算式來決定 `VacationHours` 設定為 0 時，針對員工之 `SalariedFlag` 資料行設定的值。 從 `VacationHours` 減去 10 小時變成負值時，`VacationHours` 會加上 40 小時，否則 `VacationHours` 會加上 20 小時。  
  
```sql 
-- Uses AdventureWorks   
  
UPDATE dbo.DimEmployee  
SET VacationHours =   
    ( CASE  
         WHEN ((VacationHours - 10.00) < 0) THEN VacationHours + 40  
         ELSE (VacationHours + 20.00)   
       END  
    )   
WHERE SalariedFlag = 0;  
```  
  
## <a name="see-also"></a>另請參閱  
 [運算式 &#40;Transact-SQL&#41;](../../t-sql/language-elements/expressions-transact-sql.md)   
 [SELECT &#40;Transact-SQL&#41;](../../t-sql/queries/select-transact-sql.md)   
 [COALESCE &#40;Transact-SQL&#41;](../../t-sql/language-elements/coalesce-transact-sql.md)   
 [IIF &#40;Transact-SQL&#41;](../../t-sql/functions/logical-functions-iif-transact-sql.md)   
 [CHOOSE &#40;Transact-SQL&#41;](../../t-sql/functions/logical-functions-choose-transact-sql.md)  
  
  



