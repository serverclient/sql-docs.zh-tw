---
description: sys.dm_db_xtp_nonclustered_index_stats (Transact-SQL)
title: sys.dm_db_xtp_nonclustered_index_stats (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 08/29/2016
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- dm_db_xtp_nonclustered_index_stats_TSQL
- dm_db_xtp_nonclustered_index_stats
- sys.dm_db_xtp_nonclustered_index_stats_TSQL
- sys.dm_db_xtp_nonclustered_index_stats
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_db_xtp_nonclustered_index_stats
ms.assetid: d55ba31c-296c-419b-9c4b-c126e0a3d156
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: =azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 1c093479ffad2ea852d9af0806f0e78ac907773a
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98099871"
---
# <a name="sysdm_db_xtp_nonclustered_index_stats-transact-sql"></a>sys.dm_db_xtp_nonclustered_index_stats (Transact-SQL)
[!INCLUDE[sql-asdb-asdbmi](../../includes/applies-to-version/sql-asdb-asdbmi.md)]

  sys.dm_db_xtp_nonclustered_index_stats 包含有關針對記憶體最佳化資料表中非叢集索引所執行之作業的統計資料。 sys.dm_db_xtp_nonclustered_index_stats 中的每一列分別代表目前資料庫中記憶體最佳化資料表的每一個非叢集索引。  
  
 sys.dm_db_xtp_nonclustered_index_stats 中反映的統計資料是在建立記憶體中的索引結構時所收集。 每次重新啟動資料庫，都會重新建立記憶體中的索引結構。  
  
 使用 sys.dm_db_xtp_nonclustered_index_stats 來了解及監視 DML 作業期間的索引活動以及資料庫何時處於線上。 當重新啟動具有記憶體最佳化資料表的資料庫時，建立索引的方式是一次將一個資料列插入記憶體中。 頁面分割、合併和彙總的計數可幫助您了解當資料庫處於線上時，建立索引所執行的工作。 您也可以在一系列的 DML 作業前後查看這些計數。  
  
 大量重試表示發生並行問題，請連絡 [!INCLUDE[msCoName](../../includes/msconame-md.md)] 支援人員。  
  
 如需記憶體優化的非叢集索引的詳細資訊，請參閱 [SQL Server In-Memory OLTP 內部總覽](https://t.co/T6zToWc6y6)，第17頁。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|object_id|**int**|物件的識別碼。|  
|xtp_object_id|**bigint**|記憶體優化資料表的識別碼。|  
|index_id|**int**|索引的識別碼。|  
|delta_pages|**bigint**|這個索引在樹狀目錄中的差異頁面總數。|  
|internal_pages|**bigint**|供內部使用。 這個索引在樹狀目錄中的內部頁面總數。|  
|leaf_pages|**bigint**|這個索引在樹狀目錄中的分葉頁面總數。|  
|outstanding_retired_nodes|**bigint**|供內部使用。 這個索引在內部結構中的節點總數。|  
|page_update_count|**bigint**|更新索引中頁面的作業累計數目。|  
|page_update_retry_count|**bigint**|更新索引中頁面之作業的重試累計數目。|  
|page_consolidation_count|**bigint**|索引中的頁面彙總累計數目。|  
|page_consolidation_retry_count|**bigint**|頁面彙總作業的重試累計數目。|  
|page_split_count|**bigint**|索引中的頁面分割作業累計數目。|  
|page_split_retry_count|**bigint**|頁面分割作業的重試累計數目。|  
|key_split_count|**bigint**|索引中的索引鍵分割累計數目。|  
|key_split_retry_count|**bigint**|索引鍵分割作業的重試累計數目。|  
|page_merge_count|**bigint**|索引中的頁面合併作業累計數目。|  
|page_merge_retry_count|**bigint**|頁面合併作業的重試累計數目。|  
|key_merge_count|**bigint**|索引中的索引鍵合併作業累計數目。|  
|key_merge_retry_count|**bigint**|索引鍵合併作業的重試累計數目。|  
  
## <a name="permissions"></a>權限  
 需要目前資料庫的 VIEW DATABASE STATE 權限。  
  
## <a name="see-also"></a>另請參閱  
 [記憶體最佳化的資料表動態管理檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/memory-optimized-table-dynamic-management-views-transact-sql.md)  
  
  
