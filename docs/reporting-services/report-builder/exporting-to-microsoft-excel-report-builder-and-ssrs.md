---
title: 匯出至 Microsoft Excel (報表產生器) | Microsoft Docs
description: 在報表產生器中，Excel 轉譯延伸模組會將分頁報表轉譯為 Office Open XML 格式，以便與 Microsoft Excel 搭配使用。
ms.date: 12/23/2020
ms.prod: reporting-services
ms.prod_service: reporting-services-native
ms.technology: report-builder
ms.topic: conceptual
ms.assetid: 74f726fc-2167-47af-9093-1644e03ef01f
author: maggiesMSFT
ms.author: maggies
ms.openlocfilehash: 4e8b43d3e9e772b06961602bf2d8bd69aa51216e
ms.sourcegitcommit: cb8e2ce950d8199470ff1259c9430f0560f0dc1d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2021
ms.locfileid: "97878851"
---
# <a name="exporting-to-microsoft-excel-report-builder-and-ssrs"></a>Exporting to Microsoft Excel (Report Builder and SSRS)
  [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] Excel 轉譯延伸模組會將 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] 分頁報表轉譯成 [!INCLUDE[ofprexcel](../../includes/ofprexcel-md.md)] 格式 (.xlsx)。 使用 Excel 轉譯延伸模組，Excel 中的資料行寬度就可以更精確地反映報表中的資料行寬度。  
  
 此格式為 Office Open XML。 這個轉譯器所產生檔案的內容類型為 **application/vnd.openxmlformats-officedocument.spreadsheetml.sheet** ，而檔案的副檔名為 .xlsx。  
  
 您可以透過變更裝置資訊設定，變更此轉譯器的某些預設設定。 如需詳細資訊，請參閱 [Excel Device Information Settings](../../reporting-services/excel-device-information-settings.md)。  
  
 如需如何匯出至 Excel 的詳細資訊，請參閱[匯出報表 &#40;報表產生器及 SSRS&#41;](../../reporting-services/report-builder/export-reports-report-builder-and-ssrs.md)。  
  
> [!IMPORTANT]  
>  當您將參數定義為 **String** 類型時，使用者會看到一個可接受任何值的文字方塊。 如果報表參數未繫結至查詢參數且參數值未包含在報表中，報表使用者就可以輸入運算式語法、指令碼或 URL 到參數值中，將報表轉譯為 Excel。 如果另一個使用者接著檢視報表並按一下轉譯的參數內容，該使用者可能會不小心執行惡意指令碼或連結。  
>   
>  若要減輕不小心執行惡意指令碼的風險，請只從信任的來源開啟轉譯的報表。 如需保護報表安全的詳細資訊，請參閱 [保護報表和資源的安全](../../reporting-services/security/secure-reports-and-resources.md)。  
  
##  <a name="excel-limitations"></a><a name="ExcelLimitations"></a> Excel 限制  
 [!INCLUDE[ofprexcel](../../includes/ofprexcel-md.md)] 會對匯出的報表加諸限制。 影響最大的限制如下所列：  
  
-   資料行寬度上限的限制為 255 個字元或 1726.5 點。 轉譯器不會驗證資料行寬度低於限制。  
  
-   資料格中的字元數目上限限制為 32,767 個字元。 如果超出這個限制，轉譯器會顯示錯誤訊息。  
  
-   資料列高度的上限為 409 點。 如果資料列的內容導致資料列高度增加超過 409 點，Excel 儲存格最多可顯示 409 點的部分文字數量。 儲存格內容的其餘部分仍在儲存格內 (最多可達 Excel 的最大字元數 32,767)。

-  由於最大資料列高度為 409 點，因此如果報表中定義的儲存格高度大於 409 點，Excel 會將儲存格內容分割成多個資料列。
  
-   在 Excel 中沒有定義工作表數目上限，但是，諸如記憶體與磁碟空間之類的外部因素，則會應用這些限制。  
  
-   在大綱中，Excel 的巢狀結構最多只允許七個層級。  
  
-   如果控制是否要切換其他項目的報表項目不在要切換之項目的上一個資料列或下一個資料列，則系統也會停用大綱。  
  
 如需 Excel 限制的詳細資訊，請參閱 [Excel 規格和限制](https://support.microsoft.com/en-us/office/excel-specifications-and-limits-1672b34d-7043-467e-8e27-269d656771c3)。  
  
### <a name="sizes-of-excel-2003-xls-files"></a>Excel 2003 (.xls) 檔案的大小  
  
> [!IMPORTANT]  
>  [!INCLUDE[ofprexcel](../../includes/ofprexcel-md.md)] 2003 轉譯延伸模組已被取代。 如需詳細資訊，請參閱 [SQL Server 2016 中 SQL Server Reporting Services 已被取代的功能](../../reporting-services/deprecated-features-in-sql-server-reporting-services-ssrs.md)。  
  
 報表首次匯出並儲存至 Excel 2003 時，並不能享有 Excel 自動套用於本身 *.xls 活頁簿檔案的檔案最佳化功能。 較大的檔案若做為電子郵件訂閱項目和附件，可能會造成問題。 為了減少報表匯出之後的 \*.xls 檔案大小，請先開啟 \*.xls 檔案再重新儲存活頁簿。 重新儲存活頁簿通常可讓檔案大小減少 40% 至 50%。  
  
> [!NOTE]  
>  在 Excel 2003 中，Excel 工作表的資料格會顯示大約 1000 個字元，但不超過可在公式列中編輯的字元數目上限。 此限制不適用於目前 (.xlsx) Excel 檔案。  
  
### <a name="text-boxes-and-text"></a>文字方塊和文字  
 下列限制適用於文字方塊和文字：  
  
-   是運算式的文字方塊值不會轉換為 Excel 公式。 每個文字方塊的值都會在報表處理期間進行評估。 評估的運算式會當做每個 Excel 資料格的內容來匯出。  
  
-   文字方塊會在一個 Excel 資料格中轉譯。 Excel 資料格內的個別文字上只有支援字型大小、字型、裝飾與字型樣式的格式設定。  
  
-   Excel 中不支援「頂線」文字效果。  
  
-   Excel 會將大約 3.75 點的預設填補加入到資料格的左側和右側。 如果文字方塊的填補設定小於 3.75 點，而且寬度僅能勉強容納文字，該文字在 Excel 中可能會換行。  
  
    > [!NOTE]  
    >  若要解決此問題，請在報表中增加文字方塊的寬度。  
  
### <a name="images"></a>影像  
 下列限制適用於影像：  
  
-   Excel 不支援個別資料格的背景影像，因此會忽略報表項目的背景影像。  
  
-   Excel 轉譯延伸模組僅支援報表主體的背景影像。 如果報表主體背景影像顯示在報表中，該影像會轉譯為工作表的背景影像。  
  
### <a name="rectangles"></a>矩形  
 下列限制適用於矩形：  
  
-   報表頁尾中的矩形不會匯出至 Excel。 但是，其餘如報表主體、Tablix 資料格中的矩形將轉譯為某個範圍的 Excel 資料格。  
  
### <a name="report-headers-and-footers"></a>報表頁首和頁尾  
 下列限制適用於報表頁首和頁尾：  
  
-   Excel 頁首和頁尾區段最多支援 256 個字元，包括標記。 轉譯延伸模組會在 256 個字元處截斷字串。  
  
-   [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] 在報表頁首和頁尾上不支援邊界。 當匯出至 Excel 時，這些邊界將設定為零值，且取決於印表機設定，任何包含多列資料的頁首和頁尾未必會列印多個資料列。  
  
-   頁首或頁尾內的文字方塊在匯出至 Excel 時會保留格式設定，但不包括對齊方式。 這是因為開頭和尾端空白在報表轉譯為 Excel 時將遭到修剪。  
  
### <a name="merging-cells"></a>合併資料格  
 下列限制適用於合併資料格：  
  
-   如果資料格經過合併，自動換行就無法正確運作。 如果在利用 AutoSize 屬性轉譯文字方塊的資料列上有任何經過合併的資料格存在，自動調整將無法運作。  
  
 Excel 轉譯器基本上是一個配置轉譯器。 其目的是盡可能接近原貌將轉譯的報表配置複寫到 Excel 工作表，從而讓資料格在工作表中能順利合併以保留報表配置。 合併的資料格可能會造成問題，因為 Excel 中的排序功能需要以非常獨特的方式合併資料格，排序才能正常運作。 例如，Excel 資料格合併的範圍必須為相同大小，才可以進行排序。  
  
 如果匯出為 Excel 工作表的報表非得排序不可，則下列措施應有助於減少 Excel 工作表中合併的資料格數目，這正是導致 Excel 排序功能難以施展的常見原因。  
  
-   未將項目靠左或靠右對齊是造成資料格合併的常見主因。 請務必將所有報表項目的左右側邊緣彼此切齊。 在大多數情況下，項目對齊且寬度相同即可解決問題。  
  
-   儘管所有項目都已精準地對齊，您可能會發現某些資料行在部分罕見情況下還是合併。 這大概是因為 Excel 工作表轉譯時的內部單位轉換與捨入所造成。 在報表定義語言 (RDL) 中，您可用不同的度量單位來指定位置和大小，例如英吋、像素、公分和點。 而 Excel 內部使用的單位是點。 為盡量避免轉換 (由英吋和公分轉換為點) 並降低捨入不準確的可能性，請考慮指定所有度量以完整的點為單位來獲得最直接的結果。 一英吋等於 72 點。  
  
### <a name="report-row-groups-and-column-groups"></a>報表資料列群組和資料行群組  
 當包含資料列群組或資料行群組的報表匯出至 Excel 時，這些報表會包含空白資料格。 假設有一個針對通勤距離分組資料列的報表。 每個通勤距離都可以包含一個以上的客戶。 下圖顯示此報表。  
  
 ![Reporting Services 入口網站中的報表](../../reporting-services/report-builder/media/ssrb-excelexportssrs.png "Reporting Services 入口網站中的報表")  
  
 當此報表匯出至 Excel 時，通勤距離只會顯示在 [通勤距離] 資料行的單一資料格中。 根據報表中文字的對齊方式 (靠上、置中或靠下)，此值會位於第一個、中央或最後一個資料格中。 其他資料格都是空白的。 包含客戶名稱的 [名稱] 資料行沒有任何空白資料格。 下圖顯示匯出至 Excel 之後的報表。 為了強調，我們加入了紅色資料格框線。 灰色方塊是空白資料格 (紅線和灰色方塊都不是所匯出報表的一部分)。  
  
 ![匯出至 Excel 的報表 (具框線)](../../reporting-services/report-builder/media/ssrb-exportedexcellines.png "匯出至 Excel 的報表 (具框線)")  
  
 這表示，當您將含有資料列群組或資料行群組的報表匯出至 Excel 之後，必須先加以修改，然後才能在樞紐資料表中顯示匯出的資料。 您必須將群組值加入至遺漏值的資料格，讓工作表成為所有資料格都含有值的二維資料表。 下圖顯示更新過的工作表。  
  
 ![匯出至 Excel 的報表 (扁平化)](../../reporting-services/report-builder/media/ssrb-excelexportnomatrix.png "匯出至 Excel 的報表 (扁平化)")  
  
 因此，如果您建立報表的特定目的是要將它匯出至 Excel，以便進一步分析報表資料，請考慮不要對報表中的資料列或資料行進行分組。  
  
## <a name="excel-renderer"></a>Excel 轉譯器  
  
### <a name="current-xlsx-excel-file-renderer"></a>目前 (.xlsx) Excel 檔案轉譯器  
 在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)][!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]中，預設 Excel 轉譯器是與目前 (.xlsx) [!INCLUDE[ofprexcel](../../includes/ofprexcel-md.md)] 檔案相容的版本。 在 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] Web 入口網站和 SharePoint 清單的 [匯出]  功能表上，這就是 [Excel]  選項。  
  
 當您使用預設的 Excel 轉譯器而不是舊版的 Excel 2003 (.xls) 轉譯器時，您可以安裝適用於 Word、Excel 以及 PowerPoint 的 Microsoft Office 相容性套件，以允許舊版 Excel 開啟匯出的檔案。  
  
### <a name="excel-2003-xls-renderer"></a>Excel 2003 (.xls) 轉譯器  
  
> [!IMPORTANT]  
>  [!INCLUDE[ofprexcel](../../includes/ofprexcel-md.md)] 2003 轉譯延伸模組已被取代。 如需詳細資訊，請參閱 [SQL Server 2016 中 SQL Server Reporting Services 已被取代的功能](../../reporting-services/deprecated-features-in-sql-server-reporting-services-ssrs.md)。  
  
 與 Excel 2003 相容的舊版 Excel 轉譯器現在已命名為 Excel 2003，而且使用該名稱列於功能表上。 此轉譯器所產生檔案的內容類型為 **application/vnd.ms-excel** ，而檔案的副檔名為 .xls。  
  
 **[Excel 2003]** 功能表選項預設不會顯示。 系統管理員可以藉由更新 RSReportServer 組態檔，在某些情況下讓它顯示。 若要使用 Excel 2003 轉譯器，從 [!INCLUDE[ssBIDevStudioFull](../../includes/ssbidevstudiofull-md.md)] 匯出報表，請更新 RSReportDesigner 組態檔。  
  
 在下列案例中， **[Excel 2003]** 功能表選項延伸模組永不顯示：  
  
-   報表產生器處於中斷連接模式，而且您在報表產生器中預覽報表。 因為 RSReportServer 組態檔位於報表伺服器上，所以您從中匯出報表的工具或產品必須連接至報表伺服器，以便讀取組態檔。  
  
-   報表檢視器 Web 組件處於本機模式，而且 SharePoint 伺服陣列並未與 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] 報表伺服器整合。 如需詳細資訊，請參閱[本機模式與連線模式報表 &#40;SharePoint 模式的 Reporting Services&#41;](../../reporting-services/report-server-sharepoint/local-mode-vs-connected-mode-reports-in-the-report-viewer.md)  
  
 如果 **[Excel 2003]** 功能表選項轉譯器設定為顯示，在下列案例中，您就可以同時使用 Excel 和 Excel 2003 選項：  
  
-   [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] Web 入口網站原生模式。  
  
-   SharePoint 網站 (當 Reporting Services 是以 SharePoint 整合模式安裝時)。  
  
-   [!INCLUDE[ssBIDevStudioFull](../../includes/ssbidevstudiofull-md.md)] 和預覽報表。  
  
-   報表產生器已連接至報表伺服器。  
  
-   報表檢視器 Web 組件處於遠端模式。  
  
 下列 XML 顯示 RSReportServer 和 RSReportDesigner 組態檔中這兩個 Excel 轉譯延伸模組的元素：  
  
 `<Extension Name="EXCELOPENXML" Type="Microsoft.ReportingServices.Rendering.ExcelOpenXmlRenderer.ExcelOpenXmlRenderer,Microsoft.ReportingServices.ExcelRendering"/>`  
  
 `<Extension Name="EXCEL" Type="Microsoft.ReportingServices.Rendering.ExcelRenderer.ExcelRenderer,Microsoft.ReportingServices.ExcelRendering" Visible="false"/>`  
  
 EXCELOPENXML 延伸模組會定義目前 (.xlsx) Excel 檔案的 Excel 轉譯器。 EXCEL 延伸模組會定義 EXCEL 2003 版本。 `Visible = "false"` 表示隱藏 Excel 2003 轉譯器。 如需詳細資訊，請參閱 [RsReportServer.config 組態檔](../../reporting-services/report-server/rsreportserver-config-configuration-file.md) 和 [RSReportDesigner 組態檔](../../reporting-services/report-server/rsreportdesigner-configuration-file.md)。  
  
### <a name="differences-between-the-current-xlsx-excel-and-excel-2003-renderers"></a>目前 (.xlsx) Excel 與 Excel 2003 轉譯器之間的差異  
 使用目前 (.xlsx) Excel 或 Excel 2003 轉譯器所轉譯的報表通常完全相同。只有在極少數的情況下，您才會發現兩種格式之間的差異。 下表將比較 Excel 與 Excel 2003 轉譯器。  
  
|屬性|[Excel 2003]|目前 Excel|  
|--------------|----------------|-------------------|  
|每個工作表的資料行上限|256|16,384|  
|每個工作表的資料列上限|65,536|1,048,576|  
|工作表中允許的色彩數目|56 (調色盤)<br /><br /> 如果在報表中使用的色彩超過 56 種，轉譯延伸模組會讓所需的色彩符合自訂調色版中已提供的 56 種色彩之一。|約為 1600 萬 (24 位元色彩)|  
|ZIP 壓縮的檔案|None|ZIP 壓縮|  
|預設字型家族|Arial|Calibri|  
|預設字型大小|10pt|11pt|  
|預設資料列高度|12.75 pt|15 pt|  
  
 因為報表會明確設定資料列高度，所以預設資料列高度只會影響匯出至 Excel 時自動調整大小的資料列。  
  
##  <a name="report-items-in-excel"></a><a name="ReportItemsExcel"></a> Excel 中的報表項目  
 矩形、子報表、報表主體以及資料區域都會轉譯為 Excel 資料格的範圍。 文字方塊、影像、圖表、資料橫條、走勢圖、地圖、量測計與指標必須在一個 Excel 資料格內轉譯，而且這個資料格可能會依據報表其餘部分的配置而合併。  
  
 系統會將影像、圖表、走勢圖、資料橫條、地圖、量測計、指標與線條放置在一個 Excel 資料格內，但是會位於資料格方格的頂端。 線條會轉譯為資料格框線。  
  
 圖表、走勢圖、資料橫條、地圖、量測計和指標都會匯出為圖片。 它們所描述的資料 (例如圖表的值和成員標籤) 都不會與它們一起匯出，而且除非該資料包含在報表內資料區的資料行或資料列中，否則也無法在 Excel 活頁簿中使用。  
  
 如果您要處理圖表、走勢圖、資料橫條、地圖、量測計與指標資料，請將報表匯出為 .csv 檔，或從報表產生符合 Atom 的資料摘要。 如需詳細資訊，請參閱[匯出至 CSV 檔案 &#40;報表產生器及 SSRS&#41;](../../reporting-services/report-builder/exporting-to-a-csv-file-report-builder-and-ssrs.md) 和[從多個報表產生資料摘要 &#40;報表產生器及 SSRS&#41;](../../reporting-services/report-builder/generating-data-feeds-from-reports-report-builder-and-ssrs.md)。  
  
## <a name="page-sizing"></a>調整頁面大小  
 Excel 轉譯延伸模組會使用頁面高度與寬度設定來決定要在 Excel 工作表中定義的紙張設定。 Excel 會嘗試讓 PageHeight 和 PageWidth 屬性設定符合其中一個常用的紙張大小。  
  
 如果找不到相符項目，Excel 會針對印表機使用預設的頁面大小。 如果頁面寬度小於頁面高度，則方向會設定為 [縱向]；否則，系統會設定為 [橫向]。  
  
##  <a name="worksheet-tab-names"></a><a name="WorksheetTabNames"></a> 工作表標籤名稱  
 當您將報表匯出到 Excel 時，分頁符號所建立的報表頁面會匯出到不同的工作表。 如果您為此報表提供初始頁面名稱，Excel 活頁簿的每一個工作表預設將會擁有這個名稱。 此名稱會出現在工作表標籤上。但是，因為活頁簿中的每一個工作表都必須有唯一的名稱，所以 1 開頭而且遞增 1 的整數會附加到每一個額外工作表的初始頁面名稱中。 例如，如果初始頁面名稱為 **Sales Report by Fiscal Year**，第二個工作表的名稱會是 **Sales Report by Fiscal Year1**，第三個工作表的名稱會是 **Sales Report by Fiscal Year2**，依此類推。  
  
 如果分頁符號所建立的所有報告頁面都會提供新的頁面名稱，每一個工作表都會有關聯的頁面名稱。 但是，這些頁面名稱可能不是唯一的。 如果頁面名稱不是唯一的，工作表的命名方式會與初始頁面名稱相同。 例如，如果兩個群組的頁面名稱為 **Sales for NW**，則一個工作表標籤的名稱將會是 **Sales for NW**，另一個則為 **Sales for NW1**。  
  
 如果報表並未提供初始頁面名稱，而且也沒有任何頁面名稱與分頁符號有關，則工作表標籤的預設名稱將會是 **Sheet1**、 **Sheet2**，依此類推。  
  
 Reporting Services 會提供可在報表、資料區、群組和矩形上設定的屬性，協助您建立可依照您所要的方式匯出到 Excel 的報表。 如需詳細資訊，請參閱 [Reporting Services 中的分頁 &#40;報表產生器及 SSRS&#41;](../../reporting-services/report-design/pagination-in-reporting-services-report-builder-and-ssrs.md)。  
  
##  <a name="document-properties"></a><a name="DocumentProperties"></a> 文件屬性  
 Excel 轉譯器會將下列中繼資料寫入到 Excel 檔。  
  
|報表元素屬性|描述|  
|-------------------------------|-----------------|  
|建立時間|執行報表的日期和時間，當做 ISO 日期/時間值。|  
|作者|Report.Author|  
|描述|Report.Description|  
|LastSaved|執行報表的日期和時間，當做 ISO 日期/時間值。|  
  
##  <a name="page-headers-and-footers"></a><a name="PageHeadersFooters"></a> 頁首和頁尾  
 根據裝置資訊的 SimplePageHeaders 設定，可以用兩種方式轉譯頁首：系統可以在每個工作表資料格方格的頂端轉譯頁首，或者在實際的 Excel 工作表頁首區段轉譯。 根據預設，頁首會轉譯到 Excel 工作表的資料格方格。  
  
 不管 SimplePageHeaders 設定的值為何，頁尾永遠會轉譯到實際的 Excel 工作表頁尾區段。  
  
 Excel 頁首和頁尾區段最多支援 256 個字元，包括標記。 如果超出這個限制，Excel 轉譯器會移除頁首和/或頁尾字串結尾的標記字元，以減少字元的總數。 如果移除所有標記字元後長度仍然超出最大值，系統會從右側開始截斷字串。  
  
### <a name="simplepageheader-settings"></a>SimplePageHeader 設定  
 根據預設，裝置資訊的 SimplePageHeaders 設定會設定為 **False**；因此，在 Excel 工作表介面上，頁首會轉譯為報表中的資料列。 包含頁首的工作表資料列會變成鎖定的資料列。 您可以在 Excel 中凍結或取消凍結窗格。 如果有選取 [列印標題]  選項，這些頁首會自動設定為列印在每個工作表頁面上。  
  
 如果在 Excel 的 [頁面配置] 索引標籤上選取 **[列印標題]** 選項，頁首會在活頁簿的每個工作表頂端重複 (除了文件引導模式封面之外)。 如果沒有在 [報表頁首屬性] 或 [報表頁尾屬性] 對話方塊中選取 **[在第一頁列印]** 或 **[在最後一頁列印]** 選項，頁首就不會分別加入到第一頁或最後一頁。  
  
 頁尾會在 Excel 的頁尾區段中轉譯。  
  
 由於 Excel 的限制，文字方塊是可以在 Excel 頁首/頁尾區段轉譯的唯一報表項目類型。  
  
##  <a name="interactivity"></a><a name="Interactivity"></a> 互動性  
 在 Excel 中支援某些互動項目。 下列是特定行為的描述。  
  
### <a name="show-and-hide"></a>顯示與隱藏  
 [!INCLUDE[ofprexcel](../../includes/ofprexcel-md.md)] 對於報表匯出時如何管理隱藏與顯示的報表項目有一些限制。 包含可以切換之報表項目的群組、資料列和資料行會轉譯為 Excel 大綱。 Excel 建立的大綱可以跨整個資料列或資料行展開和折疊資料列與資料行，這可能會折疊不想折疊的報表項目。 此外，Excel 的大綱符號會因為重疊的大綱而變得雜亂。 若要解決這些問題，請在使用 Excel 轉譯延伸模組時套用下列大綱規則：  
  
-   左上角可以切換的報表項目在 Excel 中仍然可以切換。 可以切換並與可以在左上角切換之報表項目共用垂直或水平空間的報表項目無法在 Excel 中切換。  
  
-   若要決定資料區域會依據資料列或資料行折疊，必須決定控制切換之報表項目的位置，以及切換報表項目的位置。 如果控制切換的項目出現在要切換的項目之前，系統就會依據資料列折疊。 否則，項目會依據資料行摺疊。 如果控制切換的項目平均出現在要切換的區域旁邊與上方，系統就會隨著可依據資料列折疊的資料列轉譯。  
  
-   為判斷小計放置在轉譯報表中的位置，轉譯延伸模組會檢查動態成員的第一個執行個體。 如果有對等靜態成員出現在該執行個體的正上方，系統就會假設該動態成員為小計。 系統會設定大綱來表示這是摘要資料。 如果沒有動態成員的靜態同層級，執行個體的第一個執行個體就是小計。  
  
-   由於 Excel 的限制，大綱所建立的巢狀結構最多只能有 7 個層級。  
  
### <a name="document-map"></a>文件引導模式  
 如果報表中有任何文件引導模式標籤，就會轉譯文件引導模式。 文件引導模式會轉譯為插入活頁簿第一個索引標籤位置的 Excel 工作表封面。 此工作表的名稱為 **「文件引導模式」** (Document Map)。  
  
 顯示在文件引導模式中的文字取決於報表項目或群組的 DocumentMapLabel 屬性。 文件引導模式標籤會以報表中出現的順序列出，從第一個資料行的第一個資料列開始。 每個文件引導模式標籤資料格都會縮排報表中出現的層級深度。 系統會在接續的資料行中放置標籤來表示每個縮排層級。 Excel 最多支援 256 層的大綱巢狀層級。  
  
 文件引導模式大綱會轉譯為可折疊的 Excel 大綱。 大綱結構會與文件引導模式的巢狀結構相符。 大綱的展開和折疊狀態會從第二層級開始。  
  
 文件引導模式的根節點就是報表名稱 \<*reportname*>.rdl，而且這不是互動的。 文件引導模式連結字型為 Arial，10pt。  
  
### <a name="drillthrough-links"></a>鑽研連結  
 系統會將文字方塊中出現的鑽研連結轉譯為轉譯文字之資料格中的 Excel 超連結。 而影像和圖表的鑽研連結則會在轉譯時，轉譯為影像上的 Excel 超連結。 當您按一下鑽研連結時，用戶端的預設瀏覽器會開啟，並巡覽至目標的 HTML 檢視。  
  
### <a name="hyperlinks"></a>超連結  
 系統會將文字方塊中出現的超連結轉譯為轉譯文字之資料格中的 Excel 超連結。 而影像和圖表的超連結則會在轉譯時，轉譯為影像上的 Excel 超連結。 當您按一下超連結時，用戶端的預設瀏覽器會開啟，並巡覽至目標 URL。  
  
### <a name="interactive-sorting"></a>互動式排序  
 Excel 不支援互動式排序。  
  
### <a name="bookmarks"></a>書籤  
 系統會將文字方塊中的書籤連結轉譯為轉譯文字之資料格中的 Excel 超連結。 而影像和圖表的書籤連結則會在轉譯時，轉譯為影像上的 Excel 超連結。 按一下書籤時，會移至轉譯設為書籤之報表項目的 Excel 資料格。  
  
##  <a name="changing-reports-at-run-time"></a><a name="ConditionalFormat"></a> 在執行階段變更報表  
 如果報表必須轉譯為多種格式，但您無法建立依希望的方式轉譯成全部所需格式的單一報表配置，則您應可考慮使用內建的全域 RenderFormat 值，在執行階段依條件變更報表外觀。 如此可讓您根據用於獲得每一種格式最佳結果的轉譯器，隱藏或顯示報表項目。 如需詳細資訊，請參閱[內建的全域和使用者參考 &#40;報表產生器及 SSRS&#41;](../../reporting-services/report-design/built-in-collections-built-in-globals-and-users-references-report-builder.md)。  
  
## <a name="see-also"></a>另請參閱  
 [Reporting Services 中的分頁 &#40;報表產生器及 SSRS&#41;](../../reporting-services/report-design/pagination-in-reporting-services-report-builder-and-ssrs.md)   
 [轉譯行為 &#40;報表產生器及 SSRS&#41;](../../reporting-services/report-design/rendering-behaviors-report-builder-and-ssrs.md)   
 [不同報表轉譯延伸模組的互動式功能 &#40;報表產生器及 SSRS&#41;](../../reporting-services/report-builder/interactive-functionality-different-report-rendering-extensions.md)   
 [轉譯報表項目 &#40;報表產生器及 SSRS&#41;](../../reporting-services/report-design/rendering-report-items-report-builder-and-ssrs.md)   
 [資料表、矩陣和清單 &#40;報表產生器及 SSRS&#41;](../../reporting-services/report-design/tables-matrices-and-lists-report-builder-and-ssrs.md)  
  
  
