---
title: 快速入門：R 資料結構、資料類型與物件
titleSuffix: SQL machine learning
description: 在此快速入門中，您將會了解如何在搭配 SQL 機器學習使用 R 時使用資料結構、資料類型與物件。
ms.prod: sql
ms.technology: machine-learning
ms.date: 05/21/2020
ms.topic: quickstart
author: garyericson
ms.author: garye
ms.custom: seo-lt-2019
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=azuresqldb-mi-current'
ms.openlocfilehash: 763ffa0cc6cf65d17489397bf63014493bf2ac19
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98101895"
---
# <a name="quickstart-data-structures-data-types-and-objects-using-r-with-sql-machine-learning"></a>快速入門：搭配 SQL 機器學習使用 R 的資料類型和物件
[!INCLUDE [SQL Server 2016 SQL MI](../../includes/applies-to-version/sqlserver2016-asdbmi.md)]

::: moniker range=">=sql-server-ver15||>=sql-server-linux-ver15"
在此快速入門中，您將會了解當您在 [SQL Server 機器學習服務](../sql-server-machine-learning-services.md)中或在[巨量資料叢集](../../big-data-cluster/machine-learning-services.md)上使用 R 時，如何使用資料結構與資料類型。 在此快速入門中，您將會了解如何在 R 與 SQL Server 之間移動資料，以及可能發生的常見問題。
::: moniker-end
::: moniker range="=sql-server-2017"
在此快速入門中，您將會了解當您在 [SQL Server 機器學習服務](../sql-server-machine-learning-services.md)中使用 R 時，如何使用資料結構與資料類型。 在此快速入門中，您將會了解如何在 R 與 SQL Server 之間移動資料，以及可能發生的常見問題。
::: moniker-end
::: moniker range="=sql-server-2016"
在此快速入門中，您將會了解當您在 [SQL Server R Services](../r/sql-server-r-services.md) 中使用 R 時，如何使用資料結構與資料類型。 在此快速入門中，您將會了解如何在 R 與 SQL Server 之間移動資料，以及可能發生的常見問題。
::: moniker-end
::: moniker range="=azuresqldb-mi-current"
在此快速入門中，您將了解在 [Azure SQL 受控執行個體機器學習服務](/azure/azure-sql/managed-instance/machine-learning-services-overview)中使用 R 時，如何使用資料結構與資料類型。 您將了解在 R 與 SQL 受控執行個體之間移動資料，以及可能發生的常見問題。
::: moniker-end

首先要瞭解的常見問題包括：

- 資料類型有時不相符
- 可能會發生隱含轉換
- 有時需要轉型和轉換作業
- R 和 SQL 會使用不同的資料物件

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

## <a name="always-return-a-data-frame"></a>一律傳回資料框架

當您的指令碼將結果從 R 傳回到 SQL Server 時，它必須傳回資料做為 **data.frame**。 您在指令碼中產生的任何其他類型物件 (不論是清單、因數、向量或二進位資料)，如果您想要輸出它以做為預存程序結果的一部分，就必須將它轉換為資料框架。 幸好有多個 R 函數可用來支援將其他物件變更為資料框架。 您甚至可將二進位模型序列化，並在資料框架中傳回它；稍後您將在本快速入門中執行此動作。

首先，讓我們使用一些 R 的基本 R 物件 (向量、矩陣及清單) 進行實驗，並查看轉換為資料框架如何變更傳遞到 SQL Server 的輸出。

在 R 中比較這兩個 "Hello World" 指令碼。這兩個指令碼看起來幾乎完全相同，但第一個會傳回三個值的單一資料行，而第二個會傳回三個資料行，每個均含單一值。

**範例 1**

```sql
EXECUTE sp_execute_external_script
       @language = N'R'
     , @script = N' mytextvariable <- c("hello", " ", "world");
       OutputDataSet <- as.data.frame(mytextvariable);'
     , @input_data_1 = N' ';
```

**範例 2**

```sql
EXECUTE sp_execute_external_script
        @language = N'R'
      , @script = N' OutputDataSet<- data.frame(c("hello"), " ", c("world"));'
      , @input_data_1 = N'  ';
```

## <a name="identify-schema-and-data-types"></a>識別結構描述和資料類型

為何結果如此不同？

您通常可以使用 R `str()` 命令來找到答案。 在 R 指令碼中的任一處加入函數 `str(object_name)`，以傳回指定 R 物件的資料結構描述做為資訊訊息。

若要了解為什麼範例 1 和範例 2 會有如此不同的結果，請在每個陳述式中 `@script` 變數定義的結尾插入一行 `str(OutputDataSet)`，如下：

**已加入 str 函數的範例 1**

```sql
EXECUTE sp_execute_external_script
        @language = N'R'
      , @script = N' mytextvariable <- c("hello", " ", "world");
      OutputDataSet <- as.data.frame(mytextvariable);
      str(OutputDataSet);'
      , @input_data_1 = N'  '
;
```

**已加入 str 函數的範例 2**

```sql
EXECUTE sp_execute_external_script
  @language = N'R', 
  @script = N' OutputDataSet <- data.frame(c("hello"), " ", c("world"));
    str(OutputDataSet);' , 
  @input_data_1 = N'  ';
```

現在，檢視 [訊息] 中的文字，以查看為何會有不同的輸出。

**結果 - 範例 1**

```sql
STDOUT message(s) from external script:
'data.frame':   3 obs. of  1 variable:
$ mytextvariable: Factor w/ 3 levels " ","hello","world": 2 1 3
```

**結果 - 範例 2**

```sql
STDOUT message(s) from external script:
'data.frame':   1 obs. of  3 variables:
$ c..hello..: Factor w/ 1 level "hello": 1
$ X...      : Factor w/ 1 level " ": 1
$ c..world..: Factor w/ 1 level "world": 1
```

如您所見，稍微變化一下 R 語法，就會對結果的結構描述產生極大的影響。 我們不會詳述原因，但 R 資料類型中的差異會在 [Hadley Wickham 所著 "Advanced R"](http://adv-r.had.co.nz) 的 *資料結構 (英文)* 一節中詳細說明。

現在只需注意，您需要在將 R 物件強制轉型為資料框架時檢查預期的結果。

> [!TIP]
> 您也可以使用 R 身分識別函數 (例如 `is.matrix`、`is.vector`) 來傳回內部資料結構的相關資訊。

## <a name="implicit-conversion-of-data-objects"></a>資料物件的隱含轉換

每個 R 資料物件都有自己的規則，如果在結合其他資料物件時，兩個資料物件具有相同數目的維度，或者如果任一個資料類型包含異質資料類型，就會按照規則處理其值。

首先，建立測試資料的小型資料表。

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

例如，假設您執行下列陳述式，以使用 R 執行矩陣相乘。您將含有三個值的單一資料行矩陣乘以含有四個值的陣列，並預期會得到一個 4x3 矩陣。

```sql
EXECUTE sp_execute_external_script
    @language = N'R'
    , @script = N'
        x <- as.matrix(InputDataSet);
        y <- array(12:15);
    OutputDataSet <- as.data.frame(x %*% y);'
    , @input_data_1 = N' SELECT [Col1]  from RTestData;'
    WITH RESULT SETS (([Col1] int, [Col2] int, [Col3] int, Col4 int));
```

其中，具有三個值的資料行會轉換成單一資料行矩陣。 因為矩陣只是 R 中的特殊陣列案例，所以，陣列 `y` 會隱含地強制轉型為單一資料行的矩陣，以使這兩個引數相符。

**結果**

|Col1|Col2|Col3|Col4|
|---|---|---|---|
|12|13|14|15|
|120|130|140|150|
|1200|1300|1400|1500|

不過，請注意您變更陣列 `y` 大小時會發生的情況。

```sql
execute sp_execute_external_script
   @language = N'R'
   , @script = N'
        x <- as.matrix(InputDataSet);
        y <- array(12:14);
   OutputDataSet <- as.data.frame(y %*% x);'
   , @input_data_1 = N' SELECT [Col1]  from RTestData;'
   WITH RESULT SETS (([Col1] int ));
```

R 現在會傳回單一值做為結果。

**結果**

|Col1|
|---|
|1542|

原因為何？ 在此案例中，因為這兩個引數可以當做長度相同的向量來處理，所以 R 會傳回內積做為矩陣。  根據線性代數的規則，這是預期的行為；不過，如果下游應用程式預期輸出結構描述永遠不會變更，則它可能會造成問題！

> [!TIP]
>
> 收到錯誤？ 請確定您是在包含資料表的資料庫內容中執行預存程序，而不是在 **master** 或其他資料庫中執行。
>
> 此外，建議您避免在這些範例中使用暫存資料表。 有些 R 用戶端會終止批次之間的連線，並刪除暫存資料表。

## <a name="merge-or-multiply-columns-of-different-length"></a>合併或相乘不同長度的資料行

R 提供絕佳彈性來使用不同大小的向量，以及將這些類似資料行的結構結合至資料框架中。 向量的清單看起來像是一個資料表，但它們不遵循用以管理資料庫資料表的所有規則。

例如，下列指令碼定義長度為 6 的數值陣列，並將它儲存在 R 變數 `df1` 中。 此數值陣列接著會與 RTestData 資料表 (含有三 (3) 個值) 的整數結合，以建立新的資料框架 `df2`。

```sql
EXECUTE sp_execute_external_script
    @language = N'R'
    , @script = N'
               df1 <- as.data.frame( array(1:6) );
               df2 <- as.data.frame( c( InputDataSet , df1 ));
               OutputDataSet <- df2'
    , @input_data_1 = N' SELECT [Col1]  from RTestData;'
    WITH RESULT SETS (( [Col2] int not null, [Col3] int not null ));
```

為了填滿資料框架，R 會根據比對陣列 `df1` 中元素數目所需的次數，多次重複使用擷取自 RTestData 的元素。

**結果**

|*Col2*|*Col3*|
|----|----|
|1|1|
|10|2|
|100|3|
|1|4|
|10|5|
|100|6|

請記住，資料框架只是看起來像資料表，其實是一個向量清單。

## <a name="cast-or-convert-data"></a>轉型或轉換資料

R 和 SQL Server 不會使用相同的資料類型，因此，當您在 SQL Server 中執行查詢來取得資料，然後將該資料傳遞至 R 執行階段時，通常會發生某些類型的隱含轉換。 當您將資料從 R 傳回至 SQL Server 時，將發生另一組轉換。

- SQL Server 將資料從查詢發送至 Launchpad 服務所管理的 R 處理序，並將其轉換為內部表示法以提升效率。
- R 執行階段會將資料載入 data.frame 變數，並對資料執行自己的作業。
- 資料庫引擎會使用受保護的內部連線來將資料傳回至 SQL server，並根據 SQL Server 資料類型顯示資料。
- 藉由使用可發出 SQL 查詢並處理表格式資料集的用戶端或網路程式庫來連接到 SQL Server，您就能取得資料。 此用戶端應用程式可能會以其他方式影響資料。

若要查看其運作方式，請在 [AdventureWorksDW](https://github.com/Microsoft/sql-server-samples/releases/tag/adventureworks) 資料倉儲上執行這類查詢。 此檢視會傳回用來建立預測的銷售資料。

```sql
USE AdventureWorksDW
GO

SELECT ReportingDate
         , CAST(ModelRegion as varchar(50)) as ProductSeries
         , Amount
           FROM [AdventureWorksDW].[dbo].[vTimeSeries]
           WHERE [ModelRegion] = 'M200 Europe'
           ORDER BY ReportingDate ASC
```

> [!NOTE]
>
> 您可以使用任何版本的 AdventureWorks，或使用您自己的資料庫建立不同查詢。 重點是，嘗試處理一些包含文字、日期時間和數值的資料。

現在，請嘗試將此查詢做為輸入貼到預存程序。

```sql
EXECUTE sp_execute_external_script
       @language = N'R'
      , @script = N' str(InputDataSet);
      OutputDataSet <- InputDataSet;'
      , @input_data_1 = N'
           SELECT ReportingDate
         , CAST(ModelRegion as varchar(50)) as ProductSeries
         , Amount
           FROM [AdventureWorksDW].[dbo].[vTimeSeries]
           WHERE [ModelRegion] = ''M200 Europe''
           ORDER BY ReportingDate ASC ;'
WITH RESULT SETS undefined;
```

如果您收到錯誤，您可能需要對查詢文字進行一些編輯。 例如，WHERE 子句中的字串述詞必須以兩組單引號括住。

讓查詢能夠正常運作之後，檢閱 `str` 函數的結果，以查看 R 如何處理輸入資料。

**結果**

```text
STDOUT message(s) from external script: 'data.frame':    37 obs. of  3 variables:
STDOUT message(s) from external script: $ ReportingDate: POSIXct, format: "2010-12-24 23:00:00" "2010-12-24 23:00:00"
STDOUT message(s) from external script: $ ProductSeries: Factor w/ 1 levels "M200 Europe",..: 1 1 1 1 1 1 1 1 1 1
STDOUT message(s) from external script: $ Amount       : num  3400 16925 20350 16950 16950
```

- 已使用 R 資料類型 **POSIXct** 來處理 datetime 資料行。
- 已將文字資料行 "ProductSeries" 識別為一個 **因數**，這表示類別變數。 預設會處理字串值以做為因數。 如果您將字串傳遞至 R，則會將它轉換為整數，以供內部使用，接著對應回輸出上的字串。

### <a name="summary"></a>摘要

您可以從這些簡短的範例中了解到，將 SQL 查詢做為輸入進行傳遞時，需檢查資料轉換的效果。 由於 R 不支援部分 SQL Server 資料類型，因此請考慮以下方式，以避免發生錯誤：

- 事先測試您的資料，並確認結構描述中可能會在傳遞至 R 程式碼發生問題的資料行或值。
- 個別指定輸入資料來源中的資料行，而不是使用 `SELECT *`，並了解將如何處理每個資料行。
- 在準備輸入資料時視需要執行明確的轉型，以避免出現意外狀況。
- 避免傳遞會造成錯誤且不適用模型化的資料行 (例如 GUID 或 rowguid)。

如需可支援與不支援資料類型的詳細資訊，請參閱 [R 程式庫和資料類型](../r/r-libraries-and-data-types.md)。

## <a name="next-steps"></a>後續步驟

若要了解如何使用 SQL 機器學習撰寫進階 R 函式，請遵循此快速入門：

> [!div class="nextstepaction"]
> [使用 SQL 機器學習撰寫進階 R 函式](quickstart-r-functions.md)