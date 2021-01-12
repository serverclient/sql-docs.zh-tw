---
description: sys.availability_groups_cluster (Transact-SQL)
title: sys.availability_groups_cluster (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.availability_groups_cluster
- availability_groups_cluster
- availability_groups_cluster_TSQL
- sys.availability_groups_cluster_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- Availability Groups [SQL Server], monitoring
- sys.availability_groups_cluster catalog view
- Availability Groups [SQL Server], WSFC clusters
ms.assetid: d0f4683f-cdf0-4227-8b68-720ffe58f158
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: e6318cc58ce51cafd380dd1f5eb8a71aa0e0b479
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98101813"
---
# <a name="sysavailability_groups_cluster-transact-sql"></a>sys.availability_groups_cluster (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  傳回 Windows Server 容錯移轉叢集 (WSFC) 中每個 Always On 可用性群組的資料列。 每個資料列都包含 WSFC 叢集中的可用性群組中繼資料。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**group_id**|**uniqueidentifier**|可用性群組的唯一識別碼 (GUID)。|  
|**name**|**sysname**|可用性群組的名稱。 這是使用者指定的名稱，它在 Windows Server 容錯移轉叢集 (WSFC) 內必須是唯一的。|  
|**resource_id**|**nvarchar(40)**|WSFC 叢集資源的資源識別碼。|  
|**resource_group_id**|**nvarchar(40)**|可用性群組之 WSFC 叢集資源群組的資源群組識別碼。|  
|**failure_condition_level**|**int**|觸發自動容錯移轉所必須根據的使用者定義失敗狀況層級，可為下列其中一個整數值：<br /><br /> 1：指定在發生下列任何情況時，應該起始自動容錯移轉： <br />- [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 服務已關閉。<br />-用於連接到 WSFC 容錯移轉叢集的可用性群組租用已過期，因為未從伺服器實例收到 ACK。 如需詳細資訊，請參閱 [How It Works:SQL Server Always On Lease Timeout](https://techcommunity.microsoft.com/t5/sql-server-support/how-it-works-sql-server-alwayson-lease-timeout/ba-p/317268) (運作方式：SQL Server Always On 租用逾時)。<br /><br /> 2：指定在發生下列任何情況時，應該起始自動容錯移轉：  <br />-實例不 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會連接到叢集，而且會超過可用性群組的使用者指定 **health_check_timeout** 臨界值。 <br />-可用性複本處於失敗狀態。 <br />3：指定應該在嚴重的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 內部錯誤（例如孤立的旋轉鎖、嚴重的寫入存取違規或太多傾印）上起始自動容錯移轉。 這是預設值。 <br />4：指定應該在中等 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 內部錯誤（例如 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 內部資源集區中持續的記憶體不足狀況）起始自動容錯移轉。<br />5：指定應該在任何合格的失敗狀況下起始自動容錯移轉，包括：<br />-SQL 引擎背景工作執行緒耗盡。 <br />-偵測無法解決鎖死。<br /><br /> 失敗狀況層級 (1-5) 的範圍從最低限制 (層級 1) 到最高限制 (層級 5)。 給定的狀況層級包含所有較少限制的層級。 因此，最嚴格的狀況層級 5 包含四個較少限制的狀況層級 (1-4)，層級 4 則包含層級 1-3，依此類推。<br /><br /> 若要變更這個值，請使用[ALTER AVAILABILITY GROUP](../../t-sql/statements/alter-availability-group-transact-sql.md)語句的 FAILURE_CONDITION_LEVEL 選項 [!INCLUDE[tsql](../../includes/tsql-md.md)] 。|  
|**health_check_timeout**|**int**|在假設伺服器實例變慢或沒有回應之前， [sp_server_diagnostics](../../relational-databases/system-stored-procedures/sp-server-diagnostics-transact-sql.md) 系統預存程式傳回伺服器健全狀況資訊的等候時間 (以毫秒為單位) 。 預設值為 30000 毫秒 (30 秒)。<br /><br /> 若要變更這個值，請使用[ALTER AVAILABILITY GROUP](../../t-sql/statements/alter-availability-group-transact-sql.md)語句的 HEALTH_CHECK_TIMEOUT 選項 [!INCLUDE[tsql](../../includes/tsql-md.md)] 。|  
|**automated_backup_preference**|**tinyint**|針對此可用性群組中的可用性資料庫執行備份的慣用位置。 下列其中一個值：<br /><br /> 0：主要。 備份一定要在主要複本上進行。<br />1：僅限次要。 偏好針對次要複本執行備份。<br />2：偏好次要。 偏好針對次要複本執行備份，但是如果沒有次要複本可用來執行備份作業，可以接受針對主要複本執行備份。 這是預設行為。<br />3：任何複本。 針對主要複本或次要複本執行備份沒有任何偏好。<br /><br /> 如需詳細資訊，請參閱[使用中次要：在次要複本上備份 &#40;Always On 可用性群組&#41;](../../database-engine/availability-groups/windows/active-secondaries-backup-on-secondary-replicas-always-on-availability-groups.md)。|  
|**automated_backup_preference_desc**|**nvarchar(60)**|**Automated_backup_preference** 的描述，下列其中一個：<br /><br /> PRIMARY<br /><br /> SECONDARY_ONLY<br /><br /> SECONDARY<br /><br /> NONE|  
  
## <a name="security"></a>安全性  
  
### <a name="permissions"></a>權限  
 需要伺服器執行個體的 VIEW ANY DEFINITION 權限。  
  
## <a name="see-also"></a>另請參閱  
 [sys.availability_replicas &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-availability-replicas-transact-sql.md)   
 [AlwaysOn 可用性群組 &#40;SQL Server&#41;](../../database-engine/availability-groups/windows/always-on-availability-groups-sql-server.md)   
 [監視可用性群組 &#40;Transact-sql&#41;](../../database-engine/availability-groups/windows/monitor-availability-groups-transact-sql.md)   
 [監視可用性群組 &#40;Transact-SQL&#41;](../../database-engine/availability-groups/windows/monitor-availability-groups-transact-sql.md)  
  
  
