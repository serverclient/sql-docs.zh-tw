---
description: sys.dm_os_process_memory (Transact-SQL)
title: sys.dm_os_process_memory (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.dm_os_process_memory_TSQL
- dm_os_process_memory_TSQL
- dm_os_process_memory
- sys.dm_os_process_memory
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_os_process_memory dynamic management view
ms.assetid: e838130c-95d4-4605-9e3b-eb0ab71cd250
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 53471004b1e3349ab49e15486479d55961f15fc1
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98098893"
---
# <a name="sysdm_os_process_memory-transact-sql"></a>sys.dm_os_process_memory (Transact-SQL)
[!INCLUDE [sql-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdbmi-asa-pdw.md)]

  大部分用於 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 處理序空間的記憶體配置都是透過允許追蹤和說明這些配置的介面進行控制。 不過，記憶體配置可能會在略過內部記憶體管理常式的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 位址空間中進行。 其值是透過呼叫基底作業系統取得。 它們不會由內部的方法操作 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ，除非它會針對鎖定或大型頁面配置進行調整。  
  
 指出記憶體大小的所有傳回值都會以 KB 為單位顯示。 資料行 **total_virtual_address_space_reserved_kb** 是 **sys.dm_os_sys_info** 的 **virtual_memory_in_bytes** 重複。  
  
 下表提供處理位址空間的完整內容。  
  
> [!NOTE]  
>  若要從或呼叫這個 [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] ，請使用 **sys.dm_pdw_nodes_os_process_memory** 名稱。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**physical_memory_in_use_kb**|**bigint**|指出處理工作集 (以 KB 為單位)，如作業系統所回報，以及使用大型分頁 API 所完成的追蹤配置。 不可為 Null。|  
|**large_page_allocations_kb**|**bigint**|指定使用大型分頁 API 所配置的實體記憶體。 不可為 Null。|  
|**locked_page_allocations_kb**|**bigint**|指定記憶體中鎖定的記憶體頁面。 不可為 Null。|  
|**total_virtual_address_space_kb**|**bigint**|指出虛擬位址空間之使用者模式部分的大小總計。 不可為 Null。|  
|**virtual_address_space_reserved_kb**|**bigint**|指出處理序所保留的虛擬位址空間的總數。 不可為 Null。|  
|**virtual_address_space_committed_kb**|**bigint**|指出已經認可或對應至實體頁面的已保留虛擬位址空間數量。 不可為 Null。|  
|**virtual_address_space_available_kb**|**bigint**|指出目前可用的虛擬位址空間數量。 不可為 Null。<br /><br /> **注意：** 可以有小於配置細微性的可用區域。 這些區域無法用於配置。|  
|**page_fault_count**|**bigint**|指出由 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 處理序所造成的分頁錯誤數目。 不可為 Null。|  
|**memory_utilization_percentage**|**int**|指定位於工作集之認可記憶體的百分比。 不可為 Null。|  
|**available_commit_limit_kb**|**bigint**|指出可供處理序認可的記憶體數量。 不可為 Null。|  
|**process_physical_memory_low**|**bit**|指出處理序正在回應實體記憶體不足的通知。 不可為 Null。|  
|**process_virtual_memory_low**|**bit**|指出偵測到虛擬記憶體不足的情況。 不可為 Null。|  
|**pdw_node_id**|**int**|**適用** 于： [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 、 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]<br /><br /> 此散發所在之節點的識別碼。|  
  
## <a name="permissions"></a>權限  
 在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 需要伺服器上的 VIEW SERVER STATE 權限。  
  
在上 [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] ，需要 `VIEW SERVER STATE` 許可權。   
在 SQL Database Basic、S0 和 S1 服務目標上，以及針對彈性集區中的資料庫，則 `Server admin` `Azure Active Directory admin` 需要或帳戶。 在所有其他 SQL Database 服務目標上， `VIEW DATABASE STATE` 資料庫中都需要有許可權。   
  
## <a name="see-also"></a>另請參閱  
 [動態管理檢視與函數 &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)   
 [SQL Server 作業系統相關的動態管理檢視 &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sql-server-operating-system-related-dynamic-management-views-transact-sql.md)  
  
  


