---
description: 運算子優先順序 (Transact-SQL)
title: 運算子優先順序 (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
dev_langs:
- TSQL
helpviewer_keywords:
- operators [Transact-SQL], precedence
- operator precedence [Transact-SQL]
- order of operator execution [Transact-SQL]
- precedence [SQL Server], operators
ms.assetid: f04d2439-6fff-4e4c-801f-cc62faef510a
author: cawrites
ms.author: chadam
ms.openlocfilehash: 66cb4af95ca6c0167570a1edd1fea4dea0a8e838
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98100964"
---
# <a name="operator-precedence-transact-sql"></a>運算子優先順序 (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  當複雜運算式有多個運算子時，運算子優先順序會決定作業的順序。 執行的順序對結果值會有很大的影響。  
  
 下表顯示運算子的優先順序層級。 先評估層級較高的運算子，再評估層級較低的運算子。 在下表中，1 是最高層級，而 8 則是最低層級。
  
|層級|操作員|  
|-----------|---------------|  
|1|~ (位元 NOT)|  
|2|* (乘)、/ (除)、% (模數)|  
|3|+ (正)、- (負)、+ (加)、(+ 串連)、- (減)、& (位元 AND)、^ (位元互斥 OR)、&#124; (位元 OR)|  
|4|=、>、\<, >=、<=、<>、!=、!>、!< (比較運算子)|  
|5|NOT|  
|6|AND|  
|7|ALL、ANY、BETWEEN、IN、LIKE、OR、SOME|  
|8|= (指派)|  
  
 當運算式中兩個運算子有相同的優先順序層級時，會依據它們在運算式中的位置，由左至右來評估它們。 例如，在下列 `SET` 陳述式所用的運算式中，會先評估減法運算子，再評估加法運算子。  
  
```sql  
DECLARE @MyNumber INT;  
SET @MyNumber = 4 - 2 + 27;  
-- Evaluates to 2 + 27 which yields an expression result of 29.  
SELECT @MyNumber;  
```  
  
 請利用括號來覆寫運算式中，已定義的運算子優先順序。 括號內的所有項目都會受到求值，並得出單一值。 這些括號外的所有運算子都能使用該值。  
  
 例如，在下列 `SET` 陳述式所用的運算式中，乘法運算子的優先順序高於加法運算子。 乘法作業會優先受到求值；運算式結果為 `13`。  
  
```sql  
DECLARE @MyNumber INT;  
SET @MyNumber = 2 * 4 + 5;  
-- Evaluates to 8 + 5 which yields an expression result of 13.  
SELECT @MyNumber;  
```  
  
 在下列 `SET` 陳述式所用的運算式中，括號會使系統優先求值加法。 運算式結果是 `18`。  
  
```sql  
DECLARE @MyNumber INT;  
SET @MyNumber = 2 * (4 + 5);  
-- Evaluates to 2 * 9 which yields an expression result of 18.  
SELECT @MyNumber;  
```  
  
 如果運算式有巢狀括號，最內層的巢狀運算式最先評估。 下列範例包含巢狀括號，最內層的一組巢狀括號中的運算式是 `5 - 3`。 這個運算式會產生 `2` 值。 之後，加法運算子 (`+`) 會將這個結果加上 `4`，得出值 `6`。 最後，`6` 再乘以 `2`，得出運算式結果 `12`。  
  
```sql  
DECLARE @MyNumber INT;  
SET @MyNumber = 2 * (4 + (5 - 3) );  
-- Evaluates to 2 * (4 + 2) which then evaluates to 2 * 6, and   
-- yields an expression result of 12.  
SELECT @MyNumber;  
```  
  
## <a name="see-also"></a>另請參閱  
 [邏輯運算子 &#40;Transact-SQL&#41;](../../t-sql/language-elements/logical-operators-transact-sql.md)   
 [運算子 &#40;Transact-SQL&#41;](../../t-sql/language-elements/operators-transact-sql.md)   
 [內建函數 &#40;Transact-SQL&#41;](~/t-sql/functions/functions.md)  
  
