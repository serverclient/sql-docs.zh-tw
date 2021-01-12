---
description: 'sys.dm_exec_dms_workers (Transact-sql) '
title: sys.dm_exec_dms_workers (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 11/04/2019
ms.prod: sql
ms.prod_service: database-engine, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- SYS.DM_EXEC_DMS_WORKERS_TSQL
- DM_EXEC_DMS_WORKERS_TSQL
- DM_EXEC_DMS_WORKERS
dev_langs:
- TSQL
helpviewer_keywords:
- PolyBase,views
- PolyBase
- dm_exec_dms_workers management view
- sys.dm_exec_dms_workers management view
ms.assetid: f468da29-78c3-4f10-8a3c-17905bbf46f2
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: dfcf81fb2a49278c4a65ddc3e1e532d666542a20
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98092844"
---
# <a name="sysdm_exec_dms_workers-transact-sql"></a>sys.dm_exec_dms_workers (Transact-sql) 
[!INCLUDE [sqlserver2016-asa-pdw](../../includes/applies-to-version/sqlserver2016-asa-pdw.md)]

  保存所有工作者完成 DMS 步驟的相關資訊。  
  
 這個視圖會顯示最後一個1000要求和使用中要求的資料;使用中的要求一律會有此 view 中的資料。  
  
|資料行名稱|資料類型|描述|範圍|  
|-----------------|---------------|-----------------|-----------|  
|execution_id|`nvarchar(32)`|查詢此 DMS 背景工作角色所屬的。 <br /><br /> execution_id、step_index 和 dms_step_index 會形成此視圖的索引鍵。||  
|step_index|`int`|此 DMS 背景工作角色所屬的查詢步驟。|請參閱 [sys.dm_exec_distributed_request_steps &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-distributed-request-steps-transact-sql.md)中的步驟索引。|  
|dms_step_index|`int`|此背景工作正在執行之 DMS 計畫中的步驟。|請參閱 [sys.dm_exec_dms_workers (transact-sql) ](../../relational-databases/system-dynamic-management-views/sys-dm-exec-dms-workers-transact-sql.md)|  
|compute_node_id|`int`|正在執行背景工作的節點。|請參閱 [sys.dm_exec_compute_nodes &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-compute-nodes-transact-sql.md)。|  
|distribution_id|`int`|||  
|類型|`nvarchar(32)`|此專案代表的 DMS 工作者執行緒類型。|「DIRECT_CONVERTER」、「DIRECT_READER」、「FILE_READER」、「HASH_CONVERTER」、「HASH_READER」、「ROUNDROBIN_CONVERTER」、「EXPORT_READER」、「EXTERNAL_READER」、「EXTERNAL_WRITER」、「PARALLEL_COPY_READER」、「REJECT_WRITER」、「寫入器」|  
|status|`nvarchar(32)`|此步驟的狀態|「擱置」、「執行中」、「完成」、「失敗」、「UndoFailed」、「PendingCancel」、「已取消」、「復原」、「已中止」|  
|bytes_per_sec|`bigint`|||  
|bytes_processed|`bigint`|||  
|rows_processed|`bigint`|||  
|start_time|`datetime`|步驟開始執行的時間|小於或等於目前的時間，而且大於或等於這個步驟所屬查詢的 end_compile_time。|  
|end_time|`datetime`|此步驟完成執行、已取消或失敗的時間。|小於或等於目前的時間，大於或等於 start_time，請將目前執行中或已排入佇列的步驟設定為 Null。|  
|total_elapsed_time|`int`|查詢步驟執行的總時間量（以毫秒為單位）|介於0和 end_time 與 start_time 之間的差異。 0表示已排入佇列的步驟。|  
|cpu_time|`bigint`|||  
|query_time|`int`|||  
|buffers_available|`int`|||  
|dms_cpid|`int`|||  
|sql_spid|`int`|||  
|error_id|`nvarchar(36)`|||  
|source_info|`nvarchar(4000)`|||  
|destination_info|`nvarchar(4000)`|||  
|命令|`nvarchar(4000)`|||
|compute_pool_id|`int`|集區的唯一識別碼。|

## <a name="see-also"></a>另請參閱  
 [使用動態管理檢視進行 PolyBase 疑難排解](/previous-versions/sql/sql-server-2016/mt146389(v=sql.130))   
 [動態管理檢視與函數 &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)   
 [資料庫相關的動態管理檢視 &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/database-related-dynamic-management-views-transact-sql.md)  
  
