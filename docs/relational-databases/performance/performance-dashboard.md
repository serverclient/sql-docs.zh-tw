---
title: 效能儀表板 | Microsoft Docs
description: 了解 SQL Server Management Studio 效能儀表板，該儀表板會提供 SQL Server 與 Azure SQL 受控執行個體的快速見解。
ms.custom: ''
ms.date: 11/13/2020
ms.prod: sql
ms.reviewer: wiassaf
ms.technology: performance
ms.topic: conceptual
helpviewer_keywords:
- performance dashboard [SQL Server]
- performance dashboard reports
- perf dashboard
ms.assetid: 07f8f594-75b4-4591-8c29-d63811d7753e
author: pelopes
ms.author: pelopes
manager: amitban
ms.openlocfilehash: ef87d8cdfb44d36faff5e06cfdc0b0f691142c03
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98170400"
---
# <a name="performance-dashboard"></a>效能儀表板
[!INCLUDE [SQL Server SQL MI](../../includes/applies-to-version/sql-asdbmi.md)]

[!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 17.2 版和更新版本包含 [效能儀表板]。 此儀表板的設計目的是要透過視覺方式提供 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssKatmai](../../includes/ssKatmai-md.md)] 開始) 和 [!INCLUDE[ssazure_md](../../includes/ssazure_md.md)] 受控執行個體效能狀態的快速見解。 

[效能儀表板] 可協助您快速識別出 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 或 [!INCLUDE[ssazure_md](../../includes/ssazure_md.md)] 是否正發生效能瓶頸。 如果發現瓶頸，將可輕鬆擷取對解決問題而言，可能必要的額外診斷資料。 [效能儀表板] 可協助識別的一些常見效能問題包括：
-  CPU 瓶頸 (以及何種查詢耗用最多 CPU)
-  I/O 瓶頸 (以及哪些查詢執行最多 I/O)
-  「查詢最佳化工具」所產生的索引建議 (遺漏索引)
-  封鎖
-  資源爭用 (包括閂鎖競爭)

[效能儀表板] 也可協助識別以前可能已執行過之佔用大量資源的查詢，並有數個計量可供用來定義高成本：CPU、邏輯寫入次數、邏輯讀取次數、持續時間、實體讀取次數及 CLR 時間。

[效能儀表板] 分成下列幾個區段和子報表：
-  系統 CPU 使用率
-  目前正在等候的要求
-  目前活動
   -  使用者要求
   -  使用者工作階段
   -  快取點擊率
-  歷程記錄資訊
   -  等候
   -  閂鎖
   -  I/O 統計資料
   -  佔用大量資源的查詢
- 其他資訊
  -  使用中的追蹤
  -  使用中的 Xevent 工作階段
  -  資料庫
  -  遺漏索引

> [!NOTE] 
> 就內部而言，[效能儀表板] 會使用[執行](../../relational-databases/system-dynamic-management-views/execution-related-dynamic-management-views-and-functions-transact-sql.md)[索引](../../relational-databases/system-dynamic-management-views/index-related-dynamic-management-views-and-functions-transact-sql.md) 及 [I/O](../../relational-databases/system-dynamic-management-views/i-o-related-dynamic-management-views-and-functions-transact-sql.md) 相關的「動態管理檢視」(DMV) 和「動態管理函數」(DMF)。

## <a name="to-view-the-performance-dashboard"></a>檢視效能儀表板 
  
若要檢視 [效能儀表板]，請在 [物件總管] 中的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體名稱上按一下滑鼠右鍵，選取 [報表]、[標準報表]，然後按一下 [效能儀表板]。  
  
![功能表中的效能儀表板](../../relational-databases/performance/media/perf_dashboard_ssms.png "功能表中的效能儀表板")  
  
[效能儀表板] 會顯示成新的索引標籤。以下是一個明顯有 CPU 瓶頸存在的範例：  
  
![主畫面中的效能儀表板](../../relational-databases/performance/media/perf_dashboard.png "主畫面中的效能儀表板")  
  
## <a name="remarks"></a>備註
[遺漏索引] 報表顯示「查詢最佳化工具」在查詢編譯期間所識別出的可能遺漏索引。 不過，不應該全然採信這些建議。 Microsoft 建議應該針對分數超過 100,000 的索引評估是否需要建立，因為這些索引最有可能大幅改善使用者查詢效能。 

> [!TIP]
> 請一律評估新索引建議是否與相同資料表中現有的索引相當，亦即是否只需變更現有的索引而無需建立新索引，即可達成相同的實際效果。 例如，假設有針對資料行 C1、C2 及 C3 的新建議索引，請先評估資料行 C1 和 C2 是否有現有的索引。 如果是，則建議直接在現有的索引中新增資料行 C3 (保留既有資料行的順序)，以避免建立新的索引。
> 如需詳細資訊，請參閱[索引架構和設計指南](../../relational-databases/sql-server-index-design-guide.md)。

[等候] 報表會篩選掉所有閒置和睡眠等候。 如需有關等候的詳細資訊，請參閱 [sys.dm_os_wait_stats &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql.md) 和[使用等候和佇列來調整 SQL Server 2005 效能](https://download.microsoft.com/download/4/7/a/47a548b9-249e-484c-abd7-29f31282b04d/performance_tuning_waits_queues.doc) \(英文\)。

[佔用大量資源的查詢] 報表在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 重新啟動時會重設，因為系統會清除基礎 DMV 中的資料。 從 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 開始，可在 [查詢存放區] 中找到有關佔用大量資源查詢的詳細資訊。 


> [!NOTE]
> [效能儀表板] 一開始是以 [SQL Server 2005](https://techcommunity.microsoft.com/t5/SQL-Server-Support/SQL-Server-2005-Performance-Dashboard-Reports/ba-p/315415) 的獨立下載項目形式發行，之後再針對 [SQL Server 2012](https://www.microsoft.com/download/details.aspx?id=29063) 進行更新。 雖然 SQL Server Management Studio 報表轉譯器不支援剪貼簿存取報表內包含的文字，但是可以透過獨立報表存取文字。  如果您需要複製報表中的查詢文字，請下載獨立報表。

## <a name="permissions"></a>權限  
在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 上，需要 `VIEW SERVER STATE` 與 `ALTER TRACE` 權限。 在 [!INCLUDE[ssazure_md](../../includes/ssazure_md.md)] 上，需要資料庫中的 `VIEW DATABASE STATE` 權限。

## <a name="see-also"></a>另請參閱  
 [效能的監視與微調](../../relational-databases/performance/monitor-and-tune-for-performance.md)     
 [效能監視及微調工具](../../relational-databases/performance/performance-monitoring-and-tuning-tools.md)     
 [開啟活動監視器 &#40;SQL Server Management Studio&#41;](../../relational-databases/performance-monitor/open-activity-monitor-sql-server-management-studio.md)     
 [活動監視器](../../relational-databases/performance-monitor/activity-monitor.md)     
 [使用查詢存放區監視效能](../../relational-databases/performance/monitoring-performance-by-using-the-query-store.md)     
