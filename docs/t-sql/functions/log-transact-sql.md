---
description: LOG (Transact-SQL)
title: LOG (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 07/29/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- LOG
- LOG_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- float expressions
- logarithm of expression
- LOG function
ms.assetid: f7c39511-cd84-4362-93ba-0d93655217ee
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 23e3d0f7674d42f172c05f67869f6814817457f2
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98092240"
---
# <a name="log-transact-sql"></a>LOG (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  傳回 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中指定之 **float** 運算式的自然對數。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
-- Syntax for SQL Server, Azure SQL Database  
  
LOG ( float_expression [, base ] )  
```  
  
```syntaxsql
-- Syntax for Azure Synapse SQL 
  
LOG ( float_expression )  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *float_expression*  
 為 **float** 類型或能夠隱含轉換成 **float** 類型的 [運算式](../../t-sql/language-elements/expressions-transact-sql.md)。  
  
 *base*  
 可設定對數基數的選擇性整數引數。  
  
**適用於**：[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 和更新版本
  
## <a name="return-types"></a>傳回型別  
 **float**  
  
## <a name="remarks"></a>備註  
 根據預設，**LOG()** 會傳回自然對數。 從 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 開始，您可以使用選擇性的 *base* 參數，將對數的基數變更為其他值。  
  
 自然對數是底數 **e** 的對數，其中 **e** 是大約等於 2.718281828 的無理常數。  
  
 數字之指數的自然對數就是該數字本身：LOG( EXP( *n* ) ) = *n*。 而數字之自然對數的指數就是該數字本身：EXP( LOG( *n* ) ) = *n*。  
  
## <a name="examples"></a>範例  
  
### <a name="a-calculating-the-logarithm-for-a-number"></a>A. 計算數值的對數。  
 下列範例會計算指定 **float** 運算式的 `LOG`。  
  
```sql  
DECLARE @var FLOAT = 10;  
SELECT 'The LOG of the variable is: ' + CONVERT(VARCHAR, LOG(@var));  
GO  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
-------------------------------------  
The LOG of the variable is: 2.30259  
  
(1 row(s) affected)  
```  
  
### <a name="b-calculating-the-logarithm-of-the-exponent-of-a-number"></a>B. 計算數值之指數的對數。  
 下列範例會計算一個數值之指數的 `LOG`。  
  
```sql  
SELECT LOG (EXP (10));  
  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
----------------------------------  
10  
(1 row(s) affected)  
```  
  
## <a name="examples-sssdwfull-and-sspdw"></a>範例：[!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 和 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
### <a name="c-calculating-the-logarithm-for-a-number"></a>C. 計算數值的對數  
 下列範例會計算指定 **float** 運算式的 `LOG`。  
  
```sql  
SELECT LOG(10);  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
 ```
 ----------------`  
  
 2.30
 ```  
  
## <a name="see-also"></a>另請參閱  
 [數學函數 &#40;Transact-SQL&#41;](../../t-sql/functions/mathematical-functions-transact-sql.md)   
 [EXP &#40;Transact-SQL&#41;](../../t-sql/functions/exp-transact-sql.md)   
 [LOG10 &#40;Transact-SQL&#41;](../../t-sql/functions/log10-transact-sql.md)  
  
  

