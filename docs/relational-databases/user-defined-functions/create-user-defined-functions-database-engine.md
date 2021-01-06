---
description: 建立使用者定義函數 (Database Engine)
title: 建立使用者定義函式 (Database Engine) | Microsoft Docs
ms.custom: ''
ms.date: 11/09/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: ''
ms.topic: conceptual
helpviewer_keywords:
- SCHEMABINDING clause
- schema-bound functions [SQL Server]
- user-defined functions [SQL Server], creating
- CREATE FUNCTION statement
- valid statements [SQL Server]
- UDF
- TVF
ms.assetid: f0d5dd10-73fd-4e05-9177-07f56552bdf7
author: rothja
ms.author: jroth
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 3303df6ac71b7a6c92d4a1c65a8b7b4b3229cd6e
ms.sourcegitcommit: 4419e99d77ee2c73f9da1559c7944f7702f2de30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/23/2020
ms.locfileid: "97743675"
---
# <a name="create-user-defined-functions-database-engine"></a>建立使用者定義函數 (Database Engine)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]
  本主題描述如何使用 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ，在 [!INCLUDE[tsql](../../includes/tsql-md.md)]中建立使用者定義函數 (UDF)。  
  
##  <a name="before-you-begin"></a><a name="BeforeYouBegin"></a> 開始之前  
  
###  <a name="limitations-and-restrictions"></a><a name="Restrictions"></a> 限制事項  
  
-   使用者定義函數不能用來執行修改資料庫狀態的動作。  
  
-   使用者定義函式不得包含具有資料表作為其目標的 `OUTPUT INTO` 子句。  
  
-   使用者定義函數無法傳回多個結果集。 如果您需要傳回多個結果集，請使用預存程序。  
  
-   使用者定義函數中限制錯誤處理。 UDF 不支援 `TRY...CATCH`、`@ERROR` 或 `RAISERROR`。  
  
-   使用者定義函數無法呼叫預存程序，但是可以呼叫擴充預存程序。  
  
-   使用者定義函數無法利用動態 SQL 或暫存資料表。 允許使用資料表變數。  
  
-   使用者定義函式中不允許使用 `SET` 陳述式。  
  
-   不允許使用 `FOR XML` 子句。  
  
-   使用者定義函數可以具有巢狀結構；也就是說，某個使用者定義函數可以呼叫另一個使用者定義函數。 被呼叫的函數開始執行時，巢狀層級會遞增；被呼叫的函數完成執行時，巢狀層級會遞減。 使用者定義函數所建立的巢狀結構最多可以有 32 個層級。 超過巢狀層級上限會導致整個呼叫函數鏈結失敗。 依照 32 個層級巢狀限制，Transact-SQL 使用者定義函數之 Managed 程式碼的任何參考都算是一個層級。 從 Managed 程式碼內叫用的方法，不列入這項限制。  
  
-   下列 Service Broker 陳述式 **不能包含** 在 [!INCLUDE[tsql](../../includes/tsql-md.md)] 使用者定義函式的定義中：  
  
    -   `BEGIN DIALOG CONVERSATION`  
  
    -   `END CONVERSATION`  
  
    -   `GET CONVERSATION GROUP`  
  
    -   `MOVE CONVERSATION`  
  
    -   `RECEIVE`  
  
    -   `SEND`  
  
###  <a name="permissions"></a><a name="Security"></a> 權限 

需要資料庫中的 `CREATE FUNCTION` 權限，以及建立此函式所在結構描述上的 `ALTER` 權限。 如果此函式指定使用者定義型別，則需要該型別的 `EXECUTE` 權限。  
  
##  <a name="scalar-functions"></a><a name="Scalar"></a> 純量函數  
 下列範例會在 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 資料庫中建立多重陳述式 **純量函式 (純量 UDF)**。 這個函數使用了一個輸入值 `ProductID`，並傳回單一資料值，也就是指定產品的彙總存貨量。  
  
```sql  
IF OBJECT_ID (N'dbo.ufnGetInventoryStock', N'FN') IS NOT NULL  
    DROP FUNCTION ufnGetInventoryStock;  
GO  
CREATE FUNCTION dbo.ufnGetInventoryStock(@ProductID int)  
RETURNS int   
AS   
-- Returns the stock level for the product.  
BEGIN  
    DECLARE @ret int;  
    SELECT @ret = SUM(p.Quantity)   
    FROM Production.ProductInventory p   
    WHERE p.ProductID = @ProductID   
        AND p.LocationID = '6';  
     IF (@ret IS NULL)   
        SET @ret = 0;  
    RETURN @ret;  
END; 
```  
  
 以下範例會使用 `ufnGetInventoryStock` 函數來傳回 `ProductModelID` 介於 75 和 80 之間的產品目前的存貨量。  
  
```sql  
SELECT ProductModelID, Name, dbo.ufnGetInventoryStock(ProductID)AS CurrentSupply  
FROM Production.Product  
WHERE ProductModelID BETWEEN 75 and 80;  
```  

> [!NOTE]  
> 如需純量函式的詳細資訊和範例，請參閱 [CREATE FUNCTION &#40;Transact-SQL&#41;](../../t-sql/statements/create-function-transact-sql.md)。 

##  <a name="table-valued-functions"></a><a name="TVF"></a> 資料表值函式  
下列範例會在 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 資料庫中建立 **內嵌資料表值函式 (TVF)**。 這個函數使用了一個輸入參數，也就是客戶 (商店) 識別碼，並傳回 `ProductID`和 `Name`資料行，以及從年初至今將每項產品銷售給商店的彙總銷售額 `YTD Total` 。  
  
```sql  
IF OBJECT_ID (N'Sales.ufn_SalesByStore', N'IF') IS NOT NULL  
    DROP FUNCTION Sales.ufn_SalesByStore;  
GO  
CREATE FUNCTION Sales.ufn_SalesByStore (@storeid int)  
RETURNS TABLE  
AS  
RETURN   
(  
    SELECT P.ProductID, P.Name, SUM(SD.LineTotal) AS 'Total'  
    FROM Production.Product AS P   
    JOIN Sales.SalesOrderDetail AS SD ON SD.ProductID = P.ProductID  
    JOIN Sales.SalesOrderHeader AS SH ON SH.SalesOrderID = SD.SalesOrderID  
    JOIN Sales.Customer AS C ON SH.CustomerID = C.CustomerID  
    WHERE C.StoreID = @storeid  
    GROUP BY P.ProductID, P.Name  
);  
```  
  
下例會叫用這個函數並指定客戶識別碼 602。  
  
```sql  
SELECT * FROM Sales.ufn_SalesByStore (602);  
```  
  
下列範例會在 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 資料庫中建立 **多重陳述式資料表值函式 (MSTVF)**。 這個函數使用單一輸入參數 `EmployeeID` ，並傳回一份清單，列出這位指定員工的所有直接或間接下屬。 然後叫用此函數並指定員工識別碼 109。  
  
```sql  
IF OBJECT_ID (N'dbo.ufn_FindReports', N'TF') IS NOT NULL  
    DROP FUNCTION dbo.ufn_FindReports;  
GO  
CREATE FUNCTION dbo.ufn_FindReports (@InEmpID INTEGER)  
RETURNS @retFindReports TABLE   
(  
    EmployeeID int primary key NOT NULL,  
    FirstName nvarchar(255) NOT NULL,  
    LastName nvarchar(255) NOT NULL,  
    JobTitle nvarchar(50) NOT NULL,  
    RecursionLevel int NOT NULL  
)  
--Returns a result set that lists all the employees who report to the   
--specific employee directly or indirectly.*/  
AS  
BEGIN  
WITH EMP_cte(EmployeeID, OrganizationNode, FirstName, LastName, JobTitle, RecursionLevel) -- CTE name and columns  
    AS (  
        SELECT e.BusinessEntityID, e.OrganizationNode, p.FirstName, p.LastName, e.JobTitle, 0 -- Get the initial list of Employees for Manager n  
        FROM HumanResources.Employee e   
INNER JOIN Person.Person p   
ON p.BusinessEntityID = e.BusinessEntityID  
        WHERE e.BusinessEntityID = @InEmpID  
        UNION ALL  
        SELECT e.BusinessEntityID, e.OrganizationNode, p.FirstName, p.LastName, e.JobTitle, RecursionLevel + 1 -- Join recursive member to anchor  
        FROM HumanResources.Employee e   
            INNER JOIN EMP_cte  
            ON e.OrganizationNode.GetAncestor(1) = EMP_cte.OrganizationNode  
INNER JOIN Person.Person p   
ON p.BusinessEntityID = e.BusinessEntityID  
        )  
-- copy the required columns to the result of the function   
   INSERT @retFindReports  
   SELECT EmployeeID, FirstName, LastName, JobTitle, RecursionLevel  
   FROM EMP_cte   
   RETURN  
END;  
GO  
```  
  
下列範例會叫用此函式並指定員工識別碼 1。  
  
```sql  
SELECT EmployeeID, FirstName, LastName, JobTitle, RecursionLevel  
FROM dbo.ufn_FindReports(1);  
```  

> [!NOTE]  
> 如需內嵌資料表值函式 (內嵌 TVF) 和多重陳述式資料表值函式 (MSTVF) 的詳細資訊，請參閱 [CREATE FUNCTION &#40;Transact-SQL&#41;](../../t-sql/statements/create-function-transact-sql.md)。 

## <a name="best-practices"></a>最佳做法  
如果未以 `SCHEMABINDING` 子句建立使用者定義函式 (UDF)，叫用該函式時，對基礎物件所進行的變更可能會影響函式的定義並產生非預期的結果。 建議您實作下列其中一個方法，以確保函數不會因為其基礎物件的變更而變成過期：  
  
-   當您要建立 UDF 時，指定 `WITH SCHEMABINDING` 子句。 這可以確保系統無法修改函數定義中參考的物件 (除非同時修改函數)。  
  
-   在修改 UDF 定義中指定的任何物件之後，執行 [sp_refreshsqlmodule](../../relational-databases/system-stored-procedures/sp-refreshsqlmodule-transact-sql.md) 預存程序。  

若要建立不會存取資料的 UDF，請指定 `SCHEMABINDING` 選項。 如此可防止查詢最佳化工具針對涉及這些 UDF 的查詢計劃，產生不必要的多工緩衝處理運算子。 如需多工緩衝處理的詳細資訊，請參閱[執行程序表邏輯和實體運算子參考](../../relational-databases/showplan-logical-and-physical-operators-reference.md)。 如需建立結構描述繫結函式的詳細資訊，請參閱[結構描述繫結的函式](../../relational-databases/user-defined-functions/user-defined-functions.md#SchemaBound)。

雖然您可以聯結至 `FROM` 子句中的 MSTVF，但是效能可能不佳。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 無法針對可包含在 MSTVF 中的某些陳述式使用所有最佳化技術，因此會產生次佳查詢計劃。 若要獲得最佳效能，請盡可能在基底資料表而非函數之間使用聯結。  

> [!IMPORTANT]
> MSTVF 從 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 開始具有固定的基數估計值 100，在舊版 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中則為 1。    
> 從 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 開始，最佳化使用 MSTVF 的執行計畫可以利用交錯執行，這會導致使用實際的基數，而不是上述啟發學習法。     
> 如需詳細資訊，請參閱[交錯執行多重陳述式資料表值函式](../../relational-databases/performance/intelligent-query-processing.md#interleaved-execution-for-mstvfs)。

> [!NOTE]  
> 在預存程序或使用者定義函數中傳遞參數，或在批次陳述式中宣告和設定變數時，不接受 ANSI_WARNINGS。 例如，若將變數定義為 **char(3)** ，然後將其設為大於三個字元的值，資料便會被截斷成定義的大小，且 `INSERT` 或 `UPDATE` 陳述式會執行成功。

## <a name="see-also"></a>另請參閱  
 [使用者定義的函式](../../relational-databases/user-defined-functions/user-defined-functions.md)     
 [CREATE FUNCTION &#40;Transact-SQL&#41;](../../t-sql/statements/create-function-transact-sql.md)    
 [ALTER FUNCTION &#40;Transact-SQL&#41;](../../t-sql/statements/alter-function-transact-sql.md)    
 [DROP FUNCTION &#40;Transact-SQL&#41;](../../t-sql/statements/drop-function-transact-sql.md)     
 [DROP PARTITION FUNCTION &#40;Transact-SQL&#41;](../../t-sql/statements/drop-partition-function-transact-sql.md)    
 [社群](https://www.bing.com/search?q=user%20defined%20function%20%22sql%20server%202016%22%20examples&qs=n&form=QBRE&pq=user%20defined%20function%20%22sql%20server%202016%22%20examples&sc=0-48&sp=-1&sk=&cvid=C3AD337125A840AD9EEFA3AAC36A3712)中有更多範例   
  
