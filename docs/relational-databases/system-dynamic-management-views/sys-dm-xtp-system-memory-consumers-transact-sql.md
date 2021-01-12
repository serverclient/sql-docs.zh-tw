---
description: sys.dm_xtp_system_memory_consumers (Transact-SQL)
title: sys.dm_xtp_system_memory_consumers (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- dm_xtp_system_memory_consumers
- sys.dm_xtp_system_memory_consumers_TSQL
- sys.dm_xtp_system_memory_consumers
- dm_xtp_system_memory_consumers_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_xtp_system_memory_consumers dynamic management view
ms.assetid: 9eb0dd82-7920-42e0-9e50-7ce6e7ecee8b
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 20edce0accbe4549b5c109b8d6958fd742cd4ede
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98096386"
---
# <a name="sysdm_xtp_system_memory_consumers-transact-sql"></a>sys.dm_xtp_system_memory_consumers (Transact-SQL)
[!INCLUDE [SQL Server Azure SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  報告 [!INCLUDE[hek_2](../../includes/hek-2-md.md)] 的系統層級記憶體取用者。 當配置位於使用者執行緒的內容中時，這些取用者的記憶體會從預設集區 () 或從內部集區 (如果配置是在系統執行緒) 的內容中。  
  
```  
-- system memory consumers @ instance  
select * from sys.dm_xtp_system_memory_consumers  
```  
  
 如需詳細資訊，請參閱[記憶體內部 OLTP &#40;記憶體內部最佳化&#41;](../../relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization.md)。  
  
|資料行名稱|類型|描述|  
|-----------------|----------|-----------------|  
|memory_consumer_id|**bigint**|記憶體取用者的內部識別碼。|  
|memory_consumer_type|**int**|整數，代表具有下列其中一個值的記憶體取用者類型：<br /><br /> 0-不應該顯示。 彙總兩個以上取用者的記憶體使用量。<br /><br /> 1-對應：追蹤系統對應的記憶體耗用量。<br /><br /> 2-VARHEAP：追蹤可變長度堆積的記憶體耗用量。<br /><br /> 4-IO 分頁集區：追蹤用於 IO 作業之系統分頁集區的記憶體耗用量。|  
|memory_consumer_type_desc|**Nvarchar (16)**|記憶體取用者類型的描述：<br /><br /> 0-不應該顯示。<br /><br /> 1-對應<br /><br /> 2 - VARHEAP<br /><br /> 4 - PGPOOL|  
|memory_consumer_desc|**Nvarchar (64)**|記憶體取用者執行個體的描述：<br /><br /> VARHEAP <br />系統堆積。 一般用途。 目前只用來配置記憶體回收工作項目。<br />-或-<br />對應堆積。 當對應清單中包含的項目數達到預先決定的上限時 (通常大約 5,000 個項目)，對應就會加以使用。<br /><br /> PGPOOL：針對 IO 系統集區，有三種不同的大小：系統4K 分頁集區、系統 64 K 分頁集區和系統 256 K 分頁集區。|  
|lookaside_id|**bigint**|執行緒本機、對應記憶體提供者的識別碼。|  
|pagepool_id|**bigint**|執行緒本機、分頁集區記憶體提供者的識別碼。|  
|allocated_bytes|**bigint**|保留給此取用者的位元組數。|  
|used_bytes|**bigint**|這個取用者使用的位元組。 只適用於 varheap 記憶體取用者。|  
|allocation_count|**int**|配置的數目。|  
|partition_count|**int**|僅供內部使用。|  
|sizeclass_count|**int**|僅供內部使用。|  
|min_sizeclass|**int**|僅供內部使用。|  
|max_sizeclass|**int**|僅供內部使用。|  
|memory_consumer_address|**varbinary**|取用者的內部位址。|  
  
## <a name="permissions"></a>權限  
 需要伺服器的 VIEW SERVER STATE 權限。  
  
## <a name="user-scenario"></a>使用者案例  
  
```  
-- system memory consumers @ instance  
selectmemory_consumer_type_desc,   
allocated_bytes/1024 as allocated_bytes_kb,   
used_bytes/1024 as used_bytes_kb, allocation_count  
from sys.dm_xtp_system_memory_consumers  
```  
  
 輸出會顯示所有位於系統層級的記憶體取用者。 例如，有交易對應的取用者。  
  
```  
memory_consumer_type_name           memory_consumer_desc                           allocated_bytes_kb   used_bytes_kb        allocation_count  
-------------------------------          ---------------------                          -------------------  --------------        ----------------  
VARHEAP                                  Lookaside heap                                 0                    0                    0  
VARHEAP                                  System heap                                    768                  0                    2  
LOOKASIDE                                GC transaction map entry                       64                   64                   910  
LOOKASIDE                                Redo transaction map entry                     128                  128                  1260  
LOOKASIDE                                Recovery table cache entry                     448                  448                  8192  
LOOKASIDE                                Transaction recent rows                        3264                 3264                 4444  
LOOKASIDE                                Range cursor                                   0                    0                    0  
LOOKASIDE                                Hash cursor                                    3200                 3200                 11070  
LOOKASIDE                                Transaction save-point set entry               0                    0                    0  
LOOKASIDE                                Transaction partially-inserted rows set        704                  704                  1287  
LOOKASIDE                                Transaction constraint set                     576                  576                  1940  
LOOKASIDE                                Transaction save-point set                     0                    0                    0  
LOOKASIDE                                Transaction write set                          704                  704                  672  
LOOKASIDE                                Transaction scan set                           320                  320                  156  
LOOKASIDE                                Transaction read set                           704                  704                  343  
LOOKASIDE                                Transaction                                    4288                 4288                 1459  
PGPOOL                                   System 256K page pool                          5120                 5120                 20  
PGPOOL                                   System 64K page pool                           0                    0                    0  
PGPOOL                                   System 4K page pool                            24                   24                   6  
```  
  
 若要查看系統配置器所耗用的總記憶體：  
  
```  
select sum(allocated_bytes)/(1024*1024) as total_allocated_MB, sum(used_bytes)/(1024*1024) as total_used_MB   
from sys.dm_xtp_system_memory_consumers  
  
total_allocated_MB   total_used_MB  
-------------------- --------------------  
2                    2  
```  
  
## <a name="see-also"></a>另請參閱  
 [記憶體最佳化的資料表動態管理檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/memory-optimized-table-dynamic-management-views-transact-sql.md)  
  
  
