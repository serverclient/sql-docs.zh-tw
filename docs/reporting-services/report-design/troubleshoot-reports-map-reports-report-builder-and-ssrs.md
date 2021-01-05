---
title: 報表疑難排解：地圖報表 (報表產生器) | Microsoft Docs
description: 探索當自訂、新增圖層，或在報表產生器中使用對應來發行報表時，可能產生情況的疑難排解構想。
ms.date: 07/22/2020
ms.prod: reporting-services
ms.prod_service: reporting-services-native
ms.technology: report-design
ms.topic: conceptual
ms.assetid: a690aec2-056b-40bc-8cab-c694bd2d6d62
author: maggiesMSFT
ms.author: maggies
ms.openlocfilehash: 6411d6f3810153614de0e60e9b20555e061836e3
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97641597"
---
# <a name="troubleshoot-reports-map-reports-report-builder-and-ssrs"></a>報表疑難排解：地圖報表 (報表產生器及 SSRS)
  當您將地圖或地圖圖層加入至報表時、自訂報表中現有的地圖或地圖圖層時、預覽報表中的地圖時，或發行包含地圖的報表時，在 [!INCLUDE[ssRSnoversion_md](../../includes/ssrsnoversion-md.md)] 分頁報表中可能會發生與地圖相關的問題。 您可以使用本主題來協助疑難排解這些問題。  
    
   ## <a name="need-more-help"></a>需要其他協助嗎？  
   
  請嘗試︰  
 * Stack Overflow 上的 [SQL Server Reporting Services](https://stackoverflow.com/questions/tagged/reporting-services)  
 * 在 [Microsoft SQL Server UserVoice](https://feedback.azure.com/forums/908035-sql-server) 記錄問題或建議。  

  
##  <a name="report-definition-size-issues"></a><a name="Embedded"></a> 報表定義大小問題  
 使用本節協助解決與報表定義大小相關的問題。  
  
## <a name="how-do-i-reduce-the-report-definition-size"></a>如何縮減報表定義大小？  
 地圖圖層包含從空間資料建立的地圖元素。 在某些情況下，地圖元素會內嵌在報表定義中。 使用下列方式會發生這個情況：  
  
-   如果空間資料的來源來自地圖庫中的地圖或您本機電腦上的 ESRI 形狀檔，則地圖元素會自動內嵌到報表定義中。  
  
     如果您將報表發行至報表伺服器，而且有一個本機檔案的空間資料來源參考，則無法在報表處理時擷取空間資料。 為避免這個問題，地圖資料會內嵌在報表定義中。  
  
-   如果您在「地圖精靈」或「圖層精靈」中選取內嵌空間資料的選項，以空間資料為基礎的地圖元素便會內嵌到地圖圖層的報表定義中。  
  
-   如果您在 [地圖] 窗格中，以滑鼠右鍵按一下圖層，然後按一下其中一個 **[內嵌空間資料]** 選項，以空間資料為基礎的地圖元素便會內嵌到地圖圖層的報表定義中。  
  
-   若要從報表定義中移除以 ESRI 形狀檔為基礎的內嵌資料，您必須執行下列操作：  
  
1.  將 ESRI .shp 和 .dbf 檔案上傳或發行至報表伺服器。  
  
2.  在報表之 [設計] 檢視的 [地圖] 窗格中，選取包含內嵌資料的圖層，然後開啟 **[圖層資料]** 屬性。 在 **[使用以下來源的空間資料]** 中，選取 **[連結到 ESRI 形狀檔 (.shp)]**，然後瀏覽到報表伺服器上包含 ESRI 形狀檔的資料夾來選取它，然後按一下 [確定]。  
  
3.  儲存報表。 您變更之圖層的內嵌資料已從報表定義中移除。  
  
 來自地圖庫之報表中的地圖元素永遠會內嵌在地圖圖層中。  
  
##  <a name="spatial-data-issues"></a><a name="Spatial"></a> 空間資料問題  
 使用本節協助解決與空間資料相關的問題。  
  
## <a name="on-the-design-surface-i-see-sample-spatial-data"></a>在設計介面上，我看到範例空間資料。  
 在設計階段，設計介面可能會因為下列原因，顯示範例空間資料的相關訊息：  
  
-   空間資料來自 ESRI .shp 檔，但是沒有對應的 .dbf 檔。 ESRI 形狀檔通常同時包含內含空間資料的 .shp 檔案和支援檔案 .dbf。 請確認 .dbf 檔與 .shp 檔位於相同的目錄中。  
  
-   空間資料來自資料集，因此查詢的資料連接無效，或目前的認證無效。  
  
-   地圖圖層包含屬性和運算式。 在報表執行之前，系統不會評估運算式。 若要查看地圖，您必須預覽報表。  
  
-   空間資料來自擁有特定範圍的資料集。 例如，如果地圖套疊 (Nested) 在 Tablix 資料區，或地圖將相同的資料集範圍用於分析資料與空間資料，則在報表執行前，系統不會計算資料範圍。  
  
## <a name="when-i-set-an-offset-for-an-individual-map-element-a-cluster-of-map-elements-move"></a>當我針對個別地圖元素設定位移時，地圖元素的叢集移動  
 空間資料會定義顯示在每個地圖圖層上的地圖元素。 地圖元素可以根據單一點、一組點、單一線條、一組線條、單一多邊形或一組多邊形的空間資料。 每一個地圖元素都是一個單位。 如果地圖元素包含多個點，而且您移動此元素，則該地圖元素的所有點都會移動。  
  
 每一個地圖元素的資料都是由外部來源的空間資料格式所決定。 例如，當查詢指定了 SQL Server 資料庫內的空間資料時，結果集中的每個資料列都可能包含多組點或線條或多邊形座標。 在結果集中，由單一資料列定義的所有地圖元素都會視為一個單位。 如果您想要更改特定座標組的顯示，必須執行下列其中一項操作：  
  
-   變更查詢，以便將座標組當做結果集中的個別資料列傳回。  
  
-   選取地圖元素來更改及設定對應的內嵌點、線條或多邊形屬性，其方法是覆寫對應圖層類型的預設顯示屬性。  
  
## <a name="my-layer-that-uses-spatial-data-from-an-esri-shapefile-always-has-embedded-data"></a>使用來自 ESRI 形狀檔中空間資料的我的圖層永遠有內嵌的資料。  
 為確保包含地圖的報表可以在報表伺服器上執行，ESRI 形狀檔必須可以當做報表伺服器上的資源使用。 如果您將圖層加入至地圖，並指定位於您本機檔案系統上的形狀檔，則空間資料會自動內嵌至報表中。  
  
 若要將內嵌的資料取代成 ESRI 形狀檔的連結，您必須將 .shp 檔及其符合的 .dbf 檔上傳至報表伺服器，然後變更圖層的空間資料來源。  
  
## <a name="i-renamed-a-data-source-or-dataset-to-a-friendly-name-and-now-no-data-appears-in-my-map"></a>我將資料來源或資料集重新命名為易記名稱，因此現在我的地圖中沒有出現任何資料。  
 當您手動變更任何報表項目的名稱時，系統不會自動更新報表定義。  
  
 當您變更資料集的名稱時，必須手動更新參考該資料集的任何資料區或地圖圖層。 若要重建資料集的 Tablix、圖表或量測計，選取設計介面上的項目，開啟資料區屬性，然後選取適當資料集的名稱。 若要重建資料集的地圖圖層，選取圖層，開啟圖層屬性，然後選取適當資料集的名稱。  
  
## <a name="my-spatial-data-contains-nulls-and-empty-strings"></a>我的空間資料包含 Nulls 和空字串。  
 在地圖報表項目的空間資料中，Null 會設為零 (0)，而空字串會設為空白 ("")。  
  
 對於來自 SQL Server 資料庫的空間資料，若要變更此行為，您必須變更傳回空間資料的查詢。  
  
## <a name="my-map-exceeds-the-maximum-number-of-spatial-elements"></a>我的地圖超出空間元素的最大數目  
 根據預設，一個地圖可以有 20,000 個地圖元素或 1,000,000 個點。 如果您的地圖超出這些限制，您可以使用下列其中一種方法：  
  
-   移除圖層。  
  
-   降低地圖解析度。  
  
-   降低地圖檢視區座標來檢視較小的區域。  
  
-   如果空間資料來自報表資料集，將篩選設定為限制來自資料集的資料。 篩選必須在非空間資料類型的欄位上設定。  
  
-   如果空間資料來自 SQL Server 資料庫，變更查詢來使用空間功能可將資料限制為較小的區域。  
  
##  <a name="viewport-center-and-view-issues"></a><a name="Viewport"></a> 檢視區置中與檢視問題  
 使用本節協助解決與檢視區選項相關的問題。  
  
## <a name="i-cannot-set-the-center-and-view-on-an-embedded-map-element"></a>我無法在內嵌的地圖元素上設定置中與檢視。  
 若要將檢視區在特定的地圖元素上置中，您必須在圖層上擁有與分析資料相關的空間資料。  
  
## <a name="i-set-the-map-center-and-view-in-my-report-when-i-reopen-the-report-why-isnt-the-map-view-the-same"></a>我在報表中設定地圖置中與檢視。 當我重新開啟報表時，為什麼地圖檢視不一樣了？  
 如果讀取空間資料所需的使用者認證在您開啟報表時無法用於該報表，則會使用預留位置空間資料。 根據針對地圖檢視區設定的置中與縮放選項，地圖檢視可能會在不同的圖層上置中。  
  
 若要重新載入空間資料並使用儲存在報表中的地圖檢視置中，以滑鼠右鍵按一下地圖檢視區，然後按一下 **[重新載入]** 。 輸入空間資料來源的認證之後，圖層會載入空間資料，並還原地圖檢視。  
  
## <a name="the-center-and-view-for-a-map-layer-option-does-not-work"></a>地圖圖層選項的置中與檢視沒有作用。  
 當檢視區設定為在特定圖層的空間資料上置中，而且檢視的中心似乎不是圖層的中心時，可能會有包含在空間資料中的小島或小區域太小，因此在檢視區中看不到。 例如，國家 (地區) 的空間資料可能會包含小島或其他小領域做為領域的一部分。 檢視區會使用所有空間資料計算圖層的中心。  
  
 若要覆寫圖層的計算，您可以執行下列其中一項操作：  
  
-   指定檢視區的自訂中心。  
  
-   變更檢視區的縮放層級來排除您不想要包含的位置。  
  
-   將空間資料內嵌在報表中，並刪除您不想要包含的位置。  
  
##  <a name="layer-issues"></a><a name="Layers"></a> 圖層問題  
 使用本節協助解決與圖層選項相關的問題。  
  
## <a name="i-do-not-see-one-or-more-layers-in-my-map"></a>我在地圖中看不到一個或多個圖層。  
 您在報表中是否看到地圖圖層，取決於空間資料的可用性、空間資料與分析資料之間的關聯性、空間資料類型與對應的圖層類型、圖層上的可見性與透明度選項，以及圖層繪製順序。 如果您從圖層看不到資料，請檢查下列選項：  
  
-   **圖層類型與空間資料類型：** 圖層類型僅顯示符合圖層類型的空間資料。 例如，如果圖層類型為 [點]，但是空間資料為 [線條]，則不會出現任何資料。  
  
-   **符合欄位值：** 在欄位中，您指定用來讓分析資料與空間資料產生關聯的值，必須唯一識別每個地圖元素。 這些欄位必須擁有相同的資料類型。 欄位中的值都必須相同。 如需詳細資訊，請參閱 [圖例、色階與距離標尺問題](#Legend)。  
  
-   **圖層順序：** 圖層在 [地圖] 窗格中的順序就是使用報表轉譯器繪製圖層的順序。 最先繪製之圖層上的空間資料可能會遭到稍後繪製之圖層的空間資料覆寫。 系統會最先繪製出現在清單頂端的圖層。 當您變更圖層在清單中的順序時，就是在變更圖層的繪製順序。  
  
-   **透明度：** 您可以針對每個地圖圖層分別指定透明度。 透明度的預設值會根據您加入圖層的方式而有所不同。 透明度為 0% 時，表示圖層不透明，而且不會透過此圖層顯示其他任何圖層資料。 若要讓其他資料透過現有的圖層顯示，請將值調整為較高的百分比，就可以提供您想要的效果。  
  
-   **可見度。** 根據地圖檢視區的縮放層級，圖層的可見性為 [Visible]、[Hidden] 或 [ZoomBased]。 您也可以指定縮放層級的最大與最小範圍。 可見性可以根據評估為這些值之其中一個的運算式。  
  
    > [!TIP]  
    >  您可以在 [地圖] 窗格中切換每個圖層的可見性。 當您要設計每個圖層時，關閉其他所有圖層，就可以判斷問題出在個別圖層還是出在圖層間的透明度問題。  
  
## <a name="i-set-a-filter-on-the-map-layer-and-it-has-no-effect"></a>我在地圖圖層上設定了一個篩選，但是沒有作用。  
 若要篩選圖層的資料，必須指定篩選運算式中的資料類型。 請確認您已經指定正確的基礎資料類型，讓篩選方程式可以正確評估指定的條件。 如需詳細資訊，請參閱[篩選、分組和排序資料 &#40;報表產生器及&#41;](../../reporting-services/report-design/filter-equation-examples-report-builder-and-ssrs.md)。  
  
##  <a name="legend-color-scale-and-rule-issues"></a><a name="Legend"></a> 圖例、色階與規則問題  
 使用本節協助解決與規則、圖例和色階選項相關的問題。  
  
## <a name="how-do-i-control-the-values-in-the-map-legend"></a>如何控制地圖圖例中的值？  
 系統會從您針對每個地圖圖層指定的地圖元素類型規則，以及您針對圖例指定的分佈規則，自動決定圖例值。  
  
 根據預設，所有規則所產生的所有項目都會顯示在第一個圖例中。 每個圖層之所有多邊形、線條與點規則的值都會構成組合的圖例範圍。 若要顯示不同圖例中的項目，您必須先建立多個圖例，然後針對每個規則，指定要將相關項目顯示在哪個圖例中。  
  
 若要讓規則與特定的圖例產生關聯，開啟規則屬性，然後在 [圖例] 頁面上選取要使用之圖例的名稱。 若要從圖例移除項目，在圖例選項中選取圖例名稱的空白行。 如果您要重新命名報表中的圖例元素，必須手動讓每個圖層與適當的圖例項目產生關聯。  
  
 若要控制每個圖例的標題與內容，請使用規則的 [圖例] 屬性。 您可以指定要建立的內文區塊數目、變更將值指派給每個內文區塊的計算、設定最小與最大範圍值，以及變更圖例文字的格式。  
  
 如需詳細資訊，請參閱 [變更地圖圖例、色階與相關的規則 &#40;報表產生器及 SSRS&#41;](../../reporting-services/report-design/change-map-legends-color-scale-and-associated-rules-report-builder-and-ssrs.md)。  
  
## <a name="the-rules-that-i-set-do-not-give-the-results-that-i-expect"></a>我設定的規則沒有得到預期的結果。  
 規則會套用到圖層上與地圖元素相關的分析資料。 使用下列清單協助識別與所有色彩規則、大小規則、寬度規則和標記類型規則相關的問題：  
  
-   將樣式套用到每個地圖元素 (多邊形、線條、點) 的優先順序為，從最低到最高：圖層屬性；圖層上所有地圖元素的地圖元素屬性；您指定的規則；然後是您指定的值 (對於您選取覆寫選項所針對的內嵌地圖元素)。 一旦您為內嵌元素選取覆寫選項之後，即使您稍後將值變更回其原始設定，規則還是不再適用。  
  
-   符合欄位問題。 符合欄位允許在地圖元素和分析資料之間進行資料繫結。 對應到符合欄位的分析資料與空間資料欄位必須擁有相同的資料類型與相同的格式。 如果符合欄位沒有完全符合空間資料與分析資料，規則就沒有作用。 例如，如果空間資料的符合欄位與分析資料的符合欄位相較之下，前者擁有額外的空白或額外的標點符號，則不相符。  
  
-   如需詳細資訊，請參閱 [使用規則與分析資料更改多邊形、線條與點顯示 &#40;報表產生器及 SSRS&#41;](../../reporting-services/report-design/vary-polygon-line-and-point-display-by-rules-and-analytical-data.md)。  
  
## <a name="what-is-the-value-nan-on-the-color-scale"></a>色階上的 NaN 值代表什麼意思？  
 **NaN** 代表「非數字」(Not a Number)。 色階值應為數值。 針對與色階相關的規則，檢查分佈設定與圖例文字值。 如果您已經建立自訂分佈範圍，請確認您在第一個範圍指定下限，並在最後一個範圍指定上限。  
  
## <a name="my-color-scale-does-not-appear-when-i-run-the-report"></a>當我執行報表時，我的色階沒有出現。  
 當地圖圖層針對整個圖層或針對內嵌的地圖元素指定多邊形、線條或點的色彩規則時，色階會對使用者顯示相關資訊。 如果地圖元素沒有指定任何色彩規則，或者色彩規則透過圖例 (而非色彩地圖) 指定，則色彩地圖不會出現在轉譯的報表中。  
  
 若要顯示色階，請針對圖層或內嵌的地圖元素指定色彩規則。 如需詳細資訊，請參閱 [變更地圖圖例、色階與相關的規則 &#40;報表產生器及 SSRS&#41;](../../reporting-services/report-design/change-map-legends-color-scale-and-associated-rules-report-builder-and-ssrs.md)。  
  
##  <a name="tile-issues"></a><a name="Tile"></a> 影像分割問題  
 使用本節協助解決與影像分割背景選項相關的問題。  
  
## <a name="i-cannot-see-the-bing-maps-tile-background"></a>我看不到 Bing 地圖底圖背景。  
 下列設定會影響 Bing Map 圖格背景顯示在本機預覽中，還是顯示在從報表伺服器執行的報表上：  
  
-   地圖底圖圖層必須存在。 在地圖精靈或圖層精靈中，選取 **[加入此地圖檢視的 Bing Maps 背景]** 。 這樣會針對目前的地圖檢視區檢視置中與縮放層級加入圖格圖層。 您也可以從 [地圖] 窗格工具列加入圖格圖層。  
  
-   檢視區的地圖座標系統必須是 **[地理]** ，而非 **[平面]** 。  
  
-   地圖投射必須為 **[Mercator]** 。  
  
-   對於本機預覽，您必須擁有網際網路存取權。 對於從報表伺服器執行的報表，必須將報表伺服器設定為支援影像分割背景。 如需詳細資訊，請參閱[規劃地圖報表](plan-a-map-report-report-builder-and-ssrs.md)。  
  
 如需新增圖格圖層的詳細資訊，請參閱[新增、變更或刪除地圖或地圖圖層 &#40;報表產生器及 SSRS&#41;](../../reporting-services/report-design/add-change-or-delete-a-map-or-map-layer-report-builder-and-ssrs.md)。  
  
## <a name="how-do-i-control-the-text-on-a-tile-layer"></a>如何控制影像分割圖層上的文字？  
 **[道路]** 與 **[混合式]** 檢視都包含文字。 文字是來自 Bing Map Web 服務之影像分割的一部分。  
  
 若要加入沒有文字的影像分割圖層，選取 **[空中]** 檢視。  
  
##  <a name="tooltip-and-label-issues"></a><a name="Tooltip"></a> 工具提示與標籤問題  
 使用本節協助解決與標籤或工具提示選項相關的問題。  
  
## <a name="i-get-an-expression-error-about-dataset-scope-when-i-set-a-label-or-tooltip-to-an-expression"></a>當我將標籤或工具提示設定為運算式時，出現有關資料集範圍的運算式錯誤。  
 當您的空間資料來自地圖庫或 ESRI 形狀檔時，相關的資料不是報表資料集的一部分。 您無法將運算式語法用於資料集欄位參考，來為標籤或工具提示指定這個資料。  
  
 若要指定與非報表資料集一部分之空間資料相關的資料，您必須使用符號 #，後面接著一個指定資料名稱的標籤。  
  
## <a name="see-also"></a>另請參閱  
 [地圖 &#40;報表產生器及 SSRS&#41;](../../reporting-services/report-design/maps-report-builder-and-ssrs.md)   
 [針對報表產生器進行疑難排解](/previous-versions/sql/2014/reporting-services/troubleshoot-report-builder?view=sql-server-2014&preserve-view=true)  
  
  
