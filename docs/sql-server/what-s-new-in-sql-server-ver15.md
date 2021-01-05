---
title: SQL Server 2019 的新功能 | Microsoft Docs
description: 了解 SQL Server 2019 (15. x) 的新功能，可供選擇開發語言、資料類型、環境以及作業系統。
ms.date: 11/04/2019
ms.prod: sql
ms.reviewer: ''
ms.technology: release-landing
ms.topic: article
author: MikeRayMSFT
ms.author: mikeray
monikerRange: '>=sql-server-ver15'
ms.openlocfilehash: b04cb475579c73a26ec5977ce52b9521ce69552a
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97642641"
---
# <a name="whats-new-in-sql-server-2019"></a>[!INCLUDE[sql-server-2019](../includes/sssqlv15-md.md)] 的新功能

[!INCLUDE[sqlserver](../includes/applies-to-version/sqlserver.md)]

[!INCLUDE[sql-server-2019](../includes/sssqlv15-md.md)] 以舊版為基礎，可使 SQL Server 發展為平台，讓您能夠選擇開發語言、資料類型、內部部署或雲端環境，以及作業系統。

本文摘要說明 [!INCLUDE[sql-server-2019](../includes/sssqlv15-md.md)] 的新功能和增強功能。

如需詳細資訊和已知問題，請參閱 [[!INCLUDE[sql-server-2019](../includes/sssqlv15-md.md)] 版本資訊](sql-server-version-15-release-notes.md)。

為獲得 [!INCLUDE[sql-server-2019](../includes/sssqlv15-md.md)] 的最佳體驗，請使用[最新的工具](https://aka.ms/getazuredatastudio)。

[!INCLUDE[sql-server-2019](../includes/sssqlv15-md.md)] 引入了適用於 [!INCLUDE[sql-server](../includes/ssnoversion-md.md)] 的 [!INCLUDE[big-data-clusters](../includes/ssbigdataclusters-nover.md)]。 它也提供 SQL Server 資料庫引擎、SQL Server Analysis Services、SQL Server 機器學習服務、Linux 上的 SQL Server 與 SQL Server Master Data Services 的額外功能與改進。

下列影片提供 SQL Server 2019 的 13 分鐘簡介：

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Introducing-SQL-Server-2019/player?WT.mc_id=dataexposed-c9-niner]


下列各節將提供這些功能的概觀。

## <a name="data-virtualization-and-big-data-clusters-2019"></a>資料虛擬化和 [!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ver15.md)]

現今企業通常會管轄大量資料資產，這是由整個公司的分散資料來源所裝載大量資料集所組成。 使用 [!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ver15.md)] 從您所有資料取得近乎即時的見解，可提供完整的環境來處理大型資料集，包括機器學習和 AI 功能。

| 新功能或更新 | 詳細資料 |
|:---|:---|
| 可擴充的巨量資料解決方案 | 為 Kubernetes 上執行的 SQL Server、Spark 和 HDFS 容器[部署可擴充叢集](../big-data-cluster/deploy-get-started.md)。 <br/><br/> 讀取、寫入及處理來自 Transact-SQL 或 Spark 的巨量資料。<br/><br/> 輕鬆結合及分析含有大量巨量資料的高價值關聯式資料。<br/><br/>查詢外部資料來源。<br/><br/>在 SQL Server 的受控 HDFS 中儲存巨量資料。<br/><br/>透過叢集查詢來自多個外部資料來源的資料。<br/><br/> 使用 AI、機器學習和其他分析工作的資料。<br/><br/> 在 [!INCLUDE[big-data-clusters](../includes/ssbigdataclusters-nover.md)] 中[部署及執行應用程式](../big-data-cluster/concept-application-deployment.md)。 <br/><br/> SQL Server 主要執行個體使用 Always On 可用性群組技術，為所有資料庫提供高可用性和災害復原。<br/>|
|使用 PolyBase 的資料虛擬化 | 使用外部資料表來查詢來自外部 SQL Server、Oracle、Teradata、MongoDB 及 ODBC 資料來源的資料，現在具備 [UTF-8 編碼支援](../relational-databases/collations/collation-and-unicode-support.md)。 如需詳細資訊，請參閱[什麼是 PolyBase？](../relational-databases/polybase/polybase-guide.md)。|
| &nbsp; | &nbsp; |

如需詳細資訊，請參閱[什麼是 SQL Server [!INCLUDE[big-data-clusters](../includes/ssbigdataclusters-nover.md)]？](../big-data-cluster/big-data-cluster-overview.md)。

## <a name="intelligent-database"></a>智慧型資料庫
[!INCLUDE[sql-server-2019](../includes/sssqlv15-md.md)] 以舊版的創新為基礎，提供現成的領先業界效能。 從[智慧型查詢處理](../relational-databases/performance/intelligent-query-processing.md)到持續性記憶體裝置的支援，[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 智慧型資料庫功能可改善所有資料庫工作負載的效能和延展性，而不需要變更您的應用程式或資料庫設計。

### <a name="intelligent-query-processing"></a>智慧查詢處理
使用[智慧型查詢處理](../relational-databases/performance/intelligent-query-processing.md)，您知道重要的平行工作負載會在大規模執行時獲得改善。 同時，這些工作負載可配合經常變動的資料世界動態調整。 預設會在最新的[資料庫相容性層級](../t-sql/statements/alter-database-transact-sql-compatibility-level.md#differences-between-compatibility-level-140-and-level-150)設定上提供智慧型查詢處理，讓您能夠以最少的實作工作量提供廣泛影響，進而改善現有工作負載的效能。

|新功能或更新 | 詳細資料 |
|:---|:---|
|資料列模式記憶體授與意見反應 |透過調整批次和資料列模式運算子的記憶體授與大小，以在批次模式記憶體授與意見反應功能上擴充。 這項調整可以自動修正過多的授與，其會導致記憶體浪費並減少並行。 該項調整還可以修正造成佔用大量磁碟資源的記憶體授與不足問題。 請參閱[資料列模式記憶體授與意見反應](../relational-databases/performance/intelligent-query-processing.md#row-mode-memory-grant-feedback)。 |
|資料列存放區上的批次模式 | 啟用批次模式執行功能，而不需要資料行存放區索引。 批次模式執行功能在分析工作負載期間會更有效率地使用 CPU，但在 [!INCLUDE[sql-server-2019](../includes/sssqlv15-md.md)] 之前，只有當查詢包含具有資料行存放區索引的作業時才會使用此功能。 不過，有些應用程式可能使用資料行存放區索引不支援的功能，因此無法利用批次模式。 從 [!INCLUDE[sql-server-2019](../includes/sssqlv15-md.md)] 開始，會在符合條件的分析工作負載 (其查詢包括對任何類型索引 (資料列存放區或資料行存放區) 的作業) 上啟用批次模式。 請參閱[資料列存放區上的批次模式](../relational-databases/performance/intelligent-query-processing.md#batch-mode-on-rowstore)。 |
|純量 UDF 內嵌|將純量 UDF 自動轉換成關聯運算式，並將它們內嵌在呼叫 SQL 查詢中。 此轉換可改善利用純量 UDF 的工作負載效能。 請參閱[純量 UDF 內嵌](../relational-databases/performance/intelligent-query-processing.md#scalar-udf-inlining)。|
|資料表變數延後編譯|針對參考資料表變數的查詢，提升計畫品質與整體效能。 在最佳化和初始編譯期間，此功能會根據實際資料表變數的資料列計數，傳播基數估計值。 這項精確的資料列計數資訊會最佳化下游計畫作業。 請參閱[資料表變數延後編譯](../relational-databases/performance/intelligent-query-processing.md#table-variable-deferred-compilation)。 |
|使用 `APPROX_COUNT_DISTINCT` 的近似查詢處理 |針對絕對有效位數不重要但回應性很重要的案例，`APPROX_COUNT_DISTINCT` 使用比 `COUNT(DISTINCT())` 更少的資源，同時彙總大型資料集，以獲得更高的並行。 請參閱[近似查詢處理](../relational-databases/performance/intelligent-query-processing.md#approximate-query-processing)。|
| &nbsp; | &nbsp; |


### <a name="in-memory-database"></a>記憶體內資料庫
[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] [記憶體內部資料庫](../relational-databases/in-memory-database.md)技術利用新式硬體創新來提供無與倫比的效能和規模。 [!INCLUDE[sql-server-2019](../includes/sssqlv15-md.md)] 以此領域中的記憶體內部線上交易處理 (OLTP) 等早期創新為基礎，可在所有資料庫工作負載上實現新的延展性層級。  

|新功能或更新 | 詳細資料 |
|:---|:---|
|混合式緩衝集區| [!INCLUDE[ssDEnoversion](../includes/ssdenoversion-md.md)] 的新功能，坐落在置於持續性記憶體 (PMEM) 裝置上之資料庫檔案上的資料庫頁面，可在必要時直接存取。 請參閱[混合式緩衝集區](../database-engine/configure-windows/hybrid-buffer-pool.md)。|
|經記憶體最佳化的 TempDB 中繼資料| [!INCLUDE[sql-server-2019](../includes/sssqlv15-md.md)] 所引進新功能是[記憶體內部資料庫](../relational-databases/in-memory-database.md)功能系列的一部分，經記憶體最佳化的 TempDB 中繼資料能有效移除此瓶頸，並為大量 TempDB 工作負載提供新一層的延展性。 在 [!INCLUDE[sql-server-2019](../includes/sssqlv15-md.md)] 中，涉及管理暫存資料表中繼資料的系統資料表，可以移至不需閂鎖之非持久性經記憶體最佳化的資料表。 請參閱[經記憶體最佳化的 TempDB 中繼資料](../relational-databases/databases/tempdb-database.md#memory-optimized-tempdb-metadata)。|
| 資料庫快照集的記憶體內部 OLTP 支援 | [!INCLUDE[sql-server-2019](../includes/sssqlv15-md.md)] 引進建立[資料庫快照集](../relational-databases/databases/database-snapshots-sql-server.md)的支援，包括記憶體最佳化檔案群組的資料庫。 |
| &nbsp; | &nbsp; |

### <a name="intelligent-performance"></a>智慧型效能
[!INCLUDE[sql-server-2019](../includes/sssqlv15-md.md)] 以舊版中的智慧型資料庫創新為基礎，以確保[其執行速度更快](https://docs.microsoft.com/archive/blogs/bobsql/)。 這些改善有助於克服已知的資源瓶頸，並提供選項讓您設定資料庫伺服器，以在所有工作負載中提供可預測的效能。

|新功能或更新 | 詳細資料 |
|:---|:---|
|`OPTIMIZE_FOR_SEQUENTIAL_KEY`|在有助於改善對索引進行高並行插入之輸送量的 [!INCLUDE[ssDEnoversion](../includes/ssdenoversion-md.md)] 內，開啟最佳化。 此選項適用於可能出現最後一頁插入競爭的索引，這通常是具有循序索引鍵 (例如識別資料行、序列或日期/時間資料行) 的索引。 請參閱 [CREATE INDEX](../t-sql/statements/create-index-transact-sql.md#sequential-keys)。|
|強制執行向前快轉及靜態資料指標 | 提供強制支援向前快轉及靜態資料指標的查詢存放區計畫。 請參閱[計畫強制支援向前快轉及靜態資料指標](../relational-databases/performance/monitoring-performance-by-using-the-query-store.md#ctp23)。|
|資源管理| `CREATE WORKLOAD GROUP` 和 `ALTER WORKLOAD GROUP` 的 `REQUEST_MAX_MEMORY_GRANT_PERCENT` 選項可設定值，已從整數變更為浮動資料類型，以允許對記憶體限制採取更細微的控制。 請參閱 [ALTER 工作負載群組](../t-sql/statements/alter-workload-group-transact-sql.md)和[建立工作負載群組](../t-sql/statements/create-workload-group-transact-sql.md)。|
|減少對工作負載的重新編譯| 藉由減少不必要的重新編譯，跨多個範圍使用暫存資料表來改善效能。 請參閱[減少對工作負載的重新編譯](../relational-databases/tables/tables.md#ctp23)。 |
|間接檢查點延展性 |請參閱[改善的間接檢查點延展性](../relational-databases/logs/database-checkpoints-sql-server.md#ctp23)。|
|並行 PFS 更新|[分頁可用空間 (PFS) 分頁](https://techcommunity.microsoft.com/t5/SQL-Server/Under-the-covers-GAM-SGAM-and-PFS-pages/ba-p/383125)是資料庫檔案中的特殊分頁，SQL Server 在配置物件的空間時，可使用這些特殊分頁來協助找出可用空間。 PFS 分頁上的頁面閂鎖爭用通常與 [TempDB](https://support.microsoft.com/help/2154845/recommendations-to-reduce-allocation-contention-in-sql-server-tempdb-d) 建立關聯，但在有許多並行物件配置執行緒時，也可能會發生在使用者資料庫上。 這種改進會改變使用 PFS 更新來管理並行的方式，使其可以在共用閂鎖下更新，而不是獨佔閂鎖。 此行為在從 [!INCLUDE[sql-server-2019](../includes/sssqlv15-md.md)] 開始的所有資料庫 (包括 TempDB) 中預設為啟用。|
|排程器背景工作角色移轉 |背景工作角色移轉可讓閒置排程器從相同 NUMA 節點上另一個排程器的可執行佇列中移轉背景工作角色，並立即繼續所移轉背景工作角色的作業。 這項增強功能可在將長時間執行工作指派給相同排程器的情況下，提供更平衡的 CPU 使用率。 如需詳細資訊，請參閱 [SQL Server 2019 智慧型效能 - 背景工作角色移轉](https://techcommunity.microsoft.com/t5/SQL-Server/SQL-Server-2019-Intelligent-Performance-Worker-Migration/ba-p/939610)。 |
| &nbsp; | &nbsp; |

### <a name="monitoring"></a>監視
監視改善可在您需要時釋出對任何資料庫工作負載的效能見解。

|新功能或更新 | 詳細資料 |
|:---|:---|
|`WAIT_ON_SYNC_STATISTICS_REFRESH` | `sys.dm_os_wait_stats` 動態管理檢視中的新等候類型。 它會顯示花費在同步統計資料重新整理作業上的累積執行個體層級時間。 請參閱 [`sys.dm_os_wait_stats`](../relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql.md)。|
|查詢存放區的自訂擷取原則 | 啟用此原則後，即可在新的查詢存放區擷取原則設定下，使用查詢存放區額外設定來微調特定伺服器中的資料收集。 請參閱 [選項](../t-sql/statements/alter-database-transact-sql-set-options.md)。|
|`LIGHTWEIGHT_QUERY_PROFILING`| 新的資料庫範圍設定。 請參閱 [`LIGHTWEIGHT_QUERY_PROFILING`](../t-sql/statements/alter-database-scoped-configuration-transact-sql.md#lqp)。 |
|`sys.dm_exec_requests` 資料行 `command` | 在 `SELECT` 正在等候同步統計資料更新作業完成以繼續查詢執行的情況下顯示 `SELECT (STATMAN)`。 請參閱 [`sys.dm_exec_requests`](../relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql.md)。|
|`sys.dm_exec_query_plan_stats` | 新的動態管理函式 (DMF)，可針對所有查詢傳回最後一個已知實際執行計畫的對等項目。 請參閱 [sys.dm_exec_query_plan_stats](../relational-databases/system-dynamic-management-views/sys-dm-exec-query-plan-stats-transact-sql.md)。|
|`LAST_QUERY_PLAN_STATS` | 啟用 `sys.dm_exec_query_plan_stats` 的新資料庫範圍設定。 請參閱 [ALTER DATABASE SCOPED CONFIGURATION](../t-sql/statements/alter-database-scoped-configuration-transact-sql.md)。|
|`query_post_execution_plan_profile` | 不同於使用標準分析的 `query_post_execution_showplan`，擴充事件會收集根據輕量型分析的實際執行計畫對等項目。 請參閱[查詢分析基礎結構](../relational-databases/performance/query-profiling-infrastructure.md)。|
|`sys.dm_db_page_info(database_id, file_id, page_id, mode)` | 傳回資料庫中頁面相關資訊的新 DMF。 請參閱 [sys.dm_db_page_info (Transact-SQL)](../relational-databases/system-dynamic-management-views/sys-dm-db-page-info-transact-sql.md)。|
| &nbsp; | &nbsp; |

## <a name="developer-experience"></a>開發人員體驗
[!INCLUDE[sql-server-2019](../includes/sssqlv15-md.md)] 繼續提供世界級的開發人員體驗，包括圖形和空間資料類型的增強功能、UTF-8 支援，以及新的擴充性架構，讓開發人員可以使用其所選語言來取得其所有資料的見解。

### <a name="graph"></a>圖形

|新功能或更新 | 詳細資料 |
|:---|:---|
|邊緣條件約束串聯刪除動作 | 您現在可以於圖表資料庫的邊緣條件約束中定義串聯刪除動作。 請參閱[邊緣條件約束](../relational-databases/tables/graph-edge-constraints.md)。 |
|新的圖形函式 - `SHORTEST_PATH` | 您現在可以使用 `MATCH` 中 `SHORTEST_PATH` 來尋找圖形中任兩個節點之間的最短路徑，或是執行任意長度的周遊。|
|分割區資料表與索引| 圖形資料表現在支援資料表和索引資料分割。 |
|在圖形比對查詢中使用衍生資料表或檢視別名 |請參閱[圖表比對查詢](../t-sql/queries/match-sql-graph.md)。 |
| &nbsp; | &nbsp; |

### <a name="unicode-support"></a>Unicode 支援
支援跨不同國家和地區的企業，其中提供全球多語系資料庫應用程式與服務的需求，對於滿足客戶需求與遵守特定市場法規而言非常重要。 

|新功能或更新 | 詳細資料 |
|:---|:---|
|支援 UTF-8 字元編碼 |支援 UTF-8 用於匯入和匯出編碼，以及作為字串資料的資料庫層級或資料行層級定序。 支援包括 PolyBase 外部資料表及 Always Encrypted (當未與記憶體保護區搭配使用時)。 請參閱[定序與 Unicode 支援](../relational-databases/collations/collation-and-unicode-support.md)。|
| &nbsp; | &nbsp; |

### <a name="language-extensions"></a>語言延伸模組

|新功能或更新 | 詳細資料 |
|:---|:---|
|新增 Java 語言 SDK | 簡化可從 SQL Server 執行的 Java 程式開發。 請參閱[適用於 SQL Server 的 Microsoft Extensibility SDK for Java](../language-extensions/how-to/extensibility-sdk-java-sql-server.md)。 |
|Java 語言 SDK 是開放原始碼 |[Microsoft SQL Server 中適用於 Java 的 Microsoft 擴充性 SDK](https://docs.microsoft.com/sql/language-extensions/how-to/extensibility-sdk-java-sql-server) 現已提供開放原始碼且[可在 GitHub 上取得](https://github.com/microsoft/sql-server-language-extensions)。|
|Java 資料類型的支援|請參閱 [Java 資料類型](../language-extensions/how-to/java-to-sql-data-types.md)。|
|新增預設 JAVA 執行階段 | SQL Server 現在在整個產品中包含 Azul 系統適用於 Java 的 Zulu Embedded 支援。 請參閱 [Free supported Java in SQL Server 2019 is now available](https://cloudblogs.microsoft.com/sqlserver/2019/07/24/free-supported-java-in-sql-server-2019-is-now-available/) (現已推出 SQL Server 2019 中的免費支援 Java)。 |
|SQL Server 語言延伸模組| 以擴充性架構執行外部程式碼。 請參閱 [SQL Server 語言延伸模組](https://docs.microsoft.com/sql/language-extensions/language-extensions-overview)。
|註冊外部語言|`CREATE EXTERNAL LANGUAGE` 這個新的資料定義語言(DDL) 會在 SQL Server 中註冊外部語言，例如 Java。 請參閱[建立外部語言](../t-sql/statements/create-external-language-transact-sql.md)。 |
| &nbsp; | &nbsp; |

### <a name="spatial"></a>空間

|新功能或更新 | 詳細資料 |
|:---|:---|
| 新增空間參考識別碼 (SRID) |[澳洲 GDA2020](http://www.ga.gov.au/scientific-topics/positioning-navigation/geodesy/datums-projections/gda2020) 提供更健全且精確的資料，可與全球定位系統更加緊密結合。 新的 SRID 包含：<ul><li>7843 (適用於地理 2D)</li><li>7844 (適用於地理 3D)</li></ul> 如需新 SRID 的定義，請參閱 [sys.spatial_reference_systems](../relational-databases/system-catalog-views/sys-spatial-reference-systems-transact-sql.md) 檢視。 |
| &nbsp; | &nbsp; |

### <a name="error-messages"></a>錯誤訊息
當擷取、轉換和載入 (ETL) 處理序因來源和目的地沒有相符的資料類型和/或長度而失敗時，過去進行疑難排解非常費時，尤其在大型資料集中時更是如此。 [!INCLUDE[sql-server-2019](../includes/sssqlv15-md.md)] 可讓您更快地深入了解資料截斷錯誤。

|新功能或更新 | 詳細資料 |
|:---|:---|
|詳細資訊截斷警告 | 資料截斷錯誤訊息預設為包含資料表和資料行名稱，以及被截斷的值。 請參閱 [VERBOSE_TRUNCATION_WARNINGS](../t-sql/statements/alter-database-scoped-configuration-transact-sql.md#verbose-truncation)。|
| &nbsp; | &nbsp; |

## <a name="mission-critical-security"></a>任務關鍵性安全性
[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 提供安全性架構，其設計目的是讓資料庫管理員和開發人員建立安全的資料庫應用程式並防範威脅。 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 的每個版本都透過引進新功能對舊版進行改善，[!INCLUDE[sql-server-2019](../includes/sssqlv15-md.md)] 會繼續以此劇本為基礎。

|新功能或更新 | 詳細資料 |
|:---|:---|
|具有安全記憶體保護區的 Always Encrypted|透過在伺服器端安全記憶體保護區中啟用純文字資料上的計算，在具備就地加密和豐富計算的 Always Encrypted 上進行擴充。 就地加密可改善密碼編譯作業 (加密資料行、輪換資料行、加密金鑰等) 的效能和可靠性，因為就地加密會避免將資料移出資料庫。<br><br> 支援豐富計算 (模式比對和比較作業) 可讓更多的範例適用 Always Encypted，以及要求敏感資料保護，但同時也需要在 Transact-SQL 查詢中擁有更豐富功能的應用程式。 請參閱[具有安全記憶體保護區的 Always Encrypted](../relational-databases/security/encryption/always-encrypted-enclaves.md)。|
|SQL Server 組態管理員中的憑證管理|您現在可以使用 SQL Server 組態管理員來進行憑證管理工作，例如檢視及部署憑證。 請參閱[憑證管理 (SQL Server 組態管理員)](../database-engine/configure-windows/manage-certificates.md)。|
|資料探索與分類|資料探索與分類可提供在使用者資料表中分類及標記資料行的功能。 分類敏感資料 (商務、財務、醫療、PII 等) 可以扮演組織資訊保護成長的關鍵角色。 它可以作為下列的基礎結構：<ul><li>協助符合資料隱私權標準和法規合規性需求</li><li>各種安全性情節，例如監視 (稽核)，以及警示敏感性資料的異常存取</li><li>更輕鬆地識別敏感性資料在企業中的位置，讓系統管理員可以採取正確的步驟來保護資料庫</li></ul>|
|SQL Server Audit|另已增強[稽核](../relational-databases/security/auditing/sql-server-audit-database-engine.md)，以在稽核記錄中包含新欄位 `data_sensitivity_information`，其中包含查詢所傳回的實際資料敏感度分類 (標籤)。 如需詳細資料和範例，請參閱 [`ADD SENSITIVITY CLASSIFICATION`](../t-sql/statements/add-sensitivity-classification-transact-sql.md)。|
| &nbsp; | &nbsp; |

## <a name="high-availability"></a>高可用性
大眾部署 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 時都必須考量的一項工作是確定所有任務關鍵性 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 執行個體及其內部資料庫，在企業和終端使用者需要這些項目時都能夠使用。 可用性是 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 平台的重要要件，[!INCLUDE[sql-server-2019](../includes/sssqlv15-md.md)] 引進許多新功能和增強功能，可讓企業確保其資料庫環境具有高可用性。

### <a name="availability-groups"></a>可用性群組

|新功能或更新 | 詳細資料 |
|:---|:---|
|最多五個同步複本|[!INCLUDE[sql-server-2019](../includes/sssqlv15-md.md)] 會將同步複本的數目上限，從 [!INCLUDE[ssSQL17](../includes/sssql17-md.md)] 中最多為 3 的狀態增加至 5。 您可以設定這五個複本的群組，使其在群組內具備自動容錯移轉。 有一個主要複本，再加上四個同步次要複本。|
|次要到主要複本連線重新導向| 可將用戶端應用程式連線導向至主要複本，而不論連接字串中指定的目標伺服器為何。 如需詳細資料，請參閱[次要到主要複本讀取/寫入連線重新導向 (Always On 可用性群組)](../database-engine/availability-groups/windows/secondary-replica-connection-redirection-always-on-availability-groups.md)。|
|HADR 權益| SQL Server 的每個軟體保證客戶都能夠針對 Microsoft 仍然支援的任何 SQL Server 版本，使用三項增強的權益。 如需詳細資料，請參閱[我們在這裡的公告。](https://cloudblogs.microsoft.com/sqlserver/2019/10/30/new-high-availability-and-disaster-recovery-benefits-for-sql-server/)
| &nbsp; | &nbsp; |

### <a name="recovery"></a>復原

|新功能或更新 | 詳細資料 |
|:---|:---|
|加速資料庫復原 | 透過加速資料庫復原 (ADR)，減少重新啟動或長時間執行交易回復後的復原時間。 請參閱[加速資料庫復原](../relational-databases/backup-restore/restore-and-recovery-overview-sql-server.md#adr)。|
| &nbsp; | &nbsp; |

### <a name="resumable-operations"></a>可繼續的作業

|新功能或更新 | 詳細資料 |
|:---|:---|
|線上建置和重建叢集資料行存放區索引 | 請參閱[線上執行索引作業](../relational-databases/indexes/perform-index-operations-online.md)。 |
|可繼續的線上建置資料列存放區索引 | 請參閱[線上執行索引作業](../relational-databases/indexes/perform-index-operations-online.md)。 |
|暫止和繼續透明資料加密 (TDE) 的初始掃描|請參閱[透明資料加密 (TDE) 掃描 - 暫止和繼續](../relational-databases/security/encryption/transparent-data-encryption.md#scan-suspend-resume)。|
| &nbsp; | &nbsp; |

## <a name="platform-choice"></a>平台選擇
[!INCLUDE[sql-server-2019](../includes/sssqlv15-md.md)] 以 [!INCLUDE[ssSQL17](../includes/sssql17-md.md)] 引進的創新為基礎，可讓您在選擇的平台上執行 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]，並提供比以往更多的功能和安全性。

### <a name="linux"></a><a id="sql-server-on-linux"></a>Linux

| 新功能或更新 | 詳細資料 |
|:-----|:-----|
|複寫支援 | 請參閱 [Linux 上的 SQL Server 複寫](../linux/sql-server-linux-replication.md)。 |
|Microsoft Distributed Transaction Coordinator (MSDTC) 支援 | 請參閱[如何在 Linux 上設定 MSDTC](../linux/sql-server-linux-configure-msdtc.md)。 |
|第三方 AD 提供者的 OpenLDAP 支援 | 請參閱[教學課程：在 Linux 上的 SQL Server 使用 Active Directory 驗證](../linux/sql-server-linux-active-directory-authentication.md)。 |
|Linux 上的機器學習服務 | 請參閱[在 Linux 上安裝 SQL Server 機器學習服務 (Python 和 R)](../linux/sql-server-linux-setup-machine-learning.md)。 |
|TempDB 的改善項目 | 根據預設，在 Linux 上進行新的 SQL Server 安裝，會根據邏輯核心的數目 (最多八個資料檔案) 來建立多個 TempDB 資料檔案。 此情況不適用於就地的次要或主要版本升級。 每個 TempDB 檔為 8 MB，且可自動成長到 64 MB。 此行為類似於 Windows 上的預設 SQL Server 安裝。 |
|Linux 上的 PolyBase | 請參閱在 Linux 上為非 Hadoop 連接器[安裝 PolyBase](../relational-databases/polybase/polybase-linux-setup.md)。<br/><br/>請參閱 [PolyBase 類型對應](../relational-databases/polybase/polybase-type-mapping.md)。 |
| 異動資料擷取 (CDC) 支援 | 在 Linux 上現在已針對 [!INCLUDE[sql-server-2019](../includes/sssqlv15-md.md)] 支援異動資料擷取 (CDC)。 |
| &nbsp; | &nbsp; |

### <a name="containers"></a>容器
開始使用 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 的最簡單方式就是使用容器。 [!INCLUDE[sql-server-2019](../includes/sssqlv15-md.md)] 以舊版中引進的創新為基礎，可讓您以安全方式在新的平台上部署 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 容器，並提供更多功能。

|新功能或更新 | 詳細資料 |
|:---|:---|
| Microsoft 容器登錄 | [Microsoft 容器登錄](https://azure.microsoft.com/blog/microsoft-syndicates-container-catalog/)現在已取代適用於新官方 Microsoft 容器映像的 Docker Hub，包括 [!INCLUDE[sql-server-2019](../includes/sssqlv15-md.md)]。 |
| 非根容器 | [!INCLUDE[sql-server-2019](../includes/sssqlv15-md.md)] 引進以非根使用者身分啟動 [!INCLUDE[sql-server](../includes/ssnoversion-md.md)] 程序來建立更安全容器的功能。 請參閱[以非根使用者的身分建置並執行 SQL Server 容器](../linux/sql-server-linux-configure-docker.md#buildnonrootcontainer)。 |
| Red Hat 認證的容器映像 | 從 [!INCLUDE[sql-server-2019](../includes/sssqlv15-md.md)] 開始，您可以在 Red Hat Enterprise Linux 上執行 SQL Server 容器。 |
| PolyBase 和機器學習支援| [!INCLUDE[sql-server-2019](../includes/sssqlv15-md.md)] 引進使用 SQL Server 容器的新方式，例如機器學習服務和 PolyBase。 請參閱[容器 GitHub 存放庫中的 SQL Server ](https://github.com/microsoft/mssql-docker/tree/master/linux/preview/examples)內的一些範例。 |
| &nbsp; | &nbsp; |

## <a name="setup-options"></a>設定選項

|新功能或更新 | 詳細資料 |
|:---|:---| 
|新的記憶體設定選項 | 設定安裝期間的 [最小伺服器記憶體 (MB)]  與 [最大伺服器記憶體 (MB)]  伺服器設定。 請參閱[資料庫引擎設定 - 記憶體頁面](./install/instance-configuration.md#memory)，以及[從命令提示字元安裝 SQL Server](../database-engine/install-windows/install-sql-server-from-the-command-prompt.md#Install) 中的 `USESQLRECOMMENDEDMEMORYLIMITS`、`SQLMINMEMORY` 與 `SQLMAXMEMORY` 參數。 建議值會與[伺服器記憶體設定選項](../database-engine/configure-windows/server-memory-server-configuration-options.md#manually)中的記憶體設定指導方針一致。| 
|新平行處理原則設定選項 | 在安裝期間設定 [平行處理原則的最大程度]  伺服器設定。 請參閱[資料庫引擎設定 - MaxDOP 頁面](./install/instance-configuration.md#maxdop)，以及[從命令提示字元安裝 SQL Server](../database-engine/install-windows/install-sql-server-from-the-command-prompt.md#Install) 中的 `SQLMAXDOP` 參數。 預設值將會與[設定 max degree of parallelism 伺服器設定選項](../database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option.md#Guidelines)中平行處理原則的最大程度指導方針一致。| 
|伺服器/CAL 授權產品金鑰上的設定警告|如果輸入企業伺服器/CAL 授權產品金鑰，且電腦具有 20 個以上的實體核心，或在啟用超執行緒時有 40 個邏輯核心，則會在安裝期間顯示警告。 使用者仍然可以確認該限制並繼續設定，或輸入可支援作業系統最大處理器數量的授權金鑰。|
| &nbsp; | &nbsp; |

## <a name="sql-server-machine-learning-services"></a><a id="ml"></a> SQL Server 機器學習服務

|新功能或更新 | 詳細資料 |
|:---|:---|
|分割區型模型 | 您可以使用新增至 `sp_execute_external_script` 的參數，來處理您資料每個資料區的外部指令碼。 此功能支援訓練許多小型模型 (每個資料的資料分割一個模型)，而不是一個大型模型。 請參閱[建立資料分割模型](../machine-learning/tutorials/r-tutorial-create-models-per-partition.md)。|
|Windows Server 容錯移轉叢集| 您可以在 Windows Server 容錯移轉叢集上設定機器學習服務的高可用性。|
| &nbsp; | &nbsp; |

## <a name="sql-server-analysis-services"></a>SQL Server Analysis Services

此版本引進效能、資源治理與用戶端支援的新功能和改善。

| 新功能或更新 | 詳細資料 |
|:---|:---|
|表格式模型中的計算群組| 計算群組可藉由將常見量值運算式分組為「計算項目」  來大幅減少多餘量值的數目。 若要深入了解，請參閱[表格式模型中的計算群組](/analysis-services/tabular-models/calculation-groups)。 |
|查詢交錯| 查詢交錯是表格式模式的系統設定，可改善高並行處理案例中的使用者查詢回應時間。 若要深入了解，請參閱[查詢交錯](/analysis-services/tabular-models/query-interleaving)。 |
|表格式模型中的多對多關聯性| 可在兩個資料行並非唯一的資料表之間建立多對多關聯性。 若要深入了解，請參閱[表格式模型中的關聯性](/analysis-services/tabular-models/relationships-ssas-tabular)。|
|資源管理的屬性設定| 此版本包含新的記憶體設定：適用於資源治理的 Memory\QueryMemoryLimit、DbpropMsmdRequestMemoryLimit 和 OLAP\Query\RowsetSerializationLimit。 若要深入了解，請參閱[記憶體設定](/analysis-services/server-properties/memory-properties)。|
|Power BI 快取重新整理的治理設定 | 此版本引進 ClientCacheRefreshPolicy 屬性，該屬性可覆寫快取儀表板磚資料和報表資料，以供 Power BI 服務初始載入 Live Connect 報表。 若要深入了解，請參閱[一般屬性](/analysis-services/server-properties/general-properties)。 |
| 線上附加  | 線上附加可用於同步處理內部部署查詢擴充環境中的唯讀複本。 若要深入了解，請參閱[線上附加](/analysis-services/what-s-new-in-sql-server-analysis-services#online-attach)。 |
| &nbsp; | &nbsp; |

## <a name="sql-server-integration-services"></a>SQL Server Integration Services

此版本引進了新功能，可改善檔案作業。

| 新功能或更新 | 詳細資料 |
|:---|:---|
|彈性檔案工作 |在本機檔案系統、Azure Blob 儲存體和 Azure Data Lake Storage Gen2 上執行檔案作業。 請參閱[彈性檔案工作](../integration-services/control-flow/flexible-file-task.md)。|
|彈性檔案來源和目的地 |讀取和寫入 Azure Blob 儲存體的資料，以及 Azure Data Lake Storage Gen2。 請參閱[彈性檔案來源](../integration-services/data-flow/flexible-file-source.md)和[彈性檔案目的地](../integration-services/data-flow/flexible-file-destination.md)。 |

## <a name="sql-server-master-data-services"></a>SQL Server [!INCLUDE[master-data-services](../includes/ssmdsshort-md.md)]

| 新功能或更新 | 詳細資料 |
|:---|:---|
|Azure SQL 受控執行個體資料庫的支援| 在 Azure SQL 受控執行個體上裝載 [!INCLUDE[master-data-services](../includes/ssmdsshort-md.md)]。 請參閱 [[!INCLUDE[master-data-services](../includes/ssmdsshort-md.md)] 安裝和設定](../master-data-services/master-data-services-installation-and-configuration.md#SetUpWeb)。|
|新增 HTML 控制項| HTML 控制項取代所有先前的 Silverlight 元件。 已移除 Silverlight 相依性。|
| &nbsp; | &nbsp; |

## <a name="sql-server-reporting-services"></a>SQL Server Reporting Services

這一版的 SQL Server Reporting Services 功能支援 Azure SQL 受控執行個體、Power BI Premium 資料集、增強的協助工具、Azure Active Directory 應用程式 Proxy，以及透明資料庫加密。 該版本還會為 Microsoft 報表產生器帶來更新。 如需詳細資料，請參閱 [SQL Server Reporting Services 的新功能](../reporting-services/what-s-new-in-sql-server-reporting-services-ssrs.md)。

## <a name="see-also"></a>另請參閱

- [`SqlServer` PowerShell 模組](https://www.powershellgallery.com/packages/Sqlserver)
- [SQL Server PowerShell 文件](../powershell/sql-server-powershell.md)

## <a name="next-steps"></a>後續步驟

- [SQL Server 研討會](https://aka.ms/sqlworkshops)
- [[!INCLUDE[sql-server-2019](../includes/sssqlv15-md.md)] 版本資訊](sql-server-version-15-release-notes.md)
- [Microsoft [!INCLUDE[sql-server-2019](../includes/sssqlv15-md.md)]：Technical white paper](https://aka.ms/sql2019whitepaper)

[!INCLUDE[get-help-options](../includes/paragraph-content/get-help-options.md)]
