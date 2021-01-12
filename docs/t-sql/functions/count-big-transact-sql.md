---
description: COUNT_BIG (Transact-SQL)
title: COUNT_BIG (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 07/24/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- COUNT_BIG_TSQL
- COUNT_BIG
dev_langs:
- TSQL
helpviewer_keywords:
- totals [SQL Server], COUNT_BIG function
- counting items in group
- groups [SQL Server], number of items in
- number of group items
- COUNT_BIG function
ms.assetid: f2e3601f-487e-4917-bb01-47b1047908cd
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 123e1147809a7fcde2ac5b189ad32b0417242f70
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98096068"
---
# <a name="count_big-transact-sql"></a>COUNT_BIG (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

此函數會傳回群組中找到的項目數。 `COUNT_BIG` 的運作方式類似 [COUNT](../../t-sql/functions/count-transact-sql.md) 函數。 這些函數唯一的差別就是其傳回值的資料類型。 `COUNT_BIG` 一律會傳回 **bigint** 資料類型值。 `COUNT` 一律會傳回 **int** 資料類型值。
  
![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)
  
## <a name="syntax"></a>語法  
  
```syntaxsql

-- Aggregation Function Syntax  
COUNT_BIG ( { [ [ ALL | DISTINCT ] expression ] | * } )  
  
-- Analytic Function Syntax  
COUNT_BIG ( [ ALL ] { expression | * } ) OVER ( [ <partition_by_clause> ] )  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
ALL  
將彙總函式套用至所有值。 全部都可以當作預設值。
  
DISTINCT  
指定 `COUNT_BIG` 傳回唯一非 Null 值的數目。
  
*expression*  
任意類型的[運算式](../../t-sql/language-elements/expressions-transact-sql.md)。 `COUNT_BIG` 不支援運算式中的彙總函數或子查詢。
  
*\**  
指定 `COUNT_BIG` 應該計算所有資料列，以判斷要傳回的總資料表資料列計數。 `COUNT_BIG(*)` 不接受任何參數，而且不支援使用 DISTINCT。 `COUNT_BIG(*)` 不需要 *expression* 參數，因為依照定義，它不會使用任何特定資料行的相關資訊。 `COUNT_BIG(*)` 會傳回指定資料表的資料列數，而且它會保留重複的資料列。 會分開計算每個資料列，包括含有 null 值的資料列。
  
OVER **(** [ *partition_by_clause* ] [ *order_by_clause* ] **)**  
*partition_by_clause* 會將 `FROM` 子句產生的結果集，分割成 `COUNT_BIG` 函數所要套用的資料分割。 如未指定，此函數會將查詢結果集的所有資料列視為單一群組。 *order_by_clause* 會決定作業的邏輯順序。 如需詳細資訊，請參閱 [OVER 子句 &#40;Transact-SQL&#41;](../../t-sql/queries/select-over-clause-transact-sql.md)。
  
## <a name="return-types"></a>傳回類型
**bigint**
  
## <a name="remarks"></a>備註  
COUNT_BIG(\*) 會傳回群組中的項目數。 其中包括 NULL 值和複本。
  
COUNT_BIG (ALL *expression*) 會針對群組中的每個資料列來評估 *expression*，且會傳回非 Null 值的數目。
  
COUNT_BIG (DISTINCT *expression*) 會針對群組中的每個資料列來評估 *expression*，且會傳回唯一且非 Null 值的數目。
  
COUNT_BIG **不搭配** OVER 和 ORDER BY 子句使用時，是具決定性的函數。 COUNT_BIG **搭配** OVER 和 ORDER BY 子句使用時，不具決定性。 如需詳細資訊，請參閱[決定性與非決定性函數](../../relational-databases/user-defined-functions/deterministic-and-nondeterministic-functions.md)。
  
## <a name="examples"></a>範例  
如需範例，請參閱 [COUNT &#40;Transact-SQL&#41;](../../t-sql/functions/count-transact-sql.md)。
  
## <a name="see-also"></a>另請參閱
[彙總函數 &#40;Transact-SQL&#41;](../../t-sql/functions/aggregate-functions-transact-sql.md)  
[COUNT &#40;Transact-SQL&#41;](../../t-sql/functions/count-transact-sql.md)  
[int、bigint、smallint 和 tinyint &#40;Transact-SQL&#41;](../../t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql.md)  
[OVER 子句 &#40;Transact-SQL&#41;](../../t-sql/queries/select-over-clause-transact-sql.md)
  
  
