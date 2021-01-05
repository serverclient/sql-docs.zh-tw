---
title: 可用性群組已超過 RPO
description: Always On 可用性群組超過復原點目標 (RPO) 時的常見問題和解決方式
ms.custom: seo-lt-2019
ms.date: 06/13/2017
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: conceptual
ms.assetid: 38de1841-9c99-435a-998d-df81c7ca0f1e
author: rothja
ms.author: jroth
ms.openlocfilehash: 34ac1e35b83158aec9aa2bae6cdd9754078212bb
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97642917"
---
# <a name="troubleshoot-availability-group-exceeded-rpo"></a>疑難排解：可用性群組已超過 RPO
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]
  在可用性群組上強制手動容錯移轉至非同步認可次要複本之後，您可能會發現資料遺失比您的復原點目標 (RPO) 還多。 或者，當您使用[監視 Always On 可用性群組的效能](monitor-performance-for-always-on-availability-groups.md)中的方法計算非同步認可次要複本的潛在資料遺失時，會發現它超過您的 RPO。  
  
 同步認可次要複本可確保資料零遺失，但是非同步認可次要複本的潛在資料遺失則取決於記錄檔在次要複本上仍在等候進行強化的程度。  
  
 下列各節說明非同步認可次要複本有高度潛在資料遺失的常見原因，假設您的伺服器執行個體中並沒有與可用性群組無關的系統性效能問題。  
  
1.  [高度網路延遲或低網路輸送量會造成主要複本上的記錄累積](#BKMK_LATENCY)  
  
2.  [磁碟 I/O 瓶頸會降低次要複本上的記錄強化](#BKMK_IO_BOTTLENECK)  
  
##  <a name="high-network-latency-or-low-network-throughput-causes-log-build-up-on-the-primary-replica"></a><a name="BKMK_LATENCY"></a> 高度網路延遲或低網路輸送量會造成主要複本上的記錄累積  
 資料庫超出其 RPO 的最常見原因是無法以夠快的速度傳送至次要複本。  
  
### <a name="explanation"></a>說明  
 主要複本在傳送記錄時，若記錄超過傳送至次要複本的未認可訊息最大允許數目，就會啟動流量控制。 一直要到其中某些訊息已認可，沒有更多記錄區塊可傳送至次要複本為止。 因為只有在次要複本上強化記錄區塊之後才能避免資料遺失，所以累積未傳送的記錄訊息會增加潛在的資料遺失。  
  
### <a name="diagnosis-and-resolution"></a>診斷和解決方案  
 有大量的訊息重新傳送至次要複本，可能表示有高度的網路延遲和網路雜訊。 您也可以將 DMV 值 **log_send_rate** 與效能物件 Log Bytes Flushed/sec 比較。如果記錄檔排清到磁碟的速度比傳送的更快，潛在資料遺失就會無限制地增加。  
  
 此外，檢查兩個效能物件 `SQL Server:Availability Replica > Flow Control Time (ms/sec)` 和 `SQL Server:Availability Replica > Flow Control/sec` 也很有用。 乘以這兩個值可以提供您在最後一刻等候流量控制清除花費了多少時間。 流量控制等候的時間越長，傳送速率越低。  
  
 下列計量對於診斷網路延遲和輸送量很有用。 您可以使用其他 Windows 工具，例如 **ping.exe** 和 [網路監視器](https://www.microsoft.com/p/network-monitor-pro-free-edition/9n8gdvj32gp7)評估延遲和網路使用率。  
  
-   DMV `sys.dm_hadr_database_replica_states, log_send_queue_size`  
  
-   DMV `sys.dm_hadr_database_replica_states, log_send_rate`  
  
-   效能計數器 `SQL Server:Database > Log Bytes Flushed/sec`  
  
-   效能計數器 `SQL Server:Database Mirroring > Send/Receive Ack Time`  
  
-   效能計數器 `SQL Server:Availability Replica > Bytes Sent to Replica/sec`  
  
-   效能計數器 `SQL Server:Availability Replica > Bytes Sent to Transport/sec`  
  
-   效能計數器 `SQL Server:Availability Replica > Flow Control Time (ms/sec)`  
  
-   效能計數器 `SQL Server:Availability Replica > Flow Control/sec`  
  
-   效能計數器 `SQL Server:Availability Replica > Resent Messages/sec`  

若要解決此問題，請嘗試升級您的網路頻寬，或者移除或減少不必要的網路流量。  


##  <a name="disk-io-bottleneck-slows-down-log-hardening-on-the-secondary-replica"></a><a name="BKMK_IO_BOTTLENECK"></a> 磁碟 I/O 瓶頸會降低次要複本上的記錄強化  
 根據資料庫檔案部署，記錄強化可能會因為報告工作負載的 I/O 競爭而變慢。  
  
### <a name="explanation"></a>說明  
 一旦記錄區塊在記錄檔上經過強化，就能防止資料遺失。 因此，請務必將記錄檔與資料檔案隔離。 如果記錄檔和資料檔案都對應到相同的硬碟，報告大量讀取資料檔案的工作負載會耗用與記錄強化作業所需的 I/O 資源相同。 慢速的記錄強化可能會轉譯為慢速認可至主要複本，這可能會導致過度啟動流量控制，以及流量控制的等候時間很長。  
  
### <a name="diagnosis-and-resolution"></a>診斷和解決方案  
 如果您確認網路不屬於高度延遲或低輸送量的狀況，則應該調查次要複本是否有 I/O 競爭情形。 [SQL Server：盡可能降低磁碟 I/O](/previous-versions/technet-magazine/jj643251(v=msdn.10))中的查詢對於識別競爭很有幫助。 為了方便起見，該文章的範例衍生如下。  
  
 下列指令碼可讓您查看在 SQL Server 執行個體上執行的每個可用性資料庫，在每個資料檔案和記錄檔的讀取數目和寫入數目。 它會依照平均的 I/O 拖延時間 (毫秒) 排序。 請注意，數字是從上次啟動伺服器執行個體的時間開始累計。 因此，您應該在經過一些時間之後，計算兩個測量值之間的差異。  
  
```sql  
SELECT DB_NAME(database_id) AS   
   [Database Name] ,   
   file_id ,   
   io_stall_read_ms ,   
   num_of_reads ,   
   CAST(io_stall_read_ms / ( 1.0 + num_of_reads ) AS NUMERIC(10, 1)) AS [avg_read_stall_ms] ,   
   io_stall_write_ms ,   
   num_of_writes ,  
   CAST(io_stall_write_ms / ( 1.0 + num_of_writes ) AS NUMERIC(10, 1)) AS [avg_write_stall_ms] ,   
   io_stall_read_ms + io_stall_write_ms AS [io_stalls] ,   
   num_of_reads + num_of_writes AS [total_io] ,   
   CAST(( io_stall_read_ms + io_stall_write_ms ) / ( 1.0 + num_of_reads  
+ num_of_writes) AS NUMERIC(10,1)) AS [avg_io_stall_ms]  
FROM sys.dm_io_virtual_file_stats(NULL, NULL)  
WHERE DB_NAME(database_id) IN (SELECT DISTINCT database_name FROM sys.dm_hadr_database_replica_cluster_states)  
ORDER BY avg_io_stall_ms DESC;  
```  
  
 下一個查詢提供系統上擱置的 I/O 要求的時間點 (不是累計) 快照集。  
  
```sql  
SELECT DB_NAME(mf.database_id) AS [Database] ,   
   mf.physical_name ,  
   r.io_pending ,   
   r.io_pending_ms_ticks ,   
   r.io_type ,   
   fs.num_of_reads ,   
   fs.num_of_writes  
FROM sys.dm_io_pending_io_requests AS r   
INNER JOIN sys.dm_io_virtual_file_stats(NULL, NULL) AS fs ON r.io_handle = fs.file_handle   
INNER JOIN sys.master_files AS mf ON fs.database_id = mf.database_id  
AND fs.file_id = mf.file_id  
ORDER BY r.io_pending , r.io_pending_ms_ticks DESC;  
```  
  
 您可以和另一種比較讀取 I/O 和寫入 I/O 相符的程度以識別 I/O 競爭。  
  
 以下是其他一些可協助您診斷 I/O 瓶頸的效能計數器：  
  
-   **Physical Disk：所有計數器**  
  
-   **實體磁碟：Avg.Disk sec/Transfer**  
  
-   **SQL Server：Databases > Log Flush Wait Time**  
  
-   **SQL Server：Databases > Log Flush Waits/sec**  
  
-   **SQL Server：Databases > Log Pool Disk Reads/sec**  
  
 如果您找到了 I/O 瓶頸，而且將記錄檔和資料檔案放在同一個硬碟上，則應該做的第一件事是將資料檔案和記錄檔放在不同的磁碟上。 這個最佳做法可防止報告工作負載干擾從主要複本到記錄緩衝區的記錄傳送路徑，以及其在次要複本上強化交易的能力。  
  
## <a name="next-steps"></a>後續步驟  
 [對 SQL Server 中的效能問題進行疑難排解 (適用於 SQL Server 2012)](/previous-versions/sql/sql-server-2008/dd672789(v=sql.100)) \(英文\)  
  
