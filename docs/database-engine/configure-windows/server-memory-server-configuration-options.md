---
title: 伺服器記憶體組態選項 | Microsoft Docs
ms.custom: contperf-fy20q4
ms.date: 08/14/2019
ms.prod: sql
ms.prod_service: high-availability
ms.reviewer: ''
ms.technology: configuration
ms.topic: conceptual
description: 了解如何設定 SQL Server 記憶體管理員配置給 SQL Server 處理序的記憶體數量。 檢視記憶體管理方法和範例。
helpviewer_keywords:
- Virtual Memory Manager
- max server memory option
- virtual memory [SQL Server]
- VMM
- server memory options [SQL Server]
- servers [SQL Server], memory
- buffer pools [SQL Server]
- min server memory option
- manual memory options [SQL Server]
- memory [SQL Server], servers
ms.assetid: 29ce373e-18f8-46ff-aea6-15bbb10fb9c2
author: pmasl
ms.author: pelopes
ms.openlocfilehash: 71cab9c11aaa4f3dc569761fdfef407601162b1c
ms.sourcegitcommit: cb8e2ce950d8199470ff1259c9430f0560f0dc1d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2021
ms.locfileid: "97878899"
---
# <a name="server-memory-configuration-options"></a>伺服器記憶體組態選項
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

重新設定 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體所使用之 SQL Server 處理序的記憶體數量 (以 MB 為單位)。  有兩個伺服器記憶體選項：**最小伺服器記憶體** 和 **最大伺服器記憶體**。 這些選項會變更 SQL Server 記憶體管理員配置給 SQL Server 處理序的記憶體數量。
  
這些選項的預設設定和最小允許值如下：

|選項  |  預設 | 允許的最小值  |
|---------|---------|---------|
|**min server memory**     |    0     |    0     |
|**max server memory**     |     2,147,483,647 MB     |  128 MB       |

根據預設，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 可以根據可用的系統資源，動態變更其記憶體需求。 如需詳細資訊，請參閱[動態記憶體管理](../../relational-databases/memory-management-architecture-guide.md#dynamic-memory-management)。

> [!IMPORTANT]  
> 將 [最大伺服器記憶體] 的值設得太高，可能導致裝載於相同主機上的單一 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體必須與其他 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體競爭記憶體。 但若將此值設得太低，可能也會引起高度的記憶體壓力與效能問題。 將 [最大伺服器記憶體] 設為最小值甚至可能會讓 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 無法啟動。 如果您變更此選項後無法啟動 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]，請使用 **_-f_** 啟動選項予以啟動，並將 [最大伺服器記憶體] 重設為先前的值。 如需詳細資訊，請參閱 [Database Engine Service Startup Options](../../database-engine/configure-windows/database-engine-service-startup-options.md)。  
    
[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 可動態使用記憶體。但您可手動設定記憶體選項，並限制 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 可存取的記憶體數量。 在您設定 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的記憶體數量之前，要先決定適當的記憶體設定，方法是將實體記憶體總數減去 OS、不受 max_server_memory 設定所控制的記憶體配置，以及任何其他 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體 (以及當電腦不專用於 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]時，其他的系統用途) 所需要的記憶體。 此差額即為可指派給目前 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體的記憶體數量上限。  
 
## <a name="set-options-manually"></a><a name="manually"></a> 手動設定選項  
您可以將伺服器選項 [最小伺服器記憶體] 與 [最大伺服器記憶體] 設定成跨某範圍的記憶體值。 這樣的做法有助於讓系統或資料庫管理員，將 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體配合同一部電腦上執行的其他應用程式或其他 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體的記憶體需求，一併設定。

> [!NOTE]
> [最小伺服器記憶體] 和 [最大伺服器記憶體] 選項屬於進階選項。 如果您要使用 **sp_configure** 系統預存程序來變更這些設定，只有當 **show advanced options** 設為 1 時，才能變更它們。 這些設定會立即生效，不需要重新啟動伺服器。  
  
<a name="min_server_memory"></a> 您可使用 **min_server_memory** 確保 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 記憶體管理員，能有最少的記憶體數量可用。 啟動時，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 不會立即配置 [最小伺服器記憶體] 中指定的記憶體數量。 不過，由於用戶端負載使記憶體使用量達到這個值後，除非降低 [最小伺服器記憶體] 的值，否則 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 無法釋出記憶體。 例如，當多個 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體可以同時存在於相同的主機上時，設定 min_server_memory 參數而非 max_server_memory 可為執行個體保留記憶體。 此外，也需要設定虛擬環境中的 min_server_memory 值，以確保基礎主機的記憶體壓力不會為了得到可接受的效能，而嘗試從客體 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 虛擬機器 (VM) 的緩衝集區，解除超過所需的記憶體。

>[!NOTE]
>[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 不保證能夠配置 [最小伺服器記憶體] 中指定的記憶體數量。 如果伺服器的負載從不需要配置 [最小伺服器記憶體] 中指定的記憶體大小，則 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會以較少的記憶體執行。

<a name="max_server_memory"></a> 您可使用 **max_server_memory** 確保 OS 不會遇到有害的記憶體壓力。 若要設定最大伺服器記憶體設定，請監視 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 處理序的整體取用量，以判斷記憶體需求。 若要為單一執行個體進行更準確的計算：
- 從 OS 總記憶體中保留 1GB - 4GB 的記憶體給 OS 本身。
- 接著減去等於不受 [最大伺服器記憶體] 控制的潛在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 記憶體配置，算法為 **堆疊大小 <sup>1</sup> \* 計算得出的最大背景工作執行緒數 <sup>2</sup>** 。 餘數即為單一執行個體安裝的 max_server_memory 設定。

<sup>1</sup> 如需每個架構之執行緒堆疊大小的資訊，請參閱[記憶體管理架構指南](../../relational-databases/memory-management-architecture-guide.md#stacksizes)。

<sup>2</sup> 如需在目前主機中，為指定數量之親和 CPU 而計算出的預設背景工作執行緒數目相關資訊，請參閱如何[設定最大背景工作執行緒伺服器設定選項](../../database-engine/configure-windows/configure-the-max-worker-threads-server-configuration-option.md)的文件頁面。

## <a name="use-ssmanstudiofull"></a>使用[!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)]  
您可使用 [最小伺服器記憶體] 與 **[最大伺服器記憶體]** 這兩個伺服器記憶體選項，針對 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體重新設定 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 記憶體管理員所管理的記憶體數量 (MB)。 根據預設，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 可以根據可用的系統資源，動態變更其記憶體需求。  
  
### <a name="procedure-for-configuring-a-fixed-amount-of-memory-not-recommended"></a>設定固定記憶體數量的程序 (不建議)  
若要設定固定的記憶體數量：  
  
1.  在物件總管中，請以滑鼠右鍵按一下伺服器，然後選取 [屬性]。  
  
2.  按一下 **[記憶體]** 節點。  
  
3.  在 [伺服器記憶體選項] 底下，輸入 [最小伺服器記憶體] 和 [最大伺服器記憶體] 所要的數量。  
  
     使用預設值可允許 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 根據可用的系統資源來動態變更它的記憶體需求。 建議[如上詳述](#max_server_memory)，設定 [最大伺服器記憶體]。 

下列螢幕擷取畫面示範所有三個步驟： 

:::image type="content" source="media/server-memory-server-configuration-options/configure-memory-in-ssms.png" alt-text="設定 SSMS 中的記憶體":::

  
## <a name="lock-pages-in-memory-lpim"></a>鎖定記憶體中的分頁 (LPIM) 
此 Windows 原則決定哪些帳戶可以使用處理序將資料保留在實體記憶體中，以防止系統將資料傳送到磁碟上的虛擬記憶體。 將記憶體分頁到磁碟時，鎖定記憶體分頁可能會讓伺服器保持回應狀態。 在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Standard 版本或更新版本的執行個體中，當具有 sqlservr.exe 執行權限的帳戶取得 Windows「鎖定記憶體中的分頁」 (LPIM) 使用者權利時，[鎖定記憶體中的分頁] 選項會設定為 [開啟]。  
  
若要停用 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的 [鎖定記憶體中的分頁] 選項，請將具有 sqlservr.exe ([!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 啟動帳戶) 執行權限之帳戶的 [鎖定記憶體中的分頁] 使用者權利移除。  
 
設定此選項並不會影響 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] [動態記憶體管理](../../relational-databases/memory-management-architecture-guide.md#dynamic-memory-management)，使其可依其他記憶體 Clerk 的要求來擴充或縮減。 使用 [在記憶體中鎖定分頁] 使用者權利時，建議設定[如上詳述](#max_server_memory)的 [最大伺服器記憶體] 上限。

> [!IMPORTANT]
> 請只有必要的情況下才設定此選項，例如出現 sqlservr 程序移出分頁的跡象。在此情況下，錯誤記錄檔中會回報錯誤 17890，類似下列範例：`A significant part of sql server process memory has been paged out. This may result in a performance degradation. Duration: #### seconds. Working set (KB): ####, committed (KB): ####, memory utilization: ##%.`
> 從 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 開始，Standard Edition 不需要[追蹤旗標 845](../../t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql.md) 即可使用已鎖定頁面。 
  
### <a name="to-enable-lock-pages-in-memory"></a>啟用在記憶體中鎖定分頁  
若要啟用在記憶體中鎖定分頁選項：  
  
1.  在 **[開始]** 功能表上，按一下 **[執行]** 。 在 [開啟舊檔] 方塊中，輸入 **gpedit.msc**。  
  
     [群組原則] 對話方塊隨即開啟。  
  
2.  在 [群組原則] 主控台上，依序展開 [電腦組態] 和 [Windows 設定]。  
  
3.  展開 [安全性設定]，然後展開 [本機原則]。  
  
4.  選取 [使用者權限指派] 資料夾。  
  
     這些原則會顯示在詳細資料窗格中。  
  
5.  在窗格中按兩下 [鎖定記憶體中的分頁]。  
  
6.  在 [本機安全性原則設定] 對話方塊中，新增具有 sqlservr.exe 執行權限的帳戶 ([!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 啟動帳戶)。  
  
## <a name="multiple-instances-of-ssnoversion"></a>[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的多個執行個體  
 當您執行 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 的多個執行個體時，您有三個方式可以用來管理記憶體：  
  
-   使用 [最大伺服器記憶體] 來控制記憶體使用量，[如上詳述](#max_server_memory)。 建立每一個執行個體的最大設定值，注意，扣除總額不大於機器的總實體記憶體。 您想要提供與執行個體的預期工作負載或資料庫大小成比例的記憶體給每一個執行個體。 此方式的優點是當新的處理序或執行個體啟動時，立刻有記憶體可用。 缺點是，如果您不是執行所有執行個體，則任何執行中執行個體都不能利用剩餘可用的記憶體。  
  
-   使用 [最小伺服器記憶體] 來控制記憶體使用量，[如上詳述](#min_server_memory)。 建立每一個執行個體的最小設定值，使這些最小值的總和小於機器的總實體記憶體 1-2 GB。 同樣地，您可以建立與該執行個體的預期負載成比例的最小值。 此方式的優點是，若沒有同時執行所有執行個體，則執行的執行個體可使用剩餘可用的記憶體。 當電腦上有另一個記憶體密集處理序時，此方法也很有用，因為它可確保 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 至少取得合理的記憶體數量。 其缺點是，當新的執行個體 (或任何其他處理序) 啟動時，執行中的執行個體可能需要花一些時間來釋出記憶體，尤其如果它們必須將已修改的頁面寫回其資料庫才能這麼做的話。  
  
-   不執行任何動作 (不建議)。 有呈現工作負載的前幾個執行個體傾向於配置所有記憶體。 閒置執行個體或稍後啟動的執行個體最後可能只剩少量的記憶體可用。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 不會嘗試在執行個體之間平衡記憶體使用量。 不過，所有執行個體將回應 Windows 記憶體通知訊號，以便調整其記憶體使用量的大小。 Windows 不會平衡具有記憶體通知 API 的應用程式之間的記憶體。 它只提供對系統上記憶體可用性的全域回應。  
  
 您可以變更這些設定，而不必重新啟動執行個體，因此，您可以輕易體驗來尋找使用模式的最佳設定。  
  
## <a name="provide-the-maximum-amount-of-memory"></a>提供記憶體數量上限

在所有 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 版本中，最多可將記憶體設定為處理虛擬位址空間的最大上限。 如需詳細資訊，請參閱 [Windows 與 Windows Server 版本的記憶體限制](/windows/desktop/Memory/memory-limits-for-windows-releases#physical-memory-limits-windows-server-2016)。

## <a name="examples"></a>範例

### <a name="example-a-set-the-max-server-memory-option-to-4-gb"></a>範例 A. 將 max server memory 選項設定為 4 GB。
 下列範例會將 `max server memory` 選項設定為 4 GB。  請注意，雖然 `sp_configure` 將選項名稱指定為 `max server memory (MB)`，但此範例會示範省略 `(MB)`。

```sql
sp_configure 'show advanced options', 1;
GO
RECONFIGURE;
GO
sp_configure 'max server memory', 4096;
GO
RECONFIGURE;
GO
```
這會輸出類似下列內容的陳述式：

> 設定選項 'max server memory (MB)' 已從 2147483647 變更為 4096。 請執行 RECONFIGURE 陳述式來安裝。

### <a name="example-b-determining-current-memory-allocation"></a>範例 B：決定目前的記憶體配置  
 以下查詢會傳回目前配置之記憶體的相關資訊。  

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

### <a name="example-c-determining-value-for-max-server-memory-mb"></a>範例 C. 判斷 'max server memory (MB)' 的值
下列查詢會傳回目前設定值和 SQL Server 中使用值的相關資訊。  不論 'show advanced options' 是否為 true，此查詢都會傳回結果。

```sql
SELECT c.value, c.value_in_use
FROM sys.configurations c WHERE c.[name] = 'max server memory (MB)'
```
  
## <a name="next-steps"></a>後續步驟
 [記憶體管理架構指南](../../relational-databases/memory-management-architecture-guide.md)   
 [效能的監視與微調](../../relational-databases/performance/monitor-and-tune-for-performance.md)   
 [RECONFIGURE &#40;Transact-SQL&#41;](../../t-sql/language-elements/reconfigure-transact-sql.md)   
 [伺服器組態選項 &#40;SQL Server&#41;](../../database-engine/configure-windows/server-configuration-options-sql-server.md)   
 [sp_configure &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-configure-transact-sql.md)   
 [Database Engine 服務啟動選項](../../database-engine/configure-windows/database-engine-service-startup-options.md)   
 [SQL Server 2016 的版本與支援功能](../../sql-server/editions-and-components-of-sql-server-2016.md#Cross-BoxScaleLimits)   
 [SQL Server 2017 的版本與支援功能](../../sql-server/editions-and-components-of-sql-server-2017.md#Cross-BoxScaleLimits)   
 [Linux 上的 SQL Server 2017 版本與支援的功能](../../linux/sql-server-linux-editions-and-components-2017.md#Cross-BoxScaleLimits)   
 [Windows 與 Windows Server 版本的記憶體限制](/windows/desktop/Memory/memory-limits-for-windows-releases)
