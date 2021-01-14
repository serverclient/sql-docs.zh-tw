---
title: R 教學課程：在 SQL 預存程序中執行預測
titleSuffix: SQL machine learning
description: 在這五部分教學課程系列的第五部分中，您將搭配使用 T-SQL 函式與 SQL 機器學習運作 SQL 預存程序中的內嵌 R 指令碼。
ms.prod: sql
ms.technology: machine-learning
ms.date: 10/15/2020
ms.topic: tutorial
author: dphansen
ms.author: davidph
ms.custom: seo-lt-2019
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||>=azuresqldb-mi-current'
ms.openlocfilehash: 026090f3eb61e2bd2ffe2b96eb09751cc3543dbf
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98101856"
---
# <a name="r-tutorial-run-predictions-in-sql-stored-procedures"></a>R 教學課程：在 SQL 預存程序中執行預測
[!INCLUDE [SQL Server 2016 SQL MI](../../includes/applies-to-version/sqlserver2016-asdbmi.md)]

在這五部分教學課程系列的第五部分中，您將了解如何使用模型預測可能的結果，以 *運作* 您在上一個部分中定型並儲存的模型。 此模型會包裝在預存程序，可由其他應用程式直接呼叫。

本文示範執行評分的兩種方式：

+ **批次評分模式**：使用 SELECT 查詢做為預存程序輸入。 此預存程序會傳回對應至輸入案例的觀察值資料表。

+ **個別計分模式**︰傳遞一組個別參數值作為輸入。  此預存程序會傳回單一資料列或值。

在本文中，您將：

> [!div class="checklist"]
> + 建立和使用預存程序進行批次評分
> + 建立和使用預存程序進行單一資料列的評分

在[第一部分](r-taxi-classification-introduction.md)中，您已安裝必要條件並還原範例資料庫。

在[第二部分](r-taxi-classification-explore-data.md)中，您已檢閱範例資料並產生一些繪圖。

在[第三部分](r-taxi-classification-create-features.md)中，您已了解如何使用 Transact-SQL 函式，從未經處理的資料建立特徵。 接著，您從預存程序呼叫該函式，建立了包含特徵值的資料表。

在[第四部分](r-taxi-classification-train-model.md)中，您已載入模組，並呼叫所需的函式，以使用 SQL Server 預存程序來建立和定型模型。

## <a name="basic-scoring"></a>基本計分

預存程序 **RPredict** 會說明將 `PREDICT` 呼叫包裝在預存程序中的基本語法。

```sql
CREATE PROCEDURE [dbo].[RPredict] (@model varchar(250), @inquery nvarchar(max))
AS 
BEGIN 

DECLARE @lmodel2 varbinary(max) = (SELECT model FROM nyc_taxi_models WHERE name = @model);  
EXEC sp_execute_external_script @language = N'R',
  @script = N' 
    mod <- unserialize(as.raw(model));
    print(summary(mod))
    OutputDataSet <- data.frame(predict(mod, InputDataSet, type = "response"));
    str(OutputDataSet)
    print(OutputDataSet)
    ',
  @input_data_1 = @inquery,
  @params = N'@model varbinary(max)',
  @model = @lmodel2 
  WITH RESULT SETS (("Score" float));
END
GO
```

+ 此 SELECT 陳述式可從資料庫取得序列化模型，並將模型儲存在 R 變數 `mod` 中，以使用 R 進一步處理。

+ 您可以從預存程序的第一個參數 `@inquery` 中指定的 [!INCLUDE[tsql](../../includes/tsql-md.md)] 查詢，取得計分的新案例。 讀取查詢資料之後，這些資料列會儲存在預設資料框架 `InputDataSet`中。 此資料框架會傳遞至產生分數的 [PREDICT](../../t-sql/queries/predict-transact-sql.md) 函數。
  
  `OutputDataSet <- data.frame(predict(mod, InputDataSet, type = "response"));`
  
  因為 data.frame 可以包含單一資料列，所以您可以使用相同的程式碼進行批次或單一計分。
  
+ `PREDICT` 函數所傳回的值是 **浮點數**，代表司機收到小費 (任何金額) 的機率。

## <a name="batch-scoring-a-list-of-predictions"></a>批次評分 (預測清單)

較常見的案例是在批次模式中產生多個觀察的預測。 在此步驟中，讓我們來看看批次評分的運作方式。

1. 先取得要使用的一小組輸入資料。 此查詢會建立「前 10 趟」車程的清單，其中包含乘客計數及進行預測所需的其他特徵。
  
   ```sql
   SELECT TOP 10 a.passenger_count AS passenger_count, a.trip_time_in_secs AS trip_time_in_secs, a.trip_distance AS trip_distance, a.dropoff_datetime AS dropoff_datetime, dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude,dropoff_longitude) AS direct_distance
   
   FROM (SELECT medallion, hack_license, pickup_datetime, passenger_count,trip_time_in_secs,trip_distance, dropoff_datetime, pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude FROM nyctaxi_sample)a

   LEFT OUTER JOIN

   (SELECT medallion, hack_license, pickup_datetime FROM nyctaxi_sample TABLESAMPLE (70 percent) REPEATABLE (98052)    )b

   ON a.medallion=b.medallion AND a.hack_license=b.hack_license 
   AND a.pickup_datetime=b.pickup_datetime
   WHERE b.medallion IS NULL
   ```

   **範例結果**

   ```text
   passenger_count   trip_time_in_secs    trip_distance  dropoff_datetime          direct_distance
   1                 283                  0.7            2013-03-27 14:54:50.000   0.5427964547
   1                 289                  0.7            2013-02-24 12:55:29.000   0.3797099614
   1                 214                  0.7            2013-06-26 13:28:10.000   0.6970098661
   ```

2. 在 [!INCLUDE[ssManStudio](../../includes/ssmanstudio-md.md)] 中建立名為 **RPredictBatchOutput** 的預存程序。

   ```sql
   CREATE PROCEDURE [dbo].[RPredictBatchOutput] (@model varchar(250), @inquery nvarchar(max))
   AS
   BEGIN
   DECLARE @lmodel2 varbinary(max) = (SELECT model FROM nyc_taxi_models WHERE name = @model);
   EXEC sp_execute_external_script 
     @language = N'R',
     @script = N'
       mod <- unserialize(as.raw(model));
       print(summary(mod))
       OutputDataSet <- data.frame(predict(mod, InputDataSet, type = "response"));
       str(OutputDataSet)
       print(OutputDataSet)
     ',
     @input_data_1 = @inquery,
     @params = N'@model varbinary(max)',
     @model = @lmodel2
     WITH RESULT SETS ((Score float));
   END
   ```

3. 在變數中提供查詢文字並當作參數傳遞至預存程序：

   ```sql
   -- Define the input data
   DECLARE @query_string nvarchar(max)
   SET @query_string='SELECT TOP 10 a.passenger_count as passenger_count, a.trip_time_in_secs AS trip_time_in_secs, a.trip_distance AS trip_distance, a.dropoff_datetime AS dropoff_datetime, dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude,dropoff_longitude) AS direct_distance FROM  (SELECT medallion, hack_license, pickup_datetime, passenger_count,trip_time_in_secs,trip_distance, dropoff_datetime, pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude FROM nyctaxi_sample  )a   LEFT OUTER JOIN (SELECT medallion, hack_license, pickup_datetime FROM nyctaxi_sample TABLESAMPLE (70 percent) REPEATABLE (98052))b ON a.medallion=b.medallion AND a.hack_license=b.hack_license AND a.pickup_datetime=b.pickup_datetime WHERE b.medallion is null'
   
   -- Call the stored procedure for scoring and pass the input data
   EXEC [dbo].[RPredictBatchOutput] @model = 'RTrainLogit_model', @inquery = @query_string;
   ```
  
此預存程序會傳回一系列的值，代表前 10 趟車程的每趟車程預測。 不過，前幾趟車程也是以相對較短的車程距離進行的單趟乘客車程，司機不太可能會收到小費。

> [!TIP]
> 除了只傳回「有小費」與「無小費」的結果，您也可以傳回預測的機率分數，然後將 WHERE 子句套用至 [分數] 資料行值，使用 0.5 或 0.7 等臨界值，將分數分類為「可能給小費」和「不可能給小費」。 雖然此步驟未包含在預存程序中，但實作起來並不難。

## <a name="single-row-scoring-of-multiple-inputs"></a>多個輸入的單一資料列評分

有時候您會想要傳入多個輸入值，並根據這些值取得單一預測。 例如，您可以設定 Excel 工作表、Web 應用程式或 Reporting Services 報表來呼叫預存程序，並且從這些應用程式提供使用者鍵入或選取的輸入。

在本節中，您將瞭解如何使用接受多個輸入 (例如乘客計數、路程距離等等) 的預存程序建立單一預測。 此預存程序會根據先前儲存的 R 模型建立分數。
  
如果您從外部應用程式呼叫預存程序，請確定資料符合 R 模型的需求。 這可能包括確保輸入資料可轉型或轉換成 R 資料類型，或是驗證資料類型和資料長度。

1. 建立預存程序 **RPredictSingleRow**。
  
   ```sql
   CREATE PROCEDURE [dbo].[RPredictSingleRow] @model varchar(50), @passenger_count int = 0, @trip_distance float = 0, @trip_time_in_secs int = 0, @pickup_latitude float = 0, @pickup_longitude float = 0, @dropoff_latitude float = 0, @dropoff_longitude float = 0
   AS
   BEGIN
   DECLARE @inquery nvarchar(max) = N'SELECT * FROM [dbo].[fnEngineerFeatures](@passenger_count, @trip_distance, @trip_time_in_secs,  @pickup_latitude, @pickup_longitude, @dropoff_latitude, @dropoff_longitude)';
   DECLARE @lmodel2 varbinary(max) = (SELECT model FROM nyc_taxi_models WHERE name = @model);
   EXEC sp_execute_external_script  
     @language = N'R',
     @script = N'  
       mod <- unserialize(as.raw(model));  
       print(summary(mod));  
       OutputDataSet <- data.frame(predict(mod, InputDataSet, type = "response"));
       str(OutputDataSet);
       print(OutputDataSet); 
       ',  
     @input_data_1 = @inquery,  
     @params = N'@model varbinary(max),@passenger_count int,@trip_distance float,@trip_time_in_secs int ,  @pickup_latitude float ,@pickup_longitude float ,@dropoff_latitude float ,@dropoff_longitude float', @model = @lmodel2, @passenger_count =@passenger_count, @trip_distance=@trip_distance, @trip_time_in_secs=@trip_time_in_secs, @pickup_latitude=@pickup_latitude, @pickup_longitude=@pickup_longitude, @dropoff_latitude=@dropoff_latitude, @dropoff_longitude=@dropoff_longitude  
     WITH RESULT SETS ((Score float));  
   END
   ```

2. 請以手動方式提供值來試試看。
  
   開啟新的 [查詢] 視窗，然後呼叫預存程序，並針對每個參數提供值。 這些參數代表模型所使用的功能資料行，而且是必要的。

   ```sql
   EXEC [dbo].[RPredictSingleRow] @model = 'RTrainLogit_model',
   @passenger_count = 1,
   @trip_distance = 2.5,
   @trip_time_in_secs = 631,
   @pickup_latitude = 40.763958,
   @pickup_longitude = -73.973373,
   @dropoff_latitude =  40.782139,
   @dropoff_longitude = -73.977303
   ```

   或者，對於[參數至預存程序](../../relational-databases/stored-procedures/specify-parameters.md)使用下列這種較短的形式：
  
   ```sql
   EXEC [dbo].[RPredictSingleRow] 'RTrainLogit_model', 1, 2.5, 631, 40.763958,-73.973373, 40.782139,-73.977303
   ```

3. 結果指出，從這前 10 趟車程獲得小費的機率偏低 (零)，因為全部都是單一乘客且距離相對較短的車程。

## <a name="conclusions"></a>結論

您已瞭解如何在預存程序中內嵌 R 程式碼，您可以擴充這些實務來建立自己的模型。 與 [!INCLUDE[tsql](../../includes/tsql-md.md)] 的整合可讓您更輕鬆地部署 R 模型進行預測，並納入模型重新定型作為企業資料工作流程的一部分。

## <a name="next-steps"></a>後續步驟

在本文章中，您將：

> [!div class="checklist"]
> + 已建立和使用預存程序進行批次評分
> + 已建立和使用預存程序進行單一資料列的評分

如需 R 的詳細資訊，請參閱 [SQL Server中的 R 延伸模組](../concepts/extension-r.md)。