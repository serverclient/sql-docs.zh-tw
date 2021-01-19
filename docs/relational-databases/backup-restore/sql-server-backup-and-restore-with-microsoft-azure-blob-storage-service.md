---
description: 使用 Microsoft Azure Blob 儲存體服務進行 SQL Server 備份及還原
title: 使用 Azure Blob 儲存體備份及還原
storage: Learn about SQL Server backup to and restore from Azure Blob storage, including the benefits of using Azure Blob storage to store SQL Server backups.
ms.custom: seo-lt-2019
ms.date: 12/17/2019
ms.prod: sql
ms.prod_service: backup-restore
ms.reviewer: ''
ms.technology: backup-restore
ms.topic: conceptual
ms.assetid: 6a0c9b6a-cf71-4311-82f2-12c445f63935
author: cawrites
ms.author: chadam
ms.openlocfilehash: e1dcbea42043d1077dd2b0c9e0f1127c9319842b
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98170740"
---
# <a name="sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service"></a>使用 Microsoft Azure Blob 儲存體服務進行 SQL Server 備份及還原
[!INCLUDE [SQL Server SQL MI](../../includes/applies-to-version/sql-asdbmi.md)]

  ![備份至 Azure Blob 儲存體的圖形](../../relational-databases/backup-restore/media/backup-to-azure-blob-graphic.png "備份至 Azure Blob 儲存體的圖形")  
  
 本主題說明 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 如何備份到 [Microsoft Azure BLOB 儲存體服務](https://www.windowsazure.com/develop/net/how-to-guides/blob-storage/)，以及如何從該服務進行還原。 本主題也會提供使用 Microsoft Azure Blob 服務來儲存 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 備份的優點摘要。  
  
 SQL Server 可以下列方式將備份儲存到 Microsoft Azure BLOB 儲存體服務：  
  
-   **管理 Microsoft Azure 的備份：** 現在，您可使用備份到磁碟和磁帶的相同方式，指定 URL 作為備份目的地以備份至 Microsoft Azure 儲存體。 一如您對於本機儲存體或其他異地選項的處理方式，您可以使用此功能手動備份或設定您自己的備份策略。 此功能又稱為 **SQL Server 備份至 URL**。 如需詳細資訊，請參閱 [SQL Server Backup to URL](../../relational-databases/backup-restore/sql-server-backup-to-url.md)。 SQL Server 2012 SP1 CU2 或更新版本皆提供此功能。 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 已增強這項功能，透過使用區塊 Blob、共用存取簽章和等量配置，提升效能和功能。  
  
    > [!NOTE]  
    >  若是使用 SQL Server 2012 SP1 CU2 之前的 SQL Server 版本，則可使用 Microsoft Azure 工具的 SQL Server 備份增益集，快速而輕鬆地建立要儲存到 Microsoft Azure 儲存體的備份。 如需詳細資訊，請參閱＜ [下載中心](https://go.microsoft.com/fwlink/?LinkID=324399)＞。  
  
-   **Azure Blob 儲存體中資料庫檔案的檔案快照集備份** ：透過使用 Azure 快照集， [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 檔案快照集備份可針對使用 Azure Blob 儲存體服務儲存的資料庫檔案，提供幾乎即時的備份和還原。 這個功能可讓您簡化備份和還原原則，並支援還原時間點。 如需詳細資訊，請參閱 [Azure 中資料庫檔案的檔案快照集備份](../../relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure.md)。 SQL Server 2016 或更新版本皆提供此功能。  
  
-   **由 SQL Server 管理 Microsoft Azure 的備份：** 設定由 SQL Server 管理單一資料庫或多個資料庫的備份策略及排程備份，或在執行個體層級設定預設值。 此功能又稱為 **[!INCLUDE[ss_smartbackup](../../includes/ss-smartbackup-md.md)]** 。 如需詳細資訊，請參閱 [SQL Server Managed Backup to Microsoft Azure](../../relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure.md)。 SQL Server 2014 或更新版本皆提供此功能。  
  
## <a name="benefits-of-using-the-microsoft-azure-blob-service-for-ssnoversion-backups"></a>使用 Microsoft Azure Blob 服務進行 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 備份的優點  
  
-   彈性、可靠且沒有限制的異地儲存體：將您的備份儲存在 Microsoft Azure Blob 服務上是一種方便、彈性且容易存取的異地選項。 針對您的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 備份建立異地儲存體就像修改現有的指令碼/作業一樣簡單。 異地儲存體通常應該遠離實際執行資料庫位置，防止單一災害同時影響異地和實際執行資料庫位置。 透過選擇異地備援 Blob 儲存體，您就可以在發生可能影響整個地區的災害時，獲得一層額外的保護。 此外，您可以隨時隨地取得備份，輕鬆地針對還原作業進行存取。  
  
    > [!IMPORTANT]  
    >  透過使用 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)]中的區塊 Blob，您可以將備份組等量配置以支援高達 12.8 TB 的備份檔案大小。  
  
-   備份封存：Microsoft Azure Blob 儲存體服務提供了較佳的替代方案，有效取代封存備份的常用磁帶選項。 磁帶儲存體可能需要實際運輸至異地設施並採取措施來保護媒體。 將您的備份儲存在 Microsoft Azure Blob 儲存體中，即可提供立即、高可用性且耐用的封存選項。  
  
-   沒有硬體管理的負擔：Microsoft Azure 服務不會產生任何硬體管理的負擔。 Microsoft Azure 服務會管理硬體並提供異地複寫，以進行備援，防止硬體故障。  
  
-   目前，對於在 Microsoft Azure 虛擬機器中執行的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體而言，備份至 Microsoft Azure Blob 儲存體服務可以透過建立附加磁碟來完成。 不過，您可以附加至 Microsoft Azure 虛擬機器的磁碟數目有所限制。 超大執行個體的限制為 16 個磁碟，而較小執行個體的限制則更低。 只要啟用直接備份至 Microsoft Azure Blob 儲存體的功能，您就可以忽略 16 個磁碟的限制。  
  
     此外，目前儲存在 Microsoft Azure Blob 儲存體服務中的備份檔案可以直接提供給內部部署 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ，或在 Microsoft Azure 虛擬機器中執行的其他 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 使用，完全不需要進行資料庫附加/卸離，或是下載並附加 VHD。  
  
-   成本效益：只有使用的服務才要付費。 成為符合成本效益的異地與備份封存選項。 如需詳細資訊和連結，請參閱 [Microsoft Azure 計費考量](#Billing) 一節。  
  
##  <a name="microsoft-azure-billing-considerations"></a><a name="Billing"></a> Microsoft Azure 計費考量：  
 了解 Microsoft Azure 儲存體成本可讓您預測在 Microsoft Azure 中建立和儲存備份的成本。  
  
 [Microsoft Azure 定價計算機](https://go.microsoft.com/fwlink/?LinkId=277060) 可以協助您預估成本。  
  
 **儲存體** ：費用是根據使用的空間收費，而計算方式採累進費率和備援等級計算。 如需詳細資料與最新資訊，請參閱 **定價詳細資料** 文章的 [資料管理](https://go.microsoft.com/fwlink/?LinkId=277059) 一節。  
  
 **資料傳輸** ：輸入 Microsoft Azure 的資料傳輸是免費的。 輸出傳輸則必須支付頻寬使用量的費用，計算方式是根據地區特定的累進費率計算。 如需詳細資料，請參閱＜定價詳細資料＞文章的 [資料傳輸](https://go.microsoft.com/fwlink/?LinkId=277061) 一節。  
  
## <a name="see-also"></a>另請參閱  

[SQL Server 備份至 URL 的最佳做法和疑難排解](../../relational-databases/backup-restore/sql-server-backup-to-url-best-practices-and-troubleshooting.md)   

[系統資料庫的備份與還原 &#40;SQL Server&#41;](../../relational-databases/backup-restore/back-up-and-restore-of-system-databases-sql-server.md)   

[教學課程：搭配使用 Microsoft Azure Blob 儲存體服務和 SQL Server 2016 資料庫](../tutorial-use-azure-blob-storage-service-with-sql-server-2016.md)

[SQL Server 備份至 URL](../../relational-databases/backup-restore/sql-server-backup-to-url.md)  
  
  
