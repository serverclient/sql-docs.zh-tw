---
description: 分頁與範圍架構指南
title: 分頁與範圍架構指南 | Microsoft Docs
ms.custom: ''
ms.date: 03/12/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: ''
ms.topic: conceptual
helpviewer_keywords:
- page and extent architecture guide
- guide, page and extent architecture
ms.assetid: 83a4aa90-1c10-4de6-956b-7c3cd464c2d2
author: rothja
ms.author: jroth
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 22e1a4832e3ef02d2b596ecd0dd4af3a08a7ec6e
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98171870"
---
# <a name="pages-and-extents-architecture-guide"></a>分頁與範圍架構指南
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

頁面是 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 中資料儲存的基本單位。 範圍是八個實體連續頁面的集合。 範圍可以協助系統有效地管理頁面。 本指南說明所有 SQL Server 版本中用於管理分頁與範圍的資料結構。 了解頁面與範圍的架構對於設計和開發有效執行的資料庫很重要。

## <a name="pages-and-extents"></a>分頁與範圍

[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 中儲存資料的基本單位是分頁。 資料庫中配置給資料檔 (.mdf 或 .ndf) 的磁碟空間會邏輯區分為從 0 連續編號到 n 的分頁。 磁碟 I/O 作業在分頁層次上操作。 也就是說，SQL Server 會讀取或寫入整個資料分頁。

範圍是八個實體連續分頁的集合，用來有效地管理這些分頁。 所有頁面都會組織成範圍。

### <a name="pages"></a>頁面

定期記錄：其所有內容都會撰寫在頁面中。 類似書籍，SQL Server 中的所有資料列都會撰寫在頁面上。 在書籍中，所有頁面的大小都相同。 同樣地，SQL Server 所有資料頁的大小也都相同 - 8 KB。 在一本書中，大部分的頁面包含資料 (該書的主要內容)，有些頁面則包含內容的相關中繼資料 (例如目錄和索引)。 SQL Server 也一樣：大部分頁面包含使用者儲存的實際資料列，其稱為資料頁和文字/影像頁 (適用於特殊情況)。 索引頁包含資料所在位置的索引參考，且最後還有系統頁，其用來儲存有關資料組織 (PFS、GAM、SGAM、IAM、DCM、BCM 頁面) 的各種中繼資料。 請參閱下表以取得頁面類型及其描述。

如前文所述，在 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 中，頁面大小為 8-KB。 這意味著 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 資料庫是每 MB 有 128 個分頁。 每個分頁的開頭為 96 個位元組的標頭，用來儲存與分頁有關的系統資訊。 此資訊包括頁碼、分頁類型、分頁上可用空間的數量，以及擁有分頁的物件配置單位識別碼。

下表顯示 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 資料庫資料檔案中使用的分頁類型。

|分頁類型 | 內容 |
|-------|-------|
|資料 |當 text in row 設為 ON 時，具有所有資料的資料列 (text, ntext、image、nvarchar(max)、varchar(max)、varbinary(max) 與 xml 資料除外)。 |
|索引 |索引項目 |
|文字/影像 |大型物件資料類型：(text、ntext、image、nvarchar(max)、varchar(max)、varbinary(max) 與 xml 資料) <br> 資料列超過 8 KB 時可變長度資料行：(varchar、nvarchar、varbinary 與 sql_variant) |
|整體配置對應、共用整體配置對應 |有關是否配置範圍的資訊。 |
|分頁可用空間 (PFS) |有關分頁配置和分頁中可用空間的資訊。 |
|索引配置對應 |有關每個配置單位資料表或索引所用範圍的資訊。 |
|大量變更對應 |有關每個配置單位從上一次 BACKUP LOG 陳述式後被大量作業修改之範圍的資訊。 |
|差異式變更對應 |有關每個配置單位追蹤自從上個 BACKUP DATABASE 陳述式之後發生變更的範圍的資訊。 |

> [!NOTE]
> 記錄檔並沒有包含分頁，它們包含的是一系列的記錄檔資料錄。

資料列將循序地放置於緊接在前置資料後的分頁中。 資料列位移資料表從頁面結尾開始，而且每個資料表分別為分頁上每一列包含一個項目。 每個資料列位移項目，都記錄資料列第一個位元組距離分頁開頭多遠。 因此，資料列位移資料表的功能是協助 SQL Server 以非常快速的速度在分頁上找出資料列。 資料列位移資料表的項目順序與分頁中的資料列順序是相反的。

![page_architecture](../relational-databases/media/page-architecture.gif)

#### <a name="large-row-support"></a>大型資料列支援  

資料列無法跨越分頁，不過資料列的部份可能已經移出資料列的分頁，所以資料列實際可能很大。 最大資料總量與分頁上單一資料列中包含的負擔為 8,060 個位元組 (8 KB)。 但是，這不包括 Text/Image 分頁類型中儲存的資料。 

此限制對於包含 varchar、nvarchar、varbinary 或 sql_variant 資料行的資料表將會較為寬鬆。 當資料表中所有固定且可變資料行的資料列總大小超過 8,060 個位元組的限制時，[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 會動態地將一個或多個可變長度資料行移至 ROW_OVERFLOW_DATA 配置單位中的分頁，從寬度最大的資料行開始。 

只要插入或更新作業使得資料列的總大小超過 8,060 個位元組的限制時，就會執行這個動作。 當資料行移至 ROW_OVERFLOW_DATA 配置單位中的分頁時，會保留 IN_ROW_DATA 配置單位中原始頁面上的 24 個位元組指標。 若是後續作業縮小了資料列大小，[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 則會動態地將資料行移回原始資料頁。 

##### <a name="row-overflow-considerations"></a>資料列溢位考量 

如先前所述，資料列不能位於多個分頁上，而且如果可變長度資料類型欄位的合併大小超過 8060 位元組的限制，可能會溢位。 為了說明，您可以使用兩個數據行來建立資料表：一個 Varchar(7000) 和另一個 Varchar (2000)。 資料行各自都不會超過 8060 個位元組，但是如果每個資料行的整個寬度都已填滿，則結合它們就會超過限制。 SQL Server 可以動態地將 Varchar(7000) 可變長度資料行移至 ROW_OVERFLOW_DATA 配置單位中的分頁。 當您將 varchar、nvarchar、varbinary、sql_variant 或 CLR 使用者定義型別資料行結合在一起，因而超過每個資料列 8,060 個位元組的限制時，請考慮下列情況：
-  當記錄因更新作業而加長時，這些大型記錄會動態地移到另一個分頁上。 當更新作業將記錄縮短時，則會造成這些記錄移回 IN_ROW_DATA 配置單位中的原始分頁。 查詢與執行其他選取作業 (例如在包含資料列溢位資料的大型記錄上執行排序或聯結) 時，處理速度將會變慢，這是因為這些記錄會以同步而不是非同步的方式來處理。   
   因此，當您設計具有多個 varchar、nvarchar、varbinary、sql_variant 或 CLR 使用者定義型別資料行的資料表時，請考慮可能會溢位的資料列百分比，以及可能會查詢此溢位資料的頻率。 如果可能會經常在許多資料列溢位資料的資料列上執行查詢，請考慮將資料表正規化，以便將一些資料行移到另一個資料表。 然後便可以用非同步 JOIN 作業進行查詢。 
-  對於 varchar、nvarchar、varbinary、sql_variant 和 CLR 使用者定義型別資料行而言，個別資料行長度仍必須符合 8,000 個位元組的限制。 只有它們合起來的長度才可以超過資料表的每個資料列 8,060 個位元組的限制。
-  其他資料類型 (包括 char 和 nchar 資料) 資料行的總和，必須符合 8,060 個位元組的資料列限制。 大型物件資料也可免除 8,060 個位元組的資料列限制。 
-  叢集索引之索引鍵所包含的 varchar 資料行不能在 ROW_OVERFLOW_DATA 配置單位中有現有資料。 如果在 varchar 資料行上建立叢集索引，且現有的資料在 IN_ROW_DATA 配置單位中，則後續在資料行上可能將資料推送到資料列外的插入或更新動作會失敗。 如需有關配置單位的詳細資訊，請參閱＜資料表與索引組織＞。
-  您可以納入包含資料列溢位資料的資料行，做為非叢集索引的索引鍵或非索引鍵資料行。
-  使用疏鬆資料行之資料表的記錄大小限制為 8,018 個位元組。 當轉換的資料加上現有記錄資料超過 8,018 個位元組時，就會傳回 [MSSQLSERVER ERROR 576](../relational-databases/errors-events/database-engine-events-and-errors.md)。 在疏鬆與非疏鬆類型之間轉換資料行時，資料庫引擎就會保留一份目前記錄資料的複本。 這樣做會暫時將記錄所需的儲存體加倍。
-  若要取得可能包含資料列溢位資料之資料表或索引的相關資訊，請使用 [sys.dm_db_index_physical_stats](../relational-databases/system-dynamic-management-views/sys-dm-db-index-physical-stats-transact-sql.md) 動態管理函式。

### <a name="extents"></a>Extents 

範圍即管理空間中的基本單位。 一個範圍是 8 個連續實體分頁，也就是 64 KB。 這意味著 SQL Server 資料庫每 MB 有 16 個範圍。

[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 有兩種範圍類型︰ 

* **制式** 的範圍將由一個物件所擁有；範圍中的所有八個頁面只能被擁有的物件所使用。
* **混合** 的範圍最多可被八個物件所共用。 範圍中的 8 個分頁都可以由不同的物件所擁有。

![統一與混合範圍](../relational-databases/media/extents.gif)

最高到 (並包含) [!INCLUDE[ssSQL14](../includes/sssql14-md.md)]，[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 並不會將整個範圍配置給包含少量資料的資料表。 新的資料表或索引，一般會從混合的範圍中配置分頁。 當資料表或索引成長至擁有八個分頁之後，接著會為後續配置切換成使用制式的範圍。 如果現有資料表有足夠的資料列可在索引中產生八個分頁，若對該資料表建立索引，索引的所有配置都將採用制式的範圍。 

從 [!INCLUDE[ssSQL15](../includes/sssql16-md.md)] 開始，使用者資料庫與 tempdb 中大部分配置的預設值都會使用統一範圍，但屬於 [IAM 鏈結](#IAM)前八個分頁的配置除外。 針對 master、msdb 與 model 資料庫的配置仍會保留先前的行為。 

> [!NOTE]
> 最高到 (並包含) [!INCLUDE[ssSQL14](../includes/sssql14-md.md)]，追蹤旗標 1118 可以用來將預設配置變更為一律使用統一範圍。 如需此追蹤旗標的詳細資訊，請參閱 [DBCC TRACEON - 追蹤旗標](../t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql.md)。   
>   
> 從 [!INCLUDE[ssSQL15](../includes/sssql16-md.md)] 開始，會針對 tempdb 與所有使用者資料庫自動啟用 TF 1118 所提供的功能。 對於使用者資料庫，此行為由 `ALTER DATABASE` 的 `SET MIXED_PAGE_ALLOCATION` 選項 (預設值設定為 OFF) 所控制，而且追蹤旗標 1118 沒有作用。 如需詳細資訊，請參閱 [ALTER DATABASE SET 選項 (Transact-SQL)](../t-sql/statements/alter-database-transact-sql-set-options.md)。

從 [!INCLUDE[ssSQL11](../includes/sssql11-md.md)] 開始，`sys.dm_db_database_page_allocations` 系統函數可以報告資料庫、資料表、索引與資料分割的分頁配置資訊。

> [!IMPORTANT]
> `sys.dm_db_database_page_allocations` 系統函數並未記載，而且可能會變更。 我們無法確保其相容性。 

從 [!INCLUDE[sql-server-2019](../includes/sssqlv15-md.md)] 開始，提供 [sys.dm_db_page_info](../relational-databases/system-dynamic-management-views/sys-dm-db-page-info-transact-sql.md) 系統函數，此系統函數可傳回資料庫中分頁的相關資訊。 此函數會傳回一個包含分頁中標頭資訊的資料列，包含 object_id、index_id 與 partition_id。 在大部分情況下，有此函式就不需要使用 `DBCC PAGE`。

## <a name="managing-extent-allocations-and-free-space"></a>管理範圍配置與可用空間 

管理範圍配置與追蹤可用空間的 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 資料結構相當簡單。 具有下列好處： 

* 可用空間的資訊是緊密壓縮的，因此只有少數的分頁包含此資訊。   
  如此可降低擷取配置資訊時所需的磁碟讀取量，進而提升速度。 此外，還可以增加配置頁保存於記憶體中的機會，而不需額外的讀取。 

* 大部分的配置資訊不會鏈結在一起。 這可以簡化配置資訊的維護工作。    
  每個分頁配置或取消配置都可迅速執行。 這可減少配置或釋出分頁所需之並行工作間的競爭。 

### <a name="managing-extent-allocations"></a>管理範圍配置

[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 使用兩種配置對應來記錄範圍的配置： 

- **整體配置對應 (GAM)**    
  GAM 分頁可記錄已配置哪些範圍。 每個 GAM 可涵蓋 64,000 個範圍，或接近 4 GB 的資料。 GAM 以 1 個位元來表示所涵蓋期間的每個範圍。 若位元為 1，代表範圍可用；若位元為 0，代表範圍已配置。 

- **共用的整體配置對應 (SGAM)**    
  SGAM 分頁可記錄哪些範圍目前當作混合範圍使用，並至少擁有一個未使用的分頁。 每個 SGAM 可涵蓋 64,000 個範圍，或接近 4 GB 的資料。 SGAM 以 1 個位元來表示所涵蓋期間的每個範圍。 若位元為 1，則表示該範圍目前當作混合範圍使用，並具有可用分頁。 若位元為 0，則表示該範圍不是當作混合範圍使用，或者它是混合範圍，但所有分頁都在使用中。 

每個範圍都將根據其目前的用法，在 GAM 與 SGAM 中擁有下列的位元模式設定。 

|範圍的目前用法 | GAM 位元設定 | SGAM 位元設定 |
|---------|----------|------| 
|可用，且未使用 |1 |0 |
|制式範圍，或完全混合範圍 |0 |0 |
|具有可用分頁的混合範圍 |0 |1 |
 
如此可產生簡單的範圍管理演算法。 
-   若要配置制式範圍，[!INCLUDE[ssDEnoversion](../includes/ssdenoversion-md.md)] 會搜尋 GAM 的 1 位元並將其設為 0。 
-   若要尋找具有可用頁面的混合範圍，[!INCLUDE[ssDEnoversion](../includes/ssdenoversion-md.md)] 會搜尋 SGAM 的 1 位元。 
-   若要配置混合範圍，[!INCLUDE[ssDEnoversion](../includes/ssdenoversion-md.md)] 會搜尋 GAM 的 1 位元並將其設為 0，然後將 SGAM 中的對應位元也設為 1。 
-   若要取消配置範圍，[!INCLUDE[ssDEnoversion](../includes/ssdenoversion-md.md)] 會確定 GAM 位元已設為 1，且 SGAM 位元已設為 0。 由 [!INCLUDE[ssDEnoversion](../includes/ssdenoversion-md.md)] 在內部使用的演算法，實際上比本文中所描述的更為複雜，因為 [!INCLUDE[ssDEnoversion](../includes/ssdenoversion-md.md)] 會將資料平均散發到資料庫中。 即使如此，因為實際的演算法不需有管理範圍配置資訊的鏈結，所以可加以簡化。

### <a name="tracking-free-space"></a>追蹤可用空間

**頁面可用空間 (PFS)** 頁面會記錄每個頁面的配置狀態、個別頁面是否已配置，以及每個頁面的可用空間量。 PFS 會為每個分頁設定 1 個位元，以記錄該分頁是否已配置，若已配置，則會記錄分頁是否為空白、已使用 1 至 50%、已使用 51 至 80%、已使用 81 至 95%，還是已使用 96 至 100%。

當範圍配置給物件之後，[!INCLUDE[ssDEnoversion](../includes/ssdenoversion-md.md)] 會使用 PFS 分頁，來記錄範圍中哪些分頁是已配置或可用的。 當 [!INCLUDE[ssDEnoversion](../includes/ssdenoversion-md.md)] 必須配置新分頁時，就會使用此資訊。 分頁中的可用空間量只會針對堆積與 Text/Image 分頁而保留。 當 [!INCLUDE[ssDEnoversion](../includes/ssdenoversion-md.md)] 必須尋找具有可用空間的分頁來存放新插入的資料列時，就會用到它。 因為插入新資料列的點由索引鍵值所設定，所以使用索引時不需追蹤分頁可用空間。

它會針對追蹤的每個額外範圍，在資料檔案中新增 PFS、GAM 或 SGAM 分頁。 在第一個 PFS 分頁之後 8,088 個分頁，有新的 PFS 分頁，而後續的其他 PFS 分頁以 8,088 個分頁為間隔。 為了說明，分頁識別碼 1 是 PFS 分頁，分頁識別碼 8088 是 PFS 分頁，分頁識別碼 16176 是 PFS 分頁，以此類推。 在第一個 GAM 分頁之後 64,000 個範圍，有新的 GAM 分頁，它會追蹤其後的 64,000 個範圍；順序會以 64,000 個範圍為間隔繼續。 同樣地，在第一個 SGAM 分頁之後 64,000 個範圍，有新的 SGAM 分頁，而後續的其他 SGAM 分頁以 64,000 個範圍為間隔。 下圖顯示 [!INCLUDE[ssDEnoversion](../includes/ssdenoversion-md.md)] 用來配置及管理範圍的頁面順序。

![manage_extents](../relational-databases/media/manage-extents.gif)

## <a name="managing-space-used-by-objects"></a><a name="IAM"></a> 管理物件所使用的空間 

[索引配置對應 (IAM)] 頁面會對應配置單位所使用資料庫檔案 4 GB 部分中的範圍。 配置單位為下列三種類型中的一種：

- IN_ROW_DATA   
    保存堆積或索引的資料分割。

- LOB_DATA   
   保存大型物件 (LOB) 資料類型，例如 XML、VARBINARY(max) 與 VARCHAR(max)。

- ROW_OVERFLOW_DATA   
   保存儲存於 VARCHAR、NVARCHAR、VARBINARY 或 SQL_VARIANT 資料行中，超過 8,060 個位元組資料列大小限制的可變長度資料。 

堆積或索引的每個資料分割都會至少包含一個 IN_ROW_DATA 配置單位。 視堆積或索引結構描述而定，資料分割也可能會包含 LOB_DATA 或 ROW_OVERFLOW_DATA 配置單位。

IAM 頁面涵蓋檔案中的 4 GB 範圍，和 GAM 和 SGAM 頁面的涵蓋範圍相同。 如果配置單位包含一個以上檔案的範圍，或超過一個 4 GB 範圍的檔案，IAM 鏈結中就會連結多個 IAM 頁面。 因此，每個配置單位在它擁有範圍的每個檔案上都會至少擁有一個 IAM。 如果配置給配置單位的檔案範圍超過一個 IAM 頁面所能記錄的範圍，該檔案也會有不止一個 IAM。 

![iam_pages](../relational-databases/media/iam-pages.gif)

IAM 頁面是視需要配置給每個配置單位，而且在檔案中的位置是隨機的。 `sys.system_internals_allocation_units` 系統檢視指向配置單位的第一個 IAM 分頁。 該配置單位的所有 IAM 分頁都會連結成一條 IAM 鏈結。

> [!IMPORTANT]
> `sys.system_internals_allocation_units` 系統檢視僅用於內部，且往後可能會變更。 我們無法確保其相容性。 此檢視在 [!INCLUDE[ssSDSfull](../includes/sssdsfull-md.md)]中無法使用。 

![iam_chain](../relational-databases/media/iam-chain.gif)
 
在每個配置單位鏈結中連結的 IAM 頁面擁有一個標頭，指出 IAM 頁面所對應範圍之範疇中的開始範圍。 IAM 頁面也擁有一個大型點陣圖，它裡面的每個位元都代表一個範圍。 對應中的第一個位元代表了範疇中的第一個範圍，第二個位元代表了第二個範圍，其餘依此類推。 若位元為 0，表示它所代表的範圍並未配置給擁有該 IAM 的配置單位。 若位元為 1，表示它所代表的範圍已配置給擁有該 IAM 頁面的配置單位。

當 [!INCLUDE[ssDEnoversion](../includes/ssdenoversion-md.md)] 必須插入新的資料列，但目前的頁面中沒有可用空間時，就會使用 IAM 與 PFS 頁面來尋找可配置的頁面，或針對堆積或文字/影像頁面，尋找擁有足夠空間可以保存資料列的頁面。 [!INCLUDE[ssDEnoversion](../includes/ssdenoversion-md.md)] 將使用 IAM 頁面來尋找配置給該配置單位的範圍。 對於每個範圍而言，[!INCLUDE[ssDEnoversion](../includes/ssdenoversion-md.md)] 會搜尋 PFS 頁面，查看是否有可用的頁面。 每個 IAM 與 PFS 頁面都涵蓋許多資料頁，因此資料庫的 IAM 與 PFS 頁面個數很少。 這代表 IAM 與 PFS 頁面通常位於 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 緩衝集區的記憶體中，因此可以快速地被搜尋到。 對於索引而言，新資料列的插入點是由索引鍵設定；但需要新頁面時，就會發生上述程序。

[!INCLUDE[ssDEnoversion](../includes/ssdenoversion-md.md)] 只在它無法在現有的範圍中找到一個擁有足夠空間來保存插入之資料列的頁面時，才會配置新的範圍給配置單位。 

<a name="ProportionalFill"></a>[!INCLUDE[ssDEnoversion](../includes/ssdenoversion-md.md)] 將使用 **比例式填滿配置演算法**，從檔案群組中的可用範圍來配置範圍。 若在相同的檔案群組有兩個檔案，其中一個檔案的可用空間是另一個檔案的兩倍，系統將從擁有可用空間的檔案中配置兩個頁面，而從另一個檔案中配置一個頁面。 這代表檔案群組中的每個檔案都擁有類似的空間使用百分比。 

## <a name="tracking-modified-extents"></a>追蹤修改的範圍 

[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 使用兩個內部資料結構來追蹤大量複製作業修改過的範圍，以及自從上個完整備份之後修改過的範圍。 這些資料結構大幅加速差異備份； 同時也在資料庫使用大量記錄復原模式時，提高了大量複製作業記錄的速度。 就好像整體配置對應 (GAM) 和共用整體配置對應 (SGAM) 分頁一樣，這些結構也是點陣圖，其中每個位元都代表一個範圍。 

- **差異式變更對應 (DCM)**    
   這種對應會追蹤自從上個 `BACKUP DATABASE` 陳述式之後發生變更的範圍。 若某個範圍的位元為 1，代表該範圍自從上個 `BACKUP DATABASE` 陳述式之後已經修改。 若位元為 0，代表該範圍並未修改過。 差異備份只讀取 DCM 分頁，找出哪些範圍經過修改。 這可大幅減少差異式備份必須掃描的分頁數目。 差異式備份的執行時間長度，將與上次執行 BACKUP DATABASE 陳述式之後修改過的範圍數目成正比，而不是與資料庫的整體大小成正比。 

- **大量複製變更對應 (BCM)**    
   這種對應會追蹤自從上個 `BACKUP LOG` 陳述式之後由大量記錄作業修改過的範圍。 若某個範圍的位元為 1，代表該範圍自從上個 `BACKUP LOG` 陳述式之後已被大量記錄作業所修改。 若位元為 0，代表該範圍並未被大量記錄作業所修改。 雖然 BCM 分頁出現在所有資料庫中，但它們只適用於資料庫正在使用大量複製復原模式時。 在此復原模式中，當執行 `BACKUP LOG` 時，備份處理序會掃描 BCM 來找出修改過的範圍。 接著將這些範圍記錄到記錄備份中。 若資料庫是由資料庫備份和一系列的交易記錄檔備份來還原，這將可復原大量記錄作業。 對於使用簡單復原模式的資料庫，BCM 分頁並不適用，因為並沒有記錄下大量記錄作業。 它們也不適用於使用完整復原模式的資料庫，因為該復原模式會將大量記錄作業視為完整記錄作業。 

DCM 分頁與 BCM 分頁之間的間隔與 GAM 和 SGAM 分頁的間隔一樣，亦即 64,000 個範圍。 DCM 與 BCM 分頁在實體檔案中剛好位於 GAM 與 SGAM 分頁之後：

![special_page_order](../relational-databases/media/special-page-order.gif)

## <a name="see-also"></a>另請參閱
[sys.allocation_units &#40;Transact-SQL&#41;](../relational-databases/system-catalog-views/sys-allocation-units-transact-sql.md)     
[堆積 &#40;無叢集索引的資料表&#41;](../relational-databases/indexes/heaps-tables-without-clustered-indexes.md#heap-structures)       
[sys.dm_db_page_info](../relational-databases/system-dynamic-management-views/sys-dm-db-page-info-transact-sql.md)     
[讀取分頁](../relational-databases/reading-pages.md)   
[寫入分頁](../relational-databases/writing-pages.md)   
