---
title: 快速入門：在 R 中將模型定型
titleSuffix: SQL machine learning
description: 在此快速入門中，您將會使用 T 來建立及定型預測模型。您會將模型儲存至資料表，然後使用該模型搭配 SQL 機器學習來預測新資料中的值。
ms.prod: sql
ms.technology: machine-learning
ms.date: 04/23/2020
ms.topic: quickstart
author: garyericson
ms.author: garye
ms.custom: seo-lt-2019
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=azuresqldb-mi-current'
ms.openlocfilehash: 21e815a95ebc6a407749195323748d6c73c19e00
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98100165"
---
# <a name="quickstart-create-and-score-a-predictive-model-in-r-with-sql-machine-learning"></a>快速入門：使用 SQL 機器學習在 R 中建立和評分預測模型
[!INCLUDE [SQL Server 2016 SQL MI](../../includes/applies-to-version/sqlserver2016-asdbmi.md)]

::: moniker range=">=sql-server-ver15||>=sql-server-linux-ver15"
在此快速入門中，您將會使用 T 來建立及定型預測模型。您會將模型儲存至 SQL Server 執行個體中的資料表，然後使用該模型搭配 [SQL Server 機器學習服務](../sql-server-machine-learning-services.md)或[巨量資料叢集](../../big-data-cluster/machine-learning-services.md)來預測新資料中的值。
::: moniker-end
::: moniker range="=sql-server-2017"
在此快速入門中，您將會使用 T 來建立及定型預測模型。您會將模型儲存至 SQL Server 執行個體中的資料表，然後透過 [SQL Server 機器學習服務](../sql-server-machine-learning-services.md)使用該模型來從新資料預測值。
::: moniker-end
::: moniker range="=sql-server-2016"
在此快速入門中，您將會使用 T 來建立及定型預測模型。您會將模型儲存至 SQL Server 執行個體中的資料表，然後使用該模型搭配 [SQL Server R Services](../r/sql-server-r-services.md)來預測新資料中的值。
::: moniker-end
::: moniker range="=azuresqldb-mi-current"
在此快速入門中，您將會使用 T 來建立及定型預測模型。您會將模型儲存至 SQL Server 執行個體中的資料表，然後透過 [Azure SQL 受控執行個體機器學習服務](/azure/azure-sql/managed-instance/machine-learning-services-overview)使用該模型來從新資料預測值。
::: moniker-end

您將建立並執行在 SQL 中執行的兩個預存程序。 第一個預存程序使用 R 隨附的 **mtcars** 資料集，並產生簡易的一般化線性模型 (GLM)，預測車輛已裝有手排變速箱的可能性。 第二個程序為評分用，會呼叫在第一個程式中產生的模型，並根據新資料輸出一組預測。 將 R 程式碼放入 SQL 預存程序後，作業會包含在 SQL 中、可重複使用，而且可由其他預存程序和用戶端應用程式呼叫。

> [!TIP]
> 如果您需要複習一下線性模型，請參閱下列教學課程，該課程說明使用 rxLinMod 來調整線性模型的程序：[配適線性模型](/machine-learning-server/r/how-to-revoscaler-linear-model)

完成本快速入門課程後，您將學習到：

> [!div class="checklist"]
> - 如何在預存程序中內嵌 R 程式碼
> - 如何透過預存程序上的輸入，將輸入傳遞至您的程式碼
> - 如何使用預存程序讓模型能夠運作

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

## <a name="create-the-model"></a>建立模型

若要建立模型，您需建立定型用的來源資料、建立模型並使用資料定型，然後將模型儲存在資料庫中，該模型便可用來產生含有新資料的預測。

### <a name="create-the-source-data"></a>建立來源資料

1. 開啟 Azure Data Studio、連接至您的執行個體，然後開啟新的查詢視窗。

1. 建立資料表來儲存定型資料。

   ```sql
   CREATE TABLE dbo.MTCars(
       mpg decimal(10, 1) NOT NULL,
       cyl int NOT NULL,
       disp decimal(10, 1) NOT NULL,
       hp int NOT NULL,
       drat decimal(10, 2) NOT NULL,
       wt decimal(10, 3) NOT NULL,
       qsec decimal(10, 2) NOT NULL,
       vs int NOT NULL,
       am int NOT NULL,
       gear int NOT NULL,
       carb int NOT NULL
   );
   ```

1. 插入內建資料集 `mtcars` 的資料。

   ```SQL
   INSERT INTO dbo.MTCars
   EXEC sp_execute_external_script @language = N'R'
       , @script = N'MTCars <- mtcars;'
       , @input_data_1 = N''
       , @output_data_1_name = N'MTCars';
   ```

   > [!TIP]
   > R 執行階段附有許多資料集，大小不一。 若要取得隨 R 一起安裝的資料集清單，請從 R 命令提示字元處，輸入 `library(help="datasets")`。

### <a name="create-and-train-the-model"></a>建立和定型模型

汽車速度資料包含兩個資料行，都是數值資料行：馬力 (`hp`) 和重量 (`wt`)。 您可以透過此資料建立一般化線性模型 (GLM)，預測車輛已裝有手排變速箱的可能性。

若要建置該模型，您需在 R 程式碼中定義該公式，然後將資料當作輸入參數來傳遞。

```sql
DROP PROCEDURE IF EXISTS generate_GLM;
GO
CREATE PROCEDURE generate_GLM
AS
BEGIN
    EXEC sp_execute_external_script
    @language = N'R'
    , @script = N'carsModel <- glm(formula = am ~ hp + wt, data = MTCarsData, family = binomial);
        trained_model <- data.frame(payload = as.raw(serialize(carsModel, connection=NULL)));'
    , @input_data_1 = N'SELECT hp, wt, am FROM MTCars'
    , @input_data_1_name = N'MTCarsData'
    , @output_data_1_name = N'trained_model'
    WITH RESULT SETS ((model VARBINARY(max)));
END;
GO
```

- `glm` 的第一個引數是 *formula* 參數，此參數將 `am` 定義成與 `hp + wt` 相依。
- 輸入資料儲存在變數 `MTCarsData` 中，會由 SQL 查詢填入。 如果您未將特定名稱指派給輸入資料，預設變數名稱將是 _InputDataSet_。

### <a name="store-the-model-in-the-database"></a>將模型儲存在資料庫中

接下來，將模型儲存在資料庫中，您就可以用此模型進行預測或重新定型。 

1. 建立資料表來儲存模型。

   R 套件如果會建立模型，其輸出通常會是二進位物件。 因此，您儲存模型的資料表必須提供一個 **varbinary(max)** 類型的資料行。

   ```sql
   CREATE TABLE GLM_models (
       model_name varchar(30) not null default('default model') primary key,
       model varbinary(max) not null
   );
   ```

1. 請執行下列 Transact-SQL 陳述式來呼叫預存程序、產生模型，然後將它儲存至您建立的資料表。

   ```sql
   INSERT INTO GLM_models(model)
   EXEC generate_GLM;
   ```

   > [!TIP]
   > 如果您二度執行此陳述式，會收到以下錯誤：「違反 PRIMARY KEY 條件約束 ...無法在物件 dbo. stopping_distance_models 中插入重複的索引鍵。」 若要避免發生此錯誤，選項之一是更新每個新模型的名稱。 例如，您可以將名稱變得更具描述性，並加入模型類型、模型的建立日期等。

     ```sql
     UPDATE GLM_models
     SET model_name = 'GLM_' + format(getdate(), 'yyyy.MM.HH.mm', 'en-gb')
     WHERE model_name = 'default model'
     ```

## <a name="score-new-data-using-the-trained-model"></a>使用定型的模型為新資料評分

*評分* 是資料科學中使用的詞彙，意指根據送入定型模型中的新資料來產生預測、可能性或其他值。 您需使用在上一節建立的模型，對新資料進行預測評分。

### <a name="create-a-table-of-new-data"></a>建立新資料的資料表

首先，建立含有新資料的資料表。

```sql
CREATE TABLE dbo.NewMTCars(
    hp INT NOT NULL
    , wt DECIMAL(10,3) NOT NULL
    , am INT NULL
)
GO

INSERT INTO dbo.NewMTCars(hp, wt)
VALUES (110, 2.634)

INSERT INTO dbo.NewMTCars(hp, wt)
VALUES (72, 3.435)

INSERT INTO dbo.NewMTCars(hp, wt)
VALUES (220, 5.220)

INSERT INTO dbo.NewMTCars(hp, wt)
VALUES (120, 2.800)
GO
```

### <a name="predict-manual-transmission"></a>預測手排變速箱

若要根據您的模型取得預測，需撰寫 SQL 指令碼來執行下列動作：

1. 取得您所需的模型
1. 取得新的輸入資料
1. 呼叫與該模型相容的 R 預測函數

資料表可能隨著時間包含多個 R 模型，所有模型都是使用不同的參數或演算法來建置，或是以不同的資料子集來定型。 在此範例中，我們使用名為 `default model` 的模型。

```sql
DECLARE @glmmodel varbinary(max) = 
    (SELECT model FROM dbo.GLM_models WHERE model_name = 'default model');

EXEC sp_execute_external_script
    @language = N'R'
    , @script = N'
            current_model <- unserialize(as.raw(glmmodel));
            new <- data.frame(NewMTCars);
            predicted.am <- predict(current_model, new, type = "response");
            str(predicted.am);
            OutputDataSet <- cbind(new, predicted.am);
            '
    , @input_data_1 = N'SELECT hp, wt FROM dbo.NewMTCars'
    , @input_data_1_name = N'NewMTCars'
    , @params = N'@glmmodel varbinary(max)'
    , @glmmodel = @glmmodel
WITH RESULT SETS ((new_hp INT, new_wt DECIMAL(10,3), predicted_am DECIMAL(10,3)));
```

上述指令碼會執行下列步驟：

- 使用 SELECT 陳述式從資料表中取得單一模型，並將其傳入作為輸入參數。

- 從資料表中擷取模型之後，請對模型呼叫 `unserialize` 函式。

- 將 `predict` 函式搭配適當的引數套用至模型，並提供新的輸入資料。

> [!NOTE]
> 此範例在測試階段新增了 `str` 函數，以檢查從 R 傳回的資料結構描述。您稍後可移除該陳述式。
>
> R 指令碼中使用的資料行名稱不一定會傳遞至預存程序輸出。 這裡使用 WITH RESULTS 子句來定義一些新的資料行名稱。

**結果**

![用以預測手排變速箱可能性的結果集](./media/r-predict-am-resultset.png)

您也可以使用 [PREDICT (Transact-SQL)](../../t-sql/queries/predict-transact-sql.md) 陳述式，根據預存模型產生預測的值或分數。

## <a name="next-steps"></a>後續步驟

如需有關使用 SQL 機器學習的 R 教學課程詳細資訊，請參閱：

- [R 教學課程](r-tutorials.md)