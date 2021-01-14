---
title: 教學課程：使用 R 開發群集模型
titleSuffix: SQL Machine Learning
description: 在這個四部分教學課程系列中，您將開發一個模型，使用 SQL 機器學習在 R 中執行群集。
ms.prod: sql
ms.technology: machine-learning
ms.topic: tutorial
author: dphansen
ms.author: davidph
ms.reviewer: garye, davidph
ms.date: 05/26/2020
ms.custom: seo-lt-2019
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=azuresqldb-mi-current'
ms.openlocfilehash: 1fccd0dfff453b4fa1529d3cc03d5d8fd635ae19
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98101888"
---
# <a name="tutorial-develop-a-clustering-model-in-r-with-sql-machine-learning"></a>教學課程：使用 SQL 機器學習在 R 中部署群集模型
[!INCLUDE [SQL Server 2016 SQL MI](../../includes/applies-to-version/sqlserver2016-asdbmi.md)]

::: moniker range=">=sql-server-ver15||>=sql-server-linux-ver15"
在本教學課程系列中 (總共四個部分)，您將使用 R，在 [SQL Server 機器學習服務](../sql-server-machine-learning-services.md)中或在[巨量資料叢集](../../big-data-cluster/machine-learning-services.md)上開發和部署 K-Means 群集模型，以分類客戶資料。
::: moniker-end
::: moniker range="=sql-server-2017"
在本教學課程系列中 (總共四個部分)，您將使用 R，在 [SQL Server 機器學習服務](../sql-server-machine-learning-services.md)中開發和部署 K-Means 群集模型，以群集客戶資料。
::: moniker-end
::: moniker range="=sql-server-2016"
在本教學課程系列中 (總共四個部分)，您將使用 R，在 [SQL Server R Services](../r/sql-server-r-services.md) 中開發和部署 K-Means 群集模型，以群集客戶資料。
::: moniker-end
::: moniker range="=azuresqldb-mi-current"
在本教學課程系列中 (總共四個部分)，您將使用 R 在 [Azure SQL 受控執行個體機器學習服務](/azure/azure-sql/managed-instance/machine-learning-services-overview)中，開發及部署 K-Means 群集模型，以群集客戶資料。
::: moniker-end

在本系列的第一部分中，您將設定本教學課程的必要條件，然後將範例資料集還原至資料庫。 在第二部分和第三部分中，您將在 Azure Data Studio 筆記本中開發一些 R 指令碼來分析和準備此範例資料，並定型機器學習模型。 接著在第四部分中，您將使用預存程序在資料庫內執行這些 R 指令碼。

*叢集* 可以解釋成將資料組織成群組，而群組的成員在某些方面是相似的。 在本教學課程系列中，假設您有一家零售公司。 您將使用 **K-Means** 演算法在產品購買和退貨資料集中，執行客戶叢集。 透過將客戶叢集，您可以鎖定特定群組，以更有效率地專注於行銷工作。 K-Means 叢集是 *非監督式學習* 演算法，會根據相似性找出資料中的模式。

在本文中，您將學會如何：

> [!div class="checklist"]
> * 還原範例資料庫

在[第二部分](r-clustering-model-prepare-data.md)，您將了解如何準備資料庫中的資料，以執行群集。

在[第三部分](r-clustering-model-build.md)中，您將了解如何在 R 中建立和定型 K-Means 群集模型。

在[第四部分](r-clustering-model-deploy.md)中，您將了解如何在資料庫中建立預存程序，以根據新的資料在 R 中執行群集。

## <a name="prerequisites"></a>Prerequisites

::: moniker range=">=sql-server-ver15||>=sql-server-linux-ver15"
* [SQL Server 機器學習服務](../sql-server-machine-learning-services.md)與 Python 語言選項 - 請遵循 [Windows 安裝指南](../install/sql-machine-learning-services-windows-install.md)或 [Linux 安裝指南](../../linux/sql-server-linux-setup-machine-learning.md?toc=%252fsql%252fmachine-learning%252ftoc.json&view=sql-server-linux-ver15&preserve-view=true)中的安裝指示。 您也可以[啟用 SQL Server 巨量資料叢集上的機器學習服務](../../big-data-cluster/machine-learning-services.md)。
::: moniker-end
::: moniker range="=sql-server-2017"
* [SQL Server 機器學習服務](../sql-server-machine-learning-services.md)與 R 語言選項 - 請遵循 [Windows 安裝指南](../install/sql-machine-learning-services-windows-install.md)中的安裝指示。
::: moniker-end
::: moniker range="=azuresqldb-mi-current"
* Azure SQL 受控執行個體機器學習服務。 如需詳細資訊，請參閱 [Azure SQL 受控執行個體機器學習服務概觀](/azure/azure-sql/managed-instance/machine-learning-services-overview)。

* 請參閱 [SQL Server Management Studio](../../ssms/download-sql-server-management-studio-ssms.md)，以了解如何將範例資料庫還原到 Azure SQL 受控執行個體。
::: moniker-end

* [Azure Data Studio](../../azure-data-studio/what-is-azure-data-studio.md)。 您會在 Azure Data Studio 中使用適用於 SQL 的筆記本。 如需筆記本的詳細資訊，請參閱[如何在 Azure Data Studio 中使用筆記本](../../azure-data-studio/notebooks/notebooks-guidance.md)。

* R IDE - 本教學課程使用 [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/)。

* RODBC - 此驅動程式用於您將在本教學課程中開發的 R 指令碼。 如果尚未安裝，請使用 R 命令 `install.packages("RODBC")` 進行安裝。 如需 RODBC 的詳細資訊，請參閱 [CRAN - 封裝 RODBC](https://CRAN.R-project.org/package=RODBC)。

## <a name="restore-the-sample-database"></a>還原範例資料庫

本教學課程中使用的範例資料集已儲存到 **.bak** 資料庫備份檔案中，供您下載和使用。 此資料集衍生自 [tpcx-bb](http://www.tpc.org/tpcx-bb/default5.asp) 資料集 (由 [Transaction Processing Performance Council (TPC)](http://www.tpc.org/) 提供)。

::: moniker range=">=sql-server-ver15||>=sql-server-linux-ver15"
> [!NOTE]
> 如果您是在巨量資料叢集上使用機器學習服務，請參閱如何[將資料庫還原至 SQL Server 巨量資料叢集主要執行個體](../../big-data-cluster/data-ingestion-restore-database.md)。
::: moniker-end

::: moniker range=">=sql-server-2017||>=sql-server-linux-ver15"
1. 下載 [tpcxbb_1gb.bak](https://sqlchoice.blob.core.windows.net/sqlchoice/static/tpcxbb_1gb.bak) 檔案。

1. 請遵循在 Azure Data Studio 中[從備份檔案還原資料庫](../../azure-data-studio/tutorial-backup-restore-sql-server.md#restore-a-database-from-a-backup-file)中的指示，使用下列詳細資料：

   * 從您下載的 **tpcxbb_1gb.bak** 檔案匯入
   * 將目標資料庫命名為 "tpcxbb_1gb"

1. 您可以藉由查詢 **dbo.customer** 資料表，確認資料集在還原資料庫後是否存在：

    ```sql
    USE tpcxbb_1gb;
    SELECT * FROM [dbo].[customer];
    ```
::: moniker-end
::: moniker range="=azuresqldb-mi-current"
1. 下載 [tpcxbb_1gb.bak](https://sqlchoice.blob.core.windows.net/sqlchoice/static/tpcxbb_1gb.bak) 檔案。

1. 遵循 SQL Server Management Studio 中[將資料庫還原至受控執行個體](/azure/sql-database/sql-database-managed-instance-get-started-restore)的指引，使用以下詳細資料：

   * 從您下載的 **tpcxbb_1gb.bak** 檔案匯入
   * 將目標資料庫命名為 "tpcxbb_1gb"

1. 您可以藉由查詢 **dbo.customer** 資料表，確認資料集在還原資料庫後是否存在：

    ```sql
    USE tpcxbb_1gb;
    SELECT * FROM [dbo].[customer];
    ```
::: moniker-end

## <a name="clean-up-resources"></a>清除資源

如果您不打算繼續進行本教學課程，請刪除 tpcxbb_1gb 資料庫。

## <a name="next-steps"></a>後續步驟

在本教學課程系列的第一部分中，您已完成下列步驟：

* 安裝了必要條件
* 還原範例資料庫

若要針對機器學習模型準備資料，請遵循本教學課程系列的第二部分進行：

> [!div class="nextstepaction"]
> [準備執行群集所需的資料](r-clustering-model-prepare-data.md)