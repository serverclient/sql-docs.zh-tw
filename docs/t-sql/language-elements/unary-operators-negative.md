---
description: '- (負) (Transact-SQL)'
title: '- (負) (Transact-SQL) | Microsoft Docs'
ms.custom: ''
ms.date: 03/13/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- negative
dev_langs:
- TSQL
helpviewer_keywords:
- '- (negative)'
- negative operator (-)
- negative values
ms.assetid: d6c14d14-d379-403b-82db-c197ad58c896
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 2aa9a8d25fcd33534f8d269983ead2e381541760
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98095933"
---
# <a name="unary-operators---negative"></a>一元運算子 - 負
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  傳回數值運算式 (一元運算子) 的負值。 一元運算子只能在屬於數值資料類型類別目錄之任何資料類型的單一運算式上執行運算。   
  
|運算子|意義|  
|--------------|-------------|  
|[+ (正)](../../t-sql/language-elements/unary-operators-positive.md)|數值是正的。|  
|[- (負)](../../t-sql/language-elements/unary-operators-negative.md)|數值是負的。|  
|[~ (位元 NOT)](../../t-sql/language-elements/bitwise-not-transact-sql.md)|傳回數字的一補數。|  
  
 屬於數值資料類型類別目錄之任一資料類型的任何有效運算式，都可以使用 + (正) 和 - (負) 運算子。 ~ (位元 NOT) 運算子只能用在屬於整數資料類型類別目錄之任一資料類型的運算式上。 
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
- numeric_expression  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *numeric_expression*  
 這是在日期和時間類別目錄以外，屬於數值資料類型類別目錄之任何資料類型的任何有效[運算式](../../t-sql/language-elements/expressions-transact-sql.md)。  
  
## <a name="result-types"></a>結果類型  
 除了不帶正負號的 **tinyint** 運算式升級為帶正負號 **smallint** 結果，傳回 *numeric_expression* 的資料類型。  
  
## <a name="examples"></a>範例  
  
### <a name="a-setting-a-variable-to-a-negative-value"></a>A. 將變數設為負值  
 下列範例會將變數設為負值。  
  
```sql 
USE tempdb;  
GO  
DECLARE @MyNumber DECIMAL(10,2);  
SET @MyNumber = -123.45;  
SELECT @MyNumber AS NegativeValue;  
GO  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
NegativeValue  
---------------------------------------  
-123.45  
  
(1 row(s) affected)  
  
```  
  
### <a name="b-changing-a-variable-to-a-negative-value"></a>B. 將變數變更為負值  
 下列範例會將變數改成負值。  
  
```sql  
USE tempdb;  
GO  
DECLARE @Num1 INT;  
SET @Num1 = 5;  
SELECT @Num1 AS VariableValue, -@Num1 AS NegativeValue;  
GO  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
VariableValue NegativeValue  
------------- -------------  
5             -5  
  
(1 row(s) affected)  
  
```  
  
## <a name="examples-sssdwfull-and-sspdw"></a>範例：[!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 和 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
### <a name="c-returning-the-negative-of-a-positive-constant"></a>C. 傳回正常數的負值  
 下列範例會傳回正常數的負值。  
  
```sql  
USE ssawPDW;  
  
SELECT TOP (1) - 17 FROM DimEmployee;  
```  
  
 傳回  
  
```  
-17  
```  
  
### <a name="d-returning-the-positive-of-a-negative-constant"></a>D. 傳回負常數的正值  
 下列範例會傳回負常數的正值。  
  
```sql  
USE ssawPDW;  
  
SELECT TOP (1) - ( - 17) FROM DimEmployee;  
```  
  
 傳回  
  
```  
17  
```  
  
### <a name="e-returning-the-negative-of-a-column"></a>E. 傳回資料行的負值  
 下列範例會傳回 `dimEmployee` 資料表中每一位員工 `BaseRate` 值的負值。  
  
```sql  
USE ssawPDW;  
  
SELECT - BaseRate FROM DimEmployee;  
```  
  
## <a name="see-also"></a>另請參閱  
 [資料類型 &#40;Transact-SQL&#41;](../../t-sql/data-types/data-types-transact-sql.md)   
 [運算式 &#40;Transact-SQL&#41;](../../t-sql/language-elements/expressions-transact-sql.md)   
 [運算子 &#40;Transact-SQL&#41;](../../t-sql/language-elements/operators-transact-sql.md)  
  
  

