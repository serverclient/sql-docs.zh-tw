---
description: SET 陳述式 (Transact-SQL)
title: SET 陳述式 (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- SET
- SET_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- ISO SET statements
- queries [SQL Server], executing
- dates [SQL Server], SET statements
- time [SQL Server], SET statements
- SET statement, about SET statement
- SET statement
- statistical information [SQL Server], SET statements
- locking [SQL Server], SET statements
ms.assetid: f7e107f8-0fcf-408b-b30f-da2323eeb714
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: = azure-sqldw-latest ||= azuresqldb-current || >= sql-server-2016 || >= sql-server-linux-2017
ms.openlocfilehash: e5c2bf8f1bfe5815a8eb696b5b837d70fbb9b21b
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98079238"
---
# <a name="set-statements-transact-sql"></a>SET 陳述式 (Transact-SQL)
[!INCLUDE[tsql-appliesto-ss2008-xxxx-asdw-xxx-md](../../includes/tsql-appliesto-ss2008-xxxx-asdw-xxx-md.md)]

[!INCLUDE[tsql](../../includes/tsql-md.md)] 程式設計語言提供了許多 SET 陳述式，供您變更目前工作階段對於特定資訊的處理。 這些 SET 陳述式可分組成下表所顯示的類別目錄。  
  
如需如何使用 SET 陳述式來設定區域變數的資訊，請參閱 [SET @local_variable &#40;Transact-SQL&#41;](../../t-sql/language-elements/set-local-variable-transact-sql.md)。  
  
|類別|陳述式|  
|--------------|----------------|  
|日期和時間陳述式|[SET DATEFIRST](../../t-sql/statements/set-datefirst-transact-sql.md)<br /><br /> [SET DATEFORMAT](../../t-sql/statements/set-dateformat-transact-sql.md)|  
|鎖定陳述式|[SET DEADLOCK_PRIORITY](../../t-sql/statements/set-deadlock-priority-transact-sql.md)<br /><br /> [SET LOCK_TIMEOUT](../../t-sql/statements/set-lock-timeout-transact-sql.md)|  
|其他陳述式|[SET CONCAT_NULL_YIELDS_NULL](../../t-sql/statements/set-concat-null-yields-null-transact-sql.md)<br /><br /> [SET CURSOR_CLOSE_ON_COMMIT](../../t-sql/statements/set-cursor-close-on-commit-transact-sql.md)<br /><br /> [SET FIPS_FLAGGER](../../t-sql/statements/set-fips-flagger-transact-sql.md)<br /><br /> [SET IDENTITY_INSERT](../../t-sql/statements/set-identity-insert-transact-sql.md)<br /><br /> [SET LANGUAGE](../../t-sql/statements/set-language-transact-sql.md)<br /><br /> [SET OFFSETS](../../t-sql/statements/set-offsets-transact-sql.md)<br /><br /> [SET QUOTED_IDENTIFIER](../../t-sql/statements/set-quoted-identifier-transact-sql.md)|  
|查詢執行陳述式|[SET ARITHABORT](../../t-sql/statements/set-arithabort-transact-sql.md)<br /><br /> [SET ARITHIGNORE](../../t-sql/statements/set-arithignore-transact-sql.md)<br /><br /> [SET FMTONLY](../../t-sql/statements/set-fmtonly-transact-sql.md)<br /> 注意：[!INCLUDE[ssNoteDepFutureAvoid](../../includes/ssnotedepfutureavoid-md.md)]<br /><br /> [SET NOCOUNT](../../t-sql/statements/set-nocount-transact-sql.md)<br /><br /> [SET NOEXEC](../../t-sql/statements/set-noexec-transact-sql.md)<br /><br /> [SET NUMERIC_ROUNDABORT](../../t-sql/statements/set-numeric-roundabort-transact-sql.md)<br /><br /> [SET PARSEONLY](../../t-sql/statements/set-parseonly-transact-sql.md)<br /><br /> [SET QUERY_GOVERNOR_COST_LIMIT](../../t-sql/statements/set-query-governor-cost-limit-transact-sql.md)<br /><br /> [SET RESULT SET CACHING](../../t-sql/statements/set-result-set-caching-transact-sql.md?view=azure-sqldw-latest&preserve-view=true) (預覽)<br /> 注意:本功能只適用於 [!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)]。<br /><br /> [SET ROWCOUNT](../../t-sql/statements/set-rowcount-transact-sql.md)<br /><br /> [SET TEXTSIZE](../../t-sql/statements/set-textsize-transact-sql.md)|  
|ISO 設定陳述式|[SET ANSI_DEFAULTS](../../t-sql/statements/set-ansi-defaults-transact-sql.md)<br /><br /> [SET ANSI_NULL_DFLT_OFF](../../t-sql/statements/set-ansi-null-dflt-off-transact-sql.md)<br /><br /> [SET ANSI_NULL_DFLT_ON](../../t-sql/statements/set-ansi-null-dflt-on-transact-sql.md)<br /><br /> [SET ANSI_NULLS](../../t-sql/statements/set-ansi-nulls-transact-sql.md)<br /><br /> [SET ANSI_PADDING](../../t-sql/statements/set-ansi-padding-transact-sql.md)<br /><br /> [SET ANSI_WARNINGS](../../t-sql/statements/set-ansi-warnings-transact-sql.md)|  
|統計資料陳述式|[SET FORCEPLAN](../../t-sql/statements/set-forceplan-transact-sql.md)<br /><br /> [SET SHOWPLAN_ALL](../../t-sql/statements/set-showplan-all-transact-sql.md)<br /><br /> [SET SHOWPLAN_TEXT](../../t-sql/statements/set-showplan-text-transact-sql.md)<br /><br /> [SET SHOWPLAN_XML](../../t-sql/statements/set-showplan-xml-transact-sql.md)<br /><br /> [SET STATISTICS IO](../../t-sql/statements/set-statistics-io-transact-sql.md)<br /><br /> [SET STATISTICS XML](../../t-sql/statements/set-statistics-xml-transact-sql.md)<br /><br /> [SET STATISTICS PROFILE](../../t-sql/statements/set-statistics-profile-transact-sql.md)<br /><br /> [SET STATISTICS TIME](../../t-sql/statements/set-statistics-time-transact-sql.md)|  
|交易陳述式|[SET IMPLICIT_TRANSACTIONS](../../t-sql/statements/set-implicit-transactions-transact-sql.md)<br /><br /> [SET REMOTE_PROC_TRANSACTIONS](../../t-sql/statements/set-remote-proc-transactions-transact-sql.md)<br /><br /> [SET TRANSACTION ISOLATION LEVEL](../../t-sql/statements/set-transaction-isolation-level-transact-sql.md)<br /><br /> [SET XACT_ABORT](../../t-sql/statements/set-xact-abort-transact-sql.md)| 
  
## <a name="considerations-when-you-use-the-set-statements"></a>SET 陳述式的使用考量  
  
- 所有 SET 陳述式都是在執行階段執行，但以下陳述式除外，這些陳述式是在剖析階段執行：

  - SET FIPS_FLAGGER
  - SET OFFSETS
  - SET PARSEONLY
  - 和 SET QUOTED_IDENTIFIER  
  
- 如果在預存程序或觸發程序中執行 SET 陳述式，則在預存程序或觸發程序傳回控制權之後，就會還原 SET 選項的值。 另外，如果您在使用 **sp_executesql** 或 EXECUTE 來執行的動態 SQL 字串中指定 SET 陳述式，從動態 SQL 字串所指定的批次傳回控制權之後，也會還原 SET 選項的值。  
  
- 預存程序是利用執行階段所指定的 SET 設定 (除了 SET ANSI_NULLS 和 SET QUOTED_IDENTIFIER) 來執行的。 指定 SET ANSI_NULLS 或 SET QUOTED_IDENTIFIER 的預存程序會使用建立預存程序時所指定的設定。 如果用於預存程序內，任何 SET 設定都會被忽略。  
  
- **sp_configure** 的 **user options** 設定允許伺服器範圍的設定，並可跨多個資料庫運作。 除了出現在登入階段之外，這項設定的行為也如同明確的 SET 陳述式。  
  
- 利用 ALTER DATABASE 來設定的資料庫設定，只在資料庫層級才有效，且只在明確設定時才有效。 資料庫設定會覆寫使用 **sp_configure** 所設定的執行個體選項設定。  
  
- 如果 SET 陳述式使用 ON 和 OFF，您可以指定多個 SET 選項中的任一個。
  
    > [!NOTE]  
    >  這並不適用於與統計資料相關的 SET 選項。
  
     例如，`SET QUOTED_IDENTIFIER, ANSI_NULLS ON` 會將 QUOTED_IDENTIFIER 和 ANSI_NULLS 都設為 ON。  
  
- SET 陳述式設定會覆寫使用 ALTER DATABASE 所設定的相同資料庫選項設定。 例如，SET ANSI_NULLS 陳述式所指定的值會覆寫 ANSI_NULL 的資料庫設定。 另外，當使用者根據先前使用 **sp_configure user options** 設定而生效之值或所有 ODBC 和 OLE/DB 連線所套用的值，來連線到資料庫時，部分連線設定會自動設成 ON。  
  
- ALTER、CREATE 和 DROP DATABASE 陳述式不接受 SET LOCK_TIMEOUT 設定。  
  
- 當全域或捷徑 SET 陳述式設定多項設定時，發出捷徑 SET 陳述式會重設捷徑 SET 陳述式影響所及的所有選項先前設定。 如果在發出捷徑 SET 陳述式之後，才明確設定捷徑 SET 陳述式影響所及的 SET 選項，則這個個別 SET 陳述式會覆寫可比較的捷徑設定。 SET ANSI_DEFAULTS 是捷徑 SET 陳述式的範例。  
  
- 當使用批次時，資料庫內容取決於利用 USE 陳述式建立的批次。 在預存程序之外執行以及在批次中的非計劃性查詢和所有其他陳述式，都繼承 USE 陳述式所建立之資料庫和連線的選項設定。  
  
- Multiple Active Result Set (MARS) 要求會共用包含最新工作階段 SET 選項設定的全域狀態。 在執行各項要求時，它都能夠修改 SET 選項。 變更專屬於設定它們的要求內容，不會影響其他並行 MARS 要求。 不過，在要求執行完成之後，會將新的 SET 選項複製到全域工作階段狀態中。 在這項變更之後，相同工作階段所執行的新要求會使用這些新的 SET 選項設定。  
  
- 從批次或從另一個預存程序執行預存程序時，都會利用這個預存程序所在資料庫中所設定的選項值來執行。 例如，當 **db1.dbo.sp1** 預存程序呼叫 **db2.dbo.sp2** 預存程序時，會使用 **db1** 資料庫目前的相容性層級設定來執行 **sp1** 預存程序，並使用 **db2** 資料庫目前的相容性層級設定來執行 **sp2** 預存程序。  
  
- 當 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式涉及多個資料庫中的物件時，這個陳述式會套用目前資料庫內容及目前連線內容。 在這個情況下，如果 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式是在批次中，目前的連接內容就是 USE 陳述式所定義的資料庫；如果 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式是在預存程序中，連接內容就是預存程序所在的資料庫。  
  
- 當您建立和操作計算資料行索引或索引檢視表時，必須將下列 SET 選項設成 ON：ARITHABORT、CONCAT_NULL_YIELDS_NULL、QUOTED_IDENTIFIER、ANSI_NULLS、ANSI_PADDING 和 ANSI_WARNINGS。 將 NUMERIC_ROUNDABORT 選項設成 OFF。  
  
  如果您未將任何這些選項設成所需要的值，則在索引檢視表或含計算資料行索引的資料表上執行 INSERT、UPDATE、DELETE、DBCC CHECKDB 和 DBCC CHECKTABLE 動作都會失敗。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會產生一個錯誤，列出所有設定不正確的選項。 另外，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 也會依照計算資料行或檢視不存在索引的方式，來處理這些資料表或索引檢視表的 SELECT 陳述式。 

- 當 SET RESULT_SET_CACHING 為 ON 時，它會為目前的用戶端工作階段啟用結果快取功能。   若在資料庫層級關閉 (OFF) Result_set_caching，便無法為工作階段開啟 (ON) 此功能。    當 SET RESULT_SET_CACHING 為關閉 (OFF) 時，便會為目前的用戶端工作階段停用結果集快取功能。 變更此設定需要 public 角色中的成員資格。
適用於：[!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] Gen2
