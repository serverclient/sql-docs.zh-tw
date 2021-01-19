---
title: 執行緒和工作架構指南 | Microsoft Docs
description: 了解 SQL Server 中的執行緒和工作架構，包括工作排程、熱新增 CPU 及最佳做法來使用超過 64 個 CPU 的電腦。
ms.custom: ''
ms.date: 09/23/2020
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: supportability
ms.topic: conceptual
helpviewer_keywords:
- guide, thread and task architecture
- thread and task architecture guide
- task scheduling
- working threads
- Large Deficit First scheduling
- LDF scheduling
- scheduling, SQL Server
- tasks, SQL Server
- threads, SQL Server
- quantum, SQL Server
ms.assetid: 925b42e0-c5ea-4829-8ece-a53c6cddad3b
author: pmasl
ms.author: jroth
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 02ddc1ad96f45ba67ed613ee7446d8a1c12e1e5b
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98171400"
---
# <a name="thread-and-task-architecture-guide"></a>執行緒和工作架構指南
[!INCLUDE [SQL Server Azure SQL Database](../includes/applies-to-version/sql-asdb.md)]

## <a name="operating-system-task-scheduling"></a>作業系統工作排程
執行緒是作業系統可以執行的最小處理單位，可讓應用程式邏輯分成數個同時執行路徑。 當複雜應用程式有許多可同時執行的工作時，執行緒就很實用。 

作業系統執行應用程式的執行個體時，會建立一個稱為處理序的單位來管理這個執行個體。 處理序有執行緒。 這是應用程式的程式碼所執行的程式化指令序列。 例如，若簡單應用程式具有能依序執行的單一指令集，系統會以單一 **工作** 方式處理該指令集，而且整個應用程式中只有一個執行路徑 (或 **執行緒**)。 較為複雜的應用程式可能會有多個能同時 (而非依序) 執行的 **工作**。 若要這樣做，應用程式可以為每個工作啟動個別處理序 (不過這是資源密集作業)，或啟動個別執行緒 (相較之下較不資源密集)。 另外，每個執行緒跟那些與處理序相關聯的其他執行緒，可分開排程執行。

執行緒可讓複雜應用程式以更有效率的方式使用處理器 (CPU)，甚至在具有單 CPU 的電腦上也一樣。 只有一個 CPU 時，一次只能執行一個執行緒。 如果有一個執行緒執行不需使用 CPU 的長時間作業，像是磁碟讀寫，另一個執行緒便可以一直執行，直到第一個作業完成為止。 由於應用程式可在其他執行緒等待作業完成時執行一些執行緒，所以使 CPU 發揮最大功效。 特別是多使用者、需要大量磁碟 I/O 的應用程式 (例如資料庫伺服器) 更是如此。 有多個 CPU 的電腦可同時讓每個 CPU 執行一個執行緒。 例如，如果一部電腦有 8 個 CPU，就可以同時執行 8 個執行緒。

## <a name="sql-server-task-scheduling"></a>SQL Server 工作排程
在 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 的範圍中，**要求** 是查詢或批次的邏輯表示法。 要求也代表系統執行緒要求的作業，例如檢查點或記錄寫入器。 要求在其整個生命週期中以各種狀態存在，而且當執行要求所需的資源無法使用 (例如[鎖定](../relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql.md#locks)或[閂鎖](../relational-databases/system-dynamic-management-views/sys-dm-os-latch-stats-transact-sql.md#latches)) 時可累積等候。 如需有關要求狀態的詳細資訊，請參閱 [sys.dm_exec_requests](../relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql.md)。

**工作** 代表必須完成以滿足要求的工作單位。 您可以將一或多個工作指派給單一要求。 
-  平行要求會有數個同時 (而不是以序列方式) 執行的作用中工作，其中會有一個 **父工作** (或協調工作) 和多個 **子工作**。 平行要求的執行計畫可能會具有序列分支，亦即計畫區域，其包含不平行執行的運算子。 父工作也會負責執行這些序列運算子。
-  在執行期間，序列要求在任何指定的時間點都只會有一個作用中工作。     
工作在其整個生命週期中以各種狀態存在。 如需有關工作狀態的詳細資訊，請參閱 [sys.dm_os_tasks](../relational-databases/system-dynamic-management-views/sys-dm-os-tasks-transact-sql.md)。 處於「暫停」狀態的工作正在等候執行工作所需資源成為可用。 如需等候中工作的詳細資訊，請參閱 [sys.dm_os_waiting_tasks](../relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql.md)。

[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] **背景工作執行緒** (也稱為背景工作角色或執行緒) 是作業系統執行緒的邏輯表示法。 當執行 **序列要求** 時，[!INCLUDE[ssDEnoversion](../includes/ssdenoversion-md.md)] 將會繁衍背景工作角色以執行作用中工作 (1:1)。 當以 [資料列模式](../relational-databases/query-processing-architecture-guide.md#execution-modes)執行 **平行要求** 時，[!INCLUDE[ssDEnoversion](../includes/ssdenoversion-md.md)] 會指派背景工作角色，以協調負責完成受指派工作的子背景工作角色 (也是 1:1)，稱為 **父執行緒** (協調執行緒)。 父執行緒具有與其建立關聯的父工作。 父執行緒是要求的進入點，在引擎剖析查詢之前即已存在。 父執行緒的主要責任如下： 
-  協調平行掃描。
-  啟動子平行背景工作角色。
-  從平行執行緒收集資料列，並傳送至用戶端。
-  執行本機和全域彙總。    

> [!NOTE]
> 如果查詢計劃有序列和平行分支，則其中一項平行工作會負責執行序列分支。 

為每個工作繁衍的背景工作執行緒數目取決於：
-   要求是否符合平行處理原則的資格 (由查詢最佳化工具判斷)。
-   根據目前的工作負載，實際可用[平行處理原則 (DOP)](../relational-databases/query-processing-architecture-guide.md#DOP) 為何。 這可能會與預估 DOP 有所差異，後者是以平行處理原則的最大程度 (MAXDOP) 伺服器組態為基礎。 例如，MAXDOP 伺服器組態選項可能是 8，執行階段的可用 DOP 只能是 2，這會影響查詢效能。 

> [!NOTE]
> **平行處理原則的最大程度 (MAXDOP)** 限制是以個別工作 (而非個別要求) 為基礎所設定的。 這表示在平行查詢執行期間，單一要求可繁衍多個工作 (最多為 MAXDOP 限制)，且每個工作都會使用一個背景工作。 如需有關 MAXDOP 的詳細資訊，請參閱 [設定 max degree of parallelism 伺服器組態選項](../database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option.md)。

**排程器** (亦稱為 SOS 排程器) 會管理需要處理時間來代表工作 (Task) 執行工作 (Work) 的背景工作執行緒。 每個排程器都對應到個別處理器 (CPU)。 背景工作角色可在排程器中維持作用中的時間稱為 OS 配量，其最大值為 4 毫秒。 在經過此配量時間之後，背景工作角色會將其時間分配給需要存取 CPU 資源的背景工作角色，並變更其狀態。 這個背景工作角色之間的合作以最大化 CPU 資源存取的機制稱為 **合作式排程**，亦稱為非先佔式排程。 接著，背景工作角色中的變更會傳播到與該背景工作角色關聯的工作，以及傳播到與該工作關聯的要求。 如需有關背景工作角色狀態的詳細資訊，請參閱 [sys.dm_os_workers](../relational-databases/system-dynamic-management-views/sys-dm-os-workers-transact-sql.md)。 如需有關排程器的詳細資訊，請參閱 [sys.dm_os_schedulers](../relational-databases/system-dynamic-management-views/sys-dm-os-schedulers-transact-sql.md)。 

總結來說，**要求** 可能會繁衍一或多個 **工作** 以執行工作的單位。 每個工作都會指派給負責完成此工作的 **背景工作執行緒**。 每個背景工作執行緒都必須排程 (位於 **排程器** 上) 才能確實執行工作。 

> [!NOTE]
> 請考慮下列案例：   
> -  背景工作角色 1 是長時間執行的工作，例如，在記憶體內為基礎的資料表上使用預先讀取的讀取查詢。 背景工作角色 1 會在緩衝集區中尋找已存在的所需資料頁，因此不需要暫止以等待 I/O 作業，而且可以在暫止之前取用其完整的配量。   
> -  背景工作角色 2 會執行短於一毫秒的工作，因此必須在其完整的配量耗盡之前必須暫止。     
>
> 在此案例中，最高可達 [!INCLUDE[ssSQL14](../includes/sssql14-md.md)]，背景工作角色 1 基本上可允許擁有更多的整體配量時間來獨佔排程器。   
> 從 [!INCLUDE[ssSQL15](../includes/sssql16-md.md)] 開始，合作式排程包括大型不足額優先 (LDF) 排程。 使用 LDF 排程時，系統會監視配量使用模式，而單一背景工作執行緒不會獨佔一個排程器。 在相同的情況下，背景工作角色 2 可以在背景工作角色1 取用更多的配量之前取用重複配量，因此可防止背景工作角色 1 在不友善的模式下獨佔排程器。

### <a name="scheduling-parallel-tasks"></a>排程平行工作
假設 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 以 MaxDOP 8 設定，且 CPU 親和性設為跨 NUMA 節點 0 和 1 的 24 個 CPU (排程器)。 排程器 0 到 11 屬於 NUMA 節點 0，而排程器 12 到 23 屬於 NUMA 節點 1。 應用程式會將下列查詢 (要求) 傳送至 [!INCLUDE[ssde_md](../includes/ssde_md.md)]：

```sql
SELECT h.SalesOrderID, h.OrderDate, h.DueDate, h.ShipDate
FROM Sales.SalesOrderHeaderBulk AS h 
INNER JOIN Sales.SalesOrderDetailBulk AS d ON h.SalesOrderID = d.SalesOrderID 
WHERE (h.OrderDate >= '2014-3-28 00:00:00');
```

> [!TIP]
> 您可使用 [AdventureWorks2016_EXT 範例資料庫](../samples/adventureworks-install-configure.md) 資料庫來執行範例查詢。 資料表 `Sales.SalesOrderHeader` 和 `Sales.SalesOrderDetail` 已擴大了 50 倍，並已重新命名為 `Sales.SalesOrderHeaderBulk` 和 `Sales.SalesOrderDetailBulk`。

執行計畫會顯示兩個資料表之間的[雜湊聯結](../relational-databases/performance/joins.md#hash)，且每個運算子會以平行方式執行，如具有兩個箭號的黃色圓圈所指示。 每個平行處理原則運算子都是計畫中的不同分支。 因此，下列執行計畫中有三個分支。 

![平行查詢計畫](../relational-databases/media/schedule-parallel-query-plan.png)

> [!NOTE]
> 如果將執行計畫視為樹狀結構，則 **分支** 就是計畫的一個區域，其可在平行處理原則運算子 (也稱為交換迭代器) 之間將一或多個運算子分組。 如需計畫運算子的詳細資訊，請參閱[執行程序表邏輯和實體運算子參考](../relational-databases/showplan-logical-and-physical-operators-reference.md)。 

雖然執行計畫中有三個分支，但在執行期間的任何時間點，只能在此執行計畫中同時執行兩個分支：
1.  在 `Sales.SalesOrderHeaderBulk` (聯結的組建輸入) 使用「叢集索引掃描」的分支會單獨執行。
2.  然後，在 `Sales.SalesOrderDetailBulk` (聯結的探查輸入) 使用「叢集索引掃描」的分支，會與建立「點陣圖」且目前正在執行「雜湊比對」的分支同時執行。

執行程序表 XML 顯示已在 NUMA 節點 0 保留並使用 16 個背景工作執行緒：

```xml
<ThreadStat Branches="2" UsedThreads="16">
  <ThreadReservation NodeId="0" ReservedThreads="16" />
</ThreadStat>
```

執行緒保留可確保 [!INCLUDE[ssde_md](../includes/ssde_md.md)] 擁有足夠背景工作執行緒，以執行要求所需的所有工作。 您可在數個 NUMA 節點上保留執行緒，也可以只保留在一個 NUMA 節點中。 執行緒保留會在執行開始之前於執行階段完成，並相依於排程器載入。 保留的背景工作執行緒數目通常衍生自公式 ***concurrent branches* * *runtime DOP***，並排除父系背景工作執行緒。 每個分支都會受限於等於 MaxDOP 的背景工作執行緒數目。 在此範例中有兩個平行分支，而 MaxDOP 設為 8，因此 **2 * 8 = 16**。

如需參考，請觀察[即時查詢統計資料](../relational-databases/performance/live-query-statistics.md)中的即時執行計畫，其中一個分支已完成，且兩個分支同時執行。

![即時平行查詢計畫](../relational-databases/media/schedule-parallel-query-live-plan.png)

[!INCLUDE[ssDEnoversion](../includes/ssdenoversion-md.md)] 會指派背景工作執行緒來執行作用中的工作 (1:1)，可藉由查詢 [sys.dm_os_tasks](../relational-databases/system-dynamic-management-views/sys-dm-os-tasks-transact-sql.md) DMV 在查詢執行期間觀察到，如下列範例所示：

```sql
SELECT parent_task_address, task_address, 
       task_state, scheduler_id, worker_address
FROM sys.dm_os_tasks
WHERE session_id = <insert_session_id>
ORDER BY parent_task_address, scheduler_id;
```

> [!TIP]
> 父工作的資料行 `parent_task_address` 一律為 Null。 

> [!TIP]
> 您可能會在非常忙碌的 [!INCLUDE[ssDEnoversion](../includes/ssdenoversion-md.md)] 上，看到作用中工作數目超過保留執行緒所設定的限制。 這些工作可能屬於已不再使用的分支，且處於暫時性狀態，等待著清除作業。 

[!INCLUDE[ssResult](../includes/ssresult-md.md)] 請注意，目前正在執行的分支有 17 個作用中工作：對應至保留執行緒的 16 個子工作，加上父工作，或協調工作。

|parent_task_address|task_address|task_state|scheduler_id|worker_address|
|--------|--------|--------|--------|--------|
|NULL|**0x000001EF4758ACA8**|SUSPENDED|3|0x000001EFE6CB6160|
|0x000001EF4758ACA8|0x000001EFE43F3468|SUSPENDED|0|0x000001EF6DB70160|
|0x000001EF4758ACA8|0x000001EEB243A4E8|SUSPENDED|0|0x000001EF6DB7A160|
|0x000001EF4758ACA8|0x000001EC86251468|SUSPENDED|5|0x000001EEC05E8160|
|0x000001EF4758ACA8|0x000001EFE3023468|SUSPENDED|5|0x000001EF6B46A160|
|0x000001EF4758ACA8|0x000001EFE3AF1468|SUSPENDED|6|0x000001EF6BD38160|
|0x000001EF4758ACA8|0x000001EFE4AFCCA8|SUSPENDED|6|0x000001EF6ACB4160|
|0x000001EF4758ACA8|0x000001EFDE043848|SUSPENDED|7|0x000001EEA18C2160|
|0x000001EF4758ACA8|0x000001EF69038108|SUSPENDED|7|0x000001EF6AEBA160|
|0x000001EF4758ACA8|0x000001EFCFDD8CA8|SUSPENDED|8|0x000001EFCB6F0160|
|0x000001EF4758ACA8|0x000001EFCFDD88C8|SUSPENDED|8|0x000001EF6DC46160|
|0x000001EF4758ACA8|0x000001EFBCC54108|SUSPENDED|9|0x000001EFCB886160|
|0x000001EF4758ACA8|0x000001EC86279468|SUSPENDED|9|0x000001EF6DE08160|
|0x000001EF4758ACA8|0x000001EFDE901848|SUSPENDED|10|0x000001EFF56E0160|
|0x000001EF4758ACA8|0x000001EF6DB32108|SUSPENDED|10|0x000001EFCC3D0160|
|0x000001EF4758ACA8|0x000001EC8628D468|SUSPENDED|11|0x000001EFBFA4A160|
|0x000001EF4758ACA8|0x000001EFBD3A1C28|SUSPENDED|11|0x000001EF6BD72160|

請觀察這 16 個子工作每個都已獲指派不同的背景工作執行緒 (在 `worker_address` 資料行中可見)，但所有背景工作都會指派給相同集區的 8 個排程器 (0、5、6、7、8、9、10、11)，且父工作會指派給此集區以外的排程器 (3)。

> [!IMPORTANT]
> 在排程指定分支上的第一組平行工作之後，[!INCLUDE[ssde_md](../includes/ssde_md.md)] 會針對其他分支上任何其他工作使用相同的排程器集區。 這表示同一組排程器會用於整個執行計畫中的所有平行工作，只受限於 MaxDOP。  
> [!INCLUDE[ssDEnoversion](../includes/ssdenoversion-md.md)] 一律會嘗試從相同的 NUMA 節點指派排程器以執行工作，並在排程器可供使用時，(以循環配置資源的方式) 依序指派排程器。 不過，指派給父工作的背景工作執行緒，可能會放置在與其他工作不同的 NUMA 節點中。

背景工作執行緒只能在其配量的持續時間 (4 毫秒) 於排程器中維持作用中狀態，且在經過該配量之後必須讓出排程器，以便讓指派給另一個工作的背景工作執行緒能成為作用中狀態。 當背景工作的配量過期且不再為作用中時，假設工作不需要存取目前尚未提供的資源 (例如閂鎖或鎖定)，則個別的工作會以 RUNNABLE 狀態放在 FIFO 佇列中，直到其再次移至 RUNNING 狀態為止；在此情況下，工作會進入 SUSPENDED 狀態 (而不是 RUNNABLE)，直到這些資源可供使用的時候為止。  

> [!TIP] 
> 針對上面所見 DMV 的輸出，所有作用中的工作都處於 SUSPENDED 狀態。 如需等候工作的詳細資料，請查詢 [sys.dm_os_waiting_tasks](../relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql.md) DMV。 

總而言之，平行要求會產生多項工作。 每項工作都必須指派給單一的背景工作執行緒。 每個背景工作執行緒都必須指派給單一排程器。 因此，每個分支的使用中排程器數目不能超過平行工作數目，此數目由 MaxDOP 組態或查詢提示所設定。 協調執行緒不會算在 MaxDOP 的限制中。 

### <a name="allocating-threads-to-a-cpu"></a>配置執行緒給 CPU
根據預設，[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 的每個執行個體會啟動每個執行緒，且作業系統會根據負載從 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 的執行個體，將執行緒散發給電腦上的處理器 (CPU)。 如果已在作業系統層級啟用處理序親和性，則作業系統就會將每個執行緒指派給特定的 CPU。 相反地，[!INCLUDE[ssDEnoversion](../includes/ssdenoversion-md.md)] 會將 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] **背景工作執行緒** 指派給 **排程器**，以循環配置資源的方式將這些執行緒平均分配給 CPU。
    
為了執行多工作業 (例如當多個應用程式存取一組相同的 CPU 時)，作業系統有時會在不同的 CPU 之間移動背景工作執行緒。 雖然從作業系統的觀點來看很有效率，但是在繁重的系統負載下，這項活動可能會降低 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 的效能，因為每個處理器快取會重複地重新載入資料。 在這些情況中，將特定執行緒指定給 CPU，可降低處理器重新載入的情形並減少跨 CPU 移轉執行緒的問題 (藉此減少內容切換)，進而提升效能，而執行緒與處理器之間的關聯則稱為處理器同質性。 如果已經啟用相似性，作業系統就會將每個執行緒指派給特定的 CPU。 

[親和性遮罩選項](../database-engine/configure-windows/affinity-mask-server-configuration-option.md)是使用 [ALTER SERVER CONFIGURATION](../t-sql/statements/alter-server-configuration-transact-sql.md) 設定的。 沒有設定親和性遮罩時，[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 執行個體就會將背景工作執行緒平均配置給尚未遮罩的排程器。

> [!CAUTION]
> 不要在作業系統中設定 CPU 親和性，然後又在 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 中設定親和性遮罩。 這些設定嘗試達到相同的結果，如果組態不一致，可能會有無法預期的結果。 如需詳細資訊，請參閱[親和性遮罩選項](../database-engine/configure-windows/affinity-mask-server-configuration-option.md)。

當大量用戶端連接到伺服器時，執行緒集區有助於最佳化效能。 通常，會針對每一個查詢要求建立個別的作業系統執行緒。 然而，在數以百計的伺服器連接之下，若每個查詢要求都使用一個執行緒，反而會耗用大量的系統資源。 [最大背景工作執行緒](../database-engine/configure-windows/configure-the-max-worker-threads-server-configuration-option.md)選項可讓 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 建立背景工作執行緒集區，以服務更多的查詢要求數量，進而改善效能。 

### <a name="using-the-lightweight-pooling-option"></a>使用 lightweight pooling 選項
切換執行緒內容所需的額外負荷可能不會太大。 大部分的 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 執行個體，在將輕量型共用選項設為 0 或 1 時並不會察覺到任何效能上的差異。 只有擁有下列特性的電腦上所執行 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 執行個體，才有可能感受到[輕量型共用](../database-engine/configure-windows/lightweight-pooling-server-configuration-option.md)的好處：    
* 具有多個 CPU 的大型伺服器
* 所有 CPU 皆以接近最大容量在執行
* 高層次的內容切換

這些系統在將輕量型共用值設定為 1 時，可能會發現效能有稍微增加。

> [!IMPORTANT]
> 請勿針對例行作業使用 Fiber 模式排程。 這可能會抑制一般內容切換的好處而降低效能，且 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 的某些元件無法在 Fiber 模式中正確運作。 如需詳細資訊，請參閱[輕量型共用](../database-engine/configure-windows/lightweight-pooling-server-configuration-option.md)。

## <a name="thread-and-fiber-execution"></a>執行緒和 Fiber 執行
Microsoft Windows 使用數值優先權系統，從 1 到 31 的範圍來排程要執行的執行緒。 零是保留給作業系統使用的。 當有數個執行緒等待執行時，Windows 會先分派有最高優先順序的執行緒。

根據預設，每個 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 執行個體的優先權為 7，這稱為一般優先權。 這讓 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 執行緒有夠高的優先權，可以取得足夠的 CPU 資源，而不會影響其他的應用程式。 

> [!IMPORTANT]  
>  [!INCLUDE[ssNoteDepFutureDontUse](../includes/ssnotedepfuturedontuse-md.md)]  

使用[優先權提升](../database-engine/configure-windows/configure-the-priority-boost-server-configuration-option.md)設定選項，可將 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 執行個體中執行緒的優先權增加至 13。 這稱為高優先權。 這個設定讓 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 執行緒擁有比大部份其他應用程式更高的優先權。 因此，每當 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 執行緒就緒可執行時，通常系統會先分派執行緒，且其他應用程式不會預先清空執行緒。 當伺服器僅執行 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 的執行個體、而沒有執行其他應用程式時，可以改善系統的效能。 然而，如果 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 中發生需要大量記憶體的作業，則其他應用程式可能無法擁有夠高的優先權，以預先清空 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 執行緒。 

如果您在電腦上執行多個 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 執行個體，並且僅提高部份執行個體的優先權，則以一般優先權執行的所有執行個體之效能都將受到影響。 另外，如果有開啟優先權提升，就可能會降低伺服器上其他應用程式與元件的效能。 因此，它應該在嚴格控制的情況下使用。


## <a name="hot-add-cpu"></a>熱新增 CPU
熱新增 CPU 是指將 CPU 動態新增到執行中系統的功能。 新增 CPU 可發生於實體上新增硬體、邏輯上進行線上硬體分割或是虛擬上透過虛擬化層時。 從 [!INCLUDE[ssKatmai](../includes/ssKatmai-md.md)] 開始，[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 便可支援熱新增 CPU。

熱新增 CPU 的需求：  
* 需要有支援熱新增 CPU 的硬體。
* 需要 64 位元版本的 Windows Server 2008 Datacenter 或適用於 Itanium 系統之作業系統的 Windows Server 2008 Enterprise Edition。
* 需要 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] Enterprise。
* [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 無法設定為使用軟體 NUMA。 如需有關軟體 NUMA 的詳細資訊，請參閱 [軟體 NUMA (SQL Server)](../database-engine/configure-windows/soft-numa-sql-server.md)。

[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 不會在新增 CPU 之後自動開始使用這些 CPU。 這樣可避免 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 使用可能要供其他用途使用所新增的 CPU。 在新增 CPU 之後，請執行 [RECONFIGURE](../t-sql/language-elements/reconfigure-transact-sql.md) 陳述式，以讓 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 將新的 CPU 辨識為可用資源。

> [!NOTE]
> 如果設定了 [affinity64 mask](../database-engine/configure-windows/affinity64-mask-server-configuration-option.md) ，則必須修改 affinity64 mask 才能使用新的 CPU。
 
## <a name="best-practices-for-running-sql-server-on-computers-that-have-more-than-64-cpus"></a>在具備超過 64 顆 CPU 之電腦上執行 SQL Server 的最佳做法

### <a name="assigning-hardware-threads-with-cpus"></a>使用 CPU 指派硬體執行緒
請勿使用 affinity mask 和 affinity64 mask 伺服器組態選項，將處理器繫結至特定執行緒。 這些選項僅限於 64 個 CPU。 改為使用 [ALTER SERVER CONFIGURATION](../t-sql/statements/alter-server-configuration-transact-sql.md) 的 `SET PROCESS AFFINITY` 選項。

### <a name="managing-the-transaction-log-file-size"></a>管理交易記錄檔大小
請勿仰賴自動成長來增加交易記錄檔的大小。 增加交易記錄必須是序列處理序。 擴充記錄可避免在記錄擴充完成之前繼續進行交易寫入作業。 不過，您可以將檔案大小設定為夠大的值來支援環境中的典型工作負載，藉此為所有記錄檔預先配置空間。

### <a name="setting-max-degree-of-parallelism-for-index-operations"></a>針對索引作業設定平行處理原則的最大程度
在具有許多 CPU 的電腦上，您可以暫時將資料庫的復原模式設定為大量記錄或簡單復原模式，藉此改善建立或重建索引等索引作業的效能。 這些索引作業可能會產生重要的記錄活動，而且記錄競爭可能會影響 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 所選擇之平行處理原則的最佳程度。

此外，若要調整 **平行處理原則的最大程度 (MAXDOP)** 伺服器組態選項，請考慮使用 [MAXDOP 選項](../t-sql/statements/alter-index-transact-sql.md) 調整索引作業的平行處理原則。 如需詳細資訊，請參閱 [設定平行索引作業](../relational-databases/indexes/configure-parallel-index-operations.md)。 如需有關調整平行處理原則的最大程度伺服器組態選項的詳細資訊，請參閱[設定 [平行處理原則的最大程度] 伺服器組態選項](../database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option.md)。

### <a name="setting-the-maximum-number-of-worker-threads"></a>設定工作者執行緒的數目上限
[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 將會在啟動時動態設定 **最大工作者執行緒** 伺服器組態選項。 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 在啟動時使用可用 CPU 數目與系統架構 (使用記載的[公式](../database-engine/configure-windows/configure-the-max-worker-threads-server-configuration-option.md#Recommendations)) 來判斷此伺服器組態。

此選項是進階選項，只有具經驗的資料庫管理員或通過認證的 SQL Server 專業人員才可變更。 如果您懷疑發生效能問題，很可能並非是否能使用背景工作執行緒所致。 原因較有可能是因為 I/O 等作業導致背景工作執行緒處於等待狀態。 建議您在變更背景工作執行緒設定的上限前，先找出效能問題的根本原因。 不過，如果您需要手動設定背景工作執行緒數目上限，則此設定值必須一律至少設定為系統上出現的 CPU 數目的七倍。 如需詳細資訊，請參閱 [設定最大工作者執行緒](../database-engine/configure-windows/configure-the-max-worker-threads-server-configuration-option.md)。

### <a name="using-sql-trace-and-sql-server-profiler"></a>使用 SQL 追蹤和 SQL Server Profiler
建議您不要在生產環境中使用 SQL 追蹤和 SQL Profiler。 執行這些工具的負擔也會隨著 CPU 數目增加而提高。 如果您必須在實際執行環境中使用 SQL 追蹤，請將追蹤事件的數目限制為最小值。 請仔細地分析和測試低於負載的每個追蹤事件，並且避免使用大幅影響效能的事件組合。

> [!IMPORTANT]
> SQL 追蹤和 [!INCLUDE[ssSqlProfiler](../includes/sssqlprofiler-md.md)] 已被淘汰。 包含 Microsoft SQL Server 追蹤和重新執行物件的 *Microsoft.SqlServer.Management.Trace* 命名空間也會被淘汰。 
> [!INCLUDE[ssNoteDepFutureAvoid](../includes/ssnotedepfutureavoid-md.md)] 
> 請改用擴充事件。 如需[延伸事件](../relational-databases/extended-events/extended-events.md)的詳細資訊，請參閱[快速入門：SQL Server 中的延伸事件](../relational-databases/extended-events/quick-start-extended-events-in-sql-server.md)和 [SSMS XEvent 分析工具](../relational-databases/extended-events/use-the-ssms-xe-profiler.md)。

> [!NOTE]
> 適用於 Analysis Services 工作負載的 [!INCLUDE[ssSqlProfiler](../includes/sssqlprofiler-md.md)]「未」遭淘汰，而且將會繼續受支援。

### <a name="setting-the-number-of-tempdb-data-files"></a>設定 tempdb 資料檔案的數目
檔案數目取決於電腦上 (邏輯) 處理器的數目。 一般而言，如果邏輯處理器的數目小於或等於 8，請使用與邏輯處理器數目相同的資料檔案數目。 如果邏輯處理器的數目大於 8，請使用 8 個資料檔案，要是競爭的情況仍持續發生，請以 4 的倍數增加資料檔案數目，直到競爭縮減到可接受的程度；或是對工作負載/程式碼進行變更。 也請記住有關 tempdb 的其他建議，您可於[將 SQL Server 中的 tempdb 效能最佳化](../relational-databases/databases/tempdb-database.md#optimizing-tempdb-performance-in-sql-server)中找到。 

不過，只要仔細地考量 tempdb 的並行需求，您就可以減少資料庫管理作業額外負荷。 例如，如果系統具有 64 個 CPU 而且通常只有 32 個查詢使用 tempdb，則將 tempdb 檔案的數目增加至 64 並不會改善效能。

### <a name="sql-server-components-that-can-use-more-than-64-cpus"></a>可以使用超過 64 個 CPU 的 SQL Server 元件
下表將列出 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 元件並指出它們是否能使用超過 64 個 CPU。

|程序名稱   |可執行的程式 |使用超過 64 個 CPU |  
|----------|----------|----------|  
|SQL Server Database Engine |Sqlserver.exe  |是 |  
|Reporting Services |Rs.exe |否 |  
|Analysis Services  |As.exe |否 |  
|Integration Services   |Is.exe |否 |  
|Service Broker |Sb.exe |否 |  
|全文檢索搜尋   |Fts.exe    |否 |  
|SQL Server Agent   |Sqlagent.exe   |否 |  
|SQL Server Management Studio   |Ssms.exe   |否 |  
|SQL Server 安裝程式   |Setup.exe  |否 |  
