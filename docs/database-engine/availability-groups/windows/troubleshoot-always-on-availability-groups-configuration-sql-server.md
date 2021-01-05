---
title: 可用性群組的常見問題 & 解決方案
description: 針對在 SQL Server 中設定 Always On 伺服器執行個體的可用性群組時常遇到的問題進行疑難排解。
ms.custom: seo-lt-2019
ms.date: 05/17/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: conceptual
helpviewer_keywords:
- troubleshooting [SQL Server], deploying
- Availability Groups [SQL Server], troubleshooting
- Availability Groups [SQL Server], configuring
ms.assetid: 8c222f98-7392-4faf-b7ad-5fb60ffa237e
author: cawrites
ms.author: chadam
ms.openlocfilehash: 0fbdfdced5e0563e86775e75aaf187d6f26a0a38
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97641513"
---
# <a name="troubleshoot-always-on-availability-groups-configuration-sql-server"></a>疑難排解 AlwaysOn 可用性群組組態 (SQL Server)
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]

  此主題中的資訊可協助您疑難排解為 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)]設定伺服器執行個體時常見的問題。 一般組態問題包含 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] 未啟用、不正確地設定帳戶、資料庫鏡像端點不存在、端點無法存取 (SQL Server 錯誤 1418)、網路存取不存在，以及聯結資料庫命令失敗 (SQL Server 錯誤 35250)。  
  
> [!NOTE]  
>  請確定您符合 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] 必要條件。 如需詳細資訊，請參閱 [AlwaysOn 可用性群組的必要條件、限制和建議 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/prereqs-restrictions-recommendations-always-on-availability.md)。  
  
 **本主題內容：**  
  
|區段|描述|  
|-------------|-----------------|  
|[AlwaysOn 可用性群組未啟用](#IsHadrEnabled)|如果 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體未啟用 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)]，此執行個體不會支援可用性群組建立，也無法裝載任何可用性複本。|  
|[帳戶](#Accounts)|討論正確設定在底下執行 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 之帳戶的需求。|  
|[端點](#Endpoints)|討論如何診斷伺服器執行個體的資料庫鏡像端點問題。|  
|[系統名稱](#SystemName)|摘要說明在端點 URL 中指定伺服器執行個體之系統名稱的替代方式。|  
|[網路存取](#NetworkAccess)|說明裝載可用性複本的每個伺服器執行個體必須要透過 TCP 存取其他伺服器執行個體之通訊埠的需求。|  
|[端點存取 (SQL Server 錯誤 1418)](#Msg1418)|包含有關此 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 錯誤訊息的資訊。|  
|[聯結資料庫失敗 (SQL Server 錯誤 35250)](#JoinDbFails)|討論因為與主要複本的連接不在作用中，次要資料庫聯結至可用性群組失敗的可能原因和解決方案。|  
|[唯讀路由未正確運作](#ROR)||  
|[相關工作](#RelatedTasks)|在《 [!INCLUDE[ssCurrent](../../../includes/sscurrent-md.md)] 線上叢書》中包含工作導向主題的清單，這些主題與疑難排解可用性群組組態特別相關。|  
|[相關內容](#RelatedContent)|包含《 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 線上叢書》外部的相關資源清單。|  
  
##  <a name="always-on-availability-groups-is-not-enabled"></a><a name="IsHadrEnabled"></a> AlwaysOn 可用性群組未啟用  
 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] 功能必須在每個 [!INCLUDE[ssCurrent](../../../includes/sscurrent-md.md)]執行個體上啟用。 如需詳細資訊，請參閱[啟用和停用 AlwaysOn 可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/enable-and-disable-always-on-availability-groups-sql-server.md)。  
  
##  <a name="accounts"></a><a name="Accounts"></a> 帳戶  
 必須正確設定用來執行 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 的帳戶。  
  
1.  帳戶有正確的權限嗎？  
  
    1.  如果夥伴是以相同的網域使用者帳戶身分執行，則兩個 **master** 資料庫中會自動存在正確的使用者登入。 這樣可簡化資料庫的安全性組態，建議您使用。  
  
    2.  如果兩個伺服器執行個體是以不同的帳戶身分執行，則必須在遠端伺服器執行個體的 **master** 中建立每一個帳戶的登入，而且該登入必須被授與 CONNECT 權限，才能連接到該伺服器執行個體的資料庫鏡像端點。 如需詳細資訊，請參閱[設定資料庫鏡像或 AlwaysOn 可用性群組的登入帳戶 &#40;SQL Server&#41;](../../../database-engine/database-mirroring/set-up-login-accounts-database-mirroring-always-on-availability.md)。  
  
2.  如果 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 服務是以內建帳戶執行 (例如本機系統、本機服務或網路服務，或是非網域帳戶)，您必須將憑證用於端點驗證。 如果服務帳戶使用同一個網域中的網域帳戶，您可以選擇在所有複本位置上授與每一個服務帳戶的 CONNECT 存取，或者也可以使用憑證。 如需詳細資訊，請參閱[使用資料庫鏡像端點憑證 &#40;Transact-SQL&#41;](../../../database-engine/database-mirroring/use-certificates-for-a-database-mirroring-endpoint-transact-sql.md)。  
  
##  <a name="endpoints"></a><a name="Endpoints"></a> Endpoints  
 必須正確設定端點。  
  
1.  確定將要裝載可用性複本的每個 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體 (每個「複本位置」)，都擁有資料庫鏡像端點。 若要判斷指定的伺服器執行個體上是否有資料庫鏡像端點，請使用 [sys.database_mirroring_endpoints](../../../relational-databases/system-catalog-views/sys-database-mirroring-endpoints-transact-sql.md) 目錄檢視。 如需詳細資訊，請參閱[建立 Windows 驗證的資料庫鏡像端點 &#40;Transact-SQL&#41;](../../../database-engine/database-mirroring/create-a-database-mirroring-endpoint-for-windows-authentication-transact-sql.md) 或[允許資料庫鏡像端點使用輸出連線的憑證 &#40;Transact-SQL&#41;](../../../database-engine/database-mirroring/database-mirroring-use-certificates-for-outbound-connections.md)。  
  
2.  檢查通訊埠編號是否正確。  
  
     若要識別目前關聯於伺服器執行個體之資料庫鏡像端點的通訊埠，請使用下列 [!INCLUDE[tsql](../../../includes/tsql-md.md)] 陳述式：  
  
    ```  
    SELECT type_desc, port FROM sys.tcp_endpoints;  
    GO  
    ```  
  
3.  對於難以解釋的 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] 設定問題，我們建議您檢查每個伺服器執行個體，以判斷它是否正在正確的通訊埠上接聽。  
  
4.  確定端點已啟動 (STATE=STARTED)。 在每個伺服器執行個體上，使用下列 [!INCLUDE[tsql](../../../includes/tsql-md.md)] 陳述式：  
  
    ```  
    SELECT state_desc FROM sys.database_mirroring_endpoints  
    ```  
  
     如需 **state_desc** 資料行的詳細資訊，請參閱 [sys.database_mirroring_endpoints &#40;Transact-SQL&#41;](../../../relational-databases/system-catalog-views/sys-database-mirroring-endpoints-transact-sql.md)。  
  
     若要啟動端點，請使用下列 [!INCLUDE[tsql](../../../includes/tsql-md.md)] 陳述式：  
  
    ```  
    ALTER ENDPOINT Endpoint_Mirroring   
    STATE = STARTED   
    AS TCP (LISTENER_PORT = <port_number>)  
    FOR database_mirroring (ROLE = ALL);  
    GO  
    ```  
  
     如需詳細資訊，請參閱 [ALTER ENDPOINT &#40;Transact-SQL&#41;](../../../t-sql/statements/alter-endpoint-transact-sql.md)。  
  
5.  確定來自其他伺服器的登入具有 CONNECT 權限。 若要判斷誰具有端點的 CONNECT 權限，請在每個伺服器執行個體上使用下列 [!INCLUDE[tsql](../../../includes/tsql-md.md)] 陳述式：  
  
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
  
##  <a name="system-name"></a><a name="SystemName"></a> System Name  
 您可以使用能夠明確識別系統的任何名稱，來當做端點 URL 中伺服器執行個體的系統名稱。 伺服器位址可以是系統名稱 (如果系統位於同一個網域內)、完整網域名稱或 IP 位址 (最好是靜態 IP 位址)。 使用完整網域名稱保證可以運作。 如需詳細資訊，請參閱 [在加入或修改可用性複本時指定端點 URL &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/specify-endpoint-url-adding-or-modifying-availability-replica.md)設定伺服器執行個體時常見的問題。  
  
##  <a name="network-access"></a><a name="NetworkAccess"></a> Network Access  
 裝載可用性複本的每個伺服器執行個體必須要透過 TCP 存取其他伺服器執行個體之通訊埠。 如果伺服器執行個體位在互不信任的不同網域 (不受信任的網域) 中，這點尤其重要。  
  
##  <a name="endpoint-access-sql-server-error-1418"></a><a name="Msg1418"></a> 端點存取 (SQL Server 錯誤 1418)  
 此 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 訊息指出無法連繫端點 URL 中指定的伺服器網路位址或該位址不存在，並建議您確認網路位址名稱，然後重新發出命令。  
  
##  <a name="join-database-fails-sql-server-error-35250"></a><a name="JoinDbFails"></a> 聯結資料庫失敗 (SQL Server 錯誤 35250)  
 本節討論因為與主要複本的連接不在作用中，次要資料庫聯結至可用性群組失敗的可能原因和解決方案。  
  
 **解決方案：**  
  
1.  檢查防火牆設定是否允許裝載主要複本和次要複本的伺服器執行個體之間的端點通訊埠通訊 (預設為通訊埠 5022)。  
  
2.  檢查網路服務帳戶是否有端點的連接權限。  
  
##  <a name="read-only-routing-is-not-working-correctly"></a><a name="ROR"></a> 唯讀路由未正確運作  
 確認下列組態值設定，並視需要加以更正。  
  
|開啟...|動作|註解|連結|  
|---------|------------|--------------|----------|  
|目前主要複本|確定可用性群組接聽程式在線上。|**確認接聽程式是否在線上：**<br /><br /> `SELECT * FROM sys.dm_tcp_listener_states;`<br /><br /> **重新啟動離線接聽程式：**<br /><br /> `ALTER AVAILABILITY GROUP myAG RESTART LISTENER 'myAG_Listener';`|[sys.dm_tcp_listener_states &#40;Transact-SQL&#41;](../../../relational-databases/system-dynamic-management-views/sys-dm-tcp-listener-states-transact-sql.md)<br /><br /> [ALTER AVAILABILITY GROUP &#40;Transact-SQL&#41;](../../../t-sql/statements/alter-availability-group-transact-sql.md)|  
|目前主要複本|確認 READ_ONLY_ROUTING_LIST 只包含裝載可讀取之次要複本的伺服器執行個體。|**識別可讀取的次要複本** ：sys.availability_replicas (**secondary_role_allow_connections_desc** 資料行)<br /><br /> **檢視唯讀路由清單：** sys.availability_read_only_routing_lists<br /><br /> **若要變更唯讀路由清單：** ALTER AVAILABILITY GROUP|[sys.availability_replicas &#40;Transact-SQL&#41;](../../../relational-databases/system-catalog-views/sys-availability-replicas-transact-sql.md)<br /><br /> [sys.availability_read_only_routing_lists &#40;Transact-SQL&#41;](../../../relational-databases/system-catalog-views/sys-availability-read-only-routing-lists-transact-sql.md)<br /><br /> [ALTER AVAILABILITY GROUP &#40;Transact-SQL&#41;](../../../t-sql/statements/alter-availability-group-transact-sql.md)|  
|read_only_routing_list 中的每個複本|確定 Windows 防火牆未封鎖 READ_ONLY_ROUTING_URL 通訊埠。|-|[設定用於 Database Engine 存取的 Windows 防火牆](../../../database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access.md)|  
|read_only_routing_list 中的每個複本|在 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 組態管理員中，確認：<br /><br /> SQL Server 遠端連接已啟用。<br /><br /> TCP/IP 已啟用。<br /><br /> IP 位址已正確設定。|-|[檢視或變更伺服器屬性 &#40;SQL Server&#41;](../../../database-engine/configure-windows/view-or-change-server-properties-sql-server.md)<br /><br /> [設定伺服器接聽特定 TCP 通訊埠 &#40;SQL Server 組態管理員&#41;](../../../database-engine/configure-windows/configure-a-server-to-listen-on-a-specific-tcp-port.md)|  
|read_only_routing_list 中的每個複本|確定 READ_ONLY_ROUTING_URL (TCP <strong>://</strong>*system-address*<strong>:</strong>*port*) 包含正確的完整網域名稱 (FQDN) 和通訊埠編號。|-|[Calculating read_only_routing_url for Always On](/archive/blogs/mattn/calculating-read_only_routing_url-for-alwayson)<br /><br /> [sys.availability_replicas &#40;Transact-SQL&#41;](../../../relational-databases/system-catalog-views/sys-availability-replicas-transact-sql.md)<br /><br /> [ALTER AVAILABILITY GROUP &#40;Transact-SQL&#41;](../../../t-sql/statements/alter-availability-group-transact-sql.md)|  
|用戶端系統|確認用戶端驅動程式支援唯讀路由。|-|[AlwaysOn 用戶端連接性 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/always-on-client-connectivity-sql-server.md)|  
  
##  <a name="related-tasks"></a><a name="RelatedTasks"></a> 相關工作  
  
-   [建立及設定可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/creation-and-configuration-of-availability-groups-sql-server.md)  
  
-   [建立 Windows 驗證的資料庫鏡像端點 &#40;Transact-SQL&#41;](../../../database-engine/database-mirroring/create-a-database-mirroring-endpoint-for-windows-authentication-transact-sql.md)  
  
-   [在新增或修改可用性複本時指定端點 URL &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/specify-endpoint-url-adding-or-modifying-availability-replica.md)  
  
-   [針對可用性群組手動準備次要資料庫 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/manually-prepare-a-secondary-database-for-an-availability-group-sql-server.md)  
  
-   [疑難排解失敗的加入檔案作業 &#40;AlwaysOn 可用性群組&#41;](../../../database-engine/availability-groups/windows/troubleshoot-a-failed-add-file-operation-always-on-availability-groups.md)  
  
-   [管理可用性群組之資料庫的登入及工作 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/logins-and-jobs-for-availability-group-databases.md)  
  
-   [在另一個伺服器執行個體上提供可用的資料庫時，管理中繼資料 &#40;SQL Server&#41;](../../../relational-databases/databases/manage-metadata-when-making-a-database-available-on-another-server.md)  
  
##  <a name="related-content"></a><a name="RelatedContent"></a> 相關內容  
  
-   [檢視容錯移轉叢集的事件和記錄檔](https://technet.microsoft.com/library/cc772342\(WS.10\).aspx)  
  
-   [Get-ClusterLog 容錯移轉叢集指令程式](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee461045(v=technet.10))  
  
-   [SQL Server Always On 小組部落格：官方 SQL Server Always On 小組部落格](/archive/blogs/sqlalwayson/)  
  
## <a name="see-also"></a>另請參閱  
 [資料庫鏡像和 Always On 可用性群組的傳輸安全性 &#40;SQL Server&#41;](../../../database-engine/database-mirroring/transport-security-database-mirroring-always-on-availability.md)   
 [用戶端網路組態](../../../database-engine/configure-windows/client-network-configuration.md)   
 [AlwaysOn 可用性群組的必要條件、限制和建議 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/prereqs-restrictions-recommendations-always-on-availability.md)  
  
