---
title: 檢視可用性複本屬性
description: 使用 SQL Server Management Studio (SSMS)、Transact-SQL (T-SQL) 或 SQL PowerShell 來檢視可用性群組複本之屬性的指示。
ms.custom: seo-lt-2019
ms.date: 05/17/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: conceptual
helpviewer_keywords:
- ', policies'
ms.assetid: 14fed3c4-8ecc-4e1c-931d-a7ec1e9f9e90
author: cawrites
ms.author: chadam
ms.openlocfilehash: 0e5ac7aa8e5c214c96983c694149bf234a7f0a7a
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97642084"
---
# <a name="view-availability-replica-properties-sql-server"></a>檢視可用性複本屬性 (SQL Server)
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]
  此主題描述如何使用 [!INCLUDE[ssManStudioFull](../../../includes/ssmanstudiofull-md.md)] 中的 [!INCLUDE[tsql](../../../includes/tsql-md.md)] 或 [!INCLUDE[ssCurrent](../../../includes/sscurrent-md.md)]，檢視 AlwaysOn 可用性群組的可用性複本屬性。  
  
 
##  <a name="using-sql-server-management-studio"></a><a name="SSMSProcedure"></a> 使用 SQL Server Management Studio  
 **若要檢視及變更可用性複本的屬性**  
  
1.  在 [物件總管] 中，連接到裝載主要複本的伺服器執行個體，然後展開伺服器樹狀目錄。  
  
2.  依序展開 [Always On 高可用性]  節點和 [可用性群組]  節點。  
  
3.  展開可用性複本所屬的可用性群組，然後展開 **[可用性複本]** 節點。  
  
4.  以滑鼠右鍵按一下要檢視其屬性的可用性複本，然後選取 [屬性]  命令。  
  
5.  在 **[可用性複本屬性]** 對話方塊中，使用 **[一般]** 頁面檢視此複本的屬性。 如果您連接至主要複本，可以變更下列屬性：可用性模式、容錯移轉模式、主要角色的連接存取、次要角色的唯讀存取 (可讀取的次要)，以及工作階段逾時值。 如需詳細資訊，請參閱[可用性複本屬性 &#40;一般頁面&#41;](../../../database-engine/availability-groups/windows/availability-replica-properties-general-page.md)。  

   [!NOTE]
   >如果叢集類型是 none，則您無法變更容錯移轉模式。
  
  
##  <a name="using-transact-sql"></a><a name="TsqlProcedure"></a> 使用 Transact-SQL  
 **若要檢視可用性複本的屬性和狀態**  
  
 若要檢視可用性複本的屬性和狀態，請使用下列檢視和系統函數：  
  
 [sys.availability_replicas](../../../relational-databases/system-catalog-views/sys-availability-replicas-transact-sql.md)  
 針對每一個可用性群組中的每一個可用性複本 ( [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 本機執行個體裝載此群組的可用性複本)，各傳回一個資料列。  
  
 **資料行名稱：** replica_id、group_id、replica_metadata_id、replica_server_name、owner_sid、endpoint_url、availability_mode、availability_mode_desc、failover_mode、failover_mode_desc、session_timeout、primary_role_allow_connections、primary_role_allow_connections_desc、secondary_role_allow_connections、secondary_role_allow_connections_desc、create_date、modify_date、backup_priority、read_only_routing_url  
  
 [sys.availability_read_only_routing_lists](../../../relational-databases/system-catalog-views/sys-availability-read-only-routing-lists-transact-sql.md)  
 針對 WSFC 容錯移轉叢集中 AlwaysOn 可用性群組內每個可用性複本的唯讀路由清單，各傳回一個資料列。  
  
 **資料行名稱：** replica_id、routing_priority、read_only_replica_id  
  
 [sys.dm_hadr_availability_replica_cluster_nodes](../../../relational-databases/system-dynamic-management-views/sys-dm-hadr-availability-replica-cluster-nodes-transact-sql.md)  
 針對 Windows Server 容錯移轉叢集 (WSFC) 叢集中 AlwaysOn 可用性群組的每一個可用性複本 (不論聯結狀態為何)，各傳回一個資料列。  
  
 **資料行名稱：** group_name、replica_server_name、node_name  
  
 [sys.dm_hadr_availability_replica_cluster_states](../../../relational-databases/system-dynamic-management-views/sys-dm-hadr-availability-replica-cluster-states-transact-sql.md)  
 針對 Windows Server 容錯移轉叢集 (WSFC) 叢集中所有 AlwaysOn 可用性群組 (不論複本位置為何) 的每一個複本 (不論聯結狀態為何)，各傳回一個資料列。  
  
 **資料行名稱：** replica_id、replica_server_name、group_id、join_state、join_state_desc  
  
 [sys.dm_hadr_availability_replica_states](../../../relational-databases/system-dynamic-management-views/sys-dm-hadr-availability-replica-states-transact-sql.md)  
 傳回顯示每個本機可用性複本之狀態的資料列，並針對同一個可用性群組中每一個遠端可用性複本，各傳回一個資料列。  
  
 **資料行名稱：** replica_id、group_id、is_local、role、role_desc、operational_state、operational_state_desc、connected_state、connected_state_desc、recovery_health、recovery_health_desc、synchronization_health、synchronization_health_desc、last_connect_error_number、last_connect_error_description 和 last_connect_error_timestamp  
  
 [sys.fn_hadr_backup_is_preferred_replica](../../../relational-databases/system-functions/sys-fn-hadr-backup-is-preferred-replica-transact-sql.md)  
 判斷目前的複本是否為慣用的備份複本。 如果目前伺服器執行個體上的資料庫為慣用複本，則傳回 1。 否則，它會傳回 0。  
  
> [!NOTE]  
>  如需可用性複本效能計數器 ( **SQLServer:Availability Replica**  效能物件) 的相關資訊，請參閱 [SQL Server、可用性複本](../../../relational-databases/performance-monitor/sql-server-availability-replica.md)。  
  
  
##  <a name="related-tasks"></a><a name="RelatedTasks"></a> 相關工作  
 **若要檢視可用性群組的相關資訊**  
  
-   [檢視可用性群組屬性 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/view-availability-group-properties-sql-server.md)  
  
-   [檢視可用性群組接聽程式屬性 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/view-availability-group-listener-properties-sql-server.md)  
  
-   [AlwaysOn 可用性群組操作問題適用的 AlwaysOn 原則 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/always-on-policies-for-operational-issues-always-on-availability.md)  
  
-   [使用 AlwaysOn 儀表板 &#40;SQL Server Management Studio&#41;](../../../database-engine/availability-groups/windows/use-the-always-on-dashboard-sql-server-management-studio.md)  
  
-   [監視可用性群組 &#40;Transact-SQL&#41;](../../../database-engine/availability-groups/windows/monitor-availability-groups-transact-sql.md)  
  
 **若要管理可用性複本**  
  
-   [將次要複本加入至可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/add-a-secondary-replica-to-an-availability-group-sql-server.md)  
  
-   [將次要複本聯結至可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/join-a-secondary-replica-to-an-availability-group-sql-server.md)  
  
-   [設定可用性複本的唯讀存取 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/configure-read-only-access-on-an-availability-replica-sql-server.md)  
  
-   [變更可用性複本的可用性模式 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/change-the-availability-mode-of-an-availability-replica-sql-server.md)  
  
-   [變更可用性複本的容錯移轉模式 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/change-the-failover-mode-of-an-availability-replica-sql-server.md)  
  
-   [變更可用性複本的工作階段逾時期限 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/change-the-session-timeout-period-for-an-availability-replica-sql-server.md)  
  
-   [將次要複本從可用性群組移除 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/remove-a-secondary-replica-from-an-availability-group-sql-server.md)  
  
 **管理可用性資料庫**  
  
-   [將資料庫加入至可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/availability-group-add-a-database.md)  
  
-   [將次要資料庫聯結至可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/join-a-secondary-database-to-an-availability-group-sql-server.md)  
  
-   [暫止可用性資料庫 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/suspend-an-availability-database-sql-server.md)  
  
-   [繼續可用性資料庫 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/resume-an-availability-database-sql-server.md)  
  
-   [將次要資料庫從可用性群組移除 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/remove-a-secondary-database-from-an-availability-group-sql-server.md)  
  
-   [將主要資料庫從可用性群組移除 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/remove-a-primary-database-from-an-availability-group-sql-server.md)  
  
  
## <a name="see-also"></a>另請參閱  
 [AlwaysOn 可用性群組概觀 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server.md)   
 [監視可用性群組 &#40;Transact-SQL&#41;](../../../database-engine/availability-groups/windows/monitor-availability-groups-transact-sql.md)   
 [AlwaysOn 可用性群組操作問題適用的 AlwaysOn 原則 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/always-on-policies-for-operational-issues-always-on-availability.md)   
 [可用性群組的管理 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/administration-of-an-availability-group-sql-server.md)  
  
  
