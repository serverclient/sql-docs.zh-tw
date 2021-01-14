---
title: 快速入門：Python 資料結構
titleSuffix: SQL machine learning
description: 在此快速入門中，了解如何使用 SQL 機器學習以處理 Python 中的資料結構與資料物件。
ms.prod: sql
ms.technology: machine-learning
ms.date: 09/28/2020
ms.topic: quickstart
author: dphansen
ms.author: davidph
ms.custom: seo-lt-2019
monikerRange: '>=sql-server-2017||>=sql-server-linux-ver15||=azuresqldb-mi-current'
ms.openlocfilehash: c6ab10331055e6142d8d5a03b4875921dbb2f609
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98101930"
---
# <a name="quickstart-data-structures-and-objects-using-python-with-sql-machine-learning"></a>快速入門：使用 Python 搭配 SQL 機器學習的資料結構與物件
[!INCLUDE [SQL Server 2017 SQL MI](../../includes/applies-to-version/sqlserver2017-asdbmi.md)]

在此快速入門中，您將會使用 [SQL Server 機器學習服務](../sql-server-machine-learning-services.md)、[Azure SQL 受控執行個體機器學習服務](/azure/azure-sql/managed-instance/machine-learning-services-overview)或在 [SQL Server 巨量資料叢集](../../big-data-cluster/machine-learning-services.md)上了解使用 Python 時的資料結構與資料類型。 在此快速入門中，您將會了解如何在 Python 與 SQL Server 之間移動資料，以及可能發生的常見問題。

SQL 機器學習依賴 Python **Pandas** 套件，這非常適合用以處理表格式資料。 不過，您無法將純量從 Python 傳遞至您的資料庫，並預期這樣「就能作用」。 在此快速入門中，您將複習部分的基本資料結構定義，以在 Python 與資料庫之間傳遞表格式資料時，為您可能遇到的額外問題做好準備。

首先要了解的概念包括：

- 資料框架是具有 _多個_ 資料行的資料表。
- 資料框架的單一資料行是一種類似清單的物件，稱為「序列」。
- 資料框架的單一值稱為儲存格，並依索引存取。

如果 data.frame 需要表格式結構，您該如何將計算的單一結果公開為資料框架？ 答案是以序列形式來表示單一純量值，便可以輕鬆地轉換成資料框架。

> [!NOTE]
> 傳回日期時，SQL 中的 Python 會使用 DATETIME，其日期範圍限制在 1753-01-01 (-53690) 到 9999-12-31 (2958463) 之間。

## <a name="prerequisites"></a>Prerequisites

您需要符合下列必要條件，才能執行此快速入門。

- SQL 資料庫位於其中一個平台上：
  - [SQL Server 機器學習服務](../sql-server-machine-learning-services.md)。 若要安裝，請參閱 [Windows 安裝指南](../install/sql-machine-learning-services-windows-install.md)或 [Linux 安裝指南](../../linux/sql-server-linux-setup-machine-learning.md?toc=%2Fsql%2Fmachine-learning%2Ftoc.json)。
  - SQL Server 巨量資料叢集。 查看如何[啟用 SQL Server 巨量資料叢集上的機器學習服務](../../big-data-cluster/machine-learning-services.md)。
  - Azure SQL 受控執行個體機器學習服務。 如需詳細資訊，請參閱 [Azure SQL 受控執行個體機器學習服務概觀](/azure/azure-sql/managed-instance/machine-learning-services-overview)。

- 執行包含 Python 指令碼之 SQL 查詢的工具。 本快速入門使用 [Azure Data Studio](../../azure-data-studio/what-is-azure-data-studio.md)。

## <a name="scalar-value-as-a-series"></a>以純量值作為序列

這個範例會執行一些簡單的數學運算，並將純量轉換成序列。

1. 序列需要索引，您可以手動指派 (如本文所示)，或以程式設計方式指派。

   ```sql
   EXECUTE sp_execute_external_script @language = N'Python'
       , @script = N'
   a = 1
   b = 2
   c = a/b
   print(c)
   s = pandas.Series(c, index =["simple math example 1"])
   print(s)
   '
   ```

   因為序列尚未轉換成資料框架，所以這些值會在 [訊息] 視窗中傳回，但是您會看到結果更進一步以表格式格式顯示。

   **結果**

   ```text
   STDOUT message(s) from external script: 
   0.5
   simple math example 1    0.5
   dtype: float64
   ```

1. 若要增加序列的長度，您可以使用陣列來加入新的值。

   ```sql
   EXECUTE sp_execute_external_script @language = N'Python'
       , @script = N'
   a = 1
   b = 2
   c = a/b
   d = a*b
   s = pandas.Series([c,d])
   print(s)
   '
   ```

   如果您未指定索引，則會產生值開頭為 0 且結尾為陣列長度的索引。

   **結果**

   ```text
   STDOUT message(s) from external script:
   0    0.5
   1    2.0
   dtype: float64
   ```

1. 如果您增加 **索引** 值的數目，但不新增新的 **資料** 值，則系統會重複資料值來填滿序列。

   ```sql
   EXECUTE sp_execute_external_script @language = N'Python'
       , @script = N'
   a = 1
   b = 2
   c = a/b
   s = pandas.Series(c, index =["simple math example 1", "simple math example 2"])
   print(s)
   '
   ```

   **結果**

   ```text
   STDOUT message(s) from external script:
   0.5
   simple math example 1    0.5
   simple math example 2    0.5
   dtype: float64
   ```

## <a name="convert-series-to-data-frame"></a>將序列轉換成資料框架

將純量數學結果轉換為表格式結構之後，您仍然需要將其轉換為 SQL 機器學習可以處理的格式。

1. 若要將數列轉換成 data.frame，請呼叫 pandas [DataFrame](https://pandas.pydata.org/pandas-docs/stable/dsintro.html#dataframe) \(英文\) 方法。

   ```sql
   EXECUTE sp_execute_external_script @language = N'Python'
       , @script = N'
   import pandas as pd
   a = 1
   b = 2
   c = a/b
   d = a*b
   s = pandas.Series([c,d])
   print(s)
   df = pd.DataFrame(s)
   OutputDataSet = df
   '
   WITH RESULT SETS((ResultValue FLOAT))
   ```

   結果如下所示。 即使您使用索引來取得 data.frame 中的特定值，索引值也不是輸出的一部分。

   **結果**

   |ResultValue|
   |------|
   |0.5|
   |2|

## <a name="output-values-into-dataframe"></a>將值輸出至 data.frame

現在您會從 data.frame 中兩個數學運算結果序列輸出特定的值。 第一個具有由 Python 產生之循序值的索引。 第二個使用字串值的任意索引。

1. 下列範例會使用整數索引來取得序列中的值。

   ```sql
   EXECUTE sp_execute_external_script @language = N'Python'
       , @script = N'
   import pandas as pd
   a = 1
   b = 2
   c = a/b
   d = a*b
   s = pandas.Series([c,d])
   print(s)
   df = pd.DataFrame(s, index=[1])
   OutputDataSet = df
   '
   WITH RESULT SETS((ResultValue FLOAT))
   ```

   **結果**

   |ResultValue|
   |------|
   |2.0|

   請記住，自動產生的索引會從 0 開始。 請嘗試使用超出範圍的索引值，並查看會發生什麼事。

1. 現在使用字串索引從另一個資料框架取得單一值。

   ```sql
   EXECUTE sp_execute_external_script @language = N'Python'
       , @script = N'
   import pandas as pd
   a = 1
   b = 2
   c = a/b
   s = pandas.Series(c, index =["simple math example 1", "simple math example 2"])
   print(s)
   df = pd.DataFrame(s, index=["simple math example 1"])
   OutputDataSet = df
   '
   WITH RESULT SETS((ResultValue FLOAT))
   ```

   **結果**

   |ResultValue|
   |------|
   |0.5|

   如果您嘗試使用數值索引來取得此序列中的值，就會發生錯誤。

## <a name="next-steps"></a>後續步驟

若要了解如何使用 SQL 機器學習編寫進階 Python 函數，請遵循此快速入門：

> [!div class="nextstepaction"]
> [編寫進階的 Python 函數](quickstart-python-functions.md)