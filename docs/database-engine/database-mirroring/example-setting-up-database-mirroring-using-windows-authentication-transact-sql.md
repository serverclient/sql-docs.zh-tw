---
title: 使用 Windows 驗證設定資料庫鏡像 (T-SQL)
description: 此文章包含在 SQL Server 中使用 Windows 驗證搭配 Transact-SQL 建立含有見證之資料庫鏡像工作階段的範例。
ms.custom: seo-lt-2019
ms.date: 05/17/2016
ms.prod: sql
ms.prod_service: high-availability
ms.reviewer: ''
ms.technology: database-mirroring
ms.topic: conceptual
helpviewer_keywords:
- database mirroring [SQL Server], deployment
- Windows authentication [SQL Server]
- authentication [SQL Server], database mirroring
- database mirroring [SQL Server], security
ms.assetid: 35800769-aede-4aac-b077-0e0e487e302f
author: MikeRayMSFT
ms.author: mikeray
ms.openlocfilehash: 128fb94e9e99315a0a8f9d24067fc863467fb349
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97641700"
---
# <a name="example-configure-database-mirroring-using-windows-authentication-transact-sql"></a>範例：使用 Windows 驗證設定資料庫鏡像 (Transact-SQL)
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
  此範例顯示使用 Windows 驗證建立具有見證的資料庫鏡像工作階段的所有必要階段。 此主題中的範例使用 [!INCLUDE[tsql](../../includes/tsql-md.md)]。 請注意，使用 [!INCLUDE[tsql](../../includes/tsql-md.md)] 步驟的另一種方法是，您可以使用 [設定資料庫鏡像安全性精靈] 來設定資料庫鏡像。 如需詳細資訊，請參閱本主題稍後的 [使用 Windows 驗證建立資料庫鏡像工作階段 &#40;SQL Server Management Studio&#41;](../../database-engine/database-mirroring/establish-database-mirroring-session-windows-authentication.md)。  
  
## <a name="prerequisite"></a>必要條件  
 此範例使用 **AdventureWorks** 範例資料庫，依預設採用簡單復原模式。 若要以此資料庫來使用資料庫鏡像，您必須改用完整復原模式。 若要在 [!INCLUDE[tsql](../../includes/tsql-md.md)] 中這樣做，請使用 ALTER DATABASE 陳述式，如下所示：  
  
```  
USE master;  
GO  
ALTER DATABASE AdventureWorks   
SET RECOVERY FULL;  
GO  
```  
  
 如需在 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 中變更復原模式的資訊，請參閱[檢視或變更資料庫的復原模式 &#40;SQL Server&#41;](../../relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server.md)。  
  
### <a name="permissions"></a>權限  
 需要資料庫的 ALTER 權限及 CREATE ENDPOINT 權限，或是 **sysadmin** 固定伺服器角色的成員資格。  
  
## <a name="example"></a>範例  
 此範例中的兩個夥伴伺服器和見證伺服器，各為三個電腦系統中的預設伺服器執行個體。 這三個伺服器執行個體都執行相同的 Windows 網域，但範例之見證伺服器執行個體的使用者帳戶 (做為啟動服務帳戶使用) 與其他兩者不同。  
  
 下表摘要說明此例中所使用的值。  
  
|初始鏡像角色|主機系統|網域使用者帳戶|  
|----------------------------|-----------------|-------------------------|  
|主體|PARTNERHOST1|*\<Mydomain>\\<dbousername\>*|  
|鏡像|PARTNERHOST5|*\<Mydomain>\\<dbousername\>*|  
|見證|WITNESSHOST4|*\<Somedomain>\\<witnessuser\>*|  
  
1.  在主體伺服器執行個體 (PARTNERHOST1 的預設執行個體) 上建立端點。  
  
    ```  
    CREATE ENDPOINT Endpoint_Mirroring  
        STATE=STARTED   
        AS TCP (LISTENER_PORT=7022)   
        FOR DATABASE_MIRRORING (ROLE=PARTNER)  
    GO  
    --Partners under same domain user; login already exists in master.  
    --Create a login for the witness server instance,  
    --which is running as Somedomain\witnessuser:  
    USE master ;  
    GO  
    CREATE LOGIN [Somedomain\witnessuser] FROM WINDOWS ;  
    GO  
    -- Grant connect permissions on endpoint to login account of witness.  
    GRANT CONNECT ON ENDPOINT::Endpoint_Mirroring TO [Somedomain\witnessuser];  
    --Grant connect permissions on endpoint to login account of partners.  
    GRANT CONNECT ON ENDPOINT::Endpoint_Mirroring TO [Mydomain\dbousername];  
    GO  
    ```  
  
2.  在鏡像伺服器執行個體 (PARTNERHOST5 的預設執行個體) 上建立端點。  
  
    ```  
    CREATE ENDPOINT Endpoint_Mirroring  
        STATE=STARTED   
        AS TCP (LISTENER_PORT=7022)   
        FOR DATABASE_MIRRORING (ROLE=ALL)  
    GO  
    --Partners under same domain user; login already exists in master.  
    --Create a login for the witness server instance,  
    --which is running as Somedomain\witnessuser:  
    USE master ;  
    GO  
    CREATE LOGIN [Somedomain\witnessuser] FROM WINDOWS ;  
    GO  
    --Grant connect permissions on endpoint to login account of witness.  
    GRANT CONNECT ON ENDPOINT::Endpoint_Mirroring TO [Somedomain\witnessuser];  
    --Grant connect permissions on endpoint to login account of partners.  
    GRANT CONNECT ON ENDPOINT::Endpoint_Mirroring TO [Mydomain\dbousername];  
    GO  
    ```  
  
3.  在見證伺服器執行個體 (WITNESSHOST4 的預設執行個體) 上建立端點。  
  
    ```  
    CREATE ENDPOINT Endpoint_Mirroring  
        STATE=STARTED   
        AS TCP (LISTENER_PORT=7022)   
        FOR DATABASE_MIRRORING (ROLE=WITNESS)  
    GO  
    --Create a login for the partner server instances,  
    --which are both running as Mydomain\dbousername:  
    USE master ;  
    GO  
    CREATE LOGIN [Mydomain\dbousername] FROM WINDOWS ;  
    GO  
    --Grant connect permissions on endpoint to login account of partners.  
    GRANT CONNECT ON ENDPOINT::Endpoint_Mirroring TO [Mydomain\dbousername];  
    GO  
    ```  
  
4.  建立鏡像資料庫。 如需詳細資訊，請參閱 [準備鏡像資料庫以進行鏡像 &#40;SQL Server&#41;](../../database-engine/database-mirroring/prepare-a-mirror-database-for-mirroring-sql-server.md)。  
  
5.  在 PARTNERHOST5 鏡像伺服器執行個體上，將 PARTNERHOST1 的伺服器執行個體設為夥伴伺服器 (也就是將其設為初始主體伺服器執行個體)。  
  
    ```  
    ALTER DATABASE AdventureWorks   
        SET PARTNER =   
        'TCP://PARTNERHOST1.COM:7022'  
    GO  
    ```  
  
6.  在 PARTNERHOST1 的主體伺服器執行個體上，將 PARTNERHOST5 的伺服器執行個體設為夥伴伺服器 (也就是將其設為初始鏡像伺服器執行個體)。  
  
    ```  
    ALTER DATABASE AdventureWorks   
        SET PARTNER = 'TCP://PARTNERHOST5.COM:7022'  
    GO  
    ```  
  
7.  在主體伺服器上，設定見證伺服器 (位於 WITNESSHOST4)。  
  
    ```  
    ALTER DATABASE AdventureWorks   
        SET WITNESS =   
        'TCP://WITNESSHOST4.COM:7022'  
    GO  
    ```  
  
##  <a name="related-tasks"></a><a name="RelatedTasks"></a> 相關工作  
  
-   [準備鏡像資料庫以進行鏡像 &#40;SQL Server&#41;](../../database-engine/database-mirroring/prepare-a-mirror-database-for-mirroring-sql-server.md)  
  
-   [啟動設定資料庫鏡像安全性精靈 &#40;SQL Server Management Studio&#41;](../../database-engine/database-mirroring/start-the-configuring-database-mirroring-security-wizard.md)  
  
-   [設定鏡像資料庫以使用 Trustworthy 屬性 &#40;Transact-SQL&#41;](../../database-engine/database-mirroring/set-up-a-mirror-database-to-use-the-trustworthy-property-transact-sql.md)  
  
-   [允許資料庫鏡像端點使用輸出連線的憑證 &#40;Transact-SQL&#41;](../../database-engine/database-mirroring/database-mirroring-use-certificates-for-outbound-connections.md)  
  
-   [允許資料庫鏡像端點使用輸入連線的憑證 &#40;Transact-SQL&#41;](../../database-engine/database-mirroring/database-mirroring-use-certificates-for-inbound-connections.md)  
  
-   [範例：使用憑證設定資料庫鏡像 &#40;Transact-SQL&#41;](../../database-engine/database-mirroring/example-setting-up-database-mirroring-using-certificates-transact-sql.md)  
  
## <a name="see-also"></a>另請參閱  
 [ALTER DATABASE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-database-transact-sql.md)   
 [資料庫鏡像端點 &#40;SQL Server&#41;](../../database-engine/database-mirroring/the-database-mirroring-endpoint-sql-server.md)   
 [資料庫鏡像和 Always On 可用性群組的傳輸安全性 &#40;SQL Server&#41;](../../database-engine/database-mirroring/transport-security-database-mirroring-always-on-availability.md)   
 [在另一個伺服器執行個體上提供可用的資料庫時，管理中繼資料 &#40;SQL Server&#41;](../../relational-databases/databases/manage-metadata-when-making-a-database-available-on-another-server.md)   
 [SQL Server Database Engine 和 Azure SQL Database 的資訊安全中心](../../relational-databases/security/security-center-for-sql-server-database-engine-and-azure-sql-database.md)  
  
  
