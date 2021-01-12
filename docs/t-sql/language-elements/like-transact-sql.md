---
description: LIKE (Transact-SQL)
title: LIKE (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- ESCAPE
- LIKE
- ESCAPE_TSQL
- LIKE_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- ESCAPE keyword
- '% (wildcard - character(s) to match)'
- ASCII pattern matching
- pattern searching [SQL Server]
- wildcard characters [SQL Server]
- literals [SQL Server], using wildcards
- character strings [SQL Server], LIKE
- exact matches [SQL Server]
- Boolean functions
- LIKE comparisons
- matching patterns [SQL Server]
- NOT LIKE keyword
ms.assetid: 581fb289-29f9-412b-869c-18d33a9e93d5
author: julieMSFT
ms.author: jrasnick
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: fd3f8c22b0087342b5d6b72ac492a3d542537c97
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98084333"
---
# <a name="like-transact-sql"></a>LIKE (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  判斷特定字元字串是否符合指定的模式。 模式中可以包含一般字元及萬用字元。 在模式比對期間，一般字元必須與字元字串中所指定的字元完全相符。 不過，萬用字元可以符合任意字元字串片段。 使用萬用字元要比使用 = 與 != 字串比較運算子能讓 LIKE 運算子更有彈性。 如果有任何一個引數不是字元字串資料類型，[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 會將它轉換成字元字串資料類型 (若可能的話)。  
  
 ![文章連結圖示](../../database-engine/configure-windows/media/topic-link.gif "文章連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
-- Syntax for SQL Server and Azure SQL Database  
  
match_expression [ NOT ] LIKE pattern [ ESCAPE escape_character ]  
```  
  
```syntaxsql
-- Syntax for Azure Synapse Analytics and Parallel Data Warehouse  
  
match_expression [ NOT ] LIKE pattern  
```  
>[!NOTE]
> [!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] 或 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] 中目前不支援 ESCAPE 和 STRING_ESCAPE。

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *match_expression*  
 這是字元資料類型的任何有效[運算式](../../t-sql/language-elements/expressions-transact-sql.md)。  
  
 *模式*  
 這是要在 *match_expression* 中搜尋的特定字元字串，可包含下列有效的萬用字元。 *pattern* 最多可有 8,000 個位元組。  
  
|萬用字元|描述|範例|  
|------------------------|-----------------|-------------|  
|%|任何含有零或多個字元的字串。|WHERE title LIKE '%computer%' 可找出書名中含有 'computer' 這個字的所有書名。|  
|_ (底線)|任何單一字元。|WHERE au_fname LIKE '_ean' 可找出所有以 ean 結尾的四個字母的名字 (如 Dean、Sean 等)。|  
|[ ]|在指定範圍 ([a-f]) 或集合 ([abcdef]) 中的任何單一字元。|WHERE au_lname LIKE '[C-P]arsen' 可找出結尾是 arsen，開頭是 C 和 P 之間的任何單一字元的作者姓氏，例如，Carsen、Larsen、Karsen 等等。 在範圍搜尋中，範圍所包含的字元可能會因定序的排序規則而不同。|  
|[^]|不在指定範圍 ([^a-f]) 或集合 ([^abcdef]) 中的任何單一字元。|WHERE au_lname LIKE 'de[^l]%' 所有開頭是 de 且下一個字元不是 l 的作者姓氏。|  
  
 *escape_character*  
 這是放在萬用字元前面的字元，指出應將萬用字元解譯成一般字元，而不是萬用字元。 *escape_character* 是沒有預設值的字元運算式，且只能得出一個字元。  
  
## <a name="result-types"></a>結果類型  
 **布林值**  
  
## <a name="result-value"></a>結果值  
 如果 *match_expression* 符合指定的 *pattern*，LIKE 就會傳回 TRUE。  
  
## <a name="remarks"></a>備註  
 當您使用 LIKE 執行字串比較時，模式字串中的所有字元都很重要。 重要字元會包含任何前置或後置的空格。 如果查詢中某個比較會傳回具有字串 LIKE 'abc ' (abc 後面跟著一個空格) 的所有資料列，則不會傳回該資料行之值為 abc (abc 後面沒有空格) 的資料列。 不過，在要比對模式的運算式中，會忽略尾端空白。 如果查詢中的某個比較會傳回具有字串 LIKE 'abc' (abc 後面沒有空格) 的所有資料列，則開頭為 abc，不管是否有尾端空格的資料列都會被傳回。  
  
 使用模式包含 **char** 和 **varchar** 資料的字串比較，可能會因為每個資料類型的資料儲存方式而無法通過 LIKE 比較。 下列範例會將區域 **char** 變數傳遞給預存程序，然後使用模式比對來尋找姓氏開頭為一組指定字元的所有員工。  
  
```sql
-- Uses AdventureWorks  
  
CREATE PROCEDURE FindEmployee @EmpLName CHAR(20)  
AS  
SELECT @EmpLName = RTRIM(@EmpLName) + '%';  
SELECT p.FirstName, p.LastName, a.City  
FROM Person.Person p JOIN Person.Address a ON p.BusinessEntityID = a.AddressID  
WHERE p.LastName LIKE @EmpLName;  
GO  
EXEC FindEmployee @EmpLName = 'Barb';  
GO  
```  
  
 在 `FindEmployee` 程序中，不會傳回任何資料列，因為每當名稱包含的字元少於 20 個時，**char** 變數 (`@EmpLName`) 就會包含尾端空白。 由於 `LastName` 資料行是 **varchar**，因此，沒有尾端空白。 因為有尾端空白，這個程序才會失敗。  
  
 但是，下列範例會成功，因為沒有將後置空白字元新增到 **varchar** 變數。  
  
```sql
-- Uses AdventureWorks  
  
CREATE PROCEDURE FindEmployee @EmpLName VARCHAR(20)  
AS  
SELECT @EmpLName = RTRIM(@EmpLName) + '%';  
SELECT p.FirstName, p.LastName, a.City  
FROM Person.Person p JOIN Person.Address a ON p.BusinessEntityID = a.AddressID  
WHERE p.LastName LIKE @EmpLName;  
GO  
EXEC FindEmployee @EmpLName = 'Barb';  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]  

```
FirstName      LastName            City
----------     -------------------- --------------- 
Angela         Barbariol            Snohomish
David          Barber               Snohomish
(2 row(s) affected)  
```

## <a name="pattern-matching-by-using-like"></a>利用 LIKE 來進行模式比對  
 LIKE 支援 ASCII 模式比對和 Unicode 模式比對。 當所有引數 (*match_expression*、*pattern* 與 *escape_character*，如果有的話) 都是 ASCII 字元資料類型時，就會執行 ASCII 模式比對。 如果有任何引數不是 Unicode 資料類型，所有引數都會轉換成 Unicode，且會執行 Unicode 模式比對。 當您搭配 LIKE 使用 Unicode 資料 (**nchar** 或 **nvarchar** 資料類型) 時，尾端空白很重要；不過，針對非 Unicode 資料，尾端空白就不重要。 Unicode LIKE 與 ISO 標準相容。 ASCII LIKE 與舊版的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 相容。  
  
 下列範例會顯示 ASCII 和 Unicode LIKE 模式比對所傳回之資料列的差異。  
  
```sql  
-- ASCII pattern matching with char column  
CREATE TABLE t (col1 CHAR(30));  
INSERT INTO t VALUES ('Robert King');  
SELECT *   
FROM t   
WHERE col1 LIKE '% King';   -- returns 1 row  
  
-- Unicode pattern matching with nchar column  
CREATE TABLE t (col1 NCHAR(30));  
INSERT INTO t VALUES ('Robert King');  
SELECT *   
FROM t   
WHERE col1 LIKE '% King';   -- no rows returned  
  
-- Unicode pattern matching with nchar column and RTRIM  
CREATE TABLE t (col1 NCHAR(30));  
INSERT INTO t VALUES ('Robert King');  
SELECT *   
FROM t   
WHERE RTRIM(col1) LIKE '% King';   -- returns 1 row  
```
  
> [!NOTE]  
>  LIKE 比較會受定序影響。 如需詳細資訊，請參閱 [COLLATE &#40;Transact-SQL&#41;](~/t-sql/statements/collations.md)。  
  
## <a name="using-the--wildcard-character"></a>使用 % 萬用字元  
 如果指定了 LIKE '5%' 符號，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 會搜尋數字 5，後面接著零或多個字元的任何字串。  
  
 例如，下列查詢會顯示 [!INCLUDE[ssSampleDBobject](../../includes/sssampledbobject-md.md)] 資料庫中的所有動態管理檢視，因為它們的開頭全是 `dm` 字母。  
  
```sql  
-- Uses AdventureWorks  
  
SELECT Name  
FROM sys.system_views  
WHERE Name LIKE 'dm%';  
GO  
```  
  
 若要查看不是動態管理檢視的所有物件，請使用 `NOT LIKE 'dm%'`。 如果您一共有 32 個物件，而 LIKE 找出了 13 個模式相符的名稱，則 NOT LIKE 會找出 19 個與 LIKE 模式不相符的物件。  
  
 利用 `LIKE '[^d][^m]%'` 之類的模式，不一定都會找到相同名稱。 您可能只會找到 14 個名稱以及動態管理檢視名稱，而不是 19 個名稱，結果會刪除開頭是 `d` 或第二個字母是 `m` 的所有名稱。 此行為是因為含有負面萬用字元的相符字串會逐步評估，每次一個萬用字元。 如果評估中的任何一點比對失敗，就會消除它。  
  
## <a name="using-wildcard-characters-as-literals"></a>使用萬用字元作為常值  
 您可以使用萬用字元模式比對字元作為常值字元。 若要使用萬用字元作為常值字元，請用括號將萬用字元括住。 下表顯示若干使用 LIKE 關鍵字及 [ ] 萬用字元的範例。  
  
|符號|意義|  
|------------|-------------|  
|LIKE '5[%]'|5%|  
|LIKE '[_]n'|_n|  
|LIKE '[a-cdf]'|a、b、c、d 或 f|  
|LIKE '[-acdf]'|-、a、c、d 或 f|  
|LIKE '[ [ ]'|[|  
|LIKE ']'|]|  
|LIKE 'abc[_]d%'|abc_d 和 abc_de|  
|LIKE 'abc[def]'|abcd、abce 和 abcf|  
  
## <a name="pattern-matching-with-the-escape-clause"></a>含 ESCAPE 子句的模式比對  
 您可以搜尋包括一個或多個特殊萬用字元的字元字串。 例如，customers 資料庫中的 discounts 資料表可能會儲存包括百分比符號 (%) 的折扣值。 若要將百分比符號當做字元而不是萬用字元來搜尋，您必須提供 ESCAPE 關鍵字和逸出字元。 例如，包含名稱為 comment 的資料行且資料行中有 30% 這個文字的範例資料庫。 若要搜尋 comment 資料行的任何位置含有 30% 這個字串的任何資料列，請指定 WHERE 子句，例如 `WHERE comment LIKE '%30!%%' ESCAPE '!'`。 如果未指定 ESCAPE 和逸出字元，則 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 會傳回任何含有 30! 這個字串的資料列。  
  
 如果 LIKE 模式中逸出字元之後沒有任何字元，模式便無效，且 LIKE 會傳回 FALSE。 如果逸出字元之後的字元不是萬用字元，就會捨棄萬用字元，並將之後的字元當作一般字元來處理。 這些字元包括用一組左右括弧 ([ ]) 括住的百分比符號 (%)、底線 (_) 和左括弧 ([) 萬用字元。 逸出字元也可以在左右括弧字元 ([ ]) 中使用，包括用來逸出插入號 (^)、連字號 (-) 或右括弧 (])。  
  
 0x0000 (**char(0)**) 是 Windows 定序中未定義的字元，且不得包含在 LIKE 中。  
  
## <a name="examples"></a>範例  
  
### <a name="a-using-like-with-the--wildcard-character"></a>A. 使用 LIKE 搭配 % 萬用字元  
 下列範例會在 `415` 資料表中，尋找區域碼是 `PersonPhone` 的所有電話號碼。  
  
```sql  
-- Uses AdventureWorks  
  
SELECT p.FirstName, p.LastName, ph.PhoneNumber  
FROM Person.PersonPhone AS ph  
INNER JOIN Person.Person AS p  
ON ph.BusinessEntityID = p.BusinessEntityID  
WHERE ph.PhoneNumber LIKE '415%'  
ORDER by p.LastName;  
GO  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  

```
 FirstName             LastName             Phone
 -----------------     -------------------  ------------
 Ruben                 Alonso               415-555-124  
 Shelby                Cook                 415-555-0121  
 Karen                 Hu                   415-555-0114  
 John                  Long                 415-555-0147  
 David                 Long                 415-555-0123  
 Gilbert               Ma                   415-555-0138  
 Meredith              Moreno               415-555-0131  
 Alexandra             Nelson               415-555-0174  
 Taylor                Patterson            415-555-0170  
 Gabrielle              Russell             415-555-0197  
 Dalton                 Simmons             415-555-0115  
 (11 row(s) affected)  
```

### <a name="b-using-not-like-with-the--wildcard-character"></a>B. 使用 NOT LIKE 搭配 % 萬用字元  
 下列範例會在 `PersonPhone` 資料表中，尋找所有區域碼不是 `415` 的電話號碼。  
  
```sql  
-- Uses AdventureWorks  
  
SELECT p.FirstName, p.LastName, ph.PhoneNumber  
FROM Person.PersonPhone AS ph  
INNER JOIN Person.Person AS p  
ON ph.BusinessEntityID = p.BusinessEntityID  
WHERE ph.PhoneNumber NOT LIKE '415%' AND p.FirstName = 'Gail'  
ORDER BY p.LastName;  
GO  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  

```
FirstName              LastName            Phone
---------------------- -------------------- -------------------
Gail                  Alexander            1 (11) 500 555-0120  
Gail                  Butler               1 (11) 500 555-0191  
Gail                  Erickson             834-555-0132  
Gail                  Erickson             849-555-0139  
Gail                  Griffin              450-555-0171  
Gail                  Moore                155-555-0169  
Gail                  Russell              334-555-0170  
Gail                  Westover             305-555-0100  
(8 row(s) affected)  
```

### <a name="c-using-the-escape-clause"></a>C. 使用 ESCAPE 子句  
 下列範例會利用 `ESCAPE` 子句和逸出字元來尋找 `mytbl2` 資料表 `c1` 資料行中完全相符的 `10-15%` 字元字串。  
  
```sql
USE tempdb;  
GO  
IF EXISTS(SELECT TABLE_NAME FROM INFORMATION_SCHEMA.TABLES  
      WHERE TABLE_NAME = 'mytbl2')  
   DROP TABLE mytbl2;  
GO  
USE tempdb;  
GO  
CREATE TABLE mytbl2  
(  
 c1 sysname  
);  
GO  
INSERT mytbl2 VALUES ('Discount is 10-15% off'), ('Discount is .10-.15 off');  
GO  
SELECT c1   
FROM mytbl2  
WHERE c1 LIKE '%10-15!% off%' ESCAPE '!';  
GO  
```  
  
### <a name="d-using-the---wildcard-characters"></a>D. 使用 [ ] 萬用字元  
 下列範例會在 `Person` 資料表中尋找名字為 `Cheryl` 或 `Sheryl` 的員工。  
  
```sql  
-- Uses AdventureWorks  
  
SELECT BusinessEntityID, FirstName, LastName   
FROM Person.Person   
WHERE FirstName LIKE '[CS]heryl';  
GO  
```  
  
 下列範例會在 `Person` 資料表上尋找姓氏為 `Zheng` 或 `Zhang` 的員工。  
  
```sql  
-- Uses AdventureWorks  
  
SELECT LastName, FirstName  
FROM Person.Person  
WHERE LastName LIKE 'Zh[ae]ng'  
ORDER BY LastName ASC, FirstName ASC;  
GO  
```  
  
## <a name="examples-sssdwfull-and-sspdw"></a>範例：[!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 和 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
### <a name="e-using-like-with-the--wildcard-character"></a>E. 使用 LIKE 搭配 % 萬用字元  
 下列範例會在 `DimEmployee` 資料表中尋找電話號碼是以 `612` 開頭的所有員工。  
  
```sql  
-- Uses AdventureWorks  
  
SELECT FirstName, LastName, Phone  
FROM DimEmployee  
WHERE phone LIKE '612%'  
ORDER by LastName;  
```  
  
### <a name="f-using-not-like-with-the--wildcard-character"></a>F. 使用 NOT LIKE 搭配 % 萬用字元  
 下列範例會在 `DimEmployee` 資料表中尋找所有開頭不是 `612` 的電話號碼。  .  
  
```sql  
-- Uses AdventureWorks  
  
SELECT FirstName, LastName, Phone  
FROM DimEmployee  
WHERE phone NOT LIKE '612%'  
ORDER by LastName;  
```  
  
### <a name="g-using-like-with-the-_-wildcard-character"></a>G. 使用 LIKE 搭配 _ 萬用字元  
 下列範例會在 `DimEmployee` 資料表中尋找區碼是以 `6` 開頭且以 `2` 結尾的所有電話號碼。 % 萬用字元會包含在搜尋模式的結尾，用來比對電話資料行值中的所有後續字元。  
  
```sql  
-- Uses AdventureWorks  
  
SELECT FirstName, LastName, Phone  
FROM DimEmployee  
WHERE phone LIKE '6_2%'  
ORDER by LastName;   
```  
  
## <a name="see-also"></a>另請參閱  
 [PATINDEX &#40;Transact-SQL&#41;](../../t-sql/functions/patindex-transact-sql.md)   
 [運算式 &#40;Transact-SQL&#41;](../../t-sql/language-elements/expressions-transact-sql.md)   
 [內建函數 &#40;Transact-SQL&#41;](~/t-sql/functions/functions.md)   
 [SELECT &#40;Transact-SQL&#41;](../../t-sql/queries/select-transact-sql.md)   
 [WHERE &#40;Transact-SQL&#41;](../../t-sql/queries/where-transact-sql.md)  
