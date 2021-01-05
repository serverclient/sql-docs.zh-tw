---
description: SSIS 目錄
title: SSIS 目錄 | Microsoft Docs
ms.custom: ''
ms.date: 09/17/2020
ms.prod: sql
ms.prod_service: integration-services
ms.reviewer: ''
ms.technology: integration-services
ms.topic: conceptual
f1_keywords:
- sql13.ssis.ssms.iscreatecatalog.f1
- sql13.ssis.ssms.iscatalogprop.general.f1
- sql13.ssis.dbupgradewizard.f1
ms.assetid: 24bd987e-164a-48fd-b4f2-cbe16a3cd95e
author: chugugrace
ms.author: chugu
ms.openlocfilehash: a8ed14b14198bb95db838e76ca07bffe5875ce69
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97642584"
---
# <a name="ssis-catalog"></a>SSIS 目錄

[!INCLUDE[sqlserver-ssis](../../includes/applies-to-version/sqlserver-ssis.md)]


  **SSISDB** 目錄是處理您已部署至 [!INCLUDE[ssISnoversion_md](../../includes/ssisnoversion-md.md)] 伺服器之 [!INCLUDE[ssISnoversion_md](../../includes/ssisnoversion-md.md)] (SSIS) 專案的中心點。 例如，您可以設定專案和封裝參數、設定環境以指定封裝的執行值、執行和疑難排解封裝，以及管理 [!INCLUDE[ssISnoversion_md](../../includes/ssisnoversion-md.md)] 伺服器作業。  
 
> [!NOTE]
> 本文描述一般 SSIS 目錄，以及在內部部署執行的 SSIS 目錄。 您也可以在 Azure SQL Database 中建立 SSIS 目錄，並在 Azure 中部署和執行 SSIS 套件。 如需詳細資訊，請參閱[將 SQL Server Integration Services 工作負載隨即轉移至雲端](../lift-shift/ssis-azure-lift-shift-ssis-packages-overview.md)。
>
> 雖然您也可以在 Linux 上執行 SSIS 套件，但 Linux 不支援 SSIS 目錄。 如需詳細資訊，請參閱[使用 SSIS 在 Linux 上擷取、轉換和載入資料](../../linux/sql-server-linux-migrate-ssis.md)。
 
 儲存在 **SSISDB** 目錄中的物件包含專案、封裝、參數、環境和作業記錄。  
  
 您可以查詢 **SSISDB** 資料庫中的檢視表，以檢查儲存在 **SSISDB** 目錄中的物件、設定及作業資料。 若要管理物件，請呼叫 **SSISDB** 資料庫中的預存程序或是使用 **SSISDB** 目錄的 UI。 在許多情況下，可以在此 UI 中或是藉由呼叫預存程序來執行相同的工作。  
  
 若要維護 **SSISDB** 資料庫，建議您套用管理使用者資料庫的標準企業原則。 如需有關建立維護計畫的詳細資訊，請參閱＜ [Maintenance Plans](../../relational-databases/maintenance-plans/maintenance-plans.md)＞。  
  
 **SSISDB** 目錄和 **SSISDB** 資料庫都支援 Windows PowerShell。 如需有關使用 SQL Server 搭配 Windows PowerShell 的詳細資訊，請參閱＜ [SQL Server PowerShell](../../powershell/sql-server-powershell.md)＞。 如需有關如何使用 Windows PowerShell 完成部署專案等工作的範例，請參閱 blogs.msdn.com 上的部落格文章： [SQL Server 2012 中的 SSIS 和 PowerShell](https://techcommunity.microsoft.com/t5/sql-server-integration-services/ssis-and-powershell-in-sql-server-2012/ba-p/388015)。  
  
 如需檢視作業資料的詳細資訊，請參閱 [監視封裝執行和其他作業](../../integration-services/performance/monitor-running-packages-and-other-operations.md)。  
  
 若要在 **中存取** SSISDB [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 目錄，請連接到 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Database Engine，然後在 [物件總管] 中展開 **[Integration Services 目錄]** 節點。 若要在 **中存取** SSISDB [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 資料庫，請在 [物件總管] 中展開 [資料庫] 節點。  
  
> [!NOTE]
> 您無法為 **SSISDB** 資料庫重新命名。  
  
> [!NOTE]
> 如果附加 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] SSISDB **資料庫的** 執行個體停止或沒有回應，ISServerExec.exe 處理序便會結束。 會在 Windows 事件記錄檔中寫入一則訊息。  
>   
>  如果 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 資源的容錯移轉是叢集容錯移轉的一部分，就不會重新啟動執行中的套件。 您可以使用檢查點重新啟動封裝。 如需詳細資訊，請參閱 [使用檢查點來重新啟動封裝](../../integration-services/packages/restart-packages-by-using-checkpoints.md)。  
  
## <a name="features-and-capabilities"></a>功能  
  
-   [目錄物件識別碼](../../integration-services/catalog/ssis-catalog.md#CatalogObjectIdentifiers)  
  
-   [目錄組態](../../integration-services/catalog/ssis-catalog.md#Configuration)  
  
-   [權限](../../integration-services/catalog/ssis-catalog.md#Permissions)  
  
-   [資料夾](../../integration-services/catalog/ssis-catalog.md#Folders)  
  
-   [專案和套件](../../integration-services/catalog/ssis-catalog.md#ProjectsAndPackages)  
  
-   [參數](../../integration-services/catalog/ssis-catalog.md#Parameters)  
  
-   [伺服器環境、伺服器變數和伺服器環境參考](../../integration-services/catalog/ssis-catalog.md#ServerEnvironments)  
  
-   [執行和驗證](../../integration-services/catalog/ssis-catalog.md#Executions)  

##  <a name="catalog-object-identifiers"></a><a name="CatalogObjectIdentifiers"></a> 目錄物件識別碼  
 當您在目錄中建立新的物件時，請為此物件指派名稱。 物件名稱是識別碼。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會定義哪些字元可以在識別碼中使用的規則。 隨後的物件名稱必須遵照識別碼規則。  
  
-   資料夾  
  
-   隨附此逐步解說的專案  
  
-   環境  
  
-   參數  
  
-   環境變數  
  
###  <a name="folder-project-environment"></a><a name="Folder"></a> 資料夾、專案、環境  
 在重新命名資料夾、專案或環境時，請考慮以下規則。  
  
-   無效的字元包括 ASCII/Unicode 字元 1 到 31、引號 (")、小於 (\<), greater than (>)、直立線符號 (|)、退格鍵 (\b)、null (\0) 和 Tab 鍵 (\t)。  
  
-   名稱不得包含開頭或尾端空格。  
  
-   不允許以 \@ 作為第一個字元，但隨後的字元可以使用 \@。  
  
-   名稱的長度必須大於 0 且小於或等於 128。  
  
###  <a name="parameter"></a><a name="Parameter"></a> 參數  
 在命名參數時，請考慮以下規則。  
  
-   名稱的第一個字元必須是 Unicode Standard 2.0 中所定義的字母，或是底線 (_)。  
  
-   後續的字元可以是 Unicode Standard 2.0 中定義的字母或數字，或是底線 (_)。  
  
###  <a name="environment-variable"></a><a name="EnvironmentVariable"></a> 環境變數  
 在命名環境變數時，請考慮以下規則。  
  
-   無效的字元包括 ASCII/Unicode 字元 1 到 31、引號 (")、小於 (\<), greater than (>)、直立線符號 (|)、退格鍵 (\b)、null (\0) 和 Tab 鍵 (\t)。  
  
-   名稱不得包含開頭或尾端空格。  
  
-   不允許以 \@ 作為第一個字元，但隨後的字元可以使用 \@。  
  
-   名稱的長度必須大於 0 且小於或等於 128。  
  
-   名稱的第一個字元必須是 Unicode Standard 2.0 中所定義的字母，或是底線 (_)。  
  
-   後續的字元可以是 Unicode Standard 2.0 中定義的字母或數字，或是底線 (_)。  
  
##  <a name="catalog-configuration"></a><a name="Configuration"></a> 目錄組態  
 您會藉由調整目錄屬性來微調目錄的行為模式。 目錄屬性會定義如何加密敏感性資料以及如何保留作業和專案版本設定資料。 若要設定目錄屬性，請使用 [目錄屬性] 對話方塊，或是呼叫 [catalog.configure_catalog &#40;SSISDB 資料庫&#41;](../../integration-services/system-stored-procedures/catalog-configure-catalog-ssisdb-database.md) 預存程序。 若要檢視屬性，請使用對話方塊或查詢 [catalog.catalog_properties &#40;SSISDB 資料庫&#41;](../../integration-services/system-views/catalog-catalog-properties-ssisdb-database.md)。 您可在物件總管中以滑鼠右鍵按一下 [SSISDB] 來存取此對話方塊。  
  
###  <a name="operations-and-project-version-cleanup"></a><a name="Cleanup"></a> 作業和專案版本清除  
 目錄中許多作業的狀態資料會儲存在內部資料庫資料表中。 例如，目錄會追蹤封裝執行和專案部署的狀態。 為了維護作業資料的大小， **中的** [SSIS Server 維護作業] [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 會用來移除舊的資料。 安裝 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 時會建立此 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] Agent 作業。  
  
 若要更新或重新部署 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 專案，請使用相同名稱將它部署到目錄中的相同資料夾。 根據預設，每當您重新部署專案時， **SSISDB** 目錄都會保留此專案的舊版。 為了維護作業資料的大小， **[SSIS Server 維護作業]** 會用來移除專案的舊版。  
 
為執行 **SSIS Server 維護作業**，SSIS 會建立 SQL Server 登入 **##MS_SSISServerCleanupJobLogin##** 。 此登入僅供 SSIS 內部使用。
  
 以下 **[SSISDB]** 目錄屬性會定義此 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent 作業的行為模式。 您可以使用 [目錄屬性] 對話方塊或使用 [catalog.catalog_properties &#40;SSISDB 資料庫&#41;](../../integration-services/system-views/catalog-catalog-properties-ssisdb-database.md) 和 [catalog.configure_catalog &#40;SSISDB 資料庫&#41;](../../integration-services/system-stored-procedures/catalog-configure-catalog-ssisdb-database.md) 檢視及修改屬性。  
  
 **定期清除記錄檔**  
 當這個屬性設定為 **True** 時，便會執行作業清除的作業步驟。  
  
 **保留週期 (天)**  
 定義可允許的作業資料存在時間上限 (以天為單位)。 移除較舊的資料。  
  
 最小值是一天。 最大值只會受到 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] **int** 資料最大值的限制。 如需此資料類型的資訊，請參閱 [int、bigint、smallint 和 tinyint &#40;Transact-SQL&#41;](../../t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql.md)。  
  
 **定期移除舊版本**  
 當這個屬性設定為 **True** 時，便會執行專案版本清除的作業步驟。  
  
 **每一專案的版本數目上限**  
 定義多少個專案版本儲存在目錄中。 移除專案的舊版。  
  
###  <a name="encryption-algorithm"></a><a name="Encryption"></a> 加密演算法  
 **[加密演算法]** 屬性會指定用來加密敏感性參數值的加密類型。 您可以從以下類型的加密中選擇。  
  
-   AES_256 (預設)  
  
-   AES_192  
  
-   AES_128  
  
-   DESX  
  
-   TRIPLE_DES_3KEY  
  
-   TRIPLE_DES  
  
-   DES  
  
 當您將 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 專案部署至 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 伺服器時，目錄會自動將封裝資料與敏感值加密。 當您擷取時，目錄也會自動解密資料。 SSISDB 目錄會使用 **ServerStorage** 保護等級。 如需詳細資訊，請參閱 [Access Control for Sensitive Data in Packages](../../integration-services/security/access-control-for-sensitive-data-in-packages.md)。  
  
 變更加密演算法是需要大量時間的作業。 首先，伺服器必須使用先前指定的演算法來解密所有組態值。 然後，伺服器必須使用新的演算法來重新加密值。 在這段期間，伺服器上不能有其他的 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 作業。 因此，為了讓 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 作業持續不受干擾，在 [!INCLUDE[ssManStudio](../../includes/ssmanstudio-md.md)]的此對話方塊中，加密演算法會是唯讀值。  
  
 若要變更 **[加密演算法]** 屬性設定，請將 **SSISDB** 資料庫設為單一使用者模式，然後呼叫 catalog.configure_catalog 預存程序。 使用 ENCRYPTION_ALGORITHM 指定 *property_name* 引數。 如需支援的屬性值，請參閱 [catalog.catalog_properties &#40;SSISDB 資料庫&#41;](../../integration-services/system-views/catalog-catalog-properties-ssisdb-database.md)。 如需預存程序的詳細資訊，請參閱 [catalog.configure_catalog &#40;SSISDB 資料庫&#41;](../../integration-services/system-stored-procedures/catalog-configure-catalog-ssisdb-database.md)。  
  
 如需單一使用者模式的詳細資訊，請參閱 [將資料庫設定為單一使用者模式](../../relational-databases/databases/set-a-database-to-single-user-mode.md)。 如需 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]中加密和加密演算法的資訊，請參閱 [SQL Server 加密](../../relational-databases/security/encryption/sql-server-encryption.md)一節中的主題。  
  
 資料庫主要金鑰會用於加密。 當您建立目錄時會建立此金鑰。  
  
 下表列出 **[目錄屬性]** 對話方塊中所顯示的屬性名稱，以及資料庫檢視中的對應屬性。  
  
|屬性名稱 ( **[目錄屬性]** 對話方塊)|屬性名稱 (資料庫檢視)|  
|---------------------------------------------------------|-------------------------------------|  
|加密演算法名稱|ENCRYPTION_ALGORITHM|  
|定期清除記錄檔|OPERATION_CLEANUP_ENABLEDâ€‹|  
|保留週期 (天)|RETENTION_WINDOW|  
|定期移除舊版本|VERSION_CLEANUP_ENABLED|  
|每一專案的版本數目上限|MAX_PROJECT_VERSIONS|  
|全伺服器的預設記錄層次|SERVER_LOGGING_LEVEL|  
  
##  <a name="permissions"></a><a name="Permissions"></a> 權限  
 專案、環境和封裝會包含在屬於安全性實體物件的資料夾中。 您可以將權限授與資料夾，包括 MANAGE_OBJECT_PERMISSIONS 權限。 MANAGE_OBJECT_PERMISSIONS 可讓您將資料夾內容管理委派給使用者，而不必將使用者成員資格授與 ssis_admin 角色。 您還可以授與權限給專案、環境和作業。 作業包括初始化 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)]、部署專案、建立及啟動執行、驗證專案和封裝及設定 **SSISDB** 目錄。  
  
 如需資料庫角色的詳細資訊，請參閱 [資料庫層級角色](../../relational-databases/security/authentication-access/database-level-roles.md)。  
  
 SSISDB 目錄會使用 DDL 觸發程序 ddl_cleanup_object_permissions 來強制 SSIS 安全性實體之權限資訊的完整性。 當資料庫主體 (例如資料庫使用者、資料庫角色或資料庫應用程式角色) 從 SSISDB 資料庫中移除時，便會引發此觸發程序。  
  
 如果此主體已被授與或拒絕其他主體的權限，請撤銷授與者所提供的權限，然後才可移除該主體。 否則，當系統嘗試移除此主體時，便會傳回錯誤訊息。 此觸發程序會移除所有權限記錄，在這些記錄中，資料庫主體為被授與者。  
  
 建議您不應該停用此觸發程序，因為它會確保從 **SSISDB** 資料庫卸除資料庫主體之後，不會有任何被遺棄的權限記錄。  
  
### <a name="managing-permissions"></a>管理權限  
 您可以使用 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] UI、預存程序及 <xref:Microsoft.SqlServer.Management.IntegrationServices> 命名空間來管理權限。  
  
 若要使用 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] UI 來管理權限，請使用以下對話方塊： 
  
-   針對資料夾，使用 **Folder Properties Dialog Box** 的 [&#91;權限&#93;](../../integration-services/catalog/folder-properties-dialog-box.md)頁面。  
  
-   針對專案中，使用 **權限** 頁面 [Project Properties Dialog Box](../../integration-services/catalog/project-properties-dialog-box.md)。  

 若要使用 Transact-SQL 來管理權限，請呼叫 [catalog.grant_permission &#40;SSISDB 資料庫&#41;](../../integration-services/system-stored-procedures/catalog-grant-permission-ssisdb-database.md)、[catalog.deny_permission &#40;SSISDB 資料庫&#41;](../../integration-services/system-stored-procedures/catalog-deny-permission-ssisdb-database.md) 及 [catalog.revoke_permission &#40;SSISDB 資料庫&#41;](../../integration-services/system-stored-procedures/catalog-revoke-permission-ssisdb-database.md)。 若要檢視對所有物件之目前主體有效的權限，請查詢 [catalog.effective_object_permissions &#40;SSISDB 資料庫&#41;](../../integration-services/system-views/catalog-effective-object-permissions-ssisdb-database.md)。 此主題會提供不同類型之權限的描述。 若要檢視已明確指派給使用者的權限，請查詢 [catalog.explicit_object_permissions &#40;SSISDB 資料庫&#41;](../../integration-services/system-views/catalog-explicit-object-permissions-ssisdb-database.md)。  
  
##  <a name="folders"></a><a name="Folders"></a> 資料夾  
 資料夾包含 **SSISDB** 目錄中的一個或多個專案和環境。 您可以使用 [catalog.folders &#40;SSISDB 資料庫&#41;](../../integration-services/system-views/catalog-folders-ssisdb-database.md) 檢視來存取目錄中資料夾的相關資訊。 您可以使用以下預存程序來管理資料夾：  
  
-   [catalog.create_folder &#40;SSISDB 資料庫&#41;](../../integration-services/system-stored-procedures/catalog-create-folder-ssisdb-database.md)  
  
-   [catalog.delete_folder &#40;SSISDB 資料庫&#41;](../../integration-services/system-stored-procedures/catalog-delete-folder-ssisdb-database.md)  
  
-   [catalog.rename_folder &#40;SSISDB 資料庫&#41;](../../integration-services/system-stored-procedures/catalog-rename-folder-ssisdb-database.md)  
  
-   [catalog.set_folder_description &#40;SSISDB 資料庫&#41;](../../integration-services/system-stored-procedures/catalog-set-folder-description-ssisdb-database.md)  
  
##  <a name="projects-and-packages"></a><a name="ProjectsAndPackages"></a> 專案和套件  
 每一個專案都可包含多個封裝。 專案和封裝都可以包含參數及環境的參考。 您可以使用 [Configure Dialog Box](../../integration-services/catalog/configure-dialog-box.md)來存取參數和環境參考。  
  
 您可以藉由呼叫以下預存程序來完成其他專案工作： 
  
-   [catalog.delete_project &#40;SSISDB 資料庫&#41;](../../integration-services/system-stored-procedures/catalog-delete-project-ssisdb-database.md)  
  
-   [catalog.deploy_project &#40;SSISDB 資料庫&#41;](../../integration-services/system-stored-procedures/catalog-deploy-project-ssisdb-database.md)  
  
-   [catalog.get_project &#40;SSISDB 資料庫&#41;](../../integration-services/system-stored-procedures/catalog-get-project-ssisdb-database.md)  
  
-   [catalog.move_project &#40;&#40;SSISDB 資料庫&#41;](../../integration-services/system-stored-procedures/catalog-move-project-ssisdb-database.md)  
  
-   [catalog.restore_project &#40;SSISDB 資料庫&#41;](../../integration-services/system-stored-procedures/catalog-restore-project-ssisdb-database.md)  
  
 這些檢視表會提供有關封裝、專案和專案版本的詳細資料。  
  
-   [catalog.projects &#40;SSISDB 資料庫&#41;](../../integration-services/system-views/catalog-projects-ssisdb-database.md)  
  
-   [catalog.packages &#40;SSISDB 資料庫&#41;](../../integration-services/system-views/catalog-packages-ssisdb-database.md)  
  
-   [catalog.object_versions &#40;SSISDB 資料庫&#41;](../../integration-services/system-views/catalog-object-versions-ssisdb-database.md)  
  
##  <a name="parameters"></a><a name="Parameters"></a> 參數  
 您在封裝執行時，可使用參數將值指派給封裝屬性。 若要設定封裝或專案參數的值及清除該值，請呼叫 [catalog.set_object_parameter_value &#40;SSISDB 資料庫&#41;](../../integration-services/system-stored-procedures/catalog-set-object-parameter-value-ssisdb-database.md) 和 [catalog.clear_object_parameter_value &#40;SSISDB 資料庫&#41;](../../integration-services/system-stored-procedures/catalog-clear-object-parameter-value-ssisdb-database.md)。 若要為執行的執行個體設定參數值，請呼叫 [catalog.set_execution_parameter_value &#40;SSISDB 資料庫&#41;](../../integration-services/system-stored-procedures/catalog-set-execution-parameter-value-ssisdb-database.md)。 您可以藉由呼叫 [catalog.get_parameter_values &#40;SSISDB 資料庫&#41;](../../integration-services/system-stored-procedures/catalog-get-parameter-values-ssisdb-database.md) 擷取預設參數值。  
  
 這些檢視表會顯示所有封裝和專案的參數，以及用於執行執行個體的參數值。  
  
-   [catalog.object_parameters &#40;SSISDB 資料庫&#41;](../../integration-services/system-views/catalog-object-parameters-ssisdb-database.md)  
  
-   [catalog.execution_parameter_values &#40;SSISDB 資料庫&#41;](../../integration-services/system-views/catalog-execution-parameter-values-ssisdb-database.md)  
  
##  <a name="server-environments-server-variables-and-server-environment-references"></a><a name="ServerEnvironments"></a> 伺服器環境、伺服器變數和伺服器環境參考  
 伺服器環境包含伺服器變數。 在 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 伺服器上執行或驗證封裝時，可以使用變數值。  
  
 以下預存程序可讓您執行環境與變數的許多其他管理工作。  
  
-   [catalog.create_environment &#40;SSISDB 資料庫&#41;](../../integration-services/system-stored-procedures/catalog-create-environment-ssisdb-database.md)  
  
-   [catalog.delete_environment &#40;SSISDB 資料庫&#41;](../../integration-services/system-stored-procedures/catalog-delete-environment-ssisdb-database.md)  
  
-   [catalog.move_environment &#40;SSISDB 資料庫&#41;](../../integration-services/system-stored-procedures/catalog-move-environment-ssisdb-database.md)  
  
-   [catalog.rename_environment &#40;SSISDB 資料庫&#41;](../../integration-services/system-stored-procedures/catalog-rename-environment-ssisdb-database.md)  
  
-   [catalog.set_environment_property &#40;SSISDB 資料庫&#41;](../../integration-services/system-stored-procedures/catalog-set-environment-property-ssisdb-database.md)  
  
-   [catalog.create_environment_variable &#40;SSISDB 資料庫&#41;](../../integration-services/system-stored-procedures/catalog-create-environment-variable-ssisdb-database.md)  
  
-   [catalog.delete_environment_variable &#40;SSISDB 資料庫&#41;](../../integration-services/system-stored-procedures/catalog-delete-environment-variable-ssisdb-database.md)  
  
-   [catalog.set_environment_variable_property &#40;SSISDB 資料庫&#41;](../../integration-services/system-stored-procedures/catalog-set-environment-variable-property-ssisdb-database.md)  
  
-   [catalog.set_environment_variable_value &#40;SSISDB 資料庫&#41;](../../integration-services/system-stored-procedures/catalog-set-environment-variable-value-ssisdb-database.md)  
  
 您可以藉由呼叫 [catalog.set_environment_variable_protection &#40;SSISDB 資料庫&#41;](../../integration-services/system-stored-procedures/catalog-set-environment-variable-protection-ssisdb-database.md) 預存程序，設定變數的敏感度位元。  
  
 若要使用伺服器變數的值，請指定專案與伺服器環境之間的參考。 您可以使用以下預存程序來建立和刪除參考。 您也可以指出環境是否位於與專案相同的資料夾中，或是在不同的資料夾中。  
  
-   [catalog.create_environment_reference &#40;SSISDB 資料庫&#41;](../../integration-services/system-stored-procedures/catalog-create-environment-reference-ssisdb-database.md)  
  
-   [catalog.delete_environment_reference &#40;SSISDB 資料庫&#41;](../../integration-services/system-stored-procedures/catalog-delete-environment-reference-ssisdb-database.md)  
  
-   [catalog.set_environment_reference_type &#40;SSISDB 資料庫&#41;](../../integration-services/system-stored-procedures/catalog-set-environment-reference-type-ssisdb-database.md)  
  
 如需有關環境和變數的其他詳細資料，請查詢這些檢視表。  
  
-   [catalog.environments &#40;SSISDB 資料庫&#41;](../../integration-services/system-views/catalog-environments-ssisdb-database.md)  
  
-   [catalog.environment_variables &#40;SSISDB 資料庫&#41;](../../integration-services/system-views/catalog-environment-variables-ssisdb-database.md)  
  
-   [catalog.environment_references &#40;SSISDB 資料庫&#41;](../../integration-services/system-views/catalog-environment-references-ssisdb-database.md)  
  
##  <a name="executions-and-validations"></a><a name="Executions"></a> 執行和驗證  
 執行是封裝執行的執行個體。 呼叫 [catalog.create_execution &#40;SSISDB Database&#41;](../../integration-services/system-stored-procedures/catalog-create-execution-ssisdb-database.md) 及 [catalog.start_execution &#40;SSISDB Database&#41;](../../integration-services/system-stored-procedures/catalog-start-execution-ssisdb-database.md) 可建立並開始執行。 若要停止執行或封裝/專案驗證，請呼叫 [catalog.stop_operation &#40;SSISDB 資料庫&#41;](../../integration-services/system-stored-procedures/catalog-stop-operation-ssisdb-database.md)。  
  
 若要使執行中的封裝暫停並建立傾印檔案，請呼叫 catalog.create_execution_dump 預存程序。 傾印檔案會提供有關封裝執行的資訊，可幫助您針對執行問題進行疑難排解。 如需有關產生及設定傾印檔案的詳細資訊，請參閱＜ [Generating Dump Files for Package Execution](../../integration-services/troubleshooting/generating-dump-files-for-package-execution.md)＞。  
  
 如需有關作業期間所記錄之執行、驗證和訊息以及與錯誤相關之內容資訊的詳細資料，請查詢這些檢視表。  
  
-   [catalog.executions &#40;SSISDB 資料庫&#41;](../../integration-services/system-views/catalog-executions-ssisdb-database.md)  
  
-   [catalog.operations &#40;SSISDB 資料庫&#41;](../../integration-services/system-views/catalog-operations-ssisdb-database.md)  
  
-   [catalog.operation_messages &#40;SSISDB 資料庫&#41;](../../integration-services/system-views/catalog-operation-messages-ssisdb-database.md)  
  
-   [catalog.extended_operation_info &#40;SSISDB 資料庫&#41;](../../integration-services/system-views/catalog-extended-operation-info-ssisdb-database.md)  
  
-   [catalog.event_messages](../../integration-services/system-views/catalog-event-messages.md)  
  
-   [catalog.event_message_context](../../integration-services/system-views/catalog-event-message-context.md)  
  
 您可以藉由呼叫 [catalog.validate_project &#40;SSISDB 資料庫&#41;](../../integration-services/system-stored-procedures/catalog-validate-project-ssisdb-database.md) 和 [catalog.validate_package &#40;SSISDB 資料庫&#41;](../../integration-services/system-stored-procedures/catalog-validate-package-ssisdb-database.md) 預存程序來驗證專案與封裝。 [catalog.validations &#40;SSISDB Database&#41;](../../integration-services/system-views/catalog-validations-ssisdb-database.md) 檢視會提供有關驗證的詳細資料，例如驗證時所考量的伺服器環境參考、這是相依性驗證還是完整驗證，以及使用 32 位元執行階段還是 64 位元執行階段來執行封裝。  

## <a name="create-the-ssis-catalog"></a>建立 SSIS 目錄
  在 [!INCLUDE[ssBIDevStudio](../../includes/ssbidevstudio-md.md)]中設計和測試封裝之後，可以將包含封裝的專案，部署到 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 伺服器。 在您將專案部署至 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 伺服器之前，該伺服器必須包含 **SSISDB** 目錄。 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 的安裝程式不會自動建立目錄，您必須依照下列指示手動建立目錄。  
  
 您可以在 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)]中建立 SSISDB 目錄。 您也可以使用 Windows PowerShell 以程式設計方式建立目錄。  
  
### <a name="to-create-the-ssisdb-catalog-in-sql-server-management-studio"></a>若要在 SQL Server Management Studio 中建立 SSISDB 目錄  
  
1.  開啟 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)]。  
  
2.  連接到 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Database Engine。  
  
3.  在物件總管中，展開伺服器節點，以滑鼠右鍵按一下 [Integration Services 目錄]  節點，然後按一下 [建立目錄] 。  
  
4.  按一下 **[啟用 CLR 整合]** 。  
  
     目錄便會使用 CLR 預存程序。  
  
5.  按一下 [在 SQL Server 啟動時允許自動執行 Integration Services 預存程序]，讓 [catalog.startup](../../integration-services/system-stored-procedures/catalog-startup.md) 預存程序會在每次 [!INCLUDE[ssIS](../../includes/ssis-md.md)] 伺服器執行個體重新啟動時執行。  
  
     預存程序會執行 SSISDB 目錄之作業狀態的維護。 如果 [!INCLUDE[ssIS](../../includes/ssis-md.md)] 伺服器執行個體關閉，它就會修正任何執行中套件的狀態。  
  
6.  輸入密碼，然後按一下 **[確定]** 。  
  
     此密碼保護用來加密目錄資料的資料庫主要金鑰。 請將密碼儲存在安全位置。 建議您同時備份資料庫主要金鑰。 如需相關資訊，請參閱 [Back Up a Database Master Key](../../relational-databases/security/encryption/back-up-a-database-master-key.md)。  
  
### <a name="to-create-the-ssisdb-catalog-programmatically"></a>若要以程式設計方式建立 SSISDB 目錄  
  
1.  執行下列 PowerShell 指令碼：  
  
    ```  
    # Load the IntegrationServices Assembly  
    [Reflection.Assembly]::LoadWithPartialName("Microsoft.SqlServer.Management.IntegrationServices")  
  
    # Store the IntegrationServices Assembly namespace to avoid typing it every time  
    $ISNamespace = "Microsoft.SqlServer.Management.IntegrationServices"  
  
    Write-Host "Connecting to server ..."  
  
    # Create a connection to the server  
    $sqlConnectionString = "Data Source=localhost;Initial Catalog=master;Integrated Security=SSPI;"  
    $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $sqlConnectionString  
  
    # Create the Integration Services object  
    $integrationServices = New-Object $ISNamespace".IntegrationServices" $sqlConnection  
  
    # Provision a new SSIS Catalog  
    $catalog = New-Object $ISNamespace".Catalog" ($integrationServices, "SSISDB", "P@assword1")  
    $catalog.Create()  
  
    ```  
  
     如需如何使用 Windows PowerShell 和 <xref:Microsoft.SqlServer.Management.IntegrationServices> 命名空間的其他範例，請參閱 blogs.msdn.com 上的部落格文章：[SQL Server 2012 中的 SSIS 和 PowerShell](https://techcommunity.microsoft.com/t5/sql-server-integration-services/ssis-and-powershell-in-sql-server-2012/ba-p/388015)。 如需此命名空間的概觀和程式碼範例，請參閱 blogs.msdn.com 上的部落格文章： [SSIS 目錄管理物件模型初探](https://techcommunity.microsoft.com/t5/sql-server-integration-services/a-glimpse-of-the-ssis-catalog-managed-object-model/ba-p/387892)。  

## <a name="catalog-properties-dialog-box"></a>目錄屬性對話方塊
  使用 [目錄屬性] 對話方塊來設定 SSISDB 目錄。 目錄屬性定義如何加密敏感性資料，如何保留作業和專案版本設定資料，以及何時驗證作業逾時。SSISDB 目錄是 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 專案、封裝、參數與環境的中央儲存和管理點。  
  
 您也可以在 `catalog.catalog_properties` 檢視表中檢視目錄屬性，以及使用 `catalog.configure_catalog` 預存程序來設定屬性。 如需詳細資訊，請參閱 [catalog.catalog_properties &#40;SSISDB 資料庫&#41;](../../integration-services/system-views/catalog-catalog-properties-ssisdb-database.md) 和 [catalog.configure_catalog &#40;SSISDB 資料庫&#41;](../../integration-services/system-stored-procedures/catalog-configure-catalog-ssisdb-database.md)。  
  
 **您想要做什麼事？**  
  
-   [開啟目錄屬性對話方塊](#open_dialog)  
  
-   [設定選項](#options)  
  
###  <a name="open-the-catalog-properties-dialog-box"></a><a name="open_dialog"></a> 開啟目錄屬性對話方塊  
  
1.  開啟 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)][!INCLUDE[ssManStudio](../../includes/ssmanstudio-md.md)]  
  
2.  連接 Microsoft SQL Server Database Engine。  
  
3.  在物件總管中，展開 [Integration Services] 節點，並以滑鼠右鍵按一下 [SSISDB]，然後按一下 [屬性]。  
  
###  <a name="configure-the-options"></a><a name="options"></a> 設定選項  
  
#### <a name="options"></a>選項。  
 下表描述對話方塊中的特定屬性，以及 `catalog.catalog_properties` 檢視表中的對應屬性。  
  
|屬性名稱 (目錄屬性對話方塊)|屬性名稱 (catalog.catalog_properties 檢視表)|描述|  
|-----------------------------------------------------|------------------------------------------------------|-----------------|  
|加密演算法名稱|ENCRYPTION_ALGORITHM|指定用來加密目錄中敏感性參數值的加密類型。 以下是可能的值：<br /><br /> DES<br /><br /> TRIPLE_DES<br /><br /> TRIPLE_DES_3KEY<br /><br /> DESPX<br /><br /> AES_128<br /><br /> AES_192<br /><br /> AES_256 (預設)|  
|每一專案的版本數目上限|MAX_PROJECT_VERSIONS|指定目錄中所儲存的專案版本數目。 當專案版本清除作業執行時，會移除超過上限的舊專案版本。|  
|定期清除記錄檔|OPERATION_CLEANUP_ENABLED|將屬性設為 True，指出 SQL Server Agent 作業 (作業清除) 會執行。 否則請將屬性設為 False。|  
|保留週期 (天)|RETENTION_WINDOW|指定可允許的作業資料存在時間上限 (以天為單位)。 SQL Agent 作業 (作業清除) 會移除比指定天數還舊的資料。|  

## <a name="back-up-restore-and-move-the-ssis-catalog"></a>備份、還原和移動 SSIS 目錄
[!INCLUDE[tsql-appliesto-ss2016-xxxx-xxxx-xxx_md](../../includes/tsql-appliesto-ss2016-xxxx-xxxx-xxx-md.md)]

  [!INCLUDE[ssISCurrent](../../includes/ssiscurrent-md.md)] 包括 SSISDB 資料庫。 您可以查詢 SSISDB 資料庫中的檢視，以檢查物件、設定以及儲存在 [SSISDB] 目錄中的作業資料。 本主題提供備份與還原資料庫的指示。  
  
 **SSISDB** 目錄會儲存您已經部署到 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 伺服器的封裝。 如需目錄的詳細資訊，請參閱 [SSIS 目錄](../../integration-services/catalog/ssis-catalog.md)。  
  
###  <a name="to-back-up-the-ssis-database"></a><a name="backup"></a> 若要備份 SSIS 資料庫  
  
1.  開啟 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 並連接至 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]的執行個體。  
  
2.  使用 BACKUP MASTER KEY Transact-SQL 陳述式，備份 SSISDB 資料庫的主要金鑰。 此金鑰儲存在您指定的檔案。 使用密碼來加密檔案中的主要金鑰密碼。  
  
     如需陳述式的詳細資訊，請參閱 [BACKUP MASTER KEY &#40;Transact-SQL&#41;](../../t-sql/statements/backup-master-key-transact-sql.md)。  
  
     在下列範例中，主要金鑰是匯出至 `c:\temp directory\RCTestInstKey` 檔案。 `LS2Setup!` 密碼是用來加密主要金鑰。  
  
    ```  
    backup master key to file = 'c:\temp\RCTestInstKey'  
           encryption by password = 'LS2Setup!'  
  
    ```  
  
3.  使用 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 中的 [備份資料庫] 對話方塊備份 SSISDB 資料庫。 如需詳細資訊，請參閱[如何：備份資料庫 (SQL Server Management Studio)](../../relational-databases/backup-restore/create-a-full-database-backup-sql-server.md)。  
  
4.  執行下列動作，以產生 ##MS_SSISServerCleanupJobLogin## 的 CREATE LOGIN 指令碼。 如需詳細資訊，請參閱 [CREATE LOGIN &#40;Transact-SQL&#41;](../../t-sql/statements/create-login-transact-sql.md)。  
  
    1.  在 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 的物件總管中，展開 [安全性] 節點，然後展開 [登入] 節點。  
  
    2.  以滑鼠右鍵按一下 [##MS_SSISServerCleanupJobLogin##]，然後按一下 [編寫登入的指令碼為] > [CREATE 至] > [新增查詢編輯器視窗]。  
  
5.  如果您要將 SSISDB 資料庫還原至從未建立過 SSISDB 目錄的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體，請執行下列動作，以產生 sp_ssis_startup 的 CREATE PROCEDURE 指令碼。 如需詳細資訊，請參閱 [CREATE PROCEDURE &#40;Transact-SQL&#41;](../../t-sql/statements/create-procedure-transact-sql.md)。  
  
    1.  在物件總管中，展開 [資料庫] 節點，然後展開 [master] > [可程式性] > [預存程序] 節點。  
  
    2.  以滑鼠右鍵按一下 **dbo.sp_ssis_startup**，然後按一下 [編寫預存程序的指令碼為] > [CREATE 至] > [新增查詢編輯器視窗]。  
  
6.  確認已啟動 SQL Server Agent  
  
7.  如果您要將 SSISDB 資料庫還原至從未建立過 SSISDB 目錄的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體，請執行下列動作，以產生 SSIS 伺服器維護作業的指令碼。 當您建立 SSISDB 目錄時，系統就會自動在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent 中建立指令碼。 此作業有助於清除保留週期外部的清除作業，並移除舊版專案。  
  
    1.  在物件總管中，展開 [SQL Server Agent] 節點，然後展開 [作業] 節點。  
  
    2.  以滑鼠右鍵按一下 SSIS 伺服器維護作業，然後按一下 [編寫作業的指令碼為] > [CREATE 至] > [新增查詢編輯器視窗]。  
  
### <a name="to-restore-the-ssis-database"></a>若要還原 SSIS 資料庫  
  
1.  如果您要將 SSISDB 資料庫還原至從未建立過 SSISDB 目錄的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體，請執行 `sp_configure` 預存程序來啟用 Common Language Runtime (CLR)。 如需詳細資訊，請參閱 [sp_configure &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-configure-transact-sql.md) 和 [CLR 已啟用選項](../../database-engine/configure-windows/clr-enabled-server-configuration-option.md)。  
  
    ```  
    use master   
           sp_configure 'clr enabled', 1  
           reconfigure  
  
    ```  
  
2.  如果您要將 SSISDB 資料庫還原至從未建立過 SSISDB 目錄的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體，請建立非對稱金鑰並根據非對稱金鑰建立登入，並且將 UNSAFE 權限授與登入。  

    ```  
    Create Asymmetric Key MS_SQLEnableSystemAssemblyLoadingKey  
           FROM Executable File = 'C:\Program Files\Microsoft SQL Server\YourSQLServerDefaultCompatibilityLevel\DTS\Binn\Microsoft.SqlServer.IntegrationServices.Server.dll'  
    ```  

    您可以從 [SQL Server 預設相容性層級清單](../../t-sql/statements/alter-database-transact-sql-compatibility-level.md#arguments)中尋找 `YourSQLServerDefaultCompatibilityLevel` 的值。
  
    [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] CLR 預存程序需要授與 UNSAFE 權限給登入，因為登入需要對於限制資源的額外存取，例如 Microsoft Win32 API。 如需 UNSAFE 程式碼權限的詳細資訊，請參閱 [建立組件](../../relational-databases/clr-integration/assemblies/creating-an-assembly.md)。  

    ```  
    Create Login ##MS_SQLEnableSystemAssemblyLoadingUser## FROM Asymmetric Key MS_SQLEnableSystemAssemblyLoadingKey   
    Grant Unsafe Assembly to ##MS_SQLEnableSystemAssemblyLoadingUser##    
    ```  
  
3.  使用 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 中的 [還原資料庫] 對話方塊，從備份還原 SSISDB 資料庫。 如需詳細資訊，請參閱下列主題：  
  
    -   [還原資料庫 &#40;一般頁面&#41;](../../relational-databases/backup-restore/restore-database-general-page.md)  
  
    -   [還原資料庫 &#40;檔案頁面&#41;](../../relational-databases/backup-restore/restore-database-files-page.md)  
  
    -   [還原資料庫 &#40;選項頁面&#41;](../../relational-databases/backup-restore/restore-database-options-page.md)  
  
4.  針對 ##MS_SSISServerCleanupJobLogin##、sp_ssis_startup 和 SSIS 伺服器維護作業，執行您在 [備份 SSIS 資料庫](#backup) 中所建立的指令碼。 確認已啟動 SQL Server Agent。  
  
5.  執行下列陳述式以設定 sp_ssis_startup 程序進行自動執行。 如需詳細資訊，請參閱 [sp_procoption &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-procoption-transact-sql.md)。  
  
    ```  
    EXEC sp_procoption N'sp_ssis_startup','startup','on'  
    ```  
  
6.  使用 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 中的 [登入屬性] 對話方塊，將 SSISDB 使用者 ##MS_SSISServerCleanupJobUser## (SSISDB 資料庫) 對應至 ##MS_SSISServerCleanupJobLogin##。  
  
7.  使用下列其中一種方法來還原主要金鑰。 如需加密的詳細資訊，請參閱 [加密階層](../../relational-databases/security/encryption/encryption-hierarchy.md)。  
  
    -   **方法 1**  
  
         如果您已經備份資料庫主要金鑰，而且您有用來加密主要金鑰的密碼，請使用此方法。  
  
        ```  
               Restore master key from file = 'c:\temp\RCTestInstKey'  
               Decryption by password = 'LS2Setup!' -- 'Password used to encrypt the master key during SSISDB backup'  
               Encryption by password = 'LS3Setup!' -- 'New Password'  
               Force  
  
        ```  
  
        > [!NOTE]  
        >  確認 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 服務帳戶擁有讀取備份金鑰檔案的權限。  
  
        > [!NOTE]  
        >  如果服務主要金鑰尚未加密資料庫主要金鑰，[!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 中即會顯示下列警告訊息。 忽略警告訊息。  
        >   
        >  **無法解密目前的主要金鑰。因為指定了 FORCE 選項，因此忽略了這個錯誤。**  
        >   
        >  FORCE 引數會指定即使未開放目前的資料庫主要金鑰，也應該繼續還原程序。 針對 SSISDB 目錄，因為尚未在您要還原資料庫的目標執行個體上開啟資料庫主要金鑰，因此您會看到此訊息。  
  
    -   **方法 2**  
  
         如果您有建立 SSISDB 所使用的原始密碼，請使用此方法。  
  
        ```  
        open master key decryption by password = 'LS1Setup!' --'Password used when creating SSISDB'  
               Alter Master Key Add encryption by Service Master Key  
        ```  
  
8.  執行 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] catalog.check_schema_version [，以判斷 SSISDB 目錄結構描述與](../../integration-services/system-stored-procedures/catalog-check-schema-version.md)二進位檔 (ISServerExec 和 SQLCLR 組件) 是否相容。  
  
9. 若要確認已成功還原 SSISDB 資料庫，請針對 SSISDB 目錄執行作業，例如執行已經部署到 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 伺服器的封裝。 如需詳細資訊，請參閱[執行 Integration Services (SSIS) 套件](../../integration-services/packages/run-integration-services-ssis-packages.md)。  
  
### <a name="to-move-the-ssis-database"></a>若要移動 SSIS 資料庫  
  
-   遵循移動使用者資料庫的指示進行。 如需詳細資訊，請參閱 [移動使用者資料庫](../../relational-databases/databases/move-user-databases.md)。  
  
     確定您有備份 SSISDB 資料庫的主要金鑰並保護備份檔案。 如需詳細資訊，請參閱 [備份 SSIS 資料庫](#backup)。  
  
     確定 Integration Services (SSIS) 相關物件建立在尚未建立 SSISDB 目錄的新 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體中。  

## <a name="upgrade-the-ssis-catalog-ssisdb"></a>升級 SSIS 目錄 (SSISDB)
  當資料庫版本比目前的 SQL Server 執行個體版本還舊時，執行 SSISDB 升級精靈來升級 SSIS 目錄資料庫 (SSISDB)。 當下列其中一個條件成立時，資料庫可能為舊版。  
  
-   您已從舊版 SQL Server 還原資料庫。  
  
-   您在升級 SQL Server 執行個體之前，並未從 AlwaysOn 可用性群組移除資料庫。 此條件可防止資料庫自動升級。 如需詳細資訊，請參閱＜ [Upgrading SSISDB in an availability group](#Upgrade)＞。  
  
 此精靈只能升級本機伺服器執行個體上的資料庫。  
  
### <a name="upgrade-the-ssis-catalog-ssisdb-by-running-the-ssisdb-upgrade-wizard"></a>執行 SSISDB 升級精靈升級 SSIS 目錄 (SSISDB)  
  
1.  備份 SSIS 目錄資料庫 (SSISDB)。  
  
2.  在 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)]中，展開本機伺服器，然後展開 [Integration Services 目錄] 。  
  
3.  以滑鼠右鍵按一下 [SSISDB]，然後選取 [資料庫升級] 啟動 [SSISDB 升級精靈]。 或者，在本機伺服器上以較高的權限執行 `C:\Program Files\Microsoft SQL Server\140\DTS\Binn\ISDBUpgradeWizard.exe`，以啟動 [SSISDB 升級精靈]。
  
     ![啟動 SSISDB 升級精靈](../../integration-services/service/media/ssisdb-upgrade-wizard-1.png)

4.  在 [選取執行個體]  頁面上，選取本機伺服器上的 SQL Server 執行個體。  
  
    > [!IMPORTANT]  
    >  此精靈只能升級本機伺服器執行個體上的資料庫。  
  
     選取此核取方塊，表示您已經在執行這個精靈之前備份 SSISDB 資料庫。  
  
     ![在 [SSISDB 升級精靈] 中選取伺服器](../../integration-services/service/media/ssisdb-upgrade-wizard-2.png "在 [SSISDB 升級精靈] 中選取伺服器")  
  
5.  選取 [升級]  升級 SSIS 目錄資料庫。  
  
6.  在 [結果]  頁面上，檢閱結果。  
  
     ![在 [SSISDB 升級精靈] 中檢閱結果](../../integration-services/service/media/ssisdb-upgrade-wizard-3.png "在 [SSISDB 升級精靈] 中檢閱結果")  

## <a name="always-on-for-ssis-catalog-ssisdb"></a>適用於 SSIS 目錄 (SSISDB) 的 Always On
  AlwaysOn 可用性群組功能是提供資料庫鏡像之企業級替代方案的高可用性與災害復原解決方案。 可用性群組支援一組可一起容錯移轉之離散化使用者資料庫的容錯移轉環境，也就是所謂的可用性資料庫。 如需詳細資訊，請參閱 [AlwaysOn 可用性群組 (SQL Server)](../../database-engine/availability-groups/windows/always-on-availability-groups-sql-server.md)。  
  
 為了提供 SSIS 目錄 (SSISDB) 及其內容 (專案、封裝、執行記錄等) 的高可用性，您可以將 SSISDB 資料庫 (就像任何其他使用者資料庫) 加入 AlwaysOn 可用性群組。 發生容錯移轉時，其中一個次要節點會自動變成新的主要節點。  
 
 > [!IMPORTANT]
 > 在容錯移轉時，執行中的套件不會重新啟動或繼續。 
 
 **本節內容：**  
  
1.  [先決條件](#prereq)  
  
2.  [設定適用於 AlwaysOn 的 SSIS 支援](#Firsttime)  
  
3.  [在可用性群組中升級 SSISDB](#Upgrade)  
  
###  <a name="prerequisites"></a><a name="prereq"></a> 必要條件  
針對 SSISDB 資料庫啟用 Always On 支援之前，請先執行下列必要條件步驟。  
  
1.  設定 Windows 容錯移轉叢集。 如需相關指示，請參閱 [安裝適用於 Windows Server 2012 的容錯移轉叢集功能和工具](https://techcommunity.microsoft.com/t5/failover-clustering/installing-the-failover-cluster-feature-and-tools-in-windows/ba-p/371733) 部落格文章。 在所有叢集節點上安裝功能和工具。  
  
2.  在叢集的每個節點上，安裝含有 Integration Services (SSIS) 功能的 SQL Server 2016。  
  
3.  啟用每個 SQL Server 執行個體的 Always On 可用性群組。 如需詳細資訊，請參閱 [啟用和停用 AlwaysOn 可用性群組 (SQL Server)](../../database-engine/availability-groups/windows/enable-and-disable-always-on-availability-groups-sql-server.md) 。  
  
###  <a name="configure-ssis-support-for-always-on"></a><a name="Firsttime"></a> 設定適用於 AlwaysOn 的 SSIS 支援  
  
-   [步驟 1：建立 Integration Services 目錄](#Step1)  
  
-   [步驟 2：將 SSISDB 新增至 Always On 可用性群組](#Step2)  
  
-   [步驟 3：啟用適用於 Always On 的 SSIS 支援](#Step3)  
  
> [!IMPORTANT]  
> -   您必須在可用性群組的 **主要節點** 上執行這些步驟。
> -   將 SSISDB 新增至 Always On 群組之後，您必須啟用 **適用於 Always On 的 SSIS 支援**。  

> [!NOTE]
> 如需此程序的詳細資訊，請參閱下列由 Data Platform MVP Marcos Freccia 提供的逐步解說與其他螢幕擷取畫面：[Adding SSISDB to AG for SQL Server 2016](https://marcosfreccia.com/2017/04/28/adding-ssisdb-to-ag-for-sql-server-2016/) (將 SSISDB 新增至 SQL Server 2016 的 AG)。

####  <a name="step-1-create-integration-services-catalog"></a><a name="Step1"></a> 步驟 1：建立 Integration Services 目錄  
  
1.  啟動 **SQL Server Management Studio** 並連接到您想要在叢集中設定為適用於 SSISDB 的 AlwaysOn 高可用性群組 **主要節點** 的 SQL Server 執行個體。  
  
2.  在物件總管中，展開伺服器節點，以滑鼠右鍵按一下 [Integration Services 目錄]  節點，然後按一下 [建立目錄] 。  
  
3.  按一下 **[啟用 CLR 整合]** 。 目錄便會使用 CLR 預存程序。  
  
4.  按一下 [在 SQL Server 啟動時允許自動執行 Integration Services 預存程序]  ，讓 [catalog.startup](../system-stored-procedures/catalog-startup.md) 預存程序會在每次 SSIS 伺服器執行個體重新啟動時執行。 預存程序會執行 SSISDB 目錄之作業狀態的維護。 它會在 SSIS 伺服器執行個體效能降低時，修正任何正在執行之封裝的狀態。  
  
5.  輸入 **密碼**，然後按一下 [確定] 。 此密碼保護用來加密目錄資料的資料庫主要金鑰。 請將密碼儲存在安全位置。 建議您同時備份資料庫主要金鑰。 如需相關資訊，請參閱 [Back Up a Database Master Key](../../relational-databases/security/encryption/back-up-a-database-master-key.md)。  
  
####  <a name="step-2-add-ssisdb-to-an-always-on-availability-group"></a><a name="Step2"></a> 步驟 2：將 SSISDB 新增至 Always On 可用性群組  
將 SSISDB 資料庫加入 AlwaysOn 可用性群組，幾乎等於是將任何其他使用者資料庫加入可用性群組。 請參閱 [使用可用性群組精靈](../../database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio.md)。  
  
提供您在 [新增可用性群組] 精靈的 [選取資料庫]  頁面中建立 SSIS 目錄時指定的密碼。

![新增可用性群組](../../integration-services/service/media/ssis-newavailabilitygroup.png "新增可用性群組")  
  
####  <a name="step-3-enable-ssis-support-for-always-on"></a><a name="Step3"></a> 步驟 3：啟用適用於 Always On 的 SSIS 支援  
 建立 Integration Services 目錄之後，以滑鼠右鍵按一下 [Integration Services 目錄] 節點，然後按一下 [啟用 Always On 支援]。 您應該會看到下列 [啟用 AlwaysOn 支援]  對話方塊。 如果這個功能表項目已停用，請確認您已安裝的所有必要條件，然後按一下 [重新整理] 。  
  
 ![啟用 Always On 支援](../../integration-services/service/media/ssis-enablesupportforalwayson.png)  
  
> [!WARNING]  
>  在您啟用適用於 AlwaysOn 的 SSIS 支援之前，不支援自動容錯移轉 SSISDB 資料庫。  
  
 表格會顯示剛從 Always On 可用性群組新增的次要複本。 針對清單中的每個複本按一下 [連接...] 按鈕，然後輸入驗證認證以連接到複本。 使用者帳戶必須是每個複本上的系統管理員群組成員，才能啟用 SSIS 的 Always On 支援。 當您成功連接到每個複本之後，按一下 [確定]  以啟用適用於 AlwaysOn 的 SSIS 支援。  
 
在您完成其他必要條件之後，如果操作功能表上的 [啟用 Always On 支援] 選項顯示為停用，請嘗試下列方法：
1.  按一下 [重新整理] 選項，以重新整理操作功能表。
2.  請確定您已連線到主要節點。 您必須啟用主要節點上的 Always On 支援。
3.  請確定 SQL Server 版本為 13.0 或更新版本。 只有在 SQL Server 2016 和更新版本上，SSIS 才會支援 Always On。

###  <a name="upgrading-ssisdb-in-an-availability-group"></a><a name="Upgrade"></a> 在可用性群組中升級 SSISDB  
 如果您要從先前的版本升級 SQL Server，而且 SSISDB 在 AlwaysOn 可用性群組中，則您的升級可能會遭到「SSISDB 在 AlwaysOn 可用性群組中檢查」規則所封鎖。 因為升級是在單一使用者模式中執行，而可用性資料庫必須是多使用者資料庫，就會發生此封鎖。 因此，在升級或修補期間，所有的可用性資料庫 (包括 SSISDB) 都要離線，而且不會進行升級或修補。 若要讓升級繼續，請先從可用性群組移除 SSISDB，再升級或修補每個節點，然後將 SSISDB 新增回可用性群組。  
  
 如果您被「AlwaysOn 可用性群組中的 SSISDB 檢查規則」封鎖，就必須遵循這些步驟來升級 SQL Server。  
  
1.  從可用性群組中移除 SSISDB 資料庫。 如需詳細資訊，請參閱[將次要資料庫從可用性群組移除 &#40;SQL Server&#41;](../../database-engine/availability-groups/windows/remove-a-secondary-database-from-an-availability-group-sql-server.md) 和[將主要資料庫從可用性群組移除 &#40;SQL Server&#41;](../../database-engine/availability-groups/windows/remove-a-primary-database-from-an-availability-group-sql-server.md)。  
  
2.  按一下升級精靈中的 [重新執行]。 即可通過「Always On 可用性群組中的 SSISDB 檢查」規則。  
  
3.  按 [下一步]  繼續升級。  
  
4.  當您已升級所有節點之後，請將 SSISDB 資料庫加回 AlwaysOn 可用性群組。 如需詳細資訊，請參閱[將資料庫加入至可用性群組 &#40;SQL Server&#41;](../../database-engine/availability-groups/windows/availability-group-add-a-database.md)。  
  
 如果您在升級 SQL Server 時未遭到封鎖，且 SSISDB 在 Always On 可用性群組中，請在升級 SQL Server 資料庫引擎之後個別升級 SSISDB。 使用 SSIS 升級精靈來升級 SSISDB，如下列程序所述。  
  
1.  將 SSISDB 資料庫移出可用性群組，或者如果 SSISDB 是可用性群組中唯一的資料庫，請刪除可用性群組。 若要執行這項工作，請在可用性群組的 **主要節點** 上啟動 [SQL Server Management Studio]。  
  
2.  從所有 **複本節點** 移除 SSISDB 資料庫。  
  
3.  在 **主要節點** 上升級 SSISDB 資料庫。 在 SQL Server Management Studio 的物件總管 中，展開 [Integration Services 目錄] 、以滑鼠右鍵按一下 [SSISDB] ，然後選取 [資料庫升級] 。 遵循 **SSISDB 升級精靈** 中的指示來升級資料庫。 在 **主要節點** 本機上啟動 [SSIDB 升級精靈]。  
  
4.  遵循[步驟 2：將 SSISDB 新增至 Always On 可用性群組](#Step2)中的指示，將 SSISDB 新增回可用性群組。  
  
5.  遵循[步驟 3：啟用適用於 Always On 的 SSIS 支援](#Step3)中的指示進行。  


## <a name="ssisdb-catalog-and-delegation-in-double-hop-scenarios"></a>雙躍點案例中的 SSISDB 目錄和委派

根據預設，儲存在 SSISDB 目錄下的 SSIS 套件遠端引動過程不支援認證的委派，即俗稱的雙躍點。 

假設有一個案例，使用者登入用戶端電腦 A 並啟動 SQL Server Management Studio (SSMS)。 在 SSMS 中，使用者會連線到裝載於電腦 B 上具有 SSISDB 目錄的 SQL 伺服器。 SSIS 套件會儲存在此 SSISDB 目錄底下，接著連線到在電腦 C 上執行的 SQL Server 服務 (此套件也可能正在存取任何其他服務)。 當使用者叫用在電腦 A 執行的 SSIS 套件時，SSMS 會先成功將使用者認證從電腦 A 傳遞至電腦 B (此時 SSIS 執行階段處理序正在執行套件)。 現在需要有 SSIS 執行的執行階段處理序 (ISServerExec.exe)，才能將使用者認證從電腦 B 委派給電腦 C，以順利完成執行。 不過，預設不會啟用認證的委派。

將「信任這個使用者可委派任何服務 (只限 Kerberos)」權限授與 SQL Server 服務帳戶 (電腦 B)，使用者即可啟用認證委派，其會將 ISServerExec.exe 啟動為子處理序。 此程序稱為設定非限制委派或 SQL Server 服務帳戶的開放委派。 在授與此權限之前，請考慮它是否符合您組織的安全性需求。

SSISDB 不支援限制委派。 在雙躍點環境中，如果裝載 SSISDB 目錄的 SQL 伺服器服務帳戶 (範例中為電腦 B) 已設定限制委派，ISServerExec.exe 即無法將認證委派給第三部電腦 (電腦 C)。 這適用當於 Windows Credential Guard 已啟用，而其強制要求設定限制委派時。

  
##  <a name="related-content"></a><a name="RelatedContent"></a> 相關內容  
  
-   blogs.msdn.com 上的部落格文章： [SQL Server 2012 中的 SSIS 和 PowerShell](https://techcommunity.microsoft.com/t5/sql-server-integration-services/ssis-and-powershell-in-sql-server-2012/ba-p/388015)。  
  
-   blogs.msdn.com 上的部落格文章： [SSIS 目錄存取控制提示](https://techcommunity.microsoft.com/t5/sql-server-integration-services/ssis-catalog-access-control-tips/ba-p/388057)。  
  
-   blogs.msdn.com 上的部落格文章 [SSIS 目錄管理物件模型初探](https://techcommunity.microsoft.com/t5/sql-server-integration-services/a-glimpse-of-the-ssis-catalog-managed-object-model/ba-p/387892)。