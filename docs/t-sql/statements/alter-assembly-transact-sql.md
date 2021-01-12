---
description: ALTER ASSEMBLY (Transact-SQL)
title: ALTER ASSEMBLY (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 09/07/2017
ms.prod: sql
ms.prod_service: sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- ALTER_ASSEMBLY_TSQL
- ALTER ASSEMBLY
dev_langs:
- TSQL
helpviewer_keywords:
- assemblies [CLR integration], modifying
- refreshing assemblies
- assemblies [CLR integration], versioning
- assemblies [CLR integration], adding files
- modifying assemblies
- adding files
- ALTER ASSEMBLY statement
ms.assetid: 87bca678-4e79-40e1-bb8b-bd5ed8f34853
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: dda1de9a2eb4628765333651c7d074b0c38551db
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98100906"
---
# <a name="alter-assembly-transact-sql"></a>ALTER ASSEMBLY (Transact-SQL)
[!INCLUDE [SQL Server - ASDBMI](../../includes/applies-to-version/sql-asdbmi.md)]

  修改組件的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 目錄屬性來改變組件。 ALTER ASSEMBLY 會將其更新為保留其實作的 [!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[dnprdnshort](../../includes/dnprdnshort-md.md)] 模組最新複本，且新增或移除其相關檔案。 組件是利用 [CREATE ASSEMBLY](../../t-sql/statements/create-assembly-transact-sql.md) 加以建立的。  

> [!WARNING]
>  CLR 使用 .NET Framework 中的程式碼存取安全性 (CAS)，而這不再作為安全性界限受支援。 使用 `PERMISSION_SET = SAFE` 所建立的 CLR 組件可以存取外部系統資源、呼叫 Unmanaged 程式碼，以及取得系統管理員權限。 從 [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] 開始，引進稱為 `clr strict security` 的 `sp_configure` 選項，來增強 CLR 組件的安全性。 `clr strict security` 會依預設啟用，且將 `SAFE` 與 `EXTERNAL_ACCESS` 組件視作已標記為 `UNSAFE` 一樣。 可以基於回溯相容性停用 `clr strict security` 選項，但不建議這麼做。 Microsoft 建議透過具有已獲授與 master 資料庫中 `UNSAFE ASSEMBLY` 權限之對應登入的憑證或非對稱金鑰簽署所有組件。 如需詳細資訊，請參閱 [CLR 嚴格安全性](../../database-engine/configure-windows/clr-strict-security.md)。  

 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
ALTER ASSEMBLY assembly_name  
    [ FROM <client_assembly_specifier> | <assembly_bits> ]  
    [ WITH <assembly_option> [ ,...n ] ]  
    [ DROP FILE { file_name [ ,...n ] | ALL } ]  
    [ ADD FILE FROM   
    {   
        client_file_specifier [ AS file_name ]   
      | file_bits AS file_name   
    } [,...n ]   
    ] [ ; ]  
<client_assembly_specifier> :: =  
    '\\computer_name\share-name\[path\]manifest_file_name'  
  | '[local_path\]manifest_file_name'  
  
<assembly_bits> :: =  
    { varbinary_literal | varbinary_expression }  
  
<assembly_option> :: =  
    PERMISSION_SET = { SAFE | EXTERNAL_ACCESS | UNSAFE }   
  | VISIBILITY = { ON | OFF }  
  | UNCHECKED DATA  
  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *assembly_name*  
 這是您要修改的組件名稱。 *assembly_name* 必須已存在於資料庫中。  
  
 FROM \<client_assembly_specifier> | \<assembly_bits>  
 將組件更新為保留其實作的 [!INCLUDE[dnprdnshort](../../includes/dnprdnshort-md.md)] 模組最新備份。 只有在沒有與指定組件相關的檔案時，才能使用這個選項。  
  
 \<client_assembly_specifier> 會指定正在重新整理的組件所在網路或本機位置。 網路位置包含電腦名稱、共用名稱，以及該共用內的路徑。 *manifest_file_name* 指定包含組件資訊清單的檔案名稱。  

> [!IMPORTANT]
> Azure SQL Database 不支援參考檔案。
  
 \<assembly_bits> 是組件的二進位值。  
  
 必須針對同樣需要更新的每一個相依組件分別發出 ALTER ASSEMBLY 陳述式。  
  
 PERMISSION_SET = { SAFE | EXTERNAL_ACCESS | UNSAFE }   
> [!IMPORTANT]
>  `PERMISSION_SET` 選項會受到開啟警告中所述的 `clr strict security` 選項影響。 當 `clr strict security` 已啟用時，所有組件會被視為 `UNSAFE`。  
>  指定組件的 [!INCLUDE[dnprdnshort](../../includes/dnprdnshort-md.md)] 程式碼存取權限集合屬性。 如需有關此屬性的詳細資訊，請參閱 [CREATE ASSEMBLY &#40;Transact-SQL&#41;](../../t-sql/statements/create-assembly-transact-sql.md)。  
> 
> [!NOTE]
>  自主資料庫無法使用 EXTERNAL_ACCESS 和 UNSAFE 選項。  
  
 VISIBILITY = { ON | OFF }  
 指出是否會顯示組件，以對該組件建立 Common Language Runtime (CLR) 函數、預存程序、觸發程序、使用者自訂類型以及使用者定義彙總函式。 如果是設為 OFF，則該組件是專供其他組件呼叫。 如果已對該組件建立現有的 CLR 資料庫物件，就不能變更該組件的可見性。 依預設，*assembly_name* 所參考的任何組件在上傳時都不會顯示。  
  
 UNCHECKED DATA  
 依預設，如果必須驗證個別資料表資料列的一致性，ALTER ASSEMBLY 便會失敗。 這個選項可讓您利用 DBCC CHECKTABLE 延後檢查。 如果指定的話，即使資料庫中有包含下列各項的資料表，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 也會執行 ALTER ASSEMBLY 陳述式：  
  
-   透過 [!INCLUDE[tsql](../../includes/tsql-md.md)] 函數或方法，直接或間接參考組件方法的保存計算資料行。  
  
-   直接或間接參考組件方法的 CHECK 條件約束。  
  
-   相依於組件的 CLR 使用者定義型別資料行，以及實作 **UserDefined** (非 **原生**) 序列化格式之類型的資料行。  
  
-   參考利用 WITH SCHEMABINDING 建立之檢視的 CLR 使用者自訂類型資料行。  
  
 如果有任何 CHECK 條件約束存在，都會被停用，並且標示為未受信任。 任何包含相依於組件之資料行的資料表，都會標示為包含尚未檢查的資料，直到明確檢查過那些資料表為止。  
  
 只有 **db_owner** 和 **db_ddlowner** 固定資料庫角色的成員，才能夠指定這個選項。  
  
 需要 **ALTER ANY SCHEMA** 權限來指定此選項。  
  
 如需詳細資訊，請參閱[實作組件](../../relational-databases/clr-integration/assemblies-implementing.md)。  
  
 [ DROP FILE { *file_name*[ **,** _...n_] | ALL } ]  
 從資料庫移除與該組件相關聯的檔案名稱，或是所有與該組件相關聯的檔案。 如果使用底下的 ADD FILE，則會先執行 DROP FILE。 此舉可讓您取代同名的檔案。  
  
> [!NOTE]  
>  此選項在自主資料庫或 Azure SQL Database 中無法使用。  
  
 [ ADD FILE FROM { *client_file_specifier* [ AS *file_name*] | *file_bits* AS *file_name*}  
 將與組件相關聯的檔案 (例如，原始程式碼、偵錯檔案或其他相關資訊)，上傳到伺服器，並顯示在 **sys.assembly_files** 目錄檢視中。 *client_file_specifier* 會指定要上傳檔案的位置。 您可以改用 *file_bits* 來指定構成該檔的二進位值清單。 *file_name* 會指定該檔案儲存在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體所用的名稱。 如果已指定 *file_bits*，則必須指定 *file_name*，而如果已指定 *client_file_specifier*，則為選用。 如果未指定 *file_name*，*client_file_specifier* 的 file_name 部分會作為 *file_name* 使用。  
  
> [!NOTE]  
>  此選項在自主資料庫或 Azure SQL Database 中無法使用。  
  
## <a name="remarks"></a>備註  
 ALTER ASSEMBLY 不會中斷正在修改的組件中，目前正在執行程式碼的執行中工作階段。 目前工作階段是利用組件的未修改位元，來完成執行作業。  
  
 如果指定了 FROM 子句，ALTER ASSEMBLY 便會以所提供的模組最新備份來更新組件。 由於 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體中，可能已經針對該組件定義了 CLR 函數、預存程序、觸發程序、資料類型和使用者定義彙總函式，因此 ALTER ASSEMBLY 陳述式會將它們重新繫結到組件的最新實作中。 若要重新繫結，則對應到 CLR 函數、預存程序和觸發程序的方法，必須仍以相同的簽章留在修改後的組件中。 實作 CLR 使用者自訂類型和使用者定義彙總函式的類別，仍然必須滿足身為使用者自訂類型或彙總函式的需求。  
  
> [!CAUTION]  
>  在未指定 WITH UNCHECKED DATA 的情況下，如果新組件版本影響資料表、索引或其他永續性站台中現有的資料，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 將嘗試阻止 ALTER ASSEMBLY 執行。 不過，當 CLR 組件更新時，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 並不保證計算資料行、索引、索引檢視或運算式都會與基礎常式及類型保持一致。 執行 ALTER ASSEMBLY 時請特別小心，以確定運算式的結果與儲存在組件中以該運算式作為基礎的值沒有不相符的情況。  
  
 ALTER ASSEMBLY 會變更組件版本。 組件的文化和公開金鑰 Token 仍然不變。  
  
 ALTER ASSEMBLY 陳述式不能用來變更下列各項：  
  
-   [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體中，參考該組件的 CLR 函數、彙總函式、預存程序和觸發程序簽章。 如果 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 無法將 [!INCLUDE[dnprdnshort](../../includes/dnprdnshort-md.md)] 中的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 資料庫物件，與新版組件重新繫結時，ALTER ASSEMBLY 就會失敗。  
  
-   組件中從其他組件呼叫的方法簽章。  
  
-   相依於組件的其他組件清單，可在該組件的 **DependentList** 屬性中參考這份清單。  
  
-   方法的編製索引功能，除非沒有直接或間接相依於該方法的索引或保存計算資料行。  
  
-   CLR 資料表值函式的 **FillRow** 方法名稱屬性。  
  
-   使用者定義彙總的 **Accumulate** 和 **Terminate** 方法簽章。  
  
-   系統組件。  
  
-   系統擁有權。 改為使用 [ALTER AUTHORIZATION &#40;Transact-SQL&#41;](../../t-sql/statements/alter-authorization-transact-sql.md)。  
  
 此外，針對實作使用者自訂類型的組件，ALTER ASSEMBLY 只能用來進行下列變更：  
  
-   修改使用者自訂類型類別的公用方法，只要簽章或屬性沒有變更即可。  
  
-   加入新的公用方法。  
  
-   以任何方式修改私用方法。  
  
 包含在原生序列化使用者自訂類型 (包括資料成員或基底類別) 中的欄位，不能使用 ALTER ASSEMBLY 加以變更。 所有其他變更亦不受支援。  
  
 如果未指定 ADD FILE FROM，ALTER ASSEMBLY 便會卸除任何與該組件相關聯的檔案。  
  
 如果執行 ALTER ASSEMBLY 時，沒有 UNCHECKED 資料子句，則會進行檢查，驗證新組件版本沒有影響資料表中現有的資料。 此舉可能會影響效能，這一點要看必須檢查的資料量而定。  
  
## <a name="permissions"></a>權限  
 需要組件的 ALTER 權限。 其他需求如下：  
  
-   若要變更現有權限集合為 EXTERNAL_ACCESS 的組件，則需要伺服器的 **EXTERNAL ACCESS ASSEMBLY** 權限。  
  
-   若要變更現有權限集合為 UNSAFE 的組件，則需要伺服器的 **UNSAFE ASSEMBLY** 權限。  
  
-   若要將組件的權限集合改為 EXTERNAL_ACCESS，則需要伺服器的 **EXTERNAL ACCESS ASSEMBLY** 權限。  
  
-   若要將組件的權限集合改為 UNSAFE，則需要伺服器的 **UNSAFE ASSEMBLY** 權限。  
  
-   指定 WITH UNCHECKED DATA 需要 **ALTER ANY SCHEMA** 權限。  


### <a name="permissions-with-clr-strict-security"></a>使用 CLR 嚴格安全性的權限    
啟用 `CLR strict security` 時，需要有下列權限才能變更 CLR 組件：

- 使用者必須具有 `ALTER ASSEMBLY` 權限  
- 而且，下列其中一個條件也必須成立：  
  - 組件是使用憑證或非對稱金鑰進行簽署，該金鑰有具有伺服器 `UNSAFE ASSEMBLY` 權限的對應登入。 建議簽署組件。  
  - 資料庫的 `TRUSTWORTHY` 屬性設定為 `ON`，而且具有伺服器之 `UNSAFE ASSEMBLY` 權限的登入擁有資料庫。 不建議使用此選項。  
  
  
 如需有關組件權限集合的詳細資訊，請參閱[設計組件](../../relational-databases/clr-integration/assemblies-designing.md)。  
  
## <a name="examples"></a>範例  
  
### <a name="a-refreshing-an-assembly"></a>A. 重新整理組件  
 下列範例會將組件 `ComplexNumber` 更新為保留其實作的 [!INCLUDE[dnprdnshort](../../includes/dnprdnshort-md.md)] 模組最新副本。  
  
> [!NOTE]  
>  您可以執行 UserDefinedDataType 範例指令碼，來建立組件 `ComplexNumber`。 如需相關資訊，請參閱[使用者定義型別](/previous-versions/sql/sql-server-2016/ms131078(v=sql.130))。  
  
 ```sql
 ALTER ASSEMBLY ComplexNumber 
 FROM 'C:\Program Files\Microsoft SQL Server\130\Tools\Samples\1033\Engine\Programmability\CLR\UserDefinedDataType\CS\ComplexNumber\obj\Debug\ComplexNumber.dll' 
  ```

> [!IMPORTANT]
> Azure SQL Database 不支援參考檔案。

### <a name="b-adding-a-file-to-associate-with-an-assembly"></a>B. 加入檔案，與組件建立關聯  
 下列範例會上傳即將與組件 `Class1.cs` 建立關聯的原始程式碼檔案 `MyClass`。 這個範例假設組件 `MyClass` 已在資料庫中建立。  
  
```sql  
ALTER ASSEMBLY MyClass   
ADD FILE FROM 'C:\MyClassProject\Class1.cs';  
```  

> [!IMPORTANT]
> Azure SQL Database 不支援參考檔案。

### <a name="c-changing-the-permissions-of-an-assembly"></a>C. 變更組件的權限  
 下列範例會將組件 `ComplexNumber` 的權限集合，從 SAFE 改為 `EXTERNAL ACCESS`。  
  
```sql  
ALTER ASSEMBLY ComplexNumber WITH PERMISSION_SET = EXTERNAL_ACCESS;  
```  
  
## <a name="see-also"></a>另請參閱  
 [CREATE ASSEMBLY &#40;Transact-SQL&#41;](../../t-sql/statements/create-assembly-transact-sql.md)   
 [DROP ASSEMBLY &#40;Transact-SQL&#41;](../../t-sql/statements/drop-assembly-transact-sql.md)   
 [EVENTDATA &#40;Transact-SQL&#41;](../../t-sql/functions/eventdata-transact-sql.md)  
  
