---
description: IN (Transact-SQL)
title: IN (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 08/29/2016
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- IN_TSQL
- IN
dev_langs:
- TSQL
helpviewer_keywords:
- values [SQL Server], matching
- NOT IN keyword
- 8623 (Database Engine error)
- matching values in subquery or list [SQL Server]
- IN keyword
- 8632 (Database Engine error)
ms.assetid: 4419de73-96b1-4dfe-8500-f4507915db04
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 1a2be93be39ac3af00895a9260958b512652fc3d
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98085088"
---
# <a name="in-transact-sql"></a>IN (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  判斷指定的值是否符合子查詢或清單中的任何值。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
test_expression [ NOT ] IN   
    ( subquery | expression [ ,...n ]  
    )   
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *test_expression*  
 這是任何有效的[運算式](../../t-sql/language-elements/expressions-transact-sql.md)。  
  
 *subquery*  
 這是有單一資料行結果集的子查詢。 這個資料行必須具備與 *test_expression* 相同的資料類型。  
  
 *expression*[ **,**... *n* ]  
 這是要進行相符測試的運算式清單。 所有運算式的類型都必須與 *test_expression* 相同。  
  
## <a name="result-types"></a>結果類型  
 **布林值**  
  
## <a name="result-value"></a>結果值  
 如果 *test_expression* 的值等於 *subquery* 所傳回的任何值，或等於以逗號分隔清單中的任何 *expression*，結果值就是 TRUE；否則，結果值就是 FALSE。  
  
 使用 NOT IN 會否定 *subquery* 值或 *expression*。  
  
> [!CAUTION]  
>  相較於使用 IN 或 NOT IN 的 *test_expression*，*subquery* 或 *expression* 所傳回的任何 Null 值都會傳回 UNKNOWN。 將 Null 值與 IN 或 NOT IN 一起使用可能會產生非預期的結果。  
  
## <a name="remarks"></a>備註  
 在 IN 子句中明確包括極大量的值 (數千個以逗號分隔的值) 會耗用資源，並傳回錯誤 8623 或 8632。 若要解決此問題，請將項目儲存在資料表的 IN 清單中，並在 IN 子句內使用 SELECT 子查詢。  
  
 錯誤 8623：  
  
 `The query processor ran out of internal resources and could not produce a query plan. This is a rare event and only expected for extremely complex queries or queries that reference a very large number of tables or partitions. Please simplify the query. If you believe you have received this message in error, contact Customer Support Services for more information.`  
  
 錯誤 8632：  
  
 `Internal error: An expression services limit has been reached. Please look for potentially complex expressions in your query, and try to simplify them.`  
  
## <a name="examples"></a>範例  
  
### <a name="a-comparing-or-and-in"></a>A. 比較 OR 及 IN  
 下列範例會選取設計工程師、工具設計師或行銷助理等員工名單。  
  
```sql  
-- Uses AdventureWorks  
  
SELECT p.FirstName, p.LastName, e.JobTitle  
FROM Person.Person AS p  
JOIN HumanResources.Employee AS e  
    ON p.BusinessEntityID = e.BusinessEntityID  
WHERE e.JobTitle = 'Design Engineer'   
   OR e.JobTitle = 'Tool Designer'   
   OR e.JobTitle = 'Marketing Assistant';  
GO  
```  
  
 不過，您將利用 IN 來擷取相同的結果。  
  
```sql  
-- Uses AdventureWorks  
  
SELECT p.FirstName, p.LastName, e.JobTitle  
FROM Person.Person AS p  
JOIN HumanResources.Employee AS e  
    ON p.BusinessEntityID = e.BusinessEntityID  
WHERE e.JobTitle IN ('Design Engineer', 'Tool Designer', 'Marketing Assistant');  
GO  
```  
  
 以下是任何一項查詢的結果集。  
  
```  
FirstName   LastName      Title  
---------   ---------   ---------------------  
Sharon      Salavaria   Design Engineer                                     
Gail        Erickson    Design Engineer                                     
Jossef      Goldberg    Design Engineer                                     
Janice      Galvin      Tool Designer                                       
Thierry     D'Hers      Tool Designer                                       
Wanida      Benshoof    Marketing Assistant                                 
Kevin       Brown       Marketing Assistant                                 
Mary        Dempsey     Marketing Assistant                                 
  
(8 row(s) affected)  
```  
  
### <a name="b-using-in-with-a-subquery"></a>B. 使用 IN 搭配子查詢  
 下列範例在 `SalesPerson` 資料表中，針對年度銷售配額超出 $250,000 之員工來尋找銷售人員的所有識別碼，之後，再從 `Employee` 資料表中，選取 `EmployeeID` 符合 `SELECT` 子查詢結果的所有員工姓名。  
  
```sql  
-- Uses AdventureWorks  
  
SELECT p.FirstName, p.LastName  
FROM Person.Person AS p  
    JOIN Sales.SalesPerson AS sp  
    ON p.BusinessEntityID = sp.BusinessEntityID  
WHERE p.BusinessEntityID IN  
   (SELECT BusinessEntityID  
   FROM Sales.SalesPerson  
   WHERE SalesQuota > 250000);  
GO  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
FirstName   LastName                                             
---------   --------   
Tsvi         Reiter                                              
Michael      Blythe                                              
Tete         Mensa-Annan                                         
  
(3 row(s) affected)  
```  
  
### <a name="c-using-not-in-with-a-subquery"></a>C. 使用 NOT IN 搭配子查詢  
 下列範例會尋找配額不超出 $250,000 的銷售人員。 `NOT IN` 會尋找不符合值清單項目的銷售人員。  
  
```sql  
-- Uses AdventureWorks  
  
SELECT p.FirstName, p.LastName  
FROM Person.Person AS p  
    JOIN Sales.SalesPerson AS sp  
    ON p.BusinessEntityID = sp.BusinessEntityID  
WHERE p.BusinessEntityID NOT IN  
   (SELECT BusinessEntityID  
   FROM Sales.SalesPerson  
   WHERE SalesQuota > 250000);  
GO  
```  
  
## <a name="examples-sssdwfull-and-sspdw"></a>範例：[!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 和 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
### <a name="d-using-in-and-not-in"></a>D. 使用 IN 和 NOT IN  
 下列範例會在 `FactInternetSales` 資料表中尋找符合 `DimSalesReason` 資料表中 `SalesReasonKey` 值的所有項目。  
  
```sql  
-- Uses AdventureWorks  
  
SELECT * FROM FactInternetSalesReason   
WHERE SalesReasonKey   
IN (SELECT SalesReasonKey FROM DimSalesReason);   
```  
  
 下列範例會在 `FactInternetSalesReason` 資料表中尋找不符合 `DimSalesReason` 資料表中 `SalesReasonKey` 值的所有項目。  
  
```sql  
-- Uses AdventureWorks  
  
SELECT * FROM FactInternetSalesReason   
WHERE SalesReasonKey   
NOT IN (SELECT SalesReasonKey FROM DimSalesReason);  
```  
  
### <a name="e-using-in-with-an-expression-list"></a>E. 搭配運算式清單使用 IN  
 下列範例會在 `DimEmployee` 資料表中針對名字為 `Mike` 或 `Michael` 的員工，尋找銷售人員的所有識別碼。  
  
```sql  
-- Uses AdventureWorks  
  
SELECT FirstName, LastName  
FROM DimEmployee  
WHERE FirstName IN ('Mike', 'Michael');  
```  
  
## <a name="see-also"></a>另請參閱  
 [CASE &#40;Transact-SQL&#41;](../../t-sql/language-elements/case-transact-sql.md)   
 [運算式 &#40;Transact-SQL&#41;](../../t-sql/language-elements/expressions-transact-sql.md)   
 [內建函數 &#40;Transact-SQL&#41;](~/t-sql/functions/functions.md)   
 [運算子 &#40;Transact-SQL&#41;](../../t-sql/language-elements/operators-transact-sql.md)   
 [SELECT &#40;Transact-SQL&#41;](../../t-sql/queries/select-transact-sql.md)   
 [WHERE &#40;Transact-SQL&#41;](../../t-sql/queries/where-transact-sql.md)   
 [ALL &#40;Transact-SQL&#41;](../../t-sql/language-elements/all-transact-sql.md)   
 [SOME &#124; ANY &#40;Transact-SQL&#41;](../../t-sql/language-elements/some-any-transact-sql.md)  
  
  



