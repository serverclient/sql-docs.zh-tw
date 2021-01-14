---
title: 快速入門：執行 R 指令碼
titleSuffix: SQL machine learning
description: 使用 SQL 機器學習來執行一組簡單的 R 指令碼。 了解如何使用預存程序 sp_execute_external_script 來執行指令碼。
ms.prod: sql
ms.technology: machine-learning
ms.date: 05/21/2020
ms.topic: quickstart
author: garyericson
ms.author: garye
ms.custom: seo-lt-2019
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=azuresqldb-mi-current'
ms.openlocfilehash: 540804d2a35758627b24586ec8c29ca2dbab6e5f
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98101920"
---
# <a name="quickstart-run-simple-r-scripts-with-sql-machine-learning"></a>快速入門：使用 SQL 機器學習來執行簡單的 R 指令碼
[!INCLUDE [SQL Server 2016 SQL MI](../../includes/applies-to-version/sqlserver2016-asdbmi.md)]

::: moniker range=">=sql-server-ver15||>=sql-server-linux-ver15"
在此快速入門中，您將會使用 [SQL Server 機器學習服務](../sql-server-machine-learning-services.md)或[巨量資料叢集](../../big-data-cluster/machine-learning-services.md)執行一組簡單的 R 指令碼。 您將會了解如何使用預存程序 [sp_execute_external_script](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md) 在 SQL Server 執行個體中執行指令碼。
::: moniker-end
::: moniker range="=sql-server-2017"
在此快速入門中，您將會使用 [SQL Server 機器學習服務](../sql-server-machine-learning-services.md)來執行一組簡單的 R 指令碼。 您將會了解如何使用預存程序 [sp_execute_external_script](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md) 在 SQL Server 執行個體中執行指令碼。
::: moniker-end
::: moniker range="=sql-server-2016"
在此快速入門中，您將會使用 [SQL Server R Services](../r/sql-server-r-services.md) 來執行一組簡單的 R 指令碼。 您將會了解如何使用預存程序 [sp_execute_external_script](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md) 在 SQL Server 執行個體中執行指令碼。
::: moniker-end
::: moniker range="=azuresqldb-mi-current"
在此快速入門中，您將會使用 [Azure SQL 受控執行個體機器學習服務](/azure/azure-sql/managed-instance/machine-learning-services-overview)來執行一組簡單的 R 指令碼。 您將會了解如何使用預存程序 [sp_execute_external_script](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md)，以在資料庫中執行指令碼。
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

## <a name="run-a-simple-script"></a>執行簡單的指令碼

若要執行 R 指令碼，請將它當做引數傳遞至系統預存程式，[sp_execute_external_script](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md)。 此系統預存程序會啟動 R 執行階段、將資料傳遞到 R、安全地管理 R 使用者工作階段，並將任何結果傳回用戶端。

在下列步驟中，您會執行此範例 R 指令碼：

```r
a <- 1
b <- 2
c <- a/b
d <- a*b
print(c(c, d))
```

1. 開啟 **Azure Data Studio**，並連接到您的伺服器。

1. 將完整的 R 指令碼傳遞至 `sp_execute_external_script` 預存程序。

   指令碼會透過 `@script` 引數傳遞。 `@script` 引數內的所有一切都必須是有效的 R 程式碼。

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

1. 系統會計算正確的結果，且 R `print` 函數會將結果傳回至 [訊息] 視窗。

   其外觀應該如下所示。

    **結果**

    ```text
    STDOUT message(s) from external script:
    0.5 2
    ```

## <a name="run-a-hello-world-script"></a>執行 Hello World 指令碼

典型的範例指令碼只會輸出字串 "Hello World"。 執行下列命令。

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'OutputDataSet<-InputDataSet'
    , @input_data_1 = N'SELECT 1 AS hello'
WITH RESULT SETS(([Hello World] INT));
GO
```

`sp_execute_external_script` 預存程序的輸入包括：

| 輸入 | 描述 |
|-|-|
| @language | 定義要呼叫的語言擴充功能，在本例中為 R |
| @script | 定義要傳遞至 R 執行階段的命令。 整個 R 指令碼都必須包含在這個引數中 (作為 Unicode 文字)。 您也可以將文字新增至 **Nvarchar** 類型的變數，並呼叫該變數 |
| @input_data_1 | 查詢所傳回的資料會傳遞到 R 執行階段，其會以資料框架的格式傳回資料 |
|使用結果集 | 子句會定義所傳回資料表的結構描述，然後加入 "Hello World" 做為資料行名稱，並將 **int** 用於資料類型 |

此命令會輸出下列文字：

| Hello World |
|-------------|
| 1 |

## <a name="use-inputs-and-outputs"></a>使用者輸入和輸出

在預設情況下，`sp_execute_external_script` 會接受單一資料集做為輸入，這通常由您以有效的 SQL 查詢形式提供。 然後，它會傳回單一 R 資料框架做為輸出。

現在，讓我們使用 `sp_execute_external_script` 的預設輸入和輸出變數：**InputDataSet** 和 **OutputDataSet**。

1. 建立測試資料的小型資料表。

    ```sql
    CREATE TABLE RTestData (col1 INT NOT NULL)
    
    INSERT INTO RTestData
    VALUES (1);
    
    INSERT INTO RTestData
    VALUES (10);
    
    INSERT INTO RTestData
    VALUES (100);
    GO
    ```

1. 使用 `SELECT` 陳述式查詢資料表。
  
    ```sql
    SELECT *
    FROM RTestData
    ```

    **結果**

    ![RTestData 資料表的內容](./media/select-rtestdata.png)

1. 請執行下列 R 指令碼。 它會使用 `SELECT` 陳述式來擷取資料表中的資料、透過 R 執行階段傳遞，然後傳回資料做為資料框架。 `WITH RESULT SETS` 子句會為 SQL 定義傳回資料表的結構描述，新增資料行名稱 *NewColName*。

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'OutputDataSet <- InputDataSet;'
        , @input_data_1 = N'SELECT * FROM RTestData;'
    WITH RESULT SETS(([NewColName] INT NOT NULL));
    ```

    **結果**

    ![從資料表傳回資料的 R 指令碼輸出](./media/r-output-rtestdata.png)

1. 現在讓我們變更輸入和輸出變數的名稱。 預設的輸入和輸出變數名稱是 **InputDataSet** 和 **OutputDataSet**，此指令碼會將名稱變更為 **SQL_in** 和 **SQL_out**：

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N' SQL_out <- SQL_in;'
        , @input_data_1 = N' SELECT 12 as Col;'
        , @input_data_1_name = N'SQL_in'
        , @output_data_1_name = N'SQL_out'
    WITH RESULT SETS(([NewColName] INT NOT NULL));
    ```

    請注意，R 區分大小寫。 R 指令碼 (**SQL_out** **SQL_in**) 中所使用的輸入和輸出變數必須比對以 `@input_data_1_name` 和 `@output_data_1_name` 定義的名稱，包括大小寫。

   > [!TIP]
   > 只有一個輸入資料集可以傳入作為參數，而且您只能傳回一個資料集。 不過，您可以從 R 程式碼內呼叫其他資料集，而且可以在資料集以外傳回其他類型的輸出。 您也可以為任何參數加上 OUTPUT 關鍵字，使其與結果一起傳回。

1. 您也可以在無輸入資料的情況下 (`@input_data_1` 設為空白)，只使用 R 指令碼產生值。

   下列指令碼輸出文字 "hello" 和 "world"。

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
    mytextvariable <- c("hello", " ", "world");
    OutputDataSet <- as.data.frame(mytextvariable);
    '
        , @input_data_1 = N''
    WITH RESULT SETS(([Col1] CHAR(20) NOT NULL));
    ```

    **結果**

    ![使用 @script 做為輸入查詢結果](./media/r-data-generated-output.png)

## <a name="check-r-version"></a>檢查 R 版本

若想要查看安裝的是哪個 R 版本，請執行下列指令碼。

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'print(version)';
GO
```

R `print` 函數會將版本傳回到 [訊息] 視窗。 在下方的範例輸出中，您可以看到在此案例中安裝的是 R 3.4.4 版。

**結果**

```text
STDOUT message(s) from external script:
                   _
platform       x86_64-w64-mingw32
arch           x86_64
os             mingw32
system         x86_64, mingw32
status
major          3
minor          4.4
year           2018
month          03
day            15
svn rev        74408
language       R
version.string R version 3.4.4 (2018-03-15)
nickname       Someone to Lean On
```

## <a name="list-r-packages"></a>列出 R 套件
::: moniker range=">=sql-server-2017"
Microsoft 提供一些預先與機器學習服務一起安裝的 R 套件。
::: moniker-end
::: moniker range="=sql-server-2016"
Microsoft 提供一些預先與 R Services 一起安裝的 R 套件。
::: moniker-end

若要查看已安裝 R 套件的清單 (包括版本、相依性、授權及程式庫路徑資訊)，請執行下列指令碼。

```SQL
EXEC sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- data.frame(installed.packages()[,c("Package", "Version", "Depends", "License", "LibPath")]);'
WITH result sets((
            Package NVARCHAR(255)
            , Version NVARCHAR(100)
            , Depends NVARCHAR(4000)
            , License NVARCHAR(1000)
            , LibPath NVARCHAR(2000)
            ));
```

輸出來自 R 中的 `installed.packages()`，並以結果集的形式傳回。

**結果**

![R 中已安裝的套件](./media/rsql-installed-packages.png) 

## <a name="next-steps"></a>後續步驟

若要了解如何在使用 R 時搭配 SQL 機器學習使用資料結構，請遵循本快速入門：

> [!div class="nextstepaction"]
> [搭配 SQL 機器學習使用 R 來處理資料類型和物件](quickstart-r-data-types-and-objects.md)