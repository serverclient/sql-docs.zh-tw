---
title: SqlPackage 匯入
description: 了解如何使用 SqlPackage.exe Import 將資料庫開發工作自動化。 檢視範例及可用的參數、屬性和 SQLCMD 變數。
ms.prod: sql
ms.prod_service: sql-tools
ms.technology: tools-other
ms.topic: conceptual
ms.assetid: 198198e2-7cf4-4a21-bda4-51b36cb4284b
author: dzsquared
ms.author: drskwier
ms.reviewer: maghan; sstein
ms.date: 12/11/2020
ms.openlocfilehash: 0e9acab737de04b002debf9d8c1b230a5cb01b14
ms.sourcegitcommit: 866554663ca3191748b6e4eb4d8d82fa58c4e426
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97577806"
---
# <a name="sqlpackage-import-parameters-and-properties"></a>SqlPackage Import 參數與屬性
SqlPackage.exe 的 Import 動作會將 BACPAC 套件 (.bacpac 檔案) 的結構描述和資料表資料，匯入至 SQL Server 或 Azure SQL Database 的新或空資料庫。 在對現有資料庫進行匯入作業時，目標資料庫不能包含任何使用者定義的結構描述物件。  

## <a name="command-line-syntax"></a>命令列語法

**SqlPackage.exe** 會使用命令列上指定的參數、屬性和 SQLCMD 變數來起始指定的動作。  
  
```bash
SqlPackage {parameters}{properties}{SQLCMD Variables}  
```

## <a name="parameters-for-the-import-action"></a>Import 動作的參數

|參數|簡短形式|值|描述|
|---|---|---|---|
|**/Action:**|**/a**|匯入|指定要執行的動作。 |
|**/AccessToken:**|**/at**|{string}| 根據要在連線到目標資料庫時使用的驗證存取權杖，來指定權杖。 |
|**/Diagnostics:**|**/d**|{True&#124;False}|指定診斷記錄是否輸出到主控台。 預設為 False。 |
|**/DiagnosticsFile:**|**/df**|{string}|指定要儲存診斷記錄的檔案。 |
|**/MaxParallelism:**|**/mp**|{int}| 指定針對資料庫執行之並行作業的平行處理原則的程度。 預設值為 8。 |
|**/Properties:**|**/p**|{PropertyName}={Value}|指定動件特定屬性的名稱/值對：{PropertyName}={Value}。 請參閱特定動作的說明，以便查看該動作的屬性名稱。 範例：sqlpackage.exe /Action:Import /?。|
|**/Quiet:**|**/q**|{True&#124;False}|指定是否隱藏詳細的意見反應。 預設為 False。|
|**/SourceFile：**|**/sf**|{string}|指定要當作動作來源使用的來源檔案。 如果使用了此參數，其他來源參數都應該無效。 |
|**/TargetConnectionString:**|**/tcs**|{string}|指定目標資料庫的有效 SQL Server/Azure 連接字串。 如果指定此參數，就應該以獨佔方式將它用於所有其他目標參數。 |
|**/TargetDatabaseName:**|**/tdn**|{string}|指定 sqlpackage.exe 動作目標之資料庫名稱的覆寫。 |
|**/TargetEncryptConnection:**|**/tec**|{True&#124;False}|指定 SQL 加密是否應該用於目標資料庫連線。 |
|**/TargetPassword:**|**/tp**|{string}|若為 SQL Server 驗證案例，則定義要用來存取目標資料庫的密碼。 |
|**/TargetServerName：**|**/tsn**|{string}|定義裝載目標資料庫的伺服器名稱。 |
|**/TargetTimeout:**|**/tt**|{int}|指定建立目標資料庫連線的逾時 (以秒為單位)。 針對 Azure AD，此值建議大於或等於 30 秒。|
|**/TargetTrustServerCertificate:**|**/ttsc**|{True&#124;False}|指定是否要使用 TLS 來加密目標資料庫連線並略過驗證信任的憑證鏈結。 |
|**/TargetUser:**|**/tu**|{string}|若為 SQL Server 驗證案例，則定義要用以存取目標資料庫的 SQL Server 使用者。 |
|**/TenantId:**|**/tid**|{string}|表示 Azure AD 租用戶識別碼或網域名稱。 支援來賓或匯入的 Azure AD 使用者以及 outlook.com、hotmail.com 或 live.com 等 Microsoft 帳戶時，這是必要選項。 假設已驗證使用者是此 AD 的原生使用者，則若省略此參數，即會使用 Azure AD 的預設租用戶識別碼。 不過，在此情況下，不支援在此 Azure AD 中所裝載任何來賓或匯入的使用者及/或 Microsoft 帳戶，且作業會失敗。 <br/> 如需 Active Directory 通用驗證的詳細資訊，請參閱 [SQL Database 和 Azure Synapse Analytics 的通用驗證 (MFA 的 SSMS 支援)](/azure/sql-database/sql-database-ssms-mfa-authentication)。|
|**/UniversalAuthentication:**|**/ua**|{True&#124;False}|指定是否應該使用通用驗證。 設定為 True 時，即會啟用支援 MFA 的互動式驗證通訊協定。 此選項也可以用於不使用 MFA 的 Azure AD 驗證，使用需要使用者輸入其使用者名稱和密碼或整合式驗證 (Windows 認證) 的互動式通訊協定。 當 /UniversalAuthentication 設定為 True 時，SourceConnectionString (/scs) 中不能指定任何 Azure AD 驗證。 當 /UniversalAuthentication 設定為 False 時， SourceConnectionString (/scs) 中必須指定 Azure AD 驗證。 <br/> 如需 Active Directory 通用驗證的詳細資訊，請參閱 [SQL Database 和 Azure Synapse Analytics 的通用驗證 (MFA 的 SSMS 支援)](/azure/sql-database/sql-database-ssms-mfa-authentication)。|

## <a name="properties-specific-to-the-import-action"></a>Import 動作的特定屬性

|屬性|值|描述|
|---|---|---|
|**/p:**|CommandTimeout=(INT32 '60')|以秒為單位指定對 SQL Server 執行查詢時的命令逾時。|
|**/p:**|DatabaseEdition=({Basic&#124;Standard&#124;Premium&#124;DataWarehouse&#124;GeneralPurpose&#124;BusinessCritical&#124;Hyperscale&#124;Default} 'Default')|定義 Azure SQL Database 的版本。|
|**/p:**|DatabaseLockTimeout=(INT32 '60')| 指定對 SQLServer 執行查詢時的資料庫鎖定逾時 (秒)。 使用 -1 表示永遠等候。|
|**/p:**|DatabaseMaximumSize=(INT32)|定義 Azure SQL Database 的大小上限 (以 GB 表示)。|
|**/p:**|DatabaseServiceObjective=(STRING)|定義 Azure SQL Database 的效能等級，例如 "P0" 或 "S1"。|
|**/p:**|ImportContributorArguments=(STRING)|指定部署參與者的部署參與者引數。 這應該是以分號區隔的值清單。|
|**/p:**|ImportContributors=(STRING)|指定部署參與者，這應該在匯入 bacpac 時執行。 這應該是以分號區隔的完整組建參與者名稱或識別碼清單。|
|**/p:**|ImportContributorPaths=(STRING)|指定載入其他部署參與者的路徑。 這應該是以分號區隔的值清單。 |
|**/p:**|LongRunningCommandTimeout=(INT32)| 以秒為單位指定對 SQL Server 執行查詢時的長時間執行命令逾時。 使用 0 表示永遠等候。|
|**/p:**|Storage=({File&#124;Memory})|指定在建置資料庫模型時，如何儲存項目。 基於效能考量，預設值為 InMemory。 若為大型資料庫，則需要檔案型儲存體。|
  

## <a name="next-steps"></a>後續步驟

- 深入了解 [sqlpackage](sqlpackage.md)