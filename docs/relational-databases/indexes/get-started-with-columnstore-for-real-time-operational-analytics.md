---
description: 開始使用資料行存放區進行即時作業分析
title: 開始使用資料行存放區進行即時作業分析 | Microsoft Docs
ms.custom: ''
ms.date: 03/08/2016
ms.prod: sql
ms.prod_service: table-view-index, sql-database
ms.reviewer: ''
ms.technology: table-view-index
ms.topic: quickstart
ms.assetid: e1328615-6b59-4473-8a8d-4f360f73187d
author: MikeRayMSFT
ms.author: mikeray
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 4e62692402f33bfc42957363794bad9a698b18bb
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98172760"
---
# <a name="get-started-with-columnstore-for-real-time-operational-analytics"></a>開始使用資料行存放區進行即時作業分析
[!INCLUDE [SQL Server Azure SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 引進即時作業分析，能夠同時在同一個資料庫資料表上執行分析和 OLTP 工作負載。 除了執行即時分析，您也可免除 ETL 和資料倉儲的需要。  
  
## <a name="real-time-operational-analytics-explained"></a>說明即時作業分析  
 傳統上，企業會有個別可用於作業 (也就是 OLTP) 和分析工作負載的系統。 在這類系統中，擷取、轉換和載入 (ETL) 工作會定期將資料從作業存放區移至分析存放區。 分析資料通常儲存於專門用來執行分析查詢的資料倉儲或資料超市中。 儘管此解決方案已是標準，但它仍有下列這三個主要挑戰︰  
  
-   **複雜度。** 實作 ETL 需要相當多的編碼，特別是只載入修改的資料列。 要找出哪些資料列已修改是相當複雜的。  
  
-   **成本。** 實作 ETL 需要購買額外硬體和軟體授權的成本。  
  
-   **資料延遲。** 實作 ETL 會新增用以執行分析的時間延遲。 例如，如果 ETL 工作是在每個營業日結束時執行，分析查詢將針對至少是前一天的資料執行。 對許多企業來說，這個延遲是無法接受的，因為企業倚賴即時分析資料。 例如，詐騙偵測需要針對作業資料進行即時分析。  
  
 ![即時作業分析概觀](../../relational-databases/indexes/media/real-time-operational-analytics-overview.png "即時作業分析概觀")  
  
即時作業分析提供這些挑戰的解決方案。   
當分析和 OLTP 工作負載在相同的基礎資料表上執行時，不會有任何時間延遲。   針對可使用即時分析的案例，可藉由免除 ETL 的需要以及購買和維護個別資料倉儲的需要，大幅降低成本和複雜度。  
  
> [!NOTE]  
>  即時作業分析的目標是單一資料來源的案例，例如企業資源規劃 (ERP) 應用程式，您可以在其上執行作業和分析工作負載。 當您需要在執行分析工作負載之前整合多個來源的資料時，或當您需要使用預先彙總的資料 (例如 Cube) 進行極端分析效能時，這不會取代個別資料倉儲的需要。  
  
 即時分析會在資料列存放區資料表上使用可更新的資料行存放區索引。 資料行存放區索引會維護資料複本，因此 OLTP 和分析工作負載會針對資料的個別複本執行。 這樣可將同時執行這兩個工作負載的效能影響降到最低。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會自動維護索引變更，讓 OLTP 變更一律保持最新狀態以供分析。 透過此設計，就能實際針對最新的資料執行即時分析。 這適用於以磁碟為基礎的資料表和記憶體最佳化的資料表。  
  
## <a name="get-started-example"></a>開始使用範例  
 開始進行即時分析︰  
  
1.  識別作業結構描述中的資料表，其中包含分析所需的資料。  
  
2.  針對每個資料表，卸除所有 btree 索引，其主要是設計來在您的 OLTP 工作負載上加速現有的分析。 使用單一資料行存放區索引來取代它們。  這可提升 OLTP 工作負載的整體效能，因為要維護的索引較少。  
  
    ```sql  
    --This example creates a nonclustered columnstore index on an existing OLTP table.  
    --Create the table  
    CREATE TABLE t_account (  
        accountkey int PRIMARY KEY,  
        accountdescription nvarchar (50),  
        accounttype nvarchar(50),  
        unitsold int  
    );  
  
    --Create the columnstore index with a filtered condition  
    CREATE NONCLUSTERED COLUMNSTORE INDEX account_NCCI   
    ON t_account (accountkey, accountdescription, unitsold)   
    ;  
    ```  
  
     記憶體內部資料表上的資料行存放區索引可整合記憶體內部 OLTP 和記憶體內部資料行存放區技術，以傳遞 OLTP 和分析工作負載的高效能，藉以允許作業分析。 記憶體內部資料表上的資料行存放區索引必須包含所有資料行。  
  
    ```sql  
    -- This example creates a memory-optimized table with a columnstore index.  
    CREATE TABLE t_account (  
        accountkey int NOT NULL PRIMARY KEY NONCLUSTERED,  
        Accountdescription nvarchar (50),  
        accounttype nvarchar(50),  
        unitsold int,  
        INDEX t_account_cci CLUSTERED COLUMNSTORE  
        )  
        WITH (MEMORY_OPTIMIZED = ON );  
    GO  
  
    ```  
  
3.  您只需要執行這個動作！  

 您現在已準備好執行即時作業分析，而不需對應用程式進行任何變更。  分析查詢將針對資料行存放區索引執行，而 OLTP 作業將針對 OLTP btree 索引繼續執行。 OLTP 工作負載將繼續執行，但會產生一些額外的負荷來維護資料行存放區索引。 請參閱下一節中的效能最佳化。  
  
## <a name="blog-posts"></a>部落格文章  
 若要深入了解即時作業分析，請參閱下列部落格文章。 如果您先看過部落格文章，可能更容易了解效能秘訣章節。  
  
-   [即時作業分析的商業案例](https://blogs.technet.microsoft.com/dataplatforminsider/2015/12/09/real-time-operational-analytics-using-in-memory-technology/)  
  
-   [使用非叢集資料行存放區索引進行即時作業分析](/archive/blogs/sqlserverstorageengine/real-time-operational-analytics-using-nonclustered-columnstore-index)  
  
-   [使用非叢集資料行存放區索引的簡單範例](/archive/blogs/sqlserverstorageengine/real-time-operational-analytics-simple-example-using-nonclustered-clustered-columnstore-index-ncci)  
  
-   [SQL Server 如何在交易式工作負載上維護非叢集資料行存放區索引](/archive/blogs/sqlserverstorageengine/real-time-operational-analytics-dml-operations-and-nonclustered-columnstore-index-ncci-in-sql-server-2016)  
  
-   [使用篩選的索引，將非叢集資料行存放區索引維護的影響降到最低](/archive/blogs/sqlserverstorageengine/real-time-operational-analytics-filtered-nonclustered-columnstore-index-ncci)  
  
-   [使用壓縮延遲，將非叢集資料行存放區索引維護的影響降到最低](/archive/blogs/sqlserverstorageengine/real-time-operational-analytics-compression-delay-option-for-nonclustered-columnstore-index-ncci)  
  
-   [使用壓縮延遲 - 效能數目，將非叢集資料行存放區索引維護的影響降到最低](/archive/blogs/sqlserverstorageengine/real-time-operational-analytics-compression-delay-option-with-ncci-and-the-performance)  
  
-   [使用記憶體最佳化資料表的即時作業分析](/archive/blogs/sqlserverstorageengine/real-time-operational-analytics-memory-optimized-table-and-columnstore-index) \(英文\)  
  
-   [資料行存放區索引和適用於資料列群組的合併原則](/archive/blogs/sqlserverstorageengine/columnstore-index-merge-policy-for-reorganize)  
  
## <a name="performance-tip-1-use-filtered-indexes-to-improve-query-performance"></a>效能提示 #1：使用經篩選索引來改善查詢效能  
 執行即時作業分析會影響 OLTP 工作負載的效能。 這種影響應該很小。 下列範例示範如何使用篩選的索引，將交易式工作負載上的非叢集資料行存放區索引影響降到最低，同時仍能提供即時分析。  
  
 若要將維護作業工作負載上非叢集資料行存放區索引的額外負荷降到最低，您可以使用篩選的條件，僅在「暖」  或緩時變的資料上建立非叢集資料行存放區索引。 例如，在訂單管理應用程式中，您可以在已經出貨的訂單上建立非叢集資料行存放區索引。 一旦訂單已出貨之後，就很少變更，因此可視為暖資料。 透過篩選的索引，非叢集資料行存放區索引中的資料所需的更新很少，因而可降低對交易式工作負載的影響。  
  
 分析查詢可視需要明確地存取暖和熱資料，來提供即時分析。 如果作業工作負載的某個重要部分會接觸到「熱」資料，則這些作業就不需要對資料行存放區索引進行額外的維護。 最佳做法是，在篩選的索引定義中使用的資料行上具備資料列存放區叢集索引。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會使用叢集索引，以快速掃描不符合篩選條件的資料列。 如果沒有這個叢集索引，就必須對資料列存放區資料表進行完整的資料表掃描，以尋找會對分析查詢效能造成顯著負面影響的資料列。 如果沒有叢集索引，您可以建立互補且篩選的非叢集 btree 索引來識別這類資料列，但不建議使用，因為透過非叢集的 btree 索引存取大範圍資料列的成本很高。  
  
> [!NOTE]  
>  只有以磁碟為基礎的資料表上才支援篩選的非叢集資料行存放區索引。 記憶體最佳化的資料表上並不支援。  
  
### <a name="example-a-access-hot-data-from-btree-index-warm-data-from-columnstore-index"></a>範例 A：從 btree 索引存取熱資料，從資料行存放區索引存取暖資料  
 此範例會使用篩選的條件 (accountkey > 0)，來建立將存在於資料行存放區索引中的資料列。 目標是設計篩選的條件和後續查詢，以便從 btree 索引存取經常變更的「熱」資料，以及從資料行存放區索引存取更穩定的「暖」資料。  
  
 ![合併暖資料及熱資料的索引](../../relational-databases/indexes/media/de-columnstore-warmhotdata.png "合併暖資料及熱資料的索引")  
  
> [!NOTE]  
>  查詢最佳化工具會考慮，但不一定會選擇對查詢計劃使用資料行存放區索引。 當查詢最佳化工具選擇篩選的資料行存放區索引時，其會明確地結合來自資料行存放區索引的資料列以及不符合篩選條件的資料列，以允許即時分析。 這不同於一般非叢集的篩選索引，這類索引只能用於將它們自己限制為出現在索引中之資料列的查詢。  
  
```sql  
--Use a filtered condition to separate hot data in a rowstore table  
-- from "warm" data in a columnstore index.  
  
-- create the table  
CREATE TABLE  orders (  
         AccountKey         int not null,  
         Customername       nvarchar (50),  
        OrderNumber         bigint,  
        PurchasePrice       decimal (9,2),  
        OrderStatus         smallint not null,  
        OrderStatusDesc     nvarchar (50))  
  
-- OrderStatusDesc  
-- 0 => 'Order Started'  
-- 1 => 'Order Closed'  
-- 2 => 'Order Paid'  
-- 3 => 'Order Fullfillment Wait'  
-- 4 => 'Order Shipped'  
-- 5 => 'Order Received'  
  
CREATE CLUSTERED INDEX  orders_ci ON orders(OrderStatus)  
  
--Create the columnstore index with a filtered condition  
CREATE NONCLUSTERED COLUMNSTORE INDEX orders_ncci ON orders  (accountkey, customername, purchaseprice, orderstatus)  
where orderstatus = 5  
;  
  
-- The following query returns the total purchase done by customers for items > $100 .00  
-- This query will pick  rows both from NCCI and from 'hot' rows that are not part of NCCI  
SELECT top 5 customername, sum (PurchasePrice)  
FROM orders  
WHERE purchaseprice > 100.0   
Group By customername  
```  
  
 分析查詢將使用下列查詢計劃來執行。 您可以查看透過叢集的 btree 索引，存取不符合篩選條件的資料列。  
  
 ![查詢計劃](../../relational-databases/indexes/media/query-plan-columnstore.png "查詢計劃")  
  
 如需 [篩選的非叢集資料行存放區索引](/archive/blogs/sqlserverstorageengine/real-time-operational-analytics-filtered-nonclustered-columnstore-index-ncci)的詳細資訊，請參閱此部落格。  
  
## <a name="performance-tip-2-offload-analytics-to-always-on-readable-secondary"></a>效能提示 #2：將分析卸載至 Always On 可讀取次要  
 雖然可使用篩選的資料行存放區索引來將資料行存放區索引維護最小化，但分析查詢仍然需要大量運算資源 (CPU、I/O、記憶體)，其會影響作業工作負載效能。 針對大部分任務關鍵性工作負載，建議您使用 AlwaysOn 組態。 在此組態中，您可以免除將分析卸載至可讀取次要以執行分析的影響。  
  
## <a name="performance-tip-3-reducing-index-fragmentation-by-keeping-hot-data-in-delta-rowgroups"></a>效能提示 #3：讓熱資料保留在差異資料列群組中來減少索引片段  
 如果工作負載會更新/刪除已壓縮的資料列，具有資料行存放區索引的資料表可能會產生大量的片段 (也就是刪除的資料列)。 片段的資料行存放區索引會導致記憶體/儲存體使用率的效率不佳。 除了資源使用效率不彰，也會因為額外的 I/O 以及從結果集中篩選出已刪除資料列的需要，而使分析查詢效能受到負面影響。  
  
 在使用 `REORGANIZE` 命令執行索引重組，或在整個資料表或受影響的分割區上重建資料行存放區索引之前，不會實際移除刪除的資料列。 索引 `REORGANIZE` 和 `REBUILD` 都是成本很高的作業，其會佔用本該用於工作負載的資源。 此外，如果資料列過早壓縮，就可能需要由於更新而多次重新壓縮，因而導致浪費了壓縮的額外負荷。  
您可使用 `COMPRESSION_DELAY` 選項將索引片段最小化。  
  
```sql  
-- Create a sample table  
CREATE TABLE t_colstor (  
               accountkey                      int not null,  
               accountdescription              nvarchar (50) not null,  
               accounttype                     nvarchar(50),  
               accountCodeAlternatekey         int)  
  
-- Creating nonclustered columnstore index with COMPRESSION_DELAY. The columnstore index will keep the rows in closed delta rowgroup for 100 minutes   
-- after it has been marked closed  
CREATE NONCLUSTERED COLUMNSTORE index t_colstor_cci on t_colstor (accountkey, accountdescription, accounttype)   
                       WITH (DATA_COMPRESSION= COLUMNSTORE, COMPRESSION_DELAY = 100);  
  
;  
```  
  
 如需[壓縮延遲](/archive/blogs/sqlserverstorageengine/real-time-operational-analytics-compression-delay-option-for-nonclustered-columnstore-index-ncci)的詳細資訊，請參閱此部落格。  
  
 以下是建議的最佳做法：  
  
-   **插入/查詢工作負載：** 如果工作負載主要是插入資料並加以查詢，則建議選項是預設為 0 的 COMPRESSION_DELAY。 將 1 百萬個資料列插入單一差異資料列群組之後，即會壓縮最新插入的資料列。  
    這類工作負載的部分範例是，(a) 傳統 DW 工作負載 (b) 當您需要在 Web 應用程式中分析按一下模式時所進行的按一下資料流分析。  
  
-   **OLTP 工作負載：** 如果工作負載是大量的 DML (也就是大量更新、刪除和插入的混合)，您可以透過檢查 DMV sys 來查看資料行存放區索引片段。 dm_db_column_store_row_group_physical_stats。 如果您看到最近壓縮的資料列群組中有 > 10% 的資料列標示為已刪除，您可以使用 COMPRESSION_DELAY 選項，在資料列變成能夠壓縮時，新增時間延遲。 例如，針對您的工作負載，如果最新插入的資料列假設在 60 分鐘內會保持「熱」(也就是進行多次更新)，您應該選擇讓 COMPRESSION_DELAY 為 60。  
  
 我們預期大多數客戶不需要執行任何動作。 他們應該適用 COMPRESSION_DELAY 選項的預設值。  
對於進階使用者，我們建議執行下列查詢，並收集過去 7 天已刪除資料列的 %。  
  
```sql  
SELECT row_group_id,cast(deleted_rows as float)/cast(total_rows as float)*100 as [% fragmented], created_time  
FROM sys. dm_db_column_store_row_group_physical_stats  
WHERE object_id = object_id('FactOnlineSales2')   
             AND  state_desc='COMPRESSED'   
             AND deleted_rows>0   
             AND created_time > GETDATE() - 7  
ORDER BY created_time DESC;  
```  
  
 如果壓縮資料列群組中已刪除的資料列數 > 20%，在變化 < 5% 的較舊資料列群組中達到平穩階段 (稱為冷資料列群組) 時，設定 COMPRESSION_DELAY = (youngest_rowgroup_created_time -  current_time)。 請注意，這種方法最適合搭配穩定且相對同質的工作負載來使用。  
  
## <a name="see-also"></a>另請參閱  
 [資料行存放區索引指南](../../relational-databases/indexes/columnstore-indexes-overview.md)   
 [資料行存放區索引資料載入](../../relational-databases/indexes/columnstore-indexes-data-loading-guidance.md)   
 [資料行存放區索引效能](../../relational-databases/indexes/columnstore-indexes-query-performance.md)   
 [資料倉儲的資料行存放區索引](../../relational-databases/indexes/columnstore-indexes-data-warehouse.md)   
 [重新組織與重建索引](../../relational-databases/indexes/reorganize-and-rebuild-indexes.md)
