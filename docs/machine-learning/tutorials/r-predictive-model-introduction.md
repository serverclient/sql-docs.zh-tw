---
title: 教學課程：使用 R 開發預測模型
titleSuffix: SQL machine learning
description: 在這個四部分教學課程系列中，您將開發資料，以使用 SQL 機器學習在 R 中定型預測模型。
ms.prod: sql
ms.technology: machine-learning
ms.topic: tutorial
author: dphansen
ms.author: davidph
ms.reviewer: garye, davidph
ms.date: 05/26/2020
ms.custom: seo-lt-2019
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=azuresqldb-mi-current'
ms.openlocfilehash: 12441de331661e5edbf40ca678963b80a70764a2
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98101845"
---
# <a name="tutorial-develop-a-predictive-model-in-r-with-sql-machine-learning"></a>教學課程：使用 SQL 機器學習在 R 中部署預測模型
[!INCLUDE [SQL Server 2016 SQL MI](../../includes/applies-to-version/sqlserver2016-asdbmi.md)]

::: moniker range=">=sql-server-ver15||>=sql-server-linux-ver15"
在這個四部分教學課程系列中，您將在 [SQL Server 機器學習服務](../sql-server-machine-learning-services.md)中或在[巨量資料叢集](../../big-data-cluster/machine-learning-services.md)上使用 R 和機器學習模型來預測滑雪工具租用的數目。
::: moniker-end
::: moniker range="=sql-server-2017"
在這個四部分教學課程系列中，您將在 [SQL Server 機器學習服務](../sql-server-machine-learning-services.md)中使用 R 和機器學習模型來預測滑雪工具租用的數目。
::: moniker-end
::: moniker range="=sql-server-2016"
在這個四部分教學課程系列中，您將在 [SQL Server R Services](../r/sql-server-r-services.md) 中使用 R 和機器學習模型來預測滑雪工具租用的數目。
::: moniker-end
::: moniker range="=azuresqldb-mi-current"
在這個四部分教學課程系列中，您將在 [Azure SQL 受控執行個體機器學習服務](/azure/azure-sql/managed-instance/machine-learning-services-overview)中使用 R 和機器學習模型來預測滑雪工具租用的數目。
::: moniker-end

假設您擁有滑雪工具租用公司，而且想要預測未來日期的租用次數。 此資訊可協助您準備好庫存、員工和設備。

在此系列課程的第一部分中，您將會設定必要條件。 在第二部分和第三部分中，您將在筆記本中開發一些 R 指令碼來準備您的資料，並將機器學習模型定型。 然後，在第三部分中，則會使用 T-SQL 預存程序在資料庫內執行這些 R 指令碼。

在本文中，您將學會如何：

> [!div class="checklist"]
> * 還原範例資料庫 

在[第二部分](r-predictive-model-prepare-data.md)中，您會了解如何將資料從資料庫載入到 Python 資料框架，並以 R 準備資料。

在[第三部分](r-predictive-model-train.md)中，您將了解如何在 R 中定型機器學習模型。

在[第四部分](r-predictive-model-deploy.md)中，您將了解如何將模型儲存在資料庫中，然後從您在第二和第三部分中開發的 R 指令碼建立預存程序。 預存程序將會在伺服器上中執行，以根據新資料進行預測。

## <a name="prerequisites"></a>Prerequisites

::: moniker range=">=sql-server-ver15||>=sql-server-linux-ver15"
* SQL Server 機器學習服務 - 若要安裝機器學習服務，請參閱 [Windows 安裝指南](../install/sql-machine-learning-services-windows-install.md)或 [Linux 安裝指南](../../linux/sql-server-linux-setup-machine-learning.md?toc=%2Fsql%2Fmachine-learning%2Ftoc.json)。 您也可以[啟用 SQL Server 巨量資料叢集上的機器學習服務](../../big-data-cluster/machine-learning-services.md)。
::: moniker-end
::: moniker range="=sql-server-2017"
* SQL Server 機器學習服務 - 若要安裝機器學習服務，請參閱 [Windows 安裝指南](../install/sql-machine-learning-services-windows-install.md)。 
::: moniker-end
::: moniker range="=sql-server-2016"
* SQL Server 2016 R Services。 若要安裝 R Services，請參閱 [Windows 安裝指南](../install/sql-r-services-windows-install.md)。 
::: moniker-end
::: moniker range="=azuresqldb-mi-current"
* Azure SQL 受控執行個體機器學習服務。 如需詳細資訊，請參閱 [Azure SQL 受控執行個體機器學習服務概觀](/azure/azure-sql/managed-instance/machine-learning-services-overview)。

* 請參閱 [SQL Server Management Studio](../../ssms/download-sql-server-management-studio-ssms.md)，以了解如何將範例資料庫還原到 Azure SQL 受控執行個體。
::: moniker-end

* R IDE - 本教學課程使用 [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/)。

* RODBC - 此驅動程式用於您將在本教學課程中開發的 R 指令碼。 如果尚未安裝，請使用 R 命令 `install.packages("RODBC")` 進行安裝。 如需 RODBC 的詳細資訊，請參閱 [CRAN - 封裝 RODBC](https://CRAN.R-project.org/package=RODBC)。

* SQL 查詢工具 - 本教學課程假設您使用 [Azure Data Studio](../../azure-data-studio/what-is-azure-data-studio.md)。 如需詳細資訊，請參閱[如何在 Azure Data Studio 中使用筆記本](../../azure-data-studio/notebooks/notebooks-guidance.md)。

## <a name="restore-the-sample-database"></a>還原範例資料庫

此教學課程中使用的範例資料庫已儲存為 **.bak** 資料庫備份檔案，以供您下載並使用。

::: moniker range=">=sql-server-ver15||>=sql-server-linux-ver15"
> [!NOTE]
> 如果您是在巨量資料叢集上使用機器學習服務，請參閱如何[將資料庫還原至 SQL Server 巨量資料叢集主要執行個體](../../big-data-cluster/data-ingestion-restore-database.md)。
::: moniker-end

::: moniker range=">=sql-server-2017||>=sql-server-linux-ver15"
1. 下載 [TutorialDB.bak](https://sqlchoice.blob.core.windows.net/sqlchoice/static/TutorialDB.bak) 檔案。

1. 請遵循在 Azure Data Studio 中[從備份檔案還原資料庫](../../azure-data-studio/tutorial-backup-restore-sql-server.md#restore-a-database-from-a-backup-file)中的指示，使用下列詳細資料：

   * 從您下載的 **TutorialDB** 檔案匯入
   * 將目標資料庫命名為 "TutorialDB"

1. 您可以藉由查詢 **dbo.rental_data** 資料表，確認已還原的資料庫是否存在：

   ```sql
   USE TutorialDB;
   SELECT * FROM [dbo].[rental_data];
   ```
::: moniker-end
::: moniker range="=azuresqldb-mi-current"
1. 下載 [TutorialDB.bak](https://sqlchoice.blob.core.windows.net/sqlchoice/static/TutorialDB.bak) 檔案。

1. 遵循 SQL Server Management Studio 中[將資料庫還原至受控執行個體](/azure/sql-database/sql-database-managed-instance-get-started-restore)的指示，使用下列詳細資料：

   * 從您下載的 **TutorialDB** 檔案匯入
   * 將目標資料庫命名為 "TutorialDB"

1. 您可以藉由查詢 **dbo.rental_data** 資料表，確認已還原的資料庫是否存在：

   ```sql
   USE TutorialDB;
   SELECT * FROM [dbo].[rental_data];
   ```
::: moniker-end

## <a name="clean-up-resources"></a>清除資源

如果您不打算繼續進行本教學課程，請刪除 TutorialDB 資料庫。
## <a name="next-steps"></a>後續步驟

在本教學課程系列的第一部分中，您已完成下列步驟：

* 安裝了必要條件
* 還原範例資料庫

若要針對機器學習模型準備資料，請遵循本教學課程系列的第二部分進行：

> [!div class="nextstepaction"]
> [準備在 R 中定型預測模型所需的資料](r-predictive-model-prepare-data.md)