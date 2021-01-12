---
description: + (字串串連) (Transact-SQL)
title: + (字串串連) (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 12/06/2016
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- concatenation
- +
- string
dev_langs:
- TSQL
helpviewer_keywords:
- concatenation [SQL Server]
- string concatenation operators
- + (string concatenation)
ms.assetid: 35cb3d7a-48f5-4b13-926c-a9d369e20ed7
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 93226b1e477c0ba0c1e5d72380c1c65cccf278eb
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98100972"
---
# <a name="-string-concatenation-transact-sql"></a>+ (字串串連) (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  這是字串運算式中的運算子，用來將兩個或更多字元或二進位字串、資料行，或字串和資料行名稱的組合，串連成單一運算式 (字串運算子)。  例如 `SELECT 'book'+'case';` 會傳回 `bookcase`。
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql  
expression + expression  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *expression*  
 這是字元和二進位資料類型類別目錄之任何資料類型的任何有效 [運算式](../../t-sql/language-elements/expressions-transact-sql.md)，但 **image**、**ntext** 或 **text** 資料類型除外。 兩個運算式的資料類型必須相同，或者其中一個運算式必須可以用隱含方式轉換為另一個運算式的資料類型。  
  
 當串連二進位字串和二進位字串之間的任何字元時，必須使用指向字元資料的明確轉換。 下列範例會顯示 `CONVERT` 或 `CAST` 何時必須搭配二進位串連來使用，何時不需要使用 `CONVERT` 或 `CAST`。  
  
```sql
DECLARE @mybin1 VARBINARY(5), @mybin2 VARBINARY(5)  
SET @mybin1 = 0xFF  
SET @mybin2 = 0xA5  
-- No CONVERT or CAST function is required because this example   
-- concatenates two binary strings.  
SELECT @mybin1 + @mybin2  
-- A CONVERT or CAST function is required because this example  
-- concatenates two binary strings plus a space.  
SELECT CONVERT(VARCHAR(5), @mybin1) + ' '   
   + CONVERT(VARCHAR(5), @mybin2)  
-- Here is the same conversion using CAST.  
SELECT CAST(@mybin1 AS VARCHAR(5)) + ' '   
   + CAST(@mybin2 AS VARCHAR(5))  
```  
  
## <a name="result-types"></a>結果類型  
 傳回優先順序最高之引數的資料類型。 如需詳細資訊，請參閱[資料類型優先順序 &#40;Transact-SQL&#41;](../../t-sql/data-types/data-type-precedence-transact-sql.md)。  
  
## <a name="remarks"></a>備註  
 + (字串串連) 運算子的行為，在使用空白、長度為零的字串時，與使用 NULL 或未知的值時，各不相同。 長度為零的字元字串可以指定為兩個單引號，引號內不含任何字元。 長度為零的二進位字串可以指定為不含以十六進位常數指定之任何二進位值的 0x。 串連長度為零的字串，一律會串連兩個指定的字串。 當您使用含 Null 值的字串時，串連結果會隨著工作階段設定而不同。 正如同在 Null 值上執行的算術運算，當未知的值加上 Null 值時，結果通常是未知的值，以 Null 值來執行的字串串連作業也應該產生 Null 結果。 不過，您可以變更目前工作階段的 `CONCAT_NULL_YIELDS_NULL` 設定來變更這個行為。 如需詳細資訊，請參閱 [SET CONCAT_NULL_YIELDS_NULL &#40;Transact-SQL&#41;](../../t-sql/statements/set-concat-null-yields-null-transact-sql.md)。  
  
 如果字串的串連結果超出 8,000 位元組的限制，就會截斷結果。 不過，如果至少有一個串連的字串是大數值類型時，就不會截斷。  
  
## <a name="examples"></a>範例  
  
### <a name="a-using-string-concatenation"></a>A. 使用字串串連  
 下列範例會根據多個字元資料行，在 `Name` 資料行標題底下建立單一資料行，其中人員姓氏後面接著一個逗號、一個空格，再接著人員的名字。 結果集的排序，按先姓後名的字母順序來遞增。  
  
```sql  
-- Uses AdventureWorks  
  
SELECT (LastName + ', ' + FirstName) AS Name  
FROM Person.Person  
ORDER BY LastName ASC, FirstName ASC;  
```  
  
### <a name="b-combining-numeric-and-date-data-types"></a>B. 組合數值和日期資料類型  
 下列範例利用 `CONVERT` 函數來串連 **numeric** 和 **date** 資料類型。  
  
```sql  
-- Uses AdventureWorks  
  
SELECT 'The order is due on ' + CONVERT(VARCHAR(12), DueDate, 101)  
FROM Sales.SalesOrderHeader  
WHERE SalesOrderID = 50001;  
GO  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
 ```
 ------------------------------------------------  
 The order is due on 04/23/2007  
 (1 row(s) affected)
 ```  
  
### <a name="c-using-multiple-string-concatenation"></a>C. 使用多重字串串連  
 下列範例串連多個字串來形成一個長字串，以顯示 [!INCLUDE[ssSampleDBCoFull](../../includes/sssampledbcofull-md.md)] 副總裁的姓氏和第一個首字母。 在姓氏之後，加上逗號，在第一個首字母之後，加上句點。  
  
```sql  
-- Uses AdventureWorks  
  
SELECT (LastName + ',' + SPACE(1) + SUBSTRING(FirstName, 1, 1) + '.') AS Name, e.JobTitle  
FROM Person.Person AS p  
    JOIN HumanResources.Employee AS e  
    ON p.BusinessEntityID = e.BusinessEntityID  
WHERE e.JobTitle LIKE 'Vice%'  
ORDER BY LastName ASC;  
GO  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
 ```
 Name               Title  
 -------------      ---------------`  
 Duffy, T.          Vice President of Engineering  
 Hamilton, J.       Vice President of Production  
 Welcker, B.        Vice President of Sales  

 (3 row(s) affected)
 ```  
 
### <a name="d-using-large-strings-in-concatenation"></a>D. 在串連中使用大型字串
下列範例會串連多個字串來形成一個長字串，然後嘗試計算最終字串的長度。 結果集的最終長度是 16000，因為運算式評估是從左邊開始的，亦即 @x + @z + @y => (@x + @z) + @y。 在此情況下，(@x + @z) 的結果會被截斷為 8000 個位元組，接著 @y 再加入至結果集，使最終字串長度為 16000。 因為 @y 是大型實值型別字串，因此不會發生截斷。

```sql
DECLARE @x VARCHAR(8000) = REPLICATE('x', 8000)
DECLARE @y VARCHAR(max) = REPLICATE('y', 8000)
DECLARE @z VARCHAR(8000) = REPLICATE('z',8000)
SET @y = @x + @z + @y
-- The result of following select is 16000
SELECT LEN(@y) AS y
GO
```
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
 ```
 y        
 -------  
 16000  
  
 (1 row(s) affected)
 ```  
## <a name="examples-sssdwfull-and-sspdw"></a>範例：[!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 和 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
### <a name="e-using-multiple-string-concatenation"></a>E. 使用多重字串串連  
 下列範例串連多個字串來形成一個長字串，以顯示簡單資料庫中副總裁的姓氏和第一個首字母。 在姓氏之後，加上逗號，在第一個首字母之後，加上句點。  
  
```sql  
-- Uses AdventureWorks  
  
SELECT (LastName + ', ' + SUBSTRING(FirstName, 1, 1) + '.') AS Name, Title  
FROM DimEmployee  
WHERE Title LIKE '%Vice Pres%'  
ORDER BY LastName ASC;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
Name               Title                                           
-------------      ---------------  
Duffy, T.          Vice President of Engineering  
Hamilton, J.       Vice President of Production  
Welcker, B.        Vice President of Sales  
```  
  
## <a name="see-also"></a>另請參閱  
 [+= &#40;字串串連指派&#41; &#40;Transact-SQL&#41;](../../t-sql/language-elements/string-concatenation-equal-transact-sql.md)   
 [ALTER DATABASE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-database-transact-sql.md)   
 [CAST 和 CONVERT &#40;Transact-SQL&#41;](../../t-sql/functions/cast-and-convert-transact-sql.md)   
 [資料類型轉換 &#40;資料庫引擎&#41;](../../t-sql/data-types/data-type-conversion-database-engine.md)   
 [資料類型 &#40;Transact-SQL&#41;](../../t-sql/data-types/data-types-transact-sql.md)   
 [運算式 &#40;Transact-SQL&#41;](../../t-sql/language-elements/expressions-transact-sql.md)   
 [內建函數 &#40;Transact-SQL&#41;](~/t-sql/functions/functions.md)   
 [運算子 &#40;Transact-SQL&#41;](../../t-sql/language-elements/operators-transact-sql.md)   
 [SELECT &#40;Transact-SQL&#41;](../../t-sql/queries/select-transact-sql.md)   
 [SET 陳述式 &#40;Transact-SQL&#41;](../../t-sql/statements/set-statements-transact-sql.md)   
  
  



