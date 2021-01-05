---
title: 準備資料庫以進行鏡像
description: 了解如何在 SQL Server 中使用 SQL Server Management Studio 或 Transact-SQL，準備 SQL Server 資料庫以進行資料庫鏡像。
ms.custom: seo-lt-2019
ms.date: 11/10/2017
ms.prod: sql
ms.prod_service: high-availability
ms.reviewer: ''
ms.technology: database-mirroring
ms.topic: conceptual
helpviewer_keywords:
- database mirroring [SQL Server], preparing for mirroring
- logins [SQL Server], database mirroring
- mirror database [SQL Server]
ms.assetid: 8676f9d8-c451-419b-b934-786997d46c2b
author: MikeRayMSFT
ms.author: mikeray
ms.openlocfilehash: 1fa476693286b32cb2910526bb0ea7431faa5120
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97642146"
---
# <a name="prepare-a-mirror-database-for-mirroring-sql-server"></a>準備鏡像資料庫以進行鏡像 (SQL Server)
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
  資料庫擁有者或系統管理員必須確認鏡像資料庫已經建立且做好鏡像的準備，才能啟動資料庫鏡像工作階段。 建立新的鏡像資料庫時，最少需要建立主體資料庫的完整備份，以及一個後續記錄備份，並使用 WITH NORECOVERY 將這兩者同時還原到鏡像伺服器執行個體。  
  
 此主題描述如何使用 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 或 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] ，在 [!INCLUDE[tsql](../../includes/tsql-md.md)]中準備鏡像資料庫。  
  
-   **開始之前：**  
  
     [需求](#Requirements)  
  
     [限制事項](#Restrictions)  
  
     [建議](#Recommendations)  
  
     [安全性](#Security)  
  
-   [若要準備現有的鏡像資料庫以重新啟動鏡像](#PrepareToRestartMirroring)  
  
-   [若要準備新的鏡像資料庫](#CombinedProcedure)  
  
-   **後續操作：** [準備鏡像資料庫之後](#FollowUp)  
  
-   [相關工作](#RelatedTasks)  
  
##  <a name="before-you-begin"></a><a name="BeforeYouBegin"></a> 開始之前  
  
###  <a name="requirements"></a><a name="Requirements"></a> 需求  
  
-   主體和鏡像伺服器執行個體必須在相同的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]版本上執行。 雖然鏡像伺服器有可能擁有較高版本的 SQL Server，但是只有在仔細規劃的升級程序中才建議使用這個組態。 在這種組態中，您會遇到自動容錯移轉的風險，此時資料移動會自動暫停，因為資料無法移到較低版本的 SQL Server。 如需詳細資訊，請參閱 [升級鏡像執行個體](../../database-engine/database-mirroring/upgrading-mirrored-instances.md)。  
  
-   主體和鏡像伺服器執行個體必須在相同的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]版本上執行。 如需 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 中資料庫鏡像支援的資訊，請參閱 [SQL Server 2017 的版本和支援的功能](~/sql-server/editions-and-components-of-sql-server-2017.md)。  
  
-   資料庫必須使用完整復原模式。  
  
     如需詳細資訊，請參閱[檢視或變更資料庫的復原模式 &#40;SQL Server &#41;](../../relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server.md) 或 [sys.databases &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 和 [ALTER DATABASE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-database-transact-sql.md)。  
  
-   鏡像資料庫的名稱必須和主體資料庫的名稱相同。  
  
-   鏡像資料庫必須處於 RESTORING 狀態，鏡像作業才能正常運作。 當準備鏡像資料庫時，您必須使用 RESTORE WITH NORECOVERY 來進行每一項還原作業。 至少需要使用 RESTORE WITH NORECOVERY 還原主體資料庫的完整備份，接著還原所有後續記錄備份。  
  
-   您打算建立鏡像資料庫的系統必須配備具有足夠空間可以保存鏡像資料庫的磁碟機。  
  
###  <a name="limitations-and-restrictions"></a><a name="Restrictions"></a> 限制事項  
  
-   您無法鏡像 **master**、 **msdb**、 **temp** 或 **model** 系統資料庫。  
  
-   您無法針對屬於 [AlwaysOn 可用性群組](../../database-engine/availability-groups/windows/always-on-availability-groups-sql-server.md)的資料庫進行鏡像處理。  
  
###  <a name="recommendations"></a><a name="Recommendations"></a> 建議  
  
-   使用主體資料庫的最近完整資料庫備份或最近差異資料庫備份。  
  
-   如果主體資料庫上排程執行記錄備份作業的頻率很高，您可能必須停用備份作業，直到鏡像啟動為止。  
  
-   如果可行的話，鏡像資料庫的路徑 (包括磁碟機代號) 應該要和主體資料庫的路徑完全相同。  
  
     如果檔案路徑必須不同，例如，如果主體資料庫在磁碟機 'F:'，但鏡像系統沒有 F: 磁碟機，您就必須在 RESTORE 陳述式中包含 MOVE 選項。  
  
    > [!IMPORTANT]  
    >  在鏡像工作階段期間，若要加入檔案但又不影響工作階段，則檔案路徑必須同時存在兩個伺服器上。 因此，如果您在建立鏡像資料庫時移動資料庫檔案，之後在鏡像資料庫上加入檔案的作業可能會失敗，而且導致鏡像暫停。 如需處理失敗之建立檔案作業的相關資訊，請參閱[疑難排解資料庫鏡像組態 &#40;SQL Server&#41;](../../database-engine/database-mirroring/troubleshoot-database-mirroring-configuration-sql-server.md)。  
  
-   如果主體資料庫有任何全文檢索目錄，建議您參閱[資料庫鏡像和全文檢索目錄 &#40;SQL Server&#41;](../../database-engine/database-mirroring/database-mirroring-and-full-text-catalogs-sql-server.md)。  
  
-   對於實際執行的資料庫，您一定要備份至其他裝置。  
  
###  <a name="security"></a><a name="Security"></a> Security  
 備份資料庫時，TRUSTWORTHY 設為 OFF。 因此，新鏡像資料庫上的 TRUSTWORTHY 一律為 OFF。 您必須採取額外的設定步驟，以確保資料庫在容錯移轉之後的可信度。 如需詳細資訊，請參閱 [設定鏡像資料庫以使用 Trustworthy 屬性 &#40;Transact-SQL&#41;](../../database-engine/database-mirroring/set-up-a-mirror-database-to-use-the-trustworthy-property-transact-sql.md)中準備鏡像資料庫。  
  
 如需啟用鏡像資料庫的資料庫主要金鑰之自動解密的相關資訊，請參閱 [設定加密鏡像資料庫](../../database-engine/database-mirroring/set-up-an-encrypted-mirror-database.md)。  
  
####  <a name="permissions"></a><a name="Permissions"></a> 權限  
 資料庫擁有者或系統管理員。  
  
##  <a name="to-prepare-an-existing-mirror-database-to-restart-mirroring"></a><a name="PrepareToRestartMirroring"></a> 若要準備現有的鏡像資料庫以重新啟動鏡像  
 如果鏡像已經移除，而且鏡像資料庫仍處於 RECOVERING 狀態，您就可以重新啟動鏡像。  
  
1.  至少取得主體資料庫上的一個記錄備份。 如需詳細資訊，請參閱 [備份交易記錄 &#40;SQL Server&#41;](../../relational-databases/backup-restore/back-up-a-transaction-log-sql-server.md)資料庫還原至新位置，並選擇性地重新命名資料庫。  
  
2.  在鏡像資料庫上，使用 RESTORE WITH NORECOVERY 來還原自從移除鏡像之後對主體資料庫進行的所有記錄備份。 如需詳細資訊，請參閱 [還原交易記錄備份 &#40;SQL Server&#41;](../../relational-databases/backup-restore/restore-a-transaction-log-backup-sql-server.md)中準備鏡像資料庫。  
  
##  <a name="to-prepare-a-new-mirror-database"></a><a name="CombinedProcedure"></a> 若要準備新的鏡像資料庫  
 **準備鏡像資料庫**  
  
> [!NOTE]  
>  如需這個程序的 [!INCLUDE[tsql](../../includes/tsql-md.md)] 範例，請參閱本節稍後的 [範例 (Transact-SQL)](#TsqlExample)。  
  
1.  連接到主體伺服器執行個體。  
  
2.  建立主體資料庫的完整資料庫備份或差異資料庫備份。  
  
    -   [建立完整資料庫備份 &#40;SQL Server&#41;](../../relational-databases/backup-restore/create-a-full-database-backup-sql-server.md)  
  
    -   [建立差異資料庫備份 &#40;SQL Server&#41;](../../relational-databases/backup-restore/create-a-differential-database-backup-sql-server.md)。  
  
3.  一般來說，您必須至少取得主體資料庫上的一個記錄備份。 不過，如果資料庫剛剛建立，而且尚未建立任何記錄備份，或是如果復原模式剛剛從 SIMPLE 變更為 FULL，可能就不需要有記錄備份。  
  
    -   [備份交易記錄 &#40;SQL Server&#41;](../../relational-databases/backup-restore/back-up-a-transaction-log-sql-server.md)  
  
4.  除非備份位於可從兩個系統存取的網路磁碟機上，否則請將資料庫備份和記錄備份複製到將裝載鏡像伺服器執行個體的系統。  
  
5.  連接到鏡像伺服器執行個體。  
  
6.  使用 RESTORE WITH NORECOVERY，藉由還原完整資料庫備份來建立鏡像資料庫，然後可選擇將最近的差異資料庫備份還原到鏡像伺服器執行個體。  
  
    > [!NOTE]  
    >  如果您是按檔案群組逐一還原資料庫，請務必還原整個資料庫。  
  
    -   [Restore a Database Backup Using SSMS](../../relational-databases/backup-restore/restore-a-database-backup-using-ssms.md)  
  
    -   [RESTORE &#40;Transact-SQL&#41;](../../t-sql/statements/restore-statements-transact-sql.md) 和 [RESTORE 引數 &#40;Transact-SQL&#41;](../../t-sql/statements/restore-statements-arguments-transact-sql.md)中準備鏡像資料庫。  
  
7.  使用 RESTORE WITH NORECOVERY，將任何未完成的記錄備份套用到鏡像資料庫。  
  
    -   [還原交易記錄備份 &#40;SQL Server&#41;](../../relational-databases/backup-restore/restore-a-transaction-log-backup-sql-server.md)  
  
###  <a name="example-transact-sql"></a><a name="TsqlExample"></a> 範例 &#40;Transact-SQL&#41;  
 開始進行資料鏡像工作階段之前，您必須先建立鏡像資料庫。 您應該在開始鏡像工作階段之前完成此動作。  
  
 此範例使用 [!INCLUDE[ssSampleDBobject](../../includes/sssampledbobject-md.md)] 範例資料庫，依預設採用簡單復原模式。  
  
1.  若要以 [!INCLUDE[ssSampleDBobject](../../includes/sssampledbobject-md.md)] 資料庫來使用資料庫鏡像，請將它修改為使用完整復原模式：  
  
    ```  
    USE master;  
    GO  
    ALTER DATABASE AdventureWorks   
    SET RECOVERY FULL;  
    GO  
    ```  
  
2.  將資料庫的復原模式從 SIMPLE 修改為 FULL 之後，請建立完整備份，以便用於建立鏡像資料庫。 因為剛剛才變更復原模式，所以指定了 WITH FORMAT 選項以建立新的媒體集。 要區分在完整復原模式與簡單復原模式建立的備份時，此方式非常有協助。 為了完成這個範例的目的，我們會在資料庫所在的相同磁碟機上建立備份檔案 (`C:\AdventureWorks.bak`)。  
  
    > [!NOTE]  
    >  對於實際執行的資料庫，您必須備份至其他裝置。  
  
     在主體伺服器執行個體 (於 `PARTNERHOST1`) 上，為主體資料庫建立完整備份，陳述式如下：  
  
    ```  
    BACKUP DATABASE AdventureWorks   
        TO DISK = 'C:\AdventureWorks.bak'   
        WITH FORMAT  
    GO  
    ```  
  
3.  將完整備份複製到鏡像伺服器。  
  
4.  使用 RESTORE WITH NORECOVERY，將完整備份還原到鏡像伺服器執行個體上。 還原命令需視主體與鏡像資料庫的路徑是否相同而定。  
  
    -   **若路徑相同：**  
  
         在鏡像伺服器執行個體 (於 `PARTNERHOST5`) 上，還原完整備份，陳述式如下：  
  
        ```  
        RESTORE DATABASE AdventureWorks   
            FROM DISK = 'C:\AdventureWorks.bak'   
            WITH NORECOVERY  
        GO  
        ```  
  
    -   **若路徑不同：**  
  
         若鏡像資料庫的路徑與主體資料庫的路徑不同 (例如，磁碟機代號不同)，則建立鏡像資料庫時，還原作業中必須包含 MOVE 子句。  
  
        > [!IMPORTANT]  
        >  若主體與鏡像資料庫的路徑名稱不同，您將無法新增檔案。 這是因為接收新增檔案的記錄檔時，鏡像伺服器執行個體會嘗試將新檔案放在主體資料庫所使用的位置。  
  
         例如，下列命令會將位於 C:\Program Files\Microsoft SQL Server\MSSQL.*n*\MSSQL\Data\ 之主體資料庫的備份還原到鏡像資料庫所在的不同位置 D:\Program Files\Microsoft SQL Server\MSSQL.*n*\MSSQL\Dat`a\`。  
  
        ```  
        RESTORE DATABASE AdventureWorks  
           FROM DISK='C:\AdventureWorks.bak'  
           WITH NORECOVERY,   
              MOVE 'AdventureWorks_Data' TO   
                 'D:\Program Files\Microsoft SQL Server\MSSQL.n\MSSQL\Data\AdventureWorks_Data.mdf',   
              MOVE 'AdventureWorks_Log' TO  
                 'D:\Program Files\Microsoft SQL Server\MSSQL.n\MSSQL\Data\AdventureWorks_Log.ldf';  
        GO  
        ```  
  
5.  建立完整備份之後，您必須在主體資料庫上建立記錄備份。 例如，下列 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式會將記錄備份至前一次完整備份所使用的相同檔案：  
  
    ```  
    BACKUP LOG AdventureWorks   
        TO DISK = 'C:\AdventureWorks.bak'   
    GO  
    ```  
  
6.  您必須先套用必要的記錄備份 (以及任何後續記錄備份)，才能啟動鏡像。  
  
     例如，下列 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式會從 `C:\AdventureWorks.bak`還原第一筆記錄：  
  
    ```  
    RESTORE LOG AdventureWorks   
        FROM DISK = 'C:\AdventureWorks.bak'   
        WITH FILE=1, NORECOVERY  
    GO  
    ```  
  
7.  如果啟動鏡像之前執行了任何額外的記錄備份，您也必須使用 WITH NORECOVERY，依序將這些記錄備份全部還原到鏡像伺服器。  
  
     例如，下列 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式會從 `C:\AdventureWorks.bak`還原兩個額外的記錄：  
  
    ```  
    RESTORE LOG AdventureWorks   
        FROM DISK = 'C:\AdventureWorks.bak'   
        WITH FILE=2, NORECOVERY  
    GO  
    RESTORE LOG AdventureWorks   
        FROM DISK = 'C:\AdventureWorks.bak'   
        WITH FILE=3, NORECOVERY  
    GO  
    ```  
  
 如需設定資料庫鏡像、顯示安全性設定、準備鏡像資料庫、設定夥伴及新增見證的完整範例，請參閱 [設定資料庫鏡像 &#40;SQL Server&#41;](../../database-engine/database-mirroring/setting-up-database-mirroring-sql-server.md)中準備鏡像資料庫。  
  
##  <a name="follow-up-after-preparing-a-mirror-database"></a><a name="FollowUp"></a> 後續操作：準備鏡像資料庫之後  
  
1.  如果您在最近的 RESTORE LOG 作業之後已經建立任何額外的記錄備份，則必須使用 RESTORE WITH NORECOVERY 手動套用每一份額外的記錄備份。  
  
2.  啟動鏡像工作階段。 如需詳細資訊，請參閱 [使用 Windows 驗證建立資料庫鏡像工作階段 &#40;SQL Server Management Studio&#41;](../../database-engine/database-mirroring/establish-database-mirroring-session-windows-authentication.md) ，在 [使用 Windows 驗證建立資料庫鏡像工作階段 &#40;Transact-SQL&#41;](../../database-engine/database-mirroring/database-mirroring-establish-session-windows-authentication.md)中準備鏡像資料庫。  
  
3.  如果已停用主體資料庫上的備份作業，請重新啟用這項作業。  
  
4.  您必須在鏡像開始之後執行額外的設定步驟，以確保資料庫在容錯移轉之後的可信度。 如需詳細資訊，請參閱 [設定鏡像資料庫以使用 Trustworthy 屬性 &#40;Transact-SQL&#41;](../../database-engine/database-mirroring/set-up-a-mirror-database-to-use-the-trustworthy-property-transact-sql.md)中準備鏡像資料庫。  
  
##  <a name="related-tasks"></a><a name="RelatedTasks"></a> 相關工作  
  
-   [建立完整資料庫備份 &#40;SQL Server&#41;](../../relational-databases/backup-restore/create-a-full-database-backup-sql-server.md)  
  
-   [還原交易記錄備份 &#40;SQL Server&#41;](../../relational-databases/backup-restore/restore-a-transaction-log-backup-sql-server.md)  
  
-   [使用 Windows 驗證建立資料庫鏡像工作階段 &#40;SQL Server Management Studio&#41;](../../database-engine/database-mirroring/establish-database-mirroring-session-windows-authentication.md)  
  
-   [使用 Windows 驗證建立資料庫鏡像工作階段 &#40;Transact-SQL&#41;](../../database-engine/database-mirroring/database-mirroring-establish-session-windows-authentication.md)  
  
-   [設定加密鏡像資料庫](../../database-engine/database-mirroring/set-up-an-encrypted-mirror-database.md)  
  
-   [設定鏡像資料庫以使用 Trustworthy 屬性 &#40;Transact-SQL&#41;](../../database-engine/database-mirroring/set-up-a-mirror-database-to-use-the-trustworthy-property-transact-sql.md)  
  
## <a name="see-also"></a>另請參閱  
 [資料庫鏡像 &#40;SQL Server&#41;](../../database-engine/database-mirroring/database-mirroring-sql-server.md)   
 [資料庫鏡像和 Always On 可用性群組的傳輸安全性 &#40;SQL Server&#41;](../../database-engine/database-mirroring/transport-security-database-mirroring-always-on-availability.md)   
 [設定資料庫鏡像 &#40;SQL Server&#41;](../../database-engine/database-mirroring/setting-up-database-mirroring-sql-server.md)   
 [備份並還原全文檢索目錄與索引。](../../relational-databases/search/back-up-and-restore-full-text-catalogs-and-indexes.md)   
 [資料庫鏡像和全文檢索目錄 &#40;SQL Server&#41;](../../database-engine/database-mirroring/database-mirroring-and-full-text-catalogs-sql-server.md)   
 [資料庫鏡像和複寫 &#40;SQL Server&#41;](../../database-engine/database-mirroring/database-mirroring-and-replication-sql-server.md)   
 [BACKUP &#40;Transact-SQL&#41;](../../t-sql/statements/backup-transact-sql.md)   
 [RESTORE &#40;Transact-SQL&#41;](../../t-sql/statements/restore-statements-transact-sql.md)   
 [RESTORE 引數 &#40;Transact-SQL&#41;](../../t-sql/statements/restore-statements-arguments-transact-sql.md)  
  
  

