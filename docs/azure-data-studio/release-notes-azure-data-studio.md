---
title: Azure Data Studio 版本資訊
description: 本文包含 Azure Data Studio 從 2017 年 11 月至今版本的版本資訊。 針對許多摘要問題，請參閱其他詳細資料的連結。
ms.prod: azure-data-studio
ms.technology: azure-data-studio
ms.topic: conceptual
author: yualan
ms.author: alayu
ms.reviewer: maghan
ms.custom: seodec18
ms.date: 12/9/2020
ms.openlocfilehash: 796499f38c9af2d7b2f6af8f860bb945d14bdac2
ms.sourcegitcommit: d8cdbb719916805037a9167ac4e964abb89c3909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/20/2021
ms.locfileid: "98596507"
---
# <a name="release-notes-for-azure-data-studio"></a>Azure Data Studio 的版本資訊

**[下載並安裝最新版本！](./download-azure-data-studio.md)**

## <a name="december-2020"></a>2020 年 12 月

2020 年 12 月 9 日 &nbsp; / &nbsp; 版本：1.25.0

&nbsp;

| 變更 | 詳細資料 |
| ------ | ------- |
| 錯誤修正 | 如需完整的修正清單，請參閱 [GitHub 的 Bug 和問題](https://github.com/microsoft/azuredatastudio/issues?q=is%3Aissue+is%3Aclosed+milestone%3A%22December+2020+Release%22)。 |
| 資料庫專案延伸模組更新 | 新增了工作區與改善的提要欄位 |

## <a name="november-2020"></a>2020 年 11 月

2020 年 11 月 12 日 &nbsp; / &nbsp; 版本：1.24.0

&nbsp;

| 變更 | 詳細資料 |
| ------ | ------- |
| 錯誤修正 | 如需完整的修正清單，請參閱 [GitHub 的 Bug 和問題](https://github.com/microsoft/azuredatastudio/issues?q=is%3Aissue+milestone%3A%22November+2020+Release%22+is%3Aclosed)。 |
| 連線對話方塊 | 已為連線對話方塊新增瀏覽索引標籤。 |
| 延伸模組更新 | 已發行 Postgres 延伸模組的更新。 |
| 新增 Notebook 功能 | 已將新功能新增至 SQL，以支援筆記本。 <br/> 已將新功能新增至 Notebook 參數化支援。 <br/>  已將新功能新增至 SQL 筆記本的結果串流。 |
| Python 安裝 | PROSE 套件已從預設的 Python 安裝中移除。 |

### <a name="known-issues-1240"></a>已知問題 (1.24.0)

| 新項目 | 詳細資料 | 因應措施 |
|----------|---------|------------|
| Azure Arc 延伸模組 | [已知問題：](https://github.com/microsoft/azuredatastudio/issues/13319)Arc MIAA & PG 部署的 [Script to Notebook] 按鈕不會在撰寫筆記本指令碼之前執行欄位驗證。 這表示，如果使用者在密碼確認輸入中輸入錯誤的密碼，最後可能會使得筆記本具有錯誤的密碼值。| 不過 [部署] 按鈕會如預期般運作，因此使用者應該可以改為加以使用。 |
| 物件總管 | 1\.24.0 之前的 ADS 版本在物件總管中有中斷性變更，其原因是與 [Azure Synapse Analytics 無伺服器 SQL 集區](/azure/synapse-analytics/sql/on-demand-workspace-overview)相關的引擎變更。 | 若要繼續搭配 Azure Synapse Analytics 無伺服器 SQL 集區在 Azure Data Studio 中使用物件總管，您必須使用 Azure Data Studio 1.24.0 或更新版本。 |

針對其他已知問題，您可參考 [Azure Data Studio 使用者意見反應](https://github.com/microsoft/azuredatastudio)並為產品小組提供意見反應。

## <a name="october-2020"></a>2020 年 10 月

2020 年 10 月 14 日 &nbsp; / &nbsp; 版本：1.23.0

&nbsp;

| 變更 | 詳細資料 |
| ------ | ------- |
| Azure SQL Edge | 支援 Azure SQL Edge 物件。 |
| 錯誤修正 | 如需完整的修正清單，請參閱 [GitHub 的 Bug 和問題](https://github.com/microsoft/azuredatastudio/issues?q=is:issue+milestone:%22October+2020+Release%22+is:closed)。 |
| 資料庫| 支援相同的資料庫參考。 |
| 擴充功能更新 | [Azure Arc](extensions/azure-arc-extension.md)</br>[azdata](../azdata/install/deploy-install-azdata.md)</br>[Machine Learning](extensions/machine-learning-extension.md)</br>[Kusto (KQL)](extensions/kusto-extension.md)</br>[結構描述比較](extensions/schema-compare-extension.md)</br>SQL 評估</br>[SQL Database 專案](extensions/sql-database-project-extension.md)</br>[SQL Server 匯入](extensions/sql-server-import-extension.md) |
| 新的部署功能 | 已新增 Azure SQL DB 和 VM 部署。 |
| PowerShell | 已新增 PowerShell 核心結果串流支援。 |

## <a name="september-2020-hotfix"></a>2020 年 9 月 (Hotfix)

2020 年 9 月 30 日 &nbsp; / &nbsp; 版本：1.22.1

&nbsp;

| 變更 | 詳細資料 |
| ------ | ------- |
| 已解決的 Bug 和問題 | 如需完整的修正清單，請參閱 [GitHub 的 Bug 和問題](https://github.com/microsoft/azuredatastudio/releases/tag/1.22.1)。 |

## <a name="september-2020"></a>2020 年 9 月

2020 年 9 月 22 日 &nbsp; / &nbsp; 版本：1.22.0

&nbsp;

| 變更 | 詳細資料 |
| ------ | ------- |
| 新增 Notebook 功能 | <br/> &bull; &nbsp; 支援全新的文字資料格編輯體驗，該體驗是以 RTF 格式和順暢轉換為 Markdown (也稱為 WYSIWYG (所見即所得) 工具列) 為基礎 <br/> &bull; &nbsp; 支援 Kusto 核心 <br/> &bull; &nbsp; 支援筆記本釘選 <br/> &bull; &nbsp; 已新增新版 Jupyter Book 的支援 <br/> &bull; &nbsp; 已改善 Jupyter 快速鍵 <br/> &bull; &nbsp; 引進效能載入改善 |
| SQL Database Projects 延伸模組 | SQL Database 專案延伸模組將以專案為基礎的資料庫開發帶入 Azure Data Studio。 在此預覽版本中，可以從 Azure Data Studio 建立和發佈 SQL 專案。 |
| Kusto (KQL) 延伸模組 | 在 Azure Data Studio 中帶入原生 Kusto 體驗，以便針對儲存在 Azure 資料總管中的大量即時串流資料進行資料探索和資料分析。 此預覽版本支援連線和瀏覽 Azure 資料總管叢集、撰寫 KQL 查詢，以及使用 Kusto 核心編寫筆記本。 |
| Azure Arc 延伸模組 | 使用者可以透過 Azure Data Studio 試用 Azure Arc 公開預覽版。 這包括： <br/> &bull; &nbsp; 部署資料控制器 <br/> &bull; &nbsp; 部署 Postgres <br/> &bull; &nbsp; 部署適用於 Azure Arc 的受控執行個體 <br/> &bull; &nbsp; 連線到資料控制器 <br/> &bull; &nbsp; 存取資料服務儀表板 <br/> &bull; &nbsp; Azure Arc Jupyter 書籍 |
| 部署選項 | <br/> &bull; &nbsp;Azure SQL Database Edge <br/> (Edge 會需要 Azure SQL Edge 部署延伸模組) |
| SQL Server 匯入延伸模組 GA | 宣佈 SQL Server 匯入延伸模組的 GA，功能不再處於預覽狀態。 此延伸模組可協助匯入 csv/txt 檔案。 在[本文](./extensions/sql-server-import-extension.md)中深入了解此延伸模組。 |
| 已解決的 Bug 和問題 | 如需完整的修正清單，請參閱 [GitHub 上的 Bug 和問題](https://github.com/microsoft/azuredatastudio/issues?q=is%3Aissue+milestone%3A%22September+2020+Release%22+is%3Aclosed) \(英文\)。 |

## <a name="august-2020"></a>2020 年 8 月

2020 年 8 月 12 日 &nbsp; / &nbsp; 版本：1.21.0

&nbsp;

| 變更 | 詳細資料 |
| :----- | :------ |
| 新增 Notebook 功能 | &bull; &nbsp; 移動資料格位置 <br/> &bull; &nbsp; 將資料格轉換成文字資料格或程式碼資料格
| Jupyter 書籍選擇器 | 使用者現在可以從 GitHub 版本中選擇 Jupyter Book，並在 Azure Data Studio 中順暢地開啟 |
| 已將搜尋新增至 Notebooks Viewlet | 使用者可以輕鬆地在其筆記本與 Jupyter 叢書中搜尋內容 |
| 已解決的 Bug 和問題 | 如需完整的修正清單，請參閱 [GitHub 上的 Bug 和問題](https://github.com/microsoft/azuredatastudio/issues?q=is%3Aissue+milestone%3A%22August+2020+Release%22+is%3Aclosed) \(英文\)。 |
| &nbsp; | &nbsp; |

## <a name="july-2020-hotfix"></a>2020 年 7月 (Hotfix)

2020 年 7 月 17 日 &nbsp; / &nbsp; 版本：1.20.1

&nbsp;

| 變更 | 詳細資料 |
| :----- | :------ |
| 修正 Bug #11372 物件總管拖放資料表不正確地包裝資料表名稱 | [#11372](https://github.com/microsoft/azuredatastudio/issues/11372) |
| 修正 Bug #11356 深色佈景主題現在是預設佈景主題 | [#11356](https://github.com/microsoft/azuredatastudio/issues/11356) |
| &nbsp; | &nbsp; |

### <a name="known-issue"></a>已知問題

- 某些使用者已回報此版本所隨附的新 Microsoft.Data.SqlClient v2.0.0 發生連線錯誤。 使用者發現[遵循這些指示](https://github.com/microsoft/azuredatastudio/issues/11367#issuecomment-659614111)可成功連線

## <a name="july-2020"></a>2020 年 7 月

2020 年 7 月 15 日 &nbsp; / &nbsp; 版本：1.20.0

&nbsp;

| 變更 | 詳細資料 |
| :----- | :------ |
| 新增功能導覽 | 在歡迎頁面與命令選擇區中，使用者現在可以啟動功能導覽，以取得常用功能的逐步解說，包括連線 Viewlet、Notebooks Viewlet，以及延伸模組 Marketplace |
| 新增 Notebook 功能 | &bull; &nbsp; Markdown 工具列中的標頭支援<br/> &bull; &nbsp; 文字資料格中的並排 Markdown 預覽
| 在查詢編輯器中拖放資料行與資料表 | 使用者現在可以將資料行與資料表直接從連線 viewlet 拖放到查詢編輯器 |
| 已新增 Azure 帳戶圖示至活動列 | 使用者現在可以輕鬆地查看要從哪裡登入 Azure |
| 已解決的 Bug 和問題 | 如需完整的修正清單，請參閱 [GitHub 上的 Bug 和問題](https://github.com/microsoft/azuredatastudio/issues?q=is%3Aissue+milestone%3A%22July+2020+Release%22+is%3Aclosed) \(英文\)。 |
| &nbsp; | &nbsp; |

## <a name="june-2020"></a>2020 年 6 月

2020 年 6 月 15 日 &nbsp; / &nbsp; 版本：1.19.0

&nbsp;

| 變更 | 詳細資料 |
| :----- | :------ |
| 已將 Azure Data Studio 新增至 Azure 入口網站整合 | 使用者現在可直接從 Azure SQL Database 連線、Azure Postgres 等啟動至 Azure 入口網站。 |
| 新增 Notebook 功能 | &bull; &nbsp; 新增筆記本工具列 <br/> &bull; &nbsp; 新增編輯儲存格工具列 <br/> &bull; &nbsp; Python 相依性精靈 UX 更新 <br/> &bull; &nbsp; 筆記本之間改善的間距 |
| 宣告 SQL 評定 API 延伸模組 | 此延伸模組會在 ADS 中新增 SQL Server 最佳做法評定。 其會公開先前僅適用於 PowerShell SqlServer 課程模組與 SMO 的 SQL 評定 API，以供評估 SQL Server 執行個體，以及透過 SQL Server 小組接收建議。 了解有關 SQL 評定 API，以及其在[本文](../tools/sql-assessment-api/sql-assessment-api-overview.md)中的功能。 |
| [機器學習延伸模組的改善](./extensions/machine-learning-extension.md) | 現在支援 Azure SQL 受控執行個體。 |
| 資料虛擬化延伸模組的改善 | 現在支援 MongoDB 與 Teradata |
| Postgres 延伸模組 Bug 修正 | 固定的 Azure MFA |
| 已解決的 Bug 和問題 | 如需完整的修正清單，請參閱 [GitHub 上的 Bug 和問題](https://github.com/microsoft/azuredatastudio/issues?q=is%3Aissue+milestone%3A%22June+2020+Release%22+is%3Aclosed) \(英文\)。 |
| &nbsp; | &nbsp; |

## <a name="may-2020-hotfix"></a>2020 年 5月 (Hotfix)

2020 年 5 月 27 日 &nbsp; / &nbsp; 版本：1.18.1

&nbsp;

| 變更 | 詳細資料 |
| :----- | :------ |
| 修正 Bug #10538 「執行目前的查詢」按鍵綁定的指令不再如預期般運作 | [#10538](https://github.com/microsoft/azuredatastudio/issues/10538)  |
| 修正 Bug #10537 無法在 v 1.18 上開啟新的或現有 SQL 檔案 | [#10537](https://github.com/microsoft/azuredatastudio/issues/10537)  |
| &nbsp; | &nbsp; |

## <a name="may-2020"></a>2020 年 5 月

2020 年 5 月 20 日 &nbsp; / &nbsp; 版本：1.18.0

&nbsp;

| 變更 | 詳細資料 |
| :----- | :------ |
| 宣告 Redgate SQL Prompt 延伸模組 | 此延伸模組可供直接在 Azure Data Studio 內管理格式化樣式，無須離開 IDE 即可建立及編輯樣式。 |
| 宣告 Machine Learning 延伸模組 | 此延伸模組可供： <br/> &bull; &nbsp; 使用 SQL Server 機器學習服務搭配 Azure Data Studio 來管理 Python 與 R 套件 。<br/> &bull; &nbsp; 使用 ONNX 模型，在 Azure SQL Edge 中進行預測。<br/> &bull; &nbsp; 檢視 Azure SQL Edge 資料庫中的 ONNX 模型。 <br/> &bull; &nbsp; 將 ONNX 模型從檔案或 Azure Machine Learning 匯入到 Azure SQL Edge 資料庫。 <br/> &bull; &nbsp; 建立筆記本來執行實驗。 |
| 新增 Notebook 功能 | &bull; &nbsp; 新增了 Python 相依性精靈，讓您更輕鬆地安裝 Python 相依性 <br/> &bull; &nbsp; 新增了 Markdown 工具列的底線支援 |
| Always Encrypted 的參數化 | 可供執行依據加密資料庫資料行來插入、更新或篩選的查詢。|
| 已解決的 Bug 和問題 | 如需完整的修正清單，請參閱 [GitHub 上的 Bug 和問題](https://github.com/microsoft/azuredatastudio/issues?q=is%3Aissue+milestone%3A%22May+2020+Release%22+is%3Aclosed) \(英文\)。 |
| &nbsp; | &nbsp; |

## <a name="april-2020-hotfix"></a>2020 年 4 月 (Hotfix)

2020 年 4 月 30 日 &nbsp; / &nbsp; 版本：1.17.1

&nbsp;

| 變更 | 詳細資料 |
| :----- | :------ |
| 修正 Bug #10197 無法透過 MFA 連線 | [#10197](https://github.com/microsoft/azuredatastudio/issues/10197)  |
| &nbsp; | &nbsp; |

## <a name="april-2020"></a>2020 年 4 月

2020 年 4 月 27 日 &nbsp; / &nbsp; 版本：1.17.0

&nbsp;

| 變更 | 詳細資料 |
| :----- | :------ |
| 改善的歡迎頁面 | 歡迎頁面上的 UI 更新，可供更輕鬆地查看一般動作和醒目提示延伸模組。 |
| 新增 Notebook 功能 | &bull; &nbsp; 在編輯文字資料格時新增 Markdown 工具列，以協助使用 Markdown 來撰寫 <br/> &bull; &nbsp; 改寫 Jupyter Book viewlet 使其成為 Notebook viewlet，您可在其中同時管理 Jupyter Book 和筆記本 <br/>&bull; &nbsp; 新增支援在儲存筆記本時保存圖表 <br/> &bull; &nbsp; 新增支援 Python 筆記本中的 KQL magic|
| 改善的儀表板 | Azure Data Studio 中的儀表板已更新為最新設計模式，包括 [動作] 工具列。 這也適用於許多延伸模組。 |
| 在 Azure 檢視中新增了 Cloud Shell 整合。 | |
| 支援 Always Encrypted 與具有安全記憶體保護區的 Always Encrypted。 | |
| 已解決的 Bug 和問題 | 如需完整的修正清單，請參閱 [GitHub 上的 Bug 和問題](https://github.com/microsoft/azuredatastudio/issues?q=is%3Aissue+is%3Aclosed+milestone%3A%22April+2020+Release%22) \(英文\)。 |
| &nbsp; | &nbsp; |
| 已解決的 Bug 和問題 | 如需完整的修正清單，請參閱 [GitHub 上的 Bug 和問題](https://github.com/microsoft/azuredatastudio/issues?q=is%3Aissue+is%3Aclosed+milestone%3A%22April+2020+Release%22) \(英文\)。 |
| &nbsp; | &nbsp; |

## <a name="march-2020"></a>2020 年 3 月

2020 年 3 月 18 日 &nbsp; / &nbsp; 版本：1.16.0 

&nbsp;

| 變更 | 詳細資料 |
| :----- | :------ |
| 已在 SQL Notebooks 中新增圖表支援 | 現於程式碼資料格中執行 SQL 查詢時，使用者可以建立和儲存圖表。 |
| 已新增建立 Jupyter Book 體驗 | 使用者現在可以使用筆記本建立自己的 Jupyter Book。 |
| Postgres 延伸模組已新增 Azure AD 支援 | |
| 已修正許多協助工具 Bug | [協助工具 Bug 清單](https://github.com/microsoft/azuredatastudio/issues?page=1&q=is%3Aissue+is%3Aclosed+milestone%3A%22S360+-+Accessibility%22+label%3AA11y_AzureDataStudio) |
| VS Code 合併到 1.42 | 此版本包含前 3 個 VS Code 版本的 VS Code 更新。 若要深入了解，請[閱讀其版本資訊](https://code.visualstudio.com/updates/v1_42)。 |
| 已解決的 Bug 和問題 | 如需完整的修正清單，請參閱 [GitHub 上的 Bug 和問題](https://github.com/microsoft/azuredatastudio/issues?q=is%3Aissue+milestone%3A%22March+2020%22+is%3Aclosed) \(英文\)。 |
| &nbsp; | &nbsp; |

## <a name="february-hotfix"></a>2 月 (Hotfix)

2020 年 2 月 19 日 &nbsp; / &nbsp; 版本：1.15.1

&nbsp;

| 變更 | 詳細資料 |
| :----- | :------ |
| 修正 Bug #9149：顯示使用中的連線 | [#9149](https://github.com/microsoft/azuredatastudio/issues/9149)  |
| 修正 Bug #9061：在顯示或隱藏 SQL 窗格時，編輯資料格不會正確調整大小 | [#9061](https://github.com/microsoft/azuredatastudio/issues/9061)  |
| &nbsp; | &nbsp; |

## <a name="february-2020"></a>2020 年 2 月

2020 年 2 月 13 日 &nbsp; / &nbsp; 版本：1.15.0

&nbsp;

| 變更 | 詳細資料 |
| :----- | :------ |
| 新的 Azure 登入改善 | 已新增改善的 Azure 登入體驗，包括移除複製/貼上裝置程式碼，以提供更順暢的連線體驗。 |
| [Find in Notebook] \(在筆記本中尋找\) 支援 | 使用者現在可以在筆記本內使用 Ctrl+F。 [Find in Notebook] \(在筆記本中尋找\) 支援會逐行搜尋程式碼和文字儲存格。 |
| VS Code 1.38 到 1.42 的合併 | 此版本包含前 3 個 VS Code 版本的 VS Code 更新。 若要深入了解，請[閱讀其版本資訊](https://code.visualstudio.com/updates/v1_42)。 |
| 修正許多使用者回報的[「白色/空白畫面」](https://github.com/microsoft/azuredatastudio/issues/8775)問題。 | |
| 已解決的 Bug 和問題 | 如需完整的修正清單，請參閱 [GitHub 上的 Bug 和問題](https://github.com/microsoft/azuredatastudio/issues?q=is%3Aissue+is%3Aclosed+milestone%3A%22February+2020%22) \(英文\)。 |
| &nbsp; | &nbsp; |

### <a name="known-issue"></a>已知問題

- MacOS Catalina 的使用者必須以滑鼠右鍵按一下 Azure Data Studio，然後按一下 [開啟]。

## <a name="december-2019-hotfix"></a>2019 年 12 月 (Hotfix)

2019 年 12 月 26 日 &nbsp; / &nbsp; 版本：1.14.1

&nbsp;

| 變更 | 詳細資料 |
| :----- | :------ |
| 修正錯誤 #8747 OE 擴充失敗 | [#8747](https://github.com/microsoft/azuredatastudio/issues/8747)  |
| &nbsp; | &nbsp; |

## <a name="december-2019"></a>2019 年 12 月

2019 年 12 月 19 日 &nbsp; / &nbsp; 版本：1.14.0 

&nbsp;

| 變更 | 詳細資料 |
| :----- | :------ |
| 已將 Notebooks 中的 [附加至連線] 下拉式清單變更為僅列出目前使用中的連線 | [#8129](https://github.com/microsoft/azuredatastudio/issues/8129) |
| 已新增 bigdatacluster.ignoreSslVerification 設定以允許在連線至 BDC 時忽略 TLS/SSL 驗證錯誤 | [#8582](https://github.com/microsoft/azuredatastudio/pull/8582) |
| 允許針對離線查詢編輯器變更預設語言類別 | [#8419](https://github.com/microsoft/azuredatastudio/pull/8419) |
| 巨量資料叢集/SQL 2019 功能的 GA 狀態 | [#8269](https://github.com/microsoft/azuredatastudio/issues/8269) |
| 已解決的 Bug 和問題 | 如需完整的修正清單，請參閱 [GitHub 上的 Bug 和問題](https://github.com/microsoft/azuredatastudio/milestone/44?closed=1) \(英文\)。 |
| &nbsp; | &nbsp; |


## <a name="november-2019-hotfix"></a>2019 年 11 月 (Hotfix)

2019 年 11 月 15 日 &nbsp; / &nbsp; 版本：1.13.1

&nbsp;

| 變更 | 詳細資料 |
| :----- | :------ |
| 修正錯誤 (Bug) #8210 複製/貼上結果順序不正確 |  |
| &nbsp; | &nbsp; |

## <a name="november-2019"></a>2019 年 11 月

2019 年 11 月 4 日 &nbsp; / &nbsp; 版本：1.13.0 

&nbsp;

| 變更 | 詳細資料 |
| :----- | :------ |
| 新的 SQL Server 2019 支援 | &bull; &nbsp; 使用 BDC 部署精靈部署 SQL Server 2019 巨量資料叢集 <br/>&bull; &nbsp; 使用控制器儀表板管理叢集健康情況 <br/>&bull; &nbsp; 使用安全性 ACL 對話方塊管理 HDFS 存取控制清單 <br/> &bull; &nbsp; 使用 HDFS 分層對話方塊新增掛接 <br/> &bull; &nbsp; 使用內建的 Jupyter Book (SQL Server 2019 指南) 進行疑難排解 <br/> &bull; &nbsp; SQL vNext 延伸模組重新命名為資料虛擬化延伸模組 <br/> &bull; &nbsp; 外部資料表精靈中新增 Teradata 和 Mongo 的支援|
| 新增 Notebook 功能 | &bull; &nbsp; 宣告 Powershell 筆記本 <br/> &bull; &nbsp; 宣佈可摺疊的程式碼資料格 <br/>&bull; &nbsp; Notebooks 中的效能改進 <br/> &bull; &nbsp; 在[這裡](https://github.com/microsoft/azuredatastudio/issues?q=is%3Aissue+milestone%3A%22November+2019+Release%22+is%3Aclosed+label%3A%22Area+-+Notebooks%22) \(英文\) 檢視完整的改進清單 |
| 推出 Jupyter Book  | Jupyter Books 是整理成目錄的 Notebook 和 Markdown 檔案集合。 |
| 新增 SQL Server 部署精靈  | 現在支援部署： <br/> &bull; &nbsp; Windows 上的 SQL Server 2019 <br/> &bull; &nbsp; Windows 上的 SQL Server 2017 <br/> &bull; &nbsp; Docker 上的 SQL Server 2019 <br/> &bull; &nbsp; Docker 上的 SQL Server 2017 |
| 宣佈正式推出「結構描述比較」延伸模組| &bull; &nbsp; SQLCMD 模式 <br/> &bull; &nbsp; 當地語系化支援 <br/> &bull; &nbsp; 協助工具修正 <br/> &bull; &nbsp; 安全性錯誤 (Bug)  |
| 宣佈 SQL Server Dacpac 延伸模組正式推出| <br/> &bull; &nbsp; 當地語系化支援 <br/> &bull; &nbsp; 協助工具修正 <br/> &bull; &nbsp; 安全性錯誤 (Bug) |
| 宣佈 Visual Studio IntelliCode 延伸模組 | Visual Studio IntelliCode 現在支援 SQL，可提供保留關鍵字更聰明的建議。 |
| 已解決的 Bug 和問題 | 如需完整的修正清單，請參閱 [GitHub 上的 Bug 和問題](https://github.com/microsoft/azuredatastudio/issues?q=is%3Aissue+milestone%3A%22November+2019+Release%22+is%3Aclosed) \(英文\)。 |
| &nbsp; | &nbsp; |

## <a name="october-2019-hotfix-2"></a>2019 年 10 月 (Hotfix 2)

2019 年 10 月 11 日 &nbsp; / &nbsp; 版本：1.12.2

&nbsp;

| 變更 | 詳細資料 |
| :----- | :------ |
| 在檢查模式中停用自動啟動 EH |  |
| &nbsp; | &nbsp; |

## <a name="october-2019-hotfix"></a>2019 年 10 月 (Hotfix)

2019 年 10 月 8 日 &nbsp; / &nbsp; 版本：1.12.1

&nbsp;

| 變更 | 詳細資料 |
| :----- | :------ |
| 修正 Notebooks 中的引號和反斜線問題以正確逸出。 |  |
| &nbsp; | &nbsp; |

## <a name="october-2019"></a>2019 年 10 月

2019 年 10 月 2 日 &nbsp; / &nbsp; 版本：1.12.0

&nbsp;

| 變更 | 詳細資料 |
| :----- | :------ |
| 發行查詢記錄延伸模組 | SQL 記錄延伸模組會儲存所有在 Azure Data Studio 工作階段中執行的過去查詢，並依執行順序列出它們。 使用者可以看到開啟查詢、執行查詢、刪除查詢、暫停查詢記錄或刪除所有查詢記錄項目。 |
| 新增複製/貼上結果 | 我們已新增從結果方格複製/貼上結果的其他方式。 |
| 更新為 Powershell 延伸模組 |  |
| 已解決的 Bug 和問題 | 如需完整的修正清單，請參閱 [GitHub 上的 Bug 和問題](https://github.com/microsoft/azuredatastudio/milestone/42?closed=1) \(英文\)。 |
| &nbsp; | &nbsp; |

### <a name="known-issues"></a>已知問題

- Notebooks
  - [7080](https://github.com/microsoft/azuredatastudio/issues/7080) 不正確地序列化筆記本的罕見案例

## <a name="september-2019"></a>2019 年 9 月

2019 年 9 月 10 日 &nbsp; / &nbsp; 版本：1.11.0 

&nbsp;

| 變更 | 詳細資料 |
| :----- | :------ |
| 啟用 SQLCMD 模式 | 查詢編輯器現在支援切換 SQLCMD 模式，以將查詢撰寫和編輯為 SQLCMD 指令碼 |
| 社群延伸模組：查詢編輯器提升 | Query Editor Boost 是開放原始碼延伸模組，著重於為經常撰寫查詢的使用者增強 Azure Data Studio 查詢編輯器。 &bull; &nbsp; 將目前的查詢儲存為程式碼片段 <br/>&bull; &nbsp; 使用 Ctrl+U 切換資料庫 <br/> &bull; &nbsp; 從範本新增查詢 <br/> &bull; &nbsp; 在[這裡](https://github.com/dzsquared/query-editor-boost) \(英文\) 檢視完整的改進清單 |
| Notebook 改進 | &bull; &nbsp; 支援較大筆記本檔案的效能改善 <br/> &bull; &nbsp; 在[這裡](https://github.com/microsoft/azuredatastudio/issues?q=is%3Aissue+milestone%3A%22September+2019+Release%22+label%3A%22Area%3A+Notebooks%22+is%3Aclosed) \(英文\) 檢視完整的改進清單 |
| Visual Studio Code 8 月版本合併 1.38 | 您可以在[此處](https://code.visualstudio.com/updates/v1_38) \(英文\) 找到最新的改進。 |
| 已解決的 Bug 和問題 | 如需完整的修正清單，請參閱 [GitHub 上的 Bug 和問題](https://github.com/microsoft/azuredatastudio/milestone/39?closed=1) \(英文\)。 |
| &nbsp; | &nbsp; |

### <a name="known-issues"></a>已知問題

- Notebooks
  - [7080](https://github.com/microsoft/azuredatastudio/issues/7080) 不正確地序列化筆記本的罕見案例

## <a name="august-2019"></a>2019 年 8 月

2019 年 8 月 15 日&nbsp; / &nbsp;版本：1.10.0 

&nbsp;

| 變更 | 詳細資料 |
| :----- | :------ |
| SandDance 1.3.1 擴充功能的版本 | &bull; &nbsp; 智慧圖表偵測 <br/>&bull; &nbsp; 3D 視覺效果 <br/> &bull; &nbsp; 資料篩選 |
| Notebook 改進 | &bull; &nbsp; 以內嵌方式新增程式碼或文字資料格 <br/>&bull; &nbsp; 已新增以滑鼠右鍵按一下 SQL 結果方格以將結果儲存為 CSV、JSON 等的功能 <br/> &bull; &nbsp; 已改善筆記本載入效能，加快載入 JSON 的速度 <br/> &bull; &nbsp; 在[這裡](https://github.com/microsoft/azuredatastudio/issues?q=is%3Aissue+label%3A%22Area%3A+Notebooks%22+milestone%3A%22August+2019+Release%22+is%3Aclosed) \(英文\) 檢視完整的改進清單 |
| SQL Server 2019 支援 |  此版本包含對額外 SQL Server 2019 巨量資料叢集功能的支援，包括： <br/> &bull; &nbsp; 減少了在物件對應頁面上載入資料表與資料行資訊所花費的時間。 <br/> &bull; &nbsp; 已修正在連線詳細資料頁面上載入現有資料庫範圍認證的錯誤 (Bug)。 <br/> &bull; &nbsp;已增加用於 PROSE 剖析的預設樣本大小。 | 
| Dacpac 延伸模組現在支援 Azure AD | 
| Visual Studio Code 7 月版本合併 1.37 | 您可以在[此處](https://code.visualstudio.com/updates/v1_37) \(英文\) 找到最新的改進。 |
| 已解決的 Bug 和問題 | 如需完整的修正清單，請參閱 [GitHub 上的 Bug 和問題](https://github.com/microsoft/azuredatastudio/milestone/39?closed=1) \(英文\)。 |
| &nbsp; | &nbsp; |

## <a name="july-2019"></a>2019 年 7 月

2019 年 7 月 11 日 &nbsp; / &nbsp; 版本：1.9.0 

&nbsp;

| 變更 | 詳細資料 |
| :----- | :------ |
| SentryOne Plan Explorer 延伸模組的版本 | 我們的重要 Microsoft 合作夥伴 SentryOne 將會發送其[適用於 Azure Data Studio 的 SentryOne Plan Explorer 延伸模組](https://www.sentryone.com/products/sentryone-plan-explorer-extension-azure-data-studio) \(英文\)。 <br> 這是一個免費的延伸模組，可針對在 Azure Data Studio 中執行的查詢提供增強的計劃圖表，並使用最佳化的版面配置演算法和直覺式色彩編碼，來協助您快速識別最耗費資源且會影響查詢效能的運算子。 若要深入了解此延伸模組，請查看[此處](https://sqlperformance.com/2019/07/sentryone/plan-explorer-extension-azure-data-studio) \(英文\) 的 SentryOne 部落格文章。 |
| 即將推出結構描述比較的新功能 | &bull; &nbsp; 結構描述比較檔案支援 (.SCMP) <br/>&bull; &nbsp; 取消結構描述比較支援 <br/>&bull; &nbsp; 您可以在[這裡](https://github.com/microsoft/azuredatastudio/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A%22July+2019+Release%22+label%3A%22Area%3A+Schema+Compare%22+is%3Aclosed+) \(英文\) 找到完整的變更|
| Notebook 改進 | &bull; &nbsp; Plotly Python 支援 <br/>&bull; &nbsp; 從瀏覽器開啟 Notebook <br/> &bull; &nbsp; Python 套件管理對話方塊 <br/> &bull; &nbsp; 效能和 Markdown 增強功能 <br/> &bull; &nbsp; 鍵盤快速鍵更新 <br/>  &bull; &nbsp; 您可以在[這裡](https://github.com/microsoft/azuredatastudio/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A%22July+2019+Release%22+is%3Aclosed+label%3A%22Area%3A+Notebooks%22+) \(英文\) 找到錯誤 (Bug) 修正與次要功能 |
| SQL Server 2019 支援 |  此版本包含對額外 SQL Server 2019 巨量資料叢集功能的支援，包括： <br/> &bull; &nbsp; 管理儀表板中的 [服務端點] 資料表，其中列出叢集中的所有重要服務。 <br/> &bull; &nbsp; 叢集狀態筆記本會示範如何查詢所有服務和 Pod 中的叢集狀態並進行疑難排解。| 
| 已更新可用的語言套件| 延伸模組管理員 Marketplace 中現在提供 10 種語言套件。 簡單地說，使用延伸模組 Marketplace 來搜尋特定語言並進行安裝。 安裝選取的語言之後，Azure Data Studio 將提示您以新語言重新啟動。 |
| SQL Server Profiler 更新 | 已更新 SQL Server Profiler 延伸模組以包含新功能，包括： <br/> &bull; &nbsp; 依資料庫名稱篩選 <br/> &bull; &nbsp; 複製並貼上支援 <br/> &bull; &nbsp; 儲存/載入篩選 <br/>您可以在[此處](https://github.com/microsoft/azuredatastudio/issues?utf8=%E2%9C%93&q=is%3Aissue+is%3Aclosed+milestone%3A%22July+2019+Release%22+label%3A%22Area%3A+SQL+Profiler%22+) \(英文\) 找到 SQL Server Profiler 延伸模組的完整改進清單。  |
| Visual Studio Code 5 月版本合併 1.35 | 您可以在[此處](https://code.visualstudio.com/updates/v1_35) \(英文\) 找到最新的改進。 |
| 已解決的 Bug 和問題 | 在舊版 Azure Data Studio 中，如果在從 [連線] 對話方塊連線時選取了使用者資料庫，則會將產生的物件總管項目範圍完全限定為該單一資料庫。 從此版本開始，該行為已經變更，因此，伺服器層級屬性也會顯示於物件總管中。 <br/> 如需完整的修正清單，請參閱 [GitHub 上的 Bug 和問題](https://github.com/microsoft/azuredatastudio/milestone/35?closed=1) \(英文\)。 |
| &nbsp; | &nbsp; |

## <a name="june-2019"></a>2019 年 6 月

2019 年 6 月 6 日 &nbsp; / &nbsp; 版本：1.8.0

&nbsp;

| 變更 | 詳細資料 |
| :----- | :------ |
| 中央管理伺服器 (CMS) 延伸模組的版本 | 中央管理伺服器會儲存一份 SQL Server 執行個體清單，其會組織為一或多個中央管理伺服器群組。 使用者可以連線到自己現有的 CMS 伺服器，並管理其伺服器，例如新增和移除伺服器。 若要深入了解，請參閱[此處](../relational-databases/administer-multiple-servers-using-central-management-servers.md) |
| 適用於 Windows 的資料庫管理工具延伸模組版本 | 此延伸模組會從 Azure Data Studio 的 SQL Server Management Studio 中啟動兩個最常使用的體驗。 使用者可在許多不同的物件 (例如資料庫、資料表、資料行、檢視等) 上按一下滑鼠右鍵，然後選取 [屬性] 以檢視該物件的 [SSMS 屬性] 對話方塊。 此外，使用者可在資料庫上按一下滑鼠右鍵，然後選取 [產生指令碼] 來啟動知名的 SSMS 產生指令碼精靈。 
| 結構描述比較改進 | &bull; &nbsp; 已新增排除/包含選項 <br/>&bull; &nbsp; [產生指令碼] 會在產生之後開啟指令碼 <br/>&bull; &nbsp; 已移除雙重捲軸  <br/>&bull; &nbsp; 格式設定和配置改進 <br/>&bull; &nbsp; 您可以在[這裡](https://github.com/microsoft/azuredatastudio/issues?q=is%3Aissue+milestone%3A%22June+2019+Release%22+label%3A%22Area%3A+Schema+Compare%22+is%3Aclosed) \(英文\) 找到完整的變更|
| 已將 [訊息] 區段移至自己的索引標籤 | 當使用者執行 SQL 查詢時，結果和訊息均位於堆疊的面板上。 現在，它們都位於一個面板的個別索引標籤中 (例如在 SSMS 中)。 |
| SQL Notebook 改進 | &bull; &nbsp; 使用者現在可以選擇在筆記本中使用自己的 Python 3 或 Anaconda 安裝 <br/>&bull; &nbsp; 多重穩定性 + 調整/完成修正 <br/> &bull; &nbsp; 在[這裡](https://github.com/microsoft/azuredatastudio/issues?q=is%3Aissue+milestone%3A%22June+2019+Release%22+is%3Aclosed+label%3A%22Area%3A+Notebooks%22) \(英文\) 檢視完整的改進清單|
| Visual Studio Code 7 月版本合併 1.34 | 您可以在[此處](https://code.visualstudio.com/updates/v1_34) \(英文\) 找到最新的改進 |
| 已解決的 Bug 和問題。 | 請參閱 [GitHub 上的 Bug 和問題](https://github.com/microsoft/azuredatastudio/milestone/32?closed=1) \(英文\)。 |
| &nbsp; | &nbsp; |

### <a name="known-issues"></a>已知問題

- 適用於 Windows 的資料庫管理工具延伸模組
    - 無法從中斷連線的伺服器節點啟動屬性
    - 無法啟動 Azure 伺服器的屬性
    - 並非所有物件都有屬性對話方塊
    - 對話方塊需要很長的時間才能啟動
    - 啟動具有某些連線類型 (例如 Azure AD) 的伺服器時發生錯誤
- Notebooks
    - [5838](https://github.com/microsoft/azuredatastudio/issues/5838) \(英文\) 允許使用者使用適用於 Notebooks 的系統 Python
- 結構描述比較
    - [5804](https://github.com/microsoft/azuredatastudio/issues/5804) (英文) 結構描述比較工作顯示不會執行任何作業的預設取消操作功能表

## <a name="may-2019"></a>2019 年 5 月

2019 年 5 月 8 日 &nbsp; / &nbsp; 版本：1.7.0 

&nbsp;

| 變更 | 詳細資料 |
| :----- | :------ |
| 結構描述比較延伸模組的版本 | 結構描述比較是 SQL Server Data Tools (SSDT) 中的知名功能，而其主要使用案例是比較資料庫和 .dacpac 檔案之間的差異並將其視覺化，以及執行動作以使其相同。 |
| 已將 [工作] 檢視移至 [輸出] 視窗 | 使用者現在可以在 [輸出] 視窗的 [工作] 檢視中，檢視長時間執行的工作狀態，例如備份、還原和結構描述比較
| 已新增歡迎頁面 | &bull; &nbsp; 常用動作的連結，例如新增查詢、新增檔案、新增筆記本 <br/>&bull; &nbsp; 文件和 GitHub 的連結 |
| SQL Notebook 改進 | &bull; &nbsp; Markdown 轉譯改進，包括對備註和資料表提供更好的支援 <br/>&bull; &nbsp; 工具列的可用性改進 <br/>&bull; &nbsp; 受信任筆記本的 Markdown 連結不再需要 Cmd/Ctrl + 按一下，而可以直接按一下 <br/>&bull; &nbsp; 對於在關閉筆記本之後清除 Jupyter 處理序，以及減少在同時啟動多個筆記本時所發生之錯誤的改進 <br/>&bull; &nbsp; 改進 SQL 筆記本連線，以確保針對相同資料庫執行 2 個筆記本時不會發生錯誤 <br/>&bull; &nbsp; 從工具列中按一下 [執行資料格] 按鈕時，將筆記本自動捲動至目前執行資料格的改進 <br/>&bull; &nbsp; 一般穩定性和效能改進 |
| 已解決的 Bug 和問題。 | 請參閱 [GitHub 上的 Bug 和問題](https://github.com/microsoft/azuredatastudio/milestone/31?closed=1) \(英文\)。 |
| &nbsp; | &nbsp; |

## <a name="april-2019"></a>2019 年 4 月

2019 年 4 月 18 日 &nbsp; / &nbsp; 版本：1.6.0 

&nbsp;

| 變更 | 詳細資料 |
| :----- | :------ |
| 已將 [伺服器] 索引標籤重新命名為 [連線] | |
| 已移動 Azure 資源總管成為 [連線] 底下的 Azure Viewlet | 使用者現在可以透過 [連線] 檢視中的 Azure Viewlet 來檢視其 Azure SQL 執行個體，並展開以檢視每個伺服器或資料庫底下的物件。|
| SQL Notebook 改進 | &bull; &nbsp; 已新增工具列上的按鈕，以清除所有資料格的輸出 <br/>&bull; &nbsp; 已新增工具列上的按鈕，以執行所有資料格 <br/>&bull; &nbsp; 已修正 [附加至] 下拉式清單中的連線名稱，而不是伺服器名稱 (如果已設定) <br/>&bull; &nbsp; 修正在使用相對影像路徑時不會呈現 Markdown 中的影像 <br/>&bull; &nbsp; 透過新增按兩下自動調整資料行大小與改善的滑鼠滾輪支援，以改善筆記本方格中的功能 <br/>&bull; &nbsp; 改進在透過筆記本安裝 Python 時的錯誤處理和 Python 安裝復原 <br/>&bull; &nbsp; 選取筆記本資料格時「全選」功能的改善 <br/>&bull; &nbsp; 改進筆記本連線，以防止關閉筆記本並影響物件總管連線 <br/>&bull; &nbsp; 已改進筆記本體驗，以在筆記本中斷連線並且需要連線來執行資料格時向使用者顯示訊息<br/>&bull;&nbsp; 已改進在重新啟動 ADS 時，於 ADS 中將未儲存筆記本解除凍結的支援 |
| 已解決的 Bug 和問題。 | 請參閱 [GitHub 上的 Bug 和問題](https://github.com/Microsoft/azuredatastudio/milestone/26?closed=1) \(英文\)。 |
| &nbsp; | &nbsp; |

## <a name="march-2019-hotfix"></a>2019 年 3 月 (Hotfix)

2019 年 3 月 22 日 &nbsp; / &nbsp; 版本：1.5.2 &nbsp; / &nbsp; Hotfix 版本

&nbsp;

| 變更 | 詳細資料 |
| :----- | :------ |
| 已修正數個在 1.5.1 中發現的問題。 | 請參閱 [GitHub 上的 3 月 Hotfix 版本](https://github.com/Microsoft/azuredatastudio/milestone/28) \(英文\)。<br/> <br/>&bull; &nbsp; 已修正使用者無法關閉從儀表板 [開啟筆記本] 工作中開啟的筆記本問題 <br/>&bull; &nbsp; 已修正筆記本 JSON 在儲存之後有多餘 } 的問題 <br/>&bull; &nbsp; 已修正筆記本方格不會回應佈景主題變更的問題 <br/>&bull; &nbsp; 已修正完整筆記本路徑會顯示於索引標籤標題中的問題。 現在只會顯示檔案名稱。 |
| &nbsp; | &nbsp; |

## <a name="march-2019"></a>2019 年 3 月

2019 年 3 月 18 日 &nbsp; / &nbsp; 版本：1.5.1

&nbsp;

| 變更 | 詳細資料 |
| :----- | :------ |
| 已新增[適用於 Azure Data Studio 的 PostgreSQL 延伸模組](./extensions/postgres-extension.md) | 支援的功能： <br/>&bull; &nbsp; 連線對話方塊 <br/>&bull; &nbsp; 物件總管 <br/>&bull; &nbsp; 查詢編輯器 <br/>&bull; &nbsp; 圖表 <br/>&bull; &nbsp; 儀表板 <br/>&bull; &nbsp; 程式碼片段 <br/>&bull; &nbsp; 編輯資料 <br/>&bull; &nbsp; Notebooks |
| 已新增 SQL Notebook | 已將 SQL 核心支援新增至內建的 Notebook 檢視器： <br/>&bull; &nbsp; 支援 T-SQL <br/>&bull; &nbsp; 支援 PGSQL |
| 已新增 PowerShell 延伸模組 | 從 VS Code 帶來 [PowerShell 延伸模組](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell) \(英文\) 體驗。  |
| 已新增 SQL Server dacpac 延伸模組  | 將資料層應用程式精靈從 SQL Server 匯入延伸模組移除至新的延伸模組。  |
| 已新增社群延伸模組 QueryPlan.show | 新增整合支援以將查詢計劃視覺化  |
| 已更新 SQL Server 2019 Preview 延伸模組 | &bull; &nbsp; 已將 Jupyter Notebook 支援 (特別是 Python3 和 Spark 核心) 移至核心 Azure Data Studio 工具。 <br/>&bull; &nbsp; 對外部資料精靈的錯誤 (Bug) 修正 |
| 已解決的 Bug 和問題。 | 請參閱 [GitHub 上的 Bug 和問題](https://github.com/Microsoft/azuredatastudio/milestone/25?closed=1) \(英文\)。 |
| &nbsp; | &nbsp; |

### <a name="known-issues"></a>已知問題
- [#4427](https://github.com/Microsoft/azuredatastudio/issues/4427) \(英文\)：在核心準備好進行 Spark 之前，於資料格上按一下執行會產生重大錯誤 **因應措施：** 等到核心載入之後，再執行任何資料格
- [#4493](https://github.com/Microsoft/azuredatastudio/issues/4493) \(英文\)：已使用 SQL 驗證從 SSMS 中啟動 ADS - 提示使用者提供密碼 **因應措施：** 立即使用 Windows 驗證。 
- [#4494](https://github.com/Microsoft/azuredatastudio/issues/4494) \(英文\)：無法安裝 SQL 筆記本功能 <br/>
**因應措施：** 遵循 [此處](https://github.com/Microsoft/azuredatastudio/issues/4494#issuecomment-473043832) \(英文\) 的因應措施步驟。 
- [#4503](https://github.com/Microsoft/azuredatastudio/issues/4503) \(英文\)：無法直接從下載資料夾開啟 Azure Data Studio (Mac) <br />
**因應措施：** 解壓縮應用程式之後將電腦重新啟動。 將會進行調查。 
- [#4539](https://github.com/Microsoft/azuredatastudio/issues/4539) \(英文\)：Notebook 另存新檔會遺失連線內容 <br />
**因應措施：** 將在下一版中修正。 
- [#4458](https://github.com/Microsoft/azuredatastudio/issues/4458) \(英文\)：如果使用了不正確的版本，Dacpac 解壓縮就會損毀 SqlToolsService <br/>
**因應措施：** 重新啟動 Azure Data Studio，並確定使用的是正確的版本。
- [新增筆記本] 和 [開啟筆記本] 圖示均遺失 <br/>
**因應措施：** 舊版連線類型已被取代。 建議連線到 SQL Server 端點，您就可以如預期般取得所有動作 (新增筆記本、Spark 作業)。 

## <a name="february-2019"></a>2019 年 2 月

2019 年 2 月 13 日 &nbsp; / &nbsp; 版本：1.4.5

&nbsp;

| 變更 | 詳細資料 |
| :----- | :------ |
| 已新增 **適用於 SQL Server 延伸模組套件的管理元件**。 | 這可讓您更輕鬆地安裝 SQL Server 管理相關的延伸模組。 這包括：<br/>&bull; &nbsp; [SQL Server Agent](./extensions/sql-server-agent-extension.md)<br/>&bull; &nbsp; [SQL Server Profiler](./extensions/sql-server-profiler-extension.md)<br/>&bull; &nbsp; [SQL Server 匯入](./extensions/sql-server-import-extension.md) |
| 已在 Profiler 延伸模組中新增篩選擴充事件支援。 | &nbsp; |
| 已新增「另存檔案為 XML」功能，可將 T-SQL 結果儲存為 XML。 | &nbsp; |
| 已新增資料層應用程式精靈改進。 | &bull; &nbsp; 已新增 [產生指令碼] 按鈕<br/>&bull; &nbsp; 已新增檢視來提供部署期間可能遺失資料的警告。 |
| 更新至 SQL Server 2019 Preview 延伸模組。 | 請參閱[資料虛擬化延伸模組](./extensions/data-virtualization-extension.md)。 |
| 預設已針對長時間執行的查詢啟用結果串流。 | &nbsp; |
| 已解決的 Bug 和問題。 | 請參閱 [GitHub 上的 Bug 和問題](https://github.com/Microsoft/azuredatastudio/milestone/23?closed=1) \(英文\)。 |
| &nbsp; | &nbsp; |

## <a name="january-2019-hotfix"></a>2019 年 1 月 (Hotfix)

2019 年 1 月 16 日 &nbsp; / &nbsp; 版本：1.3.9 &nbsp; / &nbsp; Hotfix 版本

&nbsp;

| 變更 | 詳細資料 |
| :----- | :------ |
| 已修正數個在 1.3.8 中發現的問題。 | 請參閱 [GitHub 上的 1 月 Hotfix 版本](https://github.com/Microsoft/azuredatastudio/milestone/24?closed=1) \(英文\)。<br/><br/>如需詳細資訊，請參閱：<br/>&bull; &nbsp; [GitHub 上的變更記錄檔](https://github.com/Microsoft/azuredatastudio/blob/main/CHANGELOG.md) \(英文\)。<br/>&bull; &nbsp; [GitHub 上的版本](https://github.com/Microsoft/azuredatastudio/releases) \(英文\)。 |
| &nbsp; | &nbsp; |

## <a name="january-2019"></a>2019 年 1 月

2019 年 1 月 9 日 &nbsp; / &nbsp; 版本：1.3.8

&nbsp;

| 變更 | 詳細資料 |
| :----- | :------ |
| 已針對 Windows 新增使用者安裝程式。 | 不同於現有的系統安裝程式，新的使用者安裝程式不需要系統管理員權限。 這也能為非系統管理員提供更輕鬆的升級體驗。 |
| 已新增 Azure Active Directory 驗證支援。 | &nbsp; |
| 宣佈 Idera SQL DM Performance Insights (預覽)。 | &nbsp; |
| SQL Server 匯入延伸模組中的資料層應用程式精靈支援。 | &nbsp; |
| 更新至 SQL Server 2019 Preview 延伸模組。 | 請參閱[資料虛擬化延伸模組](./extensions/data-virtualization-extension.md)。 |
| SQL Server Profiler 改進。 | &nbsp; |
| 大型查詢的結果串流 (預覽)。 | &nbsp; |
| 社群延伸模組：sp_executesql 至 sql 和「新增資料庫」。 | &nbsp; |
| 已解決的 Bug 和問題。 | 請參閱 [GitHub 上的 Bug 和問題](https://github.com/Microsoft/azuredatastudio/milestone/19?closed=1) \(英文\)。 |
| &nbsp; | &nbsp; |

## <a name="november-2018"></a>2018 年 11 月

2018 年 11 月 6 日 &nbsp; / &nbsp; 版本：1.2.4

&nbsp;

| 變更 | 詳細資料 |
| :----- | :------ |
| 更新至 SQL Server 2019 Preview 延伸模組。 | 請參閱[資料虛擬化延伸模組](./extensions/data-virtualization-extension.md)。 |
| 引進「貼上計劃」延伸模組。 | &nbsp; |
| 引進「高彩」查詢延伸模組，包括 SSMS 編輯器佈景主題。 | &nbsp; |
| SQL Server Agent、Profiler 和「匯入」延伸模組中的修正。 | &nbsp; |
| 修正 .Net Core Socket KeepAlive 問題，此問題會導致 macOS 上的非使用中連線中斷。 | &nbsp; |
| 將 SQL Tools 服務升級至 .Net Core 2.2 Preview 3 (適用於最終 Azure AD 支援)。 | &nbsp; |
| &nbsp; | &nbsp; |

### <a name="bug-fixes-november-2018"></a>Bug 修正，2018 年 11 月

- 修正[問題 #2933](https://github.com/Microsoft/azuredatastudio/issues/2933) \(英文\)：與 Azure SQL Database 的連線中斷
- 修正[問題 #2914](https://github.com/Microsoft/azuredatastudio/issues/2914) \(英文\)：擴充 OE 資料庫節點時發生「無效引數」例外狀況
- 修正[問題 #2935](https://github.com/Microsoft/azuredatastudio/pull/2935) \(英文\)：在查詢結果中正確顯示多行訊息
- 修正[問題 #2906](https://github.com/Microsoft/azuredatastudio/pull/2906) \(英文\)：修正在資料表名稱包含特殊字元時編輯資料檔案名稱
- 修正[問題 #2929](https://github.com/Microsoft/azuredatastudio/issues/2929) \(英文\)：內建延伸模組變更記錄指出要檢查 VSCode 版本資訊的變更
- 修正[問題 #2719](https://github.com/Microsoft/azuredatastudio/issues/2719) \(英文\)：高對比佈景主題雙重/三重圖示
- 修正[問題 #3047](https://github.com/Microsoft/azuredatastudio/pull/3047) \(英文\)：新增用來連線到 SQL Server 的命令列介面
- 修正[問題 #3031](https://github.com/Microsoft/azuredatastudio/pull/3031) \(英文\)：新增查詢計劃佈景主題支援

## <a name="october-2018"></a>2018 年 10 月

2018 年 10 月 29 日 &nbsp; / &nbsp; 版本：1.1.4

&nbsp;

| 變更 | 詳細資料 |
| :----- | :------ |
| 引進 Azure 資源總管來瀏覽 Azure SQL Database。 | &nbsp; |
| 改進物件總管和查詢編輯器的連線穩定性。 | &nbsp; |
| SQL Agent 延伸模組改進。 | &nbsp; |
| 更新至 SQL Server 2019 Preview 延伸模組。 | 請參閱[資料虛擬化延伸模組](./extensions/data-virtualization-extension.md)。 |
| &nbsp; | &nbsp; |

### <a name="bug-fixes-october-2018"></a>Bug 修正，2018 年 10 月

- 修正[問題 #2717](https://github.com/Microsoft/azuredatastudio/issues/2717) \(英文\)：按一下 XML 資料行結果的格式設定
- 修正[問題 #2993](https://github.com/Microsoft/azuredatastudio/issues/2993) \(英文\)：寬度的結果視窗不完整
- 修正[問題 #2999](https://github.com/Microsoft/azuredatastudio/issues/2999) \(英文\)：連線到 DB 時，無法在 Mac 上載入 System.Diagnostics.Tracing 檔案
- 修正[問題 #2851](https://github.com/Microsoft/azuredatastudio/issues/2851) \(英文\)：時間序列圖未正確呈現
- 修正[問題 #2996](https://github.com/Microsoft/azuredatastudio/issues/2996) \(英文\)：暫存資料表因為突然發生的工作階段變更而遺失

如需詳細資訊，請參閱[變更記錄檔](https://github.com/Microsoft/azuredatastudio/blob/main/CHANGELOG.md) \(英文\) 和[版本](https://github.com/Microsoft/azuredatastudio/releases) \(英文\)。

## <a name="september-2018-ga-release"></a>2018 年 9 月 (GA 版本)

2018 年 9 月 24 日 &nbsp; / &nbsp; 版本：1.0 &nbsp; / &nbsp; GA 版本

Azure Data Studio 的正式發行版本 (先前稱為 SQL Operations Studio)。

&nbsp;

| 變更 | 詳細資料 |
| :----- | :------ |
| 適用於大量結果集的查詢結果方格效能與 UX 改進。 | &nbsp; |
| 使用方格版面配置和改進的設定編輯器 (預覽)，將 Visual Studio Code 原始程式碼從 1.23 重新整理為1.26.1。 | &nbsp; |
| 對於螢幕助讀程式、鍵盤導覽和高對比的協助工具改進。 | &nbsp; |
| 已新增 `Connection name` 選項，可在 [伺服器] Viewlet 中提供替代的顯示名稱。 | &nbsp; |
| &nbsp; | &nbsp; |

### <a name="announcing-the-sql-server-2019-preview-extension"></a>宣布 SQL Server 2019 Preview 延伸模組

&nbsp;

| 變更 | 詳細資料 |
| :----- | :------ |
| 支援 SQL Server 2019 預覽功能，包括[巨量資料叢集](../big-data-cluster/big-data-cluster-overview.md)支援。 | 連線到 SQL Server 2019 Preview 隨附的 HDFS/Spark 閘道。<br/><br/>瀏覽 HDFS、上傳檔案、儲存檔案，以及啟動實用的動作，例如，在 Notebook 中分析 CSV 檔案。<br/><br/>從儀表板提交 Spark 作業，或以滑鼠右鍵按一下 [物件總管] 中的 HDFS/Spark 連線。 |
| Azure Data Studio Notebooks。 | 使用整合式 Notebook 檢視器建立或開啟 Notebook。 在此版本中，Notebook 檢視器僅支援連線到本機核心與 SQL Server 2019 巨量資料叢集。<br/><br/>在您的 Notebook 中使用 PROSE 程式碼加速器程式庫來了解檔案格式和資料類型，以便快速進行資料準備。 |
| Azure 資源總管。 | [Azure 資源總管] 檢視可讓您瀏覽 Azure 帳戶的資料相關端點，並在 [物件總管] 中建立與它們的連線。 在此版本中，支援 Azure SQL Database。 |
| SQL Server PolyBase 的建立外部資料表精靈。 | 使用簡單易用的精靈，來建立外部資料表及其支援的中繼資料結構。 在此版本中，支援遠端 SQL Server 和 Oracle 伺服器。 |
| &nbsp; | &nbsp; |

### <a name="bug-fixes-september-2018"></a>Bug 修正，2018 年 9 月

- 修正[問題 #2647](https://github.com/Microsoft/azuredatastudio/issues/143) \(英文\)：圖表向後退了一大步。
- 修正[問題 #2648](https://github.com/Microsoft/azuredatastudio/issues/143) \(英文\)：SELECT 會傳回整個資料行的 JSON 超連結。

如需詳細資訊，請參閱[變更記錄檔](https://github.com/Microsoft/azuredatastudio/blob/main/CHANGELOG.md) \(英文\) 和[版本](https://github.com/Microsoft/azuredatastudio/releases) \(英文\)。

## <a name="august-2018"></a>2018 年 8 月

2018 年 8 月 30 日 &nbsp; / &nbsp; 版本：0.32.8 &nbsp; / &nbsp; 公開預覽

「8 月公開預覽」著重於 Bug 修正、產品穩定性，以及填補現有案例中的差距。

0\.32.8 包含對於數個在 0.32.7 ([#1971](https://github.com/Microsoft/azuredatastudio/issues/1971) \(英文\)、[#2372](https://github.com/Microsoft/azuredatastudio/issues/2372) \(英文\)) 中找到之迴歸的修正

&nbsp;

| 變更 | 詳細資料 |
| :----- | :------ |
| 宣布 SQL Server 匯入延伸模組。 | &nbsp; |
| SQL Server Profiler 工作階段管理。 | &nbsp; |
| SQL Server Profiler 工作階段範本支援。 | &nbsp; |
| SQL Server Agent 改進。 | &nbsp; |
| 新的社群延伸模組：第一個回應程式套件。 | &nbsp; |
| 生活品質改進：連接字串 | &nbsp; |
| &nbsp; | &nbsp; |

### <a name="bug-fixes-august-2018"></a>Bug 修正，2018 年 8 月

- 使用 `Parse Syntax` 命令，在 [查詢編輯器] 視窗中剖析 SQL。
- 修正[問題 #143](https://github.com/Microsoft/azuredatastudio/issues/143) \(英文\)：按兩下不會選取變數名稱中的 @。
- 修正[問題 #387](https://github.com/Microsoft/azuredatastudio/issues/387) \(英文\)：SQL 索引標籤 DB 圖示為紅色。
- 修正[問題 #825](https://github.com/Microsoft/azuredatastudio/issues/825) \(英文\)：要求：在「編寫指令碼為」之後自動連線到目前的伺服器 
- 修正[問題 #1278](https://github.com/Microsoft/azuredatastudio/issues/1278) \(英文\)：sqlops.desktop [桌面項目] - 對於名稱與註解的多餘值。
- 修正[問題 #1285](https://github.com/Microsoft/azuredatastudio/issues/1285) \(英文\)：更新會導致在 Windows 中移除/取代應用程式圖示。
- 修正[問題 #1317](https://github.com/Microsoft/azuredatastudio/issues/1317) \(英文\)：修正十進位分隔符號。
- 修正[問題 #1474](https://github.com/Microsoft/azuredatastudio/issues/1474) \(英文\)：取消變更連線會中斷目前連線的連線。
- 修正[問題 #1497](https://github.com/Microsoft/azuredatastudio/issues/1497) \(英文\)：[以圖表檢視] 選項會在底部截斷。
- 修正[問題 #1524](https://github.com/Microsoft/azuredatastudio/issues/1524) \(英文\)：殼層/儀表板：主要的 Viewlet 圖示是可拖曳的，而且可能會損毀應用程式。
- 修正[問題 #1578](https://github.com/Microsoft/azuredatastudio/issues/1578) \(英文\)：無法透過按一下名稱來展開/摺疊遠端檔案瀏覽器資料夾。
- 修正[問題 #1620](https://github.com/Microsoft/azuredatastudio/issues/1620) \(英文\)：功能建議：取得現有連線的連接字串。
- 修正[問題 #1624](https://github.com/Microsoft/azuredatastudio/issues/1624) \(英文\)：SelectBox 不會在停用時變更色彩。
- 修正[問題 #1728](https://github.com/Microsoft/azuredatastudio/issues/1728) \(英文\)：[另存檔案為 JSON/EXCEL/CSV] 無法運作。
- 修正[問題 #1744](https://github.com/Microsoft/azuredatastudio/issues/1744) \(英文\)：在索引標籤之間切換時，[結果] 窗格會遺失其捲動位置。
- 修正[問題 #1748](https://github.com/Microsoft/azuredatastudio/issues/1748) \(英文\)：第二次 (及後續) 儲存 Excel 檔案時的錯誤訊息。
- 修正[問題 #1782](https://github.com/Microsoft/azuredatastudio/issues/1782) \(英文\)：編輯資料：資料格不會在按 Escape 鍵時還原為原始值。
- 修正[問題 #1836](https://github.com/Microsoft/azuredatastudio/issues/1836) \(英文\)：.sql 檔案不會與 SQL Operations Studio 產生關聯。
- 修正[問題 #1850](https://github.com/Microsoft/azuredatastudio/issues/1850) \(英文\)：輸入 N'' 會自動校正為 N'''。
- 修正[問題 #1985](https://github.com/Microsoft/azuredatastudio/issues/1985) \(英文\)：從查詢結果方格中進行的複製少了 1 個資料行。
- 修正[問題 #1998](https://github.com/Microsoft/azuredatastudio/pull/1998) \(英文\)：將 VS Code 版本新增至 [關於] 對話方塊。
- 修正[問題 #2042](https://github.com/Microsoft/azuredatastudio/pull/2042) \(英文\)：代理人員：已啟用按鈕，可從 sql 檔案匯入查詢。
- 修正[問題 #2091](https://github.com/Microsoft/azuredatastudio/issues/2091) \(英文\)：無法使用 Ctrl+C 快速鍵來從結果窗格進行複製。
- 修正[問題 #2099](https://github.com/Microsoft/azuredatastudio/pull/2099) \(英文\)：已新增更多 saveAsCsv 選項。
- 修正[問題 #2107](https://github.com/Microsoft/azuredatastudio/issues/2107) \(英文\)：更新適用於儀表板和 Profiler 文件的文件圖示。
- 修正[問題 #2129](https://github.com/Microsoft/azuredatastudio/pull/2129) \(英文\)：切換索引標籤時，儲存編輯資料捲軸位置。
- 修正[問題 #2152](https://github.com/Microsoft/azuredatastudio/issues/2152) \(英文\)：結果方格資料列指標以零為基底。

### <a name="known-issues-august-2018"></a>已知問題，2018 年 8 月

- [問題 #2371](https://github.com/Microsoft/azuredatastudio/issues/2371) \(英文\)：另存檔案為 Excel 只會儲存第一個資料列
- [問題 #2150](https://github.com/Microsoft/azuredatastudio/issues/2150) \(英文\)：無法在 Ubuntu 16.04 上連線到容器中的 SQL

## <a name="july-2018"></a>2018 年 7 月

2018 年 7 月 19 日 &nbsp; / &nbsp; 版本：0.31.4 &nbsp; / &nbsp; 公開預覽

「7月公開預覽」著重於下列項目：

- SQL Server Agent 設定案例的初始版本。
- SQL Server Profiler 工作階段和檢視範本增強功能。
- 持續修正客戶所回報之 GitHub 問題的 Bug。

&nbsp;

| 變更 | 詳細資料 |
| :----- | :------ |
| [適用於 SQL Operations Studio 的 SQL Server Agent 延伸模組](./extensions/sql-server-agent-extension.md)改進。 | 已在左窗格中新增警示、操作員，以及 Proxy 和圖示的檢視。<br/><br/>已新增 [新增作業]、[新增作業步驟]、[新增警示] 和 [新增操作員] 的對話方塊。<br/><br/>已新增 [刪除作業]、[刪除警示] 和 [刪除操作員] (按一下滑鼠右鍵)。<br/><br/>已新增 [先前的執行] 視覺效果。<br/><br/>已針對每個資料行名稱新增篩選。 |
| [適用於 SQL Operations Studio 的 SQL Server Profiler延伸模組](./extensions/sql-server-profiler-extension.md)改進。 | 已新增 5 個預設範本來檢視擴充事件。<br/><br/>已新增伺服器/資料庫連線名稱。<br/><br/>已新增對 Azure SQL Database 執行個體的支援。<br/><br/>已新增當索引標籤於 Profiler 仍在執行期間關閉時結束 Profiler 的建議。 |
| 合併指令碼延伸模組的版本。 | &nbsp; |
| 已針對延伸模組作者新增的精靈和對話方塊擴充點。 | &nbsp; |
| &nbsp; | &nbsp; |

### <a name="bug-fixes-july-2018"></a>Bug 修正，2018 年 7 月

- 修正[問題 728](https://github.com/Microsoft/azuredatastudio/issues/728) \(英文\)：對於 macOS 上的 [新增連線] 沒有任何回應
- 修正[問題 1612](https://github.com/Microsoft/azuredatastudio/issues/1612) \(英文\)：結果方格文字顯示被國際字元弄亂了
- 修正[問題 1693](https://github.com/Microsoft/azuredatastudio/issues/1693) \(英文\)：[備份] 對話方塊：檔案瀏覽器 UI 中斷
- 修正[問題 1713](https://github.com/Microsoft/azuredatastudio/issues/1713) \(英文\)：受影響的資料列數目
- 修正[問題 1718](https://github.com/Microsoft/azuredatastudio/issues/1718) \(英文\)：無法連線到任何資料來源
- 修正[問題 1719](https://github.com/Microsoft/azuredatastudio/issues/1719) \(英文\)：連線到伺服器時的 TypeError
- 修正[問題 1724](https://github.com/Microsoft/azuredatastudio/issues/1724) \(英文\)：延伸模組對話方塊已停止運作
- 修正[問題 1749](https://github.com/Microsoft/azuredatastudio/issues/1749) \(英文\)：BUG：資料行中的 HTML 資料會被解譯
- 修正[問題 1789](https://github.com/Microsoft/azuredatastudio/issues/1789) \(英文\)：擴充性：如果您新增連線提供者，解除安裝 將永遠不會從清單中移除它
- 修正[問題 1791](https://github.com/Microsoft/azuredatastudio/issues/1791) \(英文\)：Sqlops 延伸模組：queryeditor.connect() 會連線到目標資料庫，但 UI 不會顯示編輯器已連線
- 修正[問題 1799](https://github.com/Microsoft/azuredatastudio/issues/1799) \(英文\)：前 10 個資料庫大小圖表無法在區分大小寫的執行個體上運作
- 修正[問題 1814](https://github.com/Microsoft/azuredatastudio/issues/1814) \(英文\)：sqlops.d.ts 錯字會導致隱含的 'any' 類型定義
- 修正[問題 1817](https://github.com/Microsoft/azuredatastudio/issues/1817) \(英文\)：錯誤 de Ortografia
- 修正[問題 1830](https://github.com/Microsoft/azuredatastudio/issues/1830) \(英文\)：呼叫 component () 之後，在 ButtonComponent 中設定 iconPath 不會變更圖示
- 修正[問題 1843](https://github.com/Microsoft/azuredatastudio/issues/1843) \(英文\)：更好的資料表組織

## <a name="june-2018"></a>2018 年 6 月

2018 年 6 月 20 日 &nbsp; / &nbsp; 版本：0.30.6 &nbsp; / &nbsp; 公開預覽

&nbsp;

| 變更 | 詳細資料 |
| :----- | :------ |
| **適用於 SQL Operations Studio _Preview_ 的 SQL Server Profiler** 延伸模組初始版本。 | &nbsp; |
| 新的 **SQL 資料倉儲** 延伸模組包含豐富的可自訂儀表板 Widget，可呈現您資料倉儲的深入解析。 | 這會將管理和調整您資料倉儲的重要案例解除鎖定，以確保它已針對一致性效能進行最佳化。 |
| **編輯資料「篩選和排序」** 支援。 | &nbsp; |
| **適用於 SQL Operations Studio _Preview_ 的 SQL Server Agent** 延伸模組增強功能，可用於 [作業] 和 [作業記錄] 檢視。 | &nbsp; |
| 已改進 **精靈與對話方塊 UI 產生器架構** 擴充性 API。 | &nbsp; |
| 更新 VS Code 平台原始程式碼。 | 已整合下列版本：<br/>&bull; &nbsp; [2018 年 3 月 (1.22)](https://code.visualstudio.com/updates/v1_22) \(英文\)<br/>&bull; &nbsp; [2018 年 4 月 (1.23)](https://code.visualstudio.com/updates/v1_23) \(英文\) |
| &nbsp; | &nbsp; |

### <a name="github-issues-fixes-june-2018"></a>GitHub 問題修正，2018 年 6 月

- 功能要求 ([問題 1204](https://github.com/Microsoft/azuredatastudio/issues/1204) \(英文\))：讓結果方格自動調整資料行寬度以符合資料，並在重新執行相同查詢時記住手動變更。
- 修正[問題 1398](https://github.com/Microsoft/azuredatastudio/issues/1398) \(英文\)：當連結的帳戶是空的時，應該顯示 [新增訊息] 和 [新增帳戶] 按鈕。
- 修正[問題 1399](https://github.com/Microsoft/azuredatastudio/issues/1399) \(英文\)：摺疊檢視時，[連結帳戶] 索引標籤會中斷。
- 修正[問題 1374](https://github.com/Microsoft/azuredatastudio/issues/1374) \(英文\)：從磁碟開啟 .sql 檔案時，SQL Tools 服務損毀。
- 修正[問題 1372](https://github.com/Microsoft/azuredatastudio/issues/1372) \(英文\)：遺漏 SQL 關鍵字 "BETWEEN"。
- 修正[問題 1395](https://github.com/Microsoft/azuredatastudio/issues/1395) \(英文\)：'MATCH' 關鍵字讓 SQL Tools 服務損毀。
- 修正[問題 1496](https://github.com/Microsoft/azuredatastudio/issues/1496) \(英文\)：[物件總管] 中的 [新增 Profiler] 捷徑功能表選項不會執行任何作業。
- 修正[問題 1495](https://github.com/Microsoft/azuredatastudio/issues/1495) \(英文\)：查詢編輯器「說明」查詢計劃已中斷。

## <a name="may-2018"></a>2018 年 5 月

2018 年 5 月 7 日 &nbsp; / &nbsp; 版本：0.29.3 &nbsp; / &nbsp; 公開預覽

「5 月公開預覽」著重於穩定性和 Bug 修正。

&nbsp;

| 變更 | 詳細資料 |
| :----- | :------ |
| 宣布在延伸模組管理員中提供 Redgate SQL 搜尋延伸模組。 | &nbsp; |
| 提供 10 種語言的「社群當地語系化」。 | 德文、西班牙文、法文、義大利文、日文、韓文、葡萄牙文、俄文、簡體中文和繁體中文。 |
| 遙測收集變更。 | &bull; &nbsp; 已縮減遙測收集。<br/>&bull; &nbsp; 已改進退出體驗。<br/>&bull; &nbsp; 隱私權聲明的產品內連結。 |
| 延伸模組管理員已改進 Marketplace 體驗。 | 更輕鬆地探索社群延伸模組。 |
| SQL Agent 延伸模組。 | &bull; &nbsp; 作業。<br/>&bull; &nbsp; 作業歷程記錄檢視改進。 |
| Whoisactive 和伺服器報表延伸模組的更新。 | &nbsp; |
| 已改進管理儀表板屬性的捲動。 | &nbsp; |
| &nbsp; | &nbsp; |

### <a name="fix-github-issues"></a>修正 GitHub 問題

- 修正[問題 703](https://github.com/Microsoft/azuredatastudio/issues/703) \(英文\)：在 [編輯資料] 中輸入類似 HTML 的文字，會導致值在重新整理之前無法正確顯示
- 修正[問題 821](https://github.com/Microsoft/azuredatastudio/issues/821)：azuredatastudio.deb 套件相依性
- 修正[問題 1260](https://github.com/Microsoft/azuredatastudio/issues/1260) \(英文\)：關鍵字 'distinct' 並未反白顯示
- 修正[問題 1332](https://github.com/Microsoft/azuredatastudio/issues/1332) \(英文\)：編輯資料還原資料列無法運作
- 修正[問題 1215](https://github.com/Microsoft/azuredatastudio/issues/1215) \(英文\)：SQL Agent 延伸模組和狀態列
- 修正[問題 1316](https://github.com/Microsoft/azuredatastudio/issues/1316) \(英文\)：SQL Agent 在變更視窗大小之後不會調整大小

## <a name="april-2018"></a>2018 年 4 月

2018 年 4 月 25 日 &nbsp; / &nbsp; 版本：0.28.6 &nbsp; / &nbsp; 公開預覽

「4 月公開預覽」包含 Bug 修正和改進。

&nbsp;

| 變更 | 詳細資料 |
| :----- | :------ |
| SQL Agent Preview 延伸模組的改進： | &nbsp; |
| &nbsp; &nbsp; &nbsp; 已改進對檔案的支援。 | &bull; &nbsp; 大型檔案。<br/>&bull; &nbsp; 受保護的檔案，用於儲存受到管理員保護的檔案。<br/>&bull; &nbsp; 在 SQL Operations Studio 中儲存 \>256M 的檔案。 |
| &nbsp; &nbsp; &nbsp; 整合式終端機分割。 | 同時使用多個開啟的終端機。 |
| &nbsp; &nbsp; &nbsp; 更快速的安裝和啟動時間。 | 減少磁碟上檔案計數磁碟使用量的安裝。 |
| &nbsp; | &nbsp; |

### <a name="fix-github-issues-april-2018"></a>修正 GitHub 問題，2018 年 4 月

- 修正[問題 37](https://github.com/Microsoft/azuredatastudio/issues/37) \(英文\)：當圖表檢視器擲回錯誤時，會發生未預期的行為。
- 修正[問題 462](https://github.com/Microsoft/azuredatastudio/issues/462) \(英文\)：功能要求：預設要展開伺服器群組的選項。
- 修正[問題 606](https://github.com/Microsoft/azuredatastudio/issues/606) \(英文\)：intellisense - 對於 'update' 命令的錯誤建議。
- 修正[問題 967](https://github.com/Microsoft/azuredatastudio/issues/967) \(英文\)：在結果方格中選取 XML 執行程序表時，預期會有查詢計劃。
- 修正[問題 1023](https://github.com/Microsoft/azuredatastudio/issues/1023) \(英文\)：針對來自 flyfishingdba 的 ms_foreachdb 呼叫新增方括弧。
- 修正[問題 1048](https://github.com/Microsoft/azuredatastudio/issues/1048) \(英文\)：登入前的 SSL/TLS 交握錯誤。
- 修正[問題 1050](https://github.com/Microsoft/azuredatastudio/issues/1050) \(英文\)：顯示錯誤之前，先清除 [深入解析] 檢視。
- 修正[問題 1057](https://github.com/Microsoft/azuredatastudio/issues/1057) \(英文\)：在 explorer-widget 中還原和新增查詢動作均已中斷。
- 修正[問題 1068](https://github.com/Microsoft/azuredatastudio/issues/1068) \(英文\)：[儀表板輸出] 視窗快顯，其中包含 Azure SQL Database 的錯誤訊息。
- 修正[問題 1069](https://github.com/Microsoft/azuredatastudio/issues/1069) \(英文\)：[連線] 對話方塊在一開始顯示時顯示了「伺服器是必要項」錯誤。
- 修正[問題 1070](https://github.com/Microsoft/azuredatastudio/issues/1070) \(英文\)：伺服器群組現在需要按兩下以展開。
- 修正[問題 1072](https://github.com/Microsoft/azuredatastudio/issues/1072) \(英文\)：選取控制項背景為半透明。
- 修正[問題 1115](https://github.com/Microsoft/azuredatastudio/issues/1115) \(英文\)：修正 SQL Operations Studio 中所有高對比的協助工具問題。
- 修正[問題 1101](https://github.com/Microsoft/azuredatastudio/issues/1101) \(英文\)：延伸模組無法升級；[手動下載] 連結會移至錯誤的位置。
- 修正[問題 1103](https://github.com/Microsoft/azuredatastudio/issues/1103) \(英文\)：垂直捲動無法在常用索引標籤上運作。
- 修正[問題 1104](https://github.com/Microsoft/azuredatastudio/issues/1104) \(英文\)：SQL 延伸模組索引標籤已停止運作。

### <a name="visual-studio-code-121-platform"></a>Visual Studio Code 1.21 平台

「4月公開預覽」著重於重新整理 Visual Studio Code 1.21 平台的原始程式碼。 這會將數個更新從先前的 1.19 同步處理點帶入核心編輯器和工作台。 部分範例如下：

&nbsp;

| 變更 | 詳細資料 |
| :----- | :------ |
| [新的通知 UI](https://code.visualstudio.com/updates/v1_21#_new-notifications-ui) \(英文\)。 | 輕鬆管理和檢閱 SQL Operations Studio 通知。 |
| [整合式終端機分割](https://code.visualstudio.com/updates/v1_21#_split-terminals) \(英文\)。 | 一次使用多個開啟的終端機。 |
| [儲存大型且受保護的檔案](https://code.visualstudio.com/updates/v1_20#_save-files-that-need-admin-privileges) \(英文\)。 | 在 SQL Operations Studio 中儲存受到管理員保護和 \>256M 的檔案。 |
| [已改進大型檔案支援](https://code.visualstudio.com/updates/v1_21#_text-buffer-improvements) \(英文\)。 | 大型檔案的文字緩衝區最佳化。 |
| [已改進設定搜尋](https://code.visualstudio.com/updates/v1_20#_settings-search) \(英文\)。 | 使用自然語言搜尋輕鬆地尋找正確的設定。 |
| [全域程式碼片段](https://code.visualstudio.com/updates/v1_20#_global-snippets) \(英文\)。 | 建立可跨所有檔案類型使用的程式碼片段。 |
| [Explorer 中的多個選取項目](https://code.visualstudio.com/updates/v1_20#_multi-select-in-the-explorer) \(英文\)。 | 一次對多個檔案執行動作。 |
| [Explorer 中的錯誤和警告](https://code.visualstudio.com/updates/v1_20#_error-indicators-in-the-explorer) \(英文\)。 | 快速導覽至程式碼基底中的錯誤。 |
| [在視窗之間拖放、複製並貼上](https://code.visualstudio.com/updates/v1_21#_better-drag-and-drop-support) \(英文\)。 | 在開啟的 SQL Operations Studio 視窗之間移動檔案。 |
| [Git 子模組支援](https://code.visualstudio.com/updates/v1_20#_git-submodules) \(英文\)。 | 在巢狀 Git 存放庫上執行 Git 作業。 |
| [終端機螢幕助讀程式支援](https://code.visualstudio.com/updates/v1_20#_screen-reader-support) \(英文\)。 | 整合式終端機現在具有 **已將螢幕助讀程式最佳化** 模式。 |
| [中央編輯器版面配置](https://code.visualstudio.com/updates/v1_21#_centered-editor-layout) \(英文\)。 | 將您的程式碼檢視畫面最大化。 |
| [水平搜尋結果 (預覽)](https://code.visualstudio.com/updates/v1_21#_horizontal-search) \(英文\)。 | 您現在可以在水平面板中檢視搜尋結果。 |
| &nbsp; | &nbsp; |

如需其他詳細資料，請參閱 [Visual Studio Code 2 月版本資訊](https://code.visualstudio.com/updates/v1_21) 及 [Visual Studio Code 1 月版本資訊](https://code.visualstudio.com/updates/v1_20)。

如需詳細資訊，請參閱[變更記錄檔](https://github.com/Microsoft/azuredatastudio/blob/main/CHANGELOG.md) \(英文\)。

## <a name="march-2018"></a>2018 年 3 月

2018 年 3 月 28 日 &nbsp; / &nbsp; 版本：0.27.3 &nbsp; / &nbsp; 公開預覽

「3月公開預覽」會繼續處理最重要的 GitHub 問題，並著重於改進我們的擴充性案例。 特別是啟用延伸模組管理員、改進儀表板管理，以及提供 SQL Agent 和深入解析延伸模組。 此版本包括下列增強功能：

&nbsp;

| 變更 | 詳細資料 |
| :----- | :------ |
| 增強儀表板擴充性模型，以支援索引標籤式的深入解析和設定窗格。 | 延伸模組管理員可讓您輕鬆取得延伸模組。<br/><br/>來自 [whoisactive.com](http://www.whoisactive.com) 之 sp\_whoisactive 的儀表板延伸模組。<br/><br/>如需詳細資訊，請參閱[擴充 SQL Operations Studio 的功能](./extensions/add-extensions.md)。 |
| 新增額外的[擴充性 API，以用於連線和物件總管](https://github.com/Microsoft/azuredatastudio/wiki/Extensibility-API) \(英文\) 管理。 | &nbsp; |
| 持續修正對重要客戶產生影響的 [GitHub 問題](https://github.com/Microsoft/azuredatastudio/issues) \(英文\)。 | &nbsp; |
| &nbsp; | &nbsp; |

## <a name="february-2018"></a>2018 年 2 月

2018 年 2 月 15 日 &nbsp; / &nbsp; 版本：0.26.7 &nbsp; / &nbsp; 公開預覽

「2 月公開預覽」包含一些功能建議和高優先順序的 Bug 修正。 此版本包括下列增強功能：

&nbsp;

| 變更 | 詳細資料 |
| :----- | :------ |
| 引進自動更新安裝，這會在有新版本可供下載時提供通知。 | &nbsp; |
| 連線對話方塊的 [資料庫] 欄位現在是動態填入的下拉式清單，其中將包含從指定伺服器填入的資料庫清單。 | &nbsp; |
| 引進連線擴充性 API。 | &nbsp; |
| VS Code 編輯器 1.19 整合。 | &nbsp; |
| 更新 JustinPealing/html-query-plan 元件，以取得數個查詢計劃檢視器改善。 | &nbsp; |
| &nbsp; | &nbsp; |

### <a name="fixed-issues-february-2018"></a>已修正的問題，2018 年 2 月

- 修正[問題 6](https://github.com/Microsoft/azuredatastudio/issues/6) \(英文\)：開啟新的查詢索引標籤時，保持連線和已選取的資料庫。
- 修正[問題 22](https://github.com/Microsoft/azuredatastudio/issues/22) \(英文\)：「伺服器名稱」和「資料庫名稱」：這些可以是下拉式清單而不是文字方塊嗎？
- 修正[問題 549](https://github.com/Microsoft/azuredatastudio/issues/549) \(英文\)：無訊息/完全無訊息安裝導致應用程式在安裝後開啟。
- 修正[問題 481](https://github.com/Microsoft/azuredatastudio/issues/481) \(英文\)：新增 [檢查更新] 選項。
- SQL 編輯器顏色標示與自動完成修正：
  - 修正[問題 584](https://github.com/Microsoft/azuredatastudio/issues/584) \(英文\)：IntelliSense 未反白顯示關鍵字 "FULL"。
  - 修正[問題 345](https://github.com/Microsoft/azuredatastudio/issues/345) \(英文\)：在編輯器中為 SQL 函數添加色彩。
  - 修正[問題 300](https://github.com/Microsoft/azuredatastudio/issues/300) \(英文\)：[#tempData] 最新的 "]" 將顯示綠色。
  - 修正[問題 225](https://github.com/Microsoft/azuredatastudio/issues/225) \(英文\)：關鍵字色彩不符。
  - 修正[問題 60](https://github.com/Microsoft/azuredatastudio/issues/60) \(英文\)：在 from 子句中使用暫存資料表時，未正確反白顯示 sql 語法色彩。

## <a name="january-2018"></a>2018 年 1 月

2018 年 1 月 17 日 &nbsp; / &nbsp; 版本：0.25.4 &nbsp; / &nbsp; 公開預覽

「1 月公開預覽」包含一些功能建議和高優先順序的 Bug 修正。 此版本包括下列增強功能：

&nbsp;

| 變更 | 詳細資料 |
| :----- | :------ |
| 連線對話方塊中提供已儲存的伺服器連線。 | &nbsp; |
| 啟用 Hot Exit。 Hot Exit 預設為關閉。 若要啟用，請參閱 [Hot Exit 設定](settings.md#hot-exit)。 | &nbsp; |
| 以伺服器群組為依據的索引標籤色彩設定。 索引標籤顏色設定預設為關閉。 若要啟用，請參閱[索引標籤色彩設定](settings.md#tab-color)。 | &nbsp; |
| 將連線對話方塊中的「伺服器名稱」變更為「伺服器」。 | &nbsp; |
| 修正已中斷的「執行目前查詢」命令。 | &nbsp; |
| 修正拖放中斷指令碼處理 Bug。 | &nbsp; |
| 修正未正確釘選的 [開始] 功能表圖示。 | &nbsp; |
| 修正遺漏的 Azure 帳戶商標圖示。 | &nbsp; |
| &nbsp; | &nbsp; |

## <a name="december-2017"></a>2017 年 12 月

2017 年 12 月 19 日 &nbsp; / &nbsp; 版本：0.24.1 &nbsp; / &nbsp; 公開預覽

「12 月公開預覽」包含所有功能區域中的數個 bug 修正，以及下列增強功能：

&nbsp;

| 變更 | 詳細資料 |
| :----- | :------ |
| [建立防火牆規則] 對話方塊現可協助連線到 Azure SQL Database 和 Azure Synapse Analytics。 | &nbsp; |
| 已新增 Windows 安裝程式，以及 Linux DEB 和 RPM 安裝套件。 | &nbsp; |
| 管理儀表板視覺效果版面配置編輯器。 | &nbsp; |
| 「編寫指令碼為 Alter」和「編寫指令碼為 Execute」 命令。 | &nbsp; |
| 「使用實際計劃執行目前的查詢」命令。 | &nbsp; |
| 整合 VS Code 1.18.1 編輯器平台。 | &nbsp; |
| 啟用 VSIX 延伸模組檔案的側載。 | &nbsp; |
| 支援 "GO N" 批次反覆項目語法。 | &nbsp; |
| &nbsp; | &nbsp; |

## <a name="november-2017"></a>2017 年 11 月

2017 年 11 月 15 日 &nbsp; / &nbsp; 版本：0.23.6

- Azure Data Studio 的初始版本。

## <a name="next-steps"></a>後續步驟

若要開始使用，請參閱下列其中一個快速入門：

- [連線與查詢 SQL Server](quickstart-sql-server.md)
- [連線與查詢 Azure SQL Database](quickstart-sql-database.md)
- [連線並查詢 Azure Synapse Analytics](quickstart-sql-dw.md)

Azure Data Studio 的參與者：

- [https://github.com/Microsoft/azuredatastudio](https://github.com/Microsoft/azuredatastudio)