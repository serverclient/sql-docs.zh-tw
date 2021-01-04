---
title: SqlPackage 匯出
description: 了解如何使用 SqlPackage.exe Export 將資料庫開發工作自動化。 檢視範例及可用的參數、屬性和 SQLCMD 變數。
ms.prod: sql
ms.prod_service: sql-tools
ms.technology: tools-other
ms.topic: conceptual
ms.assetid: 198198e2-7cf4-4a21-bda4-51b36cb4284b
author: dzsquared
ms.author: drskwier
ms.reviewer: maghan; sstein
ms.date: 12/11/2020
ms.openlocfilehash: c0c3b1462fe165678e3826585f5ce82d5945de56
ms.sourcegitcommit: 866554663ca3191748b6e4eb4d8d82fa58c4e426
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97577808"
---
# <a name="sqlpackage-export-parameters-and-properties"></a>SqlPackage Export 參數與屬性
SqlPackage.exe 的 Export 動作會將即時資料庫從 SQL Server 或 Azure SQL 匯出至 BACPAC 套件 (.bacpac 檔案)。 依預設，所有資料表的資料將包括在 .bacpac 檔案中。 您可以選擇性地只指定要匯出資料的資料表子集。 驗證 Export 動作，可確保 Azure SQL Database 相容於完整目標資料庫，即使已指定資料表子集進行匯出也一樣。 

## <a name="command-line-syntax"></a>命令列語法

**SqlPackage.exe** 會使用命令列上指定的參數、屬性和 SQLCMD 變數來起始指定的動作。  
  
```bash
SqlPackage {parameters}{properties}{SQLCMD Variables}  
```

## <a name="parameters-for-the-export-action"></a>Export 動作的參數

|參數|簡短形式|值|描述|
|---|---|---|---|
|**/Action:**|**/a**|匯出|指定要執行的動作。 |
|**/AccessToken:**|**/at**|{string}| 根據要在連線到目標資料庫時使用的驗證存取權杖，來指定權杖。 |
|**/Diagnostics:**|**/d**|{True&#124;False}|指定診斷記錄是否輸出到主控台。 預設為 False。 |
|**/DiagnosticsFile:**|**/df**|{string}|指定要儲存診斷記錄的檔案。 |
|**/MaxParallelism:**|**/mp**|{int}| 指定針對資料庫執行之並行作業的平行處理原則的程度。 預設值為 8。 |
|**/OverwriteFiles:**|**/of**|{True&#124;False}|指定 sqlpackage.exe 是否應該覆寫現有的檔案。 指定 false 會導致 sqlpackage.exe 在遇到現有的檔案時中止動作。 預設值是 True。 |
|**/Properties:**|**/p**|{PropertyName}={Value}|指定動件特定屬性的名稱/值對：{PropertyName}={Value}。 請參閱特定動作的說明，以便查看該動作的屬性名稱。 範例：sqlpackage.exe /Action:Export /?。|
|**/Quiet:**|**/q**|{True&#124;False}|指定是否隱藏詳細的意見反應。 預設為 False。|
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

## <a name="properties-specific-to-the-export-action"></a>Export 動作的特定屬性

|屬性|值|描述|
|---|---|---|
|**/p:**|CommandTimeout=(INT32 '60')|以秒為單位指定對 SQL Server 執行查詢時的命令逾時。|
|**/p:**|DatabaseLockTimeout=(INT32 '60')| 指定對 SQLServer 執行查詢時的資料庫鎖定逾時 (秒)。 使用 -1 表示永遠等候。|
|**/p:**|LongRunningCommandTimeout=(INT32)| 以秒為單位指定對 SQL Server 執行查詢時的長時間執行命令逾時。 使用 0 表示永遠等候。|
|**/p:**|Storage=({File&#124;Memory} 'File')|指定支援儲存體的類型，以供結構描述模型在擷取期間使用。|
|**/p:**|TableData=(STRING)|指出要解壓縮其資料的資料表。 下列列格式指定資料表名稱，名稱部分使用或不用括弧括住：schema_name.table_identifier。 可以多次指定這個選項。|
|**/p:**|TempDirectoryForTableData=(STRING)|指定在寫入套件檔案之前，用於緩衝資料表資料的暫存目錄。|
|**/p:**|TargetEngineVersion=({Default&#124;Latest&#124;V11&#124;V12} 'Latest')|指定預期的目標引擎版本。 這影響是否在產生的 bacpac 中允許具有 V12 功能的 Azure SQL Database 伺服器支援物件，例如經記憶體最佳化的資料表。|
|**/p:**|VerifyFullTextDocumentTypesSupported=(BOOLEAN)|指定是否要驗證 Microsoft Azure SQL Database v12 所支援的全文檢索文件類型。|

## <a name="next-steps"></a>後續步驟

- 深入了解 [sqlpackage](sqlpackage.md)