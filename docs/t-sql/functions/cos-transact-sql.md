---
description: COS (Transact-SQL)
title: COS (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 07/24/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- COS
- COS_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- cosine
- COS function
ms.assetid: c9fa8ae1-3373-4f3e-9b97-fa05077c1040
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 96e0eb9dd3806ae10502a66b9738b589a721d110
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98097250"
---
# <a name="cos-transact-sql"></a>COS (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

這是一種數學函數，會在指定的運算式中，傳回指定角度 (以弧度為單位) 的三角餘弦函數。
  
![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)
  
## <a name="syntax"></a>語法  
  
```syntaxsql
COS ( float_expression )  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
*float_expression*  
一個 **float** 類型的 [運算式](../../t-sql/language-elements/expressions-transact-sql.md)。
  
## <a name="return-types"></a>傳回類型
**float**
  
## <a name="examples"></a>範例  
這個範例會傳回指定角度的 `COS` 值：
  
```sql
  DECLARE @angle FLOAT;  
SET @angle = 14.78;  
SELECT 'The COS of the angle is: ' + CONVERT(VARCHAR,COS(@angle));  
GO  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
The COS of the angle is: -0.599465                        
  
(1 row(s) affected)  
```  
  
[!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 和 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  


這個範例會傳回指定角度的 COS 值：
  
```sql
SELECT COS(14.76) AS cosCalc1, COS(-0.1472738) AS cosCalc2;   
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
cosCalc1  cosCalc2
--------  --------
-0.58     0.99
```
  
## <a name="see-also"></a>另請參閱
[數學函數 &#40;Transact-SQL&#41;](../../t-sql/functions/mathematical-functions-transact-sql.md)
  
  

