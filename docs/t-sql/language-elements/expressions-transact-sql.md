---
description: 運算式 (Transact-SQL)
title: 運算式 (Transact-SQL) | Microsoft Docs
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
- Boolean expressions
- expressions [SQL Server], about expressions
- combining expressions
- Transact-SQL expressions
- expressions [SQL Server], combining
- simple expressions [SQL Server]
- complex expressions [SQL Server]
ms.assetid: ee53c5c8-e36c-40f9-8cd1-d933791b98fa
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 6db31166cf0931e7ace6a6f1366fed9776509568
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98100376"
---
# <a name="expressions-transact-sql"></a>運算式 (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  這是 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 進行評估以取得單一資料值的符號和運算子組合。 簡單運算式可以是單一常數、變數、資料行或純量函數。 運算子可用來將兩個或更多簡單運算式聯結成複雜運算式。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
-- Syntax for SQL Server and Azure SQL Database  
  
{ constant | scalar_function | [ table_name. ] column | variable   
    | ( expression ) | ( scalar_subquery )   
    | { unary_operator } expression   
    | expression { binary_operator } expression   
    | ranking_windowed_function | aggregate_windowed_function  
}  
```  
  
```syntaxsql
-- Syntax for Azure Synapse Analytics and Parallel Data Warehouse  

-- Expression in a SELECT statement  
<expression> ::=   
{  
    constant   
    | scalar_function   
    | column  
    | variable  
    | ( expression  )  
    | { unary_operator } expression   
    | expression { binary_operator } expression   
}  
[ COLLATE Windows_collation_name ]  
  
-- Scalar Expression in a DECLARE, SET, IF...ELSE, or WHILE statement  
<scalar_expression> ::=  
{  
    constant   
    | scalar_function   
    | variable  
    | ( expression  )  
    | (scalar_subquery )  
    | { unary_operator } expression   
    | expression { binary_operator } expression   
}  
[ COLLATE { Windows_collation_name ]  
  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
  
|詞彙|定義|  
|----------|----------------|  
|*constant*|這是代表單一特定資料值的符號。 如需詳細資訊，請參閱[常數 &#40;Transact-SQL&#41;](../../t-sql/data-types/constants-transact-sql.md)。|  
|*scalar_function*|這是 [!INCLUDE[tsql](../../includes/tsql-md.md)] 語法的單位，可提供特定服務並傳回單一值。 *scalar_function* 可以是內建的純量函數 (例如 SUM、GETDATE 或 CAST 函數) 或純量使用者定義函數。|  
|[ _table_name_ **.** ]|這是資料表的名稱或別名。|  
|*column*|這是資料行的名稱。 運算式中只能使用資料行的名稱。|  
|*variable*|這是變數或參數的名稱。 如需詳細資訊，請參閱 [DECLARE @local_variable &#40;Transact-SQL&#41;](../../t-sql/language-elements/declare-local-variable-transact-sql.md)。|  
|**(** _expression_  **)**|這是符合這個主題所定義的任何有效運算式。 括號是分組運算子，可確保會先評估運算式在括號內的所有運算子之後，才組合各個產生的運算式。|  
|**(** _scalar_subquery_ **)**|這是傳回單一值的子查詢。 例如：<br /><br /> `SELECT MAX(UnitPrice)`<br /><br /> `FROM Products`|  
|{ *unary_operator* }|一元運算子只適用於會評估得出數值資料類型類別目錄之任何資料類型的運算式。 這是只有單一數值運算元的運算子：<br /><br /> + 表示正數。<br /><br /> - 表示負數。<br /><br /> ~ 表示運算元的補充運算子。|  
|{ *binary_operator* }|這是定義組合兩個運算式來產生單一結果之方式的運算子。 *binary_operator* 可以是算術運算子、指派運算子 (=)、位元運算子、比較運算子、邏輯運算子、字串串連運算子 (+) 或一元運算子。 如需運算子的詳細資訊，請參閱[運算子 &#40;Transact-SQL&#41;](../../t-sql/language-elements/operators-transact-sql.md)。|  
|*ranking_windowed_function*|這是任何 [!INCLUDE[tsql](../../includes/tsql-md.md)] 次序函數。 如需詳細資訊，請參閱[次序函數 &#40;Transact-SQL&#41;](../../t-sql/functions/ranking-functions-transact-sql.md)。|  
|*aggregate_windowed_function*|這是含有 OVER 子句的任何 [!INCLUDE[tsql](../../includes/tsql-md.md)] 彙總函式。 如需詳細資訊，請參閱 [OVER 子句 &#40;Transact-SQL&#41;](../../t-sql/queries/select-over-clause-transact-sql.md)。|  
  
## <a name="expression-results"></a>運算式結果  
 單一常數、變數、純量函數或資料行名稱所組成的簡單運算式：運算式的資料類型、定序、有效位數、小數位數和值，就是所參考之元素的資料類型、定序、有效位數、小數位數和值。  
  
 當利用比較或邏輯運算子來組合兩個運算式時，產生的資料類型是布林，而值是下列項目之一：TRUE、FALSE 或 UNKNOWN。 如需布林值資料類型的詳細資訊，請參閱[比較運算子 &#40;Transact-SQL&#41;](../../t-sql/language-elements/comparison-operators-transact-sql.md)。  
  
 當利用算術、位元或字串運算子來組合兩個運算式時，運算子會決定產生的資料類型。  
  
 許多符號和運算子組成的複雜運算式會評估得出單值結果。 產生之運算式的資料類型、定序、有效位數和值取決於元件運算式的組合，每次兩個，直到到達最終結果為止。 運算式的組合順序是由運算式中的運算子優先順序所定義。  
  
## <a name="remarks"></a>備註  
 如果兩個運算式都有運算子所支援的資料類型，且至少符合下列條件之一，就可以用這個運算子來組合這兩個運算式：  
  
-   運算式有相同的資料類型。  
  
-   優先順序較低的資料類型可以隱含地轉換成優先順序較高的資料類型。  
  
 如果運算式不符合這些條件，CAST 或 CONVERT 函數可用來將優先順序較低的資料類型明確地轉換成優先順序較高的資料類型，或明確地轉換成中繼資料類型，這個中繼資料類型必須能夠隱含地轉換成優先順序較高的資料類型。  
  
 如果不支援任何隱含或明確的轉換，便無法組合這兩個運算式。  
  
 評估得出字元字串的任何運算式之定序，是由下列定序優先順序規則來設定的。 如需詳細資訊，請參閱[定序優先順序 &#40;Transact-SQL&#41;](../../t-sql/statements/collation-precedence-transact-sql.md)。  
  
 在 C 或 [!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[vbprvb](../../includes/vbprvb-md.md)] 之類的程式設計語言中，運算式一律會評估得出單一結果。 [!INCLUDE[tsql](../../includes/tsql-md.md)] 選取清單中的運算式會遵循這個規則的變化：此運算式會針對結果集中的每個資料列個別進行評估。 在結果集的每個資料列中，單一運算式可以有不同的值，但每個資料列只能有運算式的單一值。 例如，在下列 `SELECT` 陳述式中，選取清單中的 `ProductID` 參考和 `1+2` 一詞都是運算式：  
  
```sql  
USE AdventureWorks2012;  
GO  
SELECT ProductID, 1+2  
FROM Production.Product;  
GO  
```  
  
 在結果集的每個資料列中，`1+2` 運算式都會評估得出 `3`。 雖然 `ProductID` 運算式會在每個結果集資料列中產生唯一值，但每個資料列都只有 `ProductID` 的一個值。  
 
- [!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] 會配置固定的記憶體數量上限給每個執行緒，這樣就不會有執行緒用盡所有記憶體。  此記憶體有一部份會用來儲存查詢的運算式。  若查詢有太多運算式，而且其所需記憶體超過內部限制，引擎將不會執行它。  為避免發生這個問題，使用者可以將查詢變更為多個查詢，以減少每個查詢中的運算式數。 例如，您查詢的 WHERE 子句中有一長串運算式： 

```sql
DELETE FROM dbo.MyTable 
WHERE
(c1 = '0000001' AND c2 = 'A000001') or
(c1 = '0000002' AND c2 = 'A000002') or
(c1 = '0000003' AND c2 = 'A000003') or
...

```
將此查詢變更為：

```sql
DELETE FROM dbo.MyTable WHERE (c1 = '0000001' AND c2 = 'A000001');
DELETE FROM dbo.MyTable WHERE (c1 = '0000002' AND c2 = 'A000002');
DELETE FROM dbo.MyTable WHERE (c1 = '0000003' AND c2 = 'A000003');
...
```

## <a name="see-also"></a>另請參閱  
 [AT TIME ZONE &#40;Transact-SQL&#41;](../../t-sql/queries/at-time-zone-transact-sql.md)   
 [CASE &#40;Transact-SQL&#41;](../../t-sql/language-elements/case-transact-sql.md)   
 [CAST 和 CONVERT &#40;Transact-SQL&#41;](../../t-sql/functions/cast-and-convert-transact-sql.md)   
 [COALESCE &#40;Transact-SQL&#41;](../../t-sql/language-elements/coalesce-transact-sql.md)   
 [資料類型轉換 &#40;資料庫引擎&#41;](../../t-sql/data-types/data-type-conversion-database-engine.md)   
 [資料類型優先順序 &#40;Transact-SQL&#41;](../../t-sql/data-types/data-type-precedence-transact-sql.md)   
 [資料類型 &#40;Transact-SQL&#41;](../../t-sql/data-types/data-types-transact-sql.md)   
 [內建函數 &#40;Transact-SQL&#41;](~/t-sql/functions/functions.md)   
 [LIKE &#40;Transact-SQL&#41;](../../t-sql/language-elements/like-transact-sql.md)   
 [NULLIF &#40;Transact-SQL&#41;](../../t-sql/language-elements/nullif-transact-sql.md)   
 [SELECT &#40;Transact-SQL&#41;](../../t-sql/queries/select-transact-sql.md)   
 [WHERE &#40;Transact-SQL&#41;](../../t-sql/queries/where-transact-sql.md)  
  
  
