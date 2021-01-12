---
description: sys.column_store_segments (Transact-SQL)
title: sys.column_store_segments (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 09/24/2020
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- column_store_segments
- sys.column_store_segments_TSQL
- sys.column_store_segments
- column_store_segments_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.column_store_segments catalog view
ms.assetid: 1253448c-2ec9-4900-ae9f-461d6b51b2ea
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 189a5cca5cfac0cce6437ccc256d461140f3c40b
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98095600"
---
# <a name="syscolumn_store_segments-transact-sql"></a>sys.column_store_segments (Transact-SQL)
[!INCLUDE[sqlserver](../../includes/applies-to-version/sqlserver.md)]

針對資料行存放區索引中的每個資料行區段，各傳回一個資料列。 每個資料列群組的每個資料行都有一個資料行區段。 例如，含有10個數據列群組和34資料行的資料表會傳回340個數據列。 
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**partition_id**|**bigint**|指出資料分割識別碼。 在資料庫中，這是唯一的。|  
|**hobt_id**|**bigint**|具有此資料行存放區索引之資料表的堆積或 B 型樹狀結構索引 (HoBT) 的識別碼。|  
|**column_id**|**int**|資料行存放區資料行的識別碼。|  
|**segment_id**|**int**|資料列群組的識別碼。 為了回溯相容性，即使這是資料列群組識別碼，還是會繼續呼叫 segment_id 資料行名稱。 您可以使用 <segment_id> 來唯一識別區段 \<hobt_id, partition_id, column_id> 。|  
|**version**|**int**|資料行區段格式的版本。|  
|**encoding_type**|**int**|用於該區段的編碼類型：<br /><br /> 1 = VALUE_BASED-不含字典的非字串/二進位 (類似于4，但有部分內部變化) <br /><br /> 2 = VALUE_HASH_BASED-字典中具有一般值的非字串/二進位資料行<br /><br /> 3 = STRING_HASH_BASED-具有字典中通用值的字串/二進位資料行<br /><br /> 4 = STORE_BY_VALUE_BASED-不含字典的非字串/二進位檔<br /><br /> 5 = STRING_STORE_BY_VALUE_BASED-不含字典的字串/二進位檔<br /><br /> 如需詳細資訊，請參閱「 [備註](#remarks) 」一節。|  
|**row_count**|**int**|資料列群組中的列數。|  
|**has_nulls**|**int**|如果資料行區段具有 Null 值，則為 1。|  
|**base_id**|**bigint**|如果正在使用編碼類型1，則為基底值識別碼。 如果未使用編碼類型1，base_id 會設定為-1。|  
|**magnitude**|**float**|使用編碼類型1時的大小。 如果未使用編碼類型1，則量值會設定為-1。|  
|**primary_dictionary_id**|**int**|值為0表示全域字典。 -1 的值表示沒有針對此資料行建立的全域字典。|  
|**secondary_dictionary_id**|**int**|非零值指向目前區段中這個資料行的本機字典 (亦即，資料列群組) 。 值為-1 表示此區段沒有本機字典。|  
|**min_data_id**|**bigint**|資料行區段中的最小資料識別碼。|  
|**max_data_id**|**bigint**|資料行區段中的最大資料識別碼。|  
|**null_value**|**bigint**|用來表示 Null 的值。|  
|**on_disk_size**|**bigint**|區段大小 (以位元組為單位)。|  
  
## <a name="remarks"></a>備註  
的資料行存放區區段編碼類型是由所選取 [!INCLUDE[ssde_md](../../includes/ssde_md.md)] ，目標是藉由分析區段資料來達到最低的儲存成本。 如果資料大多不同，則會 [!INCLUDE[ssde_md](../../includes/ssde_md.md)] 使用以值為基礎的編碼方式。 如果資料大多不相異，則會 [!INCLUDE[ssde_md](../../includes/ssde_md.md)] 使用雜湊式編碼。 以字串為基礎和以值為基礎的編碼之間的選擇，與儲存的資料類型（無論是字串資料或二進位資料）有關。 所有編碼會盡可能利用位封裝和執行長度的編碼。
 
## <a name="permissions"></a>權限  
 所有資料行都至少需要 `VIEW DEFINITION` 資料表的許可權。 除非使用者也有許可權，否則下列資料行會傳回 null `SELECT` ： `has_nulls` 、 `base_id` 、 `magnitude` 、 `min_data_id` 、 `max_data_id` 和 `null_value` 。  
  
 [!INCLUDE[ssCatViewPerm](../../includes/sscatviewperm-md.md)] 如需相關資訊，請參閱 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)。  

## <a name="examples"></a>範例

### <a name="the-following-query-returns-information-about-segments-of-a-columnstore-index"></a>下列查詢會傳回資料行存放區索引之區段的相關資訊。  
  
```sql  
SELECT i.name, p.object_id, p.index_id, i.type_desc,   
    COUNT(*) AS number_of_segments  
FROM sys.column_store_segments AS s   
INNER JOIN sys.partitions AS p   
    ON s.hobt_id = p.hobt_id   
INNER JOIN sys.indexes AS i   
    ON p.object_id = i.object_id  
WHERE i.type = 5 OR i.type = 6  
GROUP BY i.name, p.object_id, p.index_id, i.type_desc ;  
GO  
```  

## <a name="see-also"></a>另請參閱  
 [物件目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/object-catalog-views-transact-sql.md)   
 [目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/catalog-views-transact-sql.md)   
 [查詢 SQL Server 系統目錄常見問題](../../relational-databases/system-catalog-views/querying-the-sql-server-system-catalog-faq.md)   
 [sys.columns &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-columns-transact-sql.md)   
 [sys.all_columns &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-all-columns-transact-sql.md)   
 [sys.computed_columns &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-computed-columns-transact-sql.md)   
 [資料行存放區索引指南](~/relational-databases/indexes/columnstore-indexes-overview.md)    
 [sys.column_store_dictionaries &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-column-store-dictionaries-transact-sql.md)  
  
 
