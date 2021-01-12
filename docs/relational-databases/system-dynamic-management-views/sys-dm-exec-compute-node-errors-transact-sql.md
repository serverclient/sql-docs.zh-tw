---
description: 'sys.dm_exec_compute_node_errors (Transact-sql) '
title: sys.dm_exec_compute_node_errors (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 11/04/2019
ms.prod: sql
ms.prod_service: database-engine, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- SYS.DM_EXEC_COMPUTE_NODE_ERRORS_TSQL
- DM_EXEC_COMPUTE_NODE_ERRORS
- DM_EXEC_COMPUTE_NODE_ERRORS_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- PolyBase
- PolyBase, views
- dm_exec_compute_node_errors
- sys.dm_exec_compute_node_errors management view
ms.assetid: 9a03c039-70e4-4974-95d8-d3fa45984ffb
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 6f2de93073776522947a227f60ef9aa3fd894323
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98094088"
---
# <a name="sysdm_exec_compute_node_errors-transact-sql"></a>sys.dm_exec_compute_node_errors (Transact-sql) 

[!INCLUDE [sqlserver2016-asdbmi-asa-pdw](../../includes/applies-to-version/sqlserver2016-asa-pdw.md)]

  傳回在 PolyBase 計算節點上發生的錯誤。  
  
|資料行名稱|資料類型|描述|範圍|  
|-----------------|---------------|-----------------|-----------|  
|error_id|`nvarchar(36)`|與錯誤相關聯的唯一數值識別碼。|在系統的所有查詢錯誤中都是唯一的|  
|source|`nvarchar(255)`|來源執行緒或進程描述||  
|類型|`nvarchar(255)`|錯誤的類型。||  
|create_time|`datetime`|發生錯誤的時間||  
|compute_node_id|`int`|特定計算節點的識別碼|請參閱[sys.dm_exec_compute_nodes &#40;transact-sql](../../relational-databases/system-dynamic-management-views/sys-dm-exec-compute-nodes-transact-sql.md)的 compute_node_id&#41;|  
|rexecution_id|`nvarchar(36)`|PolyBase 查詢的識別碼（如果有的話）。||  
|spid|`int`|SQL Server 會話的識別碼||  
|thread_id|`int`|發生錯誤之執行緒的數值識別碼。||  
|詳細資料|nvarchar(4000)|錯誤詳細資料的完整描述。||
|compute_pool_id|`int`|集區的唯一識別碼。|

  
## <a name="see-also"></a>另請參閱  
 [使用動態管理檢視進行 PolyBase 疑難排解](/previous-versions/sql/sql-server-2016/mt146389(v=sql.130))   
 [動態管理檢視與函數 &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)   
 [資料庫相關的動態管理檢視 &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/database-related-dynamic-management-views-transact-sql.md)  
  
