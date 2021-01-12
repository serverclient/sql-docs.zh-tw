---
description: ~ (位元 NOT) (Transact-SQL)
title: ~ (位元 NOT) (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 01/10/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- ~_TSQL
- bitwise
- NOT
- "~"
- Bitwise NOT
dev_langs:
- TSQL
helpviewer_keywords:
- NOT keyword
- bitwise NOT (~)
- ~ (bitwise NOT)
ms.assetid: 02da8016-f6c0-41ae-8d59-33eaa02bfc95
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: fe1193cb7251b690c4d610ee0a2fd691fc298d0f
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98092288"
---
# <a name="-bitwise-not-transact-sql"></a>~ (位元 NOT) (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  在整數值上，執行位元邏輯 NOT 運算。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql  
~ expression  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *expression*  
 這是整數資料類型類別目錄之任何資料類型或是 **bit**、**binary** 或 **varbinary** 資料類型的任何有效 [運算式](../../t-sql/language-elements/expressions-transact-sql.md)。 *expression* 會被視為適用於位元運算的二進位數字。  
  
> [!NOTE]  
>  在位元運算中，只能有一個 *expression* 是 **binary** 或 **varbinary** 資料類型。  
  
## <a name="result-types"></a>結果類型  
 如果輸入值為 **int**，則為 **int**。  
  
 如果輸入值為 **smallint**，則為 **smallint**。  
  
 如果輸入值為 **tinyint**，則為 **tinyint**。  
  
 如果輸入值為 **bit**，則為 **bit**。  
  
## <a name="remarks"></a>備註  
 **~** 位元運算子會執行 *expression* 的位元邏輯 NOT 運算，依次處理每個位元。 如果 *expression* 的值為 0，結果集內的位元就會設定為 1；否則，結果中的位元會清除為 0 值。 換句話說，1 會變更為零，零則會變更為 1。  
  
> [!IMPORTANT]  
>  當您執行任何種類的位元運算時，位元運算所用的運算式儲存長度非常重要。 我們建議您在儲存值時，使用這個相同的位元組數。 例如，將十進位值 5 儲存成 **tinyint**、**smallint** 或 **int** ，會產生以不同位元組數儲存的值：**tinyint** 會使用 1 個位元組來儲存資料；**smallint** 會使用 2 個位元組來儲存資料；**int** 會使用 4 個位元組來儲存資料。 因此，在 **int** 十進位值上執行位元運算，與使用直接二進位或十六進位轉譯，可能會產生不同的結果，當使用 **~** (位元 NOT) 運算子時，尤其如此。 位元 NOT 運算可能發生在長度較短的變數上。 在這個情況下，當較短的長度轉換成較長的資料類型變數時，上面 8 位元中的位元可能不會設為預期的值。 我們建議您將較小的資料類型變數轉換成較長的資料類型，之後，在結果上執行 NOT 運算。  
  
## <a name="examples"></a>範例  
 下列範例會使用 **int** 資料類型來建立資料表以儲存值，並將兩個值插入到單一資料列中。  
  
```sql  
CREATE TABLE bitwise (  
  a_int_value INT NOT NULL,  
  b_int_value INT NOT NULL); 
GO  
INSERT bitwise VALUES (170, 75);  
GO  
```  
  
 下列查詢在 `a_int_value` 和 `b_int_value` 資料行上執行位元 NOT 運算。  
  
```sql  
SELECT ~ a_int_value, ~ b_int_value  
FROM bitwise;  
```  
  
 以下為結果集：  
  
```  
--- ---   
-171  -76   
  
(1 row(s) affected)  
```  
  
 170 (`a_int_value` 或 `A`) 的二進位表示法是 `0000 0000 1010 1010`。 對這個值執行位元 NOT 運算，會產生二進位結果 `1111 1111 0101 0101`，也就是十進位的 -171。 75 的二進位表示是 `0000 0000 0100 1011`。 執行位元 NOT 運算會產生 `1111 1111 1011 0100`，也就是十進位的 -76。  
  
```  
 (~A)     
         0000 0000 1010 1010  
         -------------------  
         1111 1111 0101 0101  
(~B)     
         0000 0000 0100 1011  
         -------------------  
         1111 1111 1011 0100  
```  
  
 
## <a name="see-also"></a>另請參閱  
 [運算式 &#40;Transact-SQL&#41;](../../t-sql/language-elements/expressions-transact-sql.md)   
 [運算子 &#40;Transact-SQL&#41;](../../t-sql/language-elements/operators-transact-sql.md)   
 [位元運算子 &#40;Transact-SQL&#41;](../../t-sql/language-elements/bitwise-operators-transact-sql.md)  
  
  


