---
title: SSMS 查詢編輯器
description: SQL Server Management Studio (SSMS) 查詢編輯器
ms.prod: sql
ms.prod_service: sql-tools
ms.technology: ssms
ms.topic: conceptual
f1_keywords:
- sql13.swb.query.advanced.f1
- sql13.swb.query.ansi.f1
- sql13.swb.query.general.f1
- sql13.swb.query.grid.f1
- sql13.swb.sqleditors.multiserverresultssettings
- sql13.swb.tsqlquery.f1
- sql13.swb.tsqlresults.f1
dev_langs:
- TSQL
helpviewer_keywords:
- SQL Server Management Studio [SQL Server], editor
- SQL Server Management Studio [SQL Server], Database Engine Query Editor
- SQL Server Management Studio [SQL Server], templates
- SQL Server Management Studio [SQL Server], query editor
- Query Editor [SQL Server Management Studio]
- Query Editor [Database Engine]
- Query Editor [Database Engine], Features
- Query Editor [Database Engine], Toolbar
- Query Editor [SQL Server Management Studio], full screen mode
- Query Editor [Database Engine], templates
- Query Editor [SQL Server Management Studio], about Query Editor
- Transact-SQL Editor See Query Editor [Database Engine]
- Database Engine Query Editor See Query Editor [Database Engine]
- Code Editor [SQL Server Management Studio], about Query Editor
- editors [SQL Server Management Studio], Database Engine Query Editor
- full screen mode [SQL Server Management Studio]
- writing scripts
- modifying scripts
- writing queries
- scripts [SQL Server], SQL Server Management Studio
- queries [SQL Server], SQL Server Management Studio
ms.assetid: 05cfae9b-96d5-4a35-a098-0bc3a548bcfc
author: markingmyname
ms.author: maghan
ms.reviewer: ''
ms.custom: seo-lt-2019, contperf-fy21q1
ms.date: 08/28/2020
ms.openlocfilehash: 87fd12a17b86a47e6fec1b235ae9e9a21fde6e45
ms.sourcegitcommit: cb8e2ce950d8199470ff1259c9430f0560f0dc1d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2021
ms.locfileid: "97878949"
---
# <a name="sql-server-management-studio-ssms-query-editor"></a>SQL Server Management Studio (SSMS) 查詢編輯器

[!INCLUDE [SQL Server ASDB, ASDBMI, ASDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa.md)]

本文說明查詢編輯器在 SQL Server Management Studio (SSMS) 中的功能和函式。

> [!Note]
> 如果您想要了解如何使用 Transact-SQL (T-SQL) F1 說明，請參閱 [Transact-SQL F1 說明](#transact-sql-f1-help)一節。
>
> 如果您想要了解使用編輯器可執行哪些工作，請瀏覽[編輯器工作](#editor-tasks)一節。

SSMS 中的編輯器會共用一般架構。 文字編輯器實作基本的功能層級，並可以當做文字檔的基本編輯器使用。 其他編輯器 (查詢編輯器) 擴充了此功能基礎，而納入了一項語言服務，用以定義 SQL Server 支援的其中一種語言的語法。 查詢編輯器也會針對 IntelliSense 和偵錯等編輯器功能，實作不同的支援層級。 這些查詢編輯器包括用來建立包含 T-SQL 和 XQuery 陳述式之指令碼的 Database Engine 查詢編輯器、MDX 語言適用的 MDX 編輯器、DMX 語言適用的 DMX 編輯器，以及 XML for Analysis 語言適用的 XML/A 編輯器。
您可以使用查詢編輯器來建立及執行包含 Transact-SQL 陳述式的指令碼。

![新增查詢](media/database-engine-query-editor-sql-server-management-studio/new-query.png)

## <a name="sql-editor-toolbar"></a>SQL 編輯器工具列

當查詢編輯器開啟時，會顯示具有下列按鈕的 SQL 編輯器工具列。

您也可以加入 SQL 編輯器工具列，其方式是選取 **[檢視]** 功能表、選取 **[工具列]** ，然後選取 **[SQL 編輯器]** 。 如果您在沒有任何查詢編輯器視窗開啟時加入 SQL 編輯器工具列，則所有的按鈕都將無法使用。

![編輯器工具列](media/database-engine-query-editor-sql-server-management-studio/editor-toolbar.png)

### <a name="connect-using-the-editor-toolbar"></a>連接 (使用編輯器工具列)

開啟 **[連接到伺服器](connect-to-server-database-engine.md)** 對話方塊。 使用此對話方塊可建立與伺服器的連接。

您也可以使用[捷徑功能表](#connection-using-the-context-menu)來連接到您的資料庫。

### <a name="change-connection-using-the-editor-toolbar"></a>變更連接 (使用編輯器工具列)

隨即開啟 [連接到伺服器]  對話方塊。 使用此對話方塊建立[與不同伺服器的連接](f1-help-for-server-connections-sql-server-management-studio.md)。

您也可以使用[捷徑功能表](#connection-using-the-context-menu)來變更連接。

### <a name="available-databases-using-the-editor-toolbar"></a>可用資料庫 (使用編輯器工具列)

變更連接到同一伺服器的其他資料庫。

### <a name="execute-using-the-editor-toolbar"></a>執行 (使用編輯器工具列)

執行選取的程式碼，或是在未選取任何程式碼時，執行所有查詢編輯器程式碼。

您也可以透過選取 F5 或從 [捷徑功能表](#execute-using-the-context-menu)**執行** 查詢。

### <a name="cancel-executing-query-using-the-editor-toolbar"></a>取消執行查詢 (使用編輯器工具列)

將取消要求傳送到伺服器。 部分查詢無法立即取消，但必須等候適當的取消條件。 當取消交易之後，交易回復時可能產生延遲。

您也可以透過選取 Alt + Break 來取消執行中的查詢。

### <a name="parse-using-the-editor-toolbar"></a>剖析 (使用編輯器工具列)

檢查所選取程式碼的語法。 如果未選取任何程式碼，其會檢查 [查詢編輯器] 視窗中所有程式碼的語法。

您也可以透過選取 Ctrl + F5 來檢查查詢編輯器中的程式碼。

### <a name="display-estimated-execution-plan-using-the-editor-toolbar"></a>顯示預估的執行計畫 (使用編輯器工具列)

在未執行查詢的情況下，向查詢處理器要求查詢執行計畫，並且將計畫顯示在 [執行計畫] 視窗中。 此計畫會使用索引統計資料，預估在執行查詢的每個部分期間預期將傳回的資料列數目。 使用的實際查詢計劃可以與預估的執行計畫不同。 如果傳回的資料列數目與估計值不同，而查詢處理器為了要提高效率而變更計畫時，就可能發生這種情況。

您也可以透過選取 Ctrl + L 或從[捷徑功能表](#display-estimated-execution-plan-using-the-context-menu)顯示預估的執行計畫。

### <a name="query-options-using-the-editor-toolbar"></a>查詢選項 (使用編輯器工具列)

隨即開啟 [查詢選項]  對話方塊。 使用此對話方塊可設定查詢執行與查詢結果的預設選項。

您也可以從[捷徑功能表](#query-options-using-the-context-menu)選取 [查詢選項]  。

### <a name="intellisense-enabled-using-the-editor-toolbar"></a>IntelliSense 已啟用 (使用編輯器工具列)

指定是否可在資料庫引擎查詢編輯器中使用 [IntelliSense](../scripting/configure-intellisense-sql-server-management-studio.md) 功能。 預設會設定此選項。

您也可以透過選取 Ctrl + B 然後選取 Ctrl + I (或從[內容功能表](#intellisense-enabled-using-the-context-menu)) 來選取 [IntelliSense 已啟用]  。

### <a name="include-actual-execution-plan-using-the-editor-toolbar"></a>包括實際執行計畫 (使用編輯器工具列)

執行查詢、傳回查詢結果，以及使用查詢的執行計畫。 查詢會以圖形化查詢計劃的形式出現在 [執行計畫] 視窗中。

您也可以透過選取 Ctrl + M 或從[捷徑功能表](#include-actual-execution-plan-using-the-context-menu)選取 [包括實際執行計畫]  。

### <a name="include-live-query-statistics-using-the-editor-toolbar"></a>包含即時查詢統計資料 (使用編輯器工具列)

隨著控制權在查詢計畫運算子之間流動，提供查詢執行程序的即時見解。

您也可以從[捷徑功能表](#include-live-query-statistics-using-the-context-menu)選取 [包含即時查詢統計資料]  。

### <a name="include-client-statistics-using-the-editor-toolbar"></a>包括用戶端統計資料 (使用編輯器工具列)

包括 [用戶端統計資料]  視窗，其中包含關於查詢、網路封包的統計資料，以及查詢經過的時間。

您也可以透過選取 Shift + Alt + S 或從[捷徑功能表](#include-client-statistics-using-the-context-menu)選取 [包含即時查詢統計資料]  。

### <a name="results-to-text-using-the-editor-toolbar"></a>以文字顯示結果 (使用編輯器工具列)

在 [結果]  視窗中以文字傳回查詢結果。

您也可以透過選取 Ctrl + T 或從[捷徑功能表](#results-using-the-context-menu)以文字傳回結果。

### <a name="results-to-grid-using-the-editor-toolbar"></a>以方格顯示結果 (使用編輯器工具列)

在 [結果]  視窗中以一或多個方格傳回查詢結果。 此選項預設為啟用。

您也可以透過選取 Ctrl + D 或從[捷徑功能表](#results-using-the-context-menu)以文字傳回結果。

### <a name="results-to-file-using-the-editor-toolbar"></a>將結果存檔 (使用編輯器工具列)

執行查詢時，會開啟 [儲存結果]  對話方塊。 在 **[儲存於]** 中，選取您想要用來儲存檔案的資料夾。 在 [檔案名稱] 中，輸入檔案的名稱，然後選取 [儲存] 以將查詢結果儲存為具有 .rpt 副檔名的 **報告** 檔案。 若要使用進階選項，請選取 [儲存] 按鈕上的向下箭頭，然後選取 [使用編碼方式儲存]。

您也可以透過選取 Ctrl + Shift + F 或從[捷徑功能表](#results-using-the-context-menu)以文字傳回結果。

### <a name="comment-out-the-selected-lines-using-the-editor-toolbar"></a>註解選取行 (使用編輯器工具列)

在行頭加入註解運算子 (--)，將目前的行標示為註解。

您也可以選取 Ctrl + K，然後選取 Ctrl + C 將某行標示為註解。

### <a name="uncomment-the-selected-lines-using-the-editor-toolbar"></a>取消註解選取行 (使用編輯器工具列)

在行頭移除任何註解運算子 (--)，將目前的行標示為使用中的來源陳述式。

您也可以選取 Ctrl + K，然後選取 Ctrl + U 以取消某行的註解。

### <a name="decrease-indent-using-the-editor-toolbar"></a>減少縮排 (使用編輯器工具列)

移除行頭的空白，將此行的文字移到左邊。

### <a name="increase-line-indent-using-the-editor-toolbar"></a>增加行縮排 (使用編輯器工具列)

在行頭加入空白，將此行的文字移到右邊。

### <a name="specify-values-for-template-parameters-using-the-editor-toolbar"></a>指定範本參數的值 (使用編輯器工具列)

開啟一個對話方塊，讓您指定預存程序和函數中之參數的值。

## <a name="context-menu"></a>捷徑功能表

以滑鼠右鍵按一下  查詢編輯器中的任何位置，即可存取捷徑功能表。 捷徑功能表中的選項類似於 SQL 編輯器工具列。 使用捷徑功能表時，您會看到如 [連接] 與 [執行] 的相同選項，但您也可以列出其他選項，例如 [插入程式碼片段] 與 [範圍陳述式]。

![選項](media/database-engine-query-editor-sql-server-management-studio/context-menu.png)

### <a name="insert-snippet-using-the-context-menu"></a>插入程式碼片段 (使用捷徑功能表)

[T-SQL 程式碼片段](../scripting/add-transact-sql-snippets.md)是範本，當您在查詢編輯器中撰寫新的 Transact-SQL 陳述式時可將其作為起點。

### <a name="surround-with-using-the-context-menu"></a>範圍陳述式 (使用捷徑功能表)

範圍陳述式程式碼片段是一個範本，當您在 BEGIN、IF 或 WHILE 區塊中封入一組 Transact-SQL 陳述式時，可將其當作起點使用。

### <a name="connection-using-the-context-menu"></a>連接 (使用捷徑功能表)

![連接](media/database-engine-query-editor-sql-server-management-studio/context-menu-connections.png)

相較於 SSMS 中的工具列選項，捷徑功能表中有更多 [連接]  選項可用。

- **連接** - 開啟 [連接到伺服器] 對話方塊。 使用此對話方塊可建立與伺服器的連接。

- **中斷連接** - 中斷目前查詢編輯器與伺服器的連接。

- **中斷所有查詢的連接** - 中斷所有查詢連接。

- **變更連接** - 開啟 [連接到伺服器] 對話方塊。 使用此對話方塊可建立與不同伺服器的連接。

### <a name="open-server-in-object-explorer-using-the-context-menu"></a>在物件總管中開啟伺服器 (使用捷徑功能表)

[物件總管] 提供階層式使用者介面以檢視及管理每個 SQL Server 執行個體中的物件。 [物件總管詳細資料] 窗格會以表格式檢視來呈現執行個體物件，而且可以搜尋特定物件。 物件總管的功能會隨著伺服器的類型而有些不同，不過，它通常會包括資料庫的開發功能，以及所有伺服器類型的管理功能。

### <a name="execute-using-the-context-menu"></a>執行 (使用捷徑功能表)

執行選取的程式碼，或是在未選取任何程式碼時，執行查詢編輯器中的所有程式碼。

### <a name="display-estimated-execution-plan-using-the-context-menu"></a>顯示預估的執行計畫 (使用捷徑功能表)

在未實際執行查詢的情況下，向查詢處理器要求查詢執行計畫，並且將計畫顯示在 [執行計畫]  視窗中。 此計畫會使用索引統計資料，預估在執行查詢的每個部分期間預期將傳回的資料列數目。 使用的實際查詢計劃可以與預估的執行計畫不同。 如果傳回的資料列數與預估的資料列數有所差異，而且查詢處理器為了要提高效率而變更計畫時，就可能發生這個情況

### <a name="intellisense-enabled-using-the-context-menu"></a>IntelliSense 已啟用 (使用捷徑功能表)

指定是否可在資料庫引擎查詢編輯器中使用 IntelliSense 功能。 預設會設定此選項。

### <a name="trace-query-in-sql-server-profiler-using-the-context-menu"></a>在 SQL Server Profiler 中追蹤查詢 (使用捷徑功能表)

SQL Server Profiler 是一個介面，可用來建立及管理追蹤，以及分析及重新執行追蹤結果。 事件會儲存於追蹤檔案中，稍後在嘗試診斷問題時，可以用來進行分析或是重新執行特定的一連串步驟。

### <a name="analyze-query-in-database-engine-tuning-advisor-using-the-context-menu"></a>在 Database Engine Tuning Advisor 中分析查詢 (使用捷徑功能表)

Microsoft Database Engine Tuning Advisor (DTA) 會分析資料庫，並提出用來最佳化查詢效能的建議。 您可以使用 Database Engine Tuning Advisor 來選取及建立最佳索引、索引檢視表或資料表資料分割集合，而不需要深入了解資料庫結構或 SQL Server 本質內容。 您可以使用 DTA 來執行下列工作。

### <a name="design-query-in-editor-using-the-context-menu"></a>在編輯器中設計查詢 (使用捷徑容功能表)

在開啟檢視的定義、顯示查詢或檢視的結果，或者建立或開啟查詢時，[查詢和檢視設計師] 會開啟。

### <a name="include-actual-execution-plan-using-the-context-menu"></a>包括實際執行計畫 (使用捷徑功能表)

執行查詢、傳回查詢結果，以及使用查詢的執行計畫。 查詢會以圖形化查詢計劃的形式出現在 [執行計畫] 視窗中。

### <a name="include-live-query-statistics-using-the-context-menu"></a>包含即時查詢統計資料 (使用捷徑功能表)

隨著控制權在查詢計畫運算子之間流動，提供查詢執行程序的即時見解。

### <a name="include-client-statistics-using-the-context-menu"></a>包括用戶端統計資料 (使用捷徑功能表)

包括 [用戶端統計資料]  視窗，其中包含關於查詢、網路封包的統計資料，以及查詢經過的時間。

### <a name="results-using-the-context-menu"></a>結果 (使用捷徑功能表)

![結果選項](media/database-engine-query-editor-sql-server-management-studio/context-menu-results.png)

您可以從捷徑功能表選取想要的任何 [結果]  選項。

- **以文字顯示結果** - 在 [結果]  視窗中以文字傳回查詢結果。

- **以方格顯示結果** - 在 [結果]  視窗中以一或多個方格傳回查詢結果。

- **將結果存檔** - 當查詢執行時，會開啟 [儲存結果]  對話方塊。 在 **[儲存於]** 中，選取您想要用來儲存檔案的資料夾。 在 [檔案名稱] 中，輸入檔案的名稱，然後選取 [儲存] 以將查詢結果儲存為具有 .rpt 副檔名的 **報告** 檔案。 若要使用進階選項，請選取 [儲存] 按鈕上的向下箭頭，然後選取 [使用編碼方式儲存]。

### <a name="properties-window-using-the-context-menu"></a>屬性視窗 (使用捷徑功能表)

[屬性視窗](../menu-help/properties-window-f1-help-management-studio.md)描述 SQL Server Management Studio 中項目 (例如連線或執行程序表運算子) 的狀態，以及資料庫物件 (例如資料表、檢視表和設計師) 的相關資訊。

您可以利用 [屬性] 視窗來檢視目前連接的屬性。 許多屬性在 [屬性] 視窗中都是唯讀的，但在 Management Studio 中的其他位置是可改變的。 例如，查詢的 [資料庫] 屬性在 [屬性] 視窗是唯讀的，但在工具列中是可改變的。

### <a name="query-options-using-the-context-menu"></a>查詢選項 (使用捷徑功能表)

隨即開啟 [查詢選項]  對話方塊。 使用此對話方塊可設定查詢執行與查詢結果的預設選項。

## <a name="transact-sql-f1-help"></a>Transact-SQL F1 說明

查詢編輯器支援當您選取 F1 時將您連結至特定 Transact-SQL 陳述式的參考主題。 若要執行這項操作，請反白顯示 Transact-SQL 陳述式的名稱，然後選取 F1。 接著，說明搜尋引擎會搜尋具有符合您反白顯示的字串之 F1 說明屬性的主題。

如果說明搜尋引擎找不到具有完全符合您反白顯示的字串之 F1 說明關鍵字的主題，則會顯示此主題。 在這種情況下，有兩種方法可以找到您正在尋找的說明：

- 複製您反白顯示的編輯器字串，並將它貼入《SQL Server 線上叢書》的 [搜尋] 索引標籤，然後進行搜尋。

- 僅反白顯示 Transact-SQL 陳述式可能符合套用至主題之 F1 說明關鍵字的部分，然後再次選取 F1。 搜尋引擎要求您反白顯示的字串與指派給主題的 F1 說明關鍵字完全相符。 如果您反白顯示的字串包含環境獨有的元素 (例如資料行或參數名稱)，搜尋引擎將不會產生符合的結果。 反白顯示的字串範例包括：

  - Transact-SQL 陳述式的名稱，例如 SELECT、CREATE DATABASE 或 BEGIN TRANSACTION。

  - 內建函數的名稱，例如 SERVERPROPERTY 或 @@VERSION。

  - 系統預存程序資料表的名稱，或是檢視，例如 sys.data_spaces 或 sp_tableoption。

## <a name="editor-tasks"></a>編輯器工作

| 工作描述 | 主題 |
|------------------|-------|
| 說明您可以在 SSMS 中開啟編輯器的各種方式。| [開啟編輯器](../scripting/open-an-editor-sql-server-management-studio.md) |
| 設定各種編輯器的選項，例如行號和 IntelliSense 選項。 | [設定編輯器](../scripting/configure-editors-sql-server-management-studio.md) |
| 如何管理檢視模式，例如自動換行、分割視窗或索引標籤。| [管理編輯器和檢視模式](../scripting/manage-the-editor-and-view-mode.md) |
| 設定格式選項，例如隱藏的文字或縮排。 | [管理程式碼格式設定](../scripting/manage-code-formatting.md) |
| 使用累加搜尋或移至等功能，在編輯器視窗中瀏覽文字。 | [導覽程式碼與文字](../scripting/navigate-code-and-text.md) |
| 設定各種語法類別的色彩編碼選項，以更容易讀取複雜的陳述式。 | [查詢編輯器中的色彩編碼](../scripting/color-coding-in-query-editors.md) |
| 將文字從指令碼的一個位置拖放至新位置。| [拖放文字](../scripting/drag-and-drop-text.md) |
| 如何設定書籤，以便更容易尋找重要的程式碼片段。 | [管理書籤](../scripting/manage-bookmarks.md) |
| 如何列印視窗或方格中的指令碼或結果。| [列印程式碼與結果](../scripting/print-code-and-results.md) |
| 檢視及使用 MDX 查詢編輯器的基本功能。 | [建立 Analysis Services 指令碼](/analysis-services/instances/create-analysis-services-scripts-in-management-studio) |
| 檢視及使用 DMX 查詢編輯器的基本功能。 | [建立 DMX 查詢](/analysis-services/data-mining/create-a-dmx-query-in-sql-server-management-studio) |
| 檢視及使用 XML/A 編輯器中的基本功能。 | [XML 編輯器](../scripting/xml-editor-sql-server-management-studio.md) |
| 如何使用資料庫引擎查詢編輯器中的 sqlcmd 功能。| [編輯 SQLCMD 指令碼](../scripting/edit-sqlcmd-scripts-with-query-editor.md) |
| 如何在資料庫引擎查詢編輯器中使用程式碼片段。 程式碼片段是常用陳述式或區塊的範本，可自訂或擴充成包含網站專屬的程式碼片段。| [T-SQL 程式碼片段](../scripting/add-transact-sql-snippets.md) |
| 如何使用 Transact\-SQL 偵錯工具逐步執行程式碼，以及檢視偵錯資訊 (例如變數和參數中的值)。| [T-SQL 偵錯工具](../scripting/transact-sql-debugger.md) |

## <a name="see-also"></a>另請參閱

- [自訂功能表與快速鍵](../customize-menus-and-shortcut-keys.md)
- [SQL Server Management Studio 鍵盤快速鍵](../../ssms/sql-server-management-studio-keyboard-shortcuts.md)