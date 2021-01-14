---
title: 快速入門：Python 函式
titleSuffix: SQL machine learning
description: 在此快速入門中，您將會了解如何搭配 SQL 機器學習使用 Python 數學與公用程式函式。
ms.prod: sql
ms.technology: machine-learning
ms.date: 09/28/2020
ms.topic: quickstart
author: dphansen
ms.author: davidph
ms.custom: seo-lt-2019
monikerRange: '>=sql-server-2017||>=sql-server-linux-ver15||=azuresqldb-mi-current'
ms.openlocfilehash: 646c1decb34adabe645a58d640b0f9adbb4768b7
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98101904"
---
# <a name="quickstart-python-functions-with-sql-machine-learning"></a>快速入門：Python 函式搭配 SQL 機器學習
[!INCLUDE [SQL Server 2017 SQL MI](../../includes/applies-to-version/sqlserver2017-asdbmi.md)]

在此快速入門中，您將會使用 [SQL Server 機器學習服務](../sql-server-machine-learning-services.md)、[Azure SQL 受控執行個體機器學習服務](/azure/azure-sql/managed-instance/machine-learning-services-overview)或在 [SQL Server 巨量資料叢集](../../big-data-cluster/machine-learning-services.md)上了解如何使用 Python 數學與公用程式函式。 使用 T-SQL 實作統計函數通常會很複雜，但若使用 Python，只需要幾行程式碼就可以完成。

## <a name="prerequisites"></a>Prerequisites

您需要符合下列必要條件，才能執行此快速入門。

- SQL 資料庫位於其中一個平台上：
  - [SQL Server 機器學習服務](../sql-server-machine-learning-services.md)。 若要安裝，請參閱 [Windows 安裝指南](../install/sql-machine-learning-services-windows-install.md)或 [Linux 安裝指南](../../linux/sql-server-linux-setup-machine-learning.md?toc=%2Fsql%2Fmachine-learning%2Ftoc.json)。
  - SQL Server 巨量資料叢集。 查看如何[啟用 SQL Server 巨量資料叢集上的機器學習服務](../../big-data-cluster/machine-learning-services.md)。
  - Azure SQL 受控執行個體機器學習服務。 如需詳細資訊，請參閱 [Azure SQL 受控執行個體機器學習服務概觀](/azure/azure-sql/managed-instance/machine-learning-services-overview)。

- 執行包含 Python 指令碼之 SQL 查詢的工具。 本快速入門使用 [Azure Data Studio](../../azure-data-studio/what-is-azure-data-studio.md)。

## <a name="create-a-stored-procedure-to-generate-random-numbers"></a>建立預存程序來產生亂數

為了簡單起見，讓我們使用預設會安裝並載入的 Python `numpy` 套件。 該套件包含數百個用來進行一般統計工作的函式，其中 `random.normal` 函式會在指定標準差和平均值的情況下，使用常態分佈產生指定數目的亂數。

例如，下列 Python 程式碼會在指定標準差為 3 的情況下，傳回平均值為 50 的 100 個數字。

```Python
numpy.random.normal(size=100, loc=50, scale=3)
```

若要從 T-SQL 呼叫此行的 Python，請在 `sp_execute_external_script` 的 Python 指令碼參數中新增 Python 函數。 輸出時應有資料框架，因此請使用 `pandas` 來進行轉換。

```sql
EXECUTE sp_execute_external_script @language = N'Python'
    , @script = N'
import numpy
import pandas
OutputDataSet = pandas.DataFrame(numpy.random.normal(size=100, loc=50, scale=3));
'
    , @input_data_1 = N'   ;'
WITH RESULT SETS(([Density] FLOAT NOT NULL));
```

想要更輕鬆地產生一組不同的亂數？ 您可以定義一個預存程序來取得使用者的引數，然後將這些引數當做變數傳遞至 Python 指令碼。

```sql
CREATE PROCEDURE MyPyNorm (
      @param1 INT
    , @param2 INT
    , @param3 INT
    )
AS
EXECUTE sp_execute_external_script @language = N'Python'
    , @script = N'
import numpy
import pandas
OutputDataSet = pandas.DataFrame(numpy.random.normal(size=mynumbers, loc=mymean, scale=mysd));
'
    , @input_data_1 = N'   ;'
    , @params = N' @mynumbers int, @mymean int, @mysd int'
    , @mynumbers = @param1
    , @mymean = @param2
    , @mysd = @param3
WITH RESULT SETS(([Density] FLOAT NOT NULL));
```

- 第一行定義執行預存程序時所需要的每個 SQL 輸入參數。

- 開頭為 `@params` 的行會定義 Python 程式碼所使用的所有變數，以及對應的 SQL 資料類型。

- 緊接的行會將 SQL 參數名稱對應至相對應的 Python 變數名稱。

現在您已將 Python 函數包裝於預存程序中，您將可以輕鬆地呼叫函數並傳入不同的值，如下所示︰

```sql
EXECUTE MyPyNorm @param1 = 100,@param2 = 50, @param3 = 3
```

## <a name="use-python-utility-functions-for-troubleshooting"></a>使用 Python 公用程式函數進行疑難排解

Python 套件提供各種公用程式函數來調查目前的 Python 環境。 如果您發現您的 Python 程式碼在 SQL Server 和外部環境中的執行方式不一致，這些函數會非常有用。

例如，您可以使用 `time` 套件中的系統計時函數來測量 Python 處理序所使用的時間量，並分析效能問題。

```sql
EXECUTE sp_execute_external_script
      @language = N'Python'
    , @script = N'
import time
start_time = time.time()

# Run Python processes

elapsed_time = time.time() - start_time
'
    , @input_data_1 = N' ;';
```

## <a name="next-steps"></a>後續步驟

若要搭配 SQL 機器學習使用 Python 來建立機器學習模型，請遵循此快速入門：

> [!div class="nextstepaction"]
> [快速入門：在 Python 中建立預測模型並為其評分](quickstart-python-train-score-model.md)