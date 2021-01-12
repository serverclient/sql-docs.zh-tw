---
title: Microsoft Azure 中的 SQL Server 資料檔案 | Microsoft Docs
description: 探索將 SQL Server 資料檔案儲存在 Microsoft Azure 儲存體中的核心概念與考量，以及使用 Azure 儲存體的一些優點。
ms.custom: ''
ms.date: 12/04/2019
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: supportability
ms.topic: conceptual
ms.assetid: 38ffd9c2-18a5-43d2-b674-e425addec4e4
author: MikeRayMSFT
ms.author: mikeray
ms.openlocfilehash: 85d9e42b9c0ac537d6f33bd884b898374119972d
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98099323"
---
# <a name="sql-server-data-files-in-microsoft-azure"></a>Microsoft Azure 中的 SQL Server 資料檔案
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
  ![Azure 上的資料檔案](../../relational-databases/databases/media/data-files-on-azure.png "Azure 上的資料檔案")  
  
Microsoft Azure 中的 SQL Server 資料檔案可提供將 SQL Server 資料庫檔案儲存為 Blob 的原生支援。 它可讓您在內部部署執行的 SQL Server 或 Microsoft Azure 虛擬機器執行的 SQL Server 中建立資料庫，以將您的 Microsoft Azure Blob 儲存體資料儲存在專用儲存位置。 它也簡化了在機器之間移動資料庫的程序。 您可以從一部機器中斷連結資料庫，並將其連結到另一部機器。 此外，它可讓您還原至 Microsoft Azure 儲存體或從中還原，為您的資料庫備份檔案提供替代儲存位置。 因此，它會針對資料虛擬化、資料移動、安全性和可用性提供許多優點，進而實現許多混合式方案，而且成本低廉、維護簡單，即可達到高可用性和彈性調整的效果。
 
> [!IMPORTANT]  
>  不建議且不支援將系統資料庫儲存在 Azure Blob 儲存體中。 

 本主題介紹將 SQL Server 資料檔案儲存在 Microsoft Azure 儲存體服務中的核心概念與考量。  
  
 如需如何使用此新功能的實際操作體驗，請參閱[教學課程：搭配 SQL Server 2016 資料庫使用 Microsoft Azure Blob 儲存體服務](../tutorial-use-azure-blob-storage-service-with-sql-server-2016.md)。  
  
## <a name="why-use-sql-server-data-files-in-microsoft-azure"></a>為何要在 Microsoft Azure 中使用 SQL Server 資料檔案？ 

- **簡單快速移轉優點：** 這項功能會在內部部署的電腦之間以及內部部署與雲端環境之間一次移動一個資料庫來藉以簡化移轉程序，且不需要變更任何應用程式。 因此，它支援累加式移轉，同時就地維護您現有的內部部署基礎結構。 此外，當應用程式需要在內部部署環境中的多個位置執行時，存取集中式資料儲存體可簡化應用程式邏輯。 在某些情況下，您可能需要快速地設定散佈於不同地理位置的電腦中心，以便收集許多不同來源的資料。 只要使用這項新的增強功能，您就可以將許多資料庫儲存成 Microsoft Azure Blob，然後執行 Transact-SQL 指令碼，在本機電腦或虛擬機器上建立資料庫，而不需要在不同的位置之間移動資料。

- **成本和無限制儲存體優點：** 這項功能可讓您在 Microsoft Azure 中擁有無限制的異地儲存體，同時運用內部部署計算資源。 當您將 Microsoft Azure 當做儲存位置使用時，可以輕鬆地將重點放在應用程式邏輯上，而不會產生硬體管理的負擔。 如果您遺失了某個內部部署的計算節點，不需要移動任何資料，就可以設定新的節點。

- **高可用性和災害復原優點：** 使用 Microsoft Azure 功能中的 SQL Server 資料檔案可簡化高可用性和災害復原解決方案。 例如，假設某部 Microsoft Azure 虛擬機器或 SQL Server 執行個體當機，您只要重新建立 Blob 的連結，就可以在新的 SQL Server 執行個體中重建資料庫。

- **安全性優點：** 這項新的增強功能可讓您分隔計算執行個體與儲存執行個體。 您可以擁有完整加密的資料庫，而且只針對計算執行個體進行解密，但不在儲存執行個體中進行解密。 換言之，使用這項新的增強功能時，您可以使用透明資料加密 (TDE) 憑證 (與資料實體分隔) 來加密公用雲端中的所有資料。 TDE 金鑰可以儲存在 master 資料庫中，而這個資料庫會儲存在實體安全的內部部署電腦本機並且進行本機備份。 您可以使用這些本機金鑰來加密位於 Microsoft Azure 儲存體中的資料。 如果您的雲端儲存體帳戶認證遭竊，您的資料仍然保持安全，因為 TDE 憑證永遠位於內部部署。

- **快照集備份：** 這項功能可讓您使用 Azure 快照集，針對使用 Azure Blob 儲存體服務儲存的資料庫檔案，提供近乎即時備份及更快速的還原。 這項功能可簡化備份和還原原則。 如需詳細資訊，請參閱 [Azure 中資料庫檔案的檔案快照集備份](../../relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure.md)。  

## <a name="concepts-and-requirements"></a>概念和需求  
  
Azure 磁碟與企業級商務持續性和災害復原解決方案相容。 如果您直接將資料庫儲存在 Blob 或 Azure Premium 檔案中，資料不會針對基礎結構、管理和監視自動與您的 VM 相關聯。

將資料庫檔案放在分頁 Blob 上是比使用 Azure 磁碟更先進的功能，它既簡單又方便易用。

基本指導方針是使用 Azure 磁碟，除非您真的需要避免建立資料複本以進行備份，或還原為資料大小的作業，否則就會用到。 針對高可用性和災害復原，使用定期備份至 URL 或受控備份至 Blob 儲存體也會比檔案快照集備份更有用，因為您可以取得生命週期管理、多區域支援、虛刪除，以及您備份之 Blob 儲存體的所有其他功能。

### <a name="azure-storage-concepts"></a>Azure 儲存體概念  
使用 Azure 功能中的 SQL Server 資料檔案時，您必須在 Azure 中建立儲存體帳戶和容器。 然後，您必須建立 SQL Server 認證，其中包括容器原則的相關資訊以及存取容器所需的共用存取簽章。  

在 [Microsoft Azure](https://azure.microsoft.com) 中，[Azure 儲存體](https://azure.microsoft.com/services/storage/)帳戶代表存取 Blob 之命名空間的最高層級。 儲存體帳戶可以包含不限制數目的容器，只要其總大小低於儲存體限制即可。 如需有關儲存體限制的最新資訊，請參閱 [Azure 訂閱與服務限制、配額及條件約束](/azure/azure-subscription-service-limits)。 容器會提供一組 [Blob](/azure/storage/common/storage-introduction#blob-storage) 的群組。 所有 Blob 都必須位於容器中。 帳戶可以包含不限制數目的容器。 同樣地，容器可以儲存不限數目的 Blob。 Azure 儲存體可以儲存的 Blob 類型有兩種：區塊和分頁 Blob。 這項新功能使用的是分頁 Blob，而且當檔案中的位元組範圍經常修改時，更有效率。 您可以使用以下 URL 格式存取 Blob：`https://storageaccount.blob.core.windows.net/<container>/<blob>`。  

### <a name="azure-billing-considerations"></a>Azure 計費考量  

 在決策和規劃程序中，估計使用 Azure 服務的成本是很重要的事項。 將 SQL Server 資料檔案儲存在 Azure 儲存體中時，需要支付與儲存體和交易相關聯的成本。 此外，實作 Azure 儲存體功能中的 SQL Server 資料檔案時，還需要每隔 45 至 60 秒，隱含地更新 Blob 租用一次。 這也會產生每個資料庫檔案 (例如 .mdf 或 .ldf) 的交易成本。 使用 [Azure 價格](https://azure.microsoft.com/pricing/)頁面上的資訊來協助估計與使用 Azure 儲存體和 Azure 虛擬機器的每月相關成本。  
  
### <a name="sql-server-concepts"></a>SQL 伺服器概念  

使用這項新的增強功能時，您必須執行下列動作：

- 在容器上建立原則，同時產生共用存取簽章 (SAS) 金鑰。

- 針對資料或記錄檔所使用的每一個容器，建立名稱符合容器路徑的 SQL Server 認證。

- 將 Azure 儲存體容器的相關資訊、其相關聯的原則名稱以及 SAS 金鑰，儲存在 SQL Server 認證存放區中。

下列範例會假設已經建立 Azure 儲存體容器，而且已經建立具有讀取、寫入和列出權限的原則。 在容器上建立原則會產生 SAS 金鑰，這個金鑰會以未加密狀態安全地保存在記憶體中，而且 SQL Server 需要此金鑰才能存取容器中的 Blob 檔案。 

在下列程式碼片段中，以 SAS 金鑰取代 `'<your SAS key>'`。 SAS 金鑰看起來會像 `'sr=c&si=<MYPOLICYNAME>&sig=<THESHAREDACCESSSIGNATURE>'`。

```sql
CREATE CREDENTIAL [https://testdb.blob.core.windows.net/data]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = '<your SAS key>'  
  
CREATE DATABASE testdb   
ON  
( NAME = testdb_dat,  
    FILENAME = 'https://testdb.blob.core.windows.net/data/TestData.mdf' )  
 LOG ON  
( NAME = testdb_log,  
    FILENAME =  'https://testdb.blob.core.windows.net/data/TestLog.ldf')  
```  

>[!IMPORTANT]
>如果有任何作用中參考指向容器中的資料檔案，則嘗試刪除相對應的 SQL Server 認證會失敗。

如需詳細資訊，請參閱 [管理 Azure 儲存體資源的存取](/azure/storage/blobs/storage-manage-access-to-resources)  

### <a name="security"></a>安全性  
 以下是將 SQL Server 資料檔案儲存在 Azure 儲存體中的安全性考量和需求。

- 建立 Azure Blob 儲存體服務的容器時，建議您將存取設為私用。 當您將存取設為私用時，只有 Azure 帳戶擁有者才能讀取容器和 Blob 資料。

- 在 Azure 儲存體中儲存 SQL Server 資料庫檔案時，您必須使用共用存取簽章，這是針對容器、Blob、佇列和資料表授與有限存取權限的 URI。 您可以使用共用存取簽章，讓 SQL Server 存取儲存體帳戶中的資源，而不需要共用您的 Azure 儲存體帳戶金鑰。

- 此外，我們建議您繼續針對資料庫實作傳統的內部部署安全性作法。  
  
### <a name="installation-prerequisites"></a>安裝先決條件  
 以下是將 SQL Server 資料檔案儲存在 Azure 中的安裝必要條件。

- **SQL Server 內部部署：** SQL Server 2016 及更新版本包含這項功能。 若要了解如何下載最新版的 SQL Server，請參閱 [SQL Server](https://www.microsoft.com/sql-server/sql-server-downloads)。

- 在 Azure 虛擬機器中執行的 SQL Server：如果您要將 [SQL Server 安裝在 Azure 虛擬機器上](https://azuremarketplace.microsoft.com/marketplace/apps?search=sql%20server&page=1)，請安裝 SQL Server 2016，或更新現有的執行個體。 同樣地，您也可以使用 SQL Server 2016 平台映像，在 Azure 中建立新的虛擬機器。

  
###  <a name="limitations"></a><a name="bkmk_Limitations"></a> 限制  
  
- 在目前版本的功能中，不支援將 **FileStream** 資料儲存在 Azure 儲存體中。 您可以在同時包含 Azure 儲存體所儲存資料檔案的資料庫中儲存 **FileStream** 資料，但所有的 FileStream 資料檔案都必須儲存在本機儲存體上。  由於 FileStream 資料必須位於本機儲存體上，所以它無法在使用 Azure 儲存體的機器之間移動，因此建議您繼續使用[傳統技術](../../relational-databases/blob/move-a-filestream-enabled-database.md)，在不同電腦之間移動與 FileStream 建立關聯的資料。  
  
- 目前，這項新的增強功能不支援多個 SQL Server 執行個體同時存取 Azure 儲存體中的相同資料庫檔案。 如果 ServerA 在線上且具有作用中的資料庫檔案，而 ServerB 意外啟動，而且也有指向相同資料檔案的資料庫，則第二部伺服器將無法啟動資料庫，錯誤碼為 5120 **無法開啟實體檔案 "%.\*ls"。作業系統錯誤 %d: "%ls"** 。  
  
- 透過使用 Azure 功能中的 SQL Server 資料檔案，僅能將 .mdf、.ldf 和 .ndf 檔案儲存在 Azure 儲存體中。  
  
- 使用 Azure 功能中的 SQL Server 資料檔案時，您的儲存體帳戶不支援異地備援。 如果儲存體帳戶進行異地備援，然後進行異地容錯移轉，可能會發生資料庫損毀。  
  
- 如需容量限制，請參閱 [Blob 儲存體簡介](/azure/storage/blobs/storage-blobs-introduction)。  
  
- 您無法使用 Azure 儲存體功能中的 SQL Server 資料檔案，將記憶體內部 OLTP 資料儲存在 Blob 儲存體中。 這是因為記憶體內部 OLTP 相依於 **FileStream** ，而在目前版本的功能中，不支援將 **FileStream** 資料儲存在 Azure 儲存體中。  
  
- 使用 Azure 功能中的 SQL Server 資料檔案時，SQL Server 會使用 **master** 資料庫中設定的定序執行所有 URL 或檔案路徑比較。  
  
- 只要您不要將新的資料庫檔案新增至主要資料庫，就支援 **AlwaysOn 可用性群組**。 如果資料庫作業需要在主要資料庫中建立新的檔案，請先在次要節點中停用可用性群組。 然後，在主要資料庫上執行資料庫作業，並且備份主要節點中的資料庫。 接著，將資料庫還原到次要節點。 完成之後，請重新啟用次要節點中的 Always On 可用性群組。 

   >[!NOTE]
   >使用 Azure 功能中的 SQL Server 資料檔案時，不支援 Always On 容錯移轉叢集執行個體。
  
- 一般作業期間，SQL Server 會使用暫時租用來保留 Blob 進行儲存，而且每隔 45 至 60 秒就會更新每個 Blob 租用。 如果伺服器當機，而且設定為使用相同 Blob 的另一個 SQL Server 執行個體啟動，新的執行個體最多會等候 60 秒，讓現有的 Blob 租用過期。 如果您想要將資料庫附加至另一個執行個體，而且您無法等候租用在 60 秒內過期，您可以明確釋放 Blob 上的租用。
  
## <a name="tools-and-programming-reference-support"></a>工具和程式設計參考支援  
 本節說明將 SQL Server 資料檔案儲存在 Azure 儲存體中時，可以使用哪些工具和程式設計參考程式庫。  
  
### <a name="powershell-support"></a>PowerShell 支援  
 您可以使用 PowerShell Cmdlet，藉由參考 Blob 儲存體 URL 路徑而非檔案路徑，將 SQL Server 資料檔案儲存在 Blob 儲存體服務中。 使用下列 URL 格式來存取 Blob：`https://storageaccount.blob.core.windows.net/<container>/<blob>`。  
  
### <a name="sql-server-object-and-performance-counters-support"></a>SQL Server 物件和效能計數器支援  
 從 SQL Server 2014 開始，已加入新的 SQL Server 物件，以用於 Azure 儲存體功能中的 SQL Server 資料檔案。 這個新的 SQL Server 物件稱為 [SQL Server, HTTP_STORAGE_OBJECT](../../relational-databases/performance-monitor/sql-server-http-storage-object.md)，而且系統監視器可以在使用 Azure 儲存體執行 SQL Server 時，使用此物件來監視活動。  
  
### <a name="sql-server-management-studio-support"></a>SQL Server Management Studio 支援  
 SQL Server Management Studio 可讓您經由許多對話方塊視窗使用此功能。 例如，`https://teststorageaccnt.blob.core.windows.net/testcontainer/` 代表儲存體容器的 URL 路徑。
 
 作為 **路徑**，例如 [新增資料庫]、[附加資料庫] 和 [還原資料庫]。 如需詳細資訊，請參閱[教學課程：搭配 SQL Server 2016 資料庫使用 Microsoft Azure Blob 儲存體服務](../tutorial-use-azure-blob-storage-service-with-sql-server-2016.md)。  
  
### <a name="sql-server-management-objects-smo-support"></a>SQL Server 管理物件 (SMO) 支援  
 使用 Azure 功能中的 SQL Server 資料檔案時，可支援所有 SQL Server 管理物件 (SMO)。 如果 SMO 物件需要檔案路徑，請使用 BLOB URL 格式而非本機檔案路徑，例如 `https://teststorageaccnt.blob.core.windows.net/testcontainer/`。 如需 SQL Server 管理物件 (SMO) 的詳細資訊，請參閱《SQL Server 線上叢書》中的 [SQL Server 管理物件 &#40;SMO&#41; 程式設計指南](../../relational-databases/server-management-objects-smo/sql-server-management-objects-smo-programming-guide.md)。  
  
### <a name="transact-sql-support"></a>Transact-SQL 支援  
 這項新功能已經在 Transact-SQL 介面區中導入下列變更：

- **sys.master_files** 系統檢視中的新 **int** 資料行： **credential_id** 。 **credential_id** 資料行是用來讓 Azure 儲存體資料檔案能夠交互參考 `sys.credentials`，以便取得針對它們建立的認證。 您可以使用其進行疑難排解，例如某個資料庫檔案正在使用認證，導致無法刪除認證時。  
  
##  <a name="troubleshooting-for-sql-server-data-files-in-microsoft-azure"></a><a name="bkmk_Troubleshooting"></a> Microsoft Azure 中的 SQL Server 資料檔案疑難排解  
 為了避免因為功能不支援或有限制而發生錯誤，請先檢閱＜ [Limitations](../../relational-databases/databases/sql-server-data-files-in-microsoft-azure.md#bkmk_Limitations)＞。  
  
 使用 Azure 儲存體功能中的 SQL Server 資料檔案時，可能會收到的錯誤清單如下。  
  
 **驗證錯誤**  
  
- *無法卸除認證 '%.\*ls'，因為作用中的資料庫檔案正在使用它。*    
    解決方案：當您嘗試卸除的認證仍然由 Azure 儲存體作用中資料庫檔案使用時，就可能會看見此錯誤。 若要卸除認證，您必須先刪除具有此資料庫檔案的相關聯 Blob。 若要刪除擁有使用中租用的 Blob，您必須先釋放租用。  
  
- *尚未在容器上正確建立共用存取簽章。*    
     解決方案：請確定您已經在容器上正確建立共用存取簽章。 請檢閱以下連結中第 2 課所提供的指示：[教學課程：搭配 SQL Server 2016 資料庫使用 Microsoft Azure Blob 儲存體服務](../tutorial-use-azure-blob-storage-service-with-sql-server-2016.md#2---create-a-sql-server-credential-using-a-shared-access-signature)。  
  
- *尚未正確建立 SQL Server 認證。*    
    解決方案：請確定您已經針對 [識別] 欄位使用「共用存取簽章」，並正確建立密碼。 請檢閱以下連結中第 3 課所提供的指示：[教學課程：搭配 SQL Server 2016 資料庫使用 Microsoft Azure Blob 儲存體服務](../tutorial-use-azure-blob-storage-service-with-sql-server-2016.md#3---database-backup-to-url)。  
  
 **租用 Blob 錯誤：**  
  
- 在另一個使用相同 Blob 檔案的執行個體已經當機之後，嘗試啟動 SQL Server 時發生錯誤。 解決方案：一般作業期間，SQL Server 會使用暫時租用來保留 Blob 進行儲存，而且每隔 45 至 60 秒就會更新每個 Blob 租用。 如果伺服器當機，而且設定為使用相同 Blob 的另一個 SQL Server 執行個體啟動，新的執行個體最多會等候 60 秒，讓現有的 Blob 租用過期。 如果您想要將資料庫附加至另一個執行個體，而且您無法等候租用在 60 秒內過期，可以明確釋放 Blob 租用，避免附加作業發生任何失敗。  
  
 **資料庫錯誤**  
  
**建立資料庫時發生錯誤** 的解決方案：請檢閱以下連結中第 4 課所提供的指示：[教學課程：搭配 SQL Server 2016 資料庫使用 Microsoft Azure Blob 儲存體服務](../tutorial-use-azure-blob-storage-service-with-sql-server-2016.md#4----restore-database-to-virtual-machine-from-url)。  
  
**執行 Alter 陳述式時發生錯誤** 解決方式：請務必在資料庫上線時執行 Alter Database 陳述式。 將資料檔案複製到 Azure 儲存體時，一定要建立分頁 Blob 而非區塊 Blob。 否則，ALTER Database 將會失敗。 請檢閱以下連結中第 7 課所提供的指示：[教學課程：搭配 SQL Server 2016 資料庫使用 Microsoft Azure Blob 儲存體服務](../tutorial-use-azure-blob-storage-service-with-sql-server-2016.md)。  
  
**錯誤碼 - 5120 無法開啟實體檔案 "%.\*ls"。作業系統錯誤 %d: "%ls"**   

解決方案：目前，這項新增強功能不支援多個 SQL Server 執行個體同時存取 Azure 儲存體中的相同資料庫檔案。 如果 ServerA 在線上且具有作用中的資料庫檔案，而 ServerB 意外啟動，而且也有指向相同資料檔案的資料庫，則第二部伺服器將無法啟動資料庫，錯誤碼為 5120 *無法開啟實體檔案 "%.\*ls"。作業系統錯誤 %d: "%ls"* 。  
  
若要解決此問題，請先判斷您是否需要讓 ServerA 存取 Azure 儲存體中的資料庫檔案。 如果不需要，移除 ServerA 與 Azure 儲存體中資料庫檔案之間的任何連線即可。 若要這樣做，請遵循下列步驟：  

1.  使用 ALTER Database 陳述式，將 Server A 的檔案路徑設定為本機資料夾。  

2.  將 Server A 中的資料庫設為離線。  

3.  然後，將資料庫檔案從 Azure 儲存體複製到 ServerA 中的本機資料夾。這樣可確保 ServerA 在本機仍然具有資料庫的複本。  

4.  將資料庫設為上線。

**錯誤碼 833 - 完成 I/O 要求需時超過 15 秒** 
   
   此錯誤表示儲存系統無法滿足 SQL Server 工作負載的需求。 請減少應用層的 IO 活動，或增加儲存層的輸送量功能。 若要深入了解，請參閱[錯誤 833](../errors-events/mssqlserver-833-database-engine-error.md)。 如果效能問題持續發生，請考慮將檔案移到其他儲存層，例如 Premium 或 UltraSSD。 如需 Azure VM 上的 SQL Server，請參閱[最佳化儲存體效能](/azure/virtual-machines/premium-storage-performance)。


## <a name="next-steps"></a>後續步驟  
  
[建立資料庫](create-a-database.md)