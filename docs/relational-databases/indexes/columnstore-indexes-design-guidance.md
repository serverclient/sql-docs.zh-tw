---
description: 資料行存放區索引 - 設計指導
title: 資料行存放區索引 - 設計指導 | Microsoft Docs
ms.custom: ''
ms.date: 12/01/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: table-view-index
ms.topic: conceptual
ms.assetid: fc3e22c2-3165-4ac9-87e3-bf27219c820f
author: MikeRayMSFT
ms.author: mikeray
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 72ab05dfce314119c30e08428fdcaa2b94ba25ed
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98171770"
---
# <a name="columnstore-indexes---design-guidance"></a>資料行存放區索引 - 設計指導
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

資料行存放區索引設計的高階建議。 下列幾個良好的設計決策，有助您實現資料行存放區索引所設計的高資料壓縮和查詢效能。 

## <a name="prerequisites"></a>必要條件

本文假設您熟悉資料行存放區架構和術語。 如需詳細資訊，請參閱[資料行存放區索引 - 概觀](../../relational-databases/indexes/columnstore-indexes-overview.md)和[資料行存放區索引架構](../../relational-databases/sql-server-index-design-guide.md#columnstore_index)。

### <a name="know-your-data-requirements"></a>了解自身的資料需求
在設計資料行存放區索引之前，請先盡可能了解您的資料需求。 例如，您可以思考下列問題的答案：

- 資料表有多大？
- 查詢執行的大多數分析是否要掃描大範圍的值？  資料行存放區索引是專為大範圍掃描精心設計，而不是用於查閱特定值。
- 工作負載是否會執行大量更新和刪除？ 資料行存放區索引適用於資料穩定的情況。 因此，查詢應僅針對 10% 以下的資料列進行更新和刪除。
- 是否具備事實資料表和維度資料表的資料倉儲？
- 是否需要對交易式工作負載執行分析？ 如果是這種情況，請參閱資料行存放區設計指引以了解即時操作分析。

您有可能不需要資料行存放區索引。 針對搜尋資料的查詢、特定值的搜尋，或搜尋小範圍值的查詢，資料列存放區資料表 (含堆積或叢集索引) 的效果最佳。 您應針對交易式工作負載使用資料列存放區索引，因為這些工作負載主要需要資料表搜尋，而不是大範圍資料表掃描。  

## <a name="choose-the-best-columnstore-index-for-your-needs"></a>視需要選擇最適合的資料行存放區索引

您可以選擇叢集或非叢集資料行存放區索引。  叢集資料行存放區索引可以有一或多個非叢集 B 型樹狀結構索引。 您可以輕鬆試用資料行存放區索引。 如果您將資料表建立為資料行存放區索引，只要卸除資料行存放區索引，即可輕鬆將資料表轉換回資料列存放區資料表。 

以下是選項和建議的摘要。 

| 資料行存放區選項 | 建議使用時機 | 壓縮 |
| :----------------- | :------------------- | :---------- |
| 叢集資料行存放區索引 | 適用於：<br></br>1) 具有星形或雪花式結構描述的傳統資料倉儲工作負載。<br></br>2) 物聯網 (IOT) 工作負載，其可插入含最低限度更新和刪除的大量資料。 | 平均 10 倍 |
| 叢集資料行存放區索引的非叢集 B 型樹狀結構索引 | 用途：<br></br>    1.在叢集資料行存放區索引上，強制執行主索引鍵和外部索引鍵條件約束。<br></br>    2.加速搜尋特定值或小範圍值的查詢。<br></br>    3.加速特定資料列的更新和刪除。| 平均為 10 倍，加上 NCI 一些額外的儲存體。|
| 以磁碟為基礎的堆積或 B 型樹狀結構索引的非叢集資料行存放區索引 | 適用於： <br></br>1) 具有一些分析查詢的 OLTP 工作負載。 您可以卸除為了分析所建立的 B 型樹狀結構索引，並將其取代為一個非叢集資料行存放區索引。<br></br>2) 許多傳統 OLTP 工作負載，其可執行擷取轉換載入 (ETL) 作業，以將資料移至不同的資料倉儲。 您可以在某些 OLTP 資料表上建立非叢集資料行存放區索引，以避免產生 ETL 和個別的資料倉儲。 | NCCI 是額外索引，平均需要多 10% 的儲存體。|
| 記憶體中的資料表上的資料行存放區索引 | 相關建議與以磁碟為基礎之資料表上的非叢集資料行存放區索引相同，除非基底資料表是記憶體中的資料表。 | 資料行存放區索引是額外的索引。|

## <a name="use-a-clustered-columnstore-index-for-large-data-warehouse-tables"></a>針對大型資料倉儲資料表，使用叢集資料行存放區索引
叢集資料行存放區索引不單只是索引，而是主要資料表儲存體。 它可以實現大型資料倉儲的事實和維度資料表的高度資料壓縮，並大幅改善查詢效能。 叢集資料行存放區索引最適合用於分析查詢，而不是交易式的查詢，因為分析查詢通常會對大範圍的值執行作業，而不是查閱特定值。 

在下列情況下，請考慮使用叢集資料行存放區索引：

- 每個分割區至少有 1 百萬個資料列。 資料行存放區索引的每個分割區內會有資料列群組。 如果資料表太小無法容納每個分割區內的資料列群組，您就無法發揮資料行存放區的壓縮和查詢效能優勢。
- 查詢主要會對各範圍的值執行分析。 例如，若要尋找資料行的平均值，查詢必須掃描所有資料行值。 接著，查詢會加總這些值以判斷平均值，並進行彙總。
- 大多數的插入作業都會發生在含最低限度更新和刪除的大量資料中。 物聯網 (IOT) 等許多工作負載，其可插入含最低限度更新和刪除的大量資料。 這些工作負載可以藉由使用叢集資料行存放區索引，發揮壓縮和查詢效能提升的優勢。

在下列情況下，請不要使用叢集資料行存放區索引：

* 資料表需使用 varchar(max)、nvarchar(max) 或 varbinary(max) 的資料類型。 或者，您可以將資料行存放區索引設計為不包括這些資料行。
* 資料表資料不是永久性的。 當您需要快速儲存和刪除資料時，請考慮使用堆積或暫存資料表。
* 資料表中每個分割區的資料列必須少於 1 百萬個。 
* 資料表上超過 10% 的作業都是更新和刪除。 大量更新和刪除會形成片段， 而片段會影響壓縮率和查詢效能，除非您執行 Reorganize 作業，將所有資料強制放入資料行存放區，並移除片段。 如需詳細資訊，請參閱[將資料行存放區索引中的索引片段最小化](/archive/blogs/sqlserverstorageengine/columnstore-index-defragmentation-using-reorganize-command)。

如需詳細資訊，請參閱[資料行存放區索引 - 資料倉儲](../../relational-databases/indexes/columnstore-indexes-data-warehouse.md)。

## <a name="add-b-tree-nonclustered-indexes-for-efficient-table-seeks"></a>新增 B 型樹狀結構非叢集索引，以保障有效率的資料表搜尋

從 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 開始，您可以將非叢集 B 型樹狀結構索引建立為叢集資料行存放區索引上的次要索引。 資料行存放區索引發生變更時，會更新 B 型樹狀結構非叢集索引。 您可以使用這項強大功能來發揮優勢。 

藉由使用次要 B 型樹狀結構索引，您可以有效率地搜尋特定的資料列，而不需掃描所有資料列。  其他選項現在也可以使用。 例如，您可以在 B 型樹狀結構索引上使用 UNIQUE 條件約束，強制執行主索引鍵或外部索引鍵條件約束。 由於非唯一的值無法插入 B 型樹狀結構索引，因此 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 無法將值插入資料行存放區。 

請考慮在資料行存放區索引上使用 B 型樹狀結構索引，以便：
* 執行用以搜尋特定值或小範圍值的查詢。
* 強制執行主索引鍵條件約束或外部索引鍵條件約束。
* 有效率地執行 Update 和 Delete 作業。 B 型樹狀結構索引能夠快速找到要更新和刪除的特定資料列，而不需掃描整個資料表或資料表的分割區。
* 擁有其他可用於存放 B 型樹狀結構索引的儲存體。

## <a name="use-a-nonclustered-columnstore-index-for-real-time-analytics"></a>使用非叢集資料行存放區索引進行即時分析

從 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 開始，您可以在以磁碟為基礎的資料列存放區資料表或記憶體內部 OLTP 資料表上，使用非叢集資料行存放區索引。 這樣一來，即可在交易式資料表上執行即時的分析。 當基礎資料表上發生交易時，您可以在資料行存放區索引上執行分析。 由於一個資料表可以管理兩種索引，因此變更可即時在資料列存放區和資料行存放區索引中生效。

比起資料列存放區索引，資料行存放區索引可提供更高 10 倍的資料壓縮功能，因此它只需要少量的額外儲存空間。 例如，如果壓縮的資料列存放區資料表需要 20 GB，資料行存放區索引可能額外需要 2 GB。 所需的額外空間也取決於非叢集資料行存放區索引中的資料行數目而定。 

 請考慮使用非叢集資料行存放區索引，以便：

* 在交易式資料列存放區資料表上執行即時的分析。 您可以將適用於分析的現有 B 型樹狀結構索引取代為非叢集資料行存放區索引。 
  
*   免除個別資料倉儲的需要。 傳統上，公司會在資料列存放區資料表上執行交易，再將資料載入不同的資料倉儲執行分析。 如果工作負載很多，您可以在交易式資料表上建立非叢集資料行存放區索引，以避免載入處理序與個別的資料倉儲。

  [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 提供數種策略，可讓這個案例更有效能。 您可以啟用非叢集資料行存放區索引，而不需變更您的 OLTP 應用程式，因此可以輕鬆試用。 

若要新增額外的處理資源，請在可讀取的次要複本上執行分析。 使用可讀取次要複本時，可以區隔交易式工作負載和分析工作負載的處理。 

如需詳細資訊，請參閱[開始使用資料行存放區索引進行即時作業分析](../../relational-databases/indexes/get-started-with-columnstore-for-real-time-operational-analytics.md)。

如需如何選擇最佳資料行存放區索引的詳細資訊，請參閱 Sunil Agarwal 的部落格文章 [Which columnstore index is right for my workload?](/archive/blogs/sql_server_team/columnstore-index-which-columnstore-index-is-right-for-my-workload) (哪一個資料行存放區索引最適合我的工作負載？)。

## <a name="use-table-partitions-for-data-management-and-query-performance"></a>使用資料表分割區以保障資料管理和查詢效能
資料行存放區索引可支援資料分割，這是管理和封存資料的好方法。 資料分割也能限制一個或多個分割區的作業，以提升查詢效能。

### <a name="use-partitions-to-make-the-data-easier-to-manage"></a>使用資料分割來讓資料更容易管理
對於大型資料表，若要管理各種範圍的資料，唯一實用的作法是使用資料分割。 資料列存放區資料表分割區的優點也適用於資料行存放區索引。 

例如，資料列存放區和資料行存放區資料表皆會使用分割區，以便：

- 控制增量備份的大小。 您可以將分割區備份至不同的檔案群組，然後將它們標示為唯讀。 如此一來，未來的備份就會略過唯讀檔案群組。 
- 藉由將較舊的分割區移至較便宜的儲存體，以節省儲存成本。 例如，您可以使用分割區切換，將分割區移至較便宜的儲存位置。
- 藉由限制分割區的作業，以有效率地執行作業。 例如，您可以僅鎖定片段的分割區進行索引維護。

此外，使用資料行存放區索引時，您可以透過資料分割進行下列作業：

* 節省額外 30% 的儲存成本。 您可以使用 COLUMNSTORE_ARCHIVE 壓縮選項來壓縮舊分割區。 資料的查詢效能會較慢，但如果分割區不常進行查詢，這是可以接受的程度。

### <a name="use-partitions-to-improve-query-performance"></a>使用分割區來提升查詢效能

使用分割區時，您可以將查詢限制為僅掃描特定的分割區，藉此限制掃描的資料列數目。 例如，如果索引是依年份來分割，而您要進行去年資料的分析查詢，則此查詢只需要掃描一個分割區中的資料。 

### <a name="use-fewer-partitions-for-a-columnstore-index"></a>針對區資料行存放區索引，使用較少的分割區

除非您的資料量夠大，否則當資料行存放區索引含較少分割區 (相較於資料列存放區索引所用的分割區) 時執行效果最好。 如果每個資料分割區的資料列不足 1 百萬個，大部分的資料列可能會移至差異存放區中，而這個位置就無法享有資料行存放區壓縮的效能優勢。 例如，如果您將 1 百萬個資料列載入具有 10 個分割區的資料表，每個分割區收到 100,000 個資料列，則所有資料列都會移到差異資料列群組。 

範例：
* 將 1,000,000 個資料列載入一個分割區或非資料分割的資料表。 您會取得一個壓縮資料列群組，其中含有 1,000,000 個資料列。 這可以確保高資料壓縮與快速的查詢效能。
* 將 1,000,000 個資料列平均載入 10 個分割區。 每個分割區取得 100,000 個資料列，這低於資料行存放區壓縮的最小臨界值。 因此，資料行存放區索引可能會有 10 個差異資料列群組，每個含有 100,000 個資料列。 您可以使用一些方法，強制將差異資料列群組放入資料行存放區。 不過，如果資料行存放區索引中僅有這些資料列，壓縮的資料列群組就會太小，而無法達到最佳壓縮和查詢效能。

如需資料分割的詳細資訊，請參閱 Sunil Agarwal 的部落格文章 [Should I partition my columnstore index?](/archive/blogs/sqlserverstorageengine/columnstore-index-should-i-partition-my-columnstore-index) (我需要針對資料行存放區索引進行資料分割嗎？)。

## <a name="choose-the-appropriate-data-compression-method"></a>選擇適當的資料壓縮方法
資料行存放區索引提供兩種資料壓縮的選擇：資料行存放區壓縮及封存壓縮。 您可以在建立索引時選擇壓縮選項，或於稍後使用下列項目來變更：[ALTER INDEX ...REBUILD](../../t-sql/statements/alter-index-transact-sql.md)。

### <a name="use-columnstore-compression-for-best-query-performance"></a>如需最佳的查詢效能，請使用資料行存放區壓縮
資料行存放區壓縮通常可實現優於資料列存放區索引 10 倍的壓縮率。 它是資料行存放區索引的標準壓縮方法，並可提供快速的查詢效能。 

### <a name="use-archive-compression-for-best-data-compression"></a>如需最佳的資料壓縮，請使用封存壓縮
當查詢效能不重要時，建議使用適合進行最大壓縮的封存壓縮。 它可以實現比資料行存放區壓縮更高的資料壓縮率，但也必須付出代價。 它會花費更多時間來壓縮和解壓縮資料，因此不適合需要快速查詢效能的情況。 

## <a name="use-optimizations-when-you-convert-a-rowstore-table-to-a-columnstore-index"></a>將資料列存放區資料表轉換成資料行存放區索引時，請使用最佳化

如果您的資料已在資料列存放區資料表中，您可以使用 [CREATE COLUMNSTORE INDEX](../../t-sql/statements/create-columnstore-index-transact-sql.md)，將資料表轉換成叢集資料行存放區索引。 在轉換資料表後，還有一些最佳化功能可以改善查詢效能，將於稍後描述。

### <a name="use-maxdop-to-improve-rowgroup-quality"></a>使用 MAXDOP 來提升資料列群組品質
您可以設定處理器的數目上限，以將堆積或叢集的 B 型樹狀結構索引轉換為資料行存放區索引。 若要設定處理器，請使用平行處理原則的最大程度選項 (MAXDOP)。 

如果您有大量的資料，MAXDOP 1 可能會太慢。  將 MAXDOP 增加到 4 則比較恰當。 如果這麼做導致幾個資料列群組的資料列數目不理想，您可以執行 [ALTER INDEX REORGANIZE](../../t-sql/statements/alter-index-transact-sql.md)，將它們合併到背景中。

### <a name="keep-the-sorted-order-of-a-b-tree-index"></a>保留 B 型樹狀結構索引的排序順序
由於 B 型樹狀結構索引已經按照排序順序儲存資料列，因此，將資料列壓縮成資料行存放區索引時，保留這個順序可以提升查詢效能。

資料行存放區索引不會排序資料，但會使用中繼資料來追蹤每個資料列群組中每個資料行區段的最小和最大值。  掃描一個範圍的值時，它可以快速計算出何時要略過資料列群組。 當資料經過排序時，可以略過多個資料列群組。 

若要在轉換期間保留排序的順序：
* 使用 [CREATE COLUMNSTORE INDEX](../../t-sql/statements/create-columnstore-index-transact-sql.md) 加上 DROP_EXISTING 子句。 這也會保留索引的名稱。 如果有些指令碼已經使用資料列存放區索引的名稱，您不需要加以更新。 

    這個範例會將名為 `MyFactTable` 的資料表上的叢集資料列存放區索引，轉換成叢集資料行存放區索引。 `ClusteredIndex_d473567f7ea04d7aafcac5364c241e09` 索引名稱維持不變。

    ```sql
    CREATE CLUSTERED COLUMNSTORE INDEX ClusteredIndex_d473567f7ea04d7aafcac5364c241e09  
    ON MyFactTable  
    WITH (DROP_EXISTING = ON);  
    ```

## <a name="related-tasks"></a>相關工作  
下列是建立和維護資料行存放區索引的工作。 
  
|Task|參考主題|注意|  
|----------|----------------------|-----------|  
|建立資料表作為資料行存放區。|[CREATE TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/create-table-transact-sql.md)|從 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)]開始，您可以建立資料表作為叢集資料行存放區索引。 您不需要先建立資料列存放區資料表，再將它轉換成資料行存放區。|  
|建立具有資料行存放區索引的記憶體資料表。|[CREATE TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/create-table-transact-sql.md)|從 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)]開始，您可以建立具有資料行存放區索引的記憶體最佳化資料表。 建立資料表之後，也可以使用 ALTER TABLE ADD INDEX 語法來加入資料行存放區索引。|  
|將資料列存放區資料表轉換成資料行存放區。|[CREATE COLUMNSTORE INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/create-columnstore-index-transact-sql.md)|將現有的堆積或二進位樹狀目錄轉換成資料行存放區。 範例示範如何在執行這項轉換時處理現有的索引及索引名稱。|  
|將資料行存放區資料表轉換成資料列存放區。|[建立叢集索引X &#40;Transact-SQL&#41;](../../t-sql/statements/create-columnstore-index-transact-sql.md#d-convert-a-columnstore-table-to-a-rowstore-table-with-a-clustered-index) 或[將資料行存放區資料表轉換回資料列存放區堆積](../../t-sql/statements/create-columnstore-index-transact-sql.md#e-convert-a-columnstore-table-back-to-a-rowstore-heap) |此轉換通常並非必要，但有時您仍舊需要轉換。 範例示範如何將資料行存放區轉換成堆積或叢集索引。|   
|在資料列存放區資料表上建立資料行存放區索引。|[CREATE COLUMNSTORE INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/create-columnstore-index-transact-sql.md)|資料列存放區資料表可以有一個資料行存放區索引。  從 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)]開始，資料行存放區索引可以有一個篩選條件。 範例示範基本語法。|  
|為作業分析建立高效能的索引。|[開始使用資料行存放區進行即時作業分析](../../relational-databases/indexes/get-started-with-columnstore-for-real-time-operational-analytics.md)|描述如何建立互補資料行存放區和 B 型樹狀結構索引，讓 OLTP 查詢使用 B 型樹狀結構索引，而分析查詢使用資料行存放區索引。|  
|為資料倉儲建立高效能的資料行存放區索引。|[資料行存放區索引 - 資料倉儲](../../relational-databases/indexes/columnstore-indexes-data-warehouse.md)|描述如何在資料行存放區資料表上使用 B 型樹狀結構索引，建立高效能的資料倉儲查詢。|  
|使用 B 型樹狀結構索引，在資料行存放區索引上強制執行主索引鍵條件約束。|[資料行存放區索引 - 資料倉儲](../../relational-databases/indexes/columnstore-indexes-data-warehouse.md)|示範如何合併 B 型樹狀結構和資料行存放區索引，在資料行存放區索引上強制執行主索引鍵條件約束。|  
|卸除資料行存放區索引|[DROP INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/drop-index-transact-sql.md)|卸除資料行存放區索引，其使用 B 型樹狀結構索引所使用的標準 DROP INDEX 語法。 若卸除叢集資料行存放區索引，會將資料行存放區資料表轉換成堆積。|  
|從資料行存放區索引刪除資料列|[DELETE &#40;Transact-SQL&#41;](../../t-sql/statements/delete-transact-sql.md)|使用 [DELETE &#40;Transact-SQL&#41;](../../t-sql/statements/delete-transact-sql.md) 刪除資料列。<br /><br /> **資料行存放區** 資料列： [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會將該資料列標示為邏輯刪除，但在重建索引之前，不會回收該資料列的實體儲存體。<br /><br /> **差異存放區** 資料列： [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會以邏輯方式實際刪除該資料列。|  
|更新資料行存放區索引中的資料列|[UPDATE &#40;Transact-SQL&#41;](../../t-sql/queries/update-transact-sql.md)|使用 [UPDATE &#40;Transact-SQL&#41;](../../t-sql/queries/update-transact-sql.md) 更新資料列。<br /><br /> **資料行存放區** 資料列：  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會將該資料列標示為邏輯刪除，然後將更新的資料列插入差異存放區中。<br /><br /> **差異存放區** 資料列： [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會更新差異存放區中的該資料列。|  
|強制將差異存放區中的所有資料列移入資料行存放區。|[ALTER INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/alter-index-transact-sql.md) ...REBUILD<br /><br /> [重新組織與重建索引](../../relational-databases/indexes/reorganize-and-rebuild-indexes.md)|ALTER INDEX 搭配 REBUILD 選項會強制將所有資料列移入資料行存放區。|  
|重組資料行存放區索引|[ALTER INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/alter-index-transact-sql.md)|ALTER INDEX ...REORGANIZE 會線上重組資料行存放區索引。|  
|合併具有資料行存放區索引的資料表。|[MERGE &#40;Transact-SQL&#41;](../../t-sql/statements/merge-transact-sql.md)|

## <a name="next-steps"></a>接下來的步驟
若要針對下列項目，建立空的資料行存放區索引：

* [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 或 [!INCLUDE[ssSDS](../../includes/sssds-md.md)]，請參閱 [CREATE TABLE (Transact-SQL)](../../t-sql/statements/create-table-transact-sql.md)。
* [!INCLUDE[ssSDW](../../includes/sssdw-md.md)]，請參閱 [CREATE TABLE (Azure Synapse Analytics)](../../t-sql/statements/create-table-as-select-azure-sql-data-warehouse.md)。

如需如何將現有的資料列存放區堆積或 B 型樹狀結構索引轉換成叢集資料行存放區索引，或建立非叢集資料行存放區索引的詳細資訊，請參閱 [CREATE COLUMNSTORE INDEX (Transact-SQL)](../../t-sql/statements/create-columnstore-index-transact-sql.md)。