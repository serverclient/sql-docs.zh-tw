---
title: 疑難排解：可用性群組超過 RTO (SQL Server) | Microsoft Docs
description: 了解如何在容錯移轉所花費的時間超過在 SQL Server 中的復原時間目標時，針對 Always On 可用性群組上的容錯移轉進行疑難排解。
ms.custom: ag-guide
ms.date: 06/13/2017
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: conceptual
ms.assetid: e83e4ef8-92f0-406f-bd0b-dc48dc210517
author: rothja
ms.author: jroth
ms.openlocfilehash: 5847162edb0d962826931624f4d14a3d78468066
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97641874"
---
# <a name="troubleshoot-availability-group-exceeded-rto"></a>疑難排解：可用性群組已超過 RTO
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]
  在可用性群組上進行自動容錯移轉或規劃的手動容錯移轉之後沒有資料遺失，您可能會發現容錯移轉時間超過您的復原時間目標 (RTO)。 或者，當您在使用[監視 Always On 可用性群組的效能](monitor-performance-for-always-on-availability-groups.md)中的方法評估同步認可次要複本的容錯移轉時間時，發現它超過您的 RTO。  
  
 如果您的自動容錯移轉仍未完成，請參閱[針對 SQL Server 2012 Always On 環境中的自動容錯移轉問題進行疑難排解](https://support.microsoft.com/kb/2833707) \(機器翻譯\)。  
  
 下節說明造成容錯移轉的時間超過 RTO 的常見原因。  
  
1.  [報告工作負載封鎖重做執行緒因而無法執行](#BKMK_REDOBLOCK)  
  
2.  [重做執行緒因資源競爭而落後](#BKMK_CONTENTION)  
  
##  <a name="reporting-workload-blocks-the-redo-thread-from-running"></a><a name="BKMK_REDOBLOCK"></a> 報告工作負載封鎖重做執行緒因而無法執行  
 在次要複本上的重做執行緒被長時間執行的唯讀查詢封鎖，因而無法進行資料定義語言 (DDL) 變更。  
  
### <a name="explanation"></a>說明  
 在次要複本上，唯讀查詢取得結構描述穩定性 (`Sch-S`) 鎖定。 這些 `Sch-S` 鎖定可以封鎖重做執行緒，使它無法取得結構描述修改 (`Sch-M`) 鎖定以進行任何 DDL 變更。 除非解除重做執行緒的封鎖，否則被封鎖的重做執行緒將無法套用任何記錄檔記錄。 一旦解除封鎖，它就能繼續跟上記錄的結尾，並允許後續的復原和容錯移轉流程繼續處理。  
  
### <a name="diagnosis-and-resolution"></a>診斷和解決方案  
 當重做執行緒被封鎖時，會產生稱為 `sqlserver.lock_redo_blocked` 的擴充事件。 此外，您可以查詢次要複本上的 DMV sys.dm_exec_request，以找出封鎖重做執行緒的工作階段，然後採取修正措施。 以下查詢傳回唯讀查詢的工作階段識別碼，而此唯讀查詢正在封鎖重做執行緒。  
  
```sql  
select session_id, command, blocking_session_id, wait_time, wait_type, wait_resource   
from sys.dm_exec_requests where command = 'DB STARTUP'  
```  
  
 您可以讓報告工作負載完成，完成之後重做執行緒便會解除封鎖。或者，您可以對造成封鎖的工作階段識別碼執行 [KILL &#40;Transact-SQL&#41;](~/t-sql/language-elements/kill-transact-sql.md) 命令，以立即將重做執行緒解除封鎖。  
  
##  <a name="redo-thread-falls-behind-due-to-resource-contention"></a><a name="BKMK_CONTENTION"></a> 重做執行緒因資源競爭而落後  
 次要複本上的大型報告工作負載造成次要複本的效能緩慢，且重做執行緒已經落後。  
  
### <a name="explanation"></a>說明  
 當在次要複本上套用記錄檔記錄時，重做執行緒會從記錄檔磁碟讀取記錄檔記錄，然後它會針對每個記錄檔記錄存取資料分頁以套用記錄檔記錄。 如果分頁尚未在緩衝集區中，分頁存取可能會具有 I/O 界限 (存取實體磁碟)。 如果有具有 I/O 界限的報告工作負載，報告工作負載會與重做執行緒競爭取得 I/O 資源，因此可能使重做執行緒緩慢。  
  
### <a name="diagnosis-and-resolution"></a>診斷和解決方案  
 您可以使用以下的 DMV 查詢，藉由測量 `last_redone_lsn` 和 `last_received_lsn` 之間的差距，來查看重做執行緒落後的程度。  
  
```sql  
select recovery_lsn, truncation_lsn, last_hardened_lsn, last_received_lsn,   
   last_redone_lsn, last_redone_time  
from sys.dm_hadr_database_replica_states  
  
```  
  
 如果重做執行緒確實落後，則您需要調查次要複本效能降低的根本原因。 如果和報告工作負載有 I/O 競爭，您可以使用 [Resource Governor](~/relational-databases/resource-governor/resource-governor.md) 來控制報告工作負載使用的 CPU 週期，進而間接地在某種程度上控制 I/O 週期。 例如，如果您的報告工作負載取用百分之 10 的 CPU，但它是以 I/O 為主的工作負載，則您可以使用 Resource Governor 將 CPU 資源使用量限制為百分之 5，以限制讀取工作負載，進而降低對 I/O 的影響。  
  
## <a name="next-steps"></a>後續步驟  
 [對 SQL Server 中的效能問題進行疑難排解 (適用於 SQL Server 2012)](/previous-versions/sql/sql-server-2008/dd672789(v=sql.100)) \(英文\)  
  
