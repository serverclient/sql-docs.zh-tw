---
title: 什麼是巨量資料叢集？
titleSuffix: SQL Server Big Data Clusters
description: 了解在 Kubernetes 上執行的 SQL Server 巨量資料叢集，並為關聯式資料與 HDFS 資料提供向外延展選項。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: mihaelab
ms.date: 01/07/2020
ms.topic: overview
ms.prod: sql
ms.technology: big-data-cluster
ms.openlocfilehash: ced001726b7a8306f2064c1436ddb41cf65a6eb0
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98091804"
---
# <a name="what-are-big-data-clusters-2019"></a>什麼是 [!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)]？

[!INCLUDE[SQL Server 2019](../includes/applies-to-version/sqlserver2019.md)]

從 [!INCLUDE[SQL Server 2019](../includes/sssqlv15-md.md)] 開始，[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)]可讓您部署在 Kubernetes 上執行之 SQL Server、Spark 和 HDFS 容器的可調式叢集。 這些元件會並存執行，可供您讀取、寫入和處理來自 Transact-SQL 或 Spark 的巨量資料，讓您輕鬆地結合與分析具有大量巨量資料的高價值關聯式資料。

請利用 [!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)] 來執行下列動作：

- 為 Kubernetes 上執行的 SQL Server、Spark 和 HDFS 容器[部署可擴充叢集](./deploy-get-started.md)。 
- 讀取、寫入及處理來自 Transact-SQL 或 Spark 的巨量資料。
- 輕鬆結合及分析含有大量巨量資料的高價值關聯式資料。
- 查詢外部資料來源。
- 在 SQL Server 的受控 HDFS 中儲存巨量資料。
- 透過叢集查詢來自多個外部資料來源的資料。
- 使用 AI、機器學習和其他分析工作的資料。
- 在 [!INCLUDE[big-data-clusters](../includes/ssbigdataclusters-nover.md)] 中[部署及執行應用程式](./concept-application-deployment.md)。
- 使用 [PolyBase](../relational-databases/polybase/polybase-guide.md) 將資料虛擬化。 使用外部資料表來查詢來自外部 SQL Server、Oracle、Teradata、MongoDB 及 ODBC 資料來源的資料。
- 使用 Always On 可用性群組技術，為 SQL Server 主要執行個體和所有資料庫提供高可用性。

如需最新版本新功能和已知問題的詳細資訊，請參閱[版本資訊](release-notes-big-data-cluster.md)。

## <a name="scenarios"></a>案例

[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)]可讓您靈活地與您的巨量資料互動。 您可以查詢外部資料源、在 SQL Server 管理的 HDFS 中儲存巨量資料，或透過叢集查詢來自多個外部資料源的資料。 接著您可以使用 AI、機器學習服務和其他分析工作的資料。 下列各節提供這些案例的詳細資訊。

### <a name="data-virtualization"></a>資料虛擬化

藉由利用 [PolyBase](../relational-databases/polybase/polybase-guide.md)，[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)] 可以查詢外部資料來源，無須移動或複製資料。 [!INCLUDE[SQL Server 2019](../includes/sssqlv15-md.md)] 為資料源引進新的連接器。

![資料虛擬化](media/big-data-cluster-overview/data-virtualization.png)

### <a name="data-lake"></a>Data Lake

SQL Server 巨量資料叢集包含可調整的 HDFS「存放集區」  。 這可以用來儲存可能內嵌自多個外部來源的巨量資料。 當巨量資料儲存在巨量資料叢集中的 HDFS 之後，您就可以分析及查詢資料，並將其與關聯式資料結合。

![Data Lake](media/big-data-cluster-overview/data-lake.png)

### <a name="scale-out-data-mart"></a>向外延展資料超市

[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)]提供向外延展計算和儲存體，以改善分析任何資料的效能。 來自各種來源的資料可以作為快取以在「資料集區」  節點之間內嵌並散發來供進一步分析。

![資料超市](media/big-data-cluster-overview/data-mart.png)

### <a name="integrated-ai-and-machine-learning"></a>整合的 AI 與機器學習

[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)]會針對儲存在 HDFS 存放集區和資料集區中的資料啟用 AI 和機器學習工作。 您可以利用 R、Python、Scala 或 JAVA，在 SQL Server 中使用 Spark 和內建 AI 工具。

![AI 和 ML](media/big-data-cluster-overview/ai-ml-spark.png)

### <a name="management-and-monitoring"></a>管理與監控

管理和監視是透過命令列工具、API、入口網站和動態管理檢視的組合來提供。

您可以使用 [Azure Data Studio](../azure-data-studio/what-is-azure-data-studio.md) 對巨量資料叢集執行各種工作：
- 常見管理工作的內建程式碼片段。
- 能夠瀏覽 HDFS、上傳檔案、預覽檔案及建立目錄。
- 能夠建立、開啟並執行與 Jupyter 相容的筆記本。
- 資料虛擬化精靈可簡化外部資料來源的建立流程 (由 **資料虛擬化延伸模組** 啟用)。

## <a name="architecture"></a><a id="architecture"></a> 架構

SQL Server 巨量資料叢集是由 [Kubernetes](https://kubernetes.io/docs/concepts/) 協調的 Linux 容器叢集。

### <a name="kubernetes-concepts"></a>Kubernetes 概念

Kubernetes 是開放原始碼容器協調器，可根據需求調整容器部署。 下表定義一些重要的 Kubernetes 術語：

|詞彙|描述|
|:--|:--|
| **Cluster** | Kubernetes 叢集是稱為節點的一組電腦。 其中一個節點會控制叢集，並受指定為主要節點，而其餘節點為背景工作節點。 Kubernetes 主要節點負責在背景工作節點間散發工作，並監視叢集的健康狀態。 |
| **節點** | 節點會執行容器化應用程式。 節點可以是實體電腦或虛擬機器。 Kubernetes 叢集可包含實體機器和虛擬機器節點的混合。 |
| **Pod** | Pod 是 Kubernetes 的不可部分完成部署單位。 Pod 是執行應用程式所需一或多個容器以及相關聯資源的邏輯群組。 每個 Pod 都會在一個節點上執行，每個節點可以執行一或多個 Pod。 Kubernetes 主機會自動將 Pod 指派給叢集中的節點。 |
| &nbsp; ||

在 [!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)]中，Kubernetes 會負責 [!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)]的狀態；Kubernetes 會建置及設定叢集節點、將 Pod 指派給節點，並監視叢集的健康狀態。

### <a name="big-data-clusters-architecture"></a>巨量資料叢集架構

下圖顯示 SQL Server 巨量資料叢集的元件：

![架構概觀](media/big-data-cluster-overview/architecture-diagram-overview.png)

### <a name="controller"></a><a id="controlplane"></a> 控制器

控制器會提供叢集的管理和安全性。 其中包含控制項服務、設定存放區，以及其他叢集層級的服務 (例如 Kibana、Grafana 和彈性搜尋)。

### <a name="compute-pool"></a><a id="computeplane"></a> 計算集區

計算集區會將計算資源提供給叢集。 其中包含在 Linux Pod 上執行 SQL Server 的節點。 計算集區中的 Pod 會分割成 *SQL 計算執行個體*，以進行特定的處理工作。 

### <a name="data-pool"></a><a id="dataplane"></a> 資料集區

資料集區用於資料持續性和快取。 資料集區由在 Linux 上執行 SQL Server 的一或多個 Pod 所組成。 用於從 SQL 查詢或 Spark 作業中內嵌資料。 SQL Server 巨量資料叢集資料超市會保存在資料集區中。 

### <a name="storage-pool"></a>儲存體集區

存放集區包含由 Linux 上的 SQL Server、Spark 和 HDFS 組成的存放集區 Pod。 SQL Server 巨量資料叢集中的所有存放裝置節點都是 HDFS 叢集成員。

> [!TIP]
> 如需深入了解叢集架構和安裝，請參閱[工作坊：Microsoft [!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)]架構](https://github.com/microsoft/sqlworkshops-bdc) \(英文\)。

## <a name="next-steps"></a>後續步驟

如需部署 [!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)] 的詳細資訊，請參閱[開始使用 [!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)]](deploy-get-started.md)。