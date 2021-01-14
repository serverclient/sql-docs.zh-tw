---
title: Reporting Services 中的新功能 | Microsoft Docs
description: 了解不同版本 SQL Server Reporting Services 的新功能，包括對主要功能區域的變更。
ms.prod: reporting-services
ms.prod_service: reporting-services-native
ms.technology: reporting-services
ms.topic: conceptual
author: maggiesMSFT
ms.author: maggies
ms.reviewer: ''
ms.custom: ''
ms.date: 12/05/2019
ms.openlocfilehash: eaef4f651b65b2097aa4cfe0f41c97442469f193
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98171440"
---
# <a name="whats-new-in-sql-server-reporting-services-ssrs"></a>SQL Server Reporting Services (SSRS) 中的新功能

[!INCLUDE[ssrs-appliesto](../includes/ssrs-appliesto.md)] [!INCLUDE[ssrs-appliesto-2016-and-later](../includes/ssrs-appliesto-2016-and-later.md)] [!INCLUDE[ssrs-appliesto-not-pbirsi](../includes/ssrs-appliesto-not-pbirs.md)]

了解不同版本 SQL Server [!INCLUDE[ssRSnoversion](../includes/ssrsnoversion-md.md)] 的新功能。 本文章涵蓋主要功能領域，並會在發行新的項目時更新。

如需 Power BI 報表伺服器的資訊，請參閱[什麼是 Power BI 報表伺服器？](https://docs.microsoft.com/power-bi/report-server/get-started)。

::: moniker range=">=sql-server-ver15"

## <a name="sql-server-2019-reporting-services"></a>SQL Server 2019 Reporting Services

**下載** :::image type="icon" source="https://docs.microsoft.com/analysis-services/analysis-services/media/download.png":::

您可以從 Microsoft 下載中心下載 [SQL Server 2019 Reporting Services](https://www.microsoft.com/download/details.aspx?id=100122)。

### <a name="azure-sql-managed-instance-support"></a>Azure SQL 受控執行個體支援

現在，可以在裝載於 VM 或您資料中心的 Azure SQL 受控執行個體 (MI) 中，裝載用於 SQL Server Reporting Services (SSRS) 的資料庫目錄。 僅支援使用資料庫認證來連線到 SQL MI。

### <a name="power-bi-premium-dataset-support"></a>Power BI Premium 資料集支援

您可以使用 Microsoft 報表產生器或 SQL Server Data Tools (SSDT) 連線到 Power BI 資料集。 接著，您就可以使用 SQL Server Analysis Services 連線能力，將這些報表發佈至 SSRS 2019。 使用者必須使用預存 Windows 使用者名稱和密碼來啟用此情節。

### <a name="alttext-alternative-text-support-for-report-elements"></a>報表元素的 AltText (替代文字) 支援

撰寫報表時，您可以使用工具提示來指定報表上每個元素的文字。 螢幕助讀程式技術會正確識別這些工具提示。

### <a name="azure-active-directory-application-proxy-support"></a>Azure Active Directory 應用程式 Proxy 支援

若使用 Azure Active Directory 應用程式 Proxy，您就不再需要管理自己的 Web 應用程式 Proxy，以允許透過 Web 或行動裝置應用程式進行安全存取。

### <a name="custom-headers"></a>自訂標頭

針對符合所指定 RegEx 模式的 URL 設定標頭值。 使用者可以使用有效的 XML 來更新自訂標頭值，以設定所選要求 URL 的標頭值。 系統管理員可以在 XML 中新增任意數目的標頭。 請參閱＜伺服器屬性進階頁面＞一文中的[自訂標頭](tools/server-properties-advanced-page-reporting-services.md#customheaders)以取得詳細資料。

### <a name="transparent-database-encryption"></a>透明資料庫加密

針對 Enterprise 和 Standard 版本，SQL Server 2019 現在支援 SSRS 類別目錄資料庫的透明資料庫加密。 

### <a name="microsoft-report-builder-update"></a>Microsoft 報表產生器更新

新發行的報表產生器版本與 2016、2017 和 2019 版 Reporting Services 完全相容。 其也與所有已發行和支援的 Power BI 報表伺服器版本相容。

::: moniker-end

::: moniker range=">=sql-server-2017"

## <a name="sql-server-2017-reporting-services"></a>SQL Server 2017 Reporting Services

**下載** :::image type="icon" source="https://docs.microsoft.com/analysis-services/analysis-services/media/download.png":::

請前往 **[Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=55252)** 下載 SQL Server 2017 Reporting Services。

### <a name="comments-on-reports"></a>報表的註解

報表現在提供留言功能，可新增觀點並與其他人共同作業。 您也可以在留言內包含附件。

![報表伺服器內的註解](media/what-s-new-in-sql-server-reporting-services-ssrs/report-server-comments.png)

如需詳細資訊，請參閱[新增報表伺服器中報表的註解](https://powerbi.microsoft.com/documentation/reportserver-add-comments/)。

### <a name="dax-queries-in-reporting-tools"></a>報告工具中的 DAX 查詢

在最新版本的報表產生器及 SQL Server Data Tools 中，您可對 SQL Server Analysis Services 表格式資料模型建立原生 DAX 查詢。 您可於查詢設計工具中拖放欄位。 請參閱 [Reporting Services 部落格](https://blogs.msdn.microsoft.com/sqlrsteamblog/2017/03/09/query-designer-support-for-dax-now-available-in-report-builder-and-sql-server-data-tools/)。

### <a name="rest-api-support"></a>REST API 支援

為了進行現代化應用程式的開發和自訂，SQL Server Reporting Services 現在支援完全符合 OpenAPI 的 RESTful API。 您現在可以在 [SwaggerHub](https://app.swaggerhub.com/apis/microsoft-rs/SSRS/2.0) 上找到完整的 API 規格和文件。

### <a name="query-designer-support-for-dax-now-in-report-builder-and-sql-server-data-tools"></a>現在報表產生器及 SQL Server Data Tools 中支援 DAX 的查詢設計工具

您現在可以在報表產生器及 SQL Server Data Tools 中，對支援的 SQL Server Analysis Services 表格式資料模型建立原生 DAX 查詢。 您可以在這兩個工具中使用查詢設計工具來拖放您想要的欄位。 DAX 查詢隨即會為您產生。

請參閱 [Reporting Services 部落格](https://blogs.msdn.microsoft.com/sqlrsteamblog/2017/03/09/query-designer-support-for-dax-now-available-in-report-builder-and-sql-server-data-tools/)。

* 下載 [SQL Server 報表產生器](https://go.microsoft.com/fwlink/?LinkId=734968)。
* 下載 [SQL Server Data Tools - 候選版](https://docs.microsoft.com/sql/ssdt/sql-server-data-tools-ssdt-release-candidate)。

> [!NOTE]
> 您只能搭配使用 DAX 的查詢設計工具與 SQL Server 2016+ 內建的 SSAS 表格式資料來源。
::: moniker-end

## <a name="ssrs-2016"></a>SSRS 2016

### <a name="reporting-services-ssrswebportal-non-markdown"></a>Reporting Services [!INCLUDE[ssRSWebPortal-Non-Markdown](../includes/ssrswebportal-non-markdown-md.md)]  

有新的 [!INCLUDE[ssRSnoversion](../includes/ssrsnoversion-md.md)] [!INCLUDE[ssRSWebPortal-Non-Markdown](../includes/ssrswebportal-non-markdown-md.md)] 可用。 更新的入口網站包含
- KPI
- 行動報表
- 分頁的報表
- Excel 檔案
- Power BI Desktop 檔案

[!INCLUDE[ssRSWebPortal](../includes/ssrswebportal.md)] 取代舊版中的報表管理員。

若要建立行動報表，您需要 [!INCLUDE[SS_MobileReptPub_Short](../includes/ss-mobilereptpub-short.md)]。

如需 [!INCLUDE[ssRSWebPortal-Non-Markdown](../includes/ssrswebportal-non-markdown-md.md)]的詳細資訊，請參閱 [入口網站 (SSRS 原生模式)](../reporting-services/web-portal-ssrs-native-mode.md)。  

![顯示 SQL Server Reporting Services 入口網站的螢幕擷取畫面。](../reporting-services/media/ssrsportal.png "ssRSPortal")  

#### <a name="custom-branding-for-the-ssrswebportal-non-markdown"></a>[!INCLUDE[ssRSWebPortal-Non-Markdown](../includes/ssrswebportal-non-markdown-md.md)] 的自訂商標 

您可以使用商標套件，以組織的標誌和色彩自訂 [!INCLUDE[ssRSWebPortal-Non-Markdown](../includes/ssrswebportal-non-markdown-md.md)] 。  

如需自訂商標的詳細資訊，請參閱 [建立入口網站品牌形象](https://msdn.microsoft.com/6dac97f7-02a6-4711-81a3-e850a6b40bf1)

#### <a name="key-performance-indicators-kpi-in-the-ssrswebportal-non-markdown"></a>[!INCLUDE[ssRSWebPortal-Non-Markdown](../includes/ssrswebportal-non-markdown-md.md)]中的關鍵效能指標 (KPI) 

您可以直接在 [!INCLUDE[ssRSWebPortal-Non-Markdown](../includes/ssrswebportal-non-markdown-md.md)] 中，建立與目前資料夾內容相關的 KPI。 建立 KPI 時，您可以選擇資料集欄位並彙總它們的值。 您也可以選取相關內容，以鑽研至更多詳細資料。

![顯示 SQL Server Reporting Services 入口網站中 KPIS 的螢幕擷取畫面。](../reporting-services/media/ssrs-webportal-kpi.png)

如需詳細資訊，請參閱[使用入口網站中的 KPI](https://msdn.microsoft.com/a28cf500-6d47-4268-a248-04837e7a09eb)

### <a name="mobile-reports"></a>行動報表

Reporting Services 行動報表是針對各種外形規格最佳化的專用報表，並為存取行動裝置上報表的使用者提供最佳體驗。 行動報表具備各種視覺效果，從時間、類別和比較圖表到矩形式樹狀結構圖和自訂的對應。 將行動報表連接到資料來源範圍，包括內部部署 SQL Server Analysis Services 多維度和表格式資料。 您可在設計界面上為行動報表放置方格列和欄會調整的欄位。 富彈性的行動報表元素會自動調整以符合任何畫面大小。 您可以將行動報表儲存至報表服務伺服器，並在瀏覽器或 Power BI 行動應用程式中加以檢視和互動。 支援的裝置包括：

- iPad
- iPhone
- Android 手機
- 或任何 Windows 10 裝置

#### <a name="mobile-report-publisher"></a>行動報表發行工具  

[!INCLUDE[SS_MobileReptPub_Long](../includes/ss-mobilereptpub-long.md)]可讓您建立 SQL Server 行動報表並將其發佈到 [!INCLUDE[ssRSnoversion](../includes/ssrsnoversion-md.md)] [!INCLUDE[ssRSWebPortal-Non-Markdown](../includes/ssrswebportal-non-markdown-md.md)]。  

![SS_MRP_LayoutTabSmall](../reporting-services/media/ss-mrp-layouttabsm.png "SS_MRP_LayoutTabSmall")  

如需詳細資訊，請參閱 [使用 SQL Server 行動報表發行工具建立行動報表](../reporting-services/mobile-reports/create-mobile-reports-with-sql-server-mobile-report-publisher.md)。  

#### <a name="sql-server-mobile-reports-hosted-in-reporting-services-available-in-power-bi-mobile-app"></a>裝載於 Reporting Services 的 SQL Server 行動報表可在 Power BI Mobile 應用程式中使用  

iPad 和 iPhone 上適用於 iOS 的 Power BI 行動裝置應用程式現在可以顯示裝載於本機報表伺服器的 SQL Server 行動報表。  

![SS_MRP_iPad_HomeSm](../reporting-services/media/ss-mrp-ipad-homesm.png "SS_MRP_iPad_HomeSm")  

根據預設，您必須進行幾項組態變更才能連接。 如需如何允許 Power BI Mobile 應用程式連接到報表伺服器的詳細資訊，請參閱 [啟用報表伺服器進行 Power BI 行動存取](../reporting-services/report-server/enable-a-report-server-for-power-bi-mobile-access.md)。

### <a name="support-of-sharepoint-mode-and-sharepoint-2016"></a>支援 SharePoint 模式和 SharePoint 2016 模式。  

[!INCLUDE[ssSQL15](../includes/sssql16-md.md)] [!INCLUDE[ssRSnoversion](../includes/ssrsnoversion-md.md)] 支援與 SharePoint 2013 和 SharePoint 2016 整合。

如需詳細資訊，請參閱  

- [支援的 SharePoint 和 Reporting Services 伺服器與增益集的組合 &#40;SQL Server 2016&#41;](../reporting-services/install-windows/supported-combinations-of-sharepoint-and-reporting-services-server.md)  

- [尋找適用於 SharePoint 產品之 Reporting Services 增益集的位置](../reporting-services/install-windows/where-to-find-the-reporting-services-add-in-for-sharepoint-products.md)  

- [安裝 Reporting Services SharePoint 模式](../reporting-services/install-windows/install-reporting-services-sharepoint-mode.md)  

### <a name="microsoft-net-framework-4-support"></a>Microsoft .NET Framework 4 支援  

[!INCLUDE[ssRSCurrent-md](../includes/ssrscurrent-md.md)] 支援 Microsoft .NET Framework 4 的目前版本，包含 4.0 和 4.5.1 版。 如果尚未安裝 .Net Framework 4.x 版本， [!INCLUDE[ssNoVersion-md](../includes/ssnoversion-md.md)] 安裝程式會在功能安裝步驟安裝 .NET 4.0。  

### <a name="report-improvements"></a>報表改進

**HTML 5 轉譯引擎：** 新的 HTML5 轉譯引擎以新式 Web「完整」標準模式及新式瀏覽器為目標。  新的轉譯引擎不再依賴幾種舊瀏覽器使用的 quirks 模式。

如需瀏覽器支援的詳細資訊，請參閱 [Reporting Services 和 Power View 的瀏覽器支援](../reporting-services/browser-support-for-reporting-services-and-power-view.md)。  

**新式編頁報表：** 使用樣式新穎的圖表、量測計、地圖和其他資料視覺效果，以設計美觀的新式編頁報表。

**矩形式樹狀結構圖和放射環狀圖：** 使用樹狀結構圖 ![ssrs_treemap_icon](../reporting-services/media/ssrs-treemap-icon.png "ssrs_treemap_icon") 與放射環狀圖 ![ssrs_sunburst_icon](../reporting-services/media/ssrs-sunburst-icon.png "ssrs_sunburst_icon") 來加強您的報表，這是顯示階層式資料的絕佳方式。 如需詳細資訊，請參閱 [Tree Map and Sunburst Charts in Reporting Services](../reporting-services/report-design/tree-map-and-sunburst-charts-in-reporting-services.md)。  

**報表內嵌：** 您現在可以使用 iframe 及 URL 參數，將行動和編頁報表內嵌到其他網頁和應用程式中。  

**將報表項目釘選到 Power BI 儀表板** ：當您在 [!INCLUDE[ssRSWebPortal](../includes/ssrswebportal.md)]中檢視報表時，可以選取報表項目，並將其釘選到 [!INCLUDE[sspowerbi](../includes/sspowerbi-md.md)] 儀表板。   可釘選的項目包括圖表、量測計面板、地圖和影像。 您可以：

1. 選取包含欲釘選之儀表板的群組。
2. 選取要將項目釘選至的儀表板。
3. 選取您儀表板更新磚的頻率。

![注意](https://docs.microsoft.com/analysis-services/analysis-services/instances/install-windows/media/ssrs-fyi-note.png "注意") 重新整理由 [!INCLUDE[ssRSnoversion](../includes/ssrsnoversion-md.md)] 訂用帳戶管理，而釘選項目之後，您可以編輯訂用帳戶及設定不同的重新整理排程。

![顯示 [釘選到 Power BI 儀表板] 對話方塊的螢幕擷取畫面。](../reporting-services/media/ssrs-pin-to-powerbi.png) 

如需詳細資訊，請參閱 [Power BI 報表伺服器整合 &#40;設定管理員&#41;](../reporting-services/install-windows/power-bi-report-server-integration-configuration-manager.md) 和[將 Reporting Services 項目釘選到 Power BI 儀表板](../reporting-services/pin-reporting-services-items-to-power-bi-dashboards.md)。  

**PowerPoint 轉譯及匯出：** Microsoft PowerPoint (PPTX) 格式是新的 [!INCLUDE[ssRSCurrent](../includes/ssrscurrent-md.md)] 轉譯延伸模組。 您可以使用 PPTX 格式從下列常用應用程式匯出報表：報表產生器、報表設計師 (在 SSDT 中) 和 [!INCLUDE[ssRSWebPortal](../includes/ssrswebportal.md)]。 如需範例，下圖顯示了 [!INCLUDE[ssRSWebPortal](../includes/ssrswebportal.md)] 的匯出功能表。 

![顯示 [匯出] 下拉式清單的螢幕擷取畫面，其中已標註 [PowerPoint] 選項。](../reporting-services/media/ssrs-export-powerpoint.png) 

您也可以對訂閱輸出選取 PPTX 格式，並使用報表伺服器 URL 存取來轉譯及匯出報表。 例如，您瀏覽器中的以下 URL 命令會從報表伺服器的具名執行個體匯出報表。  

```https
https://servername/ReportServer_THESQLINSTANCE/Pages/ReportViewer.aspx?%2freportfolder%2freport+name+with+spaces&rs:Format=pptx  
```  

如需詳細資訊，請參閱 [Export a Report Using URL Access](../reporting-services/export-a-report-using-url-access.md)。

**PDF 在遠端列印取代 ActiveX：** 報表檢視器工具列現在會透過 PDF 列印，而非 ActiveX 控制項。 包含 Microsoft Edge 在內的大多數新式瀏覽器都支援新的報表檢視器。 不再需要下載任何 ActiveX 控制項！ 根據您使用的瀏覽器以及已安裝的 PDF 檢視應用程式和服務，[!INCLUDE[ssRSnoversion](../includes/ssrsnoversion-md.md)] 或 [列印] 對話方塊會開啟以列印您的報表，或您會收到下載 .PDF 檔案的提示。 您身為系統管理員，仍然可以從 [!INCLUDE[ssManStudio](../includes/ssmanstudio-md.md)] 停用用戶端列印。

如需詳細資訊，請參閱 [啟用和停用 Reporting Services 的用戶端列印](../reporting-services/report-server/enable-and-disable-client-side-printing-for-reporting-services.md)。

![[列印] 對話方塊的螢幕擷取畫面。](../reporting-services/media/ssrs-pdf-printing.png)

### <a name="subscription-improvements"></a>訂閱功能改進  

|功能|支援的伺服器模式|  
|-------------|---------------------------|  
|**啟用和停用訂閱**。 新的使用者介面選項可快速停用及啟用訂閱。 停用的訂閱會維持其中的其他組態屬性，例如排程，並且可以輕鬆啟用。<br /><br /> ![顯示 [啟用]、[停用] 和 [刪除] 選項的螢幕擷取畫面。](../reporting-services/media/ssrs-enable-disable-subscriptions.png)<br /><br /> 如需詳細資訊，請參閱 [Disable or Pause Report and Subscription Processing](../reporting-services/subscriptions/disable-or-pause-report-and-subscription-processing.md)。|原生模式|  
|**訂閱描述**。 您現在可以在建立新訂閱時，在訂閱屬性中加入報表的描述。 該描述會加到訂閱摘要頁面上。|SharePoint 與原生模式|  
|**變更訂閱擁有者**。 加強的使用者介面可快速變更訂閱的擁有者。 舊版 [!INCLUDE[ssRSnoversion](../includes/ssrsnoversion-md.md)] 可讓系統管理員使用指令碼變更訂閱擁有者。 從 [!INCLUDE[ssSQL15](../includes/sssql16-md.md)] 版本開始，您可以使用使用者介面或指令碼來變更訂閱擁有者。 有使用者離開或在組織中變更角色時，便需要進行變更訂閱擁有者這項一般管理工作。|SharePoint 與原生模式|  
|**檔案共用訂閱的共用認證**。 [!INCLUDE[ssRSnoversion](../includes/ssrsnoversion-md.md)] 檔案共用訂閱現在同時存有兩個工作流程：<br /><br /> 您的 [!INCLUDE[ssRSnoversion](../includes/ssrsnoversion-md.md)] 系統管理員可以設定單一檔案共用帳戶，可供多個訂用帳戶使用。 檔案共用帳戶是在 [!INCLUDE[ssRSnoversion](../includes/ssrsnoversion-md.md)] 原生模式設定管理員的 [指定檔案共用帳戶] 中設定。 使用者在訂用帳戶設定頁面上選取 [使用檔案共用帳戶]。<br /><br /> 您針對目的檔案共用，使用特定認證設定個別訂閱。<br /><br /> 您也可以混用兩種方法，讓某些檔案共用訂閱使用中央檔案共用帳戶，而其他訂閱則使用特定認證。|原生模式|

### <a name="sql-server-data-tools-ssdt"></a>SQL Server Data Tools (SSDT)

SSDT 的新版本包括 [!INCLUDE[ssRSCurrent](../includes/ssrscurrent-md.md)] 的專案範本：[報表伺服器專案精靈] 與 [報表伺服器專案]。 如需下載 SSDT 的相關資訊，請參閱 [SQL Server Data Tools for Visual Studio 2015](https://go.microsoft.com/fwlink/?LinkId=827542)(適用於 Visual Studio 2015 的 SQL Server Data Tools)。  

### <a name="report-builder-improvements"></a>報表產生器改進

**新的報表產生器使用者介面：** 核心 [!INCLUDE[ssRBnoversion](../includes/ssrbnoversion.md)] 使用者介面現在透過簡化的 UI 項目，具備了新式外觀及操作方式。  

|新增|Previous|  
|-|-|  
|![ssrs_rbfacelift_new](../reporting-services/media/ssrs-rbfacelift-new.png "ssrs_rbfacelift_new")|![ssrs_rbfacelift_old](../reporting-services/media/ssrs-rbfacelift-old.png "ssrs_rbfacelift_old")|  

**自訂參數窗格：** 您現在可以自訂參數窗格。 使用報表產生器中的設計界面，您可以將參數拖曳到參數窗格中的特定資料行及資料列。 您可以新增及移除資料行以變更窗格配置。 如需詳細資訊，請參閱 [自訂報表中的參數窗格 &#40;報表產生器&#41;](../reporting-services/report-design/customize-the-parameters-pane-in-a-report-report-builder.md)中建立的行動報表。  

![報表資料窗格和參數窗格中的參數清單](../reporting-services/media/ssrs-customizeparameter-parameterlist-reportdatapane.png "報表資料窗格和參數窗格中的參數清單")  

**高 DPI 支援：** [!INCLUDE[ssRBnoversion](../includes/ssrbnoversion.md)] 支援高 DPI (每英吋點數) 縮放比例及裝置。  如需高 DPI 的詳細資訊，請參閱下列主題：  

- [Windows 8.1 DPI 縮放比例功能加強 (英文)](https://blogs.windows.com/windowsexperience/2013/07/15/windows-8-1-dpi-scaling-enhancements/)  

- [高 DPI 與 Windows 8.1](https://technet.microsoft.com/library/dn528848.aspx)  

## <a name="next-steps"></a>後續步驟

[Analysis Services 的新功能](https://msdn.microsoft.com/aa69c299-b8f4-4969-86d8-b3292fe13f08)  
[回溯相容性](reporting-services-backward-compatibility.md)  
[SQL Server 版本所支援的 Reporting Services 功能](../reporting-services/reporting-services-features-supported-by-the-editions-of-sql-server-2016.md)  
[升級和移轉 Reporting Services](../reporting-services/install-windows/upgrade-and-migrate-reporting-services.md)  
[Reporting Services](../reporting-services/create-deploy-and-manage-mobile-and-paginated-reports.md)  

更多問題嗎？ [請嘗試詢問 Reporting Services 論壇](https://go.microsoft.com/fwlink/?LinkId=620231)
