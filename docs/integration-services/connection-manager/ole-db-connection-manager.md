---
title: OLE DB 連線管理員 | Microsoft Docs
description: OLE DB 連接管理員可透過使用 OLE DB 提供者讓封裝連接到資料來源。
ms.custom: ''
ms.date: 05/24/2019
ms.prod: sql
ms.prod_service: integration-services
ms.reviewer: ''
ms.technology: integration-services
ms.topic: conceptual
f1_keywords:
- sql13.dts.designer.oledbconnection.f1
helpviewer_keywords:
- OLE DB connection manager
- data sources [Integration Services], connections
- connection managers [Integration Services], OLE DB
- connections [Integration Services], OLE DB
ms.assetid: 91e3622e-4b1a-439a-80c7-a00b90d66979
author: chugugrace
ms.author: chugu
ms.openlocfilehash: 7c50a2e201bcba99299998dc232e1246005eb168
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97639213"
---
# <a name="ole-db-connection-manager"></a>OLE DB 連接管理員

[!INCLUDE[sqlserver-ssis](../../includes/applies-to-version/sqlserver-ssis.md)]


OLE DB 連接管理員可透過使用 OLE DB 提供者讓封裝連接到資料來源。 例如，與 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 連接的 OLE DB 連線管理員可以使用 [!INCLUDE[msCoName](../../includes/msconame-md.md)] OLE DB Provider for [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]。    
    
> [!NOTE]
>  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client 11.0 OLEDB 提供者不支援多重子網路容錯移轉叢集的新連接字串關鍵字 (MultiSubnetFailover=True)。 如需詳細資訊，請參閱 [SQL Server 版本資訊](https://go.microsoft.com/fwlink/?LinkId=247824) \(英文\)。    
> 
> [!NOTE]
>  如果資料來源為 [!INCLUDE[msCoName](../../includes/msconame-md.md)] Office Excel 2007 或 [!INCLUDE[msCoName](../../includes/msconame-md.md)] Office Access 2007，則資料來源需要舊版 Excel 或 Access 以外的資料提供者。 如需詳細資訊，請參閱 [連接至 Excel 活頁簿](../load-data-to-from-excel-with-ssis.md) 和 [連接至 Access 資料庫](./integration-services-ssis-connections.md)。    
    
有數個 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 工作與資料流程元件使用 OLE DB 連線管理員。 例如，OLE DB 來源和 OLE DB 目的地使用此連線管理員來將資料解壓縮和載入資料。 「執行 SQL」工作可以使用此連線管理員，來連線到 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 資料庫以執行查詢。    
    
您也可以使用 OLE DB 連線管理員來存取非受控程式碼 (使用如 C++ 等語言) 撰寫之自訂工作中的 OLE DB 資料來源。    
    
當您將 OLE DB 連線管理員加入套件時，[!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 會建立在執行階段解析為 OLE DB 連線的連線管理員、設定連線管理員屬性，並將連線管理員加入套件上的 **Connections** 集合。    
    
連接管理員的 `ConnectionManagerType` 屬性會設為 `OLEDB`。    
    
以下列方式設定 OLE DB 連線管理員：    
    
-   提供設定的特定連接字串，以符合所選取提供者的需求。    
    
-   視提供者而定，包含要連接的資料來源名稱。    
    
-   為所選的提供者提供適當的安全性認證。    
    
-   指示是否在執行階段保留從連線管理員建立的連線。    
    
## <a name="log-calls-and-troubleshoot-connections"></a>記錄呼叫和對連線進行疑難排解    
 您可以記錄 OLE DB 連接管理員對外部資料提供者執行的呼叫。 您可以對 OLE DB 連線管理員的外部資料來源連線進行疑難排解。 若要記錄 OLE DB 連線管理員對外部資料提供者執行的呼叫，請啟用套件記錄，然後在套件層級選取 [診斷]  事件。 如需詳細資訊，請參閱 [封裝執行的疑難排解工具](../../integration-services/troubleshooting/troubleshooting-tools-for-package-execution.md)。    
    
## <a name="configure-the-ole-db-connection-manager"></a>設定 OLE DB 連線管理員    
 您可以透過「[!INCLUDE[ssIS](../../includes/ssis-md.md)] 設計工具」或以程式設計方式設定屬性。 如需可在 [!INCLUDE[ssIS](../../includes/ssis-md.md)] 設計師中設定之屬性的詳細資訊，請參閱 [設定 OLE DB 連線管理員]()。 如需以程式設計方式設定連線管理員的相關資訊，請參閱《開發人員指南》中 **T:Microsoft.SqlServer.Dts.Runtime.ConnectionManager** 類別的文件集。    
    
### <a name="configure-ole-db-connection-manager"></a>設定 OLE DB 連線管理員

使用 [設定 OLE DB 連線管理員]  對話方塊，將連線加入資料來源。 此連線可以是新的或現有連線的複本。  
  
> [!NOTE]  
>  如果資料來源為 [!INCLUDE[msCoName](../../includes/msconame-md.md)] Office Excel 2007，則資料來源需要舊版 Excel 以外的連接管理員。 如需詳細資訊，請參閱 [連接至 Excel 活頁簿](../load-data-to-from-excel-with-ssis.md)。  
>   
>  如果資料來源為 [!INCLUDE[msCoName](../../includes/msconame-md.md)] Office Access 2007，則資料來源需要舊版 Access 以外的 OLE DB 提供者。 如需詳細資訊，請參閱 [連接至 Access 資料庫](./integration-services-ssis-connections.md)。  
  
 若要深入了解 OLE DB 連接管理員，請參閱＜ [OLE DB Connection Manager](../../integration-services/connection-manager/ole-db-connection-manager.md)＞。  
  
#### <a name="options"></a>選項。  
 **資料連接**  
 從清單中選取現有的 OLE DB 資料連接。  
  
 **資料連接屬性**  
 檢視選取之 OLE DB 資料連接的屬性和值。  
  
 **新增**  
 使用 [連線管理員]  對話方塊來建立 OLE DB 資料連線。  
  
 **刪除**  
 選取一個資料連線，然後選取 [刪除]  來刪除它。  
  
#### <a name="managed-identities-for-azure-resources-authentication"></a>Azure 資源驗證的受控識別
在 [Azure Data Factory 中的 Azure-SSIS 整合執行階段](/azure/data-factory/concepts-integration-runtime#azure-ssis-integration-runtime) \(部分機器翻譯\) 上執行 SSIS 套件時，請使用與您資料處理站建立關聯的[受控識別](/azure/data-factory/connector-azure-sql-database#managed-identity) \(部分機器翻譯\) 來進行 Azure SQL Database 或受控執行個體驗證。 指定的處理站可以使用此身分識別從您資料庫存取資料，或從您的資料庫複製資料。

> [!NOTE]
>  當您使用 Azure Active Directory (Azure AD) 驗證 (包括受控識別驗證) 連線到 Azure SQL Database 或受控執行個體時，可能會遇到有關套件執行失敗或非預期行為變更的問題。 如需詳細資訊，請參閱 [Azure AD 功能和限制](/azure/sql-database/sql-database-aad-authentication#azure-ad-features-and-limitations) \(部分機器翻譯\)。

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

然後為 OLE DB 連線管理員設定 OLE DB 提供者。 有兩個選項可以執行此操作：
    
- **在設計階段設定。** 在 SSIS 設計工具中按兩下 [OLE DB 連線管理員]，開啟 [連線管理員]  視窗。 在 [提供者] 下拉式清單中選取 [Microsoft OLE DB Driver for SQL Server](https://go.microsoft.com/fwlink/?linkid=871294)。
    > [!NOTE]
    >  下拉式清單中的其他提供者可能不支援受控識別驗證。
    
- **在執行階段設定。** 當您透過 [SQL Server Management Studio (SSMS)](../ssis-quickstart-run-ssms.md) 或 [Azure Data Factory 執行 SSIS 套件活動](/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) \(部分機器翻譯\) 來執行套件時，請尋找 OLE DB 連線管理員的 **ConnectionString** 連線管理員屬性。 將連線屬性 `Provider` 更新為 `MSOLEDBSQL` (也就是 Microsoft OLE DB Driver for SQL Server)。
    ```vb
    Data Source=serverName;Initial Catalog=databaseName;Provider=MSOLEDBSQL;...
    ```

最後，為 OLE DB 連線管理員設定受控識別驗證。 有兩個選項可以執行此操作：
    
- **在設計階段設定。** 在 [SSIS 設計工具] 中，以滑鼠右鍵按一下 [OLE DB 連線管理員]，然後選取 [屬性]  。 將 `ConnectUsingManagedIdentity` 屬性更新為 `True`。
    > [!NOTE]
    >  目前，當您在 SSIS 設計工具中或 [!INCLUDE[msCoName](../../includes/msconame-md.md)] SQL Server 中執行 SSIS 套件時，連線管理員的 `ConnectUsingManagedIdentity` 屬性不會生效 (表示受控識別驗證無法運作)。

- **在執行階段設定。** 當您透過 SSMS 或 **執行 SQL 套件** 活動執行套件時，尋找 OLE DB 連線管理員，並將其 `ConnectUsingManagedIdentity` 屬性更新為 `True`。
    > [!NOTE]
    >  在 Azure-SSIS 整合執行階段中，當受控識別驗證用於建立資料庫連線時，會覆寫 OLE DB 連線管理員上預先設定的所有其他驗證方法 (例如，整合式安全性和密碼)。

> [!NOTE]
>  若要在現有套件上設定受控識別驗證，建議您使用[最新的 SSIS 設計工具](../../ssdt/download-sql-server-data-tools-ssdt.md)，重建您的 SSIS 專案至少一次。 將該 SSIS 專案重新部署到您的 Azure-SSIS 整合執行階段，以便將新的連線管理員屬性 `ConnectUsingManagedIdentity` 自動加入至 SSIS 專案中的所有 OLE DB 連線管理員。 替代方式是在執行階段直接搭配屬性路徑 **\Package.Connections[{您的連線管理員名稱}].Properties[ConnectUsingManagedIdentity]** 使用屬性覆寫。

## <a name="see-also"></a>另請參閱    
 [OLE DB 來源](../../integration-services/data-flow/ole-db-source.md)     
 [OLE DB 目的地](../../integration-services/data-flow/ole-db-destination.md)     
 [執行 SQL 工作](../../integration-services/control-flow/execute-sql-task.md)     
 [Integration Services &#40;SSIS&#41; 連接](../../integration-services/connection-manager/integration-services-ssis-connections.md)    
    
