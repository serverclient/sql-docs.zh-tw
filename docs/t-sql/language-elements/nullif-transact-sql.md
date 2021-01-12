---
description: NULLIF (Transact-SQL)
title: NULLIF (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 09/08/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- NULLIF
- NULLIF_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- null values [SQL Server], equivalent expressions
- expressions [SQL Server], null values
- NULLIF function
- equivalent expressions [SQL Server]
ms.assetid: 44c7b67e-74c7-4bb9-93a4-7a3016bd2feb
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 0bcf87ce6ab8dae4bcf509a204d486e9a809eedd
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98093538"
---
# <a name="nullif-transact-sql"></a>NULLIF (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  如果兩個指定的運算式相等，便傳回 Null 值。 例如，`SELECT NULLIF(4,4) AS Same, NULLIF(5,7) AS Different;` 會針對第一個資料行 (4 和 4) 傳回 NULL，因為這兩個輸入值一樣。 第二個資料行會傳回第一個值 (5)，因為這兩個輸入值不同。 
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql  
NULLIF ( expression , expression )  
```  
  
## <a name="arguments"></a>引數  
 *expression*  
 這是任何有效的純量[運算式](../../t-sql/language-elements/expressions-transact-sql.md)。  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="return-types"></a>傳回型別
 傳回與第一個 *expression* 相同的類型。  
  
 如果這兩個運算式不相等，NULLIF 會傳回第一個 *expression*。 如果運算式相等，NULLIF 會傳回第一個 *expression* 類型的 Null 值。  
  
## <a name="remarks"></a>備註  
 NULLIF 相當於兩個運算式相等且產生的運算式為 NULL 的搜尋 CASE 運算式。  
  
 我們建議您不要在 NULLIF 函數中使用時間相依函數，例如 RAND()。 這可能會導致系統評估此函數兩次，並從這兩個引動過程傳回不同的結果。  
  
## <a name="examples"></a>範例  
  
### <a name="a-returning-budget-amounts-that-have-not-changed"></a>A. 傳回尚未變更的預算數量  
 下列範例會建立一份 `budgets` 資料表，來顯示部門 (`dept`) 及其目前預算 (`current_year`) 和前一年的預算 (`previous_year`)。 對今年而言，如果部門預算與前一年相同，就使用 `NULL`，如果預算仍未確定，就使用 `0`。 若只要知道收到預算之部門的平均值，且要併入前一年的預算值 (使用 `previous_year` 值，其中 `current_year` 是 `NULL`)，便將 `NULLIF` 和 `COALESCE` 函數組合起來。  
  
```sql  
CREATE TABLE dbo.budgets  
(  
   dept            TINYINT   IDENTITY,  
   current_year    DECIMAL   NULL,  
   previous_year   DECIMAL   NULL  
);  
INSERT budgets VALUES(100000, 150000);  
INSERT budgets VALUES(NULL, 300000);  
INSERT budgets VALUES(0, 100000);  
INSERT budgets VALUES(NULL, 150000);  
INSERT budgets VALUES(300000, 250000);  
GO    
SET NOCOUNT OFF;  
SELECT AVG(NULLIF(COALESCE(current_year,  
   previous_year), 0.00)) AS 'Average Budget'  
FROM budgets;  
GO  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
 ```
 Average Budget  
 --------------  
 212500.000000  
 (1 row(s) affected)
 ```  
  
### <a name="b-comparing-nullif-and-case"></a>B. 比較 NULLIF 和 CASE  
 下列查詢會評估 `NULLIF` 和 `CASE` 資料行中的值是否相同，以顯示 `MakeFlag` 和 `FinishedGoodsFlag` 之間的相似度。 第一個查詢使用 `NULLIF`。 第二個查詢則使用 `CASE` 運算式。  
  
```sql  
USE AdventureWorks2012;  
GO  
SELECT ProductID, MakeFlag, FinishedGoodsFlag,   
   NULLIF(MakeFlag,FinishedGoodsFlag) AS 'Null if Equal'  
FROM Production.Product  
WHERE ProductID < 10;  
GO  
  
SELECT ProductID, MakeFlag, FinishedGoodsFlag,'Null if Equal' =  
   CASE  
       WHEN MakeFlag = FinishedGoodsFlag THEN NULL  
       ELSE MakeFlag  
   END  
FROM Production.Product  
WHERE ProductID < 10;  
GO  
```  

### <a name="c-returning-budget-amounts-that-contain-no-data"></a>C：傳回未包含資料的預算金額  
 下列範例會建立 `budgets` 資料表、載入資料，然後使用 `NULLIF` 傳回 Null (如果 `current_year` 和 `previous_year` 都未包含資料)。  
  
```sql  
CREATE TABLE budgets (  
   dept           TINYINT,  
   current_year   DECIMAL(10,2),  
   previous_year  DECIMAL(10,2)  
);  
  
INSERT INTO budgets VALUES(1, 100000, 150000);  
INSERT INTO budgets VALUES(2, NULL, 300000);  
INSERT INTO budgets VALUES(3, 0, 100000);  
INSERT INTO budgets VALUES(4, NULL, 150000);  
INSERT INTO budgets VALUES(5, 300000, 300000);  
  
SELECT dept, NULLIF(current_year,  
   previous_year) AS LastBudget  
FROM budgets;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
 ```
 dept   LastBudget  
 ----   -----------  
 1      100000.00  
 2      null 
 3      0.00  
 4      null  
 5      null
 ```  
  
## <a name="see-also"></a>另請參閱  
 [CASE &#40;Transact-SQL&#41;](../../t-sql/language-elements/case-transact-sql.md)   
 [decimal 和 numeric &#40;Transact-SQL&#41;](../../t-sql/data-types/decimal-and-numeric-transact-sql.md)   
 [系統函數 &#40;Transact-SQL&#41;](../../relational-databases/system-functions/system-functions-category-transact-sql.md)  
  
  

