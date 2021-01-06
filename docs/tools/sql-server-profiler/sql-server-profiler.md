---
title: SQL Server Profiler
titleSuffix: SQL Server Profiler
description: 探索 SQL Server Profiler 的功能。 透過使用此工具建立追蹤和分析重新執行追蹤結果，以協助針對問題進行疑難排解。
ms.prod: sql
ms.prod_service: sql-tools
ms.reviewer: ''
ms.technology: profiler
ms.topic: conceptual
ms.assetid: 3ad5f33d-559e-41a4-bde6-bb98792f7f1a
author: markingmyname
ms.author: maghan
ms.custom: seo-lt-2019
ms.date: 05/01/2020
ms.openlocfilehash: 3169f8fbbc86fc95a62631c0cc93d77b6a46b0a4
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97643392"
---
# <a name="sql-server-profiler"></a>SQL Server Profiler

 [!INCLUDE[sql-asdbmi](../../includes/applies-to-version/sql-asdbmi.md)]

[!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)] 是一個介面，可以建立和管理追蹤，以及分析和重新執行追蹤結果。 事件會儲存於追蹤檔案中，稍後在診斷問題時，可用來進行分析或是重新執行特定的一連串步驟。

> [!IMPORTANT]
> SQL 追蹤和 [!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)] 已被淘汰。 包含 Microsoft SQL Server 追蹤和重新執行物件的 *Microsoft.SqlServer.Management.Trace* 命名空間也會被淘汰。
> [!INCLUDE[ssNoteDepFutureAvoid](../../includes/ssnotedepfutureavoid-md.md)]
> 請改用擴充事件。 如需[延伸事件](../../relational-databases/extended-events/extended-events.md)的詳細資訊，請參閱[快速入門：SQL Server 中的延伸事件](../../relational-databases/extended-events/quick-start-extended-events-in-sql-server.md)和 [SSMS XEvent 分析工具](../../relational-databases/extended-events/use-the-ssms-xe-profiler.md)。

> [!NOTE]
> 支援適用於 Analysis Services 工作負載的 [!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)]。

> [!NOTE]
> 當嘗試從 SQL Server Profiler 連線至 Azure SQL Database 時，會擲回具誤導性的錯誤訊息，如下所示：
>
> - 若要對 SQL Server 執行追蹤，則必須為系統管理員固定伺服器角色的成員，或具有「改變追蹤」權限。
>
> 此訊息原本應說明 SQL Server Profiler 不支援 Azure SQL Database。

## <a name="where-is-the-profiler"></a>Profiler 位於何處？

您可以從 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 內以數種方式啟動 Profiler。 [以下主題將列出各種啟動 Profiler 的方式。](start-sql-server-profiler.md)

## <a name="capture-and-replay-trace-data"></a>擷取並重新執行追蹤資料

下表顯示我們建議在 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 中用來擷取和重新執行追蹤資料的功能。

||||
|-|-|-|
|**功能\目標工作負載**|**關聯式引擎**|**Analysis Services (英文)**|  
|**追蹤擷取**|[!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 中的[擴充事件](../../relational-databases/extended-events/extended-events.md)圖形化使用者介面|[!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)]|  
|**追蹤重新執行**|[Distributed Replay](../distributed-replay/sql-server-distributed-replay.md)|[!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)]|

## <a name="use-sql-server-profiler"></a>使用 SQL Server Profiler

Microsoft [!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)] 是「SQL 追蹤」的圖形化使用者介面，可用來監視 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 或 Analysis Services 的執行個體。 您可以擷取每一個事件的相關資料，並將資料儲存至檔案或資料表，以供稍後分析。 例如，您可以監視生產環境，查看哪些預存程序由於執行速度過慢而影響效能。 [!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)] 可用於下列活動：

- 逐步執行問題查詢來找出問題原因。

- 尋找和診斷執行速度很慢的查詢。

- 擷取造成問題的 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式系列。 之後，就可以利用已儲存的追蹤，在能夠診斷問題的伺服器上複製這個問題。

- 監視 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的效能來微調工作負載。 如需有關針對資料庫工作負載來微調實體資料庫設計的資訊，請參閱＜ [Database Engine Tuning Advisor](../../relational-databases/performance/database-engine-tuning-advisor.md)＞。

- 將效能計數器相互關聯以診斷問題。

另外，[!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)] 也支援稽核在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體上執行的動作。 稽核會將安全性相關動作記錄下來，供安全性管理員稍後進行檢閱。

## <a name="sql-server-profiler-concepts"></a>SQL Server Profiler 概念

若要使用 [!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)]，您必須對於描述工具功能的專門用語有所了解。

> [!NOTE]
> 在使用 [!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)] 時，了解 SQL 追蹤會對您非常有幫助。 如需詳細資訊，請參閱 [SQL Trace](../../relational-databases/sql-trace/sql-trace.md)。

### <a name="event"></a>事件

事件是在 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]之執行個體中產生的動作。 範例如下：  
  
- 登入連接、失敗及中斷連接。    
- [!INCLUDE[tsql](../../includes/tsql-md.md)] `SELECT`、`INSERT`、`UPDATE`，以及 `DELETE` 陳述式。
- 遠端程序呼叫 (RPC) 批次狀態。  
- 預存程序的啟動或結束。  
- 預存程序內部陳述式的啟動或結束。  
- SQL 批次的啟動或結束。  
- 寫入 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 錯誤記錄檔的錯誤。  
- 資料庫物件上的鎖定被取得或釋放。  
- 開放式資料指標。  
- 安全性權限檢查。  
  
事件產生的所有資料，都會在追蹤中以單一資料列呈現。 另有資料行與此資料列交叉，用以詳細描述該事件。  

### <a name="eventclass"></a>EventClass

事件類別是一種可追蹤的事件類型。 事件類別包含所有可由事件來報告的資料。 事件類別的範例如下：

- **SQL:BatchCompleted**
- **稽核登入**
- **稽核登出**
- **Lock:Acquired**
- **Lock:Released**

### <a name="eventcategory"></a>EventCategory

事件類別目錄用來定義事件在 [!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)]中的分組方式。 例如，所有的鎖定事件類別都會在 **Locks** 事件類別目錄中予以分組。 然而，事件類別目錄僅存在於 [!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)]中。 這個字詞無法反映 Engine 事件的分組方式。

### <a name="datacolumn"></a>DataColumn

資料行是追蹤中所擷取事件類別的屬性。 因為事件類別會決定可收集的資料類型，所以並不是所有的資料行都適用於所有的事件類別。 例如，在擷取 **Lock:Acquired** 事件類別的追蹤中，**BinaryData** 資料行包含鎖定分頁識別碼或資料列的值，但是 **Integer Data** 資料行則不包含任何值，因為其不適用於所擷取的事件類別。

### <a name="template"></a>[範本]

範本可定義追蹤的預設組態。 它特別包含您要用 [!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)]來監視的事件類別。 例如，您可以建立範本，其中指定要使用的事件、資料行及篩選。 範本不能執行，但會儲存為 .tdf 副檔名的檔案。 一旦儲存之後，當您啟動以範本為依據的追蹤時，範本就會控制所要擷取的追蹤資料。

### <a name="trace"></a>追蹤

追蹤會根據所選取的事件類別、資料行及篩選，來擷取資料。 例如，您可以建立追蹤來監視異常錯誤。 若要執行此作業，請選取 **Exception** 事件類別及 **Error**、 **State** 和 **Severity** 資料行。 必須同時收集這三個資料行的資料，追蹤結果才能提供有意義的資料。 接著，您可以執行以此方式設定的追蹤，並收集出現在伺服器中之任何 **Exception** 事件的資料。 追蹤資料可以儲存起來，也可以立即用於分析。 稍後可以再重新執行追蹤，但有些事件 (例如 **Exception** 事件) 永遠也無法重新執行。 您也可以將追蹤儲存成範本，以在未來建立類似的追蹤。  

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 提供兩種方式來追蹤 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的執行個體：您可以透過 [!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)] 追蹤，或是使用系統預存程序追蹤。  

### <a name="filter"></a>Filter

建立追蹤或範本時，您可以定義條件來篩選由事件所收集的資料。 若不想讓追蹤變得太大，您可加以篩選，只收集某些事件資料的子集。 例如，您可以在追蹤中將 Microsoft Windows 使用者名稱限制在特定的使用者，進而減少輸出資料。  

如果沒有設定篩選條件，則選定事件類別的所有事件都會傳回到追蹤輸出。

## <a name="sql-server-profiler-tasks"></a>SQL Server Profiler 工作

|工作描述|主題|  
|----------------------|-----------|  
|列出 SQL Server 提供用於監視事件特定類型的預先定義範本，以及用以重新執行追蹤的必要權限。|[SQL Server Profiler 範本和權限](../../tools/sql-server-profiler/sql-server-profiler-templates-and-permissions.md)|  
|描述如何執行 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Profiler。|[執行 SQL Server Profiler 所需的權限](../../tools/sql-server-profiler/permissions-required-to-run-sql-server-profiler.md)|  
|描述如何建立追蹤。|[建立追蹤 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/create-a-trace-sql-server-profiler.md)|  
|描述如何指定追蹤檔案的事件和資料行。|[指定追蹤檔案的事件及資料行 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/specify-events-and-data-columns-for-a-trace-file-sql-server-profiler.md)|  
|描述如何將追蹤結果儲存至檔案。|[將追蹤結果儲存至檔案 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/save-trace-results-to-a-file-sql-server-profiler.md)|  
|描述如何將追蹤結果儲存至資料表。|[將追蹤結果儲存到資料表 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/save-trace-results-to-a-table-sql-server-profiler.md)|  
|描述如何篩選追蹤中的事件。|[篩選追蹤中的事件 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/filter-events-in-a-trace-sql-server-profiler.md)|  
|描述如何檢視篩選資訊。|[檢視篩選資訊 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/view-filter-information-sql-server-profiler.md)|  
|描述如何修改篩選。|[修改篩選 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/modify-a-filter-sql-server-profiler.md)|  
|描述如何為追蹤檔案 ([!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)]) 設定檔案大小上限。|[設定追蹤檔案的檔案大小上限 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/set-a-maximum-file-size-for-a-trace-file-sql-server-profiler.md)|  
|描述如何設定追蹤資料表的資料表大小上限。|[設定追蹤資料表的資料表大小上限 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/set-a-maximum-table-size-for-a-trace-table-sql-server-profiler.md)|  
|描述如何啟動追蹤。|[啟動追蹤](../../tools/sql-server-profiler/start-a-trace.md)|  
|描述如何在連接至伺服器後自動啟動追蹤。|[在連接伺服器之後自動啟動追蹤 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/start-a-trace-automatically-after-connecting-to-a-server-sql-server-profiler.md)|  
|描述如何根據事件開始時間篩選事件。|[根據事件開始時間篩選事件 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/filter-events-based-on-the-event-start-time-sql-server-profiler.md)|  
|描述如何根據事件結束時間篩選事件。|[根據事件結束時間篩選事件 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/filter-events-based-on-the-event-end-time-sql-server-profiler.md)|  
|描述如何篩選追蹤中的伺服器處理序識別碼 (SPID)。|[篩選追蹤中的伺服器處理序識別碼 &#40;SPIDs&#41; &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/filter-server-process-ids-spids-in-a-trace-sql-server-profiler.md)|  
|描述如何暫停追蹤。|[暫停追蹤 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/pause-a-trace-sql-server-profiler.md)|  
|描述如何停止追蹤。|[停止追蹤 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/stop-a-trace-sql-server-profiler.md)|  
|描述如何在追蹤已暫停或停止之後執行追蹤。|[在追蹤暫停或停止之後執行追蹤 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/run-a-trace-after-it-has-been-paused-or-stopped-sql-server-profiler.md)|  
|描述如何清除追蹤視窗。|[清除追蹤視窗 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/clear-a-trace-window-sql-server-profiler.md)|  
|描述如何關閉追蹤視窗。|[關閉追蹤視窗 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/close-a-trace-window-sql-server-profiler.md)|  
|描述如何設定追蹤定義預設值。|[設定追蹤定義預設值 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/set-trace-definition-defaults-sql-server-profiler.md)|  
|描述如何設定追蹤顯示預設值。|[設定追蹤顯示預設值 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/set-trace-display-defaults-sql-server-profiler.md)|  
|描述如何開啟追蹤檔案。|[開啟追蹤檔案 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/open-a-trace-file-sql-server-profiler.md)|  
|描述如何開啟追蹤資料表。|[開啟追蹤資料表 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/open-a-trace-table-sql-server-profiler.md)|  
|描述如何重新執行追蹤資料表。|[重新執行追蹤資料表 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/replay-a-trace-table-sql-server-profiler.md)|  
|描述如何重新執行追蹤檔案。|[重新執行追蹤檔案 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/replay-a-trace-file-sql-server-profiler.md)|  
|描述如何一次重新執行單一事件。|[一次重新執行一個事件 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/replay-a-single-event-at-a-time-sql-server-profiler.md)|  
|描述如何重新執行至中斷點。|[重新執行至中斷點 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/replay-to-a-breakpoint-sql-server-profiler.md)|  
|描述如何重新執行至資料指標。|[重新執行至資料指標處 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/replay-to-a-cursor-sql-server-profiler.md)|  
|描述如何重新執行 [!INCLUDE[tsql](../../includes/tsql-md.md)] 指令碼。|[重新執行 Transact-SQL 指令碼 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/replay-a-transact-sql-script-sql-server-profiler.md)|  
|描述如何建立追蹤範本。|[建立追蹤範本 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/create-a-trace-template-sql-server-profiler.md)|  
|描述如何修改追蹤範本。|[修改追蹤範本 &#40;SQL Server Profiler&#41;](./modify-trace-templates.md)|  
|描述如何設定全域追蹤選項。|[設定全域追蹤選項 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/set-global-trace-options-sql-server-profiler.md)|  
|描述如何在追蹤時尋找值或資料行。|[在追蹤時尋找值或資料行 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/find-a-value-or-data-column-while-tracing-sql-server-profiler.md)|  
|描述如何從執行中追蹤衍生範本。|[從執行中的追蹤衍生範本 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/derive-a-template-from-a-running-trace-sql-server-profiler.md)|  
|描述如何從追蹤檔案或追蹤資料表衍生範本。|[從追蹤檔案或追蹤資料表衍生範本 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/derive-a-template-from-a-trace-file-or-trace-table-sql-server-profiler.md)|  
|描述如何建立用於執行追蹤的 [!INCLUDE[tsql](../../includes/tsql-md.md)] 指令碼。|[建立 Transact-SQL 指令碼以執行追蹤 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/create-a-transact-sql-script-for-running-a-trace-sql-server-profiler.md)|  
|描述如何匯出追蹤範本。|[匯出追蹤範本 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/export-a-trace-template-sql-server-profiler.md)|  
|描述如何匯入追蹤範本。|[匯入追蹤範本 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/import-a-trace-template-sql-server-profiler.md)|  
|描述如何從追蹤擷取指令碼。|[從追蹤中擷取指令碼 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/extract-a-script-from-a-trace-sql-server-profiler.md)|  
|描述如何使追蹤與 Windows 效能記錄資料相互關聯。|[使追蹤與 Windows 效能記錄資料產生相互關聯 &#40;SQL Server Profiler&#41;](./correlate-a-trace-with-windows-performance-log-data.md)|  
|描述如何組織追蹤中所顯示的資料行。|[組織追蹤內顯示的資料行 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/organize-columns-displayed-in-a-trace-sql-server-profiler.md)|  
|描述如何啟動 [!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)]。|[啟動 SQL Server Profiler](../../tools/sql-server-profiler/start-sql-server-profiler.md)|  
|描述如何儲存追蹤及追蹤範本。|[儲存追蹤及追蹤範本](../../tools/sql-server-profiler/save-traces-and-trace-templates.md)|  
|描述如何修改追蹤範本。|[修改追蹤範本](../../tools/sql-server-profiler/modify-trace-templates.md)|  
|描述如何使追蹤與 Windows 效能記錄資料相互關聯。|[使追蹤與 Windows 效能記錄資料相互關聯](../../tools/sql-server-profiler/correlate-a-trace-with-windows-performance-log-data.md)|  
|描述如何使用 [!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)] 檢視及分析追蹤。|[使用 SQL Server Profiler 檢視和分析追蹤](../../tools/sql-server-profiler/view-and-analyze-traces-with-sql-server-profiler.md)|  
|描述如何使用 [!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)] 分析死結。|[使用 SQL Server Profiler 分析死結](../../tools/sql-server-profiler/analyze-deadlocks-with-sql-server-profiler.md)|  
|描述如何在 SQL Server Profiler 中分析具有 SHOWPLAN 結果的查詢。|[在 SQL Server Profiler 中使用 SHOWPLAN 結果分析查詢](../../tools/sql-server-profiler/analyze-queries-with-showplan-results-in-sql-server-profiler.md)|  
|描述如何使用 [!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)] 篩選追蹤。|[使用 SQL Server Profiler 篩選追蹤](../../tools/sql-server-profiler/filter-traces-with-sql-server-profiler.md)|  
|描述如何使用 [!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)] 重新執行功能。|[重新執行追蹤](../../tools/sql-server-profiler/replay-traces.md)|  
|列出 [!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)] 的即時線上說明主題。|[SQL Server Profiler F1 說明](../../tools/sql-server-profiler/sql-server-profiler-f1-help.md)|  
|列出 [!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)] 用以監視效能及活動的系統預存程序。|[SQL Server Profiler 預存程序 &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sql-server-profiler-stored-procedures-transact-sql.md)|  

## <a name="see-also"></a>另請參閱

- [Locks 事件類別目錄](../../relational-databases/event-classes/locks-event-category.md)
- [Sessions 事件類別目錄](../../relational-databases/event-classes/sessions-event-category.md)
- [Stored Procedures 事件類別目錄](../../relational-databases/event-classes/stored-procedures-event-category.md)
- [TSQL 事件類別目錄](../../relational-databases/event-classes/tsql-event-category.md)
- [伺服器效能與活動監視](../../relational-databases/performance/server-performance-and-activity-monitoring.md)