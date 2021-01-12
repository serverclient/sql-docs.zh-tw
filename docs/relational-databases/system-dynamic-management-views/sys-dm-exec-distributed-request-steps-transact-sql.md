---
description: 'sys.dm_exec_distributed_request_steps (Transact-sql) '
title: sys.dm_exec_distributed_request_steps (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- SYS.DM_EXEC_DISTRIBUTED_REQUEST_STEPS_TSQL
- DM_EXEC_DISTRIBUTED_REQUEST_STEPS_TSQL
- DM_EXEC_DISTRIBUTED_REQUEST_STEPS
dev_langs:
- TSQL
helpviewer_keywords:
- PolyBase,views
- PolyBase
- dm_exec_distributed_request_steps
- sys.dm_exec_distributed_request_steps management view
ms.assetid: 1954541d-b716-4e03-8fcc-7022f428e01d
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 57ea8cc29caae50f91c8351bf72af494b000b178
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98094076"
---
# <a name="sysdm_exec_distributed_request_steps-transact-sql"></a>sys.dm_exec_distributed_request_steps (Transact-sql) 
[!INCLUDE [sqlserver2016-asa-pdw](../../includes/applies-to-version/sqlserver2016-asa-pdw.md)]

  保存所有組成指定 PolyBase 要求或查詢之步驟的相關資訊。 它會針對每個查詢步驟列出一個資料列。  
  
|資料行名稱|資料類型|描述|範圍|  
|-----------------|---------------|-----------------|-----------|  
|execution_id|**int**|execution_id 並 step_index 組成此視圖的金鑰。 與要求相關聯的唯一數值識別碼。|請參閱 [sys.dm_exec_requests 中 &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql.md)的識別碼。|  
|step_index|**int**|此步驟在提出要求的步驟順序中的位置。|0到 (n-1) 用於具有 n 個步驟的要求。|  
|operation_type|**nvarchar(128)**|此步驟所表示之作業的類型。|' MoveOperation '、' OnOperation '、' RandomIDOperation '、' RemoteOperation '、' ReturnOperation '、' ShuffleMoveOperation '、' TempTablePropertiesOperation '、' DropDiagnosticsNotifyOperation '、' HadoopShuffleOperation '、' HadoopBroadCastOperation '、' HadoopRoundRobinOperation '|  
|distribution_type|**nvarchar(32)**|步驟執行所在的位置。|' AllComputeNodes '、' AllDistributions '、' ComputeNode '、' 散發 '、' >allnodes '、' SubsetNodes '、' SubsetDistributions '、' 未指定 '。|  
|location_type|**nvarchar(32)**|步驟執行所在的位置。|「計算」、「標頭」或「DMS」。 所有資料移動步驟都會顯示「DMS」。|  
|status|**nvarchar(32)**|此步驟的狀態|「擱置」、「執行中」、「完成」、「失敗」、「UndoFailed」、「PendingCancel」、「已取消」、「復原」、「已中止」|  
|error_id|**Nvarchar (36)**|與此步驟相關聯之錯誤的唯一識別碼（如果有的話）|請參閱 [sys.dm_exec_compute_node_errors &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-compute-node-errors-transact-sql.md)的識別碼，如果沒有發生錯誤則為 Null。|  
|start_time|**datetime**|步驟開始執行的時間|小於或等於目前的時間，而且大於或等於這個步驟所屬查詢的 end_compile_time。|  
|end_time|**datetime**|此步驟完成執行、已取消或失敗的時間。|小於或等於目前的時間，大於或等於 start_time，請將目前執行中或已排入佇列的步驟設定為 Null。|  
|total_elapsed_time|**int**|查詢步驟執行的總時間量（以毫秒為單位）|介於0和 end_time 與 start_time 之間的差異。 0表示已排入佇列的步驟。|  
|row_count|**bigint**|此要求變更或傳回的資料列總數|0針對未變更或傳回資料的步驟，則會受到影響的資料列數目。 針對 DMS 步驟，設定為-1。|  
|命令|nvarchar(4000)|保存此步驟之命令的完整文字。|步驟的任何有效要求字串。 如果超過4000個字元，則會截斷。|  
  
## <a name="see-also"></a>另請參閱  
 [使用動態管理檢視進行 PolyBase 疑難排解](/previous-versions/sql/sql-server-2016/mt146389(v=sql.130))   
 [動態管理檢視與函數 &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)   
 [資料庫相關的動態管理檢視 &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/database-related-dynamic-management-views-transact-sql.md)  
  
