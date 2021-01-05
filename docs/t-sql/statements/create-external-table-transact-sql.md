---
description: CREATE EXTERNAL TABLE (Transact-SQL)
title: CREATE EXTERNAL TABLE (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 01/27/2020
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- CREATE_EXTERNAL_TABLE
- CREATE EXTERNAL TABLE
- PolyBase, T-SQL
dev_langs:
- TSQL
helpviewer_keywords:
- External
- External, table create
- PolyBase, external table
ms.assetid: 6a6fd8fe-73f5-4639-9908-2279031abdec
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 49eb63fb4e90f20a7c63778efdd017b7ca32439a
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97643143"
---
# <a name="create-external-table-transact-sql"></a>CREATE EXTERNAL TABLE (Transact-SQL)

建立外部資料表

本文提供適用於您所選擇之 SQL 產品的語法、引數、備註、權限和範例。

如需語法慣例的詳細資訊，請參閱 [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)。

[!INCLUDE[select-product](../../includes/select-product.md)]

::: moniker range=">=sql-server-2016||>=sql-server-linux-2017"

:::row:::
    :::column:::
        **_\* SQL Server \*_** &nbsp;
    :::column-end:::
    :::column:::
        [SQL Database](create-external-table-transact-sql.md?view=azuresqldb-current&preserve-view=true)
    :::column-end:::
    :::column:::
        [Azure Synapse<br />Analytics](create-external-table-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)
    :::column-end:::
    :::column:::
        [Analytics Platform<br />System (PDW)](create-external-table-transact-sql.md?view=aps-pdw-2016-au7&preserve-view=true)
    :::column-end:::
:::row-end:::

&nbsp;

## <a name="overview-sql-server"></a>概觀：SQL Server

此命令會建立 PolyBase 的外部資料表來存取儲存在 Hadoop 叢集中的資料，或是參考儲存在 Hadoop 叢集或 Azure Blob 儲存體中資料的 Azure Blob 儲存體 PolyBase 外部資料表。

**適用於**：SQL Server 2016 (或更新版本)

使用具備外部資料來源的外部資料表進行 PolyBase 查詢。 外部資料來源會用來建立連線能力，並支援這些主要使用案例：

- 使用 [PolyBase](../../relational-databases/polybase/polybase-guide.md) 來執行資料虛擬化和資料載入
- 使用 SQL Server 或 SQL Database 來執行大量載入作業 (使用 `BULK INSERT` 或 `OPENROWSET`)

另請參閱 [CREATE EXTERNAL DATA SOURCE](../../t-sql/statements/create-external-data-source-transact-sql.md) 及 [DROP EXTERNAL TABLE](../../t-sql/statements/drop-external-table-transact-sql.md)。

## <a name="syntax"></a>語法

```syntaxsql
-- Create a new external table
CREATE EXTERNAL TABLE { database_name.schema_name.table_name | schema_name.table_name | table_name }
    ( <column_definition> [ ,...n ] )
    WITH (
        LOCATION = 'folder_or_filepath',
        DATA_SOURCE = external_data_source_name,
        FILE_FORMAT = external_file_format_name
        [ , <reject_options> [ ,...n ] ]
    )
[;]

<reject_options> ::=
{
    | REJECT_TYPE = value | percentage
    | REJECT_VALUE = reject_value
    | REJECT_SAMPLE_VALUE = reject_sample_value
}

<column_definition> ::=
column_name <data_type>
    [ COLLATE collation_name ]
    [ NULL | NOT NULL ]
```

## <a name="arguments"></a>引數

*{ database_name.schema_name.table_name | schema_name.table_name | table_name }* 所要建立資料表的名稱，由一到三個部分組成。 針對外部資料表，SQL 只會儲存資料表中繼資料，以及 Hadoop 或 Azure Blob 儲存體中所參考檔案或資料夾的基本統計資料。 實際上不會有任何資料會移至或儲存於 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中。

> [!IMPORTANT]
> 為了達到最佳效能，如果外部資料來源驅動程式支援三部分名稱，則強烈建議您提供三部分名稱。  

\<column_definition> [ ,...*n* ] CREATE EXTERNAL TABLE 支援設定資料行名稱、資料類型、可 NULL 性和定序功能。 您無法在外部資料表上使用 DEFAULT CONSTRAINT。

資料行定義 (包括資料類型及資料行數目) 必須符合外部檔案中的資料。 若有不相符的情形，系統在查詢實際資料時將會拒絕檔案資料列。

LOCATION = '*folder_or_filepath*' 指定 Hadoop 或 Azure Blob 儲存體中實際資料的資料夾或檔案路徑，以及檔案名稱。 位置會從根資料夾開始。 根資料夾是在外部資料來源中指定的資料位置。

在 SQL Server 中，CREATE EXTERNAL TABLE 陳述式會建立尚未存在的路徑和資料夾。 您接著可以使用 INSERT INTO 將資料從本機 SQL Server 資料表匯出至外部資料來源。 如需詳細資訊，請參閱 [PolyBase 查詢](../../relational-databases/polybase/polybase-queries.md)。

若您將 LOCATION 指定為資料夾，會從外部資料表中選取的 PolyBase 查詢，將會從該資料夾及其所有子資料夾中擷取檔案。 PolyBase 和 Hadoop 相同，並不會傳回隱藏的資料夾。 它也不會傳回檔案名稱是以底線 (_) 或句號 (.) 開始的檔案。

在此範例中，若 LOCATION='/webdata/'，PolyBase 查詢將會傳回來自 mydata.txt 和 mydata2.txt 的資料列。 其不會傳回 mydata3.txt，因為該檔案是隱藏資料夾中的檔案。 它不會傳回 _hidden.txt，因為它是隱藏的檔案。

![外部資料表的遞迴資料](../../t-sql/statements/media/aps-polybase-folder-traversal.png "外部資料表的遞迴資料")

若要變更預設設定並僅從根資料夾讀取，請在 core-site.xml 設定檔中將 \<polybase.recursive.traversal> 屬性設為 'false'。 此檔案位於 `<SqlBinRoot>\PolyBase\Hadoop\Conf with SqlBinRoot the bin root of SQl Server`。 例如： `C:\\Program Files\\Microsoft SQL Server\\MSSQL13.XD14\\MSSQL\\Binn` 。

DATA_SOURCE = *external_data_source_name* 指定包含外部資料位置的外部資料來源名稱。 此位置是 Hadoop 檔案系統 (HDFS)、Azure 儲存體 Blob 容器或 Azure Data Lake Store。 若要建立外部資料來源，請使用 [CREATE EXTERNAL DATA SOURCE](../../t-sql/statements/create-external-data-source-transact-sql.md)。

FILE_FORMAT = *external_file_format_name* 指定儲存外部資料檔案類型和壓縮方法的外部檔案格式物件名稱。 若要建立外部檔案格式，請使用 [CREATE EXTERNAL FILE FORMAT](../../t-sql/statements/create-external-file-format-transact-sql.md)。

拒絕選項：您可以指定拒絕參數，決定 PolyBase 處理從外部資料來源所擷取「已變更」記錄的方式。 若資料記錄的實際資料類型或資料行數目，與外部資料表的資料行定義不相符，該資料記錄就會被系統視為「已修改」。

當您不指定或變更拒絕值時，PolyBase 就會使用預設值。 拒絕參數的相關資訊會在您搭配 CREATE EXTERNAL TABLE 陳述式建立外部資料表時，以額外中繼資料的形式儲存。 當未來有 SELECT 陳述式或 SELECT INTO SELECT 陳述式從外部資料表中選取資料時，PolyBase 將會使用拒絕選項來判斷在實際的查詢失敗之前，可以拒絕的資料列數目或百分比。 查詢將會傳回 (部分) 結果，直到超過拒絕閾值為止。 接著它便會失敗並顯示適當的錯誤訊息。

REJECT_TYPE = **value** | percentage 指明要將 REJECT_VALUE 選項指定為常值或百分比。

value 表示 REJECT_VALUE 是常值，不是百分比。 PolyBase 查詢會在被拒絕資料列的數目超過 *reject_value* 時失敗。

例如，若 REJECT_VALUE = 5 且 REJECT_TYPE = value，PolyBase SELECT 查詢將會在系統拒絕五個資料列之後失敗。

percentage 表示 REJECT_VALUE 是百分比，不是常值。 PolyBase 查詢會在被拒絕資料列的 *百分比* 超過 *reject_value* 時失敗。 失敗的資料列百分比會每隔一段時間就計算一次。

REJECT_VALUE = *reject_value* 指定在查詢失敗前可拒絕的資料列數目或百分比。

針對 REJECT_TYPE = value，*reject_value* 必須為介於 0 和 2,147,483,647 的整數。

針對 REJECT_TYPE = percentage，*reject_value* 必須為介於 0 和 100 的浮點數。

REJECT_SAMPLE_VALUE = *reject_sample_value* 在您指定 REJECT_TYPE = percentage 時，此屬性是必要項目。 它會決定在 PolyBase 重新計算被拒絕資料列的百分比之前，應嘗試擷取的資料列數目。

*reject_sample_value* 參數必須是介於 0 和 2,147,483,647 的整數。

例如，如果 REJECT_SAMPLE_VALUE = 1000，PolyBase 將會在已嘗試從外部資料檔案匯入 1000 個資料列之後，計算失敗的資料列百分比。 如果失敗的資料列百分比小於 *reject_value*，PolyBase 就會嘗試擷取另外 1000 個資料列。 它會在嘗試匯入每個額外的 1000 個資料列之後，持續重新計算失敗的資料列百分比。

> [!NOTE]
> 由於 PolyBase 會不時計算失敗的資料列百分比，因此實際的失敗資料列百分比可能超出 *reject_value*。

範例：

此範例說明三個 REJECT 選項彼此如何互動。 例如，如果 REJECT_TYPE = percentage、REJECT_VALUE = 30 且 REJECT_SAMPLE_VALUE = 100，就可能發生下列案例：

- PolyBase 會嘗試擷取前 100 個資料列；其中有 25 個失敗，75 個成功。
- 失敗資料列的百分比會計算為 25%，低於拒絕值 30%。 因此，PolyBase 將會繼續從外部資料來源擷取資料。
- PolyBase 會嘗試載入接下來的 100 個資料列；這次有 25 個資料列成功，75 個資料列失敗。
- 失敗資料列的百分比在重新計算後為 50%。 失敗資料列的百分比已超出 30% 的拒絕值。
- PolyBase 查詢在嘗試傳回前 200 個資料列後，會因被拒絕的資料列達 50% 而失敗。 請注意，相符的資料列會在 PolyBase 查詢偵測到超出拒絕閾值之前傳回。

SCHEMA_NAME：SCHEMA_NAME 子句能讓您將外部資料表定義對應至位於遠端資料庫上不同結構描述的資料表。 使用此子句來區分同時存在於本機和遠端資料庫上的結構描述。

OBJECT_NAME：OBJECT_NAME 子句能讓您將外部資料表定義對應至位於遠端資料庫上具有不同名稱的資料表。 使用此子句來區分同時存在於本機和遠端資料庫上的物件名稱。

DISTRIBUTION：選擇性。 只有類型為 SHARD_MAP_MANAGER 的資料庫才需要這個引數。 這個引數能控制資料表是否會被視為分區資料表或複寫資料表。 使用 **SHARDED** (*column name*) 資料表，來自不同資料表的資料便不會彼此重疊。 **REPLICATED** 指定資料表在每個分區上都有相同的資料。 **ROUND_ROBIN** 指出系統會使用應用程式特定的方法來散發資料。

## <a name="permissions"></a>權限

需要下列使用者權限：

- **CREATE TABLE**
- **ALTER ANY SCHEMA**
- **ALTER ANY EXTERNAL DATA SOURCE**
- **ALTER ANY EXTERNAL FILE FORMAT**
- **CONTROL DATABASE**

請注意，建立外部資料來源的登入，必須具有讀取和寫入位於 Hadoop 或 Azure Blob 儲存體上之外部資料來源的權限。

> [!IMPORTANT]
> ALTER ANY EXTERNAL DATA SOURCE 權限可授與任何主體建立及修改任何外部資料來源物件的能力，因此也能讓主體存取資料庫上的所有資料庫範圍認證。 必須將此權限視為具高度權限，因此必須僅授與系統中受信任的主體。

## <a name="error-handling"></a>錯誤處理

執行 CREATE EXTERNAL TABLE 陳述式的同時，PolyBase 會嘗試連線至外部資料來源。 如果連線的嘗試失敗，陳述式就會失敗，且不會建立外部資料表。 由於 PolyBase 在查詢失敗之前會多次嘗試連線，因此可能需要一分鐘或更久的時間，命令才會失敗。

## <a name="general-remarks"></a>一般備註

在臨機操作的案例中 (例如 SELECT FROM EXTERNAL TABLE)，PolyBase 會將擷取自外部資料來源的資料列，儲存在暫存資料表中。 在查詢完成之後，PolyBase 便會移除並刪除該暫存資料表。 SQL 資料表中不會永久存放資料。

相反地，在匯入案例中 (例如 SELECT INTO FROM EXTERNAL TABLE)，PolyBase 會將擷取自外部資料來源的資料列，以永久資料的形式儲存在 SQL 資料表中。 新的資料表會在查詢執行期間，當 PolyBase 擷取外部資料時建立。

PolyBase 可以將部分的查詢計算推送至 Hadoop 以改善查詢效能。 此動作稱為述詞下推。 若要啟用它，請在 [CREATE EXTERNAL DATA SOURCE](../../t-sql/statements/create-external-data-source-transact-sql.md) 中指定 Hadoop 資源管理員位置選項。

您可以建立許多參考相同或不同外部資料來源的外部資料表。

## <a name="limitations-and-restrictions"></a>限制事項

因為外部資料表的資料不受 SQL Server 直接管理控制，所以可隨時由外部處理序變更或移除。 因此，針對外部資料表的查詢結果並不保證具有確定性。 相同的查詢在每次針對外部資料表執行時，都有可能傳回不同的結果。 同樣地，在移動或移除外部資料的情況下，查詢也有可能會失敗。

您可以建立多個參考不同外部資料來源的外部資料表。 如果您同時針對不同的 Hadoop 資料來源執行查詢，則每個 Hadoop 來源都必須使用相同的「Hadoop 連線能力」伺服器組態設定。 例如，您不能同時針對 Cloudera Hadoop 叢集和 Hortonworks Hadoop 叢集執行查詢，因為這些叢集是使用不同的組態設定。 如需組態設定和支援的組合，請參閱 [PolyBase 連線能力設定](../../database-engine/configure-windows/polybase-connectivity-configuration-transact-sql.md)。

外部資料表上僅允許使用下列資料定義語言 (DDL) 陳述式：

- CREATE TABLE 和 DROP TABLE
- CREATE STATISTICS 和 DROP STATISTICS
- CREATE VIEW 和 DROP VIEW

不支援的建構和作業：

- 外部資料表資料行上的 DEFAULT 限制式
- 刪除、插入及更新的資料操作語言 (DML) 作業

### <a name="query-limitations"></a>查詢限制

執行 32 個並行的 PolyBase 查詢時，PolyBase 每個資料夾可取用的檔案數目上限為 33000 個檔案。 這個上限數同時包含了每個 HDFS 資料夾中的檔案和子資料夾。 如果並行程度小於 32，使用者就可以針對 HDFS 中內含超過 33000 個檔案的資料夾執行 PolyBase 查詢。 我們建議您使用簡短的外部檔案路徑，且所使用的每個 HDFS 資料夾檔案數目不要超過 30000 個檔案。 參考太多檔案時，可能會發生 Java 虛擬機器 (JVM) 記憶體不足的例外狀況。

### <a name="table-width-limitations"></a>資料表寬度限制

SQL Server 2016 中的 PolyBase 具有 32 KB 的資料列寬度限制，這是以依資料表定義的單一有效資料列大小上限為基礎。 若資料行結構描述的總和超過 32 KB，PolyBase 將無法查詢資料。

### <a name="data-type-limitations"></a>資料類型限制

下列資料類型不能用在 PolyBase 外部資料表中：

- `geography`
- `geometry`
- `hierarchyid`
- `image`
- `text`
- `nText`
- `xml`
- 任何使用者定義的類型

## <a name="locking"></a>鎖定

SCHEMARESOLUTION 物件上的共用鎖定。

## <a name="security"></a>安全性

外部資料表的資料檔案會儲存在 Hadoop 或 Azure Blob 儲存體中。 這些資料檔案是由您自己的處理程序所建立及管理。 因此您應負責管理外部資料的安全性。

## <a name="examples"></a>範例

### <a name="a-create-an-external-table-with-data-in-text-delimited-format"></a>A. 建立具有文字分隔格式資料的外部資料表

此範例會示範建立將資料儲存為文字分隔檔案之外部資料表的所有步驟。 它會定義外部資料來源 *mydatasource*，以及外部檔案格式 *myfileformat*。 系統接著會在 CREATE EXTERNAL TABLE 陳述式中參考這些資料庫層級的物件。 如需詳細資訊，請參閱 [CREATE EXTERNAL DATA SOURCE](../../t-sql/statements/create-external-data-source-transact-sql.md) 和 [CREATE EXTERNAL FILE FORMAT](../../t-sql/statements/create-external-file-format-transact-sql.md)。

```sql
CREATE EXTERNAL DATA SOURCE mydatasource
WITH (
    TYPE = HADOOP,
    LOCATION = 'hdfs://xxx.xxx.xxx.xxx:8020'
)

CREATE EXTERNAL FILE FORMAT myfileformat
WITH (
    FORMAT_TYPE = DELIMITEDTEXT,
    FORMAT_OPTIONS (FIELD_TERMINATOR ='|')
);

CREATE EXTERNAL TABLE ClickStream (
    url varchar(50),
    event_date date,
    user_IP varchar(50)
)
WITH (
        LOCATION='/webdata/employee.tbl',
        DATA_SOURCE = mydatasource,
        FILE_FORMAT = myfileformat
    )
;
```

### <a name="b-create-an-external-table-with-data-in-rcfile-format"></a>B. 建立具有 RCFile 格式資料的外部資料表

此範例會示範建立將資料格式化為 RCFile 之外部資料表的所有步驟。 它會定義外部資料來源 *mydatasource_rc*，以及外部檔案格式 *myfileformat_rc*。 系統接著會在 CREATE EXTERNAL TABLE 陳述式中參考這些資料庫層級的物件。 如需詳細資訊，請參閱 [CREATE EXTERNAL DATA SOURCE](../../t-sql/statements/create-external-data-source-transact-sql.md) 和 [CREATE EXTERNAL FILE FORMAT](../../t-sql/statements/create-external-file-format-transact-sql.md)。

```sql
CREATE EXTERNAL DATA SOURCE mydatasource_rc
WITH (
    TYPE = HADOOP,
    LOCATION = 'hdfs://xxx.xxx.xxx.xxx:8020'
)

CREATE EXTERNAL FILE FORMAT myfileformat_rc
WITH (
    FORMAT_TYPE = RCFILE,
    SERDE_METHOD = 'org.apache.hadoop.hive.serde2.columnar.LazyBinaryColumnarSerDe'
)
;

CREATE EXTERNAL TABLE ClickStream_rc (
    url varchar(50),
    event_date date,
    user_ip varchar(50)
)
WITH (
        LOCATION='/webdata/employee_rc.tbl',
        DATA_SOURCE = mydatasource_rc,
        FILE_FORMAT = myfileformat_rc
    )
;
```

### <a name="c-create-an-external-table-with-data-in-orc-format"></a>C. 建立具有 ORC 格式資料的外部資料表

此範例會示範建立將資料格式化為 ORC 檔案之外部資料表的所有步驟。 它會定義外部資料來源 mydatasource_orc，以及外部檔案格式 myfileformat_orc。 系統接著會在 CREATE EXTERNAL TABLE 陳述式中參考這些資料庫層級的物件。 如需詳細資訊，請參閱 [CREATE EXTERNAL DATA SOURCE](../../t-sql/statements/create-external-data-source-transact-sql.md) 和 [CREATE EXTERNAL FILE FORMAT](../../t-sql/statements/create-external-file-format-transact-sql.md)。

```sql
CREATE EXTERNAL DATA SOURCE mydatasource_orc
WITH (
    TYPE = HADOOP,
    LOCATION = 'hdfs://xxx.xxx.xxx.xxx:8020'
)

CREATE EXTERNAL FILE FORMAT myfileformat_orc
WITH (
    FORMAT = ORC,
    COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'
)
;

CREATE EXTERNAL TABLE ClickStream_orc (
    url varchar(50),
    event_date date,
    user_ip varchar(50)
)
WITH (
        LOCATION='/webdata/',
        DATA_SOURCE = mydatasource_orc,
        FILE_FORMAT = myfileformat_orc
    )
;
```

### <a name="d-querying-hadoop-data"></a>D. 查詢 Hadoop 資料

Clickstream 為能夠連線至 Hadoop 叢集上 employee.tbl 分隔符號文字檔案的外部資料表。 下列查詢看起來像是針對標準資料表的查詢。 不過，此查詢會從 Hadoop 擷取資料，然後計算結果。

```sql
SELECT TOP 10 (url) FROM ClickStream WHERE user_ip = 'xxx.xxx.xxx.xxx'
;
```

### <a name="e-join-hadoop-data-with-sql-data"></a>E. 將 Hadoop 資料與 SQL 資料聯結

此查詢看起來像是針對兩個 SQL 資料表的標準 JOIN 查詢。 差異在於，PolyBase 會從 Hadoop 擷取 Clickstream 資料，然後將它聯結至 UrlDescription 資料表。 其中一個資料表是外部資料表，另一個則是標準 SQL 資料表。

```sql
SELECT url.description
FROM ClickStream cs
JOIN UrlDescription url ON cs.url = url.name
WHERE cs.url = 'msdn.microsoft.com'
;
```

### <a name="f-import-data-from-hadoop-into-a-sql-table"></a>F. 將資料從 Hadoop 匯入至 SQL 資料表

此範例會建立新的 SQL 資料表 ms_user，它能永久儲存標準 SQL 資料表 *user* 及外部資料表 *ClickStream* 之間的聯結結果。

```sql
SELECT DISTINCT user.FirstName, user.LastName
INTO ms_user
FROM user INNER JOIN (
    SELECT * FROM ClickStream WHERE cs.url = 'www.microsoft.com'
    ) AS ms
ON user.user_ip = ms.user_ip
;
```

### <a name="g-create-an-external-table-for-a-sharded-data-source"></a>G. 針對分區資料來源建立外部資料表

此範例會使用 SCHEMA_NAME 和 OBJECT_NAME 子句將遠端 DMV 重新對應至外部資料表。

```sql
CREATE EXTERNAL TABLE [dbo].[all_dm_exec_requests]([session_id] smallint NOT NULL,
  [request_id] int NOT NULL,
  [start_time] datetime NOT NULL,
  [status] nvarchar(30) NOT NULL,
  [command] nvarchar(32) NOT NULL,
  [sql_handle] varbinary(64),
  [statement_start_offset] int,
  [statement_end_offset] int,
  [cpu_time] int NOT NULL)
WITH
(
  DATA_SOURCE = MyExtSrc,
  SCHEMA_NAME = 'sys',
  OBJECT_NAME = 'dm_exec_requests',
  DISTRIBUTION=ROUND_ROBIN
);
```

### <a name="h-create-an-external-table-for-sql-server"></a>H. 建立 SQL Server 的外部資料表

```sql
     -- Create a Master Key
      CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'S0me!nfo';
    GO
     /*  specify credentials to external data source
     *  IDENTITY: user name for external source.
     *  SECRET: password for external source.
     */
     CREATE DATABASE SCOPED CREDENTIAL SqlServerCredentials
     WITH IDENTITY = 'username', Secret = 'password';
    GO

    /* LOCATION: Location string should be of format '<vendor>://<server>[:<port>]'.
    * PUSHDOWN: specify whether computation should be pushed down to the source. ON by default.
    * CREDENTIAL: the database scoped credential, created above.
    */
    CREATE EXTERNAL DATA SOURCE SQLServerInstance
    WITH (
    LOCATION = 'sqlserver://SqlServer',
    -- PUSHDOWN = ON | OFF,
      CREDENTIAL = SQLServerCredentials
    );
    GO

    CREATE SCHEMA sqlserver;
    GO

     /* LOCATION: sql server table/view in 'database_name.schema_name.object_name' format
     * DATA_SOURCE: the external data source, created above.
     */
     CREATE EXTERNAL TABLE sqlserver.customer(
     C_CUSTKEY INT NOT NULL,
     C_NAME VARCHAR(25) NOT NULL,
     C_ADDRESS VARCHAR(40) NOT NULL,
     C_NATIONKEY INT NOT NULL,
     C_PHONE CHAR(15) NOT NULL,
     C_ACCTBAL DECIMAL(15,2) NOT NULL,
     C_MKTSEGMENT CHAR(10) NOT NULL,
     C_COMMENT VARCHAR(117) NOT NULL
      )
      WITH (
      LOCATION='tpch_10.dbo.customer',
      DATA_SOURCE=SqlServerInstance
     );
```

### <a name="i-create-an-external-table-for-oracle"></a>I. 建立 Oracle 的外部資料表

```sql
  -- Create a Master Key
   CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'password';
   /*
   * Specify credentials to external data source
   * IDENTITY: user name for external source.
   * SECRET: password for external source.
   */
   CREATE DATABASE SCOPED CREDENTIAL credential_name
   WITH IDENTITY = 'username', Secret = 'password';

   /*
   * LOCATION: Location string should be of format '<vendor>://<server>[:<port>]'.
   * PUSHDOWN: specify whether computation should be pushed down to the source. ON by default.
   * CONNECTION_OPTIONS: Specify driver location
   * CREDENTIAL: the database scoped credential, created above.
   */
   CREATE EXTERNAL DATA SOURCE external_data_source_name
   WITH (
     LOCATION = 'oracle://<server address>[:<port>]',
     -- PUSHDOWN = ON | OFF,
     CREDENTIAL = credential_name)

   /*
   * LOCATION: Oracle table/view in '.<schema_name>.<object_name>' format
   * DATA_SOURCE: the external data source, created above.
   */
   CREATE EXTERNAL TABLE customers(
   [O_ORDERKEY] DECIMAL(38) NOT NULL,
   [O_CUSTKEY] DECIMAL(38) NOT NULL,
   [O_ORDERSTATUS] CHAR COLLATE Latin1_General_BIN NOT NULL,
   [O_TOTALPRICE] DECIMAL(15,2) NOT NULL,
   [O_ORDERDATE] DATETIME2(0) NOT NULL,
   [O_ORDERPRIORITY] CHAR(15) COLLATE Latin1_General_BIN NOT NULL,
   [O_CLERK] CHAR(15) COLLATE Latin1_General_BIN NOT NULL,
   [O_SHIPPRIORITY] DECIMAL(38) NOT NULL,
   [O_COMMENT] VARCHAR(79) COLLATE Latin1_General_BIN NOT NULL
   )
   WITH (
    LOCATION='.mySchema.customer',
    DATA_SOURCE= external_data_source_name
   );
```

### <a name="j-create-an-external-table-for-teradata"></a>J. 建立 Teradata 的外部資料表

```sql
  -- Create a Master Key
   CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'password';

   /*
   * Specify credentials to external data source
   * IDENTITY: user name for external source.
   * SECRET: password for external source.
   */
   CREATE DATABASE SCOPED CREDENTIAL credential_name
   WITH IDENTITY = 'username', Secret = 'password';

    /* LOCATION: Location string should be of format '<vendor>://<server>[:<port>]'.
    * PUSHDOWN: specify whether computation should be pushed down to the source. ON by default.
    * CONNECTION_OPTIONS: Specify driver location
    * CREDENTIAL: the database scoped credential, created above.
    */
    CREATE EXTERNAL DATA SOURCE external_data_source_name
    WITH (
    LOCATION = teradata://<server address>[:<port>],
   -- PUSHDOWN = ON | OFF,
    CREDENTIAL =credential_name
    );


     /* LOCATION: Teradata table/view in '<database_name>.<object_name>' format
      * DATA_SOURCE: the external data source, created above.
      */
     CREATE EXTERNAL TABLE customer(
      L_ORDERKEY INT NOT NULL,
      L_PARTKEY INT NOT NULL,
     L_SUPPKEY INT NOT NULL,
     L_LINENUMBER INT NOT NULL,
     L_QUANTITY DECIMAL(15,2) NOT NULL,
     L_EXTENDEDPRICE DECIMAL(15,2) NOT NULL,
     L_DISCOUNT DECIMAL(15,2) NOT NULL,
     L_TAX DECIMAL(15,2) NOT NULL,
     L_RETURNFLAG CHAR NOT NULL,
     L_LINESTATUS CHAR NOT NULL,
     L_SHIPDATE DATE NOT NULL,
     L_COMMITDATE DATE NOT NULL,
     L_RECEIPTDATE DATE NOT NULL,
     L_SHIPINSTRUCT CHAR(25) NOT NULL,
     L_SHIPMODE CHAR(10) NOT NULL,
     L_COMMENT VARCHAR(44) NOT NULL
     )
     WITH (
     LOCATION='customer',
     DATA_SOURCE= external_data_source_name
     );
```

### <a name="k-create-an-external-table-for-mongodb"></a>K. 建立 MongoDB 的外部資料表

```sql
  -- Create a Master Key
   CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'password';

   /*
   * Specify credentials to external data source
   * IDENTITY: user name for external source.
   * SECRET: password for external source.
   */
   CREATE DATABASE SCOPED CREDENTIAL credential_name
   WITH IDENTITY = 'username', Secret = 'password';

     /* LOCATION: Location string should be of format '<type>://<server>[:<port>]'.
    * PUSHDOWN: specify whether computation should be pushed down to the source. ON by default.
    * CONNECTION_OPTIONS: Specify driver location
    * CREDENTIAL: the database scoped credential, created above.
    */
    CREATE EXTERNAL DATA SOURCE external_data_source_name
    WITH (
    LOCATION = mongodb://<server>[:<port>],
    -- PUSHDOWN = ON | OFF,
      CREDENTIAL = credential_name
    );

     /* LOCATION: MongoDB table/view in '<database_name>.<schema_name>.<object_name>' format
     * DATA_SOURCE: the external data source, created above.
     */
     CREATE EXTERNAL TABLE customers(
     [O_ORDERKEY] DECIMAL(38) NOT NULL,
     [O_CUSTKEY] DECIMAL(38) NOT NULL,
     [O_ORDERSTATUS] CHAR COLLATE Latin1_General_BIN NOT NULL,
     [O_TOTALPRICE] DECIMAL(15,2) NOT NULL,
     [O_ORDERDATE] DATETIME2(0) NOT NULL,
     [O_COMMENT] VARCHAR(79) COLLATE Latin1_General_BIN NOT NULL
     )
     WITH (
     LOCATION='customer',
     DATA_SOURCE= external_data_source_name
     );
```

## <a name="see-also"></a>另請參閱

- [CREATE EXTERNAL DATA SOURCE](../../t-sql/statements/create-external-data-source-transact-sql.md)
- [CREATE EXTERNAL FILE FORMAT](../../t-sql/statements/create-external-file-format-transact-sql.md)

::: moniker-end
::: moniker range="=azuresqldb-current"

:::row:::
    :::column:::
        [SQL Server](create-external-table-transact-sql.md?view=sql-server-ver15&preserve-view=true)
    :::column-end:::
    :::column:::
        **_\* SQL Database \*_** &nbsp;
    :::column-end:::
    :::column:::
        [Azure Synapse<br />Analytics](create-external-table-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)
    :::column-end:::
    :::column:::
        [Analytics Platform<br />System (PDW)](create-external-table-transact-sql.md?view=aps-pdw-2016-au7&preserve-view=true)
    :::column-end:::
:::row-end:::

&nbsp;

## <a name="overview-azure-sql-database"></a>概觀：Azure SQL Database

在 Azure SQL Database 中，建立[彈性查詢 (預覽階段)](/azure/sql-database/sql-database-elastic-query-overview/) 的外部資料表。


另請參閱 [CREATE EXTERNAL DATA SOURCE](../../t-sql/statements/create-external-data-source-transact-sql.md)。

## <a name="syntax"></a>語法

```syntaxsql
-- Create a table for use with elastic query  
CREATE EXTERNAL TABLE { database_name.schema_name.table_name | schema_name.table_name | table_name }
    ( <column_definition> [ ,...n ] )  
    WITH ( <sharded_external_table_options> )  
[;]  

<column_definition> ::=
column_name <data_type>
    [ COLLATE collation_name ]
    [ NULL | NOT NULL ]
  
<sharded_external_table_options> ::=  
        DATA_SOURCE = external_data_source_name,
        SCHEMA_NAME = N'nonescaped_schema_name',  
        OBJECT_NAME = N'nonescaped_object_name',  
        [DISTRIBUTION  = SHARDED(sharding_column_name) | REPLICATED | ROUND_ROBIN]]  
    )  
[;]  
```

## <a name="arguments"></a>引數

*{ database_name.schema_name.table_name | schema_name.table_name | table_name }* 所要建立資料表的名稱，由一到三個部分組成。 針對外部資料表，SQL 只會儲存資料表中繼資料，以及 Azure SQL Database 中所參考檔案或資料夾的基本統計資料。 不會在 Azure SQL Database 中移動或儲存任何實際資料。

> [!IMPORTANT]
> 為了達到最佳效能，如果外部資料來源驅動程式支援三部分名稱，則強烈建議您提供三部分名稱。

\<column_definition> [ ,...*n* ] CREATE EXTERNAL TABLE 支援設定資料行名稱、資料類型、可 NULL 性和定序功能。 您無法在外部資料表上使用 DEFAULT CONSTRAINT。

> [!NOTE]
> Azure SQL Database 外部資料表中的資料行不支援 `Text`、`nText` 和 `XML` 資料類型。

資料行定義 (包括資料類型及資料行數目) 必須符合外部檔案中的資料。 若有不相符的情形，系統在查詢實際資料時將會拒絕檔案資料列。

分區外部資料表選項

針對[彈性查詢](/azure/azure-sql/database/elastic-query-overview)指定外部資料來源 (非 SQL Server 資料來源) 及發佈方法。

DATA_SOURCE DATA_SOURCE 子句定義用於外部資料表的外部資料來源 (分區對應)。 如需範例，請參閱[建立外部資料表](/azure/sql-database/sql-database-elastic-query-horizontal-partitioning#13-create-external-tables)。

SCHEMA_NAME 和 OBJECT_NAME SCHEMA_NAME 和 OBJECT_NAME 子句會將外部資料表定義對應至不同結構描述中的資料表。 如果省略，即會假設遠端物件的結構描述為 "dbo" 並假設其名稱與所定義的外部資料表名稱相同。 如果您的遠端資料表名稱已存在於您要建立外部資料表的資料庫中，這會很有用。 例如，您想要定義外部資料表以取得相應放大的資料層上目錄檢視或 DMV 的彙總檢視。 由於目錄檢視和 DMV 已經存在於本機，所以您無法將其名稱使用於外部資料表定義。 可以改為使用不同的名稱，並使用目錄檢視名稱，或 SCHEMA_NAME 和/或 OBJECT_NAME 子句中 DMV 的名稱。 如需範例，請參閱[建立外部資料表](/azure/sql-database/sql-database-elastic-query-horizontal-partitioning#13-create-external-tables)。

DISTRIBUTION DISTRIBUTION 子句指定用於此資料表的資料散發。 查詢處理器會利用 DISTRIBUTION 子句中提供的資訊來建置最有效率的查詢計劃。

- SHARDED 表示跨資料庫水平分割資料。 用於資料散發的分割索引鍵是 <sharding_column_name> 參數。
- REPLICATED 表示資料表的相同複本存在於每個資料庫上。 您必須負責確保複本在所有資料庫上都相同。
- ROUND_ROBIN 表示使用應用程式相依的散發方法，以水平方式分割資料表。

## <a name="permissions"></a>權限

可存取外部資料表的使用者可以在外部資料來源定義中所提供的認證下，自動取得基礎遠端資料表的存取權。 避免透過外部資料來源認證提高不想提高的權限。 對外部資料表使用「授與」或「撤銷」，就像它是一般的資料表一樣。 一旦您已定義外部資料來源和外部資料表，現在您可以對外部資料表使用完整的 T-SQL。

## <a name="error-handling"></a>錯誤處理

執行 CREATE EXTERNAL TABLE 陳述式時，若嘗試連線失敗，陳述式便會失敗，且不會建立外部資料表。 由於 SQL Database 在查詢失敗之前會多次嘗試連線，因此可能需要一分鐘或更久的時間，命令才會失敗。

## <a name="general-remarks"></a>一般備註

在臨機操作的案例中 (例如 SELECT FROM EXTERNAL TABLE)，SQL Database 會將擷取自外部資料來源的資料列儲存在暫存資料表中。 在查詢完成之後，SQL Database 便會移除並刪除該暫存資料表。 SQL 資料表中不會永久存放資料。

相反地，在匯入案例中 (例如 SELECT INTO FROM EXTERNAL TABLE)，SQL Database 會將擷取自外部資料來源的資料列，以永久資料的形式儲存在 SQL 資料表中。 新的資料表會在查詢執行期間，於 SQL Database 擷取外部資料時建立。

您可以建立許多參考相同或不同外部資料來源的外部資料表。

## <a name="limitations-and-restrictions"></a>限制事項

透過外部資料表存取資料並不會遵守 SQL Server 內的隔離語義。 也就是說，查詢外部不會強加任何鎖定或快照集隔離，因此，如果外部資料來源中的資料變更，則資料傳回可能會變更。  相同的查詢在每次針對外部資料表執行時，都有可能傳回不同的結果。 同樣地，在移動或移除外部資料的情況下，查詢也有可能會失敗。

您可以建立多個參考不同外部資料來源的外部資料表。

外部資料表上僅允許使用下列資料定義語言 (DDL) 陳述式：

- CREATE TABLE 和 DROP TABLE
- CREATE VIEW 和 DROP VIEW

不支援的建構和作業：

- 外部資料表資料行上的 DEFAULT 限制式
- 刪除、插入及更新的資料操作語言 (DML) 作業

只有在查詢中定義的常值述詞可以向下推送到外部資料來源。 這不同於連結的伺服器，而且可以使用存取查詢執行期間決定的述詞，也就是在查詢計劃中搭配巢狀迴圈使用時。 這通常會導致整個外部資料表複製到本機，然後加入。

```sql
  \\ Assuming External.Orders is an external table and Customer is a local table.
  \\ This query  will copy the whole of the external locally as the predicate needed
  \\ to filter isn't known at compile time. Its only known during execution of the query
  
  SELECT Orders.OrderId, Orders.OrderTotal
    FROM External.Orders
   WHERE CustomerId in (SELECT TOP 1 CustomerId
                          FROM Customer
                          WHERE CustomerName = 'MyCompany')
```

使用外部資料表可防止在查詢計劃中使用平行處理原則。

外部資料表會實作為遠端查詢，因此，所傳回的估計資料列數目通常是 1000，根據篩選外部資料表所使用的述詞類型，還有其他規則。 它們是以規則為基礎的估計值，而不是根據外部資料表中的實際資料進行評估。 最佳化工具不會存取遠端資料源來取得更精確的估計值。

### <a name="data-type-limitations"></a>資料類型限制

下列資料類型不能用在 PolyBase 外部資料表中：

- `geography`
- `geometry`
- `hierarchyid`
- `image`
- `text`
- `nText`
- `xml`
- 任何使用者定義的類型

## <a name="locking"></a>鎖定

SCHEMARESOLUTION 物件上的共用鎖定。

## <a name="examples"></a>範例

### <a name="a-create-external-table-for-azure-sql-database"></a>A. 建立 Azure SQL Database 的外部資料表

```sql
CREATE EXTERNAL TABLE [dbo].[CustomerInformation]
( [CustomerID] [int] NOT NULL,
  [CustomerName] [varchar](50) NOT NULL,
  [Company] [varchar](50) NOT NULL)
WITH
( DATA_SOURCE = MyElasticDBQueryDataSrc)
```

## <a name="see-also"></a>另請參閱

- [Azure SQL Database 彈性查詢概觀](/azure/sql-database/sql-database-elastic-query-overview) \(部分機器翻譯\)
- [跨相應放大的雲端資料庫報告](/azure/sql-database/sql-database-elastic-query-horizontal-partitioning)
- [開始使用跨資料庫查詢 (垂直資料分割)](/azure/sql-database/sql-database-elastic-query-getting-started-vertical) \(部分機器翻譯\)

::: moniker-end
::: moniker range="=azure-sqldw-latest"

:::row:::
    :::column:::
        [SQL Server](create-external-table-transact-sql.md?view=sql-server-ver15&preserve-view=true)
    :::column-end:::
    :::column:::
        [SQL Database](create-external-table-transact-sql.md?view=azuresqldb-current&preserve-view=true)
    :::column-end:::
    :::column:::
        **_\* Azure Synapse<br />Analytics \*_** &nbsp;
    :::column-end:::
    :::column:::
        [Analytics Platform<br />System (PDW)](create-external-table-transact-sql.md?view=aps-pdw-2016-au7&preserve-view=true)
    :::column-end:::
:::row-end:::

&nbsp;

## <a name="overview-azure-synapse-analytics"></a>概觀：Azure Synapse Analytics

使用外部資料表來：

- 搭配 [!INCLUDE[tsql](../../includes/tsql-md.md)] 查詢 Hadoop 或 Azure Blob 儲存體資料。
- 從 Hadoop 或 Azure Blob 儲存體匯入資料並儲存。
- 從 Azure Data Lake Store 匯入資料並儲存。

另請參閱 [CREATE EXTERNAL DATA SOURCE](../../t-sql/statements/create-external-data-source-transact-sql.md) 及 [DROP EXTERNAL TABLE](../../t-sql/statements/drop-external-table-transact-sql.md)。  

## <a name="syntax"></a>語法

### [[!INCLUDE[sss-dedicated-pool-md.md](../../includes/sss-dedicated-pool-md.md)]](#tab/dedicated)
```syntaxsql
CREATE EXTERNAL TABLE { database_name.schema_name.table_name | schema_name.table_name | table_name }
    ( <column_definition> [ ,...n ] )  
    WITH (
        LOCATION = 'hdfs_folder_or_filepath',  
        DATA_SOURCE = external_data_source_name,  
        FILE_FORMAT = external_file_format_name  
        [ , <reject_options> [ ,...n ] ]  
    )  
[;]  

<column_definition> ::=
column_name <data_type>
    [ COLLATE collation_name ]
    [ NULL | NOT NULL ]
  
<reject_options> ::=  
{  
    | REJECT_TYPE = value | percentage,  
    | REJECT_VALUE = reject_value,  
    | REJECT_SAMPLE_VALUE = reject_sample_value,
    | REJECTED_ROW_LOCATION = '/REJECT_Directory'
}  
```
### [[!INCLUDE[sssod-md.md](../../includes/sssod-md.md)]](#tab/serverless)
```syntaxsql
CREATE EXTERNAL TABLE { database_name.schema_name.table_name | schema_name.table_name | table_name } 
    ( <column_definition> [ ,...n ] )   
    WITH ( 
        LOCATION = 'folder_or_filepath',   
        DATA_SOURCE = external_data_source_name,   
        FILE_FORMAT = external_file_format_name   
    )   
[;]   
<column_definition> ::= 
column_name <data_type> 
    [ COLLATE collation_name ] 
```
---

## <a name="arguments"></a>引數

*{ database_name.schema_name.table_name | schema_name.table_name | table_name }* 所要建立資料表的名稱，由一到三個部分組成。 針對外部資料表，只有資料表中繼資料，以及 Azure Data Lake、Hadoop 或 Azure Blob 儲存體中所參考檔案或資料夾的基本統計資料。 建立外部資料表時，不會移動或儲存實際資料。

> [!IMPORTANT]
> 為了達到最佳效能，如果外部資料來源驅動程式支援三部分名稱，則強烈建議您提供三部分名稱。

\<column_definition> [ ,...*n* ] CREATE EXTERNAL TABLE 支援設定資料行名稱、資料類型、可 NULL 性和定序功能。 您無法在外部資料表上使用 DEFAULT CONSTRAINT。

> [!NOTE]
> Azure SQL 資料倉儲外部資料表中的資料行不支援 `Text`、`nText` 和 `XML` 資料類型。

資料行定義 (包括資料類型及資料行數目) 必須符合外部檔案中的資料。 若有不相符的情形，系統在查詢實際資料時將會拒絕檔案資料列。

LOCATION = '*folder_or_filepath*' 指定 Azure Data Lake、Hadoop 或 Azure Blob 儲存體中實際資料的資料夾或檔案路徑及檔案名稱。 位置會從根資料夾開始。 根資料夾是在外部資料來源中指定的資料位置。 [CREATE EXTERNAL TABLE AS SELECT](create-external-table-as-select-transact-sql.md) 陳述式會建立路徑及資料夾 (若不存在的話)。 `CREATE EXTERNAL TABLE` 則不會建立路徑和資料夾。

若您將 LOCATION 指定為資料夾，會從外部資料表中選取的 PolyBase 查詢，將會從該資料夾及其所有子資料夾中擷取檔案。 PolyBase 和 Hadoop 相同，並不會傳回隱藏的資料夾。 它也不會傳回檔案名稱是以底線 (_) 或句號 (.) 開始的檔案。

在此範例中，若 LOCATION='/webdata/'，PolyBase 查詢將會傳回來自 mydata.txt 和 mydata2.txt 的資料列。 它不會傳回 mydata3.txt，因為它是隱藏資料夾的子資料夾。 它不會傳回 _hidden.txt，因為它是隱藏的檔案。

![外部資料表的遞迴資料](../../t-sql/statements/media/aps-polybase-folder-traversal.png "外部資料表的遞迴資料")

若要變更預設設定並僅從根資料夾讀取，請在 core-site.xml 設定檔中將 \<polybase.recursive.traversal> 屬性設為 'false'。 此檔案位於 `<SqlBinRoot>\PolyBase\Hadoop\Conf with SqlBinRoot the bin root of SQl Server`。 例如： `C:\\Program Files\\Microsoft SQL Server\\MSSQL13.XD14\\MSSQL\\Binn` 。

DATA_SOURCE = *external_data_source_name* 指定包含外部資料位置的外部資料來源名稱。 此位置位於 Azure Data Lake 中。 若要建立外部資料來源，請使用 [CREATE EXTERNAL DATA SOURCE](../../t-sql/statements/create-external-data-source-transact-sql.md)。

FILE_FORMAT = *external_file_format_name* 指定儲存外部資料檔案類型和壓縮方法的外部檔案格式物件名稱。 若要建立外部檔案格式，請使用 [CREATE EXTERNAL FILE FORMAT](../../t-sql/statements/create-external-file-format-transact-sql.md)。

拒絕選項：您可以指定拒絕參數，決定 PolyBase 處理從外部資料來源所擷取「已變更」記錄的方式。 若資料記錄的實際資料類型或資料行數目，與外部資料表的資料行定義不相符，該資料記錄就會被系統視為「已修改」。

當您不指定或變更拒絕值時，PolyBase 就會使用預設值。 拒絕參數的相關資訊會在您搭配 CREATE EXTERNAL TABLE 陳述式建立外部資料表時，以額外中繼資料的形式儲存。 當未來有 SELECT 陳述式或 SELECT INTO SELECT 陳述式從外部資料表中選取資料時，PolyBase 將會使用拒絕選項來判斷在實際的查詢失敗之前，可以拒絕的資料列數目或百分比。 查詢將會傳回 (部分) 結果，直到超過拒絕閾值為止。 接著它便會失敗並顯示適當的錯誤訊息。

REJECT_TYPE = **value** | percentage 指明要將 REJECT_VALUE 選項指定為常值或百分比。

value 表示 REJECT_VALUE 是常值，不是百分比。 PolyBase 查詢會在被拒絕資料列的數目超過 *reject_value* 時失敗。

例如，若 REJECT_VALUE = 5 且 REJECT_TYPE = value，PolyBase SELECT 查詢將會在系統拒絕五個資料列之後失敗。

percentage 表示 REJECT_VALUE 是百分比，不是常值。 PolyBase 查詢會在被拒絕資料列的 *百分比* 超過 *reject_value* 時失敗。 失敗的資料列百分比會每隔一段時間就計算一次。

REJECT_VALUE = *reject_value* 指定在查詢失敗前可拒絕的資料列數目或百分比。

針對 REJECT_TYPE = value，*reject_value* 必須為介於 0 和 2,147,483,647 的整數。

針對 REJECT_TYPE = percentage，*reject_value* 必須為介於 0 和 100 的浮點數。

REJECT_SAMPLE_VALUE = *reject_sample_value* 在您指定 REJECT_TYPE = percentage 時，此屬性是必要項目。 它會決定在 PolyBase 重新計算被拒絕資料列的百分比之前，應嘗試擷取的資料列數目。

*reject_sample_value* 參數必須是介於 0 和 2,147,483,647 的整數。

例如，如果 REJECT_SAMPLE_VALUE = 1000，PolyBase 將會在已嘗試從外部資料檔案匯入 1000 個資料列之後，計算失敗的資料列百分比。 如果失敗的資料列百分比小於 *reject_value*，PolyBase 就會嘗試擷取另外 1000 個資料列。 它會在嘗試匯入每個額外的 1000 個資料列之後，持續重新計算失敗的資料列百分比。

> [!NOTE]
> 由於 PolyBase 會不時計算失敗的資料列百分比，因此實際的失敗資料列百分比可能超出 *reject_value*。

範例：

此範例說明三個 REJECT 選項彼此如何互動。 例如，如果 REJECT_TYPE = percentage、REJECT_VALUE = 30 且 REJECT_SAMPLE_VALUE = 100，就可能發生下列案例：

- PolyBase 會嘗試擷取前 100 個資料列；其中有 25 個失敗，75 個成功。
- 失敗資料列的百分比會計算為 25%，低於拒絕值 30%。 因此，PolyBase 將會繼續從外部資料來源擷取資料。
- PolyBase 會嘗試載入接下來的 100 個資料列；這次有 25 個資料列成功，75 個資料列失敗。
- 失敗資料列的百分比在重新計算後為 50%。 失敗資料列的百分比已超出 30% 的拒絕值。
- PolyBase 查詢在嘗試傳回前 200 個資料列後，會因被拒絕的資料列達 50% 而失敗。 請注意，相符的資料列會在 PolyBase 查詢偵測到超出拒絕閾值之前傳回。

REJECTED_ROW_LOCATION = *Directory Location*

指定外部資料來源中，已拒絕資料列和相應錯誤檔案應寫入的目錄。
若指定的路徑不存在，PolyBase 會為您建立一個目錄。 會建立名稱為 "\_rejectedrows" 的子目錄。 "\_" 字元可確保該目錄從其他資料處理逸出，除非已明確在位置參數中指名。 在此目錄中，會有一個根據載入提交時間建立的資料夾，格式為 YearMonthDay -HourMinuteSecond (例如 20180330-173205)。 在此資料中寫入了兩種類型的檔案，分別是 _reason 檔案與資料檔案。

原因檔案與資料檔案均具有與 CTAS 陳述式相關的 queryID。 因為資料與原因檔案在不同的檔案中，所以對應的檔案會具有相符尾碼。

## <a name="permissions"></a>權限

需要下列使用者權限：

- **CREATE TABLE**
- **ALTER ANY SCHEMA**
- **ALTER ANY EXTERNAL DATA SOURCE**
- **ALTER ANY EXTERNAL FILE FORMAT**

> [!NOTE]
> 只有建立主要 MASTER KEY、DATABASE SCOPED CREDENTIAL 和 EXTERNAL DATA SOURCE 時，才需要 CONTROL DATABASE 權限

請注意，建立外部資料來源的登入，必須具有讀取和寫入位於 Hadoop 或 Azure Blob 儲存體上之外部資料來源的權限。

> [!IMPORTANT]
> ALTER ANY EXTERNAL DATA SOURCE 權限可授與任何主體建立及修改任何外部資料來源物件的能力，因此也能讓主體存取資料庫上的所有資料庫範圍認證。 必須將此權限視為具高度權限，因此必須僅授與系統中受信任的主體。

## <a name="error-handling"></a>錯誤處理

執行 CREATE EXTERNAL TABLE 陳述式的同時，PolyBase 會嘗試連線至外部資料來源。 如果連線的嘗試失敗，陳述式就會失敗，且不會建立外部資料表。 由於 PolyBase 在查詢失敗之前會多次嘗試連線，因此可能需要一分鐘或更久的時間，命令才會失敗。

## <a name="general-remarks"></a>一般備註

在臨機操作的案例中 (例如 SELECT FROM EXTERNAL TABLE)，PolyBase 會將擷取自外部資料來源的資料列，儲存在暫存資料表中。 在查詢完成之後，PolyBase 便會移除並刪除該暫存資料表。 SQL 資料表中不會永久存放資料。

相反地，在匯入案例中 (例如 SELECT INTO FROM EXTERNAL TABLE)，PolyBase 會將擷取自外部資料來源的資料列，以永久資料的形式儲存在 SQL 資料表中。 新的資料表會在查詢執行期間，當 PolyBase 擷取外部資料時建立。

PolyBase 可以將部分的查詢計算推送至 Hadoop 以改善查詢效能。 此動作稱為述詞下推。 若要啟用它，請在 [CREATE EXTERNAL DATA SOURCE](../../t-sql/statements/create-external-data-source-transact-sql.md) 中指定 Hadoop 資源管理員位置選項。

您可以建立許多參考相同或不同外部資料來源的外部資料表。

## <a name="limitations-and-restrictions"></a>限制事項

由於外部資料表的資料未直接受到 Azure Synapse 管理控制，其可以隨時由外部處理序變更或移除。 因此，針對外部資料表的查詢結果並不保證具有確定性。 相同的查詢在每次針對外部資料表執行時，都有可能傳回不同的結果。 同樣地，在移動或移除外部資料的情況下，查詢也有可能會失敗。

您可以建立多個參考不同外部資料來源的外部資料表。

外部資料表上僅允許使用下列資料定義語言 (DDL) 陳述式：

- CREATE TABLE 和 DROP TABLE
- CREATE STATISTICS 和 DROP STATISTICS
- CREATE VIEW 和 DROP VIEW

不支援的建構和作業：

- 外部資料表資料行上的 DEFAULT 限制式
- 刪除、插入及更新的資料操作語言 (DML) 作業

### <a name="query-limitations"></a>查詢限制

建議每個資料夾包含的檔案數不要超過 3 萬個。 參考太多檔案時，可能會發生 Java 虛擬機器 (JVM) 記憶體不足的例外狀況，或效能可能會變差。

### <a name="table-width-limitations"></a>資料表寬度限制

Azure 資料倉儲中 PolyBase 具有 1 MB 的資料列寬度限制，這是以依資料表定義的單一有效資料列大小上限為基礎。 若資料行結構描述的總和超過 1 MB，PolyBase 便無法查詢資料。

### <a name="data-type-limitations"></a>資料類型限制

下列資料類型不能用在 PolyBase 外部資料表中：

- `geography`
- `geometry`
- `hierarchyid`
- `image`
- `text`
- `nText`
- `xml`
- 任何使用者定義的類型

## <a name="locking"></a>鎖定

SCHEMARESOLUTION 物件上的共用鎖定。

## <a name="examples"></a>範例

### <a name="a-importing-data-from-adls-gen-2-into-azure-ssdw"></a>A. 從 ADLS Gen 2 將資料匯入 Azure [!INCLUDE[ssDW](../../includes/ssdw-md.md)]。 

以 Gen ADLS Gen 1 為例，請參閱[建立外部資料來源](create-external-data-source-transact-sql.md)。

```sql

-- These values come from your Azure Active Directory Application used to authenticate to ADLS Gen 2. 
CREATE DATABASE SCOPED CREDENTIAL ADLUser
WITH IDENTITY = '<clientID>@\<OAuth2.0TokenEndPoint>',
SECRET = '<KEY>' ;

CREATE EXTERNAL DATA SOURCE AzureDataLakeStore
WITH (TYPE = HADOOP,
      LOCATION = 'abfss://data@pbasetr.azuredatalakestore.net'
)

CREATE EXTERNAL FILE FORMAT TextFileFormat
WITH
(
    FORMAT_TYPE = DELIMITEDTEXT
    , FORMAT_OPTIONS ( FIELD_TERMINATOR = '|'
       , STRING_DELIMITER = ''
      , DATE_FORMAT = 'yyyy-MM-dd HH:mm:ss.fff'
      , USE_TYPE_DEFAULT = FALSE
      )
)

CREATE EXTERNAL TABLE [dbo].[DimProductexternal]
( [ProductKey] [int] NOT NULL,
  [ProductLabel] nvarchar NULL,
  [ProductName] nvarchar NULL )
WITH
(
    LOCATION='/DimProduct/' ,
    DATA_SOURCE = AzureDataLakeStore ,
    FILE_FORMAT = TextFileFormat ,
    REJECT_TYPE = VALUE ,
    REJECT_VALUE = 0
) ;

CREATE TABLE [dbo].[DimProduct]
WITH (DISTRIBUTION = HASH([ProductKey] ) )
AS SELECT * FROM
[dbo].[DimProduct_external] ;
```

## <a name="see-also"></a>另請參閱

- [CREATE EXTERNAL DATA SOURCE](../../t-sql/statements/create-external-data-source-transact-sql.md)
- [CREATE EXTERNAL FILE FORMAT](../../t-sql/statements/create-external-file-format-transact-sql.md)
- [CREATE EXTERNAL TABLE AS SELECT](../../t-sql/statements/create-external-table-as-select-transact-sql.md)
- [CREATE TABLE AS SELECT &#40;Azure Synapse Analytics&#41;](../../t-sql/statements/create-table-as-select-azure-sql-data-warehouse.md)

::: moniker-end
::: moniker range=">=aps-pdw-2016"

:::row:::
    :::column:::
        [SQL Server](create-external-table-transact-sql.md?view=sql-server-ver15&preserve-view=true)
    :::column-end:::
    :::column:::
        [SQL Database](create-external-table-transact-sql.md?view=azuresqldb-current&preserve-view=true)
    :::column-end:::
    :::column:::
        [Azure Synapse<br />Analytics](create-external-table-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)
    :::column-end:::
    :::column:::
        **_\* Analytics<br />Platform System (PDW) \*_** &nbsp;
    :::column-end:::
:::row-end:::

&nbsp;

## <a name="overview-analytics-platform-system"></a>概觀：分析平台系統

使用外部資料表來：

- 搭配 [!INCLUDE[tsql](../../includes/tsql-md.md)] 查詢 Hadoop 或 Azure Blob 儲存體資料。
- 從 Hadoop 或 Azure Blob 儲存體將資料匯入並儲存至 Analytics Platform System。

另請參閱 [CREATE EXTERNAL DATA SOURCE](../../t-sql/statements/create-external-data-source-transact-sql.md) 及 [DROP EXTERNAL TABLE](../../t-sql/statements/drop-external-table-transact-sql.md)。

## <a name="syntax"></a>語法

```syntaxsql
CREATE EXTERNAL TABLE { database_name.schema_name.table_name | schema_name.table_name | table_name }
    ( <column_definition> [ ,...n ] )  
    WITH (
        LOCATION = 'hdfs_folder_or_filepath',  
        DATA_SOURCE = external_data_source_name,  
        FILE_FORMAT = external_file_format_name  
        [ , <reject_options> [ ,...n ] ]  
    )  
[;]  

<column_definition> ::=
column_name <data_type>
    [ COLLATE collation_name ]
    [ NULL | NOT NULL ]

<reject_options> ::=  
{  
    | REJECT_TYPE = value | percentage,  
    | REJECT_VALUE = reject_value,  
    | REJECT_SAMPLE_VALUE = reject_sample_value,
  
}  
```

## <a name="arguments"></a>引數

*{ database_name.schema_name.table_name | schema_name.table_name | table_name }* 所要建立資料表的名稱，由一到三個部分組成。 針對外部資料表，Analytics Platform System 只會儲存資料表中繼資料，以及 Hadoop 或 Azure Blob 儲存體中所參考檔案或資料夾的基本統計資料。 不會在 Analytics Platform System 中移動或儲存任何實際資料。

> [!IMPORTANT]
> 為了達到最佳效能，如果外部資料來源驅動程式支援三部分名稱，則強烈建議您提供三部分名稱。

\<column_definition> [ ,...*n* ] CREATE EXTERNAL TABLE 支援設定資料行名稱、資料類型、可 NULL 性和定序功能。 您無法在外部資料表上使用 DEFAULT CONSTRAINT。

資料行定義 (包括資料類型及資料行數目) 必須符合外部檔案中的資料。 若有不相符的情形，系統在查詢實際資料時將會拒絕檔案資料列。

LOCATION = '*folder_or_filepath*' 指定 Hadoop 或 Azure Blob 儲存體中實際資料的資料夾或檔案路徑，以及檔案名稱。 位置會從根資料夾開始。 根資料夾是在外部資料來源中指定的資料位置。

在 Analytics Platform System 中，[CREATE EXTERNAL TABLE AS SELECT](create-external-table-as-select-transact-sql.md) 陳述式會建立路徑和資料夾 (若不存在的話)。 `CREATE EXTERNAL TABLE` 則不會建立路徑和資料夾。

若您將 LOCATION 指定為資料夾，會從外部資料表中選取的 PolyBase 查詢，將會從該資料夾及其所有子資料夾中擷取檔案。 PolyBase 和 Hadoop 相同，並不會傳回隱藏的資料夾。 它也不會傳回檔案名稱是以底線 (_) 或句號 (.) 開始的檔案。

在此範例中，若 LOCATION='/webdata/'，PolyBase 查詢將會傳回來自 mydata.txt 和 mydata2.txt 的資料列。 它不會傳回 mydata3.txt，因為它是隱藏資料夾的子資料夾。 它不會傳回 _hidden.txt，因為它是隱藏的檔案。

![外部資料表的遞迴資料](../../t-sql/statements/media/aps-polybase-folder-traversal.png "外部資料表的遞迴資料")

若要變更預設設定並僅從根資料夾讀取，請在 core-site.xml 設定檔中將 \<polybase.recursive.traversal> 屬性設為 'false'。 此檔案位於 `<SqlBinRoot>\PolyBase\Hadoop\Conf with SqlBinRoot the bin root of SQl Server`。 例如： `C:\\Program Files\\Microsoft SQL Server\\MSSQL13.XD14\\MSSQL\\Binn` 。

DATA_SOURCE = *external_data_source_name* 指定包含外部資料位置的外部資料來源名稱。 此位置可為 Hadoop 或 Azure Blob 儲存體。 若要建立外部資料來源，請使用 [CREATE EXTERNAL DATA SOURCE](../../t-sql/statements/create-external-data-source-transact-sql.md)。

FILE_FORMAT = *external_file_format_name* 指定儲存外部資料檔案類型和壓縮方法的外部檔案格式物件名稱。 若要建立外部檔案格式，請使用 [CREATE EXTERNAL FILE FORMAT](../../t-sql/statements/create-external-file-format-transact-sql.md)。

拒絕選項：您可以指定拒絕參數，決定 PolyBase 處理從外部資料來源所擷取「已變更」記錄的方式。 若資料記錄的實際資料類型或資料行數目，與外部資料表的資料行定義不相符，該資料記錄就會被系統視為「已修改」。

當您不指定或變更拒絕值時，PolyBase 就會使用預設值。 拒絕參數的相關資訊會在您搭配 CREATE EXTERNAL TABLE 陳述式建立外部資料表時，以額外中繼資料的形式儲存。 當未來有 SELECT 陳述式或 SELECT INTO SELECT 陳述式從外部資料表中選取資料時，PolyBase 將會使用拒絕選項來判斷在實際的查詢失敗之前，可以拒絕的資料列數目或百分比。 查詢將會傳回 (部分) 結果，直到超過拒絕閾值為止。 接著它便會失敗並顯示適當的錯誤訊息。

REJECT_TYPE = **value** | percentage 指明要將 REJECT_VALUE 選項指定為常值或百分比。

value 表示 REJECT_VALUE 是常值，不是百分比。 PolyBase 查詢會在被拒絕資料列的數目超過 *reject_value* 時失敗。

例如，若 REJECT_VALUE = 5 且 REJECT_TYPE = value，PolyBase SELECT 查詢將會在系統拒絕五個資料列之後失敗。

percentage 表示 REJECT_VALUE 是百分比，不是常值。 PolyBase 查詢會在被拒絕資料列的 *百分比* 超過 *reject_value* 時失敗。 失敗的資料列百分比會每隔一段時間就計算一次。

REJECT_VALUE = *reject_value* 指定在查詢失敗前可拒絕的資料列數目或百分比。

針對 REJECT_TYPE = value，*reject_value* 必須為介於 0 和 2,147,483,647 的整數。

針對 REJECT_TYPE = percentage，*reject_value* 必須為介於 0 和 100 的浮點數。

REJECT_SAMPLE_VALUE = *reject_sample_value* 在您指定 REJECT_TYPE = percentage 時，此屬性是必要項目。 它會決定在 PolyBase 重新計算被拒絕資料列的百分比之前，應嘗試擷取的資料列數目。

*reject_sample_value* 參數必須是介於 0 和 2,147,483,647 的整數。

例如，如果 REJECT_SAMPLE_VALUE = 1000，PolyBase 將會在已嘗試從外部資料檔案匯入 1000 個資料列之後，計算失敗的資料列百分比。 如果失敗的資料列百分比小於 *reject_value*，PolyBase 就會嘗試擷取另外 1000 個資料列。 它會在嘗試匯入每個額外的 1000 個資料列之後，持續重新計算失敗的資料列百分比。

> [!NOTE]
> 由於 PolyBase 會不時計算失敗的資料列百分比，因此實際的失敗資料列百分比可能超出 *reject_value*。

範例：

此範例說明三個 REJECT 選項彼此如何互動。 例如，如果 REJECT_TYPE = percentage、REJECT_VALUE = 30 且 REJECT_SAMPLE_VALUE = 100，就可能發生下列案例：

- PolyBase 會嘗試擷取前 100 個資料列；其中有 25 個失敗，75 個成功。
- 失敗資料列的百分比會計算為 25%，低於拒絕值 30%。 因此，PolyBase 將會繼續從外部資料來源擷取資料。
- PolyBase 會嘗試載入接下來的 100 個資料列；這次有 25 個資料列成功，75 個資料列失敗。
- 失敗資料列的百分比在重新計算後為 50%。 失敗資料列的百分比已超出 30% 的拒絕值。
- PolyBase 查詢在嘗試傳回前 200 個資料列後，會因被拒絕的資料列達 50% 而失敗。 請注意，相符的資料列會在 PolyBase 查詢偵測到超出拒絕閾值之前傳回。

## <a name="permissions"></a>權限

需要下列使用者權限：

- **CREATE TABLE**
- **ALTER ANY SCHEMA**
- **ALTER ANY EXTERNAL DATA SOURCE**
- **ALTER ANY EXTERNAL FILE FORMAT**
- **CONTROL DATABASE**

請注意，建立外部資料來源的登入，必須具有讀取和寫入位於 Hadoop 或 Azure Blob 儲存體上之外部資料來源的權限。

> [!IMPORTANT]
> ALTER ANY EXTERNAL DATA SOURCE 權限可授與任何主體建立及修改任何外部資料來源物件的能力，因此也能讓主體存取資料庫上的所有資料庫範圍認證。 必須將此權限視為具高度權限，因此必須僅授與系統中受信任的主體。

## <a name="error-handling"></a>錯誤處理

執行 CREATE EXTERNAL TABLE 陳述式的同時，PolyBase 會嘗試連線至外部資料來源。 如果連線的嘗試失敗，陳述式就會失敗，且不會建立外部資料表。 由於 PolyBase 在查詢失敗之前會多次嘗試連線，因此可能需要一分鐘或更久的時間，命令才會失敗。

## <a name="general-remarks"></a>一般備註

在臨機操作的案例中 (例如 SELECT FROM EXTERNAL TABLE)，PolyBase 會將擷取自外部資料來源的資料列，儲存在暫存資料表中。 在查詢完成之後，PolyBase 便會移除並刪除該暫存資料表。 SQL 資料表中不會永久存放資料。

相反地，在匯入案例中 (例如 SELECT INTO FROM EXTERNAL TABLE)，PolyBase 會將擷取自外部資料來源的資料列，以永久資料的形式儲存在 SQL 資料表中。 新的資料表會在查詢執行期間，當 PolyBase 擷取外部資料時建立。

PolyBase 可以將部分的查詢計算推送至 Hadoop 以改善查詢效能。 此動作稱為述詞下推。 若要啟用它，請在 [CREATE EXTERNAL DATA SOURCE](../../t-sql/statements/create-external-data-source-transact-sql.md) 中指定 Hadoop 資源管理員位置選項。

您可以建立許多參考相同或不同外部資料來源的外部資料表。

## <a name="limitations-and-restrictions"></a>限制事項

由於外部資料表資料未直接受到該設備的管理控制，因此其可以隨時由外部處理序變更或移除。 因此，針對外部資料表的查詢結果並不保證具有確定性。 相同的查詢在每次針對外部資料表執行時，都有可能傳回不同的結果。 同樣地，在移動或移除外部資料的情況下，查詢也有可能會失敗。

您可以建立多個參考不同外部資料來源的外部資料表。 如果您同時針對不同的 Hadoop 資料來源執行查詢，則每個 Hadoop 來源都必須使用相同的「Hadoop 連線能力」伺服器組態設定。 例如，您不能同時針對 Cloudera Hadoop 叢集和 Hortonworks Hadoop 叢集執行查詢，因為這些叢集是使用不同的組態設定。 如需組態設定和支援的組合，請參閱 [PolyBase 連線能力設定](../../database-engine/configure-windows/polybase-connectivity-configuration-transact-sql.md)。

外部資料表上僅允許使用下列資料定義語言 (DDL) 陳述式：

- CREATE TABLE 和 DROP TABLE
- CREATE STATISTICS 和 DROP STATISTICS
- CREATE VIEW 和 DROP VIEW

不支援的建構和作業：

- 外部資料表資料行上的 DEFAULT 限制式
- 刪除、插入及更新的資料操作語言 (DML) 作業

### <a name="query-limitations"></a>查詢限制

執行 32 個並行的 PolyBase 查詢時，PolyBase 每個資料夾可取用的檔案數目上限為 33000 個檔案。 這個上限數同時包含了每個 HDFS 資料夾中的檔案和子資料夾。 如果並行程度小於 32，使用者就可以針對 HDFS 中內含超過 33000 個檔案的資料夾執行 PolyBase 查詢。 我們建議您使用簡短的外部檔案路徑，且所使用的每個 HDFS 資料夾檔案數目不要超過 30000 個檔案。 參考太多檔案時，可能會發生 Java 虛擬機器 (JVM) 記憶體不足的例外狀況。

### <a name="table-width-limitations"></a>資料表寬度限制

SQL Server 2016 中的 PolyBase 具有 32 KB 的資料列寬度限制，這是以依資料表定義的單一有效資料列大小上限為基礎。 若資料行結構描述的總和超過 32 KB，PolyBase 將無法查詢資料。

在 Azure Synapse Analytics 中，這項限制已提高至 1 MB。

### <a name="data-type-limitations"></a>資料類型限制

下列資料類型不能用在 PolyBase 外部資料表中：

- `geography`
- `geometry`
- `hierarchyid`
- `image`
- `text`
- `nText`
- `xml`
- 任何使用者定義的類型

## <a name="locking"></a>鎖定

SCHEMARESOLUTION 物件上的共用鎖定。

## <a name="security"></a>安全性

外部資料表的資料檔案會儲存在 Hadoop 或 Azure Blob 儲存體中。 這些資料檔案是由您自己的處理程序所建立及管理。 因此您應負責管理外部資料的安全性。

## <a name="examples"></a>範例

### <a name="a-join-hdfs-data-with-analytics-platform-system-data"></a>A. 聯結 HDFS 資料及 Analytics Platform System 資料

```sql
SELECT cs.user_ip FROM ClickStream cs
JOIN User u ON cs.user_ip = u.user_ip
WHERE cs.url = 'www.microsoft.com'
;
```

### <a name="b-import-row-data-from-hdfs-into-a-distributed-analytics-platform-system-table"></a>B. 從 HDFS 將資料列資料匯入至分散式 Analytics Platform System 資料表

```sql
CREATE TABLE ClickStream_PDW
WITH ( DISTRIBUTION = HASH (url) )
AS SELECT url, event_date, user_ip FROM ClickStream
;
```

### <a name="c-import-row-data-from-hdfs-into-a-replicated-analytics-platform-system-table"></a>C. 從 HDFS 將資料列資料匯入至複寫 Analytics Platform System 資料表

```sql
CREATE TABLE ClickStream_PDW
WITH ( DISTRIBUTION = REPLICATE )
AS SELECT url, event_date, user_ip
FROM ClickStream
;
```

## <a name="see-also"></a>另請參閱

- [CREATE EXTERNAL DATA SOURCE](../../t-sql/statements/create-external-data-source-transact-sql.md)
- [CREATE EXTERNAL FILE FORMAT](../../t-sql/statements/create-external-file-format-transact-sql.md)
- [CREATE EXTERNAL TABLE AS SELECT](../../t-sql/statements/create-external-table-as-select-transact-sql.md)
- [CREATE TABLE AS SELECT &#40;Azure Synapse Analytics&#41;](../../t-sql/statements/create-table-as-select-azure-sql-data-warehouse.md)

::: moniker-end
