---
description: CREATE STATISTICS (Transact-SQL)
title: CREATE STATISTICS (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 01/04/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- STATISTICS
- STATISTICS_TSQL
- CREATE STATISTICS
- CREATE_STATISTICS_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- query optimization statistics [SQL Server], creating
- indexed views [SQL Server], statistics
- FULLSCAN option
- CREATE STATISTICS statement
- filtered statistics [SQL Server]
- creating statistics [SQL Server]
- NORECOMPUTE clause
ms.assetid: b23e2f6b-076c-4e6d-9281-764bdb616ad2
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: d0e799f28d327204707298dc63a4fb1a8470fa2f
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98170520"
---
# <a name="create-statistics-transact-sql"></a>CREATE STATISTICS (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  在資料表、索引檢視表或外部資料表的一或多個資料行建立查詢最佳化統計資料。 對於大部分查詢而言，查詢最佳化工具已經產生高品質查詢計劃的必要統計資料。不過，在少數情況下，您必須使用 CREATE STATISTICS 來建立其他統計資料或修改查詢設計，以便改善查詢效能。  
  
 若要深入了解，請參閱[統計資料](../../relational-databases/statistics/statistics.md)。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
-- Syntax for SQL Server and Azure SQL Database  
  
-- Create statistics on an external table  
CREATE STATISTICS statistics_name   
ON { table_or_indexed_view_name } ( column [ ,...n ] )   
    [ WITH FULLSCAN ] ;  
  
-- Create statistics on a regular table or indexed view  
CREATE STATISTICS statistics_name   
ON { table_or_indexed_view_name } ( column [ ,...n ] )   
    [ WHERE <filter_predicate> ]  
    [ WITH   
        [ [ FULLSCAN   
            [ [ , ] PERSIST_SAMPLE_PERCENT = { ON | OFF } ]    
          | SAMPLE number { PERCENT | ROWS }   
            [ [ , ] PERSIST_SAMPLE_PERCENT = { ON | OFF } ]    
          | <update_stats_stream_option> [ ,...n ]    
        [ [ , ] NORECOMPUTE ]   
        [ [ , ] INCREMENTAL = { ON | OFF } ] 
        [ [ , ] MAXDOP = max_degree_of_parallelism ]
    ] ;  
  
<filter_predicate> ::=   
    <conjunct> [AND <conjunct>]  
  
<conjunct> ::=  
    <disjunct> | <comparison>  
  
<disjunct> ::=  
        column_name IN (constant ,...)  
  
<comparison> ::=  
        column_name <comparison_op> constant  
  
<comparison_op> ::=  
    IS | IS NOT | = | <> | != | > | >= | !> | < | <= | !<  
    
<update_stats_stream_option> ::=  
    [ STATS_STREAM = stats_stream ]  
    [ ROWCOUNT = numeric_constant ]  
    [ PAGECOUNT = numeric_contant ] 
```  
  
```syntaxsql
-- Syntax for Azure Synapse Analytics and Parallel Data Warehouse  
  
CREATE STATISTICS statistics_name   
    ON { database_name.schema_name.table_name | schema_name.table_name | table_name }
    ( column_name  [ ,...n ] )   
    [ WHERE <filter_predicate> ]  
    [ WITH {  
           FULLSCAN   
           | SAMPLE number PERCENT   
      }  
    ]  
[;]  
  
<filter_predicate> ::=   
    <conjunct> [AND <conjunct>]  
  
<conjunct> ::=  
    <disjunct> | <comparison>  
  
<disjunct> ::=  
        column_name IN (constant ,...)  
  
<comparison> ::=  
        column_name <comparison_op> constant  
  
<comparison_op> ::=  
    IS | IS NOT | = | <> | != | > | >= | !> | < | <= | !<  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *statistics_name*  
 這是要建立之統計資料的名稱。  
  
 *table_or_indexed_view_name*  
 這是要在其上建立統計資料之資料表、索引檢視表或外部資料表的名稱。 若要在另一個資料庫上建立統計資料，請指定限定的資料表名稱。  
  
 *column [ ,...n]*  
 要在統計資料中包含的一或多個資料行。 資料行應該由左至右依優先順序排列。 只有第一個資料行用來建立色階分佈圖。 所有資料行都是用於名為密度的跨資料行相互關聯統計資料。  
  
 您可以指定任何可指定為索引鍵資料行的資料行，但下列例外狀況除外：  
  
-   您無法指定 **Xml**、全文檢索和 FILESTREAM 資料行。  
  
-   只有在 ARITHABORT 和 QUOTED_IDENTIFIER 資料庫設定為 ON 時，才能指定計算資料行。  
  
-   如果 CLR 使用者定義型別可支援二進位排序，您可以指定這個類型的資料行。 如果方法標示為具決定性，就能指定從使用者定義型別資料行中定義為方法引動過程的計算資料行。  
  
 WHERE \<filter_predicate> 指定運算式，以便選取在建立統計資料物件時要包含的資料列子集。 使用篩選述詞所建立的統計資料稱為篩選的統計資料。 篩選述詞會使用簡單比較邏輯，而且無法參考計算資料行、UDT 資料行、空間資料類型資料行或 **hierarchyID** 資料類型資料行。 比較運算子不允許使用 NULL 常值的比較。 請改用 IS NULL 和 IS NOT NULL 運算子。  
  
 以下是 Production.BillOfMaterials 資料表之篩選述詞的一些範例：  
  
 * `WHERE StartDate > '20000101' AND EndDate <= '20000630'`  
  
 * `WHERE ComponentID IN (533, 324, 753)`  
  
 * `WHERE StartDate IN ('20000404', '20000905') AND EndDate IS NOT NULL`  
  
 如需篩選述詞的詳細資訊，請參閱[建立篩選的索引](../../relational-databases/indexes/create-filtered-indexes.md)。  
  
 FULLSCAN  
 透過掃描所有資料列來計算統計資料。 FULLSCAN 和 SAMPLE 100 PERCENT 的結果相同。 FULLSCAN 不能搭配 SAMPLE 選項一起使用。  
  
 當略過時，SQL Server 會使用取樣來建立統計資料，並判斷建立高品質查詢計劃所需的樣本大小  
  
 SAMPLE *number* { PERCENT | ROWS }  
 指定當查詢最佳化工具建立統計資料時，要在資料表或索引檢視表中使用的近似百分比或資料列數目。 針對 PERCENT，*number* 可以介於 0 到 100 之間；針對 ROWS，*number* 可以介於 0 到總資料列數目之間。 查詢最佳化工具所取樣的實際百分比或資料列數目可能會與指定的百分比或數目不符。 例如，查詢最佳化工具會掃描資料頁面上的所有資料列。  
  
 在特殊情況下，根據預設取樣的查詢計劃並非最佳化，此時 SAMPLE 便非常有用。 通常，查詢最佳化工具已經會依預設使用取樣並決定具有統計價值的取樣大小，因此不需要使用 SAMPLE 便可以建立高品質的查詢計劃。  
  
 SAMPLE 不能和 FULLSCAN 選項一起使用。 如果 SAMPLE 或 FULLSCAN 都未指定，查詢最佳化工具會依預設使用取樣資料並計算取樣大小。  
  
 我們建議您不要指定 0 PERCENT 或 0 ROWS。 將 PERCENT 或 ROWS 指定為 0 時，雖會建立統計資料物件，但是不會包含統計資料。  
 
 PERSIST_SAMPLE_PERCENT = { ON | OFF }  
 當設定為 **ON** 時，統計資料將針對未明確指定取樣百分比的後續更新，保留特定的取樣百分比。 當設定為 **OFF** 時，統計資料取樣百分比將重設為未明確指定取樣百分比之後續更新中的預設取樣。 預設值為 **OFF**。 
 
 > [!NOTE]
 > 如果資料表遭到截斷，則所有以遭截斷 HoBT 為基礎建置的統計資料都會還原至使用預設取樣百分比。

 **適用於**：[!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] (從 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] SP1 CU4 開始) 及更新版本 (從 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] CU1 開始)。    
  
 STATS_STREAM **=** _stats_stream_  
 [!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]  
  
 NORECOMPUTE  
 針對 *statistics_name* 停用自動統計資料更新選項 AUTO_STATISTICS_UPDATE。 如果您指定了這個選項，查詢最佳化工具就會針對 *statistics_name* 完成任何進行中的統計資料更新並停用未來的更新。  
  
 若要重新啟用統計資料更新，請使用 [DROP STATISTICS](../../t-sql/statements/drop-statistics-transact-sql.md) 來移除統計資料，然後再執行不含 NORECOMPUTE 選項的 CREATE STATISTICS。  
  
> [!WARNING]  
> 使用這個選項可能會產生次佳查詢計劃。 我們建議您盡量少用這個選項，而且只有合格的系統管理員可以使用。  
  
 如需 AUTO_STATISTICS_UPDATE 選項的詳細資訊，請參閱 [ALTER DATABASE SET 選項 &#40;Transact-SQL&#41;](../../t-sql/statements/alter-database-transact-sql-set-options.md)。 如需停用及重新啟用統計資料更新的詳細資訊，請參閱[統計資料](../../relational-databases/statistics/statistics.md)。  
  
 INCREMENTAL = { ON | OFF }  
 若設定為 **ON**，所建立的統計資料會以每個資料分割統計資料為依據。 設定為 **OFF** 時，會合併所有資料分割的統計資料。 預設值為 **OFF**。  
  
 如果不支援依據每個分割區區的統計資料，則會產生錯誤。 針對下列統計資料類型，不支援累加統計資料：  
  
-   建立統計資料時，所使用的索引未與基底資料表進行分割區對齊。  
-   在 AlwaysOn 可讀取次要資料庫上建立的統計資料。  
-   在唯讀資料庫上建立的統計資料。  
-   在篩選的索引上建立的統計資料。  
-   在檢視上建立的統計資料。  
-   在內部資料表上建立的統計資料。  
-   使用空間索引或 XML 索引建立的統計資料。  
  
**適用對象**：[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 及更新版本。  
  
MAXDOP = *max_degree_of_parallelism*  
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] SP2 和 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] CU3 開始)。  
  
 在統計作業期間，覆寫 **max degree of parallelism** 設定選項。 如需詳細資訊，請參閱 [設定 max degree of parallelism 伺服器組態選項](../../database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option.md)。 請利用 MAXDOP 來限制執行平行計畫所用的處理器數目。 最大值是 64 個處理器。  
  
 *max_degree_of_parallelism* 可以是：  
  
 1  
 隱藏平行計畫的產生。  
  
 \>1  
 根據目前的系統工作負載，將平行統計作業所使用的處理器數目上限，限制為所指定的數目或更少的數目。  
  
 0 (預設值)  
 根據目前的系統工作負載，使用實際數目或比實際數目更少的處理器。  
  
 \<update_stats_stream_option> [!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]  

## <a name="permissions"></a>權限  
 需要下列權限其中一個權限：  
  
-   ALTER TABLE  
-   使用者是資料表擁有者  
-   **db_ddladmin** 固定資料庫角色中的成員資格  
  
## <a name="general-remarks"></a>一般備註  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 可以使用 tempdb 在建立統計資料之前，將取樣的資料列排序。  
  
### <a name="statistics-for-external-tables"></a>外部資料表的統計資料  
 建立外部資料表統計資料時，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會將外部資料表匯入暫存的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 資料表，然後建立統計資料。 針對範例統計資料，只有取樣資料列會匯入。 如果您有大型的外部資料表，使用預設取樣的速度會比完整掃描選項快得多。  
  
### <a name="statistics-with-a-filtered-condition"></a>具有已篩選條件的統計資料  
 對於從定義完善的資料子集中選取的查詢而言，篩選的統計資料可以改善查詢效能。 篩選的統計資料會在 WHERE 子句中使用篩選述詞來選取統計資料中所含的資料子集。  
  
### <a name="when-to-use-create-statistics"></a>使用 CREATE STATISTICS 的時機  
 如需使用 CREATE STATISTICS 之時機的詳細資訊，請參閱[統計資料](../../relational-databases/statistics/statistics.md)。  
  
### <a name="referencing-dependencies-for-filtered-statistics"></a>篩選統計資料的參考相依性  
 [sys.sql_expression_dependencies](../../relational-databases/system-catalog-views/sys-sql-expression-dependencies-transact-sql.md)目錄檢視會將篩選統計資料述詞中的每個資料行當做參考相依性來追蹤。 請在建立篩選統計資料之前先考慮要在資料表資料行上執行的作業，因為在篩選統計資料述詞中定義的資料表資料行是無法卸除、重新命名或變更定義的。  
  
## <a name="limitations-and-restrictions"></a>限制事項  
* 不支援更新外部資料表上的統計資料。 若要更新外部資料表上的統計資料，請卸除並重新建立統計資料。  
* 您最多可以針對每個統計資料物件列出 64 個資料行。
* MAXDOP 選項與 STATS_STREAM、ROWCOUNT 及 PAGECOUNT 選項不相容。
* 如果使用 MAXDOP 選項，會受限於 Resource Governor 工作負載群組 MAX_DOP 設定。
* Azure SQL Database 不支援在外部資料表上使用 CREATE 和 DROP STATISTICS。
  
## <a name="examples"></a>範例  

### <a name="examples-use-the-adventureworks-database"></a>範例使用的是 AdventureWorks 資料庫。  

### <a name="a-using-create-statistics-with-sample-number-percent"></a>A. 搭配 SAMPLE 數目 PERCENT 使用 CREATE STATISTICS  
 下列範例會使用 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 資料庫的 `Person` 資料表內，`BusinessEntityID` 和 `EmailPromotion` 資料行的 5% 隨機取樣，來建立 `ContactMail1` 統計資料。  
  
```sql  
CREATE STATISTICS ContactMail1  
    ON Person.Person (BusinessEntityID, EmailPromotion)  
    WITH SAMPLE 5 PERCENT;  
```  
  
### <a name="b-using-create-statistics-with-fullscan-and-norecompute"></a>B. 搭配 FULLSCAN 和 NORECOMPUTE 使用 CREATE STATISTICS  
 下列範例會針對 `NamePurchase` 資料表的 `BusinessEntityID` 和 `EmailPromotion` 資料行中的所有資料列來建立 `Person` 統計資料，且會停用統計資料的自動重新計算。  
  
```sql  
CREATE STATISTICS NamePurchase  
    ON AdventureWorks2012.Person.Person (BusinessEntityID, EmailPromotion)  
    WITH FULLSCAN, NORECOMPUTE;  
```  
  
### <a name="c-using-create-statistics-to-create-filtered-statistics"></a>C. 使用 CREATE STATISTICS 來建立篩選的統計資料  
 下列範例會建立篩選的統計資料 `ContactPromotion1`。 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 會取樣百分之 50 的資料，然後選取 `EmailPromotion` 等於 2 的所有資料列。  
  
```sql  
CREATE STATISTICS ContactPromotion1  
    ON Person.Person (BusinessEntityID, LastName, EmailPromotion)  
WHERE EmailPromotion = 2  
WITH SAMPLE 50 PERCENT;  
GO  
```  
  
### <a name="d-create-statistics-on-an-external-table"></a>D. 在外部資料表上建立統計資料  
 在外部資料表上建立統計資料時，除了提供資料行清單之外，您唯一要做的決定是要透過為資料列進行取樣或透過掃描所有資料列來建立統計資料。 Azure SQL Database 不支援在外部資料表上使用 CREATE 和 DROP STATISTICS。
  
 由於 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會從外部資料表匯入資料到暫存資料表中，以建立統計資料，因此完整掃描選項會花費更長的時間。 針對大型資料表，預設的取樣方法通常就已足夠。  
  
```sql  
--Create statistics on an external table and use default sampling.  
CREATE STATISTICS CustomerStats1 ON DimCustomer (CustomerKey, EmailAddress);  
  
--Create statistics on an external table and scan all the rows  
CREATE STATISTICS CustomerStats1 ON DimCustomer (CustomerKey, EmailAddress) WITH FULLSCAN;  
```  

### <a name="e-using-create-statistics-with-fullscan-and-persist_sample_percent"></a>E. 搭配 FULLSCAN 和 PERSIST_SAMPLE_PERCENT 使用 CREATE STATISTICS  
 下列範例會針對 `BusinessEntityID` 中所有資料列和 `Person` 資料表中的 `EmailPromotion` 資料行建立 `NamePurchase` 統計資料，並針對未明確指定取樣百分比的所有後續更新，設定 100% 取樣百分比。  
  
```sql  
CREATE STATISTICS NamePurchase  
    ON AdventureWorks2012.Person.Person (BusinessEntityID, EmailPromotion)  
    WITH FULLSCAN, PERSIST_SAMPLE_PERCENT = ON;  
```  
  
### <a name="examples-using-adventureworksdw-database"></a>使用 AdventureWorksDW 資料庫的範例。 
  
### <a name="f-create-statistics-on-two-columns"></a>F. 建立兩個資料行的統計資料  
 下列範例會根據 `DimCustomer` 資料表的 `CustomerKey` 和 `EmailAddress` 資料行，建立 `CustomerStats1` 統計資料。 該統計資料是根據 `Customer` 資料表中統計上很重要的資料列取樣而建立的。  
  
```sql  
CREATE STATISTICS CustomerStats1 ON DimCustomer (CustomerKey, EmailAddress);  
```  
  
### <a name="g-create-statistics-by-using-a-full-scan"></a>G. 使用完整掃描建立統計資料  
 下列範例會根據掃描 `DimCustomer` 資料表中的所有資料列來建立 `CustomerStatsFullScan` 統計資料。  
  
```sql  
CREATE STATISTICS CustomerStatsFullScan 
ON DimCustomer (CustomerKey, EmailAddress) WITH FULLSCAN;  
```  
  
### <a name="h-create-statistics-by-specifying-the-sample-percentage"></a>H. 透過指定取樣百分比來建立統計資料  
 下列範例會根據掃描 `DimCustomer` 資料表中 50% 的資料列來建立 `CustomerStatsSampleScan` 統計資料。  
  
```sql  
CREATE STATISTICS CustomerStatsSampleScan 
ON DimCustomer (CustomerKey, EmailAddress) WITH SAMPLE 50 PERCENT;  
```  
  
## <a name="see-also"></a>另請參閱  
 [統計資料](../../relational-databases/statistics/statistics.md)   
 [UPDATE STATISTICS &#40;Transact-SQL&#41;](../../t-sql/statements/update-statistics-transact-sql.md)   
 [sp_updatestats &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-updatestats-transact-sql.md)   
 [DBCC SHOW_STATISTICS &#40;Transact-SQL&#41;](../../t-sql/database-console-commands/dbcc-show-statistics-transact-sql.md)   
 [DROP STATISTICS &#40;Transact-SQL&#41;](../../t-sql/statements/drop-statistics-transact-sql.md)   
 [sys.stats &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-stats-transact-sql.md)   
 [sys.stats_columns &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-stats-columns-transact-sql.md)  
  
  

