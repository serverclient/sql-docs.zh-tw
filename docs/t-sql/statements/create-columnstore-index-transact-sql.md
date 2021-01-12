---
description: CREATE COLUMNSTORE INDEX (Transact-SQL)
title: CREATE COLUMNSTORE INDEX (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 09/25/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- CREATE_COLUMNSTORE_INDEX_TSQL
- COLUMNSTORE INDEX
- COLUMNSTORE_INDEX_TSQL
- CREATE CLUSTERED COLUMNSTORE INDEX
- COLUMNSTORE_TSQL
- CREATE NONCLUSTERED COLUMNSTORE INDEX
- CREATE_NONCLUSTERED_COLUMNSTORE_INDEX_TSQL
- CREATE COLUMNSTORE INDEX
- CREATE_CLUSTERED_COLUMNSTORE_INDEX_TSQL
- COLUMNSTORE
dev_langs:
- TSQL
helpviewer_keywords:
- index creation [SQL Server], columnstore indexes
- columnstore index, creating
- CREATE COLUMNSTORE INDEX statement
- CREATE INDEX statement
ms.assetid: 7e1793b3-5383-4e3d-8cef-027c0c8cb5b1
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: b650e0fa1d1d6c02e657d3d5860908164ed5cd91
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98099554"
---
# <a name="create-columnstore-index-transact-sql"></a>CREATE COLUMNSTORE INDEX (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

將資料列存放區資料表轉換為叢集資料行存放區索引或建立一個非叢集資料行存放區索引。 有效率地對 OLTP 工作負載執行即時作業分析，或是改善資料倉儲工作負載的資料壓縮和查詢效能，請使用資料行存放區索引。  
  
> [!NOTE]
> 從 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 開始，您可以建立資料表作為叢集資料行存放區索引。   您再也不用先建立資料列存放區資料表，然後將它轉換成叢集資料行存放區索引。  

> [!TIP]
> 如需索引設計指導方針的詳細資訊，請參閱 [SQL Server 索引設計指南](../../relational-databases/sql-server-index-design-guide.md)。

跳到範例：  
-   [將資料列存放區資料表轉換為資料行存放區範例](../../t-sql/statements/create-columnstore-index-transact-sql.md#convert)  
-   [非叢集資料行存放區索引範例](../../t-sql/statements/create-columnstore-index-transact-sql.md#nonclustered)  
  
移至案例：  
-   [即時作業分析的資料行存放區索引](../../relational-databases/indexes/get-started-with-columnstore-for-real-time-operational-analytics.md)  
-   [資料倉儲的資料行存放區索引](../../relational-databases/indexes/columnstore-indexes-data-warehouse.md)  
  
深入了解：  
-   [資料行存放區索引指南](../../relational-databases/indexes/columnstore-indexes-overview.md)  
-   [資料行存放區索引功能摘要](../../relational-databases/indexes/columnstore-indexes-what-s-new.md)  
  
![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
-- Syntax for SQL Server and Azure SQL Database  
  
-- Create a clustered columnstore index on disk-based table.  
CREATE CLUSTERED COLUMNSTORE INDEX index_name  
    ON { database_name.schema_name.table_name | schema_name.table_name | table_name }  
    [ WITH ( < with_option> [ ,...n ] ) ]  
    [ ON <on_option> ] 
[ ; ]  
  
--Create a nonclustered columnstore index on a disk-based table.  
CREATE [NONCLUSTERED]  COLUMNSTORE INDEX index_name   
    ON { database_name.schema_name.table_name | schema_name.table_name | table_name }
        ( column  [ ,...n ] )  
    [ WHERE <filter_expression> [ AND <filter_expression> ] ]
    [ WITH ( < with_option> [ ,...n ] ) ]  
    [ ON <on_option> ]
[ ; ]  
  
<with_option> ::=  
      DROP_EXISTING = { ON | OFF } -- default is OFF  
    | MAXDOP = max_degree_of_parallelism 
    | ONLINE = { ON | OFF } 
    | COMPRESSION_DELAY  = { 0 | delay [ Minutes ] }  
    | DATA_COMPRESSION = { COLUMNSTORE | COLUMNSTORE_ARCHIVE }  
      [ ON PARTITIONS ( { partition_number_expression | range } [ ,...n ] ) ]  
  
<on_option>::=  
      partition_scheme_name ( column_name )
    | filegroup_name
    | "default"
  
<filter_expression> ::=  
      column_name IN ( constant [ ,...n ]  
    | column_name { IS | IS NOT | = | <> | != | > | >= | !> | < | <= | !< } constant  
  
```  
  
```syntaxsql
-- Syntax for Azure Synapse Analytics and Parallel Data Warehouse  
  
CREATE CLUSTERED COLUMNSTORE INDEX index_name
    ON { database_name.schema_name.table_name | schema_name.table_name | table_name } 
    [ORDER (column [,...n] ) ]  
    [ WITH ( DROP_EXISTING = { ON | OFF } ) ] --default is OFF  
[;]  

```
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數

部分選項無法在所有資料庫引擎版本上使用。 下表說明當在 CLUSTERED COLUMNSTORE 和 NONCLUSTERED COLUMNSTORE 索引中引入該選項時，所顯示的版本：

|選項| CLUSTERED | NONCLUSTERED |
|---|---|---|
| COMPRESSION_DELAY | [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] | [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] |
| DATA_COMPRESSION | [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] | [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] | 
| ONLINE | [!INCLUDE[ssSQLv15_md](../../includes/sssqlv15-md.md)] | [!INCLUDE[ssSQLv14_md](../../includes/sssqlv14-md.md)] |
| WHERE 子句 | N/A | [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] |

所有選項皆可在 Azure SQL Database 中使用。

### <a name="create-clustered-columnstore-index"></a>CREATE CLUSTERED COLUMNSTORE INDEX

建立叢集資料行存放區索引，由資料行將所有資料壓縮並儲存。 索引會包括資料表中的所有資料行，而且將儲存整個資料表。 如果現有的資料表是堆積或叢集索引，則該資料表會轉換成叢集資料行存放區索引。 如果資料表已儲存為叢集資料行存放區索引，將卸除並重建現有的索引。  
  
*index_name*  
指定新索引的名稱。  
  
如果資料表已經有叢集資料行存放區索引，則您可以指定與現有索引相同的名稱，或您可以使用 DROP EXISTING 選項來指定新名稱。  
  
ON [*database_name*. [*schema_name* ] . | *schema_name* . ] *table_name*

指定要儲存為叢集資料行存放區索引之資料表的單部分、兩部分或三部分名稱。 如果資料表是堆積或叢集索引，則該資料表會從資料列存放區轉換成資料行存放區。 如果資料表已經是資料行存放區，此陳述式會重建叢集資料行存放區索引。 若要轉換成已排序的叢集資料行存放區索引，現有的索引必須是叢集資料行存放區索引。
  
#### <a name="with-options"></a>WITH 選項

##### <a name="drop_existing--off--on"></a>DROP_EXISTING = [OFF] | ON

   `DROP_EXISTING = ON` 指定要卸除現有的索引，並建立新的資料行存放區索引。  
```sql
CREATE CLUSTERED COLUMNSTORE INDEX cci ON Sales.OrderLines
       WITH (DROP_EXISTING = ON);
```
   預設值為 DROP_EXISTING = OFF，表示 索引名稱要與現有名稱相同。 如果指定的索引名稱已存在，就會發生錯誤。  
  
##### <a name="maxdop--max_degree_of_parallelism"></a>MAXDOP = *max_degree_of_parallelism*  
   在索引作業期間，覆寫現有的「平行處理原則的最大程度」伺服器組態。 請利用 MAXDOP 來限制執行平行計畫所用的處理器數目。 最大值是 64 個處理器。  
  
   *max_degree_of_parallelism* 值可以是：  
   - 1 - 隱藏平行計畫的產生。  
   - \>1 - 根據目前的系統工作負載，將平行索引作業所使用的處理器數目上限，限制為所指定的數目或更少的數目。 例如，當 MAXDOP = 4，使用的處理器數目將會等於或小於 4。  
   - 0 (預設值) - 根據目前的系統工作負載來使用實際數目的處理器或比實際數目更少的處理器。  
  
```sql
CREATE CLUSTERED COLUMNSTORE INDEX cci ON Sales.OrderLines
       WITH (MAXDOP = 2);
```

   如需詳細資訊，請參閱[設定平行處理原則的最大程度伺服器組態選項](../../database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option.md)和[設定平行索引作業](../../relational-databases/indexes/configure-parallel-index-operations.md)。  
 
###### <a name="compression_delay--0--delay--minutes-"></a>COMPRESSION_DELAY = **0** | *delay* [ Minutes ]  
   至於磁碟資料表，「延遲」  會指定關閉狀態下的差異資料列群組，必須在差異資料列群組中至少保留多少分鐘的時間，然後 SQL Server 才能將它壓縮到壓縮的資料列群組。 因為磁碟資料表不會追蹤個別資料列的插入和更新時間，因此 SQL Server 會將這段延遲時間套用於關閉狀態下的差異資料列群組。  
   預設值是 0 分鐘。  
   
```sql
CREATE CLUSTERED COLUMNSTORE INDEX cci ON Sales.OrderLines
       WITH ( COMPRESSION_DELAY = 10 Minutes );
```

   如需 COMPRESSION_DELAY 的使用時機建議，請參閱[開始使用資料行存放區進行即時作業分析](../../relational-databases/indexes/get-started-with-columnstore-for-real-time-operational-analytics.md)。  
  
##### <a name="data_compression--columnstore--columnstore_archive"></a>DATA_COMPRESSION = COLUMNSTORE | COLUMNSTORE_ARCHIVE  
   針對指定的資料表、分割區編號或分割區範圍指定資料壓縮選項。 選項如下：   
- `COLUMNSTORE` 是預設，並指定要利用最高效能的資料行存放區壓縮方式來壓縮。 這是典型的選擇。  
- `COLUMNSTORE_ARCHIVE` 會進一步將資料表或分割區壓縮成較小的大小。 某些封存需要的儲存空間較小而且允許較長儲存和擷取時間，此時可以使用這個選項。  
  
```sql
CREATE CLUSTERED COLUMNSTORE INDEX cci ON Sales.OrderLines
       WITH ( DATA_COMPRESSION = COLUMNSTORE_ARCHIVE );
```
   如需與壓縮有關的詳細資訊，請參閱[資料壓縮](../../relational-databases/data-compression/data-compression.md)。  

###### <a name="online--on--off"></a>ONLINE = [ON | OFF]
- `ON` 指定當建置索引的新複本時，資料行存放區索引會保持連線並可供使用。
- `OFF` 指定當建置新複本時，索引不可供使用。

```sql
CREATE CLUSTERED COLUMNSTORE INDEX cci ON Sales.OrderLines
       WITH ( ONLINE = ON );
```

#### <a name="on-options"></a>ON 選項 
   使用 ON 選項可讓您指定資料存放區選項，例如資料分割配置、特定的檔案群組或預設檔案群組。 如果未指定 ON 選項，索引會使用現有資料表的設定分割區或檔案群組設定。  
  
   *partition_scheme_name* **(** _column_name_ **)**  
   指定資料表的資料分割配置。 資料分割配置必須已存在於資料庫中。 若要建立資料分割配置，請參閱[CREATE PARTITION SCHEME](../../t-sql/statements/create-partition-scheme-transact-sql.md)。  
 
   *column_name* 會指定分割區索引進行分割所依據的資料行。 此資料行必須符合 *partition_scheme_name* 所使用資料分割函數引數的資料類型、長度與有效位數。  

   *filegroup_name*  
   指定用以儲存叢集資料行存放區索引的檔案群組。 如果未指定位置，且資料表未分割，則索引會使用與基礎資料表或檢視相同的檔案群組。 此檔案群組必須已存在。  

   **"** default **"**  
   若要在預設的檔案群組上建立索引，請使用 "default" 或 [ default ]。  
  
   如果指定了 "default"，目前工作階段的 QUOTED_IDENTIFIER 選項就必須是 ON。 QUOTED_IDENTIFIER 的預設值是 ON。 如需詳細資訊，請參閱 [SET QUOTED_IDENTIFIER &#40;Transact-SQL&#41;](../../t-sql/statements/set-quoted-identifier-transact-sql.md)。  
  
### <a name="create-nonclustered-columnstore-index"></a>CREATE [NONCLUSTERED] COLUMNSTORE INDEX  
在資料列存放區資料表上建立記憶體內非叢集資料行存放區索引，並儲存為堆積或叢集索引。 索引可以有一個篩選的條件而且不需要包含基礎資料表的所有資料行。 資料行存放區索引需要足夠的空間來儲存資料複本。 它是可更新的，而且當基礎資料表變更時，就會隨之更新。 叢集索引的非叢集資料行存放區索引可以進行即時分析。  
  
*index_name*  
   指定索引的名稱。 *index_name* 在資料表中必須是唯一的，但在資料庫中不需要是唯一的。 索引名稱必須遵照[識別碼](../../relational-databases/databases/database-identifiers.md)的規則。  
  
 **(** _column_  [ **,** ...*n* ] **)**  
    指定要存放的資料行。 非叢集資料行存放區索引僅限 1024 個資料行。  
   每個資料行必須是資料行存放區索引支援的資料類型。 如需支援的資料類型清單，請參閱[限制](../../t-sql/statements/create-columnstore-index-transact-sql.md#LimitRest)。  

ON [*database_name*. [*schema_name* ] . | *schema_name* . ] *table_name*  
   指定包含索引之資料表的一部分、兩部分或三部分名稱。  

#### <a name="with-options"></a>WITH 選項
##### <a name="drop_existing--off--on"></a>DROP_EXISTING = [OFF] | ON  
   DROP_EXISTING = ON 卸除及重建現有的索引。 所指定的索引名稱必須與目前現有的索引相同；不過，索引定義可以修改。 例如，您可以指定不同的資料行或索引選項。
  
   DROP_EXISTING = OFF 如果指定的索引名稱已存在，就會顯示錯誤訊息。 您無法利用 DROP_EXISTING 來變更索引類型。 在與舊版本相容的語法中，WITH DROP_EXISTING 相當於 WITH DROP_EXISTING = ON。  

###### <a name="maxdop--max_degree_of_parallelism"></a>MAXDOP = *max_degree_of_parallelism*  
   針對索引作業期間，覆寫[設定平行處理原則的最大程度伺服器組態選項](../../database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option.md)組態選項。 請利用 MAXDOP 來限制執行平行計畫所用的處理器數目。 最大值是 64 個處理器。  
  
   *max_degree_of_parallelism* 值可以是：  
   - 1 - 隱藏平行計畫的產生。  
   - \>1 - 根據目前的系統工作負載，將平行索引作業所使用的處理器數目上限，限制為所指定的數目或更少的數目。 例如，當 MAXDOP = 4，使用的處理器數目將會等於或小於 4。  
   - 0 (預設值) - 根據目前的系統工作負載來使用實際數目的處理器或比實際數目更少的處理器。  
  
   如需詳細資訊，請參閱 [設定平行索引作業](../../relational-databases/indexes/configure-parallel-index-operations.md)。  
  
> [!NOTE]
>  [!INCLUDE[msC](../../includes/msconame-md.md)][!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的每個版本都無法使用平行索引作業。 如需 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 版本支援的功能清單，請參閱 [SQL Server 2016 版本和支援的功能](../../sql-server/editions-and-components-of-sql-server-2016.md)。  
  
###### <a name="online--on--off"></a>ONLINE = [ON | OFF]   
- `ON` 指定當建置索引的新複本時，資料行存放區索引會保持連線並可供使用。
- `OFF` 指定當建置新複本時，索引不可供使用。 在非叢集索引中，基底資料表仍可供使用，在新的索引建置完畢前，只有非叢集資料行存放區索引不會用來供查詢使用。 

```sql
CREATE COLUMNSTORE INDEX ncci ON Sales.OrderLines (StockItemID, Quantity, UnitPrice, TaxRate) WITH ( ONLINE = ON );
```

##### <a name="compression_delay--0--delayminutes"></a>COMPRESSION_DELAY = **0** | \<delay>[Minutes]  
   指定資料列應該保留在差異資料列群組中的時間下限，當過了這段時間後，資料列才能遷移到壓縮的資料列群組。 例如，客戶可以指定資料列維持不變 120 分鐘後，就可以把它壓縮成單欄式儲存體格式。 至於磁碟資料表上的資料行存放區索引，我們不會追蹤資料列插入或更新的時間，我們使用差異資料列群組關閉時間來代理資料列。 預設持續時間是 0 分鐘。 當差異資料列群組累積了 1 百萬個資料列並標示為已關閉，就會將資料列移轉至單欄式儲存體。  
  
###### <a name="data_compression"></a>DATA_COMPRESSION  
   針對指定的資料表、分割區編號或分割區範圍指定資料壓縮選項。 只適用於資料行存放區索引，包括非叢集資料行存放區索引和叢集資料行存放區索引。 選項如下：
   
- `COLUMNSTORE` - 這是預設，並指定要利用最高效能的資料行存放區壓縮方式來壓縮。 這是典型的選擇。  
- `COLUMNSTORE_ARCHIVE` - COLUMNSTORE_ARCHIVE 會進一步將資料表或分割區壓縮成較小的大小。 這可用於封存，或是其他需要較小儲存體，而且可負擔更多時間來儲存和擷取的狀況。  
  
 如需與壓縮有關的詳細資訊，請參閱[資料壓縮](../../relational-databases/data-compression/data-compression.md)。  
  
##### <a name="where-filter_expression--and-filter_expression-"></a>WHERE \<filter_expression> [ AND \<filter_expression> ]
  
   這稱為篩選述詞，可指定要在索引中包含的資料列。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會在篩選之索引的資料列上，建立已篩選的統計資料。  
  
   這個篩選述詞使用簡單的比較邏輯。 比較運算子不允許使用 NULL 常值的比較。 請改用 IS NULL 和 IS NOT NULL 運算子。  
  
   下面是一些 `Production.BillOfMaterials` 資料表之篩選述詞的範例：  
   `WHERE StartDate > '20000101' AND EndDate <= '20000630'`    
   `WHERE ComponentID IN (533, 324, 753)`  
   `WHERE StartDate IN ('20000404', '20000905') AND EndDate IS NOT NULL`  
   
   如需篩選的索引指引，請參閱[建立篩選的索引](../../relational-databases/indexes/create-filtered-indexes.md)。  
  
#### <a name="on-options"></a>ON 選項  
   這些選項會指定將在哪些檔案群組建立索引。  
  
*partition_scheme_name* **(** _column_name_ **)**  
   指定資料分割配置來定義檔案群組，以便對應分割索引的分割區。 我們可以執行 [CREATE PARTITION SCHEME](../../t-sql/statements/create-partition-scheme-transact-sql.md)，這樣資料庫中就一定會有分割區配置。 
   *column_name* 會指定分割區索引進行分割所依據的資料行。 此資料行必須符合 *partition_scheme_name* 所使用資料分割函數引數的資料類型、長度與有效位數。 *column_name* 不限定為索引定義中的資料行。 對資料行存放區索引進行分割時，如果未指定分割區資料行，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 會將它新增為索引的資料行。  
   如果未指定 *partition_scheme_name* 或 *filegroup*，且已分割資料表，則會使用相同的分割資料行，將索引放在與基礎資料表相同的資料分割配置中。  
   分割資料表的資料行存放區索引必須保持分割區對齊。  
   如需分割索引的詳細資訊，請參閱[資料分割資料表與索引](../../relational-databases/partitions/partitioned-tables-and-indexes.md)。  

*filegroup_name*  
   指定索引建立所在的檔案群組名稱。 如果未指定 *filegroup_name* 且資料表未分割，則索引會使用與基礎資料表相同的檔案群組。 此檔案群組必須已存在。  
 
**"** default **"**  
在預設的檔案群組上建立指定的索引。  
  
在這個內容中，default 這個字不是關鍵字。 它是預設檔案群組的識別碼，必須加以分隔，如 ON **"** default **"** 或 ON **[** default **]** 。 如果指定了 "default"，目前工作階段的 QUOTED_IDENTIFIER 選項就必須是 ON。 這是預設值。 如需詳細資訊，請參閱 [SET QUOTED_IDENTIFIER &#40;Transact-SQL&#41;](../../t-sql/statements/set-quoted-identifier-transact-sql.md)。  
  
##  <a name="permissions"></a><a name="Permissions"></a> 權限  
 需要資料表的 ALTER 權限。  
  
##  <a name="general-remarks"></a><a name="GenRemarks"></a> 一般備註  
資料行存放區索引可以在暫存資料表上建立。 當資料表卸除或工作階段結束時，也會卸除索引。  

已排序的叢集資料行存放區索引可以建立在 [!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] 支援的任何資料類型的資料行上，但不包括字串資料行。  
 
## <a name="filtered-indexes"></a>篩選的索引  
已篩選的索引是最佳化的非叢集索引，適用於從資料表選取小型資料列百分比的查詢使用。 它會使用篩選述詞，針對資料表中的部分資料建立索引。 設計良好的已篩選索引可以提升查詢效能、降低儲存成本，並減少維護成本。  
  
### <a name="required-set-options-for-filtered-indexes"></a>需要已篩選之索引的 SET 選項  
每當發生下列任何一個狀況時，都需要必要值資料行中的 SET 選項：  
- 建立已篩選的索引。  
- INSERT、UPDATE、DELETE 或 MERGE 作業修改已篩選之索引中的資料。  
- 查詢最佳化工具會利用篩選索引來產生查詢計劃。  
  
    |Set 選項|必要值|預設伺服器值|預設<br /><br /> OLE DB 與 ODBC 值|預設<br /><br /> DB-Library 值|  
    |-----------------|--------------------|--------------------------|---------------------------------------|-----------------------------------|  
    |ANSI_NULLS|開啟|開啟|開啟|OFF|  
    |ANSI_PADDING|開啟|開啟|開啟|OFF|  
    |ANSI_WARNINGS*|開啟|開啟|開啟|OFF|  
    |ARITHABORT|開啟|開啟|OFF|OFF|  
    |CONCAT_NULL_YIELDS_NULL|開啟|開啟|開啟|OFF|  
    |NUMERIC_ROUNDABORT|OFF|OFF|OFF|OFF|  
    |QUOTED_IDENTIFIER|開啟|開啟|開啟|OFF|   
  
     *當資料庫的相容性層級設定為 90 或更高層級時，將 ANSI_WARNINGS 設定為 ON 也會將 ARITHABORT 隱含設定為 ON。 如果資料庫的相容性層級設定為 80 或更低，ARITHABORT 選項就必須明確地設定為 ON。  
  
 當 SET 選項不正確時，可能會發生下列狀況：  
  
-   不會建立已篩選的索引。  
  
-   [!INCLUDE[ssDE](../../includes/ssde-md.md)] 會產生錯誤，並回復可變更索引中資料的 INSERT、UPDATE、DELETE 或 MERGE 陳述式。  
  
-   查詢最佳化工具不會針對任何 Transact-SQL 陳述式考量執行計畫中的索引。  
  
 如需篩選索引的詳細資訊，請參閱[建立篩選的索引](../../relational-databases/indexes/create-filtered-indexes.md)。 
  
##  <a name="limitations-and-restrictions"></a><a name="LimitRest"></a> 限制事項  

**資料行存放區索引中的每個資料行都必須是下列其中一種一般商務資料類型：** 
-   datetimeoffset [ ( *n* ) ]  
-   datetime2 [ ( *n* ) ]  
-   Datetime  
-   smalldatetime  
-   date  
-   time [ ( *n* ) ]  
-   float [ ( *n* ) ]  
-   real [ ( *n* ) ]  
-   decimal [ ( *precision* [ *, scale* ] **)** ]
-   numeric [ ( *precision* [ *, scale* ] **)** ]    
-   money  
-   SMALLMONEY  
-   BIGINT  
-   int  
-   SMALLINT  
-   TINYINT  
-   bit  
-   nvarchar [ ( *n* ) ] 
-   nvarchar(max)  (僅適用於 [!INCLUDE[ssSQLv14_md](../../includes/sssqlv14-md.md)] 和進階層、標準層 (S3 和以上)，以及所有 VCore 供應項目層的叢集資料行存放區索引)   
-   nchar [ ( *n* ) ]  
-   varchar [ ( *n* ) ]  
-   varchar(max)  (僅適用於 [!INCLUDE[ssSQLv14_md](../../includes/sssqlv14-md.md)] 和進階層、標準層 (S3 和以上)，以及所有 VCore 供應項目層的叢集資料行存放區索引)
-   char [ ( *n* ) ]  
-   varbinary [ ( *n* ) ] 
-   varbinary (max)  (僅適用於 [!INCLUDE[ssSQLv14_md](../../includes/sssqlv14-md.md)] 和進階層的 Azure SQL Database、標準層 (S3 和以上)，以及所有 VCore 供應項目層的叢集資料行存放區索引)
-   binary [ ( *n* ) ]  
-   uniqueidentifier (適用於 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 和更新的版本)
  
如果基礎資料表有一個資料行不屬於資料行存放區索引支援的資料類型，該資料行必須從資料行存放區索引剔除。  
  
**使用任何下列資料類型的資料行不可加入資料行存放區索引：**
-   ntext、text 和 image  
-   nvarchar(max), varchar(max), and varbinary(max) (適用於 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 和先前版本，以及非叢集資料行存放區索引) 
-   rowversion (和 timestamp)  
-   sql_variant  
-   CLR 類型 (hierarchyid 和空間類型)  
-   Xml  
-   uniqueidentifier (適用於 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)])  

**非叢集資料行存放區索引：**
-   不能有 1024 個以上的資料行。
-   無法建立為以條件約束為基礎的索引。 在具有叢集資料行存放區索引的資料表上，可能有唯一條件約束、主索引鍵條件約束或外部索引鍵條件約束。 條件約束一律會使用資料列存放區索引強制實施。 無法使用資料行存放區 (叢集或非叢集) 索引強制實執條件約束。
-   不能包含疏鬆資料行。  
-   無法使用 **ALTER INDEX** 陳述式加以變更。 若要變更非叢集索引，您必須先卸除再重新建立資料行存放區索引。 您可以使用 **ALTER INDEX** 來停用並重建資料行存放區索引。  
-   無法使用 **INCLUDE** 關鍵字來建立。  
-   不可包含 **ASC** 或 **DESC** 關鍵字來排序索引。 資料行存放區索引是依據壓縮演算法來排序。 遞增或遞減排序會取消許多效能優點。  
-   無法在非叢集資料行存放區索引中包含類型 nvarchar(max)、varchar(max), 和 varbinary(max) 的大型物件 (LOB) 資料行。 僅叢集資料行存放區索引支援 LOB 類型，開始於 [!INCLUDE[ssSQLv14_md](../../includes/sssqlv14-md.md)] 版和設定於進階層的 Azure SQL Database、標準層 (S3 和以上)，以及所有 VCore 供應項目層。 請注意，先前的版本不支援叢集和非叢集資料行存放區索引中的 LOB 類型。


> [!NOTE]  
> 從 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 開始，您可以在索引檢視表上建立非叢集資料行存放區索引。  


 **資料行存放區索引無法與下列功能結合：**  
-   計算資料行。 從 SQL Server 2017 開始，叢集資料行存放區索引可以包含非保存的計算資料行。 不過，在 SQL Server 2017，叢集資料行存放區索引不能包含保存的計算資料行，而且您不能在計算資料行上建立非叢集索引。 
-   頁面和資料列壓縮，以及 **vardecimal** 儲存格式 (資料行存放區索引已使用不同格式壓縮)。  
-   複寫  
-   檔案資料流

您無法在具有叢集資料行存放區索引的資料表上使用資料指標或觸發程序。 此限制不適用於非叢集資料行存放區索引。您可以在具有非叢集資料行存放區索引的資料表上使用資料指標和觸發程序。

**[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 特定的限制**  
這些限制僅套用到 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)]。 在此版本中，我們引進了可更新的叢集資料行存放區索引。 非叢集資料行存放區索引仍然是唯讀的。  

-   變更追蹤。 您無法搭配資料行存放區索引使用變更追蹤。  
-   異動資料擷取。 無法在具有叢集資料行存放區索引的資料表上啟用變更資料擷取。 從 SQL Server 2016 開始，您可以在具有非叢集資料行存放區索引的資料表上啟用此功能。  
-   可讀取的次要複本。 您無法從 AlwaysOn 可用性群組的可讀取次要複本來存取叢集資料行存放區索引 (CCI)。  您可以從可讀取的次要複本來存取非叢集資料行存放區索引 (NCCI)。  
-   Multiple Active Result Sets (MARS)。 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 使用 MARS 來與具有資料行存放區索引的資料表進行唯讀連線。 不過，[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 不支援 MARS 在具備資料行存放區索引的資料表上，進行並行資料操作語言 (DML) 作業。 發生這種情況時，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會終止連線並中止交易。  
-  無法在檢視表或索引檢視表上建立非叢集資料行存放區索引。
  
 如需有關資料行存放區索引之效能優點和限制的詳細資訊，請參閱[資料行存放區索引概觀](../../relational-databases/indexes/columnstore-indexes-overview.md)。
  
##  <a name="metadata"></a><a name="Metadata"></a> 中繼資料  
 資料行存放區索引中的所有資料行都將儲存於中繼資料內成為內含資料行。 資料行存放區索引沒有索引鍵資料行。 這些系統檢視表提供有關資料行存放區索引的資訊。  
  
-   [sys.indexes &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-indexes-transact-sql.md)  
-   [sys.index_columns &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-index-columns-transact-sql.md)  
-   [sys.partitions &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-partitions-transact-sql.md)  
-   [sys.column_store_segments &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-column-store-segments-transact-sql.md)  
-   [sys.column_store_dictionaries &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-column-store-dictionaries-transact-sql.md)  
-   [sys.column_store_row_groups &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-column-store-row-groups-transact-sql.md)  

##  <a name="examples-for-converting-a-rowstore-table-to-columnstore"></a><a name="convert"></a> 將資料列存放區資料表轉換為資料行存放區的範例  
  
### <a name="a-convert-a-heap-to-a-clustered-columnstore-index"></a>A. 將堆積轉換成叢集資料行存放區索引  
 此範例會建立資料表做為堆積，然後將它轉換成名為 cci_Simple 的叢集資料行存放區索引。 這會將整個資料表的儲存體從資料列存放區變更為資料行存放區。  
  
```sql  
CREATE TABLE SimpleTable(  
    ProductKey [INT] NOT NULL,   
    OrderDateKey [INT] NOT NULL,   
    DueDateKey [INT] NOT NULL,   
    ShipDateKey [INT] NOT NULL);  
GO  
CREATE CLUSTERED COLUMNSTORE INDEX cci_Simple ON SimpleTable;  
GO  
```  
  
### <a name="b-convert-a-clustered-index-to-a-clustered-columnstore-index-with-the-same-name"></a>B. 將叢集索引轉換成具有相同名稱的叢集資料行存放區索引。  
 此範例會建立一個具有叢集索引的資料表，然後示範將叢集索引轉換成叢集資料行存放區索引的語法。 這會將整個資料表的儲存體從資料列存放區變更為資料行存放區。  
  
```sql  
CREATE TABLE SimpleTable (  
    ProductKey [INT] NOT NULL,   
    OrderDateKey [INT] NOT NULL,   
    DueDateKey [INT] NOT NULL,   
    ShipDateKey [INT] NOT NULL);  
GO  
CREATE CLUSTERED INDEX cl_simple ON SimpleTable (ProductKey);  
GO  
CREATE CLUSTERED COLUMNSTORE INDEX cl_simple ON SimpleTable  
WITH (DROP_EXISTING = ON);  
GO  
```  
  
### <a name="c-handle-nonclustered-indexes-when-converting-a-rowstore-table-to-a-columnstore-index"></a>C. 將資料列存放區資料表轉換成資料行存放區索引時，處理非叢集索引。  
 這個範例顯示將資料列存放區資料表轉換成資料行存放區索引時，如何處理非叢集索引。 實際上，從 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 開始，不需要任何特殊的動作；[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會針對新的叢集資料行存放區索引，自動定義並重建非叢集索引。  
  
 如果您想要卸除非叢集索引，可在建立資料行存放區索引之前，先使用 DROP INDEX 陳述式。 DROP EXISTING 選項只會卸除目前轉換中的叢集索引。 它不會卸除非叢集索引。  
  
 在 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 和 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 中，您無法在資料行存放區索引上建立非叢集索引。 這個範例會示範使用舊的版本時，如何在建立資料行存放區索引之前，先卸除非叢集索引。  
  
```sql  
--Create the table for use with this example.  
CREATE TABLE SimpleTable (  
    ProductKey [INT] NOT NULL,   
    OrderDateKey [INT] NOT NULL,   
    DueDateKey [INT] NOT NULL,   
    ShipDateKey [INT] NOT NULL);  
GO  
  
--Create two nonclustered indexes for use with this example  
CREATE INDEX nc1_simple ON SimpleTable (OrderDateKey);  
CREATE INDEX nc2_simple ON SimpleTable (DueDateKey);   
GO  
  
--SQL Server 2012 and SQL Server 2014: you need to drop the nonclustered indexes  
--in order to create the columnstore index.   
  
DROP INDEX SimpleTable.nc1_simple;  
DROP INDEX SimpleTable.nc2_simple;  
  
--Convert the rowstore table to a columnstore index.  
CREATE CLUSTERED COLUMNSTORE INDEX cci_simple ON SimpleTable;   
GO  
```  
  
### <a name="d-convert-a-large-fact-table-from-rowstore-to-columnstore"></a>D. 將大型事實資料表從資料列存放區轉換成資料行存放區  
 此範例說明如何將大型事實資料表從資料列存放區資料表轉換成資料行存放區資料表。  
  
 若要將資料列存放區資料表轉換成資料行存放區資料表。  
  
1.  首先，建立一個小型資料表以供此範例使用。  
  
    ```sql  
    --Create a rowstore table with a clustered index and a nonclustered index.  
    CREATE TABLE MyFactTable (  
        ProductKey [INT] NOT NULL,  
        OrderDateKey [INT] NOT NULL,  
         DueDateKey [INT] NOT NULL,  
         ShipDateKey [INT] NOT NULL )  
    )  
    WITH (  
        CLUSTERED INDEX ( ProductKey )  
    );  
  
    --Add a nonclustered index.  
    CREATE INDEX my_index ON MyFactTable ( ProductKey, OrderDateKey );  
    ```  
  
2.  卸除資料列存放區資料表中所有的非叢集索引。  
  
    ```sql  
    --Drop all nonclustered indexes  
    DROP INDEX my_index ON MyFactTable;  
    ```  
  
3.  卸除叢集索引。  
  
    -   只有在您將索引轉換成叢集資料行存放區索引的過程中，想要為索引指定新名稱時才這樣做。 如果您未卸除叢集索引，新的叢集資料行存放區索引就會有相同的名稱。  
  
        > [!NOTE]  
        > 如果您使用自己的索引名稱，可能較容易記住。 所有資料列存放區叢集索引都會使用預設名稱，即 'ClusteredIndex_\<GUID>'。  
  
    ```sql  
    --Process for dropping a clustered index.  
    --First, look up the name of the clustered rowstore index.  
    --Clustered rowstore indexes always use the DEFAULT name 'ClusteredIndex_<GUID>'.  
    SELECT i.name   
    FROM sys.indexes i   
    JOIN sys.tables t  
    ON ( i.type_desc = 'CLUSTERED' ) WHERE t.name = 'MyFactTable';  
  
    --Drop the clustered rowstore index.  
    DROP INDEX ClusteredIndex_d473567f7ea04d7aafcac5364c241e09 ON MyDimTable;  
    ```  
  
4.  將資料列存放區資料表轉換成具有叢集資料行存放區索引的資料行存放區資料表。  
  
    ```sql  
    --Option 1: Convert to columnstore and name the new clustered columnstore index MyCCI.  
    CREATE CLUSTERED COLUMNSTORE INDEX MyCCI ON MyFactTable;  
  
    --Option 2: Convert to columnstore and use the rowstore clustered   
    --index name for the columnstore clustered index name.  
    --First, look up the name of the clustered rowstore index.  
    SELECT i.name   
    FROM sys.indexes i  
    JOIN sys.tables t   
    ON ( i.type_desc = 'CLUSTERED' )  
    WHERE t.name = 'MyFactTable';  
  
    --Second, create the clustered columnstore index and   
    --Replace ClusteredIndex_d473567f7ea04d7aafcac5364c241e09  
    --with the name of your clustered index.  
    CREATE CLUSTERED COLUMNSTORE INDEX   
    ClusteredIndex_d473567f7ea04d7aafcac5364c241e09  
     ON MyFactTable  
    WITH DROP_EXISTING = ON;  
    ```  
  
### <a name="e-convert-a-columnstore-table-to-a-rowstore-table-with-a-clustered-index"></a>E. 將資料行存放區資料表轉換成具有叢集索引的資料列存放區資料表。  
 若要將資料行存放區資料表轉換成具有叢集索引的資料列存放區資料表，請使用 CREATE INDEX 陳述式搭配 DROP_EXISTING 選項。  
  
```sql  
CREATE CLUSTERED INDEX ci_MyTable   
ON MyFactTable  
WITH ( DROP EXISTING = ON );  
```  
  
### <a name="f-convert-a-columnstore-table-to-a-rowstore-heap"></a>F. 將資料行存放區資料表轉換成資料列存放區堆積  
 若要將資料行存放區資料表轉換成資料列存放區堆積，只要卸除叢集資料行存放區索引即可。  
  
```sql  
DROP INDEX MyCCI   
ON MyFactTable;  
```  
  

### <a name="g-defragment-by-rebuilding-the-entire-clustered-columnstore-index"></a>G. 重建整個叢集資料行存放區索引來進行重組  
   適用於：[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)]  
  
 有兩種方式可重建完整的叢集資料行存放區索引。 您可以使用 CREATE CLUSTERED COLUMNSTORE INDEX，或 [ALTER INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/alter-index-transact-sql.md) 以及 REBUILD 選項。 這兩種方法會獲得相同的結果。  
  
> [!NOTE]  
> 從 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 開始，請使用 `ALTER INDEX...REORGANIZE`，而不是使用此範例中描述的方法來重建。  
  
```sql  
--Determine the Clustered Columnstore Index name of MyDimTable.  
SELECT i.object_id, i.name, t.object_id, t.name   
FROM sys.indexes i   
JOIN sys.tables t  
ON (i.type_desc = 'CLUSTERED COLUMNSTORE')  
WHERE t.name = 'RowstoreDimTable';  
  
--Rebuild the entire index by using CREATE CLUSTERED INDEX.  
CREATE CLUSTERED COLUMNSTORE INDEX my_CCI   
ON MyFactTable  
WITH ( DROP_EXISTING = ON );  
  
--Rebuild the entire index by using ALTER INDEX and the REBUILD option.  
ALTER INDEX my_CCI  
ON MyFactTable  
REBUILD PARTITION = ALL  
WITH ( DROP_EXISTING = ON );  
```  
  
##  <a name="examples-for-nonclustered-columnstore-indexes"></a><a name="nonclustered"></a> 非叢集資料行存放區索引的範例  
  
### <a name="a-create-a-columnstore-index-as-a-secondary-index-on-a-rowstore-table"></a>A. 在資料列存放區資料表上，建立資料行存放區索引作為次要索引。  
 這個範例會在資料列存放區資料表上，建立非叢集資料行存放區索引。 在這種情況下，只能建立一個資料行存放區索引。 資料行存放區索引需要額外的儲存體，因為它包含資料列存放區資料表中的資料複本。 這個範例會建立簡單資料表和叢集索引，然後示範建立非叢集資料行存放區索引的語法。  
  
```sql  
CREATE TABLE SimpleTable  
(ProductKey [INT] NOT NULL,   
OrderDateKey [INT] NOT NULL,   
DueDateKey [INT] NOT NULL,   
ShipDateKey [INT] NOT NULL);  
GO  
CREATE CLUSTERED INDEX cl_simple ON SimpleTable (ProductKey);  
GO  
CREATE NONCLUSTERED COLUMNSTORE INDEX csindx_simple  
ON SimpleTable  
(OrderDateKey, DueDateKey, ShipDateKey);  
GO  
```  
  
### <a name="b-create-a-simple-nonclustered-columnstore-index-using-all-options"></a>B. 使用所有選項來建立簡單的非叢集資料行存放區索引  
 下列範例示範利用所有選項建立非叢集資料行存放區索引的語法。  
  
```sql  
CREATE NONCLUSTERED COLUMNSTORE INDEX csindx_simple  
ON SimpleTable  
(OrderDateKey, DueDateKey, ShipDateKey)  
WITH (DROP_EXISTING =  ON,   
    MAXDOP = 2)  
ON "default"  
GO  
```  
  
 如需使用資料分割資料表的較複雜範例，請參閱[資料行存放區索引概觀](../../relational-databases/indexes/columnstore-indexes-overview.md)。  
  
### <a name="c-create-a-nonclustered-columnstore-index-with-a-filtered-predicate"></a>C. 使用篩選述詞來建立非叢集資料行存放區索引  
 下列範例會對 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 資料庫中的 Production.BillOfMaterials 資料表，建立篩選的非叢集資料行存放區索引。 篩選述詞可以包含在已篩選之索引中不是索引鍵資料行的資料行。 此範例中的述詞只會選取 EndDate 不是 NULL 的資料列。  
  
```sql  
IF EXISTS (SELECT name FROM sys.indexes  
    WHERE name = N'FIBillOfMaterialsWithEndDate'   
    AND object_id = OBJECT_ID(N'Production.BillOfMaterials'))  
DROP INDEX FIBillOfMaterialsWithEndDate  
    ON Production.BillOfMaterials;  
GO  
CREATE NONCLUSTERED COLUMNSTORE INDEX "FIBillOfMaterialsWithEndDate"  
    ON Production.BillOfMaterials (ComponentID, StartDate)  
    WHERE EndDate IS NOT NULL;  
```  
  
###  <a name="d-change-the-data-in-a-nonclustered-columnstore-index"></a><a name="ncDML"></a> D. 變更非叢集資料行存放區索引中的資料  
   適用於：[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 到 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)]。
  
 一旦您在資料表上建立非叢集資料行存放區索引，就無法直接修改該資料表中的資料。 使用 INSERT、UPDATE、DELETE 或 MERGE 的查詢會失敗，並傳回錯誤訊息。 若要加入或修改資料表中的資料，您可以執行下列其中一項操作：  
  
-   停用或卸除資料行存放區索引。 然後您就可以更新資料表中的資料。 如果您停用資料行存放區索引，您可以在完成更新資料時重建資料行存放區索引。 例如，  
  
    ```sql  
    ALTER INDEX mycolumnstoreindex ON mytable DISABLE;  
    -- update mytable --  
    ALTER INDEX mycolumnstoreindex on mytable REBUILD  
    ```  
  
-   將資料載入未包含資料行存放區索引的暫存資料表。 在暫存資料表上建立資料行存放區索引。 將暫存資料表切換至主資料表的空白分割區。  
  
-   從具有資料行存放區索引的資料表分割區切換至空白的暫存資料表。 如果暫存資料表上有資料行存放區索引，請停用資料行存放區索引。 執行所有更新。 建立 (或重建) 資料行存放區索引。 將暫存資料表切換回 (現在為空白的) 主資料表分割區。  
  
## <a name="examples-sssdwfull-and-sspdw"></a>範例：[!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 和 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
### <a name="a-change-a-clustered-index-to-a-clustered-columnstore-index"></a>A. 將叢集索引轉換成叢集資料行存放區索引  
 您可以使用 CREATE CLUSTERED COLUMNSTORE INDEX 陳述式和 DROP_EXISTING = ON 來完成以下事項：  
  
-   將叢集索引轉換成叢集資料行存放區索引。  
  
-   重建叢集資料行存放區索引。  
  
 本範例會將 xDimProduct 資料表建立為具有叢集索引的資料列存放區資料表，然後使用 CREATE CLUSTERED COLUMNSTORE INDEX，將資料表從資料列存放區資料表變更為資料行存放區資料表。  
  
```sql  
-- Uses AdventureWorks  
  
IF EXISTS (SELECT name FROM sys.tables  
    WHERE name = N'xDimProduct'  
    AND object_id = OBJECT_ID (N'xDimProduct'))  
DROP TABLE xDimProduct;  
  
--Create a distributed table with a clustered index.  
CREATE TABLE xDimProduct (ProductKey, ProductAlternateKey, ProductSubcategoryKey)  
WITH ( DISTRIBUTION = HASH(ProductKey),  
    CLUSTERED INDEX (ProductKey) )  
AS SELECT ProductKey, ProductAlternateKey, ProductSubcategoryKey FROM DimProduct;  
  
--Change the existing clustered index   
--to a clustered columnstore index with the same name.  
--Look up the name of the index before running this statement.  
CREATE CLUSTERED COLUMNSTORE INDEX <index_name>   
ON xdimProduct   
WITH ( DROP_EXISTING = ON );  
```  
  
### <a name="b-rebuild-a-clustered-columnstore-index"></a>B. 重建叢集資料行存放區索引  
 這個範例以前面的範例為基礎，使用 CREATE CLUSTERED COLUMNSTORE INDEX 來重建稱為 cci_xDimProduct 的現有叢集資料行存放區索引。  
  
```sql  
--Rebuild the existing clustered columnstore index.  
CREATE CLUSTERED COLUMNSTORE INDEX cci_xDimProduct   
ON xdimProduct   
WITH ( DROP_EXISTING = ON );  
```  
  
### <a name="c-change-the-name-of-a-clustered-columnstore-index"></a>C. 變更叢集資料行存放區索引的名稱  
 若要變更叢集資料行存放區索引的名稱，請卸除現有的叢集資料行存放區索引，然後使用新名稱來重新建立索引。  
  
 我們建議只在小的資料表或空白資料表執行此操作。 卸除大型叢集資料行存放區索引並使用不同名稱來重建，將會花很長的時間。  
  
 本範例使用先前範例的 cci_xDimProduct 叢集資料行存放區索引，卸除 cci_xDimProduct 叢集資料行存放區索引，然後重新建立叢集資料行存放區索引並命名為 mycci_xDimProduct。  
  
```sql  
--For illustration purposes, drop the clustered columnstore index.   
--The table continues to be distributed, but changes to a heap.  
DROP INDEX cci_xdimProduct ON xDimProduct;  
  
--Create a clustered index with a new name, mycci_xDimProduct.  
CREATE CLUSTERED COLUMNSTORE INDEX mycci_xDimProduct  
ON xdimProduct  
WITH ( DROP_EXISTING = OFF );  
```  
  
### <a name="d-convert-a-columnstore-table-to-a-rowstore-table-with-a-clustered-index"></a>D. 將資料行存放區資料表轉換成具有叢集索引的資料列存放區資料表。  
 有時候您可能會想卸除叢集資料行存放區索引，然後建立叢集索引。 這樣會以資料列存放區格式來儲存資料表。 本範例會將資料行存放區資料表轉換成具有叢集索引且名稱相同的資料列存放區資料表。 不會遺失任何資料。 所有資料會移到資料列存放區資料表，然後列出的資料行會成為叢集索引中的索引鍵資料行。  
  
```sql  
--Drop the clustered columnstore index and create a clustered rowstore index.   
--All of the columns are stored in the rowstore clustered index.   
--The columns listed are the included columns in the index.  
CREATE CLUSTERED INDEX cci_xDimProduct    
ON xdimProduct (ProductKey, ProductAlternateKey, ProductSubcategoryKey, WeightUnitMeasureCode)  
WITH ( DROP_EXISTING = ON);  
```  
  
### <a name="e-convert-a-columnstore-table-back-to-a-rowstore-heap"></a>E. 將資料行存放區資料表轉換回資料列存放區堆積  
 使用 [DROP INDEX (SQL Server PDW)](drop-index-transact-sql.md) 來卸除叢集資料行存放區索引，然後將資料表轉換成資料列存放區堆積。 這個範例會將 cci_xDimProduct 資料表轉換成資料列存放區堆積。 資料表仍然會繼續散發，但是儲存為堆積。  
  
```sql  
--Drop the clustered columnstore index. The table continues to be distributed, but changes to a heap.  
DROP INDEX cci_xdimProduct ON xdimProduct;  
```  

### <a name="f-create-an-ordered-clustered-columnstore-index-on-a-table-with-no-index"></a>F. 在沒有索引的資料表上建立已排序的叢集資料行存放區索引

```sql
CREATE CLUSTERED COLUMNSTORE INDEX cci ON Sales.OrderLines
ORDER ( SHIPDATE );
```

### <a name="g-convert-a-clustered-columnstore-index-to-an-ordered-clustered-columnstore-index"></a>G. 將叢集資料行存放區索引轉換為已排序的叢集資料行存放區索引

```sql  
CREATE CLUSTERED COLUMNSTORE INDEX cci ON Sales.OrderLines
ORDER ( SHIPDATE );
WITH (DROP_EXISTING = ON)
```

### <a name="h-add-a-column-to-the-ordering-of-an-ordered-clustered-columnstore-index"></a>H. 將資料行新增到已排序叢集資料行存放區索引的順序

```sql
-- The original ordered clustered columnstore index was ordered on SHIPDATE column only.  Add PRODUCTKEY column to the ordering.
CREATE CLUSTERED COLUMNSTORE INDEX cci ON Sales.OrderLines
ORDER ( SHIPDATE, PRODUCTKEY );
WITH (DROP_EXISTING = ON)
```
### <a name="i-change-the-ordinal-of-ordered-columns"></a>I. 變更已排序資料行的序數  
```sql
-- The original ordered clustered columnstore index was ordered on SHIPDATE, PRODUCTKEY.  Change the ordering to PRODUCTKEY, SHIPDATE.  
CREATE CLUSTERED COLUMNSTORE INDEX cci ON Sales.OrderLines
ORDER ( PRODUCTKEY,SHIPDATE );
WITH (DROP_EXISTING = ON)
```