---
description: 反斜線 (行接續符號) (Transact-SQL)
title: 反斜線 (行接續符號) (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 07/25/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- '\_TSQL'
- '\'
dev_langs:
- TSQL
helpviewer_keywords:
- backwhack
- backslash
- escape character
- hack character
- '\ (backslash)'
- backslant
- bash
- reverse slant
- slosh
- reversed virgule
- line continuation character
- reverse solidus
ms.assetid: c97fbb20-3d12-4d0b-9b52-62a229bc83c0
author: cawrites
ms.author: chadam
ms.openlocfilehash: 3451d6a9346fd1988cdfc509635967017a0b6cb9
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98095910"
---
# <a name="backslash-line-continuation-transact-sql"></a>反斜線 (行接續符號) (Transact-SQL)

[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

`\` 會將長的字串常數、字元或二進位分成兩行或多行，以提高可讀性。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql  
<first section of string> \  
<continued section of string>  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 \<first section of string>  
 這是字串的開頭。  
  
 \<continued section of string>  
 這是字串的接續。  
  
## <a name="remarks"></a>備註  
這個命令會將字串的第一個區段和接續區段當做一個字串傳回，但不含反斜線。 反斜線之後的新行必須是換行字元 (U+000A) 或是歸位字元 (U+000D) 和換行字元 (U+000A) (依照此順序) 的組合。 

## <a name="examples"></a>範例  

### <a name="a-splitting-a-character-string"></a>A. 分割字元字串  

下列範例會使用反斜線和歸位字元，將字元字串分成兩行。  
  
```sql  
SELECT 'abc\  
def' AS [ColumnResult];  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
 ```  
 ColumnResult  
 ------------  
 abcdef
 ```    

### <a name="b-splitting-a-binary-string"></a>B. 分割二進位字串  

下列範例會使用反斜線和歸位字元，將二進位字串分成兩行。  

```sql  
SELECT 0xabc\
def AS [ColumnResult];  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
 ```  
 ColumnResult  
 ------------  
 0xABCDEF
 ```    

## <a name="see-also"></a>另請參閱  
 [資料類型 &#40;Transact-SQL&#41;](../../t-sql/data-types/data-types-transact-sql.md)   
 [內建函數 &#40;Transact-SQL&#41;](~/t-sql/functions/functions.md)   
 [運算子 &#40;Transact-SQL&#41;](../../t-sql/language-elements/operators-transact-sql.md)   
 [&#40;除法&#41; &#40;Transact-SQL&#41;](../../t-sql/language-elements/divide-transact-sql.md)   
 [&#40;除法指派&#41; &#40;Transact-SQL&#41;](../../t-sql/language-elements/divide-equals-transact-sql.md)   
 [複合運算子 &#40;Transact-SQL&#41;](../../t-sql/language-elements/compound-operators-transact-sql.md)  
  
  
