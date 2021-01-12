---
description: SOME | ANY (Transact-SQL)
title: SOME | ANY (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- SOME
- SOME_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- scalar values
- comparing scalar with single-column set
- ANY operator
- SOME | ANY keyword
- single-column set of values [SQL Server]
ms.assetid: 1f717ad6-f67b-4980-9397-577ecb0e5789
author: cawrites
ms.author: chadam
ms.openlocfilehash: 42df6fc94ca0ca62b98156db73f3394d1850219f
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98095932"
---
# <a name="some--any-transact-sql"></a>SOME | ANY (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  比較純量值與單一資料行集的值。 SOME 和 ANY 是相等的。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
scalar_expression { = | < > | ! = | > | > = | ! > | < | < = | ! < }   
     { SOME | ANY } ( subquery )   
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *scalar_expression*  
 這是任何有效的[運算式](../../t-sql/language-elements/expressions-transact-sql.md)。  
  
 { = \| <> \| != \| > \| >= \| !> \| < \| <= \| !< }  
 這是任何有效的比較運算子。  
  
 SOME | ANY  
 指定應該進行比較。  
  
 *subquery*  
 這是有單一資料行結果集的子查詢。 傳回的資料行資料類型必須與 *scalar_expression* 的資料類型相同。  
  
## <a name="result-types"></a>結果類型  
 **布林值**  
  
## <a name="result-value"></a>結果值  
 若任意組 (**scalar_expression** _,_ **x**) 的指定比較都是 TRUE (其中 _x_ 是單一資料行集中的值)，則 SOME 或 ANY 會傳回 *TRUE*；否則，會傳回 **FALSE**。  
  
## <a name="remarks"></a>備註  
 SOME 需要正面比較 *scalar_expression* 與至少一個子查詢傳回值。 對於需要以正數來比較 *scalar_expression* 和由子查詢傳回之每個值的陳述式，請參閱 [ALL &#40;Transact-SQL&#41;](../../t-sql/language-elements/all-transact-sql.md)。 例如，如果子查詢傳回 2 和 3 的值，*scalar_expression* = SOME (子查詢) 會針對值為 2 的 *scalar_express* 評估為 TRUE。 如果子查詢傳回 2 和 3 的值，*scalar_expression* = ALL (子查詢) 就會評估為 FALSE，因為子查詢 (值為 3) 的某些值不符合運算式準則。  
  
## <a name="examples"></a>範例  
  
### <a name="a-running-a-simple-example"></a>A. 執行簡單範例  
 下列陳述式會建立一份簡單資料表，並將 `1`、`2`、`3` 和 `4` 的值加入至 `ID` 資料行。  
  
```sql  
CREATE TABLE T1  
(ID INT) ;  
GO  
INSERT T1 VALUES (1) ;  
INSERT T1 VALUES (2) ;  
INSERT T1 VALUES (3) ;  
INSERT T1 VALUES (4) ;  
```  
  
 下列查詢會傳回 `TRUE`，因為 `3` 小於資料表中的某些值。  
  
```sql  
IF 3 < SOME (SELECT ID FROM T1)  
PRINT 'TRUE'   
ELSE  
PRINT 'FALSE' ;  
```  
  
 下列查詢會傳回 `FALSE`，因為 `3` 不小於資料表中的所有值。  
  
```sql  
IF 3 < ALL (SELECT ID FROM T1)  
PRINT 'TRUE'   
ELSE  
PRINT 'FALSE' ;  
```  
  
### <a name="b-running-a-practical-example"></a>B. 執行實際範例  
 下列範例會建立預存程序，以判斷 `SalesOrderID` 資料庫中所指定 `AdventureWorks2012` 的所有組件，是否都可以在指定的天數內製造出來。 這個範例會使用子查詢，針對特定 `DaysToManufacture` 的所有元件建立一份 `SalesOrderID` 值數目的清單，然後測試是否有任何一個子查詢傳回值大於所指定的天數。 如果傳回的每個 `DaysToManufacture` 值都小於提供的數目，則條件為 TRUE，並且會列印出第一個訊息。  
  
```sql  
-- Uses AdventureWorks  
  
CREATE PROCEDURE ManyDaysToComplete @OrderID INT, @NumberOfDays INT  
AS  
IF   
@NumberOfDays < SOME  
   (  
    SELECT DaysToManufacture  
    FROM Sales.SalesOrderDetail  
    JOIN Production.Product   
    ON Sales.SalesOrderDetail.ProductID = Production.Product.ProductID   
    WHERE SalesOrderID = @OrderID  
   )  
PRINT 'At least one item for this order can't be manufactured in specified number of days.'  
ELSE   
PRINT 'All items for this order can be manufactured in the specified number of days or less.' ;  
  
```  
  
 若要測試這個程序，請使用 `SalesOrderID``49080` 來執行程序，其具有一個需要 `2` 天時間來製造的組件，以及兩個需要 0 天來製造的組件。 第一個陳述式符合此準則， 第二項查詢則不符合。  
  
```sql  
EXECUTE ManyDaysToComplete 49080, 2 ;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
 `All items for this order can be manufactured in the specified number of days or less.`  
  
```  
EXECUTE ManyDaysToComplete 49080, 1 ;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
 `At least one item for this order can't be manufactured in specified number of days.`  
  
## <a name="see-also"></a>另請參閱  
 [ALL &#40;Transact-SQL&#41;](../../t-sql/language-elements/all-transact-sql.md)   
 [CASE &#40;Transact-SQL&#41;](../../t-sql/language-elements/case-transact-sql.md)   
 [內建函數 &#40;Transact-SQL&#41;](~/t-sql/functions/functions.md)   
 [運算子 &#40;Transact-SQL&#41;](../../t-sql/language-elements/operators-transact-sql.md)   
 [SELECT &#40;Transact-SQL&#41;](../../t-sql/queries/select-transact-sql.md)   
 [WHERE &#40;Transact-SQL&#41;](../../t-sql/queries/where-transact-sql.md)   
 [IN &#40;Transact-SQL&#41;](../../t-sql/language-elements/in-transact-sql.md)  
  
