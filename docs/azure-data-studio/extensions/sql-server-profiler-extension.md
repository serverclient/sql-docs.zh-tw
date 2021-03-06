---
title: SQL Server Profiler 延伸模組
description: 了解如何安裝和使用 SQL Server Profiler 延伸模組。 容易使用的 SQL Server 追蹤解決方案，類似於 SSMS Profiler。
ms.prod: azure-data-studio
ms.technology: azure-data-studio
ms.topic: conceptual
author: yualan
ms.author: alayu
ms.reviewer: maghan, sstein
ms.custom: seodec18
ms.date: 09/24/2018
ms.openlocfilehash: c76e4cf15edcfeb6cb25b9d205f79ba34386e8d0
ms.sourcegitcommit: cc23d8646041336d119b74bf239a6ac305ff3d31
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/23/2020
ms.locfileid: "91123032"
---
# <a name="sql-server-profiler-extension-preview"></a>SQL Server Profiler 延伸模組 (預覽)

SQL Server Profiler 延伸模組 (預覽) 提供簡單的 SQL Server 追蹤解決方案，其類似 SQL Server Management Studio (SSMS) Profiler 但是使用擴充事件所建置。 SQL Server Profiler 非常容易使用，並具有適合大多數常見追蹤設定的良好預設值。 其 UX 已針對瀏覽事件和檢視關聯的 Transact-SQL (T-SQL) 文字進行最佳化。 適用於 Azure Data Studio 的 SQL Server Profiler 也假設有良好的預設值，可透過便於使用的 UX 收集 T-SQL 執行活動。 此延伸模組目前為預覽狀態。

**常見的 SQL Profiler 使用案例：**

- 逐步執行問題查詢來找出問題原因。
- 尋找和診斷執行速度很慢的查詢。
- 擷取造成問題的 Transact-SQL 陳述式系列。
- 監視 SQL Server 的效能，從而調整工作負載。
- 將效能計數器相互關聯以診斷問題。

## <a name="install-the-sql-server-profiler-extension"></a>安裝 SQL Server Profiler 延伸模組

1. 若要開啟延伸模組管理員並存取可用的延伸模組，請選取延伸模組圖示，或選取 [檢視]  功能表中的 [延伸模組]  。
2. 選取可用的延伸模組，檢視詳細資料。

    ![Profiler 延伸模組管理員](media/sql-server-profiler-extension/profiler-extension.png)

3. 選取您想要的延伸模組並加以**安裝**。
4. 選取 [重新載入]  ，啟用此延伸模組 (只有當您第一次安裝延伸模組時才需要)。

## <a name="start-profiler"></a>啟動 Profiler

1. 若要啟動 Profiler，請先在 [伺服器] 索引標籤中建立伺服器連線。
2. 建立連線之後，請鍵入 **Alt + P**，啟動 Profiler。
3. 若要啟動 Profiler，請鍵入 **Alt + S**。您現在可以開始查看擴充事件。

    ![檢視分析工具](media/sql-server-profiler-extension/view-profiler.png)

4. 若要停止 Profiler，請鍵入 **Alt + S**。此快速鍵是切換鍵。

## <a name="next-steps"></a>後續步驟

若要深入了解 Profiler 和擴充事件，請參閱[擴充事件](../../relational-databases/extended-events/extended-events.md)。