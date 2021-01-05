---
description: ALTER LOGIN (Transact-SQL)
title: ALTER LOGIN (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 01/10/2020
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- ALTER_LOGIN_TSQL
- ALTER LOGIN
dev_langs:
- TSQL
helpviewer_keywords:
- ALTER LOGIN statement
- change password
- mapping logins [SQL Server]
- logins [SQL Server], modifying
- passwords [SQL Server], modifying
- names [SQL Server], logins
- modifying login accounts
ms.assetid: e247b84e-c99e-4af8-8b50-57586e1cb1c5
author: VanMSFT
ms.author: vanto
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 62fcae210d2fdcef552f7a3f8ae0053a16e8c5c6
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97642215"
---
# <a name="alter-login-transact-sql"></a>ALTER LOGIN (Transact-SQL)

變更 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入帳戶的屬性。

![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)

[!INCLUDE[select-product](../../includes/select-product.md)]

::: moniker range=">=sql-server-2016||>=sql-server-linux-2017"

:::row:::
    :::column:::
        **_\* SQL Server \*_** &nbsp;
    :::column-end:::
    :::column:::
        [SQL Database](alter-login-transact-sql.md?view=azuresqldb-current&preserve-view=true)
    :::column-end:::
    :::column:::
        [SQL 受控執行個體](alter-login-transact-sql.md?view=azuresqldb-mi-current&preserve-view=true)
    :::column-end:::
    :::column:::
        [Azure Synapse<br />Analytics](alter-login-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)
    :::column-end:::
    :::column:::
        [Analytics Platform<br />System (PDW)](alter-login-transact-sql.md?view=aps-pdw-2016&preserve-view=true)
    :::column-end:::
:::row-end:::

&nbsp;

## <a name="sql-server"></a>SQL Server

## <a name="syntax"></a>語法

```syntaxsql
-- Syntax for SQL Server

ALTER LOGIN login_name
    {
    <status_option>
    | WITH <set_option> [ ,... ]
    | <cryptographic_credential_option>
    }
[;]

<status_option> ::=
        ENABLE | DISABLE

<set_option> ::=
    PASSWORD = 'password' | hashed_password HASHED
    [
      OLD_PASSWORD = 'oldpassword'
      | <password_option> [<password_option> ]
    ]
    | DEFAULT_DATABASE = database
    | DEFAULT_LANGUAGE = language
    | NAME = login_name
    | CHECK_POLICY = { ON | OFF }
    | CHECK_EXPIRATION = { ON | OFF }
    | CREDENTIAL = credential_name
    | NO CREDENTIAL

<password_option> ::=
    MUST_CHANGE | UNLOCK
  
<cryptographic_credentials_option> ::=
    ADD CREDENTIAL credential_name
  | DROP CREDENTIAL credential_name
```

## <a name="arguments"></a>引數

*login_name* 指定正在變更的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入名稱。 網域登入必須加上方括號，使用 [domain\user] 格式。

ENABLE | DISABLE 啟用或停用此登入。 停用登入並不會影響已經連接之登入的行為。 (使用 `KILL` 陳述式來終止現有的連線。)已停用的登入會保留其權限，且依然可以模擬。

PASSWORD **='** _password_ **'** 僅適用於 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入。 指定正在變更的登入密碼。 密碼會區分大小寫。

PASSWORD **=** _hashed\_password_ 僅適用於 HASHED 關鍵字。 指定要建立之登入的密碼雜湊值。

> [!IMPORTANT]
> 當登入 (或自主資料庫使用者) 連接並通過驗證時，此連接就會快取有關登入的識別資訊。 若為 Windows 驗證登入，這就包括 Windows 群組中成員資格的相關資訊。 只要維持連接，登入的識別就會維持驗證狀態。 若要強制變更識別 (例如重設密碼或變更 Windows 群組成員資格)，登入必須先登出驗證授權單位 (Windows 或 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)])，然後重新登入。 **系統管理員 (sysadmin)** 固定伺服器角色的成員或任何擁有 **ALTER ANY CONNECTION** 權限的登入都可以使用 **KILL** 命令來結束連接並強制登入重新連接。 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 可以在開啟 [物件總管] 視窗和 [查詢編輯器] 視窗的多個連接時，重複使用連接資訊。 關閉所有連接以強制重新連接。

HASHED 僅適用於 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入。 指定在 PASSWORD 引數之後輸入的密碼已雜湊處理。 如果未選取這個選項，則密碼要儲存至資料庫之前會先雜湊處理。 只有針對兩部伺服器之間的登入同步處理，才應使用這個選項。 請勿使用 HASHED 選項進行例行性地變更密碼。

OLD_PASSWORD **='** _oldpassword_ **'** 僅適用於 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入。 將要指派新密碼之登入的目前密碼。 密碼會區分大小寫。

MUST_CHANGE 僅適用於 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入。 如果包含這個選項，則在第一次使用變更後的登入時，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會提示您輸入更新後的密碼。

DEFAULT_DATABASE **=** _database_ 指定要指派給登入的預設資料庫。

DEFAULT_LANGUAGE **=** _language_ 指定要指派給登入的預設語言。 所有 SQL Database 登入的預設語言都是英文且無法變更。 在 Linux 上，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 上 `sa` 登入的預設語言是英文，但是無法變更它。

NAME = *login_name* 正在重新命名的登入新名稱。 如果這是 Windows 登入，則對應到新名稱的 Windows 主體 SID 必須與 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中之登入相關聯的 SID 相同。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入的新名稱，不能包含反斜線字元 (\\)。

CHECK_EXPIRATION = { ON | **OFF** } 僅適用於 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入。 指定是否應該對這個登入強制執行密碼逾期原則。 預設值是 OFF。

CHECK_POLICY **=** { **ON** | OFF } 僅適用於 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入。 指定應該在這項登入上強制使用執行 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 之電腦的 Windows 密碼原則。 預設值是 ON。

CREDENTIAL = *credential_name* 要對應到 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入的認證名稱。 認證必須已存在於伺服器中。 如需詳細資訊，請參閱[認證](../../relational-databases/security/authentication-access/credentials-database-engine.md)。 認證無法對應至 sa 登入。

NO CREDENTIAL 移除對應至伺服器認證的任何現有登入對應。 如需詳細資訊，請參閱[認證](../../relational-databases/security/authentication-access/credentials-database-engine.md)。

UNLOCK 僅適用於 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入。 指定應該將鎖定的登入解除鎖定。

ADD CREDENTIAL 將可延伸金鑰管理 (EKM) 提供者認證新增到登入中。 如需詳細資訊，請參閱[可延伸金鑰管理 (EKM)](../../relational-databases/security/encryption/extensible-key-management-ekm.md)。

DROP CREDENTIAL 將可延伸金鑰管理 (EKM) 提供者認證從登入移除。 如需詳細資訊，請參閱[可延伸金鑰管理 (EKM)] (../.. /relational-databases/security/encryption/extensible-key-management-ekm.md)。

## <a name="remarks"></a>備註

當 CHECK_POLICY 設定為 ON 時，無法使用 HASHED 引數。

當 CHECK_POLICY 變更為 ON 時，會發生下列行為：

- 密碼記錄會使用目前密碼雜湊的值來初始化。

  當 CHECK_POLICY 變更為 OFF 時，會發生下列行為：

- CHECK_EXPIRATION 也會設為 OFF。
- 會清除密碼記錄。
- 會重設 *lockout_time* 的值。

如果指定 MUST_CHANGE，則 CHECK_EXPIRATION 和 CHECK_POLICY 必須設為 ON。 否則，陳述式便會失敗。

如果 CHECK_POLICY 設為 OFF，CHECK_EXPIRATION 就不可設為 ON。 具有這些選項組合的 ALTER LOGIN 陳述式會失敗。

您無法使用含 DISABLE 引數的 ALTER LOGIN 來拒絕存取 Windows 群組。 例如，ALTER LOGIN [網域\群組] DISABLE 會傳回下列錯誤訊息：

`"Msg 15151, Level 16, State 1, Line 1
"Cannot alter the login '*Domain\Group*', because it does not exist or you do not have permission."`

這是原廠設定。
  
在 [!INCLUDE[ssSDS](../../includes/sssds-md.md)] 中，驗證連線需要登入資料，且伺服器層級防火牆規則會暫時快取在每個資料庫中。 此快取會定期重新整理。 若要重新整理驗證快取，並確定資料庫擁有登入資料表的最新版本，請執行 [DBCC FLUSHAUTHCACHE](../../t-sql/database-console-commands/dbcc-flushauthcache-transact-sql.md)。

## <a name="permissions"></a>權限

需要 ALTER ANY LOGIN 權限。

如果使用 CREDENTIAL 選項，還需要 ALTER ANY CREDENTIAL 權限。

如果變更的登入是 **sysadmin** 固定伺服器角色的成員，或 CONTROL SERVER 權限的被授與者，則在進行下列變更時，也需要 CONTROL SERVER 權限：

- 重設密碼，但不提供舊密碼。
- 啟用 MUST_CHANGE、CHECK_POLICY 或 CHECK_EXPIRATION。
- 變更登入名稱。
- 啟用或停用登入。
- 將登入對應到不同的認證。

主體可以為它自己的登入變更密碼、預設語言和預設資料庫。

## <a name="examples"></a>範例

### <a name="a-enabling-a-disabled-login"></a>A. 啟用已停用的登入

下列範例會啟用登入 `Mary5`。

```sql
ALTER LOGIN Mary5 ENABLE;
```

### <a name="b-changing-the-password-of-a-login"></a>B. 變更登入的密碼

下列範例會將登入 `Mary5` 的密碼變更為增強式密碼。

```sql
ALTER LOGIN Mary5 WITH PASSWORD = '<enterStrongPasswordHere>';
```

### <a name="c-changing-the-password-of-a-login-when-logged-in-as-the-login"></a>C. 在以登入身分登入時，變更登入密碼

如果您嘗試為目前用來登入的登入變更密碼，但沒有 `ALTER ANY LOGIN` 權限，就必須指定 `OLD_PASSWORD` 選項。

```sql
ALTER LOGIN Mary5 WITH PASSWORD = '<enterStrongPasswordHere>' OLD_PASSWORD = '<oldWeakPasswordHere>';
```

### <a name="d-changing-the-name-of-a-login"></a>D. 變更登入的名稱

下列範例會將登入 `Mary5` 的名稱變更為 `John2`。

```sql
ALTER LOGIN Mary5 WITH NAME = John2;
```

### <a name="e-mapping-a-login-to-a-credential"></a>E. 將登入對應到認證

下列範例會將登入 `John2` 對應到認證 `Custodian04`。

```sql
ALTER LOGIN John2 WITH CREDENTIAL = Custodian04;
```

### <a name="f-mapping-a-login-to-an-extensible-key-management-credential"></a>F. 將登入對應到可延伸金鑰管理認證

下列範例會將登入 `Mary5` 對應到 EKM 認證 `EKMProvider1`。

```sql
ALTER LOGIN Mary5
ADD CREDENTIAL EKMProvider1;
GO
```

### <a name="f-unlocking-a-login"></a>F. 解除鎖定登入

若要解除鎖定 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入，請執行下列陳述式，並將 \*\*\*\* 取代為您要的帳戶密碼。

```sql
ALTER LOGIN [Mary5] WITH PASSWORD = '****' UNLOCK ;

GO
```

若要在不變更密碼的情況下解除鎖定登入，請先關閉再開啟 CHECK_POLICY。

```sql
ALTER LOGIN [Mary5] WITH CHECK_POLICY = OFF;
ALTER LOGIN [Mary5] WITH CHECK_POLICY = ON;
GO
```

### <a name="g-changing-the-password-of-a-login-using-hashed"></a>G. 使用 HASHED 變更登入密碼

下列範例會將 `TestUser` 登入密碼變更為已雜湊的值。

```sql
ALTER LOGIN TestUser WITH
PASSWORD = 0x01000CF35567C60BFB41EBDE4CF700A985A13D773D6B45B90900 HASHED ;
GO
```

## <a name="see-also"></a>另請參閱

- [認證](../../relational-databases/security/authentication-access/credentials-database-engine.md)
- [CREATE LOGIN](../../t-sql/statements/create-login-transact-sql.md)
- [DROP LOGIN](../../t-sql/statements/drop-login-transact-sql.md)
- [CREATE CREDENTIAL](../../t-sql/statements/create-credential-transact-sql.md)
- [EVENTDATA](../../t-sql/functions/eventdata-transact-sql.md)
- [可延伸金鑰管理 (EKM)](../../relational-databases/security/encryption/extensible-key-management-ekm.md)

::: moniker-end
::: moniker range="=azuresqldb-current"

:::row:::
    :::column:::
        [SQL Server](alter-login-transact-sql.md?view=sql-server-ver15&preserve-view=true)
    :::column-end:::
    :::column:::
        **_\* SQL Database \*_**
    :::column-end:::
    :::column:::
        [SQL 受控執行個體](alter-login-transact-sql.md?view=azuresqldb-mi-current&preserve-view=true)
    :::column-end:::
    :::column:::
        [Azure Synapse<br />Analytics](alter-login-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)
    :::column-end:::
    :::column:::
        [Analytics Platform<br />System (PDW)](alter-login-transact-sql.md?view=aps-pdw-2016&preserve-view=true)
    :::column-end:::
:::row-end:::

&nbsp;

## <a name="sql-database"></a>SQL Database

## <a name="sql-server"></a>SQL Server

## <a name="syntax"></a>語法

```syntaxsql
-- Syntax for Azure SQL Database

ALTER LOGIN login_name
  {
      <status_option>
    | WITH <set_option> [ ,.. .n ]
  }
[;]

<status_option> ::=
    ENABLE | DISABLE

<set_option> ::=
    PASSWORD ='password'
    [
      OLD_PASSWORD ='oldpassword'
    ]
    | NAME = login_name
```

## <a name="arguments"></a>引數

*login_name* 指定正在變更的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入名稱。 網域登入必須加上方括號，使用 [domain\user] 格式。

ENABLE | DISABLE 啟用或停用此登入。 停用登入並不會影響已經連接之登入的行為。 (使用 `KILL` 陳述式來終止現有的連線。)已停用的登入會保留其權限，且依然可以模擬。

PASSWORD **='** _password_ **'** 僅適用於 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入。 指定正在變更的登入密碼。 密碼會區分大小寫。

持續作用中的 SQL Database 連線至少每 10 小時就需要授權 (由「資料庫引擎」執行)。 「資料庫引擎」會嘗試使用最初提交的密碼重新授權，而且不需要使用者輸入。 基於效能考量，當密碼在 SQL Database 中重設時時，不會重新驗證連線，即使連線因為連線共用而重設。 這和內部部署 SQL Server 的行為不同。 如果自從連線初始授權後密碼已經變更，則必須中斷該連線，然後使用新密碼建立新連線。 具有 KILL DATABASE CONNECTION 權限的使用者可以使用 KILL 命令明確地中斷對 SQL Database 的連線。 如需詳細資訊，請參閱 [KILL](../../t-sql/language-elements/kill-transact-sql.md)。

> [!IMPORTANT]
> 當登入 (或自主資料庫使用者) 連接並通過驗證時，此連接就會快取有關登入的識別資訊。 若為 Windows 驗證登入，這就包括 Windows 群組中成員資格的相關資訊。 只要維持連接，登入的識別就會維持驗證狀態。 若要強制變更識別 (例如重設密碼或變更 Windows 群組成員資格)，登入必須先登出驗證授權單位 (Windows 或 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)])，然後重新登入。 **系統管理員 (sysadmin)** 固定伺服器角色的成員或任何擁有 **ALTER ANY CONNECTION** 權限的登入都可以使用 **KILL** 命令來結束連接並強制登入重新連接。 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 可以在開啟 [物件總管] 視窗和 [查詢編輯器] 視窗的多個連接時，重複使用連接資訊。 關閉所有連接以強制重新連接。

OLD_PASSWORD **='** _oldpassword_ **'** 僅適用於 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入。 將要指派新密碼之登入的目前密碼。 密碼會區分大小寫。

NAME = *login_name* 正在重新命名的登入新名稱。 如果這是 Windows 登入，則對應到新名稱的 Windows 主體 SID 必須與 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中之登入相關聯的 SID 相同。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入的新名稱，不能包含反斜線字元 (\\)。

## <a name="remarks"></a>備註

在 [!INCLUDE[ssSDS](../../includes/sssds-md.md)] 中，驗證連線需要登入資料，且伺服器層級防火牆規則會暫時快取在每個資料庫中。 此快取會定期重新整理。 若要重新整理驗證快取，並確定資料庫擁有登入資料表的最新版本，請執行 [DBCC FLUSHAUTHCACHE](../../t-sql/database-console-commands/dbcc-flushauthcache-transact-sql.md)。

## <a name="permissions"></a>權限

需要 ALTER ANY LOGIN 權限。

如果變更的登入是 **sysadmin** 固定伺服器角色的成員，或 CONTROL SERVER 權限的被授與者，則在進行下列變更時，也需要 CONTROL SERVER 權限：

- 重設密碼，但不提供舊密碼。
- 變更登入名稱。
- 啟用或停用登入。
- 將登入對應到不同的認證。

主體可以變更自己的登入密碼。

## <a name="examples"></a>範例

這些範例也包含使用其他 SQL 產品的範例。 請查看支援上面哪些引數。

### <a name="a-enabling-a-disabled-login"></a>A. 啟用已停用的登入

下列範例會啟用登入 `Mary5`。

```sql
ALTER LOGIN Mary5 ENABLE;
```

### <a name="b-changing-the-password-of-a-login"></a>B. 變更登入的密碼

下列範例會將登入 `Mary5` 的密碼變更為增強式密碼。

```sql
ALTER LOGIN Mary5 WITH PASSWORD = '<enterStrongPasswordHere>';
```

### <a name="c-changing-the-name-of-a-login"></a>C. 變更登入的名稱

下列範例會將登入 `Mary5` 的名稱變更為 `John2`。

```sql
ALTER LOGIN Mary5 WITH NAME = John2;
```

### <a name="d-mapping-a-login-to-a-credential"></a>D. 將登入對應到認證

下列範例會將登入 `John2` 對應到認證 `Custodian04`。

```sql
ALTER LOGIN John2 WITH CREDENTIAL = Custodian04;
```

### <a name="e-mapping-a-login-to-an-extensible-key-management-credential"></a>E. 將登入對應到可延伸金鑰管理認證

下列範例會將登入 `Mary5` 對應到 EKM 認證 `EKMProvider1`。


**適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。

```sql
ALTER LOGIN Mary5
ADD CREDENTIAL EKMProvider1;
GO
```

### <a name="f-unlocking-a-login"></a>F. 解除鎖定登入

若要解除鎖定 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入，請執行下列陳述式，並將 \*\*\*\* 取代為您要的帳戶密碼。

```sql
ALTER LOGIN [Mary5] WITH PASSWORD = '****' UNLOCK ;

GO
```

若要在不變更密碼的情況下解除鎖定登入，請先關閉再開啟 CHECK_POLICY。

```sql
ALTER LOGIN [Mary5] WITH CHECK_POLICY = OFF;
ALTER LOGIN [Mary5] WITH CHECK_POLICY = ON;
GO
```

### <a name="g-changing-the-password-of-a-login-using-hashed"></a>G. 使用 HASHED 變更登入密碼

下列範例會將 `TestUser` 登入密碼變更為已雜湊的值。

**適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。

```sql
ALTER LOGIN TestUser WITH
PASSWORD = 0x01000CF35567C60BFB41EBDE4CF700A985A13D773D6B45B90900 HASHED ;
GO
```

## <a name="see-also"></a>另請參閱

- [認證](../../relational-databases/security/authentication-access/credentials-database-engine.md)
- [CREATE LOGIN](../../t-sql/statements/create-login-transact-sql.md)
- [DROP LOGIN](../../t-sql/statements/drop-login-transact-sql.md)
- [CREATE CREDENTIAL](../../t-sql/statements/create-credential-transact-sql.md)
- [EVENTDATA](../../t-sql/functions/eventdata-transact-sql.md)
- [可延伸金鑰管理 (EKM)](../../relational-databases/security/encryption/extensible-key-management-ekm.md)

::: moniker-end
::: moniker range="=azuresqldb-mi-current"

:::row:::
    :::column:::
        [SQL Server](alter-login-transact-sql.md?view=sql-server-ver15&preserve-view=true)
    :::column-end:::
    :::column:::
        [SQL Database](alter-login-transact-sql.md?view=azuresqldb-current&preserve-view=true)
    :::column-end:::
    :::column:::
        **_\* SQL 受控執行個體\*_**
    :::column-end:::
    :::column:::
        [Azure Synapse<br />Analytics](alter-login-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)
    :::column-end:::
    :::column:::
        [Analytics Platform<br />System (PDW)](alter-login-transact-sql.md?view=aps-pdw-2016&preserve-view=true)
    :::column-end:::
:::row-end:::

&nbsp;

## <a name="azure-sql-managed-instance"></a>Azure SQL 受控執行個體

## <a name="syntax"></a>語法

```syntaxsql
-- Syntax for SQL Server and Azure SQL Managed Instance

ALTER LOGIN login_name
    {
    <status_option>
    | WITH <set_option> [ ,... ]
    | <cryptographic_credential_option>
    }
[;]

<status_option> ::=
        ENABLE | DISABLE
  
<set_option> ::=
    PASSWORD = 'password' | hashed_password HASHED
    [
      OLD_PASSWORD = 'oldpassword'
      | <password_option> [<password_option> ]
    ]
    | DEFAULT_DATABASE = database
    | DEFAULT_LANGUAGE = language
    | NAME = login_name
    | CHECK_POLICY = { ON | OFF }
    | CHECK_EXPIRATION = { ON | OFF }
    | CREDENTIAL = credential_name
    | NO CREDENTIAL

<password_option> ::=
    MUST_CHANGE | UNLOCK

<cryptographic_credentials_option> ::=
    ADD CREDENTIAL credential_name
  | DROP CREDENTIAL credential_name
```

> [!NOTE]
> 建立完成後的受控執行個體 Azure AD 管理員已變更。 如需詳細資訊，請參閱 [MI 的新 Azure AD 管理員功能](/azure/sql-database/sql-database-aad-authentication-configure#new-azure-ad-admin-functionality-for-mi)。

```syntaxsql
-- Syntax for Azure SQL Managed Instance using Azure AD logins

ALTER LOGIN login_name
  {
      <status_option>
    | WITH <set_option> [ ,.. .n ]
  }
[;]

<status_option> ::=
    ENABLE | DISABLE

<set_option> ::=
     DEFAULT_DATABASE = database
   | DEFAULT_LANGUAGE = language
```

## <a name="arguments"></a>引數

### <a name="arguments-applicable-to-sql-and-azure-ad-logins"></a>適用於 SQL 和 Azure AD 登入的引數

*login_name* 指定正在變更的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入名稱。 Azure AD 登入必須指定為 user@domain。 例如 john.smith@contoso.com，或指定為 Azure AD 群組或應用程式名稱。 針對 Azure AD 登入，*login_name* 必須對應至 master 資料庫中建立的現有 Azure AD 登入。

ENABLE | DISABLE 啟用或停用此登入。 停用登入並不會影響已經連接之登入的行為。 (使用 `KILL` 陳述式來終止現有的連線。)已停用的登入會保留其權限，且依然可以模擬。

DEFAULT_DATABASE **=** _database_ 指定要指派給登入的預設資料庫。

DEFAULT_LANGUAGE **=** _language_ 指定要指派給登入的預設語言。 所有 SQL Database 登入的預設語言都是英文且無法變更。 在 Linux 上，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 上 `sa` 登入的預設語言是英文，但是無法變更它。

### <a name="arguments-applicable-only-to-sql-logins"></a>僅適用於 SQL 登入的引數

PASSWORD **='** _password_ **'** 僅適用於 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入。 指定正在變更的登入密碼。 密碼會區分大小寫。 密碼也不適合搭配外部登入使用，例如 Azure AD 登入。

持續作用中的 SQL Database 連線至少每 10 小時就需要授權 (由「資料庫引擎」執行)。 「資料庫引擎」會嘗試使用最初提交的密碼重新授權，而且不需要使用者輸入。 基於效能考量，當密碼在 SQL Database 中重設時時，不會重新驗證連線，即使連線因為連線共用而重設。 這和內部部署 SQL Server 的行為不同。 如果自從連線初始授權後密碼已經變更，則必須中斷該連線，然後使用新密碼建立新連線。 具有 KILL DATABASE CONNECTION 權限的使用者可以使用 KILL 命令明確地中斷對 SQL Database 的連線。 如需詳細資訊，請參閱 [KILL](../../t-sql/language-elements/kill-transact-sql.md)。

PASSWORD **=** _hashed\_password_ 僅適用於 HASHED 關鍵字。 指定要建立之登入的密碼雜湊值。

HASHED 僅適用於 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入。 指定在 PASSWORD 引數之後輸入的密碼已雜湊處理。 如果未選取這個選項，則密碼要儲存至資料庫之前會先雜湊處理。 只有針對兩部伺服器之間的登入同步處理，才應使用這個選項。 請勿使用 HASHED 選項進行例行性地變更密碼。

OLD_PASSWORD **='** _oldpassword_ **'** 僅適用於 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入。 將要指派新密碼之登入的目前密碼。 密碼會區分大小寫。

MUST_CHANGE<br>
只適用於 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入。 如果包含這個選項，則在第一次使用變更後的登入時，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會提示您輸入更新後的密碼。

NAME = *login_name* 正在重新命名的登入新名稱。 如果登入是 Windows 登入，則對應到新名稱的 Windows 主體 SID 必須與 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中登入建立關聯的 SID 相同。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入的新名稱，不能包含反斜線字元 (\\)。

CHECK_EXPIRATION = { ON | **OFF** } 僅適用於 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入。 指定是否應該對這個登入強制執行密碼逾期原則。 預設值是 OFF。

CHECK_POLICY **=** { **ON** | OFF } 僅適用於 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入。 指定應該在這項登入上強制使用執行 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 之電腦的 Windows 密碼原則。 預設值是 ON。

CREDENTIAL = *credential_name* 要對應到 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入的認證名稱。 認證必須已存在於伺服器中。 如需詳細資訊，請參閱[認證](../../relational-databases/security/authentication-access/credentials-database-engine.md)。 認證無法對應至 sa 登入。

NO CREDENTIAL 移除對應至伺服器認證的任何現有登入對應。 如需詳細資訊，請參閱[認證](../../relational-databases/security/authentication-access/credentials-database-engine.md)。

UNLOCK 僅適用於 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入。 指定應該將鎖定的登入解除鎖定。

ADD CREDENTIAL 將可延伸金鑰管理 (EKM) 提供者認證新增到登入中。 如需詳細資訊，請參閱[可延伸金鑰管理 (EKM)](../../relational-databases/security/encryption/extensible-key-management-ekm.md)。

DROP CREDENTIAL 將可延伸金鑰管理 (EKM) 提供者認證從登入移除。 如需詳細資訊，請參閱[可延伸金鑰管理 (EKM)](../../relational-databases/security/encryption/extensible-key-management-ekm.md)。

## <a name="remarks"></a>備註

當 CHECK_POLICY 設定為 ON 時，無法使用 HASHED 引數。

當 CHECK_POLICY 變更為 ON 時，會發生下列行為：

- 密碼記錄會使用目前密碼雜湊的值來初始化。

  當 CHECK_POLICY 變更為 OFF 時，會發生下列行為：

- CHECK_EXPIRATION 也會設為 OFF。
- 會清除密碼記錄。
- 會重設 *lockout_time* 的值。

如果指定 MUST_CHANGE，則 CHECK_EXPIRATION 和 CHECK_POLICY 必須設為 ON。 否則，陳述式便會失敗。

如果 CHECK_POLICY 設為 OFF，CHECK_EXPIRATION 就不可設為 ON。 具有這些選項組合的 ALTER LOGIN 陳述式會失敗。

您無法使用含 DISABLE 引數的 ALTER_LOGIN 來拒絕存取 Windows 群組。 這是原廠設定。 例如，ALTER_LOGIN [*domain\group*] DISABLE 將傳回下列錯誤訊息：

`"Msg 15151, Level 16, State 1, Line 1
"Cannot alter the login '*Domain\Group*', because it does not exist or you do not have permission."`

在 [!INCLUDE[ssSDS](../../includes/sssds-md.md)] 中，驗證連線需要登入資料，且伺服器層級防火牆規則會暫時快取在每個資料庫中。 此快取會定期重新整理。 若要重新整理驗證快取，並確定資料庫擁有登入資料表的最新版本，請執行 [DBCC FLUSHAUTHCACHE](../../t-sql/database-console-commands/dbcc-flushauthcache-transact-sql.md)。

## <a name="permissions"></a>權限

需要 ALTER ANY LOGIN 權限。

如果使用 CREDENTIAL 選項，還需要 ALTER ANY CREDENTIAL 權限。

如果變更的登入是 **sysadmin** 固定伺服器角色的成員，或 CONTROL SERVER 權限的被授與者，則在進行下列變更時，也需要 CONTROL SERVER 權限：

- 重設密碼，但不提供舊密碼。
- 啟用 MUST_CHANGE、CHECK_POLICY 或 CHECK_EXPIRATION。
- 變更登入名稱。
- 啟用或停用登入。
- 將登入對應到不同的認證。

主體可以為它自己的登入變更密碼、預設語言和預設資料庫。

僅具有 `sysadmin` 權限的 SQL 主體可對 Azure AD 登入執行 ALTER LOGIN 命令。

## <a name="examples"></a>範例

這些範例也包含使用其他 SQL 產品的範例。 請查看支援上面哪些引數。

### <a name="a-enabling-a-disabled-login"></a>A. 啟用已停用的登入

下列範例會啟用登入 `Mary5`。

```sql
ALTER LOGIN Mary5 ENABLE;
```

### <a name="b-changing-the-password-of-a-login"></a>B. 變更登入的密碼

下列範例會將登入 `Mary5` 的密碼變更為增強式密碼。

```sql
ALTER LOGIN Mary5 WITH PASSWORD = '<enterStrongPasswordHere>';
```

### <a name="c-changing-the-name-of-a-login"></a>C. 變更登入的名稱

下列範例會將登入 `Mary5` 的名稱變更為 `John2`。

```sql
ALTER LOGIN Mary5 WITH NAME = John2;
```

### <a name="d-mapping-a-login-to-a-credential"></a>D. 將登入對應到認證

下列範例會將登入 `John2` 對應到認證 `Custodian04`。

```sql
ALTER LOGIN John2 WITH CREDENTIAL = Custodian04;
```

### <a name="e-mapping-a-login-to-an-extensible-key-management-credential"></a>E. 將登入對應到可延伸金鑰管理認證

下列範例會將登入 `Mary5` 對應到 EKM 認證 `EKMProvider1`。

**適用於**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 與更新版本，以及 Azure SQL 受控執行個體。

```sql
ALTER LOGIN Mary5
ADD CREDENTIAL EKMProvider1;
GO
```

### <a name="f-unlocking-a-login"></a>F. 解除鎖定登入

若要解除鎖定 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入，請執行下列陳述式，並將 \*\*\*\* 取代為您要的帳戶密碼。

```sql
ALTER LOGIN [Mary5] WITH PASSWORD = '****' UNLOCK ;

GO
```

若要在不變更密碼的情況下解除鎖定登入，請先關閉再開啟 CHECK_POLICY。

```sql
ALTER LOGIN [Mary5] WITH CHECK_POLICY = OFF;
ALTER LOGIN [Mary5] WITH CHECK_POLICY = ON;
GO
```

### <a name="g-changing-the-password-of-a-login-using-hashed"></a>G. 使用 HASHED 變更登入密碼

下列範例會將 `TestUser` 登入密碼變更為已雜湊的值。

**適用於**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 與更新版本，以及 Azure SQL 受控執行個體。

```sql
ALTER LOGIN TestUser WITH
PASSWORD = 0x01000CF35567C60BFB41EBDE4CF700A985A13D773D6B45B90900 HASHED ;
GO
```

### <a name="h-disabling-the-login-of-an-azure-ad-user"></a>H. 停用 Azure AD 使用者的登入

下列範例會停用 Azure AD 使用者 joe@contoso.com 的登入。

```sql
ALTER LOGIN [joe@contoso.com] DISABLE
```

## <a name="see-also"></a>另請參閱

- [認證](../../relational-databases/security/authentication-access/credentials-database-engine.md)
- [CREATE LOGIN](../../t-sql/statements/create-login-transact-sql.md)
- [DROP LOGIN](../../t-sql/statements/drop-login-transact-sql.md)
- [CREATE CREDENTIAL](../../t-sql/statements/create-credential-transact-sql.md)
- [EVENTDATA](../../t-sql/functions/eventdata-transact-sql.md)
- [可延伸金鑰管理 (EKM)](../../relational-databases/security/encryption/extensible-key-management-ekm.md)

::: moniker-end
::: moniker range="=azure-sqldw-latest"

:::row:::
    :::column:::
        [SQL Server](alter-login-transact-sql.md?view=sql-server-ver15&preserve-view=true)
    :::column-end:::
    :::column:::
        [SQL Database](alter-login-transact-sql.md?view=azuresqldb-current&preserve-view=true)
    :::column-end:::
    :::column:::
        [SQL 受控執行個體](alter-login-transact-sql.md?view=azuresqldb-mi-current&preserve-view=true)
    :::column-end:::
    :::column:::
        **_\* Azure Synapse<br />Analytics \*_**
    :::column-end:::
    :::column:::
        [Analytics Platform<br />System (PDW)](alter-login-transact-sql.md?view=aps-pdw-2016&preserve-view=true)
    :::column-end:::
:::row-end:::

&nbsp;

## <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

## <a name="syntax"></a>語法

```syntaxsql
-- Syntax for Azure Synapse

ALTER LOGIN login_name
  {
      <status_option>
    | WITH <set_option> [ ,.. .n ]
  }
[;]

<status_option> ::=
    ENABLE | DISABLE
  
<set_option> ::=
    PASSWORD ='password'
    [
      OLD_PASSWORD ='oldpassword'
    ]
    | NAME = login_name
```

## <a name="arguments"></a>引數

*login_name* 指定正在變更的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入名稱。 網域登入必須加上方括號，使用 [domain\user] 格式。

ENABLE | DISABLE 啟用或停用此登入。 停用登入並不會影響已經連接之登入的行為。 (使用 `KILL` 陳述式來終止現有的連線。)已停用的登入會保留其權限，且依然可以模擬。

PASSWORD **='** _password_ **'** 僅適用於 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入。 指定正在變更的登入密碼。 密碼會區分大小寫。

持續作用中的 SQL Database 連線至少每 10 小時就需要授權 (由「資料庫引擎」執行)。 「資料庫引擎」會嘗試使用最初提交的密碼重新授權，而且不需要使用者輸入。 基於效能考量，當密碼在 SQL Database 中重設時時，不會重新驗證連線，即使連線因為連線共用而重設。 這和內部部署 SQL Server 的行為不同。 如果自從連線初始授權後密碼已經變更，則必須中斷該連線，然後使用新密碼建立新連線。 具有 KILL DATABASE CONNECTION 權限的使用者可以使用 KILL 命令明確地中斷對 SQL Database 的連線。 如需詳細資訊，請參閱 [KILL](../../t-sql/language-elements/kill-transact-sql.md)。

> [!IMPORTANT]
> 當登入 (或自主資料庫使用者) 連接並通過驗證時，此連接就會快取有關登入的識別資訊。 若為 Windows 驗證登入，這就包括 Windows 群組中成員資格的相關資訊。 只要維持連接，登入的識別就會維持驗證狀態。 若要強制變更識別 (例如重設密碼或變更 Windows 群組成員資格)，登入必須先登出驗證授權單位 (Windows 或 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)])，然後重新登入。 **系統管理員 (sysadmin)** 固定伺服器角色的成員或任何擁有 **ALTER ANY CONNECTION** 權限的登入都可以使用 **KILL** 命令來結束連接並強制登入重新連接。 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 可以在開啟 [物件總管] 視窗和 [查詢編輯器] 視窗的多個連接時，重複使用連接資訊。 關閉所有連接以強制重新連接。

OLD_PASSWORD **='** _oldpassword_ **'** 僅適用於 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入。 將要指派新密碼之登入的目前密碼。 密碼會區分大小寫。

NAME = *login_name* 正在重新命名的登入新名稱。 如果這是 Windows 登入，則對應到新名稱的 Windows 主體 SID 必須與 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中之登入相關聯的 SID 相同。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入的新名稱，不能包含反斜線字元 (\\)。

## <a name="remarks"></a>備註

在 [!INCLUDE[ssSDS](../../includes/sssds-md.md)] 中，驗證連線需要登入資料，且伺服器層級防火牆規則會暫時快取在每個資料庫中。 此快取會定期重新整理。 若要重新整理驗證快取，並確定資料庫擁有登入資料表的最新版本，請執行 [DBCC FLUSHAUTHCACHE](../../t-sql/database-console-commands/dbcc-flushauthcache-transact-sql.md)。

## <a name="permissions"></a>權限

需要 ALTER ANY LOGIN 權限。

如果變更的登入是 **sysadmin** 固定伺服器角色的成員，或 CONTROL SERVER 權限的被授與者，則在進行下列變更時，也需要 CONTROL SERVER 權限：

- 重設密碼，但不提供舊密碼。
- 變更登入名稱。
- 啟用或停用登入。
- 將登入對應到不同的認證。

主體可以變更自己的登入密碼。

## <a name="examples"></a>範例

這些範例也包含使用其他 SQL 產品的範例。 請查看支援上面哪些引數。

### <a name="a-enabling-a-disabled-login"></a>A. 啟用已停用的登入

下列範例會啟用登入 `Mary5`。

```sql
ALTER LOGIN Mary5 ENABLE;
```

### <a name="b-changing-the-password-of-a-login"></a>B. 變更登入的密碼

下列範例會將登入 `Mary5` 的密碼變更為增強式密碼。

```sql
ALTER LOGIN Mary5 WITH PASSWORD = '<enterStrongPasswordHere>';
```

### <a name="c-changing-the-name-of-a-login"></a>C. 變更登入的名稱

下列範例會將登入 `Mary5` 的名稱變更為 `John2`。

```sql
ALTER LOGIN Mary5 WITH NAME = John2;
```

### <a name="d-mapping-a-login-to-a-credential"></a>D. 將登入對應到認證

下列範例會將登入 `John2` 對應到認證 `Custodian04`。

```sql
ALTER LOGIN John2 WITH CREDENTIAL = Custodian04;
```

### <a name="e-mapping-a-login-to-an-extensible-key-management-credential"></a>E. 將登入對應到可延伸金鑰管理認證

下列範例會將登入 `Mary5` 對應到 EKM 認證 `EKMProvider1`。

**適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。

```sql
ALTER LOGIN Mary5
ADD CREDENTIAL EKMProvider1;
GO
```

### <a name="f-unlocking-a-login"></a>F. 解除鎖定登入

若要解除鎖定 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入，請執行下列陳述式，並將 \*\*\*\* 取代為您要的帳戶密碼。

```sql
ALTER LOGIN [Mary5] WITH PASSWORD = '****' UNLOCK ;

GO
```

若要在不變更密碼的情況下解除鎖定登入，請先關閉再開啟 CHECK_POLICY。

```sql
ALTER LOGIN [Mary5] WITH CHECK_POLICY = OFF;
ALTER LOGIN [Mary5] WITH CHECK_POLICY = ON;
GO
```

### <a name="g-changing-the-password-of-a-login-using-hashed"></a>G. 使用 HASHED 變更登入密碼

下列範例會將 `TestUser` 登入密碼變更為已雜湊的值。

**適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。

```sql
ALTER LOGIN TestUser WITH
PASSWORD = 0x01000CF35567C60BFB41EBDE4CF700A985A13D773D6B45B90900 HASHED ;
GO
```

## <a name="see-also"></a>另請參閱

- [認證](../../relational-databases/security/authentication-access/credentials-database-engine.md)
- [CREATE LOGIN](../../t-sql/statements/create-login-transact-sql.md)
- [DROP LOGIN](../../t-sql/statements/drop-login-transact-sql.md)
- [CREATE CREDENTIAL](../../t-sql/statements/create-credential-transact-sql.md)
- [EVENTDATA](../../t-sql/functions/eventdata-transact-sql.md)
- [可延伸金鑰管理 (EKM)](../../relational-databases/security/encryption/extensible-key-management-ekm.md)

::: moniker-end
::: moniker range=">=aps-pdw-2016"

:::row:::
    :::column:::
        [SQL Server](alter-login-transact-sql.md?view=sql-server-ver15&preserve-view=true)
    :::column-end:::
    :::column:::
        [SQL Database](alter-login-transact-sql.md?view=azuresqldb-current&preserve-view=true)
    :::column-end:::
    :::column:::
        [SQL 受控執行個體](alter-login-transact-sql.md?view=azuresqldb-mi-current&preserve-view=true)
    :::column-end:::
    :::column:::
        [Azure Synapse<br />Analytics](alter-login-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)
    :::column-end:::
    :::column:::
        **_\* Analytics<br />Platform System (PDW) \*_**
    :::column-end:::
:::row-end:::

&nbsp;

## <a name="analytics-platform-system"></a>分析平台系統

## <a name="syntax"></a>語法

```syntaxsql
-- Syntax for Analytics Platform System

ALTER LOGIN login_name
    {
    <status_option>
    | WITH <set_option> [ ,... ]
    }

<status_option> ::=ENABLE | DISABLE

<set_option> ::=
    PASSWORD ='password'
    [
      OLD_PASSWORD ='oldpassword'
      | <password_option> [<password_option> ]
    ]
    | NAME = login_name
    | CHECK_POLICY = { ON | OFF }
    | CHECK_EXPIRATION = { ON | OFF }

<password_option> ::=
    MUST_CHANGE | UNLOCK
```

## <a name="arguments"></a>引數

*login_name* 指定正在變更的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入名稱。 網域登入必須加上方括號，使用 [domain\user] 格式。

ENABLE | DISABLE 啟用或停用此登入。 停用登入並不會影響已經連接之登入的行為。 (使用 `KILL` 陳述式來終止現有的連線。)已停用的登入會保留其權限，且依然可以模擬。

PASSWORD **='** _password_ **'** 僅適用於 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入。 指定正在變更的登入密碼。 密碼會區分大小寫。

> [!IMPORTANT]
> 當登入 (或自主資料庫使用者) 連接並通過驗證時，此連接就會快取有關登入的識別資訊。 若為 Windows 驗證登入，這就包括 Windows 群組中成員資格的相關資訊。 只要維持連接，登入的識別就會維持驗證狀態。 若要強制變更識別 (例如重設密碼或變更 Windows 群組成員資格)，登入必須先登出驗證授權單位 (Windows 或 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)])，然後重新登入。 **系統管理員 (sysadmin)** 固定伺服器角色的成員或任何擁有 **ALTER ANY CONNECTION** 權限的登入都可以使用 **KILL** 命令來結束連接並強制登入重新連接。 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 可以在開啟 [物件總管] 視窗和 [查詢編輯器] 視窗的多個連接時，重複使用連接資訊。 關閉所有連接以強制重新連接。

OLD_PASSWORD **='** _oldpassword_ **'** 僅適用於 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入。 將要指派新密碼之登入的目前密碼。 密碼會區分大小寫。

MUST_CHANGE 僅適用於 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入。 如果包含這個選項，則在第一次使用變更後的登入時，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會提示您輸入更新後的密碼。

NAME = *login_name* 正在重新命名的登入新名稱。 如果登入是 Windows 登入，則對應到新名稱的 Windows 主體 SID 必須與 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中登入建立關聯的 SID 相同。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入的新名稱，不能包含反斜線字元 (\\)。

CHECK_EXPIRATION = { ON | **OFF** } 僅適用於 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入。 指定是否應該對這個登入強制執行密碼逾期原則。 預設值是 OFF。

CHECK_POLICY **=** { **ON** | OFF } 僅適用於 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入。 指定應該在這項登入上強制使用執行 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 之電腦的 Windows 密碼原則。 預設值是 ON。

UNLOCK 僅適用於 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入。 指定應該將鎖定的登入解除鎖定。

## <a name="remarks"></a>備註

當 CHECK_POLICY 設定為 ON 時，無法使用 HASHED 引數。

當 CHECK_POLICY 變更為 ON 時，會發生下列行為：

- 密碼記錄會使用目前密碼雜湊的值來初始化。

  當 CHECK_POLICY 變更為 OFF 時，會發生下列行為：

- CHECK_EXPIRATION 也會設為 OFF。
- 會清除密碼記錄。
- 會重設 *lockout_time* 的值。

如果指定 MUST_CHANGE，則 CHECK_EXPIRATION 和 CHECK_POLICY 必須設為 ON。 否則，陳述式便會失敗。

如果 CHECK_POLICY 設為 OFF，CHECK_EXPIRATION 就不可設為 ON。 具有這些選項組合的 ALTER LOGIN 陳述式會失敗。

您無法使用含 DISABLE 引數的 ALTER_LOGIN 來拒絕存取 Windows 群組。 這是原廠設定。 例如，ALTER_LOGIN [*domain\group*] DISABLE 將傳回下列錯誤訊息：

`"Msg 15151, Level 16, State 1, Line 1
"Cannot alter the login '*Domain\Group*', because it does not exist or you do not have permission."`

在 [!INCLUDE[ssSDS](../../includes/sssds-md.md)] 中，驗證連線需要登入資料，且伺服器層級防火牆規則會暫時快取在每個資料庫中。 此快取會定期重新整理。 若要重新整理驗證快取，並確定資料庫擁有登入資料表的最新版本，請執行 [DBCC FLUSHAUTHCACHE](../../t-sql/database-console-commands/dbcc-flushauthcache-transact-sql.md)。

## <a name="permissions"></a>權限

需要 ALTER ANY LOGIN 權限。

如果使用 CREDENTIAL 選項，還需要 ALTER ANY CREDENTIAL 權限。

如果變更的登入是 **sysadmin** 固定伺服器角色的成員，或 CONTROL SERVER 權限的被授與者，則在進行下列變更時，也需要 CONTROL SERVER 權限：

- 重設密碼，但不提供舊密碼。
- 啟用 MUST_CHANGE、CHECK_POLICY 或 CHECK_EXPIRATION。
- 變更登入名稱。
- 啟用或停用登入。
- 將登入對應到不同的認證。

主體可以為它自己的登入變更密碼、預設語言和預設資料庫。

## <a name="examples"></a>範例

這些範例也包含使用其他 SQL 產品的範例。 請查看支援上面哪些引數。

### <a name="a-enabling-a-disabled-login"></a>A. 啟用已停用的登入

下列範例會啟用登入 `Mary5`。

```sql
ALTER LOGIN Mary5 ENABLE;
```

### <a name="b-changing-the-password-of-a-login"></a>B. 變更登入的密碼

下列範例會將登入 `Mary5` 的密碼變更為增強式密碼。

```sql
ALTER LOGIN Mary5 WITH PASSWORD = '<enterStrongPasswordHere>';
```

### <a name="c-changing-the-name-of-a-login"></a>C. 變更登入的名稱

下列範例會將登入 `Mary5` 的名稱變更為 `John2`。

```sql
ALTER LOGIN Mary5 WITH NAME = John2;
```

### <a name="d-mapping-a-login-to-a-credential"></a>D. 將登入對應到認證

下列範例會將登入 `John2` 對應到認證 `Custodian04`。

```sql
ALTER LOGIN John2 WITH CREDENTIAL = Custodian04;
```

### <a name="e-mapping-a-login-to-an-extensible-key-management-credential"></a>E. 將登入對應到可延伸金鑰管理認證

下列範例會將登入 `Mary5` 對應到 EKM 認證 `EKMProvider1`。

**適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。

```sql
ALTER LOGIN Mary5
ADD CREDENTIAL EKMProvider1;
GO
```

### <a name="f-unlocking-a-login"></a>F. 解除鎖定登入

若要解除鎖定 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入，請執行下列陳述式，並將 \*\*\*\* 取代為您要的帳戶密碼。

```sql
ALTER LOGIN [Mary5] WITH PASSWORD = '****' UNLOCK ;

GO
```

若要在不變更密碼的情況下解除鎖定登入，請先關閉再開啟 CHECK_POLICY。

```sql
ALTER LOGIN [Mary5] WITH CHECK_POLICY = OFF;
ALTER LOGIN [Mary5] WITH CHECK_POLICY = ON;
GO
```

### <a name="g-changing-the-password-of-a-login-using-hashed"></a>G. 使用 HASHED 變更登入密碼

下列範例會將 `TestUser` 登入密碼變更為已雜湊的值。

**適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。

```sql
ALTER LOGIN TestUser WITH
PASSWORD = 0x01000CF35567C60BFB41EBDE4CF700A985A13D773D6B45B90900 HASHED ;
GO
```

## <a name="see-also"></a>另請參閱

- [認證](../../relational-databases/security/authentication-access/credentials-database-engine.md)
- [CREATE LOGIN](../../t-sql/statements/create-login-transact-sql.md)
- [DROP LOGIN](../../t-sql/statements/drop-login-transact-sql.md)
- [CREATE CREDENTIAL](../../t-sql/statements/create-credential-transact-sql.md)
- [EVENTDATA](../../t-sql/functions/eventdata-transact-sql.md)
- [可延伸金鑰管理 (EKM)](../../relational-databases/security/encryption/extensible-key-management-ekm.md)

::: moniker-end
