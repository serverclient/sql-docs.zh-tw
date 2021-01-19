---
title: 即時查詢統計資料 | Microsoft Docs
description: 了解如何在 SQL Server Management Studio 中，檢視使用中查詢的即時執行計畫。 使用執行統計資料針對查詢效能問題進行偵錯。
ms.custom: ''
ms.date: 11/21/2018
ms.prod: sql
ms.reviewer: ''
ms.technology: performance
ms.topic: conceptual
helpviewer_keywords:
- query statistics [SQL Server] live query stats
- live query statistics
- debugging [SQL Server], live query stats
- statistics [SQL Server], live query statistics
- query profiling
- lightweight query profiling
- lightweight profiling
ms.assetid: 07f8f594-75b4-4591-8c29-d63811d7753e
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: ee989011b8e5cf6092fdabd9ee0afcd1a06e0e58
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98171370"
---
# <a name="live-query-statistics"></a>即時查詢統計資料
[!INCLUDE [SQL Server Azure SQL Database](../../includes/applies-to-version/sql-asdb.md)]

[!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 可供檢視作用中查詢的即時執行計畫。 這個即時查詢計畫會隨著控制項在[查詢計畫運算子](../../relational-databases/showplan-logical-and-physical-operators-reference.md)之間流動，提供查詢執行程序的即時深入資訊。 即時查詢計畫會顯示整體的查詢進度，以及運算子層級的執行階段執行統計資料，如產生的資料列數目、耗用時間、運算子進度等等。因為這份資料是即時提供，不需要等待查詢完成，所以這些執行統計資料在偵錯查詢效能問題方面非常有用。 從 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] [!INCLUDE[ssManStudio](../../includes/ssmanstudio-md.md)] 開始即提供這項功能，但這項功能也可以搭配 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 使用。  

> [!NOTE]
> 在內部，即時查詢統計資料會利用 [sys.dm_exec_query_profiles](../../relational-databases/system-dynamic-management-views/sys-dm-exec-query-profiles-transact-sql.md) DMV。
  
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 起) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。  
  
> [!WARNING]  
> 這項功能主要用在疑難排解。 使用這項功能不會過度降低整體查詢效能，尤其在 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 中。 如需詳細資訊，請參閱[查詢分析基礎結構](../../relational-databases/performance/query-profiling-infrastructure.md)。  
> 這項功能可以搭配 [TRANSACT-SQL 偵錯工具](../../ssms/scripting/configure-firewall-rules-before-running-the-tsql-debugger.md)使用。  
  
## <a name="to-view-live-query-statistics-for-one-query"></a>檢視某項查詢的即時查詢統計資料 
  
1.  若要檢視即時查詢執行計劃，請在 [工具] 功能表上按一下 **包含即時查詢統計資料** 圖示。  
  
     ![工具列上的 [即時查詢統計資料] 按鈕](../../relational-databases/performance/media/livequerystatstoolbar.png "工具列上的 [即時查詢統計資料] 按鈕")  
  
     您也可以用滑鼠右鍵按一下 [!INCLUDE[ssManStudio](../../includes/ssmanstudio-md.md)] 中選取的查詢來檢視存取即時查詢執行計畫，然後按一下 [包含即時查詢統計資料]。  
  
     ![快顯功能表上的 [即時查詢統計資料] 按鈕](../../relational-databases/performance/media/livequerystatsmenu.png "快顯功能表上的 [即時查詢統計資料] 按鈕")  
  
2.  現在執行查詢。 即時查詢計劃會顯示查詢計劃運算子的整體查詢進度，以及執行階段的執行統計資料 (例如已耗用時間、進度等)。 在進行查詢執行時，會定期更新查詢進度資訊和執行統計資料。 使用這項資訊來了解整體的查詢執行處理程序，以及偵錯長時間執行的查詢、無限期執行的查詢、會造成 tempdb 溢位的查詢和逾時問題。  
  
     ![執行程序表中的 [即時查詢統計資料] 按鈕](../../relational-databases/performance/media/livequerystatsplan.png "執行程序表中的 [即時查詢統計資料] 按鈕")  
  
## <a name="to-view-live-query-statistics-for-any-query"></a>檢視任何查詢的即時查詢統計資料 

以滑鼠右鍵按一下 [處理序] 或 [使用中的費時查詢] 資料表中的任一查詢，也可以從 [[活動監視器]](../../relational-databases/performance-monitor/activity-monitor.md) 存取即時執行計劃。  
  
 ![活動監視器中的 [即時查詢統計資料] 按鈕](../../relational-databases/performance/media/livequerystatsactmon.png "活動監視器中的 [即時查詢統計資料] 按鈕")  
  
## <a name="remarks"></a>備註  
 必須先啟用統計資料設定檔基礎結構，即時查詢統計資料才可以擷取查詢進度資訊。 視版本而定，額外負荷可能十分可觀。 如需此額外負荷的詳細資訊，請參閱[查詢分析基礎結構](../../relational-databases/performance/query-profiling-infrastructure.md)。
  
## <a name="permissions"></a>權限  
填入 [即時查詢統計資料] 結果頁面需要資料庫層級的 `SHOWPLAN` 權限，而執行查詢則需要任何必要權限。
在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 上，需要伺服器層級的 `VIEW SERVER STATE` 權限才能查看即時統計資料。  
在 [!INCLUDE[ssSDS](../../includes/sssds-md.md)] 進階層上，需要資料庫的 `VIEW DATABASE STATE` 權限才能查看即時統計資料。 在 [!INCLUDE[ssSDS](../../includes/sssds-md.md)] 標準和基本層上，需要 **伺服器管理員** 或 **Azure Active Directory 系統管理員** 帳戶，才能查看即時統計資料。
  
## <a name="see-also"></a>另請參閱  
 [執行計畫](../../relational-databases/performance/execution-plans.md)    
 [查詢處理架構指南](../../relational-databases/query-processing-architecture-guide.md)    
 [效能的監視與微調](../../relational-databases/performance/monitor-and-tune-for-performance.md)     
 [效能監視及微調工具](../../relational-databases/performance/performance-monitoring-and-tuning-tools.md)     
 [開啟活動監視器 &#40;SQL Server Management Studio&#41;](../../relational-databases/performance-monitor/open-activity-monitor-sql-server-management-studio.md)     
 [活動監視器](../../relational-databases/performance-monitor/activity-monitor.md)     
 [相關檢視、函數與程序](../../relational-databases/performance/monitoring-performance-by-using-the-query-store.md)     
 [sys.dm_exec_query_statistics_xml](../../relational-databases/system-dynamic-management-views/sys-dm-exec-query-statistics-xml-transact-sql.md)     
 [sys.dm_exec_query_profiles](../../relational-databases/system-dynamic-management-views/sys-dm-exec-query-profiles-transact-sql.md)     
 [追蹤旗標](../../t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql.md)    
 [執行程序邏輯和實體運算子參考](../../relational-databases/showplan-logical-and-physical-operators-reference.md)     
 [查詢分析基礎結構](../../relational-databases/performance/query-profiling-infrastructure.md)