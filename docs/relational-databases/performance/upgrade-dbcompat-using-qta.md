---
title: 使用查詢調整小幫手來升級資料庫
description: 了解查詢調整小幫手如何引導完成建議的工作流程，以便在升級到較新的 SQL Server 版本期間保持效能穩定性。
ms.custom: seo-dt-2019
ms.date: 02/13/2019
ms.prod: sql
ms.reviewer: wiassaf
ms.technology: performance
ms.topic: conceptual
f1_keywords:
- sql13.swb.querytuning.f1
helpviewer_keywords:
- query statistics [SQL Server] live query stats
- live query statistics
- debugging [SQL Server], live query stats
- statistics [SQL Server], live query statistics
- query profiling
- lightweight query profiling
- lightweight profiling
ms.assetid: 07f8f594-75b4-4591-8c29-d63811e7753e
author: pmasl
ms.author: pelopes
manager: amitban
ms.openlocfilehash: 08e105eac49b8377e128aa1cc746de1eebdee389
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98102211"
---
# <a name="upgrading-databases-by-using-the-query-tuning-assistant"></a>使用查詢調整小幫手來升級資料庫

[!INCLUDE[tsql-appliesto-ss2016-xxxx-xxxx-xxx-md.md](../../includes/tsql-appliesto-ss2016-xxxx-xxxx-xxx-md.md)]

從舊版 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 移轉至 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 或更新版本，並升級到最新的[資料庫相容性層級](../../relational-databases/databases/view-or-change-the-compatibility-level-of-a-database.md)時，工作負載可能會有效能衰退的風險。 在 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 和任何較新版本之間升級時，這種情況的程度也可能較小。

從 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 開始 (以及所有新的版本)，所有的查詢最佳化工具變更都會調控至最新的資料庫相容性層級；因此，執行計劃不會在升級時立即變更，而是在使用者將 `COMPATIBILITY_LEVEL` 資料庫選項變更為最新可用版本時變更。 如需 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 中引入的查詢最佳化工具變更的相關詳細資訊，請參閱[基數估計工具](../../relational-databases/performance/cardinality-estimation-sql-server.md)。 如需相容性層級及其如何影響升級的相關詳細資訊，請參閱[相容性層級與資料庫引擎升級](../../t-sql/statements/alter-database-transact-sql-compatibility-level.md#compatibility-levels-and-database-engine-upgrades)。

如果升級遵循如下所示的建議工作流程，則資料庫相容性層級提供的此調控功能與查詢存放區結合，可讓您在升級過程中充分掌控查詢效能。 如需升級相容性層級之建議工作流程的詳細資訊，請參閱[變更資料庫相容性模式並使用查詢存放區](../../database-engine/install-windows/change-the-database-compatibility-mode-and-use-the-query-store.md)。 

![使用查詢存放區的建議資料庫升級工作流程](../../relational-databases/performance/media/query-store-usage-5.png "使用查詢存放區的建議資料庫升級工作流程") 

[自動調整](../../relational-databases/automatic-tuning/automatic-tuning.md)的引進，進一步改善了 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 對升級的控制，並允許自動執行上述建議的工作流程的最後一個步驟。

從 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] v18 開始，新的 **查詢調整小幫手 (QTA)** 功能將引導使用者完成建議的工作流程，以便在升級到較新的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 版本期間保持效能穩定性，如 [查詢存放區使用案例](../../relational-databases/performance/query-store-usage-scenarios.md#CEUpgrade)的 *在升級至更新版 SQL Server 期間保持效能的穩定性* 一節所述。 但是，QTA 不會像建議工作流程的最後一個步驟中所見，復原至先前已知的良好計畫。 QTA 會改為追蹤任何在 [查詢存放區 **迴歸查詢**](../../relational-databases/performance/monitoring-performance-by-using-the-query-store.md#Regressed)檢視中找到的迴歸，並會逐一查看適用最佳化工具模型變化的可能排列，以產生更好的新計畫。

> [!IMPORTANT]
> QTA 不會產生使用者工作負載。 如果在您應用程式未使用的環境中執行 QTA，請確保您仍然可以透過其他方式在目標 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 上執行代表性的測試工作負載。

## <a name="the-query-tuning-assistant-workflow"></a>查詢調整小幫手工作流程

QTA 的起始點假設將舊版 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的資料庫 (透過 [CREATE DATABASE ...FOR ATTACH](../..//relational-databases/databases/attach-a-database.md) 或 [RESTORE](../../t-sql/statements/restore-statements-transact-sql.md)) 移動到較新版本的 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]，且升級前的資料庫相容性層級不會立即變更。 QTA 將引導完成下列步驟：

1. 根據使用者針對工作負載持續時間 (以天為單位) 設定的建議設定來設定查詢存放區。 請考慮與一般商務週期相符的工作負載持續時間。
2. 要求啟動必要的工作負載，以便查詢存放區可以收集工作負載資料的基準 (如果尚未提供)。
3. 升級至由使用者選擇的目標資料庫相容性層級。
4. 要求收集第二個傳遞的工作負載資料，以進行比較和迴歸偵測。
5. 逐一查看基於 [查詢存放區 **迴歸查詢**](../../relational-databases/performance/monitoring-performance-by-using-the-query-store.md#Regressed)檢視所找到的任何迴歸，藉由收集有關適用的最佳化工具模型變化之可能排列的執行階段統計資料進行測試，並測量結果。 
6. 報告已測量的改善，並選擇性地允許使用[計劃指南](../../relational-databases/performance/plan-guides.md)保存這些變更。

如需附加資料庫的詳細資訊，請參閱[資料庫卸離與附加](../../relational-databases/databases/database-detach-and-attach-sql-server.md#AttachDb)。

請參閱下文，QTA 僅使用如上所示的查詢存放區來變更建議工作流程的最後一個步驟，以升級相容性層級。 QTA 提供針對所選取迴歸查詢的特定調整選項，以使用調整後執行計畫建立新的改善狀態，而不是選擇目前無效率的執行計畫或最後一個已知好執行計畫。

![使用 QTA 的建議資料庫升級工作流程](../../relational-databases/performance/media/qta-usage.png "使用 QTA 的建議資料庫升級工作流程")

### <a name="qta-tuning-internal-search-space"></a>QTA 調整內部搜尋空間

QTA 僅以可從查詢存放區中執行的 `SELECT` 查詢為目標。 如果編譯的參數已知，則參數化查詢是合格的。 仰賴執行階段建構 (例如暫存資料表或資料表變數) 的查詢目前不符合資格。

由於[基數估計工具 (CE)](../../relational-databases/performance/cardinality-estimation-sql-server.md) 版本的變更，QTA 針對可能的已知查詢回歸模式為目標。 例如，在將資料庫從 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 和資料庫相容性層級 110 升級到 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 和資料庫相容性層級 140 時，某些查詢可能會迴歸，因為它們是專門設計來搭配 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] (CE 70) 中存在的 CE 版本一起使用。 這並不表示從 CE 140 還原到 CE 70 是唯一的選項。 如果只有較新版本中的特定變更引入了迴歸，則可以提示該查詢僅使用先前 CE 版本的相關部分，該部分對特定查詢更有效，同時仍然利用較新版本之 CE 版本的所有其他改進功能。 此外，也允許未迴歸之工作負載中的其他查詢，從較新的 CE 改進功能中受益。

QTA 搜尋的 CE 模式如下：

- **獨立性與相互關聯**：如果獨立性假設可為特定查詢提供更好的估計，查詢提示 `USE HINT ('ASSUME_MIN_SELECTIVITY_FOR_FILTER_ESTIMATES')` 就會使得 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 在估計過濾器的 `AND` 述詞以考慮相互關聯時，使用最少的選擇性來產生執行計畫。 如需詳細資訊，請參閱 [USE HINT 查詢提示](../../t-sql/queries/hints-transact-sql-query.md#use_hint)和 [CE 的版本](../../relational-databases/performance/cardinality-estimation-sql-server.md#versions-of-the-ce)。
- **簡單內含項目與基底內含項目**：如果不同的聯結內含項目可為特定查詢提供更好的估計，查詢提示 `USE HINT ('ASSUME_JOIN_PREDICATE_DEPENDS_ON_FILTERS')` 就會使得 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 使用「簡單內含項目」假設而不是預設的「基底內含項目」假設來產生執行計畫。 如需詳細資訊，請參閱 [USE HINT 查詢提示](../../t-sql/queries/hints-transact-sql-query.md#use_hint)和 [CE 的版本](../../relational-databases/performance/cardinality-estimation-sql-server.md#versions-of-the-ce)。
- **多重陳述式資料表值函式 (MSTVF) 固定基數猜測** 的 100 個資料列與1 個資料列：如果與使用 1 個資料列之 TVF 的固定估計 (與 [!INCLUDE[ssKilimanjaro](../../includes/ssKilimanjaro-md.md)] 和更早版本的查詢最佳化工具 CE 模型下的預設值對應) 相比，100 個資料列之 TVF 的預設固定估計並不會產生更有效率的計畫，就會使用查詢提示 `QUERYTRACEON 9488` 來產生執行計畫。 如需有關 MSTVF 的詳細資訊，請參閱[建立使用者定義函式 &#40;資料庫引擎&#41;](../../relational-databases/user-defined-functions/create-user-defined-functions-database-engine.md#TVF)。

> [!NOTE]
> 作為最後的手段，如果窄範圍的提示沒有為符合資格的查詢模式產生足夠好的結果，那麼也可以考慮完整使用 CE 70 (藉由使用查詢提示 `USE HINT ('FORCE_LEGACY_CARDINALITY_ESTIMATION')` 來產生執行計畫)。

> [!IMPORTANT]
> 任何提示都會強制在未來的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 更新中解決特定行為。 建議您僅在沒有其他選項時套用提示，並計劃在每次有新的升級時重新瀏覽提示的代碼。 藉由強制執行行為，您可能會阻止您的工作負載受益於較新版本的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中引入的增強功能。

## <a name="starting-query-tuning-assistant-for-database-upgrades"></a>啟動查詢調整小幫手以進行資料庫升級

QTA 是一種以工作階段為基礎的功能，它會將工作階段狀態儲存在首次建立工作階段所在之使用者資料庫的 `msqta` 結構描述中。 可以在單一資料庫上建立多個微調工作階段，但是對於任何指定的資料庫，只能有一個作用中工作階段。

### <a name="creating-a-database-upgrade-session"></a>建立資料庫升級工作階段

1. 在 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 中開啟 [物件總管] 並連接到 [!INCLUDE[ssDE](../../includes/ssde-md.md)]。

2. 對於要升級資料庫相容性層級的資料庫，以滑鼠右鍵選取資料庫名稱，依序選取 [工作]、[資料庫升級]，然後選取 [新的資料庫升級工作階段]。

3. 在 [QTA 精靈] 視窗中，設定工作階段需要兩個步驟：

    1. 在 [安裝程式] 視窗中，設定查詢存放區來擷取相當於一個完整商務週期的工作負載資料，以進行分析及調整。
        - 輸入預期的工作負載持續時間 (以天為單位) (最短為 1 天)。 這將用於提出建議的查詢存放區設定，以暫時允許收集整個基準。 擷取理想的基準，對於確保在變更資料庫相容性層級後找到的任何迴歸查詢都能夠進行分析非常重要。 
        - 在 QTA 工作流程完成後，設定使用者資料庫預期的目標資料庫相容性層級。
        完成後，請選取 [下一步]。

       ![新的資料庫升級工作階段設定視窗](../../relational-databases/performance/media/qta-new-session-setup.png "新的資料庫升級工作階段設定視窗")  
  
    2. 在 [設定] 視窗中，兩個資料行顯示目標資料庫中查詢存放區的 **目前** 狀態，以及 **建議** 設定。 
        - 根據預設，會選取 [建議] 設定，但選取 [目前] 資料行上的選項按鈕，即會接受目前的設定，同時允許對目前的查詢存放區設定進行微調。
        - 提議的 *過時查詢臨界值* 設定是預期工作負載持續時間 (以天為單位) 的兩倍。 這是因為查詢存放區需要保存有關基準工作負載和升級後的資料庫工作負載的詳細資訊。
        完成後，請選取 [下一步]。

       ![新的資料庫升級設定視窗](../../relational-databases/performance/media/qta-new-session-settings.png "新的資料庫升級設定視窗")

       > [!IMPORTANT]
       > 建議的 *大小上限* 是一個適用於短時間工作負載的任意值。
       > 不過，請記住，對於非常大量的工作負載，可能不足以保存基準和升級後的資料庫工作負載的資訊，即可能會產生許多不同的計劃。
       > 如果您是預期會發生這個情況，那就輸入一個更合適的較高的值。

4. [微調] 視窗結束工作階段設定，並指示開啟並繼續進行工作階段的後續步驟。 完成後，請選取 [完成]。

    ![新的資料庫升級調整視窗](../../relational-databases/performance/media/qta-new-session-tuning.png "新的資料庫升級調整視窗")

> [!NOTE]
> 可能的替代方案一開始會先將資料庫備份從實際伺服器 (其中資料庫已經通過建議的資料庫相容性升級工作流程) 還原到測試伺服器。

### <a name="executing-the-database-upgrade-workflow"></a>執行資料庫升級工作流程

1. 對於要升級資料庫相容性層級的資料庫，以滑鼠右鍵選取資料庫名稱，依序選取 [工作]、[資料庫升級]，然後選取 [監視工作階段]。

2. [工作階段管理] 頁面會列出範圍內之資料庫的目前和過去工作階段。 選擇所需的工作階段，然後選取 [詳細資料]。

    > [!NOTE]
    > 如果目前的工作階段不存在，請選取 [重新整理] 按鈕。

    清單包含以下資訊：
    - **工作階段識別碼**
    - **工作階段名稱**：系統產生的名稱，由資料庫名稱、日期和工作階段建立的時間所組成。
    - **狀態**：工作階段的狀態 (「作用中」或「已關閉」)。
    - **描述**：系統產生，由使用者選取的目標資料庫相容性層級和商務週期工作負載的天數所組成。
    - **開始時間**：建立工作階段時的日期和時間。

    ![QTA 工作階段管理頁面](../../relational-databases/performance/media/qta-session-management.png "QTA 工作階段管理頁面")

    > [!NOTE]
    > **刪除工作階段** 刪除為選取的工作階段儲存的任何資料。
    > 不過，刪除已關閉的工作階段 **不會** 刪除任何先前已部署的計劃指南。
    > 如果您刪除已部署的計劃指南的工作階段，就無法使用 QTA 進行回復。
    > 相反地，使用 [sys.plan_guides](../../relational-databases/system-catalog-views/sys-plan-guides-transact-sql.md) 系統資料表搜尋計劃指南，並使用 [sp_control_plan_guide](../../relational-databases/system-stored-procedures/sp-control-plan-guide-transact-sql.md) 以手動方式刪除。
  
3. 新工作階段的進入點是 **資料收集** 步驟。

    > [!NOTE]
    > [工作階段] 按鈕返回到 [工作階段管理] 頁面，讓使用中的工作階段保持原樣。

    此步驟有三個子步驟：

    1. **基準資料收集** 要求使用者執行代表性工作負載循環，以便查詢存放區可以收集基準。 該工作負載完成後，請核取 [工作負載執行已完成]，然後選取 [下一步]。

        > [!NOTE]
        > 工作負載執行時，可以關閉 QTA 視窗。 稍後返回到仍保留在作用中狀態的工作階段，將從已停止的地方繼續。 

        ![QTA 步驟 2 子步驟 1](../../relational-databases/performance/media/qta-step2-substep1.png "QTA 步驟 2 子步驟 1")

    2. **升級資料庫** 將提示要求將資料庫相容性層級升級到所需目標的權限。 若要繼續下一個子步驟，請選取 [是]。

        ![QTA 步驟 2 子步驟 2 - 升級資料庫相容性層級](../../relational-databases/performance/media/qta-step2-substep2-prompt.png "QTA 步驟 2 子步驟 2 - 升級資料庫相容性層級")

        下列頁面確認已成功升級資料庫相容性層級。

        ![QTA 步驟 2 子步驟 2](../../relational-databases/performance/media/qta-step2-substep2.png "QTA 步驟 2 子步驟 2")

    3. **觀察到的資料收集** 要求使用者重新執行代表性工作負載循環，以讓查詢存放區收集將用來搜尋最佳化機會的比較基準。 當工作負載執行時，使用 [重新整理] 按鈕繼續更新迴歸查詢的清單 (如果有找到任何迴歸查詢)。 變更 **要顯示的查詢** 值以限制顯示的查詢數目。 清單的順序受 **計量** (持續時間或 CpuTime) 和 **彙總** (預設為平均值) 的影響。 同時選取要顯示 **要顯示的查詢** 數量。 該工作負載完成後，請核取 [工作負載執行已完成]，然後選取 [下一步]。

        ![QTA 步驟 2 子步驟 3](../../relational-databases/performance/media/qta-step2-substep3.png "QTA 步驟 2 子步驟 3")

        清單包含以下資訊：
        - **查詢識別碼** 
        - **查詢文字**：[!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式，可藉由選取 [...] 按鈕展開。
        - **回合數**：顯示針對整個工作負載集合，該查詢的執行次數。
        - **基準計量**：針對資料庫相容性升級前的基準資料收集選取的計量 (持續時間或 CpuTime)，以毫秒為單位。
        - **觀察的計量**：針對資料庫相容性升級後的資料收集選取的計量 (持續時間或 CpuTime)，以毫秒為單位。
        - **% 變更**：所選計量在資料庫相容性升級前與升級後狀態之間的變更百分比。 負數表示查詢的測量迴歸數量。
        - **可調整**：*True* 或 *False*，依據查詢是否適合進行測試而定。

4. **檢視分析** 允許選取要測試的查詢，並找出最佳化的機會。 **要顯示的查詢** 值成為要測試的合格查詢的範圍。 檢查完所需的查詢之後，選取 [下一步] 啟動測試。

    > [!NOTE]
    > 無法選取可調整 = False 的查詢進行實驗。

    > [!IMPORTANT]
    > 提示建議一旦 QTA 進入測試階段，將無法回到 [檢視分析] 頁面。   
    > 如果在移至測試階段之前未選取所有合格的查詢，則需要在稍後建立新的工作階段，並重複工作流程。 這需要將資料庫相容性層級重設為先前的值。

    ![QTA 步驟 3](../../relational-databases/performance/media/qta-step3.png "QTA 步驟 3")

5. **檢視結果** 允許選取將建議的最佳化部署為計劃指南的查詢。 

    清單包含以下資訊：
    - **查詢識別碼**
    - **查詢文字**：[!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式，可藉由選取 [...] 按鈕展開。
    - **狀態**：顯示查詢的目前測試狀態。
    - **基準計量**：在 **步驟 2 子步驟 3** 中執行之查詢的所選計量 (持續時間或 CpuTime)，以毫秒為單位，表示資料庫相容性升級後的迴歸查詢。
    - **觀察的計量**：針對測試後查詢選取的計量 (持續時間或 CpuTime)，以毫秒為單位，用以取得夠好的建議最佳化。
    - **% 變更**：所選計量在測試前與測試後狀態之間的變更百分比，代表對使用建議最佳化之查詢測量到的改進程度。
    - **查詢選項**：連結到可改善查詢執行計量的建議提示。
    - **可部署**：*True* 或 *False*，依據是否可以將建議的查詢最佳化部署成計劃指南而定。

    ![QTA 步驟 4](../../relational-databases/performance/media/qta-step4.png "QTA 步驟 4")

6. **驗證** 顯示先前針對此工作階段選取之查詢的部署狀態。 透過將 **可以部署** 資料行變更為 **可以回復**，此頁面中的清單與前一個頁面的不同。 此資料行可以是 *True* 或 *False*，取決於是否可以回復已部署的查詢最佳化並移除其計劃指南。

    ![QTA 步驟 5](../../relational-databases/performance/media/qta-step5.png "QTA 步驟 5")

    若以後需要復原建議的最佳化，請選取相關查詢，然後選取 [復原]。 移除該查詢計劃指南，並更新清單以移除已回復的查詢。 請注意，下圖中移除了查詢 8。

    ![QTA 步驟 5 - 復原](../../relational-databases/performance/media/qta-step5-rollback.png "QTA 步驟 5 - 復原")

    > [!NOTE]
    > 刪除已關閉的工作階段 **不會** 刪除任何先前已部署的規劃指南。
    > 如果您刪除已部署的計劃指南的工作階段，就無法使用 QTA 進行回復。
    > 相反地，使用 [sys.plan_guides](../../relational-databases/system-catalog-views/sys-plan-guides-transact-sql.md) 系統資料表搜尋計劃指南，並使用 [sp_control_plan_guide](../../relational-databases/system-stored-procedures/sp-control-plan-guide-transact-sql.md) 以手動方式刪除。
  
## <a name="permissions"></a>權限

需要 **db_owner** 角色的成員資格。
  
## <a name="see-also"></a>另請參閱

- [相容性層級和資料庫引擎升級](../../t-sql/statements/alter-database-transact-sql-compatibility-level.md#compatibility-levels-and-database-engine-upgrades)
- [效能監視及微調工具](../../relational-databases/performance/performance-monitoring-and-tuning-tools.md)
- [使用查詢存放區監視效能](../../relational-databases/performance/monitoring-performance-by-using-the-query-store.md)
- [變更資料庫相容性模式並使用查詢存放區](../../database-engine/install-windows/change-the-database-compatibility-mode-and-use-the-query-store.md)
- [追蹤旗標](../../t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql.md) \(機器翻譯\)
- [USE HINT 查詢提示](../../t-sql/queries/hints-transact-sql-query.md#use_hint)
- [基數估計工具](../../relational-databases/performance/cardinality-estimation-sql-server.md)
- [自動調整](../../relational-databases/automatic-tuning/automatic-tuning.md)   
- [使用 SQL Server 查詢調整小幫手](/learn/modules/use-sql-server-query-tuning-assistant/)