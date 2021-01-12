---
description: sys.dm_os_memory_pools (Transact-SQL)
title: sys.dm_os_memory_pools (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/13/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.dm_os_memory_pools_TSQL
- dm_os_memory_pools
- dm_os_memory_pools_TSQL
- sys.dm_os_memory_pools
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_os_memory_pools dynamic management view
ms.assetid: 1ef053f3-c6f3-456e-82b6-26e4bd630d46
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 493359d62aaff4b653094c34b37557f3e5e314f8
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98101545"
---
# <a name="sysdm_os_memory_pools-transact-sql"></a>sys.dm_os_memory_pools (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  針對 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體中的每一個物件存放區，各傳回一個資料列。 您可以利用這份檢視，來監視快取記憶體的使用情形，並且識別不當的快取行為。  
  
> [!NOTE]  
>  若要從或呼叫這個 [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] ，請使用 **sys.dm_pdw_nodes_os_memory_pools** 名稱。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**memory_pool_address**|**varbinary(8)**|代表記憶體集區之項目的記憶體位址。 不可為 Null。|  
|**pool_id**|**int**|一組集區中某個特定集區的識別碼。 不可為 Null。|  
|**type**|**nvarchar(60)**|物件集區的類型。 不可為 Null。 如需詳細資訊，請參閱 [sys.dm_os_memory_clerks &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-os-memory-clerks-transact-sql.md)。|  
|**name**|**nvarchar(256)**|這個記憶體物件之系統指派的名稱。 不可為 Null。|  
|**max_free_entries_count**|**bigint**|集區最多所能容納的可用項目數。 不可為 Null。|  
|**free_entries_count**|**bigint**|目前在集區中的可用項目數。 不可為 Null。|  
|**removed_in_all_rounds_count**|**bigint**|自  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的執行個體啟動之後，從集區移除的項目數。 不可為 Null。|  
|**pdw_node_id**|**int**|**適用** 于： [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 、 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]<br /><br /> 此散發所在之節點的識別碼。|  
  
## <a name="permissions"></a>權限

在上 [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] ，需要 `VIEW SERVER STATE` 許可權。   
在 SQL Database Basic、S0 和 S1 服務目標上，以及針對彈性集區中的資料庫，則 `Server admin` `Azure Active Directory admin` 需要或帳戶。 在所有其他 SQL Database 服務目標上， `VIEW DATABASE STATE` 資料庫中都需要有許可權。   

## <a name="remarks"></a>備註  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 元件有時會使用一個共用集區架構，來快取異質、非靜態類型的資料。 集區架構比快取架構更簡單。 集區中所有的項目都視為相同。 集區在內部相當於記憶體 Clerk，可以取代記憶體 Clerk 使用。  
  
## <a name="see-also"></a>另請參閱  
 
  [SQL Server 作業系統相關的動態管理檢視 &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sql-server-operating-system-related-dynamic-management-views-transact-sql.md)  
  
  


