---
description: CHAR (Transact-SQL)
title: CHAR (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 10/19/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- char_TSQL
- char
dev_langs:
- TSQL
helpviewer_keywords:
- converting int ASCII code to character
- control characters
- tab
- ASCII conversions
- CHAR function
- carriage return
- inserting control characters
- characters [SQL Server], control
- line feed
- printing ASCII values
ms.assetid: 955afe94-539c-465d-af22-16ec45da432a
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 4c0281782b3a21c809ee725d4dd99d80fbad20ec
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98097284"
---
# <a name="char-transact-sql"></a>CHAR (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

此函式會將 **int** ASCII 代碼轉換成字元值。
  
![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)
  
## <a name="syntax"></a>語法  
  
```syntaxsql
CHAR ( integer_expression )  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
*integer_expression*  
0 到 255 之間的整數。 `CHAR` 會針對此範圍以外的整數運算式傳回 `NULL` 值，或當整數運算式只包含雙位元組字元的第一個位元組時也會傳回此值。

> [!NOTE]
> 一些非歐洲字元集 (例如 [Shift Japanese Industrial Standards](https://www.wikipedia.org/wiki/Shift_JIS)) 包括能以單位元組編碼配置表示的字元，但需要多位元組編碼。 如需有關字元集的詳細資訊，請參閱[單位元組和多位元組字元集](/cpp/c-runtime-library/single-byte-and-multibyte-character-sets)。 
  
## <a name="return-types"></a>傳回類型
**char(1)**
  
## <a name="remarks"></a>備註  
使用 `CHAR` 將控制字元插入字元字串。 此表顯示一些常用的控制字元。
  
|控制字元|值|  
|---|---|
|索引標籤|**char(9)**|  
|換行字元|**char(10)**|  
|歸位字元|**char(13)**|  
  
## <a name="examples"></a>範例  
  
### <a name="a-using-ascii-and-char-to-print-ascii-values-from-a-string"></a>A. 利用 ASCII 和 CHAR 來列印字串中的 ASCII 值  
此範例會列印 `New Moon` 字串中每個字元的 ASCII 值和字元。
  
```sql
SET TEXTSIZE 0;  
-- Create variables for the character string and for the current   
-- position in the string.  
DECLARE @position INT, @string CHAR(8);  
-- Initialize the current position and the string variables.  
SET @position = 1;  
SET @string = 'New Moon';  
WHILE @position <= DATALENGTH(@string)  
   BEGIN  
   SELECT ASCII(SUBSTRING(@string, @position, 1)),   
      CHAR(ASCII(SUBSTRING(@string, @position, 1)))  
   SET @position = @position + 1  
   END;  
GO  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
----------- -
78          N  
----------- -  
101         e  
----------- -  
119         w  
----------- -  
32  
----------- -  
77          M  
----------- -  
111         o  
----------- -  
111         o  
----------- - 
110         n  
```
  
### <a name="b-using-char-to-insert-a-control-character"></a>B. 利用 CHAR 來插入控制字元  
此範例在查詢將其結果傳回為文字時，使用 `CHAR(13)` 於個別行中列印員工的名稱和電子郵件地址。 這個範例會使用 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 資料庫。
  
```sql
SELECT p.FirstName + ' ' + p.LastName, + CHAR(13)  + pe.EmailAddress   
FROM Person.Person p 
INNER JOIN Person.EmailAddress pe ON p.BusinessEntityID = pe.BusinessEntityID  
  AND p.BusinessEntityID = 1;  
GO  
```
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
Ken Sanchez
ken0@adventure-works.com
  
(1 row(s) affected)
```
  
### <a name="c-using-ascii-and-char-to-print-ascii-values-from-a-string"></a>C. 利用 ASCII 和 CHAR 來列印字串中的 ASCII 值  
此範例採用 ASCII 字元集。 它會傳回六個不同 ASCII 字元數字值的字元值。
  
```sql
SELECT CHAR(65) AS [65], CHAR(66) AS [66],   
CHAR(97) AS [97], CHAR(98) AS [98],   
CHAR(49) AS [49], CHAR(50) AS [50];  
```
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
65   66   97   98   49   50  
---- ---- ---- ---- ---- ----  
A    B    a    b    1    2  
```
  
### <a name="d-using-char-to-insert-a-control-character"></a>D. 利用 CHAR 來插入控制字元  
此範例在查詢將其結果傳回為文字時，使用 `CHAR(13)` 於個別行中傳回 sys.databases 的資訊。
  
```sql
SELECT name, 'was created on ', create_date, CHAR(13), name, 'is currently ', state_desc   
FROM sys.databases;  
GO  
```
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
name                                      create_date               name                                  state_desc  
--------------------------------------------------------------------------------------------------------------------  
master                    was created on  2003-04-08 09:13:36.390   master                  is currently  ONLINE 
tempdb                    was created on  2014-01-10 17:24:24.023   tempdb                  is currently  ONLINE   
AdventureWorksPDW2012     was created on  2014-05-07 09:05:07.083   AdventureWorksPDW2012   is currently  ONLINE 
```

### <a name="e-using-char-to-return-single-byte-characters"></a>E. 使用 CHAR 來傳回單位元組字元  
此範例使用 ASCII 有效範圍的整數與十六進位值。 CHAR 函式可輸出單位元組日文字元。
  
```sql
SELECT CHAR(188) AS single_byte_representing_complete_character, 
  CHAR(0xBC) AS single_byte_representing_complete_character;  
GO  
```
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
single_byte_representing_complete_character single_byte_representing_complete_character
------------------------------------------- -------------------------------------------
ｼ                                           ｼ                                         
```

### <a name="f-using-char-to-return-multibyte-characters"></a>F. 使用 CHAR 來傳回多位元組字元  
此範例使用 ASCII 有效範圍的整數與十六進位值。 不過，CHAR 函式會傳回 NUL，因為參數只代表多位元組字元的第一個位元組。
  
```sql
SELECT CHAR(129) AS first_byte_of_double_byte_character, 
  CHAR(0x81) AS first_byte_of_double_byte_character;  
GO  
```
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
first_byte_of_double_byte_character first_byte_of_double_byte_character
----------------------------------- -----------------------------------
NULL                                NULL                                         
```
  
## <a name="see-also"></a>請參閱
 [ASCII &#40;Transact-SQL&#41;](../../t-sql/functions/ascii-transact-sql.md)  
 [NCHAR &#40;Transact-SQL&#41;](../../t-sql/functions/nchar-transact-sql.md)  
 [UNICODE &#40;Transact-SQL&#41;](../../t-sql/functions/unicode-transact-sql.md)  
 [+ &#40;字串串連&#41; &#40;Transact-SQL&#41;](../../t-sql/language-elements/string-concatenation-transact-sql.md)  
 [字串函數 &#40;Transact-SQL&#41;](../../t-sql/functions/string-functions-transact-sql.md)
  
  

