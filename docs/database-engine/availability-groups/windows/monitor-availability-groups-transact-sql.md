---
title: 使用 Transact-SQL (T-SQL) 監視可用性群組
description: 使用 Transact-SQL SELECT 陳述式來監視 Always On 可用性群組和其複本與資料庫。
ms.custom: seodec18
ms.date: 05/17/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: conceptual
helpviewer_keywords:
- Availability Groups [SQL Server], monitoring
- dynamic management views [SQL Server], AlwaysOn Availability Groups
- Availability Groups [SQL Server], availability replicas
- Availability Groups [SQL Server], listeners
- Availability Groups [SQL Server], databases
- catalog views [SQL Server], AlwaysOn Availability Groups
ms.assetid: 881a34de-8461-4811-8c62-322bf7226bed
author: cawrites
ms.author: chadam
ms.openlocfilehash: a3a3110540340da3dcb05c660e8c0027a0ba6d97
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97642960"
---
# <a name="monitor-availability-groups-transact-sql"></a>監視可用性群組 (Transact-SQL)
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]
  為了透過 [!INCLUDE[tsql](../../../includes/tsql-md.md)]監視可用性群組和複本，以及相關聯的資料庫， [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] 提供一組目錄和動態管理檢視與伺服器屬性。 您可以透過 [!INCLUDE[tsql](../../../includes/tsql-md.md)] SELECT 陳述式使用這些檢視來監視可用性群組及其複本和資料庫。 針對給定可用性群組所傳回的資訊取決於連接到的是裝載主要複本或次要複本的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體。  
  
> [!TIP]  
>  許多這些檢視可在單一查詢中聯結，透過檢視識別碼資料行從多個檢視傳回資訊。  
  
  
##  <a name="permissions"></a><a name="Permissions"></a> 權限  
 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] 目錄檢視需要伺服器執行個體的 VIEW ANY DEFINITION 權限。 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] 動態管理檢視需要伺服器的 VIEW SERVER STATE 權限。  
  
##  <a name="monitoring-the-always-on-availability-groups-feature-on-a-server-instance"></a><a name="AoAgFeatureOnSI"></a> 監視伺服器執行個體上的 AlwaysOn 可用性群組功能  
 若要監視伺服器執行個體上的 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] 功能，請使用下列內建函數：  
  
 [SERVERPROPERTY](../../../t-sql/functions/serverproperty-transact-sql.md) 函數  
 傳回有關 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] 是否已啟用的伺服器屬性資訊，如果已啟用，也指出它是否已在伺服器執行個體上啟動。  
  
 **資料行名稱：** IsHadrEnabled、HadrManagerStatus  
  
##  <a name="monitoring-availability-groups-on-the-wsfc-cluster"></a><a name="WSFC"></a> 監視 WSFC 叢集中的可用性群組  
 若要監視裝載已啟用 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)]之本機伺服器執行個體的 Windows Server 容錯移轉叢集 (WSFC) 叢集，請使用下列檢視：  
  
 [sys.dm_hadr_cluster](../../../relational-databases/system-dynamic-management-views/sys-dm-hadr-cluster-transact-sql.md)  
 如果裝載已啟用 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] 之 SQL Server 執行個體的 Windows Server 容錯移轉叢集 (WSFC) 節點有 WSFC 仲裁，則 **sys.dm_hadr_cluster** 會傳回一個資料列，該資料列會公開叢集名稱以及有關此仲裁的相關資訊。 如果 WSFC 節點沒有仲裁，則不傳回任何資料列。  
  
 **資料行名稱：** cluster_name、quorum_type、quorum_type_desc、quorum_state、quorum_state_desc  
  
 [sys.dm_hadr_cluster_members](../../../relational-databases/system-dynamic-management-views/sys-dm-hadr-cluster-members-transact-sql.md)  
 如果裝載已啟用 AlwaysOn 之 SQL Server 本機執行個體的 WSFC 節點有 WSFC 仲裁，則針對構成仲裁的每個成員及其狀態各傳回一個資料列。  
  
 **資料行名稱：** member_name、member_type、member_type_desc、member_state、member_state_desc、number_of_quorum_votes  
  
 [sys.dm_hadr_cluster_networks](../../../relational-databases/system-dynamic-management-views/sys-dm-hadr-cluster-networks-transact-sql.md)  
 針對參與可用性群組之子網路組態的每個成員，各傳回一個資料列。 您可以使用此動態管理檢視來驗證為每個可用性複本所設定的網路虛擬 IP。  
  
 **資料行名稱：** member_name、network_subnet_ip、network_subnet_ipv4_mask、network_subnet_prefix_length、is_public、is_ipv4  
  
 **主索引鍵：** member_name + network_subnet_IP + network_subnet_prefix_length  
  
 [sys.dm_hadr_instance_node_map](../../../relational-databases/system-dynamic-management-views/sys-dm-hadr-instance-node-map-transact-sql.md)  
 對於裝載已聯結其 AlwaysOn 可用性群組之可用性複本的每個 SQL Server 執行個體，傳回裝載伺服器執行個體的 Windows Server 容錯移轉叢集 (WSFC) 節點名稱。 這個動態管理檢視有下列用途：  
  
-   這個動態管理檢視適用於偵測有多個可用性複本裝載於同一個 WSFC 節點的可用性群組，如果可用性群組不正確地設定，在 FCI 容錯移轉後可能會發生此不支援的組態狀況。  
  
-   當多個 SQL Server 執行個體裝載於同一個 WSFC 節點時，資源 DLL 會使用此動態管理檢視，判斷要連接的 SQL Server 執行個體。  
  
 **資料行名稱：** ag_resource_id、instance_name、node_name  
  
 [sys.dm_hadr_name_id_map](../../../relational-databases/system-dynamic-management-views/sys-dm-hadr-name-id-map-transact-sql.md)  
 顯示 SQL Server 目前執行個體已聯結之 AlwaysOn 可用性群組與三個唯一識別碼的對應：可用性群組識別碼、WSFC 資源識別碼和 WSFC 群組識別碼。 此對應的目的是要處理重新命名 WSFC 資源/群組的案例。  
  
 **資料行名稱：** ag_name、ag_id、ag_resource_id、ag_group_id  
  
> [!NOTE]  
>  另請參閱本主題稍後 [監視可用性複本](#AvReplicas)一節中的 **sys.dm_hadr_availability_replica_cluster_nodes** 和 **sys.dm_hadr_availability_replica_cluster_states**，以及 [監視可用性資料庫](#AvDbs)一節中的 **sys.availability_databases_cluster** 和 **sys.dm_hadr_database_replica_cluster_states**。  
  
 如需 WSFC 叢集和 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] 的相關資訊，請參閱 [SQL Server 的 Windows Server 容錯移轉叢集 &#40;WSFC&#41;](../../../sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server.md) 和[容錯移轉叢集和 AlwaysOn 可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/failover-clustering-and-always-on-availability-groups-sql-server.md)。  
  
##  <a name="monitoring-availability-groups"></a><a name="AvGroups"></a> 監視可用性群組  
 若要監視伺服器執行個體裝載其可用性複本的可用性群組，請使用下列檢視：  
  
 [sys.availability_groups](../../../relational-databases/system-catalog-views/sys-availability-groups-transact-sql.md)  
 針對裝載可用性複本的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 本機執行個體的每一個可用性群組，各傳回一個資料列。 每一個資料列都包含可用性群組中繼資料的快取副本。  
  
 **資料行名稱：** group_id、name、resource_id、resource_group_id、failure_condition_level、health_check_timeout、automated_backup_preference、automated_backup_preference_desc  
  
 [sys.availability_groups_cluster](../../../relational-databases/system-catalog-views/sys-availability-groups-cluster-transact-sql.md)  
 針對 WSFC 叢集中的每一個可用性群組，各傳回一個資料列。 每個資料列都包含 Windows Server 容錯移轉叢集 (WSFC) 叢集中的可用性群組中繼資料。  
  
 **資料行名稱：** group_id、name、resource_id、resource_group_id、failure_condition_level、health_check_timeout、automated_backup_preference、automated_backup_preference_desc  
  
 [sys.dm_hadr_availability_group_states](../../../relational-databases/system-dynamic-management-views/sys-dm-hadr-availability-group-states-transact-sql.md)  
 針對擁有 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]本機執行個體之可用性複本的每一個可用性群組，各傳回一個資料列。 每個資料列會顯示定義給定之可用性群組健全狀況的狀態。  
  
 **資料行名稱：** group_id、primary_replica、primary_recovery_health、primary_recovery_health_desc、secondary_recovery_health、secondary_recovery_health_desc、synchronization_health、synchronization_health_desc  
  
##  <a name="monitoring-availability-replicas"></a><a name="AvReplicas"></a> sys.dm_hadr_availability_replica_cluster_states  
 若要監視可用性複本，請使用下列檢視和系統函數：  
  
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
 判斷目前的複本是否為慣用的備份複本。  
  
> [!NOTE]  
>  如需可用性複本效能計數器 ( **SQLServer:Availability Replica**  效能物件) 的相關資訊，請參閱 [SQL Server、可用性複本](../../../relational-databases/performance-monitor/sql-server-availability-replica.md)。  
  
##  <a name="monitoring-availability-databases"></a><a name="AvDbs"></a> sys.dm_hadr_database_replica_cluster_states  
 若要監視可用性資料庫，請使用下列檢視：  
  
 [監視可用性資料庫](../../../relational-databases/system-catalog-views/sys-availability-databases-cluster-transact-sql.md)  
 針對屬於叢集中所有 AlwaysOn 可用性群組的 SQL Server 執行個體上的每一個資料庫，各包含一個資料列，無論本機資料庫複本是否已聯結至可用性群組。  
  
> [!NOTE]  
>  當資料庫加入至可用性群組時，主要資料庫會自動聯結至此群組。 次要資料庫必須先在每個次要複本上備妥，然後才能聯結至可用性群組。  
  
 **資料行名稱：** group_id、group_database_id、database_name  
  
 [sys.databases](../../../relational-databases/system-catalog-views/sys-databases-transact-sql.md)  
 針對 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]執行個體中的每個資料庫，各包含一個資料列。 如果資料庫屬於某個可用性複本，該資料庫的資料列會顯示複本的 GUID，以及資料庫在其可用性群組內的唯一識別碼。  
  
 **[!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] 資料行名稱：** replica_id、group_database_id  
  
 [sys.dm_hadr_auto_page_repair](../../../relational-databases/system-dynamic-management-views/sys-dm-hadr-auto-page-repair-transact-sql.md)  
 針對可用性複本上的任何可用性資料庫進行的每個自動修復頁面嘗試行為，各傳回一個資料列，該可用性複本是針對伺服器執行個體的任何可用性群組所裝載。 這個檢視包含在給定之主要或次要資料庫上進行最新自動修復頁面嘗試行為的資料列，而且每個資料庫最多 100 個資料列。 一旦資料庫到達上限時，下一個自動修復頁面嘗試行為的資料列就會取代其中一個現有的項目。  
  
 **資料行名稱：** database_id、file_id、page_id、error_type、page_status、modification_time  
  
 [sys.dm_hadr_database_replica_states](../../../relational-databases/system-dynamic-management-views/sys-dm-hadr-database-replica-states-transact-sql.md)  
 針對參與任何可用性群組的每一個資料庫 ( [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 本機執行個體正在裝載此群組的可用性複本)，各傳回一個資料列。  
  
 **資料行名稱：** database_id、group_id、replica_id、group_database_id、is_local、synchronization_state、synchronization_state_desc、is_commit_participant、synchronization_health、synchronization_health_desc、database_state、database_state_desc、is_suspended、suspend_reason、suspend_reason_desc、recovery_lsn、truncation_lsn、last_sent_lsn、last_sent_time、last_received_lsn、last_received_time、last_hardened_lsn、last_hardened_time、last_redone_lsn、last_redone_time、log_send_queue_size、log_send_rate、redo_queue_size、redo_rate、filestream_send_rate、end_of_log_lsn、last_commit_lsn、last_commit_time、low_water_mark_for_ghosts  
  
 [sys.availability_databases_cluster](../../../relational-databases/system-dynamic-management-views/sys-dm-hadr-database-replica-cluster-states-transact-sql.md)  
 傳回包含資訊的資料列，該資訊的目的是為了讓您深入了解 Windows Server 容錯移轉叢集 (WSFC) 叢集中每個可用性群組內可用性資料庫的健全狀況。 當您計劃或回應容錯移轉，或要探索可用性群組中哪個次要複本阻止給定之主要資料庫的記錄截斷時，這個動態管理檢視相當實用。  
  
 **資料行名稱：** replica_id、group_database_id、database_name、is_failover_ready、is_pending_secondary_suspend、is_database_joined、recovery_lsn、truncation_lsn  
  
> [!NOTE]  
>  主要複本位置是可用性群組的授權來源。  
  
> [!NOTE]  
>  如需可用性資料庫 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] 效能計數器 ( **SQLServer:Database Replica** 效能物件) 的相關資訊，請參閱 [SQL Server、資料庫複本](../../../relational-databases/performance-monitor/sql-server-database-replica.md)。 此外，若要監視可用性資料庫上的交易記錄活動，請使用 **SQLServer:Databases** 效能物件的下列計數器：**Log Flush Write Time (ms)** 、**Log Flushes/sec**、**Log Pool Cache Misses/sec**、**Log Pool Disk Reads/sec** 和 **Log Pool Requests/sec**。如需詳細資訊，請參閱 [SQL Server, Databases Object](../../../relational-databases/performance-monitor/sql-server-databases-object.md)。  
  
##  <a name="monitoring-availability-group-listeners"></a><a name="AGlisteners"></a> 監視可用性群組接聽程式  
 若要監視 WSFC 叢集子網路上的可用性群組接聽程式，請使用下列檢視：  
  
 [sys.availability_group_listener_ip_addresses](../../../relational-databases/system-catalog-views/sys-availability-group-listener-ip-addresses-transact-sql.md)  
 針對目前上線供可用性群組接聽程式使用的每個符合標準虛擬 IP 位址傳回一個資料列。  
  
 **資料行名稱** ：listener_id、ip_address、ip_subnet_mask、is_dhcp、network_subnet_ip、network_subnet_prefix_length、network_subnet_ipv4_mask、state、state_desc  
  
 [sys.availability_group_listeners](../../../relational-databases/system-catalog-views/sys-availability-group-listeners-transact-sql.md)  
 若為給定的可用性群組，傳回零個資料列，表示沒有網路名稱與可用性群組相關聯，或針對 WSFC 叢集中的每個可用性群組接聽程式組態傳回一個資料列。  
  
 **資料行名稱** ：group_id、listener_id、dns_name、port、is_conformant、ip_configuration_string_from_cluster  
  
 [sys.dm_tcp_listener_states](../../../relational-databases/system-dynamic-management-views/sys-dm-tcp-listener-states-transact-sql.md)  
 針對每個 TCP 接聽程式傳回一個包含動態狀態資訊的資料列。  
  
 **資料行名稱：** listener_id、ip_address、is_ipv4、port、type、type_desc、state、state_desc、start_time  
  
 **主索引鍵：** listener_id  
  
 如需可用性群組接聽程式的相關資訊，請參閱[可用性群組接聽程式、用戶端連接性及應用程式容錯移轉 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/listeners-client-connectivity-application-failover.md)。  
  
##  <a name="related-tasks"></a><a name="RelatedTasks"></a> 相關工作  
 **AlwaysOn 可用性群組監視工作：**  
  
-   [使用物件總管詳細資料監視可用性群組 &#40;SQL Server Management Studio&#41;](../../../database-engine/availability-groups/windows/use-object-explorer-details-to-monitor-availability-groups.md)  
  
-   [檢視可用性群組屬性 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/view-availability-group-properties-sql-server.md)  
  
-   [檢視可用性複本屬性 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/view-availability-replica-properties-sql-server.md)  
  
-   [檢視可用性群組接聽程式屬性 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/view-availability-group-listener-properties-sql-server.md)  
  
 **AlwaysOn 可用性群組監視參考 (Transact-SQL)：**  
  
-   [SERVERPROPERTY &#40;Transact-SQL&#41;](../../../t-sql/functions/serverproperty-transact-sql.md)  
  
-   [sys.availability_group_listener_ip_addresses &#40;Transact-SQL&#41;](../../../relational-databases/system-catalog-views/sys-availability-group-listener-ip-addresses-transact-sql.md)  
  
-   [sys.availability_group_listeners &#40;Transact-SQL&#41;](../../../relational-databases/system-catalog-views/sys-availability-group-listeners-transact-sql.md)  
  
-   [sys.availability_databases_cluster &#40;Transact-SQL&#41;](../../../relational-databases/system-catalog-views/sys-availability-databases-cluster-transact-sql.md)  
  
-   [sys.availability_groups &#40;Transact-SQL&#41;](../../../relational-databases/system-catalog-views/sys-availability-groups-transact-sql.md)  
  
-   [sys.availability_read_only_routing_lists &#40;Transact-SQL&#41;](../../../relational-databases/system-catalog-views/sys-availability-read-only-routing-lists-transact-sql.md)  
  
-   [sys.availability_replicas &#40;Transact-SQL&#41;](../../../relational-databases/system-catalog-views/sys-availability-replicas-transact-sql.md)  
  
-   [sys.dm_hadr_availability_replica_cluster_nodes &#40;Transact-SQL&#41;](../../../relational-databases/system-dynamic-management-views/sys-dm-hadr-availability-replica-cluster-nodes-transact-sql.md)  
  
-   [sys.dm_hadr_availability_replica_cluster_states &#40;Transact-SQL&#41;](../../../relational-databases/system-dynamic-management-views/sys-dm-hadr-availability-replica-cluster-states-transact-sql.md)  
  
-   [sys.database_mirroring_endpoints &#40;Transact-SQL&#41;](../../../relational-databases/system-catalog-views/sys-database-mirroring-endpoints-transact-sql.md)  
  
-   [sys.dm_hadr_auto_page_repair &#40;Transact-SQL&#41;](../../../relational-databases/system-dynamic-management-views/sys-dm-hadr-auto-page-repair-transact-sql.md)  
  
-   [sys.dm_hadr_availability_group_states &#40;Transact-SQL&#41;](../../../relational-databases/system-dynamic-management-views/sys-dm-hadr-availability-group-states-transact-sql.md)  
  
-   [sys.dm_hadr_availability_replica_cluster_states &#40;Transact-SQL&#41;](../../../relational-databases/system-dynamic-management-views/sys-dm-hadr-availability-replica-cluster-states-transact-sql.md)  
  
-   [sys.dm_hadr_availability_replica_states &#40;Transact-SQL&#41;](../../../relational-databases/system-dynamic-management-views/sys-dm-hadr-availability-replica-states-transact-sql.md)  
  
-   [sys.dm_hadr_database_replica_states &#40;Transact-SQL&#41;](../../../relational-databases/system-dynamic-management-views/sys-dm-hadr-database-replica-states-transact-sql.md)  
  
-   [sys.dm_hadr_database_replica_cluster_states &#40;Transact-SQL&#41;](../../../relational-databases/system-dynamic-management-views/sys-dm-hadr-database-replica-cluster-states-transact-sql.md)  
  
-   [sys.dm_hadr_cluster &#40;Transact-SQL&#41;](../../../relational-databases/system-dynamic-management-views/sys-dm-hadr-cluster-transact-sql.md)  
  
-   [sys.dm_hadr_cluster_members &#40;Transact-SQL&#41;](../../../relational-databases/system-dynamic-management-views/sys-dm-hadr-cluster-members-transact-sql.md)  
  
-   [sys.dm_hadr_cluster_networks &#40;Transact-SQL&#41;](../../../relational-databases/system-dynamic-management-views/sys-dm-hadr-cluster-networks-transact-sql.md)  
  
-   [sys.dm_hadr_database_replica_cluster_states &#40;Transact-SQL&#41;](../../../relational-databases/system-dynamic-management-views/sys-dm-hadr-database-replica-cluster-states-transact-sql.md)  
  
-   [sys.dm_hadr_database_replica_states &#40;Transact-SQL&#41;](../../../relational-databases/system-dynamic-management-views/sys-dm-hadr-database-replica-states-transact-sql.md)  
  
-   [sys.dm_hadr_instance_node_map &#40;Transact-SQL&#41;](../../../relational-databases/system-dynamic-management-views/sys-dm-hadr-instance-node-map-transact-sql.md)  
  
-   [sys.dm_hadr_name_id_map &#40;Transact-SQL&#41;](../../../relational-databases/system-dynamic-management-views/sys-dm-hadr-name-id-map-transact-sql.md)  
  
-   [sys.dm_os_performance_counters &#40;Transact-SQL&#41;](../../../relational-databases/system-dynamic-management-views/sys-dm-os-performance-counters-transact-sql.md)  
  
-   [sys.dm_tcp_listener_states &#40;Transact-SQL&#41;](../../../relational-databases/system-dynamic-management-views/sys-dm-tcp-listener-states-transact-sql.md)  
  
-   [sys.fn_hadr_backup_is_preferred_replica &#40;Transact-SQL&#41;](../../../relational-databases/system-functions/sys-fn-hadr-backup-is-preferred-replica-transact-sql.md)  
  
 **AlwaysOn 效能計數器：**  
  
-   [SQL Server、可用性複本](../../../relational-databases/performance-monitor/sql-server-availability-replica.md)  
  
-   [SQL Server 的 Database Replica](../../../relational-databases/performance-monitor/sql-server-database-replica.md)  
  
-   [SQL Server, Databases Object](../../../relational-databases/performance-monitor/sql-server-databases-object.md)  
  
 **AlwaysOn 可用性群組的原則式管理**  
  
-   [使用 AlwaysOn 原則檢視可用性群組的健全狀況 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/use-always-on-policies-to-view-the-health-of-an-availability-group-sql-server.md)  
  
-   [使用 AlwaysOn 儀表板 &#40;SQL Server Management Studio&#41;](../../../database-engine/availability-groups/windows/use-the-always-on-dashboard-sql-server-management-studio.md)  
  
## <a name="see-also"></a>另請參閱  
 [AlwaysOn 可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/always-on-availability-groups-sql-server.md)   
 [AlwaysOn 可用性群組概觀 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server.md)   
 [監視可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server.md)  
  
  
