---
title: += 字串串連
description: 串連兩個字串，並將該字串設定為運算結果。
titleSuffix: SQL Server (Transact-SQL)
ms.custom: seo-lt-2019
ms.date: 12/07/2016
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
dev_langs:
- TSQL
helpviewer_keywords:
- concatenate strings
- string concatenation
- += (concatenate operator)
ms.assetid: 4aaeaab7-9b2b-48e0-8487-04ed672ebcb1
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 9209a237e98cbc6357a3faa824e3517600265148
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98095935"
---
# <a name="-string-concatenation-assignment-transact-sql"></a>+= (字串串連指派) (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  串連兩個字串，並將該字串設定為運算結果。 例如，如果變數 @x 等於 'Adventure'，則 @x += 'Works' 會採用 @x 的原始值、將 'Works' 加入到字串，然後將 @x 設定為 'AdventureWorks' 這個新值。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
expression += expression  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *expression*  
 這是任何字元資料類型的任何有效[運算式](../../t-sql/language-elements/expressions-transact-sql.md)。  
  
## <a name="result-types"></a>結果類型  
 傳回針對變數所定義的資料類型。  
  
## <a name="remarks"></a>備註  
 SET @v1 += 'expression' 相當於 SET @v1 = @v1 + ('expression')。 此外，SET @v1 = @v2 + @v3 + @v4 相當於 SET @v1 = (@v2 + @v3) + @v4。  
  
 += 運算子無法在沒有變數的情況下使用。 例如，下列程式碼將會造成錯誤：  
  
```sql  
SELECT 'Adventure' += 'Works'  
```  
  
## <a name="examples"></a>範例  
### <a name="a-concatenation-using--operator"></a>A. 使用 += 運算子的串連
 下列範例會使用 `+=` 運算子串連。  
  
```sql  
DECLARE @v1 VARCHAR(40);  
SET @v1 = 'This is the original.';  
SET @v1 += ' More text.';  
PRINT @v1;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
 `This is the original. More text.`  
  
### <a name="b-order-of-evaluation-while-concatenating-using--operator"></a>B. 使用 += 運算子進行串連時的評估順序
下列範例會串連多個字串來形成一個長字串，然後嘗試計算最終字串的長度。 此範例示範使用串連運算子時的評估順序和截斷規則。 

```sql
DECLARE @x VARCHAR(4000) = REPLICATE('x', 4000)
DECLARE @z VARCHAR(8000) = REPLICATE('z',8000)
DECLARE @y VARCHAR(max);
 
SET @y = '';
SET @y += @x + @z;
SELECT LEN(@y) AS Y; -- 8000
 
SET @y = '';
SET @y = @y + @x + @z;
SELECT LEN(@y) AS Y; -- 12000
 
SET @y = '';
SET @y = @y +(@x + @z);
SELECT LEN(@y) AS Y; -- 8000
-- or
SET @y = '';
SET @y = @x + @z + @y;
SELECT LEN(@y) AS Y; -- 8000
GO
```
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
 ```
 Y       
 ------- 
 8000 
  
 (1 row(s) affected) 
  
    
 Y       
 ------- 
 12000 
  
 (1 row(s) affected) 

 Y       
 ------- 
 8000 
  
 (1 row(s) affected) 
  
 Y       
 ------- 
 8000 
  
 (1 row(s) affected)
  ```   
   
## <a name="see-also"></a>另請參閱  
 [運算子 &#40;Transact-SQL&#41;](../../t-sql/language-elements/operators-transact-sql.md)   
 [+= &#40;加法指派&#41; &#40;Transact-SQL&#41;](../../t-sql/language-elements/add-equals-transact-sql.md)   
 [+ &#40;字串串連&#41; &#40;Transact-SQL&#41;](../../t-sql/language-elements/string-concatenation-transact-sql.md)  
  
  
