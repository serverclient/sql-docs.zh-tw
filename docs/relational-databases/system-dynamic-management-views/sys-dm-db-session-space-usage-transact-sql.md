---
description: sys.dm_db_session_space_usage (Transact-SQL)
title: sys.dm_db_session_space_usage (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 11/16/2015
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- dm_db_session_space_usage_TSQL
- dm_db_session_space_usage
- sys.dm_db_session_space_usage
- sys.dm_db_session_space_usage_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_db_session_space_usage dynamic management view
ms.assetid: a67a6045-8e14-460a-9fe3-912b846c08c1
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 45fc95bdfa5a1dabe3b8b50e5acb4b05a09d0998
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98096648"
---
# <a name="sysdm_db_session_space_usage-transact-sql"></a>sys.dm_db_session_space_usage (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  傳回資料庫的每一個工作階段已配置和取消配置的頁數。  
  
> [!NOTE]  
>  此視圖只適用于 [tempdb 資料庫](../../relational-databases/databases/tempdb-database.md)。  
  
> [!NOTE]  
>  若要從或呼叫這個 [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] ，請使用 **sys.dm_pdw_nodes_db_session_space_usage** 名稱。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**session_id**|**smallint**|工作階段識別碼。<br /><br /> **session_id** 對應至 [sys.dm_exec_sessions](../../relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql.md)中的 **session_id** 。|  
|**database_id**|**smallint**|資料庫識別碼。|  
|**user_objects_alloc_page_count**|**bigint**|這個工作階段所保留或配置給使用者物件的頁數。|  
|**user_objects_dealloc_page_count**|**bigint**|這個工作階段已取消配置且不再保留給使用者物件的頁數。|  
|**internal_objects_alloc_page_count**|**bigint**|這個工作階段所保留或配置給內部物件的頁數。|  
|**internal_objects_dealloc_page_count**|**bigint**|這個工作階段已取消配置且不再保留給內部物件的頁數。|  
|**user_objects_deferred_dealloc_page_count**|**bigint**|已標示為延後解除配置的頁面數目。<br /><br /> **注意：** 在和的 service pack 中引進 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 。|  
|**pdw_node_id**|**int**|**適用** 于： [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 、 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]<br /><br /> 此散發所在之節點的識別碼。|  
  
## <a name="permissions"></a>權限  

在上 [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] ，需要 `VIEW SERVER STATE` 許可權。   
在 SQL Database Basic、S0 和 S1 服務目標上，以及針對彈性集區中的資料庫，則 `Server admin` `Azure Active Directory admin` 需要或帳戶。 在所有其他 SQL Database 服務目標上， `VIEW DATABASE STATE` 資料庫中都需要有許可權。   

## <a name="remarks"></a>備註  
 IAM 頁面不包括在這份檢視所報告的任何配置或取消配置計數中。  
  
 在工作階段開始時，頁面計數器會初始化為零 (0)。 計數器會追蹤已配置或取消配置給工作階段中已完成工作的總頁數。 只有在工作結束時才會更新計數器；計數器不反映執行中的工作。  
  
 工作階段可以同時有多項作用中的要求。 如果是平行查詢，則一項要求可啟動多個執行緒和工作。  
  
 如需會話、要求和工作的詳細資訊，請參閱 [sys.dm_exec_sessions &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql.md)、 [sys.dm_exec_requests &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql.md)和 [sys.dm_os_tasks &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-os-tasks-transact-sql.md)。  
  
## <a name="user-objects"></a>使用者物件  
 下列物件已包括在使用者物件頁面計數器中：  
  
-   使用者自訂資料表和索引  
  
-   系統資料表和索引  
  
-   全域暫存資料表和索引  
  
-   本機暫存資料表和索引  
  
-   資料表變數  
  
-   資料表值函式中傳回的資料表  
  
## <a name="internal-objects"></a>內部物件  
 內建物件只位於 **tempdb** 中。 下列物件已包括在內部物件頁面計數器中：  
  
-   用於資料指標或多工緩衝處理作業和暫存大型物件 (LOB) 儲存體的工作資料表  
  
-   用於如雜湊聯結等作業的工作檔案  
  
-   排序執行  
  
## <a name="physical-joins"></a>實體聯結  
 ![sys.dm_db_session_space_usage 的實體聯結](../../relational-databases/system-dynamic-management-views/media/join-dm-db-session-space-usage-1.gif "sys.dm_db_session_space_usage 的實體聯結")  
  
## <a name="relationship-cardinalities"></a>關聯性基數  
  
|寄件者|收件者|關聯性|  
|----------|--------|------------------|  
|dm_db_session_space_usage.session_id|dm_exec_sessions.session_id|一對一|  
  
## <a name="see-also"></a>另請參閱  
 [動態管理檢視與函數 &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)   
 [資料庫相關的動態管理檢視 &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/database-related-dynamic-management-views-transact-sql.md)   
 [sys.dm_exec_sessions &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql.md)   
 [sys.dm_exec_requests &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql.md)   
 [sys.dm_os_tasks &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-os-tasks-transact-sql.md)   
 [sys.dm_db_task_space_usage &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-task-space-usage-transact-sql.md)   
 [sys.dm_db_file_space_usage &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-file-space-usage-transact-sql.md)  
  
  



