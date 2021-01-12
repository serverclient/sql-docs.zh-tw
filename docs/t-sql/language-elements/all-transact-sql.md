---
description: ALL (Transact-SQL)
title: ALL (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- ALL_TSQL
- ALL
dev_langs:
- TSQL
helpviewer_keywords:
- single-column set of values [SQL Server]
- ALL (Transact-SQL)
ms.assetid: 4b0c002e-1ffd-4425-a980-11fdc1f24af7
author: cawrites
ms.author: chadam
ms.openlocfilehash: b531df59f8350058d88afd0d974aa873d88b7520
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98101030"
---
# <a name="all-transact-sql"></a>ALL (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  比較純量值與單一資料行集的值。  
  
 ![文章連結圖示](../../database-engine/configure-windows/media/topic-link.gif "文章連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
scalar_expression { = | <> | != | > | >= | !> | < | <= | !< } ALL ( subquery )  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *scalar_expression*  
 這是任何有效的[運算式](../../t-sql/language-elements/expressions-transact-sql.md)。  
  
 { = \| <> \| != \| > \| >= \| !> \| < \| <= \| !< }  
 這是一個比較運算子。  
  
 *subquery*  
 這是傳回單一資料行結果集的子查詢。 所傳回資料行的資料類型必須與 *scalar_expression* 的資料類型相同。  
  
 這是一個受限制的 SELECT 陳述式，不允許使用 ORDER BY 子句和 INTO 關鍵字。  
  
## <a name="result-types"></a>結果類型  
 **布林值**  
  
## <a name="result-value"></a>結果值  
 若各組 (_scalar_expression_ **,** _x)_ 的指定比較都是 TRUE (其中 *x* 是單一資料行集中的值)，則傳回 TRUE。 否則傳回 FALSE。  
  
## <a name="remarks"></a>備註  
 ALL 需要正面比較 *scalar_expression* 與子查詢所傳回的每個值。 例如，如果子查詢傳回 2 和 3 的值，*scalar_expression* <= ALL (子查詢) 會針對值為 2 的 *scalar_expression* 評估為 TRUE。 如果子查詢傳回 2 和 3 的值，*scalar_expression* = ALL (子查詢) 就會評估為 FALSE，因為子查詢 (值為 3) 的某些值不符合運算式準則。  
  
 如需要求正面比較 *scalar_expression* 和僅由子查詢傳回之單一值的陳述式，請參閱 [SOME &#124; ANY &#40;Transact-SQL&#41;](../../t-sql/language-elements/some-any-transact-sql.md)。  
  
 本文所使用的子查詢都是指 ALL。 ALL 也可以搭配 [UNION](../../t-sql/language-elements/set-operators-union-transact-sql.md) 和 [SELECT](../../t-sql/queries/select-transact-sql.md) 使用。  
  
## <a name="examples"></a>範例  
 下列範例會建立預存程序，以判斷 `SalesOrderID` 資料庫中所指定 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 的所有組件，是否都可以在指定的天數內製造出來。 這個範例會使用子查詢，針對特定 `DaysToManufacture` 的所有元件建立一份 `SalesOrderID` 值數目清單，然後確認所有 `DaysToManufacture` 都在指定的天數範圍內。  
  
```sql  
-- Uses AdventureWorks  
  
CREATE PROCEDURE DaysToBuild @OrderID INT, @NumberOfDays INT  
AS  
IF   
@NumberOfDays >= ALL  
   (  
    SELECT DaysToManufacture  
    FROM Sales.SalesOrderDetail  
    JOIN Production.Product   
    ON Sales.SalesOrderDetail.ProductID = Production.Product.ProductID   
    WHERE SalesOrderID = @OrderID  
   )  
PRINT 'All items for this order can be manufactured in specified number of days or less.'  
ELSE   
PRINT 'Some items for this order can''t be manufactured in specified number of days or less.' ;  
```  
  
 若要測試這個程序，請使用 `SalesOrderID 49080` 來執行程序，其具有一個需要 `2` 天時間來製造的組件，以及兩個需要 0 天來製造的組件。 下列的第一個陳述式符合此準則， 第二項查詢則不符合。  
  
```sql  
EXECUTE DaysToBuild 49080, 2 ;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
 `All items for this order can be manufactured in specified number of days or less.`  
  
```sql  
EXECUTE DaysToBuild 49080, 1 ;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
 `Some items for this order can't be manufactured in specified number of days or less.`  
  
## <a name="see-also"></a>另請參閱  
 [CASE &#40;Transact-SQL&#41;](../../t-sql/language-elements/case-transact-sql.md)   
 [運算式 &#40;Transact-SQL&#41;](../../t-sql/language-elements/expressions-transact-sql.md)   
 [內建函數 &#40;Transact-SQL&#41;](~/t-sql/functions/functions.md)   
 [LIKE &#40;Transact-SQL&#41;](../../t-sql/language-elements/like-transact-sql.md)   
 [運算子 &#40;Transact-SQL&#41;](../../t-sql/language-elements/operators-transact-sql.md)   
 [SELECT &#40;Transact-SQL&#41;](../../t-sql/queries/select-transact-sql.md)   
 [WHERE &#40;Transact-SQL&#41;](../../t-sql/queries/where-transact-sql.md)   
 [IN &#40;Transact-SQL&#41;](../../t-sql/language-elements/in-transact-sql.md)  
  
