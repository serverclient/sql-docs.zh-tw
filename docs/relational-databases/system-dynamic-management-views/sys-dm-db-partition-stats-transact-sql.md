---
description: sys.dm_db_partition_stats (Transact-SQL)
title: sys.dm_db_partition_stats (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 05/28/2020
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- dm_db_partition_stats
- dm_db_partition_stats_TSQL
- sys.dm_db_partition_stats_TSQL
- sys.dm_db_partition_stats
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_db_partition_stats dynamic management view
ms.assetid: 9db9d184-b3a2-421e-a804-b18ebcb099b7
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: fe7e6639e8f0b04e8a3482ce56baf732a9113c6d
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98095148"
---
# <a name="sysdm_db_partition_stats-transact-sql"></a>sys.dm_db_partition_stats (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  針對目前資料庫中的每個資料分割，各傳回其頁面和資料列計數資訊。  
  
> [!NOTE]  
> 若要從或呼叫這個 [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] ，請使用 **sys.dm_pdw_nodes_db_partition_stats** 名稱。 Sys.dm_pdw_nodes_db_partition_stats 中的 partition_id 與 Azure Synapse Analytics 之 sys. 分割目錄檢視中的 partition_id 不同。
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**partition_id**|**bigint**|資料分割的識別碼。 在資料庫中，這是唯一的。 這與 **sys.** 分割目錄檢視中的 **partition_id** 值相同，但 Azure Synapse Analytics 除外。|  
|object_id|**int**|資料分割所屬資料表或索引檢視的物件識別碼。|  
|**index_id**|**int**|資料分割所屬之堆積或索引的識別碼。<br /><br /> 0 = 堆積<br /><br /> 1 = 叢集索引。<br /><br /> > 1 = 非叢集索引|  
|**partition_number**|**int**|在索引或堆積內，以 1 為基底的資料分割編號。|  
|**in_row_data_page_count**|**bigint**|這個資料分割中用來儲存同資料列資料的頁數。 如果資料分割屬於堆積，這個值是堆積中的資料頁數。 如果資料分割屬於索引，這個值是分葉層級中的頁數。 B 型樹狀目錄中 (非分葉頁面不包含在計數中。 ) IAM (索引配置對應) 頁面不會包含在任何一種情況中。 xVelocity 記憶體最佳化的資料行存放區索引之 Always 0。|  
|**in_row_used_page_count**|**bigint**|這個資料分割中用來儲存和管理同資料列資料的總頁數。 這個計數包括非分葉 B 型樹狀目錄頁數、IAM 頁數，以及 **in_row_data_page_count** 資料行中包括的所有頁數。 永遠是 0，表示資料行存放區索引。|  
|**in_row_reserved_page_count**|**bigint**|這個資料分割中為儲存和管理同資料列資料所保留的總頁數，不管這些頁面是否正在使用中。 永遠是 0，表示資料行存放區索引。|  
|**lob_used_page_count**|**bigint**|資料分割中用來儲存和管理 out-of-row **text**、**ntext**、**image**、**varchar(max)**、**nvarchar(max)**、**varbinary(max)** 和 **xml** 資料行的頁數。 IAM 頁數包括在內。<br /><br /> 資料分割中用來儲存和管理資料行存放區索引的 LOB 總數。|  
|**lob_reserved_page_count**|**bigint**|資料分割中為儲存和管理 out-of-row **text**、**ntext**、**image**、**varchar(max)**、**nvarchar(max)**、**varbinary(max)** 和 **xml** 資料行所保留的頁數，不管這些頁面是否正在使用中。 IAM 頁數包括在內。<br /><br /> 保留在資料分割中儲存和管理資料行存放區索引的 LOB 總數。|  
|**row_overflow_used_page_count**|**bigint**|資料分割中用來儲存管理資料列溢位 **varchar**、**nvarchar**、**varbinary** 和 **sql_variant** 資料行的頁數。 IAM 頁數包括在內。<br /><br /> 永遠是 0，表示資料行存放區索引。|  
|**row_overflow_reserved_page_count**|**bigint**|資料分割中為儲存管理資料列溢位 **varchar**、**nvarchar**、**varbinary** 和 **sql_variant** 資料行所保留的頁數，不管這些頁面是否正在使用中。 IAM 頁數包括在內。<br /><br /> 永遠是 0，表示資料行存放區索引。|  
|**used_page_count**|**bigint**|資料分割的總使用頁數。 計算為 **in_row_used_page_count**  +  **lob_used_page_count**  +  **row_overflow_used_page_count**。|  
|**reserved_page_count**|**bigint**|資料分割的總保留頁數。 計算為 **in_row_reserved_page_count**  +  **lob_reserved_page_count**  +  **row_overflow_reserved_page_count**。|  
|**row_count**|**bigint**|此資料分割中大約的資料列數目。|  
|**pdw_node_id**|**int**|**適用** 于： [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 、 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]<br /><br /> 此散發所在之節點的識別碼。|  
|**distribution_id**|**int**|**適用** 于： [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 、 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]<br /><br /> 與散發相關聯的唯一數值識別碼。|  
  
## <a name="remarks"></a>備註  
 **sys.dm_db_partition_stats** 顯示資料庫中所有資料分割用來儲存和管理同資料列資料、LOB 資料和資料列溢位資料的空間相關資訊。 每個資料分割顯示一個資料列。  
  
 在各個不同的系統資料表中，輸出所依據的計數快取於記憶體中或儲存在磁碟中。  
  
 同資料列資料、LOB 資料和資料列溢位資料代表組成資料分割的三個配置單位。 您可以查詢 [sys.allocation_units](../../relational-databases/system-catalog-views/sys-allocation-units-transact-sql.md) 目錄檢視有關資料庫中各配置單位的中繼資料。  
  
 如果堆積或索引不分割，則是由一個資料分割組成 (資料分割數目 = 1)；因此，只會針對該堆積或索引傳回一個資料列。 您可以查詢 [sys.partitions](../../relational-databases/system-catalog-views/sys-partitions-transact-sql.md) 目錄檢視有關資料庫中所有資料表和索引之各資料分割的中繼資料。  
  
 個別資料表或索引的總計數可以藉由加入所有相關資料分割的計數來取得。  
  
## <a name="permissions"></a>權限  
 需要 `VIEW DATABASE STATE` 和 `VIEW DEFINITION` 許可權，才能查詢 **sys.dm_db_partition_stats** 動態管理檢視。 如需有關動態管理檢視之許可權的詳細資訊，請參閱 [&#40;transact-sql&#41;的動態管理檢視和函數 ](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)。  
  
## <a name="examples"></a>範例  
  
### <a name="a-returning-all-counts-for-all-partitions-of-all-indexes-and-heaps-in-a-database"></a>A. 傳回資料庫中所有索引和堆積之所有資料分割的所有計數  
 下列範例會顯示 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 資料庫中所有索引和堆積之所有資料分割的所有計數。  
  
```sql  
USE AdventureWorks2012;  
GO  
SELECT * FROM sys.dm_db_partition_stats;  
GO  
```  
  
### <a name="b-returning-all-counts-for-all-partitions-of-a-table-and-its-indexes"></a>B. 傳回資料表與其索引之所有資料分割的所有計數  
 下列範例會顯示 `HumanResources.Employee` 資料表與其索引之所有資料分割的所有計數。  
  
```sql  
USE AdventureWorks2012;  
GO  
SELECT * FROM sys.dm_db_partition_stats   
WHERE object_id = OBJECT_ID('HumanResources.Employee');  
GO  
```  
  
### <a name="c-returning-total-used-pages-and-total-number-of-rows-for-a-heap-or-clustered-index"></a>C. 傳回堆積或叢集索引的總使用頁數和資料列總數  
 下列範例會傳回 `HumanResources.Employee` 資料表之堆積或叢集索引的總使用頁數和資料列總數。 因為依預設不會分割 `Employee` 資料表，請注意總和只包括一個資料分割。  
  
```sql  
USE AdventureWorks2012;  
GO  
SELECT SUM(used_page_count) AS total_number_of_used_pages,   
    SUM (row_count) AS total_number_of_rows   
FROM sys.dm_db_partition_stats  
WHERE object_id=OBJECT_ID('HumanResources.Employee')    AND (index_id=0 or index_id=1);  
GO  
```  
  
## <a name="see-also"></a>另請參閱  
 [動態管理檢視與函數 &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)   
 [資料庫相關的動態管理檢視 &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/database-related-dynamic-management-views-transact-sql.md)  
  
  


