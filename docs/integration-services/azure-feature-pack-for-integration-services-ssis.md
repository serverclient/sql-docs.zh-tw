---
description: Azure Feature Pack for Integration Services (SSIS)
title: 適用於 Integration Services (SSIS) 的 Azure Feature Pack | Microsoft Docs
ms.custom: ''
ms.date: 12/24/2019
ms.prod: sql
ms.prod_service: integration-services
ms.reviewer: ''
ms.technology: integration-services
ms.topic: conceptual
f1_keywords:
- SQL13.SSIS.AZURE.F1
- SQL14.SSIS.AZURE.F1
ms.assetid: 31de555f-ae62-4f2f-a6a6-77fea1fa8189
author: chugugrace
ms.author: chugu
ms.openlocfilehash: b22910f0a794925d07358fa73f3bd6d7a7c6e15a
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98091656"
---
# <a name="azure-feature-pack-for-integration-services-ssis"></a>Azure Feature Pack for Integration Services (SSIS)

[!INCLUDE[sqlserver-ssis](../includes/applies-to-version/sqlserver-ssis.md)]


SQL Server Integration Services (SSIS) Feature Pack for Azure 是一個延伸模組，可提供此頁面上所列的元件，以便讓 SSIS 連接到 Azure 服務、在 Azure 和內部部署資料來源之間轉送資料，以及處理儲存在 Azure 中的資料。

[![下載 SSIS Feature Pack for Azure](/analysis-services/analysis-services/media/download.png)](https://www.microsoft.com/download/details.aspx?id=100430) **下載**

- SQL Server 2019 - [Microsoft SQL Server 2019 Integration Services Feature Pack for Azure](https://www.microsoft.com/download/details.aspx?id=100430)
- SQL Server 2017 - [Microsoft SQL Server 2017 Integration Services Feature Pack for Azure](https://www.microsoft.com/download/details.aspx?id=54798)
- SQL Server 2016 - [Microsoft SQL Server 2016 Integration Services Feature Pack for Azure](https://www.microsoft.com/download/details.aspx?id=49492)
- SQL Server 2014 - [Microsoft SQL Server 2014 Integration Services Feature Pack for Azure](https://www.microsoft.com/download/details.aspx?id=47366)
- SQL Server 2012 - [Microsoft SQL Server 2012 Integration Services Feature Pack for Azure](https://www.microsoft.com/download/details.aspx?id=47367)

下載頁面也包含必要條件的相關資訊。 請確定您在伺服器上安裝 Azure Feature Pack 之前安裝 SQL Server，或當您將套件部署至伺服器上的 SSIS 目錄資料庫 (SSISDB) 時，Feature Pack 中的元件可能無法使用。

## <a name="components-in-the-feature-pack"></a>Feature Pack 中的元件
-   連接管理員

    -   [Azure Data Lake Analytics Connection Manager](connection-manager/azure-data-lake-analytics-connection-manager.md)

    -   [Azure Data Lake Store 連線管理員](../integration-services/connection-manager/azure-data-lake-store-connection-manager.md)
    
    -   [Azure HDInsight 連線管理員](../integration-services/connection-manager/azure-hdinsight-connection-manager.md)

    -   [Azure Resource Manager 連線管理員](../integration-services/connection-manager/azure-resource-manager-connection-manager.md)
    
    -   [Azure 儲存體連線管理員](../integration-services/connection-manager/azure-storage-connection-manager.md)

    -   [Azure 訂用帳戶連線管理員](../integration-services/connection-manager/azure-subscription-connection-manager.md)
    
-   工作

    -   [Azure Blob 下載工作](../integration-services/control-flow/azure-blob-download-task.md)

    -   [Azure Blob 上傳工作](../integration-services/control-flow/azure-blob-upload-task.md)

    -   [Azure Data Lake Analytics 工作](control-flow/azure-data-lake-analytics-task.md)

    -   [Azure Data Lake Store 檔案系統工作](../integration-services/control-flow/azure-data-lake-store-file-system-task.md)

    -   [Azure HDInsight 建立叢集工作](../integration-services/control-flow/azure-hdinsight-create-cluster-task.md)

    -   [Azure HDInsight 刪除叢集工作](../integration-services/control-flow/azure-hdinsight-delete-cluster-task.md)
    
    -   [Azure HDInsight Hive 工作](../integration-services/control-flow/azure-hdinsight-hive-task.md)

    -   [Azure HDInsight Pig 工作](../integration-services/control-flow/azure-hdinsight-pig-task.md)

    -   [Azure SQL DW 上傳工作](../integration-services/control-flow/azure-sql-dw-upload-task.md)

    -   [彈性檔案工作](../integration-services/control-flow/flexible-file-task.md)

-   資料流程元件

    -   [Azure Blob 來源](../integration-services/data-flow/azure-blob-source.md)

    -   [Azure Blob 目的地](../integration-services/data-flow/azure-blob-destination.md)
    
    -   [Azure Data Lake Store 來源](../integration-services/data-flow/azure-data-lake-store-source.md)
    
    -   [Azure Data Lake Store 目的地](../integration-services/data-flow/azure-data-lake-store-destination.md)

    -   [彈性檔案來源](../integration-services/data-flow/flexible-file-source.md)

    -   [彈性檔案目的地](../integration-services/data-flow/flexible-file-destination.md)

-   Azure Blob、Azure Data Lake Store 和 Data Lake Storage Gen2 檔案列舉程式。 請參閱 [Foreach 迴圈容器](../integration-services/control-flow/foreach-loop-container.md)

## <a name="use-tls-12"></a>使用 TLS 1.2

Azure Feature Pack 所使用的 TLS 版本遵循系統 .NET Framework 設定。
若要使用 TLS 1.2，請在下列兩個登錄機碼下方，新增名為 `SchUseStrongCrypto` 的 `REG_DWORD` 值和資料 `1`。

1. `HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319`
2. `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319`

## <a name="dependency-on-java"></a>Java 的相依性

需要 Java 才能搭配 Azure Data Lake Store/彈性檔案連接器使用 ORC/Parquet 檔案格式。  
Java 組建架構 (32/64 位元) 應該符合要使用的 SSIS 執行階段架構。
下列 JAVA 組建已經過測試。

- [Zulu 的 OpenJDK 8u192](https://www.azul.com/downloads/zulu/zulu-windows/)
- [Oracle 的 Java SE Runtime Environment 8u192](https://www.oracle.com/technetwork/java/javase/downloads/java-archive-javase8-2177648.html)

### <a name="set-up-zulus-openjdk"></a>設定 Zulu 的 OpenJDK

1. 下載並解壓縮安裝 ZIP 套件。
2. 從命令提示字元中，執行 `sysdm.cpl`。
3. 在 [進階]  索引標籤上，選取 [環境變數]  。
4. 在 [系統變數]  區段底下，選取 [新增]  。
5. 針對 [變數名稱]  輸入 `JAVA_HOME`。
6. 選取 [瀏覽目錄]  ，巡覽至解壓縮的資料夾，然後選取 `jre` 子資料夾。
   然後選取 [確定]  ，系統會自動填入 [變數值]  。
7. 選取 [確定]  以關閉 [新增系統變數]  對話方塊。
8. 選取 [確定]  以關閉 [環境變數]  對話方塊。
9. 選取 [確定]  以關閉 [系統內容]  對話方塊。

> [!TIP]
> 如果您使用 Parquet 格式並遇到錯誤，指出「叫用 Java 時發生錯誤，訊息:**java.lang.OutOfMemoryError:Java heap space**」，您可以新增環境變數 *`_JAVA_OPTIONS`* ，以調整 JVM 的最小/最大堆積大小。
>
>![JVM 堆積](media/azure-feature-pack-jvm-heap-size.png)
>
> 範例：將變數 *`_JAVA_OPTIONS`* 的值設定為 *`-Xms256m -Xmx16g`* 。 旗標 Xms 指定 Java 虛擬機器 (JVM) 的初始記憶體配置集區，而 Xmx 指定記憶體配置集區的最大值。 這表示 JVM 啟動時有 *`Xms`* 數量的記憶體，且最多可以使用 *`Xmx`* 數量的記憶體。 預設值為最小 64MB 和最大 1G。

### <a name="set-up-zulus-openjdk-on-azure-ssis-integration-runtime"></a>在 Azure-SSIS Integration Runtime 上設定 Zulu 的 OpenJDK

這應該透過 Azure-SSIS Integration Runtime 的[自訂設定介面](/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)來完成。
假設使用 `zulu8.33.0.1-jdk8.0.192-win_x64.zip`。
Blob 容器的組織方式如下。

~~~
main.cmd
install_openjdk.ps1
zulu8.33.0.1-jdk8.0.192-win_x64.zip
~~~

作為進入點，`main.cmd` 會觸發 PowerShell 指令碼 `install_openjdk.ps1` 的執行，該指令碼會解壓縮 `zulu8.33.0.1-jdk8.0.192-win_x64.zip`，從而設定 `JAVA_HOME`。

**main.cmd**

~~~
powershell.exe -file install_openjdk.ps1
~~~

> [!TIP]
> 如果您使用 Parquet 格式並遇到錯誤，指出「叫用 Java 時發生錯誤，訊息:**java.lang.OutOfMemoryError:Java heap space**」，您可以在 *`main.cmd`* 中新增命令，以調整 JVM 的最小/最大堆積大小。 範例：
> ~~~
> setx /M _JAVA_OPTIONS "-Xms256m -Xmx16g"
> ~~~
> 旗標 Xms 指定 Java 虛擬機器 (JVM) 的初始記憶體配置集區，而 Xmx 指定記憶體配置集區的最大值。 這表示 JVM 啟動時有 *`Xms`* 數量的記憶體，且最多可以使用 *`Xmx`* 數量的記憶體。 預設值為最小 64MB 和最大 1G。

**install_openjdk.ps1**

~~~
Expand-Archive zulu8.33.0.1-jdk8.0.192-win_x64.zip -DestinationPath C:\
[Environment]::SetEnvironmentVariable("JAVA_HOME", "C:\zulu8.33.0.1-jdk8.0.192-win_x64\jre", "Machine")
~~~

### <a name="set-up-oracles-java-se-runtime-environment"></a>設定 Oracle 的 Java SE Runtime Environment

1. 下載並執行 exe 安裝程式。
2. 依照安裝程式指示來完成安裝。

## <a name="scenario-processing-big-data"></a>案例：處理巨量資料
 您可以使用 Azure 連接器來完成下列巨量資料處理工作：

1.  使用 Azure Blob 上傳工作，將輸入資料上傳至 Azure Blob 儲存體。

2.  使用 Azure HDInsight 建立叢集工作，建立 Azure HDInsight 叢集。 如果您要使用自己的叢集，這是選擇性步驟。

3.  使用 Azure HDInsight 登錄區工作或 Azure HDInsight Pig 工作，在 Azure HDInsight 叢集上叫用 Pig 或登錄區工作。

4.  如果在步驟 2 中建立了隨選 HDInsight 叢集，在使用過後，可使用 Azure HDInsight 刪除叢集工作來刪除該 HDInsight 叢集。

5.  使用 Azure HDInsight Blob 下載工作，從 Azure Blob 儲存體下載 Pig/登錄區輸出資料。

![顯示 SSIS Azure 連接器巨量資料案例的螢幕擷取畫面。](../integration-services/media/ssis-azureconnector-bigdatascenario.png)
 
## <a name="scenario-managing-data-in-the-cloud"></a>案例：管理雲端中的資料
 您可以使用 SSIS 封裝中的 Azure Blob 目的地，將輸出資料寫入 Azure Blob 儲存體，或者使用 Azure Blob 來源，從 Azure Blob 儲存體讀取資料。

![顯示從 OLE DB 來源到 Azure Blob 目的地的資料流程螢幕擷取畫面。](../integration-services/media/ssis-azureconnector-cloudarchive-1.png)
 
 ![顯示從 Azure Blob 來源到 OLE DB 目的地的資料流程螢幕擷取畫面。](../integration-services/media/ssis-azureconnector-cloudarchive-2.png)

 您可以搭配 Azure Blob 列舉程式使用 Foreach 迴圈容器，來處理多個 Bob 檔案中的資料。

![顯示控制流程 Foreach 迴圈容器的螢幕擷取畫面。](../integration-services/media/ssis-azureconnector-cloudarchive-3.png)

## <a name="release-notes"></a>版本資訊

### <a name="version-1190"></a>版本1.19.0

#### <a name="improvements"></a>改善項目

1. 新增支援 Azure 儲存體連線管理員的共用存取簽章驗證。

### <a name="version-1180"></a>版本 1.18.0

#### <a name="improvements"></a>改善項目

1. 針對彈性檔案工作的三項功能改善：(1) 已新增複製/刪除作業的萬用字元支援；(2) 使用者可以啟用/停用刪除作業的遞迴搜尋；(3) 複製作業的目的地檔案名稱可為空白，以便保留來源檔案名稱。

### <a name="version-1170"></a>1\.17.0 版

這是僅針對 SQL Server 2019 發行的 Hotfix 版本。

#### <a name="bugfixes"></a>Bug 修正

1. 在 Visual Studio 2019 中執行並以 SQL Server 2019 為目標時，彈性檔案工作/來源/目的地可能會失敗，並出現錯誤訊息 `Attempted to access an element as a type incompatible with the array.`
1. 在 Visual Studio 2019 中執行並以 SQL Server 2019 為目標時，使用 ORC/Parquet 格式的彈性檔案來源/目的地可能會失敗，並出現錯誤訊息 `Microsoft.DataTransfer.Common.Shared.HybridDeliveryException: An unknown error occurred. JNI.JavaExceptionCheckException.`

### <a name="version-1160"></a>版本 1.16.0

#### <a name="bugfixes"></a>Bug 修正

1. 在某些情況下，套件執行會回報「錯誤:無法載入檔案或組件檔 ‘Newtonsoft.Json, Version=11.0.0.0, Culture=neutral, PublicKeyToken=30ad4fe6b2a6aeed’ 或它的其中一個相依性。」

### <a name="version-1150"></a>版本 1.15.0

#### <a name="improvements"></a>改善項目

1. 將刪除資料夾/檔案作業新增至彈性檔案工作
1. 在彈性檔案來源中新增外部/輸出資料類型轉換函式

#### <a name="bugfixes"></a>Bug 修正

1. 在某些情況下，Data Lake Storage Gen2 的測試連線會發生問題，並附帶錯誤訊息「嘗試以和陣列不相容的類型存取元素」
1. 恢復對 Azure 儲存體模擬器的支援