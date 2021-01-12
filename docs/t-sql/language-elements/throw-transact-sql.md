---
description: THROW (Transact-SQL)
title: THROW (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/16/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- THROW_TSQL
- THROW
dev_langs:
- TSQL
helpviewer_keywords:
- THROW statement
ms.assetid: 43661b89-8f13-4480-ad53-70306cbb14c5
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: a1ad3ea0844911d5059cc2445c2978c3866f2916
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98100288"
---
# <a name="throw-transact-sql"></a>THROW (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  在 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 中引發例外狀況，並將執行轉移至 TRY...CATCH 建構的 CATCH 區塊。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
THROW [ { error_number | @local_variable },  
        { message | @local_variable },  
        { state | @local_variable } ]   
[ ; ]  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *error_number*  
 這是代表例外狀況的常數或變數。 *error_number* 是 **int**，必須大於或等於 50000，並小於或等於 2147483647。  
  
 *message*  
 即描述例外狀況的字串或變數。 *message* 是 **nvarchar(2048)** 。  
  
 *state*  
 這是介於 0 和 255 之間的常數或變數，表示要與訊息相關聯的狀態。 *state* 是 **tinyint**。  
  
## <a name="remarks"></a>備註  
 THROW 陳述式之前的陳述式後面必須接著分號 (;) 陳述式結束字元。  
  
 如果沒有 TRY...CATCH 建構，就會終止陳述式批次。 系統會設定引發例外狀況的行號和程序。 嚴重性設為 16。  
  
 如果指定不含參數的 THROW 陳述式，它必須出現在 CATCH 區塊內。 這會導致引發攔截到的例外狀況。 THROW 陳述式中發生的任何錯誤都會導致陳述式批次終止。  
  
 % 是 THROW 陳述式訊息文字中的保留字元，而且必須逸出。 重複兩遍 % 字元使 % 返回為訊息文字的一部分，例如 ' 增加超過 15%%的原始值。 '  
  
## <a name="differences-between-raiserror-and-throw"></a>RAISERROR 和 THROW 之間的差異  
 下表列出 RAISERROR 和 THROW 陳述式之間的某些差異。  
  
|RAISERROR 陳述式|THROW 陳述式|  
|-------------------------|---------------------|  
|如果將 *msg_id* 傳遞給 RAISERROR，則識別碼必須定義在 sys.messages 中。|*error_number* 參數不需要定義在 sys.messages 中。|  
|*msg_str* 參數可以包含 **printf** 格式化樣式。|*message* 參數不接受 **printf** 樣式格式。|  
|*severity* 參數指定例外狀況的嚴重性。|沒有任何 *severity* 參數。 當使用 THROW 起始例外狀況時，嚴重性一律設為 16。 不過，使用 THROW 重新擲回現有的例外狀況時，嚴重性會設定為該例外狀況的嚴重性層級。|  
  
## <a name="examples"></a>範例  
  
### <a name="a-using-throw-to-raise-an-exception"></a>A. 使用 THROW 來引發例外狀況  
 下列範例示範如何使用 `THROW` 陳述式引發例外狀況。  
  
```sql  
THROW 51000, 'The record does not exist.', 1;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
 ```
 Msg 51000, Level 16, State 1, Line 1  
  
 The record does not exist.
 ```  
  
### <a name="b-using-throw-to-raise-an-exception-again"></a>B. 使用 THROW 來重新引發例外狀況  
 下列範例示範如何使用 `THROW` 陳述式，重新引發上次擲回的例外狀況。  
  
```sql  
USE tempdb;  
GO  
CREATE TABLE dbo.TestRethrow  
(    ID INT PRIMARY KEY  
);  
BEGIN TRY  
    INSERT dbo.TestRethrow(ID) VALUES(1);  
--  Force error 2627, Violation of PRIMARY KEY constraint to be raised.  
    INSERT dbo.TestRethrow(ID) VALUES(1);  
END TRY  
BEGIN CATCH  
  
    PRINT 'In catch block.';  
    THROW;  
END CATCH;  
  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
 ```
 In catch block. 
 Msg 2627, Level 14, State 1, Line 1  
 Violation of PRIMARY KEY constraint 'PK__TestReth__3214EC272E3BD7D3'. Cannot insert duplicate key in object 'dbo.TestRethrow'.  
 The statement has been terminated.
 ```  
  
### <a name="c-using-formatmessage-with-throw"></a>C. 使用 FORMATMESSAGE 搭配 THROW  
 下列範例示範如何使用 `FORMATMESSAGE` 函數搭配 `THROW` 來擲回自訂的錯誤訊息。 此範例會先使用 `sp_addmessage` 來建立使用者定義的錯誤訊息。 因為 THROW 陳述式不允許在 *message* 參數中使用替代參數 (而 RAISERROR 允許)，所以 FORMATMESSAGE 函式會用來傳遞錯誤訊息 60000 所預期的三個參數值。  
  
```sql  
EXEC sys.sp_addmessage  
     @msgnum   = 60000  
    ,@severity = 16  
    ,@msgtext  = N'This is a test message with one numeric parameter (%d), one string parameter (%s), and another string parameter (%s).'  
    ,@lang = 'us_english';   
GO  
  
DECLARE @msg NVARCHAR(2048) = FORMATMESSAGE(60000, 500, N'First string', N'second string');   
  
THROW 60000, @msg, 1;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
 ```
 Msg 60000, Level 16, State 1, Line 2  
 This is a test message with one numeric parameter (500), one string parameter (First string), and another string parameter (second string).
 ```  
  
## <a name="see-also"></a>另請參閱  
 [FORMATMESSAGE &#40;Transact-SQL&#41;](../../t-sql/functions/formatmessage-transact-sql.md)   
 [資料庫引擎錯誤嚴重性](../../relational-databases/errors-events/database-engine-error-severities.md)   
 [ERROR_LINE &#40;Transact-SQL&#41;](../../t-sql/functions/error-line-transact-sql.md)   
 [ERROR_MESSAGE &#40;Transact-SQL&#41;](../../t-sql/functions/error-message-transact-sql.md)   
 [ERROR_NUMBER &#40;Transact-SQL&#41;](../../t-sql/functions/error-number-transact-sql.md)   
 [ERROR_PROCEDURE &#40;Transact-SQL&#41;](../../t-sql/functions/error-procedure-transact-sql.md)   
 [ERROR_SEVERITY &#40;Transact-SQL&#41;](../../t-sql/functions/error-severity-transact-sql.md)   
 [ERROR_STATE &#40;Transact-SQL&#41;](../../t-sql/functions/error-state-transact-sql.md)   
 [RAISERROR &#40;Transact-SQL&#41;](../../t-sql/language-elements/raiserror-transact-sql.md)   
 [@@ERROR &#40;Transact-SQL&#41;](../../t-sql/functions/error-transact-sql.md)   
 [GOTO &#40;Transact-SQL&#41;](../../t-sql/language-elements/goto-transact-sql.md)   
 [BEGIN...END &#40;Transact-SQL&#41;](../../t-sql/language-elements/begin-end-transact-sql.md)   
 [XACT_STATE &#40;Transact-SQL&#41;](../../t-sql/functions/xact-state-transact-sql.md)   
 [SET XACT_ABORT &#40;Transact-SQL&#41;](../../t-sql/statements/set-xact-abort-transact-sql.md)  
  
  

