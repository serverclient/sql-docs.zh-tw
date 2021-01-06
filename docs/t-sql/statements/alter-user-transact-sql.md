---
description: ALTER USER (Transact-SQL)
title: ALTER USER (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 01/10/2020
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- ALTER_USER_TSQL
- ALTER USER
dev_langs:
- TSQL
helpviewer_keywords:
- modifying database users
- ALTER USER statement
- renaming databases
- schemas [SQL Server], default
- database user names [SQL Server]
- names [SQL Server], database users
- default schemas
- modifying default schemas
ms.assetid: 344fc6ce-a008-47c8-a02e-47fae66cc590
author: VanMSFT
ms.author: vanto
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: ff06180d1abb67239407d91c564558e4d45c2550
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97644414"
---
# <a name="alter-user-transact-sql"></a>ALTER USER (Transact-SQL)

重新命名資料庫使用者或變更其預設結構描述。

 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)

[!INCLUDE[select-product](../../includes/select-product.md)]

::: moniker range=">=sql-server-2016||>=sql-server-linux-2017"

:::row:::
    :::column:::
        **_\* SQL Server \*_** &nbsp;
    :::column-end:::
    :::column:::
        [SQL Database](alter-user-transact-sql.md?view=azuresqldb-current&preserve-view=true)
    :::column-end:::
    :::column:::
        [SQL 受控執行個體](alter-user-transact-sql.md?view=azuresqldb-mi-current&preserve-view=true)
    :::column-end:::
    :::column:::
        [Azure Synapse<br />Analytics](alter-user-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)
    :::column-end:::
    :::column:::
        [Analytics Platform<br />System (PDW)](alter-user-transact-sql.md?view=aps-pdw-2016&preserve-view=true)
    :::column-end:::
:::row-end:::

&nbsp;

## <a name="sql-server"></a>SQL Server

## <a name="syntax"></a>語法

```syntaxsql
-- Syntax for SQL Server

ALTER USER userName
 WITH <set_item> [ ,...n ]
[;]

<set_item> ::=
NAME = newUserName
| DEFAULT_SCHEMA = { schemaName | NULL }
| LOGIN = loginName
| PASSWORD = 'password' [ OLD_PASSWORD = 'oldpassword' ]
| DEFAULT_LANGUAGE = { NONE | <lcid> | <language name> | <language alias> }
| ALLOW_ENCRYPTED_VALUE_MODIFICATIONS = [ ON | OFF ]
```

## <a name="arguments"></a>引數

 *userName* 指定在這個資料庫內用來識別使用者的名稱。

 LOGIN **=** _loginName_ 將使用者的安全性識別碼 (SID) 變更為符合登入的 SID，以便將使用者重新對應到另一個登入。

 NAME **=** _newUserName_ 指定新的名稱給這個使用者。 *newUserName* 不可已存在目前資料庫中。

 DEFAULT_SCHEMA **=** { *schemaName* | NULL } 指定當伺服器解析這個使用者的物件名稱時，將搜尋的第一個結構描述。 當預設結構描述設為 NULL 時，會從 Windows 群組移除預設結構描述。 NULL 選項不可用於 Windows 使用者。

 PASSWORD **=** '*password*'  **適用於**：[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 及更新版本、[!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)]。

 指定正在變更之使用者的密碼。 密碼會區分大小寫。

> [!NOTE]
> 只有包含的使用者能夠使用此選項。 如需詳細資訊，請參閱[自主資料庫](../../relational-databases/databases/contained-databases.md)和 [sp_migrate_user_to_contained &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-migrate-user-to-contained-transact-sql.md)。

 OLD_PASSWORD **=** _'oldpassword'_ **適用於**：[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 及更新版本、[!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)]。

 目前的使用者密碼，將由 '*password*' 取代。 密碼會區分大小寫。 密碼區分大小寫。除非您有 **ALTER ANY USER** 權限，否則需要 *OLD_PASSWORD* 才能變更密碼。 需要 *OLD_PASSWORD* 可防止具有 **IMPERSONATION** 權限的使用者變更密碼。

> [!NOTE]
> 只有包含的使用者能夠使用此選項。

 DEFAULT_LANGUAGE **=** _{ NONE \| \<lcid> \| \<language name> \| \<language alias> }_ **適用對象**：[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)]及更新版本。

 指定要指派給使用者的預設語言。 如果這個選項設為 NONE，預設語言將設為資料庫的目前預設語言。 如果稍後變更了資料庫的預設語言，使用者的預設語言會保持不變。 *DEFAULT_LANGUAGE* 可以是本機識別碼 (lcid)、語言名稱或語言別名。

> [!NOTE]
> 這個選項只能指定在自主資料庫中，而且只適用於包含的使用者。

 ALLOW_ENCRYPTED_VALUE_MODIFICATIONS = [ ON | **OFF** ]  **適用於**：[!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 及更新版本、[!INCLUDE[ssSDS](../../includes/sssds-md.md)]。

 在大量複製作業時隱藏伺服器上的密碼編譯中繼資料檢查。 這會讓使用者得以在資料表或資料庫間大量複製加密資料，而無須解密資料。 預設值為 OFF。

> [!WARNING]
> 不當使用這個選項會導致資料損毀。 如需詳細資訊，請參閱[移轉透過 Always Encrypted 保護的機密資料](../../relational-databases/security/encryption/migrate-sensitive-data-protected-by-always-encrypted.md)。

## <a name="remarks"></a>備註

 預設結構描述是伺服器在解析這個資料庫使用者之物件名稱時，所搜尋到的第一個結構描述。 除非另有指定，否則預設結構描述是此資料庫使用者建立之物件的擁有者。

 如果使用者有預設結構描述，則將會使用預設結構描述。 如果使用者沒有預設結構描述，但使用者是具有預設結構描述群組的成員，則會使用群組的預設結構描述。 如果使用者沒有預設結構描述，且是具有一個以上群組的成員，則使用者預設結構描述將會是具有最低 principle_id 且明確設定預設結構描述 Windows 群組的結構描述。 如果無法判斷使用者的預設結構描述，即會使用 **dbo** 結構描述。

 DEFAULT_SCHEMA 可設為目前不存在於資料庫中的結構描述。 因此，您可以在建立該結構描述之前，先將 DEFAULT_SCHEMA 指派給使用者。

 DEFAULT_SCHEMA 不能指定給對應至憑證或非對稱金鑰的使用者。

> [!IMPORTANT]
> 如果使用者是 **sysadmin** 固定伺服器角色的成員，則會忽略 DEFAULT_SCHEMA 的值。 **sysadmin** 固定伺服器角色的所有成員都有預設的 `dbo` 結構描述。

 當新使用者名稱的 SID 符合資料庫所記錄的 SID 時，您可以變更對應至 Windows 登入或群組的使用者名稱。 這項檢查可防止資料庫中詐騙的 Windows 登入。

 WITH LOGIN 子句可讓使用者重新對應到另一個登入。 沒有登入的使用者、對應至憑證或對應至非對稱金鑰的使用者，都無法使用這個子句來重新對應。 只有 SQL 使用者和 Windows 使用者 (或群組) 才能重新對應。 WITH LOGIN 子句無法用來變更使用者的類型，例如將 Windows 帳戶變更為 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入。

 如果下列條件成立，使用者的名稱將會自動重新命名為登入名稱。

- 使用者是 Windows 使用者。

- 名稱是 Windows 名稱 (包含反斜線)。

- 沒有指定任何新的名稱。

- 目前的名稱不同於登入名稱。

 否則，除非呼叫者額外叫用 NAME 子句，否則使用者將不會重新命名。

對應至 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入、憑證或非對稱金鑰的使用者名稱不可包含反斜線字元 (\\)。

> [!CAUTION]
> [!INCLUDE[ssCautionUserSchema](../../includes/sscautionuserschema-md.md)]

## <a name="security"></a>安全性

> [!NOTE]
> 具有 **ALTER ANY USER** 權限的使用者可以變更任何使用者的預設結構描述。 結構描述變更過的使用者可能會在不知情的情況下，從錯誤的資料表選取資料或從錯誤的結構描述執行程式碼。

### <a name="permissions"></a>權限

 若要變更使用者的名稱，需要具有 **ALTER ANY USER** 權限。

 若要變更使用者的目標登入，則需要資料庫的 **CONTROL** 權限。

 若要變更擁有資料庫 **CONTROL** 權限之使用者的使用者名稱，則需要資料庫的 **CONTROL** 權限。

 若要變更預設結構描述或語言，需要使用者的 **ALTER** 權限。 使用者能夠變更他們自己的預設結構描述或語言。

## <a name="examples"></a>範例

所有的範例會在使用者資料庫中執行。

### <a name="a-changing-the-name-of-a-database-user"></a>A. 變更資料庫使用者的名稱

 下列範例會將資料庫使用者名稱 `Mary5` 變更為 `Mary51`。

```sql
ALTER USER Mary5 WITH NAME = Mary51;
GO
```

### <a name="b-changing-the-default-schema-of-a-user"></a>B. 變更使用者的預設結構描述

 下列範例會將使用者 `Mary51` 的預設結構描述變更為 `Purchasing`。

```sql
ALTER USER Mary51 WITH DEFAULT_SCHEMA = Purchasing;
GO
```

### <a name="c-changing-several-options-at-once"></a>C. 一次變更數個選項

 下列範例會在一個陳述式中變更自主資料庫使用者的數個選項。

**適用對象**：[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 及更新版本。

```sql
ALTER USER Philip
WITH NAME = Philipe
, DEFAULT_SCHEMA = Development
, PASSWORD = 'W1r77TT98%ab@#' OLD_PASSWORD = 'New Devel0per'
, DEFAULT_LANGUAGE= French ;
GO
```

## <a name="see-also"></a>另請參閱

- [CREATE USER &#40;Transact-SQL&#41;](../../t-sql/statements/create-user-transact-sql.md)
- [DROP USER &#40;Transact-SQL&#41;](../../t-sql/statements/drop-user-transact-sql.md)
- [自主資料庫](../../relational-databases/databases/contained-databases.md)
- [EVENTDATA &#40;Transact-SQL&#41;](../../t-sql/functions/eventdata-transact-sql.md)
- [sp_migrate_user_to_contained &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-migrate-user-to-contained-transact-sql.md)

::: moniker-end
::: moniker range="=azuresqldb-current"

:::row:::
    :::column:::
        [SQL Server](alter-user-transact-sql.md?view=sql-server-ver15&preserve-view=true)
    :::column-end:::
    :::column:::
        **_\* SQL Database \*_**
    :::column-end:::
    :::column:::
        [SQL 受控執行個體](alter-user-transact-sql.md?view=azuresqldb-mi-current&preserve-view=true)
    :::column-end:::
    :::column:::
        [Azure Synapse<br />Analytics](alter-user-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)
    :::column-end:::
    :::column:::
        [Analytics Platform<br />System (PDW)](alter-user-transact-sql.md?view=aps-pdw-2016&preserve-view=true)
    :::column-end:::
:::row-end:::

&nbsp;

## <a name="sql-database"></a>SQL Database

## <a name="syntax"></a>語法

```syntaxsql
-- Syntax for Azure SQL Database

ALTER USER userName
 WITH <set_item> [ ,...n ]

<set_item> ::=
NAME = newUserName
| DEFAULT_SCHEMA = schemaName
| LOGIN = loginName
| ALLOW_ENCRYPTED_VALUE_MODIFICATIONS = [ ON | OFF ]
[;]

-- Azure SQL Database Update Syntax
ALTER USER userName
 WITH <set_item> [ ,...n ]
[;]

<set_item> ::=
NAME = newUserName
| DEFAULT_SCHEMA = { schemaName | NULL }
| LOGIN = loginName
| PASSWORD = 'password' [ OLD_PASSWORD = 'oldpassword' ]
| ALLOW_ENCRYPTED_VALUE_MODIFICATIONS = [ ON | OFF ]

-- SQL Database syntax when connected to a federation member
ALTER USER userName
 WITH <set_item> [ ,... n ]
[;]

<set_item> ::=
 NAME = newUserName
```

## <a name="arguments"></a>引數

 *userName* 指定在這個資料庫內用來識別使用者的名稱。

 LOGIN **=** _loginName_ 將使用者的安全性識別碼 (SID) 變更為符合登入的 SID，以便將使用者重新對應到另一個登入。

 如果 ALTER USER 陳述式是 SQL 批次中的唯一陳述式，Azure SQL Database 會支援 WITH LOGIN 子句。 如果 ALTER USER 陳述式不是 SQL 批次中的唯一陳述式或在動態 SQL 中執行，則不支援 WITH LOGIN 子句。

 NAME **=** _newUserName_ 指定新的名稱給這個使用者。 *newUserName* 不可已存在目前資料庫中。

 DEFAULT_SCHEMA **=** { *schemaName* | NULL } 指定當伺服器解析這個使用者的物件名稱時，將搜尋的第一個結構描述。 當預設結構描述設為 NULL 時，會從 Windows 群組移除預設結構描述。 NULL 選項不可用於 Windows 使用者。

 PASSWORD **=** '*password*'  **適用於**：[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 及更新版本、[!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)]。

 指定正在變更之使用者的密碼。 密碼會區分大小寫。

> [!NOTE]
> 只有包含的使用者能夠使用此選項。 如需詳細資訊，請參閱[自主資料庫](../../relational-databases/databases/contained-databases.md)和 [sp_migrate_user_to_contained &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-migrate-user-to-contained-transact-sql.md)。

 OLD_PASSWORD **=** _'oldpassword'_ **適用於**：[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 及更新版本、[!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)]。

 目前的使用者密碼，將由 '*password*' 取代。 密碼會區分大小寫。 密碼區分大小寫。除非您有 **ALTER ANY USER** 權限，否則需要 *OLD_PASSWORD* 才能變更密碼。 需要 *OLD_PASSWORD* 可防止具有 **IMPERSONATION** 權限的使用者變更密碼。

> [!NOTE]
> 只有包含的使用者能夠使用此選項。

 ALLOW_ENCRYPTED_VALUE_MODIFICATIONS = [ ON | **OFF** ]  **適用於**：[!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 及更新版本、[!INCLUDE[ssSDS](../../includes/sssds-md.md)]。

 在大量複製作業時隱藏伺服器上的密碼編譯中繼資料檢查。 這會讓使用者得以在資料表或資料庫間大量複製加密資料，而無須解密資料。 預設值為 OFF。

> [!WARNING]
> 不當使用這個選項會導致資料損毀。 如需詳細資訊，請參閱[移轉透過 Always Encrypted 保護的機密資料](../../relational-databases/security/encryption/migrate-sensitive-data-protected-by-always-encrypted.md)。

## <a name="remarks"></a>備註

 預設結構描述是伺服器在解析這個資料庫使用者之物件名稱時，所搜尋到的第一個結構描述。 除非另有指定，否則預設結構描述是此資料庫使用者建立之物件的擁有者。

 如果使用者有預設結構描述，則使用預設結構描述。 如果使用者沒有預設結構描述，但使用者是具有預設結構描述群組的成員，則會使用群組的預設結構描述。 如果使用者沒有預設結構描述，且是具有一個以上群組的成員，則使用者預設結構描述將會是具有最低 principle_id 且明確設定預設結構描述 Windows 群組的結構描述。 如果無法判斷使用者的預設結構描述，即會使用 **dbo** 結構描述。

 DEFAULT_SCHEMA 可設為目前不存在於資料庫中的結構描述。 因此，您可以在建立該結構描述之前，先將 DEFAULT_SCHEMA 指派給使用者。

 DEFAULT_SCHEMA 不能指定給對應至憑證或非對稱金鑰的使用者。

> [!IMPORTANT]
> 如果使用者是 **sysadmin** 固定伺服器角色的成員，則會忽略 DEFAULT_SCHEMA 的值。 **sysadmin** 固定伺服器角色的所有成員都有預設的 `dbo` 結構描述。

 當新使用者名稱的 SID 符合資料庫所記錄的 SID 時，您可以變更對應至 Windows 登入或群組的使用者名稱。 這項檢查可防止資料庫中詐騙的 Windows 登入。

 WITH LOGIN 子句可讓使用者重新對應到另一個登入。 沒有登入的使用者、對應至憑證或對應至非對稱金鑰的使用者，都無法使用這個子句來重新對應。 只有 SQL 使用者和 Windows 使用者 (或群組) 才能重新對應。 WITH LOGIN 子句無法用來變更使用者的類型，例如將 Windows 帳戶變更為 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入。

 如果下列條件成立，使用者的名稱將會自動重新命名為登入名稱。

- 使用者是 Windows 使用者。

- 名稱是 Windows 名稱 (包含反斜線)。

- 沒有指定任何新的名稱。

- 目前的名稱不同於登入名稱。

 否則，除非呼叫者額外叫用 NAME 子句，否則使用者將不會重新命名。

對應至 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入、憑證或非對稱金鑰的使用者名稱不可包含反斜線字元 (\\)。

> [!CAUTION]
> [!INCLUDE[ssCautionUserSchema](../../includes/sscautionuserschema-md.md)]

## <a name="security"></a>安全性

> [!NOTE]
> 具有 **ALTER ANY USER** 權限的使用者可以變更任何使用者的預設結構描述。 結構描述變更過的使用者可能會在不知情的情況下，從錯誤的資料表選取資料或從錯誤的結構描述執行程式碼。

### <a name="permissions"></a>權限

 若要變更使用者的名稱，需要具有 **ALTER ANY USER** 權限。

 若要變更使用者的目標登入，則需要資料庫的 **CONTROL** 權限。

 若要變更擁有資料庫 **CONTROL** 權限之使用者的使用者名稱，則需要資料庫的 **CONTROL** 權限。

 若要變更預設結構描述或語言，需要使用者的 **ALTER** 權限。 使用者能夠變更他們自己的預設結構描述或語言。

## <a name="examples"></a>範例

所有的範例會在使用者資料庫中執行。

### <a name="a-changing-the-name-of-a-database-user"></a>A. 變更資料庫使用者的名稱

 下列範例會將資料庫使用者名稱 `Mary5` 變更為 `Mary51`。

```sql
ALTER USER Mary5 WITH NAME = Mary51;
GO
```

### <a name="b-changing-the-default-schema-of-a-user"></a>B. 變更使用者的預設結構描述

 下列範例會將使用者 `Mary51` 的預設結構描述變更為 `Purchasing`。

```sql
ALTER USER Mary51 WITH DEFAULT_SCHEMA = Purchasing;
GO
```

### <a name="c-changing-several-options-at-once"></a>C. 一次變更數個選項

 下列範例會在一個陳述式中變更自主資料庫使用者的數個選項。

```sql
ALTER USER Philip
WITHNAME = Philipe
, DEFAULT_SCHEMA = Development
, PASSWORD = 'W1r77TT98%ab@#' OLD_PASSWORD = 'New Devel0per';
GO
```

## <a name="see-also"></a>另請參閱

- [CREATE USER &#40;Transact-SQL&#41;](../../t-sql/statements/create-user-transact-sql.md)
- [DROP USER &#40;Transact-SQL&#41;](../../t-sql/statements/drop-user-transact-sql.md)
- [自主資料庫](../../relational-databases/databases/contained-databases.md)
- [EVENTDATA &#40;Transact-SQL&#41;](../../t-sql/functions/eventdata-transact-sql.md)
- [sp_migrate_user_to_contained &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-migrate-user-to-contained-transact-sql.md)

::: moniker-end
::: moniker range="=azuresqldb-mi-current"

:::row:::
    :::column:::
        [SQL Server](alter-user-transact-sql.md?view=sql-server-ver15&preserve-view=true)
    :::column-end:::
    :::column:::
        [SQL Database](alter-user-transact-sql.md?view=azuresqldb-current&preserve-view=true)
    :::column-end:::
    :::column:::
        **_\* SQL 受控執行個體\*_**
    :::column-end:::
    :::column:::
        [Azure Synapse<br />Analytics](alter-user-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)
    :::column-end:::
    :::column:::
        [Analytics Platform<br />System (PDW)](alter-user-transact-sql.md?view=aps-pdw-2016&preserve-view=true)
    :::column-end:::
:::row-end:::

&nbsp;

## <a name="azure-sql-managed-instance"></a>Azure SQL 受控執行個體

## <a name="syntax"></a>語法

> [!IMPORTANT]
> 套用至使用 Azure AD 登入的使用者時，Azure SQL 受控執行個體只支援下列選項：`DEFAULT_SCHEMA = { schemaName | NULL }` 與 `DEFAULT_LANGUAGE = { NONE | lcid | language name | language alias }`
> </br> </br> 已新增新的語法延伸模組，可協助重新對應已移轉至 Azure SQL 受控執行個體資料庫中的使用者。 ALTER USER 語法可協助將具有 Azure AD 同盟及同步網域中的資料庫使用者對應到 Azure AD 登入。

```syntaxsql
-- Syntax for SQL Managed Instance
ALTER USER userName
 { WITH <set_item> [ ,...n ] | FROM EXTERNAL PROVIDER }
[;]

<set_item> ::=
NAME = newUserName
| DEFAULT_SCHEMA = { schemaName | NULL }
| LOGIN = loginName
| PASSWORD = 'password' [ OLD_PASSWORD = 'oldpassword' ]
| DEFAULT_LANGUAGE = { NONE | <lcid> | <language name> | <language alias> }
| ALLOW_ENCRYPTED_VALUE_MODIFICATIONS = [ ON | OFF ]

-- Users or groups that are migrated as federated and synchronized with Azure AD have the following syntax:

/** Applies to Windows users that were migrated and have the following user names:
- Windows user <domain\user>
- Windows group <domain\MyWindowsGroup>
- Windows alias <MyWindowsAlias>
**/

ALTER USER userName
 { WITH <set_item> [ ,...n ] | FROM EXTERNAL PROVIDER }
[;]

<set_item> ::=
 NAME = newUserName
| DEFAULT_SCHEMA = { schemaName | NULL }
| LOGIN = loginName
| DEFAULT_LANGUAGE = { NONE | <lcid> | <language name> | <language alias> }
```

## <a name="arguments"></a>引數

 *userName* 指定在這個資料庫內用來識別使用者的名稱。

 LOGIN **=** _loginName_ 將使用者的安全性識別碼 (SID) 變更為符合登入的 SID，以便將使用者重新對應到另一個登入。

 如果 ALTER USER 陳述式是 SQL 批次中的唯一陳述式，Azure SQL Database 會支援 WITH LOGIN 子句。 如果 ALTER USER 陳述式不是 SQL 批次中的唯一陳述式或在動態 SQL 中執行，則不支援 WITH LOGIN 子句。

 NAME **=** _newUserName_ 指定新的名稱給這個使用者。 *newUserName* 不可已存在目前資料庫中。

 DEFAULT_SCHEMA **=** { *schemaName* | NULL } 指定當伺服器解析這個使用者的物件名稱時，將搜尋的第一個結構描述。 當預設結構描述設為 NULL 時，會從 Windows 群組移除預設結構描述。 NULL 選項不可用於 Windows 使用者。

 PASSWORD **=** '*password*'

 指定正在變更之使用者的密碼。 密碼會區分大小寫。

> [!NOTE]
> 只有包含的使用者能夠使用此選項。 如需詳細資訊，請參閱[自主資料庫](../../relational-databases/databases/contained-databases.md)和 [sp_migrate_user_to_contained &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-migrate-user-to-contained-transact-sql.md)。

 OLD_PASSWORD **=** _'oldpassword'_

 目前的使用者密碼，將由 '*password*' 取代。 密碼會區分大小寫。 密碼區分大小寫。除非您有 **ALTER ANY USER** 權限，否則需要 *OLD_PASSWORD* 才能變更密碼。 需要 *OLD_PASSWORD* 可防止具有 **IMPERSONATION** 權限的使用者變更密碼。

> [!NOTE]
> 只有包含的使用者能夠使用此選項。

 DEFAULT_LANGUAGE **=** _{ NONE | \<lcid> | \<language name> | \<language alias> }_

 指定要指派給使用者的預設語言。 如果這個選項設為 NONE，預設語言將設為資料庫的目前預設語言。 如果稍後變更了資料庫的預設語言，使用者的預設語言會保持不變。 *DEFAULT_LANGUAGE* 可以是本機識別碼 (lcid)、語言名稱或語言別名。

> [!NOTE]
> 這個選項只能指定在自主資料庫中，而且只適用於包含的使用者。

 ALLOW_ENCRYPTED_VALUE_MODIFICATIONS = [ ON | **OFF** ]

 在大量複製作業時隱藏伺服器上的密碼編譯中繼資料檢查。 這會讓使用者得以在資料表或資料庫間大量複製加密資料，而無須解密資料。 預設值為 OFF。

> [!WARNING]
> 不當使用這個選項會導致資料損毀。 如需詳細資訊，請參閱[移轉透過 Always Encrypted 保護的機密資料](../../relational-databases/security/encryption/migrate-sensitive-data-protected-by-always-encrypted.md)。

## <a name="remarks"></a>備註

 預設結構描述是伺服器在解析這個資料庫使用者之物件名稱時，所搜尋到的第一個結構描述。 除非另有指定，否則預設結構描述是此資料庫使用者建立之物件的擁有者。

 如果使用者有預設結構描述，則使用預設結構描述。 如果使用者沒有預設結構描述，但使用者是具有預設結構描述群組的成員，則會使用群組的預設結構描述。 如果使用者沒有預設結構描述，且是具有一個以上群組的成員，則使用者預設結構描述將會是具有最低 principle_id 且明確設定預設結構描述 Windows 群組的結構描述。 如果無法判斷使用者的預設結構描述，即會使用 **dbo** 結構描述。

 DEFAULT_SCHEMA 可設為目前不存在於資料庫中的結構描述。 因此，您可以在建立該結構描述之前，先將 DEFAULT_SCHEMA 指派給使用者。

 DEFAULT_SCHEMA 不能指定給對應至憑證或非對稱金鑰的使用者。

> [!IMPORTANT]
> 如果使用者是 **sysadmin** 固定伺服器角色的成員，則會忽略 DEFAULT_SCHEMA 的值。 **sysadmin** 固定伺服器角色的所有成員都有預設的 `dbo` 結構描述。

 當新使用者名稱的 SID 符合資料庫所記錄的 SID 時，您可以變更對應至 Windows 登入或群組的使用者名稱。 這項檢查可防止資料庫中詐騙的 Windows 登入。

 WITH LOGIN 子句可讓使用者重新對應到另一個登入。 沒有登入的使用者、對應至憑證或對應至非對稱金鑰的使用者，都無法使用這個子句來重新對應。 只有 SQL 使用者和 Windows 使用者 (或群組) 才能重新對應。 WITH LOGIN 子句無法用來變更使用者的類型，例如將 Windows 帳戶變更為 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入。 唯一的例外是將 Windows 使用者變更為 Azure AD 使用者時。

> [!NOTE]
> 下列規則不適用於 Azure SQL 受控執行個體上的 Windows 使用者，因為不支援在 Azure SQL 受控執行個體上建立 Windows 登入。 只有 Azure AD 登入存在時，才能使用 WITH LOGIN 選項。

 如果下列條件成立，使用者的名稱將會自動重新命名為登入名稱。

- 使用者是 Windows 使用者。

- 名稱是 Windows 名稱 (包含反斜線)。

- 沒有指定任何新的名稱。

- 目前的名稱不同於登入名稱。

 否則，除非呼叫者額外叫用 NAME 子句，否則使用者將不會重新命名。

對應至 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入、憑證或非對稱金鑰的使用者名稱不可包含反斜線字元 (\\)。

> [!CAUTION]
> [!INCLUDE[ssCautionUserSchema](../../includes/sscautionuserschema-md.md)]

### <a name="remarks-for-windows-users-in-sql-on-premises-migrated-to-azure-sql-managed-instance"></a>將 SQL 內部部署中的 Windows 使用者移轉到 Azure SQL 受控執行個體的備註

這些備註適用於透過已使用 Azure AD 同盟及同步的 Windows 使用者進行驗證。

> [!NOTE]
> 建立完成後的 Azure SQL 受控執行個體 Azure AD 管理員功能已變更。 如需詳細資訊，請參閱 [MI 的新 Azure AD 管理員功能](/azure/sql-database/sql-database-aad-authentication-configure#new-azure-ad-admin-functionality-for-mi)。

- 在用於移轉用途的 ALTER USER 語法所有版本中，根據預設，對應至 Azure AD 的 Windows 使用者或群組驗證是透過圖形 API 執行。
- 已建立別名 (使用與原始 Windows 帳戶不同的名稱) 的內部部署使用者會保留其別名。
- 針對 Azure AD 驗證，LOGIN 參數僅適用於 Azure SQL 受控執行個體，無法與 SQL Database 搭配使用。
- 若要檢視 Azure AD 主體的登入，請使用下列命令：`select * from sys.server_principals`。
- 檢查登入的指定類型為 `E` 或 `X`。
- PASSWORD 選項無法用於 Azure AD 使用者。
- 在所有移轉案例中，Windows 使用者或群組的角色和權限將會自動傳輸到新 Azure AD 使用者或群組。
- 新語法延伸模組 **FROM EXTERNAL PROVIDER** 可用來將 SQL 內部部署的 Windows 使用者和群組更改成 Azure AD 使用者和群組。 Windows 網域必須與 Azure AD 同盟，且在使用此延伸模組時，所有 Windows 網域成員都必須存在於 Azure AD 中。 **FROM EXTERNAL PROVIDER** 語法適用於 Azure SQL 受控執行個體，且應在 Windows 使用者於原始 SQL 執行個體上不具有登入，且需要對應到獨立 Azure AD 資料庫使用者時使用。
- 在此情況下，允許的 userName 可以是：
- Windows 使用者 (_domain\user_)。
- Windows 群組 (_MyWindowsGroup_)。
- Windows 別名 (_MyWindowAlias_)。
- ALTER 命令結果會根據舊 userName 的原始 SID，使用在 Azure AD 中所找到對應名稱來取代舊的 userName。 更改後名稱會遭到替換並儲存在資料庫的中繼資料內：
- (_domain\user_) 將會替換成 Azure AD user@domain.com。
- (_domain\\MyWindowsGroup_) 將會替換成 Azure AD 群組。
- (_MyWindowsAlias_) 將會維持不變，但會在 Azure AD 中檢查此使用者的 SID。

> [!NOTE]
> 若無法在 Azure AD 中找到原始使用者 SID 轉換成的 objectID，則 ALTER USER 命令將會失敗。

- 若要檢視更改後的使用者，請使用下列命令：`select * from sys.database_principals`
- 檢查使用者的指定類型是 `E` 或 `X`。
- 使用 NAME 來將 Windows 使用者移轉至 Azure AD 使用者時，適用下列限制：
- 必須指定有效的 LOGIN。
- 將會在 Azure AD 中檢查 NAME，且只能是：
- LOGIN 的名稱。
- 別名 (Azure AD 中不可存在該名稱)。
- 在其他所有情況下，語法都會失敗。

## <a name="security"></a>安全性

> [!NOTE]
> 具有 **ALTER ANY USER** 權限的使用者可以變更任何使用者的預設結構描述。 結構描述變更過的使用者可能會在不知情的情況下，從錯誤的資料表選取資料或從錯誤的結構描述執行程式碼。

### <a name="permissions"></a>權限

 若要變更使用者的名稱，需要具有 **ALTER ANY USER** 權限。

 若要變更使用者的目標登入，則需要資料庫的 **CONTROL** 權限。

 若要變更擁有資料庫 **CONTROL** 權限之使用者的使用者名稱，則需要資料庫的 **CONTROL** 權限。

 若要變更預設結構描述或語言，需要使用者的 **ALTER** 權限。 使用者能夠變更他們自己的預設結構描述或語言。

## <a name="examples"></a>範例

所有的範例會在使用者資料庫中執行。

### <a name="a-changing-the-name-of-a-database-user"></a>A. 變更資料庫使用者的名稱

 下列範例會將資料庫使用者名稱 `Mary5` 變更為 `Mary51`。

```sql
ALTER USER Mary5 WITH NAME = Mary51;
GO
```

### <a name="b-changing-the-default-schema-of-a-user"></a>B. 變更使用者的預設結構描述

 下列範例會將使用者 `Mary51` 的預設結構描述變更為 `Purchasing`。

```sql
ALTER USER Mary51 WITH DEFAULT_SCHEMA = Purchasing;
GO
```

### <a name="c-changing-several-options-at-once"></a>C. 一次變更數個選項

 下列範例會在一個陳述式中變更自主資料庫使用者的數個選項。

```sql
ALTER USER Philip
WITH NAME = Philipe
, DEFAULT_SCHEMA = Development
, PASSWORD = 'W1r77TT98%ab@#' OLD_PASSWORD = 'New Devel0per'
, DEFAULT_LANGUAGE= French ;
GO
```

### <a name="d-map-the-user-in-the-database-to-an-azure-ad-login-after-migration"></a>D. 在移轉後將資料庫中的使用者對應到 Azure AD 登入

下列範例會將使用者 `westus/joe` 重新對應到 Azure AD 使用者 `joe@westus.com`。 此範例適用於已存在於受控執行個體中的登入。 在您完成資料庫移轉至 Azure SQL 受控執行個體，且想要使用 Azure AD 登入進行驗證時，便需要執行此動作。

```sql
ALTER USER [westus/joe] WITH LOGIN = joe@westus.com
```

### <a name="e-map-an-old-windows-user-in-the-database-without-a-login-in-azure-sql-managed-instance-to-an-azure-ad-user"></a>E. 將資料庫中於 Azure SQL 受控執行個體上不具有登入的舊 Windows 使用者對應到 Azure AD 使用者

下列範例會將不具有登入的使用者 `westus/joe` 重新對應到 Azure AD 使用者 `joe@westus.com`。 Azure AD 中必須存在該同盟使用者。

```sql
ALTER USER [westus/joe] FROM EXTERNAL PROVIDER
```

### <a name="f-map-the-user-alias-to-an-existing-azure-ad-login"></a>F. 將使用者別名對應到現有的 Azure AD 登入

下列範例會將使用者名稱 `westus\joe` 重新對應到 `joe_alias`。 此案例中對應的 Azure AD 登入是 `joe@westus.com`。

```sql
ALTER USER [westus/joe] WITH LOGIN = joe@westus.com, name= joe_alias
```

### <a name="g-map-a-windows-group-that-was-migrated-in-azure-sql-managed-instance-to-an-azure-ad-group"></a>G. 將 Azure SQL 受控執行個體中已移轉的 Windows 群組對應到 Azure AD 群組

下列範例會將舊內部部署群組 `westus\mygroup` 重新對應到受控執行個體中的 Azure AD 群組 `mygroup`。 Azure AD 中必須存在該群組。

```sql
ALTER USER [westus\mygroup] WITH LOGIN = mygroup
```

## <a name="see-also"></a>另請參閱

- [CREATE USER &#40;Transact-SQL&#41;](../../t-sql/statements/create-user-transact-sql.md)
- [DROP USER &#40;Transact-SQL&#41;](../../t-sql/statements/drop-user-transact-sql.md)
- [自主資料庫](../../relational-databases/databases/contained-databases.md)
- [EVENTDATA &#40;Transact-SQL&#41;](../../t-sql/functions/eventdata-transact-sql.md)
- [sp_migrate_user_to_contained &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-migrate-user-to-contained-transact-sql.md)
- [教學課程：使用 T-SQL DDL 語法將 SQL Server 內部部署 Windows 使用者與群組移轉至 SQL 受控執行個體](/azure/sql-database/tutorial-managed-instance-azure-active-directory-migration)

::: moniker-end
::: moniker range="=azure-sqldw-latest"

:::row:::
    :::column:::
        [SQL Server](alter-user-transact-sql.md?view=sql-server-ver15&preserve-view=true)
    :::column-end:::
    :::column:::
        [SQL Database](alter-user-transact-sql.md?view=azuresqldb-current&preserve-view=true)
    :::column-end:::
    :::column:::
        [SQL 受控執行個體](alter-user-transact-sql.md?view=azuresqldb-mi-current&preserve-view=true)
    :::column-end:::
    :::column:::
        **_\* Azure Synapse<br />Analytics \*_**
    :::column-end:::
    :::column:::
        [Analytics Platform<br />System (PDW)](alter-user-transact-sql.md?view=aps-pdw-2016&preserve-view=true)
    :::column-end:::
:::row-end:::

&nbsp;

## <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

## <a name="syntax"></a>語法

```syntaxsql
-- Syntax for Azure Synapse

ALTER USER userName
 WITH <set_item> [ ,...n ]

<set_item> ::=
 NAME = newUserName
 | LOGIN = loginName
 | DEFAULT_SCHEMA = schema_name
[;]
```

## <a name="arguments"></a>引數

 *userName* 指定在這個資料庫內用來識別使用者的名稱。

 LOGIN **=** _loginName_ 將使用者的安全性識別碼 (SID) 變更為符合登入的 SID，以便將使用者重新對應到另一個登入。

 如果 ALTER USER 陳述式是 SQL 批次中的唯一陳述式，Azure SQL Database 會支援 WITH LOGIN 子句。 如果 ALTER USER 陳述式不是 SQL 批次中的唯一陳述式或在動態 SQL 中執行，則不支援 WITH LOGIN 子句。

 NAME **=** _newUserName_ 指定新的名稱給這個使用者。 *newUserName* 不可已存在目前資料庫中。

 DEFAULT_SCHEMA **=** { *schemaName* | NULL } 指定當伺服器解析這個使用者的物件名稱時，將搜尋的第一個結構描述。 當預設結構描述設為 NULL 時，會從 Windows 群組移除預設結構描述。 NULL 選項不可用於 Windows 使用者。

## <a name="remarks"></a>備註

 預設結構描述是伺服器在解析這個資料庫使用者之物件名稱時，所搜尋到的第一個結構描述。 除非另有指定，否則預設結構描述是此資料庫使用者建立之物件的擁有者。

 如果使用者有預設結構描述，則使用預設結構描述。 如果使用者沒有預設結構描述，但使用者是具有預設結構描述群組的成員，則會使用群組的預設結構描述。 如果使用者沒有預設結構描述，且是具有一個以上群組的成員，則使用者預設結構描述將會是具有最低 principle_id 且明確設定預設結構描述 Windows 群組的結構描述。 如果無法判斷使用者的預設結構描述，即會使用 **dbo** 結構描述。

 DEFAULT_SCHEMA 可設為目前不存在於資料庫中的結構描述。 因此，您可以在建立該結構描述之前，先將 DEFAULT_SCHEMA 指派給使用者。

 DEFAULT_SCHEMA 不能指定給對應至憑證或非對稱金鑰的使用者。

> [!IMPORTANT]
> 如果使用者是 **sysadmin** 固定伺服器角色的成員，則會忽略 DEFAULT_SCHEMA 的值。 **sysadmin** 固定伺服器角色的所有成員都有預設的 `dbo` 結構描述。

 WITH LOGIN 子句可讓使用者重新對應到另一個登入。 沒有登入的使用者、對應至憑證或對應至非對稱金鑰的使用者，都無法使用這個子句來重新對應。 只有 SQL 使用者和 Windows 使用者 (或群組) 才能重新對應。 WITH LOGIN 子句無法用來變更使用者的類型，例如將 Windows 帳戶變更為 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入。

 如果下列條件成立，使用者的名稱將會自動重新命名為登入名稱。

- 沒有指定任何新的名稱。

- 目前的名稱不同於登入名稱。

 否則，除非呼叫者額外叫用 NAME 子句，否則使用者將不會重新命名。

對應至 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入、憑證或非對稱金鑰的使用者名稱不可包含反斜線字元 (\\)。

> [!CAUTION]
> [!INCLUDE[ssCautionUserSchema](../../includes/sscautionuserschema-md.md)]

## <a name="security"></a>安全性

> [!NOTE]
> 具有 **ALTER ANY USER** 權限的使用者可以變更任何使用者的預設結構描述。 結構描述變更過的使用者可能會在不知情的情況下，從錯誤的資料表選取資料或從錯誤的結構描述執行程式碼。

### <a name="permissions"></a>權限

 若要變更使用者的名稱，需要具有 **ALTER ANY USER** 權限。

 若要變更使用者的目標登入，則需要資料庫的 **CONTROL** 權限。

 若要變更擁有資料庫 **CONTROL** 權限之使用者的使用者名稱，則需要資料庫的 **CONTROL** 權限。

 若要變更預設結構描述或語言，需要使用者的 **ALTER** 權限。 使用者能夠變更他們自己的預設結構描述或語言。

## <a name="examples"></a>範例

所有的範例會在使用者資料庫中執行。

### <a name="a-changing-the-name-of-a-database-user"></a>A. 變更資料庫使用者的名稱

 下列範例會將資料庫使用者名稱 `Mary5` 變更為 `Mary51`。

```sql
ALTER USER Mary5 WITH NAME = Mary51;
GO
```

### <a name="b-changing-the-default-schema-of-a-user"></a>B. 變更使用者的預設結構描述

下列範例會將使用者 `Mary51` 的預設結構描述變更為 `Purchasing`。

```sql
ALTER USER Mary51 WITH DEFAULT_SCHEMA = Purchasing;
GO
```

## <a name="see-also"></a>另請參閱

- [CREATE USER &#40;Transact-SQL&#41;](../../t-sql/statements/create-user-transact-sql.md)
- [DROP USER &#40;Transact-SQL&#41;](../../t-sql/statements/drop-user-transact-sql.md)
- [自主資料庫](../../relational-databases/databases/contained-databases.md)
- [EVENTDATA &#40;Transact-SQL&#41;](../../t-sql/functions/eventdata-transact-sql.md)
- [sp_migrate_user_to_contained &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-migrate-user-to-contained-transact-sql.md)

::: moniker-end
::: moniker range=">=aps-pdw-2016"

:::row:::
    :::column:::
        [SQL Server](alter-user-transact-sql.md?view=sql-server-ver15&preserve-view=true)
    :::column-end:::
    :::column:::
        [SQL Database](alter-user-transact-sql.md?view=azuresqldb-current&preserve-view=true)
    :::column-end:::
    :::column:::
        [SQL 受控執行個體](alter-user-transact-sql.md?view=azuresqldb-mi-current&preserve-view=true)
    :::column-end:::
    :::column:::
        [Azure Synapse<br />Analytics](alter-user-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)
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

ALTER USER userName
 WITH <set_item> [ ,...n ]

<set_item> ::=
 NAME = newUserName
 | LOGIN = loginName
 | DEFAULT_SCHEMA = schema_name
[;]
```

## <a name="arguments"></a>引數

 *userName* 指定在這個資料庫內用來識別使用者的名稱。

 LOGIN **=** _loginName_ 將使用者的安全性識別碼 (SID) 變更為符合登入的 SID，以便將使用者重新對應到另一個登入。

 如果 ALTER USER 陳述式是 SQL 批次中的唯一陳述式，Azure SQL Database 會支援 WITH LOGIN 子句。 如果 ALTER USER 陳述式不是 SQL 批次中的唯一陳述式或在動態 SQL 中執行，則不支援 WITH LOGIN 子句。

 NAME **=** _newUserName_ 指定新的名稱給這個使用者。 *newUserName* 不可已存在目前資料庫中。

 DEFAULT_SCHEMA **=** { *schemaName* | NULL } 指定當伺服器解析這個使用者的物件名稱時，將搜尋的第一個結構描述。 當預設結構描述設為 NULL 時，會從 Windows 群組移除預設結構描述。 NULL 選項不可用於 Windows 使用者。

## <a name="remarks"></a>備註

 預設結構描述是伺服器在解析這個資料庫使用者之物件名稱時，所搜尋到的第一個結構描述。 除非另有指定，否則預設結構描述是此資料庫使用者建立之物件的擁有者。

 如果使用者有預設結構描述，則使用預設結構描述。 如果使用者沒有預設結構描述，但使用者是具有預設結構描述群組的成員，則會使用群組的預設結構描述。 如果使用者沒有預設結構描述，且是具有一個以上群組的成員，則使用者預設結構描述將會是具有最低 principle_id 且明確設定預設結構描述 Windows 群組的結構描述。 如果無法判斷使用者的預設結構描述，即會使用 **dbo** 結構描述。

 DEFAULT_SCHEMA 可設為目前不存在於資料庫中的結構描述。 因此，您可以在建立該結構描述之前，先將 DEFAULT_SCHEMA 指派給使用者。

 DEFAULT_SCHEMA 不能指定給對應至憑證或非對稱金鑰的使用者。

> [!IMPORTANT]
> 如果使用者是 **sysadmin** 固定伺服器角色的成員，則會忽略 DEFAULT_SCHEMA 的值。 **sysadmin** 固定伺服器角色的所有成員都有預設的 `dbo` 結構描述。

 WITH LOGIN 子句可讓使用者重新對應到另一個登入。 沒有登入的使用者、對應至憑證或對應至非對稱金鑰的使用者，都無法使用這個子句來重新對應。 只有 SQL 使用者和 Windows 使用者 (或群組) 才能重新對應。 WITH LOGIN 子句無法用來變更使用者的類型，例如將 Windows 帳戶變更為 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入。

 如果下列條件成立，使用者的名稱將會自動重新命名為登入名稱。

- 沒有指定任何新的名稱。

- 目前的名稱不同於登入名稱。

 否則，除非呼叫者額外叫用 NAME 子句，否則使用者將不會重新命名。

對應至 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入、憑證或非對稱金鑰的使用者名稱不可包含反斜線字元 (\\)。

> [!CAUTION]
> [!INCLUDE[ssCautionUserSchema](../../includes/sscautionuserschema-md.md)]

## <a name="security"></a>安全性

> [!NOTE]
> 具有 **ALTER ANY USER** 權限的使用者可以變更任何使用者的預設結構描述。 結構描述變更過的使用者可能會在不知情的情況下，從錯誤的資料表選取資料或從錯誤的結構描述執行程式碼。

### <a name="permissions"></a>權限

 若要變更使用者的名稱，需要具有 **ALTER ANY USER** 權限。

 若要變更使用者的目標登入，則需要資料庫的 **CONTROL** 權限。

 若要變更擁有資料庫 **CONTROL** 權限之使用者的使用者名稱，則需要資料庫的 **CONTROL** 權限。

 若要變更預設結構描述或語言，需要使用者的 **ALTER** 權限。 使用者能夠變更他們自己的預設結構描述或語言。

## <a name="examples"></a>範例

所有的範例會在使用者資料庫中執行。

### <a name="a-changing-the-name-of-a-database-user"></a>A. 變更資料庫使用者的名稱

 下列範例會將資料庫使用者名稱 `Mary5` 變更為 `Mary51`。

```sql
ALTER USER Mary5 WITH NAME = Mary51;
GO
```

### <a name="b-changing-the-default-schema-of-a-user"></a>B. 變更使用者的預設結構描述
 下列範例會將使用者 `Mary51` 的預設結構描述變更為 `Purchasing`。

```sql
ALTER USER Mary51 WITH DEFAULT_SCHEMA = Purchasing;
GO
```

## <a name="see-also"></a>另請參閱

- [CREATE USER &#40;Transact-SQL&#41;](../../t-sql/statements/create-user-transact-sql.md)
- [DROP USER &#40;Transact-SQL&#41;](../../t-sql/statements/drop-user-transact-sql.md)
- [自主資料庫](../../relational-databases/databases/contained-databases.md)
- [EVENTDATA &#40;Transact-SQL&#41;](../../t-sql/functions/eventdata-transact-sql.md)
- [sp_migrate_user_to_contained &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-migrate-user-to-contained-transact-sql.md)

::: moniker-end
