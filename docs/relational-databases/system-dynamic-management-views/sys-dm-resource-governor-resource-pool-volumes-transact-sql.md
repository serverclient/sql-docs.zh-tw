---
description: 'sys.dm_resource_governor_resource_pool_volumes (Transact-sql) '
title: sys.dm_resource_governor_resource_pool_volumes (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 08/09/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.dm_resource_governor_resource_pool_volumes_TSQL
- dm_resource_governor_resource_pool_volumes_TSQL
- dm_resource_governor_resource_pool_volumes
- sys.dm_resource_governor_resource_pool_volumes
dev_langs:
- TSQL
helpviewer_keywords:
- dm_resource_governor_resource_pool_volumes
- sys.dm_resource_governor_resource_pool_volumes
ms.assetid: fa692e56-c561-4533-97c5-bc12c600553f
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 9c6707bfd8b1c29073239821cd0fa3c0425ef089
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98093976"
---
# <a name="sysdm_resource_governor_resource_pool_volumes-transact-sql"></a>sys.dm_resource_governor_resource_pool_volumes (Transact-sql) 
[!INCLUDE[sqlserver](../../includes/applies-to-version/sqlserver.md)]

  傳回目前的資源集區的每個磁碟區的 IO 統計資料的相關資訊。 [Sys.dm_resource_governor_resource_pools &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-transact-sql.md)的資源集區層級也會提供這項資訊。  
  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|pool_id|**int**|資源集區的識別碼。 不可為 Null。|  
|volume_name|**sysname**|磁碟 volue 的名稱。 不可為 Null。|  
|read_io_queued_total|**int**|重設資源管理員之後加入佇列的讀取 IO 總數。 不可為 Null。|  
|read_io_issued_total|**int**|重設資源管理員統計資料之後發出的讀取 IO 總數。 不可為 Null。|  
|read_ios_completed_total|**int**|重設資源管理員統計資料之後完成的讀取 IO 總數。 不可為 Null。|  
|read_ios_throttled_total|**int**|重設資源管理員統計資料之後節流的讀取 IO 總數。 不可為 Null。|  
|read_bytes_total|**bigint**|重設資源管理員統計資料之後讀取的總位元組數。 不可為 Null。|  
|read_io_stall_total_ms|**bigint**|讀取 IO 從抵達到完成的總時間 (以毫秒為單位)。 不可為 Null。|  
|read_io_stall_queued_ms|**bigint**|讀取 IO 從抵達到發行的總時間 (以毫秒為單位)。 這是 IO 資源管理導入的延遲。 不可為 Null。|  
|write_io_queued_total|**int**|重設資源管理員統計資料之後加入佇列的寫入 IO 總數。 不可為 Null。|  
|write_io_issued_total|**int**|重設資源管理員統計資料之後發出的寫入 IO 總數。 不可為 Null。|  
|write_io_completed_total|**int**|重設資源管理員統計資料之後完成的寫入 IO 總數。 不可為 Null|  
|write_io_throttled_total|**int**|重設資源管理員統計資料之後節流的寫入 IO 總數。 不可為 Null|  
|write_bytes_total|**bigint**|重設資源管理員統計資料之後寫入的總位元組數。 不可為 Null。|  
|write_io_stall_total_ms|**bigint**|寫入 IO 從發出到完成的總時間 (以毫秒為單位)。 不可為 Null。|  
|write_io_stall_queued_ms|**bigint**|寫入 IO 從抵達到發行的總時間 (以毫秒為單位)。 這是 IO 資源管理導入的延遲。 不可為 Null。|  
|io_issue_violations_total|**int**|IO 發出違規總數。 也就是說，在 IO 發出率低於保留率時的次數。 不可為 Null。|  
|io_issue_delay_total_ms|**bigint**|排程發出 IO 到實際發出 IO 的總時間 (以毫秒為單位)。 不可為 Null。|  
  
## <a name="permissions"></a>權限  
 需要 VIEW SERVER STATE 權限。  
  
## <a name="see-also"></a>另請參閱  
 [動態管理檢視與函數 &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)   
 [sys.dm_resource_governor_workload_groups &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-transact-sql.md)   
 [sys.resource_governor_resource_pools &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-resource-governor-resource-pools-transact-sql.md)   
 [ALTER RESOURCE GOVERNOR &#40;Transact-SQL&#41;](../../t-sql/statements/alter-resource-governor-transact-sql.md)  
  
  

