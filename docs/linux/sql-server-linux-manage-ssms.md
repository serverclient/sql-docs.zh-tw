---
title: 使用 SSMS 來管理 Linux 上的 SQL Server
description: 本文會介紹 SQL Server Management Studio，這是一種整合式環境，可存取、設定、管理及開發 SQL Server 的元件。
author: VanMSFT
ms.author: vanto
ms.date: 05/21/2018
ms.topic: conceptual
ms.prod: sql
ms.technology: linux
ms.assetid: b2fcf858-21c3-462a-8d49-50c85647d092
ms.openlocfilehash: f5ba3ea9260da9e278273ef39d0f5893b67c120e
ms.sourcegitcommit: d8cdbb719916805037a9167ac4e964abb89c3909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/20/2021
ms.locfileid: "98596436"
---
# <a name="use-sql-server-management-studio-on-windows-to-manage-sql-server-on-linux"></a>使用 Windows 上的 SQL Server Management Studio 來管理 Linux 上的 SQL Server

[!INCLUDE [SQL Server - Linux](../includes/applies-to-version/sql-linux.md)]

本文介紹 [SQL Server Management Studio (SSMS)](../ssms/sql-server-management-studio-ssms.md)，並逐步引導您完成幾個常見工作。 SSMS 是 Windows 應用程式，因此當您的 Windows 電腦可以連線到 Linux 遠端 SQL Server 執行個體時，請使用 SSMS。

> [!TIP]
> 如果您沒有要在其上執行 SSMS 的 Windows 電腦，請考慮使用新的 [Azure Data Studio](../azure-data-studio/index.yml)。 它提供用來管理 SQL Server 的圖形化工具，並可在 Linux 和 Windows 上執行。

[SQL Server Management Studio (SSMS)](../ssms/sql-server-management-studio-ssms.md) 是 Microsoft 針對開發和管理需求免費提供的部分 SQL 工具套件。 SSMS 是一種整合式環境，可存取、設定、管理及開發 SQL Server 的所有元件。 它可以連線到內部部署、Docker 容器和雲端中任何平台上執行的 SQL Server。 其也會連線到 Azure SQL Database 和 Azure Synapse Analytics。 SSMS 使用許多豐富指令碼編輯器來合併一群非常廣泛的圖形工具，使所有技術層級的開發人員及系統管理員都能夠存取 SQL Server。

SSMS 為 SQL Server 提供一組廣泛的開發和管理功能，包括可執行下列作業的工具：

- 設定、監視及管理一或多個 SQL Server 執行個體
- 部署、監視及升級資料層元件，例如資料庫和資料倉儲
- 備份及還原資料庫
- 建立及執行 T-SQL 查詢和指令碼並查看結果
- 產生資料庫物件的 T-SQL 指令碼
- 檢視及編輯資料庫中的資料
- 以視覺化方式設計 T-SQL 查詢和資料庫物件，例如檢視、資料表和預存程序

如需 SSMS 的相關資訊，請參閱[什麼是 SSMS？](../ssms/sql-server-management-studio-ssms.md)。

## <a name="install-the-newest-version-of-sql-server-management-studio-ssms"></a>下載最新版的 SQL Server Management Studio (SSMS)

使用 SQL Server 時，您應該一律使用最新版本的 SQL Server Management Studio (SSMS)。 最新版本的 SSMS 會持續更新和最佳化，目前可與 Linux 上的 SQL Server 搭配使用。 若要下載並安裝最新版本，請參閱[下載 SQL Server Management Studio](../ssms/download-sql-server-management-studio-ssms.md)。 為了保持最新狀態，只要有新版本可供下載時，最新版本的 SSMS 就會提示您。

> [!NOTE]
> 使用 SSMS 來管理 Linux 之前，請先檢閱 Linux 上 SSMS 的[已知問題](sql-server-linux-release-notes.md)。

## <a name="connect-to-sql-server-on-linux"></a>連線到 Linux上的 SQL Server

使用下列基本步驟來進行連線：

1. 在 Windows 搜尋方塊中鍵入 **Microsoft SQL Server Management Studio** 來啟動 SSMS，然後按一下傳統型應用程式。

    ![SQL Server Management Studio](./media/sql-server-linux-manage-ssms/ssms.png)

1. 在 [連線到伺服器] 視窗中，輸入下列資訊 (如果 SSMS 已在執行中，請按一下 [連線] > [資料庫引擎] 來開啟 [連線到伺服器] 視窗)：

   | 設定 | 描述 |
   |-----|-----|
   | **伺服器類型** | 預設值為資料庫引擎；請勿變更此值。 |
   | **伺服器名稱** | 輸入目標 Linux SQL Server 電腦的名稱，或其 IP 位址與連接埠 (以 `IP,port` 的格式)。 |
   | **驗證** | 如果是 Linux 上的 SQL Server，請使用 [SQL Server 驗證]。 |
   | **登入** | 輸入有權存取伺服器上資料庫的使用者名稱 (例如，安裝期間建立的預設 **SA** 帳戶)。 |
   | **密碼** | 輸入指定使用者的密碼 (針對 **SA** 帳戶，您在安裝期間建立了此密碼)。 |

    ![SQL Server Management Studio：連線到 SQL Database 伺服器](./media/sql-server-linux-manage-ssms/connect.png)

1. 按一下 [ **連接**]。

    > [!TIP]
    > 如果您收到連線失敗，請先嘗試從錯誤訊息診斷問題。 然後檢閱[連線疑難排解建議](sql-server-linux-troubleshooting-guide.md#connection)。
 
1. 成功連線到 SQL Server 之後，[物件總管] 隨即開啟，您現在就可以存取資料庫來執行系統管理工作或查詢資料。

## <a name="run-transact-sql-queries"></a>執行 Transact-SQL 查詢

連線到伺服器之後，您就可以連線到資料庫並執行 Transact-SQL 查詢。 Transact-SQL 查詢幾乎可用於任何資料庫工作。

1. 在 [物件總管] 中，巡覽至伺服器上的目標資料庫。 例如，展開 [系統資料庫] 以使用 **master** 資料庫。

1. 以滑鼠右鍵按一下資料庫，然後選取 [新增查詢]。

1. 在查詢視窗中，撰寫 Transact-SQL 查詢以選擇傳回伺服器上所有資料庫的名稱。

   ```sql
   SELECT [Name]
   FROM sys.Databases
   ```

   如果您不熟悉撰寫查詢，請參閱[撰寫 Transact-SQL 陳述式](../t-sql/tutorial-writing-transact-sql-statements.md)。

1. 按一下 [執行] 按鈕以執行查詢並查看結果。

   ![成功。 連線到 SQL Database 伺服器：SQL Server Management Studio](./media/sql-server-linux-manage-ssms/execute-query.png)

雖然您幾乎可以使用 Transact-SQL 查詢進行任何管理工作，但 SSMS 是一種圖形化工具，可讓您更輕鬆地管理 SQL Server。 下列各節提供使用圖形化使用者介面的一些範例。

## <a name="create-and-manage-databases"></a>建立及管理資料庫

連線到 *master* 資料庫時，您可以在伺服器上建立資料庫並修改或卸除現有的資料庫。 下列步驟描述如何透過 Management Studio 完成數個常見的資料庫管理工作。 若要執行這些工作，請確定您已使用設定 Linux 上的 SQL Server 時所建立的伺服器層級主體登入，連線到 *master* 資料庫。

### <a name="create-a-new-database"></a>建立新資料庫

1. 啟動 SSMS 並連線到您在 Linux 上 SQL Server 中的伺服器

2. 在 [物件總管] 中，以滑鼠右鍵按一下 [資料庫] 資料夾，然後按一下 [新增資料庫...]

3. 在 [新增資料庫] 對話方塊中，輸入新資料庫的名稱，然後按一下 [確定]

隨即在您的伺服器中成功建立新資料庫。 如果您想要使用 T-SQL 建立新的資料庫，請參閱 [CREATE DATABASE (SQL Server Transact-SQL)](../t-sql/statements/create-database-transact-sql.md)。

### <a name="drop-a-database"></a>卸除資料庫

1. 啟動 SSMS 並連線到您在 Linux 上 SQL Server 中的伺服器

2. 在 [物件總管] 中，展開 [資料庫] 資料夾，以查看伺服器上所有資料庫的清單。

3. 在 [物件總管] 中，以滑鼠右鍵按一下您想要卸除的資料庫，然後按一下 [刪除]

4. 在 [刪除物件] 對話方塊中，選取 [關閉現有的連線]，然後按一下 [確定]

隨即從您的伺服器成功卸除資料庫。 如果您想要使用 T-SQL 卸除資料庫，請參閱 [DROP DATABASE (SQL Server Transact-SQL)](../t-sql/statements/drop-database-transact-sql.md)。

## <a name="use-activity-monitor-to-see-information-about-sql-server-activity"></a>使用活動監視器來查看 SQL Server 活動的相關資訊

[活動監視器](../relational-databases/performance-monitor/activity-monitor.md)工具內建在 SQL Server Management Studio (SSMS) 中，並顯示 SQL Server 處理序的相關資訊，以及這些處理序如何影響目前的 SQL Server 執行個體。

1. 啟動 SSMS 並連線到您在 Linux 上 SQL Server 中的伺服器

1. 在 [物件總管] 中，以滑鼠右鍵按一下 [伺服器] 節點，然後按一下 [活動監視器]

活動監視器會顯示可展開且可摺疊的窗格，其中包含下列資訊：

- 概觀
- 處理序
- 資源等候
- 資料檔案 I/O
- 佔用大量資源的最近查詢
- 佔用大量資源的使用中查詢

展開窗格時，活動監視器會查詢執行個體以便取得相關資訊。 摺疊某個窗格時，該窗格的所有查詢活動就會停止。 您可以同時展開一或多個窗格，以便檢視不同種類的執行個體活動。

## <a name="see-also"></a>另請參閱
- [什麼是 SSMS？](../ssms/sql-server-management-studio-ssms.md)
- [使用 SSMS 匯出和匯入資料庫](sql-server-linux-migrate-ssms.md)
- [教學課程：SQL Server Management Studio](../ssms/quickstarts/ssms-connect-query-sql-server.md)
- [教學課程：撰寫 Transact-SQL 陳述式](../t-sql/tutorial-writing-transact-sql-statements.md)
- [伺服器效能與活動監視](../relational-databases/performance/server-performance-and-activity-monitoring.md)