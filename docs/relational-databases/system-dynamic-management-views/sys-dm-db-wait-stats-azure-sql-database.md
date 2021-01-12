---
description: sys.dm_db_wait_stats (Azure SQL Database)
title: sys.dm_db_wait_stats (Azure SQL Database) Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.service: sql-database
ms.reviewer: ''
ms.topic: language-reference
f1_keywords:
- dm_db_wait_stats_TSQL
- dm_db_wait_stats
- sys.dm_db_wait_stats
- sys.dm_db_wait_stats_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_db_wait_stats dynamic management view
- dm_db_wait_stats
ms.assetid: 00abd0a5-bae0-4d71-b173-f7a14cddf795
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: = azuresqldb-current
ms.openlocfilehash: 3d711b3eb0cb99c65582d8feacf8678fbca264c7
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98092910"
---
# <a name="sysdm_db_wait_stats-azure-sql-database"></a>sys.dm_db_wait_stats (Azure SQL Database)
[!INCLUDE[Azure SQL Database Azure SQL Managed Instance](../../includes/applies-to-version/asdb-asdbmi.md)]

  傳回作業期間執行的執行緒所遇到之所有等候的相關資訊。 您可以使用這份彙總檢視來診斷 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 的效能問題，以及特定查詢和批次的效能問題。  
  
 在執行查詢時，特定類型的等候時間可以指出查詢中的瓶頸或拋錨點。 同樣地，等候時間很長或伺服器等候計數很大，也代表伺服器執行個體中互動查詢互動的瓶頸或熱點。 例如，鎖定等候表示查詢在競爭資料；資料頁 IO 閂鎖等候表示 IO 回應時間很慢；資料頁閂鎖更新等候表示檔案配置不正確。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|wait_type|**nvarchar(60)**|等候類型的名稱。 如需詳細資訊，請參閱這個主題稍後的＜[等候的類型](#WaitTypes)＞。|  
|waiting_tasks_count|**bigint**|這個等候類型的等候次數。 這個計數器是從每次開始等候時逐量遞增計算。|  
|wait_time_ms|**bigint**|這個等候類型的總等候時間 (以毫秒為單位)。 這個時間包括 signal_wait_time_ms 在內。|  
|max_wait_time_ms|**bigint**|這個等候類型的等候時間上限。|  
|signal_wait_time_ms|**bigint**|從等候執行緒接獲訊號到開始執行的時間。|  
  
## <a name="remarks"></a>備註  
  
-   這份動態管理檢視只會顯示目前資料庫的資料。  
  
-   這份動態管理檢視會顯示已完成等候的時間， 而並未顯示目前的等候。  
  
-   只要資料庫已移動或離線，計數器就會重設為零。  
  
-   在下列任何一種情況下，SQL Server 工作者執行緒都不算是在等候中：  
  
    -   資源可以使用。  
  
    -   佇列不是空的。  
  
    -   外部處理序完成。  
  
-   這些統計資料一經過 SQL Database 容錯移轉事件後就不會保存，而且所有的資料都是從上次重設統計資料之後開始累加計算。  
  
## <a name="permissions"></a>權限  
 需要伺服器的 VIEW DATABASE STATE 權限。  
  
##  <a name="types-of-waits"></a><a name="WaitTypes"></a> 等候類型  
 資源等候  
 當工作者要求存取無法使用的資源時 (因為該資源正被另一個工作者使用中，因此還不能使用)，會發生資源等候的情形。 鎖定、閂鎖、網路和磁碟 I/O 等候，都是資源等候的範例。 鎖定和閂鎖等候是對同步處理物件的等候。  
  
 佇列等候  
 佇列等候發生在工作者因為等候指派工作而閒置時。 佇列等候大部分都是在進行系統背景工作 (例如，監視死結和刪除記錄清除工作) 時發生。 這些工作會等候工作要求被置於工作佇列。 即使沒有新的封包置於佇列中，佇列等候也可能會定期變成使用中狀態。  
  
 外部等候  
 外部等候是 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 工作者正在等候外部事件 (例如，擴充預存程序呼叫或連結伺服器查詢) 完成時發生。 記住，當您診斷封鎖問題時，外部等候不會一直暗示工作者正在閒置，因為工作者可能很積極的在執行一些外部程式碼。  
  
 雖然執行緒已經不在等候中，執行緒也不必立即開始執行。 因為這類執行緒會先置於可執行之工作者的佇列上，而且必須等候排程器執行某個配量才行。  
  
 在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 等候時間計數器中是 **Bigint** 值，因此在舊版的中，並不像計數器變換一樣容易成為相等的計數器 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。  
  
 下表列出工作會遇到的等候類型。  
  
|等候類型|描述|  
|---------------|-----------------|  
|ABR|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|ASSEMBLY_LOAD|在組件載入的獨佔存取期間發生。|  
|ASYNC_DISKPOOL_LOCK|在同步處理執行建立檔案，或者檔案初始化等工作的平行執行緒時發生。|  
|ASYNC_IO_COMPLETION|在工作等候 I/O 完成時發生。|  
|ASYNC_NETWORK_IO|當工作被封鎖在網路後面時，進行網路寫入就會發生這種情形。 請確認用戶端正在處理來自伺服器的資料。|  
|AUDIT_GROUPCACHE_LOCK|當控制特殊快取之存取權的鎖定上存在等候時發生。 此快取包含哪些稽核要用來稽核每個稽核動作群組的相關資訊。|  
|AUDIT_LOGINCACHE_LOCK|當控制特殊快取之存取權的鎖定上存在等候時發生。 此快取包含哪些稽核要用來稽核登入稽核動作群組的相關資訊。|  
|AUDIT_ON_DEMAND_TARGET_LOCK|當用來確保稽核相關「擴充事件」目標之單一初始化的鎖定上存在等候時發生。|  
|AUDIT_XE_SESSION_MGR|當用來同步處理稽核相關「擴充事件」工作階段之啟動和停止的鎖定上存在等候時發生。|  
|備份|當工作被當做備份處理的一部分而加以封鎖時發生。|  
|BACKUP_OPERATOR|在工作等候磁帶掛載時發生。 若要檢視磁帶狀態，請查詢 [sys.dm_io_backup_tapes](../../relational-databases/system-dynamic-management-views/sys-dm-io-backup-tapes-transact-sql.md)。 如果掛載作業沒有暫止，這個等候類型可能就表示磁帶機發生硬體問題。|  
|BACKUPBUFFER|當備份工作正在等候資料，或者等候儲存資料的緩衝區時發生。 除非當工作正在等候磁帶掛載，否則這個類型並非標準類型。|  
|BACKUPIO|當備份工作正在等候資料，或者等候儲存資料的緩衝區時發生。 除非當工作正在等候磁帶掛載，否則這個類型並非標準類型。|  
|BACKUPTHREAD|在工作等候備份工作完成時發生。 等候時間可能會很長，從幾分鐘到數小時都可能。 如果等候的工作是在 I/O 處理序中，這個類型就不表示發生問題。|  
|BAD_PAGE_PROCESS|當背景可疑頁面記錄器想要避免執行間隔超過每 5 秒一次時發生。 可疑頁面過多將導致記錄器經常執行。|  
|BROKER_CONNECTION_RECEIVE_TASK|在連接端點上等候接收訊息的存取權時發生。 端點的接收存取是序列化的。|  
|BROKER_ENDPOINT_STATE_MUTEX|當存取 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 連接端點的狀態有競爭狀況時發生。 存取變更的狀態是序列化的。|  
|BROKER_EVENTHANDLER|當工作正在 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 的主要事件處理常式中等候時發生。 發生的時間相當短暫。|  
|BROKER_INIT|在每一個使用中資料庫中將 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 初始化時發生。 這種情況不常發生。|  
|BROKER_MASTERSTART|當工作正在等候 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 的主要事件處理常式啟動時發生。 發生的時間相當短暫。|  
|BROKER_RECEIVE_WAITFOR|當 RECEIVE WAITFOR 在等候時發生。 如果沒有任何訊息可以接收，則此為標準值。|  
|BROKER_REGISTERALLENDPOINTS|在將 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 連接端點初始化時發生。 發生的時間相當短暫。|  
|BROKER_SERVICE|當與目標服務相關聯的 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 目的地清單已更新或重新設定優先權時發生。|  
|BROKER_SHUTDOWN|當 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 計畫關閉時發生。 即使發生，時間也相當短暫。|  
|BROKER_TASK_STOP|當 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 佇列工作處理常式嘗試關閉工作時發生。 狀態檢查會序化列，而且必須事先處於執行中狀態。|  
|BROKER_TO_FLUSH|當 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 延遲清除器清除工作資料表的記憶體中傳輸物件 (TO) 時發生。|  
|BROKER_TRANSMITTER|當 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 發送器在等候工作時發生。|  
|BUILTIN_HASHKEY_MUTEX|可能在啟動執行個體之後，而內部資料結構正在初始化時發生。 資料結構初始化之後就不會再次發生。|  
|CHECK_PRINT_RECORD|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|CHECKPOINT_QUEUE|當檢查點工作在等候下一個檢查點要求時發生。|  
|CHKPT|在伺服器啟動時發生，目的在告知檢查點執行緒，它可以啟動了。|  
|CLEAR_DB|在變更資料庫狀態的作業期間發生，例如開啟或關閉資料庫。|  
|CLR_AUTO_EVENT|當工作目前在執行 Common Language Runtime (CLR) 執行，以及在等候特定自動事件起始時發生。 等候時間長是正常情況，並不表示發生任何問題。|  
|CLR_CRST|當工作目前正在執行 CLR 執行，以及正在等候進入目前正被另一個工作使用中之工作的重要區段時發生。|  
|CLR_JOIN|當工作目前正在執行 CLR 執行，以及正在等候另一個工作結束時發生。 這個等候狀態是在工作之間有聯結時發生。|  
|CLR_MANUAL_EVENT|當工作目前正在執行 CLR 執行，以及正在等候特定的手動事件起始時發生。|  
|CLR_MEMORY_SPY|在用來記錄來自 CLR 之所有虛擬記憶體配置的資料結構等候取得鎖定期間發生。 如果存在平行存取，資料結構就會鎖定，以便維持其完整性。|  
|CLR_MONITOR|當工作目前正在執行 CLR 執行，以及正在等候取得監視器鎖定時發生。|  
|CLR_RWLOCK_READER|當工作目前正在執行 CLR 執行，以及正在等候讀取器鎖定時發生。|  
|CLR_RWLOCK_WRITER|當工作目前正在執行 CLR 執行，以及正在等候寫入器鎖定時發生。|  
|CLR_SEMAPHORE|當工作目前正在執行 CLR 執行，以及正在等候信號時發生。|  
|CLR_TASK_START|在等候 CLR 工作完成啟動時發生。|  
|CLRHOST_STATE_ACCESS|在等候取得 CLR 主控資料結構之獨佔存取權的情況下發生。 這種等候類型會在設定或終止 CLR 執行階段時發生。|  
|CMEMTHREAD|當工作在安全執行緒記憶體物件待命時發生。 如果有多個工作嘗試從同一個記憶體物件配置記憶體而導致競爭情形時，等候時間可能會增加。|  
|CXPACKET |在同步處理查詢處理器交換重複時發生。 如果這個等候類型的競爭情形已經成為問題時，不妨考慮降低平行處理的程度。|  
|CXROWSET_SYNC|在平行範圍掃描期間發生。|  
|DAC_INIT|當專用管理員連接正在初始化時發生。|  
|DBMIRROR_DBM_EVENT|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|DBMIRROR_DBM_MUTEX|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|DBMIRROR_EVENTS_QUEUE|當資料庫鏡像在等候處理事件時發生。|  
|DBMIRROR_SEND |當工作在網路層等候通訊積存清除，以傳送訊息時發生。 它指出通訊層已經開始多載，並影響到資料庫鏡像資料輸送量。|  
|DBMIRROR_WORKER_QUEUE|指出資料庫鏡像工作者工作正在等候其他工作。|  
|DBMIRRORING_CMD |當工作在等候記錄排清到磁碟時發生。 這個等候狀態預期會保留一段很長的時間。|  
|DEADLOCK_ENUM_MUTEX |當死結監視器和 sys.dm_os_waiting_tasks 嘗試確定 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 沒有同時執行多個死結搜尋時發生。|  
|DEADLOCK_TASK_SEARCH |在此資源上的等候時間如果很長，表示伺服器正在 sys.dm_os_waiting_tasks 之上執行查詢，而這些查詢則造成死結監視器無法執行死結搜尋。 只有死結監視器才會使用這種等候類型。 在 sys.dm_os_waiting_tasks 之上的查詢會使用 DEADLOCK_ENUM_MUTEX。|  
|DEBUG|在 [!INCLUDE[tsql](../../includes/tsql-md.md)] 和 CLR 針對內部同步處理進行偵錯時發生。|  
|DISABLE_VERSIONING|當 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 輪詢版本交易管理員，看看最早使用中交易的時間戳記，是否晚於狀態開啟改變時的時間戳記時發生。 如果是這樣，則表示所有在 ALTER DATABASE 陳述式執行之前所啟動的快照集交易，已經全部完成了。 這個等候狀態是在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 利用 ALTER DATABASE 陳述式停用版本控制時所使用。|  
|DISKIO_SUSPEND|當外部備份在使用中時，工作正在等候存取檔案時發生。 這是針對每個等候中的使用者處理序所報告。 如果超過每個使用者處理序五次時，可能表示外部備份花了太多時間才完成。|  
|DISPATCHER_QUEUE_SEMAPHORE|當發送器集區的執行緒正在等候處理其他工作時發生。 這種等候類型的等候時間應該會在發送器閒置時增加。|  
|DLL_LOADING_MUTEX|在等候 XML 剖析器 DLL 載入時發生一次。|  
|DROPTEMP|卸除暫存物件時，如果前一次的嘗試失敗，就會在兩次嘗試之間發生這種等候類型。 等候持續時間將以指數方式，隨著每一次的卸除嘗試失敗而增加。|  
|DTC|當工作在服務管理狀態轉移所用的事件時發生。 這個狀態可以控制在 [!INCLUDE[msCoName](../../includes/msconame-md.md)] 收到 MS DTC 服務已經無法使用的通知之後，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 分散式交易協調器 (MS DTC) 交易何時復原。<br /><br /> 這個狀態也可以描述當 MS DTC 交易的認可是由 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 起始，而且 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 在等候 MS DTC 認可完成時，正在等候中的工作。|  
|DTC_ABORT_REQUEST |當工作階段在等候接收 MS DTC 交易的擁有權時，在 MS DTC 工作者工作階段發生。 MS DTC 擁有交易之後，工作階段就可以回復該交易。 通常，工作階段會等候另一個使用該交易的工作階段。|  
|DTC_RESOLVE |當復原工作在等候跨資料庫交易中的 master 資料庫，讓工作得以查詢交易結果時發生。|  
|DTC_STATE |當工作在保護您對內部 MS DTC 全域狀態物件所做變更的事件待命時發生。 這個狀態的保留時間很短。|  
|DTC_TMDOWN_REQUEST|當 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 接收 MS DTC 服務無法使用的通知時，在 MS DTC 工作者工作階段發生。 首先，工作者會等候 MS DTC 復原處理序啟動。 接著，工作者會等候取得他所處理的分散式交易結果。 除非與 MS DTC 服務的連接已經重新建立，否則這項作業會繼續進行。|  
|DTC_WAITFOR_OUTCOME |當復原工作在等候 MS DTC 開始解析準備交易時發生。|  
|DUMP_LOG_COORDINATOR |當主要工作正在等候子工作產生資料時發生。 這個狀態通常不會發生。 如果等候時間很長，就表示發生了非預期的封鎖。 您最好調查一下子工作。|  
|DUMPTRIGGER|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|EC|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|EE_PMOLOCK|在陳述式執行期間同步處理某些類型的記憶體配置時發生。|  
|EE_SPECPROC_MAP_INIT|在同步處理內部程序雜湊表的建立時發生。 這個等候只可能會在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體開始之後初次存取雜湊表時發生。|  
|ENABLE_VERSIONING|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 在宣告資料庫可以轉移到允許快照集隔離的狀態之前，等候這個資料庫中所有的更新交易完成時發生。 這個狀態是在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 利用 ALTER DATABASE 陳述式啟用快照集隔離時所使用。|  
|ERROR_REPORTING_MANAGER|在同步處理多個並行錯誤記錄檔的初始化時發生。|  
|EXCHANGE|在平行查詢期間同步處理查詢處理器交換重複時發生。|  
|EXECSYNC |平行查詢期間在與交換重複無關之區域的查詢處理器中進行同步處理時發生。 這類區域的範例包括點陣圖、大型二進位物件 (LOB) 和多工緩衝處理重複。 LOB 可能會經常使用這個等候狀態。|  
|EXECUTION_PIPE_EVENT_INTERNAL|在透過連接內容傳送的批次執行產生者與取用者部分之間同步處理期間發生。|  
|FAILPOINT|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|FCB_REPLICA_READ |在同步處理快照集 (或 DBCC 所建立的暫時快照集) 疏鬆檔案的讀取作業時發生。|  
|FCB_REPLICA_WRITE |在同步處理將資料頁發送或提取到快照集 (或 DBCC 所建立的暫時快照集) 疏鬆檔案時發生。|  
|FS_FC_RWLOCK|當 FILESTREAM 記憶體回收行程等候進行下列其中一項作業時發生：<br /><br /> 停用記憶體回收 (由備份和還原所使用)。<br /><br /> 執行一次 FILESTREAM 記憶體回收行程的循環。|  
|FS_GARBAGE_COLLECTOR_SHUTDOWN|當 FILESTREAM 記憶體回收行程正在等候清除工作完成時發生。|  
|FS_HEADER_RWLOCK|當系統等候取得 FILESTREAM 資料容器之 FILESTREAM 標頭的存取權來讀取或更新 FILESTREAM 標頭檔 (Filestream.hdr) 內容時發生。|  
|FS_LOGTRUNC_RWLOCK|當系統等候取得 FILESTREAM 記錄截斷的存取權來進行下列其中一項作業時發生：<br /><br /> 暫時停用 FILESTREAM 記錄 (FSLOG) 截斷 (由備份和還原所使用)。<br /><br /> 執行一次 FSLOG 截斷的循環。|  
|FSA_FORCE_OWN_XACT|當 FILESTREAM 檔案 I/O 作業需要繫結至相關聯的交易，但是此交易目前由其他工作階段所擁有時發生。|  
|FSAGENT|當 FILESTREAM 檔案 I/O 作業正在等候其他檔案 I/O 作業所使用的 FILESTREAM 代理程式資源時發生。|  
|FSTR_CONFIG_MUTEX|當系統等候其他 FILESTREAM 功能重新設定完成時發生。|  
|FSTR_CONFIG_RWLOCK|當系統等候序列化 FILESTREAM 組態參數的存取權時發生。|  
|FT_METADATA_MUTEX|記載僅供參考之用。 不支援。 我們無法保證未來的相容性。|  
|FT_RESTART_CRAWL|在全文檢索搜耙必須從最後一個已知的恰當起點重新啟動以便從暫時性失敗中復原時發生。 等候可讓目前正在處理該母體擴展的工作者工作得以完成或結束目前的步驟。|  
|FULLTEXT GATHERER|在同步處理全文檢索作業時發生。|  
|GUARDIAN|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|HTTP_ENUMERATION |在啟動以列舉 HTTP 端點來啟動 HTTP 時發生。|  
|HTTP_START|在連接等候 HTTP 完成初始化時發生。|  
|IMPPROV_IOWAIT|在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 等候大量載入 I/O 完成時發生。|  
|INTERNAL_TESTING|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|IO_AUDIT_MUTEX |在同步處理追蹤事件緩衝區時發生。|  
|IO_COMPLETION|在等候 I/O 作業完成時發生。 這種等候類型通常代表非資料頁面 I/O。 資料頁面 I/O 完成等候會顯示為 PAGEIOLATCH_* 等候。|  
|IO_QUEUE_LIMIT|當 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 的非同步 IO 佇列有太多 IO 暫止時發生。 這種等候類型會封鎖嘗試發出另一個 IO 的工作，直到 IO 等候的數目下降到低於臨界值。 臨界值與指派給資料庫的 DTU 成正比。|  
|IO_RETRY|當讀取或寫入磁碟等 I/O 作業由於資源不足而失敗，然後重試時發生。|  
|IOAFF_RANGE_QUEUE|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|KSOURCE_WAKEUP|供服務控制工作在等候來自服務控制管理員的要求時使用。 等候時間長是正常情況，並不表示發生任何問題。|  
|KTM_ENLISTMENT|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|KTM_RECOVERY_MANAGER|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|KTM_RECOVERY_RESOLUTION|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|LATCH_DT |在等候 DT (終結) 閂鎖時發生。 其中不包括緩衝區閂鎖或交易標示閂鎖。 sys.dm_os_latch_stats 中提供 LATCH_* 等候的清單。 請注意，sys.dm_os_latch_stats 會將 LATCH_NL、LATCH_SH、LATCH_UP、LATCH_EX 和 LATCH_DT 等候分組在一起。|  
|LATCH_EX |在等候 EX (獨佔) 閂鎖時發生。 其中不包括緩衝區閂鎖或交易標示閂鎖。 sys.dm_os_latch_stats 中提供 LATCH_* 等候的清單。 請注意，sys.dm_os_latch_stats 會將 LATCH_NL、LATCH_SH、LATCH_UP、LATCH_EX 和 LATCH_DT 等候分組在一起。|  
|LATCH_KP |在等候 KP (保留) 閂鎖時發生。 其中不包括緩衝區閂鎖或交易標示閂鎖。 sys.dm_os_latch_stats 中提供 LATCH_* 等候的清單。 請注意，sys.dm_os_latch_stats 會將 LATCH_NL、LATCH_SH、LATCH_UP、LATCH_EX 和 LATCH_DT 等候分組在一起。|  
|LATCH_NL |[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|LATCH_SH |在等候 SH (共用) 閂鎖時發生。 其中不包括緩衝區閂鎖或交易標示閂鎖。 sys.dm_os_latch_stats 中提供 LATCH_* 等候的清單。 請注意，sys.dm_os_latch_stats 會將 LATCH_NL、LATCH_SH、LATCH_UP、LATCH_EX 和 LATCH_DT 等候分組在一起。|  
|LATCH_UP |在等候 UP (更新) 閂鎖時發生。 其中不包括緩衝區閂鎖或交易標示閂鎖。 sys.dm_os_latch_stats 中提供 LATCH_* 等候的清單。 請注意，sys.dm_os_latch_stats 會將 LATCH_NL、LATCH_SH、LATCH_UP、LATCH_EX 和 LATCH_DT 等候分組在一起。|  
|LAZYWRITER_SLEEP |在延遲寫入器工作暫止時發生。 這是等候中的背景工作所花的時間。 如果您要尋找使用者拋錨點，就不要考慮這個狀態。|  
|LCK_M_BU |當工作在等候取得大量更新 (BU) 鎖定時發生。 如需鎖定相容性矩陣，請參閱 [sys.dm_tran_locks &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql.md)。|  
|LCK_M_IS|當工作在等候取得意圖共用 (IS) 鎖定時發生。 如需鎖定相容性矩陣，請參閱 [sys.dm_tran_locks &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql.md)。|  
|LCK_M_IU|當工作在等候取得意圖更新 (IU) 鎖定時發生。 如需鎖定相容性矩陣，請參閱 [sys.dm_tran_locks &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql.md)。|  
|LCK_M_IX |當工作在等候取得意圖獨佔 (IX) 鎖定時發生。 如需鎖定相容性矩陣，請參閱 [sys.dm_tran_locks &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql.md)。|  
|LCK_M_RIn_NL |當工作在等候取得目前索引鍵值的 NULL 鎖定，以及目前索引鍵和前一個索引鍵之間的插入範圍鎖定時發生。 索引鍵的 NULL 鎖定是一個立即釋放鎖定。 如需鎖定相容性矩陣，請參閱 [sys.dm_tran_locks &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql.md)。|  
|LCK_M_RIn_S |當工作在等候取得目前索引鍵值的共用鎖定，以及目前索引鍵和前一個索引鍵之間的插入範圍鎖定時發生。 如需鎖定相容性矩陣，請參閱 [sys.dm_tran_locks &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql.md)。|  
|LCK_M_RIn_U |工作在等候取得目前索引鍵值的更新鎖定，以及目前索引鍵和前一個索引鍵之間的插入範圍鎖定。 如需鎖定相容性矩陣，請參閱 [sys.dm_tran_locks &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql.md)。|  
|LCK_M_RIn_X |當工作在等候取得目前索引鍵值的獨佔鎖定，以及目前索引鍵和前一個索引鍵之間的插入範圍鎖定時發生。 如需鎖定相容性矩陣，請參閱 [sys.dm_tran_locks &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql.md)。|  
|LCK_M_RS_S |當工作在等候取得目前索引鍵值的共用鎖定，以及目前索引鍵和前一個索引鍵之間的共用範圍鎖定時發生。 如需鎖定相容性矩陣，請參閱 [sys.dm_tran_locks &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql.md)。|  
|LCK_M_RS_U |當工作在等候取得目前索引鍵值的更新鎖定，以及目前索引鍵和前一個索引鍵之間的更新範圍鎖定時發生。 如需鎖定相容性矩陣，請參閱 [sys.dm_tran_locks &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql.md)。|  
|LCK_M_RX_S |當工作在等候取得目前索引鍵值的共用鎖定，以及目前索引鍵和前一個索引鍵之間的獨佔範圍鎖定時發生。 如需鎖定相容性矩陣，請參閱 [sys.dm_tran_locks &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql.md)。|  
|LCK_M_RX_U |當工作在等候取得目前索引鍵值的更新鎖定，以及目前索引鍵和前一個索引鍵之間的獨佔範圍鎖定時發生。 如需鎖定相容性矩陣，請參閱 [sys.dm_tran_locks &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql.md)。|  
|LCK_M_RX_X |當工作在等候取得目前索引鍵值的獨佔鎖定，以及目前索引鍵和前一個索引鍵之間的獨佔範圍鎖定時發生。 如需鎖定相容性矩陣，請參閱 [sys.dm_tran_locks &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql.md)。|  
|LCK_M_S |當工作在等候取得共用鎖定時發生。 如需鎖定相容性矩陣，請參閱 [sys.dm_tran_locks &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql.md)。|  
|LCK_M_SCH_M |當工作在等候取得結構描述修改鎖定時發生。 如需鎖定相容性矩陣，請參閱 [sys.dm_tran_locks &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql.md)。|  
|LCK_M_SCH_S |當工作在等候取得結構描述共用鎖定時發生。 如需鎖定相容性矩陣，請參閱 [sys.dm_tran_locks &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql.md)。|  
|LCK_M_SIU |當工作在等候取得共用意圖更新鎖定時發生。 如需鎖定相容性矩陣，請參閱 [sys.dm_tran_locks &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql.md)。|  
|LCK_M_SIX |當工作在等候取得共用意圖獨佔鎖定時發生。 如需鎖定相容性矩陣，請參閱 [sys.dm_tran_locks &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql.md)。|  
|LCK_M_U |當工作在等候取得更新鎖定時發生。 如需鎖定相容性矩陣，請參閱 [sys.dm_tran_locks &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql.md)。|  
|LCK_M_UIX |當工作在等候取得更新意圖獨佔鎖定時發生。 如需鎖定相容性矩陣，請參閱 [sys.dm_tran_locks &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql.md)。|  
|LCK_M_X |當工作在等候取得獨佔鎖定時發生。 如需鎖定相容性矩陣，請參閱 [sys.dm_tran_locks &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql.md)。|  
|LOG_RATE_GOVERNOR|發生在資料庫正在等待配額寫入記錄中時。|  
|LOGBUFFER |當工作在等候記錄緩衝區的空間來儲存記錄時發生。 如果這個值一直居高不下，可能表示記錄裝置趕不上伺服器產生的記錄量。|  
|LOGGENERATION|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|LOGMGR |當工作在關閉資料庫時，於關閉記錄之前等候任何未完成的記錄 I/O 完成時發生。|  
|LOGMGR_FLUSH |[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|LOGMGR_QUEUE|在記錄寫入器工作等候工作要求時發生。|  
|LOGMGR_RESERVE_APPEND |當工作在等候記錄截斷清出記錄空間，讓工作寫入新記錄時發生。 請考慮增加受影響之資料庫的記錄檔案大小，以縮短這個等候時間。|  
|LOWFAIL_MEMMGR_QUEUE |在等候記憶體變成可供使用的狀態時發生。|  
|MSQL_DQ |在工作等候分散式查詢作業完成時發生。 這種等候類型可用來偵測潛在的 Multiple Active Result Set (MARS) 應用程式死結。 等候會隨著分散式查詢呼叫的完成而結束。|  
|MSQL_XACT_MGR_MUTEX |當工作在等候取得工作階段交易管理員的擁有權，以執行工作階段層級交易作業時發生。|  
|MSQL_XACT_MUTEX|在同步處理交易使用量時發生。 要求必須先取得 Mutex，才能使用交易。|  
|MSQL_XP |當工作在等候擴充預存程序結束時發生。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會使用這個等候狀態來偵測可能發生的 MARS 應用程式死結。 這個等候會隨著擴充預存程序呼叫的結束而停止。|  
|MSSEARCH|在使用全文檢索搜尋呼叫時發生。 這個等候會隨著全文檢索作業的完成而結束。 這並不表示發生競爭情況，而是指出全文檢索作業的持續時間。|  
|NET_WAITFOR_PACKET|當連接在網路讀取期間等候網路封包時發生。|  
|OLEDB|當 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 呼叫 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client OLE DB Provider 時發生。 這個等候類型不用於同步處理， 而會指出呼叫 OLE DB 提供者的持續時間。|  
|ONDEMAND_TASK_QUEUE|在背景工作等候高優先權的系統工作要求時發生。 如果等候時間很長，表示沒有高優先權的要求需要處理，而且應該不會造成任何問題。|  
|PAGEIOLATCH_DT |當工作在位於 I/O 要求中的緩衝區閂鎖待命時發生。 閂鎖要求是終結模式。 如果等候時間很長，表示磁碟子系統有問題。|  
|PAGEIOLATCH_EX |當工作在位於 I/O 要求中的緩衝區閂鎖待命時發生。 閂鎖要求是獨佔模式。 如果等候時間很長，表示磁碟子系統有問題。|  
|PAGEIOLATCH_KP |當工作在位於 I/O 要求中的緩衝區閂鎖待命時發生。 閂鎖要求是保留模式。 如果等候時間很長，表示磁碟子系統有問題。|  
|PAGEIOLATCH_NL |[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|PAGEIOLATCH_SH |當工作在位於 I/O 要求中的緩衝區閂鎖待命時發生。 閂鎖要求是共用模式。 如果等候時間很長，表示磁碟子系統有問題。|  
|PAGEIOLATCH_UP |當工作在位於 I/O 要求中的緩衝區閂鎖待命時發生。 閂鎖要求是更新模式。 如果等候時間很長，表示磁碟子系統有問題。|  
|PAGELATCH_DT |當工作不是在 I/O 要求中的緩衝區閂鎖待命時發生。 閂鎖要求是終結模式。|  
|PAGELATCH_EX |當工作不是在 I/O 要求中的緩衝區閂鎖待命時發生。 閂鎖要求是獨佔模式。|  
|PAGELATCH_KP |當工作不是在 I/O 要求中的緩衝區閂鎖待命時發生。 閂鎖要求是保留模式。|  
|PAGELATCH_NL |[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|PAGELATCH_SH |當工作不是在 I/O 要求中的緩衝區閂鎖待命時發生。 閂鎖要求是共用模式。|  
|PAGELATCH_UP |當工作不是在 I/O 要求中的緩衝區閂鎖待命時發生。 閂鎖要求是更新模式。|  
|PARALLEL_BACKUP_QUEUE|在序列化 RESTORE HEADERONLY、RESTORE FILELISTONLY 或 RESTORE LABELONLY 所產生的輸出時發生。|  
|PREEMPTIVE_ABR|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|PREEMPTIVE_AUDIT_ACCESS_EVENTLOG|在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 作業系統 (SQLOS) 排程器切換到先佔式模式，以便將稽核事件寫入 Windows 事件記錄檔時發生。|  
|PREEMPTIVE_AUDIT_ACCESS_SECLOG|當 SQLOS 排程器切換到先佔式模式，以便將稽核事件寫入 Windows 安全性記錄檔時發生。|  
|PREEMPTIVE_CLOSEBACKUPMEDIA|當 SQLOS 排程器切換到先佔式模式，以便關閉備份媒體時發生。|  
|PREEMPTIVE_CLOSEBACKUPTAPE|當 SQLOS 排程器切換到先佔式模式，以便關閉磁帶備份裝置時發生。|  
|PREEMPTIVE_CLOSEBACKUPVDIDEVICE|當 SQLOS 排程器切換到先佔式模式，以便關閉虛擬備份裝置時發生。|  
|PREEMPTIVE_CLUSAPI_CLUSTERRESOURCECONTROL|當 SQLOS 排程器切換到先佔式模式，以便執行 Windows 容錯移轉叢集作業時發生。|  
|PREEMPTIVE_COM_COCREATEINSTANCE|當 SQLOS 排程器切換到先佔式模式，以便建立 COM 物件時發生。|  
|PREEMPTIVE_HADR_LEASE_MECHANISM|CSS 診斷的 Always On 可用性群組租用管理員排程。|  
|PREEMPTIVE_SOSTESTING|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|PREEMPTIVE_STRESSDRIVER|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|PREEMPTIVE_TESTING|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|PREEMPTIVE_XETESTING|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|PRINT_ROLLBACK_PROGRESS|用於使用者處理序在已經使用 ALTER DATABASE 終止子句加以轉換的資料庫中結束時等候。 如需詳細資訊，請參閱 [ALTER DATABASE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-database-transact-sql.md)。|  
|PWAIT_HADR_CHANGE_NOTIFIER_TERMINATION_SYNC|在背景工作正在等候接收 (經由輪詢) Windows Server 容錯移轉叢集通知的背景工作終止時發生。  僅供內部使用。|  
|PWAIT_HADR_CLUSTER_INTEGRATION|附加、取代和/或移除作業正在等候抓取 Always On 內部清單的寫入鎖定 (例如網路、網路位址或可用性群組接聽程式) 的清單。  僅供內部使用。|  
|PWAIT_HADR_OFFLINE_COMPLETED|Always On 卸載可用性群組作業正在等候目標可用性群組離線，再終結 Windows Server 容錯移轉叢集物件。|  
|PWAIT_HADR_ONLINE_COMPLETED|Always On 建立或容錯移轉可用性群組作業正在等候目標可用性群組上線。|  
|PWAIT_HADR_POST_ONLINE_COMPLETED|Always On 卸載可用性群組作業正在等候已排程為先前命令之一部分的任何背景工作終止。 例如，可能會存在將可用性資料庫轉換成主要角色的背景工作。 DROP AVAILABILITY GROUP DDL 必須等候此背景工作終止，才能避免競爭情形。|  
|PWAIT_HADR_WORKITEM_COMPLETED|等候非同步工作完成之執行緒的內部等候。 這是預期的等候，而且可供 CSS 使用。|  
|PWAIT_MD_LOGIN_STATS|在登入統計資料的中繼資料中執行內部同步處理期間發生。|  
|PWAIT_MD_RELATION_CACHE|在資料表或索引的中繼資料中執行內部同步處理期間發生。|  
|PWAIT_MD_SERVER_CACHE|在連結伺服器的中繼資料中執行內部同步處理期間發生。|  
|PWAIT_MD_UPGRADE_CONFIG|在升級伺服器範圍組態時執行內部同步處理期間發生。|  
|PWAIT_METADATA_LAZYCACHE_RWLOCk|在中繼資料快取及資料表中的重複索引或統計資料中執行內部同步處理期間發生。|  
|QPJOB_KILL|指出非同步自動統計資料更新開始執行時，已被 KILL 的呼叫所取消。 終止執行緒已經暫停，正在等候它開始接聽 KILL 命令。 其值最好少於一秒。|  
|QPJOB_WAITFOR_ABORT|指出非同步自動統計資料更新在執行時，已被 KILL 的呼叫所取消。 雖然這項更新已經完成了，但卻被暫停到終止執行緒訊息協調完成為止。 這種狀態雖然很平常，卻很少見，而且為時甚短。 其值最好少於一秒。|  
|QRY_MEM_GRANT_INFO_MUTEX |當查詢執行記憶體管理試圖控制靜態授與資訊清單的存取時發生。 這個狀態會列出目前已授與和等候中之記憶體要求的相關資訊。 這個狀態是一種簡單的存取控制狀態。 這個狀態應該不會有長時間的等候。 如果沒有釋出這個 Mutex，所有新的記憶體使用查詢都會停止回應。|  
|QUERY_ERRHDL_SERVICE_DONE|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|QUERY_EXECUTION_INDEX_SORT_EVENT_OPEN|在離線建立索引建置平行執行時，以及排序中的不同工作者執行緒同步處理排序檔存取作業時的某些情況中發生。|  
|QUERY_NOTIFICATION_MGR_MUTEX |在同步處理查詢通知管理員中的記憶體回收佇列時發生。|  
|QUERY_NOTIFICATION_SUBSCRIPTION_MUTEX |在同步處理查詢通知中交易的狀態時發生。|  
|QUERY_NOTIFICATION_TABLE_MGR_MUTEX |在查詢通知管理員內執行內部同步處理時發生。|  
|QUERY_NOTIFICATION_UNITTEST_MUTEX |[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|QUERY_OPTIMIZER_PRINT_MUTEX|在同步處理查詢最佳化工具診斷輸出生產作業時發生。 只有在依照 [!INCLUDE[msCoName](../../includes/msconame-md.md)] 產品支援部門的指示啟用診斷設定時，才會發生這種等候類型。|  
|QUERY_TRACEOUT|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|QUERY_WAIT_ERRHDL_SERVICE|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|RECOVER_CHANGEDB|在同步處理暖待命資料庫中的資料庫狀態時發生。|  
|REPL_CACHE_ACCESS|在複寫發行項快取上進行同步處理時發生。 在這些等候期間，複寫記錄讀取器將會暫停，而已發行資料表上的資料定義語言 (DDL) 陳述式也會遭到封鎖。|  
|REPL_SCHEMA_ACCESS|在同步處理複寫結構描述版本資訊時發生。 在已複寫物件上執行 DDL 陳述式時，以及當記錄讀取器依據 DDL 出現位置來建立或取用版本化結構描述時，都會出現這種狀態。|  
|REPLICA_WRITES|在工作等候頁面寫入資料庫快照集或 DBCC 複本完成時發生。|  
|REQUEST_DISPENSER_PAUSE|當工作在等候所有未處理 I/O 完成，使其可凍結檔案 I/O 以供快照集備份時發生。|  
|REQUEST_FOR_DEADLOCK_SEARCH|在死結監視器等候開始下個死結搜尋時發生。 這個等候應該在兩次死結偵測之間發生，在此資源上的等候時間如果很長，並不表示發生任何問題。|  
|RESMGR_THROTTLED|當新的要求送入，然後根據 GROUP_MAX_REQUESTS 設定降低速度。|  
|RESOURCE_QUEUE|在同步處理各種內部資源佇列時發生。|  
|RESOURCE_SEMAPHORE|在其他並行查詢導致查詢記憶體要求無法立即取得授權時發生。 如果等候頻率很高且等候時間很長，可能表示並行查詢數目過多、重複編譯，或是記憶體要求量過大。|  
|RESOURCE_SEMAPHORE_MUTEX|在查詢等候系統滿足其執行緒保留要求時發生。 同步處理查詢編譯與記憶體授權要求時也會發生這種等候類型。|  
|RESOURCE_SEMAPHORE_QUERY_COMPILE|當並行查詢編譯數目達到調整流速上限時發生。 如果等候頻率很高且等候時間很長，可能表示編譯數目過多，或是計畫無法快取。|  
|RESOURCE_SEMAPHORE_SMALL_QUERY|在其他並行查詢導致小型查詢之記憶體要求無法立即取得授權時發生。 等候時間應該不會超過幾秒鐘，因為如果伺服器無法在幾秒鐘內授權使用要求的記憶體，它就會將要求傳送到主要查詢記憶體集區。 如果等候頻率很高，可能表示並行的小型查詢數目過多，而主要記憶體集區則受到等候中的查詢所封鎖。|  
|SE_REPL_CATCHUP_THROTTLE|當交易正在等候其中一個資料庫次要備援報告進度時發生。|  
|SE_REPL_COMMIT_ACK|當交易正在等候來自次要複本的仲裁認可收條時發生。|  
|SE_REPL_COMMIT_TURN|當交易接收到仲裁認可收條之後正在等候認可時發生。|  
|SE_REPL_ROLLBACK_ACK|當交易正在等候來自次要複本的仲裁回復收條時發生。|  
|SE_REPL_SLOW_SECONDARY_THROTTLE|當執行緒正在等候其中一個資料庫次要複本時發生。|  
|SEC_DROP_TEMP_KEY|在嘗試卸除暫時安全性金鑰失敗之後，再次重試之前發生。|  
|SECURITY_MUTEX|當系統等候控制可延伸金鑰管理 (EKM) 密碼編譯提供者之全域清單和 EKM 工作階段之工作階段範圍清單存取權的 Mutex 時發生。|  
|SEQUENTIAL_GUID|正在取得新的循序 GUID 時發生。|  
|SERVER_IDLE_CHECK|在資源監視器嘗試將 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體宣告為閒置或嘗試喚醒期間，同步處理 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體閒置狀態時發生。|  
|SHUTDOWN|在關機陳述式等候使用中的連接結束時發生。|  
|SLEEP_BPOOL_FLUSH|在檢查點調整流速發出新 I/O 之流速以避免磁碟子系統爆滿時發生。|  
|SLEEP_DBSTARTUP|在啟動資料庫期間等候所有資料庫復原時發生。|  
|SLEEP_DCOMSTARTUP|最多只在啟動 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體期間等候 DCOM 初始化完成時發生一次。|  
|SLEEP_MSDBSTARTUP|在 SQL 追蹤等候 msdb 資料庫啟動完成時發生。|  
|SLEEP_SYSTEMTASK|在啟動背景工作期間等候 tempdb 啟動完成時發生。|  
|SLEEP_TASK|當工作在等候一般事件發生期間進入休眠時發生。|  
|SLEEP_TEMPDBSTARTUP|在工作等候 tempdb 完成啟動時發生。|  
|SNI_CRITICAL_SECTION|在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 網路元件內執行內部同步處理時發生。|  
|SNI_HTTP_WAITFOR_0_DISCON|在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 關機期間等候未處理的 HTTP 連接結束時發生。|  
|SNI_LISTENER_ACCESS|等候非統一記憶體存取 (NUMA) 節點更新狀態變更時發生。 狀態變更的存取權是序列化的。|  
|SNI_TASK_COMPLETION|當系統在 NUMA 節點狀態變更期間等候所有工作完成時發生。|  
|SOAP_READ|在等候 HTTP 網路讀取動作完成時發生。|  
|SOAP_WRITE|在等候 HTTP 網路寫入動作完成時發生。|  
|SOS_CALLBACK_REMOVAL|在回撥清單上執行同步處理以移除回撥時發生。 在伺服器初始化完成之後，此計數器不應該變更。|  
|SOS_DISPATCHER_MUTEX|在發送器集區的內部同步處理期間發生。 這包括正在調整集區時。|  
|SOS_LOCALALLOCATORLIST|在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 記憶體管理員中執行內部同步處理時發生。|  
|SOS_MEMORY_USAGE_ADJUSTMENT|調整集區之間的記憶體使用量時發生。|  
|SOS_OBJECT_STORE_DESTROY_MUTEX|在記憶體集區中執行內部同步處理期間從集區終結物件時發生。|  
|SOS_PROCESS_AFFINITY_MUTEX|在同步處理程序相似性設定的存取作業時發生。|  
|SOS_RESERVEDMEMBLOCKLIST|在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 記憶體管理員中執行內部同步處理時發生。|  
|SOS_SCHEDULER_YIELD|在工作主動產生排程器以供其他工作執行時發生。 在這段等候期間，該工作將等候系統更新其配量。|  
|SOS_SMALL_PAGE_ALLOC|在配置和釋放某些記憶體物件所管理的記憶體期間發生。|  
|SOS_STACKSTORE_INIT_MUTEX|在同步處理內部存放區初始化時發生。|  
|SOS_SYNC_TASK_ENQUEUE_EVENT|在以同步的方式啟動工作時發生。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中大部分的工作都是以非同步方式啟動，使用這種方式時，將工作要求放在工作佇列上之後，控制權便會立即交還給啟動器。|  
|SOS_VIRTUALMEMORY_LOW|在記憶體配置等候資源管理員釋放虛擬記憶體時發生。|  
|SOSHOST_EVENT|在主控的元件 (例如 CLR) 等候 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 事件同步處理物件時發生。|  
|SOSHOST_INTERNAL|在同步處理主控元件 (例如 CLR) 使用的記憶體管理員回撥時發生。|  
|SOSHOST_MUTEX|在主控的元件 (例如 CLR) 等候 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Mutex 同步處理物件時發生。|  
|SOSHOST_RWLOCK|在主控的元件 (例如 CLR) 等候 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 讀取器-寫入器同步處理物件時發生。|  
|SOSHOST_SEMAPHORE|在主控的元件 (例如 CLR) 等候 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 信號同步處理物件時發生。|  
|SOSHOST_SLEEP|當主控的工作在等候一般事件發生期間進入休眠時發生。 主控的工作是供主控的元件 (例如 CLR) 使用。|  
|SOSHOST_TRACELOCK|在同步處理追蹤資料流的存取作業時發生。|  
|SOSHOST_WAITFORDONE|在主控的元件 (例如 CLR) 等候工作完成時發生。|  
|SQLCLR_APPDOMAIN|在 CLR 等候應用程式網域完成啟動時發生。|  
|SQLCLR_ASSEMBLY|在等候存取 appdomain 中已載入的組件清單時發生。|  
|SQLCLR_DEADLOCK_DETECTION|在 CLR 等候死結偵測完成時發生。|  
|SQLCLR_QUANTUM_PUNISHMENT|在 CLR 工作由於本身已超過其執行配量而進行調整流速時發生。 執行這項調整流速的目的是為了降低這個大量使用資源的工作對其他工作所造成的影響。|  
|SQLSORT_NORMMUTEX|在內部同步處理期間初始化內部排序結構時發生。|  
|SQLSORT_SORTMUTEX|在內部同步處理期間初始化內部排序結構時發生。|  
|SQLTRACE_BUFFER_FLUSH|當工作在等候背景工作每四秒將追蹤緩衝區排清到磁碟時發生。|  
|SQLTRACE_LOCK|在檔案追蹤期間同步處理追蹤緩衝區時發生。|  
|SQLTRACE_SHUTDOWN|在追蹤關機等候未處理的追蹤事件完成時發生。|  
|SQLTRACE_WAIT_ENTRIES|在 SQL 追蹤事件佇列等候封包到達佇列時發生。|  
|SRVPROC_SHUTDOWN|在關機處理序等候內部資源釋出以完整關機時發生。|  
|TEMPOBJ|在已同步處理暫存物件卸除時發生。 這種等候很罕見，只有在工作已要求 temp 資料表卸除的獨佔存取權時才會發生。|  
|THREADPOOL|當工作在等候工作者以供其執行時發生。 這可能表示工作者設定上限太低，或者批次執行花費的時間超乎尋常地多，因而減少了可以滿足其他批次的可用工作者數目。|  
|TIMEPRIV_TIMEPERIOD|在「擴充事件」計時器的內部同步處理期間發生。|  
|TRACEWRITE|在 SQL 追蹤資料列集追蹤提供者等候可用緩衝區或具有待處理事件之緩衝區時發生。|  
|TRAN_MARKLATCH_DT|在交易標示閂鎖上等候終結模式閂鎖時發生。 交易標示閂鎖可用來同步處理認可與已標示交易。|  
|TRAN_MARKLATCH_EX|在已標示交易上等候獨佔模式閂鎖時發生。 交易標示閂鎖可用來同步處理認可與已標示交易。|  
|TRAN_MARKLATCH_KP|在已標示交易上等候保留模式閂鎖時發生。 交易標示閂鎖可用來同步處理認可與已標示交易。|  
|TRAN_MARKLATCH_NL|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|TRAN_MARKLATCH_SH|在已標示交易上等候共用模式閂鎖時發生。 交易標示閂鎖可用來同步處理認可與已標示交易。|  
|TRAN_MARKLATCH_UP|在已標示交易上等候更新模式閂鎖時發生。 交易標示閂鎖可用來同步處理認可與已標示交易。|  
|TRANSACTION_MUTEX|在同步處理多個批次對交易的存取作業時發生。|  
|UTIL_PAGE_ALLOC|在交易記錄掃描在記憶體壓力下等候記憶體變成可供使用的狀態時發生。|  
|VIA_ACCEPT|當虛擬介面卡 (VIA) 提供者連接在啟動期間完成時發生。|  
|VIEW_DEFINITION_MUTEX|在同步處理快取檢視定義的存取作業時發生。|  
|WAIT_FOR_RESULTS|在等候查詢通知被觸發時發生。|  
|WAITFOR|因為 WAITFOR [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式而發生。 等候的持續時間由陳述式的參數決定。 這是使用者起始的等候。|  
|WAITFOR_TASKSHUTDOWN|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|WAITSTAT_MUTEX|在同步處理用來擴展 sys.dm_os_wait_stats 之統計資料集合的存取作業時發生。|  
|WCC|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|WORKTBL_DROP|在工作資料表卸除失敗之後，於重試之前暫停時發生。|  
|WRITE_COMPLETION|當寫入作業正在進行時發生。|  
|WRITELOG|在等候記錄排清完成時發生。 造成記錄排清的一般作業包括檢查點和交易認可。|  
|XACT_OWN_TRANSACTION|在等候取得交易的擁有權時發生。|  
|XACT_RECLAIM_SESSION|在等候工作階段目前的擁有者釋出工作階段擁有權時發生。|  
|XACTLOCKINFO|在同步處理交易鎖定清單的存取作業時發生。 除了交易本身之外，諸如死結偵測和鎖定移轉等作業也會在頁面分割時存取鎖定清單。|  
|XACTWORKSPACE_MUTEX|在同步處理從交易脫離的作業，以及交易之編列成員之間的資料庫鎖定數目時發生。|  
|XE_BUFFERMGR_ALLPROCESSED_EVENT|當擴充的事件工作階段緩衝區排清至目標時發生。 這項等候發生在背景執行緒中。|  
|XE_BUFFERMGR_FREEBUF_EVENT|當下列其中一個條件成立時發生：<br /><br /> 擴充的事件工作階段會設定無事件遺失，而且此工作階段中的所有緩衝區目前已滿。 這表示緩衝區對於擴充的事件工作階段而言太小，或是應該分割。<br /><br /> 稽核遇到延遲。 這表示寫入稽核的磁碟機上發生磁碟瓶頸。|  
|XE_DISPATCHER_CONFIG_SESSION_LIST|當使用非同步目標的擴充的事件工作階段啟動或停止時發生。 這個等候表示下列其中一個狀況：<br /><br /> 擴充的事件工作階段正在向背景執行緒集區註冊。<br /><br /> 背景執行緒集區正在根據目前的負載計算所需的執行緒數目。|  
|XE_DISPATCHER_JOIN|當用於擴充的事件工作階段的背景執行緒結束時發生。|  
|XE_DISPATCHER_WAIT|當用於擴充的事件工作階段的背景執行緒正在等候事件緩衝區處理時發生。|  
|XE_MODULEMGR_SYNC|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|XE_OLS_LOCK|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|XE_PACKAGE_LOCK_BACKOFF|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|FT_COMPROWSET_RWLOCK|全文檢索正在等候片段中繼資料作業。 記載僅供參考之用。 不支援。 我們無法保證未來的相容性。|  
|FT_IFTS_RWLOCK|全文檢索正在等候內部的同步處理。 記載僅供參考之用。 不支援。 我們無法保證未來的相容性。|  
|FT_IFTS_SCHEDULER_IDLE_WAIT|全文檢索排程器睡眠等候類型。 排程器閒置中。|  
|FT_IFTSHC_MUTEX|全文檢索正在等候 fdhost 控制項作業。 記載僅供參考之用。 不支援。 我們無法保證未來的相容性。|  
|FT_IFTSISM_MUTEX|全文檢索正在等候通訊作業。 記載僅供參考之用。 不支援。 我們無法保證未來的相容性。|  
|FT_MASTER_MERGE|全文檢索正在等候主要合併作業。 記載僅供參考之用。 不支援。 我們無法保證未來的相容性。|  
  
  
