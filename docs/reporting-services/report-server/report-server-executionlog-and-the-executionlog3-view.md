---
title: 報表伺服器 ExecutionLog 和 ExecutionLog3 檢視 | Microsoft Docs
description: 了解 Reporting Services 中的報表伺服器執行記錄，其中包含原生模式下或 SharePoint 伺服器陣列中伺服器的報表資訊。
ms.date: 03/01/2017
ms.prod: reporting-services
ms.prod_service: reporting-services-native
ms.technology: report-server
ms.topic: conceptual
helpviewer_keywords:
- logs [Reporting Services], execution
- execution logs [Reporting Services]
ms.assetid: a7ead67d-1404-4e67-97e7-4c7b0d942070
author: maggiesMSFT
ms.author: maggies
ms.openlocfilehash: 28d083e053e31c1fcce26e233ba22211e211a993
ms.sourcegitcommit: 1f826eb3f73bd4d94bc9638b9cdd60991a2e2fa0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98125582"
---
# <a name="report-server-executionlog-and-the-executionlog3-view"></a>報表伺服器執行記錄和 ExecutionLog3 檢視
  [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]報表伺服器執行記錄包含有關在伺服器上執行，或在原生模式向外延展部署或 SharePoint 伺服器陣列中多個伺服器上執行之報表的資訊。 您可以使用報表執行記錄來了解要求報表的頻率、最常使用的輸出格式，以及每一個處理階段所花費處理時間的毫秒數。 此記錄會包含執行報表之資料集查詢所花費時間長度的資訊，以及處理資料所花費的時間。 如果您是報表伺服器管理員，可以檢閱記錄資訊、識別長時間執行工作，並且向報表作者提出有關他們能夠改善之報表區域 (資料集或處理) 的建議。  
  
 設定為 SharePoint 模式的報表伺服器也可以利用 SharePoint ULS 記錄。 如需詳細資訊，請參閱 [開啟 SharePoint 追蹤記錄的 Reporting Services 事件 &#40;ULS&#41;](../../reporting-services/report-server/turn-on-reporting-services-events-for-the-sharepoint-trace-log-uls.md)  
  
##  <a name="viewing-log-information"></a><a name="bkmk_top"></a> 檢視記錄資訊  
 報表伺服器執行會將有關報表執行的資料記錄到內部資料庫資料表中。 您可以從 SQL Server 檢視取得此資料表的資訊。  
  
 報表執行記錄會儲存在預設名為 **ReportServer** 的報表伺服器資料庫中。 SQL 檢視會提供執行記錄資訊。 "2" 和 "3" 檢視是在較新版本中新增，而且包含新欄位或是名稱比舊版更易記的欄位。 舊版檢視仍然保留在產品中，因此相依於這些檢視的自訂應用程式不受影響。 如果您沒有舊版檢視 (例如 ExecutionLog) 的相依性，建議您使用最新檢視 ExecutionLog **3**。  
  
 本主題內容：  
  
-   [SharePoint 模式報表伺服器的組態設定](#bkmk_sharepoint)  
  
-   [原生模式報表伺服器的組態設定](#bkmk_native)  
  
-   [記錄欄位 (ExecutionLog3)](#bkmk_executionlog3)  
  
-   [AdditionalInfo 欄位](#bkmk_additionalinfo)  
  
-   [記錄欄位 (ExecutionLog2)](#bkmk_executionlog2)  
  
-   [記錄欄位 (ExecutionLog)](#bkmk_executionlog)  
  
##  <a name="configuration-settings-for-a-sharepoint-mode-report-server"></a><a name="bkmk_sharepoint"></a> SharePoint 模式報表伺服器的組態設定  
 您可以從 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] 服務應用程式的 [系統設定] 開啟或關閉報表執行記錄。  
  
 根據預設，記錄項目會保留 60 天。 超過此日期的項目會在每天上午 2:00 移除 。 在到期的安裝上，不論何時都只能取得 60 天的資訊。  
  
 您無法針對資料列數目或記錄的項目類型設定限制。  
  
 **若要啟用執行記錄：**  
  
1.  從 SharePoint 管理中心，按一下 [應用程式管理] 群組中的 [管理服務應用程式]。  
  
2.  按一下您想要設定之 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] 服務應用程式的名稱。  
  
3.  按一下 **[系統設定]** 。  
  
4.  選取 [記錄] 區段中的 [啟用執行記錄]。  
  
5.  按一下 [確定]。  
  
 **若要啟用詳細資訊記錄：**  
  
 您必須依照先前步驟的說明啟用記錄，然後完成下列步驟：  
  
1.  在 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] 服務應用程式的 [系統設定] 頁面中，尋找 [使用者定義] 區段。  
  
2.  將 [ExecutionLogLevel] 變更為 [verbose]。 這個欄位是文字輸入欄位，而且兩個可能的值為 [verbose] 和 [normal]。  
  
##  <a name="configuration-settings-for-a-native-mode-report-server"></a><a name="bkmk_native"></a> 原生模式報表伺服器的組態設定  
 您可以從 SQL Server Management Studio 的 [伺服器屬性] 頁面開啟或關閉報表執行記錄。 **EnableExecutionLogging** 是進階屬性。  
  
 根據預設，記錄項目會保留 60 天。 超過此日期的項目會在每天上午 2:00 移除 。 在到期的安裝上，不論何時都只能取得 60 天的資訊。  
  
 您無法針對資料列數目或記錄的項目類型設定限制。  
  
 **若要啟用執行記錄：**  
  
1.  使用系統管理權限來啟動 SQL Server Management Studio。 例如，以滑鼠右鍵按一下 Management Studio 圖示，然後按一下 [以系統管理員身分執行]。  
  
2.  連接到所需的報表伺服器。  
  
3.  以滑鼠右鍵按一下伺服器名稱，然後按一下 [屬性]。 如果 [屬性] 選項已停用，請確認您已使用系統管理權限來啟動 SQL Server Management Studio。  
  
4.  按一下 [記錄] 頁面。  
  
5.  選取 [啟用報表執行記錄]。  
  
 **若要啟用詳細資訊記錄：**  
  
 您必須依照先前步驟的說明啟用記錄，然後完成下列步驟：  
  
1.  在 [伺服器屬性] 對話方塊中，按一下 [進階] 頁面。  
  
2.  在 [使用者定義] 區段中，將 [ExecutionLogLevel] 變更為 [verbose]。 這個欄位是文字輸入欄位，而且兩個可能的值為 [verbose] 和 [normal]。  
  
##  <a name="log-fields-executionlog3"></a><a name="bkmk_executionlog3"></a> 記錄欄位 (ExecutionLog3)  
 這個檢視已在 XML 架構的 **AdditionalInfo** 資料行內加入其他效能診斷節點。 AdditionalInfo 資料行包含的 XML 結構是由 1 至多個其他資訊欄位所組成。 下面是可從 ExecutionLog3 檢視中擷取資料列的範例 Transact SQL 陳述式。 此範例會假設報表伺服器資料庫名為 **ReportServer**：  
  
```  
Use ReportServer  
select * from ExecutionLog3 order by TimeStart DESC  
```  
  
 下表描述在報表執行記錄中擷取的資料。  
  
|資料行|描述|  
|------------|-----------------|  
|InstanceName|處理要求的報表伺服器執行個體名稱。 如果您的環境具有多個報表伺服器，您可以分析 InstanceName 散發以監視並判斷網路負載平衡器是否依照預期方式在報表伺服器之間散發要求。|  
|ItemPath|儲存報表或報表項目的路徑。|  
|UserName|使用者識別碼。|  
|ExecutionID|與要求相關聯的內部識別碼。 相同使用者工作階段上的要求會共用相同的執行識別碼。|  
|RequestType|可能的值如下：<br /><br /> 互動式<br /><br /> 訂用帳戶<br /><br /> <br /><br /> 分析依 RequestType=Subscription 所篩選並且依 TimeStart 所排序的記錄資料可能會顯現訂閱使用量龐大的週期，而且您可能會想要將某些報表訂閱修改成不同的時間。|  
|[格式]|轉譯格式。|  
|參數|報表執行所使用的參數值。|  
|ItemAction|可能的值：<br /><br /> 轉譯<br /><br /> Sort<br /><br /> BookMarkNavigation<br /><br /> DocumentNavigation<br /><br /> GetDocumentMap<br /><br /> Findstring<br /><br /> 執行<br /><br /> RenderEdit|  
|TimeStart|指出報表處理持續期間的開始與結束時間。|  
|TimeEnd||  
|TimeDataRetrieval|擷取資料所花費的毫秒數。|  
|TimeProcessing|處理報表所花費的毫秒數。|  
|TimeRendering|轉譯報表所花費的毫秒數。|  
|來源|報表執行的來源。 可能的值：<br /><br /> 即時<br /><br /> Cache：表示快取的執行作業，例如資料集查詢不會以即時方式執行。<br /><br /> 快照式<br /><br /> 記錄<br /><br /> AdHoc：表示根據鑽研報表且動態產生的報表模型，或者在使用報表伺服器處理與轉譯的用戶端上預覽的報表產生器報表。<br /><br /> Session：表示已建立工作階段內的後續要求。  例如，初始要求是檢視頁面 1，而後續要求則是匯出到 Excel (包含目前的工作階段狀態)。<br /><br /> Rdce：表示報表定義自訂延伸模組。 RDCE 自訂延伸模組可以動態地自訂報表定義，然後在執行報表時將其傳遞至處理引擎。|  
|狀態|狀態 (不是 rsSuccess 就是錯誤碼；如果發生多個錯誤，就只會記錄第一個錯誤)。|  
|ByteCount|轉譯報表的大小 (以位元組為單位)。|  
|RowCount|從查詢傳回的資料列數目。|  
|AdditionalInfo|XML 屬性包，其中包含有關執行的其他資訊。 每個資料列的內容可能都不同。|  
  
##  <a name="the-additionalinfo-field"></a><a name="bkmk_additionalinfo"></a> AdditionalInfo 欄位  
 AdditionalInfo 欄位是一種 XML 屬性包或結構，其中包含有關執行的其他資訊。 記錄中每個資料列的內容可能都不同。  
  
 以下是標準和詳細資訊記錄中，AddtionalInfo 欄位內容的範例：  
  
 **AddtionalInfo 的標準記錄範例**  
  
```  
<AdditionalInfo>  
  <ProcessingEngine>2</ProcessingEngine>  
  <ScalabilityTime>  
    <Pagination>0</Pagination>  
    <Processing>0</Processing>  
  </ScalabilityTime>  
  <EstimatedMemoryUsageKB>  
    <Pagination>0</Pagination>  
    <Processing>6</Processing>  
  </EstimatedMemoryUsageKB>  
  <DataExtension>  
    <SQL>1</SQL>  
  </DataExtension>  
  <Connections>  
    <Connection>  
      <ConnectionOpenTime>147</ConnectionOpenTime>  
      <DataSets>  
        <DataSet>  
          <Name>DataSet1</Name>  
          <RowsRead>16</RowsRead>  
          <TotalTimeDataRetrieval>642</TotalTimeDataRetrieval>  
          <ExecuteReaderTime>63</ExecuteReaderTime>  
        </DataSet>  
        <DataSet>  
          <Name>DataSet2</Name>  
          <RowsRead>3</RowsRead>  
          <TotalTimeDataRetrieval>157</TotalTimeDataRetrieval>  
          <ExecuteReaderTime>60</ExecuteReaderTime>  
        </DataSet>  
      </DataSets>  
    </Connection>  
  </Connections>  
</AdditionalInfo>  
  
```  
  
 **AdditionalInfo 的詳細資訊記錄範例**  
  
```  
<AdditionalInfo>  
  <ProcessingEngine>2</ProcessingEngine>  
  <ScalabilityTime>  
    <Pagination>0</Pagination>  
    <Processing>0</Processing>  
  </ScalabilityTime>  
  <EstimatedMemoryUsageKB>  
    <Pagination>0</Pagination>  
    <Processing>6</Processing>  
  </EstimatedMemoryUsageKB>  
  <DataExtension>  
    <SQL>1</SQL>  
  </DataExtension>  
  <Connections>  
    <Connection>  
      <ConnectionOpenTime>127</ConnectionOpenTime>  
      <DataSource>  
        <Name>DataSource1</Name>  
        <DataExtension>SQL</DataExtension>  
      </DataSource>  
      <DataSets>  
        <DataSet>  
          <Name>DataSet1</Name>  
          <RowsRead>16</RowsRead>  
          <TotalTimeDataRetrieval>655</TotalTimeDataRetrieval>  
          <QueryPrepareAndExecutionTime>94</QueryPrepareAndExecutionTime>  
          <ExecuteReaderTime>33</ExecuteReaderTime>  
          <DataReaderMappingTime>30</DataReaderMappingTime>  
          <DisposeDataReaderTime>1</DisposeDataReaderTime>  
        </DataSet>  
        <DataSet>  
          <Name>DataSet2</Name>  
          <RowsRead>3</RowsRead>  
          <TotalTimeDataRetrieval>16</TotalTimeDataRetrieval>  
          <QueryPrepareAndExecutionTime>2</QueryPrepareAndExecutionTime>  
          <ExecuteReaderTime>1</ExecuteReaderTime>  
          <DataReaderMappingTime>0</DataReaderMappingTime>  
          <DisposeDataReaderTime>0</DisposeDataReaderTime>  
        </DataSet>  
      </DataSets>  
    </Connection>  
  </Connections>  
</AdditionalInfo>  
  
```  
  
 以下是您可以在 AdditionalInfo 欄位中看見的某些值：  
  
-   **ProcessingEngine**  
  
     1= SQL Server 2005，2= 新的視需要處理引擎。 如果大部分報表仍然顯示 1 值，可以調查重新設計報表的方式，讓它們都利用較新且更有效率的視需要處理引擎。  
  
     `<ProcessingEngine>2</ProcessingEngine>`  
  
-   **ScalabilityTime**  
  
     在處理引擎中執行延展相關作業所花費的毫秒數。 0 值表示沒有針對延展作業花費任何額外時間，而且 0 也表示要求沒有承受記憶體不足的壓力。  
  
    ```  
    <ScalabilityTime>  
        <Processing>0</Processing>  
    </ScalabilityTime>  
    ```  
  
-   **EstimatedMemoryUsageKB**  
  
     在特定要求期間，每個元件所耗用之記憶體尖峰數量的估計值 (以 KB 為單位)。  
  
    ```  
    <EstimatedMemoryUsageKB>  
        <Processing>38</Processing>  
    </EstimatedMemoryUsageKB>  
    ```  
  
-   **DataExtension**  
  
     用於報表之資料延伸模組或資料來源的類型。 其數字為特定資料來源的出現次數。  
  
    ```  
    <DataExtension>  
       <DAX>2</DAX>  
    </DataExtension>  
    ```  
  
-   **ExternalImages**  
  
     此值會以毫秒來表示。 此資料可用來診斷效能問題。 從外部 Web 伺服器擷取影像所需的時間可能會讓整體報表執行變慢。  
  
    ```  
    <ExternalImages>  
        <Count>3</Count>  
        <ByteCount>9268</ByteCount>  
        <ResourceFetchTime>9</ResourceFetchTime>  
    </ExternalImages>  
    ```  
  
-   **連線**  
  
     多層結構  
  
    ```  
    <Connections>  
        <Connection>  
          <ConnectionOpenTime>127</ConnectionOpenTime>  
          <DataSource>  
            <Name>DataSource1</Name>  
            <DataExtension>SQL</DataExtension>  
          </DataSource>  
          <DataSets>  
            <DataSet>  
              <Name>DataSet1</Name>  
              <RowsRead>16</RowsRead>  
              <TotalTimeDataRetrieval>655</TotalTimeDataRetrieval>  
              <QueryPrepareAndExecutionTime>94</QueryPrepareAndExecutionTime>  
              <ExecuteReaderTime>33</ExecuteReaderTime>  
              <DataReaderMappingTime>30</DataReaderMappingTime>  
              <DisposeDataReaderTime>1</DisposeDataReaderTime>  
            </DataSet>  
            <DataSet>  
              <Name>DataSet2</Name>  
              <RowsRead>3</RowsRead>  
              <TotalTimeDataRetrieval>16</TotalTimeDataRetrieval>  
              <QueryPrepareAndExecutionTime>2</QueryPrepareAndExecutionTime>  
              <ExecuteReaderTime>1</ExecuteReaderTime>  
              <DataReaderMappingTime>0</DataReaderMappingTime>  
              <DisposeDataReaderTime>0</DisposeDataReaderTime>  
            </DataSet>  
          </DataSets>  
        </Connection>  
    </Connections>  
  
    ```  
  
##  <a name="log-fields-executionlog2"></a><a name="bkmk_executionlog2"></a> 記錄欄位 (ExecutionLog2)  
 這個檢視加入了一些新欄位並且重新命名了一些其他欄位。 下面是可從 ExecutionLog2 檢視中擷取資料列的範例 Transact SQL 陳述式。 此範例會假設報表伺服器資料庫名為 **ReportServer**：  
  
```  
Use ReportServer  
select * from ExecutionLog2 order by TimeStart DESC  
```  
  
 下表描述在報表執行記錄中擷取的資料。  
  
|資料行|描述|  
|------------|------------------------------------------------------------|  
|InstanceName|處理要求的報表伺服器執行個體名稱。|  
|ReportPath|報表的路徑結構。 儲存在根資料夾中名為 "test" 的轉貼包含 "/test" 的 ReportPath。<br /><br /> 儲存在資料夾 "samples" 中名為 "test" 的報表，可能包含 "/Samples/test/" 的 ReportPath|  
|UserName|使用者識別碼。|  
|ExecutionID||  
|RequestType|要求類型 (使用者或系統)。|  
|[格式]|轉譯格式。|  
|參數|報表執行所使用的參數值。|  
|ReportAction|可能的值：Render、Sort、BookMarkNavigation、DocumentNavigation、GetDocumentMap、Findstring|  
|TimeStart|指出報表處理持續期間的開始時間。|
|TimeEnd|指出報表處理持續期間的結束時間。|
|TimeDataRetrieval|擷取資料所花費的毫秒數。|
|TimeProcessing|處理報表所花費的毫秒數。|
|TimeRendering|轉譯報表所花費的毫秒數。|
|來源|報表執行的來源 (1= 即時、2= 快取、3= 快照集、4= 記錄)。|  
|狀態|狀態 (不是 rsSuccess 就是錯誤碼；如果發生多個錯誤，就只會記錄第一個錯誤)。|  
|ByteCount|轉譯報表的大小 (以位元組為單位)。|  
|RowCount|從查詢傳回的資料列數目。|  
|AdditionalInfo|XML 屬性包，其中包含有關執行的其他資訊。|  
  
##  <a name="log-fields-executionlog"></a><a name="bkmk_executionlog"></a> 記錄欄位 (ExecutionLog)  
 下面是可從 ExecutionLog 檢視中擷取資料列的範例 Transact SQL 陳述式。 此範例會假設報表伺服器資料庫名為 **ReportServer**：  
  
```  
Use ReportServer  
select * from ExecutionLog order by TimeStart DESC  
  
```  
  
 下表描述在報表執行記錄中擷取的資料。  
  
|資料行|描述|  
|------------|-----------------|  
|InstanceName|處理要求的報表伺服器執行個體名稱。|  
|ReportID|報表識別碼。|  
|UserName|使用者識別碼。|  
|RequestType|可能的值：<br /><br /> True= 訂閱要求<br /><br /> False= 互動式要求|  
|[格式]|轉譯格式。|  
|參數|報表執行所使用的參數值。|  
|TimeStart|指出報表處理持續期間的開始與結束時間。|  
|TimeEnd||  
|TimeDataRetrieval|擷取資料、處理報表和轉譯報表所花費的毫秒數。|  
|TimeProcessing||  
|TimeRendering||  
|來源|報表執行的來源。 可能的值：(1= 即時、2= 快取、3= 快照集、4= 歷程記錄、5= 特定、6= 工作階段、7= RDCE)。|  
|狀態|可能的值如下：rsSuccess、rsProcessingAborted 或錯誤碼。 如果發生多個錯誤，只會記錄第一個錯誤。|  
|ByteCount|轉譯報表的大小 (以位元組為單位)。|  
|RowCount|從查詢傳回的資料列數目。|  
  
## <a name="see-also"></a>另請參閱  
 [開啟 SharePoint 追蹤記錄的 Reporting Services 事件 &#40;ULS&#41;](../../reporting-services/report-server/turn-on-reporting-services-events-for-the-sharepoint-trace-log-uls.md)   
 [Reporting Services 記錄檔和來源](../../reporting-services/report-server/reporting-services-log-files-and-sources.md)   
 [錯誤和事件參考 &#40;Reporting Services&#41;](../../reporting-services/troubleshooting/errors-and-events-reference-reporting-services.md)  
  
  
