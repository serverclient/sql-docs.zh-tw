---
title: 具可用性群組的 Service Broker
description: 包含搭配 SQL Server Always On 可用性群組設定 Service Broker 的相關資訊。
ms.custom: seo-lt-2019
ms.date: 05/17/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: conceptual
helpviewer_keywords:
- Service Broker, AlwaysOn Availability Groups
- Availability Groups [SQL Server], interoperability
ms.assetid: 881c20e5-1c99-44eb-b393-09fc5ea0f122
author: cawrites
ms.author: chadam
ms.openlocfilehash: 521d66002a1ad3d2ead2a906dc40a87d22ae220a
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97642104"
---
# <a name="service-broker-with-always-on-availability-groups-sql-server"></a>Service Broker 與 AlwaysOn 可用性群組 (SQL Server)
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]

  本主題包含將 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] Service Broker 設定為使用 [!INCLUDE[ssCurrent](../../../includes/sscurrent-md.md)]的相關資訊。  
  
  
##  <a name="requirements-for-a-service-in-an-availability-group-to-receive-remote-messages"></a><a name="ReceiveRemoteMessages"></a> 讓可用性群組中之服務接收遠端訊息的需求  
  
1.  **確定可用性群組擁有接聽程式。**  
  
     如需詳細資訊，請參閱 [建立或設定可用性群組接聽程式 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/create-or-configure-an-availability-group-listener-sql-server.md)。  
  
2.  **確定 Service Broker 端點存在而且設定正確。**  
  
     在裝載可用性群組之可用性複本的每個 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體上，依照下列方式設定 Service Broker 端點：  
  
    -   將 LISTENER_IP 設定為 'ALL'。 這項設定會針對繫結至可用性群組接聽程式的任何有效 IP 位址啟用連接。  
  
    -   將所有主機伺服器執行個體的 Service Broker PORT 設定為相同的通訊埠編號。  
  
        > [!TIP]  
        >  若要檢視指定伺服器執行個體之 Service Broker 端點的通訊埠編號，請查詢 **sys.tcp_endpoints** 目錄檢視的 [port](../../../relational-databases/system-catalog-views/sys-tcp-endpoints-transact-sql.md) 資料行，其中 **type_desc** = 'SERVICE_BROKER'。  
  
     下列範例會建立使用預設 Service Broker 通訊埠 (4022) 並且接聽所有有效 IP 位址的 Windows 驗證 Service Broker 端點。  
  
    ```  
    CREATE ENDPOINT [SSBEndpoint]  
        STATE = STARTED  
        AS TCP  (LISTENER_PORT = 4022, LISTENER_IP = ALL )  
        FOR SERVICE_BROKER (AUTHENTICATION = WINDOWS)  
    ```  
  
     如需詳細資訊，請參閱 [CREATE ENDPOINT &#40;Transact-SQL&#41;](../../../t-sql/statements/create-endpoint-transact-sql.md)的相關資訊。  

    > [!NOTE]  
    SQL Server Service Broker 不是多重子網路感知。 將 `RegisterAllProvidersIP` 設為 0，並驗證叢集在 DNS 中具備使用靜態 IP 位址的必要權限。 請參閱[設定可用性群組接聽程式](create-or-configure-an-availability-group-listener-sql-server.md)深入了解。 Service Broker 可能會延遲訊息，在顯示 "CONVERSING" 的狀態下，嘗試使用已停用的 IP 位址。

3.  **授與端點的 CONNECT 權限。**  
  
     將 Service Broker 端點的 CONNECT 權限授與 PUBLIC 或登入。  
  
     下列範例會將名為 `broker_endpoint` 之 Service Broker 端點的連接授與 PUBLIC。  
  
    ```  
    GRANT CONNECT ON ENDPOINT::[broker_endpoint] TO [PUBLIC]  
    ```  
  
     如需詳細資訊，請參閱 [GRANT &#40;Transact-SQL&#41;](../../../t-sql/statements/grant-transact-sql.md)的相關資訊。  
  
4.  **確定 msdb 包含 AutoCreatedLocal 路由或特定服務的路由。**  
  
    > [!NOTE]  
    >  根據預設，每個使用者資料庫 (包括 **msdb**) 都包含 **AutoCreatedLocal** 路由。 此路由會比對所有服務名稱和 Broker 執行個體，並指定訊息應在目前執行個體內傳遞。 **AutoCreatedLocal** 的優先權低於明確指定與遠端執行個體通訊之特定服務的路由。  
  
     如需建立路由的相關資訊，請參閱 [Service Broker 路由範例](https://msdn.microsoft.com/library/ms166090\(SQL.105\).aspx) (在 [!INCLUDE[ssKilimanjaro](../../../includes/sskilimanjaro-md.md)] 版本的《線上叢書》中) 和 [CREATE ROUTE &#40;Transact-SQL&#41;](../../../t-sql/statements/create-route-transact-sql.md)的相關資訊。  
  
##  <a name="requirements-for-sending-messages-to-a-remote-service-in-an-availability-group"></a><a name="SendRemoteMessages"></a> 將訊息傳送至可用性群組中之遠端服務的需求  
  
1.  **建立目標服務的路由。**  
  
     依照下列方式設定路由：  
  
    -   將 ADDRESS 設定為裝載服務資料庫之可用性群組的接聽程式 IP 位址。  
  
    -   將 PORT 設定為您在每個遠端 SQL Server 執行個體之 Service Broker 端點中指定的通訊埠。  
  
     下列範例會針對 `RouteToTargetService` 服務建立名為 `ISBNLookupRequestService` 的路由。 此路由會以使用通訊埠 4022 的可用性群組接聽程式 `MyAgListener`做為目標。  
  
    ```  
    CREATE ROUTE [RouteToTargetService] WITH   
    SERVICE_NAME = 'ISBNLookupRequestService',   
    ADDRESS = 'TCP://MyAgListener:4022';  
  
    ```  
  
     如需詳細資訊，請參閱 [CREATE ROUTE &#40;Transact-SQL&#41;](../../../t-sql/statements/create-route-transact-sql.md)的相關資訊。  
  
2.  **確定 msdb 包含 AutoCreatedLocal 路由或特定服務的路由。** (如需詳細資訊，請參閱本主題稍早的 [讓可用性群組中之服務接收遠端訊息的需求](#ReceiveRemoteMessages)。)  
  
##  <a name="related-tasks"></a><a name="RelatedTasks"></a> 相關工作  
  
-   [CREATE ENDPOINT &#40;Transact-SQL&#41;](../../../t-sql/statements/create-endpoint-transact-sql.md)  
  
-   [CREATE ROUTE &#40;Transact-SQL&#41;](../../../t-sql/statements/create-route-transact-sql.md)  
  
-   [GRANT &#40;Transact-SQL&#41;](../../../t-sql/statements/grant-transact-sql.md)  
  
-   [建立或設定可用性群組接聽程式 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/create-or-configure-an-availability-group-listener-sql-server.md)。  
  
-   [建立及設定可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/creation-and-configuration-of-availability-groups-sql-server.md)  
  
-   [設定資料庫鏡像或 AlwaysOn 可用性群組的登入帳戶 &#40;SQL Server&#41;](../../../database-engine/database-mirroring/set-up-login-accounts-database-mirroring-always-on-availability.md)  
  
## <a name="see-also"></a>另請參閱  
 [AlwaysOn 可用性群組概觀 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server.md)   
 [可用性群組接聽程式、用戶端連接性及應用程式容錯移轉 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/listeners-client-connectivity-application-failover.md)   
 [SQL Server Service Broker](../../../database-engine/configure-windows/sql-server-service-broker.md)  
  
  
