---
description: 資料行存放區索引 - 查詢效能
title: 資料行存放區索引 - 查詢效能 | Microsoft Docs
ms.custom: ''
ms.date: 01/11/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: table-view-index
ms.topic: conceptual
ms.assetid: 83acbcc4-c51e-439e-ac48-6d4048eba189
author: MikeRayMSFT
ms.author: mikeray
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 1ea031ed8c733a2ced272bf05c952b7f32aa7da4
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98172770"
---
# <a name="columnstore-indexes---query-performance"></a>資料行存放區索引 - 查詢效能

[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  針對達到資料行存放區索引設計來提供的快速查詢效能的建議。    
    
 資料行存放區索引的分析和資料倉儲工作負載效能可提升高達 100 倍，且資料壓縮比傳統的資料列存放區索引提升高達 10 倍。 這些建議能讓您的查詢達到資料行存放區索引設計來提供的快速查詢效能。 本文結尾部分有資料行存放區效能的進一步說明。    
    
## <a name="recommendations-for-improving-query-performance"></a>改善查詢效能的建議    
 以下是針對達到資料行存放區索引設計來提供的高效能的建議。    
    
### <a name="1-organize-data-to-eliminate-more-rowgroups-from-a-full-table-scan"></a>1.藉由全資料表掃描來組織資料以排除更多資料列群組    
    
-   **運用插入順序。** 在一般的傳統資料倉儲案例中，資料確實是按照時間順序插入且分析是在時間維度中進行。 例如，按照季來分析銷售量。 針對這類型的工作負載，會自動發生列群組刪除。 在 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 中，您會發現查詢程序略過數字資料列群組。    
    
-   **運用資料列存放區叢集索引。** 如果一般查詢述詞位於與資料列插入順序無關的資料行上 (例如 C1)，您可以在資料行 C1 上建立資料列存放區叢集索引，然後透過卸除資料列存放區叢集索引來建立資料行存放區叢集索引。 如果您使用 `MAXDOP = 1` 明確地建立叢集資料行存放區索引，產生的叢集資料行存放區索引會在資料行 C1 上完美地排序。 如果您指定 `MAXDOP = 8`，您將會看到跨八個資料列群組重疊的值。 在您使用大型資料集建立資料行存放區索引的初期，這是此策略的常見情況。 請注意，對於非叢集資料行存放區索引 (NCCI)，如果基底的資料列存放區資料表具有叢集索引，表示資料列已排序過。 在此情況下，產生的非叢集資料行存放區索引將會自動排序。 一項需要注意的重點是，資料行存放區索引不會因繼承而維持資料列的順序。 隨著插入新資料列或更新舊資料列，您可能需要重複該程序，因為分析查詢的效能可能會下降。    
    
-   **運用資料表分割。** 您可以分割資料行存放區索引，然後使用分割區刪除來減少可掃描之資料列群組的數量。 例如，一個事實資料表儲存客戶的購物記錄，及按季尋找特定客戶購物記錄的一般查詢模式，您可以將插入順序與自訂資料行上的分割區結合。 每個資料分割都會包含特定客戶按時間排序的資料列。 此外，如果需要從資料行存放區移除資料，請考慮使用資料表分割。 退出並截斷不再需要的資料分割是一個有效率的策略，可刪除資料，而不會產生較小的資料列群組所引進的片段。    

-   **避免刪除大量資料**。 從資料列群組移除已壓縮的資料列不是同步作業。 解壓縮資料列群組、刪除資料列然後將其重新壓縮，會十分昂貴。 因此，如果您刪除已壓縮資料列群組中的資料，仍會針對這些資料列群組進行掃描，即使其傳回較少的資料列也一樣。 如果數個資料列群組的已刪除資料列數目夠大，而足以合併到較少的資料列群組，則重新組織資料行存放區會增加索引品質，並改善查詢效能。 如果您的資料刪除程序通常會清空整個資料列群組，請考慮使用資料表分割、退出不再需要的資料分割，並將其截斷，而不是刪除資料列。 

    > [!NOTE]
    > 從 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 開始，Tuple Mover 會由背景合併工作協助，該工作會自動壓縮已存在一段時間的較小 OPEN 差異資料列群組 (由內部閾值決定)，或合併已刪除大量資料列的 COMPRESSED 資料列群組。 這可改善一段時間的資料行存放區索引品質。   
    > 如果需要從資料行存放區索引中刪除大量資料，請考慮隨著時間將該作業分成較小的刪除批次，讓背景合併工作能夠處理合併較小資料列群組的工作，並改善索引品質，而不需要在資料刪除之後排程索引重新組織維護時段。    
    > 如需資料行存放區詞彙與概念的詳細資訊，請參閱[資料行存放區索引：概觀](../../relational-databases/indexes/columnstore-indexes-overview.md)。
    
### <a name="2-plan-for-enough-memory-to-create-columnstore-indexes-in-parallel"></a>2.規劃足夠的記憶體以平行建立資料行存放區索引    
 除非記憶體受到限制，資料行存放區索引的建立預設都是平行作業。 平行建立索引比循序建立索引需要更多的記憶體。 在記憶體很寬裕的情況下，建立資料行存放區索引花費的時間大約是根據相同的資料行建構 B 型樹狀目錄的 1.5 倍。    
    
 建立資料行存放區索引所需的記憶體取決於資料行數目、字串資料行的數目、平行處理原則的程度 (DOP) 和資料的特性。 例如，假設您的資料表少於一百萬個資料列，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 便只會使用單一執行緒來建立資料行存放區索引。    
    
 如果資料表的資料列數目超過一百萬個，但是 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 無法取得足夠的記憶體授權可使用 MAXDOP 建立索引，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 則會視需要自動降低 `MAXDOP` 以符合所提供的記憶體授權。  在某些情況下若記憶體受到限制，DOP 就必須降至 1 才能建立索引。    
    
 從 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 開始，查詢一律會以批次模式進行。 在舊版中，只有當 DOP 大於 1 時才會使用批次執行。    
    
## <a name="columnstore-performance-explained"></a>資料行存放區效能說明    
 資料行存放區索引結合了高速記憶體內批次模式處理，以及大幅減少 I/O 要求的技術，來達到高效能查詢。 由於分析查詢會搜尋大量的資料列 (它們通常與 IO 關聯)，因此減少查詢執行期間的 I/O 對於資料行存放區索引的設計相當重要。 一旦將資料讀取到記憶體中，減少記憶體內作業數量就非常重要。    
    
 資料行存放區索引會透過高資料壓縮、資料行存放區刪除、資料列群組刪除和批次處理來減少 I/O 並最佳化記憶體內作業。    
    
### <a name="data-compression"></a>資料壓縮    
 資料行存放區索引比資料列存放區索引的資料壓縮提升高達 10 倍。 這樣可以大幅減少執行分析查詢的 I/O 要求，因此可改善查詢效能。    
    
-   資料行存放區索引會讀取來自磁碟機的壓縮資料，這表示需要讀取道記憶體中之資料的位元組更少了。    
    
-   資料行存放區索引會在記憶體中是以壓縮格式儲存資料，這樣可以減少相同資料讀取到記憶體中的次數，以減少 I/O。 例如，使用 10 倍壓縮，資料行存放區索引可以在記憶體中保存比未壓縮資料格式多 10 倍的資料。 記憶體中的資料越多，資料行存放區索引就越有可能在記憶體中找到所需的資料，而不會導致額外的從磁碟讀取次數。    
    
-   資料行存放區索引是根據資料行來壓縮資料，而不是根據資料列，這樣可以達到高壓縮率並減少磁碟機上儲存的資料大小。 每個資料行都已壓縮並獨立儲存。  資料行內的資料一律具有相同的資料類型，且通常會有類似的值。 當值相似的時候，資料壓縮技術更能夠達到高壓縮率。    
    
-   例如，如果一個事實資料表儲存客戶的地址，並有國家/地區的資料行，則可能值的總數量小於 200。 某些值會重複許多次。 如果該事實資料表有 1 億個資料列，則輕鬆就能壓縮國家/地區的資料列，且只需非常小的儲存空間。 逐列壓縮無法這樣利用資料行內值的相似性，且壓縮國家/地區資料行內的值會使用更多位元組。    
    
### <a name="column-elimination"></a>資料行刪除    
 資料行存放區索引讀取資料行時會略過查詢結果不需要的值。 這項功能稱為資料行刪除，能夠進一步減少查詢執行的 I/O，因而改善查詢效能。    
    
-   能夠進行資料行刪除的原因是資料以行為單位組織及壓縮。 相較之下，當逐列儲存資料時，每個資料行中的資料列值在實體上已經儲存在一起，且無法輕易分離。 查詢處理器需要讀取整個資料列來擷取特定資料行值，這樣會增加 I/O，因為不必要的額外資料也會讀取到記憶體中。    
    
-   例如，如果資料表有 50 個資料行，而查詢只使用其中 5 個資料行，則資料行存放區索引只會從磁碟機擷取那 5 個資料行。 它會略過讀取其他 45 個資料行。 假設所有資料行大小相同，這樣能額外減少 90% 的 I/O。 如果是儲存在資料列存放區，則查詢處理器必須讀取額外的 45 個資料行。    
    
### <a name="rowgroup-elimination"></a>資料列群組刪除    
 對於完整資料表掃描，大部分的資料通常都不符合查詢述詞準則。 透過使用中繼資料，資料行存放區索引能夠略過讀取不包含查詢結果所需資料的資料列群組，完全不需要實際 I/O。 這項功能稱為群組資料列刪除，能夠進一步減少查詢執行的 I/O，因而改善查詢效能。    
    
 **資料行存放區索引何時需要執行完整資料表掃描？**    
    
 從 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 開始，您可以在叢集資料行存放區索引建立一或多個一般非叢集 B 型樹狀結構索引，就如同在資料列存放區堆積上建立一樣。 非叢集 B 型樹狀結構索引可以加快具有等號比較述詞或述詞的值範圍較小的查詢。 對於更複雜的述詞，查詢最佳化工具可能會選擇完整資料表掃描。 因為無法略過資料列群組，完整資料表掃描會很費時，尤其對於大型資料表。    
    
 **什麼時候完整資料表掃描的資料列群組刪除會有利於分析查詢？**    
    
 例如，一家零售商已將他們的銷售資料以包含叢集資料行存放區索引的事實資料表來模型化。 每筆新的銷售資料儲存該交易的各種屬性，包括產品售出的日期。 有趣的是，即使資料行存放區索引不保證會經過排序，但此資料表中的資料列將會依日期排序的順序載入。 這個資料表會隨時間增長。 雖然該零售商可能會保留近 10 年的銷售資料，但分析查詢應該只需要計算最近一季的彙總。 資料行存放區索引只要查看日期資料行的中繼資料，即可刪除對前 39 季資料的存取。 這樣減少了額外 97% 讀取到記憶體及處理的資料。    
    
 **完整資料表掃描會略過哪些資料列群組？**    
    
 資料行存放區索引會使用中繼資料，來針對每個資料列群組排序各資料行區段的最大值和最小值，以決定要刪除的資料列群組。 當沒有資料行區段符合查詢述詞準則時，則會略過整個資料列群組而不進行任何值計的 IO。 能這樣運作是因為載入的資料通常已經排序過，雖然不保證資料列已經排序過，但類似的資料值通常會位於相同或鄰近的資料列群組中。    
    
 如需資料列群組的詳細資訊，請參閱[資料行存放區索引設計指導方針](../../relational-databases/sql-server-index-design-guide.md#columnstore_index)。    
    
### <a name="batch-mode-execution"></a>批次模式執行    
 [批次模式執行](../../relational-databases/query-processing-architecture-guide.md#batch-mode-execution)是指一次處理一組資料列 (通常最多 900 個資料列)，以提升執行效率。 例如，查詢 `SELECT SUM (Sales) FROM SalesData` 會彙總 SalesData 資料表的總銷售量。 在批次模式執行中，查詢執行引擎會計算群組中 900 個值的彙總。 這會將中繼資料、存取成本和其他類型的負擔分散到批次中的所有資料列上，而不是將成本花費在每個資料列上，因此可以大幅減少程式碼路徑。 當情況允許時，批次模式執行會在壓縮的資料上作業，並刪除某些資料列處理的交換運算子。 這樣能依據重要順序來加快分析查詢的執行。    
    
 並非所有查詢執行操作子都能在批次模式中執行。 例如，Insert、Delete 或 Update 等 DML 作業是以資料列為單位來執行。 批次模式運算子的目標是加速查詢效能的運算子，例如 Scan、Join、Aggregate、Sort 等等。 因為資料行存放區索引是在 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 中引進，所以仍需要一些努力來擴增可在批次模式中執行的運算子。 下表根據產品版本顯示在批次模式中執行的運算子。    
    
|批次模式運算子|何時使用？|[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)]|[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)]|[!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 與 [!INCLUDE[ssSDS](../../includes/sssds-md.md)]<sup>1</sup>|註解|    
|---------------------------|------------------------|---------------------|---------------------|---------------------------------------|--------------|    
|DML 運算子 (insert、delete、update、merge)||否|否|否|DML 不是批次模式運算子，因為它不是平行處理。 即使我們啟用序列模式批次處理，仍看不出允許 DML 以批次模式處理之後能有顯著改善。|    
|資料行存放區索引掃描|SCAN|NA|是|是|對於資料行存放區索引，我們可以將述詞推送到 SCAN 節點。|    
|資料行存放區索引掃描 (非叢集)|SCAN|是|是|是|是|    
|索引搜尋||NA|NA|否|我們以資料列模式透過非叢集 B 型樹狀結構索引執行搜尋作業。|    
|計算純量|評估純量值的運算式|是|是|是|資料類型有所限制。 適用於所有批次模式運算子。|    
|串連 (concatenation)|UNION 和 UNION ALL|否|是|是||    
|filter|套用述詞|是|是|是||    
|雜湊比對|雜湊型彙總函式，outer hash join、right hash join、left hash join、right inner join、left inner join|是|是|是|彙總限制︰對於字串沒有最小值/最大值。 可用的彙總函式有 sum/count/avg/min/max。<br />連結的限制：在非整數類型上不相符類型不能互相聯結。|    
|合併連結||否|否|否||    
|多執行緒查詢||是|是|是||    
|巢狀迴圈||否|否|否||    
|在 MAXDOP 1 下執行的單一執行緒查詢||否|否|是||    
|具有序列查詢計畫的單一執行序查詢||否|否|是||    
|sort|在 SCAN 上搭配資料行存放區索引由子句排序。|否|否|是||    
|頂端排序||否|否|是||    
|視窗彙總||NA|NA|是|[!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 中的新運算子。|    
    
<sup>1</sup> 適用於 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)]、[!INCLUDE[ssSDS](../../includes/sssds-md.md)] 進階層、標準層 - S3 及更新版本，和所有 vCore 層，以及 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]    

如需詳細資訊，請參閱[查詢處理架構指南](../../relational-databases/query-processing-architecture-guide.md#batch-mode-execution)。
    
### <a name="aggregate-pushdown"></a>彙總下推    
 彙總計算從 SCAN 擷取符合之資料列並在「批次模式」中彙總其值的一般執行路徑。 儘管這提供良好的效能，但在 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 中，可將彙總作業推入 SCAN 節點，若符合下列條件，則能夠以依據重要順序提升「批次模式」執行上彙總運算的效能： 
 
-    彙總是 `MIN`、`MAX`、`SUM`、`COUNT` 和 `COUNT(*)`。 
-  彙總運算子必須在 SCAN 節點頂端或包含 `GROUP BY` 的 SCAN 節點頂端。
-  此彙總不是相異彙總。
-  彙總資料行不是字串資料行。
-  彙總資料行不是虛擬資料行。 
-  輸入和輸出資料類型必須是下列其中一項，必須納入 64 個位元。
    -  `tinyint`, `int`, `bigint`, `smallint`, `bit`
    -  精確度 <= 18 的 `smallmoney`、`money`、`decimal` 和 `numeric`
    -  `smalldate`, `date`, `datetime`, `datetime2`, `time`
    
 彙總下推可透過有效彙總快取相容執行中壓縮過/編碼過的資料，及透過利用 SIMD 來進一步加速。    
    
 ![彙總下推](../../relational-databases/indexes/media/aggregate-pushdown.jpg "彙總下推")    
    
例如，以下兩個查詢都會進行彙總下推：    
    
```sql     
SELECT  productkey, SUM(TotalProductCost)    
FROM FactResellerSalesXL_CCI    
GROUP BY productkey;    
    
SELECT  SUM(TotalProductCost)    
FROM FactResellerSalesXL_CCI;    
```    
    
### <a name="string-predicate-pushdown"></a>字串述詞下推    
在設計資料倉儲結構描述時，建議的結構描述模型是使用一或多個事實資料表及多個維度資料表的星型結構描述或雪花結構描述。 [事實資料表](https://wikipedia.org/wiki/Fact_table) 會儲存商務測量或交易，而 [維度資料表](https://wikipedia.org/wiki/Dimension_table) 會儲存要分析之事實間的維度。    
    
例如，事實可以是代表特定產品在特定地區之銷售的記錄，而維度則代表地區、產品等集合。 事實和維度資料表示透過主/外部索引鍵關聯性來連線。 最常使用的分析查詢會聯結一或多個維度資料表和事實資料表。    
    
讓我們考慮維度資料表 `Products`。 一般的主索引鍵通常會是以字串資料類型表示的 `ProductCode`。 為了查詢的效能，最佳做法是建立替代索引鍵 (通常是整數資料行)，以從事實資料表來參照維度資料表中的資料列。 
    
資料行存放區索引可以非常有效率地執行具有涉及數字或整數型索引鍵之聯結/述詞的分析查詢。 不過，在許多客戶工作負載中，我們發現以字串型資料行連結事實/維度資料表的用法，而結果是具有資料行存放區索引的查詢效能無法望其項背。 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 藉由將包含字串資料行的述詞下推至 SCAN 節點，大幅改善對字串型資料行分析查詢的效能。    
    
字串述詞下推利用為資料行建立的主要/次要字典來改善查詢效能。 例如，讓我們考慮位在包含 100 個不同字串值的資料列群組中的字串資料行區段。 假設有 1 百萬個資料列，則表示每個不同的字串值平均都被參照 10,000 次。    
    
使用字串述詞下推，查詢執行會依據字典中的值來計算述詞，如果符合，則所有參照該字典值的資料列都會自動符合。 這藉由兩種方法改善效能：
1.  只傳回符合的資料列，減少需要傳送出 SCAN 節點的資料列數量。 
2.  大幅減少字串比較的數量。 在此範例中，只需要對 1 百萬個比較項目進行 100 個字串比較。 以下描述一些限制：    

    -   差異資料列群組沒有字串述詞下推。 差異資料列群組中的資料行沒有字典。    
    -   如果字典超過 64 KB 個項目則沒有字串述詞下推。    
    -   不支援評估 NULL 的運算式。    
    
## <a name="see-also"></a>另請參閱    
 [資料行存放區索引設計指導方針](../../relational-databases/sql-server-index-design-guide.md#columnstore_index) [資料行存放區索引資料載入指導方針](../../relational-databases/indexes/columnstore-indexes-data-loading-guidance.md)   
 [開始使用資料行存放區進行即時作業分析](../../relational-databases/indexes/get-started-with-columnstore-for-real-time-operational-analytics.md)     
 [資料倉儲的資料行存放區索引](../../relational-databases/indexes/columnstore-indexes-data-warehouse.md)   
 [重新組織與重建索引](../../relational-databases/indexes/reorganize-and-rebuild-indexes.md)    
 [資料行存放區索引架構](../../relational-databases/sql-server-index-design-guide.md#columnstore_index)   
 [CREATE INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/create-index-transact-sql.md)    
 [ALTER INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/alter-index-transact-sql.md)     
  
