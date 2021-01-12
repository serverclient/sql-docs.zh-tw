---
description: CHECKSUM (Transact-SQL)
title: CHECKSUM (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 07/24/2017
ms.prod: sql
ms.prod_service: sql-data-warehouse, database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- CHECKSUM_TSQL
- CHECKSUM
dev_langs:
- TSQL
helpviewer_keywords:
- hash indexes
- CHECKSUM function
- checksum values
ms.assetid: e26d3339-845c-49c2-9d89-243376874c13
author: cawrites
ms.author: chadam
monikerRange: =azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 3534f5011275035b11adc1984604f8795f4208f2
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98092376"
---
# <a name="checksum-transact-sql"></a>CHECKSUM (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa.md)]

`CHECKSUM` 函式會傳回針對資料表資料列或運算式清單所計算的總和檢查碼值。 使用 `CHECKSUM` 建置雜湊索引。
  
![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)
  
## <a name="syntax"></a>語法  
  
```syntaxsql
CHECKSUM ( * | expression [ ,...n ] )  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]
[!INCLUDE[synapse-analytics-od-unsupported-syntax](../../includes/synapse-analytics-od-unsupported-syntax.md)]

## <a name="arguments"></a>引數
\*  
此引數指定總和檢查碼計算涵蓋所有資料表資料行。 如果任何資料行具有無法比較的資料類型，則 `CHECKSUM` 會傳回錯誤。 無法比較的資料類型包含：

- **cursor**
- **image**
- **ntext**
- **text**
- **XML**

另一個無法比較的資料類型是將上述任一個資料類型作為其基底類型的 **sql_variant**。
  
*expression*  
任何類型的[運算式](../../t-sql/language-elements/expressions-transact-sql.md) (無法比較的資料類型除外)。
  
## <a name="return-types"></a>傳回類型
 **int**  
  
## <a name="remarks"></a>備註  
`CHECKSUM` 會針對它的引數清單來計算稱為總和檢查碼的雜湊值。 使用此雜湊值來建置雜湊索引。 如果 `CHECKSUM` 函數具有資料行引數，而且會針對計算得來的 `CHECKSUM` 值建置索引，則將會產生雜湊索引。 這可用來進行資料行的相等搜尋。
  
`CHECKSUM` 函式滿足雜湊函式屬性：如果兩份清單的對應項目具有相同的資料類型，而且這些對應的項目在使用等於 (=) 運算子比較時相等，則套用至任兩份運算式清單的 `CHECKSUM` 會傳回相同的值。 針對 `CHECKSUM` 函式目的，定義所指定類型的 Null 值以比較為相等。 如果運算式清單中至少有一個值變更，則清單總和檢查碼可能會變更。 不過，不保證一定會如此。 因此，若要偵測值是否已變更，建議只有在您的應用程式可以容忍偶而遺失的變更時才使用 `CHECKSUM`。 否則，請考慮改用 `HASHBYTES`。 使用指定的 MD5 雜湊演算法，`HASHBYTES` 將為兩個不同的輸入傳回相同結果的可能性比 `CHECKSUM` 要低很多。
  
運算式順序會影響計算的 `CHECKSUM` 值。 用於 `CHECKSUM(*)` 的資料行順序，就是資料表或檢視定義中所指定的資料行順序。 計算資料行也包括在內。
  
`CHECKSUM` 值取決於定序。 以不同定序儲存的相同值，將會傳回不同的 `CHECKSUM` 值。
  
`CHECKSUM ()` 不保證結果是唯一的。

## <a name="examples"></a>範例  
這些範例示範如何使用 `CHECKSUM` 建置雜湊索引。
  
若要建置雜湊索引，第一個範例會將計算的總和檢查碼資料行新增至我們想要編製索引的資料表。 它接著會建置總和檢查碼資料行的索引。 
  
```sql
-- Create a checksum index.  

SET ARITHABORT ON;  
USE AdventureWorks2012;   
GO  
ALTER TABLE Production.Product  
ADD cs_Pname AS CHECKSUM(Name);  
GO  
CREATE INDEX Pname_index ON Production.Product (cs_Pname);  
GO  
```  
  
此範例示範如何使用總和檢查碼索引作為雜湊索引。 要編製索引的資料行是長字元資料行時，這有助於改善編製索引速度。 總和檢查碼索引也可用來進行相等搜尋。
  
```sql
/*Use the index in a SELECT query. Add a second search   
condition to catch stray cases where checksums match,   
but the values are not the same.*/  

SELECT *   
FROM Production.Product  
WHERE CHECKSUM(N'Bearing Ball') = cs_Pname  
AND Name = N'Bearing Ball';  
GO  
```  
  
建立計算資料行的索引，會將總和檢查碼資料行具體化，而且 `ProductName` 值的任何變更都會傳播到總和檢查碼資料行。 或者，我們可以直接建置想要編製索引之資料行的索引。 不過，對於長索引鍵值，可能不會執行一般索引和總和檢查碼索引。
  
## <a name="see-also"></a>另請參閱
[CHECKSUM_AGG &#40;Transact-SQL&#41;](../../t-sql/functions/checksum-agg-transact-sql.md)  
[HASHBYTES &#40;Transact-SQL&#41;](../../t-sql/functions/hashbytes-transact-sql.md)  
[BINARY_CHECKSUM  &#40;Transact-SQL&#41;](../../t-sql/functions/binary-checksum-transact-sql.md)
  
  
