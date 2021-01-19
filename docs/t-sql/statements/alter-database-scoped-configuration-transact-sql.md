---
title: ALTER DATABASE SCOPED CONFIGURATION
description: 可啟用數個個別資料庫層級的資料庫組態設定。
titleSuffix: SQL Server (Transact-SQL)
ms.custom: seo-lt-2019
ms.date: 09/15/2020
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: conceptual
f1_keywords:
- ALTER_DATABASE_SCOPED_CONFIGURATION
- ALTER_DATABASE_SCOPED_CONFIGURATION_TSQL
- DATABASE_SCOPED_CONFIGURATION_TSQL
- SCOPED_CONFIGURATION_TSQL
- SCOPED_TSQL
- ALTER_DATABASE_SCOPED_TSQL
- DATABASE_SCOPED_TSQL
helpviewer_keywords:
- ALTER DATABASE SCOPED CONFIGURATION statement
- configuration [SQL Server], ALTER DATABASE SCOPED CONFIGURATION statement
ms.assetid: 63373c2f-9a0b-431b-b9d2-6fa35641571a
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: = azuresqldb-current || = azuresqldb-mi-current || >= sql-server-2016 || >= sql-server-linux-2017 ||=azure-sqldw-latest
ms.openlocfilehash: 8d314e90bbe869dec9ccdc31c45996c4834fc829
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98170420"
---
# <a name="alter-database-scoped-configuration-transact-sql"></a>ALTER DATABASE SCOPED CONFIGURATION (Transact-SQL)

[!INCLUDE[tsql-appliesto-ss2016-asdb-asdw-xxx-md.md](../../includes/tsql-appliesto-ss2016-asdb-asdw-xxx-md.md)]

此命令可以在 **個別資料庫** 層級設定幾項資料庫設定。 

如[＜引數＞](#arguments)一節中每個設定的 [適用於] 一行所示，[!INCLUDE[sssdsfull](../../includes/sssdsfull-md.md)] 和 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中支援下列設定： 

- 清除程序快取。
- 針對主要資料庫將 MAXDOP 參數設定為任意值 (1、2、...，以最適用於該特定資料庫的值為準)，並且針對所使用的所有次要資料庫 (例如，用於報表查詢) 設定不同的值 (例如 0)。
- 將與資料庫無關的查詢最佳化工具基數估計模型設定為相容性層級。
- 在資料庫層級啟用或停用參數探測。
- 在資料庫層級啟用或停用查詢最佳化。
- 在資料庫層級啟用或停用識別快取。
- 允許或不允許在第一次編譯批次時，將已編譯的計劃虛設常式儲存在快取中。
- 啟用或停用原生編譯 T-SQL 模組的執行統計資料收集。
- 為支援 `ONLINE =` 語法的 DDL 陳述式啟用或停用預設為線上的選項。
- 為支援 `RESUMABLE =` 語法的 DDL 陳述式啟用或停用預設為可繼續的選項。
- 啟用或停用[智慧查詢處理](../../relational-databases/performance/intelligent-query-processing.md)功能。
- 啟用或停用加速強制執行計劃。
- 啟用或停用全域暫存資料表的自動卸除功能。
- 啟用或停用[輕量型查詢分析基礎結構](../../relational-databases/performance/query-profiling-infrastructure.md)。
- 啟用或停用新的 `String or binary data would be truncated` 錯誤訊息。
- 啟用或停用 [sys.dm_exec_query_plan_stats](../../relational-databases/system-dynamic-management-views/sys-dm-exec-query-plan-stats-transact-sql.md) 中最後一個實際執行計畫的集合。
- 指定暫停的可繼續索引作業在被 SQL Server 引擎自動中止之前，所暫停的分鐘數。
- 啟用或停用非同步統計資料更新的低優先順序等候鎖定

此設定僅適用於 Azure Synapse Analytics。
- 設定使用者資料庫相容性層級

![連結圖示](../../database-engine/configure-windows/media/topic-link.gif "連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)

## <a name="syntax"></a>語法

```syntaxsql
-- Syntax for SQL Server and Azure SQL Database

ALTER DATABASE SCOPED CONFIGURATION
{
    { [ FOR SECONDARY] SET <set_options>}
}
| CLEAR PROCEDURE_CACHE [plan_handle]
| SET < set_options >
[;]

< set_options > ::=
{
    MAXDOP = { <value> | PRIMARY}
    | LEGACY_CARDINALITY_ESTIMATION = { ON | OFF | PRIMARY}
    | PARAMETER_SNIFFING = { ON | OFF | PRIMARY}
    | QUERY_OPTIMIZER_HOTFIXES = { ON | OFF | PRIMARY}
    | IDENTITY_CACHE = { ON | OFF }
    | INTERLEAVED_EXECUTION_TVF = { ON | OFF }
    | BATCH_MODE_MEMORY_GRANT_FEEDBACK = { ON | OFF }
    | BATCH_MODE_ADAPTIVE_JOINS = { ON | OFF }
    | TSQL_SCALAR_UDF_INLINING = { ON | OFF }
    | ELEVATE_ONLINE = { OFF | WHEN_SUPPORTED | FAIL_UNSUPPORTED }
    | ELEVATE_RESUMABLE = { OFF | WHEN_SUPPORTED | FAIL_UNSUPPORTED }
    | OPTIMIZE_FOR_AD_HOC_WORKLOADS = { ON | OFF }
    | XTP_PROCEDURE_EXECUTION_STATISTICS = { ON | OFF }
    | XTP_QUERY_EXECUTION_STATISTICS = { ON | OFF }
    | ROW_MODE_MEMORY_GRANT_FEEDBACK = { ON | OFF }
    | BATCH_MODE_ON_ROWSTORE = { ON | OFF }
    | DEFERRED_COMPILATION_TV = { ON | OFF }
    | ACCELERATED_PLAN_FORCING = { ON | OFF }
    | GLOBAL_TEMPORARY_TABLE_AUTO_DROP = { ON | OFF }
    | LIGHTWEIGHT_QUERY_PROFILING = { ON | OFF }
    | VERBOSE_TRUNCATION_WARNINGS = { ON | OFF }
    | LAST_QUERY_PLAN_STATS = { ON | OFF }
    | PAUSED_RESUMABLE_INDEX_ABORT_DURATION_MINUTES = <time>
    | ISOLATE_SECURITY_POLICY_CARDINALITY  = { ON | OFF }
    | ASYNC_STATS_UPDATE_WAIT_AT_LOW_PRIORITY = { ON | OFF }
}
```

> [!IMPORTANT]
> 從 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 開始，[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 中已變更部分選項名稱：      
> -  `DISABLE_INTERLEAVED_EXECUTION_TVF` 變更為 `INTERLEAVED_EXECUTION_TVF`
> -  `DISABLE_BATCH_MODE_MEMORY_GRANT_FEEDBACK` 變更為 `BATCH_MODE_MEMORY_GRANT_FEEDBACK`
> -  `DISABLE_BATCH_MODE_ADAPTIVE_JOINS` 變更為 `BATCH_MODE_ADAPTIVE_JOINS`

```SQL
-- Syntax for Azure Synapse Analytics

ALTER DATABASE SCOPED CONFIGURATION
{
    SET <set_options>
}
[;]

< set_options > ::=
{
    DW_COMPATIBILITY_LEVEL = { AUTO | 10 | 20 } 
}
```

## <a name="arguments"></a>引數

FOR SECONDARY

指定次要資料庫的設定 (所有次要資料庫都必須具有相同的值)。

CLEAR PROCEDURE_CACHE [plan_handle]

清除資料庫的程序 (計劃) 快取，並且可在主要端和次要端上執行。

指定查詢計劃控制代碼來將單一查詢計劃從計畫快取清除。

**適用於**：指定查詢的計畫控制代碼適用於 Azure SQL Database 和 SQL Server 2019 或更高版本。

MAXDOP **=** {\<value> | PRIMARY } **\<value>**

指定應該用於陳述式的預設 **平行處理原則最大程度 (MAXDOP)** 設定。 0 是預設值，表示將改用伺服器組態。 資料庫範圍的 MAXDOP 會覆寫 (設定為 0 時除外) sp_configure 在伺服器層級設定的 **平行處理原則的最大程度**。 查詢提示仍然可以覆寫資料庫範圍的 MAXDOP 來調整需要不同設定的特定查詢。 所有這些設定都會受到針對[工作負載群組](create-workload-group-transact-sql.md)設定的 MAXDOP 限制。

您可以使用 MAXDOP 選項來限制執行平行計畫所用的處理器數目。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會針對查詢、索引資料定義語言 (DDL) 作業、平行插入、線上改變資料行、平行收集統計資料，以及靜態和索引鍵集驅動資料指標擴展，考慮平行執行計畫。

> [!NOTE]
> **平行處理原則最大程度 (MAXDOP)** 限制的設定會根據 [工作](../../relational-databases/system-dynamic-management-views/sys-dm-os-tasks-transact-sql.md)。 它不是根據[要求](../../relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql.md)或查詢限制。 這表示在平行查詢執行期間，單一要求可能會繁衍指派至[排程器](../../relational-databases/system-dynamic-management-views/sys-dm-os-tasks-transact-sql.md)的多個工作。 如需詳細資訊，請參閱[執行緒與工作架構指南](../../relational-databases/thread-and-task-architecture-guide.md)。 

若要在執行個體層級設定此選項，請參閱[設定 max degree of parallelism 伺服器組態選項](../../database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option.md)。

> [!NOTE]
> 在 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]中，新的單一和彈性集區資料庫的 MAXDOP 資料庫範圍設定預設為 8。 您可以為每個資料庫設定 MAXDOP，如本文中所述。 如需設定 MAXDOP 的最佳建議，請參閱[其他資源](#additional-resources)一節。

> [!TIP]
> 若要在查詢層級完成此操作，請使用 **MAXDOP** [查詢提示](../../t-sql/queries/hints-transact-sql-query.md)。    
> 若要在伺服器層級完成此操作，請使用 **平行處理原則的最大程度 (MAXDOP)** [伺服器組態選項](../../database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option.md)。     
> 若要在工作負載層級完成此操作，請使用 **MAX_DOP** [Resource Governor 工作負載群組組態選項](../../t-sql/statements/create-workload-group-transact-sql.md)。    

PRIMARY

只能在資料庫位於主要端上時，針對次要端進行此設定，並且表示將採用針對主要端設定的組態。 如果主要端的組態發生變更，次要端上的值將會相應地變更，而無須明確地設定次要端值。 **PRIMARY** 是次要端的預設設定。

LEGACY_CARDINALITY_ESTIMATION **=** { ON | **OFF** | PRIMARY }

可讓您將查詢最佳化工具基數估計模型設定為 SQL Server 2012 和更舊版本，而不根據資料庫的相容性層級。 預設值為 **OFF**，這會根據資料庫的相容性層級來設定查詢最佳化工具基數估計模型。 將 LEGACY_CARDINALITY_ESTIMATION 設為 [ON] 相當於啟用[追蹤旗標 9481](../../t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql.md)。

> [!TIP]
> 若要在查詢層級完成此操作，請新增 **QUERYTRACEON** [查詢提示](../../t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql.md)。
> 從 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] SP1 開始，若要在查詢層級完成此操作，請新增 **USE HINT** [查詢提示](../../t-sql/queries/hints-transact-sql-query.md#use_hint)，而不要使用追蹤旗標。

PRIMARY

只有在資料庫位於主要端上時，此值才會在次要端上有效，並且指定所有次要端上的查詢最佳化工具基數估計模型設定將採用針對主要端設定的值。 如果主要端上查詢最佳化工具基數估計模型的組態發生變更，次要端上的值將會相應地變更。 **PRIMARY** 是次要端的預設設定。

PARAMETER_SNIFFING **=** { **ON** | OFF | PRIMARY}

啟用或停用[參數探查](../../relational-databases/query-processing-architecture-guide.md#ParamSniffing)。 預設值是 ON。 將 PARAMETER_SNIFFING 設為 OFF 相當於啟用[追蹤旗標 4136](../../t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql.md)。

> [!TIP]
> 若要在查詢層級完成此操作，請參閱 **OPTIMIZE FOR UNKNOWN** [查詢提示](../../t-sql/queries/hints-transact-sql-query.md)。
> 從 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] SP1 開始，若要在查詢層級完成此操作，也可以使用 **USE HINT** [查詢提示](../../t-sql/queries/hints-transact-sql-query.md#use_hint)。

PRIMARY

只有在資料庫位於主要端上時，此值才會在次要端上有效，並且指定所有次要端上此設定的值將採用針對主要端設定的值。 如果主要端上使用[參數探查](../../relational-databases/query-processing-architecture-guide.md#ParamSniffing)的組態發生變更，次要端上的值將會相應地變更，而無須明確地設定次要端值。 [PRIMARY] 是次要端的預設設定。

<a name="qo_hotfixes"></a> QUERY_OPTIMIZER_HOTFIXES **=** { ON | **OFF** | PRIMARY }

啟用或停用查詢最佳化 Hotfix，而不管資料庫的相容性層級為何。 預設值為 **OFF**，這會停用在針對特定版本導入最高可用相容性層級之後 (RTM 後) 發行的查詢最佳化 Hotfix。 將此值設定為 **ON** 相當於啟用 [追蹤旗標 4199](../../t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql.md)。

**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL16](../../includes/sssql16-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]

> [!TIP]
> 若要在查詢層級完成此操作，請新增 **QUERYTRACEON** [查詢提示](../../t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql.md)。
> 從 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] SP1 開始，若要在查詢層級完成此操作，請新增 USE HINT [查詢提示](../../t-sql/queries/hints-transact-sql-query.md#use_hint)，而不要使用追蹤旗標。

PRIMARY

只有在資料庫位於主要端上時，此值才會在次要端上有效，並且指定所有次要端上此設定的值將採用針對主要端設定的值。 如果主要端的組態發生變更，次要端上的值將會相應地變更，而無須明確地設定次要端值。 [PRIMARY] 是次要端的預設設定。

IDENTITY_CACHE **=** { **ON** | OFF }

**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]

在資料庫層級啟用或停用識別快取。 預設值為 **ON**。 識別快取可用來改善含有識別資料行之資料表上的 INSERT 效能。 若要避免因伺服器意外重新啟動或容錯移轉至次要伺服器，而導致識別資料行值不連貫，請停用 IDENTITY_CACHE 選項。 此選項類似於現有的[追蹤旗標 272](../../t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql.md)差異在於此選項可以在資料庫層級設定，而不僅止於在伺服器層級設定。

> [!NOTE]
> 只能針對 PRIMARY 設定此選項。 如需詳細資訊，請參閱[識別資料行](create-table-transact-sql-identity-property.md)。

INTERLEAVED_EXECUTION_TVF **=** { **ON** | OFF }

**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]

可讓您在資料庫或陳述式範圍啟用或停用多重陳述式資料表值函式的交錯執行，同時仍保有 140 (含) 以上的資料庫相容性層級。 交錯執行是 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 中自適性查詢處理的部分功能。 如需詳細資訊，請參閱[智慧查詢處理](../../relational-databases/performance/intelligent-query-processing.md)。

> [!NOTE]
> 針對資料庫相容性層級 130 或更低，這個資料庫範圍設定沒有任何作用。
>
> 僅限 SQL Server 2017 (14.x)，選項 INTERLEAVED_EXECUTION_TVF 舊稱為 **DISABLE** _INTERLEAVED_EXECUTION_TVF。

BATCH_MODE_MEMORY_GRANT_FEEDBACK **=** { **ON** | OFF}

**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]

可讓您在資料庫範圍啟用或停用批次模式記憶體授與回饋，同時仍保有 140 (含) 以上的資料庫相容性層級。 批次模式記憶體授與回饋是 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 推出的[智慧查詢處理](../../relational-databases/performance/intelligent-query-processing.md)其中一項功能。

> [!NOTE]
> 針對資料庫相容性層級 130 或更低，這個資料庫範圍設定沒有任何作用。

BATCH_MODE_ADAPTIVE_JOINS **=** { **ON** | OFF}

**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]

可讓您在資料庫範圍啟用或停用批次模式自適性聯結，同時仍保有 140 (含) 以上的資料庫相容性層級。 批次模式自適性聯結是 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 推出的[智慧查詢處理](../../relational-databases/performance/intelligent-query-processing.md)其中一項功能。

> [!NOTE]
> 針對資料庫相容性層級 130 或更低，這個資料庫範圍設定沒有任何作用。

TSQL_SCALAR_UDF_INLINING **=** { **ON** | OFF }

**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] (功能目前為公開預覽版)

可讓您在資料庫範圍啟用或停用 T-SQL 純量 UDF 內嵌，同時仍保有 150 (含) 以上的資料庫相容性層級。 T-SQL 純量 UDF 內嵌是[智慧查詢處理](../../relational-databases/performance/intelligent-query-processing.md)功能系列的一部分。

> [!NOTE]
> 針對資料庫相容性層級 140 (含) 以下，這個資料庫範圍設定沒有任何作用。

ELEVATE_ONLINE = { OFF | WHEN_SUPPORTED | FAIL_UNSUPPORTED }

**適用於**：[!INCLUDE[ssSDSFull](../../includes/sssdsfull-md.md)] (功能處於公開預覽階段)

可讓您選取選項，讓引擎自動將支援的作業提升至線上。 預設為 OFF，這表示除非在陳述式中指定，否則不會將作業提升至線上。 [sys.database_scoped_configurations](../../relational-databases/system-catalog-views/sys-database-scoped-configurations-transact-sql.md) 會反映 ELEVATE_ONLINE 目前的值。 這些選項將僅適用於線上支援的作業。

FAIL_UNSUPPORTED

此值會將所有支援的 DLL 作業提升至 ONLINE。 不支援線上執行的作業將會失敗並擲回警告。

WHEN_SUPPORTED

此值會提升支援 ONLINE 的作業。 不支援線上的作業將離線執行。

> [!NOTE]
> 您可以在指定 ONLINE 選項的情形下提交陳述式，進而覆寫預設設定。

ELEVATE_RESUMABLE= { OFF | WHEN_SUPPORTED | FAIL_UNSUPPORTED }

**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] (功能目前為公開預覽版)

可讓您選取選項，讓引擎自動將支援的作業提升至可繼續。 預設為 OFF，這表示除非在陳述式中指定，否則不會將作業提升至可繼續。 [sys.database_scoped_configurations](../../relational-databases/system-catalog-views/sys-database-scoped-configurations-transact-sql.md) 會反映 ELEVATE_RESUMABLE 目前的值。 這些選項僅適用於可繼續支援的作業。

FAIL_UNSUPPORTED

此值會將所有支援的 DLL 作業提升至 RESUMABLE。 不支援可繼續執行的作業會失敗並擲回警告。

WHEN_SUPPORTED

此值會提升支援 RESUMABLE 的作業。 不支援可繼續的作業會在非可繼續的情況下執行。

> [!NOTE]
> 您可以在指定 RESUMABLE 選項的情形下提交陳述式，進而覆寫預設設定。

OPTIMIZE_FOR_AD_HOC_WORKLOADS **=** { ON | **OFF** }

**適用於**：[!INCLUDE[sssdsfull](../../includes/sssdsfull-md.md)]

允許或不允許在第一次編譯批次時，將已編譯的計劃虛設常式儲存在快取中。 預設值為 OFF。 針對資料庫啟用 OPTIMIZE_FOR_AD_HOC_WORKLOADS 資料庫範圍組態之後，在第一次編譯批次時，就會將已編譯的計劃虛設常式儲存在快取中。 與完整的已編譯計劃大小相比，計劃虛設常式的記憶體耗用量較少。 如果再次編譯或執行某個批次，就會移除已編譯的計劃虛設常式，並以完整的已編譯計劃取代。

XTP_PROCEDURE_EXECUTION_STATISTICS **=** { ON | **OFF** }

**適用於**：[!INCLUDE[ssSDSFull](../../includes/sssdsfull-md.md)]

在目前的資料庫上啟用或停用原生編譯 T-SQL 模組的模組層級執行統計資料收集。 預設值為 OFF。 執行統計資料會反映在 [sys.dm_exec_procedure_stats](../../relational-databases/system-dynamic-management-views/sys-dm-exec-procedure-stats-transact-sql.md)。

如果這個選項是 ON，或已透過 [sp_xtp_control_proc_exec_stats](../../relational-databases/system-stored-procedures/sys-sp-xtp-control-proc-exec-stats-transact-sql.md) 啟用統計資料收集，則會收集原生編譯 T-SQL 模組的模組層級執行統計資料。

XTP_QUERY_EXECUTION_STATISTICS **=** { ON | **OFF** }

**適用於**：[!INCLUDE[ssSDSFull](../../includes/sssdsfull-md.md)]

在目前的資料庫上啟用或停用原生編譯 T-SQL 模組的陳述式層級執行統計資料收集。 預設值為 OFF。 執行統計資料會反映在 [sys.dm_exec_query_stats](../../relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql.md) 和[查詢存放區](../../relational-databases/performance/monitoring-performance-by-using-the-query-store.md)。

如果這個選項是 ON，或已透過 [sp_xtp_control_query_exec_stats](../../relational-databases/system-stored-procedures/sys-sp-xtp-control-query-exec-stats-transact-sql.md) 啟用統計資料收集，則會收集原生編譯 T-SQL 模組的陳述式層級執行統計資料。

如需原生編譯 [!INCLUDE[tsql](../../includes/tsql-md.md)] 模組效能監控的詳細資訊，請參閱[監視原生編譯預存程序的效能](../../relational-databases/in-memory-oltp/monitoring-performance-of-natively-compiled-stored-procedures.md)。

ROW_MODE_MEMORY_GRANT_FEEDBACK **=** { **ON** | OFF}

**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] (功能目前為公開預覽版)

可讓您在資料庫範圍啟用或停用資料列模式記憶體授與回饋，同時仍保有 150 (含) 以上的資料庫相容性層級。 資料列模式記憶體授與回饋是 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 推出的[智慧查詢處理](../../relational-databases/performance/intelligent-query-processing.md)其中一項功能 ([!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 支援資料列模式)。

> [!NOTE]
> 針對資料庫相容性層級 140 (含) 以下，這個資料庫範圍設定沒有任何作用。

BATCH_MODE_ON_ROWSTORE **=** { **ON** | OFF}

**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] (功能目前為公開預覽版)

可讓您在資料庫範圍啟用或停用資料列存放區上的批次模式，同時仍保有 150 (含) 以上的資料庫相容性層級。 資料列存放區上的批次模式是[智慧查詢處理](../../relational-databases/performance/intelligent-query-processing.md)功能系列的其中一項功能。

> [!NOTE]
> 針對資料庫相容性層級 140 (含) 以下，這個資料庫範圍設定沒有任何作用。

DEFERRED_COMPILATION_TV **=** { **ON** | OFF}

**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] (功能目前為公開預覽版)

可讓您在資料庫範圍啟用或停用資料表變數延遲編譯，同時仍保有 150 (含) 以上的資料庫相容性層級。 資料表變數延遲編譯是[智慧查詢處理](../../relational-databases/performance/intelligent-query-processing.md)功能系列的其中一項功能。

> [!NOTE]
> 針對資料庫相容性層級 140 (含) 以下，這個資料庫範圍設定沒有任何作用。

ACCELERATED_PLAN_FORCING **=** { **ON** | OFF }

**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 開始)

針對強制執行查詢計劃啟用最佳化機制，適用於所有形式的強制執行計劃，例如[查詢存放區強制執行計劃](../../relational-databases/performance/monitoring-performance-by-using-the-query-store.md#Regressed)、[自動調整](../../relational-databases/automatic-tuning/automatic-tuning.md#automatic-plan-correction)或[使用計劃](../../t-sql/queries/hints-transact-sql-query.md#use-plan)查詢提示。 預設值是 ON。

> [!NOTE]
> 不建議停用加速強制執行計劃。

GLOBAL_TEMPORARY_TABLE_AUTO_DROP **=** { **ON** | OFF }

**適用於**：[!INCLUDE[ssSDSFull](../../includes/sssdsfull-md.md)] (功能處於公開預覽階段)

允許設定[全域暫存資料表](../../t-sql/statements/create-table-transact-sql.md#temporary-tables)的自動卸除功能。 預設值為 [ON]，表示全域暫存資料表會在沒有任何工作階段使用時自動卸除。 當設為 [OFF] 時，將需要使用 DROP TABLE 陳述式，才能明確卸除全域暫存資料表，或是等到伺服器重新啟動時再自動卸除。

- 使用 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 單一資料庫和彈性集區，此選項可以在 SQL Database 伺服器的個別使用者資料庫中進行設定。
- 在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 與 Azure SQL 受控執行個體中，此選項會在 `TempDB` 中設定，而且個別使用者資料庫的設定不會有任何效果。

<a name="lqp"></a>

LIGHTWEIGHT_QUERY_PROFILING **=** { **ON** | OFF}

**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]

可讓您啟用或停用[輕量型查詢分析基礎結構](../../relational-databases/performance/query-profiling-infrastructure.md)。 輕量型查詢分析基礎結構 (LWP) 提供比標準分析機制更具效率的查詢效能資料，預設會予以啟用。

<a name="verbose-truncation"></a>

VERBOSE_TRUNCATION_WARNINGS **=** { **ON** | OFF}

**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]

可讓您啟用或停用新的 `String or binary data would be truncated` 錯誤訊息。 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 針對此案例引進了更加特定的新錯誤訊息 (2628)：

`String or binary data would be truncated in table '%.*ls', column '%.*ls'. Truncated value: '%.*ls'.`

在資料庫相容性層級 150 下設為 ON 時，截斷錯誤會引發新的錯誤訊息 2628 以提供更多內容，並簡化疑難排解程序。

在資料庫相容性層級 150 下設為 OFF 時，截斷錯誤會引發先前的錯誤訊息 8152。

在資料庫相容性層級 140 或更低層級下，錯誤訊息 2628 會保有必須啟用[追蹤旗標](../../t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql.md) 460 的選擇加入錯誤訊息，且此資料庫範圍的組態沒有任何作用。

LAST_QUERY_PLAN_STATS **=** { ON | **OFF**}

**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 開始) (功能目前為公開預覽版)

可讓您啟用或停用 [sys.dm_exec_query_plan_stats](../../relational-databases/system-dynamic-management-views/sys-dm-exec-query-plan-stats-transact-sql.md) 中最後一個查詢計劃統計資料 (相當於實際執行計畫) 的集合。

PAUSED_RESUMABLE_INDEX_ABORT_DURATION_MINUTES

**適用於**：僅 Azure SQL Database 適用

`PAUSED_RESUMABLE_INDEX_ABORT_DURATION_MINUTES` 選項會決定可繼續索引在被引擎自動中止之前，其暫停的時間 (以分鐘為單位)。

- 預設值設定為 1 天 (1440分鐘)
- 最小持續時間設定為 1 分鐘
- 最大持續時間為 71582 分鐘
- 當設定為 0 時，暫停的作業永遠不會自動中止

此選項目前的值會顯示於 [sys.database_scoped_configurations](../../relational-databases/system-catalog-views/sys-database-scoped-configurations-transact-sql.md)。

ISOLATE_SECURITY_POLICY_CARDINALITY **=** { ON | **OFF**}

**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]

可讓您控制[資料列層級安全性](../../relational-databases/security/row-level-security.md) (RLS) 述詞是否會影響整體使用者查詢的執行計畫基數。 當 ISOLATE_SECURITY_POLICY_CARDINALITY 為 ON 時，RLS 述詞不會影響執行計畫的基數。 例如，假設某資料表包含 1 百萬個資料列以及一個 RLS 述詞，此述詞會針對發出查詢的特定使用者將結果限制為 10 個資料列。 將此資料庫範圍的設定設為 OFF 時，此述詞的基數估計值會是 10。 當此資料庫範圍的設定為 ON 時，查詢最佳化會估計 1 百萬個資料列。 建議針對大部分工作負載使用預設值。

DW 相容性層級 **=** { **AUTO** | 10 | 20 }

**適用於**：僅限 Azure Synapse Analytics

將 Transact-SQL 及查詢處理行為，設定為相容於資料庫引擎的指定版本。  設定之後，當對該資料庫執行查詢時，就只會執行相容的功能。  根據預設，資料庫的相容性層級在第一次建立時，會設定為 AUTO。  資料庫即使在暫停/繼續、備份/還原作業之後，也會保留該相容性層級。 

|相容性層級    |   註解|  
|-----------------------|--------------|
|**AUTO**| 預設值。  Synapse Analytics 引擎會自動更新其值。  目前的值為 20。|
|**10**| 在引進相容性層級支援之前，先執行 Transact-SQL 與查詢處理行為。|
|**20**| 第 1 層相容性層級包含受限的 Transact-SQL 與查詢處理行為。 |

ASYNC_STATS_UPDATE_WAIT_AT_LOW_PRIORITY **=** { ON | **OFF**}

**適用於**：僅限 Azure SQL Database (功能目前處於公開預覽階段)

如果已啟用非同步統計資料更新，則啟用這項組態會導致背景要求更新統計資料，以等待對低優先順序佇列進行 Sch-M 鎖定，而避免在高並行處理案例中封鎖其他工作階段。 如需詳細資訊，請參閱 [AUTO_UPDATE_STATISTICS_ASYNC](../../relational-databases/statistics/statistics.md#auto_update_statistics_async)。

## <a name="permissions"></a><a name="Permissions"></a> 權限

資料庫上需要 `ALTER ANY DATABASE SCOPED CONFIGURATION`。 可由在資料庫上具有 CONTROL 權限的使用者來授與此權限。

## <a name="general-remarks"></a>一般備註

雖然您可以設定讓次要資料庫擁有與其主要資料庫不同的範圍組態設定，但所有次要資料庫都會使用相同的組態。 無法為個別次要資料庫設定不同的設定。

執行此陳述式會清除目前資料庫中的程序快取，這意謂著所有查詢都必須重新編譯。

針對有 3 部分名稱的查詢，會採用查詢的目前資料庫連線設定，而不會採用另一個資料庫內容中所編譯 SQL 模組 (例如程序、函式及觸發程序) 的設定，因此會使用其所在資料庫的選項。 同樣地，以非同步方式更新統計資料時，就會採用統計資料所在資料庫的 ASYNC_STATS_UPDATE_WAIT_AT_LOW_PRIORITY 設定。

`ALTER_DATABASE_SCOPED_CONFIGURATION` 事件會以 DDL 事件的形式新增，可用來引發 DDL 觸發程序，且是 `ALTER_DATABASE_EVENTS` 觸發程序群組的子觸發程序。

資料庫範圍組態設定會伴隨資料庫，這表示當還原或附加特定資料庫時，會保留現有的組態設定。

從 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 開始，[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 中已變更部分選項名稱：      
-  `DISABLE_INTERLEAVED_EXECUTION_TVF` 變更為 `INTERLEAVED_EXECUTION_TVF`
-  `DISABLE_BATCH_MODE_MEMORY_GRANT_FEEDBACK` 變更為 `BATCH_MODE_MEMORY_GRANT_FEEDBACK`
-  `DISABLE_BATCH_MODE_ADAPTIVE_JOINS` 變更為 `BATCH_MODE_ADAPTIVE_JOINS`

## <a name="limitations-and-restrictions"></a>限制事項

### <a name="maxdop"></a>MAXDOP

細微的設定可以覆寫全域設定，而資源管理員則可以設定所有其他 MAXDOP 設定的限制。 以下是 MAXDOP 設定的邏輯：

- 查詢提示會覆寫 `sp_configure` 和資料庫範圍設定。 如果已針對工作負載群組設定資源群組 MAXDOP：

  - 如果將查詢提示設定為零 (0)，資源管理員設定就會覆寫該提示。

  - 如果查詢提示不是零 (0)，就會以資源管理員設定作為其限制。

- 除非有查詢提示，否則資料庫範圍設定 (值為零時除外) 會覆寫 `sp_configure` 設定，並以資源管理員設定作為其限制。

- 資源管理員設定會覆寫 `sp_configure` 設定。

### <a name="query_optimizer_hotfixes"></a>QUERY_OPTIMIZER_HOTFIXES

使用 `QUERYTRACEON` 提示來啟用 SQL Server 7.0 到 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 版本的預設查詢最佳化工具或查詢最佳化工具 Hotfix 時，在查詢提示與資料庫範圍組態設定之間會是 OR 條件；也就是說，如果啟用兩者其中之一，就會套用資料庫範圍設定選項。

### <a name="geo-dr"></a>異地災害復原

可讀取次要資料庫 (Always On 可用性群組和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 異地複寫資料庫)，會透過檢查資料庫的狀態來使用次要值。 儘管重新編譯並不會發生在容錯移轉時，而且就技術而言，新的主要資料庫會有使用次要設定的查詢，但主要資料庫與次要資料庫之間的設定只有在工作負載不同時會有差異，因此快取的查詢會使用最佳設定，而新查詢則會挑選適合它們的新設定。

### <a name="dacfx"></a>DacFx

因為 `ALTER DATABASE SCOPED CONFIGURATION` 是 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 和 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 開始) 中會影響資料庫結構描述的新功能，所以匯出的結構描述 (不論有無資料) 不能匯入至較舊的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 版本，例如 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 或 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)]。 例如，從使用此新功能的 [!INCLUDE[ssSDS](../../includes/sssds-md.md)] 或 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 資料庫匯出至 [DACPAC](../../relational-databases/data-tier-applications/data-tier-applications.md) 或 [BACPAC](../../relational-databases/data-tier-applications/data-tier-applications.md#bacpac) 的匯出項目，將無法匯入至舊版伺服器。

### <a name="elevate_online"></a>ELEVATE_ONLINE

此選項僅適用於支援 `WITH (ONLINE = <syntax>)` 的 DDL 陳述式。 不會影響 XML 索引。

### <a name="elevate_resumable"></a>ELEVATE_RESUMABLE

此選項僅適用於支援 `WITH (RESUMABLE = <syntax>)` 的 DDL 陳述式。 不會影響 XML 索引。

## <a name="metadata"></a>中繼資料

[sys.database_scoped_configurations &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-database-scoped-configurations-transact-sql.md) 系統檢視會提供有關資料庫內範圍組態的資訊。 資料庫範圍組態選項只會出現在 sys.database_scoped_configurations 中，因為它們會覆寫伺服器層級預設設定。 [sys.configurations &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-configurations-transact-sql.md) 系統檢視只會顯示伺服器層級設定。

## <a name="examples"></a>範例

下列範例示範如何使用 ALTER DATABASE SCOPED CONFIGURATION

### <a name="a-grant-permission"></a>A. 授與權限

此範例會將執行 ALTER DATABASE SCOPED CONFIGURATION 所需的權限授與使用者 Joe。

```sql
GRANT ALTER ANY DATABASE SCOPED CONFIGURATION to [Joe] ;
```

### <a name="b-set-maxdop"></a>B. 設定 MAXDOP

此範例會在異地複寫案例中，針對主要資料庫設定 MAXDOP = 1，並針對次要資料庫設定 MAXDOP = 4。

```sql
ALTER DATABASE SCOPED CONFIGURATION SET MAXDOP = 1 ;
ALTER DATABASE SCOPED CONFIGURATION FOR SECONDARY SET MAXDOP = 4 ;
```

此範例會在異地複寫案例中，將次要資料庫及其主要資料庫的 MAXDOP 設定為相同。

```sql
ALTER DATABASE SCOPED CONFIGURATION FOR SECONDARY SET MAXDOP = PRIMARY ;
```

### <a name="c-set-legacy_cardinality_estimation"></a>C. 設定 LEGACY_CARDINALITY_ESTIMATION

此範例會在異地複寫案例中，將次要資料庫的 LEGACY_CARDINALITY_ESTIMATION 設定為 ON。

```sql
ALTER DATABASE SCOPED CONFIGURATION FOR SECONDARY SET LEGACY_CARDINALITY_ESTIMATION = ON ;
```

此範例會在異地複寫案例中，將次要資料庫及其主要資料庫的 LEGACY_CARDINALITY_ESTIMATION 設定為相同。

```sql
ALTER DATABASE SCOPED CONFIGURATION FOR SECONDARY SET LEGACY_CARDINALITY_ESTIMATION = PRIMARY ;
```

### <a name="d-set-parameter_sniffing"></a>D. 設定 PARAMETER_SNIFFING

此範例會在異地複寫案例中，將主要資料庫的 PARAMETER_SNIFFING 設定為 OFF。

```sql
ALTER DATABASE SCOPED CONFIGURATION SET PARAMETER_SNIFFING = OFF ;
```

此範例會在異地複寫案例中，將次要資料庫的 PARAMETER_SNIFFING 設定為 OFF。

```sql
ALTER DATABASE SCOPED CONFIGURATION FOR SECONDARY SET PARAMETER_SNIFFING = OFF ;
```

此範例會在異地複寫案例中，將次要資料庫及主要資料庫的 PARAMETER_SNIFFING 設定為相同。

```sql
ALTER DATABASE SCOPED CONFIGURATION FOR SECONDARY SET PARAMETER_SNIFFING = PRIMARY ;
```

### <a name="e-set-query_optimizer_hotfixes"></a>E. 設定 QUERY_OPTIMIZER_HOTFIXES

在異地複寫案例中，將主要資料庫的 QUERY_OPTIMIZER_HOTFIXES 設定為 ON。

```sql
ALTER DATABASE SCOPED CONFIGURATION SET QUERY_OPTIMIZER_HOTFIXES = ON ;
```

### <a name="f-clear-procedure-cache"></a>F. 清除程序快取

此範例會清除程序快取 (可能僅適用於主要資料庫)。

```sql
ALTER DATABASE SCOPED CONFIGURATION CLEAR PROCEDURE_CACHE;
```

### <a name="g-set-identity_cache"></a>G. 設定 IDENTITY_CACHE

**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] (功能目前為公開預覽版)

此範例會停用識別快取。

```sql
ALTER DATABASE SCOPED CONFIGURATION SET IDENTITY_CACHE = OFF ;
```

### <a name="h-set-optimize_for_ad_hoc_workloads"></a>H. 設定 OPTIMIZE_FOR_AD_HOC_WORKLOADS

**適用於**：[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]

此範例會允許在第一次編譯批次時，將已編譯的計劃虛設常式儲存在快取中。

```sql
ALTER DATABASE SCOPED CONFIGURATION SET OPTIMIZE_FOR_AD_HOC_WORKLOADS = ON;
```

### <a name="i-set-elevate_online"></a>I. 設定 ELEVATE_ONLINE

**適用於**：[!INCLUDE[ssSDSFull](../../includes/sssdsfull-md.md)] (功能處於公開預覽階段)

此範例會將 ELEVATE_ONLINE 設定為 FAIL_UNSUPPORTED。

```sql
ALTER DATABASE SCOPED CONFIGURATION SET ELEVATE_ONLINE = FAIL_UNSUPPORTED ;
```

### <a name="j-set-elevate_resumable"></a>J. 設定 ELEVATE_RESUMABLE

**適用於**：[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 和 [!INCLUDE[ssNoVersion](../../includes/sssqlv15-md.md)] (功能處於公開預覽階段)

此範例會將 ELEVEATE_RESUMABLE 設定為 WHEN_SUPPORTED。

```sql
ALTER DATABASE SCOPED CONFIGURATION SET ELEVATE_RESUMABLE = WHEN_SUPPORTED ;
```

### <a name="k-clear-a-query-plan-from-the-plan-cache"></a>K. 從計畫快取清除查詢計劃

**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]

此範例會將特定計劃從程序快取清除

```sql
ALTER DATABASE SCOPED CONFIGURATION CLEAR PROCEDURE_CACHE 0x06000500F443610F003B7CD12C02000001000000000000000000000000000000000000000000000000000000;
```

### <a name="l-set-paused-duration"></a>L. 設定暫停的持續時間

**適用於**：僅 Azure SQL Database 適用

此範例會將可繼續索引的暫停持續時間設定為 60 分鐘。

```sql
ALTER DATABASE SCOPED CONFIGURATION
SET PAUSED_RESUMABLE_INDEX_ABORT_DURATION_MINUTES = 60
```

## <a name="additional-resources"></a>其他資源

### <a name="maxdop-resources"></a>MAXDOP 資源

- [平行處理原則的程度](../../relational-databases/query-processing-architecture-guide.md#DOP)
- [SQL Server 的 "max degree of parallelism" 組態選項的建議和指導方針](https://support.microsoft.com/kb/2806535)

### <a name="legacy_cardinality_estimation-resources"></a>LEGACY_CARDINALITY_ESTIMATION 資源

- [基數估計 (SQL Server)](../../relational-databases/performance/cardinality-estimation-sql-server.md)
- [Optimizing Your Query Plans with the SQL Server 2014 Cardinality Estimator](/previous-versions/dn673537(v=msdn.10)) (使用 SQL Server 2014 基數估算程式最佳化您的查詢計劃)

### <a name="parameter_sniffing-resources"></a>PARAMETER_SNIFFING 資源

- [參數探測](../../relational-databases/query-processing-architecture-guide.md#ParamSniffing)
- [參數的運用](/archive/blogs/queryoptteam/i-smell-a-parameter) \(英文\)

### <a name="query_optimizer_hotfixes-resources"></a>QUERY_OPTIMIZER_HOTFIXES 資源

- [追蹤旗標](../../t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql.md)
- [SQL Server 查詢最佳化工具 Hotfix 追蹤旗標 4199 服務模型](https://support.microsoft.com/kb/974006)

### <a name="elevate_online-resources"></a>ELEVATE_ONLINE 資源

[線上索引作業的指導方針](../../relational-databases/indexes/guidelines-for-online-index-operations.md)

### <a name="elevate_resumable-resources"></a>ELEVATE_RESUMABLE 資源

[線上索引作業的指導方針](../../relational-databases/indexes/guidelines-for-online-index-operations.md)

## <a name="more-information"></a>詳細資訊   
 [sys.database_scoped_configurations](../../relational-databases/system-catalog-views/sys-database-scoped-configurations-transact-sql.md)      
 [SQL Server 中 [平行處理原則最大程度] 設定選項的建議和指導方針](../../database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option.md#Guidelines)      
 [sys.configurations](../../relational-databases/system-catalog-views/sys-configurations-transact-sql.md)    
 [資料庫和檔案目錄檢視](../../relational-databases/system-catalog-views/databases-and-files-catalog-views-transact-sql.md)    
 [伺服器設定選項](../../database-engine/configure-windows/server-configuration-options-sql-server.md)    
 [線上索引作業如何運作](../../relational-databases/indexes/how-online-index-operations-work.md)    
 [線上執行索引作業](../../relational-databases/indexes/perform-index-operations-online.md)    
 [ALTER INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/alter-index-transact-sql.md)    
 [CREATE INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/create-index-transact-sql.md)