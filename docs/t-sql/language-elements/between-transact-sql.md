---
description: BETWEEN (Transact-SQL)
title: BETWEEN (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 08/28/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- BETWEEN
- BETWEEN_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- inclusive ranges
- testing range
- exclusive ranges
- NOT BETWEEN operator
- BETWEEN operator
- range to test [SQL Server]
ms.assetid: a5d5b050-203e-4355-ac85-e08ef5ca7823
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 3d41c356b20dd0bb0577f10ee0abc47681ed38e7
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98096003"
---
# <a name="between-transact-sql"></a>BETWEEN (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  指定要測試的範圍。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
test_expression [ NOT ] BETWEEN begin_expression AND end_expression  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *test_expression*  
 這是要在 *begin_expression* 和 *end_expression* 定義的範圍中測試的 [運算式](../../t-sql/language-elements/expressions-transact-sql.md)。 *test_expression* 的資料類型必須與 *begin_expression* 和 *end_expression* 相同。  
  
 NOT  
 指定執行否定運算的述詞結果。  
  
 *begin_expression*  
 為任何有效運算式。 *begin_expression* 的資料類型必須與 *test_expression* 和 *end_expression* 相同。  
  
 *end_expression*  
 為任何有效運算式。 *end_expression* 的資料類型必須與 *test_expression* 和 *begin_expression* 相同。  
  
 AND  
 用來作為預留位置，表示 *test_expression* 應該位於 *begin_expression* 和 *end_expression* 所指定的範圍內。  
  
## <a name="result-types"></a>結果類型  
 **布林值**  
  
## <a name="result-value"></a>結果值  
 如果 *test_expression* 的值大於或等於 *begin_expression* 的值且小於或等於 *end_expression* 的值，BETWEEN 就會傳回 **TRUE**。  
  
 如果 *test_expression* 的值小於 *begin_expression* 的值或大於 *end_expression* 的值，NOT BETWEEN 就會傳回 **TRUE**。  
  
## <a name="remarks"></a>備註  
 若要指定獨佔範圍，請使用大於 (>) 和小於 (<) 運算子。 如果 BETWEEN 或 NOT BETWEEN 述詞的任何輸入是 NULL，結果就是 UNKNOWN。  
  
## <a name="examples"></a>範例  
  
### <a name="a-using-between"></a>A. 使用 BETWEEN  
 下列範例會傳回資料庫中資料庫角色的相關資訊。 第一個查詢會傳回所有角色。 第二個範例會使用 `BETWEEN` 子句，將角色限制為指定的 `database_id` 值。  
  
```sql  
SELECT principal_id, name 
FROM sys.database_principals
WHERE type = 'R';

SELECT principal_id, name 
FROM sys.database_principals
WHERE type = 'R'
AND principal_id BETWEEN 16385 AND 16390;
GO  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]   
```  
principal_id    name
------------  ---- 
0               public
16384           db_owner
16385           db_accessadmin
16386           db_securityadmin
16387           db_ddladmin
16389           db_backupoperator
16390           db_datareader
16391           db_datawriter
16392           db_denydatareader
16393           db_denydatawriter
```  
```  
principal_id    name
------------  ---- 
16385           db_accessadmin
16386           db_securityadmin
16387           db_ddladmin
16389           db_backupoperator
16390           db_datareader
```  
  
### <a name="b-using--and--instead-of-between"></a>B. 使用 > 和 < 來取代 BETWEEN  
 下列範例使用大於 (`>`) 和小於 (`<`) 運算子，因為這些運算子頭尾不包括在內，因此，不像前一個範例傳回 10 個資料列，它只會傳回 9 個資料列。  
  
```sql  
-- Uses AdventureWorks  
  
SELECT e.FirstName, e.LastName, ep.Rate  
FROM HumanResources.vEmployee e   
JOIN HumanResources.EmployeePayHistory ep   
    ON e.BusinessEntityID = ep.BusinessEntityID  
WHERE ep.Rate > 27 AND ep.Rate < 30  
ORDER BY ep.Rate;  
GO  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
 ```  
 FirstName   LastName             Rate  
 ---------   -------------------  ---------  
 Paula       Barreto de Mattos    27.1394  
 Janaina     Bueno                27.4038  
 Dan         Bacon                27.4038  
 Ramesh      Meyyappan            27.4038  
 Karen       Berg                 27.4038  
 David       Bradley              28.7500  
 Hazem       Abolrous             28.8462  
 Ovidiu      Cracium              28.8462  
 Rob         Walters              29.8462  
 ```    
  
### <a name="c-using-not-between"></a>C. 使用 NOT BETWEEN  
 下列範例會找出不在指定範圍 `27` 到 `30` 之間的所有資料列。  
  
```sql  
-- Uses AdventureWorks  
  
SELECT e.FirstName, e.LastName, ep.Rate  
FROM HumanResources.vEmployee e   
JOIN HumanResources.EmployeePayHistory ep   
    ON e.BusinessEntityID = ep.BusinessEntityID  
WHERE ep.Rate NOT BETWEEN 27 AND 30  
ORDER BY ep.Rate;  
GO  
```  
  
### <a name="d-using-between-with-datetime-values"></a>D. 使用含有 datetime 值的 BETWEEN  
 下列範例會擷取 **datetime** 值介於 `'20011212'` 和 `'20020105'` (含) 之間的資料列。  
  
```sql  
-- Uses AdventureWorks  
  
SELECT BusinessEntityID, RateChangeDate  
FROM HumanResources.EmployeePayHistory  
WHERE RateChangeDate BETWEEN '20011212' AND '20020105';  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
 ```  
 BusinessEntityID RateChangeDate  
 ----------- -----------------------  
 3           2001-12-12 00:00:00.000  
 4           2002-01-05 00:00:00.000  
 ```  
 
 查詢會擷取預期的資料列，因為已指定查詢中的日期值以及儲存在 `RateChangeDate` 資料行中的 **datetime** 值不含日期的時間部分。 未指定時間部分時，它會預設為上午 12:00。 請注意，若資料列包含 2002-01-05 上午 12:00 之後的時間部分， 此查詢將不會傳回該資料列，因為它是在範圍之外。  
  
  
## <a name="see-also"></a>另請參閱  
 [&#62; &#40;大於&#41; &#40;Transact-SQL&#41;](../../t-sql/language-elements/greater-than-transact-sql.md)   
 [&#60; &#40;小於&#41; &#40;Transact-SQL&#41;](../../t-sql/language-elements/less-than-transact-sql.md)   
 [運算式 &#40;Transact-SQL&#41;](../../t-sql/language-elements/expressions-transact-sql.md)   
 [內建函數 &#40;Transact-SQL&#41;](~/t-sql/functions/functions.md)   
 [運算子 &#40;Transact-SQL&#41;](../../t-sql/language-elements/operators-transact-sql.md)   
 [SELECT &#40;Transact-SQL&#41;](../../t-sql/queries/select-transact-sql.md)   
 [WHERE &#40;Transact-SQL&#41;](../../t-sql/queries/where-transact-sql.md)  
  
  


