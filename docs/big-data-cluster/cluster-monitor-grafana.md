---
title: 使用 Grafana 儀表板監視叢集
titleSuffix: SQL Server Big Data Clusters
description: 在 SQL Server 2019 巨量資料叢集上使用 Grafana 儀表板監視叢集。
author: cloudmelon
ms.author: melqin
ms.reviewer: mikeray
ms.metadata: seo-lt-2019
ms.date: 09/22/2020
ms.topic: conceptual
ms.prod: sql
ms.technology: big-data-cluster
ms.openlocfilehash: ee0ceb3a9f86fc2a7fabe44e9279b7e63dabfb8b
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98091683"
---
# <a name="monitor-cluster-with-azdata-and-grafana-dashboard"></a>使用 azdata 和 Grafana 儀表板監視叢集

此文章描述如何監視 SQL Server 巨量資料叢集內的應用程式。

## <a name="prerequisites"></a>Prerequisites

- [SQL Server 2019 巨量資料叢集](deployment-guidance.md)
- [azdata 命令列公用程式](../azdata/install/deploy-install-azdata.md)

## <a name="capabilities"></a>功能

在 SQL Server 2019 中，您可以建立、刪除、描述、初始化、列出執行及更新您的應用程式。 下表描述您可以搭配 **azdata** 使用的應用程式部署命令。

|Command |描述 |
|:---|:---|
|`azdata bdc endpoint list` | 列出巨量資料叢集的端點。 |


您可以使用下列範例列出 **Grafana 儀表板** 的端點：

```bash
azdata bdc endpoint list --endpoint-name metricsui 
```

輸出將會為您提供端點，您可以使用您的叢集使用者名稱與密碼登入。 

![Grafana 儀表板](media/big-data-cluster-monitor-apps/grafana-dashboard-endpoint.png)

`nodeMetricsUrl` 和 `sqlMetricsUrl` 值會連結至 Grafana 儀表板，以監視 Kubernetes 節點計量和巨量資料叢集服務計量：

![Grafana 儀表板](./media/view-cluster-status/grafana-dashboard.png)

![SQL](./media/view-cluster-status/grafana-sql-status.png)



## <a name="next-steps"></a>後續步驟

如需 [!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)]的詳細資訊，請參閱[什麼是 [!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ver15.md)]？](big-data-cluster-overview.md)。