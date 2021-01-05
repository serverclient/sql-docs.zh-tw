---
description: CREATE EXTERNAL DATA SOURCE (Transact-SQL)
title: CREATE EXTERNAL DATA SOURCE (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 02/26/2020
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- CREATE EXTERNAL DATA SOURCE
- CREATE_EXTERNAL_DATA_SOURCE
dev_langs:
- TSQL
helpviewer_keywords:
- External
- External, data source
- PolyBase, create data source
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 5a4bec65f3a89d789801bee44ce767718b2ab5ea
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97638474"
---
# <a name="create-external-data-source-transact-sql"></a>CREATE EXTERNAL DATA SOURCE (Transact-SQL)

建立使用 SQL Server、SQL Database、Azure Synapse Analytics 或 Analytics Platform System (平行處理資料倉儲或 PDW) 進行查詢的外部資料來源。

本文提供適用於您所選擇之 SQL 產品的語法、引數、備註、權限和範例。

如需語法慣例的詳細資訊，請參閱 [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)。

[!INCLUDE[select-product](../../includes/select-product.md)]

::: moniker range=">=sql-server-2016||>=sql-server-linux-2017"

:::row:::
    :::column:::
        **_\* SQL Server \*_** &nbsp;
    :::column-end:::
    :::column:::
        [SQL Database](create-external-data-source-transact-sql.md?view=azuresqldb-current&preserve-view=true)
    :::column-end:::
    :::column:::
        [Azure Synapse<br />Analytics](create-external-data-source-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)
    :::column-end:::
    :::column:::
        [Analytics Platform<br />System (PDW)](create-external-data-source-transact-sql.md?view=aps-pdw-2016-au7&preserve-view=true)
    :::column-end:::
:::row-end:::

&nbsp;

## <a name="overview-sql-server"></a>概觀：SQL Server

建立 PolyBase 查詢的外部資料來源。 外部資料來源會用來建立連線能力，並支援這些主要使用案例：

- 使用 [PolyBase][intro_pb] 來執行資料虛擬化和資料載入
- 使用 `BULK INSERT` 或 `OPENROWSET` 的大量載入作業

**適用於**：從 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 開始

## <a name="syntax"></a>語法

```syntaxsql
CREATE EXTERNAL DATA SOURCE <data_source_name>
WITH
  ( [ LOCATION = '<prefix>://<path>[:<port>]' ]
    [ [ , ] CONNECTION_OPTIONS = '<name_value_pairs>']
    [ [ , ] CREDENTIAL = <credential_name> ]
    [ [ , ] PUSHDOWN = { ON | OFF } ]
    [ [ , ] TYPE = { HADOOP | BLOB_STORAGE } ]
    [ [ , ] RESOURCE_MANAGER_LOCATION = '<resource_manager>[:<port>]' )
[ ; ]
```

## <a name="arguments"></a>引數

### <a name="data_source_name"></a>data_source_name

指定資料來源的使用者定義名稱。 這個名稱在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 資料庫內必須是唯一的。

### <a name="location--prefixpathport"></a>LOCATION = *`'<prefix>://<path[:port]>'`*

提供連線通訊協定和路徑給外部資料來源。

| 外部資料來源    | 位置前置詞 | 位置路徑                                         | 支援的位置 (依產品/服務) |
| ----------------------- | --------------- | ----------------------------------------------------- | ---------------------------------------- |
| Cloudera 或 Hortonworks | `hdfs`          | `<Namenode>[:port]`                                   | 從 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 開始                       |
| Azure 儲存體帳戶 (V2) | `wasb[s]`       | `<container>@<storage_account>.blob.core.windows.net` | 從 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 開始         **不** 支援階層命名空間 |
| [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]              | `sqlserver`     | `<server_name>[\<instance_name>][:port]`              | 從 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 開始                       |
| Oracle                  | `oracle`        | `<server_name>[:port]`                                | 從 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 開始                       |
| Teradata                | `teradata`      | `<server_name>[:port]`                                | 從 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 開始                       |
| MongoDB 或 CosmosDB     | `mongodb`       | `<server_name>[:port]`                                | 從 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 開始                       |
| ODBC                    | `odbc`          | `<server_name>[:port]`                                | 從 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 開始 - 僅限 Windows        |
| 大量作業         | `https`         | `<storage_account>.blob.core.windows.net/<container>` | 從 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 開始                        |
| Edge 中樞         | `edgehub`         | 不適用 | EdgeHub 對於 [Azure SQL Edge](/azure/azure-sql-edge/overview/) 執行個體一律是本機的。 因此，不需要指定路徑或連接埠值。 僅適用於 Azure SQL Edge。                      |
| Kafka        | `kafka`         | `<Kafka IP Address>[:port]` | 僅適用於 Azure SQL Edge。                      |

位置路徑：

- `<`Namenode`>` = Hadoop 叢集中 `Namenode` 的電腦名稱、名稱服務 URI 或 IP 位置。 PolyBase 必須解析 Hadoop 叢集所使用的任何 DNS 名稱。 <!-- For highly available Hadoop configurations, provide the Nameservice ID as the `LOCATION`. -->
- `port` = 外部資料來源正在接聽的連接埠。 在 Hadoop 中，此連接埠可使用 `fs.defaultFS` 設定參數來尋找。 預設值為 8020。
- `<container>` = 保留資料的儲存體帳戶容器。 根容器是唯讀的，因此無法將資料寫回至容器。
- `<storage_account>` = Azure 資源的儲存體帳戶名稱。
- `<server_name>` = 主機名稱。
- `<instance_name>` = SQL Server 具名執行個體的名稱。 在目標執行個體上執行 SQL Server Browser 服務時使用。

設定位置時的其他注意事項和指引：

- 建立物件時，[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 不會驗證外部資料來源是否存在。 若要驗證，請使用外部資料來源建立外部資料表。
- 查詢 Hadoop 時，請針對所有資料表使用相同的外部資料來源，以確保查詢語意一致。
- 您可以使用 `sqlserver` 位置前置詞，將 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 連接到另一個 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]、[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 或 Azure Synapse Analytics。
- 透過 `ODBC` 連線時，請指定 `Driver={<Name of Driver>}`。
- 針對存取 Azure 儲存體帳戶，`wasbs` 是選擇性的，但由於資料會使用安全的 TLS/SSL 連線傳送，因此建議使用。
- 存取 Azure 儲存體帳戶時，不支援 `abfs` 或 `abfss` API。
- 不支援 Azure 儲存體帳戶 (V2) 的 [階層式命名空間] 選項。 請確認此選項保持 **停用**。
- 為確保在 Hadoop `Namenode` 容錯移轉期間能成功進行 PolyBase 查詢，請考慮使用虛擬 IP 位址作為 Hadoop 叢集的 `Namenode`。 若未這樣做，請執行 [ALTER EXTERNAL DATA SOURCE][alter_eds] 命令以指向新位置。

### <a name="connection_options--key_value_pair"></a>CONNECTION_OPTIONS = *key_value_pair*

透過 `ODBC` 連線到外部資料來源時，請指定其他選項。 若要使用多個連線選項，請以分號進行分隔。


至少需要驅動程式的名稱，但還有其他選項 (例如 `APP='<your_application_name>'` 或 `ApplicationIntent= ReadOnly|ReadWrite`) 若加以設定也會很有用，並可協助進行疑難排解。

如需允許的 [CONNECTION_OPTIONS][connection_options] 清單，請參閱 `ODBC` 產品文件

### <a name="pushdown--on--off"></a>PUSHDOWN = *ON | OFF*

指出是否可以將計算下推到外部資料來源。 預設為開啟。

在外部資料來源層級連線到 SQL Server、Oracle、Teradata、MongoDB 或 ODBC 時，才支援 `PUSHDOWN`。

透過[提示][hint_pb]可啟用或停用查詢層級的下推。

### <a name="credential--credential_name"></a>CREDENTIAL = *credential_name*

指定資料庫範圍的認證，以便向外部資料來源進行驗證。

建立認證時的其他注意事項和指引：

- 只有在資料受到保護時才需要 `CREDENTIAL`。 允許匿名存取的資料集不需要 `CREDENTIAL`。
- 當 `TYPE` = `BLOB_STORAGE` 時，必須使用 `SHARED ACCESS SIGNATURE` 作為身分識別來建立認證。 此外，SAS 權杖應設定如下：
  - 設定為祕密時，請排除前置 `?`
  - 至少擁有應載入檔案的讀取權限 (例如 `srt=o&sp=r`)
  - 使用有效的到期時間 (所有日期都是 UTC 時間)。

如需搭配 `SHARED ACCESS SIGNATURE` 和 `TYPE` = `BLOB_STORAGE` 使用 `CREDENTIAL` 的範例，請參閱[建立外部資料來源以執行大量作業並將資料從 Azure 儲存體擷取到 SQL Database](#i-create-an-external-data-source-for-bulk-operations-retrieving-data-from-azure-storage)

若要建立資料庫範圍認證，請參閱 [CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)][create_dsc]。

### <a name="type---hadoop--blob_storage-"></a>TYPE = *[ HADOOP | BLOB_STORAGE ]*

指定要設定的外部資料來源類型。 不一定需要此參數。

- 當外部資料來源為 Cloudera、Hortonworks 或 Azure 儲存體帳戶時，請使用 HADOOP。
- 搭配 [!INCLUDE[ssSQLv14_md](../../includes/sssqlv14-md.md)] 使用 [BULK INSERT][bulk_insert] 或 [OPENROWSET][openrowset] 執行來自 Azure 儲存體帳戶的大量作業時，請使用 BLOB_STORAGE。

> [!IMPORTANT]
> 如果使用任何其他外部資料來源，請不要設定 `TYPE`。

如需使用 `TYPE` = `HADOOP` 從 Azure 儲存體帳戶載入資料的範例，請參閱[使用 wasb:// 介面建立存取 Azure 儲存體中資料的外部資料來源](#e-create-external-data-source-to-access-data-in-azure-storage-using-the-wasb-interface) <!--[Create external data source to reference Azure Storage](#e-create-external-data-source-to-reference-azure-storage).-->

### <a name="resource_manager_location--resourcemanager_uriport"></a>RESOURCE_MANAGER_LOCATION = *'ResourceManager_URI[:port]'*

連線到 Hortonworks 或 Cloudera 時，請設定這個選用值。

定義 `RESOURCE_MANAGER_LOCATION` 時，查詢最佳化工具會制訂成本型決策以改善效能。 MapReduce 作業可用於將計算下推到 Hadoop。 指定 `RESOURCE_MANAGER_LOCATION` 可大幅降低 Hadoop 與 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 之間的資料傳輸量，這可改善查詢效能。

若未指定 Resource Manager，系統會針對 PolyBase 查詢停用將計算推送到 Hadoop。

若未指定連接埠，系統會使用 'hadoop connectivity' 設定的目前設定選擇預設值。

| Hadoop 連線能力 | 預設資源管理員連接埠 |
| ------------------- | ----------------------------- |
| 1                   | 50300                         |
| 2                   | 50300                         |
| 3                   | 8021                          |
| 4                   | 8032                          |
| 5                   | 8050                          |
| 6                   | 8032                          |
| 7                   | 8050                          |

如需支援 Hadoop 版本的完整清單，請參閱 [PolyBase 連線設定 (Transact-SQL)][connectivity_pb]。

> [!IMPORTANT]  
> 當您建立外部資料來源時，不會驗證 RESOURCE_MANAGER_LOCATION 值。 輸入不正確的值可能會導致每次嘗試下推就在執行時間發生查詢失敗，因為無法解析所提供的值。

[在已啟用下推的情況下，建立參考 Hadoop 的外部資料來源](#c-create-external-data-source-to-reference-hadoop-with-push-down-enabled)提供具體範例及進一步指引。

## <a name="permissions"></a>權限

需要有 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 資料庫的 `CONTROL` 權限。

## <a name="locking"></a>鎖定

對 `EXTERNAL DATA SOURCE` 物件採取共用鎖定。

## <a name="security"></a>安全性

PolyBase 支援大多數外部資料來源的 Proxy 驗證。 建立資料庫範圍認證以建立 Proxy 帳戶。

當您連線到 SQL Server 巨量資料叢集中的儲存體或資料集區時，系統會將使用者的認證傳遞到後端系統。 在資料集區本身中建立登入以啟用傳遞驗證。

目前不支援 `HADOOP` 類型的 SAS 權杖。 只支援搭配儲存體帳戶存取金鑰使用。 嘗試使用 `HADOOP` 類型及 SAS 認證來建立外部資料來源時會失敗，並發生下列錯誤：

`Msg 105019, Level 16, State 1 - EXTERNAL TABLE access failed due to internal error: 'Java exception raised on call to HdfsBridge_Connect. Java exception message: Parameters provided to connect to the Azure storage account are not valid.: Error [Parameters provided to connect to the Azure storage account are not valid.] occurred while accessing external file.'`

## <a name="examples-starting-with-sssql15"></a>範例 (自 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 起)

> [!IMPORTANT]
> 如需如何安裝及啟用 Polybase 的相關資訊，請參閱[在 Windows 上安裝 PolyBase](../../relational-databases/polybase/polybase-installation.md)

### <a name="a-create-external-data-source-in-sql-server-2019-to-reference-oracle"></a>A. 在 SQL Server 2019 中建立參考 Oracle 的外部資料來源

若要建立參考 Oracle 的外部資料來源，請確保您擁有資料庫範圍認證。 您也可以選擇啟用或停用對此資料來源的下推計算。

```sql
-- Create a database master key if one does not already exist, using your own password. This key is used to encrypt the credential secret in next step.
CREATE MASTER KEY ENCRYPTION BY PASSWORD = '!MyC0mpl3xP@ssw0rd!' ;

-- Create a database scoped credential with Azure storage account key as the secret.
CREATE DATABASE SCOPED CREDENTIAL OracleProxyAccount
WITH
     IDENTITY = 'oracle_username',
     SECRET = 'oracle_password' ;

CREATE EXTERNAL DATA SOURCE MyOracleServer
WITH
  ( LOCATION = 'oracle://145.145.145.145:1521',
    CREDENTIAL = OracleProxyAccount,
    PUSHDOWN = ON
  ) ;
```

如需 MongoDB 等其他資料來源的範例，請參閱[設定 PolyBase 存取 MongoDB 中的外部資料][mongodb_pb]

### <a name="b-create-external-data-source-to-reference-hadoop"></a>B. 建立參考 Hadoop 的外部資料來源

若要建立參考 Hortonworks 或 Cloudera Hadoop 叢集的外部資料來源，請指定 Hadoop `Namenode` 的電腦名稱或 IP 位址與連接埠。 <!-- Provide the Nameservice ID as the `LOCATION` for highly available configurations. -->

```sql
CREATE EXTERNAL DATA SOURCE MyHadoopCluster
WITH
  ( LOCATION = 'hdfs://10.10.10.10:8050' ,
    TYPE = HADOOP
  ) ;
```

### <a name="c-create-external-data-source-to-reference-hadoop-with-push-down-enabled"></a>C. 在已啟用下推的情況下，建立參考 Hadoop 的外部資料來源

指定 `RESOURCE_MANAGER_LOCATION` 選項，以便對適用於 PolyBase 查詢的 Hadoop 啟用下推計算。 啟用之後，PolyBase 會制訂成本型決策來判斷是否應該將查詢計算推送到 Hadoop。

```sql
CREATE EXTERNAL DATA SOURCE MyHadoopCluster
WITH
  ( LOCATION = 'hdfs://10.10.10.10:8020' ,
    TYPE = HADOOP ,
    RESOURCE_MANAGER_LOCATION = '10.10.10.10:8050'
  ) ;
```

### <a name="d-create-external-data-source-to-reference-kerberos-secured-hadoop"></a>D. 建立參考由 Kerberos 保護之 Hadoop 的外部資料來源

若要確認 Hadoop 叢集是否由 Kerberos 保護，請檢查 Hadoop core-site.xml 中 hadoop.security.authentication 屬性的值。 若要參考由 Kerberos 保護的 Hadoop 叢集，您必須指定資料庫範圍的認證，其中包含您的 Kerberos 使用者名稱與密碼。 資料庫主要金鑰用來加密資料庫範圍的認證密碼。

```sql
-- Create a database master key if one does not already exist, using your own password. This key is used to encrypt the credential secret in next step.
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'S0me!nfo' ;

-- Create a database scoped credential with Kerberos user name and password.
CREATE DATABASE SCOPED CREDENTIAL HadoopUser1
WITH
     IDENTITY = '<hadoop_user_name>',
     SECRET = '<hadoop_password>' ;

-- Create an external data source with CREDENTIAL option.
CREATE EXTERNAL DATA SOURCE MyHadoopCluster
WITH
  ( LOCATION = 'hdfs://10.10.10.10:8050' ,
    CREDENTIAL = HadoopUser1 ,
    TYPE = HADOOP ,
    RESOURCE_MANAGER_LOCATION = '10.10.10.10:8050'
  );
```

### <a name="e-create-external-data-source-to-access-data-in-azure-storage-using-the-wasb-interface"></a>E. 使用 wasb:// 介面建立存取 Azure 儲存體中資料的外部資料來源
在此範例中，外部資料來源是名為 `logs` 的 Azure V2 儲存體帳戶。 容器名稱為 `daily`。 Azure 儲存體外部資料來源僅供資料傳輸使用。 不支援述詞下推。 透過 `wasb://` 介面存取資料時，不支援階層命名空間。

此範例示範如何建立資料庫範圍認證，以便向 Azure V2 儲存體帳戶進行驗證。 在資料庫認證祕密中指定 Azure 儲存體帳戶金鑰。 您可以在資料庫範圍認證身分識別中指定任何字串，因為系統不會使用其向 Azure 儲存體進行驗證。

```sql
-- Create a database master key if one does not already exist, using your own password. This key is used to encrypt the credential secret in next step.
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'S0me!nfo' ;

-- Create a database scoped credential with Azure storage account key as the secret.
CREATE DATABASE SCOPED CREDENTIAL AzureStorageCredential
WITH
  IDENTITY = '<my_account>' ,
  SECRET = '<azure_storage_account_key>' ;

-- Create an external data source with CREDENTIAL option.
CREATE EXTERNAL DATA SOURCE MyAzureStorage
WITH
  ( LOCATION = 'wasbs://daily@logs.blob.core.windows.net/' ,
    CREDENTIAL = AzureStorageCredential ,
    TYPE = HADOOP
  ) ;
```

### <a name="f-create-external-data-source-to-reference-a-sql-server-named-instance-via-polybase-connectivity-sql-server-2019"></a>F. 透過 PolyBase 連線建立參考 SQL Server 具名執行個體的外部資料來源 ([!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)])

若要建立參考 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 具名執行個體的外部資料來源，您可以使用 CONNECTION_OPTIONS 來指定執行個體名稱。 在以下範例中，`WINSQL2019` 是主機名稱，而 `SQL2019` 是執行個體名稱。

```sql
CREATE EXTERNAL DATA SOURCE SQLServerInstance2
WITH (
  LOCATION = 'sqlserver://WINSQL2019' ,
  CONNECTION_OPTIONS = 'Server=%s\SQL2019' ,
  CREDENTIAL = SQLServerCredentials
) ;
```

或者，您也可以使用連接埠來連線到 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體。

```sql
CREATE EXTERNAL DATA SOURCE SQLServerInstance2
WITH (
  LOCATION = 'sqlserver://WINSQL2019:58137' ,
  CREDENTIAL = SQLServerCredentials
) ;
```

### <a name="g-create-external-data-source-to-reference-kafka"></a>G. 建立參考 Kafka 的外部資料來源

在此範例中，外部資料來源是 Kafak 伺服器，其 IP 位址為 xxx.xxx.xxx.xxx 且在連接埠 1900 上接聽。 Kafka 外部資料來源僅供資料串流使用，而且不支援述詞下推。

```sql
-- Create an External Data Source for Kafka
CREATE EXTERNAL DATA SOURCE MyKafkaServer WITH (
    LOCATION = 'kafka://xxx.xxx.xxx.xxx:1900'
)
go
```

### <a name="h-create-external-data-source-to-reference-edgehub"></a>H. 建立參考 EdgeHub 的外部資料來源

在此範例中，外部資料來源是在與 Azure SQL Edge 相同的邊緣裝置上執行的 EdgeHub。 EdgeHub 外部資料來源僅供資料串流使用，而且不支援述詞下推。

```sql
-- Create an External Data Source for Kafka
CREATE EXTERNAL DATA SOURCE MyEdgeHub WITH (
    LOCATION = 'edgehub://'
)
go
```

## <a name="examples-bulk-operations"></a>範例：大量作業

> [!IMPORTANT]
> 設定大量作業的外部資料來源時，請不要在 `LOCATION` URL 的結尾處新增尾端 **/** 、檔案名稱或共用存取簽章參數。

### <a name="i-create-an-external-data-source-for-bulk-operations-retrieving-data-from-azure-storage"></a>I. 針對從 Azure 儲存體擷取資料的大量作業，建立外部資料來源

**適用於：** [!INCLUDE[ssSQLv14_md](../../includes/sssqlv14-md.md)]。
針對使用 [BULK INSERT][bulk_insert] 或 [OPENROWSET][openrowset] 的大量作業，請使用下列資料來源。 認證必須將 `SHARED ACCESS SIGNATURE` 設定為身分識別、不得在 SAS 權杖中有前置 `?`、必須至少擁有應載入檔案的讀取權限 (例如 `srt=o&sp=r`)，且到期時間應該有效 (所有日期都是 UTC 時間)。 如需共用存取簽章的詳細資訊，請參閱[使用共用存取簽章 (SAS)][sas_token]。

```sql
CREATE DATABASE SCOPED CREDENTIAL AccessAzureInvoices
WITH
  IDENTITY = 'SHARED ACCESS SIGNATURE',
  -- Remove ? from the beginning of the SAS token
  SECRET = '******srt=sco&sp=rwac&se=2017-02-01T00:55:34Z&st=2016-12-29T16:55:34Z***************' ;

CREATE EXTERNAL DATA SOURCE MyAzureInvoices
WITH
  ( LOCATION = 'https://newinvoices.blob.core.windows.net/week3' ,
    CREDENTIAL = AccessAzureInvoices ,
    TYPE = BLOB_STORAGE
  ) ;
```

若要查看使用中的這個範例，請參閱 [BULK INSERT][bulk_insert_example] 範例。

## <a name="see-also"></a>另請參閱

- [ALTER EXTERNAL DATA SOURCE (Transact-SQL)][alter_eds]
- [CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)][create_dsc]
- [CREATE EXTERNAL FILE FORMAT (Transact-SQL)][create_eff]
- [CREATE EXTERNAL TABLE (Transact-SQL)][create_etb]
- [sys.external_data_sources (Transact-SQL)][cat_eds]
- [使用共用存取簽章 (SAS)][sas_token]

<!-- links to external pages -->
<!-- SQL Docs -->
[bulk_insert]: ./bulk-insert-transact-sql.md
[bulk_insert_example]: ./bulk-insert-transact-sql.md#f-importing-data-from-a-file-in-azure-blob-storage
[openrowset]: ../functions/openrowset-transact-sql.md

[create_dsc]: ./create-database-scoped-credential-transact-sql.md
[create_eff]: ./create-external-file-format-transact-sql.md
[create_etb]: ./create-external-table-transact-sql.md
[create_etb_as_sel]: ./create-external-table-as-select-transact-sql.md?view=azure-sqldw-latest&preserve-view=true
[create_tbl_as_sel]: ./create-table-as-select-azure-sql-data-warehouse.md?view=azure-sqldw-latest&preserve-view=true

[alter_eds]: ./alter-external-data-source-transact-sql.md

[cat_eds]: ../../relational-databases/system-catalog-views/sys-external-data-sources-transact-sql.md
<!-- PolyBase docs -->
[intro_pb]: ../../relational-databases/polybase/polybase-guide.md
[mongodb_pb]: ../../relational-databases/polybase/polybase-configure-mongodb.md
[connectivity_pb]:https://docs.microsoft.com/sql/database-engine/configure-windows/polybase-connectivity-configuration-transact-sql
[connection_options]: ../../relational-databases/native-client/applications/using-connection-string-keywords-with-sql-server-native-client.md
[hint_pb]: ../../relational-databases/polybase/polybase-pushdown-computation.md#force-pushdown

<!-- Azure Docs -->
[sas_token]: /azure/storage/storage-dotnet-shared-access-signature-part-1

::: moniker-end
::: moniker range="=azuresqldb-current"

:::row:::
    :::column:::
        [SQL Server](create-external-data-source-transact-sql.md?view=sql-server-ver15&preserve-view=true)
    :::column-end:::
    :::column:::
        **_\* SQL Database \*_** &nbsp;
    :::column-end:::
    :::column:::
        [Azure Synapse<br />Analytics](create-external-data-source-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)
    :::column-end:::
    :::column:::
        [Analytics Platform<br />System (PDW)](create-external-data-source-transact-sql.md?view=aps-pdw-2016-au7&preserve-view=true)
    :::column-end:::
:::row-end:::

&nbsp;

## <a name="overview-azure-sql-database"></a>概觀：Azure SQL Database

建立彈性查詢的外部資料來源。 外部資料來源會用來建立連線能力，並支援這些主要使用案例：

- 使用 `BULK INSERT` 或 `OPENROWSET` 的大量載入作業
- 使用 SQL Database 搭配[彈性查詢][remote_eq]，查詢遠端 SQL Database 或 Azure Synapse 執行個體
- 使用[彈性查詢][sharded_eq]，查詢分區化 Azure SQL Database

## <a name="syntax"></a>語法

```syntaxsql
CREATE EXTERNAL DATA SOURCE <data_source_name>
WITH
  ( [ LOCATION = '<prefix>://<path>[:<port>]' ]
    [ [ , ] CREDENTIAL = <credential_name> ]
    [ [ , ] TYPE = { BLOB_STORAGE | RDBMS | SHARD_MAP_MANAGER } ]
    [ [ , ] DATABASE_NAME = '<database_name>' ]
    [ [ , ] SHARD_MAP_NAME = '<shard_map_manager>' ] )
[ ; ]
```

## <a name="arguments"></a>引數

### <a name="data_source_name"></a>data_source_name

指定資料來源的使用者定義名稱。 這個名稱在 SQL Database 的資料庫內必須是唯一的。

### <a name="location--prefixpathport"></a>LOCATION = *`'<prefix>://<path[:port]>'`*

提供連線通訊協定和路徑給外部資料來源。

| 外部資料來源   | 位置前置詞 | 位置路徑                                         |
| ---------------------- | --------------- | ----------------------------------------------------- |
| 大量作業        | `https`         | `<storage_account>.blob.core.windows.net/<container>` |
| 彈性查詢 (分區)  | 不需要    | `<shard_map_server_name>.database.windows.net`        |
| 彈性查詢 (遠端) | 不需要    | `<remote_server_name>.database.windows.net`           |

位置路徑：

- `<shard_map_server_name>` = Azure 中裝載分區對應管理員的邏輯伺服器名稱。 `DATABASE_NAME` 引數提供用於裝載分區對應的資料庫，而 `SHARD_MAP_NAME` 則用於分區對應本身。
- `<remote_server_name>` = 彈性查詢的目標邏輯伺服器名稱。 資料庫名稱則是使用 `DATABASE_NAME` 引數來指定。

設定位置時的其他注意事項和指引：

- 建立物件時，[!INCLUDE[ssde_md](../../includes/ssde_md.md)] 不會驗證外部資料來源是否存在。 若要驗證，請使用外部資料來源建立外部資料表。

### <a name="credential--credential_name"></a>CREDENTIAL = *credential_name*

指定資料庫範圍的認證，以便向外部資料來源進行驗證。

建立認證時的其他注意事項和指引：

- 若要從 Azure 儲存體將資料載入 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]，請使用共用存取簽章 (SAS 權杖)。
- 只有在資料受到保護時才需要 `CREDENTIAL`。 允許匿名存取的資料集不需要 `CREDENTIAL`。
- 當 `TYPE` = `BLOB_STORAGE` 時，必須使用 `SHARED ACCESS SIGNATURE` 作為身分識別來建立認證。 此外，SAS 權杖應設定如下：
  - 設定為祕密時，請排除前置 `?`
  - 至少擁有應載入檔案的讀取權限 (例如 `srt=o&sp=r`)
  - 使用有效的到期時間 (所有日期都是 UTC 時間)。

如需搭配 `SHARED ACCESS SIGNATURE` 和 `TYPE` = `BLOB_STORAGE` 使用 `CREDENTIAL` 的範例，請參閱[建立外部資料來源以執行大量作業並將資料從 Azure 儲存體擷取到 SQL Database](#c-create-an-external-data-source-for-bulk-operations-retrieving-data-from-azure-storage)

若要建立資料庫範圍認證，請參閱 [CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)][create_dsc]。

### <a name="type---blob_storage--rdbms--shard_map_manager"></a>TYPE = *[ BLOB_STORAGE | RDBMS | SHARD_MAP_MANAGER]*

指定要設定的外部資料來源類型。 不一定需要此參數。

- 使用 `RDBMS` 處理使用 SQL Database 彈性查詢的跨資料庫查詢。
- 建立外部資料來源以連線到共用 SQL Database 時，請使用 `SHARD_MAP_MANAGER`。
- 使用 [BULK INSERT][bulk_insert] 或 [OPENROWSET][openrowset] 執行大量作業時，請使用 `BLOB_STORAGE`。

> [!IMPORTANT]
> 如果使用任何其他外部資料來源，請不要設定 `TYPE`。

### <a name="database_name--database_name"></a>DATABASE_NAME = *database_name*

當 `TYPE` 設定為 `RDBMS` 或 `SHARD_MAP_MANAGER` 時，請設定此引數。

| TYPE              | DATABASE_NAME 的值                                       |
| ----------------- | ------------------------------------------------------------ |
| RDBMS             | 使用 `LOCATION` 所提供伺服器上的遠端資料庫名稱 |
| SHARD_MAP_MANAGER | 以分區對應管理員運作的資料庫名稱      |

如需示範如何建立外部資料來源 (其中 `TYPE` =`RDBMS`) 的範例，請參閱[建立 RDBMS 外部資料來源](#b-create-an-rdbms-external-data-source)

### <a name="shard_map_name--shard_map_name"></a>SHARD_MAP_NAME = *shard_map_name*

僅限在 `TYPE` 引數設定為 `SHARD_MAP_MANAGER` 時用於設定分區對應的名稱。

如需示範如何建立外部資料來源 (其中 `TYPE` =`SHARD_MAP_MANAGER`) 的範例，請參閱[建立分區對應管理員外部資料來源](#a-create-a-shard-map-manager-external-data-source)

## <a name="permissions"></a>權限

需要有 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 資料庫的 `CONTROL` 權限。

## <a name="locking"></a>鎖定

對 `EXTERNAL DATA SOURCE` 物件採取共用鎖定。

## <a name="examples"></a>範例：

### <a name="a-create-a-shard-map-manager-external-data-source"></a>A. 建立分區對應管理員外部資料來源

若要建立參考 SHARD_MAP_MANAGER 的外部資料來源，請指定要在 SQL Database 或虛擬機器上的 SQL Server 資料庫中裝載分區對應管理員的 SQL Database 伺服器名稱。

```sql
CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<password>' ;

CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred
WITH
  IDENTITY = '<username>',
  SECRET = '<password>' ;

CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc
WITH
  ( TYPE = SHARD_MAP_MANAGER ,
    LOCATION = '<server_name>.database.windows.net' ,
    DATABASE_NAME = 'ElasticScaleStarterKit_ShardMapManagerDb' ,
    CREDENTIAL = ElasticDBQueryCred ,
    SHARD_MAP_NAME = 'CustomerIDShardMap'
  ) ;
```

如需逐步教學課程，請參閱[分區彈性查詢入門 (水平資料分割)][sharded_eq_tutorial]。

### <a name="b-create-an-rdbms-external-data-source"></a>B. 建立 RDBMS 外部資料來源

若要建立參考 RDBMS 的外部資料來源，請在 SQL Database 中指定遠端資料庫的 SQL Database 伺服器名稱。

```sql
CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<password>' ;

CREATE DATABASE SCOPED CREDENTIAL SQL_Credential
WITH
  IDENTITY = '<username>' ,
  SECRET = '<password>' ;

CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc
WITH
  ( TYPE = RDBMS ,
    LOCATION = '<server_name>.database.windows.net' ,
    DATABASE_NAME = 'Customers' ,
    CREDENTIAL = SQL_Credential
  ) ;
```

如需有關 RDBMS 的逐步教學課程，請參閱[跨資料庫查詢入門 (垂直資料分割)][remote_eq_tutorial]。

## <a name="examples-bulk-operations"></a>範例：大量作業

> [!IMPORTANT]
> 設定大量作業的外部資料來源時，請不要在 `LOCATION` URL 的結尾處新增尾端 **/** 、檔案名稱或共用存取簽章參數。

### <a name="c-create-an-external-data-source-for-bulk-operations-retrieving-data-from-azure-storage"></a>C. 針對從 Azure 儲存體擷取資料的大量作業，建立外部資料來源

針對使用 [BULK INSERT][bulk_insert] 或 [OPENROWSET][openrowset] 的大量作業，請使用下列資料來源。 認證必須將 `SHARED ACCESS SIGNATURE` 設定為身分識別、不得在 SAS 權杖中有前置 `?`、必須至少擁有應載入檔案的讀取權限 (例如 `srt=o&sp=r`)，且到期時間應該有效 (所有日期都是 UTC 時間)。 如需共用存取簽章的詳細資訊，請參閱[使用共用存取簽章 (SAS)][sas_token]。

```sql
CREATE DATABASE SCOPED CREDENTIAL AccessAzureInvoices
WITH
  IDENTITY = 'SHARED ACCESS SIGNATURE',
  -- Remove ? from the beginning of the SAS token
  SECRET = '******srt=sco&sp=rwac&se=2017-02-01T00:55:34Z&st=2016-12-29T16:55:34Z***************' ;

CREATE EXTERNAL DATA SOURCE MyAzureInvoices
WITH
  ( LOCATION = 'https://newinvoices.blob.core.windows.net/week3' ,
    CREDENTIAL = AccessAzureInvoices ,
    TYPE = BLOB_STORAGE
  ) ;
```

若要查看使用中的這個範例，請參閱 [BULK INSERT][bulk_insert_example]。

## <a name="see-also"></a>另請參閱

- [CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)][create_dsc]
- [CREATE EXTERNAL TABLE (Transact-SQL)][create_etb]
- [sys.external_data_sources (Transact-SQL)][cat_eds]
- [使用共用存取簽章 (SAS)][sas_token]
- [彈性查詢簡介][intro_eq]

<!-- links to external pages -->
<!-- SQL Docs -->
[bulk_insert]: ./bulk-insert-transact-sql.md
[bulk_insert_example]: ./bulk-insert-transact-sql.md#f-importing-data-from-a-file-in-azure-blob-storage
[openrowset]: ../functions/openrowset-transact-sql.md
[create_dsc]: ./create-database-scoped-credential-transact-sql.md
[create_etb]: https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source
[alter_eds]: ./alter-external-data-source-transact-sql.md
[cat_eds]: ../../relational-databases/system-catalog-views/sys-external-data-sources-transact-sql.md
<!-- PolyBase docs -->
[intro_pb]: ../../relational-databases/polybase/polybase-guide.md
[mongodb_pb]: ../../relational-databases/polybase/polybase-configure-mongodb.md
[connectivity_pb]:https://docs.microsoft.com/sql/database-engine/configure-windows/polybase-connectivity-configuration-transact-sql
[connection_options]: ../../relational-databases/native-client/applications/using-connection-string-keywords-with-sql-server-native-client.md
[hint_pb]: ../../relational-databases/polybase/polybase-pushdown-computation.md#force-pushdown
<!-- Elastic Query Docs -->
[intro_eq]: /azure/azure-sql/database/elastic-query-overview
[remote_eq]: /azure/azure-sql/database/elastic-query-getting-started-vertical
[remote_eq_tutorial]: /azure/azure-sql/database/elastic-query-getting-started-vertical
[sharded_eq]: /azure/azure-sql/database/elastic-query-getting-started
[sharded_eq_tutorial]: /azure/azure-sql/database/elastic-query-getting-started

<!-- Azure Docs -->
[azure_ad]: /azure/data-lake-store/data-lake-store-authenticate-using-active-directory
[sas_token]: /azure/storage/storage-dotnet-shared-access-signature-part-1

::: moniker-end
::: moniker range="=azure-sqldw-latest"

:::row:::
    :::column:::
        [SQL Server](create-external-data-source-transact-sql.md?view=sql-server-ver15&preserve-view=true)
    :::column-end:::
    :::column:::
        [SQL Database](create-external-data-source-transact-sql.md?view=azuresqldb-current&preserve-view=true)
    :::column-end:::
    :::column:::
        **_\* Azure Synapse<br />Analytics \*_** &nbsp;
    :::column-end:::
    :::column:::
        [Analytics Platform<br />System (PDW)](create-external-data-source-transact-sql.md?view=aps-pdw-2016-au7&preserve-view=true)
    :::column-end:::
:::row-end:::

&nbsp;

## <a name="overview-azure-synapse-analytics"></a>概觀：Azure Synapse Analytics

建立 PolyBase 的外部資料來源。 外部資料來源會用來建立連線能力，並支援下列主要使用案例：使用 [PolyBase][intro_pb] 來執行資料虛擬化和資料載入

> [!IMPORTANT]  
> 若要建立外部資料來源，以使用 Azure SQL Database 搭配[彈性查詢][remote_eq]來查詢 SQL Analytics 資源，請參閱 [SQL Database](create-external-data-source-transact-sql.md?view=azuresqldb-current&preserve-view=true)。

## <a name="syntax"></a>語法

### [[!INCLUDE[sss-dedicated-pool-md.md](../../includes/sss-dedicated-pool-md.md)]](#tab/dedicated)
```syntaxsql
CREATE EXTERNAL DATA SOURCE <data_source_name>
WITH
  ( [ LOCATION = '<prefix>://<path>[:<port>]' ]
    [ [ , ] CREDENTIAL = <credential_name> ]
    [ [ , ] TYPE = HADOOP ]
[ ; ]
```
### [[!INCLUDE[sssod-md.md](../../includes/sssod-md.md)]](#tab/serverless)
```syntaxsql
CREATE EXTERNAL DATA SOURCE <data_source_name>  
WITH
(    LOCATION = '<prefix>://<path>[:<port>]'
) 
[;]
```
---

## <a name="arguments"></a>引數

### <a name="data_source_name"></a>data_source_name

指定資料來源的使用者定義名稱。 名稱在 [!INCLUDE[ssSDW](../../includes/sssdw-md.md)] 的 [!INCLUDE[ssazure_md](../../includes/ssazure_md.md)] 中必須為唯一。

### <a name="location--prefixpathport"></a>LOCATION = *`'<prefix>://<path[:port]>'`*

提供連線通訊協定和路徑給外部資料來源。

| 外部資料來源        | 位置前置詞 | 位置路徑                                         |
| --------------------------- | --------------- | ----------------------------------------------------- |
| Azure Data Lake Store Gen 1 | `adl`           | `<storage_account>.azuredatalake.net`                 |
| Azure Data Lake Store Gen 2 | `abfs[s]`       | `<container>@<storage_account>.dfs.core.windows.net`  |
| Azure V2 儲存體帳戶    | `wasb[s]`       | `<container>@<storage_account>.blob.core.windows.net` |

位置路徑：

- `<container>` = 保留資料的儲存體帳戶容器。 根容器是唯讀的，因此無法將資料寫回至容器。
- `<storage_account>` = Azure 資源的儲存體帳戶名稱。

設定位置時的其他注意事項和指引：

- 預設選項是在佈建 Azure Data Lake Storage Gen 2 時使用 `enable secure SSL connections`。 當啟用此項目時，您必須在選取了安全 TLS/SSL 連線時使用 `abfss`。 請注意，`abfss` 也適用於不安全的 TLS 連線。
- 在建立物件時，Azure Synapse 不會驗證外部資料來源是否存在。 . 若要驗證，請使用外部資料來源建立外部資料表。
- 查詢 Hadoop 時，請針對所有資料表使用相同的外部資料來源，以確保查詢語意一致。
- 由於資料會使用安全的 TLS 連線傳送，因此建議使用 `wasbs`
- 使用 wasb://介面透過 PolyBase 存取資料時，不支援搭配 Azure V2 儲存體帳戶使用階層命名空間。

### <a name="credential--credential_name"></a>CREDENTIAL = *credential_name*

指定資料庫範圍的認證，以便向外部資料來源進行驗證。

建立認證時的其他注意事項和指引：

- 若要將資料從 Azure 儲存體或 Azure Data Lake Store (ADLS) Gen 2 載入 Azure Synapse Analytics，請使用 Azure 儲存體金鑰。
- 只有在資料受到保護時才需要 `CREDENTIAL`。 允許匿名存取的資料集不需要 `CREDENTIAL`。

若要建立資料庫範圍認證，請參閱 [CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)][create_dsc]。

### <a name="type--hadoop"></a>TYPE = *HADOOP*

指定要設定的外部資料來源類型。 不一定需要此參數。

- 當外部資料來源為 Azure 儲存體、ADLS Gen 1 或 ADLS Gen 2 時，請使用 HADOOP。

如需使用 `TYPE` = `HADOOP` 從 Azure 儲存體載入資料的範例，請參閱[使用服務主體建立參考 Azure Data Lake Store Gen 1 或 2 的外部資料來源](#b-create-external-data-source-to-reference-azure-data-lake-store-gen-1-or-2-using-a-service-principal)。

## <a name="permissions"></a>權限

需要資料庫的 `CONTROL` 權限。

## <a name="locking"></a>鎖定

對 `EXTERNAL DATA SOURCE` 物件採取共用鎖定。

## <a name="security"></a>安全性

PolyBase 支援大多數外部資料來源的 Proxy 驗證。 建立資料庫範圍認證以建立 Proxy 帳戶。

當您連線到 SQL Server 巨量資料叢集中的儲存體或資料集區時，系統會將使用者的認證傳遞到後端系統。 在資料集區本身中建立登入以啟用傳遞驗證。

目前不支援 `HADOOP` 類型的 SAS 權杖。 只支援搭配儲存體帳戶存取金鑰使用。 嘗試使用 `HADOOP` 類型及 SAS 認證來建立外部資料來源時會失敗，並發生下列錯誤：

`Msg 105019, Level 16, State 1 - EXTERNAL TABLE access failed due to internal error: 'Java exception raised on call to HdfsBridge_Connect. Java exception message: Parameters provided to connect to the Azure storage account are not valid.: Error [Parameters provided to connect to the Azure storage account are not valid.] occurred while accessing external file.'`

## <a name="examples"></a>範例：

### <a name="a-create-external-data-source-to-access-data-in-azure-storage-using-the-wasb-interface"></a>A. 使用 wasb:// 介面建立存取 Azure 儲存體中資料的外部資料來源
在此範例中，外部資料來源是名為 `logs` 的 Azure V2 儲存體帳戶。 容器名稱為 `daily`。 Azure 儲存體外部資料來源僅供資料傳輸使用。 不支援述詞下推。 透過 `wasb://` 介面存取資料時，不支援階層命名空間。

此範例示範如何建立資料庫範圍認證，以便向 Azure 儲存體進行驗證。 在資料庫認證祕密中指定 Azure 儲存體帳戶金鑰。 您可以在資料庫範圍認證身分識別中指定任何字串，因為系統不會使用它向 Azure 儲存體進行驗證。

```sql
-- Create a database master key if one does not already exist, using your own password. This key is used to encrypt the credential secret in next step.
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'S0me!nfo' ;

-- Create a database scoped credential with Azure storage account key as the secret.
CREATE DATABASE SCOPED CREDENTIAL AzureStorageCredential
WITH
  IDENTITY = '<my_account>',
  SECRET = '<azure_storage_account_key>' ;

-- Create an external data source with CREDENTIAL option.
CREATE EXTERNAL DATA SOURCE MyAzureStorage
WITH
  ( LOCATION = 'wasbs://daily@logs.blob.core.windows.net/' ,
    CREDENTIAL = AzureStorageCredential ,
    TYPE = HADOOP
  ) ;
```

### <a name="b-create-external-data-source-to-reference-azure-data-lake-store-gen-1-or-2-using-a-service-principal"></a>B. 使用服務主體建立外部資料來源，來參考 Azure Data Lake Store Gen 1 或 2

Azure Data Lake Store 連線能力以您的 ADLS URI 與 Azure Active Directory 應用程式服務主體為基礎。 說明如何建立此應用程式的文件可以在[使用 Azure Active Directory 的 Data Lake Store 驗證][azure_ad]中找到。

```sql
-- If you do not have a Master Key on your DW you will need to create one.
CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<password>' ;

-- These values come from your Azure Active Directory Application used to authenticate to ADLS
CREATE DATABASE SCOPED CREDENTIAL ADLS_credential
WITH
  -- IDENTITY = '<clientID>@<OAuth2.0TokenEndPoint>' ,
  IDENTITY = '536540b4-4239-45fe-b9a3-629f97591c0c@https://login.microsoftonline.com/42f988bf-85f1-41af-91ab-2d2cd011da47/oauth2/token' ,
  -- SECRET = '<KEY>'
  SECRET = 'BjdIlmtKp4Fpyh9hIvr8HJlUida/seM5kQ3EpLAmeDI=' 
;

-- For Gen 1 - Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure Data Lake Storage.
-- LOCATION: Provide Data Lake Storage Gen 1 account name and URI
-- CREDENTIAL: Provide the credential created in the previous step
CREATE EXTERNAL DATA SOURCE AzureDataLakeStore
WITH
  ( LOCATION = 'adl://newyorktaxidataset.azuredatalakestore.net' ,
    CREDENTIAL = ADLS_credential ,
    TYPE = HADOOP
  ) ;

-- For Gen 2 - Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure Data Lake Storage.
-- LOCATION: Provide Data Lake Storage Gen 2 account name and URI
-- CREDENTIAL: Provide the credential created in the previous step
CREATE EXTERNAL DATA SOURCE AzureDataLakeStore
WITH
  -- Please note the abfss endpoint when your account has secure transfer enabled
  ( LOCATION = 'abfss://data@newyorktaxidataset.dfs.core.windows.net' , 
    CREDENTIAL = ADLS_credential ,
    TYPE = HADOOP
  ) ;
```

### <a name="c-create-external-data-source-to-reference-azure-data-lake-store-gen-2-using-the-storage-account-key"></a>C. 使用儲存體帳戶金鑰建立外部資料來源，來參考 Azure Data Lake Store Gen 2

```sql
-- If you do not have a Master Key on your DW you will need to create one.
CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<password>' ;

CREATE DATABASE SCOPED CREDENTIAL ADLS_credential
WITH
-- IDENTITY = '<storage_account_name>' ,
  IDENTITY = 'newyorktaxidata' ,
-- SECRET = '<storage_account_key>'
  SECRET = 'yz5N4+bxSb89McdiysJAzo+9hgEHcJRJuXbF/uC3mhbezES/oe00vXnZEl14U0lN3vxrFKsphKov16C0w6aiTQ=='
;

-- Note this example uses a Gen 2 secured endpoint (abfss)
CREATE EXTERNAL DATA SOURCE <data_source_name>
WITH
  ( LOCATION = 'abfss://2013@newyorktaxidataset.dfs.core.windows.net' ,
    CREDENTIAL = ADLS_credential ,
    TYPE = HADOOP
  ) ;
```

### <a name="d-create-external-data-source-to-reference-polybase-connectivity-to-azure-data-lake-store-gen-2-using-abfs"></a>D. 建立外部資料來源以使用 abfs:// 參考 Polybase 與 Azure Data Lake Store Gen 2 的連線

當連線至具有[受控識別](/azure/active-directory/managed-identities-azure-resources/overview
)機制的 Azure Data Lake 存放區 Gen2 帳戶時，不需要指定祕密。

```sql
-- If you do not have a Master Key on your DW you will need to create one
CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<password>' ;

--Create database scoped credential with **IDENTITY = 'Managed Service Identity'**

CREATE DATABASE SCOPED CREDENTIAL msi_cred 
WITH IDENTITY = 'Managed Service Identity' ;

--Create external data source with abfss:// scheme for connecting to your Azure Data Lake Store Gen2 account

CREATE EXTERNAL DATA SOURCE ext_datasource_with_abfss 
WITH 
  ( TYPE = HADOOP , 
    LOCATION = 'abfss://myfile@mystorageaccount.dfs.core.windows.net' , 
    CREDENTIAL = msi_cred
  ) ;
```

## <a name="see-also"></a>另請參閱

- [CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)][create_dsc]
- [CREATE EXTERNAL FILE FORMAT (Transact-SQL)][create_eff]
- [CREATE EXTERNAL TABLE (Transact-SQL)][create_etb]
- [CREATE EXTERNAL TABLE AS SELECT (Azure Synapse Analytics)][create_etb_as_sel]
- [CREATE TABLE AS SELECT (Azure Synapse Analytics)][create_tbl_as_sel]
- [sys.external_data_sources (Transact-SQL)][cat_eds]
- [使用共用存取簽章 (SAS)][sas_token]

<!-- links to external pages -->
<!-- SQL Docs -->
[bulk_insert]: ./bulk-insert-transact-sql.md
[bulk_insert_example]: ./bulk-insert-transact-sql.md#f-importing-data-from-a-file-in-azure-blob-storage
[openrowset]: ../functions/openrowset-transact-sql.md

[create_dsc]: ./create-database-scoped-credential-transact-sql.md
[create_eff]: ./create-external-file-format-transact-sql.md
[create_etb]: https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source
[create_etb_as_sel]: ./create-external-table-as-select-transact-sql.md?view=azure-sqldw-latest&preserve-view=true
[create_tbl_as_sel]: ./create-table-as-select-azure-sql-data-warehouse.md?view=azure-sqldw-latest&preserve-view=true

[alter_eds]: ./alter-external-data-source-transact-sql.md

[cat_eds]: ../../relational-databases/system-catalog-views/sys-external-data-sources-transact-sql.md
<!-- PolyBase docs -->
[intro_pb]: ../../relational-databases/polybase/polybase-guide.md
[mongodb_pb]: ../../relational-databases/polybase/polybase-configure-mongodb.md
[connectivity_pb]:https://docs.microsoft.com/sql/database-engine/configure-windows/polybase-connectivity-configuration-transact-sql
[connection_options]: ../../relational-databases/native-client/applications/using-connection-string-keywords-with-sql-server-native-client.md
[hint_pb]: ../../relational-databases/polybase/polybase-pushdown-computation.md#force-pushdown
<!-- Elastic Query Docs -->
[intro_eq]: /azure/azure-sql/database/elastic-query-overview
[remote_eq]: /azure/azure-sql/database/elastic-query-getting-started-vertical
[remote_eq_tutorial]: /azure/azure-sql/database/elastic-query-getting-started-vertical
[sharded_eq]: /azure/azure-sql/database/elastic-query-getting-started
[sharded_eq_tutorial]: /azure/azure-sql/database/elastic-query-getting-started

<!-- Azure Docs -->
[azure_ad]: /azure/data-lake-store/data-lake-store-authenticate-using-active-directory
[sas_token]: /azure/storage/storage-dotnet-shared-access-signature-part-1

::: moniker-end
::: moniker range=">=aps-pdw-2016"

:::row:::
    :::column:::
        [SQL Server](create-external-data-source-transact-sql.md?view=sql-server-ver15&preserve-view=true)
    :::column-end:::
    :::column:::
        [SQL Database](create-external-data-source-transact-sql.md?view=azuresqldb-current&preserve-view=true)
    :::column-end:::
    :::column:::
        [Azure Synapse<br />Analytics](create-external-data-source-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)
    :::column-end:::
    :::column:::
        **_\* Analytics<br />Platform System (PDW) \*_** &nbsp;
    :::column-end:::
:::row-end:::

&nbsp;

## <a name="overview-analytics-platform-system"></a>概觀：分析平台系統

建立 PolyBase 查詢的外部資料來源。 外部資料來源會用來建立連線能力，並支援下列使用案例：使用 [PolyBase][intro_pb] 來執行資料虛擬化和資料載入

## <a name="syntax"></a>語法

```syntaxsql
CREATE EXTERNAL DATA SOURCE <data_source_name>
WITH
  ( [ LOCATION = '<prefix>://<path>[:<port>]' ]
    [ [ , ] CREDENTIAL = <credential_name> ]
    [ [ , ] TYPE = HADOOP ]
    [ [ , ] RESOURCE_MANAGER_LOCATION = '<resource_manager>[:<port>]' )
[ ; ]
```

## <a name="arguments"></a>引數

### <a name="data_source_name"></a>data_source_name

指定資料來源的使用者定義名稱。 名稱在 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] 的伺服器中必須為唯一。

### <a name="location--prefixpathport"></a>LOCATION = *`'<prefix>://<path[:port]>'`*

提供連線通訊協定和路徑給外部資料來源。

| 外部資料來源    | 位置前置詞 | 位置路徑                                         |
| ----------------------- | --------------- | ----------------------------------------------------- |
| Cloudera 或 Hortonworks | `hdfs`          | `<Namenode>[:port]`                                   |
| Azure 儲存體帳戶   | `wasb[s]`       | `<container>@<storage_account>.blob.core.windows.net` |

位置路徑：

- `<Namenode>` = Hadoop 叢集中電腦名稱、名稱服務 URI 或 `Namenode` 的 IP 位址。 PolyBase 必須解析 Hadoop 叢集所使用的任何 DNS 名稱。 <!-- For highly available Hadoop configurations, provide the Nameservice ID as the `LOCATION`. -->
- `port` = 外部資料來源正在接聽的連接埠。 在 Hadoop 中，此連接埠可使用 `fs.defaultFS` 設定參數來尋找。 預設值為 8020。
- `<container>` = 保留資料的儲存體帳戶容器。 根容器是唯讀的，因此無法將資料寫回至容器。
- `<storage_account>` = Azure 資源的儲存體帳戶名稱。

設定位置時的其他注意事項和指引：

- 在建立物件時，PDW 引擎不會驗證外部資料來源是否存在。 若要驗證，請使用外部資料來源建立外部資料表。
- 查詢 Hadoop 時，請針對所有資料表使用相同的外部資料來源，以確保查詢語意一致。
- 由於資料會使用安全的 TLS 連線傳送，因此建議使用 `wasbs`。
- 透過 wasb:// 搭配 Azure 儲存體帳戶使用時，不支援階層命名空間。
- 為確保在 Hadoop `Namenode` 容錯移轉期間能成功進行 PolyBase 查詢，請考慮使用虛擬 IP 位址作為 Hadoop 叢集的 `Namenode`。 若未這樣做，請執行 [ALTER EXTERNAL DATA SOURCE][alter_eds] 命令以指向新位置。

### <a name="credential--credential_name"></a>CREDENTIAL = *credential_name*

指定資料庫範圍的認證，以便向外部資料來源進行驗證。

建立認證時的其他注意事項和指引：

- 若要將 Azure 儲存體的資料載入 Azure Synapse 或 PDW，請使用 Azure 儲存體金鑰。
- 只有在資料受到保護時才需要 `CREDENTIAL`。 允許匿名存取的資料集不需要 `CREDENTIAL`。

### <a name="type---hadoop-"></a>TYPE = *[ HADOOP ]*

指定要設定的外部資料來源類型。 不一定需要此參數。

- 當外部資料來源為 Cloudera、Hortonworks 或 Azure 儲存體時，請使用 HADOOP。

如需使用 `TYPE` = `HADOOP` 從 Azure 儲存體載入資料的範例，請參閱[建立參考 Hadoop 的外部資料來源](#a-create-external-data-source-to-reference-hadoop)。

### <a name="resource_manager_location--resourcemanager_uriport"></a>RESOURCE_MANAGER_LOCATION = *'ResourceManager_URI[:port]'*

連線到 Hortonworks 或 Cloudera 時，請設定這個選用值。

定義 `RESOURCE_MANAGER_LOCATION` 時，查詢最佳化工具會制訂成本型決策以改善效能。 MapReduce 作業可用於將計算下推到 Hadoop。 指定 `RESOURCE_MANAGER_LOCATION` 可大幅降低在 Hadoop 與 SQL 之間傳輸的資料量，而導致查詢效能獲得改善。

若未指定 Resource Manager，系統會針對 PolyBase 查詢停用將計算推送到 Hadoop。

若未指定連接埠，系統會使用 'hadoop connectivity' 設定的目前設定選擇預設值。

| Hadoop 連線能力 | 預設資源管理員連接埠 |
| ------------------- | ----------------------------- |
| 1                   | 50300                         |
| 2                   | 50300                         |
| 3                   | 8021                          |
| 4                   | 8032                          |
| 5                   | 8050                          |
| 6                   | 8032                          |
| 7                   | 8050                          |

如需支援 Hadoop 版本的完整清單，請參閱 [PolyBase 連線設定 (Transact-SQL)][connectivity_pb]。

> [!IMPORTANT]
> 當您建立外部資料來源時，不會驗證 `RESOURCE_MANAGER_LOCATION` 值。 輸入不正確的值可能會導致每次嘗試下推就在執行時間發生查詢失敗，因為無法解析所提供的值。

[在已啟用下推的情況下，建立參考 Hadoop 的外部資料來源](#b-create-external-data-source-to-reference-hadoop-with-push-down-enabled)提供具體範例及進一步指引。

## <a name="permissions"></a>權限

需要有 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] 資料庫的 `CONTROL` 權限。

> [!NOTE]
> 在舊版 PDW 中，建立外部資料來源需要有 `ALTER ANY EXTERNAL DATA SOURCE` 權限。

## <a name="locking"></a>鎖定

對 `EXTERNAL DATA SOURCE` 物件採取共用鎖定。

## <a name="security"></a>安全性

PolyBase 支援大多數外部資料來源的 Proxy 驗證。 建立資料庫範圍認證以建立 Proxy 帳戶。

目前不支援 `HADOOP` 類型的 SAS 權杖。 只支援搭配儲存體帳戶存取金鑰使用。 嘗試使用 `HADOOP` 類型及 SAS 認證來建立外部資料來源時會失敗，並發生下列錯誤：

`Msg 105019, Level 16, State 1 - EXTERNAL TABLE access failed due to internal error: 'Java exception raised on call to HdfsBridge_Connect. Java exception message: Parameters provided to connect to the Azure storage account are not valid.: Error [Parameters provided to connect to the Azure storage account are not valid.] occurred while accessing external file.'`

## <a name="examples"></a>範例：

### <a name="a-create-external-data-source-to-reference-hadoop"></a>A. 建立參考 Hadoop 的外部資料來源

若要建立參考 Hortonworks 或 Cloudera Hadoop 叢集的外部資料來源，請指定 Hadoop `Namenode` 的電腦名稱或 IP 位址與連接埠。 <!-- Provide the Nameservice ID as the `LOCATION` for highly available configurations. -->

```sql
CREATE EXTERNAL DATA SOURCE MyHadoopCluster
WITH
  ( LOCATION = 'hdfs://10.10.10.10:8050' ,
    TYPE = HADOOP
  ) ;
```

### <a name="b-create-external-data-source-to-reference-hadoop-with-push-down-enabled"></a>B. 在已啟用下推的情況下，建立參考 Hadoop 的外部資料來源

指定 `RESOURCE_MANAGER_LOCATION` 選項，以便對適用於 PolyBase 查詢的 Hadoop 啟用下推計算。 啟用之後，PolyBase 會制訂成本型決策來判斷是否應該將查詢計算推送到 Hadoop。

```sql
CREATE EXTERNAL DATA SOURCE MyHadoopCluster
WITH
  ( LOCATION = 'hdfs://10.10.10.10:8020'
    TYPE = HADOOP
    RESOURCE_MANAGER_LOCATION = '10.10.10.10:8050'
) ;
```

### <a name="c-create-external-data-source-to-reference-kerberos-secured-hadoop"></a>C. 建立參考由 Kerberos 保護之 Hadoop 的外部資料來源

若要確認 Hadoop 叢集是否由 Kerberos 保護，請檢查 Hadoop core-site.xml 中 hadoop.security.authentication 屬性的值。 若要參考由 Kerberos 保護的 Hadoop 叢集，您必須指定資料庫範圍的認證，其中包含您的 Kerberos 使用者名稱與密碼。 資料庫主要金鑰用來加密資料庫範圍的認證密碼。

```sql
-- Create a database master key if one does not already exist, using your own password. This key is used to encrypt the credential secret in next step.
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'S0me!nfo' ;

-- Create a database scoped credential with Kerberos user name and password.
CREATE DATABASE SCOPED CREDENTIAL HadoopUser1
WITH
  IDENTITY = '<hadoop_user_name>' ,
  SECRET = '<hadoop_password>' ;

-- Create an external data source with CREDENTIAL option.
CREATE EXTERNAL DATA SOURCE MyHadoopCluster
WITH
  ( LOCATION = 'hdfs://10.10.10.10:8050' ,
    CREDENTIAL = HadoopUser1 ,
    TYPE = HADOOP ,
    RESOURCE_MANAGER_LOCATION = '10.10.10.10:8050'
  ) ;
```

### <a name="d-create-external-data-source-to-access-data-in-azure-storage-using-the-wasb-interface"></a>D. 使用 wasb:// 介面建立存取 Azure 儲存體中資料的外部資料來源

在此範例中，外部資料來源是名為 `logs` 的 Azure V2 儲存體帳戶。 容器名稱為 `daily`。 Azure 儲存體外部資料來源僅供資料傳輸使用。 不支援述詞下推。 透過 `wasb://` 介面存取資料時，不支援階層命名空間。

此範例示範如何建立資料庫範圍的認證，以便向 Azure 儲存體進行驗證。 在資料庫認證密碼中指定 Azure 儲存體帳戶金鑰。 您可以在資料庫範圍認證身分識別中指定任何字串，因為系統不會使用它向 Azure 儲存體進行驗證。

```sql
-- Create a database master key if one does not already exist, using your own password. This key is used to encrypt the credential secret in next step.
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'S0me!nfo' ;

-- Create a database scoped credential with Azure storage account key as the secret.
CREATE DATABASE SCOPED CREDENTIAL AzureStorageCredential
WITH
  IDENTITY = '<my_account>' ,
  SECRET = '<azure_storage_account_key>' ;

-- Create an external data source with CREDENTIAL option.
CREATE EXTERNAL DATA SOURCE MyAzureStorage
WITH
  ( LOCATION = 'wasbs://daily@logs.blob.core.windows.net/'
    CREDENTIAL = AzureStorageCredential
    TYPE = HADOOP
  ) ;
```


## <a name="see-also"></a>另請參閱

- [CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)][create_dsc]
- [CREATE EXTERNAL FILE FORMAT (Transact-SQL)][create_eff]
- [CREATE EXTERNAL TABLE (Transact-SQL)][create_etb]
- [sys.external_data_sources (Transact-SQL)][cat_eds]
- [使用共用存取簽章 (SAS)][sas_token]

<!-- links to external pages -->
<!-- SQL Docs -->
[bulk_insert]: ./bulk-insert-transact-sql.md
[bulk_insert_example]: ./bulk-insert-transact-sql.md#f-importing-data-from-a-file-in-azure-blob-storage
[openrowset]: ../functions/openrowset-transact-sql.md

[create_dsc]: ./create-database-scoped-credential-transact-sql.md
[create_eff]: ./create-external-file-format-transact-sql.md
[create_etb]: https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source
[create_etb_as_sel]: ./create-external-table-as-select-transact-sql.md?view=azure-sqldw-latest&preserve-view=true
[create_tbl_as_sel]: ./create-table-as-select-azure-sql-data-warehouse.md?view=azure-sqldw-latest&preserve-view=true

[alter_eds]: ./alter-external-data-source-transact-sql.md

[cat_eds]: ../../relational-databases/system-catalog-views/sys-external-data-sources-transact-sql.md
<!-- PolyBase docs -->
[intro_pb]: ../../relational-databases/polybase/polybase-guide.md
[mongodb_pb]: ../../relational-databases/polybase/polybase-configure-mongodb.md
[connectivity_pb]:https://docs.microsoft.com/sql/database-engine/configure-windows/polybase-connectivity-configuration-transact-sql
[connection_options]: ../../relational-databases/native-client/applications/using-connection-string-keywords-with-sql-server-native-client.md
[hint_pb]: ../../relational-databases/polybase/polybase-pushdown-computation.md#force-pushdown
<!-- Elastic Query Docs -->
[intro_eq]: /azure/azure-sql/database/elastic-query-overview
[remote_eq]: /azure/azure-sql/database/elastic-query-getting-started-vertical
[remote_eq_tutorial]: /azure/azure-sql/database/elastic-query-getting-started-vertical
[sharded_eq]: /azure/azure-sql/database/elastic-query-getting-started
[sharded_eq_tutorial]: /azure/azure-sql/database/elastic-query-getting-started

<!-- Azure Docs -->
[azure_ad]: /azure/data-lake-store/data-lake-store-authenticate-using-active-directory
[sas_token]: /azure/storage/storage-dotnet-shared-access-signature-part-1

::: moniker-end
