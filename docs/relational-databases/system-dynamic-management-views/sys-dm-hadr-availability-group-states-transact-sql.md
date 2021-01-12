---
description: sys.dm_hadr_availability_group_states (Transact-SQL)
title: sys.dm_hadr_availability_group_states (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.dm_hadr_availability_group_states
- sys.dm_hadr_availability_group_states_TSQL
- dm_hadr_availability_group_states_TSQL
- dm_hadr_availability_group_states
dev_langs:
- TSQL
helpviewer_keywords:
- Availability Groups [SQL Server], monitoring
- sys.dm_hadr_availability_group_states dynamic management view
ms.assetid: d18019dd-f8dc-4492-b035-b1a639369b65
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: c5cdbf3f295fc8781e25a6df3ae78d63c22a3b6a
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98092804"
---
# <a name="sysdm_hadr_availability_group_states-transact-sql"></a>sys.dm_hadr_availability_group_states (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  針對在本機實例上擁有可用性複本的每個 Always On 可用性群組，各傳回一個資料列 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。 每個資料列會顯示定義給定之可用性群組健全狀況的狀態。  
  
> [!NOTE]  
>  若要取得的完整清單，請查詢 [sys.availability_groups](../../relational-databases/system-catalog-views/sys-availability-groups-transact-sql.md) 目錄檢視。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**group_id**|**uniqueidentifier**|可用性群組的唯一識別碼。|  
|**primary_replica**|**varchar(128)**|裝載目前主要複本的伺服器執行個體名稱。<br /><br /> NULL = 不是主要複本，或是無法與 WSFC 容錯移轉叢集通訊。|  
|**primary_recovery_health**|**tinyint**|表示主要複本的復原健全狀況，可為下列其中一個值：<br /><br /> 0 = 進行中<br /><br /> 1 = 線上<br /><br /> NULL<br /><br /> 在次要複本上， **primary_recovery_health** 的資料行是 Null。|  
|**primary_recovery_health_desc**|**nvarchar(60)**|**Primary_replica_health** 的描述，下列其中一個：<br /><br /> ONLINE_IN_PROGRESS<br /><br /> ONLINE<br /><br /> NULL|  
|**secondary_recovery_health**|**tinyint**|指出次要複本複本的復原健全狀況，其中一個：<br /><br /> 0 = 進行中<br /><br /> 1 = 線上<br /><br /> NULL<br /><br /> 在主要複本上， **secondary_recovery_health** 的資料行是 Null。|  
|**secondary_recovery_health_desc**|**nvarchar(60)**|**Secondary_recovery_health** 的描述，下列其中一個：<br /><br /> ONLINE_IN_PROGRESS<br /><br /> ONLINE<br /><br /> NULL|  
|**synchronization_health**|**tinyint**|反映可用性群組中所有可用性複本的 **synchronization_health** 匯總。 以下是可能的值及其描述。<br /><br /> 0：狀況不良。 可用性複本沒有狀況良好的 **synchronization_health** (2 = 狀況良好的) 。<br /><br /> 1：部分狀況良好。 部分 (而不是所有) 可用性複本的同步處理狀況良好。<br /><br /> 2：狀況良好。 每一個可用性複本的同步處理狀況良好。<br /><br /> 如需複本同步處理健全狀況的詳細資訊，請參閱 [sys.dm_hadr_availability_replica_states &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-hadr-availability-replica-states-transact-sql.md)中的 **synchronization_health** 資料行。|  
|**synchronization_health_desc**|**nvarchar(60)**|**Synchronization_health** 的描述，下列其中一個：<br /><br /> NOT_HEALTHY<br /><br /> PARTIALLY_HEALTHY<br /><br /> HEALTHY|  
  
## <a name="security"></a>安全性  
  
### <a name="permissions"></a>權限  
 需要伺服器的 VIEW SERVER STATE 權限。  
  
## <a name="see-also"></a>另請參閱  
 [監視可用性群組 &#40;Transact-sql&#41;](../../database-engine/availability-groups/windows/monitor-availability-groups-transact-sql.md)   
 [AlwaysOn 可用性群組 &#40;SQL Server&#41;](../../database-engine/availability-groups/windows/always-on-availability-groups-sql-server.md)   
 [AlwaysOn 可用性群組動態管理檢視和函式 &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/always-on-availability-groups-dynamic-management-views-functions.md)  
  
  
