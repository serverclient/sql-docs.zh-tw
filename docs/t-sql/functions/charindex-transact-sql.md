---
title: CHARINDEX (Transact-SQL) | Microsoft Docs
description: CHARINDEX 函式的 Transact-SQL 參考。
ms.date: 07/24/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- CHARINDEX
- CHARINDEX_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- expressions [SQL Server], pattern searching
- CHARINDEX function
- pattern searching [SQL Server]
- starting point of expression in character string
ms.assetid: 78c10341-8373-4b30-b404-3db20e1a3ac4
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 1914ae25b1b8f86e66c1f85b4789e7ded66c0f02
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98093742"
---
# <a name="charindex-transact-sql"></a>CHARINDEX (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

此函式會在第二個字元運算式內搜尋一個字元運算式，並傳回第一個運算式的開始位置 (如果找到的話)。
  
![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)
  
## <a name="syntax"></a>語法  
  
```syntaxsql
CHARINDEX ( expressionToFind , expressionToSearch [ , start_location ] )   
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
*expressionToFind*  
包含要尋找之順序的字元 [expression](../../t-sql/language-elements/expressions-transact-sql.md)。 *expressionToFind* 具有 8000 字元限制。
  
*expressionToSearch*  
要搜尋的字元運算式。
  
*start_location*  
搜尋開始的 **integer** 或 **bigint** 運算式。 如果未指定 *start_location*，或者它是負數或零 (0) 值，則搜尋會從 *expressionToSearch* 開頭開始。
  
## <a name="return-types"></a>傳回類型
如果 *expressionToSearch* 具有 **nvarchar(max)** 、**varbinary(max)** 或 **varchar(max)** 資料類型，則為 **bigint**；否則為 **int**。
  
## <a name="remarks"></a>備註  
如果 *expressionToFind* 或 *expressionToSearch* 運算式具有 Unicode 資料類型 (**nchar** 或 **nvarchar**)，但另一個運算式沒有，則 CHARINDEX 函式會將這個其他運算式轉換成 Unicode 資料類型。 CHARINDEX 不得與 **image**、**ntext** 或 **text** 資料類型搭配使用。
  
如果 *expressionToFind* 或 *expressionToSearch* 運算式具有 NULL 值，則 CHARINDEX 會傳回 NULL。
  
如果在 *expressionToSearch* 內找不到 *expressionToFind*，則 CHARINDEX 會傳回 0。
  
CHARINDEX 會根據輸入定序來執行比較。 若要執行指定定序的比較，請使用 COLLATE，將明確定序套用至輸入。
  
傳回的開始位置是以 1 為基準，而不是以 0 為基準。
  
0x0000 (**char(0)** ) 是 Windows 定序中未定義的字元，而且不得包含在 CHARINDEX 中。
  
## <a name="supplementary-characters-surrogate-pairs"></a>補充字元 (Surrogate 字組)  
使用 SC 定序時，*start_location* 和傳回值會將代理字組計算成一個字元，而不是兩個字元。 如需詳細資訊，請參閱 [Collation and Unicode Support](../../relational-databases/collations/collation-and-unicode-support.md)。
  
## <a name="examples"></a>範例  
  
### <a name="a-returning-the-starting-position-of-an-expression"></a>A. 傳回運算式的開始位置  
此範例會在已搜尋字串值變數 `@document` 中搜尋 `bicycle`。
  
```sql
DECLARE @document VARCHAR(64);  
SELECT @document = 'Reflectors are vital safety' +  
                   ' components of your bicycle.';  
SELECT CHARINDEX('bicycle', @document);  
GO  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
-----------   
48            
```  
  
### <a name="b-searching-from-a-specific-position"></a>B. 從特定位置執行搜尋  
此範例會使用選擇性 *start_location* 參數，在已搜尋字串值變數 `@document` 的第五個字元開始搜尋 `vital`。
  
```sql
DECLARE @document VARCHAR(64);  
  
SELECT @document = 'Reflectors are vital safety' +  
                   ' components of your bicycle.';  
SELECT CHARINDEX('vital', @document, 5);  
GO  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
-----------   
16            
  
(1 row(s) affected)  
```  
  
### <a name="c-searching-for-a-nonexistent-expression"></a>C. 搜尋不存在的運算式  
此範例會顯示 CHARINDEX 在 *expressionToSearch* 內找不到 *expressionToFind* 時的結果集。
  
```sql
DECLARE @document VARCHAR(64);  
  
SELECT @document = 'Reflectors are vital safety' +  
                   ' components of your bicycle.';  
SELECT CHARINDEX('bike', @document);  
GO  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
-----------
0
  
(1 row(s) affected)
```
  
### <a name="d-performing-a-case-sensitive-search"></a>D. 執行區分大小寫的搜尋  
此範例會顯示已搜尋字串 `'This is a Test``'` 中字串 `'TEST'` 的區分大小寫搜尋。
  
```sql
USE tempdb;  
GO  
--perform a case sensitive search  
SELECT CHARINDEX ( 'TEST',  
       'This is a Test'  
       COLLATE Latin1_General_CS_AS);  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
-----------
0
```  
  
此範例會顯示 `'This is a Test'` 中字串 `'Test'` 的區分大小寫搜尋。
  
```sql
  
USE tempdb;  
GO  
SELECT CHARINDEX ( 'Test',  
       'This is a Test'  
       COLLATE Latin1_General_CS_AS);  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
-----------
11
```  
  
### <a name="e-performing-a-case-insensitive-search"></a>E. 執行不區分大小寫的搜尋  
此範例會顯示 `'This is a Test'` 中字串 `'TEST'` 的不區分大小寫搜尋。
  
```sql
USE tempdb;  
GO  
SELECT CHARINDEX ( 'TEST',  
       'This is a Test'  
       COLLATE Latin1_General_CI_AS);  
GO  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
-----------
11
```  
  
## <a name="examples-sssdwfull-and-sspdw"></a>範例：[!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 和 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
### <a name="f-searching-from-the-start-of-a-string-expression"></a>F. 從字串運算式的開頭搜尋  
此範例會傳回 `This is a string` 字串中 `is` 字串的第一個位置，並且從 `This is a string` 的位置 1 (第一個字元) 開始進行。
  
```sql
SELECT CHARINDEX('is', 'This is a string');  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
---------
3
```  
  
### <a name="g-searching-from-a-position-other-than-the-first-position"></a>G. 從第一個位置以外的位置執行搜尋  
此範例會傳回 `This is a string` 字串中 `is` 字串的第一個位置，並從位置 4 開始搜尋。
  
```sql
SELECT CHARINDEX('is', 'This is a string', 4);  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
---------
 6
 ```  
  
### <a name="h-results-when-the-string-is-not-found"></a>H. 找不到字串時的結果  
此範例會顯示 CHARINDEX 在已搜尋字串中找不到 *string_pattern* 字串時的傳回值。
  
```sql
SELECT TOP(1) CHARINDEX('at', 'This is a string') FROM dbo.DimCustomer;  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
---------
0
```  
  
## <a name="see-also"></a>另請參閱
 [LEN &#40;Transact-SQL&#41;](../../t-sql/functions/len-transact-sql.md)  
 [PATINDEX &#40;Transact-SQL&#41;](../../t-sql/functions/patindex-transact-sql.md)  
 [字串函數 &#40;Transact-SQL&#41;](../../t-sql/functions/string-functions-transact-sql.md)  
 [+ &#40;字串串連&#41; &#40;Transact-SQL&#41;](../../t-sql/language-elements/string-concatenation-transact-sql.md)  
 [定序與 Unicode 支援](../../relational-databases/collations/collation-and-unicode-support.md)  
  
  


