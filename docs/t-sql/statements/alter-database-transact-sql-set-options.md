---
title: ALTER DATABASE SET 選項 (Transact-SQL) | Microsoft Docs
description: 深入了解如何在 SQL Server 和 Azure SQL Database 中設定資料庫選項，例如自動調整、加密、查詢存放區。
ms.custom: ''
ms.date: 09/04/2020
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
dev_langs:
- TSQL
helpviewer_keywords:
- online database state [SQL Server]
- database options [SQL Server]
- emergency database state [SQL Server]
- databases [SQL Server], options
- read-only databases
- recovery models [SQL Server], switching
- ALTER DATABASE statement, SET options
- offline database state [SQL Server]
- snapshot isolation framework option
- checksums [SQL Server]
- Automatic tuning
- " Data Retention Policy"
- query plan regression correction
- auto_create_statistics
- auto_update_statistics
- Query Store options
ms.assetid: f76fbd84-df59-4404-806b-8ecb4497c9cc
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: =azuresqldb-current||=azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azure-sqldw-latest||=azuresqldb-mi-current
ms.openlocfilehash: d02a4199775c519602e88573c7fbca72c1e8f9a9
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98170480"
---
# <a name="alter-database-set-options-transact-sql"></a>ALTER DATABASE SET 選項 (Transact-SQL)

設定 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]、[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 和 [!INCLUDE[ssSDW](../../includes/sssdw-md.md)] 中的資料庫選項。 如需其他 ALTER DATABASE 選項，請參閱 [ALTER DATABASE](../../t-sql/statements/alter-database-transact-sql.md)。

選取下列其中一個索引標籤，以查看您所使用特定 SQL 版本的語法、引數、備註、權限和範例。

如需語法慣例的詳細資訊，請參閱 [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)。

> [!NOTE]
> 使用 ALTER DATABASE 來設定一些選項，可能需要獨佔式資料庫存取權。 若 ALTER DATABASE 陳述式未及時完成，請檢查資料庫內是否有其他工作階段封鎖了 ALTER DATABASE 工作階段。

## <a name="select-a-product"></a>選取產品

在下列資料列中，選取您感興趣的產品名稱。 視您所選取的產品而定會在本網頁的這裡顯示不同內容。

::: moniker range=">=sql-server-2016||>=sql-server-linux-2017"

:::row:::
    :::column:::
        **_\* SQL Server \*_** &nbsp;
    :::column-end:::
    :::column:::
        [SQL Database](alter-database-transact-sql-set-options.md?view=azuresqldb-current&preserve-view=true)
    :::column-end:::
    :::column:::
        [SQL 受控執行個體](alter-database-transact-sql-set-options.md?view=azuresqldb-mi-current&preserve-view=true)
    :::column-end:::
    :::column:::
        [Azure Synapse<br />Analytics](alter-database-transact-sql-set-options.md?view=azure-sqldw-latest&preserve-view=true)
    :::column-end:::
:::row-end:::

&nbsp;

## <a name="sql-server"></a>SQL Server

資料庫鏡像、[!INCLUDE[ssHADR](../../includes/sshadr-md.md)] 和相容性層級為 `SET` 選項，但是礙於篇幅的因素，將會在個別的文章中描述。 如需詳細資訊，請參閱 [ALTER DATABASE 資料庫鏡像](../../t-sql/statements/alter-database-transact-sql-database-mirroring.md)、[ALTER DATABASE SET HADR](../../t-sql/statements/alter-database-transact-sql-set-hadr.md) 和 [ALTER DATABASE 相容性層級](../../t-sql/statements/alter-database-transact-sql-compatibility-level.md)。

資料庫範圍設定用來設定數個個別資料庫層級的資料庫設定。 如需詳細資訊，請參閱 [ALTER DATABASE SCOPED CONFIGURATION](../../t-sql/statements/alter-database-scoped-configuration-transact-sql.md)。

> [!NOTE]
> 目前工作階段的許多資料庫 SET 選項都可以使用 [SET 陳述式](../../t-sql/statements/set-statements-transact-sql.md)來設定，且通常是在應用程式連線時由其加以設定。 工作階段層級 SET 選項會覆寫 **ALTER DATABASE SET** 值。 下列章節所述的資料庫選項皆為未明確提供其他 SET 選項值，您可針對工作階段進行設定的值。

## <a name="syntax"></a>語法

```syntaxsql
ALTER DATABASE { database_name | CURRENT }
SET
{
    <option_spec> [ ,...n ] [ WITH <termination> ]
}

<option_spec> ::=
{
    <accelerated_database_recovery>
  | <auto_option>
  | <automatic_tuning_option>
  | <change_tracking_option>
  | <containment_option>
  | <cursor_option>
  | <database_mirroring_option>
  | <date_correlation_optimization_option>
  | <db_encryption_option>
  | <db_state_option>
  | <db_update_option>
  | <db_user_access_option>
  | <delayed_durability_option>
  | <external_access_option>
  | FILESTREAM ( <FILESTREAM_option> )
  | <HADR_options>
  | <mixed_page_allocation_option>
  | <parameterization_option>
  | <query_store_options>
  | <recovery_option>
  | <remote_data_archive_option>
  | <service_broker_option>
  | <snapshot_option>
  | <sql_option>
  | <target_recovery_time_option>
  | <termination>
  | <temporal_history_retention>
  | <data_retention_policy>
}
;

<accelerated_database_recovery> ::=
{
    ACCELERATED_DATABASE_RECOVERY = { ON | OFF }
     [ ( PERSISTENT_VERSION_STORE_FILEGROUP = { filegroup name } ) ];
}

<auto_option> ::=
{
    AUTO_CLOSE { ON | OFF }
  | AUTO_CREATE_STATISTICS { OFF | ON [ ( INCREMENTAL = { ON | OFF } ) ] }
  | AUTO_SHRINK { ON | OFF }
  | AUTO_UPDATE_STATISTICS { ON | OFF }
  | AUTO_UPDATE_STATISTICS_ASYNC { ON | OFF }
}

<automatic_tuning_option> ::=
{
    AUTOMATIC_TUNING ( FORCE_LAST_GOOD_PLAN = { ON | OFF } )
}

<change_tracking_option> ::=
{
    CHANGE_TRACKING
   {
       = OFF
     | = ON [ ( <change_tracking_option_list > [,...n] ) ]
     | ( <change_tracking_option_list> [,...n] )
   }
}

<change_tracking_option_list> ::=
{
   AUTO_CLEANUP = { ON | OFF }
 | CHANGE_RETENTION = retention_period { DAYS | HOURS | MINUTES }
}

<containment_option> ::=
   CONTAINMENT = { NONE | PARTIAL }

<cursor_option> ::=
{
    CURSOR_CLOSE_ON_COMMIT { ON | OFF }
  | CURSOR_DEFAULT { LOCAL | GLOBAL }
}

<database_mirroring_option>
  ALTER DATABASE Database Mirroring

<date_correlation_optimization_option> ::=
    DATE_CORRELATION_OPTIMIZATION { ON | OFF }
  
<db_encryption_option> ::=
    ENCRYPTION { ON | OFF | SUSPEND | RESUME }

<db_state_option> ::=
    { ONLINE | OFFLINE | EMERGENCY }

<db_update_option> ::=
    { READ_ONLY | READ_WRITE }

<db_user_access_option> ::=
    { SINGLE_USER | RESTRICTED_USER | MULTI_USER }

<delayed_durability_option> ::=
    DELAYED_DURABILITY = { DISABLED | ALLOWED | FORCED }

<external_access_option> ::=
{
    DB_CHAINING { ON | OFF }
  | TRUSTWORTHY { ON | OFF }
  | DEFAULT_FULLTEXT_LANGUAGE = { <lcid> | <language name> | <language alias> }
  | DEFAULT_LANGUAGE = { <lcid> | <language name> | <language alias> }
  | NESTED_TRIGGERS = { OFF | ON }
  | TRANSFORM_NOISE_WORDS = { OFF | ON }
  | TWO_DIGIT_YEAR_CUTOFF = { 1753, ..., 2049, ..., 9999 }
}

<FILESTREAM_option> ::=
{
    NON_TRANSACTED_ACCESS = { OFF | READ_ONLY | FULL
  | DIRECTORY_NAME = <directory_name>
}

<HADR_options> ::=
    ALTER DATABASE SET HADR

<mixed_page_allocation_option> ::=
    MIXED_PAGE_ALLOCATION { OFF | ON }

<parameterization_option> ::=
    PARAMETERIZATION { SIMPLE | FORCED }

<query_store_options> ::=
{
    QUERY_STORE
    {
          = OFF [ ( FORCED ) ] 
        | = ON [ ( <query_store_option_list> [,...n] ) ]
        | ( < query_store_option_list> [,...n] )
        | CLEAR [ ALL ]
    }
}

<query_store_option_list> ::=
{
      OPERATION_MODE = { READ_WRITE | READ_ONLY }
    | CLEANUP_POLICY = ( STALE_QUERY_THRESHOLD_DAYS = number )
    | DATA_FLUSH_INTERVAL_SECONDS = number
    | MAX_STORAGE_SIZE_MB = number
    | INTERVAL_LENGTH_MINUTES = number
    | SIZE_BASED_CLEANUP_MODE = { AUTO | OFF }
    | QUERY_CAPTURE_MODE = { ALL | AUTO | CUSTOM | NONE }
    | MAX_PLANS_PER_QUERY = number
    | WAIT_STATS_CAPTURE_MODE = { ON | OFF }
    | QUERY_CAPTURE_POLICY = ( <query_capture_policy_option_list> [,...n] )
}

<query_capture_policy_option_list> :: =
{
      STALE_CAPTURE_POLICY_THRESHOLD = number { DAYS | HOURS }
    | EXECUTION_COUNT = number
    | TOTAL_COMPILE_CPU_TIME_MS = number
    | TOTAL_EXECUTION_CPU_TIME_MS = number
}

<recovery_option> ::=
{
    RECOVERY { FULL | BULK_LOGGED | SIMPLE }
  | TORN_PAGE_DETECTION { ON | OFF }
  | PAGE_VERIFY { CHECKSUM | TORN_PAGE_DETECTION | NONE }
}

<remote_data_archive_option> ::=
{
    REMOTE_DATA_ARCHIVE =
    {
        ON ( SERVER = <server_name> ,
             { 
                  CREDENTIAL = <db_scoped_credential_name>
                  | FEDERATED_SERVICE_ACCOUNT = ON | OFF
             }
        )
        | OFF
    }
}

<service_broker_option> ::=
{
    ENABLE_BROKER
  | DISABLE_BROKER
  | NEW_BROKER
  | ERROR_BROKER_CONVERSATIONS
  | HONOR_BROKER_PRIORITY { ON | OFF}
}

<snapshot_option> ::=
{
    ALLOW_SNAPSHOT_ISOLATION { ON | OFF }
  | READ_COMMITTED_SNAPSHOT { ON | OFF }
  | MEMORY_OPTIMIZED_ELEVATE_TO_SNAPSHOT = { ON | OFF }
}
<sql_option> ::=
{
    ANSI_NULL_DEFAULT { ON | OFF }
  | ANSI_NULLS { ON | OFF }
  | ANSI_PADDING { ON | OFF }
  | ANSI_WARNINGS { ON | OFF }
  | ARITHABORT { ON | OFF }
  | COMPATIBILITY_LEVEL = { 150 | 140 | 130 | 120 | 110 | 100 }
  | CONCAT_NULL_YIELDS_NULL { ON | OFF }
  | NUMERIC_ROUNDABORT { ON | OFF }
  | QUOTED_IDENTIFIER { ON | OFF }
  | RECURSIVE_TRIGGERS { ON | OFF }
}

<target_recovery_time_option> ::=
    TARGET_RECOVERY_TIME = target_recovery_time { SECONDS | MINUTES }

<termination>::=
{
    ROLLBACK AFTER number [ SECONDS ]
  | ROLLBACK IMMEDIATE
  | NO_WAIT
}

<temporal_history_retention> ::=
    TEMPORAL_HISTORY_RETENTION { ON | OFF }

<data_retention_policy> ::=
    DATA_RETENTION { ON | OFF }

```

## <a name="arguments"></a>引數

*database_name*     
要修改之資料庫的名稱。

CURRENT     
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 開始)

會在目前資料庫中執行動作。 所有選項在所有內容中不支援 `CURRENT`。 如果 `CURRENT` 失敗，請提供資料庫名稱。

**\<accelerated_database_recovery> ::=**      
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 開始)

啟用每個資料庫的[加速資料庫復原](../../relational-databases/accelerated-database-recovery-management.md) (ADR)。 依據 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 中的預設，ADR 設定為 OFF。 透過使用此語法，您就有為持續版本存放區 (PVS) 資料指定特定檔案群組的選項。 若沒有指定檔案群組，PVS 將會儲存在 PRIMARY 檔案群組中。 如需範例和詳細資訊，請參閱[加速資料庫復原](../../relational-databases/accelerated-database-recovery-management.md)。

**\<auto_option> ::=**      
控制自動選項。

<a name="auto_close"></a> AUTO_CLOSE { ON | **OFF** }     
開啟     
資料庫會完整關閉，而當最後一位使用者結束之後，便會將它的資源釋放出來。

當使用者試圖重新使用資料庫時，便會自動重新開啟資料庫。 例如，當使用者發出 `USE database_name` 陳述式時，就會發生重新開啟行為。 當 AUTO_CLOSE 設定為 ON 時，資料庫可能會正常關機。 如果是這種情況，則要等到使用者嘗試在下次重新啟動[!INCLUDE[ssDE](../../includes/ssde-md.md)]時使用資料庫之後，才會重新開啟資料庫。

OFF     
在最後一個使用者結束之後，資料庫仍保持開啟狀態。

對於桌面資料庫而言，AUTO_CLOSE 選項非常有用，因為它可讓您將資料庫檔案當做一般檔案來管理。 您可以移動它們、複製它們來建立備份，甚至可以用電子郵件將它們傳給其他使用者。 AUTO_CLOSE 處理序為非同步；重複開啟和關閉資料庫不會降低效能。

> [!NOTE]
> 自主資料庫或 [!INCLUDE[ssSDS](../../includes/sssds-md.md)] 無法使用 AUTO_CLOSE 選項。
> 您可以檢查 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中 `is_auto_close_on` 資料行或 [DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md) 函式 `IsAutoClose` 屬性來判斷這個選項的狀態。
>
> 當 AUTO_CLOSE 設為 ON 時，[sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中某些資料行及 [DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md) 函式會傳回 NULL，因為資料庫無法擷取資料。 若要解決這個問題，請執行 USE 陳述式來開啟資料庫。
>
> 資料庫鏡像需要 AUTO_CLOSE 設為 OFF。

當資料庫設為 `AUTOCLOSE = ON` 時，起始自動資料庫關閉的作業會清除 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體其計畫快取。 清除計畫快取會導致重新編譯所有後續執行計畫，而且可能會導致查詢效能突然暫時下降。 從 [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] Service Pack 2 開始，針對計畫快取中每個已清除的快取存放區，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 錯誤記錄檔會包含下列資訊訊息：`SQL Server has encountered %d occurrence(s) of cachestore flush for the '%s' cachestore (part of plan cache) due to some database maintenance or reconfigure operations`。 只要在該時間間隔內快取發生排清，這個訊息就會每五分鐘記錄一次。

<a name="auto_create_statistics"></a> AUTO_CREATE_STATISTICS { **ON** | OFF }     
開啟     
查詢最佳化工具會視需要針對查詢述詞中的單一資料行建立統計資料，以便改善查詢計劃和查詢效能。 這些單一資料行統計資料是在查詢最佳化工具編譯查詢時所建立。 它只會針對尚未成為現有統計資料物件第一個資料行的資料行建立單一資料行統計資料。

預設值是 ON。 我們建議您針對大部分資料庫使用預設設定。

OFF     
查詢最佳化工具不會在編譯查詢時針對查詢述詞中的單一資料行建立統計資料。 將這個選項設定為 OFF 可能會導致次佳查詢計劃並降低查詢效能。

您可以檢查 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中 `is_auto_create_stats_on` 資料行來判斷這個選項的狀態。 您也可以檢查 [DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md) 函式的 `IsAutoCreateStatistics` 屬性來判斷狀態。

如需詳細資訊，請參閱[統計資料](../../relational-databases/statistics/statistics.md)中的＜使用資料庫範圍統計資料選項＞一節。

INCREMENTAL = ON | **OFF**     
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]

將 AUTO_CREATE_STATISTICS 設定為 ON，並將 INCREMENTAL 設定為 ON。 此設定會在每次支援累加統計資料時，以累加方式自動建立統計資料。 預設值是 OFF。 如需詳細資訊，請參閱 [CREATE STATISTICS](../../t-sql/statements/create-statistics-transact-sql.md)。

<a name="auto_shrink"></a> AUTO_SHRINK { ON | **OFF** }     
開啟     
資料庫檔案是定期壓縮的候選項。

資料檔案和記錄檔都可以自動壓縮。 只有在您將資料庫設定為 SIMPLE 復原模式或備份記錄時，AUTO_SHRINK 才會縮減交易記錄的大小。 當您將 AUTO_SHRINK 設定為 OFF 時，便不會在定期檢查未用空間時，自動壓縮資料庫檔案。

當超出 25% 的檔案包含未用空間時，AUTO_SHRINK 選項便會壓縮檔案。 它會將檔案壓縮成兩種大小的其中一種 (以較大者為準)：

- 25% 的檔案是未用空間大小
- 檔案建立時的大小

您無法壓縮唯讀資料庫。

OFF     
在定期檢查未用空間時，不自動壓縮資料庫檔案。

您可以檢查 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中 `is_auto_shrink_on` 資料行來判斷這個選項的狀態。 您也可以檢查 [DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md) 函式的 `IsAutoShrink` 屬性來判斷狀態。

> [!NOTE]
> 自主資料庫無法使用 AUTO_SHRINK 選項。

<a name="auto_update_statistics"></a> AUTO_UPDATE_STATISTICS { **ON** | OFF }     
開啟     
指定當查詢使用統計資料且它們可能已過期時，查詢最佳化工具就會更新這些統計資料。 當插入、更新、刪除或合併作業變更資料表或索引檢視表中的資料分佈之後，統計資料就會變成過期。 查詢最佳化工具會計算自從上次更新統計資料以來資料修改的次數，並將修改次數與某個閾值比較，藉以判斷統計資料是否可能已經過期。 此臨界值是以資料表或索引檢視表中的資料列數目為基礎。

查詢最佳化工具會在編譯查詢及執行快取查詢計劃之前，檢查是否有過期的統計資料。 查詢最佳化工具會在查詢述詞中使用資料行、資料表和索引檢視表，來判斷哪些統計資料可能已過期。 查詢最佳化工具會在編譯查詢之前判斷這項資訊。 在執行快取查詢計劃之前，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 會確認查詢計劃是否參考最新的統計資料。

AUTO_UPDATE_STATISTICS 選項會套用至針對索引所建立的統計資料、查詢述詞中的單一資料行，以及使用 CREATE STATISTICS 陳述式所建立的統計資料。 此外，這個選項也會套用至篩選的統計資料。

預設值是 ON。 我們建議您針對大部分資料庫使用預設設定。

您可以使用 AUTO_UPDATE_STATISTICS_ASYNC 選項來指定要以同步或非同步方式更新統計資料。

OFF     
指定當查詢使用統計資料時，查詢最佳化工具不會更新這些統計資料。 當統計資料可能已過期時，查詢最佳化工具也不會更新這些統計資料。 將這個選項設定為 OFF 可能會導致次佳查詢計劃並降低查詢效能。

您可以檢查 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中 `is_auto_update_stats_on` 資料行來判斷這個選項的狀態。 您也可以檢查 [DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md) 函式的 `IsAutoUpdateStatistics` 屬性來判斷狀態。

如需詳細資訊，請參閱[統計資料](../../relational-databases/statistics/statistics.md)中的＜使用資料庫範圍統計資料選項＞一節。

<a name="auto_update_statistics_async"></a> AUTO_UPDATE_STATISTICS_ASYNC { ON | **OFF** }     
開啟     
指定 AUTO_UPDATE_STATISTICS 選項的統計資料更新是非同步的。 查詢最佳化工具在編譯查詢之前，不會等候統計資料更新完成。

除非 AUTO_UPDATE_STATISTICS 設為 ON，否則將這個選項設為 ON 沒有作用。

根據預設，AUTO_UPDATE_STATISTICS_ASYNC 選項為 OFF，而查詢最佳化工具會同步更新統計資料。

OFF     
指定 AUTO_UPDATE_STATISTICS 選項的統計資料更新是同步的。 查詢最佳化工具在編譯查詢之前，會先等候統計資料更新完成。

> [!NOTE]
> 除非 AUTO_UPDATE_STATISTICS 設為 ON，否則將這個選項設為 OFF 沒有作用。

您可以檢查 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中 `is_auto_update_stats_async_on` 資料行來判斷這個選項的狀態。

如需描述何時應使用同步或非同步統計資料更新的詳細資訊，請參閱[統計資料](../../relational-databases/statistics/statistics.md#statistics-options)中的＜統計資料選項＞一節。

<a name="auto_tuning"></a> **\<automatic_tuning_option> ::=**      
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] 開始)

啟用或停用 `FORCE_LAST_GOOD_PLAN` [自動調整](../../relational-databases/automatic-tuning/automatic-tuning.md)選項。

FORCE_LAST_GOOD_PLAN = { ON | **OFF** }     
開啟     
[!INCLUDE[ssde_md](../../includes/ssde_md.md)] 會對新查詢計劃將造成效能衰退的 [!INCLUDE[tsql-md](../../includes/tsql-md.md)] 查詢，自動強制執行最後一個已知的良好計劃。 [!INCLUDE[ssde_md](../../includes/ssde_md.md)] 會使用強制方案持續監視 [!INCLUDE[tsql-md](../../includes/tsql-md.md)] 查詢的查詢效能。

如果效能有所提升，[!INCLUDE[ssde_md](../../includes/ssde_md.md)] 會繼續使用最後一個已知的良好計劃。 如果未偵測到效能提升，[!INCLUDE[ssde_md](../../includes/ssde_md.md)] 會產生新的查詢計劃。 如果未啟用查詢存放區，或其不在「讀寫」模式中，陳述式便會失敗。

OFF     
[!INCLUDE[ssde_md](../../includes/ssde_md.md)] 會在 [sys.dm_db_tuning_recommendations](../../relational-databases/system-dynamic-management-views/sys-dm-db-tuning-recommendations-transact-sql.md) 檢視中報告查詢計劃變更所造成可能的查詢效能衰退。 不過，不會自動套用這些建議。 使用者可以套用檢視中顯示的 [!INCLUDE[tsql-md](../../includes/tsql-md.md)] 指令碼，來監視使用中建議並修正已識別的問題。 預設值是 OFF。

**\<change_tracking_option> ::=**      
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 和 [!INCLUDE[ssSDSFull](../../includes/sssds-md.md)]

控制變更追蹤選項。 您可以啟用變更追蹤、設定選項、變更選項，以及停用變更追蹤。 如需範例，請參閱本文稍後的＜範例＞一節。

開啟     
啟用資料庫的變更追蹤。 當您啟用變更追蹤時，也可以設定 AUTO CLEANUP 和 CHANGE RETENTION 選項。

AUTO_CLEANUP = { ON | **OFF** }     
開啟     
在經過了指定的保留週期後，將會自動移除變更追蹤資訊。

OFF     
不會從資料庫中自動移除變更追蹤資料。

CHANGE_RETENTION = *retention_period* { **DAYS** | HOURS | MINUTES }     
指定在資料庫中保存變更追蹤資訊的最小週期。 只有當 AUTO_CLEANUP 值為 ON 時，才會移除資料。

*retention_period* 是一個整數，它會指定保留週期的數值元件。

預設保留期間為 **2 天**。 最小保留週期是 1 分鐘。 預設保留期類型為 **DAYS**。

OFF     
停用資料庫的變更追蹤。 在您停用資料庫的變更追蹤之前，請先在所有資料表上停用變更追蹤。

**\<containment_option> ::=**      
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 開始)

控制資料庫內含項目選項。

CONTAINMENT = { **NONE** | PARTIAL}     
無     
資料庫不是自主資料庫。

PARTIAL     
資料庫是自主資料庫。 如果資料庫已啟用複寫、變更資料擷取或變更追蹤，無法將資料庫內含項目設為部分。 在一次失敗之後錯誤檢查會停止。 如需自主資料庫的詳細資訊，請參閱 [自主資料庫](../../relational-databases/databases/contained-databases.md)。

**\<cursor_option> ::=**     

控制資料指標選項。

CURSOR_CLOSE_ON_COMMIT { ON | OFF }     
開啟     
當您認可或回復交易時，將會關閉任何開啟的資料指標。

OFF     
當認可交易時，資料指標維持開啟狀態；回復交易會關閉任何資料指標，但定義為 INSENSITIVE 或 STATIC 的資料指標除外。

利用 SET 陳述式來設定的連接層級設定會覆寫 CURSOR_CLOSE_ON_COMMIT 的預設資料庫設定。 根據預設，ODBC 和 OLE DB 用戶端會發出連線層級的 SET 陳述式，將工作階段的 CURSOR_CLOSE_ON_COMMIT 設定為 OFF。 當您連線到 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體時，用戶端會執行此陳述式。 如需詳細資訊，請參閱 [SET CURSOR_CLOSE_ON_COMMIT](../../t-sql/statements/set-cursor-close-on-commit-transact-sql.md)。

您可以檢查 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中 `is_cursor_close_on_commit_on` 資料行或 [DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md) 函式 `IsCloseCursorsOnCommitEnabled` 屬性來判斷這個選項的狀態。

CURSOR_DEFAULT { LOCAL | GLOBAL }     
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]

控制資料指標範圍是使用 LOCAL 還是 GLOBAL。

LOCAL     
如果您指定 LOCAL，且未在建立資料指標時將資料指標定義為 GLOBAL，則資料指標的範圍為本機。 具體來說，此範圍是您建立資料指標所在批次、預存程序或觸發程序的本機範圍。 資料指標名稱只在這個範圍內有效。

批次、預存程序或觸發程序內的區域資料指標變數或是預存程序 OUTPUT 參數可以參考資料指標。 當批次、預存程序或觸發程序結束時，會隱含地解除配置資料指標。 除非在 OUTPUT 參數中傳回資料指標，否則會解除配置資料指標。 資料指標可能會在 OUTPUT 參數中傳回。 如果資料指標以此方式傳回，則當最後一個參考資料指標的變數解除配置或離開範圍時，便會解除配置資料指標。

GLOBAL     
如果指定 GLOBAL，且資料指標並未在建立時定義為 LOCAL，則資料指標範圍即為連線的全域範圍。 連接所執行的任何預存程序或批次內都可以參考資料指標名稱。

只有在中斷連接時，才會隱含地取消配置資料指標。 如需詳細資訊，請參閱 [DECLARE CURSOR](../../t-sql/language-elements/declare-cursor-transact-sql.md)。

您可以檢查 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中 `is_local_cursor_default` 資料行來判斷這個選項的狀態。 您也可以檢查 [DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md) 函式的 `IsLocalCursorsDefault` 屬性來判斷狀態。

**\<data_retention_policy> ::=**

**適用於**：「僅限」Azure SQL Edge

DATA_RETENTION { ON | OFF }   
開啟    
在資料庫上啟用以資料保留原則為基礎的清除。

OFF   
在資料庫上停用以資料保留原則為基礎的清除。

**\<database_mirroring>**     
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]

如需引數描述，請參閱 [ALTER DATABASE 資料庫鏡像](../../t-sql/statements/alter-database-transact-sql-database-mirroring.md)。

**\<date_correlation_optimization_option> ::=**      
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]

控制 date_correlation_optimization 選項。

DATE_CORRELATION_OPTIMIZATION { ON | **OFF** }     
開啟     
[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會維護相互關聯統計資料，其中 FOREIGN KEY 條件約束會連結資料庫中的任意兩個資料表，且這些資料表具有 **datetime** 資料行。

OFF     
不維護相互關聯統計資料。

若要將 DATE_CORRELATION_OPTIMIZATION 設定為 ON，除了正在執行 ALTER DATABASE 陳述式的連線外，資料庫都不可以有使用中連線。 之後就可以支援多個連接。

您可以檢查 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中 `is_date_correlation_on` 資料行來判斷此選項的目前設定。

**\<db_encryption_option> ::=**      
控制資料庫加密狀態。

ENCRYPTION { ON \| **OFF** \| SUSPEND \| RESUME }     
開啟     
將資料庫設為加密。

OFF     
將資料庫設為不加密。

SUSPEND     
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 開始)     

可在透明資料加密已啟用或停用之後，或是加密金鑰已變更之後，用來暫停加密掃描。

RESUME     
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 開始)

可用來繼續先前暫停的加密掃描。

如需資料庫加密的詳細資訊，請參閱[透明資料加密](../../relational-databases/security/encryption/transparent-data-encryption.md) 和 [Azure SQL Database 的透明資料加密](/azure/azure-sql/database/transparent-data-encryption-tde-overview)。

在資料庫層級啟用加密時，所有的檔案群組都會加密。 任何新檔案群組都會繼承加密的屬性。 如果資料庫中有任何檔案群組設定為 READ ONLY，則資料庫加密作業將會失敗。

您可以使用 [sys.dm_database_encryption_keys](../../relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql.md) 動態管理檢視來查看資料庫的加密狀態，以及加密掃描的狀態。

**\<db_state_option> ::=**      
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]

控制資料庫的狀態。

OFFLINE     
關閉資料庫，並將它完整關機，再標示為離線。 資料庫在離線狀態時，無法修改。

ONLINE     
資料庫在開啟狀態，可供使用。

EMERGENCY     
資料庫是標示為 READ_ONLY、記錄已停用並限定只有系統管理員 (sysadmin) 固定伺服器角色的成員才可存取。 EMERGENCY 主要是做為疑難排解的用途。 例如，由於記錄檔損毀而被標示有疑問的資料庫可以設定為 EMERGENCY 狀態。 此設定可讓系統管理員進行資料庫的唯讀存取。 只有系統管理員 (sysadmin) 固定伺服器角色的成員，可以將資料庫設定為 EMERGENCY 狀態。

需要主題資料庫的 `ALTER DATABASE` 權限，才能將資料庫變更為離線或緊急狀態，且需要伺服器層級 `ALTER ANY DATABASE` 權限，才能將資料庫從離線移動到線上。

您可以檢查 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中 `state` 和 `state_desc` 資料行來判斷此選項的狀態。 您也可以檢查 [DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md) 函式的 `Status` 屬性來判斷狀態。 如需詳細資訊，請參閱 [Database States](../../relational-databases/databases/database-states.md)。

標示為 RESTORING 的資料庫不能設定為 OFFLINE、ONLINE 或 EMERGENCY。 在使用中的還原作業期間，或是由於備份檔損毀導致資料庫或記錄檔的還原作業失敗時，資料庫都有可能處於 RESTORING 狀態。

**\<db_update_option> ::=**

控制是否允許更新資料庫。

READ_ONLY     
使用者可以從資料庫中讀取資料，但無法加以修改。

> [!NOTE]
> 如果要提高查詢的效能，請先更新統計資料，再將資料庫設為 READ_ONLY。 如果資料庫設為 READ_ONLY 之後，還需要其他的統計資料，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 將會在 tempdb 中建立統計資料。 如需唯讀資料庫統計資料的詳細資訊，請參閱[統計資料](../../relational-databases/statistics/statistics.md)。

READ_WRITE     
資料庫可以執行讀寫作業。

若要變更這個狀態，您必須具有資料庫的獨佔存取權。 如需詳細資訊，請參閱 SINGLE_USER 子句。

> [!NOTE]
> 在 [!INCLUDE[ssazure_md](../../includes/ssazure_md.md)] 同盟資料庫上，已停用 `SET { READ_ONLY | READ_WRITE }`。

**\<db_user_access_option> ::=**      
控制使用者對資料庫的存取權。

SINGLE_USER     
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]

指定每次只能有一位使用者存取資料庫。 如果您指定 SINGLE_USER，且沒有其他使用者連線到資料庫，就會封鎖 ALTER DATABASE 陳述式，直到所有使用者都中斷與指定資料庫的連線為止。 若要覆寫這個行為，請參閱 WITH \<termination> 子句。

資料庫會保留在 SINGLE_USER 模式中，即使當設定選項的使用者已登出也一樣。此時其他使用者可以連接到這個資料庫，但只能有一位。

將資料庫設為 SINGLE_USER 之前，請先確定 AUTO_UPDATE_STATISTICS_ASYNC 選項是否設為 OFF。 當設定為 ON 時，用來更新統計資料的背景執行緒會取得資料庫連線，而您將無法以單一使用者模式存取資料庫。 若要檢視這個選項的狀態，請查詢 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中的 `is_auto_update_stats_async_on` 資料行。 如果選項設為 ON，請執行下列工作：

1. 將 AUTO_UPDATE_STATISTICS_ASYNC 設為 OFF。

2. 查詢 [sys.dm_exec_background_job_queue](../../relational-databases/system-dynamic-management-views/sys-dm-exec-background-job-queue-transact-sql.md) 動態管理檢視，檢查是否有作用中的非同步統計資料作業。

如果有使用中的作業，請等待作業完成，或使用 [KILL STATS JOB](../../t-sql/language-elements/kill-stats-job-transact-sql.md) 手動終止作業。

RESTRICTED_USER     
只允許 `db_owner` 固定資料庫角色與 `dbcreator` 及 `sysadmin` 固定伺服器角色的成員連線至資料庫。 RESTRICTED_USER 不會限制其數目。 使用 ALTER DATABASE 陳述式 termination 子句指定的時間範圍，中斷與資料庫的所有連線。 在資料庫進入 RESTRICTED_USER 狀態之後，不合格使用者的連接嘗試都會遭到拒絕。

MULTI_USER     
允許所有具備適當權限來連接資料庫的使用者。

您可以檢查 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中 `user_access` 資料行來判斷這個選項的狀態。 您也可以檢查 [DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md) 函式的 `UserAccess` 屬性來判斷狀態。

**\<delayed_durability_option> ::=**      
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 開始)

控制交易是否認可完全持久或延遲的持久。

DISABLED     
`SET DISABLED` 之後的所有交易都具有完全持久性。 在不可部分完成的區塊或 Commit 陳述式中設定的任何持久性選項都會被忽略。

ALLOWED     
`SET ALLOWED` 之後的所有交易都具有完全持久性或延遲持久性，視在 ATOMIC 區塊或 Commit 陳述式中設定的持久性選項而定。

FORCED     
`SET FORCED` 之後的所有交易都具有延遲持久性。 在不可部分完成的區塊或 Commit 陳述式中設定的任何持久性選項都會被忽略。

**\<external_access_option> ::=**      
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]

控制外部資源 (如另一個資料庫的物件) 是否能夠存取資料庫。

DB_CHAINING { ON | **OFF** }     
開啟     
資料庫可以是跨資料庫擁有權鏈結的來源或目標。

OFF     
資料庫無法參與跨資料庫擁有權鏈結。

> [!IMPORTANT]
> 當 cross db ownership chaining 伺服器選項為 0 (OFF) 時，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的執行個體可以辨識這項設定。 當 cross db ownership chaining 為 1 (ON) 時，不論這個選項的值為何，所有使用者資料庫都可以參與跨資料庫擁有權鏈結。 您可以使用 [sp_configure](../../relational-databases/system-stored-procedures/sp-configure-transact-sql.md) 來設定這個選項。

若要設定這個選項，則需要有資料庫的 `CONTROL SERVER` 權限。

您無法在 master、model 和 tempdb 系統資料庫上設定 DB_CHAINING 選項。

您可以檢查 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中 `is_db_chaining_on` 資料行來判斷這個選項的狀態。

TRUSTWORTHY { ON | **OFF** }     
開啟     
使用模擬內容的資料庫模組 (如使用者定義函數或預存程序) 能夠在資料庫之外存取資源。

OFF     
模擬內容中的資料庫模組無法存取資料庫之外資源。

每當附加資料庫時，TRUSTWORTHY 都設為 OFF。

依預設，除了 msdb 資料庫以外，所有的系統資料庫都會將 TRUSTWORTHY 設為 OFF。 model 和 tempdb 資料庫的這個值不可變更。 建議您絕對不要將 master 資料庫的 TRUSTWORTHY 選項設為 ON。

若要設定這個選項，則需要有資料庫的 `CONTROL SERVER` 權限。

您可以檢查 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中 `is_trustworthy_on` 資料行來判斷這個選項的狀態。

DEFAULT_FULLTEXT_LANGUAGE     
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 開始)

指定全文檢索索引資料行的預設語言值。

> [!IMPORTANT]
> 只有當 CONTAINMENT 已經設為 PARTIAL 時，才允許這個選項。 如果 CONTAINMENT 設定為 NONE，便會發生錯誤。

DEFAULT_LANGUAGE     
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 開始)

指定所有新建立之登入的預設語言。 語言可藉由提供地區設定識別碼 (LCID)、語言名稱或語言別名來指定。 如需可接受語言名稱和別名的清單，請參閱 [sys.syslanguages](../../relational-databases/system-compatibility-views/sys-syslanguages-transact-sql.md)。 只有當 CONTAINMENT 已經設為 PARTIAL 時，才允許這個選項。 如果 CONTAINMENT 設定為 NONE，便會發生錯誤。

NESTED_TRIGGERS     
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 開始)

指定 AFTER 觸發程序是否可以重疊顯示；亦即，執行起始另一個觸發程序的動作，後者再起始另一個觸發程序等。 只有當 CONTAINMENT 已經設為 PARTIAL 時，才允許這個選項。 如果 CONTAINMENT 設定為 NONE，便會發生錯誤。

TRANSFORM_NOISE_WORDS     
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 開始)

如果非搜尋字或停用字詞造成全文檢索查詢的布林運算失敗，用來隱藏錯誤訊息。 只有當 CONTAINMENT 已經設為 PARTIAL 時，才允許這個選項。 如果 CONTAINMENT 設定為 NONE，便會發生錯誤。

TWO_DIGIT_YEAR_CUTOFF     
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 開始)

從 1753 到 9999 中指定一個整數來表示截止年份，以便將兩位數年份解譯為四位數年份。 只有當 CONTAINMENT 已經設為 PARTIAL 時，才允許這個選項。 如果 CONTAINMENT 設定為 NONE，便會發生錯誤。

**\<FILESTREAM_option> ::=**      
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 開始)

控制 FileTable 的設定。

NON_TRANSACTED_ACCESS = { OFF | READ_ONLY | FULL }     
OFF     
已停用 FileTable 資料的非交易式存取。

READ_ONLY     
非交易式處理序可以讀取此資料庫中 FileTable 的 FILESTREAM 資料。

FULL     
啟用 FileTable 中 FILESTREAM 資料的完整非交易式存取。

DIRECTORY_NAME = *\<directory_name>*      
Windows 相容的目錄名稱。 此名稱在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體的所有資料庫層級目錄名稱之間必須是唯一的。 無論定序設定為何，唯一性比較皆不會區分大小寫。 在此資料庫中建立 FileTable 之前，必須先設定這個選項。

**\<HADR_options> ::=**      
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]

請參閱 [ALTER DATABASE SET HADR](../../t-sql/statements/alter-database-transact-sql-set-hadr.md)。

**\<mixed_page_allocation_option> ::=**      
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 開始)

控制資料庫是否可以針對資料表或索引的前八個頁面使用混合範圍來建立初始頁面。

MIXED_PAGE_ALLOCATION { OFF | **ON** }     
OFF     
資料庫一律使用制式範圍建立初始頁面。 OFF 是預設值。

開啟     
資料庫可以使用混合範圍建立起始頁面。

對於所有系統資料庫，這項設定是 ON。 **tempdb** 是支援 OFF 的唯一系統資料庫。

**\<PARAMETERIZATION_option> ::=**      
控制參數化選項。 如需參數化的詳細資訊，請參閱[查詢處理架構指南](../../relational-databases/query-processing-architecture-guide.md#SimpleParam)。

PARAMETERIZATION { **SIMPLE** | FORCED }     
簡單     
根據資料庫的預設行為，將查詢參數化。

FORCED     
[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會將資料庫中的所有查詢參數化。

您可以檢查 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中 `is_parameterization_forced column` 來判斷此選項的目前設定。

<a name="query-store"></a> **\<query_store_options> ::=**      
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 開始)

ON | **OFF** [ ( FORCED )  ] | CLEAR [ ALL ]     
控制是否在此資料庫中啟用查詢存放區，且控制查詢存放區內容的移除。 如需詳細資訊，請參閱[查詢存放區使用案例](../../relational-databases/performance/query-store-usage-scenarios.md)。

開啟     
啟用查詢存放區。

OFF [ ( FORCED ) ]      
停用查詢存放區。 OFF 是預設值。 FORCED 為選擇性。 FORCED 會中止所有執行中的查詢存放區背景工作，並略過查詢存放區關閉時的同步排清。 導致查詢存放區以最快的速度關機。 FORCED 適用於 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] SP2 CU14、[!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] CU21、[!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] CU6 與更新的組建。

> [!NOTE]  
> 您無法在 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 單一資料庫和彈性集區停用查詢存放區。 執行 `ALTER DATABASE [database] SET QUERY_STORE = OFF` 將會傳回警告 `'QUERY_STORE=OFF' is not supported in this version of SQL Server.`。 

CLEAR [ ALL ]     
從查詢存放區移除查詢相關的資料。 ALL 是選擇性的。 ALL 會從查詢存放區移除查詢相關的資料與中繼資料。

OPERATION_MODE {READ_ONLY |READ_WRITE}     
描述查詢存放區的作業模式。

READ_WRITE     
查詢存放區會收集並保存查詢計劃和執行階段執行統計資料資訊。

READ_ONLY     
可以從查詢存放區讀取資訊，但不會新增資訊。 如果查詢存放區所發出的最大空間已用盡，則查詢存放區會將作業模式變更為 READ_ONLY。

CLEANUP_POLICY     
描述查詢存放區的資料保留原則。 STALE_QUERY_THRESHOLD_DAYS 會決定在查詢存放區中保留查詢資訊的天數。 STALE_QUERY_THRESHOLD_DAYS 的類型為 **bigint**。 預設值是 30。

DATA_FLUSH_INTERVAL_SECONDS     
決定將寫入查詢存放區之資料保存到磁碟的頻率。 為了獲得最佳效能，查詢存放區所收集的資料會以非同步方式寫入磁碟。 此非同步傳輸發生的頻率是使用 DATA_FLUSH_INTERVAL_SECONDS 引數所設定。 DATA_FLUSH_INTERVAL_SECONDS 的類型為 **bigint**。 預設值為 **900** (15 分鐘)。

MAX_STORAGE_SIZE_MB     
決定發給查詢存放區的空間。 MAX_STORAGE_SIZE_MB 的類型為 **bigint**。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 到 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)]) 的預設值為 **100 MB**。 從 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 開始，預設值為 **1 GB**。

> [!NOTE]
> 系統不會嚴格強制執行 `MAX_STORAGE_SIZE_MB` 限制。 只有當查詢存放區將資料寫入磁碟時，系統才會檢查儲存體大小。 這個間隔是由 `DATA_FLUSH_INTERVAL_SECONDS` 選項或是 [!INCLUDE[ssManStudio](../../includes/ssManStudio-md.md)] 查詢存放區對話方塊選項 [資料排清間隔] 所設定。 間隔預設值為 900 秒 (15 分鐘)。
> 如果查詢存放區違反儲存體大小檢查之間的 `MAX_STORAGE_SIZE_MB` 限制，即會轉換為唯讀模式。 如果已啟用 `SIZE_BASED_CLEANUP_MODE`，也會觸發強制執行 `MAX_STORAGE_SIZE_MB` 限制的清除機制。
> 清除了足夠的空間之後，查詢存放區模式就會自動切換回讀寫。

> [!IMPORTANT]
> 如果您認為自己的工作負載擷取將會需要超過 10 GB 的磁碟空間，便應該重新思考並最佳化您的工作負載以重新使用查詢計劃 (例如使用[強制參數化](../../relational-databases/query-processing-architecture-guide.md#ForcedParam)，或是調整查詢存放區設定。    
> 從 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 開始以及在 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 中，您可以將 `QUERY_CAPTURE_MODE` 設定為 CUSTOM 以進一步控制查詢擷取原則。

INTERVAL_LENGTH_MINUTES     
決定執行階段執行統計資料彙總至查詢存放區的時間間隔。 若要將空間使用量最佳化，在執行階段統計資料存放區中的執行階段執行統計資料會透過固定的時段彙總。 這個固定的時段是使用 INTERVAL_LENGTH_MINUTES 引數所設定。 INTERVAL_LENGTH_MINUTES 的類型為 **bigint**。 預設值是 **60** 秒。

SIZE_BASED_CLEANUP_MODE { **AUTO** | OFF }     
控制當總資料量接近大小上限時，是否會自動啟用清除。

AUTO     
當磁碟上大小達到 90% 的 **MAX_STORAGE_SIZE_MB** 時，就會自動啟用以大小為依據的清除。 以大小為依據之清除會先移除成本最高和最舊的查詢。 此作業會在達到 **MAX_STORAGE_SIZE_MB** 的大約 80% 處停止。 此值是預設設定值。

OFF     
不會自動啟用以大小為依據的清除。

SIZE_BASED_CLEANUP_MODE 的類型為 **nvarchar**。

QUERY_CAPTURE_MODE { ALL \| AUTO \| CUSTOM \| NONE }     
指定目前使用中的查詢擷取模式。 每個模式都會定義特定的查詢擷取原則。

> [!NOTE]
> 當查詢擷取模式被設定為 ALL、AUTO 或 CUSTOM 時，一律都會擷取資料指標、預存程序中的查詢，以及原生編譯的查詢。

ALL     
擷取所有的查詢。 **ALL** 是 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 到 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)]) 的預設組態值。

AUTO     
根據執行計數和資源耗用量擷取相關的查詢。 這是 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 的預設組態值。

無     
停止擷取新的查詢。 查詢存放區將會繼續收集已擷取查詢的編譯和執行階段統計資料。 因為您可能會錯過擷取重要查詢，請小心使用此設定。

CUSTOM     
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 開始)

允許對 QUERY_CAPTURE_POLICY 選項的控制。

QUERY_CAPTURE_MODE 的類型為 **nvarchar**。 

max_plans_per_query     
定義為每個查詢維持的計劃最大數目。 MAX_PLANS_PER_QUERY 的類型為 **int**。預設值為 **200**。

WAIT_STATS_CAPTURE_MODE { **ON** | OFF }     
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 開始)

控制是否會針對每個查詢擷取等候統計資料。

開啟    
會擷取每個查詢的等候統計資料資訊。 此值是預設設定值。

OFF    
不會擷取每個查詢的等候統計資料資訊。

**\<query_capture_policy_option_list> :: =**      
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 開始)

控制查詢存放區擷取原則選項。 除了 STALE_CAPTURE_POLICY_THRESHOLD，這些選項會定義必須進行的 OR 條件，以便在定義的過時擷取原則閾值中擷取查詢。

STALE_CAPTURE_POLICY_THRESHOLD = *number* { DAYS | HOURS }     
定義評估間隔週期以判斷是否應該擷取查詢。 預設為 1 天，且可設為 1 小時到七天。 *number* 的類型為 **int**。

EXECUTION_COUNT     
定義在評估週期內執行查詢的次數。 預設為 30，這表示針對過時擷取原則閾值的預設值，查詢必須在一天內至少執行 30 次才能保存在查詢存放區中。 EXECUTION_COUNT 的類型為 **int**。

TOTAL_COMPILE_CPU_TIME_MS     
定義查詢在評估週期內使用的總耗用編譯 CPU 時間。 預設為 1000，這表示針對過時擷取原則閾值的預設值，查詢在一天之中的查詢編譯期間內，必須總共至少有一秒的 CPU 時間，才能保存在查詢存放區中。 TOTAL_COMPILE_CPU_TIME_MS 的類型為 **int**。

TOTAL_EXECUTION_CPU_TIME_MS     
定義查詢在評估週期內使用的總耗用執行 CPU 時間。 預設為 100，這表示針對過時擷取原則閾值的預設值，查詢在一天之中的執行期間內，必須總共至少有 100 毫秒的 CPU 時間，才能保存在查詢存放區中。 TOTAL_EXECUTION_CPU_TIME_MS 的類型為 **int**。

**\<recovery_option> ::=**      
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]

控制資料庫復原選項及磁碟 I/O 錯誤檢查。

FULL     
在媒體失敗之後，利用交易記錄備份來提供完整復原。 如果資料檔案損毀，媒體復原可以還原所有已認可的交易。 如需詳細資訊，請參閱[復原模式](../../relational-databases/backup-restore/recovery-models-sql-server.md)。

BULK_LOGGED     
在媒體失敗之後提供復原。 結合某些大規模或大量作業之最佳效能與最少記錄使用空間的優點。 如需哪些作業只記錄基本資訊的詳細資訊，請參閱[交易記錄](../../relational-databases/logs/the-transaction-log-sql-server.md)。 在 BULK_LOGGED 復原模式之下，這些作業的只會記錄基本資訊。 如需詳細資訊，請參閱[復原模式](../../relational-databases/backup-restore/recovery-models-sql-server.md)。

簡單     
提供使用最少記錄空間的簡單備份策略。 當伺服器失敗復原不再需要記錄空間，會自動重複使用這個記錄空間。 如需詳細資訊，請參閱[復原模式](../../relational-databases/backup-restore/recovery-models-sql-server.md)。

> [!IMPORTANT]
> 簡單復原模式比另兩種模式更容易管理，但在資料檔案損毀時，遺失資料的風險比較大。 在最近的資料庫或差異資料庫備份之後進行的所有變更都會遺失，必須以手動方式重新輸入。

預設復原模式取決於 **model** 資料庫的復原模式。 如需有關選取適當復原模式的詳細資訊，請參閱[復原模式](../../relational-databases/backup-restore/recovery-models-sql-server.md)。

您可以檢查 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中 **recovery_model** 和 **recovery_model_desc** 資料行來判斷這個選項的狀態。 您也可以檢查 [DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md) 函式的 `Recovery` 屬性來判斷狀態。

TORN_PAGE_DETECTION { ON | **OFF** }     
開啟     
[!INCLUDE[ssDE](../../includes/ssde-md.md)] 能夠偵測出不完整的頁面。

OFF     
[!INCLUDE[ssDE](../../includes/ssde-md.md)] 無法偵測出不完整的頁面。

> [!IMPORTANT]
> 在未來的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 版本中，將移除 TORN_PAGE_DETECTION ON | OFF 語法結構。 請避免在新的開發工作中使用這項語法結構，並規劃修改目前使用這項語法結構的應用程式。 請改用 PAGE_VERIFY 選項。

<a name="page_verify"></a> PAGE_VERIFY { **CHECKSUM** | TORN_PAGE_DETECTION | NONE }     
探索 I/O 路徑錯誤所造成的損毀資料庫頁面。 磁碟 I/O 路徑錯誤可能是資料庫損毀問題的原因。 這些錯誤最常是因為頁面寫入磁碟時發生電源故障或磁碟硬體故障所造成。

CHECKSUM     
計算整個頁面內容的總和檢查碼，當頁面寫入磁碟時，將值儲存在頁首。 從磁碟讀取頁面時，總和檢查碼會重新計算並與頁首所儲存的總和檢查碼值做比較。 如果值都不符，便會在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 錯誤記錄檔和 Windows 事件記錄檔中，報告錯誤訊息 824 (表示總和檢查碼失敗)。 總和檢查碼失敗表示 I/O 路徑發生問題。 判斷主要原因時，需要調查硬體、韌體驅動程式、BIOS、篩選驅動程式 (例如，病毒軟體) 和其他 I/O 路徑元件。

TORN_PAGE_DETECTION     
將每個 512 位元組磁區的 2 位元模式儲存在 8 KB 資料庫頁面上，當頁面寫入磁碟時，便將它儲存在資料庫頁首。 當從磁碟中讀取頁面時，會比較頁首中所儲存的損毀位元和實際的頁面磁區資訊。

值不符合表示該頁面只有一部分寫入磁碟中。 在這個狀況下，會在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 錯誤記錄檔和 Windows 事件記錄檔中，報告錯誤訊息 824 (表示發生損毀頁的錯誤)。 如果真的是頁面寫入不完整，通常會由資料庫復原作業來偵測出損毀頁。 不過，其他 I/O 路徑失敗也可能隨時造成損毀頁。

無     
資料庫頁面寫入不會產生 CHECKSUM 或 TORN_PAGE_DETECTION 值。 在讀取期間，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 即使頁首包含 CHECKSUM 或 TORN_PAGE_DETECTION 值，亦不會驗證總和檢查碼或損毀頁。

當您使用 PAGE_VERIFY 選項時，請考慮下列要點：

- 預設為 **CHECKSUM**。
- 當使用者或系統資料庫升級至 [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] 或更新版本時，PAGE_VERIFY 值 (NONE 或 TORN_PAGE_DETECTION) 不會變更。 我們建議您變更為 CHECKSUM。

    > [!NOTE]
    > 在舊版 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中，TempDB 資料庫的 PAGE_VERIFY 資料庫選項設定為 NONE 且無法修改。 從 [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 開始，新安裝之 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的 TempDB 資料庫預設值為 CHECKSUM。 升級 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]安裝時，預設值仍然維持 NONE。 此選項可以進行修改。 我們建議您針對 tempdb 資料庫使用 CHECKSUM。

- TORN_PAGE_DETECTION 可以使用較少資源，但所提供的 CHECKSUM 保護最少。
- 在資料庫不離線、不鎖定，或不妨礙資料庫並行作業的情況下，可以設定 PAGE_VERIFY。
- CHECKSUM 與 TORN_PAGE_DETECTION 互斥。 這兩個選項無法同時啟用。

當偵測到損毀頁或總和檢查碼失敗時，您可以還原資料來加以復原，如果失敗只限於索引頁面，您可以重建索引。 如果您發現總和檢查碼失敗，且要判斷受影響的資料庫頁面類型，請執行 DBCC CHECKDB。 如需還原選項的詳細資訊，請參閱 [RESTORE 引數](../../t-sql/statements/restore-statements-arguments-transact-sql.md)。 雖然還原資料可以解決資料損毀問題，但您仍應診斷主要原因 (如磁碟硬體故障)，並盡快更正，以防止繼續發生錯誤。

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會重試任何因總和檢查碼、損毀頁或其他 I/O 錯誤而失敗的讀取作業四次。 如果任何一次重試讀取成功，都會將訊息寫入至錯誤記錄檔。 並將繼續執行觸發讀取的命令。 如果重試失敗，此命令便會失敗，且會出現錯誤訊息 824。

如需錯誤訊息 823、824 和 825 的詳細資訊，請參閱：

- [如何為 SQL Server 中的錯誤訊息 823 進行疑難排解](https://support.microsoft.com/help/2015755)
- [如何為 SQL Server 中的訊息 824 進行疑難排解](https://support.microsoft.com/help/2015756)
- [如何為 Msg - read retry 進行疑難排解](https://support.microsoft.com/help/2015757) \(英文\)。

您可以檢查 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中的 `page_verify_option` 資料行，或檢查 [DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md) 函式的 `IsTornPageDetectionEnabled` 屬性，以判斷這個選項的目前設定。

**\<remote_data_archive_option> ::=**      
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 開始)

為資料庫啟用或停用 Stretch Database。 如需詳細資訊，請參閱 [Stretch Database](../../sql-server/stretch-database/stretch-database.md)。

REMOTE_DATA_ARCHIVE = { ON ( SERVER = \<server_name> , { CREDENTIAL = \<db_scoped_credential_name> \| FEDERATED_SERVICE_ACCOUNT = ON \| OFF } )\| **OFF**     
開啟     
為資料庫啟用 Stretch Database。 如需詳細資訊，包括其他必要條件，請參閱[為資料庫啟用 Stretch Database](../../sql-server/stretch-database/enable-stretch-database-for-a-database.md)。

需要 `db_owner` 權限才能為資料表啟用 Stretch Database。 需要 `db_owner` 和 `CONTROL DATABASE` 權限才能為資料庫啟用 Stretch Database。

SERVER = \<server_name>     
指定 Azure 伺服器的位址。 包含名稱的 `.database.windows.net` 部分。 例如： `MyStretchDatabaseServer.database.windows.net` 。

CREDENTIAL = \<db_scoped_credential_name>     
指定 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體用來連接到 Azure 伺服器的資料庫範圍認證。 請先確定認證已存在，再執行此命令。 如需詳細資訊，請參閱 [CREATE DATABASE SCOPED CREDENTIAL](../../t-sql/statements/create-database-scoped-credential-transact-sql.md)。

FEDERATED_SERVICE_ACCOUNT = { ON | OFF }     
當下列條件成立時，您可以使用內部部署 SQL Server 的同盟服務帳戶來與遠端 Azure 伺服器通訊。

- 正在執行之 SQL Server 執行個體下的服務帳戶是網域帳戶。
- 網域帳戶所屬的網域，其 Active Directory 與 Azure Active Directory 同盟。
- 遠端 Azure 伺服器已設定支援 Azure Active Directory 驗證。
- 正在執行的 SQL Server 執行個體下服務帳戶必須設定為遠端 Azure 伺服器上 `dbmanager` 或 `sysadmin` 帳戶。

如果您指定同盟服務帳戶為 ON，則您無法也指定 CREDENTIAL 引數。 如果您指定 OFF，請提供 CREDENTIAL 引數。

OFF     
為資料庫停用 Stretch Database。 如需詳細資訊，請參閱 [停用 Stretch Database 並帶回遠端資料](../../sql-server/stretch-database/disable-stretch-database-and-bring-back-remote-data.md)。

只有資料庫不再包含任何已啟用 Stretch Database 的資料表後，您才能為資料庫停用 Stretch Database。 停用 Stretch Database 之後，資料移轉即會停止。 此外，查詢結果不再包含來自遠端資料表的結果。

停用 Stretch 並不會移除遠端資料庫。 若要刪除遠端資料庫，請使用 Azure 入口網站將其卸除。

**\<service_broker_option> ::=**      
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]

控制下列 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 選項：啟用或停用訊息傳遞、設定新的 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 識別碼，或是將交談優先權設定為 ON 或 OFF。

ENABLE_BROKER     
指定啟用指定之資料庫的 [!INCLUDE[ssSB](../../includes/sssb-md.md)]。 啟動訊息傳遞，且 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中的 is_broker_enabled 旗標會設為 true。 資料庫會保留現有的 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 識別碼。 當資料庫是資料庫鏡像設定中的主體時，無法啟用 Service Broker。

> [!NOTE]
> ENABLE_BROKER 需要獨佔式資料庫鎖定。 如果其他工作階段已鎖定資料庫內的資源，ENABLE_BROKER 將會等候到其他工作階段釋放其鎖定為止。 若要在使用者資料庫內啟用 [!INCLUDE[ssSB](../../includes/sssb-md.md)]，請確定在您執行 ALTER DATABASE SET ENABLE_BROKER 陳述式之前，沒有其他工作階段正在使用此資料庫，例如將資料庫置於單一使用者模式。 若要在 msdb 資料庫中啟用 [!INCLUDE[ssSB](../../includes/sssb-md.md)]，請先停止 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent，使 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 可以取得必要的鎖定。

DISABLE_BROKER     
指定針對指定的資料庫停用 [!INCLUDE[ssSB](../../includes/sssb-md.md)]。 停止訊息傳遞，且 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中的 is_broker_enabled 旗標會設為 false。 資料庫會保留現有的 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 識別碼。

NEW_BROKER     
指定資料庫應該接收新的 Broker 識別碼。 資料庫會作為新的 Service Broker。 因此，系統會立即移除資料庫中所有現有的交談，不會產生結束對話訊息。 您必須使用新的識別碼來重新建立參考舊 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 識別碼的任何路由。

ERROR_BROKER_CONVERSATIONS     
指定 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 訊息傳遞已啟用。 此設定會保留資料庫的現有 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 識別碼。 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 會結束資料庫中的所有交談，並產生錯誤。 此設定可讓應用程式執行現有交談的正規清除工作。

HONOR_BROKER_PRIORITY {ON | OFF}     
開啟     
傳送作業會將指派給交談的優先權等級列入考量。 系統會先傳送具高優先權層級交談的訊息，再傳送獲指派低優先權層級交談的訊息。

OFF     
傳送作業的執行方式，就像是所有交談都有預設優先權等級一樣。

HONOR_BROKER_PRIORITY 選項的變更對於沒有等候要傳送之訊息的新對話或對話將會立即生效。 執行 ALTER DATABASE 時具有要傳送訊息之對話要等到對話的某些訊息傳送以後，才會收取新的設定。 所有對話開始使用新設定之前的時間長短可能會有很大的變化。

此屬性的前設定會在 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視的 `is_broker_priority_honored` 資料行中報告。

**\<snapshot_option> ::=**      
計算交易隔離等級。

ALLOW_SNAPSHOT_ISOLATION { ON | **OFF** }     
開啟     
在資料庫層級啟用快照集選項。 啟用時，即使沒有交易使用快照隔離，DML 陳述式也會開始產生資料列版本。 啟用此選項之後，交易可以指定 SNAPSHOT 交易隔離等級。 當交易執行的隔離等級是 SNAPSHOT 時，所有陳述式都會見到在交易開頭便存在的資料快照集。 如果執行 SNAPSHOT 隔離等級的交易存取多個資料庫中的資料，此時所有資料庫中的 ALLOW_SNAPSHOT_ISOLATION 都必須設為 ON，或是每當 FROM 子句參考 ALLOW_SNAPSHOT_ISOLATION 是 OFF 的資料庫中的資料表時，交易中的每個陳述式都必須使用鎖定提示。

OFF     
在資料庫層級關閉快照集選項。 交易無法指定 SNAPSHOT 交易隔離等級。

當您將 ALLOW_SNAPSHOT_ISOLATION 設為新狀態 (從 ON 設成 OFF，或從 OFF 設成 ON) 時，在認可資料庫中的所有現有交易之前，ALTER DATABASE 並不會將控制權傳回呼叫端。 如果資料庫已在 ALTER DATABASE 陳述式所指定的狀態中，控制權會立即傳回呼叫端。 如果 ALTER DATABASE 陳述式並沒有很快傳回，請使用 [sys.dm_tran_active_snapshot_database_transactions](../../relational-databases/system-dynamic-management-views/sys-dm-tran-active-snapshot-database-transactions-transact-sql.md) 來判斷是否有長期執行的交易。 如果取消了 ALTER DATABASE 陳述式，資料庫會保留在 ALTER DATABASE 啟動時的狀態中。 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視指出資料庫中快照集隔離交易的狀態。 如果 **snapshot_isolation_state_desc** = IN_TRANSITION_TO_ON，ALTER DATABASE ALLOW_SNAPSHOT_ISOLATION OFF 會暫停六秒，然後重試作業。

如果資料庫是 OFFLINE，則您無法變更 ALLOW_SNAPSHOT_ISOLATION 的狀態。

如果您在 READ_ONLY 資料庫中設定 ALLOW_SNAPSHOT_ISOLATION，資料庫後來又設為 READ_WRITE，會保留這個設定。

您可以變更 master、model、msdb 和 tempdb 等資料庫的 ALLOW_SNAPSHOT_ISOLATION 設定。 如果您變更 tempdb 的設定，每次停止和重新啟動 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 執行個體時，會保留這個設定。 如果您變更模型的設定，除了 tempdb 以外，任何新建資料庫都會以這個設定為預設值。

根據預設，master 和 msdb 資料庫的這個選項為 ON。

您可以檢查 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中 `snapshot_isolation_state` 資料行來判斷此選項的目前設定。

READ_COMMITTED_SNAPSHOT { ON | OFF }     
開啟     
在資料庫層級啟用讀取認可快照集選項。 啟用時，即使沒有交易使用快照隔離，DML 陳述式也會開始產生資料列版本。 啟用此選項之後，指定讀取認可隔離等級的交易即會使用資料列版本設定，而不是鎖定。 在 READ COMMITTED 隔離等級執行交易時，所有陳述式都會看到資料的快照，與陳述式開始時所存在的資料一樣。

OFF     
在資料庫層級關閉讀取認可快照集選項。 指定 READ COMMITTED 隔離等級的交易會使用鎖定。

若要設定 READ_COMMITTED_SNAPSHOT ON 或 OFF，除了執行 ALTER DATABASE 命令的連接之外，不能有任何使用中資料庫連線。 不過，資料庫不一定要處於單一使用者模式。 當資料庫是 OFFLINE 時，您無法變更這個選項的狀態。

如果您在 READ_ONLY 資料庫中設定 READ_COMMITTED_SNAPSHOT，則當資料庫後來又設為 READ_WRITE 時，會保留這個設定。

master、tempdb 或 msdb 系統資料庫的 READ_COMMITTED_SNAPSHOT 不能設為 ON。 如果您變更 model 的設定，除了 tempdb 以外，這項設定會成為任何新建資料庫的預設值。

您可以檢查 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中 `is_read_committed_snapshot_on` 資料行來判斷此選項的目前設定。

> [!WARNING]
> 使用 **DURABILITY = SCHEMA_ONLY**, 和 **READ_COMMITTED_SNAPSHOT** 建立的資料表，在使用 **ALTER DATABASE** 進行變更時， 資料表中的資料會遺失。

MEMORY_OPTIMIZED_ELEVATE_TO_SNAPSHOT { ON | **OFF** }     
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 開始)

開啟     
當交易隔離等級設定為 SNAPSHOT 以下的任何隔離等級時，記憶體最佳化資料表上所有解譯的 [!INCLUDE[tsql](../../includes/tsql-md.md)] 作業都會在 SNAPSHOT 隔離下執行。 SNAPSHOT 以下的隔離等級範例包括 READ COMMITTED 或 READ UNCOMMITTED。 不論是在工作階段層級明確設定交易隔離等級，或隱含使用預設值，都會執行這些作業。

OFF     
不會為經記憶體最佳化資料表上解譯的 [!INCLUDE[tsql](../../includes/tsql-md.md)] 作業提高交易隔離等級。

如果資料庫是 OFFLINE，則您無法變更 MEMORY_OPTIMIZED_ELEVATE_TO_SNAPSHOT 的狀態。

預設選項為 OFF。

您可以檢查 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中 `is_memory_optimized_elevate_to_snapshot_on` 資料行來判斷此選項的目前設定。

**\<sql_option> ::=**      
控制資料庫層級的 ANSI 合規性選項。

ANSI_NULL_DEFAULT { ON | **OFF** }     
決定未在 CREATE TABLE 或 ALTER TABLE 陳述式中明確定義 Null 屬性之資料行或 [CLR 使用者定義型別](../../relational-databases/clr-integration-database-objects-user-defined-types/clr-user-defined-types.md)的預設值 (NULL 或 NOT NULL)。 條件約束所定義的資料行會遵循條件約束規則，不論這個設定可能為何。

開啟     
未定義資料行的預設值為 NULL。

OFF     
未定義資料行的預設值為 NOT NULL。

利用 SET 陳述式來設定的連接層級設定會覆寫 ANSI_NULL_DEFAULT 的預設資料庫層級設定。 根據預設，ODBC 和 OLE DB 用戶端會發出連線層級的 SET 陳述式，將工作階段的 ANSI_NULL_DEFAULT 設定為 ON。 當您連線到 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體時，用戶端會執行此陳述式。 如需詳細資訊，請參閱 [SET ANSI_NULL_DFLT_ON](../../t-sql/statements/set-ansi-null-dflt-on-transact-sql.md)。

對於 ANSI 相容性而言，將資料庫選項 ANSI_NULL_DEFAULT 設為 ON 會將資料庫預設值改成 NULL。

您可以檢查 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中 `is_ansi_null_default_on` 資料行來判斷這個選項的狀態。 您也可以檢查 [DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md) 函式的 `IsAnsiNullDefault` 屬性來判斷狀態。

ANSI_NULLS { ON | **OFF** }     
開啟     
所有對於 Null 值的比較，都會得出 UNKNOWN。

OFF     
比較非 UNICODE 值和 Null 值，如果兩個值都是 NULL，便會得出 TRUE。

> [!IMPORTANT]
> 在未來的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 版本中，ANSI_NULLS 一律為 ON，而且明確將此選項設定為 OFF 的任何應用程式都會產生錯誤。 請避免在新的開發工作中使用這項功能，並規劃修改目前使用這項功能的應用程式。

利用 SET 陳述式來設定的連接層級設定會覆寫 ANSI_NULLS 的預設資料庫設定。 根據預設，ODBC 和 OLE DB 用戶端會發出連線層級的 SET 陳述式，將工作階段的 ANSI_NULLS 設定為 ON。 當您連線到 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體時，用戶端會執行此陳述式。 如需詳細資訊，請參閱 [SET ANSI_NULLS](../../t-sql/statements/set-ansi-nulls-transact-sql.md)。

> [!IMPORTANT]
> 當您建立或變更計算資料行索引或索引檢視表時，SET ANSI_NULLS 也必須設為 ON。

您可以檢查 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中 `is_ansi_nulls_on` 資料行來判斷這個選項的狀態。 您也可以檢查 [DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md) 函式的 `IsAnsiNullsEnabled` 屬性來判斷狀態。

ANSI_PADDING { ON | **OFF** }     
開啟     
轉換字串前先填補至相同的長度。 也會填補到相同的長度，再插入 **varchar** 或 **nvarchar** 資料類型。

OFF     
**varchar** 或 **nvarchar** 資料行會插入字元值的尾端空格。 已插入 **varbinary** 資料行的二進位值尾端零也會保留。 值不會填補到資料行的長度。

當指定 OFF 時，這個設定只會影響新資料行的定義。

> [!IMPORTANT]
> 在未來的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 版本中，ANSI_PADDING 一律為 ON，而且明確將此選項設定為 OFF 的應用程式將會產生錯誤。 請避免在新的開發工作中使用這項功能，並規劃修改目前使用這項功能的應用程式。 我們建議您一律將 ANSI_PADDING 設為 ON。 當您建立或操作計算資料行索引或索引檢視表時，ANSI_PADDING 也必須是 ON。

當 ANSI_PADDING 設定為 ON 時，允許 Null 的 **char(_n_)** 和 **binary(_n_)** 資料行會填補到資料行長度。 當 ANSI_PADDING 為 OFF 時，則會修剪尾端空格和尾端零。 不允許 Null 的 **char(_n_)** 和 **binary(_n_)** 資料行一律會填補到資料行的長度。

利用 SET 陳述式來設定的連接層級設定會覆寫 ANSI_PADDING 的預設資料庫層級設定。 根據預設，ODBC 和 OLE DB 用戶端會發出連線層級的 SET 陳述式，將工作階段的 ANSI_PADDING 設定為 ON。 當您連線到 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體時，用戶端會執行此陳述式。 如需詳細資訊，請參閱 [SET ANSI_PADDING](../../t-sql/statements/set-ansi-padding-transact-sql.md)。

您可以檢查 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中 `is_ansi_padding_on` 資料行來判斷這個選項的狀態。 您也可以檢查 [DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md) 函式的 `IsAnsiPaddingEnabled` 屬性來判斷狀態。

ANSI_WARNINGS { ON | **OFF** }     
開啟     
如果發生「除以零」之類的狀況，便會發出錯誤或警告。 當彙總函式中出現 Null 值時，也會發出錯誤或警告。

OFF     
當發生除以零之類的情況時，不會產生警告，但會傳回 NULL 值。

> [!IMPORTANT]
> 當您建立或變更計算資料行索引或索引檢視表時，SET ANSI_WARNINGS 必須設為 ON。

利用 SET 陳述式來設定的連接層級設定會覆寫 ANSI_WARNINGS 的預設資料庫設定。 根據預設，ODBC 和 OLE DB 用戶端會發出連線層級的 SET 陳述式，將工作階段的 ANSI_WARNINGS 設定為 ON。 當您連線到 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體時，用戶端會執行此陳述式。 如需詳細資訊，請參閱 [SET ANSI_WARNINGS](../../t-sql/statements/set-ansi-warnings-transact-sql.md)。

您可以檢查 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中 `is_ansi_warnings_on` 資料行來判斷這個選項的狀態。 您也可以檢查 [DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md) 函式的 `IsAnsiWarningsEnabled` 屬性來判斷狀態。

ARITHABORT { ON | OFF }     
開啟     
在查詢執行期間，當發生溢位或除以零的錯誤時，查詢會結束。

OFF     
當發生這些錯誤之一時，畫面上會顯示警告訊息。 即使顯示警告，查詢、批次或交易還是會繼續處理，如同未發生任何錯誤一樣。

> [!IMPORTANT]
> 當您建立或變更計算資料行索引或索引檢視表時，SET ARITHABORT 必須設為 ON。

您可以檢查 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中 `is_arithabort_on` 資料行來判斷這個選項的狀態。 您也可以檢查 [DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md) 函式的 `IsArithmeticAbortEnabled` 屬性來判斷狀態。

COMPATIBILITY_LEVEL = { 150 | 140 | 130 | 120 | 110 | 100 }     

如需詳細資訊，請參閱 [ALTER DATABASE 相容性層級](../../t-sql/statements/alter-database-transact-sql-compatibility-level.md)。

CONCAT_NULL_YIELDS_NULL { ON | **OFF** }     
開啟     
當任何一個運算元是 NULL 時，串連作業的結果都會是 NULL。 例如，串連字元字串 "This is" 和 NULL 會得出 NULL 值，而不是 "This is" 值。

OFF     
將 Null 值當做空的字元字串來處理。

> [重要] 當您建立或變更計算資料行索引或索引檢視表時，CONCAT_NULL_YIELDS_NULL 也必須設為 ON。
>
> 在將來的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 版本中，CONCAT_NULL_YIELDS_NULL 一律會是 ON，且明確將此選項設定為 OFF 的應用程式將會觸發錯誤。 請避免在新的開發工作中使用這項功能，並規劃修改目前使用這項功能的應用程式。

利用 SET 陳述式來設定的連接層級設定會覆寫 CONCAT_NULL_YIELDS_NULL 的預設資料庫設定。 根據預設，當連接到 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體時，ODBC 和 OLE DB 用戶端會發出連接層級的 SET 陳述式，將工作階段的 CONCAT_NULL_YIELDS_NULL 設為 ON。 如需詳細資訊，請參閱 [SET CONCAT_NULL_YIELDS_NULL](../../t-sql/statements/set-concat-null-yields-null-transact-sql.md)。

您可以檢查 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中 `is_concat_null_yields_null_on` 資料行來判斷這個選項的狀態。 您也可以檢查 [DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md) 函式的 `IsNullConcat` 屬性來判斷狀態。

QUOTED_IDENTIFIER { ON | OFF }     
開啟     
可以利用雙引號來含括分隔識別碼。

用雙引號來分隔的所有字串都會解譯為物件識別碼。 附加引號的識別碼不需要遵循 [!INCLUDE[tsql](../../includes/tsql-md.md)] 的識別碼規則。 它們可以是關鍵字，也可以包括 [!INCLUDE[tsql](../../includes/tsql-md.md)] 識別碼不允許的字元。 如果單引號 (') 是文字字串的一部分，您可以用雙引號 (") 來表示它。

OFF     
識別碼不能放在引號中，且必須遵循所有 [!INCLUDE[tsql](../../includes/tsql-md.md)] 識別碼規則。 文字可以用單引號或雙引號來分隔。

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 也允許用方括號 ([ ]) 來分隔識別碼。 不論 QUOTED_IDENTIFIER 設定為何，用方括弧括住的識別項一律可以使用。 如需詳細資訊，請參閱＜ [Database Identifiers](../../relational-databases/databases/database-identifiers.md)＞。

當建立資料表時，一律會在資料表的中繼資料中，將 QUOTED IDENTIFIER 選項儲存成 ON。 即使建立資料表時，將選項設定為 OFF，也會儲存此選項。

利用 SET 陳述式來設定的連接層級設定會覆寫 QUOTED_IDENTIFIER 的預設資料庫設定。 根據預設，ODBC 和 OLE DB 用戶端會發出連線層級的 SET 陳述式，將 QUOTED_IDENTIFIER 設定為 ON。 當您連線到 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體時，用戶端會執行此陳述式。 如需詳細資訊，請參閱 [SET QUOTED_IDENTIFIER](../../t-sql/statements/set-quoted-identifier-transact-sql.md)。

您可以檢查 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中 `is_quoted_identifier_on` 資料行來判斷這個選項的狀態。 您也可以檢查 [DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md) 函式的 `IsQuotedIdentifiersEnabled` 屬性來判斷狀態。

NUMERIC_ROUNDABORT { ON | OFF }     
開啟     
當運算式中遺失有效位數時，系統會產生錯誤。

OFF     
遺失有效位數並不會產生錯誤訊息，且結果會四捨五入到儲存結果的資料行或變數有效位數。

> [!IMPORTANT]
> 當您建立或變更計算資料行索引或索引檢視表時，NUMERIC_ROUNDABORT 必須設為 OFF。

您可以檢查 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中 `is_numeric_roundabort_on` 資料行來判斷這個選項的狀態。 您也可以檢查 [DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md) 函式的 `IsNumericRoundAbortEnabled` 屬性來判斷狀態。

RECURSIVE_TRIGGERS { ON | OFF }     
開啟     
允許遞迴引發 AFTER 觸發程序。

OFF     
您可以檢查 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中 `is_recursive_triggers_on` 資料行來判斷這個選項的狀態。 您也可以檢查 [DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md) 函式的 `IsRecursiveTriggersEnabled` 屬性來判斷狀態。

> [!NOTE]
> 當 RECURSIVE_TRIGGERS 設為 OFF 時，只防止直接遞迴。 若要停用間接遞迴，您也必須將巢狀觸發程序伺服器選項設為 0。

您可以檢查 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中 `is_recursive_triggers_on` 資料行或 [DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md) 函式 `IsRecursiveTriggersEnabled` 屬性來判斷這個選項的狀態。

**\<target_recovery_time_option> ::=**      
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 開始)

為每個資料庫指定間接檢查點的頻率。 從 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 開始，新資料庫的預設值為 **1 分鐘**，這表示資料庫將會使用間接檢查點。 舊版的預設值為 0，這表示資料庫將使用自動檢查點，其頻率取決於伺服器執行個體的復原間隔設定。 對於大多數的系統，[!INCLUDE[msCoName](../../includes/msconame-md.md)] 建議使用 1 分鐘。

TARGET_RECOVERY_TIME **=** *target_recovery_time* { SECONDS | MINUTES }     
*target_recovery_time*     
指定萬一發生損毀時，復原指定之資料庫的時間上限。 *target_recovery_time* 的類型為 **int**。

SECONDS     
指出 *target_recovery_time* 應以秒數表示。

MINUTES     
指出 *target_recovery_time* 應以分鐘數表示。

如需間接檢查點的詳細資訊，請參閱[資料庫檢查點](../../relational-databases/logs/database-checkpoints-sql-server.md)。

**WITH \<termination> ::=**      
指定資料庫狀態轉換時，何時回復不完整的交易。 如果省略 termination 子句，且資料庫有任何鎖定，則 ALTER DATABASE 陳述式會無限等候。 只能指定一個 termination 子句，它在 SET 子句之後。

> [!NOTE]
> 並非所有資料庫選項都會使用 WITH \<termination> 子句。 如需詳細資訊，請參閱本文＜備註＞一節中[＜設定選項＞](#SettingOptions)下的表格。

ROLLBACK AFTER *number* [SECONDS] | ROLLBACK IMMEDIATE     
指定在指定的秒數之後回復，或是立即回復。 *number* 的類型為 **int**。

NO_WAIT     
指定如果要求的資料庫狀態或選項變更無法立即完成，要求將會失敗。 立即完成表示不等候交易自行認可或回復。

## <a name="setting-options"></a><a name="SettingOptions"></a> 設定選項
若要擷取資料庫選項的目前設定，請使用 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視或 [DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md)

設好資料庫選項之後，新設定會立即生效。

您可以針對所有新建立的資料庫，變更任何一個資料庫選項的預設值。 若要執行這項操作，請變更 model 資料庫中的適當資料庫選項。

並非所有資料庫選項都會使用 WITH \<termination> 子句，或可以搭配其他選項來指定。 下表列出這些選項及其選項和終止狀態。

|選項類別目錄|可以搭配其他選項指定|可以使用 WITH \<termination> 子句|
|----------------------|-----------------------------------------|---------------------------------------------|
|\<db_state_option>|是|是|
|\<db_user_access_option>|是|是|
|\<db_update_option>|是|是|
|\<delayed_durability_option>|是|是|
|\<external_access_option>|是|否|
|\<cursor_option>|是|否|
|\<auto_option>|是|否|
|\<sql_option>|是|否|
|\<recovery_option>|是|否|
|\<target_recovery_time_option>|否|是|
|\<database_mirroring_option>|否|否|
|ALLOW_SNAPSHOT_ISOLATION|否|否|
|READ_COMMITTED_SNAPSHOT|否|是|
|MEMORY_OPTIMIZED_ELEVATE_TO_SNAPSHOT|是|是|
|\<service_broker_option>|是|否|
|DATE_CORRELATION_OPTIMIZATION|是|是|
|\<parameterization_option>|是|是|
|\<change_tracking_option>|是|是|
|\<db_encryption_option>|是|否|

設定下列其中一個選項，以清除 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體的計畫快取：

:::row:::
    :::column:::
        OFFLINE
    :::column-end:::
    :::column:::
        ONLINE
    :::column-end:::
    :::column:::
        MODIFY_NAME
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        COLLATE
    :::column-end:::
    :::column:::
        READ_ONLY
    :::column-end:::
    :::column:::
        READ_WRITE
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        MODIFY FILEGROUP DEFAULT
    :::column-end:::
    :::column:::
        MODIFY FILEGROUP READ_WRITE
    :::column-end:::
    :::column:::
        MODIFY FILEGROUP READ_ONLY
    :::column-end:::
:::row-end:::

在下列情況下也會排清計劃快取。

- 資料庫將 AUTO_CLOSE 資料庫選項設定為 ON。 當沒有任何使用者連接參考或使用資料庫時，背景工作嘗試關閉並自動關閉資料庫。
- 您針對有預設選項的資料庫執行幾個查詢。 然後卸除資料庫。
- 卸除來源資料庫的資料庫快照集。
- 您已成功重建資料庫的交易記錄。
- 您還原資料庫備份。
- 您卸離資料庫。

清除計畫快取會導致重新編譯所有後續執行計畫，而且可能會導致查詢效能突然暫時下降。 針對計畫快取中每個已清除的快取存放區，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 錯誤記錄檔會包含下列參考訊息：`SQL Server has encountered %d occurrence(s) of cachestore flush for the '%s' cachestore (part of plan cache) due to some database maintenance or reconfigure operations`。 只要在該時間間隔內快取發生排清，這個訊息就會每五分鐘記錄一次。

## <a name="examples"></a>範例

### <a name="a-setting-options-on-a-database"></a>A. 設定資料庫的選項

下列範例會設定 [!INCLUDE[ssSampleDBobject](../../includes/sssampledbobject-md.md)] 範例資料庫的復原模式和資料頁面驗證選項。

```sql
USE master;
GO
ALTER DATABASE [database_name]
SET RECOVERY FULL PAGE_VERIFY CHECKSUM;
GO

```

### <a name="b-setting-the-database-to-read_only"></a>B. 將資料庫設為 READ_ONLY

將資料庫或檔案群組的狀態改成 READ_ONLY 或 READ_WRITE 時，需要資料庫的獨佔存取權。 下列範例會將資料庫設成 `SINGLE_USER` 模式來取得獨佔存取。 之後，範例會將 [!INCLUDE[ssSampleDBobject](../../includes/sssampledbobject-md.md)] 資料庫的狀態設成 `READ_ONLY` ，並將資料庫的存取權還給所有使用者。

> [!NOTE]
> 這個範例在第一個 `WITH ROLLBACK IMMEDIATE` 陳述式中，使用終止選項 `ALTER DATABASE` 。 所有未完成的交易都會回復，而且 [!INCLUDE[ssSampleDBobject](../../includes/sssampledbobject-md.md)] 資料庫的任何其他連接都會立即中斷。

```sql
USE master;
GO
ALTER DATABASE [database_name]
SET SINGLE_USER
WITH ROLLBACK IMMEDIATE;
GO
ALTER DATABASE [database_name]
SET READ_ONLY
GO
ALTER DATABASE [database_name]
SET MULTI_USER;
GO
```

### <a name="c-enabling-snapshot-isolation-on-a-database"></a>C. 啟用資料庫的快照集隔離

下列範例會啟用 [!INCLUDE[ssSampleDBobject](../../includes/sssampledbobject-md.md)] 資料庫的快照集隔離架構選項。

```sql
USE [database_name];
USE master;
GO
ALTER DATABASE [database_name]
SET ALLOW_SNAPSHOT_ISOLATION ON;
GO
-- Check the state of the snapshot_isolation_framework
-- in the database.
SELECT name, snapshot_isolation_state,
    snapshot_isolation_state_desc AS description
FROM sys.databases
WHERE name = N'[database_name]';
GO

```

結果集顯示啟用快照集隔離架構。

|NAME |snapshot_isolation_state |description|
|-------------------- |------------------------|----------|
|[database_name] |1| 開啟 |

### <a name="d-enabling-modifying-and-disabling-change-tracking"></a>D. 啟用、修改及停用變更追蹤

下列範例會啟用 [!INCLUDE[ssSampleDBobject](../../includes/sssampledbobject-md.md)] 資料庫的變更追蹤，並將保留週期設定為 `2` 天。

```sql
ALTER DATABASE [database_name]
SET CHANGE_TRACKING = ON
(AUTO_CLEANUP = ON, CHANGE_RETENTION = 2 DAYS);
```

下列範例會示範如何將保留週期變更為 `3` 天。

```sql
ALTER DATABASE [database_name]
SET CHANGE_TRACKING (CHANGE_RETENTION = 3 DAYS);
```

下列範例會示範如何停用 [!INCLUDE[ssSampleDBobject](../../includes/sssampledbobject-md.md)] 資料庫的變更追蹤。

```sql
ALTER DATABASE [database_name]
SET CHANGE_TRACKING = OFF;
```

### <a name="e-enabling-the-query-store"></a>E. 啟用查詢存放區

**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 開始)

下列範例會啟用查詢存放區並設定其參數。

```sql
ALTER DATABASE [database_name]
SET QUERY_STORE = ON
    (
      OPERATION_MODE = READ_WRITE,
      CLEANUP_POLICY = ( STALE_QUERY_THRESHOLD_DAYS = 90 ),
      DATA_FLUSH_INTERVAL_SECONDS = 900,
      QUERY_CAPTURE_MODE = AUTO,
      MAX_STORAGE_SIZE_MB = 1024,
      INTERVAL_LENGTH_MINUTES = 60
    );
```

### <a name="f-enabling-the-query-store-with-wait-statistics"></a>F. 啟用包含等候統計資料的查詢存放區

**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 開始)

下列範例會啟用查詢存放區並設定其參數。

```sql
ALTER DATABASE [database_name]
SET QUERY_STORE = ON
    (
      OPERATION_MODE = READ_WRITE,
      CLEANUP_POLICY = ( STALE_QUERY_THRESHOLD_DAYS = 90 ),
      DATA_FLUSH_INTERVAL_SECONDS = 900,
      MAX_STORAGE_SIZE_MB = 1024,
      INTERVAL_LENGTH_MINUTES = 60,
      SIZE_BASED_CLEANUP_MODE = AUTO,
      MAX_PLANS_PER_QUERY = 200,
      WAIT_STATS_CAPTURE_MODE = ON,
    );
```

### <a name="g-enabling-the-query-store-with-custom-capture-policy-options"></a>G. 啟用包含自訂擷取原則選項的查詢存放區

**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 開始)

下列範例會啟用查詢存放區並設定其參數。

```sql
ALTER DATABASE [database_name]
SET QUERY_STORE = ON
    (
      OPERATION_MODE = READ_WRITE,
      CLEANUP_POLICY = ( STALE_QUERY_THRESHOLD_DAYS = 90 ),
      DATA_FLUSH_INTERVAL_SECONDS = 900,
      MAX_STORAGE_SIZE_MB = 1024,
      INTERVAL_LENGTH_MINUTES = 60,
      SIZE_BASED_CLEANUP_MODE = AUTO,
      MAX_PLANS_PER_QUERY = 200,
      WAIT_STATS_CAPTURE_MODE = ON,
      QUERY_CAPTURE_MODE = CUSTOM,
      QUERY_CAPTURE_POLICY = (
        STALE_CAPTURE_POLICY_THRESHOLD = 24 HOURS,
        EXECUTION_COUNT = 30,
        TOTAL_COMPILE_CPU_TIME_MS = 1000,
        TOTAL_EXECUTION_CPU_TIME_MS = 100
      )
    );
```

## <a name="see-also"></a>另請參閱

- [ALTER DATABASE 相容性層級](../../t-sql/statements/alter-database-transact-sql-compatibility-level.md)
- [ALTER DATABASE 資料庫鏡像](../../t-sql/statements/alter-database-transact-sql-database-mirroring.md)
- [ALTER DATABASE SET HADR](../../t-sql/statements/alter-database-transact-sql-set-hadr.md)
- [統計資料](../../relational-databases/statistics/statistics.md)
- [CREATE DATABASE](../../t-sql/statements/create-database-transact-sql.md)
- [啟用和停用變更追蹤](../../relational-databases/track-changes/enable-and-disable-change-tracking-sql-server.md)
- [DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md)
- [DROP DATABASE](../../t-sql/statements/drop-database-transact-sql.md)
- [SET TRANSACTION ISOLATION LEVEL](../../t-sql/statements/set-transaction-isolation-level-transact-sql.md)
- [sp_configure](../../relational-databases/system-stored-procedures/sp-configure-transact-sql.md)
- [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md)
- [sys.data_spaces](../../relational-databases/system-catalog-views/sys-data-spaces-transact-sql.md)
- [使用查詢存放區的最佳作法](../../relational-databases/performance/best-practice-with-the-query-store.md)

::: moniker-end
::: moniker range="=azuresqldb-current"

:::row:::
    :::column:::
        [SQL Server](alter-database-transact-sql-set-options.md?view=sql-server-ver15&preserve-view=true)
    :::column-end:::
    :::column:::
        **_\* SQL Database \*_** &nbsp;
    :::column-end:::
    :::column:::
        [SQL 受控執行個體](alter-database-transact-sql-set-options.md?view=azuresqldb-mi-current&preserve-view=true)
    :::column-end:::
    :::column:::
        [Azure Synapse<br />Analytics](alter-database-transact-sql-set-options.md?view=azure-sqldw-latest&preserve-view=true)
    :::column-end:::
:::row-end:::

&nbsp;

## <a name="sql-database"></a>SQL Database

相容性層級為 `SET` 選項，但在 [ALTER DATABASE 相容性層級](../../t-sql/statements/alter-database-transact-sql-compatibility-level.md)中描述。

> [!NOTE]
> 目前工作階段的許多資料庫 SET 選項都可以使用 [SET 陳述式](../../t-sql/statements/set-statements-transact-sql.md)來設定，而且通常是在應用程式連線時由其加以設定。 工作階段層級 SET 選項會覆寫 **ALTER DATABASE SET** 值。 下列章節所述的資料庫選項皆為未明確提供其他 SET 選項值，因而可針對工作階段進行設定的值。

## <a name="syntax"></a>語法

```syntaxsql
ALTER DATABASE { database_name | Current }
SET
{
    <option_spec> [ ,...n ] [ WITH <termination> ]
}
;

<option_spec> ::=
{
    <auto_option>
  | <automatic_tuning_option>
  | <change_tracking_option>
  | <cursor_option>
  | <db_encryption_option>
  | <db_update_option>
  | <db_user_access_option>
  | <delayed_durability_option>
  | <parameterization_option>
  | <query_store_options>
  | <snapshot_option>
  | <sql_option>
  | <target_recovery_time_option>
  | <termination>
  | <temporal_history_retention>
}
;

<auto_option> ::=
{
    AUTO_CREATE_STATISTICS { OFF | ON [ ( INCREMENTAL = { ON | OFF } ) ] }
  | AUTO_SHRINK { ON | OFF }
  | AUTO_UPDATE_STATISTICS { ON | OFF }
  | AUTO_UPDATE_STATISTICS_ASYNC { ON | OFF }
}

<automatic_tuning_option> ::=
{
    AUTOMATIC_TUNING = { AUTO | INHERIT | CUSTOM }
  | AUTOMATIC_TUNING ( CREATE_INDEX = { DEFAULT | ON | OFF } )
  | AUTOMATIC_TUNING ( DROP_INDEX = { DEFAULT | ON | OFF } )
  | AUTOMATIC_TUNING ( FORCE_LAST_GOOD_PLAN = { DEFAULT | ON | OFF } )
}

<change_tracking_option> ::=
{
    CHANGE_TRACKING
    {
        = OFF
      | = ON [ ( <change_tracking_option_list > [,...n] ) ]
      | ( <change_tracking_option_list> [,...n] )
    }
}

<change_tracking_option_list> ::=
   {
       AUTO_CLEANUP = { ON | OFF }
     | CHANGE_RETENTION = retention_period { DAYS | HOURS | MINUTES }
   }

<cursor_option> ::=
{
    CURSOR_CLOSE_ON_COMMIT { ON | OFF }
}

<db_encryption_option> ::=
  ENCRYPTION { ON | OFF }

<db_update_option> ::=
  { READ_ONLY | READ_WRITE }

<db_user_access_option> ::=
  { RESTRICTED_USER | MULTI_USER }

<delayed_durability_option> ::= DELAYED_DURABILITY = { DISABLED | ALLOWED | FORCED }

<parameterization_option> ::=
  PARAMETERIZATION { SIMPLE | FORCED }

<query_store_options> ::=
{
  QUERY_STORE
  {
      = OFF
    | = ON [ ( <query_store_option_list> [,... n] ) ]
    | ( < query_store_option_list> [,... n] )
    | CLEAR [ ALL ]
  }
}

<query_store_option_list> ::=
{
  OPERATION_MODE = { READ_WRITE | READ_ONLY }
  | CLEANUP_POLICY = ( STALE_QUERY_THRESHOLD_DAYS = number )
  | DATA_FLUSH_INTERVAL_SECONDS = number
  | MAX_STORAGE_SIZE_MB = number
  | INTERVAL_LENGTH_MINUTES = number
  | SIZE_BASED_CLEANUP_MODE = { AUTO | OFF }
  | QUERY_CAPTURE_MODE = { ALL | AUTO | CUSTOM | NONE }
  | MAX_PLANS_PER_QUERY = number
  | WAIT_STATS_CAPTURE_MODE = { ON | OFF }
  | QUERY_CAPTURE_POLICY = ( <query_capture_policy_option_list> [,...n] )
}

<query_capture_policy_option_list> :: =
{
    STALE_CAPTURE_POLICY_THRESHOLD = number { DAYS | HOURS }
    | EXECUTION_COUNT = number
    | TOTAL_COMPILE_CPU_TIME_MS = number
    | TOTAL_EXECUTION_CPU_TIME_MS = number
}

<snapshot_option> ::=
{
    ALLOW_SNAPSHOT_ISOLATION { ON | OFF }
  | READ_COMMITTED_SNAPSHOT {ON | OFF }
  | MEMORY_OPTIMIZED_ELEVATE_TO_SNAPSHOT {ON | OFF }
}
<sql_option> ::=
{
    ANSI_NULL_DEFAULT { ON | OFF }
  | ANSI_NULLS { ON | OFF }
  | ANSI_PADDING { ON | OFF }
  | ANSI_WARNINGS { ON | OFF }
  | ARITHABORT { ON | OFF }
  | COMPATIBILITY_LEVEL = { 150 | 140 | 130 | 120 | 110 | 100 }
  | CONCAT_NULL_YIELDS_NULL { ON | OFF }
  | NUMERIC_ROUNDABORT { ON | OFF }
  | QUOTED_IDENTIFIER { ON | OFF }
  | RECURSIVE_TRIGGERS { ON | OFF }
}

<termination>::=
{
    ROLLBACK AFTER integer [ SECONDS ]
  | ROLLBACK IMMEDIATE
  | NO_WAIT
}

<temporal_history_retention>::=TEMPORAL_HISTORY_RETENTION { ON | OFF }
```

## <a name="arguments"></a>引數

*database_name*     
這是要修改之資料庫的名稱。

CURRENT     
`CURRENT` 會在目前資料庫中執行動作。 所有選項在所有內容中不支援 `CURRENT`。 如果 `CURRENT` 失敗，請提供資料庫名稱。

**\<auto_option> ::=**      
控制自動選項。

<a name="auto_create_statistics"></a> AUTO_CREATE_STATISTICS { ON | OFF }     
開啟     
查詢最佳化工具會視需要針對查詢述詞中的單一資料行建立統計資料，以便改善查詢計劃和查詢效能。 這些單一資料行統計資料是在查詢最佳化工具編譯查詢時所建立。 它只會針對尚未成為現有統計資料物件之第一個資料行的資料行建立單一資料行統計資料。

預設值是 ON。 我們建議您針對大部分資料庫使用預設設定。

OFF     
查詢最佳化工具不會在編譯查詢時針對查詢述詞中的單一資料行建立統計資料。 將這個選項設定為 OFF 可能會導致次佳查詢計劃並降低查詢效能。

您可以檢查 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中 `is_auto_create_stats_on` 資料行來判斷這個選項的狀態。 您也可以檢查 [DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md) 函式的 `IsAutoCreateStatistics` 屬性來判斷狀態。

如需詳細資訊，請參閱[統計資料](../../relational-databases/statistics/statistics.md#statistics-options)中的＜統計資料選項＞一節。

INCREMENTAL = ON | **OFF**     
將 AUTO_CREATE_STATISTICS 設定為 ON，並將 INCREMENTAL 設定為 ON。 此設定會在每次支援累加統計資料時，以累加方式建立自動建立的統計資料。 預設值是 OFF。 如需詳細資訊，請參閱 [CREATE STATISTICS](../../t-sql/statements/create-statistics-transact-sql.md)。

<a name="auto_shrink"></a> AUTO_SHRINK { ON | **OFF** }     
開啟     
資料庫檔案是定期壓縮的候選項。

資料檔案和記錄檔都可以自動壓縮。 只有在您將資料庫設定為 SIMPLE 復原模式或備份記錄時，AUTO_SHRINK 才會縮減交易記錄的大小。 當設定為 OFF 時，便不會在定期檢查未用空間時，自動壓縮資料庫檔案。

當超出 25% 的檔案包含未用空間時，AUTO_SHRINK 選項便會壓縮檔案。 此選項會使檔案壓縮成兩種大小之一。 它會壓縮成兩者中的較大者：

- 25% 的檔案是未使用空間的大小
- 檔案建立時的大小

您無法壓縮唯讀資料庫。

OFF     
在定期檢查未用空間時，不自動壓縮資料庫檔案。

您可以檢查 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中 `is_auto_shrink_on` 資料行來判斷這個選項的狀態。 您也可以檢查 [DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md) 函式的 `IsAutoShrink` 屬性來判斷狀態。

> [!NOTE]
> 自主資料庫無法使用 AUTO_SHRINK 選項。

<a name="auto_update_statistics"></a> AUTO_UPDATE_STATISTICS { **ON** | OFF }     
開啟     
指定當查詢使用統計資料且它們可能已過期時，查詢最佳化工具就會更新這些統計資料。 當插入、更新、刪除或合併作業變更資料表或索引檢視表中的資料分佈之後，統計資料就會變成過期。 查詢最佳化工具會計算自從上次更新統計資料以來資料修改的次數，並將修改次數與某個閾值比較，藉以判斷統計資料是否可能已經過期。 此臨界值是以資料表或索引檢視表中的資料列數目為基礎。

查詢最佳化工具會在編譯查詢及執行快取查詢計劃之前，檢查是否有過期的統計資料。 查詢最佳化工具會在查詢述詞中使用資料行、資料表和索引檢視表，來判斷哪些統計資料可能已過期。 查詢最佳化工具會在編譯查詢之前判斷這項資訊。 在執行快取查詢計劃之前，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 會確認查詢計劃是否參考最新的統計資料。

AUTO_UPDATE_STATISTICS 選項會套用至針對索引所建立的統計資料、查詢述詞中的單一資料行，以及使用 CREATE STATISTICS 陳述式所建立的統計資料。 此外，這個選項也會套用至篩選的統計資料。

預設值是 ON。 我們建議您針對大部分資料庫使用預設設定。

您可以使用 AUTO_UPDATE_STATISTICS_ASYNC 選項來指定要以同步或非同步方式更新統計資料。

OFF     
指定當查詢使用統計資料時，查詢最佳化工具不會更新這些統計資料。 當統計資料可能已過期時，查詢最佳化工具也不會更新這些統計資料。 將這個選項設定為 OFF 可能會導致次佳查詢計劃並降低查詢效能。

您可以檢查 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中 `is_auto_update_stats_on` 資料行來判斷這個選項的狀態。 您也可以檢查 [DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md) 函式的 `IsAutoUpdateStatistics` 屬性來判斷狀態。

如需詳細資訊，請參閱[統計資料](../../relational-databases/statistics/statistics.md#statistics-options)中的＜統計資料選項＞一節。

<a name="auto_update_statistics_async"></a> AUTO_UPDATE_STATISTICS_ASYNC { ON | **OFF** }     
開啟     
指定 AUTO_UPDATE_STATISTICS 選項的統計資料更新是非同步的。 查詢最佳化工具在編譯查詢之前，不會等候統計資料更新完成。

除非 AUTO_UPDATE_STATISTICS 設為 ON，否則將這個選項設為 ON 沒有作用。

預設會將 AUTO_UPDATE_STATISTICS_ASYNC 選項設為 OFF，且查詢最佳化工具會同步更新統計資料。

OFF     
指定 AUTO_UPDATE_STATISTICS 選項的統計資料更新是同步的。 查詢最佳化工具在編譯查詢之前，會先等候統計資料更新完成。

除非 AUTO_UPDATE_STATISTICS 設為 ON，否則將這個選項設為 OFF 沒有作用。

您可以檢查 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中 `is_auto_update_stats_async_on` 資料行來判斷這個選項的狀態。

如需描述何時應使用同步或非同步統計資料更新的詳細資訊，請參閱[統計資料](../../relational-databases/statistics/statistics.md#statistics-options)中的＜統計資料選項＞一節。

<a name="auto_tuning"></a> **\<automatic_tuning_option> ::=**      
**適用於**：[!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)]

控制[自動調整](../../relational-databases/automatic-tuning/automatic-tuning.md)的自動選項。

AUTOMATIC_TUNING = { AUTO | INHERIT | CUSTOM }     
AUTO     
將自動調整值設定為 AUTO，會套用 Azure 設定預設值以進行自動調整。

INHERIT     
使用 INHERIT 值會從父伺服器繼承預設設定。 如果您想在父伺服器自訂自動調整設定，並讓這種伺服器上的所有資料庫都 INHERIT 這些自訂設定，這會特別有用。 請注意，為了讓繼承有效，FORCE_LAST_GOOD_PLAN、CREATE_INDEX 及 DROP_INDEX 這三個個別的調整選項就必須在伺服器上設為 DEFAULT。

CUSTOM     
使用 CUSTOM 值，您就必須手動自訂設定資料庫上可用的各個自動調整選項。

啟用或停用[自動調整](../../relational-databases/automatic-tuning/automatic-tuning.md)的自動索引管理 `CREATE_INDEX` 選項。

CREATE_INDEX = { DEFAULT | ON | OFF }     
DEFAULT     
從伺服器繼承預設設定。 在這種情況下，就會在伺服器層級定義啟用或停用個別自動調整功能的選項。

開啟     
啟用時，會在資料庫自動產生缺少的索引。 在建立索引之後，會驗證工作負載效能的增量。 當這類建立的索引不再對工作負載效能有助益時，會自動還原。 自動建立的索引會加上旗標，表示是系統產生的索引。

OFF     
不會自動產生資料庫上遺漏的索引。

啟用或停用[自動調整](../../relational-databases/automatic-tuning/automatic-tuning.md)的自動索引管理 `DROP_INDEX` 選項。

DROP_INDEX = { DEFAULT | ON | OFF }     
DEFAULT     
從伺服器繼承預設設定。 在這種情況下，就會在伺服器層級定義啟用或停用個別自動調整功能的選項。

開啟     
自動卸除重複索引或不再對效能工作負載有用的索引。

OFF     
不會自動卸除資料庫上遺漏的索引。

啟用或停用[自動調整](../../relational-databases/automatic-tuning/automatic-tuning.md)的自動計劃修正 `FORCE_LAST_GOOD_PLAN` 選項。

FORCE_LAST_GOOD_PLAN = { DEFAULT | ON | OFF }     
DEFAULT     
從伺服器繼承預設設定。 在這種情況下，就會在伺服器層級定義啟用或停用個別自動調整功能的選項。

開啟     
[!INCLUDE[ssde_md](../../includes/ssde_md.md)] 會對新查詢計劃將造成效能衰退的 [!INCLUDE[tsql-md](../../includes/tsql-md.md)] 查詢，自動強制執行最後一個已知的良好計劃。 [!INCLUDE[ssde_md](../../includes/ssde_md.md)] 會使用強制方案持續監視 [!INCLUDE[tsql-md](../../includes/tsql-md.md)] 查詢的查詢效能。 如果效能有所提升，[!INCLUDE[ssde_md](../../includes/ssde_md.md)] 會繼續使用最後一個已知的良好計劃。 如果未偵測到效能提升，[!INCLUDE[ssde_md](../../includes/ssde_md.md)] 會產生新的查詢計劃。 如果未啟用查詢存放區，或其不在「讀寫」模式中，陳述式便會失敗。

OFF     
[!INCLUDE[ssde_md](../../includes/ssde_md.md)] 會在 [sys.dm_db_tuning_recommendations](../../relational-databases/system-dynamic-management-views/sys-dm-db-tuning-recommendations-transact-sql.md) 檢視中報告查詢計劃變更所造成可能的查詢效能衰退。 不過，不會自動套用這些建議。 使用者可以套用檢視中顯示的 [!INCLUDE[tsql-md](../../includes/tsql-md.md)] 指令碼，來監視使用中建議並修正已識別的問題。 這是預設值。

**\<change_tracking_option> ::=**      
控制變更追蹤選項。 您可以啟用變更追蹤、設定選項、變更選項，以及停用變更追蹤。 如需範例，請參閱本文稍後的＜範例＞一節。

開啟     
啟用資料庫的變更追蹤。 當您啟用變更追蹤時，也可以設定 AUTO CLEANUP 和 CHANGE RETENTION 選項。

AUTO_CLEANUP = { ON | OFF }     
開啟     
在經過了指定的保留週期後，將會自動移除變更追蹤資訊。

OFF     
不會從資料庫中移除變更追蹤資料。

CHANGE_RETENTION = *retention_period* { **DAYS** | HOURS | MINUTES }     
指定在資料庫中保存變更追蹤資訊的最小週期。 只有當 AUTO_CLEANUP 值為 ON 時，才會移除資料。

*retention_period* 是一個整數，它會指定保留週期的數值元件。

預設保留期間為 **2 天**。 最小保留週期是 1 分鐘。 預設保留期類型為 **DAYS**。

OFF     
停用資料庫的變更追蹤。 在您停用資料庫的變更追蹤之前，請先在所有資料表上停用變更追蹤。

**\<cursor_option> ::=**      
控制資料指標選項。

CURSOR_CLOSE_ON_COMMIT { ON | OFF }     
開啟     
當您認可或回復交易時，將會關閉任何開啟的資料指標。

OFF     
當認可交易時，資料指標維持開啟狀態；回復交易會關閉任何資料指標，但定義為 INSENSITIVE 或 STATIC 的資料指標除外。

利用 SET 陳述式來設定的連接層級設定會覆寫 CURSOR_CLOSE_ON_COMMIT 的預設資料庫設定。 根據預設，ODBC 和 OLE DB 用戶端會發出連線層級的 SET 陳述式，將工作階段的 CURSOR_CLOSE_ON_COMMIT 設定為 OFF。 當您連線到 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體時，用戶端會執行此陳述式。 如需詳細資訊，請參閱 [SET CURSOR_CLOSE_ON_COMMIT](../../t-sql/statements/set-cursor-close-on-commit-transact-sql.md)。

您可以檢查 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中 `is_cursor_close_on_commit_on` 資料行或 [DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md) 函式 `IsCloseCursorsOnCommitEnabled` 屬性來判斷這個選項的狀態。 只有在中斷連接時，才會隱含地取消配置資料指標。 如需詳細資訊，請參閱 [DECLARE CURSOR](../../t-sql/language-elements/declare-cursor-transact-sql.md)。

**\<db_encryption_option> ::=**      
控制資料庫加密狀態。

ENCRYPTION { ON | OFF }     
設定資料庫要加密 (ON) 或是不要加密 (OFF)。 如需資料庫加密的詳細資訊，請參閱[透明資料加密](../../relational-databases/security/encryption/transparent-data-encryption.md) 和 [Azure SQL Database 的透明資料加密](/azure/azure-sql/database/transparent-data-encryption-tde-overview)。

在資料庫層級啟用加密時，所有的檔案群組都會加密。 任何新檔案群組都會繼承加密的屬性。 如果資料庫中有任何檔案群組設定為 READ ONLY，則資料庫加密作業將會失敗。

您可以使用 [sys.dm_database_encryption_keys](../../relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql.md) 動態管理檢視來查看資料庫的加密狀態。

**\<db_update_option> ::=**      
控制是否允許更新資料庫。

READ_ONLY     
使用者可以從資料庫中讀取資料，但無法加以修改。

> [!NOTE]
> 如果要提高查詢的效能，請先更新統計資料，再將資料庫設為 READ_ONLY。 如果資料庫設為 READ_ONLY 之後，還需要其他的統計資料，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 將會在 tempdb 中建立統計資料。 如需唯讀資料庫統計資料的詳細資訊，請參閱[統計資料](../../relational-databases/statistics/statistics.md)。

READ_WRITE     
資料庫可以執行讀寫作業。

若要變更這個狀態，您必須具有資料庫的獨佔存取權。 如需詳細資訊，請參閱 SINGLE_USER 子句。

> [!NOTE]
> 在 [!INCLUDE[ssazure_md](../../includes/ssazure_md.md)] 同盟資料庫上，已停用 `SET { READ_ONLY | READ_WRITE }`。

**\<db_user_access_option> ::=**      
控制使用者對資料庫的存取權。

RESTRICTED_USER     
只允許 `db_owner` 固定資料庫角色與 `dbcreator` 及 `sysadmin` 固定伺服器角色的成員連線到資料庫，但不限制其數目。 在 ALTER DATABASE 陳述式的 termination 子句所指定的時間範圍中，會中斷資料庫的所有連接。 在資料庫進入 RESTRICTED_USER 狀態之後，不合格使用者的連接嘗試都會遭到拒絕。 **RESTRICTED_USER** 無法使用 SQL 受控執行個體來修改。

MULTI_USER     
允許所有具備適當權限來連接資料庫的使用者。

您可以檢查 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中 `user_access` 資料行或 [DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md) 函式 `UserAccess` 屬性來判斷這個選項的狀態。

**\<delayed_durability_option> ::=**      
控制交易是否認可完全持久或延遲的持久。

DISABLED     
`SET DISABLED` 之後的所有交易都具有完全持久性。 在不可部分完成的區塊或 Commit 陳述式中設定的任何持久性選項都會被忽略。

ALLOWED     
`SET ALLOWED` 之後的所有交易都具有完全持久性或延遲持久性，視在 ATOMIC 區塊或 Commit 陳述式中設定的持久性選項而定。

FORCED     
`SET FORCED` 之後的所有交易都具有延遲持久性。 在不可部分完成的區塊或 Commit 陳述式中設定的任何持久性選項都會被忽略。

**\<PARAMETERIZATION_option> ::=**      
控制參數化選項。

PARAMETERIZATION { **SIMPLE** | FORCED }     
簡單     
根據資料庫的預設行為，將查詢參數化。

FORCED     
[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會將資料庫中的所有查詢參數化。

您可以檢查 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中 `is_parameterization_forced` 資料行來判斷此選項的目前設定。

<a name="query-store"></a> **\<query_store_options> ::=**

ON | OFF | CLEAR [ ALL ]     
控制是否在此資料庫中啟用查詢存放區，且控制查詢存放區內容的移除。

開啟     
啟用查詢存放區。 ON 為預設值。

OFF     
停用查詢存放區。 

> [!NOTE]  
> 您無法在 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 單一資料庫和彈性集區停用查詢存放區。 執行 `ALTER DATABASE [database] SET QUERY_STORE = OFF` 將會傳回警告 `'QUERY_STORE=OFF' is not supported in this version of SQL Server.`。 

CLEAR     
移除查詢存放區的內容。

OPERATION_MODE     
描述查詢存放區的作業模式。 有效值為 READ_ONLY 和 READ_WRITE。 在 READ_WRITE 模式中，查詢存放區會收集並保存查詢計劃和執行階段執行統計資料資訊。 在 READ_ONLY 模式中，可以從查詢存放區讀取資訊，但不會新增資訊。 如果查詢存放區所配置的最大空間已用盡，則查詢存放區會將作業模式變更為 READ_ONLY。

CLEANUP_POLICY     
描述查詢存放區的資料保留原則。 STALE_QUERY_THRESHOLD_DAYS 會決定在查詢存放區中保留查詢資訊的天數。 STALE_QUERY_THRESHOLD_DAYS 的類型為 **bigint**。 預設值是 30。 [!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)] Basic 版的預設值為 **7** 天。

DATA_FLUSH_INTERVAL_SECONDS     
決定將寫入查詢存放區之資料保存到磁碟的頻率。 為了獲得最佳效能，查詢存放區所收集的資料會以非同步方式寫入磁碟。 此非同步傳輸發生的頻率是使用 DATA_FLUSH_INTERVAL_SECONDS 引數所設定。 DATA_FLUSH_INTERVAL_SECONDS 的類型為 **bigint**。 預設值為 **900** (15 分鐘)。

MAX_STORAGE_SIZE_MB     
決定配置給查詢存放區的空間。 MAX_STORAGE_SIZE_MB 的類型為 **bigint**。 [!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)] Premium 版的預設值為 **1 GB**，而 [!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)] Basic 版的預設值為 **10 MB**。

> [!NOTE]
> [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 上的 `MAX_STORAGE_SIZE_MB` 設定限制為 10,240 MB。 

> [!NOTE]
> 系統不會嚴格強制執行 `MAX_STORAGE_SIZE_MB` 限制。 只有當查詢存放區將資料寫入磁碟時，系統才會檢查儲存體大小。 這個間隔是由 `DATA_FLUSH_INTERVAL_SECONDS` 選項或是 [!INCLUDE[ssManStudio](../../includes/ssManStudio-md.md)] 查詢存放區對話方塊選項 [資料排清間隔] 所設定。 間隔預設值為 900 秒 (15 分鐘)。
> 如果查詢存放區違反儲存體大小檢查之間的 `MAX_STORAGE_SIZE_MB` 限制，即會轉換為唯讀模式。 如果已啟用 `SIZE_BASED_CLEANUP_MODE`，也會觸發強制執行 `MAX_STORAGE_SIZE_MB` 限制的清除機制。
> 清除了足夠的空間之後，查詢存放區模式就會自動切換回讀寫。

> [!IMPORTANT]
> 如果您認為自己的工作負載擷取將會需要超過 10 GB 的磁碟空間，便應該重新思考並最佳化您的工作負載以重新使用查詢計劃 (例如使用[強制參數化](../../relational-databases/query-processing-architecture-guide.md#ForcedParam)，或是調整查詢存放區設定。    
> 從 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 開始以及在 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 中，您可以將 `QUERY_CAPTURE_MODE` 設定為 CUSTOM 以進一步控制查詢擷取原則。

INTERVAL_LENGTH_MINUTES     
決定執行階段執行統計資料彙總至查詢存放區的時間間隔。 若要將空間使用量最佳化，在執行階段統計資料存放區中的執行階段執行統計資料會透過固定的時段彙總。 這個固定的時段是使用 INTERVAL_LENGTH_MINUTES 引數所設定。 INTERVAL_LENGTH_MINUTES 的類型為 **bigint**。 預設值是 **60** 秒。

SIZE_BASED_CLEANUP_MODE     
控制當總資料量接近大小上限時，是否將自動啟用清除。

OFF     
不會自動啟用以大小為依據的清除。

AUTO     
當磁碟上的大小達到 90% 的 **max_storage_size_mb** 時，就會自動啟用以大小為依據的清除。 以大小為依據之清除會先移除成本最高和最舊的查詢。 它會在達到 **max_storage_size_mb** 的大約 80% 處停止。 這是預設組態值。

SIZE_BASED_CLEANUP_MODE 的類型為 **nvarchar**。

QUERY_CAPTURE_MODE { ALL \| AUTO \| CUSTOM \| NONE }     
指定目前使用中的查詢擷取模式。 每個模式都會定義特定的查詢擷取原則。   

> [!NOTE]
> 當查詢擷取模式被設定為 ALL、AUTO 或 CUSTOM 時，一律都會擷取資料指標、預存程序中的查詢，以及原生編譯的查詢。

ALL     
擷取所有的查詢。

AUTO     
根據執行計數和資源耗用量擷取相關的查詢。 這是 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 的預設組態值。

無     
停止擷取新的查詢。 查詢存放區將會繼續收集已擷取查詢的編譯和執行階段統計資料。 因為您可能會錯過擷取重要查詢，請小心使用此設定。

CUSTOM     
允許對 QUERY_CAPTURE_POLICY 選項的控制。

QUERY_CAPTURE_MODE 的類型為 **nvarchar**。

max_plans_per_query     
定義為每個查詢維持的計劃最大數目。 MAX_PLANS_PER_QUERY 的類型為 **int**。預設值為 **200**。

WAIT_STATS_CAPTURE_MODE { **ON** | OFF }     
控制是否會針對每個查詢擷取等候統計資料。

開啟    
會擷取每個查詢的等候統計資料資訊。 此值是預設設定值。

OFF    
不會擷取每個查詢的等候統計資料資訊。

**\<query_capture_policy_option_list> :: =**      
控制查詢存放區擷取原則選項。 除了 STALE_CAPTURE_POLICY_THRESHOLD，這些選項會定義必須進行的 OR 條件，以便在定義的過時擷取原則閾值中擷取查詢。

STALE_CAPTURE_POLICY_THRESHOLD = *number* { DAYS | HOURS }     
定義評估間隔週期以判斷是否應該擷取查詢。 預設為 1 天，且可設為 1 小時到七天。 *number* 的類型為 **int**。

EXECUTION_COUNT     
定義在評估週期內執行查詢的次數。 預設為 30，這表示針對過時擷取原則閾值的預設值，查詢必須在一天內至少執行 30 次才能保存在查詢存放區中。 EXECUTION_COUNT 的類型為 **int**。

TOTAL_COMPILE_CPU_TIME_MS     
定義查詢在評估週期內使用的總耗用編譯 CPU 時間。 預設為 1000，這表示針對過時擷取原則閾值的預設值，查詢在一天之中的查詢編譯期間內，必須總共至少有一秒的 CPU 時間，才能保存在查詢存放區中。 TOTAL_COMPILE_CPU_TIME_MS 的類型為 **int**。

TOTAL_EXECUTION_CPU_TIME_MS     
定義查詢在評估週期內使用的總耗用執行 CPU 時間。 預設為 100，這表示針對過時擷取原則閾值的預設值，查詢在一天之中的執行期間內，必須總共至少有 100 毫秒的 CPU 時間，才能保存在查詢存放區中。 TOTAL_EXECUTION_CPU_TIME_MS 的類型為 **int**。

**\<snapshot_option> ::=**      
決定交易隔離等級。

ALLOW_SNAPSHOT_ISOLATION { ON | **OFF** }     
開啟     
在資料庫層級啟用快照集選項。 啟用時，即使沒有交易使用快照隔離，DML 陳述式也會開始產生資料列版本。 啟用此選項之後，交易可以指定 SNAPSHOT 交易隔離等級。 當交易執行的隔離等級是 SNAPSHOT 時，所有陳述式都會見到在交易開頭便存在的資料快照集。 如果執行 SNAPSHOT 隔離等級的交易存取多個資料庫中的資料，此時所有資料庫中的 ALLOW_SNAPSHOT_ISOLATION 都必須設為 ON，或是每當 FROM 子句參考 ALLOW_SNAPSHOT_ISOLATION 是 OFF 的資料庫中的資料表時，交易中的每個陳述式都必須使用鎖定提示。

OFF     
在資料庫層級關閉快照集選項。 交易無法指定 SNAPSHOT 交易隔離等級。

當您將 ALLOW_SNAPSHOT_ISOLATION 設為新狀態 (從 ON 設成 OFF，或從 OFF 設成 ON) 時，在認可資料庫中的所有現有交易之前，ALTER DATABASE 並不會將控制權傳回呼叫端。 如果資料庫已在 ALTER DATABASE 陳述式所指定的狀態中，控制權會立即傳回呼叫端。 如果 ALTER DATABASE 陳述式並沒有很快傳回，請使用 [sys.dm_tran_active_snapshot_database_transactions](../../relational-databases/system-dynamic-management-views/sys-dm-tran-active-snapshot-database-transactions-transact-sql.md) 來判斷是否有長期執行的交易。 如果取消了 ALTER DATABASE 陳述式，資料庫會保留在 ALTER DATABASE 啟動時的狀態中。 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視指出資料庫中快照集隔離交易的狀態。 如果 **snapshot_isolation_state_desc** = IN_TRANSITION_TO_ON，ALTER DATABASE ALLOW_SNAPSHOT_ISOLATION OFF 會暫停六秒，然後重試作業。

如果資料庫是 OFFLINE，則您無法變更 ALLOW_SNAPSHOT_ISOLATION 的狀態。

如果您在 READ_ONLY 資料庫中設定 ALLOW_SNAPSHOT_ISOLATION，資料庫後來又設為 READ_WRITE，會保留這個設定。

您可以檢查 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中 `snapshot_isolation_state` 資料行來判斷此選項的目前設定。

READ_COMMITTED_SNAPSHOT { ON | OFF }     
開啟     
在資料庫層級啟用讀取認可快照集選項。 啟用時，即使沒有交易使用快照隔離，DML 陳述式也會開始產生資料列版本。 啟用此選項之後，指定 READ COMMITTED 隔離等級的交易即會使用資料列版本設定，而不是鎖定。 在 READ COMMITTED 隔離等級執行交易時，所有陳述式都會看到資料的快照，與陳述式開始時所存在的資料一樣。

OFF     
在資料庫層級關閉讀取認可快照集選項。 指定 READ COMMITTED 隔離等級的交易會使用鎖定。

若要設定 READ_COMMITTED_SNAPSHOT ON 或 OFF，除了執行 ALTER DATABASE 命令的連接之外，不能有任何使用中資料庫連線。 不過，資料庫不一定要處於單一使用者模式。 當資料庫是 OFFLINE 時，您無法變更這個選項的狀態。

如果您在 READ_ONLY 資料庫中設定 READ_COMMITTED_SNAPSHOT，則當資料庫後來又設為 READ_WRITE 時，會保留這個設定。

master、tempdb 或 msdb 系統資料庫的 READ_COMMITTED_SNAPSHOT 不能設為 ON。 如果您變更 model 的設定，除了 tempdb 以外，這項設定會成為任何新建資料庫的預設值。

您可以檢查 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中 `is_read_committed_snapshot_on` 資料行來判斷此選項的目前設定。

> [!WARNING]
> 當資料表是以 `DURABILITY = SCHEMA_ONLY` 建立，而且後續會使用 `ALTER DATABASE` 來變更 **READ_COMMITTED_SNAPSHOT** 時，資料表中的資料將會遺失。

MEMORY_OPTIMIZED_ELEVATE_TO_SNAPSHOT { ON | **OFF** }     
開啟     
當交易隔離等級設定為 SNAPSHOT 以下的任何隔離等級時，記憶體最佳化資料表上所有解譯的 [!INCLUDE[tsql](../../includes/tsql-md.md)] 作業都會在 SNAPSHOT 隔離下執行。 SNAPSHOT 以下的隔離等級範例包括 READ COMMITTED 或 READ UNCOMMITTED。 不論是在工作階段層級明確設定交易隔離等級，或隱含使用預設值，都會執行這些作業。

OFF     
不會為經記憶體最佳化資料表上解譯的 [!INCLUDE[tsql](../../includes/tsql-md.md)] 作業提高交易隔離等級。

如果資料庫是 OFFLINE，則您無法變更 MEMORY_OPTIMIZED_ELEVATE_TO_SNAPSHOT 的狀態。

預設值是 OFF。

您可以檢查 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中 `is_memory_optimized_elevate_to_snapshot_on` 資料行來判斷此選項的目前設定。

**\<sql_option> ::=**      
控制資料庫層級的 ANSI 合規性選項。

ANSI_NULL_DEFAULT { ON | **OFF** }     
決定未在 CREATE TABLE 或 ALTER TABLE 陳述式中明確定義 Null 屬性之資料行或 [CLR 使用者定義型別](../../relational-databases/clr-integration-database-objects-user-defined-types/clr-user-defined-types.md)的預設值 (NULL 或 NOT NULL)。 條件約束所定義的資料行會遵循條件約束規則，不論這個設定可能為何。

開啟     
預設值是 NULL。

OFF     
預設值是 NOT NULL。

利用 SET 陳述式來設定的連接層級設定會覆寫 ANSI_NULL_DEFAULT 的預設資料庫層級設定。 根據預設，ODBC 和 OLE DB 用戶端會發出連線層級的 SET 陳述式，將工作階段的 ANSI_NULL_DEFAULT 設定為 ON。 當您連線到 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體時，用戶端會執行此陳述式。 如需詳細資訊，請參閱 [SET ANSI_NULL_DFLT_ON](../../t-sql/statements/set-ansi-null-dflt-on-transact-sql.md)。

對於 ANSI 相容性而言，將資料庫選項 ANSI_NULL_DEFAULT 設為 ON 會將資料庫預設值改成 NULL。

您可以檢查 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中 `is_ansi_null_default_on` 資料行來判斷這個選項的狀態。 您也可以檢查 [DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md) 函式的 `IsAnsiNullDefault` 屬性來判斷狀態。

ANSI_NULLS { ON | **OFF** }     
開啟     
所有對於 Null 值的比較，都會得出 UNKNOWN。

OFF     
比較非 UNICODE 值和 Null 值，如果兩個值都是 NULL，便會得出 TRUE。

> [!IMPORTANT]
> 在未來的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 版本中，ANSI_NULLS 一律為 ON，而且明確將此選項設定為 OFF 的任何應用程式都會產生錯誤。 請避免在新的開發工作中使用這項功能，並規劃修改目前使用這項功能的應用程式。

利用 SET 陳述式來設定的連接層級設定會覆寫 ANSI_NULLS 的預設資料庫設定。 根據預設，ODBC 和 OLE DB 用戶端會發出連線層級的 SET 陳述式，將工作階段的 ANSI_NULLS 設定為 ON。 當您連線到 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體時，用戶端會執行此陳述式。 如需詳細資訊，請參閱 [SET ANSI_NULLS](../../t-sql/statements/set-ansi-nulls-transact-sql.md)。

> [!NOTE]
> 當您建立或變更計算資料行索引或索引檢視表時，SET ANSI_NULLS 也必須設為 ON。

您可以檢查 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中 `is_ansi_nulls_on` 資料行來判斷這個選項的狀態。 您也可以檢查 [DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md) 函式的 `IsAnsiNullsEnabled` 屬性來判斷狀態。

ANSI_PADDING { ON | **OFF** }     
開啟     
轉換字串前先填補至相同的長度。 也會填補到相同的長度，再插入 **varchar** 或 **nvarchar** 資料類型。

OFF     
**varchar** 或 **nvarchar** 資料行會插入字元值的尾端空格。 已插入 **varbinary** 資料行的二進位值尾端零也會保留。 值不會填補到資料行的長度。

當指定 OFF 時，這個設定只會影響新資料行的定義。

> [!IMPORTANT]
> 在未來的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 版本中，ANSI_PADDING 一律為 ON，而且明確將此選項設定為 OFF 的應用程式將會產生錯誤。 請避免在新的開發工作中使用這項功能，並規劃修改目前使用這項功能的應用程式。 我們建議您一律將 ANSI_PADDING 設為 ON。 當您建立或操作計算資料行索引或索引檢視表時，ANSI_PADDING 也必須是 ON。

當 ANSI_PADDING 設定為 ON 時，允許 Null 的 **char(_n_)** 和 **binary(_n_)** 資料行會填補到資料行長度。 當 ANSI_PADDING 為 OFF 時，則會修剪尾端空格和尾端零。 不允許 Null 的 **char(_n_)** 和 **binary(_n_)** 資料行一律會填補到資料行的長度。

利用 SET 陳述式來設定的連接層級設定會覆寫 ANSI_PADDING 的預設資料庫層級設定。 根據預設，ODBC 和 OLE DB 用戶端會發出連線層級的 SET 陳述式，將工作階段的 ANSI_PADDING 設定為 ON。 當您連線到 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體時，用戶端會執行此陳述式。 如需詳細資訊，請參閱 [SET ANSI_PADDING](../../t-sql/statements/set-ansi-padding-transact-sql.md)。

您可以檢查 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中 `is_ansi_padding_on` 資料行來判斷這個選項的狀態。 您也可以檢查 [DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md) 函式的 `IsAnsiPaddingEnabled` 屬性來判斷狀態。

ANSI_WARNINGS { ON | **OFF** }     
開啟     
如果發生「除以零」之類的狀況，便會發出錯誤或警告。 當彙總函式中出現 Null 值時，也會發出錯誤或警告。

OFF     
當發生除以零之類的情況時，不會產生警告，但會傳回 NULL 值。

> [!NOTE]
> 當您建立或變更計算資料行索引或索引檢視表時，SET ANSI_WARNINGS 必須設為 ON。

利用 SET 陳述式來設定的連接層級設定會覆寫 ANSI_WARNINGS 的預設資料庫設定。 根據預設，ODBC 和 OLE DB 用戶端會發出連線層級的 SET 陳述式，將工作階段的 ANSI_WARNINGS 設定為 ON。 當您連線到 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體時，用戶端會執行此陳述式。 如需詳細資訊，請參閱 [SET ANSI_WARNINGS](../../t-sql/statements/set-ansi-warnings-transact-sql.md)。

您可以檢查 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中 `is_ansi_warnings_on` 資料行來判斷這個選項的狀態。 您也可以檢查 [DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md) 函式的 `IsAnsiWarningsEnabled` 屬性來判斷狀態。

ARITHABORT { ON | OFF }     
開啟     
在查詢執行期間，當發生溢位或除以零的錯誤時，查詢會結束。

OFF     
當發生這些錯誤之一時，畫面上會顯示警告訊息。 即使顯示警告，查詢、批次或交易還是會繼續處理，如同未發生任何錯誤一樣。

> [!NOTE]
> 當您建立或變更計算資料行索引或索引檢視表時，SET ARITHABORT 必須設為 ON。

您可以檢查 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中 `is_arithabort_on` 資料行來判斷這個選項的狀態。 您也可以檢查 [DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md) 函式的 `IsArithmeticAbortEnabled` 屬性來判斷狀態。

COMPATIBILITY_LEVEL = { 150 \| 140 \| 130 \| 120 \| 110 \| 100 }     
如需詳細資訊，請參閱 [ALTER DATABASE 相容性層級](../../t-sql/statements/alter-database-transact-sql-compatibility-level.md)。

CONCAT_NULL_YIELDS_NULL { ON | **OFF** }     
開啟     
當任何一個運算元是 NULL 時，串連作業的結果都會是 NULL。 例如，串連字元字串 "This is" 和 NULL 會得出 NULL 值，而不是 "This is" 值。

OFF     
將 Null 值當做空的字元字串來處理。

> [!NOTE]
> 當您建立或變更計算資料行索引或索引檢視表時，CONCAT_NULL_YIELDS_NULL 也必須設為 ON。
>
> 在未來的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 版本中，CONCAT_NULL_YIELDS_NULL 一律為 ON，而且明確將此選項設定為 OFF 的應用程式將會產生錯誤。 請避免在新的開發工作中使用這項功能，並規劃修改目前使用這項功能的應用程式。

利用 SET 陳述式來設定的連接層級設定會覆寫 CONCAT_NULL_YIELDS_NULL 的預設資料庫設定。 根據預設，當連接到 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體時，ODBC 和 OLE DB 用戶端會發出連接層級的 SET 陳述式，將工作階段的 CONCAT_NULL_YIELDS_NULL 設為 ON。 如需詳細資訊，請參閱 [SET CONCAT_NULL_YIELDS_NULL](../../t-sql/statements/set-concat-null-yields-null-transact-sql.md)。

您可以檢查 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中 `is_concat_null_yields_null_on` 資料行來判斷這個選項的狀態。 您也可以檢查 [DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md) 函式的 `IsNullConcat` 屬性來判斷狀態。

QUOTED_IDENTIFIER { ON | OFF }     
開啟     
可以利用雙引號來含括分隔識別碼。

用雙引號來分隔的所有字串都會解譯為物件識別碼。 附加引號的識別碼不需要遵循 [!INCLUDE[tsql](../../includes/tsql-md.md)] 的識別碼規則。 它們可以是關鍵字，也可以包括 [!INCLUDE[tsql](../../includes/tsql-md.md)] 識別碼不允許的字元。 如果單引號 (') 是文字字串的一部分，您可以用雙引號 (") 來表示它。

OFF     
識別碼不能放在引號中，且必須遵循所有 [!INCLUDE[tsql](../../includes/tsql-md.md)] 識別碼規則。 文字可以用單引號或雙引號來分隔。

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 也允許用方括號 ([ ]) 來分隔識別碼。 不論 QUOTED_IDENTIFIER 設定為何，用方括弧括住的識別項一律可以使用。 如需詳細資訊，請參閱＜ [Database Identifiers](../../relational-databases/databases/database-identifiers.md)＞。

當建立資料表時，一律會在資料表的中繼資料中，將 QUOTED IDENTIFIER 選項儲存成 ON。 即使建立資料表時，將選項設定為 OFF，也會儲存此選項。

利用 SET 陳述式來設定的連接層級設定會覆寫 QUOTED_IDENTIFIER 的預設資料庫設定。 根據預設，ODBC 和 OLE DB 用戶端會發出連線層級的 SET 陳述式，將 QUOTED_IDENTIFIER 設定為 ON。 當您連線到 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體時，用戶端會執行此陳述式。 如需詳細資訊，請參閱 [SET QUOTED_IDENTIFIER](../../t-sql/statements/set-quoted-identifier-transact-sql.md)。

您可以檢查 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中 `is_quoted_identifier_on` 資料行來判斷這個選項的狀態。 您也可以檢查 [DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md) 函式的 `IsQuotedIdentifiersEnabled` 屬性來判斷狀態。

NUMERIC_ROUNDABORT { ON | OFF }     
開啟     
當運算式中遺失有效位數時，系統會產生錯誤。

OFF     
遺失有效位數並不會產生錯誤訊息，且結果會四捨五入到儲存結果的資料行或變數有效位數。

> [!IMPORTANT]
> 當您建立或變更計算資料行索引或索引檢視表時，NUMERIC_ROUNDABORT 必須設為 OFF。

您可以檢查 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中 `is_numeric_roundabort_on` 資料行來判斷這個選項的狀態。 您也可以檢查 [DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md) 函式的 `IsNumericRoundAbortEnabled` 屬性來判斷狀態。

RECURSIVE_TRIGGERS { ON | OFF }     
開啟     
允許遞迴引發 AFTER 觸發程序。

OFF     
您可以檢查 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中 `is_recursive_triggers_on` 資料行來判斷這個選項的狀態。 您也可以檢查 [DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md) 函式的 `IsRecursiveTriggersEnabled` 屬性來判斷狀態。

> [!NOTE]
> 當 RECURSIVE_TRIGGERS 設為 OFF 時，只防止直接遞迴。 若要停用間接遞迴，您也必須將巢狀觸發程序伺服器選項設為 0。

您可以檢查 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中 `is_recursive_triggers_on` 資料行或 [DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md) 函式 `IsRecursiveTriggersEnabled` 屬性來判斷這個選項的狀態。

**\<target_recovery_time_option> ::=**      
為每個資料庫指定間接檢查點的頻率。 從 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 開始，新資料庫的預設值為 1 分鐘，這表示資料庫將會使用間接檢查點。 舊版的預設值為 0，這表示資料庫將使用自動檢查點，其頻率取決於伺服器執行個體的復原間隔設定。 對於大多數的系統，[!INCLUDE[msCoName](../../includes/msconame-md.md)] 建議使用 1 分鐘。

TARGET_RECOVERY_TIME **=** target_recovery_time { SECONDS | MINUTES }     
*target_recovery_time*     
指定萬一發生損毀時，復原指定之資料庫的時間上限。

SECONDS     
指出 *target_recovery_time* 應以秒數表示。

MINUTES     
指出 *target_recovery_time* 應以分鐘數表示。

如需間接檢查點的詳細資訊，請參閱[資料庫檢查點](../../relational-databases/logs/database-checkpoints-sql-server.md)。

**WITH \<termination> ::=**      
指定資料庫狀態轉換時，何時回復不完整的交易。 如果省略 termination 子句，且資料庫有任何鎖定，則 ALTER DATABASE 陳述式會無限等候。 只能指定一個 termination 子句，它在 SET 子句之後。

> [!NOTE]
> 並非所有資料庫選項都會使用 WITH \<termination> 子句。 如需詳細資訊，請參閱本文＜備註＞一節中[＜設定選項＞](#SettingOptions)下的表格。

ROLLBACK AFTER *integer* [SECONDS] | ROLLBACK IMMEDIATE     
指定在指定的秒數之後回復，或是立即回復。

NO_WAIT     
指定如果要求的資料庫狀態或選項變更無法立即完成，要求將會失敗。 立即完成表示不等候交易自行認可或回復。

## <a name="setting-options"></a><a name="SettingOptions"></a> 設定選項

若要擷取資料庫選項的目前設定，請使用 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視或 [DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md)

設好資料庫選項之後，新設定會立即生效。

您可以針對所有新建立的資料庫，變更任何一個資料庫選項的預設值。 若要執行這項操作，請變更 model 資料庫中的適當資料庫選項。

並非所有資料庫選項都會使用 WITH \<termination> 子句，或可以搭配其他選項來指定。 下表列出這些選項及其選項和終止狀態。

|選項類別目錄|可以搭配其他選項指定|可以使用 WITH \<termination> 子句|
|----------------------|-----------------------------------------|---------------------------------------------|
|\<auto_option>|是|否|
|\<change_tracking_option>|是|是|
|\<cursor_option>|是|否|
|\<db_encryption_option>|是|否|
|\<db_update_option>|是|是|
|\<db_user_access_option>|是|是|
|\<delayed_durability_option>|是|是|
|\<parameterization_option>|是|是|
|ALLOW_SNAPSHOT_ISOLATION|否|否|
|READ_COMMITTED_SNAPSHOT|否|是|
|MEMORY_OPTIMIZED_ELEVATE_TO_SNAPSHOT|是|是|
|DATE_CORRELATION_OPTIMIZATION|是|是|
|\<sql_option>|是|否|
|\<target_recovery_time_option>|否|是|

## <a name="examples"></a>範例

### <a name="a-setting-the-database-to-read_only"></a>A. 將資料庫設為 READ_ONLY
將資料庫或檔案群組的狀態改成 READ_ONLY 或 READ_WRITE 時，需要資料庫的獨佔存取權。 下列範例會將資料庫設成 `RESTRICTED_USER` 模式來限制存取。 之後，範例會將 [!INCLUDE[ssSampleDBobject](../../includes/sssampledbobject-md.md)] 資料庫的狀態設成 `READ_ONLY` ，並將資料庫的存取權還給所有使用者。

```sql
USE master;
GO
ALTER DATABASE [database_name]
SET RESTRICTED_USER;
GO
ALTER DATABASE [database_name]
SET READ_ONLY
GO
ALTER DATABASE [database_name]
SET MULTI_USER;
GO

```

### <a name="b-enabling-snapshot-isolation-on-a-database"></a>B. 啟用資料庫的快照集隔離
下列範例會啟用 [!INCLUDE[ssSampleDBobject](../../includes/sssampledbobject-md.md)] 資料庫的快照集隔離架構選項。

```sql
USE [database_name];
USE master;
GO
ALTER DATABASE [database_name]
SET ALLOW_SNAPSHOT_ISOLATION ON;
GO
-- Check the state of the snapshot_isolation_framework
-- in the database.
SELECT name, snapshot_isolation_state,
    snapshot_isolation_state_desc AS description
FROM sys.databases
WHERE name = N'[database_name]';
GO
```

結果集顯示啟用快照集隔離架構。

|NAME |snapshot_isolation_state |description|
|-------------------- |------------------------|----------|
|[database_name] |1| 開啟 |

### <a name="c-enabling-modifying-and-disabling-change-tracking"></a>C. 啟用、修改及停用變更追蹤
下列範例會啟用 [!INCLUDE[ssSampleDBobject](../../includes/sssampledbobject-md.md)] 資料庫的變更追蹤，並將保留週期設定為 `2` 天。

```sql
ALTER DATABASE [database_name]
SET CHANGE_TRACKING = ON
(AUTO_CLEANUP = ON, CHANGE_RETENTION = 2 DAYS);
```

下列範例會示範如何將保留期間變更為 3 天。

```sql
ALTER DATABASE [database_name]
SET CHANGE_TRACKING (CHANGE_RETENTION = 3 DAYS);
```

下列範例會示範如何停用 [!INCLUDE[ssSampleDBobject](../../includes/sssampledbobject-md.md)] 資料庫的變更追蹤。

```sql
ALTER DATABASE [database_name]
SET CHANGE_TRACKING = OFF;
```

### <a name="d-enabling-the-query-store"></a>D. 啟用查詢存放區
下列範例會啟用查詢存放區並設定查詢存放區參數。

```sql
ALTER DATABASE [database_name]
SET QUERY_STORE = ON
    (
      OPERATION_MODE = READ_WRITE,
      CLEANUP_POLICY = ( STALE_QUERY_THRESHOLD_DAYS = 90 ),
      DATA_FLUSH_INTERVAL_SECONDS = 900,
      QUERY_CAPTURE_MODE = AUTO,
      MAX_STORAGE_SIZE_MB = 1024,
      INTERVAL_LENGTH_MINUTES = 60
    );
```

### <a name="e-enabling-the-query-store-with-wait-statistics"></a>E. 啟用包含等候統計資料的查詢存放區
下列範例會啟用查詢存放區並設定其參數。

```sql
ALTER DATABASE [database_name]
SET QUERY_STORE = ON
    (
      OPERATION_MODE = READ_WRITE,
      CLEANUP_POLICY = ( STALE_QUERY_THRESHOLD_DAYS = 90 ),
      DATA_FLUSH_INTERVAL_SECONDS = 900,
      MAX_STORAGE_SIZE_MB = 1024,
      INTERVAL_LENGTH_MINUTES = 60,
      SIZE_BASED_CLEANUP_MODE = AUTO,
      MAX_PLANS_PER_QUERY = 200,
      WAIT_STATS_CAPTURE_MODE = ON,
    );
```

### <a name="f-enabling-the-query-store-with-custom-capture-policy-options"></a>F. 啟用包含自訂擷取原則選項的查詢存放區
下列範例會啟用查詢存放區並設定其參數。

```sql
ALTER DATABASE [database_name]
SET QUERY_STORE = ON
    (
      OPERATION_MODE = READ_WRITE,
      CLEANUP_POLICY = ( STALE_QUERY_THRESHOLD_DAYS = 90 ),
      DATA_FLUSH_INTERVAL_SECONDS = 900,
      MAX_STORAGE_SIZE_MB = 1024,
      INTERVAL_LENGTH_MINUTES = 60,
      SIZE_BASED_CLEANUP_MODE = AUTO,
      MAX_PLANS_PER_QUERY = 200,
      WAIT_STATS_CAPTURE_MODE = ON,
      QUERY_CAPTURE_MODE = CUSTOM,
      QUERY_CAPTURE_POLICY = (
        STALE_CAPTURE_POLICY_THRESHOLD = 24 HOURS,
        EXECUTION_COUNT = 30,
        TOTAL_COMPILE_CPU_TIME_MS = 1000,
        TOTAL_EXECUTION_CPU_TIME_MS = 100
      )
    );
```

## <a name="see-also"></a>另請參閱

- [ALTER DATABASE 相容性層級](../../t-sql/statements/alter-database-transact-sql-compatibility-level.md)
- [ALTER DATABASE 資料庫鏡像](../../t-sql/statements/alter-database-transact-sql-database-mirroring.md)
- [統計資料](../../relational-databases/statistics/statistics.md)
- [CREATE DATABASE](../../t-sql/statements/create-database-transact-sql.md)
- [啟用和停用變更追蹤](../../relational-databases/track-changes/enable-and-disable-change-tracking-sql-server.md)
- [DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md)
- [DROP DATABASE](../../t-sql/statements/drop-database-transact-sql.md)
- [SET TRANSACTION ISOLATION LEVEL](../../t-sql/statements/set-transaction-isolation-level-transact-sql.md)
- [sp_configure](../../relational-databases/system-stored-procedures/sp-configure-transact-sql.md)
- [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md)
- [sys.data_spaces](../../relational-databases/system-catalog-views/sys-data-spaces-transact-sql.md)
- [使用查詢存放區的最佳作法](../../relational-databases/performance/best-practice-with-the-query-store.md)

::: moniker-end
::: moniker range="=azuresqldb-mi-current"

:::row:::
    :::column:::
        [SQL Server](alter-database-transact-sql-set-options.md?view=sql-server-ver15&preserve-view=true)
    :::column-end:::
    :::column:::
        [SQL Database](alter-database-transact-sql-set-options.md?view=azuresqldb-current&preserve-view=true)
    :::column-end:::
    :::column:::
        **_\* SQL 受控執行個體\*_** &nbsp;
    :::column-end:::
    :::column:::
        [Azure Synapse<br />Analytics](alter-database-transact-sql-set-options.md?view=azure-sqldw-latest&preserve-view=true)
    :::column-end:::
:::row-end:::

&nbsp;

## <a name="azure-sql-managed-instance"></a>Azure SQL 受控執行個體

相容性層級為 `SET` 選項，但在 [ALTER DATABASE 相容性層級](../../t-sql/statements/alter-database-transact-sql-compatibility-level.md)中描述。

> [!NOTE]
> 目前工作階段的許多資料庫 SET 選項都可以使用 [SET 陳述式](../../t-sql/statements/set-statements-transact-sql.md)來設定，而且通常是在應用程式連線時由其加以設定。 工作階段層級 SET 選項會覆寫 **ALTER DATABASE SET** 值。 下列章節所述的資料庫選項皆為未明確提供其他 SET 選項值，因而可針對工作階段進行設定的值。

## <a name="syntax"></a>語法

```syntaxsql
ALTER DATABASE { database_name | Current }
SET
{
    <optionspec> [ ,...n ]
}
;

<optionspec> ::=
{
    <auto_option>
  | <change_tracking_option>
  | <cursor_option>
  | <db_encryption_option>
  | <delayed_durability_option>
  | <parameterization_option>
  | <query_store_options>
  | <snapshot_option>
  | <sql_option>
  | <target_recovery_time_option>
  | <termination>
  | <temporal_history_retention>
}
;
<auto_option> ::=
{
    AUTO_CREATE_STATISTICS { OFF | ON [ ( INCREMENTAL = { ON | OFF } ) ] }
  | AUTO_SHRINK { ON | OFF }
  | AUTO_UPDATE_STATISTICS { ON | OFF }
  | AUTO_UPDATE_STATISTICS_ASYNC { ON | OFF }
}

<automatic_tuning_option> ::=
{
    AUTOMATIC_TUNING ( FORCE_LAST_GOOD_PLAN = { ON | OFF } )
}

<change_tracking_option> ::=
{
    CHANGE_TRACKING
    {
       = OFF
     | = ON [ ( <change_tracking_option_list > [,...n] ) ]
     | ( <change_tracking_option_list> [,...n] )
    }
}

<change_tracking_option_list> ::=
   {
       AUTO_CLEANUP = { ON | OFF }
     | CHANGE_RETENTION = retention_period { DAYS | HOURS | MINUTES }
   }

<cursor_option> ::=
{
    CURSOR_CLOSE_ON_COMMIT { ON | OFF }
}

<db_encryption_option> ::=
  ENCRYPTION { ON | OFF }

<delayed_durability_option> ::=DELAYED_DURABILITY = { DISABLED | ALLOWED | FORCED }

<parameterization_option> ::=
  PARAMETERIZATION { SIMPLE | FORCED }

<query_store_options> ::=
{
  QUERY_STORE
  {
    = OFF
    | = ON [ ( <query_store_option_list> [,... n] ) ]
    | ( < query_store_option_list> [,... n] )
    | CLEAR [ ALL ]
  }
}

<query_store_option_list> ::=
{
  OPERATION_MODE = { READ_WRITE | READ_ONLY }
  | CLEANUP_POLICY = ( STALE_QUERY_THRESHOLD_DAYS = number )
  | DATA_FLUSH_INTERVAL_SECONDS = number
  | MAX_STORAGE_SIZE_MB = number
  | INTERVAL_LENGTH_MINUTES = number
  | SIZE_BASED_CLEANUP_MODE = { AUTO | OFF }
  | QUERY_CAPTURE_MODE = { ALL | AUTO | CUSTOM | NONE }
  | MAX_PLANS_PER_QUERY = number
  | WAIT_STATS_CAPTURE_MODE = { ON | OFF }
  | QUERY_CAPTURE_POLICY = ( <query_capture_policy_option_list> [,...n] )
}

<query_capture_policy_option_list> :: =
{
    STALE_CAPTURE_POLICY_THRESHOLD = number { DAYS | HOURS }
    | EXECUTION_COUNT = number
    | TOTAL_COMPILE_CPU_TIME_MS = number
    | TOTAL_EXECUTION_CPU_TIME_MS = number
}

<snapshot_option> ::=
{
    ALLOW_SNAPSHOT_ISOLATION { ON | OFF }
  | READ_COMMITTED_SNAPSHOT {ON | OFF }
  | MEMORY_OPTIMIZED_ELEVATE_TO_SNAPSHOT {ON | OFF }
}
<sql_option> ::=
{
    ANSI_NULL_DEFAULT { ON | OFF }
  | ANSI_NULLS { ON | OFF }
  | ANSI_PADDING { ON | OFF }
  | ANSI_WARNINGS { ON | OFF }
  | ARITHABORT { ON | OFF }
  | COMPATIBILITY_LEVEL = { 150 | 140 | 130 | 120 | 110 | 100 }
  | CONCAT_NULL_YIELDS_NULL { ON | OFF }
  | NUMERIC_ROUNDABORT { ON | OFF }
  | QUOTED_IDENTIFIER { ON | OFF }
  | RECURSIVE_TRIGGERS { ON | OFF }
}

<temporal_history_retention>::= TEMPORAL_HISTORY_RETENTION { ON | OFF }
```

## <a name="arguments"></a>引數

*database_name*     
這是要修改之資料庫的名稱。

CURRENT     
`CURRENT` 會在目前資料庫中執行動作。 所有選項在所有內容中不支援 `CURRENT`。 如果 `CURRENT` 失敗，請提供資料庫名稱。

**\<auto_option> ::=**      
控制自動選項。

<a name="auto_create_statistics"></a> AUTO_CREATE_STATISTICS { **ON** | OFF }     
開啟     
查詢最佳化工具會視需要針對查詢述詞中的單一資料行建立統計資料，以便改善查詢計劃和查詢效能。 這些單一資料行統計資料是在查詢最佳化工具編譯查詢時所建立。 它只會針對尚未成為現有統計資料物件之第一個資料行的資料行建立單一資料行統計資料。

預設值是 ON。 我們建議您針對大部分資料庫使用預設設定。

OFF     
查詢最佳化工具不會在編譯查詢時針對查詢述詞中的單一資料行建立統計資料。 將這個選項設定為 OFF 可能會導致次佳查詢計劃並降低查詢效能。

您可以檢查 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中 `is_auto_create_stats_on` 資料行來判斷這個選項的狀態。 您也可以檢查 [DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md) 函式的 `IsAutoCreateStatistics` 屬性來判斷狀態。

如需詳細資訊，請參閱[統計資料](../../relational-databases/statistics/statistics.md#statistics-options)中的＜統計資料選項＞一節。

INCREMENTAL = ON | **OFF**     
將 AUTO_CREATE_STATISTICS 設定為 ON，並將 INCREMENTAL 設定為 ON。 此設定會在每次支援累加統計資料時，以累加方式建立自動建立的統計資料。 預設值是 OFF。 如需詳細資訊，請參閱 [CREATE STATISTICS](../../t-sql/statements/create-statistics-transact-sql.md)。

<a name="auto_shrink"></a> AUTO_SHRINK { ON | **OFF** }     
開啟     
資料庫檔案是定期壓縮的候選項。

資料檔案和記錄檔都可以自動壓縮。 只有在您將資料庫設定為 SIMPLE 復原模式或備份記錄時，AUTO_SHRINK 才會縮減交易記錄的大小。 當設定為 OFF 時，便不會在定期檢查未用空間時，自動壓縮資料庫檔案。

當超出 25% 的檔案包含未用空間時，AUTO_SHRINK 選項便會壓縮檔案。 此選項會使檔案壓縮成兩種大小之一。 它會壓縮成兩者中的較大者：

- 25% 的檔案是未使用空間的大小
- 檔案建立時的大小

您無法壓縮唯讀資料庫。

OFF     
在定期檢查未用空間時，不自動壓縮資料庫檔案。

您可以檢查 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中 `is_auto_shrink_on` 資料行來判斷這個選項的狀態。 您也可以檢查 [DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md) 函式的 `IsAutoShrink` 屬性來判斷狀態。

> [!NOTE]
> 自主資料庫無法使用 AUTO_SHRINK 選項。

<a name="auto_update_statistics"></a> AUTO_UPDATE_STATISTICS { **ON** | OFF }     
開啟     
指定當查詢使用統計資料且它們可能已過期時，查詢最佳化工具就會更新這些統計資料。 當插入、更新、刪除或合併作業變更資料表或索引檢視表中的資料分佈之後，統計資料就會變成過期。 查詢最佳化工具會計算自從上次更新統計資料以來資料修改的次數，並將修改次數與某個閾值比較，藉以判斷統計資料是否可能已經過期。 此臨界值是以資料表或索引檢視表中的資料列數目為基礎。

查詢最佳化工具會在編譯查詢及執行快取查詢計劃之前，檢查是否有過期的統計資料。 查詢最佳化工具會在查詢述詞中使用資料行、資料表和索引檢視表，來判斷哪些統計資料可能已過期。 查詢最佳化工具會在編譯查詢之前判斷這項資訊。 在執行快取查詢計劃之前，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 會確認查詢計劃是否參考最新的統計資料。

AUTO_UPDATE_STATISTICS 選項會套用至針對索引所建立的統計資料、查詢述詞中的單一資料行，以及使用 CREATE STATISTICS 陳述式所建立的統計資料。 此外，這個選項也會套用至篩選的統計資料。

預設值是 ON。 我們建議您針對大部分資料庫使用預設設定。

您可以使用 AUTO_UPDATE_STATISTICS_ASYNC 選項來指定要以同步或非同步方式更新統計資料。

OFF     
指定當查詢使用統計資料時，查詢最佳化工具不會更新這些統計資料。 當統計資料可能已過期時，查詢最佳化工具也不會更新這些統計資料。 將這個選項設定為 OFF 可能會導致次佳查詢計劃並降低查詢效能。

您可以檢查 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中 is_auto_update_stats_on 資料行來判斷這個選項的狀態。 您也可以檢查 [DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md) 函式的 `IsAutoUpdateStatistics` 屬性來判斷狀態。

如需詳細資訊，請參閱[統計資料](../../relational-databases/statistics/statistics.md)中的＜使用資料庫範圍統計資料選項＞一節。

<a name="auto_update_statistics_async"></a> AUTO_UPDATE_STATISTICS_ASYNC { ON | **OFF** }     
開啟     
指定 AUTO_UPDATE_STATISTICS 選項的統計資料更新是非同步的。 查詢最佳化工具在編譯查詢之前，不會等候統計資料更新完成。

除非 AUTO_UPDATE_STATISTICS 設為 ON，否則將這個選項設為 ON 沒有作用。

預設會將 AUTO_UPDATE_STATISTICS_ASYNC 選項設為 OFF，且查詢最佳化工具會同步更新統計資料。

OFF     
指定 AUTO_UPDATE_STATISTICS 選項的統計資料更新是同步的。 查詢最佳化工具在編譯查詢之前，會先等候統計資料更新完成。

除非 AUTO_UPDATE_STATISTICS 設為 ON，否則將這個選項設為 OFF 沒有作用。

您可以檢查 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中 is_auto_update_stats_async_on 資料行來判斷這個選項的狀態。

如需描述使用同步或非同步統計資料更新的時機詳細資訊，請參閱[統計資料](../../relational-databases/statistics/statistics.md)中的＜使用資料庫範圍統計資料選項＞一節。

<a name="auto_tuning"></a> **\<automatic_tuning_option> ::=**      
**適用於**：[!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)]

啟用或停用 `FORCE_LAST_GOOD_PLAN` [自動調整](../../relational-databases/automatic-tuning/automatic-tuning.md)選項。

FORCE_LAST_GOOD_PLAN = { ON | **OFF** }     
開啟     
[!INCLUDE[ssde_md](../../includes/ssde_md.md)] 會對新查詢計劃將造成效能衰退的 [!INCLUDE[tsql-md](../../includes/tsql-md.md)] 查詢，自動強制執行最後一個已知的良好計劃。 [!INCLUDE[ssde_md](../../includes/ssde_md.md)] 會使用強制方案持續監視 [!INCLUDE[tsql-md](../../includes/tsql-md.md)] 查詢的查詢效能。 如果效能有所提升，[!INCLUDE[ssde_md](../../includes/ssde_md.md)] 會繼續使用最後一個已知的良好計劃。 如果未偵測到效能提升，[!INCLUDE[ssde_md](../../includes/ssde_md.md)] 會產生新的查詢計劃。 如果未啟用查詢存放區，或其不在「讀寫」模式中，陳述式便會失敗。

OFF     
[!INCLUDE[ssde_md](../../includes/ssde_md.md)] 會在 [sys.dm_db_tuning_recommendations](../../relational-databases/system-dynamic-management-views/sys-dm-db-tuning-recommendations-transact-sql.md) 檢視中報告查詢計劃變更所造成可能的查詢效能衰退。 不過，不會自動套用這些建議。 使用者可以套用檢視中顯示的 [!INCLUDE[tsql-md](../../includes/tsql-md.md)] 指令碼，來監視使用中建議並修正已識別的問題。 這是預設值。

**\<change_tracking_option> ::=**      
控制變更追蹤選項。 您可以啟用變更追蹤、設定選項、變更選項，以及停用變更追蹤。 如需範例，請參閱本文稍後的＜範例＞一節。

ON 啟用資料庫的變更追蹤。 當您啟用變更追蹤時，也可以設定 AUTO CLEANUP 和 CHANGE RETENTION 選項。

AUTO_CLEANUP = { ON | OFF }     
開啟     
在經過了指定的保留週期後，將會自動移除變更追蹤資訊。

OFF     
不會從資料庫中移除變更追蹤資料。

CHANGE_RETENTION = *retention_period* { **DAYS** | HOURS | MINUTES }     
指定在資料庫中保存變更追蹤資訊的最小週期。 只有當 AUTO_CLEANUP 值為 ON 時，才會移除資料。

*retention_period* 是一個整數，它會指定保留週期的數值元件。

預設保留期間為 **2 天**。 最小保留週期是 1 分鐘。 預設保留期類型為 **DAYS**。

OFF     
停用資料庫的變更追蹤。 在您停用資料庫的變更追蹤之前，請先在所有資料表上停用變更追蹤。

**\<cursor_option> ::=**      
控制資料指標選項。

CURSOR_CLOSE_ON_COMMIT { ON | OFF }     
開啟     
當您認可或回復交易時，將會關閉任何開啟的資料指標。

OFF     
當認可交易時，資料指標維持開啟狀態；回復交易會關閉任何資料指標，但定義為 INSENSITIVE 或 STATIC 的資料指標除外。

利用 SET 陳述式來設定的連接層級設定會覆寫 CURSOR_CLOSE_ON_COMMIT 的預設資料庫設定。 根據預設，ODBC 和 OLE DB 用戶端會發出連線層級的 SET 陳述式，將工作階段的 CURSOR_CLOSE_ON_COMMIT 設定為 OFF。 當您連線到 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體時，用戶端會執行此陳述式。 如需詳細資訊，請參閱 [SET CURSOR_CLOSE_ON_COMMIT](../../t-sql/statements/set-cursor-close-on-commit-transact-sql.md)。

您可以檢查 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中的 is_cursor_close_on_commit_on 資料行，或 [DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md) 函式 IsCloseCursorsOnCommitEnabled 屬性來判斷這個選項的狀態。 只有在中斷連接時，才會隱含地取消配置資料指標。 如需詳細資訊，請參閱 [DECLARE CURSOR](../../t-sql/language-elements/declare-cursor-transact-sql.md)。

**\<db_encryption_option> ::=**      
控制資料庫加密狀態。

ENCRYPTION { ON | **OFF** }     
設定資料庫要加密 (ON) 或是不要加密 (OFF)。 如需資料庫加密的詳細資訊，請參閱[透明資料加密](../../relational-databases/security/encryption/transparent-data-encryption.md) 和 [Azure SQL Database 的透明資料加密](/azure/azure-sql/database/transparent-data-encryption-tde-overview)。

在資料庫層級啟用加密時，所有的檔案群組都會加密。 任何新檔案群組都會繼承加密的屬性。 如果資料庫中有任何檔案群組設定為 READ ONLY，則資料庫加密作業將會失敗。

您可以使用 [sys.dm_database_encryption_keys](../../relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql.md) 動態管理檢視來查看資料庫的加密狀態。

**\<db_update_option> ::=**      
控制是否允許更新資料庫。

READ_ONLY     
使用者可以從資料庫中讀取資料，但無法加以修改。

> [!NOTE]
> 如果要提高查詢的效能，請先更新統計資料，再將資料庫設為 READ_ONLY。 如果資料庫設為 READ_ONLY 之後，還需要其他的統計資料，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 將會在 tempdb 中建立統計資料。 如需唯讀資料庫統計資料的詳細資訊，請參閱[統計資料](../../relational-databases/statistics/statistics.md)。

READ_WRITE     
資料庫可以執行讀寫作業。

若要變更這個狀態，您必須具有資料庫的獨佔存取權。

**\<db_user_access_option> ::=**      
控制使用者對資料庫的存取權。

RESTRICTED_USER     
只允許 `db_owner` 固定資料庫角色與 `dbcreator` 及 `sysadmin` 固定伺服器角色的成員連線到資料庫，但不限制其數目。 在 ALTER DATABASE 陳述式的 termination 子句所指定的時間範圍中，會中斷資料庫的所有連接。 在資料庫進入 RESTRICTED_USER 狀態之後，不合格使用者的連接嘗試都會遭到拒絕。 **RESTRICTED_USER** 無法使用 SQL 受控執行個體來修改。

MULTI_USER     
允許所有具備適當權限來連接資料庫的使用者。

您可以檢查 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中 user_access 資料行或 [DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md) 函式的 `UserAccess` 屬性來判斷這個選項的狀態。

**\<delayed_durability_option> ::=**      
控制交易是否認可完全持久或延遲的持久。

DISABLED     
`SET DISABLED` 之後的所有交易都具有完全持久性。 在不可部分完成的區塊或 Commit 陳述式中設定的任何持久性選項都會被忽略。

ALLOWED     
`SET ALLOWED` 之後的所有交易都具有完全持久性或延遲持久性，視在 ATOMIC 區塊或 Commit 陳述式中設定的持久性選項而定。

FORCED     
`SET FORCED` 之後的所有交易都具有延遲持久性。 在不可部分完成的區塊或 Commit 陳述式中設定的任何持久性選項都會被忽略。

**\<PARAMETERIZATION_option> ::=**      
控制參數化選項。

PARAMETERIZATION { **SIMPLE** | FORCED }     
簡單     
根據資料庫的預設行為，將查詢參數化。

FORCED     
[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會將資料庫中的所有查詢參數化。

您可以檢查 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中 `is_parameterization_forced` 資料行來判斷此選項的目前設定。

<a name="query-store"></a> **\<query_store_options> ::=**

ON | OFF | CLEAR [ ALL ]     
控制是否在此資料庫中啟用查詢存放區，且控制查詢存放區內容的移除。

開啟     
啟用查詢存放區。

OFF     
停用查詢存放區。 這是預設值。

CLEAR     
移除查詢存放區的內容。

OPERATION_MODE     
描述查詢存放區的作業模式。 有效值為 READ_ONLY 和 READ_WRITE。 在 READ_WRITE 模式中，查詢存放區會收集並保存查詢計劃和執行階段執行統計資料資訊。 在 READ_ONLY 模式中，可以從查詢存放區讀取資訊，但不會新增資訊。 如果查詢存放區所配置的最大空間已用盡，則查詢存放區會將作業模式變更為 READ_ONLY。

CLEANUP_POLICY     
描述查詢存放區的資料保留原則。 STALE_QUERY_THRESHOLD_DAYS 會決定在查詢存放區中保留查詢資訊的天數。 STALE_QUERY_THRESHOLD_DAYS 的類型為 **bigint**。 預設值是 30。 [!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)] Basic 版的預設值為 **7** 天。

DATA_FLUSH_INTERVAL_SECONDS     
決定將寫入查詢存放區之資料保存到磁碟的頻率。 為了獲得最佳效能，查詢存放區所收集的資料會以非同步方式寫入磁碟。 此非同步傳輸發生的頻率是使用 DATA_FLUSH_INTERVAL_SECONDS 引數所設定。 DATA_FLUSH_INTERVAL_SECONDS 的類型為 **bigint**。 預設值為 **900** (15 分鐘)。

MAX_STORAGE_SIZE_MB     
決定配置給查詢存放區的空間。 MAX_STORAGE_SIZE_MB 的類型為 **bigint**。 預設值為 **100 MB**。

> [!NOTE]
> 系統不會嚴格強制執行 `MAX_STORAGE_SIZE_MB` 限制。 只有當查詢存放區將資料寫入磁碟時，系統才會檢查儲存體大小。 這個間隔是由 `DATA_FLUSH_INTERVAL_SECONDS` 選項或是 [!INCLUDE[ssManStudio](../../includes/ssManStudio-md.md)] 查詢存放區對話方塊選項 [資料排清間隔] 所設定。 間隔預設值為 900 秒 (15 分鐘)。
> 如果查詢存放區違反儲存體大小檢查之間的 `MAX_STORAGE_SIZE_MB` 限制，即會轉換為唯讀模式。 如果已啟用 `SIZE_BASED_CLEANUP_MODE`，也會觸發強制執行 `MAX_STORAGE_SIZE_MB` 限制的清除機制。
> 清除了足夠的空間之後，查詢存放區模式就會自動切換回讀寫。

> [!IMPORTANT]
> 如果您認為自己的工作負載擷取將會需要超過 10 GB 的磁碟空間，便應該重新思考並最佳化您的工作負載以重新使用查詢計劃 (例如使用[強制參數化](../../relational-databases/query-processing-architecture-guide.md#ForcedParam)，或是調整查詢存放區設定。    
> 從 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 開始以及在 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 中，您可以將 `QUERY_CAPTURE_MODE` 設定為 CUSTOM 以進一步控制查詢擷取原則。

INTERVAL_LENGTH_MINUTES     
決定執行階段執行統計資料彙總至查詢存放區的時間間隔。 若要將空間使用量最佳化，在執行階段統計資料存放區中的執行階段執行統計資料會透過固定的時段彙總。 這個固定的時段是使用 INTERVAL_LENGTH_MINUTES 引數所設定。 INTERVAL_LENGTH_MINUTES 的類型為 **bigint**。 預設值是 **60** 秒。

SIZE_BASED_CLEANUP_MODE     
控制當總資料量接近大小上限時，是否將自動啟用清除。

OFF     
不會自動啟用以大小為依據的清除。

AUTO     
當磁碟上的大小達到 90% 的 **max_storage_size_mb** 時，就會自動啟用以大小為依據的清除。 以大小為依據之清除會先移除成本最高和最舊的查詢。 它會在達到 **max_storage_size_mb** 的大約 80% 處停止。 這是預設組態值。

SIZE_BASED_CLEANUP_MODE 的類型為 **nvarchar**。

QUERY_CAPTURE_MODE { ALL \| AUTO \| CUSTOM \| NONE }     
指定目前使用中的查詢擷取模式。

ALL     
擷取所有查詢。

AUTO     
根據執行計數和資源耗用量擷取相關的查詢。 這是 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 的預設組態值。

無     
停止擷取新的查詢。 查詢存放區將會繼續收集已擷取查詢的編譯和執行階段統計資料。 因為您可能會錯過擷取重要查詢，請小心使用此設定。

QUERY_CAPTURE_MODE 的類型為 **nvarchar**。

max_plans_per_query     
表示維護每個查詢計劃的大數目的整數。 MAX_PLANS_PER_QUERY 的類型為 **int**。預設值為 **200**。

WAIT_STATS_CAPTURE_MODE { **ON** | OFF }     
控制是否會針對每個查詢擷取等候統計資料。

開啟    
會擷取每個查詢的等候統計資料資訊。 此值是預設設定值。

OFF    
不會擷取每個查詢的等候統計資料資訊。

**\<query_capture_policy_option_list> :: =**      
控制查詢存放區擷取原則選項。 除了 STALE_CAPTURE_POLICY_THRESHOLD，這些選項會定義必須進行的 OR 條件，以便在定義的過時擷取原則閾值中擷取查詢。

STALE_CAPTURE_POLICY_THRESHOLD = *number* { DAYS | HOURS }     
定義評估間隔週期以判斷是否應該擷取查詢。 預設為 1 天，且可設為 1 小時到七天。 *number* 的類型為 **int**。

EXECUTION_COUNT     
定義在評估週期內執行查詢的次數。 預設為 30，這表示針對過時擷取原則閾值的預設值，查詢必須在一天內至少執行 30 次才能保存在查詢存放區中。 EXECUTION_COUNT 的類型為 **int**。

TOTAL_COMPILE_CPU_TIME_MS     
定義查詢在評估週期內使用的總耗用編譯 CPU 時間。 預設為 1000，這表示針對過時擷取原則閾值的預設值，查詢在一天之中的查詢編譯期間內，必須總共至少有一秒的 CPU 時間，才能保存在查詢存放區中。 TOTAL_COMPILE_CPU_TIME_MS 的類型為 **int**。

TOTAL_EXECUTION_CPU_TIME_MS     
定義查詢在評估週期內使用的總耗用執行 CPU 時間。 預設為 100，這表示針對過時擷取原則閾值的預設值，查詢在一天之中的執行期間內，必須總共至少有 100 毫秒的 CPU 時間，才能保存在查詢存放區中。 TOTAL_EXECUTION_CPU_TIME_MS 的類型為 **int**。

**\<snapshot_option> ::=**

決定交易隔離等級。

ALLOW_SNAPSHOT_ISOLATION { ON | OFF }     
開啟     
在資料庫層級啟用快照集選項。 啟用時，即使沒有交易使用快照隔離，DML 陳述式也會開始產生資料列版本。 啟用此選項之後，交易可以指定 SNAPSHOT 交易隔離等級。 當交易執行的隔離等級是 SNAPSHOT 時，所有陳述式都會見到在交易開頭便存在的資料快照集。 如果執行 SNAPSHOT 隔離等級的交易存取多個資料庫中的資料，此時所有資料庫中的 ALLOW_SNAPSHOT_ISOLATION 都必須設為 ON，或是每當 FROM 子句參考 ALLOW_SNAPSHOT_ISOLATION 是 OFF 的資料庫中的資料表時，交易中的每個陳述式都必須使用鎖定提示。

OFF     
在資料庫層級關閉快照集選項。 交易無法指定 SNAPSHOT 交易隔離等級。

當您將 ALLOW_SNAPSHOT_ISOLATION 設為新狀態 (從 ON 設成 OFF，或從 OFF 設成 ON) 時，在認可資料庫中的所有現有交易之前，ALTER DATABASE 並不會將控制權傳回呼叫端。 如果資料庫已在 ALTER DATABASE 陳述式所指定的狀態中，控制權會立即傳回呼叫端。 如果 ALTER DATABASE 陳述式並沒有很快傳回，請使用 [sys.dm_tran_active_snapshot_database_transactions](../../relational-databases/system-dynamic-management-views/sys-dm-tran-active-snapshot-database-transactions-transact-sql.md) 來判斷是否有長期執行的交易。 如果取消了 ALTER DATABASE 陳述式，資料庫會保留在 ALTER DATABASE 啟動時的狀態中。 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視指出資料庫中快照集隔離交易的狀態。 如果 **snapshot_isolation_state_desc** = IN_TRANSITION_TO_ON，ALTER DATABASE ALLOW_SNAPSHOT_ISOLATION OFF 會暫停六秒，然後重試作業。

如果資料庫是 OFFLINE，則您無法變更 ALLOW_SNAPSHOT_ISOLATION 的狀態。

如果您在 READ_ONLY 資料庫中設定 ALLOW_SNAPSHOT_ISOLATION，資料庫後來又設為 READ_WRITE，會保留這個設定。

您可以變更 master、model、msdb 和 tempdb 等資料庫的 ALLOW_SNAPSHOT_ISOLATION 設定。 如果您變更 tempdb 的設定，每次停止和重新啟動 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 執行個體時，會保留這個設定。 如果您變更模型的設定，除了 tempdb 以外，任何新建資料庫都會以這個設定為預設值。

依預設，master 和 msdb 資料庫的這個選項是 ON。

您可以檢查 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中 `snapshot_isolation_state` 資料行來判斷此選項的目前設定。

READ_COMMITTED_SNAPSHOT { ON | **OFF** }     
開啟     
啟用資料庫層級的 [讀取認可快照集] 選項。 啟用時，即使沒有交易使用快照隔離，DML 陳述式也會開始產生資料列版本。 啟用此選項之後，指定 READ COMMITTED 隔離等級的交易即會使用資料列版本設定，而不是鎖定。 在 READ COMMITTED 隔離等級執行交易時，所有陳述式都會看到資料的快照，與陳述式開始時所存在的資料一樣。

OFF     
在資料庫層級關閉 [讀取認可快照集] 選項。 指定 READ COMMITTED 隔離等級的交易會使用鎖定。

若要設定 READ_COMMITTED_SNAPSHOT 為 ON 或 OFF，除了執行 ALTER DATABASE 命令的連線之外，不能有任何使用中資料庫連線。 不過，資料庫不一定要處於單一使用者模式。 當資料庫是 OFFLINE 時，您無法變更這個選項的狀態。

如果您在 READ_ONLY 資料庫中設定 READ_COMMITTED_SNAPSHOT，則當資料庫後來又設為 READ_WRITE 時，會保留這個設定。

master、tempdb 或 msdb 系統資料庫的 READ_COMMITTED_SNAPSHOT 不能設為 ON。 如果您變更 model 的設定，除了 tempdb 以外，這項設定會成為任何新建資料庫的預設值。

您可以檢查 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中 `is_read_committed_snapshot_on` 資料行來判斷此選項的目前設定。

> [!WARNING]
> 使用 **DURABILITY = SCHEMA_ONLY**, 和 **READ_COMMITTED_SNAPSHOT** 建立的資料表，在使用 **ALTER DATABASE** 進行變更時， 資料表中的資料會遺失。

MEMORY_OPTIMIZED_ELEVATE_TO_SNAPSHOT { ON | **OFF** }     
開啟     
當交易隔離等級設定為 SNAPSHOT 以下的任何隔離等級時，記憶體最佳化資料表上所有解譯的 [!INCLUDE[tsql](../../includes/tsql-md.md)] 作業都會在 SNAPSHOT 隔離下執行。 SNAPSHOT 以下的隔離等級範例包括 READ COMMITTED 或 READ UNCOMMITTED。 不論是在工作階段層級明確設定交易隔離等級，或隱含使用預設值，都會執行這些作業。

OFF     
不會為經記憶體最佳化資料表上解譯的 [!INCLUDE[tsql](../../includes/tsql-md.md)] 作業提高交易隔離等級。

如果資料庫是 OFFLINE，則您無法變更 MEMORY_OPTIMIZED_ELEVATE_TO_SNAPSHOT 的狀態。

預設值是 OFF。

您可以檢查 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中 `is_memory_optimized_elevate_to_snapshot_on` 資料行來判斷此選項的目前設定。

**\<sql_option> ::=**      
控制資料庫層級的 ANSI 合規性選項。

ANSI_NULL_DEFAULT { ON | OFF }     
決定未在 CREATE TABLE 或 ALTER TABLE 陳述式中明確定義 Null 屬性之資料行或 [CLR 使用者定義型別](../../relational-databases/clr-integration-database-objects-user-defined-types/clr-user-defined-types.md)的預設值 (NULL 或 NOT NULL)。 條件約束所定義的資料行會遵循條件約束規則，不論這個設定可能為何。

開啟     
預設值是 NULL。

OFF     
預設值是 NOT NULL。

利用 SET 陳述式來設定的連接層級設定會覆寫 ANSI_NULL_DEFAULT 的預設資料庫層級設定。 根據預設，ODBC 和 OLE DB 用戶端會發出連線層級的 SET 陳述式，將工作階段的 ANSI_NULL_DEFAULT 設定為 ON。 當您連線到 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體時，用戶端會執行此陳述式。 如需詳細資訊，請參閱 [SET ANSI_NULL_DFLT_ON](../../t-sql/statements/set-ansi-null-dflt-on-transact-sql.md)。

對於 ANSI 相容性而言，將資料庫選項 ANSI_NULL_DEFAULT 設為 ON 會將資料庫預設值改成 NULL。

您可以檢查 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中 `is_ansi_null_default_on` 資料行來判斷這個選項的狀態。 您也可以檢查 [DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md) 函式的 `IsAnsiNullDefault` 屬性來判斷狀態。

ANSI_NULLS { ON | OFF }     
開啟     
所有對於 Null 值的比較，都會得出 UNKNOWN。

OFF     
比較非 UNICODE 值和 Null 值，如果兩個值都是 NULL，便會得出 TRUE。

> [!IMPORTANT]
> 在未來的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 版本中，ANSI_NULLS 一律為 ON，而且明確將此選項設定為 OFF 的任何應用程式都會產生錯誤。 請避免在新的開發工作中使用這項功能，並規劃修改目前使用這項功能的應用程式。

  利用 SET 陳述式來設定的連接層級設定會覆寫 ANSI_NULLS 的預設資料庫設定。 根據預設，ODBC 和 OLE DB 用戶端會發出連線層級的 SET 陳述式，將工作階段的 ANSI_NULLS 設定為 ON。 當您連線到 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體時，用戶端會執行此陳述式。 如需詳細資訊，請參閱 [SET ANSI_NULLS](../../t-sql/statements/set-ansi-nulls-transact-sql.md)。

> [!IMPORTANT]
> 當您建立或變更計算資料行索引或索引檢視表時，SET ANSI_NULLS 也必須設為 ON。

您可以檢查 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中 `is_ansi_nulls_on` 資料行來判斷這個選項的狀態。 您也可以檢查 [DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md) 函式的 `IsAnsiNullsEnabled` 屬性來判斷狀態。

ANSI_PADDING { ON | **OFF** }     
開啟     
轉換字串前先填補至相同的長度。 也會填補到相同的長度，再插入 **varchar** 或 **nvarchar** 資料類型。

OFF     
**varchar** 或 **nvarchar** 資料行會插入字元值的尾端空格。 已插入 **varbinary** 資料行的二進位值尾端零也會保留。 值不會填補到資料行的長度。

當指定 OFF 時，這個設定只會影響新資料行的定義。

> [!IMPORTANT]
> 在未來的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 版本中，ANSI_PADDING 一律為 ON，而且明確將此選項設定為 OFF 的應用程式將會產生錯誤。 請避免在新的開發工作中使用這項功能，並規劃修改目前使用這項功能的應用程式。 我們建議您一律將 ANSI_PADDING 設為 ON。 當您建立或操作計算資料行索引或索引檢視表時，ANSI_PADDING 也必須是 ON。

當 ANSI_PADDING 設定為 ON 時，允許 Null 的 **char(_n_)** 和 **binary(_n_)** 資料行會填補到資料行長度。 當 ANSI_PADDING 為 OFF 時，則會修剪尾端空格和尾端零。 不允許 Null 的 **char(_n_)** 和 **binary(_n_)** 資料行一律會填補到資料行的長度。

  利用 SET 陳述式來設定的連接層級設定會覆寫 ANSI_PADDING 的預設資料庫層級設定。 根據預設，ODBC 和 OLE DB 用戶端會發出連線層級的 SET 陳述式，將工作階段的 ANSI_PADDING 設定為 ON。 當您連線到 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體時，用戶端會執行此陳述式。 如需詳細資訊，請參閱 [SET ANSI_PADDING](../../t-sql/statements/set-ansi-padding-transact-sql.md)。

您可以檢查 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中 `is_ansi_padding_on` 資料行來判斷這個選項的狀態。 您也可以檢查 [DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md) 函式的 `IsAnsiPaddingEnabled` 屬性來判斷狀態。

ANSI_WARNINGS { ON | **OFF** }     
開啟     
如果發生「除以零」之類的狀況，便會發出錯誤或警告。 當彙總函式中出現 Null 值時，也會發出錯誤或警告。

OFF     
當發生除以零之類的情況時，不會產生警告，但會傳回 NULL 值。

> [!IMPORTANT]
> 當您建立或變更計算資料行索引或索引檢視表時，SET ANSI_WARNINGS 必須設為 ON。

  利用 SET 陳述式來設定的連接層級設定會覆寫 ANSI_WARNINGS 的預設資料庫設定。 根據預設，ODBC 和 OLE DB 用戶端會發出連線層級的 SET 陳述式，將工作階段的 ANSI_WARNINGS 設定為 ON。 當您連線到 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體時，用戶端會執行此陳述式。 如需詳細資訊，請參閱 [SET ANSI_WARNINGS](../../t-sql/statements/set-ansi-warnings-transact-sql.md)。

您可以檢查 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中 `is_ansi_warnings_on` 資料行來判斷這個選項的狀態。 您也可以檢查 [DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md) 函式的 `IsAnsiWarningsEnabled` 屬性來判斷狀態。

ARITHABORT { ON | OFF }     
開啟     
在查詢執行期間，當發生溢位或除以零的錯誤時，查詢會結束。

OFF     
當發生這些錯誤之一時，畫面上會顯示警告訊息。 即使顯示警告，查詢、批次或交易還是會繼續處理，如同未發生任何錯誤一樣。

> [!IMPORTANT]
> 當您建立或變更計算資料行索引或索引檢視表時，SET ARITHABORT 必須設為 ON。

  您可以檢查 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中 `is_arithabort_on` 資料行來判斷這個選項的狀態。 您也可以檢查 [DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md) 函式的 `IsArithmeticAbortEnabled` 屬性來判斷狀態。

COMPATIBILITY_LEVEL = { 150 \| 140 \| 130 \| 120 \| 110 \| 100 }     
如需詳細資訊，請參閱 [ALTER DATABASE 相容性層級](../../t-sql/statements/alter-database-transact-sql-compatibility-level.md)。

CONCAT_NULL_YIELDS_NULL { ON | **OFF** }     
開啟     
當任何一個運算元是 NULL 時，串連作業的結果都會是 NULL。 例如，串連字元字串 "This is" 和 NULL 會得出 NULL 值，而不是 "This is" 值。

OFF     
將 Null 值當做空的字元字串來處理。

> [!IMPORTANT]
> 當您建立或變更計算資料行索引或索引檢視表時，CONCAT_NULL_YIELDS_NULL 也必須設為 ON。
>
> 在未來的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 版本中，CONCAT_NULL_YIELDS_NULL 一律為 ON，而且明確將此選項設定為 OFF 的應用程式將會產生錯誤。 請避免在新的開發工作中使用這項功能，並規劃修改目前使用這項功能的應用程式。

利用 SET 陳述式來設定的連接層級設定會覆寫 CONCAT_NULL_YIELDS_NULL 的預設資料庫設定。 根據預設，當連接到 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體時，ODBC 和 OLE DB 用戶端會發出連接層級的 SET 陳述式，將工作階段的 CONCAT_NULL_YIELDS_NULL 設為 ON。 如需詳細資訊，請參閱 [SET CONCAT_NULL_YIELDS_NULL](../../t-sql/statements/set-concat-null-yields-null-transact-sql.md)。

您可以檢查 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中 `is_concat_null_yields_null_on` 資料行來判斷這個選項的狀態。 您也可以檢查 [DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md) 函式的 `IsNullConcat` 屬性來判斷狀態。

QUOTED_IDENTIFIER { ON | OFF }     
開啟     
可以利用雙引號來含括分隔識別碼。

用雙引號來分隔的所有字串都會解譯為物件識別碼。 附加引號的識別碼不需要遵循 [!INCLUDE[tsql](../../includes/tsql-md.md)] 的識別碼規則。 它們可以是關鍵字，也可以包括 [!INCLUDE[tsql](../../includes/tsql-md.md)] 識別碼不允許的字元。 如果單引號 (') 是文字字串的一部分，您可以用雙引號 (") 來表示它。

OFF     
識別碼不能放在引號中，且必須遵循所有 [!INCLUDE[tsql](../../includes/tsql-md.md)] 識別碼規則。 文字可以用單引號或雙引號來分隔。

  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 也允許用方括號 ([ ]) 來分隔識別碼。 不論 QUOTED_IDENTIFIER 設定為何，用方括弧括住的識別項一律可以使用。 如需詳細資訊，請參閱＜ [Database Identifiers](../../relational-databases/databases/database-identifiers.md)＞。

  當建立資料表時，一律會在資料表的中繼資料中，將 QUOTED IDENTIFIER 選項儲存成 ON。 即使建立資料表時，將選項設定為 OFF，也會儲存此選項。

利用 SET 陳述式來設定的連接層級設定會覆寫 QUOTED_IDENTIFIER 的預設資料庫設定。 根據預設，ODBC 和 OLE DB 用戶端會發出連線層級的 SET 陳述式，將 QUOTED_IDENTIFIER 設定為 ON。 當您連線到 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體時，用戶端會執行此陳述式。 如需詳細資訊，請參閱 [SET QUOTED_IDENTIFIER](../../t-sql/statements/set-quoted-identifier-transact-sql.md)。

  您可以檢查 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中 `is_quoted_identifier_on` 資料行來判斷這個選項的狀態。 您也可以檢查 [DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md) 函式的 `IsQuotedIdentifiersEnabled` 屬性來判斷狀態。

NUMERIC_ROUNDABORT { ON | OFF }     
開啟     
當運算式中遺失有效位數時，系統會產生錯誤。

OFF     
遺失有效位數並不會產生錯誤訊息，且結果會四捨五入到儲存結果的資料行或變數有效位數。

> [!IMPORTANT]
> 當您建立或變更計算資料行索引或索引檢視表時，NUMERIC_ROUNDABORT 必須設為 OFF。

您可以檢查 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中 `is_numeric_roundabort_on` 資料行來判斷這個選項的狀態。 您也可以檢查 [DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md) 函式的 `IsNumericRoundAbortEnabled` 屬性來判斷狀態。

RECURSIVE_TRIGGERS { ON | OFF }     
開啟     
允許遞迴引發 AFTER 觸發程序。

OFF     
您可以檢查 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中 `is_recursive_triggers_on` 資料行來判斷這個選項的狀態。 您也可以檢查 [DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md) 函式的 `IsRecursiveTriggersEnabled` 屬性來判斷狀態。

> [!NOTE]
> 當 RECURSIVE_TRIGGERS 設為 OFF 時，只防止直接遞迴。 若要停用間接遞迴，您也必須將巢狀觸發程序伺服器選項設為 0。

您可以檢查 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中 `is_recursive_triggers_on` 資料行或 [DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md) 函式 `IsRecursiveTriggersEnabled` 屬性來判斷這個選項的狀態。

**\<target_recovery_time_option> ::=**      
為每個資料庫指定間接檢查點的頻率。 從 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 開始，新資料庫的預設值為 **1 分鐘**，這表示資料庫將會使用間接檢查點。 舊版的預設值為 0，這表示資料庫將使用自動檢查點，其頻率取決於伺服器執行個體的復原間隔設定。 對於大多數的系統，[!INCLUDE[msCoName](../../includes/msconame-md.md)] 建議使用 1 分鐘。

TARGET_RECOVERY_TIME **=** _target_recovery_time_ { SECONDS | MINUTES }     
*target_recovery_time*     
指定萬一發生損毀時，復原指定之資料庫的時間上限。

SECONDS     
指出 *target_recovery_time* 應以秒數表示。

MINUTES     
指出 *target_recovery_time* 應以分鐘數表示。

如需間接檢查點的詳細資訊，請參閱[資料庫檢查點](../../relational-databases/logs/database-checkpoints-sql-server.md)。

ROLLBACK AFTER *integer* [SECONDS] | ROLLBACK IMMEDIATE     
指定在指定的秒數之後回復，或是立即回復。

NO_WAIT     
指定如果要求的資料庫狀態或選項變更無法立即完成，要求將會失敗。 立即完成表示不等候交易自行認可或回復。

## <a name="setting-options"></a><a name="SettingOptions"></a> 設定選項
若要擷取資料庫選項的目前設定，請使用 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視或 [DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md)

設好資料庫選項之後，新設定會立即生效。

您可以針對所有新建立的資料庫，變更任何一個資料庫選項的預設值。 若要執行這項操作，請變更 model 資料庫中的適當資料庫選項。

## <a name="examples"></a>範例

### <a name="a-setting-the-database-to-read_only"></a>A. 將資料庫設為 READ_ONLY
將資料庫或檔案群組的狀態改成 READ_ONLY 或 READ_WRITE 時，需要資料庫的獨佔存取權。 下列範例會將資料庫設成 `RESTRICTED_USER` 模式來限制存取。 之後，範例會將 [!INCLUDE[ssSampleDBobject](../../includes/sssampledbobject-md.md)] 資料庫的狀態設成 `READ_ONLY` ，並將資料庫的存取權還給所有使用者。

```sql
USE master;
GO
ALTER DATABASE [database_name]
SET RESTRICTED_USER;
GO
ALTER DATABASE [database_name]
SET READ_ONLY
GO
ALTER DATABASE [database_name]
SET MULTI_USER;
GO
```

### <a name="b-enabling-snapshot-isolation-on-a-database"></a>B. 啟用資料庫的快照集隔離
下列範例會啟用 [!INCLUDE[ssSampleDBobject](../../includes/sssampledbobject-md.md)] 資料庫的快照集隔離架構選項。

```sql
USE [database_name];
USE master;
GO
ALTER DATABASE [database_name]
SET ALLOW_SNAPSHOT_ISOLATION ON;
GO
-- Check the state of the snapshot_isolation_framework
-- in the database.
SELECT name, snapshot_isolation_state,
    snapshot_isolation_state_desc AS description
FROM sys.databases
WHERE name = N'[database_name]';
GO
```

結果集顯示啟用快照集隔離架構。

|NAME |snapshot_isolation_state |description|
|-------------------- |------------------------|----------|
|[database_name] |1| 開啟 |

### <a name="c-enabling-modifying-and-disabling-change-tracking"></a>C. 啟用、修改及停用變更追蹤
下列範例會啟用 [!INCLUDE[ssSampleDBobject](../../includes/sssampledbobject-md.md)] 資料庫的變更追蹤，並將保留週期設定為 `2` 天。

```sql
ALTER DATABASE [database_name]
SET CHANGE_TRACKING = ON
(AUTO_CLEANUP = ON, CHANGE_RETENTION = 2 DAYS);
```

下列範例會示範如何將保留週期變更為 `3` 天。

```sql
ALTER DATABASE [database_name]
SET CHANGE_TRACKING (CHANGE_RETENTION = 3 DAYS);
```

下列範例會示範如何停用 [!INCLUDE[ssSampleDBobject](../../includes/sssampledbobject-md.md)] 資料庫的變更追蹤。

```sql
ALTER DATABASE [database_name]
SET CHANGE_TRACKING = OFF;
```

### <a name="d-enabling-the-query-store"></a>D. 啟用查詢存放區
下列範例會啟用查詢存放區並設定查詢存放區參數。

```sql
ALTER DATABASE [database_name]
SET QUERY_STORE = ON
    (
      OPERATION_MODE = READ_WRITE,
      CLEANUP_POLICY = ( STALE_QUERY_THRESHOLD_DAYS = 90 ),
      DATA_FLUSH_INTERVAL_SECONDS = 900,
      QUERY_CAPTURE_MODE = AUTO,
      MAX_STORAGE_SIZE_MB = 1024,
      INTERVAL_LENGTH_MINUTES = 60
    );
```

### <a name="e-enabling-the-query-store-with-wait-statistics"></a>E. 啟用包含等候統計資料的查詢存放區
下列範例會啟用查詢存放區並設定其參數。

```sql
ALTER DATABASE [database_name]
SET QUERY_STORE = ON
    (
      OPERATION_MODE = READ_WRITE,
      CLEANUP_POLICY = ( STALE_QUERY_THRESHOLD_DAYS = 90 ),
      DATA_FLUSH_INTERVAL_SECONDS = 900,
      MAX_STORAGE_SIZE_MB = 1024,
      INTERVAL_LENGTH_MINUTES = 60,
      SIZE_BASED_CLEANUP_MODE = AUTO,
      MAX_PLANS_PER_QUERY = 200,
      WAIT_STATS_CAPTURE_MODE = ON,
    );
```

### <a name="f-enabling-the-query-store-with-custom-capture-policy-options"></a>F. 啟用包含自訂擷取原則選項的查詢存放區
下列範例會啟用查詢存放區並設定其參數。

```sql
ALTER DATABASE [database_name]
SET QUERY_STORE = ON
    (
      OPERATION_MODE = READ_WRITE,
      CLEANUP_POLICY = ( STALE_QUERY_THRESHOLD_DAYS = 90 ),
      DATA_FLUSH_INTERVAL_SECONDS = 900,
      MAX_STORAGE_SIZE_MB = 1024,
      INTERVAL_LENGTH_MINUTES = 60,
      SIZE_BASED_CLEANUP_MODE = AUTO,
      MAX_PLANS_PER_QUERY = 200,
      WAIT_STATS_CAPTURE_MODE = ON,
      QUERY_CAPTURE_MODE = CUSTOM,
      QUERY_CAPTURE_POLICY = (
        STALE_CAPTURE_POLICY_THRESHOLD = 24 HOURS,
        EXECUTION_COUNT = 30,
        TOTAL_COMPILE_CPU_TIME_MS = 1000,
        TOTAL_EXECUTION_CPU_TIME_MS = 100
      )
    );
```

## <a name="see-also"></a>另請參閱

- [ALTER DATABASE 相容性層級](../../t-sql/statements/alter-database-transact-sql-compatibility-level.md)
- [ALTER DATABASE 資料庫鏡像](../../t-sql/statements/alter-database-transact-sql-database-mirroring.md)
- [統計資料](../../relational-databases/statistics/statistics.md)
- [CREATE DATABASE](../../t-sql/statements/create-database-transact-sql.md?view=azuresqldb-mi-current&preserve-view=true)
- [啟用和停用變更追蹤](../../relational-databases/track-changes/enable-and-disable-change-tracking-sql-server.md)
- [DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md)
- [DROP DATABASE](../../t-sql/statements/drop-database-transact-sql.md)
- [SET TRANSACTION ISOLATION LEVEL](../../t-sql/statements/set-transaction-isolation-level-transact-sql.md)
- [sp_configure](../../relational-databases/system-stored-procedures/sp-configure-transact-sql.md)
- [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md)
- [sys.data_spaces](../../relational-databases/system-catalog-views/sys-data-spaces-transact-sql.md)
- [使用查詢存放區的最佳作法](../../relational-databases/performance/best-practice-with-the-query-store.md)

::: moniker-end
::: moniker range="=azure-sqldw-latest"

:::row:::
    :::column:::
        [SQL Server](alter-database-transact-sql-set-options.md?view=sql-server-ver15&preserve-view=true)
    :::column-end:::
    :::column:::
        [SQL Database](alter-database-transact-sql-set-options.md?view=azuresqldb-current&preserve-view=true)
    :::column-end:::
    :::column:::
        [SQL 受控執行個體](alter-database-transact-sql-set-options.md?view=azuresqldb-mi-current&preserve-view=true)
    :::column-end:::
    :::column:::
        **_\* Azure Synapse<br />Analytics \*_** &nbsp;
    :::column-end:::
:::row-end:::

&nbsp;

## <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

## <a name="syntax"></a>語法

```syntaxsql
ALTER DATABASE { database_name }
SET
{
    <optionspec> [ ,...n ]
}
;

<option_spec>::=
{
    <auto_option>
  | <db_encryption_option>
  | <query_store_options>
  | <result_set_caching>
  | <snapshot_option>
}
;

<auto_option> ::=
{
    AUTO_CREATE_STATISTICS { OFF | ON }
}

<db_encryption_option> ::=
{
    ENCRYPTION { ON | OFF }
}

<query_store_option> ::=
{
    QUERY_STORE { OFF |  ON }
}

<result_set_caching_option> ::=
{
    RESULT_SET_CACHING {ON | OFF}
}

<snapshot_option> ::=
{
    READ_COMMITTED_SNAPSHOT {ON | OFF }
}

```

## <a name="arguments"></a>引數

*database_name*     
這是要修改之資料庫的名稱。

**<自動選項> ::=**      
控制自動選項。

AUTO_CREATE_STATISTICS { **ON** | OFF }     

開啟     
查詢最佳化工具會視需要針對查詢述詞中的單一資料行建立統計資料，以便改善查詢計劃和查詢效能。 這些單一資料行統計資料是在查詢最佳化工具編譯查詢時所建立。 它只會針對尚未成為現有統計資料物件之第一個資料行的資料行建立單一資料行統計資料。

預設值是 ON。 我們建議您針對大部分資料庫使用預設設定。

OFF     
查詢最佳化工具不會在編譯查詢時針對查詢述詞中的單一資料行建立統計資料。 將這個選項設定為 OFF 可能會導致次佳查詢計劃並降低查詢效能。

### <a name="remarks"></a>備註
此命令必須在連線到使用者資料庫時執行。

您可以檢查 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中 i`s_auto_create_stats_on` 資料行來判斷這個選項的狀態。 您也可以檢查 [DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md) 函式的 `IsAutoCreateStatistics` 屬性來判斷狀態。

如需詳細資訊，請參閱＜統計資料＞中的＜使用資料庫範圍統計資料選項＞一節。

**<DB 加密選項> ::=**      
控制資料庫加密狀態。

ENCRYPTION { ON | OFF }     

開啟     
將資料庫設為加密。

OFF     
將資料庫設為不加密。

如需資料庫加密的詳細資訊，請參閱＜透明資料加密＞和＜Azure SQL Database 的透明資料加密＞。

在資料庫層級啟用加密時，所有的檔案群組都會加密。 任何新檔案群組都會繼承加密的屬性。 如果資料庫中有任何檔案群組設定為 READ ONLY，則資料庫加密作業將會失敗。

您可以使用 sys.dm_database_encryption_keys 動態管理檢視來查看資料庫的加密狀態，以及加密掃描的狀態。

**<查詢存放區選項> ::=**      
控制此資料倉儲中是否已啟用查詢存放區。

QUERY_STORE { ON |  **OFF**  }     

開啟     
啟用查詢存放區。

OFF     
停用查詢存放區。 OFF 是預設值。

> [!NOTE]
> 針對 [!INCLUDE[ssSDW](../../includes/sssdw-md.md)]，您必須從使用者資料庫中執行 `ALTER DATABASE SET QUERY_STORE`。 不支援從另一個資料倉儲執行個體執行陳述式。

**<result_set_caching_option> ::=**      
**適用於**：Azure Synapse Analytics  

控制是否要在資料庫中快取查詢結果。

RESULT_SET_CACHING { ON | OFF}     

開啟     
指定從此資料庫傳回的查詢結果集會在資料庫中進行快取。

OFF     
指定從此資料庫傳回的查詢結果集不會在資料庫中進行快取。

### <a name="remarks"></a>備註

此命令必須在連線到 `master` 資料庫時執行。  對此資料庫設定的變更會立即生效。  儲存體費用會以快取查詢結果集的數目計算。 停用資料庫的結果快取後，會立即從 Azure Synapse 儲存體中刪除先前保存的結果快取。

執行此命令來檢查資料庫的結果集快取設定。  如果結果集快取已開啟，is_result_set_caching_on 將傳回 1。

```sql
SELECT name, is_result_set_caching_on FROM sys.databases
WHERE name = <'Your_Database_Name'>
```

執行此命令來檢查查詢是否使用快取的結果來執行。  result_cache_hit 資料行會針對快取命中傳回 1、針對快取遺漏傳回 0，並針對未使用結果集快取的原因傳回負值。  檢查 [sys.dm_pdw_exec_requests](../../relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql.md?view=aps-pdw-2016-au7&preserve-view=true) 以取得詳細資料。  

```sql
SELECT request_id, command, result_cache_hit FROM sys.dm_pdw_exec_requests
WHERE request_id = <'Your_Query_Request_ID'>
```

> [!IMPORTANT]
> 建立結果集快取以及從快取擷取資料的作業會在資料倉儲執行個體的控制節點上進行。 當結果集快取開啟時，執行傳回大型結果集的查詢 (例如，> 1 百萬個資料列) 可能會導致控制節點上的 CPU 使用率過高，並使執行個體的整體查詢回應變慢。 這些查詢通常會在資料探索或 ETL 作業期間使用。 若要避免對控制節點造成壓力並導致效能問題，使用者應該先關閉資料庫的結果集快取，再執行這些類型的查詢。  

如需有關使用結果集快取進行效能調整的詳細資訊，請參閱[效能調整指引](/azure/sql-data-warehouse/performance-tuning-result-set-caching)。

### <a name="permissions"></a>權限

若要設定 RESULT_SET_CACHING 選項，使用者需要伺服器層級主體登入 (由佈建程序所建立) 或為 `dbmanager` 資料庫角色的成員。  

**<snapshot_option> ::=**      
**適用於**：Azure Synapse Analytics

控制資料庫的交易隔離等級。

READ_COMMITTED_SNAPSHOT  { ON | **OFF** }     

開啟     
啟用資料庫層級的 READ_COMMITTED_SNAPSHOT 選項。

OFF     
在資料庫層級關閉 READ_COMMITTED_SNAPSHOT 選項。

### <a name="remarks"></a>備註

此命令必須在連線到 `master` 資料庫時執行。 開啟或關閉使用者資料庫的 READ_COMMITTED_SNAPSHOT，將會終止此資料庫的所有開放連線。 建議您在資料庫維護期間或等到資料庫沒有使用中的連線再進行此變更，執行 ALTER DATABASE 命令的連線除外。  資料庫不一定要處於單一使用者模式。 不支援變更工作階段層級的 READ_COMMITTED_SNAPSHOT 設定。  若要確認資料庫的這項設定，請檢查 sys.databases 中的 is_read_committed_snapshot_on 資料行。

在已啟用 READ_COMMITTED_SNAPSHOT 的資料庫中，如果有多個資料版本存在，查詢可能會遭遇較低的效能。 長時間開啟的交易也會導致資料庫大小增加。 如果封鎖版本清除的這些交易進行資料變更，就會發生此問題。  

### <a name="permissions"></a>權限

若要設定 READ_COMMITTED_SNAPSHOT 選項，使用者需要資料庫的 ALTER 權限。

## <a name="examples"></a>範例

### <a name="check-statistics-setting-for-a-database"></a>檢查資料庫的統計資料設定

```sql
SELECT name, is_auto_create_stats_on FROM sys.databases
```

### <a name="enable-query-store-for-a-database"></a>啟用資料庫的查詢存放區

```sql
ALTER DATABASE [database_name]
SET QUERY_STORE = ON;
```

### <a name="enable-result-set-caching-for-a-database"></a>啟用資料庫的結果集快取

```sql
-- Run this command when connecting to the MASTER database

ALTER DATABASE [database_name]
SET RESULT_SET_CACHING ON;
```

### <a name="check-result-set-caching-setting-for-a-database"></a>檢查資料庫的結果集快取設定

```sql
SELECT name, is_result_set_caching_on
FROM sys.databases;
```

### <a name="enable-the-read_committed_snapshot-option-for-a-database"></a>啟用資料庫的 Read_Committed_Snapshot 選項

```sql
-- Run this command when connecting to the MASTER database

ALTER DATABASE MyDatabase
SET READ_COMMITTED_SNAPSHOT ON
```

## <a name="see-also"></a>另請參閱

- [DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md)
- [DROP DATABASE](../../t-sql/statements/drop-database-transact-sql.md)
- [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md)
- [Azure Synapse Analytics 的最佳做法](/azure/sql-data-warehouse/sql-data-warehouse-best-practices#maintain-statistics)
- [在 Azure Synapse Analytics 中設計資料表](/azure/sql-data-warehouse/sql-data-warehouse-tables-overview#statistics)
- [Azure Synapse Analytics 語言元素](/azure/sql-data-warehouse/sql-data-warehouse-reference-tsql-language-elements)

::: moniker-end
