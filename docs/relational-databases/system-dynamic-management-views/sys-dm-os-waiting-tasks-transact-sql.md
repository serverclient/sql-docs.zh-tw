---
description: sys.dm_os_waiting_tasks (Transact-SQL)
title: sys.dm_os_waiting_tasks (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/13/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- dm_os_waiting_tasks
- sys.dm_os_waiting_tasks_TSQL
- dm_os_waiting_tasks_TSQL
- sys.dm_os_waiting_tasks
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_os_waiting_tasks dynamic management view
ms.assetid: ca5e6844-368c-42e2-b187-6e5f5afc8df3
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: c02bb3523eda26afb5aac7e4e904cb1aec8dd62b
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98097610"
---
# <a name="sysdm_os_waiting_tasks-transact-sql"></a>sys.dm_os_waiting_tasks (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  傳回有關等候某項資源的工作等候佇列資訊。 如需工作的詳細資訊，請參閱 [執行緒和工作架構指南](../../relational-databases/thread-and-task-architecture-guide.md)。
   
> [!NOTE]  
> 若要從或呼叫這個 [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] ，請使用 **sys.dm_pdw_nodes_os_waiting_tasks** 名稱。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**waiting_task_address**|**varbinary(8)**|等候工作的位址。|  
|**session_id**|**smallint**|與這項工作相關聯的工作階段識別碼。|  
|**exec_context_id**|**int**|與這項工作相關聯的執行內容識別碼。|  
|**wait_duration_ms**|**bigint**|這個等候類型的總等候時間 (以毫秒為單位)。 這次包含 **signal_wait_time**。|  
|**wait_type**|**nvarchar(60)**|等候類型的名稱。|  
|**resource_address**|**varbinary(8)**|工作在等候的資源位址。|  
|**blocking_task_address**|**varbinary(8)**|目前保留這項資源的工作。|  
|**blocking_session_id**|**smallint**|封鎖要求之工作階段的識別碼。 如果這個資料行是 NULL，表示要求沒有被封鎖，或者封鎖工作階段的工作階段資訊無法使用 (或無法識別)。<br /><br /> -2 = 封鎖資源是由被遺棄的分散式交易所擁有。<br /><br /> -3 = 封鎖資源是由延遲的復原交易所擁有。<br /><br /> -4 = 由於內部閂鎖狀態轉換，而無法判斷封鎖閂鎖擁有者的工作階段識別碼。|  
|**blocking_exec_coNtext_id**|**int**|封鎖工作的執行內容識別碼。|  
|**resource_description**|**Nvarchar (3072)**|正在耗用的資源描述。 如需詳細資訊，請參閱以下清單。|  
|**pdw_node_id**|**int**|**適用** 于： [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 、 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]<br /><br /> 此散發所在之節點的識別碼。|  
  
## <a name="resource_description-column"></a>resource_description 資料行  
 resource_description 資料行具有以下的可能值。  
  
 **執行緒集區資源擁有者：**  
  
-   threadpool id = 排程器\<hex-address>  
  
 **平行查詢資源擁有者：**  
  
-   exchangeEvent id = {Port |管道} \<hex-address> WaitType = \<exchange-wait-type> 的 =\<exchange-node-id>  
  
 **Exchange-wait-type：**  
  
-   e_waitNone  
  
-   e_waitPipeNewRow  
  
-   e_waitPipeGetRow  
  
-   e_waitSynchronizeConsumerOpen  
  
-   e_waitPortOpen  
  
-   e_waitPortClose  
  
-   e_waitRange  
  
 **鎖定資源擁有者：**  
  
-   \<type-specific-description> id = 鎖定 \<lock-hex-address> 模式 = \<mode> associatedObjectId =\<associated-obj-id>  
  
     **\<type-specific-description> 可以是：**  
  
    -   針對資料庫： k subresource = \<databaselock-subresource> dbid =\<db-id>  
  
    -   針對 FILE： k fileid = \<file-id> subresource = \<filelock-subresource> dbid =\<db-id>  
  
    -   針對 OBJECT： objectlock lockPartition = \<lock-partition-id> objid = \<obj-id> subresource = \<objectlock-subresource> dbid =\<db-id>  
  
    -   針對頁面： pagelock fileid = \<file-id> pageid = \<page-id> dbid = \<db-id> subresource =\<pagelock-subresource>  
  
    -   針對金鑰：機碼 hobtid = \<hobt-id> dbid =\<db-id>  
  
    -   針對範圍： extentlock fileid = \<file-id> pageid = \<page-id> dbid =\<db-id>  
  
    -   若為 RID： ridlock fileid = \<file-id> pageid = \<page-id> dbid =\<db-id>  
  
    -   針對應用程式： applicationlock hash = \<hash> databasePrincipalId = \<role-id> dbid =\<db-id>  
  
    -   中繼資料： metadatalock subresource = \<metadata-subresource> classid = \<metadatalock-description> dbid =\<db-id>  
  
    -   若為 HOBT： hobtlock hobtid = \<hobt-id> subresource = \<hobt-subresource> dbid =\<db-id>  
  
    -   針對 ALLOCATION_UNIT： allocunitlock hobtid = \<hobt-id> subresource = \<alloc-unit-subresource> dbid =\<db-id>  
  
     **\<mode> 可以是：**  
  
     Sch-S、Sch-M、S、U、X、IS、IU、IX、SIU、SIX、UIX、BU、RangeS-S、RangeS-U、RangeI-N、RangeI-S、RangeI-U、RangeI-X、RangeX-、RangeX-U、RangeX-X  
  
 **外部資源擁有者：**  
  
-   External ExternalResource =\<wait-type>  
  
 **一般資源擁有者：**  
  
-   TransactionMutex TransactionInfo 工作區 =\<workspace-id>  
  
-   Mutex  
  
-   CLRTaskJoin  
  
-   CLRMonitorEvent  
  
-   CLRRWLockEvent  
  
-   resourceWait  
  
 **閂鎖資源擁有者：**  
  
-   \<db-id>:\<file-id>:\<page-in-file>  
  
-   \<GUID>  
  
-   \<latch-class> (\<latch-address>)  
  
## <a name="permissions"></a>權限

在上 [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] ，需要 `VIEW SERVER STATE` 許可權。   
在 SQL Database Basic、S0 和 S1 服務目標上，以及針對彈性集區中的資料庫，則 `Server admin` `Azure Active Directory admin` 需要或帳戶。 在所有其他 SQL Database 服務目標上， `VIEW DATABASE STATE` 資料庫中都需要有許可權。   
 
## <a name="example"></a>範例
### <a name="a-identify-tasks-from-blocked-sessions"></a>A. 識別封鎖會話中的工作。 

```sql
SELECT * FROM sys.dm_os_waiting_tasks 
WHERE blocking_session_id IS NOT NULL; 
```   

### <a name="b-view-waiting-tasks-per-connection"></a>B. 查看每個連接的等候工作

```sql
SELECT st.text AS [SQL Text], c.connection_id, w.session_id, 
  w.wait_duration_ms, w.wait_type, w.resource_address, 
  w.blocking_session_id, w.resource_description, c.client_net_address, c.connect_time
FROM sys.dm_os_waiting_tasks AS w
INNER JOIN sys.dm_exec_connections AS c ON w.session_id = c.session_id 
CROSS APPLY (SELECT * FROM sys.dm_exec_sql_text(c.most_recent_sql_handle)) AS st 
              WHERE w.session_id > 50 AND w.wait_duration_ms > 0
ORDER BY c.connection_id, w.session_id
GO
```

### <a name="c-view-waiting-tasks-for-all-user-processes-with-additional-information"></a>C. 以其他資訊查看所有使用者進程的等候工作

```sql
SELECT 'Waiting_tasks' AS [Information], owt.session_id,
    owt.wait_duration_ms, owt.wait_type, owt.blocking_session_id,
    owt.resource_description, es.program_name, est.text,
    est.dbid, eqp.query_plan, er.database_id, es.cpu_time,
    es.memory_usage*8 AS memory_usage_KB
FROM sys.dm_os_waiting_tasks owt
INNER JOIN sys.dm_exec_sessions es ON owt.session_id = es.session_id
INNER JOIN sys.dm_exec_requests er ON es.session_id = er.session_id
OUTER APPLY sys.dm_exec_sql_text (er.sql_handle) est
OUTER APPLY sys.dm_exec_query_plan (er.plan_handle) eqp
WHERE es.is_user_process = 1
ORDER BY owt.session_id;
GO
```
  
## <a name="see-also"></a>另請參閱  
[SQL Server 作業系統相關的動態管理檢視 &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sql-server-operating-system-related-dynamic-management-views-transact-sql.md)      
[執行緒和工作架構指南](../../relational-databases/thread-and-task-architecture-guide.md)     
   
 
