---
description: RAISERROR (Transact-SQL)
title: RAISERROR (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 02/21/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- RAISERROR
- RAISERROR_TSQL
- RAISEERROR_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sysmessages system table
- errors [SQL Server], RAISERROR statement
- user-defined error messages [SQL Server]
- system flags
- generating errors [SQL Server]
- TRY block [SQL Server]
- recording errors
- ad hoc messages
- RAISERROR statement
- CATCH block
- messages [SQL Server], RAISERROR statement
ms.assetid: 483588bd-021b-4eae-b4ee-216268003e79
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 6025177359ba8da3d2d330c88ec08a162f12b2d2
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98102444"
---
# <a name="raiserror-transact-sql"></a>RAISERROR (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

> [!NOTE]
> **RAISERROR** 陳述式不受 **SET XACT_ABORT** 影響。 新的應用程式應該使用 **THROW**，而非 **RAISERROR**。

  產生錯誤訊息並起始工作階段的錯誤處理。 RAISERROR 可以參考儲存在 sys.messages 目錄檢視表的使用者自訂訊息，或是動態建立訊息。 訊息以伺服器錯誤訊息傳回給呼叫應用程式，或傳回給 TRY...CATCH 建構的相關聯 CATCH 區塊。 新應用程式應該改用 [THROW](../../t-sql/language-elements/throw-transact-sql.md)。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
-- Syntax for SQL Server and Azure SQL Database  
  
RAISERROR ( { msg_id | msg_str | @local_variable }  
    { ,severity ,state }  
    [ ,argument [ ,...n ] ] )  
    [ WITH option [ ,...n ] ]  
```  
  
```syntaxsql
-- Syntax for Azure Synapse Analytics and Parallel Data Warehouse  
  
RAISERROR ( { msg_str | @local_variable }  
    { ,severity ,state }  
    [ ,argument [ ,...n ] ] )  
    [ WITH option [ ,...n ] ]  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *msg_id*  
 這是使用 sp_addmessage 儲存在 sys.messages 目錄檢視表的使用者自訂錯誤訊息編號。 使用者自訂錯誤訊息的錯誤號碼應該大於 50000。 如果未指定 *msg_id*，RAISERROR 會引發錯誤號碼為 50000 的錯誤訊息。  
  
 *msg_str*  
 這是使用者自訂訊息，格式類似於 C 標準程式庫中的 **printf** 函數。 這個錯誤訊息最多可有 2,047 個字元。 如果訊息包含 2,048 個或更多字元，則只會顯示前 2,044 個字元，並且會加上省略符號以表示該訊息已被截斷。 請注意，由於內部儲存行為的緣故，替代參數比輸出顯示耗用更多字元。 例如，指派值為 2 的 *%d* 替代參數，實際上會在訊息字串中產生一個字元，但在內部卻也佔用了三個額外儲存字元。 這項儲存需求減少了訊息輸出的可用字元數。  
  
 當指定 *msg_str* 時，RAISERROR 會引發錯誤號碼為 50000 的錯誤訊息。  
  
 *msg_str* 是具有選擇性內嵌轉換規格的字元字串。 每一轉換規格定義了引數清單中的值如何格式化，以及如何置入位於 *msg_str* 中轉換規格的欄位。 轉換規格具有這個格式：  
  
 % [[*旗標*] [*寬度*] [. *有效位數*] [{h | l}]] *類型*  
  
 可用於 *msg_str* 中的參數有：  
  
 *旗標*  
  
 這是決定替代值之間距與對齊的程式碼。  
  
|程式碼|前置詞或對齊|描述|  
|----------|-----------------------------|-----------------|  
|- (減號)|靠左對齊|給定欄位寬度內的引數值靠左對齊。|  
|+ (加號)|符號前置詞|如果值為帶正負號的類型，則在引數值前加上正號 (+) 或負號 (-)。|  
|0 (零)|零填補|在輸出前加上 0，直到到達最小寬度為止。 當 0 與減號 (-) 出現時，0 會被忽略。|  
|# (數字)|x 或 X 之十六進位類型的 0x 前置詞|使用 o、x 或 X 格式時，數字符號 (#) 旗標會分別在非零值前面加上 0、0x 或 0X。 當在 d、i 或 u 前面加上數字符號 (#) 旗標時，該旗標會被忽略。|  
|' ' (空白)|空間填補|如果輸出值帶正負號且為正值時，會在輸出值前加上空格。 如果包含了正號 (+) 旗標時，這個空格會被忽略。|  
  
 *寬度*  
  
 這是定義引數值所在欄位最小寬度的整數。 如果引數值的長度等於或長於「寬度」  ，則列印出的值不帶填補。 如果值短於「寬度」  ，則會將值填補至「寬度」  中指定的長度。  
  
 星號 (*) 表示寬度是由引數清單中相關聯的引數所指定，必須是整數值。  
  
 *有效位數*  
  
 這是從字串值的引數值取得的最大字元數。 例如，如果字串有五個字元而有效位數為 3，則只會使用字串值的前三個字元。  
  
 至於整數值，「有效位數」  是列印出的最少小數位數。  
  
 星號 (*) 表示有效位數是由引數清單中相關聯的引數所指定，必須是整數值。  
  
 {h | l} *類型*  
  
 這會搭配字元類型 d、i、o、s、x、X 或 u 一起使用，並建立 **shortint** (h) 或 **longint** (l) 值。  
  
|類型規格|表示|  
|------------------------|----------------|  
|d 或 i|帶正負號的整數|  
|o|不帶正負號的八進位|  
|s|String|  
|u|不帶正負號的整數|  
|x 或 X|不帶正負號的十六進位|  
  
> [!NOTE]  
>  這些類型規格的基礎乃是原本為 C 標準程式庫中 **printf** 函數所定義的規格。 用於 RAISERROR 訊息字串的類型規格對應到 [!INCLUDE[tsql](../../includes/tsql-md.md)] 資料類型，而用於 **printf** 的規格對應到 C 語言資料類型。 當 [!INCLUDE[tsql](../../includes/tsql-md.md)] 沒有類似於相關聯 C 資料類型的資料類型時，RAISERROR 不支援用於 **printf** 的類型規格。 例如，RAISERROR 不支援指標的 *%p* 規格，因為 [!INCLUDE[tsql](../../includes/tsql-md.md)] 沒有指標資料類型。  
  
> [!NOTE]  
>  若要將某個值轉換為 [!INCLUDE[tsql](../../includes/tsql-md.md)] **bigint** 資料類型，請指定 **%I64d**。  
  
 *\@local_variable*  
 這是任何有效字元資料類型的變數，其中包含採用與 *msg_str* 相同方法格式化的字串。 *\@local_variable* 必須是 **char** 或 **varchar**，或者必須能夠隱含轉換為這兩種資料類型。  
  
 *severity*  
 這是與這則訊息相關聯的使用者自訂嚴重性層級。 當利用 *msg_id* 來引發使用 sp_addmessage 建立的使用者自訂訊息時，RAISERROR 上指定的嚴重性會覆寫 sp_addmessage 中指定的嚴重性。  
  
 任何使用者皆可指定從 0 到 18 的嚴重性層級。 從 19 到 25 的嚴重性層級只能由系統管理員 (sysadmin) 固定伺服器角色成員或具有 ALTER TRACE 權限的使用者來指定。 因為從 19 到 25 的嚴重性層級需要 WITH LOG 選項。 小於 0 的嚴重性層級會被解譯為 0。 大於 25 的嚴重性層級會被解譯為 25。  
  
> [!CAUTION]  
>  從 20 到 25 的嚴重性層級是極嚴重的。 如果遇到嚴重的嚴重性層級，用戶端連接會在收到訊息之後中斷，而該錯誤會記錄在錯誤和應用程式記錄檔中。  
  
 您可以指定 -1，以傳回與錯誤相關聯的嚴重性值，如下列範例所示。  
  
```sql  
RAISERROR (15600,-1,-1, 'mysp_CreateCustomer');  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
 ```
 Msg 15600, Level 15, State 1, Line 1   
 An invalid parameter or option was specified for procedure 'mysp_CreateCustomer'.
 ```  
  
 *state*  
 這是介於 0 到 255 之間的整數。 負值預設為 1。 不應使用大於 255 的值。 
  
 如果相同的使用者自訂錯誤在多個位置引發，針對每個位置使用唯一的狀態碼可以協助您找出引發錯誤的程式碼區段。  
  
 *引數*  
 這些參數可替代 *msg_str* 或對應於 *msg_id* 的訊息中所定義的 變數。 可以有 0 或更多的替代參數，但是替代參數的總數不能超過 20。 每個替代參數都可以是區域變數或任何以下的這些資料類型：**tinyint**、**smallint**、**int**、**char**、**varchar**、**nchar**、**nvarchar**、**binary** 或 **varbinary**。 不支援其他資料類型。  
  
 *選項*  
 這是錯誤的自訂選項，可以是下表中的值之一。  
  
|值|描述|  
|-----------|-----------------|  
|記錄|在 [!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] [!INCLUDE[ssDE](../../includes/ssde-md.md)] 執行個體的錯誤記錄檔和應用程式記錄檔中記錄錯誤。 記錄在錯誤記錄檔中的錯誤目前最大限制為 440 位元組。 只有系統管理員 (sysadmin) 固定伺服器角色成員，或具有 ALTER TRACE 權限的使用者，才可以指定 WITH LOG。<br /><br /> [!INCLUDE[applies](../../includes/applies-md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)], [!INCLUDE[ssSDS](../../includes/sssds-md.md)]|  
|NOWAIT|立即傳送訊息給用戶端。<br /><br /> [!INCLUDE[applies](../../includes/applies-md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)], [!INCLUDE[ssSDS](../../includes/sssds-md.md)]|  
|SETERROR|不論嚴重性層級為何，都將 @@ERROR 和 ERROR_NUMBER 值設定為 *msg_id* 或 50000。<br /><br /> [!INCLUDE[applies](../../includes/applies-md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)], [!INCLUDE[ssSDS](../../includes/sssds-md.md)]|  
  
## <a name="remarks"></a>備註  
 由 RAISERROR 所產生的錯誤，運作方式和由 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 程式碼所產生的錯誤相同。 由 RAISERROR 指定的值是由 ERROR_LINE、ERROR_MESSAGE、ERROR_NUMBER、ERROR_PROCEDURE、ERROR_SEVERITY、ERROR_STATE 和 @@ERROR 等系統函數所報告。 在 TRY 區塊以 11 或更高的嚴重性來執行 RAISERROR 時，RAISERROR 會傳送控制項到相關聯的 CATCH 區塊。 如果在下列情況下執行 RAISERROR，會將錯誤傳回給呼叫端：  
  
-   在任何 TRY 區塊的範圍外執行。  
  
-   在 TRY 區塊以 10 或更低的嚴重性執行。  
  
-   以會結束資料庫連接的 20 或更高的嚴重性執行。  
  
 CATCH 區塊可以使用 RAISERROR 來重新擲出錯誤，這個錯誤可藉由使用 ERROR_NUMBER 和 ERROR_MESSAGE 等系統函數擷取原來的錯誤資訊，來叫用 CATCH 區塊。 對於嚴重性從 1 到 10 的訊息，@@ERROR 預設會設定為 0。  
  
 當 *msg_id* 指定來自 sys.messages 目錄檢視表的使用者自訂訊息時，RAISERROR 在處理文字資料行的訊息時所使用的規則，會與套用至利用 *msg_str* 而指定之使用者定義訊息文字的規則相同。 使用者自訂訊息文字可以包含轉換規格，且 RAISERROR 會將引數值對應到轉換規格。 利用 sp_addmessage 來加入使用者自訂錯誤訊息，以及利用 sp_dropmessage 來刪除使用者自訂錯誤訊息。  
  
 RAISERROR 可以用作 PRINT 的替代方法，將訊息傳回給呼叫的應用程式。 RAISERROR 支援類似於 C 標準程式庫中 **printf** 函數功能的字元替代，而 [!INCLUDE[tsql](../../includes/tsql-md.md)] PRINT 陳述式則不支援。 PRINT 陳述式不受 TRY 區塊影響，而在 TRY 區塊中以嚴重性 11 到 19 執行的 RAISERROR 會將控制項傳送給相關聯的 CATCH 區塊。 若要使用 RAISERROR 傳回來自 TRY 區塊的訊息，而不叫用 CATCH 區塊，請指定 10 或更低的嚴重性。  
  
 通常連續引數會取代連續轉換規格；第一個引數會取代第一個轉換規格，第二個引數會取代第二個轉換規格，依此類推。 例如，在下列 `RAISERROR` 陳述式中，`N'number'` 的第一個引數會取代 `%s` 的第一個轉換規格，而第二個引數 `5` 則會取代 `%d.` 的第二個轉換規格。  
  
```sql  
RAISERROR (N'This is message %s %d.', -- Message text.  
           10, -- Severity,  
           1, -- State,  
           N'number', -- First argument.  
           5); -- Second argument.  
-- The message text returned is: This is message number 5.  
GO  
```  
  
 如果為轉換規格的寬度或有效位數指定星號 (*)，則要用於寬度或有效位數的值會被指定成整數引數值。 在這個情況下，一個轉換規格最多可使用三個引數，分別是寬度、有效位數和替代值。  
  
 例如，下列兩個 `RAISERROR` 陳述式都會傳回相同的字串。 一個在引數清單中指定寬度和有效位數值；另一個在轉換規格中指定寬度和有效位數值。  
  
```sql  
RAISERROR (N'<\<%*.*s>>', -- Message text.  
           10, -- Severity,  
           1, -- State,  
           7, -- First argument used for width.  
           3, -- Second argument used for precision.  
           N'abcde'); -- Third argument supplies the string.  
-- The message text returned is: <<    abc>>.  
GO  
RAISERROR (N'<\<%7.3s>>', -- Message text.  
           10, -- Severity,  
           1, -- State,  
           N'abcde'); -- First argument supplies the string.  
-- The message text returned is: <<    abc>>.  
GO  
```  
  
## <a name="examples"></a>範例  
  
### <a name="a-returning-error-information-from-a-catch-block"></a>A. 從 CATCH 區塊傳回錯誤資訊  
 下列程式碼範例顯示如何在 `RAISERROR` 區塊內使用 `TRY`，使執行位置跳到相關聯的 `CATCH` 區塊。 這個範例也會顯示如何利用 `RAISERROR`，來傳回叫用 `CATCH` 區塊之錯誤的相關資訊。  
  
> [!NOTE]  
>  RAISERROR 只會產生由 1 到 127 之狀態的錯誤。 因為 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 可能會引發狀態為 0 的錯誤，我們建議您在傳送 ERROR_STATE 的值給 RAISERROR 的狀態參數之前，先檢查它傳回的錯誤狀態。  
  
```sql  
BEGIN TRY  
    -- RAISERROR with severity 11-19 will cause execution to   
    -- jump to the CATCH block.  
    RAISERROR ('Error raised in TRY block.', -- Message text.  
               16, -- Severity.  
               1 -- State.  
               );  
END TRY  
BEGIN CATCH  
    DECLARE @ErrorMessage NVARCHAR(4000);  
    DECLARE @ErrorSeverity INT;  
    DECLARE @ErrorState INT;  
  
    SELECT   
        @ErrorMessage = ERROR_MESSAGE(),  
        @ErrorSeverity = ERROR_SEVERITY(),  
        @ErrorState = ERROR_STATE();  
  
    -- Use RAISERROR inside the CATCH block to return error  
    -- information about the original error that caused  
    -- execution to jump to the CATCH block.  
    RAISERROR (@ErrorMessage, -- Message text.  
               @ErrorSeverity, -- Severity.  
               @ErrorState -- State.  
               );  
END CATCH;  
```  
  
### <a name="b-creating-an-ad-hoc-message-in-sysmessages"></a>B. 在 sys.messages 中建立特定訊息  
 下列範例會顯示如何引發儲存在 sys.message 目錄檢視表的訊息。 使用 `sp_addmessage` 系統預存程序，可將訊息加入 sys.messages 目錄檢視表中成為訊息編號 `50005`。  
  
```sql  
sp_addmessage @msgnum = 50005,  
              @severity = 10,  
              @msgtext = N'<\<%7.3s>>';  
GO  
RAISERROR (50005, -- Message id.  
           10, -- Severity,  
           1, -- State,  
           N'abcde'); -- First argument supplies the string.  
-- The message text returned is: <<    abc>>.  
GO  
sp_dropmessage @msgnum = 50005;  
GO  
```  
  
### <a name="c-using-a-local-variable-to-supply-the-message-text"></a>C. 使用區域變數來提供訊息文字  
 下列程式碼範例會顯示如何使用本機變數為 `RAISERROR` 陳述式提供訊息文字。  
  
```sql  
DECLARE @StringVariable NVARCHAR(50);  
SET @StringVariable = N'<\<%7.3s>>';  
  
RAISERROR (@StringVariable, -- Message text.  
           10, -- Severity,  
           1, -- State,  
           N'abcde'); -- First argument supplies the string.  
-- The message text returned is: <<    abc>>.  
GO  
```  
  
## <a name="see-also"></a>另請參閱  
 [內建函數 &#40;Transact-SQL&#41;](~/t-sql/functions/functions.md)   
 [DECLARE @local_variable (Transact-SQL)](../../t-sql/language-elements/declare-local-variable-transact-sql.md)   
 [PRINT &#40;Transact-SQL&#41;](../../t-sql/language-elements/print-transact-sql.md)   
 [sp_addmessage &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-addmessage-transact-sql.md)   
 [sp_dropmessage &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-dropmessage-transact-sql.md)   
 [sys.messages &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/messages-for-errors-catalog-views-sys-messages.md)   
 [xp_logevent &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/xp-logevent-transact-sql.md)   
 [@@ERROR &#40;Transact-SQL&#41;](../../t-sql/functions/error-transact-sql.md)   
 [ERROR_LINE &#40;Transact-SQL&#41;](../../t-sql/functions/error-line-transact-sql.md)   
 [ERROR_MESSAGE &#40;Transact-SQL&#41;](../../t-sql/functions/error-message-transact-sql.md)   
 [ERROR_NUMBER &#40;Transact-SQL&#41;](../../t-sql/functions/error-number-transact-sql.md)   
 [ERROR_PROCEDURE &#40;Transact-SQL&#41;](../../t-sql/functions/error-procedure-transact-sql.md)   
 [ERROR_SEVERITY &#40;Transact-SQL&#41;](../../t-sql/functions/error-severity-transact-sql.md)   
 [ERROR_STATE &#40;Transact-SQL&#41;](../../t-sql/functions/error-state-transact-sql.md)   
 [TRY...CATCH &#40;Transact-SQL&#41;](../../t-sql/language-elements/try-catch-transact-sql.md)  
  
  

