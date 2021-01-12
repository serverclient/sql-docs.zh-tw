---
description: + (一元加法) (Transact-SQL)
title: + (一元加法) (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/13/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- + (positive)
- +
- positive
dev_langs:
- TSQL
helpviewer_keywords:
- + (positive operator)
- positive operator (+)
- positive values [SQL Server]
ms.assetid: 0f31c5cc-3078-4f6a-9870-7eb1a98053fb
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 70dae64868be97cf243a788075a1b84c37872b31
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98095899"
---
# <a name="unary-operators---positive"></a>一元加法 - 正數
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa.md)]

傳回數值運算式 (一元運算子) 的值。 一元運算子只能在屬於數值資料類型類別目錄之任何資料類型的單一運算式上執行運算。   
  
|運算子|意義|  
|--------------|-------------|  
|[+ (正)](../../t-sql/language-elements/unary-operators-positive.md)|數值是正的。|  
|[- (負)](../../t-sql/language-elements/unary-operators-negative.md)|數值是負的。|  
|[~ (位元 NOT)](../../t-sql/language-elements/bitwise-not-transact-sql.md)|傳回數字的一補數。|  
  
 屬於數值資料類型類別目錄之任一資料類型的任何有效運算式，都可以使用 + (正) 和 - (負) 運算子。 ~ (位元 NOT) 運算子只能用在屬於整數資料類型類別目錄之任一資料類型的運算式上。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
+ numeric_expression  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *numeric_expression*  
 這是數值資料類型類別目錄中任何一個資料類型的任何有效 [運算式](../../t-sql/language-elements/expressions-transact-sql.md)，但是 **datetime** 和 **smalldatetime** 資料類型除外。  
  
## <a name="result-types"></a>結果類型  
 傳回 *numeric_expression* 的資料類型。  
  
## <a name="remarks"></a>備註  
 雖然一元加號可以出現在任何數值運算式之前，但從運算式傳回的值，它並不會做任何處理。 明確地說，如果運算式是負的，它便不會傳回正值。 如果負運算式要傳回正值，請使用 [ABS](../../t-sql/functions/abs-transact-sql.md) 函數。  
  
## <a name="examples"></a>範例  
  
### <a name="a-setting-a-variable-to-a-positive-value"></a>A. 將變數設為正值  
 下列範例會將變數設為正值。  
  
```sql  
DECLARE @MyNumber DECIMAL(10,2);  
SET @MyNumber = +123.45;  
SELECT @MyNumber;  
GO  
```  
  
 以下為結果集：  
  
```  
-----------   
123.45            
  
(1 row(s) affected)  
```  
  
### <a name="b-using-the-unary-plus-operator-with-a-negative-value"></a>B. 在負值上使用一元加號運算子  
 下列範例會顯示在相同的負運算式上使用一元加號和 ABS() 函數。 一元加號不會影響運算式，ABS 函數會傳回運算式的正值。  
  
```sql  
USE tempdb;  
GO  
DECLARE @Num1 INT;  
SET @Num1 = -5;  
SELECT +@Num1, ABS(@Num1);  
GO  
```  
  
 以下為結果集：  
  
```  
----------- -----------  
-5          5  
  
(1 row(s) affected)  
```  
  
## <a name="see-also"></a>另請參閱  
 [資料類型 &#40;Transact-SQL&#41;](../../t-sql/data-types/data-types-transact-sql.md)   
 [運算式 &#40;Transact-SQL&#41;](../../t-sql/language-elements/expressions-transact-sql.md)   
 [運算子 &#40;Transact-SQL&#41;](../../t-sql/language-elements/operators-transact-sql.md)   
 [ABS &#40;Transact-SQL&#41;](../../t-sql/functions/abs-transact-sql.md)  
  
  
