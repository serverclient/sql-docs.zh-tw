---
title: Python 教學課程：滑雪工具租用
titleSuffix: SQL machine learning
description: 在這個四部分教學課程系列中，您將會使用 SQL 機器學習，在 Python 中建置線性迴歸模型來預測滑雪工具租用。
ms.prod: sql
ms.technology: machine-learning
ms.date: 05/26/2020
ms.topic: tutorial
author: dphansen
ms.author: davidph
ms.custom: seo-lt-2019
monikerRange: '>=sql-server-2017||>=sql-server-linux-ver15||=azuresqldb-mi-current'
ms.openlocfilehash: f63ffdc74fab82ca5bbf803e3dbc34c66ae5e629
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98101978"
---
# <a name="python-tutorial-predict-ski-rental-with-linear-regression-with-sql-machine-learning"></a>Python 教學課程：使用 SQL 機器學習搭配線性迴歸來預測滑雪工具租用
[!INCLUDE [SQL Server 2017 SQL MI](../../includes/applies-to-version/sqlserver2017-asdbmi.md)]

::: moniker range=">=sql-server-ver15||>=sql-server-linux-ver15"
在這個教學課程系列中 (總共四個部分)，您將在 [SQL Server 機器學習服務](../sql-server-machine-learning-services.md)中或在[巨量資料叢集](../../big-data-cluster/machine-learning-services.md)上使用 Python 和線性迴歸來預測滑雪工具租用的數目。 本教學課程使用 [Azure Data Studio 中的 Python 筆記本](../../azure-data-studio/notebooks/notebooks-guidance.md)。
::: moniker-end
::: moniker range="=sql-server-2017"
在這個教學課程系列中 (總共四個部分)，您將在 [SQL Server 機器學習服務](../sql-server-machine-learning-services.md)中使用 Python 和線性迴歸來預測滑雪工具租用的數目。 本教學課程使用 [Azure Data Studio 中的 Python 筆記本](../../azure-data-studio/notebooks/notebooks-guidance.md)。
::: moniker-end
::: moniker range="=azuresqldb-mi-current"
在這個四部分教學課程系列中，您會在 [Azure SQL 受控執行個體機器學習服務](/azure/azure-sql/managed-instance/machine-learning-services-overview)中，使用 Python 和線性迴歸來預測租用的滑雪板數目。 本教學課程使用 [Azure Data Studio 中的 Python 筆記本](../../azure-data-studio/notebooks/notebooks-guidance.md)。
::: moniker-end

假設您擁有滑雪工具租用公司，而且想要預測未來日期的租用次數。 此資訊可協助您準備好庫存、員工和設備。

在此系列課程的第一部分中，您將會設定必要條件。 在第二部分和第三部分中，您將在筆記本中開發一些 Python 指令碼來準備您的資料，並定型機器學習模型。 接著在第三部分中，您將使用 T-SQL 預存程序來執行資料庫中的這些 Python 指令碼。

在本文中，您將學會如何：

> [!div class="checklist"]
> * 匯入範例資料庫

在[第二部分](python-ski-rental-linear-regression-prepare-data.md)中，您會了解如何將資料從資料庫載入到 Python 資料框架，並以 Python 準備資料。

在[第三部分](python-ski-rental-linear-regression-train-model.md)中，您將了解如何在 Python 中定型線性迴歸模型。

在[第四部分](python-ski-rental-linear-regression-deploy-model.md)中，您將了解如何將模型儲存在資料庫中，然後從您在第二和第三部分中開發的 Python 指令碼建立預存程序。 預存程序將會在伺服器上中執行，以根據新資料進行預測。

## <a name="prerequisites"></a>Prerequisites

::: moniker range=">=sql-server-ver15||>=sql-server-linux-ver15"
* SQL Server 機器學習服務 - 若要安裝機器學習服務，請參閱 [Windows 安裝指南](../install/sql-machine-learning-services-windows-install.md)或 [Linux 安裝指南](../../linux/sql-server-linux-setup-machine-learning.md?toc=%2Fsql%2Fmachine-learning%2Ftoc.json)。 您也可以[啟用 SQL Server 巨量資料叢集上的機器學習服務](../../big-data-cluster/machine-learning-services.md)。
::: moniker-end
::: moniker range="=sql-server-2017"
* SQL Server 機器學習服務 - 若要安裝機器學習服務，請參閱 [Windows 安裝指南](../install/sql-machine-learning-services-windows-install.md)。 
::: moniker-end
::: moniker range="=azuresqldb-mi-current"
* Azure SQL 受控執行個體機器學習服務 - 如需詳細資訊，請參閱 [Azure SQL 受控執行個體機器學習服務概觀](/azure/azure-sql/managed-instance/machine-learning-services-overview)。

* 請參閱 [SQL Server Management Studio](../../ssms/download-sql-server-management-studio-ssms.md)，以了解如何將範例資料庫還原到 Azure SQL 受控執行個體。
::: moniker-end

* Python IDE - 本教學課程使用 [Azure Data Studio](../../azure-data-studio/what-is-azure-data-studio.md) 中的 Python 筆記本。 如需詳細資訊，請參閱[如何在 Azure Data Studio 中使用筆記本](../../azure-data-studio/notebooks/notebooks-guidance.md)。

* SQL 查詢工具 - 本教學課程假設您使用 [Azure Data Studio](../../azure-data-studio/what-is-azure-data-studio.md)。

* 其他 Python 套件 - 此教學課程系列中的範例會使用下列 Python 套件，這些套件預設可能不會安裝：

  * pandas
  * pyodbc
  * sklearn

  若要安裝這些套件：
  1. 在 Azure Data Studio 筆記本中，選取 [管理套件]。
  2. 在 [管理套件] 窗格中，選取 [新增] 索引標籤。
  3. 針對每個下列套件，輸入套件名稱，按一下 [搜尋]，然後按一下 [安裝]。

  或者，您可以開啟 [命令提示字元]，變更至您用於 Azure Data Studio 之 Python 版本的安裝路徑 (例如 `cd %LocalAppData%\Programs\Python\Python37-32`)，然後針對每個套件執行 `pip install`。

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
* 匯入範例資料庫

若要準備 TutorialDB 資料庫中的資料，請遵循本教學課程系列的第二部分：

> [!div class="nextstepaction"]
> [Python 教學課程：準備資料以定型線性迴歸模型](python-ski-rental-linear-regression-prepare-data.md)