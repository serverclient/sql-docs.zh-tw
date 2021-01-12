---
title: 使用 Data Migration Assistant 評定企業併合並評量報告
description: 瞭解如何使用 DMA 來評估企業，並在升級 SQL Server 或遷移至 Azure SQL Database 之前合併評量報告。
ms.date: 06/21/2019
ms.prod: sql
ms.prod_service: dma
ms.reviewer: ''
ms.technology: dma
ms.topic: conceptual
keywords: ''
helpviewer_keywords:
- Data Migration Assistant, Assess
ms.assetid: ''
author: rajeshsetlem
ms.author: rajpo
ms.custom: seo-lt-2019
ms.openlocfilehash: 4d1bcf2cdc9997ea33be87184c38447b7cd62973
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98091604"
---
# <a name="assess-an-enterprise-and-consolidate-assessment-reports-with-dma"></a>使用 DMA 評估企業及整合評估報告

下列逐步指示可協助您使用 Data Migration Assistant 執行成功調整規模的評量，以升級內部部署 SQL Server 或在 Azure Vm 上執行的 SQL Server，或遷移至 Azure SQL Database。

## <a name="prerequisites"></a>必要條件

- 在您的網路上，指定將起始 DMA 的工具電腦。 確定這部電腦可以連線到您的 SQL Server 目標。
- 下載與安裝：
  - [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) 3.6 版或更新版本。
  - [PowerShell](https://aka.ms/wmf5download) 5.0 版或更新版本。
  - [.NET Framework](https://www.microsoft.com/download/details.aspx?id=30653) 4.5 或更新版本。
  - [SSMS](../ssms/download-sql-server-management-studio-ssms.md) 17.0 或更新版本。
  - [Power BI 桌面](/power-bi/fundamentals/desktop-get-the-desktop)。
  - [Azure PowerShell 模組](/powershell/azure/install-az-ps)
- 下載並解壓縮：
  - [DMA 會報告 Power BI 範本](https://techcommunity.microsoft.com/gxcuf89792/attachments/gxcuf89792/MicrosoftDataMigration/161/4/PowerBI-Reports.zip)。
  - [LoadWarehouse 腳本](https://techcommunity.microsoft.com/gxcuf89792/attachments/gxcuf89792/MicrosoftDataMigration/161/3/LoadWarehouse1.zip)。

## <a name="loading-the-powershell-modules"></a>載入 PowerShell 模組

將 PowerShell 模組儲存至 PowerShell 模組目錄，可讓您呼叫模組，而不需要在使用之前明確載入它們。

若要載入模組，請執行下列步驟：

1. 流覽至 [C:\Program Files\WindowsPowerShell\Modules]，然後建立名為 **>datamigrationassistant.msi** 的資料夾。
2. 開啟 [PowerShell 模組](https://techcommunity.microsoft.com/gxcuf89792/attachments/gxcuf89792/MicrosoftDataMigration/161/1/PowerShell-Modules2.zip)，然後將它們儲存至您建立的資料夾。

      ![PowerShell 模組](../dma/media//dma-consolidatereports/dma-powershell-modules.png)

    每個資料夾都包含相關聯的 .psm1 檔，如下圖所示：

   ![PowerShell 模組 .psm1 檔](../dma/media//dma-consolidatereports/dma-powershell-modules-psm1-files.png)

   > [!NOTE]
   > 它包含的資料夾和 .psm1 檔案必須具有相同的名稱。

   > [!IMPORTANT]
   > 將 PowerShell 檔案儲存至 WindowsPowerShell 目錄後，您可能需要解除封鎖這些檔案，以確保模組能正確載入。 若要解除封鎖 PowerShell 檔案，請以滑鼠右鍵按一下檔案，選取 [ **屬性**]，選取 [ **解除封鎖** ] 文字方塊，然後選取 **[確定]**。

   ![.psm1 檔案屬性](../dma/media//dma-consolidatereports/dma-psm1-file-properties.png)

    PowerShell 現在應該會在新的 PowerShell 會話啟動時自動載入這些模組。

## <a name="create-an-inventory-of-sql-servers"></a><a name="create-inventory"></a> 建立 SQL Server 的清查

執行 PowerShell 腳本來評估您的 SQL Server 之前，您必須先建立要評估的 SQL 伺服器清查。

這份清查可以是下列兩種形式的其中一種：

- Excel CSV 檔案
- SQL Server 資料表

### <a name="if-using-a-csv-file"></a>如果使用 CSV 檔案

> [!IMPORTANT]
> 確定清查檔案儲存為逗點分隔的 (CSV) 檔。
>
> 若為預設實例，請將實例名稱設定為 MSSQLServer。

使用 csv 檔案匯入資料時，請確定只有兩個數據行的資料 **實例名稱** 和 **資料庫名稱**，而且資料行沒有標頭資料列。

 ![csv 檔案內容](../dma/media//dma-consolidatereports/dma-csv-file-contents.png)

### <a name="if-using-a-sql-server-table"></a>如果使用 SQL Server 資料表

> [!IMPORTANT]
> 若為預設實例，請將實例名稱設定為 MSSQLServer。

建立名為 **EstateInventory** 的資料庫，以及名為 **DatabaseInventory** 的資料表。 包含此清查資料的資料表可以有任意數目的資料行，但前提是下列四個數據行存在：

- ServerName
- InstanceName
- DatabaseName
- AssessmentFlag

![如果您要使用 SQL Server 資料表，SQL Server 資料表內容的螢幕擷取畫面。](../dma/media//dma-consolidatereports/dma-sql-server-table-contents-database-inventory.png)

如果此資料庫不在工具電腦上，請確定工具電腦具有此 SQL Server 實例的網路連線能力。

透過 CSV 檔案使用 SQL Server 資料表的優點是，您可以使用 [評定旗標] 資料行來控制所挑選的實例/資料庫以進行評量，這樣可讓您更輕鬆地將評量分成較小的區塊。  接著，您可以跨越多個評量 (請參閱本文稍後有關執行評估的章節) ，這比維護多個 CSV 檔案更容易。

請記住，根據物件的數目及其複雜度，評定可能會花很長的時間 (小時 +) ，因此將評量分成可管理的區塊是明智的。

### <a name="if-using-an-instance-inventory"></a>如果使用實例清查

建立名為 **EstateInventory** 的資料庫，以及名為 **InstanceInventory** 的資料表。 包含此清查資料的資料表可以有任意數目的資料行，但前提是下列四個數據行存在：

- ServerName
- InstanceName
- Port
- AssessmentFlag

![使用實例清查時，SQL Server 資料表內容的螢幕擷取畫面。](../dma/media//dma-consolidatereports/dma-sql-server-table-contents-instance-inventory.png)

## <a name="running-a-scaled-assessment"></a>執行調整規模的評量

將 PowerShell 模組載入至模組目錄並建立清查之後，您必須開啟 PowerShell 並執行 dmaDataCollector 函式來執行調整的評量。

  ![dmaDataCollector 函式清單](../dma/media//dma-consolidatereports/dma-dmaDataCollector-function-listing.png)

下表說明與 dmaDataCollector 函數相關聯的參數。

|參數  |描述 |
|---------|---------|
|**getServerListFrom** | 您的清查。 可能的值為 **SqlServer** 和 **CSV**。<br/>如需詳細資訊，請參閱 [建立 SQL server 的清查](#create-inventory)。 |
|**csvPath** | CSV 清查檔案的路徑。  只有當 **getServerListFrom** 設定為  **CSV** 時，才會使用。 |
|**serverName** | 在 **getServerListFrom** 參數中使用 **SqlServer** 時的清查 SQL Server 實例名稱。 |
|**databaseName** | 裝載清查資料表的資料庫。 |
|**useInstancesOnly** | 位旗標，用來指定是否要使用實例清單進行評量。  如果設定為0，則會使用 DatabaseInventory 資料表來建立評量目標清單。 |
|**AssessmentName** | DMA 評估的名稱。 |
|**TargetPlatform** | 您要執行的評量目標型別。  可能的值為 **AzureSQLDatabase**、 **ManagedSqlServer**、 **SQLServer2012**、 **SQLServer2014**、 **SQLServer2016**、 **SQLServerLinux2017**、 **SQLServerWindows2017**、  **SqlServerWindows2019** 和 **SqlServerLinux2019**。  |
|**AuthenticationMethod** | 用來連接到您想要評估之 SQL Server 目標的驗證方法。 可能的值為 **SQLAuth** 和 **WindowsAuth**。 |
|**OutputLocation** | 要在其中儲存 JSON 評定輸出檔案的目錄。 根據要評估的資料庫數目以及資料庫內的物件數目，評定可能需要相當長的時間。 檔案會在所有評量完成後寫入。 |

如果發生未預期的錯誤，則會終止此進程所起始的命令視窗。  檢查錯誤記錄檔，以判斷失敗的原因。

  ![錯誤記錄檔位置](../dma/media//dma-consolidatereports/dma-error-log-file-location.png)

## <a name="consuming-the-assessment-json-file"></a>使用評定 JSON 檔案

評量完成後，您就可以開始將資料匯入 SQL Server 進行分析。 若要使用評定 JSON 檔案，請開啟 PowerShell 並執行 dmaProcessor 函式。

  ![dmaProcessor 函式清單](../dma/media//dma-consolidatereports/dma-dmaProcessor-function-listing.png)

下表說明與 dmaProcessor 函數相關聯的參數。

|參數  |描述 |
|---------|---------|
|**processTo** | 將處理 JSON 檔案的位置。 可能的值為 **SQLServer** 和 **AzureSQLDatabase**。 |
|**serverName** | 將處理資料的 SQL Server 實例。  如果您針對 **processTo** 參數指定 **AzureSQLDatabase** ，則只包含 SQL Server 名稱 (不包含 database.windows.net) 。 以 Azure SQL Database 為目標時，系統會提示您輸入兩次登入。第一個是您的 Azure 租使用者認證，而第二個則是您的 Azure SQL Server 系統管理員登入。 |
|**CreateDMAReporting** | 要建立以處理 JSON 檔案的暫存資料庫。  如果您指定的資料庫已存在，而且您將此參數設定為其中一個，則不會建立物件。  這個參數適用于重新建立已卸載的單一物件。 |
|**CreateDataWarehouse** | 建立 Power BI 報表將使用的資料倉儲。 |
|**databaseName** | DMAReporting 資料庫的名稱。 |
|**warehouseName** | 資料倉儲資料庫的名稱。 |
|**jsonDirectory** | 包含 JSON 評定檔案的目錄。  如果目錄中有多個 JSON 檔案，則會一併逐一處理。 |

DmaProcessor 函式應該只需要幾秒鐘的時間來處理單一檔案。

## <a name="loading-the-data-warehouse"></a>載入資料倉儲

在 dmaProcessor 完成評估檔案的處理之後，資料就會載入至 ReportData 資料表中的 DMAReporting 資料庫。 此時您需要載入資料倉儲。

1. 使用 LoadWarehouse 腳本來填入維度中的任何遺漏值。

    此腳本會從 DMAReporting 資料庫中的 ReportData 資料表取得資料，並將其載入至倉儲。  如果在此載入程式期間發生任何錯誤，則可能是因為維度資料表中遺漏專案的結果。

2. 載入資料倉儲。

  ![已載入 LoadWarehouse 內容](../dma/media//dma-consolidatereports/dma-load-warehouse-loaded.png)

## <a name="set-your-database-owners"></a>設定您的資料庫擁有者

雖然不是強制的，若要從報表中取得最大的值，建議您在 **dimDBOwner** 維度中設定資料庫擁有者，然後更新 **FactAssessment** 資料表中的 **DBOwnerKey** 。  遵循此程式可讓您根據特定資料庫擁有者來切割和篩選 Power BI 報表。

您也可以使用 LoadWarehouse 腳本提供基本的 TSQL 語句，讓您設定資料庫擁有者。

  ![LoadWarehouse 設定擁有者](../dma/media//dma-consolidatereports/dma-load-warehouse-set-owners.png)

## <a name="dma-reports"></a>DMA 報告

1. 在 Power BI Desktop 中開啟 DMA Reports Power BI 範本。
2. 輸入指向 **DMAWarehouse** 資料庫的伺服器詳細資料，然後選取 [ **載入**]。

   ![已載入 DMA Reports Power BI 範本](../dma/media//dma-consolidatereports/dma-reports-powerbi-template-loaded.png)

   報表從 **DMAWarehouse** 資料庫重新整理資料之後，您會看到類似下列的報表。

   ![DMAWarehouse 報表檢視](../dma/media//dma-consolidatereports/dma-DMAWarehouse-report1.png)

   > [!TIP]
   > 如果您看不到您預期的資料，請嘗試變更使用中的書簽。  如需詳細資訊，請參閱下一節中的詳細資料。

## <a name="working-with-dma-reports"></a>使用 DMA 報告

若要使用 DMA 報表，請使用書簽和交叉分析篩選器來篩選：

- 評量類型 (Azure SQL Database、Azure SQL 受控執行個體、SQL Server)  
- 執行個體名稱
- 資料庫名稱
- 小組名稱

若要存取 [書簽和篩選] 分頁，請選取主報表頁面上的 [篩選] 書簽：

![DMA 報表書簽和篩選](../dma/media//dma-consolidatereports/dma-report-bookmarks-filters.png)

選取 [篩選] 書簽可啟用下列分頁：

![DMA 報表檢視分頁](../dma/media//dma-consolidatereports/dma-report-views-blade.png)

您可以使用書簽來切換報表內容：

- Azure SQL Database 雲端評量
- Azure SQL 受控執行個體雲端評量
- 內部部署評量

![DMA 報表檢視書簽](../dma/media//dma-consolidatereports/dma-report-bookmarks1.png)

若要隱藏 [篩選] 分頁，請按 CTRL 鍵 [上一頁] 按鈕：

![DMA 報表檢視返回按鈕](../dma/media//dma-consolidatereports/dma-report-bookmarks-back.png)

報表頁面左下角有一個提示，顯示篩選器目前是否已套用於下列任何專案：

- FactAssessment – InstanceName
- FactAssessment – DatabaseName
- dimDBOwner-DBOwner

![篩選套用的提示](../dma/media//dma-consolidatereports/dma-filter-applied-prompt.png)

> [!NOTE]
> 如果您只執行 Azure SQL Database 評量，則只會填入雲端報告。 相反地，如果您只執行內部部署評量，則只會填入內部部署報表。 不過，如果您同時執行 Azure 和內部部署評量，然後將這兩個評量載入至您的倉儲，您可以透過 CTRL + 按一下相關聯的圖示，在雲端報表和內部部署報表之間切換。

## <a name="reports-visuals"></a>報表視覺效果

Power BI 報表中顯示的詳細資料會顯示在下列各節中。

### <a name="readiness-"></a>隨時

  ![DMA 就緒百分比](../dma/media//dma-consolidatereports/dma-readiness-percentage.png)

此視覺效果會根據選取範圍內容更新 (所有專案、實例、資料庫 [倍數] ) 。

### <a name="readiness-count"></a>就緒計數

  ![DMA 就緒計數](../dma/media//dma-consolidatereports/dma-readiness-count.png)

此視覺效果會顯示已準備好遷移尚未準備好遷移的資料庫計數的資料庫計數。

### <a name="readiness-bucket"></a>就緒 bucket

  ![DMA 就緒值區](../dma/media//dma-consolidatereports/dma-readiness-bucket.png)

此視覺效果會依下列就緒值區來顯示資料庫的細目：

- 100% 就緒
- 75-99% 就緒
- 50-75% 就緒
- 未就緒

### <a name="issues-word-cloud"></a>Word 雲端問題

  ![DMA 問題 WordCloud](../dma/media//dma-consolidatereports/dma-issues-word-cloud.png)

此視覺效果會顯示目前在選取內容中發生的問題 (所有專案、實例、資料庫 [倍數] ) 。 在畫面上顯示的文字愈大，該類別中的問題數目就愈大。 將滑鼠指標暫留在文字上，會顯示該類別中發生的問題數目。

### <a name="database-readiness"></a>資料庫就緒

  ![DMA 資料庫就緒狀態報表](../dma/media//dma-consolidatereports/dma-database-readiness-report.png)

此區段是報表的主要部分，會顯示實例資料庫的就緒程度。 這份報表的深入剖析階層：

- InstanceDatabase
- ChangeCategory
- 標題
- ObjectType
- ImpactedObjectName

 ![DMA 資料庫就緒程度報告詳細資料](../dma/media//dma-consolidatereports/dma-database-readiness-report-drilldown.png)

這份報表也可做為建立補救計畫報表的篩選點。

若要深入瞭解補救計畫報告，請以滑鼠右鍵按一下此圖表中的資料點，指向 [ **鑽取**]，然後選取 [ **補救計畫**]。

這項工作會根據您選取 [向下切入] 選項的點，將補救計畫報告篩選至目前的階層層級。

  ![已篩選 DMA 資料庫就緒程度報告深入分析](../dma/media//dma-consolidatereports/dma-database-readiness-report-drilldown-filtered.png)

  ![DMA 補救計畫報告](../dma/media//dma-consolidatereports/dma-remediation-plan-report.png)

您也可以使用 [ **視覺效果篩選** ] 分頁中的篩選器，自行使用「補救計畫」報表來建立自訂的補救計畫。

  ![DMA 補救計畫報告篩選選項](../dma/media//dma-consolidatereports/dma-remediation-plan-report-filter-options.png)

### <a name="script-disclaimer"></a>腳本免責聲明

*本文章中提供的範例腳本不受任何 Microsoft 標準支援計畫或服務支援。所有的腳本都是依原樣提供，不含任何種類的擔保。Microsoft 會進一步免除任何默示擔保，包括未受任何明示擔保或適用于特定用途之適用性擔保的限制。由於範例腳本和檔的使用或效能所產生的全部風險，仍會與您保持在一起。在任何活動中，Microsoft、其作者或者，在建立、生產或傳遞腳本的任何其他人，都必須承擔任何損害的責任， (包括不受限制、遺失業務利潤、商務中斷、商務資訊遺失或其他 pecuniary 損失) 因使用或無法使用範例腳本或檔而造成的損害，即使 Microsoft 已被告知此類損害的可能性也是如此。 在其他網站/存放庫/blog 上 reposting 這些腳本之前，請先搜尋許可權。*