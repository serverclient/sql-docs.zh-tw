---
description: ACOS (Transact-SQL)
title: ACOS (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 07/24/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- ACOS
- ACOS_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- cosine
- ACOS function
- arccosine
ms.assetid: 4ec6b46e-9438-4f0f-8b96-461edd84280a
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 152de67ae9c1cb8863088fa6c4636b4d3de4d1d3
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98090399"
---
# <a name="acos-transact-sql"></a>ACOS (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

函數，傳回以弧度為單位的角度，其餘弦值為指定的 float 運算式。 這也稱為反餘弦函數 (Arccosine)。
  
![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)
  
## <a name="syntax"></a>語法  
  
```syntaxsql
ACOS ( float_expression )  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
*float_expression*  
[運算式](../../t-sql/language-elements/expressions-transact-sql.md)，為 **float** 類型或是能隱含地轉換成 float 的類型。 只有範圍在 -1.00 到 1.00 之間的值為有效。 在這個範圍之外的值會傳回 NULL，且 ASIN 會報告定義域錯誤。
  
## <a name="return-types"></a>傳回型別  
**float**
  
## <a name="examples"></a>範例  
這個範例會傳回指定數字的 `ACOS` 值。
  
```sql
SET NOCOUNT OFF;  
DECLARE @cos FLOAT;  
SET @cos = -1.0;  
SELECT 'The ACOS of the number is: ' + CONVERT(VARCHAR, ACOS(@cos));  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
---------------------------------   
The ACOS of the number is: 3.14159   
  
(1 row(s) affected)  
```  
  
## <a name="see-also"></a>另請參閱
[數學函數 &#40;Transact-SQL&#41;](../../t-sql/functions/mathematical-functions-transact-sql.md)  
[函式](../../t-sql/functions/functions.md)
  
  

