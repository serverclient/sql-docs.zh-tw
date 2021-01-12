---
description: sys.availability_replicas (Transact-SQL)
title: sys.availability_replicas (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 10/16/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- availability_replicas_TSQL
- availability_replicas
- sys.availability_replicas
- sys.availability_replicas_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- Availability Groups [SQL Server], monitoring
- sys.availability_replicas catalog view
ms.assetid: 0a06e9b6-a1e4-4293-867b-5c3f5a8ff62c
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: bd1dd844b84e2d437434fc78b380872019b78660
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98102208"
---
# <a name="sysavailability_replicas-transact-sql"></a>sys.availability_replicas (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

針對屬於 WSFC 容錯移轉叢集中 Always On 可用性群組的每個可用性複本傳回一個資料列。  
  
如果本機伺服器執行個體無法與 WSFC 容錯移轉叢集聯繫，例如由於叢集已關閉或仲裁已遺失，則只會傳回本機可用性複本的資料列。 這些資料列只會包含在本機快取於中繼資料內的資料行。  
  
 
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**replica_id**|**uniqueidentifier**|複本的唯一識別碼。|  
|**group_id**|**uniqueidentifier**|複本所屬之可用性群組的唯一識別碼。|  
|**replica_metadata_id**|**int**|Database Engine 中可用性複本之本機中繼資料物件的識別碼。|  
|**replica_server_name**|**nvarchar(256)**|裝載這個複本之 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體的伺服器名稱，如果是非預設執行個體，則是它的執行個體名稱。|  
|**owner_sid**|**varbinary(85)**|針對這個可用性複本的外部擁有者，註冊給這個伺服器執行個體的安全性識別碼 (SID)。<br /><br /> 非本機可用性複本為 NULL。|  
|**endpoint_url**|**nvarchar(128)**|使用者指定之資料庫鏡像端點的字串表示法，該端點是由主要與次要複本之間的資料同步處理連接所使用。 如需這些端點 URL 語法的相關資訊，請參閱[在加入或修改可用性複本時指定端點 URL &#40;SQL Server&#41;](../../database-engine/availability-groups/windows/specify-endpoint-url-adding-or-modifying-availability-replica.md)。<br /><br /> NULL = 無法聯繫 WSFC 容錯移轉叢集。<br /><br /> 若要變更這個端點，請使用[ALTER AVAILABILITY GROUP](../../t-sql/statements/alter-availability-group-transact-sql.md)語句的 ENDPOINT_URL 選項 [!INCLUDE[tsql](../../includes/tsql-md.md)] 。|  
|**availability_mode**|**tinyint**|複本的可用性模式，下列其中一項：<br /><br /> 0 &#124; 非同步認可。 主要複本可認可交易，而不需要等候次要複本將記錄寫入磁碟中。<br /><br /> 1 &#124; 同步認可。 主要複本會等候認可給定交易，直到次要複本將交易寫入磁碟為止。<br /><br />4 &#124; 僅限設定。 主要複本會以同步方式將可用性群組設定中繼資料傳送至複本。 使用者資料不會傳送至複本。 可在 SQL Server 2017 CU1 和更新版本中使用。<br /><br /> 如需詳細資訊，請參閱 [可用性模式 &#40;AlwaysOn 可用性群組&#41;](../../database-engine/availability-groups/windows/availability-modes-always-on-availability-groups.md)或 PowerShell，針對 AlwaysOn 可用性群組執行規劃的手動容錯移轉或強制手動容錯移轉 (強制容錯移轉)。|  
|**availability_mode_desc**|**nvarchar(60)**|**可用性 \_ 模式** 的描述，下列其中一個：<br /><br /> 非同步 \_ 認可<br /><br /> 同步 \_ 認可<br /><br /> \_僅限設定<br /><br /> 若要變更可用性複本的可用性模式，請使用[ALTER AVAILABILITY GROUP](../../t-sql/statements/alter-availability-group-transact-sql.md)語句的 AVAILABILITY_MODE 選項 [!INCLUDE[tsql](../../includes/tsql-md.md)] 。<br/><br>您無法將複本的可用性模式變更為 [ \_ 僅限設定]。 您無法將 \_ 僅限設定複本變更為次要或主要複本。 |  
|**容錯移轉 \_ 模式**|**tinyint**|可用性複本的 [容錯移轉模式](../../database-engine/availability-groups/windows/failover-and-failover-modes-always-on-availability-groups.md) ，下列其中一個：<br /><br /> 0 &#124; 自動容錯移轉。 此複本可能是自動容錯移轉的目標。  只有當可用性模式設定為同步認可 (**可用性 \_ 模式** = 1) 且可用性複本目前已同步處理時，才支援自動容錯移轉。<br /><br /> 1 &#124; 手動容錯移轉。 如果容錯移轉到次要複本的程序設定為手動容錯移轉，則必須由資料庫管理員手動起始。 執行的容錯移轉類型將取決於次要複本是否同步處理，如下所示：<br /><br /> 如果可用性複本並未同步處理或者依然在同步處理，只會發生強制容錯移轉 (可能會遺失資料)。<br /><br /> 如果可用性模式設定為同步認可 (**可用性 \_ 模式** = 1) 且可用性複本目前已同步處理，則不會遺失資料的手動容錯移轉。<br /><br /> 若要在可用性複本中查看每個可用性資料庫的資料庫同步處理健全狀況匯總，請使用 [sys.dm_hadr_availability_replica_states](../../relational-databases/system-dynamic-management-views/sys-dm-hadr-availability-replica-states-transact-sql.md)動態管理檢視的 [**同步處理 \_ 健全** 狀況] 和 [**同步處理健全狀況 \_ \_ desc** ] 資料行。 此積存會考量每個可用性資料庫的同步處理狀態及其可用性複本的可用性模式。<br /><br /> **注意：** 若要查看給定可用性資料庫的同步處理健全狀況，請查詢 [sys.dm_hadr_database_replica_states](../../relational-databases/system-dynamic-management-views/sys-dm-hadr-database-replica-states-transact-sql.md)動態管理檢視的 [**同步處理 \_ 狀態**] 和 [**同步處理 \_ 健全狀況**] 資料行。|  
|**容錯移轉 \_ 模式 \_ desc**|**nvarchar(60)**|**容錯移轉 \_ 模式** 的描述，下列其中一個：<br /><br /> MANUAL<br /><br /> AUTOMATIC<br /><br /> 若要變更容錯移轉模式，請使用 \_ [ALTER AVAILABILITY GROUP](../../t-sql/statements/alter-availability-group-transact-sql.md)語句的 [容錯移轉模式] 選項 [!INCLUDE[tsql](../../includes/tsql-md.md)] 。|  
|**會話 \_ 超時**|**int**|逾時期間 (以秒為單位)。 逾時期間是將主要複本與次要複本之間的連接視為失敗之前，複本等待接收另一個複本之訊息的時間上限。 工作階段逾時會偵測次要複本是否連接到主要複本。<br /><br /> 當偵測到與次要複本之間的連接失敗時，主要複本會將次要複本視為未 \_ 同步處理。 一旦偵測到與主要複本之間的連接失敗時，次要複本只會嘗試重新連接。<br /><br /> **注意：** 會話超時不會造成自動容錯移轉。<br /><br /> 若要變更這個值，請使用[ALTER AVAILABILITY GROUP](../../t-sql/statements/alter-availability-group-transact-sql.md)語句的 SESSION_TIMEOUT 選項 [!INCLUDE[tsql](../../includes/tsql-md.md)] 。|  
|**主要 \_ 角色 \_ 允許 \_ 連接**|**tinyint**|可用性允許所有連接還是只允許讀寫連接，下列其中一項：<br /><br /> 2 = 所有連接 (預設值)<br /><br /> 3 = 讀寫連接|  
|**主要 \_ 角色 \_ 允許 \_ 連接 \_ desc**|**nvarchar(60)**|**主要 \_ 角色 \_ 允許 \_ 連接** 的描述，下列其中一個：<br /><br /> ALL<br /><br /> 讀 \_ 寫|  
|**次要 \_ 角色 \_ 允許 \_ 連接**|**tinyint**|執行次要角色的可用性複本 (也就是次要複本) 是否可接受來自用戶端的連接，下列其中一個值：<br /><br /> 0 = 否。 不允許連接次要複本的資料庫，這些資料庫也不可用於讀取存取。 這是預設值。<br /><br /> 1 = 唯讀。 只允許與次要複本的資料庫進行唯讀連接。 可讀取複本中的所有資料庫。<br /><br /> 2 = 全部。 次要複本的資料庫允許所有連接進行唯讀存取。<br /><br /> 如需詳細資訊，請參閱[使用中次要：可讀取的次要複本 &#40;Always On 可用性群組&#41;](../../database-engine/availability-groups/windows/active-secondaries-readable-secondary-replicas-always-on-availability-groups.md)。|  
|**secondary_role_allow_connections_desc**|**nvarchar(60)**|**Secondary_role_allow_connections** 的描述，下列其中一個：<br /><br /> 否<br /><br /> READ_ONLY<br /><br /> ALL|  
|**create_date**|**datetime**|建立複本的日期。<br /><br /> NULL = 複本不在這個伺服器執行個體上。|  
|**modify_date**|**datetime**|上次修改複本的日期。<br /><br /> NULL = 複本不在這個伺服器執行個體上。|  
|**backup_priority**|**int**|表示使用者為了在這個複本上執行備份所指定的優先權 (相對於相同可用性群組中的其他複本)。 這個值是 0 到 100 範圍之間的整數。<br /><br /> 如需詳細資訊，請參閱[使用中次要：在次要複本上備份 &#40;Always On 可用性群組&#41;](../../database-engine/availability-groups/windows/active-secondaries-backup-on-secondary-replicas-always-on-availability-groups.md)。|  
|**read_only_routing_url**|**nvarchar(256)**|唯讀可用性複本的連接端點 (URL)。 如需詳細資訊，請參閱本主題稍後的 [設定可用性群組的唯讀路由 &#40;SQL Server&#41;](../../database-engine/availability-groups/windows/configure-read-only-routing-for-an-availability-group-sql-server.md))。|  
|**seeding_mode**|**tinyint**|值為下列其中之一： </br></br> 0：自動 </br></br> 1：手動|
|**seeding_mode_desc**|**nvarchar(60)**|說明植入模式。 </br></br> AUTOMATIC </br></br>MANUAL|
  
## <a name="security"></a>安全性  
  
### <a name="permissions"></a>權限  
 需要伺服器執行個體的 VIEW ANY DEFINITION 權限。  
  
## <a name="see-also"></a>另請參閱  
 [sys.availability_groups &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-availability-groups-transact-sql.md)   
 [AlwaysOn 可用性群組概觀 &#40;SQL Server&#41;](../../database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server.md)   
 [AlwaysOn 可用性群組 &#40;SQL Server&#41;](../../database-engine/availability-groups/windows/always-on-availability-groups-sql-server.md)   
 [監視可用性群組 &#40;Transact-sql&#41;](../../database-engine/availability-groups/windows/monitor-availability-groups-transact-sql.md)   
 [監視可用性群組 &#40;Transact-SQL&#41;](../../database-engine/availability-groups/windows/monitor-availability-groups-transact-sql.md)  
  
  
