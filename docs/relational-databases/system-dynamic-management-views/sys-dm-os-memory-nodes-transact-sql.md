---
description: sys.dm_os_memory_nodes (Transact-SQL)
title: sys.dm_os_memory_nodes (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/13/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- dm_os_memory_nodes_TSQL
- sys.dm_os_memory_nodes
- sys.dm_os_memory_nodes_TSQL
- dm_os_memory_nodes
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_os_memory_nodes dynamic management view
ms.assetid: bf4032fe-7db1-40e9-a62e-d69cebff4b44
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 02d875cc2ee069c43ca04c16f374c5cb8afe2b31
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98097601"
---
# <a name="sysdm_os_memory_nodes-transact-sql"></a>sys.dm_os_memory_nodes (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 內部的配置會使用 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 記憶體管理員。 追蹤 **sys.dm_os_process_memory** 和內部計數器的進程記憶體計數器之間的差異，可能表示記憶體空間中的外部元件記憶體使用 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。  
  
 每個實體 NUMA 記憶體節點都會建立一些節點。 這些可能與 **sys.dm_os_nodes** 中的 CPU 節點不同。  
  
 系統不會追蹤直接透過 Windows 記憶體配置常式完成的配置。 下表將提供僅使用 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 記憶體管理員介面所完成之記憶體配置的相關資訊。  
  
> [!NOTE]  
>  若要從或呼叫這個 [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] ，請使用 **sys.dm_pdw_nodes_os_memory_nodes** 名稱。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**memory_node_id**|**smallint**|指定記憶體節點的識別碼。 與 **sys.dm_os_memory_clerks** 的 **memory_node_id** 相關。 不可為 Null。|  
|**virtual_address_space_reserved_kb**|**bigint**|指出未經認可也沒有對應至實體頁面的虛擬位址保留數目 (以 KB 為單位)。 不可為 Null。|  
|**virtual_address_space_committed_kb**|**bigint**|指定已經認可或對應至實體頁面的虛擬位址數量 (以 KB 為單位)。 不可為 Null。|  
|**locked_page_allocations_kb**|**bigint**|指定已經由 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 鎖定的實體記憶體數量 (以 KB 為單位)。 不可為 Null。|  
|**single_pages_kb**|**bigint**|**適用於**： [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 至 [!INCLUDE[ssKilimanjaro](../../includes/sskilimanjaro-md.md)]。<br /><br /> 由這個節點上執行之執行緒使用單一頁面配置器所配置的認可記憶體數量 (以 KB 為單位)。 這個記憶體是從緩衝集區配置。 這個值會指出發生配置要求的節點，而非滿足配置要求的實體位置。|  
|**pages_kb**|**bigint**|**適用對象**：[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 及更新版本。<br /><br /> 指定由 Memory Manager 頁面配置器從這個 NUMA 節點所配置的認可記憶體數量 (以 KB 為單位)。 不可為 Null。|  
|**multi_pages_kb**|**bigint**|**適用於**： [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 至 [!INCLUDE[ssKilimanjaro](../../includes/sskilimanjaro-md.md)]。<br /><br /> 由這個節點上執行之執行緒使用多頁配置器所配置的認可記憶體數量 (以 KB 為單位)。 這個記憶體是在緩衝集區外部配置。 這個值會指出發生配置要求的節點，而非滿足配置要求的實體位置。|  
|**shared_memory_reserved_kb**|**bigint**|指定已經從這個節點保留的共用記憶體數量 (以 KB 為單位)。 不可為 Null。|  
|**shared_memory_committed_kb**|**bigint**|指定已經在這個節點上認可的共用記憶體數量 (以 KB 為單位)。 不可為 Null。|  
|**cpu_affinity_mask**|**bigint**|**適用對象**：[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 及更新版本。<br /><br /> 僅供內部使用。 不可為 Null。|  
|**online_scheduler_mask**|**bigint**|**適用對象**：[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 及更新版本。<br /><br /> 僅供內部使用。 不可為 Null。|  
|**processor_group**|**smallint**|**適用對象**：[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 及更新版本。<br /><br /> 僅供內部使用。 不可為 Null。|  
|**foreign_committed_kb**|**bigint**|**適用對象**：[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 及更新版本。<br /><br /> 指定來自其他記憶體節點的認可記憶體數量 (以 KB 為單位)。 不可為 Null。|  
|**target_kb** |**bigint** |**適用於**：[!INCLUDE[ssSQL15_md](../../includes/sssql15-md.md)] 及更新版本、[!INCLUDE[ssSDS_md](../../includes/sssds-md.md)]。<br /><br /> 指定記憶體節點的記憶體目標（以 KB 為單位）。 |   
|**pdw_node_id**|**int**|**適用** 于： [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 、 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]<br /><br /> 此散發所在之節點的識別碼。|  
  
## <a name="permissions"></a>權限

在上 [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] ，需要 `VIEW SERVER STATE` 許可權。   
在 SQL Database Basic、S0 和 S1 服務目標上，以及針對彈性集區中的資料庫，則 `Server admin` `Azure Active Directory admin` 需要或帳戶。 在所有其他 SQL Database 服務目標上， `VIEW DATABASE STATE` 資料庫中都需要有許可權。   

## <a name="see-also"></a>另請參閱  
  [SQL Server 作業系統相關的動態管理檢視 &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sql-server-operating-system-related-dynamic-management-views-transact-sql.md)  
  
  


