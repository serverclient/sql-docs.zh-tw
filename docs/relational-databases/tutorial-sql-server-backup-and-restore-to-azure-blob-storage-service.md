---
title: 快速入門：備份及還原至 Azure Blob 儲存體服務
description: 快速入門：了解如何將備份寫入至 Azure Blob 儲存體服務以及從中還原。 建立 Azure Blob 容器、寫入備份，然後進行還原。
ms.custom: seo-dt-2019
ms.date: 12/21/2020
ms.prod: sql
ms.technology: backup-restore
ms.prod_service: backup-restore
ms.reviewer: ''
ms.topic: quickstart
author: cawrites
ms.author: chadam
ms.openlocfilehash: d27f53f80c8f987106f90816a4566339d777e6f6
ms.sourcegitcommit: bb54e4c9dd8c97365b7a96dfcd557b8b86d06978
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/22/2020
ms.locfileid: "97736896"
---
# <a name="quickstart-sql-backup-and-restore-to-azure-blob-storage-service"></a>快速入門：SQL 備份及還原至 Azure Blob 儲存體服務

[!INCLUDE [sqlserver2016-asdbmi](../includes/applies-to-version/sqlserver2016-asdbmi.md)]

本快速入門可協助您了解如何將備份寫入至 Azure Blob 儲存體服務以及從中還原。  此文章說明如何建立 Azure Blob 容器、將備份寫入到 Blob 服務，然後執行還原。

> [!NOTE]
> SQL Server 2012 SP1 CU2 已推出對備份到 Azure Blob 儲存體的支援。 SQL Server 2014 和先前版本不支援此快速入門文章中所述的共用存取簽章 (SAS)。
>
> 針對 SQL Server 2014 和先前版本，請使用[教學課程：SQL Server 2014 備份及還原至 Microsoft Azure Blob 儲存體](/previous-versions/sql/2014/relational-databases/backup-restore/sql-server-backup-to-url)。
>
  
## <a name="prerequisites"></a>必要條件

若要完成本快速入門，您必須熟悉 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 備份與還原概念以及 T-SQL 語法。  您需要 Azure 儲存體帳戶、SQL Server Management Studio (SSMS)，以及執行 SQL Server 伺服器或 Azure SQL 受控執行個體之伺服器的存取權。 此外，用來發出 BACKUP 或 RESTORE 命令的帳戶，應該位於擁有 **ALTER ANY CREDENTIAL** 權限的 **db_backupoperator** 資料庫角色中。 

- 取得免費 [Azure 帳戶](https://azure.microsoft.com/offers/ms-azr-0044p/)。
- 建立 [Azure 儲存體帳戶](/azure/storage/common/storage-quickstart-create-account?tabs=portal)。
- 安裝 [SQL Server Management Studio](../ssms/download-sql-server-management-studio-ssms.md)。
- 安裝 [SQL Server 2017 Developer Edition](https://www.microsoft.com/sql-server/sql-server-downloads)，或使用透過 [Azure SQL 虛擬機器](/azure/sql-database/sql-database-managed-instance-configure-vm)或[點對站](/azure/sql-database/sql-database-managed-instance-configure-p2s)建立的連線來部署 [Azure SQL 受控執行個體](/azure/sql-database/sql-database-managed-instance-get-started)。
- 將使用者帳戶指派給 [db_backupoperator](./security/authentication-access/database-level-roles.md) 的角色，並授與 [ALTER ANY CREDENTIAL](../t-sql/statements/alter-credential-transact-sql.md) 權限。 

## <a name="create-azure-blob-container"></a>建立 Azure Blob 容器
容器會提供一組 Blob 的群組。 所有 Blob 都必須位於容器中。 儲存體帳戶可以包含不限數目的容器，但是至少必須具有一個容器。 容器可以儲存不限數目的 Blob。 

若要建立容器，請遵循下列步驟：

1. 開啟 Azure 入口網站。 
1. 巡覽至您的儲存體帳戶。 
1. 選取儲存體帳戶，向下捲動至 [Blob 服務]。
1. 選取 [Blob]，然後選取 [+ 容器] 以新增新的容器。 
1. 輸入容器名稱，並記下您指定的容器名稱。 此資訊會在本快速入門稍後的 T-SQL 陳述式內的 URL (備份檔路徑) 中用到。 
1. 選取 [確定]。 
    
    ![新增容器](media/tutorial-sql-server-backup-and-restore-to-azure-blob-storage-service/new-container.png)

  > [!NOTE]
  > 即使您選擇建立公用容器，SQL Server 備份與還原仍然需要儲存體帳戶的驗證。 您也可以使用 REST API，以程式設計方式建立容器。 如需詳細資訊，請參閱[建立容器](/rest/api/storageservices/Create-Container)

## <a name="create-a-test-database"></a>建立測試資料庫 
在此步驟中，使用 SQL Server Management Studio (SSMS) 來建立測試資料庫。 

1. 啟動 [SQL Server Management Studio (SSMS)](../ssms/download-sql-server-management-studio-ssms.md) 並連線至 SQL Server 執行個體。
1. 開啟 [新增查詢] 視窗。 
1. 執行下列 Transact-SQL (T-SQL) 程式碼以建立測試資料庫。 重新整理 [物件總管] 中的 [資料庫] 節點以查看新的資料庫。 在 SQL 受控執行個體上建立的新資料庫會自動啟用 TDE，因此您將必須加以停用以繼續。 

```sql
USE [master]
GO

-- Create database
CREATE DATABASE [SQLTestDB]
GO

-- Create table in database
USE [SQLTestDB]
GO
CREATE TABLE SQLTest (
    ID INT NOT NULL PRIMARY KEY,
    c1 VARCHAR(100) NOT NULL,
    dt1 DATETIME NOT NULL DEFAULT getdate()
)
GO

-- Populate table 
USE [SQLTestDB]
GO

INSERT INTO SQLTest (ID, c1) VALUES (1, 'test1')
INSERT INTO SQLTest (ID, c1) VALUES (2, 'test2')
INSERT INTO SQLTest (ID, c1) VALUES (3, 'test3')
INSERT INTO SQLTest (ID, c1) VALUES (4, 'test4')
INSERT INTO SQLTest (ID, c1) VALUES (5, 'test5')
GO

SELECT * FROM SQLTest
GO

-- Disable TDE for newly-created databases on SQL Managed Instance 
USE [SQLTestDB];
GO
ALTER DATABASE [SQLTestDB] SET ENCRYPTION OFF;
GO
```

## <a name="create-credential"></a>建立認證

依照下面的步驟使用 SQL Server Management Studio 中的 GUI 來建立認證。 或者，您也能以[程式設計](tutorial-use-azure-blob-storage-service-with-sql-server-2016.md#2---create-a-sql-server-credential-using-a-shared-access-signature)方式建立認證。 

1. 展開 [SQL Server Management Studio(SSMS)](../ssms/download-sql-server-management-studio-ssms.md) 之 [物件總管] 內的 [資料庫] 節點。
1. 以滑鼠右鍵按一下新的 `SQLTestDB` 資料庫，將滑鼠游標移到 [工作] 上方並選取 [備份] 以啟動 [備份資料庫] 精靈。 
1. 從 [備份至] 目的地下拉式清單選取 [URL]，然後選取 [新增] 以啟動 [選取備份目的地] 對話方塊。 

   ![備份至 URL](media/tutorial-sql-server-backup-and-restore-to-azure-blob-storage-service/back-up-to-url.png)

1. 選取 [選取備份目的地] 對話方塊上的 [新增容器] 以啟動 [連線至 Microsoft 訂用帳戶] 視窗。 

   ![[選取備份目的地] 對話方塊視窗的螢幕擷取畫面，其中已標註 [新增容器] 選項。](media/tutorial-sql-server-backup-and-restore-to-azure-blob-storage-service/select-backup-destination.png)

1. 選取 [登入] 以登入 Azure 入口網站，然後繼續完成登入程序。 
1. 從下拉式清單選取您的 **訂用帳戶**。 
1. 從下拉式清單中選取您的 **儲存體帳戶**。 
1. 從下拉式清單選取您先前建立的容器。 
1. 選取 [建立認證] 以產生您的 *共用存取簽章 (SAS)* 。  **儲存此值，因為您將需要它才能進行還原。**

   ![建立認證](media/tutorial-sql-server-backup-and-restore-to-azure-blob-storage-service/create-credential.png)

1. 選取 [確定] 以關閉 [連線至 Microsoft 訂用帳戶] 視窗。 這會在 [選取備份目的地] 對話方塊上填入 *Azure 儲存體容器* 值。 選取 [確定] 以選擇選取的儲存體容器，並關閉對話方塊。 
1. 目前，您可以跳到下一節中的步驟 4 以快速建立資料庫備份，或關閉 [備份資料庫] 精靈以改為使用 Transact-SQL 備份資料庫來繼續。 


## <a name="back-up-database"></a>備份資料庫
在此步驟中，使用 SQL Server Management Studio 內的 GUI 或 Transact-SQL (T-SQL) 將資料庫 `SQLTestDB` 備份到您的 Azure Blob 儲存體帳戶。 

# <a name="ssms"></a>[SSMS](#tab/SSMS)

1. 若 [備份資料庫] 精靈尚未開啟，請展開 [SQL Server Management Studio(SSMS)](../ssms/download-sql-server-management-studio-ssms.md) 之 [物件總管] 內的 [資料庫] 節點。
1. 以滑鼠右鍵按一下新的 `SQLTestDB` 資料庫，將滑鼠游標移到 [工作] 上方並選取 [備份] 以啟動 [備份資料庫] 精靈。 
1. 從 [備份至] 下拉式清單選取 [URL]，然後選取 [新增] 以啟動 [選取備份目的地] 對話方塊。 

   ![備份至 URL](media/tutorial-sql-server-backup-and-restore-to-azure-blob-storage-service/back-up-to-url.png)

1. 在 [Azure 儲存體容器] 下拉式清單中選取您在上一個步驟中建立的容器。 

   ![Azure 儲存體容器](media/tutorial-sql-server-backup-and-restore-to-azure-blob-storage-service/azure-storage-container.png)

1. 選取 [備份資料庫] 精靈上的 [確定] 以備份您的資料庫。 
1. 成功備份資料庫之後，請選取 [確定] 以關閉所有備份相關視窗。 

   > [!TIP]
   > 您可以透過選取 [備份資料庫] 精靈頂端的 [指令碼] 來建立此命令背後的 Transact-SQL：![指令碼命令](media/tutorial-sql-server-backup-and-restore-to-azure-blob-storage-service/script-backup-command.png)


# <a name="transact-sql"></a>[Transact-SQL](#tab/tsql)

使用 Transact-SQL 透過執行下列命令以備份您的資料庫： 


```sql
USE [master]

BACKUP DATABASE [SQLTestDB] 
TO  URL = N'https://msftutorialstorage.blob.core.windows.net/sql-backup/sqltestdb_backup_2020_01_01_000001.bak' 
WITH  COPY_ONLY, CHECKSUM
GO
```

---

## <a name="delete-database"></a>刪除資料庫
在此步驟中，請先刪除資料庫，然後再執行還原。 此步驟僅適用於此教學課程的目的，在一般資料庫管理程序中不太可能使用。 您可以跳過此步驟，但是在受控執行個體上進行還原時，您將需要變更資料庫的名稱，或執行還原命令 `WITH REPLACE`，以便在內部部署環境中成功還原資料庫。 

# <a name="ssms"></a>[SSMS](#tab/SSMS)

1. 展開 [物件總管] 中的 [資料庫] 節點、以滑鼠右鍵按一下 []`SQLTestDB` 資料庫，然後選取刪除以啟動 [刪除物件] 精靈。 
1. 在受控執行個體上，選取 [確定] 以刪除資料庫。 在內部部署環境中，選取 [關閉現有的連接] 旁的核取方塊並選取 [確定] 以刪除資料庫。 

# <a name="transact-sql"></a>[Transact-SQL](#tab/tsql)

執行下列 Transact-SQL 命令以刪除資料庫：

```sql
USE [master]
GO
DROP DATABASE [SQLTestDB]
GO

-- If connections currently exist on-premises, you'll need to set the database into single user mode first
USE [master]
GO
ALTER DATABASE [SQLTestDB] SET  SINGLE_USER WITH ROLLBACK IMMEDIATE
GO
USE [master]
GO
DROP DATABASE [SQLTestDB]
GO
```

---


## <a name="restore-database"></a>對話方塊的 
在此步驟中，使用 SQL Server Management Studio 中的 GUI 或使用 Transact-SQL 來還原資料庫。 

# <a name="ssms"></a>[SSMS](#tab/SSMS)

1. 以滑鼠右鍵按一下 SQL Server Management Studio 內 [物件總管] 中的 [資料庫] 節點，然後選取 [還原資料庫]。 
1. 選取 [裝置]，然後選取省略符號 (...) 以選擇裝置。 

   ![選取還原裝置](media/tutorial-sql-server-backup-and-restore-to-azure-blob-storage-service/select-restore-device.png)

1. 從 [備份媒體類型] 下拉式清單選取 [URL] 並選取 [新增] 以新增您的裝置。 

   ![新增備份裝置](media/tutorial-sql-server-backup-and-restore-to-azure-blob-storage-service/add-backup-device.png)

1. 從下拉式清單選取容器，然後貼到您在建立認證時儲存的共用存取簽章 (SAS)。 

   ![[選取備份檔案位置] 對話方塊的螢幕擷取畫面，其中已填入 [共用存取簽章] 欄位。](media/tutorial-sql-server-backup-and-restore-to-azure-blob-storage-service/restore-from-container.png)

1. 選取 [確定] 以選取備份檔案位置。 
1. 展開 [容器] 並選取您的備份檔案所在的容器。 
1. 選取您要還原的備份檔案，然後選取 [確定]。 如果看不到任何檔案，則您可能使用了錯誤的 SAS 金鑰。 您可以遵循與先前相同的步驟來重新產生 SAS 金鑰，以新增容器。 

   ![選取還原檔案](media/tutorial-sql-server-backup-and-restore-to-azure-blob-storage-service/select-restore-file.png)

1. 選取 [確定] 以關閉 [選取備份裝置] 對話方塊。
1. 選取 [確定] 以還原您的資料庫。

# <a name="transact-sql"></a>[Transact-SQL](#tab/tsql)

若要從 Azure Blob 儲存體還原您的內部部署資料庫，請修改下列 Transact-SQL 以使用您自己的儲存體帳戶，然後在新查詢視窗內執行它。

```sql
USE [master]
RESTORE DATABASE [SQLTestDB] FROM 
URL = N'https://msftutorialstorage.blob.core.windows.net/sql-backup/sqltestdb_backup_2020_01_01_000001.bak'
```

---


## <a name="see-also"></a>另請參閱 
下列是一些建議閱讀的主題，這些主題可讓您了解針對 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 備份使用 Azure Blob 儲存體服務的概念與最佳做法。  
  
-   [使用 Microsoft Azure Blob 儲存體服務進行 SQL Server 備份及還原](../relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service.md)   
-   [SQL Server 備份至 URL 的最佳做法和疑難排解](../relational-databases/backup-restore/sql-server-backup-to-url-best-practices-and-troubleshooting.md)  
