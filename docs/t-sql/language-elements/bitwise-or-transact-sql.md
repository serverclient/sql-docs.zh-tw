---
description: '| (位元 OR) (Transact-SQL)'
title: '| (位元 OR) (Transact-SQL) | Microsoft Docs'
ms.custom: ''
ms.date: 01/10/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- '|'
- '|_TSQL'
- Bitwise OR
- bitwise
- OR
dev_langs:
- TSQL
helpviewer_keywords:
- OR operator
- bitwise OR (|)
- '| (bitwise OR operator)'
ms.assetid: 86a3b87f-9688-4eaf-a552-29f1b01d880a
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 9d25034bf1089464ed80a7b924875baec1bb9a8b
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98095974"
---
# <a name="-bitwise-or-transact-sql"></a>| (位元 OR) (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  當在 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式內轉換成二進位運算式時，執行兩個指定整數值之間的位元邏輯 OR 運算。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql   
expression | expression  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *expression*  
 這是整數資料類型類別目錄或是 **bit**、**binary** 或 **varbinary** 資料類型的任何有效 [運算式](../../t-sql/language-elements/expressions-transact-sql.md)。 *expression* 會被視為適用於位元運算的二進位數字。  
  
> [!NOTE]  
>  在位元運算中，只能有一個 *expression* 是 **binary** 或 **varbinary** 資料類型。  
  
## <a name="result-types"></a>結果類型  
 如果輸入值是 **int**，便傳回 **int**，如果輸入值是 **smallint**，便傳回 **smallint**，或者如果輸入值是 **tinyint**，便傳回 **tinyint**。  
  
## <a name="remarks"></a>備註  
 位元 | 運算子會在兩個運算式之間執行位元邏輯 OR 運算，兩個運算式各有一個對應的位元。 如果輸入運算式的兩個位元或其中一個位元 (針對目前所解析的位元) 的值是 1，結果中的位元都會設為 1；如果輸入運算式中的兩個位元都不是 1，結果中的位元便設為 0。  
  
 如果左右運算式的整數資料類型不同 (例如，左邊的 *expression* 是 **smallint**，而右邊的 *expression* 是 **int**)，就會將較小資料類型的引數轉換為較大的資料類型。 在此範例中，會將 **smallint**_expression_ 轉換為 **int**。  
  
## <a name="examples"></a>範例  
 下列範例會建立一個含 **int** 資料類型的資料表來顯示原始值，並將資料表放入單一資料列中。  
  
```sql  
CREATE TABLE bitwise (  
  a_int_value INT NOT NULL,  
  b_int_value INT NOT NULL);  
GO  
INSERT bitwise VALUES (170, 75);  
GO  
```  
  
 下列查詢會在 **a_int_value** 和 **b_int_value** 資料行上執行位元 OR 運算。  
  
```sql  
SELECT a_int_value | b_int_value  
FROM bitwise;  
GO  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
-----------   
235           
  
(1 row(s) affected)  
```  
  
 170 (**a_int_value** 或下面的 `A`) 的二進位表示法是 `0000 0000 1010 1010`。 75 (**b_int_value** 或下面的 `B`) 的二進位表示法是 `0000 0000 0100 1011`。 對這兩個值執行位元 OR 運算，會產生二進位結果 `0000 0000 1110 1011`，也就是十進位的 235。  
  
```  
(A | B)  
0000 0000 1010 1010  
0000 0000 0100 1011  
-------------------  
0000 0000 1110 1011  
```  
  
## <a name="see-also"></a>另請參閱  
 [運算子 &#40;Transact-SQL&#41;](../../t-sql/language-elements/operators-transact-sql.md)   
 [位元運算子 &#40;Transact-SQL&#41;](../../t-sql/language-elements/bitwise-operators-transact-sql.md)   
 [&#124;= &#40;位元 OR 指派&#41; &#40;Transact-SQL&#41;](../../t-sql/language-elements/bitwise-or-equals-transact-sql.md)   
 [複合運算子 &#40;Transact-SQL&#41;](../../t-sql/language-elements/compound-operators-transact-sql.md)  
  
  


