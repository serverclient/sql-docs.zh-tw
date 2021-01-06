---
title: 快速入門：執行 Python 指令碼
titleSuffix: SQL machine learning
description: 使用機器學習服務在 SQL Server、巨量資料叢集或 Azure SQL 受控執行個體上執行一組簡單的 Python 指令碼。 了解如何使用預存程序 sp_execute_external_script 來執行指令碼。
ms.prod: sql
ms.technology: machine-learning
ms.date: 09/28/2020
ms.topic: quickstart
author: dphansen
ms.author: davidph
ms.custom: contperf-fy21q1
monikerRange: '>=sql-server-2017||>=sql-server-linux-ver15||=azuresqldb-mi-current'
ms.openlocfilehash: b911915b612a198e405811aa0f3e5b685f0adbca
ms.sourcegitcommit: cb8e2ce950d8199470ff1259c9430f0560f0dc1d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2021
ms.locfileid: "97878721"
---
# <a name="quickstart-run-simple-python-scripts-with-sql-machine-learning"></a>快速入門：使用 SQL 機器學習來執行簡單的 Python 指令碼
[!INCLUDE [SQL Server 2017 SQL MI](../../includes/applies-to-version/sqlserver2017-asdbmi.md)]

在此快速入門中，您將會使用 [SQL Server 機器學習服務](../sql-server-machine-learning-services.md)、[Azure SQL 受控執行個體機器學習服務](/azure/azure-sql/managed-instance/machine-learning-services-overview)或在 [SQL Server 巨量資料叢集](../../big-data-cluster/machine-learning-services.md)上執行一組簡單的 Python 指令碼。 您將會了解如何使用預存程序 [sp_execute_external_script](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md) 在 SQL Server 執行個體中執行指令碼。

## <a name="prerequisites"></a>Prerequisites

您需要符合下列必要條件，才能執行此快速入門。

- SQL 資料庫位於其中一個平台上：
  - [SQL Server 機器學習服務](../sql-server-machine-learning-services.md)。 若要安裝，請參閱 [Windows 安裝指南](../install/sql-machine-learning-services-windows-install.md)或 [Linux 安裝指南](../../linux/sql-server-linux-setup-machine-learning.md?toc=%2Fsql%2Fmachine-learning%2Ftoc.json)。
  - SQL Server 巨量資料叢集。 查看如何[啟用 SQL Server 巨量資料叢集上的機器學習服務](../../big-data-cluster/machine-learning-services.md)。
  - Azure SQL 受控執行個體機器學習服務。 如需詳細資訊，請參閱 [Azure SQL 受控執行個體機器學習服務概觀](/azure/azure-sql/managed-instance/machine-learning-services-overview)。

- 執行包含 Python 指令碼之 SQL 查詢的工具。 本快速入門使用 [Azure Data Studio](../../azure-data-studio/what-is.md)。

## <a name="run-a-simple-script"></a>執行簡單的指令碼

若要執行 Python 指令碼，請將它當做引數傳遞至系統預存程式，[sp_execute_external_script](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md)。 這個系統預存程序會在 SQL 機器學習內容中啟動 Python 執行階段、將資料傳遞到 Python，安全地管理 Python 使用者工作階段，並將任何結果傳回至用戶端。

在下列步驟中，您會在自己的資料庫中執行此範例 Python 指令碼：

```python
a = 1
b = 2
c = a/b
d = a*b
print(c, d)
```

1. 以連線到 SQL 執行個體的 **Azure Data Studio** 來開啟新查詢視窗。

1. 將完整的 Python 指令碼傳遞至 `sp_execute_external_script` 預存程序。

   指令碼會透過 `@script` 引數傳遞。 `@script` 引數內的所有一切都必須是有效的 Python 程式碼。

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

1. 系統會計算正確的結果，且 Python `print` 函數會將結果傳回至 [訊息] 視窗。

   其外觀應該如下所示。

    **結果**

    ```text
    STDOUT message(s) from external script:
    0.5 2
    ```

## <a name="run-a-hello-world-script"></a>執行 Hello World 指令碼

典型的範例指令碼只會輸出字串 "Hello World"。 執行下列命令。

```sql
EXECUTE sp_execute_external_script @language = N'Python'
    , @script = N'OutputDataSet = InputDataSet'
    , @input_data_1 = N'SELECT 1 AS hello'
WITH RESULT SETS(([Hello World] INT));
GO
```

`sp_execute_external_script` 預存程序的輸入包括：

| 輸入 | 描述 |
|-|-|
| @language | 定義要呼叫的語言擴充功能，在本例中為 Python |
| @script | 定義要傳遞至 Python 執行階段的命令。 您的整個 Python 指令碼必須以 Unicode 文字的格式包含在此引數中。 您也可以將文字新增至 **Nvarchar** 類型的變數，並呼叫該變數 |
| @input_data_1 | 查詢所傳回的資料會傳遞到 Python 執行階段，其會以資料框架的格式傳回資料 |
| 使用結果集 | 子句會定義 SQL 機器學習傳回資料表的結構描述，然後新增 "Hello World" 作為資料行名稱，並將 **int** 用於資料類型 |

此命令會輸出下列文字：

| Hello World |
|-------------|
| 1 |

## <a name="use-inputs-and-outputs"></a>使用者輸入和輸出

在預設情況下，`sp_execute_external_script` 會接受單一資料集做為輸入，這通常由您以有效的 SQL 查詢形式提供。 然後，它會傳回單一 Python 資料框架做為輸出。

現在，讓我們使用 `sp_execute_external_script` 的預設輸入和輸出變數：**InputDataSet** 和 **OutputDataSet**。

1. 建立測試資料的小型資料表。

    ```sql
    CREATE TABLE PythonTestData (col1 INT NOT NULL)
    
    INSERT INTO PythonTestData
    VALUES (1);
    
    INSERT INTO PythonTestData
    VALUES (10);
    
    INSERT INTO PythonTestData
    VALUES (100);
    GO
    ```

1. 使用 `SELECT` 陳述式查詢資料表。
  
    ```sql
    SELECT *
    FROM PythonTestData
    ```

    **結果**

    ![PythonTestData 資料表的內容](./media/select-pythontestdata.png)

1. 請執行下列 Python 指令碼。 它會使用 `SELECT` 陳述式來擷取資料表中的資料、透過 Python 執行階段傳遞，然後傳回資料做為資料框架。 `WITH RESULT SETS` 子句會為 SQL 定義傳回資料表的結構描述，新增資料行名稱 *NewColName*。

    ```sql
    EXECUTE sp_execute_external_script @language = N'Python'
        , @script = N'OutputDataSet = InputDataSet;'
        , @input_data_1 = N'SELECT * FROM PythonTestData;'
    WITH RESULT SETS(([NewColName] INT NOT NULL));
    ```

    **結果**

    ![從資料表傳回資料的 Python 指令碼輸出](./media/python-output-pythontestdata.png)

1. 現在變更輸入和輸出變數的名稱。 預設的輸入和輸出變數名稱是 **InputDataSet** 和 **OutputDataSet**，以下指令碼會將名稱變更為 **SQL_in** 和 **SQL_out**：

    ```sql
    EXECUTE sp_execute_external_script @language = N'Python'
        , @script = N'SQL_out = SQL_in;'
        , @input_data_1 = N'SELECT 12 as Col;'
        , @input_data_1_name  = N'SQL_in'
        , @output_data_1_name = N'SQL_out'
    WITH RESULT SETS(([NewColName] INT NOT NULL));
    ```

    請注意，Python 區分大小寫。 Python 指令碼 (**SQL_out** **SQL_in**) 中所使用的輸入和輸出變數必須比對以 `@input_data_1_name` 和 `@output_data_1_name` 定義的名稱，包括大小寫。

    > [!TIP]
    > 只有一個輸入資料集可以傳入作為參數，而且您只能傳回一個資料集。 不過，您可以從 Python 程式碼內部呼叫其他資料集，而且除了資料集之外，您還可以傳回其他類型的輸出。 您也可以為任何參數加上 OUTPUT 關鍵字，使其與結果一起傳回。

1. 您也可以在無輸入資料的情況下 (`@input_data_1` 設為空白)，只使用 Python 指令碼產生值。

    下列指令碼輸出文字 "hello" 和 "world"。

    ```sql
    EXECUTE sp_execute_external_script @language = N'Python'
        , @script = N'
    import pandas as pd
    mytextvariable = pandas.Series(["hello", " ", "world"]);
    OutputDataSet = pd.DataFrame(mytextvariable);
    '
        , @input_data_1 = N''
    WITH RESULT SETS(([Col1] CHAR(20) NOT NULL));
    ```

    **結果**

    ![使用 @script 做為輸入查詢結果](./media/python-data-generated-output.png)

> [!NOTE]
> Python 使用前置空格將陳述式分組。 因此當內嵌的 Python 指令碼跨越多行時 (如上述指令碼所示)，請勿嘗試將 Python 命令縮排以與 SQL 命令對齊。 例如，此指令碼將產生錯誤：
>
> ```sql
> EXECUTE sp_execute_external_script @language = N'Python'
>       , @script = N'
>       import pandas as pd
>       mytextvariable = pandas.Series(["hello", " ", "world"]);
>       OutputDataSet = pd.DataFrame(mytextvariable);
>       '
>       , @input_data_1 = N''
> WITH RESULT SETS(([Col1] CHAR(20) NOT NULL));
> ```

## <a name="check-python-version"></a>檢查 Python 版本

如果想要查看安裝在伺服器中的 Python 版本，請執行下列指令碼。

```sql
EXECUTE sp_execute_external_script @language = N'Python'
    , @script = N'
import sys
print(sys.version)
'
GO
```

Python `print` 函數會將版本傳回到 [訊息] 視窗。 在下方的範例輸出中，您可以看到在此案例中安裝的是 Python 3.5.2 版。

**結果**

```text
STDOUT message(s) from external script:
3.5.2 |Continuum Analytics, Inc.| (default, Jul  5 2016, 11:41:13) [MSC v.1900 64 bit (AMD64)]
```

## <a name="list-python-packages"></a>列出 Python 套件

Microsoft 提供一些與機器學習服務一起預先安裝的 Python 套件。

若要查看已安裝的 Python 套件清單 (包括版本)，請執行以下指令碼。

```SQL
EXECUTE sp_execute_external_script @language = N'Python'
    , @script = N'
import pkg_resources
import pandas
dists = [str(d) for d in pkg_resources.working_set]
OutputDataSet = pandas.DataFrame(dists)
'
WITH RESULT SETS(([Package] NVARCHAR(max)))
GO
```

該清單是來自 Python 中的 `pkg_resources.working_set`，並以資料框架的形式傳回到 SQL。

**結果**

:::image type="content" source="media/python-package-list.png" alt-text="已安裝的 Python 套件清單":::

## <a name="next-steps"></a>後續步驟

若要了解如何在 SQL 機器學習中使用 Python 時使用資料結構，請遵循本快速入門：

> [!div class="nextstepaction"]
> [快速入門：使用 Python 的資料結構與物件](quickstart-python-data-structures.md)
