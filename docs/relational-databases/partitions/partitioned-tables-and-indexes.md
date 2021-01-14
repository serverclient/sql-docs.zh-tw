---
description: 分割資料表與索引
title: 資料分割資料表與索引 | Microsoft Docs
ms.custom: ''
ms.date: 1/5/2021
ms.prod: sql
ms.reviewer: ''
ms.technology: ''
ms.topic: conceptual
helpviewer_keywords:
- partitioned tables [SQL Server], about partitioned tables
- partitioned indexes [SQL Server], architecture
- partitioned tables [SQL Server], architecture
- partitioned indexes [SQL Server], about partitioned indexes
- index partitions
ms.assetid: cc5bf181-18a0-44d5-8bd7-8060d227c927
author: julieMSFT
ms.author: jrasnick
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: dcc5d8e3602261c975f5517ea859e19fc7902936
ms.sourcegitcommit: 629229a7c33a3ed99db63b89127bb016449f7d3d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97952049"
---
# <a name="partitioned-tables-and-indexes"></a>分割資料表與索引
[!INCLUDE [SQL Server Azure SQL Database](../../includes/applies-to-version/sql-asdb.md)]
  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 支援資料表和索引資料分割。 資料分割資料表和索引的資料，已分成可以選擇性地在資料庫中的多個檔案群組之間散佈的單位。 資料是以水平方式分割，因此資料列的群組可對應至個別的資料分割。 單一索引或資料表的所有分割區必須在同一個資料庫中。 在資料上執行查詢或更新時，資料表或索引會被視為單一邏輯實體。 在 [!INCLUDE[ssSQL15_md](../../includes/sssql15-md.md)] SP1 之前，並非每個 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 版本都可使用資料分割資料表和索引。 如需 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 版本支援的功能清單，請參閱 [SQL Server 2016 版本和支援的功能](../../sql-server/editions-and-components-of-sql-server-2016.md)。  
  
> [!IMPORTANT]  
> [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 預設最多支援 15,000 個資料分割。 在 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 之前的舊版本中，預設的資料分割數量不得超過 1,000 個。 在 x86 系統中雖能建立超過 1,000 個資料表或索引，但不予支援。  
  
## <a name="benefits-of-partitioning"></a>資料分割的優點  
 對大型資料表或索引進行資料分割，可以具有下列管理能力和效能優點。  
  
-   您可以快速並有效率地傳送或存取資料子集，同時維護資料集合的完整性。 例如，將資料從 OLTP 載入至 OLAP 系統這類作業只需要數秒的時間，而不用像未對資料進行資料分割時，需要數分鐘或數小時的時間才能執行作業。  
  
-   您可以更快速地對一個或多個分割區執行維護作業。 作業只處理這些資料子集，而非整個資料表，因此會更有效率。 例如，您可以選擇壓縮一個或多個分割區中的資料，或是重建索引的一個或多個分割區。  
  
-   您可以提升查詢效能，不過這要視您經常執行的查詢類型和硬體組態而定。 例如，當分割資料行與要在其上聯結資料表的資料行相同時，查詢最佳化工具可以更快速地同等聯結 (equi-join) 兩個或更多資料分割資料表間的查詢。 請參閱以下的[查詢](#queries)以取得進一步資訊。
  
[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 在為 I/O 作業執行資料排序時，會先依資料分割排序資料。 若要改善資料排序效能，請設定 RAID，以將分割區的資料檔案分割到多個磁碟上。 利用這種方式，雖然 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 仍然會依資料分割排序資料，但它可以同時存取每個資料分割的所有磁碟機。  
  
此外，您可以啟用分割區層級的鎖定擴大 (而非整個資料表) 來提升效能。 這可以減少資料表上的鎖定競爭。 若要允許鎖定擴大到資料分割，以減少鎖定競爭，請將 `ALTER TABLE` 陳述式的 `LOCK_ESCALATION` 選項設定為 AUTO。 

> [!TIP]
> 資料表或索引的分割區可以置於單一檔案群組 (例如 `PRIMARY` 檔案群組) 或多個檔案群組上。 使用分層式儲存體時，使用多個檔案群組可讓您將特定的分割區指派至特定的儲存層。 所有其他資料分割優點皆適用，無論所使用的檔案群組數目為何，或是特定檔案群組上的分割區位置為何。
  
## <a name="components-and-concepts"></a>元件和概念  
下列詞彙適用於資料表和索引資料分割。  
  
### <a name="partition-function"></a>分割區函數  
一種資料庫物件，其可定義資料表或索引的資料列如何根據某些資料行 (稱為分割資料行) 的值對應至資料分割集。 分割資料行中的每個值都是分割區函數其輸入，而函數會傳回分割值。 分割區函數會定義分割區的數目，以及資料表擁有的分割區界限。 例如，如果資料表包含銷售訂單資料，您可能想要根據 **datetime** 資料行 (例如銷售日期) 將資料表分割為 12 個 (每月) 分割區。  
  
### <a name="partition-scheme"></a>分割區配置 
將分割區函數的資料分割對應至一組檔案群組的資料庫物件。 將分割區放在不同檔案群組的主要理由是可以確保能夠對分割區獨立執行備份作業。 這是因為您可以對個別檔案群組執行備份。  

> [!NOTE]
> Azure [!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)] 只支援主要檔案群組。  
  
### <a name="partitioning-column"></a>資料分割資料行  
分割區函數用於分割資料表或索引的資料表或索引資料行。 參與分割區函數的計算資料行必須明確地標示為 PERSISTED。 所有適用於做為索引資料行的資料類型都可以做為分割資料行，但 **timestamp** 除外。 無法指定 **ntext**、 **text**、 **image**、 **xml**、 **varchar(max)** 、 **nvarchar(max)** 或 **varbinary(max)** 資料類型。 此外，也無法指定 Microsoft .NET Framework Common Language Runtime (CLR) 使用者定義型別及別名資料類型資料行。  
  
### <a name="aligned-index"></a>對齊的索引  
在與對應資料表相同的分割區配置上建立的索引。 資料表與其索引對齊時，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 在維護資料表及其索引的資料分割結構的同時，可快速且有效地切換資料分割。 索引不需要參與相同的具名分割區函數，即可對齊其基底資料表。 因為下列原因，索引與基底資料表的資料分割函式在本質上必然相同：
 1. 資料分割函式的引數具有相同的資料類型。
 2. 兩者定義了相同的資料分割數。
 3. 兩者為資料分割定義了相同的界限值。  

#### <a name="partitioning-clustered-indexes"></a>分割叢集索引
分割叢集索引時，叢集索引鍵必須包含分割資料行。 分割非唯一的叢集索引，且未在叢集索引鍵中明確指定分割資料行時，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 預設會將分割資料行加入叢集索引鍵清單中。 如果叢集索引是唯一的，您必須明確指定叢集索引鍵包含分割資料行。        

#### <a name="partitioning-nonclustered-indexes"></a>分割非叢集索引
分割唯一的非叢集索引時，索引鍵必須包含分割資料行。 根據預設，當分割不是唯一的非叢集索引時，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會新增分割資料行，作為索引的非索引鍵 (已包含) 資料行，務使索引與基底資料表對齊。 若索引中已有 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]，便不會將分割資料行新增到索引。 

### <a name="non-aligned-index"></a>未對齊的索引  
與其對應資料表分開進行資料分割的索引。 亦即，索引具有不同的分割區配置，或放在與基底資料表不同的檔案群組中。 在下列情況下，設計未對齊的資料分割索引可能十分有用：  
-   尚未對基底資料表進行資料分割。  
-   索引鍵是唯一的，並且不含資料表的分割資料行。  
-   您希望使用不同的聯結資料行，讓基底資料表參與具有多個資料表的共置聯結。  

### <a name="partition-elimination"></a>分割區刪除
查詢最佳化工具用來只存取相關分割區以滿足查詢篩選準則的程序。  

## <a name="performance-guidelines"></a>效能方針  
 15,000 個分割區中新的且較高的限制會影響記憶體、資料分割索引作業、DBCC 命令和查詢。 此節描述將分割區數目增加為超過 1,000 個的效能含意，並視需要提供解決方案。 具有最大分割區數目增加為 15,000 的限制，就可以較長時間地儲存資料。 不過，您只應該保留必要的資料，並維護效能與資料分割數目之間的平衡。  
  
### <a name="processor-cores-and-number-of-partitions-guidelines"></a>處理器核心和資料分割數目的指導方針  
 若要最大化平行作業的效能，使用的資料分割數建議與處理器核心數相同 (上限為 64，這是 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 所能運用的平行處理器數上限)。  
  
### <a name="memory-usage-and-guidelines"></a>記憶體使用量和方針  
 如果正在使用大量分割區，則建議您至少使用 16 GB 的 RAM。 如果系統的記憶體不足，則資料操作語言 (DML) 陳述式、資料定義語言 (DDL) 陳述式和其他作業可能會因記憶體不足而失敗。 RAM 為 16 GB 且執行許多記憶體密集處理序的系統，可能會在針對大量分割區執行的作業時記憶體不足。 因此，記憶體愈多 (超過 16 GB)，發生效能和記憶體問題的機會可能就愈少。  
  
 記憶體限制會影響效能或 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 建立資料分割索引的能力。 如果資料表有適用的叢集索引，當索引沒有對齊基底資料表或沒有對齊叢集索引時，特別會發生這種狀況。 在此情況下，增加索引建立記憶體伺服器組態選項可能很有用。 如需詳細資訊，請參閱[設定索引建立記憶體伺服器組態選項](../../database-engine/configure-windows/configure-the-index-create-memory-server-configuration-option.md)。 
  
### <a name="partitioned-index-operations"></a>資料分割索引作業  
可以對包含超過 1,000 個資料分割的資料表，建立及重建未對齊的索引，但不予支援。 此做法可能會導致在作業期間效能降低或耗用過多記憶體。  
  
建立和重建對齊索引所需的執行時間，可能會隨著分割區數目的增加而增加。 建議您不要同時執行多個建立和重建索引命令，這樣做可能會造成效能和記憶體問題。  
  
 當 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行排序以建立資料分割索引時，會先對每個資料分割建立一個排序表。 如有指定 SORT_IN_TEMPDB 索引選項，其會在每個資料分割的個別檔案群組或在 **tempdb** 中建置排序表。 每個排序表都需要最小量的記憶體才能建立。 當您在建立對齊基底資料表的資料分割索引時，會使用少量記憶體一次建立一個排序表。 不過，當您在建立非對齊資料分割索引時，則會同時建立排序表。 因此，必須有足夠的記憶體才能處理這些並行排序作業。 分割區的數量越大的話，則需要越多記憶體。 對每個分割區來說，每個排序表的大小下限為 40 個頁面，而每一頁都為 8 KB。 例如，具有 100 個分割區的非對齊資料分割索引，需要足夠的記憶體，才能同時連續排序 4,000 (40 * 100) 頁。 如果有可用的記憶體，則建立作業會成功，但效能會變差。 如果無法使用這個數量的記憶體，建立作業會失敗。 此外，具有 100 個分割區的對齊資料分割索引只需要足夠排序 40 頁的記憶體，因為並不會同時執行排序作業。  
  
 若 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 對多處理器電腦的建置作業套用了平行處理原則指標，則已對齊及未對齊之索引的記憶體需求可能會更大。 這是因為平行處理原則的程度越大，則記憶體需求也越大。 例如，如果 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 將平行處理原則的程度設為 4，則具有 100 個資料分割的非對齊資料分割索引需要四個處理器的足夠記憶體，才能同時排序 4,000 頁或 16,000 頁。 如果已對齊資料分割索引，則記憶體需求會降低為四個處理器排序 40 頁，或 160 (4 * 40) 頁。 您可以使用 MAXDOP 索引選項，以手動方式降低平行處理原則的程度。  

### <a name="dbcc-commands"></a>DBCC 命令  
如果具有較大的分割區數目，則 DBCC 命令所需的執行時間可能會隨著分割區數目的增加而增加。  
  
### <a name="queries"></a>查詢  
使用分割區刪除的查詢，可能會具有較大分割區數目的可比較或改善效能。 未使用分割區刪除的查詢，所需執行時間可能會隨著分割區數目的增加而增加。  
  
例如，假設資料表有 1 億個資料列和資料行 `A`、 `B`和 `C`。 
-  在案例 1 中，資料表的資料行 `A`上分成 1000 個分割區。 
-  在案例 2 中，資料表的資料行 `A`上分成 10,000 個資料分區。 資料表查詢若對資料行 `A` 使用了 `WHERE` 子句篩選，將會執行資料分割刪除，並掃描一個資料分割。 在案例 2 中，因為分割區中要掃描的資料列較少，所以這個相同查詢的執行速度會較快。 查詢若對資料行 B 使用了 `WHERE` 子句篩選，將會掃描所有資料分割。 在案例 1 中，因為要掃描的分割區較少，所以此查詢的執行速度會比案例 2 還要快。  

如果查詢在分割資料行以外的資料行上使用類似 TOP 或 MAX/MIN 的運算子，則可能會因為資料分割而遇到效能降低的情況，因為所有分割區都必須評估。  

如果您經常需要在二或多個資料分割資料表之間進行等聯結 (Equi-Join) 來執行查詢，則查詢的分割資料行必須與資料表所據以聯結的資料行相同。 此外，資料表或其索引都應該進行共置。 這表示它們如果不是使用相同命名的資料分割函數，就是使用不同名稱但本質上相同的資料分割函數，以便能：
-  使用相同數目的資料分割參數，並確保對應的參數具有相同的資料類型。
-  定義相同數目的資料分割。
-  為資料分割定義相同的界限值。
如此一來，因為資料分割本身可以聯結，所以查詢最佳化工具處理聯結的速度會更快。 如果查詢所聯結的兩個資料表並未共置或未根據聯結欄位進行資料分割，則資料分割的存在，實際上可能會使查詢速度變慢，而非變快。

如需查詢處理中分割區處理的詳細資訊，請參閱[分割資料表和索引上的查詢處理增強功能](../../relational-databases/query-processing-architecture-guide.md#query-processing-enhancements-on-partitioned-tables-and-indexes)。

## <a name="behavior-changes-in-statistics-computation-during-partitioned-index-operations"></a>資料分割索引作業期間，統計資料運算的行為會改變  
 從 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)]開始，並不會在建立或重建資料分割索引之後掃描資料表中所有的資料列建立統計資料。 反之，查詢最佳化工具會使用預設的採樣演算法來產生統計資料。 升級具有分割區索引的資料庫之後，可能會注意到這些索引之長條圖資料的差異。 此行為變更可能不會影響查詢效能。 若要在掃描資料表中所有資料列時取得分割區索引的統計資料，使用子句 `FULLSCAN` 時請使用 `CREATE STATISTICS` 或 `UPDATE STATISTICS`。  
  
## <a name="related-tasks"></a>相關工作  
  
|工作|主題|  
|-|-|   
|描述如何建立分割區函數和分割區配置，然後將它們套用至資料表和索引。|[建立分割區資料表及索引](../../relational-databases/partitions/create-partitioned-tables-and-indexes.md)|  
|||  
  
## <a name="related-content"></a>相關內容  
 您可能會發現下列資料分割資料表和索引策略和實作白皮書十分有用。  
-   [使用 SQL Server 2008 的資料分割資料表和索引策略](https://msdn.microsoft.com/library/dd578580\(SQL.100\).aspx)    
-   [如何實作自動滑動視窗](https://msdn.microsoft.com/library/aa964122\(SQL.90\).aspx)    
-   [大量載入至資料分割資料表](/previous-versions/sql/sql-server-2005/administrator/cc966380(v=technet.10))    
-   [Project REAL:Data Lifecycle - Partitioning](/previous-versions/sql/sql-server-2005/administrator/cc966424(v=technet.10)) (專案 REAL：資料生命週期 - 資料分割)    
-   [分割資料表和索引上的查詢處理增強功能](/previous-versions/sql/sql-server-2008-r2/ms345599(v=sql.105))    
-   [建立大規模關聯式資料倉儲的前 10 大最佳作法](https://download.microsoft.com/download/0/F/B/0FBFAA46-2BFD-478F-8E56-7BF3C672DF9D/SQLCAT's%20Guide%20to%20Relational%20Engine.pdf)，位於 _SQLCAT 指南：關聯式工程_ 中
  
