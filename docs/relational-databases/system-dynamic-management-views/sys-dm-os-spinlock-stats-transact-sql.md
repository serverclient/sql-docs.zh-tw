---
description: 'sys.dm_os_spinlock_stats (Transact-sql) '
title: sys.dm_os_spinlock_stats (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/03/2019
ms.prod: sql-non-specified
ms.prod_service: database-engine
ms.service: ''
ms.suite: sql
ms.technology: database-engine
ms.tgt_pltfrm: ''
ms.topic: language-reference
f1_keywords:
- sys.dm_os_spinlock_stats_TSQL
- dm_os_spinlock_stats_TSQL
- dm_os_spinlock_stats
- sys.dm_os_spinlock_stats
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_os_spinlock_stats dynamic management view
author: bluefooted
ms.author: pamela
ms.reviewer: wiassaf
manager: amitban
ms.openlocfilehash: 396c52a6d687c554b277e6f26c290978f035ae01
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98099761"
---
# <a name="sysdm_os_spinlock_stats-transact-sql"></a>sys.dm_os_spinlock_stats (Transact-sql) 

[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

傳回依類型組織之所有 spinlock 等候的相關資訊。  
  

|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|NAME|**nvarchar(256)**|Spinlock 類型的名稱。|  
|碰撞|**bigint**|執行緒嘗試取得 spinlock 並遭到封鎖的次數，因為另一個執行緒目前保留 spinlock。|  
|旋轉|**bigint**|執行緒在嘗試取得 spinlock 時執行迴圈的次數。|  
|spins_per_collision|**real**|每次衝突的周旋比。|  
|sleep_time|**bigint**|執行緒在輪詢時花費在睡眠狀態的時間量（以毫秒為單位）。|  
|輪詢之外機率|**int**|「旋轉」執行緒無法取得 spinlock 並產生排程器的次數。|  


## <a name="permissions"></a>權限  
在上 [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] ，需要 `VIEW SERVER STATE` 許可權。   
在 SQL Database Basic、S0 和 S1 服務目標上，以及針對彈性集區中的資料庫，則 `Server admin` `Azure Active Directory admin` 需要或帳戶。 在所有其他 SQL Database 服務目標上， `VIEW DATABASE STATE` 資料庫中都需要有許可權。    
  
## <a name="remarks"></a>備註  
 
 sys.dm_os_spinlock_stats 可以用來識別 spinlock 爭用的來源。 在某些情況下，您可以解決或減少 spinlock 爭用。 不過有些時候您可能還是必須連絡 [!INCLUDE[msCoName](../../includes/msconame-md.md)] 客戶支援服務部門。  
  
 您可以使用來重設 sys.dm_os_spinlock_stats 的內容，如下所示 `DBCC SQLPERF` ：  
  
```  
DBCC SQLPERF ('sys.dm_os_spinlock_stats', CLEAR);  
GO  
```  
  
 此舉會將所有的計數器重設為 0。  
  
> [!NOTE]  
>  如果重新啟動 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]，將無法保存這些統計資料。 所有的資料都是從上次重設統計資料之後，或是從 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 啟動之後開始累加計算。  
  
## <a name="spinlocks"></a>旋轉鎖  
 Spinlock 是輕量的同步處理物件，用來序列化通常會保留一小段時間的資料結構存取。 當執行緒嘗試存取由另一個執行緒所持有的 spinlock 所保護的資源時，執行緒會執行迴圈或「旋轉」，然後再次嘗試存取資源，而不是立即以閂鎖或其他資源等候來產生排程器。 執行緒將會繼續旋轉，直到資源可供使用或迴圈完成為止，此時執行緒將會產生排程器並返回可執行檔佇列。 這種作法有助於減少過度的執行緒內容切換，但當 spinlock 的爭用很高時，可能會觀察到顯著的 CPU 使用率。
   
 下表包含一些最常見的 spinlock 類型的簡短描述。  
  
|Spinlock 類型|描述|  
|-----------------|-----------------|  
|ABR|僅供內部使用。|
|ADB_CACHE|僅供內部使用。|
|ALLOC_CACHES_HASH|僅供內部使用。|
|APPENDONLY_STORAGE|僅供內部使用。|
|APRC_BACK_OFF_STATS|僅供內部使用。|
|APRC_EVENT_LIST|僅供內部使用。|
|APRC_QUEUE_LIST|僅供內部使用。|
|APRC_VALIDATION_QUEUE_LIST|僅供內部使用。|
|ASYNC_OP_ADMIN_CLIENT_REGISTRATION_LIST|僅供內部使用。|
|ASYNC_OP_ADMIN_WORK_REGISTRATION_HASH_TABLE|僅供內部使用。|
|ASYNCSTATSLIST|僅供內部使用。|
|備份|僅供內部使用。|
|BACKUP_COPY_CONTEXT|僅供內部使用。|
|BACKUP_CTX|僅供內部使用。|
|BASE_XACT_HASH|僅供內部使用。|
|BLOCKER_ENUM|僅供內部使用。|
|BPREPARTITION|僅供內部使用。|
|BPWORKFILE|僅供內部使用。|
|BUF_HASH|僅供內部使用。|
|BUF_LINK|僅供內部使用。|
|BUF_WRITE_LOG|僅供內部使用。|
|CACHEOBJ_DBG|僅供內部使用。|
|CHANNELFORCECLOSEMANAGER|僅供內部使用。|
|CHECK_AGGREGATE_STATE|僅供內部使用。|
|CLR_HOSTTASK|僅供內部使用。|
|CLR_SPIN_LOCK|僅供內部使用。|
|CMED_DATABASE|僅供內部使用。|
|CMED_HASH_SET|僅供內部使用。|
|COLUMNDATASETSESSIONLIST|僅供內部使用。|
|COLUMNSTORE_HASHTABLE|僅供內部使用。|
|COLUMNSTOREBUILDSTATE_LIST|僅供內部使用。|
|COM_INIT|僅供內部使用。|
|提交|僅供內部使用。|
|COMPPLAN_SKELETON|僅供內部使用。|
|CONNECTION_MANAGER|僅供內部使用。|
|連接|僅供內部使用。|
|CSIBUILDMEM|僅供內部使用。|
|CURSOR|僅供內部使用。|
|CURSQL|僅供內部使用。|
|DATAPORTCONSUMER|僅供內部使用。|
|DATAPORTSOURCEINFOCREDIT|僅供內部使用。|
|DATAPORTSOURCEINFOQUEUE|僅供內部使用。|
|DATASET_FREELIST|僅供內部使用。|
|DBCC_CHECK|僅供內部使用。|
|DBSEEDING_OPERATION|僅供內部使用。|
|DBT_HASH|僅供內部使用。|
|DBT_IO_LIST|僅供內部使用。|
|DBTABLE|控制 SQL Server 中包含該資料庫屬性之每個資料庫的記憶體內部資料結構存取。 如需詳細資訊，請參閱[本文](https://techcommunity.microsoft.com/t5/SQL-Server/Improving-Concurrency-Scalability-of-SQL-Server-workload-by/ba-p/384789)。 |
|DEFERRED_WF_EXT_DROP|僅供內部使用。|
|DEK_INSTANCE|僅供內部使用。|
|DELAYED_PARTITIONED_STACK|僅供內部使用。|
|DELETEBITMAP|僅供內部使用。|
|DIAG_MANAGER|僅供內部使用。|
|DIAG_OBJECT|僅供內部使用。|
|DIGEST_CACHE|僅供內部使用。|
|DINPBUF|僅供內部使用。|
|DIRECTLOGCONSUMER|僅供內部使用。|
|DP_LIST|控制已開啟間接檢查點的資料庫之中途分頁清單的存取權。 如需詳細資訊，請參閱[本文](https://techcommunity.microsoft.com/t5/SQL-Server/Indirect-Checkpoint-and-tempdb-8211-the-good-the-bad-and-the-non/ba-p/385510)。|
|DROP|僅供內部使用。|
|DROP_TEMPO|僅供內部使用。|
|DROPPED_ALLOC_UNIT|僅供內部使用。|
|DTC_HASHTABLE|僅供內部使用。|
|DTT_LIST|僅供內部使用。|
|ENDD_LIST|僅供內部使用。|
|EXT_CACHE|僅供內部使用。|
|EXTENT_ACTI加值稅ION|僅供內部使用。|
|FABRIC_DB_MGR_PTR|僅供內部使用。|
|FABRIC_LOG_MANAGEMENT_INPUT_VALUE|僅供內部使用。|
|FABRIC_REPLICA_TRANSPORT|僅供內部使用。|
|FABRIC_TVF_DATA_CONSUMER_LIST|僅供內部使用。|
|FABRIC_TVF_LOAD_LIB|僅供內部使用。|
|FCB_REPLICA_SYNC|僅供內部使用。|
|FGCB_PRP_FILL|僅供內部使用。|
|FILE_HANDLE_CACHE|僅供內部使用。|
|FILE_TABLE|僅供內部使用。|
|FILESTREAM_CHUNKER|僅供內部使用。|
|FREE_SPACE_CACHE_ENTRY|僅供內部使用。|
|FS_CONTAINER_LIST_WITH_DELETE|僅供內部使用。|
|FS_DELETED_FOLDER_CLEANUP|僅供內部使用。|
|FSAGENT|僅供內部使用。|
|FSGHOST_STATUS|僅供內部使用。|
|FT_INIT|僅供內部使用。|
|GHOST_FREE|僅供內部使用。|
|GHOST_HASH|僅供內部使用。|
|GLOBAL_SCHEDULER_LIST|僅供內部使用。|
|GLOBAL_TRACE_FLAGS|僅供內部使用。|
|GLOBALTRANS|僅供內部使用。|
|GROUP_COMMIT_FEEDBACK_LOOP|僅供內部使用。|
|GUARDIAN|僅供內部使用。|
|HADR_AGH_X_ACCESS|僅供內部使用。|
|HADR_AR_CONTROLLER_COLLECTION|僅供內部使用。|
|HADR_AR_DB_MGR|僅供內部使用。|
|HADR_AR_TRANSPORT|僅供內部使用。|
|HADR_COMPRESSION_MGR_POOL|僅供內部使用。|
|HADR_FABRIC_FACTORY|僅供內部使用。|
|HADR_PRIORITY_QUEUE|僅供內部使用。|
|HADR_TRANSPORT_CONTROL|僅供內部使用。|
|HADR_TRANSPORT_LIST|僅供內部使用。|
|HADRSEEDINGLIST|僅供內部使用。|
|HOBT_DROPPED|僅供內部使用。|
|HOBT_HASH|僅供內部使用。|
|HTTP|僅供內部使用。|
|HTTP_CONNCACHE|僅供內部使用。|
|HTTP_ENDPOINT|僅供內部使用。|
|IDENTITY|僅供內部使用。|
|INDEX_CREATE|僅供內部使用。|
|IO_DISPENSER_PAUSE|僅供內部使用。|
|IO_RG_VOLUME_HASHTABLE|僅供內部使用。|
|IOREQ|僅供內部使用。|
|ISSRESOURCE|僅供內部使用。|
|KTM_ENLISTMENT|僅供內部使用。|
|LANG_RES_LOAD|僅供內部使用。|
|LIVE_TARGET_TVF|僅供內部使用。|
|LOCK_FREE_LIST|僅供內部使用。|
|LOCK_HASH|保護對鎖定管理員雜湊表的存取，該資料表會儲存保存在資料庫中之鎖定的相關資訊。 如需詳細資訊，請參閱[本文](https://support.microsoft.com/kb/2926217)。|
|LOCK_NOTIFICATION|僅供內部使用。|
|LOCK_RESOURCE_ID|僅供內部使用。|
|LOCK_RW_ABTX_HASH_SET|僅供內部使用。|
|LOCK_RW_AGDB_HEALTH_DIAG|僅供內部使用。|
|LOCK_RW_CMED_HASH_SET|僅供內部使用。|
|LOCK_RW_DPT_TABLE|僅供內部使用。|
|LOCK_RW_IN_ROW_TRACKER|僅供內部使用。|
|LOCK_RW_LOGIN_RATE_STATS|僅供內部使用。|
|LOCK_RW_PVS_PAGE_TRACKER|僅供內部使用。|
|LOCK_RW_RBIO_REQ|僅供內部使用。|
|LOCK_RW_SECURITY_CACHE|僅供內部使用。|
|LOCK_RW_TEST|僅供內部使用。|
|LOCK_RW_WPR_BUCKET|僅供內部使用。|
|LOCK_SORT_STREAM|僅供內部使用。|
|LOCK_SQLSATELLITE_MESSAGE|僅供內部使用。|
|LOG_CONSOLIDATION|僅供內部使用。|
|LOG_RG_GOVERNOR|僅供內部使用。|
|LOGCACHE_ACCESS|僅供內部使用。|
|LOGFLUSHQ|僅供內部使用。|
|LOGIOSEQ|僅供內部使用。|
|LOGIOSEQMAPPENDINGMESSAGEQUEUE|僅供內部使用。|
|LOGLC|僅供內部使用。|
|LOGLFM|僅供內部使用。|
|LOGON_TRIGGER_CACHE|僅供內部使用。|
|LOGPOOL_HASHBUCKET|僅供內部使用。|
|LOGPOOL_REFCOUNTEDOBJECT|僅供內部使用。|
|LOGPOOL_SHAREDCACHEBUFFER|僅供內部使用。|
|LOGPOOL_SIZEPERRESOURCEPOOL|僅供內部使用。|
|LPE_BATCH|僅供內部使用。|
|LPE_SESSION|僅供內部使用。|
|LPE_SXTP|僅供內部使用。|
|Lsid|僅供內部使用。|
|LSLIST|僅供內部使用。|
|LSNREFLIST|僅供內部使用。|
|LSS_SYNC_DTC|僅供內部使用。|
|MD_CHANGE_NOTIFICATION|僅供內部使用。|
|MDB_REMOTE_BATCH_STATS_HASH_TABLE|僅供內部使用。|
|MDB_REMOTE_SESSION_HASH_TABLE|僅供內部使用。|
|MEM_MGR|僅供內部使用。|
|MGR_CACHE|僅供內部使用。|
|MIGRATION_BUF_LIST|僅供內部使用。|
|NETCONN_ADDRESS|僅供內部使用。|
|ONDEMAND_TASK|僅供內部使用。|
|ONE_PROC_SIM_NODE_CONTEXT|僅供內部使用。|
|ONE_PROC_SIM_NODE_CONTEXT_LIST|僅供內部使用。|
|ONE_PROC_SIM_REPLICA_CONTEXT|僅供內部使用。|
|ONE_PROC_SIM_SERVICE_PARTITION|僅供內部使用。|
|OPT_IDX_MISS_ID|僅供內部使用。|
|OPT_IDX_MISS_KEY|僅供內部使用。|
|OPT_IDX_STATS|僅供內部使用。|
|OPT_INFO_MGR|僅供內部使用。|
|PAGE_WORKITEMLIST|僅供內部使用。|
|PAGECOPIER|僅供內部使用。|
|PARALLELREDOCACHE|僅供內部使用。|
|PARTITIONED_HEAP_FREE_LIST|僅供內部使用。|
|PROGRESS_REPORT|僅供內部使用。|
|QE_SHUTDOWN|僅供內部使用。|
|QSCAN_CACHE|僅供內部使用。|
|QUERY_EXEC_STATS|僅供內部使用。|
|QUERY_STORE_ASYNC_PERSIST|僅供內部使用。|
|QUERY_STORE_ASYNC_QUEUE_TLIST|僅供內部使用。|
|QUERY_STORE_CAPTURE_POLICY_INTERVAL|僅供內部使用。|
|QUERY_STORE_CAPTURE_POLICY_STATS|僅供內部使用。|
|QUERY_STORE_CAPTURE_POLICY_THRESHOLD|僅供內部使用。|
|QUERY_STORE_CURRENT_INTERVAL|僅供內部使用。|
|QUERY_STORE_HT_CACHE|僅供內部使用。|
|QUERY_STORE_LIST|僅供內部使用。|
|QUERY_STORE_PLAN_COMP_AGG|僅供內部使用。|
|QUERY_STORE_PLAN_LIST|僅供內部使用。|
|QUERY_STORE_READ_ONLY_FLAGS|僅供內部使用。|
|QUERY_STORE_SELF_AGG|僅供內部使用。|
|QUERY_STORE_STMT_COMP_AGG|僅供內部使用。|
|QUERYEXEC|僅供內部使用。|
|QUERYSCAN|僅供內部使用。|
|RANGE_GENERATION|僅供內部使用。|
|READ_AHEAD|僅供內部使用。|
|REDOMGRSTATE|僅供內部使用。|
|REMOTE_SESSION_CACHE|僅供內部使用。|
|REMOTEBLOCKIO|僅供內部使用。|
|REMOTEOP|僅供內部使用。|
|REPL_LOGREADER_HISTORY_CACHE|僅供內部使用。|
|REPL_LOGREADER_PERDB_HISTORY_CACHE|僅供內部使用。|
|RESMANAGER|僅供內部使用。|
|資源|僅供內部使用。|
|RESQUEUE|僅供內部使用。|
|RFS_THREAD_QUEUE|僅供內部使用。|
|RG_TIMER|僅供內部使用。|
|ROWGROUP_VERSIONS|僅供內部使用。|
|RPCCHANNELPOOL|僅供內部使用。|
|RPCPACKAGE|僅供內部使用。|
|RPCREQUESTORCONTEXT|僅供內部使用。|
|RWLOCK_LAST|僅供內部使用。|
|SATELLITE_CONNECTION|僅供內部使用。|
|SBS_CLIENT_ENDPOINTS|僅供內部使用。|
|SBS_CLIENT_REQUESTS|僅供內部使用。|
|SBS_DISPATCH|僅供內部使用。|
|SBS_PENDING|僅供內部使用。|
|SBS_SERVER_XACT_TASK_PROXY|僅供內部使用。|
|SBS_TRANSPORT|僅供內部使用。|
|SBS_UCS_DISPATCH|僅供內部使用。|
|安全性|僅供內部使用。|
|SECURITY_CACHE|僅供內部使用。|
|SECURITY_FEDAUTH_AAD_BECWSCONNS|僅供內部使用。|
|SEMANTIC_TICACHE|僅供內部使用。|
|SEQUENCED_OBJECT|僅供內部使用。|
|SEQUEUE_SIZED_THREADSAFE|僅供內部使用。|
|SESSION_KILLER|僅供內部使用。|
|SESSION_MANAGER|僅供內部使用。|
|SESSION_SEC_CONTEXT|僅供內部使用。|
|SETRANGE_SYNC|僅供內部使用。|
|SHARABLE_SESSION_OBJECTS|僅供內部使用。|
|SLO_INFO_LIST|僅供內部使用。|
|SNI|僅供內部使用。|
|SNI_NODE_PENDING_IO_QUEUE|僅供內部使用。|
|SOAPSESSIONS|僅供內部使用。|
|SOS_ABORT_TASK|僅供內部使用。|
|SOS_ACTIVEDESCRIPTOR|僅供內部使用。|
|SOS_BLOCKALLOCPARTIALLIST|僅供內部使用。|
|SOS_BLOCKDESCRIPTORBUCKET|僅供內部使用。|
|SOS_CACHESTORE|同步存取 SQL Server 中的各種記憶體內部快取，例如計畫快取或臨時表快取。 這種 spinlock 類型的繁重爭用可能表示許多不同的事情，視競爭中的特定快取而定。 請聯絡 [!INCLUDE[msCoName](../../includes/msconame-md.md)] 客戶支援服務，協助針對此 spinlock 類型進行疑難排解。 |
|SOS_CACHESTORE_CLOCK|僅供內部使用。|
|SOS_CLOCKALG_INTERNODE_SYNC|僅供內部使用。|
|SOS_DEBUG_HOOK|僅供內部使用。|
|SOS_DESCDATABUFFERLIST|僅供內部使用。|
|SOS_LARGEPAGE_ALLOCATOR|僅供內部使用。|
|SOS_MINITHREAD|僅供內部使用。|
|SOS_NODE|僅供內部使用。|
|SOS_OBJECT_POOL|僅供內部使用。|
|SOS_OBJECT_STORE|僅供內部使用。|
|SOS_OOM_CHECK|僅供內部使用。|
|SOS_PHYS_PAGE_CACHE|僅供內部使用。|
|SOS_RESOURCE_CLERK_LIST|僅供內部使用。|
|SOS_RINGBUFFER_RECORD|僅供內部使用。|
|SOS_RW|僅供內部使用。|
|SOS_SATELLITE_USER_POOL|僅供內部使用。|
|SOS_SCHEDULER|僅供內部使用。|
|SOS_SELIST_SIZED_SLOCK|僅供內部使用。|
|SOS_SUSPEND_QUEUE|僅供內部使用。|
|SOS_SYSTHREAD|僅供內部使用。|
|SOS_SYSTHREAD_DISPATCHER|僅供內部使用。|
|SOS_TASK|僅供內部使用。|
|SOS_TLIST|僅供內部使用。|
|SOS_VM_LOW|僅供內部使用。|
|SOS_WAIT_STATS|僅供內部使用。|
|SOS_WAITABLE_ADDRESS_HASHBUCKET|僅供內部使用。|
|SPIN_EVENT_MUTEX|僅供內部使用。|
|SPL_DISPATCHER_LIST|僅供內部使用。|
|SPL_DISPATCHER_QUEUE|僅供內部使用。|
|SPL_NONYIELD_ANALYSIS|僅供內部使用。|
|SPL_QUERY_STORE_CTX_INITIALIZED|僅供內部使用。|
|SPL_QUERY_STORE_EXEC_STATS_AGG|僅供內部使用。|
|SPL_QUERY_STORE_EXEC_STATS_READ|僅供內部使用。|
|SPL_QUERY_STORE_STATS_COOKIE_CACHE|僅供內部使用。|
|SPL_SOS_DISPATCHER|僅供內部使用。|
|SPL_TDS_PKT_QUEUE|僅供內部使用。|
|SPL_XE_BUFFER_MGR|僅供內部使用。|
|SPL_XE_DISPATCHER_QUEUE|僅供內部使用。|
|SPL_XE_NOTIFICATION_CALLBACK_LIST|僅供內部使用。|
|SPL_XE_SESSION_EVENT_MGR|僅供內部使用。|
|SPL_XE_SESSION_MGR|僅供內部使用。|
|SPL_XE_SESSION_TARGET_MGR|僅供內部使用。|
|SPT_PROFILE|僅供內部使用。|
|SQL_MGR|僅供內部使用。|
|SQL_NORM|僅供內部使用。|
|SQLTRACE_FILE_BUFFER|僅供內部使用。|
|SRVPROC|僅供內部使用。|
|STACK_HASHER|僅供內部使用。|
|SUBLATCH|僅供內部使用。|
|SUBPDESC|僅供內部使用。|
|SUBPDESC_LIST|僅供內部使用。|
|SVC_BROKER_CTRL|僅供內部使用。|
|SVC_BROKER_DEBUG_LIST|僅供內部使用。|
|SVC_BROKER_LIST|僅供內部使用。|
|SVC_BROKER_OBJECT|僅供內部使用。|
|SYNCPOINT_RESOURCE|僅供內部使用。|
|TaskElapsedExecutionMonitor|僅供內部使用。|
|TDS_TVP|僅供內部使用。|
|TESTTEAM|僅供內部使用。|
|TESTTEAMEXPONENTIAL|僅供內部使用。|
|TESTTEAMEXPONENTIALTASTAS|僅供內部使用。|
|TESTTEAMTASTAS|僅供內部使用。|
|TMP_SESS_KEY|僅供內部使用。|
|TSQL_DEBUG|僅供內部使用。|
|TXFRM_REPL|僅供內部使用。|
|VDI_OPERATION|僅供內部使用。|
|WINFAB_REPORT_FAULT|僅供內部使用。|
|WRITE_PAGE_RECORDER|僅供內部使用。|
|X_PACKET_LIST|僅供內部使用。|
|X_PIPE|僅供內部使用。|
|X_PIPE_DEMAND|僅供內部使用。|
|X_PORT|僅供內部使用。|
|XACT_LOCK_INFO|僅供內部使用。|
|XACT_LOCKINFO_TASK|僅供內部使用。|
|XACT_WORKSPACE|僅供內部使用。|
|XCB|僅供內部使用。|
|XCB_FREE_LIST|僅供內部使用。|
|XCB_HASH|僅供內部使用。|
|XCHNG_TRACE|僅供內部使用。|
|XDES|僅供內部使用。|
|XDES_HASH|僅供內部使用。|
|XDESMGR|僅供內部使用。|
|XDESTABLELIST|僅供內部使用。|
|XE_RATE_LIMITER_STRETCHDB|僅供內部使用。|
|XE_SESSION_STORAGE|僅供內部使用。|
|XID_ARRAY|僅供內部使用。|
|XIO_BLOCKLIST|僅供內部使用。|
|XIO_REQSTR|僅供內部使用。|
|XIO_SEQNUMBUMP|僅供內部使用。|
|XIOSTATS|僅供內部使用。|
|XTP_RT_DATA_LIST|僅供內部使用。|
|XTS_MGR|僅供內部使用。|
|XVB_CSN|僅供內部使用。|
|XVB_LIST|僅供內部使用。|
 

  
## <a name="see-also"></a>另請參閱  
 
 [DBCC SQLPERF &#40;Transact-SQL&#41;](../../t-sql/database-console-commands/dbcc-sqlperf-transact-sql.md)   
 
 [SQL Server 作業系統相關的動態管理檢視 &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sql-server-operating-system-related-dynamic-management-views-transact-sql.md)  

 [何時 Spinlock SQL Server 中 CPU 使用率的重要驅動程式？](https://techcommunity.microsoft.com/t5/SQL-Server-Support/When-is-Spinlock-a-Significant-Driver-of-CPU-utilization-in-SQL/ba-p/530142)

 [診斷和解決 SQL Server 上的 Spinlock 爭用](../diagnose-resolve-spinlock-contention.md)
  
  


