---
description: 邏輯函式 - IIF (Transact-SQL)
title: IIF (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- IIF_TSQL
- IIF
dev_langs:
- TSQL
helpviewer_keywords:
- IIF function
ms.assetid: e3ccf8ed-1cec-43ac-90b7-d8597c24b050
author: cawrites
ms.author: chadam
ms.openlocfilehash: 49701ce47afa3baeae4152d0b4be0b6ad7efaa5f
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98100398"
---
# <a name="logical-functions---iif-transact-sql"></a>邏輯函式 - IIF (Transact-SQL)
[!INCLUDE [SQL Server Azure SQL Database ](../../includes/applies-to-version/sql-asdb.md)]

  在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中根據布林運算式評估為 true 或 false 而傳回兩值之一。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
IIF ( boolean_expression, true_value, false_value )  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *boolean_expression*  
 有效的布林運算式。  
  
 此引數若不是布林運算式，將會引發語法錯誤。  
  
 *true_value*  
 *boolean_expression* 評估為 true 時，要傳回的值。  
  
 *false_value*  
 *boolean_expression* 評估為 false 時，要傳回的值。  
  
## <a name="return-types"></a>傳回型別  
 從 *true_value* 及 *false_value* 的類型中，傳回優先順序最高的資料類型。 如需詳細資訊，請參閱[資料類型優先順序 &#40;Transact-SQL&#41;](../../t-sql/data-types/data-type-precedence-transact-sql.md)。  
  
## <a name="remarks"></a>備註  
 IIF 是一種編寫 CASE 運算式的簡略方法。 其會求得第一個引數所傳遞之布林運算式的解，然後依據求解結果，傳回另外兩個引數之一。 亦即，如果布林運算式為 true，則會傳回 *true_value* 如果布林運算式為 false 或不明，則會傳回 *false_value*。 *true_value* 和 *false_value* 可為任何類型。 套用到布林運算式、null 處理及傳回類型之 CASE 運算式的規則也同樣會套用至 IIF。 如需詳細資訊，請參閱 [CASE &#40;Transact-SQL&#41;](../../t-sql/language-elements/case-transact-sql.md)。  
  
 IIF 轉換為 CASE 的事實，對此函數之行為的其他層面也有影響。 由於 IIF 陳述式最多只可巢狀化到層級 10，因此 CASE 運算式最多也只可巢狀化至層級 10。 此外，IIF 會以語意相等之 CASE 運算式，並以遠端處理之 CASE 運算式的所有行為，從遠端處理到其他伺服器。  
  
## <a name="examples"></a>範例  
  
### <a name="a-simple-iif-example"></a>A. 簡單的 IIF 範例  
  
```sql  
DECLARE @a INT = 45, @b INT = 40;  
SELECT IIF ( @a > @b, 'TRUE', 'FALSE' ) AS Result;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
Result  
--------  
TRUE  
  
(1 row(s) affected)  
```  
  
### <a name="b-iif-with-null-constants"></a>B. 包含 NULL 常數的 IIF  
  
```sql 
SELECT IIF ( 45 > 30, NULL, NULL ) AS Result;  
```  
  
 此陳述式的結果為錯誤。  
  
### <a name="c-iif-with-null-parameters"></a>C. 包含 NULL 參數的 IIF  
  
```sql  
DECLARE @P INT = NULL, @S INT = NULL;  
SELECT IIF ( 45 > 30, @p, @s ) AS Result;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
Result  
--------  
NULL  
  
(1 row(s) affected)  
```  
  
## <a name="see-also"></a>另請參閱  
 [CASE &#40;Transact-SQL&#41;](../../t-sql/language-elements/case-transact-sql.md)   
 [CHOOSE &#40;Transact-SQL&#41;](../../t-sql/functions/logical-functions-choose-transact-sql.md)  
  
  
