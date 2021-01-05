---
title: 啟動及使用 Database Engine Tuning Advisor | Microsoft Docs
description: 了解如何在 SQL Server 中啟動及使用 Database Engine Tuning Advisor，以建立工作負載、微調資料庫及建立 XML 輸入檔。
ms.custom: ''
ms.date: 01/09/2017
ms.prod: sql
ms.reviewer: ''
ms.technology: performance
ms.topic: conceptual
f1_keywords:
- sql13.dta.workload.f1
- sql13.dta.general.f1
- sql13.dta.advancedtuningoptions.f1
- sql13.dta.tuningoptions.f1
- sql13.dta.progress.f1
- sql13.dta.options.f1
helpviewer_keywords:
- Database Engine Tuning Advisor [SQL Server], starting
ms.assetid: a4e3226a-3917-4ec8-bdf0-472879d231c9
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 9bf93bf9b59df77efe905a6c0debb722c63c3bbb
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97643037"
---
# <a name="start-and-use-the-database-engine-tuning-advisor"></a>啟動及使用 Database Engine Tuning Advisor
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
  此主題描述如何在 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)]中啟動及使用 Database Engine Tuning Advisor。 如需如何在微調資料庫後檢視及處理結果的資訊，請參閱 [檢視及處理 Database Engine Tuning Advisor 的輸出](../../relational-databases/performance/view-and-work-with-the-output-from-the-database-engine-tuning-advisor.md)。  
  
##  <a name="initialize-the-database-engine-tuning-advisor"></a><a name="Initialize"></a> 初始化 Database Engine Tuning Advisor  
 在第一次使用時， **系統管理員 (sysadmin)** 固定伺服器角色的成員使用者必須初始化 Database Engine Tuning Advisor。 原因是必須在 **msdb** 資料庫中建立數個系統資料表，以支援微調作業。 初始化也可讓屬於 **db_owner** 固定資料庫角色成員的使用者，微調他們所擁有資料庫中資料表的工作負載。  
  
 具有系統管理員權限的使用者必須執行下列其中一種動作：  
  
-   使用 Database Engine Tuning Advisor 圖形化使用者介面連接到 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)]執行個體。 如需詳細資訊，請參閱本主題稍後的＜ [啟動 Database Engine Tuning Advisor](#Start) ＞。  
  
-   使用 **dta** 公用程式微調第一個工作負載。 如需詳細資訊，請參閱本主題稍後的＜ [使用 dta 公用程式](#dta) ＞。  
  
##  <a name="start-the-database-engine-tuning-advisor"></a><a name="Start"></a> 啟動 Database Engine Tuning Advisor  
 您可以利用若干不同方法來啟動 Database Engine Tuning Advisor 圖形化使用者介面 (GUI)，以支援各種狀況中的資料庫微調。 啟動 Database Engine Tuning Advisor 的不同方式包括：從 **[開始]** 功能表、從 **中的** [工具] [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)]功能表、從 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)]中的查詢編輯器，以及從 **中的** [工具] [!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)]功能表。 當您第一次啟動 Database Engine Tuning Advisor 時，應用程式會顯示一個 **[連接到伺服器]** 對話方塊，供您指定要連接的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體。  
  
> [!WARNING]  
>  當 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 在執行單一使用者模式時，請勿啟動 Database Engine Tuning Advisor。 如果伺服器在單一使用者模式，嘗試啟動 Database Engine Tuning Advisor 會傳回錯誤，它將不會啟動。 如需單一使用者模式的詳細資訊，請參閱 [以單一使用者模式啟動 SQL Server](../../database-engine/configure-windows/start-sql-server-in-single-user-mode.md)。  
  
#### <a name="to-start-database-engine-tuning-advisor-from-the-windows-start-menu"></a>從 Windows 的 [開始] 功能表中啟動 Database Engine Tuning Advisor  
  
1.  在 **[開始]** 功能表上，依序指向 **[所有程式]** 、 **[Microsoft SQL Server]** 、 **[效能工具]** ，然後按一下 **[Database Engine Tuning Advisor]** 。  
  
#### <a name="to-start-the-database-engine-tuning-advisor-in-sql-server-management-studio"></a>若要在 SQL Server Management Studio 中啟動 Database Engine Tuning Advisor  
  
1.  在 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 的 [工具] 功能表上，按一下 [Database Engine Tuning Advisor]。  
  
#### <a name="to-start-the-database-engine-tuning-advisor-from-the-sql-server-management-studio-query-editor"></a>若要從 SQL Server Management Studio 查詢編輯器中啟動 Database Engine Tuning Advisor  
  
1.  在 [!INCLUDE[tsql](../../includes/tsql-md.md)] 中，開啟一個 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)]指令碼檔案。 如需詳細資訊，請參閱[查詢與文字編輯器 &#40;SQL Server Management Studio&#41;](../../ssms/f1-help/database-engine-query-editor-sql-server-management-studio.md)。  
  
2.  選取 [!INCLUDE[tsql](../../includes/tsql-md.md)] 指令碼中的一項查詢，或選取整份指令碼，以滑鼠右鍵按一下選取範圍，選擇 [在 Database Engine Tuning Advisor 中分析查詢]。 此時會開啟 Database Engine Tuning Advisor GUI，且會將指令碼匯入來作為一份 XML 檔工作負載。 您可以指定工作階段名稱和微調選項來微調做為工作負載的所選 [!INCLUDE[tsql](../../includes/tsql-md.md)] 查詢。  
  
#### <a name="to-start-the-database-engine-tuning-advisor-in-sql-server-profiler"></a>若要在 SQL Server Profiler 中啟動 Database Engine Tuning Advisor  
  
1.  在 SQL Server Profiler 的 **[工具]** 功能表上，按一下 **[Database Engine Tuning Advisor]** 。  
  
##  <a name="create-a-workload"></a><a name="Create"></a> 建立工作負載  
 工作負載是針對需要微調的一或多個資料庫來執行的一組 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式。 Database Engine Tuning Advisor 會分析這些工作負載，以建議可改善伺服器查詢效能的索引或資料分割策略。  
  
 您可以使用下列其中一個方法，建立新的工作負載。  
  
-   使用[查詢存放區](../../relational-databases/performance/how-query-store-collects-data.md)作為工作負載。 利用此操作，您可以不必手動建立工作負載。 如需詳細資訊，請參閱[使用查詢存放區的工作負載微調資料庫](../../relational-databases/performance/tuning-database-using-workload-from-query-store.md)。  

      ||  
      |-|  
      |**適用對象**：[!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 及更新版本。|  

  
-   使用計畫快取做為工作負載。 利用此操作，您可以不必手動建立工作負載。 如需詳細資訊，請參閱本主題稍後的＜ [微調資料庫](#Tune) ＞。  
  
-   使用 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 中的 [查詢編輯器] 或您慣用的文字編輯器，可手動建立 [!INCLUDE[tsql](../../includes/tsql-md.md)] 指令碼工作負載。  
  
-   使用 [!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)] 建立追蹤檔案或追蹤資料表工作負載  
  
    > [!NOTE]  
    >  使用追蹤資料表做為工作負載時，該資料表必須位於 Database Engine Tuning Advisor 所微調的同一部伺服器上。 若您在不同的伺服器上建立追蹤資料表，請將其移至 Database Engine Tuning Advisor 正在微調的伺服器。  
  
-   工作負載亦可內嵌於 XML 輸入檔中，您也可於該檔中指定各事件的加權。 如需有關指定內嵌工作負載的詳細資訊，請參閱本主題稍後的＜ [建立 XML 輸入檔](#XMLInput) ＞。  
  
###  <a name="to-create-transact-sql-script-workloads"></a><a name="SSMS"></a> 建立 Transact-SQL 指令碼工作負載  
  
1.  在 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)]中啟動 [查詢編輯器]。 如需詳細資訊，請參閱[查詢與文字編輯器 &#40;SQL Server Management Studio&#41;](../../ssms/f1-help/database-engine-query-editor-sql-server-management-studio.md)。  
  
2.  在 [查詢編輯器] 中輸入您的 [!INCLUDE[tsql](../../includes/tsql-md.md)] 指令碼。 此指令碼應包含一組 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式，可針對您要微調的資料庫來執行。  
  
3.  請用 **.sql** 副檔名儲存檔案。 Database Engine Tuning Advisor GUI 及命令列 **dta** 公用程式，都可使用此 [!INCLUDE[tsql](../../includes/tsql-md.md)] 指令碼作為工作負載。  
  
###  <a name="to-create-trace-file-and-trace-table-workloads"></a><a name="Profiler"></a> 建立追蹤檔及追蹤資料表工作負載  
  
1.  使用下列其中一種方法啟動 [!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)] ：  
  
    -   在 **[開始]** 功能表中，依序指向 **[所有程式]** 、 **[Microsoft SQL Server]** 與 **[效能工具]** ，然後按一下 **[SQL Server Profiler]** 。  
  
    -   在 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)]中，按一下 **[工具]** 功能表，然後按一下 **[SQL Server Profiler]** 。  
  
2.  依下列程序，使用 [!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)] [微調] 範本來建立追蹤檔案或資料表：  
  
    -   [建立追蹤 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/create-a-trace-sql-server-profiler.md)  
  
    -   [將追蹤結果儲存至檔案 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/save-trace-results-to-a-file-sql-server-profiler.md)  
  
         Database Engine Tuning Advisor 會假設工作負載追蹤檔案為換用檔案。 如需有關換用檔案的詳細資訊，請參閱＜ [Limit Trace File and Table Sizes](../../relational-databases/sql-trace/limit-trace-file-and-table-sizes.md)＞。  
  
    -   [將追蹤結果儲存到資料表 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/save-trace-results-to-a-table-sql-server-profiler.md)  
  
         將追蹤資料表作為工作負載使用之前，請確定已停止追蹤。  
  
 我們建議您使用 SQL Server Profiler [微調] 範本來擷取 Database Engine Tuning Advisor 的工作負載。  
  
 若您想要使用自己的範本，請確定已擷取下列追蹤事件：  
  
-   **RPC:Completed**  
  
-   **SQL:BatchCompleted**  
  
-   **SP:StmtCompleted**  
  
 您也可以使用這些追蹤事件的 **Starting** 版本， 例如 **SQL:BatchStarting**。 不過，這些追蹤事件的 **Completed** 版本包含 **Duration** 資料行，能讓 Database Engine Tuning Advisor 更有效率地微調工作負載。 Database Engine Tuning Advisor 不會微調其他類型的追蹤事件。 如需這些追蹤事件的詳細資訊，請參閱＜ [Stored Procedures Event Category](../../relational-databases/event-classes/stored-procedures-event-category.md) ＞和＜ [TSQL Event Category](../../relational-databases/event-classes/tsql-event-category.md)＞。 如需使用「SQL 追蹤」預存程序來建立追蹤檔案工作負載的資訊，請參閱[建立追蹤 &#40;Transact-SQL&#41;](../../relational-databases/sql-trace/create-a-trace-transact-sql.md)。  
  
### <a name="trace-file-or-trace-table-workloads-that-contain-the-loginname-data-column"></a>包含 LoginName 資料行的追蹤檔案或追蹤資料表工作負載  
 Database Engine Tuning Advisor 會在微調處理過程中送出「執行程序表」要求。 將包含 **LoginName** 資料行的追蹤資料表或檔案當作工作負載來使用時，Database Engine Tuning Advisor 會模擬 **LoginName** 中指定的使用者。 如果此使用者沒有 SHOWPLAN 權限，無法為追蹤所包含的陳述式執行和產生「執行程序表」，Database Engine Tuning Advisor 就不會微調這些陳述式。  
  
##### <a name="to-avoid-granting-the-showplan-permission-to-each-user-specified-in-the-loginname-column-of-the-trace"></a>若要避免將 SHOWPLAN 權限授與追蹤之 LoginName 資料行所指定的每個使用者  
  
1.  微調追蹤檔案或資料表工作負載。 如需詳細資訊，請參閱本主題稍後的＜ [微調資料庫](#Tune) ＞。  
  
2.  檢查微調記錄檔，找出因沒有適當權限而未微調的陳述式。 如需詳細資訊，請參閱 [檢視及處理 Database Engine Tuning Advisor 的輸出](../../relational-databases/performance/view-and-work-with-the-output-from-the-database-engine-tuning-advisor.md)。  
  
3.  從未微調的事件刪除 **LoginName** 資料行來建立新工作負載，然後在新的追蹤檔案或資料表中僅儲存未微調的事件。 如需從追蹤刪除資料行的詳細資訊，請參閱[指定追蹤檔案的事件及資料行 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/specify-events-and-data-columns-for-a-trace-file-sql-server-profiler.md) 或[修改現有的追蹤 &#40;Transact-SQL&#41;](../../relational-databases/sql-trace/modify-an-existing-trace-transact-sql.md)。  
  
4.  將不含 **LoginName** 資料行的工作負載重新提交給 Database Engine Tuning Advisor。  
  
 由於追蹤中沒有指定登入資訊，所以 Database Engine Tuning Advisor 將會微調新的工作負載。 如果陳述式中沒有 **LoginName** ，Database Engine Tuning Advisor 便會模擬啟動微調工作階段的使用者 ( **系統管理員** 固定伺服器角色或 **db_owner** 固定資料庫角色的成員)，來微調該陳述式。  
  
##  <a name="tune-a-database"></a><a name="Tune"></a> 微調資料庫  
 若要微調資料庫，您可以使用 Database Engine Tuning Advisor GUI 或 **dta** 公用程式。  
  
> [!NOTE]  
>  使用追蹤資料表作為 Database Engine Tuning Advisor 的工作負載之前，請確定追蹤已經停止。 Database Engine Tuning Advisor 不支援使用仍在寫入追蹤事件的追蹤資料表作為工作負載。  
  
### <a name="use-the-database-engine-tuning-advisor-graphical-user-interface"></a>使用 Database Engine Tuning Advisor 圖形化使用者介面  
 在 Database Engine Tuning Advisor GUI，您可以使用計畫快取、工作負載檔案或工作負載資料表來微調資料庫。 您可以使用 Database Engine Tuning Advisor GUI 輕鬆檢視目前微調工作階段的結果，以及上次微調工作階段的結果。 如需有關使用者介面選項的詳細資訊，請參閱本主題稍後的＜ [使用者介面描述](#UI) ＞。 如需微調資料庫後處理輸出的詳細資訊，請參閱 [檢視及處理 Database Engine Tuning Advisor 的輸出](../../relational-databases/performance/view-and-work-with-the-output-from-the-database-engine-tuning-advisor.md)。  

####  <a name="to-tune-a-database-by-using-the-query-store"></a><a name="PlanCache"></a> 使用查詢存放區微調資料庫
如需詳細資訊，請參閱[使用查詢存放區的工作負載微調資料庫](../../relational-databases/performance/tuning-database-using-workload-from-query-store.md)。
  
####  <a name="to-tune-a-database-by-using-the-plan-cache"></a><a name="PlanCache"></a> 使用計畫快取微調資料庫  
  
1.  啟動 Database Engine Tuning Advisor，登入 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]的執行個體。 如需詳細資訊，請參閱本主題前面的＜ [啟動 Database Engine Tuning Advisor](#Start) ＞。  
  
2.  在 **[一般]** 索引標籤的 **[工作階段名稱]** 中輸入名稱，以建立新的微調工作階段。 啟動微調工作階段之前，您必須設定 **[一般]** 索引標籤中的欄位。 啟動微調工作階段之前，不一定要修改 **[微調選項]** 索引標籤的設定。  
  
3.  選取 **[計畫快取]** 做為工作負載選項。 Database Engine Tuning Advisor 會選取計畫快取中前 1,000 個用於分析的事件。  
  
4.  選取一個或多個想要微調的資料庫，並選擇性地從 **[選取的資料表]** 中選擇每個資料庫中的一個或多個資料表。 若要包含所有資料庫的快取項目，請按一下 **[微調選項]** 中的 **[進階選項]** ，然後核取 **[包含來自所有資料庫的計畫快取事件]** 。  
  
5.  核取 **[儲存微調記錄]** ，以儲存微調記錄的副本。 若您不想儲存微調記錄的副本，請清除核取方塊。  
  
     分析之後，若要檢視微調記錄，您可以開啟工作階段，並選取 **[進度]** 索引標籤。  
  
6.  按一下 **[微調選項]** 索引標籤，並從所列出的選項中選取。  
  
7.  按一下 **[開始分析]** 。  
  
     如果您要在啟動之後停止微調，請選擇 **[動作]** 功能表上的下列其中一個選項：  
  
    -   [停止分析 (附帶建議)] 會停止微調工作階段並提示您確定是否要 Database Engine Tuning Advisor 根據現階段完成的分析產生建議。  
  
    -   **[停止分析]** 會停止微調工作階段而不產生任何建議。  
  
> [!NOTE]  
>  不支援暫停 Database Engine Tuning Advisor。 若在按下 [停止分析] 或 [停止分析 (附帶建議)] 工具列按鈕之後按下 [開始分析] 工具列按鈕，Database Engine Tuning Advisor 會啟動新的微調工作階段。  
  
#### <a name="to-tune-a-database-using-a-workload-file-or-table-as-input"></a>若要使用工作負載檔案或資料表做為輸入以微調資料庫  
  
1.  決定您希望 Database Engine Tuning Advisor 在分析過程中考慮加入、移除或保留的資料庫功能 (索引、索引檢視、分割)。  
  
2.  建立工作負載。 如需詳細資訊，請參閱本主題前面的＜ [建立工作負載](#Create) ＞。  
  
3.  啟動 Database Engine Tuning Advisor，並登入 [!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的執行個體。 如需詳細資訊，請參閱本主題前面的＜ [啟動 Database Engine Tuning Advisor](#Start) ＞。  
  
4.  在 **[一般]** 索引標籤的 **[工作階段名稱]** 中輸入名稱，以建立新的微調工作階段。  
  
5.  選擇 **[工作負載檔案]** 或 **[資料表]** 並輸入檔案的路徑，或在相鄰的文字方塊中輸入資料表的名稱。  
  
     指定資料表時的格式為  
  
    ```  
    database_name.schema_name.table_name  
    ```  
  
     若要搜尋工作負載檔案或資料表，請按一下 **[瀏覽]** 。 Database Engine Tuning Advisor 會假設工作負載檔案是換用檔案。 如需有關換用檔案的詳細資訊，請參閱＜ [Limit Trace File and Table Sizes](../../relational-databases/sql-trace/limit-trace-file-and-table-sizes.md)＞。  
  
     使用追蹤資料表做為工作負載時，該資料表必須位於 Database Engine Tuning Advisor 所微調的同一部伺服器上。 若您在不同的伺服器上建立追蹤資料表，請先將它移到 Database Engine Tuning Advisor 所微調的伺服器上，再用它來作為您的工作負載。  
  
6.  選取您在步驟 5 中選取要執行工作負載的資料庫與資料表。 若要選取資料表，請按一下 **[選取的資料表]** 箭頭。  
  
7.  核取 **[儲存微調記錄]** ，以儲存微調記錄的副本。 若您不想儲存微調記錄的副本，請清除核取方塊。  
  
     分析之後，若要檢視微調記錄，您可以開啟工作階段，並選取 **[進度]** 索引標籤。  
  
8.  按一下 **[微調選項]** 索引標籤，並從所列出的選項中選取。  
  
9. 按一下工具列中的 **[開始分析]** 按鈕。  
  
     如果您要在啟動之後停止微調，請選擇 **[動作]** 功能表上的下列其中一個選項：  
  
    -   [停止分析 (附帶建議)] 會停止微調工作階段並提示您確定是否要 Database Engine Tuning Advisor 根據現階段完成的分析產生建議。  
  
    -   **[停止分析]** 會停止微調工作階段而不產生任何建議。  
  
> [!NOTE]  
>  不支援暫停 Database Engine Tuning Advisor。 若在按下 [停止分析] 或 [停止分析 (附帶建議)] 工具列按鈕之後按下 [開始分析] 工具列按鈕，Database Engine Tuning Advisor 會啟動新的微調工作階段。  
  
###  <a name="use-the-dta-utility"></a><a name="dta"></a> 使用 dta 公用程式  
 [dta 公用程式](../../tools/dta/dta-utility.md) 提供一個命令提示字元可執行檔，您可用來微調資料庫。 這個公用程式可讓您在批次檔和指令碼中使用 Database Engine Tuning Advisor 的功能。 您的 **dta** 公用程式會將計畫快取項目、追蹤檔案、追蹤資料表和 [!INCLUDE[tsql](../../includes/tsql-md.md)] 指令碼視為工作負載； 它也會使用符合 Database Engine Tuning Advisor XML 結構描述的 XML 輸入，此結構描述可從此 [Microsoft 網站](https://go.microsoft.com/fwlink/?linkid=43100)取得。  
  
 開始使用 **dta** 公用程式微調工作負載之前，請先考慮下列事項：  
  
-   使用追蹤資料表做為工作負載時，該資料表必須位於 Database Engine Tuning Advisor 所微調的同一部伺服器上。 如果追蹤資料表是在不同的伺服器上建立的，請將其移動至 Database Engine Tuning Advisor 正在進行微調的伺服器。  
  
-   使用追蹤資料表作為 Database Engine Tuning Advisor 的工作負載之前，請確定追蹤已經停止。 Database Engine Tuning Advisor 不支援使用仍在寫入追蹤事件的追蹤資料表作為工作負載。  
  
-   如果微調工作階段繼續執行的時間超過您所預期的執行時間，可以按 CTRL+C 停止微調工作階段，並根據現階段完成的分析 **dta** 產生建議。 系統會提示您決定是否要產生建議。 請再按一下 CTRL+C 來停止微調工作階段，不產生建議。  
  
 如需有關 **dta** 公用程式語法和範例的詳細資訊，請參閱＜ [dta Utility](../../tools/dta/dta-utility.md)＞。  
  
#### <a name="to-tune-a-database-by-using-the-plan-cache"></a>使用計畫快取微調資料庫  
  
1.  指定 **-ip** 選項。 針對所選取資料庫排名前 1,000 個計畫快取事件進行分析。  
  
     從命令提示字元，輸入下列內容：  
  
    ```  
    dta -E -D DatabaseName -ip -s SessionName  
    ```  
  
2.  若要修改用於分析的事件數，請指定 **-n** 選項。 下列範例將快取項目數增加為 2,000。  
  
    ```  
    dta -E -D DatabaseName -ip -n 2000-s SessionName1  
    ```  
  
3.  若要分析執行個體中所有資料庫的事件，請指定 **-ipf** 選項。  
  
    ```  
    dta -E -D DatabaseName -ip -ipf -n 2000 -s SessionName2  
    ```  
  
#### <a name="to-tune-a-database-by-using-a-workload-and-dta-utility-default-settings"></a>使用工作負載和 dta 公用程式預設值來微調資料庫  
  
1.  決定您希望 Database Engine Tuning Advisor 在分析過程中考慮加入、移除或保留的資料庫功能 (索引、索引檢視、分割)。  
  
2.  建立工作負載。 如需詳細資訊，請參閱本主題前面的＜ [建立工作負載](#Create) ＞。  
  
3.  從命令提示字元，輸入下列內容：  
  
    ```  
    dta -E -D DatabaseName -if WorkloadFile -s SessionName  
    ```  
  
     其中 `-E` 指定您的微調工作階段使用信任連接 (而非登入識別碼和密碼)，而 `-D` 指定您要微調的資料庫名稱。 依預設，公用程式會連接到本機電腦上的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 預設執行個體 (使用 `-S` 選項指定遠端資料庫，如下列程序所示，或指定具名執行個體)。 `-if` 選項指定工作負載檔案 (可以是 [!INCLUDE[tsql](../../includes/tsql-md.md)] 指令碼或追蹤檔) 的名稱和路徑，而 `-s` 指定微調工作階段的名稱。  
  
     這裡顯示的四個選項 (資料庫名稱、工作負載、連接類型和工作階段名稱) 都是強制選項。  
  
#### <a name="to-tune-a-remote-database-or-a-named-instance-for-a-specific-duration"></a>若要在特定持續期間內微調遠端資料庫或具名執行個體  
  
1.  決定您希望 Database Engine Tuning Advisor 在分析過程中考慮加入、移除或保留的資料庫功能 (索引、索引檢視、分割)。  
  
2.  建立工作負載。 如需詳細資訊，請參閱本主題前面的＜ [建立工作負載](#Create) ＞。  
  
3.  從命令提示字元，輸入下列內容：  
  
    ```  
    dta -S ServerName\Instance -D DatabaseName -it WorkloadTableName   
    -U LoginID -P Password -s SessionName -A TuningTimeInMinutes  
    ```  
  
     其中 `-S` 指定遠端伺服器名稱和執行個體 (或本機伺服器上的具名執行個體)，而 `-D` 指定您要微調的資料庫名稱。 `-it` 選項指定工作負載資料表的名稱、 `-U` 和 `-P` 指定遠端資料庫的登入識別碼和密碼、 `-s` 指定微調工作階段名稱，而 `-A` 指定微調工作階段持續期間 (以分鐘為單位)。 依預設， **dta** 公用程式會使用 8 小時的微調持續時間。 如果您要讓 Database Engine Tuning Advisor 在不限制時間長度的情況下微調工作負載，請指定 **0** (零) 與 `-A` 選項。  
  
#### <a name="to-tune-a-database-using-an-xml-input-file"></a>若要使用 XML 輸入檔微調資料庫  
  
1.  決定您希望 Database Engine Tuning Advisor 在分析過程中考慮加入、移除或保留的資料庫功能 (索引、索引檢視、分割)。  
  
2.  建立工作負載。 如需詳細資訊，請參閱本主題前面的＜ [建立工作負載](#Create) ＞。  
  
3.  建立 XML 輸入檔。 如需詳細資訊，請參閱本主題稍後的＜ [建立 XML 輸入檔](#XMLInput) ＞。  
  
4.  從命令提示字元，輸入下列內容：  
  
    ```  
    dta -E -S ServerName\Instance -s SessionName -ix PathToXMLInputFile  
    ```  
  
     其中 `-E` 指定信任連接、 `-S` 指定遠端伺服器和執行個體，或者本機伺服器上的具名執行特體、 `-s` 指定微調工作階段名稱，而 `-ix` 指定要用於微調工作階段的 XML 輸入檔。  
  
5.  公用程式完成微調工作負載之後，您可以透過 Database Engine Tuning Advisor GUI 來檢視微調工作階段的結果。 或者，您也可以透過 **-ox** 選項，指定將微調建議寫入 XML 檔案。 如需詳細資訊，請參閱 [dta Utility](../../tools/dta/dta-utility.md)。  
  
##  <a name="create-an-xml-input-file"></a><a name="XMLInput"></a> 建立 XML 輸入檔  
 如果您是有經驗的 XML 開發人員，即可建立 XML 格式的檔案供 [!INCLUDE[ssDE](../../includes/ssde-md.md)] Tuning Advisor 用以微調工作負載。 若要建立這些 XML 檔案，請使用您喜好的 XML 工具來編輯範例檔，或是根據 [!INCLUDE[ssDE](../../includes/ssde-md.md)] Tuning Advisor XML 結構描述產生執行個體。  
  
 您的 [!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝中提供 [!INCLUDE[ssDE](../../includes/ssde-md.md)] Tuning Advisor XML 結構描述，其位置如下：  
  
 `C:\Program Files\Microsoft SQL Server\100\Tools\Binn\schemas\sqlserver\2004\07\dta\dtaschema.xsd` 
  
 您也可以從這個 [!INCLUDE[ssDE](../../includes/ssde-md.md)] Microsoft 網站 [，線上取得](https://go.microsoft.com/fwlink/?linkid=43100&clcid=0x409)Tuning Advisor XML 結構描述。  
  
 此 URL 可將您帶往具有許多 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] XML 結構描述的網頁。 將網頁向下捲動，直到您找到 [!INCLUDE[ssDE](../../includes/ssde-md.md)] Tuning Advisor 的資料列。  
  
#### <a name="to-create-an-xml-input-file-to-tune-workloads"></a>若要建立 XML 輸入檔來微調工作負載  
  
1.  建立工作負載。 您可以使用 [!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)]中的微調範本來使用追蹤檔案或資料表，或是建立 [!INCLUDE[tsql](../../includes/tsql-md.md)] 指令碼來重新產生 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]的代表性工作負載。 如需詳細資訊，請參閱本主題前面的＜ [建立工作負載](#Create) ＞。  
  
2.  使用下列其中一種方法來建立 XML 輸入檔：  
  
    -   將其中一個 [XML 輸入檔範例 &#40;DTA&#41;](../../tools/dta/xml-input-file-samples-dta.md)複製並貼到您喜好的 XML 編輯器中。 變更當中的值，指定適合您的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝的引數，然後儲存該 XML 檔案。  
  
    -   使用您慣用的 XML 工具，根據 [!INCLUDE[ssDE](../../includes/ssde-md.md)] Tuning Advisor 結構描述產生執行個體。  
  
3.  建立 XML 輸入檔之後，將其用為 **dta** 命令列公用程式的輸入值，以微調工作負載。 如需有關 XML 輸入檔與此公用程式一起使用的詳細資訊，請參閱本主題前面的＜ [使用 dta 公用程式](#dta) ＞。  
  
> [!NOTE]  
>  若您想要使用內嵌工作負載 (此為在 XML 輸入檔中直接指定的工作負載)，請使用範例：[含內嵌工作負載的 XML 輸入檔範例 &#40;DTA&#41;](../../tools/dta/xml-input-file-sample-with-inline-workload-dta.md)。  
  
##  <a name="user-interface-descriptions"></a><a name="UI"></a> 使用者介面描述  
  
### <a name="tools-menuoptions-page"></a>工具功能表/選項頁面  
 使用此對話方塊，來指定 Database Engine Tuning Advisor 的一般組態參數。  
  
 **啟動時**  
 指定 Database Engine Tuning Advisor 在啟動時應執行的工作：開啟但沒有資料庫連接、顯示 [新增連接] 對話方塊、顯示新的工作階段，或者載入上次載入的工作階段。  
  
 **變更字型**  
 指定 Database Engine Tuning Advisor 資料表所用的字型。  
  
 **最近使用清單中的項目數目**  
 指定在 [檔案] 功能表中的 [最近使用的工作階段] 或 [最近使用的檔案] 之下，要顯示之工作階段或檔案的數目。  
  
 **記住上次的微調選項**  
 在工作階段之間保留微調選項。 依預設為已選取。 清除此核取方塊，即可永遠使用 Database Engine Tuning Advisor 的預設值來啟動。  
  
 **永久刪除工作階段之前先詢問**  
 在刪除工作階段之前先顯示確認對話方塊。  
  
 **停止工作階段分析之前先詢問**  
 在停止分析工作負載前，先顯示確認對話方塊。  
  
#### <a name="general-tab-options"></a>一般索引標籤選項  
 啟動微調工作階段之前，您必須設定 **[一般]** 索引標籤中的欄位。 啟動微調工作階段之前，不需要修改 **[微調選項]** 索引標籤的設定。  
  
 **[工作階段名稱]**  
 指定工作階段的名稱。 工作階段名稱與微調工作階段的名稱相關聯。 您可以參考此名稱，以便稍後檢閱微調工作階段。  
  
 **檔案**  
 指定工作負載的 .sql 指令碼或追蹤檔案。 在相關聯的文字方塊中，指定路徑和檔名。 Database Engine Tuning Advisor 會假設工作負載追蹤檔案為換用檔案。 如需有關換用檔案的詳細資訊，請參閱＜ [Limit Trace File and Table Sizes](../../relational-databases/sql-trace/limit-trace-file-and-table-sizes.md)＞。  
  
 **Table**  
 指定工作負載的追蹤資料表。 指定相關聯的文字方塊中之追蹤資料表的完整限定名稱如下：  
  
```  
database_name.owner_name.table_name  
```  
  
-   將追蹤資料表作為工作負載使用之前，請確定已停止追蹤。  
  
-   追蹤資料表所在的伺服器必須和 Database Engine Tuning Advisor 正在進行微調的伺服器相同。 如果追蹤資料表是在不同的伺服器上建立的，請將其移動至 Database Engine Tuning Advisor 正在進行微調的伺服器。  
  
 **[計畫快取]**  
 指定計畫快取做為工作負載。 利用此操作，您可以不必手動建立工作負載。 Database Engine Tuning Advisor 會選取前 1,000 個用於分析的事件。  
  
 **XML**  
 除非您從 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)]匯入工作負載查詢，否則不會出現。  
  
 若要從 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)]匯入工作負載：  
  
1.  在查詢編輯器鍵入查詢，並反白顯示。  
  
2.  以滑鼠右鍵按一下反白顯示的查詢，並按一下 [在 Database Engine Tuning Advisor 中分析查詢]。  
  
 **瀏覽工作負載 [檔案或資料表]**  
 選取 [檔案] 或 [資料表] 作為工作負載來源時，請使用此瀏覽按鈕來選取目標。  
  
 **預覽 XML 工作負載**  
 檢視已從 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)]匯入的 XML 格式工作負載。  
  
 **工作負載分析的資料庫**  
 指定 Database Engine Tuning Advisor 微調工作負載時，第一個連接的資料庫。 在微調開始之後，Database Engine Tuning Advisor 會連接到工作負載包含的 `USE DATABASE` 陳述式所指定的資料庫。  
  
 **選取要微調的資料庫與資料表**  
 指定要微調的資料庫與資料表。 若要指定所有資料庫，請選取 **[名稱]** 資料行標題中的核取方塊。 若要指定某些資料庫，請選取資料庫名稱旁的核取方塊。 依預設，所有選取之資料庫的資料表會自動包含在微調工作階段中。 若要排除資料表，請按一下 **[選取的資料表]** 資料行中的箭頭，然後將您不要微調之資料表旁的核取方塊清除。  
  
 [選取的資料表] 向下箭頭  
 展開資料表清單，以允許選取個別資料表進行微調。  
  
 **[儲存微調記錄]**  
 在工作階段中建立記錄檔並記錄錯誤。  
  
> [!NOTE]  
>  Database Engine Tuning Advisor 不會自動為 **[一般]** 索引標籤上顯示的資料表，更新資料列資訊。而是依賴資料庫中的中繼資料。 如果您質疑資料列資訊已過期，請為相關物件執行 DBCC UPDATEUSAGE 命令。  
  
##### <a name="tuning-tab-options"></a>微調選項索引標籤  
 使用 **[微調選項]** 索引標籤來修改一般微調選項的預設值。 啟動微調工作階段之前，不需要修改 **[微調選項]** 索引標籤的設定。  
  
 **限制微調時間**  
 限制目前微調工作階段的時間。 提供更多微調時間以改善建議的品質。 若要確保最佳建議，請勿選取此選項。  
  
> [!NOTE]  
> [!INCLUDE[ssDE](../../includes/ssde-md.md)] Tuning Advisor 會在分析期間耗用系統資源。 預期在要微調的伺服器會有較重的工作負載期間之前，您可以使用 **[限制微調時間]** 來停止微調。  
  
 **[進階選項]**  
 使用 [進階微調選項] 對話方塊，即可設定最大空間、最大索引鍵資料行和線上索引建議。  
  
 **定義建議的最大空間 (MB)**  
 鍵入 Database Engine Tuning Advisor 所建議，由實體設計結構使用的最大空間量。  
  
 如果此處未輸入任何值，Database Engine Tuning Advisor 會假設採用下列空間限制中的較小者：  
  
-   目前的原始資料大小的三倍，其中包括資料庫中各資料表的堆積和叢集索引的總大小。  
  
-   所有相連硬碟的可用空間，加上原始資料大小。  
  
 **[包含來自所有資料庫的計畫快取事件]**  
 指定分析所有資料庫中的計畫快取事件。  
  
 **每個索引的最大資料行數**  
 指定任何索引中所包含的最大資料行數。 預設值為 1023。  
  
 **所有建議都是離線**  
 產生可能最佳的建議，但不要建議在線上建立任何實體設計結構。  
  
 **如果可能，產生線上建議**  
 建立 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式來實作建議時，即使有較快速的離線方法可以使用，也請選擇可以維持伺服器在線上的實作方法。  
  
 **只產生線上建議**  
 只提供伺服器能夠維持在線上的建議。  
  
 **停止時間**  
 提供 [!INCLUDE[ssDE](../../includes/ssde-md.md)] Tuning Advisor 應該停止的日期和時間。  
  
 **索引與索引檢視**  
 核取此方塊以包含加入叢集索引、非叢集索引以及索引檢視的建議。  
  
 **索引檢視**  
 只包含加入索引檢視的建議。 不建議叢集與非叢集索引。  
  
 **包含篩選的索引**  
 包含加入篩選之索引的建議。 如果您選取下列其中一個實體設計結構，即可使用這個選項：[索引與索引檢視]、[索引] 或 [非叢集索引]。  
  
 **索引數**  
 只包含加入叢集與非叢集索引的建議。 不建議索引檢視。  
  
 **[非叢集索引]**  
 只包含非叢集索引的建議。 不建議叢集索引與索引檢視。  
  
 **只評估現有 PDS 的使用情形**  
 評估目前索引的效能，但不建議其他索引或索引檢視。  
  
 **沒有資料分割。**  
 不建議資料分割。  
  
 **完整的資料分割**  
 包含資料分割的建議。  
  
 **對齊的資料分割**  
 將會對齊新建議的資料分割，以便於維護資料分割。  
  
 **不要保留任何現有的 PDS**  
 建議卸除不必要的現有索引、檢視和資料分割。 如果現有的實體設計結構 (PDS) 對工作負載很有用， [!INCLUDE[ssDE](../../includes/ssde-md.md)] Tuning Advisor 就不會建議將它卸除。  
  
 **只保留索引**  
 保留所有現有的索引，但建議卸除不必要的索引檢視與資料分割。  
  
 **保留所有現有的 PDS**  
 保留所有現有的索引、索引檢視以及資料分割。  
  
 **只保留叢集索引**  
 保留所有現有的叢集索引，但建議卸除不必要的索引檢視、資料分割以及非叢集索引。  
  
 **保留對齊的資料分割**  
 保留目前對齊的資料分割結構，但建議卸除不必要的索引檢視、索引以及非對齊的資料分割。 建議的任何其他資料分割將對齊目前的資料分割配置。  
  
###### <a name="progress-tab-options"></a>進度索引標籤選項  
 Database Engine Tuning Advisor 的 **[進度]** 索引標籤會在 Database Engine Tuning Advisor 開始分析工作負載之後出現。  
  
 如果您要在啟動之後停止微調，請選擇 **[動作]** 功能表上的下列其中一個選項：  
  
-   [停止分析 (附帶建議)] 會停止微調工作階段並提示您確定是否要 Database Engine Tuning Advisor 根據現階段完成的分析產生建議。  
  
-   **[停止分析]** 會停止微調工作階段而不產生任何建議。  
  
 **微調進度**  
 指出進度的目前狀態。 包含已執行的動作數目，以及錯誤、成功與接收之警告訊息的數目。  
  
 **詳細資料**  
 包含指出狀態的圖示。  
  
 **動作**  
 顯示要執行的步驟。  
  
 **狀態**  
 顯示動作步驟的狀態。  
  
 **訊息**  
 包含動作步驟所傳回的任何訊息。  
  
 **微調記錄**  
 包含此微調工作階段的相關資訊。 若要列印此記錄，請以滑鼠右鍵按一下記錄，然後按一下 [列印]。  
  
## <a name="see-also"></a>另請參閱  
 [檢視及處理 Database Engine Tuning Advisor 的輸出](../../relational-databases/performance/view-and-work-with-the-output-from-the-database-engine-tuning-advisor.md)   
 [dta 公用城市](../../tools/dta/dta-utility.md)    
 [教學課程：Database Engine Tuning Advisor](../../tools/dta/tutorial-database-engine-tuning-advisor.md)
