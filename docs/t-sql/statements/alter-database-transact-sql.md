---
description: ALTER DATABASE (Transact-SQL)
title: ALTER DATABASE (Transact-SQL) | Microsoft Docs
ms.custom: references_regions
ms.date: 10/30/2020
ms.prod: sql
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- ALTER_DATABASE_TSQL
- ALTER DATABASE
dev_langs:
- TSQL
helpviewer_keywords:
- databases [SQL Server], modifying
- ALTER DATABASE statement
- databases [SQL Server], renaming
- renaming databases
- database modifications [SQL Server]
- ALTER DATABASE statement, syntax described
- database names [SQL Server], ALTER DATABASE
- modifying databases
- collations [SQL Server], modifying
- database mirroring [SQL Server], Transact-SQL
ms.assetid: 15f8affd-8f39-4021-b092-0379fc6983da
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-current||=azuresqldb-mi-current||=azure-sqldw-latest||>=aps-pdw-2016'
ms.openlocfilehash: 1f25887409183593230d44dfd813e1ff36a3ee36
ms.sourcegitcommit: 713e5a709e45711e18dae1e5ffc190c7918d52e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98689040"
---
# <a name="alter-database-transact-sql"></a>ALTER DATABASE (Transact-SQL)

修改資料庫的特定組態選項。

本文提供適用於您所選擇之 SQL 產品的語法、引數、備註、權限和範例。

如需語法慣例的詳細資訊，請參閱 [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)。

[!INCLUDE[select-product](../../includes/select-product.md)]

::: moniker range=">=sql-server-2016||>=sql-server-linux-2017"

:::row:::
    :::column:::
        **_\* SQL Server \*_** &nbsp;
    :::column-end:::
    :::column:::
        [SQL Database](alter-database-transact-sql.md?view=azuresqldb-current&preserve-view=true)
    :::column-end:::
    :::column:::
        [SQL 受控執行個體](alter-database-transact-sql.md?view=azuresqldb-mi-current&preserve-view=true)
    :::column-end:::
    :::column:::
        [Azure Synapse<br />Analytics](alter-database-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)
    :::column-end:::
    :::column:::
        [Analytics Platform<br />System (PDW)](alter-database-transact-sql.md?view=aps-pdw-2016-au7&preserve-view=true)
    :::column-end:::
:::row-end:::

&nbsp;

## <a name="overview-sql-server"></a>概觀：SQL Server

在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中，此陳述式可修改資料庫或與資料庫建立關聯的檔案和檔案群組。 在資料庫中新增或移除檔案和檔案群組、變更資料庫或其檔案和檔案群組的屬性、變更資料庫定序，以及設定資料庫選項。 無法修改資料庫快照集。 若要修改與複寫相關聯的資料庫選項，請使用 [sp_replicationdboption](../../relational-databases/system-stored-procedures/sp-replicationdboption-transact-sql.md)。

由於長度的關係，`ALTER DATABASE` 語法會分成多篇文章。

ALTER DATABASE   
目前的文章會提供變更資料庫名稱和定序的語法與相關資訊。

[ALTER DATABASE 檔案及檔案群組選項](../../t-sql/statements/alter-database-transact-sql-file-and-filegroup-options.md)   
提供在資料庫中新增和移除檔案及檔案群組的語法與相關資訊，以及變更檔案及檔案群組屬性的語法與相關資訊。

[ALTER DATABASE SET 選項](../../t-sql/statements/alter-database-transact-sql-set-options.md)   
提供使用 ALTER DATABASE 的 SET 選項來變更資料庫屬性的語法與相關資訊。

[ALTER DATABASE 資料庫鏡像](../../t-sql/statements/alter-database-transact-sql-database-mirroring.md)   
可為與資料庫鏡像相關的 ALTER DATABASE SET 選項提供語法與相關資訊。

[ALTER DATABASE SET HADR](../../t-sql/statements/alter-database-transact-sql-set-hadr.md)   
為 ALTER DATABASE 的 [!INCLUDE[ssHADR](../../includes/sshadr-md.md)] 選項，提供在 Always On 可用性群組的次要複本上設定次要資料庫的語法與相關資訊。

[ALTER DATABASE 相容性層級](../../t-sql/statements/alter-database-transact-sql-compatibility-level.md)   
可為與資料庫相容性層級相關的 ALTER DATABASE SET 選項提供語法與相關資訊。

[ALTER DATABASE SCOPED CONFIGURATION](../../t-sql/statements/alter-database-scoped-configuration-transact-sql.md)   
提供與資料庫範圍設定 (用於個別的資料庫層級設定，例如查詢最佳化及查詢執行相關行為) 相關的語法。

## <a name="syntax"></a>語法

```syntaxsql
-- SQL Server Syntax
ALTER DATABASE { database_name | CURRENT }
{
    MODIFY NAME = new_database_name
  | COLLATE collation_name
  | <file_and_filegroup_options>
  | SET <option_spec> [ ,...n ] [ WITH <termination> ]
}
[;]

<file_and_filegroup_options>::=
  <add_or_modify_files>::=
  <filespec>::=
  <add_or_modify_filegroups>::=
  <filegroup_updatability_option>::=

<option_spec>::=
{
  | <auto_option>
  | <change_tracking_option>
  | <cursor_option>
  | <database_mirroring_option>
  | <date_correlation_optimization_option>
  | <db_encryption_option>
  | <db_state_option>
  | <db_update_option>
  | <db_user_access_option><delayed_durability_option>
  | <external_access_option>
  | <FILESTREAM_options>
  | <HADR_options>
  | <parameterization_option>
  | <query_store_options>
  | <recovery_option>
  | <service_broker_option>
  | <snapshot_option>
  | <sql_option>
  | <termination>
  | <temporal_history_retention>
  | <data_retention_policy>
  | <compatibility_level>
      { 150 | 140 | 130 | 120 | 110 | 100 | 90 }
}
```

## <a name="arguments"></a>引數

*database_name*：這是要修改的資料庫名稱。

> [!NOTE]
> 自主資料庫無法使用這個選項。

CURRENT   
**適用對象**：[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 及更新版本。

指定應該改變正在使用中的目前資料庫。

MODIFY NAME **=** _new_database_name_   
使用指定為 *new_database_name* 的名稱來重新命名資料庫。

COLLATE *collation_name*   
指定資料庫的定序。 *collation_name* 可以是 Windows 定序名稱或 SQL 定序名稱。 若未指定，就會將 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體的定序指派給資料庫。

> [!NOTE]
> 在 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 中建立資料庫之後，即無法變更定序。

使用預設定序除外的方式建立資料庫時，資料庫中的資料一律會接受指定的定序。 針對 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]，建立自主資料庫時，會使用 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 預設定序 (**Latin1_General_100_CI_AS_WS_KS_SC**) 來維護內部的目錄資訊。

如需有關 Windows 和 SQL 定序名稱的詳細資訊，請參閱 [COLLATE](~/t-sql/statements/collations.md)。

**\<delayed_durability_option> ::=**    
**適用對象**：[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 及更新版本。

如需詳細資訊，請參閱 [ALTER DATABASE SET 選項](../../t-sql/statements/alter-database-transact-sql-set-options.md)及[控制交易持久性](../../relational-databases/logs/control-transaction-durability.md)。

**\<file_and_filegroup_options>::=**    
如需詳細資訊，請參閱 [ALTER DATABASE 檔案及檔案群組選項](../../t-sql/statements/alter-database-transact-sql-file-and-filegroup-options.md)。

## <a name="remarks"></a>備註

若要移除資料庫，請使用 [DROP DATABASE](../../t-sql/statements/drop-database-transact-sql.md)。

若要縮小資料庫大小，請使用 [DBCC SHRINKDATABASE](../../t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql.md)。

`ALTER DATABASE` 陳述式必須在自動認可模式 (預設的交易管理模式) 下執行，且不能用於明確或隱含交易。

資料庫檔案狀態 (如線上或離線) 的維護與資料庫狀態無關。 如需詳細資訊，請參閱[檔案狀態](../../relational-databases/databases/file-states.md)。 檔案群組內的檔案狀態決定了整個檔案群組的可用性。 若要使某個檔案群組為可用的，則在檔案群組中的所有檔案必須都在線上。 如果檔案群組離線，SQL 陳述式存取檔案群組的任何嘗試都會失敗，且會出現錯誤。 當您建置 SELECT 陳述式的查詢計劃時，查詢最佳化工具會避開在離線檔案群組中的非叢集索引和索引檢視表。 這樣會讓這些陳述式能夠執行成功。 不過，如果離線檔案群組包含目標資料表的堆積或叢集索引，SELECT 陳述式將會失敗。 此外，在離線檔案群組中，以 `INSERT`、`UPDATE` 或 `DELETE` 陳述式修改含有索引的資料表將會失敗。

當資料庫處於 RESTORING 狀態時，大部分的 `ALTER DATABASE` 陳述式都會失敗。 設定資料庫鏡像選項例外。 在使用中的還原作業期間，或是由於備份檔損毀導致資料庫或記錄檔的還原作業失敗時，資料庫都有可能處於 RESTORING 狀態。

設定下列其中一個選項，可清除 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體的計畫快取。

- COLLATE
- MODIFY FILEGROUP DEFAULT
- MODIFY FILEGROUP READ_ONLY
- MODIFY FILEGROUP READ_WRITE
- MODIFY_NAME
- OFFLINE
- ONLINE
- PAGE_VERIFY
- READ_ONLY
- READ_WRITE

清除計畫快取會導致重新編譯所有後續執行計畫，而且可能會導致查詢效能突然暫時下降。 針對計畫快取中每個清除的快取存放區，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 錯誤記錄檔會包含下列資訊訊息：`SQL Server has encountered %d occurrence(s) of cachestore flush for the '%s' cachestore (part of plan cache) due to some database maintenance or reconfigure operations`。 只要在該時間間隔內快取發生排清，這個訊息就會每五分鐘記錄一次。

在下列情況下也會排清計畫快取：

- 資料庫將 `AUTO_CLOSE` 資料庫選項設定為 ON。 當沒有任何使用者連接參考或使用資料庫時，背景工作嘗試關閉並自動關閉資料庫。
- 您針對有預設選項的資料庫執行幾個查詢。 然後卸除資料庫。
- 卸除來源資料庫的資料庫快照集。
- 您已成功重建資料庫的交易記錄。
- 您還原資料庫備份。
- 您卸離資料庫。

## <a name="changing-the-database-collation"></a>變更資料庫定序

將不同定序套用至資料庫之前，請確定已符合下列條件：

- 您是資料庫目前唯一的使用者。
- 沒有結構描述繫結的物件相依於資料庫的定序。

如果相依於資料庫定序的下列物件存在於資料庫中，ALTER DATABASE *database_name* COLLATE 陳述式將會失敗。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 將會針對每一個封鎖 `ALTER` 動作的物件傳回錯誤訊息：

- 使用 SCHEMABINDING 建立的使用者定義函式和檢視
- 計算資料行
- CHECK 條件約束
- 傳回包含字元資料行資料表的資料表值函式，其定序繼承自預設資料庫定序
  
變更資料庫定序時，就會自動更新非結構描述繫結實體的相依性資訊。

變更資料庫定序並不會在資料庫物件的任何系統名稱之間建立複本。 如果變更的定序產生重複名稱，下列命名空間可能會使資料庫定序的變更失敗：

- 物件名稱，例如程序、資料表、觸發程序或檢視
- 結構描述名稱
- 主體，例如群組、角色或使用者
- 純量類型名稱，例如系統和使用者定義型別
- 全文檢索目錄名稱
- 物件內的資料行或參數名稱
- 資料表內的索引名稱

新定序所造成的重複名稱會使變更動作失敗，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會傳回錯誤訊息，指出出現重複名稱的命名空間。

## <a name="viewing-database-information"></a>檢視資料庫資訊

您可以利用目錄檢視、系統函數和系統預存程序，以傳回資料庫、檔案和檔案群組的相關資訊。

## <a name="permissions"></a>權限

需要資料庫的 `ALTER` 權限。

## <a name="examples"></a>範例

### <a name="a-changing-the-name-of-a-database"></a>A. 變更資料庫的名稱

下列範例會將 `AdventureWorks2012` 資料庫的名稱變更為 `Northwind`。

```sql
USE master;
GO
ALTER DATABASE AdventureWorks2012
Modify Name = Northwind ;
GO
```

### <a name="b-changing-the-collation-of-a-database"></a>B. 變更資料庫的定序

下列範例會使用 `testdb`S 定序來建立名為 `SQL_Latin1_General_CP1_CI_A` 的資料庫，然後將 `testdb` 資料庫的定序變更為 `COLLATE French_CI_AI`。

**適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。

```sql
USE master;
GO

CREATE DATABASE testdb
COLLATE SQL_Latin1_General_CP1_CI_AS ;
GO

ALTER DATABASE testDB
COLLATE French_CI_AI ;
GO
```

## <a name="see-also"></a>另請參閱

- [CREATE DATABASE](../../t-sql/statements/create-database-transact-sql.md)
- [DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md)
- [DROP DATABASE](../../t-sql/statements/drop-database-transact-sql.md)
- [SET TRANSACTION ISOLATION LEVEL](../../t-sql/statements/set-transaction-isolation-level-transact-sql.md)
- [EVENTDATA](../../t-sql/functions/eventdata-transact-sql.md)
- [sp_configure](../../relational-databases/system-stored-procedures/sp-configure-transact-sql.md)
- [sp_spaceused](../../relational-databases/system-stored-procedures/sp-spaceused-transact-sql.md)
- [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md)
- [sys.database_files](../../relational-databases/system-catalog-views/sys-database-files-transact-sql.md)
- [sys.database_mirroring_witnesses](../../relational-databases/system-catalog-views/database-mirroring-witness-catalog-views-sys-database-mirroring-witnesses.md)
- [sys.data_spaces](../../relational-databases/system-catalog-views/sys-data-spaces-transact-sql.md)
- [sys.filegroups](../../relational-databases/system-catalog-views/sys-filegroups-transact-sql.md)
- [sys.master_files](../../relational-databases/system-catalog-views/sys-master-files-transact-sql.md)
- [系統資料庫](../../relational-databases/databases/system-databases.md)

::: moniker-end
::: moniker range="=azuresqldb-current"

:::row:::
    :::column:::
        [SQL Server](alter-database-transact-sql.md?view=sql-server-ver15&preserve-view=true)
    :::column-end:::
    :::column:::
        **_\* SQL Database \*_** &nbsp;
    :::column-end:::
    :::column:::
        [SQL 受控執行個體](alter-database-transact-sql.md?view=azuresqldb-mi-current&preserve-view=true)
    :::column-end:::
    :::column:::
        [Azure Synapse<br />Analytics](alter-database-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)
    :::column-end:::
    :::column:::
        [Analytics Platform<br />System (PDW)](alter-database-transact-sql.md?view=aps-pdw-2016-au7&preserve-view=true)
    :::column-end:::
:::row-end:::

&nbsp;

## <a name="overview-sql-database"></a>概觀：SQL Database

在 Azure SQL Database 中，使用此陳述式來修改資料庫。 使用此陳述式變更資料庫名稱、變更資料庫的版本和服務目標、新增或移除彈性集區的資料庫、設定資料庫選項、新增或移除具有地理複寫關聯性的次要資料庫，以及設定資料庫相容性層級。

由於長度的關係，`ALTER DATABASE` 語法會分成多篇文章。

ALTER DATABASE   
目前的文章會提供變更資料庫名稱和定序的語法與相關資訊。

[ALTER DATABASE SET 選項](../../t-sql/statements/alter-database-transact-sql-set-options.md?view=azuresqldb-current&preserve-view=true)    
提供使用 ALTER DATABASE 的 SET 選項來變更資料庫屬性的語法與相關資訊。

[ALTER DATABASE 相容性層級](../../t-sql/statements/alter-database-transact-sql-compatibility-level.md?view=azuresqldb-current&preserve-view=true)   
可為與資料庫相容性層級相關的 ALTER DATABASE SET 選項提供語法與相關資訊。

## <a name="syntax"></a>語法

```syntaxsql
-- Azure SQL Database Syntax
ALTER DATABASE { database_name | CURRENT }
{
    MODIFY NAME = new_database_name
  | MODIFY ( <edition_options> [, ... n] )
  | MODIFY BACKUP_STORAGE_REDUNDANCY = { 'LOCAL' | 'ZONE' | 'GEO' }
  | SET { <option_spec> [ ,... n ] WITH <termination>}
  | ADD SECONDARY ON SERVER <partner_server_name>
    [WITH ( <add-secondary-option>::=[, ... n] ) ]
  | REMOVE SECONDARY ON SERVER <partner_server_name>
  | FAILOVER
  | FORCE_FAILOVER_ALLOW_DATA_LOSS
}
[;]

<edition_options> ::=
{

  MAXSIZE = { 100 MB | 250 MB | 500 MB | 1 ... 1024 ... 4096 GB }
  | EDITION = { 'Basic' | 'Standard' | 'Premium' | 'GeneralPurpose' | 'BusinessCritical' | 'Hyperscale'}
  | SERVICE_OBJECTIVE =
       { <service-objective>
       | { ELASTIC_POOL (name = <elastic_pool_name>) }
       }
}

<add-secondary-option> ::=
   {
      ALLOW_CONNECTIONS = { ALL | NO }
     | SERVICE_OBJECTIVE =
       { <service-objective>
       | { ELASTIC_POOL ( name = <elastic_pool_name>) }
       }
   }

<service-objective> ::={ 'Basic' |'S0' | 'S1' | 'S2' | 'S3'| 'S4'| 'S6'| 'S7'| 'S9'| 'S12'
       | 'P1' | 'P2' | 'P4'| 'P6' | 'P11' | 'P15'
      | 'GP_Gen4_1' | 'GP_Gen4_2' | 'GP_Gen4_3' | 'GP_Gen4_4' | 'GP_Gen4_5' | 'GP_Gen4_6'
      | 'GP_Gen4_7' | 'GP_Gen4_8' | 'GP_Gen4_9' | 'GP_Gen4_10' | 'GP_Gen4_16' | 'GP_Gen4_24'
      | 'GP_Gen5_2' | 'GP_Gen5_4' | 'GP_Gen5_6' | 'GP_Gen5_8' | 'GP_Gen5_10' | 'GP_Gen5_12' | 'GP_Gen5_14'
      | 'GP_Gen5_16' | 'GP_Gen5_18' | 'GP_Gen5_20' | 'GP_Gen5_24' | 'GP_Gen5_32' | 'GP_Gen5_40' | 'GP_Gen5_80'
      | 'GP_Fsv2_8' | 'GP_Fsv2_10' | 'GP_Fsv2_12' | 'GP_Fsv2_14' | 'GP_Fsv2_16' | 'GP_Fsv2_18'
      | 'GP_Fsv2_20' | 'GP_Fsv2_24' | 'GP_Fsv2_32' | 'GP_Fsv2_36' | 'GP_Fsv2_72'
      | 'GP_S_Gen5_1' | 'GP_S_Gen5_2' | 'GP_S_Gen5_4' | 'GP_S_Gen5_6' | 'GP_S_Gen5_8'
      | 'GP_S_Gen5_10' | 'GP_S_Gen5_12' | 'GP_S_Gen5_14' | 'GP_S_Gen5_16'
      | 'GP_S_Gen5_18' | 'GP_S_Gen5_20' | 'GP_S_Gen5_24' | 'GP_S_Gen5_32' | 'GP_S_Gen5_40'
      | 'BC_Gen4_1' | 'BC_Gen4_2' | 'BC_Gen4_3' | 'BC_Gen4_4' | 'BC_Gen4_5' | 'BC_Gen4_6'
      | 'BC_Gen4_7' | 'BC_Gen4_8' | 'BC_Gen4_9' | 'BC_Gen4_10' | 'BC_Gen4_16' | 'BC_Gen4_24'
      | 'BC_Gen5_2' | 'BC_Gen5_4' | 'BC_Gen5_6' | 'BC_Gen5_8' | 'BC_Gen5_10' | 'BC_Gen5_12' | 'BC_Gen5_14'
      | 'BC_Gen5_16' | 'BC_Gen5_18' | 'BC_Gen5_20' | 'BC_Gen5_24' | 'BC_Gen5_32' | 'BC_Gen5_40' | 'BC_Gen5_80'
      | 'BC_M_8' | 'BC_M_10' | 'BC_M_12' | 'BC_M_14' | 'BC_M_16' | 'BC_M_18'
      | 'BC_M_20' | 'BC_M_24' | 'BC_M_32' | 'BC_M_64' | 'BC_M_128'
      | 'HS_GEN4_1' | 'HS_GEN4_2' | 'HS_GEN4_4' | 'HS_GEN4_8' | 'HS_GEN4_16' | 'HS_GEN4_24'
      | 'HS_GEN5_2' | 'HS_GEN5_4' | 'HS_GEN5_8' | 'HS_GEN5_16' | 'HS_GEN5_24' | 'HS_GEN5_32' | 'HS_GEN5_48' | 'HS_GEN5_80'
      | { ELASTIC_POOL(name = <elastic_pool_name>) }
      }

<option_spec> ::=
{
    <auto_option>
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
  | <compatibility_level>
    { 150 | 140 | 130 | 120 | 110 | 100 | 90 }

}
```

## <a name="arguments"></a>引數

*database_name*   
這是要修改之資料庫的名稱。

CURRENT   
指定應該改變正在使用中的目前資料庫。

MODIFY NAME **=** _new_database_name_   
使用指定為 *new_database_name* 的名稱來重新命名資料庫。 下列範例會將資料庫 `db1` 的名稱變更為 `db2`：

```sql
ALTER DATABASE db1
    MODIFY Name = db2 ;
```

MODIFY (EDITION **=** ['Basic' \| 'Standard' \| 'Premium' \|'GeneralPurpose' \| 'BusinessCritical' \| 'Hyperscale'])   
變更資料庫的服務層。

下列範例會將版本變更為 `Premium`：

```sql
ALTER DATABASE current
    MODIFY (EDITION = 'Premium');
```

> [!IMPORTANT]
> 如果為資料庫 MAXSIZE 屬性設定的值超出該版本所支援的有效範圍，EDITION 變更就會失敗。

MODIFY (BACKUP_STORAGE_REDUNDANCY **=** ['LOCAL' \| 'ZONE' \| 'GEO'])   
變更資料庫時間點還原備份與長期保留備份 (若已設定) 的儲存體備援。 這些變更會套用至所有未來建立的備份。 現有備份會繼續使用先前的設定。 

> [!IMPORTANT]
> Azure SQL 資料庫的 BACKUP_STORAGE_REDUNDANCY 選項在巴西南部推出公開預覽，且僅在 Azure 區域的東南亞正式推出。  

MODIFY (MAXSIZE **=** [100 MB \| 500 MB \| 1 \| 1024...4096] GB)   
指定資料庫的大小上限。 大小上限必須符合資料庫的有效 EDITION 屬性值集合。 變更資料庫的大小上限可能也會造成資料庫版本變更。

> [!NOTE]
> **MAXSIZE** 引數不適用於超大規模服務層中的單一資料庫。 超大規模服務層資料庫會視需要成長，最多 100 TB。 SQL Database 服務會自動新增儲存體；您不需要設定大小上限。

**DTU 模型**

|**MAXSIZE**|**基本**|**S0-S2**|**S3-S12**|**P1-P6**|**P11-P15**|
|-----------------|---------------|------------------|-----------------|-----------------|-----------------|-----------------|
|100 MB|√|√|√|√|√|
|250 MB|√|√|√|√|√|
|500 MB|√|√|√|√|√|
|1 GB|√|√|√|√|√|
|2 GB|√ (D)|√|√|√|√|
|5 GB|N/A|√|√|√|√|
|10 GB|N/A|√|√|√|√|
|20 GB|N/A|√|√|√|√|
|30 GB|N/A|√|√|√|√|
|40 GB|N/A|√|√|√|√|
|50 GB|N/A|√|√|√|√|
|100 GB|N/A|√|√|√|√|
|150 GB|N/A|√|√|√|√|
|200 GB|N/A|√|√|√|√|
|250 GB|N/A|√ (D)|√ (D)|√|√|
|300 GB|N/A|√|√|√|√|
|400 GB|不適用|√|√|√|√|
|500 GB|N/A|√|√|√ (D)|√|
|750 GB|N/A|√|√|√|√|
|1024 GB|N/A|√|√|√|√ (D)|
|從 1024 GB 至最大 4096 GB (以每 256 GB 的大小遞增)*|N/A|N/A|N/A|N/A|√|

\* P11 和 P15 允許 MAXSIZE 最大至 4 TB，並以 1024 GB 作為預設大小。 P11 和 P15 最多可使用 4 TB 的隨附儲存體，且不另收費。 在進階層中，大於 1 TB 的 MAXSIZE 目前可用於下列區域：美國東部 2、美國西部、US Gov 維吉尼亞州、西歐、德國中部、東南亞、日本東部、澳大利亞東部、加拿大中部和加拿大東部。 如需 DTU 模型的資源限制的額外詳細資訊，請參閱 [DTU 資源限制](/azure/sql-database/sql-database-dtu-resource-limits) \(部分機器翻譯\)。

對於 DTU 模型，若指定了 MAXSIZE 值，則此值必須為上表中所示適用於所指定服務層的有效值。

**虛擬核心模型**

**一般用途 - 佈建的計算 - Gen4 (第 1 部分)**

|MAXSIZE|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6|
|:----- | ------: |-------: |-------: |-------: |-------: |--------:|
|資料大小上限 (GB)|1024|1024|1024|1536|1536|1536|

**一般用途 - 佈建的計算 - Gen4 (第 2 部分)**

|MAXSIZE|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24
|:----- | ------: |-------: |-------: |-------: |-------: |--------:|
|資料大小上限 (GB)|1536|3072|3072|3072|4096|4096|

**一般用途 - 佈建的計算 - Gen5 (第 1 部分)**

|MAXSIZE|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:----- | ------: |-------: |-------: |-------: |--------: |---------:|--------: |
|資料大小上限 (GB)|1024|1024|1024|1536|1536|1536|1536|

**一般用途 - 佈建的計算 - Gen5 (第 2 部分)**

|MAXSIZE|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:----- | ------: |-------: |-------: |-------: |--------: |---------:|--------: |
|資料大小上限 (GB)|3072|3072|3072|4096|4096|4096|4096|

**一般用途 - 佈建的計算 - Fsv2-系列 (第 1 部分)**

|MAXSIZE|GP_Fsv2_8|GP_Fsv2_10|GP_Fsv2_12|GP_Fsv2_14|GP_Fsv2_16|GP_Fsv2_18|
|:----- | ------: | ------: | ------: | ------: | ------: | ------: |
|資料大小上限 (GB)|1024|1024|1024|1024|1536|1536|

**一般用途 - 佈建的計算 - Fsv2-系列 (第 2 部分)**

|MAXSIZE|GP_Fsv2_20|GP_Fsv2_24|GP_Fsv2_32|GP_Fsv2_36|GP_Fsv2_72|
|:----- | ------: | ------: | ------: | ------: | ------: |
|資料大小上限 (GB)|1536|1536|3072|3072|4096|

**一般用途 - 無伺服器計算 - Gen5 (第 1 部分)**

|MAXSIZE|GP_S_Gen5_1|GP_S_Gen5_2|GP_S_Gen5_4|GP_S_Gen5_6|GP_S_Gen5_8|
|:----- | ------: |-------: |-------: |-------: |--------: |
|虛擬核心數上限|1|2|4|6|8|

**一般用途 - 無伺服器計算 - Gen5 (第 2 部分)**

|MAXSIZE|GP_S_Gen5_10|GP_S_Gen5_12|GP_S_Gen5_14|GP_S_Gen5_16|
|:----- | ------: |-------: |-------: |-------: |
|虛擬核心數上限|10|12|14|16|

**一般用途 - 無伺服器計算 - Gen5 (第 3 部分)**

|MAXSIZE|GP_S_Gen5_18|GP_S_Gen5_20|GP_S_Gen5_24|GP_S_Gen5_32|GP_S_Gen5_40|
|:----- | ------: |-------: |-------: |-------: |--------: |
|虛擬核心數上限|18|20|24|32|40|

**商務關鍵性 - 佈建的計算 - Gen4 (第 1 部分)**

|計算大小 (服務目標)|BC_Gen4_1|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--------------- | ------: |-------: |-------: |-------: |-------: |-------: |
|資料大小上限 (GB)|1024|1024|1024|1024|1024|1024|

**商務關鍵性 - 佈建的計算 - Gen4 (第 2 部分)**

|計算大小 (服務目標)|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--------------- | ------: |-------: |-------: |--------: |--------: |--------: |
|資料大小上限 (GB)|1024|1024|1024|1024|1024|1024|

**商務關鍵性 - 佈建的計算 - Gen5 (第 1 部分)**

|MAXSIZE|BC_Gen5_2|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:----- | ------: |-------: |-------: |-------: |---------: |--------:|--------: |
|資料大小上限 (GB)|1024|1024|1024|1536|1536|1536|1536|

**商務關鍵性 - 佈建的計算 - Gen5 (第 2 部分)**

|MAXSIZE|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:----- | -------: |--------: |--------: |--------: |--------: |---------:|--------: |
|資料大小上限 (GB)|3072|3072|3072|4096|4096|4096|4096|

**商務關鍵性 - 佈建的計算 - M-系列 (第 1 部分)**

|MAXSIZE|BC_M_8|BC_M_10|BC_M_12|BC_M_14|BC_M_16|BC_M_18|
|:----- | -------: | -------: | -------: | -------: | -------: | -------: |
|資料大小上限 (GB)|512|640|768|896|1024|1152|

**商務關鍵性 - 佈建的計算 - M-系列 (第 2 部分)**

|MAXSIZE|BC_M_20|BC_M_24|BC_M_32|BC_M_64|BC_M_128|
|:----- | -------: | -------: | -------: | -------: | -------: |
|資料大小上限 (GB)|1280|1536|2048|4096|4096|

當使用 vCore 模型時，如果未設定 `MAXSIZE` 值，預設值為 32 GB。 如需有關虛擬核心模型資源限制的其他詳細資訊，請參閱[虛擬核心資源限制](/azure/sql-database/sql-database-dtu-resource-limits)。

以下規則會套用到 MAXSIZE 和 EDITION 引數：

- 如果指定了 EDITION 但是未指定 MAXSIZE，就會使用版本的預設值。 例如，如果將 EDITION 設為 Standard，而未指定 MAXSIZE，則 MAXSIZE 會自動設定為 250 MB。
- 如果 MAXSIZE 和 EDITION 皆未指定，則 EDITION 會設定為 General Purpose 而 MAXSIZE 設定為 32 GB。

MODIFY (SERVICE_OBJECTIVE = \<service-objective>)   
指定計算大小 (服務目標)。 下列範例會將進階資料庫的服務目標變更為 `P6`：

```sql
ALTER DATABASE current
    MODIFY (SERVICE_OBJECTIVE = 'P6');
```

SERVICE_OBJECTIVE   

- **針對單一和集區資料庫**

  - 指定計算大小 (服務目標)。 服務目標的可用值為：`S0`、`S1`、`S2`、`S3`、`S4`、`S6`、`S7`、`S9`、`S12`、`P1`、`P2`、`P4`、`P6`、`P11`、`P15`、`GP_GEN4_1`、`GP_GEN4_2`、`GP_GEN4_3`、`GP_GEN4_4`、`GP_GEN4_5`、`GP_GEN4_6`、`GP_GEN4_7`、`GP_GEN4_8`、`GP_GEN4_7`、`GP_GEN4_8`、`GP_GEN4_9`、`GP_GEN4_10`、`GP_GEN4_16`、`GP_GEN4_24`、`BC_GEN4_1`、`BC_GEN4_2`、`BC_GEN4_3`、`BC_GEN4_4`、`BC_GEN4_5`、`BC_GEN4_6`、`BC_GEN4_7`、`BC_GEN4_8`、`BC_GEN4_9`、`BC_GEN4_10`、`BC_GEN4_16`、`BC_GEN4_24`、`GP_Gen5_2`、`GP_Gen5_4`、`GP_Gen5_6`、`GP_Gen5_8`、`GP_Gen5_10`、`GP_Gen5_12`、`GP_Gen5_14`、`GP_Gen5_16`、`GP_Gen5_18`、`GP_Gen5_20`、`GP_Gen5_24`、`GP_Gen5_32`、`GP_Gen5_40`、`GP_Gen5_80`、`GP_Fsv2_8`、`GP_Fsv2_10`、`GP_Fsv2_12`、`GP_Fsv2_14`、`GP_Fsv2_16`、`GP_Fsv2_18`、`GP_Fsv2_20`、`GP_Fsv2_24`、`GP_Fsv2_32`、`GP_Fsv2_36`、`GP_Fsv2_72`、`BC_Gen5_2`、`BC_Gen5_4`、`BC_Gen5_6`、`BC_Gen5_8`、`BC_Gen5_10`、`BC_Gen5_12`、`BC_Gen5_14`、`BC_Gen5_16`、`BC_Gen5_18`、`BC_Gen5_20`、`BC_Gen5_24`、`BC_Gen5_32`、`BC_Gen5_40`、`BC_Gen5_80`、`BC_M_8`、`BC_M_10`、`BC_M_12`、`BC_M_14`、`BC_M_16`、`BC_M_18`、`BC_M_20`、`BC_M_24`、`BC_M_32`、`BC_M_64`、`BC_M_128`。

- **針對無伺服器計算層中的單一資料庫**

  - 指定計算大小 (服務目標)。 服務目標的可用值為：`GP_S_Gen5_1`、`GP_S_Gen5_2`、`GP_S_Gen5_4`、`GP_S_Gen5_6`、`GP_S_Gen5_8`、`GP_S_Gen5_10`、`GP_S_Gen5_12`、`GP_S_Gen5_14`、`GP_S_Gen5_16`、`GP_S_Gen5_18`、`GP_S_Gen5_20`、`GP_S_Gen5_24`、`GP_S_Gen5_32`、`GP_S_Gen5_40`。

- **針對超大規模服務層中的單一資料庫**

  - 指定計算大小 (服務目標)。 服務目標的可用值為：`HS_GEN4_1`、`HS_GEN4_2`、`HS_GEN4_4`、`HS_GEN4_8`、`HS_GEN4_16`、`HS_GEN4_24`、`HS_Gen5_2`、`HS_Gen5_4`、`HS_Gen5_8`、`HS_Gen5_16`、`HS_Gen5_24`、`HS_Gen5_32`、`HS_Gen5_48`、`HS_Gen5_80`。

如需服務目標描述和大小、版本及服務目標組合的詳細資訊，請參閱 [Azure SQL Database 服務層和效能層級](/azure/azure-sql/database/purchasing-models) \(部分機器翻譯\)、[DTU 資源限制](/azure/sql-database/sql-database-dtu-resource-limits) \(部分機器翻譯\) 和[虛擬核心資源限制](/azure/sql-database/sql-database-dtu-resource-limits) \(部分機器翻譯\)。 目前已移除對 PRS 服務目標的支援。 如有疑問，請使用此電子郵件別名： premium-rs@microsoft.com。

MODIFY (SERVICE_OBJECTIVE = ELASTIC\_POOL (name = \<elastic_pool_name>)   
若要將現有的資料庫新增至彈性集區，請將資料庫的 SERVICE_OBJECTIVE 設定為 ELASTIC_POOL，並提供彈性集區的名稱。 您也可以使用此選項將資料庫變更至相同伺服器內的不同彈性集區。 如需詳細資訊，請參閱[建立和管理 SQL Database 彈性資料庫集區](/azure/azure-sql/database/elastic-pool-overview)。 若要從彈性集區中移除資料庫，請使用 ALTER DATABASE 將 SERVICE_OBJECTIVE 設定為單一資料庫計算大小 (服務目標)。

> [!NOTE]
> 超大規模服務層中的資料庫不得新增至彈性集區。

ADD SECONDARY ON SERVER \<partner_server_name>   
在夥伴伺服器上使用相同名稱來建立異地複寫次要資料庫，其中將本機資料庫設定為異地複寫主要資料庫，然後開始以非同步方式將資料從主要端複寫到新的次要端。 如果次要端上已經有相同名稱的資料庫，命令就會失敗。 此命令會在伺服器的 master 資料庫上執行，該伺服器裝載了成為主要資料庫的本機資料庫。

> [!IMPORTANT]
> 超大規模服務層目前不支援異地複寫。

> [!IMPORTANT]
> 根據預設，會使用與主要或來源資料庫相同的備份儲存體備援來建立次要資料庫。 不支援透過 T-SQL 在建立次要資料庫時變更備份儲存體備援。 

WITH ALLOW_CONNECTIONS { **ALL** | NO }   
未指定 ALLOW_CONNECTIONS 時，預設會設定為 ALL。 如果設定為 ALL，就是允許所有具備適當權限的登入進行連線的唯讀資料庫。

WITH SERVICE_OBJECTIVE { `S0`、`S1`、`S2`、`S3`、`S4`、`S6`、`S7`、`S9`、`S12`、`P1`、`P2`、`P4`、`P6`、`P11`、`P15`、`GP_GEN4_1`、`GP_GEN4_2`、`GP_GEN4_3`、`GP_GEN4_4`、`GP_GEN4_5`、`GP_GEN4_6`、`GP_GEN4_7`、`GP_GEN4_8`、`GP_GEN4_7`、`GP_GEN4_8`、`GP_GEN4_9`、`GP_GEN4_10`、`GP_GEN4_16`、`GP_GEN4_24`、`BC_GEN4_1`、`BC_GEN4_2`、`BC_GEN4_3`、`BC_GEN4_4`、`BC_GEN4_5`、`BC_GEN4_6`、`BC_GEN4_7`、`BC_GEN4_8`、`BC_GEN4_9`、`BC_GEN4_10`、`BC_GEN4_16`、`BC_GEN4_24`、`GP_Gen5_2`、`GP_Gen5_4`、`GP_Gen5_6`、`GP_Gen5_8`、`GP_Gen5_10`、`GP_Gen5_12`、`GP_Gen5_14`、`GP_Gen5_16`、`GP_Gen5_18`、`GP_Gen5_20`、`GP_Gen5_24`、`GP_Gen5_32`、`GP_Gen5_40`、`GP_Gen5_80`、`GP_Fsv2_8`、`GP_Fsv2_10`、`GP_Fsv2_12`、`GP_Fsv2_14`、`GP_Fsv2_16`、`GP_Fsv2_18`、`GP_Fsv2_20`、`GP_Fsv2_24`、`GP_Fsv2_32`、`GP_Fsv2_36`、`GP_Fsv2_72`、`GP_S_Gen5_1`、`GP_S_Gen5_2`、`GP_S_Gen5_4`、`GP_S_Gen5_6`、`GP_S_Gen5_8`、`GP_S_Gen5_10`、`GP_S_Gen5_12`、`GP_S_Gen5_14`、`GP_S_Gen5_16`、`GP_S_Gen5_18`、`GP_S_Gen5_20`、`GP_S_Gen5_24`、`GP_S_Gen5_32`、`GP_S_Gen5_40`、`BC_Gen5_2`、`BC_Gen5_4`、`BC_Gen5_6`、`BC_Gen5_8`、`BC_Gen5_10`、`BC_Gen5_12`、`BC_Gen5_14`、`BC_Gen5_16`、`BC_Gen5_18`、`BC_Gen5_20`、`BC_Gen5_24`、`BC_Gen5_32`、`BC_Gen5_40`、`BC_Gen5_80`、`BC_M_8`、`BC_M_10`、`BC_M_12`、`BC_M_14`、`BC_M_16`、`BC_M_18`、`BC_M_20`、`BC_M_24`、`BC_M_32`、`BC_M_64`、`BC_M_128` }

未指定 SERVICE_OBJECTIVE 時，會在與主要資料庫相同的服務層級建立次要資料庫。 若有指定 SERVICE_OBJECTIVE，則會在指定的層級建立次要資料庫。 此選項支援以成本較低廉的服務層級建立異地複寫次要端。 所指定的 SERVICE_OBJECTIVE 必須是在與來源相同的版本內。 例如，如果版本為 Premium，您便無法指定 S0。

ELASTIC_POOL (name = \<elastic_pool_name>) 未指定 ELASTIC_POOL 時，不會在彈性集區中建立次要資料庫。 已指定 ELASTIC_POOL 時，則會在指定的集區中建立次要資料庫。

> [!IMPORTANT]
> 執行 ADD SECONDARY 命令的使用者必須是主要伺服器上的 DBManager、具備本機資料庫中的 db_owner 成員資格，並且是次要伺服器上的 DBManager。 您必須同時在主要與次要伺服器的防火牆規則底下，將用戶端 IP 位址新增至允許清單。 如果是不同的用戶端 IP 位址，也必須將已在主要伺服器上新增的完全相同用戶端 IP 位址新增至次要資料庫。 在執行 ADD SECONDARY 命令以起始異地複寫之前，必須先完成此步驟。

REMOVE SECONDARY ON SERVER \<partner_server_name> 移除所指定伺服器上指定的異地複寫次要資料庫。 此命令會在裝載主要資料庫之伺服器的 master 資料庫上執行。

> [!IMPORTANT]
> 執行 REMOVE SECONDARY 命令的使用者必須是主要伺服器上的 DBManager。

FAILOVER 將異地複寫合作關係中用來執行命令的次要資料庫升階成主要端，並將目前的主要端降級成新次要端。 在此程序中，異地複寫模式會從非同步模式暫時切換至同步模式。 在容錯移轉程序期間：

1. 主要端會停止接受新的交易。
2. 所有未完成的交易都會排清至次要端。
3. 次要端會變成主要端，然後開始與舊的主要端/新的次要端進行非同步異地複寫。

這個順序可確保不會發生任何資料遺失。 兩個資料庫都無法使用的期間大約是 0-25 秒，即切換角色時。 整個作業應該花費不超過一分鐘的時間。 如果在發出此命令時，無法使用主要資料庫，命令就會失敗，並顯示指出無法使用主要資料庫的錯誤訊息。 如果容錯移轉程序未完成並出現停滯現象，您可以使用強制容錯移轉命令並接受資料遺失，然後，如果您需要復原遺失的資料，便呼叫 devops (CSS) 來復原遺失的資料。

> [!IMPORTANT]
> 執行 FAILOVER 命令的使用者必須同時是主要伺服器和次要伺服器上的 DBManager。

FORCE_FAILOVER_ALLOW_DATA_LOSS 將異地複寫合作關係中用來執行命令的次要資料庫升階成主要端，並將目前的主要端降級成新次要端。 請只在目前主要端不再可供使用的情況下，才使用此命令。 這是僅針對在必須緊急復原可用性而可接受遺失部分資料的災害復原情況而設計。

在強制容錯移轉期間：

1. 指定的次要資料庫會立即變成主要資料庫，並開始接受新的交易。
2. 當原始主要端可以與新的主要端重新連線時，就會在原始主要端上進行增量備份，然後原始主要端會變成新的次要端。
3. 若要從舊主要端上的這個增量備份復原資料，使用者必須進行 devops/CSS。
4. 如果有額外的次要端，這些次要端將會自動重新成新主要端的次要端。 此程序會以非同步方式進行，因此可能會有延遲，直到此程序完成為止。 在重新設定完成之前，次要端仍繼續是舊主要端的次要端。

> [!IMPORTANT]
> 執行 `FORCE_FAILOVER_ALLOW_DATA_LOSS` 命令的使用者必須同時是主要伺服器和次要伺服器上的 `dbmanager` 角色。

## <a name="remarks"></a>備註

若要移除資料庫，請使用 [DROP DATABASE](../../t-sql/statements/drop-database-transact-sql.md)。
若要縮小資料庫大小，請使用 [DBCC SHRINKDATABASE](../../t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql.md)。

`ALTER DATABASE` 陳述式必須在自動認可模式 (預設的交易管理模式) 下執行，且不能用於明確或隱含交易。

清除計畫快取會導致重新編譯所有後續執行計畫，而且可能會導致查詢效能突然暫時下降。 針對計畫快取中每個清除的快取存放區，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 錯誤記錄檔會包含下列資訊訊息：`SQL Server has encountered %d occurrence(s) of cachestore flush for the '%s' cachestore (part of plan cache) due to some database maintenance or reconfigure operations`。 只要在該時間間隔內快取發生排清，這個訊息就會每五分鐘記錄一次。

在下列情況下也會排清程序快取：您針對有預設選項的資料庫執行幾個查詢。 然後卸除資料庫。

## <a name="viewing-database-information"></a>檢視資料庫資訊

您可以利用目錄檢視、系統函數和系統預存程序，以傳回資料庫、檔案和檔案群組的相關資訊。

## <a name="permissions"></a>權限

若要變更資料庫，登入必須是伺服器層級主體登入 (由佈建程序所建立)、master 中 `dbmanager` 資料庫角色的成員、目前資料庫中 `db_owner` 資料庫角色的成員，或資料庫的 `dbo`。

## <a name="examples"></a>範例

### <a name="a-check-the-edition-options-and-change-them"></a>A. 檢查版本選項並變更它們

設定資料庫 db1 的版本和大小上限：

```sql
SELECT Edition = DATABASEPROPERTYEX('db1', 'EDITION'),
        ServiceObjective = DATABASEPROPERTYEX('db1', 'ServiceObjective'),
        MaxSizeInBytes =  DATABASEPROPERTYEX('db1', 'MaxSizeInBytes');

ALTER DATABASE [db1] MODIFY (EDITION = 'Premium', MAXSIZE = 1024 GB, SERVICE_OBJECTIVE = 'P15');
```

### <a name="b-moving-a-database-to-a-different-elastic-pool"></a>B. 將資料庫移至不同的彈性集區

將現有的資料庫移至名為 pool1 的集區：

```sql
ALTER DATABASE db1
MODIFY ( SERVICE_OBJECTIVE = ELASTIC_POOL ( name = pool1 ) ) ;
```

### <a name="c-add-a-geo-replication-secondary"></a>C. 新增異地複寫次要端

在本機伺服器之 db1 的伺服器 `secondaryserver` 上建立可讀取的次要資料庫 db1。

```sql
ALTER DATABASE db1
ADD SECONDARY ON SERVER secondaryserver
WITH ( ALLOW_CONNECTIONS = ALL )
```

### <a name="d-remove-a-geo-replication-secondary"></a>D. 移除異地複寫次要端

移除伺服器 `secondaryserver`上的次要資料庫 db1。

```sql
ALTER DATABASE db1
REMOVE SECONDARY ON SERVER testsecondaryserver
```

### <a name="e-failover-to-a-geo-replication-secondary"></a>E. 容錯移轉至異地複寫次要端

當在伺服器 `secondaryserver` 上執行時，會將伺服器 `secondaryserver` 上的次要資料庫 db1 升階成新的主要資料庫。

```sql
ALTER DATABASE db1 FAILOVER
```

### <a name="e-force-failover-to-a-geo-replication-secondary-with-data-loss"></a>E. 強制容錯移轉至異地複寫次要端 (可能遺失資料)

在伺服器 `secondaryserver` 上執行時，如果主要伺服器變得無法使用，則強制讓伺服器 `secondaryserver` 上的次要資料庫 db1 成為新的主要資料庫。 此選項可能會造成資料遺失。

```sql
ALTER DATABASE db1 FORCE_FAILOVER_ALLOW_DATA_LOSS
```

### <a name="g-update-a-single-database-to-service-tier-s0-standard-edition-performance-level-0"></a>G. 將單一資料庫更新為服務層級 S0 (標準版，效能等級 0)

將單一資料庫更新至計算大小 (服務目標) 為 S0 且大小上限為 250 GB 的標準版 (服務層級)。

```sql
ALTER DATABASE [db1] MODIFY (EDITION = 'Standard', MAXSIZE = 250 GB, SERVICE_OBJECTIVE = 'S0');
```

### <a name="h-update-the-backup-storage-redundancy-of-a-database"></a>H. 更新資料庫的備份儲存體備援

將資料庫的備份儲存體備援更新至區域備援。 此資料庫未來所有的備份都會使用新的設定。 這也包括時間點還原備份與長期保留備份 (如果已設定)。 

```sql
ALTER DATABASE db1 MODIFY BACKUP_STORAGE_REDUNDANCY = 'ZONE'
```

## <a name="see-also"></a>另請參閱

- [CREATE DATABASE - Azure SQL Database](../../t-sql/statements/create-database-transact-sql.md?view=azuresqldb-current&preserve-view=true)
- [DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md)
- [DROP DATABASE](../../t-sql/statements/drop-database-transact-sql.md)
- [SET TRANSACTION ISOLATION LEVEL](../../t-sql/statements/set-transaction-isolation-level-transact-sql.md)
- [EVENTDATA](../../t-sql/functions/eventdata-transact-sql.md)
- [sp_configure](../../relational-databases/system-stored-procedures/sp-configure-transact-sql.md)
- [sp_spaceused](../../relational-databases/system-stored-procedures/sp-spaceused-transact-sql.md)
- [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md)
- [sys.database_files](../../relational-databases/system-catalog-views/sys-database-files-transact-sql.md)
- [sys.database_mirroring_witnesses](../../relational-databases/system-catalog-views/database-mirroring-witness-catalog-views-sys-database-mirroring-witnesses.md)
- [sys.data_spaces](../../relational-databases/system-catalog-views/sys-data-spaces-transact-sql.md)
- [sys.filegroups](../../relational-databases/system-catalog-views/sys-filegroups-transact-sql.md)
- [sys.master_files](../../relational-databases/system-catalog-views/sys-master-files-transact-sql.md)
- [系統資料庫](../../relational-databases/databases/system-databases.md)

::: moniker-end
::: moniker range="=azuresqldb-mi-current"

:::row:::
    :::column:::
        [SQL Server](alter-database-transact-sql.md?view=sql-server-ver15&preserve-view=true)
    :::column-end:::
    :::column:::
        [SQL Database](alter-database-transact-sql.md?view=azuresqldb-current&preserve-view=true)
    :::column-end:::
    :::column:::
        **_\* SQL 受控執行個體\*_** &nbsp;
    :::column-end:::
    :::column:::
        [Azure Synapse<br />Analytics](alter-database-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)
    :::column-end:::
    :::column:::
        [Analytics Platform<br />System (PDW)](alter-database-transact-sql.md?view=aps-pdw-2016-au7&preserve-view=true)
    :::column-end:::
:::row-end:::



&nbsp;

## <a name="overview-azure-sql-managed-instance"></a>概觀：Azure SQL 受控執行個體

在 [!INCLUDE[ssSDSMIfull](../../includes/sssdsmifull-md.md)] 中，使用此陳述式來設定資料庫選項。

由於長度的關係，`ALTER DATABASE` 語法會分成多篇文章。

ALTER DATABASE   
目前的文章提供的語法與相關資訊，可用於設定檔案和檔案群組選項、設定資料庫選項，以及設定資料庫相容性層級。  
  
[ALTER DATABASE 檔案及檔案群組選項](../../t-sql/statements/alter-database-transact-sql-file-and-filegroup-options.md?&tabs=sqldbmi)   
提供在資料庫中新增和移除檔案及檔案群組的語法與相關資訊，以及變更檔案及檔案群組屬性的語法與相關資訊。  
  
[ALTER DATABASE SET 選項](../../t-sql/statements/alter-database-transact-sql-set-options.md?&tabs=sqldbmi)   
提供使用 ALTER DATABASE 的 SET 選項來變更資料庫屬性的語法與相關資訊。  
  
[ALTER DATABASE 相容性層級](../../t-sql/statements/alter-database-transact-sql-compatibility-level.md?&tabs=sqldbmi)   
可為與資料庫相容性層級相關的 ALTER DATABASE SET 選項提供語法與相關資訊。  

## <a name="syntax"></a>語法

```syntaxsql
-- Azure SQL Database Syntax  
ALTER DATABASE { database_name | CURRENT }  
{
    MODIFY NAME = new_database_name
  | COLLATE collation_name
  | <file_and_filegroup_options>  
  | SET <option_spec> [ ,...n ]  
}  
[;]

<file_and_filegroup_options>::=  
  <add_or_modify_files>::=  
  <filespec>::=
  <add_or_modify_filegroups>::=  
  <filegroup_updatability_option>::=  

<option_spec> ::=
{
    <auto_option>
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
  | <temporal_history_retention>
  | <compatibility_level>
      { 150 | 140 | 130 | 120 | 110 | 100 | 90 }

}  

```

## <a name="arguments"></a>引數

*database_name*   
這是要修改之資料庫的名稱。

CURRENT   
指定應該改變正在使用中的目前資料庫。

## <a name="remarks"></a>備註

若要移除資料庫，請使用 [DROP DATABASE](../../t-sql/statements/drop-database-transact-sql.md)。
若要縮小資料庫大小，請使用 [DBCC SHRINKDATABASE](../../t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql.md)。

`ALTER DATABASE` 陳述式必須在自動認可模式 (預設的交易管理模式) 下執行，且不能用於明確或隱含交易。

清除計畫快取會導致重新編譯所有後續執行計畫，而且可能會導致查詢效能突然暫時下降。 針對每次清除計畫快取的快取存放區，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 錯誤記錄檔會包含下列資訊訊息：「由於某些資料庫維護或重新設定作業，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的 '%s' 快取存放區 (計畫快取的一部分) 發生 %d 次快取存放區排清」。 只要在該時間間隔內快取發生排清，這個訊息就會每五分鐘記錄一次。

在針對具有預設選項的資料庫執行數個查詢時，系統也會排清計畫快取。 然後卸除資料庫。

某些 `ALTER DATABASE` 語句需要執行資料庫的獨佔鎖定。 這就是為什麼當另一個使用中的 proces cannot 持有資料庫鎖定時，它們可能會失敗。 像這樣的情況所報告的錯誤是 `Msg 5061, Level 16, State 1, Line 38` 使用 message `ALTER DATABASE failed because a lock could not be placed on database '<database name>'. Try again later` 。 這通常是暫時性的失敗，若要解決此問題，請在資料庫上的所有鎖定都釋出之後，重試失敗的 ALTER DATABASE 語句。 系統檢視會 `sys.dm_tran_locks` 保存作用中鎖定的資訊。 若要檢查資料庫上是否有共用或獨佔鎖定，請使用下列查詢。

```sql
SELECT
    resource_type, resource_database_id, request_mode, request_type, request_status, request_session_id 
FROM 
    sys.dm_tran_locks
WHERE
    resource_database_id = DB_ID('testdb')
```

## <a name="viewing-database-information"></a>檢視資料庫資訊

您可以利用目錄檢視、系統函數和系統預存程序，以傳回資料庫、檔案和檔案群組的相關資訊。

## <a name="permissions"></a>權限

只有伺服器層級主體登入 (由佈建程序所建立) 或 `dbcreator` 資料庫角色成員可以改變資料庫。

> [!IMPORTANT]
> 資料庫的擁有者不能改變資料庫，除非他們是 `dbcreator` 角色的成員。

## <a name="examples"></a>範例

下列範例示範如何設定自動調整及如何在受控執行個體中新增檔案。

```sql
ALTER DATABASE WideWorldImporters
  SET AUTOMATIC_TUNING ( FORCE_LAST_GOOD_PLAN = ON)

ALTER DATABASE WideWorldImporters
  ADD FILE (NAME = 'data_17')
```

## <a name="see-also"></a>另請參閱

- [CREATE DATABASE - Azure SQL Database](../../t-sql/statements/create-database-transact-sql.md?view=azuresqldb-mi-current&preserve-view=true)
- [DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md)
- [DROP DATABASE](../../t-sql/statements/drop-database-transact-sql.md)
- [SET TRANSACTION ISOLATION LEVEL](../../t-sql/statements/set-transaction-isolation-level-transact-sql.md)
- [EVENTDATA](../../t-sql/functions/eventdata-transact-sql.md)
- [sp_configure](../../relational-databases/system-stored-procedures/sp-configure-transact-sql.md)
- [sp_spaceused](../../relational-databases/system-stored-procedures/sp-spaceused-transact-sql.md)
- [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md)
- [sys.database_files](../../relational-databases/system-catalog-views/sys-database-files-transact-sql.md)
- [sys.database_mirroring_witnesses](../../relational-databases/system-catalog-views/database-mirroring-witness-catalog-views-sys-database-mirroring-witnesses.md)[sys.data_spaces](../../relational-databases/system-catalog-views/sys-data-spaces-transact-sql.md)
- [sys.filegroups](../../relational-databases/system-catalog-views/sys-filegroups-transact-sql.md)
- [sys.master_files](../../relational-databases/system-catalog-views/sys-master-files-transact-sql.md)
- [系統資料庫](../../relational-databases/databases/system-databases.md)

::: moniker-end
::: moniker range="=azure-sqldw-latest"

:::row:::
    :::column:::
        [SQL Server](alter-database-transact-sql.md?view=sql-server-ver15&preserve-view=true)
    :::column-end:::
    :::column:::
        [SQL Database](alter-database-transact-sql.md?view=azuresqldb-current&preserve-view=true)
    :::column-end:::
    :::column:::
        [SQL 受控執行個體](alter-database-transact-sql.md?view=azuresqldb-mi-current&preserve-view=true)
    :::column-end:::
    :::column:::
        **_\* Azure Synapse<br />Analytics \*_** &nbsp;
    :::column-end:::
    :::column:::
        [Analytics Platform<br />System (PDW)](alter-database-transact-sql.md?view=aps-pdw-2016-au7&preserve-view=true)
    :::column-end:::
:::row-end:::

&nbsp;

## <a name="overview-azure-synapse-analytics"></a>概觀：Azure Synapse Analytics

在 Azure Synapse 中，`ALTER DATABASE` 會修改資料庫的名稱、大小上限或服務目標。

由於長度的關係，`ALTER DATABASE` 語法會分成多篇文章。

[ALTER DATABASE SET 選項](../../t-sql/statements/alter-database-transact-sql-set-options.md)   
提供使用 ALTER DATABASE 的 SET 選項來變更資料庫屬性的語法與相關資訊。

## <a name="syntax"></a>語法

### <a name="dedicated-sql-pool"></a>[專用 SQL 集區](#tab/sqlpool)
```syntaxsql
ALTER DATABASE { database_name | CURRENT }
{
  MODIFY NAME = new_database_name
| MODIFY ( <edition_option> [, ... n] )
| SET <option_spec> [ ,...n ] [ WITH <termination> ]
}
[;]

<edition_option> ::=
      MAXSIZE = {
            250 | 500 | 750 | 1024 | 5120 | 10240 | 20480
          | 30720 | 40960 | 51200 | 61440 | 71680 | 81920
          | 92160 | 102400 | 153600 | 204800 | 245760
      } GB
      | SERVICE_OBJECTIVE = {
            'DW100' | 'DW200' | 'DW300' | 'DW400' | 'DW500'
          | 'DW600' | 'DW1000' | 'DW1200' | 'DW1500' | 'DW2000'
          | 'DW3000' | 'DW6000' | 'DW500c' | 'DW1000c' | 'DW1500c'
          | 'DW2000c' | 'DW2500c' | 'DW3000c' | 'DW5000c' | 'DW6000c'
          | 'DW7500c' | 'DW10000c' | 'DW15000c' | 'DW30000c'
      }
```
### <a name="serverless-sql-pool"></a>[無伺服器 SQL 集區](#tab/sqlod)
```syntaxsql
ALTER DATABASE { database_name | Current } 
{ 
    COLLATE collation_name 
  | SET { <optionspec> [ ,...n ] } 
} 
[;] 

<optionspec> ::= 
{ 
    <auto_option> 
  | <sql_option> 
}  

<auto_option> ::= 
{ 
    AUTO_CREATE_STATISTICS { OFF | ON [ ( INCREMENTAL = { ON | OFF } ) ] } 
  | AUTO_UPDATE_STATISTICS { ON | OFF } 
  | AUTO_UPDATE_STATISTICS_ASYNC { ON | OFF } 
} 

<sql_option> ::= 
{ 
    ANSI_NULL_DEFAULT { ON | OFF } 
  | ANSI_NULLS { ON | OFF } 
  | ANSI_PADDING { ON | OFF } 
  | ANSI_WARNINGS { ON | OFF } 
  | ARITHABORT { ON | OFF } 
  | COMPATIBILITY_LEVEL = { 140 | 130 | 120 | 110 | 100 } 
  | CONCAT_NULL_YIELDS_NULL { ON | OFF } 
  | NUMERIC_ROUNDABORT { ON | OFF } 
  | QUOTED_IDENTIFIER { ON | OFF } 
} 
```
---

## <a name="arguments"></a>引數

*database_name*   
指定要修改的資料庫名稱。

MODIFY NAME = *new_database_name*   
使用指定為 *new_database_name* 的名稱來重新命名資料庫。

MAXSIZE   
預設為 245,760 GB (240 TB)。

**適用範圍：** 針對「計算第 1 代」最佳化

資料庫的允許大小上限。 資料庫不可增大超過 MAXSIZE。

**適用範圍：** 針對「計算第 2 代」最佳化

資料庫中資料列存放區資料的允許大小上限。 儲存在資料列存放區資料表的資料、資料行存放區索引的差異存放區，或叢集資料行存放區索引的非叢集索引，不可增大超過 MAXSIZE。 壓縮成資料行存放區格式的資料大小沒有大小限制，因此不受 MAXSIZE 限制。

SERVICE_OBJECTIVE   
指定計算大小 (服務目標)。 如需適用於 Azure Synapse 之服務目標的詳細資訊，請參閱[資料倉儲單位 (DWU)](/azure/sql-data-warehouse/what-is-a-data-warehouse-unit-dwu-cdwu) \(部分機器翻譯\)。

## <a name="permissions"></a>權限

需要下列權限：

- 由佈建程序建立的伺服器層級主體登入，或
- `dbmanager` 資料庫角色的成員。

除非資料庫擁有者是 `dbmanager` 角色的成員，否則無法變更資料庫。

## <a name="general-remarks"></a>一般備註

目前資料庫必須是與您變更的資料庫不同的資料庫，因此 **必須在連線至 master 資料庫時執行 ALTER**。

SQL Analytics 中的 COMPATIBILITY_LEVEL 預設已設定為 130 且無法變更。 如需詳細資料，請參閱 [Azure SQL Database 中改善的查詢效能與相容性層級 130](./alter-database-transact-sql-compatibility-level.md)。

> [!NOTE]
> COMPATIBILITY_LEVEL 僅適用於已佈建資源 (集區)。

## <a name="limitations-and-restrictions"></a>限制事項

若要執行 `ALTER DATABASE`，資料庫必須在線上，而且不能處於暫停狀態。

`ALTER DATABASE` 陳述式必須以自動認可模式 (即預設的交易管理模式) 執行。 這是設定於連線設定中。

`ALTER DATABASE` 陳述式不能是使用者定義交易的一部分。

您無法變更資料庫定序。

## <a name="examples"></a>範例

執行這些範例之前，請確定您要變更的資料庫不是目前資料庫。 目前資料庫必須是與您變更的資料庫不同的資料庫，因此 **必須在連線至 master 資料庫時執行 ALTER**。

### <a name="a-change-the-name-of-the-database"></a>A. 變更資料庫的名稱

```sql
ALTER DATABASE AdventureWorks2012
MODIFY NAME = Northwind;
```

### <a name="b-change-max-size-for-the-database"></a>B. 變更資料庫的大小上限

```sql
ALTER DATABASE dw1 MODIFY ( MAXSIZE=10240 GB );
```

### <a name="c-change-the-compute-size-service-objective"></a>C. 變更計算大小 (服務目標)

```sql
ALTER DATABASE dw1 MODIFY ( SERVICE_OBJECTIVE= 'DW1200' );
```

### <a name="d-change-the-max-size-and-the-compute-size-service-objective"></a>D. 變更大小上限和計算大小 (服務目標)

```sql
ALTER DATABASE dw1 MODIFY ( MAXSIZE=10240 GB, SERVICE_OBJECTIVE= 'DW1200' );
```

## <a name="see-also"></a>另請參閱

- [CREATE DATABASE (Azure Synapse Analytics)](../../t-sql/statements/create-database-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)
- [Azure Synapse Analytics 參考文章清單](/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-reference-tsql-language-elements) \(部分機器翻譯\)

::: moniker-end
::: moniker range=">=aps-pdw-2016"

:::row:::
    :::column:::
        [SQL Server](alter-database-transact-sql.md?view=sql-server-ver15&preserve-view=true)
    :::column-end:::
    :::column:::
        [SQL Database](alter-database-transact-sql.md?view=azuresqldb-current&preserve-view=true)
    :::column-end:::
    :::column:::
        [SQL 受控執行個體](alter-database-transact-sql.md?view=azuresqldb-mi-current&preserve-view=true)
    :::column-end:::
    :::column:::
        [Azure Synapse<br />Analytics](alter-database-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)
    :::column-end:::
    :::column:::
        **_\* Analytics<br />Platform System (PDW) \*_** &nbsp;
    :::column-end:::
:::row-end:::

&nbsp;

## <a name="overview-analytics-platform-system"></a>概觀：分析平台系統

修改 PDW 中複寫資料表、分散式資料表和交易記錄的資料庫大小上限選項。 在資料庫的大小成長或壓縮時，使用此陳述式來管理它的磁碟空間配置。 本文也會描述在 PDW 中設定資料庫選項的相關語法。

## <a name="syntax"></a>語法

```syntaxsql
-- Analytics Platform System
ALTER DATABASE database_name
    SET ( <set_database_options> | <db_encryption_option> )
[;]

<set_database_options> ::=
{
    AUTOGROW = { ON | OFF }
    | REPLICATED_SIZE = size [GB]
    | DISTRIBUTED_SIZE = size [GB]
    | LOG_SIZE = size [GB]
    | SET AUTO_CREATE_STATISTICS { ON | OFF }
    | SET AUTO_UPDATE_STATISTICS { ON | OFF }
    | SET AUTO_UPDATE_STATISTICS_ASYNC { ON | OFF }
}

<db_encryption_option> ::=
    ENCRYPTION { ON | OFF }
```

## <a name="arguments"></a>引數

*database_name*   
要修改之資料庫的名稱。 若要顯示設備上資料庫的清單，請使用 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md)。

AUTOGROW = { ON | OFF }   
更新 AUTOGROW 選項。 當 AUTOGROW 為 ON 時，[!INCLUDE[ssPDW](../../includes/sspdw-md.md)]會視需要針對複寫資料表、分散式資料表及交易記錄自動提高配置的空間，以適應儲存空間需求的增長。 當 AUTOGROW 為 OFF 時，如果複寫資料表、分散式資料表或交易記錄超過大小上限設定，[!INCLUDE[ssPDW](../../includes/sspdw-md.md)]就會傳回錯誤。

REPLICATED_SIZE = *size* [GB]   
指定每個計算節點的新 GB 上限，以用來儲存要改變之資料庫中的所有複寫資料表。 如果您正在規劃設備儲存空間，就必須將 REPLICATED_SIZE 乘以設備中計算節點的數目。

DISTRIBUTED_SIZE = *size* [GB]   
指定每個資料庫的新 GB 上限，以用來儲存要改變之資料庫中的所有分散式資料表。 此大小會分佈於設備中的所有計算節點上。

LOG_SIZE = *size* [GB]   
指定每個資料庫的新 GB 上限，以用來儲存要改變之資料庫中的所有交易記錄。 此大小會分佈於設備中的所有計算節點上。

ENCRYPTION { ON | OFF }   
設定資料庫要加密 (ON) 或是不要加密 (OFF)。 只有在將 [sp_pdw_database_encryption](../../relational-databases/system-stored-procedures/sp-pdw-database-encryption-sql-data-warehouse.md) \(英文\) 設為 **1** 時，才能針對[!INCLUDE[ssPDW](../../includes/sspdw-md.md)]設定加密。 您必須先建立資料庫加密金鑰，才能設定透明資料加密。 如需資料庫加密的詳細資訊，請參閱[透明資料加密 (TDE)](../../relational-databases/security/encryption/transparent-data-encryption.md)。

SET AUTO_CREATE_STATISTICS { ON | OFF }   
當自動建立統計資料選項 AUTO_CREATE_STATISTICS 為 ON 時，查詢最佳化工具就會視需要針對查詢述詞中的個別資料行來建立統計資料，以便改善查詢計劃的基數估計值。 這些單一資料行統計資料是針對在現有統計資料物件中尚未具有長條圖的資料行建立的。

若為升級至 AU7 之後建立的新資料庫，預設值是 ON。 若為在升級之前建立的資料庫，預設值是 OFF。

如需統計資料的詳細資訊，請參閱[統計資料](../../relational-databases/statistics/statistics.md)

SET AUTO_UPDATE_STATISTICS { ON | OFF }   
當自動更新統計資料選項 AUTO_UPDATE_STATISTICS 為 ON 時，查詢最佳化工具會判斷統計資料何時過期，然後在查詢使用統計資料時加以更新。 當作業插入、更新、刪除或合併變更資料表或索引檢視表中的資料分佈之後，統計資料就會變成過期。 查詢最佳化工具會計算自從上次更新統計資料以來資料修改的次數，並且比較修改次數與臨界值，藉以判斷統計資料可能過期的時間。 此臨界值是以資料表或索引檢視表中的資料列數目為基礎。

若為升級至 AU7 之後建立的新資料庫，預設值是 ON。 若為在升級之前建立的資料庫，預設值是 OFF。

如需統計資料的詳細資訊，請參閱[統計資料](../../relational-databases/statistics/statistics.md)。

SET AUTO_UPDATE_STATISTICS_ASYNC { ON | OFF }   
非同步統計資料更新選項 AUTO_UPDATE_STATISTICS_ASYNC 會決定查詢最佳化工具要使用同步或非同步統計資料更新。 AUTO_UPDATE_STATISTICS_ASYNC 選項會套用至針對索引所建立的統計資料物件、查詢述詞中的單一資料行，以及使用CREATE STATISTICS 陳述式所建立的統計資料。

若為升級至 AU7 之後建立的新資料庫，預設值是 ON。 若為在升級之前建立的資料庫，預設值是 OFF。

如需統計資料的詳細資訊，請參閱[統計資料](../../relational-databases/statistics/statistics.md)。

## <a name="permissions"></a>權限

需要資料庫的 `ALTER` 權限。

## <a name="error-messages"></a>錯誤訊息

如果已停用自動統計資料，而且您嘗試改變統計資料設定，PDW 會輸出錯誤 `This option is not supported in PDW`。 系統管理員可以藉由啟用功能參數 [AutoStatsEnabled](../../analytics-platform-system/appliance-feature-switch.md) 來啟用自動統計資料。

## <a name="general-remarks"></a>一般備註

`REPLICATED_SIZE`、`DISTRIBUTED_SIZE` 和 `LOG_SIZE` 的值可以大於、等於或小於資料庫的目前值。

## <a name="limitations-and-restrictions"></a>限制事項

成長和壓縮作業很近似。 產生的實際大小會因大小參數而異。

[!INCLUDE[ssPDW](../../includes/sspdw-md.md)]不會以不可部分完成之作業的形式來執行 ALTER DATABASE 陳述式。 如果陳述式在執行期間中止，系統將會保留已發生的變更。

只有在系統管理員啟用了自動統計資料時，統計資料設定才會作用。如果您是系統管理員，請使用功能參數 [AutoStatsEnabled](../../analytics-platform-system/appliance-feature-switch.md) 來啟用或停用自動統計資料。

## <a name="locking-behavior"></a>鎖定行為

在 DATABASE 物件上採取共用鎖定。 您無法改變有另一個使用者正在讀取或寫入的資料庫。 這包括已在資料庫上發出 [USE](../language-elements/use-transact-sql.md) \(英文\) 陳述式的工作階段。

## <a name="performance"></a>效能

根據資料庫內實際資料的大小及磁碟上的片段程度而定，壓縮資料庫可能需要大量的時間與系統資源。 例如，壓縮資料庫可能需要數小時以上的時間。

## <a name="determining-encryption-progress"></a>判斷加密進度

使用下列查詢來判斷資料庫透明資料加密的進度 (以百分比表示)：

```sql
WITH
database_dek AS (
    SELECT ISNULL(db_map.database_id, dek.database_id) AS database_id,
        dek.encryption_state, dek.percent_complete,
        dek.key_algorithm, dek.key_length, dek.encryptor_thumbprint,
        type
    FROM sys.dm_pdw_nodes_database_encryption_keys AS dek
    INNER JOIN sys.pdw_nodes_pdw_physical_databases AS node_db_map
        ON dek.database_id = node_db_map.database_id
        AND dek.pdw_node_id = node_db_map.pdw_node_id
    LEFT JOIN sys.pdw_database_mappings AS db_map
        ON node_db_map .physical_name = db_map.physical_name
    INNER JOIN sys.dm_pdw_nodes nodes
        ON nodes.pdw_node_id = dek.pdw_node_id
    WHERE dek.encryptor_thumbprint <> 0x
),
dek_percent_complete AS (
    SELECT database_dek.database_id, AVG(database_dek.percent_complete) AS percent_complete
    FROM database_dek
    WHERE type = 'COMPUTE'
    GROUP BY database_dek.database_id
)
SELECT DB_NAME( database_dek.database_id ) AS name,
    database_dek.database_id,
    ISNULL(
       (SELECT TOP 1 dek_encryption_state.encryption_state
        FROM database_dek AS dek_encryption_state
        WHERE dek_encryption_state.database_id = database_dek.database_id
        ORDER BY (CASE encryption_state
            WHEN 3 THEN -1
            ELSE encryption_state
            END) DESC), 0)
        AS encryption_state,
dek_percent_complete.percent_complete,
database_dek.key_algorithm, database_dek.key_length, database_dek.encryptor_thumbprint
FROM database_dek
INNER JOIN dek_percent_complete
    ON dek_percent_complete.database_id = database_dek.database_id
WHERE type = 'CONTROL';
```

如需示範實作 TDE 所有步驟的完整範例，請參閱[透明資料加密 (TDE)](../../relational-databases/security/encryption/transparent-data-encryption.md)。

## <a name="examples-sspdw"></a>範例：[!INCLUDE[ssPDW](../../includes/sspdw-md.md)]

### <a name="a-altering-the-autogrow-setting"></a>A. 改變 AUTOGROW 設定

針對 `CustomerSales` 資料庫，將 AUTOGROW 設為 ON。

```sql
ALTER DATABASE CustomerSales
    SET ( AUTOGROW = ON );
```

### <a name="b-altering-the-maximum-storage-for-replicated-tables"></a>B. 改變複寫資料表的儲存空間上限

下列範例會針對 `CustomerSales` 資料庫，將複寫資料表儲存空間限制設為 1 GB。 這是每個計算節點的儲存空間限制。

```sql
ALTER DATABASE CustomerSales
    SET ( REPLICATED_SIZE = 1 GB );
```

### <a name="c-altering-the-maximum-storage-for-distributed-tables"></a>C. 改變分散式資料表的儲存空間上限

 下列範例會針對 `CustomerSales` 資料庫，將分散式資料表儲存空間限制設為 1000 GB (1 TB)。 這是設備上所有計算節點的組合儲存空間限制，而非每個計算節點的儲存空間限制。

```sql
ALTER DATABASE CustomerSales
    SET ( DISTRIBUTED_SIZE = 1000 GB );
```

### <a name="d-altering-the-maximum-storage-for-the-transaction-log"></a>D. 改變交易記錄的儲存空間上限

 下列範例會更新 `CustomerSales` 資料庫，使其可在設備上擁有最多 10 GB 的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 交易記錄大小。

```sql
ALTER DATABASE CustomerSales
    SET ( LOG_SIZE = 10 GB );
```

### <a name="e-check-for-current-statistics-values"></a>E. 檢查目前的統計資料值

下列查詢會傳回所有資料庫目前的統計資料值。 值 1 表示開啟此功能，而 0 表示關閉此功能。

```sql
SELECT NAME,
    is_auto_create_stats_on,
    is_auto_update_stats_on,
    is_auto_update_stats_async_on
FROM sys.databases;
```

### <a name="f-enable-auto-create-and-auto-update-stats-for-a-database"></a>F. 啟用資料庫的自動建立與自動更新統計資料

使用下列陳述式，針對資料庫 CustomerSales 以自動且非同步的方式啟用建立和更新統計資料功能。 這會視需要建立和更新單一資料行統計資料，以建立高品質的查詢計劃。

```sql
ALTER DATABASE CustomerSales
    SET AUTO_CREATE_STATISTICS ON;
ALTER DATABASE CustomerSales
    SET AUTO_UPDATE_STATISTICS ON;
ALTER DATABASE
    SET AUTO_UPDATE_STATISTICS_ASYNC ON;
```

## <a name="see-also"></a>另請參閱

- [CREATE DATABASE - Analytics Platform System](../../t-sql/statements/create-database-transact-sql.md?view=aps-pdw-2016-au7&preserve-view=true)
- [DROP DATABASE](../../t-sql/statements/drop-database-transact-sql.md)

::: moniker-end
