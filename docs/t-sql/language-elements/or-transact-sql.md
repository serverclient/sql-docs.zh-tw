---
description: OR (Transact-SQL)
title: OR (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- OR_TSQL
- OR
dev_langs:
- TSQL
helpviewer_keywords:
- conditions [SQL Server], combining
- combining conditions
- OR operator
ms.assetid: b730a256-4a63-4880-9906-65b05cd9caf2
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 237eafc6386bbf6bbe3d4ec7b8e6fc5f99ffb41c
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98095960"
---
# <a name="or-transact-sql"></a>OR (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  結合兩個條件。 當在陳述式中使用一個以上的邏輯運算子時，OR 運算子會在 AND 運算子之後評估。 然而，您可以使用括號來變更驗算的順序。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
boolean_expression OR boolean_expression  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *boolean_expression*  
 這是任何會傳回 TRUE、FALSE 或 UNKNOWN 的有效[運算式](../../t-sql/language-elements/expressions-transact-sql.md)。  
  
## <a name="result-types"></a>結果類型  
 **布林值**  
  
## <a name="result-value"></a>結果值  
 當任何一個條件是 TRUE 時，OR 會傳回 TRUE。  
  
## <a name="remarks"></a>備註  
 下表會顯示 OR 運算子的結果。  
  
||true|false|UNKNOWN|  
|------|----------|-----------|-------------|  
|**TRUE**|TRUE|TRUE|TRUE|  
|**FALSE**|true|false|UNKNOWN|  
|**UNKNOWN**|true|UNKNOWN|UNKNOWN|  
  
## <a name="examples"></a>範例  
 下列範例會使用 `vEmployeeDepartmentHistory` 檢視，來擷取上夜班或大夜班的 `Quality Assurance` 人員名稱。 如果省略括號，查詢會傳回中班的 `Quality Assurance` 員工與大夜班的所有員工。  
  
```sql  
-- Uses AdventureWorks  
  
SELECT FirstName, LastName, Shift   
FROM HumanResources.vEmployeeDepartmentHistory  
WHERE Department = 'Quality Assurance'  
   AND (Shift = 'Evening' OR Shift = 'Night');  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
 ```
 FirstName    LastName         Shift 
 ------------ ---------------- ------- 
 Andreas      Berglund         Evening 
 Sootha       Charncherngkha   Night
 ```  
  
## <a name="examples-sssdwfull-and-sspdw"></a>範例：[!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 和 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
 下列範例會擷取所賺得的 `BaseRate` 少於 20 或 `HireDate` 為 2001 年 1 月 1 日或更晚日期的員工名稱。  
  
```sql  
-- Uses AdventureWorks  
  
SELECT FirstName, LastName, BaseRate, HireDate   
FROM DimEmployee  
WHERE BaseRate < 10 OR HireDate >= '2001-01-01';  
```  
  
## <a name="see-also"></a>另請參閱  
 [運算式 &#40;Transact-SQL&#41;](../../t-sql/language-elements/expressions-transact-sql.md)   
 [內建函數 &#40;Transact-SQL&#41;](~/t-sql/functions/functions.md)   
 [運算子 &#40;Transact-SQL&#41;](../../t-sql/language-elements/operators-transact-sql.md)   
 [SELECT &#40;Transact-SQL&#41;](../../t-sql/queries/select-transact-sql.md)   
 [WHERE &#40;Transact-SQL&#41;](../../t-sql/queries/where-transact-sql.md)  
  
  


