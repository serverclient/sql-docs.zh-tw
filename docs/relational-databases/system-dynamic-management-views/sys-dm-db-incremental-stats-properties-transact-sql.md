---
description: sys.dm_db_incremental_stats_properties (Transact-SQL)
title: sys.dm_db_incremental_stats_properties (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 12/18/2017
ms.prod: sql
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.dm_db_incremental_stats_properties
- sys.dm_db_incremental_stats_properties_TSQL
- dm_db_incremental_stats_properties
- dm_db_incremental_stats_properties_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_db_incremental_stats_properties
ms.assetid: aa0db893-34d1-419c-b008-224852e71307
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 27585efd99f537e6b2f6d3082c341533e8bbea3b
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98172090"
---
# <a name="sysdm_db_incremental_stats_properties-transact-sql"></a>sys.dm_db_incremental_stats_properties (Transact-SQL)
[!INCLUDE[sqlserver](../../includes/applies-to-version/sqlserver.md)]

  針對目前 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 資料庫中的指定資料庫物件 (資料表) 傳回累加統計資料的屬性。 `sys.dm_db_incremental_stats_properties` (其中包含資料分割編號) 的使用類似於用於非累加統計資料的 `sys.dm_db_stats_properties` 。 
  
  此函數是在 [!INCLUDE[ssSQL14_md](../../includes/sssql14-md.md)] Service pack 2 和 [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] service pack 1 中引進。
  
## <a name="syntax"></a>語法  
  
```  
sys.dm_db_incremental_stats_properties (object_id, stats_id)  
```  
  
## <a name="arguments"></a>引數  
 object_id  
 這是目前資料庫中，要求其中一個累加統計資料屬性之物件的識別碼。 *object_id* 為 **int**。  
  
 *stats_id*  
 這是指定 *object_id* 之統計資料的識別碼。 您可以從 [sys.stats](../../relational-databases/system-catalog-views/sys-stats-transact-sql.md) 動態管理檢視取得統計資料識別碼。 *stats_id* 是 **int**。  
  
## <a name="table-returned"></a>傳回的資料表  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|object_id|**int**|要傳回統計資料物件屬性之物件 (資料表) 的識別碼。|  
|stats_id|**int**|統計資料物件的識別碼。 在資料表中，這是唯一的。 如需詳細資訊，請參閱 [sys.stats &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-stats-transact-sql.md)。|
|partition_number|**int**|包含資料表其中一部分的磁碟分割的號碼。|  
|last_updated|**datetime2**|上次更新統計資料物件的日期和時間。 如需詳細資訊，請參閱此頁的[備註](#Remarks)一節。|  
|rows|**bigint**|上一次更新統計資料時位於資料表中的資料列總數。 如果篩選了統計資料或是統計資料對應至篩選過的索引，此資料列數可能會少於資料表中的資料列數。|  
|rows_sampled|**bigint**|針對統計資料計算進行取樣的資料列總數。|  
|steps|**int**|長條圖中的步驟數。 如需詳細資訊，請參閱 [DBCC SHOW_STATISTICS &#40;Transact-SQL&#41;](../../t-sql/database-console-commands/dbcc-show-statistics-transact-sql.md)。|  
|unfiltered_rows|**bigint**|套用篩選運算式 (針對篩選的統計資料) 之前，資料表中的資料列總數。 如果統計資料未經過篩選，unfiltered_row 就會等於 rows 資料行中傳回的值。|  
|modification_counter|**bigint**|自從上次更新統計資料以來，前端統計資料資料行 (用以建置長條圖的資料行) 的總修改次數。<br /><br /> 此資料行沒有包含記憶體最佳化資料表的資訊。|  
  
## <a name="remarks"></a><a name="Remarks"></a> 備註  
 `sys.dm_db_incremental_stats_properties` 在下列任何情況下，都會傳回空的資料列集：  
  
-   `object_id` 或 `stats_id` 是 NULL。   
-   找不到指定的物件，或者該物件沒有對應至具有累加統計資料的資料表。  
-   指定的統計資料識別碼沒有對應至指定之物件識別碼的現有統計資料。  
-   目前的使用者沒有檢視統計資料物件的權限。
 
 此行為可讓您在交叉套用至例如 `sys.dm_db_incremental_stats_properties` 和 `sys.objects` 等檢視中的資料列時，安全地使用 `sys.stats`。 這個方法可以傳回對應每個資料分割的統計資料屬性。 若要查看跨所有資料分割合併的合併統計資料屬性，請改用 sys.dm_db_stats_properties。 

統計資料更新日期儲存在[統計資料 Blob 物件](../../relational-databases/statistics/statistics.md#DefinitionQOStatistics)中，其中還有[長條圖](../../relational-databases/statistics/statistics.md#histogram)和[密度向量](../../relational-databases/statistics/statistics.md#density)，不是儲存在中繼資料中。 如果沒有讀取資料以產生統計資料，則不會建立統計資料 blob、無法使用日期，而且 *last_updated* 資料行是 Null。 這是已篩選統計資料的情況，其中述詞未傳回任何資料列，或為新的空白資料表的情況。

## <a name="permissions"></a>權限  
 要求使用者對於統計資料資料行擁有選取權限，或是使用者擁有資料表，或使用者是 `sysadmin` 固定伺服器角色、`db_owner` 固定資料庫角色或 `db_ddladmin` 固定資料庫角色的成員。  
  
## <a name="examples"></a>範例  

### <a name="a-simple-example"></a>A. 簡單範例
下列範例會傳回在 `PartitionTable` 建立資料分割資料表及索引 [主題中所述](../../relational-databases/partitions/create-partitioned-tables-and-indexes.md)資料表的統計資料。

```sql
SELECT * FROM sys.dm_db_incremental_stats_properties (object_id('PartitionTable'), 1);
``` 

如需其他使用建議，請參閱  [sys.dm_db_stats_properties](../../relational-databases/system-dynamic-management-views/sys-dm-db-stats-properties-transact-sql.md)。
  
## <a name="see-also"></a>另請參閱  
 [DBCC SHOW_STATISTICS &#40;Transact-SQL&#41;](../../t-sql/database-console-commands/dbcc-show-statistics-transact-sql.md)   
 [sys.stats &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-stats-transact-sql.md)   
 [物件相關的動態管理檢視和函數 &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/object-related-dynamic-management-views-and-functions-transact-sql.md)   
 [動態管理檢視與函數 &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)  
 [sys.dm_db_stats_properties](../../relational-databases/system-dynamic-management-views/sys-dm-db-stats-properties-transact-sql.md)   
 [sys.dm_db_stats_histogram (Transact-SQL)](../../relational-databases/system-dynamic-management-views/sys-dm-db-stats-histogram-transact-sql.md) 
