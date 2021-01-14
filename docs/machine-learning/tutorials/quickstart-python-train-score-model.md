---
title: 快速入門：在 Python 中將模型定型
titleSuffix: SQL machine learning
description: 在此快速入門中，您將會使用 Python 來建立及定型預測模型。 您會將模型儲存在您資料庫中的資料表，然後以 SQL 機器學習使用模型從新資料預測值。
ms.prod: sql
ms.technology: machine-learning
ms.date: 09/28/2020
ms.topic: quickstart
author: dphansen
ms.author: davidph
ms.custom: seo-lt-2019
monikerRange: '>=sql-server-2017||>=sql-server-linux-ver15||=azuresqldb-mi-current'
ms.openlocfilehash: 6b5d59568f68894b3691287cd581f390101f0b02
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98101921"
---
# <a name="quickstart-create-and-score-a-predictive-model-in-python-with-sql-machine-learning"></a>快速入門：透過 SQL 機器學習，建立 Python 的預測模型並為其評分
[!INCLUDE [SQL Server 2017 SQL MI](../../includes/applies-to-version/sqlserver2017-asdbmi.md)]

在此快速入門中，您將會使用 Python 來建立及定型預測模型。 您會將模型儲存至 SQL Server 執行個體中的資料表，然後使用模型以利用 [SQL Server 機器學習服務](../sql-server-machine-learning-services.md)或在 [Azure SQL 受控執行個體機器學習服務](/azure/azure-sql/managed-instance/machine-learning-services-overview)，或 [SQL Server 巨量資料叢集](../../big-data-cluster/machine-learning-services.md)來預測新資料中的值。

您將建立並執行在 SQL 中執行的兩個預存程序。 第一個預存程序使用傳統的鳶尾花資料集，並產生貝氏機率分類模型，以根據花卉特徵來預測鳶尾花的品種。 第二個程序為評分用，會呼叫在第一個程式中產生的模型，並根據新資料輸出一組預測。 將 Python 程式碼放入 SQL 預存程序後，作業會包含在 SQL 中、可重複使用，而且可由其他預存程序和用戶端應用程式呼叫。

完成本快速入門課程後，您將學習到：

> [!div class="checklist"]
> - 如何在預存程序中內嵌 Python 程式碼
> - 如何透過預存程序上的輸入，將輸入傳遞至您的程式碼
> - 如何使用預存程序讓模型能夠運作

## <a name="prerequisites"></a>Prerequisites

您需要符合下列必要條件，才能執行此快速入門。

- SQL 資料庫位於其中一個平台上：
  - [SQL Server 機器學習服務](../sql-server-machine-learning-services.md)。 若要安裝，請參閱 [Windows 安裝指南](../install/sql-machine-learning-services-windows-install.md)或 [Linux 安裝指南](../../linux/sql-server-linux-setup-machine-learning.md?toc=%2Fsql%2Fmachine-learning%2Ftoc.json)。
  - SQL Server 巨量資料叢集。 查看如何[啟用 SQL Server 巨量資料叢集上的機器學習服務](../../big-data-cluster/machine-learning-services.md)。
  - Azure SQL 受控執行個體機器學習服務。 如需詳細資訊，請參閱 [Azure SQL 受控執行個體機器學習服務概觀](/azure/azure-sql/managed-instance/machine-learning-services-overview)。

- 執行包含 Python 指令碼之 SQL 查詢的工具。 本快速入門使用 [Azure Data Studio](../../azure-data-studio/what-is-azure-data-studio.md)。

- 本練習使用的範例資料是鳶尾花範例資料。 依照 [鳶尾花示範資料](demo-data-iris-in-sql.md)中的指示，建立範例資料庫 **irissql**。

## <a name="create-a-stored-procedure-that-generates-models"></a>建立會產生模型的預存程序

在此步驟中，您將建立一個預存程序，該程序會產生預測結果的模型。

1. 開啟 Azure Data Studio、連線至您的 SQL 執行個體，並開啟新的查詢視窗。

1. 連線至 irissql 資料庫。

    ```sql
    USE irissql
    GO
    ```

1. 複製下列程式碼，以建立新的預存程序。

   執行時，此程序會呼叫 [sp_execute_external_script](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md) 來啟動 Python 工作階段。 

   Python 程式碼所需的輸入會當做此預存程序中的輸入參數來傳遞。 輸出將會是已定型的模型，並以機器學習演算法的 Python **scikit-learn** 程式庫為基礎。

   此程式碼會使用 [**pickle**](https://docs.python.org/2/library/pickle.html) 來序列化模型。 此模型將使用 **iris_data** 資料表中資料行 0 到 4 的資料定型。 

   您在此程序第二部分中看到的參數會明白指出資料輸入和模型輸出。 我們建議您在預存程序中執行的 Python 程式碼中，盡量清楚地定義輸入和輸出，進而對應至在執行時間傳遞的預存程序輸入和輸出。

    ```sql
    CREATE PROCEDURE generate_iris_model (@trained_model VARBINARY(max) OUTPUT)
    AS
    BEGIN
        EXECUTE sp_execute_external_script @language = N'Python'
            , @script = N'
    import pickle
    from sklearn.naive_bayes import GaussianNB
    GNB = GaussianNB()
    trained_model = pickle.dumps(GNB.fit(iris_data[["Sepal.Length", "Sepal.Width", "Petal.Length", "Petal.Width"]], iris_data[["SpeciesId"]].values.ravel()))
    '
            , @input_data_1 = N'select "Sepal.Length", "Sepal.Width", "Petal.Length", "Petal.Width", "SpeciesId" from iris_data'
            , @input_data_1_name = N'iris_data'
            , @params = N'@trained_model varbinary(max) OUTPUT'
            , @trained_model = @trained_model OUTPUT;
    END;
    GO
    ```

1. 確認預存程序存在。 

   如果上一個步驟中的 T-SQL 指令碼正確執行，會建立名為 **generate_iris_model** 的新預存程序，並新增至 **irissql** 資料庫。 您可以在 Azure Data Studio [物件總管] 的 [可程式性] 底下找到預存程序。

## <a name="execute-the-procedure-to-create-and-train-models"></a>執行此程序，以建立和定型模型

在此步驟中，您需執行程序來執行內嵌程式碼，並建立定型和序列化模型做為輸出。 

儲存在您資料庫中以便重複使用的模型，皆序列化為位元組資料流，並儲存在資料庫資料表中的 VARBINARY(MAX) 資料行。 模型在建立、定型、序列化並儲存到資料庫之後，就可以由其他程式或評分工作負載中的 [PREDICT T-SQL](../../t-sql/queries/predict-transact-sql.md) 函數來呼叫。

1. 執行下列指令碼以執行程序。 執行預存程序的特定陳述式是第四行的 `EXECUTE`。

   此特定指令碼會刪除名稱相同 (貝氏機率分類) 的現有模型，以騰出空間給透過重新執行相同程序所建立的新模型。 若未刪除模型會發生錯誤，說明物件已經存在。 當您建立 **irissql** 資料庫時，模型會儲存在名為 **iris_models** 的資料表中。

    ```sql
    DECLARE @model varbinary(max);
    DECLARE @new_model_name varchar(50)
    SET @new_model_name = 'Naive Bayes'
    EXECUTE generate_iris_model @model OUTPUT;
    DELETE iris_models WHERE model_name = @new_model_name;
    INSERT INTO iris_models (model_name, model) values(@new_model_name, @model);
    GO
    ```

1. 確認已插入模型。

    ```sql
    SELECT * FROM dbo.iris_models
    ```

    **結果**

    | model_name  | model |
    |---|-----------------|
    | 貝氏機率分類 | 0x800363736B6C6561726E2E6E616976655F62617965730A... | 

## <a name="create-and-execute-a-stored-procedure-for-generating-predictions"></a>建立和執行預存程序以產生預測

現在您已建立、定型及儲存模型，請繼續進行下一個步驟：建立會產生預測的預存程序。 方法是呼叫 `sp_execute_external_script` 以執行 Python 指令碼，載入序列化模型並提供新資料輸入進行評分。

1. 執行下列程式碼，以建立執行評分的預存程序。 在執行時，此程序會載入二進位模型、使用資料行 `[1,2,3,4]` 做為輸入，並指定資料行 `[0,5,6]` 做為輸出。

   ```sql
   CREATE PROCEDURE predict_species (@model VARCHAR(100))
   AS
   BEGIN
       DECLARE @nb_model VARBINARY(max) = (
               SELECT model
               FROM iris_models
               WHERE model_name = @model
               );
   
       EXECUTE sp_execute_external_script @language = N'Python'
           , @script = N'
   import pickle
   irismodel = pickle.loads(nb_model)
   species_pred = irismodel.predict(iris_data[["Sepal.Length", "Sepal.Width", "Petal.Length", "Petal.Width"]])
   iris_data["PredictedSpecies"] = species_pred
   OutputDataSet = iris_data[["id","SpeciesId","PredictedSpecies"]] 
   print(OutputDataSet)
   '
           , @input_data_1 = N'select id, "Sepal.Length", "Sepal.Width", "Petal.Length", "Petal.Width", "SpeciesId" from iris_data'
           , @input_data_1_name = N'iris_data'
           , @params = N'@nb_model varbinary(max)'
           , @nb_model = @nb_model
       WITH RESULT SETS((
                   "id" INT
                 , "SpeciesId" INT
                 , "SpeciesId.Predicted" INT
                   ));
   END;
   GO
   ```

2. 執行預存程序，並將模型命名為「貝氏機率分類」，讓程序知道要使用哪一個模型。

   ```sql
   EXECUTE predict_species 'Naive Bayes';
   GO
   ```

   當您執行預存程序時，該程序會傳回 Python data.frame。 T-SQL 這一行會指定傳回結果的結構描述：`WITH RESULT SETS ( ("id" int, "SpeciesId" int, "SpeciesId.Predicted" int));`。 您可以將結果插入新的資料表中，或是將結果傳回應用程式。

   ![執行預存程序的結果集](media/train-score-using-python-NB-model-results.png)

   結果是使用花卉特徵做為輸入的相關品種共有 150 筆預測。 對於大多數的觀察來說，預測的品種會和實際品種相符。

   這個範例相對簡單，是使用 Python 鳶尾花資料集進行定型和評分。 較常見的方法包含執行 SQL 查詢來取得新資料，並將該資料當做 `InputDataSet` 傳遞至 Python。

## <a name="conclusion"></a>結論

您在本練習中已了解如何建立不同工作專用的預存程序，其中每個預存程序都使用系統預存程序 `sp_execute_external_script` 來啟動 Python 流程。 Python 流程的輸入會傳遞至 `sp_execute_external` 做為參數。 Python 指令碼本身與資料庫中的資料變數皆以輸入形式傳遞。

一般來說，您應該只打算使用 Python 程式碼已經完善的 Azure Data Studio，或是傳回資料列輸出的簡易 Python 程式碼。 Azure Data Studio 這項工具可支援 T-SQL 等查詢語言，並傳回扁平化資料列集。 如果您的程式碼會產生散佈圖或長條圖等視覺效果輸出，則需要可在預存程序之外轉譯影像的個別工具或終端使用者應用程式。

對於習慣撰寫全功能指令碼來處理各種作業的 Python 開發人員而言，可能不需要將工作整理成不同的程序。 但定型和評分有不同的使用案例。 區隔這兩者後，您可以將每個工作放在不同的排程裡，並設定每個作業的權限範圍。

最後一項優勢是可以使用參數來修改流程。 在本練習中，已將建立模型 (在此範例中名為「貝氏機率分類」) 的 Python 程式碼當做輸入傳遞至第二個預存程序，以便在評分流程中呼叫此模型。 本練習只會使用一個模型，但您可以想像在評分工作中將模型參數化後，該指令碼將會變得更加實用。

## <a name="next-steps"></a>後續步驟

如需有關使用 SQL 機器學習的 Python 教學課程詳細資訊，請參閱：

- [Python 教學課程](python-tutorials.md)