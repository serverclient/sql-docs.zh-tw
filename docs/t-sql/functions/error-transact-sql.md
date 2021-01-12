---
description: '&#x40;&#x40;ERROR (Transact-SQL)'
title: '@@ERROR (Transact-SQL) | Microsoft Docs'
ms.custom: ''
ms.date: 08/29/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- '@@ERROR'
- '@@ERROR_TSQL'
dev_langs:
- TSQL
helpviewer_keywords:
- '@@ERROR function'
- errors [SQL Server], Transact-SQL
- error numbers [SQL Server]
ms.assetid: c8b43477-b6c0-49bf-a608-394a0b6cc7a2
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: f05c9fe20766bec9f0308a608b340b400718505b
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98093608"
---
# <a name="x40x40error-transact-sql"></a>&#x40;&#x40;ERROR (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  傳回最後執行之 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式的錯誤號碼。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```  
@@ERROR  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="return-types"></a>傳回型別
 integer  
  
## <a name="remarks"></a>備註  
 如果先前的 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式沒有發現任何錯誤，便傳回 0。  
  
 如果先前的陳述式發現錯誤，便傳回錯誤號碼。 如果錯誤是 sys.messages 目錄檢視中的錯誤之一，@@ERROR 會包含這項錯誤在 sys.messages.message_id 資料行中的值。 您可以在 sys.messages 中檢視 @@ERROR 錯誤號碼的相關聯文字。  
  
 由於執行每個陳述式時，都會清除再重設 @@ERROR，因此，請在陳述式之後，立即檢查它，或將它儲存在稍後能夠檢查的本機變數中。  
  
 使用 TRY...CATCH 建構來處理錯誤。 TRY...CATCH 建構也支援其他會傳回 @@ERROR 以外之錯誤資訊的系統函數 (ERROR_LINE、ERROR_MESSAGE、ERROR_PROCEDURE、ERROR_SEVERITY 和 ERROR_STATE)。 TRY...CATCH 也支援 ERROR_NUMBER 函數，ERROR_NUMBER 函數並不限於在緊接於產生錯誤的陳述式之後的陳述式中傳回錯誤號碼。 如需詳細資訊，請參閱 [TRY...CATCH &#40;Transact-SQL&#41;](../../t-sql/language-elements/try-catch-transact-sql.md)。  
  
## <a name="examples"></a>範例  
  
### <a name="a-using-error-to-detect-a-specific-error"></a>A. 利用 @@ERROR 來偵測特定錯誤  
 下列範例利用 `@@ERROR` 來檢查 `UPDATE` 陳述式的 CHECK 條件約束違規 (錯誤號碼 547)。  
  
```sql  
USE AdventureWorks2012;  
GO  
UPDATE HumanResources.EmployeePayHistory  
    SET PayFrequency = 4  
    WHERE BusinessEntityID = 1;  
IF @@ERROR = 547
    BEGIN
    PRINT N'A check constraint violation occurred.';
    END
GO  
```  
  
### <a name="b-using-error-to-conditionally-exit-a-procedure"></a>B. 利用 @@ERROR 有條件地結束程序  
 下列範例會在預存程序中的 `DELETE` 陳述式之後，使用 `IF...ELSE` 陳述式來測試 `@@ERROR`。 `@@ERROR` 變數的值決定了傳給呼叫端程式來指出程序成功或失敗的傳回碼。  
  
```sql  
USE AdventureWorks2012;  
GO  
-- Drop the procedure if it already exists.  
IF OBJECT_ID(N'HumanResources.usp_DeleteCandidate', N'P') IS NOT NULL  
    DROP PROCEDURE HumanResources.usp_DeleteCandidate;  
GO  
-- Create the procedure.  
CREATE PROCEDURE HumanResources.usp_DeleteCandidate   
    (  
    @CandidateID INT  
    )  
AS  
-- Execute the DELETE statement.  
DELETE FROM HumanResources.JobCandidate  
    WHERE JobCandidateID = @CandidateID;  
-- Test the error value.  
IF @@ERROR <> 0   
    BEGIN  
        -- Return 99 to the calling program to indicate failure.  
        PRINT N'An error occurred deleting the candidate information.';  
        RETURN 99;  
    END  
ELSE  
    BEGIN  
        -- Return 0 to the calling program to indicate success.  
        PRINT N'The job candidate has been deleted.';  
        RETURN 0;  
    END;  
GO  
```  
  
### <a name="c-using-error-with-rowcount"></a>C. 搭配 @@ROWCOUNT使用 @@ERROR  
 下列範例搭配 `@@ERROR` 使用 `@@ROWCOUNT` 來驗證 `UPDATE` 陳述式的作業。 `@@ERROR` 的值用來針對任何錯誤指示來進行檢查，而 `@@ROWCOUNT` 則用來確保更新已成功套用至資料表中的資料列。  
  
```sql  
USE AdventureWorks2012;  
GO  
IF OBJECT_ID(N'Purchasing.usp_ChangePurchaseOrderHeader',N'P')IS NOT NULL  
    DROP PROCEDURE Purchasing.usp_ChangePurchaseOrderHeader;  
GO  
CREATE PROCEDURE Purchasing.usp_ChangePurchaseOrderHeader  
    (  
    @PurchaseOrderID INT  
    ,@BusinessEntityID INT  
   )  
AS  
-- Declare variables used in error checking.  
DECLARE @ErrorVar INT;  
DECLARE @RowCountVar INT;  
  
-- Execute the UPDATE statement.  
UPDATE PurchaseOrderHeader   
    SET BusinessEntityID = @BusinessEntityID   
    WHERE PurchaseOrderID = @PurchaseOrderID;  
  
-- Save the @@ERROR and @@ROWCOUNT values in local   
-- variables before they are cleared.  
SELECT @ErrorVar = @@ERROR  
    ,@RowCountVar = @@ROWCOUNT;  
  
-- Check for errors. If an invalid @BusinessEntityID was specified,  
-- the UPDATE statement returns a foreign key violation error #547.  
IF @ErrorVar <> 0  
    BEGIN  
        IF @ErrorVar = 547  
            BEGIN  
                PRINT N'ERROR: Invalid ID specified for new employee.';  
                 RETURN 1;  
            END  
        ELSE  
            BEGIN  
                PRINT N'ERROR: error '  
                    + RTRIM(CAST(@ErrorVar AS NVARCHAR(10)))  
                    + N' occurred.';  
                RETURN 2;  
            END  
    END  
  
-- Check the row count. @RowCountVar is set to 0   
-- if an invalid @PurchaseOrderID was specified.  
IF @RowCountVar = 0  
    BEGIN  
        PRINT 'Warning: The BusinessEntityID specified is not valid';  
        RETURN 1;  
    END  
ELSE  
    BEGIN  
        PRINT 'Purchase order updated with the new employee';  
        RETURN 0;  
    END;  
GO  
```  

  
## <a name="see-also"></a>另請參閱  
 [TRY...CATCH &#40;Transact-SQL&#41;](../../t-sql/language-elements/try-catch-transact-sql.md)   
 [ERROR_LINE &#40;Transact-SQL&#41;](../../t-sql/functions/error-line-transact-sql.md)   
 [ERROR_MESSAGE &#40;Transact-SQL&#41;](../../t-sql/functions/error-message-transact-sql.md)   
 [ERROR_NUMBER &#40;Transact-SQL&#41;](../../t-sql/functions/error-number-transact-sql.md)   
 [ERROR_PROCEDURE &#40;Transact-SQL&#41;](../../t-sql/functions/error-procedure-transact-sql.md)   
 [ERROR_SEVERITY &#40;Transact-SQL&#41;](../../t-sql/functions/error-severity-transact-sql.md)   
 [ERROR_STATE &#40;Transact-SQL&#41;](../../t-sql/functions/error-state-transact-sql.md)   
 [@@ROWCOUNT &#40;Transact-SQL&#41;](../../t-sql/functions/rowcount-transact-sql.md)   
 [sys.messages &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/messages-for-errors-catalog-views-sys-messages.md)     
 [錯誤和事件參考 &#40;資料庫引擎&#41;](../../relational-databases/errors-events/errors-and-events-reference-database-engine.md)     
  

