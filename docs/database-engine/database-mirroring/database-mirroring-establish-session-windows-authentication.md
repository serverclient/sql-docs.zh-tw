---
title: 設定資料庫鏡像
description: 使用 Windows 驗證在主體和鏡像之間設定資料庫鏡像關聯性的步驟。
ms.custom: seo-lt-2019
ms.date: 05/17/2016
ms.prod: sql
ms.prod_service: high-availability
ms.reviewer: ''
ms.technology: database-mirroring
ms.topic: conceptual
helpviewer_keywords:
- Windows authentication [SQL Server]
- database mirroring [SQL Server], security
ms.assetid: 143c68a5-589f-4e7f-be59-02707e1a430a
author: MikeRayMSFT
ms.author: mikeray
ms.openlocfilehash: 7584e4611d957f48efcb73d12b5965f0a4727b45
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97642018"
---
# <a name="configure-database-mirroring"></a>設定資料庫鏡像
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
    
> [!NOTE]  
>  [!INCLUDE[ssNoteDepFutureAvoid](../../includes/ssnotedepfutureavoid-md.md)] 請改用 [!INCLUDE[ssHADR](../../includes/sshadr-md.md)]。  
  
 準備好鏡像資料庫之後 (請參閱 [準備鏡像資料庫以進行鏡像 &#40;SQL Server&#41;](../../database-engine/database-mirroring/prepare-a-mirror-database-for-mirroring-sql-server.md))，就可以建立資料庫鏡像工作階段。 主體、鏡像及見證伺服器執行個體必須是個別的伺服器執行個體，且應位於個別的主機系統上。  
  
> [!IMPORTANT]
>  我們建議您將資料庫鏡像作業排定在離峰時間執行，因為設定鏡像會影響效能。  
> 
> [!NOTE]
>  給定的伺服器執行個體可參與具有相同或不同夥伴的多個並行資料庫鏡像工作階段。 伺服器執行個體可以在某些工作階段中是夥伴，在其他工作階段中是見證。 鏡像伺服器執行個體必須執行與主體伺服器執行個體相同的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 版本。 並非所有 [!INCLUDE[msCoName](../../includes/msconame-md.md)][!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]。 如需 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]版本支援的功能清單，請參閱 [SQL Server 2016 版本支援的功能](~/sql-server/editions-and-supported-features-for-sql-server-2016.md)。 此外，我們強烈建議您在可比較而且可以處理相同工作負載的系統上執行這些伺服器執行個體。  
  
### <a name="to-establish-a-database-mirroring-session"></a>建立資料庫鏡像工作階段  
  
1.  建立鏡像資料庫。 如需詳細資訊，請參閱 [準備鏡像資料庫以進行鏡像 &#40;SQL Server&#41;](../../database-engine/database-mirroring/prepare-a-mirror-database-for-mirroring-sql-server.md)。  
  
2.  設定每個伺服器執行個體的安全性。  
  
     資料庫鏡像工作階段中的每個伺服器執行個體都需要一個資料庫鏡像端點。 如果端點不存在，您就必須自行建立。  
  
    > [!NOTE]  
    >  伺服器執行個體用於資料庫鏡像的驗證格式，是其資料庫鏡像端點的屬性。 有兩種傳輸安全性可用於資料庫鏡像：Windows 驗證或以憑證為基礎的驗證。 如需詳細資訊，請參閱[資料庫鏡像和 AlwaysOn 可用性群組的傳輸安全性 &#40;SQL Server&#41;](../../database-engine/database-mirroring/transport-security-database-mirroring-always-on-availability.md)。  
  
     確定在每個夥伴伺服器上，資料庫鏡像都有端點可供使用。 不論要支援的鏡像工作階段數有多少，伺服器執行個體只能有一個資料庫鏡像端點。 若要讓資料庫鏡像工作階段的夥伴獨佔使用此伺服器執行個體，您可以將夥伴的角色指派給端點 (ROLE **=** PARTNER)。 如果您也想讓其他資料庫鏡像工作階段的見證使用此伺服器，請將端點的角色指派為 ALL。  
  
     若要執行 SET PARTNER 陳述式，兩個夥伴的端點狀態 (STATE) 必須都設為 STARTED。  
  
     若要了解伺服器執行個體是否有資料庫鏡像端點，以及它在該執行個體上的角色與狀態，請使用下列 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式：  
  
    ```  
    SELECT role_desc, state_desc FROM sys.database_mirroring_endpoints  
    ```  
  
    > [!IMPORTANT]  
    >  請不要重新設定使用中資料庫鏡像端點。 若資料庫鏡像端點存在且已在使用中，我們建議您在該伺服器執行個體上為每個工作階段使用該端點。 卸除使用中端點可能會導致端點重新啟動，並中斷現有工作階段的連接，而這對於其他伺服器執行個體可能會是一項錯誤。 在具有自動容錯移轉的高安全性模式中，這點尤其重要，因為在這種模式中重新設定夥伴上的端點，可能會導致發生容錯移轉。 此外，如果已針對工作階段設定見證，則卸除資料庫鏡像端點可能會導致該工作階段的主體伺服器失去仲裁。若發生此情況，則資料庫會離線，並中斷與資料庫使用者的的連線。 如需詳細資訊，請參閱[仲裁：見證如何影響資料庫可用性 &#40;資料庫鏡像&#41;](../../database-engine/database-mirroring/quorum-how-a-witness-affects-database-availability-database-mirroring.md)。  
  
     如果任一夥伴缺少端點，請參閱[建立 Windows 驗證的資料庫鏡像端點 &#40;Transact-SQL&#41;](../../database-engine/database-mirroring/create-a-database-mirroring-endpoint-for-windows-authentication-transact-sql.md)。  
  
3.  如果伺服器執行個體正在不同的網域使用者帳戶下執行，每個執行個體都會需要登入其他執行個體的 **master** 資料庫。 如果登入不存在，您就必須自行建立。 如需詳細資訊，請參閱 [使用 Windows 驗證允許資料庫鏡像的網路存取 &#40;SQL Server&#41;](../../database-engine/database-mirroring/database-mirroring-allow-network-access-windows-authentication.md)。  
  
4.  若要設定主體伺服器做為鏡像資料庫上的夥伴，請連接到該鏡像伺服器，並執行以下陳述式：  
  
     ALTER DATABASE *\<database_name\>* SET PARTNER **=** _\<server\_network\_address\>_  
  
     其中 _\<database\_name\>_ 是要鏡像的資料庫名稱 (此名稱在兩個合作夥伴中都相同)，而 _\<server\_network\_address\>_ 是主體伺服器的伺服器網路位址。  
  
     伺服器網路位址的語法如下：  
  
     TCP <b>\://</b> _\<system-address\>_ <b>\:</b> _\<port\>_  
  
     其中 _\<system-address>_ 是清楚識別目的地電腦系統的字串，而 _\<port>_ 是合作夥伴伺服器執行個體之鏡像端點使用的連接埠號碼。 如需詳細資訊，請參閱 [指定伺服器網路位址 &#40;資料庫鏡像&#41;](../../database-engine/database-mirroring/specify-a-server-network-address-database-mirroring.md)。  
  
     例如，在鏡像伺服器執行個體上，下列 ALTER DATABASE 陳述式將夥伴設為原始主體伺服器執行個體。 資料庫名稱是 **AdventureWorks**、系統位址是 DBSERVER1 (夥伴系統的名稱)，而夥伴資料庫鏡像端點使用的通訊埠是 7022：  
  
    ```  
    ALTER DATABASE AdventureWorks   
       SET PARTNER = 'TCP://DBSERVER1:7022'  
    ```  
  
     此陳述式會準備鏡像伺服器，以便在主體伺服器通知之後建立工作階段。  
  
5.  若要設定鏡像伺服器做為主體資料庫上的夥伴，請連接到該主體伺服器，並發出以下陳述式：  
  
     ALTER DATABASE _\<database\_name\>_ SET PARTNER **=** _\<server\_network\_address\>_  
  
     如需詳細資訊，請參閱步驟 4。  
  
     例如，在主體伺服器執行個體上，下列 ALTER DATABASE 陳述式將夥伴設為原始鏡像伺服器執行個體。 資料庫名稱是 **AdventureWorks**，系統位址是 DBSERVER2 (夥伴系統的名稱) 而夥伴資料庫鏡像端點使用的通訊埠是 7025：  
  
    ```  
    ALTER DATABASE AdventureWorks SET PARTNER = 'TCP://DBSERVER2:7022'  
    ```  
  
     在主體伺服器上輸入此陳述式可開始資料庫鏡像工作階段。  
  
6.  依預設，工作階段會設定為完整交易安全性 (SAFETY 設定為 FULL)，它會以不含自動容錯移轉的同步高安全性模式啟動工作階段。 您可以依照下列方式，將工作階段重新設定為在具有自動容錯移轉的高安全性模式下執行，或在非同步的高效能模式下執行：  
  
    -   **具有自動容錯移轉的高安全性模式**  
  
         如果您想讓高安全性模式工作階段支援自動容錯移轉，請加入見證伺服器執行個體。 如需詳細資訊，請參閱[使用 Windows 驗證加入資料庫鏡像見證 &#40;Transact-SQL&#41;](../../database-engine/database-mirroring/add-a-database-mirroring-witness-using-windows-authentication-transact-sql.md)。  
  
    -   **高效能模式**  
  
         或者，如果您不想進行自動容錯移轉，而且較注重效能而非可用性，請關閉交易安全性。 如需詳細資訊，請參閱[在資料庫鏡像工作階段中變更交易安全性 &#40;Transact-SQL&#41;](../../database-engine/database-mirroring/change-transaction-safety-in-a-database-mirroring-session-transact-sql.md)。  
  
        > [!NOTE]  
        >  在高效能模式中，必須將 WITNESS 設定為 OFF。 如需詳細資訊，請參閱[仲裁：見證如何影響資料庫可用性 &#40;資料庫鏡像&#41;](../../database-engine/database-mirroring/quorum-how-a-witness-affects-database-availability-database-mirroring.md)。  
  
## <a name="example"></a>範例  
  
> [!NOTE]  
>  下列範例會在夥伴之間為現有的鏡像資料庫建立資料庫鏡像工作階段。 如需建立鏡像資料庫的資訊，請參閱 [準備鏡像資料庫以進行鏡像 &#40;SQL Server&#41;](../../database-engine/database-mirroring/prepare-a-mirror-database-for-mirroring-sql-server.md)。  
  
 此範例會顯示建立資料庫鏡像工作階段 (不使用見證) 的基本步驟。 這兩個夥伴是兩個電腦系統 (PARTNERHOST1 和 PARTNERHOST5) 上的預設伺服器執行個體。 這兩個夥伴執行個體會執行相同的 Windows 網域使用者帳戶 (MYDOMAIN\dbousername)。  
  
1.  在主體伺服器執行個體 (PARTNERHOST1 上的預設執行個體) 上，使用通訊埠 7022 建立支援所有角色的端點：  
  
    ```  
    --create an endpoint for this instance  
    CREATE ENDPOINT Endpoint_Mirroring  
        STATE=STARTED   
        AS TCP (LISTENER_PORT=7022)   
        FOR DATABASE_MIRRORING (ROLE=ALL)  
    GO  
    --Partners under same domain user; login already exists in master.  
    ```  
  
    > [!NOTE]  
    >  如需設定登入的範例，請參閱 [使用 Windows 驗證允許資料庫鏡像的網路存取 &#40;SQL Server&#41;](../../database-engine/database-mirroring/database-mirroring-allow-network-access-windows-authentication.md)。  
  
2.  在鏡像伺服器執行個體 (PARTNERHOST5 上的預設執行個體) 上，使用通訊埠 7022 建立支援所有角色的端點：  
  
    ```  
    --create an endpoint for this instance  
    CREATE ENDPOINT Endpoint_Mirroring  
        STATE=STARTED   
        AS TCP (LISTENER_PORT=7022)   
        FOR DATABASE_MIRRORING (ROLE=ALL)  
    GO  
    --Partners under same domain user; login already exists in master.  
    ```  
  
3.  在主體伺服器執行個體 (位於 PARTNERHOST1) 上，備份資料庫：  
  
    ```  
    BACKUP DATABASE AdventureWorks   
        TO DISK = 'C:\AdvWorks_dbmirror.bak'   
        WITH FORMAT  
    GO  
    ```  
  
4.  在鏡像伺服器執行個體 (在 `PARTNERHOST5`上) 上，還原資料庫：  
  
    ```  
    RESTORE DATABASE AdventureWorks   
        FROM DISK = 'Z:\AdvWorks_dbmirror.bak'   
        WITH NORECOVERY  
    GO  
    ```  
  
5.  建立完整資料庫備份之後，您必須在主體資料庫上建立記錄備份。 例如，下列 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式會將記錄備份至前一次資料庫備份所使用的相同檔案：  
  
    ```  
    BACKUP LOG AdventureWorks   
        TO DISK = 'C:\AdventureWorks.bak'   
    GO  
    ```  
  
6.  您必須先套用必要的記錄備份 (以及任何後續記錄備份)，才能啟動鏡像。  
  
     例如，下列 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式會從 C:\AdventureWorks.bak 還原第一筆記錄：  
  
    ```  
    RESTORE LOG AdventureWorks   
        FROM DISK = 'C:\ AdventureWorks.bak'   
        WITH FILE=1, NORECOVERY  
    GO  
    ```  
  
7.  在鏡像伺服器執行個體上，將 PARTNERHOST1 上的伺服器執行個體設定為夥伴 (讓它成為初始主體伺服器)：  
  
    ```  
    USE master;  
    GO  
    ALTER DATABASE AdventureWorks   
        SET PARTNER =   
        'TCP://PARTNERHOST1:7022'  
    GO  
    ```  
  
    > [!IMPORTANT]  
    >  依預設，資料庫鏡像工作階段會以同步模式執行，前提是必須有完整交易安全性 (SAFETY 設定為 FULL)。 若要讓工作階段以非同步的高效能模式執行，請將 SAFETY 設定為 OFF。 如需詳細資訊，請參閱 [Database Mirroring Operating Modes](../../database-engine/database-mirroring/database-mirroring-operating-modes.md)。  
  
8.  在主體伺服器執行個體上，將 `PARTNERHOST5` 上的伺服器執行個體設定為夥伴 (讓它成為初始鏡像伺服器)：  
  
    ```  
    USE master;  
    GO  
    ALTER DATABASE AdventureWorks   
        SET PARTNER = 'TCP://PARTNERHOST5:7022'  
    GO  
    ```  
  
9. 或者，如果您想要使用具有自動容錯移轉的高安全性模式，請設定見證伺服器執行個體。 如需詳細資訊，請參閱[使用 Windows 驗證加入資料庫鏡像見證 &#40;Transact-SQL&#41;](../../database-engine/database-mirroring/add-a-database-mirroring-witness-using-windows-authentication-transact-sql.md)。  
  
> [!NOTE]  
>  如需顯示安全性設定、準備鏡像資料庫、設定夥伴及新增見證的完整範例，請參閱[設定資料庫鏡像 &#40;SQL Server&#41;](../../database-engine/database-mirroring/setting-up-database-mirroring-sql-server.md)。  
  
## <a name="see-also"></a>另請參閱  
 [設定資料庫鏡像 &#40;SQL Server&#41;](../../database-engine/database-mirroring/setting-up-database-mirroring-sql-server.md)   
 [ALTER DATABASE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-database-transact-sql.md)   
 [使用 Windows 驗證允許資料庫鏡像的網路存取 &#40;SQL Server&#41;](../../database-engine/database-mirroring/database-mirroring-allow-network-access-windows-authentication.md)   
 [準備鏡像資料庫以進行鏡像 &#40;SQL Server&#41;](../../database-engine/database-mirroring/prepare-a-mirror-database-for-mirroring-sql-server.md)   
 [建立 Windows 驗證的資料庫鏡像端點 &#40;Transact-SQL&#41;](../../database-engine/database-mirroring/create-a-database-mirroring-endpoint-for-windows-authentication-transact-sql.md)   
 [資料庫鏡像和記錄傳送 &#40;SQL Server&#41;](../../database-engine/database-mirroring/database-mirroring-and-log-shipping-sql-server.md)   
 [資料庫鏡像 &#40;SQL Server&#41;](../../database-engine/database-mirroring/database-mirroring-sql-server.md)   
 [資料庫鏡像和複寫 &#40;SQL Server&#41;](../../database-engine/database-mirroring/database-mirroring-and-replication-sql-server.md)   
 [設定資料庫鏡像 &#40;SQL Server&#41;](../../database-engine/database-mirroring/setting-up-database-mirroring-sql-server.md)   
 [指定伺服器網路位址 &#40;資料庫鏡像&#41;](../../database-engine/database-mirroring/specify-a-server-network-address-database-mirroring.md)   
 [Database Mirroring Operating Modes](../../database-engine/database-mirroring/database-mirroring-operating-modes.md)  
  
  

