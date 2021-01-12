---
description: sys.dm_db_xtp_hash_index_stats (Transact-SQL)
title: sys.dm_db_xtp_hash_index_stats (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 08/29/2016
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.dm_db_xtp_hash_index_stats
- sys.dm_db_xtp_hash_index_stats_TSQL
- dm_db_xtp_hash_index_stats
- dm_db_xtp_hash_index_stats_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_db_xtp_hash_index_stats (dynamic management view)
ms.assetid: 45969884-cd61-48e8-aee5-c725c78e3e4c
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: =azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 239ca2e5a60f8ffeeafd807316e5a903dc840754
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98092869"
---
# <a name="sysdm_db_xtp_hash_index_stats-transact-sql"></a>sys.dm_db_xtp_hash_index_stats (Transact-SQL)
[!INCLUDE[sql-asdb-asdbmi](../../includes/applies-to-version/sql-asdb-asdbmi.md)]

  這些統計資料有助於了解及調整值區計數。 另外還可用於偵測索引鍵擁有太多重複項目的情況。  
  
 平均鏈結長度較大時，表示有許多資料列經過雜湊處理進入相同貯體內。 發生這種情況可能是因為：  
  
-   如果空貯體的數目較低或平均和最大鏈結長度類似，總值區計數就可能過低。 這樣會導致將許多不同的索引鍵雜湊處理至相同貯體。  
  
-   如果空貯體的數目較高或最大鏈結長度相對於平均鏈結長度而言較高，則可能有許多資料列包含重複的索引鍵值，或是索引鍵值扭曲。 所有具有相同索引鍵值的資料列都會雜湊處理為相同貯體，因此該貯體會擁有很長的鏈結長度。  
  
較長的鏈結長度可能對個別資料列上所有 DML 作業的效能造成相當大的影響，包括 SELECT 和 INSERT。 鏈結長度較短且空的值區計數較高時，表示 bucket_count 太高。 這樣會降低索引掃描的效能。  
  
> [!WARNING]
> **sys.dm_db_xtp_hash_index_stats** 會掃描整個資料表。 因此，如果您的資料庫中有大型資料表， **sys.dm_db_xtp_hash_index_stats** 可能會花很長的時間執行。  
  
如需詳細資訊，請參閱 [Memory-Optimized 資料表的雜湊索引](../../relational-databases/sql-server-index-design-guide.md#hash_index)。  
  
|資料行名稱|類型|描述|  
|-----------------|----------|-----------------|  
|object_id|**int**|父資料表的物件識別碼。|  
|xtp_object_id|**bigint**|記憶體優化資料表的識別碼。|  
|index_id|**int**|索引識別碼。|  
|total_bucket_count|**bigint**|索引中雜湊值區的總數。|  
|empty_bucket_count|**bigint**|索引中空雜湊值區的數目。|  
|avg_chain_length|**bigint**|索引中所有雜湊值區的平均資料列鏈結長度。|  
|max_chain_length|**bigint**|雜湊值區中資料列鏈結的最大長度。|  
|xtp_object_id|**bigint**|對應于記憶體優化資料表的記憶體內部 OLTP 物件識別碼。|  
  
## <a name="permissions"></a>權限  
 需要伺服器的 VIEW DATABASE STATE 權限。  

## <a name="examples"></a>範例  
  
### <a name="a-troubleshooting-hash-index-bucket-count"></a>A. 疑難排解雜湊索引值區計數

下列查詢可以用來針對現有資料表的雜湊索引值區計數進行疑難排解。 此查詢會傳回有關使用者資料表上所有雜湊索引的空值區和鏈長度百分比的統計資料。

```sql
  SELECT  
    QUOTENAME(SCHEMA_NAME(t.schema_id)) + N'.' + QUOTENAME(OBJECT_NAME(h.object_id)) as [table],   
    i.name                   as [index],   
    h.total_bucket_count,  
    h.empty_bucket_count,  
    FLOOR((  
      CAST(h.empty_bucket_count as float) /  
        h.total_bucket_count) * 100)  
                             as [empty_bucket_percent],  
    h.avg_chain_length,   
    h.max_chain_length  
  FROM sys.dm_db_xtp_hash_index_stats as h   
  INNER JOIN sys.indexes as i  
            ON h.object_id = i.object_id  
           AND h.index_id  = i.index_id  
    INNER JOIN sys.memory_optimized_tables_internal_attributes ia ON h.xtp_object_id=ia.xtp_object_id
    INNER JOIN sys.tables t on h.object_id=t.object_id
  WHERE ia.type=1
  ORDER BY [table], [index];  
``` 

如需有關如何解讀此查詢結果的詳細資訊，請參閱 [針對 Memory-Optimized 資料表的雜湊索引進行疑難排解](../../relational-databases/in-memory-oltp/hash-indexes-for-memory-optimized-tables.md) 。  

### <a name="b-hash-index-statistics-for-internal-tables"></a>B. 內部資料表的雜湊索引統計資料

某些功能會使用內部資料表來利用雜湊索引，例如記憶體優化資料表上的資料行存放區索引。 下列查詢會針對連結至使用者資料表的內部資料表傳回雜湊索引的統計資料。

```sql
  SELECT  
    QUOTENAME(SCHEMA_NAME(t.schema_id)) + N'.' + QUOTENAME(OBJECT_NAME(h.object_id)) as [user_table],
    ia.type_desc as [internal_table_type],
    i.name                   as [index],   
    h.total_bucket_count,  
    h.empty_bucket_count,  
    h.avg_chain_length,   
    h.max_chain_length  
  FROM sys.dm_db_xtp_hash_index_stats as h   
  INNER JOIN sys.indexes as i  
            ON h.object_id = i.object_id  
           AND h.index_id  = i.index_id  
    INNER JOIN sys.memory_optimized_tables_internal_attributes ia ON h.xtp_object_id=ia.xtp_object_id
    INNER JOIN sys.tables t on h.object_id=t.object_id
  WHERE ia.type!=1
  ORDER BY [user_table], [internal_table_type], [index]; 
```

請注意，內部資料表上的索引 BUCKET_COUNT 無法變更，因此此查詢的輸出應該只被視為僅供參考。 您不需要執行任何動作。  

除非您使用的功能會利用內部資料表的雜湊索引，否則此查詢不應傳回任何資料列。 下列記憶體優化資料表包含資料行存放區索引。 建立此資料表之後，您會看到內部資料表的雜湊索引。

```sql
  CREATE TABLE dbo.table_columnstore
  (
    c1 INT NOT NULL PRIMARY KEY NONCLUSTERED,
    INDEX ix_columnstore CLUSTERED COLUMNSTORE
  ) WITH (MEMORY_OPTIMIZED=ON)
```

## <a name="see-also"></a>另請參閱  
 [記憶體最佳化的資料表動態管理檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/memory-optimized-table-dynamic-management-views-transact-sql.md)  
  
  
