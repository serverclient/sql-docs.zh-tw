---
description: BREAK (Transact-SQL)
title: BREAK (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 11/19/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- BREAK
- BREAK_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- exiting innermost loop [SQL Server]
- END keyword
- ignored statements
- BREAK keyword
ms.assetid: 67c30b8d-3f15-41ad-b9a9-a4ced3b2af9f
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: d4fdea1af5b30d916b2323172653a2d59f167bc8
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98092242"
---
# <a name="break-transact-sql"></a>BREAK (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

BREAK 會退出目前的 WHILE 迴圈。 若目前的 WHILE 迴圈位於另一個迴圈的巢狀結構內，則 BREAK 只會退出目前的迴圈，將控制權交給外部迴圈的下一個陳述式。

BREAK 通常位於 IF 陳述式內。

## <a name="examples"></a>範例

```sql
WHILE (1=1)
BEGIN
   IF EXISTS (SELECT * FROM ##MyTempTable WHERE EventCode = 'Done')
   BEGIN
      BREAK;  -- 'Done' row has finally been inserted and detected, so end this loop.
   END

   PRINT N'The other process is not yet done.';  -- Re-confirm the non-done status to the console.
   WAITFOR DELAY '00:01:30';  -- Sleep for 90 seconds.
END
```

## <a name="see-also"></a>另請參閱

- [流程控制語言 &#40;Transact-SQL&#41;](~/t-sql/language-elements/control-of-flow.md)
- [WHILE &#40;Transact-SQL&#41;](../../t-sql/language-elements/while-transact-sql.md)
- [IF...ELSE &#40;Transact-SQL&#41;](../../t-sql/language-elements/if-else-transact-sql.md)

