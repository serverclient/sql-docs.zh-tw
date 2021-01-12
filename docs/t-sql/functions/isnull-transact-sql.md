---
description: ISNULL (Transact-SQL)
title: ISNULL (Transact-SQL)
ms.custom: ''
ms.date: 03/06/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- ISNULL
- ISNULL_TSQL
- IFNULL_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- replacing null values
- null values [SQL Server], ISNULL
- null values [SQL Server], replacement values
- ISNULL function
ms.assetid: 6f3e5802-864b-4e77-9862-657bb5430b68
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 1a3a5b4a1f22a84435c0658c3e0cfc8a14c6f086
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98100468"
---
# <a name="isnull-transact-sql"></a>ISNULL (Transact-SQL) 

[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

以指定的取代值來取代 NULL。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
ISNULL ( check_expression , replacement_value )  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *check_expression*  
 為要檢查 NULL 的[運算式](../../t-sql/language-elements/expressions-transact-sql.md)。 *check_expression* 可為任何類型。  
  
 *replacement_value*  
 為 *check_expression* 是 NULL 時，要傳回的運算式。 *replacement_value* 必須是能夠隱含轉換成 *check_expression* 類型的類型。  
  
## <a name="return-types"></a>傳回型別  
 傳回與 *check_expression* 相同的類型。 若將常值 NULL 作為 *check_expression* 提供，則會傳回 *replacement_value* 的資料類型。 若將常值 NULL 作為 *check_expression* 提供，並且未提供任何 *replacement_value*，則會傳回 **int**。  
  
## <a name="remarks"></a>備註  
 如果 *check_expression* 的值不是 NULL，則會傳回該值；否則會在其隱含轉換成 *check_expression* 的類型後傳回 *replacement_value* (若兩者類型不同的話)。 若 *replacement_value* 的長度超過 *check_expression*，則 *replacement_value* 可能會被截斷。  
  
> [!NOTE]  
>  使用 [COALESCE &#40;Transact-SQL&#41;](../../t-sql/language-elements/coalesce-transact-sql.md) 來傳回第一個不是 NULL 的值。  
  
## <a name="examples"></a>範例  
  
### <a name="a-using-isnull-with-avg"></a>A. 使用 ISNULL 搭配 AVG  
 下列範例會尋找所有產品的加權平均值。 它會在 `50` 資料表的 `Weight` 資料行中，用值 `Product` 來取代所有 NULL 項目。  
  
```sql  
USE AdventureWorks2012;  
GO  
SELECT AVG(ISNULL(Weight, 50))  
FROM Production.Product;  
GO  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
 ```
 -------------------------- 
59.79  
  
 (1 row(s) affected)
 ```  
  
### <a name="b-using-isnull"></a>B. 使用 ISNULL  
 下列範例會選取 `AdventureWorks2012` 中所有特殊供應項目的描述、折扣百分比、最小數量和最大數量。 如果所有特殊供應項目的最大數量是 NULL，結果集所顯示的 `MaxQty` 便是 `0.00`。  
  
```sql  
USE AdventureWorks2012;  
GO  
SELECT Description, DiscountPct, MinQty, ISNULL(MaxQty, 0.00) AS 'Max Quantity'  
FROM Sales.SpecialOffer;  
GO  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
|  描述       |  DiscountPct    |   MinQty    |   最大數量 (Max Quantity)       |
|  ---------------   |  -------------  |   --------  |   ---------------    |
|  {1}No Discount{2}       |  0.00           |   0         |   0                  |
|  {1}Volume Discount{2}   |  0.02           |   11        |   14                 |
|  {1}Volume Discount{2}   |  0.05           |   15        |   4                  |
|  {1}Volume Discount{2}   |  0.10           |   25        |   0                  |
|  {1}Volume Discount{2}   |  0.15           |   41        |   0                  |
|  {1}Volume Discount{2}   |  0.20           |   61        |   0                  |
|  Mountain-100 Cl   |  0.35           |   0         |   0                  |
|  Sport Helmet Di   |  0.10           |   0         |   0                  |
|  Road-650 Overst   |  0.30           |   0         |   0                  |
|  Mountain Tire S   |  0.50           |   0         |   0                  |
|  Sport Helmet Di   |  0.15           |   0         |   0                  |
|  LL Road Frame S   |  0.35           |   0         |   0                  |
|  Touring-3000 Pr   |  0.15           |   0         |   0                  |
|  Touring-1000 Pr   |  0.20           |   0         |   0                  |
|  Half-Price Peda   |  0.50           |   0         |   0                  |
|  Mountain-500 Si   |  0.40           |   0         |   0                  |

 `(16 row(s) affected)`  
  
### <a name="c-testing-for-null-in-a-where-clause"></a>C. 在 WHERE 子句中測試 NULL  
 請勿使用 ISNULL 來尋找 NULL 值。 請改用 IS NULL。 下列範例會尋找加權資料行中有 `NULL` 的所有產品。 請注意 `IS` 和 `NULL` 之間的空格。  
  
```sql  
USE AdventureWorks2012;  
GO  
SELECT Name, Weight  
FROM Production.Product  
WHERE Weight IS NULL;  
GO  
```  
  
## <a name="examples-sssdwfull-and-sspdw"></a>範例：[!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 和 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
### <a name="d-using-isnull-with-avg"></a>D. 使用 ISNULL 搭配 AVG  
 下列範例會尋找範例資料表中所有產品的加權平均值。 它會在 `50` 資料表的 `Weight` 資料行中，用值 `Product` 來取代所有 NULL 項目。  
  
```sql  
-- Uses AdventureWorks  
  
SELECT AVG(ISNULL(Weight, 50))  
FROM dbo.DimProduct;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
--------------------------   
52.88   
```  
  
### <a name="e-using-isnull"></a>E. 使用 ISNULL  
 下列範例會使用 ISNULL 來在 `MinPaymentAmount` 資料行中測試 NULL 值，然後為那些資料列顯示 `0.00` 值。  
  
```sql  
-- Uses AdventureWorks  
  
SELECT ResellerName,   
       ISNULL(MinPaymentAmount,0) AS MinimumPayment  
FROM dbo.DimReseller  
ORDER BY ResellerName;  
  
```  
  
 以下為部分結果集。  
  
|  ResellerName                |  MinimumPayment    |
|  -------------------------   |  --------------    |
|  A Bicycle Association       |     0.0000         |
|  A Bike Store                |     0.0000         |
|  A Cycle Shop                |     0.0000         |
|  A Great Bicycle Company     |     0.0000         |
|  A Typical Bike Shop         |   200.0000         |
|  Acceptable Sales & Service  |     0.0000         |
  
### <a name="f-using-is-null-to-test-for-null-in-a-where-clause"></a>F. 使用 IS NULL 來在 WHERE 子句中測試 NULL  
 下列範例會尋找 `NULL` 資料行中有 `Weight` 的所有產品。 請注意 `IS` 和 `NULL` 之間的空格。  
  
```sql  
-- Uses AdventureWorks  
  
SELECT EnglishProductName, Weight  
FROM dbo.DimProduct  
WHERE Weight IS NULL;  
```  
  
## <a name="see-also"></a>另請參閱  
 [運算式 &#40;Transact-SQL&#41;](../../t-sql/language-elements/expressions-transact-sql.md)   
 [IS NULL &#40;Transact-SQL&#41;](../../t-sql/queries/is-null-transact-sql.md)   
 [系統函數 &#40;Transact-SQL&#41;](../../relational-databases/system-functions/system-functions-category-transact-sql.md)   
 [WHERE &#40;Transact-SQL&#41;](../../t-sql/queries/where-transact-sql.md)   
 [COALESCE &#40;Transact-SQL&#41;](../../t-sql/language-elements/coalesce-transact-sql.md)  
  
  

