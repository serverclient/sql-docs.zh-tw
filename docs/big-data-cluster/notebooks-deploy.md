---
title: 部署：Azure Data Studio 筆記本
titleSuffix: SQL Server Big Data Clusters
description: 了解如何從 Azure Data Studio 使用筆記本中的程式碼與文件，來部署 SQL Server 巨量資料叢集。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: mihaelab
ms.metadata: seo-lt-2019
ms.date: 12/13/2019
ms.topic: conceptual
ms.prod: sql
ms.technology: big-data-cluster
ms.openlocfilehash: 868c7685c59ae1ccf43057fdd03dfbb0b5bdaf21
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97637324"
---
# <a name="deploy-sql-server-big-data-cluster-with-azure-data-studio-notebook"></a>使用 Azure Data Studio 筆記本部署 SQL Server 巨量資料叢集

[!INCLUDE[SQL Server 2019](../includes/applies-to-version/sqlserver2019.md)]

[!INCLUDE[sql-server-2019](../includes/sssqlv15-md.md)] 提供包含部署筆記本的 Azure Data Studio 擴充功能。 部署筆記本所包含的文件和程式碼，可供您用來在 Azure Data Studio 中建立 SQL Server 巨量資料叢集。

[筆記本](../azure-data-studio/notebooks/notebooks-guidance.md)在實作之初是開放原始碼專案的型態，現已實作到 [Azure Data Studio](../azure-data-studio/download-azure-data-studio.md) 之中。 您現在可以將 Markdown 用於文字儲存格中的文字，也可以使用其中一個可用的核心，在程式碼儲存格中撰寫程式碼。

您可以使用筆記本來部署 [!INCLUDE[sql-server-2019](../includes/sssqlv15-md.md)] 的巨量資料叢集。

## <a name="prerequisites"></a>Prerequisites

您必須滿足下列必要條件，才能啟動筆記本：

* 已安裝最新版的 [Azure Data Studio 測試人員組建](https://github.com/microsoft/azuredatastudio#try-out-the-latest-insiders-build-from-master)

除了上述，部署 SQL Server 2019 巨量資料叢集也需要：

* [azdata](../azdata/install/deploy-install-azdata.md)
* [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-kubectl-binary-using-native-package-management)
* [Azure CLI (若是部署在 Azure 中)](/cli/azure/install-azure-cli)

## <a name="launch-the-notebook"></a>啟動筆記本

1. 啟動 Azure Data Studio。

2. 在 [連線]  索引標籤中，依多選取省略符號 ( **...** ) 及 [部署 SQL Server]  。

   ![部署 SQL Server](media/notebooks-deploy/deploy-notebooks.png)

3. 從部署選項中，選取 [SQL Server 巨量資料叢集]  。

4. 在 [Options]  \(選項\) 下的 [Deployment Target]  \(部署目標\) 中，選取 [New Azure Kubernetes Cluster]  \(新增 Azure Kubernetes 叢集\) 或 [Existing Azure Kubernetes Service cluster]  \(現有的 Azure Kubernetes Service 叢集\)。

5. 接受隱私權與授權條款

6. 此對話方塊也會檢查主機上是否有所選 SQL 部署類型所需的工具。 工具檢查成功之後，才會啟用 [選取]  按鈕。

7. 選取 [選取]  按鈕。 此動作會啟動部署體驗。

## <a name="set-deployment-configuration-template"></a>設定部署設定範本

您可以依照下列指示，自訂部署設定檔的設定。

### <a name="target-configuration-template"></a>目標設定範本

從提供的範本中，選取目標設定範本。 可用的設定檔會依據在上個對話方塊中選擇的部署目標類型進行篩選。

   ![部署設定範本步驟 1](media/notebooks-deploy/deployment-configuration-template.png)

### <a name="azure-settings"></a>Azure 設定

若部署目標是新的 AKS，便須在建立 AKS 叢集時，提供額外的資訊，例如 Azure 訂閱識別碼、資源群組、AKS 叢集名稱、VM 計數、大小及其他額外的資訊。

   ![Azure 設定](media/notebooks-deploy/azure-settings.png)

若部署目標是現有的 Kubernetes 叢集，精靈會提示您輸入 *kube* 設定檔的路徑，以匯入 Kubernetes 叢集設定。 請確認已選取適當的叢集內容，以便於部署 SQL Server 2019 巨量資料叢集。

   ![目標叢集內容](media/notebooks-deploy/target-cluster-context.png)

### <a name="cluster-docker-and-ad-settings"></a>叢集、Docker 與 AD 設定

1. 輸入 SQL Server 2019 BDC 的叢集名稱、系統管理員名稱及密碼。
注意:控制器與 SQL Server 會使用相同的帳戶。

   ![叢集設定](media/notebooks-deploy/cluster-settings.png)

2. 輸入適當的 Docker 設定

   ![Docker 設定](media/notebooks-deploy/docker-settings.png)

3. 如能使用 AD 驗證，請輸入 AD 設定

   ![Active Directory 設定](media/notebooks-deploy/active-directory-settings.png)

### <a name="service-settings"></a>服務設定

此畫面包含多項不同設定的輸入，例如 [調整大小]  、[端點]  、[儲存體]  與其他 [進階儲存體設定]  。 請輸入適當的值，然後選取 [下一步]  。

#### <a name="scale-settings"></a>調整大小設定

請輸入巨量資料叢集中，每個元件的執行個體數。

Spark 執行個體可包含在 HDFS 中。 其包含於儲存體集區，或個別包含於 Spark 集區中。

   ![服務設定](media/notebooks-deploy/service-settings.png)

如需各項元件的詳細資訊，可以參閱[主要執行個體](concept-master-instance.md)、[資料集區](concept-data-pool.md)、[儲存體集區](concept-storage-pool.md)或[計算集區](concept-compute-pool.md)。

#### <a name="endpoint-settings"></a>端點設定

預設的端點已預先填入， 但可以變更為適當的值。

   ![端點設定](media/notebooks-deploy/endpoint-settings.png)

#### <a name="storage-settings"></a>儲存體設定

儲存體設定包含資料與記錄的儲存體類別及宣告大小。 這些設定可套用到儲存體集區、資料及 SQL Server 主要集區。

   ![儲存體設定](media/notebooks-deploy/storage-settings.png)

#### <a name="advanced-storage-settings"></a>進階儲存體設定

您可以在 [**進階儲存設定**] 底下新增其他儲存設定

* 存放集區 (HDFS)
* 資料集區
* SQL Server Master

   ![進階儲存體設定](media/notebooks-deploy/advanced-storage-settings.png)

### <a name="summary"></a>摘要

此畫面摘錄所有提供用以部署 SQL Server 2019 巨量資料叢集的輸入。 您可以使用 [儲存設定檔]  按鈕下載設定檔。 選取 [將指令碼編寫至筆記本]  ，將整個部署設定的指令碼編寫至筆記本。 開啟筆記本之後，選取 [執行儲存格]  ，開始將 SQL Server 2019 巨量資料叢集部署至所選的目標。

   ![摘要](media/notebooks-deploy/deploy-sql-server-big-data-cluster-on-a-new-AKS-cluster.png)

## <a name="next-steps"></a>後續步驟

如需部署的詳細資訊，請參閱 [SQL Server 巨量資料叢集的部署指導方針](deployment-guidance.md)。