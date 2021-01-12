---
description: ALTER INDEX (Transact-SQL)
title: ALTER INDEX (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 08/21/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- ALTER INDEX
- ALTER_INDEX_TSQL
dev_langs:
- t-sql
helpviewer_keywords:
- indexes [SQL Server], reorganizing
- ALTER INDEX statement
- indexes [SQL Server], disabling
- online index operations
- index reorganization [SQL Server]
- ALLOW_ROW_LOCKS option
- ALL keyword
- reorganizing indexes
- constraints [SQL Server], indexes
- row locks [SQL Server]
- index rebuilding [SQL Server]
- rebuilding indexes
- locking [SQL Server], indexes
- partitioned indexes [SQL Server], rebuilding
- defragmenting indexes
- disabling indexes
- XML indexes [SQL Server], modifying
- index modifications [SQL Server]
- indexes [SQL Server], modifying
- index options [SQL Server]
- modifying indexes
- index disabling [SQL Server]
- MAXDOP index option, ALTER INDEX statement
- spatial indexes [SQL Server], modifying
- indexes [SQL Server], options
- ALLOW_PAGE_LOCKS option
- page locks [SQL Server]
- index rebuild [SQL Server]
- index reorganize [SQL Server]
ms.assetid: b796c829-ef3a-405c-a784-48286d4fb2b9
author: pmasl
ms.author: pelopes
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 7854c78f0643294b1b5111c1b6f2e0b0ef07afa6
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98099570"
---
# <a name="alter-index-transact-sql"></a>ALTER INDEX (Transact-SQL)

[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  藉由停用、重建或重新組織索引或設定索引選項，修改現有的資料表或檢視表索引 (資料列存放區、資料行存放區或 XML)。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
-- Syntax for SQL Server and Azure SQL Database
  
ALTER INDEX { index_name | ALL } ON <object>  
{  
      REBUILD {  
            [ PARTITION = ALL ] [ WITH ( <rebuild_index_option> [ ,...n ] ) ]   
          | [ PARTITION = partition_number [ WITH ( <single_partition_rebuild_index_option> ) [ ,...n ] ]  
      }  
    | DISABLE  
    | REORGANIZE  [ PARTITION = partition_number ] [ WITH ( <reorganize_option>  ) ]  
    | SET ( <set_index_option> [ ,...n ] )   
    | RESUME [WITH (<resumable_index_options>,[...n])]
    | PAUSE
    | ABORT
}  
[ ; ]  
  
<object> ::=   
{  
    { database_name.schema_name.table_or_view_name | schema_name.table_or_view_name | table_or_view_name }  
}  
  
<rebuild_index_option > ::=  
{  
      PAD_INDEX = { ON | OFF }  
    | FILLFACTOR = fillfactor   
    | SORT_IN_TEMPDB = { ON | OFF }  
    | IGNORE_DUP_KEY = { ON | OFF }  
    | STATISTICS_NORECOMPUTE = { ON | OFF }  
    | STATISTICS_INCREMENTAL = { ON | OFF }  
    | ONLINE = {   
          ON [ ( <low_priority_lock_wait> ) ]   
        | OFF } 
    | RESUMABLE = { ON | OFF } 
    | MAX_DURATION = <time> [MINUTES}     
    | ALLOW_ROW_LOCKS = { ON | OFF }  
    | ALLOW_PAGE_LOCKS = { ON | OFF }  
    | MAXDOP = max_degree_of_parallelism  
    | DATA_COMPRESSION = { NONE | ROW | PAGE | COLUMNSTORE | COLUMNSTORE_ARCHIVE }   
        [ ON PARTITIONS ( {<partition_number> [ TO <partition_number>] } [ , ...n ] ) ]  
}  
  
<single_partition_rebuild_index_option> ::=  
{  
      SORT_IN_TEMPDB = { ON | OFF }  
    | MAXDOP = max_degree_of_parallelism  
    | RESUMABLE = { ON | OFF } 
    | MAX_DURATION = <time> [MINUTES}     
    | DATA_COMPRESSION = { NONE | ROW | PAGE | COLUMNSTORE | COLUMNSTORE_ARCHIVE } }  
    | ONLINE = { ON [ ( <low_priority_lock_wait> ) ] | OFF }  
}  
  
<reorganize_option>::=  
{  
       LOB_COMPACTION = { ON | OFF }  
    |  COMPRESS_ALL_ROW_GROUPS =  { ON | OFF}  
}  
  
<set_index_option>::=  
{  
      ALLOW_ROW_LOCKS = { ON | OFF }  
    | ALLOW_PAGE_LOCKS = { ON | OFF }  
    | OPTIMIZE_FOR_SEQUENTIAL_KEY = { ON | OFF }
    | IGNORE_DUP_KEY = { ON | OFF }  
    | STATISTICS_NORECOMPUTE = { ON | OFF }  
    | COMPRESSION_DELAY= { 0 | delay [Minutes] }  
}  

<resumable_index_option> ::=
 { 
    MAXDOP = max_degree_of_parallelism
    | MAX_DURATION =<time> [MINUTES]
    | <low_priority_lock_wait>  
 }
 
<low_priority_lock_wait>::=  
{  
    WAIT_AT_LOW_PRIORITY ( MAX_DURATION = <time> [ MINUTES ] ,   
                          ABORT_AFTER_WAIT = { NONE | SELF | BLOCKERS } )  
}  

```  
  
```syntaxsql
-- Syntax for Azure Synapse Analytics and Parallel Data Warehouse 
  
ALTER INDEX { index_name | ALL }  
    ON   [ schema_name. ] table_name  
{  
      REBUILD {  
            [ PARTITION = ALL [ WITH ( <rebuild_index_option> ) ] ] 
          | [ PARTITION = partition_number [ WITH ( <single_partition_rebuild_index_option> )] ] 
      }  
    | DISABLE  
    | REORGANIZE [ PARTITION = partition_number ]  
}  
[;]  

<rebuild_index_option > ::=   
{  
    DATA_COMPRESSION = { COLUMNSTORE | COLUMNSTORE_ARCHIVE }
        [ ON PARTITIONS ( {<partition_number> [ TO <partition_number>] } [ , ...n ] ) ]   
}

<single_partition_rebuild_index_option > ::=   
{  
    DATA_COMPRESSION = { COLUMNSTORE | COLUMNSTORE_ARCHIVE }  
}  
  
```

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數

 *index_name*  
 這是索引的名稱。 在資料表或檢視表內，索引名稱必須是唯一的，但在資料庫內就不一定要是唯一的。 索引名稱必須遵照[識別碼](../../relational-databases/databases/database-identifiers.md)的規則。  
  
 ALL  
 指定與資料表或檢視表相關聯的所有索引 (不論索引類型為何)。 如果有一個或多個索引在離線或唯讀檔案群組中，或有一個或多個索引類型不允許指定的作業，指定 ALL 便會使陳述式失敗。 下表列出索引作業和不允許的索引類型。  
  
|搭配此作業使用關鍵字 ALL|如果資料表有一個或多個下列項目，便告失敗|  
|----------------------------------------|----------------------------------------|  
|REBUILD WITH ONLINE = ON|XML 索引<br /><br /> 空間索引<br /><br /> 資料行存放區索引：**適用範圍：** [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]|  
|REBUILD PARTITION = *partition_number*|未分割索引、XML 索引、空間索引或停用的索引|  
|REORGANIZE|ALLOW_PAGE_LOCKS 設定為 OFF 的索引|  
|REORGANIZE PARTITION = *partition_number*|未分割索引、XML 索引、空間索引或停用的索引|  
|IGNORE_DUP_KEY = ON|XML 索引<br /><br /> 空間索引<br /><br /> 資料行存放區索引：**適用範圍：** [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]|  
|ONLINE = ON|XML 索引<br /><br /> 空間索引<br /><br /> 資料行存放區索引：**適用範圍：** [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]|
|RESUMABLE = ON| **All** 關鍵字不支援可繼續的索引。 <br /><br /> **適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] |   
  
> [!WARNING]
> 如需有關可以在線上執行之索引作業的詳細資訊，請參閱[線上索引作業的指導方針](../../relational-databases/indexes/guidelines-for-online-index-operations.md)。

 如果設定 PARTITION = *partition_number* 來指定 ALL，便會對齊所有索引。 這表示它們會根據對等的分割區函數來進行分割。 搭配 PARTITION 子句使用 ALL 時，會重建或重新組織含有相同 *partition_number* 的所有索引分割區。 如需有關分割區索引的詳細資訊，請參閱＜ [Partitioned Tables and Indexes](../../relational-databases/partitions/partitioned-tables-and-indexes.md)＞。  
  
 *database_name*  
 這是資料庫的名稱。  
  
 *schema_name*  
 這是資料表或檢視表所屬的結構描述名稱。  
  
 *table_or_view_name*  
 這是與索引相關聯的資料表或檢視表的名稱。 若要顯示物件的索引報表，請使用 [sys.indexes](../../relational-databases/system-catalog-views/sys-indexes-transact-sql.md) 目錄檢視。  
  
 當 database_name 是目前的資料庫或 database_name 是 tempdb，而且 table_or_view_name 開頭為 # 時，[!INCLUDE[ssSDS](../../includes/sssds-md.md)] 支援三部分名稱格式 database_name.[schema_name].table_or_view_name。  
  
 REBUILD [ WITH **(** \<rebuild_index_option> [ **,** ... *n*] **)** ]  
  
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 

指定將利用相同的資料行、索引類型、唯一性屬性和排序次序來重建索引。 這個子句相當於 [DBCC DBREINDEX](../../t-sql/database-console-commands/dbcc-dbreindex-transact-sql.md)。 REBUILD 會啟用停用的索引。 除非指定了 ALL 關鍵字，否則重建叢集索引不會重建相關聯的非叢集索引。 如果未指定索引選項，便會套用儲存在 [sys.indexes](../../relational-databases/system-catalog-views/sys-indexes-transact-sql.md) 中的現有索引選項值。 任何值未儲存在 **sys.indexes** 中的索引選項，都會套用選項引數定義中所指示的預設值。  
  
 如果指定了 ALL，且基礎資料表是堆積，重建作業便不會影響資料表。 與資料表相關聯的任何非叢集索引都會重建。  
  
 如果資料庫復原模式設為大量記錄模式或簡單模式，重建作業便可以只進行最基本的記錄。  
  
> [!NOTE]
> 當您重建主要 XML 索引時，在索引作業的持續時間，無法使用基礎使用者資料表。  
 
對於資料行存放區索引，重建作業：  
  
-  不使用排序次序。  
-  在進行重建時，取得資料表或分割區上的獨佔鎖定。  在重建期間，資料處於「離線」狀態而且無法使用，即使使用 NOLOCK、RCSI 或 SI 亦然。  
-  將所有資料重新壓縮到資料行存放區。 當重建進行時，有兩個資料行存放區索引複本存在。 當重建完成時， [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會刪除原始資料行存放區索引。  

如需詳細資訊，請參閱 [重新組織與重建索引](../../relational-databases/indexes/reorganize-and-rebuild-indexes.md)。 
  
PARTITION  

**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssKatmai](../../includes/ssKatmai-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]  
  
 指定只重建或重新組織索引的一個分割區。 如果 *index_name* 不是分割區索引，便不能指定 PARTITION。  
  
 PARTITION = ALL 會重建所有分割區。  
  
> [!WARNING]
> 您可以對包含超過 1,000 個分割區的資料表，建立及重建不以資料表為準的索引，但不予支援。 此做法可能會導致在作業期間效能降低或耗用過多記憶體。 Microsoft 建議當分割區數超過 1,000 個時，才使用以資料表為準的索引。  
  
 *partition_number*  
   
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssKatmai](../../includes/ssKatmai-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]
  
 要重建或重新組織之分割區索引的分割區數。 *partition_number* 是一個可以參考變數的常數運算式。 其中包括使用者定義類型變數或函數及使用者定義函數，但無法參考 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式。 *partition_number* 必須存在，否則陳述式將會失敗。  
  
 WITH **(** \<single_partition_rebuild_index_option> **)**  
   
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssKatmai](../../includes/ssKatmai-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]  
  
 `SORT_IN_TEMPDB`、`MAXDOP` 和 `DATA_COMPRESSION` 是重建單一分割區 (PARTITION = *partition_number*) 時所能指定的選項。 不能在單一分割區重建作業中指定 XML 索引。  
  
 DISABLE  
 將索引標示為已停用，無法供 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 使用。 任何索引都可以停用。 已停用之索引的索引定義會保留在系統目錄中，但不含基礎索引資料。 停用叢集索引可以防止使用者存取基礎資料表資料。 若要啟用索引，請使用 ALTER INDEX REBUILD 或 CREATE INDEX WITH DROP_EXISTING。 如需詳細資訊，請參閱[停用索引和條件約束](../../relational-databases/indexes/disable-indexes-and-constraints.md)及[啟用索引與條件約束](../../relational-databases/indexes/enable-indexes-and-constraints.md)。  
  
 REORGANIZE **資料列存放區** 索引  
 對於資料列存放區索引，REORGANIZE 會指定重新組織索引分葉層級。 REORGANIZE 作業：  
  
-   一律在線上執行。 這表示不會保留長期封鎖的資料表鎖定，而且在 ALTER INDEX REORGANIZE 交易期間，可以繼續查詢或更新基礎資料表。
-   不允許使用停用的索引。
-   在 ALLOW_PAGE_LOCKS 設為 OFF 時，不允許使用。
-   在交易中執行且回復交易時不會回復。

> [!NOTE]
> 當 ALTER INDEX REORGANIZE 使用明確交易 (例如，BEGIN TRAN ...COMMIT/ROLLBACK內的 ALTER INDEX)，而非預設的隱含交易模式時，REORGANIZE 的鎖定行為會變得更加嚴格，可能因此導致封鎖。 如需隱含交易的詳細資訊，請參閱 [SET IMPLICIT_TRANSACTIONS &#40;Transact-SQL&#41;](../../t-sql/statements/set-implicit-transactions-transact-sql.md)。

如需詳細資訊，請參閱 [重新組織與重建索引](../../relational-databases/indexes/reorganize-and-rebuild-indexes.md)。 

REORGANIZE WITH **(** LOB_COMPACTION = { **ON** | OFF } **)**  
 適用於資料列存放區索引。  
  
LOB_COMPACTION = ON  
  
-   指定壓縮包含下列大型物件 (LOB) 資料類型資料的所有頁面：image、text、ntext、varchar(max)、nvarchar(max)、varbinary(max) 和 xml。 壓縮此資料可縮小磁碟上的資料大小。  
-   對於叢集索引，這會壓縮資料表中包含的所有 LOB 資料行。  
-   對於非叢集索引，這會壓縮索引中本身是非索引鍵資料行 (內含資料行) 的所有 LOB 資料行。  
-   REORGANIZE ALL 會在所有索引上執行 LOB_COMPACTION。 對於每個索引，這會壓縮叢集索引中的所有 LOB 資料行、基礎資料表，或非叢集索引中包含的資料行。  
  
LOB_COMPACTION = OFF  
  
-   不壓縮包含大型物件資料的頁面。  
-   OFF 對堆積沒有作用。  
  
 REORGANIZE **資料行存放區** 索引  
 對於資料行存放區索引，REORGANIZE 會以壓縮的資料列群組方式，將每個 CLOSED 的差異資料列群組壓縮至資料行存放區中。 REORGANIZE 作業一律會在線上執行。 這表示不會保留長期封鎖的資料表鎖定，而且在 ALTER INDEX REORGANIZE 交易期間，可以繼續查詢或更新基礎資料表。 如需詳細資訊，請參閱 [重新組織與重建索引](../../relational-databases/indexes/reorganize-and-rebuild-indexes.md)。 
  
-   不需要使用 REORGANIZE，也能將關閉的差異資料列群組移到壓縮的資料列群組中。 背景 tuple-mover (TM) 流程會定期喚醒，以壓縮關閉的差異資料列群組。 當 tuple-mover 進度落後時，我們建議使用 REORGANIZE。 REORGANIZE 可以更積極地壓縮資料列群組。  
-   若要壓縮所有 OPEN 和 CLOSED 的資料列群組，請參閱本節中的 `REORGANIZE WITH (COMPRESS_ALL_ROW_GROUPS)` 選項。  
  
對於 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 中的資料行存放區索引，REORGANIZE 會線上執行以下額外的重組最佳化：  
  
-   當 10% 或更多資料列已經以邏輯方式刪除時，會實際將資料列從資料列群組移除。 已刪除的位元組會在實體媒體上回收。 例如，如果在包含 1 百萬個資料列的壓縮資料列群組中刪除 10 萬個資料列，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 將會移除刪除的資料列，並重新壓縮包含 90 萬個資料列的資料列群組。 將刪除的資料列移除可以節省儲存空間。  
  
-   可合併一或多個壓縮的資料列群組，將每個資料列群組的資料列數目最多提高至 1,048,576 個資料列的上限。 例如，如果您大量匯入 5 個批次的 102,400 個資料列，就會有 5 個壓縮的資料列群組。 如果執行 REORGANIZE，這些資料列群組將會合併成 1 個包含 512,000 個資料列的壓縮資料列群組。 這是假設沒有任何目錄大小或記憶體限制的情況。  
  
-   對於已透過邏輯方式刪除 10% 或更多資料列的資料列群組，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 將會嘗試把這個資料列群組與一或多個資料列群組合併。 例如，資料列群組 1 壓縮了 500,000 個資料列，而資料列群組 21 則壓縮了達到數目上限的 1,048,576 個資料列。 資料列群組 21 中刪除了 60% 的資料列，剩下 409,830 個資料列。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會優先合併這兩個資料列群組，以壓縮成一個包含 909,830 個資料列的新資料列群組。  
  
REORGANIZE WITH ( COMPRESS_ALL_ROW_GROUPS = { ON | **OFF** } )  
 適用資於料行存放區索引。 

 **適用範圍：** [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]

COMPRESS_ALL_ROW_GROUPS 可用來將開啟或關閉的差異資料列群組強制移動到資料行存放區中。 使用此選項時，不需要重建資料行存放區索引來清空差異資料列群組。  此功能和其他移除與合併重組功能結合之後，可讓它在大部分情況下都不再需要重建索引。    

-   ON 會將所有資料列群組強制移動到資料行存放區中，無論其大小和狀態 (關閉或開啟) 為何。  
-   OFF 則會將所有關閉的資料列群組強制移動到資料行存放區中。  

如需詳細資訊，請參閱 [重新組織與重建索引](../../relational-databases/indexes/reorganize-and-rebuild-indexes.md)。 

SET **(** \<set_index option> [ **,** ... *n*] **)**  
 在不重建或重新組織索引的情況下，指定索引選項。 停用的索引不能指定 SET。  
  
PAD_INDEX = { ON | OFF }  
   
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssKatmai](../../includes/ssKatmai-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]  

 指定索引填補。 預設值為 OFF。  
  
 開啟  
 FILLFACTOR 指定的可用空間百分比會套用到索引的中繼層級頁面上。 如果 PAD_INDEX 設為 ON 時，並未指定 FILLFACTOR，就會使用 [sys.indexes](../../relational-databases/system-catalog-views/sys-indexes-transact-sql.md)中所儲存的填滿因數值。  
  
 OFF 或未指定 *fillfactor*  
 填入中繼層級頁面至接近填滿的程度。 這會保留至少足以容納一個資料列的空間，且該資料列具有索引所能擁有的大小上限 (以中繼頁面的索引鍵組為基礎)。  
  
 如需詳細資訊，請參閱 [CREATE INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/create-index-transact-sql.md)。  
  
FILLFACTOR = *fillfactor*  
 
 **適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssKatmai](../../includes/ssKatmai-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]
  
 指定一個百分比來指出在建立或改變索引期間，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 應該使各索引頁面之分葉層級填滿的程度。 *fillfactor* 必須是 1 到 100 之間的整數值。 預設值是 0。 填滿因數值 0 和 100 在各方面都是一樣的。  
  
 只有在最初建立或重建索引時，才適用明確的 FILLFACTOR 設定。 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 不會動態保留頁面中空白空間的指定百分比。 如需詳細資訊，請參閱 [CREATE INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/create-index-transact-sql.md)。  
  
 若要檢視填滿因數設定，請使用 **sys.indexes**。  
  
> [!IMPORTANT]
> 利用 FILLFACTOR 值來建立或變更叢集索引時，會影響資料所佔用的儲存空間量，因為 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 在建立叢集索引時，會轉散發資料。  
  
 SORT_IN_TEMPDB = { ON | **OFF** }  
 
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssKatmai](../../includes/ssKatmai-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]  
  
 指定是否將排序結果儲存在 **tempdb** 中。 預設值是關閉，但 Azure SQL Database 超大規模資料庫例外。 針對超大規模資料庫中的所有索引重建作業，不論指定的選項為何，除非使用可繼續的索引重建，否則 SORT_IN_TEMPDB 一律會是 ON。  
  
 開啟  
 用來建置索引的中繼排序結果會儲存在 **tempdb** 中。 如果 **tempdb** 是在使用者資料庫以外的磁碟組中，這可能會縮短建立索引所需要的時間。 不過，這會增加建立索引時所使用的磁碟空間量。  
  
 OFF  
 中繼排序結果會儲存在與用來儲存索引相同的資料庫中。  
  
 如果不需要排序作業，或者可以在記憶體中執行排序，則忽略 SORT_IN_TEMPDB 選項。  
  
 如需詳細資訊，請參閱[索引的 SORT_IN_TEMPDB 選項](../../relational-databases/indexes/sort-in-tempdb-option-for-indexes.md)。  
  
 IGNORE_DUP_KEY **=** { ON | OFF }  
 指定當插入作業嘗試將重複的索引鍵值插入唯一索引時所產生的錯誤回應。 IGNORE_DUP_KEY 選項只適用於在建立或重建索引之後所發生的插入作業。 預設值為 OFF。  
  
 開啟  
 當重複的索引鍵值插入唯一索引時，就會出現警告訊息。 只有違反唯一性條件約束的資料列才會失敗。  
  
 OFF  
 當重複的索引鍵值插入唯一索引時，就會出現錯誤訊息。 整個 INSERT 作業將會回復。  
  
 若為針對檢視表所建立的索引、非唯一索引、XML 索引、空間索引和篩選索引，IGNORE_DUP_KEY 不得設為 ON。  
  
 若要檢視 IGNORE_DUP_KEY，請使用 [sys.indexes](../../relational-databases/system-catalog-views/sys-indexes-transact-sql.md)。  
  
 在與舊版本相容的語法中，WITH IGNORE_DUP_KEY 相當於 WITH IGNORE_DUP_KEY = ON。  
  
 STATISTICS_NORECOMPUTE **=** { ON | OFF }  
 指定是否要重新計算散發統計資料。 預設值為 OFF。  
  
 開啟  
 不會自動重新計算過期的統計資料。  
  
 OFF  
 啟用自動統計資料更新。  
  
 若要還原自動統計資料更新，請將 STATISTICS_NORECOMPUTE 設為 OFF，或執行不含 NORECOMPUTE 子句的 UPDATE STATISTICS。  
  
> [!IMPORTANT]
> 停用散發統計資料的自動重新計算，可防止查詢最佳化工具取得與資料表有關之查詢的最佳執行計畫。  
  
STATISTICS_INCREMENTAL = { ON | **OFF** }  

**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]

若設定為 **ON**，所建立的統計資料會以每個資料分割統計資料為依據。 若為 **OFF**，則會卸除統計資料樹狀結構，且 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會重新計算統計資料。 預設值為 **OFF**。  
  
 如果不支援每個分割區區的統計資料，則會忽略該選項，並產生警告。 針對下列統計資料類型，不支援累加統計資料：  
  
-   建立統計資料時，所使用的索引未與基底資料表進行分割區對齊  
-   在 Always On 可讀取次要資料庫上建立的統計資料  
-   在唯讀資料庫上建立的統計資料  
-   在篩選的索引上建立的統計資料  
-   在檢視表上建立的統計資料  
-   在內部資料表上建立的統計資料  
-   使用空間索引或 XML 索引建立的統計資料  
  
 ONLINE **=** { ON | **OFF** } \<as applies to rebuild_index_option>  
 指定在索引作業期間，查詢和資料修改是否能夠使用基礎資料表和相關聯的索引。 預設值為 OFF。  
  
 如果是 XML 索引或空間索引，則只支援 `ONLINE = OFF`，而如果將 ONLINE 設定為 ON，將會引發錯誤。  
  
> [!IMPORTANT]
> 並非所有版本的 [!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 都可以使用線上索引作業。 如需 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 版本支援的功能清單，請參閱 [[!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 版本和支援的功能](../../sql-server/editions-and-supported-features-for-sql-server-2016.md)和 [SQL Server 2017 版本和支援的功能](../../sql-server/editions-and-components-of-sql-server-2017.md)。  
  
 開啟  
 索引作業持續期間不會保留長期資料表鎖定。 在索引作業的主要階段期間，來源資料表上只保留意圖共用 (IS) 鎖定。 這使得基礎資料表和索引的查詢或更新能夠繼續運作。 在作業開始時，共用 (S) 鎖定會在來源物件上保留一段很短的時間。 在作業結束時，如果建立非叢集索引，S (共用) 鎖定會在來源上保留一段很短的時間；在線上建立或卸除叢集索引時，以及重建叢集或非叢集索引時，將會取得 SCH-M (結構描述修改) 鎖定。 建立本機暫存資料表的索引時，ONLINE 不可設為 ON。  
  
 OFF  
 在索引作業期間會套用資料表鎖定。 建立、重建或卸除叢集索引、空間索引或 XML 索引的離線索引作業，或是重建或卸除非叢集索引的離線索引作業，將會取得資料表的結構描述修改 (Sch-M) 鎖定。 這可防止所有使用者在作業持續期間存取基礎資料表。 建立非叢集索引的離線索引作業會取得資料表的共用 (S) 鎖定。 這可避免對基礎資料表進行更新，但仍可執行讀取作業，如 SELECT 陳述式。  
  
如需詳細資訊，請參閱 [Perform Index Operations Online](../../relational-databases/indexes/perform-index-operations-online.md)。  
  
您可以在線上重建索引，其中包括全域暫存資料表的索引，但下列情況除外：
- XML 索引
- 本機暫存資料表上的索引
- 在檢視上的初始唯一叢集索引
- 資料行存放區索引
- 叢集索引 (如果基礎資料表包含 LOB 資料類型 (**image**、**ntext**、**text**) 及空間類型)
- **varchar(max)** 和 **varbinary(max)** 資料行不得為索引的一部分。 在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 中，當資料表包含 **varchar(max)** 或 **varbinary(max)** 資料行時，可以使用 **ONLINE** 選項來建置或重建包含其他資料行的叢集索引。 當基底資料表包含 **varchar(max)** 或 **varbinary(max)** 資料行時，[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 不允許 **ONLINE** 選項

如需詳細資訊，請參閱[線上索引作業如何運作](../../relational-databases/indexes/how-online-index-operations-work.md)。

RESUMABLE **=** { ON | **OFF**}

**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]   

 指定線上索引作業是否為可繼續的作業。

 ON 索引作業為可繼續的作業。

 OFF 索引作業不是可繼續的作業。

MAX_DURATION **=** *time* [**MINUTES**] 與 **RESUMABLE = ON** 搭配使用 (需要 **ONLINE = ON**)。

**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 

指出可繼續的線上索引作業在暫停之前的執行時間 (以分鐘為單位指定的一個整數值)。 

> [!IMPORTANT]
> 如需有關可以在線上執行之索引作業的詳細資訊，請參閱[線上索引作業的指導方針](../../relational-databases/indexes/guidelines-for-online-index-operations.md)。

> [!NOTE] 
> 資料行存放區索引不支援可繼續的線上索引重建。

ALLOW_ROW_LOCKS **=** { **ON** | OFF }  

**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssKatmai](../../includes/ssKatmai-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]  
  
 指定是否允許資料列鎖定。 預設值是 ON。  
  
 開啟  
 當存取索引時，允許資料列鎖定。 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 會決定使用資料列鎖定的時機。  
  
 OFF  
 不使用資料列鎖定。  
  
ALLOW_PAGE_LOCKS **=** { **ON** | OFF }  
  
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssKatmai](../../includes/ssKatmai-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]
  
 指定是否允許頁面鎖定。 預設值是 ON。  
  
 開啟  
 當您存取索引時，允許頁面鎖定。 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 會決定使用頁面鎖定的時機。  
  
 OFF  
 不使用頁面鎖定。  
  
> [!NOTE]
> 當 ALLOW_PAGE_LOCKS 設為 OFF 時，無法重新組織索引。  

 OPTIMIZE_FOR_SEQUENTIAL_KEY = { ON | **OFF** }

**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]

指定是否要最佳化最後一頁的插入競爭。 預設值為 OFF。 請參閱 CREATE INDEX 頁面的[循序索引鍵](./create-index-transact-sql.md#sequential-keys)一節以取得詳細資訊。

 MAXDOP **=** max_degree_of_parallelism  
 
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssKatmai](../../includes/ssKatmai-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]  
  
 在索引作業期間，覆寫 **max degree of parallelism** 設定選項。 如需詳細資訊，請參閱 [設定 max degree of parallelism 伺服器組態選項](../../database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option.md)。 請利用 MAXDOP 來限制執行平行計畫所用的處理器數目。 最大值是 64 個處理器。  
  
> [!IMPORTANT]
>  雖然所有 XML 索引在語法上都支援 MAXDOP 選項，但是對於空間索引或主要 XML 索引而言，ALTER INDEX 目前只會使用單一處理器。  
  
 *max_degree_of_parallelism* 可以是：  
  
 1  
 隱藏平行計畫的產生。  
  
 \>1  
 將平行索引作業所用的最大處理器數目限制為指定的數目。  
  
 0 (預設值)  
 根據目前的系統工作負載，使用實際數目或比實際數目更少的處理器。  
  
 如需詳細資訊，請參閱 [設定平行索引作業](../../relational-databases/indexes/configure-parallel-index-operations.md)。  
  
> [!NOTE]
> [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的所有版本都無法使用平行索引作業。 如需 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 版本支援的功能清單，請參閱 [SQL Server 2016 版本和支援的功能](../../sql-server/editions-and-components-of-sql-server-2016.md)。  
  
COMPRESSION_DELAY **=** { **0** |*持續時間 [分鐘]* }  

**適用範圍：** [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 開始)  
  
 針對磁碟型資料表，延遲會指定處於「關閉」狀態的差異資料列群組必須在差異資料列群組中至少保留多少分鐘的時間，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 才能將它壓縮到壓縮的資料列群組。 由於磁碟型資料表不會追蹤個別資料列的插入和更新時間，因此 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會將這段延遲時間套用於「關閉」狀態下的差異資料列群組。  
  
 預設值是 0 分鐘。  
  
 如需 COMPRESSION_DELAY 的使用時機建議，請參閱[開始使用資料行存放區進行即時作業分析](../../relational-databases/indexes/get-started-with-columnstore-for-real-time-operational-analytics.md)。  
  
 DATA_COMPRESSION  
 針對指定的索引、分割區編號或分割區範圍指定資料壓縮選項。 選項如下：  
  
 無  
 不壓縮索引或指定的分割區。 這不適用於資料行存放區索引。  
  
 ROW  
 使用資料列壓縮來壓縮索引或指定的分割區。 這不適用於資料行存放區索引。  
  
 PAGE  
 使用頁面壓縮來壓縮索引或指定的分割區。 這不適用於資料行存放區索引。  
  
 COLUMNSTORE  
   
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]
  
 只適用於資料行存放區索引，包括非叢集資料行存放區索引和叢集資料行存放區索引。 COLUMNSTORE 會指定解壓縮以 COLUMNSTORE_ARCHIVE 選項壓縮的索引或指定的分割區。 當還原資料時，資料將會繼續以用於所有資料行存放區索引的資料行存放區壓縮來壓縮。  
  
 COLUMNSTORE_ARCHIVE  
  
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]
  
 只適用於資料行存放區索引，包括非叢集資料行存放區索引和叢集資料行存放區索引。 COLUMNSTORE_ARCHIVE 將進一步將指定的分割區壓縮成較小的大小。 這可用於封存，或是其他需要較小儲存體，而且可負擔更多時間來儲存和擷取的狀況。  
  
 如需與壓縮有關的詳細資訊，請參閱[資料壓縮](../../relational-databases/data-compression/data-compression.md)。  
  
 ON PARTITIONS **(** { \<partition_number_expression> | \<range> } [ **,** ...n] **)**  
    
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssKatmai](../../includes/ssKatmai-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 
  
 指定套用 DATA_COMPRESSION 設定的分割區。 如果未分割此索引，ON PARTITIONS 引數將會產生錯誤。 如果未提供 ON PARTITIONS 子句，DATA_COMPRESSION 選項會套用到分割區索引的所有分割區。  
  
 可以使用下列方式來指定 \<partition_number_expression>：  
  
-   提供分割區的編號，例如：`ON PARTITIONS (2)`。  
-   為數個個別分割區提供以逗號分隔的分割區編號，例如：`ON PARTITIONS (1, 5)`。  
-   同時提供範圍和個別分割區：`ON PARTITIONS (2, 4, 6 TO 8)`。  
  
 \<range> 可以指定為以 TO 一字分隔的分割區編號，例如：`ON PARTITIONS (6 TO 8)`。  
  
 若要為不同的分割區設定不同類型的資料壓縮，請指定 DATA_COMPRESSION 選項一次以上，例如：  
  
```sql  
REBUILD WITH   
(  
DATA_COMPRESSION = NONE ON PARTITIONS (1),   
DATA_COMPRESSION = ROW ON PARTITIONS (2, 4, 6 TO 8),   
DATA_COMPRESSION = PAGE ON PARTITIONS (3, 5)  
);  
```  
  
 ONLINE **=** { ON  | **OFF** } \<as applies to single_partition_rebuild_index_option>  
 指定基礎資料表的索引或索引分割區可以在線上重建或離線重建。 如果 **REBUILD** 是在線上執行 (**ON**)，這個資料表中的資料可以在索引作業期間用於查詢和資料修改。  預設值為 **OFF**。  
  
 開啟  
 索引作業持續期間不會保留長期資料表鎖定。 在索引作業的主要階段期間，來源資料表上只保留意圖共用 (IS) 鎖定。 在索引重建開始時，資料表上需要 S 鎖定，而在線上索引重建結束時，資料表上需要 Sch-M 鎖定。 雖然這兩個鎖定是短暫的中繼資料鎖定，尤其是 Sch-M 鎖定必須等候所有封鎖交易完成。 在等候期間，Sch-M 鎖定會在存取相同資料表時，封鎖等待在這個鎖定之後的所有其他交易。  
  
> [!NOTE]
>  線上索引重建可設定本節稍後所述的 *low_priority_lock_wait* 選項。  
  
 OFF  
 在索引作業期間會套用資料表鎖定。 這可防止所有使用者在作業持續期間存取基礎資料表。  
  
 WAIT_AT_LOW_PRIORITY 只能與 **ONLINE=ON** 搭配使用。  
 
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]
  
 線上索引重建必須等候這個資料表的封鎖作業。 **WAIT_AT_LOW_PRIORITY** 表示線上索引重建作業將會等候低優先順序鎖定，讓其他作業在線上索引建立作業等候時繼續進行。 省略 **WAIT AT LOW PRIORITY** 選項相當於 WAIT_AT_LOW_PRIORITY (MAX_DURATION = 0 minutes, ABORT_AFTER_WAIT = NONE)。 如需詳細資訊，請參閱 [WAIT_AT_LOW_PRIORITY](alter-index-transact-sql.md)。 
  
 MAX_DURATION = *time* [**MINUTES**]  
  
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]
  
 執行 DDL 命令時，線上索引重建鎖定將會以低優先權等候的等候時間 (以分鐘為單位指定的整數值)。 如果作業被封鎖時間長度達到在 **MAX_DURATION** 中指定的時間，則會執行其中一個 **ABORT_AFTER_WAIT** 動作。 **MAX_DURATION** 時間一律以分鐘為單位，而且可以省略 **MINUTES** 這個字。  
 
 ABORT_AFTER_WAIT = [**NONE** | **SELF** | **BLOCKERS** } ]  
   
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]
  
 無  
 繼續等候一般 (標準) 優先權的鎖定。  
  
 SELF  
 結束目前正在執行的線上索引重建 DDL 作業，但不採取任何動作。  
  
 BLOCKERS  
 終止封鎖線上索引重建 DDL 作業的所有使用者交易，讓作業可以繼續。 **BLOCKERS** 選項需要登入才能擁有 **ALTER ANY CONNECTION** 權限。  
 
 RESUME 
 
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]  

繼續以手動方式暫停或因失敗而暫停的索引作業。

MAX_DURATION 與 **RESUMABLE=ON** 搭配使用
 
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]

可繼續的線上索引作業在繼續之後的執行時間 (以分鐘為單位指定的整數值)。 時間到期之後，如果可繼續的作業仍在執行中，就會暫停作業。

WAIT_AT_LOW_PRIORITY 與 **RESUMABLE=ON** 和 **ONLINE = ON** 搭配使用。  
  
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 
  
 在暫停之後繼續線上索引重建時，必須等候這個資料表的封鎖作業。 **WAIT_AT_LOW_PRIORITY** 表示線上索引重建作業將會等候低優先順序鎖定，讓其他作業在線上索引建立作業等候時繼續進行。 省略 **WAIT AT LOW PRIORITY** 選項相當於 WAIT_AT_LOW_PRIORITY (MAX_DURATION = 0 minutes, ABORT_AFTER_WAIT = NONE)。 如需詳細資訊，請參閱 [WAIT_AT_LOW_PRIORITY](alter-index-transact-sql.md)。 

PAUSE
 
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 
  
暫停可繼續的線上索引重建作業。

ABORT

**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]   

中止已宣告為可繼續的執行中或已暫停的索引作業。 您必須明確地執行 **ABORT** 命令，以終止可繼續的索引重建作業。 失敗或暫停可繼續的索引作業不會終止其執行；相反地，它會使作業進入無限期暫停狀態。
  
## <a name="remarks"></a>備註  
ALTER INDEX 無法用來重新進行索引的分割區，或將它移到另一個檔案群組。 您不能利用這個陳述式來修改索引定義，例如新增或刪除資料行，或變更資料行順序。 請搭配 DROP_EXISTING 子句來使用 CREATE INDEX，以執行這些作業。  
  
未明確指定選項時，會套用目前的設定。 例如，如果 REBUILD 子句並未指定 FILLFACTOR 設定，在重建過程中，會使用系統目錄中所儲存的填滿因數值。 若要檢視目前的索引選項設定，請使用 [sys.indexes](../../relational-databases/system-catalog-views/sys-indexes-transact-sql.md)。  
  
`ONLINE`、`MAXDOP` 和 `SORT_IN_TEMPDB` 的值並未儲存在系統目錄中。 除非索引陳述式中另有指定，否則會使用選項的預設值。
  
在多重處理器的電腦上，`ALTER INDEX REBUILD` 也如同其他查詢一樣，會自動使用更多處理器來執行與修改索引相關的掃描和排序作業。 當您執行 `ALTER INDEX REORGANIZE` 時，不論是否設定了 LOB_COMPACTION，**max degree of parallelism** 值都是單一執行緒作業。 如需詳細資訊，請參閱 [設定平行索引作業](../../relational-databases/indexes/configure-parallel-index-operations.md)。  
  
> [!IMPORTANT]
> 如果索引所在的檔案群組離線或設為唯讀，便無法重新組織或重建索引。 當指定了 ALL 關鍵字，且有一個或多個索引在離線或唯讀檔案群組中，陳述式會失敗。  
  
## <a name="rebuilding-indexes"></a><a name="rebuilding-indexes"></a> 重建索引  
重建索引會卸除和重新建立索引。 這會移除片段；根據指定的或現有的填滿因數設定壓縮頁面來收回磁碟空間，以及重新排序連續頁面中的索引資料列。 當指定 ALL 時，會在單一交易中卸除和重建資料表的所有索引。 不需要事先卸除外部索引鍵條件約束。 當重建含有 128 個或更多範圍的索引時，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 會延遲取消配置實際的頁面，也會延遲其關聯鎖定，直到認可交易之後。  
 
如需詳細資訊，請參閱 [重新組織與重建索引](../../relational-databases/indexes/reorganize-and-rebuild-indexes.md)。 
  
## <a name="reorganizing-indexes"></a><a name="reorganizing-indexes"></a> 重新組織索引
重新組織索引所用的系統資源最少。 它會實際重新排序分葉層級的頁面，使它們由左至右符合分葉節點的邏輯順序，以重新組織資料表和檢視表之叢集和非叢集索引的分葉層級。 重新組織也會壓縮索引頁面。 壓縮是以現有填滿因數值為基礎。 
  
當指定 `ALL` 時，會重新組織資料表的叢集和非叢集關聯式索引及 XML 索引。 當指定 ALL 時，適用某些限制，請參閱本文＜引數＞一節中的 ALL 定義。  
  
如需詳細資訊，請參閱 [重新組織與重建索引](../../relational-databases/indexes/reorganize-and-rebuild-indexes.md)。  

> [!IMPORTANT]
> 對於具有已排序叢集資料行存放區索引的 [!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] 資料表，`ALTER INDEX REORGANIZE` 不會重新排序資料。 若要重新排序資料，請使用 `ALTER INDEX REBUILD`。
  
## <a name="disabling-indexes"></a><a name="disabling-indexes"></a> 停用索引  
停用索引可防止使用者存取索引，如果是叢集索引，則可防止使用者存取基礎資料表的資料。 索引定義會保留在系統目錄中。 停用檢視的非叢集索引或叢集索引時，會實際刪除索引資料。 停用叢集索引可防止存取資料，資料仍會保留在 B 型樹狀目錄中，但不進行維護，直到卸除或重建索引為止。 若要檢視已啟用或已停用索引的狀態，請查詢 **sys.indexes** 目錄檢視中的 **is_disabled** 資料行。  
  
如果資料表在異動複寫發行集中，您便無法停用關聯於主索引鍵資料行的任何索引。 複寫需要這些索引。 若要停用索引，您必須先從發行集中卸除資料表。 如需詳細資訊，請參閱[發行資料和資料庫物件](../../relational-databases/replication/publish/publish-data-and-database-objects.md)。  
  
使用 `ALTER INDEX REBUILD` 陳述式或 `CREATE INDEX WITH DROP_EXISTING` 陳述式來啟用索引。 當 ONLINE 選項設為 ON 時，無法重建停用的叢集索引。 如需詳細資訊，請參閱 [停用索引和條件約束](../../relational-databases/indexes/disable-indexes-and-constraints.md)。  
  
## <a name="setting-options"></a>設定選項  
您可以在不重建或重新組織指定之索引的情況下，設定這個索引的 `ALLOW_ROW_LOCKS`、`ALLOW_PAGE_LOCKS`、`OPTIMIZE_FOR_SEQUENTIAL_KEY`、`IGNORE_DUP_KEY` 和 `STATISTICS_NORECOMPUTE` 選項。 修改的值會立即套用在索引上。 若要檢視這些設定，請使用 **sys.indexes**。 如需詳細資訊，請參閱 [設定索引選項](../../relational-databases/indexes/set-index-options.md)。  
  
### <a name="row-and-page-locks-options"></a>資料列和頁面鎖定選項  
如果 `ALLOW_ROW_LOCKS = ON` 且 `ALLOW_PAGE_LOCK = ON`，當您存取索引時，允許資料列、頁面和資料表層級的鎖定。 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 會選擇適當的鎖定，且可以將鎖定從資料列或頁面鎖定擴大到資料表鎖定。  
  
如果 `ALLOW_ROW_LOCKS = OFF` 且 `ALLOW_PAGE_LOCK = OFF`，當您存取索引時，只允許資料表層級的鎖定。  
  
如果指定 ALL，且設定了資料列或頁面鎖定，便會將這些設定套用至所有索引上。 當基礎資料表是堆積時，會依照下列方式來套用設定：  
  
|選項|詳細資料|
|------|-------|
|ALLOW_ROW_LOCKS = ON 或 OFF|套用在堆積和任何相關聯的非叢集索引上。|  
|ALLOW_PAGE_LOCKS = ON|套用在堆積和任何相關聯的非叢集索引上。|  
|ALLOW_PAGE_LOCKS = OFF|完整套用在非叢集索引上。 這表示在非叢集索引上，不允許所有頁面鎖定。 在堆積上，不允許的鎖定只有頁面的共用 (S)、更新 (U) 和獨佔 (X) 鎖定。 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 仍能取得意圖頁面鎖定 (IS、IU 或 IX)，供內部使用。|  
  
## <a name="online-index-operations"></a><a name="online-index-operations"></a> 線上索引作業  
當重建索引且 ONLINE 選項設為 ON 時，查詢和資料修改可以使用基礎物件、資料表和相關聯的索引。 您也可以在線上重建位於單一分割區之索引的一部分。 在改變過程中，只會在非常短的時間內，保留獨佔的資料表鎖定。  
  
索引一律是在線上重新組織。 這個過程不會長期保留鎖定，因此，不會封鎖執行中的查詢或更新。  
  
只有在執行下列動作時，您才能在相同資料表或資料表分割區上執行並行的線上索引作業：  
  
-   建立多個非叢集索引。  
-   在相同資料表上重新組織不同的索引。  
-   在重建相同資料表的非重疊索引時，重新組織不同的索引。  
  
同時執行的所有其他線上索引作業都會失敗。 例如，您不能在相同資料表上，同時重建兩個或更多索引，或在相同資料表上重建現有索引時，建立新的索引。  

### <a name="resumable-index-operations"></a><a name="resumable-indexes"></a> 可繼續的索引作業

**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 

線上索引重建已使用 RESUMABLE = ON 選項指定為可繼續。 
-  指定索引的中繼資料中不會保存 RESUMABLE 選項，並且僅適用於目前 DDL 陳述式的持續時間。 因此，必須明確指定 RESUMABLE = ON 子句，才能啟用可繼續性。
-  RESUMABLE = ON 選項或 **low_priority_lock_wait** 引數選項支援 MAX_DURATION 選項。 
   -  RESUMABLE 選項的 MAX_DURATION 可指定重建索引時的時間間隔。 使用此時間之後，索引重建就會暫停或完成執行。 使用者可決定何時可以繼續已暫停索引的重建。 MAX_DURATION 的 **時間** 是以分鐘計算，且必須大於 0 分鐘，並少於或等於一週 (7 \* 24 \* 60 = 10080 分鐘)。 索引作業長時間暫停可能會影響特定資料表上的 DML 效能，以及影響資料庫磁碟容量，因為原始索引和新建立的索引都需要磁碟空間，且需要在 DML 作業期間更新。 如果省略 MAX_DURATION 選項，索引作業將會繼續執行直到完成或發生失敗為止。 
   -  \<low_priority_lock_wait> 引數選項可讓您決定索引作業在 SCH-M 鎖定上遭到封鎖時可繼續作業的方式。
 
-  重新執行具有相同參數的原始 ALTER INDEX REBUILD 陳述式，就會繼續已暫停的索引重建作業。 您也可以執行 ALTER INDEX RESUME 陳述式，繼續已暫停的索引重建作業。
-  可繼續的索引不支援 SORT_IN_TEMPDB=ON 選項 
-  RESUMABLE=ON 的 DDL 命令無法在明確交易內部執行 (不能是 begin tran ... commit 區塊的一部分)。
-  只有已暫停的索引作業才能繼續。
-  繼續已暫停的索引作業時，您可以將 MAXDOP 值變更為新值。  如果繼續已暫停的索引作業時未指定 MAXDOP，會使用最後一個 MAXDOP 值。 如果沒有為索引重建作業指定 MAXDOP 選項，會使用預設值。
- 若要立即暫停索引作業，您可以停止進行中的命令 (Ctrl-C)，或執行 ALTER INDEX PAUSE 命令或 KILL *session_id* 命令。 暫停命令之後，可以使用 RESUME 選項繼續執行命令。
-  ABORT 命令會終止裝載原始索引重建的工作階段，並中止索引作業  
-  可繼續的索引重建不需要額外的資源，除了
   -    需要額外的空間以保留正在建立的索引，包括索引的暫停時間
   -    可防止進行任何 DDL 修改的 DDL 狀態
-  準刪除清除將會在索引暫停階段期間執行，但它將會在索引執行期間暫停   
已針對可繼續的索引重建作業停用下列功能
   -    RESUMABLE=ON 不支援重建已停用的索引
   -    ALTER INDEX REBUILD ALL 命令
   -    ALTER TABLE 使用索引重建  
   -    "RESUMABLE = ON" 的 DDL 命令無法在明確交易內部執行 (不能是 begin tran ... commit 區塊的一部分)
   -    重建已經計算或以 TIMESTAMP 資料行作為索引鍵資料行的索引。
-   如果基底資料表包含 LOB 資料行，可繼續的叢集索引重建就會在此作業開始時需要 Sch-M 鎖定 

> [!NOTE]
> DDL 命令會執行，直到完成、暫停或失敗為止。 如果命令暫停，將會發出錯誤指出作業已暫停，而且沒有完成索引建立。 您可以從 [sys.index_resumable_operations](../../relational-databases/system-catalog-views/sys-index-resumable-operations.md) 取得目前索引狀態的詳細資訊。 和以前一樣，如果發生失敗，也會發出錯誤。 

 如需詳細資訊，請參閱 [Perform Index Operations Online](../../relational-databases/indexes/perform-index-operations-online.md)。  
  
 ### <a name="wait_at_low_priority-with-online-index-operations"></a>WAIT_AT_LOW_PRIORITY 與線上索引作業  
  
 為了執行線上索引重建的 DDL 陳述式，特定資料表上執行的所有使用中封鎖交易都必須完成。 當線上索引重建執行時，它會封鎖這個資料表上準備開始執行的所有新交易。 雖然線上索引重建的鎖定期間很短，但是等候特定資料表上所有未完成的交易完成並封鎖要開始的新交易，可能會大幅影響輸送量，導致工作負載速度變慢或逾時，並且大幅限制對基礎資料表的存取。 **WAIT_AT_LOW_PRIORITY** 選項可讓 DBA 管理線上索引重建所需的 S 鎖定和 Sch-M 鎖定，並允許它們選取 3 個選項的其中一個。 在這 3 個案例中，如果在等候期間 ( (MAX_DURATION = n [minutes]) ) 沒有封鎖活動，線上索引重建會立即執行而不等候，並且 DDL 陳述式會完成。  
  
## <a name="spatial-index-restrictions"></a>空間索引的限制  
 當您重建空間索引時，在索引作業的持續時間，無法使用基礎使用者資料表，因為空間索引會持有結構描述鎖定。  
  
 使用者資料表中的 PRIMARY KEY 條件約束無法在空間索引定義於該資料表的資料行上時，加以修改。 若要變更 PRIMARY KEY 條件約束，請先卸除此資料表的每一個空間索引。 在修改 PRIMARY KEy 條件約束之後，您可以重新建立每一個空間索引。  
  
 在單一分割區重建作業中，您不能指定任何空間索引。 但是，您可以在完整分割區重建中指定空間索引。  
  
 若要變更空間索引特定的選項 (例如 BOUNDING_BOX 或 GRID)，您可以使用指定 DROP_EXISTING = ON 的 CREATE SPATIAL INDEX 陳述式，或是卸除此空間索引並建立新的索引。 如需範例，請參閱 [CREATE SPATIAL INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/create-spatial-index-transact-sql.md)中的＜備註＞一節。  
  
## <a name="data-compression"></a>資料壓縮  
 如需資料壓縮的詳細資訊，請參閱 [資料壓縮](../../relational-databases/data-compression/data-compression.md)。  
  
 若要評估變更 PAGE 和 ROW 壓縮如何影響資料表、索引或分割區，請使用 [sp_estimate_data_compression_savings](../../relational-databases/system-stored-procedures/sp-estimate-data-compression-savings-transact-sql.md) 預存程序。  
  
下列限制適用於分割區索引：  
  
-   使用 ALTER INDEX ALL ... 時，您無法在資料表具有非對齊索引時變更單一分割區的壓縮設定。  
-   ALTER INDEX \<index> ...REBUILD PARTITION ... 語法會重建此索引的指定分割區。  
-   The ALTER INDEX \<index> ...REBUILD WITH ... 語法會重建此索引的所有分割區。  
  
## <a name="statistics"></a>統計資料  
 當您針對資料表執行 **ALTER INDEX ALL ...** 時，只會更新與索引相關聯的統計資料。 針對資料表 (而非索引) 所建立的自動或手動統計資料不會進行更新。  
  
## <a name="permissions"></a>權限  
 若要執行 ALTER INDEX，至少需要資料表或檢視表的 ALTER 權限。  
  
## <a name="version-notes"></a>版本資訊  
  
-  [!INCLUDE[ssSDS](../../includes/sssds-md.md)] 不使用 filegroup 和 filestream 選項。  
-  在 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 之前無法使用資料行存放區索引。 
-  可繼續的索引作業從 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 起開始提供   
  
## <a name="basic-syntax-example"></a>基本語法範例：   
  
```sql 
ALTER INDEX index1 ON table1 REBUILD;  
  
ALTER INDEX ALL ON table1 REBUILD;  
  
ALTER INDEX ALL ON dbo.table1 REBUILD;  
```

## <a name="examples-columnstore-indexes"></a>範例：資料行存放區索引  
 這些範例適用於資料行存放區索引。  
  
### <a name="a-reorganize-demo"></a>A. REORGANIZE 示範  
 此範例示範 ALTER INDEX REORGANIZE 命令的運作方式。  它會建立一個擁有多個資料列群組的資料表，然後示範 REORGANIZE 如何合併資料列群組。  
  
```sql  
-- Create a database   
CREATE DATABASE [ columnstore ];  
GO  
  
-- Create a rowstore staging table  
CREATE TABLE [ staging ] (  
     AccountKey              int NOT NULL,  
     AccountDescription      nvarchar (50),  
     AccountType             nvarchar(50),  
     AccountCodeAlternateKey     int  
     )  
  
-- Insert 10 million rows into the staging table.   
DECLARE @loop int  
DECLARE @AccountDescription varchar(50)  
DECLARE @AccountKey int  
DECLARE @AccountType varchar(50)  
DECLARE @AccountCode int  
  
SELECT @loop = 0  
BEGIN TRAN  
    WHILE (@loop < 300000)   
      BEGIN  
        SELECT @AccountKey = CAST (RAND()*10000000 as int);  
        SELECT @AccountDescription = 'accountdesc ' + CONVERT(varchar(20), @AccountKey);  
        SELECT @AccountType = 'AccountType ' + CONVERT(varchar(20), @AccountKey);  
        SELECT @AccountCode =  CAST (RAND()*10000000 as int);  
  
        INSERT INTO  staging VALUES (@AccountKey, @AccountDescription, @AccountType, @AccountCode);  
  
        SELECT @loop = @loop + 1;  
    END  
COMMIT  
  
-- Create a table for the clustered columnstore index  
  
CREATE TABLE cci_target (  
     AccountKey              int NOT NULL,  
     AccountDescription      nvarchar (50),  
     AccountType             nvarchar(50),  
     AccountCodeAlternateKey int  
     )  
  
-- Convert the table to a clustered columnstore index named inxcci_cci_target;  
CREATE CLUSTERED COLUMNSTORE INDEX idxcci_cci_target ON cci_target;  
```  
  
 使用 TABLOCK 選項來以平行處理方式插入資料列。 從 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 起，INSERT INTO 作業可以在使用 TABLOCK 時以平行處理方式執行。  
  
```sql  
INSERT INTO cci_target WITH (TABLOCK) 
SELECT TOP 300000 * FROM staging;  
```  
  
 執行此命令可查看開啟的差異資料列群組。 資料列群組的數目取決於平行處理原則的程度。  
  
```sql  
SELECT *   
FROM sys.dm_db_column_store_row_group_physical_stats   
WHERE object_id  = object_id('cci_target');  
```  
  
 執行此命令以將所有關閉和開啟的資料列群組強制移動到資料行存放區中。  
  
```sql  
ALTER INDEX idxcci_cci_target ON cci_target REORGANIZE WITH (COMPRESS_ALL_ROW_GROUPS = ON);  
```  
  
 再次執行此命令，您將會看到幾個較小的資料列群組合併成一個壓縮的資料列群組。  
  
```sql  
ALTER INDEX idxcci_cci_target ON cci_target REORGANIZE WITH (COMPRESS_ALL_ROW_GROUPS = ON);  
```  
  
### <a name="b-compress-closed-delta-rowgroups-into-the-columnstore"></a>B. 將關閉的差異資料列群組壓縮到資料行存放區中  
 此範例使用 REORGANIZE 選項，以壓縮的資料列群組方式，將每個關閉的差異資料列群組壓縮到資料行存放區中。   這是非必要的，但當 tuple-mover 壓縮關閉的資料列群組的速度不夠快時，就很有用。  
  
```sql  
-- Uses AdventureWorksDW  
-- REORGANIZE all partitions  
ALTER INDEX cci_FactInternetSales2 ON FactInternetSales2 REORGANIZE;  
  
-- REORGANIZE a specific partition  
ALTER INDEX cci_FactInternetSales2 ON FactInternetSales2 REORGANIZE PARTITION = 0;  
```  
  
### <a name="c-compress-all-open-and-closed-delta-rowgroups-into-the-columnstore"></a>C. 將所有開啟和關閉的差異資料列群組壓縮到資料行存放區中  
 **適用範圍：** [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 
  
 REORGANIZE WITH ( COMPRESS_ALL_ROW_GROUPS = ON ) 命令會以壓縮的資料列群組方式，將每個 OPEN 和 CLOSED 的差異資料列群組壓縮到資料行存放區中。 這會清空差異存放區，並將所有資料列強制壓縮到資料行存放區。 這在執行許多插入作業之後特別有用，因為這些作業會將資料列儲存在一或多個差異資料行群組中。  
  
 REORGANIZE 可合併資料列群組，讓資料列群組中的資料列數目最高達到資料列 \<= 1,024,576 的數目上限。 因此當您壓縮所有開啟和關閉的資料列群組時，不會產生裡面只有幾個資料列的大量已壓縮資料列群組。 您可以將資料列群組盡量填滿，以縮小壓縮的大小並增進查詢效能。  
  
```sql  
-- Uses AdventureWorksDW2016  
-- Move all OPEN and CLOSED delta rowgroups into the columnstore.  
ALTER INDEX cci_FactInternetSales2 ON FactInternetSales2 REORGANIZE WITH (COMPRESS_ALL_ROW_GROUPS = ON);  
  
-- For a specific partition, move all OPEN AND CLOSED delta rowgroups into the columnstore  
ALTER INDEX cci_FactInternetSales2 ON FactInternetSales2 REORGANIZE PARTITION = 0 WITH (COMPRESS_ALL_ROW_GROUPS = ON);  
```  
  
### <a name="d-defragment-a-columnstore-index-online"></a>D. 線上重組資料行存放區索引  
 不適用於：[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 和 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)]。  
  
 從 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 起，REORGANIZE 不只能將差異資料列群組壓縮到資料行存放區，還能執行其他作業。 它也會執行線上重組。 首先，它會在資料列群組中 10% 或更多資料列已遭到刪除時，實際移除已刪除的資料列，以縮小資料行存放區大小。  然後，它會合併資料列群組以構成較大的資料列群組，每個資料列群組最多可包含 1,024,576 個資料列。  所有變更的資料列群組都會重新壓縮。  
  
> [!NOTE]
> 從 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 起，在大部分情況下已不再需要重建資料行存放區索引，因為 REORGANIZE 會實際移除已刪除的資料列並合併資料列群組。 COMPRESS_ALL_ROW_GROUPS 選項會將所有開啟或關閉的差異資料列群組強制移動到資料行存放區中，之前只能使用重建執行這項作業。 REORGANIZE 在線上且會在背景執行，因此可以在作業執行時繼續進行查詢。  
  
```sql  
-- Uses AdventureWorks  
-- Defragment by physically removing rows that have been logically deleted from the table, and merging rowgroups.  
ALTER INDEX cci_FactInternetSales2 ON FactInternetSales2 REORGANIZE;  
```  
  
### <a name="e-rebuild-a-clustered-columnstore-index-offline"></a>E. 離線重建叢集資料行存放區索引  
適用於：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 開始)   
  
> [!TIP]
> 從 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 起且在 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 中，我們建議使用 ALTER INDEX REORGANIZE，不要使用 ALTER INDEX REBUILD。  
  
> [!NOTE]
> 在 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 和 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 中，REORGANIZE 僅用於將關閉的資料列群組壓縮到資料行存放區。 若要執行重組作業並將所有差異資料列群組強制移動到資料行存放區，重建索引是唯一的方式。  
  
 此範例說明如何重建叢集資料行存放區索引，並將所有差異資料列群組強制移動到資料行存放區。 第一個步驟是準備包含叢集資料行存放區索引的 FactInternetSales2 資料表，並插入前四個資料行的資料。  
  
```sql  
-- Uses AdventureWorksDW  
  
CREATE TABLE dbo.FactInternetSales2 (  
    ProductKey [int] NOT NULL,   
    OrderDateKey [int] NOT NULL,   
    DueDateKey [int] NOT NULL,   
    ShipDateKey [int] NOT NULL);  
  
CREATE CLUSTERED COLUMNSTORE INDEX cci_FactInternetSales2  
ON dbo.FactInternetSales2;  
  
INSERT INTO dbo.FactInternetSales2  
SELECT ProductKey, OrderDateKey, DueDateKey, ShipDateKey  
FROM dbo.FactInternetSales;  
  
SELECT * FROM sys.column_store_row_groups;  
```  
  
 結果顯示有一個開啟的資料列群組，這表示 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 將會等候更多的資料列加入，然後才會關閉資料列群組，並將資料移到資料行存放區。 下一個陳述式會重建叢集資料行存放區索引，這會將所有資料列強制移動到資料行存放區中。  
  
```sql  
ALTER INDEX cci_FactInternetSales2 ON FactInternetSales2 REBUILD;  
SELECT * FROM sys.column_store_row_groups;  
```  
  
 SELECT 陳述式的結果顯示資料列群組為 COMPRESSED，這表示資料列群組的資料行區段現在已壓縮，而且儲存在資料行存放區中。  
  
### <a name="f-rebuild-a-partition-of-a-clustered-columnstore-index-offline"></a>F. 離線重建叢集資料行存放區索引的分割區  
 **適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 開始)  
 
 若要重建大型叢集資料行存放區索引的分割區，請使用 ALTER INDEX REBUILD 與分割區選項。 這個範例會重建分割區 12。 從 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 起，我們建議使用 REORGANIZE 取代 REBUILD。  
  
```sql  
ALTER INDEX cci_fact3   
ON fact3  
REBUILD PARTITION = 12;  
```  
  
### <a name="g-change-a-clustered-columstore-index-to-use-archival-compression"></a>G. 變更叢集資料行存放區索引以使用封存壓縮  
 不適用於：[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)]  
  
 您可以使用 COLUMNSTORE_ARCHIVE 資料壓縮選項，進一步縮小叢集資料行存放區索引的大小。 這項功能非常適合用於想要保存在較便宜儲存裝置上的較舊資料。 我們建議只針對不常存取的資料使用此功能，因為解壓縮速度會比使用一般的 COLUMNSTORE 壓縮慢。  
  
 下列範例會重建叢集資料行存放區索引來使用封存壓縮，然後示範如何移除封存壓縮。 最後的結果只會使用資料行存放區壓縮。  
  
```sql  
--Prepare the example by creating a table with a clustered columnstore index.  
CREATE TABLE SimpleTable (  
    ProductKey [int] NOT NULL,   
    OrderDateKey [int] NOT NULL,   
    DueDateKey [int] NOT NULL,   
    ShipDateKey [int] NOT NULL  
);  
  
CREATE CLUSTERED INDEX cci_SimpleTable ON SimpleTable (ProductKey);  
  
CREATE CLUSTERED COLUMNSTORE INDEX cci_SimpleTable  
ON SimpleTable  
WITH (DROP_EXISTING = ON);  
  
--Compress the table further by using archival compression.  
ALTER INDEX cci_SimpleTable ON SimpleTable  
REBUILD  
WITH (DATA_COMPRESSION = COLUMNSTORE_ARCHIVE);  
  
--Remove the archive compression and only use columnstore compression.  
ALTER INDEX cci_SimpleTable ON SimpleTable  
REBUILD  
WITH (DATA_COMPRESSION = COLUMNSTORE);  
GO  
```  
  
## <a name="examples-rowstore-indexes"></a>範例：資料列存放區索引  
  
### <a name="a-rebuilding-an-index"></a>A. 重建索引  
 下列範例會在 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 資料庫的 `Employee` 資料表上重建單一索引。  
  
```sql  
ALTER INDEX PK_Employee_EmployeeID ON HumanResources.Employee REBUILD;  
```  
  
### <a name="b-rebuilding-all-indexes-on-a-table-and-specifying-options"></a>B. 在資料表上重建所有索引以及指定選項  
 下列範例指定 ALL 關鍵字。 這樣會重建與 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 資料庫中 Production.Product 資料表相關聯的所有索引。 指定三個選項。  
  
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssKatmai](../../includes/ssKatmai-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]  
  
```sql  
ALTER INDEX ALL ON Production.Product  
REBUILD WITH (FILLFACTOR = 80, SORT_IN_TEMPDB = ON, STATISTICS_NORECOMPUTE = ON);  
```  
  
下列範例會加入包括低優先權鎖定選項的 ONLINE 選項，並加入資料列壓縮選項。  
  
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]  
  
```sql  
ALTER INDEX ALL ON Production.Product  
REBUILD WITH   
(  
    FILLFACTOR = 80,   
    SORT_IN_TEMPDB = ON,  
    STATISTICS_NORECOMPUTE = ON,  
    ONLINE = ON ( WAIT_AT_LOW_PRIORITY ( MAX_DURATION = 4 MINUTES, ABORT_AFTER_WAIT = BLOCKERS ) ),   
    DATA_COMPRESSION = ROW  
);  
```  
  
### <a name="c-reorganizing-an-index-with-lob-compaction"></a>C. 重新組織具有 LOB 壓縮的索引  
 下列範例會重新組織 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 資料庫中的單一叢集索引。 由於索引在分葉層級中包含 LOB 資料類型，因此，這個陳述式也會壓縮包含大型物件資料的所有頁面。 請注意，您不需要指定 WITH (LOB_COMPACTION = ON) 選項，因為預設值是 ON。  
  
```sql  
ALTER INDEX PK_ProductPhoto_ProductPhotoID ON Production.ProductPhoto REORGANIZE WITH (LOB_COMPACTION = ON);  
```  
  
### <a name="d-setting-options-on-an-index"></a>D. 設定索引選項  
 下列範例會設定 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 資料庫中 `AK_SalesOrderHeader_SalesOrderNumber` 索引的幾個選項。  
  
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssKatmai](../../includes/ssKatmai-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]  
  
```sql  
ALTER INDEX AK_SalesOrderHeader_SalesOrderNumber ON  
    Sales.SalesOrderHeader  
SET (  
    STATISTICS_NORECOMPUTE = ON,  
    IGNORE_DUP_KEY = ON,  
    ALLOW_PAGE_LOCKS = ON  
    ) ;  
GO
```  
  
### <a name="e-disabling-an-index"></a>E. 停用索引  
 下列範例會停用 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 資料庫中 `Employee` 資料表的非叢集索引。  
  
```sql  
ALTER INDEX IX_Employee_ManagerID ON HumanResources.Employee DISABLE;
```  
  
### <a name="f-disabling-constraints"></a>F. 停用條件約束  
 下列範例會藉由停用 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 資料庫中 PRIMARY KEY 索引來停用 PRIMARY KEY 條件約束。 基礎資料表的 FOREIGN KEY 條件約束會自動停用，並且會顯示一則警告訊息。  
  
```sql  
ALTER INDEX PK_Department_DepartmentID ON HumanResources.Department DISABLE;  
```  
  
結果集會傳回這則警告訊息。  
  
```  
Warning: Foreign key 'FK_EmployeeDepartmentHistory_Department_DepartmentID'  
on table 'EmployeeDepartmentHistory' referencing table 'Department'  
was disabled as a result of disabling the index 'PK_Department_DepartmentID'.
```  
  
### <a name="g-enabling-constraints"></a>G. 啟用條件約束  
 下列範例會啟用 F 範例所停用的 PRIMARY KEY 和 FOREIGN KEY 條件約束。  
  
PRIMARY KEY 條件約束是藉由重建 PRIMARY KEY 索引來啟用。  
  
```sql  
ALTER INDEX PK_Department_DepartmentID ON HumanResources.Department REBUILD;  
```  
  
然後會啟用 FOREIGN KEY 條件約束。  
  
```sql  
ALTER TABLE HumanResources.EmployeeDepartmentHistory  
CHECK CONSTRAINT FK_EmployeeDepartmentHistory_Department_DepartmentID;  
GO  
```  
  
### <a name="h-rebuilding-a-partitioned-index"></a>H. 重建分割區索引  
 下列範例會重建 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 資料庫中分割區索引 `5` 的單一分割區，分割區編號是 `IX_TransactionHistory_TransactionDate`。 分割區 5 在線上重建，而且低優先權鎖定的 10 分鐘等候時間會分別套用至索引重建作業取得的每一個鎖定。 如果在此時間無法取得鎖定來完成索引重建，重建作業陳述式會中止。  
  
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]  
  
```sql  
-- Verify the partitioned indexes.  
SELECT *  
FROM sys.dm_db_index_physical_stats (DB_ID(),OBJECT_ID(N'Production.TransactionHistory'), NULL , NULL, NULL);  
GO  
--Rebuild only partition 5.  
ALTER INDEX IX_TransactionHistory_TransactionDate  
ON Production.TransactionHistory  
REBUILD Partition = 5   
   WITH (ONLINE = ON (WAIT_AT_LOW_PRIORITY (MAX_DURATION = 10 minutes, ABORT_AFTER_WAIT = SELF)));  
GO  
```  
  
### <a name="i-changing-the-compression-setting-of-an-index"></a>I. 變更索引的壓縮設定  
 下列範例會在非分割資料列存放區資料表上重建索引。  
  
```sql
ALTER INDEX IX_INDEX1   
ON T1  
REBUILD   
WITH (DATA_COMPRESSION = PAGE);  
GO  
```  
  
如需其他資料壓縮範例，請參閱[資料壓縮](../../relational-databases/data-compression/data-compression.md)。  
 
### <a name="j-online-resumable-index-rebuild"></a>J. 線上可繼續的索引重建

**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]   

 以下範例說明如何使用線上可繼續的索引重建。 

1. 以 MAXDOP=1 的可繼續作業方式，執行線上索引重建。

   ```sql
   ALTER INDEX test_idx on test_table REBUILD WITH (ONLINE=ON, MAXDOP=1, RESUMABLE=ON) ;
   ```

2. 在索引作業暫停之後再次執行相同命令 (請參閱上述說明)，會自動繼續索引重建作業。

3. 以 MAX_DURATION 設為 240 分鐘 的可繼續作業方式，執行線上索引重建。

   ```sql
   ALTER INDEX test_idx on test_table REBUILD WITH (ONLINE=ON, RESUMABLE=ON, MAX_DURATION=240) ; 
   ```
4. 暫停執行中的可繼續的線上索引重建。

   ```sql
   ALTER INDEX test_idx on test_table PAUSE ;
   ```   
5. 繼續索引重建 (以指定新值將 MAXDOP 設為 4 之可繼續的作業方式執行) 的線上索引重建。

   ```sql
   ALTER INDEX test_idx on test_table RESUME WITH (MAXDOP=4) ;
   ```
6. 繼續索引線上重建 (以可繼續的方式執行) 的線上索引重建作業。 將 MAXDOP 設為 2，將索引 (以可繼續方式執行) 的執行時間設定為 240 分鐘，並且如果索引在鎖定上遭到封鎖，請等待 10 分鐘，然後在那之後終止所有封鎖者。 

   ```sql
      ALTER INDEX test_idx on test_table  
         RESUME WITH (MAXDOP=2, MAX_DURATION= 240 MINUTES, 
         WAIT_AT_LOW_PRIORITY (MAX_DURATION=10, ABORT_AFTER_WAIT=BLOCKERS)) ;
   ```      
7. 中止執行中或已暫停的可繼續的索引重建作業。

   ```sql
   ALTER INDEX test_idx on test_table ABORT ;
   ``` 
  
## <a name="see-also"></a>另請參閱  
[SQL Server 索引架構和設計指南](../../relational-databases/sql-server-index-design-guide.md)     
[線上執行索引作業](../../relational-databases/indexes/perform-index-operations-online.md)    
[CREATE INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/create-index-transact-sql.md)     
[CREATE SPATIAL INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/create-spatial-index-transact-sql.md)   
[CREATE XML INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/create-xml-index-transact-sql.md)   
[DROP INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/drop-index-transact-sql.md)   
[停用索引和條件約束](../../relational-databases/indexes/disable-indexes-and-constraints.md)   
[XML 索引 &#40;SQL Server&#41;](../../relational-databases/xml/xml-indexes-sql-server.md)   
[重新組織與重建索引](../../relational-databases/indexes/reorganize-and-rebuild-indexes.md)   
[sys.dm_db_index_physical_stats &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-index-physical-stats-transact-sql.md)   
[EVENTDATA &#40;Transact-SQL&#41;](../../t-sql/functions/eventdata-transact-sql.md)    
