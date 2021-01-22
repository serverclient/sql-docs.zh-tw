---
description: CREATE CREDENTIAL (Transact-SQL)
title: CREATE CREDENTIAL (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 09/25/2019
ms.prod: sql
ms.prod_service: sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- CREDENTIAL_TSQL
- SQL13.SWB.CREDENTIAL.GENERAL.F1
- CREATE CREDENTIAL
- CREATE_CREDENTIAL_TSQL
- CREDENTIAL
dev_langs:
- TSQL
helpviewer_keywords:
- SECRET clause
- authentication [SQL Server], credentials
- CREATE CREDENTIAL statement
- credentials [SQL Server], CREATE CREDENTIAL statement
ms.assetid: d5e9ae69-41d9-4e46-b13d-404b88a32d9d
author: VanMSFT
ms.author: vanto
monikerRange: =azuresqldb-mi-current||>=sql-server-2016||>=sql-server-linux-2017
ms.openlocfilehash: b98affdc431a7e13f83ed152248e3abc1300bf76
ms.sourcegitcommit: 713e5a709e45711e18dae1e5ffc190c7918d52e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98688793"
---
# <a name="create-credential-transact-sql"></a>CREATE CREDENTIAL (Transact-SQL)

[!INCLUDE [SQL Server - ASDBMI](../../includes/applies-to-version/sql-asdbmi.md)]

建立伺服器層級認證。 認證是包含驗證資訊的記錄，而該資訊是連線到 SQL Server 外部資源時的必要資訊。 大部分認證都包含 Windows 使用者和密碼。 例如，將資料庫備份儲存至某位置，可能需要 SQL Server 提供特殊的認證才能存取該位置。 如需詳細資訊，請參閱[認證 (資料引擎)](../../relational-databases/security/authentication-access/credentials-database-engine.md)。

> [!NOTE]
> 若要在資料庫層級建立認證，請使用 [CREATE DATABASE SCOPED CREDENTIAL &#40;Transact-SQL&#41;](../../t-sql/statements/create-database-scoped-credential-transact-sql.md)。 當您需要在伺服器上的多個資料庫使用相同認證時，請使用伺服器層級認證。 使用資料庫範圍認證讓資料庫更方便移植。 當資料庫移動至新的伺服器時，資料庫範圍認證會隨著一起移動。 在 [!INCLUDE[ssSDS](../../includes/sssds-md.md)] 使用資料庫範圍認證。

![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)

## <a name="syntax"></a>語法

```syntaxsql
CREATE CREDENTIAL credential_name
WITH IDENTITY = 'identity_name'
    [ , SECRET = 'secret' ]
        [ FOR CRYPTOGRAPHIC PROVIDER cryptographic_provider_name ]
```

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數

*credential_name* 指定所要建立認證的名稱。 *credential_name* 的開頭不可以是編號 (#) 符號。 系統認證必須以 ## 為開頭。 

> [!IMPORTANT]
> 當使用共用存取簽章 (SAS) 時，此名稱必須符合容器路徑、開頭為 https，而且不能包含正斜線。 請參閱[範例 D](#d-creating-a-credential-using-a-sas-token)。

IDENTITY **='** _identity\_name_ **'** 指定連接到伺服器外部時要使用的帳戶名稱。 認證用來存取 Azure Key Vault 時，**IDENTITY** 是金鑰保存庫的名稱。 請參閱以下的範例 C。 認證使用共用存取簽章 (SAS) 時，**IDENTITY** 是 *SHARED ACCESS SIGNATURE*。 請參閱下方範例 D。

> [!IMPORTANT]
> Azure SQL Database 只支援 Azure Key Vault 與共用存取簽章身分識別。 不支援 Windows 使用者身分識別。

SECRET **='** _secret_ **'** 指定外寄驗證所需的祕密。

當認證用來存取 Azure Key Vault 時，**CREATE CREDENTIAL** 其 **SECRET** 引數要求 Azure Active Directory 中 **服務主體** 的 *\<Client ID>* (不含連字號) 和 *\<Secret>* 一併傳遞，且兩者之間沒有空格。 請參閱以下的範例 C。 當認證使用共用存取簽章時，**SECRET** 是共用存取簽章權杖。 請參閱下方範例 D。 如需有關在 Azure 容器上建立預存存取原則與共用存取簽章的詳細資訊，請參閱[第 1 課：在 Azure 容器上建立預存存取原則和共用存取簽章](../../relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016.md#1---create-stored-access-policy-and-shared-access-storage)。

FOR CRYPTOGRAPHIC PROVIDER *cryptographic_provider_name* 指定 *企業金鑰管理 (EKM) 提供者* 的名稱。 如需金鑰管理的詳細資訊，請參閱[可延伸金鑰管理 &#40;EKM&#41;](../../relational-databases/security/encryption/extensible-key-management-ekm.md)。

## <a name="remarks"></a>備註

當 IDENTITY 是 Windows 使用者時，秘密可以是密碼。 秘密是利用服務主要金鑰來加密的。 如果重新產生服務主要金鑰，便會利用新的服務主要金鑰來重新加密秘密。

建立認證之後，您可以利用 [CREATE LOGIN](../../t-sql/statements/create-login-transact-sql.md) 或 [ALTER LOGIN](../../t-sql/statements/alter-login-transact-sql.md)，將其對應至 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入只能對應至一個認證，但單一認證則可對應至多個 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入。 如需詳細資訊，請參閱[認證 &#40;資料庫引擎&#41;](../../relational-databases/security/authentication-access/credentials-database-engine.md)。 伺服器層級認證只能對應至登入，不能對應至資料庫使用者。 

您可以在 [sys.credentials](../../relational-databases/system-catalog-views/sys-credentials-transact-sql.md) 目錄檢視中，看到認證的相關資訊。

如果提供者沒有任何登入對應認證，系統就會使用對應至 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 服務帳戶的認證。

一個登入可以具有多個對應認證，只要這些認證用於不同的提供者即可。 但是，每個登入的每個提供者必須只有一個對應認證。 相同的認證可對應至其他登入。

## <a name="permissions"></a>權限

需要 **ALTER ANY CREDENTIAL** 權限。

## <a name="examples"></a>範例

### <a name="a-creating-a-credential-for-windows-identity"></a>A. 建立 Windows 身分識別的認證

下列範例會建立一個稱為 `AlterEgo` 的認證。 這個認證包含 Windows 使用者 `Mary5` 和密碼。

```sql
CREATE CREDENTIAL AlterEgo WITH IDENTITY = 'Mary5',
    SECRET = '<EnterStrongPasswordHere>';
GO
```

### <a name="b-creating-a-credential-for-ekm"></a>B. 建立 EKM 的認證

下列範例會透過 EKM 的管理工具 (包含基本帳戶類型和密碼)，在 EKM 模組上使用之前建立的帳戶 `User1OnEKM`。 伺服器上 **系統管理員** 帳戶會建立用來連線到 EKM 帳戶的認證，並將其指派給 `User1` [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 帳戶：

```sql
CREATE CREDENTIAL CredentialForEKM
    WITH IDENTITY='User1OnEKM', SECRET='<EnterStrongPasswordHere>'
    FOR CRYPTOGRAPHIC PROVIDER MyEKMProvider;
GO

/* Modify the login to assign the cryptographic provider credential */
ALTER LOGIN User1
ADD CREDENTIAL CredentialForEKM;
```

### <a name="c-creating-a-credential-for-ekm-using-the-azure-key-vault"></a>C. 使用 Azure 金鑰保存庫建立 EKM 的認證

下列範例會建立 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 認證，供 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 在使用 **適用於 Microsoft Azure Key Vault 的 SQL Server 連接器** 時用於存取 Azure Key Vault。 如需使用 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 連接器的完整範例，請參閱[使用 Azure Key Vault 進行可延伸金鑰管理 &#40;SQL Server&#41;](../../relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server.md)。

> [!IMPORTANT]
> **CREATE CREDENTIAL** 的 **IDENTITY** 引數需要金鑰保存庫名稱。 **CREATE CREDENTIAL** 的 **SECRET** 引數要求 *\<Client ID>* (不含連字號) 和 *\<Secret>* 一併傳遞，且兩者之間不含空格。

 在下列範例中， **用戶端識別碼** (`EF5C8E09-4D2A-4A76-9998-D93440D8115D`) 已移除連字號並以字串 `EF5C8E094D2A4A769998D93440D8115D` 輸入，而 **密碼** 會以字串 *SECRET_DBEngine* 來表示。

```sql
USE master;
CREATE CREDENTIAL Azure_EKM_TDE_cred
    WITH IDENTITY = 'ContosoKeyVault',
    SECRET = 'EF5C8E094D2A4A769998D93440D8115DSECRET_DBEngine'
    FOR CRYPTOGRAPHIC PROVIDER AzureKeyVault_EKM_Prov ;
```

下列範例會使用 **用戶端識別碼** 和 **祕密** 字串的變數建立相同的認證，然後串連在一起以形成 **SECRET** 引數。 **REPLACE** 函數可用來從用戶端識別碼中移除連字號。

```sql
DECLARE @AuthClientId uniqueidentifier = 'EF5C8E09-4D2A-4A76-9998-D93440D8115D';
DECLARE @AuthClientSecret varchar(200) = 'SECRET_DBEngine';
DECLARE @pwd varchar(max) = REPLACE(CONVERT(varchar(36), @AuthClientId) , '-', '') + @AuthClientSecret;

EXEC ('CREATE CREDENTIAL Azure_EKM_TDE_cred
    WITH IDENTITY = 'ContosoKeyVault', SECRET = ''' + @PWD + '''
    FOR CRYPTOGRAPHIC PROVIDER AzureKeyVault_EKM_Prov ;');
```

### <a name="d-creating-a-credential-using-a-sas-token"></a>D. 使用 SAS 權杖建立認證

**適用於**：[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 到 [目前版本](../../sql-server/what-s-new-in-sql-server-2016.md)與 Azure SQL 受控執行個體。

下列範例會使用 SAS 權杖來建立共用存取簽章憑證。 如需在 Azure 容器上建立預存存取原則和共用存取簽章，再使用共用存取簽章來建立認證的教學課程，請參閱[教學課程：搭配 SQL Server 2016 資料庫使用 Microsoft Azure Blob 儲存體服務](../../relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016.md)。

> [!IMPORTANT]
> **CREDENTIAL NAME** 引數要求名稱與容器路徑相符，開頭為 https，而且不包含尾端斜線。 **IDENTITY** 引數需要名稱 *SHARED ACCESS SIGNATURE*。 **SECRET** 引數需要共用存取簽章權杖。
>
> **SHARED ACCESS SIGNATURE 祕密** 開頭不應該有 **?** 。

```sql
USE master
CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>] -- this name must match the container path, start with https and must not contain a trailing forward slash.
    WITH IDENTITY='SHARED ACCESS SIGNATURE' -- this is a mandatory string and do not change it.
    , SECRET = 'sharedaccesssignature' -- this is the shared access signature token
GO
```

### <a name="e-creating-a-credential-for-managed-identity"></a>E. 建立受控識別的認證

下列範例會建立代表 Azure SQL 受控識別或 Azure Synapse 服務的認證。 在此情況下，密碼與祕密並不適用。

```sql
CREATE CREDENTIAL ServiceIdentity WITH IDENTITY = 'Managed Identity';
GO
```

## <a name="see-also"></a>另請參閱

- [認證 &#40;Database Engine&#41;](../../relational-databases/security/authentication-access/credentials-database-engine.md)
- [ALTER CREDENTIAL &#40;Transact-SQL&#41;](../../t-sql/statements/alter-credential-transact-sql.md)
- [DROP CREDENTIAL &#40;Transact-SQL&#41;](../../t-sql/statements/drop-credential-transact-sql.md)
- [CREATE DATABASE SCOPED CREDENTIAL &#40;TRANSACT-SQL&#41;](../../t-sql/statements/create-database-scoped-credential-transact-sql.md)
- [CREATE LOGIN &#40;Transact-SQL&#41;](../../t-sql/statements/create-login-transact-sql.md)
- [ALTER LOGIN &#40;Transact-SQL&#41;](../../t-sql/statements/alter-login-transact-sql.md)
- [sys.credentials &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-credentials-transact-sql.md)
- [第 2 課：使用共用存取簽章建立 SQL Server 認證](../../relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016.md#2---create-a-sql-server-credential-using-a-shared-access-signature)
- [共用存取簽章](/azure/storage/common/storage-sas-overview)