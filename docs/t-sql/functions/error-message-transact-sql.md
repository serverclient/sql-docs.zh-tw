---
description: ERROR_MESSAGE (Transact-SQL)
title: ERROR_MESSAGE (Transact-SQL)
ms.custom: ''
ms.date: 03/16/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- ERROR_MESSAGE_TSQL
- ERROR_MESSAGE
dev_langs:
- TSQL
helpviewer_keywords:
- ERROR_MESSAGE function
- errors [SQL Server], text of
- messages [SQL Server], text of
- TRY...CATCH [SQL Server]
- CATCH block
ms.assetid: f32877a6-5f17-418c-a32c-5a1a344b3c45
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: d7b4d0a29c4168339b236834b395c451e1604dc4
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98093630"
---
# <a name="error_message-transact-sql"></a>ERROR_MESSAGE (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

此函式會傳回造成執行 TRY...CATCH 建構的 CATCH 區塊之錯誤的訊息文字。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql  
ERROR_MESSAGE ( )   
```  

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="return-types"></a>傳回型別
 **nvarchar(4000)**  
  
## <a name="return-value"></a>傳回值  
在 CATCH 區塊中呼叫時，`ERROR_MESSAGE` 會傳回造成執行 `CATCH` 區塊之錯誤訊息的完整文字。 文字包括提供給任何可替代參數的值；例如，長度、物件名稱或次數。  
  
在 CATCH 區塊範圍之外呼叫時，`ERROR_MESSAGE` 會傳回 NULL。  
  
## <a name="remarks"></a>備註  
`ERROR_MESSAGE` 支援在 CATCH 區塊範圍內的任何位置呼叫。  
  
不論執行多少次，或在 `CATCH` 區塊範圍內的哪個位置執行，`ERROR_MESSAGE` 都會傳回相關的錯誤訊息。 這有別於 @@ERROR 之類的函式，它們只會在緊接於發生錯誤的陳述式之後的陳述式中，傳回錯誤號碼。  
  
在巢狀 `CATCH` 區塊中，`ERROR_MESSAGE` 會參考該 `CATCH` 區塊之 `CATCH` 區塊範圍特定的錯誤訊息。 例如，外部 TRY...CATCH 建構的 `CATCH` 區塊可能會有內部 `TRY...CATCH` 建構。 在該內部 `CATCH` 區塊內，`ERROR_MESSAGE` 會傳回叫用內部 `CATCH` 區塊之錯誤的訊息。 如果 `ERROR_MESSAGE` 是在外部 `CATCH` 區塊中執行，它會傳回叫用該外部 `CATCH` 區塊之錯誤的訊息。  
  
## <a name="examples"></a>範例  
  
### <a name="a-using-error_message-in-a-catch-block"></a>A. 在 CATCH 區塊中使用 ERROR_MESSAGE  
此範例會顯示產生除以零之錯誤的 `SELECT` 陳述式。 `CATCH` 區塊會傳回錯誤訊息。  
  
```sql   
BEGIN TRY  
    -- Generate a divide-by-zero error.  
    SELECT 1/0;  
END TRY  
BEGIN CATCH  
    SELECT ERROR_MESSAGE() AS ErrorMessage;  
END CATCH;  
GO  
```
[!INCLUDE[ssResult](../../includes/ssresult-md.md)] 
```
-----------

(0 row(s) affected)

ErrorMessage
----------------------------------
Divide by zero error encountered.

(1 row(s) affected)

```  
  
### <a name="b-using-error_message-in-a-catch-block-with-other-error-handling-tools"></a>B. 在含有其他錯誤處理工具的 CATCH 區塊中使用 ERROR_MESSAGE  
此範例會顯示產生除以零之錯誤的 `SELECT` 陳述式。 除了錯誤訊息，`CATCH` 區塊也會傳回該錯誤的相關資訊。  
  
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
[!INCLUDE[ssResult](../../includes/ssresult-md.md)] 
```
-----------

(0 row(s) affected)

ErrorNumber ErrorSeverity ErrorState  ErrorProcedure  ErrorLine  ErrorMessage
----------- ------------- ----------- --------------- ---------- ----------------------------------
8134        16            1           NULL            4          Divide by zero error encountered.

(1 row(s) affected)

```
  
## <a name="see-also"></a>另請參閱  
 [sys.messages &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/messages-for-errors-catalog-views-sys-messages.md)   
 [TRY...CATCH &#40;Transact-SQL&#41;](../../t-sql/language-elements/try-catch-transact-sql.md)   
 [ERROR_LINE &#40;Transact-SQL&#41;](../../t-sql/functions/error-line-transact-sql.md)   
 [ERROR_MESSAGE &#40;Transact-SQL&#41;](../../t-sql/functions/error-message-transact-sql.md)   
 [ERROR_PROCEDURE &#40;Transact-SQL&#41;](../../t-sql/functions/error-procedure-transact-sql.md)   
 [ERROR_SEVERITY &#40;Transact-SQL&#41;](../../t-sql/functions/error-severity-transact-sql.md)   
 [ERROR_STATE &#40;Transact-SQL&#41;](../../t-sql/functions/error-state-transact-sql.md)   
 [RAISERROR &#40;Transact-SQL&#41;](../../t-sql/language-elements/raiserror-transact-sql.md)   
 [@@ERROR &#40;Transact-SQL&#41;](../../t-sql/functions/error-transact-sql.md)     
 [錯誤和事件參考 &#40;資料庫引擎&#41;](../../relational-databases/errors-events/errors-and-events-reference-database-engine.md)     
  
    

