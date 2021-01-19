---
description: SQL Server 2016 Integration Services 的新功能
title: SQL Server 2016 Integration Services 的新功能 | Microsoft Docs
ms.custom:
- SQL2016_New_Updated
ms.date: 09/28/2017
ms.prod: sql
ms.prod_service: integration-services
ms.reviewer: ''
ms.technology: integration-services
ms.topic: conceptual
helpviewer_keywords:
- Integration Services, what's new
- what's new [Integration Services]
ms.assetid: da6999c7-e5e3-4a59-a284-1da635995af1
author: chugugrace
ms.author: chugu
ms.openlocfilehash: 838b01e2fd4f09b787b11f146143fc6c327504b7
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98172210"
---
# <a name="what39s-new-in-integration-services-in-sql-server-2016"></a>SQL Server 2016 Integration Services 的新功能

[!INCLUDE[sqlserver-ssis](../includes/applies-to-version/sqlserver-ssis.md)]



本主題描述 SQL Server 2016 [!INCLUDE[ssISnoversion](../includes/ssisnoversion-md.md)] 中已新增或更新的功能。 它也包含 [Azure Feature Pack for Integration Services & #40;SSIS & #41;](../integration-services/azure-feature-pack-for-integration-services-ssis.md) 在SQL Server 2016 期間新增或更新的功能。  

## <a name="new-for-ssis-in-azure-data-factory"></a>Azure Data Factory 中的 SSIS 新功能

使用 2017 年 9 月 Azure Data Factory 第 2 版的公開預覽，您現在可以執行下列動作：
-   將套件部署至 Azure SQL Database 上的 SSIS 目錄資料庫 (SSISDB)。
-   在 Azure-SSIS Integration Runtime (即 Azure Data Factory 第 2 版的元件) 上執行部署至 Azure 的套件。

如需詳細資訊，請參閱[將 SQL Server Integration Services 工作負載隨即轉移至雲端](lift-shift/ssis-azure-lift-shift-ssis-packages-overview.md)。

這些新功能需要 SQL Server Data Tools (SSDT) 17.2 版或更新版本，但不需要 SQL Server 2017 或 SQL Server 2016。 當您將套件部署至 Azure 時，[套件部署精靈] 一律會將套件升級至最新套件格式。

## <a name="2016-improvements-by-category"></a>2016 版的改進 (依類別)  
  
-   **管理能力**  
  
    -   更容易部署  
  
        -   [SSISDB 升級精靈](#ssisdbupgrwiz)  
  
        -   [支援 SSIS 目錄中的AlwaysOn](#AlwaysOn)  
  
        -   [累加套件部署](#IncrementalDeployment)  
  
        -   [支援 SSIS 目錄中的永遠加密功能](#encrypted)  
  
    -   更容易偵錯  
  
        -   [SSIS 目錄中新的 ssis_logreader 資料庫層級角色](#LogReader)  
  
        -   [SSIS 目錄中新的 RuntimeLineage 記錄層級](#RuntimeLineage)  
  
        -   [SSIS 目錄中新的自訂記錄層級](#CustomLogging)  
  
        -   [資料流程中適用於錯誤的資料行名稱](#ErrorColumn)  
  
        -   [已擴大錯誤資料行名稱的支援](#getidstring)  
  
        -   [支援全伺服器的預設記錄層級](#ServerLogLevel)  
  
        -   [API 中新的 IDTSComponentMetaData130 介面](#CMD130)  
  
    -   更容易進行封裝管理  
  
        -   [已改進專案升級的體驗](#ProjectUpgrade)  
  
        -   [AutoAdjustBufferSize 屬性會自動計算資料流程的緩衝區大小](#BufferSize)  
  
        -   [可重複使用的控制流程範本](#Templates)  
  
        -   [將新範本重新命名為組件](#Parts)  
  
-   **連線能力**  
  
    -   已擴充內部部署的連線  
  
        -   [支援 OData v4 資料來源](#ODatav4)  
  
        -   [明確支援 Excel 2013 資料來源](#Excel2013)  
  
        -   [支援 Hadoop 檔案系統 (HDFS)](#HDFS)  
  
        -   [已擴大 Hadoop 和 HDFS 的支援](#more_hadoop)  
  
        -   [HDFS 檔案目的地現在支援 ORC 檔案格式](#hdfsORC)  
  
        -   [已針對 SQL Server 2016 更新 ODBC 元件](#odbc2016)  
  
        -   [明確支援 Excel 2016 資料來源](#Excel2016)  
  
        -   [已發行 Connector for SAP BW for SQL Server 2016](#SAPBW)
        
        -   [已發行 Connectors v4.0 for Oracle and Teradata](#oracleteradata)
        
        -   [已發行 Connectors for Analytics Platform System (PDW) Appliance Update 5](#pdwau5)
  
    -   已擴充與雲端的連線  
  
        -   Azure 儲存體連接器及 HDInsight 的 Hive 和 Pig 工作 - [針對 SQL Server 2016 發行的 Azure Feature Pack for SSIS](#AFP2016)
        
        -   [Service Pack 1 已發行 Microsoft Dynamics Online 資源的支援](#dynamics)
        
        -   [已推出 Azure Data Lake Store 的支援](#datalakestore)
        
        -   [已發佈對 Azure Synapse Analytics 的支援](#sqldwupload)
  
-   **可用性和產能**  
  
    -   更好的安裝體驗  
  
        -   [當 SSISDB 屬於可用性群組時封鎖升級](#Upgrade)  
  
    -   更好的設計體驗  
  
        -   [SSIS 設計師會建立和維護適用於 SQL Server 2016、2014 或 2012 的封裝](#OneDesigner)  
  
        -   多個設計工具改進功能和 Bug 修正。  
  
    -   在 SQL Server Management Studio 中更好的管理體驗
  
        -   [已改進 SSIS 目錄檢視的效能](#CatViews)  
  
    -   其他增強功能  
  
        -   [平衡型資料分配器轉換現在是 SSIS 的一部分](#BDDinbox)  
  
        -   [資料摘要發行元件現在是 SSIS 的一部分](#ComplexFeedinbox)  
  
        -   [支援 SQL Server 匯入和匯出精靈中的 Azure Blob 儲存體](#AzureBlob)  
  
        -   [已發行適用於 Microsoft SQL Server 2016 的 Change Data Capture Designer for Oracle 和 Change Data Capture Service for Oracle](#CDCOracle)  
  
        -   [已針對 SQL Server 2016 更新 CDC 元件](#cdc2016)  
  
        -   [Analysis Services 執行 DDL 工作已更新](#ASDDL)  
  
        -   [Analysis Services 工作支援表格式模型](#ssasrc0)  
  
        -   [支援內建的 R 服務](#builtinR)  
  
        -   [XML 工作中詳細的 XML 驗證輸出](#ValidateXML)  
  
## <a name="manageability"></a>管理能力  

### <a name="better-deployment"></a>更容易部署

####  <a name="ssisdb-upgrade-wizard"></a><a name="ssisdbupgrwiz"></a> SSISDB 升級精靈  
 當資料庫版本比目前的 SQL Server 執行個體版本還舊時，執行 SSISDB 升級精靈來升級 SSIS 目錄資料庫 (SSISDB)。 這會在下列其中一個條件成立時發生：  
  
-   您已從舊版 SQL Server 還原資料庫。  
  
-   您在升級 SQL Server 執行個體之前，並未從 AlwaysOn 可用性群組移除資料庫。 這可防止資料庫自動升級。 如需詳細資訊，請參閱＜ [Upgrading SSISDB in an availability group](../integration-services/catalog/ssis-catalog.md#Upgrade)＞。  
  
 如需詳細資訊，請參閱[SSIS 目錄 &#40;SSISDB&#41;](../integration-services/catalog/ssis-catalog.md)。 

####  <a name="support-for-always-on-in-the-ssis-catalog"></a><a name="AlwaysOn"></a> 支援 SSIS 目錄中的AlwaysOn  
 AlwaysOn 可用性群組功能是提供資料庫鏡像之企業級替代方案的高可用性與災害復原解決方案。 可用性群組支援一組可一起容錯移轉之離散化使用者資料庫的容錯移轉環境，也就是所謂的可用性資料庫。 如需詳細資訊，請參閱 [永遠開啟可用性群組](../database-engine/availability-groups/windows/always-on-availability-groups-sql-server.md)。  
  
 在 SQL Server 2016 中，SSIS 引進新功能，可讓您輕鬆部署到集中式 SSIS 目錄 (也就是 SSISDB 使用者資料庫)。 為了提供 SSISDB 資料庫及其內容的高可用性 (專案、封裝、執行記錄等)，您可以將 SSISDB 資料庫加入「永遠開啟」可用性群組，就像其他任何使用者資料庫。 發生容錯移轉時，其中一個次要節點會自動變成新的主要節點。  
  
 如需詳細的概觀以及針對 SSISDB 啟用 AlwaysOn 的逐步指示，請參閱 [SSIS 目錄](../integration-services/catalog/ssis-catalog.md)。  

####  <a name="incremental-package-deployment"></a><a name="IncrementalDeployment"></a> 累加套件部署  
累加封裝部署功能可讓您將一或多個封裝部署到現有或新的專案中，而不需部署整個專案。 您可以使用下列工具，以累加方式部署封裝。  
  
-   部署精靈  
  
-   SQL Server Management Studio (這會使用部署精靈)  
  
-   SQL Server Data Tools (這也會使用部署精靈)  
  
-   預存程序  
  
-   管理物件模型 (MOM) API  
  
 如需詳細資訊，請參閱[部署 Integration Services (SSIS) 專案和套件](../integration-services/packages/deploy-integration-services-ssis-projects-and-packages.md)。  

####  <a name="support-for-always-encrypted-in-the-ssis-catalog"></a><a name="encrypted"></a> 支援 SSIS 目錄中的 AlwaysOn  
 SSIS 已經支援 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]中的永遠加密功能。 如需詳細資訊，請參閱下列部落格文章。  
  
-   [使用永遠加密的 SSIS](https://techcommunity.microsoft.com/t5/sql-server-integration-services/ssis-with-always-encrypted/ba-p/388272)  
  
-   [使用永遠加密的查詢轉換](https://techcommunity.microsoft.com/t5/sql-server-integration-services/lookup-transformation-with-always-encrypted/ba-p/388282)  

### <a name="better-debugging"></a>更容易偵錯

####  <a name="new-ssis_logreader-database-level-role-in-the-ssis-catalog"></a><a name="LogReader"></a> SSIS 目錄中新的 ssis_logreader 資料庫層級角色  
 在舊版 SSIS 目錄中，只有 **ssis_admin** 角色中的使用者可以存取含有記錄輸出的檢視。 現在提供一個新的 **ssis_logreader** 資料庫層級角色，讓您可用來為不是系統管理員的使用者授與權限，以存取包含記錄輸入的檢視。  
  
 此外，還有一個新的 **ssis_monitor** 角色。 此角色僅支援「永遠開啟」，而且僅可供 SSIS 目錄內部使用。  

####  <a name="new-runtimelineage-logging-level-in-the-ssis-catalog"></a><a name="RuntimeLineage"></a> SSIS 目錄中新的 RuntimeLineage 記錄層級  
 SSIS 目錄中新的 **RuntimeLineage** 記錄層次會收集在資料流程中追蹤歷程資訊所需的資料。 您可以剖析此歷程資訊，以對應工作間的歷程關聯性。 ISV 和開發人員可以使用此資訊來建置自訂歷程對應工具。 

####  <a name="new-custom-logging-level-in-the-ssis-catalog"></a><a name="CustomLogging"></a> SSIS 目錄中新的自訂記錄層級  
 舊版 SSIS 目錄可讓您在執行封裝時，從四個內建的記錄層次進行選擇： **無、基本、效能或詳細資訊**。 SQL Server 2016 新增 **RuntimeLineage** 記錄層級。 此外，您現在可以在 SSIS 目錄中建立和儲存多個自訂記錄層級，並挑選每次您執行封裝時要使用的記錄層級。 針對每個自訂記錄層級，只選取您想要擷取的統計資料和事件。 選擇性地包含事件內容，以查看變數值、連接字串和工作屬性。 如需詳細資訊，請參閱＜ [Enable Logging for Package Execution on the SSIS Server](../integration-services/performance/integration-services-ssis-logging.md#server_logging)＞。 

####  <a name="column-names-for-errors-in-the-data-flow"></a><a name="ErrorColumn"></a> 資料流程中適用於錯誤的資料行名稱  
 當您在資料流程中包含錯誤至錯誤輸出的資料列重新導向時，則輸出會包含錯誤發生，但不會顯示的資料行名稱的資料行的數值識別項。 現在，有數種方式可用來尋找或顯示發生錯誤的資料行名稱。  
  
-   當您設定記錄時，請選取 **DiagnosticEx** 事件以供記錄使用。 此事件會將資料流程資料行對應寫入記錄檔。 您接著可以使用錯誤輸出所擷取的資料行識別碼，在此資料行對應中查詢資料行名稱。 如需詳細資訊，請參閱[處理資料中的錯誤](../integration-services/data-flow/error-handling-in-data.md)。  
  
-   在進階編輯器中，您可以在檢視資料流程元件的輸入或輸出資料行的屬性時，看到上游資料行的資料行名稱。  
  
-   若要查看發生錯誤的資料行名稱，請將資料檢視器附加至錯誤輸出。  資料檢視器現在會顯示錯誤的說明以及發生錯誤的資料行名稱。  
  
-   在指令碼元件或自訂資料流程元件中，呼叫 IDTSComponentMetadata100 介面的新 <xref:Microsoft.SqlServer.Dts.Pipeline.Wrapper.IDTSComponentMetaData130.GetIdentificationStringByID%2A> 方法。  
  
 如需此改進功能的詳細資訊，請參閱下列由 SSIS 開發人員 Bo Fan 所張貼的部落格文章︰ [適用於 SSIS 資料流程的錯誤資料行改進功能](https://techcommunity.microsoft.com/t5/sql-server-integration-services/error-column-improvements-for-ssis-data-flow-updated-for-rc2/ba-p/388253)。  
  
> [!NOTE]  
>  (後續版本中已擴大此支援。 如需詳細資訊，請參閱 [Expanded support for error column names](#getidstring) (已擴大錯誤資料行名稱的支援) 和 [New IDTSComponentMetaData130 interface in the API](#CMD130)(API 中新的 IDTSComponentMetaData130 介面)。  

####  <a name="expanded-support-for-error-column-names"></a><a name="getidstring"></a> 已擴大錯誤資料行名稱的支援  
 **DiagnosticEx** 事件現在會記錄所有輸入和輸出資料行的資料行資訊，而不只是歷程資料行。 因此，我們現在會呼叫管線資料行對應，而不是管線歷程對應。  
  
 GetIdentificationStringByLineageID 方法已重新命名為 <xref:Microsoft.SqlServer.Dts.Pipeline.Wrapper.IDTSComponentMetaData130.GetIdentificationStringByID%2A>中已新增或更新的功能。 如需詳細資訊，請參閱＜ [資料流程中適用於錯誤的資料行名稱](#ErrorColumn)＞。  
  
 如需此變更和錯誤資料行改進功能的詳細資訊，請參閱下列更新的部落格文章。 [適用於 SSIS 資料流程的錯誤資料行增強功能 (已針對 CTP3.3 更新)](https://techcommunity.microsoft.com/t5/sql-server-integration-services/error-column-improvements-for-ssis-data-flow-updated-for-rc2/ba-p/388253)  
  
> [!NOTE]  
>  (在 RC0 中，這個方法已移至新的 <xref:Microsoft.SqlServer.Dts.Pipeline.Wrapper.IDTSComponentMetaData130> 介面。 如需詳細資訊，請參閱 [New IDTSComponentMetaData130 interface in the API](#CMD130)(API 中新的 IDTSComponentMetaData130 介面)。  

####  <a name="support-for-server-wide-default-logging-level"></a><a name="ServerLogLevel"></a> 支援全伺服器的預設記錄層級  
 在 SQL Server 的 [伺服器屬性] 中，您現在可以在 [Server logging level (伺服器記錄層次)]  屬性下方，選取預設的全伺服器記錄層次。 您可以挑選其中一個內建的記錄層級 (基本、無、詳細資訊、效能或執行階段歷程)，或者可挑選現有的自訂記錄層級。 選取的記錄層級會套用到所有部署到 SSIS 目錄的封裝。 它預設也會套用到執行 SSIS 封裝的 SQL 代理程式工作步驟。  

####  <a name="new-idtscomponentmetadata130-interface-in-the-api"></a><a name="CMD130"></a> API 中新的 IDTSComponentMetaData130 介面  
 SSIS 目錄中新的 <xref:Microsoft.SqlServer.Dts.Pipeline.Wrapper.IDTSComponentMetaData130> 介面會在 SQL Server 2016 中將新功能加入現有 <xref:Microsoft.SqlServer.Dts.Pipeline.Wrapper.IDTSComponentMetaData100> 介面中，尤其是 <xref:Microsoft.SqlServer.Dts.Pipeline.Wrapper.IDTSComponentMetaData130.GetIdentificationStringByID%2A> 方法。 ( **GetIdentificationStringByID** 方法會從 <xref:Microsoft.SqlServer.Dts.Pipeline.Wrapper.IDTSComponentMetaData100> 介面移至新的介面)。另外還有新的 <xref:Microsoft.SqlServer.Dts.Pipeline.Wrapper.IDTSInputColumn130> (已擴大錯誤資料行名稱的支援) 和 <xref:Microsoft.SqlServer.Dts.Pipeline.Wrapper.IDTSOutputColumn130> 介面，這兩種介面都提供 **LineageIdentificationString** 屬性。 如需詳細資訊，請參閱＜ [資料流程中適用於錯誤的資料行名稱](#ErrorColumn)＞。  

### <a name="better-package-management"></a>更容易進行封裝管理

####  <a name="improved-experience-for-project-upgrade"></a><a name="ProjectUpgrade"></a> 已改進專案升級的體驗  
 當您將 SSIS 專案從舊版升級到目前的版本時，專案層級的連線管理員會繼續如預期般運作，並保留封裝配置和註解。  

####  <a name="autoadjustbuffersize-property-automatically-calculates-buffer-size-for-data-flow"></a><a name="BufferSize"></a> AutoAdjustBufferSize 屬性會自動計算資料流程的緩衝區大小  
 當您將新的 **AutoAdjustBufferSize** 屬性值設為 [true] 時，資料流程引擎會自動計算資料流程的緩衝區大小。 如需詳細資訊，請參閱＜ [Data Flow Performance Features](../integration-services/data-flow/data-flow-performance-features.md)＞。  

####  <a name="reusable-control-flow-templates"></a><a name="Templates"></a> 可重複使用的控制流程範本  
 將常用的控制流程工作或容器儲存到獨立的範本檔案，並使用控制流程範本，在專案的一或多個封裝中多次重複使用。 這個再使用性讓 SSIS 封裝的設計和維護變得更容易。 如需詳細資訊，請參閱 [使用控制流程封裝組件在封裝之間重複使用控制流程](../integration-services/reuse-control-flow-across-packages-by-using-control-flow-package-parts.md)。  

####  <a name="new-templates-renamed-as-parts"></a><a name="Parts"></a> 將新範本重新命名為組件  
 在 CTP 3.0 中發行之可重複使用的新控制流程範本，已重新命名為控制流程組件或封裝組件。 如需此功能的詳細資訊，請參閱 [使用控制流程封裝組件在封裝之間重複使用控制流程](../integration-services/reuse-control-flow-across-packages-by-using-control-flow-package-parts.md)。  

## <a name="connectivity"></a>連線能力  

### <a name="expanded-connectivity-on-premises"></a>已擴充內部部署的連線

####  <a name="support-for-odata-v4-data-sources"></a><a name="ODatav4"></a> 支援 OData v4 資料來源  
 OData 來源和 OData 連線管理員現在支援 OData v3 和 v4 通訊協定。  
  
-   針對 OData V3 通訊協定，此元件支援 ATOM 和 JSON 資料格式。  
  
-   針對 OData V4 通訊協定，此元件支援 JSON 資料格式。  
  
 如需詳細資訊，請參閱＜ [OData Source](../integration-services/data-flow/odata-source.md)＞。  

####  <a name="explicit-support-for-excel-2013-data-sources"></a><a name="Excel2013"></a> 明確支援 Excel 2013 資料來源  
 Excel 連線管理員、Excel 來源和 Excel 目的地，以及 SQL Server 匯入和匯出精靈現在明確支援 Excel 2013 資料來源。 

####  <a name="support-for-the-hadoop-file-system-hdfs"></a><a name="HDFS"></a> 支援 Hadoop 檔案系統 (HDFS)  
 HDFS 的支援包含連線管理員，以連接到 Hadoop 叢集和工作來執行一般的 HDFS 作業。 如需詳細資訊，請參閱 [Integration Services &#40;SSIS&#41; 中的 Hadoop 和 HDFS 支援](../integration-services/hadoop-and-hdfs-support-in-integration-services-ssis.md)。  

####  <a name="expanded-support-for-hadoop-and-hdfs"></a><a name="more_hadoop"></a> 已擴大 Hadoop 和 HDFS 的支援  
  
-   Hadoop 連線管理員現在支援基本和 Kerberos 驗證。 如需詳細資訊，請參閱＜ [Hadoop Connection Manager](../integration-services/connection-manager/hadoop-connection-manager.md)＞。  
  
-   HDFS 檔案來源和 HDFS 檔案目的地如何支援文字和 Avro 格式。 如需詳細資訊，請參閱  [HDFS File Source](../integration-services/data-flow/hdfs-file-source.md) 和  [HDFS File Destination](../integration-services/data-flow/hdfs-file-destination.md)。  
  
-   除了 CopyToHadoop 和 CopyFromHadoop 選項，Hadoop 檔案系統工作現在支援 CopyWithinHadoop 選項。 如需詳細資訊，請參閱＜ [Hadoop File System Task](../integration-services/control-flow/hadoop-file-system-task.md)＞。  

####  <a name="hdfs-file-destination-now-supports-orc-file-format"></a><a name="hdfsORC"></a> HDFS 檔案目的地現在支援 ORC 檔案格式  
 除了文字和 Avro，HDFS 檔案目的地現在還支援 ORC 檔案格式。 (HDFS 檔案來源僅支援文字和 Avro)。如需此元件的詳細資訊，請參閱 [HDFS File Destination](../integration-services/data-flow/hdfs-file-destination.md)。  

####  <a name="odbc-components-updated-for-sql-server-2016"></a><a name="odbc2016"></a> 已針對 SQL Server 2016 更新 ODBC 元件  
 ODBC 來源和目的地元件已更新，可與 SQL Server 2016 完全相容。 沒有任何新功能，也沒有任何行為變更。  

####  <a name="explicit-support-for-excel-2016-data-sources"></a><a name="Excel2016"></a> 明確支援 Excel 2016 資料來源  
 Excel 連線管理員、Excel 來源和 Excel 目的地現在明確支援 Excel 2016 資料來源。  

####  <a name="connector-for-sap-bw-for-sql-server-2016-released"></a><a name="SAPBW"></a> 已發行 Connector for SAP BW for SQL Server 2016  
 適用於 Microsoft SQL ServerÂ® 2016 的 MicrosoftÂ® Connector for SAP BW 已作為 SQL Server 2016 Feature Pack 的一部分發行。 若要下載 Feature Pack 的元件，請參閱 [MicrosoftÂ® SQL ServerÂ® 2016 Feature Pack](https://www.microsoft.com/download/details.aspx?id=56833)。
 
#### <a name="connectors-v40-for-oracle-and-teradata-released"></a><a name="oracleteradata"></a> 已發行 Connectors v4.0 for Oracle and Teradata
已發行 Microsoft Connectors v4.0 for Oracle and Teradata。 若要下載連接器，請參閱 [Microsoft Connectors v4.0 for Oracle and Teradata](https://www.microsoft.com/download/details.aspx?id=52950)。

### <a name="connectors-for-analytics-platform-system-pdw-appliance-update-5-released"></a><a name="pdwau5"></a> 已發行 Connectors for Analytics Platform System (PDW) Appliance Update 5
已發行將資料載入 PDW 與 AU5 的目的地配接器。 若要下載配接器，請參閱 [Analytics Platform System Appliance Update 5 Documentation and Client Tools](https://www.microsoft.com/download/details.aspx?id=51610)(Analytics Platform System Appliance Update 5 說明文件和用戶端工具)。

### <a name="expanded-connectivity-to-the-cloud"></a>已擴充與雲端的連線

####  <a name="azure-feature-pack-for-ssis-released-for-sql-server-2016"></a><a name="AFP2016"></a> 針對 SQL Server 2016 發行的 Azure Feature Pack for SSIS  
 已針對 SQL Server 2016 發行 Azure Feature Pack for Integration Services。 此功能套件包含連線管理員，可連接到 Azure 資料來源和工作來執行一般的 Azure 作業。 如需詳細資訊，請參閱 [Azure Feature Pack for Integration Services &#40;SSIS&#41;](../integration-services/azure-feature-pack-for-integration-services-ssis.md)。  

#### <a name="support-for-microsoft-dynamics-online-resources-released-in-service-pack-1"></a><a name="dynamics"></a> Service Pack 1 已發行 Microsoft Dynamics Online 資源的支援

安裝 SQL Server 2016 Service Pack 1 後，OData 來源和 OData 連接管理員現在支援連接到 Microsoft Dynamics AX Online 和 Microsoft Dynamics CRM Online 的 OData 摘要。

#### <a name="support-for-azure-data-lake-store-released"></a><a name="datalakestore"></a> 已推出 Azure Data Lake Store 的支援

最新版的 Azure Feature Pack 包含將資料移入及移出 Azure Data Lake Store 的連線管理員、來源和目的地。 如需詳細資訊，請參閱 [Azure Feature Pack for Integration Services &#40;SSIS&#41;](../integration-services/azure-feature-pack-for-integration-services-ssis.md)

#### <a name="support-for-azure-synapse-analytics-released"></a><a name="sqldwupload"></a> 已發佈對 Azure Synapse Analytics 的支援

最新版 Azure Feature Pack 包含用來將資料填入 Azure Synapse Analytics 的 Azure SQL DW 上傳工作。 如需詳細資訊，請參閱 [Azure Feature Pack for Integration Services &#40;SSIS&#41;](../integration-services/azure-feature-pack-for-integration-services-ssis.md)

## <a name="usability-and-productivity"></a>可用性和產能  
 
### <a name="better-install-experience"></a>更好的安裝體驗

####  <a name="upgrade-blocked-when-ssisdb-belongs-to-an-availability-group"></a><a name="Upgrade"></a> 當 SSISDB 屬於可用性群組時封鎖升級  
 如果 SSIS 目錄資料庫 (SSISDB) 屬於「永遠開啟」可用性群組，您就必須從可用性群組中移除 SSISDB、升級 SQL Server，然後將 SSISDB 加回可用性群組。 如需詳細資訊，請參閱＜ [Upgrading SSISDB in an availability group](../integration-services/catalog/ssis-catalog.md#Upgrade)＞。  

### <a name="better-design-experience"></a>更好的設計體驗

####  <a name="multi-targeting-and-multi-version-support-in-ssis-designer"></a><a name="OneDesigner"></a> SSIS 設計師中的多目標和多個版本支援  
 您現在可以在適用於 Visual Studio 2015 的 SQL Server Data Tools &#40;SSDT&#41; 中使用 SSIS 設計師，來建立、維護和執行目標為 SQL Server 2016、SQL Server 2014 或 SQL Server 2012 的封裝。 若要取得 SSDT，請參閱 [下載最新的 SQL Server Data Tools](../ssdt/download-sql-server-data-tools-ssdt.md)。 

 在方案總管中，在 Integration Services 專案上按一下滑鼠右鍵，然後選取 [屬性]  以開啟專案的屬性頁。 在 [組態屬性]  的 [一般] 索引標籤中，選取 [TargetServerVersion]  屬性，然後選擇 SQL Server 2016、SQL Server 2014 或 SQL Server 2012。  
   
 ![專案屬性對話方塊中的 TargetServerVersion 屬性](../integration-services/media/targetserverversion2.png "專案屬性對話方塊中的 TargetServerVersion 屬性")  

> [!IMPORTANT]
> 如果您正在開發 SSIS 的自訂擴充功能，請參閱 [Support multi-targeting in your custom components](../integration-services/extending-packages-custom-objects/support-multi-targeting-in-your-custom-components.md) (在您的自訂元件中支援多目標功能) 和 [Getting your SSIS custom extensions to be supported by the multi-version support of SSDT 2015 for SQL Server 2016](https://blogs.msdn.microsoft.com/ssis/2016/04/19/getting-your-ssis-custom-extensions-to-be-supported-by-the-multi-version-support-of-ssdt-2015-for-sql-server-2016/)(取得 SSIS 自訂延伸模組，以支援 SQL Server 2016 的 SSDT 2015 多版本支援)。  

### <a name="better-management-experience-in-sql-server-management-studio"></a>在 SQL Server Management Studio 中更好的管理體驗

####  <a name="improved-performance-for-ssis-catalog-views"></a><a name="CatViews"></a> 已改進 SSIS 目錄檢視的效能  
 如果 SSIS 目錄檢視是透過不屬於 ssis_admin 角色成員的使用者來執行，則大多數現在都能執行得更好。  

### <a name="other-enhancements"></a>其他增強功能

####  <a name="balanced-data-distributor-transformation-is-now-part-of-ssis"></a><a name="BDDinbox"></a> 平衡型資料分配器轉換現在是 SSIS 的一部分  
 平衡型資料分配器轉換在舊版 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]中需要個別下載，但現在當您安裝 [!INCLUDE[ssISnoversion](../includes/ssisnoversion-md.md)]時就已安裝。 如需詳細資訊，請參閱＜ [Balanced Data Distributor Transformation](../integration-services/data-flow/transformations/balanced-data-distributor-transformation.md)＞。  
  
####  <a name="data-feed-publishing-components-are-now-part-of-ssis"></a><a name="ComplexFeedinbox"></a> 資料摘要發行元件現在是 SSIS 的一部分  
 資料摘要發行元件在舊版 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]中需要個別下載，但現在當您安裝 [!INCLUDE[ssISnoversion](../includes/ssisnoversion-md.md)]時就已安裝。 如需詳細資訊，請參閱＜ [Data Streaming Destination](../integration-services/data-flow/data-streaming-destination.md)＞。  

####  <a name="support-for-azure-blob-storage-in-the-sql-server-import-and-export-wizard"></a><a name="AzureBlob"></a> 支援 SQL Server 匯入和匯出精靈中的 Azure Blob 儲存體  
 [SQL Server 匯入和匯出精靈] 現在可以從 Azure Blob 儲存體匯入資料，並將資料儲存至其中。 如需詳細資訊，請參閱[選擇資料來源 &#40;SQL Server 匯入和匯出精靈&#41;](../integration-services/import-export-data/choose-a-data-source-sql-server-import-and-export-wizard.md) 和[選擇目的地 &#40;SQL Server 匯入和匯出精靈&#41;](../integration-services/import-export-data/choose-a-destination-sql-server-import-and-export-wizard.md)。 

####  <a name="change-data-capture-designer-and-service-for-oracle-for-microsoft-sql-server-2016-released"></a><a name="CDCOracle"></a> 已發行 Change Data Capture Designer and Service for Oracle for Microsoft SQL Server 2016  
 適用於 Microsoft SQL ServerÂ® 2016 的 MicrosoftÂ® Change Data Capture Designer and Service for Oracle by Attunity 已作為 SQL Server 2016 Feature Pack 的一部分發行。  這些元件現在支援傳統安裝中的 Oracle 12c。 (不支援多租用戶安裝) 若要下載 Feature Pack 的元件，請參閱 [MicrosoftÂ® SQL ServerÂ® 2016 Feature Pack](https://www.microsoft.com/download/details.aspx?id=56833)。  
  
####  <a name="cdc-components-updated-for-sql-server-2016"></a><a name="cdc2016"></a> 已針對 SQL Server 2016 更新 CDC 元件  
 CDC (異動資料擷取) 控制工作、來源和分隔器轉換元件已更新，可與 SQL Server 2016 完全相容。 沒有任何新功能，也沒有任何行為變更。  
  
####  <a name="analysis-services-execute-ddl-task-updated"></a><a name="ASDDL"></a> Analysis Services 執行 DDL 工作已更新  
 Analysis Services 執行 DDL 工作已更新，可接受表格式模型指令碼語言命令。

####  <a name="analysis-services-tasks-support-tabular-models"></a><a name="ssasrc0"></a> Analysis Services 工作支援表格式模型  
 您現在可以使用所有的 SSIS 工作和目的地，使用 SQL Server 2016 表格式模型來支援 SQL Server Analysis Services (SSAS)。 SSIS 工作已更新來表示表格式物件，而不是多維度物件。 例如，當您選取要處理的物件時，Analysis Services 處理工作會自動偵測表格式模型並顯示表格式物件清單，而不是顯示量值群組和維度。 資料分割處理目的地現在也會顯示表格式物件，並支援將資料推送到分割區。  
  
 維度處理目的地不適用於 SQL 2016 相容性層級的表格式模型。  如果您要進行表格式處理，就只需要 Analysis Services 處理工作和資料分割處理目的地。 

####  <a name="support-for-built-in-r-services"></a><a name="builtinR"></a> 支援內建的 R Services  
 SSIS 已支援 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]中的內建 R 服務。 您不只能使用 SSIS 來擷取資料和載入分析的輸出，而且還能建置、執行及定期重新訓練 R 模型。 如需詳細資訊，請參閱下列部落格文章。 [使用 SQL Server 2016 SSIS 和 R 服務推動您的機器學習服務專案](https://techcommunity.microsoft.com/t5/sql-server-integration-services/operationalize-your-machine-learning-project-using-sql-server/ba-p/388296)。 

####  <a name="rich-xml-validation-output-in-the-xml-task"></a><a name="ValidateXML"></a> XML 工作中詳細的 XML 驗證輸出  
 驗證 XML 文件，並啟用 XML 工作的 **ValidationDetails** 屬性以取得詳細的錯誤輸出。 在提供 **ValidationDetails** 屬性前，XML 工作所執行的 XML 驗證只會傳回結果為 True 或 False，而不會有錯誤的相關資訊及其位置。 現在，當您將 **ValidationDetails** 設定為 True 時，輸出檔案即涵蓋每項錯誤的詳細資訊，包括行號及位置。 您可以使用此資訊來了解、尋找及修正 XML 文件中的錯誤。 如需詳細資訊，請參閱＜ [Validate XML with the XML Task](../integration-services/control-flow/validate-xml-with-the-xml-task.md)＞。  
  
 [!INCLUDE[ssIS](../includes/ssis-md.md)] 在 **Service Pack 2 中引進** ValidationDetails [!INCLUDE[ssSQL11](../includes/sssql11-md.md)] 屬性。 這項新的屬性目前尚未經過宣布或記載。 **和** 中也提供 [!INCLUDE[ssSQL14](../includes/sssql14-md.md)] ValidationDetails [!INCLUDE[ssSQL15](../includes/sssql16-md.md)]屬性。   

[!INCLUDE[get-help-options](../includes/paragraph-content/get-help-options.md)]

[!INCLUDE[contribute-to-content](../includes/paragraph-content/contribute-to-content.md)]

## <a name="see-also"></a>另請參閱  
 [SQL Server 2016 的新功能](../sql-server/what-s-new-in-sql-server-2016.md)   
 [SQL Server 2016 的版本及支援功能](../sql-server/editions-and-components-of-sql-server-2016.md)