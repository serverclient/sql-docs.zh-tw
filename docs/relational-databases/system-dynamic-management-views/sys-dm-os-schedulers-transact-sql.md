---
description: sys.dm_os_schedulers (Transact-SQL)
title: sys.dm_os_schedulers (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/13/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- dm_os_schedulers
- sys.dm_os_schedulers_TSQL
- sys.dm_os_schedulers
- dm_os_schedulers_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_os_schedulers dynamic management view
ms.assetid: 3a09d81b-55d5-416f-9cda-1a3a5492abe0
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 6a2d8ed3f8eb3aa5a89ee7176863165a2a0a0b74
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98171720"
---
# <a name="sysdm_os_schedulers-transact-sql"></a>sys.dm_os_schedulers (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  針對 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中的每個排程器，各傳回一個資料列，其中每個排程器都會對應至個別的處理器。 請利用這份檢視來監視排程器的狀況或識別失控的工作。 如需排程器的詳細資訊，請參閱 [執行緒和工作架構指南](../../relational-databases/thread-and-task-architecture-guide.md)。  
  
> [!NOTE]  
>  若要從或呼叫這個 [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] ，請使用 **sys.dm_pdw_nodes_os_schedulers** 名稱。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|scheduler_address|**varbinary(8)**|排程器的記憶體位址。 不可為 Null。|  
|parent_node_id|**int**|排程器所屬節點 (也稱為父節點) 的識別碼。 這代表非統一記憶體存取 (NUMA) 節點。 不可為 Null。|  
|scheduler_id|**int**|排程器的識別碼。 所有用來執行一般查詢的排程器，其識別碼都小於 1048576。 識別碼大於或等於 1048576 的排程器是 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 內部使用的排程器，例如專用管理員連接排程器。 不可為 Null。|  
|cpu_id|**smallint**|指派給排程器的 CPU 識別碼。<br /><br /> 不可為 Null。<br /><br /> **注意：** 255 不表示與中的親和性相同 [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] 。 如需其他親和性資訊，請參閱 [sys.dm_os_threads &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-os-threads-transact-sql.md) 。|  
|status|**nvarchar(60)**|指出排程器的狀態。 可以是下列值之一：<br /><br /> -線上隱藏<br />-離線隱藏<br />-線上顯示<br />-離線顯示<br />-可見的線上 (DAC) <br />-HOT_ADDED<br /><br /> 不可為 Null。<br /><br /> HIDDEN 排程器用來處理 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 內部的要求。 VISIBLE 排程器用來處理使用者要求。<br /><br /> OFFLINE 排程器對應到相似性遮罩中離線的處理器，因此不會用來處理任何要求。 ONLINE 排程器對應到相似性遮罩中上線的處理器，可以用來處理執行緒。<br /><br /> DAC 指出排程器正在專用管理員連接下執行。<br /><br /> HOT ADDED 表示已加入排程器來回應 Hot Add CPU 事件。|  
|is_online|**bit**|如果 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 設定成只使用伺服器上部分可用的處理器，這個組態可能表示部分排程器對應到不在相似性遮罩中的處理器。 如果是這種情況，這個資料行會傳回 0。 這個值表示排程器目前未用於處理查詢或批次。<br /><br /> 不可為 Null。|  
|is_idle|**bit**|1 = 排程器閒置。 目前沒有任何工作者正在執行。 不可為 Null。|  
|preemptive_switches_count|**int**|排程器的工作者切換到先佔式模式的次數。<br /><br /> 若要執行 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 外部的程式碼 (例如，擴充預存程序和分散式查詢)，執行緒必須在非先佔式排程器的控制之外執行。 若要這麼做，工作者必須切換到先佔式模式。|  
|context_switches_count|**int**|這個排程器上發生的內容切換次數。 不可為 Null。<br /><br /> 若要讓其他的工作者能夠執行，目前正在執行的工作者必須讓出排程器的控制權或切換內容。<br /><br /> **注意：** 如果背景工作產生排程器，並將其放入可執行檔佇列中，然後找不到其他任何背景工作角色，則背景工作會自行選取。 在這個情況下，不會更新 context_switches_count，但會更新 yield_count。|  
|idle_switches_count|**int**|排程器於閒置時等候事件的次數。 這個資料行與 context_switches_count 相似。 不可為 Null。|  
|current_tasks_count|**int**|與這個排程器關聯的目前工作數目。 這個計數包含下列項目：<br /><br /> -等候工作者執行工作的工作。<br />-目前正在等候或正在執行的工作 (處於暫停或可執行狀態) 。<br /><br /> 工作完成時，這個計數會遞減。 不可為 Null。|  
|runnable_tasks_count|**int**|已擁有指派工作且正在等候排程到可執行佇列上的工作者數目。 不可為 Null。|  
|current_workers_count|**int**|與這個排程器相關聯的工作者數目。 這個計數包括未獲指派任何工作的工作者。 不可為 Null。|  
|active_workers_count|**int**|使用中工作者的數目。 使用中的工作者絕對不是先佔式、必須擁有相關聯的工作，而且正在執行、可執行或已暫停。 不可為 Null。|  
|work_queue_count|**bigint**|暫止佇列中的工作數目。 這些工作正在等候工作者收取。 不可為 Null。|  
|pending_disk_io_count|**int**|等候完成的暫止 I/O 數目。 每個排程器都有暫止 I/O 的清單，每當內容切換時，會檢查這些暫止 I/O，判斷這些暫止 I/O 是否已完成。 插入要求時，這個計數會遞增。 完成要求時，這個計數會遞減。 這個數目不會指出 I/O 的狀態。 不可為 Null。|  
|load_factor|**int**|指出這個排程器上所見負載的內部值。 這個值是用來決定新工作應置於這個排程器還是另一個排程器。 當排程器負載不平衡時，這個值對偵錯相當有用。 路由決策是依據排程器的負載而定。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 也會利用節點和排程器的負載因數來協助判斷取得資源的最佳位置。 工作加入佇列時，負載因數會遞增。 工作完成時，負載因數會遞減。 使用負載因數可協助 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] OS 工作負載平衡得更好。 不可為 Null。|  
|yield_count|**int**|用來表示這個排程器上進度的內部值。 排程器監視器使用這個值來判斷排程器上的工作者是否未準時讓給其他的工作者。 這個值不表示工作者或工作已轉換到新工作者。 不可為 Null。|  
|last_timer_activity|**bigint**|在 CPU 刻度中，排程器上次檢查排程器計時器佇列的時間。 不可為 Null。|  
|failed_to_create_worker|**bit**|如果新工作者無法建立在這個排程器上，則設成 1。 發生原因通常是記憶體條件約束。 可為 Null。|  
|active_worker_address|**varbinary(8)**|目前使用中工作者的記憶體位址。 可為 Null。 如需詳細資訊，請參閱 [sys.dm_os_workers &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-os-workers-transact-sql.md)。|  
|memory_object_address|**varbinary(8)**|排程器記憶體物件的記憶體位址。 不是 NULLABLE。|  
|task_memory_object_address|**varbinary(8)**|工作記憶體物件的記憶體位址。 不可為 Null。 如需詳細資訊，請參閱 [sys.dm_os_memory_objects &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-os-memory-objects-transact-sql.md)。|  
|quantum_length_us|**bigint**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)] 公開 SQLOS 所使用的排程器配量。|  
| total_cpu_usage_ms |**bigint**|**適用於**：[!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 和更新版本 <br><br> 由非先占式工作者回報的此排程器所耗用的總 CPU。 不可為 Null。|
|total_cpu_idle_capped_ms|**bigint**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)] 指出根據 [服務等級目標](/azure/sql-data-warehouse/what-is-a-data-warehouse-unit-dwu-cdwu#service-level-objective)的節流，針對非 Azure 版本，一律會是 0 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。 可為 Null。|
|total_scheduler_delay_ms|**bigint**|**適用於**：[!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 和更新版本 <br><br> 一個背景工作切換的時間與另一個背景工作之間切換的時間。 可能是因為先占式工作者延遲下次非先占式工作者的排程，或由於作業系統排程執行緒來自其他進程所造成。 不可為 Null。|
|ideal_workers_limit|**int**|**適用於**：[!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 和更新版本 <br><br> 最好在排程器上有多少背景工作。 如果目前的背景工作負載因為不平衡工作負載而超過限制，則在它們變成閒置狀態時，就會被修剪。 不可為 Null。|
|pdw_node_id|**int**|**適用** 于： [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 、 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]<br /><br /> 此散發所在之節點的識別碼。|  
  
## <a name="permissions"></a>權限
在上 [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] ，需要 `VIEW SERVER STATE` 許可權。   
在 SQL Database Basic、S0 和 S1 服務目標上，以及針對彈性集區中的資料庫，則 `Server admin` `Azure Active Directory admin` 需要或帳戶。 在所有其他 SQL Database 服務目標上， `VIEW DATABASE STATE` 資料庫中都需要有許可權。   

## <a name="examples"></a>範例  
  
### <a name="a-monitoring-hidden-and-nonhidden-schedulers"></a>A. 監視隱藏及非隱藏的排程器  
 下列查詢會輸出所有排程器之 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中工作者和工作的狀態。 這項查詢可以在具有下列元件的電腦系統上執行：  
  
-   兩個處理器 (CPU)  
  
-   兩個 (NUMA) 節點  
  
-   每個 NUMA 節點各一個 CPU  
  
-   設定為 `0x03` 的相似性遮罩。  
  
```sql  
SELECT  
    scheduler_id,  
    cpu_id,  
    parent_node_id,  
    current_tasks_count,  
    runnable_tasks_count,  
    current_workers_count,  
    active_workers_count,  
    work_queue_count  
  FROM sys.dm_os_schedulers;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
scheduler_id cpu_id parent_node_id current_tasks_count  
------------ ------ -------------- -------------------  
0            1      0              9                    
257          255    0              1                    
1            0      1              10                   
258          255    1              1                    
255          255    32             2                    
  
runnable_tasks_count current_workers_count  
-------------------- ---------------------  
0                    11                     
0                    1                      
0                    18                     
0                    1                      
0                    3                      
  
active_workers_count work_queue_count  
-------------------- --------------------  
6                    0  
1                    0  
8                    0  
1                    0  
1                    0  
```  
  
 輸出中提供下列資訊：  
  
-   有五個排程器。 有兩個排程器的識別碼值 < 1048576。 識別碼為 >= 1048576 的排程器稱為隱藏的排程器。 排程器 `255` 代表專用管理員連接 (DAC)。 每個執行個體只有一個 DAC 排程器。 協調記憶體壓力的資源監視器會使用排程器 `257` 和排程器 `258`，每個 NUMA 節點各一個。  
  
-   輸出中有 23 項使用中的工作。 除了 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 已經啟動的資源管理工作以外，這些工作還包括使用者要求。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 工作的範例為 RESOURCE MONITOR (每個 NUMA 節點一個)、LAZY WRITER (每個 NUMA 節點一個)、LOCK MONITOR、CHECKPOINT 和 LOG WRITER。  
  
-   NUMA 節點 `0` 對應到 CPU `1`，而 NUMA 節點 `1` 對應到 CPU `0`。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 通常會在節點 0 以外的 NUMA 節點上啟動。  
  
-   當 `runnable_tasks_count` 傳回 `0` 時，不會有任何動態執行的工作。 不過，可能會有使用中的工作階段。  
  
-   代表 DAC 的排程器 `255` 有 `3` 個與其相關聯的工作者。 這些工作者是在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 啟動時配置，而且不會變更。 這些工作者只能用來處理 DAC 查詢。 此排程器上的兩項工作代表連接管理員和閒置工作者。  
  
-   `active_workers_count` 代表具有相關聯工作且在非先占式模式下執行的所有背景工作。 某些工作 (例如網路接聽程式) 則是在先佔式排程下執行。  
  
-   隱藏排程器不會處理一般使用者要求。 DAC 排程器例外。 這個 DAC 排程器有一個執行緒可以處理要求。  
  
### <a name="b-monitoring-nonhidden-schedulers-in-a-busy-system"></a>B. 監視忙碌系統中的非隱藏排程器  
 下列查詢顯示負載沈重之非隱藏排程器的狀態，其中包含的要求數目大於可用工作者所能處理的上限。 在這個範例中，有 256 個工作者已被指派了工作。 某些工作正在等候對工作者的指派。 可執行計數偏低表示有多個工作正在等候資源。  
  
> [!NOTE]  
>  您可以藉由查詢 sys.dm_os_workers 找出工作者的狀態。 如需詳細資訊，請參閱 [sys.dm_os_workers &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-os-workers-transact-sql.md)。  
  
 查詢內容如下：  
  
```sql  
SELECT  
    scheduler_id,  
    cpu_id,  
    current_tasks_count,  
    runnable_tasks_count,  
    current_workers_count,  
    active_workers_count,  
    work_queue_count  
  FROM sys.dm_os_schedulers  
  WHERE scheduler_id < 255;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
scheduler_id current_tasks_count runnable_tasks_count  
------------ ------------------- --------------------  
0            144                 0                     
1            147                 1                     
  
current_workers_count active_workers_count work_queue_count  
--------------------- -------------------- --------------------  
128                   125                  16  
128                   126                  19  
```  
  
 相較之下，下列結果顯示的多個可執行工作中，沒有任何一項工作正在等候取得工作者。 兩種排程器的 `work_queue_count` 都是 `0`。  
  
```  
scheduler_id current_tasks_count runnable_tasks_count  
------------ ------------------- --------------------  
0            107                 98                    
1            110                 100                   
  
current_workers_count active_workers_count work_queue_count  
--------------------- -------------------- --------------------  
128                   104                  0  
128                   108                  0  
```  
  
## <a name="see-also"></a>另請參閱  
 [SQL Server 作業系統相關的動態管理檢視 &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sql-server-operating-system-related-dynamic-management-views-transact-sql.md)  
