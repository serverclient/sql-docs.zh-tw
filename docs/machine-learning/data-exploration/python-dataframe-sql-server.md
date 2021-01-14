---
title: 將 Python 資料框架插入至 SQL 資料表
titleSuffix: SQL machine learning
description: 如何將資料框架中的資料插入至 SQL 資料表。
author: dphansen
ms.author: davidph
ms.date: 07/23/2020
ms.topic: how-to
ms.prod: sql
ms.technology: machine-learning
monikerRange: '>=sql-server-2017||>=sql-server-linux-ver15||=azuresqldb-mi-current||=azuresqldb-current'
ms.openlocfilehash: 9a8b30cc1fa5af3e22ce0294e8eff21cf413e2c6
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98102037"
---
# <a name="insert-python-dataframe-into-sql-table"></a>將 Python 資料框架插入至 SQL 資料表
[!INCLUDE[SQL Server SQL DB SQL MI](../../includes/applies-to-version/sql-asdb-asdbmi.md)]

本文描述如何使用 Python 中的 [pyodbc](../../connect/python/pyodbc/python-sql-driver-pyodbc.md) 套件，將 [pandas](https://pandas.pydata.org/) 資料框架插入至 SQL 資料庫。

## <a name="prerequisites"></a>必要條件

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

您可藉由查詢 **HumanResources.Department** 資料表，以驗證還原的資料庫是否存在：

```sql
USE AdventureWorks;
SELECT * FROM HumanResources.Department;
```

## <a name="install-python-packages"></a>安裝 Python 套件

* [下載並安裝 Azure Data Studio](../../azure-data-studio/download-azure-data-studio.md)

安裝下列 Python 套件：
  * pyodbc
  * pandas

  若要安裝這些套件：

  1. 在 Azure Data Studio 筆記本中，選取 [管理套件]。
  2. 在 [管理套件] 窗格中，選取 [新增] 索引標籤。
  3. 針對每個下列套件，輸入套件名稱，按一下 [搜尋]，然後按一下 [安裝]。

## <a name="connect-to-sql-server-using-azure-data-studio"></a>使用 Azure Data Studio 連線到 SQL Server

[使用 Azure Data Studio 進行連線](../../azure-data-studio/quickstart-sql-server.md)。

1. 連線到 Adventureworks 資料庫，以建立新的資料表 HumanResources.DepartmentTest。 此 SQL 資料表將會用於插入資料框架。

```sql
CREATE TABLE [HumanResources].[DepartmentTest](
[DepartmentID] [smallint] NOT NULL,
[Name] [dbo].[Name] NOT NULL,
[GroupName] [dbo].[Name] NOT NULL
)
GO
```

## <a name="create-csv-file"></a>建立 CSV 檔案

複製文字，並將檔案儲存為資料框架的 department.csv。

```text
DepartmentID,Name,GroupName,
1,Engineering,Research and Development,
2,Tool Design,Research and Development,
3,Sales,Sales and Marketing,
4,Marketing,Sales and Marketing,
5,Purchasing,Inventory Management,
6,Research and Development,Research and Development,
7,Production,Manufacturing,
8,Production Control,Manufacturing,
9,Human Resources,Executive General and Administration,
10,Finance,Executive General and Administration,
11,Information Services,Executive General and Administration,
12,Document Control,Quality Assurance,
13,Quality Assurance,Quality Assurance,
14,Facilities and Maintenance,Executive General and Administration,
15,Shipping and Receiving,Inventory Management,
16,Executive,Executive General and Administration
```

## <a name="connect-to-sql-using-python"></a>使用 Python 連線到 SQL

1. 編輯連接字串變數 'server'、'database'、'username' 和 'password' 以連線到 SQL 資料庫。

2. 編輯 CSV 檔案的路徑。

## <a name="load-dataframe-from-csv-file"></a>從 CSV 檔案載入資料框架

使用 Python `pandas` 套件來建立資料框架並載入 CSV 檔案。 連線到 SQL，以將資料框架載入至新的 SQL 資料表 HumanResources.DepartmentTest。

建立新的筆記本：

1. 在 Azure Data Studio 中，選取 [檔案]，然後選取 [新增筆記本]。
2. 在筆記本中，選取核心 [Python3]，然後選取 [+程式碼]。
3. 將程式碼貼到筆記本中，然後選取 [全部執行]。

 ```Python
import pyodbc
import pandas as pd
# insert data from csv file into dataframe.
# working directory for csv file: type "pwd" in Azure Data Studio or Linux
# working directory in Windows c:\users\username
df = pd.read_csv("c:\\user\\username\department.csv")
# Some other example server values are
# server = 'localhost\sqlexpress' # for a named instance
# server = 'myserver,port' # to specify an alternate port
server = 'yourservername' 
database = 'AdventureWorks' 
username = 'username' 
password = 'yourpassword' 
cnxn = pyodbc.connect('DRIVER={SQL Server};SERVER='+server+';DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
# Insert Dataframe into SQL Server:
for index, row in df.iterrows():
     cursor.execute("INSERT INTO HumanResources.DepartmentTest (DepartmentID,Name,GroupName) values(?,?,?)", row.DepartmentID, row.Name, row.GroupName)
cnxn.commit()
cursor.close()
```

## <a name="confirm-row-count-in-sql"></a>確認 SQL 中的資料列計數

執行 SQL 陳述式，確認已成功將資料框架中的資料載入資料表。

```sql
SELECT count(*) from HumanResources.DepartmentTest;
```

結果

```bash
(No column name)
16
```

## <a name="next-steps"></a>後續步驟

+ [使用 Python 繪製長條圖以進行資料探索](../data-exploration/python-plot-histogram.md)