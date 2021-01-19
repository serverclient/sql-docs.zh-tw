---
description: CREATE INDEX (Transact-SQL)
title: CREATE INDEX (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/17/2020
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- CREATE INDEX
- INDEX
- INDEX_TSQL
- CREATE_INDEX_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- CREATE XML INDEX statement
- PRIMARY XML INDEX statement
- indexes [SQL Server], creating
- online index operations
- index keys [SQL Server]
- PROPERTY INDEX statement
- computed columns, index creation
- clustered indexes, creating
- indexed views [SQL Server], index creation
- partitioned indexes [SQL Server], creating
- VALUE INDEX statement
- index creation [SQL Server], CREATE INDEX statement
- DROP_EXISTING clause
- row locks [SQL Server]
- composite indexes
- MAXDOP index option, CREATE INDEX statement
- filtered indexes [SQL Server], creating
- PATH INDEX statement
- locking [SQL Server], indexes
- unique indexes, creating
- primary indexes [SQL Server]
- CREATE PRIMARY XML INDEX statement
- SET statement, index creation
- index options [SQL Server]
- included columns
- ONLINE option
- nonclustered indexes [SQL Server], creating
- CREATE INDEX statement
- IGNORE_DUP_KEY option
- deterministic functions
- keys [SQL Server], index
- SECONDARY XML INDEX statement
- page locks [SQL Server]
- secondary indexes [SQL Server]
- XML indexes [SQL Server], creating
ms.assetid: d2297805-412b-47b5-aeeb-53388349a5b9
author: pmasl
ms.author: pelopes
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: c472b3996683512fb6ac7cd3f001d53ca1fd73ae
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98170680"
---
# <a name="create-index-transact-sql"></a>CREATE INDEX (Transact-SQL)

[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

在資料表或檢視上建立關聯式索引。 也稱為資料列存放區索引，因為它是叢集或非叢集的 B 型樹狀結構索引。 您可以在資料表中含有資料之前，先建立資料列存放區索引。 特別是在查詢會從特定資料行中選取，或需要以特定順序排序值時，使用資料列存放區索引來改善查詢效能。

> [!NOTE]
> [!INCLUDE[ssSDW](../../includes/sssdw-md.md)] 和 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] 目前不支援 Unique 條件約束。 參考 Unique 條件約束的任何範例僅適用於 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 和 [!INCLUDE[ssSDS](../../includes/sssds-md.md)]。
> [!TIP]
> 如需索引設計指導方針的詳細資訊，請參閱 [SQL Server 索引設計指南](../../relational-databases/sql-server-index-design-guide.md)。

 **簡單範例：**

```sql
-- Create a nonclustered index on a table or view
CREATE INDEX i1 ON t1 (col1);

-- Create a clustered index on a table and use a 3-part name for the table
CREATE CLUSTERED INDEX i1 ON d1.s1.t1 (col1);

-- Syntax for SQL Server and Azure SQL Database
-- Create a nonclustered index with a unique constraint
-- on 3 columns and specify the sort order for each column
CREATE UNIQUE INDEX i1 ON t1 (col1 DESC, col2 ASC, col3 DESC);
```

**關鍵案例：**

從 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 和 [!INCLUDE[ssSDS](../../includes/sssds-md.md)] 開始，使用資料行存放區索引上的非叢集索引來改善資料倉儲查詢效能。 如需詳細資訊，請參閱[資料行存放區索引 - 資料倉儲](../../relational-databases/indexes/columnstore-indexes-data-warehouse.md)。

針對其他索引類型，請參閱：

- [CREATE XML INDEX](../../t-sql/statements/create-xml-index-transact-sql.md)
- [CREATE SPATIAL INDEX](../../t-sql/statements/create-spatial-index-transact-sql.md)
- [CREATE COLUMNSTORE INDEX](../../t-sql/statements/create-columnstore-index-transact-sql.md)

![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)

## <a name="syntax"></a>語法

### <a name="syntax-for-sql-server-and-azure-sql-database"></a>SQL Server 和 Azure SQL Database 的語法

```syntaxsql
CREATE [ UNIQUE ] [ CLUSTERED | NONCLUSTERED ] INDEX index_name
    ON <object> ( column [ ASC | DESC ] [ ,...n ] )
    [ INCLUDE ( column_name [ ,...n ] ) ]
    [ WHERE <filter_predicate> ]
    [ WITH ( <relational_index_option> [ ,...n ] ) ]
    [ ON { partition_scheme_name ( column_name )
         | filegroup_name
         | default
         }
    ]
    [ FILESTREAM_ON { filestream_filegroup_name | partition_scheme_name | "NULL" } ]
  
[ ; ]
  
<object> ::=
{ database_name.schema_name.table_or_view_name | schema_name.table_or_view_name | table_or_view_name }

<relational_index_option> ::=
{
    PAD_INDEX = { ON | OFF }
  | FILLFACTOR = fillfactor
  | SORT_IN_TEMPDB = { ON | OFF }
  | IGNORE_DUP_KEY = { ON | OFF }
  | STATISTICS_NORECOMPUTE = { ON | OFF }
  | STATISTICS_INCREMENTAL = { ON | OFF }
  | DROP_EXISTING = { ON | OFF }
  | ONLINE = { ON | OFF }
  | RESUMABLE = { ON | OFF }
  | MAX_DURATION = <time> [MINUTES]
  | ALLOW_ROW_LOCKS = { ON | OFF }
  | ALLOW_PAGE_LOCKS = { ON | OFF }
  | OPTIMIZE_FOR_SEQUENTIAL_KEY = { ON | OFF }
  | MAXDOP = max_degree_of_parallelism
  | DATA_COMPRESSION = { NONE | ROW | PAGE }
     [ ON PARTITIONS ( { <partition_number_expression> | <range> }
     [ , ...n ] ) ]
}

<filter_predicate> ::=
    <conjunct> [ AND <conjunct> ]

<conjunct> ::=
    <disjunct> | <comparison>

<disjunct> ::=
        column_name IN (constant ,...n)

<comparison> ::=
        column_name <comparison_op> constant

<comparison_op> ::=
    { IS | IS NOT | = | <> | != | > | >= | !> | < | <= | !< }

<range> ::=
<partition_number_expression> TO <partition_number_expression>
```

### <a name="backward-compatible-relational-index"></a>與舊版相容的關聯式索引

> [!IMPORTANT]
> [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的未來版本將會移除與舊版相容的關聯式索引語法結構。
> 請避免在新的開發工作中使用此語法結構，並規劃修改目前使用此功能的應用程式。
> 改為使用 <relational_index_option> 中指定的語法結構。

```syntaxsql
CREATE [ UNIQUE ] [ CLUSTERED | NONCLUSTERED ] INDEX index_name
    ON <object> ( column_name [ ASC | DESC ] [ ,...n ] )
    [ WITH <backward_compatible_index_option> [ ,...n ] ]
    [ ON { filegroup_name | "default" } ]

<object> ::=
{
    [ database_name. [ owner_name ] . | owner_name. ]
    table_or_view_name
}

<backward_compatible_index_option> ::=
{
    PAD_INDEX
  | FILLFACTOR = fillfactor
  | SORT_IN_TEMPDB
  | IGNORE_DUP_KEY
  | STATISTICS_NORECOMPUTE
  | DROP_EXISTING
}
```

### <a name="syntax-for-azure-synapse-analytics-and-parallel-data-warehouse"></a>Azure Synapse Analytics 和平行處理資料倉儲的語法

```syntaxsql

CREATE CLUSTERED COLUMNSTORE INDEX index_name
    ON [ database_name . [ schema ] . | schema . ] table_name
    [ORDER (column[,...n])]
    [WITH ( DROP_EXISTING = { ON | OFF } )]
[;]


CREATE [ CLUSTERED | NONCLUSTERED ] INDEX index_name
    ON [ database_name . [ schema ] . | schema . ] table_name
        ( { column [ ASC | DESC ] } [ ,...n ] )
    WITH ( DROP_EXISTING = { ON | OFF } )
[;]


```

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數

UNIQUE      
在資料表或檢視表上建立唯一索引。 在唯一索引中，任兩個資料列都不能有相同的索引鍵值。 檢視表中的叢集索引必須是唯一的。

不論 IGNORE_DUP_KEY 是否設為 ON，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 都不允許在已包含重複值的資料行上建立唯一索引。 如果嘗試執行這項作業，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 會顯示錯誤訊息。 必須先移除重複值，才能在資料行上建立唯一索引。 唯一索引中使用的資料行應設為 NOT NULL，因為當建立唯一索引時，多個 Null 值會被視為重複值。

CLUSTERED      
建立一個索引，在該索引中，索引鍵值的邏輯順序會決定資料表中對應資料列的實體順序。 叢集索引的層級 (底部或分葉) 包含資料表的實際資料列。 資料表或檢視表一次只允許一個叢集索引。

含有唯一叢集索引的檢視表稱為索引檢視表。 在檢視表上建立唯一叢集索引，可將檢視表實際具體化。 必須先在檢視表上建立唯一叢集索引，才能在相同檢視表上定義任何其他索引。 如需詳細資訊，請參閱 [建立索引檢視表](../../relational-databases/views/create-indexed-views.md)。

先建立叢集索引，再建立任何非叢集索引。 當建立叢集索引時，會重建資料表上現有的非叢集索引。

如果未指定 `CLUSTERED`，就會建立非叢集索引。

> [!NOTE]
> 依定義，叢集索引和資料頁面的分葉層級都一樣，因此，建立叢集索引並有效地使用 ON *partition_scheme_name* 或 ON *filegroup_name* 子句，就可將資料表從建立資料表的檔案群組移至新的資料分割配置或檔案群組。 在特定檔案群組上建立資料表或索引之前，請先確認檔案群組是可用的，而且它們有足夠的空間可供索引使用。

在某些情況下，建立叢集索引可啟用先前停用的索引。 如需詳細資訊，請參閱[啟用索引與條件約束](../../relational-databases/indexes/enable-indexes-and-constraints.md)及[停用索引與條件約束](../../relational-databases/indexes/disable-indexes-and-constraints.md)。

NONCLUSTERED      
建立索引來指定資料表的邏輯排序。 如果是非叢集索引，資料列的實體順序和它們的索引順序無關。

不論索引的建立方式為何，每一份資料表最多只能有 999 個非叢集索引：以隱含的方式使用 PRIMARY KEY 和 UNIQUE 條件約束或以明確的方式使用 CREATE INDEX。

如果是索引檢視表，只能在已定義唯一叢集索引的檢視表上建立非叢集索引。

如果未指定，預設的索引類型為 NONCLUSTERED。

*index_name*      
 這是索引的名稱。 在資料表或檢視表內，索引名稱必須是唯一的，但在資料庫內就不一定要是唯一的。 索引名稱必須遵照[識別碼](../../relational-databases/databases/database-identifiers.md)的規則。

*column*      
 這是做為索引根據的資料行。 您可以指定兩個或兩個以上的資料行名稱，在指定之資料行的合計值上建立複合索引。 在 *table_or_view_name* 後面的括號內，依排序優先權順序列出要併入複合式索引的資料行。

單一複合式索引鍵中最多只能結合 32 個資料行。 複合索引鍵中的所有資料行都必須在相同的資料表或檢視表中。 針對叢集索引，組合索引值的允許大小上限是 900 個位元組，非叢集索引則為 1,700 個位元組。 針對 [!INCLUDE[ssSDS](../../includes/sssds-md.md)] 和 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 之前的版本，限制為 16 個資料行與 900 個位元組。

屬於大型物件 (LOB) 資料類型 **ntext**、**text**、**varchar(max)** 、**nvarchar(max)** 、**varbinary(max)** 、**xml** 或 **image** 的資料行無法指定為索引的索引鍵資料行。 此外，即使 CREATE INDEX 陳述式中未參考 **ntext**、**text** 或 **image** 資料行，檢視定義也不能包含這些資料行。

如果 CLR 使用者定義型別支援二進位排序，您可以在該型別的資料行上建立索引。 只要方法標示為具決定性且不執行資料存取作業，您也可以在定義為使用者定義型別資料行方法引動過程的計算資料行上建立索引。 如需編製 CLR 使用者定義類型資料行索引的詳細資訊，請參閱 [CLR 使用者定義類型](../../relational-databases/clr-integration-database-objects-user-defined-types/clr-user-defined-types.md)。

[ **ASC** | DESC ]      
決定特定索引資料行的遞增或遞減排序方向。 預設值是 ASC。

INCLUDE **(** _column_ [ **,** ... *n* ] **)**       
指定要加入至非叢集索引分葉層級的非索引鍵資料行。 非叢集索引可以是唯一或非唯一的。

資料行名稱在 INCLUDE 清單中不能重複，且不能同時做為索引鍵資料行和非索引鍵資料行。 如果資料表上有定義叢集索引，非叢集索引一定會包含叢集索引資料行。 如需詳細資訊，請參閱 [建立內含資料行的索引](../../relational-databases/indexes/create-indexes-with-included-columns.md)。

允許所有的資料類型，除了 **text**、 **ntext** 和 **image** 以外。 如果任一個指定的非索引鍵資料行屬於 **varchar(max)** 、**nvarchar(max)** 或 **varbinary(max)** 資料類型，就必須離線 (ONLINE = OFF) 建立或重建索引。

具決定性之精確或非精確的計算資料行都可以當做內含資料行。 從 **image**、**ntext**、**text**、**varchar(max)** 、**nvarchar(max)** 、**varbinary(max)** 及 **xml** 資料類型衍生的計算資料行，只要計算資料行資料類型可作為內含資料行，就可包含於非索引鍵的資料行中。 如需詳細資訊，請參閱 [計算資料行的索引](../../relational-databases/indexes/indexes-on-computed-columns.md)。

如需建立 XML 索引的資訊，請參閱 [CREATE XML INDEX](../../t-sql/statements/create-xml-index-transact-sql.md)。

WHERE \<filter_predicate>      
藉由指定要包含在已篩選之索引中的資料列來建立該索引。 已篩選的索引必須是資料表上的非叢集索引。 針對已篩選之索引中的資料列建立已篩選的統計資料。

篩選述詞會使用簡單比較邏輯，而且無法參考計算資料行、UDT 資料行、空間資料類型資料行或 hierarchyID 資料類型資料行。 比較運算子不允許使用 NULL 常值的比較。 請改用 IS NULL 和 IS NOT NULL 運算子。

下面是一些 `Production.BillOfMaterials` 資料表之篩選述詞的範例：

```sql
WHERE StartDate > '20000101' AND EndDate <= '20000630'

WHERE ComponentID IN (533, 324, 753)

WHERE StartDate IN ('20000404', '20000905') AND EndDate IS NOT NULL
```

已篩選的索引不適用於 XML 索引和全文檢索索引。 如果是 UNIQUE 索引，只有選取的資料列必須有唯一的索引值。 已篩選的索引不允許 IGNORE_DUP_KEY 選項。

ON *partition_scheme_name* **( _column_name_ )**      

**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]

指定分割區配置來定義要做為分割區索引之分割區對應目標的檔案群組。 透過執行 [CREATE PARTITION SCHEME](../../t-sql/statements/create-partition-scheme-transact-sql.md) 或 [ALTER PARTITION SCHEME](../../t-sql/statements/alter-partition-scheme-transact-sql.md)，讓資料分割配置一定會存在於資料庫內。 *column_name* 會指定資料分割索引將進行分割的資料行。 此資料行必須符合 *partition_scheme_name* 所使用資料分割函數引數的資料類型、長度與有效位數。 *column_name* 不限定為索引定義中的資料行。 可以指定基底資料表中的任何資料行，但有個例外是，在分割 UNIQUE 索引時，必須從用來作為唯一索引鍵使用的資料行中選擇 *column_name*。 這項限制可讓 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 只在單一分割區內驗證索引鍵值的唯一性。

> [!NOTE]
> 當您分割一個非唯一的叢集索引時，如果尚未指定分割區資料行，依預設，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 會將它加入至叢集索引鍵清單。 當您分割一個非唯一的非叢集索引時，如果尚未指定分割區資料行，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 會將它新增為索引的非索引鍵 (內含) 資料行。

如果未指定 _partition_scheme_name_ 或 _filegroup_，且已分割資料表，則會使用相同的分割資料行，將索引放在與基礎資料表相同的資料分割配置中。

> [!NOTE]
> 您無法在 XML 索引上指定分割區配置。 如果基底資料表已分割，XML 索引會使用與資料表相同的分割區配置。

如需分割索引的詳細資訊，請參閱[資料分割資料表與索引](../../relational-databases/partitions/partitioned-tables-and-indexes.md)。

ON _filegroup_name_      

**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本)

在指定的檔案群組上建立指定的索引。 如果未指定位置，且資料表或檢視表未分割，則索引會使用與基礎資料表或檢視表相同的檔案群組。 此檔案群組必須已存在。

ON **"** default **"**      

**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]

在與資料表或檢視相同的檔案群組或分割區結構描述上建立所指定索引。

在這個內容中，default 這個字不是關鍵字。 它是預設檔案群組的識別碼，必須加以分隔，如 ON **"** default **"** 或 ON **[** default **]** 。 如果指定了 "default"，目前工作階段的 QUOTED_IDENTIFIER 選項就必須是 ON。 這是預設值。 如需詳細資訊，請參閱 [SET QUOTED_IDENTIFIER](../../t-sql/statements/set-quoted-identifier-transact-sql.md)。

> [!NOTE]
> "default" 並未指出 CREATE INDEX 內容中的資料庫預設檔案群組。 這不同於 CREATE TABLE，其中的 "default" 會將資料表定位在資料庫的預設檔案群組上。

[ FILESTREAM_ON { *filestream_filegroup_name* | *partition_scheme_name* | "NULL" } ]      

**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本)

指定在建立叢集索引時，資料表之 FILESTREAM 資料的位置。 FILESTREAM_ON 子句允許將 FILESTREAM 資料移到不同的 FILESTREAM 檔案群組或分割區配置。

_filestream_filegroup_name_ 是 FILESTREAM 檔案群組的名稱。 此檔案群組必須有一個使用 [CREATE DATABASE](../../t-sql/statements/create-database-transact-sql.md) 或 [ALTER DATABASE](../../t-sql/statements/alter-database-transact-sql.md) 陳述式針對此檔案群組定義的檔案，否則會引發錯誤。

如果分割此資料表，則必須包含 FILESTREAM_ON 子句，而且必須指定 FILESTREAM 檔案群組的分割區配置，此配置會使用與資料表之分割區配置相同的分割區函數和分割區資料行。 否則，就會引發錯誤。

如果此資料表未分割，FILESTREAM 資料行將無法分割。 此資料表的 FILESTREAM 資料必須儲存在 FILESTREAM_ON 子句中指定的單一檔案群組內。

如果正在建立叢集索引，而且此資料表不包含 FILESTREAM 資料行，則可以在 CREATE INDEX 陳述式內指定 `FILESTREAM_ON NULL`。

如需詳細資訊，請參閱 [FILESTREAM &#40;SQL Server&#41;](../../relational-databases/blob/filestream-sql-server.md)。

**\<object>::=**      

這是要建立索引的完整或非完整物件。

_database_name_      
這是資料庫的名稱。

*schema_name*      
這是資料表或檢視表所屬的結構描述名稱。

*table_or_view_name*      
這是要建立索引之資料表或檢視表的名稱。

必須利用 SCHEMABINDING 定義檢視表，才能在該檢視表上建立索引。 必須先在檢視表上建立唯一叢集索引，才能建立任何非叢集索引。 如需有關索引檢視表的詳細資訊，請參閱「備註」一節。

從 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 開始，此物件可以是與叢集資料行存放區索引一併儲存的資料表。

當 *database_name* 是目前的資料庫或 _database_name_ 是 `tempdb`，且 _object_name_ 的開頭為 # 時，[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 支援三部分名稱格式 _database_name_.[_schema_name_]._object_name_。

**\<relational_index_option\>::=**       
指定當您建立索引時所需使用的選項。

PAD_INDEX = { ON | **OFF** }      

**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]

指定索引填補。 預設值為 OFF。

開啟      
*fillfactor* 指定的可用空間百分比會套用到索引的中繼層級頁面。

OFF 或未指定 _fillfactor_      
中繼層級頁面會幾乎填滿整個容量，但會考量中繼頁面上的索引鍵集，而保留至少可供索引所能擁有之大小上限的一個資料列使用的足夠空間。

只有在指定 FILLFACTOR 時，才能使用 PAD_INDEX 選項，因為 PAD_INDEX 會使用 FILLFACTOR 所指定的百分比。 如果 FILLFACTOR 所指定的百分比不夠，無法允許一個資料列，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 會在內部覆寫該百分比以允許最小值。 不論 _fillfactor_ 的值設得多低，中繼索引頁面上的資料列數目絕對不能少於兩個。

在與舊版本相容的語法中，WITH PAD_INDEX 相當於 WITH PAD_INDEX = ON。

FILLFACTOR **=** _fillfactor_      

**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]

指定用以指出建立或重建索引時，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 填滿各索引頁面分葉層級之程度的百分比。 *fillfactor* 必須是 1 到 100 之間的整數值。 如果 *fillfactor* 是 100，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 會利用已填滿容量的分葉頁面來建立索引。

只有在建立或重建索引時才會套用 FILLFACTOR 設定。 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 不會動態保留頁面中空白空間的指定百分比。 若要檢視填滿因數設定，請使用 [sys.indexes](../../relational-databases/system-catalog-views/sys-indexes-transact-sql.md) 目錄檢視表。

> [!IMPORTANT]
> 利用小於 100 的 FILLFACTOR 來建立叢集索引，會影響資料所佔用的儲存空間數量，因為 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 在建立叢集索引時會轉散發資料。

如需詳細資訊，請參閱 [指定索引的填滿因素](../../relational-databases/indexes/specify-fill-factor-for-an-index.md)。

SORT_IN_TEMPDB = { ON | **OFF** }      

**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]

指定是否要將暫時排序結果儲存在 **tempdb** 中。 預設值為 OFF (除了 Azure SQL Database 超大規模資料庫以外)。若是超大規模資料庫中的所有索引建置作業，除非使用可繼續的索引重建，否則 SORT_IN_TEMPDB 一律會是 ON。

開啟      
用來建置索引的中繼排序結果會儲存在 **tempdb** 中。 如果 **tempdb** 位於與使用者資料庫所在磁碟不同的磁碟上，這可能會減少建立索引所需的時間。 不過，這會增加建立索引時所使用的磁碟空間量。

OFF      
中繼排序結果會儲存在與用來儲存索引相同的資料庫中。

除了建立索引時使用者資料庫中所需的空間以外，**tempdb** 還需要大約相同數量的額外空間來容納中繼排序結果。 如需詳細資訊，請參閱[索引的 SORT_IN_TEMPDB 選項](../../relational-databases/indexes/sort-in-tempdb-option-for-indexes.md)。

在與舊版本相容的語法中，WITH SORT_IN_TEMPDB 相當於 WITH SORT_IN_TEMPDB = ON。

IGNORE_DUP_KEY = { ON | **OFF** }      
指定當插入作業嘗試將重複的索引鍵值插入唯一索引時所產生的錯誤回應。 IGNORE_DUP_KEY 選項只適用於在建立或重建索引之後所發生的插入作業。 執行 [CREATE INDEX](../../t-sql/statements/create-index-transact-sql.md)、[ALTER INDEX](../../t-sql/statements/alter-index-transact-sql.md) 或 [UPDATE](../../t-sql/queries/update-transact-sql.md) 時，這個選項沒有任何作用。 預設值為 OFF。

開啟      
當重複的索引鍵值插入唯一索引時，就會出現警告訊息。 只有違反唯一性條件約束的資料列才會失敗。

OFF      
當重複的索引鍵值插入唯一索引時，就會出現錯誤訊息。 整個 INSERT 作業將會回復。

若為針對檢視表所建立的索引、非唯一索引、XML 索引、空間索引和篩選索引，IGNORE_DUP_KEY 不得設為 ON。

若要檢視 IGNORE_DUP_KEY，請使用 [sys.indexes](../../relational-databases/system-catalog-views/sys-indexes-transact-sql.md)。

在與舊版本相容的語法中，WITH IGNORE_DUP_KEY 相當於 WITH IGNORE_DUP_KEY = ON。

STATISTICS_NORECOMPUTE = { ON | **OFF**}      
指定是否要重新計算散發統計資料。 預設值為 OFF。

開啟      
不會自動重新計算過期的統計資料。

OFF      
啟用自動統計資料更新。

若要還原自動統計資料更新，請將 STATISTICS_NORECOMPUTE 設為 OFF，或執行不含 NORECOMPUTE 子句的 UPDATE STATISTICS。

> [!IMPORTANT]
> 停用散發統計資料的自動重新計算，可防止查詢最佳化工具取得與資料表有關之查詢的最佳執行計畫。

在與舊版本相容的語法中，WITH STATISTICS_NORECOMPUTE 相當於 WITH STATISTICS_NORECOMPUTE = ON。

STATISTICS_INCREMENTAL = { ON | **OFF** }     

**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]

若設定為 **ON**，所建立的統計資料會以每個資料分割統計資料為依據。 若為 **OFF**，則會卸除統計資料樹狀結構，且 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會重新計算統計資料。 預設值為 **OFF**。

如果不支援每個分割區區的統計資料，則會忽略該選項，並產生警告。 針對下列統計資料類型，不支援累加統計資料：

- 建立統計資料時，所使用的索引未與基底資料表進行分割區對齊。
- 在 AlwaysOn 可讀取次要資料庫上建立的統計資料。
- 在唯讀資料庫上建立的統計資料。
- 在篩選的索引上建立的統計資料。
- 在檢視上建立的統計資料。
- 在內部資料表上建立的統計資料。
- 使用空間索引或 XML 索引建立的統計資料。

DROP_EXISTING = { ON | **OFF** }      
這是一個選項，可用來卸除現有的叢集或非叢集索引，並使用已修改的資料行指定值來重建，並會維持相同的索引名稱。 預設值為 OFF。

開啟      
指定要卸除並重建現有索引，此索引的名稱必須與 *index_name* 參數相同。

OFF      
指定不要卸除並重建現有索引。 如果指定的索引名稱已存在，SQL Server 就會顯示錯誤。

利用 DROP_EXISTING，您可以：

- 將非叢集資料列存放區索引變更為叢集資料列存放區索引。

利用 DROP_EXISTING，您無法：

- 將叢集資料列存放區索引變更為非叢集資料列存放區索引。
- 將叢集資料行存放區索引變更為任何類型的資料列存放區索引。

在與舊版本相容的語法中，WITH DROP_EXISTING 相當於 WITH DROP_EXISTING = ON。

ONLINE = { ON | **OFF** }      
指定在索引作業期間，查詢和資料修改是否能夠使用基礎資料表和相關聯的索引。 預設值為 OFF。

> [!IMPORTANT]
> [!INCLUDE[msCoName](../../includes/msconame-md.md)][!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的所有版本都無法使用線上索引作業。 如需 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 版本支援的功能清單，請參閱 [SQL Server 2016 版本和支援的功能](../../sql-server/editions-and-components-of-sql-server-2016.md)。

開啟      
索引作業持續期間不會保留長期資料表鎖定。 在索引作業的主要階段期間，來源資料表上只保留意圖共用 (IS) 鎖定。 這可使基礎資料表和索引的查詢或更新能夠進行。 在作業開始時，共用 (S) 鎖定會在來源物件上保留一段很短的時間。 在作業結束時，如果正在建立非叢集索引，則有一段短時間會在來源上取得 S (共用) 鎖定；或者，當以線上方式建立或卸除叢集索引時，以及正在重建叢集索引或非叢集索引時，則會取得 SCH-M (結構描述修改) 鎖定。 建立本機暫存資料表的索引時，ONLINE 不可設為 ON。

OFF      
在索引作業期間會套用資料表鎖定。 建立、重建或卸除叢集索引的離線索引作業，或重建或卸除非叢集索引的離線索引作業，會取得資料表的結構描述修改 (Sch-M) 鎖定。 這可防止所有使用者在作業持續期間存取基礎資料表。 建立非叢集索引的離線索引作業會取得資料表的共用 (S) 鎖定。 這可避免對基礎資料表進行更新，但仍可執行讀取作業，如 SELECT 陳述式。

如需詳細資訊，請參閱 [Perform Index Operations Online](../../relational-databases/indexes/perform-index-operations-online.md)。  

您可以在線上建立索引，其中包括全域暫存資料表的索引，但下列情況除外：

- XML 索引
- 本機暫存資料表上的索引
- 在檢視上的初始唯一叢集索引
- 停用的叢集索引
- 資料行存放區索引
- 叢集索引 (如果基礎資料表包含 LOB 資料類型 (**image**、**ntext**、**text**) 及空間類型)
- **varchar(max)** 和 **varbinary(max)** 資料行不得為索引的一部分。 在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 中，當資料表包含 **varchar(max)** 或 **varbinary(max)** 資料行時，可以使用 **ONLINE** 選項來建置或重建包含其他資料行的叢集索引。 當基底資料表包含 **varchar(max)** 或 **varbinary(max)** 資料行時，[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 不允許 **ONLINE** 選項

如需詳細資訊，請參閱[線上索引作業如何運作](../../relational-databases/indexes/how-online-index-operations-work.md)。

RESUMABLE **=** { ON | **OFF**}      

**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]

 指定線上索引作業是否為可繼續的作業。

 開啟      
索引作業為可繼續的作業。

 OFF      
索引作業不是可繼續的作業。

MAX_DURATION **=** *time* [**MINUTES**] 與 **RESUMABLE = ON** 搭配使用 (需要 **ONLINE = ON**)   

**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]

指出可繼續的線上索引作業在暫停之前的執行時間 (以分鐘為單位指定的一個整數值)。

> [!IMPORTANT]
> 如需有關可以在線上執行之索引作業的詳細資訊，請參閱[線上索引作業的指導方針](../../relational-databases/indexes/guidelines-for-online-index-operations.md)。

> [!NOTE] 
> 資料行存放區索引不支援可繼續的線上索引重建。

ALLOW_ROW_LOCKS = { **ON** | OFF }      
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]

指定是否允許資料列鎖定。 預設值是 ON。

開啟      
當存取索引時，允許資料列鎖定。 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 會決定使用資料列鎖定的時機。

OFF      
不使用資料列鎖定。

ALLOW_PAGE_LOCKS = { **ON** | OFF }      
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]

指定是否允許頁面鎖定。 預設值是 ON。

開啟      
當存取索引時，允許頁面鎖定。 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 會決定使用頁面鎖定的時機。

OFF      
不使用頁面鎖定。

OPTIMIZE_FOR_SEQUENTIAL_KEY = { ON | **OFF** }      
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]

指定是否要最佳化最後一頁的插入競爭。 預設值為 OFF。 請參閱[循序索引鍵](#sequential-keys)一節以取得詳細資訊。

MAXDOP = *max_degree_of_parallelism*      
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]

在索引作業期間，覆寫 **max degree of parallelism** 設定選項。 如需詳細資訊，請參閱 [設定 max degree of parallelism 伺服器組態選項](../../database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option.md)。 請利用 MAXDOP 來限制執行平行計畫所用的處理器數目。 最大值是 64 個處理器。

*max_degree_of_parallelism* 可以是：

1      
隱藏平行計畫的產生。

\>1      
根據目前的系統工作負載，將平行索引作業所使用的處理器數目上限，限制為所指定的數目或更少的數目。

0 (預設值)      
根據目前的系統工作負載，使用實際數目或比實際數目更少的處理器。

 如需詳細資訊，請參閱 [設定平行索引作業](../../relational-databases/indexes/configure-parallel-index-operations.md)。

> [!NOTE]
> [!INCLUDE[msCoName](../../includes/msconame-md.md)][!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的每個版本都無法使用平行索引作業。 如需 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 版本支援的功能清單，請參閱 [SQL Server 2016 的版本及支援功能](../../sql-server/editions-and-components-of-sql-server-2016.md)和 [SQL Server 2017 的版本及支援功能](../../sql-server/editions-and-components-of-sql-server-2017.md)。

DATA_COMPRESSION      
針對指定的索引、分割區編號或分割區範圍指定資料壓縮選項。 選項如下：

無      
不壓縮索引或指定的分割區。

ROW      
使用資料列壓縮來壓縮索引或指定的分割區。

PAGE      
使用頁面壓縮來壓縮索引或指定的分割區。

如需與壓縮有關的詳細資訊，請參閱[資料壓縮](../../relational-databases/data-compression/data-compression.md)。

ON PARTITIONS **(** { \<partition_number_expression> | \<range> } [ **,** ..._n_ ] **)**       
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]

指定套用 DATA_COMPRESSION 設定的分割區。 如果未分割此索引，ON PARTITIONS 引數將會產生錯誤。 如果未提供 ON PARTITIONS 子句，DATA_COMPRESSION 選項會套用到分割區索引的所有分割區。

可使用下列方式來指定 \<partition_number_expression>：

- 提供分割區的編號，例如：ON PARTITIONS (2)。
- 為數個個別分割區提供以逗號分隔的分割區編號，例如：ON PARTITIONS (1, 5)。
- 同時提供範圍和個別分割區，例如：ON PARTITIONS (2, 4, 6 TO 8)。

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

## <a name="remarks"></a>備註
CREATE INDEX 陳述式的最佳化方式與其他任何查詢一樣。 若要儲存在 I/O 作業上，查詢處理器可以選擇掃描另一個索引，而不是執行資料表掃描。 在某些情況下，排序作業可以省略。 在多處理器電腦上，CREATE INDEX 可以利用更多的處理器來執行與建立索引相關聯的掃描和排序作業，執行方式與其他查詢的執行方式相同。 如需詳細資訊，請參閱 [設定平行索引作業](../../relational-databases/indexes/configure-parallel-index-operations.md)。

如果資料庫復原模式設為大量記錄或簡單模式，建立索引作業就可以利用最低限度記錄。

索引可以在暫存資料表上建立。 當資料表卸除或工作階段結束時，就會卸除索引。

建立主索引鍵時，可以在資料表變數上建立叢集索引。 當查詢完成或工作階段結束時，也會卸除索引。

索引支援擴充屬性。

## <a name="clustered-indexes"></a>叢集索引
若要在資料表 (堆積) 上建立叢集索引，或要卸除及重新建立現有的叢集索引，則資料庫中必須有可用的其他工作空間，才能容納資料排序和原始資料表或現有叢集索引資料的暫存複本。 如需叢集索引的詳細資訊，請參閱[建立叢集索引](../../relational-databases/indexes/create-clustered-indexes.md)及 [SQL Server 索引架構和設計指南](../../relational-databases/sql-server-index-design-guide.md)。

## <a name="nonclustered-indexes"></a>非叢集索引
從 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 開始以及在 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 中，您可以在儲存為叢集資料行存放區索引的資料表上建立非叢集索引。 如果您先在儲存為堆積或叢集索引的資料表上建立非叢集索引，當您稍後將該資料表轉換為叢集資料行存放區索引時，索引將持續保留。 當您重建叢集資料行存放區索引時，也不需卸除非叢集索引。

限制事項：

- 當您在儲存為叢集資料行存放區索引的資料表上建立非叢集索引時，`FILESTREAM_ON` 選項無效。

## <a name="unique-indexes"></a>唯一索引
如果唯一索引存在，每當插入作業新增資料時，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 都會確認是否有重複的值。 可能產生重複索引鍵值的插入作業會回復，且 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 會顯示錯誤訊息。 即使插入作業變更多個資料列但只造成一個重複的值，一樣會發生這種情況。 如果嘗試輸入資料，而該資料有唯一索引，且該資料的 IGNORE_DUP_KEY 子句設為 ON，則只有違反 UNIQUE 索引規則的資料列會失敗。

## <a name="partitioned-indexes"></a>分割區索引
分割區索引與分割區資料表的建立和維護方式類似，不過，跟一般索引一樣，分割區索引會當做個別資料庫物件來處理。 未進行分割的資料表上可以有分割區索引；已進行分割的資料表上也可以有非分割區索引。

如果您要在分割區資料表上建立索引，且不指定要從中放置索引的檔案群組，則索引的分割區方式與基礎資料表相同。 這是因為索引及其基礎資料表預設會置於同一個檔案群組內，且索引會供相同分割區配置中，使用相同分割區資料行的分割區資料表使用。 當索引使用與資料表相同的資料分割配置及分割資料行時，索引將會與資料表「對齊」。

> [!WARNING]
> 您可以對包含超過 1,000 個分割區的資料表，建立及重建不以資料表為準的索引，但不予支援。 此做法可能會導致在作業期間效能降低或耗用過多記憶體。 建議當分割區數超過 1,000 時，一律使用以資料表為準的索引。

分割區不是唯一的叢集索引時若未指定分割區資料行，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 預設會將其加入叢集索引鍵清單。

您可以對分割區資料表建立索引檢視表，其方法與建立資料表索引相同。 如需資料分割索引的詳細資訊，請參閱[資料分割資料表和索引](../../relational-databases/partitions/partitioned-tables-and-indexes.md)及 [SQL Server 索引架構和設計指南](../../relational-databases/sql-server-index-design-guide.md)。

[!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 並不會在建立或重建分割區索引之後掃描資料表中所有的資料列建立統計資料。 反之，查詢最佳化工具會使用預設的採樣演算法來產生統計資料。 若要在掃描資料表中所有資料列時取得分割區索引的統計資料，使用子句 `FULLSCAN` 時請使用 `CREATE STATISTICS` 或 `UPDATE STATISTICS`。

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
  
     * 當資料庫的相容性層級設定為 90 或以上時，將 ANSI_WARNINGS 設定為 ON 也會將 ARITHABORT 隱含設定為 ON。 如果資料庫的相容性層級設定為 80 或更低，ARITHABORT 選項就必須明確地設定為 ON。

當 SET 選項不正確時，可能會發生下列狀況：

- 不會建立已篩選的索引。
- [!INCLUDE[ssDE](../../includes/ssde-md.md)] 會產生錯誤，並回復可變更索引中資料的 INSERT、UPDATE、DELETE 或 MERGE 陳述式。
- 查詢最佳化工具不會針對任何 Transact-SQL 陳述式考量執行計畫中的索引。

 如需篩選索引的詳細資訊，請參閱[建立篩選索引](../../relational-databases/indexes/create-filtered-indexes.md)及 [SQL Server 索引架構和設計指南](../../relational-databases/sql-server-index-design-guide.md)。

## <a name="spatial-indexes"></a>空間索引
如需空間索引的資訊，請參閱 [CREATE SPATIAL INDEX](../../t-sql/statements/create-spatial-index-transact-sql.md) 和[空間索引概觀](../../relational-databases/spatial/spatial-indexes-overview.md)。

## <a name="xml-indexes"></a>XML 索引
如需 XML 索引的資訊，請參閱 [CREATE XML INDEX](../../t-sql/statements/create-xml-index-transact-sql.md) 和 [XML 索引 &#40;SQL Server&#41;](../../relational-databases/xml/xml-indexes-sql-server.md)。

## <a name="index-key-size"></a>索引鍵大小
叢集索引的索引鍵大小上限為 900 個位元組，而非叢集索引為 1,700 個位元組 (在 [!INCLUDE[ssSDS](../../includes/sssds-md.md)] 和 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 之前，限制一律為 900 個位元組)。如果資料行中現有資料未超出建立索引時的限制，則可在 **varchar** 資料行上建立超過位元組限制的索引；但是，如果後續在資料行進行插入或更新動作時造成總計大小超過限制，則動作會失敗。 叢集索引的索引鍵所包含的 **varchar** 資料行不能在 ROW_OVERFLOW_DATA 配置單位中有現有的資料。 如果在 **varchar** 資料行上建立叢集索引，且現有的資料在 IN_ROW_DATA 配置單位中，則後續在可能發送資料非資料列的資料行上進行的插入或更新動作會失敗。

非叢集索引可將非索引鍵資料行併入索引的分葉層級中。 在計算索引鍵大小時，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 不會考量這些資料行。 如需詳細資訊，請參閱[建立內含資料行的索引](../../relational-databases/indexes/create-indexes-with-included-columns.md)及 [SQL Server 索引架構和設計指南](../../relational-databases/sql-server-index-design-guide.md)。

> [!NOTE]
> 分割資料表時，如果分割區索引鍵資料行原本不存在非唯一的叢集索引中，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 就會將它們加入至索引。 在非唯一的叢集索引中，索引資料行 (不計算包含的資料行) 再加上任何加入之分割區資料行的組合大小不得超過 1800 個位元組。

## <a name="computed-columns"></a>計算資料行
索引可以在計算資料行上建立。 此外，計算資料行可以有 PERSISTED 屬性。 這表示 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 會將計算值儲存在資料表中，並在更新計算資料行所根據的任何其他資料行時更新這些計算值。 當 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 在資料行上建立某索引，且查詢中參考該索引時，它會使用這些保存值。

若要為計算資料行建立索引，則計算資料行必須具決定性且精確。 不過，您可以利用 PERSISTED 屬性，擴充可建立索引的計算資料行類型，使其包括：

- 根據 [!INCLUDE[tsql](../../includes/tsql-md.md)] 和 CLR 函數及使用者標示為具決定性的 CLR 使用者定義型別方法所產生的計算資料行。
- 根據具決定性 (如 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 所定義) 但不精確的運算式所產生的計算資料行。

保存的計算資料行需要設定前一節[篩選索引的必要 SET 選項](#required-set-options-for-filtered-indexes)中所顯示的下列 SET 選項。

UNIQUE 或 PRIMARY KEY 條件約束只要符合索引作業的所有條件就可以包含計算資料行。 更具體的說法是，計算資料行必須具決定性且精確，或是具決定性且一直保存。 如需確定性的詳細資訊，請參閱[決定性與非決定性函數](../../relational-databases/user-defined-functions/deterministic-and-nondeterministic-functions.md)。

從 **image**、**ntext**、**text**、**varchar(max)** 、**nvarchar(max)** 、**varbinary(max)** 及 **xml** 資料類型衍生的計算資料行，只要計算資料行資料類型可作為索引鍵資料行或非索引鍵資料行，就可編製索引以作為索引鍵或內含的非索引鍵資料行。 例如，您無法在計算的 **xml** 資料行上建立主要 XML 索引。 如果索引鍵大小超過 900 個位元組，畫面上會顯示警告訊息。

在計算資料行上建立索引，可能會使先前有效的插入或更新作業失敗。 當計算資料行導致算術錯誤時，就可能發生這類失敗。 例如在下表中，雖然計算資料行 `c` 導致算術錯誤，但 INSERT 陳述式仍可運作。

```sql
CREATE TABLE t1 (a INT, b INT, c AS a/b);
INSERT INTO t1 VALUES (1, 0);
```

但是，如果在建立資料表之後，您在計算資料行 `c` 上建立索引，相同的 `INSERT` 陳述式在這種情況下則會失敗。

```sql
CREATE TABLE t1 (a INT, b INT, c AS a/b);
CREATE UNIQUE CLUSTERED INDEX Idx1 ON t1(c);
INSERT INTO t1 VALUES (1, 0);
```

如需詳細資訊，請參閱 [計算資料行的索引](../../relational-databases/indexes/indexes-on-computed-columns.md)。

## <a name="included-columns-in-indexes"></a>將資料行併入索引中
您可以將非索引鍵資料行 (稱為內含資料行) 加入至非叢集索引的分葉層級，以處理查詢來提升查詢效能。 換句話說，查詢中參考的所有資料行都會併入索引中當做索引鍵資料行或非索引鍵資料行。 這可讓查詢最佳化工具從索引掃描中尋找所有的必要資訊；但不存取資料表或叢集索引。 如需詳細資訊，請參閱[建立內含資料行的索引](../../relational-databases/indexes/create-indexes-with-included-columns.md)及 [SQL Server 索引架構和設計指南](../../relational-databases/sql-server-index-design-guide.md)。

## <a name="specifying-index-options"></a>指定索引選項
[!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] 導入新的索引選項，並修改選項的指定方式。 在回溯相容語法中，WITH *option_name* 相當於 WITH **(** \<option_name> **= ON )** 。 當您設定索引選項時，適用下列規則：

- 只能使用 WITH (**_option\_name_ = ON | OFF**) 來指定新的索引選項。
- 不能在相同的陳述式中同時利用與舊版本相容的語法和新語法來指定選項。 例如，指定 WITH (**DROP_EXISTING, ONLINE = ON**) 會造成陳述式失敗。
- 當您建立 XML 索引時，必須搭配 WITH (**_option_name_= ON | OFF**) 來指定選項。

## <a name="drop_existing-clause"></a>DROP_EXISTING 子句
您可以利用 DROP_EXISTING 子句來重建索引、加入或卸除資料行、修改選項、修改資料行排序次序，或變更分割區配置或檔案群組。

如果索引強制執行 PRIMARY KEY 或 UNIQUE 條件約束，且索引定義完全沒有變更，則會卸除索引並重新建立索引以保留現有的條件約束。 不過，如果索引定義變更了，陳述式就會失敗。 若要變更 PRIMARY KEY 或 UNIQUE 條件約束的定義，請卸除該條件約束，然後利用新的定義來新增條件約束。

當您在一份也有非叢集索引的資料表上利用一組相同或不同的索引鍵來重新建立叢集索引時，DROP_EXISTING 可以增強效能。 DROP_EXISTING 會取代舊叢集索引上之 DROP INDEX 陳述式的執行，然後再針對新叢集索引執行 CREATE INDEX 陳述式。 非叢集索引只重建一次，之後，只有在索引定義變更時才會再重建。 當索引定義的索引名稱、索引鍵資料行和分割區資料行、唯一性屬性及排序次序與原始索引相同時，DROP_EXISTING 子句不會重建非叢集索引。

不論非叢集索引是否重建，它們一律會保留在它們的原始檔案群組或分割區配置中，且會使用原始的分割區函數。 如果將叢集索引重建至不同的檔案群組或分割區配置中，則不會移動非叢集索引來符合叢集索引的新位置。 因此，即使非叢集索引先前與叢集索引對齊，它們也可能不再與叢集索引對齊。 如需資料分割索引對齊的詳細資訊，請參閱[資料分割資料表與索引](../../relational-databases/partitions/partitioned-tables-and-indexes.md)。

除非索引陳述式指定非叢集索引且 ONLINE 選項設為 OFF，否則，如果您以相同的順序使用相同的索引鍵資料行，且相同的索引鍵資料行含有相同的遞增或遞減順序，則 DROP_EXISTING 子句不會再次排序資料。 如果叢集索引已停用，則執行 CREATE INDEX WITH DROP_EXISTING 作業時必須將 ONLINE 設為 OFF。 如果非叢集索引已停用，且與停用的叢集索引無關，則執行 CREATE INDEX WITH DROP_EXISTING 作業時可以將 ONLINE 設為 OFF，也可以將它設為 ON。

> [!NOTE]
> 當卸除或重新建立含有 128 個 (含) 以上之範圍的索引時，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 會延遲實際的頁面取消配置及其相關聯的鎖定，直到認可交易之後。

## <a name="online-option"></a>ONLINE 選項
以線上方式執行索引作業時，適用下列方針：

- 當線上索引作業進行中時，不能變更、截斷或卸除基礎資料表。
- 在索引作業進行期間，需要其他暫存磁碟空間。
- 線上作業可在下列索引上執行：分割區索引，以及包含保存的計算資料行或內含資料行的索引。

如需詳細資訊，請參閱 [Perform Index Operations Online](../../relational-databases/indexes/perform-index-operations-online.md)。

### <a name="resumable-index-operations"></a><a name="resumable-indexes"></a> 可繼續的索引作業
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]

可繼續索引作業適用下列方針：

- 線上索引建立已使用 `RESUMABLE = ON` 選項指定為可繼續的作業。
- 指定索引的中繼資料中不會保存 RESUMABLE 選項，並且僅適用於目前 DDL 陳述式的持續時間。 因此，必須明確指定 `RESUMABLE = ON` 子句，才能啟用可繼續性。
- 只有 `RESUMABLE = ON` 選項支援 MAX_DURATION 選項。
- RESUMABLE 選項的 MAX_DURATION 可指定建置索引時的時間間隔。 使用此時間之後，索引建置就會暫停或完成執行。 使用者可決定何時可以繼續已暫停索引的建置。 MAX_DURATION 的 **時間** 是以分鐘計算，且必須大於 0 分鐘，並少於或等於一週 (7 \* 24 \* 60 = 10080 分鐘)。 索引作業長時間暫停可能會影響特定資料表上的 DML 效能，以及影響資料庫磁碟容量，因為原始索引和新建立的索引都需要磁碟空間，且需要在 DML 作業期間更新。 如果省略 MAX_DURATION 選項，索引作業將會繼續執行直到完成或發生失敗為止。
- 若要立即暫停索引作業，您可以停止 (Ctrl-C) 進行中的命令，執行 [ALTER INDEX](alter-index-transact-sql.md) PAUSE 命令，或執行 `KILL <session_id>` 命令。 暫停命令之後，可以使用 [ALTER INDEX](alter-index-transact-sql.md) 命令繼續執行該命令。
- 重新執行可繼續索引的原始 CREATE INDEX 陳述式，會自動繼續已暫停索引的建立作業。
- 可繼續的索引不支援 `SORT_IN_TEMPDB = ON` 選項。
- `RESUMABLE = ON` 的 DDL 命令無法在明確交易內部執行 (不能是開始 TRAN...COMMIT 區塊的一部份)。
- 若要繼續/中止建立/重建索引，請使用 [ALTER INDEX](alter-index-transact-sql.md) T-SQL 語法

> [!NOTE]
> DDL 命令會執行，直到完成、暫停或失敗為止。 如果命令暫停，將會發出錯誤指出作業已暫停，而且沒有完成索引建立。 您可以從 [sys.index_resumable_operations](../../relational-databases/system-catalog-views/sys-index-resumable-operations.md) 取得目前索引狀態的詳細資訊。 和以前一樣，如果發生失敗，也會發出錯誤。

若要指出索引建立是以可繼續的作業來執行，以及檢查其目前的執行狀態，請參閱 [sys.index_resumable_operations](../../relational-databases/system-catalog-views/sys-index-resumable-operations.md)。

#### <a name="resources"></a>資源
下列資源是可繼續線上索引建立操作的必要項目：

- 需要額外的空間以保留正在建立的索引，包括索引的暫停時間
- 進行排序階段時的額外記錄檔輸送量。 相較於一般線上索引建立，可繼續索引的整體記錄檔空間使用量較小，而且在此作業期間允許記錄截斷。
- 可防止進行任何 DDL 修改的 DDL 狀態
- 在作業暫停期間以及作業執行時，內建索引上都會封鎖準刪除清除。

#### <a name="current-functional-limitations"></a>目前的功能限制
下列功能已針對可繼續索引建立操作停用：

- 在可繼續的線上索引建立作業暫停之後，即無法變更 MAXDOP 的初始值
- 建立包含下列項目的索引：

  - 作為索引鍵資料行的計算或 TIMESTAMP 資料行
  - LOB 資料行作為可繼續索引建立的內含資料行
  - 已篩選的索引

## <a name="row-and-page-locks-options"></a>資料列和頁面鎖定選項
如果 `ALLOW_ROW_LOCKS = ON` 且 `ALLOW_PAGE_LOCK = ON`，您存取索引時就允許資料列層級、頁面層級和資料表層級的鎖定。 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 會選擇適當的鎖定，且可以將鎖定從資料列或頁面鎖定擴大到資料表鎖定。

如果 `ALLOW_ROW_LOCKS = OFF` 且 `ALLOW_PAGE_LOCK = OFF`，您存取索引時，只允許資料表層級的鎖定。

## <a name="sequential-keys"></a>循序索引鍵
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 

最後一頁的插入競爭是大量並行的執行緒在嘗試將資料列插入具備循序索引鍵的索引時，經常發生的一種效能問題。 當開頭的索引鍵資料行包含總是在增加 (或減少) 的值 (例如識別欄位或預設為目前日期/時間的日期) 時，便會將索引視為循序。 因為要插入的索引鍵是循序的，所有新的資料列都會在索引結構的結尾插入，也就是位於相同的頁面。 這會導致頁面在記憶體中互相競爭，可觀察到的結果就是數個執行緒不明所以地在 PAGELATCH_EX 持續等待頁面。

開啟 OPTIMIZE_FOR_SEQUENTIAL_KEY 索引選項，可在資料庫引擎中啟用最佳化，協助改善對索引進行高並行插入時的輸送量。 它適用於因具備循序索引鍵，而可能發生最後一頁插入競爭的索引，但它也可以協助在 B 型樹狀索引結構中其它區域內包含熱點的索引。

## <a name="viewing-index-information"></a>檢視索引資訊
若要傳回有關索引的資訊，您可以使用目錄檢視、系統函數和系統預存程序。

## <a name="data-compression"></a>資料壓縮
[資料壓縮](../../relational-databases/data-compression/data-compression.md)主題中會描述資料壓縮。 以下是考量的幾個要點：

- 壓縮可讓更多的資料列儲存在頁面上，但是不會變更最大資料列大小。
- 索引的非分葉頁面不會進行頁面壓縮，但是可以進行資料列壓縮。
- 每一個非叢集索引都有個別的壓縮設定，而且不會繼承基礎資料表的壓縮設定。
- 在堆積上建立叢集索引時，此叢集索引會繼承堆積的壓縮狀態，除非指定了替代的壓縮狀態。

下列限制適用於分割區索引：

- 您無法在資料表具有非對齊索引時變更單一分割區的壓縮設定。
- The ALTER INDEX \<index> ...REBUILD PARTITION ... 語法會重建此索引的指定分割區。
- The ALTER INDEX \<index> ...REBUILD WITH ... 語法會重建此索引的所有分割區。

若要評估變更壓縮狀態如何影響資料表、索引或分割區，請使用 [sp_estimate_data_compression_savings](../../relational-databases/system-stored-procedures/sp-estimate-data-compression-savings-transact-sql.md) 預存程序。

## <a name="permissions"></a>權限
必須具備資料表或檢視的 `ALTER` 權限。 使用者必須是 `sysadmin` 固定伺服器角色的成員，或 `db_ddladmin` 或 `db_owner` 固定資料庫角色的成員。

## <a name="limitations-and-restrictions"></a>限制事項
在 [!INCLUDE[ssSDW](../../includes/sssdw-md.md)] 和 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] 中，您無法建立：

- 當資料行存放區索引已存在時，無法在資料倉儲資料表上建立叢集或非叢集資料列存放區索引。 此行為與 SMP [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 不同，後者可讓資料列存放區和資料行存放區索引共存於相同的資料表上。
- 您無法在檢視上建立索引。

## <a name="metadata"></a>中繼資料
若要檢視現有索引的資訊，您可以查詢 [sys.indexes](../../relational-databases/system-catalog-views/sys-indexes-transact-sql.md) 目錄檢視。

## <a name="version-notes"></a>版本資訊

[!INCLUDE[ssSDS](../../includes/sssds-md.md)] 不支援 filegroup 和 filestream 選項。

## <a name="examples-all-versions-uses-the-adventureworks-database"></a>範例：所有版本。 使用 AdventureWorks 資料庫

### <a name="a-create-a-simple-nonclustered-rowstore-index"></a>A. 建立簡單的非叢集資料列存放區索引
下列範例會在 `Purchasing.ProductVendor` 資料表的 `VendorID` 資料行上建立非叢集索引。

```sql
CREATE INDEX IX_VendorID ON ProductVendor (VendorID);
CREATE INDEX IX_VendorID ON dbo.ProductVendor (VendorID DESC, Name ASC, Address DESC);
CREATE INDEX IX_VendorID ON Purchasing..ProductVendor (VendorID);
```

### <a name="b-create-a-simple-nonclustered-rowstore-composite-index"></a>B. 建立簡單的非叢集資料列存放區複合式索引
下列範例會在 `Sales.SalesPerson` 資料表的 `SalesQuota` 和 `SalesYTD` 資料行上建立非叢集複合式索引。

```sql
CREATE NONCLUSTERED INDEX IX_SalesPerson_SalesQuota_SalesYTD ON Sales.SalesPerson (SalesQuota, SalesYTD);
```

### <a name="c-create-an-index-on-a-table-in-another-database"></a>C. 在另一個資料庫的資料表上建立索引
下列範例會在 `Purchasing` 資料庫中 `ProductVendor` 資料表的 `VendorID` 資料行上建立叢集索引。

```sql
CREATE CLUSTERED INDEX IX_ProductVendor_VendorID ON Purchasing..ProductVendor (VendorID);
```

### <a name="d-add-a-column-to-an-index"></a>D. 將資料行加入至索引
下列範例會使用 dbo.FactFinance 資料表的兩個資料行建立索引 IX_FF。 下一個陳述式會使用多個資料行重建索引，並保留現有的名稱。

```sql
CREATE INDEX IX_FF ON dbo.FactFinance (FinanceKey ASC, DateKey ASC);

-- Rebuild and add the OrganizationKey
CREATE INDEX IX_FF ON dbo.FactFinance (FinanceKey, DateKey, OrganizationKey DESC)
  WITH (DROP_EXISTING = ON);
```

## <a name="examples-sql-server-azure-sql-database"></a>範例：SQL Server、Azure SQL Database

### <a name="e-create-a-unique-nonclustered-index"></a>E. 建立唯一的非叢集索引
下列範例會在 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 資料庫中 `Name` 資料表的 `Production.UnitMeasure` 資料行上建立唯一非叢集索引。 索引會強制將資料上的唯一性插入 `Name` 資料行中。

```sql
CREATE UNIQUE INDEX AK_UnitMeasure_Name
  ON Production.UnitMeasure(Name);
```

下列查詢會藉由嘗試插入與現有資料列具有相同值的資料列，以測試條件約束的唯一性。

```sql
-- Verify the existing value.
SELECT Name FROM Production.UnitMeasure WHERE Name = N'Ounces';
GO

INSERT INTO Production.UnitMeasure (UnitMeasureCode, Name, ModifiedDate)
  VALUES ('OC', 'Ounces', GETDATE());
```

產生的錯誤訊息如下：

```
Server: Msg 2601, Level 14, State 1, Line 1
Cannot insert duplicate key row in object 'UnitMeasure' with unique index 'AK_UnitMeasure_Name'. The statement has been terminated.
```

### <a name="f-use-the-ignore_dup_key-option"></a>F. 使用 IGNORE_DUP_KEY 選項
下列範例分別利用兩種不同的選項設定 (先將選項設為 `IGNORE_DUP_KEY`，再將選項設為 `ON`) 將多個資料列插入暫存資料表中，示範 `OFF` 選項的效果。 單一資料列會插入 `#Test` 資料表中，該資料表則會在第二個多重資料列 `INSERT` 陳述式執行時刻意造成重複的值。 資料表中的資料列計數會傳回所插入的資料列數目。

```sql
CREATE TABLE #Test (C1 NVARCHAR(10), C2 NVARCHAR(50), C3 DATETIME);
GO

CREATE UNIQUE INDEX AK_Index ON #Test (C2)
  WITH (IGNORE_DUP_KEY = ON);
GO

INSERT INTO #Test VALUES (N'OC', N'Ounces', GETDATE());
INSERT INTO #Test SELECT * FROM Production.UnitMeasure;
GO

SELECT COUNT(*) AS [Number of rows] FROM #Test;
GO

DROP TABLE #Test;
GO
```

以下是第二個 `INSERT` 陳述式的結果。

```cmd
Server: Msg 3604, Level 16, State 1, Line 5 Duplicate key was ignored.

Number of rows
--------------
38
```

請注意，從 `Production.UnitMeasure` 資料表插入之未違反唯一性條件約束的資料列已順利插入。 發出警告且忽略重複的資料列，但不回復整個交易。

重新執行相同的陳述式，但將 `IGNORE_DUP_KEY` 設為 `OFF`。

```sql
CREATE TABLE #Test (C1 NVARCHAR(10), C2 NVARCHAR(50), C3 DATETIME);
GO

CREATE UNIQUE INDEX AK_Index ON #Test (C2)
  WITH (IGNORE_DUP_KEY = OFF);
GO

INSERT INTO #Test VALUES (N'OC', N'Ounces', GETDATE());
INSERT INTO #Test SELECT * FROM Production.UnitMeasure;
GO

SELECT COUNT(*) AS [Number of rows] FROM #Test;
GO

DROP TABLE #Test;
GO
```

以下是第二個 `INSERT` 陳述式的結果。

```
Server: Msg 2601, Level 14, State 1, Line 5
Cannot insert duplicate key row in object '#Test' with unique index
'AK_Index'. The statement has been terminated.

Number of rows
--------------
1
```

請注意，即便 `Production.UnitMeasure` 資料表只有一個資料列違反 `UNIQUE` 索引條件約束，皆會導致資料表中所有的資料列無法插入資料表。

### <a name="g-using-drop_existing-to-drop-and-re-create-an-index"></a>G. 使用 DROP_EXISTING 卸除及重新建立索引
下列範例會利用 `ProductID` 選項，在 `Production.WorkOrder` 資料庫中 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 資料表的 `DROP_EXISTING` 資料行上卸除及重新建立現有的索引。 也會設定 `FILLFACTOR` 和 `PAD_INDEX` 選項。

```sql
CREATE NONCLUSTERED INDEX IX_WorkOrder_ProductID
  ON Production.WorkOrder(ProductID)
    WITH (FILLFACTOR = 80,
      PAD_INDEX = ON,
      DROP_EXISTING = ON);
GO
```

### <a name="h-create-an-index-on-a-view"></a>H. 在檢視表上建立索引
下列範例會在該檢視表上建立檢視表和索引。 內含使用索引檢視的兩項查詢。

```sql
-- Set the options to support indexed views
SET NUMERIC_ROUNDABORT OFF;
SET ANSI_PADDING, ANSI_WARNINGS, CONCAT_NULL_YIELDS_NULL, ARITHABORT,
  QUOTED_IDENTIFIER, ANSI_NULLS ON;
GO

-- Create view with schemabinding
IF OBJECT_ID ('Sales.vOrders', 'view') IS NOT NULL
  DROP VIEW Sales.vOrders;
GO

CREATE VIEW Sales.vOrders
  WITH SCHEMABINDING
AS
  SELECT SUM(UnitPrice * OrderQty * (1.00 - UnitPriceDiscount)) AS Revenue,
    OrderDate, ProductID, COUNT_BIG(*) AS COUNT
  FROM Sales.SalesOrderDetail AS od, Sales.SalesOrderHeader AS o
  WHERE od.SalesOrderID = o.SalesOrderID
  GROUP BY OrderDate, ProductID;
GO

-- Create an index on the view
CREATE UNIQUE CLUSTERED INDEX IDX_V1
  ON Sales.vOrders (OrderDate, ProductID);
GO

-- This query can use the indexed view even though the view is
-- not specified in the FROM clause.
SELECT SUM(UnitPrice * OrderQty * (1.00 - UnitPriceDiscount)) AS Rev,
  OrderDate, ProductID
FROM Sales.SalesOrderDetail AS od
  JOIN Sales.SalesOrderHeader AS o ON od.SalesOrderID = o.SalesOrderID
    AND ProductID BETWEEN 700 AND 800
    AND OrderDate >= CONVERT(DATETIME, '05/01/2002', 101)
GROUP BY OrderDate, ProductID
ORDER BY Rev DESC;
GO

-- This query can use the above indexed view
SELECT OrderDate, SUM(UnitPrice * OrderQty * (1.00 - UnitPriceDiscount)) AS Rev
FROM Sales.SalesOrderDetail AS od
  JOIN Sales.SalesOrderHeader AS o ON od.SalesOrderID = o.SalesOrderID
    AND DATEPART(mm, OrderDate) = 3
  AND DATEPART(yy, OrderDate) = 2002
GROUP BY OrderDate
ORDER BY OrderDate ASC;
GO
```

### <a name="i-create-an-index-with-included-non-key-columns"></a>I. 使用內含的 (非索引鍵) 資料行建立索引
下列範例會利用一個索引鍵資料行 (`PostalCode`) 和四個非索引鍵資料行 (`AddressLine1`、`AddressLine2`、`City`、`StateProvinceID`) 來建立非叢集索引。 其後有一個由索引處理的查詢。 若要顯示查詢最佳化工具所選取的索引，請先在 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 的 [查詢] 功能表上選取 [Display Actual Execution Plan] \(顯示實際執行計畫\)，然後再執行查詢。

```sql
CREATE NONCLUSTERED INDEX IX_Address_PostalCode
  ON Person.Address (PostalCode)
  INCLUDE (AddressLine1, AddressLine2, City, StateProvinceID);
GO

SELECT AddressLine1, AddressLine2, City, StateProvinceID, PostalCode
FROM Person.Address
WHERE PostalCode BETWEEN N'98000' and N'99999';
GO
```

### <a name="j-create-a-partitioned-index"></a>J. 建立資料分割索引
下列範例會在 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 資料庫中現有的分割區配置 `TransactionsPS1` 上建立非叢集分割區索引。 此範例假設您已安裝分割區索引範例。

**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]

```sql
CREATE NONCLUSTERED INDEX IX_TransactionHistory_ReferenceOrderID
  ON Production.TransactionHistory (ReferenceOrderID)
  ON TransactionsPS1 (TransactionDate);
GO
```

### <a name="k-creating-a-filtered-index"></a>K. 建立篩選的索引
下列範例會對 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 資料庫中的 Production.BillOfMaterials 資料表建立篩選的索引。 篩選述詞可以包含在已篩選之索引中不是索引鍵資料行的資料行。 此範例中的述詞只會選取 EndDate 不是 NULL 的資料列。

```sql
CREATE NONCLUSTERED INDEX "FIBillOfMaterialsWithEndDate"
  ON Production.BillOfMaterials (ComponentID, StartDate)
  WHERE EndDate IS NOT NULL;
```

### <a name="l-create-a-compressed-index"></a>L. 建立壓縮索引
下列範例會使用資料列壓縮，在非分割區資料表上建立索引。

```sql
CREATE NONCLUSTERED INDEX IX_INDEX_1
  ON T1 (C2)
  WITH (DATA_COMPRESSION = ROW);
GO
```

下列範例會在索引的所有分割區上使用資料列壓縮，以便在分割區資料表上建立索引。

```sql
CREATE CLUSTERED INDEX IX_PartTab2Col1
  ON PartitionTable1 (Col1)
  WITH (DATA_COMPRESSION = ROW);
GO
```

 下列範例會在分割區資料表上建立索引，其方式是在索引的分割區 `1` 上使用頁面壓縮，並在索引的分割區 `2` 到 `4` 上使用資料列壓縮。

```sql
CREATE CLUSTERED INDEX IX_PartTab2Col1
  ON PartitionTable1 (Col1)
  WITH (
    DATA_COMPRESSION = PAGE ON PARTITIONS(1),
    DATA_COMPRESSION = ROW ON PARTITIONS (2 TO 4)
  );
GO
```

### <a name="m-create-resume-pause-and-abort-resumable-index-operations"></a>M. 建立、繼續、暫停及中止可繼續的索引作業
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]

```sql
-- Execute a resumable online index create statement with MAXDOP=1
CREATE INDEX test_idx1 ON test_table (col1) WITH (ONLINE = ON, MAXDOP = 1, RESUMABLE = ON);

-- Executing the same command again (see above) after an index operation was paused, resumes automatically the index create operation.

-- Execute a resumable online index creates operation with MAX_DURATION set to 240 minutes. After the time expires, the resumable index create operation is paused.
CREATE INDEX test_idx2 ON test_table (col2) WITH (ONLINE = ON, RESUMABLE = ON, MAX_DURATION = 240);

-- Pause a running resumable online index creation
ALTER INDEX test_idx1 ON test_table PAUSE;
ALTER INDEX test_idx2 ON test_table PAUSE;

-- Resume a paused online index creation
ALTER INDEX test_idx1 ON test_table RESUME;
ALTER INDEX test_idx2 ON test_table RESUME;

-- Abort resumable index create operation which is running or paused
ALTER INDEX test_idx1 ON test_table ABORT;
ALTER INDEX test_idx2 ON test_table ABORT;
```

## <a name="examples-sssdwfull-and-sspdw"></a>範例：[!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 和 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]

### <a name="n-basic-syntax"></a>N. 基本語法
建立、繼續、暫停及中止可繼續的索引作業       

**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]

```sql
-- Execute a resumable online index create statement with MAXDOP=1
CREATE INDEX test_idx ON test_table WITH (ONLINE = ON, MAXDOP = 1, RESUMABLE = ON);

-- Executing the same command again (see above) after an index operation was paused, resumes automatically the index create operation.

-- Execute a resumable online index creates operation with MAX_DURATION set to 240 minutes. After the time expires, the resumable index create operation is paused.
CREATE INDEX test_idx ON test_table WITH (ONLINE = ON, RESUMABLE = ON, MAX_DURATION = 240);

-- Pause a running resumable online index creation
ALTER INDEX test_idx ON test_table PAUSE;

-- Resume a paused online index creation
ALTER INDEX test_idx ON test_table RESUME;

-- Abort resumable index create operation which is running or paused
ALTER INDEX test_idx ON test_table ABORT;
```

### <a name="o-create-a-nonclustered-index-on-a-table-in-the-current-database"></a>O. 在目前資料庫的資料表上建立非叢集索引
下列範例會在 `VendorID`資料表的 `ProductVendor` 資料行上建立非叢集索引。

```sql
CREATE INDEX IX_ProductVendor_VendorID
  ON ProductVendor (VendorID);
```

### <a name="p-create-a-clustered-index-on-a-table-in-another-database"></a>P. 在另一個資料庫的資料表上建立叢集索引
下列範例會在 `VendorID` 資料庫中 `ProductVendor` 資料表的 `Purchasing` 資料行上建立非叢集索引。

```sql
CREATE CLUSTERED INDEX IX_ProductVendor_VendorID
  ON Purchasing..ProductVendor (VendorID);
```
### <a name="q-create-an-ordered-clustered-index-on-a-table"></a>Q. 在資料表上建立已排序的叢集索引  
下列範例會在 `MyDB` 資料庫中 `T1` 資料表的 `c1` 和 `c2` 資料行上建立已排序叢集索引。

```sql
CREATE CLUSTERED COLUMNSTORE INDEX MyOrderedCCI ON MyDB.dbo.T1 
ORDER (c1, c2);

```

### <a name="r-convert-a-cci-to-an-ordered-clustered-index-on-a-table"></a>R. 在資料表上將 CCI 轉換成已排序的叢集索引  
下列範例會將現有叢集資料行存放區索引轉換成已排序的叢集資料行存放區索引，其稱為 `MyOrderedCCI` (位於 `MyDB` 資料庫中 `T2` 資料表的 `c1` 和 `c2` 資料行)。

```sql
CREATE CLUSTERED COLUMNSTORE INDEX MyOrderedCCI ON MyDB.dbo.T2
ORDER (c1, c2)
WITH (DROP_EXISTING = ON);

```

## <a name="see-also"></a>另請參閱
[SQL Server 索引架構和設計指南](../../relational-databases/sql-server-index-design-guide.md)     
[線上執行索引作業](../../relational-databases/indexes/perform-index-operations-online.md)  
[索引和 ALTER TABLE](../../t-sql/statements/alter-table-transact-sql.md#indexes-and-alter-table)     
[ALTER INDEX](../../t-sql/statements/alter-index-transact-sql.md)     
[CREATE PARTITION FUNCTION](../../t-sql/statements/create-partition-function-transact-sql.md)     
[CREATE PARTITION SCHEME](../../t-sql/statements/create-partition-scheme-transact-sql.md)     
[CREATE SPATIAL INDEX](../../t-sql/statements/create-spatial-index-transact-sql.md)      
[CREATE STATISTICS](../../t-sql/statements/create-statistics-transact-sql.md)     
[CREATE TABLE](../../t-sql/statements/create-table-transact-sql.md)    
[CREATE XML INDEX](../../t-sql/statements/create-xml-index-transact-sql.md)     
[資料類型](../../t-sql/data-types/data-types-transact-sql.md)    
[DBCC SHOW_STATISTICS](../../t-sql/database-console-commands/dbcc-show-statistics-transact-sql.md)    
[DROP INDEX](../../t-sql/statements/drop-index-transact-sql.md)    
[XML 索引 &#40;SQL Server&#41;](../../relational-databases/xml/xml-indexes-sql-server.md)     
[sys.indexes](../../relational-databases/system-catalog-views/sys-indexes-transact-sql.md)     
[sys.index_columns](../../relational-databases/system-catalog-views/sys-index-columns-transact-sql.md)    
[sys.xml_indexes](../../relational-databases/system-catalog-views/sys-xml-indexes-transact-sql.md)     
[EVENTDATA](../../t-sql/functions/eventdata-transact-sql.md)