---
description: '&amp; (位元 AND) (Transact-SQL)'
title: '&amp; (位元 AND) (Transact-SQL) | Microsoft Docs'
ms.custom: ''
ms.date: 01/10/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- bitwise
- '&'
- '&_TSQL'
dev_langs:
- TSQL
helpviewer_keywords:
- AND, bitwise AND
- '& (bitwise AND)'
- bitwise AND (&)
ms.assetid: 20275755-4fa7-47b1-a9be-ac85606d63b0
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: a6d75027d1ccd171a8b860bfa536da0b51fb457b
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98095973"
---
# <a name="amp-bitwise-and-transact-sql"></a>&amp; (位元 AND) (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  在兩個整數值之間，執行位元邏輯 AND 運算。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
expression & expression  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *expression*  
 這是整數資料類型類別目錄之任何資料類型，或是 **bit**、**binary** 或 **varbinary** 資料類型的任何有效 [運算式](../../t-sql/language-elements/expressions-transact-sql.md)。 *expression* 會被視為適用於位元運算的二進位數字。  
  
> [!NOTE]  
>  在位元運算中，只能有一個 *expression* 是 **binary** 或 **varbinary** 資料類型。  
  
## <a name="result-types"></a>結果類型  
 如果輸入值為 **int**，則為 **int**。  
  
 如果輸入值為 **smallint**，則為 **smallint**。  
  
 如果輸入值為 **tinyint** 或 **bit**，則為 **tinyint**。  
  
## <a name="remarks"></a>備註  
 **&** 位元運算子會在這兩個運算式之間執行位元邏輯 AND 運算，每個運算式各有一個對應的位元。 只有在輸入運算式的兩個位元 (針對目前所解析的位元) 的值都是 1 時，結果中的兩個位元才會都設為 1；否則，結果中的位元便設為 0。  
  
 如果左右運算式的整數資料類型不同 (例如，左邊的 *expression* 是 **smallint**，而右邊的 *expression* 是 **int**)，就會將較小資料類型的引數轉換為較大的資料類型。 在此案例中，會將 **smallint** _expression_ 轉換為 **int**。  
  
## <a name="examples"></a>範例  
 下列範例會使用 **int** 資料類型建立資料表來儲存值，並在單一資料列中插入兩個值。  
  
```sql
CREATE TABLE bitwise (   
  a_int_value INT NOT NULL,  
  b_int_value INT NOT NULL);  
GO  
INSERT bitwise VALUES (170, 75);  
GO  
```  
  
 這個查詢在 `a_int_value` 和 `b_int_value` 資料行之間執行位元 AND 運算。  
  
```sql  
SELECT a_int_value & b_int_value  
FROM bitwise;  
GO  
```  
  
 以下為結果集：  
  
```  
-----------   
10            
  
(1 row(s) affected)  
```  
  
 170 (`a_int_value` 或 `A`) 的二進位表示法是 `0000 0000 1010 1010`。 75 (`b_int_value` 或 `B`) 的二進位表示法是 `0000 0000 0100 1011`。 對這兩個值執行位元 AND 運算，會產生二進位結果 `0000 0000 0000 1010`，也就是十進位的 10。  
  
```  
(A & B)  
0000 0000 1010 1010  
0000 0000 0100 1011  
-------------------  
0000 0000 0000 1010  
```  
  
  
## <a name="see-also"></a>另請參閱  
 [運算式 &#40;Transact-SQL&#41;](../../t-sql/language-elements/expressions-transact-sql.md)   
 [運算子 &#40;Transact-SQL&#41;](../../t-sql/language-elements/operators-transact-sql.md)   
 [位元運算子 &#40;Transact-SQL&#41;](../../t-sql/language-elements/bitwise-operators-transact-sql.md)   
 [&= &#40;位元 AND 指派&#41; &#40;Transact-SQL&#41;](../../t-sql/language-elements/bitwise-and-equals-transact-sql.md)   
 [複合運算子 &#40;Transact-SQL&#41;](../../t-sql/language-elements/compound-operators-transact-sql.md)  
  
  


