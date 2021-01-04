---
title: SqlPackage 部署報表和漂移報表
description: 了解如何使用 SqlPackage.exe 部署報表和漂移報表將資料庫開發工作自動化。 檢視範例及可用的參數、屬性和 SQLCMD 變數。
ms.prod: sql
ms.prod_service: sql-tools
ms.technology: tools-other
ms.topic: conceptual
ms.assetid: 198198e2-7cf4-4a21-bda4-51b36cb4284b
author: dzsquared
ms.author: drskwier
ms.reviewer: maghan; sstein
ms.date: 12/11/2020
ms.openlocfilehash: 07a393bb785aafda352aff28f920cb4316a47191
ms.sourcegitcommit: 866554663ca3191748b6e4eb4d8d82fa58c4e426
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97577815"
---
# <a name="sqlpackage-deploy-report-and-drift-report"></a>SqlPackage 部署報表和漂移報表
SqlPackage.exe 的 [DeployReport](#deployreport-action-parameters) 報表動作會建立發佈動作所做變更的 XML 報表。
SqlPackage.exe 的 [DriftReport](#driftreport-action-parameters) 報表動作會建立自從上次註冊以來對註冊資料庫所做變更的 XML 報表。  

## <a name="command-line-syntax"></a>命令列語法

**SqlPackage.exe** 會使用命令列上指定的參數、屬性和 SQLCMD 變數來起始指定的動作。  
  
```bash
SqlPackage {parameters}{properties}{SQLCMD Variables}  
```

## <a name="deployreport-action-parameters"></a>DeployReport 動作參數

|參數|簡短形式|值|描述|
|---|---|---|---|
|**/Action:**|**/a**|DeployReport|指定要執行的動作。 |
|**/AccessToken:**|**/at**|{string}| 根據要在連線到目標資料庫時使用的驗證存取權杖，來指定權杖。 |
|**/Diagnostics:**|**/d**|{True&#124;False}|指定診斷記錄是否輸出到主控台。 預設為 False。 |
|**/DiagnosticsFile:**|**/df**|{string}|指定要儲存診斷記錄的檔案。 |
|**/MaxParallelism:**|**/mp**|{int}| 指定針對資料庫執行之並行作業的平行處理原則的程度。 預設值為 8。 |
|**/OutputPath:**|**/op**|{string}|指定輸出檔案產生位置的檔案路徑。 |
|**/OverwriteFiles:**|**/of**|{True&#124;False}|指定 sqlpackage.exe 是否應該覆寫現有的檔案。 指定 false 會導致 sqlpackage.exe 在遇到現有的檔案時中止動作。 預設值是 True。 |
|**/Profile:**|**/pr**|{string}|指定 DAC 發行設定檔的檔案路徑。 設定檔會定義產生輸出時要使用之屬性及變數的集合。 |
|**/Properties:**|**/p**|{PropertyName}={Value}|指定動件特定屬性的名稱/值對：{PropertyName}={Value}。 請參閱特定動作的說明，以便查看該動作的屬性名稱。 範例：sqlpackage.exe /Action:DeployReport /?。 |
|**/Quiet:**|**/q**|{True&#124;False}|指定是否隱藏詳細的意見反應。 預設為 False。 |
|**/SourceConnectionString:**|**/scs**|{string}|指定來源資料庫的有效 SQL Server/Azure 連接字串。 如果指定了此參數，就應該以獨佔方式將其用於所有其他來源參數。 |
|**/SourceDatabaseName:**|**/sdn**|{string}|定義來源資料庫的名稱。 |
|**/SourceEncryptConnection:**|**/sec**|{True&#124;False}|指定 SQL 加密是否應該用於來源資料庫連接。 |
|**/SourceFile：**|**/sf**|{string}|指定要當作動作來源使用的來源檔案，而非資料庫。 如果使用了此參數，其他來源參數都應該無效。 |
|**/SourcePassword:**|**/sp**|{string}|若為 SQL Server 驗證案例，則定義要用來存取來源資料庫的密碼。 |
|**/SourceServerName：**|**/ssn**|{string}|定義裝載來源資料庫的伺服器名稱。 |
|**/SourceTimeout:**|**/st**|{int}|指定建立來源資料庫連線的逾時 (以秒為單位)。 |
|**/SourceTrustServerCertificate:**|**/stsc**|{True&#124;False}|指定是否要使用 TLS 來加密來源資料庫連線並且略過驗證信任的憑證鏈結。 |
|**/SourceUser:**|**/su**|{string}|若為 SQL Server 驗證案例，則定義要用來存取來源資料庫的 SQL Server 使用者。 |
|**/TargetConnectionString:**|**/tcs**|{string}|指定目標資料庫的有效 SQL Server/Azure 連接字串。 如果指定此參數，就應該以獨佔方式將它用於所有其他目標參數。 |
|**/TargetDatabaseName:**|**/tdn**|{string}|指定 sqlpackage.exe 動作目標之資料庫名稱的覆寫。 |
|**/TargetEncryptConnection:**|**/tec**|{True&#124;False}|指定 SQL 加密是否應該用於目標資料庫連線。 |
|**/TargetFile:**|**/tf**|{string}|指定作為動作目標使用的目標檔案 (即 .dacpac 檔案)，而非資料庫。 如果使用此參數，其他目標參數都應該無效。 這個參數對僅支援資料庫目標的動作而言應無效。|
|**/TargetPassword:**|**/tp**|{string}|若為 SQL Server 驗證案例，則定義要用來存取目標資料庫的密碼。 |
|**/TargetServerName：**|**/tsn**|{string}|定義裝載目標資料庫的伺服器名稱。 |
|**/TargetTimeout:**|**/tt**|{int}|指定建立目標資料庫連線的逾時 (以秒為單位)。 針對 Azure AD，此值建議大於或等於 30 秒。|
|**/TargetTrustServerCertificate:**|**/ttsc**|{True&#124;False}|指定是否要使用 TLS 來加密目標資料庫連線並略過驗證信任的憑證鏈結。 |
|**/TargetUser:**|**/tu**|{string}|若為 SQL Server 驗證案例，則定義要用以存取目標資料庫的 SQL Server 使用者。 |
|**/TenantId:**|**/tid**|{string}|表示 Azure AD 租用戶識別碼或網域名稱。 支援來賓或匯入的 Azure AD 使用者以及 outlook.com、hotmail.com 或 live.com 等 Microsoft 帳戶時，這是必要選項。 假設已驗證使用者是此 AD 的原生使用者，則若省略此參數，即會使用 Azure AD 的預設租用戶識別碼。 不過，在此情況下，不支援在此 Azure AD 中所裝載任何來賓或匯入的使用者及/或 Microsoft 帳戶，且作業會失敗。 <br/> 如需 Active Directory 通用驗證的詳細資訊，請參閱 [SQL Database 和 Azure Synapse Analytics 的通用驗證 (MFA 的 SSMS 支援)](/azure/sql-database/sql-database-ssms-mfa-authentication)。|
|**/UniversalAuthentication:**|**/ua**|{True&#124;False}|指定是否應該使用通用驗證。 設定為 True 時，即會啟用支援 MFA 的互動式驗證通訊協定。 此選項也可以用於不使用 MFA 的 Azure AD 驗證，使用需要使用者輸入其使用者名稱和密碼或整合式驗證 (Windows 認證) 的互動式通訊協定。 當 /UniversalAuthentication 設定為 True 時，SourceConnectionString (/scs) 中不能指定任何 Azure AD 驗證。 當 /UniversalAuthentication 設定為 False 時， SourceConnectionString (/scs) 中必須指定 Azure AD 驗證。 <br/> 如需 Active Directory 通用驗證的詳細資訊，請參閱 [SQL Database 和 Azure Synapse Analytics 的通用驗證 (MFA 的 SSMS 支援)](/azure/sql-database/sql-database-ssms-mfa-authentication)。|
|**/Variables:**|**/v**|{PropertyName}={Value}|指定動件特定變數的名稱/值對：{VariableName}={Value}。 DACPAC 檔案包含有效 SQLCMD 變數的清單。 如果未針對每一個變數提供值，則會產生錯誤。 |

## <a name="deployreport-action-properties"></a>DeployReport 動作屬性

|屬性|值|描述|
|---|---|---|
|**/p:**|AdditionalDeploymentContributorArguments=(STRING)|為部署參與者指定其他部署參與者引數。 這應該是以分號區隔的值清單。|
|**/p:**|AdditionalDeploymentContributors=(STRING)|指定部署 dacpac 時應該執行的其他部署參與者。 這應該是以分號區隔的完整組建參與者名稱或識別碼清單。|
|**/p:**|AdditionalDeploymentContributorPaths=(STRING)| 指定載入其他部署參與者的路徑。 這應該是以分號區隔的值清單。 | 
|**/p:**|AllowDropBlocking Assemblies=(BOOLEAN)|這個屬性是供 SqlClr 部署用來使任何封鎖的組件會在部署計畫中卸除。 根據預設，如果必須卸除任何參考組件，則該封鎖/參考組件會封鎖組件更新。|
|**/p:**|AllowIncompatiblePlatform=(BOOLEAN)|指定儘管 SQL Server 平台不相容，是否仍要嘗試動作。|
|**/p:**|AllowUnsafeRowLevelSecurityDataMovement=(BOOLEAN)|若此屬性設定為 true，請勿封鎖具有資料列層級安全性之資料表的資料動作。 預設值為 false。|
|**/p:**|BackupDatabaseBeforeChanges=(BOOLEAN)|在部署任何變更之前，先備份資料庫。|
|**/p:**|BlockOnPossibleDataLoss=(BOOLEAN 'True')|指定如果發行作業可能導致資料遺失，就應該終止發行事件。|
|**/p:**|BlockWhenDriftDetected=(BOOLEAN 'True')|指定是否封鎖更新結構描述不再符合註冊或已取消註冊的資料庫。 |
|**/p:**|CommandTimeout=(INT32 '60')|以秒為單位指定對 SQL Server 執行查詢時的命令逾時。 |
|**/p:**|CommentOutSetVarDeclarations=(BOOLEAN)|指定在產生的發行指令碼中是否應該將 SETVAR 變數的宣告標記為註解。 如果您計畫在使用 SQLCMD.EXE 等工具進行發行時在命令列指定值，就可以選擇這種作法。 |
|**/p:**|CompareUsingTargetCollation=(BOOLEAN)|這個設定表示資料庫的定序於部署期間的處理方式。如果目標資料庫的定序不符合來源所指定的定序，預設會更新目標資料庫的定序。 當設定這個選項時，則應該使用目標資料庫 (或伺服器) 的定序。 |
|**/p:**|CreateNewDatabase=(BOOLEAN)|指定當您發行至資料庫時，應該更新目標資料庫或應該卸除並重新建立目標資料庫。 |
|**/p:**|DatabaseEdition=({Basic&#124;Standard&#124;Premium&#124;DataWarehouse&#124;GeneralPurpose&#124;BusinessCritical&#124;Hyperscale&#124;Default} 'Default')|定義 Azure SQL Database 的版本。|
|**/p:**|DatabaseLockTimeout=(INT32 '60')| 指定對 SQLServer 執行查詢時的資料庫鎖定逾時 (秒)。 使用 -1 表示永遠等候。|
|**/p:**|DatabaseMaximumSize=(INT32)|定義 Azure SQL Database 的大小上限 (以 GB 表示)。|
|**/p:**|DatabaseServiceObjective=(STRING)|定義 Azure SQL Database 的效能等級，例如 "P0" 或 "S1"。 |
|**/p:**|DeployDatabaseInSingleUserMode=(BOOLEAN)|若為 true，則資料庫會在設定為單一使用者模式後部署。 |
|**/p:**|DisableAndReenableDdlTriggers=(BOOLEAN 'True')| 指定是否在發行程序開始時停用資料定義語言 (DDL) 觸發程序，並在發行動作結束時重新啟用。|
|**/p:**|DoNotAlterChangeDataCaptureObjects=(BOOLEAN 'True')|如果為 true，則不會改變異動資料擷取物件。|
|**/p:**|DoNotAlterReplicatedObjects=(BOOLEAN 'True')|指定驗證期間是否識別有複寫的物件。|
|**/p:**|DoNotDropObjectType=(STRING)|當 DropObjectsNotInSource 為 true 時不應捨棄的物件類型。 有效的物件類型名稱為 Aggregates、ApplicationRoles、Assemblies、AsymmetricKeys、BrokerPriorities、Certificates、ColumnEncryptionKeys、ColumnMasterKeys、Contracts、DatabaseRoles、DatabaseTriggers、Defaults、ExtendedProperties、ExternalDataSources、ExternalFileFormats、ExternalTables、Filegroups、FileTables、FullTextCatalogs、FullTextStoplists、MessageTypes、PartitionFunctions、PartitionSchemes、Permissions、Queues、RemoteServiceBindings、RoleMembership、Rules、ScalarValuedFunctions、SearchPropertyLists、SecurityPolicies、Sequences、Services、Signatures、StoredProcedures、SymmetricKeys、Synonyms、Tables、TableValuedFunctions、UserDefinedDataTypes、UserDefinedTableTypes、ClrUserDefinedTypes、Users、Views、XmlSchemaCollections、Audits、Credentials、CryptographicProviders、DatabaseAuditSpecifications、DatabaseScopedCredentials、Endpoints、ErrorMessages、EventNotifications、EventSessions、LinkedServerLogins、LinkedServers、Logins、Routes、ServerAuditSpecifications、ServerRoleMembership、ServerRoles、ServerTriggers。 |
|**/p:**|DoNotDropObjectTypes=(STRING)|當 DropObjectsNotInSource 為 true 時不應捨棄的分號分隔物件類型清單。 有效的物件類型名稱為 Aggregates、ApplicationRoles、Assemblies、AsymmetricKeys、BrokerPriorities、Certificates、ColumnEncryptionKeys、ColumnMasterKeys、Contracts、DatabaseRoles、DatabaseTriggers、Defaults、ExtendedProperties、ExternalDataSources、ExternalFileFormats、ExternalTables、Filegroups、FileTables、FullTextCatalogs、FullTextStoplists、MessageTypes、PartitionFunctions、PartitionSchemes、Permissions、Queues、RemoteServiceBindings、RoleMembership、Rules、ScalarValuedFunctions、SearchPropertyLists、SecurityPolicies、Sequences、Services、Signatures、StoredProcedures、SymmetricKeys、Synonyms、Tables、TableValuedFunctions、UserDefinedDataTypes、UserDefinedTableTypes、ClrUserDefinedTypes、Users、Views、XmlSchemaCollections、Audits、Credentials、CryptographicProviders、DatabaseAuditSpecifications、DatabaseScopedCredentials、Endpoints、ErrorMessages、EventNotifications、EventSessions、LinkedServerLogins、LinkedServers、Logins、Routes、ServerAuditSpecifications、ServerRoleMembership、ServerRoles、ServerTriggers。|
|**/p:**|DropConstraintsNotInSource=(BOOLEAN 'True')|指定當您發行至資料庫時，是否要從目標資料庫中卸除不存在資料庫快照集 (.dacpac) 檔案中的條件約束。|
|**/p:**|DropDmlTriggersNotInSource=(BOOLEAN 'True')|指定當您發行至資料庫時，是否要從目標資料庫中卸除不存在資料庫快照集 (.dacpac) 檔案中的 DML 觸發程序。|
|**/p:**|DropExtendedPropertiesNotInSource=(BOOLEAN 'True')|指定當您發行至資料庫時，是否要從目標資料庫中卸除不存在資料庫快照集 (.dacpac) 檔案中的擴充屬性。|
|**/p:**|DropIndexesNotInSource=(BOOLEAN 'True')|指定當您發行至資料庫時，是否要從目標資料庫中卸除不存在資料庫快照集 (.dacpac) 檔案中的索引。|
|**/p:**|DropObjectsNotInSource=(BOOLEAN)|指定當您發行至資料庫時，是否要從目標資料庫中卸除不存在資料庫快照集 (.dacpac) 檔案中的物件。 這個值的優先順序高於 DropExtendedProperties。|
|**/p:**|DropPermissionsNotInSource=(BOOLEAN)|指定當您發行更新至資料庫時，是否要從目標資料庫中卸除不存在資料庫快照集 (.dacpac) 檔案中的權限。|
|**/p:**|DropRoleMembersNotInSource=(BOOLEAN)|指定當您發行更新至資料庫時，是否要從目標資料庫中卸除資料庫快照集 (.dacpac) 檔案中沒有定義的角色成員。|
|**/p:**|DropStatisticsNotInSource=(BOOLEAN 'True')|指定當您發行至資料庫時，是否要從目標資料庫卸除不存在於資料庫快照集 (.dacpac) 檔案中的統計資料。|
|**/p:**|ExcludeObjectType=(STRING)|部署期間應該忽略的物件類型。 有效的物件類型名稱為 Aggregates、ApplicationRoles、Assemblies、AsymmetricKeys、BrokerPriorities、Certificates、ColumnEncryptionKeys、ColumnMasterKeys、Contracts、DatabaseRoles、DatabaseTriggers、Defaults、ExtendedProperties、ExternalDataSources、ExternalFileFormats、ExternalTables、Filegroups、FileTables、FullTextCatalogs、FullTextStoplists、MessageTypes、PartitionFunctions、PartitionSchemes、Permissions、Queues、RemoteServiceBindings、RoleMembership、Rules、ScalarValuedFunctions、SearchPropertyLists、SecurityPolicies、Sequences、Services、Signatures、StoredProcedures、SymmetricKeys、Synonyms、Tables、TableValuedFunctions、UserDefinedDataTypes、UserDefinedTableTypes、ClrUserDefinedTypes、Users、Views、XmlSchemaCollections、Audits、Credentials、CryptographicProviders、DatabaseAuditSpecifications、DatabaseScopedCredentials、Endpoints、ErrorMessages、EventNotifications、EventSessions、LinkedServerLogins、LinkedServers、Logins、Routes、ServerAuditSpecifications、ServerRoleMembership、ServerRoles、ServerTriggers。|
|**/p:**|ExcludeObjectTypes=(STRING)|以分號區隔的物件類型清單，列出部署期間應該忽略的物件類型。 有效的物件類型名稱為 Aggregates、ApplicationRoles、Assemblies、AsymmetricKeys、BrokerPriorities、Certificates、ColumnEncryptionKeys、ColumnMasterKeys、Contracts、DatabaseRoles、DatabaseTriggers、Defaults、ExtendedProperties、ExternalDataSources、ExternalFileFormats、ExternalTables、Filegroups、FileTables、FullTextCatalogs、FullTextStoplists、MessageTypes、PartitionFunctions、PartitionSchemes、Permissions、Queues、RemoteServiceBindings、RoleMembership、Rules、ScalarValuedFunctions、SearchPropertyLists、SecurityPolicies、Sequences、Services、Signatures、StoredProcedures、SymmetricKeys、Synonyms、Tables、TableValuedFunctions、UserDefinedDataTypes、UserDefinedTableTypes、ClrUserDefinedTypes、Users、Views、XmlSchemaCollections、Audits、Credentials、CryptographicProviders、DatabaseAuditSpecifications、DatabaseScopedCredentials、Endpoints、ErrorMessages、EventNotifications、EventSessions、LinkedServerLogins、LinkedServers、Logins、Routes、ServerAuditSpecifications、ServerRoleMembership、ServerRoles、ServerTriggers。|
|**/p:**|GenerateSmartDefaults=(BOOLEAN)|在更新含有資料且資料行不允許 null 值的資料表時，自動提供預設值。|
|**/p:**|IgnoreAnsiNulls=(BOOLEAN 'True')|指定當您發行至資料庫時，應該忽略或更新 ANSI NULLS 設定的差異。|
|**/p:**|IgnoreAuthorizer=(BOOLEAN)|指定當您發行至資料庫時，應該忽略或更新授權者的差異。|
|**/p:**|IgnoreColumnCollation=(BOOLEAN)|指定當您發行至資料庫時，應該忽略或更新資料行定序的差異。|
|**/p:**|IgnoreColumnOrder=(BOOLEAN)|指定當您發行至資料庫時，是否應略過或更新資料表資料行順序的差異。|
|**/p:**|IgnoreComments=(BOOLEAN)|指定當您發行至資料庫時，應該忽略或更新註解的差異。|
|**/p:**|IgnoreCryptographicProviderFilePath=(BOOLEAN 'True')|指定當您發行至資料庫時，應該忽略或更新密碼編譯提供者之檔案路徑的差異。|
|**/p:**|IgnoreDdlTriggerOrder=(BOOLEAN)|指定當您發行至資料庫或伺服器時，應該忽略或更新資料定義語言 (Data Definition Language) (DDL) 觸發程序的順序差異。|
|**/p:**|IgnoreDdlTriggerState=(BOOLEAN)|指定當您發行至資料庫時，應該忽略或更新資料定義語言 (Data Definition Language) (DDL) 觸發程序之啟用或停用狀態的差異。|
|**/p:**|IgnoreDefaultSchema=(BOOLEAN)|指定當您發行至資料庫時，應該忽略或更新預設結構描述的差異。 |
|**/p:**|IgnoreDmlTriggerOrder=(BOOLEAN)|指定當您發行至資料庫時，應該忽略或更新資料操作語言 (DML) 觸發程序的順序差異。| 
|**/p:**|IgnoreDmlTriggerState=(BOOLEAN)|指定當您發行至資料庫時，應該忽略或更新 DML 觸發程序之啟用或停用狀態的差異。 |
|**/p:**|IgnoreExtendedProperties=(BOOLEAN)|指定當您發行至資料庫時，應該忽略或更新擴充屬性的差異。 |
|**/p:**|IgnoreFileAndLogFilePath=(BOOLEAN 'True')|指定當您發行至資料庫時，應該忽略或更新檔案和記錄檔之路徑的差異。 |
|**/p:**|IgnoreFilegroupPlacement=(BOOLEAN 'True')|指定當您發行至資料庫時，應該忽略或更新 FILEGROUP 中物件位置的差異。| 
|**/p:**|IgnoreFileSize=(BOOLEAN 'True')|指定當您發行至資料庫時，應該忽略檔案大小的差異或應該發出警告。 |
|**/p:**|IgnoreFillFactor=(BOOLEAN 'True')|指定當您發行至資料庫時，應該忽略索引儲存體之填滿因數的差異或應該發出警告|
|**/p:**|IgnoreFullTextCatalogFilePath=(BOOLEAN 'True')|指定當您發行至資料庫時，應該忽略全文檢索目錄之檔案路徑的差異或應該發出警告。| 
|**/p:**|IgnoreIdentitySeed=(BOOLEAN)|指定當您發行更新至資料庫時，應該忽略或更新識別欄位之種子的差異。 |
|**/p:**|IgnoreIncrement=(BOOLEAN)|指定當您發行至資料庫時，應該忽略或更新識別欄位之增量的差異。 |
|**/p:**|IgnoreIndexOptions=(BOOLEAN)|指定當您發行至資料庫時，應該忽略或更新索引選項的差異。 |
|**/p:**|IgnoreIndexPadding=(BOOLEAN 'True')|指定當您發行至資料庫時，應該忽略或更新索引填補的差異。 |
|**/p:**|IgnoreKeywordCasing=(BOOLEAN 'True')|指定當您發行至資料庫時，應該忽略或更新關鍵字之大小寫的差異。 |
|**/p:**|IgnoreLockHintsOnIndexes=(BOOLEAN)|指定當您發行至資料庫時，應該忽略或更新索引之鎖定提示的差異。 |
|**/p:**|IgnoreLoginSids=(BOOLEAN 'True')| 指定當您發行至資料庫時，應該忽略或更新安全性識別碼 (SID) 的差異。| 
|**/p:**|IgnoreNotForReplication=(BOOLEAN)|指定當您發行至資料庫時，應該忽略或更新不可複寫設定。 |
|**/p:**|IgnoreObjectPlacementOnPartitionScheme=(BOOLEAN 'True')|指定當您發行至資料庫時，應該忽略或更新物件在資料分割配置上的位置。|
 |**/p:**|IgnorePartitionSchemes=(BOOLEAN)|指定當您發行至資料庫時，應該忽略或更新資料分割配置和函式的差異。|
|**/p:**|IgnorePermissions=(BOOLEAN)|指定當您發行至資料庫時，應該忽略或更新權限的差異。 |
|**/p:**|IgnoreQuotedIdentifiers=(BOOLEAN 'True')|指定當您發行至資料庫時，應該忽略或更新引號識別項設定的差異。 |
|**/p:**|IgnoreRoleMembership=(BOOLEAN)|指定當您發行至資料庫時，應該忽略或更新登入之角色成員資格的差異。 |
|**/p:**|IgnoreRouteLifetime=(BOOLEAN 'True')|指定當您發行至資料庫時，應該忽略或更新 SQL Server 將路由保留在路由表中之時間長短的差異|
|**/p:**|IgnoreSemicolonBetweenStatements=(BOOLEAN 'True')|指定當您發行至資料庫時，應該忽略或更新 T-SQL 陳述式間之分號的差異。| 
|**/p:**|IgnoreTableOptions=(BOOLEAN)|指定當您發行至資料庫時，將忽略或更新資料表選項的差異。| 
|**/p:**|IgnoreTablePartitionOptions=(BOOLEAN)|指定當您發佈至資料庫時，要忽略還是更新資料表分割區選項的差異。  此選項只適用於 Azure Synapse Analytics 資料倉儲資料庫。|
|**/p:**|IgnoreUserSettingsObjects=(BOOLEAN)|指定當您發行至資料庫時，將忽略或更新使用者設定物件的差異。|
|**/p:**|IgnoreWhitespace=(BOOLEAN 'True')|指定當您發行至資料庫時，將忽略或更新空白字元的差異。 |
|**/p:**|IgnoreWithNocheckOnCheckConstraints=(BOOLEAN)|指定當您發行至資料庫時，將忽略或更新檢查條件約束之 WITH NOCHECK 子句值的差異。| 
|**/p:**|IgnoreWithNocheckOnForeignKeys=(BOOLEAN)|指定當您發行至資料庫時，將忽略或更新外部索引鍵之 WITH NOCHECK 子句值的差異。| 
|**/p:**|IncludeCompositeObjects=(BOOLEAN)|將所有複合項目包含在單一發行作業中。|
|**/p:**|IncludeTransactionalScripts=(BOOLEAN)|指定當您發行至資料庫時，是否應該盡可能使用交易陳述式。|
|**/p:**|LongRunningCommandTimeout=(INT32)| 以秒為單位指定對 SQL Server 執行查詢時的長時間執行命令逾時。 使用 0 表示永遠等候。|
|**/p:**|NoAlterStatementsToChangeClrTypes=(BOOLEAN)|指定發行在發現差異時一定要卸除並重新建立組件，而不是發出 ALTER ASSEMBLY 陳述式。 |
|**/p:**|PopulateFilesOnFileGroups=(BOOLEAN 'True')|指定在目標資料庫中建立新 FileGroup 時，是否一併建立新檔案。 |
|**/p:**|RegisterDataTierApplication=(BOOLEAN)|指定結構描述是否向資料庫伺服器註冊。 
|**/p:**|RunDeploymentPlanExecutors=(BOOLEAN)|指定執行其他作業時，是否應該執行 DeploymentPlanExecutor 參與者。|
|**/p:**|ScriptDatabaseCollation=(BOOLEAN)|指定當您發行至資料庫時，應該忽略或更新資料庫定序的差異。 |
|**/p:**|ScriptDatabaseCompatibility=(BOOLEAN)|指定當您發行至資料庫時，應該忽略或更新資料庫相容性的差異。 |
|**/p:**|ScriptDatabaseOptions=(BOOLEAN 'True')|指定是否應該在發行動作中設定或更新目標資料庫屬性。 |
|**/p:**|ScriptDeployStateChecks=(BOOLEAN)|指定是否在發行指令碼中產生陳述式，來驗證資料庫名稱和伺服器名稱是否符合資料庫專案中指定的名稱。|
|**/p:**|ScriptFileSize=(BOOLEAN)|控制將檔案加入至檔案群組時是否指定大小。 |
|**/p:**|ScriptNewConstraintValidation=(BOOLEAN 'True')|在發佈結束時，所有的條件約束會當作一個集合進行驗證，避免在發佈中途因檢查或外部索引鍵條件約束造成資料錯誤。 如果設為 False，則會發行您的條件約束，但不檢查對應的資料。|
|**/p:**|ScriptRefreshModule=(BOOLEAN 'True')|在發行指令碼的結尾包含重新整理陳述式。|
|**/p:**|Storage=({File&#124;Memory})|指定在建置資料庫模型時，如何儲存項目。 基於效能考量，預設值為 InMemory。 若為大型資料庫，則需要檔案型儲存體。|
|**/p:**|TreatVerificationErrorsAsWarnings=(BOOLEAN)|指定在發佈驗證期間發生的錯誤是否應視為警告。 系統會先針對產生的部署計畫執行檢查，再針對您的目標資料庫執行計畫。 計畫驗證會偵測出遺漏僅限於目標的物件 (如索引) 這類必須卸除後才能進行變更的問題。 驗證也會偵測因為參考複合專案而存在相依性 (如資料表或檢視)，但卻不存在於目標資料庫中的情況。 您可以選擇執行此動作，以取得所有問題的完整清單，而不是在第一個錯誤就停止 Publish 動作。 |
|**/p:**|UnmodifiableObjectWarnings=(BOOLEAN 'True')|指定在無法修改的物件中發現差異時 (例如檔案的檔案大小或檔案路徑不同) 是否應該產生警告。| 
|**/p:**|VerifyCollationCompatibility=(BOOLEAN 'True')|指定是否驗證定序相容性。| 
|**/p:**|VerifyDeployment=(BOOLEAN 'True')|指定是否應該在發行前執行檢查，以便在出現阻止發行成功的問題時停止發行動作。 例如，如果目標資料庫的外部索引鍵不存在資料庫專案中，因而在您發行時造成錯誤，則發行動作可能會停止。 |
  
## <a name="driftreport-action-parameters"></a>DriftReport 動作參數

|參數|簡短形式|值|描述|
|---|---|---|---|
|**/Action:**|**/a**|DriftReport|指定要執行的動作。 |
|**/AccessToken:**|**/at**|{string}| 根據要在連線到目標資料庫時使用的驗證存取權杖，來指定權杖。 |
|**/Diagnostics:**|**/d**|{True&#124;False}|指定診斷記錄是否輸出到主控台。 預設為 False。 |
|**/DiagnosticsFile:**|**/df**|{string}|指定要儲存診斷記錄的檔案。 |
|**/MaxParallelism:**|**/mp**|{int}| 指定針對資料庫執行之並行作業的平行處理原則的程度。 預設值為 8。 |
|**/OutputPath:**|**/op**|{string}|指定輸出檔案產生位置的檔案路徑。 |
|**/OverwriteFiles:**|**/of**|{True&#124;False}|指定 sqlpackage.exe 是否應該覆寫現有的檔案。 指定 false 會導致 sqlpackage.exe 在遇到現有的檔案時中止動作。 預設值是 True。 |
|**/Quiet:**|**/q**|{True&#124;False}|指定是否隱藏詳細的意見反應。 預設為 False。|
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

## <a name="next-steps"></a>後續步驟

- 深入了解 [sqlpackage](sqlpackage.md)
