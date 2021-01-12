---
description: CREATE ASSEMBLY (Transact-SQL)
title: CREATE ASSEMBLY (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 09/07/2018
ms.prod: sql
ms.prod_service: sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- ASSEMBLY
- CREATE ASSEMBLY
- CREATE_ASSEMBLY_TSQL
- ASSEMBLY_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- assemblies [CLR integration], validating
- validating assemblies
- CREATE ASSEMBLY statement
- assemblies [CLR integration], creating
ms.assetid: d8d1d245-c2c3-4325-be52-4fc1122c2079
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: =azuresqldb-mi-current||>=sql-server-2016||>=sql-server-linux-2017
ms.openlocfilehash: 13b639ace60f98ce9ab55cc0280130131f6851ca
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98098402"
---
# <a name="create-assembly-transact-sql"></a>CREATE ASSEMBLY (Transact-SQL)
[!INCLUDE[tsql-appliesto-ss2008-asdbmi-xxxx-xxx-md](../../includes/tsql-appliesto-ss2008-asdbmi-xxxx-xxx-md.md )]

  建立一個 Managed 應用程式模組，其中所包含的類別中繼資料和 Managed 程式碼是 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體中的一個物件。 您可以參考這個模組，在資料庫中建立 Common Language Runtime (CLR) 函數、預存程序、觸發程序、使用者定義彙總以及使用者定義型別。  
  
> [!WARNING]
>  CLR 使用 .NET Framework 中的程式碼存取安全性 (CAS)，而這不再作為安全性界限受支援。 使用 `PERMISSION_SET = SAFE` 所建立的 CLR 組件可以存取外部系統資源、呼叫 Unmanaged 程式碼，以及取得系統管理員權限。 從 [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] 開始，引進稱為 `clr strict security` 的 `sp_configure` 選項，來增強 CLR 組件的安全性。 `clr strict security` 會依預設啟用，且將 `SAFE` 與 `EXTERNAL_ACCESS` 組件視作已標記為 `UNSAFE` 一樣。 可以基於回溯相容性停用 `clr strict security` 選項，但不建議這麼做。 Microsoft 建議透過具有已獲授與 master 資料庫中 `UNSAFE ASSEMBLY` 權限之對應登入的憑證或非對稱金鑰簽署所有組件。 如需詳細資訊，請參閱 [CLR 嚴格安全性](../../database-engine/configure-windows/clr-strict-security.md)。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
CREATE ASSEMBLY assembly_name  
[ AUTHORIZATION owner_name ]  
FROM { <client_assembly_specifier> | <assembly_bits> [ ,...n ] }  
[ WITH PERMISSION_SET = { SAFE | EXTERNAL_ACCESS | UNSAFE } ]  
[ ; ]  
<client_assembly_specifier> :: =  
        '[\\computer_name\]share_name\[path\]manifest_file_name'  
  | '[local_path\]manifest_file_name'  
  
<assembly_bits> :: =  
{ varbinary_literal | varbinary_expression }  
```  
  
## <a name="arguments"></a>引數  
 *assembly_name*  
 這是組件的名稱。 這個名稱在資料庫中必須是唯一名稱，而且是有效的[識別碼](../../relational-databases/databases/database-identifiers.md)。  
  
 AUTHORIZATION *owner_name*  
 指定身為組件擁有者的使用者或角色的名稱。 *owner_name* 必須是目前使用者所屬的角色名稱，或者目前使用者必須具有 *owner_name* 的 IMPERSONATE 權限。 若未指定，擁有權便歸目前使用者。  
  
 \<client_assembly_specifier>  
指定正在上傳之組件所在的本機路徑或網路位置，以及對應於該組件的資訊清單檔案名稱。  \<client_assembly_specifier> 能用變數以固定字串或會評估為固定字串來表示。 CREATE ASSEMBLY 不支援載入多模組組件。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 也會在同一個位置尋找這個組件的任何相依組件，同時以同一位擁有者做為根層級組件來上傳它們。 如果找不到這些相依組件，而且它們也尚未載入目前資料庫，CREATE ASSEMBLY 便會失敗。 如果相依組件已經載入到目前資料庫中，則那些組件的擁有者，必須與剛建立組件的擁有者一樣。

> [!IMPORTANT]
> Azure SQL Database 不支援從檔案建立組件。
  
 如果系統正在模擬登入的使用者，您就無法指定 \<client_assembly_specifier>。  
  
 \<assembly_bits>  
 這是構成組件及其相依組件的二進位值清單。 清單中的第一個值，被視為根層級組件。 對應於相依組件的值，可以採用任何順序提供。 任何不對應於根組件相依性的值，則略過不管。  
  
> [!NOTE]  
>  自主資料庫無法使用這個選項。  
  
 *varbinary_literal*  
 這是 **varbinary** 常值。  
  
 *varbinary_expression*  
 這是類型 **varbinary** 的運算式。  
  
 PERMISSION_SET { **SAFE** | EXTERNAL_ACCESS | UNSAFE }  
> [!IMPORTANT]
>  `PERMISSION_SET` 選項會受到開啟警告中所述的 `clr strict security` 選項影響。 當 `clr strict security` 已啟用時，所有組件會被視為 `UNSAFE`。
 
 當 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 存取組件時，指定一組授與組件的程式碼存取權限。 若未指定，便採用 SAFE 作為預設值。  
  
 我們建議您使用 SAFE。 SAFE 是最有限的權限集合。 具有 SAFE 權限的組件所執行的程式碼，不能存取外部系統資源，例如，檔案、網路、環境變數或登錄。  
  
 EXTERNAL_ACCESS 可讓組件存取特定的外部系統資源，例如，檔案、網路、環境變數和登錄。  
  
> [!NOTE]  
>  自主資料庫無法使用這個選項。  
  
 UNSAFE 可讓組件無限制地存取 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體內外的資源。 從 UNSAFE 組件內執行的程式碼，可以呼叫 Unmanaged 程式碼。  
  
> [!NOTE]  
>  自主資料庫無法使用這個選項。  
  
> [!IMPORTANT]  
>  對於執行計算和資料管理工作，而不存取 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體外之資源的組件，建議您採用 SAFE 做為權限設定。  
>   
>  對於存取 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體外之資源的組件，我們建議您使用 EXTERNAL_ACCESS。 EXTERNAL_ACCESS 組件包含 SAFE 組件的可靠性和延展性保護，但是從安全性的角度看來，卻與 UNSAFE 組件很相似。 這是因為 EXTERNAL_ACCESS 組件中的程式碼，依預設是以 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 服務帳戶執行，並且以該帳戶存取外部資源，除非程式碼明確模擬呼叫端。 因此，建立 EXTERNAL_ACCESS 組件的權限，只能授與可以 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 服務帳戶來執行程式碼的可靠登入。 如需模擬的相關資訊，請參閱 [CLR 整合安全性](../../relational-databases/clr-integration/security/clr-integration-security.md)。  
>   
>  指定 UNSAFE 可讓組件中的程式碼，在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 處理序空間執行可能會犧牲 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 效能的作業。 UNSAFE 組件也可能會破壞 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 或 Common Language Runtime 的安全性系統。 UNSAFE 權限應該只授與受到高度信任的組件。 只有 **sysadmin** 固定伺服器角色的成員，才能夠建立及變更 UNSAFE 組件。  
  
 如需有關組件權限集合的詳細資訊，請參閱[設計組件](../../relational-databases/clr-integration/assemblies-designing.md)。  
  
## <a name="remarks"></a>備註  
 CREATE ASSEMBLY 會上傳之前從 Managed 程式碼編譯為 .dll 檔、用於 SQL Server 執行個體內部的組件。  
 
啟用時，會在執行階段忽略 `CREATE ASSEMBLY` 和 `ALTER ASSEMBLY` 陳述式中的 `PERMISSION_SET` 選項，但在中繼資料中會保留 `PERMISSION_SET` 選項。 忽略此選項可將中斷現有程式碼陳述式最小化。
 
  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 不允許註冊具有相同名稱、文化特性及公開金鑰之不同版本的組件。  
  
當您嘗試存取 \<client_assembly_specifier> 中指定的組件時，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會模擬目前 Windows 登入的資訊安全內容。 由於委派限制之故，如果 \<client_assembly_specifier> 指定網路位置 (UNC 路徑)，目前登入的模擬並不會在網路位置發揮作用。 此時就得利用 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 服務帳戶的安全性內容來完成存取作業。 如需詳細資訊，請參閱[認證 &#40;資料庫引擎&#41;](../../relational-databases/security/authentication-access/credentials-database-engine.md)。
  
 除了 *assembly_name* 所指定的根組件之外，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 還會嘗試上傳任何正在上傳之根組件所參考的組件。 如果因為之前 CREATE ASSEMBLY 陳述式的關係，所參考的組件已經上傳到資料庫了，就不會上傳這個組件，但是根組件仍然能夠使用它。 如果之前相依組件並未上傳，但是 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 在來源目錄找不到其資訊清單檔，CREATE ASSEMBLY 便會傳回錯誤。  
  
 如果由根組件參考的任何相依組件尚未在資料庫中，而且以隱含方式與根組件一起載入，則它們的權限集合與根層級組件相同。 如果相依組件必須以與根層級組件不同的權限集合加以建立，必須利用適當的權限集合，在根層級組件之前明確上傳。  
  
## <a name="assembly-validation"></a>組件驗證  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會在 CREATE ASSEMBLY 陳述式上傳的組件二進位執行檢查，來保證下列各項：  
  
-   組件二進位具有有效的中繼資料和程式碼區段，格式良好，而且程式碼區段也具有有效的 Microsoft 中繼語言 (MSIL) 指示。  
  
-   它所參考的系統組件集，是下列其中一個 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 支援的組件：Microsoft.Visualbasic.dll、Mscorlib.dll、System.Data.dll、System.dll、System.Xml.dll、Microsoft.Visualc.dll、Custommarshallers.dll、System.Security.dll、System.Web.Services.dll、System.Data.SqlXml.dll、System.Core.dll 和 System.Xml.Linq.dll。 也可以參考其他系統組件，但是它們必須在資料庫中明確註冊。  
  
-   以 SAFE 或 EXTERNAL ACCESS 權限集合加以建立的組件：  
  
    -   組件程式碼應該是類型安全的程式碼。 類型安全性是藉由對組件執行 Common Language Runtime 驗證器而完成的。  
  
    -   除非被標示為唯讀，否則組件的類別不應包含任何靜態資料成員。  
  
    -   組件的類別不能包含 finalizer 方法。  
  
    -   組件的類別或方法只能以允許的程式碼屬性加以註解。 如需詳細資訊，請參閱 [CLR 常式的自訂屬性](../../relational-databases/clr-integration/database-objects/clr-integration-custom-attributes-for-clr-routines.md)。  
  
 除了之前在 CREATE ASSEMBLY 執行時所執行的檢查之外，在執行組件程式碼時，還會執行其他檢查：  
  
-   如果組件的權限集合不含特定程式碼存取權限，則可能無法呼叫某些必須具備該權限的 [!INCLUDE[msCoName](../../includes/msconame-md.md)][!INCLUDE[dnprdnshort](../../includes/dnprdnshort-md.md)] API。  
  
-   如果是 SAFE 和 EXTERNAL_ACCESS 組件，則完全無法呼叫以特定 HostProtectionAttributes 加以註解的 [!INCLUDE[dnprdnshort](../../includes/dnprdnshort-md.md)] API。  
  
 如需詳細資訊，請參閱[設計組件](../../relational-databases/clr-integration/assemblies-designing.md)。  
  
## <a name="permissions"></a>權限  
 需要 CREATE ASSEMBLY 權限。  
  
 如果指定 PERMISSION_SET = EXTERNAL_ACCESS，則需要伺服器的 **EXTERNAL ACCESS ASSEMBLY** 權限。 如果未指定 PERMISSION_SET = UNSAFE，則伺服器上需要 **UNSAFE ASSEMBLY** 權限。  
  
 如果任何由即將上傳之組件所參考的組件已經在資料庫中，則使用者必須是這些組件的擁有者才行。 若要利用檔案路徑來上傳組件，則目前使用者必須是 Windows 驗證的登入，或是 **sysadmin** 固定伺服器角色的成員。 執行 CREATE ASSEMBLY 的使用者 Windows 登入，必須具有共用區以及正在載入陳述式中之檔案的讀取權限。  

### <a name="permissions-with-clr-strict-security"></a>使用 CLR 嚴格安全性的權限    
啟用 `CLR strict security` 時，需要有下列權限才能建立 CLR 組件：

- 使用者必須具有 `CREATE ASSEMBLY` 權限  
- 而且，下列其中一個條件也必須成立：  
  - 組件是使用憑證或非對稱金鑰進行簽署，該金鑰有具有伺服器 `UNSAFE ASSEMBLY` 權限的對應登入。 建議簽署組件。  
  - 資料庫的 `TRUSTWORTHY` 屬性設定為 `ON`，而且具有伺服器之 `UNSAFE ASSEMBLY` 權限的登入擁有資料庫。 不建議使用此選項。  
  
 如需有關組件權限集合的詳細資訊，請參閱[設計組件](../../relational-databases/clr-integration/assemblies-designing.md)。  
  
## <a name="examples"></a>範例  
  
### <a name="example-a-creating-an-assembly-from-a-dll"></a>範例 A：從 dll 建立組件  
  
**適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。  
  
 下列範例假設 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 範例是安裝在機電腦的預設位置，而且 HelloWorld.csproj 範例應用程式已經編譯。 如需詳細資訊，請參閱 [Hello World 範例](/previous-versions/sql/sql-server-2016/ff878250(v=sql.130))。  
  
```sql  
CREATE ASSEMBLY HelloWorld   
FROM '<system_drive>:\Program Files\Microsoft SQL Server\100\Samples\HelloWorld\CS\HelloWorld\bin\debug\HelloWorld.dll'  
WITH PERMISSION_SET = SAFE;  
```  

> [!IMPORTANT]
> Azure SQL Database 不支援從檔案建立組件。
  
### <a name="example-b-creating-an-assembly-from-assembly-bits"></a>範例 B：從組件位元建立組件  
  
**適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。  
  
 使用您的組件位元取代範例位元 (不完整或無效)。  
  
```sql  
CREATE ASSEMBLY HelloWorld  
    FROM 0x4D5A900000000000  
WITH PERMISSION_SET = SAFE;  
```  
  
## <a name="see-also"></a>另請參閱  
 [ALTER ASSEMBLY &#40;Transact-SQL&#41;](../../t-sql/statements/alter-assembly-transact-sql.md)   
 [DROP ASSEMBLY &#40;Transact-SQL&#41;](../../t-sql/statements/drop-assembly-transact-sql.md)   
 [CREATE FUNCTION &#40;Transact-SQL&#41;](../../t-sql/statements/create-function-transact-sql.md)   
 [CREATE PROCEDURE &#40;Transact-SQL&#41;](../../t-sql/statements/create-procedure-transact-sql.md)   
 [CREATE TRIGGER &#40;Transact-SQL&#41;](../../t-sql/statements/create-trigger-transact-sql.md)   
 [CREATE TYPE &#40;Transact-SQL&#41;](../../t-sql/statements/create-type-transact-sql.md)   
 [CREATE AGGREGATE &#40;Transact-SQL&#41;](../../t-sql/statements/create-aggregate-transact-sql.md)   
 [EVENTDATA &#40;Transact-SQL&#41;](../../t-sql/functions/eventdata-transact-sql.md)   
 [通用語言執行平台 &#40;CLR&#41; 整合的使用案例和範例](/previous-versions/sql/sql-server-2016/ms131078(v=sql.130))  
  
