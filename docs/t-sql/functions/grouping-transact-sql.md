---
description: GROUPING (Transact-SQL)
title: GROUPING (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 12/03/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- GROUPING
- GROUPING_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- null values [SQL Server], GROUPING function
- grouping columns
- ROLLUP operator
- GROUP BY clause, GROUPING function
- GROUPING function
- CUBE operator
ms.assetid: 4efa3868-1fc4-4626-8fb1-e863cc03e422
author: cawrites
ms.author: chadam
ms.openlocfilehash: 9ced8a83cff51304050b71373a3272cdc2a80af6
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98100448"
---
# <a name="grouping-transact-sql"></a>GROUPING (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa.md)]

  指出是否彙總 GROUP BY 清單中指定的資料行運算式。 GROUPING 傳回 1 時，表示會在結果集中彙總，傳回 0 則不會。 當指定 GROUP BY 時，GROUPING 只能在 SELECT \<select> 清單、HAVING 和 ORDER BY 子句中使用。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
GROUPING ( <column_expression> )  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 \<column_expression>  
 為包含 [GROUP BY](../../t-sql/queries/select-group-by-transact-sql.md) 子句中資料行的資料行或運算式。  
  
## <a name="return-types"></a>傳回型別  
 **tinyint**  
  
## <a name="remarks"></a>備註  
 GROUPING 是用來區別 ROLLUP、CUBE 或 GROUPING SETS 傳回的 null 值與標準 null 值。 因 ROLLUP、CUBE 或 GROUPING SETS 運算之結果而傳回的 NULL 是 NULL 的特殊使用。 這用來作為結果集中的資料行預留位置，代表全部。  
  
## <a name="examples"></a>範例  
 下列範例會在 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 資料庫中，將 `SalesQuota` 和彙總 `SaleYTD` 數量進行分組。 `GROUPING` 函數套用在 `SalesQuota` 資料行上。  
  
```sql 
SELECT SalesQuota, SUM(SalesYTD) 'TotalSalesYTD', GROUPING(SalesQuota) AS 'Grouping'  
FROM Sales.SalesPerson  
GROUP BY SalesQuota WITH ROLLUP;  
GO  
```  
  
 結果集在 `SalesQuota` 之下顯示兩個 Null 值。 第一個 `NULL` 代表資料表中這個資料行的 Null 值群組。 第二個 `NULL` 位在 ROLLUP 作業所加入的摘要資料列中。 摘要資料列會顯示所有 `SalesQuota` 群組的 `TotalSalesYTD` 數量，由 `Grouping` 資料行中的 `1` 來表示。  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
 ```
 SalesQuota     TotalSalesYTD       Grouping  
------------   -----------------   --------  
NULL           1533087.5999          0  
250000.00      33461260.59           0  
300000.00      9299677.9445          0  
NULL           44294026.1344         1  

(4 row(s) affected)
```  
  
## <a name="see-also"></a>另請參閱  
 [GROUPING_ID &#40;Transact-SQL&#41;](../../t-sql/functions/grouping-id-transact-sql.md)   
 [GROUP BY &#40;Transact-SQL&#41;](../../t-sql/queries/select-group-by-transact-sql.md)  
  
  
