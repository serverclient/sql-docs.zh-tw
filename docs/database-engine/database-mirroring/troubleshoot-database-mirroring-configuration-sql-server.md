---
title: 設定資料庫鏡像時的常見問題
description: 提供資訊以協助對設定資料庫鏡像工作階段的問題進行疑難排解。
ms.custom: seo-lt-2019
ms.date: 05/17/2016
ms.prod: sql
ms.prod_service: high-availability
ms.reviewer: ''
ms.technology: database-mirroring
ms.topic: conceptual
helpviewer_keywords:
- database mirroring [SQL Server], deployment
- endpoints [SQL Server], database mirroring
- database mirroring [SQL Server], troubleshooting
- troubleshooting [SQL Server], database mirroring
ms.assetid: 87d3801b-dc52-419e-9316-8b1f1490946c
author: MikeRayMSFT
ms.author: mikeray
ms.openlocfilehash: 2b50f06d3830a8f723d9d6ecc570125ecafb136a
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97641535"
---
# <a name="troubleshoot-database-mirroring-configuration-sql-server"></a>疑難排解資料庫鏡像組態 (SQL Server)
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
  本主題提供資訊以協助您對設定資料庫鏡像工作階段的問題進行疑難排解。  
  
> [!NOTE]  
>  確定您符合所有 [資料庫鏡像的必要條件](../../database-engine/database-mirroring/prerequisites-restrictions-and-recommendations-for-database-mirroring.md)。  
  
|問題|摘要|  
|-----------|-------------|  
|錯誤訊息 1418|此 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 訊息指出無法連繫伺服器網路位址或該位址不存在，並建議您確認網路位址名稱，然後重新發出命令。 |  
|[帳戶](#Accounts)|討論正確設定在底下執行 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 之帳戶的需求。|  
|[端點](#Endpoints)|討論正確設定每個伺服器執行個體之資料庫鏡像端點的需求。|  
|[系統位址](#SystemAddress)|摘要說明在資料庫鏡像組態中指定伺服器執行個體之系統名稱的替代方式。|  
|[網路存取](#NetworkAccess)|說明每個伺服器執行個體要透過 TCP 來存取其他伺服器執行個體之通訊埠的需求。|  
|[鏡像資料庫的準備工作](#MirrorDbPrep)|摘要說明準備鏡像資料庫以便開始進行鏡像作業的需求。|  
|[失敗的檔案建立作業](#FailedCreateFileOp)|描述如何回應失敗的檔案建立作業。|  
|[使用 Transact-SQL 啟動鏡像](#StartDbm)|描述 ALTER DATABASE *database_name* SET PARTNER **='** _partner_server_ **'** 陳述式的必要順序。|  
|[跨資料庫交易](#CrossDbTxns)|自動容錯移轉可能導致自動以及可能不正確地解決有疑問的交易。 因此，資料庫鏡像不支援跨資料庫交易。|  
  
##  <a name="accounts"></a><a name="Accounts"></a> 帳戶  
 必須正確設定用來執行 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的帳戶。  
  
1.  帳戶有正確的權限嗎？  
  
    1.  如果帳戶是在相同的網域帳戶中執行，則錯誤設定的可能性較低。  
  
    2.  如果帳戶是在不同的網域中執行，或不是網域帳戶，則必須在其他電腦的 **master** 中建立一個帳戶的登入，而且必須將端點的 CONNECT 權限授與該登入。 如需詳細資訊，請參閱 [在另一個伺服器執行個體上提供可用的資料庫時，管理中繼資料 &#40;SQL Server&#41;](../../relational-databases/databases/manage-metadata-when-making-a-database-available-on-another-server.md)。 這包括 Network Service 帳戶。  
  
2.  如果 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 使用本機系統帳戶以服務的方式執行，您必須使用憑證進行驗證。 如需詳細資訊，請參閱[使用資料庫鏡像端點憑證 &#40;Transact-SQL&#41;](../../database-engine/database-mirroring/use-certificates-for-a-database-mirroring-endpoint-transact-sql.md)。  
  
##  <a name="endpoints"></a><a name="Endpoints"></a> Endpoints  
 必須正確設定端點。  
  
1.  確定每個伺服器執行個體 (主體伺服器、鏡像伺服器和任何見證) 都有資料庫鏡像端點。 如需詳細資訊，請參閱 [sys.database_mirroring_endpoints &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-database-mirroring-endpoints-transact-sql.md) 以及[建立 Windows 驗證的資料庫鏡像端點 &#40;Transact-SQL&#41;](../../database-engine/database-mirroring/create-a-database-mirroring-endpoint-for-windows-authentication-transact-sql.md) 或[使用資料庫鏡像端點憑證 &#40;Transact-SQL&#41;](../../database-engine/database-mirroring/use-certificates-for-a-database-mirroring-endpoint-transact-sql.md) (根據驗證格式而定)。  
  
2.  檢查通訊埠編號是否正確。  
  
     若要識別目前與伺服器執行個體的資料庫鏡像端點相關聯的通訊埠，請使用 [sys.database_mirroring_endpoints](../../relational-databases/system-catalog-views/sys-database-mirroring-endpoints-transact-sql.md) 和 [sys.tcp_endpoints](../../relational-databases/system-catalog-views/sys-tcp-endpoints-transact-sql.md) 目錄檢視。  
  
3.  對於難以解釋的資料庫鏡像設定問題，我們建議您檢查每個伺服器執行個體，以判斷它是否正在接聽正確的通訊埠。   
  
4.  確定端點已啟動 (STATE=STARTED)。 在每個伺服器執行個體上，使用下列 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式。  
  
    ```  
    SELECT state_desc FROM sys.database_mirroring_endpoints  
    ```  
  
     如需 **state_desc** 資料行的詳細資訊，請參閱 [sys.database_mirroring_endpoints &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-database-mirroring-endpoints-transact-sql.md)。  
  
     若要啟動端點，請使用下列 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式。  
  
    ```  
    ALTER ENDPOINT Endpoint_Mirroring   
    STATE = STARTED   
    AS TCP (LISTENER_PORT = <port_number>)  
    FOR database_mirroring (ROLE = ALL);  
    GO  
    ```  
  
     如需詳細資訊，請參閱 [ALTER ENDPOINT &#40;Transact-SQL&#41;](../../t-sql/statements/alter-endpoint-transact-sql.md)。  
  
5.  檢查 ROLE 是否正確。 在每個伺服器執行個體上，使用下列 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式。  
  
    ```  
    SELECT role FROM sys.database_mirroring_endpoints;  
    GO  
    ```  
  
     如需詳細資訊，請參閱 [sys.database_mirroring_endpoints &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-database-mirroring-endpoints-transact-sql.md)。  
  
6.  來自其他伺服器執行個體的服務帳戶登入需要 CONNECT 權限。 確定來自其他伺服器的登入具有 CONNECT 權限。 若要判斷誰具有端點的 CONNECT 權限，請在每個伺服器執行個體上使用下列 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式。  
  
    ```  
    SELECT 'Metadata Check';  
    SELECT EP.name, SP.STATE,   
       CONVERT(nvarchar(38), suser_name(SP.grantor_principal_id))   
          AS GRANTOR,   
       SP.TYPE AS PERMISSION,  
       CONVERT(nvarchar(46),suser_name(SP.grantee_principal_id))   
          AS GRANTEE   
       FROM sys.server_permissions SP , sys.endpoints EP  
       WHERE SP.major_id = EP.endpoint_id  
       ORDER BY Permission,grantor, grantee;   
    GO  
  
    ```  
  
##  <a name="system-address"></a><a name="SystemAddress"></a> 系統位址  
 您可以使用能夠明確識別系統的任何名稱，來當做資料庫鏡像組態中伺服器執行個體的系統名稱。 伺服器位址可以是系統名稱 (如果系統位於同一個網域內)、完整網域名稱或 IP 位址 (最好是靜態 IP 位址)。 使用完整網域名稱保證可以運作。 如需詳細資訊，請參閱 [指定伺服器網路位址 &#40;資料庫鏡像&#41;](../../database-engine/database-mirroring/specify-a-server-network-address-database-mirroring.md)。  
  
##  <a name="network-access"></a><a name="NetworkAccess"></a> Network Access  
 每個伺服器執行個體都必須能夠透過 TCP 來存取其他伺服器執行個體的通訊埠。 如果伺服器執行個體位在互不信任的不同網域 (不受信任的網域) 中，這點尤其重要。 因為這會限制伺服器執行個體之間的許多通訊。  
  
##  <a name="mirror-database-preparation"></a><a name="MirrorDbPrep"></a> Mirror Database Preparation  
 不論您是第一次啟動鏡像或在移除鏡像後再次啟動鏡像，請確認已備妥鏡像資料庫，可進行鏡像作業。  
  
 在鏡像伺服器上建立鏡像資料庫時，請確定您使用 WITH NORECOVERY 指定了相同的資料庫名稱來還原主體資料庫的備份。 另外，您還必須使用 WITH NORECOVERY 套用製作該備份之後建立的所有記錄備份。  
  
 另外，建議鏡像資料庫的檔案路徑 (包括磁碟機代號) 最好和主體資料庫的路徑完全相同。 如果檔案路徑必須不同，例如，如果主體資料庫在磁碟機 'F:'，但鏡像系統沒有 F: 磁碟機，您就必須在 RESTORE 陳述式中包含 MOVE 選項。  
  
> [!IMPORTANT]  
>  如果您在建立鏡像資料庫時移動資料庫檔案，那麼稍後若不暫停鏡像作業，可能就無法將檔案加入資料庫。  
  
 如果資料庫鏡像已經停止，則必須先將在主體資料庫上製作的所有後續記錄備份套用到鏡像資料庫，然後才能重新啟動鏡像。  
  
 如需詳細資訊，請參閱 [準備鏡像資料庫以進行鏡像 &#40;SQL Server&#41;](../../database-engine/database-mirroring/prepare-a-mirror-database-for-mirroring-sql-server.md)。  
  
##  <a name="failed-create-file-operation"></a><a name="FailedCreateFileOp"></a> Failed Create-File Operation  
 若要加入檔案但又不影響鏡像工作階段，則此檔案的路徑必須同時存在兩個伺服器上。 因此，如果您在建立鏡像資料庫時移動資料庫檔案，之後在鏡像資料庫上加入檔案的作業可能會失敗，而且導致鏡像暫停。  
  
 若要修正這個問題：  
  
1.  資料庫擁有者必須移除鏡像工作階段，並且還原包含加入之檔案的檔案群組完整備份。  
  
2.  然後，此擁有者必須在主體伺服器上備份包含加入檔案作業的記錄，並且使用 WITH NORECOVERY 和 WITH MOVE 選項來手動在鏡像資料庫上還原記錄備份。 這樣做會在鏡像伺服器上建立指定的檔案路徑，並且將新的檔案還原至該位置。  
  
3.  若要針對新的鏡像工作階段準備資料庫，此擁有者也必須使用 WITH NO RECOVERY，從主體伺服器還原任何其他未完成的記錄備份。  
  
 如需詳細資訊，請參閱[移除資料庫鏡像 &#40;SQL Server&#41;](../../database-engine/database-mirroring/removing-database-mirroring-sql-server.md)、[準備鏡像資料庫以進行鏡像 &#40;SQL Server&#41;](../../database-engine/database-mirroring/prepare-a-mirror-database-for-mirroring-sql-server.md)、[使用 Windows 驗證建立資料庫鏡像工作階段 &#40;Transact-SQL&#41;](../../database-engine/database-mirroring/database-mirroring-establish-session-windows-authentication.md)、[使用資料庫鏡像端點憑證 &#40;Transact-SQL&#41;](../../database-engine/database-mirroring/use-certificates-for-a-database-mirroring-endpoint-transact-sql.md) 或[使用 Windows 驗證建立資料庫鏡像工作階段 &#40;SQL Server Management Studio&#41;](../../database-engine/database-mirroring/establish-database-mirroring-session-windows-authentication.md)。  
  
##  <a name="starting-mirroring-by-using-transact-sql"></a><a name="StartDbm"></a> 使用 Transact-SQL 啟動鏡像  
 ALTER DATABASE *database_name* SET PARTNER **='** _partner_server_ **'** 陳述式發出的順序非常重要。  
  
1.  第一個陳述式必須在鏡像伺服器上執行。 發出這個陳述式時，鏡像伺服器並不會嘗試聯繫任何其他伺服器執行個體。 鏡像伺服器卻會指示其資料庫等到主體伺服器聯繫上鏡像伺服器為止。  
  
2.  第二個 ALTER DATABASE 陳述式必須在主體伺服器上執行。 這個陳述式會讓主體伺服器嘗試連接鏡像伺服器。 建立該連接之後，鏡像便會嘗試在另一個連接上連接到主體伺服器。  
  
 如需詳細資訊，請參閱 [ALTER DATABASE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-database-transact-sql.md)。  
  
> [!NOTE]  
>  如需使用 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 啟動鏡像的相關資訊，請參閱[使用 Windows 驗證建立資料庫鏡像工作階段 &#40;SQL Server Management Studio&#41;](../../database-engine/database-mirroring/establish-database-mirroring-session-windows-authentication.md)。  
  
##  <a name="cross-database-transactions"></a><a name="CrossDbTxns"></a> 跨資料庫交易  
 當資料庫正在具有自動容錯移轉的高安全性模式下進行鏡像時，自動容錯移轉會造成自動解析未確定的交易，但解析可能不正確。 如果在認可跨資料庫交易時，其中一個資料庫進行了自動容錯移轉，則資料庫之間可能會發生邏輯不一致的情況。  
  
 可能受到自動容錯移轉影響的跨資料庫交易類型包括：  
  
-   在相同 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]執行個體中對多個資料庫進行更新的交易。  
  
-   使用 [!INCLUDE[msCoName](../../includes/msconame-md.md)] 分散式交易協調器 (MS DTC) 的交易。  
  
 如需詳細資訊，請參閱[資料庫鏡像或 AlwaysOn 可用性群組不支援跨資料庫交易 &#40;SQL Server&#41;](../../database-engine/availability-groups/windows/transactions-always-on-availability-and-database-mirroring.md)。  
  
## <a name="see-also"></a>另請參閱  
 [設定資料庫鏡像 &#40;SQL Server&#41;](../../database-engine/database-mirroring/setting-up-database-mirroring-sql-server.md)   
 [資料庫鏡像和 AlwaysOn 可用性群組的傳輸安全性 &#40;SQL Server&#41;](../../database-engine/database-mirroring/transport-security-database-mirroring-always-on-availability.md)  
  
  


