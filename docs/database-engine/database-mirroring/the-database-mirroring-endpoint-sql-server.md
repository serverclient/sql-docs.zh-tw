---
title: 資料庫鏡像端點 (SQL Server) | Microsoft Docs
description: 了解專用的資料庫鏡像端點，這是每部伺服器參與 Always On 可用性群組或資料庫鏡像的必要項目。
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: high-availability
ms.reviewer: ''
ms.technology: database-mirroring
ms.topic: conceptual
helpviewer_keywords:
- database mirroring [SQL Server], deployment
- database mirroring [SQL Server], endpoint
- endpoints [SQL Server], AlwaysOn Availability Groups
- endpoints [SQL Server], database mirroring
- Availability Groups [SQL Server], endpoint
ms.assetid: 39332dc5-678e-4650-9217-6aa3cdc41635
author: MikeRayMSFT
ms.author: mikeray
monikerRange: '>=sql-server-2016'
ms.openlocfilehash: 81d32a7a21c8e152944b8e24002320e865372681
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97641933"
---
# <a name="the-database-mirroring-endpoint-sql-server"></a>資料庫鏡像端點 (SQL Server)

[!INCLUDE [SQL Server Windows Only - ASDBMI ](../../includes/applies-to-version/sql-windows-only-asdbmi.md)]

  若要參與 [!INCLUDE[ssHADR](../../includes/sshadr-md.md)] 或資料庫鏡像，伺服器執行個體就需要有自己專用的 *「資料庫鏡像端點」* (Database Mirroring Endpoint)。 這個端點是特殊目的之端點，專門用來接收其他伺服器執行個體的連接。 在給定的伺服器執行個體上，任何其他伺服器執行個體的每個 [!INCLUDE[ssHADR](../../includes/sshadr-md.md)] 或資料庫鏡像連接都需要一個資料庫鏡像端點。  
  
 資料庫鏡像端點使用「傳輸控制通訊協定」(TCP)，在參與資料庫鏡像工作階段或裝載可用性複本的伺服器執行個體之間傳送和接收訊息。 資料庫鏡像端點會在唯一的 TCP 通訊埠編號上接聽。  
  
> [!NOTE]  
>  主體伺服器或主要複本的用戶端連接不會使用資料庫鏡像端點。  
  
> [!NOTE]  
>  未來的 Microsoft SQL Server 版本將移除資料庫鏡像功能。 請避免在新的開發工作中使用這項功能，並規劃將目前使用資料庫鏡像的應用程式修改為使用 [!INCLUDE[ssHADR](../../includes/sshadr-md.md)] 。  
  
  
##  <a name="server-network-address"></a><a name="ServerNetworkAddress"></a> 伺服器網路位址  
 伺服器執行個體的網路位址 (其「伺服器網路位址」或「端點 URL」) 包含其端點的通訊埠編號，以及其主機電腦的系統和網域名稱。 通訊埠編號會唯一識別特定伺服器執行個體。  
  
 下圖說明如何唯一識別相同伺服器上的兩個伺服器執行個體。 這兩個伺服器執行個體的伺服器網路位址包含相同的系統名稱 `MYSYSTEM`和網域名稱 `Adventure-Works.MyDomain.com`。 若要讓系統將連接傳送到伺服器執行個體，伺服器網路位址會包含與特定伺服器執行個體之鏡像端點相關聯的通訊埠編號。  
  
 ![預設執行個體的伺服器網路位址](../../database-engine/availability-groups/windows/media/dbm-2-instances-ports-1-system.gif "預設執行個體的伺服器網路位址")  
  
 依預設， [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的執行個體不包含資料庫鏡像端點。 設定資料庫鏡像工作階段時，必須以手動方式建立這些資料庫鏡像端點。 系統管理員必須在參與資料庫鏡像的每個伺服器執行個體中建立不同的端點。 請注意，如果給定電腦上多個伺服器執行個體需要資料庫鏡像端點，請為每個端點指定不同的通訊埠編號。  
  
> [!IMPORTANT]  
>  如果執行 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的電腦具有防火牆，此防火牆的組態必須允許端點中所指定的通訊埠同時使用內送和外送連接。  
  
 對於資料庫鏡像和 [!INCLUDE[ssHADR](../../includes/sshadr-md.md)]，會在端點上設定驗證與加密。 如需詳細資訊，請參閱 [資料庫鏡像和 AlwaysOn 可用性群組的傳輸安全性 (SQL Server)](../../database-engine/database-mirroring/transport-security-database-mirroring-always-on-availability.md)。  
  
> [!IMPORTANT]  
>  請不要重新設定使用中資料庫鏡像端點。 伺服器執行個體會使用彼此的端點來了解其他系統的狀態。 如果端點重新設定，它可能會重新啟動，而這對其他伺服器執行個體可能會是一項錯誤。 對於自動容錯移轉模式，這點尤其重要，因為在這種模式中重新設定夥伴上的端點，可能會導致發生容錯移轉。  
  
  
##  <a name="determining-the-authentication-type-for-a-database-mirroring-endpoint"></a><a name="EndpointAuthenticationTypes"></a> 決定資料庫鏡像端點的驗證類型  
 請務必了解，伺服器執行個體的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 服務帳戶會決定可用於資料庫鏡像端點的驗證類型，如下：  
  
-   如果每個伺服器執行個體各自在網域服務帳戶下執行，您可以將 Windows 驗證用於資料庫鏡像端點。 如果所有伺服器執行個體是以相同的網域使用者帳戶執行，兩個 **master** 資料庫中都會自動存在正確的使用者登入。 這樣可簡化可用性資料庫的安全性組態，建議您使用。  
  
     如果裝載可用性群組之可用性複本的任何伺服器執行個體是以不同的帳戶執行，則每一個帳戶的登入都必須在其他伺服器執行個體的 **master** 中建立。 然後，該登入必須被授與 CONNECT 權限，才能連接到該伺服器執行個體的資料庫鏡像端點。 如需詳細資訊，請參閱 [設定資料庫鏡像或 AlwaysOn 可用性群組的登入帳戶 (SQL Server)](../../database-engine/database-mirroring/set-up-login-accounts-database-mirroring-always-on-availability.md)。  
  
     如果您的伺服器執行個體使用 Windows 驗證，可透過 [!INCLUDE[tsql](../../includes/tsql-md.md)]、PowerShell 或新增可用性群組精靈來建立資料庫鏡像端點。  
  
    > [!NOTE]  
    >  如果將要裝載可用性複本的伺服器執行個體缺少資料庫鏡像端點，新增可用性群組精靈會自動建立使用 Windows 驗證的資料庫鏡像端點。 如需詳細資訊，請參閱 [使用可用性群組精靈 (SQL Server Management Studio)](../../database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio.md)。  
  
-   如果有任何伺服器執行個體在內建帳戶之下執行 (例如本機系統、本機服務或網路服務，或是非網域帳戶)，您必須將憑證用於端點驗證。 如果您要針對資料庫鏡像端點使用憑證，您的系統管理員必須設定每一個伺服器執行個體同時在傳出和傳入的連接上使用憑證。  
  
     沒有任何自動的方法可以設定使用憑證的資料庫鏡像安全性。 您將需要使用 CREATE ENDPOINT [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式或 **New-SqlHadrEndpoint** PowerShell Cmdlet。 如需詳細資訊，請參閱 [CREATE ENDPOINT (Transact-SQL)](../../t-sql/statements/create-endpoint-transact-sql.md)。 如需在伺服器執行個體上啟用憑證驗證的資訊，請參閱 [使用資料庫鏡像端點憑證 (Transact-SQL)](../../database-engine/database-mirroring/use-certificates-for-a-database-mirroring-endpoint-transact-sql.md)。  
  
  
##  <a name="related-tasks"></a><a name="RelatedTasks"></a> 相關工作  
 **若要設定資料庫鏡像端點**  
  
-   [建立 Windows 驗證的資料庫鏡像端點 (Transact-SQL)](../../database-engine/database-mirroring/create-a-database-mirroring-endpoint-for-windows-authentication-transact-sql.md)  
  
-   [使用資料庫鏡像端點憑證 (Transact-SQL)](../../database-engine/database-mirroring/use-certificates-for-a-database-mirroring-endpoint-transact-sql.md)  
  
    -   [允許資料庫鏡像端點使用輸出連線的憑證 (Transact-SQL)](../../database-engine/database-mirroring/database-mirroring-use-certificates-for-outbound-connections.md)  
  
    -   [允許資料庫鏡像端點使用傳入連接的憑證 (Transact-SQL)](../../database-engine/database-mirroring/database-mirroring-use-certificates-for-inbound-connections.md)  
  
-   [指定伺服器網路位址 (資料庫鏡像)](../../database-engine/database-mirroring/specify-a-server-network-address-database-mirroring.md)  
  
-   [在加入或修改可用性複本時指定端點 URL (SQL Server)](../../database-engine/availability-groups/windows/specify-endpoint-url-adding-or-modifying-availability-replica.md)  
  
-   [使用可用性群組精靈 (SQL Server Management Studio)](../../database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio.md)  
  
 **若要檢視有關資料庫鏡像端點的資訊**  
  
-   [sys.database_mirroring_endpoints (Transact-SQL)](../../relational-databases/system-catalog-views/sys-database-mirroring-endpoints-transact-sql.md)  
  
  
## <a name="see-also"></a>另請參閱  
 [資料庫鏡像和 AlwaysOn 可用性群組的傳輸安全性 (SQL Server)](../../database-engine/database-mirroring/transport-security-database-mirroring-always-on-availability.md)   
 [為資料庫鏡像組態進行疑難排解 (SQL Server)](../../database-engine/database-mirroring/troubleshoot-database-mirroring-configuration-sql-server.md)   
 [sys.dm_hadr_availability_replica_states (Transact-SQL)](../../relational-databases/system-dynamic-management-views/sys-dm-hadr-availability-replica-states-transact-sql.md)   
 [sys.dm_db_mirroring_connections (Transact-SQL)](../../relational-databases/system-dynamic-management-views/database-mirroring-sys-dm-db-mirroring-connections.md)  
  
  
