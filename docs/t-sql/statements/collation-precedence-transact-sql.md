---
description: 定序優先順序
title: 定序優先順序 | Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
dev_langs:
- TSQL
helpviewer_keywords:
- coercible-default collation label
- precedence [SQL Server], collations
- collation sensitive
- collations [SQL Server], precedence
- explicit collation label [SQL Server]
- implicit collation label
- no-collation label
- collation insensitive
- operators [Transact-SQL], collations
- collation labels
- collation coercion rules
- rules [SQL Server], collations
ms.assetid: 58c4e64b-5634-4c29-aa22-33193282dd27
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: c06bf7aa0ec45150f657e5822d1d5b1387ef18df
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98098456"
---
# <a name="collation-precedence"></a>定序優先順序
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  定序優先順序 (也稱為定序強制型轉規則) 會決定下列各項：  
  
-   評估得出字元字串之運算式最終結果的定序。  
  
-   輸入是字元字串而不傳回字元字串的區分定序運算子 (如 LIKE 和 IN) 所用的定序。  
  
定序優先順序規則只適用於字元字串資料類型：**char**、**varchar**、**text**、**nchar**、**nvarchar** 和 **ntext**。 其他資料類型的物件不參與定序評估。  
  
## <a name="collation-labels"></a>定序標籤  
下表列出和描述識別所有物件之定序的四個類別目錄。 每個類別目錄的名稱都稱為定序標籤。  
  
|定序標籤|物件類型|  
|---------------------|----------------------|  
|強制預設|任何 [!INCLUDE[tsql](../../includes/tsql-md.md)] 字元字串變數、參數、常值，或目錄內建函數的輸出，或輸入不是字串卻會產生字串輸出的內建函數。<br /><br /> 如果物件宣告在使用者自訂函數、預存程序或觸發程序中，便會將建立了函數、預存程序或觸發程序之資料庫的預設定序指派給物件。 如果物件宣告在批次中，便會將連接目前資料庫的預設定序指派給物件。|  
|隱含 X|資料行參考。 運算式 (X) 的定序取自定義給資料表或檢視中之資料行的定序。<br /><br /> 即使已利用 CREATE TABLE 或 CREATE VIEW 陳述式中的 COLLATE 子句，將資料行明確指派給某個定序，資料行參考仍會被分類為隱含的資料行參考。|  
|明確 X|在運算式中，利用 COLLATE 子句明確轉換成特定定序 (X) 的運算式。|  
|無定序|指出運算式的值是兩個含有隱含定序標籤衝突定序字串之間的運算結果。 運算式結果定義為無定序。|  
  
## <a name="collation-rules"></a>定序規則  
只參考單一字元字串物件的簡單運算式，其定序標籤是被參考物件的定序標籤。  
  
參考兩個含有相同定序標籤的運算元運算式之複雜運算式，其定序標籤是運算元運算式的定序標籤。  
  
參考兩個含不同定序的運算元運算式之複雜運算式，其最終結果的定序標籤是以下列規則為基礎：  
  
-   明確優先於隱含。 隱含優先於強制預設：  
  
     明確 > 隱含 > 強制預設  
  
-   組合兩個已指派了不同定序的明確運算式，會產生一則錯誤：  
  
     明確 X + 明確 Y = 錯誤  
  
-   組合兩個含不同定序的隱含運算式會產生無定序的結果：  
  
     隱含 X + 隱含 Y = 無定序  
  
-   組合無定序運算式和任何標籤的運算式，除了明確定序 (請參閱下列規則) 之外，會產生含無定序標籤的結果：  
  
     無定序 + 任何項目 = 無定序  
  
-   組合無定序運算式和明確定序運算式，會產生含明確標籤的運算式：  
  
     無定序 + 明確 X = 明確  
  
下表彙總這些規則。  
  
|運算元強制標籤|明確 X|隱含 X|強制預設|無定序|  
|----------------------------|----------------|----------------|------------------------|-------------------|  
|**明確 Y**|產生錯誤|結果是明確 Y|結果是明確 Y|結果是明確 Y|  
|**隱含 Y**|結果是明確 X|結果是無定序|結果是隱含 Y|結果是無定序|  
|**強制預設**|結果是明確 X|結果是隱含 X|結果是強制預設|結果是無定序|  
|**無定序**|結果是明確 X|結果是無定序|結果是無定序|結果是無定序|  
  
定序優先順序也適用下列其他規則：  
  
-   已是明確運算式的運算式，不能有多個 COLLATE 子句。 例如，下列 `WHERE` 子句無效，因為 `COLLATE` 子句的指定運算式已是明確運算式：  
  
     `WHERE ColumnA = ( 'abc' COLLATE French_CI_AS) COLLATE French_CS_AS`  
  
-   不允許進行 **text** 資料類型的字碼頁轉換。 若有不同的字碼頁，您便不能將 **text** 運算式的定序轉換成另外一個。 當右文字運算元的定序，其字碼頁與左文字運算元不同時，指派運算子便不能指派值。  
  
定序優先順序是在資料類型轉換之後所決定的。 得到結果定序的運算元，可能不是提供最終結果的資料類型之運算元。 例如，請考量下列批次：  
  
```sql  
CREATE TABLE TestTab  
   (PrimaryKey int PRIMARY KEY,  
    CharCol char(10) COLLATE French_CI_AS  
   )  
  
SELECT *  
FROM TestTab  
WHERE CharCol LIKE N'abc'  
```  
  
簡單運算式 `N'abc'` 的 Unicode 資料類型，資料類型優先順序較高。 因此，結果運算式會將 Unicode 資料類型指派給 `N'abc'`。 不過，`CharCol` 運算式會有隱含定序標籤，`N'abc'` 會有較低的強制預設強制標籤。 因此，所用的定序是 `French_CI_AS` 的 `CharCol` 定序。  
  
### <a name="examples-of-collation-rules"></a>定序規則的範例  
 下列範例會顯示定序規則的運作方式。 若要執行這些範例，請建立下列測試資料表。  
  
```sql  
USE tempdb;  
GO  
  
CREATE TABLE TestTab (  
   id int,   
   GreekCol nvarchar(10) collate greek_ci_as,   
   LatinCol nvarchar(10) collate latin1_general_cs_as  
   )  
INSERT TestTab VALUES (1, N'A', N'a');  
GO  
```  
  
#### <a name="collation-conflict-and-error"></a>定序衝突和錯誤  
 下列查詢中的述詞發生定序衝突，產生錯誤。  
  
```sql  
SELECT *   
FROM TestTab   
WHERE GreekCol = LatinCol;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
Msg 448, Level 16, State 9, Line 2  
Cannot resolve collation conflict between 'Latin1_General_CS_AS' and 'Greek_CI_AS' in equal to operation.  
```  
  
#### <a name="explicit-label-vs-implicit-label"></a>明確標籤和隱含標籤  
 下列查詢中的述詞是以 `greek_ci_as` 定序來評估的，因為右側運算式有明確的標籤。 它的優先順序比左側運算式的隱含標籤高。  
  
```sql  
SELECT *   
FROM TestTab   
WHERE GreekCol = LatinCol COLLATE greek_ci_as;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
id          GreekCol             LatinCol  
----------- -------------------- --------------------  
          1 A                    a  
  
(1 row affected)  
```  
  
#### <a name="no-collation-labels"></a>無定序標籤  
下列查詢中的 `CASE` 運算式具備無定序標籤；因此，它們不能出現在選取清單中，也不能由區分定序的運算子來處理。 不過，任何不區分定序的運算子都能夠處理這些運算式。  
  
```sql  
SELECT (CASE WHEN id > 10 THEN GreekCol ELSE LatinCol END)   
FROM TestTab;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
Msg 451, Level 16, State 1, Line 1  
Cannot resolve collation conflict for column 1 in SELECT statement.  
```  
  
```sql  
SELECT PATINDEX((CASE WHEN id > 10 THEN GreekCol ELSE LatinCol END), 'a')  
FROM TestTab;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
Msg 446, Level 16, State 9, Server LEIH2, Line 1  
Cannot resolve collation conflict for patindex operation.  
```  
  
```sql  
SELECT (CASE WHEN id > 10 THEN GreekCol ELSE LatinCol END) COLLATE Latin1_General_CI_AS   
FROM TestTab;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
--------------------  
a  
  
(1 row affected)  
```  
  
## <a name="collation-sensitive-and-collation-insensitive"></a>區分定序和不區分定序  
運算子和函數會區分定序或不區分定序。  
  
區分定序  
這表示指定無定序運算元是一項編譯階段的錯誤。 運算式結果不能無定序。  
  
不區分定序  
這表示運算元和結果可以沒有定序。  
  
### <a name="operators-and-collation"></a>運算子和定序  
比較運算子，以及 MAX、MIN、BETWEEN、LIKE 和 IN 等運算子，都會區分定序。 運算子所用的字串會指派優先順序較高之運算元的定序標籤。 UNION 陳述式也會區分定序，所有字串運算元和最終結果都會指派含最高優先順序的運算元定序。 UNION 運算元和結果的定序優先順序，是按資料行逐一評估的。  
  
指派運算子不區塊定序，右側運算式會轉換成左側的定序。  
  
字串串連運算子區分定序，兩個字串運算元和結果都會指派定序優先順序最高之運算元的定序標籤。 UNION ALL 和 CASE 陳述式不區分定序，所有字串運算元和最終結果都會指派含最高優先順序的運算元定序標籤。 UNION ALL 運算元和結果的定序優先順序，是按資料行逐一評估的。  
  
### <a name="functions-and-collation"></a>函數和定序  
CAST、CONVERT，和 COLLATE 函式會針對 **char**、**varchar** 及 **text** 資料類型區分定序。 如果 CAST 和 CONVERT 函數的輸入和輸出是字元字串，輸出字串會採用輸入字串的定序標籤。 如果輸入不是字元字串，輸出字串就是強制預設，且會指派連接之目前資料庫的定序，或參考 CAST 或 CONVERT 的使用者自訂函數、預存程序或觸發程序所在的資料庫定序。  
  
 如果是傳回字串而沒有輸入字串的內建函數，結果字串便是強制預設，且會指派目前資料庫的定序，或參考了這個函數的使用者自訂函數、預存程序或觸發程序所在的資料庫定序。  
  
 下列函數會區分定序，它們的輸出字串會有輸入字串的定序標籤：  

:::row:::
    :::column:::
        CHARINDEX
    :::column-end:::
    :::column:::
        REPLACE
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        DIFFERENCE
    :::column-end:::
    :::column:::
        REVERSE
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        ISNUMERIC
    :::column-end:::
    :::column:::
        RIGHT
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        LEFT
    :::column-end:::
    :::column:::
        SOUNDEX
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        LEN
    :::column-end:::
    :::column:::
        STUFF
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        LOWER
    :::column-end:::
    :::column:::
        SUBSTRING
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        PATINDEX
    :::column-end:::
    :::column:::
        UPPER
    :::column-end:::
:::row-end:::
  
## <a name="see-also"></a>另請參閱  
 [COLLATE &#40;Transact-SQL&#41;](~/t-sql/statements/collations.md)   
 [資料類型轉換 &#40;資料庫引擎&#41;](../../t-sql/data-types/data-type-conversion-database-engine.md)   
 [運算子 &#40;Transact-SQL&#41;](../../t-sql/language-elements/operators-transact-sql.md)   
 [運算式 &#40;Transact-SQL&#41;](../../t-sql/language-elements/expressions-transact-sql.md)  
  
  
