---
description: 'sys.dm_db_column_store_row_group_physical_stats (Transact-sql) '
title: sys.dm_db_column_store_row_group_physical_stats (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 05/05/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.dm_db_column_store_row_group_physical_stats_TSQL
- sys.dm_db_column_store_row_group_physical_stats
- dm_db_column_store_row_group_physical_stats
- dm_db_column_store_row_group_physical_stats_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- dm_db_column_store_row_group_physical_stats
- sys.dm_db_column_store_row_group_physical_stats dynamic management view
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 0c1b5bd85e22c4876004b000c5cc41f9f120d681
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98095178"
---
# <a name="sysdm_db_column_store_row_group_physical_stats-transact-sql"></a>sys.dm_db_column_store_row_group_physical_stats (Transact-sql) 


[!INCLUDE [sqlserver2016-asdb-asdbmi](../../includes/applies-to-version/sqlserver2016-asdb-asdbmi.md)]

提供目前資料庫中所有資料行存放區索引的目前資料列群組層級資訊。  

這會將 [sys.column_store_row_groups &#40;transact-sql&#41;](../../relational-databases/system-catalog-views/sys-column-store-row-groups-transact-sql.md)的目錄檢視擴充。  

|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|object_id|**int**|基礎資料表的識別碼。|  
|**index_id**|**int**|*Object_id* 資料表上此資料行存放區索引的識別碼。|  
|**partition_number**|**int**|保存 *row_group_id* 之資料表分割區的識別碼。 您可以使用 partition_number 將此 DMV 聯結至 sys.partitions。|  
|**row_group_id**|**int**|此資料列群組的識別碼。 針對資料分割資料表，值在資料分割內是唯一的。<br /><br /> -1 代表記憶體中的結尾。|  
|**delta_store_hobt_id**|**bigint**|差異存放區中資料列群組的 hobt_id。<br /><br /> 如果資料列群組不在差異存放區中，則為 Null。<br /><br /> 如果是記憶體中資料表的結尾，則為 Null。|  
|**state**|**tinyint**|*State_description* 相關聯的識別碼號碼。<br /><br /> 0 = INVISIBLE<br /><br /> 1 = OPEN<br /><br /> 2 = CLOSED<br /><br /> 3 = COMPRESSED<br /><br /> 4 = 標記<br /><br /> 「壓縮」是唯一適用于記憶體中資料表的狀態。|  
|**state_desc**|**nvarchar(60)**|資料列群組狀態的描述：<br /><br /> 0-不可見-正在建立的資料列群組。 例如： <br />壓縮資料時，資料行存放區中的資料列群組是不可見的。 壓縮完成時，中繼資料參數會將資料行存放區資料列群組的狀態從隱藏變更為「已壓縮」，並將差異存放區資料列群組的狀態從「已關閉」變更為「標記」。<br /><br /> 1-開啟-正在接受新資料列的差異存放區資料列群組。 開啟的資料列群組仍採用資料列存放區格式，且尚未壓縮為資料行存放區格式。<br /><br /> 2-封閉式-差異存放區中包含資料列數上限的資料列群組，並正在等候元組移動程式將它壓縮到資料行存放區。<br /><br /> 3-壓縮-以資料行存放區壓縮壓縮並儲存在資料行存放區中的資料列群組。<br /><br /> 4-標記-先前在差異存放區中且已不再使用的資料列群組。|  
|**total_rows**|**bigint**|實際儲存在資料列群組中的資料列數目。 適用于壓縮的資料列群組。 包含標示為已刪除的資料列。|  
|**deleted_rows**|**bigint**|實際儲存在標示為要刪除之已壓縮資料列群組的資料列數目。<br /><br /> 0 代表位於差異存放區中的資料列群組。|  
|**size_in_bytes**|**bigint**|此資料列群組中所有頁面的合併大小（以位元組為單位）。 這個大小不包含儲存中繼資料或共用字典所需的大小。|  
|**trim_reason**|**tinyint**|觸發壓縮的資料列群組，使其小於最大資料列數的原因。<br /><br /> 0-UNKNOWN_UPGRADED_FROM_PREVIOUS_VERSION<br /><br /> 1-NO_TRIM<br /><br /> 2-大量載入<br /><br /> 3-REORG<br /><br /> 4-DICTIONARY_SIZE<br /><br /> 5-MEMORY_LIMITATION<br /><br /> 6-RESIDUAL_ROW_GROUP<br /><br /> 7-STATS_MISMATCH<br /><br /> 8-溢出<br /><br /> 9-AUTO_MERGE|  
|**trim_reason_desc**|**nvarchar(60)**|*Trim_reason* 的描述。<br /><br /> 0-UNKNOWN_UPGRADED_FROM_PREVIOUS_VERSION：從舊版升級時發生 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。<br /><br /> 1-NO_TRIM：未修剪資料列群組。 已壓縮資料列群組，最大值為1048576個數據列。  如果在關閉差異資料列群組之後刪除了資料列的子集，資料列數目可能會較少<br /><br /> 2-大量載入：大量載入批次大小限制資料列的數目。<br /><br /> 3-REORG：強制壓縮為 REORG 命令的一部分。<br /><br /> 4-DICTIONARY_SIZE：字典大小的成長太大，無法將所有資料列壓縮在一起。<br /><br /> 5 MEMORY_LIMITATION：沒有足夠的可用記憶體可將所有資料列壓縮在一起。<br /><br /> 6-RESIDUAL_ROW_GROUP：在索引建立作業期間，以資料列 < 1000000 的最後一個資料列群組的一部分關閉<br /><br /> 7-STATS_MISMATCH：僅適用于記憶體中資料表上的資料行存放區。 如果統計資料不正確地指出 >= 1000000 結尾的限定資料列，但我們發現較少，則壓縮的資料列群組將會有 < 1000000 資料列<br /><br /> 8-溢出：僅適用于記憶體中資料表上的資料行存放區。 如果 tail 有 > 1000000 個合格的資料列，則如果計數介於100k 和1000000之間，則會壓縮最後一個批次剩餘的資料列<br /><br /> 9-AUTO_MERGE：在背景中執行的 Tuple Mover 合併作業，會將一或多個資料列群組合並到此資料列群組中。|  
|**transition_to_compressed_state**|TINYINT|顯示此資料列群組如何從差異存放區移至資料行存放區中的壓縮狀態。<br /><br /> 1-NOT_APPLICABLE<br /><br /> 2-INDEX_BUILD<br /><br /> 3-TUPLE_MOVER<br /><br /> 4-REORG_NORMAL<br /><br /> 5-REORG_FORCED<br /><br /> 6-大量載入<br /><br /> 7-合併|  
|**transition_to_compressed_state_desc**|nvarchar(60)| 1-NOT_APPLICABLE-不會將作業套用至差異存放區。 或者，在升級至之前壓縮資料列群組， [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 在這種情況下，不會保留歷程記錄。<br /><br /> 2-INDEX_BUILD-索引建立或索引重建已壓縮資料列群組。<br /><br /> 3-TUPLE_MOVER-在背景中執行的元組移動器會壓縮資料列群組。 當資料列群組變更狀態從 OPEN 變更為已關閉之後，就會發生元組移動。<br /><br /> 4-REORG_NORMAL-重組作業，ALTER INDEX .。。REORG，將已關閉的資料列群組從差異存放區移至資料行存放區。 這發生在元組移動器有時間移動資料列群組之前。<br /><br /> 5-REORG_FORCED-此資料列群組已在差異存放區中開啟，而且已強制進入資料行存放區，然後才會有完整的資料列數目。<br /><br /> 6-大量載入-大量載入作業會直接壓縮資料列群組，而不使用差異存放區。<br /><br /> 7-合併-合併作業將一或多個資料列群組合並到此資料列群組，然後執行資料行存放區壓縮。|  
|**has_vertipaq_optimization**|bit|VertiPaq 優化可透過重新排列資料列群組中的資料列順序，以達到較高的壓縮，來改善資料行存放區壓縮 這項優化會在大部分情況下自動進行。 不使用 VertiPaq 優化的情況有兩種：<br/>  a. 當差異資料列群組移入資料行存放區，且資料行存放區索引上有一或多個非叢集索引時，在此情況下會略過 VertiPaq 優化以將對應索引的變更降至最低;<br/> b. 記憶體優化資料表上的資料行存放區索引。 <br /><br /> 0 = 否<br /><br /> 1 = 是|  
|**生成**|BIGINT|與此資料列群組相關聯的資料列群組產生。|  
|**created_time**|datetime2|建立此資料列群組時的時鐘時間。<br /><br /> Null-用於記憶體中資料表上的資料行存放區索引。|  
|**closed_time**|datetime2|關閉此資料列群組的時鐘時間。<br /><br /> Null-用於記憶體中資料表上的資料行存放區索引。|  
| &nbsp; | &nbsp; | &nbsp; |

## <a name="results"></a>結果  
 針對目前資料庫中的每個資料列群組，各傳回一個資料列。  
  
## <a name="permissions"></a>權限  
需要 `CONTROL` 資料庫之資料表和許可權的許可權 `VIEW DATABASE STATE` 。  
  
## <a name="examples"></a>範例  
  
### <a name="a-calculate-fragmentation-to-decide-when-to-reorganize-or-rebuild-a-columnstore-index"></a>A. 計算片段，以決定何時要重新組織或重建資料行存放區索引。  
 若為數據行存放區索引，已刪除資料列的百分比對於資料列群組中的片段而言是很好的量值。 當片段為20% 或更多時，請移除已刪除的資料列。 如需更多範例，請參閱 [重新組織和重建索引](~/relational-databases/indexes/reorganize-and-rebuild-indexes.md)。  
  
 此範例會聯結 **sys.dm_db_column_store_row_group_physical_stats** 與其他系統資料表，然後將 `Fragmentation` 資料行計算為目前資料庫中每個資料列群組的效率估計。 若要尋找單一資料表的資訊，請移除 **WHERE** 子句前面的批註連字號，並提供資料表名稱。  
  
```sql  
SELECT i.object_id,   
    object_name(i.object_id) AS TableName,   
    i.name AS IndexName,   
    i.index_id,   
    i.type_desc,   
    CSRowGroups.*,  
    100*(ISNULL(deleted_rows,0))/NULLIF(total_rows,0) AS 'Fragmentation'
FROM sys.indexes AS i  
JOIN sys.dm_db_column_store_row_group_physical_stats AS CSRowGroups  
    ON i.object_id = CSRowGroups.object_id AND i.index_id = CSRowGroups.index_id   
-- WHERE object_name(i.object_id) = 'table_name'   
ORDER BY object_name(i.object_id), i.name, row_group_id;  
```  
  
## <a name="see-also"></a>另請參閱  
 [物件目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/object-catalog-views-transact-sql.md)   
 [目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/catalog-views-transact-sql.md)      
 [資料行存放區索引架構](../../relational-databases/sql-server-index-design-guide.md#columnstore_index)         
 [查詢 SQL Server 系統目錄常見問題](../../relational-databases/system-catalog-views/querying-the-sql-server-system-catalog-faq.md)   
 [sys.columns &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-columns-transact-sql.md)   
 [sys.all_columns &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-all-columns-transact-sql.md)   
 [sys.computed_columns &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-computed-columns-transact-sql.md)  
 [sys.column_store_dictionaries &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-column-store-dictionaries-transact-sql.md)   
 [sys.column_store_segments &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-column-store-segments-transact-sql.md)  
  
