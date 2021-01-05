---
title: 檢視可用性群組接聽程式屬性
description: 描述如何使用 SQL Server Management Studio、Transact-SQL 或 PowerShell，在 SQL Server 中檢視 Always On 可用性群組接聽程式的屬性。
ms.custom: seo-lt-2019
ms.date: 07/11/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: conceptual
f1_keywords:
- sql13.swb.availabilitygrouplistenerproperties.general.f1
helpviewer_keywords:
- Availability Groups [SQL Server], monitoring
ms.assetid: aca0d016-3228-40b8-bdc3-285ed6d9b280
author: cawrites
ms.author: chadam
ms.openlocfilehash: b3b77706425eae89bd74b4803859e4a5c029ad17
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97641694"
---
# <a name="view-availability-group-listener-properties-sql-server"></a>檢視可用性群組接聽程式屬性 (SQL Server)
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]
  此主題描述如何使用 *中的* 或 [!INCLUDE[ssManStudioFull](../../../includes/ssmanstudiofull-md.md)] 來檢視 AlwaysOn [!INCLUDE[tsql](../../../includes/tsql-md.md)] 「可用性群組接聽程式」 [!INCLUDE[ssCurrent](../../../includes/sscurrent-md.md)](Availability Group Listener) 的屬性。  
  
##  <a name="using-sql-server-management-studio"></a><a name="SSMSProcedure"></a> 使用 SQL Server Management Studio  
 **若要檢視接聽程式屬性**  
  
1.  在 [物件總管] 中，連接到裝載您想要檢視其接聽程式之可用性群組的任何可用性複本的伺服器執行個體。 按一下伺服器名稱展開伺服器樹狀目錄。  
  
2.  依序展開 [Always On 高可用性] 節點和 [可用性群組] 節點。  
  
3.  展開可用性群組的節點，然後展開 **[可用性群組接聽程式]** 節點。  
  
4.  以滑鼠右鍵按一下您想要檢視的接聽程式，然後選取 [屬性] 命令。  
  
5.  這樣就會開啟 **[可用性群組接聽項屬性]** 對話方塊。 如需詳細資訊，請參閱本主題稍後的 [可用性群組接聽程式屬性 (對話方塊)](#AgListenerPropertiesDialog)。  
  
###  <a name="availability-group-listener-properties-dialog-box"></a><a name="AgListenerPropertiesDialog"></a> 可用性群組接聽程式屬性 (對話方塊)  
 **接聽程式 DNS 名稱**  
 可用性群組接聽程式的網路名稱。  
  
 **通訊埠**  
 此接聽程式所使用的 TCP 通訊埠。  
  
> [!NOTE]  
>  如果您連接到主要複本，就可以使用此欄位來修改接聽程式的通訊埠編號。 這項作業需要可用性群組的 ALTER AVAILABILITY GROUP 權限、CONTROL AVAILABILITY GROUP 權限、ALTER ANY AVAILABILITY GROUP 權限或 CONTROL SERVER 權限。  
  
 **網路模式**  
 指出接聽程式所使用的 TCP 通訊協定，其中一個：  
  
 **DHCP**  
 接聽程式會使用執行動態主機設定通訊協定 (DHCP) 之伺服器所指派的動態 IP 位址。  
  
 **靜態 IP**  
 接聽程式會使用一個或多個靜態 IP 位址。 若要存取不同的子網路，可用性群組接聽程式必須使用靜態 IP 位址。  
  
 此方格會顯示接聽程式所接聽的每個子網路，以及與該子網路相關聯的 IP 位址。  
  
##  <a name="using-transact-sql"></a><a name="TsqlProcedure"></a> 使用 Transact-SQL  
 **若要檢視接聽程式屬性**  
  
 若要監視可用性群組接聽程式，請使用下列檢視：  
  
 [sys.availability_group_listener_ip_addresses](../../../relational-databases/system-catalog-views/sys-availability-group-listener-ip-addresses-transact-sql.md)  
 針對目前上線供可用性群組接聽程式使用的每個符合標準虛擬 IP 位址傳回一個資料列。  
  
 **資料行名稱** ：listener_id、ip_address、ip_subnet_mask、is_dhcp、network_subnet_ip、network_subnet_prefix_length、network_subnet_ipv4_mask、state、state_desc  
  
 [sys.availability_group_listeners](../../../relational-databases/system-catalog-views/sys-availability-group-listeners-transact-sql.md)  
 若為給定的可用性群組，傳回零個資料列，表示沒有網路名稱與可用性群組相關聯，或針對 WSFC 叢集中的每個可用性群組接聽程式組態傳回一個資料列。  
  
 **資料行名稱** ：group_id、listener_id、dns_name、port、is_conformant、ip_configuration_string_from_cluster  
  
 [sys.dm_tcp_listener_states](../../../relational-databases/system-dynamic-management-views/sys-dm-tcp-listener-states-transact-sql.md)  
 針對每個 TCP 接聽程式傳回一個包含動態狀態資訊的資料列。  
  
 **資料行名稱：** listener_id、ip_address、is_ipv4、port、type、type_desc、state、state_desc、start_time  
  
> [!NOTE]  
>  如需使用 [!INCLUDE[tsql](../../../includes/tsql-md.md)] 監視 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] 環境的詳細資訊，請參閱 [監視可用性群組 &#40;Transact-SQL&#41;](../../../database-engine/availability-groups/windows/monitor-availability-groups-transact-sql.md)(Availability Group Listener) 的屬性。  
  
##  <a name="related-tasks"></a><a name="RelatedTasks"></a> 相關工作  
  
-   [建立或設定可用性群組接聽程式 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/create-or-configure-an-availability-group-listener-sql-server.md)  
  
-   [移除可用性群組接聽程式 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/remove-an-availability-group-listener-sql-server.md)  
  
## <a name="see-also"></a>另請參閱  
 [AlwaysOn 可用性群組概觀 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server.md)   
 [可用性群組接聽程式、用戶端連接性及應用程式容錯移轉 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/listeners-client-connectivity-application-failover.md)   
 [監視可用性群組 &#40;Transact-SQL&#41;](../../../database-engine/availability-groups/windows/monitor-availability-groups-transact-sql.md)  
  
  
