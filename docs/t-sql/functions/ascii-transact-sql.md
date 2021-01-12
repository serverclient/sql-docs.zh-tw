---
description: ASCII (Transact-SQL)
title: ASCII (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 11/14/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- ASCII_TSQL
- ASCII
dev_langs:
- TSQL
helpviewer_keywords:
- ASCII function
- characters [SQL Server], ASCII
- code [SQL Server], ASCII
- leftmost character of expression
ms.assetid: 45c2044a-0593-4805-8bae-0fad4bde2e6b
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 456a7fd8184ffeae420229cd7c781c33ee4bdd68
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98089372"
---
# <a name="ascii-transact-sql"></a>ASCII (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

傳回字元運算式最左側字元的 ASCII 字碼值。
  
![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)
  
## <a name="syntax"></a>語法  
  
```syntaxsql
ASCII ( character_expression )  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
*character_expression*  
**char** 或 **varchar** 類型的 [運算式](../../t-sql/language-elements/expressions-transact-sql.md)。
  
## <a name="return-types"></a>傳回類型
 **int**  
  
## <a name="remarks"></a>備註
ASCII 代表「美國訊息交換標準代碼」(**A** merican **S** tandard **C** ode for **I** nformation **I** nterchange)。 它是現代電腦的字元編碼標準。 如需 ASCII 字元清單，請參閱 [ASCII](https://www.wikipedia.org/wiki/ASCII) 的＜可列印字元＞(Printable characters) 一節。

ASCII 是一組 7 位元的字元集。 擴充 ASCII 或高 ASCII 是一組 8 位元的字元集，其不會由 `ASCII` 函式進行處理。 

## <a name="examples"></a>範例 

### <a name="a-this-example-assumes-an-ascii-character-set-and-returns-the-ascii-value-for-6-characters"></a>A. 這個範例假設使用 ASCII 字元集，並傳回 6 個字元的 `ASCII` 值。
  
```sql
SELECT ASCII('A') AS A, ASCII('B') AS B,   
ASCII('a') AS a, ASCII('b') AS b,  
ASCII(1) AS [1], ASCII(2) AS [2];  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
A           B           a           b           1           2  
----------- ----------- ----------- ----------- ----------- -----------  
65          66          97          98          49          50  
```  
  
### <a name="b-this-examples-shows-how-a-7-bit-ascii-value-is-returned-correctly-but-an-8-bit-extended-ascii-value-is-not-handled"></a>B. 此範例會示範如何正確傳回 7 位元 ASCII 值，但並未處理 8 位元的擴充 ASCII 值。

```sql
SELECT ASCII('P') AS [ASCII], ASCII('æ') AS [Extended_ASCII];
```

[!INCLUDE[ssResult](../../includes/ssresult-md.md)]

```
ASCII       Extended_ASCII
----------- --------------
80          195
```

若要驗證上述結果是否對應到正確的字元字碼元素，請搭配 `CHAR` 或 `NCHAR` 函式來使用輸出值：

```sql
SELECT NCHAR(80) AS [CHARACTER], NCHAR(195) AS [CHARACTER];
```

[!INCLUDE[ssResult](../../includes/ssresult-md.md)]

```
CHARACTER CHARACTER
--------- ---------
P         Ã
```

請注意先前結果中，字碼元素 195 的字元是 **Ã**，而非 **æ**。 這是因為 `ASCII` 函式能夠讀取前 7 個位元資料流，但無法讀取額外的位元。 您可以使用 `UNICODE` 函式來找到 `æ` 字元的正確字碼元素，該函式可以傳回正確的字元字碼元素：

```sql
SELECT UNICODE('æ') AS [Extended_ASCII], NCHAR(230) AS [CHARACTER];
```

[!INCLUDE[ssResult](../../includes/ssresult-md.md)]

```
Extended_ASCII CHARACTER
-------------- ---------
230            æ
```

## <a name="see-also"></a>另請參閱
 [CHAR &#40;Transact-SQL&#41;](../../t-sql/functions/char-transact-sql.md)  
 [NCHAR &#40;Transact-SQL&#41;](../../t-sql/functions/nchar-transact-sql.md)  
 [UNICODE &#40;Transact-SQL&#41;](../../t-sql/functions/unicode-transact-sql.md)  
 [字串函數 &#40;Transact-SQL&#41;](../../t-sql/functions/string-functions-transact-sql.md)
  
