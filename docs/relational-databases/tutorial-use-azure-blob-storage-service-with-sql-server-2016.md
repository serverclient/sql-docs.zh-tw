---
description: 教學課程：搭配使用 Azure Blob 儲存體服務和 SQL Server 2016
title: 教學課程：搭配使用 Azure Blob 儲存體服務和 SQL Server 2016
ms.custom: seo-dt-2019
ms.date: 07/22/2020
ms.prod: sql
ms.technology: ''
ms.prod_service: database-engine
ms.reviewer: ''
ms.suite: sql
ms.tgt_pltfrm: ''
ms.topic: conceptual
applies_to:
- SQL Server 2016
ms.assetid: e69be67d-da1c-41ae-8c9a-6b12c8c2fb61
author: MashaMSFT
ms.author: mathoma
ms.openlocfilehash: 31407e6d056ce8ec847f52346b8aa3ddf8e621f3
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97644182"
---
# <a name="tutorial-use-azure-blob-storage-service-with-sql-server-2016"></a>教學課程：搭配使用 Azure Blob 儲存體服務和 SQL Server 2016

[!INCLUDE[sqlserver](../includes/applies-to-version/sqlserver.md)]
歡迎使用在 Microsoft Azure Blob 儲存體服務教學課程中使用 SQL Server 2016。 本教學課程可協助您了解如何將 Microsoft Azure Blob 儲存體服務用於 SQL Server 資料檔案和 SQL Server 備份。  
  
Microsoft Azure Blob 儲存體服務的 SQL Server 整合支援一開始是 SQL Server 2012 Service Pack 1 CU2 增強功能，並且已使用 SQL Server 2014 和 SQL Server 2016 進一步加強。 如需功能概觀以及使用此功能的優點，請參閱 [Microsoft Azure 中的 SQL Server 資料檔案](../relational-databases/databases/sql-server-data-files-in-microsoft-azure.md)。 如需即時的示範，請參閱 [File-Snapshot Backups Demo (檔案快照集備份示範)](https://channel9.msdn.com/Blogs/Windows-Azure/File-Snapshot-Backups-Demo)。  

本教學課程會在多個章節中示範如何在 Microsoft Azure Blob 儲存體服務中使用 SQL Server 資料檔案。 每個章節都著重在特定工作，並且應該依序完成章節。 首先，您將學習如何使用預存存取原則和共用存取簽章在 Blob 儲存體中建立新的容器。 然後，您將學習如何建立 SQL Server 認證，以便整合 SQL Server 與 Azure Blob 儲存體。 接下來，您會將資料庫備份至 Blob 儲存體，並將它還原至 Azure 虛擬機器。 您接著將使用 SQL Server 2016 檔案快照集交易記錄備份還原至某個時間點和新的資料庫。 最後，本教學課程將示範如何使用中繼資料系統預存程序和函數，協助您了解和使用檔案快照集備份。
  
## <a name="prerequisites"></a>必要條件

若要完成本教學課程，您必須熟悉 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 備份與還原概念以及 T-SQL 語法。 若要使用本教學課程，您需要 Azure 儲存體帳戶、SQL Server Management Studio (SSMS)、存取 SQL Server 內部部署執行個體的權限、存取執行 SQL Server 2016 Azure 虛擬機器 (VM) 的權限，以及 AdventureWorks2016 資料庫。 此外，用來發出 BACKUP 或 RESTORE 命令的帳戶，應該位於擁有 **ALTER ANY CREDENTIAL** 權限的 **db_backupoperator** 資料庫角色中。 

- 取得免費 [Azure 帳戶](https://azure.microsoft.com/offers/ms-azr-0044p/)。
- 建立 [Azure 儲存體帳戶](/azure/storage/common/storage-quickstart-create-account?tabs=portal)。
- 安裝 [SQL Server 2017 Developer Edition](https://www.microsoft.com/sql-server/sql-server-downloads)。
- 佈建[執行 SQL Server 的 Azure VM](https://azure.microsoft.com/services/virtual-machines/sql-server/)
- 安裝 [SQL Server Management Studio](../ssms/download-sql-server-management-studio-ssms.md)。
- 下載 [AdventureWorks2016 範例資料庫](../samples/adventureworks-install-configure.md) \(英文\)。
- 將使用者帳戶指派給 [db_backupoperator](./security/authentication-access/database-level-roles.md) 的角色，並授與 [ALTER ANY CREDENTIAL](../t-sql/statements/alter-credential-transact-sql.md) 權限。 

## <a name="1---create-stored-access-policy-and-shared-access-storage"></a>1 - 建立預存存取原則和共用存取儲存體

在本節中，您將使用 [Azure PowerShell](/powershell/azure/) 指令碼，在 Azure Blob 容器上建立使用預存存取原則的共用存取簽章。  
  
> [!NOTE]  
> 此指令碼是使用 Azure PowerShell 5.0.10586 所撰寫。  
  
共用存取簽章是一個 URI，會將有限的存取權限授與容器、Blob、佇列或資料表。 預存存取原則可對伺服器端的共用存取簽章提供一層額外的控制，包括撤銷、逾期或延伸存取權。 使用這項新的增強功能時，您需要在容器上建立具有最低讀取、寫入和列出權限的原則。  
  
您可以使用 Azure PowerShell、Azure 儲存體 SDK、Azure REST API 或協力廠商公用程式，建立預存存取原則和共用存取簽章。 本教學課程示範如何使用 Azure PowerShell 指令碼來完成這項工作。 此指令碼會使用資源管理員部署模型，並建立下列新的資源  
  
-   資源群組   
-   儲存體帳戶  
-   Azure Blob 容器   
-   SAS 原則    

此指令碼一開始會宣告一些變數，來指定上述資源的名稱，以及下列必要輸入值的名稱：  
  
-   用來命名其他資源物件的前置名稱    
-   訂閱名稱    
-   資料中心位置  

  
此指令碼最後會產生適當的 CREATE CREDENTIAL 陳述式，您將在 [2 - 使用共用存取簽章建立 SQL Server 認證](#2---create-a-sql-server-credential-using-a-shared-access-signature)中使用此陳述式。 系統會為您將此陳述式複製到剪貼簿，並輸出到主控台以供檢視。  
  
若要在容器上建立原則並產生共用存取簽章 (SAS) 金鑰，請遵循下列步驟執行：  
  
1.  開啟 Window PowerShell 或 Windows PowerShell ISE (請參閱上述的版本需求)。  
  
2.  編輯並執行下面的指令碼：  
  
    ```powershell
    # Define global variables for the script  
    $prefixName = '<a prefix name>'  # used as the prefix for the name for various objects  
    $subscriptionID = '<your subscription ID>'   # the ID  of subscription name you will use  
    $locationName = '<a data center location>'  # the data center region you will use  
    $storageAccountName= $prefixName + 'storage' # the storage account name you will create or use  
    $containerName= $prefixName + 'container'  # the storage container name to which you will attach the SAS policy with its SAS token  
    $policyName = $prefixName + 'policy' # the name of the SAS policy 

    # Set a variable for the name of the resource group you will create or use  
    $resourceGroupName=$prefixName + 'rg'   
  
    # Add an authenticated Azure account for use in the session   
    Connect-AzAccount    
  
    # Set the tenant, subscription and environment for use in the rest of   
    Set-AzContext -SubscriptionId $subscriptionID   
  
    # Create a new resource group - comment out this line to use an existing resource group  
    New-AzResourceGroup -Name $resourceGroupName -Location $locationName   
  
    # Create a new Azure Resource Manager storage account - comment out this line to use an existing Azure Resource Manager storage account  
    New-AzStorageAccount -Name $storageAccountName -ResourceGroupName $resourceGroupName -Type Standard_RAGRS -Location $locationName   
  
    # Get the access keys for the Azure Resource Manager storage account  
    $accountKeys = Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName  
  
    # Create a new storage account context using an Azure Resource Manager storage account  
    $storageContext = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $accountKeys[0].Value

    # Creates a new container in blob storage  
    $container = New-AzStorageContainer -Context $storageContext -Name $containerName  
  
    # Sets up a Stored Access Policy and a Shared Access Signature for the new container  
    $policy = New-AzStorageContainerStoredAccessPolicy -Container $containerName -Policy $policyName -Context $storageContext -StartTime $(Get-Date).ToUniversalTime().AddMinutes(-5) -ExpiryTime $(Get-Date).ToUniversalTime().AddYears(10) -Permission rwld

    # Gets the Shared Access Signature for the policy  
    $sas = New-AzStorageContainerSASToken -name $containerName -Policy $policyName -Context $storageContext
    Write-Host 'Shared Access Signature= '$($sas.Substring(1))''  

    # Sets the variables for the new container you just created
    $container = Get-AzStorageContainer -Context $storageContext -Name $containerName
    $cbc = $container.CloudBlobContainer 
  
    # Outputs the Transact SQL to the clipboard and to the screen to create the credential using the Shared Access Signature  
    Write-Host 'Credential T-SQL'  
    $tSql = "CREATE CREDENTIAL [{0}] WITH IDENTITY='Shared Access Signature', SECRET='{1}'" -f $cbc.Uri,$sas.Substring(1)   
    $tSql | clip  
    Write-Host $tSql 

    # Once you're done with the tutorial, remove the resource group to clean up the resources. 
    # Remove-AzResourceGroup -Name $resourceGroupName  
    ```  

3.  指令碼完成之後，會將 CREATE CREDENTIAL 陳述式放在剪貼簿中以供下一節使用。  


## <a name="2---create-a-sql-server-credential-using-a-shared-access-signature"></a>2 - 使用共用存取簽章建立 SQL Server 認證

在本節中，您將建立用來儲存安全性資訊的認證，這些資訊可供 SQL Server 用來寫入及讀取您在先前步驟中所建立的 Azure 容器內容。  
  
SQL Server 認證是用來儲存連接到 SQL Server 外部資源所需之驗證資訊的物件。 認證會儲存儲存體容器的 URI 路徑，以及該容器的共用存取簽章金鑰值。  

若要建立 SQL Server 認證，請依照下列步驟進行：  
  
1.  連接到 SQL Server Management Studio。  
2.  開啟新的查詢視窗，並連線到內部部署環境中資料引擎的 SQL Server 執行個體。  
  
3.  在新的查詢視窗中，貼上第 1 節的共用存取簽章及 CREATE CREDENTIAL 陳述式，然後執行該指令碼。  
  
    指令碼看起來類似下列程式碼。  
  
    ```sql   
    /* Example:
    USE master  
    CREATE CREDENTIAL [https://msfttutorial.blob.core.windows.net/containername] 
    WITH IDENTITY='SHARED ACCESS SIGNATURE'   
    , SECRET = 'sharedaccesssignature' 
    GO */
    
    USE master  
    CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>] 
      -- this name must match the container path, start with https and must not contain a forward slash at the end
    WITH IDENTITY='SHARED ACCESS SIGNATURE' 
      -- this is a mandatory string and should not be changed   
     , SECRET = 'sharedaccesssignature' 
       -- this is the shared access signature key that you obtained in section 1.   
    GO    
    ```  
  
4.  若要查看所有可用的認證，您可以在已連接到執行個體的查詢視窗中執行下列陳述式：  
  
    ```sql  
    SELECT * from sys.credentials  
    ```  
  
5.  開啟新的查詢視窗，並連線到您 Azure 虛擬機器中資料庫引擎的 SQL Server 執行個體。  
  
6.  在新的查詢視窗中，貼上第 1 節的共用存取簽章及 CREATE CREDENTIAL 陳述式，然後執行該指令碼。  
  
7.  如果您想要讓任何其他 SQL Server 執行個體擁有 Azure 容器的存取權，請重複步驟 5 和 6。  

## <a name="3---database-backup-to-url"></a>3 - 資料庫備份至 URL

在本節中，您會將內部部署 SQL Server 2016 執行個體中的 AdventureWorks2016 資料庫備份到您在[第 1 節](#1---create-stored-access-policy-and-shared-access-storage)建立的 Azure 容器。
  
> [!NOTE]  
> 如果您想要將 SQL Server 2012 SP1 CU2 或更新版本的資料庫或 SQL Server 2014 資料庫備份至此 Azure 容器，您可以使用 [這裡](/previous-versions/sql/2014/relational-databases/backup-restore/sql-server-backup-to-url?view=sql-server-2014&preserve-view=true) 所記載之已被取代的語法，透過 WITH CREDENTIAL 語法備份至 URL。  
  
若要將資料庫備份至 Blob 儲存體，請遵循下列步驟：  
  
1.  連接到 SQL Server Management Studio。  
2.  開啟新的查詢視窗，並連接到您 Azure 虛擬機器中資料庫引擎的 SQL Server 2016 執行個體。  
3.  將下列 Transact-SQL 指令碼複製並貼入 [查詢] 視窗中。 適當地修改儲存體帳戶名稱以及您在第 1 節中所指定容器的 URL，然後執行此指令碼。  
  
    ```sql  
  
    -- To permit log backups, before the full database backup, modify the database to use the full recovery model.  
    USE master;  
    ALTER DATABASE AdventureWorks2016  
       SET RECOVERY FULL;  
  
    -- Back up the full AdventureWorks2016 database to the container that you created in section 1  
    BACKUP DATABASE AdventureWorks2016   
       TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>/AdventureWorks2016_onprem.bak'  
  
    ```  
  
4.  開啟物件總管，並使用您的儲存體帳戶和帳戶金鑰連接到 Azure 儲存體。 
    1.  依序展開 [容器] 及您在第 1 節中建立的容器，然後確認上述步驟 3 中的備份出現在此容器中。  
  
   ![連線到 Azure 儲存體帳戶](media/tutorial-use-azure-blob-storage-service-with-sql-server-2016/connect-to-azure-storage.png)


## <a name="4----restore-database-to-virtual-machine-from-url"></a>4 - 從 URL 將資料庫還原至虛擬機器

在本節中，您會將 AdventureWorks2016 資料庫還原至 Azure 虛擬機器中的 SQL Server 2016 執行個體。
  
> [!NOTE]  
> 為了簡化本教學課程，我們將使用與資料庫備份時所用的相同資料和記錄檔容器。 在生產環境中，您可能會使用多個容器，通常也可能會使用多個資料檔案。 使用 SQL Server 2016 時，您也可以考慮將備份等量分割到多個 Blob，以提升備份大型資料庫時的備份效能。  
  
若要將 AdventureWorks2016 資料庫從 Azure Blob 儲存體還原至 Azure 虛擬機器中的 SQL Server 2016 執行個體，請遵循下列步驟：  
  
1.  連接到 SQL Server Management Studio。   
2.  開啟新的查詢視窗，並連接到您 Azure 虛擬機器中資料庫引擎的 SQL Server 2016 執行個體。   
3.  將下列 Transact-SQL 指令碼複製並貼入 [查詢] 視窗中。 適當地修改儲存體帳戶名稱以及您在第 1 節中所指定容器的 URL，然後執行此指令碼。  
  
    ```sql  
    -- Restore AdventureWorks2016 from URL to SQL Server instance using Azure blob storage for database files  
    RESTORE DATABASE AdventureWorks2016   
       FROM URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>/AdventureWorks2016_onprem.bak'   
       WITH  
          MOVE 'AdventureWorks2016_data' to 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>/AdventureWorks2016_Data.mdf'  
         ,MOVE 'AdventureWorks2016_log' to 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>/AdventureWorks2016_Log.ldf'  
    --, REPLACE  
  
    ```  
  
4.  開啟物件總管，並連接到您的 Azure SQL Server 2016 執行個體。   
5.  在 [物件總管] 中，展開 [資料庫] 節點，並確認 AdventureWorks2016 資料庫已還原 (必要時請重新整理節點)    
    1. 以滑鼠右鍵按一下 AdventureWorks2016，然後選取 [屬性]。
    1. 選取 [檔案]，並確認兩個資料庫檔案的路徑是指向 Azure Blob 容器中的 Blob URL (完成時請選取 [取消])。
    
     ![Azure VM 上的 AdventureWorks 資料庫](media/tutorial-use-azure-blob-storage-service-with-sql-server-2016/adventureworks-on-azure-vm.png)

    
8.  在物件總管中，連接到 Azure 儲存體。
    1. 依序展開 [容器] 及您在第 1 節中建立的容器，然後確認上述步驟 3 中的 AdventureWorks2016_Data.mdf 和 AdventureWorks2016_Log.ldf，與第 3 節中的備份檔案一起出現在此容器中 (必要時請重新整理節點)。  
  
   ![Azure 上容器內中的資料檔案](media/tutorial-use-azure-blob-storage-service-with-sql-server-2016/data-files-in-container.png)

## <a name="5---backup-database-using-file-snapshot-backup"></a>5 - 使用檔案快照集備份來備份資料庫

在本節中，您將使用檔案快照集備份，在 Azure 虛擬機器中備份 AdventureWorks2016 資料庫，以透過 Azure 快照集執行幾乎即時的備份。 如需檔案快照集備份的詳細資訊，請參閱 [Azure 中資料庫檔案的檔案快照集備份](../relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure.md)  
  
若要使用快照集檔案備份來備份 AdventureWorks2016 資料庫，請遵循下列步驟：  
  
1.  連接到 SQL Server Management Studio。   
2.  開啟新的查詢視窗，並連接到您 Azure 虛擬機器中資料庫引擎的 SQL Server 2016 執行個體。  
3.  將下列 Transact-SQL 指令碼複製並貼入查詢視窗中，然後執行此指令碼 (不要關閉此查詢視窗 - 您將在步驟 5 中再次執行此指令碼)。 此系統預存程序可讓您檢視組成指定資料庫之每個檔案的現有檔案快照集備份。 您會發現此資料庫沒有任何檔案快照集備份。  
  
    ```sql  
    -- Verify that no file snapshot backups exist  
    SELECT * FROM sys.fn_db_backup_file_snapshots ('AdventureWorks2016');  
    ```  
  
4.  將下列 Transact-SQL 指令碼複製並貼入 [查詢] 視窗中。 適當地修改儲存體帳戶名稱以及您在第 1 節中所指定容器的 URL，然後執行此指令碼。 注意此備份速度有多快。  
  
    ```sql  
    -- Backup the AdventureWorks2016 database with FILE_SNAPSHOT  
    BACKUP DATABASE AdventureWorks2016   
       TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>/AdventureWorks2016_Azure.bak'   
       WITH FILE_SNAPSHOT;    
    ```  
  
5.  確認步驟 4 中的指令碼順利執行之後，請再次執行下列指令碼。 請注意，步驟 4 中的檔案快照集備份作業會產生資料和記錄檔的檔案快照集。  
  
    ```sql  
    -- Verify that two file-snapshot backups exist  
    SELECT * FROM sys.fn_db_backup_file_snapshots ('AdventureWorks2016');  
  
    ```  
  
    ![fn_db_backup_file_snapshots 顯示快照集的結果](media/tutorial-use-azure-blob-storage-service-with-sql-server-2016/results-showing-snapshot.png) 
  
6.  在 [物件總管] 中，於您 Azure 虛擬機器的 SQL Server 2016 執行個體中，展開 [資料庫] 節點，並確認 AdventureWorks2016 資料庫已還原至此執行個體 (必要時請重新整理節點)。  
7.  在物件總管中，連接到 Azure 儲存體。   
8.  依序展開 [容器] 及您在第 1 節中建立的容器，然後確認上述步驟 4 中的 AdventureWorks2016_Azure.bak，與第 3 節中的備份檔案及第 4 節中的資料庫檔案一起出現在此容器中 (必要時請重新整理節點)。  
  
    ![Azure 上的快照集備份](media/tutorial-use-azure-blob-storage-service-with-sql-server-2016/snapshot-backup-on-azure.PNG)

## <a name="6----generate-activity-and-backup-log-using-file-snapshot-backup"></a>6 - 使用檔案快照集備份來產生活動和備份記錄

在本節中，您將使用檔案快照集備份，在 AdventureWorks2016 資料庫中產生活動並定期建立交易記錄備份。 如需如何使用檔案快照集備份的詳細資訊，請參閱 [Azure 中資料庫檔案的檔案快照集備份](../relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure.md)。  
  
若要使用檔案快照集備份，在 AdventureWorks2016 資料庫中產生活動並定期建立交易記錄備份，請遵循下列步驟：  
  
1.  連接到 SQL Server Management Studio。   
2.  開啟兩個新的查詢視窗，並將這兩個視窗都連接到 Azure 虛擬機器中資料庫引擎的 SQL Server 2016 執行個體。   
3.  將下列 Transact-SQL 指令碼複製並貼入其中一個查詢視窗中，然後執行此指令碼。 請注意，在我們新增步驟 4 的新資料列之前，Production.Location 資料表有 14 個資料列。  
  
    ```sql 
    -- Verify row count at start  
    SELECT COUNT (*) from AdventureWorks2016.Production.Location;    
    ```  
  
4.  將下列兩個 Transact-SQL 指令碼複製並分別貼入這兩個查詢視窗中。 適當地修改儲存體帳戶名稱以及您在第 1 節中所指定容器的 URL，然後分別在這兩個查詢視窗中同時執行這些指令碼。 這些指令碼需要大約七分鐘時間才能完成。  
  
    ```sql  
    -- Insert 30,000 new rows into the Production.Location table in the AdventureWorks2014 database in batches of 75  
    DECLARE @count INT=1, @inner INT;  
    WHILE @count < 400  
       BEGIN  
          BEGIN TRAN;  
             SET @inner =1;  
                WHILE @inner <= 75  
                   BEGIN;  
                      INSERT INTO AdventureWorks2016.Production.Location    
                         (Name, CostRate, Availability, ModifiedDate)   
                            VALUES (NEWID(), .5, 5.2, GETDATE());  
                      SET @inner = @inner + 1;  
                   END;  
          COMMIT;  
       WAITFOR DELAY '00:00:01';   
       SET @count = @count + 1;  
       END;  
    SELECT COUNT (*) from AdventureWorks2014.Production.Location;    
    ```  
  
    ```sql  
    --take 7 transaction log backups with FILE_SNAPSHOT, one per minute, and include the row count and the execution time in the backup file name   
    DECLARE @count INT=1, @device NVARCHAR(120), @numrows INT;  
    WHILE @count <= 7  
       BEGIN  
             SET @numrows = (SELECT COUNT (*) FROM AdventureWorks2016.Production.Location);  
             SET @device = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>/tutorial-' + CONVERT (varchar(10),@numrows) + '-' + FORMAT(GETDATE(), 'yyyyMMddHHmmss') + '.bak';  
             BACKUP LOG AdventureWorks2016 TO URL = @device WITH FILE_SNAPSHOT;  
             SELECT * from sys.fn_db_backup_file_snapshots ('AdventureWorks2016');  
          WAITFOR DELAY '00:1:00';   
             SET @count = @count + 1;  
       END;  
    ```  
  
5.  檢閱第一個指令碼的輸出，並注意現在最後的資料列計數是 29,939。  
  
    ![隨即顯示 29,939 個資料列計數](media/tutorial-use-azure-blob-storage-service-with-sql-server-2016/29-thousand-rows.png)
  
6.  檢閱第二個指令碼的輸出，並注意，每次 BACKUP LOG 陳述式執行時，系統都會建立兩個新的檔案快照集：一個記錄檔檔案快照集和一個資料檔檔案快照集，因此每一個資料庫檔案總共會有兩個檔案快照集。 第二個指令碼完成之後，請注意，現在共有 16 個檔案快照集，每一個資料庫檔案總共有 8 個：一個來自 BACKUP DATABASE 陳述式而另一個是每次執行 BACKUP LOG 陳述式時產生。  
  
   ![備份快照集結果](media/tutorial-use-azure-blob-storage-service-with-sql-server-2016/snapshot-back-results.png)
  
7.  在物件總管中，連接到 Azure 儲存體。  
  
8.  展開 [容器]，並展開您在第 1 節所建立的容器，並確認其中顯示七個新備份記錄檔與先前章節的資料檔案 (您可視需要重新整理)。  
  
    ![Azure 容器中的多個快照集](media/tutorial-use-azure-blob-storage-service-with-sql-server-2016/tutorial-snapshots-in-container.png)

## <a name="7---restore-a-database-to-a-point-in-time"></a>7 - 將資料庫還原至某個時間點

在本節中，您會將 AdventureWorks2016 資料庫還原至兩個交易記錄備份之間的某個時間點。  
  
使用傳統備份時，為了完成時間點還原，您必須使用完整資料庫備份 (可能是差異備份)，以及您想要還原的時間點之前及剛好超過此時間點的所有交易記錄檔。 使用檔案快照集備份時，您只需要兩個相鄰的記錄備份檔案，這兩個檔案會提供您想要還原的時間點之前時間範圍的目標張貼內容。 您只需要兩個記錄檔案快照集備份組，因為每個記錄備份都會建立每個資料庫檔案的檔案快照集 (每個資料檔案和記錄檔)。  
  
若要將資料庫從檔案快照集備份組還原至指定的時間點，請遵循下列步驟：  
  
1.  連接到 SQL Server Management Studio。  
2.  開啟新的查詢視窗，並連接到您 Azure 虛擬機器中資料庫引擎的 SQL Server 2016 執行個體。   
3.  將下列 Transact-SQL 指令碼複製並貼入查詢視窗中，然後執行此指令碼。 確認 Production.Location 資料表有 29,939 個資料列，再將它還原至步驟 5 中有較少資料列的時間點。  
  
    ```sql
    -- Verify row count at start  
    SELECT COUNT (*) from AdventureWorks2016.Production.Location   
    ```  
  
    ![隨即顯示 29,939 個資料列計數](media/tutorial-use-azure-blob-storage-service-with-sql-server-2016/29-thousand-rows.png) 
  
4.  將下列 Transact-SQL 指令碼複製並貼入 [查詢] 視窗中。 選取兩個相鄰的記錄備份檔案，並將檔案名稱轉換成此指令碼所需的日期和時間。 適當地修改儲存體帳戶名稱以及您在第 1 節中所指定容器的 URL，提供第一個和第二個備份檔案名稱，提供 "June 26, 2018 01:48 PM" 格式的 STOPAT 時間，然後執行此指令碼。 此指令碼需要幾分鐘才能完成  
  
    ```sql  
    -- restore and recover to a point in time between the times of two transaction log backups, and then verify the row count  
    ALTER DATABASE AdventureWorks2016 SET SINGLE_USER WITH ROLLBACK IMMEDIATE;  
    RESTORE DATABASE AdventureWorks2016   
       FROM URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>/<firstbackupfile>.bak'   
       WITH NORECOVERY,REPLACE;  
    RESTORE LOG AdventureWorks2016   
       FROM URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>/<secondbackupfile>.bak'    
       WITH RECOVERY, STOPAT = 'June 26, 2018 01:48 PM';  
    ALTER DATABASE AdventureWorks2016 set multi_user;  
    -- get new count  
    SELECT COUNT (*) FROM AdventureWorks2016.Production.Location ;
    ```  
  
5.  檢閱輸出。 請注意，還原後的資料列計數為 18,389，也就是介於記錄備份 5 和 6 之間的資料列計數 (您的資料列計數會有所不同)。  
  
    ![18-thousand-rows.JPG](media/tutorial-use-azure-blob-storage-service-with-sql-server-2016/18-thousand-rows.png)

## <a name="8----restore-as-new-database-from-log-backup"></a>8 - 從記錄備份還原為新的資料庫

在本節中，您會從檔案快照集交易記錄備份將 AdventureWorks2016 資料庫還原為新的資料庫。  
  
在此情況下，您會基於商務分析和報告在不同的虛擬機器上執行還原至 SQL Server 執行個體。 還原至不同虛擬機器上的不同執行個體，會基於此目的，將工作負載卸載至專用虛擬機器並進行大小調整，方法是從交易式系統中移除其資源需求。  
  
使用檔案快照集備份從交易記錄備份進行還原十分地快速，而且比使用傳統串流備份還要快。 運用傳統串流備份，您必須使用完整資料庫備份 (可能是差異備份)，以及部分或所有交易記錄備份 (或新的完整資料庫備份)。 不過，使用檔案快照集記錄備份，您只需要最新的記錄備份 (或任何其他記錄備份，或任兩個相鄰記錄備份以將時間點還原至兩個記錄備份時間之間的時間點)。 若要更為清楚，您只需要一個記錄檔案快照集備份組，因為每個檔案快照集記錄備份都會建立每個資料庫檔案的檔案快照集 (每個資料檔案和記錄檔)。  
  
若要使用檔案快照集備份從交易記錄備份將資料庫還原到新的資料庫，請執行下列步驟︰  
  
1.  連接到 SQL Server Management Studio。   
2.  開啟新的查詢視窗，並連接到 Azure 虛擬機器中資料庫引擎的 SQL Server 2016 執行個體。  
  
    > [!NOTE]  
    > 如果這是與您之前用於先前章節不同的 Azure 虛擬機器，請確定您遵循 [2 - 使用共用存取簽章建立 SQL Server 認證](#2---create-a-sql-server-credential-using-a-shared-access-signature)中的步驟進行。 若您想要還原到不同容器，請遵循 [1 - 建立預存存取原則和共用存取儲存體](#1---create-stored-access-policy-and-shared-access-storage)中的步驟。  
  
3.  將下列 Transact-SQL 指令碼複製並貼入 [查詢] 視窗中。 選取您想要使用的記錄備份檔案。 適當地修改儲存體帳戶名稱以及您在第 1 節中所指定容器的 URL，並提供記錄備份檔案名稱，然後執行此指令碼。  
  
    ```sql  
    -- restore as a new database from a transaction log backup file  
    RESTORE DATABASE AdventureWorks2016_EOM   
        FROM URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>/<logbackupfile.bak>'    
        WITH MOVE 'AdventureWorks2016_data' to 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>/AdventureWorks2014_EOM_Data.mdf'  
       , MOVE 'AdventureWorks2016_log' to 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>/AdventureWorks2014_EOM_Log.ldf'  
       , RECOVERY  
    --, REPLACE   
    ```  
  
4.  請檢閱輸出，確認還原成功。   
5.  在物件總管中，連接到 Azure 儲存體。    
6.  展開 [容器]，並展開您在第 1 節所建立的容器 (必要時，請重新整理)，然後確認新資料和記錄檔出現在容器以及先前章節的 Blob 中。  
  
    ![Azure 容器，顯示新的資料庫資料和記錄檔](media/tutorial-use-azure-blob-storage-service-with-sql-server-2016/new-db-in-azure-container.png)

## <a name="9---manage-backup-sets-and-file-snapshot-backups"></a>9 - 管理備份組和檔案快照集備份

在本節中，您將會使用 [sp_delete_backup &#40;Transact-SQL&#41;](../relational-databases/system-stored-procedures/snapshot-backup-sp-delete-backup.md) 系統預存程序來刪除備份組。 這個系統預存程序會刪除備份檔案以及與這個備份組相關聯的每個資料庫檔案上的檔案快照集。  
  
> [!NOTE]  
> 如果您只是刪除 Azure Blob 容器中的備份檔案來嘗試刪除備份組，則只會刪除備份檔案本身，相關聯的檔案快照集將會予以保留。 如果您發現自己處於這種情況，請使用 [sys.fn_db_backup_file_snapshots &#40;Transact-SQL&#41;](../relational-databases/system-functions/sys-fn-db-backup-file-snapshots-transact-sql.md) 系統函數來識別孤立檔案快照集 URL，以及使用 [sp_delete_backup_file_snapshot &#40;Transact-SQL&#41;](../relational-databases/system-stored-procedures/snapshot-backup-sp-delete-backup-file-snapshot.md) 系統預存程序來刪除每個孤立檔案快照集。 如需詳細資訊，請參閱  [Azure 中資料庫檔案的檔案快照集備份](../relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure.md)。  
  
若要刪除檔案快照集備份組，請遵循下列步驟：  
  
1.  連接到 SQL Server Management Studio。  
2.  開啟新的查詢視窗，並連接到您的 Azure 虛擬機器中資料庫引擎的 SQL Server 2016 執行個體 (或具有這個容器之讀取和寫入權限的任何 SQL Server 2016 執行個體)。   
3.  將下列 Transact-SQL 指令碼複製並貼入 [查詢] 視窗中。 選取您想要刪除的記錄備份與其相關聯的檔案快照集。 適當地修改儲存體帳戶名稱以及您在第 1 節中所指定容器的 URL，並提供記錄備份檔案名稱，然後執行此指令碼。  
  
  ```sql
  sys.sp_delete_backup 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>/tutorial-21764-20181003205236.bak';  
  ```  
  
4.  在物件總管中，連接到 Azure 儲存體。  
5.  依序展開 [容器] 以及您在第 1 節中建立的容器，然後確認步驟 3 中所使用的備份檔案不再出現於這個容器中 (必要時，請重新整理節點)。  
  
    ![Azure 容器，顯示刪除記錄備份 Blob](media/tutorial-use-azure-blob-storage-service-with-sql-server-2016/deleted-backup-snapshot.png)
  
6.  將下列 Transact-SQL 指令碼複製並貼入查詢視窗中，然後執行此指令碼，確認已刪除兩個檔案快照集。  
  
    ```sql  
    -- verify that two file snapshots have been removed  
    SELECT * from sys.fn_db_backup_file_snapshots ('AdventureWorks2016');   
    ```  
    ![顯示 2 個檔案快照集已刪除的 [結果] 窗格](media/tutorial-use-azure-blob-storage-service-with-sql-server-2016/results-of-two-deleted-snapshot-files.png)

## <a name="10---remove-resources"></a>10 - 移除資源

當您完成本教學課程後，若要節省資源，請務必刪除在本教學課程中建立的資源群組。 

若要刪除資源群組，請執行下列 PowerShell 程式碼：

  ```powershell
  # Define global variables for the script  
  $prefixName = '<prefix name>'  # should be the same as the beginning of the tutorial
  
  # Set a variable for the name of the resource group you will create or use  
  $resourceGroupName=$prefixName + 'rg'   
  
  # Adds an authenticated Azure account for use in the session   
  Connect-AzAccount    
  
  # Set the tenant, subscription and environment for use in the rest of   
  Set-AzContext -SubscriptionId $subscriptionID    
    
  # Remove the resource group
  Remove-AzResourceGroup -Name $resourceGroupName   
  ```


  
## <a name="see-also"></a>另請參閱

[Microsoft Azure 中的 SQL Server 資料檔案](../relational-databases/databases/sql-server-data-files-in-microsoft-azure.md)  
[Azure 中資料庫檔案的檔案快照集備份](../relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure.md)  
[SQL Server 備份至 URL](../relational-databases/backup-restore/sql-server-backup-to-url.md) 
[共用存取簽章，第 1 部分：了解 SAS 模型](/azure/storage/common/storage-sas-overview)  
[建立容器](/rest/api/storageservices/Create-Container)  
[Set Container ACL (設定容器 ACL)](/rest/api/storageservices/Set-Container-ACL)  
[取得容器 ACL](/rest/api/storageservices/Get-Container-ACL)
[認證 &#40;資料庫引擎&#41;](../relational-databases/security/authentication-access/credentials-database-engine.md)  
[CREATE CREDENTIAL &#40;Transact-SQL&#41;](../t-sql/statements/create-credential-transact-sql.md)  
[sys.credentials &#40;Transact-SQL&#41;](../relational-databases/system-catalog-views/sys-credentials-transact-sql.md)  
[sp_delete_backup &#40;Transact-SQL&#41;](../relational-databases/system-stored-procedures/snapshot-backup-sp-delete-backup.md)  
[sys.fn_db_backup_file_snapshots &#40;Transact-SQL&#41;](../relational-databases/system-functions/sys-fn-db-backup-file-snapshots-transact-sql.md)  
[sp_delete_backup_file_snapshot &#40;Transact-SQL&#41;](../relational-databases/system-stored-procedures/snapshot-backup-sp-delete-backup-file-snapshot.md) [Azure 中的資料庫檔案檔案快照集備份](../relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure.md)