---
description: 彙總函式 (Transact-SQL)
title: 彙總函式 (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 08/15/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
dev_langs:
- TSQL
helpviewer_keywords:
- functions [SQL Server], aggregate
- aggregate functions [SQL Server], about aggregate functions
- summarizing functions
- aggregate functions [SQL Server]
ms.assetid: 0c06ae42-eb0a-4d77-9d74-aa1e7f344009
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 48e47f1f81744765c8fe76080a5c62ba70089327
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98090958"
---
# <a name="aggregate-functions-transact-sql"></a>彙總函式 (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

彙總函式會根據一組值來執行計算，並傳回單一值。 除了 `COUNT(*)` 之外，彙總函式會忽略 Null 值。 彙總函式經常用來搭配 SELECT 陳述式的 GROUP BY 子句使用。
  
所有彙總函式都具有決定性。 換句話說，使用一組特定輸入值呼叫時，彙總函式會在每次呼叫時傳回相同的值。 如需函式確定性的詳細資訊，請參閱[決定性和非決定性函式](../../relational-databases/user-defined-functions/deterministic-and-nondeterministic-functions.md)。 [OVER 子句](../../t-sql/queries/select-over-clause-transact-sql.md)可以跟在所有彙總函式之後，但 STRING_AGG、GROUPING 和 GROUPING_ID 函式除外。
  
只有在下列情況下，才能使用彙總函式作為運算式：
-   SELECT 陳述式的選取清單 (子查詢或外部查詢)。  
-   HAVING 子句。  
  
[!INCLUDE[tsql](../../includes/tsql-md.md)] 提供下列彙總函式：

- [APPROX_COUNT_DISTINCT](../../t-sql/functions/approx-count-distinct-transact-sql.md)
- [AVG](../../t-sql/functions/avg-transact-sql.md)
- [CHECKSUM_AGG](../../t-sql/functions/checksum-agg-transact-sql.md)
- [COUNT](../../t-sql/functions/count-transact-sql.md)
- [COUNT_BIG](../../t-sql/functions/count-big-transact-sql.md)
- [GROUPING](../../t-sql/functions/grouping-transact-sql.md)
- [GROUPING_ID](../../t-sql/functions/grouping-id-transact-sql.md)
- [MAX](../../t-sql/functions/max-transact-sql.md)
- [MIN](../../t-sql/functions/min-transact-sql.md)
- [STDEV](../../t-sql/functions/stdev-transact-sql.md)
- [STDEVP](../../t-sql/functions/stdevp-transact-sql.md)
- [STRING_AGG](../../t-sql/functions/string-agg-transact-sql.md)
- [SUM](../../t-sql/functions/sum-transact-sql.md)
- [VAR](../../t-sql/functions/var-transact-sql.md)
- [VARP](../../t-sql/functions/varp-transact-sql.md)
  
## <a name="see-also"></a>另請參閱
[內建函數 &#40;Transact-SQL&#41;](../../t-sql/functions/functions.md)  
[OVER 子句 &#40;Transact-SQL&#41;](../../t-sql/queries/select-over-clause-transact-sql.md)
  
  
