---
description: 'sys.dm_exec_compute_node_status (Transact-sql) '
title: sys.dm_exec_compute_node_status (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 11/04/2019
ms.prod: sql
ms.prod_service: database-engine, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- DM_EXEC_COMPUTE_NODE_STATUS_TSQL
- DM_EXEC_COMPUTE_NODE_STATUS
dev_langs:
- TSQL
helpviewer_keywords:
- PolyBase,views
- PolyBase
- dm_exec_compute_node_status
- sys.dm_exec_compute_node_status management view
ms.assetid: b606f91f-3a08-4a4f-bb57-32ae155b3738
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: b649f3ea0ff67770a18376e95c86b8ef2b8aefb0
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98101603"
---
# <a name="sysdm_exec_compute_node_status-transact-sql"></a>sys.dm_exec_compute_node_status (Transact-sql) 
[!INCLUDE [sqlserver2016-asa-pdw](../../includes/applies-to-version/sqlserver2016-asa-pdw.md)]

  保存所有 PolyBase 節點的效能和狀態的其他相關資訊。 列出每個節點一個資料列。  
  
|資料行名稱|資料類型|描述|範圍|  
|-----------------|---------------|-----------------|-----------|  
|compute_node_id|`int`|與節點相關聯的唯一數值識別碼。|不論類型為何，都是在相應放大叢集中唯一的。|  
|process_id|`int`|||  
|process_name|`nvarchar(255)`|節點的邏輯名稱。|適當長度的任何字串。|  
|allocated_memory|`bigint`|此節點上配置的記憶體總數。||  
|available_memory|`bigint`|此節點的可用記憶體總計。||  
|process_cpu_usage|`bigint`|總處理常式 CPU 使用量（以刻度為單位）。||  
|total_cpu_usage|`bigint`|總 CPU 使用量（以刻度為單位）。||  
|thread_count|`bigint`|此節點上使用中的執行緒總數。||  
|handle_count|`bigint`|此節點上使用中的控制碼總數。||  
|total_elapsed_time|`bigint`|自系統啟動或重新開機以來經過的總時間。|自系統啟動或重新開機以來經過的總時間。 如果 total_elapsed_time 超過整數 (24.8 天（以毫秒為單位）的最大值) ，則會造成具體化失敗，因為溢位。以毫秒為單位的最大值相當於24.8 天。|  
|is_available|`bit`|指出這個節點是否可用的旗標。||  
|sent_time|`datetime`|上次傳送網路封裝的時間||  
|received_time|`datetime`|上次此節點傳送網路封裝的時間。||  
|error_id|`nvarchar(36)`|在此節點上發生最後一個錯誤的唯一識別碼。||
|compute_pool_id|`int`|集區的唯一識別碼。|

## <a name="see-also"></a>另請參閱  
 [使用動態管理檢視進行 PolyBase 疑難排解](/previous-versions/sql/sql-server-2016/mt146389(v=sql.130))   
 [動態管理檢視與函數 &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)   
 [資料庫相關的動態管理檢視 &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/database-related-dynamic-management-views-transact-sql.md)  
  
