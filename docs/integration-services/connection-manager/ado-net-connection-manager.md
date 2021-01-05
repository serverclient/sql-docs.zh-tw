---
title: ADO.NET 連線管理員 | Microsoft Docs
description: ADO.NET 連線管理員可讓套件使用 .NET 提供者來存取資料來源。
ms.custom: ''
ms.date: 05/24/2019
ms.prod: sql
ms.prod_service: integration-services
ms.reviewer: ''
ms.technology: integration-services
ms.topic: conceptual
f1_keywords:
- sql13.dts.designer.adonetconnection.f1
helpviewer_keywords:
- connection managers [Integration Services], ADO.NET
- ADO.NET connection manager [Integration Services]
- connections [Integration Services], ADO.NET
ms.assetid: fc5daa2f-0159-4bda-9402-c87f1035a96f
author: chugugrace
ms.author: chugu
ms.openlocfilehash: 8c2efe0145643f3f9e969c298e76115967477019
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97637482"
---
# <a name="adonet-connection-manager"></a>ADO.NET 連接管理員

[!INCLUDE[sqlserver-ssis](../../includes/applies-to-version/sqlserver-ssis.md)]


[!INCLUDE[vstecado](../../includes/vstecado-md.md)] 連接管理員可讓封裝使用 .NET 提供者來存取資料來源。 通常，您會使用此連線管理員來存取資料來源，例如 [!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]。 您也可以存取透過自訂工作 (使用如 C# 這類語言以受控程式碼撰寫) 中之 OLE DB 和 XML 公開的資料來源。  
  
當您將 [!INCLUDE[vstecado](../../includes/vstecado-md.md)] 連線管理員加入套件時，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 會建立在執行階段解析為 [!INCLUDE[vstecado](../../includes/vstecado-md.md)] 連線的連線管理員。 它會設定連線管理員屬性，並將連線管理員加入套件上的 **Connections** 集合。  
  
連接管理員的 `ConnectionManagerType` 屬性會設為 `ADO.NET`。 系統會限定 `ConnectionManagerType` 的值，以包含連接管理員使用之 .NET 提供者的名稱。  
  
## <a name="adonet-connection-manager-troubleshooting"></a>對 ADO.NET 連線管理員進行疑難排解  
您可以記錄 [!INCLUDE[vstecado](../../includes/vstecado-md.md)] 連接管理員對外部資料提供者執行的呼叫。 然後，您可以對 [!INCLUDE[vstecado](../../includes/vstecado-md.md)] 連線管理員的外部資料來源連線進行疑難排解。 若要記錄 [!INCLUDE[vstecado](../../includes/vstecado-md.md)] 連線管理員對外部資料提供者執行的呼叫，請啟用套件記錄，然後在套件層級選取 [診斷]  事件。 如需詳細資訊，請參閱 [封裝執行的疑難排解工具](../../integration-services/troubleshooting/troubleshooting-tools-for-package-execution.md)。  
  
由 [!INCLUDE[vstecado](../../includes/vstecado-md.md)] 連線管理員讀取時，某些 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 日期資料類型的資料會產生如下表所示的結果。  
  
|SQL Server 資料類型|結果|  
|--------------------------|------------|  
|**time**, **datetimeoffset**|除非封裝使用參數化 SQL 命令，否則封裝會失敗。 若要使用參數化 SQL 命令，請在封裝中使用「執行 SQL 工作」。 如需詳細資訊，請參閱 [執行 SQL 工作](../../integration-services/control-flow/execute-sql-task.md) 和 [執行 SQL 工作中的參數和傳回碼](../control-flow/execute-sql-task.md)。|  
|**datetime2**|[!INCLUDE[vstecado](../../includes/vstecado-md.md)] 連接管理員會截斷毫秒值。|  
  
> [!NOTE]  
>  如需 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 資料類型以及如何將其對應到 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 資料類型的詳細資訊，請參閱[資料類型 &#40;Transact-SQL&#41;](../../t-sql/data-types/data-types-transact-sql.md) 和 [Integration Services 資料類型](../../integration-services/data-flow/integration-services-data-types.md)。  
  
## <a name="adonet-connection-manager-configuration"></a>ADO.NET 連線管理員設定  
  
您可以透過「[!INCLUDE[ssIS](../../includes/ssis-md.md)] 設計工具」或以程式設計方式設定屬性。  
  
-   提供設定的特定連接字串，以符合所選 .NET 提供者的需求。  
  
-   視提供者而定，包含要連接的資料來源名稱。  
  
-   為所選的提供者提供適當的安全性認證。  
  
-   指示是否在執行階段保留從連線管理員建立的連線。  
  
[!INCLUDE[vstecado](../../includes/vstecado-md.md)] 連線管理員的許多組態選項依存於連線管理員使用的 .NET 提供者。  
  
如需您可以在 [!INCLUDE[ssIS](../../includes/ssis-md.md)] 設計工具中設定之屬性的詳細資訊，請參閱[設定 ADO.NET 連線管理員]()。  
  
 如需以程式設計方式設定連線管理員的資訊，請參閱 <xref:Microsoft.SqlServer.Dts.Runtime.ConnectionManager> 和 [以程式設計方式加入連接](../../integration-services/building-packages-programmatically/adding-connections-programmatically.md)。  
  
### <a name="configure-adonet-connection-manager"></a>設定 ADO.NET 連線管理員
使用 [設定 ADO.NET 連線管理員]  對話方塊加入資料來源的連線，可使用 .NET Framework 資料提供者來存取此資料來源。 例如，其中一個提供者是 SqlClient 提供者。 連接管理員可以使用現有的連接，或者您也可以建立新的連接。  
  
 若要深入了解 ADO.NET 連線管理員，請參閱 [ADO.NET 連線管理員](../../integration-services/connection-manager/ado-net-connection-manager.md)。  
  
#### <a name="options"></a>選項。  
**資料連接**  
從清單中選取現有的 ADO.NET 資料連接。  
  
**資料連接屬性**  
檢視選取之 ADO.NET 資料連接的屬性和值。  
  
**新增**  
使用 [連線管理員]  對話方塊來建立 ADO.NET 資料連接。  
  
**刪除**  
選取一個連線，然後選取 [刪除]  來刪除它。  
  
#### <a name="managed-identities-for-azure-resources-authentication"></a>Azure 資源驗證的受控識別
在 [Azure Data Factory 中的 Azure-SSIS 整合執行階段](/azure/data-factory/concepts-integration-runtime#azure-ssis-integration-runtime) \(部分機器翻譯\) 上執行 SSIS 套件時，您可以使用與您資料處理站建立關聯的[受控識別](/azure/data-factory/connector-azure-sql-database#managed-identity) \(部分機器翻譯\) 來進行 Azure SQL Database 或 Azure SQL 受控執行個體驗證。 指定的處理站可以使用此身分識別從您資料庫存取資料，或從您的資料庫複製資料。

> [!NOTE]
>  當您使用 Azure Active Directory (Azure AD) 驗證 (包括受控識別驗證) 連線到 Azure SQL Database 或 Azure SQL 受控執行個體時，可能會遇到有關套件執行失敗或非預期行為變更的問題。 如需詳細資訊，請參閱 [Azure AD 功能和限制](/azure/sql-database/sql-database-aad-authentication#azure-ad-features-and-limitations) \(部分機器翻譯\)。

若要使用 Azure SQL Database 的受控識別驗證，請遵循下列步驟來設定您的資料庫：

1. 在 Azure 入口網站為您的 Azure SQL Server [佈建 Azure Active Directory 系統管理員](/azure/sql-database/sql-database-aad-authentication-configure#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server) \(部分機器翻譯\)，如果您尚未這麼做。 Azure AD 系統管理員可以是 Azure AD 使用者或 Azure AD 群組。 如果您將系統管理員角色授與受控識別群組，請略過步驟 2 與 3。 系統管理員將擁有資料庫的完整存取權。

1. 為 Data Factory 受控識別[建立自主資料庫使用者](/azure/sql-database/sql-database-aad-authentication-configure#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) \(部分機器翻譯\)。 使用至少具有 ALTER ANY USER 權限的 Azure AD 身分識別，使用 SSMS 等工具連線至您要複製資料的資料庫。 執行下列 T-SQL： 
    
    ```sql
    CREATE USER [your data factory name] FROM EXTERNAL PROVIDER;
    ```

1. 依照您平常為 SQL 使用者和其他人所進行的操作一樣，授與 Data Factory 受控識別所需的權限。 如需適當的角色，請參閱[資料庫層級角色](../../relational-databases/security/authentication-access/database-level-roles.md)。 執行下列程式碼。 如需更多選項，請參閱[此文件](../../relational-databases/system-stored-procedures/sp-addrolemember-transact-sql.md)。

    ```sql
    EXEC sp_addrolemember [role name], [your data factory name];
    ```

若要為 Azure SQL 受控執行個體使用受控識別驗證，請遵循這些步驟來設定您的資料庫：
    
1. 在 Azure 入口網站為您的受控執行個體[佈建 Azure Active Directory 系統管理員](/azure/sql-database/sql-database-aad-authentication-configure#provision-an-azure-active-directory-administrator-for-your-managed-instance) \(部分機器翻譯\)，如果您尚未這麼做。 Azure AD 系統管理員可以是 Azure AD 使用者或 Azure AD 群組。 如果您將系統管理員角色授與受控識別群組，請略過步驟 2-4。 系統管理員將擁有資料庫的完整存取權。

1. 為 Data Factory 受控識別[建立登入](../../t-sql/statements/create-login-transact-sql.md?view=azuresqldb-mi-current&preserve-view=true)。 在 SQL Server Management Studio (SSMS) 中，以 **系統管理員** 身分的 SQL Server 帳戶連線到您的受控執行個體。 在 **master** 資料庫中執行下列 T-SQL：

    ```sql
    CREATE LOGIN [your data factory name] FROM EXTERNAL PROVIDER;
    ```

1. 為 Data Factory 受控識別[建立自主資料庫使用者](/azure/sql-database/sql-database-aad-authentication-configure#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) \(部分機器翻譯\)。 連線到您想要從中複製資料的資料庫，然後執行下列 T-SQL： 
  
    ```sql
    CREATE USER [your data factory name] FROM EXTERNAL PROVIDER;
    ```

1. 依照您平常為 SQL 使用者和其他人所進行的操作一樣，授與 Data Factory 受控識別所需的權限。 執行下列程式碼。 如需更多選項，請參閱[此文件](../../t-sql/statements/alter-role-transact-sql.md?view=azuresqldb-mi-current&preserve-view=true)。

    ```sql
    ALTER ROLE [role name e.g., db_owner] ADD MEMBER [your data factory name];
    ```

最後，為 ADO.NET 連線管理員設定受控識別驗證。 有兩個選項可以執行此操作：
    
- **在設計階段設定。** 在 [SSIS 設計工具] 中，以滑鼠右鍵按一下 [ADO.NET 連線管理員]，然後選取 [屬性]  。 將 `ConnectUsingManagedIdentity` 屬性更新為 `True`。
    > [!NOTE]
    >  目前，當您在 SSIS 設計工具中或 [!INCLUDE[msCoName](../../includes/msconame-md.md)] SQL Server 中執行 SSIS 套件時，連線管理員的 `ConnectUsingManagedIdentity` 屬性不會生效 (表示受控識別驗證無法運作)。
    
- **在執行階段設定。** 當您透過 [SQL Server Management Studio (SSMS)](../ssis-quickstart-run-ssms.md) 或 [Azure Data Factory 執行 SSIS 套件活動](/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) \(部分機器翻譯\) 來執行套件時，請尋找 ADO.NET 連線管理員。 將其屬性 `ConnectUsingManagedIdentity` 更新為 `True`。
    > [!NOTE]
    >  在 Azure-SSIS 整合執行階段中，當受控識別驗證用於建立資料庫連線時，會覆寫 ADO.NET 連線管理員上預先設定的所有其他驗證方法 (例如，整合式驗證、密碼)。

> [!NOTE]
>  若要在現有套件上設定受控識別驗證，建議您使用[最新的 SSIS 設計工具](../../ssdt/download-sql-server-data-tools-ssdt.md)，重建您的 SSIS 專案至少一次。 將該 SSIS 專案重新部署到您的 Azure-SSIS 整合執行階段，以便將新的連線管理員屬性 `ConnectUsingManagedIdentity` 自動加入至 SSIS 專案中的所有 ADO.NET 連線管理員。 替代方式是在執行階段直接搭配屬性路徑 **\Package.Connections[{您的連線管理員名稱}].Properties[ConnectUsingManagedIdentity]** 使用屬性覆寫。

## <a name="see-also"></a>另請參閱  
 [Integration Services &#40;SSIS&#41; 連接](../../integration-services/connection-manager/integration-services-ssis-connections.md)  
  
