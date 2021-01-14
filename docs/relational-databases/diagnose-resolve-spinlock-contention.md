---
title: 白皮書：診斷並解決執行緒同步鎖定競爭
description: 本文深入探討如何診斷並解決 SQL Server 中的執行緒同步鎖定競爭。 本文最初由 Microsoft SQLCAT 小組發佈。」
ms.date: 09/30/2020
ms.prod: sql
ms.reviewer: wiassaf
ms.technology: performance
ms.topic: how-to
author: bluefooted
ms.author: pamela
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 00b4856ab0c057b7f63aae44834884bc775d8e92
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98102166"
---
# <a name="diagnose-and-resolve-spinlock-contention-on-sql-server"></a>診斷並解決 SQL Server 中的執行緒同步鎖定競爭

本文提供如何在高並行系統上的 SQL Server 應用程式中，找出並解決執行緒同步鎖定競爭相關問題的深入資訊。

> [!NOTE]
> 這裡記載的建議與最佳做法，是根據實際開發和部署真實世界 OLTP 系統期間的體驗。 本文最初由 Microsoft SQL Server 客戶諮詢小組 (SQLCAT) 發佈。

## <a name="background"></a>背景

在過去，商用 Windows Server 電腦只使用了一到兩個微處理器/CPU 晶片，而 CPU 中也只有單一處理器或「核心」。 隨著電晶體密度的提升，電腦能夠利用更快速的 CPU 來增加處理效能。 根據「摩爾定律」，自從 1971 年第一批一般用途單一晶片 CPU 問世以來，電晶體密度或可放置於積體電路上的電晶體數目，每兩年會增加一倍。 近幾年來，人們藉由組裝內含多個 CPU 的電腦，大幅改善了使用更快 CPU 來提升電腦處理效能的傳統方法。 在撰寫本文的同時，Intel Nehalem CPU 架構最多可讓每個 CPU 容納八個核心，這些核心在八插槽系統中，可進一步透過超執行緒技術，加倍到 128 個邏輯處理器。 由於 x86 相容電腦上的邏輯處理器數目增加，並行問題亦隨著邏輯處理器爭用資源而變得更加明顯。 本指引描述在具有某些工作負載的高並行系統上執行 SQL Server 應用程式時，如何找出並解決所觀察到的特定資源競爭問題。

在本節中，我們將分析 SQLCAT 小組在診斷及解決執行緒同步鎖定競爭問題中所學習到的體驗。 執行緒同步鎖定競爭是並行問題的一種，常見於大規模系統上的真實客戶工作負載中。

## <a name="symptoms-and-causes-of-spinlock-contention"></a>執行緒同步鎖定競爭的徵兆與原因

本節描述如何診斷對 SQL Server 上 OLTP 應用程式效能造成影響的「執行緒同步鎖定競爭」問題。 執行緒同步鎖定診斷與疑難排解應該視為進階主題，且需要具備偵錯工具和 Windows 內部的知識。

執行緒同步鎖定是輕量同步處理的基本類型，其用來保護對資料結構的存取。 對 SQL Server 而言，執行緒同步鎖定不是唯一的機制。 作業系統只有在短時間內需要存取特定資料結構時，才會使用這項機制。 當嘗試取得執行緒同步鎖定的執行緒無法獲得存取權時，該執行緒會在迴圈中定期執行檢查，以判斷資源是否可用，而不是立即退讓。 在等待執行緒同步鎖定一段時間之後，執行緒才會因為無法取得資源而退讓。 退讓可使相同 CPU 上的其他執行緒得以執行。 這種行為稱為「輪詢」，將在本文稍後更深入討論。

SQL Server 利用執行緒同步鎖定以保護對其部分內部資料結構的存取。 執行緒同步鎖定可用於引擎內，以類似於閂鎖的方式，將特定資料結構的存取序列化。 閂鎖與執行緒同步鎖定之間的主要差異在於，執行緒同步鎖定會周旋 (執行迴圈) 一段時間來檢查資料結構的可用性，而執行緒在嘗試存取受閂鎖保護的結構時，則會在資源無法使用的情況下立即退讓。 執行緒在退讓時，會將其內容切換至 CPU，而讓另一個執行緒得以執行。 退讓是相當耗費資源的作業，其對於保留期較短資源而言更有效率的方式是讓執行緒以迴圈方式執行，以定期檢查資源的可用性。

## <a name="symptoms"></a>徵兆

在忙碌的高並行系統上，經常可看見受執行緒同步鎖定所保護且頻繁存取的結構處於競爭中狀態。 只有在競爭造成大量 CPU 負荷時，才會將此現象視為問題。 執行緒同步鎖定統計資料是由 SQL Server 內的 *sys.dm_os_spinlock_stats* 動態管理檢視 (DMV) 所公開。 例如，此查詢會產生下列輸出：

> [!NOTE]
> 本文稍後會詳細討論如何解譯此 DMV 所傳回的資訊。

```sql
select * from sys.dm_os_spinlock_stats
order by spins desc
```

![sys.dm_os_spinlock_stats 輸出](./media/diagnose-resolve-spinlock-contention/image4.png)

此查詢所公開的統計資料描述如下：

| 資料行 | 描述 |
|---|---|
| **衝突** | 每當執行緒遭到封鎖而無法存取受執行緒同步鎖定保護的資源時，這個值就會遞增。 |
| **周旋** | 每當執行緒執行迴圈，並等候執行緒同步鎖定可供使用時，這個值就會遞增。 這是執行緒在嘗試取得資源時，所執行工作量的量值。 |
| **Spins_per_collision** | 每次衝突的周旋比。 |
| **睡眠時間** | 與輪詢事件相關，但與本文所述的技術無關。 |
| **輪詢** | 當嘗試存取保留資源的「周旋中」執行緒，判斷其必須允許相同 CPU 上的其他執行緒執行時，這個值就會遞增。 |

為了便於討論，在此會特別關注的統計資料，是系統負載過大的情況下，在特定時段內發生的衝突、周旋以及輪詢事件的數目。 當執行緒嘗試存取受執行緒同步鎖定保護的資源時，就會發生衝突。 發生衝突時，衝突計數便會遞增，執行緒會開始在迴圈中周旋，並定期檢查資源是否可用。 每次執行緒進行周旋 (迴圈) 時，周旋計數便會遞增。

每次衝突的周旋比，是指當執行緒佔有執行緒同步鎖定時，所發生周旋次數的量值，告知您執行緒佔有執行緒同步鎖定時，所發生的周旋次數。 例如，每次衝突的周旋比低，而衝突計數高，意味著執行緒同步鎖定中發生的周旋次數少，但有很多執行緒在競爭該資源。 周旋次數高，意味著在執行緒同步鎖定程式碼中進行周旋的時間相對較長 (即該程式碼檢查了雜湊貯體中的大量項目)。 當競爭增加 (導致衝突計數增加)，周旋數目也會因此增加。

輪詢與周旋的概念相當類似。 根據設計，為了避免過度浪費 CPU，執行緒同步鎖定並不會無限期地周旋，直到可存取保留的資源為止。 為了確保執行緒同步鎖定不會過度使用 CPU 資源，執行緒同步鎖定會執行輪詢，或停止周旋並進入「睡眠」。 無論是否取得目標資源的擁有權，執行緒同步鎖定都會執行輪詢。 這麼做是為了讓其他執行緒可在 CPU 上進行排程，以期望達到更好的工作效率。 引擎其預設行為是先周旋一段固定的時間間隔，再執行輪詢。 嘗試取得執行緒同步鎖定需要維持快取並行的狀態，相對於周旋所耗費的 CPU，這是強度更高 CPU 作業。 因此，執行緒並不會在每次周旋時皆嘗試取得執行緒同步鎖定，而是謹慎地少量執行。 在 SQL Server 中，某些執行緒同步鎖定類型 (例如：LOCK_HASH) 已藉由在嘗試取得執行緒同步鎖定的之間，以指數方式增加嘗試的間隔 (直至某極限值) 而獲得改善，這通常可降低對 CPU 效能的影響。

下圖為執行緒同步鎖定演算法的概念圖：

![執行緒同步鎖定演算法概念圖](./media/diagnose-resolve-spinlock-contention/image5.png)

## <a name="typical-scenarios"></a>典型案例

發生執行緒同步鎖定競爭的原因有很多，且可能與資料庫設計決策無關。 由於執行緒同步鎖定會限制對內部資料結構的存取，因此執行緒同步鎖定競爭與緩衝區閂鎖競爭的表現方式不同，後者會直接受到結構描述設計選擇與資料存取模式的影響。

執行緒同步鎖定競爭的主要徵兆是高 CPU 使用量，這是由大量周旋以及嘗試取得相同執行緒同步鎖定的許多執行緒所導致。 一般而言，這種情況會發生在 CPU 核心 \>= 24 的系統上 (CPU 核心 \>= 32 的系統上最常見)。 如先前所述，對於具有大量負載的高並行 OLTP 系統而言，執行緒同步鎖定上某種程度的競爭是正常現象，且在長時間執行的系統上，*sys.dm_os_spinlock_stats* DMV 通常會回報大量的周旋次數 (數十億/數兆次)。 同理，所觀察到的任何特定執行緒同步鎖定類型其大量周旋，並不足以判斷其對工作負載效能有負面影響。

下列幾個徵兆的組合，可能表示出現執行緒同步鎖定競爭：

* 在特定執行緒同步鎖定類型中，觀察到大量的周旋與輪詢次數。

* 系統的 CPU 使用率很高，或 CPU 使用量達到尖峰。 在 CPU 負載過大的情況下，您會在 SOS_SCHEDULER_YEILD (來自 *sys.dm_os_spinlock_stats* DMV 的回報) 看到大量信號等候。

* 系統正處於高並行狀態。

* CPU 使用量與周旋次數相較於輸送量不成比例地增加。

   > [!IMPORTANT]
   > 即使上述每個條件都成立，造成高 CPU 使用量的根本原因仍可能在其他地方。 事實上，在大部分的情況下，CPU 增加的原因都不是執行緒同步鎖定競爭所造成。 CPU 使用量增加的一些常見原因包括：

* 隨著基礎資料增長導致需要執行額外的記憶體駐留資料邏輯讀取，而變得更耗費資源的查詢。

* 查詢計劃變更導致執行效果欠佳。

如果上述條件皆成立，請針對潛在的執行緒同步鎖定競爭問題進行進一步調查。

其中一個容易診斷的常見現象，是輸送量與 CPU 使用量出現明顯差異。 許多 OLTP 工作負載在「輸送量/系統上的使用者人數」與 CPU 使用量之間皆具有關聯性。 當 CPU 使用量與輸送量出現明顯差異，且觀察到大量周旋次數，即可能表示執行緒同步鎖定競爭會對 CPU 造成額外負荷。 這裡要注意的重點是，當某些查詢隨著時間而變得更耗費資源時，也很容易在系統上看見這類差異。 例如，針對隨著時間而執行較多邏輯讀取的資料集發出查詢，便可能會產生類似的徵兆。

針對這些類型的問題進行疑難排解時，請務必排除其他造成高 CPU 負荷的常見原因。

## <a name="example"></a>範例

在下列範例中，依每秒交易次數所測得的 CPU 使用量與輸送量之間，幾乎呈現線性關係。 出現些許差異是正常現象，因為當工作負載增加時，就會產生額外負荷。 如下圖所示，差異開始大幅增加。 當 CPU 使用量達到 100% 時，輸送量也同時急遽下降。

![效能監視器中的 CPU 下降](./media/diagnose-resolve-spinlock-contention/image7.png)

以 3 分鐘為間隔測量周旋次數時，可看到周旋次數更傾向於以指數而非線性增加，這表示執行緒同步鎖定競爭可能存在問題。

![以 3 分鐘為間隔的周旋次數圖表](./media/diagnose-resolve-spinlock-contention/image8.png)

如先前所述，執行緒同步鎖定競爭最常見於負載過大的高並行系統上。

一些容易發生此問題的案例包括：

* 因為無法完整授與物件名稱資格而引起的名稱解析問題。 如需詳細資訊，請參閱[編譯鎖引起的 SQL Server 阻塞問題描述](https://support.microsoft.com/help/263889/how-to-troubleshoot-blocking-caused-by-compile-locks)。 這類特定問題在該文章中會有更詳細的描述。

* 頻繁存取相同鎖定的工作負載 (例如，經常性讀取資料列上的共用鎖定)，其鎖定管理員中的鎖定雜湊貯體爭用。 這種競爭會以 LOCK_HASH 類型的執行緒同步鎖定顯現。 我們在某個特定案例中發現，此問題是由於在測試環境中以錯誤的方式將存取模式模型化所導致。 在此環境中，因為測試參數設定不正確，而導致超出預期數目的執行緒持續地存取完全相同資料列。

* 當 MSDTC 交易協調器之間存在高度延遲時，亦會隨即出現高比率的 DTC 交易。 這類特定問題在 SQLCAT 部落格項目[解決與 DTC 相關的等候並調整 DTC 的可擴縮性](https://techcommunity.microsoft.com/t5/datacat/resolving-dtc-related-waits-and-tuning-scalability-of-dtc/ba-p/305054) (英文) 中有詳細記載。

## <a name="diagnosing-spinlock-contention"></a>診斷自旋鎖競爭

本節提供診斷 SQL Server 執行緒同步鎖定競爭的相關資訊。 用於診斷執行緒同步鎖定競爭的主要工具如下：

| 工具 | 使用 |
|---|---|
| **效能監視器** | 用於尋找 CPU 高使用量狀態，或輸送量與 CPU 使用量之間的差異。 |
| **sys.dm_os_spinlock stats DMV** | 用於在一段時間內，尋找大量的周旋次數與輪詢事件。 |
| **SQL Server 擴充事件** | 用於在執行緒同步鎖定發生大量周旋次數時，追蹤其呼叫堆疊。 |
| **記憶體傾印** | SQL Server 處理序與 Windows 偵錯工具的記憶體傾印 (適用於某些情況)。 一般而言，當 Microsoft SQL Server 支援小組介入時，就會執行此層級的分析。 |

診斷 SQL Server 執行緒同步鎖定競爭的一般技術程序如下：

1. **步驟 1**：判斷是否存在與執行緒同步鎖定相關的競爭。

2. **步驟 2**：從 *sys.dm\_ os_spinlock_stats* 擷取統計資料，以找出發生最多競爭的執行緒同步鎖定類型。

3. **步驟 3**：取得 sqlservr.exe (sqlservr.pdb) 的偵錯符號，並將該符號與 SQL Server 執行個體的 SQL Server 服務 .exe 檔案 (sqlservr.exe) 置於相同目錄中。您必須取得所正在執行 SQL Server 特定版本的符號，才能查看輪詢事件的呼叫堆疊。 您可從 Microsoft 符號伺服器上取得 SQL Server 的符號。 如需如何從 Microsoft 符號伺服器下載符號的詳細資訊，請參閱[使用符號進行偵錯](/windows/win32/dxtecharts/debugging-with-symbols) (英文)。

4. **步驟 4**：使用 SQL Server 擴充事件，以追蹤欲知執行緒同步鎖定類型的輪詢事件。

擴充事件可供追蹤「輪詢」\"\"事件，並針對最頻繁嘗試取得執行緒同步鎖定的作業，擷取其呼叫堆疊。 藉由分析呼叫堆疊，即可判斷哪些類型的作業會導致特定執行緒同步鎖定發生競爭。

## <a name="diagnostic-walkthrough"></a>診斷逐步解說

下列逐步解說會示範如何在真實世界的案例中，使用這些工具與技術來診斷執行緒同步鎖定競爭問題。 本逐步解說是以執行基準測試的客戶參與為基礎，在具有 1 TB 記憶體、8 座插槽、64 個實體核心的伺服器上模擬大約 6500 名並行使用者。

### <a name="symptoms"></a>徵兆

出現 CPU 的周期性尖峰，使 CPU 使用率達到將近 100%。 輸送量與 CPU 使用量之間出現差異，因而導致問題。 當出現大型 CPU 尖峰時，即會出現大量周旋的現象，這種現象會在特定時間間隔內的大量 CPU 使用量期間發生。

競爭便是在如此極端情況下，出現了執行緒同步鎖定的阻塞情況。 當執行緒無法繼續處理工作負載而投入所有處理資源，以嘗試取得鎖定的存取權時，就會發生阻塞。 下列效能監視器記錄說明交易記錄輸送量與 CPU 使用量之間的差異，並在最後出現 CPU 使用率的大型尖峰。

![效能監視器中的 CPU 尖峰](./media/diagnose-resolve-spinlock-contention/image9.png)

查詢 *sys.dm_os_spinlock_stats* 以判斷 SOS_CACHESTORE 中是否存在嚴重競爭之後，就能使用擴充事件指令碼來測量欲知執行緒同步鎖定類型的輪詢事件數目。

| 名稱 | 衝突 | 周旋 | 每次衝突的周旋數 | 輪詢 |
|---|---|---|---|---|
| **SOS_CACHESTORE** |       14,752,117 |   942,869,471,526 |   63,914 |                67,900,620 |
| **SOS_SUSPEND_QUEUE** |   69,267,367 |   473,760,338,765 |   6,840  |                2,167,281 |
| **LOCK_HASH** |           5,765,761 |    260,885,816,584 |   45,247 |                3,739,208 |
| **MUTEX** |               2,802,773 |    9,767,503,682 |     3,485  |                350,997 |
| **SOS_SCHEDULER** |       1,207,007 |    3,692,845,572 |     3,060  |                109,746 |

將周旋所造成影響量化的最直接方法，就是查看在周旋次數最高的執行緒同步鎖定類型在相同 1 分鐘間隔內，*sys.dm_os_spinlock_stats* 所公開的輪詢事件數目。 這個方法最適合用來偵測嚴重競爭，該方法會指出執行緒在等候取得執行緒同步鎖定時，何時耗盡其周旋次數限制。 下列指令碼會說明一項進階技術，此技術會利用擴充事件來測量相關的輪詢事件，並找出發生競爭的特定程式碼路徑。

如需 SQL Server 擴充事件的詳細資訊，請參閱 [SQL Server 擴充事件簡介](./extended-events/extended-events.md)。

**指令碼**

```sql
/*
This Scriptis provided "AS IS" with no warranties, and confers no rights.

This script will monitor for backoff events over a given period of time and
capture the code paths (callstacks) for those.

--Find the spinlock types
select map_value, map_key, name from sys.dm_xe_map_values
where name = 'spinlock_types'
order by map_value asc

--Example: Get the type value for any given spinlock type
select map_value, map_key, name from sys.dm_xe_map_values
where map_value IN ('SOS_CACHESTORE', 'LOCK_HASH', 'MUTEX')

Examples:
61LOCK_HASH
144 SOS_CACHESTORE
08MUTEX

*/

--create the even session that will capture the callstacks to a bucketizer
--more information is available in this reference: http://msdn.microsoft.com/en-us/library/bb630354.aspx
create event session spin_lock_backoff on server
      add event sqlos.spinlock_backoff (action (package0.callstack)
where 
type = 61--LOCK_HASH
or type = 144--SOS_CACHESTORE
or type = 8--MUTEX
)
      add target package0.asynchronous_bucketizer (
            set filtering_event_name='sqlos.spinlock_backoff',
            source_type=1, source='package0.callstack')
      with (MAX_MEMORY=50MB, MEMORY_PARTITION_MODE = PER_NODE)

--Ensure the session was created
select * from sys.dm_xe_sessions
where name = 'spin_lock_backoff'

--Run this section to measure the contention 
alter event session spin_lock_backoff on server state=start

--wait to measure the number of backoffs over a 1 minute period
waitfor delay '00:01:00'

--To view the data
--1. Ensure the sqlservr.pdb is in the same directory as the sqlservr.exe
--2. Enable this trace flag to turn on symbol resolution 
DBCC traceon (3656, -1)

--Get the callstacks from the bucketize target
select event_session_address, target_name, execution_count, cast (target_data as XML)
from sys.dm_xe_session_targets xst
inner join sys.dm_xe_sessions xs on (xst.event_session_address = xs.address)
where xs.name = 'spin_lock_backoff'

--clean up the session 
alter event session spin_lock_backoff on server state=stop
drop event session spin_lock_backoff on server
```

藉由分析輸出，即可看到 SOS_CACHESTORE 周旋中最常見程式碼路徑的呼叫堆疊。 在高 CPU 使用率期間多次執行該指令碼，以檢查所傳回呼叫堆疊的一致性。 請注意，位置值區計數最高的呼叫堆疊，在兩個輸出 (35,668 與 8,506) 之間是相同的。 這些呼叫堆疊具有「位置計數」，比下一個最大項目還要大兩個數量級。 此情況會指出欲知的程式碼路徑。

> [!NOTE]
> 先前指令碼所傳回的呼叫堆疊很常見。 當指令碼執行 1 分鐘時，我們發現含 1000 個以上位置計數的呼叫堆疊有問題，但含 10000 個以上的位置計數更可能出現問題，因為後者的位置計數較高。

> [!NOTE]
> 為了便於閱讀，已清除下列輸出的格式。

**輸出 1**

```xml
<BucketizerTarget truncated="0" buckets="256">
<Slot count="35668" trunc="0">
  <value>
      XeSosPkg::spinlock_backoff::Publish 
      SpinlockBase::Sleep 
      SpinlockBase::Backoff 
      Spinlock<144,1,0>::SpinToAcquireOptimistic 
      SOS_CacheStore::GetUserData 
      OpenSystemTableRowset 
      CMEDScanBase::Rowset 
      CMEDScan::StartSearch 
      CMEDCatalogOwner::GetOwnerAliasIdFromSid 
      CMEDCatalogOwner::LookupPrimaryIdInCatalog CMEDCacheEntryFactory::GetProxiedCacheEntryByAltKey
      CMEDCatalogOwner::GetProxyOwnerBySID
      CMEDProxyDatabase::GetOwnerBySID
      ISECTmpEntryStore::Get 
      ISECTmpEntryStore::Get
      NTGroupInfo::`vector deleting destructor'
  </value> 
</Slot>
<Slot count="752" trunc="0">
  <value>
      XeSosPkg::spinlock_backoff::Publish 
      SpinlockBase::Sleep 
      SpinlockBase::Backoff 
      Spinlock<144,1,0>::SpinToAcquireOptimistic 
      SOS_CacheStore::GetUserData 
      OpenSystemTableRowset
      CMEDScanBase::Rowset 
      CMEDScan::StartSearch
      CMEDCatalogOwner::GetOwnerAliasIdFromSid CMEDCatalogOwner::LookupPrimaryIdInCatalog CMEDCacheEntryFactory::GetProxiedCacheEntryByAltKey             CMEDCatalogOwner::GetProxyOwnerBySID 
      CMEDProxyDatabase::GetOwnerBySID 
      ISECTmpEntryStore::Get
      ISECTmpEntryStore::Get 
      ISECTmpEntryStore::Get
  </value>
  </Slot>
```

**輸出 2**

```xml
<BucketizerTarget truncated="0" buckets="256">
<Slot count="8506" trunc="0">
  <value>
      XeSosPkg::spinlock_backoff::Publish 
      SpinlockBase::Sleep+c7 [ @ 0+0x0 SpinlockBase::Backoff Spinlock<144,1,0>::SpinToAcquireOptimistic
      SOS_CacheStore::GetUserData 
      OpenSystemTableRowset 
      CMEDScanBase::Rowset 
      CMEDScan::StartSearch
      CMEDCatalogOwner::GetOwnerAliasIdFromSid CMEDCatalogOwner::LookupPrimaryIdInCatalog CMEDCacheEntryFactory::GetProxiedCacheEntryByAltKey CMEDCatalogOwner::GetProxyOwnerBySID 
      CMEDProxyDatabase::GetOwnerBySID 
      ISECTmpEntryStore::Get
      ISECTmpEntryStore::Get
      NTGroupInfo::`vector deleting destructor'
</value> 
 </Slot>
<Slot count="190" trunc="0">
  <value>
      XeSosPkg::spinlock_backoff::Publish 
      SpinlockBase::Sleep
       SpinlockBase::Backoff 
      Spinlock<144,1,0>::SpinToAcquireOptimistic 
      SOS_CacheStore::GetUserData 
      OpenSystemTableRowset 
      CMEDScanBase::Rowset 
      CMEDScan::StartSearch 
      CMEDCatalogOwner::GetOwnerAliasIdFromSid CMEDCatalogOwner::LookupPrimaryIdInCatalog CMEDCacheEntryFactory::GetProxiedCacheEntryByAltKey CMEDCatalogOwner::GetProxyOwnerBySID 
      CMEDProxyDatabase::GetOwnerBySID 
      ISECTmpEntryStore::Get 
      ISECTmpEntryStore::Get
      ISECTmpEntryStore::Get
   </value> 
 </Slot>
```

在上述範例中，最令人感興趣的堆疊具有最大位置計數 (35,668 與 8,506)，實際上，其位置計數為 \> 1000。

您現在的問題可能是：「我該如何處理這些資訊？」 一般而言，您必須具備 SQL Server 引擎的豐富知識才能利用呼叫堆疊資訊，因此目前僅能概述疑難排解流程。 在此特殊情況下，藉由查看呼叫堆疊，即可看到發生問題的程式碼路徑與安全性及中繼資料查閱有關 (從下列堆疊框架可明顯看出：**CMEDCatalogOwner::GetProxyOwnerBySID & CMEDProxyDatabase::GetOwnerBySID)** 。

這項資訊很難獨立用來解決問題，但其確實能提供一些思路，使我們可集中進行其他疑難排解，以進一步隔離出問題。

因為此問題看似與執行安全性相關檢查的程式碼路徑有關，所以我們決定執行測試，在此測試中，連線到資料庫的應用程式使用者已被授與系統管理員 (sysadmin) 權限。 儘管不建議在生產環境中使用這項技術，但在測試環境中，該技術已確定是一項有用的疑難排解步驟。 在以提升權限 (sysadmin) 所執行的工作階段時，競爭所造成的 CPU 尖峰消失了。

## <a name="options-and-workarounds"></a>選項與因應措施

很顯然，針對執行緒同步鎖定競爭進行疑難排解是一項艱鉅的工作。 目前並沒有任何「通用的最佳方法」。 要進行疑難排解並解決任何效能問題的第一步，就是找出根本原因。 使用本文所述的技術與工具，是您執行所需分析以了解執行緒同步鎖定相關競爭點的首要工作。

當開發新版本的 SQL Server 時，引擎會藉由執行針對高並行系統最佳化的程式碼，以持續改善其可擴縮性。 SQL Server 為高並行系統進行了多次最佳化，其中一項是是最常見競爭點的指數輪詢最佳化。 我們從 SQL Server 2012 開始提供特定的增強功能，藉由利用引擎內所有執行緒同步鎖定的指數輪詢演算法，以特別改善此特定區域。

在設計需要極高效能與規模的高端應用程式時，請考慮如何盡可能地縮短 SQL Server 中所需的程式碼路徑。 較短的程式碼路徑表示資料庫引擎能夠執行較少工作，並自然地避免競爭點。 許多最佳做法皆有一項額外作用，就是減少了引擎所需的工作量，讓工作負載效能得以最佳化。

以本文稍早的幾個最佳做法為例：

* **完整名稱：** 所有物件的完整名稱，可讓 SQL Server 不需要執行解析名稱所需的程式碼路徑。 我們已經發現，當呼叫預存程序而不使用完整名稱時，SOS_CACHESTORE 執行緒同步鎖定類型上也會出現競爭點。 若無法完整授與名稱資格，SQL Server 就需要查閱使用者的預設結構描述，這會導致執行 SQL 所需的程式碼路徑較長。

* **參數化查詢：** 另一個範例是利用參數化查詢與預存程序呼叫來減少產生執行計畫所需的工作。 同樣地，這也能夠縮短待執行的程式碼路徑。

* **LOCK_HASH 競爭：** 在某些情況下，無法避免特定鎖定結構或雜湊貯體衝突的競爭。 雖然 SQL Server 引擎會分割大部分的鎖定結構，但是在取得鎖定的時候，仍然會導致執行緒存取相同的雜湊貯體。 例如，應用程式透過多個並行執行緒來同時存取相同的資料列 (即參考資料)。 這些類型的問題可透過在資料庫結構描述中相應放大該參考資料，或在允許情況下利用 NOLOCK 提示等技術來解決。

標準微調做法一律為微調 SQL Server 工作負載的第一道防禦措施 (例如，編製索引、查詢最佳化、I/O 最佳化等等)。 不過，除了執行標準微調以外，下列可減少執行作業所需程式碼數量的做法亦相當重要。 即使遵循最佳做法，仍然有可能在忙碌的高並行系統上發生執行緒同步鎖定競爭。 使用本文中工具與技術可有助找出或排除這些類型的問題，並判斷何時需要利用適當的 Microsoft 資源以取得協助。

希望這些技術能為這類疑難排解提供實用的方法，並讓您深入了解 SQL Server 所提供的一些更進階效能分析技術。

## <a name="appendix-automate-memory-dump-capture"></a>附錄：自動化記憶體傾印擷取

下列擴充事件指令碼已經過驗證，可在執行緒同步鎖定競爭情況加重時，自動收集記憶體傾印。 在某些情況下，將需要記憶體傾印才能完整診斷問題，Microsoft 支援小組亦可能會要求記憶體傾印，以進行深入分析。 在 SQL Server 2008 中，bucketizer 所擷取到呼叫堆疊中存在 16 個框架的限制，因為不夠深入，以至於無法明確判斷呼叫堆疊在引擎中要進入的確切位置。 SQL Server 2012 藉由將 bucketizer 所擷取到的呼叫堆疊框架數目增加至 32 個，以改善該功能。

下列 SQL 指令碼可用於將記憶體傾印的擷取程序自動化，以協助分析執行緒同步鎖定競爭：

```sql
/*
This script is provided "AS IS" with no warranties, and confers no rights.

Use:    This procedure will monitor for spinlocks with a high number of backoff events
        over a defined time period which would indicate that there is likely significant
        spin lock contention.
        
        Modify the variables noted below before running.


Requires:
        xp_cmdshell to be enabled
            sp_configure 'xp_cmd', 1
            go 
            reconfigure 
            go
        
*********************************************************************************************************/
use tempdb
go 
if object_id('sp_xevent_dump_on_backoffs') is not null
    drop proc sp_xevent_dump_on_backoffs
go 
create proc sp_xevent_dump_on_backoffs
(
    @sqldumper_path                       nvarchar(max)      = '"c:\Program Files\Microsoft SQL Server\100\Shared\SqlDumper.exe"'
    ,@dump_threshold                      int                = 500           --capture mini dump when the slot count for the top bucket exceeds this
    ,@total_delay_time_seconds            int                = 60            --poll for 60 seconds
    ,@PID                                 int                = 0
    ,@output_path                         nvarchar(max)      = 'c:\'
    ,@dump_captured_flag                  int = 0 OUTPUT
    
)
as
/* 
    --Find the spinlock types
    select map_value, map_key, name from sys.dm_xe_map_values
    where name = 'spinlock_types'
    order by map_value asc

    --Example: Get the type value for any given spinlock type
    select map_value, map_key, name from sys.dm_xe_map_values
    where map_value IN ('SOS_CACHESTORE', 'LOCK_HASH', 'MUTEX')
*/
if exists (select * from sys.dm_xe_session_targets xst
                inner join sys.dm_xe_sessions xs on (xst.event_session_address = xs.address)
                where xs.name = 'spinlock_backoff_with_dump')
    drop event session spinlock_backoff_with_dump on server

create event session spinlock_backoff_with_dump  on server
      add event sqlos.spinlock_backoff (action (package0.callstack)
            where
                type = 61                 --LOCK_HASH
                --or type = 144           --SOS_CACHESTORE
                --or type = 8             --MUTEX
                --or type = 53            --LOGCACHE_ACCESS
                --or type = 41            --LOGFLUSHQ
                --or type = 25            --SQL_MGR
                --or type = 39            --XDESMGR
                )
      add target package0.asynchronous_bucketizer (
            set filtering_event_name='sqlos.spinlock_backoff',
            source_type=1, source='package0.callstack')
      with (MAX_MEMORY=50MB, MEMORY_PARTITION_MODE = PER_NODE)

alter event session spinlock_backoff_with_dump  on server state=start


declare @instance_name            nvarchar(max) = @@SERVICENAME
declare @loop_count               int = 1
declare @xml_result               xml 
declare @slot_count               bigint 
declare @xp_cmdshell              nvarchar(max) = null

--start polling for the backoffs
print 'Polling for: ' + convert(varchar(32), @total_delay_time_seconds) + ' seconds'
while (@loop_count < CAST (@total_delay_time_seconds/1 as int))
begin 
    waitfor delay '00:00:01'

    --get the xml from the bucketizer for the session
    select @xml_result= CAST(target_data as xml)
    from sys.dm_xe_session_targets xst
        inner join sys.dm_xe_sessions xs on (xst.event_session_address = xs.address)
    where xs.name = 'spinlock_backoff_with_dump'
    
    --get the highest slot count from the bucketizer
    select @slot_count = @xml_result.value(N'(//Slot/@count)[1]', 'int')

    --if the slot count is higher than the threshold in the one minute period
    --dump the process and clean up session
    if (@slot_count > @dump_threshold)
    begin 
        print 'exec xp_cmdshell ''' + @sqldumper_path + ' ' + convert(nvarchar(max), @PID) + ' 0 0x800 0 c:\ '''
        select @xp_cmdshell = 'exec xp_cmdshell ''' + @sqldumper_path + ' ' + convert(nvarchar(max), @PID) + ' 0 0x800 0 ' + @output_path + ' '''
        exec sp_executesql @xp_cmdshell
        print 'loop count: ' + convert (varchar(128), @loop_count)
        print 'slot count: ' + convert (varchar(128), @slot_count)
        set @dump_captured_flag = 1
        break
    end 

    --otherwise loop 
    set @loop_count = @loop_count + 1

end

--see what was collected then clean up
DBCC traceon (3656, -1)
select event_session_address, target_name, execution_count, cast (target_data as XML)
from sys.dm_xe_session_targets xst
    inner join sys.dm_xe_sessions xs on (xst.event_session_address = xs.address)
where xs.name = 'spinlock_backoff_with_dump'

alter event session spinlock_backoff_with_dump  on server state=stop
drop event session spinlock_backoff_with_dump  on server
go

/* CAPTURE THE DUMPS 
******************************************************************/
--Example: This will run continuously until a dump is created. 
declare @sqldumper_path                nvarchar(max)        = '"c:\Program Files\Microsoft SQL Server\100\Shared\SqlDumper.exe"'
declare @dump_threshold                int                  = 300            --capture mini dump when the slot count for the top bucket exceeds this
declare @total_delay_time_seconds      int                  = 60             --poll for 60 seconds 
declare @PID                           int                  = 0
declare @flag                          tinyint              = 0
declare @dump_count                    tinyint              = 0
declare @max_dumps                     tinyint              = 3              --stop after collecting this many dumps
declare @output_path                   nvarchar(max)        = 'c:\'          --no spaces in the path please :)


--Get the process id for sql server 
declare @error_log table (LogDate datetime,
    ProcessInfo varchar(255),
    Text varchar(max)
    )
insert into @error_log
    exec ('xp_readerrorlog 0, 1, ''Server Process ID''')
select @PID = convert(int, (REPLACE(REPLACE(Text, 'Server Process ID is ', ''), '.', '')))
    from @error_log where Text like ('Server Process ID is%')
print 'SQL Server PID: ' + convert (varchar(6), @PID)

--Loop to monitor the spinlocks and capture dumps. while (@dump_count < @max_dumps)
begin 

    exec sp_xevent_dump_on_backoffs @sqldumper_path             = @sqldumper_path,
                                    @dump_threshold             = @dump_threshold,
                                    @total_delay_time_seconds   = @total_delay_time_seconds,
                                    @PID                        = @PID,
                                    @output_path                = @output_path,
                                    @dump_captured_flag         = @flag OUTPUT
    if (@flag > 0) 
        set @dump_count=@dump_count + 1
    print 'Dump Count: ' + convert(varchar(2), @dump_count)
    waitfor delay '00:00:02'

end
```

## <a name="appendix-capture-spinlock-statistics-over-time"></a>附錄：隨著時間擷取執行緒同步鎖定統計資料

下列指令碼可用於查看特定時段內的執行緒同步鎖定統計資料。 每次執行該指令碼時，就會傳回所收集目前值與先前值之間的差異。

```sql
/* Snapshot the current spinlock stats and store so that this can be compared over a time period
   Return the statistics between this point in time and the last collection point in time.

   **This data is maintained in tempdb so the connection must persist between each execution**
   **alternatively this could be modified to use a persisted table in tempdb. if that
   is changed code should be included to clean up the table at some point.**
*/

use tempdb
go

declare @current_snap_time    datetime
declare @previous_snap_time   datetime

set @current_snap_time = GETDATE()

if not exists(select name from tempdb.sys.sysobjects where name like '#_spin_waits%')
    create table #_spin_waits
    (
        lock_name    varchar(128)
        ,collisions  bigint
        ,spins       bigint
        ,sleep_time  bigint
        ,backoffs    bigint
        ,snap_time   datetime
    )

--capture the current stats
insert into #_spin_waits
    (
        lock_name
        ,collisions
        ,spins
        ,sleep_time
        ,backoffs
        ,snap_time
        )
        select  name
                ,collisions
                ,spins
                ,sleep_time
                ,backoffs
                ,@current_snap_time
        from sys.dm_os_spinlock_stats

select top 1 @previous_snap_time = snap_time from #_spin_waits
                where snap_time < (select max(snap_time) from #_spin_waits)
                order by snap_time desc

--get delta in the spin locks stats   
select top 10
        spins_current.lock_name
        , (spins_current.collisions - spins_previous.collisions) as collisions
        , (spins_current.spins - spins_previous.spins) as spins
        , (spins_current.sleep_time - spins_previous.sleep_time) as sleep_time
        , (spins_current.backoffs - spins_previous.backoffs) as backoffs
        , spins_previous.snap_time as [start_time]
        , spins_current.snap_time as [end_time]
        , DATEDIFF(ss, @previous_snap_time, @current_snap_time) as [seconds_in_sample]
    from #_spin_waits spins_current
    inner join (
        select * from #_spin_waits
          where snap_time = @previous_snap_time
        ) spins_previous on (spins_previous.lock_name = spins_current.lock_name)
    where
        spins_current.snap_time = @current_snap_time
        and spins_previous.snap_time = @previous_snap_time
        and spins_current.spins > 0
    order by (spins_current.spins - spins_previous.spins) desc

--clean up table
delete from #_spin_waits
where snap_time = @previous_snap_time
```

## <a name="next-steps"></a>後續步驟

如需效能監視工具的詳細資訊，請參閱[效能監視及微調工具](./performance/performance-monitoring-and-tuning-tools.md)。
