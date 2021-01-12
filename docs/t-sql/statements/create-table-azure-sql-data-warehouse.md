---
description: CREATE TABLE (Azure Synapse Analytics)
title: CREATE TABLE (Azure Synapse Analytics) | Microsoft Docs
ms.custom: ''
ms.date: 07/03/2019
ms.service: sql-data-warehouse
ms.reviewer: ''
ms.topic: language-reference
dev_langs:
- TSQL
ms.assetid: ea21c73c-40e8-4c54-83d4-46ca36b2cf73
author: julieMSFT
ms.author: jrasnick
monikerRange: '>= aps-pdw-2016 || = azure-sqldw-latest'
ms.openlocfilehash: 733b129c9dd4f598e75baa31f49a4f9811edd5b8
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98102351"
---
# <a name="create-table-azure-synapse-analytics"></a>CREATE TABLE (Azure Synapse Analytics)

[!INCLUDE[applies-to-version/asa-pdw](../../includes/applies-to-version/asa-pdw.md)]

  在 [!INCLUDE[ssSDW](../../includes/sssdw-md.md)] 或 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] 中建立新的資料表。  

若要了解資料表及其使用方式，請參閱 [[!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)]中的資料表](https://azure.microsoft.com/documentation/articles/sql-data-warehouse-tables-overview/)。

> [!NOTE]
>  除非另有註明，否則本文中有關 [!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] 討論都適用於 [!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] 和 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]。

 ![文章連結圖示](../../database-engine/configure-windows/media/topic-link.gif "文章連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  

<a name="Syntax"></a>

## <a name="syntax"></a>語法
  
```syntaxsql
-- Create a new table.
CREATE TABLE { database_name.schema_name.table_name | schema_name.table_name | table_name }
    ( 
      { column_name <data_type>  [ <column_options> ] } [ ,...n ]
    )  
    [ WITH ( <table_option> [ ,...n ] ) ]  
[;]  

<column_options> ::=
    [ COLLATE Windows_collation_name ]  
    [ NULL | NOT NULL ] -- default is NULL  
    [ <column_constraint> ]

<column_constraint>::=
    {
        DEFAULT DEFAULT constant_expression
        | PRIMARY KEY NONCLUSTERED  NOT ENFORCED -- Applies to Azure Synapse Analytics only
        | UNIQUE NOT ENFORCED -- Applies to Azure Synapse Analytics only
    }

<table_option> ::=
    {
       CLUSTERED COLUMNSTORE INDEX --default for Azure Synapse Analytics 
      | CLUSTERED COLUMNSTORE INDEX ORDER (column [,...n])  
      | HEAP --default for Parallel Data Warehouse
      | CLUSTERED INDEX ( { index_column_name [ ASC | DESC ] } [ ,...n ] ) -- default is ASC
    }  
    {
        DISTRIBUTION = HASH ( distribution_column_name )
      | DISTRIBUTION = ROUND_ROBIN -- default for Azure Synapse Analytics
      | DISTRIBUTION = REPLICATE -- default for Parallel Data Warehouse
    }
    | PARTITION ( partition_column_name RANGE [ LEFT | RIGHT ] -- default is LEFT  
        FOR VALUES ( [ boundary_value [,...n] ] ) )

<data type> ::=
      datetimeoffset [ ( n ) ]  
    | datetime2 [ ( n ) ]  
    | datetime  
    | smalldatetime  
    | date  
    | time [ ( n ) ]  
    | float [ ( n ) ]  
    | real [ ( n ) ]  
    | decimal [ ( precision [ , scale ] ) ]   
    | numeric [ ( precision [ , scale ] ) ]   
    | money  
    | smallmoney  
    | bigint  
    | int   
    | smallint  
    | tinyint  
    | bit  
    | nvarchar [ ( n | max ) ]  -- max applies only to Azure Synapse Analytics 
    | nchar [ ( n ) ]  
    | varchar [ ( n | max )  ] -- max applies only to Azure Synapse Analytics  
    | char [ ( n ) ]  
    | varbinary [ ( n | max ) ] -- max applies only to Azure Synapse Analytics  
    | binary [ ( n ) ]  
    | uniqueidentifier  
```  

<a name="Arguments"></a>
## <a name="arguments"></a>引數

 *database_name*  
 將包含新資料表之資料庫的名稱。 預設為目前資料庫。  
  
 *schema_name*  
 資料表的結構描述。 指定 *結構描述* 是選擇性的。 如果空白，將會使用預設結構描述。  
  
 *table_name*  
 新資料表的名稱。 若要建立本機暫存資料表，請在資料表名稱前面加上 #。  如需暫存資料表的說明和指導方針，請參閱 [[!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)]中的暫存資料表](https://azure.microsoft.com/documentation/articles/sql-data-warehouse-tables-temporary/)。 

 *column_name*  
 資料表資料行的名稱。

### <a name="column-options"></a><a name="ColumnOptions"></a> 資料行選項

 `COLLATE` *Windows_collation_name*  
 指定運算式的定序。 定序必須是 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 所支援的其中一個 Windows 定序。 如需 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 所支援 Windows 定序的清單，請參閱 [Windows 定序名稱 (Transact-SQL)](windows-collation-name-transact-sql.md)。  
  
 `NULL` | `NOT NULL`  
 指定資料行是否允許使用 `NULL` 值。 預設值為 `NULL`。  
  
 [ `CONSTRAINT` *constraint_name* ] `DEFAULT` *constant_expression*  
 指定預設資料行值。  
  
 | 引數 | 說明 |
 | -------- | ----------- |
 | *constraint_name* | 條件約束的選擇性名稱。 條件約束名稱在資料庫中是唯一的。 名稱可以在其他資料庫中重複使用。 |
 | *constant_expression* | 資料行的預設值。 運算式必須是常值或常數。 例如，允許使用這些常數運算式：`'CA'`、`4`。 這些常數運算式不允許：`2+3`、`CURRENT_TIMESTAMP`。 |
  
### <a name="table-structure-options"></a><a name="TableOptions"></a> 資料表結構選項

如需選擇資料表類型的指導方針，請參閱[在 [!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)]中編製資料表的索引](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-index/)。
  
 `CLUSTERED COLUMNSTORE INDEX` 
 
將資料表儲存為叢集資料行存放區索引。 叢集資料行存放區索引適用於所有資料表資料。 這是 [!INCLUDE[ssSDW](../../includes/sssdw-md.md)] 的預設行為。
 
 `HEAP` 將資料表儲存為堆積。 這是 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] 的預設行為。  
  
 `CLUSTERED INDEX` ( *index_column_name* [ ,...*n* ] )  
 將資料表儲存為具有一或多個索引鍵資料行的叢集索引。 此行為會依資料列儲存資料。 使用 *index_column_name* 指定索引中一或多個索引鍵資料行的名稱。  如需詳細資訊，請參閱「一般備註」中的「資料列存放區資料表」。
 
 `LOCATION = USER_DB` 此選項已淘汰。 在語法上仍會接受，但已不再需要且不會再影響行為。   
  
### <a name="table-distribution-options"></a><a name="TableDistributionOptions"></a> 資料表散發選項

若要了解如何選擇最佳散發方法及如何使用分散式資料表，請參閱[在 [!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)]中散發資料表](https://azure.microsoft.com/documentation/articles/sql-data-warehouse-tables-distribute/)。

`DISTRIBUTION = HASH` ( *distribution_column_name* ) 透過對儲存在 *distribution_column_name* 中的值進行雜湊處理，將每個資料列指派給一個發佈。 演算法具有確定性，這表示其一律會將相同值進行雜湊處理至相同的散發。  散發資料行應定義為 NOT NULL，因為擁有 NULL 的所有資料列都會指派給相同散發。

`DISTRIBUTION = ROUND_ROBIN` 以循環配置資源方式將資料列平均散發到所有發佈中。 這是 [!INCLUDE[ssSDW](../../includes/sssdw-md.md)] 的預設行為。

`DISTRIBUTION = REPLICATE` 在每個計算節點上儲存一份資料表的複本。 針對 [!INCLUDE[ssSDW](../../includes/sssdw-md.md)]，資料表會儲存在每個計算節點的散發資料庫中。 針對 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]，資料表會儲存在跨越計算節點的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 檔案群組中。 這是 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] 的預設行為。
  
### <a name="table-partition-options"></a><a name="TablePartitionOptions"></a> 資料表資料分割選項
如需使用資料表資料分割的指導方針，請參閱[在 [!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)]中分割資料表](https://azure.microsoft.com/documentation/articles/sql-data-warehouse-tables-partition/)。

 `PARTITION` ( *partition_column_name* `RANGE` [ `LEFT` | `RIGHT` ] `FOR VALUES` ( [ *boundary_value* [,...*n*] ] ))   
建立一或多個資料表資料分割。 這些資料分割是水平資料表配量，可讓您將作業套用至資料列子集，無論資料表是儲存為堆積、叢集索引，或叢集資料行存放區索引。 和散發資料行不同，資料表資料分割不會決定每個資料列儲存所在的散發。 反之，資料表資料分割會決定資料列在每個散發內的分組方式和儲存方式。  

| 引數 | 說明 |
| -------- | ----------- |
|*partition_column_name*| 指定 [!INCLUDE[ssSDW](../../includes/sssdw-md.md)] 將用來分割資料列的資料行。 此資料行可以是任何資料類型。 [!INCLUDE[ssSDW](../../includes/sssdw-md.md)] 會以遞增順序排序資料分割資料行值。 從低到高順序會以 `LEFT` 規格由 `RIGHT` 到 `RANGE` 排序。 |  
| `RANGE LEFT` | 指定屬於左邊 (較低的值) 資料分割的界限值。 預設值為 LEFT。 |
| `RANGE RIGHT` | 指定屬於右邊 (較高的值) 資料分割的界限值。 | 
| `FOR VALUES` ( *boundary_value* [,...*n*] ) | 指定資料分割的界限值。 *boundary_value* 是常數運算式。 不得為 NULL。 它必須符合或可以隱含轉換為 *partition_column_name* 的資料類型。 無法在進行隱含轉換期間截斷，因此值的大小和級別不會和 *partition_column_name* 的資料類型相符<br></br><br></br>如果指定 `PARTITION` 子句，但未指定界限值，[!INCLUDE[ssSDW](../../includes/sssdw-md.md)] 將會建立包含一個資料分割的資料分割資料表。 如果適用，您隨後可以將資料表分割成兩個資料分割。<br></br><br></br>如果您指定一個界限值，產生的資料表會有兩個資料分割，一個用於低於界限值的值，一個用於高於界限值的值。 如果將資料分割移動到非資料分割資料表中，非資料分割資料表將會接收資料，但其中繼資料中將不會有資料分割界限。| 

 請參閱＜範例＞一節中的[建立資料分割資料表](#PartitionedTable)。

### <a name="ordered-clustered-columnstore-index-option"></a>已排序的叢集資料行存放區索引選項 

叢集資料行存放區索引 (CCI) 是在 [!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)]中建立資料表的預設值。  CCI 中的資料在壓縮成資料行存放區區段之前，不會進行排序。  建立具有順序的 CCI 時，資料會先進行排序，再新增至索引區段，且可改善查詢效能。 如需詳細資訊，請參閱[使用已排序叢集資料行存放區索引進行效能調整](/azure/sql-data-warehouse/performance-tuning-ordered-cci?view=azure-sqldw-latest&preserve-view=true)。  

已排序的 CCI 可以建立在 [!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)]支援的任何資料類型的資料行上，但不包括字串資料行。  

使用者可以查詢 **sys.index_columns** 中的 **column_store_order_ordinal** 資料行，以取得資料表排序所在的資料行，以及排序中的順序。  

瀏覽[使用已排序叢集資料行存放區索引的效能微調](/azure/sql-data-warehouse/performance-tuning-ordered-cci)以取得詳細資料。   

### <a name="data-type"></a><a name="DataTypes"></a> 資料類型

[!INCLUDE[ssSDW](../../includes/sssdw-md.md)] 支援最常使用的資料類型。 下方是所支援資料類型的清單，以及它們的詳細資料和儲存體位元組。 若要進一步了解資料類型和使用方式，請參閱 [[!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)]中的資料表資料類型](https://azure.microsoft.com/documentation/articles/sql-data-warehouse-tables-data-types)。

如需資料類型轉換表，請參閱 [CAST 和 CONVERT (Transact-SQL)](../functions/cast-and-convert-transact-sql.md) 的＜隱含轉換＞一節。

>[!NOTE]
>請參閱[日期與時間資料類型及函式 &#40;Transact-SQL&#41;](../functions/date-and-time-data-types-and-functions-transact-sql.md) 以取得詳細資料。

`datetimeoffset` [ ( *n* ) ]  
 *n* 的預設值是 7。  
  
 `datetime2` [ ( *n* ) ]  
與 `datetime` 相同，但您可以指定小數部分的秒數。 *n* 的預設值是 `7`。  
  
|*n* 值|Precision|調整|  
|--:|--:|-:|  
|`0`|19|0|  
|`1`|21|1|  
|`2`|22|2|  
|`3`|23|3|  
|`4`|24|4|  
|`5`|25|5|  
|`6`|26|6|  
|`7`|27|7|  
  
 `datetime`  
 根據西曆使用 19 到 23 個字元儲存日期和時間。 日期可以包含年、月和日。 時間則包含小時、分鐘和秒。您可以選擇為小數部分的秒數顯示三個位數。 儲存體大小是 8 位元組。  
  
 `smalldatetime`  
 儲存日期和時間。 儲存體大小是 4 位元組。  
  
 `date`  
 根據西曆使用最多 10 個字元儲存年、月和日的日期。 儲存體大小是 3 位元組。 日期會儲存為整數。  
  
 `time` [ ( *n* ) ]  
 *n* 的預設值是 `7`。  
  
 `float` [ ( *n* ) ]  
 用來搭配浮點數值資料使用的近似數字資料類型。 浮點數資料是近似的，這表示並非資料類型範圍內的所有值都能夠精確地表示。 *n* 指定用來以科學記號標記法儲存 `float` 之尾數的位元數。 *n* 可決定有效位數和儲存體大小。 如果指定 *n*，則其值必須介於 `1` 與 `53` 之間。 *n* 的預設值是 `53`。  
  
| *n* 值 | Precision | 儲存體大小 |  
| --------: | --------: | -----------: |  
| 1-24   | 7 位數  | 4 個位元組      |  
| 25-53  | 15 位數 | 8 個位元組      |  
  
 [!INCLUDE[ssSDW](../../includes/sssdw-md.md)] 會將 *n* 當做兩個可能值的其中一個來處理。 如果 `1`<= *n* <= `24`，則將 *n* 當作 `24` 來處理。 如果 `25` <= *n* <= `53`，則將 *n* 當作 `53` 來處理。  
  
 [!INCLUDE[ssSDW](../../includes/sssdw-md.md)] `float` 資料類型從 *到* 的所有 `1`n`53` 值都符合 ISO 標準。 double precision 的同義字是 `float(53)`。  
  
 `real` [ ( *n* ) ]  
 real 的定義和 float 相同。 `real` 的 ISO 同義字是 `float(24)`。  
  
 `decimal` [ ( *precision* [ *, scale* ] ) ] | `numeric` [ ( *precision* [ *, scale* ] ) ]  
 儲存固定有效位數和小數位數的數字。  
  
 *有效位數*  
 可儲存的最大十進位數總數，小數點左右兩側都包括在內。 有效位數必須是從 `1` 到最大有效位數 `38` 之間的值。 預設有效位數是 `18`。  
  
 *scale*  
 小數點右側所能儲存的最大十進位數。 *小數位數* 必須是從 `0` 到 *有效位數* 之間的值。 只有在已指定 *precision* 的情況下，您才能指定 *scale*。 預設小數位數為 `0`，因此 `0` <= *scale* <= *precision*。 最大儲存體大小會隨著有效位數而不同。  
  
| Precision | 儲存體位元組  |  
| ---------: |-------------: |  
|  1-9       |             5 |  
| 10-19      |             9 |  
| 20-28      |            13 |  
| 29-38      |            17 |  
  
 `money` | `smallmoney`  
 代表貨幣值的資料類型。  
  
| 資料類型 | 儲存體位元組 |  
| --------- | ------------: |  
| `money`|8|  
| `smallmoney` |4|  
  
 `bigint` \| `int` \| `smallint` \| `tinyint`  
 使用整數資料的 Exact-number 資料類型。 下表會顯示儲存體。  
  
| 資料類型 | 儲存體位元組 |  
| --------- | ------------: |  
| `bigint`|8|  
| `int` |4|  
| `smallint` |2|  
| `tinyint` |1|  
  
 `bit`  
 一種整數資料類型，其值有 `1`、`0` 或 NULL 幾種。 [!INCLUDE[ssSDW](../../includes/sssdw-md.md)] 可將 bit 資料行的儲存體最佳化。 如果資料表中的 bit 資料行小於或等於 8 個，這些資料行會儲存為 1 個位元組。 如果有 9 到 16 個 bit 資料行，則儲存為 2 個位元組，依此類推。  
  
 `nvarchar` [ ( *n* | `max` ) ] -- `max` 僅適用於 [!INCLUDE[ssSDW](../../includes/sssdw-md.md)].  
 可變長度的 Unicode 字元資料。 *n* 可以是從 1 到 4000 之間的值。 `max` 表示儲存體大小上限是 2^31-1 個位元組 (2 GB)。 儲存體大小 (以位元組為單位) 是輸入字元數的兩倍再加上 2 位元組。 輸入的資料長度可以是 0 個字元。  
  
 `nchar` [ ( *n* ) ]  
 長度為 *n* 個字元的固定長度 Unicode 字元資料。 *n* 必須是從 `1` 到 `4000` 之間的值。 儲存體大小是 *n* 個位元組的兩倍。  
  
 `varchar` [ ( *n*  | `max` ) ] -- `max` 僅適用於 [!INCLUDE[ssSDW](../../includes/sssdw-md.md)].   
 長度為 *n* 位元組的可變長度非 Unicode 字元資料。 *n* 必須是從 `1` 到 `8000` 之間的值。 `max` 表示儲存體大小上限是 2^31-1 個位元組 (2 GB)。儲存體大小是輸入資料的實際長度再加上 2 位元組。  
  
 `char` [ ( *n* ) ]  
 長度為 *n* 位元組的固定長度非 Unicode 字元資料。 *n* 必須是從 `1` 到 `8000` 之間的值。 儲存體大小是 *n* 位元組。 *n* 的預設值是 `1`。  
  
 `varbinary` [ ( *n*  | `max` ) ] -- `max` 僅適用於 [!INCLUDE[ssSDW](../../includes/sssdw-md.md)].  
 可變長度的二進位資料。 *n* 可以是從 `1` 到 `8000` 之間的值。 `max` 表示儲存體大小上限是 2^31-1 個位元組 (2 GB)。 儲存體大小是輸入資料的實際長度再加上 2 位元組。 *n* 的預設值是 7。  
  
 `binary` [ ( *n* ) ]  
 長度為 *n* 位元組的固定長度二進位資料。 *n* 可以是從 `1` 到 `8000` 之間的值。 儲存體大小是 *n* 位元組。 *n* 的預設值是 `7`。  
  
 `uniqueidentifier`  
 這是 16 位元組的 GUID。  
   
<a name="Permissions"></a>  
## <a name="permissions"></a>權限  
 建立資料表時需要 `db_ddladmin` 固定資料庫角色的權限，或：
 - 資料庫的 `CREATE TABLE` 權限
 - 將包含資料表之結構描述的 `ALTER SCHEMA` 權限。 

建立資料分割資料表時需要 `db_ddladmin` 固定資料庫角色的權限，或

- `ALTER ANY DATASPACE` 權限
  
 建立本機暫存資料表的登入會接收資料表的 `CONTROL`、`INSERT`、`SELECT` 和 `UPDATE` 權限。  
 
<a name="GeneralRemarks"></a>  
## <a name="general-remarks"></a>一般備註  
 
如需最小和最大限制，請參閱 [[!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)]容量限制](https://azure.microsoft.com/documentation/articles/sql-data-warehouse-service-capacity-limits/)。 
 
### <a name="determining-the-number-of-table-partitions"></a>判斷資料表資料分割數目
每個使用者定義的資料表都會分割成多個較小資料表，儲存在稱為散發的個別位置。 [!INCLUDE[ssSDW](../../includes/sssdw-md.md)] 使用 60 個散發。 在 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] 中，散發的數目取決於計算節點的數目。
 
每個散發都會包含所有資料表資料分割。 例如，如果有 60 個散發和四個資料表分割區加上一個空的分割區，將會有 300 個分割區 (5 x 60 = 300)。 如果資料表是叢集資料行存放區索引，每個資料分割都將有一個資料行存放區索引，這表示您將會有 300 個資料行存放區索引。

我們建議使用較少的資料表資料分割，以確保每個資料行存放區索引都有足夠的資料列，以充分利用資料行存放區索引的優點。 如需詳細資訊，請參閱[在 [!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)]中分割資料表](https://azure.microsoft.com/documentation/articles/sql-data-warehouse-tables-partition/)和[在 [!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)]中編製資料表的索引](https://azure.microsoft.com/documentation/articles/sql-data-warehouse-tables-index/)  

### <a name="rowstore-table-heap-or-clustered-index"></a>資料列存放區資料表 (堆積或叢集索引)

資料列存放區資料表是以資料列逐列順序儲存的資料表。 是堆積或叢集索引。 [!INCLUDE[ssSDW](../../includes/sssdw-md.md)] 會建立具有頁面壓縮的所有資料列存放區資料表，使用者無法設定此行為。

### <a name="columnstore-table-columnstore-index"></a>資料行存放區資料表 (資料行存放區索引)

資料行存放區資料表是以資料行逐行順序儲存的資料表。 資料行存放區索引是資料管理技術，可管理資料行存放區資料表中儲存的資料。  叢集資料行存放區索引不會影響資料散發方式，而會影響資料在每個散發內的儲存方式。

若要將資料列存放區資料表變更為資料行存放區資料表，請卸除資料表中所有現有的索引，然後建立叢集資料行存放區索引。 如需範例，請參閱 [CREATE COLUMNSTORE INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/create-columnstore-index-transact-sql.md)。

如需詳細資訊，請參閱這些文章：
- [資料行存放區索引建立版本功能摘要](../../relational-databases/indexes/columnstore-indexes-what-s-new.md)
- [在 [!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)]中編製資料表的索引](https://azure.microsoft.com/documentation/articles/sql-data-warehouse-tables-index/)
- [資料行存放區索引指南](~/relational-databases/indexes/columnstore-indexes-overview.md) 

<a name="LimitationsRestrictions"></a>  
## <a name="limitations-and-restrictions"></a>限制事項  
 您無法在散發資料行上定義預設條件約束。  
  
### <a name="partitions"></a>資料分割
使用資料分割時，資料分割資料行不能有僅限 Unicode 的定序。 例如，以下陳述式會失敗。  
  
 ```sql
CREATE TABLE t1 ( c1 varchar(20) COLLATE Divehi_90_CI_AS_KS_WS) WITH (PARTITION (c1 RANGE FOR VALUES (N'')))
```  
 
 如果 *boundary_value* 是一個必須隱含轉換成 *partition_column_name* 中資料類型的常值，就會發生不一致的情形。 常值會透過 [!INCLUDE[ssSDW](../../includes/sssdw-md.md)] 系統檢視顯示，但轉換後的值會用於 [!INCLUDE[tsql](../../includes/tsql-md.md)] 作業。 

### <a name="temporary-tables"></a>暫存資料表

 不支援開頭為 ## 的全域暫存資料表。  
  
 本機暫存資料表具有下列限制事項：  
  
-   只有目前的工作階段才能看見它們。 [!INCLUDE[ssSDW](../../includes/sssdw-md.md)] 會在工作階段結束時自動將它們卸除。 若要明確地將它們卸除，請使用 DROP TABLE 陳述式。   
-   無法將其重新命名。 
-   不能有資料分割或檢視。  
-   無法變更其權限。 `GRANT`、`DENY` 和 `REVOKE` 陳述式無法與本機暫存資料表搭配使用。   
-   系統會針對暫存資料表封鎖資料庫主控台命令。   
-   如果在批次內使用超過一個本機暫存資料表，每個資料表都必須有唯一的名稱。 如果在同一批次中執行多個工作階段，並建立相同的本機暫存資料表，[!INCLUDE[ssSDW](../../includes/sssdw-md.md)] 會在內部將一個數字尾碼附加到本機暫存資料表名稱，以維持每個本機暫存資料表都有唯一的名稱。  
    
<a name="LockingBehavior"></a>   
## <a name="locking-behavior"></a>鎖定行為  
 在資料表上取得獨佔鎖定。 在 DATABASE、SCHEMA 和 SCHEMARESOLUTION 物件上取得共用鎖定。  
 

<a name="ExamplesColumn"></a>   
## <a name="examples-for-columns"></a>資料行範例

### <a name="a-specify-a-column-collation"></a><a name="ColumnCollation"></a> A. 指定資料行定序 
 在以下範例中，資料表 `MyTable` 是使用兩個不同的資料行定序所建立的。 依照預設，資料行 `mycolumn1` 具有預設定序 Latin1_General_100_CI_AS_KS_WS。 資料行 `mycolumn2` 具有定序 Frisian_100_CS_AS。  
  
```sql
CREATE TABLE MyTable   
  (  
    mycolumnnn1 nvarchar,  
    mycolumn2 nvarchar COLLATE Frisian_100_CS_AS )  
WITH ( CLUSTERED COLUMNSTORE INDEX )  
;  
```  
  
### <a name="b-specify-a-default-constraint-for-a-column"></a><a name="DefaultConstraint"></a> B. 指定資料行的預設條件約束

 以下範例說明指定資料行預設值的語法。 colA 資料行有名為 constraint_colA 的預設條件約束，且預設值為 0。  
  
```sql
CREATE TABLE MyTable
  (  
    colA int CONSTRAINT constraint_colA DEFAULT 0,  
    colB nvarchar COLLATE Frisian_100_CS_AS
  )  
WITH ( CLUSTERED COLUMNSTORE INDEX )  
;  
```

<a name="ExamplesTemporaryTables"></a> 
## <a name="examples-for-temporary-tables"></a>暫存資料表範例

### <a name="c-create-a-local-temporary-table"></a><a name="TemporaryTable"></a> C. 建立本機暫存資料表  
 下列範例會建立一個名為 #myTable 的本機暫存資料表。 指定的資料表使用三部分名稱，開頭為 #。
  
```sql
CREATE TABLE AdventureWorks.dbo.#myTable
  (  
   id int NOT NULL,  
   lastName varchar(20),  
   zipCode varchar(6)  
  )  
WITH  
  (   
    DISTRIBUTION = HASH (id),  
    CLUSTERED COLUMNSTORE INDEX
  )  
;  
```

<a name="ExTableStructure"></a>  
## <a name="examples-for-table-structure"></a>資料表結構範例

### <a name="d-create-a-table-with-a-clustered-columnstore-index"></a><a name="ClusteredColumnstoreIndex"></a> D. 建立具有叢集資料行存放區索引的資料表  
 以下範例會建立具有叢集資料行存放區索引的分散式資料表。 每個散發都會儲存為資料行存放區。  
  
 叢集資料行存放區索引不會影響資料散發方式；資料一律由資料列散發。 叢集資料行存放區索引會影響資料在每個散發內的儲存方式。  
  
```sql
  CREATE TABLE MyTable
  (  
    colA int CONSTRAINT constraint_colA DEFAULT 0,  
    colB nvarchar COLLATE Frisian_100_CS_AS
  )  
WITH   
  (   
    DISTRIBUTION = HASH ( colB ),  
    CLUSTERED COLUMNSTORE INDEX
  )  
;  
```  

### <a name="e-create-an-ordered-clustered-columnstore-index"></a><a name="OrderedClusteredColumnstoreIndex"></a> E. 建立已排序的叢集資料行存放區索引

下列範例會示範如何建立已排序的叢集資料行存放區索引。 索引會根據 SHIPDATE 進行排序。

```sql
CREATE TABLE Lineitem  
WITH (DISTRIBUTION = ROUND_ROBIN, CLUSTERED COLUMNSTORE INDEX ORDER(SHIPDATE))  
AS  
SELECT * FROM ext_Lineitem
```

<a name="ExTableDistribution"></a> 
## <a name="examples-for-table-distribution"></a>資料表散發範例

### <a name="f-create-a-round_robin-table"></a><a name="RoundRobin"></a> F. 建立 ROUND_ROBIN 資料表  
 以下範例會建立一個具有三個資料行且沒有資料分割的 ROUND_ROBIN 資料表。 資料會分散到所有散發。 資料表是使用 CLUSTERED COLUMNSTORE INDEX 所建立的，可提供比堆積或資料列存放區叢集索引更好的效能和資料壓縮。  
  
```sql
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED COLUMNSTORE INDEX );  
```  
  
### <a name="g-create-a-hash-distributed-table"></a><a name="HashDistributed"></a> G. 建立雜湊分散式資料表

 以下範例會建立和上一個範例相同的資料表。 但是，針對這個資料表，資料列會被散發 (在 `id` 資料行上)，而不是像 ROUND_ROBIN 資料表一樣隨機分散。 資料表是使用 CLUSTERED COLUMNSTORE INDEX 所建立的，可提供比堆積或資料列存放區叢集索引更好的效能和資料壓縮。  
  
```sql
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH  
  (   
    DISTRIBUTION = HASH (id),   
    CLUSTERED COLUMNSTORE INDEX  
  );  
```  
  
### <a name="h-create-a-replicated-table"></a><a name="Replicated"></a> H. 建立複寫資料表  
 以下範例會建立和上一個範例類似的複寫資料表。 複寫資料表會完整複製到每個計算節點。 當每個計算節點上都有此複本時，就可以在查詢時減少資料移動。 此範例使用叢集索引建立，提供比堆積更佳的資料壓縮。 堆積可能未包含足夠的資料列以達成良好叢集資料行存放區索引壓縮。  
  
```sql
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH  
  (   
    DISTRIBUTION = REPLICATE,
    CLUSTERED INDEX (lastName)  
  );  
```  

<a name="ExTablePartitions"></a> 
## <a name="examples-for-table-partitions"></a>資料表資料分割範例

###  <a name="i-create-a-partitioned-table"></a><a name="PartitionedTable"></a> I. 建立資料分割資料表

 以下範例會建立如範例 A 中所示的相同資料表，並在 `id` 資料行中加上 RANGE LEFT 資料分割。 它會指定四個資料分割界限值，結果會產生五個資料分割。  
  
```sql
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode int)  
WITH
  (
  
    PARTITION ( id RANGE LEFT FOR VALUES (10, 20, 30, 40 )),  
    CLUSTERED COLUMNSTORE INDEX
  )  
;  
```  
  
 在這個範例中，資料將排序到下列資料分割中：  
  
- 資料分割 1：col <= 10
- 分割區 2：10 < col <= 20
- 分割區 3：20 < col <= 30
- 分割區 4：30 < col <= 40
- 分割區 5：40 < col  
  
 如果這個相同的資料表分割為 RANGE RIGHT，而不是 RANGE LEFT (預設)，資料將排序到下列資料分割：  
  
- 資料分割 1：col < 10  
- 分割區 2：10 <= col < 20
- 分割區 3：20 <= col < 30
- 分割區 4：30 <= col < 40
- 分割區 5：40 <= col  
  
### <a name="j-create-a-partitioned-table-with-one-partition"></a><a name="OnePartition"></a> J. 建立具有一個資料分割的資料分割資料表

 以下範例會建立具有一個資料分割的資料分割資料表。 不會指定任何界限值，從而產生單一資料分割。  
  
```sql
CREATE TABLE myTable (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode int)  
WITH
    (
      PARTITION ( id RANGE LEFT FOR VALUES ( )),  
      CLUSTERED COLUMNSTORE INDEX  
    )  
;  
```  
  
### <a name="k-create-a-table-with-date-partitioning"></a><a name="DatePartition"></a> K. 建立具有日期資料分割的資料表

 以下範例會建立一個名為 `myTable` 新資料表，並在 `date` 資料行上具有資料分割。 透過使用 RANGE RIGHT 和日期作為界限值，它會在每個資料分割中放置一個月的資料。  
  
```sql
CREATE TABLE myTable (  
    l_orderkey      bigint,
    l_partkey       bigint,
    l_suppkey       bigint,
    l_linenumber    bigint,
    l_quantity      decimal(15,2),  
    l_extendedprice decimal(15,2),  
    l_discount      decimal(15,2),  
    l_tax           decimal(15,2),  
    l_returnflag    char(1),  
    l_linestatus    char(1),  
    l_shipdate      date,  
    l_commitdate    date,  
    l_receiptdate   date,  
    l_shipinstruct  char(25),  
    l_shipmode      char(10),  
    l_comment       varchar(44))  
WITH
  (
    DISTRIBUTION = HASH (l_orderkey),  
    CLUSTERED COLUMNSTORE INDEX,  
    PARTITION ( l_shipdate  RANGE RIGHT FOR VALUES
      (  
        '1992-01-01','1992-02-01','1992-03-01','1992-04-01','1992-05-01',
        '1992-06-01','1992-07-01','1992-08-01','1992-09-01','1992-10-01',
        '1992-11-01','1992-12-01','1993-01-01','1993-02-01','1993-03-01',
        '1993-04-01','1993-05-01','1993-06-01','1993-07-01','1993-08-01',
        '1993-09-01','1993-10-01','1993-11-01','1993-12-01','1994-01-01',
        '1994-02-01','1994-03-01','1994-04-01','1994-05-01','1994-06-01',
        '1994-07-01','1994-08-01','1994-09-01','1994-10-01','1994-11-01',
        '1994-12-01'  
      ))
  );  
```  
  
<a name="SeeAlso"></a>
## <a name="see-also"></a>另請參閱
 
[CREATE TABLE AS SELECT &#40;Azure Synapse Analytics&#41;](../../t-sql/statements/create-table-as-select-azure-sql-data-warehouse.md)   
[DROP TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/drop-table-transact-sql.md)   
[ALTER TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-table-transact-sql.md)   
[sys.index_columns &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-index-columns-transact-sql.md) 
