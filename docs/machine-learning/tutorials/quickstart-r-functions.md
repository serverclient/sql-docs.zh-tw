---
title: 快速入門：R 函式
titleSuffix: SQL machine learning
description: 在此快速入門中，您將會了解如何搭配 SQL 機器學習使用 R 數學與公用程式函式。
ms.prod: sql
ms.technology: machine-learning
ms.date: 05/21/2020
ms.topic: quickstart
author: garyericson
ms.author: garye
ms.custom: seo-lt-2019
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=azuresqldb-mi-current'
ms.openlocfilehash: 0da73dd1202d4b5eab2c14077dfe54b04dfe9bd8
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98092662"
---
# <a name="quickstart-r-functions-with-sql-machine-learning"></a>快速入門：R 函式搭配 SQL 機器學習
[!INCLUDE [SQL Server 2016 SQL MI](../../includes/applies-to-version/sqlserver2016-asdbmi.md)]

::: moniker range=">=sql-server-ver15||>=sql-server-linux-ver15"
在此快速入門中，您將會了解如何搭配 [SQL Server 機器學習服務](../sql-server-machine-learning-services.md)或[巨量資料叢集](../../big-data-cluster/machine-learning-services.md)使用 R 數學與公用程式函式。 以 T-SQL 實作統計函數通常會很複雜，但若使用 R，只需要幾行程式碼就可以完成。
::: moniker-end
::: moniker range="=sql-server-2017"
在此快速入門中，您將會了解如何搭配 [SQL Server 機器學習服務](../sql-server-machine-learning-services.md)使用 R 數學與公用程式函式。 以 T-SQL 實作統計函數通常會很複雜，但若使用 R，只需要幾行程式碼就可以完成。
::: moniker-end
::: moniker range="=sql-server-2016"
在此快速入門中，您將會了解如何搭配 [SQL Server R Services](../r/sql-server-r-services.md) 使用 R 數學與公用程式函式。 以 T-SQL 實作統計函數通常會很複雜，但若使用 R，只需要幾行程式碼就可以完成。
::: moniker-end
::: moniker range="=azuresqldb-mi-current"
在此快速入門中，您將了解在 [Azure SQL 受控執行個體機器學習服務](/azure/azure-sql/managed-instance/machine-learning-services-overview)中使用 R 時，如何使用資料結構與資料類型。 您將了解如何在 R 與 SQL 受控執行個體之間移動資料，以及可能發生的常見問題。
::: moniker-end

## <a name="prerequisites"></a>Prerequisites

您需要符合下列必要條件，才能執行此快速入門。

::: moniker range=">=sql-server-ver15||>=sql-server-linux-ver15"
- SQL Server 機器學習服務。 若要安裝機器學習服務，請參閱 [Windows 安裝指南](../install/sql-machine-learning-services-windows-install.md)或 [Linux 安裝指南](../../linux/sql-server-linux-setup-machine-learning.md?toc=%2Fsql%2Fmachine-learning%2Ftoc.json)。 您也可以[啟用 SQL Server 巨量資料叢集上的機器學習服務](../../big-data-cluster/machine-learning-services.md)。
::: moniker-end
::: moniker range="=sql-server-2017"
- SQL Server 機器學習服務。 若要安裝機器學習服務，請參閱 [Windows 安裝指南](../install/sql-machine-learning-services-windows-install.md)。 
::: moniker-end
::: moniker range="=sql-server-2016"
- SQL Server 2016 R Services。 若要安裝 R Services，請參閱 [Windows 安裝指南](../install/sql-r-services-windows-install.md)。
::: moniker-end
::: moniker range="=azuresqldb-mi-current"
- Azure SQL 受控執行個體機器學習服務。 如需詳細資訊，請參閱 [Azure SQL 受控執行個體機器學習服務概觀](/azure/azure-sql/managed-instance/machine-learning-services-overview)。
::: moniker-end

- 執行 SQL 查詢的工具，這些查詢包含 R 指令碼。 本快速入門使用 [Azure Data Studio](../../azure-data-studio/what-is-azure-data-studio.md)。

## <a name="create-a-stored-procedure-to-generate-random-numbers"></a>建立預存程序來產生亂數

為了簡單起見，讓我們使用預設會安裝並載入的 R `stats` 套件。 該套件包含數百個用來進行一般統計工作的函式，其中 `rnorm` 函式會在指定標準差和平均值的情況下，使用常態分佈產生指定數目的亂數。

例如，下列 R 程式碼會在指定標準差為 3 的情況下，傳回平均值為 50 的 100 個數字。

```R
as.data.frame(rnorm(100, mean = 50, sd = 3));
```

若要從 T-SQL 呼叫此行的 R，請在 `sp_execute_external_script` 的 R 指令碼參數中新增 R 函數，如下所示：

```sql
EXECUTE sp_execute_external_script
      @language = N'R'
    , @script = N'
         OutputDataSet <- as.data.frame(rnorm(100, mean = 50, sd =3));'
    , @input_data_1 = N'   ;'
      WITH RESULT SETS (([Density] float NOT NULL));
```

想要更輕鬆地產生一組不同的亂數？

與 T-SQL 合併時很容易做到。 您可以定義一個預存程序來取得使用者的引數，然後將這些引數當做變數傳遞至 R 指令碼。

```sql
CREATE PROCEDURE MyRNorm (
    @param1 INT
    , @param2 INT
    , @param3 INT
    )
AS
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
         OutputDataSet <- as.data.frame(rnorm(mynumbers, mymean, mysd));'
    , @input_data_1 = N'   ;'
    , @params = N' @mynumbers int, @mymean int, @mysd int'
    , @mynumbers = @param1
    , @mymean = @param2
    , @mysd = @param3
WITH RESULT SETS(([Density] FLOAT NOT NULL));
```

- 第一行定義執行預存程序時所需要的每個 SQL 輸入參數。

- 開頭為 `@params` 的行會定義 R 程式碼所使用的所有變數，以及對應的 SQL 資料類型。

- 緊接的行會將 SQL 參數名稱對應至相對應的 R 變數名稱。

現在您已將 R 函式包裝於預存程序中，您將可以輕鬆地呼叫函式並傳入不同的值，如下所示︰

```sql
EXECUTE MyRNorm @param1 = 100,@param2 = 50, @param3 = 3
```

## <a name="use-r-utility-functions-for-troubleshooting"></a>使用 R 公用程式函式進行疑難排解

預設安裝的 **utils** 套件可提供用來調查目前 R 環境的各種公用程式函數。 如果您發現您的 R 程式碼在 SQL Server 和外部環境中的執行方式不一致，這些函數會非常有用。

例如，您可能會在 R 中使用系統計時函數，例如 `system.time` 與 `proc.time`，以擷取 R 程序使用的時間，並分析效能問題。 如需範例，請參閱[建立資料特徵](../tutorials/walkthrough-create-data-features.md)教學課程，其中 R 時間函數會內嵌在解決方案中。

```sql
EXECUTE sp_execute_external_script
      @language = N'R'
    , @script = N'
        library(utils);
        start.time <- proc.time();
        
        # Run R processes
        
        elapsed_time <- proc.time() - start.time;'
```

如需其他有用的函式，請參閱[使用 R 分析函式來改善效能](../r/using-r-code-profiling-functions.md)。

## <a name="next-steps"></a>後續步驟

若要搭配 SQL 機器學習使用 R 來建立機器學習模型，請依照此快速入門進行：

> [!div class="nextstepaction"]
> [使用 SQL 機器學習在 R 中建立和評分預測模型](quickstart-r-train-score-model.md)