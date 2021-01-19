---
title: 偵測和解決片段索引 | Microsoft Docs
description: 本文描述索引片段如何發生、如何偵測存在多少片段，以及如何判斷使用 T-SQL 和 SQL Server Management Studio 來解決索引片段是最佳選擇。
ms.custom: ''
ms.date: 03/19/2020
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.technology: table-view-index
ms.topic: conceptual
f1_keywords:
- sql13.swb.index.rebuild.f1
- sql13.swb.indexproperties.fragmentation.f1
- sql13.swb.index.reorg.f1
helpviewer_keywords:
- large object defragmenting
- indexes [SQL Server], reorganizing
- index reorganization [SQL Server]
- reorganizing indexes
- defragmenting large object data types
- index fragmentation [SQL Server]
- index rebuilding [SQL Server]
- rebuilding indexes
- indexes [SQL Server], rebuilding
- defragmenting indexes
- nonclustered indexes [SQL Server], defragmenting
- fragmentation [SQL Server]
- index defragmenting [SQL Server]
- LOB data [SQL Server], defragmenting
- clustered indexes, defragmenting
ms.assetid: a28c684a-c4e9-4b24-a7ae-e248808b31e9
author: pmasl
ms.author: mikeray
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 991a30108d0683d89d8bece48eb0d2de1c1e0d37
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98171880"
---
# <a name="resolve-index-fragmentation-by-reorganizing-or-rebuilding-indexes"></a>藉由重新組織或重建索引來解決索引片段

[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

本文描述索引重組如何發生，並討論其對查詢效能的影響。 當判斷[索引中存在的片段數量](#detecting-the-amount-of-fragmentation)之後，您可在所選擇的工具中執行 Transact-SQL 命令或使用 SQL Server Management Studio，藉由[重新組織索引](#reorganize-an-index)或[重建索引](#rebuild-an-index)來重組索引。

## <a name="index-fragmentation-overview"></a>索引片段概觀

什麼是索引片段，以及應該關注的理由：

- 當索引的索引內邏輯順序頁面 (根據索引的索引鍵值) 與索引頁面中實體順序不相符時，就會存在片段。
- 只要對基礎資料進行插入、更新或刪除作業，[!INCLUDE[ssde_md](../../includes/ssde_md.md)] 就會自動修改索引。 例如，在資料表中新增資料列，可能會造成資料列存放區索引中的現有頁面分割，以騰出空間來插入新的索引鍵值。 過一段時間後，這些修改就可能使索引中的資訊變成散佈於資料庫中 (片段)。 當根據索引鍵值的邏輯順序頁面，與資料檔中的實體順序不相符時，就會有片段產生。
- 嚴重片段的索引可能會降低查詢效能，因為需要額外的 I/O 來找出索引指向的資料。 愈多 I/O 會導致應用程式回應速度愈緩慢，尤其是涉及掃描作業時。

## <a name="detecting-the-amount-of-fragmentation"></a>偵測片段數量

決定要使用哪一個索引重組方法，第一步是分析索引以決定片段的程度。 您會以不同方式來偵測資料列存放區索引和資料行存放區索引的片段。

> [!NOTE]
> 在刪除大量資料之後，檢閱索引或堆積片段尤其重要。 針對堆積，如果經常更新，則可能也需要檢閱片段以避免激增的轉送記錄。 如需堆積的詳細資訊，請參閱[堆積 (無叢集索引的資料表)](../../relational-databases/indexes/heaps-tables-without-clustered-indexes.md#heap-structures)。 

### <a name="detecting-fragmentation-of-rowstore-indexes"></a>偵測資料列存放區索引的片段

透過使用 [sys.dm_db_index_physical_stats](../../relational-databases/system-dynamic-management-views/sys-dm-db-index-physical-stats-transact-sql.md)，即可在特定索引中、在資料表或索引檢視表上的所有索引、在資料庫中所有索引或在所有資料庫的所有索引中偵測片段。 對於資料分割索引而言， **sys.dm_db_index_physical_stats** 也為每個資料分割提供片段資訊。

由 **sys.dm_db_index_physical_stats** 所傳回的結果集包含下列資料行：

|資料行|描述|
|------------|-----------------|
|**avg_fragmentation_in_percent**|邏輯片段的百分比 (索引中失序的頁面)。|
|**fragment_count**|在索引中的片段數目 (實體上為連續的分葉頁面)。|
|**avg_fragment_size_in_pages**|在索引中一個片段的頁面平均數目。|

在了解片段的程度後，請使用下表來決定移除片段最好的方法：[INDEX REORGANIZE](#reorganize-an-index) 或 [INDEX](#rebuild-an-index)。

|**avg_fragmentation_in_percent** 值|修正的陳述式|
|-----------------------------------------------|--------------------------|
|> 5% 且 < = 30% <sup>1</sup>|ALTER INDEX REORGANIZE|
|> 30% <sup>1</sup>|ALTER INDEX REBUILD WITH (ONLINE = ON) <sup>2</sup>|

<sup>1</sup> 這些值提供概略的方針，其可供判斷應該在 `ALTER INDEX REORGANIZE` 和 `ALTER INDEX REBUILD` 之間切換的時間點。 不過，實際的值可能隨各種狀況而異。 請務必嘗試不同的值，以判斷適合您環境的最佳臨界值。      

> [!TIP] 
> 例如，如果指定的索引主要用於掃描作業，則移除片段可以改善這些作業的效能。 對於主要用於搜尋作業的索引而言，效能優勢可能較不明顯。    
同樣地，移除堆積中的片段 (沒有叢集索引的資料表) 對於非叢集索引掃描作業特別有用，但對查閱作業則幾乎沒有作用。

<sup>2</sup> 重建索引可於線上或離線執行。 重新組織索引則一律在線上執行。 若要達到與重新組織選項相似的可用性，您應該在線上重建索引。 如需詳細資訊，請參閱 [INDEX](#rebuild-an-index) 和[線上執行索引作業](../../relational-databases/indexes/perform-index-operations-online.md)。

您不需要重組片段百分比低於 5% 的索引，因為由重新組織或重建索引所產生 CPU 成本一律遠超過移除如此少量片段所獲得的好處。 此外，重建或重新組織小型資料列存放區索引通常不會減少實際片段。 最高到 (包括) [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)]，[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 會使用混合範圍來配置空間。 因此，小型索引的頁面有時候會儲存在混合範圍上。 混合範圍最多可由八個物件所共用，所以當重新組織或重建索引之後，小型索引中的片段可能不會減少。 另請參閱[重建資料列存放區索引的特定考量](#considerations-specific-to-rebuilding-rowstore-indexes)。 如需範圍的詳細資訊，請參閱[分頁與範圍架構指南](../../relational-databases/pages-and-extents-architecture-guide.md#extents)。

### <a name="detecting-fragmentation-of-columnstore-indexes"></a>偵測資料行存放區索引的片段

透過使用 [sys.dm_db_column_store_row_group_physical_stats](../../relational-databases/system-dynamic-management-views/sys-dm-db-column-store-row-group-physical-stats-transact-sql.md)，您可判斷已刪除資料列的百分比，其為資料行索引內資料列群組中片段的合理量值。 使用此資訊來計算特定索引、資料表上所有索引、資料庫中所有索引，或是所有資料庫中所有索引的片段。

由 **sys.dm_db_column_store_row_group_physical_stats** 所傳回的結果集包含下列資料行：

|資料行|描述|
|------------|-----------------|
|**total_rows**|實際儲存在資料列群組中的資料列數目。 針對已壓縮的資料列群組，這包含標示為已刪除的資料列。|
|**deleted_rows**|實際儲存在標示為要刪除之已壓縮資料列群組的資料列數目。 0 代表位於差異存放區中的資料列群組。|

請使用透過此公式傳回的這項資訊來計算索引片段：

```
100*(ISNULL(deleted_rows,0))/NULLIF(total_rows,0)
```

在了解索引片段的程度後，請使用下表來決定移除片段的最佳方法：[INDEX REORGANIZE](#reorganize-an-index) 或 [INDEX](#rebuild-an-index)。

|**computed fragmentation in percent** 值|套用至版本|修正的陳述式|
|-----------------------------------------------|--------------------------|--------------------------|
|> = 20%|[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 和 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)]|ALTER INDEX REBUILD|
|> = 20%|從 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 開始|ALTER INDEX REORGANIZE|

### <a name="to-check-the-fragmentation-of-a-rowstore-index-using-tsql"></a>使用 [!INCLUDE[tsql](../../includes/tsql-md.md)] 檢查資料列存放區索引的片段

下列範例會在 `AdventureWorks2016` 資料庫的 `HumanResources.Employee` 資料表中尋找所有索引的平均片段百分比。

```sql
SELECT a.object_id, object_name(a.object_id) AS TableName,
    a.index_id, name AS IndedxName, avg_fragmentation_in_percent
FROM sys.dm_db_index_physical_stats
    (DB_ID (N'AdventureWorks2016_EXT')
        , OBJECT_ID(N'HumanResources.Employee')
        , NULL
        , NULL
        , NULL) AS a
INNER JOIN sys.indexes AS b
    ON a.object_id = b.object_id
    AND a.index_id = b.index_id;
GO
```

上面的陳述式會傳回如下結果集。

```
object_id   TableName    index_id    IndexName                                             avg_fragmentation_in_percent
----------- ------------ ----------- ----------------------------------------------------- ------------------------------
1557580587  Employee     1           PK_Employee_BusinessEntityID                          0
1557580587  Employee     2           IX_Employee_OrganizationalNode                        0
1557580587  Employee     3           IX_Employee_OrganizationalLevel_OrganizationalNode    0
1557580587  Employee     5           AK_Employee_LoginID                                   66.6666666666667
1557580587  Employee     6           AK_Employee_NationalIDNumber                          50
1557580587  Employee     7           AK_Employee_rowguid                                   0

(6 row(s) affected)
```

如需詳細資訊，請參閱 [sys.dm_db_index_physical_stats](../../relational-databases/system-dynamic-management-views/sys-dm-db-index-physical-stats-transact-sql.md)。

### <a name="to-check-the-fragmentation-of-a-columnstore-index-using-transact-sql"></a>使用 Transact-SQL 檢查資料行存放區索引的片段

下列範例會在 `AdventureWorksDW2016` 資料庫的 `dbo.FactResellerSalesXL_CCI` 資料表中尋找所有索引的平均片段百分比。

```sql
SELECT i.object_id,
    object_name(i.object_id) AS TableName,
    i.index_id,
    i.name AS IndexName,
    100*(ISNULL(SUM(CSRowGroups.deleted_rows),0))/NULLIF(SUM(CSRowGroups.total_rows),0) AS 'Fragmentation'
FROM sys.indexes AS i  
INNER JOIN sys.dm_db_column_store_row_group_physical_stats AS CSRowGroups
    ON i.object_id = CSRowGroups.object_id
    AND i.index_id = CSRowGroups.index_id
WHERE object_name(i.object_id) = 'FactResellerSalesXL_CCI'
GROUP BY i.object_id, i.index_id, i.name
ORDER BY object_name(i.object_id), i.name;
```

上面的陳述式會傳回如下結果集。

```
object_id   TableName                   index_id    IndexName                       Fragmentation
----------- --------------------------- ----------- ------------------------------- ---------------
114099447   FactResellerSalesXL_CCI     1           IndFactResellerSalesXL_CCI      0

(1 row(s) affected)
```

### <a name="check-index-fragmentation-using-sql-server-management-studio"></a>使用 SQL Server Management Studio 檢查索引片段

> [!NOTE]
> [!INCLUDE[ssManStudio](../../includes/ssManStudio-md.md)] 無法用來計算 SQL Server 中資料行存放區索引的片段，而且無法用來計算 Azure SQL Database 中任何索引的片段。 使用上述[!INCLUDE[tsql](../../includes/tsql-md.md)] [範例](#to-check-the-fragmentation-of-a-columnstore-index-using-transact-sql)。

1. 在 [物件總管] 中，展開包含您要檢查其索引片段之資料表的資料庫。
2. 展開 **[資料表]** 資料夾。
3. 展開要檢查其索引片段的資料表。
4. 展開 **[索引]** 資料夾。
5. 以滑鼠右鍵按一下要檢查其片段的索引，然後選取 **[屬性]** 。
6. 在 **[選取頁面]** 底下，選取 **[片段]** 。

**[片段]** 頁面上提供下列資訊：

|值|描述|
|---|---|
|**頁面飽和度**|指出索引頁面的平均飽和度，以百分比表示。 100% 表示索引頁面完全飽和。 50% 表示平均每個索引頁面為半飽和。|
|**片段總計**|邏輯片段百分比。 這指出索引中未依順序儲存的頁數。|
|**平均資料列大小**|分葉層級資料列的平均大小。|
|**深度**|索引中的層級數目，包括分葉層級。|
|**轉送的記錄**|在堆積中，有指向另一個資料位置之轉送指標的記錄數目。 (此狀態發生於更新期間，原始位置的空間不足以儲存新資料列時)。|
|**準刪除列**|標示為刪除但尚未移除的資料列數目。 這些資料列將由清除執行緒在伺服器不忙碌時移除。 這個值不包含因未處理的快照集隔離交易而保留的資料列。|
|**索引類型**|索引的類型。 可能的值為 **[叢集索引]** 、 **[非叢集索引]** 以及 **[主要 XML]** 。 資料表也可以儲存為堆積 (無索引)，但是無法開啟此 [索引屬性] 頁面。|
|**分葉層級資料列**|分葉層級資料列的數目。|
|**資料列大小上限**|分葉層級資料列大小上限。|
|**資料列大小下限**|最小分葉層級資料列大小。|
|**頁面**|資料頁的總數。|
|**分割區識別碼**|包含索引之 B 型樹狀目錄的資料分割識別碼。|
|**版本準刪除列**|因為未處理的快照集隔離交易而保留的準刪除記錄數目。|

## <a name="defragmenting-indexes-by-rebuilding-or-reorganizing-the-index"></a>藉由重建或重新組織索引來重組索引

您可使用下列其中一種方法來重組片段索引：

- 重新組織索引
- 重建索引

> [!NOTE]
> 針對在資料分割配置上建立的資料分割索引，您可在完整索引或在索引的單一資料分割上使用下列方法。

### <a name="reorganize-an-index"></a>重新組織索引

重新組織索引所用的系統資源最少，且為線上作業。 這表示不會保留長期封鎖的資料表鎖定，而且在 `ALTER INDEX REORGANIZE` 交易期間，可以繼續查詢或更新基礎資料表。

- 針對[資料列存放區索引](clustered-and-nonclustered-indexes-described.md)，[!INCLUDE[ssde_md](../../includes/ssde_md.md)] 會實際重新排序分葉層級的頁面，使這些頁面符合分葉節點的邏輯順序 (由左至右)，以重組資料表與檢視表上叢集與非叢集索引的分葉層級。 重新組織也會根據索引的填滿因數值來壓縮索引頁面。 若要檢視填滿因數設定，請使用 [sys.indexes](../../relational-databases/system-catalog-views/sys-indexes-transact-sql.md)。 如需語法範例，請參閱[範例：資料列存放區重新組織](../../t-sql/statements/alter-index-transact-sql.md#examples-rowstore-indexes)。

- 使用[資料行存放區索引](columnstore-indexes-overview.md)時，在一段時間內插入、更新及刪除資料之後，差異存放區最後可能會有多個小型資料列群組。 重新組織資料行存放區索引會強制將所有資料列群組存放至資料行存放區，然後將資料列群組合併成具有較多資料列的較少資料列群組。 重新組織作業也會移除已從資料行存放區刪除的資料列。 重新組織一開始會需要額外的 CPU 資源來壓縮資料，這可能會拖慢整體系統效能。 不過，在壓縮完資料後，查詢效能就會改善。 如需語法範例，請參閱[範例：資料行存放區重新組織](../../t-sql/statements/alter-index-transact-sql.md#examples-columnstore-indexes)。

### <a name="rebuild-an-index"></a>重建索引

重建索引會卸除和重新建立索引。 視索引類型和 [!INCLUDE[ssde_md](../../includes/ssde_md.md)] 版本而定，重建作業可能可以在線上或離線完成。 如需 T-SQL 語法，請參閱 [ALTER INDEX REBUILD](../../t-sql/statements/alter-index-transact-sql.md#rebuilding-indexes)

- 針對[資料列存放區](clustered-and-nonclustered-indexes-described.md)索引，重建會移除片段，根據所指定或現有的填滿因數設定壓縮頁面來收回磁碟空間，然後重新排序連續頁面中的索引資料列。 指定 `ALL` 時，會在單一交易中卸除資料表的所有索引，然後加以重建。 不需要事先卸除外部索引鍵條件約束。 當重建含有 128 個或更多範圍的索引時，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 會延遲取消配置實際的頁面，也會延遲其關聯鎖定，直到認可交易之後。 如需語法範例，請參閱[範例：資料列存放區重新組織](../../t-sql/statements/alter-index-transact-sql.md#examples-rowstore-indexes)。

- 針對[資料行存放區](columnstore-indexes-overview.md)索引，重建會移除片段，將所有資料列移至資料行存放區，然後透過將已從資料表上以邏輯方式刪除的資料列實體刪除來回收磁碟空間。 
  
  > [!TIP]
  > 從 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 開始，通常無須重建資料行存放區索引，這是因為 `REORGANIZE` 會在背景以線上作業方式執行必要的重建作業。 
  
  如需語法範例，請參閱[範例：資料行存放區重建](../../t-sql/statements/alter-index-transact-sql.md#examples-columnstore-indexes)。

### <a name="permissions"></a><a name="Permissions"></a> 權限

必須具備資料表或檢視的 `ALTER` 權限。 使用者至少必須是下列角色的成員之一：

- **db_ddladmin** 資料庫角色 <sup>1</sup>
- **db_owner** 資料庫角色
- **sysadmin** 伺服器角色

<sup>1</sup>**db_ddladmin** 資料庫角色具有 [最低權限](/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models)。

### <a name="remove-fragmentation-using-ssmanstudiofull"></a><a name="SSMSProcedureReorg"></a> 使用 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 移除片段

#### <a name="to-reorganize-or-rebuild-an-index"></a>若要重新組織或重建索引

1. 在 [物件總管] 中，展開包含您要重新組織其索引之資料表的資料庫。
2. 展開 **[資料表]** 資料夾。
3. 展開您要重新組織其索引的資料表。
4. 展開 **[索引]** 資料夾。
5. 以滑鼠右鍵按一下您要重新組織的索引，然後選取 **[重新組織]** 。
6. 在 **[重新組織索引]** 對話方塊中，確認 **[要重新組織的索引]** 方格中有正確索引，然後按一下 **[確定]** 。
7. 選取 **[壓縮大型物件資料行資料]** 核取方塊，可指定一併壓縮包含大型物件 (LOB) 資料的所有頁面。
8. 按一下 [確定]。

#### <a name="to-reorganize-all-indexes-in-a-table"></a>若要重新組織資料表中的所有索引

1. 在 [物件總管] 中，展開包含您要重新組織其索引之資料表的資料庫。
2. 展開 **[資料表]** 資料夾。
3. 展開您要重新組織其索引的資料表。
4. 以滑鼠右鍵按一下 **[索引]** 資料夾，並選取 **[全部重新組織]** 。
5. 在 **[重新組織索引]** 對話方塊中，確認 **[要重新組織的索引]** 方格中有正確索引。 若要從 **[要重新組織的索引]** 方格中移除索引，請選取索引，然後按下 DELETE 鍵。
6. 選取 **[壓縮大型物件資料行資料]** 核取方塊，可指定一併壓縮包含大型物件 (LOB) 資料的所有頁面。
7. 按一下 [確定]。

#### <a name="to-rebuild-an-index"></a>若要重建索引

1. 在 [物件總管] 中，展開包含您要重新組織其索引之資料表的資料庫。
2. 展開 **[資料表]** 資料夾。
3. 展開您要重新組織其索引的資料表。
4. 展開 **[索引]** 資料夾。
5. 以滑鼠右鍵按一下您要重新組織的索引，並選取 [重建]。
6. 在 **[重建索引]** 對話方塊中，確認 **[要重建的索引]** 方格中有正確索引，然後按一下 **[確定]** 。
7. 選取 **[壓縮大型物件資料行資料]** 核取方塊，可指定一併壓縮包含大型物件 (LOB) 資料的所有頁面。
8. 按一下 [確定]。

### <a name="remove-fragmentation-using-tsql"></a><a name="TsqlProcedureReorg"></a> 使用 [!INCLUDE[tsql](../../includes/tsql-md.md)] 移除片段

> [!NOTE]
> 如需更多使用 [!INCLUDE[tsql](../../includes/tsql-md.md)] 來重建或重新組織索引的範例，請參閱 [ALTER INDEX 範例：資料行存放區索引](../../t-sql/statements/alter-index-transact-sql.md#examples-columnstore-indexes)與 [ALTER INDEX 範例：資料列存放區索引](../../t-sql/statements/alter-index-transact-sql.md#examples-rowstore-indexes)。

#### <a name="to-reorganize-a-fragmented-index"></a>若要重新組織片段索引

下列範例會重新組織 `AdventureWorks2016` 資料庫中 `HumanResources.Employee` 資料表上的 `IX_Employee_OrganizationalLevel_OrganizationalNode` 索引。

```sql
ALTER INDEX IX_Employee_OrganizationalLevel_OrganizationalNode
    ON HumanResources.Employee
    REORGANIZE;
```

下列範例會重新組織 `AdventureWorksDW2016` 資料庫中 `dbo.FactResellerSalesXL_CCI` 資料表上的 `IndFactResellerSalesXL_CCI` 資料行存放區索引。

```sql
-- This command will force all CLOSED and OPEN rowgroups into the columnstore.
ALTER INDEX IndFactResellerSalesXL_CCI
    ON FactResellerSalesXL_CCI
    REORGANIZE WITH (COMPRESS_ALL_ROW_GROUPS = ON);
```

#### <a name="to-reorganize-all-indexes-in-a-table"></a>若要重新組織資料表中的所有索引

下列範例會重新組織 `AdventureWorks2016` 資料庫中 `HumanResources.Employee` 資料表上的所有索引。

```sql
ALTER INDEX ALL ON HumanResources.Employee
   REORGANIZE;
```

#### <a name="to-rebuild-a-fragmented-index"></a>若要重建片段索引

下列範例會在 `AdventureWorks2016` 資料庫的 `Employee` 資料表上重建單一索引。

[!code-sql[IndexDDL#AlterIndex1](../../relational-databases/indexes/codesnippet/tsql/reorganize-and-rebuild-i_1.sql)]

#### <a name="to-rebuild-all-indexes-in-a-table"></a>若要重建資料表中全部的索引

下列範例會使用 `ALL` 關鍵字來重建與 `AdventureWorks2016` 資料庫中資料表相關聯的所有索引。 指定三個選項。

[!code-sql[IndexDDL#AlterIndex2](../../relational-databases/indexes/codesnippet/tsql/reorganize-and-rebuild-i_2.sql)]

如需詳細資訊，請參閱 [ALTER INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/alter-index-transact-sql.md)。

#### <a name="automatic-index-and-statistics-management"></a>自動索引與統計資料管理

利用[自適性索引重組](https://github.com/Microsoft/tigertoolbox/tree/master/AdaptiveIndexDefrag)等解決方案，為一或多個資料庫自動管理索引重組以及統計資料更新。 這項程序會根據索引分散程度與其他參數，自動選擇要進行重建或是重新組織索引，並以線性閾值更新統計資料。

## <a name="considerations-specific-to-rebuilding-rowstore-indexes"></a>重建資料列存放區索引的特定考量

如果非叢集索引記錄中所包含的實體或邏輯識別碼需要變更，則重建叢集索引將會自動重建任何參考叢集索引鍵的非叢集索引。

下列案例會強制自動重建資料表上的所有資料列存放區非叢集索引：

- 在資料表上建立叢集索引
- 移除使資料表儲存為堆積的叢集索引
- 變更叢集索引鍵以包含或排除資料行

下列案例不需要在資料表上自動重建所有資料列存放區非叢集索引：

- 重建唯一的叢集索引
- 重建非唯一的叢集索引
- 變更索引結構描述，例如將資料分割結構描述套用至叢集索引，或將叢集索引移至不同的檔案群組

> [!IMPORTANT]
> 如果索引所在的檔案群組離線或設為唯讀，便無法重新組織或重建索引。 當指定了 ALL 關鍵字，且有一個或多個索引在離線或唯讀檔案群組中，陳述式會失敗。
>
> 發生索引重建時，實體媒體必須具有足夠空間來儲存兩份索引複本。 重建完成時，[!INCLUDE[ssde_md](../../includes/ssde_md.md)] 會刪除原始索引。

當搭配 `ALTER INDEX` 陳述式指定 `ALL` 時，會重新組織資料表上的叢集與非叢集關聯式索引和 XML 索引。

## <a name="considerations-specific-to-rebuilding-a-columnstore-index"></a>重建資料行存放區索引的特定考量

重建資料行存放區索引時，[!INCLUDE[ssde_md](../../includes/ssde_md.md)] 會從原始資料行存放區索引讀取所有資料，包括差異存放區。 這會將資料合併成新的資料列群組，並將資料列群組壓縮到資料行存放區。 [!INCLUDE[ssde_md](../../includes/ssde_md.md)] 會透過實際刪除已經以邏輯方式從資料表刪除的資料列，以重組資料行存放區。 已刪除的位元組會在磁碟上回收。

> [!NOTE]
> 使用 [!INCLUDE[ssManStudio](../../includes/ssManStudio-md.md)] 來重新組織資料行存放區索引會將 COMPRESSED 資料列群組合併在一起，但不會強制將所有資料列群組壓縮到資料行存放區。 系統會壓縮 CLOSED 資料列群組，但不會將 OPEN 資料列群組壓縮到資料行存放區。 若要強制壓縮所有資料列群組，請使用[下面](#TsqlProcedureReorg)的 [!INCLUDE[tsql](../../includes/tsql-md.md)] 範例。

> [!NOTE]
> 從 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 開始，Tuple Mover 會由背景合併工作協助，該工作會自動壓縮已存在一段時間的較小 OPEN 差異資料列群組 (由內部閾值決定)，或合併已刪除大量資料列的 COMPRESSED 資料列群組。 這可改善一段時間的資料行存放區索引品質。    
> 如需資料行存放區詞彙與概念的詳細資訊，請參閱[資料行存放區索引：概觀](../../relational-databases/indexes/columnstore-indexes-overview.md)。

### <a name="rebuild-a-partition-instead-of-the-entire-table"></a>重建分割區，而非整個資料表

- 如果索引很大，重建整個資料表將花上很長的時間，而且需要足夠的磁碟空間來存放重建期間額外的索引副本。 通常只需要重建最近使用的分割區。
- 對於資料分割資料表，您不需要重建整個資料行存放區索引，因為片段可能只會出現在最近修改過的分割區中。 事實資料表和大型維度資料表通常會進行分割區，以便在資料表區塊上執行備份和管理作業。

### <a name="rebuild-a-partition-after-heavy-dml-operations"></a>在繁重的 DML 作業之後重建分割區

重建分割區將會重組分割區並減少磁碟儲存體。 重建將會刪除資料行存放區中所有標示為要刪除的資料列，並將所有資料列群組從差異存放區移至資料行存放區。 差異存放區中可能存有資料列數量小於 1 百萬個的多個資料列群組。

### <a name="rebuild-a-partition-after-loading-data"></a>載入資料後重建分割區

載入資料後重建分割區，可確保所有資料儲存到資料行存放區中。 當並行處理序同時個別將不到 100,000 個資料列載入相同的分割區時，分割區最後可能會具有多個差異存放區。 重建會將所有差異存放區資料列移至資料行存放區。

## <a name="considerations-specific-to-reorganizing-a-columnstore-index"></a>重新組織資料行存放區索引的特定考量

重新組織資料行存放區索引時，[!INCLUDE[ssde_md](../../includes/ssde_md.md)] 會以壓縮資料列群組的方式，將每個 CLOSED 差異資料列群組壓縮至資料行存放區中。 從 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 開始且在 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 中，`REORGANIZE` 命令會在線上執行以下額外的重組最佳化：

- 當 10% 或更多資料列已經以邏輯方式刪除時，會實際將資料列從資料列群組移除。 已刪除的位元組會在實體媒體上回收。 例如，如果在包含 1 百萬個資料列的壓縮資料列群組中刪除 10 萬個資料列，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 將會移除刪除的資料列，並重新壓縮包含 90 萬個資料列的資料列群組。 將刪除的資料列移除可以節省儲存空間。

- 可合併一或多個壓縮的資料列群組，將每個資料列群組的資料列數目最多提高至 1,048,576 個資料列的上限。 例如，如果您大量匯入 5 個批次的 102,400 個資料列，就會有 5 個壓縮的資料列群組。 如果執行 REORGANIZE，這些資料列群組將會合併成 1 個包含 512,000 個資料列的壓縮資料列群組。 這是假設沒有任何目錄大小或記憶體限制的情況。

- 對於已透過邏輯方式刪除 10% 或更多資料列的資料列群組，[!INCLUDE[ssde_md](../../includes/ssde_md.md)] 會嘗試把這個資料列群組與一或多個資料列群組合併。 例如，資料列群組 1 壓縮了 500,000 個資料列，而資料列群組 21 則壓縮了達到數目上限的 1,048,576 個資料列。 資料列群組 21 中刪除了 60% 的資料列，剩下 409,830 個資料列。 [!INCLUDE[ssde_md](../../includes/ssde_md.md)] 會優先合併這兩個資料列群組，以壓縮成一個包含 909,830 個資料列的新資料列群組。

執行資料負載後，您在差異存放區中可能有多個小型的資料列群組。 您可使用 `ALTER INDEX REORGANIZE` 強制將所有資料列群組存放至資料行存放區，然後再將資料列群組合併成較少資料列的資料列群組。 重新組織作業也會移除已從資料行存放區刪除的資料列。

## <a name="limitations-and-restrictions"></a><a name="Restrictions"></a> 限制事項

具有超過 128 個範圍的資料列存放區索引將以兩個不同的階段重建：邏輯和實體。 在邏輯階段中，索引所使用的現有配置單位將以取消配置標示，並複製和排序資料列，然後移到所建立的新配置單位以儲存重建索引。 在實體階段中，會將先前標示為取消配置的配置單位，在背景以短暫的交易實際卸除，而且不需要許多鎖定。 如需範圍的詳細資訊，請參閱[分頁與範圍架構指南](../../relational-databases/pages-and-extents-architecture-guide.md)。

`ALTER INDEX REORGANIZE` 陳述式需要包含索引的資料檔案具有可用空間，因為作業只能配置在相同檔案上的暫存工作頁面上，而不是在檔案群組內的其他檔案中。 因此，雖然檔案群組可能有可用的分頁，但是使用者仍然可能會出現錯誤 1105：`Could not allocate space for object '###' in database '###' because the '###' filegroup is full. Create disk space by deleting unneeded files, dropping objects in the filegroup, adding additional files to the filegroup, or setting autogrowth on for existing files in the filegroup.`

> [!WARNING]
> 您可以對包含超過 1,000 個分割區的資料表，建立及重建不以資料表為準的索引，但不予支援。 此做法可能會導致在作業期間效能降低或耗用過多記憶體。 Microsoft 建議當分割區數超過 1,000 個時，才使用[對齊的索引](../partitions/partitioned-tables-and-indexes.md#aligned-index)。

如果索引所在的檔案群組 **離線** 或設定為 [唯讀]，便無法重新組織或重建該索引。 當指定 `ALL` 關鍵字，且有一或多個索引在離線或唯讀檔案群組中時，陳述式會失敗。

統計：

- **建立** 或 **重建** 索引之後，即可藉由掃描資料表中所有的資料列來建立或更新統計資料。 不過，從 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 開始，並不會在建立或重建資料分割索引之後，透過掃描資料表中的所有資料列來建立或更新統計資料。 反之，查詢最佳化工具會使用預設的取樣演算法來產生這些統計資料。 如果要在掃描資料表中所有資料列時取得分割區索引的統計資料，請使用 [CREATE STATISTICS](../../t-sql/statements/create-statistics-transact-sql.md) 或 [UPDATE STATISTICS](../../t-sql/statements/update-statistics-transact-sql.md) 搭配 `FULLSCAN` 子句。

- **重新組織** 索引時，不會更新統計資料。

當 `ALLOW_PAGE_LOCKS` 設定為 OFF 時，無法重新組織索引。

在 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 與更舊版本中，重建叢集資料行存放區索引為離線作業。 在進行重建時，資料庫引擎必須取得資料表或分割區上的獨佔鎖定。 在重建期間，資料將會離線且無法使用，即便是使用 `NOLOCK`、讀取認可快照隔離 (RCSI) 或快照隔離也一樣。
從 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 開始，叢集資料行存放區索引可以使用 `ONLINE = ON` 選項來重建。

針對具有已排序叢集資料行存放區索引的 Azure Synapse Analytics (先前稱為 [!INCLUDE[ssSDW](../../includes/sssdw-md.md)]) 資料表，`ALTER INDEX REBUILD` 將會使用 TempDB 將資料重新排序。 在重建作業期間監視 TempDB。 如果您需要更多 TempDB 空間，請擴大資料倉儲。 當索引重建完成之後，請相應減少回來。

針對具有已排序叢集資料行存放區索引的 Azure Synapse Analytics (先前稱為 [!INCLUDE[ssSDW](../../includes/sssdw-md.md)]) 資料表，`ALTER INDEX REORGANIZE` 不會將資料重新排序。 若要重新排序資料，請使用 `ALTER INDEX REBUILD`。

## <a name="using-index-rebuild-to-recover-from-hardware-failures"></a>使用索引重建從硬體故障中復原

在舊版的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中，有時候您可以重建資料列存放區非叢集索引，來更正硬體故障所造成的任何不一致情況。
從 [!INCLUDE[ssKatmai](../../includes/ssKatmai-md.md)] 開始，您仍可能透過離線重建非叢集索引，來修復索引和叢集索引之間的這類不一致情況。 不過，您無法利用線上重建索引的方式來修復非叢集索引不一致情況，因為線上重建機制會以現有非叢集索引作為重建基礎，並因而保存不一致的情況。 離線時重建索引可能會導致強制掃描叢集索引 (或堆積)，因而移除不一致的情況。 請卸除並重新建立非叢集索引，確保會從叢集索引進行重建。 如果要從不一致的情況中復原，在舊版中，我們建議的方法是從備份中還原受影響的資料，不過，您現在可以利用離線重建非叢集索引的方式來修復索引不一致的情況。 如需詳細資訊，請參閱 [DBCC CHECKDB &#40;Transact-SQL&#41;](../../t-sql/database-console-commands/dbcc-checkdb-transact-sql.md)。

## <a name="see-also"></a>另請參閱

- [SQL Server 索引架構和設計指南](../../relational-databases/sql-server-index-design-guide.md)
- [線上執行索引作業](../../relational-databases/indexes/perform-index-operations-online.md)
- [ALTER INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/alter-index-transact-sql.md)
- [自適性索引重組](https://github.com/Microsoft/tigertoolbox/tree/master/AdaptiveIndexDefrag)
- [CREATE STATISTICS &#40;TRANSACT-SQL&#41;](../../t-sql/statements/create-statistics-transact-sql.md)
- [UPDATE STATISTICS &#40;Transact-SQL&#41;](../../t-sql/statements/update-statistics-transact-sql.md)
- [資料行存放區索引效能](../../relational-databases/indexes/columnstore-indexes-query-performance.md)
- [開始使用資料行存放區進行即時作業分析](../../relational-databases/indexes/get-started-with-columnstore-for-real-time-operational-analytics.md)
- [資料倉儲的資料行存放區索引](../../relational-databases/indexes/columnstore-indexes-data-warehouse.md)
- [資料行存放區索引和適用於資料列群組的合併原則](/archive/blogs/sqlserverstorageengine/columnstore-index-merge-policy-for-reorganize)