---
title: (SSMS) 的版本資訊
description: SQL Server Management Studio (SSMS) 版本資訊。
ms.prod: sql
ms.prod_service: sql-tools
ms.technology: ssms
ms.topic: conceptual
ms.assetid: 3dc76cc1-3b4c-4719-8296-f69ec1b476f9
author: dzsquared
ms.author: drskwier
ms.reviewer: maghan
ms.custom: seo-lt-2019
ms.date: 12/17/2020
ms.openlocfilehash: 98211695d60592c8178c4b60e45f2105c3b86db4
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98102659"
---
# <a name="release-notes-for-sql-server-management-studio-ssms"></a>SQL Server Management Studio (SSMS) 版本資訊

[!INCLUDE [SQL Server ASDB, ASDBMI, ASDW ](../includes/applies-to-version/sql-asdb-asdbmi-asa.md)]

本文提供目前版本和舊版本之 SSMS 的更新、改善和 Bug 修正詳細資料。

## <a name="current-ssms-release"></a>目前的 SSMS 版本

### <a name="188"></a>18.8

![下載](media/download-icon.png) [下載 SSMS 18.8](download-sql-server-management-studio-ssms.md)

- 版本號碼：18.8
- 組建編號：15.0.18369.0
- 發行日期：2020 年 12 月 17 日

[簡體中文](https://go.microsoft.com/fwlink/?linkid=2151644&clcid=0x804) | [繁體中文](https://go.microsoft.com/fwlink/?linkid=2151644&clcid=0x404) | [英文 (美國)](https://go.microsoft.com/fwlink/?linkid=2151644&clcid=0x409) | [法文](https://go.microsoft.com/fwlink/?linkid=2151644&clcid=0x40c) | [德文](https://go.microsoft.com/fwlink/?linkid=2151644&clcid=0x407) | [義大利文](https://go.microsoft.com/fwlink/?linkid=2151644&clcid=0x410) | [日文](https://go.microsoft.com/fwlink/?linkid=2151644&clcid=0x411) | [韓文](https://go.microsoft.com/fwlink/?linkid=2151644&clcid=0x412) | [葡萄牙文 (巴西)](https://go.microsoft.com/fwlink/?linkid=2151644&clcid=0x416) | [俄文](https://go.microsoft.com/fwlink/?linkid=2151644&clcid=0x419) | [西班牙文](https://go.microsoft.com/fwlink/?linkid=2151644&clcid=0x40a)

SSMS 18.8 是 SSMS 最新的正式發行 (GA) 版本。 如果您需要舊版 SSMS，請參閱[舊版 SSMS](release-notes-ssms.md#previous-ssms-releases)。

#### <a name="whats-new-in-188"></a>18.8 的新功能

[!INCLUDE [ssms-ads-install](../includes/ssms-azure-data-studio-install.md)]

| 新項目 | 詳細資料 |
|----------|---------|
| Azure Data Studio 安裝整合 | 安裝 SSMS 會安裝 Azure Data Studio 1.25.1。 |
| Analysis Services | 已新增對 Power BI Premium Gen2 工作區的支援。 |
| 稽核 | 已新增對 EXTERNAL_MONITOR 與「操作員稽核」的支援。  如需詳細資訊，請參閱設定[文件](https://docs.microsoft.com/azure/azure-sql/managed-instance/auditing-configure) \(部分機器翻譯\)。 |
| Integration Services | 已改進 [IR 建立精靈]，因而在建立 SSIS IR 時建立 SSISDB 為選用的 |

#### <a name="bug-fixes-in-188"></a>18.8 中的錯誤 (Bug) 修正

| 新項目 | 詳細資料 |
|----------|---------|
| Accessibility | 已修正 [進度報告] 對話方塊中影響朗讀程式的問題 |
| 活動監視器 | 已修正 [使用中的高成本查詢] 方格中的 [編輯查詢文字] 操作功能表無法運作的問題 |
| Always Encrypted | 已修正 SSMS 可能擲回執行階段錯誤 (「找不到類別」) 的問題 |
| 高 DPI/調整 | 已改進 [進度報告] 對話方塊的 4K 顯示 |
| 高 DPI/調整 | 已修正 Integration Services 中「新增排程 - 套件」表單的問題 |
| 一般 SSMS |  已修正當嘗試顯示的資料行屬性具有超過 Bigint 大小的識別值種子或身分識別增量時，SSMS 可能擲回錯誤的問題。
| 一般 SSMS | 已修正 SSMS 中由功能表項目 [專案匯出範本…] 所造成的損毀 (其不應顯示) |
| 一般 SSMS | 已修正在列舉資料夾中的備份時可能造成 SSMS 損毀的問題 |
| 一般 SSMS | 已減緩在顯示查詢結果時造成 SSMS 流失控制代碼和記憶體的問題 |
| 一般 SSMS | 已修正使用者無法針對特定類型 (SQL 2014 與更新版本上的 UniqueIdentifier、SQL 2017 和更新版本上的 VarCharMax/NVarCharMax/VarBinaryMax) 的資料行建立叢集資料行存放區索引的問題 |
| 一般 SSMS | 已修正 [結果方格] 中 XML 資料行的重新排序作業無法運作的長時間待處理問題。 請參閱 [SQL Server 使用者意見反應](https://feedback.azure.com/forums/908035/suggestions/32890930) |
| 一般 SSMS | 已修正 SSMS 錯誤顯示「有記憶體最佳化物件的資料庫不支援變更追蹤」警告的問題。 |
| 產生指令碼精靈 | 已修正禁止在 SQL Server 2014 與更舊版本上列舉資料庫物件的錯誤 (Bug)。 請參閱 [SQL Server 使用者意見反應](https://feedback.azure.com/forums/908035-sql-server/suggestions/41885587)。 |
| 複寫工具 | 已修正 SQL 複寫監視器未接受自動連線設定，因而強制使用者每次手動重新連線的問題。 請參閱 [SQL Server 使用者意見反應](https://feedback.azure.com/forums/908035/suggestions/40394743) |
| SMO/指令碼 | 已修正嘗試在 SQL Azure DB 中已加密的檢視上使用 [產生指令碼...] 時發生的問題 |
| XEvent UI | 已修正從其他應用程式切換至 SSMS 時可能造成 SSMS 損毀的問題。 |
| XEvent UI | 已還原 18.7 的改進，其是針對在 XEvent 建立期間因為有額外引號而造成錯誤 (Bug) 所做的改進。 此變更是暫時的，該改進將在後續版本中回復，因而不會伴隨錯誤 (Bug)。 |

#### <a name="known-issues-188"></a>已知問題 (18.8)

| 新項目 | 詳細資料 | 因應措施 |
|----------|---------|------------|
| Analysis Services | 透過 msmdpump.dll 連線到 SSAS 時發生錯誤。 請參閱 [SQL Server 使用者意見反應](https://feedback.azure.com/forums/908035-sql-server/suggestions/40144696)。 | N/A |
| Analysis Services | 在罕見的情況下，使用升級安裝程式時，在升級 SSMS 後嘗試開啟 DAX 編輯器時可能會有「物件未設定成物件的執行個體」錯誤。 | 將 SQL Server Management Studio 解除安裝並重新安裝。  如果無法透過重新安裝解決，請關閉所有 SSMS 執行個體、進行備份，然後移除 `%AppData%\Microsoft\SQL Server Management Studio` 與 `%LocalAppData%\Microsoft\SQL Server Management Studio`。 |
| 一般 SSMS | [新的伺服器稽核規格] 對話方塊可能會導致 SSMS 損毀，並出現存取違規錯誤。 | N/A |
| 一般 SSMS | 使用 SMO 的 SSMS 延伸模組應以新 SSMS 特定 SMO v161 套件為目標重新編譯。 您可在 https://www.nuget.org/packages/Microsoft.SqlServer.SqlManagementObjects.SSMS/ 取得預覽版本 </br></br> 針對 Microsoft.SqlServer.SqlManagementObjects 套件 160 以前版本所編譯的延伸模組仍然會正常運作。 | N/A |
| Integration Services | 在 Integration Services 中匯入或匯出套件，或在 Azure-SSIS Integration Runtime 中匯出套件時，包含指令碼工作/元件的套件會遺失指令碼。 | 移除資料夾 "C:\Program Files (x86)\Microsoft SQL Server Management Studio 18\Common7\IDE\CommonExtensions\MSBuild"。 |
| Integration Services | 從遠端連線至 Integration Services 的作業可能會失敗，並出現「指定的服務不是以已安裝的服務形式存在。」 (在較新的作業系統上)。 | 在 Computer\HKEY_CLASSES_ROOT\AppID & Computer\HKEY_CLASSES_ROOT\ WOW6432Node\AppID 下找出 Integration Services 的相關登錄位置，然後在這些登錄區內，針對我們嘗試連線的特定 Integration Services 版本，將名為 'LocalService' 的登錄機碼重新命名為 'LocalService_A'得 |


針對其他已知問題，您可參考 [SQL Server 使用者意見反應](https://feedback.azure.com/forums/908035-sql-server)並為產品小組提供意見反應。

## <a name="previous-ssms-releases"></a>舊版 SSMS

[!INCLUDE[ssms-connect-aazure-ad](../includes/ssms-connect-azure-ad.md)]

選取相關區段中的下載連結，以下載舊版的 SSMS。

| SSMS 版本 | 組建編號 | 發行日期 |
|--------------|--------------|--------------|
| [18.7.1](#1871) | 15.0.18358.0 | 2020 年 10 月 27 日 |
| [18.7](#187) | 15.0.18357.0 | 2020 年 10 月 20 日 |
| [18.6](#186) | 15.0.18338.0 | 2020 年 7 月 22 日 |
| [18.5.1](#1851) | 15.0.18333.0 | 2020 年 6 月 9 日 |
| [18.5](#185) | 15.0.18330.0 | 2020 年 4 月 7 日 |
| [18.4](#184) | 15.0.18206.0 | 2019 年 11 月 4 日 |
| [18.3.1](#1831) | 15.0.18183.0 | 2019 年 10 月 2 日 |
| [18.2](#182) | 15.0.18142.0 | 2019 年 7 月 25 日 |
| [18.1](#181) | 15.0.18131.0 | 2019 年 6 月 11 日 |
| [18.0](#180) | 15.0.18118.0 | 2019 年 4 月 24 日 |
| [17.9.1](#1791) | 14.0.17289.0 | 2018 年 11 月 21 日 |
| [16.5.3](#1653) | 13.0.16106.4 | 2017 年 1 月 30 日 |

### <a name="1871"></a>18.7.1

![下載](media/download-icon.png) [下載 SSMS 18.7](https://go.microsoft.com/fwlink/?linkid=2147207)

- 版本號碼：18.7.1
- 組建編號：15.0.18358.0
- 發行日期：2020 年 10 月 27 日

[簡體中文](https://go.microsoft.com/fwlink/?linkid=2147207&clcid=0x804) | [繁體中文](https://go.microsoft.com/fwlink/?linkid=2147207&clcid=0x404) | [英文 (美國)](https://go.microsoft.com/fwlink/?linkid=2147207&clcid=0x409) | [法文](https://go.microsoft.com/fwlink/?linkid=2147207&clcid=0x40c) | [德文](https://go.microsoft.com/fwlink/?linkid=2147207&clcid=0x407) | [義大利文](https://go.microsoft.com/fwlink/?linkid=2147207&clcid=0x410) | [日文](https://go.microsoft.com/fwlink/?linkid=2147207&clcid=0x411) | [韓文](https://go.microsoft.com/fwlink/?linkid=2147207&clcid=0x412) | [葡萄牙文 (巴西)](https://go.microsoft.com/fwlink/?linkid=2147207&clcid=0x416) | [俄文](https://go.microsoft.com/fwlink/?linkid=2147207&clcid=0x419) | [西班牙文](https://go.microsoft.com/fwlink/?linkid=2147207&clcid=0x40a)

SSMS 18.7 是 SSMS 最新的正式發行 (GA) 版本。 如果您需要舊版 SSMS，請參閱[舊版 SSMS](release-notes-ssms.md#previous-ssms-releases)。

#### <a name="whats-new-in-1871"></a>18.7.1 的新功能

[!INCLUDE [ssms-ads-install](../includes/ssms-azure-data-studio-install.md)]


#### <a name="bug-fixes-in-1871"></a>18.7.1 的 Bug 修正

| 新項目 | 詳細資料 |
|----------|---------|
| 查詢存放區 | 在查詢存放區的 [物件總管] 節點上按一下滑鼠右鍵時，所擲回的已修正錯誤。 |


#### <a name="known-issues-1871"></a>已知問題 (18.7.1)

| 新項目 | 詳細資料 | 因應措施 |
|----------|---------|------------|
| Analysis Services | 透過 msmdpump.dll 連線到 SSAS 時發生錯誤。 請參閱 [SQL Server 使用者意見反應](https://feedback.azure.com/forums/908035-sql-server/suggestions/40144696)。 | N/A |
| Analysis Services | 在罕見的情況下，使用升級安裝程式時，在升級 SSMS 後嘗試開啟 DAX 編輯器時可能會有「物件未設定成物件的執行個體」錯誤。 | 若要解決此問題，請將 SSMS 解除安裝，然後再重新安裝。 |
| DAX 查詢編輯器 | 開啟 DAX 查詢編輯器會導致錯誤：「物件未設定至物件的執行個體」 | 將 SQL Server Management Studio 解除安裝並重新安裝。  如果無法透過重新安裝解決，請關閉所有 SSMS 執行個體、進行備份，然後移除 `%AppData%\Microsoft\SQL Server Management Studio` 與 `%LocalAppData%\Microsoft\SQL Server Management Studio`。 |
| 一般 SSMS | [新的伺服器稽核規格] 對話方塊可能會導致 SSMS 損毀，並出現存取違規錯誤。 | N/A |
| 一般 SSMS | 使用 SMO 的 SSMS 延伸模組應以新 SSMS 特定 SMO v161 套件為目標重新編譯。 您可在 https://www.nuget.org/packages/Microsoft.SqlServer.SqlManagementObjects.SSMS/ 取得預覽版本 </br></br> 針對 Microsoft.SqlServer.SqlManagementObjects 套件 160 以前版本所編譯的延伸模組仍然會正常運作。 | N/A |
| 產生指令碼精靈 | 嘗試列舉 SQL Server 2014 和較舊版本上的資料庫物件時，此精靈失敗。 請參閱 [SQL Server 使用者意見反應](https://feedback.azure.com/forums/908035-sql-server/suggestions/41885587)。 | 在 SQL 2014 和更舊版本的 [產生指令碼精靈] 中，使用 SSMS 18.6 來選取物件。 |
| Integration Services | 在 Integration Services 中匯入或匯出套件，或在 Azure-SSIS Integration Runtime 中匯出套件時，包含指令碼工作/元件的套件會遺失指令碼。 | 移除資料夾 "C:\Program Files (x86)\Microsoft SQL Server Management Studio 18\Common7\IDE\CommonExtensions\MSBuild"。 |
| Integration Services | 從遠端連線至 Integration Services 的作業可能會失敗，並出現「指定的服務不是以已安裝的服務形式存在。」 (在較新的作業系統上)。 | 在 Computer\HKEY_CLASSES_ROOT\AppID & Computer\HKEY_CLASSES_ROOT\ WOW6432Node\AppID 下找出 Integration Services 的相關登錄位置，然後在這些登錄區內，針對我們嘗試連線的特定 Integration Services 版本，將名為 'LocalService' 的登錄機碼重新命名為 'LocalService_A'得 |
| 物件總管 | 18.7 之前的 SSMS 版本在物件總管中有中斷性變更，其原因是與 [Azure Synapse Analytics 無伺服器 SQL 集區](https://docs.microsoft.com/azure/synapse-analytics/sql/on-demand-workspace-overview)相關的引擎變更。 | 若要繼續搭配 Azure Synapse Analytics 無伺服器 SQL 集區利用 SSMS 中的物件總管，您必須使用 SSMS 18.7 或更新版本。 |

### <a name="187"></a>18.7

![下載](media/download-icon.png) [下載 SSMS 18.7](https://go.microsoft.com/fwlink/?linkid=2146265)

- 版本號碼：18.7
- 組建編號：15.0.18357.0
- 發行日期：2020 年 10 月 20 日

[簡體中文](https://go.microsoft.com/fwlink/?linkid=2146265&clcid=0x804) | [繁體中文](https://go.microsoft.com/fwlink/?linkid=2146265&clcid=0x404) | [英文 (美國)](https://go.microsoft.com/fwlink/?linkid=2146265&clcid=0x409) | [法文](https://go.microsoft.com/fwlink/?linkid=2146265&clcid=0x40c) | [德文](https://go.microsoft.com/fwlink/?linkid=2146265&clcid=0x407) | [義大利文](https://go.microsoft.com/fwlink/?linkid=2146265&clcid=0x410) | [日文](https://go.microsoft.com/fwlink/?linkid=2146265&clcid=0x411) | [韓文](https://go.microsoft.com/fwlink/?linkid=2146265&clcid=0x412) | [葡萄牙文 (巴西)](https://go.microsoft.com/fwlink/?linkid=2146265&clcid=0x416) | [俄文](https://go.microsoft.com/fwlink/?linkid=2146265&clcid=0x419) | [西班牙文](https://go.microsoft.com/fwlink/?linkid=2146265&clcid=0x40a)

SSMS 18.7 是 SSMS 最新的正式發行 (GA) 版本。 如果您需要舊版 SSMS，請參閱[舊版 SSMS](release-notes-ssms.md#previous-ssms-releases)。

#### <a name="whats-new-in-187"></a>18.7 的新功能

[!INCLUDE [ssms-ads-install](../includes/ssms-azure-data-studio-install.md)]

| 新項目 | 詳細資料 |
|----------|---------|
| Azure Data Studio 安裝整合 | 安裝 SSMS 也會安裝 Azure Data Studio。 |
| Always Encrypted | 若要辨識新的 HSM 端點，您必須升級 SSMS。 這可透過取用新的 AKV 提供者 NugetPackage 來達成。 |
| 匯入一般檔案 | 已做出改善，來透過預設學習 300 行來改善預測資料類型的效果。 |
| 匯入一般檔案 | 防止將應該為 Smallint 的資料行宣告為 Tinyint。 |
| 匯入一般檔案 | 已做出改善，使得在資料匯入中發生失敗的情況下確保會正確清除 DW 資料表。 |
| 資源管理員 | 已新增對十進位值的支援。 |
| 執行程序表 | 已新增 PREDICT 運算子。 |
| XEvent UI | 已新增使用 wait_type 名稱來編寫擴充事件指令碼的功能。 使用者要求在 wait_type 篩選述詞中使用 map_value 資料行 (而非 map_key) 的值，因為金鑰值會在版本升級期間變更。 修正：已新增核取方塊並給予使用者選項，以選擇要針對 wait_type 篩選述詞值使用 map_value 或 map_key。 |

#### <a name="bug-fixes-in-187"></a>18.7 中的錯誤 (Bug) 修正

| 新項目 | 詳細資料 |
|----------|---------|
| Accessibility | 已修正會在針對 DW 進行查詢時顯示的「您嘗試連接的資料庫不支援下列設定」視窗中按鈕的定位順序。 |
| 協助工具選項 | 匯入及匯出精靈：頁面配置在高 DPI 模式中不正確。 |
| 活動監視器 | 已修正活動監視器會在開啟 [處理序] 索引標籤時暫停的問題。請參閱 [SQL Server 使用者意見反應](https://feedback.azure.com/forums/908035/suggestions/37050118) \(英文\)。 |
| Always On 可用性群組 | 已修正讀取縮放可用性群組容錯移轉無法運作的問題。 |
| Analysis Services | 已針對 AS 表格式模型案例將 PowerQuery 元件更新至 2.84.982。 |
| Analysis Services | 已修正在嘗試透過 msmdpump.dll 連線到 SSAS 時可能會導致錯誤的問題。 請參閱 [SQL Server 使用者意見反應](https://feedback.azure.com/forums/908035-sql-server/suggestions/40144696) \(英文\)。 |
| 備份/還原 | 已修正選取 [檢視連線屬性] 會導致 HostDistribution 屬性針對 SQL 2016 及更早版本遺失之 SMO 錯誤的問題。 |
| 資料庫設計工具 | 已修正問題，其會導致 SSMS 在處理十進位數字時損毀。 |
| 資料庫圖表 | 已修正在使用資料庫圖表時，[新增資料表] 對話方塊未正確顯示，因而導致 SSMS 損毀或停止回應的問題。 |
| 資料庫鏡像 | 已修正造成鏡像設定失敗的問題。 請參閱 [SQL Server 使用者意見反應](https://feedback.azure.com/forums/908035-sql-server/suggestions/32897281) \(英文\)。 |
| 一般 SSMS | 已修正嘗試連線到 Azure SQL DB 可能會需要數秒鐘時間的問題 (使用者資料庫中的 SQL 登入)。 |
| 一般 SSMS | 已修正 SSMS 無法處理/顯示所擷取鎖死 (.xdl 檔案) 的問題。 |
| 一般 SSMS | 已修正嘗試開啟 SQL Server 2008 R2 及更低版本的錯誤記錄檔設定時，會因找不到 ErrorLogSizeKb 屬性而失敗的問題。 |
| 一般 SSMS | 針對 [Azure Synapse Analytics 無伺服器 SQL 集區](/azure/synapse-analytics/sql/on-demand-workspace-overview)支援的一般修正和改善。 |
| 匯入一般檔案 | 已修正精靈偵測不到檔案可能正由另一個應用程式使用，而改為擲回錯誤的問題。 請參閱 [SQL Server 使用者意見反應](https://feedback.azure.com/forums/908035/suggestions/40761574) \(英文\)。 |
| 匯入/匯出資料層應用程式 | 已將匯入 bacpac 時的預設服務層級修正為標準 S0 (與 Azure 入口網站和 SqlPackage.exe 行為相同)。 |
| 匯入一般檔案 | 已修正精靈偵測不到檔案可能正由另一個應用程式使用，而改為擲回錯誤的問題。 請參閱 [SQL Server 使用者意見反應](https://feedback.azure.com/forums/908035/suggestions/40761574) \(英文\)。 |
| Integration Services | 已修正在不同的訂用帳戶具有相同名稱的情況下，[Azure 訂用帳戶] 下拉式方塊項目會在 [IR 建立] 精靈和 [作業移轉] 精靈中重複的問題。 |
| Integration Services | 已修正 [連線] 按鈕有時候無法在 [IR 建立] 精靈中啟用的問題。 |
| Integration Services | 已修正 [設定參數值] 對話方塊中 [使用環境變數] 下拉式方塊的項目順序錯誤的問題。 |
| Intellisense | 已修正 SSMS 中可能會在執行查詢時發生的損毀。 |
| Intellisense | 已修正在使用者連線到針對唯讀路由設定的可用性群組時，IntelliSense 無法運作的問題。 |
| 連結的伺服器 | 已修正具有 CONTROL SERVER 權限 (但不是 sysadmin 角色) 的使用者無法新增連結的伺服器的問題。 |
| 記錄檢視器 | 已修正具有 VIEW SERVER STATE 權限的使用者無法檢視 SQL Server 錯誤記錄檔的問題。 請參閱 [SQL Server 使用者意見反應](https://feedback.azure.com/forums/908035/suggestions/32899204) \(英文\)。 |
| 物件總管 | 已修正在某些物件總管節點 (例如 [原則管理]、[擴充事件]) 上選取 [啟動 PowerShell] 功能表會導致 PowerShell 無法正確啟動的問題。 |
| 已註冊的伺服器 | 已修正 SSMS 會在嘗試註冊中央管理伺服器時損毀的問題。 |
| 已註冊的伺服器 | 已修正從已註冊的伺服器啟動 Azure Data Studio 的功能表項目遺失的問題。 |
| 報表 | 已修正在 [效能儀表板] 上嘗試瀏覽至子連結 (例如 [佔用大量資源的查詢]) 時無法正常執行的問題。 此問題常見於大部分非英文版本的 SSMS。 請參閱 [SQL Server 使用者意見反應](https://feedback.azure.com/forums/908035/suggestions/41454499) \(英文\)。 |
| 執行程序表 | 已修正在使用 [尋找節點] 來搜尋文字時會導致 SSMS 損毀的問題。 請參閱 [SQL Server 使用者意見反應](https://feedback.azure.com/forums/908035/suggestions/40421650) \(英文\)。 |
| 執行程序表 | 在 [記憶體授與] 工具提示列上新增 KB 尾碼 |
| 弱點評量 | 已修正導致 SSMS 在 [弱點評量] 中嘗試設定基準時擲回錯誤的問題。 請參閱 [SQL Server 使用者意見反應](https://feedback.azure.com/forums/908035/suggestions/40578565) \(英文\)。 |
| XEvent UI | 已修正按下 F1 不會移至 DOCS 上正確頁面的問題。 |
| XEvent UI | 已修正 XEvent 檢視器中的記錄，其中工具提示不會顯示包含使用代理字組編碼之文字的正確文字。 |

#### <a name="known-issues-187"></a>已知問題 (18.7)

| 新項目 | 詳細資料 | 因應措施 |
|----------|---------|------------|
| Analysis Services | 透過 msmdpump.dll 連線到 SSAS 時發生錯誤。 請參閱 [SQL Server 使用者意見反應](https://feedback.azure.com/forums/908035-sql-server/suggestions/40144696)。 | N/A |
| Analysis Services | 在罕見的情況下，使用升級安裝程式時，在升級 SSMS 後嘗試開啟 DAX 編輯器時可能會有「物件未設定成物件的執行個體」錯誤。 | 若要解決此問題，請將 SSMS 解除安裝，然後再重新安裝。 |
| 一般 SSMS | [新的伺服器稽核規格] 對話方塊可能會導致 SSMS 損毀，並出現存取違規錯誤。 | N/A |
| 一般 SSMS | 使用 SMO 的 SSMS 延伸模組應以新 SSMS 特定 SMO v161 套件為目標重新編譯。 您可在 https://www.nuget.org/packages/Microsoft.SqlServer.SqlManagementObjects.SSMS/ 取得預覽版本 </br></br> 針對 Microsoft.SqlServer.SqlManagementObjects 套件 160 以前版本所編譯的延伸模組仍然會正常運作。 | N/A |
| 產生指令碼精靈 | 嘗試列舉 SQL Server 2014 和較舊版本上的資料庫物件時，此精靈失敗。 請參閱 [SQL Server 使用者意見反應](https://feedback.azure.com/forums/908035-sql-server/suggestions/41885587)。 | 在 SQL 2014 和更舊版本的 [產生指令碼精靈] 中，使用 SSMS 18.6 來選取物件。 |
| Integration Services | 在 Integration Services 中匯入或匯出套件，或在 Azure-SSIS Integration Runtime 中匯出套件時，包含指令碼工作/元件的套件會遺失指令碼。 因應措施：移除資料夾 "C:\Program Files (x86)\Microsoft SQL Server Management Studio 18\Common7\IDE\CommonExtensions\MSBuild"。 | N/A |
| Integration Services | 從遠端連線至 Integration Services 的作業可能會失敗，並出現「指定的服務不是以已安裝的服務形式存在。」 (在較新的作業系統上)。 因應措施：在 Computer\HKEY_CLASSES_ROOT\AppID & Computer\HKEY_CLASSES_ROOT\ WOW6432Node\AppID 下找出 Integration Services 的相關登錄位置，然後在這些登錄區內，針對我們嘗試連線的特定 Integration Services 版本，將名為 'LocalService' 的登錄機碼重新命名為 'LocalService_A'得 | N/A |
| 物件總管 | 18.7 之前的 SSMS 版本在物件總管中有中斷性變更，其原因是與 [Azure Synapse Analytics 無伺服器 SQL 集區](/azure/synapse-analytics/sql/on-demand-workspace-overview)相關的引擎變更。 | 若要繼續搭配 Azure Synapse Analytics 無伺服器 SQL 集區利用 SSMS 中的物件總管，您需要 SSMS 18.7 或更新版本。 |
| 查詢存放區 | 查詢存放區的 [物件總管] 節點會在按一下滑鼠右鍵時擲回錯誤。 | 展開節點並以滑鼠右鍵按一下個別的子選項，可直接存取項目。 |

### <a name="186"></a>18.6

![下載](media/download-icon.png) [下載 SSMS 18.6](https://go.microsoft.com/fwlink/?linkid=2135491)

- 版本號碼：18.6
- 組建編號：15.0.18338.0
- 發行日期：2020 年 7 月 22 日

[簡體中文](https://go.microsoft.com/fwlink/?linkid=2135491&clcid=0x804) | [繁體中文](https://go.microsoft.com/fwlink/?linkid=2135491&clcid=0x404) | [英文 (美國)](https://go.microsoft.com/fwlink/?linkid=2135491&clcid=0x409) | [法文](https://go.microsoft.com/fwlink/?linkid=2135491&clcid=0x40c) | [德文](https://go.microsoft.com/fwlink/?linkid=2135491&clcid=0x407) | [義大利文](https://go.microsoft.com/fwlink/?linkid=2135491&clcid=0x410) | [日文](https://go.microsoft.com/fwlink/?linkid=2135491&clcid=0x411) | [韓文](https://go.microsoft.com/fwlink/?linkid=2135491&clcid=0x412) | [葡萄牙文 (巴西)](https://go.microsoft.com/fwlink/?linkid=2135491&clcid=0x416) | [俄文](https://go.microsoft.com/fwlink/?linkid=2135491&clcid=0x419) | [西班牙文](https://go.microsoft.com/fwlink/?linkid=2135491&clcid=0x40a)

#### <a name="whats-new-in-186"></a>18.6 的新功能

| 新項目 | 詳細資料 |
|----------|---------|
| Analysis Services | 更新至最新的 AS 用戶端程式庫版本。 |
| 稽核 | 新增 SENSITIVE_BATCH_COMPLETED_GROUP 動作識別碼的支援 (字串而非數字)。 |
| 稽核 | 將下列欄位新增到稽核檢視器：affected_rows、response_rows、connection_id、duration_milliseconds，以及 data_sensitivity_information。 |
| 資料分類 | 更新 SSMS 以支援匯入/匯出透過 PowerShell Cmdlet 匯出的原則。 |
| 匯入一般檔案 | 新增固定寬度檔案的支援以及 .csv/.tsv 檔案的檔案類型偵測，確保這些檔案會分別作為 csv/tsv 檔案剖析。 |
| Integration Services | 新增 Azure SQL 受控執行個體代理程式作業，以執行 Azure SSIS IR 中封裝存放區的 SSIS 套件。 |
| SMO/指令碼 | 新增在 [Azure Synapse Analytics](/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is) (先前稱為 SQL Azure DW) 上撰寫動態資料遮罩指令碼的支援。 |
| SMO/指令碼 | 已新增在 [Azure Synapse Analytics](/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is) 上撰寫安全性原則指令碼的支援。 |

#### <a name="bug-fixes-in-186"></a>18.6 中的 Bug 修正

| 新項目 | 詳細資料 |
|----------|---------|
| Accessibility | 針對協助工具，調整 [資料庫一般屬性頁面] 上的框線色彩 (格線和名稱方塊周圍的框線已調成較暗，使對比大於 3:1)。 |
| Accessibility | 新增查詢執行的處理，以更新朗讀程式 (需要在電腦上安裝 NetFx 4.8 以上版本)。 |
| Always Encrypted | 修正 [新增資料行加密金鑰] 對話方塊在 CMK 已啟用記憶體保護區的情況下，仍指出 CEK 並未啟用記憶體保護區的問題。 |
| Analysis Services | 修正檢視 Analysis Services 分割區時可能會造成未處理例外狀況的問題。 |
| **資料庫圖表** | 修正 **資料庫圖表** 造成現有圖表及 SSMS 損毀的長期問題。 若使用 SSMS 18.0 到 18.5.1 建立或儲存了圖表，且該圖表包含「文字註解」，則將無法在任何 SSMS 版本中開啟該圖表。 透過此修正，SSMS 18.6 可開啟及儲存 SSMS 17.9.1 及先前版本所建立的圖表。 SSMS 17.9.1 及先前版本也可在使用 SSMS 18.6 儲存該圖表後開啟圖表。 請參閱 [SQL Server 使用者意見反應](https://feedback.azure.com/forums/908035/suggestions/37992649)。 |
| 資料分類 | 修正資料行名稱不會在資料分類窗格其建議面板中顯示的問題。 |
| 一般 SSMS | 修正針對 SQL Azure DB (超大規模資料庫服務層級) 中資料庫屬性「大小」和「可用空間」值不正確的問題。 |
| 一般 SSMS | 修正資料庫屬性「大小」顯示大小上限，而非 SQL Azure DB 資料庫實際大小的問題 (注意：針對 DW，其仍會顯示大小上限)。 |
| 一般 SSMS | 解決 SSMS 中造成停止回應的三個常見原因。 |
| 一般 SSMS | 修正與 SSMS 連線對話方塊「忘記」項目 (伺服器/使用者/密碼) 相關的幾個問題。 請參閱 [SQL Server 使用者意見反應](https://feedback.azure.com/forums/908035/suggestions/40256401)和 [SQL Server 使用者意見反應](https://feedback.azure.com/forums/908035/suggestions/40015519)。 |
| 一般 SSMS | 修正 [統計資料屬性] 對話方塊在選取 [更新這些資料行的統計資料] 核取方塊並選取 [確定] 時沒有任何效果的問題。 統計資料不會更新，且嘗試撰寫動作指令碼會產生「沒有任何要撰寫指令碼的動作」訊息)。 請參閱 [SQL Server 使用者意見反應](https://feedback.azure.com/forums/908035/suggestions/37799992)。 |
| 一般 SSMS | 已解決 [CVE-2020-1455](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2020-1455) 的相關問題。 | 
| 匯入/匯出資料層應用程式 | 修正 SSMS 在匯入 bacpac 檔案時擲回錯誤的問題。 請參閱 [SQL Server 使用者意見反應](https://feedback.azure.com/forums/908035/suggestions/40229137)。 |
| Integration Services | 修正客戶在使用 SSMS 版本 18.4 或更早版本於 Azure SQL 受控執行個體中執行 SSIS 套件時，無法編輯 SQL 代理程式作業的錯誤 (Bug)。 |
| Integration Services | 修正在內部部署 SQL Server 的 SQL 代理程式作業中執行 SSIS 套件的 [執行選項] 索引標籤內遺失 [使用 32 位元執行階段] 選項的 Bug。 |
| Intellisense/編輯器 | 修正在執行 [檔案] -> [新增] -> [資料庫引擎查詢] 時可能會彈出錯誤對話方塊的問題。 |
| 物件總管 | 修正在 [物件總管] 中的資料表或索引節點上以滑鼠右鍵按一下時，無法使用 Azure SQL Database [屬性視窗] 的問題。 |
| 物件總管 | 解決控制平面發生影響 sys.database_service_objectives 的中斷時 SSMS 無法展開 master 資料庫節點此問題。 |
| 報表 | 修正在 Linux 上中斷的數個標準報表 </br></br> 範例：記憶體耗用量報表因為與「/var/opt/mssql/log/log_116.trc\log.trc 無效…」相似的錯誤而失敗。 |
| SMO/指令碼 | 已更新在 SQL Azure Database 中建立新資料庫的邏輯，以使用 Gen5_2 作為預設 SLO。 |
| Xevent UI | 修正「儲存到 XEL 檔案…」擲回錯誤的長期問題 (在 SSMS 18.0 中首次出現)。 請參閱 [SQL Server 使用者意見反應](https://feedback.azure.com/forums/908035/suggestions/37695592)。 |

#### <a name="known-issues-186"></a>已知問題 (18.6)

| 新項目 | 詳細資料 | 因應措施 |
|----------|---------|------------|
| Analysis Services | 透過 msmdpump.dll 連線到 SSAS 時發生錯誤。 請參閱 [SQL Server 使用者意見反應](https://feedback.azure.com/forums/908035-sql-server/suggestions/40144696)。 | N/A |
| 一般 SSMS | [新的伺服器稽核規格] 對話方塊可能會導致 SSMS 損毀，並出現存取違規錯誤。 | N/A |
| 一般 SSMS | 使用 SMO 的 SSMS 延伸模組應以新 SSMS 特定 SMO v161 套件為目標重新編譯。 您可在 https://www.nuget.org/packages/Microsoft.SqlServer.SqlManagementObjects.SSMS/ 取得預覽版本 </br></br> 針對 Microsoft.SqlServer.SqlManagementObjects 套件 160 以前版本所編譯的延伸模組仍然會正常運作。 | N/A |
| Integration Services | 在 Integration Services 中匯入或匯出套件，或在 Azure-SSIS Integration Runtime 中匯出套件時，包含指令碼工作/元件的套件會遺失指令碼。 因應措施：移除資料夾 "C:\Program Files (x86)\Microsoft SQL Server Management Studio 18\Common7\IDE\CommonExtensions\MSBuild"。 | N/A|
| Integration Services | 從遠端連線至 Integration Services 的作業可能會失敗，並出現「指定的服務不是以已安裝的服務形式存在。」 (在較新的作業系統上)。 因應措施：在 Computer\HKEY_CLASSES_ROOT\AppID & Computer\HKEY_CLASSES_ROOT\ WOW6432Node\AppID 下找出 Integration Services 的相關登錄位置，然後在這些 Hive 內，將名為 'LocalService' 的登錄機碼重新命名為 'LocalService_A'，以取得我們嘗試連線的特定 Integration Services 版本 | N/A|

### <a name="1851"></a>18.5.1

![下載](media/download-icon.png) [下載 SSMS 18.5.1](https://go.microsoft.com/fwlink/?linkid=2132606)

- 版本號碼：18.5.1
- 組建編號：15.0.18333.0
- 發行日期：2020 年 6 月 9 日

[簡體中文](https://go.microsoft.com/fwlink/?linkid=2132606&clcid=0x804) | [繁體中文](https://go.microsoft.com/fwlink/?linkid=2132606&clcid=0x404) | [英文 (美國)](https://go.microsoft.com/fwlink/?linkid=2132606&clcid=0x409) | [法文](https://go.microsoft.com/fwlink/?linkid=2132606&clcid=0x40c) | [德文](https://go.microsoft.com/fwlink/?linkid=2132606&clcid=0x407) | [義大利文](https://go.microsoft.com/fwlink/?linkid=2132606&clcid=0x410) | [日文](https://go.microsoft.com/fwlink/?linkid=2132606&clcid=0x411) | [韓文](https://go.microsoft.com/fwlink/?linkid=2132606&clcid=0x412) | [葡萄牙文 (巴西)](https://go.microsoft.com/fwlink/?linkid=2132606&clcid=0x416) | [俄文](https://go.microsoft.com/fwlink/?linkid=2132606&clcid=0x419) | [西班牙文](https://go.microsoft.com/fwlink/?linkid=2132606&clcid=0x40a)

#### <a name="bug-fixes-in-1851"></a>18.5.1 中的 Bug 修正

| 新項目 | 詳細資料 |
|----------|---------|
| Analysis Services | 改善連線到 AS Azure 或 Power BI 伺服器時展開資料庫清單的效能。 |
| Analysis Services | 修正嘗試開啟 Analysis Serverices 伺服器的 [同步資料庫精靈] 時發生錯誤此問題。 |
| Analysis Services | 修正讓使用者無法使用行動數據權限查詢 SSAS 2017 及更早版本的問題。 |
| 一般 SSMS | [資料表設計工具 - 修正嘗試在資料表設計工具格線中嘗試 TAB 時發出的嗶聲](https://feedback.azure.com/forums/908035/suggestions/40318435) |

#### <a name="known-issues-1851"></a>已知問題 (18.5.1)

| 新項目 | 詳細資料 | 因應措施 |
|----------|---------|------------|
| 一般 SSMS | [圖表設計] 具有一個已知錯誤 (Bug)，其會造成現有的圖表損毀。 例如，您使用 SSMS 17.9.1 建立圖表設計，然後使用 SSMS 18.x 予以更新/儲存，並稍後使用 17.9.1 來嘗試開啟。 請參閱 [SQL Server 使用者意見反應](https://feedback.azure.com/forums/908035/suggestions/37992649)以取得詳細資料。 | N/A |
| 一般 SSMS | [新的伺服器稽核規格] 對話方塊可能會導致 SSMS 損毀，並出現存取違規錯誤。 | N/A ||
| SMO/指令碼 | 使用 SMO 的 SSMS 延伸模組需要以新的 SMO v160 為目標重新編譯。 | N/A |
| Integration Services | 在 Integration Services 中匯入或匯出套件，或在 Azure-SSIS Integration Runtime 中匯出套件時，包含指令碼工作/元件的套件會遺失指令碼。 因應措施： | 移除資料夾 "C:\Program Files (x86)\Microsoft SQL Server Management Studio 18\Common7\IDE\CommonExtensions\MSBuild"。 |

### <a name="185"></a>18.5

![下載](media/download-icon.png) [下載 SSMS 18.5](https://go.microsoft.com/fwlink/?linkid=2125901)

- 版本號碼：18.5
- 組建編號：15.0.18330.0
- 發行日期：2020 年 4 月 7 日

[簡體中文](https://go.microsoft.com/fwlink/?linkid=2125901&clcid=0x804) | [繁體中文](https://go.microsoft.com/fwlink/?linkid=2125901&clcid=0x404) | [英文 (美國)](https://go.microsoft.com/fwlink/?linkid=2125901&clcid=0x409) | [法文](https://go.microsoft.com/fwlink/?linkid=2125901&clcid=0x40c) | [德文](https://go.microsoft.com/fwlink/?linkid=2125901&clcid=0x407) | [義大利文](https://go.microsoft.com/fwlink/?linkid=2125901&clcid=0x410) | [日文](https://go.microsoft.com/fwlink/?linkid=2125901&clcid=0x411) | [韓文](https://go.microsoft.com/fwlink/?linkid=2125901&clcid=0x412) | [葡萄牙文 (巴西)](https://go.microsoft.com/fwlink/?linkid=2125901&clcid=0x416) | [俄文](https://go.microsoft.com/fwlink/?linkid=2125901&clcid=0x419) | [西班牙文](https://go.microsoft.com/fwlink/?linkid=2125901&clcid=0x40a)

#### <a name="whats-new-in-185"></a>18.5 的新功能

| 新項目 | 詳細資料 |
|----------|---------|
| Analysis Services | 已新增 Analysis Services 中對 Power BI 端點的支援 - Azure Analysis Services 的比對功能。 |
| Analysis Services | 分析工具：已新增 Analysis Services 追蹤定義 15.1 的支援。 |
| 資料分類 | 在 VA 掃描結果檢視中加入一個按鈕，以便透過前往 [資料分類] 窗格來補救資料分類規則。 |
| 資料分類 | 已新增資料分類中對敏感度等級的支援。 |
| 超大規模資料庫 | 已新增 SQL Azure HyperScale 的 [匯入資料層應用程式]  \(.bacpac\) 支援。 |
| Integration Services | 支援從 MI 代理程式作業中的檔案系統執行 SSIS 套件。 |
| Integration Services | 對於設定已啟用 Azure 的 DTExec 在 Azure-SSIS Integration Runtime 上叫用 SSIS 套件執行，進行了使用者友善的改善。
| Integration Services | 支援連線 Azure-SSIS Integration Runtime，以及管理或執行套件存放區中的 SSIS 套件。
| Integration Services | 支援將內部部署 SSIS 代理程式作業移轉至 ADF 管線和觸發程式。
| Integration Services | 改善從 SSIS DB 匯出 SSIS 專案的使用者體驗。 相較於 SSIS 專案中已載入和已升級套件的舊匯出，與版本無關的新匯出將不會載入和升級 SSIS 專案中的套件。 相反地，除了將保護等級變更為 EncryptSensitiveWithUserKey 之外，其會將套件保留在專案中，如同在 SSIS DB 中一樣。 |
| SMO/指令碼 | 新的 DwMaterializedViewDistribution 屬性已新增至 View 物件。 |
| SMO/指令碼 | 已移除 [功能限制]  的支援 (此預覽功能已從 SQL Azure 和內部部署 SQL 移除)。 |
| SMO/指令碼 | 已將 [筆記本]  新增為 [產生指令碼] 精靈的目的地。 |
| SMO/指令碼 | 已新增對「Azure Synapse Analytics 無伺服器 SQL 集區」的支援。 |
| SMO/指令碼 | [SQL 評定 API](../tools/sql-assessment-api/sql-assessment-api-overview.md) - Platform、Name 和 engineEdition 欄位現在可以包含一般逗號分隔清單 (平台  ：\[*Windows*、*Linux*\])，而不只是規則運算式 (平台  ： *\/Windows\|Linux\/* )
| SMO/指令碼 | [SQL 評定 API](../tools/sql-assessment-api/sql-assessment-api-overview.md) - 已新增 13 個評定規則。 如需詳細資訊，請移至 [GitHub](https://github.com/microsoft/sql-server-samples/tree/master/samples/manage/sql-assessment-api))。 |

#### <a name="bug-fixes-in-185"></a>18.5 中的錯誤 (Bug) 修正

| 新項目 | 詳細資料 |
|----------|---------|
| Accessibility | SSIS ADF / 新增排程：已修正 [新增排程]  精靈下，朗讀程式的掃描模式中，焦點順序不合邏輯的問題。 |
| Accessibility | 延展資料庫精靈：已修正當提供資料表的相關資訊時，螢幕助讀程式不會通知查詢資料表名稱的問題。 |
| Analysis Services | 使用 Azure AD 連線在 AS 中撰寫指令碼時，修正快取的連線。 |
| 永遠開啟 | 已修正新增至 Always On AG 的第一個資料庫未正確聯結的問題。
| 永遠開啟 | 已修正當連線到巨量資料叢集端點時，嘗試顯示儀表板時會顯示錯誤的問題。 |
| 稽核 | 已修正當儲存體帳戶的根資料夾中有一個資料夾具有空白名稱時，[稽核記錄合併] 視窗當機的問題。 |
| 稽核 | 已修正當容器的根目錄中有太多項目時，[稽核記錄合併] 視窗不會顯示所有伺服器的問題。 |
| 資料分類 | 已修正針對具有大量資料表的資料庫，[資料分類]  精靈無法開啟的問題。 |
| 資料分類 | 我們現在會針對驗證程序中的每個標籤/infoType 和 GUID 結構強制執行不同的 GUID。 |
| 資料分類 | 移除 SqlServer2019 中的分類程序。 |
| 資料分類 | 更正先前的驗證測試 (加入順位、移除不合法的屬性 *InformationTypes*)，並為前兩點新增新的驗證測試。 |
| 資料分類 | 現在，[已分類的資料行] 資料表上方的按鈕可以最小化 [建議] 面板。 |
| 一般 SSMS | 更新 MSODBC 和 MSOLEDB 驅動程式的版本。 |
| 一般 SSMS | 已解決 SSMS 中至少有兩個常見來源停止回應和當機。 |
| 一般 SSMS | 已解決在選取 [瀏覽] 按鈕時，[還原]  對話方塊停止回應的情況。 |
| 一般 SSMS | 已修正適用於 Azure Synapse Analytics 無伺服器 SQL 集區的「新資料庫 GUI」。 |
| 一般 SSMS | 已修正 Azure Synapse Analytics 無伺服器 SQL 集區的 [新增外部資料表] 與 [新增外部資料來源] 範本。 |
| 一般 SSMS | 已修正適用於 Azure Synapse Analytics 無伺服器 SQL 集區的資料庫屬性、連線屬性、隱藏報表與重新命名。 |
| 一般 SSMS | Always Encrypted：已修正在選取新的已啟用記憶體保護區金鑰時，金鑰名稱下拉式清單變為唯讀的問題。 |
| 一般 SSMS | 已清除 [資料庫屬性選項]  格線，其中顯示兩個「其他類別」  。 |
| 一般 SSMS | 已修正捲軸從「資料庫屬性選項」格線中間開始的問題。 |
| 一般 SSMS | 已修正在連線至 Analysis Services 伺服器時，開啟 .sql 檔案時導致 SSMS 損毀的問題。 |
| 一般 SSMS | 連線對話方塊：已修正取消選取 [記住密碼] 無法運作的問題。 |
| 一般 SSMS | 已修正與伺服器/使用者相關聯的認證一律會被記憶的問題。 請參閱 [SQL Server 使用者意見反應](https://feedback.azure.com/forums/908035/suggestions/37875172)。 |
| 一般 SSMS | 已修正偶爾不會正確重新整理 [編輯器] 視窗的問題。 這是藉由停用 [工具] > [選項] > [環境]  中的硬體加速來達成。 請參閱 [SQL Server 使用者意見反應](https://feedback.azure.com/forums/908035/suggestions/37474042)。 |
| 一般 SSMS | 已修正 Azure Active Directory 驗證無法透過 Proxy 運作的問題。 |
| 高 DPI/調整 | 已修正「索引屬性」  上的控制項可能錯誤地轉譯 (按鈕重疊格線) 的問題。 請參閱 [SQL Server 使用者意見反應](https://feedback.azure.com/forums/908035/suggestions/36030424)。 |
| 高 DPI/調整 | 已修正 [資料庫屬性]  對話方塊中可能會在 4K 監視器上顯示裁剪的控制項的多個問題。 |
| 高 DPI/調整 | 已修正 4K 顯示上的已修正發行集和訂閱精靈。 |
| 高 DPI/調整 | [新的伺服器稽核規格] 頁面上的次要修正。 |
| 高 DPI/調整 | 已修正高可用性精靈上的 4K 顯示問題。 |
| 高 DPI/調整 | 已修正當顯示縮放比例為 125% 時，使用者無法在 Xevent [新增工作階段] 視窗中新增目標，以及在 Xevent 工作階段精靈中設定工作階段事件篩選器的問題。 |
| 高 DPI/調整 | 修正將比例縮放到 100% 以上時，[備份資料庫到 URL]  UI 上的控制項無法顯示的問題。 |
|匯入一般檔案 | 已更新一般檔案匯入精靈，以允許選取全部允許 Null 資料行。 請參閱 [SQL Server 使用者意見反應](https://feedback.azure.com/forums/908035/suggestions/38027137)。 |
| 物件總管 | 已修正在 [連線] 對話方塊中使用連接字串連線時，物件總管可能會顯示不正確的資訊的問題。 |
| 物件總管 | 已修正在擴充具有數千個資料表 (20k+) 之資料庫時，OE 展開資料表之速度緩慢的問題。 |
| 查詢存放區 UI | 已修正 TRC 報表將執行計數 (針對「等候時間」  計量) 計算為每個單獨等待類別的執行計數之和，這是不正確的。 但是針對查詢的單一執行，其會針對查詢所等候的每個等候類別註冊。 因此，如果 TRC 只是將其在等候類別中加總，其將使執行計數膨脹。 實際上，這應該是 wait_category 的最大值。 |
| 查詢存放區 UI | 已修正 TRC 詳細資料檢視會在頂端 x 篩選結果集時傳回不正確的資料。 這是因為查詢會使用多個通用資料表運算式，然後將其聯結在一起，以建立最終的結果集。 如果將頂端 x 推送至 CTE，有時可能篩選掉必要的資料列。 這有時會使結果集不具決定性。 修正方法是不要將頂端 x 子句推送至 CTE。 |
| 查詢存放區 UI | 已修正兩者中的計劃摘要 - 格線或圖表檢視需要最後一次查詢執行等候時間。 缺少此資料行將中斷查詢。 此變更集會將此資料行新增至等候統計資料 CTE。 |
| 執行程序表 | 已改善 SSMS 如何針對具有多個執行的運算子顯示估計的資料列計數：(1) 將 SSMS 中的 [估計的資料列數目]  修改為 [所有執行估計的資料列數目]；(2) 新增屬性 [每次執行估計的資料列數目]  ；(3) 將屬性 [實際資料列數目]  修改為 [所有執行的實際資料列數目]  。 |
| SQL Agent | 已修正嘗試編輯 SQL 代理程式作業步驟可能導致 SSMS UI 凍結的問題。 SSMS 現在允許檢視 ([檢視]  按鈕) 其名稱已 Token 化的 output_file (至少針對 SQL Agent 所支援，在執行階段未確定的簡單巨集/權杖)。 此外，當使用者沒有檔案的存取權時 (就 SQL 權限而言)，SSMS 也不會停用 [檢視] 按鈕。 請參閱 [SQL Server 使用者意見反應](https://feedback.azure.com/forums/908035/suggestions/39063124)。 |
| SQL Agent | 已修正 [作業步驟] 頁面上的索引標籤順序。 |
| SQL Agent | 已調換 [作業步驟] 頁面上 [下一步] 和 [上一步] 按鈕的位置，以邏輯順序加以放置。 |
| SQL Agent | 已調整 [作業排程] 視窗，以不裁剪 UI。 |
| SMO/指令碼 | 已修正適用於 Azure Synapse Analytics 無伺服器 SQL 集區的資料庫指令碼撰寫。 |
| SMO/指令碼 | 移除明確的 sqlvariant 強制轉換 (SqlOnDemand 不合法的 T-SQL 語法)，可修正 SqlOnDemand 的指令碼。 |
| SMO/指令碼 | 已修正略過 SQL Azure 索引上 FILLFACTOR 的問題。 |
| SMO/指令碼 | 已修正與指令碼外部物件有關的問題。 |
| SMO/指令碼 | 已修正 [產生指令碼] 不允許為針對 SQL Database 的 [擴充屬性] 選擇指令碼選項的問題。 此外，也已修正這類擴充屬性的指令碼。 |
| SMO/指令碼 | [SQL 評定 API](../tools/sql-assessment-api/sql-assessment-api-overview.md) - XTPHashAvgChainBuckets 規則中有錯誤的說明連結。 |
| XEvent UI | 已修正格線中的項目在游標暫留時選取的問題。 請參閱 [SQL Server 使用者意見反應](https://feedback.azure.com/forums/908035/suggestions/38262124)及 [SQL Server 使用者意見反應](https://feedback.azure.com/forums/908035-sql-server/suggestions/37873921)。 |

#### <a name="known-issues-185"></a>已知問題 (18.5)

| 新項目 | 詳細資料 | 因應措施 |
|----------|---------|------------|

- 從電腦 A 上所執行 SSMS 建立的資料庫圖表無法從電腦 B 進行修改 (SSMS 當機)。 請參閱 [SQL Server 使用者意見反應 37992649](https://feedback.azure.com/forums/908035/suggestions/37992649) 以取得詳細資料。

- 在 Integration Services 中匯入或匯出套件，或在 Azure-SSIS Integration Runtime 中匯出套件時，包含指令碼工作/元件的套件會遺失指令碼。 解決方法是移除資料夾 *C:\Program Files (x86)\Microsoft SQL Server Management Studio 18\Common7\IDE\CommonExtensions\MSBuild*。

- [新的伺服器稽核規格] 對話方塊可能會導致 SSMS 損毀，並出現存取違規錯誤。

- 使用 SMO 的 SSMS 延伸模組必須以新的 SMO v160 為目標重新編譯 (該套件將在 SSMS 18.5 發行後立即在 nuget.org 上提供)。

- [透過 msmdpump.dll 連線到 SSAS 時發生錯誤](https://feedback.azure.com/forums/908035-sql-server/suggestions/40144696-error-when-connecting-to-ssas-via-msmdpump-dll-in)。

### <a name="184"></a>18.4

![下載](media/download-icon.png) [下載 SSMS 18.4](https://go.microsoft.com/fwlink/?linkid=2108895)

- 版本號碼：18.4
- 組建編號：15.0.18206.0
- 發行日期：2019 年 11 月 4 日

[簡體中文](https://go.microsoft.com/fwlink/?linkid=2108895&clcid=0x804) | [繁體中文](https://go.microsoft.com/fwlink/?linkid=2108895&clcid=0x404) | [英文 (美國)](https://go.microsoft.com/fwlink/?linkid=2108895&clcid=0x409) | [法文](https://go.microsoft.com/fwlink/?linkid=2108895&clcid=0x40c) | [德文](https://go.microsoft.com/fwlink/?linkid=2108895&clcid=0x407) | [義大利文](https://go.microsoft.com/fwlink/?linkid=2108895&clcid=0x410) | [日文](https://go.microsoft.com/fwlink/?linkid=2108895&clcid=0x411) | [韓文](https://go.microsoft.com/fwlink/?linkid=2108895&clcid=0x412) | [葡萄牙文 (巴西)](https://go.microsoft.com/fwlink/?linkid=2108895&clcid=0x416) | [俄文](https://go.microsoft.com/fwlink/?linkid=2108895&clcid=0x419) | [西班牙文](https://go.microsoft.com/fwlink/?linkid=2108895&clcid=0x40a)

| 新項目 | 詳細資料 |
|----------|---------|
| 資料分類 | 已新增資料分類的自訂資訊保護原則支援。 |
| 查詢存放區 | 已新增對話方塊屬性中的「每個查詢的計劃上限」  值。 |
| 查詢存放區 | 已新增新自訂擷取原則的支援。 |
| 查詢存放區 | 在 [查詢存放區]  的 [資料庫屬性]  選項中，新增了 [等候統計資料擷取模式]  。 |
| SMO/指令碼 | 支援 SQL DW 中具體化檢視的指令碼。 |
| SMO/指令碼 | 已新增對「Azure Synapse Analytics 無伺服器 SQL 集區」的支援。 |
| SMO/指令碼 | [SQL 評定 API](../tools/sql-assessment-api/sql-assessment-api-overview.md) - 已新增 50 個評定規則 (請參閱 GitHub 上的詳細資料)。 |
| SMO/指令碼 | [SQL 評定 API](../tools/sql-assessment-api/sql-assessment-api-overview.md) - 已新增基底數學運算式和規則條件的比較。 |
| SMO/指令碼 | [SQL 評定 API](../tools/sql-assessment-api/sql-assessment-api-overview.md) - 已新增 RegisteredServer 物件的支援。 |
| SMO/指令碼 | [SQL 評定 API](../tools/sql-assessment-api/sql-assessment-api-overview.md) - 已更新 JSON 格式儲存規則的方式，並已更新套用覆寫/自訂的機制。 |
| SMO/指令碼 | [SQL 評定 API](../tools/sql-assessment-api/sql-assessment-api-overview.md) - 已更新支援 Linux 上 SQL 的規則。 |
| SMO/指令碼 | [SQL 評定 API](../tools/sql-assessment-api/sql-assessment-api-overview.md) - 已更新規則集 JSON 格式，並已新增結構描述版本。 |
| SMO/指令碼 | [SQL 評定 API](../tools/sql-assessment-api/sql-assessment-api-overview.md) - 已更新 Cmdlet 輸出以改善建議的可讀性。 |
| XEvent 分析工具 | 已將 *error_reported* 事件新增至 XEvent Profiler 工作階段。 |

#### <a name="bug-fixes-in-184"></a>18.4 中的 Bug 修正

| 新項目 | 詳細資料 |
|----------|---------|
| Analysis Services | 已修正多維度資料庫 DAX 指令碼編輯器未在 IntelliSense 中顯示資料表的問題。 |
| Analysis Services | 使用 DAX 剖析器轉換成引擎字串。 這適用於國際分隔符號、十進位和空白字元。 |
| Always Encrypted | 已修正問題：「宣告驗證」  不會「區分大小寫」  。 |
| Always Encrypted | 已修正問題：錯誤/警告報告未正常運作。 |
| 複製資料庫精靈 | 已修正轉譯此對話方塊時的各種截斷和版面配置問題。 |
| 一般 SSMS | 已修正長期未決的問題：同時指定 SQL 檔案時，SSMS 未遵循命令列中傳遞的連線資訊。 |
| 一般 SSMS | 已修正在嘗試顯示「複寫篩選器」物件上的安全性實體時，SSMS 中的損毀。 |
| 一般 SSMS | 藉由讓 SSMS 查看其認證的快取，以減輕移除 -P 命令列選項的風險：如果找到了所需的認證，則會將其用於建立連接。 |
| 匯入一般檔案 | 已修正「匯入一般檔案」  功能未正確處理文字限定詞的問題。 |
| 物件總管 | 已修正卸除 [物件總管] 中 Azure SQL Database 時顯示不正確訊息的問題。 |
| 查詢結果 | 正在修正 SSMS 18.3.1 中的問題：格線有點細，每個資料行最長字串的結尾都會顯示 *...* 。 |
| 複寫工具 | 已修正問題：嘗試編輯 SQL Agent 作業時，發生應用程式擲回錯誤 (「無法載入檔案或組件...」)。 |
| SMO/指令碼 | 已修正「指令碼資料表為...」時的問題  定序為 Japanese_BIN2 的 SQL DW 無法運作。|
| SMO/指令碼 | 已修正 ScriptAlter() 最後在伺服器上執行陳述式的問題。|
| SQL Agent | 已修正問題：代理程式運算子 UI 在 UI 中變更時不會更新運算子名稱，也不會被寫入指令碼。 請參閱 [SQL Server 使用者意見反應](https://feedback.azure.com/forums/908035/suggestions/32897647)以取得詳細資料。|

#### <a name="known-issues-184"></a>已知問題 (18.4)

- 從電腦 A 上所執行 SSMS 建立的資料庫圖表無法從電腦 B 進行修改 (SSMS 當機)。 請參閱 [SQL Server 使用者意見反應](https://feedback.azure.com/forums/908035/suggestions/37992649)以取得詳細資料。

- 在多個查詢視窗間進行切換時的重繪問題。 請參閱 [SQL Server 使用者意見反應](https://feedback.azure.com/forums/908035/suggestions/37474042)以取得詳細資料。 此問題的因應措施是在 [工具] > [選項] 下停用硬體加速。

針對其他已知問題，您可參考 [SQL Server 使用者意見反應](https://feedback.azure.com/forums/908035-sql-server)並為產品小組提供意見反應。

### <a name="1831"></a>18.3.1

![下載](media/download-icon.png) [下載 SSMS 18.3.1](https://go.microsoft.com/fwlink/?linkid=2105412)

- 版本號碼：18.3.1
- 組建編號：15.0.18183.0
- 發行日期：2019 年 10 月 2 日

[簡體中文](https://go.microsoft.com/fwlink/?linkid=2105412&clcid=0x804) | [繁體中文](https://go.microsoft.com/fwlink/?linkid=2105412&clcid=0x404) | [英文 (美國)](https://go.microsoft.com/fwlink/?linkid=2105412&clcid=0x409) | [法文](https://go.microsoft.com/fwlink/?linkid=2105412&clcid=0x40c) | [德文](https://go.microsoft.com/fwlink/?linkid=2105412&clcid=0x407) | [義大利文](https://go.microsoft.com/fwlink/?linkid=2105412&clcid=0x410) | [日文](https://go.microsoft.com/fwlink/?linkid=2105412&clcid=0x411) | [韓文](https://go.microsoft.com/fwlink/?linkid=2105412&clcid=0x412) | [葡萄牙文 (巴西)](https://go.microsoft.com/fwlink/?linkid=2105412&clcid=0x416) | [俄文](https://go.microsoft.com/fwlink/?linkid=2105412&clcid=0x419) | [西班牙文](https://go.microsoft.com/fwlink/?linkid=2105412&clcid=0x40a)

#### <a name="whats-new-in-1831"></a>18.3.1 的新功能

| 新項目 | 詳細資料 |
|----------|---------|
| 資料分類 | 將資料分類資訊新增到資料行屬性 UI (SSMS UI 中不會公開 [資訊類型]  、[資訊類型識別碼]  、[敏感度標籤]  和 [敏感度標籤識別碼]  )。 |
| IntelliSense/編輯器 | 更新最近新增至 SQL Server 2019 功能 (例如，*ALTER SERVER CONFIGURATION*) 的支援。 |
| Integration Services | 新增新的選項功能表項目 `Tools > Migrate to Azure > Configure Azure-enabled DTExec`，此項目會在 Azure-SSIS Integration Runtime 上叫用執行 SSIS 套件，以作為 ADF 管線中的執行 SSIS 套件活動。 |
| SMO/指令碼 | 新增 Azure SQL DW 唯一條件約束支援指令碼的支援。 |
| SMO/指令碼 | 資料分類 </br> - 新增 SQL 版本 10 (SQL 2008) 及更高版本的支援。 </br> - 新增 SQL 版本 15 (SQL 2019) 及更高版本，以及 Azure SQL Database 的新敏感性屬性 'rank'。 |
| SMO/指令碼 | [SQL 評定 API](../tools/sql-assessment-api/sql-assessment-api-overview.md) - 將版本設定新增至規則集格式。 |
| SMO/指令碼 | [SQL 評定 API](../tools/sql-assessment-api/sql-assessment-api-overview.md) - 新增新的檢查。 |
| SMO/指令碼 | [SQL 評定 API](../tools/sql-assessment-api/sql-assessment-api-overview.md) - 新增 Azure SQL 受控執行個體的支援。 |
| SMO/指令碼 | [SQL 評定 API](../tools/sql-assessment-api/sql-assessment-api-overview.md) - 更新 Cmdlet 的預設檢視，將結果顯示為資料表。 |

#### <a name="bug-fixes-in-1831"></a>18.3.1 中的 Bug 修正

| 新項目 | 詳細資料 |
|----------|---------|
| Analysis Services | 修正 MDX 查詢編輯器中的縮放問題。|
| Analysis Services | 修正 XEvent UI 中的問題，該問題會讓使用者無法建立新的工作階段。 |
| Azure SQL Database 的資料庫部署 | 修正問題 (DacFx 中)，該問題會造成此功能無法運作。|
| 一般 SSMS | 修正在 XEvent 檢視器中使用排序功能會造成 SSMS 當機的問題。 |
| 一般 SSMS | 修正 SSMS 還原資料庫可能會無限期停止回應的長期問題。 </br></br> 請參閱 SQL Server 使用者意見反應項目以取得詳細資料： </br> [Restore Database - Select Backup Devices Slow to Load](https://feedback.azure.com/forums/908035/suggestions/32899099/) (還原資料庫 - 選取備份裝置載入緩慢)。  </br> [SSMS 2016 very slow in the database restore dialogs](https://feedback.azure.com/forums/908035/suggestions/32900767/) (SSMS 2016 在資料庫還原對話方塊中非常緩慢)。 </br> [Restoring database is slow](https://feedback.azure.com/forums/908035/suggestions/32900224/) (還原資料庫相當緩慢)。  </br> [Restore Database from Device HANGS on clicking "..."](https://feedback.azure.com/forums/908035/suggestions/34281658/) (從裝置還原資料庫在按一下 "..." 時停止回應)。  |
| 一般 SSMS | 修正將所有登入之預設語言顯示為阿拉伯文的問題。 </br></br> 請參閱 SQL Server 使用者意見反應項目以取得詳細資料：[SSMS 18.2 default language display bug](https://feedback.azure.com/forums/908035/suggestions/38236363) (SSMS 18.2 預設語言顯示 Bug)。 |
| 一般 SSMS | 藉由將其設為可調整大小來修正難以查看 [查詢選項]  對話方塊 (使用者以滑鼠右鍵按一下 T-SQL 編輯器視窗時) 的問題。|
| 一般 SSMS | 結果方格/檔案 (於 SSMS 18.2 中引進) 中可見的 [完成時間]  訊息現在可以在 [工具] > [選項] > [查詢執行] > [SQL Server] > [進階] > [顯示完成時間] 中進行設定。 |
| 一般 SSMS | 在 [連線] 對話方塊中，分別將 [Active Directory - 密碼]  和 [Active Directory - 整合]  替換成 [Azure Active Directory - 密碼]  和 [Azure Active Directory - 整合]  。 |
| 一般 SSMS | 修正位於具有負值 UTC 時差的時區時，使用者無法使用 SSMS 在 SQL Azure 受控執行個體上設定稽核的問題。 |
| 一般 SSMS | 修正 XEvent UI 中，暫留在方格上會選取資料列的問題。 </br></br> 請參閱 SQL Server 使用者意見反應項目以取得詳細資料：[SSMS Extended Events UI Selects Actions on Hover](https://feedback.azure.com/forums/908035/suggestions/38262124) (SSMS 延伸事件 UI 在暫留時會選取動作)。 |
| 匯入一般檔案 | 已修正問題：讓使用者選擇簡易或豐富的資料類型偵測時，「匯入一般檔案」不會匯入所有資料。</br></br> 請參閱 SQL Server 使用者意見反應項目以取得詳細資料：[SSMS Import Flat File fails to import all data](https://feedback.azure.com/forums/908035/suggestions/38096989) (SSMS 匯入一般檔案無法匯入所有資料)。 |
| Integration Services | 為 SSIS 作業報表新增新的作業類型 *StartNonCatalogExecution*。|
| Integration Services | 已修正 Azure 啟用的 `DTExec` 公用程式所產生 Azure Data Factory 管線中問題，以使用正確的參數類型。 (明確用於 18.3.1) |
| SMO/指令碼 | 修正造成 SMO 使用 **SMO.Server.SetDefaultInitFields(true)** 擷取屬性時擲回錯誤的問題。|
| 查詢存放區 UI | 已修正在 [追蹤查詢]  檢視中選取 [執行計數]  計量時，Y 軸無法調整大小的問題。 |
| 弱點評量 | 停用 Azure SQL Database 的清除和核准基準。|

#### <a name="known-issues-1831"></a>已知問題 (18.3.1)

- 從電腦 A 上所執行 SSMS 建立的資料庫圖表無法從電腦 B 進行修改 (SSMS 當機)。 請參閱 [SQL Server 使用者意見反應](https://feedback.azure.com/forums/908035/suggestions/37992649)以取得詳細資料。

- 在多個查詢視窗間進行切換時的重繪問題。 請參閱 [SQL Server 使用者意見反應](https://feedback.azure.com/forums/908035/suggestions/37474042)以取得詳細資料。 此問題的因應措施是在 [工具] > [選項] 下停用硬體加速。

針對其他已知問題，您可參考 [SQL Server 使用者意見反應](https://feedback.azure.com/forums/908035-sql-server)並為產品小組提供意見反應。

### <a name="182"></a>18.2

![下載](media/download-icon.png) [下載 SSMS 18.2](https://go.microsoft.com/fwlink/?linkid=2099720)

- 版本號碼：18.2
- 組建編號：15.0.18142.0
- 發行日期：2019 年 7 月 25 日

[簡體中文](https://go.microsoft.com/fwlink/?linkid=2099720&clcid=0x804) | [繁體中文](https://go.microsoft.com/fwlink/?linkid=2099720&clcid=0x404) | [英文 (美國)](https://go.microsoft.com/fwlink/?linkid=2099720&clcid=0x409) | [法文](https://go.microsoft.com/fwlink/?linkid=2099720&clcid=0x40c) | [德文](https://go.microsoft.com/fwlink/?linkid=2099720&clcid=0x407) | [義大利文](https://go.microsoft.com/fwlink/?linkid=2094583&clcid=0x410) | [日文](https://go.microsoft.com/fwlink/?linkid=2099720&clcid=0x411) | [韓文](https://go.microsoft.com/fwlink/?linkid=2099720&clcid=0x412) | [葡萄牙文 (巴西)](https://go.microsoft.com/fwlink/?linkid=2099720&clcid=0x416) | [俄文](https://go.microsoft.com/fwlink/?linkid=2099720&clcid=0x419) | [西班牙文](https://go.microsoft.com/fwlink/?linkid=2099720&clcid=0x40a)

#### <a name="whats-new-in-182"></a>18.2 的新功能

| 新項目 | 詳細資料 |
|----------|---------|
| Integration Services (SSIS) | Azure 中 SSIS 套件排程器的效能最佳化。 |
| IntelliSense/編輯器 | 新增資料分類的支援。 |
| OPTIMIZE_FOR_SEQUENTIAL_KEY | 新增 Intellisense 支援。 |
| OPTIMIZE_FOR_SEQUENTIAL_KEY | 可在能協助改善對索引進行高並行插入之輸送量的資料庫引擎內，開啟最佳化。 此選項適用於可能出現最後一頁插入競爭的索引，通常是具有循序鍵 (例如識別資料行、序列或日期/時間資料行) 的索引。 如需詳細資料，請參閱 [CREATE INDEX](../t-sql/statements/create-index-transact-sql.md#sequential-keys)。 |
| 查詢執行或結果 | 在訊息中新增了「完成時間」  ，用來追蹤指定的查詢何時完成其執行。 |
| 查詢執行或結果 | 允許顯示更多的資料 (結果為文字) 並將其儲存在資料格 (結果為方格) 中。 SSMS 現在兩者允許最多 2M 個字元 (分別自 256 K 和 64 K 起)。 這也解決了使用者無法從方格資料格抓取超過 43680 個字元的問題。 |
| 執行程序表 | 啟用[內嵌純量 UDF 功能](../relational-databases/performance/intelligent-query-processing.md#scalar-udf-inlining) (ContainsInlineScalarTsqludfs) 時，在 QueryPlan 中新增了新屬性。 |
| SMO | 已新增 *SQL 評定 API* 的支援。 如需詳細資訊，請參閱 [SQL 評定 API](../tools/sql-assessment-api/sql-assessment-api-overview.md)。 |
|  |  |

#### <a name="bug-fixes-in-182"></a>18.2 中的 Bug 修正

| 新項目 | 詳細資料 |
|------------|-------|
| Accessibility | 按 F3 將 XEvent UI (方格) 更新為可排序。 |
| 永遠開啟 | 修正 SSMS 在嘗試刪除可用性群組 (AG) 時擲回錯誤的問題 |
| 永遠開啟 | 已修正當使用讀取級別 AG (叢集類型=NONE) 時，SSMS 在複本設定為同步時呈現錯誤容錯移轉精靈的問題。 現在，SSMS 會為 Force_Failover_Allow_Data_Loss 選項提供精靈，這是唯一允許叢集類型 NONE 可用性的選項 |
| 永遠開啟 | 修正精靈將所允許同步處理數目限制為三個的問題 |
| 資料分類 | 修正當嘗試在資料庫上以 CompatLevel 小於 150 的設定檢視資料分類報表時，SSMS 擲回「索引 (以零為基底) 必須大於或等於零」  錯誤的問題。 |
| 一般 SSMS | 修正使用者無法透過滑鼠滾輪水平捲動 [結果] 窗格的問題。 請參閱 [SQL Server 使用者意見反應](https://feedback.azure.com/forums/908035/suggestions/34145641)以取得詳細資料。 |
| 一般 SSMS | 更新「活動監視器」  以忽略良性等候類型 SQLTRACE_WAIT_ENTRIES |
| 一般 SSMS | 已修正未保存某些色彩選項「([文字編輯器] > [編輯器] 索引標籤和狀態列)」  的問題。 請參閱 [SQL Server 使用者意見反應](https://feedback.azure.com/forums/908035/suggestions/37924165)
| 一般 SSMS | 在 [連線] 對話方塊中，以「具 MFA 支援的 Azure Active Directory - 通用」  取代「具 MFA 的 Azure Active Directory - 通用」  (功能相同，但希望這比較不會令人混淆)。 |
| 一般 SSMS | 已更新在建立 Azure SQL Database 時，SSMS 使用正確的預設值。 |
| 一般 SSMS | 已修正問題：當伺服器為 [SQL Linux 容器](../linux/quickstart-install-connect-docker.md)時，使用者無法從[註冊伺服器](register-servers/register-servers.md)中的節點「啟動 PowerShell」  。 |
| 匯入一般檔案 | 已修正問題：從 SSMS 18.0 升級至 18.1 之後，「匯入一般檔案」  無法運作。 請參閱 [SQL Server 使用者意見反應](https://feedback.azure.com/forums/908035/suggestions/37912636) |
| 匯入一般檔案 | 修正「[匯入一般檔案精靈] 回報 .csv 檔案有重複或不正確的資料行」  ，且標頭具有 Unicode 字元的問題。 |
| 物件總管 | 已修正當連線到 SQL Express 時，部分功能表項目 (例如，SQL Server [匯入和匯出精靈]  ) 遺失或停用的問題。 請參閱 [SQL Server 使用者意見反應](https://feedback.azure.com/forums/908035/suggestions/37500016)以取得詳細資料。 |
| 物件總管 | 已修正當物件從 [物件總管] 拖曳至編輯器時，造成 SSMS 損毀的問題。 請參閱 [SQL Server 使用者意見反應](https://feedback.azure.com/forums/908035/suggestions/37887988)以取得詳細資料。 |
| 物件總管 | 修正重新命名資料庫導致 [物件總管] 顯示不正確資料庫名稱的問題。 請參閱 [SQL Server 使用者意見反應](https://feedback.azure.com/forums/908035/suggestions/37638472)以取得詳細資料。 |
| 物件總管 | 已修正一個長時間未解決的問題，即嘗試在 [物件總管] 中針對資料庫展開「資料表」  節點，而該資料庫設定為使用 Windows 不再支援的定序時，就會觸發錯誤 (且使用者無法展開其資料表)。 這類定序的範例是 Korean_Wansung_Unicode_CI_AS。 |
| [註冊伺服器](register-servers/register-servers.md) | 已修正當已註冊伺服器使用「Active Directory - 整合式」  或「具 MFA 支援的 Azure Active Directory - 通用」  時，嘗試對多部伺服器 (在已註冊伺服器中的「群組」  底下) 發出查詢的作業，可能因 SSMS 無法連線而無法正常運作的問題。 |
| [註冊伺服器](register-servers/register-servers.md) | 修正當已註冊伺服器使用「Active Directory - 密碼」  或「SQL 驗證」  ，且使用者選擇不要記住密碼時，嘗試對多部伺服器 (在已註冊伺服器中的「群組」  底下) 發出查詢的作業，可能會造成 SSMS 損毀的問題。 |
| 報表 | 修正「磁碟使用量」  報表中當資料檔案具有大量範圍時，報表會失敗的問題。 |
| 複寫工具 | 已修正問題：複寫監視器無法使用 AG 中發行者 DB 和 AG 的散發者 (SSMS 17.x 之前已修正此問題) |
| SQL Agent | 已修正新增、插入、編輯或移除作業步驟，導致焦點重設為第一個資料列，而不是使用中資料列的問題。 請參閱 [SQL Server 使用者意見反應](https://feedback.azure.com/forums/908035/suggestions/38070892)以取得詳細資料。 |
| SMO/指令碼 | 已修正問題：*CREATE OR ALTER* 不會針對具有擴充屬性的物件撰寫指令碼。 請參閱 [SQL Server 使用者意見反應](https://feedback.azure.com/forums/908035-sql-server/suggestions/37236748)以取得詳細資料。 |
| SMO/指令碼 | 修正 SSMS 無法正確編寫 CREATE EXTERNAL LIBRARY 指令碼的問題。 請參閱 [SQL Server 使用者意見反應](https://feedback.azure.com/forums/908035/suggestions/37868089)以取得詳細資料。 |
| SMO/指令碼 | 修正嘗試對具有數千個資料表之資料庫執行「產生指令碼」  的問題 (導致進度對話方塊似乎停滯)。 |
| SMO/指令碼 | 已修正 SQL 2019 上「外部資料表」  指令碼無法正常運作的問題。 |
| SMO/指令碼 | 已修正 SQL 2019 上「外部資料來源」  指令碼無法正常運作的問題。 請參閱 [SQL Server 使用者意見反應](https://feedback.azure.com/forums/908035/suggestions/34295080)以取得詳細資料。 |
| SMO/指令碼 | 已修正當目標為 Azure SQL Database 時，不會撰寫資料行上「擴充屬性」的問題。 如需詳細資料，請參閱 [stackoverflow](https://stackoverflow.com/questions/56952337/how-can-i-script-the-descriptions-of-columns-in-ms-sql-server-management-studio)。 |
| SMO/指令碼 | 最後一頁插入：SMO - 新增屬性 *Index.IsOptimizedForSequentialKey* |
|**SSMS 安裝程式**| **減輕 SSMS 安裝程式不正確地封鎖 SSMS 安裝，使其無法回報不相符語言的問題。在某些異常情況下，這可能會是個問題，例如中止的安裝程式或錯誤解除安裝舊版 SSMS。請參閱 [SQL Server 使用者意見反應](https://feedback.azure.com/forums/908035/suggestions/37483594/)以取得詳細資料。** |
| XEvent 分析工具 | 修正當檢視器關閉時發生的損毀問題。 |

#### <a name="known-issues-182"></a>已知問題 (18.2)

- 從電腦 A 上執行的 SSMS 所建立資料庫圖表無法從電腦 B 進行修改 (它會造成 SSMS 損毀)。 請參閱 [SQL Server 使用者意見反應](https://feedback.azure.com/forums/908035/suggestions/37992649)以取得詳細資料。

- 在多個查詢視窗間進行切換時的重繪問題。 請參閱 [SQL Server 使用者意見反應](https://feedback.azure.com/forums/908035/suggestions/37474042)。 此問題的因應措施是停用 [工具]   > [選項]  下方的硬體加速。

- 您從方格、文字或檔案中顯示的 SSMS 結果所看到資料大小具有限制。

- 有一個刪除 [物件總管] 中的 Azure SQL Database 時發生錯誤，但實際上是成功的問題。

- 在 [登入屬性] 對話方塊中，不論是針對登入所設定的實際預設語言為何，SQL 登入的預設語言都可能會顯示為阿拉伯文。 若要檢視指定登入的實際預設語言，請使用 T-SQL 從 **master.sys.server_principles** 中選取登入的 **default_language_name**。

### <a name="181"></a>18.1

![下載](media/download-icon.png) [下載 SSMS 18.1](https://go.microsoft.com/fwlink/?linkid=2094583)

- 版本號碼：18.1
- 組建編號：15.0.18131.0
- 發行日期：2019 年 6 月 11 日

[簡體中文](https://go.microsoft.com/fwlink/?linkid=2094583&clcid=0x804) | [繁體中文](https://go.microsoft.com/fwlink/?linkid=2094583&clcid=0x404) | [英文 (美國)](https://go.microsoft.com/fwlink/?linkid=2094583&clcid=0x409) | [法文](https://go.microsoft.com/fwlink/?linkid=2094583&clcid=0x40c) | [德文](https://go.microsoft.com/fwlink/?linkid=2094583&clcid=0x407) | [義大利文](https://go.microsoft.com/fwlink/?linkid=2094583&clcid=0x410) | [日文](https://go.microsoft.com/fwlink/?linkid=2094583&clcid=0x411) | [韓文](https://go.microsoft.com/fwlink/?linkid=2094583&clcid=0x412) | [葡萄牙文 (巴西)](https://go.microsoft.com/fwlink/?linkid=2094583&clcid=0x416) | [俄文](https://go.microsoft.com/fwlink/?linkid=2094583&clcid=0x419) | [西班牙文](https://go.microsoft.com/fwlink/?linkid=2094583&clcid=0x40a)

#### <a name="whats-new-in-181"></a>18.1 的新功能

| 新項目 | 詳細資料 |
| :-------| :------|
| 資料庫圖表 | [將資料庫圖表新增回 SSMS](https://feedback.azure.com/forums/908035/suggestions/37507828)。
| SSBDIAGNOSE.EXE |SQL Server 診斷 (命令列工具) 已新增回 SSMS 套件。|
| Integration Services (SSIS) | 支援在 Azure 中，排程位於 Azure 或檔案系統中 SSIS 目錄裡的 SSIS 套件。 有三種啟動新增排程對話方塊的項目：[新增排程...]  功能表項目 (以滑鼠右鍵按一下 Azure 中 SSIS 目錄內的 SSIS 套件時顯示)、位於 [工具] 功能表項目下方 [移轉到 Azure] 功能表項目下的 [排程 Azure 中的 SSIS 套件]，以及以滑鼠右鍵按一下 Azure SQL 受控執行個體 SQL Server 代理程式下方的 Jobs 資料夾時所顯示的「排程 Azure 中的 SSIS」。|

#### <a name="bug-fixes-in-181"></a>18.1 中的錯誤修正

| 新項目 | 詳細資料 |
|---------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Accessibility | 改善代理程式作業 UI 的協助工具。 |'
| Accessibility | 已改善 Stretch 監視器頁面上的協助工具，除了新增 [自動重新整理]  按鈕的可存取名稱，也新增智慧型可存取名稱，可協助使用者知道其所在按鈕和按鈕的作用。 |
| ADS 整合| 嘗試使用 ADS 已註冊的伺服器時，可能發生的 SSMS 當機問題已修正。|
| 資料庫設計工具 | 新增 Latin1_General_100_BIN2_UTF8 集合的支援 (適用於 SQL Server 2019 CTP3.0) |
| 資料分類 | 防止將敏感度標籤新增至記錄資料表中的資料行，不支援該行為。 |
| 資料分類 | 已解決未正確處理相容性層級 (伺服器與資料庫) 的問題。 |
| 資料庫設計工具 | 新增 Latin1_General_100_BIN2_UTF8 集合的支援 (適用於 SQL2019 CTP3.0)。 |
| 一般 SSMS | 已修正下列問題：索引中的虛擬資料行指令碼不正確。 |
| 一般 SSMS | 已修正下列問題：在 [登入屬性] 頁面中按一下 [新增認證]  按鈕可能會擲回 Null 參考例外狀況。 |
| 一般 SSMS | 已修正索引屬性頁面中的貨幣資料行大小顯示。 |
| 一般 SSMS | 已修正下列問題：SSMS 不接受 SQL 編輯器視窗 [工具/選項]  中的 [IntelliSense] 設定。 |
| 一般 SSMS | 已修正 SSMS 不接受 [說明] 設定 (線上與離線) 的問題。 |
| 高 DPI | 修正未支援查詢選項中，錯誤對話方塊的控制項配置。 |
| 高 DPI | 修正某些當地語系化版本的 SSMS 上，[新增可用性群組]  頁面中的控制項配置。 |
| 高 DPI | 修正 [新增作業排程]  頁面的配置。 請參閱 [SQL Server 使用者意見反應](https://feedback.azure.com/forums/908035/suggestions/37632094)以取得詳細資料。 |
| 匯入一般檔案 | 已修正資料列可能會在匯入期間遺失的問題。|
| IntelliSense/編輯器 | 降低 IntelliSense 前往 Azure SQL Database 的 SMO 查詢流量。 |
| IntelliSense/編輯器 | 鍵入 T-SQL 建立使用者時，所顯示工具提示中的文法錯誤已修正。 此外，也修正了錯誤訊息，釐清使用者與登入。 |
| 記錄檢視器 | 已修正即使按兩下 [物件總管] 上較舊的封存符號，SSMS 也一律會開啟目前伺服器 (或代理程式) 記錄的問題：。 請參閱 [SQL Server 使用者意見反應](https://feedback.azure.com/forums/908035/suggestions/37633648)以取得詳細資料。 |
| SSMS 安裝程式 | 已修正下列問題：安裝程式記錄檔路徑包含空格時，造成 SSMS 安裝程式失敗。 請參閱 [SQL Server 使用者意見反應](https://feedback.azure.com/forums/908035/suggestions/37496110)以取得詳細資料。 |
| SSMS 安裝程式 | 已修正下列問題：SSMS 在顯示啟動顯示畫面之後立即結束。 </br> 如需詳細資料，請參閱下列網站：[SQL Server 使用者意見反應](https://feedback.azure.com/forums/908035/suggestions/37502512)、[無法啟動 SSMS](https://dba.stackexchange.com/questions/238609/ssms-refuses-to-start)，以及[資料庫管理員](https://dba.stackexchange.com/questions/237086/sql-server-management-studio-18-wont-open-only-splash-screen-pops-up)。 |
| 物件總管 | 提高當連線到 Linux 上的 SQL 時，啟用 [啟動 PowerShell]  的限制。 |
| 物件總管 | 已修正下列問題：嘗試展開 [PolyBase/向外延展群組] 節點時 (連線到計算節點時)，造成 SSMS 當機。 |
| 物件總管 | 已修正下列問題：即使在停用指定的索引之後，[已停用]  功能表項目仍維持啟用狀態。 請參閱 [SQL Server 使用者意見反應](https://feedback.azure.com/forums/908035/suggestions/37735375)以取得詳細資料。 |
| 報表 | 修正報表以顯示 GrantedQueryMemory 值 (單位為 KB) (SQL 效能儀表板報表)。 請參閱 [SQL Server 使用者意見反應](https://feedback.azure.com/forums/908035/suggestions/37167289)以取得詳細資料。 |
| 報表 | 改善 Always-On 案例中記錄區塊的追蹤。 |
| 執行程序表 | 在執行程序表結構描述中，新增執行程序表項目 *SpillOccurred*。 |
| 執行程序表 | 在執行程序表結構描述中，新增遠端讀取 (*ActualPageServerReads*、*ActualPageServerReadAheads*、*ActualLobPageServerReads*、*ActualLobPageServerReadAheads*)。 |
| SMO/指令碼 | 避免在為非圖形資料表編寫指令碼期間查詢邊緣條件約束。 |
| SMO/指令碼 | 移除使用 [資料分類]  為資料行編寫指令碼時，對敏感度分類的條件約束。 |
| SMO/指令碼 | 已修正產生資料時，在圖形資料表上「產生指令碼」失敗的問題：。 請參閱 [SQL Server 使用者意見反應](https://feedback.azure.com/forums/908035-sql-server/suggestions/32898466)以取得詳細資料。 |
| SMO/指令碼 | 已修正 EnumObjects() 方法針對同義字未擷取結構描述名稱的問題。 |
| SMO/指令碼 | 已修正下列問題：UIConnectionInfo.LoadFromStream() 中無法讀取 *AdvancedOptions* 區段 (未指定密碼時)。 |
| SQL Agent | 已修正下列問題：使用 [作業屬性] 視窗時，造成 SSMS 當機。 請參閱 [SQL Server 使用者意見反應](https://feedback.azure.com/forums/908035/suggestions/37164244)以取得詳細資料。 |
| SQL Agent | 已修正下列問題：[作業步驟屬性]  上的 [檢視] 按鈕不一定會啟用，妨礙到檢視指定作業步驟的輸出。 |
| XEvent UI | 在 XEvent 清單中新增 [套件] 資料行來釐清同名的事件。 |
| XEvent UI | 在 XEventUI 中，新增遺漏的「外部程式庫」類別類型對應。 |

#### <a name="known-issues-181"></a>已知問題 (18.1)

- 當將資料表物件從 [物件總管] 拖曳到 [查詢編輯器] 中時，使用者可能會見到錯誤。 我們已經知道此問題，並計劃於下一版中修正。

- 關閉 SSMS 18.1 之後，不會保留 [選項]-> [文字編輯器]-> [編輯器] 索引標籤及狀態列 -> [狀態列配置和色彩] 下的 [群組連線]  與 [單一伺服器連線]  色彩選項。 當您重新開啟 SSMS 之後，[狀態列配置和色彩] 選項會還原成預設值 (白色)。

- 您從方格、文字或檔案中顯示的 SSMS 結果所看到資料大小具有限制。

- 從電腦 A 上所執行 SSMS 建立的資料庫圖表無法從電腦 B 進行修改 (SSMS 當機)。 請參閱 [SQL Server 使用者意見反應](https://feedback.azure.com/forums/908035/suggestions/37992649)以取得詳細資料。

### <a name="180"></a>18.0

![下載](media/download-icon.png) [下載 SSMS 18.0](https://go.microsoft.com/fwlink/?linkid=2088649)

- 版本號碼：18.0  
- 組建編號：15.0.18118.0  
- 發行日期：2019 年 4 月 24 日

[簡體中文](https://go.microsoft.com/fwlink/?linkid=2088649&clcid=0x804)| [繁體中文](https://go.microsoft.com/fwlink/?linkid=2088649&clcid=0x404)| [英文 (美國)](https://go.microsoft.com/fwlink/?linkid=2088649&clcid=0x409)| [法文](https://go.microsoft.com/fwlink/?linkid=2088649&clcid=0x40c)| [德文](https://go.microsoft.com/fwlink/?linkid=2088649&clcid=0x407)| [義大利文](https://go.microsoft.com/fwlink/?linkid=2088649&clcid=0x410)| [日文](https://go.microsoft.com/fwlink/?linkid=2088649&clcid=0x411)| [韓文](https://go.microsoft.com/fwlink/?linkid=2088649&clcid=0x412)| [葡萄牙文 (巴西)](https://go.microsoft.com/fwlink/?linkid=2088649&clcid=0x416)| [俄文](https://go.microsoft.com/fwlink/?linkid=2088649&clcid=0x419)| [西班牙文](https://go.microsoft.com/fwlink/?linkid=2088649&clcid=0x40a)

#### <a name="whats-new-in-180"></a>18.0 的新功能

| 新項目| 詳細資料|
| :-------| :------|
|SQL Server 2019 支援|SSMS 18.0 是第一個完整「感知」  SQL Server 2019 (compatLevel 150) 的版本。|
|SQL Server 2019 支援|支援 SQL Server 2019 中的 "BATCH_STARTED_GROUP" 和 "BATCH_COMPLETED_GROUP"，以及 SQL 受控執行個體。|
|SQL Server 2019 支援|SMO：新增 UDF 內嵌支援。|
|SQL Server 2019 支援|GraphDB：在 Graph TC 序列的執行程序表中新增旗標。|
|SQL Server 2019 支援|Always Encrypted：新增 AEv2/記憶體保護區支援。|
|SQL Server 2019 支援|Always Encrypted：當使用者按一下 [選項] 按鈕來啟用/設定記憶體保護區支援時，連線對話方塊會有新的索引標籤 [Always Encrypted]。|
|較小的 SSMS 下載大小| 目前的大小為 ~500 MB，約 SSMS 17.x 套件組合的一半。
|SSMS 是以 Visual Studio 2017 Isolated Shell 為基礎|新 Shell (SSMS 是以 Visual Studio 2017 15.9.11 為基礎) 會解除鎖定所有修復 SSMS 和 Visual Studio 的協助工具修正程式，包括最新的安全性問題修正。|
|SSMS 協助工具改善| 為了解決所有工具 (SSMS、DTA 和分析工具) 中的協助工具問題，我們進行了大量的工作|
|SSMS 現在可以安裝在自訂資料夾中| 您可以從命令列 (適用於自動安裝) 和安裝程式 UI 存取這個選項。 從命令列，將這個額外的引數傳遞給 SSMS-Setup-ENU.exe： SSMSInstallRoot=C:\MySSMS18  根據預設，新的 SSMS 安裝位置是：%ProgramFiles(x86)%\Microsoft SQL Server Management Studio 18\Common7\IDE\ssms.exe。  這並不表示 SSMS 是多重執行個體。|
|SSMS 允許以不同於作業系統語言的語言進行安裝|已解除封鎖混合語言安裝。 例如，您可以在法文版 Windows 上安裝 SSMS 德文版。 如果作業系統語言與 SSMS 語言不相符，則使用者必須在 [工具]   > [選項]   > [國際設定]  下變更語言，否則 SSMS 會顯示英文的 UI。|
|SSMS 不再與 SQL 引擎共用元件|我們做了很多努力來避免與 SQL 引擎共用元件時，通常會導致某一方覆寫另一方所安裝檔案的服務性問題。|
|SSMS 需要 NetFx 4.7.2 或更新版本|我們將最低需求從 NetFx4.6.1 升級至 NetFx4.7.2：這可讓我們善用新架構所公開的新功能。|
|移轉 SSMS 設定的能力| 第一次啟動 SSMS 18 時，系統會提示使用者移轉 17.x 設定。 使用者設定檔案現在會儲存為純文字的 XML 檔案，因而提升可攜性並可能允許編輯。|
|高 DPI 支援| 現在預設會啟用高 DPI。|
|SSMS 隨附 Microsoft OLE DB 驅動程式| 如需詳細資料，請參閱[下載 Microsoft OLE DB Driver for SQL Server](../connect/oledb/download-oledb-driver-for-sql-server.md)。|
|Windows 8 不支援 SSMS。 Windows 10 和 Windows Server 2016 需要版本 1607 (10.0.14393) 或更新版本|由於 NetFx 4.7.2 的新相依性之故，SSMS 18.0 不會安裝在 Windows 8、舊版 Windows 10 和 Windows Server 2016 上。 SSMS 安裝程式會封鎖這些系統。 Windows 8.1 仍然受到支援。|
|SSMS 不再新增至 PATH 環境變數|SSMS.EXE (和一般工具) 的路徑不再新增至路徑。 使用者可以手動新增；如果在新式 Windows 電腦上，請使用 [開始] 功能表。|
|開發 SSMS 延伸模組不再需要套件識別碼| 在過去，SSMS 選擇性地只載入已知套件，因此需要開發人員註冊他們自己的套件。 現已不再是如此。|
|一般 SSMS|在 SSMS 中公開檔案群組的 AUTOGROW_ALL_FILES 設定選項。|
|一般 SSMS|從 SSMS GUI 移除具風險的 [輕量型共用] 和 [優先權提升] 選項。 如需詳細資料，請參閱[優先權提升詳細資料 - 及不建議使用的原因](https://deep.data.blog/2010/01/26/priority-boost-details-and-why-its-not-recommended/) \(英文\)。
|一般 SSMS|用來建立檔案的新功能表和按鍵繫結關係：**CTRL+ALT+N**。 **CTRL+N** 會繼續建立新的查詢。|
|一般 SSMS|[新增防火牆規則]  對話方塊現在可讓使用者指定規則名稱，而不是代替使用者自動產生。|
|一般 SSMS|專為 v140+ T-SQL 改善了編輯器中的 IntelliSense。|
|一般 SSMS|在 [定序] 對話方塊上新增 SSMS UI 的 UTF-8 支援。|
|一般 SSMS|針對連線對話方塊的 MRU 密碼，切換到「Windows 認證管理員」。 這可解決長久以來密碼的持續性不完全可靠的問題。|
|一般 SSMS|確保在預期的監視器上顯示越來越多的對話方塊和視窗，藉以改善對多個監視器系統的支援。|
|一般 SSMS|在 [伺服器屬性] 對話方塊的新 [資料庫設定] 頁面中公開 [備份總和檢查碼預設] 伺服器設定。 如需詳細資訊，請參閱 [https://feedback.azure.com/forums/08035-sql-server/suggestions/34634974](https://feedback.azure.com/forums/08035-sql-server/suggestions/34634974)。|
|一般 SSMS|在 [設定 SQL Server 錯誤記錄檔] 下公開 [maximum size for error log files] \(錯誤記錄檔大小上限\)。 如需詳細資訊，請參閱 [https://feedback.azure.com/forums/908035/suggestions/33624115](https://feedback.azure.com/forums/908035/suggestions/33624115)。|
|一般 SSMS|在 [工具] 功能表下新增 [移轉到 Azure] – 我們已整合 Database Migration Assistant 與 Azure 資料庫移轉服務，可讓您快速輕鬆地存取，以協助加速移轉到 Azure。|
|一般 SSMS|新增邏輯以提示使用者在使用 [變更連線] 時認可已開啟的交易。|
|Azure Data Studio 整合|新增功能表項目以啟動/下載 Azure Data Studio。|
|Azure Data Studio 整合|在 [物件總管] 中新增 [啟動 Azure Data Studio] 功能表項目。|
|Azure Data Studio 整合|當使用者以滑鼠右鍵按一下 OE 中的資料庫節點時，即會顯示操作功能表，讓使用者在 Azure Data Studio 中執行查詢或建立新的筆記本。|
|Azure SQL 支援| SLO/Edition/MaxSize 資料庫屬性現在可接受自訂名稱，讓您更輕鬆地支援未來版本的 Azure SQL Database。|
|Azure SQL 支援| 新增對 vCore SKU (一般用途和業務關鍵) 的支援：Gen4_24 及所有 Gen5。|
|Azure SQL 受控執行個體|新增「Azure AD 登入」作為 SMO 及 SSMS 中連線至 Azure SQL 受控執行個體時的新登入類型。|
|永遠開啟|重新雜湊 SSMS Always On 儀表板中的 RTO (預估復原時間) 和 RPO (估計的資料遺失)。 請參閱 [https://docs.microsoft.com/sql/database-engine/availability-groups/windows/monitor-performance-for-always-on-availability-groups](../database-engine/availability-groups/windows/monitor-performance-for-always-on-availability-groups.md) 上的更新文件。|
|Always Encrypted| [連線至伺服器] 對話方塊之新 [Always Encrypted] 索引標籤中的 [啟用 Always Encrypted] 核取方塊現在提供簡單的方法來啟用/停用資料庫連線的 Always Encrypted。|
|具有安全記憶體保護區的 Always Encrypted| 在 SQL Server 2019 中，已完成數個增強功能來支援具有安全記憶體保護區的 Always Encrypted：[連線至伺服器] 對話方塊中用於指定記憶體保護區證明 URL 的文字欄位 (新的 [Always Encrypted] 索引標籤)。  [新增資料行主要金鑰] 對話方塊中用來控制新資料行主要金鑰是否允許記憶體保護區計算的新核取方塊。  其他 Always Encrypted 金鑰管理對話方塊現在會公開哪些資料行主要金鑰允許記憶體保護區計算的資訊。|
|稽核檔案|將驗證方法從儲存體帳戶金鑰驗證變更為 Azure AD 驗證。|
|資料分類| 重新組織資料分類工作功能表：新增資料庫工作功能表的子功能表，並新增選項讓您從功能表開啟報表，而不需要先開啟分類資料視窗。|
|資料分類|在 SMO 中新增 [資料分類] 功能。 資料行物件公開了新的屬性：SensitivityLabelName、SensitivityLabelId、SensitivityInformationTypeName、SensitivityInformationTypeId，及 IsClassified (唯讀)。 如需詳細資訊，請參閱 [ADD SENSITIVITY CLASSIFICATION (Transact-SQL)](../t-sql/statements/add-sensitivity-classification-transact-sql.md)|
|資料分類|在 [資料分類] 飛出視窗中新增 [分類報告] 功能表項目。|
|資料分類| 已更新建議。|
|資料庫相容性層級升級|在 [資料庫名稱]***_ > _[工作]*  *_ > _[資料庫升級]* *下方新增選項。這會啟動新的* Query Tuning Assistant (QTA)** 引導使用者完成下列流程：收集效能基準資料，再升級資料庫相容性層級。 升級至想要的資料庫相容性層級。  針對相同的工作負載，收集第二輪的效能資料。 偵測工作負載迴歸，並提供經過測試的建議來提升工作負載效能。  這類似於[查詢存放區使用案例](../relational-databases/performance/query-store-usage-scenarios.md#CEUpgrade)中所述的資料庫升級程序，不同之處是在最後一個步驟中，QTA 不會依賴先前已知良好的狀態來產生建議。|
|資料層應用程式精靈|新增使用圖形資料表匯入/匯出資料層應用程式的支援。|
|一般檔案匯入精靈|新增邏輯，通知使用者匯入可能會導致重新命名資料行。|
|Integration Services (SSIS)|新增支援，允許客戶對 Azure Government 雲端中 Azure-SSIS IR 上的 SSIS 套件進行排程。|
|Integration Services (SSIS)|當您透過 SSMS 使用 Azure SQL 受控執行個體的 SQL Agent 時，您可以在 SSIS 代理程式作業步驟中設定參數和連線管理員。|
|Integration Services (SSIS)|連線至 Azure SQL Database/Azure SQL 受控執行個體時，您可以使用 *default* 作為初始資料庫進行連線。|
|Integration Services (SSIS)|在 [Integration Services 目錄] 節點下新增項目 [在 Azure Data Factory 中嘗試 SSIS]  ，該項目可用於啟動 [Integration Runtime 建立精靈]，並可快速建立 "Azure-SSIS Integration Runtime"。
|Integration Services (SSIS)|在 [目錄建立精靈] 中新增 [建立 SSIS IR]  按鈕，該項目可用於啟動 [Integration Runtime 建立精靈]，並可快速建立 "Azure-SSIS Integration Runtime"。|
|Integration Services (SSIS)|ISDeploymentWizard 現在支援 SQL 驗證、Azure Active Directory 整合式驗證，以及命令列模式下的 Azure Active Directory 密碼驗證。|
|Integration Services (SSIS)|[部署精靈] 現在支援建立並部署到 Azure Data Factory SSIS Integration Runtime。|
|物件指令碼|為物件編寫指令碼時，新增功能表項目 [CREATE 或 ALTER]。|
|查詢存放區|藉由對圖表 Y 軸上顯示的數值新增千位分隔符號，改善了某些報表 (整體資源耗用量) 的可用性。|
|查詢存放區|新增「查詢等候統計資料」報表。|
|查詢存放區|在 [追蹤的查詢] 檢視中新增 [執行計數] 計量。|
|複寫工具|在複寫監視器和 SSMS 中新增非預設連接埠規格功能的支援。|
|執行程序表|在執行程序表運算子節點下新增實際經過時間、實際值與估計值的資料列 (若有)。 這會讓實際計畫看起來與即時查詢統計資料計畫一致。|
|執行程序表|按一下執行程序表的 [編輯查詢] 按鈕時已修改工具提示並新增了註解，以指示使用者如果查詢超過 4000 個字元，則 SQL 引擎可能會截斷執行程序表。|
|執行程序表|新增邏輯以顯示「具體化程式運算子 (外部 Select)」。|
|執行程序表|新增執行程序表屬性 BatchModeOnRowStoreUsed，讓您輕鬆找出正在使用「資料列存放區批次模式掃描」功能的查詢。 只要查詢在資料列存放區上執行批次模式掃描，新的屬性 (BatchModeOnRowStoreUsed="true") 就會新增至 StmtSimple 元素。|
|執行程序表|在 DW ROLLUP 和 CUBE 的 LocalCube RelOp 中新增執行程序表支援。|
|執行程序表|在 Azure Synapse Analytics 的新 ROLLUP 和 CUBE 彙總功能中，新增 LocalCube 運算子。|
|SMO| 擴充建立可繼續索引的 SMO 支援。|
|SMO| 在 SMO 物件 ("PropertyMissing") 上新增新事件，以協助應用程式作者更快偵測到 SMO 的效能問題。|
|SMO| 在 Configuration 物件上公開新的 DefaultBackupChecksum 屬性，該屬性對應至 [備份總和檢查碼預設] 伺服器設定。|
|SMO| 在伺服器物件上公開新的 ProductUpdateLevel 屬性，該屬性對應至使用中 SQL 版本的服務等級 (例如 CU12、RTM)。|
|SMO| 在資料庫物件上公開新的 LastGoodCheckDbTime 屬性，該屬性對應至 "lastgoodcheckdbtime" 資料庫屬性。 如果沒有這類屬性可用，則會傳回預設值 1/1/1900 12:00:00 AM。|
|SMO|將 RegSrvr.xml 檔案 (已註冊的伺服器設定檔) 位置移至 "%AppData%\Microsoft\SQL Server Management Studio" (未進行版本控制，因此可跨不同的 SSMS 版本共用)。|
|SMO|已新增「雲端見證」作為新的仲裁及資源類型。|
|SMO|在 SMO 和 SSMS 中新增「邊緣條件約束」的支援。|
|SMO|已在 SMO 和 SSMS 中新增「邊緣條件約束」的串聯刪除支援。|
|SMO|已新增資料分類「讀取-寫入」權限的支援。|
|弱點評量| 啟用 Azure SQL DW 上的 [弱點評定] 工作功能表。|
|弱點評量|變更在 Azure SQL 受控執行個體上執行的弱點評定規則集，使「弱點評定」的掃描結果與 Azure SQL DB 中的結果一致。|
|弱點評量| [弱點評定] 現在支援 Azure SQL DW。|
|弱點評量|新增匯出功能以將弱點評定的掃描結果匯出到 Excel。|
|XEvent 檢視器|XEvent 檢視器：啟用執行程序表視窗以取得更多 XEvent。|

#### <a name="bug-fixes-in-180"></a>18.0 中的錯誤修正

| 新項目 | 詳細資料|
|----------|--------|
|損毀和凍結|修正與 GDI 物件相關的常見 SSMS 損毀來源。|
|損毀和凍結|修正選取 [編寫指令碼為 Create/Update/Drop] (已移除 SMO 物件的不必要提取) 時，造成停止回應和效能不佳的常見來源。|
|損毀和凍結|修正在啟用 ADAL 追蹤的情況下使用 MFA 連線至 Azure SQL Database 時系統停止回應此問題。|
|損毀和凍結|修正從 [活動監視器] 叫用即時查詢統計資料時系統停止回應 (或察覺停止回應) 的問題 (在沒有設定「持續安全性資訊」的情況下使用 SQL Server驗證時便會出現此問題)。|
|損毀和凍結|修正在 [物件總管] 中選取「報表」時系統停止回應的問題，這個問題會在高延遲連線或暫時無法存取資源時發生。|
|損毀和凍結|已修正嘗試使用中央管理伺服器和 Azure SQL Server 時，SSMS 發生損毀的問題。 如需詳細資料，請參閱 [SMSS 17.5 application error and crash when using Central Management Server](https://feedback.azure.com/forums/908035/suggestions/33374884) (使用中央管理伺服器時發生 SMSS 17.5 應用程式錯誤和損毀)。|
|損毀和凍結|透過最佳化接收 IsFullTextEnabled 屬性的方式，修正系統在 [物件總管] 中停止回應的問題。|
|損毀和凍結|藉由避免建置不必要的查詢來擷取屬性，修正系統在 [複製資料庫精靈] 中停止回應的問題。|
|損毀和凍結|修正在編輯 T-SQL 期間造成 SSMS 停止回應/損毀的問題。|
|損毀和凍結|減輕編輯大型 T-SQL 指令碼時，SSMS 變得沒有回應的問題。|
|損毀和凍結|已修正處理查詢所傳回的大型資料集時，造成 SSMS 記憶體不足的問題。|
|一般 SSMS|已修正下列問題："ApplicationIntent" 並未在「已註冊的伺服器」的連線中一同傳遞。|
|一般 SSMS|已修正在高 DPI 監視器中，並未適當轉譯「新 XEvent 工作階段精靈 UI」表單的問題。|
|一般 SSMS|修正嘗試匯入 bacpac 檔案時的問題。|
|一般 SSMS|已修正嘗試顯示資料庫屬性時 (FILEGROWTH > 2048 GB)，擲回算數溢位錯誤的問題。|
|一般 SSMS|已修正問題：效能儀表板報表會報告在子報表中找不到的 PAGELATCH 和 PAGEIOLATCH 等候。|
|一般 SSMS|進一步修正以提高 SSMS 對多監視器的感知能力，讓它在正確的監視器中開啟對話方塊。|
|Analysis Services (AS)|修正已裁剪 AS XEvent UI [進階設定] 的問題。|
|Analysis Services (AS)|修正 DAX 剖析擲回找不到檔案例外狀況的問題。|
|Azure SQL Database|已修正下列問題：連線至 Azure SQL Database 中的使用者資料庫 (而非 master) 時，Azure SQL Database 查詢視窗中的資料庫清單填入的資料不正確。|
|Azure SQL Database|已修正問題：無法將「時態表」新增至 Azure SQL Database。|
|Azure SQL Database|在 Azure 中的 [統計資料] 功能表下啟用 [統計資料屬性] 子功能表選項，因為到目前為止已完全支援一段時間。|
|Azure SQL - 一般支援|修正通用 Azure UI 控制項中防止使用者顯示 Azure 訂用帳戶 (若未超過 50 個) 的問題。 此外，排序已變更為依名稱，而不是依訂用帳戶識別碼。 例如，當使用者嘗試從 URL 還原備份時，可能遇到這個問題。|
|Azure SQL - 一般支援|已修正通用 Azure UI 控制項在列舉訂用帳戶時，使用者在一些租用戶中沒有任何訂用帳戶時，這可能會產生「索引超出範圍。 必須為非負數且小於集合的大小。」 錯誤。 例如，當使用者嘗試從 URL 還原備份時，可能遇到這個問題。|
|Azure SQL - 一般支援|修正服務等級目標會硬式編碼而導致 SSMS 難以支援較新 Azure SQL SLO 的問題。 現在，使用者可以登入 Azure，且讓 SSMS 擷取所有適用的 SLO 資料 (版本和大小上限)|
|Azure SQL 受控執行個體支援|已改善/完善對受控執行個體的支援：停用 UI 中不支援的選項，並修正 [檢視稽核記錄] 選項來處理 URL 稽核目標。|
|Azure SQL 受控執行個體支援|[產生和發佈指令碼精靈] 會撰寫不受支援的 CREATE DATABASE 子句指令碼。|
|Azure SQL 受控執行個體支援|啟用受控執行個體的即時查詢統計資料。|
|Azure SQL 受控執行個體支援|[資料庫屬性] -> [檔案] 不正確地撰寫 ALTER DB ADD FILE 指令碼。|
|Azure SQL 受控執行個體支援|修正使用 SQL Agent 排程器的迴歸問題，在其中即使選擇了其他某個排程類型，還是會選擇 ONIDLE 排程。|
|Azure SQL 受控執行個體支援|調整 MAXTRANSFERRATE、MAXBLOCKSIZE，以便在 Azure 儲存體上執行備份。|
|Azure SQL 受控執行個體支援|在 RESTORE 作業之前為結尾記錄備份編寫指令碼的問題 (CL 不支援此作業)。|
|Azure SQL 受控執行個體支援|[建立資料庫精靈] 不正確地撰寫 CREATE DATABASE 陳述式指令碼。|
|Azure SQL 受控執行個體支援|當 SSMS 連線到受控執行個體時，可對其中的 SSIS 套件進行特殊處理。|
|Azure SQL 受控執行個體支援|已修正在連線至受控執行個體的情況下，當嘗試使用「活動監視器」時顯示錯誤的問題。|
|Azure SQL 受控執行個體支援|改善 Azure AD 登入 (SSMS 總管中) 的支援。|
|Azure SQL 受控執行個體支援|改善 SMO 檔案群組物件的指令碼撰寫。|
|Azure SQL 受控執行個體支援|改善認證的 UI。|
|Azure SQL 受控執行個體支援|新增邏輯複寫的支援。|
|Azure SQL 受控執行個體支援|已修正造成以滑鼠右鍵按一下資料庫，並選擇 [匯入資料層應用程式] 時失敗的問題。|
|Azure SQL 受控執行個體支援|已修正造成以滑鼠右鍵按一下 [TempDB] 來顯示錯誤時的問題。|
|Azure SQL 受控執行個體支援|修正嘗試為 SMO 中的 ALTER DB ADD FILE 陳述式編寫指令碼時，造成所產生 T-SQL 指令碼空白的問題。|
|Azure SQL 受控執行個體支援|已改善受控執行個體伺服器特定屬性 (硬體世代、服務層、已使用和保留的儲存體) 的顯示方式。|
|Azure SQL 受控執行個體支援|已修正下列問題：為資料庫編寫指令碼時 ([指令碼編寫為 Create...])，無法為額外檔案群組和檔案編寫指令碼。 如需詳細資訊，請參閱 [https://feedback.azure.com/forums/908035/suggestions/37326799](https://feedback.azure.com/forums/908035/suggestions/37326799)。 |
|備份/還原/附加/卸離資料庫|修正當 .mdf 檔案的實體檔案名稱與原始檔案名稱不相符時，使用者無法附加資料庫的問題。|
|備份/還原/附加/卸離資料庫|已修正 SSMS 可能找不到有效的還原計畫，或者可能找到次佳還原計畫的問題。 如需詳細資訊，請參閱 [https://feedback.azure.com/forums/908035-sql-server/suggestions/32897752](https://feedback.azure.com/forums/908035-sql-server/suggestions/32897752)。 |
|備份/還原/附加/卸離資料庫|已修正下列問題：[附加資料庫精靈] 沒有顯示已重新命名的次要檔案。 現在會顯示檔案，並新增其相關註解 (例如「找不到」)。 如需詳細資訊，請參閱 [https://feedback.azure.com/forums/908035/suggestions/32897434](https://feedback.azure.com/forums/908035/suggestions/32897434)。 |
|複製資料庫精靈|[產生指令碼/傳輸/複製資料庫精靈] 嘗試使用記憶體內部資料表建立資料表時，不會強制開啟 ansi_padding。|
|複製資料庫精靈|SQL Server 2017 和 SQL Server 2019 上已中斷 [傳輸資料庫工作/複製資料庫精靈]。|""
|複製資料庫精靈|在建立相關聯的外部資料來源之前，須建立 [產生指令碼/傳輸/複製資料庫精靈] 指令碼資料表。|
|連線對話方塊|啟用按下 DEL 鍵從先前使用者名稱清單移除使用者名稱的功能。 如需詳細資料，請參閱 [Allow deletion of users from SSMS login window](https://feedback.azure.com/forums/908035/suggestions/32897632) (允許從 SSMS 登入視窗刪除使用者)。|
|DAC 匯入精靈|已修正下列問題：使用 Azure Active Directory (Azure AD) 連線時，[DAC 匯入精靈] 無法運作。|
|資料分類|修正在資料分類窗格中儲存分類時，若其他資料庫上已開啟其他資料分類窗格的問題。|
|資料層應用程式精靈|已修正使用者因為伺服器的有限存取權 (例如無法存取相同伺服器上的所有資料庫)，而無法匯入資料層應用程式 (.dacpac) 的問題。|
|資料層應用程式精靈|已修正當多個資料庫裝載於相同 Azure SQL 伺服器時會導致匯入極度緩慢的問題。|
|外部資料表|新增範本、SMO、IntelliSense 和屬性方格對 Rejected_Row_Location 的支援。|
|一般檔案匯入精靈|已修正下列問題：[匯入一般檔案精靈] 的雙引號處理方式不正確 (逸出)。 如需詳細資訊，請參閱 [https://feedback.azure.com/forums/908035/suggestions/32897998](https://feedback.azure.com/forums/908035/suggestions/32897998)。 |
|一般檔案匯入精靈|修正與浮點類型處理方式不正確 (在對浮點使用不同分隔符號的地區設定上) 相關的問題。|
|一般檔案匯入精靈|修正值為 0 或 1 時，與匯入位元相關的問題。 如需詳細資訊，請參閱 [https://feedback.azure.com/forums/908035-sql-server/suggestions/32898535](https://feedback.azure.com/forums/908035-sql-server/suggestions/32898535)。 |
|一般檔案匯入精靈|已修正「浮點數」  資料類型輸入為 *Null* 的問題。|
|一般檔案匯入精靈|已修正下列問題：匯入精靈無法處理負小數值。|
|一般檔案匯入精靈|已修正下列問題：精靈無法從單一資料行 CSV 檔案匯入。|
|一般檔案匯入精靈|已修正問題：當資料表存在時，「一般檔案匯入」不允許變更目標資料表。 如需詳細資訊，請參閱 [https://feedback.azure.com/forums/908035-sql-server/suggestions/32896186](https://feedback.azure.com/forums/908035-sql-server/suggestions/32896186)。 |
|說明檢視器|改善接受線上/離線模式的邏輯 (可能仍有些問題待解決)。|
|說明檢視器|修正 [檢視說明] 以接受線上/離線設定。 如需詳細資訊，請參閱 [https://feedback.azure.com/forums/908035-sql-server/suggestions/32897791](https://feedback.azure.com/forums/908035-sql-server/suggestions/32897791)。 |
|高可用性災害復原 (HADR)<BR> 可用性群組 (AG)|修正 [容錯移轉可用性群組精靈] 中角色一律顯示為 [解析中] 的問題。|
|高可用性災害復原 (HADR)<BR> 可用性群組 (AG)|修正 SSMS 在 [AG 儀表板] 中顯示截斷警告的問題。|
|Integration Services (IS)|已修正 SxS 問題：當 SQL Server 2019 和 SSMS 18.0 安裝在同一部電腦上時，部署精靈無法連線到 SQL 伺服器。|
|Integration Services (IS)|修正設計維護計劃時，無法編輯維護計劃工作的問題。|
|Integration Services (IS)|已修正重新命名部署中專案時，[部署精靈] 當掉的問題。|
|Integration Services (IS)|啟用 Azure-SSIS IR 排程功能中的環境設定。|
|Integration Services (IS)|已修正下列問題：客戶帳戶屬於多個租用戶時，[SSIS Integration Runtime 建立精靈] 停止回應。|
|作業活動監視器|修正使用作業活動監視器 (含篩選) 時的當機問題。|
|物件總管|修正嘗試在 OE 中展開 [管理] 節點時，SSMS 可能會擲回「物件無法從 DBNull 轉換為其他類型」例外狀況 (設定錯誤的 DataCollector) 的問題。|
|物件總管|已修正下列問題：OE 在叫用「編輯前 N 個...」之前未逸出引號，造成設計工具混淆。|
|物件總管|修正 [匯入資料層應用程式精靈] 無法從 Azure 儲存體樹狀結構啟動的問題。|
|物件總管|已修正下列問題：「Database Mail 組態」中沒有保存 TLS/SSL 核取方塊狀態。 如需詳細資訊，請參閱 [https://feedback.azure.com/forums/908035-sql-server/suggestions/32895541](https://feedback.azure.com/forums/908035-sql-server/suggestions/32895541)。 |
|物件總管|修正 SSMS 在嘗試使用 is_auto_update_stats_async_on 還原資料庫時，現有連線關閉選項呈現灰色的問題。|
|物件總管|已修正以滑鼠右鍵按一下 OE 節點 (例如「資料表」，並想要執行動作，例如移至 [篩選] > [篩選設定] 來篩選資料表，篩選設定表單可能會出現在 SSMS 目前使用中畫面以外的其他畫面上) 的問題。 如需詳細資訊，請參閱 [https://feedback.azure.com/forums/908035-sql-server/suggestions/34284106](https://feedback.azure.com/forums/908035-sql-server/suggestions/34284106)。 |
|物件總管|修正當嘗試重新命名物件時，DELETE 鍵在 OE 中無法運作的長時間待處理問題。 如需詳細資訊，請參閱 [https://feedback.azure.com/forums/908035-sql-server/suggestions/33073510](https://feedback.azure.com/forums/908035-sql-server/suggestions/33073510)、[https://feedback.azure.com/forums/908035/suggestions/32910247](https://feedback.azure.com/forums/908035/suggestions/32910247) 和其他重複主題。|
|物件總管|顯示現有資料庫檔案的屬性時，大小會出現在 [大小 (MB)] 資料行下方，而不是 [初始大小 (MB)]，後者是建立新資料庫時所顯示的項目。 如需詳細資訊，請參閱 [https://feedback.azure.com/forums/908035-sql-server/suggestions/32629024](https://feedback.azure.com/forums/908035-sql-server/suggestions/32629024)。 |
|物件總管|停用 [圖形資料表] 上的 [設計] 操作功能表項目，因為目前版本的 SSMS 並不支援這些資料表。|
|物件總管|已修正下列問題：[新增作業排程] 對話方塊無法在高 DPI 監視器上正確呈現。 如需詳細資訊，請參閱 [https://feedback.azure.com/admin/v3/suggestions/35541262](https://feedback.azure.com/admin/v3/suggestions/35541262)。 |
|物件總管|修正/改善資料庫大小 ([大小 (MB)]) 在 [物件總管] 詳細資料中顯示方式的問題：僅限 2 個小數位數，並使用千位分隔符格式化。 如需詳細資訊，請參閱 [https://feedback.azure.com/forums/908035/suggestions/34379308](https://feedback.azure.com/forums/908035/suggestions/34379308)。|
|物件總管|修正造成「空間索引」建立失敗並出現「若要完成此動作，請設定屬性 PartitionScheme」之類錯誤的問題。|
|物件總管|[物件總管] 中的次要效能改善，盡可能避免發出額外的查詢。|
|物件總管|將重新命名資料庫時要求確認的邏輯延伸到所有結構描述物件 (可進行設定)。|
|物件總管|已在物件總管篩選中新增適當的逸出。 如需詳細資訊，請參閱 [https://feedback.azure.com/forums/908035/suggestions/36678803](https://feedback.azure.com/forums/908035/suggestions/36678803)。 |
|物件總管|修正/改善 [物件總管詳細資料] 中的檢視，以正確的分隔符號顯示數字。 如需詳細資訊，請參閱 [https://feedback.azure.com/forums/908035/suggestions/32900944](https://feedback.azure.com/forums/908035/suggestions/32900944)。 |
|物件總管|修正連線到 SQL Express 時的 [資料表] 節點操作功能表，其中遺漏 [新增] 飛出視窗、未正確列出圖形資料表，且遺漏由系統建立版本的資料表。 如需詳細資訊，請參閱 [https://feedback.azure.com/forums/908035/suggestions/37245529](https://feedback.azure.com/forums/908035/suggestions/37245529)。 |
|物件指令碼|整體效能改善 - 相較於 SSMS 17.7，產生 WideWorldImporters 指令碼只要一半的時間。|
|物件指令碼|當撰寫物件指令碼，會省略具有預設值的資料庫範圍設定。|
|物件指令碼|編寫指令碼時，不要產生動態 T-SQL。 如需詳細資訊，請參閱 [https://feedback.azure.com/forums/908035-sql-server/suggestions/32898391](https://feedback.azure.com/forums/908035-sql-server/suggestions/32898391)。 |
|物件指令碼|在 SQL Server 2016 及更早版本上撰寫資料表指令碼時，省略圖形語法 "as edge" 和 "as node"。|
|物件指令碼|已修正搭配使用 Azure AD 與 MFA 連線至 Azure SQL Database 時，撰寫資料庫物件指令碼失敗的問題。|
|物件指令碼|修正嘗試在 Azure SQL Database 上使用 GEOMETRY_AUTO_GRID/GEOGRAPHY_AUTO_GRID 撰寫空間索引指令碼時擲回錯誤的問題。|
|物件指令碼|已修正問題：即使「物件總管」指令碼設定已設為符合來源，Azure SQL Database 的資料庫指令碼仍一律以內部部署 SQL 為目標。|
|物件指令碼|修正嘗試為 SQL DW 資料庫中涉及叢集及非叢集索引的資料表編寫指令碼時，產生的 T-SQL 陳述式不正確之問題。|
|物件指令碼|修正嘗試為 SQL DW 資料庫中具有「叢集資料行存放區索引」及「叢集索引」的資料表編寫指令碼時，產生不正確 T-SQL (重複陳述式) 的問題。|
|物件指令碼|修正沒有範圍值的資料分割資料表指令碼 (SQL DW 資料庫)。|
|物件指令碼|修正使用者無法為稽核/稽核規格 SERVER_PERMISSION_CHANGE_GROUP 編寫指令碼的問題。|
|物件指令碼|修正使用者無法為 SQL DW 中的統計資料編寫指令碼的問題。 如需詳細資訊，請參閱 [https://feedback.azure.com/forums/908035-sql-server/suggestions/32897296](https://feedback.azure.com/forums/908035-sql-server/suggestions/32897296)。 |
|物件指令碼|修正 [產生指令碼精靈] 顯示不正確的資料表在 [發生錯誤時繼續編寫指令碼] 設定為 false 時發生指令碼錯誤的問題。|
|物件指令碼|改善 SQL Server 2019 上的指令碼產生。|
|分析工具|將「彙總資料表重寫查詢」事件新增至分析工具事件。|
|查詢資料存放區|修正可能擲回 "DocumentFrame (SQLEditors)" 例外狀況的問題。|
|查詢資料存放區|已修正下列問題：嘗試在內建查詢存放區報表設定自訂時間間隔時，使用者無法在開始/結束間隔上選取上午或下午。|
|結果格線|修正造成高對比模式 (看不到選取的行號) 的問題。|
|結果格線|已修正在按一下方格時會導致「索引超出範圍」例外狀況的問題。|
|結果格線|已修正方格結果背景色彩遭到忽略的問題。 如需詳細資訊，請參閱 [https://feedback.azure.com/forums/908035/suggestions/32895916](https://feedback.azure.com/forums/908035/suggestions/32895916)。 |
|執行程序表|新的記憶體授與運算子屬性在有多個執行緒時不正確地顯示。|
|執行程序表|已在實際執行 XML 計畫的 RunTimeCountersPerThread 中新增下列 4 個屬性：HpcRowCount (*hpc* 裝置所處理的資料列數目)、HpcKernelElapsedUs (等候使用中核心執行的經過時間)、HpcHostToDeviceBytes (從主機傳輸至裝置的位元組數) 和 HpcDeviceToHostBytes (從裝置傳輸至主機的位元組數)。|
|執行程序表|修正類似計畫節點在錯誤位置醒目提示的問題。|
|SMO|已修正 SMO/ServerConnection 不正確地處理 SqlCredential 連線的問題。 如需詳細資訊，請參閱 [https://feedback.azure.com/forums/908035-sql-server/suggestions/33698941](https://feedback.azure.com/forums/908035-sql-server/suggestions/33698941)。 |
|SMO|修正使用 SMO 所撰寫應用程式若嘗試列舉多個執行緒上相同伺服器之資料庫時發生錯誤的問題 (即使在每個執行緒上使用個別的 SqlConnection 執行個體仍會發生錯誤)。|
|SMO|修正從外部資料表傳輸時效能降低的問題。|
|SMO|已修正瞄準 Azure 時 ServerConnection 執行緒安全性中造成 SMO 流失 SqlConnection 執行個體的問題。|
|SMO|已修正嘗試還原名稱中具有大括號的資料庫時，造成 StringBuilder.FormatError 的問題。|
|SMO|修正 SMO 中 Azure 資料庫預設為所有字串比較皆採用不區分大小寫的定序，而不是使用為資料庫指定之定序的問題。|
|SSMS 編輯器|已修正在「SQL 系統資料表」中，還原預設色彩是將色彩變更為檸檬綠，而不是預設綠色，而令人難以在白色背景上進行閱讀的問題。 如需詳細資料，請參閱 [Restoring wrong default color for SQL System Table](https://feedback.azure.com/forums/908035-sql-server/suggestions/32896906) (SQL 系統資料表的還原預設色彩錯誤)。 此問題在非英文版的 SSMS 中仍持續發生。|
|SSMS 編輯器|已修正下列問題：使用 Azure Active Directory (Azure AD) 驗證連線到 Azure SQLDW 時，IntelliSense 無法運作。|
|SSMS 編輯器|修正 IntelliSense 在使用者缺少 **master** 資料庫存取權時於 Azure 中的體驗。|
|SSMS 編輯器|修正用來建立「時態表」的程式碼片段，當目標資料庫的定序會區分大小寫時，此程式碼片段就會中斷。|
|SSMS 編輯器|IntelliSense 現在可識別新的 TRANSLATE 函式。 如需詳細資訊，請參閱 [https://feedback.azure.com/forums/908035-sql-server/suggestions/32898430](https://feedback.azure.com/forums/908035-sql-server/suggestions/32898430)。 |
|SSMS 編輯器|已改善 FORMAT 內建函式的 IntelliSense。 如需詳細資訊，請參閱 [https://feedback.azure.com/forums/908035-sql-server/suggestions/32898676](https://feedback.azure.com/forums/908035-sql-server/suggestions/32898676)。 |
|SSMS 編輯器|LAG 和 LEAD 現在是內建函式。 如需詳細資訊，請參閱 [https://feedback.azure.com/forums/908035-sql-server/suggestions/32898757](https://feedback.azure.com/forums/908035-sql-server/suggestions/32898757)。 |
|SSMS 編輯器|修正 IntelliSense 在使用 "ALTER TABLE...ADD CONSTRAINT...WITH(ONLINE=ON)" 時發出警告的問題。|
|SSMS 編輯器|已修正有數個系統檢視和資料表值函式未正確以色彩標示的問題。|
|SSMS 編輯器|修正按一下編輯器索引標籤時，可能造成索引標籤關閉而不是取得焦點的問題。 如需詳細資訊，請參閱 [https://feedback.azure.com/forums/908035/suggestions/37291114](https://feedback.azure.com/forums/908035/suggestions/37291114)。 |
|SSMS 選項|已修正下列問題：[工具]   > [選項]   > [SQL Server 物件總管]   > [命令]  頁面未正確調整大小。|
|SSMS 選項|根據預設，SSMS 現在停用 XMLA 編輯器中的 DTD 自動下載功能 -- 根據預設，XMLA 指令碼編輯器 (使用 XML 語言服務) 現在會防止自動下載潛在惡意 XMLA 檔案的 DTD。 這可藉由在 [工具]   > [選項]   > [環境]   > [文字編輯器]   > [XML]   > [其他]  中關閉 [自動下載 DTD 和結構描述] 設定進行控制。|
|SSMS 選項|將 **CTRL+D** 還原為舊版 SSMS 中所使用的快速鍵。 如需詳細資訊，請參閱 [https://feedback.azure.com/forums/908035/suggestions/35544754](https://feedback.azure.com/forums/908035/suggestions/35544754)。 |
|資料表設計工具|修正「編輯 200 個資料列」的當機問題。|
|資料表設計工具|已修正問題：設計工具在連線至 Azure SQL Database 時，允許新增資料表。|
|弱點評量|已修正掃描結果未正確載入的問題。|
|XEvent|新增 "action_name" 和 "class_type_desc" 這兩個資料行，它們會將動作識別碼及類別類型欄位顯示為可讀取字串。|
|XEvent|移除事件 XEvent 檢視器 1,000,000 個事件的上限。|
|XEvent 分析工具|修正當連線至含 96 個核心的 SQL Server 時，XEvent 分析工具無法啟動的問題。|
|XEvent 檢視器|修正嘗試使用「擴充事件工具列選項」群組事件時，XEvent 檢視器將損毀的問題。|

#### <a name="deprecated-and-removed-features-in-180"></a>18.0 中已淘汰和移除的功能

以下是 SSMS 18.0 版中已淘汰及已移除的功能。

- T-SQL 偵錯工具
- 資料庫圖表
- SSMS 不再隨附安裝下列工具：
  - OSQL.EXE
  - DReplay.exe
  - SQLdiag.exe
  - SSBDiagnose.exe
  - bcp.exe
  - sqlcmd.exe
- Configuration Manager 工具：
  - SQL Server 組態管理員和報表伺服器設定管理員不再是 SSMS 安裝程式的一部分。
- DMF 標準原則
  - 這些原則不再與 SSMS 一起安裝。 它們會移至 Git。 只要使用者想要，就能參與及下載/安裝這些原則。
- 已移除 SSMS 命令列選項 -P
  - 基於安全性考量，已移除命令列上指定純文字密碼的選項。
- 已移除 [產生指令碼] > [發佈到 Web 服務]
  - 已從 SSMS UI 中移除這個已淘汰的功能。
- 已移除 [物件總管] 中的 [維護] > [舊版] 節點。
  - 您再也無法存取舊的 [資料庫維護計劃] 與 [SQL Mail] 節點。 新式的 [Database Mail] 和 [維護計劃] 節點會繼續如往常般運作。

#### <a name="known-issues-180"></a>已知問題 (18.0)

- 您可能會在安裝 18.0 版時，遇到無法執行 SQL Server Management Studio 的問題。 如果您遇到此問題，請遵循 [SSMS2018 - 已安裝但未執行](https://feedback.azure.com/forums/908035-sql-server/suggestions/37502512-ssms2018-installed-but-will-not-run) (英文) 一文中的步驟處理。

- 您從方格、文字或檔案中顯示的 SSMS 結果所看到資料大小具有限制

- 在多個查詢視窗間進行切換時的重繪問題。 請參閱 [SQL Server 使用者意見反應](https://feedback.azure.com/forums/908035/suggestions/37474042)以取得詳細資料。 此問題的因應措施是在 [工具] > [選項] 下停用硬體加速。

### <a name="1791"></a>17.9.1

![下載](media/download-icon.png) [下載 SSMS 17.9.1](https://go.microsoft.com/fwlink/?linkid=2043154&clcid=0x409)

- 版本號碼：17.9.1
- 組建編號：14.0.17289.0
- 發行日期：2018 年 11 月 21 日

#### <a name="bug-fixes-in-1791"></a>17.9.1 中的 Bug 修正

- 已修正在搭配使用 SQL 查詢編輯器和「具 MFA 支援的 Azure Active Directory - 通用」驗證時，使用者可能會遇到每一次查詢叫用時連線都會關閉並重新開啟的問題。 這類連線關閉的副作用包括在未預期的情況下卸除全域暫存資料表，以及有時候會給予連線新的 SPID。
- 修正一個長期未解決的問題，即還原計畫找不到還原計畫，或者在某些情況下會產生無效率的還原計畫。
- 已修正問題：[匯入資料層應用程式精靈] 連線至 Azure SQL Database 時可能會導致錯誤。

> [!NOTE]
> SSMS 17.x 的非英文當地語系化版本若安裝在下列項目上，則需要 [KB 2862966 安全性更新程式套件](https://support.microsoft.com/kb/2862966)：Windows 8、Windows 7、Windows Server 2012 和 Windows Server 2008 R2。

[簡體中文](https://go.microsoft.com/fwlink/?linkid=2043154&clcid=0x804)| [繁體中文](https://go.microsoft.com/fwlink/?linkid=2043154&clcid=0x404)| [英文 (美國)](https://go.microsoft.com/fwlink/?linkid=2043154&clcid=0x409)| [法文](https://go.microsoft.com/fwlink/?linkid=2043154&clcid=0x40c)| [德文](https://go.microsoft.com/fwlink/?linkid=2043154&clcid=0x407)| [義大利文](https://go.microsoft.com/fwlink/?linkid=2043154&clcid=0x410)| [日文](https://go.microsoft.com/fwlink/?linkid=2043154&clcid=0x411)| [韓文](https://go.microsoft.com/fwlink/?linkid=2043154&clcid=0x412)| [葡萄牙文 (巴西)](https://go.microsoft.com/fwlink/?linkid=2043154&clcid=0x416)| [俄文](https://go.microsoft.com/fwlink/?linkid=2043154&clcid=0x419)| [西班牙文](https://go.microsoft.com/fwlink/?linkid=2043154&clcid=0x40a)

#### <a name="uninstall-and-reinstall-ssms-17x"></a>解除並重新安裝 SSMS 17.x

如果您的 SSMS 安裝遇到問題，而且無法經由標準的解除並重新安裝來解決，您可以先嘗試[修復](https://support.microsoft.com/help/4028054/windows-10-repair-or-remove-programs) Visual Studio 2015 IsoShell。 若修復 Visual Studio 2015 IsoShell 未解決問題，下列幾個步驟可以修正許多非特定的問題：

1. 以您解除安裝任何應用程式的相同方式，解除安裝 SSMS (視您的 Windows 版本，請使用 [應用程式與功能]  、[程式與功能]  )。

2. **從提升權限的命令提示字元** 解除安裝 Visual Studio 2015 IsoShell：

    ```PUSHD "C:\ProgramData\Package Cache\FE948F0DAB52EB8CB5A740A77D8934B9E1A8E301\redist"```

    ```vs_isoshell.exe /Uninstall /Force /PromptRestart```

3. 以您解除安裝任何應用程式的相同方式，解除安裝 Microsoft Visual C++ 2015 可轉散發套件。 若電腦上有 x86 及 x64，則兩者都解除安裝。

4. **從提升權限的命令提示字元** 重新安裝 Visual Studio 2015 IsoShell：  

    ```PUSHD "C:\ProgramData\Package Cache\FE948F0DAB52EB8CB5A740A77D8934B9E1A8E301\redist"```  

    ```vs_isoshell.exe /PromptRestart```

5. 重新安裝 SSMS。

6. 若您目前不是最新狀態，請升級為[最新版的 Visual C++ 2015 可轉散發套件](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)。

### <a name="1653"></a>16.5.3

![下載](media/download-icon.png) [下載 SSMS 16.5.3](https://go.microsoft.com/fwlink/?LinkID=840946)

- 版本號碼：16.5.3  
- 組建編號：13.0.16106.4  
- 發行日期：2017 年 1 月 30 日

[簡體中文](https://go.microsoft.com/fwlink/?linkid=840946&clcid=0x804)| [繁體中文](https://go.microsoft.com/fwlink/?linkid=840946&clcid=0x404)| [英文 (美國)](https://go.microsoft.com/fwlink/?linkid=840946&clcid=0x409)| [法文](https://go.microsoft.com/fwlink/?linkid=840946&clcid=0x40c)| [德文](https://go.microsoft.com/fwlink/?linkid=840946&clcid=0x407)| [義大利文](https://go.microsoft.com/fwlink/?linkid=840946&clcid=0x410)| [日文](https://go.microsoft.com/fwlink/?linkid=840946&clcid=0x411)| [韓文](https://go.microsoft.com/fwlink/?linkid=840946&clcid=0x412)| [葡萄牙文 (巴西)](https://go.microsoft.com/fwlink/?linkid=840946&clcid=0x416)| [俄文](https://go.microsoft.com/fwlink/?linkid=840946&clcid=0x419)| [西班牙文](https://go.microsoft.com/fwlink/?linkid=840946&clcid=0x40a)

#### <a name="bug-fixes-in-1653"></a>16.5.3 中的 Bug 修正

- 已修正 SSMS 16.5.2 中出現的問題，這造成 'Table' 節點會在資料表有多個疏鬆資料行時展開。

- 使用者可以將包含 OData 連線管理員且連線到 Microsoft Dynamics AX/CRM Online 資源的 SSIS 套件部署到 SSIS 目錄。 如需詳細資訊，請參閱 [OData 連線管理員](../integration-services/connection-manager/odata-connection-manager.md)。

- 在現有資料表上設定 Always Encrypted 失敗，不相關的物件發生錯誤。 [Connect 識別碼 3103181](https://connect.microsoft.com/SQLServer/feedback/details/3103181/setting-up-always-encrypted-on-an-existing-table-fails-with-errors-on-unrelated-objects)

- 無法為具有多個結構描述的現有資料庫設定 Always Encrypted。 [Connect 識別碼 3109591](https://connect.microsoft.com/SQLServer/feedback/details/3109591/sql-server-2016-always-encrypted-against-existing-database-with-multiple-schemas-doesnt-work)

- Always Encrypted、加密資料行精靈失敗，原因是資料庫包含參考系統檢視的檢視。 [Connect 識別碼 3111925](https://connect.microsoft.com/SQLServer/feedback/details/3111925/sql-server-2016-always-encrypted-encrypted-column-wizard-failed-task-failed-due-to-following-error-cannot-save-package-to-file-the-model-has-build-blocking-errors)

- 使用 Always Encrypted 進行加密時，未正確處理加密後來自重新整理模組的錯誤。

- 「開啟最近使用的項目」  功能表未顯示最近儲存的檔案。 [Connect 識別碼 3113288](https://connect.microsoft.com/SQLServer/feedback/details/3113288/ssms-2016-open-recent-menu-doesnt-show-recently-saved-files)

- SSMS 在以右鍵按一下資料表索引時 (透過遠端 (網際網路) 連線) 很緩慢。 [Connect 識別碼 3114074](https://connect.microsoft.com/SQLServer/feedback/details/3114074/ssms-slow-when-right-clicking-an-index-for-a-table-over-a-remote-internet-connection)

- 修正 SQL Designer 捲軸的問題。 [Connect 識別碼 3114856](https://connect.microsoft.com/SQLServer/feedback/details/3114856/bug-in-scrollbar-on-sql-desginer-in-ssms-2016)

- 資料表的操作功能表暫時停止回應

- SSMS 有時會擲回活動監視器的例外狀況及損毀。 [Connect 識別碼 697527](https://connect.microsoft.com/SQLServer/feedback/details/697527/)

- SSMS 2016 會損毀，並出現錯誤「處理序因位於 IP 71AF8579 (71AE0000) 的 .NET 執行階段發生內部錯誤而終止，錯誤碼為 80131506」

## <a name="additional-downloads"></a>其他下載

如需所有 SQL Server Management Studio 的下載清單，請搜尋 [Microsoft 下載中心](https://www.microsoft.com/download/search.aspx?q=sql%20server%20management%20studio&p=0&r=10&t=&s=Relevancy~Descending)。  
  
若要取得最新版的 SQL Server Management Studio，請參閱[下載 SQL Server Management Studio &#40;SSMS&#41;](../ssms/download-sql-server-management-studio-ssms.md)。