---
description: 使用 DQS (內部) 知識清理資料
title: 使用 DQS (內部) 知識清理資料
ms.date: 03/01/2017
ms.prod: sql
ms.prod_service: data-quality-services
ms.reviewer: ''
ms.technology: data-quality-services
ms.topic: conceptual
f1_keywords:
- sql13.dqs.dqproject.interactivecleansing.f1
- sql13.dqs.dqproject.map.f1
- sql13.dqs.dqproject.correction.f1
- sql13.dqs.dqproject.export.f1
ms.assetid: c96b13ad-02a6-4646-bcc7-b4a8d490f5cc
author: swinarko
ms.author: sawinark
ms.openlocfilehash: ee9c9e746dcc85e80ae96a7d04a84b12c594e98c
ms.sourcegitcommit: e700497f962e4c2274df16d9e651059b42ff1a10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/17/2020
ms.locfileid: "88449949"
---
# <a name="cleanse-data-using-dqs-internal-knowledge"></a>使用 DQS (內部) 知識清理資料

[!INCLUDE [SQL Server - Windows only ASDBMI  ](../includes/applies-to-version/sqlserver.md)]

  此主題描述如何在 [!INCLUDE[ssDQSnoversion](../includes/ssdqsnoversion-md.md)] (DQS) 中使用資料品質專案來清理資料。 資料清理是使用已針對高品質資料集內建在 DQS 中的知識庫，於來源資料上執行。 如需詳細資訊，請參閱 [建立知識庫](../data-quality-services/building-a-knowledge-base.md)。  
  
 資料清理會在四個階段執行： *對應* 階段 (您會在此階段識別要清理的資料來源、並將其對應到知識庫中的必要定義域)、 *電腦輔助清理* 階段 (DQS 會將知識庫套用到要清理的資料，並提議/進行來源資料的變更)、 *互動式清理* 階段 (資料監管可以分析資料變更，並接受/拒絕資料變更) 以及最後的 *匯出* 階段 (此階段可讓您匯出已清理的資料)。 以上每一個程序都是在清理活動精靈的個別頁面中執行，好讓您來回移到不同的頁面、重新執行此程序，並退出特定清理程序，然後返回該程序的相同階段。 DQS 會提供有關來源資料和清理結果的統計資料，好讓您做出有關資料清理的明智決定。  
  
## <a name="before-you-begin"></a>開始之前  
  
###  <a name="prerequisites"></a><a name="Prerequisites"></a> 必要條件  
  
-   您必須為清理活動指定適當的臨界值。 如需有關執行此作業的詳細資訊，請參閱＜ [設定清理和比對的臨界值](../data-quality-services/configure-threshold-values-for-cleansing-and-matching.md)＞。  
  
-   DQS 知識庫必須在您想要進行比較及清理來源資料的 [!INCLUDE[ssDQSServer](../includes/ssdqsserver-md.md)] 上提供。 此外，知識庫必須包含您想要清理之資料類型的相關知識。 例如，如果您想要清理包含美國地址的來源資料，您必須擁有已針對「高品質」美國地址取樣資料建立的知識庫。  
  
-   如果要清理的來源資料為 Excel 檔案，則 Microsoft Excel 必須安裝在 [!INCLUDE[ssDQSClient](../includes/ssdqsclient-md.md)] 電腦上。 否則，您將無法在對應階段選取此 Excel 檔案。 由 Microsoft Excel 建立的檔案可以具有 .xlsx、.xls 或 .csv 的副檔名。 如果使用 64 位元版本的 Excel，則僅支援 Excel 2003 檔案 (.xls)；Excel 2007 或 2010 檔案 (.xlsx) 不受支援。 如果您使用 64 位元版本的 Excel 2007 或 2010，請將檔案儲存為 .xls 檔案或 .csv 檔案，或是改為安裝 32 位元版本的 Excel。  
  
###  <a name="security"></a><a name="Security"></a> Security  
  
####  <a name="permissions"></a><a name="Permissions"></a> 權限  
 您必須擁有 DQS_MAIN 資料庫的 dqs_kb_editor 或 dqs_kb_operator 角色，才能執行資料清理。  
  
##  <a name="create-a-cleansing-data-quality-project"></a><a name="Create"></a> 建立清理資料品質專案  
 您必須使用資料品質專案，才能執行資料清理作業。 若要建立清理資料品質專案：  
  
1.  依照＜ [建立資料品質專案](../data-quality-services/create-a-data-quality-project.md)＞主題中的步驟 1-3 進行。  
  
2.  在步驟 3.d 選取 **[清理]** 活動。  
  
3.  按一下 **[建立]** ，建立清理資料品質專案。  
  
 這樣會建立清理資料品質專案，並開啟清理資料品質精靈的 **[對應]** 頁面。  
  
##  <a name="mapping-stage"></a><a name="Mapping"></a> 對應階段  
 在對應階段中，您會指定要清理之來源資料的連接，以及將來源資料中的資料行對應至選取之知識庫中的適當定義域。  
  
1.  在清理資料品質精靈的 **[對應]** 頁面上，選取要清理的來源資料： **[SQL Server]** 或 **[Excel 檔案]**：  
  
    1.  **SQL Server**：如果您已將來源資料複製到這個資料庫，請選取 **DQS_STAGING_DATA** 當做來源資料庫，然後選取包含來源資料的適當資料表/檢視表。 否則，請選取來源資料庫及適當的資料表/檢視表。 您的來源資料庫必須與 [!INCLUDE[ssDQSServer](../includes/ssdqsserver-md.md)] 位於相同的 SQL Server 執行個體上，才會在 **[資料庫]** 下拉式清單中提供。  
  
    2.  **Excel 檔案**：按一下 **[瀏覽]**，並選取包含要清理之資料的 Excel 檔案。 [!INCLUDE[ssDQSClient](../includes/ssdqsclient-md.md)] 電腦上必須安裝 Microsoft Excel，才能選取 Excel 檔案。 否則，將無法使用 **[瀏覽]** 按鈕，而且這個文字方塊下方會通知您尚未安裝 Microsoft Excel。 此外，如果 Excel 檔案的第一個資料列包含標頭資料，請繼續選取 **[使用第一個資料列做為標頭]** 核取方塊。  
  
2.  若要在 **[對應]** 底下將來源資料中的資料行與知識庫中的適當定義域對應，請在 **[來源資料行]** 資料行的下拉式清單中選取來源資料行，然後從相同資料列的 **[定義域]** 資料行的下拉式清單中選取定義域。 重複以上步驟，將來源資料中的所有資料行對應至知識庫中的適當定義域。 您可以視需要按一下 **[加入資料行對應]** 圖示，將資料列加入至對應資料表。  
  
    > [!NOTE]  
    >  只有當 DQS 支援來源資料類型，而且該類型符合 DQS 定義域資料類型時，您才能將來源資料對應至 DQS 定義域，以便執行資料清理。 如需有關支援之來源資料類型的詳細資訊，請參閱＜ [DQS 定義域支援的 SQL Server 和 SSIS 資料類型](../data-quality-services/supported-sql-server-and-ssis-data-types-for-dqs-domains.md)＞。  
  
3.  按一下 **[預覽資料來源]** 圖示查看您選取之 SQL Server 資料表或檢視表中的資料，或者您選取的 Excel 工作表。  
  
4.  按一下 **[檢視/選取複合定義域]** 檢視對應至來源資料行的複合定義域清單。 只有當您至少有一個複合定義域對應至來源資料行時，才可使用這個按鈕。  
  
5.  按 **[下一步]** ，繼續前進到電腦輔助的清理階段 (**[清理]** 頁面)。  
  
##  <a name="computer-assisted-cleansing-stage"></a><a name="ComputerAssisted"></a> 電腦輔助的清理階段  
 在電腦輔助的清理階段中，您會執行自動資料清理程序，針對知識庫中的對應定義域來分析來源資料，以及執行/建議資料變更。  
  
1.  在資料品質精靈的 **[清理]** 頁面上，按一下 **[啟動]** ，執行電腦輔助的清理程序。 DQS 會根據為了針對選取的知識庫分析及清理資料所指定的臨界值層級，使用進階演算法與信賴等級。 如需電腦輔助清理程序如何在 DQS 中進行的詳細資訊，請參閱[資料清理](../data-quality-services/data-cleansing.md)中的[電腦輔助的清理](../data-quality-services/data-cleansing.md#ComputerAssisted)。  
  
    > [!IMPORTANT]  
    >  -   資料分析完成後， **[啟動]** 按鈕將變為 **[重新啟動]** 按鈕。 如果上一次分析的結果尚未儲存，按一下 **[重新啟動]** 將會遺失之前的資料。 執行分析時請勿離開頁面，否則分析程序將會終止。  
    > -   如果在建立清理專案之後，用於清理專案的知識庫已更新及發行，按一下 **[啟動]** 會提示您是否要將最新的知識庫用於清理。 如果您已經使用知識庫建立資料品質專案、按一下 **[關閉]** 關閉中途的清理中專案，然後之後重新開啟資料品質專案來執行清理，通常會發生這個情況。 在這期間，用於清理專案的知識庫已經更新及發行。  
    >   
    >      同樣地，如果上次執行電腦輔助的清理之後，用於清理專案的知識庫已更新及發行，按一下 **[重新啟動]** 會提示您是否要將最新的知識庫用於清理。  
    >   
    >      在這兩種情況下，按一下 **[是]** 可將更新的知識庫用於電腦輔助的清理。 此外，如果目前的對應與更新的知識庫之間有任何衝突 (例如定義域已刪除或是定義域資料類型已變更)，此訊息也會提示您修正目前的對應來使用更新過的知識庫。 按一下 **[是]** 會帶您前往 **[對應]** 頁面，您可以在這裡修正對應，然後再繼續進行電腦輔助的清理。  
  
2.  在電腦輔助的清理階段，您可以按一下 **[分析工具]** 索引標籤在分析工具上切換，以檢視即時資料分析與通知。 如需詳細資訊，請參閱 [分析工具統計資料](#Profiler)。  
  
3.  如果您對結果不滿意，請按一下 **[上一步]** 返回 **[對應]** 頁面、視需要修改一個或多個對應、返回 **[清理]** 頁面，然後按一下 **[重新啟動]**。  
  
4.  在完成電腦輔助的清理程序之後，按 **[下一步]** 繼續前往互動式清理階段 (**[管理和檢視結果]** 頁面)。  
  
##  <a name="interactive-cleansing-stage"></a><a name="Interactive"></a> 互動式清理階段  
 在互動式清理階段中，您可以查看 DQS 所建議的變更，然後決定是否要核准或拒絕變更來實作變更。 在 **[管理和檢視結果]** 頁面的左窗格上，DQS 會顯示您之前在對應階段所對應的所有定義域清單，以及在電腦輔助的清理階段針對每一個定義域分析之來源資料中的值數目。 在 **[管理和檢視結果]** 頁面的右窗格上，根據是否遵循定義域規則、語法錯誤規則和進階演算法，DQS 會使用 *信賴等級*將資料分類在五個索引標籤之下。 信賴等級會指示 DQS 針對更正或建議的確信程度，而且會根據以下臨界值：  
  
-   **自動更正臨界值**：DQS 會自動更正信賴等級高於此臨界值的任何值。 不過，資料監管在互動式清理程序期間可以覆寫此變更。 您可以在 **[組態]** 畫面的 **[一般設定]** 索引標籤中，指定自動更正臨界值。 如需詳細資訊，請參閱 [設定清理和比對的臨界值](../data-quality-services/configure-threshold-values-for-cleansing-and-matching.md)。  
  
-   **自動建議臨界值**：針對信賴等級高於此臨界值但低於自動更正臨界值的任何值，建議取代值。 只有在資料監管核准時，DQS 才會進行變更。 您可以在 **[組態]** 畫面的 **[一般設定]** 索引標籤中，指定自動建議臨界值。 如需詳細資訊，請參閱 [設定清理和比對的臨界值](../data-quality-services/configure-threshold-values-for-cleansing-and-matching.md)。  
  
-   **其他**：DQS 將低於自動建議臨界值的任何值保持不變。  
  
 根據信賴等級，這些值會顯示在下列五個索引標籤之下：  
  
|索引標籤|描述|  
|---------|-----------------|  
|**建議**|顯示以下情況的定義域值：DQS 找到信賴等級高於 *自動建議臨界值* ，但低於 *自動更正臨界值* 的建議值。<br /><br /> 建議值會針對原始值顯示在 **[更正為]** 資料行中。 您可以在上方方格中，針對某個值按一下 **[核准]** 或 **[拒絕]** 資料行中的選項按鈕，以接受或拒絕該值所有出現地方的建議。 在此情況下，接受的值會移到 **[更正]** 索引標籤，而拒絕的值則會移到 **[無效]** 索引標籤。|  
|**新增**|顯示 DQS 沒有足夠資訊的有效網域，因此無法對應至其他任何索引標籤。此外，此索引標籤也包含信賴等級低於 *自動建議臨界* 值的值，但夠高，足以標示為有效。<br /><br /> 如果您認為此值是正確的，請按一下 **[核准]** 資料行中的選項按鈕。 否則，請按一下 **[拒絕]** 資料行中的選項按鈕。 接受的值會移至 **正確** 的索引標籤，而拒絕的值則會移到 [ **無效** ] 索引標籤。您也可以針對值以手動方式輸入正確的值來取代 [ **更正為** ] 資料行中的原始值，然後按一下 [ **核准** ] 欄中的選項按鈕，以接受變更。 在此情況下，此值會移到 **[更正]** 索引標籤。|  
|**無效**|顯示在知識庫定義域中標示為無效的定義域值，或不符合定義域規則的值。 這個索引標籤也包含使用者在其他四個索引標籤的任何一個所拒絕的值。<br /><br /> 但是，如果您認為此值是正確的，請按一下 **[核准]** 資料行中的選項按鈕。 接受的值會移至 **正確** 的索引標籤。您也可以針對值以手動方式輸入正確的值來取代 [ **更正為** ] 資料行中的原始值，然後按一下 [ **核准** ] 欄中的選項按鈕，以接受變更。 在此情況下，此值會移到 **[更正]** 索引標籤。|  
|**糾正**|顯示 DQS 在自動清理程序期間更正的定義域值 (當 DQS 以高於自動更正臨界值的信賴等級找到值的更正時)。<br /><br /> 更正值會針對原始值顯示在 **[更正為]** 資料行中。 預設會針對此值選取 **[核准]** 資料行中的選項按鈕。 如果需要，您可以拒絕建議的更正，方法是按一下 **[拒絕]** 資料行中的選項按鈕，將其移到 **[無效]** 索引標籤，或是在 **[更正為]** 資料行中手動輸入正確值，然後按一下 **[核准]** 資料行中的選項按鈕接受變更，並將其移到 **[更正]** 索引標籤。|  
|**正確**|顯示已發現正確的定義域值。 例如，值符合定義域值。 此索引標籤也包含使用者按一下 **[新增]** 和 **[無效]** 索引標籤中， **[核准]** 資料行內的選項按鈕所核准的值。<br /><br /> 預設會針對每一個值選取 **[核准]** 資料行中的選項按鈕。 但是，如果您認為這個索引標籤中的值不正確，您可以針對此值按一下 **[拒絕]** 資料行中的選項按鈕，將其移到 **[無效]** 索引標籤，或是在 **[更正為]** 資料行中針對此值手動輸入正確值來取代此值，然後按一下 **[核准]** 資料行中的選項按鈕接受變更，並將其移到 **[更正]** 索引標籤。|  
  
 若要以互動方式清理資料：  
  
1.  在清理資料品質精靈的 **[管理和檢視結果]** 頁面中，按一下左窗格的定義域名稱。  
  
2.  檢閱這五個索引標籤之下的定義域值，並依照先前解釋的內容採取適當的動作。  
  
    -   右上方窗格會針對選取之定義域中的每一個值顯示以下資訊：原始值、出現的次數 (記錄數)、指定另一個 (正確) 值的方塊、信賴等級 (不適用於 **[正確]** 索引標籤底下的值)、針對此值採取 DQS 動作的理由，以及核准和拒絕此值之更正與建議的選項。  
  
        > [!TIP]  
        >  您可以在右上方窗格中核准或拒絕選取之定義域中的所有值，方法是分別按一下 **[核准所有詞彙]** 或 **[拒絕所有詞彙]** 圖示。 另外，您也可以用滑鼠右鍵按一下選取之定義域中的值，並在快速鍵功能表中按一下 **[全部接受]** 或 **[全部拒絕]** 。  
  
    -   下方窗格會顯示右上方窗格中選取之定義域值的個別出現次數。 顯示以下資訊：指定另一個 (正確) 值的方塊、信賴等級 (不適用於 **[正確]** 索引標籤底下的值)、針對此值採取 DQS 動作的理由、核准和拒絕此值之更正與建議的選項，以及原始值。  
  
3.  如果您在建立定義域時，以啟用此定義域的 **[拼字檢查]** 功能，將會針對識別為潛在錯誤的這類定義域值顯示波浪式紅色底線。 整個值都會顯示此底線。 例如，如果 "New York" 錯誤地拼寫為 "Neu York"，拼字檢查將會在 "Neu York" 底下顯示紅色底線 (而不只是在 "Neu" 底下顯示)。 如果您以滑鼠右鍵按一下此值，您會看到建議的更正。 如果有 5 個以上的建議，您可以在內容功能表中按一下 **[其他建議]** ，檢視其餘的建議。 至於錯誤顯示方面，這些建議會取代整個值。 例如，"New York" 在上一個範例中將會顯示為建議，而不只是 "New"。 您可以選擇其中一項建議，或將值添加到該值所要顯示的字典中。 值會在使用者帳戶層級儲存在字典中。 當您從 [拼字檢查] 內容功能表選取建議時，選取的建議將會加入至 **[更正為]** 資料行。 但是，如果您在 **[更正為]** 資料行中選取建議，選取的建議會取代此資料行中的值。  
  
     預設會在互動式清理階段啟用拼字檢查功能。 您可以在互動式清理階段停用拼字檢查，方法是按一下 **[啟用/停用拼字檢查]** 圖示，或是以滑鼠右鍵按一下定義域值區域，然後在快速鍵功能表中按一下 **[拼字檢查]** 。 若要再次啟用它，請執行相同的動作。  
  
    > [!NOTE]  
    >  拼字檢查功能只能在上方窗格使用 (定義域值)。 此外，您不能啟用或停用複合定義域的拼字檢查。 複合定義域中具有字串類型而且已啟用拼字檢查功能的子定義域預設將會在互動式清理階段啟用拼字檢查功能。  
  
4.  在互動式清理階段，您可以按一下 **[分析工具]** 索引標籤在分析工具上切換，以檢視即時資料分析與通知。 如需詳細資訊，請參閱 [分析工具統計資料](#Profiler)。  
  
5.  在您檢閱所有定義域值之後，請按 **[下一步]** 繼續前往匯出階段。  
  
##  <a name="export-stage"></a><a name="Export"></a> 匯出階段  
 在匯出階段，您會指定用來匯出清理資料的參數：要匯出的項目和地方。  
  
1.  在清理資料品質精靈的 **[匯出]** 頁面上，選取匯出清理資料的目的地類型： **[SQL Server]**、 **[CSV 檔案]** 或 **[Excel 檔案]**。  
  
    > [!IMPORTANT]  
    >  如果您要使用 64 位元版本的 Excel，就無法將清理的資料匯出到 Excel 檔案，只能匯出到 SQL Server 資料庫或 .csv 檔案。  
  
    1.  **SQL Server**：如果您想要在這裡匯出資料，然後指定將建立來儲存匯出之資料的資料表名稱，請選取 **DQS_STAGING_DATA** 當做目的地資料庫。 否則，如果您想要將資料匯出到另一個資料庫，請選取另一個資料庫，然後指定將建立來儲存匯出之資料的資料表名稱。 您的目的地資料庫必須與 [!INCLUDE[ssDQSServer](../includes/ssdqsserver-md.md)] 位於相同的 SQL Server 執行個體上，才會在 **[資料庫]** 下拉式清單中提供。  
  
    2.  **CSV 檔案**：按一下 **[瀏覽]**，並指定您想要匯出清理資料之目的 .csv 檔案的名稱和位置。 您也可以輸入您想要匯出清理資料之目的 .csv 檔案的名稱，連同完整路徑。 例如，"c:\ExportedData.csv"。 此檔案會儲存在安裝 [!INCLUDE[ssDQSServer](../includes/ssdqsserver-md.md)] 的電腦上。  
  
    3.  **Excel 檔案**：按一下 **[瀏覽]**，並指定您想要匯出清理資料之目的 Excel 檔案的名稱和位置。 您也可以輸入您想要匯出清理資料之目的 Excel 檔案的名稱，連同完整路徑。 例如，"c:\ExportedData.xlsx"。 此檔案會儲存在安裝 [!INCLUDE[ssDQSServer](../includes/ssdqsserver-md.md)] 的電腦上。  
  
2.  選取 **[標準化輸出]** 核取方塊，根據為定義域選取的輸出格式來標準化輸出。 例如，將字串值變更為大寫，或是將單字的第一個字母變成大寫。 如需有關指定定義域之輸出格式的詳細資訊，請參閱＜ **設定定義域屬性** ＞中的 [[設定輸出格式為]](../data-quality-services/set-domain-properties.md)清單。  
  
3.  接下來，選取資料輸出：只匯出清理過的資料，或是匯出清理過的資料以及清理中的資訊。  
  
    -   **僅限資料**：按一下此選項按鈕，只匯出清理過的資料。  
  
    -   **資料和清理資訊**：按一下此選項按鈕，匯出每一個定義域的以下資料：  
  
        -   ** \<Domain> _Source**：定義域中的原始值。  
  
        -   ** \<Domain> _Output**：定義域中的清理值。  
  
        -   ** \<Domain> _Reason**：為值更正指定的原因。  
  
        -   ** \<Domain> _Confidence**：已更正之所有詞彙的信賴等級。 顯示為小數值，相當於對應的百分比值。 例如，95% 的信賴等級將會顯示為 .9500000。  
  
        -   ** \<Domain> _Status**：資料清理之後之定義域值的狀態。 例如， **[建議]**、 **[新增]**、 **[無效]**、 **[更正]** 或 **[正確]**。  
  
        -   **記錄狀態**：除了每個對應的定義域都有 [狀態] 欄位 ** (\<DomainName> _Status**) ，[ **記錄狀態** ] 欄位也會顯示記錄的狀態。 如果記錄中任何定義域的狀態是 [ *新增* ] 或 [ *正確*]，則 [ **記錄狀態** ] 會設為 [ *正確*]。 如果記錄中任何定義域的狀態是 [ *建議*]、[ *無效*] 或 [已 *更正*]，則 [ **記錄狀態** ] 會設為各自的值。 例如，如果記錄中有任何網域的狀態是 *建議*的，則 [ **記錄狀態** ] 會設為 [ *建議*]。  
  
            > [!NOTE]  
            >  如果您針對清理作業使用參考資料服務，也可使用有關此定義域值的一些其他資料進行匯出。 如需詳細資訊，請參閱[使用參考資料 &#40;外部&#41; 知識清理資料](../data-quality-services/cleanse-data-using-reference-data-external-knowledge.md)。  
  
4.  按一下 **[匯出]** ，將資料匯出至選取的資料目的地。 如果您選取：  
  
    -   **[SQL Server]** 做為資料目的地，則會在選取的資料庫中建立具有指定之名稱的新資料表。  
  
    -   **[CSV 檔案]** 做為資料目的地，則會在 [!INCLUDE[ssDQSServer](../includes/ssdqsserver-md.md)] 電腦位置上，以先前在 **[CSV 檔案名稱]** 方塊中指定的檔案名稱來建立 .csv 檔案。  
  
    -   **[Excel 檔案]** 做為資料目的地，則會在 [!INCLUDE[ssDQSServer](../includes/ssdqsserver-md.md)] 電腦位置上，以先前在 **[Excel 檔案名稱]** 方塊中指定的檔案名稱來建立 Excel 檔案。  
  
5.  按一下 **[完成]** ，關閉資料品質專案。  
  
##  <a name="profiler-statistics"></a><a name="Profiler"></a> Profiler 統計資料  
 [ **分析** 工具] 索引標籤會提供指示來源資料品質的統計資料。 資料分析會幫助您評估資料清理活動的效用，您也可以判斷資料清理幫助提升資料品質的程度。  
  
 **[分析工具]** 索引標籤會提供來源資料適用的以下統計資料 (依據欄位和定義域)：  
  
-   **記錄**：已針對資料清理活動分析資料取樣中的多少筆記錄  
  
-   **正確記錄**：被發現正確的記錄數目  
  
-   **更正的記錄**：已更正的記錄數目  
  
-   **建議的記錄**：建議的記錄數目  
  
-   **無效的記錄**：無效的記錄數目  
  
 欄位統計資料包括以下項目：  
  
-   **欄位**：欄位在來源資料中的名稱  
  
-   **定義域**：對應至欄位的定義域名稱  
  
-   **更正的值**：已更正的定義域值數目  
  
-   **建議的值**：建議的定義域值數目  
  
-   **完整性**：針對清理活動所對應之每一個來源欄位的完整性  
  
-   **精確度**：針對清理活動所對應之每一個來源欄位的精確度  
  
 DQS 分析會提供兩個資料品質維度： *完整性* (資料存在的程度) 和 *精確度* (可將資料用於預定用途的程度)。 如果分析告訴您某個欄位相對不完整，您可能會想要從資料品質專案的知識庫中將其移除。 分析可能不會針對複合定義域提供可靠的完整性統計資料。 如果您需要完整性統計資料，請使用單一定義域，而非複合定義域。 如果您想要使用複合定義域，您可能會想要使用分析用的單一定義域來建立一個知識庫以判斷完整性，並使用清理程序所用的複合定義域來建立另一個定義域。 例如，分析可能會針對使用複合定義域的位址記錄顯示 95% 完整性，但是其中一個資料行可能會有更高層級的不完整性，例如郵遞區號 (zip) 資料行。 在此範例中，您可能會想要使用單一定義域衡量郵遞區號資料行的完整性。 分析可能會針對複合定義域提供可靠的精確度統計資料，因為您可以一起衡量多個資料行的精確度。 此資料的值位於複合彙總中，所以您可能會想要使用複合定義域衡量精確度。  
  
 如果您未使用參考資料服務，精確度統計資料可能需要更多解譯。 如果您針對資料清理使用參考資料服務，您對於精確度統計資料會有某個程度的信任。 如需使用 Reference Data Service 清理資料的詳細資訊，請參閱[使用參考資料 &#40;外部&#41; 知識清理資料](../data-quality-services/cleanse-data-using-reference-data-external-knowledge.md)。  
  
### <a name="cleansing-notifications"></a>清理通知  
 以下情況會產生通知：  
  
-   欄位沒有更正或建議。 您可能會想要從對應中加以移除、先執行知識探索，或是使用另一個知識庫。  
  
-   欄位的更正或建議相當少。 您可能會想要從對應中加以移除、先執行知識探索，或是使用另一個知識庫。  
  
-   欄位的精確度非常低。 您可能會想要驗證對應，或是先考慮執行知識探索。  
  
 如需有關分析的詳細資訊，請參閱＜ [DQS 中的資料分析與通知](../data-quality-services/data-profiling-and-notifications-in-dqs.md)＞。  
  
  
