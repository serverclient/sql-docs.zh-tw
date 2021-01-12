---
description: 'sys.dm_exec_external_work (Transact-sql) '
title: sys.dm_exec_external_work (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 11/04/2019
ms.prod: sql
ms.prod_service: database-engine, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- DM_EXEC_EXTERNAL_WORK
- DM_EXEC_EXTERNAL_WORK_TSQL
- SYS.DM_EXEC_EXTERNAL_WORK_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_exec_external_work management view
- dm_exec_external_work management view
- PolyBase,views
- PolyBase
ms.assetid: 7597d97b-1fde-4135-ac35-4af12968f300
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 32a0949cfbc06d9fb801681c6ecfa56abc9f811e
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98098959"
---
# <a name="sysdm_exec_external_work-transact-sql"></a>sys.dm_exec_external_work (Transact-sql) 
[!INCLUDE [sqlserver2016-asa-pdw](../../includes/applies-to-version/sqlserver2016-asa-pdw.md)]

  在每個計算節點上，傳回每個背景工作負載的相關資訊。  
  
 查詢 sys.dm_exec_external_work 識別與外部資料源進行通訊的工作， (例如 Hadoop 或外部 SQL Server) 。  
  
|資料行名稱|資料類型|描述|範圍|  
|-----------------|---------------|-----------------|-----------|  
|execution_id|`nvarchar(32)`|相關聯 PolyBase 查詢的唯一識別碼。|請參閱 [sys.dm_exec_requests &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql.md)中的 *request_ID* 。|  
|step_index|`int`|此背景工作正在執行的要求。|請參閱 [sys.dm_exec_requests &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql.md)中的 *step_index* 。|  
|dms_step_index|`int`|此背景工作正在執行之 DMS 計畫中的步驟。|請參閱 [sys.dm_exec_dms_workers &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-dms-workers-transact-sql.md)。|  
|compute_node_id|`int`|正在執行背景工作的節點。|請參閱 [sys.dm_exec_compute_nodes &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-compute-nodes-transact-sql.md)。|  
|類型|`nvarchar(60)`|外部工作的類型。|「檔案分割」|  
|work_id|`int`|實際分割的識別碼。|大於或等於0。|  
|input_name|`nvarchar(4000)`|要讀取之輸入的名稱|使用 Hadoop 時的檔案名。|  
|read_location|`bigint`|位移或讀取位置。|要讀取的檔案位移。|  
|bytes_processed|`bigint`|此背景工作配置給處理資料的總位元組數。 這可能不一定代表查詢所傳回的資料總計 |大於或等於0。|  
|長度|`bigint`|Hadoop 時的分割或 HDFS 區塊長度|使用者可定義。 預設值為 Ed-64m|  
|status|`nvarchar(32)`|背景工作角色的狀態|暫止、處理、完成、失敗、已中止|  
|start_time|`datetime`|工作開始||  
|end_time|`datetime`|工作結束||  
|total_elapsed_time|`int`|總時間（毫秒）||
|compute_pool_id|`int`|集區的唯一識別碼。|

## <a name="see-also"></a>另請參閱  
 [使用動態管理檢視進行 PolyBase 疑難排解](/previous-versions/sql/sql-server-2016/mt146389(v=sql.130))   
 [動態管理檢視與函數 &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)   
 [資料庫相關的動態管理檢視 &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/database-related-dynamic-management-views-transact-sql.md)  
  
