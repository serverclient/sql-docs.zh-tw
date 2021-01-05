---
title: 什麼是可用性群組接聽程式？
description: 'Always On 可用性群組接聽程式概觀及自動將流量引導至目標伺服器的運作方式。 '
ms.custom: seodec18
ms.date: 02/27/2020
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: conceptual
helpviewer_keywords:
- Availability Groups [SQL Server], listeners
- read-only routing
- read-intent connections [AlwaysOn Availability Groups]
- Availability Groups [SQL Server], configuring
- Availability Groups [SQL Server], read-only routing
- Availability Groups [SQL Server], client connectivity
ms.assetid: 76fb3eca-6b08-4610-8d79-64019dd56c44
author: cawrites
ms.author: chadam
ms.openlocfilehash: 2dc23852fc5792d29c25b151f7c6180ba32295ce
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97641138"
---
# <a name="what-is-an-availability-group-listener"></a>什麼是可用性群組接聽程式？  
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]

可用性群組接聽程式是用戶端可連線的虛擬網路名稱 (VNN)，以存取 Always On 可用性群組的主要或次要複本中資料庫。 接聽程式可讓用戶端連線到複本，卻不需要知道 SQL Server 的實體執行個體名稱。 因為接聽程式會路由流量，所以在容錯移轉之後，不需要修改用戶端連接字串。 

可用性群組接聽程式是由網域名稱系統 (DNS) 接聽程式名稱、接聽程式通訊埠指定和一個或多個 IP 位址所組成。 可用性群組接聽程式只支援 TCP 通訊協定。  接聽程式的 DNS 名稱在網域和 NetBIOS 中都必須是唯一的。  當建立接聽程式時，其會成為叢集中具有相關虛擬網路名稱 (VNN)、虛擬 IP (VIP) 及可用性群組相依性的資源。 用戶端使用 DNS 將 VNN 解析為多個 IP 位址，然後嘗試連接到每個位址，直到連接要求成功為止，或直到連接要求逾時為止。  
  
如果將唯讀路由設定為一或多個[可讀取的次要複本](../../../database-engine/availability-groups/windows/active-secondaries-readable-secondary-replicas-always-on-availability-groups.md)，則接聽程式的讀取意圖用戶端連線會被自動重新導向至可讀取次要複本。 
  
本文提供可用性群組接聽程式的概觀。 您也可以[設定接聽程式](create-or-configure-an-availability-group-listener-sql-server.md)，然後了解如何[連線接聽程式](listeners-client-connectivity-application-failover.md)。
  
  
##  <a name="listener-parameters"></a><a name="AGlConfig"></a> 接聽程式參數  

 可用性群組接聽程式使用下列名稱：
  
 **唯一的 DNS 名稱**  
 這也稱為虛擬網路名稱 (VNN)。 套用 DNS 主機名稱的 Active Directory 命名規則。 如需詳細資訊，請參閱 [Active Directory 中的電腦、網域、站台及 OU 的命名慣例](https://support.microsoft.com/kb/909264) 知識庫文件。  
  
**一或多個虛擬 IP 位址 (VIP)**  
 為可用性群組可容錯移轉之一或多個目標子網路設定 VIP。  
  
**IP 位址組態**  
 對指定的可用性群組接聽程式而言，此 IP 位址可使用動態主機設定通訊協定 (DHCP) 或者一或多個靜態 IP 位址。 使用 DHCP 會造成容錯移轉期間的連線延遲，所以不建議在生產環境中使用。 跨多個子網路擴充或使用混合式網路組態的可用性群組，必須使用靜態 IP 位址。 
 
  
##  <a name="listener-port"></a><a name="SelectListenerPort"></a> 接聽程式連接埠 
 當您設定可用性群組接聽程式時，必須指定通訊埠。  您可以將預設通訊埠設定為 1433，以便提供用戶端連接字串的簡易性。 如果使用 1433，則不需要在連接字串中指定通訊埠編號。 此外，因為每個可用性群組接聽程式都有個別的虛擬網路名稱，所以單一 WSFC 上設定的每個可用性群組接聽程式都可以設定為參考相同的預設通訊埠 1433。  
  
 您也可以指定非標準接聽程式通訊埠，但這表示每當連接到可用性群組接聽程式時，您也需要在連接字串中明確指定目標通訊埠。  此外，也需要在防火牆上開啟非標準通訊埠的權限。  
  
 如果您將預設通訊埠 1433 用於可用性群組接聽程式 VNN，仍然需要確定叢集節點上沒有其他服務正在使用此通訊埠，否則會導致通訊埠衝突。  
  
 如果其中一個 SQL Server 執行個體已經透過執行個體接聽程式在 TCP 通訊埠 1433 上接聽，而且在電腦上沒有其他服務 (包括其他 SQL Server 執行個體) 正在通訊埠 1433 上接聽，就不會導致與可用性群組接聽程式通訊埠相衝突。  這是因為可用性群組接聽程式可在同一個服務處理序內共用相同的 TCP 通訊埠。  但是不應該將多個 SQL Server (並存) 執行個體設定為在相同通訊埠上接聽。  
  
  
##  <a name="behavior-of-client-connections-on-failover"></a><a name="CCBehaviorOnFailover"></a> 容錯移轉時的用戶端連線行為  

 當可用性群組發生容錯移轉時，對可用性群組的現有持續連線會終止，而且用戶端必須建立新連接，才能繼續使用相同的主要資料庫或唯讀次要資料庫。  在伺服器端發生容錯移轉時，對可用性群組的連接可能會失敗，而強制用戶端連接重試連接，直到主要資料庫恢復上線為止。  
  
 如果在用戶端應用程式嘗試連線期間，但在連線逾時之前，可用性群組恢復上線，用戶端驅動程式在其中一個內部重試期間可能會成功連線，而應用程式在此情況下不會發生錯誤。  


## <a name="next-steps"></a>後續步驟

現在您已熟悉可用性群組接聽程式的運作方式，請[建立接聽程式](create-or-configure-an-availability-group-listener-sql-server.md)，然後將應用程式設定為[連線到接聽程式](listeners-client-connectivity-application-failover.md)。 您也可以檢閱各種[可用性群組監視策略](monitoring-of-availability-groups-sql-server.md)，以確保可用性群組的健康狀態。 

如需可用性群組的詳細資訊，請參閱 [Always On 可用性群組概觀 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server.md)。 
  

  
  
