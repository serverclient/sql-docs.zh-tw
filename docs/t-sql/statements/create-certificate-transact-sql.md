---
description: CREATE CERTIFICATE (Transact-SQL)
title: CREATE CERTIFICATE (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 02/06/2020
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- CERTIFICATE
- CREATE_CERTIFICATE_TSQL
- sql13.swb.createcertificate.f1
- CERTIFICATE_TSQL
- CREATE CERTIFICATE
- sql13.swb.certificateproperties.f1
dev_langs:
- TSQL
helpviewer_keywords:
- encryption [SQL Server], certificates
- certificates [SQL Server], adding
- certificates [SQL Server]
- adding certificates
- cryptography [SQL Server], certificates
- CREATE CERTIFICATE statement
ms.assetid: a4274b2b-4cb0-446a-a956-1c8e6587515d
author: VanMSFT
ms.author: vanto
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 238fcde63bf0af459ab741f54a411fefb317bfe9
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98170350"
---
# <a name="create-certificate-transact-sql"></a>CREATE CERTIFICATE (Transact-SQL)
[!INCLUDE [sql-asdb-pdw](../../includes/applies-to-version/sql-asdb-pdw.md)]

  將憑證加入至 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 資料庫中。  

 此功能與使用資料層應用程式架構 (DACFx) 的資料庫匯出不相容。 您必須在匯出之前先卸除所有憑證。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
-- Syntax for SQL Server and Azure SQL Database  
  
CREATE CERTIFICATE certificate_name [ AUTHORIZATION user_name ]   
    { FROM <existing_keys> | <generate_new_keys> }  
    [ ACTIVE FOR BEGIN_DIALOG = { ON | OFF } ]  
  
<existing_keys> ::=   
    ASSEMBLY assembly_name  
    | {   
        [ EXECUTABLE ] FILE = 'path_to_file'  
        [ WITH PRIVATE KEY ( <private_key_options> ) ]   
      }  
    | {   
        BINARY = asn_encoded_certificate  
        [ WITH PRIVATE KEY ( <private_key_options> ) ]  
      }  
<generate_new_keys> ::=   
    [ ENCRYPTION BY PASSWORD = 'password' ]   
    WITH SUBJECT = 'certificate_subject_name'   
    [ , <date_options> [ ,...n ] ]   
  
<private_key_options> ::=  
      {   
        FILE = 'path_to_private_key'  
         [ , DECRYPTION BY PASSWORD = 'password' ]  
         [ , ENCRYPTION BY PASSWORD = 'password' ]    
      }  
    |  
      {   
        BINARY = private_key_bits  
         [ , DECRYPTION BY PASSWORD = 'password' ]  
         [ , ENCRYPTION BY PASSWORD = 'password' ]    
      }  
  
<date_options> ::=  
    START_DATE = 'datetime' | EXPIRY_DATE = 'datetime'  
```  
  
   
```syntaxsql
-- Syntax for Parallel Data Warehouse  
  
CREATE CERTIFICATE certificate_name   
    { <generate_new_keys> | FROM <existing_keys> }  
    [ ; ]  
  
<generate_new_keys> ::=   
    WITH SUBJECT = 'certificate_subject_name'   
    [ , <date_options> [ ,...n ] ]   
  
<existing_keys> ::=   
    {   
      FILE ='path_to_file'  
      WITH PRIVATE KEY   
         (   
           FILE = 'path_to_private_key'  
           , DECRYPTION BY PASSWORD ='password'   
         )  
    }  
  
<date_options> ::=  
    START_DATE ='datetime' | EXPIRY_DATE ='datetime'  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *certificate_name*  
 為資料庫憑證的名稱。  
  
 AUTHORIZATION *user_name*  
 為擁有此憑證的使用者名稱。  
  
 ASSEMBLY *assembly_name*  
 指定已載入資料庫中之簽署的組件。  
  
 [ EXECUTABLE ] FILE = '*path_to_file*'  
 指定包含憑證之 DER 編碼檔案的完整路徑，包括檔案名稱。 如有使用 EXECUTABLE 選項，該檔案即是已經過憑證簽署的 DLL。 *path_to_file* 可以是本機路徑或通往網路位置的 UNC 路徑。 您可在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 服務帳戶的安全性內容中存取這個檔案。 這個帳戶必須有所需的檔案系統權限。  

> [!IMPORTANT]
> [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 不支援從檔案或使用私密金鑰檔案建立憑證。
  
 BINARY = *asn_encoded_certificate*  
 指定為二進位常數的 ASN 編碼憑證位元組。  
 **適用對象**：[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 及更新版本。  
  
 WITH PRIVATE KEY  
 指定憑證的私密金鑰必須載入 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中。 從組件建立憑證時，這個子句無效。 若要載入從組件所建立憑證的私密金鑰，請使用 [ALTER CERTIFICATE](../../t-sql/statements/alter-certificate-transact-sql.md)。  
  
 FILE ='*path_to_private_key*'  
 指定通往私密金鑰的完整路徑 (包括檔案名稱)。 *path_to_private_key* 可以是本機路徑或通往網路位置的 UNC 路徑。 您可在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 服務帳戶的安全性內容中存取這個檔案。 這個帳戶必須有必要的檔案系統權限。  
  
> [!IMPORTANT]  
> 自主資料庫或 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 無法使用這個選項。  
  
 BINARY = *private_key_bits*  
 **適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。  
  
 指定為二進位常數的私密金鑰位元。 這些位元可以是加密形式。 如果加密的話，使用者必須提供解密密碼。 不會針對這個密碼執行密碼原則檢查。 私密金鑰位元應該採用 PVK 檔案格式。  
  
 DECRYPTION BY PASSWORD = '*key_password*'  
 指定解密從檔案擷取的私密金鑰時所需的密碼。 如果私密金鑰由 Null 密碼保護，則這個子句是選擇性的。 建議您不要將私密金鑰儲存至一個沒有密碼保護的檔案。 如果需要密碼但並未指定，則陳述式會失敗。  
  
 ENCRYPTION BY PASSWORD = '*password*'  
 指定用來加密私密金鑰的密碼。 請只在您要利用密碼來加密憑證時才使用這個選項。 如果省略這個子句，則會使用資料庫主要金鑰加密此私密金鑰。 *password* 必須符合執行 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體之電腦的 Windows 密碼原則需求。 如需詳細資訊，請參閱＜ [Password Policy](../../relational-databases/security/password-policy.md)＞。  
  
 SUBJECT = '*certificate_subject_name*'  
 「主旨」一詞是指憑證中繼資料的欄位，如 X.509 標準所定義。 主旨不應超過 64 個字元，並會對 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] on Linux 強制執行這項限制。 若是 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] on Windows，主旨長度最多可達 128 個字元。 超過 128 個字元的主旨在儲存至目錄時會被截斷，但包含憑證的二進位大型物件 (BLOB) 會保留完整的主旨名稱。  
  
 START_DATE = '*datetime*'  
 這是憑證生效的日期。 若未指定，會將 START_DATE 設為等於目前的日期。 START_DATE 為 UTC 時間，可以用任何可轉換成日期和時間的格式指定。  
  
 EXPIRY_DATE = '*datetime*'  
 這是憑證到期的日期。 若未指定，會將 EXPIRY_DATE 設為 START_DATE 之後一年的日期。 EXPIRY_DATE 為 UTC 時間，可以用任何可轉換成日期和時間的格式指定。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Service Broker 會檢查到期日。 使用憑證進行加密的備份也會檢查到期日，而且將不會允許使用已過期之憑證來建立新備份，但將會允許使用已過期的憑證來還原。 不過當憑證用於資料庫加密或 Always Encrypted 時，不會強制執行到期設定。  
  
 ACTIVE FOR BEGIN_DIALOG = { **ON** | OFF }  
 讓 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 對話交談的起始端能夠使用該憑證。 預設值是 ON。  
  
## <a name="remarks"></a>備註  
 憑證是遵照 X.509 標準及支援 X.509 V1 欄位的資料庫層級安全性實體。 `CREATE CERTIFICATE` 可以從檔案、二進位常數或組件載入憑證。 這個陳述式也可以產生金鑰組及建立自簽憑證。  
  
 私密金鑰必須為 \<= 2500 位元組的加密格式。 截至 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 止，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 產生的私密金鑰長度為 1024 個位元；從 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 開始則為 2048 位元。 從外部來源匯入的私密金鑰，其最小長度為 384 個位元，其最大長度為 4,096 個位元。 匯入的私密金鑰，其長度必須為 64 個位元的整數倍。 用於 TDE 的憑證限制在 3456 位元的私密金鑰大小。  
  
 系統會儲存憑證的完整「序號」，但只有前 16 個位元組會出現在 sys.certificates 目錄檢視中。  
  
 系統會儲存憑證的完整「簽發者」欄位，但只有前 884 個位元組會出現在 sys.certificates 目錄檢視中。  
  
 私密金鑰必須對應至 *certificate_name* 所指定的公開金鑰。  
  
 當您從容器建立憑證時，載入私密金鑰是選擇性的。 但是當 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 產生自簽憑證時，永遠會建立私密金鑰。 依預設，私密金鑰是利用資料庫主要金鑰來加密的。 如果資料庫主要金鑰不存在且未指定密碼，則陳述式會失敗。  
  
 若是使用資料庫主要金鑰來加密私密金鑰，則不需要 `ENCRYPTION BY PASSWORD` 選項。 只有要使用密碼加密私密金鑰時，才使用這個選項。 如果未指定密碼，則會利用資料庫主要金鑰加密憑證的私密金鑰。 如果無法開啟資料庫的主要金鑰，省略這個子句時會造成錯誤。  
  
 如果是利用資料庫主要金鑰加密私密金鑰，就不必指定解密密碼。  
  
> [!NOTE]  
> 用於加密及簽署的內建函數不會檢查憑證的到期日期。 這些函數的使用者必須決定何時檢查憑證到期日期。  
  
 您可以使用 [CERTENCODED &#40;Transact-SQL&#41;](../../t-sql/functions/certencoded-transact-sql.md) 和 [CERTPRIVATEKEY &#40;Transact-SQL&#41;](../../t-sql/functions/certprivatekey-transact-sql.md) 函式來建立憑證的二進位描述。 如需使用 **CERTPRIVATEKEY** 和 **CERTENCODED** 將憑證複製到其他資料庫的範例，請參閱 [CERTENCODED &#40;Transact-SQL&#41;](../../t-sql/functions/certencoded-transact-sql.md) 一文中的範例 B。  

[!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 已淘汰 MD2、MD4、MD5、SHA 和 SHA1 演算法。 在 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 之前，會使用 SHA1 建立自我簽署憑證。 從 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 開始，則使用 SHA2_256 建立自我簽署憑證。

## <a name="permissions"></a>權限  
 需要資料庫的 `CREATE CERTIFICATE` 權限。 只有 Windows 登入、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入，以及應用程式角色可以擁有憑證。 群組和角色無法擁有憑證。  
  
## <a name="examples"></a>範例  
  
### <a name="a-creating-a-self-signed-certificate"></a>A. 建立自我簽署憑證  
 下列範例會建立一個稱為 `Shipping04` 的憑證。 這個憑證的私密金鑰是利用密碼來保護的。  
  
```sql  
CREATE CERTIFICATE Shipping04   
   ENCRYPTION BY PASSWORD = 'pGFD4bb925DGvbd2439587y'  
   WITH SUBJECT = 'Sammamish Shipping Records',   
   EXPIRY_DATE = '20201031';  
GO  
```  
  
### <a name="b-creating-a-certificate-from-a-file"></a>B. 從檔案建立憑證  
 下列範例在資料庫中建立憑證，並從檔案載入金鑰組。  
  
```sql  
CREATE CERTIFICATE Shipping11   
    FROM FILE = 'c:\Shipping\Certs\Shipping11.cer'   
    WITH PRIVATE KEY (FILE = 'c:\Shipping\Certs\Shipping11.pvk',   
    DECRYPTION BY PASSWORD = 'sldkflk34et6gs%53#v00');  
GO   
```  

> [!IMPORTANT]
> [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 不支援從檔案建立憑證。
   
### <a name="c-creating-a-certificate-from-a-signed-executable-file"></a>C. 從簽署的可執行檔建立憑證  
  
```sql  
CREATE CERTIFICATE Shipping19   
    FROM EXECUTABLE FILE = 'c:\Shipping\Certs\Shipping19.dll';  
GO  
```  
  
 另外，您也可以從 `dll` 檔建立組件，然後從該組件建立憑證。  
  
```sql  
CREATE ASSEMBLY Shipping19   
    FROM ' c:\Shipping\Certs\Shipping19.dll'   
    WITH PERMISSION_SET = SAFE;  
GO  
CREATE CERTIFICATE Shipping19 FROM ASSEMBLY Shipping19;  
GO  
```  

> [!IMPORTANT]
> [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 不支援從檔案建立憑證。

> [!IMPORTANT]
> 從 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 開始，['CLR strict security'](../../database-engine/configure-windows/clr-strict-security.md) 伺服器組態選項可防止在未先為其設定安全性的情況下載入組件。 請載入憑證，從中建立登入，向該登入授與 `UNSAFE ASSEMBLY`，然後載入組件。

### <a name="d-creating-a-self-signed-certificate"></a>D. 建立自我簽署憑證  
 下列範例會建立稱為 `Shipping04` 的憑證，而不指定加密密碼。 這個範例可以搭配使用 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]。
  
```sql  
CREATE CERTIFICATE Shipping04   
   WITH SUBJECT = 'Sammamish Shipping Records';  
GO  
```  
  
## <a name="see-also"></a>另請參閱  
 [ALTER CERTIFICATE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-certificate-transact-sql.md)   
 [DROP CERTIFICATE &#40;Transact-SQL&#41;](../../t-sql/statements/drop-certificate-transact-sql.md)   
 [BACKUP CERTIFICATE &#40;Transact-SQL&#41;](../../t-sql/statements/backup-certificate-transact-sql.md)   
 [加密階層](../../relational-databases/security/encryption/encryption-hierarchy.md)   
 [EVENTDATA &#40;Transact-SQL&#41;](../../t-sql/functions/eventdata-transact-sql.md)   
 [CERTENCODED &#40;Transact-SQL&#41;](../../t-sql/functions/certencoded-transact-sql.md)   
 [CERTPRIVATEKEY &#40;Transact-SQL&#41;](../../t-sql/functions/certprivatekey-transact-sql.md)  
 [CERT_ID &#40;Transact-SQL&#41;](../../t-sql/functions/cert-id-transact-sql.md)  
 [CERTPROPERTY &#40;Transact-SQL&#41;](../../t-sql/functions/certproperty-transact-sql.md)  
  
  


