---
title: 使用查詢存放區監視效能 | Microsoft Docs
description: SQL Server 查詢存放區可提供關於查詢計畫選擇及效能的見解。 查詢存放區會擷取查詢、計畫和執行階段統計資料的歷程記錄。
ms.custom: ''
ms.date: 04/09/2020
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: performance
ms.topic: conceptual
helpviewer_keywords:
- Query Store
- Query Store, described
ms.assetid: e06344a4-22a5-4c67-b6c6-a7060deb5de6
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current||=azure-sqldw-latest
ms.openlocfilehash: e61f723ddbc3010cc705c076675b731482fdee6b
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98171840"
---
# <a name="monitoring-performance-by-using-the-query-store"></a>使用查詢存放區監視效能

[!INCLUDE [SQL Server ASDB, ASDBMI, ASDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa.md)]

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 查詢存放區功能為您提供關於查詢計劃選擇及效能的深入資訊。 其可協助您您快速找出由於查詢計劃變更所導致的效能差異，以簡化效能疑難排解作業。 查詢存放區會自動擷取查詢、計劃和執行階段統計資料的歷程記錄，並將其保留供您檢閱。 其會以時段來區分資料、供您查看資料庫使用模式，並了解何時在伺服器上發生查詢計劃變更。 使用 [[ALTER DATABASE SET](../../t-sql/statements/alter-database-transact-sql-set-options.md)] 選項可設定查詢存放區。

如需操作 Azure [!INCLUDE[ssSDS](../../includes/sssds-md.md)] 中查詢存放區的資訊，請參閱[操作 Azure SQL Database 中的查詢存放區](best-practice-with-the-query-store.md#Insight)。

> [!IMPORTANT]
> 若您只針對 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 中的 Just-In-Time 負載見解使用查詢存放區，請計畫儘快安裝 [KB 4340759](https://support.microsoft.com/help/4340759) 中的效能延展性修正程式。

## <a name="enabling-the-query-store"></a><a name="Enabling"></a> 啟用查詢存放區

 新的 SQL Server 與 Azure Synapse Analytics 資料庫預設不會啟用查詢存放區，但新的 Azure SQL Database 資料庫預設則會啟用此功能。

### <a name="use-the-query-store-page-in-ssmanstudiofull"></a>使用 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 中的 [查詢存放區] 頁面

1. 在 [物件總管] 中，以滑鼠右鍵按一下資料庫，然後按一下 [屬性] 。

   > [!NOTE]
   > 至少需要 [!INCLUDE[ssManStudio](../../includes/ssmanstudio-md.md)] 16 版。

2. 在 [資料庫屬性]  對話方塊中，選取 [查詢存放區]  頁面。

3. 在 [作業模式 (要求)]  方塊中，選取 [讀取寫入] 。

### <a name="use-transact-sql-statements"></a>使用 Transact-SQL 陳述式

使用 **ALTER DATABASE** 陳述式可啟用指定資料庫的查詢存放區。 例如：

```sql
SET QUERY_STORE = ON (OPERATION_MODE = READ_WRITE);
```

如需和查詢存放區相關的更多語法選項，請參閱 [ALTER DATABASE SET 選項 &#40;Transact-SQL&#41;](../../t-sql/statements/alter-database-transact-sql-set-options.md)。

> [!NOTE]
> 您無法為 **master** 或 **tempdb** 資料庫啟用查詢存放區。

> [!IMPORTANT]
> 如需有關啟用查詢存放區並讓它根據您的工作負載調整的相關資訊，請參閱[使用查詢存放區的最佳作法](../../relational-databases/performance/best-practice-with-the-query-store.md#Configure).

## <a name="information-in-the-query-store"></a><a name="About"></a> 查詢存放區中的資訊

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中任何特定查詢的執行計劃通常會在一段時間後，因為統計資料的變更、結構描述變更、建立/刪除索引等數種不同原因而有所演變。儲存快取的查詢計劃之程序快取，只會儲存最新的執行計劃。 計劃也會因為記憶體不足的壓力，而從計劃快取中收回。 因此，因為執行計劃變更所造成的查詢效能低下，可能相形重要，而且可能需要許多時間才可解決。

因為查詢存放區會為每項查詢保留多個執行計劃，其可強制套用原則以指示查詢處理器要為查詢使用特定的執行計劃。 這也稱為強制執行計劃。 查詢存放區中的強制執行計劃，透過類似於 [USE PLAN](../../t-sql/queries/hints-transact-sql-query.md) 查詢提示的機制加以提供，但它不需要在使用者應用程式中進行任何變更。 強制執行計劃可以解決在非常短的期間內，因計劃變更所導致的查詢效能低下。

> [!NOTE]
> 查詢存放區會收集 DML 陳述式 (例如 SELECT、INSERT、UPDATE、DELETE、MERGE 與 BULK INSERT) 的計畫。
>
> 查詢存放區根據預設不會收集原生編譯預存程序的資料。 請使用 [sys.sp_xtp_control_query_exec_stats](../../relational-databases/system-stored-procedures/sys-sp-xtp-control-query-exec-stats-transact-sql.md) 來啟用收集原生編譯預存程序的資料。

**等候統計資料** 是另一種可協助您針對 [!INCLUDE[ssde_md](../../includes/ssde_md.md)] 效能進行疑難排解的來源資訊。 等候統計資料長久以來只能在執行個體層級取得，難以回溯至特定查詢。 從 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 開始，查詢存放區會包含追蹤等候統計資料的維度。下列範例會啟用查詢存放區來收集等候統計資料。

```sql
SET QUERY_STORE = ON ( WAIT_STATS_CAPTURE_MODE = ON );
```

使用查詢存放區功能的常見情況包括：

- 強制執行先前的查詢計劃，快速找出並修正計劃效能低下。 修正因為執行計劃變更而最近出現的效能低下。
- 判斷在指定的時段執行查詢的次數、協助 DBA 疑難排解資源的效能問題。
- 識別過去 *x* 小時內的前 *n* 項查詢 (依據執行時間、記憶體耗用量等等)。
- 稽核指定的查詢之查詢計劃記錄。
- 分析特定資料庫的資源 (CPU、I/O 及記憶體) 使用模式。
- 識別前 n 項等候資源的查詢。
- 了解特定查詢或計劃的等候本質。

查詢存放區包含三個存放區：

- **計劃存放區** 以保存執行計劃資訊。
- **執行階段統計資料存放區** 以保存執行統計資料資訊。
- **等候統計資料存放區** 以保存等候統計資料資訊。

計劃存放區中可為查詢儲存的不重複計劃數目，受限於 **max_plans_per_query** 組態選項。 為了增強效能，資訊會以非同步方式寫入存放區。 若要將空間使用量降至最低，在執行階段統計資料存放區中的執行階段執行統計資料，會以固定的時段彙總。 對查詢存放區目錄檢視進行查詢時，會顯示這些存放區中的資訊。

下列查詢會傳回查詢存放區中查詢與計劃的相關資訊。

```sql
SELECT Txt.query_text_id, Txt.query_sql_text, Pl.plan_id, Qry.*
FROM sys.query_store_plan AS Pl
INNER JOIN sys.query_store_query AS Qry
    ON Pl.query_id = Qry.query_id
INNER JOIN sys.query_store_query_text AS Txt
    ON Qry.query_text_id = Txt.query_text_id ;
```

## <a name="use-the-regressed-queries-feature"></a><a name="Regressed"></a> 使用迴歸查詢功能

啟用查詢存放區之後，請重新整理 [物件總管] 窗格中的資料庫部分，以新增查詢存放區 區段。

![SSMS 物件總管中的 SQL Server 2016 查詢存放區樹狀結構](../../relational-databases/performance/media/objectexplorerquerystore.PNG "SSMS 物件總管中的 SQL Server 2016 查詢存放區樹狀結構") ![SSMS 物件總管中的 SQL Server 2017 查詢存放區樹狀結構](../../relational-databases/performance/media/objectexplorerquerystore_sql17.PNG "SSMS 物件總管中的 SQL Server 2017 查詢存放區樹狀結構")

選取 [迴歸查詢]  ，開啟 **中的 [迴歸查詢]** [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)]窗格。 [迴歸查詢] 窗格會顯示查詢存放區中的查詢與計劃。 頂端的下拉式方塊，可供依據各種準則來篩選查詢：**持續時間 (毫秒)** (預設)、CPU 時間 (毫秒)、邏輯讀取 (KB)、邏輯寫入 (KB)、實體讀取 (KB)、CLR 時間 (毫秒)、DOP、記憶體耗用量 (KB)、資料列計數、已使用的記錄記憶體 (KB)、已使用的暫存 DB 記憶體 (KB)，以及等候時間 (毫秒)。

選取計劃即可以圖形方式檢視查詢計劃。 按鈕可用來檢視來源查詢、強制執行及取消強制執行查詢計畫、在格線和圖表格式之間切換、比較所選取的計畫 (如果選取了多個)，以及重新整理顯示。

![SSMS 物件總管中的 SQL Server 2016 迴歸查詢](../../relational-databases/performance/media/objectexplorerregressedqueries.PNG "SSMS 物件總管中的 SQL Server 2016 迴歸查詢")

若要強制執行計劃，請選取查詢與計劃，然後按一下 [強制執行計劃] 。 您只可以強制執行由查詢計劃功能所儲存且仍保留在查詢計劃快取中的計劃。

## <a name="finding-waiting-queries"></a><a name="Waiting"></a> 尋找等候查詢

從 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 開始，可在查詢存放區中使用每個查詢經過一段時間的等候統計資料。

在查詢存放區中，等候類型會合併到 **等候類別**。 [sys.query_store_wait_stats & #40;TRANSACT-SQL & #41;](../../relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql.md#wait-categories-mapping-table) 可將等候類別對應至等候類型。

選取 [查詢等候統計資料]，以在 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] v18 或更新版本中開啟 [查詢等候統計資料] 窗格。 [查詢等候統計資料] 窗格會在查詢存放區中顯示包含前幾個等候類別的長條圖。 使用頂端的下拉式清單來選取等候時間彙總準則：平均值、最大值、最小值、標準差及 **總計** (預設)。

![SSMS 物件總管中的 SQL Server 2017 查詢等候統計資料](../../relational-databases/performance/media/query-store-waits.PNG "SSMS 物件總管中的 SQL Server 2017 查詢等候統計資料")

按一下長條圖來選取等候類別，隨即顯示有關所選取等候類別的詳細資料檢視。 這個新的長條圖包含提供給該等候類別的查詢。

![SSMS 物件總管中的 SQL Server 2017 查詢等候統計資料詳細檢視](../../relational-databases/performance/media/query-store-waits-detail.PNG "SSMS 物件總管中的 SQL Server 2017 查詢等候統計資料詳細檢視")

使用頂端的下拉式清單方塊，根據所選取等候類別的各種等候時間準則來篩選查詢：平均值、最大值、最小值、標準差及 **總計** (預設)。 選取計劃即可以圖形方式檢視查詢計劃。 提供有按鈕可供檢視來源查詢、強制執行或取消強制執行查詢計劃，以及重新整理顯示畫面。

**等候類別** 會將不同的等候類型合併到本質類似的貯體中。 不同的等候類別需要不同的後續操作分析來解決問題，但同類別的等候類型會導致非常類似的疑難排解體驗，而提供受影響的前幾項查詢可能就是順利完成大部分這類調查所缺少的片段。

以下範例示範如何在查詢存放區引入等候類別之前及之後深入了解您的工作負載：

|過去的體驗|新的體驗|動作|
|-|-|-|
|每個資料庫的高 RESOURCE_SEMAPHORE 等候|查詢存放區特定查詢的高記憶體等候|找出查詢存放區中記憶體耗用量名列前茅的查詢。 這些查詢可能會進一步延遲受影響查詢的進度。 請考慮對這些查詢或受影響的查詢使用 MAX_GRANT_PERCENT 查詢提示。|
|每個資料庫的高 LCK_M_X 等候|查詢存放區特定查詢的高鎖定等候|查看受影響查詢的查詢文字，並找出目標實體。 在查詢存放區中尋找修改相同項目的其他查詢，這些查詢經常執行且/或持續時間很長。 找出這些查詢之後，請考慮變更應用程式邏輯，改善並行存取，或使用限制較少的隔離等級。|
|每個資料庫的高 PAGEIOLATCH_SH 等候|查詢存放區特定查詢的高緩衝區 IO 等候|在查詢存放區中尋找實體讀取次數高的查詢。 如果它們符合高 IO 等候的查詢，請考慮引入基礎實體索引搜尋，以執行搜尋而不是掃描，進而將查詢的 IO 負擔降至最低。|
|每個資料庫的高 SOS_SCHEDULER_YIELD 等候|查詢存放區特定查詢的高 CPU 等候|尋找查詢存放區中前幾項最耗 CPU 的查詢。 在它們中間找出高 CPU 趨勢與受影響查詢之高 CPU 等候相互關聯的查詢。 專注於將那些查詢最佳化，可能存在計畫迴歸或缺少索引。|

## <a name="configuration-options"></a><a name="Options"></a> 組態選項

如需設定查詢存放區參數的可用選項，請參閱 [ALTER DATABASE SET 選項 (Transact-SQL)](../../t-sql/statements/alter-database-transact-sql-set-options.md#query-store)。

查詢 **sys.database_query_store_options** 檢視以判斷查詢存放區的目前選項。 如需值的詳細資訊，請參閱 [sys.database_query_store_options](../../relational-databases/system-catalog-views/sys-database-query-store-options-transact-sql.md)。

如需使用 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式設定組態選項的範例，請參閱[選項管理](#OptionMgmt)。

## <a name="related-views-functions-and-procedures"></a><a name="Related"></a> 相關檢視、函數與程序

透過 [!INCLUDE[ssManStudio](../../includes/ssmanstudio-md.md)] 或使用下列檢視與程序來檢視及管理查詢存放區。

### <a name="query-store-functions"></a>查詢存放區函式

此函式可協助執行查詢存放區作業。

:::row:::
    :::column:::
        [sys.fn_stmt_sql_handle_from_sql_stmt &#40;Transact-SQL&#41;](../../relational-databases/system-functions/sys-fn-stmt-sql-handle-from-sql-stmt-transact-sql.md)
    :::column-end:::
:::row-end:::

### <a name="query-store-catalog-views"></a>查詢存放區目錄檢視

目錄檢視會提供查詢存放區的相關資訊。

:::row:::
    :::column:::
        [sys.database_query_store_options &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-database-query-store-options-transact-sql.md)
    :::column-end:::
    :::column:::
        [sys.query_context_settings &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-query-context-settings-transact-sql.md)
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [sys.query_store_plan &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-query-store-plan-transact-sql.md)
    :::column-end:::
    :::column:::
        [sys.query_store_query &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-query-store-query-transact-sql.md)
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [sys.query_store_query_text &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-query-store-query-text-transact-sql.md)
    :::column-end:::
    :::column:::
        [sys.query_store_runtime_stats &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql.md)
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [sys.query_store_wait_stats &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql.md)
    :::column-end:::
    :::column:::
        [sys.query_store_runtime_stats_interval &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-query-store-runtime-stats-interval-transact-sql.md)
    :::column-end:::
:::row-end:::


### <a name="query-store-stored-procedures"></a>查詢存放區預存程序

預存程序可設定查詢存放區。

:::row:::
    :::column:::
        [sp_query_store_flush_db &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-query-store-flush-db-transact-sql.md)
    :::column-end:::
    :::column:::
        [sp_query_store_reset_exec_stats &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-query-store-reset-exec-stats-transact-sql.md)
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [sp_query_store_force_plan &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-query-store-force-plan-transact-sql.md)
    :::column-end:::
    :::column:::
        [sp_query_store_unforce_plan &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-query-store-unforce-plan-transact-sql.md)
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [sp_query_store_remove_plan &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-query-store-remove-plan-transct-sql.md)
    :::column-end:::
    :::column:::
        [sp_query_store_remove_query &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-query-store-remove-query-transact-sql.md)
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        sp_query_store_consistency_check &#40;Transact-SQL&#41;<sup>1</sup>
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

<sup>1</sup> 在極端的案例中，查詢存放區可能會因為內部錯誤而進入錯誤狀態。 從 SQL Server 2017 (14.x) 開始，您可以透過在受影響的資料庫中執行 sp_query_store_consistency_check 預存程序來復原查詢存放區。 如需 actual_state_desc 資料行描述中所述的詳細資訊，請參閱 [sys.database_query_store_options](../../relational-databases/system-catalog-views/sys-database-query-store-options-transact-sql.md)。

## <a name="key-usage-scenarios"></a><a name="Scenarios"></a> 主要使用方式案例

### <a name="option-management"></a><a name="OptionMgmt"></a> 選項管理

本節提供管理查詢存放區功能本身的一些指導方針。

**查詢存放區目前為作用中？**

查詢存放區會將其資料儲存在使用者資料庫中，這也就是為什麼其有大小限制 (以 `MAX_STORAGE_SIZE_MB` 設定)。 若查詢存放區中的資料達到上限，查詢存放區會自動從讀寫狀態變更為唯讀，並會停止收集新的資料。

查詢 [sys.database_query_store_options](../../relational-databases/system-catalog-views/sys-database-query-store-options-transact-sql.md) 可判斷查詢存放區目前是否在作用中，以及其目前是否正在收集執行階段統計資料。

```sql
SELECT actual_state, actual_state_desc, readonly_reason,
    current_storage_size_mb, max_storage_size_mb
FROM sys.database_query_store_options;
```

查詢存放區的狀態取決於 actual_state 資料行。 若與想要的狀態不同，`readonly_reason` 資料行可以提供更多的資訊。
當查詢存放區的大小超過配額時，此功能會切換為 read_only 模式。

**取得查詢存放區選項**

若要找出查詢存放區狀態的詳細資訊，請於使用者資料庫中執行下列作業。

```sql
SELECT * FROM sys.database_query_store_options;
```

**設定查詢存放區間隔**

您可以覆寫彙總查詢執行階段統計資料的間隔 (預設為 60 分鐘)。

```sql
ALTER DATABASE <database_name>
SET QUERY_STORE (INTERVAL_LENGTH_MINUTES = 15);
```

> [!NOTE]
> `INTERVAL_LENGTH_MINUTES` 不允許任意值。 您可以使用下列其中一項：1、5、10、15、30、 60 或 1440 分鐘。

新的間隔值是透過 **sys.database_query_store_options** 檢視而公開。

**查詢存放區空間使用量**

若要檢查目前的查詢存放區的大小和限制，請在使用者資料庫中執行下列陳述式。

```sql
SELECT current_storage_size_mb, max_storage_size_mb
FROM sys.database_query_store_options;
```

如果查詢存放區的儲存體已滿，請使用下列陳述式來擴充該儲存體。

```sql
ALTER DATABASE <database_name>
SET QUERY_STORE (MAX_STORAGE_SIZE_MB = <new_size>);
```

**設定查詢存放區選項**

使用單一 ALTER DATABASE 陳述式即可一次設定多個查詢存放區選項。

```sql
ALTER DATABASE <database name>
SET QUERY_STORE (
    OPERATION_MODE = READ_WRITE,
    CLEANUP_POLICY = (STALE_QUERY_THRESHOLD_DAYS = 30),
    DATA_FLUSH_INTERVAL_SECONDS = 3000,
    MAX_STORAGE_SIZE_MB = 500,
    INTERVAL_LENGTH_MINUTES = 15,
    SIZE_BASED_CLEANUP_MODE = AUTO,
    QUERY_CAPTURE_MODE = AUTO,
    MAX_PLANS_PER_QUERY = 1000,
    WAIT_STATS_CAPTURE_MODE = ON
);
```

如需組態選項的完整清單，請參閱 [ALTER DATABASE SET 選項 (Transact-SQL)](../../t-sql/statements/alter-database-transact-sql-set-options.md)。

**清理空間**

查詢存放區內部資料表於建立資料庫時建立在 PRIMARY 檔案群組中，且該組態之後無法變更。 如果您快要用完了空間，可能會想要使用下列陳述式，來清除舊的查詢存放區資料。

```sql
ALTER DATABASE <db_name> SET QUERY_STORE CLEAR;
```

或者，您也可能只想要清除特定查詢資料，因為它對於查詢最佳化和計劃分析來說較不相關，但也佔用一樣的空間。

**刪除臨機操作查詢**

這會每隔 3 分鐘清除查詢存放區中的臨機操作和內部查詢，讓查詢存放區不會用盡空間，並移除真正需要追蹤的查詢

```sql
SET NOCOUNT ON
-- This purges adhoc and internal queries from the query store every 3 minutes so that the
-- query store does not run out of space and remove queries we really need to track
DECLARE @command varchar(1000)

SELECT @command = 'IF ''?'' NOT IN(''master'', ''model'', ''msdb'', ''tempdb'') BEGIN USE ?
EXEC(''
DECLARE @id int
DECLARE adhoc_queries_cursor CURSOR
FOR
SELECT q.query_id
FROM sys.query_store_query_text AS qt
JOIN sys.query_store_query AS q
ON q.query_text_id = qt.query_text_id
JOIN sys.query_store_plan AS p
ON p.query_id = q.query_id
JOIN sys.query_store_runtime_stats AS rs
ON rs.plan_id = p.plan_id
WHERE q.is_internal_query = 1 ' -- is it an internal query then we dont care to keep track of it

' OR q.object_id = 0' -- if it does not have a valid object_id then it is an adhoc query and we dont care about keeping track of it
' GROUP BY q.query_id
HAVING MAX(rs.last_execution_time) < DATEADD (minute, -5, GETUTCDATE()) ' -- if it has been more than 5 minutes since the adhoc query ran
' ORDER BY q.query_id ;
OPEN adhoc_queries_cursor ;
FETCH NEXT FROM adhoc_queries_cursor INTO @id;
WHILE @@fetch_status = 0
BEGIN
EXEC sp_query_store_remove_query @id
FETCH NEXT FROM adhoc_queries_cursor INTO @id
END
CLOSE adhoc_queries_cursor ;
DEALLOCATE adhoc_queries_cursor;
'') END' ;
EXEC sp_MSforeachdb @command
```

您可以用不同的邏輯來定義自己的程序，以清除不再需要的資料。

以上範例使用 **sp_query_store_remove_query** 擴充預存程序，以移除不必要的資料。 您也可以使用：

- **sp_query_store_reset_exec_stats** 為指定的計畫清除執行階段統計資料。
- **sp_query_store_remove_plan** 以移除單一計畫。

### <a name="performance-auditing-and-troubleshooting"></a><a name="Peformance"></a> 效能稽核及疑難排解

查詢存放區會保留整個查詢執行過程當中的編譯和執行階段度量歷程記錄，以讓您詢問關於工作負載的問題。

**前 *n* 個在資料庫上執行的查詢？**

```sql
SELECT TOP 10 qt.query_sql_text, q.query_id,
    qt.query_text_id, p.plan_id, rs.last_execution_time
FROM sys.query_store_query_text AS qt
JOIN sys.query_store_query AS q
    ON qt.query_text_id = q.query_text_id
JOIN sys.query_store_plan AS p
    ON q.query_id = p.query_id
JOIN sys.query_store_runtime_stats AS rs
    ON p.plan_id = rs.plan_id
ORDER BY rs.last_execution_time DESC;
```

**每項查詢的執行次數？**

```sql
SELECT q.query_id, qt.query_text_id, qt.query_sql_text,
    SUM(rs.count_executions) AS total_execution_count
FROM sys.query_store_query_text AS qt
JOIN sys.query_store_query AS q
    ON qt.query_text_id = q.query_text_id
JOIN sys.query_store_plan AS p
    ON q.query_id = p.query_id
JOIN sys.query_store_runtime_stats AS rs
    ON p.plan_id = rs.plan_id
GROUP BY q.query_id, qt.query_text_id, qt.query_sql_text
ORDER BY total_execution_count DESC;
```

**前一個小時內，平均執行時間最長的查詢數目？**

```sql
SELECT TOP 10 rs.avg_duration, qt.query_sql_text, q.query_id,
    qt.query_text_id, p.plan_id, GETUTCDATE() AS CurrentUTCTime,
    rs.last_execution_time
FROM sys.query_store_query_text AS qt
JOIN sys.query_store_query AS q
    ON qt.query_text_id = q.query_text_id
JOIN sys.query_store_plan AS p
    ON q.query_id = p.query_id
JOIN sys.query_store_runtime_stats AS rs
    ON p.plan_id = rs.plan_id
WHERE rs.last_execution_time > DATEADD(hour, -1, GETUTCDATE())
ORDER BY rs.avg_duration DESC;
```

**過去 24 小時內，有相對應的平均資料列計數與執行計數，且具有最大平均實體 I/O 讀取的查詢數目？**

```sql
SELECT TOP 10 rs.avg_physical_io_reads, qt.query_sql_text,
    q.query_id, qt.query_text_id, p.plan_id, rs.runtime_stats_id,
    rsi.start_time, rsi.end_time, rs.avg_rowcount, rs.count_executions
FROM sys.query_store_query_text AS qt
JOIN sys.query_store_query AS q
    ON qt.query_text_id = q.query_text_id
JOIN sys.query_store_plan AS p
    ON q.query_id = p.query_id
JOIN sys.query_store_runtime_stats AS rs
    ON p.plan_id = rs.plan_id
JOIN sys.query_store_runtime_stats_interval AS rsi
    ON rsi.runtime_stats_interval_id = rs.runtime_stats_interval_id
WHERE rsi.start_time >= DATEADD(hour, -24, GETUTCDATE())
ORDER BY rs.avg_physical_io_reads DESC;
```

**具有多項計畫的查詢？** 這些查詢特別有趣的原因是，它們都是因為計劃選擇變更而導致低下的對象。 下列查詢能找出這些查詢以及所有計劃：

```sql
WITH Query_MultPlans
AS
(
SELECT COUNT(*) AS cnt, q.query_id
FROM sys.query_store_query_text AS qt
JOIN sys.query_store_query AS q
    ON qt.query_text_id = q.query_text_id
JOIN sys.query_store_plan AS p
    ON p.query_id = q.query_id
GROUP BY q.query_id
HAVING COUNT(distinct plan_id) > 1
)

SELECT q.query_id, object_name(object_id) AS ContainingObject,
    query_sql_text, plan_id, p.query_plan AS plan_xml,
    p.last_compile_start_time, p.last_execution_time
FROM Query_MultPlans AS qm
JOIN sys.query_store_query AS q
    ON qm.query_id = q.query_id
JOIN sys.query_store_plan AS p
    ON q.query_id = p.query_id
JOIN sys.query_store_query_text qt
    ON qt.query_text_id = q.query_text_id
ORDER BY query_id, plan_id;
```

 **最近效能低下的查詢 (與時間中的不同點相較)？** 下列查詢範例會傳回所有過去 48 小時內，因為計劃選擇變更而導致執行時間為兩倍的查詢。 查詢會並列比較所有執行階段。

```sql
SELECT
    qt.query_sql_text,
    q.query_id,
    qt.query_text_id,
    rs1.runtime_stats_id AS runtime_stats_id_1,
    rsi1.start_time AS interval_1,
    p1.plan_id AS plan_1,
    rs1.avg_duration AS avg_duration_1,
    rs2.avg_duration AS avg_duration_2,
    p2.plan_id AS plan_2,
    rsi2.start_time AS interval_2,
    rs2.runtime_stats_id AS runtime_stats_id_2
FROM sys.query_store_query_text AS qt
JOIN sys.query_store_query AS q
    ON qt.query_text_id = q.query_text_id
JOIN sys.query_store_plan AS p1
    ON q.query_id = p1.query_id
JOIN sys.query_store_runtime_stats AS rs1
    ON p1.plan_id = rs1.plan_id
JOIN sys.query_store_runtime_stats_interval AS rsi1
    ON rsi1.runtime_stats_interval_id = rs1.runtime_stats_interval_id
JOIN sys.query_store_plan AS p2
    ON q.query_id = p2.query_id
JOIN sys.query_store_runtime_stats AS rs2
    ON p2.plan_id = rs2.plan_id
JOIN sys.query_store_runtime_stats_interval AS rsi2
    ON rsi2.runtime_stats_interval_id = rs2.runtime_stats_interval_id
WHERE rsi1.start_time > DATEADD(hour, -48, GETUTCDATE())
    AND rsi2.start_time > rsi1.start_time
    AND p1.plan_id <> p2.plan_id
    AND rs2.avg_duration > 2*rs1.avg_duration
ORDER BY q.query_id, rsi1.start_time, rsi2.start_time;
```

如果您想要查看所有低下的效能 (不只因方案選擇變更的相關項目)，只要從上一個查詢移除條件 `AND p1.plan_id <> p2.plan_id` 即可。

**等候最久的查詢？**
此查詢會傳回等候最久的前 10 項查詢。

```sql
SELECT TOP 10
    qt.query_text_id,
    q.query_id,
    p.plan_id,
    sum(total_query_wait_time_ms) AS sum_total_wait_ms
FROM sys.query_store_wait_stats ws
JOIN sys.query_store_plan p ON ws.plan_id = p.plan_id
JOIN sys.query_store_query q ON p.query_id = q.query_id
JOIN sys.query_store_query_text qt ON q.query_text_id = qt.query_text_id
GROUP BY qt.query_text_id, q.query_id, p.plan_id
ORDER BY sum_total_wait_ms DESC
```

**最近效能低下的查詢 (比較最近的執行與記錄的執行)？** 下一個查詢會依據執行時段，比較查詢的執行。 在此特別的範例中，查詢會比較最近期間內 (1 小時) 與歷程記錄期間 (前一天) 的執行，並找出因 `additional_duration_workload`所引發的項目。 此度量會計算最近的平均執行與記錄的平均執行之間的差，乘以最近執行的數目。 它實際上代表與記錄相較，最近的執行引發了多少額外的時間：

```sql
--- "Recent" workload - last 1 hour
DECLARE @recent_start_time datetimeoffset;
DECLARE @recent_end_time datetimeoffset;
SET @recent_start_time = DATEADD(hour, -1, SYSUTCDATETIME());
SET @recent_end_time = SYSUTCDATETIME();

--- "History" workload
DECLARE @history_start_time datetimeoffset;
DECLARE @history_end_time datetimeoffset;
SET @history_start_time = DATEADD(hour, -24, SYSUTCDATETIME());
SET @history_end_time = SYSUTCDATETIME();

WITH
hist AS
(
    SELECT
        p.query_id query_id,
        ROUND(ROUND(CONVERT(FLOAT, SUM(rs.avg_duration * rs.count_executions)) * 0.001, 2), 2) AS total_duration,
        SUM(rs.count_executions) AS count_executions,
        COUNT(distinct p.plan_id) AS num_plans
     FROM sys.query_store_runtime_stats AS rs
        JOIN sys.query_store_plan AS p ON p.plan_id = rs.plan_id
    WHERE (rs.first_execution_time >= @history_start_time
               AND rs.last_execution_time < @history_end_time)
        OR (rs.first_execution_time <= @history_start_time
               AND rs.last_execution_time > @history_start_time)
        OR (rs.first_execution_time <= @history_end_time
               AND rs.last_execution_time > @history_end_time)
    GROUP BY p.query_id
),
recent AS
(
    SELECT
        p.query_id query_id,
        ROUND(ROUND(CONVERT(FLOAT, SUM(rs.avg_duration * rs.count_executions)) * 0.001, 2), 2) AS total_duration,
        SUM(rs.count_executions) AS count_executions,
        COUNT(distinct p.plan_id) AS num_plans
    FROM sys.query_store_runtime_stats AS rs
        JOIN sys.query_store_plan AS p ON p.plan_id = rs.plan_id
    WHERE  (rs.first_execution_time >= @recent_start_time
               AND rs.last_execution_time < @recent_end_time)
        OR (rs.first_execution_time <= @recent_start_time
               AND rs.last_execution_time > @recent_start_time)
        OR (rs.first_execution_time <= @recent_end_time
               AND rs.last_execution_time > @recent_end_time)
    GROUP BY p.query_id
)
SELECT
    results.query_id AS query_id,
    results.query_text AS query_text,
    results.additional_duration_workload AS additional_duration_workload,
    results.total_duration_recent AS total_duration_recent,
    results.total_duration_hist AS total_duration_hist,
    ISNULL(results.count_executions_recent, 0) AS count_executions_recent,
    ISNULL(results.count_executions_hist, 0) AS count_executions_hist
FROM
(
    SELECT
        hist.query_id AS query_id,
        qt.query_sql_text AS query_text,
        ROUND(CONVERT(float, recent.total_duration/
                   recent.count_executions-hist.total_duration/hist.count_executions)
               *(recent.count_executions), 2) AS additional_duration_workload,
        ROUND(recent.total_duration, 2) AS total_duration_recent,
        ROUND(hist.total_duration, 2) AS total_duration_hist,
        recent.count_executions AS count_executions_recent,
        hist.count_executions AS count_executions_hist
    FROM hist
        JOIN recent
            ON hist.query_id = recent.query_id
        JOIN sys.query_store_query AS q
            ON q.query_id = hist.query_id
        JOIN sys.query_store_query_text AS qt
            ON q.query_text_id = qt.query_text_id
) AS results
WHERE additional_duration_workload > 0
ORDER BY additional_duration_workload DESC
OPTION (MERGE JOIN);
```

### <a name="maintaining-query-performance-stability"></a><a name="Stability"></a> 維護查詢效能穩定性

針對執行多次的查詢，您可能會注意到 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 使用不同的計畫，而產生了不同的資源使用率與持續時間。 您可利用查詢存放區，輕鬆偵測查詢效能何時低下，以及判斷在意時段中的最佳計劃。 然後可以對未來的查詢強制執行該最佳計劃。

您也可以為具有參數 (自動設定參數或手動設定參數) 的查詢，找出不一致的查詢效能。 您可以在不同的計劃間，找出適合所有或大部分參數值的良好且快速之計劃，並強制執行該計劃，為更多使用者案例留下可預測的效能。

### <a name="force-a-plan-for-a-query-apply-forcing-policy"></a>為查詢強制執行計畫 (套用強制原則)

針對特定查詢強制執行計畫時，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會嘗試在最佳化工具中強制執行該計畫。 如果計劃強制失敗，會引發 XEvent，系統會指示最佳化工具以一般方式最佳化。

```sql
EXEC sp_query_store_force_plan @query_id = 48, @plan_id = 49;
```

使用 **sp_query_store_force_plan** 時，只能強制執行查詢存放區所記錄的計劃，作為該查詢的計劃。 換句話說，可用於查詢的計劃，是已經用於執行該查詢的計劃 (查詢存放區當時在作用中)。

#### <a name="a-namectp23-plan-forcing-support-for-fast-forward-and-static-cursors"></a><a name="ctp23"><a/>強制支援向前快轉及靜態資料指標

從 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 和 Azure SQL Database (所有部署模型) 開始，查詢存放區支援強制查詢執行計劃，以進行向前快轉及提供靜態 [!INCLUDE[tsql](../../includes/tsql-md.md)] 和 API 資料指標。 強制執行會透過 `sp_query_store_force_plan` 或 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 查詢存放區報告支援。

### <a name="remove-plan-forcing-for-a-query"></a>針對查詢移除強制執行計畫

若要再次依賴 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 查詢最佳化工具來計算最佳的查詢計劃，請使用 **sp_query_store_unforce_plan** 以取消為該查詢所選取的強制計劃。

```sql
EXEC sp_query_store_unforce_plan @query_id = 48, @plan_id = 49;
```

## <a name="see-also"></a>另請參閱

- [使用查詢存放區的最佳作法](../../relational-databases/performance/best-practice-with-the-query-store.md)
- [使用含有記憶體內部 OLTP 的查詢存放區](../../relational-databases/performance/using-the-query-store-with-in-memory-oltp.md)
- [查詢存放區使用案例](../../relational-databases/performance/query-store-usage-scenarios.md)
- [查詢存放區如何收集資料](../../relational-databases/performance/how-query-store-collects-data.md)
- [查詢存放區預存程序 &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/query-store-stored-procedures-transact-sql.md)
- [查詢存放區目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/query-store-catalog-views-transact-sql.md)
- [效能的監視與微調](../../relational-databases/performance/monitor-and-tune-for-performance.md)
- [效能監視及微調工具](../../relational-databases/performance/performance-monitoring-and-tuning-tools.md)
- [開啟活動監視器 &#40;SQL Server Management Studio&#41;](../../relational-databases/performance-monitor/open-activity-monitor-sql-server-management-studio.md)
- [即時查詢統計資料](../../relational-databases/performance/live-query-statistics.md)
- [活動監視器](../../relational-databases/performance-monitor/activity-monitor.md)
- [sys.database_query_store_options &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-database-query-store-options-transact-sql.md)
