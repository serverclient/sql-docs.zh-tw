---
title: 下載 SQL Server Data Tools (SSDT)
description: 了解 SQL Server Data Tools (SSDT)。 了解如何搭配 Visual Studio 2019 和 Visual Studio 2017 安裝這項資料庫開發工具組。
ms.prod: sql
ms.prod_service: sql-tools
ms.technology: ssdt
ms.topic: conceptual
keywords: 安裝 SSDT, 下載 SSDT, 最新的 SSDT
ms.assetid: b0fc4987-d260-4d0a-9dd1-98099835b361
author: dzsquared
ms.author: drskwier
ms.reviewer: maghan
ms.custom: seo-lt-2019
ms.date: 02/20/2020
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||=azuresqldb-mi-current'
ms.openlocfilehash: 8913e553460c9fae3d008783d571aca81b5c21a4
ms.sourcegitcommit: 44eebb659f9b226c08aea6c31a909b22ad4fec60
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2021
ms.locfileid: "97860569"
---
# <a name="download-sql-server-data-tools-ssdt-for-visual-studio"></a>下載適用於 Visual Studio 的 SQL Server Data Tools (SSDT)

[!INCLUDE[appliesto-ss-asdb-asdw-pdw-md.md](../includes/appliesto-ss-asdb-asdw-pdw-md.md)]

**SQL Server Data Tools (SSDT)** 是一款新式開發工具，可用來建置 SQL Server 關聯式資料庫、Azure SQL 中的資料庫、Analysis Services (AS) 資料模型、Integration Services (IS) 套件和 Reporting Services (RS) 報表。 有了 SSDT，您便可設計和部署任何 SQL Server 內容類型，就像在 Visual Studio 中開發應用程式一樣容易。

## <a name="ssdt-for-visual-studio-2019"></a>適用於 Visual Studio 2019 的 SSDT

### <a name="changes-in-ssdt-for-visual-studio-2019"></a>適用於 Visual Studio 2019 的 SSDT 中的變更

用於建立資料庫專案的 SSDT 核心功能，對於 Visual Studio 一直是不可或缺的。

在 Visual Studio 2019 中，啟用 Analysis Services、Integration Services 和 Reporting Services 專案其所需功能已移至個別限定的 Visual Studio (VSIX) 延伸模組。

> [!NOTE]
> 沒有適用於 Visual Studio 2019 的 SSDT 獨立安裝程式。

### <a name="install-ssdt-with-visual-studio-2019"></a>使用 Visual Studio 2019 安裝 SSDT

如果已安裝 [Visual Studio 2019](/visualstudio/install/install-visual-studio?preserve-view=true&view=vs-2019)，則可編輯工作負載清單以包含 SSDT。 如果您尚未安裝 Visual Studio 2019，則可下載並安裝 [Visual Studio 2019 Community](https://visualstudio.microsoft.com/downloads/)。

若要修改已安裝的 Visual Studio 工作負載以包含 SSDT，請使用 Visual Studio 安裝程式。

1. 啟動 Visual Studio 安裝程式。 在 Windows [開始] 功能表中，您可搜尋「安裝程式」。

   ![2019 年 Windows [開始] 功能表中的 Visual Studio 安裝程式](../ssdt/media/visual-studio-installer.png)

2. 在安裝程式中，選取所要新增 SSDT 的 Visual Studio 版本，然後選擇 [修改]。

3. 在工作負載清單中選取位於 [資料儲存和處理] 下方的 [SQL Server Data Tools]。

   ![2019 年資料儲存及處理工作負載](../ssdt/media/download-sql-server-data-tools-ssdt/data-workload-2019.png)

針對 Analysis Services、Integration Services 或 Reporting Services 專案，請從 Visual Studio 內的 [延伸模組] > [管理延伸模組]，或從 [Marketplace](https://marketplace.visualstudio.com/search?term=services&target=VS&category=All%20categories&vsVersion=&sortBy=Relevance) 安裝適當的[延伸模組](/visualstudio/ide/finding-and-using-visual-studio-extensions)。

* [Analysis Services (英文)](https://marketplace.visualstudio.com/items?itemName=ProBITools.MicrosoftAnalysisServicesModelingProjects)
* [Integration Services](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects)
* [Reporting Services](https://marketplace.visualstudio.com/items?itemName=ProBITools.MicrosoftReportProjectsforVisualStudio)

## <a name="ssdt-for-visual-studio-2017"></a>適用於 Visual Studio 2017 的 SSDT

### <a name="changes-in-ssdt-for-visual-studio-2017"></a>適用於 Visual Studio 2017 的 SSDT 中的變更

從 Visual Studio 2017 開始，建立資料庫專案的功能已整合到 Visual Studio 安裝。 不需要針對核心 SSDT 體驗安裝 SSDT 獨立安裝程式。

現在建立 Analysis Services、Integration Services 或 Reporting Services 專案仍須使用 SSDT 獨立安裝程式。

### <a name="install-ssdt-with-visual-studio-2017"></a>使用 Visual Studio 2017 安裝 SSDT

若要在 [Visual Studio 安裝](/visualstudio/install/install-visual-studio)期間安裝 SSDT，請選取 [Data storage and processing] \(資料儲存和處理\) 工作負載，然後選取 [SQL Server Data Tools]。

若已安裝 Visual Studio，請使用 Visual Studio 安裝程式來修改已安裝的工作負載，以包含 SSDT。

1. 啟動 Visual Studio 安裝程式。 在 Windows [開始] 功能表中，您可搜尋「安裝程式」。

   ![2017 年 Windows [開始] 功能表中的 Visual Studio 安裝程式](../ssdt/media/visual-studio-installer.png)

2. 在安裝程式中，選取所要新增 SSDT 的 Visual Studio 版本，然後選擇 [修改]。

3. 在工作負載清單中選取位於 [資料儲存和處理] 下方的 [SQL Server Data Tools]。

   ![2017 年資料儲存及處理工作負載](../ssdt/media/download-sql-server-data-tools-ssdt/data-workload-2017.png)

### <a name="install-analysis-services-integration-services-and-reporting-services-tools"></a>安裝 Analysis Services、Integration Services 和 Reporting Services 工具

若要安裝 Analysis Services、Integration Services 或 Reporting Services 專案支援，請執行 [SSDT 獨立安裝程式](#ssdt-for-vs-2017-standalone-installer)。

此安裝程式會列出可新增 SSDT 工具的 Visual Studio 執行個體。 如果尚未安裝 Visual Studio，請選取 [安裝新的 SQL Server Data Tools 執行個體] 以搭配 Visual Studio 的最小版本來安裝 SSDT，但若要獲得最佳體驗，則建議搭配 [Visual Studio 的最新版本](https://www.visualstudio.com/downloads)來使用 SSDT。

![選取 AS、IS、RS](../ssdt/media/download-sql-server-data-tools-ssdt/select-services.png)

## <a name="ssdt-for-vs-2017-standalone-installer"></a>SSDT for VS 2017 (獨立安裝程式)

:::image type="icon" source="media/download.png" border="false"::: **[下載適用於 Visual Studio 2017 (15.9.7) 的 SSDT](https://go.microsoft.com/fwlink/?linkid=2151759)**

> [!IMPORTANT]
> * 如果已安裝「Analysis Services 專案」與「Reporting Services 專案」延伸模組，請先將其解除安裝並關閉所有 VS 執行個體，再安裝適用於 Visual Studio 2017 (15.9.7) 的 SSDT。 
> * 已移除 Power Query Source for SQL Server 2017 的收件匣元件。 我們現在宣告 Power Query Source for SQL Server 2017 與 2019 為 OOB 元件，您可以在[這裡](https://www.microsoft.com/download/details.aspx?id=100619)下載。
> * 若要使用 Oracle 和 Teradata 連接器設計套件，並將目標設為 SQL 2019 之前的舊版 SQL Server，除了 [Microsoft Oracle Connector for SQL 2019](https://www.microsoft.com/download/details.aspx?id=58228) \(英文\) 和 [Microsoft Teradata Connector for SQL 2019](https://www.microsoft.com/download/details.aspx?id=100599) \(英文\) 以外，您還需要安裝由 Attunity 提供適用於 Oracle 和 Teradata 的 Microsoft 連接器相應版本。
>    * [目標為 SQL Server 2017 由 Attunity 提供適用於 Oracle 和 Teradata 的 Microsoft 連接器 5.0 版](https://www.microsoft.com/download/details.aspx?id=55179) \(英文\)
>    * [目標為 SQL Server 2016 由 Attunity 提供適用於 Oracle 和 Teradata 的 Microsoft 連接器 4.0 版](https://www.microsoft.com/download/details.aspx?id=52950) \(英文\)
>    * [目標為 SQL Server 2014 由 Attunity 提供適用於 Oracle 和 Teradata 的 Microsoft 連接器 3.0 版](https://www.microsoft.com/download/details.aspx?id=44582) \(英文\)
>    * [目標為 SQL Server 2012 由 Attunity 提供適用於 Oracle 和 Teradata 的 Microsoft 連接器 2.0 版](https://www.microsoft.com/download/details.aspx?id=29283) \(英文\)

### <a name="release-notes"></a>版本資訊

如需完整變更清單，請參閱 [SQL Server Data Tools (SSDT) 的版本資訊](release-notes-ssdt.md)。

### <a name="system-requirements"></a>系統需求

SSDT for Visual Studio 2017 與 Visual Studio 具有相同的[系統需求](/visualstudio/productinfo/vs2017-system-requirements-vs)。

### <a name="available-languages---ssdt-for-vs-2017"></a>可用語言 - 適用於 VS 2017 的 SSDT

這版 **適用於 VS 2017 的 SSDT** 提供下列語言版本：

* [簡體中文](https://go.microsoft.com/fwlink/?linkid=2151759&clcid=0x804)
* [繁體中文](https://go.microsoft.com/fwlink/?linkid=2151759&clcid=0x404)
* [英文 (美國)](https://go.microsoft.com/fwlink/?linkid=2151759&clcid=0x409)
* [法文](https://go.microsoft.com/fwlink/?linkid=2151759&clcid=0x40c)
* [德文](https://go.microsoft.com/fwlink/?linkid=2151759&clcid=0x407)
* [義大利文](https://go.microsoft.com/fwlink/?linkid=2151759&clcid=0x410)
* [日文](https://go.microsoft.com/fwlink/?linkid=2151759&clcid=0x411)
* [韓文](https://go.microsoft.com/fwlink/?linkid=2151759&clcid=0x412)
* [葡萄牙文 (巴西)](https://go.microsoft.com/fwlink/?linkid=2151759&clcid=0x416)
* [俄文](https://go.microsoft.com/fwlink/?linkid=2151759&clcid=0x419)
* [西班牙文](https://go.microsoft.com/fwlink/?linkid=2151759&clcid=0x40a)

### <a name="considerations-and-limitations"></a>考量與限制

* 您無法離線安裝社群版本

* 若要升級 SSDT，您必須遵循用來安裝 SSDT 的相同路徑。 例如，如果您使用 VSIX 延伸模組以新增 SSDT，則必須透過 VSIX 延伸模組來更新。 如果您是透過個別安裝來安裝 SSDT，則必須使用該方法進行升級。

## <a name="offline-install"></a>離線安裝

若要在未連線到網際網路時安裝 SSDT，請依照此節中的步驟執行。 如需詳細資訊，請參閱[建立 Visual Studio 2017 的網路安裝](/visualstudio/install/create-a-network-installation-of-visual-studio)。

首先，請在 **連線** 時完成下列步驟：

1. [下載 SSDT 的獨立安裝程式](#ssdt-for-vs-2017-standalone-installer)。

2. [下載 vs_sql.exe](https://aka.ms/vs/15/release/vs_sql.exe)。

3. 在連線時，執行下列命令下載離線安裝所需的全部檔案。 使用 `--layout` 選項是關鍵，這會為離線安裝下載實際檔案。 以實際配置路徑取代 `<filepath>` 來儲存檔案。
   1. 針對特定語言，請傳遞地區設定：`vs_sql.exe --layout c:\<filepath> --lang en-us` (一種語言 ~1 GB)。
   1. 針對所有語言，請省略 `--lang` 引數：`vs_sql.exe --layout c:\<filepath>` (所有語言 ~3.9 GB)。

完成前述步驟之後，就可以 **離線** 執行下列步驟：

1. 執行 `vs_setup.exe --NoWeb`，以安裝 VS2017 Shell 及 SQL Server 資料專案。

2. 從配置資料夾執行 `SSDT-Setup-ENU.exe /install`，並選取 SSIS/SSRS/SSAS。
   a. 若要自動安裝，請執行 `SSDT-Setup-ENU.exe /INSTALLALL[:vsinstances] /passive`。

若要查看可用的選項，請執行 `SSDT-Setup-ENU.exe /help`

> [!NOTE]
> 如果使用 Visual Studio 2017 的完整版本，請為 SSDT 建立專用的離線資料夾，並從這個新建立的資料夾執行 `SSDT-Setup-ENU.exe` (不要將 SSDT 新增至其他 Visual Studio 2017 離線配置)。 如果您將 SSDT 配置新增至現有的 Visual Studio 離線配置，就不會在此建立必要的執行階段 (.exe) 元件。

## <a name="supported-sql-versions"></a>支援的 SQL 版本

|專案範本|支援的 SQL 平台|
|-------------------|--------------------|
|關聯式資料庫| SQL Server 2005\* - SQL Server 2017<br> (使用 SSDT 17.x 或適用於 Visual Studio 2017 的 SSDT 來連線至 [Linux 上的 SQL Server](../linux/sql-server-linux-overview.md))<br /><br />Azure SQL Database<br /><br />Azure Synapse Analytics (僅支援查詢；尚不支援資料庫專案)<br /><br /> \* SQL Server 2005 支援已淘汰，<br /><br /> 改為使用官方支援的 SQL 版本|
|Analysis Services 模型<br /><br />Reporting Services 報表 | SQL Server 2008 - SQL Server 2017|
|Integration Services 封裝| SQL Server 2012 - SQL Server 2019 |

## <a name="dacfx"></a>DacFx

Visual Studio 2015 與 2017 的 SSDT 都使用 DacFx 17.4.1：[下載資料層應用程式架構 (DacFx) 17.4.1](https://www.microsoft.com/download/details.aspx?id=56508)。

## <a name="previous-versions"></a>舊版

若要下載及安裝 SSDT for Visual Studio 2015 或更舊版的 SSDT，請參閱[舊版 SQL Server Data Tools (SSDT 及 SSDT-BI)](previous-releases-of-sql-server-data-tools-ssdt-and-ssdt-bi.md)。

## <a name="see-also"></a>另請參閱

* [SSDT MSDN 論壇](https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=ssdt) 

* [SSDT 團隊部落格](/archive/blogs/ssdt/)

* [DACFx API 參考](/previous-versions/sql/sql-server-2014/dn645454(v=sql.120))

* [下載 SQL Server Management Studio (SSMS)](../ssms/download-sql-server-management-studio-ssms.md)

## <a name="next-steps"></a>後續步驟

安裝 SSDT 後，請逐步完成這些教學課程，以了解如何使用 SSDT 來建立資料庫、套件、資料模型及報表。

* [專案導向的離線資料庫開發](project-oriented-offline-database-development.md)

* [SSIS 教學課程：建立簡易 ETL 套件](../integration-services/ssis-how-to-create-an-etl-package.md)

* [Analysis Services 教學課程](/analysis-services/analysis-services-tutorials-ssas)

* [建立基本資料表報表 (SSRS 教學課程)](../reporting-services/create-a-basic-table-report-ssrs-tutorial.md)

[!INCLUDE[get-help-options](../includes/paragraph-content/get-help-options.md)]
