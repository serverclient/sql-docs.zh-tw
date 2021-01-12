---
description: sys.dm_os_workers (Transact-SQL)
title: sys.dm_os_workers (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/13/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- dm_os_workers_TSQL
- sys.dm_os_workers_TSQL
- dm_os_workers
- sys.dm_os_workers
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_os_workers dynamic management view
ms.assetid: 4d5d1e52-a574-4bdd-87ae-b932527235e8
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: d93b2be18167232798b3d8de6ce1ca67f9e8c9d6
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98095116"
---
# <a name="sysdm_os_workers-transact-sql"></a>sys.dm_os_workers (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  針對系統中每一個工作者，各傳回一個資料列。 如需背景工作的詳細資訊，請參閱 [執行緒和工作架構指南](../../relational-databases/thread-and-task-architecture-guide.md)。 
  
> [!NOTE]  
>  若要從或呼叫這個 [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] ，請使用 **sys.dm_pdw_nodes_os_workers** 名稱。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|worker_address|**varbinary(8)**|工作者的記憶體位址。|  
|status|**int**|僅供內部使用。|  
|is_preemptive|**bit**|1 = 工作者以先佔式排程執行。 執行外部程式碼的任何工作者都是在先佔式排程下執行。|  
|is_fiber|**bit**|1 = 工作者是以輕量型共用來執行。 如需詳細資訊，請參閱本主題稍後的 [sp_configure &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-configure-transact-sql.md)的使用者閱讀。|  
|is_sick|**bit**|1 = 工作者在嘗試取得微調鎖定時卡住。 如果有設定這個位元，這可能指出經常存取的物件有競爭問題。|  
|is_in_cc_exception|**bit**|1 = 工作者目前正在處理非 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 例外狀況。|  
|is_fatal_exception|**bit**|指定這個工作者是否接收嚴重例外狀況。|  
|is_inside_catch|**bit**|1 = 工作者目前處理例外狀況。|  
|is_in_polling_io_completion_routine|**bit**|1 = 工作者目前針對暫止 I/O 執行 I/O 完成常式。 如需詳細資訊，請參閱 [sys.dm_io_pending_io_requests &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-io-pending-io-requests-transact-sql.md)。|  
|context_switch_count|**int**|這個工作者執行的排程器內容切換數目。|  
|pending_io_count|**int**|此工作者執行的實體 I/O 數目。|  
|pending_io_byte_count|**bigint**|這個工作者之所有暫止實體 I/O 的總位元組數。|  
|pending_io_byte_average|**int**|這個工作者之實體 I/O 的平均位元組數。|  
|wait_started_ms_ticks|**bigint**|當此背景工作進入暫停狀態時， [ms_ticks](../../relational-databases/system-dynamic-management-views/sys-dm-os-sys-info-transact-sql.md)中的時間點。 從 [sys.dm_os_sys_info](../../relational-databases/system-dynamic-management-views/sys-dm-os-sys-info-transact-sql.md) 減去 ms_ticks 的值，會傳回背景工作角色等候的毫秒數。|  
|wait_resumed_ms_ticks|**bigint**|當此背景工作進入可執行狀態時， [ms_ticks](../../relational-databases/system-dynamic-management-views/sys-dm-os-sys-info-transact-sql.md)中的時間點。 從 [sys.dm_os_sys_info](../../relational-databases/system-dynamic-management-views/sys-dm-os-sys-info-transact-sql.md) 減去 ms_ticks 的值，會傳回背景工作在可執行佇列中的毫秒數。|  
|task_bound_ms_ticks|**bigint**|當工作系結至此背景工作時， [ms_ticks](../../relational-databases/system-dynamic-management-views/sys-dm-os-sys-info-transact-sql.md)中的時間點。|  
|worker_created_ms_ticks|**bigint**|建立背景工作角色時， [ms_ticks](../../relational-databases/system-dynamic-management-views/sys-dm-os-sys-info-transact-sql.md)中的時間點。|  
|exception_num|**int**|此工作者遇到之最後例外狀況的錯誤號碼。|  
|exception_severity|**int**|這個工作者遇到之最後例外狀況的嚴重性。|  
|exception_address|**varbinary(8)**|擲出例外狀況的程式碼位址|  
|affinity|**bigint**|工作者的執行緒相似性。 符合 [sys.dm_os_threads &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-os-threads-transact-sql.md)中線程的親和性。|  
|state|**nvarchar(60)**|工作者狀態。 可以是下列值之一：<br /><br /> INIT = 目前正在初始化的工作者。<br /><br /> RUNNING = 工作者目前以非先佔式或先佔式執行。<br /><br /> RUNNABLE = 工作者準備在排程器執行。<br /><br /> SUSPENDED = 工作者目前暫停，等待事件傳送信號給它。|  
|start_quantum|**bigint**|此工作者目前執行的開始時間 (以毫秒為單位)。|  
|end_quantum|**bigint**|此工作者目前執行的結束時間 (以毫秒為單位)。|  
|last_wait_type|**nvarchar(60)**|最後等待的類型。 如需等候類型的清單，請參閱 [sys.dm_os_wait_stats &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql.md)。|  
|return_code|**int**|從最後等待傳回值。 可以是下列值之一：<br /><br /> 0 =SUCCESS<br /><br /> 3 = DEADLOCK<br /><br /> 4 = PREMATURE_WAKEUP<br /><br /> 258 = TIMEOUT|  
|quantum_used|**bigint**|僅供內部使用。|  
|max_quantum|**bigint**|僅供內部使用。|  
|boost_count|**int**|僅供內部使用。|  
|tasks_processed_count|**int**|這個工作者所處理的工作數。|  
|fiber_address|**varbinary(8)**|這個工作者相關聯之 Fiber 的記憶體位址。<br /><br /> NULL = [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 未設定為輕量型共用。|  
|task_address|**varbinary(8)**|目前工作的記憶體位址。 如需詳細資訊，請參閱 [sys.dm_os_tasks &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-os-tasks-transact-sql.md)。|  
|memory_object_address|**varbinary(8)**|工作者記憶體物件的記憶體位址。 如需詳細資訊，請參閱 [sys.dm_os_memory_objects &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-os-memory-objects-transact-sql.md)。|  
|thread_address|**varbinary(8)**|與這個工作者相關聯之執行緒的記憶體位址。 如需詳細資訊，請參閱 [sys.dm_os_threads &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-os-threads-transact-sql.md)。|  
|signal_worker_address|**varbinary(8)**|最後立即通知這個物件之工作者的記憶體位址。 如需詳細資訊，請參閱 [sys.dm_os_workers](../../relational-databases/system-dynamic-management-views/sys-dm-os-workers-transact-sql.md)。|  
|scheduler_address|**varbinary(8)**|排程器的記憶體位址。 如需詳細資訊，請參閱 [sys.dm_os_schedulers &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-os-schedulers-transact-sql.md)。|  
|processor_group|**smallint**|儲存指派給這個執行緒的處理器群組識別碼。|  
|pdw_node_id|**int**|**適用** 于： [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 、 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]<br /><br /> 此散發所在之節點的識別碼。|  
  
## <a name="remarks"></a>備註  
 如果工作者狀態是 RUNNING，且工作者是以非先佔式執行，則工作者位址會符合 sys.dm_os_schedulers 中的 active_worker_address。  
  
 當通知等待事件的工作者時，會將工作者放在可執行佇列的開頭。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 可讓它在資料列出現一千次，之後就會將工作者放在佇列結尾。 將工作者移到佇列結尾，會有一些效能隱含作用。  
  
## <a name="permissions"></a>權限
在上 [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] ，需要 `VIEW SERVER STATE` 許可權。   
在進階層中 [!INCLUDE[ssSDS_md](../../includes/sssds-md.md)] ，需要 `VIEW DATABASE STATE` 資料庫中的許可權。 在 [!INCLUDE[ssSDS_md](../../includes/sssds-md.md)] 標準和基本層中，需要  `Server Admin` 角色成員資格或 `Azure Active Directory admin` 帳戶。   

## <a name="examples"></a>範例  
 您可以使用下列查詢來查明工作者在 SUSPENDED 或 RUNNABLE 狀態下執行的時間長度。  
  
```sql
SELECT   
    t1.session_id,  
    CONVERT(varchar(10), t1.status) AS status,  
    CONVERT(varchar(15), t1.command) AS command,  
    CONVERT(varchar(10), t2.state) AS worker_state,  
    w_suspended =   
      CASE t2.wait_started_ms_ticks  
        WHEN 0 THEN 0  
        ELSE   
          t3.ms_ticks - t2.wait_started_ms_ticks  
      END,  
    w_runnable =   
      CASE t2.wait_resumed_ms_ticks  
        WHEN 0 THEN 0  
        ELSE   
          t3.ms_ticks - t2.wait_resumed_ms_ticks  
      END  
  FROM sys.dm_exec_requests AS t1  
  INNER JOIN sys.dm_os_workers AS t2  
    ON t2.task_address = t1.task_address  
  CROSS JOIN sys.dm_os_sys_info AS t3  
  WHERE t1.scheduler_id IS NOT NULL;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  

```
 session_id status     command         worker_state w_suspended w_runnable  
 ---------- ---------- --------------- ------------ ----------- --------------------  
 4          background LAZY WRITER     SUSPENDED    688         688  
 6          background LOCK MONITOR    SUSPENDED    4657        4657
 19         background BRKR TASK       SUSPENDED    603820344   603820344  
 14         background BRKR EVENT HNDL SUSPENDED    63583641    63583641  
 51         running    SELECT          RUNNING      0           0  
 2          background RESOURCE MONITO RUNNING      0           603825954  
 3          background LAZY WRITER     SUSPENDED    422         422  
 7          background SIGNAL HANDLER  SUSPENDED    603820485   603820485  
 13         background TASK MANAGER    SUSPENDED    603824704   603824704  
 18         background BRKR TASK       SUSPENDED    603820407   603820407  
 9          background TRACE QUEUE TAS SUSPENDED    454         454  
 52         suspended  SELECT          SUSPENDED    35094       35094  
 1          background RESOURCE MONITO RUNNING      0           603825954  
```

 在輸出中，當 `w_runnable` 和 `w_suspended` 相等時，這代表工作者處於 SUSPENDED 狀態的時間。 否則，`w_runnable` 便代表工作者在 RUNNABLE 狀態中所花的時間。 在此輸出中，工作階段 `52` 處於 `SUSPENDED` 狀態的時間為 `35,094` 毫秒。  
  
## <a name="see-also"></a>另請參閱  
[SQL Server 作業系統相關的動態管理檢視 &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sql-server-operating-system-related-dynamic-management-views-transact-sql.md)       
[查詢處理架構指南](../../relational-databases/query-processing-architecture-guide.md#DOP)       
[執行緒和工作架構指南](../../relational-databases/thread-and-task-architecture-guide.md)    
