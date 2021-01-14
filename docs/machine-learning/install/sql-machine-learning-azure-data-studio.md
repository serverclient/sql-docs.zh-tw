---
title: Azure Data Studio 筆記本 (Python、R)
description: 了解如何使用 SQL Server 機器學習服務在 Azure Data Studio 的筆記本中執行 Python 和 R 指令碼。
ms.prod: sql
ms.technology: machine-learning-services
ms.date: 03/09/2020
ms.topic: how-to
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2017||>=sql-server-linux-ver15'
ms.openlocfilehash: b1e3d81e0229d028b9fab6a4521ee11cb20ce1b8
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98091544"
---
# <a name="run-python-and-r-scripts-in-azure-data-studio-notebooks-with-sql-server-machine-learning-services"></a>使用 SQL Server 機器學習服務在 Azure Data Studio 筆記本中執行 Python 和 R 指令碼
[!INCLUDE [SQL Server 2017 and later](../../includes/applies-to-version/sqlserver2017.md)]

了解如何使用 [SQL Server 機器學習服務](../../azure-data-studio/what-is-azure-data-studio.md)在 [Azure Data Studio](../sql-server-machine-learning-services.md) 筆記本中執行 Python 和 R 指令碼。 Azure Data Studio 是跨平台的資料庫工具。

## <a name="prerequisites"></a>Prerequisites

- 在您的工作站電腦上[下載並安裝 Azure Data Studio](../../azure-data-studio/download-azure-data-studio.md)。 Azure Data Studio 為跨平台產品，可在 Windows、macOS 和 Linux 上執行。

- 已安裝並啟用 SQL Server 機器學習服務的伺服器。 您可以在 Windows、Linux 或巨量資料叢集上使用機器學習服務：

  - [在 Windows 上安裝 SQL Server 機器學習服務](sql-machine-learning-services-windows-install.md)。
  - [在 Linux 上安裝 SQL Server 機器學習服務](../../linux/sql-server-linux-setup-machine-learning.md)。
  - [對安裝有機器學習服務的 SQL Server 巨量資料叢集執行 Python 與 R 指令碼](../../big-data-cluster/machine-learning-services.md)。

## <a name="create-a-sql-notebook"></a>建立 SQL 筆記本

> [!IMPORTANT]
> 機器學習服務會作為 SQL Server 的一部分來執行。 因此，您需要使用 SQL 核心，而不是 Python 核心。

您可以將 Azure Data Studio 中的 機器學習服務與 SQL 筆記本搭配使用。 若要建立新的筆記本，請遵循下列步驟進行：

1. 按一下 [檔案]  和 [新增筆記本]  以建立新的筆記本。 筆記本預設會使用 **SQL 核心**。

1. 按一下 [附加至]  和 [變更連線]  。 

    > [!div class="mx-imgBorder"]
    > ![Azure Data Studio SQL 筆記本的 [變更連線]](media/ads-attach-to-connection.png)
    
1. 連線到現有或新的 SQL Server。 您可以：

    1. 選擇 [最近的連線]  或 [儲存的連線]  下的現有連線。

    1. 在 [連線詳細資料]  下，建立新的連線。 在您的 SQL Server 和資料庫中填入連線詳細資料。

    > [!div class="mx-imgBorder"]
    > ![Azure Data Studio SQL 筆記本的 [連線詳細資料]](media/ads-connection-details.png)  

## <a name="run-python-or-r-scripts"></a>執行 Python 或 R 指令碼

SQL 筆記本包含程式碼和文字資料格。 程式碼資料格可用來透過預存程序 [sp_execute_external_script](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md) 執行 Python 或 R 指令碼。 文字資料格可以用來在筆記本中記錄您的程式碼。

### <a name="run-a-python-script"></a>執行 Python 指令碼

請遵循下列步驟執行 Python 指令碼：

1. 按一下 [+ 程式碼]  新增程式碼資料格。

    > [!div class="mx-imgBorder"]
    > ![Azure Data Studio SQL 筆記本的新增程式碼區塊](media/ads-add-code.png)  

1. 在程式碼資料格中輸入下列指令碼：

    ```sql
    EXECUTE sp_execute_external_script @language = N'Python'
        , @script = N'
    a = 1
    b = 2
    c = a/b
    d = a*b
    print(c, d)
    '
    ```

1. 按一下 **執行資料格** (圓形黑色箭號)，或按 **F5** 鍵來執行單一資料格。

    > [!div class="mx-imgBorder"]
    > ![Azure Data Studio SQL 筆記本的執行 Python 程式碼](media/ads-run-python.png)  

1. 結果會顯示在程式碼資料格底下。

    > [!div class="mx-imgBorder"]
    > ![Azure Data Studio SQL 筆記本的 Python 程式碼輸出](media/ads-run-python-output.png)  

### <a name="run-an-r-script"></a>執行 R 指令碼

請遵循下列步驟執行 R 指令碼：

1. 按一下 [+ 程式碼]  新增程式碼資料格。

    > [!div class="mx-imgBorder"]
    > ![Azure Data Studio SQL 筆記本的新增程式碼區塊](media/ads-add-code.png)  

1. 在程式碼資料格中輸入下列指令碼：

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
    a <- 1
    b <- 2
    c <- a/b
    d <- a*b
    print(c(c, d))
    '
    ```

1. 按一下 **執行資料格** (圓形黑色箭號)，或按 **F5** 鍵來執行單一資料格。

    > [!div class="mx-imgBorder"]
    > ![Azure Data Studio SQL 筆記本的執行 R 程式碼](media/ads-run-r.png)  

1. 結果會顯示在程式碼資料格底下。

    > [!div class="mx-imgBorder"]
    > ![Azure Data Studio SQL 筆記本的 R 程式碼輸出](media/ads-run-r-output.png)  

## <a name="next-steps"></a>後續步驟

- [如何在 Azure Data Studio 中使用筆記本](../../azure-data-studio/notebooks/notebooks-guidance.md)
- [建立並執行 SQL Server 筆記本](../../azure-data-studio/notebooks/notebooks-sql-kernel.md)
- [快速入門：使用 SQL Server 機器學習服務來執行簡單的 Python 指令碼](../tutorials/quickstart-python-create-script.md)
- [快速入門：使用 SQL Server 機器學習服務來執行簡單的 R 指令碼](../tutorials/quickstart-r-create-script.md)