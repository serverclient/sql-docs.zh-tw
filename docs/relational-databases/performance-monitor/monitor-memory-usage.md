---
title: 監視記憶體使用量
description: 監視 SQL Server 執行個體，以確認記憶體使用量在正常範圍內。 使用記憶體：Available Bytes 以及記憶體：Pages/sec 計數器。
ms.custom: ''
ms.date: 01/20/2021
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: performance
ms.topic: conceptual
helpviewer_keywords:
- tuning databases [SQL Server], memory
- monitoring server performance [SQL Server], memory usage
- isolating memory [SQL Server]
- paging rate [SQL Server]
- memory [SQL Server], monitoring usage
- monitoring [SQL Server], memory usage
- low-memory conditions
- database monitoring [SQL Server], memory usage
- available memory [SQL Server]
- page faults [SQL Server]
- monitoring performance [SQL Server], memory usage
- server performance [SQL Server], memory
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 8954573b0c32eef45ec655b27d26e03e72be96ed
ms.sourcegitcommit: 713e5a709e45711e18dae1e5ffc190c7918d52e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98688757"
---
# <a name="monitor-memory-usage"></a>監視記憶體使用量
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
  定期監視 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體，以確認記憶體使用量是在正常範圍內。 

## <a name="configuring-ssnoversion-max-memory"></a>設定 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 最大記憶體

根據預設， [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 實例可能會隨著時間耗用伺服器中大部分可用的 Windows 作業系統記憶體。 一旦取得記憶體，除非偵測到記憶體壓力，否則不會釋出記憶體。 這是設計的，並不表示進程中的記憶體流失 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。 使用 [[ **最大伺服器記憶體** ] 選項](../../database-engine/configure-windows/server-memory-server-configuration-options.md) 來限制 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 大部分用途所能取得的記憶體數量。 如需詳細資訊，請參閱 [記憶體管理架構指南](../../relational-databases/memory-management-architecture-guide.md#changes-to-memory-management-starting-with-)。

在 Linux 上的 SQL Server 中，使用 mssql 會議工具和[>memory.memorylimitmb](../../linux/sql-server-linux-configure-mssql-conf.md#memorylimit)設定來[設定記憶體限制](../../linux/sql-server-linux-performance-best-practices.md#advanced-configuration)。  

## <a name="monitor-operating-system-memory"></a>監視作業系統記憶體   
 若要監視是否有記憶體不足的狀況，請使用下列 Windows server 計數器。 許多作業系統記憶體計數器都可以透過動態管理檢視 [sys.dm_os_process_memory](../system-dynamic-management-views/sys-dm-os-process-memory-transact-sql.md) 和 [sys.dm_os_sys_memory](../system-dynamic-management-views/sys-dm-os-sys-memory-transact-sql.md)來查詢。

-   **記憶體：Available Bytes**  
此計數器表示目前可供進程使用的記憶體位元組數目。 **可用位元組** 計數器的低值可能表示作業系統記憶體的整體不足。 您可以使用 [sys.dm_os_sys_memory](../system-dynamic-management-views/sys-dm-os-sys-memory-transact-sql.md).available_physical_memory_kb，透過 t-sql 來查詢此值。

-   **記憶體：Pages/sec**  
此計數器表示因為發生分頁錯誤，而從磁片取出的頁數，或由於分頁錯誤而寫入磁片，以釋出工作集中空間的頁面數目。 **Pages/sec** 計數器數值過高可能代表過度分頁。  

-   **記憶體：分頁錯誤數/秒** 此計數器表示所有進程（包括系統進程）的分頁錯誤率。 對磁片的分頁 (的低比率（非零），因此即使電腦有足夠的可用記憶體，仍) 的分頁錯誤是正常的。 當「Microsoft Windows 虛擬記憶體管理員 (VMM)」修剪 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 和其他處理序的工作集大小時，它會從這些處理序取得分頁。 此 VMM 活動會造成分頁錯誤。  

-   **進程：分頁錯誤數/秒** 此計數器表示給定使用者進程的分頁錯誤率。 監視 **進程：分頁錯誤數/秒** ，以判斷是否由分頁所造成的磁片活動 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。 若要判斷 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 或其他處理序是否為過度分頁的原因，您可以監視 **處理序：Page Faults/sec** 計數器 (針對 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 處理序執行個體)。  
  
 如需有關解決過度分頁的詳細資訊，請參閱作業系統檔集。  
  
## <a name="isolating-memory-used-by-ssnoversion"></a>隔離所使用的記憶體 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 

 若要監視 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 記憶體使用量，請使用下列 [SQL Server 物件計數器](use-sql-server-objects.md)。 您可以透過動態管理檢視 [sys.dm_os_performance_counters](../system-dynamic-management-views/sys-dm-os-performance-counters-transact-sql.md) 或 [sys.dm_os_process_memory](../system-dynamic-management-views/sys-dm-os-process-memory-transact-sql.md)來查詢許多 SQL Server 物件計數器。

 根據預設，會 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 根據可用的系統資源，動態管理其記憶體需求。 如果 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 需要更多記憶體，它將詢問作業系統以判斷是否有可用的實體記憶體，並使用可用的記憶體。 如果 OS 的可用記憶體不足， [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會將記憶體釋回作業系統，直到記憶體不足的狀況緩解，或 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 達到 **最小的伺服器記憶體** 限制為止。 不過，您可以使用 [ **最小伺服器記憶體**] 和 [ **最大伺服器記憶體** 伺服器設定] 選項，覆寫選項以動態使用記憶體。 如需詳細資訊，請參閱＜ [伺服器記憶體選項](../../database-engine/configure-windows/server-memory-server-configuration-options.md)＞。  
  
 若要監視 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 所使用的記憶體數量，請檢查下列效能計數器：  
  
-   **SQL Server：記憶體管理員：Total Server Memory (KB)**  
此計數器表示 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 記憶體管理員目前已認可的作業系統記憶體量 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。 此數位預期會隨著實際活動的要求成長，且將在下列 SQL Server 啟動時成長。 使用 [sys.dm_os_sys_info](../system-dynamic-management-views/sys-dm-os-sys-info-transact-sql.md) 動態管理檢視來查詢此計數器，觀察 **committed_kb** 資料行。

-   **SQL Server：記憶體管理員：目標伺服器記憶體 (KB)**  
此計數器 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會根據最近的工作負載，指出可能耗用的理想記憶體數量。 在一段一般作業之後，與 **伺服器記憶體總計** 進行比較，以判斷是否要配置 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 所需的記憶體數量。 一般作業之後， **伺服器記憶體** 和 **目標伺服器記憶體** 的總計應該類似。 如果 **總伺服器記憶體** 明顯低於 **目標伺服器記憶體**， [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 實例可能會遇到記憶體不足的壓力。 在啟動後的期間內 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ， **伺服器** 記憶體總計應低於 **目標伺服器記憶體**，因為 **伺服器記憶體總計** 增加。 使用 [sys.dm_os_sys_info](../system-dynamic-management-views/sys-dm-os-sys-info-transact-sql.md) 動態管理檢視來查詢此計數器，觀察 **committed_target_kb** 資料行。 如需設定記憶體的詳細資訊和最佳作法，請參閱 [伺服器記憶體設定選項](../../database-engine/configure-windows/server-memory-server-configuration-options.md#manually)。

-   **處理序：Working Set**  
此計數器會根據作業系統，指出目前正在由進程使用的實體記憶體數量。 觀察此計數器的 sqlservr.exe 實例。 使用 [sys.dm_os_process_memory](../system-dynamic-management-views/sys-dm-os-process-memory-transact-sql.md) 動態管理檢視來查詢此計數器，觀察 **physical_memory_in_use_kb** 資料行。

-   **進程：私用位元組**  
此計數器表示進程為了其自己的作業系統使用所要求的記憶體數量。 觀察此計數器的 sqlservr.exe 實例。 因為此計數器包含 sqlservr.exe 所要求的所有記憶體配置，包括未受 [[ **最大伺服器記憶體** ] 選項](../../database-engine/configure-windows/server-memory-server-configuration-options.md)限制的記憶體配置，此計數器可以報告大於 [ **max server memory** 選項的](../../database-engine/configure-windows/server-memory-server-configuration-options.md)值。

-   **SQL Server：緩衝區管理員：Database Pages**  
此計數器會指出具有資料庫內容之緩衝集區中的頁面數目。 不包含 SQL Server 進程內的其他 nonbuffer 集區記憶體。 使用 [sys.dm_os_performance_counters](../system-dynamic-management-views/sys-dm-os-performance-counters-transact-sql.md) 動態管理檢視來查詢此計數器。

-   **SQL Server：緩衝區管理員：Buffer Cache Hit Ratio**  
此計數器是特有的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。 需要90或更高的比例。 大於90的值表示在記憶體中的資料快取已滿足超過90% 的資料要求，而不需要從磁片讀取。 若要尋找 SQL Server 緩衝區管理員的詳細資訊，請參閱 [SQL Server Buffer Manager 物件](sql-server-buffer-manager-object.md)。 使用 [sys.dm_os_performance_counters](../system-dynamic-management-views/sys-dm-os-performance-counters-transact-sql.md) 動態管理檢視來查詢此計數器。  
 
-   **SQL Server：緩衝區管理員：頁面壽命預期**  
此計數器會測量最舊頁面停留在緩衝集區中的時間量（以秒為單位）。 針對使用 NUMA 架構的系統，這是所有 NUMA 節點的平均值。 更高的成長值最適合。 突然 dip 表示大量資料在緩衝集區中的變化，表示工作負載無法完全受益于已經存在於記憶體中的資料。 每個 NUMA 節點都有自己的緩衝區集區節點。 在具有多個 NUMA 節點的伺服器上，使用 **SQL Server： Buffer 節點** 來查看每個緩衝集區節點的頁面壽命預期：頁面的預期壽命。 使用 [sys.dm_os_performance_counters](../system-dynamic-management-views/sys-dm-os-performance-counters-transact-sql.md) 動態管理檢視來查詢此計數器。

  
## <a name="examples"></a>範例 

### <a name="determining-current-memory-allocation"></a>判斷目前的記憶體配置  
 下列查詢會傳回目前配置之記憶體的相關資訊。  
  
```  
SELECT
(total_physical_memory_kb/1024) AS Total_OS_Memory_MB,
(available_physical_memory_kb/1024)  AS Available_OS_Memory_MB
FROM sys.dm_os_sys_memory;

SELECT  
(physical_memory_in_use_kb/1024) AS Memory_used_by_Sqlserver_MB,  
(locked_page_allocations_kb/1024) AS Locked_pages_used_by_Sqlserver_MB,  
(total_virtual_address_space_kb/1024) AS Total_VAS_in_MB,
process_physical_memory_low,  
process_virtual_memory_low  
FROM sys.dm_os_process_memory;  
```  

### <a name="determining-current-sql-server-memory-utilization"></a>判斷目前的 SQL Server 記憶體使用量   
 下列查詢會傳回目前 SQL Server 記憶體使用量的相關資訊。  
```  
SELECT
sqlserver_start_time,
(committed_kb/1024) AS Total_Server_Memory_MB,
(committed_target_kb/1024)  AS Target_Server_Memory_MB
FROM sys.dm_os_sys_info;
```   

### <a name="determining-page-life-expectancy"></a>判斷頁面的預期壽命
 下列查詢會使用 **sys.dm_os_performance_counters** 來觀察整體緩衝區管理員層級及每個 NUMA 節點層級上 SQL Server 實例的目前 **頁面生命預期** 值。
```
SELECT
CASE instance_name WHEN '' THEN 'Overall' ELSE instance_name END AS NUMA_Node, cntr_value AS PLE_s
FROM sys.dm_os_performance_counters    
WHERE counter_name = 'Page life expectancy';
```

## <a name="see-also"></a>另請參閱
- [sys.dm_os_sys_memory (Transact-SQL)](../system-dynamic-management-views/sys-dm-os-sys-memory-transact-sql.md)
- [sys.dm_os_process_memory (Transact-SQL)](../system-dynamic-management-views/sys-dm-os-process-memory-transact-sql.md)
- [sys.dm_os_sys_info (Transact-SQL)](../system-dynamic-management-views/sys-dm-os-sys-info-transact-sql.md)
- [sys.dm_os_performance_counters (Transact-SQL)](../system-dynamic-management-views/sys-dm-os-performance-counters-transact-sql.md)
- [SQL Server 的 Memory Manager 物件](sql-server-memory-manager-object.md)
- [SQL Server 的 Buffer Manager 物件](sql-server-buffer-manager-object.md)   
- [伺服器記憶體組態選項](../../database-engine/configure-windows/server-memory-server-configuration-options.md)
- [記憶體管理架構指南](../../relational-databases/memory-management-architecture-guide.md)
