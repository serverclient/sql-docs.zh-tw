---
description: sys.column_store_row_groups (Transact-SQL)
title: sys.column_store_row_groups (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 10/28/2020
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.column_store_row_groups_TSQL
- column_store_row_groups
- sys.column_store_row_groups
- column_store_row_groups_TSQL
- deleted bitmap
dev_langs:
- TSQL
helpviewer_keywords:
- sys.column_store_row_groups catalog view
ms.assetid: 76e7fef2-d1a4-4272-a2bb-5f5dcd84aedc
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: bb30e3c829dcdabde66fd1a330ec617eea7e5b34
ms.sourcegitcommit: 7791bd2ba339edc5cd2078a6537c8f6bfe72a19b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/19/2021
ms.locfileid: "98564466"
---
# <a name="syscolumn_store_row_groups-transact-sql"></a>sys.column_store_row_groups (Transact-SQL)
[!INCLUDE[sqlserver](../../includes/applies-to-version/sqlserver.md)]

  提供以每個區段為基礎的叢集資料行存放區索引資訊，協助系統管理員做出系統管理決策。 **sys.column_store_row_groups** 具有實際儲存的資料列總數的資料行 (包括標示為已刪除) 的資料行，以及標示為已刪除的資料列數目的資料行。 使用 **sys.column_store_row_groups** 來判斷哪些資料列群組的已刪除資料列的百分比很高，應該重建。  
   
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|object_id|int|定義此索引所在資料表的識別碼。|  
|**index_id**|int|內含此資料行存放區索引之資料表的索引識別碼。|  
|**partition_number**|int|保存資料列群組 row_group_id 之資料表分割區的識別碼。 您可以使用 partition_number 將此 DMV 聯結至 sys.partitions。|  
|**row_group_id**|int|與此資料列群組相關聯的資料列群組號碼。 此號碼在分割區中是唯一的。<br /><br /> -1 = 記憶體中資料表的結尾。|  
|**delta_store_hobt_id**|BIGINT|差異存放區中的 OPEN row 群組 hobt_id。<br /><br /> 如果資料列群組不在差異存放區中，則為 Null。<br /><br /> 如果是記憶體中資料表的結尾，則為 Null。|  
|**state**|TINYINT|與 state_description 相關聯的識別碼。<br /><br /> 0 = INVISIBLE<br /><br /> 1 = OPEN<br /><br /> 2 = CLOSED<br /><br /> 3 = COMPRESSED <br /><br /> 4 = 標記|  
|**state_description**|nvarchar(60)|資料列群組的持續狀態描述：<br /><br /> 隱藏-從差異存放區中的資料建立的隱藏壓縮區段。 讀取動作將會使用差異存放區，直到不可見的壓縮區段完成為止。 然後新的區段會變成可見，並移除來源差異存放區。<br /><br /> 開啟-正在接受新記錄的讀取/寫入資料列群組。 開啟的資料列群組仍採用資料列存放區格式，且尚未壓縮為資料行存放區格式。<br /><br /> 已關閉-已填滿但尚未由元組移動器進程壓縮的資料列群組。<br /><br /> 已壓縮-已填滿和壓縮的資料列群組。|  
|**total_rows**|BIGINT|實際儲存在資料列群組中的總列數。 有些可能已刪除，但是仍然保存。 資料列群組中資料列數目的上限為 1,048,576 (十六進位 FFFFF)。|  
|**deleted_rows**|BIGINT|資料列群組中標示為已刪除的總列數。 DELTA 資料列群組的此值永遠為 0。|  
|**size_in_bytes**|BIGINT|DELTA 和 COLUMNSTORE 資料列群組的此資料列群組中所有資料的大小 (以位元組為單位，但不包括中繼資料或共用字典)。|  
  
## <a name="remarks"></a>備註  
 針對擁有叢集或非叢集資料行存放區索引的每一個資料表的每一個資料行存放區資料列群組傳回一個資料列。  
  
 使用 **sys.column_store_row_groups** 來判斷資料列群組中包含的資料列數目，以及資料列群組的大小。  
  
 當資料列群組中已刪除的資料列數增加到佔總列數的相當大比例時，資料表的效率就會降低。 重建資料行存放區索引可縮小資料表，減少讀取資料表所需的磁碟 I/O。 若要重建資料行存放區索引，請使用 **ALTER index** 語句的 **rebuild** 選項。  
  
 可更新的資料行存放區首先會將新的資料插入至 **開啟** 的資料列群組，這是 rowstore 格式，有時也稱為 delta 資料表。  一旦開啟的資料列群組已滿，其狀態會變更為 [ **已關閉**]。 已關閉的資料列群組會由元組移動器壓縮成資料行存放區格式，而狀態會變更為 **壓縮**。  Tuple Mover 是背景處理序，會定期喚醒並檢查是否有任何準備好壓縮為資料行存放區資料列群組的關閉資料列群組。  Tuple Mover 也會取消配置其中所有資料列都已刪除的任何資料列群組。 已解除 **分配的資料列群組** 會標示為標記。 若要立即執行元組移動器，請使用 **ALTER INDEX** 語句的重新 **組織** 選項。  
  
 當資料行存放區資料列群組已滿時，就會進行壓縮，並停止接受新的資料列。 資料列從壓縮的群組中刪除時仍會持續存在，但會標示為已刪除。 對壓縮的群組進行更新的實作方式，是從壓縮的群組中刪除，然後插入開啟的群組。  
  
## <a name="permissions"></a>權限  
 如果使用者具有資料表的許可權，則傳回資料表的資訊 `VIEW DEFINITION` 。  
  
 [!INCLUDE[ssCatViewPerm](../../includes/sscatviewperm-md.md)] 如需相關資訊，請參閱 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)。  
  
## <a name="examples"></a>範例  
 下列範例會將 **sys.column_store_row_groups** 資料表聯結至其他系統資料表，以傳回特定資料表的相關資訊。 導出的 `PercentFull` 資料行是資料列群組的效率預估。 若要尋找單一資料表上的資訊，請移除 **WHERE** 子句前面的批註連字號，並提供資料表名稱。  
  
```sql  
SELECT i.object_id, object_name(i.object_id) AS TableName,   
i.name AS IndexName, i.index_id, i.type_desc,   
CSRowGroups.*,   
100*(total_rows - ISNULL(deleted_rows,0))/total_rows AS PercentFull    
FROM sys.indexes AS i  
JOIN sys.column_store_row_groups AS CSRowGroups  
    ON i.object_id = CSRowGroups.object_id  
AND i.index_id = CSRowGroups.index_id   
--WHERE object_name(i.object_id) = '<table_name>'   
ORDER BY object_name(i.object_id), i.name, row_group_id;  
```  
  
## <a name="see-also"></a>請參閱  
 [物件目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/object-catalog-views-transact-sql.md)   
 [目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/catalog-views-transact-sql.md)   
 [查詢 SQL Server 系統目錄常見問題](../../relational-databases/system-catalog-views/querying-the-sql-server-system-catalog-faq.md)   
 [sys.columns &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-columns-transact-sql.md)   
 [sys.all_columns &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-all-columns-transact-sql.md)   
 [sys.computed_columns &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-computed-columns-transact-sql.md)   
 [資料行存放區索引指南](~/relational-databases/indexes/columnstore-indexes-overview.md)     
 [sys.column_store_dictionaries &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-column-store-dictionaries-transact-sql.md)   
 [sys.column_store_segments &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-column-store-segments-transact-sql.md)  
  
  

