---
title: 記憶體管理架構指南 | Microsoft Docs
description: 了解 SQL Server 中的記憶體管理結構，包括對先前版本中記憶體管理進行的變更。
ms.custom: ''
ms.date: 01/09/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: supportability
ms.topic: conceptual
helpviewer_keywords:
- guide, memory management architecture
- memory management architecture guide
- PMO
- Partitioned Memory Objects
- cmemthread
- AWE
- SPA, Single Page Allocator
- MPA, Multi Page Allocator
- memory allocation, SQL Server
- memory pressure, SQL Server
- stack size, SQL Server
- buffer manager, SQL Server
- buffer pool, SQL Server
- resource monitor, SQL Server
ms.assetid: 7b0d0988-a3d8-4c25-a276-c1bdba80d6d5
author: rothja
ms.author: jroth
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 6af1077d46fa6378e0853eb570ba560d49e6eaec
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98171340"
---
# <a name="memory-management-architecture-guide"></a>記憶體管理架構指南

[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

## <a name="windows-virtual-memory-manager"></a>Windows 虛擬記憶體管理員  
此位址空間的認可區域是由「Windows 虛擬記憶體管理員 (VMM)」對應到可用的實體記憶體。  
  
如需有關不同作業系統所支援之實體記憶體數量的詳細資訊，請參閱 Windows 文件[Windows 版本的記憶體限制](/windows/desktop/Memory/memory-limits-for-windows-releases)。  
  
虛擬記憶體系統會允許超額認可實體記憶體，所以虛擬記憶體與實體記憶體的比率可超過 1:1。 因此，大型程式可以在各種實體記憶體組態的電腦上執行。 然而，使用的虛擬記憶體若遠大於所有處理序的平均工作集組合，可能會導致效能降低。 

## <a name="sql-server-memory-architecture"></a>SQL Server 記憶體架構

[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 會視需要動態地取得和釋放記憶體。 雖然有指定記憶體的選項可供選擇而且在某些環境下也是必要的，但是系統管理員通常不需要指定應配置多少記憶體給 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]。

所有資料庫軟體的主要設計目的之一，便是將磁碟 I/O 最小化，因為磁碟的讀取和寫入，是電腦上最需要用到大量資源的作業之一。 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 會在記憶體中建立緩衝集區，以保存從資料庫讀取的分頁。 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 中的大部分程式碼，主要是用來最小化磁碟和緩衝集區之間實體讀取和寫入的次數。 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 嘗試在兩個目標之間取得平衡：

* 避免緩衝集區過大，造成整個系統的記憶體不足。
* 最大化緩衝集區的大小以最小化資料庫檔案的實體 I/O。

> [!NOTE]
> 在工作負載極高的系統上，某些需要大量記憶體來執行的大型查詢因為無法取得要求的最少記憶體，而在等候記憶體資源時發生逾時錯誤。 若要解決這個問題，請增加 [查詢等候選項](../database-engine/configure-windows/configure-the-query-wait-server-configuration-option.md)。 如果是平行查詢，請考慮減少 [平行處理原則的最大程度選項](../database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option.md)。
 
> [!NOTE]
> 在記憶體負載極高的系統中，若在查詢計畫中使用合併聯結、排序與點陣圖的查詢，當查詢無法取得點陣圖所需的最低記憶體時，會卸除點陣圖。 這樣會影響查詢效能，且若排序處理序無法放入記憶體中，它會增加 tempdb 資料庫中工作資料表的使用數目，導致 tempdb 成長。 若要解決此問題，請新增記憶體或微調查詢以使用不同與更快的查詢計畫。
 
### <a name="providing-the-maximum-amount-of-memory-to-ssnoversion"></a>為 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 提供記憶體數量上限

透過使用 AWE 與 [鎖定記憶體中的分頁] 權限，您可以為 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] Database Engine 提供下列記憶體數量。 

> [!NOTE]
> 下表中的欄位包含已不再提供的 32 位元版本。

|記憶體原則|32 位元 <sup>1</sup> |64 位元|
|-------|-------|-------| 
|傳統記憶體 |所有的 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 版本。 處理序虛擬位址空間的最大限制： <br>- 2 GB<br>- 使用 /3gb 開機參數時為 3 GB <sup>2</sup> <br>- 在 WOW64 上為 4 GB <sup>3</sup> |所有的 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 版本。 處理序虛擬位址空間的最大限制： <br>- 使用 IA64 架構時為 7 TB (IA64 不支援 [!INCLUDE[ssSQL11](../includes/sssql11-md.md)] 和以上版本)<br>- 使用 x64 架構時為作業系統最大值 <sup>4</sup>
|AWE 機制 (允許 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 在 32 位元平台上超過處理虛擬位址空間的限制。) |[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] Standard、Enterprise 與 Developer 版本：緩衝集區最多可存取 64 GB 的記憶體。|不適用 <sup>5</sup> |
|鎖定記憶體中作業系統 (OS) 分頁的權限 (允許鎖定實體記憶體，避免鎖定記憶體的 OS 分頁。)<sup>6</sup> |[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] Standard、Enterprise 與 Developer 版本：[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 處理序使用 AWE 機制所需。 透過 AWE 機制配置的記憶體無法分頁。 <br> 授與此權限但未啟用 AWE 對伺服器將沒有作用。 | 請只有在必要的情況下才使用，例如出現 sqlservr 程序移出分頁的跡象。在此情況下，錯誤記錄檔中會回報錯誤 17890，類似如下範例：`A significant part of sql server process memory has been paged out. This may result in a performance degradation. Duration: #### seconds. Working set (KB): ####, committed (KB): ####, memory utilization: ##%.`|

<sup>1</sup> 從 [!INCLUDE[ssSQL14](../includes/sssql14-md.md)]開始不提供 32 位元版本。  
<sup>2</sup> /3gb 是作業系統開機參數。  
<sup>3</sup> WOW64 (Windows 64 上的 Windows) 是 32 位元的 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 在 64 位元作業系統上執行的模式。  
<sup>4</sup> [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] Standard Edition 最多支援 128 GB。 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] Enterprise Edition 支援作業系統上限。  
<sup>5</sup> 請注意，sp_configure awe enabled 選項會出現在 64 位元的 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]上，但會遭到忽略。    
<sup>6</sup> 如果(在具有 AWE 支援的 32 位元上或是本身的 64 位元上) 授與鎖定記憶體中的分頁權限 (LPIM)，我們建議另外設定最大伺服器記憶體。 如需 LPIM 的詳細資訊，請參閱[伺服器記憶體伺服器設定選項](../database-engine/configure-windows/server-memory-server-configuration-options.md#lock-pages-in-memory-lpim)

> [!NOTE]
> 舊版的 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 可以在 32 位元作業系統上執行。 在 32 位元作業系統上存取超過 4 GB 的記憶體，會需要 Address Windowing Extensions (AWE) 來管理記憶體。 若 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 是在 64 位元作業系統上執行，就沒有這項需要。 如需有關 AWE 的詳細資訊，請參閱 [!INCLUDE[ssKatmai](../includes/ssKatmai-md.md)] 文件中的[處理序位址空間](/previous-versions/sql/sql-server-2008-r2/ms189334(v=sql.105))以及[管理大型資料庫的記憶體](/previous-versions/sql/sql-server-2008-r2/ms191481(v=sql.105))。   

<a name="changes-to-memory-management-starting-2012-11x-gm"></a>

## <a name="changes-to-memory-management-starting-with-sssql11"></a>從 [!INCLUDE[ssSQL11](../includes/sssql11-md.md)] 開始對記憶體管理進行的變更

在舊版 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] ([!INCLUDE[ssVersion2005](../includes/ssversion2005-md.md)]、[!INCLUDE[ssKatmai](../includes/ssKatmai-md.md)] 及 [!INCLUDE[ssKilimanjaro](../includes/ssKilimanjaro-md.md)]) 中，使用了五種不同的機制配置記憶體：
-  **單一頁面配置器 (SPA)** ，在 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 處理序中只包含少於或等於 8 KB 的記憶體配置。 [最大伺服器記憶體 (MB)] 與 [最小伺服器記憶體 (MB)] 設定選項決定了 SPA 可取用的實體記憶體上限。 緩衝集區同時是 SPA 的機制，以及單頁配置的最大取用者。
-  **多頁配置器 (MPA)** ，適用於要求超過 8KB 的記憶體配置。
-  **CLR 配置器**，包括 SQL CLR 堆積，及其在 CLR 初始化期間所建立的全域配置。
-  [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 處理序中 **[執行緒堆疊](../relational-databases/memory-management-architecture-guide.md#stacksizes)** 的記憶體配置。
-  **直接 Windows 配置 (DWA)** ，適用於直接向 Windows 提出的記憶體配置要求。 這些包括使用 Windows 堆積，以及載入至 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 處理之模組所做的直接虛擬配置。 這類的記憶體配置要求範例，包括擴充預存程序 DLL 的配置、使用「自動」處理序 (sp_OA 呼叫) 所建立的物件，以及連結伺服器提供者的配置。

從 [!INCLUDE[ssSQL11](../includes/sssql11-md.md)] 開始，單頁配置、多頁配置及 CLR 配置皆一併整合為 **「任何大小」分頁配置器**，且包含在 [最大伺服器記憶體 (MB)] 與 [最小伺服器記憶體 (MB)] 設定選項所控制的記憶體限制之中。 這些變更為經由 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 記憶體管理員的所有記憶體需求，提供了更準確的調整大小功能。 

> [!IMPORTANT]
> 請在升級到 [!INCLUDE[ssSQL11](../includes/sssql11-md.md)] 至 [!INCLUDE[ssCurrent](../includes/sscurrent-md.md)]之後，仔細檢閱目前的 [最大伺服器記憶體 (MB)] 與 [最小伺服器記憶體 (MB)] 設定。 這是因為從 [!INCLUDE[ssSQL11](../includes/sssql11-md.md)] 開始，這類設定目前所包含且納入記憶體配置，較舊版來得多。 這些變更適用 32 位元及 64 位元版本的 [!INCLUDE[ssSQL11](../includes/sssql11-md.md)] 與 [!INCLUDE[ssSQL14](../includes/sssql14-md.md)]，以及 64 位元版本的 [!INCLUDE[ssSQL15](../includes/sssql16-md.md)] 到 [!INCLUDE[ssCurrent](../includes/sscurrent-md.md)]。

下表指出特定類型的記憶體配置是否受 [最大伺服器記憶體 (MB)] 與 [最小伺服器記憶體 (MB)] 設定選項的控制：

|記憶體配置類型| [!INCLUDE[ssVersion2005](../includes/ssversion2005-md.md)]、[!INCLUDE[ssKatmai](../includes/ssKatmai-md.md)] 及 [!INCLUDE[ssKilimanjaro](../includes/ssKilimanjaro-md.md)]| 從 [!INCLUDE[ssSQL11](../includes/sssql11-md.md)] 開始|
|-------|-------|-------|
|單頁配置|是|是，已整合為「任何大小」分頁配置|
|多頁配置|否|是，已整合為「任何大小」分頁配置|
|CLR 配置|否|是|
|執行緒堆疊記憶體|否|否|
|Windows 直接配置|否|否|

從 [!INCLUDE[ssSQL11](../includes/sssql11-md.md)] 開始，[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 配置的記憶體可能會超過最大伺服器記憶體設定中所指定的值。 當 [總伺服器記憶體 (KB)] 值已經超過 [目標伺服器記憶體 (KB)] 設定 (由最大伺服器記憶體指定) 時，即可能出現此行為。 如因記憶體分散而導致連續的可用記憶體不足以符合多頁記憶體要求的需求 (大於 8 KB)，[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 仍可超額配置而不拒絕記憶體要求。 

執行配置之後，*資源監視器* 背景工作會開始對所有記憶體取用者要求釋放配置的記憶體，並會嘗試將 [總伺服器記憶體 (KB)] 值降至 [目標伺服器記憶體 (KB)] 規格之下。 因此，[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 的記憶體使用量可能會短時間超過伺服器記憶體設定的上限。 在此情況下，[總伺服器記憶體 (KB)] 效能計數器會讀取超過最大伺服器記憶體及 [目標伺服器記憶體 (KB)] 設定。

下列作業期間通常會觀察到此行為： 
-  大型資料行存放區索引查詢。
-  資料行存放區的重建/建置，這會使用大量的記憶體來執行雜湊與排序作業。
-  需要大型記憶體緩衝區的備份作業。
-  需要儲存大量輸入參數的追蹤作業。

<a name="#changes-to-memory-management-starting-with-includesssql11includessssql11-mdmd"></a>
## <a name="changes-to-memory_to_reserve-starting-with-sssql11"></a>從 [!INCLUDE[ssSQL11](../includes/sssql11-md.md)] 開始對 "memory_to_reserve" 所進行的變更
在舊版 SQL Server ([!INCLUDE[ssVersion2005](../includes/ssversion2005-md.md)]、[!INCLUDE[ssKatmai](../includes/ssKatmai-md.md)] 及 [!INCLUDE[ssKilimanjaro](../includes/ssKilimanjaro-md.md)]) 中，[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 記憶體管理員保留了一部分處理序虛擬位址空間 (VAS)，供 **多頁配置器 (MPA)** 、**CLR 配置器**、SQL Server 處理序中 **執行緒堆疊** 的記憶體配置，以及 **直接 Windows 配置 (DWA)** 使用。 這部分的虛擬位址空間又稱為「假釋記憶體」(Mem-To-Leave) 或「非緩衝集區」區域。

為這些配置保留的虛擬位址空間會依 _**memory\_to\_reserve**_ 設定選項而定。 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 使用的預設值為 256 MB。 若要覆寫預設值，請使用 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] *-g* 啟動參數。 如需 [-g](../database-engine/configure-windows/database-engine-service-startup-options.md) 啟動參數的詳細資訊，請參閱 *資料庫引擎服務啟動選項* 的文件頁面。

因為從 [!INCLUDE[ssSQL11](../includes/sssql11-md.md)] 開始，新的「任何大小」分頁配置器也會處理大於 8 KB 的配置，所以 *memory_to_reserve* 值不會包含多頁配置。 除此變更之外，此設定選項的其餘項目皆維持原狀。

下表指出特定類型的記憶體配置是否屬於 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]處理序之虛擬位址空間的 *memory_to_reserve* 區域：

|記憶體配置類型| [!INCLUDE[ssVersion2005](../includes/ssversion2005-md.md)]、[!INCLUDE[ssKatmai](../includes/ssKatmai-md.md)] 及 [!INCLUDE[ssKilimanjaro](../includes/ssKilimanjaro-md.md)]| 從 [!INCLUDE[ssSQL11](../includes/sssql11-md.md)] 開始|
|-------|-------|-------|
|單頁配置|否|否，已整合為「任何大小」分頁配置|
|多頁配置|是|否，已整合為「任何大小」分頁配置|
|CLR 配置|是|是|
|執行緒堆疊記憶體|是|是|
|Windows 直接配置|是|是|

## <a name="dynamic-memory-management"></a><a name="dynamic-memory-management"></a> 動態記憶體管理
[!INCLUDE[ssDEnoversion](../includes/ssdenoversion-md.md)] 的預設記憶體管理行為是以不造成系統發生記憶體短缺為前提，盡可能取得所需的記憶體。 [!INCLUDE[ssDEnoversion](../includes/ssdenoversion-md.md)] 會使用 Microsoft Windows 的「記憶體通知 API」來達成此目的。

當 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 動態使用記憶體時，它會定期查詢系統以判定可用的記憶體量。 維持這個可用記憶體數量可避免作業系統 (OS) 進行分頁。 如果可用記憶體少於這個數量， [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 會將記憶體釋出給 OS。 如果可用記憶體多於這個數量， [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 可能會配置更多記憶體。 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 只有當工作負載需要更多的記憶體時，它才會增加記憶體。休息中的伺服器不會增加其虛擬位址空間的大小。  
  
**[Max server memory](../database-engine/configure-windows/server-memory-server-configuration-options.md)** 控制 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 記憶體配置、編譯記憶體、所有快取 (包括緩衝集區)、[查詢執行記憶體授與](#effects-of-min-memory-per-query)、[鎖定管理員記憶體](#memory-used-by-sql-server-objects-specifications)，以及 CLR <sup>1</sup> 記憶體 (基本上任何在 **[sys.dm_os_memory_clerks](../relational-databases/system-dynamic-management-views/sys-dm-os-memory-clerks-transact-sql.md)** 中找到的記憶體 Clerk 皆是)。 

<sup>1</sup> 從 [!INCLUDE[ssSQL11](../includes/sssql11-md.md)] 開始，CLR 記憶體由 max_server_memory 配置進行管理。

以下查詢會傳回目前所配置之記憶體的相關資訊：  
  
```sql  
SELECT 
  physical_memory_in_use_kb/1024 AS sql_physical_memory_in_use_MB, 
    large_page_allocations_kb/1024 AS sql_large_page_allocations_MB, 
    locked_page_allocations_kb/1024 AS sql_locked_page_allocations_MB,
    virtual_address_space_reserved_kb/1024 AS sql_VAS_reserved_MB, 
    virtual_address_space_committed_kb/1024 AS sql_VAS_committed_MB, 
    virtual_address_space_available_kb/1024 AS sql_VAS_available_MB,
    page_fault_count AS sql_page_fault_count,
    memory_utilization_percentage AS sql_memory_utilization_percentage, 
    process_physical_memory_low AS sql_process_physical_memory_low, 
    process_virtual_memory_low AS sql_process_virtual_memory_low
FROM sys.dm_os_process_memory;  
```  
 
<a name="stacksizes"></a> 執行緒堆疊的記憶體 <sup>1</sup>、CLR <sup>2</sup>、擴充處理序 .dll 檔案、分散式查詢所參考的 OLE DB 提供者、[!INCLUDE[tsql](../includes/tsql-md.md)] 陳述式中參考的自動化物件，以及任何非 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] DLL 所配置的記憶體，皆 **不受** 伺服器記憶體上限的限制。

<sup>1</sup> 如需在目前主機中，為指定數量之親和 CPU 而計算出的預設背景工作執行緒相關資訊，請參閱如何[設定最大背景工作執行緒伺服器設定選項](../database-engine/configure-windows/configure-the-max-worker-threads-server-configuration-option.md)的文件頁面。 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 堆疊大小如下：

|SQL Server 架構|OS 架構|堆疊大小|  
|--------------------|----------------------|----------------------|
|x86 (32 位元)|x86 (32 位元)|512 KB|
|x86 (32 位元)|x64 (64 位元)|768 KB| 
|x64 (64 位元)|x64 (64 位元)|2048 KB|
|IA64 (Itanium)|IA64 (Itanium)|4096 KB|

<sup>2</sup> 從 [!INCLUDE[ssSQL11](../includes/sssql11-md.md)] 開始，CLR 記憶體由 max_server_memory 配置進行管理。

[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 使用記憶體通知 API **QueryMemoryResourceNotification**，判定 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 記憶體管理員何時要配置記憶體以及釋放記憶體。  

[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 啟動後，會根據系統上的參數量 (如實體記憶體量)、伺服器執行緒數量和許多的啟動參數，計算緩衝集區的虛擬位址空間的大小。 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 會為緩衝集區保留計算所得的處理序虛擬位址空間量，但它只會取得 (認可) 目前負載所需的實體記憶體數量。

該執行個體接著會繼續視需要取得記憶體，來支援工作負載。 當有更多使用者連接和執行查詢時， [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 會依需要取得其他實體記憶體。 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 執行個體會持續取得實體記憶體，直到達到它的最大伺服器記憶體配置目標，或直到 OS 指出已無可用記憶體；當擁有的記憶體多於最小伺服器記憶體設定，且 OS 指出可用記憶體短缺時，它就會釋出記憶體。 

當其他應用程式開始在執行 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]執行個體的電腦上執行時，它們會消耗記憶體，並使可用實體記憶體的數量掉到 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 目標以下。 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 執行個體會調整它的記憶體耗用量。 如果其他應用程式停止而有更多記憶體可供使用， [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 執行個體就會增加其記憶體配置的大小。 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 每秒都可釋放及取得數 MB 的記憶體，使它能隨記憶體配置的變更快速調整。

## <a name="effects-of-min-and-max-server-memory"></a>最小與最大伺服器記憶體的作用
*min server memory* 和 *max server memory* 設定選項，會建立緩衝集區及 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 資料庫引擎的其他快取所使用之記憶體數量的上限與下限。 緩衝集區不會立即取得最小伺服器記憶體中指定的記憶體數量。 緩衝集區只會以初始化所需的記憶體啟動。 當 [!INCLUDE[ssDEnoversion](../includes/ssdenoversion-md.md)] 的工作負載增加時，它會一再取得支援工作負載所需的記憶體。 除非緩衝集區達到最小伺服器記憶體中指定的數量，否則它不會釋放取得的任何記憶體。 一旦達到最小伺服器記憶體，緩衝集區便會使用標準演算法來視需要取得及釋放記憶體。 唯一的差別在於緩衝集區絕不會將其記憶體配置降到最小伺服器記憶體中指定的數量以下，也絕不會取得比最大伺服器記憶體中指定的數量還多的記憶體。

> [!NOTE]
> 做為處理序的[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] ，需要比最大伺服器記憶體選項所指定還多的記憶體。 內部和外部元件皆可在緩衝集區之外配置記憶體 ，這會取用其他記憶體，但是配置給緩衝集區的記憶體通常仍代表 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 所取用之記憶體的最大部份。

[!INCLUDE[ssDEnoversion](../includes/ssdenoversion-md.md)] 取得的記憶體數量完全是依據執行個體的工作負載而定。 如果 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 執行個體不需處理許多要求，可能永遠也不會達到最小伺服器記憶體。

如果為最小伺服器記憶體和最大伺服器記憶體指定相同的值，則一旦配置給 [!INCLUDE[ssDEnoversion](../includes/ssdenoversion-md.md)] 的記憶體達到該值，[!INCLUDE[ssDEnoversion](../includes/ssdenoversion-md.md)] 就會停止動態釋放及取得緩衝集區的記憶體。

如果 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 的執行個體正在電腦上執行，而且電腦上有其他應用程式頻繁地停止或啟動，則 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 執行個體配置和取消配置記憶體，可能會延遲其他應用程式啟動的時間。 此外，如果 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 是在單一電腦上執行的數個伺服器應用程式之一，系統管理員可能需要控制配置給 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]的記憶體數量。 在這些情況下，您可以使用最小伺服器記憶體和最大伺服器記憶體選項，來控制 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 可以使用的記憶體數量。 [最小伺服器記憶體] 與 [最大伺服器記憶體] 選項以 MB 指定。 如需詳細資訊，請參閱 [伺服器記憶體組態選項](../database-engine/configure-windows/server-memory-server-configuration-options.md)。

## <a name="memory-used-by-sql-server-objects-specifications"></a>SQL Server 物件使用記憶體的規格
下列清單將描述 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]中不同物件所使用的記憶體大約數量。 所列的數量為估計值，且會因環境及建立物件的方式而有所不同：

* 鎖定 (由鎖定管理員保留)：64 位元組 + 每位擁有者 32 位元組   
* 使用者連線：大約是 (3 \* network_packet_size + 94 KB)    

**網路封包大小** 是表格式資料配置 (TDS) 封包的大小，用於在應用程式和 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 資料庫引擎之間進行通訊。 預設封包大小是 4 KB，由 network packet size 組態選項所控制。

啟用多個使用中結果集 (MARS) 時，使用者連接大約是 (3 + 3 \*num_logical_connections)\* network_packet_size + 94 KB

## <a name="effects-of-min-memory-per-query"></a>min memory per query 的作用
*min memory per query* 設定選項會建立為執行查詢所配置的最小記憶體數量 (以 KB 為單位)。 這也稱為最小記憶體授與。 所有查詢都必須等到可取得要求的最小記憶體、執行可以開始前，或超過查詢等候伺服器設定選項中指定的值為止。 在此情況下累積的等候類型為 RESOURCE_SEMAPHORE。

> [!IMPORTANT]
> 請勿將 min memory per query 伺服器設定選項設得太高，特別是在非常忙碌的系統上，因為這樣做可能會導致：         
> - 記憶體資源的競用情況增加。         
> - 藉由增加每個單一查詢的記憶體數量會減少並行處理，即使執行階段所需的記憶體比此設定低也一樣。    
>    
> 如需使用此設定的建議，請參閱[設定 min memory per query 伺服器設定選項](../database-engine/configure-windows/configure-the-min-memory-per-query-server-configuration-option.md#Recommendations)。

### <a name="memory-grant-considerations"></a><a name="memory-grant-considerations"></a>記憶體授與考量
針對 **資料列模式執行**，在任何情況下，都不得超過初始記憶體授與。 如果需要比初始授與更多的記憶體才能執行 **雜湊** 或 **排序** 作業，則這些作業會溢出至磁碟。 溢出的雜湊作業受到 TempDB 中之工作檔案的支援，而溢出的排序作業則受到[工作資料表](../relational-databases/query-processing-architecture-guide.md#worktables)的支援。   

排序作業期間發生的溢出稱為[排序警告](../relational-databases/event-classes/sort-warnings-event-class.md)。 排序警告會指出不符合記憶體的排序作業。 不包括關於索引建立的排序作業，只包括在查詢中 (例如在 `SELECT` 陳述式中所用之 `ORDER BY` 子句) 的排序作業。

雜湊作業期間發生的溢出稱為[雜湊警告](../relational-databases/event-classes/hash-warning-event-class.md)。 當雜湊作業期間發生雜湊遞迴或雜湊停止 (雜湊釋出) 時，就會發生這些情況。
-  當建立輸入不符合可用記憶體時，會發生雜湊遞迴，因而將輸入分割為多個需要個別處理的資料分割。 如果這些資料分割中仍有任何資料分割不符合可用記憶體，則這些資料分割會再分為子資料分割，仍是需要個別處理。 這個分割處理會繼續進行，直到每個資料分割都符合可用記憶體或達到最大遞迴層級為止。
-  當雜湊作業達到最大遞迴等級時會發生 Hash Bailout，且切換到其他計畫以處理其他的分割資料。 這些事件會導致您的伺服器效能降低。

針對 **批次模式執行**，根據預設，初始記憶體授與最高可動態增加到特定內部閾值。 此動態記憶體授與機制的設計是為了允許 **雜湊** 或 **排序** 作業的記憶體駐留執行在批次模式中執行。 如果這些作業仍不符合記憶體，則會溢出至磁碟。

如需執行模式的詳細資訊，請參閱[查詢處理架構指南](../relational-databases/query-processing-architecture-guide.md#execution-modes)。

## <a name="buffer-management"></a>緩衝區管理
[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 資料庫的主要用途是為了儲存和擷取資料，因此大量磁碟 I/O 是 Database Engine 的核心特性。 此外，因為磁碟 I/O 作業會秏用許多資源，而且相對上需要較長的時間才能完成，所以 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 非常著重提高 I/O 的效率。 緩衝區管理是達成這種效率的重要元件。 緩衝區管理元件包含兩種機制：可存取及更新資料庫分頁的 **緩衝區管理員**，以及可降低資料庫檔案 I/O 的 **緩衝快取** (也稱為 **緩衝集區**)。 

### <a name="how-buffer-management-works"></a>緩衝區管理如何運作
緩衝區是 8 KB 的記憶體分頁，大小與資料或索引頁相同。 因此，緩衝區快取會分成 8 KB 的分頁。 緩衝區管理員管理從資料庫磁碟檔案將資料或索引頁面讀取到緩衝快取中，以及將修改後頁面重新寫入磁碟的功能。 頁面會保留在緩衝區快取中，直到緩衝區管理員需要緩衝區來讀取更多資料為止。 只有資料修改後，才會重新寫入磁碟。 在重新寫入磁碟之前，可以多次修改緩衝區快取中的資料。 如需詳細資訊，請參閱 [讀取分頁](../relational-databases/reading-pages.md) 和 [寫入分頁](../relational-databases/writing-pages.md)。

當 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 啟動時，會根據數種參數 (例如，系統上的實體記憶體數量、設定的最大伺服器執行緒數量，以及各種啟動參數)，計算緩衝區快取的虛擬位址空間大小。 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 會為緩衝區快取保留此計算所得的處理序虛擬位址空間量 (稱為記憶體目標)，但它只會取得 (認可) 目前負載所需的實體記憶體量。 您可以在 **sys.dm_os_sys_info** 目錄檢視中查詢 **bpool_commit_target** 和 [bpool_committed](../relational-databases/system-dynamic-management-views/sys-dm-os-sys-info-transact-sql.md) 資料行，以分別傳回保留為記憶體目標的頁數和緩衝區快取中目前認可的頁數。

在 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 啟動時與緩衝區快取取得其記憶體目標時當中的間隔稱為「擴置」(Ramp-up)。 在這段期間，讀取要求會依需要填滿緩衝區。 例如，讀取單頁 8 KB 的要求會填滿單一緩衝區分頁。 這表示擴置是依據用戶端要求的數目和類型而定。 擴大會將讀取單頁要求，轉換成對齊的八頁要求 (組成一個範圍) 來加速。 這可讓擴置加快完成，特別是在具有大量記憶體的電腦上。 如需分頁及範圍的詳細資訊，請參閱[分頁與範圍架構指南](../relational-databases/pages-and-extents-architecture-guide.md#pages-and-extents)。

因為緩衝區管理員要使用 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 處理序中大部分的記憶體，所以會與記憶體管理員合作以允許其他元件使用其緩衝區。 緩衝區管理員主要與下列元件進行互動：

* 資源管理員，以控制整個記憶體的使用量，而在 32 位元平台中，還要控制位址空間使用量。  
* 資料庫管理員和 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 作業系統 (SQLOS)，以進行低階檔案 I/O 作業。  
* 記錄檔管理員，以處理預寫記錄檔。  

### <a name="supported-features"></a>支援的功能
緩衝區管理員支援下列功能：

* 緩衝區管理員可知曉 **非統一記憶體存取 (NUMA)** 。 緩衝快取頁面會分散到硬體 NUMA 節點，這可讓執行緒存取本機 NUMA 節點上配置的緩衝區頁面，而不是從外部記憶體中存取。 
* 緩衝區管理員支援 [熱新增記憶體]，讓使用者無須重新啟動伺服器即可增加實體記憶體。 
* 緩衝區管理員在 64 位元平台上支援 **大型分頁**。 Windows 的不同版本各有特定的頁面大小。

  > [!NOTE]
  > 在 [!INCLUDE[ssSQL11](../includes/sssql11-md.md)] 以前，在 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 中建立大型分頁需要[追蹤旗標 834](../t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql.md)。  

* 緩衝區管理員提供可透過動態管理檢視公開的額外診斷資訊。 您可以使用這些檢視來監視 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]特定的各種作業系統資源。 例如，您可使用 [sys.dm_os_buffer_descriptors](../relational-databases/system-dynamic-management-views/sys-dm-os-buffer-descriptors-transact-sql.md) 檢視來監視緩衝區快取中的分頁。   

### <a name="disk-io"></a>磁碟 I/O
緩衝區管理員僅針對資料庫執行讀取和寫入。 其他檔案及資料庫作業，如開啟、關閉、擴充和壓縮，都是由資料庫管理員和檔案管理員元件來執行。 

緩衝區管理員執行的磁碟 I/O 作業具有下列特性：

* 所有的 I/O 都是以非同步的方式進行，這可讓呼叫執行緒繼續進行處理，而 I/O 作業則同時於背景中執行。
* 除非使用了關連 I/O 選項，否則所有的 I/O 都是在呼叫執行緒中發出。 affinity I/O mask 選項會將 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 磁碟 I/O 繫結到指定的 CPU 子集。 在高階的 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 線上交易處理 (OLTP) 環境中，此延伸模組可強化 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 執行緒發行 I/O 的效能。
* 多頁 I/O 是透過散佈 - 收集 I/O 來完成，可讓資料轉入或轉出非連續的記憶體區域。 這表示 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 可以快速填滿或排清緩衝快取，同時還能避免多個實體 I/O 要求。 

#### <a name="long-io-requests"></a>長 I/O 要求  
緩衝區管理員會報告任何至少持續 15 秒仍未處理的 I/O 要求。 這樣可以協助系統管理員區別 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 問題和 I/O 子系統問題。 報告錯誤訊息 833，並會出現在 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 錯誤記錄檔中，如下：

`SQL Server has encountered ## occurrence(s) of I/O requests taking longer than 15 seconds to complete on file [##] in database [##] (#). The OS file handle is 0x00000. The offset of the latest long I/O is: 0x00000.` 

長 I/O 可能是讀取或寫入動作；不會在目前的訊息中指出。 長 I/O 訊息是警告，而非錯誤。 它們不會指出 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 的問題，而是指出基礎 I/O 系統的問題。 報告這些訊息的目的是在協助系統管理員更快找到 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 回應時間遲緩的原因，並區別 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]控制範圍之外的問題。 因此，不需要對它們採取任何動作，但是系統管理員應該調查 I/O 要求為何會用那麼久的時間，以及這段時間是否合理。

#### <a name="causes-of-long-io-requests"></a>長 I/O 要求的原因  
長 I/O 訊息可能指出 I/O 已遭永久封鎖而永遠無法完成 (稱為遺失 I/O)，或者只是尚未完成。 雖然遺失 I/O 經常導致閂鎖逾時，但也無法從訊息中辨別實際狀況為何。

長 I/O 通常表示 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 工作負載對磁碟子系統而言有些過重。 出現下列情形時，可能表示磁碟子系統已經不勝負荷：

* 在繁重的 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 工作負載期間，錯誤記錄出現多則長 I/O 訊息。
* Perfmon 計數器顯示長時間的磁碟延遲、冗長的磁碟佇列或沒有磁碟閒置時間。  

長 I/O 也可能是 I/O 路徑中的元件 (例如，驅動程式、控制器或韌體) 所造成，因為這個元件持續延後對舊有 I/O 要求的服務，而優先服務較接近磁碟讀寫頭目前位置的較新要求。 一般以最接近讀寫頭目前位置的要求為優先處理的技術即稱為「電梯式尋軌」(Elevator Seeking)。 這就很難用 Windows 系統監視器 (PERFMON.EXE) 工具來證實，因為多數情況下都應該立即服務 I/O 作業才對。 執行大量循序 I/O 的工作負載，如備份與還原、資料表掃描、排序、建立索引、大量載入以及清空檔案等作業，會使長 I/O 要求的情況惡化。

顯然與上述任何情況無關的孤立長 I/O，可能是因硬體或驅動程式問題所引起。 系統事件記錄檔可能會包含有助於診斷問題的相關事件。

### <a name="memory-pressure-detection"></a>記憶體壓力偵測
記憶體壓力是記憶體短缺所造成的情況，可能會導致：
- 額外的 I/O (例如非常活躍的延遲寫入器背景執行緒)
- 重新編譯率偏高
- 查詢的執行時間變長 (如果存在記憶體授與等候)
- 額外的 CPU 週期

觸發此情況的原因可能來自外部，也可能來自內部。 外部原因包括：
- 可用的實體記憶體 (RAM) 不足。 這會導致系統修剪目前執行中處理序的工作集，而可能造成整體速度變慢。 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 可能會縮減緩衝集區的認可目標，並開始更頻繁地修剪內部快取。 
- 整體可用的系統記憶體 (包括系統分頁檔) 不足。 這可能會導致系統記憶體配置失敗，因為它無法分頁目前已配置的記憶體。
內部原因包括：
- 當 [!INCLUDE[ssDEnoversion](../includes/ssdenoversion-md.md)] 設定較低的記憶體使用量上限時，回應外部記憶體壓力。
- 記憶體設定已透過降低 *max server memory* 設定手動降低。 
- 數個快取之間的內部元件記憶體分配有所變更。

[!INCLUDE[ssDEnoversion](../includes/ssdenoversion-md.md)] 會實作專門用來偵測及處理記憶體壓力的架構，以作為其動態記憶體管理的一部分。 此架構包含稱為 **資源監視器** 的背景工作。 資源監視器工作會監視外部和內部記憶體指標的狀態。 一旦其中一個指標變更狀態，就會計算對應的通知並予以廣播。 這些通知是來自每個引擎元件的內部訊息，並會儲存在信號緩衝區中。 

有兩個信號緩衝區會保存與動態記憶體管理相關的資訊： 
- 資源監視器信號緩衝區，它會追蹤資源監視器活動，例如是否發出記憶體壓力信號。 此信號緩衝區會根據目前條件為 *RESOURCE_MEMPHYSICAL_HIGH*、*RESOURCE_MEMPHYSICAL_LOW*、*RESOURCE_MEMPHYSICAL_STEADY* 或 *RESOURCE_MEMVIRTUAL_LOW* 來包含狀態資訊。
- 記憶 Broker 信號緩衝區，其中包含每個 Resource Governor 資源集區的記憶體通知記錄。 偵測到內部記憶體壓力時，系統會針對配置記憶體的元件開啟記憶體不足通知，以觸發動作來平衡快取之間的記憶體。 

記憶 Broker 會監視每個元件的記憶體使用量需求，然後根據所收集的資訊，計算每個元件的最佳記憶體值。 每個 Resource Governor 資源集區會有一組 Broker。 這項資訊會接著廣播到每個元件，並視需要縮放其使用量。
如需記憶 Broker 的詳細資訊，請參閱 [sys.dm_os_memory_brokers](../relational-databases/system-dynamic-management-views/sys-dm-os-memory-brokers-transact-sql.md)。 

### <a name="error-detection"></a>錯誤偵測  
資料庫頁面可以使用兩個選用機制 (損毀頁保護與總和檢查碼保護) 的其中一個，來確保頁面從寫入磁碟一直到再次被讀取的完整性。 這些機制讓獨立的方法，不僅可以確認資料儲存媒體的正確性，而且也可以驗證硬體元件，如控制器、驅動程式、纜線，甚至作業系統。 這個保護會在頁面寫入磁碟之前加諸頁面，並在從磁碟讀取頁面之後進行驗證。

[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 會重試任何因總和檢查碼、損毀頁或其他 I/O 錯誤而失敗的讀取作業四次。 如果任何一次重試讀取成功，都會將訊息寫入錯誤記錄檔中，且會繼續觸發讀取作業的命令。 如果重試失敗，此命令便會失敗，且會出現錯誤訊息 824。 

頁面使用的保護類型是含有該頁面之資料庫的屬性。 總和檢查碼保護是在 [!INCLUDE[ssVersion2005](../includes/ssversion2005-md.md)] 和更新版本中建立之資料庫的預設保護。 分頁保護機制指定於資料庫建立期間，且可使用 ALTER DATABASE 加以變更。 您可查詢 [sys.databases](../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中的 *page_verify_option* 資料行，或查詢 [DATABASEPROPERTYEX](../t-sql/functions/databasepropertyex-transact-sql.md) 函式的 *IsTornPageDetectionEnabled* 屬性，來判斷目前的分頁保護設定。 

> [!NOTE]
> 如果變更頁面保護設定，新的設定並不會立即影響整個資料庫。 反而每當頁面有資料寫入時，這些頁面還是會採用目前的資料庫保護等級。 這表示資料庫可能會由具有不同保護類型的頁面組成。 

#### <a name="torn-page-protection"></a>損毀頁保護  
在 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 2000 中導入的損毀頁保護，是偵測因電源故障而導致分頁損毀的主要方法。 例如，非預期的電源故障可能造成只有部分的頁面寫入磁碟。 使用損毀頁保護時，會在分頁寫入磁碟時，將 8 KB 的資料庫分頁中的每 512 位元組磁區之特定 2 位元簽章模式，儲存至資料庫頁首中。 當從磁碟中讀取頁面時，會比較頁首中所儲存的損毀位元和實際的頁面磁區資訊。 簽章模式會在每次寫入時交替使用二進位數 01 與 10，因此總是可以判斷何時未將磁區完整寫入磁碟：如果稍後讀取分頁時，位元處於錯誤的狀態，即代表分頁寫入不正確，因而偵測到損毀頁。 損毀頁偵測會使用最少的資源，但是無法偵測磁碟硬體故障引起的所有錯誤。 如需設定損毀頁偵測的資訊，請參閱 [ALTER DATABASE SET Options &#40;Transact-SQL&#41;](../t-sql/statements/alter-database-transact-sql-set-options.md#page_verify)。

#### <a name="checksum-protection"></a>總和檢查碼保護  
在 [!INCLUDE[ssVersion2005](../includes/ssversion2005-md.md)] 中導入的「總和檢查碼保護」會提供更強的資料完整性檢查。 總和檢查碼會針對每個頁面中寫入的資料來計算，並且儲存在頁面標頭中。 每當從磁碟讀取具有已儲存總和檢查碼的頁面時，資料庫引擎會在頁面中重新計算資料的總和檢查碼，如果新的總和檢查碼與儲存的總和檢查碼不同，即引發錯誤 824。 總和檢查碼保護可以比損毀頁保護捕捉更多的錯誤，因為頁面的每個位元組都會牽動它，不過相對上也需要大量的資源。 啟用總和檢查碼時，緩衝區管理員任何時間從磁碟讀取頁面，都能偵測到因電源故障和有缺陷硬體或韌體所造成的錯誤。 如需設定總和檢查碼的資訊，請參閱 [ALTER DATABASE SET Options &#40;Transact-SQL&#41;](../t-sql/statements/alter-database-transact-sql-set-options.md#page_verify)。

> [!IMPORTANT]
> 將使用者或系統資料庫升級為 [!INCLUDE[ssVersion2005](../includes/ssversion2005-md.md)] 或更新版本時，會保留 [PAGE_VERIFY](../t-sql/statements/alter-database-transact-sql-set-options.md#page_verify) 值 (NONE 或 TORN_PAGE_DETECTION)。 我們強烈建議您使用 CHECKSUM。
> TORN_PAGE_DETECTION 可以使用較少資源，但所提供的 CHECKSUM 保護最少。

## <a name="understanding-non-uniform-memory-access"></a>了解非統一記憶體存取
[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 是非統一記憶體存取 (NUMA) 感知，不需要特殊組態就可以在 NUMA 硬體上順利執行。 隨著處理器時脈和數目的增加，要降低使用此額外處理能力所需要的記憶體延遲變得越來越困難。 為了避免這個狀況，硬體供應商提供了大型的 L3 快取，但這只是有限的解決方案。 NUMA 架構對這個問題提供了可擴充的解決方案。 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 已設計成可利用 NUMA 型電腦的優點，而不需要進行任何應用程式變更。 如需詳細資訊，請參閱[如何：設定 SQL Server 使用軟體 NUMA](../database-engine/configure-windows/soft-numa-sql-server.md)。

## <a name="dynamic-partition-of-memory-objects"></a>記憶體物件的動態分割
堆積配置器 (在 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 中稱為記憶體物件) 可讓 [!INCLUDE[ssde_md](../includes/ssde_md.md)] 從堆積配置記憶體。 您可以使用 [sys.dm_os_memory_objects](../relational-databases/system-dynamic-management-views/sys-dm-os-memory-objects-transact-sql.md) DMV 來追蹤這些物件。 CMemThread 是安全執行緒的記憶體物件類型，允許從多個執行緒進行並行記憶體配置。 為了正確追蹤，CMemThread 物件依賴同步處理建構 (Mutex) 來確保一次只有一個執行緒更新重要的資訊片段。 

> [!NOTE]
> CMemThread 物件類型會在許多不同配置的 [!INCLUDE[ssde_md](../includes/ssde_md.md)] 程式碼基底中使用，而且可以透過節點或 CPU 進行全域分割。   

不過，如果有許多執行緒是以高度並行的方式從相同的記憶體物件配置，則 mutex 的使用可能會導致爭用。 因此，[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 具有分割記憶體物件 (PMO) 的概念，而且每個資料分割都是由單一 CMemThread 物件來表示。 記憶體物件的資料分割會以靜態方式定義，而且無法在建立後變更。 由於記憶體配置模式根據硬體和記憶體使用量等方面而有很大的差異，因此無法預先提供完美的資料分割模式。 在大部分的情況下，使用單一分割區就已足夠，但在某些情況下，這可能會導致爭用，而只有在高度分割的記憶體物件上才可避免爭用發生。 不建議將每個記憶體物件都加以分割，因為多個磁碟分割區可能會導致其他部分效率不足並增加記憶體片段。

> [!NOTE]
> 在 [!INCLUDE[ssSQL15](../includes/sssql16-md.md)] 之前，追蹤旗標 8048 可用來強制以節點為基礎的 PMO 成為以 CPU 為基礎的 PMO。 從 [!INCLUDE[ssSQL14](../includes/sssql14-md.md)] SP2 和 [!INCLUDE[ssSQL15](../includes/sssql16-md.md)] 開始，此行為是動態的且由引擎所控制。

從 [!INCLUDE[ssSQL14](../includes/sssql14-md.md)] SP2 和 [!INCLUDE[ssSQL15](../includes/sssql16-md.md)] 開始，[!INCLUDE[ssde_md](../includes/ssde_md.md)] 可以動態偵測特定 CMemThread 物件上的爭用，並將物件升階為以每個節點或每個 CPU 為基礎的實作。  一旦升階之後，PMO 就會保持升階，直到 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 程序重新開始為止。 [sys.dm_os_wait_stats](../relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql.md) DMV 中是否有高 CMEMTHREAD 等候，以及透過觀察 [sys.dm_os_memory_objects](../relational-databases/system-dynamic-management-views/sys-dm-os-memory-objects-transact-sql.md) DMV 資料行 *contention_factor*、*partition_type*、*exclusive_allocations_count* 和 *waiting_tasks_count*，都可以偵測到 CMemThread 爭用。

## <a name="see-also"></a>另請參閱
[伺服器記憶體伺服器組態選項](../database-engine/configure-windows/server-memory-server-configuration-options.md)   
[讀取分頁](../relational-databases/reading-pages.md)   
[寫入分頁](../relational-databases/writing-pages.md)   
[操作說明：設定 SQL Server 使用軟體 NUMA](../database-engine/configure-windows/soft-numa-sql-server.md)   
[使用記憶體最佳化資料表的需求](../relational-databases/in-memory-oltp/requirements-for-using-memory-optimized-tables.md)   
[使用記憶體最佳化資料表解決記憶體不足的問題](../relational-databases/in-memory-oltp/resolve-out-of-memory-issues.md)