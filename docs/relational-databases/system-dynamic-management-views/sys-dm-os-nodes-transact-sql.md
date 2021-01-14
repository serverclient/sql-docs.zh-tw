---
description: sys.dm_os_nodes (Transact-SQL)
title: sys.dm_os_nodes (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 02/13/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.dm_os_nodes
- dm_os_nodes_TSQL
- dm_os_nodes
- sys.dm_os_nodes_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_os_nodes dynamic management view
ms.assetid: c768b67c-82a4-47f5-850b-0ea282358d50
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 4409ba73ba7231f674ac60f3ed63b06b6c454fa7
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98170640"
---
# <a name="sysdm_os_nodes-transact-sql"></a>sys.dm_os_nodes (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

名為 SQLOS 的內部元件會建立模擬硬體處理器位置的節點結構。 您可以使用 [軟體 NUMA](../../database-engine/configure-windows/soft-numa-sql-server.md) 來變更這些結構，以建立自訂節點配置。  

> [!NOTE]
> 從開始 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] ， [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 將會自動針對某些硬體設定使用軟體 NUMA。 如需詳細資訊，請參閱 [自動軟體 NUMA](../../database-engine/configure-windows/soft-numa-sql-server.md#automatic-soft-numa)。
  
下表提供有關這些節點的資訊。  
  
> [!NOTE]
> 若要從或呼叫此 DMV [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] ，請使用 **sys.dm_pdw_nodes_os_nodes** 名稱。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|node_id|**smallint**|節點的識別碼。|  
|node_state_desc|**nvarchar(256)**|節點狀態的描述。 系統會先顯示互斥的值，然後再顯示可結合的值。 例如：<br /> Online, Thread Resources Low, Lazy Preemptive<br /><br />有四個互斥的 node_state_desc 值。 其說明如下所示。<br /><ul><li>線上：節點在線上<li>離線：節點已離線<li>閒置：節點沒有暫止的工作要求，且已進入閒置狀態。<li>IDLE_READY： Node 沒有擱置中的工作要求，且已準備好進入閒置狀態。</li></ul><br />有三個可組合的 node_state_desc 值（如下所列）及其說明。<br /><ul><li>DAC：此節點會保留給 [專用管理連接](../../database-engine/configure-windows/diagnostic-connection-for-database-administrators.md)。<li>THREAD_RESOURCES_LOW：因為記憶體不足的狀況，所以無法在此節點上建立新的執行緒。<li>熱新增：指出已新增節點以回應熱新增 CPU 事件。</li></ul>|  
|memory_object_address|**varbinary(8)**|與這個節點相關聯之記憶體物件的位址。 與 [sys.dm_os_memory_objects](../../relational-databases/system-dynamic-management-views/sys-dm-os-memory-objects-transact-sql.md).memory_object_address 的一對一關聯性。|  
|memory_clerk_address|**varbinary(8)**|與這個節點相關聯之記憶體 Clerk 的位址。 與 [sys.dm_os_memory_clerks](../../relational-databases/system-dynamic-management-views/sys-dm-os-memory-clerks-transact-sql.md).memory_clerk_address 的一對一關聯性。|  
|io_completion_worker_address|**varbinary(8)**|指派給這個節點之 IO 完成的工作者位址。 與 [sys.dm_os_workers](../../relational-databases/system-dynamic-management-views/sys-dm-os-workers-transact-sql.md).worker_address 的一對一關聯性。|  
|memory_node_id|**smallint**|這個節點所屬之記憶體節點的識別碼。 與 [sys.dm_os_memory_nodes](../../relational-databases/system-dynamic-management-views/sys-dm-os-memory-nodes-transact-sql.md).memory_node_id 的多對一關聯性。|  
|cpu_affinity_mask|**bigint**|識別與這個節點相關之 CPU 的點陣圖。|  
|online_scheduler_count|**smallint**|由此節點管理的線上排程器數目。|  
|idle_scheduler_count|**smallint**|沒有使用中工作者之線上排程器的數目。|  
|active_worker_count|**int**|在這個節點所管理之所有排程器上使用中的工作者數目。|  
|avg_load_balance|**int**|這個節點上每個排程器的平均工作數目。|  
|timer_task_affinity_mask|**bigint**|識別可指派計時器工作給本身之排程器的點陣圖。|  
|permanent_task_affinity_mask|**bigint**|識別可指派永久工作給本身之排程器的點陣圖。|  
|resource_monitor_state|**bit**|每個節點都具有一個指派給本身的資源監視器。 資源監視器可能是執行中或閒置。 值 1 是表示執行中，而值 0 則表示閒置。|  
|online_scheduler_mask|**bigint**|識別這個節點的處理序相似性遮罩。|  
|processor_group|**smallint**|識別這個節點的處理器群組。|  
|cpu_count |**int** |此節點可用的 Cpu 數目。 |
|pdw_node_id|**int**|此散發所在之節點的識別碼。<br /><br /> **適用** 于： [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 、 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]|  
  
## <a name="permissions"></a>權限

在上 [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] ，需要 `VIEW SERVER STATE` 許可權。   
在 SQL Database Basic、S0 和 S1 服務目標上，以及針對彈性集區中的資料庫，則 `Server admin` `Azure Active Directory admin` 需要或帳戶。 在所有其他 SQL Database 服務目標上， `VIEW DATABASE STATE` 資料庫中都需要有許可權。   

## <a name="see-also"></a>另請參閱    
 [SQL Server 作業系統相關的動態管理檢視 &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sql-server-operating-system-related-dynamic-management-views-transact-sql.md)   
 [軟體 NUMA &#40;SQL Server&#41;](../../database-engine/configure-windows/soft-numa-sql-server.md)  
  
