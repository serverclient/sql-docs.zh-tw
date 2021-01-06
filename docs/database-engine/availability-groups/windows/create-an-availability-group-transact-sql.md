---
title: 搭配 Transact-SQL (T-SQL) 建立可用性群組
description: 使用 Transact-SQL 在已啟用 Always On 可用性群組功能的 SQL Server 2019 (15.x) 執行個體上建立及設定可用性群組。
ms.custom: seo-lt-2019
ms.date: 05/17/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: how-to
helpviewer_keywords:
- Availability Groups [SQL Server], creating
ms.assetid: 8b0a6301-8b79-4415-b608-b40876f30066
author: cawrites
ms.author: chadam
ms.openlocfilehash: 7223b0d8b283caf6300e2be056d399e45e637350
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97643292"
---
# <a name="create-an-always-on-availability-group-using-transact-sql-t-sql"></a>使用 Transact-SQL (T-SQL) 建立 Always On 可用性群組
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]
  此主題描述如何使用 [!INCLUDE[tsql](../../../includes/tsql-md.md)] 在 [!INCLUDE[ssCurrent](../../../includes/sscurrent-md.md)] 執行個體上建立和設定已啟用 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] 功能的可用性群組。 *「可用性群組」* (Availability Group) 會定義當做單一單位容錯移轉的一組使用者資料庫，以及支援容錯移轉的一組容錯移轉夥伴 (也稱為 *「可用性複本」* (Availability Replica))。  
  
> [!NOTE]  
>  如需可用性群組的簡介，請參閱 [AlwaysOn 可用性群組概觀 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server.md)中的 PowerShell，透過 PowerShell Cmdlet 建立及設定 AlwaysOn 可用性群組。  
  
> [!NOTE]  
>  [!INCLUDE[tsql](../../../includes/tsql-md.md)]以外的替代方案是，使用 [建立可用性群組精靈] 或 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] PowerShell 指令程式。 如需詳細資訊，請參閱 [使用可用性群組精靈 &#40;SQL Server Management Studio&#41;](../../../database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio.md)、 [使用新增可用性群組對話方塊 &#40;SQL Server Management Studio&#41;](../../../database-engine/availability-groups/windows/use-the-new-availability-group-dialog-box-sql-server-management-studio.md)或 [建立可用性群組 &#40;SQL Server PowerShell&#41;](../../../database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell.md)。  

  
## <a name="prerequisites-restrictions-and-recommendations"></a><a name="PrerequisitesRestrictions"></a> 必要條件、限制及建議  
  
-   建立可用性群組之前，請確認裝載可用性複本的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體位於相同 Windows Server 容錯移轉叢集 (WSFC) 容錯移轉叢集的不同 WSFC 節點上。 此外，請確認每個伺服器執行個體都符合所有其他 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] 必要條件。 如需詳細資訊，強烈建議您閱讀 [AlwaysOn 可用性群組的必要條件、限制和建議 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/prereqs-restrictions-recommendations-always-on-availability.md)。  
  
  
##  <a name="permissions"></a><a name="Permissions"></a> 權限  
 需要 **系統管理員 (sysadmin)** 固定伺服器角色的成員資格，以及 CREATE AVAILABILITY GROUP 伺服器權限、ALTER ANY AVAILABILITY GROUP 權限或 CONTROL SERVER 權限。  
  
##  <a name="using-transact-sql-to-create-and-configure-an-availability-group"></a><a name="TsqlProcedure"></a> 使用 Transact-SQL 建立和設定可用性群組 

###  <a name="summary-of-tasks-and-corresponding-transact-sql-statements"></a><a name="SummaryTsqlStatements"></a> 工作和對應 Transact-SQL 陳述式的摘要  
 下表列出與建立和設定可用性群組有關的基本工作，並指出哪些 [!INCLUDE[tsql](../../../includes/tsql-md.md)] 陳述式要用於這些工作。 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] 工作必須依照其出現在表格中的順序來執行。  
  
|Task|Transact-SQL 陳述式|在何處執行工作 **&#42;**|  
|----------|----------------------------------|---------------------------------|  
|建立資料庫鏡像端點 (每個 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體一次)|[CREATE ENDPOINT](../../../t-sql/statements/create-endpoint-transact-sql.md) 端點名稱  ...FOR DATABASE_MIRRORING|在缺少資料庫鏡像端點的每一個伺服器執行個體上執行。|  
|建立可用性群組|[CREATE AVAILABILITY GROUP](../../../t-sql/statements/create-availability-group-transact-sql.md)|於裝載初始主要複本的伺服器執行個體上執行。|  
|將次要複本加入可用性群組|[ALTER AVAILABILITY GROUP](../../../database-engine/availability-groups/windows/join-a-secondary-replica-to-an-availability-group-sql-server.md) 群組名稱  JOIN|在裝載次要複本的每一個伺服器執行個體上執行。|  
|準備次要資料庫|[BACKUP](../../../t-sql/statements/backup-transact-sql.md) 和 [RESTORE](../../../t-sql/statements/restore-statements-transact-sql.md)。|在裝載主要複本的伺服器執行個體上建立備份。<br /><br /> 使用 RESTORE WITH NORECOVERY，還原裝載次要複本之每個伺服器執行個體上的備份。|  
|將每一個次要資料庫加入可用性群組來啟動資料同步處理。|[ALTER DATABASE](../../../t-sql/statements/alter-database-transact-sql-set-hadr.md) 資料庫名稱  SET HADR AVAILABILITY GROUP = 群組名稱 |在裝載次要複本的每一個伺服器執行個體上執行。|  
  
 \* 若要執行指定的工作，請連接到指定的伺服器執行個體。   
 
### <a name="using-transact-sql"></a>使用 TRANSACT-SQL 
> [!NOTE]  
>  如需包含每一個這類 [!INCLUDE[tsql](../../../includes/tsql-md.md)] 陳述式之程式碼範例的範例設定程序，請參閱[範例：設定使用 Windows 驗證的可用性群組](#ExampleConfigAGWinAuth)。  
  
1.  連接到裝載主要複本的伺服器執行個體。  
  
2.  使用 [CREATE AVAILABILITY GROUP](../../../t-sql/statements/create-availability-group-transact-sql.md)[!INCLUDE[tsql](../../../includes/tsql-md.md)] 陳述式建立可用性群組。  
  
3.  將新的次要複本加入可用性群組。 如需詳細資訊，請參閱 [將次要複本聯結至可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/join-a-secondary-replica-to-an-availability-group-sql-server.md)。  
  
4.  如果是可用性群組中的每個資料庫，請使用 RESTORE WITH NORECOVERY 還原主要資料庫的最近備份來建立次要資料庫。 如需詳細資訊，請參閱[範例：設定使用 Windows 驗證的可用性群組 (Transact-SQL)](../../../database-engine/availability-groups/windows/create-an-availability-group-transact-sql.md)，從還原資料庫備份的步驟開始。  
  
5.  將每一個新的次要資料庫加入可用性群組。 如需詳細資訊，請參閱 [將次要複本聯結至可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/join-a-secondary-replica-to-an-availability-group-sql-server.md)。  
  
##  <a name="example-configuring-an-availability-group-that-uses-windows-authentication"></a><a name="ExampleConfigAGWinAuth"></a> 範例：設定使用 Windows 驗證的可用性群組  
 這個範例會建立範例 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] 組態程序，此程序會使用 [!INCLUDE[tsql](../../../includes/tsql-md.md)] 來設定使用 Windows 驗證的資料庫鏡像端點，並建立及設定可用性群組以及其次要資料庫。  
  
 此範例包含下列章節：  
  
-   [使用範例組態程序的必要條件](#PrerequisitesForExample)  
  
-   [範例組態程序](#SampleProcedure)  
  
-   [範例組態程序的完整程式碼範例](#CompleteCodeExample)  
  
###  <a name="prerequisites-for-using-the-sample-configuration-procedure"></a><a name="PrerequisitesForExample"></a> 使用範例組態程序的必要條件  
 此範例程序有下列需求：  
  
-   伺服器執行個體必須支援 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)]。 如需詳細資訊，請參閱 [AlwaysOn 可用性群組的必要條件、限制和建議 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/prereqs-restrictions-recommendations-always-on-availability.md)。  
  
-   *MyDb1* 與 *MyDb2* 這兩個範例資料庫必須存在於將裝載主要複本的伺服器執行個體上。 下列程式碼範例會建立及設定這兩個資料庫，並建立每一個資料庫的完整備份。 在您打算建立範例可用性群組的伺服器執行個體上，執行這些程式碼範例。 此伺服器執行個體將會裝載範例可用性群組的初始主要複本。  
  
    1.  下列 [!INCLUDE[tsql](../../../includes/tsql-md.md)] 範例會建立這些資料庫，並加以更改為使用完整復原模式：  
  
        ```sql  
        -- Create sample databases:  
        CREATE DATABASE MyDb1;  
        GO  
        ALTER DATABASE MyDb1 SET RECOVERY FULL;  
        GO  
  
        CREATE DATABASE MyDb2;  
        GO  
        ALTER DATABASE MyDb2 SET RECOVERY FULL;  
        GO  
        ```  
  
    2.  下列程式碼範例會建立 *MyDb1* 與 *MyDb2* 的完整資料庫備份。 此程式碼範例會使用虛構的備份共用 \\\\*FILESERVER*\\*SQLbackups*。  
  
        ```sql  
        -- Backup sample databases:  
        BACKUP DATABASE MyDb1   
        TO DISK = N'\\FILESERVER\SQLbackups\MyDb1.bak'   
            WITH FORMAT;  
        GO  
  
        BACKUP DATABASE MyDb2   
        TO DISK = N'\\FILESERVER\SQLbackups\MyDb2.bak'   
            WITH FORMAT;  
        GO  
        ```  
  
 [&#91;回到範例頂端&#93;](#ExampleConfigAGWinAuth)  
  
###  <a name="sample-configuration-procedure"></a><a name="SampleProcedure"></a> 範例組態程序  
 在此範例組態中，將會於不同但受信任網域 (`DOMAIN1` 和 `DOMAIN2`) 底下執行其服務帳戶的兩個獨立伺服器執行個體上建立可用性複本。  
  
 下表摘要說明此範例組態中所使用的值。  
  
|初始角色|系統|主機 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體|  
|------------------|------------|---------------------------------------------|  
|Primary|`COMPUTER01`|`AgHostInstance`|  
|次要|`COMPUTER02`|預設執行個體|  
  
1.  在您打算建立可用性群組的伺服器執行個體上 (這是 *上名為* 的執行個體)，建立名為 `AgHostInstance` dbm_endpoint `COMPUTER01`的資料庫鏡像端點。 此端點使用通訊埠 7022。 請注意，可用性群組建立所在的伺服器執行個體將會裝載主要複本。  
  
    ```sql  
    -- Create endpoint on server instance that hosts the primary replica:  
    CREATE ENDPOINT dbm_endpoint  
        STATE=STARTED   
        AS TCP (LISTENER_PORT=7022)   
        FOR DATABASE_MIRRORING (ROLE=ALL);  
    GO  
    ```  
  
2.  在要裝載次要複本的伺服器執行個體上 (這是 *上的預設伺服器執行個體)，建立端點* dbm_endpoint `COMPUTER02`。 此端點使用通訊埠 5022。  
  
    ```sql  
    -- Create endpoint on server instance that hosts the secondary replica:   
    CREATE ENDPOINT dbm_endpoint  
        STATE=STARTED   
        AS TCP (LISTENER_PORT=5022)   
        FOR DATABASE_MIRRORING (ROLE=ALL);  
    GO  
    ```  
  
3.  > [!NOTE]  
    >  如果要裝載可用性複本之伺服器執行個體的服務帳戶在相同的網域帳戶下執行，則不需要此步驟。 略過它，直接進入下一個步驟。  
  
     如果伺服器執行個體的服務帳戶在不同的網域使用者之下執行，請在每一個伺服器執行個體上建立其他伺服器執行個體的登入，並授與此登入存取本機資料庫鏡像端點的權限。  
  
     下列程式碼範例將示範用來建立登入，並在端點上授與其權限的 [!INCLUDE[tsql](../../../includes/tsql-md.md)] 陳述式。 遠端伺服器執行個體的網域帳戶在這裡會以 *domain_name*\\*user_name* 來表示。  
  
    ```sql  
    -- If necessary, create a login for the service account, domain_name\user_name  
    -- of the server instance that will host the other replica:  
    USE master;  
    GO  
    CREATE LOGIN [domain_name\user_name] FROM WINDOWS;  
    GO  
    -- And Grant this login connect permissions on the endpoint:  
    GRANT CONNECT ON ENDPOINT::dbm_endpoint   
       TO [domain_name\user_name];  
    GO  
    ```  
  
4.  在使用者資料庫所在的伺服器執行個體上，建立可用性群組。  
  
     下列程式碼範例會在建立 *MyDb1* 與 *MyDb2* 範例資料庫的伺服器執行個體上建立名為 *MyAG* 的可用性群組。 先指定本機伺服器執行個體，即 `AgHostInstance`COMPUTER01 *上的* 。 這個執行個體將會裝載初始主要複本。 接著指定遠端伺服器執行個體，即 *COMPUTER02* 上的預設伺服器執行個體，以裝載次要複本。 這兩個可用性複本都是設定為具有手動容錯移轉的非同步認可模式 (對於非同步認可複本，手動容錯移轉表示在可能遺失資料的情況下強制容錯移轉)。  
  
    ```sql
    -- Create the availability group, MyAG:   
    CREATE AVAILABILITY GROUP MyAG   
       FOR   
          DATABASE MyDB1, MyDB2   
       REPLICA ON   
          'COMPUTER01\AgHostInstance' WITH   
             (  
             ENDPOINT_URL = 'TCP://COMPUTER01.Adventure-Works.com:7022',   
             AVAILABILITY_MODE = ASYNCHRONOUS_COMMIT,  
             FAILOVER_MODE = MANUAL  
             ),  
          'COMPUTER02' WITH   
             (  
             ENDPOINT_URL = 'TCP://COMPUTER02.Adventure-Works.com:5022',  
             AVAILABILITY_MODE = ASYNCHRONOUS_COMMIT,  
             FAILOVER_MODE = MANUAL  
             );   
    GO  
    ```  
  
     如需建立可用性群組的其他 [!INCLUDE[tsql](../../../includes/tsql-md.md)] 程式碼範例，請參閱 [CREATE AVAILABILITY GROUP &#40;Transact-SQL&#41;](../../../t-sql/statements/create-availability-group-transact-sql.md)。  
  
5.  在裝載次要複本的伺服器執行個體上，將次要複本加入至可用性群組。  
  
     下列程式碼範例會將 `COMPUTER02` 上的第二個複本加入到 `MyAG` 可用性群組。  
  
    ```sql 
    -- On the server instance that hosts the secondary replica,   
    -- join the secondary replica to the availability group:  
    ALTER AVAILABILITY GROUP MyAG JOIN;  
    GO  
    ```  
  
6.  在裝載次要複本的伺服器執行個體上，建立次要資料庫。  
  
     下列程式碼範例會使用 RESTORE WITH NORECOVERY 來還原資料庫備份，以建立 *MyDb1* 與 *MyDb2* 次要資料庫。  
  
    ```sql 
    -- On the server instance that hosts the secondary replica,   
    -- Restore database backups using the WITH NORECOVERY option:  
    RESTORE DATABASE MyDb1   
        FROM DISK = N'\\FILESERVER\SQLbackups\MyDb1.bak'   
        WITH NORECOVERY;  
    GO  
  
    RESTORE DATABASE MyDb2   
        FROM DISK = N'\\FILESERVER\SQLbackups\MyDb2.bak'   
        WITH NORECOVERY;  
    GO 
    ```  
  
7.  在裝載主要複本的伺服器執行個體上，於每一個主要資料庫上備份交易記錄。  
  
    > [!IMPORTANT]  
    >  當您設定真正的可用性群組時，我們建議您在取得此記錄備份之前，先暫停主要資料庫的記錄備份工作，直到您將對應的次要資料庫加入可用性群組為止。  
  
     下列程式碼範例會在 MyDb1 和 MyDb2 上建立交易記錄備份。  
  
    ```sql  
    -- On the server instance that hosts the primary replica,   
    -- Backup the transaction log on each primary database:  
    BACKUP LOG MyDb1   
    TO DISK = N'\\FILESERVER\SQLbackups\MyDb1.bak'   
        WITH NOFORMAT;  
    GO  
  
    BACKUP LOG MyDb2   
    TO DISK = N'\\FILESERVER\SQLbackups\MyDb2.bak'   
        WITHNOFORMAT;  
    GO
    ```  
  
    > [!TIP]  
    >  一般而言，您必須在每一個主要資料庫上進行記錄備份，然後在對應的次要資料庫上還原 (使用 WITH NORECOVERY)。 不過，如果資料庫剛剛建立，而且尚未建立任何記錄備份，或是如果復原模式剛剛從 SIMPLE 變更為 FULL，可能就不需要此記錄備份。  
  
8.  在裝載次要複本的伺服器執行個體上，將記錄備份套用到次要資料庫。  
  
     下列程式碼範例會使用 RESTORE WITH NORECOVERY 來還原資料庫備份，以將備份套用至 *MyDb1* 與 *MyDb2* 次要資料庫。  
  
    > [!IMPORTANT]  
    >  當您準備真正的次要資料庫時，您必須套用您從建立次要資料庫的資料庫備份開始所取得的每一個記錄備份 (從最早的開始)，而且一定要使用 RESTORE WITH NORECOVERY。 當您同時還原完整和差異資料庫備份時，您只需要套用差異備份之後所做的記錄備份。  
  
    ```sql  
    -- Restore the transaction log on each secondary database,  
    -- using the WITH NORECOVERY option:  
    RESTORE LOG MyDb1   
        FROM DISK = N'\\FILESERVER\SQLbackups\MyDb1.bak'   
        WITH FILE=1, NORECOVERY;  
    GO  
    RESTORE LOG MyDb2   
        FROM DISK = N'\\FILESERVER\SQLbackups\MyDb2.bak'   
        WITH FILE=1, NORECOVERY;  
    GO  
    ```  
  
9. 在裝載次要複本的伺服器執行個體上，將新的次要資料庫加入至可用性群組。  
  
     下列程式碼範例會依序將 *MyDb1* 次要資料庫與 *MyDb2* 次要資料庫聯結至 *MyAG* 可用性群組。  
  
    ```sql  
    -- On the server instance that hosts the secondary replica,   
    -- join each secondary database to the availability group:  
    ALTER DATABASE MyDb1 SET HADR AVAILABILITY GROUP = MyAG;  
    GO  
  
    ALTER DATABASE MyDb2 SET HADR AVAILABILITY GROUP = MyAG;  
    GO  
    ```  
  
###  <a name="complete-code-example-for-sample-configuration-procedure"></a><a name="CompleteCodeExample"></a> 範例組態程序的完整程式碼範例  
 下列範例會合併範例組態程序之所有步驟的程式碼範例。 下表摘要說明此程式碼範例中所使用的預留位置值。 如需有關此程式碼範例中步驟的詳細資訊，請參閱本主題稍早的 [使用範例組態程序的必要條件](#PrerequisitesForExample) 和 [範例組態程序](#SampleProcedure)。  
  
|預留位置|描述|  
|-----------------|-----------------|  
|\\\\*FILESERVER*\\*SQLbackups*|虛構的備份共用。|  
|\\\\*FILESERVER*\\*SQLbackups\MyDb1.bak*|MyDb1 的備份檔案。|  
|\\\\*FILESERVER*\\*SQLbackups\MyDb2.bak*|MyDb2 的備份檔案。|  
|*7022*|指派給每一個資料庫鏡像端點的通訊埠編號。|  
|*COMPUTER01\AgHostInstance*|裝載初始主要複本的伺服器執行個體。|  
|*COMPUTER02*|裝載初始次要複本的伺服器執行個體。 這是 `COMPUTER02`上的預設伺服器執行個體。|  
|*上名為*|為每個資料庫鏡像端點指定的名稱。|  
|*MyDb1*|範例可用性群組的名稱。|  
|*MyDb1*|第一個範例資料庫的名稱。|  
|*MyDb2*|第二個範例資料庫的名稱。|  
|*DOMAIN1\user1*|要裝載初始主要複本之伺服器執行個體的服務帳戶。|  
|*DOMAIN2\user2*|要裝載初始次要複本之伺服器執行個體的服務帳戶。|  
|TCP://*COMPUTER01.Adventure-Works.com*:*7022*|COMPUTER01 上 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 之 AgHostInstance 執行個體的端點 URL。|  
|TCP://*COMPUTER02.Adventure-Works.com*:*5022*|COMPUTER02 上 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 之預設執行個體的端點 URL。|  
  
> [!NOTE]  
>  如需建立可用性群組的其他 [!INCLUDE[tsql](../../../includes/tsql-md.md)] 程式碼範例，請參閱 [CREATE AVAILABILITY GROUP &#40;Transact-SQL&#41;](../../../t-sql/statements/create-availability-group-transact-sql.md)。  
  
```sql  
-- on the server instance that will host the primary replica,   
-- create sample databases:  
CREATE DATABASE MyDb1;  
GO  
ALTER DATABASE MyDb1 SET RECOVERY FULL;  
GO  
  
CREATE DATABASE MyDb2;  
GO  
ALTER DATABASE MyDb2 SET RECOVERY FULL;  
GO  
  
-- Backup sample databases:  
BACKUP DATABASE MyDb1   
TO DISK = N'\\FILESERVER\SQLbackups\MyDb1.bak'   
    WITH FORMAT;  
GO  
  
BACKUP DATABASE MyDb2   
TO DISK = N'\\FILESERVER\SQLbackups\MyDb2.bak'   
    WITH FORMAT;  
GO  
  
-- Create the endpoint on the server instance that will host the primary replica:  
CREATE ENDPOINT dbm_endpoint  
    STATE=STARTED   
    AS TCP (LISTENER_PORT=7022)   
    FOR DATABASE_MIRRORING (ROLE=ALL);  
GO  
  
-- Create the endpoint on the server instance that will host the secondary replica:   
CREATE ENDPOINT dbm_endpoint  
    STATE=STARTED   
    AS TCP (LISTENER_PORT=7022)   
    FOR DATABASE_MIRRORING (ROLE=ALL);  
GO  
  
-- If both service accounts run under the same domain account, skip this step. Otherwise,   
-- On the server instance that will host the primary replica,   
-- create a login for the service account   
-- of the server instance that will host the secondary replica, DOMAIN2\user2,   
-- and grant this login connect permissions on the endpoint:  
USE master;  
GO  
CREATE LOGIN [DOMAIN2\user2] FROM WINDOWS;  
GO  
GRANT CONNECT ON ENDPOINT::dbm_endpoint   
   TO [DOMAIN2\user2];  
GO  
  
-- If both service accounts run under the same domain account, skip this step. Otherwise,   
-- On the server instance that will host the secondary replica,  
-- create a login for the service account   
-- of the server instance that will host the primary replica, DOMAIN1\user1,   
-- and grant this login connect permissions on the endpoint:  
USE master;  
GO  
  
CREATE LOGIN [DOMAIN1\user1] FROM WINDOWS;  
GO  
GRANT CONNECT ON ENDPOINT::dbm_endpoint   
   TO [DOMAIN1\user1];  
GO  
  
-- On the server instance that will host the primary replica,   
-- create the availability group, MyAG:  
CREATE AVAILABILITY GROUP MyAG   
   FOR   
      DATABASE MyDB1, MyDB2   
   REPLICA ON   
      'COMPUTER01\AgHostInstance' WITH   
         (  
         ENDPOINT_URL = 'TCP://COMPUTER01.Adventure-Works.com:7022',  
         AVAILABILITY_MODE = SYNCHRONOUS_COMMIT,  
         FAILOVER_MODE = AUTOMATIC  
         ),  
      'COMPUTER02' WITH   
         (  
         ENDPOINT_URL = 'TCP://COMPUTER02.Adventure-Works.com:7022',  
         AVAILABILITY_MODE = SYNCHRONOUS_COMMIT,  
         FAILOVER_MODE = AUTOMATIC  
         );   
GO  
  
-- On the server instance that hosts the secondary replica,   
-- join the secondary replica to the availability group:  
ALTER AVAILABILITY GROUP MyAG JOIN;  
GO  
  
-- Restore database backups onto this server instance, using RESTORE WITH NORECOVERY:  
RESTORE DATABASE MyDb1   
    FROM DISK = N'\\FILESERVER\SQLbackups\MyDb1.bak'   
    WITH NORECOVERY;  
GO  
  
RESTORE DATABASE MyDb2   
    FROM DISK = N'\\FILESERVER\SQLbackups\MyDb2.bak'   
    WITH NORECOVERY;  
GO  
  
-- Back up the transaction log on each primary database:  
BACKUP LOG MyDb1   
TO DISK = N'\\FILESERVER\SQLbackups\MyDb1.bak'   
    WITH NOFORMAT;  
GO  
  
BACKUP LOG MyDb2   
TO DISK = N'\\FILESERVER\SQLbackups\MyDb2.bak'   
    WITHNOFORMAT  
GO  
  
-- Restore the transaction log on each secondary database,  
-- using the WITH NORECOVERY option:  
RESTORE LOG MyDb1   
    FROM DISK = N'\\FILESERVER\SQLbackups\MyDb1.bak'   
    WITH FILE=1, NORECOVERY;  
GO  
RESTORE LOG MyDb2   
    FROM DISK = N'\\FILESERVER\SQLbackups\MyDb2.bak'   
    WITH FILE=1, NORECOVERY;  
GO  
  
-- On the server instance that hosts the secondary replica,   
-- join each secondary database to the availability group:  
ALTER DATABASE MyDb1 SET HADR AVAILABILITY GROUP = MyAG;  
GO  
  
ALTER DATABASE MyDb2 SET HADR AVAILABILITY GROUP = MyAG;  
GO  
```  
  
##  <a name="related-tasks"></a><a name="RelatedTasks"></a> 相關工作  
 **若要設定可用性群組和複本屬性**  
  
-   [變更可用性複本的可用性模式 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/change-the-availability-mode-of-an-availability-replica-sql-server.md)  
  
-   [變更可用性複本的容錯移轉模式 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/change-the-failover-mode-of-an-availability-replica-sql-server.md)  
  
-   [建立或設定可用性群組接聽程式 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/create-or-configure-an-availability-group-listener-sql-server.md)  
  
-   [設定彈性容錯移轉原則以控制自動容錯移轉的條件 &#40;AlwaysOn 可用性群組&#41;](../../../database-engine/availability-groups/windows/configure-flexible-automatic-failover-policy.md)  
  
-   [在新增或修改可用性複本時指定端點 URL &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/specify-endpoint-url-adding-or-modifying-availability-replica.md)  
  
-   [設定可用性複本的備份 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/configure-backup-on-availability-replicas-sql-server.md)  
  
-   [設定可用性複本的唯讀存取 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/configure-read-only-access-on-an-availability-replica-sql-server.md)  
  
-   [設定可用性群組的唯讀路由 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/configure-read-only-routing-for-an-availability-group-sql-server.md)  
  
-   [變更可用性複本的工作階段逾時期限 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/change-the-session-timeout-period-for-an-availability-replica-sql-server.md)  
  
 **若要完成可用性群組組態**  
  
-   [將次要複本聯結至可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/join-a-secondary-replica-to-an-availability-group-sql-server.md)  
  
-   [針對可用性群組手動準備次要資料庫 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/manually-prepare-a-secondary-database-for-an-availability-group-sql-server.md)  
  
-   [將次要資料庫聯結至可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/join-a-secondary-database-to-an-availability-group-sql-server.md)  
  
-   [建立或設定可用性群組接聽程式 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/create-or-configure-an-availability-group-listener-sql-server.md)  
  
 **建立可用性群組的其他方法**  
  
-   [使用可用性群組精靈 &#40;SQL Server Management Studio&#41;](../../../database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio.md)  
  
-   [使用新增可用性群組對話方塊 &#40;SQL Server Management Studio&#41;](../../../database-engine/availability-groups/windows/use-the-new-availability-group-dialog-box-sql-server-management-studio.md)  
  
-   [建立可用性群組 &#40;SQL Server PowerShell&#41;](../../../database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell.md)  
  
 **啟用 AlwaysOn 可用性群組**  
  
-   [啟用和停用 AlwaysOn 可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/enable-and-disable-always-on-availability-groups-sql-server.md)  
  
 **若要設定資料庫鏡像端點**  
  
-   [針對 AlwaysOn 可用性群組建立資料庫鏡像端點 &#40;SQL Server PowerShell&#41;](../../../database-engine/availability-groups/windows/database-mirroring-always-on-availability-groups-powershell.md)  
  
-   [建立 Windows 驗證的資料庫鏡像端點 &#40;Transact-SQL&#41;](../../../database-engine/database-mirroring/create-a-database-mirroring-endpoint-for-windows-authentication-transact-sql.md)  
  
-   [使用資料庫鏡像端點憑證 &#40;Transact-SQL&#41;](../../../database-engine/database-mirroring/use-certificates-for-a-database-mirroring-endpoint-transact-sql.md)  
  
-   [在新增或修改可用性複本時指定端點 URL &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/specify-endpoint-url-adding-or-modifying-availability-replica.md)  
  
 **疑難排解 AlwaysOn 可用性群組組態**  
  
-   [疑難排解 AlwaysOn 可用性群組組態 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/troubleshoot-always-on-availability-groups-configuration-sql-server.md)  
  
-   [疑難排解失敗的加入檔案作業 &#40;AlwaysOn 可用性群組&#41;](../../../database-engine/availability-groups/windows/troubleshoot-a-failed-add-file-operation-always-on-availability-groups.md)  
  
##  <a name="related-content"></a><a name="RelatedContent"></a> 相關內容  
  
-   **部落格：**  
  
     [Always On - HADRON Learning Series:Worker Pool Usage for HADRON Enabled Databases](/archive/blogs/psssql/alwayson-hadron-learning-series-worker-pool-usage-for-hadron-enabled-databases) (Always On - HADRON 學習系列：已啟用 HADRON 功能的資料庫背景工作集區使用方式)  
  
     [SQL Server Always On 小組部落格：官方 SQL Server Always On 小組部落格](/archive/blogs/sqlalwayson/)  
  
     [CSS SQL Server 工程師部落格](/archive/blogs/psssql/)  
  
-   **影片：**  
  
     [Microsoft SQL Server Code-Named "Denali" Always On Series,Part 1:Introducing the Next Generation High Availability Solution](https://channel9.msdn.com/Events/TechEd/NorthAmerica/2011/DBI302) (Microsoft SQL Server Code-Named "Denali" Always On 系列第 1 部分：新一代高可用性解決方案簡介)  
  
     [Microsoft SQL Server Code-Named "Denali" Always On Series,Part 2:Building a Mission-Critical High Availability Solution Using Always On](https://channel9.msdn.com/Events/TechEd/NorthAmerica/2011/DBI404) (Microsoft SQL Server Code-Named "Denali" Always On 系列第 2 部分：使用 Always On 建立關鍵任務的高可用性解決方案)  
  
-   **白皮書：**  
  
     [Microsoft SQL Server AlwaysOn 高可用性和災害復原方案指南](/previous-versions/sql/sql-server-2012/hh781257(v=msdn.10))  
  
     [Microsoft 的 SQL Server 2012 白皮書](https://social.technet.microsoft.com/wiki/contents/articles/13146.white-paper-gallery-for-sql-server.aspx#[Category]SQLServer2012)  
  
     [SQL Server 客戶諮詢團隊白皮書](https://techcommunity.microsoft.com/t5/DataCAT/bg-p/DataCAT/)  
  
## <a name="see-also"></a>另請參閱  
 [資料庫鏡像端點 &#40;SQL Server&#41;](../../../database-engine/database-mirroring/the-database-mirroring-endpoint-sql-server.md)   
 [AlwaysOn 可用性群組概觀 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server.md)   
 [可用性群組接聽程式、用戶端連接性及應用程式容錯移轉 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/listeners-client-connectivity-application-failover.md)   
 [AlwaysOn 可用性群組的必要條件、限制和建議 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/prereqs-restrictions-recommendations-always-on-availability.md)  
  
