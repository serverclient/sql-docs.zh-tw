---
description: CREATE SYMMETRIC KEY (Transact-SQL)
title: CREATE SYMMETRIC KEY (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 06/11/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- CREATE SYMMETRIC KEY
- SYMMETRIC KEP
- CREATE_SYMMETRIC_KEY_TSQL
- SYMMETRIC_KEY_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- CREATE SYMMETRIC KEY statement
- temporary symmetric keys [SQL Server]
- symmetric keys [SQL Server], creating
- symmetric keys [SQL Server]
ms.assetid: b5d23572-b79d-4cf1-9eef-d648fa3b1358
author: VanMSFT
ms.author: vanto
ms.openlocfilehash: 581f49677f56c0d0c1360d5282c07205f18ee35a
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98170820"
---
# <a name="create-symmetric-key-transact-sql"></a>CREATE SYMMETRIC KEY (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中產生對稱金鑰及指定它的屬性。  
  
 此功能與使用資料層應用程式架構 (DACFx) 的資料庫匯出不相容。 您必須在匯出之前先卸除所有非對稱金鑰。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
CREATE SYMMETRIC KEY key_name   
    [ AUTHORIZATION owner_name ]  
    [ FROM PROVIDER provider_name ]  
    WITH 
      [
          <key_options> [ , ... n ]  
        | ENCRYPTION BY <encrypting_mechanism> [ , ... n ] 
      ]
  
<key_options> ::=  
      KEY_SOURCE = 'pass_phrase'  
    | ALGORITHM = <algorithm>  
    | IDENTITY_VALUE = 'identity_phrase'  
    | PROVIDER_KEY_NAME = 'key_name_in_provider'   
    | CREATION_DISPOSITION = {CREATE_NEW | OPEN_EXISTING }  
  
<algorithm> ::=  
    DES | TRIPLE_DES | TRIPLE_DES_3KEY | RC2 | RC4 | RC4_128  
    | DESX | AES_128 | AES_192 | AES_256   
  
<encrypting_mechanism> ::=  
      CERTIFICATE certificate_name   
    | PASSWORD = 'password'   
    | SYMMETRIC KEY symmetric_key_name   
    | ASYMMETRIC KEY asym_key_name  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *Key_name*  
 依據資料庫中已知之對稱金鑰指定唯一的名稱。 當 _key_name_ 的開頭是數字 (#) 符號時，會指定暫存金鑰。 例如， **#temporaryKey900007**。 您無法建立名稱開頭有多個數字符號 (#) 的對稱金鑰。 您無法使用 EKM 提供者建立暫時對稱金鑰。  
  
 AUTHORIZATION *owner_name*  
 指定將擁有這個金鑰的資料庫使用者或應用程式角色的名稱。  
  
 FROM PROVIDER *provider_name*  
 指定可延伸金鑰管理 (EKM) 提供者和名稱。 此金鑰不會從 EKM 裝置匯出。 您必須先使用 CREATE PROVIDER 陳述式定義提供者。 如需有關建立外部索引鍵提供者的詳細資訊，請參閱[可延伸金鑰管理 &#40;EKM&#41;](../../relational-databases/security/encryption/extensible-key-management-ekm.md)。  
  
> [!NOTE]  
>  自主資料庫無法使用這個選項。  
  
 KEY_SOURCE **='** _pass\_phrase_ **'**  
 指定要從其衍生金鑰的複雜密碼。  
  
 IDENTITY_VALUE **='** _identity\_phrase_ **'**  
 指定要從中產生 GUID 來標記利用暫時金鑰加密的資料之識別片語。  
  
 PROVIDER_KEY_NAME **='** _key\_name\_in\_provider_ **'**  
 指定在可延伸金鑰管理提供者中所參考的名稱。  
  
> [!NOTE]  
>  自主資料庫無法使用這個選項。  
  
 CREATION_DISPOSITION **=** CREATE_NEW  
 在可延伸金鑰管理裝置上建立新的金鑰。  如果金鑰已存在於裝置上，陳述式會失敗，並傳回錯誤。  
  
 CREATION_DISPOSITION **=** OPEN_EXISTING  
 將 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 對稱金鑰對應到現有的「可延伸金鑰管理」金鑰。 如果未提供 CREATION_DISPOSITION = OPEN_EXISTING，就會預設為 CREATE_NEW。  
  
 *certificate_name*  
 指定要用於加密對稱金鑰的憑證之名稱。 這個憑證必須已存在於資料庫中。  
  
 **'** *password* **'**  
 指定衍生 TRIPLE_DES 金鑰的密碼，其可以用於保護對稱金鑰的安全。 *password* 必須符合執行 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體之電腦的 Windows 密碼原則需求。 一律使用強式密碼。  
  
 *symmetric_key_name*  
 指定要用於將建立的金鑰加密之對稱金鑰。 指定的金鑰必須已存在於資料庫中，且該金鑰必須是開啟的。  
  
 *asym_key_name*  
 指定要用於將建立的金鑰加密之非對稱金鑰。 這個非對稱金鑰必須已存在於資料庫中。  
  
 \<algorithm>  
指定加密演算法。   
> [!WARNING]  
> 開頭為 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)]，取代 AES_128、AES_192 和 AES_256 以外的所有演算法。 若要使用較舊的演算法 (不建議)，您必須將資料庫相容性層級設定為 120 或更低。  
  
## <a name="remarks"></a>備註  
 當建立對稱金鑰時，至少必須利用下列一項來加密對稱金鑰：憑證、密碼、對稱金鑰、非對稱金鑰或 PROVIDER。 針對每一種類型，金鑰都可以有多個加密。 換句話說，可以同時利用多個憑證、密碼、對稱金鑰及非對稱金鑰來加密單一對稱金鑰。  
  
> [!CAUTION]  
>  當對稱金鑰是使用密碼進行加密而不是使用憑證 (或其他金鑰) 時，系統會使用 TRIPLE DES 加密演算法將該密碼加密。 因此，利用強式加密演算法 (如 AES) 建立的金鑰，其本身的安全是由較弱的演算法來維護的。  
  
 將金鑰散發至多個使用者之前，可先利用選擇性的密碼來加密對稱金鑰。  
  
 暫時金鑰由建立它們的使用者擁有。 暫時金鑰只能用於目前的工作階段。  
  
 IDENTITY_VALUE 會產生 GUID，這個 GUID 可用於標記利用新對稱金鑰加密的資料。 這項標記作業可用於使金鑰符合加密的資料。 特定片語產生的 GUID 一律相同。 在利用片語產生 GUID 之後，只要至少有一個工作階段正主動使用該片語，就無法重複使用該片語。 IDENTITY_VALUE 是選擇性的子句。不過，當您要儲存利用暫時金鑰加密的資料時，我們建議您使用這個子句。  
  
 沒有預設的加密演算法。  
  
> [!IMPORTANT]  
>  不建議您使用 RC4 和 RC4_128 資料流加密保護機密資料。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 不會對使用這類金鑰進一步執行加密編碼。  
  
 您可以在 [sys.symmetric_keys](../../relational-databases/system-catalog-views/sys-symmetric-keys-transact-sql.md) 目錄檢視中，看到有關對稱金鑰的資訊。  
  
 您無法利用從加密提供者所建立的對稱金鑰，加密對稱金鑰。  
  
 **釐清有關 DES 演算法的資訊：**  
  
-   DESX 未正確命名。 以 ALGORITHM = DESX 建立的對稱金鑰實際上使用具有 192 位元金鑰的 TRIPLE DES 加密。 未提供 DESX 演算法。 [!INCLUDE[ssNoteDepFutureAvoid](../../includes/ssnotedepfutureavoid-md.md)]  
-   以 ALGORITHM = TRIPLE_DES_3KEY 建立的對稱金鑰使用具有 192 位元金鑰的 TRIPLE DES。  
-   以 ALGORITHM = TRIPLE_DES 建立的對稱金鑰使用具有 128 位元金鑰的 TRIPLE DES。  
  
 **RC4 演算法的取代：**  
  
 在不同的資料區塊上重複使用相同的 RC4 或 RC4_128 KEY_GUID，結果會是相同的 RC4 金鑰，因為 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 不會自動提供 Salt。 重複使用相同的 RC4 金鑰是已知的錯誤，此錯誤會造成加密變弱。 因此，我們取代了 RC4 和 RC4_128 關鍵字。 [!INCLUDE[ssNoteDepFutureDontUse](../../includes/ssnotedepfuturedontuse-md.md)]  
  
> [!WARNING]  
>  只有 RC4 演算法支援回溯相容性。 只有在資料庫相容性層級為 90 或 100 時，才能使用 RC4 或 RC4_128 加密新資料 (不建議使用)。請改用較新的演算法，例如其中一個 AES 演算法。 在 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 中，使用 RC4 或 RC4_128 加密的資料可以在任何相容性層級進行解密。  
  
## <a name="permissions"></a>權限  
 需要資料庫的 ALTER ANY SYMMETRIC KEY 權限。 如果指定了 AUTHORIZATION，則需要資料庫使用者的 IMPERSONATE 權限或應用程式角色的 ALTER 權限。 如果是利用憑證或非對稱金鑰來加密，則需要憑證或非對稱金鑰的 VIEW DEFINITION 權限。 只有 Windows 登入、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入，以及應用程式角色可以擁有對稱金鑰。 群組和角色無法擁有對稱金鑰。  
  
## <a name="examples"></a>範例  
  
### <a name="a-creating-a-symmetric-key"></a>A. 建立對稱金鑰  
 下列範例會利用 `JanainaKey09` 演算法建立一個稱為 `AES 256` 的對稱金鑰，然後利用憑證 `Shipping04` 加密新金鑰。  
  
```sql  
CREATE SYMMETRIC KEY JanainaKey09   
WITH ALGORITHM = AES_256  
ENCRYPTION BY CERTIFICATE Shipping04;  
GO  
```  
  
### <a name="b-creating-a-temporary-symmetric-key"></a>B. 建立暫時對稱金鑰  
 下列範例會從這個複雜密碼中建立一個稱為 `#MarketingXXV` 的暫時對稱金鑰：`The square of the hypotenuse is equal to the sum of the squares of the sides`。 金鑰由某 GUID 提供，該 GUID 是從字串 `Pythagoras` 產生且利用憑證 `Marketing25` 來加密的。  
  
```sql 
CREATE SYMMETRIC KEY #MarketingXXV   
WITH ALGORITHM = AES_128,  
KEY_SOURCE   
     = 'The square of the hypotenuse is equal to the sum of the squares of the sides',  
IDENTITY_VALUE = 'Pythagoras'  
ENCRYPTION BY CERTIFICATE Marketing25;  
GO  
```  
  
### <a name="c-creating-a-symmetric-key-using-an-extensible-key-management-ekm-device"></a>C. 使用可延伸金鑰管理 (EKM) 裝置建立對稱金鑰  
 下列範例會使用稱為 `MySymKey` 的提供者與 `MyEKMProvider` 的金鑰名稱，建立稱為 `KeyForSensitiveData` 的對稱金鑰。 它會將授權指派給 `User1`，並假設系統管理員已經在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中，註冊稱為 `MyEKMProvider` 的提供者。  
  
```sql  
CREATE SYMMETRIC KEY MySymKey  
AUTHORIZATION User1  
FROM PROVIDER EKMProvider  
WITH  
PROVIDER_KEY_NAME='KeyForSensitiveData',  
CREATION_DISPOSITION=OPEN_EXISTING;  
GO  
```  
  
## <a name="see-also"></a>另請參閱  
 [選擇加密演算法](../../relational-databases/security/encryption/choose-an-encryption-algorithm.md)   
 [ALTER SYMMETRIC KEY &#40;Transact-SQL&#41;](../../t-sql/statements/alter-symmetric-key-transact-sql.md)   
 [DROP SYMMETRIC KEY &#40;Transact-SQL&#41;](../../t-sql/statements/drop-symmetric-key-transact-sql.md)   
 [加密階層](../../relational-databases/security/encryption/encryption-hierarchy.md)   
 [sys.symmetric_keys &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-symmetric-keys-transact-sql.md)   
 [可延伸金鑰管理 &#40;EKM&#41;](../../relational-databases/security/encryption/extensible-key-management-ekm.md)   
 [使用 Azure 金鑰保存庫進行可延伸金鑰管理 &#40;SQL Server&#41;](../../relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server.md)  
  
  
