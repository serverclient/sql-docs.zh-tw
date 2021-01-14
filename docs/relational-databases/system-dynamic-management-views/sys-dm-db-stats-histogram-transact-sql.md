---
description: sys.dm_db_stats_histogram (Transact-SQL)
title: sys.dm_db_stats_histogram (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: system-objects
ms.topic: conceptual
f1_keywords:
- sys.dm_db_stats_histogram
- sys.dm_db_stats_histogram_TSQL
- dm_db_stats_histogram
- dm_db_stats_histogram_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_db_stats_histogram dynamic management function
ms.assetid: 1897fd4a-8d51-461e-8ef2-c60be9e563f2
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 240737da03fa21d1076251062d99f64b46aecad8
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98168181"
---
# <a name="sysdm_db_stats_histogram-transact-sql"></a>sys.dm_db_stats_histogram (Transact-SQL)

[!INCLUDE [sqlserver2016-asdb-asdbmi](../../includes/applies-to-version/sqlserver2016-asdb-asdbmi.md)]

傳回目前資料庫中 (資料表或索引視圖) 之指定資料庫物件的統計資料長條圖 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。 類似於 `DBCC SHOW_STATISTICS WITH HISTOGRAM`。

> [!NOTE] 
> 從 SP1 開始提供此 DMF [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] CU2

## <a name="syntax"></a>語法  
  
```  
sys.dm_db_stats_histogram (object_id, stats_id)  
```  
  
## <a name="arguments"></a>引數  
 object_id  
 這是目前資料庫中，要求其中一個統計資料屬性之物件的識別碼。 *object_id* 為 **int**。  
  
 *stats_id*  
 這是指定 *object_id* 之統計資料的識別碼。 您可以從 [sys.stats](../../relational-databases/system-catalog-views/sys-stats-transact-sql.md) 動態管理檢視取得統計資料識別碼。 *stats_id* 是 **int**。  
  
## <a name="table-returned"></a>傳回的資料表  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|object_id |**int**|要傳回統計資料物件屬性之物件 (資料表或索引檢視表) 的識別碼。|  
|stats_id |**int**|統計資料物件的識別碼。 這在資料表或索引檢視表中是唯一的。 如需詳細資訊，請參閱 [sys.stats &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-stats-transact-sql.md)。|  
|step_number |**int** |長條圖中的步驟數。 |
|range_high_key |**sql_variant** |長條圖步驟的上限資料行值。 此資料行值也稱為索引鍵值。|
|range_rows |**real** |資料行值在長條圖步驟內的預估資料列數，不包括上限。 |
|equal_rows |**real** |資料行值等於長條圖步驟之上限的預估資料列數。 |
|distinct_range_rows |**bigint** |在長條圖步驟內具有相異資料行值的預估資料列數，不包括上限。 |
|average_range_rows |**real** |長條圖步驟內具有重復資料行值的平均資料列數目，不包括) 的上限 (`RANGE_ROWS / DISTINCT_RANGE_ROWS` `DISTINCT_RANGE_ROWS > 0` 。 |
  
 ## <a name="remarks"></a>備註  
 
 的 resultset 會傳回 `sys.dm_db_stats_histogram` 類似的資訊， `DBCC SHOW_STATISTICS WITH HISTOGRAM` 也包含 `object_id` 、 `stats_id` 和 `step_number` 。

 因為資料行 `range_high_key` 是 SQL_variant 的資料類型，所以您可能需要使用 `CAST` 或述詞 `CONVERT` 與非字串常數進行比較。

### <a name="histogram"></a>長條圖
  
 長條圖會測量資料集中每一個相異值的發生頻率。 查詢最佳化工具會計算有關統計資料物件之第一個索引鍵資料行中資料行值的長條圖，以統計方式取樣資料列或執行資料表或檢視表中所有資料列的完整掃描來選取資料行值。 如果長條圖是從一組取樣的資料列所建立，資料列數和相異值數的儲存總計會是預估值，而且不需要為整數。  
  
 若要建立長條圖，查詢最佳化工具會排序資料行值、計算符合每一個相異資料行值的值數目，然後將資料行值彙總成最多 200 個連續長條圖步驟。 每一個步驟都包含某個範圍的資料行值，後面緊接著上限資料行值。 此範圍包括界限值之間的所有可能資料行值，但是不包括界限值本身。 最低的已排序資料行值就是第一個長條圖步驟的上限值。  
  
 下列長條圖顯示包含六個步驟的長條圖。 第一個上限值左側的區域就是第一個步驟。  
  
 ![長條圖](../../relational-databases/system-dynamic-management-views/media/histogram-2.svg "長條圖")  
  
 每一個長條圖步驟：  
  
-   粗線代表上限值 (*range_high_key*) 以及其所發生的次數 (*equal_rows*)  
  
-   *range_high_key* 左邊的實線區域代表資料行值範圍，以及每一個資料行值發生的平均次數 (*average_range_rows*)。 第一個長條圖步驟的 *average_range_rows* 一定是 0。  
  
-   虛線代表用來預估範圍內相異值總數的取樣值 (*distinct_range_rows*) 以及範圍內的值總數 (*range_rows*)。 查詢最佳化工具會使用 *range_rows* 和 *distinct_range_rows* 來計算 *average_range_rows*，而且不會儲存取樣值。  
  
 查詢最佳化工具會根據長條圖步驟的統計重要性來定義長條圖步驟。 它會使用最大值差異演算法，讓長條圖中的步驟數減至最少，同時讓界限值之間的差異最大化。 步驟數的最大值為 200。 長條圖步驟的數目可以少於相異值數目，即使包含了少於 200 個界限點的資料行也是如此。 例如，包含 100 個相異值的資料行可以擁有少於 100 個界限點的長條圖。  
  
## <a name="permissions"></a>權限  

要求使用者對於統計資料資料行擁有選取權限，或是使用者擁有資料表，或使用者是 `sysadmin` 固定伺服器角色、`db_owner` 固定資料庫角色或 `db_ddladmin` 固定資料庫角色的成員。

## <a name="examples"></a>範例  

### <a name="a-simple-example"></a>A. 簡單範例    
下列範例會建立並填入簡單的資料表。 然後，在資料行上建立統計資料 `Country_Name` 。

```sql
CREATE TABLE Country
(Country_ID int IDENTITY PRIMARY KEY,
Country_Name varchar(120) NOT NULL);
INSERT Country (Country_Name) VALUES ('Canada'), ('Denmark'), ('Iceland'), ('Peru');

CREATE STATISTICS Country_Stats  
    ON Country (Country_Name) ;  
```   
主鍵會佔用 `stat_id` 數位1，因此呼叫 `sys.dm_db_stats_histogram` `stat_id` 數位2，以傳回資料表的統計資料長條圖 `Country` 。    
```sql     
SELECT * FROM sys.dm_db_stats_histogram(OBJECT_ID('Country'), 2);
```

### <a name="b-useful-query"></a>B. 有用的查詢：   
```sql  
SELECT hist.step_number, hist.range_high_key, hist.range_rows, 
    hist.equal_rows, hist.distinct_range_rows, hist.average_range_rows
FROM sys.stats AS s
CROSS APPLY sys.dm_db_stats_histogram(s.[object_id], s.stats_id) AS hist
WHERE s.[name] = N'<statistic_name>';
```

### <a name="c-useful-query"></a>C. 有用的查詢：
下列範例會從資料表中選取具有述詞的資料 `Country` 行 `Country_Name` 。

```sql  
SELECT * FROM Country 
WHERE Country_Name = 'Canada';
```

下列範例會 `Country` `Country_Name` 針對符合上述查詢中述詞的長條圖步驟，查看資料表和資料行上先前建立的統計資料。

```sql  
SELECT ss.name, ss.stats_id, shr.steps, shr.rows, shr.rows_sampled, 
    shr.modification_counter, shr.last_updated, sh.range_rows, sh.equal_rows
FROM sys.stats ss
INNER JOIN sys.stats_columns sc 
    ON ss.stats_id = sc.stats_id AND ss.object_id = sc.object_id
INNER JOIN sys.all_columns ac 
    ON ac.column_id = sc.column_id AND ac.object_id = sc.object_id
CROSS APPLY sys.dm_db_stats_properties(ss.object_id, ss.stats_id) shr
CROSS APPLY sys.dm_db_stats_histogram(ss.object_id, ss.stats_id) sh
WHERE ss.[object_id] = OBJECT_ID('Country') 
    AND ac.name = 'Country_Name'
    AND sh.range_high_key = CAST('Canada' AS CHAR(8));
```
  
## <a name="see-also"></a>另請參閱  
[DBCC SHOW_STATISTICS (Transact-sql) ](../../t-sql/database-console-commands/dbcc-show-statistics-transact-sql.md)   
[物件相關的動態管理檢視和函數 (Transact-SQL)](../../relational-databases/system-dynamic-management-views/object-related-dynamic-management-views-and-functions-transact-sql.md)  
[sys.dm_db_stats_properties (Transact-SQL)](../../relational-databases/system-dynamic-management-views/sys-dm-db-stats-properties-transact-sql.md)  
