---
title: SqlPackage 擷取
description: 了解如何使用 SqlPackage.exe Extract 將資料庫開發工作自動化。 檢視範例及可用的參數、屬性和 SQLCMD 變數。
ms.prod: sql
ms.prod_service: sql-tools
ms.technology: tools-other
ms.topic: conceptual
ms.assetid: 198198e2-7cf4-4a21-bda4-51b36cb4284b
author: dzsquared
ms.author: drskwier
ms.reviewer: maghan; sstein
ms.date: 12/11/2020
ms.openlocfilehash: abefb39814213426d863fa3839c4095eadc82249
ms.sourcegitcommit: 866554663ca3191748b6e4eb4d8d82fa58c4e426
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97577807"
---
# <a name="sqlpackage-extract-parameters-and-properties"></a>SqlPackage Extract 參數與屬性
SqlPackage.exe 的 Extract 動作會建立從 SQL Server 或 Azure SQL Database 到 DACPAC 套件 (.dacpac 檔案) 的即時資料庫結構描述。 根據預設，資料不會包含在 .dacpac 檔案中。 若要包含資料，請使用 [Export 動作](sqlpackage-export.md)。 

## <a name="command-line-syntax"></a>命令列語法

**SqlPackage.exe** 會使用命令列上指定的參數、屬性和 SQLCMD 變數來起始指定的動作。  
  
```bash
SqlPackage {parameters}{properties}{SQLCMD Variables}  
```

> [!NOTE]
> 當擷取含有密碼認證 (例如，SQL 驗證使用者) 的資料庫時，該密碼會取代為具有適當複雜性的不同密碼。 SqlPackage 或 DacFx 使用者應該在發佈 Dacpac 之後變更密碼。

## <a name="parameters-for-the-extract-action"></a>Extract 動作的參數

|參數|簡短形式|值|描述|
|---|---|---|---|
|**/Action:**|**/a**|Extract|指定要執行的動作。 |
|**/AccessToken:**|**/at**|{string}| 根據要在連線到目標資料庫時使用的驗證存取權杖，來指定權杖。 |
|**/Diagnostics:**|**/d**|{True&#124;False}|指定診斷記錄是否輸出到主控台。 預設為 False。 |
|**/DiagnosticsFile:**|**/df**|{string}|指定要儲存診斷記錄的檔案。 |
|**/MaxParallelism:**|**/mp**|{int}| 指定針對資料庫執行之並行作業的平行處理原則的程度。 預設值為 8。 |
|**/OverwriteFiles:**|**/of**|{True&#124;False}|指定 sqlpackage.exe 是否應該覆寫現有的檔案。 指定 false 會導致 sqlpackage.exe 在遇到現有的檔案時中止動作。 預設值是 True。 |
|**/Properties:**|**/p**|{PropertyName}={Value}|指定動件特定屬性的名稱/值對：{PropertyName}={Value}。 請參閱特定動作的說明，以便查看該動作的屬性名稱。 範例：sqlpackage.exe /Action:Extract /?。 |
|**/Quiet:**|**/q**|{True&#124;False}|指定是否隱藏詳細的意見反應。 預設為 False。 |
|**/SourceConnectionString:**|**/scs**|{string}|指定來源資料庫的有效 SQL Server/Azure 連接字串。 如果指定了此參數，就應該以獨佔方式將其用於所有其他來源參數。 |
|**/SourceDatabaseName:**|**/sdn**|{string}|定義來源資料庫的名稱。 |
|**/SourceEncryptConnection:**|**/sec**|{True&#124;False}|指定 SQL 加密是否應該用於來源資料庫連接。 |
|**/SourcePassword:**|**/sp**|{string}|若為 SQL Server 驗證案例，則定義要用來存取來源資料庫的密碼。 |
|**/SourceServerName：**|**/ssn**|{string}|定義裝載來源資料庫的伺服器名稱。 |
|**/SourceTimeout:**|**/st**|{int}|指定建立來源資料庫連線的逾時 (以秒為單位)。 |
|**/SourceTrustServerCertificate:**|**/stsc**|{True&#124;False}|指定是否要使用 TLS 來加密來源資料庫連線並且略過驗證信任的憑證鏈結。 |
|**/SourceUser:**|**/su**|{string}|若為 SQL Server 驗證案例，則定義要用來存取來源資料庫的 SQL Server 使用者。 |
|**/TargetFile:**|**/tf**|{string}| 指定作為動作目標使用的目標檔案 (即 .dacpac 檔案)，而非資料庫。 如果使用此參數，其他目標參數都應該無效。 這個參數對僅支援資料庫目標的動作而言應無效。| 
|**/TenantId:**|**/tid**|{string}|表示 Azure AD 租用戶識別碼或網域名稱。 支援來賓或匯入的 Azure AD 使用者以及 outlook.com、hotmail.com 或 live.com 等 Microsoft 帳戶時，這是必要選項。 假設已驗證使用者是此 AD 的原生使用者，則若省略此參數，即會使用 Azure AD 的預設租用戶識別碼。 不過，在此情況下，不支援在此 Azure AD 中所裝載任何來賓或匯入的使用者及/或 Microsoft 帳戶，且作業會失敗。 <br/> 如需 Active Directory 通用驗證的詳細資訊，請參閱 [SQL Database 和 Azure Synapse Analytics 的通用驗證 (MFA 的 SSMS 支援)](/azure/sql-database/sql-database-ssms-mfa-authentication)。|
|**/UniversalAuthentication:**|**/ua**|{True&#124;False}|指定是否應該使用通用驗證。 設定為 True 時，即會啟用支援 MFA 的互動式驗證通訊協定。 此選項也可以用於不使用 MFA 的 Azure AD 驗證，使用需要使用者輸入其使用者名稱和密碼或整合式驗證 (Windows 認證) 的互動式通訊協定。 當 /UniversalAuthentication 設定為 True 時，SourceConnectionString (/scs) 中不能指定任何 Azure AD 驗證。 當 /UniversalAuthentication 設定為 False 時， SourceConnectionString (/scs) 中必須指定 Azure AD 驗證。 <br/> 如需 Active Directory 通用驗證的詳細資訊，請參閱 [SQL Database 和 Azure Synapse Analytics 的通用驗證 (MFA 的 SSMS 支援)](/azure/sql-database/sql-database-ssms-mfa-authentication)。|

## <a name="properties-specific-to-the-extract-action"></a>Extract 動作的特定屬性

|屬性|值|描述|
|---|---|---|
|**/p:**|CommandTimeout=(INT32 '60')|以秒為單位指定對 SQL Server 執行查詢時的命令逾時。|
|**/p:**|DacApplicationDescription=(STRING)|定義要儲存在 DACPAC 中繼資料中的應用程式描述。|
|**/p:**|DacApplicationName=(STRING)|定義要儲存在 DACPAC 中繼資料中的應用程式名稱。 預設值為資料庫名稱。|
|**/p:**|DacMajorVersion=(INT32 '1')|定義要儲存在 DACPAC 中繼資料中的主要版本。|
|**/p:**|DacMinorVersion=(INT32 '0')|定義要儲存在 DACPAC 中繼資料中的次要版本。|
|**/p:**|DatabaseLockTimeout=(INT32 '60')| 指定對 SQLServer 執行查詢時的資料庫鎖定逾時 (秒)。 使用 -1 表示永遠等候。|
|**/p:**|ExtractAllTableData=(BOOLEAN)|指出是否已解壓縮所有使用者資料表中的資料。 如為 'true'，則會解壓縮所有使用者資料表中的資料，且您不能指定解壓縮個別的使用者資料表資料。 如為 'false'，請指定一或多個要解壓縮其資料的使用者資料表。|
|**/p:**|ExtractApplicationScopedObjectsOnly=(BOOLEAN 'True')|如果為 true，則只擷取指定之來源的應用程式範圍物件。 如果為 false，則擷取指定之來源的所有物件。|
|**/p:**|ExtractReferencedServerScopedElements=(BOOLEAN 'True')|如果為 true，則擷取來源資料庫物件所參考的登入、伺服器稽核及認證物件。|
|**/p:**|ExtractUsageProperties=(BOOLEAN)|指定是否會從資料庫擷取用法屬性，例如資料表資料列計數和索引大小。|
|**/p:**|IgnoreExtendedProperties=(BOOLEAN)|指定是否應該忽略擴充屬性。|
|**/p:**|IgnorePermissions=(BOOLEAN 'True')|指定是否應該忽略權限。|
|**/p:**|IgnoreUserLoginMappings=(BOOLEAN)|指定是否忽略使用者與登入之間的關聯性。|
|**/p:**|LongRunningCommandTimeout=(INT32)| 以秒為單位指定對 SQL Server 執行查詢時的長時間執行命令逾時。 使用 0 表示永遠等候。|
|**/p:**|Storage=({File&#124;Memory} 'File')|指定支援儲存體的類型，以供結構描述模型在擷取期間使用。|
|**/p:**|TableData=(STRING)|指出要解壓縮其資料的資料表。 下列列格式指定資料表名稱，名稱部分使用或不用括弧括住：schema_name.table_identifier。 可以多次指定這個選項。|
|**/p:**| TempDirectoryForTableData=(STRING)|指定在寫入套件檔案之前，用於緩衝資料表資料的暫存目錄。|
|**/p:**|VerifyExtraction=(BOOLEAN)|指定是否應該驗證擷取的 dacpac。|

## <a name="next-steps"></a>後續步驟

- 深入了解 [sqlpackage](sqlpackage.md)