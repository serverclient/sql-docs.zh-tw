---
description: 資料行存放區索引 - 新功能
title: 資料行存放區索引 - 新功能 | Microsoft Docs
ms.custom: ''
ms.date: 05/11/2020
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: table-view-index
ms.topic: conceptual
author: MikeRayMSFT
ms.author: mikeray
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: a9fc87cef0244afc4459c789f94855c0c0530ddd
ms.sourcegitcommit: e5664d20ed507a6f1b5e8ae7429a172a427b066c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/19/2020
ms.locfileid: "97697122"
---
# <a name="columnstore-indexes---what39s-new"></a>資料行存放區索引 - 新功能
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的每個版本以及 [!INCLUDE[ssSDS](../../includes/sssds-md.md)]、[!INCLUDE[ssSDW](../../includes/sssdw-md.md)] 和 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] 的最新版本，皆可使用資料行存放區功能的摘要。  

 > [!NOTE]
 > 針對 [!INCLUDE[ssSDS](../../includes/sssds-md.md)]，[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 進階層、標準層 - S3 及更新版本，和所有 vCore 層可使用資料行存放區索引。 針對 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] SP1 及更新版本，則所有版本皆可使用資料行存放區索引。 針對 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] (SP1 以前) 及舊版，則只有 Enterprise Edition 才可使用資料行存放區索引。
 
## <a name="feature-summary-for-product-releases"></a>產品版本的功能摘要  
 本表會摘要說明資料行存放區索引的重要功能以及提供它們的產品。  

|資料行存放區索引功能|[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)]|[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)]|[!INCLUDE[ssSQL15](../../includes/sssql15-md.md)]|[!INCLUDE[ssSQL17](../../includes/sssql17-md.md)]|[!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)]|[!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)]|[!INCLUDE[ssSDW](../../includes/sssdw-md.md)]|  
|-------------------------------|---------------------------|---------------------------|---------------------------|---------------------------|--------------------------------------------|-------------------------|---|  
|多執行緒查詢的批次模式執行|是|是|是|是|是|是|是| 
|單一執行緒查詢的批次模式執行|||是|是|是|是|是|  
|封存壓縮選項||是|是|是|是|是|是|  
|快照集隔離和讀取認可快照集隔離|||是|是|是|是|是| 
|建立資料表時指定資料行存放區索引|||是|是|是|是|是|  
|AlwaysOn 支援資料行存放區索引|是|是|是|是|是|是|是| 
|AlwaysOn 可讀取次要支援唯讀的非叢集資料行存放區索引|是|是|是|是|是|是|是|  
|AlwaysOn 可讀取次要支援可更新的資料行存放區索引|||是||是|||  
|堆積或 B-tree 的唯讀非叢集資料行存放區索引|是|是|是 <sup>1</sup>|是 <sup>1</sup>|是 <sup>1</sup>|是 <sup>1</sup>|是 <sup>1</sup>|  
|堆積或 B-tree 的可更新非叢集資料行存放區索引|||是|是|是|是|是|  
|有非叢集資料行存放區索引的堆積或 B-tree 允許其他的 B-tree 索引|是|是|是|是|是|是|是|  
|可更新的叢集資料行存放區索引||是|是|是||是|是|  
|叢集資料行存放區索引的 B-tree 索引|||是|是||是|是|  
|記憶體最佳化資料表的資料行存放區索引|||是|是||是|是|  
|非叢集資料行存放區索引定義支援使用篩選的條件|||是|是|是|是|是|  
|`CREATE TABLE` 和 `ALTER TABLE` 中資料行存放區索引的壓縮延遲選項|||是|是|是|是|是|
|資料行存放區索引可以具有非保存的計算資料行||||是|是|||   
|Tuple Mover 背景合併支援||||||是|是|是|
  
 <sup>1</sup> 若要建立唯讀的非叢集資料行存放區索引，請將索引儲存在唯讀的檔案群組中。  
 
> [!NOTE]
> [批次模式](../../relational-databases/query-processing-architecture-guide.md#batch-mode-execution)作業的平行處理原則程度 (DOP) 限制如下：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Standard Edition 為 2，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Web Edition 和 Express Edition 為 1。 這會參考以磁碟式資料表和記憶體最佳化資料表建立的資料行存放區索引。

## [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 
 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 新增這些新功能。

### <a name="functional"></a>函數
- 從 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 開始，背景合併工作將會協助 Tuple Mover，該工作會自動壓縮已存在一段時間的較小 OPEN 差異資料列群組 (由內部閾值決定)，或合併已刪除大量資料列的 COMPRESSED 資料列群組。 之前，需要進行索引重新組織作業，才能合併包含部分刪除之資料的資料列群組。 這可改善一段時間的資料行存放區索引品質。 

## [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 
 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 新增這些新功能。

### <a name="functional"></a>函數
- [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 支援叢集資料行存放區索引中的非保存計算資料行。 叢集資料行存放區索引中不支援保存的計算資料行。 您無法在具有計算資料行的資料行存放區索引上建立非叢集索引。 

## [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)]  
 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 會加入索引鍵增強功能，以改善資料行存放區索引的效能和彈性。 這些改善會強化資料倉儲案例，並進行即時作業分析。  
  
### <a name="functional"></a>函數  
  
-   資料列存放區資料表可以有一個可更新的非叢集資料行存放區索引。 非叢集資料行存放區索引以前是唯讀的。  
  
-   非叢集資料行存放區索引定義支援使用篩選的條件。 若要將 OLTP 資料表新增資料行存放區索引對效能的影響降到最低，請只對您作業的工作負載冷資料，使用篩選的條件建立非叢集資料行存放區索引。 
  
-   記憶體中的資料表可以有一個資料行存放區索引。 您可以在建立資料表時予以建立，或稍後使用 [ALTER TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-table-transact-sql.md) 將其加入。 從前，只有磁碟資料表可以有資料行存放區索引。  
  
-   叢集資料行存放區索引可以有一或多個非叢集資料列存放區索引。 資料行存放區索引以前不支援非叢集索引。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會自動維護 DML 作業的非叢集索引。  
  
-   您可以使用 B-tree 索引對叢集資料行存放區索引強制執行這些條件約束，來支援主索引鍵和外部索引鍵。  
  
-   資料行存放區索引的壓縮延遲選項，可將即時作業分析的交易工作負載影響降到最低。  這個選項允許經常變更的資料列在穩定後，再壓縮到資料行存放區。 如需詳細資訊，請參閱 [CREATE COLUMNSTORE INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/create-columnstore-index-transact-sql.md) 和[開始使用資料行存放區進行即時作業分析](../../relational-databases/indexes/get-started-with-columnstore-for-real-time-operational-analytics.md)。  
  
### <a name="performance-for-database-compatibility-level-120-or-130"></a>資料庫相容性等級 120 或 130 的效能  
  
-   資料行存放區索引支援讀取認可快照集隔離等級 (RCSI) 和快照集隔離 (SI)。 這可讓交易式一致性分析查詢沒有任何鎖定。  
  
-   資料行存放區透過移除刪除的資料列，但不必明確重建索引，來支援索引重組。 `ALTER INDEX ... REORGANIZE` 陳述式會根據內部定義的原則，如同線上作業從資料行存放區中移除已刪除的資料列  
  
-   您可在 Always On 可讀取次要複本上存取資料行存放區索引。 您可將分析查詢卸載到 Always On 次要複本，以改善作業分析的效能。  
  
-   當資料類型使用不超過 8 個位元組，且不是字串類型時，彙總下推會在資料表掃描期間計算彙總函式 `MIN`、`MAX`、`SUM`、`COUNT` 和 `AVG`。 無論是否使用 `GROUP BY` 子句，叢集資料行存放區索引和非叢集資料行存放區都支援彙總下推。 在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 上，這項增強功能已保留給 Enterprise Edition。
  
-   字串述詞下推會加速比較 VARCHAR/CHAR 或 NVARCHAR/NCHAR 字串類型的查詢。 這適用於一般比較運算子和包括運算子，例如使用點陣圖篩選的 `LIKE`。 這適用於所有支援的定序。 在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 上，這項增強功能已保留給 Enterprise Edition。 

-   利用向量型硬體功能增強批次模式作業。 [!INCLUDE[ssde_md](../../includes/ssde_md.md)] 會偵測 AVX 2 (Advanced Vector Extensions) 和 SSE 4 (Streaming SIMD Extensions 4) 硬體擴充的 CPU 支援層級，並在支援時使用。 在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 上，這項增強功能已保留給 Enterprise Edition。
  
### <a name="performance-for-database-compatibility-level-130"></a>資料庫相容性等級 130 的效能  
  
-   新的批次模式執行支援使用任一這些作業的查詢︰  
    -   `SORT`  
    -   使用多個不同的函式進行彙總。 部分範例：`COUNT/COUNT`、`AVG/SUM`、`CHECKSUM_AGG`、`STDEV/STDEVP`  
    -   視窗彙總函式：`COUNT`、`COUNT_BIG`、`SUM`、`AVG`、`MIN`、`MAX` 和 `CLR`  
    -   視窗使用者定義彙總：`CHECKSUM_AGG`、`STDEV`、`STDEVP`、`VAR`、`VARP` 和 `GROUPING`  
    -   視窗彙總分析函式：`LAG`、`LEAD`、`FIRST_VALUE`、`LAST_VALUE`、`PERCENTILE_CONT`、`PERCENTILE_DISC`、`CUME_DIST` 和 `PERCENT_RANK`  

-   在 `MAXDOP 1` 下執行，或以批次模式執行序列查詢計畫的單一執行緒查詢。 以前只有多執行緒查詢可以批次執行的方式來執行。  

-   在存取資料列存放區或資料行存放區索引中的資料時，記憶體最佳化資料表查詢在 SQL InterOp 模式中可以有平行計畫。
  
### <a name="supportability"></a>支援能力  
這些系統檢視表對資料行存放區而言是新的︰  
  
:::row:::
    :::column:::
        [sys.column_store_row_groups &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-column-store-row-groups-transact-sql.md)
    :::column-end:::
    :::column:::
        [sys.dm_column_store_object_pool &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-column-store-object-pool-transact-sql.md)
    :::column-end:::
:::row-end:::  
:::row:::
    :::column:::
        [sys.dm_db_column_store_row_group_operational_stats &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-column-store-row-group-operational-stats-transact-sql.md)
    :::column-end:::
    :::column:::
        [sys.dm_db_column_store_row_group_physical_stats &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-column-store-row-group-physical-stats-transact-sql.md)
    :::column-end:::
:::row-end:::  
:::row:::
    :::column:::
        [sys.dm_db_index_operational_stats &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-index-operational-stats-transact-sql.md)
    :::column-end:::
    :::column:::
        [sys.dm_db_index_physical_stats &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-index-physical-stats-transact-sql.md)
    :::column-end:::
:::row-end:::  
:::row:::
    :::column:::
        [sys.internal_partitions &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-internal-partitions-transact-sql.md)
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::
  
這些記憶體中的 OLTP 型 DMV 包含資料行存放區的更新︰  

:::row:::
    :::column:::
        [sys.dm_db_xtp_hash_index_stats &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-xtp-hash-index-stats-transact-sql.md)
    :::column-end:::
    :::column:::
        [sys.dm_db_xtp_index_stats &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-xtp-index-stats-transact-sql.md)
    :::column-end:::
:::row-end:::  
:::row:::
    :::column:::
        [sys.dm_db_xtp_memory_consumers &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-xtp-memory-consumers-transact-sql.md)
    :::column-end:::
    :::column:::
        [sys.dm_db_xtp_nonclustered_index_stats &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-xtp-nonclustered-index-stats-transact-sql.md)
    :::column-end:::
:::row-end:::  
:::row:::
    :::column:::
        [sys.dm_db_xtp_object_stats &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-xtp-object-stats-transact-sql.md)
    :::column-end:::
    :::column:::
        [sys.dm_db_xtp_table_memory_stats &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-xtp-table-memory-stats-transact-sql.md)
    :::column-end:::
:::row-end:::

### <a name="limitations"></a>限制    
-   至於記憶體中資料表，資料行存放區索引必須包含所有的資料行，而資料行存放區索引不能有篩選的條件。  
-   至於記憶體中資料表，針對資料行存放區索引的查詢只能在 InterOP 模式中執行，不是在記憶體中原生模式中執行。 支援平行執行。  
  
## [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)]  
 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 引進叢集資料行存放區索引，作為主要的儲存體格式。 這允許一般的負載以及更新、刪除和插入作業。  
  
-   資料表可以使用叢集資料行存放區索引作為主要的資料表儲存體。 資料表上不允許其他索引，但是叢集資料行存放區索引是可以更新的，所以您可以執行一般的載入並對個別資料列進行變更。  
-   非叢集資料行存放區索引繼續保有在 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 中的相同功能，但現在可在批次模式中執行其他運算子。 而除非重建和使用資料分割切換，否則仍然不能更新。 只有磁碟資料表支援非叢集資料行存放區索引，記憶體中資料表不支援。  
-   叢集與非叢集資料行存放區索引都有封存壓縮選項，可進一步壓縮資料。 封存選項有利於減少記憶體中和磁碟上的資料大小，但是確實會降低查詢效能。 它非常適合不常存取的資料。  
-   叢集資料行存放區索引和非叢集資料行存放區索引的作用十分相似，它們使用相同的單欄式儲存體格式、相同的查詢處理引擎，和相同的動態管理檢視集合。 差別在於主要和次要的索引類型，而且非叢集資料行存放區索引是唯讀的。  
-   這些運算子可在批次模式下執行多執行緒查詢︰scan、filter、project、join、group by 和 union all。  
  
## [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)]  
 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 引進非叢集資料行存放區索引，作為資料列存放區資料表的另一個索引類型和批次處理資料行存放區資料的查詢。  
  
-   資料列存放區資料表可以有一個非叢集資料行存放區索引。  
-   資料行存放區索引是唯讀的。 建立資料行存放區索引之後，您無法執行 `INSERT`、`DELETE` 和 `UPDATE` 作業來更新資料表；若要執行這些作業，您必須卸除索引，並更新資料表，然後重建資料行存放區索引。 您可以切換資料分割，在資料表中載入其他資料。 資料分割切換的優點是您可以載入資料，卻不用卸除和重建資料行存放區索引。  
-   資料行存放區索引一定需要額外的儲存體，通常是資料列存放區多加 10%，因為它會儲存一份資料複本。  
-   批次處理序提供 2 倍或更高的查詢效能，但僅供平行查詢執行使用。  
  
## <a name="see-also"></a>另請參閱  
 [資料行存放區索引設計指導](../../relational-databases/indexes/columnstore-indexes-design-guidance.md)   
 [資料行存放區索引資料載入指導](../../relational-databases/indexes/columnstore-indexes-data-loading-guidance.md)   
 [資料行存放區索引效能](../../relational-databases/indexes/columnstore-indexes-query-performance.md)   
 [開始使用資料行存放區進行即時作業分析](../../relational-databases/indexes/get-started-with-columnstore-for-real-time-operational-analytics.md)   
 [資料倉儲的資料行存放區索引](../../relational-databases/indexes/columnstore-indexes-data-warehouse.md)   
 [重新組織與重建索引](../../relational-databases/indexes/reorganize-and-rebuild-indexes.md)
  
