---
title: 可用性群組系統物件參考
description: 可以在使用 Always On 可用性群組時所使用之各種系統物件的參考。
ms.custom: seo-lt-2019
ms.date: 04/03/2010
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: reference
author: cawrites
ms.author: chadam
monikerRange: '>=sql-server-2016'
ms.openlocfilehash: 340fcfa12f3432a09821132f44e8d58672be8198
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97640872"
---
# <a name="always-on-availability-group-system-object-reference"></a>AlwaysOn 可用性群組系統物件參考

[!INCLUDE[sql windows only](../../../includes/applies-to-version/sql-windows-only.md)]
    
本主題作為使用 Always On 可用性群組時之所有各種可用系統物件的參考頁面。 

## <a name="system-catalog-views"></a>系統目錄檢視

| 系統目錄檢視 | 描述|
| :------ | :----------------------------- |
| [監視可用性資料庫](../../../relational-databases/system-catalog-views/sys-availability-databases-cluster-transact-sql.md)   | 針對 SQL Server 執行個體上的每一個可用性資料庫各包含一個資料列，該執行個體會針對 Windows Server 容錯移轉叢集 (WSFC) 叢集中的任何 Always On 可用性群組來裝載可用性複本，不論本機資料庫複本是否已聯結可用性群組。 |
| [sys.availability_group_listener_ip_addresses](../../../relational-databases/system-catalog-views/sys-availability-group-listener-ip-addresses-transact-sql.md)  | 針對 Windows Server 容錯移轉叢集 (WSFC) 叢集中與任何 Always On 可用性群組接聽程式相關聯的每一個 IP 位址，各傳回一個資料列。 |
| [sys.availability_group_listeners](../../../relational-databases/system-catalog-views/sys-availability-group-listeners-transact-sql.md)    | 針對每一個 Always On 可用性群組，傳回零個資料列，表示沒有網路名稱與可用性群組相關聯，或針對 Windows Server 容錯移轉叢集服務 (WSFC) 叢集中的每個可用性群組接聽程式設定傳回一個資料列。  |
| [sys.availability_groups](../../../relational-databases/system-catalog-views/sys-availability-groups-transact-sql.md)   | 針對裝載可用性複本的 SQL Server 主機本機執行個體的每一個可用性群組，各傳回一個資料列。 每一個資料列都包含可用性群組中繼資料的快取副本。 |
| [sys.availability_groups_cluster](../../../relational-databases/system-catalog-views/sys-availability-groups-cluster-transact-sql.md)    | 傳回 Windows Server 容錯移轉叢集 (WSFC) 中每個 Always On 可用性群組的資料列。 每個資料列都包含 WSFC 叢集中的可用性群組中繼資料。 |
| [sys.availability_read_only_routing_lists](../../../relational-databases/system-catalog-views/sys-availability-read-only-routing-lists-transact-sql.md)    | 針對 WSFC 容錯移轉叢集中 AlwaysOn 可用性群組內每個可用性複本的唯讀路由清單，各傳回一個資料列。 |
| [sys.availability_replicas](../../../relational-databases/system-catalog-views/sys-availability-replicas-transact-sql.md)    | 針對屬於 WSFC 容錯移轉叢集中 Always On 可用性群組的每個可用性複本傳回一個資料列。 |
| &nbsp; | &nbsp; |

## <a name="system-dynamic-management-views"></a>系統動態管理檢視


| 系統動態管理檢視 | 描述|
| :------ | :----------------------------- |
| [sys.dm_hadr_auto_page_repair](../../../relational-databases/system-dynamic-management-views/sys-dm-hadr-auto-page-repair-transact-sql.md)   | 針對可用性複本上的任何可用性資料庫進行的每個自動修復頁面嘗試行為，各傳回一個資料列，該可用性複本是針對伺服器執行個體的任何可用性群組所裝載。  |
| [sys.dm_hadr_availability_group_states](../../../relational-databases/system-dynamic-management-views/sys-dm-hadr-availability-group-states-transact-sql.md)    | 針對擁有 SQL Server 本機執行個體之可用性複本的每一個 Always On 可用性群組，各傳回一個資料列。 每個資料列會顯示定義給定之可用性群組健全狀況的狀態。 |
| [sys.dm_hadr_availability_replica_cluster_nodes](../../../relational-databases/system-dynamic-management-views/sys-dm-hadr-availability-replica-cluster-nodes-transact-sql.md)     | 針對 Windows Server 容錯移轉叢集 (WSFC) 叢集中 Always On 可用性群組的每一個可用性複本 (不論聯結狀態為何)，各傳回一個資料列 |
| [sys.dm_hadr_availability_replica_cluster_states](../../../relational-databases/system-dynamic-management-views/sys-dm-hadr-availability-replica-cluster-states-transact-sql.md)     | 針對 Windows Server 容錯移轉叢集 (WSFC) 叢集中所有 Always On 可用性群組 (不論複本位置為何) 的每一個 Always On 可用性複本 (不論其聯結狀態為何)，各傳回一個資料列。 |
| [sys.dm_hadr_availability_replica_states](../../../relational-databases/system-dynamic-management-views/sys-dm-hadr-availability-replica-states-transact-sql.md)    | 傳回每個本機複本的資料列，並針對同一個 Always On 群組中當做本機複本的每一個遠端複本，各傳回一個資料列。 每一個資料列都包含有關給定複本狀態的資訊。 |
| [sys.dm_hadr_cluster](../../../relational-databases/system-dynamic-management-views/sys-dm-hadr-cluster-transact-sql.md)    | 傳回資料列，以公開叢集名稱以及有關仲裁的資訊 |
| [sys.dm_hadr_cluster_members](../../../relational-databases/system-dynamic-management-views/sys-dm-hadr-cluster-members-transact-sql.md)    | 針對構成仲裁的成員及它們每個的狀態，各傳回一個資料列 |
| [sys.dm_hadr_cluster_networks](../../../relational-databases/system-dynamic-management-views/sys-dm-hadr-cluster-networks-transact-sql.md)    | 針對參與可用性群組之子網路組態的每個 WSFC 叢集成員，各傳回一個資料列。  |
| [sys.availability_databases_cluster](../../../relational-databases/system-dynamic-management-views/sys-dm-hadr-database-replica-cluster-states-transact-sql.md)     | 傳回包含資訊的資料列，該資訊的目的是為了提供您 Windows Server 容錯移轉叢集 (WSFC) 叢集中每個 Always On 可用性群組內 AlwaysOn 可用性群組的可用性資料庫健康情況見解。  |
| [sys.dm_hadr_database_replica_states](../../../relational-databases/system-dynamic-management-views/sys-dm-hadr-database-replica-states-transact-sql.md)    | 針對參與 Always On 可用性群組的每一個資料庫傳回一個資料列 (SQL Server 本機執行個體正在裝載此群組的可用性複本)。 |
| [sys.dm_hadr_instance_node_map](../../../relational-databases/system-dynamic-management-views/sys-dm-hadr-instance-node-map-transact-sql.md)    | 對於裝載已聯結其 Always On 可用性群組之可用性複本的每個 SQL Server 執行個體，傳回裝載伺服器執行個體的 Windows Server 容錯移轉叢集 (WSFC) 節點名稱。 |
| [sys.dm_hadr_name_id_map](../../../relational-databases/system-dynamic-management-views/sys-dm-hadr-name-id-map-transact-sql.md)    | 顯示 SQL Server 目前執行個體已聯結之 AlwaysOn 可用性群組與三個唯一識別碼的對應：可用性群組識別碼、WSFC 資源識別碼和 WSFC 群組識別碼。 |
| [sys.dm_tcp_listener_states](../../../relational-databases/system-dynamic-management-views/sys-dm-tcp-listener-states-transact-sql.md)    | 針對每個 TCP 接聽程式傳回一個包含動態狀態資訊的資料列。 |
| &nbsp; | &nbsp; |

## <a name="system-functions"></a>系統函式


| 系統函式 | 描述|
| :------ | :----------------------------- |
| [sys.fn_hadr_is_primary_replica](../../../relational-databases/system-functions/sys-fn-hadr-is-primary-replica-transact-sql.md)  | 用於判斷目前的複本是否為主要複本。 |
| [sys.fn_hadr_backup_is_preferred_replica](../../../relational-databases/system-functions/sys-fn-hadr-backup-is-preferred-replica-transact-sql.md)    | 用於判斷目前的複本是否為慣用的備份複本。 |
| [sys.fn_hadr_distributed_ag_replica](../../../relational-databases/system-functions/sys-fn-hadr-distributed-ag-replica-transact-sql.md)    | 用於將分散式可用性群組中的複本對應到本機可用性群組。 |
| &nbsp; | &nbsp; |


  
## <a name="see-also"></a>另請參閱  
 [AlwaysOn 可用性群組概觀 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server.md)   

  
