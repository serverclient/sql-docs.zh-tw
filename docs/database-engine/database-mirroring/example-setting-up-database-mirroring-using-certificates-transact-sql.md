---
title: 範例：搭配憑證的資料庫鏡像 (T-SQL)
description: 本文包含一個範例，該範例會透過 SQL Server 中的 Transact-SQL，使用以憑證為基礎的驗證來設定 SQL Server 資料庫鏡像。
ms.custom: seo-lt-2019
ms.date: 05/17/2016
ms.prod: sql
ms.prod_service: high-availability
ms.reviewer: ''
ms.technology: database-mirroring
ms.topic: conceptual
helpviewer_keywords:
- database mirroring [SQL Server], deployment
- certificates [SQL Server], database mirroring
- authentication [SQL Server], database mirroring
- database mirroring [SQL Server], security
ms.assetid: df489ecd-deee-465c-a26a-6d1bef6d7b66
author: MikeRayMSFT
ms.author: mikeray
ms.openlocfilehash: a6b1093f9c648d8753b68b9c9905dfe072ce4210
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97639857"
---
# <a name="example-setting-up-database-mirroring-using-certificates-transact-sql"></a>範例：使用憑證設定資料庫鏡像 (Transact-SQL)
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
  此範例會顯示使用以憑證為基礎的驗證建立資料庫鏡像工作階段所需的所有階段。 此主題中的範例使用 [!INCLUDE[tsql](../../includes/tsql-md.md)]。 除非您可保證網路的安全無虞，否則建議您對資料庫鏡像連接使用加密。  
  
 將憑證複製到另一個系統時，請使用安全複製方法。 務必將您所有的憑證小心保管。  
  
##  <a name="example"></a><a name="ExampleH2"></a> 範例  
 下列範例示範必須針對位於 HOST_A 的一個夥伴完成什麼動作。 此範例中的兩個夥伴都是三個電腦系統中的預設伺服器執行個體。 兩個伺服器執行個體在不受信任的 Windows 網域上執行，因此必須有憑證驗證。  
  
 由 HOST_A 取得初始主體角色，而由 HOST_B 取得鏡像角色。  
  
 使用憑證設定資料庫鏡像包含四個一般階段，其中三個階段 (1、2 和 4) 將由這則範例示範。 這些階段如下：  
  
1.  [設定傳出連接](#ConfiguringOutboundConnections)  
  
     這個範例會說明下列作業的步驟：  
  
    1.  設定傳出連接的 Host_A。  
  
    2.  設定傳出連接的 Host_B。  
  
     如需有關此設定資料庫鏡像階段的詳細資訊，請參閱 [允許資料庫鏡像端點使用輸出連線的憑證 &#40;Transact-SQL&#41;](../../database-engine/database-mirroring/database-mirroring-use-certificates-for-outbound-connections.md)。  
  
2.  [設定傳入連接](#ConfigureInboundConnections)  
  
     這個範例會說明下列作業的步驟：  
  
    1.  設定傳入連接的 Host_A。  
  
    2.  設定傳入連接的 Host_B。  
  
     如需有關此設定資料庫鏡像階段的詳細資訊，請參閱 [允許資料庫鏡像端點使用輸入連線的憑證 &#40;Transact-SQL&#41;](../../database-engine/database-mirroring/database-mirroring-use-certificates-for-inbound-connections.md)。  
  
3.  建立鏡像資料庫  
  
     如需有關如何建立鏡像資料庫的資訊，請參閱 [準備鏡像資料庫以進行鏡像 &#40;SQL Server&#41;](../../database-engine/database-mirroring/prepare-a-mirror-database-for-mirroring-sql-server.md)。  
  
4.  [設定鏡像夥伴](#ConfigureMirroringPartners)  
  
###  <a name="configuring-outbound-connections"></a><a name="ConfiguringOutboundConnections"></a> 設定傳出連接  
 **若要設定傳出連接設定的 Host_A**  
  
1.  在 master 資料庫上，若有需要，請建立資料庫主要索引鍵。  
  
    ```  
    USE master;  
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<1_Strong_Password!>';  
    GO  
    ```  
  
2.  產生這個伺服器執行個體的憑證。  
  
    ```  
    USE master;  
    CREATE CERTIFICATE HOST_A_cert   
       WITH SUBJECT = 'HOST_A certificate';  
    GO  
    ```  
  
3.  使用憑證建立伺服器執行個體的鏡像端點。  
  
    ```  
    CREATE ENDPOINT Endpoint_Mirroring  
       STATE = STARTED  
       AS TCP (  
          LISTENER_PORT=7024  
          , LISTENER_IP = ALL  
       )   
       FOR DATABASE_MIRRORING (   
          AUTHENTICATION = CERTIFICATE HOST_A_cert  
          , ENCRYPTION = REQUIRED ALGORITHM AES  
          , ROLE = ALL  
       );  
    GO  
    ```  
  
4.  備份 HOST_A 憑證，並將它複製到其他系統 (HOST_B)。  
  
    ```  
    BACKUP CERTIFICATE HOST_A_cert TO FILE = 'C:\HOST_A_cert.cer';  
    GO  
    ```  
  
5.  使用任何安全複製方法，將 C:\HOST_A_cert.cer 複製到 HOST_B。  
  
 **若要設定傳出連接設定的 Host_B**  
  
1.  在 master 資料庫上，若有需要，請建立資料庫主要索引鍵。  
  
    ```  
    USE master;  
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<Strong_Password_#2>';  
    GO  
    ```  
  
2.  在 HOST_B 伺服器執行個體上產生憑證。  
  
    ```  
    CREATE CERTIFICATE HOST_B_cert   
       WITH SUBJECT = 'HOST_B certificate for database mirroring';  
    GO  
    ```  
  
3.  在 HOST_B 上建立伺服器執行個體的鏡像端點。  
  
    ```  
    CREATE ENDPOINT Endpoint_Mirroring  
       STATE = STARTED  
       AS TCP (  
          LISTENER_PORT=7024  
          , LISTENER_IP = ALL  
       )   
       FOR DATABASE_MIRRORING (   
          AUTHENTICATION = CERTIFICATE HOST_B_cert  
          , ENCRYPTION = REQUIRED ALGORITHM AES  
          , ROLE = ALL  
       );  
    GO  
    ```  
  
4.  備份 HOST_B 憑證。  
  
    ```  
    BACKUP CERTIFICATE HOST_B_cert TO FILE = 'C:\HOST_B_cert.cer';  
    GO   
    ```  
  
5.  使用任何安全複製方法，將 C:\HOST_B_cert.cer 複製到 HOST_A。  
  
 如需詳細資訊，請參閱 [允許資料庫鏡像端點使用輸出連線的憑證 &#40;Transact-SQL&#41;](../../database-engine/database-mirroring/database-mirroring-use-certificates-for-outbound-connections.md)。  
  
 [[範例頂端]](#ExampleH2)  
  
###  <a name="configuring-inbound-connections"></a><a name="ConfigureInboundConnections"></a> 設定傳入連接  
 **若要設定傳入連接設定的 Host_A**  
  
1.  在 HOST_A 上建立 HOST_B 的登入。  
  
    ```  
    USE master;  
    CREATE LOGIN HOST_B_login WITH PASSWORD = '1Sample_Strong_Password!@#';  
    GO  
    ```  
  
2.  --為該登入建立使用者。  
  
    ```  
    CREATE USER HOST_B_user FOR LOGIN HOST_B_login;  
    GO  
    ```  
  
3.  --將憑證與使用者產生關聯。  
  
    ```  
    CREATE CERTIFICATE HOST_B_cert  
       AUTHORIZATION HOST_B_user  
       FROM FILE = 'C:\HOST_B_cert.cer'  
    GO  
    ```  
  
4.  將 CONNECT 權限授與登入，以連接遠端鏡像端點。  
  
    ```  
    GRANT CONNECT ON ENDPOINT::Endpoint_Mirroring TO [HOST_B_login];  
    GO  
    ```  
  
 **若要設定傳入連接設定的 Host_B**  
  
1.  在 HOST_B 上建立 HOST_A 的登入。  
  
    ```  
    USE master;  
    CREATE LOGIN HOST_A_login WITH PASSWORD = '=Sample#2_Strong_Password2';  
    GO  
    ```  
  
2.  為該登入建立使用者。  
  
    ```  
    CREATE USER HOST_A_user FOR LOGIN HOST_A_login;  
    GO  
    ```  
  
3.  將憑證與使用者產生關聯。  
  
    ```  
    CREATE CERTIFICATE HOST_A_cert  
       AUTHORIZATION HOST_A_user  
       FROM FILE = 'C:\HOST_A_cert.cer'  
    GO  
    ```  
  
4.  將 CONNECT 權限授與登入，以連接遠端鏡像端點。  
  
    ```  
    GRANT CONNECT ON ENDPOINT::Endpoint_Mirroring TO [HOST_A_login];  
    GO  
    ```  
  
> [!IMPORTANT]  
>  如果您想要在具有自動容錯移轉的高安全性模式下執行，就必須重複相同的設定步驟，以便設定傳出和傳入連接的見證。 需要見證時若要設定傳入連接，您必須為兩個夥伴上的見證與見證上的兩個夥伴設定登入與使用者。  
  
 如需詳細資訊，請參閱 [允許資料庫鏡像端點使用輸入連線的憑證 &#40;Transact-SQL&#41;](../../database-engine/database-mirroring/database-mirroring-use-certificates-for-inbound-connections.md)。  
  
 [[範例頂端]](#ExampleH2)  
  
### <a name="creating-the-mirror-database"></a>建立鏡像資料庫  
 如需有關如何建立鏡像資料庫的資訊，請參閱 [準備鏡像資料庫以進行鏡像 &#40;SQL Server&#41;](../../database-engine/database-mirroring/prepare-a-mirror-database-for-mirroring-sql-server.md)。  
  
###  <a name="configuring-the-mirroring-partners"></a><a name="ConfigureMirroringPartners"></a> 設定鏡像夥伴  
  
1.  在 HOST_B 的鏡像伺服器執行個體上，設定 HOST_A 上的伺服器執行個體為夥伴 (設定初始主體伺服器執行個體)。 以有效網路位址取代 `TCP://HOST_A.Mydomain.Corp.Adventure-Works``.com:7024`。 如需詳細資訊，請參閱 [指定伺服器網路位址 &#40;資料庫鏡像&#41;](../../database-engine/database-mirroring/specify-a-server-network-address-database-mirroring.md)。  
  
    ```  
    --At HOST_B, set server instance on HOST_A as partner (principal server):  
    ALTER DATABASE AdventureWorks   
        SET PARTNER = 'TCP://HOST_A.Mydomain.Corp.Adventure-Works.com:7024';  
    GO  
    ```  
  
2.  在 HOST_A 的主體伺服器執行個體上，設定 HOST_B 上的伺服器執行個體為夥伴 (設定為初始鏡像伺服器執行個體)。 以有效網路位址取代 `TCP://HOST_B.Mydomain.Corp.Adventure-Works.com:7024`。  
  
    ```  
    --At HOST_A, set server instance on HOST_B as partner (mirror server).  
    ALTER DATABASE AdventureWorks   
        SET PARTNER = 'TCP://HOST_B.Mydomain.Corp.Adventure-Works.com:7024';  
    GO  
    ```  
  
3.  此範例假設工作階段將在高效能模式下執行。 若要設定此工作階段為高效能模式，請在主體伺服器執行個體上 (在 HOST_A 上) 設定交易安全性為 OFF。  
  
    ```  
    --Change to high-performance mode by turning off transacton safety.  
    ALTER DATABASE AdventureWorks   
        SET PARTNER SAFETY OFF  
    GO  
    ```  
  
    > [!NOTE]  
    >  如果您想要在具有自動容錯移轉的高安全性模式下執行，請保持交易安全性設定為 FULL (預設值)，並在執行第二個 SET PARTNER **'** _partner_server_ **'** 陳述式後儘快新增見證。 請注意，必須先為傳入與傳出設定見證。  
  
 [[範例頂端]](#ExampleH2)  
  
##  <a name="related-tasks"></a><a name="RelatedTasks"></a> 相關工作  
  
-   [準備鏡像資料庫以進行鏡像 &#40;SQL Server&#41;](../../database-engine/database-mirroring/prepare-a-mirror-database-for-mirroring-sql-server.md)  
  
-   [允許資料庫鏡像端點使用輸入連線的憑證 &#40;Transact-SQL&#41;](../../database-engine/database-mirroring/database-mirroring-use-certificates-for-inbound-connections.md)  
  
-   [允許資料庫鏡像端點使用輸出連線的憑證 &#40;Transact-SQL&#41;](../../database-engine/database-mirroring/database-mirroring-use-certificates-for-outbound-connections.md)  
  
-   [角色切換後針對登入和作業進行管理 &#40;SQL Server&#41;](../../sql-server/failover-clusters/management-of-logins-and-jobs-after-role-switching-sql-server.md)  
  
-   [在另一個伺服器執行個體上提供可用的資料庫時，管理中繼資料 &#40;SQL Server&#41;](../../relational-databases/databases/manage-metadata-when-making-a-database-available-on-another-server.md) (SQL Server)  
  
-   [疑難排解資料庫鏡像組態 &#40;SQL Server&#41;](../../database-engine/database-mirroring/troubleshoot-database-mirroring-configuration-sql-server.md)  
  
## <a name="see-also"></a>另請參閱  
 [資料庫鏡像和 Always On 可用性群組的傳輸安全性 &#40;SQL Server&#41;](../../database-engine/database-mirroring/transport-security-database-mirroring-always-on-availability.md)   
 [指定伺服器網路位址 &#40;資料庫鏡像&#41;](../../database-engine/database-mirroring/specify-a-server-network-address-database-mirroring.md)   
 [資料庫鏡像端點 &#40;SQL Server&#41;](../../database-engine/database-mirroring/the-database-mirroring-endpoint-sql-server.md)   
 [使用資料庫鏡像端點憑證 &#40;Transact-SQL&#41;](../../database-engine/database-mirroring/use-certificates-for-a-database-mirroring-endpoint-transact-sql.md)   
 [ALTER DATABASE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-database-transact-sql.md)   
 [SQL Server Database Engine 和 Azure SQL Database 的資訊安全中心](../../relational-databases/security/security-center-for-sql-server-database-engine-and-azure-sql-database.md)  
  
  
