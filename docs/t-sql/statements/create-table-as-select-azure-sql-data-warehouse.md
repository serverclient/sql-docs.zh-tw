---
description: CREATE TABLE AS SELECT (Azure Synapse Analytics)
title: CREATE TABLE AS SELECT (Azure Synapse Analytics) | Microsoft Docs
ms.custom: ''
ms.date: 10/07/2016
ms.service: sql-data-warehouse
ms.reviewer: ''
ms.topic: language-reference
dev_langs:
- TSQL
ms.assetid: d1e08f88-64ef-4001-8a66-372249df2533
author: julieMSFT
ms.author: jrasnick
monikerRange: '>= aps-pdw-2016 || = azure-sqldw-latest'
ms.openlocfilehash: 55b57802b33ed1e0187ba2f83f3dd9a20ffdd3c1
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98102336"
---
# <a name="create-table-as-select-azure-synapse-analytics"></a>CREATE TABLE AS SELECT (Azure Synapse Analytics)
[!INCLUDE[applies-to-version/asa-pdw](../../includes/applies-to-version/asa-pdw.md)]

CREATE TABLE AS SELECT (CTAS) 是最重要的 T-SQL 功能之一。 這是一種完全平行化作業，會利用 SELECT 陳述式的輸出來建立新的資料表。 CTAS 是建立資料表複本最快、最簡單的方法。   
 
 例如，使用 CTAS 可執行以下作業：  
  
-   重新建立具有不同雜湊散發資料行的資料表。
-   重新建立如同複寫的資料表。   
-   只在資料表的部分資料行上，建立資料行存放區索引。  
-   查詢或匯入的外部資料。  

> [!NOTE]  
> 因為 CTAS 擴充了原本的資料表建立功能，所以本主題不再重複討論 CREATE TABLE 主題。 我們將重點放在描述 CTAS 和 CREATE TABLE 陳述式之間的差異。 如需 CREATE TABLE 詳細資訊，請參閱 [CREATE TABLE (Azure Synapse Analytics)](./create-table-azure-sql-data-warehouse.md) 陳述式。 
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
<a name="syntax-bk"></a>

## <a name="syntax"></a>語法   

```syntaxsql
CREATE TABLE { database_name.schema_name.table_name | schema_name.table_name | table_name }
    [ ( column_name [ ,...n ] ) ]  
    WITH ( 
      <distribution_option> -- required
      [ , <table_option> [ ,...n ] ]    
    )  
    AS <select_statement>  
    OPTION <query_hint> 
[;]  

<distribution_option> ::=
    { 
        DISTRIBUTION = HASH ( distribution_column_name ) 
      | DISTRIBUTION = ROUND_ROBIN 
      | DISTRIBUTION = REPLICATE
    }   

<table_option> ::= 
    {   
        CLUSTERED COLUMNSTORE INDEX --default for Synapse Analytics 
      | CLUSTERED COLUMNSTORE INDEX ORDER (column[,...n])
      | HEAP --default for Parallel Data Warehouse   
      | CLUSTERED INDEX ( { index_column_name [ ASC | DESC ] } [ ,...n ] ) --default is ASC 
    }  
      | PARTITION ( partition_column_name RANGE [ LEFT | RIGHT ] --default is LEFT  
        FOR VALUES ( [ boundary_value [,...n] ] ) ) 
  
<select_statement> ::=  
    [ WITH <common_table_expression> [ ,...n ] ]  
    SELECT select_criteria  

<query_hint> ::=
    {
        MAXDOP 
    }
```  

<a name="arguments-bk"></a>
  
## <a name="arguments"></a>引數  
如需詳細資訊，請參閱 CREATE TABLE 中的[引數小節](./create-table-azure-sql-data-warehouse.md#Arguments)。  

<a name="column-options-bk"></a>

### <a name="column-options"></a>資料行選項
`column_name` [ ,...`n` ]   
 資料行名稱不允許 CREATE TABLE 中提到的[資料行選項](./create-table-azure-sql-data-warehouse.md#ColumnOptions)。  您反而應該為新資料表提供一個由一或多個資料行名稱構成的選擇性清單。 新資料表中的資料行，將使用您指定的名稱。 當您指定資料行名稱時，資料行清單中的資料行數目必須與選取結果中的資料行數目相符。 如果您未指定任何資料行名稱，新目標資料表就會使用選取陳述式結果中的資料行名稱。 
  
 您無法指定任何其他資料行選項，例如資料類型、定序或可 Null 性。 這些屬性每個都是從 `SELECT` 陳述式的結果衍生而來的。 不過，您可以使用 SELECT 陳述式來變更屬性。 如需範例，請參閱[使用 CTAS 來變更資料行的屬性](#ctas-change-column-attributes-bk)。   

<a name="table-distribution-options-bk"></a>

### <a name="table-distribution-options"></a>資料表散發選項

`DISTRIBUTION` = `HASH` ( *distribution_column_name* ) | ROUND_ROBIN | REPLICATE      
CTAS 陳述式需要一個散發選項，而且沒有預設值。 這和 CREATE TABLE 不同，後者有預設值。 

如需詳細資訊以及了解如何選擇最佳散發資料行，請參閱 CREATE TABLE 中的[資料表散發選項](./create-table-azure-sql-data-warehouse.md#TableDistributionOptions)小節。 

<a name="table-partition-options-bk"></a>

### <a name="table-partition-options"></a>資料表資料分割選項
即使來源資料表已經過分割，CTAS 陳述式預設仍會建立未分割的資料表。 若要使用 CTAS 陳述式來建立資料分割資料表，您必須指定資料分割選項。 

如需詳細資訊，請參閱 CREATE TABLE 中的[資料表資料分割選項](./create-table-azure-sql-data-warehouse.md#TablePartitionOptions)小節。

<a name="select-options-bk"></a>

### <a name="select-statement"></a>SELECT 陳述式
選取陳述式是 CTAS 與 CREATE TABLE 之間的基本差異。  

 `WITH` *common_table_expression*  
 指定稱為通用資料表運算式 (CTE) 的暫存具名結果集。 如需詳細資訊，請參閱 [WITH common_table_expression &#40;Transact-SQL&#41;](../../t-sql/queries/with-common-table-expression-transact-sql.md)。  
  
 `SELECT` *select_criteria*  
 將 SELECT 陳述式產生的結果填入新資料表。 *select_criteria* 是 SELECT 陳述式的主體，可決定要複製到新資料表的資料。 如需 SELECT 陳述式的相關資訊，請參閱 [SELECT &#40;Transact-SQL&#41;](../../t-sql/queries/select-transact-sql.md)。  
 
### <a name="query-hint"></a>查詢提示
使用者可以將 MAXDOP 設定為整數值，以控制平行處理原則的最大程度。  當 MAXDOP 設為 1 時，會由單一執行緒執行查詢。

 
<a name="permissions-bk"></a>  
  
## <a name="permissions"></a>權限  
CTAS 需要 *select_criteria* 中所參考任何物件的 `SELECT` 權限。

如需資料表的建立權限，請參閱 CREATE TABLE 中的[權限](./create-table-azure-sql-data-warehouse.md#Permissions)。 
  
<a name="general-remarks-bk"></a>
  
## <a name="general-remarks"></a>一般備註
如需詳細資訊，請參閱 CREATE TABLE 中的[ 一般備註](./create-table-azure-sql-data-warehouse.md#GeneralRemarks)。

<a name="limitations-bk"></a>

## <a name="limitations-and-restrictions"></a>限制事項  

已排序的叢集資料行存放區索引可以建立在 [!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)]支援的任何資料類型的資料行上，但不包括字串資料行。  

[SET ROWCOUNT &#40;Transact-SQL&#41;](../../t-sql/statements/set-rowcount-transact-sql.md) 對 CTAS 沒有作用。 若要達到類似的行為，請使用 [TOP &#40;Transact-SQL&#41;](../../t-sql/queries/top-transact-sql.md)。  
 
如需詳細資訊，請參閱 CREATE TABLE 中的[限制事項](./create-table-azure-sql-data-warehouse.md#LimitationsRestrictions)。

<a name="locking-behavior-bk"></a>
  
## <a name="locking-behavior"></a>鎖定行為  
 如需詳細資訊，請參閱 CREATE TABLE 中的[鎖定行為](./create-table-azure-sql-data-warehouse.md#LockingBehavior)。
 
<a name="performance-bk"></a>
 
 ## <a name="performance"></a>效能 

至於雜湊散發的資料表，您可以使用 CTAS 來選擇不同的散發資料行，讓聯結和彙總達到更佳的效能。 如果您的目標並不是選擇不同的散發資料行，則指定相同的散發資料行，就會獲得最佳的 CTAS 效能，因為如此可避免重新散發資料列。 

如果您使用 CTAS 來建立資料表 ，而且效能不是考慮因素，則可以指定 `ROUND_ROBIN`，這樣就不用決定散發資料行了。

若要避免在後續查詢中移動資料，可以指定 `REPLICATE`，但代價是須 要增加儲存體，才能載入每一個計算節點的資料表完整複本。  


<a name="examples-copy-table-bk"></a>

## <a name="examples-for-copying-a-table"></a>資料表複製範例

<a name="ctas-copy-table-bk"></a>

### <a name="a-use-ctas-to-copy-a-table"></a>A. 使用 CTAS 複製資料表 
適用於：[!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] 和 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]

或許 `CTAS` 最常見的用途之一就是建立資料表複本，以便您變更 DDL。 例如，您最初是將資料表建立為 `ROUND_ROBIN`，而現在想要將它變更為散發到資料行上的資料表，就可以使用 `CTAS` 來變更散發資料行。 `CTAS` 也可以用來變更資料分割、索引或資料行的類型。

比方說您使用預設散發類型的 `ROUND_ROBIN` 散發來建立這個資料表，因為 `CREATE TABLE` 中未指定任何散發資料行。

```sql
CREATE TABLE FactInternetSales
(
    ProductKey INT NOT NULL,
    OrderDateKey INT NOT NULL,
    DueDateKey INT NOT NULL,
    ShipDateKey INT NOT NULL,
    CustomerKey INT NOT NULL,
    PromotionKey INT NOT NULL,
    CurrencyKey INT NOT NULL,
    SalesTerritoryKey INT NOT NULL,
    SalesOrderNumber NVARCHAR(20) NOT NULL,
    SalesOrderLineNumber TINYINT NOT NULL,
    RevisionNumber TINYINT NOT NULL,
    OrderQuantity SMALLINT NOT NULL,
    UnitPrice MONEY NOT NULL,
    ExtendedAmount MONEY NOT NULL,
    UnitPriceDiscountPct FLOAT NOT NULL,
    DiscountAmount FLOAT NOT NULL,
    ProductStandardCost MONEY NOT NULL,
    TotalProductCost MONEY NOT NULL,
    SalesAmount MONEY NOT NULL,
    TaxAmt MONEY NOT NULL,
    Freight MONEY NOT NULL,
    CarrierTrackingNumber NVARCHAR(25),
    CustomerPONumber NVARCHAR(25)
);
```

現在您想要建立具有叢集資料行存放區索引的此資料表新複本，以便您可以善用叢集資料行存放區資料表的效能。 您也想要在 ProductKey 散發此資料表，因為您預期這個資料行會進行聯結，並想要在 ProductKey 進行聯結的期間避免資料移動。 最後您也想要在 OrderDateKey 上加入資料分割，以便能透過卸除舊資料分割來快速刪除舊資料。 以下是會將舊資料表複製到新資料表的 CTAS 陳述式。

```sql
CREATE TABLE FactInternetSales_new
WITH
(
    CLUSTERED COLUMNSTORE INDEX,
    DISTRIBUTION = HASH(ProductKey),
    PARTITION
    (
        OrderDateKey RANGE RIGHT FOR VALUES
        (
        20000101,20010101,20020101,20030101,20040101,20050101,20060101,20070101,20080101,20090101,
        20100101,20110101,20120101,20130101,20140101,20150101,20160101,20170101,20180101,20190101,
        20200101,20210101,20220101,20230101,20240101,20250101,20260101,20270101,20280101,20290101
        )
    )
)
AS SELECT * FROM FactInternetSales;
```

最後您也可以重新命名資料表，以便以新資料表來交換，然後再卸除舊的資料表。

```sql
RENAME OBJECT FactInternetSales TO FactInternetSales_old;
RENAME OBJECT FactInternetSales_new TO FactInternetSales;

DROP TABLE FactInternetSales_old;
```

<a name="examples-column-bk"></a>

## <a name="examples-for-column-options"></a>資料行選項範例

<a name="ctas-change-column-attributes-bk"></a>

### <a name="b-use-ctas-to-change-column-attributes"></a>B. 使用 CTAS 來變更資料行屬性 
適用於：[!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] 和 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]

這個範例會使用 CTAS 來為 DimCustomer2 資料表的數個資料行變更資料類型、可 Null 性和定序。  
  
```sql  
-- Original table 
CREATE TABLE [dbo].[DimCustomer2] (  
    [CustomerKey] INT NOT NULL,  
    [GeographyKey] INT NULL,  
    [CustomerAlternateKey] nvarchar(15) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL  
)  
WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = HASH([CustomerKey]));  
  
-- CTAS example to change data types, nullability, and column collations  
CREATE TABLE test  
WITH (HEAP, DISTRIBUTION = ROUND_ROBIN)  
AS  
SELECT  
    CustomerKey AS CustomerKeyNoChange,  
    CustomerKey*1 AS CustomerKeyChangeNullable,  
    CAST(CustomerKey AS DECIMAL(10,2)) AS CustomerKeyChangeDataTypeNullable,  
    ISNULL(CAST(CustomerKey AS DECIMAL(10,2)),0) AS CustomerKeyChangeDataTypeNotNullable,  
    GeographyKey AS GeographyKeyNoChange,  
    ISNULL(GeographyKey,0) AS GeographyKeyChangeNotNullable,  
    CustomerAlternateKey AS CustomerAlternateKeyNoChange,  
    CASE WHEN CustomerAlternateKey = CustomerAlternateKey 
        THEN CustomerAlternateKey END AS CustomerAlternateKeyNullable,  
    CustomerAlternateKey COLLATE Latin1_General_CS_AS_KS_WS AS CustomerAlternateKeyChangeCollation  
FROM [dbo].[DimCustomer2]  
  
-- Resulting table 
CREATE TABLE [dbo].[test] (
    [CustomerKeyNoChange] INT NOT NULL, 
    [CustomerKeyChangeNullable] INT NULL, 
    [CustomerKeyChangeDataTypeNullable] DECIMAL(10, 2) NULL, 
    [CustomerKeyChangeDataTypeNotNullable] DECIMAL(10, 2) NOT NULL, 
    [GeographyKeyNoChange] INT NULL, 
    [GeographyKeyChangeNotNullable] INT NOT NULL, 
    [CustomerAlternateKeyNoChange] NVARCHAR(15) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL, 
    [CustomerAlternateKeyNullable] NVARCHAR(15) COLLATE SQL_Latin1_General_CP1_CI_AS NULL, 
    [CustomerAlternateKeyChangeCollation] NVARCHAR(15) COLLATE Latin1_General_CS_AS_KS_WS NOT NULL
)
WITH (DISTRIBUTION = ROUND_ROBIN);
```
 
在最後的步驟中，您可以使用 [RENAME &#40;Transact-SQL&#41;](../../t-sql/statements/rename-transact-sql.md) 來切換資料表名稱。 這樣會將 DimCustomer2 變成新的資料表。

```sql
RENAME OBJECT DimCustomer2 TO DimCustomer2_old;
RENAME OBJECT test TO DimCustomer2;

DROP TABLE DimCustomer2_old;
```

<a name="examples-table-distribution-bk"></a>

## <a name="examples-for-table-distribution"></a>資料表散發範例

<a name="ctas-change-distribution-method-bk"></a>

### <a name="c-use-ctas-to-change-the-distribution-method-for-a-table"></a>C. 使用 CTAS 來變更資料表的散發方法
適用於：[!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] 和 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]

這個簡易範例會示範如何變更資料表的散發方法。 為了示範整個操作流程，它會將雜湊散發資料表變更為循環配置資源資料表，然後再將循環配置資源資料表變更回雜湊散發資料表。 最後的資料表將與原始資料表相符。 

大部分情況下，您不需要將雜湊散發資料表變更為循環配置資源資料表。 更多的情況是，您可能需要將循環配置資源資料表變更為雜湊散發資料表。 例如，您可能一開始將新的資料表載入為循環配置資源資料表，但之後將其移至雜湊散發資料表以獲取更佳的聯結效能。

這個範例會使用 AdventureWorksDW 範例資料庫。 若要載入 [!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] 版本，請參閱[將範例資料載入 [!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)]](https://azure.microsoft.com/documentation/articles/sql-data-warehouse-load-sample-databases/)
 
```sql
-- DimSalesTerritory is hash-distributed.
-- Copy it to a round-robin table.
CREATE TABLE [dbo].[myTable]   
WITH   
  (   
    CLUSTERED COLUMNSTORE INDEX,  
    DISTRIBUTION = ROUND_ROBIN  
  )  
AS SELECT * FROM [dbo].[DimSalesTerritory]; 

-- Switch table names

RENAME OBJECT [dbo].[DimSalesTerritory] to [DimSalesTerritory_old];
RENAME OBJECT [dbo].[myTable] TO [DimSalesTerritory];

DROP TABLE [dbo].[DimSalesTerritory_old];
```  
接下來，將它變更回雜湊散發資料表。

```sql
-- You just made DimSalesTerritory a round-robin table.
-- Change it back to the original hash-distributed table. 
CREATE TABLE [dbo].[myTable]   
WITH   
  (   
    CLUSTERED COLUMNSTORE INDEX,  
    DISTRIBUTION = HASH(SalesTerritoryKey) 
  )  
AS SELECT * FROM [dbo].[DimSalesTerritory]; 

-- Switch table names

RENAME OBJECT [dbo].[DimSalesTerritory] to [DimSalesTerritory_old];
RENAME OBJECT [dbo].[myTable] TO [DimSalesTerritory];

DROP TABLE [dbo].[DimSalesTerritory_old];
```
 
<a name="ctas-change-to-replicated-bk"></a>

### <a name="d-use-ctas-to-convert-a-table-to-a-replicated-table"></a>D. 使用 CTAS 將資料表轉換成複寫資料表  
適用於：[!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] 和 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]

此範例適用於將循環配置資源資料表或雜湊散發資料表轉換為複寫資料表。 這個特殊範例會針對之前的散發類型變更方法，做更進一步的應用。  因為 DimSalesTerritory 是一個維度，而且可能是小型的資料表，因此可以選擇將資料表重新建立為複寫資料表，這樣在聯結至其他資料表時，就能避免移動資料。 

```sql
-- DimSalesTerritory is hash-distributed.
-- Copy it to a replicated table.
CREATE TABLE [dbo].[myTable]   
WITH   
  (   
    CLUSTERED COLUMNSTORE INDEX,  
    DISTRIBUTION = REPLICATE 
  )  
AS SELECT * FROM [dbo].[DimSalesTerritory]; 

-- Switch table names

RENAME OBJECT [dbo].[DimSalesTerritory] to [DimSalesTerritory_old];
RENAME OBJECT [dbo].[myTable] TO [DimSalesTerritory];

DROP TABLE [dbo].[DimSalesTerritory_old];
```
 
### <a name="e-use-ctas-to-create-a-table-with-fewer-columns"></a>E. 使用 CTAS 來建立資料行較少的資料表
適用於：[!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] 和 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]

下列範例會建立名為 `myTable (c, ln)` 的循環配置資源散發資料表。 新的資料表只有兩個資料行。 它會使用 SELECT 陳述式中的資料行別名來作為資料行的名稱。  
  
```sql  
CREATE TABLE myTable  
WITH   
  (   
    CLUSTERED COLUMNSTORE INDEX,  
    DISTRIBUTION = ROUND_ROBIN  
  )  
AS SELECT CustomerKey AS c, LastName AS ln  
    FROM dimCustomer; 
```  

<a name="examples-query-hints-bk"></a>

## <a name="examples-for-query-hints"></a>查詢提示範例

<a name="ctas-query-hint-bk"></a>

### <a name="f-use-a-query-hint-with-create-table-as-select-ctas"></a>F. 查詢提示與 CREATE TABLE AS SELECT (CTAS) 搭配使用  
適用於：[!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] 和 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]
  
此查詢示會示範查詢聯結提示與 CTAS 陳述式搭配使用的基本語法。 提交查詢後，[!INCLUDE[ssSDW](../../includes/sssdw-md.md)] 會在為每一個散發產生查詢計劃時，套用雜湊聯結策略。 如需有關雜湊聯結查詢提示的詳細資訊，請參閱 [OPTION 子句 &#40;Transact-SQL&#41;](../../t-sql/queries/option-clause-transact-sql.md)。  
  
```sql  
CREATE TABLE dbo.FactInternetSalesNew  
WITH   
  (   
    CLUSTERED COLUMNSTORE INDEX,  
    DISTRIBUTION = ROUND_ROBIN   
  )  
AS SELECT T1.* FROM dbo.FactInternetSales T1 JOIN dbo.DimCustomer T2  
ON ( T1.CustomerKey = T2.CustomerKey )  
OPTION ( HASH JOIN );  
```  

<a name="examples-external-tables"></a>

## <a name="examples-for-external-tables"></a>外部資料表範例

<a name="ctas-azure-blob-storage-bk"></a>

### <a name="g-use-ctas-to-import-data-from-azure-blob-storage"></a>G. 使用 CTAS 來從 Azure Blob 儲存體匯入資料  
適用於：[!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] 和 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  

若要從外部資料表匯入資料，只要使用 CREATE TABLE AS SELECT 來從外部資料表進行選取即可。 從外部資料表選取資料來匯入 [!INCLUDE[ssSDW](../../includes/sssdw-md.md)] 時所用的語法，與從一般資料表中選取資料時所使用語法相同。  
  
 下列範例會針對 Azure Blob 儲存體帳戶中的資料，定義一個外部資料表。 接著它會使用 CREATE TABLE AS SELECT，從外部資料表進行選取。 這樣會從 Azure Blob 儲存體文字分隔的檔案匯入資料，然後將資料儲存至新的 [!INCLUDE[ssSDW](../../includes/sssdw-md.md)] 資料表。  
  
```sql  
--Use your own processes to create the text-delimited files on Azure blob storage.  
--Create the external table called ClickStream.  
CREATE EXTERNAL TABLE ClickStreamExt (   
    url VARCHAR(50),  
    event_date DATE,  
    user_IP VARCHAR(50)  
)  
WITH (  
    LOCATION='/logs/clickstream/2015/',  
    DATA_SOURCE = MyAzureStorage,  
    FILE_FORMAT = TextFileFormat)  
;  
  
--Use CREATE TABLE AS SELECT to import the Azure blob storage data into a new   
--Synapse Analytics table called ClickStreamData  
CREATE TABLE ClickStreamData   
WITH  
  (  
    CLUSTERED COLUMNSTORE INDEX,  
    DISTRIBUTION = HASH (user_IP)  
  )  
AS SELECT * FROM ClickStreamExt  
;  
```  

<a name="ctas-import-Hadoop-bk"></a>
  
### <a name="h-use-ctas-to-import-hadoop-data-from-an-external-table"></a>H. 使用 CTAS 來從外部資料表匯入 Hadoop 資料  
適用於：[!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
若要從外部資料表匯入資料，只要使用 CREATE TABLE AS SELECT 來從外部資料表進行選取即可。 從外部資料表選取資料來匯入 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] 時所用的語法，與從一般資料表中選取資料時所使用語法相同。  
  
 下列範例會在 Hadoop 叢集上定義一個外部資料表。 接著它會使用 CREATE TABLE AS SELECT，從外部資料表進行選取。 這樣會從 Hadoop 文字分隔的檔案匯入資料，然後將資料儲存至新的 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] 資料表。  
  
```sql  
-- Create the external table called ClickStream.  
CREATE EXTERNAL TABLE ClickStreamExt (   
    url VARCHAR(50),  
    event_date DATE,  
    user_IP VARCHAR(50)  
)  
WITH (  
    LOCATION = 'hdfs://MyHadoop:5000/tpch1GB/employee.tbl',  
    FORMAT_OPTIONS ( FIELD_TERMINATOR = '|')  
)  
;  
  
-- Use your own processes to create the Hadoop text-delimited files 
-- on the Hadoop Cluster.  
  
-- Use CREATE TABLE AS SELECT to import the Hadoop data into a new 
-- table called ClickStreamPDW  
CREATE TABLE ClickStreamPDW   
WITH  
  (  
    CLUSTERED COLUMNSTORE INDEX,  
    DISTRIBUTION = HASH (user_IP)  
  )  
AS SELECT * FROM ClickStreamExt  
;   
```  
 
<a name="examples-workarounds-bk"></a>
 
## <a name="examples-using-ctas-to-replace-sql-server-code"></a>使用 CTAS 來取代 SQL Server 程式碼的範例

使用 CTAS 來解決一些不支援的功能。 除了可以在資料倉儲上執行自己的程式碼之外，重新撰寫現有程式碼來使用 CTAS，通常還能改善效能。 這是 CTAS 完全平行化設計的結果。 

> [!NOTE]
> 試著「優先考慮 CTAS」。 如果您認為使用 `CTAS` 可以解決問題，通常這是最好的方法，即使您會因此而撰寫更多的資料。
>

<a name="ctas-replace-select-into-bk"></a>

### <a name="i-use-ctas-instead-of-selectinto"></a>I. 使用 CTAS 而不使用 SELECT..INTO  
適用於：[!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] 和 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]

SQL Server 程式碼通常會使用 SELECT...INTO 來將 SELECT 陳述式的結果填入資料表。 這是一個 SQL Server SELECT..INTO 陳述式的範例。

```sql
SELECT *
INTO    #tmp_fct
FROM    [dbo].[FactInternetSales]
```

[!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] 和平行處理資料倉儲不支援這種語法。 這個範例會示範如何將以前的 SELECT..INTO 陳述式，重新撰寫為 CTAS 陳述式。 您可以選擇 CTAS 語法中所描述的任何 DISTRIBUTION 選項。 這個範例會使用 ROUND_ROBIN 散發方法。

```sql
CREATE TABLE #tmp_fct
WITH
(
    DISTRIBUTION = ROUND_ROBIN
)
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
;
```

<a name="ctas-replace-implicit-joins-bk"></a>

### <a name="j-use-ctas-and-implicit-joins-to-replace-ansi-joins-in-the-from-clause-of-an-update-statement"></a>J. 使用 CTAS 和隱含聯結來取代 `UPDATE` 陳述式 `FROM` 子句中的 ANSI 聯結  
適用於：[!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] 和 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  

假設您有一個複雜更新，其中使用 ANSI 聯結語法將兩個以上的資料表聯結起來，以執行 UPDATE 或 DELETE。

想像您必須更新這個資料表：

```sql
CREATE TABLE [dbo].[AnnualCategorySales]
(   [EnglishProductCategoryName]    NVARCHAR(50)    NOT NULL
,   [CalendarYear]          SMALLINT    NOT NULL
,   [TotalSalesAmount]      MONEY       NOT NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN
)
;
```

原始查詢可能看起來像這個樣子：

```sql
UPDATE  acs
SET     [TotalSalesAmount] = [fis].[TotalSalesAmount]
FROM    [dbo].[AnnualCategorySales]     AS acs
JOIN    (
        SELECT  [EnglishProductCategoryName]
        ,       [CalendarYear]
        ,       SUM([SalesAmount])              AS [TotalSalesAmount]
        FROM    [dbo].[FactInternetSales]       AS s
        JOIN    [dbo].[DimDate]                 AS d    ON s.[OrderDateKey]             = d.[DateKey]
        JOIN    [dbo].[DimProduct]              AS p    ON s.[ProductKey]               = p.[ProductKey]
        JOIN    [dbo].[DimProductSubCategory]   AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
        JOIN    [dbo].[DimProductCategory]      AS c    ON u.[ProductCategoryKey]       = c.[ProductCategoryKey]
        WHERE   [CalendarYear] = 2004
        GROUP BY
                [EnglishProductCategoryName]
        ,       [CalendarYear]
        ) AS fis
ON  [acs].[EnglishProductCategoryName]  = [fis].[EnglishProductCategoryName]
AND [acs].[CalendarYear]                = [fis].[CalendarYear]
;
```

因為 [!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)]不支援 `UPDATE` 陳述式 `FROM` 子句中的 ANSI 聯結，因此您必須微幅修改這個 SQL Server 程式碼，否則無法使用。

您可以使用 `CTAS` 和隱含聯結的組合來取代此程式碼：

```sql
-- Create an interim table
CREATE TABLE CTAS_acs
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT  ISNULL(CAST([EnglishProductCategoryName] AS NVARCHAR(50)),0)    AS [EnglishProductCategoryName]
,       ISNULL(CAST([CalendarYear] AS SMALLINT),0)                      AS [CalendarYear]
,       ISNULL(CAST(SUM([SalesAmount]) AS MONEY),0)                     AS [TotalSalesAmount]
FROM    [dbo].[FactInternetSales]       AS s
JOIN    [dbo].[DimDate]                 AS d    ON s.[OrderDateKey]             = d.[DateKey]
JOIN    [dbo].[DimProduct]              AS p    ON s.[ProductKey]               = p.[ProductKey]
JOIN    [dbo].[DimProductSubCategory]   AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
JOIN    [dbo].[DimProductCategory]      AS c    ON u.[ProductCategoryKey]       = c.[ProductCategoryKey]
WHERE   [CalendarYear] = 2004
GROUP BY
        [EnglishProductCategoryName]
,       [CalendarYear]
;

-- Use an implicit join to perform the update
UPDATE  AnnualCategorySales
SET     AnnualCategorySales.TotalSalesAmount = CTAS_ACS.TotalSalesAmount
FROM    CTAS_acs
WHERE   CTAS_acs.[EnglishProductCategoryName] = AnnualCategorySales.[EnglishProductCategoryName]
AND     CTAS_acs.[CalendarYear]               = AnnualCategorySales.[CalendarYear]
;

--Drop the interim table
DROP TABLE CTAS_acs
;
```

<a name="ctas-replace-ansi-joins-bk"></a>

### <a name="k-use-ctas-to-specify-which-data-to-keep-instead-of-using-ansi-joins-in-the-from-clause-of-a-delete-statement"></a>K. 使用 CTAS 來指定要保留的資料，而不是在 DELETE 陳述式的 FROM 子句中使用 ANSI 聯結  
適用於：[!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] 和 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  

有時候，刪除資料的最佳方法是使用 `CTAS`。 您不用刪除資料，只要選取想要保留的資料即可。 對於使用 ANSI 聯結語法的 `DELETE` 陳述式更是如此，因為 [!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)]不支援 `DELETE` 陳述式 `FROM` 子句的 ANSI 聯結。

以下是已轉換的 DELETE 陳述式範例：

```sql
CREATE TABLE dbo.DimProduct_upsert
WITH
(   Distribution=HASH(ProductKey)
,   CLUSTERED INDEX (ProductKey)
)
AS -- Select Data you wish to keep
SELECT     p.ProductKey
,          p.EnglishProductName
,          p.Color
FROM       dbo.DimProduct p
RIGHT JOIN dbo.stg_DimProduct s
ON         p.ProductKey = s.ProductKey
;

RENAME OBJECT dbo.DimProduct        TO DimProduct_old;
RENAME OBJECT dbo.DimProduct_upsert TO DimProduct;
```

<a name="ctas-simplify-merge-bk"></a>

### <a name="l-use-ctas-to-simplify-merge-statements"></a>L. 使用 CTAS 來簡化合併陳述式  
適用於：[!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] 和 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  

使用 `CTAS` 可以取代至少部分取代合併陳述式。 您可以將 `INSERT` 和 `UPDATE` 合併成一個陳述式。 任何刪除的記錄都應該在第二個陳述式中關閉。

以下是一個 `UPSERT` 範例：

```sql
CREATE TABLE dbo.[DimProduct_upsert]
WITH
(   DISTRIBUTION = HASH([ProductKey])
,   CLUSTERED INDEX ([ProductKey])
)
AS
-- New rows and new versions of rows
SELECT      s.[ProductKey]
,           s.[EnglishProductName]
,           s.[Color]
FROM      dbo.[stg_DimProduct] AS s
UNION ALL  
-- Keep rows that are not being touched
SELECT      p.[ProductKey]
,           p.[EnglishProductName]
,           p.[Color]
FROM      dbo.[DimProduct] AS p
WHERE NOT EXISTS
(   SELECT  *
    FROM    [dbo].[stg_DimProduct] s
    WHERE   s.[ProductKey] = p.[ProductKey]
)
;

RENAME OBJECT dbo.[DimProduct]          TO [DimProduct_old];
RENAME OBJECT dbo.[DimProduct_upsert]  TO [DimProduct];

```

<a name="ctas-data-type-and-nullability-bk"></a>

### <a name="m-explicitly-state-data-type-and-nullability-of-output"></a>M. 明確陳述資料類型和輸出可為 null  
適用於：[!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] 和 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  

將 SQL Server 程式碼移轉至 [!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)]時，您可能碰到這種類型的程式碼模式：

```sql
DECLARE @d DECIMAL(7,2) = 85.455
,       @f FLOAT(24)    = 85.455

CREATE TABLE result
(result DECIMAL(7,2) NOT NULL
)
WITH (DISTRIBUTION = ROUND_ROBIN)

INSERT INTO result
SELECT @d*@f
;
```

您可能會憑直覺認為自己應該將這段程式碼移轉至 CTAS，而這正是正確的做法。 不過，這其中隱藏了一個問題。

下列程式碼不會產生相同的結果：

```sql
DECLARE @d DECIMAL(7,2) = 85.455
,       @f FLOAT(24)    = 85.455
;

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT @d*@f as result
;
```

請注意，資料行 "result" 會帶有運算式的資料類型和可 Null 性。 如果您不小心，這可能會導致值出現細微的變化。

請試試以下範例：

```sql
SELECT result,result*@d
from result
;

SELECT result,result*@d
from ctas_r
;
```

為結果而儲存的值不相同。 當結果資料行中的持續值用在其他運算式時，錯誤會變得更加明顯。

![CREATE TABLE AS SELECT 結果](../../t-sql/statements/media/create-table-as-select-results.png)

這對於資料移轉而言特別重要。 儘管第二個查詢無疑更為準確，但還是有一個問題。 與來源系統相比之下，資料會不同，因而導致資料在移轉時出現完整性問題。 這是罕見案例之一，也就是「錯誤」的答案實際上是正確的答案！

之所以會在這兩個結果之間看到差異，原因與隱含類型轉換有關。 在第一個範例中，資料表定義了資料行。 插入資料列時，就會發生隱含類型轉換。 在第二個範例中，沒有任何隱含類型轉換，因為運算式會定義資料行的資料類型。 另請注意，第二個範例中的資料行已定義成一個可為 Null 的資料行，而在第一個範例中並未這樣定義。 在第一個範例中建立資料表時，會明確定義料資料行的可 Null 性。 在第二個範例中，則交給運算式決定。根據預設，這樣會產生一個 NULL 定義。  

若要解決這些問題，您必須在 `CTAS` 陳述式的 `SELECT` 部分，明確設定型別轉換和可 Null 性。 您無法在 CREATE TABLE 部分中設定這些屬性。

下列範例會示範如何修正程式碼：

```sql
DECLARE @d DECIMAL(7,2) = 85.455
,       @f FLOAT(24)    = 85.455

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT ISNULL(CAST(@d*@f AS DECIMAL(7,2)),0) as result
```

請注意：
- 原本可使用 CAST 或 CONVERT
- ISNULL 是用來強制可 Null 性，而不是 COALESCE
- ISNULL 是最外層的函數
- ISNULL 的第二個部分是一個常數，也就是 0

> [!NOTE]
> 若要正確地設定可 Null 性，請務必使用 `ISNULL`，而不要使用 `COALESCE`。 `COALESCE` 不是一個確定性函數，因此運算式的結果將永遠是可為 Null。 `ISNULL` 不一樣。 它是具確定性的。 因此，當 `ISNULL` 函數的第個二部分是一個常數或常值時，則產生的值將會是非 Null。

這個提示不僅是在確定計算方式的完整性時很實用， 對於資料表資料分割切換也是很重要。 想像您將這個資料表定義為事實：

```sql
CREATE TABLE [dbo].[Sales]
(
    [date]      INT     NOT NULL
,   [product]   INT     NOT NULL
,   [store]     INT     NOT NULL
,   [quantity]  INT     NOT NULL
,   [price]     MONEY   NOT NULL
,   [amount]    MONEY   NOT NULL
)
WITH
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
)
;
```

不過，值欄位是一個計算運算式，它不是來源資料的一部分。

若要建立分割的資料集，建議您這樣做：

```sql
CREATE TABLE [dbo].[Sales_in]
WITH    
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
    [date]    
,   [product]
,   [store]
,   [quantity]
,   [price]   
,   [quantity]*[price]  AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create')
;
```

這個查詢可正確執行。 當您嘗試執行資料分割切換時，便會發生問題。 資料表定義不相符。 若要讓資料表定義相符，需要修改 CTAS。

```sql
CREATE TABLE [dbo].[Sales_in]
WITH    
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
    [date]    
,   [product]
,   [store]
,   [quantity]
,   [price]   
,   ISNULL(CAST([quantity]*[price] AS MONEY),0) AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create');
```

因此，您便知道 CTAS 上的類型一致性以及維護可 Null 性屬性，是正確的操縱最佳作法。 這有助於維護計算的完整性，也能夠確定資料分割切換的可行性。

### <a name="n-create-an-ordered-clustered-columnstore-index-with-maxdop-1"></a>N. 使用 MAXDOP 1 建立已排序的叢集資料行存放區索引  
```sql
CREATE TABLE Table1 WITH (DISTRIBUTION = HASH(c1), CLUSTERED COLUMNSTORE INDEX ORDER(c1) )
AS SELECT * FROM ExampleTable
OPTION (MAXDOP 1);
```

 
## <a name="see-also"></a>另請參閱  
 [CREATE EXTERNAL DATA SOURCE &#40;Transact-SQL&#41;](../../t-sql/statements/create-external-data-source-transact-sql.md)   
 [CREATE EXTERNAL FILE FORMAT &#40;Transact-SQL&#41;](../../t-sql/statements/create-external-file-format-transact-sql.md)   
 [CREATE EXTERNAL TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/create-external-table-transact-sql.md)   
 [CREATE EXTERNAL TABLE AS SELECT &#40;Transact-SQL&#41;](../../t-sql/statements/create-external-table-as-select-transact-sql.md)   
 [CREATE TABLE &#40;Azure Synapse Analytics&#41;](../../t-sql/statements/create-table-azure-sql-data-warehouse.md) [DROP TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/drop-table-transact-sql.md)   
 [DROP EXTERNAL TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/drop-external-table-transact-sql.md)   
 [ALTER TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-table-transact-sql.md)   
 [ALTER EXTERNAL TABLE &#40;Transact-SQL&#41;](./create-external-table-transact-sql.md)  
