---
description: sys.dm_hadr_name_id_map (Transact-SQL)
title: sys.dm_hadr_name_id_map (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.dm_hadr_name_id_map
- sys.dm_hadr_name_id_map_TSQL
- dm_hadr_name_id_map
- dm_hadr_name_id_map_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- Availability Groups [SQL Server], monitoring
- sys.dm_hadr_name_id_map dynamic management view
ms.assetid: e07bb8a9-37de-4a39-a257-950d7c3ae8fb
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 7cf7f8aa5f87db5cb572ce6b643b5c70c9d273ac
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98098904"
---
# <a name="sysdm_hadr_name_id_map-transact-sql"></a>sys.dm_hadr_name_id_map (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  顯示目前實例 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 已聯結至三個唯一識別碼的 Always On 可用性群組對應：可用性群組識別碼、wsfc 資源識別碼和 Wsfc 群組識別碼。 此對應的目的是要處理重新命名 WSFC 資源/群組的案例。  
   
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**ag_name**|**nvarchar(256)**|可用性群組的名稱。 這是使用者指定的名稱，它在 Windows Server 容錯移轉叢集 (WSFC) 叢集內必須是唯一的。|  
|**ag_id**|**uniqueidentifier**|可用性群組的唯一識別碼 (GUID)。|  
|**ag_resource_id**|**nvarchar(256)**|在 WSFC 叢集中做為資源之可用性群組的唯一識別碼。|  
|**ag_group_id**|**nvarchar(256)**|可用性群組的唯一 WSFC 群組識別碼。|  
  
## <a name="permissions"></a>權限  
 需要伺服器的 VIEW SERVER STATE 權限。  
  
## <a name="see-also"></a>另請參閱  
 [Always On 可用性群組動態管理檢視和函數 &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/always-on-availability-groups-dynamic-management-views-functions.md)   
 [AlwaysOn 可用性群組目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/always-on-availability-groups-catalog-views-transact-sql.md)   
 [監視可用性群組 &#40;Transact-sql&#41;](../../database-engine/availability-groups/windows/monitor-availability-groups-transact-sql.md)   
 [AlwaysOn 可用性群組 &#40;SQL Server&#41;](../../database-engine/availability-groups/windows/always-on-availability-groups-sql-server.md)  
  
  
