---
description: CHECKSUM_AGG (Transact-SQL)
title: CHECKSUM_AGG (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 07/24/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- CHECKSUM_AGG
- CHECKSUM_AGG_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- checksum values
- CHECKSUM_AGG function
- groups [SQL Server], checksum values
ms.assetid: cdede70c-4eb5-4c92-98ab-b07787ab7222
author: cawrites
ms.author: chadam
ms.openlocfilehash: 2bea0aad7839bc0b487b77b6ac1434396b84b765
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98102630"
---
# <a name="checksum_agg-transact-sql"></a>CHECKSUM_AGG (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

此函式會傳回群組中值的總和檢查碼。 `CHECKSUM_AGG` 會忽略 Null 值。 [OVER 子句](../../t-sql/queries/select-over-clause-transact-sql.md)可以跟在 `CHECKSUM_AGG` 後面。
  
![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)
  
## <a name="syntax"></a>語法  
  
```syntaxsql
CHECKSUM_AGG ( [ ALL | DISTINCT ] expression )  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
**ALL**  
將彙總函式套用至所有值。 ALL 是預設引數。
  
DISTINCT  
指定 `CHECKSUM_AGG` 傳回唯一值的總和檢查碼。
  
*expression*  
整數[運算式](../../t-sql/language-elements/expressions-transact-sql.md)。 `CHECKSUM_AGG` 不允許使用彙總函式或子查詢。
  
## <a name="return-types"></a>傳回類型
將所有 *expression* 值的總和檢查碼作為 **int** 傳回。
  
## <a name="remarks"></a>備註  
`CHECKSUM_AGG` 可以偵測資料表中的變更。
  
`CHECKSUM_AGG` 結果與資料表中資料列的順序無關。 此外，`CHECKSUM_AGG` 函式允許使用 `DISTINCT` 關鍵字和 `GROUP BY` 子句。
  
如果運算式清單值變更，則清單總和檢查碼值清單也可能會變更。 不過，計算的總和檢查碼有極小的可能不會變更。
  
`CHECKSUM_AGG` 的功能與其他彙總函式的功能類似。 如需詳細資訊，請參閱[彙總函式 &#40;Transact-SQL&#41;](../../t-sql/functions/aggregate-functions-transact-sql.md)。
  
## <a name="examples"></a>範例  
這些範例使用 `CHECKSUM_AGG` 來偵測 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 資料庫中 `ProductInventory` 資料表之 `Quantity` 資料行的變更。
  
```sql
--Get the checksum value before the column value is changed.  

SELECT CHECKSUM_AGG(CAST(Quantity AS INT))  
FROM Production.ProductInventory;  
GO  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
------------------------  
262  
```  
  
```sql
UPDATE Production.ProductInventory   
SET Quantity=125  
WHERE Quantity=100;  
GO  

--Get the checksum of the modified column.  
SELECT CHECKSUM_AGG(CAST(Quantity AS INT))  
FROM Production.ProductInventory;  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
------------------------  
287  
```  
  
## <a name="see-also"></a>請參閱
[CHECKSUM &#40;Transact-SQL&#41;](../../t-sql/functions/checksum-transact-sql.md)  
[HASHBYTES &#40;Transact-SQL&#41;](../../t-sql/functions/hashbytes-transact-sql.md)  
[BINARY_CHECKSUM &#40;Transact-SQL&#41;](../../t-sql/functions/binary-checksum-transact-sql.md)
[OVER 子句 &#40;Transact-SQL&#41;](../../t-sql/queries/select-over-clause-transact-sql.md)
  
