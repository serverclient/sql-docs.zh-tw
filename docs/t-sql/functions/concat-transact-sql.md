---
description: CONCAT (Transact-SQL)
title: CONCAT (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 07/24/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- CONCAT
- CONCAT_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- CONCAT function
ms.assetid: fce5a8d4-283b-4c47-95e5-4946402550d5
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: e48b358b33276a42d4c4f9c1cc0915238434e02d
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98088368"
---
# <a name="concat-transact-sql"></a>CONCAT (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

此函式會傳回透過以端對端方式串連 (或聯結) 兩個以上字串值所產生的字串。 (若要在串連期間新增分隔值，請參閱[CONCAT_WS](../../t-sql/functions/concat-ws-transact-sql.md)。)
  
![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)
  
## <a name="syntax"></a>語法  
  
```syntaxsql
CONCAT ( string_value1, string_value2 [, string_valueN ] )  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
*string_value*  
要串連至其他值的字串值。 `CONCAT` 函式需要至少兩個 *string_value* 引數，而且不能超過 254 個 *string_value* 引數。
  
## <a name="return-types"></a>傳回類型  
*string_value*  
長度和類型取決於輸入的字串值。
  
## <a name="remarks"></a>備註  
`CONCAT` 會採用可變數量的字串引數，並將其串連 (聯結) 成單一字串。 它至少需要兩個輸入值，否則 `CONCAT` 會引發錯誤。 `CONCAT` 會在串連之前將所有引數隱含地轉換成字串類型。 `CONCAT` 會將 Null 值隱含地轉換成空字串。 如果 `CONCAT` 收到全部都是 **NULL** 值的引數，則會傳回 **varchar**(1) 類型的空字串。 隱含轉換成字串會遵循現有的資料類型轉換規則。 如需資料類型轉換的詳細資訊，請參閱 [CAST 和 CONVERT &#40;Transact-SQL&#41;](../../t-sql/functions/cast-and-convert-transact-sql.md)。
  
傳回類型取決於引數類型。 此表說明對應：
  
|輸入類型|輸出類型及長度|  
|---|---|
|1.下者的任何引數：<br><br />SQL-CLR 系統類型<br><br />SQL-CLR UDT<br><br />或<br><br />`nvarchar(max)`|**nvarchar(max)**|  
|2.否則，是下列類型的任何參數：<br><br />**varbinary(max)**<br><br />或<br><br />**varchar(max)**|**varchar(max)** ，除非其中一個參數是任何長度的 **nvarchar**。 在此情況下，`CONCAT` 會傳回 **nvarchar(max)** 類型的結果。|  
|3.否則為 **nvarchar** 類型且最多 4000 個字元的任何引數<br><br />(**nvarchar**(<= 4000))|**nvarchar**(<= 4000)|  
|4.在所有其他情況下|**varchar**(<= 8000) (最多 8000 個字元的 **varchar**)，除非其中一個參數是任何長度的 nvarchar。 在該情況下，`CONCAT` 會傳回 **nvarchar(max)** 類型的結果。|  
  
`CONCAT` 收到長度 <= 4000 個字元的 **nvarchar** 輸入引數或長度 <= 8000 個字元的 **varchar** 輸入引數時，隱含轉換可能會影響結果長度。 其他資料類型在隱含地轉換成字串時，長度會不同。 例如，**int** (14) 的字串長度為 12，而 **float** 的長度為 32。 因此，串連兩個整數會傳回長度不超過 24 的結果。
  
如果輸入引數沒有支援的大型物件 (LOB) 類型，則無論傳回類型為何，傳回類型的長度都會截斷成 8000 個字元。 這項截斷可以保留空間，讓產生計畫更具效率。
  
CONCAT 函式可以在 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 版及更高版本的連結伺服器上，以遠端方式執行。 對於較舊的連結伺服器，會在連結伺服器傳回非串連值之後，於本機執行 CONCAT 作業。
  
## <a name="examples"></a>範例  
  
### <a name="a-using-concat"></a>A. 使用 CONCAT  
  
```sql
SELECT CONCAT ( 'Happy ', 'Birthday ', 11, '/', '25' ) AS Result;  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
Result  
-------------------------  
Happy Birthday 11/25  
  
(1 row(s) affected)  
```  
  
### <a name="b-using-concat-with-null-values"></a>B. 使用 CONCAT 搭配 NULL 值  
  
```sql
CREATE TABLE #temp (  
    emp_name NVARCHAR(200) NOT NULL,  
    emp_middlename NVARCHAR(200) NULL,  
    emp_lastname NVARCHAR(200) NOT NULL  
);  
INSERT INTO #temp VALUES( 'Name', NULL, 'Lastname' );  
SELECT CONCAT( emp_name, emp_middlename, emp_lastname ) AS Result  
FROM #temp;  
```  

[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
Result  
------------------  
NameLastname  
  
(1 row(s) affected)  
```  
  
## <a name="see-also"></a>請參閱
 [CONCAT_WS &#40;Transact-SQL&#41;](../../t-sql/functions/concat-ws-transact-sql.md)   
 [FORMATMESSAGE &#40;Transact-SQL&#41;](../../t-sql/functions/formatmessage-transact-sql.md)  
 [QUOTENAME &#40;Transact-SQL&#41;](../../t-sql/functions/quotename-transact-sql.md)  
 [REPLACE &#40;Transact-SQL&#41;](../../t-sql/functions/replace-transact-sql.md)  
 [REVERSE &#40;Transact-SQL&#41;](../../t-sql/functions/reverse-transact-sql.md)  
 [STRING_AGG &#40;Transact-SQL&#41;](../../t-sql/functions/string-agg-transact-sql.md)  
 [STRING_ESCAPE &#40;Transact-SQL&#41;](../../t-sql/functions/string-escape-transact-sql.md)  
 [STUFF &#40;Transact-SQL&#41;](../../t-sql/functions/stuff-transact-sql.md)  
 [TRANSLATE &#40;Transact-SQL&#41;](../../t-sql/functions/translate-transact-sql.md)  
 [字串函數 &#40;Transact-SQL&#41;](../../t-sql/functions/string-functions-transact-sql.md)  
  


