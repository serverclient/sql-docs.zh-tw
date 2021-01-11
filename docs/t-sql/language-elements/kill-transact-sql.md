---
description: KILL (Transact-SQL)
title: KILL (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 08/31/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- KILL_TSQL
- KILL
dev_langs:
- TSQL
helpviewer_keywords:
- WITH STATUSONLY option
- terminating distributed transactions
- distributed transactions [SQL Server], terminating
- in-doubt transactions
- IDs [SQL Server], user processes
- ending distributed transactions [SQL Server]
- stopping distributed transactions
- session IDs [SQL Server]
- system process termination [SQL Server]
- stopping processes
- ending processes [SQL Server]
- UOW [SQL Server]
- orphaned transactions [SQL Server]
- unit of work [SQL Server]
- process termination [SQL Server]
- KILL statement
- terminating process
ms.assetid: 071cf260-c794-4b45-adc0-0e64097938c0
author: rothja
ms.author: jroth
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 13afe3aa357bfd968874ae1f89bf0720c39fe49c
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97644451"
---
# <a name="kill-transact-sql"></a>KILL (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

根據工作階段識別碼或工作單位 (UOW) 來結束使用者處理序。 如果指定的工作階段識別碼或 UOW 有許多工作要復原，KILL 陳述式可能需要花一些時間來完成。 特別是當牽涉到復原較長的交易時，處理序需要花較長時間來完成。  
  
KILL 會結束正常的連線，這樣就可在內部停止與指定之工作階段識別碼建立關聯的交易。 有些時候 [!INCLUDE[msCoName](../../includes/msconame-md.md)] 分散式交易協調器 (MS DTC) 可能處於使用中。 如果 MS DTC 處於使用中，您也可以使用此陳述式來結束孤立和不確定的分散式交易。  
  
![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql  
-- Syntax for SQL Server  
  
KILL { session ID [ WITH STATUSONLY ] | UOW [ WITH STATUSONLY | COMMIT | ROLLBACK ] }    

```  
  
```syntaxsql  
-- Syntax for Azure Synapse Analytics and Parallel Data Warehouse  
  
KILL 'session_id'  
[;]   
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數

_session ID_   
這是要結束之處理序的工作階段識別碼。 `session_id` 是在建立連線時，指派給每個使用者連線的唯一整數 (**int**)。 在連接持續時間，工作階段識別碼值會繫結連接。 當連接結束時，會釋出這個整數值，它可以重新指派給新的連接。  

下列查詢可協助識別您想要終止的 `session_id`：  

 ```sql  
 SELECT conn.session_id, host_name, program_name,
     nt_domain, login_name, connect_time, last_request_end_time 
FROM sys.dm_exec_sessions AS sess
JOIN sys.dm_exec_connections AS conn
    ON sess.session_id = conn.session_id;

```  


_UOW_   
識別分散式交易的工作單位識別碼 (UOW)。 _UOW_ 是可以從 `sys.dm_tran_locks` 動態管理檢視的 request_owner_guid 資料行取得的 GUID。 您也可以從錯誤記錄檔或透過 MS DTC 監視器來取得 _UOW_。 如需有關監視分散式交易的詳細資訊，請參閱 MS DTC 文件集。  
  
使用 KILL \<UOW> 來停止尚未解決的分散式交易。 這些交易並未與任何實際工作階段識別碼建立關聯；反之，是以人工方式與 session ID = '-2' 建立關聯。 此工作階段識別碼可讓您更輕鬆地識別尚未解決的交易，方法是透過在 `sys.dm_tran_locks`、` sys.dm_exec_sessions` 或 `sys.dm_exec_requests` 動態管理檢視中查詢 session ID 資料行。  

_WITH STATUSONLY_   
用來針對因先前 KILL 陳述式而導致復原的指定 _UOW_ 或 `session_id` 產生進度報告。 KILL WITH STATUSONLY 並不會結束或復原 UOW 或工作階段識別碼。 此命令只會顯示目前的復原進度。

_WITH COMMIT_   
用來終止具有認可且尚未解決的分散式交易。 僅適用於分散式交易，您必須指定 _UOW_ 才能使用此選項。  如需詳細資訊，請參閱[分散式交易](../../database-engine/availability-groups/windows/configure-availability-group-for-distributed-transactions.md#manage-unresolved-transactions)。

_WITH ROLLBACK_   
用來終止具有復原且尚未解決的分散式交易。 僅適用於分散式交易，您必須指定 _UOW_ 才能使用此選項。  如需詳細資訊，請參閱[分散式交易](../../database-engine/availability-groups/windows/configure-availability-group-for-distributed-transactions.md#manage-unresolved-transactions)。


  
## <a name="remarks"></a>備註  
KILL 通常用於結束會使用鎖定來封鎖其他重要處理序的處理序。 KILL 也可用來停止正在執行必要系統資源之查詢的處理序。 系統處理序和執行擴充預存程序的處理序無法結束。  
  
請謹慎使用 KILL，特別是當重要處理序正在執行時。 您無法終止自己的處理序。 您也不應終止下列處理序：  
  
-   AWAITING COMMAND  
-   CHECKPOINT SLEEP  
-   LAZY WRITER  
-   LOCK MONITOR  
-   SIGNAL HANDLER  
  
利用 @@SPID 來顯示目前工作階段的工作階段識別碼值。  
  
若要取得使用中工作階段識別碼值的報告，請查詢 sys.dm_tran_locks、sys.dm_exec_sessions 以及 sys.dm_exec_requests 動態管理檢視中的 session_id 資料行。 您也可以檢視由 sp_who 系統預存程序傳回的 SPID 資料行。 如果正在進行特定 SPID 的復原，sp_who 結果集中適用於該 SPID 的 cmd 資料行就會指出 KILLED/ROLLBACK。  
  
當特定連接對資料庫資源有鎖定並封鎖另一個連接的進度時，封鎖連接的工作階段識別碼會出現在 `blocking_session_id` 的 `sys.dm_exec_requests` 資料行或是由 `blk` 傳回的 `sp_who` 資料行中。  
  
KILL 命令可用來解決不確定的分散式交易。 這些交易是未解決的分散式交易，之所以會發生是因為資料庫伺服器或 MS DTC 協調器發生了非計畫的重新啟動。 如需未決交易的詳細資訊，請參閱[使用標示的交易以一致的方式復原相關資料庫 &#40;完整復原模式&#41;](../../relational-databases/backup-restore/use-marked-transactions-to-recover-related-databases-consistently.md)中的＜兩階段交易認可＞一節。  
  
## <a name="using-with-statusonly"></a>使用 WITH STATUSONLY  
如果因先前的 KILL _session ID_|_UOW_ 陳述式導致工作階段識別碼或 UOW 復原，則 KILL WITH STATUSONLY 會產生報告。 進度報告會指出已完成的復原量 (百分比) 及估計的剩下時間長度 (以秒為單位)。 報表會以下列形式將其指出：  
  
`Spid|UOW <xxx>: Transaction rollback in progress. Estimated rollback completion: <yy>% Estimated time left: <zz> seconds`  
  
如果在執行 KILL _session ID_|_UOW_ WITH STATUSONLY 陳述式前，工作階段識別碼或 UOW 的復原已經完成，KILL _session ID_|_UOW_ WITH STATUSONLY 便會傳回下列錯誤：  
  
```
"Msg 6120, Level 16, State 1, Line 1"  
"Status report cannot be obtained. Rollback operation for Process ID <session ID> is not in progress."
```  
如果不存在正在復原的工作階段識別碼或 UOW，也會發生此錯誤


您可以重複使用不含 WITH STATUSONLY 選項的相同 KILL _session ID_|_UOW_ 陳述式來取得相同狀態報告。 不過，不建議您透過此方式重複該選項。 如果復原已完成，且在執行新的 KILL 陳述式之前已將工作階段識別碼重新指派給新工作，則如果您重複 KILL _session ID_ 陳述式，新處理序可能會停止。 指定 WITH STATUSONLY 以避免新處理序停止。  
  
## <a name="permissions"></a>權限  
**[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]：** 需要 ALTER ANY CONNECTION 權限。 ALTER ANY CONNECTION 隨附在系統管理員 (sysadmin) 或處理序管理員 (processadmin) 固定伺服器角色的成員資格中。  
  
**[!INCLUDE[ssSDS](../../includes/sssds-md.md)]：** 需要 KILL DATABASE CONNECTION 權限。 伺服器層級主體登入具備 KILL DATABASE CONNECTION。  
  
## <a name="examples"></a>範例  
  
### <a name="a-using-kill-to-stop-a-session"></a>A. 使用 KILL 來停止工作階段  
 下列範例示範如何停止工作階段識別碼 `53`。  
  
```sql  
KILL 53;  
GO  
```  
  
### <a name="b-using-kill-session-id-with-statusonly-to-obtain-a-progress-report"></a>B. 使用 KILL 工作階段識別碼 WITH STATUSONLY 來取得進度報告  
下列範例會產生特定工作階段識別碼的回復處理序狀態。  
  
```sql  
KILL 54;  
KILL 54 WITH STATUSONLY;  
GO  
  
--This is the progress report.  
spid 54: Transaction rollback in progress. Estimated rollback completion: 80% Estimated time left: 10 seconds.  
```  
  
### <a name="c-using-kill-to-stop-an-orphaned-distributed-transaction"></a>C. 使用 KILL 來停止孤立的分散式交易  
下列範例示範如何停止 *UOW* 為 `D5499C66-E398-45CA-BF7E-DC9C194B48CF` 的孤立分散式交易 (工作階段識別碼 = -2)。  
  
```sql  
KILL 'D5499C66-E398-45CA-BF7E-DC9C194B48CF';  
```  

  
## <a name="see-also"></a>另請參閱  
[KILL STATS JOB &#40;Transact-SQL&#41;](../../t-sql/language-elements/kill-stats-job-transact-sql.md)   
[KILL QUERY NOTIFICATION SUBSCRIPTION &#40;Transact-SQL&#41;](../../t-sql/language-elements/kill-query-notification-subscription-transact-sql.md)   
[內建函數 &#40;Transact-SQL&#41;](~/t-sql/functions/functions.md)   
[SHUTDOWN &#40;Transact-SQL&#41;](../../t-sql/language-elements/shutdown-transact-sql.md)   
[@@SPID &#40;Transact-SQL&#41;](../../t-sql/functions/spid-transact-sql.md)   
[sys.dm_exec_requests &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql.md)   
[sys.dm_exec_sessions &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql.md)   
[sys.dm_tran_locks &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql.md)   
[sp_lock &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-lock-transact-sql.md)   
[sp_who &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-who-transact-sql.md)  
  
  
