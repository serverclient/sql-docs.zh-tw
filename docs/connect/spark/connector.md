---
title: 適用於 SQL Server 的 Apache Spark 連接器
description: 了解如何使用適用於 SQL Server 的 Apache Spark 連接器。
ms.custom: ''
ms.date: 08/31/2020
ms.prod: sql
ms.prod_service: connectivity
ms.technology: connectivity
ms.topic: conceptual
author: rajmera3
ms.author: raajmera
ms.reviewer: mikeray
ms.openlocfilehash: b4c7027fd4695e1c1110fb19ae8cefc9be34ac10
ms.sourcegitcommit: 1a544cf4dd2720b124c3697d1e62ae7741db757c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2020
ms.locfileid: "97438851"
---
# <a name="apache-spark-connector-sql-server--azure-sql"></a>Apache Spark 連接器：SQL Server 和 Azure SQL

適用於 SQL Server 和 Azure SQL 的 Apache Spark 連接器是一種高效能連接器，可讓您在巨量資料分析中使用交易資料，並且保存結果以供臨機操作查詢或報告使用。 此連接器可讓您使用內部部署或雲端中的任何 SQL 資料庫，作為 Spark 作業的輸入資料來源或輸出資料接收器。

此程式庫包含適用於 SQL Server 和 Azure SQL 的 Apache Spark 連接器的原始程式碼。

[Apache Spark](https://spark.apache.org/) 是用於進行大規模資料處理的整合分析引擎。

您可以從 [Maven](https://search.maven.org/search?q=spark-mssql-connector) 取得連接器。 若要匯入連接器，請使用座標 `com.microsoft.azure:spark-mssql-connector:<latest version>`。 您也可以從來源建置連接器，或從 GitHub 的 [發行] 區段下載 Jar。 如需此連接器的最新資訊，請參閱 [SQL Spark 連接器 GitHub 存放庫](https://github.com/microsoft/sql-spark-connector)。

## <a name="supported-features"></a>支援的功能

* 支援所有 Spark 繫結 (Scala、Python、R)
* 基本驗證和 Active Directory (AD) 金鑰表支援
* 重新排序的 `dataframe` 寫入支援
* 支援寫入至 SQL Server 巨量資料叢集中的 SQL Server 單一執行個體和資料集區
* SQL Server 單一執行個體的穩定連接器支援

| 元件                            | 支援的版本              |
|--------------------------------------|---------------------------------|
| Apache Spark                         | 2.4.5 (不支援 Spark 3.0) |
| Scala                                | 2.11                            |
| Microsoft JDBC Driver for SQL Server | 8.2                             |
| Microsoft SQL Server                 | SQL Server 2008 或更新版本        |
| Azure SQL Database                  | 支援                       |

> [!NOTE]
> 未進行將此連接器用於 Azure Synapse Analytics 的測試。 雖然或許可運作，但可能會產生非預期的結果。

### <a name="supported-options"></a>支援的選項
適用於 SQL Server 和 Azure SQL 的 Apache Spark 連接器支援此處定義的選項：[SQL DataSource JDBC](https://spark.apache.org/docs/latest/sql-data-sources-jdbc.html)

此外，也支援下列選項

| 選項 | 預設 | 描述 |
| --------- | ------------------ | ------------------------------------------ |
| `reliabilityLevel` | `BEST_EFFORT` | `BEST_EFFORT` 或 `NO_DUPLICATES`。 `NO_DUPLICATES` 會在執行程式重新啟動情況下會實作可靠的插入 |
| `dataPoolDataSource` | `none` | `none` 表示未設定此值，且連接器應寫入至 SQL Server 的單一執行個體。 將此值設定為資料來源名稱，以在巨量資料叢集中寫入資料集區資料表|
| `isolationLevel` | `READ_COMMITTED` | 指定隔離等級 |
| `tableLock` | `false` | 使用 `TABLOCK` 選項執行插入可改善寫入效能 |
| `schemaCheckEnabled` | `true` | 設定為 false 時，會停用 strict 資料框架和 SQL 資料表結構描述檢查 |

其他[大量複製選項](../jdbc/using-bulk-copy-with-the-jdbc-driver.md#sqlserverbulkcopyoptions)可設定為 `dataframe` 上的選項，將會在寫入時傳遞至 `bulkcopy` API

## <a name="performance-comparison"></a>表現比較

適用於 SQL Server 和 Azure SQL 的 Apache Spark 連接器寫入 SQL Server 的速度比一般 JDBC 連接器快上 15 倍。 效能特性會因資料類型、資料量、使用的選項而異，並且可顯示不同次執行的變化。 下列效能結果是以 Spark `dataframe` 中的 143.9M 個資料列覆寫 SQL 資料表所花費的時間。 Spark `dataframe` 是在讀取使用 [Spark TPCDS 基準測試](https://github.com/databricks/spark-sql-perf)產生的 `store_sales` HDFS 資料表後所建構的。 將 `store_sales` 讀取至 `dataframe` 的時間排除在外。 結果是超過三次執行的平均值。

| 連接器類型 | 選項。 | 描述 |  寫入的時間 |
| --------- | ------------------ | -------------------------------------| ---------- |
| `JDBCConnector` | 預設 | 使用預設選項的一般 JDBC 連接器 |  1385 秒 |
| `sql-spark-connector` | `BEST_EFFORT` | 使用預設選項的最大投入量 `sql-spark-connector` |580 秒 |
| `sql-spark-connector` | `NO_DUPLICATES ` | 可靠 `sql-spark-connector` | 709 秒 |
| `sql-spark-connector` | `BEST_EFFORT` + tabLock=true | 已啟用資料表鎖定的最大投入量 `sql-spark-connector` | 72 秒 |
| `sql-spark-connector` | `NO_DUPLICATES ` + tabLock=true| 已啟用資料表鎖定的可靠 `sql-spark-connector`| 198 秒 |

Config

- Spark 設定：num_executors = 20、executor_memory = '1664 m'、executor_cores = 2
- 資料產生設定：scale_factor=50、partitioned_tables=true
- 具有 143,997,590 個資料列的資料檔案 `store_sales`

環境

- [SQL Server 巨量資料叢集](../../big-data-cluster/release-notes-big-data-cluster.md) CU5
- `master` 6 個以上的節點
- 每個節點：第 5 代伺服器、512 GB RAM、4 TB NVM、NIC 10 GB

## <a name="commonly-faced-issues"></a>常發生的問題

### `java.lang.NoClassDefFoundError: com/microsoft/aad/adal4j/AuthenticationException`

在 Hadoop 環境中使用較舊版本的 mssql 驅動程式 (現已包含在此連接器中)，就會發生此問題。 如果您使用先前的 Azure SQL 連接器，並已將驅動程式手動安裝到該叢集上以達到 Azure Active Directory 相容性，您將需要移除這些驅動程式。

修正問題的步驟：

1. 如果您使用一般 Hadoop 環境，請檢查並移除 mssql jar：`rm $HADOOP_HOME/share/hadoop/yarn/lib/mssql-jdbc-6.2.1.jre7.jar`。 如果您使用 Databricks，請新增全域或叢集 init 指令碼以從 `/databricks/jars` 資料夾中移除舊版的 mssql 驅動程式，或將以下這一行新增至現有的指令碼：`rm /databricks/jars/*mssql*`
2. 新增 `adal4j` 和 `mssql` 套件。 例如，您可以使用 Maven，但任何方式應該都可行。 

   > [!CAUTION]
   > 請勿以這種方式安裝 SQL Spark 連接器。

1. 將驅動程式類別新增至您的連線設定。 例如：

   ```csharp
   connectionProperties = {
     `Driver`: `com.microsoft.sqlserver.jdbc.SQLServerDriver`
   }`
   ```

如需詳細資訊和說明，請參閱 [https://github.com/microsoft/sql-spark-connector/issues/26](https://github.com/microsoft/sql-spark-connector/issues/26#issuecomment-672006339) 的解決方法。

## <a name="get-started"></a>開始使用

適用於 SQL Server 和 Azure SQL 的 Apache Spark 連接器以 Spark DataSourceV1 API 和 SQL Server Bulk API 為建置基礎，並使用與內建 JDBC Spark-SQL 連接器相同的介面。 這項整合可讓您輕鬆地整合連接器並移轉現有的 Spark 作業，只要使用 `com.microsoft.sqlserver.jdbc.spark` 更新格式參數即可。

若要在您的專案中包含連接器，請下載此存放庫，並使用 SBT 建置 jar。

## <a name="write-to-a-new-sql-table"></a>寫入新的 SQL 資料表

> [!WARNING]
> `overwrite` 模式會先卸除資料表 (如果依預設已存在於資料庫中)。 請謹慎使用此選項，以免發生非預期的資料遺失。

使用模式 `overwrite` 時，如果您在重新建立資料表時未使用 `truncate` 選項，則會遺失索引。 資料行存放區資料表現在會是堆積。 如果您想要維護現有的索引，請同時指定值為 true 的選項 `truncate`。 例如： `.option("truncate","true")` 。

```python
server_name = "jdbc:sqlserver://{SERVER_ADDR}"
database_name = "database_name"
url = server_name + ";" + "databaseName=" + database_name + ";"

table_name = "table_name"
username = "username"
password = "password123!#" # Please specify password here

try:
  df.write \
    .format("com.microsoft.sqlserver.jdbc.spark") \
    .mode("overwrite") \
    .option("url", url) \
    .option("dbtable", table_name) \
    .option("user", username) \
    .option("password", password) \
    .save()
except ValueError as error :
    print("Connector write failed", error)
```

### <a name="append-to-sql-table"></a>附加至 SQL 資料表

```python
try:
  df.write \
    .format("com.microsoft.sqlserver.jdbc.spark") \
    .mode("append") \
    .option("url", url) \
    .option("dbtable", table_name) \
    .option("user", username) \
    .option("password", password) \
    .save()
except ValueError as error :
    print("Connector write failed", error)
```

## <a name="specify-the-isolation-level"></a>指定隔離等級

此連接器在對資料庫執行大量插入時，依預設會使用 `READ_COMMITTED` 隔離等級。 如果您想要覆寫隔離等級，請使用 `mssqlIsolationLevel` 選項，如下所示。

```python
    .option("mssqlIsolationLevel", "READ_UNCOMMITTED") \
```

## <a name="read-from-sql-table"></a>讀取 SQL 資料表

```python
jdbcDF = spark.read \
        .format("com.microsoft.sqlserver.jdbc.spark") \
        .option("url", url) \
        .option("dbtable", table_name) \
        .option("user", username) \
        .option("password", password).load()
```

## <a name="azure-active-directory-authentication"></a>Azure Active Directory 驗證

### <a name="python-example-with-service-principal"></a>使用服務主體的 Python 範例

```python
context = adal.AuthenticationContext(authority)
token = context.acquire_token_with_client_credentials(resource_app_id_url, service_principal_id, service_principal_secret)
access_token = token["accessToken"]

jdbc_db = spark.read \
        .format("com.microsoft.sqlserver.jdbc.spark") \
        .option("url", url) \
        .option("dbtable", table_name) \
        .option("accessToken", access_token) \
        .option("encrypt", "true") \
        .option("hostNameInCertificate", "*.database.windows.net") \
        .load()
```

### <a name="python-example-with-active-directory-password"></a>使用 Active Directory 密碼的 Python 範例

```python
jdbc_df = spark.read \
        .format("com.microsoft.sqlserver.jdbc.spark") \
        .option("url", url) \
        .option("dbtable", table_name) \
        .option("authentication", "ActiveDirectoryPassword") \
        .option("user", user_name) \
        .option("password", password) \
        .option("encrypt", "true") \
        .option("hostNameInCertificate", "*.database.windows.net") \
        .load()
```

必須安裝必要的相依性，才能使用 Active Directory 進行驗證。

針對 **Scala**，必須安裝 `_com.microsoft.aad.adal4j_` 成品。

針對 **Python**，必須安裝 `_adal_` 程式庫。  您可以透過 pip 加以取得。

如需範例，請查看[範例筆記本](https://github.com/microsoft/sql-spark-connector/tree/master/samples)。

## <a name="support"></a>支援

適用於 Azure SQL 和 SQL Server 的 Apache Spark 連接器是開放原始碼專案。 此連接器並未隨附任何 Microsoft 支援。 如有連接器的相關問題或疑問，請在此專案存放庫中建立問題。 連接器社群會持續運作並監視提交。

## <a name="next-steps"></a>後續步驟

瀏覽 [SQL Spark 連接器 GitHub 存放庫](https://github.com/microsoft/sql-spark-connector)。

如需隔離等級的資訊，請參閱 [SET TRANSACTION ISOLATION LEVEL &#40;Transact-SQL&#41;](../../t-sql/statements/set-transaction-isolation-level-transact-sql.md)。
