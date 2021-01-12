---
description: TRY...CATCH (Transact-SQL)
title: TRY...CATCH (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/16/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- BEGIN_TRY_TSQL
- BEGIN_CATCH_TSQL
- TRY
- BEGIN TRY
- TRY_TSQL
- BEGIN CATCH
dev_langs:
- TSQL
helpviewer_keywords:
- BEGIN CATCH statement
- uncommittable transactions
- errors [SQL Server], TRY...CATCH
- TRY block [SQL Server]
- XACT_STATE function
- TRY...CATCH [SQL Server]
- BEGIN TRY statement
- CATCH block
ms.assetid: 248df62a-7334-4bca-8262-235a28f4b07f
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 6d31f8951dbf878a6ae7b6895fb060af91ad1c27
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98095962"
---
# <a name="trycatch-transact-sql"></a>TRY...CATCH (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]


  實作類似於 [!INCLUDE[tsql](../../includes/tsql-md.md)] Visual C# 與 [!INCLUDE[msCoName](../../includes/msconame-md.md)] Visual C++ 語言中之例外狀況處理的 [!INCLUDE[msCoName](../../includes/msconame-md.md)] 錯誤處理。 您可以將 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式群組含括在 TRY 區塊內。 如果 TRY 區塊內發生錯誤，就會將控制權傳給含括在 CATCH 區塊內的另一個陳述式群組。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
BEGIN TRY  
     { sql_statement | statement_block }  
END TRY  
BEGIN CATCH  
     [ { sql_statement | statement_block } ]  
END CATCH  
[ ; ]  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *sql_statement*  
 這是任何 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式。  
  
 *statement_block*  
 在批次或 BEGIN...END 區塊中的任何 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式群組。  
  
## <a name="remarks"></a>備註  
 TRY...CATCH 建構會擷取嚴重性高於 10 而未關閉資料庫連線的所有執行錯誤。  
  
 TRY 區塊後面必須緊接著相關聯的 CATCH 區塊。 在 END TRY 與 BEGIN CATCH 陳述式之間包含任何其他陳述式，將會產生語法錯誤。  
  
 TRY...CATCH 建構不能跨越多個批次。 TRY...CATCH 建構不能跨越多個 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式區塊。 例如，TRY...CATCH 建構不能跨越 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式的兩個 BEGIN...END 區塊，也不能跨越 IF...ELSE 建構。  
  
 如果 TRY 區塊所含括的程式碼沒有錯誤，當 TRY 區塊中的最後一個陳述式完成執行時，控制權會傳給緊接在相關聯的 END CATCH 陳述式之後的陳述式。
 
 如果 TRY 區塊所含括的程式碼發生錯誤，控制權會傳給相關聯的 CATCH 區塊中的第一個陳述式。 當 CATCH 區塊中的程式碼完成時，控制權會傳給緊接在 END CATCH 陳述式之後的陳述式。 
 
 > [!NOTE] 
 > 如果 END CATCH 陳述式是預存程序或觸發程序中的最後一個陳述式，控制權便會傳回呼叫預存程序或引發觸發程序的陳述式。 
 
 CATCH 區塊擷取的錯誤不會傳回發出呼叫的應用程式。 如果有任何錯誤資訊必須傳回應用程式，CATCH 區塊中的程式碼便必須利用 SELECT 結果集或 RAISERROR 和 PRINT 陳述式之類的機制來執行這個動作。  
  
 TRY...CATCH 建構可以有巢狀結構。 TRY 區塊或 CATCH 區塊可以包含巢狀的 TRY...CATCH 建構。 例如，CATCH 區塊可以包含內嵌的 TRY...CATCH 建構，以便處理 CATCH 程式碼所發現的錯誤。  
  
 CATCH 區塊所發現的錯誤，會依照其他位置產生之錯誤的相同方式來處理。 如果 CATCH 區塊包含巢狀的 TRY...CATCH 建構，巢狀 TRY 區塊中的任何錯誤都會將控制權傳給巢狀的 CATCH 區塊。 如果沒有巢狀的 TRY...CATCH 建構，便會將錯誤傳回給呼叫者。  
  
 TRY...CATCH 建構會從 TRY 區塊中的程式碼所執行的預存程序或觸發程序中，擷取尚未處理的錯誤。 另外，預存程序或觸發程序也可以包含它們自己的 TRY...CATCH 建構來處理它們的程式碼所產生的錯誤。 例如，當 TRY 區塊執行預存程序且在預存程序中發生錯誤時，便可以依照下列方式來處理錯誤：  
  
-   如果預存程序未包含它自己的 TRY...CATCH 建構，錯誤會將控制權傳回給與包含 EXECUTE 陳述式之 TRY 區塊相關聯的 CATCH 區塊。  
  
-   如果預存程序包含 TRY...CATCH 建構，錯誤會將控制權傳送給預存程序中的 CATCH 區塊。 當 CATCH 區塊程式碼完成時，控制權會傳回給緊接在呼叫預存程序的 EXECUTE 陳述式之後的陳述式。  
  
 GOTO 陳述式無法用來進入 TRY 或 CATCH 區塊。 GOTO 陳述式可用來跳到相同 TRY 或 CATCH 區塊內的標籤，或離開 TRY 或 CATCH 區塊。  
  
 在使用者定義函數內，無法使用 TRY...CATCH 建構。  
  
## <a name="retrieving-error-information"></a>擷取錯誤資訊  
 在 CATCH 區塊的範圍內，下列系統函數可用來取得造成執行 CATCH 區塊之錯誤的相關資訊：  
  
-   [ERROR_NUMBER()](../../t-sql/functions/error-number-transact-sql.md) 會傳回錯誤碼。  
  
-   [ERROR_SEVERITY()](../../t-sql/functions/error-severity-transact-sql.md) 會傳回嚴重性。  
  
-   [ERROR_STATE()](../../t-sql/functions/error-state-transact-sql.md) 會傳回錯誤狀態碼。  
  
-   [ERROR_PROCEDURE()](../../t-sql/functions/error-procedure-transact-sql.md) 會傳回發生錯誤的預存程序或觸發程序的名稱。  
  
-   [ERROR_LINE()](../../t-sql/functions/error-line-transact-sql.md) 會傳回常式內造成錯誤的行號。  
  
-   [ERROR_MESSAGE()](../../t-sql/functions/error-message-transact-sql.md) 會傳回錯誤訊息的完整文字。 文字包括提供給任何可替代參數的值，例如，長度、物件名稱或次數。  
  
如果是在 CATCH 區塊範圍之外呼叫這些函數，它們會傳回 NULL。 這些函數可以從 CATCH 區塊範圍內的任何位置擷取錯誤資訊。 例如，下列指令碼顯示包含錯誤處理函數的預存程序。 在 `CATCH`建構的 `TRY...CATCH`區塊中，會呼叫預存程序，並傳回錯誤的相關資訊。  
  
```sql  
-- Verify that the stored procedure does not already exist.  
IF OBJECT_ID ( 'usp_GetErrorInfo', 'P' ) IS NOT NULL   
    DROP PROCEDURE usp_GetErrorInfo;  
GO  
  
-- Create procedure to retrieve error information.  
CREATE PROCEDURE usp_GetErrorInfo  
AS  
SELECT  
    ERROR_NUMBER() AS ErrorNumber  
    ,ERROR_SEVERITY() AS ErrorSeverity  
    ,ERROR_STATE() AS ErrorState  
    ,ERROR_PROCEDURE() AS ErrorProcedure  
    ,ERROR_LINE() AS ErrorLine  
    ,ERROR_MESSAGE() AS ErrorMessage;  
GO  
  
BEGIN TRY  
    -- Generate divide-by-zero error.  
    SELECT 1/0;  
END TRY  
BEGIN CATCH  
    -- Execute error retrieval routine.  
    EXECUTE usp_GetErrorInfo;  
END CATCH;   
```  
  
 ERROR\_\* 函數也可在[原生編譯之預存程序](../../relational-databases/in-memory-oltp/a-guide-to-query-processing-for-memory-optimized-tables.md)的 `CATCH` 區塊中運作。  
  
## <a name="errors-unaffected-by-a-trycatch-construct"></a>不受 TRY...CATCH 建構影響的錯誤  
 TRY...CATCH 建構不會擷取下列狀況：  
  
-   嚴重性為 10 或以下的警告或參考訊息。  
  
-   嚴重性為 20 或以上的錯誤，它們會停止工作階段的 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 工作處理。 如果發生嚴重性為 20 或以上的錯誤，且資料庫連線並未中斷，TRY...CATCH 會處理這個錯誤。  
  
-   用戶端中斷要求或中斷用戶端連接之類的注意事項。  
  
-   系統管理員利用 KILL 陳述式來結束工作階段。  
  
當 TRY...CATCH 建構的相同執行層級發生下列錯誤類型時，CATCH 區塊不會處理這些錯誤：  
  
-   造成無法執行批次的編譯錯誤，如語法錯誤。  
  
-   在陳述式層級重新編譯期間發生的錯誤，例如在編譯之後，因延遲的名稱解析所發生的物件名稱解析錯誤。  
-   物件名稱解析錯誤   

  
這些錯誤會傳回執行批次、預存程序或觸發程序的層級。  
  
如果在 TRY 區塊內，在編譯或陳述式層級重新編譯期間，較低的執行層級發生錯誤 (例如，執行 sp_executesql 或使用者定義預存程序時)，發生錯誤的層級會低於 TRY...CATCH 建構，並由相關聯的 CATCH 區塊來處理。  
  
下列範例顯示 `SELECT` 陳述式所產生的物件名稱解析錯誤，是在預存程序內執行相同的 `TRY...CATCH` 陳述式時，由 `CATCH` 區塊來擷取，而不是由 `SELECT` 建構來擷取。  
  
```sql  
BEGIN TRY  
    -- Table does not exist; object name resolution  
    -- error not caught.  
    SELECT * FROM NonexistentTable;  
END TRY  
BEGIN CATCH  
    SELECT   
        ERROR_NUMBER() AS ErrorNumber  
       ,ERROR_MESSAGE() AS ErrorMessage;  
END CATCH  
```  
  
 此時不會擷取錯誤，控制權會將 `TRY...CATCH` 建構交給下一個較高的層級。  
  
 在預存程序內執行 `SELECT` 陳述式，會使錯誤發生在低於 `TRY` 區塊的層級。 這個錯誤由 `TRY...CATCH` 建構來處理。  
  
```sql  
-- Verify that the stored procedure does not exist.  
IF OBJECT_ID ( N'usp_ExampleProc', N'P' ) IS NOT NULL   
    DROP PROCEDURE usp_ExampleProc;  
GO  
  
-- Create a stored procedure that will cause an   
-- object resolution error.  
CREATE PROCEDURE usp_ExampleProc  
AS  
    SELECT * FROM NonexistentTable;  
GO  
  
BEGIN TRY  
    EXECUTE usp_ExampleProc;  
END TRY  
BEGIN CATCH  
    SELECT   
        ERROR_NUMBER() AS ErrorNumber  
        ,ERROR_MESSAGE() AS ErrorMessage;  
END CATCH;  
```  
  
## <a name="uncommittable-transactions-and-xact_state"></a>無法認可的交易和 XACT_STATE  
 如果在 TRY 區塊內產生的錯誤使目前交易的狀態失效，交易便會分類為無法認可的交易。 通常會在 TRY 區塊之外結束交易的錯誤，當它發生在 TRY 區塊內時，會使交易進入無法認可的狀態。 無法認可的交易只能執行讀取作業或 ROLLBACK TRANSACTION。 這個交易無法執行會產生寫入作業或 COMMIT TRANSACTION 的任何 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式。 如果交易分類為無法認可的交易，XACT_STATE 函數會傳回 -1 值。 當批次完成時，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 會回復任何使用中無法認可的交易。 如果在交易進入無法認可的狀態時沒有傳送任何錯誤訊息，則當批次完成時，就會將錯誤訊息傳送給用戶端應用程式。 這表示偵測到無法認可的交易，並且需要回復它。  
  
 如需有關無法認可的交易和 XACT_STATE 函數的詳細資訊，請參閱 [XACT_STATE &#40;Transact-SQL&#41;](../../t-sql/functions/xact-state-transact-sql.md)。  
  
## <a name="examples"></a>範例  
  
### <a name="a-using-trycatch"></a>A. 使用 TRY...CATCH  
 下列範例顯示將會產生除以零的錯誤之 `SELECT` 陳述式。 這個錯誤會使執行動作跳到相關聯的 `CATCH` 區塊。  
  
```sql  
BEGIN TRY  
    -- Generate a divide-by-zero error.  
    SELECT 1/0;  
END TRY  
BEGIN CATCH  
    SELECT  
        ERROR_NUMBER() AS ErrorNumber  
        ,ERROR_SEVERITY() AS ErrorSeverity  
        ,ERROR_STATE() AS ErrorState  
        ,ERROR_PROCEDURE() AS ErrorProcedure  
        ,ERROR_LINE() AS ErrorLine  
        ,ERROR_MESSAGE() AS ErrorMessage;  
END CATCH;  
GO  
```  
  
### <a name="b-using-trycatch-in-a-transaction"></a>B. 在交易中使用 TRY...CATCH  
 下列範例顯示 `TRY...CATCH` 區塊在交易內運作的方式。 `TRY` 區塊內的陳述式產生條件約束違規錯誤。  
  
```sql  
BEGIN TRANSACTION;  
  
BEGIN TRY  
    -- Generate a constraint violation error.  
    DELETE FROM Production.Product  
    WHERE ProductID = 980;  
END TRY  
BEGIN CATCH  
    SELECT   
        ERROR_NUMBER() AS ErrorNumber  
        ,ERROR_SEVERITY() AS ErrorSeverity  
        ,ERROR_STATE() AS ErrorState  
        ,ERROR_PROCEDURE() AS ErrorProcedure  
        ,ERROR_LINE() AS ErrorLine  
        ,ERROR_MESSAGE() AS ErrorMessage;  
  
    IF @@TRANCOUNT > 0  
        ROLLBACK TRANSACTION;  
END CATCH;  
  
IF @@TRANCOUNT > 0  
    COMMIT TRANSACTION;  
GO  
```  
  
### <a name="c-using-trycatch-with-xact_state"></a>C. 使用 TRY...CATCH 搭配 XACT_STATE  
 下列範例顯示如何利用 `TRY...CATCH` 建構來處理交易內所發生的錯誤。 `XACT_STATE` 函數會判斷是否應該認可或回復交易。 在此範例中，`SET XACT_ABORT` 是 `ON`。 當發生條件約束違規錯誤時，會使交易成為無法認可。  
  
```sql  
-- Check to see whether this stored procedure exists.  
IF OBJECT_ID (N'usp_GetErrorInfo', N'P') IS NOT NULL  
    DROP PROCEDURE usp_GetErrorInfo;  
GO  
  
-- Create procedure to retrieve error information.  
CREATE PROCEDURE usp_GetErrorInfo  
AS  
    SELECT   
         ERROR_NUMBER() AS ErrorNumber  
        ,ERROR_SEVERITY() AS ErrorSeverity  
        ,ERROR_STATE() AS ErrorState  
        ,ERROR_LINE () AS ErrorLine  
        ,ERROR_PROCEDURE() AS ErrorProcedure  
        ,ERROR_MESSAGE() AS ErrorMessage;  
GO  
  
-- SET XACT_ABORT ON will cause the transaction to be uncommittable  
-- when the constraint violation occurs.   
SET XACT_ABORT ON;  
  
BEGIN TRY  
    BEGIN TRANSACTION;  
        -- A FOREIGN KEY constraint exists on this table. This   
        -- statement will generate a constraint violation error.  
        DELETE FROM Production.Product  
            WHERE ProductID = 980;  
  
    -- If the DELETE statement succeeds, commit the transaction.  
    COMMIT TRANSACTION;  
END TRY  
BEGIN CATCH  
    -- Execute error retrieval routine.  
    EXECUTE usp_GetErrorInfo;  
  
    -- Test XACT_STATE:  
        -- If 1, the transaction is committable.  
        -- If -1, the transaction is uncommittable and should   
        --     be rolled back.  
        -- XACT_STATE = 0 means that there is no transaction and  
        --     a commit or rollback operation would generate an error.  
  
    -- Test whether the transaction is uncommittable.  
    IF (XACT_STATE()) = -1  
    BEGIN  
        PRINT  
            N'The transaction is in an uncommittable state.' +  
            'Rolling back transaction.'  
        ROLLBACK TRANSACTION;  
    END;  
  
    -- Test whether the transaction is committable.
    -- You may want to commit a transaction in a catch block if you want to commit changes to statements that ran prior to the error.
    IF (XACT_STATE()) = 1  
    BEGIN  
        PRINT  
            N'The transaction is committable.' +  
            'Committing transaction.'  
        COMMIT TRANSACTION;     
    END;  
END CATCH;  
GO  
```  
  
## <a name="examples-sssdwfull-and-sspdw"></a>範例：[!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 和 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
### <a name="d-using-trycatch"></a>D. 使用 TRY...CATCH  
 下列範例顯示將會產生除以零的錯誤之 `SELECT` 陳述式。 這個錯誤會使執行動作跳到相關聯的 `CATCH` 區塊。  
  
```sql  
BEGIN TRY  
    -- Generate a divide-by-zero error.  
    SELECT 1/0;  
END TRY  
BEGIN CATCH  
    SELECT  
        ERROR_NUMBER() AS ErrorNumber  
        ,ERROR_SEVERITY() AS ErrorSeverity  
        ,ERROR_STATE() AS ErrorState  
        ,ERROR_PROCEDURE() AS ErrorProcedure  
        ,ERROR_MESSAGE() AS ErrorMessage;  
END CATCH;  
GO  
```  
  
## <a name="see-also"></a>另請參閱  
 [THROW &#40;Transact-SQL&#41;](../../t-sql/language-elements/throw-transact-sql.md)   
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
  
