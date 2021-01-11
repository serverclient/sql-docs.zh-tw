---
title: SQL Server Data Tools (SSDT) 的版本資訊
description: 檢視適用於 Visual Studio 2017 和舊版 Visual Studio 的所有 SQL Server Data Tools (SSDT) 版本其版本資訊。
ms.prod: sql
ms.prod_service: sql-tools
ms.technology: ssdt
ms.topic: conceptual
ms.assetid: b071f8b8-c8e5-44e0-bbb6-04804dd1863a
author: dzsquared
ms.author: drskwier
ms.reviewer: maghan
ms.custom: seo-lt-2019
ms.date: 12/15/2019
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||=azuresqldb-mi-current'
ms.openlocfilehash: a380eabba4a20a65fd291b938d270a440f66d754
ms.sourcegitcommit: 44eebb659f9b226c08aea6c31a909b22ad4fec60
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2021
ms.locfileid: "97860604"
---
# <a name="release-notes-for-sql-server-data-tools-ssdt"></a>SQL Server Data Tools (SSDT) 的版本資訊

[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

這些是適用於 Visual Studio (VS) 的 [SQL Server Data Tools (SSDT)](download-sql-server-data-tools-ssdt.md) 的版本資訊。

<!--
Hello.  We have switched to a newer standardized format for Release Notes articles.
Basically we have switched from bullet lists to a 2-column table.
And we have shortened the H2 titles, to reduce wrapping in the rightNav.
And we have renamed the .md file to the standard format, from 'changelog-for-sql-server-data-tools-ssdt.md' to 'release-notes-ssdt.md'.
The presently latest H2 section (## 15.9.0) has been converted to the new format.
But the older sections are too numerous and long to warrant conversion.

REQUEST_1:  Please use the newer 2-column table format from now onward, for each new release section.

REQUEST_2:  Please consider whether it is time to erase perhaps the oldest 25% of these H2 sections.
Or maybe move all but the latest 9 (of 25) H2 sections to a new file perhaps named 'release-notes-history-ssdt.md', and link to it from the bottom of this file.

For questions, contact CraigG or SStein or GeneMi.

GeneMi , 2019/03/22.

P.S.  there's no need to keep this large HTML comment indefinitely.
-->

## <a name="1597nbsp-ssdt-for-vs-2017"></a>15.9.7，適用於 VS 2017 的 SSDT&nbsp;

發行日期：2021 年 1 月 5 日&nbsp;  
組建編號：14.0.16228.0&nbsp;  
適用於 Visual Studio 2017 的 SSDT。 

### <a name="whats-new"></a>新功能

| 新項目 | 詳細資料 |
|-----------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Integration Services (SSIS) | 將在 [IR 建立精靈] 中建立 SSISDB 的作業設為選用。 |
| Integration Services (SSIS) | 已修正在不同的訂用帳戶具有相同名稱的情況下，[Azure 訂用帳戶] 下拉式方塊項目會在 [IR 建立精靈] 與 [Azure 支援專案精靈] 中重複的問題。 |
| Integration Services (SSIS) | 已修正 [連線] 按鈕有時候無法在 [IR 建立] 精靈中啟用的問題。 |
| Integration Services (SSIS) | 已修正在不同的訂用帳戶具有相同名稱的情況下，[Azure 訂用帳戶] 下拉式方塊項目會在 [IR 建立精靈] 與 [Azure 支援專案精靈] 中重複的問題。 |
| Integration Services (SSIS) | 已修正當目前的地區設定為 [德國] 時，在指令碼元件 bufferwrapper.cs 底下自動產生的程式碼會額外新增雙引號的問題。 |
| Integration Services (SSIS) | 已修正當目標伺服器版本為 SQL Server 2012、2014、2016 時，不會顯示 [下載 WSDL] 按鈕的問題。 |
| Integration Services (SSIS) | 已修正建置大型專案可能會因為記憶體不足的例外狀況而導致失敗的問題。 |
| Integration Services (SSIS) | 已修正在套件部署模型中將套件儲存為檔案系統或 MSDB 的複本時，該套件不會降級為專案目前目標伺服器版本的問題。 |
| Integration Services (SSIS) | 已修正由於「沒有這類介面」錯誤而導致維度處理目的地無法正常運作的問題。 |
| Integration Services (SSIS) | 已修正某些與協助工具和高 DPI 有關的問題。 |

### <a name="known-issues"></a>已知問題

| 已知問題 | 詳細資料 |
| :---------- | :------ |
| 當 ExecuteOutOfProcess 設定為 True 時，SSIS 執行套件工作不支援偵錯。 | 此問題僅適用於偵錯。 透過 DTExec.exe 或 SSIS 目錄進行的儲存、部署及執行則不受到影響。 |
| &nbsp; | &nbsp; |


## <a name="1596nbsp-ssdt-for-vs-2017"></a>15.9.6，適用於 VS 2017 的 &nbsp; SSDT

發行日期：&nbsp; 2020 年 8 月 31 日  
組建編號：&nbsp; 14.0.16222.0  
適用於 Visual Studio 2017 的 SSDT。 

### <a name="whats-new"></a>新功能

| 新項目 | 詳細資料 |
|-----------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Integration Services (SSIS) | 已修正在連線至 SQL Server Analysis Services (SSAS) 資料來源時，[預覽] 按鈕對於 OLE DB 來源無法運作的問題。 |
| Integration Services (SSIS) | 已修正在移除相關聯的路徑之前即移除資料流程元件的輸入或輸出可能會導致 COMException 錯誤的問題。 |
| Integration Services (SSIS) | 已修正 SSAS 處理工作無法連線至 Power BI 工作區並重新整理其模型的問題。 |
| Integration Services (SSIS) | 已修正在使用 x64 執行階段並以 SQL Server 2017 為目標時，Visual Studio 會在偵錯指令碼工作/元件上停止回應的問題。 |
| Integration Services (SSIS) | 已修正在某些環境中選取 MySQL 驅動程式時，「匯入/匯出精靈」會損毀的問題。 |
| Integration Services (SSIS) | 已修正某些與協助工具和高 DPI 有關的問題。 |
| Integration Services (SSIS) | 允許使用者在開啟套件時略過驗證，這有助於改善效能。 如需詳細資訊，請參閱[在 SSDT 中加速開啟 SSIS 套件](https://techcommunity.microsoft.com/t5/sql-server-integration-services/accelerate-the-opening-of-ssis-package-in-ssdt/ba-p/1607099)。 |
| Integration Services (SSIS) | 在目標伺服器版本不是 SQL Server 2017 時，封鎖部署至 Azure-SSIS 的作業。 |

### <a name="known-issues"></a>已知問題

| 已知問題 | 詳細資料 |
| :---------- | :------ |
| 當 ExecuteOutOfProcess 設定為 True 時，SSIS 執行套件工作不支援偵錯。 | 此問題僅適用於偵錯。 透過 DTExec.exe 或 SSIS 目錄進行的儲存、部署及執行則不受到影響。 |
| 當 SSIS 和 SSAS 安裝在相同的 Visual Studio 執行個體時，Power Query 來源可能不支援 OData v4。 | &nbsp; |
| 當 SSIS 和 SSAS 安裝在相同的 Visual Studio 執行個體時，Power Query 來源可能不支援使用 ODBC 連接到 Oracle。 | &nbsp; |
| Power Query 來源未當地語系化。 | &nbsp; |
| &nbsp; | &nbsp; |

## <a name="1595nbsp-ssdt-for-vs-2017"></a>15.9.5，適用於 VS 2017 的 SSDT&nbsp;

發行日期：2020 年 5 月 27 日&nbsp;  
組建編號：14.0.16218.0&nbsp;  
適用於 Visual Studio 2017 的 SSDT。 

### <a name="whats-new"></a>新功能

| 新項目 | 詳細資料 |
|-----------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Integration Services (SSIS) | 已在 SSIS 工具箱中新增搜尋方塊，藉此新增搜尋工作和管線元件的支援。 |
| Integration Services (SSIS) | 已新增切換目標伺服器版本時的進度列。 |
| Integration Services (SSIS) | 已為啟用 Azure 的專案新增其他雲端設定，並新增在 Azure 中執行套件的 Windows 驗證支援。 |
| Integration Services (SSIS) | 已在啟用 Azure 的專案中，針對要在 Azure 中執行的套件新增評定。 |
| Integration Services (SSIS) | 已修正在某些情況下 VS 執行個體無法列在安裝程式中的問題 |
| Integration Services (SSIS) | 已修正如果解除安裝 VS 執行個體，則無法解除安裝此生產環境的問題。 |
| Integration Services (SSIS) | 已修正當目標伺服器版本低於 SQL Server 2019 時，從相同套件中另一個指令碼元件所複製的元件無法在偵錯期間正確載入的問題。 |
| Integration Services (SSIS) | 已修正在套件設計工具視窗底下，元件連接線的亮度比小於 3:1 的協助工具問題。 |
| Integration Services (SSIS) | 已修正在套件設計工具視窗底下，[將檢視調整成視窗大小] 控制項的亮度比小於 3:1 的協助工具問題。 |
| Integration Services (SSIS) | 已修正當資料庫具有包含 Filestream 的檔案群組時，傳送資料庫工作無法運作的問題。 |
| Integration Services (SSIS) | 已修正在 Foreach 迴圈元件中使用 ODBC 元件時，ODBC 元件會在套件執行期間於第二個迴圈中遇到「函式順序錯誤」的問題。 |
| Integration Services (SSIS) | 已修正重建索引工作 UI 在低解析度模式中將會被截斷的問題。 |
| Integration Services (SSIS) | 已修正 [登入] 按鈕在高 DPI 模式中未顯示的問題。 |
| Integration Services (SSIS) | 已修正連線管理員元素在高 DPI 模式中顯示太大的問題。 |
| Integration Services (SSIS) | 已修正執行結果在高 DPI 模式中一個接著一個堆疊的問題。 |

### <a name="known-issues"></a>已知問題

| 已知問題 | 詳細資料 |
| :---------- | :------ |
| 當 ExecuteOutOfProcess 設定為 True 時，SSIS 執行套件工作不支援偵錯。 | 此問題僅適用偵錯。 透過 DTExec.exe 或 SSIS 目錄進行的儲存、部署及執行則不受到影響。 |
| 當 SSIS 和 SSAS 安裝在相同的 Visual Studio 執行個體時，Power Query 來源可能不支援 OData v4。 | &nbsp; |
| 當 SSIS 和 SSAS 安裝在相同的 Visual Studio 執行個體時，Power Query 來源可能不支援使用 ODBC 連接到 Oracle。 | &nbsp; |
| Power Query 來源未當地語系化 | &nbsp; |
| 當目標為 SQL Server 2017 和 SxS 且 SQL Server 2017 已透過 CU19 或更新版 CU 修補時，如果 Run64BitRuntime 設定為 true，則偵錯套件 (其包含具中斷點的指令碼工作/元件) 會停止回應。 | &nbsp; |
| &nbsp; | &nbsp; |

## <a name="1594nbsp-ssdt-for-vs-2017"></a>15.9.4，適用於 VS 2017 的 SSDT

發行日期：  2020 年 3 月 26 日  
組建編號：  14.0.16214.0  
適用於 Visual Studio 2017 的 SSDT。 

### <a name="whats-new"></a>新功能

| 新項目 | 詳細資料 |
|-----------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Integration Services (SSIS) | 已修正在容器內移動控制流程限制式程式碼行時，VS 可能會損毀的問題。 |
| Integration Services (SSIS) | 已修正維護計劃工作的 UI 無法列出在工作 UI 外部所建立 ADO.NET 連線管理員的問題。 |
| Integration Services (SSIS) | 已修正當部署的 SSIS 專案屬於同時正在載入 SSIS 專案的解決方案時，Azure 互動式登入頁面未顯示的問題。 |
| Integration Services (SSIS) | 已修正當 SQL Server 未安裝時，按一下 MSOLAP 驅動程式屬性按鈕會使 DTS 精靈損毀的問題。 |
| Integration Services (SSIS) | 已修正 MSOLEDBSQL 驅動程式不支援 DTS 精靈中 AAD 驗證的問題。 |
| Integration Services (SSIS) | 已修正目標為 SQL Server 2012 時，XML 來源和 ADO.NET 目的地無法正確保存的問題。 |
| Integration Services (SSIS) | 已修正 Web 服務工作編輯器中 [下載 WSDL] 按鈕可能無法正確顯示的問題。 |
| Integration Services (SSIS) | 已修正可能無法在查閱轉換編輯器的 [連線管理員] 頁面中選取資料表的問題。 |
| Integration Services (SSIS) | 已修正快取轉換編輯器的版面配置可能錯誤的問題。 |
| Integration Services (SSIS) | 已修正套件編輯器中 [連線管理員] 區域可能無法正確顯示的問題。 |
| Integration Services (SSIS) | 已修正 [轉換為套件部署模型精靈] 中狀態圖示可能無法正確顯示的問題。 |
| Integration Services (SSIS) | 已將安裝程式變更為不需要從網際網路下載承載的完整安裝程式。 |

### <a name="known-issues"></a>已知問題

| 已知問題 | 詳細資料 |
| :---------- | :------ |
| 當 ExecuteOutOfProcess 設定為 True 時，SSIS 執行套件工作不支援偵錯。 | 此問題僅適用偵錯。 透過 DTExec.exe 或 SSIS 目錄進行的儲存、部署及執行則不受到影響。 |
| 當 SSIS 和 SSAS 安裝在相同的 Visual Studio 執行個體時，Power Query 來源可能不支援 OData v4。 | &nbsp; |
| 當 SSIS 和 SSAS 安裝在相同的 Visual Studio 執行個體時，Power Query 來源可能不支援使用 ODBC 連接到 Oracle。 | &nbsp; |
| Power Query 來源未當地語系化 | &nbsp; |
| &nbsp; | &nbsp; |

## <a name="1593nbsp-ssdt-for-vs-2017"></a>15.9.3，&nbsp; 適用於 VS 2017 的 SSDT

發行日期：  &nbsp; 2020 年 1 月 3 日  
組建編號：  &nbsp; 14.0.16203.0  
適用於 Visual Studio 2017 的 SSDT。 

### <a name="whats-new"></a>新功能

| 新項目 | 詳細資料 |
|-----------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Integration Services (SSIS) | 已移除 Power Query Source for SQL Server 2017 的收件匣元件。 我們現在宣告 Power Query Source for SQL Server 2017 與 2019 為 OOB 元件，您可以在[這裡](https://www.microsoft.com/en-us/download/details.aspx?id=100619)下載。 |
| Integration Services (SSIS) | 已移除 Microsoft Oracle Connector for SQL Server 2019 的收件匣元件。 我們現在宣告 Microsoft Oracle Connector for SQL Server 2019 為 OOB 元件，您可以在[這裡](https://www.microsoft.com/en-us/download/details.aspx?id=58228)下載。 |
| Integration Services (SSIS) | 已修正 SSIS 偵錯工具偶爾因為目標伺服器版本為 SQL Server 2017 或 2019 導致 IDtsHost 介面未註冊而無法啟動的問題。 |
| Integration Services (SSIS) | 已修正高 DPI 模式中的主要 UI 版面配置問題。 |
| Integration Services (SSIS) | 已將指令碼工作/元件的 .NET framework 版本升級為 4.7 (適用於目標伺服器版本為 SQL Server 2019 時)。 |
| Integration Services (SSIS) | 已在 ODBC 連線管理員中新增 ConnectByProxy 屬性，以支援在 ODBC 連線管理員中，將自我裝載整合執行階段啟用為 Proxy。 |
| Integration Services (SSIS) | 已修正使用者無法在套件部署模式下新增資料來源的問題。 |
| Integration Services (SSIS) | 已修正當程式碼使用 .NET 4.5 之後引進的任何新語法時，使用者無法對指令碼工作/元件進行偵錯的問題。 |
| Integration Services (SSIS) | 已修正透過 [整合執行階段建立精靈] 來建立 Azure 訂用帳戶的第一個 Data Factory 時，可能會因為未註冊 Data Factory 資源提供者而失敗的問題。 |
| Integration Services (SSIS) | 已修正當訂用帳戶中含有僅限檔案的儲存體帳戶時，[ADF 連線精靈] 中的 SSIS 無法正確顯示 Azure 儲存體帳戶清單的問題。 |
| Integration Services (SSIS) | 已修正當套件包含容器時，「在 Azure 中執行」沒有作用的問題。 |
| Integration Services (SSIS) | 已修正 Char (n Char) 與 Varchar2 (n Char) 對應到 Oracle 連接器中 DTS 類型不正確的問題。 |

### <a name="known-issues"></a>已知問題

| 已知問題 | 詳細資料 |
| :---------- | :------ |
| 當 ExecuteOutOfProcess 設定為 True 時，SSIS 執行套件工作不支援偵錯。 | 此問題僅適用偵錯。 透過 DTExec.exe 或 SSIS 目錄進行的儲存、部署及執行則不受到影響。 |
| 當 SSIS 和 SSAS 安裝在相同的 Visual Studio 執行個體時，Power Query 來源可能不支援 OData v4。 | &nbsp; |
| 當 SSIS 和 SSAS 安裝在相同的 Visual Studio 執行個體時，Power Query 來源可能不支援使用 ODBC 連接到 Oracle。 | &nbsp; |
| Power Query 來源未當地語系化 | &nbsp; |
| &nbsp; | &nbsp; |

## <a name="1592nbsp-ssdt-for-vs-2017"></a>15.9.2，適用於 VS 2017 的 SSDT

發行日期：  &nbsp; 2019 年 7 月 17 日  
組建編號：  &nbsp; 14.0.16194.0  
適用於 Visual Studio 2017 的 SSDT。 

### <a name="whats-new"></a>新功能

| 新項目 | 詳細資料 |
|-----------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Integration Services (SSIS) | 新增 AzureEnabled 功能。 讓專案套件能夠在 Azure Data Factory 的 SSIS 平台即服務 (PaaS) 上執行 |
| Integration Services (SSIS) | 修正無法從變數運算式設定 Oracle 連接器屬性的問題 |
| Integration Services (SSIS) | 修正 Oracle 連接器在針對目標為 SQL Server 2019 之前版本套件進行偵錯時發生 VS_NEEDSNEWMETATDATA 錯誤的問題 |
| Integration Services (SSIS) | 修正當套件/專案針對連線管理員屬性使用運算式時，Oracle 連接器無法升級/降級套件/專案的問題 |
| Integration Services (SSIS) | 修正 Web 服務工作編輯器 [下載 WSDL] 按鈕不支援 TLS 1.1 與 1.2 通訊協定 (目標為 SQL Server 2019) 的問題 |
| Integration Services (SSIS) | 修正包含 DQS 連線管理員的套件在儲存後無法再次載入的問題 |

### <a name="known-issues"></a>已知問題

| 已知問題 | 詳細資料 |
| :---------- | :------ |
| 當 ExecuteOutOfProcess 設定為 True 時，SSIS 執行套件工作不支援偵錯。 | 此問題僅適用偵錯。 透過 DTExec.exe 或 SSIS 目錄進行的儲存、部署及執行則不受到影響。 |
| 無法在套件部署模型中建立或編輯資料來源。 | 無法開啟 [資料來源精靈]。 |
| 當 SSIS 和 SSAS 安裝在相同的 Visual Studio 執行個體時，Power Query 來源可能不支援 OData v4。 | &nbsp; |
| 當 SSIS 和 SSAS 安裝在相同的 Visual Studio 執行個體時，Power Query 來源可能不支援使用 ODBC 連接到 Oracle。 | &nbsp; |
| Power Query 來源未當地語系化 | &nbsp; |
| &nbsp; | &nbsp; |

## <a name="1591nbsp-ssdt-for-vs-2017"></a>15.9.1，&nbsp;適用於 VS 2017 的 SSDT

發行日期：  &nbsp;2019 年 4 月 27 日  
組建編號：  &nbsp; 14.0.16191.0  
適用於 Visual Studio 2017 的 SSDT。 

### <a name="whats-new"></a>新功能

| 新項目 | 詳細資料 |
|-----------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Integration Services (SSIS) | 修正以先前 SQL Server 版本為目標時，套件組件無法正確保存的問題。 |
| Integration Services (SSIS) | 修正使用套件組件時，無法將運算式新增至優先順序條件約束的問題。 |
| Integration Services (SSIS) | 修正 Power Query 來源與連線管理員的 [說明] 按鈕無法連結到正確文件的問題。 |
| Integration Services (SSIS) | 修正 SSIS 組建版本不會在 VS 說明視窗中顯示的問題。 |
| Integration Services (SSIS) | 新增 Ole DB 和一般檔案連線管理員的 "ConnectByProxy" 屬性，該屬性可啟用使用 Azure-SSIS IR 中的自我裝載 IR 存取內部部署資料。 |
| Integration Services (SSIS) | 修正 ODBC 元件不正確對應 DT_DBDATE 資料類型的問題。 |
| Integration Services (SSIS) | 新增 ADO.NET 和 OLE DB 連線管理員的 "ConnectUsingManagedIdentity" 屬性，該屬性可啟用受控識別驗證來連線到 Azure-SSIS IR 中的資料來源。 |

### <a name="known-issues"></a>已知問題

| 已知問題 | 詳細資料 |
| :---------- | :------ |
| 當 ExecuteOutOfProcess 設定為 True 時，SSIS 執行套件工作不支援偵錯。 | 此問題僅適用偵錯。 透過 DTExec.exe 或 SSIS 目錄進行的儲存、部署及執行則不受到影響。 |
| 無法在套件部署模型中建立或編輯資料來源。 | 無法開啟 [資料來源精靈]。 |
| 當 SSIS 和 SSAS 安裝在相同的 Visual Studio 執行個體時，Power Query 來源可能不支援 OData v4。 | &nbsp; |
| 當 SSIS 和 SSAS 安裝在相同的 Visual Studio 執行個體時，Power Query 來源可能不支援使用 ODBC 連接到 Oracle。 | &nbsp; |
| Power Query 來源未當地語系化。 | &nbsp; |
| &nbsp; | &nbsp; |

## <a name="1590nbsp-ssdt-for-vs-2017"></a>15.9.0，&nbsp;適用於 VS 2017 的 SSDT

發行日期：  &nbsp; 2019 年 1 月 28 日  
組建編號：  &nbsp; 14.0.16186.0  
適用於 Visual Studio 2017 的 SSDT。 

### <a name="whats-new"></a>新功能

| 新項目 | 詳細資料 |
|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Integration Services (SSIS) | 在 ADF 2017 中新增適用於 SSIS 的 Power Query 來源 (預覽)。 |
| Integration Services (SSIS) | 再次新增對 SQL Server 2012 的支援。 |
| Integration Services (SSIS) | 新增 SQL Server 2019 的 Oracle 來源和目的地。 |
| Integration Services (SSIS) | 目標為 SQL Server 2019 的 Oracle 來源和目的地已由 SSDT 安裝。 <br/></br> 若要設計目標伺服器版本為 2017 或更低版本的套件，請從 Microsoft 下載網站下載對應的 Oracle 連接器版本，並將其安裝在 SSDT 電腦上。 <br/></br> [目標為 SQL Server 2017 的 Microsoft Connector Version 5.0 for Oracle by Attunity](https://www.microsoft.com/download/details.aspx?id=55179 ) \(英文\) <br/></br> [目標為 SQL Server 2016 的 Microsoft Connector Version 4.0 for Oracle by Attunity](https://www.microsoft.com/download/details.aspx?id=52950 ) \(英文\)<br/></br> [目標為 SQL Server 2014 的 Microsoft Connector Version 3.0 for Oracle by Attunity](https://www.microsoft.com/download/details.aspx?id=44582 ) \(英文\)<br/></br> [目標為 SQL Server 2012 的 Microsoft Connector Version 2.0 for Oracle by Attunity](https://www.microsoft.com/download/details.aspx?id=29283 ) \(英文\) |
| Integration Services (SSIS) | 修正從舊版 SSIS 移轉時無法載入指令碼工作/元件的問題。 |
| Integration Services (SSIS) | 修正資料檢視器在 Windows 7 SP1 和 Windows 8.1 上無法運作的問題。 |
| Integration Services (SSIS) | 修正在某些情況下，儲存套件會導致 Visual Studio 損毀的問題。 |
| Integration Services (SSIS) | 修正在某些情況下無法執行套件的問題。 |
| Integration Services (SSIS) | 當下列兩個條件同時成立時，就會發生這個問題：< br />< br /> &bull;   保護層級是 EncryptSensitiveWithPassword。< br /> &bull;   目標伺服器版本早於 SQL Server 2017。          |
| Integration Services (SSIS) | 修正預設字型的註釋無法在 SSDT 中顯示的問題。 |
| Integration Services (SSIS) | ISDeploymentWizard 支援在命令列模式中進行 SQL 驗證、Azure Active Directory 整合式驗證，以及 Azure Active Directory 密碼驗證。 |

### <a name="known-issues"></a>已知問題

| 已知問題 | 詳細資料 |
| :---------- | :------ |
| 當 ExecuteOutOfProcess 設定為 True 時，SSIS 執行套件工作不支援偵錯。 | 此問題僅適用偵錯。 透過 DTExec.exe 或 SSIS 目錄進行的儲存、部署及執行則不受到影響。 |
| 當 SSIS 和 SSAS 安裝在相同的 Visual Studio 執行個體時，Power Query 來源可能不支援 OData v4。 | &nbsp; |
| 當 SSIS 和 SSAS 安裝在相同的 Visual Studio 執行個體時，Power Query 來源可能不支援使用 ODBC 連接到 Oracle。 | &nbsp; |
| Power Query 來源未當地語系化。 | &nbsp; |
| &nbsp; | &nbsp; |

## <a name="1582nbsp-ssdt-for-vs-2017"></a>15.8.2，&nbsp;適用於 VS 2017 的 SSDT

發行日期：  &nbsp; 2018 年 11 月 5 日  
組建編號：  &nbsp; 14.0.16182.0  
適用於 Visual Studio 2017 的 SSDT。 

### <a name="whats-new"></a>新功能
**SSIS：**

修正了將 SSIS 專案 (內有包含指令碼工作/一般檔案目的地之套件) 部署到 Azure-SSIS 時，會導致該套件無法在 Azure-SSIS 中執行的問題。 

### <a name="known-issues"></a>已知問題：

- 當 ExecuteOutOfProcess 設定為 True 時，SSIS 執行套件工作不支援偵錯。 此問題僅適用偵錯。 透過 DTExec.exe 或 SSIS 目錄進行的儲存、部署及執行則不受到影響。

## <a name="1581nbsp-ssdt-for-vs-2017"></a>15.8.1，&nbsp;適用於 VS 2017 的 SSDT

發行日期：  &nbsp; 2018 年 9 月 27 日  
組建編號：  &nbsp; 14.0.16179.0  
適用於 Visual Studio 2017 的 SSDT。 

### <a name="whats-new"></a>新功能

**SSIS：**

1. 新增 [!INCLUDE[sql-server-2019](../includes/sssqlv15-md.md)] 的支援。
2. 移除 SQL Server 2012 的支援。

### <a name="known-issues"></a>已知問題：

- 當 ExecuteOutOfProcess 設定為 True 時，SSIS 執行套件工作不支援偵錯。 此問題僅適用偵錯。 透過 DTExec.exe 或 SSIS 目錄進行的儲存、部署及執行則不受到影響。
- 將套件包含指令碼工作/一般檔案目的地的 SSIS 專案部署至 Azure SSIS，會導致套件無法在 Azure-SSIS 中執行。

## <a name="158nbsp-ssdt-for-vs-2017"></a>15.8，&nbsp;適用於 VS 2017 的 SSDT

發行日期：  &nbsp; 2018 年 9 月 5 日  
組建編號：  &nbsp; 14.0.16174.0  
適用於 Visual Studio 2017 的 SSDT。 

### <a name="whats-new"></a>新功能

**SSIS：**

1. 修正在 VS 15.8 中儲存指令碼工作/元件將會觸發編譯錯誤的迴歸。
1. 修正 VS 15.8 中部署精靈無法運作的迴歸。
1. 修正 ADO.NET 連線管理員不支援協力廠商 ADO.NET 提供者的問題。

**安裝程式：**

- 實作在 Windows 10 上安裝 SSDT 時的中途重新開機。


### <a name="known-issues"></a>已知問題：

- 當 ExecuteOutOfProcess 設定為 True 時，SSIS 執行套件工作不支援偵錯。 此問題僅適用偵錯。 透過 DTExec.exe 或 SSIS 目錄進行的儲存、部署及執行則不受到影響。

## <a name="1571nbsp-ssdt-for-vs-2017"></a>15.7.1，&nbsp;適用於 VS 2017 的 SSDT

發行日期：  &nbsp; 2018 年 7 月 2 日  
組建編號：  &nbsp; 14.0.16167.0  
適用於 Visual Studio 2017 的 SSDT。 

### <a name="whats-new"></a>新功能

**SSIS：**

- 加入對新的 Azure Government AAD 授權單位 (login.microsoftonline.us) 的支援，以搭配 AS 工作一起使用。
- 修正當目標伺服器版本為 SQLServer2016 時，AS 處理工作 UI 將顯示「找不到方法」的問題。
- 修正當目標伺服器版本為 SQLServer2012 時，無法執行某些管線元件的問題。

**安裝程式：**

- 篩選 VS 執行個體清單，以排除無法安裝 SSDT 的執行個體。

### <a name="known-issues"></a>已知問題：

- 當 ExecuteOutOfProcess 設定為 True 時，SSIS 執行套件工作不支援偵錯。 此問題僅適用偵錯。 透過 DTExec.exe 或 SSIS 目錄進行的儲存、部署及執行則不受到影響。
- 在 Windows 10 上安裝 SSDT 並選擇 [安裝適用於 Visual Studio 2017 執行個體的新 SQL Server Data Tools] 時，安裝將因為「不支援要求的中繼檔作業」失敗。 重新啟動電腦，然後再次啟動 SSDT 安裝程式，以繼續安裝。

## <a name="1570nbsp-ssdt-for-vs-2017"></a>15.7.0，&nbsp;適用於 VS 2017 的 SSDT

發行日期：  &nbsp; 2018 年 6 月 4 日  
組建編號：  &nbsp; 14.0.16165.0  
適用於 Visual Studio 2017 的 SSDT。 

### <a name="whats-new"></a>新功能

**SSIS：**

- 修正 [選項] 對話方塊中的 [Integration Services 設計師] 頁面無法正確顯示的問題。  
- 修正出現在 [排序轉換編輯器]  編輯器中文字亮度比的問題。  
- 修正嘗試編輯下拉式方塊時 [解析參考]  對話方塊消失的問題。  
- 修正 [Hadoop 連線管理員]  的 F1 說明連結無法運作的問題。  
- 修正如果指令碼工作程式碼位於以 SQL Server 2016 為目標的容器中則會遺失的問題。  

**安裝程式：**

- 修正在 VS 15.7.2 中安裝 SSRS 和 SSIS 之前無法安裝 SSAS 的問題。

### <a name="known-issues"></a>已知問題：

- 當 *ExecuteOutOfProcess* 設定為 *True* 時，SSIS 執行套件工作不支援偵錯。 此問題僅適用偵錯。 透過 DTExec.exe 或 SSIS 目錄進行的儲存、部署及執行則不受到影響。

## <a name="1560nbsp-ssdt-for-vs-2017"></a>15.6.0，&nbsp;適用於 VS 2017 的 SSDT

發行日期：  &nbsp; 2018 年 4 月 10 日  
組建編號：  &nbsp; 14.0.16162.0  
適用於 Visual Studio 2017 的 SSDT。 

### <a name="whats-new"></a>新功能

**SSIS：**

- 修正下列問題：以 SQLServer2016 和 SQLServer2017 為目標時，AS 處理工作不會記錄任何處理步驟
- 修正在 SSDT 中開啟具有長的非英文工作名稱的 dtsx 時，會發生存取違規的問題
- 修正 ScriptTask 的變數清單有時候會從工作 UI 消失的問題
- 修正當套件位置為 SQL Server 時，新增現有套件複本會失敗的問題
- 修正在某些編輯器對話方塊中存取下拉式方塊時，焦點會卡住的問題。
- 修正切換 VS 佈景主題時，背景不會變更的問題。
- 修正在深色佈景主題中看不到註釋與載入標籤的問題。
- 修正已停用 SSIS 工具箱之項目的狀態屬性未正確定義的問題。
- 修正執行 WebServiceTask 時總是失敗的問題。
- 修正當連接字串設定為具有相依於專案參數之運算式的變數時，套件部署會失敗的問題。

**安裝程式：**

- 在隱私權免責聲明中新增「適用於 SQL Server Data Tools 的客戶經驗改進計畫」的連結。
- 修正選取 [為 Visual Studio 2017 執行個體安裝新的 SQL Server Data Tools] 時，VS 安裝程式視窗會彈出的問題

### <a name="known-issues"></a>已知問題：
- 當 ExecuteOutOfProcess 設定為 True 時，SSIS 執行套件工作不支援偵錯。 此問題僅適用偵錯。 透過 DTExec.exe 或 SSIS 目錄進行的儲存、部署及執行則不受到影響。

## <a name="1552nbsp-ssdt-for-vs-2017"></a>15.5.2，&nbsp;適用於 VS 2017 的 SSDT

組建編號：  &nbsp; 14.0.16156.0  
適用於 Visual Studio 2017 的 SSDT。 

### <a name="whats-new"></a>新功能

**SSIS**
- 修正當 SSAS 與 SSIS 都安裝在相同的 VS 2017 執行個體時，移轉 SSIS 2008 專案會失敗的問題。
- 修正將 RDLC 報表設計師和 SSIS 安裝到相同 VS 2017 執行個體時無法建置 RDLC 專案的問題。
- 修正無法更新註解色彩的問題。
- 修正 Hadoop 連線管理員編輯器會截斷其他語言某些字串的問題。
- 修正 OData 連線管理員編輯器會截斷某些字串的問題。
- 修正 [Integration Services 匯入專案精靈] 視窗會截斷某些字串的問題。
- 修正 SSIS 工具箱資訊視窗中的標題問題。
- 修正 [Integration Services 部署精靈] 視窗會截斷某些字串的問題。 

**安裝程式**
- 修正有時無法下載承載並會出現「系統找不到指定的檔案 (0x80070002)」錯誤的問題。  

### <a name="known-issues"></a>已知問題
- 當 *ExecuteOutOfProcess* 設定為 *True* 時，SSIS 執行套件工作不支援偵錯。 此問題僅適用偵錯。 透過 DTExec.exe 或 SSIS 目錄進行的儲存、部署及執行則不受到影響。

## <a name="1551nbsp-ssdt-for-vs-2017"></a>15.5.1，&nbsp;適用於 VS 2017 的 SSDT

組建編號：  &nbsp; 14.0.16148.0  
適用於 Visual Studio 2017 的 SSDT。 

### <a name="whats-new"></a>新功能

Visual Studio 2017 (15.5.1) 是與 15.5.0 版相同的版本，但安裝程式的下列 Bug 修正除外：

1. 修正安裝程式在 SQL Server Integration Services 後續安裝上停止回應的問題。
2. 修正安裝程式因下列錯誤訊息而失敗的問題：「不支援所要求的中繼檔作業 (0x800707D3)」。

除了這兩個 Bug 修正之外，15.5.0 的下列詳細資料仍然會套用至 15.5.1

## <a name="1550nbsp-ssdt-for-vs-2017"></a>15.5.0,&nbsp; 適用於 VS 2017 的 SSDT

組建編號：  &nbsp; 14.0.16146.0  
適用於 Visual Studio 2017 的 SSDT。 

### <a name="whats-new"></a>新功能

適用於 Visual Studio 2017 (15.5.0) 的 SSDT 會從預覽移至正式運作 (GA)。

**安裝程式**
1. 安裝程式 UI 已經當地語系化。
1. 將圖示取代為較高品質版本。

**Integration Services (IS)**
1. 在 ADF 中部署至 Azure SSIS IR 時，已在 [部署精靈] 中新增套件驗證步驟，這可探索要在 Azure SSIS IR 中執行之 SSIS 套件中的潛在相容性問題。 如需詳細資訊，請參閱[驗證部署到 Azure 的 SSIS 套件](../integration-services/lift-shift/ssis-azure-validate-packages.md)。
1. 已將 SSIS 延伸模組當地語系化。

### <a name="bug-fixes"></a>錯誤修正

**Integration Services (IS)**
1. 已修正 OLEDB 和 ADO.NET 連線管理員版面配置損毀的問題。
2. 已修正嘗試編輯維度處理工作時引發找不到組件錯誤的問題。

### <a name="known-issues"></a>已知問題

ExecuteOutOfProcess 設定為 True 時，**Integration Services (IS)** SSIS 執行套件工作不支援偵錯。 此問題僅適用偵錯。 透過 DTExec.exe 或 SSIS 目錄進行的儲存、部署及執行則不受到影響。

## <a name="173nbsp-ssdt-for-vs-2015"></a>17.3，&nbsp;適用於 VS 2015 的 SSDT

組建編號：  &nbsp; 14.0.61712.050  
適用於 Visual Studio 2015 的 SSDT。 

### <a name="whats-new"></a>新功能

**Analysis Services (AS) 專案**
- 已在表格式專案中新增三個新的選項 (在 [選項] > [Analysis Services 表格式] > [資料匯入] 下方)：
  - 啟用舊版資料來源 - 可讓使用者在較新的相容性模式中建立較舊的「1200 相容性模式」資料來源。
  - 自動類型偵測 - 啟用時，現代資料來源的查詢編輯器將在載入非結構化查詢時嘗試偵測其資料類型。 如果偵測成功，可能會將新的步驟新增至查詢。
  - 執行背景分析 - 啟用時，如果載入查詢以分析查詢的輸出結構描述，則現代資料來源的查詢編輯器將對資料來源執行查詢。

**Integration Services (IS)**
- 在 ADF 中部署至 Azure SSIS IR 時，已在 [部署精靈] 中新增套件驗證步驟，這可探索要在 Azure SSIS IR 中執行之 SSIS 套件中的潛在相容性問題。 如需詳細資訊，請參閱[驗證部署到 Azure 的 SSIS 套件](../integration-services/lift-shift/ssis-azure-validate-packages.md)。

### <a name="bug-fixes"></a>錯誤修正

**Analysis Services (AS) 專案：**
- 已修正將模型變更存回 TFS 時可能導致未處理例外狀況的問題。
- 已修正將具有複雜 M 運算式的資料表新增至 1400 模型時造成例外狀況的問題。
- 已修正在模型圖表檢視中搜尋中繼資料時可能造成 Visual Studio 損毀的問題。
- 已修正將變更儲存至資料分割 M 查詢時可能導致從資料表定義中移除計算結果欄的 1400 模型問題。
- 已修正在 [取得資料]\[資料表編輯器 UI] 的 1400 模型上使用重新命名查詢時可能在驗證與目前資料模型的相容性時凍結的問題。
- 已修正將 1400 模型部署至 Azure Analysis Service 時造成遺漏 Newtonsoft 組件參考的問題。
- 已修正導致在某些情況下透過 PQ 將資料匯入至 1400 模型時發生錯誤的問題。
- 已修正在設定 Windows 調整時出現的 PowerQuery 使用者介面對話方塊調整問題。
- 已修正重新命名角色問題。
- 已修正可能導致在某些情況下未將變更適當地儲存\同步處理的專案設定問題。
- 已修正 PowerQuery 編輯器中已自動新增「變更類型」步驟的問題。
- 已修正在切換至\自整合式工作區模式後開啟 BIM 檔案時導致錯誤的問題。
- 表格式模型中的資料來源現在可看見 MaxConnections 屬性。
- 增加 PowerQuery 編輯器視窗的初始大小。
- PowerQuery 編輯器中 "Source" 這類 M 查詢關鍵字現在會顯示為當地語系化。
- 使用 1400 模型和結構化資料來源而不需要輸入每個所編輯資料表的相同認證時的快取認證。

**RS 專案：**
- 已修正防止在多報表專案中部署單一報表的問題
- 已修正可能導致部署問題的共用資料來源問題
- 已修正復原管理員在程式碼檢視、設計檢視與查詢編輯器視窗之間切換時可能損毀的問題
- 已修正可能導致參數窗格在執行階段錯誤之後消失的問題
- 已修正可能會讓報表專案遺失原始檔控制對應的問題

**Integration Services：**
- 已修正可能在 Analysis Services 處理工作上切換連線時發生的問題
- 已修正未適當地當地語系化一些工作/元件的問題。
- 已修正套用可新增 \__$command\_id 資料行之 CDC 的 SQL 修正程式之後的CDC 元件中斷問題。

## <a name="1540-previewnbsp-ssdt-for-vs-2017"></a>15.4.0 (預覽)，&nbsp;適用於 VS 2017 的 SSDT

組建編號：  &nbsp; 14.0.16134.0  
適用於 Visual Studio 2017 的 SSDT。 
  
### <a name="whats-new"></a>新功能

此版本提供獨立式 Web 安裝程式，適用於 Visual Studio 2017 15.4 (或更新版本) 的 SQL Server Database、Analysis Services、Reporting Services 與 Integration Services 專案。

### <a name="installer"></a>安裝程式

- 可讓使用者在安裝新的適用於 VS2017 的 SSDT 執行個體時設定暱稱。
- 如果未選取任何 VS 執行個體，則隱藏安裝程式的功能選取核取方塊。
- 根據客戶意見反應改進安裝程式的某些訊息。
- 修正安裝程式不支援升級的問題。

### <a name="ssis"></a>SSIS

- 修正在安裝 Azure 功能套件時，[匯入/匯出精靈] 無法列出資料來源的問題。
- 修正在切換連線時，編輯 SSIS Analysis Services 處理工作擲回例外狀況的問題。
- 修正在套用新增 __$command_id 資料行的 SQL 修正程式之後，CDC 元件中斷的問題。
- 修正在以舊版 SQL Server 為目標時，無法編輯和執行協力廠商套件的問題。
- 修正在按兩下 DTSWizard.exe 並選取 [一般檔案來源] 時，[一般檔案來源] 設定對話方塊未正確顯示的問題。
- 修正在以 SQL Server 2017 為目標時，含有 Azure 功能套件工作/元件的套件無法執行的問題。

**已知問題**

- 安裝程式未當地語系化。
- 當 *ExecuteOutOfProcess* 設定為 True 時，SSIS 執行套件工作不支援偵錯。 此問題僅適用偵錯。 透過 DTExec.exe 或 SSIS 目錄進行的儲存、部署及執行則不受到影響。

## <a name="1730nbsp-ssdt-for-vs-2015"></a>17.30，&nbsp;適用於 VS 2015 的 SSDT

組建編號：  &nbsp; 14.0.61709.290  
適用於 Visual Studio 2015 的 SSDT。 

### <a name="whats-new"></a>新功能

**Analysis Services (AS)**

- 已啟用 1400 模型中的 Cosmos DB 和 HDI Spark。
- 表格式資料來源屬性。
- 現在支援使用 [空白查詢] 選項，在查詢編輯器中為 1400 相容性層級的模型建立新的查詢。
- 適用於 1400 模式模型的查詢編輯器現在允許儲存查詢，而不自動處理新的資料表。

**Reporting Services (RS)**

- 專案現在會提示開啟至已升級的格式來支援使用 MSBuild 進行建置和部署。

### <a name="known-issues"></a>已知問題

**Analysis Services (AS)**

- 直接查詢模式中的 1400 相容性層級模型若具有檢視方塊，則會在查詢或探索中繼資料時失敗。

**Reporting Services (RS)**

- 新的報表專案格式不會保留原始檔控制繫結，而且會引發類似下列訊息的錯誤：

   「專案檔 C:\path 並未繫結至原始檔控制，但方案中卻含有該專案檔的原始檔控制繫結資訊」  。

   若要解決此問題，請在每次開啟方案時，按一下 [使用方案繫結]  。

- 將您的專案升級至新的 MSBuild 格式之後，儲存可能會失敗並顯示類似如下的訊息：

   「參數 "unevaluatedValue" 不可為 Null」。

   若要解決此問題，請更新您的 [專案設定]  並填入 [平台]  屬性。

### <a name="bug-fixes"></a>錯誤修正

**Analysis Services (AS)**

- 大幅提升載入表格式模型圖表檢視的效能。
- 已修正一些問題來改善 1400 相容性層級模型中的 PowerQuery 整合與體驗。
   - 已修正導致沒有權限可編輯檔案來源的問題。
   - 已修正無法變更檔案來源之來源的問題。
   - 已修正針對檔案來源顯示的 UI 不正確的問題。
- 已修正 "Join on Date" 關聯性設成停用時導致 "JoinOnDate" 屬性被移除的問題。
- 查詢產生器中的新 [查詢] 選項現在允許建立新的空白查詢。
- 已修正導致對現有資料來源查詢所做的編輯無法更新 1400 相容性層級中資料表之模型定義的問題。
- 已修正自訂內容運算式可能會造成例外狀況的問題。
- 在 1400 表格式模型中匯入具有重複名稱的新資料表時，現在會通知使用者發生名稱衝突，並調整為唯一的名稱。
- 目前的使用者模擬模式由於不是支援的案例，因此已從匯入模式的模型中移除。
- PowerQuery 整合現在支援其他資料來源選項 (OData.Feed、Odbc.DataSource、Access.Database、SapBusinessWarehouse.Cubes)。
- 資料來源的 PowerQuery 選項字串現在會根據用戶端地區設定正確顯示當地語系化的文字。
- 圖表檢視現在會顯示 1400 相容性層級模型中從 M 查詢編輯器新建立的資料行。
- Power Query 編輯器現在提供不匯入資料的選項。
- 已修正安裝用來在 VS2017 多維度模型中，從 Oracle 匯入資料表的資料夾時所發生的問題。
- 已修正在罕見的情況下，當滑鼠游標離開表格式公式列時可能導致當機的問題。
- 已修正 [編輯資料表屬性] 對話方塊的問題；當變更資料表名稱未正確變更來源資料表名稱時，就會發生未預期的錯誤。
- 已修正嘗試在多維度專案中使用 [角色] 設計工具/[資料格資料] 索引標籤設計工具叫用 [測試 Cube 安全性] 時，VS2017 中可能會發生當機的問題。
- SSDT：無法編輯表格式資料來源的屬性。
- 已修正在使用方案檔的某些情況下，可能導致 MSBuild 和 DevEnv 組建無法正確運作的問題。
- 已大幅提升表格式模型包含較大中繼資料時的認可模型變更 (量值、計算結果欄的 DAX 編輯) 效能
- 已修正在 1400 相容性層級模型中使用 PowerQuery 匯入資料的一些問題
   - 按一下 [匯入] 之後需要很長的時間匯入，而且 UI 未顯示任何狀態
   - 當嘗試選取所要匯入資料表的速度很慢時，導覽檢視上的資料表清單很大
   - 在查詢編輯器檢視中使用 35 個查詢的清單時，查詢編輯器的效能低落 (PBI Desktop 也會發生此問題)
   - 匯入多個資料表已停用工具列，而且在特定情況下可能永遠不會完成 
   - 模型設計師似乎已停用，而且在使用 PQ 匯入資料表之後未顯示任何資料
   - 在 PQ UI 中取消選取 [建立新的資料表] 之後仍會建立新的資料表
   - 資料夾資料來源未出現輸入認證的提示 
   - 嘗試更新結構化資料來源中的認證時，可能會發生物件參考未設定的例外狀況
   - 使用 M 運算式開啟資料分割管理員的速度很慢
   - 在 PQ 編輯器中選取資料表上的屬性未顯示屬性
- 已改善 Power Query UI 整合來攔截最上層例外狀況並在 [輸出] 視窗中顯示
- 已修正在結構資料來源上變更來源不會在內容運算式中保留變更的問題
- 已修正 M 運算式錯誤可能會導致更新模型失敗但未顯示錯誤訊息的問題
- 已修正關閉 SSDT 並出現錯誤「此組建必須在方案可被關閉前停止」的問題
- 修正在 1400 相容性層級模型中設定錯誤的模擬模式時，VS 可能看似停止回應的問題 
- 詳細資料列屬性現在只會在非空白時 (預設值已變更)，才能序列化為 JSON
- 表格式直接查詢模式清單現在提供 Oracle OLEDB 驅動程式
- 在 1400 相容性表格式模型中新增 M 運算式現在會在表格式模型檔案總管 (TME) 中顯示/重新整理
- 已修正嘗試在 1400 之前版本的相容性層級模型中使用「其他」資料來源匯入時，導致 MSOLAP 提供者未在 VS2017 中顯示的問題
- 已修正透過 TME 新增轉譯可能造成的問題 
- 已修正「物件層級安全性」介面造成索引標籤在某些情況下未正確顯示/隱藏的問題
- 已修正嘗試使用 [連接到資料庫] 對話方塊開啟先前載入的多維度模型時可能發生失敗的問題
- 已修正將自訂組件新增至多維度模型時造成錯誤的問題

**Reporting Services (RS)**

- 已修正在 VS 2017 中編譯和建置 RDLC 的問題

## <a name="1530-previewnbsp-ssdt-for-vs-2017"></a>15.3.0 (預覽)，&nbsp;適用於 VS 2017 的 SSDT

組建編號：  &nbsp; 14.0.16121.0  
適用於 Visual Studio 2017 的 SSDT。 
  
### <a name="whats-new"></a>新功能

此預覽為適用於 Visual Studio 2017 的第一個 SSDT 版本。 此版本加入了獨立式網頁安裝體驗，適用於 Visual Studio 2017 15.3 (或更新版本) 的 SQL Server Database、Analysis Services、Reporting Services 與 Integration Services 專案。

**已知問題**

- 安裝程式未當地語系化。
- SSIS 未當地語系化。
- 當 *ExecuteOutofProcess* 設定為 *True* 時，SSIS 執行封裝工作不支援偵錯。 此問題僅適用偵錯。 透過 DTExec.exe 或 SSIS 目錄進行的儲存、部署及執行則不受到影響。
- 無法將包含協力廠商延伸模組的 SSIS 套件切換為其他伺服器版本目標。


## <a name="172nbsp-ssdt-for-vs-2015"></a>17.2，&nbsp;適用於 VS 2015 的 SSDT

組建編號：  &nbsp; 14.0.61707.300  
適用於 Visual Studio 2015 的 SSDT。 

### <a name="whats-new"></a>新功能

**AS 專案：**
- 在 1400 相容性層級表格式模型中，現在可以在進階安全性的 [角色]  對話方塊中設定「物件層級安全性」。
- 在 VS2017 的 SSDT AS 專案中，AS Azure 模型中沒有電子郵件地址之使用者的新 AAD 角色成員選擇。
- SSDT AS 表格式專案中自訂 ADAL 認證快取行為的新 AS Azure [一律提示] 專案屬性。

### <a name="bug-fixes"></a>錯誤修正

**一般**
- SQL Server 2017 的已更新商標參考。

**AS 專案**
- 認可 DAX 量值變更和其他模型編輯時，對改善體驗所進行的大幅效能修正。
- 修正使用 1400 相容性層級表格式模型之 Analysis Services 專案中的一些 Power Query 整合問題。
- 修正 VS2017 的多維專案中可能無法載入設計彙總設計工具的問題。
- 修正拖曳 Analysis Services 多維 DSV 圖表中可能會讓 VS 2017 當機之項目時的問題。
- 修正 AS 專案中 [部署] 對話方塊不一定會在 Visual Studio 前景的問題。
- 因為已解除服務，所以移除從作為資料來源之 Data Marketplace 的 Analysis Services 匯入。
- 修正透過 [表格式模型檔案總管] 從現有資料來源 [匯入新資料表] 之後停用資料表設計工具的問題。
- 修正可能讓 [模型] 功能表項目 [從資料來源匯入]/[新增資料來源] 在錯誤內容中保持隱藏的問題。
- 改善從 [表格式模型檔案總管] 建立量值以避免將焦點切換回用來建立量值之資料行時的問題。
- 從 AS 表格式專案中的整合式工作區切換為明確工作空間伺服器時，會立即清除舊的資料庫檔案。
- 修正 [資料列層級安全性] 核取方塊 UI 狀態一開始顯示為未核取之 AS 表格式 1400 模型專案中的問題，而不論實際基礎物件狀態為何。
- 修正使用 Power Query 將文字檔或 Excel 檔案匯入至 1400 相容性模式表格式模型時可能發生的當機，以及擲回未處理的例外狀況。
- 修正 AS 表格式模型設計工具之 DAX 公式編輯控制項中捲軸縮圖可能發生的問題。
- 修正防止 PowerQuery 混搭資料來源在包含使用者名稱/密碼驗證時進行修改的問題。
- 修正可防止在連接字串中設定其他屬性時連接資料來源的問題。
- 修正多個 AS 表格式模型專案載入並關閉第二個模型設計工具時可能會讓 VS 當機的問題，而不需要先與設計工具中的任何項目互動。
- 修正在某些情況下持續保存對 KPI 格式化進行之編輯的問題。
- 修正顯示錯誤功能表已核取狀態指出是否顯示公式列之 PowerQuery UI 的問題。
- 修正 AS 表格式 1400 相容性層級專案中，從 [表格式模型檔案總管] 選取 [變更資料來源] 功能表時可以讓 VS 當機的 PowerQuery 資料來源問題。
- 修正載入 1400 表格式模型可能會顯示「無法載入檔案或組件 'Microsoft.ProBI.MashupLibrary'」  錯誤的間歇性問題。

**RS 專案**
- 在工作階段之間，正確地記住 [RS 尺規和參數] 方塊設定選取狀態的使用者喜好設定。

**IS 專案**
- 修正未正確顯示 ADO/ADO.NET ForEachLoop 容器的問題
- 修正未當地語系化一些工作/元件/精靈的問題
- 已將最新的 *TargetServerVersion* 從 "SQL Server vNext" 變更為 "SQL Server 2017"

## <a name="1710nbsp-ssdt-for-vs-2015"></a>17.10，&nbsp;適用於 VS 2015 的 SSDT

組建編號：  &nbsp; 14.0.61705.170  
適用於 Visual Studio 2015 的 SSDT。 

### <a name="whats-new"></a>新功能
**AS 專案：**
- 使用者可以在 1400 模型的 UI 資料行上設定編碼提示
- 非模型相關的 IntelliSense 目前可以在離線模式中使用
- [表格式模型檔案總管] 現在包含一個節點，代表模型 (1400 相容性層級表格式模型) 中可用的具名 M 運算式
- Azure Active Directory 人員選擇器類似於 Microsoft Azure 入口網站的 IAM，目前在表格式模型中設定角色成員時即可供使用

**資料庫專案：**
- 更新為 DacFx 17.1

### <a name="bug-fixes"></a>錯誤修正
- 修正在 VS2017 的 Visual Studio 選項中，商業智慧設計師群組名稱顯示不正確的問題
- 修正使用報表專案或 AS 專案產生方案的 Code Map 時，發生當機的問題
- 修正 Analysis Services 1400 相容性層級表格式模型的一些 PowerQuery 整合問題
- 修正新的 DAX 編輯器工具視窗中當定義量值時，指派運算子可能不是單獨一行的問題
- 修正在重新命名檢視方塊中的量值時，阻止更新表格式量值顯示畫面的問題
- 更新 Analysis Services 整合式工作區引擎和表格式物件模型，以修正造成包含翻譯的 1200 表格式專案在部署到 SQL Server 2016 Analysis Services 伺服器時失敗的迴歸
- 修正建立\刪除新的 1400 表格式資料來源非常緩慢的效能問題
- 修正在不同 DSV 之間快速變更速檢視時，多維模型中的 DSV 圖表無法停止呈現的問題

## <a name="dacfx-171"></a>DacFx 17.1

- 修正使用其他身分識別資料行為記憶體最佳化資料表的資料行加密的問題
- SQLDOM 支援 CREATE DATABASE 的CATALOG_COLLATION 選項

## <a name="dacfx-1701"></a>DacFx 17.0.1

- 修正使用 HSM 的非對稱金鑰與 EKM 提供者[連接項目](https://connect.microsoft.com/SQLServer/feedback/details/3132749/sqlpackage-exe-fails-when-extracting-a-database-which-contains-an-asymmetric-key-using-an-ekm-provider) 的資料庫問題

## <a name="170nbsp-ssdt-for-vs-2015"></a>17.0，&nbsp;適用於 VS 2015 的 SSDT

組建編號：  &nbsp; 14.0.61704.140  
適用於 Visual Studio 2015 的 SSDT。   
最多支援 SQL Server 2017。 

### <a name="whats-new"></a>新功能
**資料庫專案：**
- 修改檢視上的叢集索引不會再封鎖部署
- 與資料行加密相關的結構描述比較字串會使用專有名稱，而不是執行個體名稱。   
- 新增命令列選項到 SqlPackage：ModelFilePath。  這可讓進階使用者選擇針對匯入、發佈和指令碼作業指定外部 model.xml 檔案   
- DacFx API 已擴充為支援 Azure AD 通用驗證和 Multi-Factor Authentication (MFA)

**IS 專案：**
- SSIS OData 來源和 OData 連線管理員現在支援連線到 Microsoft Dynamics AX Online 和 Microsoft Dynamics CRM Online 的 OData 摘要。
- SSIS 專案現在支援 "SQL Server 2017" 的目標伺服器版本 
- 以 SQL Server 2017 為目標時，支援 CDC 控制工作、CDC 分隔器和 CDC 來源。 

**AS 專案：**
- Analysis Services PowerQuery 整合 (1400 相容性層級表格式模型)：
    - 如果使用者已安裝協力廠商驅動程式，則 DirectQuery 適用於 SQL Oracle 和 Teradata
    - 在 PowerQuery 中透過範例新增資料行
    - 1400 模型 (M 引擎所使用的模型層級屬性) 中的資料存取選項
        - 啟用快速合併 (預設值為 false - 設定為 true 時，交互式 Web 應用程式引擎會在合併資料時忽略資料來源的隱私權等級)
        - 啟用舊版重新導向 (預設值為 false - 設為 true 時，交互式 Web 應用程式引擎會遵循可能不安全的 HTTP 重新導向。    例如，從 HTTPS 至 HTTP URI 的重新導向)  
        - 以 Null 傳回錯誤值 (預設值為 false - 設為 true 時，資料格層級的錯誤會以 null 傳回。 設定為 false 時，所引發的例外狀況是資料格包含錯誤)  
    - 使用 PowerQuery 的額外資料來源 (檔案資料來源)
        - Excel 
        - 文字/CSV 
        - Xml 
        - Json 
        - 資料夾 
        - Access 資料庫 
        - Azure Blob 儲存體 
    - 當地語系化的 PowerQuery 使用者介面
- DAX 編輯器工具視窗
    - 改善量值、導出資料行和詳細資料列運算式的 DAX 編輯體驗，可透過 SSDT 中的 [檢視]、其他 Windows 功能表存取
    - DAX 部析器\Intellisense 的增強功能


**RS 專案︰**
- 可內嵌的 RVC Control 現在可支援 SSRS 2016

### <a name="bug-fixes"></a>錯誤修正
**AS 專案：**
- 修正 BI 專案的範本優先順序，使它們不會顯示在 VS 的 [新專案] 類別頂端
- 修正開啟 SSIS、SSAS 或 SSRS 解決方案時 VS 可能會當機的罕見問題
- 表格式：DAX 剖析和資料編輯列的各種功能增強及效能修正。
- 表格式：如果未開啟任何 SSAS 表格式專案，表格式 [模型總管] 就不會再顯示。
- 多維度：修正處理對話方塊在高 DPI 電腦上無法使用的問題。
- 表格式：已修正 SSDT 會在 SSMS 已開啟的情況下開啟任何 BI 專案時發生錯誤的問題。 [Connect 項目](https://connect.microsoft.com/SQLServer/feedback/details/3100900/ssdt-faults-when-opening-any-bi-project-when-ssms-is-already-open)
- 表格式：修正階層未正確儲存到 1103 模型中 BIM 檔案的問題。[Connect 項目](https://connect.microsoft.com/SQLServer/feedback/details/3105222/vs-2015-ssdt) \(部分機器翻譯\)
- 表格式：修正即使不支援，但在 32 位元電腦上仍允許整合式工作區模式的問題。
- 表格式：修正在半選取模式 (例如鍵入 DAX 運算式但按下量值) 下按一下任何項目都會造成損毀的問題。
- 表格式：修正 [部署精靈] 會將模型的 Name 屬性重設回 "Model" 的問題。 [Connect 項目](https://connect.microsoft.com/SQLServer/feedback/details/3107018/ssas-deployment-wizard-resets-modelname-to-model)
- 表格式：修正即使未選取圖表檢視，在 TME 中選取階層也應顯示屬性的問題。
- 表格式：修正從特定應用程式貼入 DAX 資料編輯列時會貼上影像或其他內容，而非文字的問題。
- 表格式：修正 1103 中有些舊模型因為具有特定定義的量值存在而無法開啟的問題。
- 表格式：修正無法刪除 XEvent 工作階段的問題。
- 修正嘗試使用 devenv.com 建置 AS "smproj" 檔案會失敗的問題
- 修正在 AS 表格式模型工作表索引標籤標題中使用韓文輸入法時，會太頻繁地完成文字變更的問題
- 修正 DAX Related() 函數的 IntelliSense 無法正確顯示其他資料表之資料行的問題
- 透過針對 AS 資料庫清單進行排序，改進從資料庫對話方塊的 AS 表格式專案匯入
- 修正在 AS 表格式模型中建立導出資料表時，Tables 在陳述式中沒有列為建議物件的問題
- 修正預覽 1400 AS 模型在檢視程式碼之後嘗試使用整合式工作空間伺服器開啟的問題
- 修正在某些情況下防止 (不具有初始目錄支援的) 資料來源正常運作的問題 
- 部署精靈應該將變更套用到導出資料表分割區，即使已啟用保持分割區的選項也一樣
- 修正現有 AS 連線的 [進階屬性] 對話方塊要在重新選取後才會顯示完整清單的問題
- 修正一些裁剪的 UI 字串出現在某些當地語系化組建的問題
- 修正 1400 相容性層級 AS 表格式模型的一些 PowerQuery 整合問題
- 修正報表精靈樣式範本未正確顯示的問題
- 修正報表精靈在從 SQL 變更為 AS 時，可能會導致資料來源設定不正確的問題
- 修正導致從命令列 (devenv.com\exe) 執行 Analysis Services (表格式) 專案組建失敗的問題
- 修正 DAX 量值剖析器在以之前字母開頭時，要反白顯示並顯示正確的文字色彩的問題: =
- 修正路徑嘗試在整合式工作區模式下顯示表格式專案的所有檔案時，花費太長時間而觸發 ObjectRefException 的問題
- 修正 Compact 4.0 用戶端資料提供者的資料來源設計師無法使用的問題
- 修正嘗試在 VS2017 中瀏覽 AS 採礦模型時造成錯誤的問題
- 修正 VS2017 中 DSV 圖表在變更檢視之後停止轉譯，然後觸發例外狀況的 AS 多維模型問題
- 修正在 VS2017 中使用 AS 連線預覽報表失敗的問題
 

**RS 專案︰**
- 修正在 SSDT 中設計報表時，參數、資料來源與資料集的樹狀檢視會在做出大多數變更時摺疊的問題 
- 修正 [儲存] 應儲存 RDL 版本而非最新版本的問題。
- 修正 SSDT RS 在備份關閉時仍備份檔案的問題，及其他數個問題。
- 修正報表產生器中，在按一下 [分割儲存格] 時會顯示錯誤的問題。 [Connect 項目](https://connect.microsoft.com/SQLServer/feedback/details/3101818/ssdt-2015-ssrs-designer-error-by-matrix-cell-split)
- 修正快取可能造成報表中資料不正確的問題。 [Connect 項目](https://connect.microsoft.com/SQLServer/feedback/details/3102158/ssdtbi-14-0-60812-report-preview-data-is-frequently-wrong-due-to-bad-caching)

**IS 專案：**
- 修正 run64bitruntime 設定無法固定的問題。
- 修正 DataViewer 不會儲存顯示的資料行的問題。
- 修正套件組件會隱藏註解的問題。 [Connect 項目](https://connect.microsoft.com/SQLServer/feedback/details/3106624/package-parts-hide-annotations)
- 修正套件組件會捨棄 [資料流程] 配置與註解的問題。 [Connect 項目](https://connect.microsoft.com/SQLServer/feedback/details/3109241/package-parts-discard-data-flow-layouts-and-annotations)
- 修正 SSDT 在從 SQL Server 匯入專案時損毀的問題。
- 修正在開啟已儲存的 SSIS 封裝之後以及執行階段時，Hadoop 檔案系統工作 TimeoutInMinutes 預設為 10 的問題。

**資料庫專案：**
- SSDT DACPAC 部署會將將設定新增回 IgnoreColumnOrder [Connect 項目](https://connect.microsoft.com/SQLServer/feedback/details/1221587/ssdt-dacpac-deploy-add-setting-back-in-for-ignorecolumnorder)
- 如果使用 STRING_SPLIT，SSDT 就無法編譯 [Connect 項目 (英文)](https://connect.microsoft.com/SQLServer/feedback/details/2906200/ssdt-failing-to-compile-if-string-split-is-used)
- 修正 DeploymentContributors 可以存取公開模型，但支援結構描述未初始化的問題 [GitHub 問題](https://github.com/Microsoft/DACExtensions/issues/8) \(英文\)
- FILEGROUP 位置的 DacFx 暫時修正
- 外部同義字的「無法解析的參考」錯誤修正。 
- Always Encrypted：線上加密在取消時不會停用變更追蹤，且如果開始加密之前沒有清理變更追蹤，線上加密便無法正常運作

## <a name="165nbsp-ssdt-for-vs-2015"></a>16.5，&nbsp;適用於 VS 2015 的 SSDT

發行日期：  &nbsp; 2016 年 10 月 20 日  
組建編號：  &nbsp; 14.0.61021.0  
適用於 Visual Studio 2015 的 SSDT。   
最多支援 SQL Server 2016。 

**新功能**

### <a name="connection-improvements"></a>連線功能改進

* 在 [瀏覽]  索引標籤中的新搜尋方塊可協助您篩選您的本機伺服器、網路伺服器和 Azure SQL 資料庫。 如果您有大量伺服器或資料庫出現在這些清單中，此功能非常有用。
* [歷程記錄]  索引標籤有右鍵功能表選項可釘選/取消釘選 [我的最愛]，還有新選項可從歷程記錄移除連線。

### <a name="sqlpackage-and-dacfx-api-improvements"></a>SqlPackage 和 DacFx API 功能改進

您現在可以使用 SqlPackage.exe 和 DacFx API，只以一個動作就能產生部署報表、部署指令碼，並發行至資料庫。 這對於想要保留部署期間發行內容報表的任何人來說非常節省時間。 另一個好處是在 Azure 的案例中，會針對主要資料庫與部署目標資料庫建立不同的指令碼。 在此之前只會建立單一指令碼，對於重複的部署不是很有用。

針對 SqlPackage 的 Publish 和 Script 動作，加入兩個新的引數。

* DeployScriptPath (簡短名稱︰dsp)。 這是要撰寫部署指令碼的選擇性路徑。 對於 Azure 部署，如果有 TSQL 命令要建立或修改資料庫，將會對主要指令碼寫入相同的路徑，但會使用 "Filename_Master.sql" 做為輸出檔案名稱。
* DeployReportPath (簡短名稱︰drp)。 這是要撰寫部署報表的選擇性路徑。

Script 動作應使用現有的「輸出路徑」引數或新的指令碼/報表特定引數，但不是同時使用兩者。

範例使用方式︰

**Publish 動作**

```Sqlpackage.exe /a:Publish /tsn:(localdb)\ProjectsV13 /tdn:MyDatabase /deployscriptpath:"My\DeployScript.sql" /deployreportpath:"My\DeployReport.xml"```

**Script 動作**

```Sqlpackage.exe /a:Script /tsn:(localdb)\ProjectsV13 /tdn:MyDatabase /deployscriptpath:"My\DeployScript.sql" /deployreportpath:"My\DeployReport.xml"```

DacFx 中已新增兩個新的 API：DacServices.Publish() 和 DacServices.Script()。 這些 API 也支援在單一作業中執行發行 + 編寫指令碼 + 報告的動作。 範例使用方式︰

```
DacServices service = new DacServices(connectionString);
using(DacPackage package = DacPackage.Load(@"C:\My\db.dacpac")) {
var options = new PublishOptions() {
    GenerateDeploymentScript = true, // Should a deployment script be created?
    GenerateDeploymentReport = true, // Should an xml deploy report be created?
    DatabaseScriptPath = @"C:\My\OutputScript.sql", // optional path to save script to
    MasterDbScriptPath = @"C:\My\OutputScript_Master.sql", // optional path to save master script to
    DeployOptions = new DacDeployOptions()
};

// Call publish and receive deployment script & report in the results
PublishResult result = service.Publish(package, "TargetDb", options);
Console.WriteLine(result.DatabaseScript);
Console.WriteLine(result.MasterDbScript);
Console.WriteLine(result.DeploymentReport);

// Call script and receive deployment script & report in results
result = service.Script(package, "TargetDb", options);
Console.WriteLine(result.DatabaseScript);
Console.WriteLine(result.MasterDbScript);
Console.WriteLine(result.DeploymentReport);
```

**Analysis Services 與 Reporting Services**

使用大型 DAX 運算式時，SSAS 表格式設計工具 DAX 剖析器已經改善效能。
如需詳細資訊，請參閱 [Analysis Services 部落格文章 (英文)](/archive/blogs/analysisservices/introducing-integrated-workspace-mode-for-sql-server-data-tools-for-analysis-services-tabular-projects-ssdt-tabular)。

### <a name="fixed--improved-this-month"></a>本月份修正/改進內容

**資料庫工具**

* [Connect Bug 3055711](https://connect.microsoft.com/SQLServer/feedback/details/3055711/columns-can't-be-selected-from-cross-apply-openjson-with-explicit-schema) - 無法從有明確結構描述的 CROSS APPLY OPENJSON 選取資料行
* 已修正 - 在自動產生的歷程記錄資料表索引中，DacFx 在重新部署時會卸除索引的問題
* 已修正 - DacFx 批次剖析器未剖析逸出括號 ']' 字元而導致發行失敗的問題
* 已改進 - SqlPackage 現在在說明輸出中會包含每個動作的描述
* 已修正 - 編輯進階選項時，以及編輯「發行」、「結構描述比較」和其他檔案中儲存的連接字串時，沒有保留 [連接] 對話方塊的 [記住密碼] 選項
* 已修正 - 對於在 [歷程記錄] 索引標籤中 IntegratedAuthentication=true 所顯示的連接，連接屬性中的 [驗證] 欄位為空白。 現在這會如預期般顯示「Windows 驗證」
* 已修正 - 沒有保留 [工具] -> [選項] -> [文字編輯器] 下對 SQL Server 工具 Intellisense 設定的變更
* 已改進 - [連接] 對話方塊的 [歷程記錄] 索引標籤中的 [釘選]/[取消釘選] 按鈕現在更緊密，降低捲軸出現的可能性
* 已修正 - 已修正 [連接] 對話方塊中幾個協助工具問題。

**Analysis Services 與 Reporting Services**

* 已修正在某些情況下在 SSDT AS 表格式設計工具中按一下資料格中的捲軸指標會當機的問題
* 已修正沒有選項可將連線模擬為 SSDT AS 表格式中目前使用者的問題
* 已修正在 SSDT AS 表格式設計工具中將公式列展開太遠而無法重新開啟專案的問題
* 已修正在 SSDT AS 表格式設計工具中若已選取資料表索引標籤，按向下鍵可能會發生當機的問題
* 已修正在 SSDT AS 專案中 [在 Excel 中進行分析] 不會連線至下層 AS 伺服器版本的問題

**整合服務**

* 已修正連接錯誤 [1608896](https://connect.microsoft.com/SQLServer/feedback/details/1608896/move-multiple-integration-service-package-tasks)︰Move Multiple Integration Service Package Tasks (移動多個整合服務封裝工作)

## <a name="164-ssdt-for-vs-2015"></a>16.4，適用於 VS 2015 的 SSDT

發行日期：  &nbsp; 2016 年 9 月 20 日  
組建編號：  &nbsp; 14.0.60918  
適用於 SQL Server 2016。 

**新功能**

SqlPackage.exe 和 Data-Tier Application Framework (DacFx) API 現在支援結構描述比較。 如需詳細資訊，請參閱 [SqlPackage 和 Data-Tier Application Framework 中的結構描述比較 (英文)](/archive/blogs/ssdt/schema-compare-in-sqlpackage-and-the-data-tier-application-framework-dacfx)。

**Analysis Services - SSDT 表格式 (SSAS) 的整合式工作區模式**

SSDT 表格式現在包含內部的 SSAS 執行個體，若啟用整合式工作區模式，則 SSDT 表格式會自動在背景啟動此執行個體，讓您可以在模型設計師中新增及檢視資料表、資料行和資料，無需提供外部工作區伺服器執行個體。 整合式工作區模式不會變更 SSDT 表格式與工作區伺服器及資料庫搭配運作的方式。 變更之處在於 SSDT 表格式裝載工作區資料庫的位置。 若要啟用整合式工作區模式，請在建立新的表格式專案時顯示的 [表格式模型設計師] 對話方塊中選取 [整合式工作區] 選項。 對於目前使用明確工作區伺服器的現有表格式專案，您可以在於 [方案總管] 中選取 Model.bim 檔案時顯示的 [屬性] 視窗中將 [整合式工作區模式] 參數設定為 True，切換到整合式工作區模式。 如需詳細資訊，請參閱 [Analysis Services 部落格文章 (英文)](/archive/blogs/analysisservices/introducing-integrated-workspace-mode-for-sql-server-data-tools-for-analysis-services-tabular-projects-ssdt-tabular)。

**更新和修正**
**資料庫工具︰**

- [Connect 問題 3087775](https://connect.microsoft.com/SQLServer/feedback/details/3087775)：VS 資料工具 7 月更新 14.0.60629.0 中的時態表損毀，「值不可以是 Null。 Parameter name: reportedElement" (VS 資料工具 7 月更新 14.0.60629.0 中的時態表損毀，「值不可以是 Null。參數名稱：reportedElement」)
- [Connect 問題 1026648](https://connect.microsoft.com/SQLServer/feedback/details/1026648)：IsPersistedNullable shows as different in SSDT Comparison (IsPersistedNullable 在 SSDT 比較中顯示為不同)
- [Connect 問題 2054735](https://connect.microsoft.com/SQLServer/feedback/details/2054735)：Identity is reset when importing a BACPAC (匯入 BACPAC 時會重設識別)
- [Connect 問題 2900167](https://connect.microsoft.com/SQLServer/feedback/details/2900167)：Running SSDT unit tests leaves temp files behind (執行 SSDT 單元測試留下暫存檔案)
- [Connect 問題 1807712](https://connect.microsoft.com/SQLServer/feedback/details/1807712)：Backwards compatibility breakage - AppLocal and Nugetization (回溯相容性損毀 - AppLocal 和 Nugetization)

**Analysis Services 與 Reporting Services**

* 已修正在 SSDT 中當編輯 DirectQuery 導出資料行的 DAX 時會出現錯誤提示快顯視窗的問題。
* 已修正在 SSDT AS 表格式方格中當 Windows 縮放比例設定在高 DPI 200%+ 時，KPI 圖示未顯示在量值方格中中的問題。
* 已修正在 SSDT AS 中貼上大型資料表資料可能會讓表格式專案沒有回應的問題。
* 已修正在 SSDT AS 表格式模型編輯器中重新命名連線的易記名稱時，將模型標示為必須儲存變更的問題。
* 已修正在 SSDT AS 表格式專案中 [管理關聯性] 對話方塊中的資料行寬度無法調整大小的問題。
* 已修正在 SSDT AS 表格式 1200 層級的模型中，若地區設定為例如德文，從 Excel 貼上資料沒有正確將逗號視為小數分隔符號的問題。
* 已修正在設定某些 KPI 圖示集的 SSDT AS 專案中可能會產生「無法擷取此視覺效果的資料」錯誤的問題。
* 已修正 SSDT AS 專案屬性對話方塊在高 DPI 縮放比例調整大小時正確錨定的問題。
* 已修正在 SSDT AS 專案中升級內含貼上的資料表的特定模型時可能會造成錯誤的問題。
* 已修正在 SSDT AS 中從 Excel 貼上整個工作表列緩慢且建立許多不必要欄的問題。
* 修正在 SSDT AS 中大型靜態 DataTable 運算式的剖析和醒目提示緩慢或似乎停止回應的問題。
* 已修正在 SSDT AS 中將量值和 KPI 值新增至編輯器中目前所選檢視方塊的問題。
* 已修正在 SSDT 中從 SQL Azure 匯入 AS 專案的資料不支援除了 "dbo" 以外之結構描述類型的問題。

## <a name="163nbsp-ssdt-for-vs-2015"></a>16.3，&nbsp;適用於 VS 2015 的 SSDT

發行日期：  &nbsp; 2016 年 8 月 15 日  
組建編號：  &nbsp; 14.0.60812.0  
適用於 SQL Server 2016。 

**新功能**

- **發行版本控制與編號：** 版本現在以數值標記而不是依月份標記。 這與新的 SSMS 原則一致，並簡化在一個月中有多個版本或 hotfix 時的情況。 此版本是 16.3，表示在 RTM 版本之後的第三個更新。 任何 Hotfix 將是 16.3.1，依此類推，下一個更新 (計劃下個月推出) 將是 16.4。
- **Analysis Services - 表格式 [模型總管]：** 表格式 [模型總管] 可讓您在模型中方便巡覽各種中繼資料物件，例如資料來源、資料表、量值和關聯性。 它會實作為獨立的工具視窗，您可以在 Visual Studio 中開啟 [檢視] 功能表，指向 [其他視窗]，然後按一下 [表格式模型總管] 來顯示。 表格式模型總管預設會出現在方案總管區域的另一個索引標籤上。表格式模型總管會將中繼資料物件組織在與表格式 1200 模型結構描述十分類似的樹狀結構中，而且有更多新功能。
- **資料庫工具 - Always Encrypted**：此版本提供新的 [[Always Encrypted 金鑰管理](../relational-databases/security/encryption/overview-of-key-management-for-always-encrypted.md)] 對話方塊，可讓您輕鬆地將資料行主要金鑰或資料行加密金鑰新增至資料庫專案或 SQL Server [物件總管] 中的即時資料庫。 此版本支援 Windows 憑證存放區中的憑證。 未來的版本將會支援 Azure 金鑰保存庫和 CNG 提供者。
    - 在建立資料行主要金鑰或資料行加密金鑰時，您可能會發現按一下 [更新資料庫] 之後，SQL Server 物件總管無法立即反映所做的變更。 若要因應此問題，請重新整理 SQL Server 物件總管中的資料庫節點。
    - 如果您嘗試加密的資料表資料行含有來自 SQL Server 物件總管的資料，您可能會失敗。 目前只有在 SSDT 資料庫專案和 SSMS 中才支援這項功能。 未來版本中將會支援 SQL Server 物件總管。

**更新和修正**
* **資料庫工具︰**
    - **SSDT：**
        - 連接錯誤 1898001 [已修正資料行描述 128 個字元限制的問題](https://connect.microsoft.com/SQLServer/feedback/details/1898001/column-description-limited-to-128-characters)。
        - 已修正從 VS 發行資料庫並未在發行設定檔 xml 中套用 DatabaseServiceObjective 屬性的問題。
        - 連接錯誤 2900167 [已修正不正確保留暫存檔的單元測試問題](https://connect.microsoft.com/SQLServer/feedback/details/2900167/running-ssdt-unit-tests-leaves-temp-files-behind)。
        - 已修正 [資料庫設定] 的 [保留週期] 下拉式方塊被截斷的問題。
        - 已修正變更密碼時遺漏在 SQL CLR 專案屬性上驗證空白舊密碼的問題。
    - **DACFx：**
        - 已修正未針對 SqlAzureV12 錯誤更新 DACFx 相容性層級的問題。
        - 已修正不正確地從模型比較排除 IsAutoGeneratedHistoryTable 屬性的問題。

- **Analysis Services 與 Reporting Services**
    - **SSDT：**
        - 已修正伺服器連線中斷時，無法儲存表格式模型的問題。
        - 已修正 SSDT 因為 AS 中可能存在的無限迴圈問題而變成沒有回應的問題。
        - 已修正根據您如何認可運算式而造成不一致行為的 DAX 運算式問題。
        - 已修正建立 KPI 時的 VS 當機問題。
        - 已修正為 SQL Server 2008 R2、2012 和 2014 產生無效報表的問題。
        - 已修正造成 .dwpro 專案無限迴圈錯誤的階層順序問題。
        - 已修正降級 RDL 需要完整重建而造成使用者混淆的 RS RDL 問題。
        - 已修正「在用戶端工具中隱藏」沒有作用的 KPI 問題。

## <a name="july-2016nbsp-ssdt-for-vs-2015"></a>2016 年 7 月，&nbsp;適用於 VS 2015 的 SSDT

發行日期：  &nbsp; 2016 年 6 月 30 日  
組建編號：  &nbsp; 14.0.60629.0  
適用於 SQL Server 2016。 

**新功能**  
- **Always Encrypted 支援：** 針對包含 Always Encrypted 資料行的資料庫，此版本透過我們的核心 API 與命令列工具 (SqlPackage.exe) 新增 Always Encrypted 的完整支援。 您可以利用所有完整支援的 Always Encrypted 功能，建置及發行資料庫專案。  
- **時態表增強支援：** 透過在改變之前取消連結時態表，然後在完成之後再重新連結來簡化體驗。 這表示時態表在支援的作業方面有其他資料表類型 (標準、記憶體內部) 的同位。 
- **SqlPackage.exe 和安裝變更：** 從 SQL Server 引擎隔離出 SSDT 的變更以及 SSMS 更新。 如需詳細資訊，請參閱 [SSDT 和 SqlPackage.exe 安裝和更新的變更 (英文)](/archive/blogs/ssdt/changes-to-ssdt-and-sqlpackage-exe-installation-and-updates)。


**更新和修正**
* **資料庫工具︰**
    * 從現在起，SSDT 將永遠不會停用資料庫的「透明資料加密」(TDE)。 先前因為停用專案資料庫設定中的預設加密選項，所以會關閉加密。 透過此修正可啟用加密，但在發行期間永遠不會停用。 
    * 增加初始連線期間 Azure SQL Database 連線的重試計數和復原能力。
    * 如果預設檔案群組不是 PRIMARY，匯入/發佈至 Azure V12 將會失敗。 現在發行時會忽略此設定。
    * 已修正當匯出的資料庫含有啟用「引號識別項」的物件時，匯出驗證可能會在某些情況下失敗的問題。
    * 已修正在建立 Hekaton 資料表時不正確加入 TEXTIMAGE_ON 選項的問題，這是不允許的動作。
    * 已修正匯出在資料階段完成之後因為寫入 model.xml 檔案而花費很長的時間匯出大量資料，造成要重新寫入 .bacpac 檔案內容的問題。
    * 已修正使用者未出現在 Azure SQL DW 和 APS 連線之 [安全性] 資料夾的問題。

* **Analysis Services 與 Reporting Services：**
    * 已修正 MSOLAP OLEDB 提供者中只安裝 32 位元提供者，影響連線到 SQL Server 2014 的 64 位元 Excel 2016 的 SxS 問題 (未重現在從 Office365 的 ClickOnce 安裝，只重現在 MSI Excel 安裝)。
    * 修正將含有貼上資料表的 AS 模型從 1103 升級為 1200 相容性層級 (可能產生「關聯性使用無效資料行識別碼」錯誤) 時，要讓極端案例 (corner case) 更健全的問題。
    * 已修正當 SSDT-BI 2013 位於相同電腦上，解除安裝 SSDT 2015 之後可能無法再匯入 AS 模型中資料的 SxS 問題 (墨水匣共用登錄設定)。
    * 已改進當 AS 引擎的連線中斷 (亦即 SSDT 整夜開啟並回收 AS 伺服器，或連線暫時中斷的其他情況) 時解決問題/當機的穩固性。 
    * 已修正在多重監視器案例中，在不同於 VS 的螢幕上開啟對話方塊的問題。 
    * 已修正/啟用在 AS 模型貼上的資料表中從 HTML 資料表 (方格資料) 貼上的支援。 
    * 已修正升級無法將空白的貼上資料表升級為 1200 (只用來做為量值的容器資料表) 的問題。 
    * 已修正將含有貼上資料表的 AS 表格式模型升級為 1200 以使用 CalcTables (用於 1200 中的貼上資料表) 解決 AS 引擎問題，以便在升級之後直接對新的導出資料表執行處理序的問題。 
    * 已修正使用不完整的 DAX 運算式取消建立新的 AS 1200 模型導出資料表可能會當機的問題。 
    * 已修正當資料庫名稱和資料表名稱相同時，從 AS 伺服器將 1200 模型匯入至 SSDT AS 專案的問題。 
    * 已修正在 1103 表格式模型中編輯 KPI 量值的問題。 
    * 已修正在 AS 1200 模型的方格中貼上 KPI 量值時發生物件參考未設定的例外狀況。 
    * 已修正無法從 1200 模型中的圖表檢視刪除導出資料表中資料行的問題。 
    * 已修正在程式碼檢視中檢視 model.bim 專案檔屬性時發生物件參考未設定的例外狀況。 
    * 已修正將資料貼上 AS 模型方格以建立貼上的資料表會使用逗點做為小數點分隔符號在國際地區設定產生不正確值的問題。 
    * 已修正在 SSDT 中開啟 2008 RS 專案並選擇不升級的問題。 
    * 修正使用資料行類型的預設格式化允許從 UI 變更格式化類型時，在 1200 相容性層級模型的導出資料表 UI 中的問題。

## <a name="june-2016nbsp-ssdt-for-vs-2015"></a>2016 年 6 月，&nbsp;適用於 VS 2015 的 SSDT

發行日期：  &nbsp; 2016 年 6 月 1 日  
組建編號：  &nbsp; 14.0.60525.0  
適用於 SQL Server 2016。 

SSDT 公開上市 (GA) 現在已發行。 2016 年 6 月的 SSDT GA 更新加入 SQL Server 2016 RTM 之最新更新的支援，和各種錯誤 (bug) 修正。 如需詳細資訊，請參閱 [2016 年 6 月的 SQL Server Data Tools GA 更新 (英文)](/archive/blogs/ssdt/sql-server-data-tools-ga-update-for-june-2016)。

## <a name="additional-resources"></a>其他資源

- [下載 SQL Server Data Tools &#40;SSDT&#41;](../ssdt/download-sql-server-data-tools-ssdt.md)
- [舊版的 SQL Server Data Tools (SSDT 和 SSDT-BI)](../ssdt/previous-releases-of-sql-server-data-tools-ssdt-and-ssdt-bi.md)
- [Database Engine 的新功能](../sql-server/what-s-new-in-sql-server-2016.md)
- [Analysis Services 的新功能](/analysis-services/what-s-new-in-analysis-services)
- [Integration Services 的新功能](../integration-services/what-s-new-in-integration-services-in-sql-server-2016.md)
