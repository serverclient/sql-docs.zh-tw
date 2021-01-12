---
description: SET XACT_ABORT (Transact-SQL)
title: SET XACT_ABORT (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 10/03/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- XACT_ABORT_TSQL
- XACT_ABORT
- SET XACT_ABORT
- SET_XACT_ABORT_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- transaction rollbacks [SQL Server]
- XACT_ABORT option
- automatic transaction roll backs
- transactions [SQL Server], rolling back
- rolling back transactions, SET XACT_ABORT
- roll back transactions [SQL Server]
- SET XACT_ABORT statement
ms.assetid: cbcaa433-58f2-4dc3-a077-27273bef65b5
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 29737fa62f99028ebf35bca5d8e9c0e94fa1af21
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98079843"
---
# <a name="set-xact_abort-transact-sql"></a>SET XACT_ABORT (Transact-SQL)

[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

> [!NOTE]
> **THROW** 陳述式接受 **SET XACT_ABORT**。 **RAISERROR** 則不接受。 新的應用程式應該使用 **THROW**，而非 **RAISERROR**。

指定當 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 陳述式產生執行階段錯誤時，[!INCLUDE[tsql](../../includes/tsql-md.md)] 是否自動回復目前的交易。

![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)

## <a name="syntax"></a>語法

```syntaxsql
SET XACT_ABORT { ON | OFF }
```

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="remarks"></a>備註

當 SET XACT_ABORT 是 ON 時，如果 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式產生執行階段錯誤，就會終止和回復整個交易。

當 SET XACT_ABORT 是 OFF 時，在某些情況下，只會回復產生錯誤的 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式，交易會繼續進行。 隨著錯誤嚴重性而不同，即使 SET XACT_ABORT 是 OFF，也有可能回復整個交易。 OFF 是 T-SQL 陳述式中的預設設定，而 ON 是觸發程式中的預設設定。

SET XACT_ABORT 不會影響到如語法錯誤之類的編譯錯誤。

針對大部分 OLE DB 提供者 (包括 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]) 的隱含或明確的交易，其中之資料修改陳述式的 XACT_ABORT 都必須設為 ON。 只有在提供者支援巢狀交易時，才不需要這個選項。

當 ANSI_WARNINGS=OFF 時，權限違規會造成交易中止。

SET XACT_ABORT 的設定是在執行階段進行設定，而不是在剖析階段進行設定。

若要檢視此設定的目前設定，請執行下列查詢。

```sql
DECLARE @XACT_ABORT VARCHAR(3) = 'OFF';
IF ( (16384 & @@OPTIONS) = 16384 ) SET @XACT_ABORT = 'ON';
SELECT @XACT_ABORT AS XACT_ABORT;

```

## <a name="examples"></a>範例

下列程式碼範例會使有其他 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式的交易發生外部索引鍵違規錯誤。 在第一組陳述式中，會產生錯誤，但其他陳述式仍能順利執行，且會順利確認交易。 在第二組陳述式中，`SET XACT_ABORT` 設為 `ON`。 這會使陳述式錯誤終止批次，且會回復交易。

```sql
IF OBJECT_ID(N't2', N'U') IS NOT NULL
    DROP TABLE t2;
GO
IF OBJECT_ID(N't1', N'U') IS NOT NULL
    DROP TABLE t1;
GO  
CREATE TABLE t1
    (a INT NOT NULL PRIMARY KEY);
CREATE TABLE t2
    (a INT NOT NULL REFERENCES t1(a));
GO
INSERT INTO t1 VALUES (1);
INSERT INTO t1 VALUES (3);
INSERT INTO t1 VALUES (4);
INSERT INTO t1 VALUES (6);
GO
SET XACT_ABORT OFF;
GO
BEGIN TRANSACTION;
INSERT INTO t2 VALUES (1);
INSERT INTO t2 VALUES (2); -- Foreign key error.
INSERT INTO t2 VALUES (3);
COMMIT TRANSACTION;
GO
SET XACT_ABORT ON;
GO
BEGIN TRANSACTION;
INSERT INTO t2 VALUES (4);
INSERT INTO t2 VALUES (5); -- Foreign key error.
INSERT INTO t2 VALUES (6);
COMMIT TRANSACTION;
GO
-- SELECT shows only keys 1 and 3 added.
-- Key 2 insert failed and was rolled back, but
-- XACT_ABORT was OFF and rest of transaction
-- succeeded.
-- Key 5 insert error with XACT_ABORT ON caused
-- all of the second transaction to roll back.
SELECT *
 FROM t2;
GO
```

## <a name="see-also"></a>另請參閱

- [THROW &#40;Transact-SQL&#41;](../../t-sql/language-elements/throw-transact-sql.md)
- [BEGIN TRANSACTION &#40;Transact-SQL&#41;](../../t-sql/language-elements/begin-transaction-transact-sql.md)
- [COMMIT TRANSACTION &#40;Transact-SQL&#41;](../../t-sql/language-elements/commit-transaction-transact-sql.md)
- [ROLLBACK TRANSACTION &#40;Transact-SQL&#41;](../../t-sql/language-elements/rollback-transaction-transact-sql.md)
- [SET 陳述式 &#40;Transact-SQL&#41;](../../t-sql/statements/set-statements-transact-sql.md)
- [@@TRANCOUNT &#40;Transact-SQL&#41;](../../t-sql/functions/trancount-transact-sql.md)  
