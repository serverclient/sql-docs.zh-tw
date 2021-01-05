---
title: 變更無法顯示於次要可用性群組複本
description: 了解如何判斷為何在主要複本上變更不會反映在 Always On 可用性群組的次要複本上。
ms.custom: seo-lt-2019
ms.date: 06/13/2017
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: conceptual
ms.assetid: c602fd39-db93-4717-8f3a-5a98b940f9cc
author: cawrites
ms.author: chadam
ms.openlocfilehash: 8bc95e935d030c792fa4a239fa722367d1f2f1db
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97641864"
---
# <a name="determine-why-changes-from-primary-replica-are-not-reflected-on-secondary-replica-for-an-always-on-availability-group"></a>判斷為什麼來自主要複本的變更不會反映在 Always On 可用性群組次要複本上
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]
  用戶端應用程式在主要複本上成功完成更新，但是查詢次要複本卻顯示未反映變更。 此案例假設您的可用性有健康情況良好的同步處理狀態。 在大部分情況下，此行為會在幾分鐘後自行解決。  
  
 如果變更在幾分鐘後仍未反映在次要複本上，表示同步處理工作程序中可能有瓶頸。 造成瓶頸的位置取決於次要複本是設定為同步認可或非同步認可。  
  
 **同步認可**  
  
 在主要複本上每個成功的更新都已同步處理至次要複本，或是次要複本上的記錄檔記錄已排清以進行強化。 因此，瓶頸應該是位於在次要複本上的記錄排清之後發生的重做程序中。  
  
 不過，一旦趕上重做，次要複本上的所有讀取工作負載都是快照集隔離：  
  
  -   在主要複本上長時間執行的交易  
  
  -   在次要複本上的重做  


**非同步認可**  
 
 由於非同步認可會在排清到本機磁碟後就認可交易，所以瓶頸可能會在該點之後的任何位置：  
 
  -   在主要複本上長時間執行的交易  
  
  -   網路延遲或輸送量  
  
  -   在次要複本上的記錄檔強化  
  
  -   在次要複本上的重做  


下列各節說明對主要複本的變更，在唯讀查詢時未反映在次要複本上的常見原因。  


##  <a name="long-running-active-transactions"></a><a name="BKMK_OLDTRANS"></a> 長時間執行的使用中交易  
 在主要複本上長時間執行的交易會阻止在次要複本上讀取更新。  
  
### <a name="explanation"></a>說明  
 次要複本上的所有讀取工作負載都是快照集隔離查詢。 在快照集隔離中，唯讀用戶端會在次要複本上看到可用性資料庫中，位於重做完成的記錄檔中最舊的使用中交易起始點。 如果交易經過幾小時都未認可，開啟的交易會阻止所有唯讀查詢看到任何新的更新。  
  
### <a name="diagnosis-and-resolution"></a>診斷和解決方案  
 在主要複本上，使用 [DBCC OPENTRAN &#40;Transact-SQL&#41;](~/t-sql/database-console-commands/dbcc-opentran-transact-sql.md) 檢視最舊的使用中交易，並查看交易是否可以復原。 一旦最舊的使用中交易復原，並同步處理至次要複本，在次要複本上的讀取工作負載就可以看到可用性資料庫中，從當時到最舊的使用中交易起始點的更新。  
  
##  <a name="high-network-latency-or-low-network-throughput-causes-log-build-up-on-the-primary-replica"></a><a name="BKMK_LATENCY"></a> 高度網路延遲或低網路輸送量會造成主要複本上的記錄累積  
 高度網路延遲或低輸送量可能會讓記錄以不夠快的速度傳送至次要複本。  
  
### <a name="explanation"></a>說明  
 主要複本在傳送記錄時，若記錄超過傳送至次要複本的未認可訊息最大允許數目，就會啟動流量控制。 一直要到其中某些訊息已認可，沒有更多記錄區塊可傳送至次要複本為止。 這種情況可能會讓您嚴重遺失資料，並危及您的復原點目標 (RPO)。  
  
### <a name="diagnosis-and-resolution"></a>診斷和解決方案  
 較高的 DMV 值 [log_send_queue_size](~/relational-databases/system-dynamic-management-views/sys-dm-hadr-database-replica-states-transact-sql.md) 可以指出在主要複本上正被阻擋住的記錄數。 將此值除以 [log_send_rate](~/relational-databases/system-dynamic-management-views/sys-dm-hadr-database-replica-states-transact-sql.md) 可以讓您粗略估計次要複本上的資料在多久後可以跟上。  
  
 此外，檢查兩個效能物件 [SQL Server:Availability Replica > Flow Control Time (ms/sec)](~/relational-databases/performance-monitor/sql-server-availability-replica.md) 和 [SQL Server:Availability Replica > Flow control/sec](~/relational-databases/performance-monitor/sql-server-availability-replica.md) 會很有用。乘以這兩個值可以提供您在最後一刻等候流量控制清除花費了多少時間。 流量控制等候的時間越長，傳送速率越低。  
  
 以下是能夠用來診斷網路延遲和輸送量的計量清單。 您可以使用其他 Windows 工具，例如 **ping.exe** 評估網路使用率。  
  
-   DMV [log_send_queue_size](~/relational-databases/system-dynamic-management-views/sys-dm-hadr-database-replica-states-transact-sql.md)  
  
-   DMV [log_send_rate](~/relational-databases/system-dynamic-management-views/sys-dm-hadr-database-replica-states-transact-sql.md)  
  
-   效能計數器 `SQL Server:Database > Log Bytes Flushed/sec`  
  
-   效能計數器 `SQL Server:Database Mirroring > Send/Receive Ack Time`  
  
-   效能計數器 `SQL Server:Availability Replica > Bytes Sent to Replica/sec`  
  
-   效能計數器 `SQL Server:Availability Replica > Bytes Sent to Transport/sec`  
  
-   效能計數器 `SQL Server:Availability Replica > Flow Control Time (ms/sec)`  
  
-   效能計數器 `SQL Server:Availability Replica > Flow Control/sec`  
  
-   效能計數器 `SQL Server:Availability Replica > Resent Messages/sec`  
  
 若要解決此問題，請嘗試升級您的網路頻寬，或者移除或減少不必要的網路流量。  
  
##  <a name="another-reporting-workload-blocks-the-redo-thread-from-running"></a><a name="BKMK_REDOBLOCK"></a> 另一個報告工作負載封鎖重做執行緒執行  
 在次要複本上的重做執行緒被長時間執行的唯讀查詢封鎖，因而無法進行資料定義語言 (DDL) 變更。 重做執行緒必須先解除封鎖，才能進一步進行更新供讀取工作負載使用。  
  
### <a name="explanation"></a>說明  
 在次要複本上，唯讀查詢取得結構描述穩定性 (`Sch-S`) 鎖定。 這些 `Sch-S` 鎖定可以封鎖重做執行緒，使它無法取得結構描述修改 (`Sch-M`) 鎖定以進行任何 DDL 變更。 除非解除重做執行緒的封鎖，否則被封鎖的重做執行緒將無法套用任何記錄檔記錄。  
  
### <a name="diagnosis-and-resolution"></a>診斷和解決方案  
 當重做執行緒被封鎖時，會產生稱為 `sqlserver.lock_redo_blocked` 的擴充事件。 此外，您可以查詢次要複本上的 DMV sys.dm_exec_request，以找出封鎖重做執行緒的工作階段，然後採取修正措施。 以下查詢傳回報告工作負載的工作階段識別碼，而此報告工作負載正在封鎖重做執行緒。  
  
```sql  
select session_id, command, blocking_session_id, wait_time, wait_type, wait_resource   
from sys.dm_exec_requests where command = 'DB STARTUP'  
```  
  
 您可以讓報告工作負載完成，完成之後重做執行緒便會解除封鎖。或者，您可以對造成封鎖的工作階段識別碼執行 [KILL &#40;Transact-SQL&#41;](~/t-sql/language-elements/kill-transact-sql.md) 命令，以立即將重做執行緒解除封鎖。  
  
##  <a name="redo-thread-falls-behind-due-to-resource-contention"></a><a name="BKMK_REDOBEHIND"></a> 重做執行緒因資源競爭而落後  
 次要複本上的大型報告工作負載造成次要複本的效能緩慢，且重做執行緒已經落後。  
  
### <a name="explanation"></a>說明  
 當在次要複本上套用記錄檔記錄時，重做執行緒會從記錄檔磁碟讀取記錄檔記錄，然後它會針對每個記錄檔記錄存取資料分頁以套用記錄檔記錄。 如果分頁尚未在緩衝集區中，分頁存取可能會具有 I/O 界限 (存取實體磁碟)。 如果有具有 I/O 界限的報告工作負載，報告工作負載會與重做執行緒競爭取得 I/O 資源，因此可能使重做執行緒緩慢。 這種情況不只會影響其他報告工作負載無法看到更新的資料，也會影響 RTO。  
  
### <a name="diagnosis-and-resolution"></a>診斷和解決方案  
 您可以使用以下的 DMV 查詢，藉由測量 `last_redone_lsn` 和 `last_received_lsn` 之間的差距，來查看重做執行緒落後的程度。  
  
```sql  
select recovery_lsn, truncation_lsn, last_hardened_lsn, last_received_lsn,   
   last_redone_lsn, last_redone_time  
from sys.dm_hadr_database_replica_states  
  
```  
  
 如果重做執行緒確實落後，則您需要調查次要複本效能降低的根本原因。 如果和報告工作負載有 I/O 競爭，您可以使用 [Resource Governor](~/relational-databases/resource-governor/resource-governor.md) 來控制報告工作負載使用的 CPU 週期，進而間接地在某種程度上控制 I/O 週期。 例如，如果您的報告工作負載取用百分之 10 的 CPU，但它是以 I/O 為主的工作負載，則您可以使用 Resource Governor 將 CPU 資源使用量限制為百分之 5，以限制讀取工作負載，進而降低對 I/O 的影響。  
  
## <a name="next-steps"></a>後續步驟  
 [對 SQL Server 2008 中的效能問題進行疑難排解](/previous-versions/sql/sql-server-2008/dd672789(v=sql.100)) \(英文\)
  
