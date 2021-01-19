---
title: Azure 中資料庫檔案的檔案快照集備份 | Microsoft Docs
description: SQL Server 檔案快照集備份使用 Azure 快照集來為使用 Azure Blob 儲存體服務所儲存資料庫檔案提供快速備份及更快速的還原。
ms.custom: ''
ms.date: 05/23/2016
ms.prod: sql
ms.prod_service: backup-restore
ms.reviewer: ''
ms.technology: backup-restore
ms.topic: conceptual
ms.assetid: 17a81fcd-8dbd-458d-a9c7-2b5209062f45
author: cawrites
ms.author: chadam
ms.openlocfilehash: 63e2f722305c2af448be7d49868eef72d1a9fb8d
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98172690"
---
# <a name="file-snapshot-backups-for-database-files-in-azure"></a>Azure 中資料庫檔案的檔案快照集備份
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 檔案快照集備份使用 Azure 快照集為使用 Azure Blob 儲存體服務儲存的資料庫檔案，提供近乎即時的備份及更快速的還原。 這項功能可簡化備份和還原原則。 如需即時的示範，請參閱 [File-Snapshot Backups Demo (檔案快照集備份示範)](https://channel9.msdn.com/Blogs/Windows-Azure/File-Snapshot-Backups-Demo)。 如需使用 Azure Blog 儲存體服務儲存資料庫檔案的詳細資訊，請參閱 [Microsoft Azure 中的 SQL Server 資料檔案](../../relational-databases/databases/sql-server-data-files-in-microsoft-azure.md)。  
  
 ![快照集備份架構圖](../../relational-databases/backup-restore/media/snapshotbackups.PNG "快照集備份架構圖")  
  
 **下載**  
  
-   若要下載 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)]，請前往  **[Evaluation Center](https://www.microsoft.com/evalcenter/evaluate-sql-server-2016)** 。  
  
-   有 Azure 帳戶嗎？  接著前往 **[這裡](https://azure.microsoft.com/services/virtual-machines/sql-server/)** ，來加速已安裝 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 的虛擬機器。  
  
## <a name="using-azure-snapshots-to-back-up-database-files-stored-in-azure"></a>使用 Azure 快照集備份儲存在 Azure 的資料庫檔案  
  
### <a name="what-is-a-ssnoversion-file-snapshot-backup"></a>什麼是 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 檔案快照集備份  
 檔案快照集備份是由一組 Blob 的 Azure 快照集組成，其中包含資料庫檔案，以及包含指向這些檔案快照集指標的備份檔案。 每個檔案快照集都以基底 Blob 儲存在容器中。 您可以指定備份檔案本身要寫入至 URL、磁碟機或磁帶。 建議備份至 URL。 如需備份的詳細資訊，請參閱 [BACKUP &#40;Transact-SQL&#41;](../../t-sql/statements/backup-transact-sql.md)；如需備份至 URL 的詳細資訊，請參閱 [SQL Server 備份至 URL](../../relational-databases/backup-restore/sql-server-backup-to-url.md)。  
  
 ![快照集功能架構](../../relational-databases/backup-restore/media/snapshotbackups-flat.png "快照集功能架構")  
  
 刪除 Blob 會使備份組無效，且您無法卸除包含檔案快照集的 Blob (除非您明確地要刪除 Blob 連同其所有檔案快照集)。 此外，卸除資料庫或資料檔案不會刪除基底 Blob 或其檔案快照集。 同樣地，刪除備份檔案不會刪除備份組中的任何檔案快照集。 若要刪除檔案快照集備份組，請使用 **sys.sp_delete_backup** 系統預存程序。  
  
 **完整資料庫備份：** 使用檔案快照集備份執行完整資料庫備份，會建立構成資料庫所有資料和記錄檔的 Azure 快照集，並會建立交易記錄備份鏈結，將檔案快照集的位置寫入備份檔案。  
  
 **交易記錄備份：** 使用檔案快照集備份執行交易記錄備份，會建立每個資料庫檔案的檔案快照集 (不只是交易記錄)，並將檔案快照集的位置資訊記錄到備份檔案中，且截斷交易記錄檔案。  
  
> [!IMPORTANT]  
>  完成建立交易記錄備份鏈結所需的初始完整備份 (可能是檔案快照集備份) 之後，您就只需要執行交易記錄備份備份，因為每個交易記錄檔案快照集備份組，包含所有資料庫檔案的檔案快照集，並可用來執行資料庫還原或記錄還原。 初始完整備份之後，您就不需要其他完整或差異備份，因為 Azure Blob 儲存體服務會處理每個檔案快照集之間的差異，以及每個資料庫檔案目前基底 Blob 的狀態。  
  
> [!NOTE]  
>  如需搭配使用 SQL Server 2016 和 Microsoft Azure Blob 儲存體服務的教學課程，請參閱[教學課程：搭配使用 Microsoft Azure Blob 儲存體服務和 SQL Server 2016 資料庫](../tutorial-use-azure-blob-storage-service-with-sql-server-2016.md)  
  
### <a name="restore-using-file-snapshot-backups"></a>使用檔案快照集還原  
 因為每個檔案快照集備份組，都包含每個資料庫檔案的檔案快照集，而還原程序需要最近的兩組檔案快照集備份組。 不論備份組是來自完整資料庫備份或記錄備份都是如此。 這和使用傳統資料流備份檔案來執行還原的程序非常不同。 透過傳統的資料流備份，還原成需需要使用備份組的整個鏈結：完整備份、差異備份，及一或多個交易記錄備份。 不論是使用檔案快照集備份或資料流備份組來還原，還原程序的復原部分仍維持相同。  
  
 **至任何備份組的時間：** 若要執行 RESTORE DATABASE 作業來將資料庫還原到特定檔案快照集備份組的時間，只需要特定備份組及基底 Blob 本身。 因為您可以使用交易記錄檔案快照集備份組來執行 RESTORE DATABASE 作業，您通常會使用交易記錄備份組來執行此類型的 RESTORE DATABASE 作業，而很少使用完整資料庫備份組。 本文最後有示範此技術的範例。  
  
 **至兩個檔案快照集備份組之間的時間點：** 若要執行 RESTORE DATABASE 作業將資料庫還原至相鄰兩個交易記錄備份組之間的特定時間點，只需要兩個交易記錄備份組 (您希望還原資料庫時間點的前後各一組)。 若要這麼做，您需要使用較早時間點的交易記錄檔案快照集備份組，來執行 RESTORE DATABASE 作業 WITH NORECOVERY，並使用較晚時間點的交易記錄檔案快照集備份組，來執行來執行 RESTORE LOG 作業 WITH RECOVERY，並使用 STOPAT 引數來指定從交易記錄備份復原要停止的時間點。 本文最後有示範此技術的範例。 如需即時的示範，請參閱 [File-Snapshot Backups Demo (檔案快照集備份示範)](https://channel9.msdn.com/Blogs/Windows-Azure/File-Snapshot-Backups-Demo)。  
  
### <a name="file-backup-set-maintenance"></a>維護檔案備份組  
 **刪除檔案快照集備份組：** 您無法使用 FORMAT 引數覆寫檔案快照集備份組。 不允許使用 FORMAT 引數是為了避免留下孤立的檔案快照集 (使用原始檔案快集照備份建立)。 若要刪除檔案快照集備份組，請使用 **sys.sp_delete_backup** 系統預存程序。 這個預存程序會刪除備份檔案和構成備份組的檔案快照集。 使用其他方法刪除檔案快照集備份組，可能會刪除備份檔案卻沒有刪除備份組中的檔案快照集。  
  
 **刪除孤立的備份檔案快照集：** 如果備份檔案不是使用 **sys.sp_delete_backup** 系統預存程序刪除，或如果卸除資料庫或資料庫檔案時，Blob 仍包含與該資料庫或資料庫檔案建立關聯的備份檔案快照集，皆可能會產生孤立的檔案快照集。 若要識別可能的孤立檔案快照集，請使用 **sys.fn_db_backup_file_snapshots** 系統函數來列出所有資料庫檔案的檔案快照集。 若要識別屬於特定檔案快照集備份組之一部分的檔案快照集，請使用 RESTORE FILELISTONLY 系統預存程序。 接著您可以使用 **sys.sp_delete_backup_file_snapshot** 系統預存程序來刪除被孤立的個別備份檔案快照集。 在本文最後有使用這個系統函式和這些系統預存程序的範例。 如需詳細資訊，請參閱 [sp_delete_backup &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/snapshot-backup-sp-delete-backup.md)、[sys.fn_db_backup_file_snapshots &#40;Transact-SQL&#41;](../../relational-databases/system-functions/sys-fn-db-backup-file-snapshots-transact-sql.md)、[sp_delete_backup_file_snapshot &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/snapshot-backup-sp-delete-backup-file-snapshot.md) 和 [RESTORE FILELISTONLY &#40;Transact-SQL&#41;](../../t-sql/statements/restore-statements-filelistonly-transact-sql.md)。  
  
### <a name="considerations-and-limitations"></a>考量與限制  
 **進階儲存體：** 使用進階儲存體時，會適用下列限制：  
  
-   備份檔案本身無法使用進階儲存體儲存。  
  
-   備份的頻率不能小於 10 分鐘。  
  
-   快照集可儲存的上限數量為 100 個。  
  
-   RESTORE WITH MOVE 是必要的。  
  
-   如需進階儲存體的額外資訊，請參閱[進階儲存體：適用於 Azure 虛擬機器工作負載的高效能儲存體](/azure/virtual-machines/disks-types)  
  
 **單一儲存體帳戶：** 檔案快照集和目的地 Blob 必須使用相同的儲存體帳戶。  
  
 **大量復原模式：** 使用大量記錄復原模式，且使用包含最小量交易記錄的交易記錄備份時，您無法使用交易記錄備份執行記錄還原 (包含時間點復原)。 相反地，您需要執行還原至檔案快照集備份組之時間的資料庫還原。 此限制與資料流備份的限制相同。  
  
 **線上還原：** 使用快照集備份時，您無法執行「線上還原」。 如需「線上還原」的詳細資訊，請參閱[線上還原 &#40;SQL Server&#41;](../../relational-databases/backup-restore/online-restore-sql-server.md)。  
  
 **計費：** 使用 SQL Server 檔案快照集備份時，會隨著資料變更產生額外費用。 如需詳細資訊，請參閱 [Understanding How Snapshots Accrue Charges](/rest/api/storageservices/Understanding-How-Snapshots-Accrue-Charges)(了解快照集的計費方式)。  
  
 **封存：** 若您希望封存檔案快照集備份，您可以封存到 Blob 儲存體或串流備份。 若要封存至 Blob 儲存體，請將檔案快照集備份中的快照集複製到其他 Blob。 若要封存至資料流備份，請將資料庫檔案快照集備份還原為新的資料庫，然後執行壓縮和/或加密的標準串流備份，並且獨於基底 Blob 之外，視所需的期限封存它。  
  
> [!IMPORTANT]  
>  維護多個檔案快照集備份只有少許的效能負擔。 不過，維護數量過多的檔案快照集備份可能會影響資料庫的 I/O 效能。 我們建議您只維護支援您的復原點目標的檔案快照集備份。  
  
## <a name="backing-up-the-database-and-log-using-a-file-snapshot-backup"></a>使用檔案快照集備份來備份資料庫和記錄檔  
 以下範例使用檔案快照集備份來將 AdventureWorks2016 範例資料庫備份至 URL。  
  
```  
-- To permit log backups, before the full database backup, modify the database   
-- to use the full recovery model.  
USE master;  
GO  
ALTER DATABASE AdventureWorks2016  
   SET RECOVERY FULL;  
GO  
-- Back up the full AdventureWorks2016 database.  
BACKUP DATABASE AdventureWorks2016   
TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/AdventureWorks2016.bak'   
WITH FILE_SNAPSHOT;  
GO  
-- Back up the AdventureWorks2016 log using a time stamp in the backup file name.  
DECLARE @Log_Filename AS VARCHAR (300);  
SET @Log_Filename = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/AdventureWorks2016_Log_'+   
REPLACE (REPLACE (REPLACE (CONVERT (VARCHAR (40), GETDATE (), 120), '-','_'),':', '_'),' ', '_') + '.trn';  
BACKUP LOG AdventureWorks2016  
 TO URL = @Log_Filename WITH FILE_SNAPSHOT;  
GO  
```  
  
## <a name="restoring-from-a-ssnoversion-file-snapshot-backup"></a>從 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 檔案快照集備份還原  
 以下範例使用交易記錄檔案快照集備份組還原 AdventureWorks2016 資料庫，並顯示還原作業。 請注意，您可以從單一的交易記錄檔案快照集備份組還原。  
  
```  
RESTORE DATABASE AdventureWorks2016 FROM URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/AdventureWorks2016_2015_05_18_16_00_00.trn'   
WITH RECOVERY, REPLACE;  
GO  
```  
  
## <a name="restoring-from-a-ssnoversion-file-snapshot-backup-to-a-point-in-time"></a>從 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 檔案快照集備份還原到時間點  
 以下範例使用兩個交易記錄檔案快照集備份組來將 AdventureWorks2016 資料庫還原至其特定時間點的狀態，並顯示還原作業。  
  
```  
RESTORE DATABASE AdventureWorks2016 FROM URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/AdventureWorks2016_2015_05_18_16_00_00.trn'   
WITH NORECOVERY,REPLACE;  
GO   
  
RESTORE LOG AdventureWorks2016 FROM URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/AdventureWorks2016_2015_05_18_18_00_00.trn'   
WITH RECOVERY,STOPAT = 'May 18, 2015 5:35 PM';  
GO  
```  
  
## <a name="deleting-a-database-file-snapshot-backup-set"></a>刪除資料庫檔案快照集備份組  
 若要刪除檔案快照集備份組，請使用 **sys.sp_delete_backup** 系統預存程序。 指定資料庫名稱，以便系統確認指定的檔案快照集備份組確實是指定之資料庫的備份。 若未指定資料庫名稱，則系統會不經驗證就會刪除所指定的備份組及其檔案快照集。 如需詳細資訊，請參閱 [sp_delete_backup &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/snapshot-backup-sp-delete-backup.md)。  
  
> [!WARNING]  
>  嘗試使用其他方法 (如 Microsoft Azure 管理入口網站或 SQL Server Management Studio 中的 Azure Storage Viewer) 刪除檔案快照集備份組，則備份組中的檔案快照集將不會被刪除。 這些工具只會刪除備份檔案本身，其中包含指向檔案快照集備份組中檔案快照集。 若要識別不當刪除備份檔案之後留下的備份檔案快照集，請使用 **sys.fn_db_backup_file_snapshots** 系統函數，然後使用 **sys.sp_delete_backup_file_snapshot** 系統預存程序來刪除個別備份檔案快照集。  
  
 以下範例會刪除指定的檔案快照集備份組，包括備份檔案和組成指定之備份組的檔案快照集。  
  
```  
sys.sp_delete_backup 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/AdventureWorks2016.bak', 'adventureworks2016' ;  
GO  
  
```  
  
## <a name="viewing-database-backup-file-snapshots"></a>檢視資料庫備份檔案快照集  
 若要檢視每個資料庫檔案之基底 Blob 的檔案快照集，請使用 **sys.fn_db_backup_file_snapshots** 系統函數。 這個系統函數可讓您檢視以 Azure Blob 儲存體服務來儲存的資料庫之每個基底 Blob 的所有備份檔案快照集。 這個函式的主要使用案例是要識別，當檔案快照集備份組的備份檔案是以 **sys.sp_delete_backup** 系統預存程序以外的機制刪除時，所留下的資料庫備份檔案快照集。 若要判斷備份檔案快照集是否屬於完整備份組之一部分，請使用 **RESTORE FILELISTONLY**  系統預存程序來列出屬於每個備份檔案的檔案快照集。 如需詳細資訊，請參閱 [sys.fn_db_backup_file_snapshots &#40;Transact-SQL&#41;](../../relational-databases/system-functions/sys-fn-db-backup-file-snapshots-transact-sql.md) 和 [RESTORE FILELISTONLY &#40;Transact-SQL&#41;](../../t-sql/statements/restore-statements-filelistonly-transact-sql.md)。  
  
 以下範例會傳回指定之資料庫的所有備份檔案快照集。  
  
```  
--Either specify the database name or set the database context  
USE AdventureWorks2016  
select * from sys.fn_db_backup_file_snapshots (null) ;  
GO  
select * from sys.fn_db_backup_file_snapshots ('AdventureWorks2016') ;  
GO  
  
```  
  
## <a name="deleting-an-individual-database-backup-file-snapshot"></a>刪除個別的資料庫備份檔案快照集  
 若要刪除資料庫基底 Blob 的個別備份檔案快照集，請使用 **sys.sp_delete_backup_file_snapshot** 系統預存程序。 這個系統預存程序的主要使用案例是要刪除以 **sys.sp_delete_backup** 系統預存程序之外的其他方法來刪除備份檔案之後，所留下的孤立檔案快照集檔案。 如需詳細資訊，請參閱 [sp_delete_backup_file_snapshot &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/snapshot-backup-sp-delete-backup-file-snapshot.md)。  
  
> [!WARNING]  
>  刪除屬於檔案快照集備份組之一部分的檔案快照集會使該備份組無效。  
  
 以下範例會刪除指定的備份檔案快照集。 指定之備份 URL 是使用 **sys.fn_db_backup_file_snapshots** 系統函數取得。  
  
```  
sys.sp_delete_backup_file_snapshot N'adventureworks2016', N'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/AdventureWorks2016Data.mdf?snapshot=2015-05-29T21:31:31.6502195Z';  
GO  
```  
  
## <a name="see-also"></a>另請參閱  
 [教學課程：搭配使用 Microsoft Azure Blob 儲存體服務和 SQL Server 2016 資料庫](../tutorial-use-azure-blob-storage-service-with-sql-server-2016.md)  
  
