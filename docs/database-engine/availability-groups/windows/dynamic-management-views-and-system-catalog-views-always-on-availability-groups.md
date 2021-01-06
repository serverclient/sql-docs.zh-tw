---
title: 可用性群組的 DMV 與系統目錄檢視
description: 動態管理和目錄檢視集合可協助您監視並診斷 Always On 可用性群組的健康情況。
ms.custom: seo-lt-2019
ms.date: 06/13/2017
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: troubleshooting
ms.assetid: 4320a4a4-6183-462b-8bda-e7424e7cb706
author: rothja
ms.author: jroth
ms.openlocfilehash: a2a69aa016ae258375c6095bbf38e58ddd75b981
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97643582"
---
# <a name="dynamic-management-views-and-system-catalog-views-always-on-availability-groups"></a>動態管理檢視與系統目錄檢視 (Always On 可用性群組)
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]
  本主題顯示在 Always On 動態管理檢視 (DMV) 上可用來監視可用性群組，並對可用性群組進行疑難排解的一些常用查詢。  
  
> [!TIP]  
>  在 Always On 儀表板中，您可以在個別的資料表標頭上按一下滑鼠右鍵，然後選取想要顯示或隱藏的 DMV，輕鬆地設定 GUI 顯示可用性複本和可用性資料庫的許多 DMV。  
  
 如需可用性群組 DMV 的詳細資訊，請參閱 [Always On 可用性群組動態管理檢視和函數 &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/always-on-availability-groups-dynamic-management-views-functions.md)。 如需可用性群組目錄檢視的詳細資訊，請參閱 [Always On 可用性群組目錄檢視 &#40;Transact-SQL&#41;](~/relational-databases/system-catalog-views/always-on-availability-groups-catalog-views-transact-sql.md)。  
  
## <a name="check-the-wsfc-cluster-node-configuration"></a>檢查 WSFC 叢集節點設定  
 下列 Transact-SQL (T-SQL) 查詢會擷取目前的 Windows Server 容錯移轉叢集 (WSFC) 叢集中所有節點的狀態。  
  
```sql  
use master  
go  
select * from sys.dm_hadr_cluster_members  
go  
```  
  
 此結果集會報告目前的 WSFC 叢集每個成員節點的狀態。 如果仲裁定義為 **節點與檔案共用多數**，也會報告檔案共用。 您可以查看每個節點的狀態，包含每個節點的投票加權 ([number_of_quorum_votes](~/relational-databases/system-dynamic-management-views/sys-dm-hadr-cluster-members-transact-sql.md) 值)。  
  
## <a name="explore-the-cluster-network"></a>探索叢集網路  
 下列查詢會擷取目前的 WSFC 叢集的網路設定。  
  
```sql  
select * from sys.dm_hadr_cluster_networks  
```  
  
 結果集會針對 WSFC 叢集中的每張網路介面卡包含一個資料列。 例如，在每個節點上包含兩張網路介面卡的雙節點叢集中，此查詢會傳回四個資料列。  
  
## <a name="explore-the-availability-groups"></a>探索可用性群組  
 下列查詢會擷取可用性群組的相關資訊。  
  
```sql  
select primary_replica, primary_recovery_health_desc, synchronization_health_desc from sys.dm_hadr_availability_group_states  
go  
select * from sys.availability_groups  
go  
select * from sys.availability_groups_cluster  
go  
```  
  
 DMV [sys.dm_hadr_availability_group_states &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/sys-dm-hadr-availability-group-states-transact-sql.md)、[sys.availability_groups &#40;Transact-SQL&#41;](~/relational-databases/system-catalog-views/sys-availability-groups-transact-sql.md) 和 [sys.availability_groups_cluster](~/relational-databases/system-catalog-views/sys-availability-groups-cluster-transact-sql.md) 都會傳回目前的 WSFC 叢集中可用性群組的相關資訊。 事實上，[sys.availability_groups &#40;Transact-SQL&#41;](~/relational-databases/system-catalog-views/sys-availability-groups-transact-sql.md) 和 [sys.availability_groups_cluster](~/relational-databases/system-catalog-views/sys-availability-groups-cluster-transact-sql.md) 似乎會傳回相同的資訊。  
  
 不過，[sys.availability_groups_cluster](~/relational-databases/system-catalog-views/sys-availability-groups-cluster-transact-sql.md) 會報告儲存在 WSFC 叢集中的可用性群組中繼資料，而 [sys.availability_groups &#40;Transact-SQL&#41;](~/relational-databases/system-catalog-views/sys-availability-groups-transact-sql.md) 則會報告快取在 SQL Server 處理序空間中的可用性群組中繼資料。 此外，這兩個 DMV 報告設定資訊，但是 [sys.dm_hadr_availability_group_states &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/sys-dm-hadr-availability-group-states-transact-sql.md) 則報告可用性群組目前的健康情況狀態。  
  
> [!IMPORTANT]  
>  此命名法也適用於產生可用性複本和可用性資料庫的 DMV。  
  
## <a name="explore-the-availability-replicas"></a>探索可用性複本  
 下列查詢會擷取在您的可用性群組中所定義可用性複本的相關資訊。  
  
```sql  
select replica_id, role_desc, connected_state_desc, synchronization_health_desc from sys.dm_hadr_availability_replica_states  
go  
select replica_server_name, replica_id, availability_mode_desc, endpoint_url from sys.availability_replicas  
go  
select replica_server_name, join_state_desc from sys.dm_hadr_availability_replica_cluster_states  
go  
```  
  
 與可用性群組 DMV 類似，您會發現三個報告可用性複本的 DMV。 [sys.dm_hadr_availability_replica_states](~/relational-databases/system-dynamic-management-views/sys-dm-hadr-availability-replica-states-transact-sql.md) 報告以本機方式快取在 SQL Server 中的可用性複本狀態資訊，而 [sys.dm_hadr_availability_replica_cluster_nodes](~/relational-databases/system-dynamic-management-views/sys-dm-hadr-availability-replica-cluster-states-transact-sql.md) 則報告 WSFC 叢集中可用性複本的狀態資訊。 最後，[sys.availability_replicas](~/relational-databases/system-dynamic-management-views/sys-dm-hadr-availability-replica-cluster-states-transact-sql.md) 報告可用性複本上的設定資料，這些資料會以本機方式快取在 SQL Server 中。  
  
## <a name="explore-availability-replica-health"></a>探索可用性複本健康情況  
 下列查詢會擷取可用性複本的目前健康情況資訊。  
  
```sql  
select replica_id, role_desc, recovery_health_desc, synchronization_health_desc from sys.dm_hadr_availability_replica_states  
go  
```  
  
 比較對主要複本和對次要複本的查詢結果，請注意，在次要複本上，只會報告該複本 (而非可用性群組中任何其他複本) 的健康情況資訊。  
  
## <a name="explore-the-availability-databases"></a>探索可用性資料庫  
 下列查詢會擷取在您的可用性群組中所定義可用性複本的相關資訊。 您可以觀察在可用性資料庫暫止移動資料之前和之後，查詢結果的變化。  
  
```sql
select * from sys.availability_databases_cluster  
go  
select group_database_id, database_name, is_failover_ready  from sys.dm_hadr_database_replica_cluster_states  
go  
select database_id, synchronization_state_desc, synchronization_health_desc, last_hardened_lsn, redo_queue_size, log_send_queue_size from sys.dm_hadr_database_replica_states  
go  
```  
  
 同樣地，有三個 DMV 會報告可用性資料庫。 [sys.availability_databases_cluster](~/relational-databases/system-catalog-views/sys-availability-databases-cluster-transact-sql.md) 報告 WSFC 叢集中可用性資料庫的設定資訊。 [sys.dm_hadr_database_replica_cluster_states](~/relational-databases/system-dynamic-management-views/sys-dm-hadr-database-replica-cluster-states-transact-sql.md) 報告資料庫複本的狀態資訊，這些資訊會以本機方式快取在 SQL Server 中。 當中包含一些重要的狀態資訊，例如可用性複本的容錯移轉整備。 最後，[sys.dm_hadr_database_replica_states](~/relational-databases/system-dynamic-management-views/sys-dm-hadr-database-replica-states-transact-sql.md) 是非常詳細的結果集，報告每個可用性資料庫的身分識別與狀態資訊，例如主要和次要資料庫複本記錄的 LSN 進度記錄資訊。  
  
## <a name="explore-availability-database-health"></a>探索可用性資料庫健康情況  
 下列查詢會擷取複本上每個可用性資料庫的健康情況資訊。 您可以觀察在可用性資料庫暫止移動資料之前和之後，查詢結果的變化。  
  
```sql  
select dc.database_name, dr.database_id, dr.synchronization_state_desc,   
dr.suspend_reason_desc, dr.synchronization_health_desc  
from sys.dm_hadr_database_replica_states dr  join sys.availability_databases_cluster dc  
on dr.group_database_id=dc.group_database_id   
where is_local=1  
go  
```  
  
  
