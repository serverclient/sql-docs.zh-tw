---
title: 在 Azure Data Studio 中搭配 Kqlmagic (Kusto 查詢語言) 的筆記本
description: 本教學課程說明如何在 Azure Data Studio 中建立並執行 Kqlmagic。
ms.topic: how-to
ms.prod: azure-data-studio
ms.technology: azure-data-studio
author: markingmyname
ms.author: maghan
ms.reviewer: jukoesma
ms.custom: ''
ms.date: 10/29/2020
ms.openlocfilehash: cd9145bb01350a2019f1bfaa12c5280151c35f49
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98091742"
---
# <a name="kqlmagic-in-azure-data-studio"></a>Azure Data Studio 中的 Kqlmagic

**Kqlmagic** 是可在 **[Azure Data Studio 筆記本](./notebooks-guidance.md)** 中擴充 Python 核心功能的命令。 您可結合 Python 和 **[Kusto 查詢語言](/azure/data-explorer/kusto/query)** ，以使用與 `render` 命令整合的豐富 Plot.ly 程式庫來查詢並以視覺方式呈現資料。 Kqlmagic 可在同一個位置提供筆記本、資料分析和豐富 Python 功能的優點。 Kqlmagic 支援的資料來源包括 **[Azure 資料總管](/azure/data-explorer/data-explorer-overview)** 、 **[Application Insights](/azure/azure-monitor/app/app-insights-overview)** 及 **[Azure 監視器記錄](/azure/azure-monitor/platform/data-platform-logs)** 。

本文說明如何使用 Azure 資料總管叢集的 Kqlmagic 延伸模組、Application Insights 記錄和 Azure 監視器記錄，以在 Azure Data Studio 中建立並執行筆記本。

## <a name="prerequisites"></a>必要條件

- [Azure Data Studio](../download-azure-data-studio.md)
- [Python](https://www.python.org/downloads/)

## <a name="install-and-set-up-kqlmagic-in-a-notebook"></a>在筆記本中安裝及設定 Kqlmagic

本節中的步驟都會在 Azure Data Studio 筆記本中執行。

1. 建立新的筆記本，並將 **核心** 變更為 *Python 3*。

   ![新增筆記本](media/notebooks-kqlmagic/install-new-notebook.png)

2. 當系統詢問時，請選取 [是] 以升級 Python 套件。

   ![是](media/notebooks-kqlmagic/install-python-yes.png)

3. 安裝 Kqlmagic：

   ```python
   !pip install Kqlmagic --no-cache-dir --upgrade
   ```

   驗證是否已安裝：

   ```python
   !pip list
   ```

   ![清單](media/notebooks-kqlmagic/install-list.png)

4. 載入 Kqlmagic：

   ```python
   %reload_ext Kqlmagic
   ```

   > [!Note]
   > 如果此步驟失敗，請關閉檔案並重新開啟。

   ![載入 Kqlmagic 延伸模組](media/notebooks-kqlmagic/install-load-kql-magic-ext.png)

5. 您可瀏覽說明文件或檢查版本來測試是否已正確載入 Kqlmagic。

   ```python
   %kql --help "help"
   ```

   > [!Note]
   > 如果 `Samples@help` 要求輸入密碼，則可將其保留空白，然後按 **Enter**。

   ![説明](media/notebooks-kqlmagic/install-help.png)

   若要查看安裝的 Kqlmagic 版本，請執行下列命令。

   ```python
   %kql --version
   ```

## <a name="kqlmagic-with-an-azure-data-explorer-cluster"></a>搭配 Azure 資料總管叢集的 Kqlmagic

本節說明如何使用 Kqlmagic 搭配 Azure 資料總管叢集執行資料分析。

### <a name="load-and-authenticate-kqlmagic-for-azure-data-explorer"></a><a name="ade-load-auth"></a> 載入及驗證適用於 Azure 資料總管的 Kqlmagic

   > [!Note]
   > 當每次在 Azure Data Studio 中建立新筆記本時，都必須載入 Kqlmagic 延伸模組。

1. 驗證 **核心** 已設為 *Python3*。

   ![核心變更](media/notebooks-kqlmagic/change-kernel.png)

2. 載入 Kqlmagic：

   ```python
   %reload_ext Kqlmagic
   ```

   ![載入 Kqlmagic 延伸模組](media/notebooks-kqlmagic/install-load-kql-magic-ext.png)

3. 連線至叢集並進行驗證：

   ```python
   %kql azureDataExplorer://code;cluster='help';database='Samples'
   ```

    > [!Note]
    > 如果您使用自己的 ADX 叢集，則必須在連接字串中包含該區域，如下所示：   
    ```%kql azuredataexplorer://code;cluster='mycluster.westus';database='mykustodb'```

   請使用裝置登入來進行驗證。 從輸出複製程式碼，然後選取 [驗證]，瀏覽器將會開啟，請在其中貼上程式碼。 成功驗證後，您可回到 Azure Data Studio 以繼續進行指令碼的其餘部分。

   ![Azure 資料總管驗證](media/notebooks-kqlmagic/ade-auth.png)

### <a name="query-and-visualize-for-azure-data-explorer"></a>針對 Azure 資料總管進行查詢與視覺化

使用 [render 運算子](/azure/data-explorer/kusto/query/renderoperator)查詢資料，並使用 ploy.ly 程式庫將資料視覺化。 此查詢與視覺效果會提供使用原生 KQL 的整合式體驗。

1. 依照狀態和頻率來分析前 10 大 Storm 事件：

   ```python
   %kql StormEvents | summarize count() by State | sort by count_ | limit 10
   ```

   若熟悉 Kusto 查詢語言 (KQL)，則可在 `%kql` 後面鍵入查詢。

   ![分析 Storm 事件](media/notebooks-kqlmagic/ade-analyze-storm-events.png)

2. 將時間軸圖表視覺化：

   ```python
   %kql StormEvents \
   | summarize event_count=count() by bin(StartTime, 1d) \
   | render timechart title= 'Daily Storm Events'
   ```

   ![將時間圖視覺化](media/notebooks-kqlmagic/ade-visualize-timechart.png)

3. 使用 `%%kql` 的多行查詢範例。

   ```python
   %%kql
   StormEvents
   | summarize count() by State
   | sort by count_
   | limit 10
   | render columnchart title='Top 10 States by Storm Event count'
   ```

   ![多行查詢範例](media/notebooks-kqlmagic/ade-multiline-query-sample.png)

## <a name="kqlmagic-with-application-insights"></a>Kqlmagic 搭配 Application Insights

### <a name="load-and-authenticate-kqlmagic-for-application-insights"></a><a name="appin-load-auth"></a> 載入及驗證適用於 Application Insights 的 Kqlmagic

1. 驗證 **核心** 已設為 *Python3*。

   ![核心](media/notebooks-kqlmagic/change-kernel.png)

2. 載入 Kqlmagic：

   ```python
   %reload_ext Kqlmagic
   ```

   ![載入 Kqlmagic 延伸模組](media/notebooks-kqlmagic/install-load-kql-magic-ext.png)

   > [!Note]
   > 當每次在 Azure Data Studio 中建立新筆記本時，都必須載入 Kqlmagic 延伸模組。

3. 連線並進行驗證。

   首先，您必須為 Application Insights 資源產生 API 金鑰。 然後，使用應用程式識別碼和 API 金鑰來連線至筆記本的 Application Insights：

   ```python
   %kql appinsights://appid='DEMO_APP';appkey='DEMO_KEY'
   ```

### <a name="query-and-visualize-for-application-insights"></a>針對 Application Insights 進行查詢與視覺化

使用 [render 運算子](/azure/data-explorer/kusto/query/renderoperator)查詢資料，並使用 ploy.ly 程式庫將資料視覺化。 此查詢與視覺效果會提供使用原生 KQL 的整合式體驗。

1. 顯示頁面檢視：

   ```python
   %%kql
   pageViews
   | limit 10
   ```

   ![頁面檢視](media/notebooks-kqlmagic/appin-page-views.png)

   > [!Note]
   > 使用滑鼠拖曳圖表的某個區域，以放大特定日期。

2. 在時間軸圖表中顯示頁面檢視：

   ```python
   %%kql
   pageViews
   | summarize event_count=count() by name, bin(timestamp, 1d)
   | render timechart title= 'Daily Page Views'
   ```

   ![時間軸圖表](media/notebooks-kqlmagic/appin-timechart.png)

## <a name="kqlmagic-with-azure-monitor-logs"></a>Kqlmagic 搭配 Azure 監視器記錄

### <a name="load-and-authenticate-kqlmagic-for-azure-monitor-logs"></a><a name="aml-load-auth"></a> 載入及驗證適用於 Azure 監視器的 Kqlmagic

1. 驗證 **核心** 已設為 *Python3*。

   ![變更](media/notebooks-kqlmagic/change-kernel.png)

2. 載入 Kqlmagic：

   ```python
   %reload_ext Kqlmagic
   ```

   ![載入 Kqlmagic 延伸模組](media/notebooks-kqlmagic/install-load-kql-magic-ext.png)

   > [!Note]
   > 當每次在 Azure Data Studio 中建立新筆記本時，都必須載入 Kqlmagic 延伸模組。

3. 連線並進行驗證：

   ```python
   %kql loganalytics://workspace='DEMO_WORKSPACE';appkey='DEMO_KEY';alias='myworkspace'
   ```

   ![Log Analytics 驗證](media/notebooks-kqlmagic/aml-auth.png)

### <a name="query-and-visualize-for-azure-monitor-logs"></a>針對 Azure 監視器進行查詢與視覺化

使用 [render 運算子](/azure/data-explorer/kusto/query/renderoperator)查詢資料，並使用 ploy.ly 程式庫將資料視覺化。 此查詢與視覺效果會提供使用原生 KQL 的整合式體驗。

1. 檢視時間軸圖表：

   ```python
   %%kql
   KubeNodeInventory
   | summarize event_count=count() by Status, bin(TimeGenerated, 1d)
   | render timechart title= 'Daily Kubernetes Nodes'
   ```

   ![Log Analytics 每日 Kubernetes 節點時間圖](media/notebooks-kqlmagic/aml-timechart-daily-kubernetes-nodes.png)

## <a name="next-steps"></a>後續步驟

深入了解筆記本和 Kqlmagic：

- [適用於 Azure Data Studio 的 Kusto (KQL) 延伸模組 (預覽)](../extensions/kusto-extension.md)
- [建立並執行 Kusto (KQL) 筆記本 (預覽)](./notebooks-kusto-kernel.md)
- [使用 Jupyter Notebook 和 Kqlmagic 延伸模組來分析 Azure 資料總管中的資料](/azure/data-explorer/Kqlmagic) (機器翻譯)
- [Jupyter Notebook 和 Jupyter 實驗室的延伸模組 (Magic)](https://github.com/Microsoft/jupyter-Kqlmagic)，其可在搭配 Kusto、Application Insights 和 LogAnalytics 資料使用時提供筆記本體驗。
- [Kqlmagic](https://pypi.org/project/Kqlmagic/)
- [如何在 Azure Data Studio 中使用筆記本](./notebooks-guidance.md)