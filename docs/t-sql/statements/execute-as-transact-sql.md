---
description: EXECUTE AS (Transact-SQL)
title: EXECUTE AS (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 08/27/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- EXECUTE AS
- EXECUTE_AS_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- REVERT statement
- WITH NO REVERT clause
- sessions [SQL Server], execution context
- EXECUTE AS
- execution context [SQL Server]
- switching execution context
ms.assetid: 613b8271-7f7d-4378-b7a2-5a7698551dbd
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: = azuresqldb-current || >= sql-server-2016 || >= sql-server-linux-2017||=azure-sqldw-latest
ms.openlocfilehash: fa823898f479867657fb106ab0d929aa861b7da5
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98081070"
---
# <a name="execute-as-transact-sql"></a>EXECUTE AS (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa.md)]

  設定工作階段的執行內容。  
  
 依預設，工作階段會在使用者登入時起始，在使用者登出時結束。 工作階段期間的所有作業是依對該使用者的權限檢查而定。 執行 **EXECUTE AS** 陳述式時，工作階段的執行內容會切換到指定的登入或使用者名稱。 在內容切換之後，系統會檢查該帳戶的登入和使用者安全性 Token 權限，而不是檢查呼叫 **EXECUTE AS** 陳述式的人員。 基本上，使用者或登入帳戶的模擬是在工作階段或模組執行的期間，否則會明確還原內容切換。  
  

  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
{ EXEC | EXECUTE } AS <context_specification>  
[;]  
  
<context_specification>::=  
{ LOGIN | USER } = 'name'  
    [ WITH { NO REVERT | COOKIE INTO @varbinary_variable } ]   
| CALLER  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 LOGIN  
 **適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。  
  
 指定您要模擬的執行內容是登入。 模擬範圍是在伺服器層級。  
  
> [!NOTE]  
>  自主資料庫或 SQL Database 或 [!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] 無法使用這個選項。  
  
 USER  
 指定您要模擬的內容是目前資料庫中的使用者。 模擬範圍僅限於目前資料庫。 通往資料庫使用者的內容切換不會繼承該使用者的伺服器層級權限。  
  
> [!IMPORTANT]  
>  如果通往資料庫使用者的內容切換在使用中，任何人想要存取資料庫以外的資源，都會導致陳述式失敗。 其中包括 USE *database* 陳述式、分散式查詢，以及參考其他使用三部分或四部分識別碼的資料庫查詢。  
  
 '*name*' 是有效的使用者或登入名稱。 *name* 必須是 **sysadmin** 固定伺服器角色的成員，或是以主體形式分別存在於 [sys.database_principals](../../relational-databases/system-catalog-views/sys-database-principals-transact-sql.md) 或 [sys.server_principals](../../relational-databases/system-catalog-views/sys-server-principals-transact-sql.md) 中。  
  
 *name* 可以指定為本機變數。  
  
 *name* 必須是單一帳戶，不可以是群組、角色、憑證、金鑰或內建帳戶，例如 NT AUTHORITY\LocalService、NT AUTHORITY\NetworkService 或 NT AUTHORITY\LocalSystem。  
  
 如需詳細資訊，請參閱本主題稍後的[指定使用者或登入名稱](#_user)。  
  
 NO REVERT  
 指定內容切換不能還原回先前的內容。 **NO REVERT** 選項只能在特定層級使用。  
  
 如需如何還原到先前內容的詳細資訊，請參閱 [REVERT &#40;Transact-SQL&#41;](../../t-sql/statements/revert-transact-sql.md)。  
  
 COOKIE INTO @*varbinary_variable*  
 指定如果呼叫的 REVERT WITH COOKIE 陳述式包含正確的 @*varbinary_variable* 值，則執行內容只可以還原回先前的內容。 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 會將 Cookie 傳遞到 @*varbinary_variable*。 **COOKIE INTO** 選項只能在特定層級使用。  
  
 @*varbinary_variable* 是 **varbinary(8000)** 。  
  
> [!NOTE]  
>  Cookie **OUTPUT** 參數目前記載為 **varbinary(8000)** ，這是正確的長度上限。 但目前的實作會傳回 **varbinary(100)** 。 應用程式應保留 **varbinary(8000)** ，如此後續版本的 Cookie 傳回大小如有增加，應用程式才可繼續正常地運作。  
  
 CALLER  
 用於模組內部時，指定模組內部的陳述式是在模組呼叫者的內容中執行。
用於模組外部時，陳述式沒有動作。
 > [!NOTE]  
>  此選項在 [!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)]中無法使用。  
  
## <a name="remarks"></a>備註  
 執行內容中的變更持續有效，直到發生下列項目之一為止：  
  
-   執行另一個 EXECUTE AS 陳述式。  
  
-   執行 REVERT 陳述式。  
  
-   卸除工作階段。  
  
-   造成執行的命令結束的預存程式或觸發程序。  
  
您可以藉由跨多個主體多次呼叫 EXECUTE AS 陳述式來建立執行內容堆疊。 REVERT 陳述式被呼叫時，會將內容切換到內容堆疊中上一層級的登入或使用者。 如需這個行為的示範，請參閱[範例 A](#_exampleA)。  
  
##  <a name="specifying-a-user-or-login-name"></a><a name="_user"></a> 指定使用者或登入名稱  
 在 EXECUTE AS \<context_specification> 中指定的使用者或登入名稱，必須以主體形式分別存在於 **sys.database_principals** 或 **sys.server_principals** 中，否則 EXECUTE AS 陳述式就會失敗。 此外，還必須授與主體的 IMPERSONATE 權限。 除非呼叫者是資料庫擁有者，或是 **sysadmin** 固定伺服器角色的成員，否則主體必須存在，即使當使用者透過 Windows 群組成員資格在存取資料庫或 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的執行個體時也一樣。 例如，假設有下列情況： 
  
-   **CompanyDomain\SQLUsers** 群組有權存取 **Sales** 資料庫。  
  
-   **CompanyDomain\SqlUser1** 是 **SQLUsers** 的成員，因此對 **Sales** 資料庫具有隱含的存取權。  
  
 雖然 **CompanyDomain\SqlUser1** 有權透過 **SQLUsers** 群組中的成員資格來存取資料庫，但是 `EXECUTE AS USER = 'CompanyDomain\SqlUser1'` 陳述式會失敗，因為 `CompanyDomain\SqlUser1` 並未以主體形式存在於資料庫中。  
  
如果使用者被遺棄 (相關聯的登入不存在)，而且使用者並非以 **WITHOUT LOGIN** 建立，該使用者的 **EXECUTE AS** 將會失敗。  
  
## <a name="best-practice"></a>最佳做法  
 指定一個登入或使用者，它具有執行工作階段中作業所需要的最低權限。 例如，如果只需要資料庫層級權限，就不要指定具有伺服器層級權限的登入名稱；或者，除非需要其權限，否則不要指定資料庫擁有者帳戶。  
  
> [!CAUTION]  
>  只要 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 可以解析名稱，EXECUTE AS 陳述式即可順利地運作。 即使 Windows 使用者無權存取 [!INCLUDE[ssDE](../../includes/ssde-md.md)]，只要網域使用者存在，Windows 或許可以解析 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的使用者。 這可能會導致無權存取 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的使用者順利地登入，但模擬登入只有 public 或 guest 的權限。  
  
## <a name="using-with-no-revert"></a>使用 WITH NO REVERT  
 當 EXECUTE AS 陳述式包括選擇性的 WITH NO REVERT 子句時，不能使用 REVERT 或藉由執行另一個 EXECUTE AS 陳述式來重設工作階段的執行內容。 陳述式設定的內容會持續有效，直到卸除工作階段為止。  
  
 指定 WITH NO REVERT COOKIE = @*varbinary_variabl* e 子句時，[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 會將 Cookie 值傳遞到 @*varbinary_variabl* e。 只有當呼叫的 EVERT WITH COOKIE = @*varbinary_variable* 陳述式包含相同 *\@varbinary_variable* 值時，該陳述式所設定的執行內容才能還原到先前內容。  
  
 這個選項在使用連接共用的環境中相當有用。 連接共用是資料庫連接群組的維護，這些連接是供應用程式伺服器上的應用程式重複使用。 由於只有 EXECUTE AS 陳述式的呼叫者知道傳送到 *\@varbinary_variable* 的值，因此呼叫者可以保證其所建立的執行內容無法由其他人變更。  
  
## <a name="determining-the-original-login"></a>決定原始登入  
 使用 [ORIGINAL_LOGIN](../../t-sql/functions/original-login-transact-sql.md) 函式時，可傳回連線到 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體的登入名稱。 您可以利用這個函數來傳回有許多明確或隱含內容切換的工作階段中原始登入的識別。  
  
## <a name="permissions"></a>權限  
 若要指定某項登入的 **EXECUTE AS**，呼叫者必須具有指定登入名稱的 **IMPERSONATE** 權限，而且不應拒絕其 **IMPERSONATE ANY LOGIN** 權限。 若要指定資料庫使用者的 **EXECUTE AS** 權限，呼叫者必須具有指定使用者名稱的 **IMPERSONATE** 權限。 指定 **EXECUTE AS CALLER** 時，不需要 **IMPERSONATE** 權限。  
  
## <a name="examples"></a>範例  
  
###  <a name="a-using-execute-as-and-revert-to-switch-context"></a><a name="_exampleA"></a> A. 使用 EXECUTE AS 及 REVERT 切換內容  
 下列範例會利用多個主體來建立一個內容執行堆疊。 然後再用 `REVERT` 陳述式，將執行內容重設為前一個呼叫者。 `REVERT` 陳述式在上移堆疊時會執行多次，直到執行內容設為原始呼叫者為止。  
  

```sql
USE AdventureWorks2012;  
GO  
--Create two temporary principals  
CREATE LOGIN login1 WITH PASSWORD = 'J345#$)thb';  
CREATE LOGIN login2 WITH PASSWORD = 'Uor80$23b';  
GO  
CREATE USER user1 FOR LOGIN login1;  
CREATE USER user2 FOR LOGIN login2;  
GO  
--Give IMPERSONATE permissions on user2 to user1  
--so that user1 can successfully set the execution context to user2.  
GRANT IMPERSONATE ON USER:: user2 TO user1;  
GO  
--Display current execution context.  
SELECT SUSER_NAME(), USER_NAME();  
-- Set the execution context to login1.   
EXECUTE AS LOGIN = 'login1';  
--Verify the execution context is now login1.  
SELECT SUSER_NAME(), USER_NAME();  
--Login1 sets the execution context to login2.  
EXECUTE AS USER = 'user2';  
--Display current execution context.  
SELECT SUSER_NAME(), USER_NAME();  
-- The execution context stack now has three principals: the originating caller, login1 and login2.  
--The following REVERT statements will reset the execution context to the previous context.  
REVERT;  
--Display current execution context.  
SELECT SUSER_NAME(), USER_NAME();  
REVERT;  
--Display current execution context.  
SELECT SUSER_NAME(), USER_NAME();  
  
--Remove temporary principals.  
DROP LOGIN login1;  
DROP LOGIN login2;  
DROP USER user1;  
DROP USER user2;  
GO  
```  
  
### <a name="b-using-the-with-cookie-clause"></a>B. 使用 WITH COOKIE 子句  
 下列範例會將工作階段的執行內容設為指定使用者，並且指定 WITH NO REVERT COOKIE = @*varbinary_variabl* e 子句。 `REVERT` 陳述式必須指定傳給 `@cookie` 陳述式中的 `EXECUTE AS` 變數值，才能順利將內容還原回呼叫端。 若要執行這個範例，則必須具備在範例 A 中建立的 `login1` 登入和 `user1` 使用者。  
  

```sql 
DECLARE @cookie VARBINARY(8000);  
EXECUTE AS USER = 'user1' WITH COOKIE INTO @cookie;  
-- Store the cookie in a safe location in your application.  
-- Verify the context switch.  
SELECT SUSER_NAME(), USER_NAME();  
--Display the cookie value.  
SELECT @cookie;  
GO  
-- Use the cookie in the REVERT statement.  
DECLARE @cookie VARBINARY(8000);  
-- Set the cookie value to the one from the SELECT @cookie statement.  
SET @cookie = <value from the SELECT @cookie statement>;  
REVERT WITH COOKIE = @cookie;  
-- Verify the context switch reverted.  
SELECT SUSER_NAME(), USER_NAME();  
GO  
```  
  
## <a name="see-also"></a>另請參閱  
 [REVERT &#40;Transact-SQL&#41;](../../t-sql/statements/revert-transact-sql.md)   
 [EXECUTE AS 子句 &#40;Transact-SQL&#41;](../../t-sql/statements/execute-as-clause-transact-sql.md)  
  
  

