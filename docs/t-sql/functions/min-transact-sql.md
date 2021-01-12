---
description: MIN (Transact-SQL)
title: MIN (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/13/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- MIN
- MIN_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- MIN function [Transact-SQL]
- minimum values [SQL Server]
- values [SQL Server], minimum
ms.assetid: 56cf6ec5-34f5-47e3-a402-7129039d4429
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 49663384cb73ed8b97720fee17ecdac641bc44f2
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98093560"
---
# <a name="min-transact-sql"></a>MIN (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  傳回運算式中的最小值。 後面可接續 [OVER 子句](../../t-sql/queries/select-over-clause-transact-sql.md)。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql  
-- Aggregation Function Syntax  
MIN ( [ ALL | DISTINCT ] expression )  
  
-- Analytic Function Syntax   
MIN ( [ ALL ] expression ) OVER ( [ <partition_by_clause> ] [ <order_by_clause> ] )  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 **ALL**  
 將彙總函式套用至所有值。 ALL 是預設值。  
  
 DISTINCT  
 指定要考量每個唯一值。 DISTINCT 對 MIN 沒意義，只是為了與 ISO 相容。  
  
 *expression*  
 這是一個常數、資料行名稱或函數，或算術、位元和字串運算子的任何組合。 MIN 可以搭配 **numeric**、**char**、**varchar**、**uniqueidentifier** 或 **datetime** 資料行使用，但無法搭配 **bit** 資料行使用。 不允許彙總函式和子查詢。  
  
 如需詳細資訊，請參閱[運算式 &#40;Transact-SQL&#41;](../../t-sql/language-elements/expressions-transact-sql.md)。  
  
 OVER **(** [ _partition\_by\_clause_ ] _order\_by\_clause_ **)**  
 *partition_by_clause* 會將 FROM 子句產生的結果集分割成函數所要套用的分割區。 如未指定，此函數會將查詢結果集的所有資料列視為單一群組。 *order_by_clause* 可決定執行作業的邏輯順序。 *order_by_clause* 為必要項目。 如需詳細資訊，請參閱 [OVER 子句 &#40;Transact-SQL&#41;](../../t-sql/queries/select-over-clause-transact-sql.md)。  
  
## <a name="return-types"></a>傳回型別  
 傳回與 *expression* 相同的值。  
  
## <a name="remarks"></a>備註  
 MIN 會忽略任何 Null 值。  
  
 當使用字元資料行時，MIN 會尋找排序順序中的最低值。  
  
 MIN 未搭配 OVER 和 ORDER BY 子句使用時，是具決定性函數。 使用 OVER 和 ORDER BY 子句指定時，則不具決定性。 如需詳細資訊，請參閱 [決定性與非決定性函數](../../relational-databases/user-defined-functions/deterministic-and-nondeterministic-functions.md)。  
  
## <a name="examples"></a>範例  
  
### <a name="a-simple-example"></a>A. 簡單範例  
 下列範例會傳回最低 (最小) 稅率。 此範例會使用 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 資料庫  
  
```sql  
SELECT MIN(TaxRate)  
FROM Sales.SalesTaxRate;  
GO  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
 ```
 -------------------
  
 5.00
  
 (1 row(s) affected)
 ```  
  
### <a name="b-using-the-over-clause"></a>B. 使用 OVER 子句  
 下列範例會將 MIN、MAX、AVG 和 COUNT 函數搭配 OVER 子句一起使用，為 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 資料庫之 `HumanResources.Department` 資料表中的各部門提供彙總值。  
  
```sql  
SELECT DISTINCT Name  
       , MIN(Rate) OVER (PARTITION BY edh.DepartmentID) AS MinSalary  
       , MAX(Rate) OVER (PARTITION BY edh.DepartmentID) AS MaxSalary  
       , AVG(Rate) OVER (PARTITION BY edh.DepartmentID) AS AvgSalary  
       ,COUNT(edh.BusinessEntityID) OVER (PARTITION BY edh.DepartmentID) AS EmployeesPerDept  
FROM HumanResources.EmployeePayHistory AS eph  
JOIN HumanResources.EmployeeDepartmentHistory AS edh  
     ON eph.BusinessEntityID = edh.BusinessEntityID  
JOIN HumanResources.Department AS d  
 ON d.DepartmentID = edh.DepartmentID  
WHERE edh.EndDate IS NULL  
ORDER BY Name;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
Name                          MinSalary             MaxSalary             AvgSalary             EmployeesPerDept  
----------------------------- --------------------- --------------------- --------------------- ----------------  
Document Control              10.25                 17.7885               14.3884               5  
Engineering                   32.6923               63.4615               40.1442               6  
Executive                     39.06                 125.50                68.3034               4  
Facilities and Maintenance    9.25                  24.0385               13.0316               7  
Finance                       13.4615               43.2692               23.935                10  
Human Resources               13.9423               27.1394               18.0248               6  
Information Services          27.4038               50.4808               34.1586               10  
Marketing                     13.4615               37.50                 18.4318               11  
Production                    6.50                  84.1346               13.5537               195  
Production Control            8.62                  24.5192               16.7746               8  
Purchasing                    9.86                  30.00                 18.0202               14  
Quality Assurance             10.5769               28.8462               15.4647               6  
Research and Development      40.8654               50.4808               43.6731               4  
Sales                         23.0769               72.1154               29.9719               18  
Shipping and Receiving        9.00                  19.2308               10.8718               6  
Tool Design                   8.62                  29.8462               23.5054               6  
  
 (16 row(s) affected)  
```  
  
## <a name="examples-sssdwfull-and-sspdw"></a>範例：[!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 和 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
### <a name="c-using-min"></a>C. 使用 MIN  
 下列範例會使用 MIN 彙總函式，以傳回指定銷售訂單集中最便宜 (最低價) 之產品的價格。  
  
```sql  
-- Uses AdventureWorks  
  
SELECT DISTINCT MIN(UnitPrice)  
FROM dbo.FactResellerSales   
WHERE SalesOrderNumber IN (N'SO43659', N'SO43660', N'SO43664');  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
 ```
 ------  
 5.1865
 ```  
  
### <a name="d-using-min-with-over"></a>D. 搭配 OVER 使用 MIN  
 下列範例會使用 MIN OVER() 分析函式，以傳回每一筆銷售訂單中最便宜產品的價格。 結果集會根據 `SalesOrderID` 資料行進行分割。  
  
```sql  
-- Uses AdventureWorks  
  
SELECT DISTINCT MIN(UnitPrice) OVER(PARTITION BY SalesOrderNumber) AS LeastExpensiveProduct,  
       SalesOrderNumber  
FROM dbo.FactResellerSales    
WHERE SalesOrderNumber IN (N'SO43659', N'SO43660', N'SO43664')  
ORDER BY SalesOrderNumber;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
 ```
LeastExpensiveProduct SalesOrderID  
--------------------- ----------  
5.1865                SO43659  
419.4589              SO43660  
28.8404               SO43664
```  
  
## <a name="see-also"></a>另請參閱  
 [彙總函數 &#40;Transact-SQL&#41;](../../t-sql/functions/aggregate-functions-transact-sql.md)   
 [MAX &#40;Transact-SQL&#41;](../../t-sql/functions/max-transact-sql.md)   
 [OVER 子句 &#40;Transact-SQL&#41;](../../t-sql/queries/select-over-clause-transact-sql.md)  
  
  

