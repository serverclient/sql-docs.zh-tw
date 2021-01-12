---
description: ERROR_LINE (Transact-SQL)
title: ERROR_LINE (Transact-SQL)
ms.custom: ''
ms.date: 03/16/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- ERROR_LINE
- ERROR_LINE_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- errors [SQL Server], line number
- messages [SQL Server], line number
- TRY...CATCH [SQL Server]
- line number of error [SQL Server]
- ERROR_LINE function
- CATCH block
ms.assetid: 47335734-0baf-45a6-8b3b-6c4fd80d2cb8
author: cawrites
ms.author: chadam
ms.openlocfilehash: 44402d9f0af858381041e9f5bb5ff989971f44f1
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98097178"
---
# <a name="error_line-transact-sql"></a>ERROR_LINE (Transact-SQL)

[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

此函式會傳回發生錯誤造成執行 TRY...CATCH 建構之 CATCH 區塊的行號。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法

```syntaxsql
ERROR_LINE ( )
```

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="return-type"></a>傳回類型
**int**

## <a name="return-value"></a>傳回值  
在 CATCH 區塊中呼叫時，`ERROR_LINE` 會傳回  
  
-   發生錯誤的行號    
-   常式中的行號 (如果在預存程序或觸發程序內發生錯誤)  
-   NULL (如果在 CATCH 區塊範圍之外呼叫)。  
  
## <a name="remarks"></a>備註  
可以在 CATCH 區塊範圍內的任何位置呼叫 `ERROR_LINE`。  
  
`ERROR_LINE` 會傳回發生錯誤的行號。 不論在 CATCH 區塊範圍內的哪個位置呼叫 `ERROR_LINE`，以及呼叫 `ERROR_LINE` 的次數為何，都是如此。 這有別於 @ERROR 之類的函式。 @ERROR 會在緊接於發生錯誤的陳述式之後的陳述式中，或在 CATCH 區塊的第一個陳述式中，傳回錯誤號碼。  
  
在巢狀 CATCH 區塊中，`ERROR_LINE` 會傳回參考它的 CATCH 區塊範圍特定的錯誤行號。 例如，TRY...CATCH 建構的 CATCH 區塊可能包含巢狀的 TRY...CATCH 建構。 在巢狀 CATCH 區塊內，`ERROR_LINE` 會傳回叫用巢狀 CATCH 區塊之錯誤的行號。 如果 `ERROR_LINE` 是在外部 CATCH 區塊內執行，它會傳回叫用該特定 CATCH 區塊之錯誤的行號。  
  
## <a name="examples"></a>範例  
  
### <a name="a-using-error_line-in-a-catch-block"></a>A. 在 CATCH 區塊中使用 ERROR_LINE  
此程式碼範例會顯示產生除以零之錯誤的 `SELECT` 陳述式。 `ERROR_LINE` 會傳回發生錯誤的行號。  
  
```sql  
BEGIN TRY  
    -- Generate a divide-by-zero error.  
    SELECT 1/0;  
END TRY  
BEGIN CATCH  
    SELECT ERROR_LINE() AS ErrorLine;  
END CATCH;  
GO  
```  
  
### <a name="b-using-error_line-in-a-catch-block-with-a-stored-procedure"></a>B. 在含有預存程序的 CATCH 區塊中使用 ERROR_LINE  
此範例會顯示產生除以零之錯誤的預存程序。 `ERROR_LINE` 會傳回發生錯誤的行號。  
  
```sql  
-- Verify that the stored procedure does not already exist.  
IF OBJECT_ID ( 'usp_ExampleProc', 'P' ) IS NOT NULL   
    DROP PROCEDURE usp_ExampleProc;  
GO  
  
-- Create a stored procedure that  
-- generates a divide-by-zero error.  
CREATE PROCEDURE usp_ExampleProc  
AS  
    SELECT 1/0;  
GO  
  
BEGIN TRY  
    -- Execute the stored procedure inside the TRY block.  
    EXECUTE usp_ExampleProc;  
END TRY  
BEGIN CATCH  
    SELECT ERROR_LINE() AS ErrorLine;  
END CATCH;  
GO  
``` 


### <a name="c-using-error_line-in-a-catch-block-with-other-error-handling-tools"></a>C. 在含有其他錯誤處理工具的 CATCH 區塊中使用 ERROR_LINE  
此程式碼範例會顯示產生除以零之錯誤的 `SELECT` 陳述式。 `ERROR_LINE` 會傳回發生錯誤的行號，以及與錯誤本身相關的資訊。  
  
```sql  
BEGIN TRY  
    -- Generate a divide-by-zero error.  
    SELECT 1/0;  
END TRY  
BEGIN CATCH  
    SELECT  
        ERROR_NUMBER() AS ErrorNumber,  
        ERROR_SEVERITY() AS ErrorSeverity,  
        ERROR_STATE() AS ErrorState,  
        ERROR_PROCEDURE() AS ErrorProcedure,  
        ERROR_LINE() AS ErrorLine,  
        ERROR_MESSAGE() AS ErrorMessage;  
END CATCH;  
GO  
``` 
  
## <a name="see-also"></a>另請參閱  
 [TRY...CATCH &#40;Transact-SQL&#41;](../../t-sql/language-elements/try-catch-transact-sql.md)   
 [sys.messages &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/messages-for-errors-catalog-views-sys-messages.md)   
 [ERROR_NUMBER &#40;Transact-SQL&#41;](../../t-sql/functions/error-number-transact-sql.md)   
 [ERROR_MESSAGE &#40;Transact-SQL&#41;](../../t-sql/functions/error-message-transact-sql.md)   
 [ERROR_PROCEDURE &#40;Transact-SQL&#41;](../../t-sql/functions/error-procedure-transact-sql.md)   
 [ERROR_SEVERITY &#40;Transact-SQL&#41;](../../t-sql/functions/error-severity-transact-sql.md)   
 [ERROR_STATE &#40;Transact-SQL&#41;](../../t-sql/functions/error-state-transact-sql.md)   
 [RAISERROR &#40;Transact-SQL&#41;](../../t-sql/language-elements/raiserror-transact-sql.md)   
 [@@ERROR &#40;Transact-SQL&#41;](../../t-sql/functions/error-transact-sql.md)  
  
  
