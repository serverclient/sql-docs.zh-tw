---
description: 'sys.dm_exec_distributed_sql_requests (Transact-sql) '
title: sys.dm_exec_distributed_sql_requests (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- DM_EXEC_DISTRIBUTED_SQL_REQUESTS_TSQL
- SYS.DM_EXEC_DISTRIBUTED_SQL_REQUESTS_TSQL
- DM_EXEC_DISTRIBUTED_SQL_REQUESTS
dev_langs:
- TSQL
helpviewer_keywords:
- PolyBase,views
- PolyBase
- sys.dm_exec_distributed_requests management view
- dm_exec_distributed_requests management view
ms.assetid: d065dc01-35d4-472f-9554-53ac41e7d104
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 1b03ee4a4c974be2b035074f03b8397df580f75d
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98098999"
---
# <a name="sysdm_exec_distributed_sql_requests-transact-sql"></a>sys.dm_exec_distributed_sql_requests (Transact-sql) 
[!INCLUDE [sqlserver2016-asdbmi-asa-pdw](../../includes/applies-to-version/sqlserver2016-asdbmi-asa-pdw.md)]

  保存所有 SQL 查詢散發的相關資訊，作為查詢中 SQL 步驟的一部分。  這個視圖會顯示最後一個1000要求的資料;使用中的要求一律會有此 view 中的資料。  
  
|資料行名稱|資料類型|描述|範圍|  
|-----------------|---------------|-----------------|-----------|  
|execution_id|**nvarchar(32)**|execution_id 並 step_index 組成此視圖的金鑰。 與要求相關聯的唯一數值識別碼。|請參閱[sys.dm_exec_requests 中 &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql.md)的識別碼|  
|step_index|**int**|此散發所屬之查詢步驟的索引。|請參閱 [sys.dm_exec_distributed_request_steps &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-distributed-request-steps-transact-sql.md)中的 step_index。|  
|compute_node_id|**int**|此步驟所表示之作業的類型。|請參閱 [sys.dm_exec_compute_nodes &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-compute-nodes-transact-sql.md)中的 compute_node_id。|  
|distribution_id|**int**|步驟執行所在的位置。|針對在節點範圍（而非散發範圍）執行的要求，設定為-1。|  
|status|**nvarchar(32)**|此步驟的狀態|作用中、已取消、已完成、失敗、已排入佇列|  
|error_id|**Nvarchar (36)**|與此步驟相關聯之錯誤的唯一識別碼（如果有的話）|請參閱 [sys.dm_exec_compute_node_errors &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-compute-node-errors-transact-sql.md)的識別碼，如果沒有發生錯誤則為 Null。|  
|start_time|**datetime**|步驟開始執行的時間|小於或等於目前的時間，而且大於或等於這個步驟所屬查詢的 end_compile_time。|  
|end_time|**datetime**|此步驟完成執行、已取消或失敗的時間。|小於或等於目前的時間，大於或等於 start_time，請將目前執行中或已排入佇列的步驟設定為 Null。|  
|total_elapsed_time|**int**|查詢步驟執行的總時間量（以毫秒為單位）|介於0和 end_time 與 start_time 之間的差異。 0表示已排入佇列的步驟。|  
|row_count|**bigint**|此要求變更或傳回的資料列總數|0針對未變更或傳回資料的步驟，則會受到影響的資料列數目。 針對 DMS 步驟，設定為-1。|  
|spid|**int**|執行查詢散發的 SQL Server 實例上的會話識別碼||  
|命令|nvarchar(4000)|保存此步驟之命令的完整文字。|步驟的任何有效要求字串。 如果超過4000個字元，則會截斷。|  
  
## <a name="see-also"></a>另請參閱  
 [使用動態管理檢視進行 PolyBase 疑難排解](/previous-versions/sql/sql-server-2016/mt146389(v=sql.130))   
 [動態管理檢視與函數 &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)   
 [資料庫相關的動態管理檢視 &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/database-related-dynamic-management-views-transact-sql.md)  
  
