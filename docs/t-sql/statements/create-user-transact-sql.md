---
description: CREATE USER (Transact-SQL)
title: CREATE USER (Transact-SQL)
ms.custom: ''
ms.date: 11/06/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- WITHOUT_LOGIN_TSQL
- CREATE_USER_TSQL
- SQL13.SWB.DATABASEUSER.OWNEDSCHEMAS.F1
- WITHOUT LOGIN
- CREATE USER
- ALLOW_ENCRYPTED_VALUE_MODIFICATIONS
- ALLOW_ENCRYPTED_VALUE_MODIFICATIONS_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- adding users
- WITHOUT LOGIN [SQL Server]
- CREATE USER statement
- database user additions [SQL Server]
- USER WITHOUT LOGIN [SQL Server]
- users [SQL Server], adding
- users [SQL Server]
ms.assetid: 01de7476-4b25-4d58-85b7-1118fe64aa80
author: VanMSFT
ms.author: vanto
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 446e9fa7dc252da75bc1c14ecf3fbe0fdf92b3e8
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98170320"
---
# <a name="create-user-transact-sql"></a>CREATE USER (Transact-SQL)

[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  將使用者加入目前資料庫中。 以下列出 12 種使用者類型與最基本的語法範例：  
  
**具有 master 登入的使用者** - 這是最常見的使用者類型。  
  
-   具有 Windows Active Directory 帳戶登入的使用者。 `CREATE USER [Contoso\Fritz];`     
-   依據 Windows 群組登入的使用者。 `CREATE USER [Contoso\Sales];`   
-   依據使用 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 驗證登入的使用者。 `CREATE USER Mary;`  
  
**在資料庫進行驗證的使用者** - 此選項有助於讓您的資料庫更具可攜性。  
 [!INCLUDE[ssSDS_md](../../includes/sssds-md.md)] 一律允許。 只有 [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] 中的自主資料庫允許。  
  
-   依據沒有登入之 Windows 使用者的使用者。 `CREATE USER [Contoso\Fritz];`    
-   依據沒有登入之 Windows 群組的使用者。 `CREATE USER [Contoso\Sales];`  
-   具有 Azure Active Directory 使用者身分的 [!INCLUDE[ssSDS](../../includes/sssds-md.md)] 或 [!INCLUDE[ssSDW_md](../../includes/sssdw-md.md)] 使用者。 `CREATE USER [Fritz@contoso.com] FROM EXTERNAL PROVIDER;`     

-   具有密碼之自主資料庫使用者。 (不適用於 [!INCLUDE[ssSDW_md](../../includes/sssdw-md.md)]。) `CREATE USER Mary WITH PASSWORD = '********';`   
  
**具有 Windows 主體的使用者，其透過 Windows 群組登入進行連接**  
  
-   依據沒有登入之 Windows 使用者，但可透過 Windows 群組成員資格連接到 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 的使用者。 `CREATE USER [Contoso\Fritz];`  
  
-   依據沒有登入之 Windows 群組，但可透過不同的 Windows 群組成員資格連接到 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 的使用者。 `CREATE USER [Contoso\Fritz];`  
  
**無法驗證的使用者** - 這些使用者無法登入 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 或 [!INCLUDE[ssSDS](../../includes/sssds-md.md)]。  
  
-   沒有登入的使用者。 無法登入但可獲得權限。 `CREATE USER CustomApp WITHOUT LOGIN;`    
-   依據憑證的使用者。 無法登入，但可獲得權限而且可簽署模組。 `CREATE USER TestProcess FOR CERTIFICATE CarnationProduction50;`  
-   依據非對稱金鑰的使用者。 無法登入，但可獲得權限而且可簽署模組。 `CREATE User TestProcess FROM ASYMMETRIC KEY PacificSales09;`   
 
![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
-- Syntax for SQL Server, Azure SQL Database, and Azure SQL Managed Instance
  
-- Syntax Users based on logins in master  
CREATE USER user_name   
    [   
        { FOR | FROM } LOGIN login_name   
    ]  
    [ WITH <limited_options_list> [ ,... ] ]   
[ ; ]  
  
-- Users that authenticate at the database  
CREATE USER   
    {  
      windows_principal [ WITH <options_list> [ ,... ] ]  
  
    | user_name WITH PASSWORD = 'password' [ , <options_list> [ ,... ]   
    | Azure_Active_Directory_principal FROM EXTERNAL PROVIDER   
    }  
  
 [ ; ]  
  
-- Users based on Windows principals that connect through Windows group logins  
CREATE USER   
    {   
          windows_principal [ { FOR | FROM } LOGIN windows_principal ]  
        | user_name { FOR | FROM } LOGIN windows_principal  
}  
    [ WITH <limited_options_list> [ ,... ] ]   
[ ; ]  
  
-- Users that cannot authenticate   
CREATE USER user_name   
    {  
         WITHOUT LOGIN [ WITH <limited_options_list> [ ,... ] ]  
       | { FOR | FROM } CERTIFICATE cert_name   
       | { FOR | FROM } ASYMMETRIC KEY asym_key_name   
    }  
 [ ; ]  
  
<options_list> ::=  
      DEFAULT_SCHEMA = schema_name  
    | DEFAULT_LANGUAGE = { NONE | lcid | language name | language alias }  
    | SID = sid   
    | ALLOW_ENCRYPTED_VALUE_MODIFICATIONS = [ ON | OFF ] ]  
  
<limited_options_list> ::=  
      DEFAULT_SCHEMA = schema_name ]   
    | ALLOW_ENCRYPTED_VALUE_MODIFICATIONS = [ ON | OFF ] ]  
  
-- SQL Database syntax when connected to a federation member  
CREATE USER user_name  
[;]

-- Syntax for users based on Azure AD logins for Azure SQL Managed Instance
CREATE USER user_name   
    [   { FOR | FROM } LOGIN login_name  ]  
    | FROM EXTERNAL PROVIDER
    [ WITH <limited_options_list> [ ,... ] ]   
[ ; ]  

<limited_options_list> ::=  
      DEFAULT_SCHEMA = schema_name 
    | DEFAULT_LANGUAGE = { NONE | lcid | language name | language alias }   
    | ALLOW_ENCRYPTED_VALUE_MODIFICATIONS = [ ON | OFF ] ] 
```

> [!NOTE]
> 建立完成後的 Azure SQL 受控執行個體 Azure AD 管理員功能已變更。 如需詳細資訊，請參閱 [MI 的新 Azure AD 管理員功能](/azure/sql-database/sql-database-aad-authentication-configure#new-azure-ad-admin-functionality-for-mi)。

```syntaxsql
-- Syntax for Azure Synapse Analytics  
  
CREATE USER user_name   
    [ { { FOR | FROM } { LOGIN login_name }   
      | WITHOUT LOGIN  
    ]   
    [ WITH DEFAULT_SCHEMA = schema_name ]  
[;]

CREATE USER Azure_Active_Directory_principal FROM EXTERNAL PROVIDER  
    [ WITH DEFAULT_SCHEMA = schema_name ]  
[;]
``` 
  
```syntaxsql
-- Syntax for Parallel Data Warehouse  
  
CREATE USER user_name   
    [ { { FOR | FROM }  
      {   
        LOGIN login_name   
      }   
      | WITHOUT LOGIN  
    ]   
    [ WITH DEFAULT_SCHEMA = schema_name ]  
[;]  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *user_name*  
 指定在這個資料庫內用來識別使用者的名稱。 *user_name* 是一種 **sysname**。 該名稱長度最多可達 128 個字元。 當建立依據 Windows 主體的使用者時，除非指定另一個使用者名稱，否則 Windows 主體名稱會成為使用者名稱。  
  
 LOGIN *login_name*  
 指定目前建立之資料庫使用者的登入。 *login_name* 必須是伺服器中的有效登入。 可以是依據 Windows 主體 (使用者或群組) 的登入，或是使用 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 驗證的登入。 當這個 SQL Server 登入進入資料庫時，它會取得正在建立之資料庫使用者的名稱和識別碼。 在建立從 Windows 主體所對應的登入時，請使用以下格式： **[** _\<domainName\>_ **\\** _\<loginName\>_ **]** 。 如需範例，請參閱[語法摘要](#SyntaxSummary)。  
  
 如果 CREATE USER 陳述式是 SQL 批次中的唯一陳述式，Azure SQL Database 會支援 WITH LOGIN 子句。 如果 CREATE USER 陳述式不是 SQL 批次中的唯一陳述式或是在動態 SQL 中執行，則不支援 WITH LOGIN 子句。  
  
 WITH DEFAULT_SCHEMA = *schema_name*  
 指定在解析這個資料庫使用者的物件名稱時，伺服器所搜尋到的第一個結構描述。  
  
 '*windows_principal*'  
 為正在建立的資料庫使用者指定 Windows 主體。 *windows_principal* 可以是 Windows 使用者或 Windows 群組。 即使 *windows_principal* 不具備登入，也可以建立使用者。 當連接到 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 時，如果 *windows_principal* 不具備登入，則 Windows 主體必須透過具有登入的 Windows 群組成員資格在 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 進行驗證，或者，連接字串必須將自主資料庫指定為初始目錄。 從 Windows 主體建立使用者時，請使用以下格式： **[** _\<domainName\>_ **\\** _\<loginName\>_ **]** 。 如需範例，請參閱[語法摘要](#SyntaxSummary)。 具有 Active Directory 使用者身分的使用者，其名稱僅限 21 個字元以內。
  
 '*Azure_Active_Directory_principal*'  
 **適用於**：[!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)]、[!INCLUDE[ssSDW_md](../../includes/sssdw-md.md)]。  
  
 為所要建立的資料庫使用者指定 Azure Active Directory 主體。 *Azure_Active_Directory_principal* 可以是 Azure Active Directory 使用者、Azure Active Directory 群組或 Azure Active Directory 應用程式。 (Azure Active Directory 使用者中不能在 [!INCLUDE[ssSDS](../../includes/sssds-md.md)] 中使用 Windows 驗證登入；只有資料庫使用者可以)。連接字串必須將自主資料庫指定為初始目錄。

 針對 Azure AD 主體，CREATE USER 語法需要：

- 適用於 Azure AD 使用者之 Azure AD 物件的 UserPrincipalName。

  - `CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;`  
  - `CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;`

- 適用於 Azure AD 群組和 Azure AD 應用程式之 Azure AD 物件的 DisplayName。 如果您有「護士」安全性群組，則可以使用：  
  
  - `CREATE USER [Nurses] FROM EXTERNAL PROVIDER;`  
  
 如需詳細資訊，請參閱[使用 Azure Active Directory 驗證連線到 SQL Database](/azure/azure-sql/database/authentication-aad-overview)。  
  
WITH PASSWORD = '*password*'  
 **適用於**：[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 及更新版本、[!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)]。  
  
 只能用於自主資料庫。 指定正在建立之使用者的密碼。 從 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 開始，預存密碼資訊會使用加料式 (Salted) 密碼的 SHA-512 加以計算。  
  
WITHOUT LOGIN  
 指定使用者不對應到現有的登入。  
  
CERTIFICATE *cert_name*  
 **適用於**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本、[!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)]。  
  
 指定目前建立之資料庫使用者的憑證。  
  
ASYMMETRIC KEY *asym_key_name*  
 **適用於**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本、[!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)]。  
  
 指定目前建立之資料庫使用者的非對稱金鑰。  
  
DEFAULT_LANGUAGE = *{ NONE \| \<lcid> \| \<language name> \| \<language salias> }*  
 **適用於**：[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 及更新版本、[!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)]。  
  
 指定新使用者的預設語言。 如果已指定使用者的預設語言，但稍後變更資料庫的預設語言，使用者預設語言會保持為指定值。 如果未指定預設語言，則使用者的預設語言將是資料庫的預設語言。 如果未指定使用者的預設語言，而稍後變更資料庫的預設語言，使用者的預設語言會變成資料庫的新預設語言。  
  
> [!IMPORTANT]  
>  *DEFAULT_LANGUAGE* 只用於自主資料庫使用者。  
  
SID = *sid*  
 **適用對象**：[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 及更新版本。  
  
 僅適用於具有密碼之自主資料庫使用者 ([!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 驗證)。 指定新資料庫使用者的 SID。 如果未選取這個選項，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 將自動指派 SID。 使用 SID 參數即可在多個資料庫中建立具有相同識別 (SID) 的使用者。 當您在多個資料庫中建立使用者以準備 Always On 容錯移轉時，這會很有用。 若要判斷使用者的 SID，請查詢 sys.database_principals。  
  
ALLOW_ENCRYPTED_VALUE_MODIFICATIONS = [ ON | **OFF** ]  
 **適用於**：[!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 及更新版本、[!INCLUDE[ssSDS](../../includes/sssds-md.md)]。  
  
 在大量複製作業時隱藏伺服器上的密碼編譯中繼資料檢查。 這會讓使用者得以在資料表或資料庫間大量複製加密資料，而無須解密資料。 預設值為 OFF。  
  
> [!WARNING]  
>  不當使用這個選項會導致資料損毀。 如需詳細資訊，請參閱[移轉透過 Always Encrypted 保護的機密資料](../../relational-databases/security/encryption/migrate-sensitive-data-protected-by-always-encrypted.md)。  
  
## <a name="remarks"></a>備註  
 如果省略了 FOR LOGIN，新的資料庫使用者就會對應至同名的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入。  
  
 預設結構描述是伺服器在解析這個資料庫使用者之物件名稱時，所搜尋到的第一個結構描述。 除非另有指定，否則預設結構描述是此資料庫使用者建立之物件的擁有者。  
  
 如果使用者有預設結構描述，則使用預設結構描述。 如果使用者沒有預設結構描述，但使用者是有預設結構描述之群組的成員，則使用群組的預設結構描述。 如果使用者沒有預設結構描述，而且是有一個以上之群組的成員，使用者的預設結構描述將會是具有最低 principle_id 且明確設定預設結構描述之 Windows 群組的結構描述。 (您無法明確選取其中一個可用的預設結構描述當做慣用結構描述)。如果無法判斷使用者的預設結構描述，即會使用 **dbo** 結構描述。  
  
 DEFAULT_SCHEMA 可以在它所指向的結構描述建立之前設定。  
  
 當您建立對應到憑證或非對稱金鑰的使用者時，不能指定 DEFAULT_SCHEMA。  
  
 如果使用者是系統管理員 (sysadmin) 固定伺服器角色的成員，則會忽略 DEFAULT_SCHEMA 的值。 系統管理員 (sysadmin) 固定伺服器角色的所有成員都有預設的 `dbo` 結構描述。  
  
 WITHOUT LOGIN 子句會建立沒有對應至 SQL Server 登入的使用者。 這個使用者可以用 guest 的身分連接到其他資料庫。 可指派權限給這位沒有登入的使用者，而且在安全性內容變更為沒有登入的使用者時，原始使用者會收到沒有登入之使用者的權限。 請參閱範例 [D. 建立及使用不含登入的使用者](#withoutLogin)。  
  
 只有對應到 Windows 主體的使用者可以包含反斜線字元 ( **\\** )。
  
 您不能以 CREATE USER 建立 guest 使用者，因為每一個資料庫都已經有 guest 使用者了。 您可以授與 guest 使用者 CONNECT 權限來啟用它，如下所示：  
  
```  
GRANT CONNECT TO guest;  
GO  
```  
  
 您可以在 [sys.database_principals](../../relational-databases/system-catalog-views/sys-database-principals-transact-sql.md) 目錄檢視中，看到資料庫使用者的資訊。

新的語法延伸模組 **FROM EXTERNAL PROVIDER** 可用於在 SQL 受控執行個體中建立伺服器層級的 Azure AD 登入。 Azure AD 登入可以讓資料庫層級的 Azure AD 主體對應到伺服器層級的 Azure AD 登入。 若要從 Azure AD 登入建立 Azure AD 使用者，請使用下列語法：

`CREATE USER [AAD_principal] FROM LOGIN [Azure AD login]`

在 SQL 受控執行個體中建立使用者時，login_name 必須對應到現有的 Azure AD 登入，否則使用 **FROM EXTERNAL PROVIDER** 子句只會建立 Azure AD 使用者，而不會在 master 資料庫中建立登入。 例如，此命令會建立包含的使用者：

`CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER`
  
##  <a name="syntax-summary"></a><a name="SyntaxSummary"></a> 語法摘要  
 **具有 master 登入的使用者**  
  
 下列清單顯示依據登入之使用者的可能語法。 未列出預設的結構描述選項。  
  
-   `CREATE USER [Domain1\WindowsUserBarry]`  
-   `CREATE USER [Domain1\WindowsUserBarry] FOR LOGIN Domain1\WindowsUserBarry`  
-   `CREATE USER [Domain1\WindowsUserBarry] FROM LOGIN Domain1\WindowsUserBarry`  
-   `CREATE USER [Domain1\WindowsGroupManagers]`  
-   `CREATE USER [Domain1\WindowsGroupManagers] FOR LOGIN [Domain1\WindowsGroupManagers]`  
-   `CREATE USER [Domain1\WindowsGroupManagers] FROM LOGIN [Domain1\WindowsGroupManagers]`  
-   `CREATE USER SQLAUTHLOGIN`  
-   `CREATE USER SQLAUTHLOGIN FOR LOGIN SQLAUTHLOGIN`  
-   `CREATE USER SQLAUTHLOGIN FROM LOGIN SQLAUTHLOGIN`  
  
**在資料庫進行驗證的使用者**  
  
 下列清單顯示只能用於自主資料庫使用者的可能語法。 所建立的使用者不會與 **master** 資料庫中的任何登入相關。 未列出預設的結構描述和語言選項。  
  
> [!IMPORTANT]  
>  此語法將資料庫的存取權以及 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 的新存取權授與使用者。  
  
-   `CREATE USER [Domain1\WindowsUserBarry]`  
-   `CREATE USER [Domain1\WindowsGroupManagers]`  
-   `CREATE USER Barry WITH PASSWORD = 'sdjklalie8rew8337!$d'`  
  
**具有 Windows 主體但不具備 master 登入的使用者**  
  
 下列清單顯示可透過 Windows 群組存取 [!INCLUDE[ssDE](../../includes/ssde-md.md)]，但不具備 **master** 登入之使用者的可能語法。 這個語法可以用於所有類型的資料庫。 未列出預設的結構描述和語言選項。  
  
 這個語法與依據 master 登入的使用者類似，但這個類別的使用者沒有 master 的登入。 使用者必須可透過 Windows 群組登入來存取 [!INCLUDE[ssDE](../../includes/ssde-md.md)]。  
  
 這個語法與依據 Windows 主體的自主資料庫使用者類似，但這個類別的使用者沒有 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 的新存取權。  
  
-   `CREATE USER [Domain1\WindowsUserBarry]`  
-   `CREATE USER [Domain1\WindowsUserBarry] FOR LOGIN Domain1\WindowsUserBarry`  
-   `CREATE USER [Domain1\WindowsUserBarry] FROM LOGIN Domain1\WindowsUserBarry`  
-   `CREATE USER [Domain1\WindowsGroupManagers]`  
-   `CREATE USER [Domain1\WindowsGroupManagers] FOR LOGIN [Domain1\WindowsGroupManagers]`  
-   `CREATE USER [Domain1\WindowsGroupManagers] FROM LOGIN [Domain1\WindowsGroupManagers]`  
  
**無法驗證的使用者**  
  
 下列清單顯示無法登入 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 之使用者的可能語法。  
  
-   `CREATE USER RIGHTSHOLDER WITHOUT LOGIN`  
-   `CREATE USER CERTUSER FOR CERTIFICATE SpecialCert`  
-   `CREATE USER CERTUSER FROM CERTIFICATE SpecialCert`  
-   `CREATE USER KEYUSER FOR ASYMMETRIC KEY SecureKey`  
-   `CREATE USER KEYUSER FROM ASYMMETRIC KEY SecureKey`  
  
## <a name="security"></a>安全性  
 建立使用者會授與資料庫存取權，但不會自動授與資料庫物件的任何存取權。 在建立使用者之後，一般動作是將使用者新增至有權存取資料庫物件的資料庫角色，或將物件權限授與給使用者。 如需設計權限系統的資訊，請參閱 [資料庫引擎權限使用者入門](../../relational-databases/security/authentication-access/getting-started-with-database-engine-permissions.md)。  
  
### <a name="special-considerations-for-contained-databases"></a>適用於自主資料庫的特殊考量  
 當連接到自主資料庫時，如果使用者不具有 **master** 資料庫的登入，則連接字串必須包含自主資料庫名稱以作為為初始目錄。 具有密碼之自主資料庫使用者永遠需要初始目錄參數。  
  
 在自主資料庫中，建立使用者有助於區隔資料庫與 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 執行個體，以便輕易將資料庫移至另一個 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體。 如需詳細資訊，請參閱[自主資料庫](../../relational-databases/databases/contained-databases.md)和[自主的資料庫使用者 - 使資料庫可攜](../../relational-databases/security/contained-database-users-making-your-database-portable.md)。 若要將資料庫使用者從具有 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 驗證登入的使用者變更為使用密碼的自主資料庫使用者，請參閱 [sp_migrate_user_to_contained &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-migrate-user-to-contained-transact-sql.md)。  
  
 在自主資料庫中，使用者不需具備 **master** 資料庫的登入。 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 管理員應該了解自主資料庫存取權可在資料庫層級授與，而非 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 層級。 如需詳細資訊，請參閱 [Security Best Practices with Contained Databases](../../relational-databases/databases/security-best-practices-with-contained-databases.md)。  
  
 當您使用自主的資料庫使用者時[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]，使用資料庫層級防火牆規則來設定存取，而非使用伺服器層級防火牆規則。 如需詳細資訊，請參閱 [sp_set_database_firewall_rule &#40;Azure SQL Database&#41;](../../relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database.md)。
 
若是 [!INCLUDE[ssSDS_md](../../includes/sssds-md.md)] 和 [!INCLUDE[ssSDW_md](../../includes/sssdw-md.md)] 自主資料庫使用者，SSMS 可支援 Multi-Factor Authentication。 如需詳細資訊，請參閱[適用於與 SQL Database 和 [!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)]搭配使用之 Azure AD MFA 的 SSMS 支援](https://azure.microsoft.com/documentation/articles/sql-database-ssms-mfa-authentication/)。  
  
### <a name="permissions"></a>權限  
 需要資料庫的 ALTER ANY USER 權限。  
  
## <a name="examples"></a>範例  
  
### <a name="a-creating-a-database-user-based-on-a-sql-server-login"></a>A. 依據 SQL Server 登入建立資料庫使用者  
 下列範例會先建立一個名為 `AbolrousHazem` 的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入，然後在 `AbolrousHazem` 建立對應的資料庫使用者 `AdventureWorks2012`。  
  
```sql  
CREATE LOGIN AbolrousHazem   
    WITH PASSWORD = '340$Uuxwp7Mcxo7Khy';  
```   
變更為使用者資料庫。 例如，在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中，使用 `USE AdventureWorks2012` 陳述式。 在 [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 和 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] 中，您必須建立與使用者資料庫之間的新連接。

```sql   
CREATE USER AbolrousHazem FOR LOGIN AbolrousHazem;  
GO   
```  
  
### <a name="b-creating-a-database-user-with-a-default-schema"></a>B. 以預設的結構描述建立資料庫使用者  
 下列範例會先建立一個具有密碼且名叫 `WanidaBenshoof` 的伺服器登入，然後再以預設的結構描述 `Wanida`，建立對應的資料庫使用者 `Marketing`。  
  
```sql  
CREATE LOGIN WanidaBenshoof   
    WITH PASSWORD = '8fdKJl3$nlNv3049jsKK';  
USE AdventureWorks2012;  
CREATE USER Wanida FOR LOGIN WanidaBenshoof   
    WITH DEFAULT_SCHEMA = Marketing;  
GO  
```  
  
### <a name="c-creating-a-database-user-from-a-certificate"></a>C. 從憑證建立資料庫使用者  
 下列範例會從憑證 `JinghaoLiu` 建立一個資料庫使用者 `CarnationProduction50`。  
  
**適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。  
  
```sql  
USE AdventureWorks2012;  
CREATE CERTIFICATE CarnationProduction50  
    WITH SUBJECT = 'Carnation Production Facility Supervisors',  
    EXPIRY_DATE = '11/11/2011';  
GO  
CREATE USER JinghaoLiu FOR CERTIFICATE CarnationProduction50;  
GO   
```  
  
###  <a name="d-creating-and-using-a-user-without-a-login"></a><a name="withoutLogin"></a> D. 建立及使用不含登入的使用者  
 下列範例會建立未對應至 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入的資料庫使用者 `CustomApp`。 然後此範例會授與使用者 `adventure-works\tengiz0` 權限來模擬 `CustomApp` 使用者。  
  
```sql  
USE AdventureWorks2012 ;  
CREATE USER CustomApp WITHOUT LOGIN ;  
GRANT IMPERSONATE ON USER::CustomApp TO [adventure-works\tengiz0] ;  
GO   
```  
  
 若要使用 `CustomApp` 認證，使用者 `adventure-works\tengiz0` 會執行下列陳述式。  
  
```sql  
EXECUTE AS USER = 'CustomApp' ;  
GO  
```  
  
 若要還原回 `adventure-works\tengiz0` 認證，使用者會執行下列陳述式。  
  
```sql  
REVERT ;  
GO  
```  
  
### <a name="e-creating-a-contained-database-user-with-password"></a>E. 建立具有密碼的自主資料庫使用者  
 下列範例會建立具有密碼之自主資料庫使用者。 您只能在自主資料庫中執行這個範例。  
  
**適用對象**：[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 及更新版本。 如果 DEFAULT_LANGUAGE 已移除，則這個範例才能運作[!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)]。  
  
```sql  
USE AdventureWorks2012 ;  
GO  
CREATE USER Carlo  
WITH PASSWORD='RN92piTCh%$!~3K9844 Bl*'  
    , DEFAULT_LANGUAGE=[Brazilian]  
    , DEFAULT_SCHEMA=[dbo]  
GO   
```  
  
### <a name="f-creating-a-contained-database-user-for-a-domain-login"></a>F. 為網域登入建立自主資料庫使用者  
 下列範例會為 Contoso 網域中的登入 Fritz，建立自主資料庫使用者。 您只能在自主資料庫中執行這個範例。  
  
**適用對象**：[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 及更新版本。  
  
```sql  
USE AdventureWorks2012 ;  
GO  
CREATE USER [Contoso\Fritz] ;  
GO   
```  
  
### <a name="g-creating-a-contained-database-user-with-a-specific-sid"></a>G. 建立具有指定 SID 之自主資料庫使用者  
 下列範例會建立名稱為 CarmenW 的 SQL Server 驗證自主資料庫使用者。 您只能在自主資料庫中執行這個範例。  
  
**適用對象**：[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 及更新版本。  
  
```sql  
USE AdventureWorks2012 ;  
GO  
CREATE USER CarmenW WITH PASSWORD = 'a8ea v*(Rd##+'  
, SID = 0x01050000000000090300000063FF0451A9E7664BA705B10E37DDC4B7;
```  
  
### <a name="h-creating-a-user-to-copy-encrypted-data"></a>H. 建立使用者以複製加密的資料  
 下列範例會建立一個使用者，以將受 Always Encrypted 功能保護的資料，從某一組資料表 (包含加密的資料行) 複製到其他組具有加密資料行的資料表 (位於相同或不同的資料庫)。  如需詳細資訊，請參閱[移轉透過 Always Encrypted 保護的機密資料](../../relational-databases/security/encryption/migrate-sensitive-data-protected-by-always-encrypted.md)。  
  
**適用於**：[!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 及更新版本、[!INCLUDE[ssSDS](../../includes/sssds-md.md)]。  
  
```sql  
CREATE USER [Chin]   
WITH   
      DEFAULT_SCHEMA = dbo  
    , ALLOW_ENCRYPTED_VALUE_MODIFICATIONS = ON ;  
```

### <a name="i-create-an-azure-ad-user-from-an-azure-ad-login-in-sql-managed-instance"></a>I. 在 SQL 受控執行個體中從 Azure AD 登入建立 Azure AD 使用者

 若要從 Azure AD 登入來建立 Azure AD 使用者，請使用下列語法。

 使用授與 `sysadmin` 角色的 Azure AD 登入來登入受控執行個體。 下列範例會從登入 bob@contoso.com 來建立 Azure AD 使用者 bob@contoso.com。 此登入是在 [CREATE LOGIN](./create-login-transact-sql.md#examples) 範例中建立。

```sql
CREATE USER [bob@contoso.com] FROM LOGIN [bob@contoso.com];
GO
```

> [!IMPORTANT]
> 從 Azure AD 登入建立 **USER** 時，將 *user_name* 指定為來自 **LOGIN** 的相同 *login_name*。

支援從是群組的 Azure AD 登入，將 Azure AD 使用者建立為群組。

```sql
CREATE USER [AAD group] FROM LOGIN [AAD group];
GO
```

您也可以從是群組的 Azure AD 登入建立 Azure AD 使用者。

```sql
CREATE USER [bob@contoso.com] FROM LOGIN [AAD group];
GO
```

### <a name="j-create-an-azure-ad-user-without-an-aad-login-for-the-database"></a>J. 建立不具資料庫之 AAD 登入的 Azure AD 使用者

下列語法可用來在 SQL 受控執行個體資料庫 (包含的使用者) 中建立 Azure AD 使用者 bob@contoso.com：

```sql
CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
GO
```

## <a name="next-steps"></a>後續步驟  
一旦建立使用者之後，請考慮使用 [ALTER ROLE](../../t-sql/statements/alter-role-transact-sql.md) 陳述式，將使用者新增至資料庫角色。  
您也可以將 [GRANT 物件權限](../../t-sql/statements/grant-object-permissions-transact-sql.md)授與角色，以讓他們存取資料表。 如需 SQL Server 安全性模型的一般資訊，請參閱[權限](../../relational-databases/security/permissions-database-engine.md)。   
  
## <a name="see-also"></a>另請參閱  
 [建立資料庫使用者](../../relational-databases/security/authentication-access/create-a-database-user.md)   
 [sys.database_principals &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-database-principals-transact-sql.md)   
 [ALTER USER &#40;Transact-SQL&#41;](../../t-sql/statements/alter-user-transact-sql.md)   
 [DROP USER &#40;Transact-SQL&#41;](../../t-sql/statements/drop-user-transact-sql.md)   
 [CREATE LOGIN &#40;Transact-SQL&#41;](../../t-sql/statements/create-login-transact-sql.md)   
 [EVENTDATA &#40;Transact-SQL&#41;](../../t-sql/functions/eventdata-transact-sql.md)   
 [自主資料庫](../../relational-databases/databases/contained-databases.md)   
 [使用 Azure Active Directory 驗證連線到 SQL 資料庫](/azure/azure-sql/database/authentication-aad-overview)   
 [資料庫引擎權限使用者入門](../../relational-databases/security/authentication-access/getting-started-with-database-engine-permissions.md)