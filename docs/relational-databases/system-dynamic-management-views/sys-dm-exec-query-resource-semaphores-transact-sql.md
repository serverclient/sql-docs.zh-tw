---
description: sys.dm_exec_query_resource_semaphores (Transact-SQL)
title: sys.dm_exec_query_resource_semaphores (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 08/09/2016
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.dm_exec_query_resource_semaphores_TSQL
- dm_exec_query_resource_semaphores_TSQL
- sys.dm_exec_query_resource_semaphores
- dm_exec_query_resource_semaphores
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_exec_query_resource_semaphores dynamic management view
ms.assetid: e43a2aa9-dd52-4c89-911e-1a7d05f7ffbb
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 0a9ec322e5f19cab5e8506e5e0dbc05a3e55e900
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98096553"
---
# <a name="sysdm_exec_query_resource_semaphores-transact-sql"></a>sys.dm_exec_query_resource_semaphores (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  傳回 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中目前查詢資源信號狀態的相關資訊。 **sys.dm_exec_query_resource_semaphores** 提供一般查詢執行的記憶體狀態，並可讓您判斷系統是否可以存取足夠的記憶體。 此視圖可補充從 [sys.dm_os_memory_clerks](../../relational-databases/system-dynamic-management-views/sys-dm-os-memory-clerks-transact-sql.md) 取得的記憶體資訊，以提供完整的伺服器記憶體狀態。 **sys.dm_exec_query_resource_semaphores** 會針對一般資源信號傳回一個資料列，並為小型查詢資源信號傳回另一個資料列。 小型查詢信號有兩個需求：  
  
-   要求的記憶體授與應該小於 5 MB  
  
-   查詢成本應小於3個成本單位  
  
> [!NOTE]  
>  若要從或呼叫這個 [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] ，請使用 **sys.dm_pdw_nodes_exec_query_resource_semaphores** 名稱。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**resource_semaphore_id**|**smallint**|資源信號的非唯一識別碼。 一般資源信號為 0，而小型查詢資源信號則為 1。|  
|**target_memory_kb**|**bigint**|授與使用目標 (以 KB 為單位)。|  
|**max_target_memory_kb**|**bigint**|最大的可能目標 (以 KB 為單位)。 小型查詢資源信號為 NULL。|  
|**total_memory_kb**|**bigint**|資源信號擁有的記憶體 (以 KB 為單位)。 如果系統有記憶體不足的壓力，或是經常授與強制的最小記憶體，這個值可能會大於 **target_memory_kb** 或 **max_target_memory_kb** 值。 總記憶體是可用記憶體和授與記憶體的總和。|  
|**available_memory_kb**|**bigint**|新授與的可用記憶體 (以 KB 為單位)。|  
|**granted_memory_kb**|**bigint**|授與的總記憶體 (以 KB 為單位)。|  
|**used_memory_kb**|**bigint**|授與記憶體的實際使用部分 (以 KB 為單位)。|  
|**grantee_count**|**int**|已滿足其授與的使用中查詢數目。|  
|**waiter_count**|**int**|等候要滿足授與的查詢數目。|  
|**timeout_error_count**|**bigint**|伺服器啟動後逾時錯誤的總數。 小型查詢資源信號為 NULL。|  
|**forced_grant_count**|**bigint**|伺服器啟動後強制最小記憶體授與的總數。 小型查詢資源信號為 NULL。|  
|**pool_id**|**int**|這個資源信號所屬資源集區的識別碼。|  
|**pdw_node_id**|**int**|**適用** 于： [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 、 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]<br /><br /> 此散發所在之節點的識別碼。|  
  
## <a name="permissions"></a>權限  

在上 [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] ，需要 `VIEW SERVER STATE` 許可權。   
在 SQL Database Basic、S0 和 S1 服務目標上，以及針對彈性集區中的資料庫，則 `Server admin` `Azure Active Directory admin` 需要或帳戶。 在所有其他 SQL Database 服務目標上， `VIEW DATABASE STATE` 資料庫中都需要有許可權。   
  
## <a name="remarks"></a>備註  
 使用包含 ORDER BY 或彙總之動態管理檢視的查詢，有可能會增加記憶體耗用量，但也因此可協助它們正在進行疑難排解的問題。  
  
 使用 **sys.dm_exec_query_resource_semaphores** 進行疑難排解，但是不要將它包含在將使用未來版本的應用程式中 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。  
  
 資源管理員功能可讓資料庫管理員在資源集區間散發伺服器資源，最多可達 64 個集區。 在 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 和更新版本中，每個集區的行為都像是一個小型的獨立伺服器執行個體，而且需要 2 個信號。  
  
## <a name="see-also"></a>另請參閱  
 [執行相關的動態管理檢視和函數 &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/execution-related-dynamic-management-views-and-functions-transact-sql.md)   
 [sys.dm_exec_query_memory_grants &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-query-memory-grants-transact-sql.md)  
  
  


