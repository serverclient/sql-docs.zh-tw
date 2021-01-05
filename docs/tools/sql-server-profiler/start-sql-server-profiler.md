---
title: 執行 SQL Server Profiler
titleSuffix: SQL Server Profiler
description: 了解您可以從中啟動 SQL Server Profiler 的程式與功能表，以及搭配追蹤輸出使用的連線內容、範本與篩選。
ms.prod: sql
ms.prod_service: sql-tools
ms.reviewer: ''
ms.technology: profiler
ms.topic: conceptual
ms.assetid: 22e57ffa-63b0-4de3-b92e-df297dda1226
author: markingmyname
ms.author: maghan
ms.custom: seo-lt-2019
ms.date: 07/07/2017
ms.openlocfilehash: 09a6d7f612e9a66ad98849ec1c7672b44387d43e
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97641918"
---
# <a name="run-sql-server-profiler"></a>執行 SQL Server Profiler

 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

您可以使用多種不同的方式來執行 [!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)]，以支援在各種狀況中收集追蹤輸出。 您可以從 Windows 10 的 [開始] 功能表、[!INCLUDE[ssDE](../../includes/ssde-md.md)] Tuning Advisor 的 [工具] 功能表，以及 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 的數個位置啟動 [!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)]。  
  
當您第一次啟動 [!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)] ，且從 [檔案] 功能表中選取 [新增追蹤] 時，應用程式會顯示一個 [連線到伺服器] 對話方塊，您可在此指定要連線的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體。  
## <a name="to-start-sql-server-profiler-from-the-windows-10-start-menu"></a>從 Windows 10 的 [開始] 功能表啟動 SQL Server Profiler  
-  按一下 Windows **開始** 圖示或按 Windows 鍵，然後鍵入 "SQL Server Profiler 17"。 按一下出現的 [SQL Server Profiler 17] 磚。   

## <a name="to-start-sql-server-profiler-in-database-engine-tuning-advisor"></a>在 Database Engine Tuning Advisor 中啟動 SQL Server Profiler  
-  在 [!INCLUDE[ssDE](../../includes/ssde-md.md)] Tuning Advisor 的 **[工具]** 功能表上，按一下 **[SQL Server Profiler]** 。  

## <a name="to-start-sql-server-profiler-in-sql-server-management-studio"></a>若要在 SQL Server Management Studio 中啟動 SQL Server Profiler  
 您可以從 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 中的數個位置啟動 [!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)]。 當 [!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)] 啟動時，它會載入連線內容、追蹤範本及其啟動點的篩選內容。 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 會在自己的執行個體中啟動每個 SQL Server Profiler 工作階段，即便在您關閉 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 後 Profiler 仍會繼續執行。  
### <a name="to-start-sql-server-profiler-from-the-tools-menu"></a>從工具功能表啟動 SQL Server Profiler  
-  在 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] [工具] 功能表中，按一下 [SQL Server Profiler]。  

### <a name="to-start-sql-server-profiler-from-the-query-editor"></a>從查詢編輯器啟動 SQL Server Profiler  
- 在查詢編輯器中按一下滑鼠右鍵，然後選取 [在 SQL Server Profiler 中追蹤查詢]。  

  > [!NOTE]  
  >  連接內容是編輯器連接、追蹤範本為 TSQL_SPs，而套用的篩選為 SPID = 查詢視窗 SPID。  
    
### <a name="to-start-sql-server-profiler-from-activity-monitor"></a>若要從活動監視器啟動 SQL Server Profiler  
- 在活動監視器中按一下 [處理序] 窗格，以滑鼠右鍵按一下您要分析的處理序，然後按一下 [在 SQL Server Profiler 中追蹤處理序]。  

    > [!NOTE]  
    >  選取處理序時，如果有開啟 [活動監視器]，則連接內容為 [物件總管] 連接。 追蹤範本是以伺服器類型為基礎的預設值，而且 SPID 等於所選處理序的 SPID。  
    
## <a name="net-framework-security"></a>.NET Framework 安全性  
- 在 Windows 驗證模式中，執行 [!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)] 的使用者帳戶必須有連線到 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體的權限。  
- 若要利用 [!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)]來執行追蹤，使用者也必須有 ALTER TRACE 權限。  

## <a name="next-steps"></a>後續步驟  
 [SQL Server Profiler 概觀](../../tools/sql-server-profiler/sql-server-profiler.md)   
 [使用 SQL Server Management Studio](../../ssms/sql-server-management-studio-ssms.md)