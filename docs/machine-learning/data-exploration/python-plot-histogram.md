---
title: 使用 Python 繪製長條圖以進行資料探索
titleSuffix: SQL machine learning
description: 了解如何使用 Python 來建立長條圖以將資料視覺化。
author: dphansen
ms.author: davidph
ms.date: 07/14/2020
ms.topic: how-to
ms.prod: sql
ms.technology: machine-learning
monikerRange: '>=sql-server-2017||>=sql-server-linux-ver15||=azuresqldb-mi-current||=azuresqldb-current'
ms.openlocfilehash: 466abc8687a7f325d216a6b5b6545bc516b2b34d
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98101988"
---
# <a name="plot-histograms-in-python"></a>以 Python 繪製長條圖 
[!INCLUDE[SQL Server SQL DB SQL MI](../../includes/applies-to-version/sql-asdb-asdbmi.md)]

本文描述如何使用 Python 套件 [pandas'.hist()](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.hist.html) 來繪製資料。 SQL 資料庫是用來將具有非重疊連續值的長條圖資料間隔視覺化的來源。

## <a name="prerequisites"></a>必要條件：

::: moniker range=">=sql-server-2017||>=sql-server-linux-ver15"
* [適用於 Windows 的 SQL Server](../../database-engine/install-windows/install-sql-server.md) 或[適用於 Linux 的 SQL Server](../../linux/sql-server-linux-overview.md)
::: moniker-end

::: moniker range="=azuresqldb-current"
* [Azure SQL Database](/azure/sql-database/sql-database-get-started-portal)
::: moniker-end

::: moniker range="=azuresqldb-mi-current"
* [Azure SQL 受控執行個體](/azure/azure-sql/managed-instance/instance-create-quickstart)

* 請參閱 [SQL Server Management Studio](../../ssms/download-sql-server-management-studio-ssms.md)，以將範例資料庫還原到 Azure SQL 受控執行個體。
::: moniker-end

* Azure Data Studio。 若要安裝，請參閱 [Azure Data Studio](../../azure-data-studio/what-is-azure-data-studio.md)。

* 請參閱 [還原範例資料庫](../../samples/adventureworks-install-configure.md)，以取得本文中所使用的範例資料。

## <a name="verify-restored-database"></a>驗證還原的資料庫

您可透過查詢 **Person.CountryRegion** 資料表來驗證還原的資料庫是否存在：
```sql
USE AdventureWorksDW;
SELECT * FROM Person.CountryRegion;
```
  
## <a name="install-python-packages"></a>安裝 Python 套件

[下載及安裝 Azure Data Studio](../../azure-data-studio/download-azure-data-studio.md)。

安裝下列 Python 套件：
  * pyodbc
  * pandas

  若要安裝這些套件：

  1. 在 Azure Data Studio 筆記本中，選取 [管理套件]。
  2. 在 [管理套件] 窗格中，選取 [新增] 索引標籤。
  3. 針對每個下列套件，輸入套件名稱，按一下 [搜尋]，然後按一下 [安裝]。

## <a name="plot-histogram"></a>繪製長條圖

長條圖中顯示的分散式資料是以 AdventureWorksDW 的 SQL 查詢為基礎。 長條圖會將資料及資料值的頻率視覺化。 編輯連接字串變數 'server'、'database'、'username' 和 'password' 以連線到 SQL 資料庫。

建立新的筆記本：

1. 在 Azure Data Studio 中，選取 [檔案]，然後選取 [新增筆記本]。
2. 在筆記本中，選取核心 [Python3]，然後選取 [+程式碼]。
3. 將程式碼貼到筆記本中，然後選取 [全部執行]。

```python
import pyodbc 
import pandas as plt
# Some other example server values are
# server = 'localhost\sqlexpress' # for a named instance
# server = 'myserver,port' # to specify an alternate port
server = 'servername' 
database = 'AdventureWorksDW' 
username = 'yourusername' 
password = 'databasename'  
cnxn = pyodbc.connect('DRIVER={SQL Server};SERVER='+server+';DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
sql = "SELECT DATEDIFF(year, c.BirthDate, GETDATE()) AS Age FROM [dbo].[FactInternetSales] s INNER JOIN dbo.DimCustomer c ON s.CustomerKey = c.CustomerKey"
df = pd.read_sql(sql, cnxn)
df.hist(bins=10)
```

顯示畫面會顯示 FactInternetSales 資料表中客戶的年齡分佈。

![Pandas 長條圖](./media/python-histogram.png)