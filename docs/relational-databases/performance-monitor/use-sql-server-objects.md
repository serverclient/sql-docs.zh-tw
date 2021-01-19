---
title: 使用 SQL Server 物件 | Microsoft 文件
description: 了解系統監視器為了監視執行 SQL Server 執行個體其電腦中活動所使用的 SQL Server 物件與計數器。
ms.custom: ''
ms.date: 03/17/2016
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: performance
ms.topic: conceptual
helpviewer_keywords:
- server performance [SQL Server], objects for monitoring
- database monitoring [SQL Server], objects for monitoring
- charts [SQL Server]
- System Monitor [SQL Server], counters
- counters [SQL Server], listed
- objects [SQL Server], performance monitoring
- objects [SQL Server], Windows System Monitor
- performance counters [SQL Server], about performance counters
- System Monitor [SQL Server], objects
- performance counters [SQL Server]
- counters [SQL Server], about performance counters
- tuning databases [SQL Server], objects for monitoring
- database performance [SQL Server], objects for monitoring
- SQL Server, objects
- monitoring performance [SQL Server], objects for monitoring
- Windows System Monitor [SQL Server], objects
- Windows System Monitor [SQL Server], counters
- counters [SQL Server]
- performance counters [SQL Server], listed
ms.assetid: bcd731b1-3c4e-4086-b58a-af7a3af904ad
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: e293b33b0b20ab7545744c7d4e3f47351f8e0c29
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98172190"
---
# <a name="use-sql-server-objects"></a>使用 SQL Server 物件
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  Microsoft [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 所提供的物件與計數器，可供「系統監視器」用來對執行 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]執行個體的電腦監視其中的活動。 物件可以是任何一種 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 資源，例如 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 鎖定或 Windows 處理序。 每個物件都包含一個或多個計數器，可決定欲監視之物件的不同層面。 例如， **SQL Server Locks** 物件包含了稱為 **Number of Deadlocks/sec** 與 **Lock Timeouts/sec** 的計數器。  
  
 若給定類型的多個資源存在於電腦內，一些物件將擁有多個執行個體。 例如，若系統擁有多個處理器， **Processor** 物件類型將擁有多個執行個體。 **Databases** 物件類型對於 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]中的每個資料庫都擁有一個執行個體。 有些物件類型 (例如 **Memory Manager** 物件) 則只有一個執行個體。 若物件擁有多個執行個體，您可增加計數器來追蹤每個執行個體的統計資料，在許多狀況下，則可同時追蹤所有的執行個體。 預設執行個體的計數器會以 **SQLServer:** _\<object name>_ 格式顯示。 具名執行個體的計數器會以 **MSSQL$:** _\<instance name>_  _\<counter name>_ 或 **SQLAgent$** _\<instance name>_  _\<counter name>_ 格式顯示。  
  
[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 效能計數器值是使用 Windows 效能計數器 (WPC) 引擎所產生。 某些計數器值不會直接由 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 計算。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會為 WPC 引擎提供基底值，該引擎會執行所需的計算 (例如百分比)。 [sys.dm_os_performance_counters &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-os-performance-counters-transact-sql.md) 動態管理檢視會提供具有由 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 所產生原始值的所有計數器。 `cntr_type` 資料行會指出計數器的類型。 WPC 引擎處理 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 計數器值的方式取決於此類型。 如需效能計數器類型的詳細資訊，請參閱 [WMI 文件](/windows/win32/wmisdk/wmi-performance-counter-types)。
  
 您可以在圖表中新增或移除計數器，並儲存圖表設定，藉以指定要在「系統監視器」啟動時監視的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 物件和計數器。  
  
 您可以設定「系統監視器」來顯示任何 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 計數器的統計資料。 此外，您也可以設定任何 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 計數器的臨界值，並在計數器超過臨界值時產生警示。 如需設定警示的詳細資訊，請參閱 [建立 SQL Server 資料庫警示](../../relational-databases/performance-monitor/create-a-sql-server-database-alert.md)。  
    
  
> [!NOTE]  
>  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 必須安裝 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體，才會顯示統計資料。 若您停止並重新啟動 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]執行個體，統計資料的顯示將會中斷，然後自動繼續。 另外請注意，就算並未執行 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ，「系統監視器」嵌入式管理單元中仍會顯示 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 計數器。 在叢集執行個體上，效能計數器只能夠在執行 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的節點上運作。  
  
 本主題包含下列幾節：  
  
-   [SQL Server Agent 效能物件](#SQLServerAgentPOs)  
  
-   [Service Broker 效能物件](#ServiceBrokerPOs)  
  
-   [SQL Server 效能物件](#SQLServerPOs)  
  
-   [SQL Server 複寫效能物件](#SQLServerReplicationPOs)  
  
-   [SSIS 管線計數器](#SsisPipelineCounters)  
  
-   [必要權限](#RequiredPermissions)  
  
##  <a name="sql-server-agent-performance-objects"></a><a name="SQLServerAgentPOs"></a> SQL Server Agent 效能物件  
 下表列出針對 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent 提供的效能物件：  
  
|效能物件|描述|  
|------------------------|-----------------|  
|[SQLAgent:Alerts](../../relational-databases/performance-monitor/sql-server-agent-alerts-object.md)|提供有關 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent 警示的資訊。|  
|[SQLAgent:Jobs](../../relational-databases/performance-monitor/sql-server-agent-jobs-object.md)|提供有關 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent 作業的資訊。|  
|[SQLAgent:JobSteps](../../relational-databases/performance-monitor/sql-server-agent-jobsteps-object.md)|提供有關 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent 作業步驟的資訊。|  
|[SQLAgent:Statistics](../../relational-databases/performance-monitor/sql-server-agent-statistics-object.md)|提供有關 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent 的一般性資訊。|  
  
##  <a name="service-broker-performance-objects"></a><a name="ServiceBrokerPOs"></a> Service Broker 效能物件  
 下表列出針對 [!INCLUDE[ssSB](../../includes/sssb-md.md)]提供的效能物件。  
  
|效能物件|描述|  
|------------------------|-----------------|  
|[SQLServer:Broker Activation](../../relational-databases/performance-monitor/sql-server-broker-activation-object.md)|提供 [!INCLUDE[ssSB](../../includes/sssb-md.md)]啟動工作的相關資訊。|  
|[SQLServer:Broker 統計資料](../../relational-databases/performance-monitor/sql-server-broker-statistics-object.md)|提供 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 的一般性資訊。|  
|[SQLServer:Broker Transport](../../relational-databases/performance-monitor/sql-server-broker-dbm-transport-object.md)|提供 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 網路的相關資訊。|  
  
##  <a name="sql-server-performance-objects"></a><a name="SQLServerPOs"></a> SQL Server 效能物件  
 下表描述 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 物件。  
  
|效能物件|描述|  
|------------------------|-----------------|  
|[SQLServer:Access Methods](../../relational-databases/performance-monitor/sql-server-access-methods-object.md)|搜尋並測量 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 資料庫物件的配置 (例如索引搜尋數或配置給索引和資料的頁數)。|  
|[SQLServer:Backup Device](../../relational-databases/performance-monitor/sql-server-backup-device-object.md)|提供備份和還原作業所使用的備份裝置相關資訊，例如備份裝置的輸送量。|  
|[SQLServer:Batch Resp Statistics](../../relational-databases/performance-monitor/sql-server-batch-resp-statistics-object.md)|追蹤 SQL 批次回應時間的計數器。| 
|[SQLServer:Buffer Manager](../../relational-databases/performance-monitor/sql-server-buffer-manager-object.md)|提供 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]所使用之記憶體緩衝區的相關資訊，例如 **freememory** 與 **buffer cache hit ratio**。|  
|[SQL Server:Buffer Node](../../relational-databases/performance-monitor/sql-server-buffer-node.md)|提供 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 要求及存取可用頁面的頻率相關資訊。|  
|[SQLServer:Catalog Metadata](../../relational-databases/performance-monitor/sql-server-catalog-metadata-object.md)|這會定義 SQL Server 的目錄中繼資料管理員物件。| 
|[SQLServer:CLR](../../relational-databases/performance-monitor/sql-server-clr-object.md)|提供有關 Common Language Runtime (CLR) 的資訊。|  
|[SQLServer:Columnstore](../../relational-databases/performance-monitor/sql-server-columnstore-object.md)|**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 及更新版本)。<br /><br /> 提供資料行存放區索引之資料列群組和區段的相關資訊。|  
|[SQLServer:Cursor Manager by Type](../../relational-databases/performance-monitor/sql-server-cursor-manager-by-type-object.md)|提供關於資料指標的資訊。|  
|[SQLServer:Cursor Manager Total](../../relational-databases/performance-monitor/sql-server-cursor-manager-total-object.md)|提供關於資料指標的資訊。|  
|[SQLServer:Database Mirroring](../../relational-databases/performance-monitor/sql-server-database-mirroring-object.md)|提供資料庫鏡像的相關資訊。|  
|[SQLServer:Databases](../../relational-databases/performance-monitor/sql-server-databases-object.md)|提供 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 資料庫的相關資訊，例如可用的記錄空間量，或資料庫中的使用中交易數目。 本物件中含有多項執行個體。|  
|[SQL Server:Deprecated Features](../../relational-databases/performance-monitor/sql-server-deprecated-features-object.md)|此值會計算使用之已被取代功能的次數。|  
|[SQLServer:Exec Statistics](../../relational-databases/performance-monitor/sql-server-execstatistics-object.md)|提供執行統計資料的相關資訊。|  
|[SQL Server：外部指令碼](../../relational-databases/performance-monitor/sql-server-external-scripts-object.md)|**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 及更新版本)。<br /><br /> 提供外部指令碼執行的相關資訊。|  
|[SQLServer:FileTable](../../relational-databases/performance-monitor/sql-server-filetable-object.md)|與 FileTable 和非交易式存取相關聯的統計資料。|  
|[SQLServer:General Statistics](../../relational-databases/performance-monitor/sql-server-general-statistics-object.md)|提供一般伺服器範圍活動的相關資訊，例如目前連接到 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]執行個體的使用者數目。|  
|[SQL Server:HADR 可用性複本](../../relational-databases/performance-monitor/sql-server-availability-replica.md)|提供 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)][!INCLUDE[ssHADR](../../includes/sshadr-md.md)] 可用性複本的相關資訊。|  
|[SQL Server:HADR 資料庫複本](../../relational-databases/performance-monitor/sql-server-database-replica.md)|提供 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)][!INCLUDE[ssHADR](../../includes/sshadr-md.md)] 資料庫複本的相關資訊。|  
|[SQL Server:HTTP Storage](../../relational-databases/performance-monitor/sql-server-http-storage-object.md)|提供在使用 [Microsoft Azure 中的 SQL Server 資料檔案](../../relational-databases/databases/sql-server-data-files-in-microsoft-azure.md)時，用來監視 Microsoft Azure 儲存體帳戶的資訊|  
|[SQLServer:Latches](../../relational-databases/performance-monitor/sql-server-latches-object.md)|針對 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]所使用之內部資源 (例如資料庫頁面) 的閂鎖，提供相關資訊。|  
|[SQLServer:Locks](../../relational-databases/performance-monitor/sql-server-locks-object.md)|提供 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]所提出之個別鎖定要求的相關資訊，例如鎖定逾時和死結。 本物件中含有多項執行個體。|  
|[SQLServer:LogPool FreePool](../../relational-databases/performance-monitor/sql-server-logpool-freepool-object.md)|描述記錄集區內可用集區的統計資料。|
|[SQLServer:Memory Broker Clerks](../../relational-databases/performance-monitor/sql-server-memory-broker-clerks-object.md)|與記憶體 Broker Clerk 相關的統計資料。|
|[SQLServer:Memory Manager](../../relational-databases/performance-monitor/sql-server-memory-manager-object.md)|提供 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 記憶體使用狀況的相關資訊，例如目前配置的鎖定結構總數。|  
|[SQLServer:Plan Cache](../../relational-databases/performance-monitor/sql-server-plan-cache-object.md)|提供用來儲存物件 (例如預存程序、觸發程序和查詢計畫) 之 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 快取的相關資訊。|  
|[SQLServer:Query Store](../../relational-databases/performance-monitor/sql-server-query-store-object.md)|提供查詢存放區的相關資訊。|  
|[SQLServer:Resource Pool Stats](../../relational-databases/performance-monitor/sql-server-resource-pool-stats-object.md)|提供有關資源管理員資源集區統計資料的資訊。|  
|[SQLServer:SQL Errors](../../relational-databases/performance-monitor/sql-server-sql-errors-object.md)|提供 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 錯誤的相關資訊。|  
|[SQLServer:SQL Statistics](../../relational-databases/performance-monitor/sql-server-sql-statistics-object.md)|提供 [!INCLUDE[tsql](../../includes/tsql-md.md)] 查詢方面的相關資訊，例如 [!INCLUDE[tsql](../../includes/tsql-md.md)] 所接收之 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]陳述式的批次數。|  
|[SQLServer:Transactions](../../relational-databases/performance-monitor/sql-server-transactions-object.md)|提供 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]的使用中交易相關資訊，例如交易總數與快照集交易的數量。|  
|[SQLServer:User Settable](../../relational-databases/performance-monitor/sql-server-user-settable-object.md)|執行自訂監視。 每個計數器皆可為自訂的預存程序，或任何可傳回監視數值的 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式。|  
|[SQLServer:Wait Statistics](../../relational-databases/performance-monitor/sql-server-wait-statistics-object.md)|提供等候的相關資訊。|  
|[SQLServer:Workload Group Stats](../../relational-databases/performance-monitor/sql-server-workload-group-stats-object.md)|提供有關資源管理員工作負載群組統計資料的資訊。|  
  
##  <a name="sql-server-replication-performance-objects"></a><a name="SQLServerReplicationPOs"></a> SQL Server 複寫效能物件  
 下表列出針對 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 複寫提供的效能物件：  
  
|效能物件|描述|  
|------------------------|-----------------|  
|**SQLServer:Replication Agents**<br /><br /> **SQLServer:Replication Snapshot**<br /><br /> **SQLServer:Replication Logreader**<br /><br /> **SQLServer:Replication Dist**<br /><br /> **SQLServer:Replication Merge**<br /><br /> 如需相關資訊，請參閱 [Monitoring Replication with System Monitor](../../relational-databases/replication/monitor/monitoring-replication-with-system-monitor.md)。|提供複寫代理程式活動的相關資訊。|  
  
##  <a name="ssis-pipeline-counters"></a><a name="SsisPipelineCounters"></a> SSIS 管線計數器  
 如需 **SSIS Pipeline** 計數器的相關資訊，請參閱 [效能計數器](../../integration-services/performance/performance-counters.md)。  
  
##  <a name="required-permissions"></a><a name="RequiredPermissions"></a> 必要權限  
 使用 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 物件時必須具備 Windows 權限，[SQLAgent:Alerts] 除外。 使用者必須是 **系統管理員** 固定伺服器角色的成員，才能使用 [SQLAgent:Alerts]。  
  
## <a name="see-also"></a>另請參閱  
 [使用效能物件](../../ssms/agent/use-performance-objects.md)   
 [sys.dm_os_performance_counters &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-os-performance-counters-transact-sql.md)  
  
