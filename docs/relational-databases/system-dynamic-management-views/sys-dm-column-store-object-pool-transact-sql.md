---
description: 'sys.dm_column_store_object_pool (Transact-sql) '
title: sys.dm_column_store_object_pool (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/17/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
dev_langs:
- TSQL
ms.assetid: a8a58ca7-0a7d-4786-bfd9-e8894bd345dd
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 9045d0b9ecda2e20bf9013d0c875adb09eeae67c
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98095235"
---
# <a name="sysdm_column_store_object_pool-transact-sql"></a>sys.dm_column_store_object_pool (Transact-sql) 

[!INCLUDE [sqlserver2016-asdb-asdbmi](../../includes/applies-to-version/sqlserver2016-asdb-asdbmi.md)]

 傳回資料行存放區索引物件的不同物件記憶體集區使用類型計數。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**database_id**|int|資料庫的識別碼。 這在 SQL Server 資料庫或 Azure SQL database 伺服器的實例內是唯一的。 |  
|object_id|int|物件的識別碼。 物件是其中一個 object_types。 | 
|**index_id**|int|資料行存放區索引的識別碼。|  
|**partition_number**|BIGINT|在索引或堆積內，以 1 為基底的資料分割編號。 每個資料表或視圖至少都有一個資料分割。| 
|**column_id**|int|資料行存放區資料行的識別碼。 若為 DELETE_BITMAP，則為 Null。| 
|**row_group_id**|int|資料列群組的識別碼。|
|**object_type**|SMALLINT|1 = COLUMN_SEGMENT<br /><br /> 2 = COLUMN_SEGMENT_PRIMARY_DICTIONARY<br /><br /> 3 = COLUMN_SEGMENT_SECONDARY_DICTIONARY<br /><br /> 4 = COLUMN_SEGMENT_BULKINSERT_DICTIONARY<br /><br /> 5 = COLUMN_SEGMENT_DELETE_BITMAP|  
|**object_type_desc**|nvarchar(60)|COLUMN_SEGMENT-資料行區段。 `object_id` 這是區段識別碼。 區段會將一個資料行的所有值儲存在一個資料列群組中。 例如，如果資料表有10個數據行，每個資料列群組都有10個數據行區段。 <br /><br /> COLUMN_SEGMENT_PRIMARY_DICTIONARY-全域字典，其中包含資料表中所有資料行區段的查閱資訊。<br /><br /> COLUMN_SEGMENT_SECONDARY_DICTIONARY-與某個資料行相關聯的本機字典。<br /><br /> COLUMN_SEGMENT_BULKINSERT_DICTIONARY-全域字典的另一個標記法。 這會提供值的反向查閱以 dictionary_id。 用來在 Tuple Mover 或大量載入的過程中建立壓縮的區段。<br /><br /> COLUMN_SEGMENT_DELETE_BITMAP-追蹤區段刪除的點陣圖。 每個分割區都有一個刪除點陣圖。|  
|**access_count**|int|此物件的讀取或寫入存取次數。|  
|**memory_used_in_bytes**|BIGINT|物件集區中此物件所使用的記憶體。|  
|**object_load_time**|Datetime|當 object_id 進入物件集區時的時鐘時間。|  
  
## <a name="permissions"></a>權限  

在上 [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] ，需要 `VIEW SERVER STATE` 許可權。   
在 SQL Database Basic、S0 和 S1 服務目標上，以及針對彈性集區中的資料庫，則 `Server admin` `Azure Active Directory admin` 需要或帳戶。 在所有其他 SQL Database 服務目標上， `VIEW DATABASE STATE` 資料庫中都需要有許可權。   
 
## <a name="see-also"></a>另請參閱  
  
 [索引相關的動態管理檢視和函式 &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/index-related-dynamic-management-views-and-functions-transact-sql.md)   
 [sys.dm_db_index_physical_stats &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-index-physical-stats-transact-sql.md)   
 [sys.dm_db_index_operational_stats &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-index-operational-stats-transact-sql.md)   
 [sys.indexes &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-indexes-transact-sql.md)   
 [sys.objects &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-objects-transact-sql.md)   
 [效能的監視與微調](../../relational-databases/performance/monitor-and-tune-for-performance.md)  
 [資料行存放區索引：概觀](../../relational-databases/indexes/columnstore-indexes-overview.md) 
  
