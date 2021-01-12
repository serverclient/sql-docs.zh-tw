---
title: 使用查詢存放區的最佳做法 | Microsoft Docs
description: 了解工作負載與 SQL Server 查詢存放區搭配使用的最佳做法，例如使用最新的 SQL Server Management Studio 和查詢效能深入解析。
ms.custom: ''
ms.date: 12/23/2020
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.technology: performance
ms.topic: conceptual
helpviewer_keywords:
- Query Store, best practices
ms.assetid: 5b13b5ac-1e4c-45e7-bda7-ebebe2784551
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: =azuresqldb-current||>=sql-server-2016||= azure-sqldw-latest||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: cccb47e059938745aa6166902402c8b94b674722
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98099324"
---
# <a name="best-practices-with-query-store"></a>使用查詢存放區的最佳做法

[!INCLUDE [SQL Server ASDB, ASDBMI, ASDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa.md)]

本文概述工作負載與 SQL Server 查詢存放區搭配使用的最佳做法。

## <a name="use-the-latest-ssmanstudiofull"></a><a name="SSMS"></a> 使用最新版 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)]

[!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 提供一組使用者介面，其設計為用來設定查詢存放區，以及用來取用所收集與工作負載相關的資料。 [在此](../../ssms/download-sql-server-management-studio-ssms.md)下載最新版的 [!INCLUDE[ssManStudio](../../includes/ssmanstudio-md.md)]。

如需如何在疑難排解案例中使用查詢存放區的快速描述，請參閱[查詢存放區@Azure部落格](https://azure.microsoft.com/blog/query-store-a-flight-data-recorder-for-your-database/)。

## <a name="use-query-performance-insight-in-azure-sql-database"></a><a name="Insight"></a> 在 Azure SQL 資料庫中使用查詢效能深入解析

如果在 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 中執行查詢存放區，則可使用[查詢效能見解](/azure/sql-database/sql-database-query-performance)來分析隨時間的資源使用量。 雖然可使用 [!INCLUDE[ssManStudio](../../includes/ssmanstudio-md.md)] 和 [Azure Data Studio](../../azure-data-studio/what-is-azure-data-studio.md) 來取得所有查詢的資源耗用量詳細資訊 (例如 CPU、記憶體和 I/O)，但查詢效能見解能提供一個快速並有效率方式來判斷資源耗用量對資料庫整體 DTU 耗用量的影響。 如需詳細資訊，請參閱 [Azure SQL 資料庫查詢效能深入解析](/azure/azure-sql/database/query-performance-insight-use)。

本節描述最佳的組態預設值，其設計目的是確保查詢存放區及相依功能可確實運作。 預設組態已針對持續收集資料最佳化，也就是在 OFF/READ_ONLY 狀態花費最少的時間。 如需所有可用查詢存放區選項的詳細資訊，請參閱 [LTER DATABASE SET 選項 (Transact-SQL)](../../t-sql/statements/alter-database-transact-sql-set-options.md#query-store)。

| 組態 | 描述 | 預設 | 註解 |
| --- | --- | --- | --- |
| MAX_STORAGE_SIZE_MB |指定查詢存放區在客戶資料庫內佔用的資料空間限制 |100 |對新資料庫強制執行 |
| INTERVAL_LENGTH_MINUTES |定義彙總和保存查詢計畫所收集到的執行階段統計資料的時段大小。 對於此組態定義的一段時間，每個使用中的查詢計劃最多會有一個資料列 |60 |對新資料庫強制執行 |
| STALE_QUERY_THRESHOLD_DAYS |以時間為基礎的清理原則，可控制保存執行階段統計資料和非使用中查詢的保留期限 |30 |對新資料庫和具有先前的預設值 (367) 的資料庫強制執行 |
| SIZE_BASED_CLEANUP_MODE |指定當查詢存放區資料大小接近限制時，是否進行自動資料清理 |AUTO |對所有資料庫強制執行 |
| QUERY_CAPTURE_MODE |指定是否會追蹤所有查詢或只有查詢的子集 |AUTO |對所有資料庫強制執行 |
| FLUSH_INTERVAL_SECONDS |指定擷取的執行階段統計資料在排清到磁碟之前，保留在記憶體中的最大期間 |900 |對新資料庫強制執行 |
| | | | |

> [!IMPORTANT]
> 這些預設會自動套用在所有 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 查詢存放區啟用的最終階段中。 在啟用後，[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 便不會變更客戶設定的組態值，除非這些組態值會對主要工作負載或查詢存放區的可靠作業造成負面影響。

> [!NOTE]  
> 您無法在 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 單一資料庫和彈性集區停用查詢存放區。 執行 `ALTER DATABASE [database] SET QUERY_STORE = OFF` 將會傳回警告 `'QUERY_STORE=OFF' is not supported in this version of SQL Server.`。 

如果您想要繼續使用自訂設定，請使用 [ALTER DATABASE 搭配查詢存放區選項](../../t-sql/statements/alter-database-transact-sql-set-options.md#query-store) ，以將組態還原到先前的狀態。 請查看[使用查詢存放區的最佳做法](../../relational-databases/performance/best-practice-with-the-query-store.md)來了解如何選擇最佳的組態參數。

## <a name="use-query-store-with-elastic-pool-databases"></a>搭配彈性集區資料庫使用查詢存放區

您可以放心地在所有資料庫中使用查詢存放區，即使在緊密壓縮的集區中也是一樣。 我們已妥善解決在彈性集區中啟用大量資料庫的查詢存放區時，可能發生的所有過度使用資源問題。

## <a name="keep-query-store-adjusted-to-your-workload"></a><a name="Configure"></a> 針對工作負載調整查詢存放區

根據您的工作負載和效能疑難排解需求設定查詢存放區。
預設參數雖然是一個好的起點，但是您應該監視查詢存放區在一段時間內的運作狀況，並據以調整其設定。

 ![查詢存放區屬性](../../relational-databases/performance/media/query-store-properties.png "query-store-properties")

 以下是設定參數值時可遵循的指導方針：

[大小上限 (MB)]：指定查詢存放區可在您資料庫內使用的資料空間限制。 這是最重要的設定，它會直接影響查詢存放區的作業模式。

由於查詢存放區會收集查詢、執行計劃和統計資料，因此它在資料庫中的大小會逐漸增加，直到達到此限制為止。 發生此情況時，查詢存放區會自動將作業模式變更為唯讀，並停止收集新的資料，這表示您的效能分析已不再正確。

[!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 和 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 中的預設值是 100 MB。 如果您的工作負載會產生很多不同查詢和計劃，或您想保留更長時間的查詢記錄，則此大小可能會不夠。 從 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 開始，預設值為 1 GB。 追蹤目前的空間使用量，並增加 [大小上限 (MB)] 值以防查詢存放區轉換為唯讀模式。

> [!IMPORTANT]
> 系統不會嚴格強制執行 [大小上限 (MB)] 的限制。 只有當查詢存放區將資料寫入磁碟時，系統才會檢查儲存體大小。 此間隔是由 [資料排清間隔 (分鐘)] 選項所設定。 如果查詢存放區違反儲存體大小檢查之間的大小上限，即會轉換為唯讀模式。 如果啟用 [以大小為基準的清除模式]，也會觸發強制執行大小上限的清除機制。

使用 [!INCLUDE[ssManStudio](../../includes/ssmanstudio-md.md)] 或執行下列指令碼取得有關查詢存放區大小的最新資訊：

```sql
USE [QueryStoreDB];
GO

SELECT actual_state_desc, desired_state_desc, current_storage_size_mb,
 max_storage_size_mb, readonly_reason
FROM sys.database_query_store_options;
```

下列指令碼會設定新的 [大小上限 (MB)] 值：

```sql
ALTER DATABASE [QueryStoreDB]
SET QUERY_STORE (MAX_STORAGE_SIZE_MB = 1024);
```

 [資料排清間隔 (分鐘)]：它定義將所收集執行階段統計資料保存到磁碟的頻率。 在圖形化使用者介面 (GUI) 中是以分鐘表示，但在 [!INCLUDE[tsql](../../includes/tsql-md.md)] 中則是以秒表示。 預設值為 900 秒，在圖形化使用者介面即 15 分鐘。 如果您的工作負載不會產生大量不同的查詢與計劃，或您可以在資料庫關閉之前承受較長的資料保存時間，請考慮使用較高的值。

> [!NOTE]
> 您可以使用追蹤旗標 7745，防止查詢存放區在發生容錯移轉或關機命令時將資料寫入磁碟。 如需詳細資訊，請參閱 [在任務關鍵性伺服器上使用追蹤旗標](#Recovery)一節。

使用 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 或 [!INCLUDE[tsql](../../includes/tsql-md.md)] 為 [資料排清間隔] 設定不同的值：

```sql
ALTER DATABASE [QueryStoreDB]
SET QUERY_STORE (DATA_FLUSH_INTERVAL_SECONDS = 900);
```

[統計資料收集間隔]：定義所收集執行階段統計資料的資料粒度層級 (以分鐘表示)。 預設值是 60 分鐘。 如果您需要更精細的資料粒度或使用較短時間來偵測與解決問題，請考慮使用較低的值。 但請注意，它會直接影響查詢存放區資料的大小。 使用 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 或 [!INCLUDE[tsql](../../includes/tsql-md.md)] 為 [統計資料收集間隔] 設定不同的值：

```sql
ALTER DATABASE [QueryStoreDB]
SET QUERY_STORE (INTERVAL_LENGTH_MINUTES = 60);
```

[過時查詢臨界值 (天數)]：以時間為基礎的清除原則，可控制所保存執行階段統計資料和非作用中查詢的保留期限 (以天表示)。 根據預設，查詢存放區設定為保留資料 30 天，但對您的案例來說，可能並不需要這麼久。

請避免保留您不打算使用的歷史資料。 這個做法可降低變更為唯讀狀態的機率。 您也可以更輕鬆預測出查詢存放區資料大小及偵測/解決問題的時間。 使用 [!INCLUDE[ssManStudio](../../includes/ssmanstudio-md.md)] 或下列指令碼來設定以時間為基礎的清除原則：

```sql
ALTER DATABASE [QueryStoreDB]
SET QUERY_STORE (CLEANUP_POLICY = (STALE_QUERY_THRESHOLD_DAYS = 90));
```

[以大小為基準的清除模式]：可指定當查詢存放區的資料大小到達限制時是否自動清除資料。 請啟用 [以大小為基準的清除模式]，以確保查詢存放區一律以讀寫模式執行並收集最新的資料。  請注意，在繁重的工作負載下，無法保證查詢存放區清除可持續將資料大小維持在限制內。 自動資料清除的進度可能會落後，並 (暫時) 切換為唯讀模式。

```sql
ALTER DATABASE [QueryStoreDB]
SET QUERY_STORE (SIZE_BASED_CLEANUP_MODE = AUTO);
```

[查詢存放區擷取模式]：可指定查詢存放區的查詢擷取原則。

- **全部**：擷取所有的查詢。 此為 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 和 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 中的預設選項。
- [自動]：會忽略不頻繁的查詢，以及包含無意義編譯和執行期間的查詢。 執行計數、編譯和執行階段持續時間的臨界值由系統內部決定。 從 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 開始，這是預設選項。
- **無**：查詢存放區會停止擷取新的查詢。
- [自訂]：允許額外控制及微調資料收集原則的功能。 新的自訂設定可定義在內部擷取原則時間臨界值期間會發生什麼情況。 這是評估可設定條件的時間界限；如果任何條件成立的話，即可由查詢存放區來擷取查詢。

> [!IMPORTANT]
> 當 [查詢存放區擷取模式] 設定為 [全部]、[自動] 或 [自訂] 時，系統一律會擷取資料指標、預存程序中的查詢，以及原生編譯的查詢。 若要擷取原生編譯查詢，請使用 [sys.sp_xtp_control_query_exec_stats](../../relational-databases/system-stored-procedures/sys-sp-xtp-control-query-exec-stats-transact-sql.md) 來啟用收集每個查詢的統計資料。

 下列指令碼會將 QUERY_CAPTURE_MODE 設定為 AUTO：

```sql
ALTER DATABASE [QueryStoreDB]
SET QUERY_STORE (QUERY_CAPTURE_MODE = AUTO);
```

### <a name="examples"></a>範例

下列範例會將 QUERY_CAPTURE_MODE 設定為 AUTO，並在 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 中設定其他建議選項：

```sql
ALTER DATABASE [QueryStoreDB]
SET QUERY_STORE = ON
    (
      OPERATION_MODE = READ_WRITE,
      CLEANUP_POLICY = ( STALE_QUERY_THRESHOLD_DAYS = 90 ),
      DATA_FLUSH_INTERVAL_SECONDS = 900,
      QUERY_CAPTURE_MODE = AUTO,
      MAX_STORAGE_SIZE_MB = 1000,
      INTERVAL_LENGTH_MINUTES = 60
    );
```

下列範例會將 QUERY_CAPTURE_MODE 設定為 AUTO，並在 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 中設定其他建議選項以包含等候統計資料：

```sql
ALTER DATABASE [QueryStoreDB]
SET QUERY_STORE = ON
    (
      OPERATION_MODE = READ_WRITE,
      CLEANUP_POLICY = ( STALE_QUERY_THRESHOLD_DAYS = 90 ),
      DATA_FLUSH_INTERVAL_SECONDS = 900,
      QUERY_CAPTURE_MODE = AUTO,
      MAX_STORAGE_SIZE_MB = 1000,
      INTERVAL_LENGTH_MINUTES = 60,
      SIZE_BASED_CLEANUP_MODE = AUTO,
      MAX_PLANS_PER_QUERY = 200,
      WAIT_STATS_CAPTURE_MODE = ON
    );
```

下列範例會將 QUERY_CAPTURE_MODE 設定為 AUTO，並在 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 中設定其他建議選項，並「選擇性」使用其預設值來設定 CUSTOM 擷取原則，而不是新的預設 AUTO 擷取模式：

```sql
ALTER DATABASE [QueryStoreDB]
SET QUERY_STORE = ON
    (
      OPERATION_MODE = READ_WRITE,
      CLEANUP_POLICY = ( STALE_QUERY_THRESHOLD_DAYS = 90 ),
      DATA_FLUSH_INTERVAL_SECONDS = 900,
      MAX_STORAGE_SIZE_MB = 1000,
      INTERVAL_LENGTH_MINUTES = 60,
      SIZE_BASED_CLEANUP_MODE = AUTO,
      MAX_PLANS_PER_QUERY = 200,
      WAIT_STATS_CAPTURE_MODE = ON,
      QUERY_CAPTURE_MODE = CUSTOM,
      QUERY_CAPTURE_POLICY = (
        STALE_CAPTURE_POLICY_THRESHOLD = 24 HOURS,
        EXECUTION_COUNT = 30,
        TOTAL_COMPILE_CPU_TIME_MS = 1000,
        TOTAL_EXECUTION_CPU_TIME_MS = 100
      )
    );
```

## <a name="start-with-query-performance-troubleshooting"></a>開始針對查詢效能進行疑難排解

查詢存放區的疑難排解工作流程很簡單，如下圖所示：

![查詢存放區疑難排解](../../relational-databases/performance/media/query-store-troubleshooting.png "query-store-troubleshooting")

如上節所述，使用 [!INCLUDE[ssManStudio](../../includes/ssmanstudio-md.md)] 啟用查詢存放區，或執行下列 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式：

```sql
ALTER DATABASE [DatabaseOne] SET QUERY_STORE = ON;
```

為了讓查詢存放區收集到可準確呈現您工作負載的資料集，這需要一些時間。 通常一天就已足夠，即使是非常複雜的工作負載。 不過，在您啟用功能之後，即可開始探索資料並識別需要立即處理的查詢。 在 [!INCLUDE[ssManStudio](../../includes/ssmanstudio-md.md)] 的 [物件總管] 中，前往資料庫節點下的查詢存放區子資料夾，以開啟特定案例的疑難排解檢視。

[!INCLUDE[ssManStudio](../../includes/ssmanstudio-md.md)] 查詢存放區檢視會操作執行計量組，每個都以下列任一統計資料函式表示：

|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 版本|執行計量|統計資料函式|
|----------------------|----------------------|------------------------|
|[!INCLUDE[ssSQL15](../../includes/sssql15-md.md)]|CPU 時間、持續時間、執行計數、邏輯讀取、邏輯寫入、記憶體耗用量、實體讀取、CLR 時間、平行處理原則的程度 (DOP) 和資料列計數|平均值、最大值、最小值、標準差、總計|
|[!INCLUDE[ssSQL17](../../includes/sssql17-md.md)]|CPU 時間、持續時間、執行計數、邏輯讀取、邏輯寫入、記憶體耗用量、實體讀取、CLR 時間、平行處理原則的程度、資料列計數、記錄檔記憶體、TempDB 記憶體和等候時間|平均值、最大值、最小值、標準差、總計|

下圖顯示顯示如何找出查詢存放區檢視 ︰

![查詢存放區檢視](../../relational-databases/performance/media/objectexplorerquerystore_sql17.png "查詢存放區檢視")

下表說明每個查詢存放區檢視的使用時機：

|SQL Server Management Studio 檢視|狀況|
|---------------|--------------|
|**迴歸查詢**|找出執行計量最近已迴歸的查詢 (例如，變得更糟)。 <br />使用此檢視，以將您在應用程式中觀察到需要修正或改善的效能問題與實際查詢相互關聯。|
|**整體資源耗用量**|針對任何執行計量，分析資料庫的整體資源耗用量。<br />使用此檢視來識別資源模式 (每日與每晚的工作負載) 和最佳化資料庫的整體耗用量。|
|**最耗用資源的查詢**|選擇一個您感興趣的執行計量，並識別在所供時間間隔中具有最極端值的查詢。 <br />使用此檢視以將注意力放在最相關的查詢，也就是對資料庫資源耗用量影響最大的查詢。|
|**強制計劃的查詢**|使用查詢存放區列出先前的強制計畫。 <br />使用此檢視快速存取所有目前的強制計畫。|
|**高變化的查詢**|當具有高執行變化的查詢與任何可用維度 (例如，所需時間間隔的持續時間、CPU 時間、IO 和記憶體使用量) 產生關聯時，對其進行分析。<br />您可以使用此檢視來識別含廣泛變化效能的查詢，這種效能會跨應用程式影響使用者體驗。|
|**查詢等候統計資料**|分析資料庫中最常使用的等候類別，以及哪些查詢最常參與所選取的等候類別。<br />使用此檢視來分析等候統計資料，並識別可能跨應用程式影響使用者體驗的查詢。<br /><br />適用於：從 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] v18.0 和 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 開始。|
|**追蹤查詢**|即時追蹤最重要的查詢的執行。 一般而言，當您有包含強制計畫的查詢且想要確定查詢效能是否穩定時，會使用此檢視。|

> [!TIP]
> 如需詳細描述，以了解如何使用 [!INCLUDE[ssManStudio](../../includes/ssmanstudio-md.md)] 識別最耗用資源的查詢，並修正那些因為計劃選擇變更而迴歸的查詢，請參閱[查詢存放區@Azure部落格](https://azure.microsoft.com/blog/query-store-a-flight-data-recorder-for-your-database/)。

當您識別出效能次佳的查詢時，您的動作將取決於問題本質。

- 如果查詢執行時有多個計劃，且最後一個計劃明顯比前一個計劃差，則您可以使用計劃強制機制來強制執行。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會嘗試在最佳化工具中強制執行計劃。 如果計劃強制失敗，會引發 XEvent，系統會指示最佳化工具以一般方式最佳化。

  ![查詢存放區強制執行計畫](../../relational-databases/performance/media/query-store-force-plan.png "query-store-force-plan")

  > [!NOTE]
  > 上一個圖形針對特定查詢計劃可能具有不同形狀，每個可能狀態的意義如下：<br />
  >
  > |形狀|意義|
  > |-------------------|-------------|
  > |Circle|查詢已完成，亦即已順利完成一般執行。|
  > |Square|已取消，亦即用戶端起始項目已中止執行。|
  > |Triangle|已失敗，亦即例外狀況已中止執行。|
  >
  > 此外，該形狀大小會反映指定時間間隔內的查詢執行計數。 此大小會隨著執行數目提高而增加。

- 您可能會推斷查詢遺漏最佳執行所需的索引。 此資訊會顯示於查詢執行計畫內。 建立遺漏的索引，並使用查詢存放區來檢查查詢效能。

   ![查詢存放區顯示計畫](../../relational-databases/performance/media/query-store-show-plan.png "query-store-show-plan")

如果您在 [!INCLUDE[ssSDS](../../includes/sssds-md.md)]上執行您的工作負載，請註冊 [!INCLUDE[ssSDS](../../includes/sssds-md.md)] 索引建議程式以自動接收索引建議。

- 在某些情況下，如果您發現執行計劃中預估和實際資料列數目之間的差異非常大，您可以強制統計資料重新編譯。
- 請重寫有問題的查詢，以充分利用查詢參數化或實作較佳的邏輯等。

## <a name="verify-that-query-store-collects-query-data-continuously"></a><a name="Verify"></a> 驗證查詢存放區會持續收集查詢資料

查詢存放區可以無訊息方式變更作業模式。 請定期監視查詢存放區的狀態，以確定查詢存放區持續運作，並採取動作以免因可預防的問題導致失敗。 執行下列查詢來判斷作業模式，並檢視最相關的參數：

```sql
USE [QueryStoreDB];
GO

SELECT actual_state_desc, desired_state_desc, current_storage_size_mb,
    max_storage_size_mb, readonly_reason, interval_length_minutes,
    stale_query_threshold_days, size_based_cleanup_mode_desc,
    query_capture_mode_desc
FROM sys.database_query_store_options;
```

`actual_state_desc` 和 `desired_state_desc` 之間的差異表示作業模式自動發生變更。 最常見的變更就是查詢存放區以無訊息模式切換到唯讀模式。 在極少數情況下，查詢存放區會因為內部錯誤而造成 ERROR 狀態。

當實際狀態是唯讀時，請使用 **readonly_reason** 資料行來判斷根本原因。 通常您會發現查詢存放區因為已超過大小配額而轉換為唯讀模式。 在此情況下，**readonly_reason** 是設定為 65536。 針對其他原因，請參閱 [sys.database_query_store_options &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-database-query-store-options-transact-sql.md)。

請考慮下列步驟將查詢存放區切換為讀寫模式並啟用資料收集：

- 使用 **ALTER DATABASE** 的 **MAX_STORAGE_SIZE_MB** 選項增加最大儲存體大小。
- 使用下列陳述式清除查詢存放區資料：

  ```sql
  ALTER DATABASE [QueryStoreDB] SET QUERY_STORE CLEAR;
  ```

您可以執行下列陳述式，套用其中一或兩個步驟，明確地將作業模式變更回讀寫：

```sql
ALTER DATABASE [QueryStoreDB]
SET QUERY_STORE (OPERATION_MODE = READ_WRITE);
```

採取下列積極步驟：

- 您可以套用最佳作法，以避免無訊息的操作模式變更。 確認查詢存放區大小一律低於允許的最大值，以大幅降低轉換為唯讀模式的機率。 啟用以大小為基礎的原則，如[設定查詢存放區](#Configure)一節所述，以讓查詢存放區在接近大小限制時自動清除資料。
- 若要確認保留最新的資料，請設定以時間為基礎的原則，以定期移除過時的資訊。
- 最後，請考慮將 [查詢擷取模式] 設定為 [自動]，因為這會篩選掉和工作負載較不相關的查詢。

### <a name="error-state"></a>ERROR 狀態

若要復原查詢存放區， 請嘗試明確地設定讀寫模式，並再次檢查實際狀態。

```sql
ALTER DATABASE [QueryStoreDB]
SET QUERY_STORE (OPERATION_MODE = READ_WRITE);
GO

SELECT actual_state_desc, desired_state_desc, current_storage_size_mb,
    max_storage_size_mb, readonly_reason, interval_length_minutes,
    stale_query_threshold_days, size_based_cleanup_mode_desc,
    query_capture_mode_desc
FROM sys.database_query_store_options;
```

如果問題持續發生，表示磁碟上保存了損毀的查詢存放區資料。

從 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 開始，您可以透過在受影響的資料庫中執行 **sp_query_store_consistency_check** 預存程序來復原查詢存放區。 您必須先停用查詢存放區，才能嘗試進行復原作業。 針對 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)]，您需要從查詢存放區清除資料，如下所示。

若復原失敗，您可以嘗試清除查詢存放區，再設定讀寫模式。

```sql
ALTER DATABASE [QueryStoreDB]
SET QUERY_STORE CLEAR;
GO

ALTER DATABASE [QueryStoreDB]
SET QUERY_STORE (OPERATION_MODE = READ_WRITE);
GO

SELECT actual_state_desc, desired_state_desc, current_storage_size_mb,
    max_storage_size_mb, readonly_reason, interval_length_minutes,
    stale_query_threshold_days, size_based_cleanup_mode_desc,
    query_capture_mode_desc
FROM sys.database_query_store_options;
```

## <a name="set-the-optimal-query-store-capture-mode"></a>設定最佳查詢存放區擷取模式

在查詢存放區中保留最相關的資料。 下表描述每個查詢擷取模式的典型案例：

|查詢存放區擷取模式|狀況|
|------------------------|--------------|
|**全部**|根據所有查詢圖形、其執行頻率和其他統計資料，徹底分析您的工作負載。<br /><br /> 識別您工作負載中的新查詢。<br /><br /> 偵測是否使用臨機操作查詢來識別使用者或自動參數化的機會。<br /><br />注意:這是 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 和 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 中的預設擷取模式。|
|**Auto**|將注意力放在相關且可採取動作的查詢上。 例如，那些會定期執行或耗用大量資源的查詢。<br /><br />注意:從 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 開始，這是預設的擷取選項。|
|**None**|您已擷取要在執行階段中監視的查詢集，並想排除其他查詢可能會造成的分心。<br /><br /> [無] 適合用於測試和效能評定環境。<br /><br /> 「無」也適用於在出貨時已將查詢存放區組態設定為監視其應用程式工作負載的軟體廠商。<br /><br /> 您應該謹慎使用 [無]，以免錯失追蹤重要新查詢並將其最佳化的機會。 除非您有需要「無」的特定案例，否則請避免使用。|
|**Custom**|[!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 會在 `ALTER DATABASE SET QUERY_STORE` 命令下引進 [自訂] 擷取模式。 啟用後，即可在新的查詢存放區擷取原則設定下，使用額外查詢存放區設定來微調特定伺服器中的資料收集。<br /><br />新的自訂設定可定義在內部擷取原則時間臨界值期間會發生什麼情況。 這是評估可設定條件的時間界限；如果任何條件成立的話，即可由查詢存放區來擷取查詢。 如需詳細資訊，請參閱 [ALTER DATABASE SET 選項 &#40;Transact-SQL&#41;](../../t-sql/statements/alter-database-transact-sql-set-options.md)。|

> [!NOTE]
> 當 [查詢存放區擷取模式] 設定為 [全部]、[自動] 或 [自訂] 時，系統一律會擷取資料指標、預存程序中的查詢，以及原生編譯的查詢。 若要擷取原生編譯查詢，請使用 [sys.sp_xtp_control_query_exec_stats](../../relational-databases/system-stored-procedures/sys-sp-xtp-control-query-exec-stats-transact-sql.md) 來啟用收集每個查詢的統計資料。

## <a name="keep-the-most-relevant-data-in-query-store"></a>在查詢存放區中保留最相關的資料

將查詢存放區設定為僅包含相關資料，以利持續執行、提供良好的疑難排解體驗，並對您的一般工作負載影響最小。
下表提供最佳作法：

|最佳做法|設定|
|-------------------|-------------|
|限制保留的歷程記錄資料。|設定以時間為基礎的原則，以啟用自動清除。|
|篩選掉不相關的查詢。|將 [查詢存放區擷取模式] 設定為 [自動]。|
|當到達大小上限時，刪除比較不相關的查詢。|啟用大小基礎的清除原則。|

## <a name="avoid-using-non-parameterized-queries"></a><a name="Parameterize"></a> 請避免使用非參數化查詢

在非必要時使用非參數化查詢並不是最佳做法。 隨選分析就是一個範例。 您無法重複使用快取的計劃，這會強制查詢最佳化工具編譯每個唯一查詢文字的查詢。 如需詳細資訊，請參閱[使用強制參數化的指導方針](../../relational-databases/query-processing-architecture-guide.md#ForcedParamGuide)。

此外，查詢存放區可能因為大量潛在不同的查詢文字，導致出現大量具有類似圖形的不同執行計劃，而快速超過大小配額。 因此，您的工作負載會出現次佳效能，而查詢存放區可能會切換到唯讀模式，或不斷刪除資料以嘗試跟上內送查詢。

請考慮下列選項：

- 在適用情況下將查詢參數化。 例如，將查詢包裝在預存程序或 sp_executesql 內。 如需詳細資訊，請參閱[參數和執行計劃的重複使用](../../relational-databases/query-processing-architecture-guide.md#PlanReuse)。
- 如果您的工作負載包含許多單次使用隨選批次與不同查詢計劃，請使用[針對隨選工作負載最佳化](../../database-engine/configure-windows/optimize-for-ad-hoc-workloads-server-configuration-option.md)選項。
  - 比較不同 query_hash 值的數目與 sys.query_store_query 中項目總數。 如果比例接近 1，您的隨選工作負載會產生不同查詢。
- 如果不同的查詢計劃數目不大，請針對資料庫或查詢子集套用[強制參數化](../../relational-databases/query-processing-architecture-guide.md#ForcedParam)。
  - 使用[計劃指南](../../relational-databases/performance/specify-query-parameterization-behavior-by-using-plan-guides.md)，僅針對選取的查詢強制參數化。
  - 如果您的工作負載中有少數不同查詢計劃，請使用[參數化資料庫選項](../../relational-databases/databases/database-properties-options-page.md#miscellaneous)命令來設定強制參數化。 例如，當不同 query_hash 計數和 sys.query_store_query 中項目總數之間比例遠小於 1 的情況。
- 將 QUERY_CAPTURE_MODE 設定為 AUTO，以自動篩選掉資源耗用量少的隨選查詢。

## <a name="avoid-a-drop-and-create-pattern-for-containing-objects"></a><a name="Drop"></a> 避免對包含物件使用 DROP 和 CREATE 模式

查詢存放區會將查詢項目與包含物件 (例如預存程序、函式和觸發程序) 建立關聯。 當您重新建立一個包含物件時，系統會針對相同查詢文字產生新的查詢項目。 這會讓您無法追蹤該查詢一段時間的效能統計資料，也無法使用計劃強制機制。 若要避免這種情況，請盡量使用 `ALTER <object>` 程序變更包含物件的定義。

## <a name="check-the-status-of-forced-plans-regularly"></a><a name="CheckForced"></a> 定期檢查強制計劃的狀態

強制執行計畫是一個針對重要查詢修正效能的方便的機制，並讓查詢變得更容易預測。 然而，因為有計劃提示與計劃指南，所以並不保證會在未來的執行中使用強制執行計劃。 一般而言，當執行計劃所參考的物件遭改變或卸除，導致資料庫結構描述跟著變更時，強制執行計劃就會開始失敗。 在此情況下，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會退而重新編譯查詢，而實際的強制執行失敗原因會顯示在 [sys.query_store_plan](../../relational-databases/system-catalog-views/sys-query-store-plan-transact-sql.md) 中。 下列查詢會傳回強制執行計畫的相關資訊 ︰

```sql
USE [QueryStoreDB];
GO

SELECT p.plan_id, p.query_id, q.object_id as containing_object_id,
    force_failure_count, last_force_failure_reason_desc
FROM sys.query_store_plan AS p
JOIN sys.query_store_query AS q on p.query_id = q.query_id
WHERE is_forced_plan = 1;
```

如需完整的原因清單，請參閱 [sys.query_store_plan](../../relational-databases/system-catalog-views/sys-query-store-plan-transact-sql.md)。 您也可以使用 **query_store_plan_forcing_failed** XEvent 追蹤強制執行計畫失敗及針對此問題進行疑難排解。

## <a name="avoid-renaming-databases-for-queries-with-forced-plans"></a><a name="Renaming"></a> 如果您有強制計劃的查詢，請避免重新命名資料庫

執行計劃會使用三部分名稱 (如 `database.schema.object`) 來參考物件。

如果您重新命名資料庫，強制執行計劃即會失敗，而導致重新編譯所有後續查詢執行。

## <a name="using-query-store-in-mission-critical-servers"></a><a name="Recovery"></a> 在任務關鍵性伺服器中使用查詢存放區

您可以使用查詢存放區和全域追蹤旗標 7745 與 7752 來提升資料庫可用性。 如需詳細資訊，請參閱[追蹤旗標](../../t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql.md)。

- 追蹤旗標 7745 會防止下列預設行為：在查詢存放區將資料寫入磁碟之後，才能關閉 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]。 這表示已收集但尚未保存到磁碟的查詢存放區資料將會遺失 (取決於 `DATA_FLUSH_INTERVAL_SECONDS` 所定義的時間範圍)。
- 追蹤旗標 7752 提供非同步載入查詢存放區的功能。 這可讓資料庫成為上線狀態，並在查詢存放區完全復原之前執行查詢。 預設行為是執行查詢存放區的同步載入。 這個預設行為使得查詢一定會在查詢存放區復原之後執行，但同時也防止資料收集過程遺漏任何查詢。

> [!NOTE]
> 從 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 開始，此行為即由引擎控制，而追蹤旗標 7752 沒有任何作用。

> [!IMPORTANT]
> 若您要將查詢存放區用於 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 中的 Just-In-Time 工作負載深入解析，請盡快擬定計劃，安裝 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] SP2 CU2 ([KB 4340759](https://support.microsoft.com/help/4340759)) 中的效能延展性改進功能。 若沒有這些改進功能，當資料庫承受繁重的工作負載時，可能會發生執行緒同步鎖定競爭，伺服器效能可能會變慢。 尤其是，您可能會在 `QUERY_STORE_ASYNC_PERSIST` 執行緒同步鎖定或 `SPL_QUERY_STORE_STATS_COOKIE_CACHE` 執行緒同步鎖定時發現嚴重的爭用。 套用此改進功能後，查詢存放區將不會再造成執行緒同步鎖定競爭。

> [!IMPORTANT]
> 若要將查詢存放區用於 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 至 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)]) 中的 Just-In-Time 工作負載見解，請盡快規劃安裝 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] SP2 CU15、[!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] CU22，以及 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] CU8 中的效能可擴縮性改善功能。 若沒有這項改進功能，當資料庫承受繁重的臨機操作工作負載時，查詢存放區可能會使用大量的記憶體，而伺服器效能可能會變慢。 套用這項改進功能後，查詢存放區將會對其各種元件可使用的記憶體數量施加內部限制，並且可在 [!INCLUDE[ssde_md](../../includes/ssde_md.md)] 重獲足夠的記憶體之前，自動將作業模式變更為唯讀。 請注意，查詢存放區內部記憶體限制有可能變更，因此並未載於文件中。  

## <a name="see-also"></a>另請參閱

- [ALTER DATABASE SET 選項 &#40;Transact-SQL&#41;](../../t-sql/statements/alter-database-transact-sql-set-options.md)
- [查詢存放區目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/query-store-catalog-views-transact-sql.md)
- [查詢存放區預存程序 &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/query-store-stored-procedures-transact-sql.md)
- [使用含有記憶體內部 OLTP 的查詢存放區](../../relational-databases/performance/using-the-query-store-with-in-memory-oltp.md)
- [使用查詢存放區監視效能](../../relational-databases/performance/monitoring-performance-by-using-the-query-store.md)
- [查詢處理架構指南](../../relational-databases/query-processing-architecture-guide.md)