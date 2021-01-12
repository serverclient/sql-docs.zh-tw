---
description: AND (Transact-SQL)
title: AND (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- AND_TSQL
- AND
dev_langs:
- TSQL
helpviewer_keywords:
- values [SQL Server], TRUE
- "TRUE"
- AND, about AND operators
- AND
- combining expressions
ms.assetid: b61d7f8d-5a51-49b7-91dd-f6190a5a0fb9
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 412344918629de3fa9b3ea1dbf90f41f4e343bdb
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98101031"
---
# <a name="and-transact-sql"></a>AND (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  結合兩個布林運算式，並在這兩個運算式都是 **TRUE** 時，傳回 **TRUE**。 在陳述式中使用一個以上的邏輯運算子時，會先評估 **AND** 運算子。 您可以使用括號來變更驗算的順序。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
boolean_expression AND boolean_expression  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *boolean_expression*  
 這是會傳回下列任一布林值的任何有效 [運算式](../../t-sql/language-elements/expressions-transact-sql.md)：**TRUE**、**FALSE** 或 **UNKNOWN**。  
  
## <a name="result-types"></a>結果類型  
 **布林值**  
  
## <a name="result-value"></a>結果值  
 當兩個運算式都是 TRUE 時，便傳回 TRUE。  
  
## <a name="remarks"></a>備註  
 下圖顯示利用 AND 運算子比較 TRUE 和 FALSE 值的結果。  
  
||true|false|UNKNOWN|  
|------|----------|-----------|-------------|  
|**TRUE**|true|false|UNKNOWN|  
|**FALSE**|FALSE|FALSE|false|  
|**UNKNOWN**|UNKNOWN|FALSE|UNKNOWN|  
  
## <a name="examples"></a>範例  
  
### <a name="a-using-the-and-operator"></a>A. 使用 AND 運算子  
 下列範例會選取職稱為 `Marketing Assistant` 而且可用休假時數超過 `41` 之員工的相關資訊。  
  
```sql  
-- Uses AdventureWorks  
  
SELECT  BusinessEntityID, LoginID, JobTitle, VacationHours   
FROM HumanResources.Employee  
WHERE JobTitle = 'Marketing Assistant'  
AND VacationHours > 41 ;  
```  
  
### <a name="b-using-the-and-operator-in-an-if-statement"></a>B. 在 IF 陳述式中使用 AND 運算子  
 下列範例將示範如何在 IF 陳述式中使用 AND。 在第一個陳述式中，`1 = 1` 和 `2 = 2` 都是 true。因此，結果為 true。 在第二個範例中，引數 `2 = 17` 是 false。因此，結果為 false。  
  
```sql  
IF 1 = 1 AND 2 = 2  
BEGIN  
   PRINT 'First Example is TRUE'  
END  
ELSE PRINT 'First Example is FALSE' ;  
GO  
  
IF 1 = 1 AND 2 = 17  
BEGIN  
   PRINT 'Second Example is TRUE'  
END  
ELSE PRINT 'Second Example is FALSE' ;  
GO  
```  
  
## <a name="see-also"></a>另請參閱  
 [內建函數 &#40;Transact-SQL&#41;](~/t-sql/functions/functions.md)   
 [運算子 &#40;Transact-SQL&#41;](../../t-sql/language-elements/operators-transact-sql.md)   
 [SELECT &#40;Transact-SQL&#41;](../../t-sql/queries/select-transact-sql.md)   
 [WHERE &#40;Transact-SQL&#41;](../../t-sql/queries/where-transact-sql.md)  
  
  
