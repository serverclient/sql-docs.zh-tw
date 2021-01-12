---
description: sys.dm_db_task_space_usage (Transact-SQL)
title: sys.dm_db_task_space_usage (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- dm_db_task_space_usage_TSQL
- sys.dm_db_task_space_usage_TSQL
- dm_db_task_space_usage
- sys.dm_db_task_space_usage
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_db_task_space_usage dynamic management view
ms.assetid: fb0c87e5-43b9-466a-a8df-11b3851dc6d0
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 0190a71c439265abea7114b4999781af2ccbc825
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98101718"
---
# <a name="sysdm_db_task_space_usage-transact-sql"></a>sys.dm_db_task_space_usage (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  傳回資料庫工作的頁面配置及取消配置活動。  
  
> [!NOTE]  
>  此視圖只適用于 [tempdb 資料庫](../../relational-databases/databases/tempdb-database.md)。  
  
> [!NOTE]  
>  若要從或呼叫這個 [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] ，請使用 **sys.dm_pdw_nodes_db_task_space_usage** 名稱。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**session_id**|**smallint**|工作階段識別碼。|  
|**request_id**|**int**|工作階段內的要求識別碼。<br /><br /> 要求也叫作批次，可包含一或多項查詢。 一個工作階段可以同時有多項作用中要求。 如果使用平行執行計畫，則要求中的每一項查詢可啟動多個執行緒 (工作)。|  
|**exec_context_id**|**int**|工作的執行內容識別碼。 如需詳細資訊，請參閱 [sys.dm_os_tasks &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-os-tasks-transact-sql.md)。|  
|**database_id**|**smallint**|資料庫識別碼。|  
|**user_objects_alloc_page_count**|**bigint**|這項工作所保留或配置給使用者物件的頁數。|  
|**user_objects_dealloc_page_count**|**bigint**|這項工作已取消配置且不再保留給使用者物件的頁數。|  
|**internal_objects_alloc_page_count**|**bigint**|這項工作所保留或配置給內部物件的頁數。|  
|**internal_objects_dealloc_page_count**|**bigint**|這項工作已取消配置且不再保留給內部物件的頁數。|  
|**pdw_node_id**|**int**|**適用** 于： [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 、 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]<br /><br /> 此散發所在之節點的識別碼。|  
  
## <a name="permissions"></a>權限

在上 [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] ，需要 `VIEW SERVER STATE` 許可權。   
在 SQL Database Basic、S0 和 S1 服務目標上，以及針對彈性集區中的資料庫，則 `Server admin` `Azure Active Directory admin` 需要或帳戶。 在所有其他 SQL Database 服務目標上， `VIEW DATABASE STATE` 資料庫中都需要有許可權。   

## <a name="remarks"></a>備註  
 IAM 頁面不包括在這份檢視所報告的任何頁面計數中。  
  
 在要求開始時，頁面計數器會初始化為零 (0)。 這些值會在要求完成時在工作階段層級上彙總。 如需詳細資訊，請參閱 [sys.dm_db_session_space_usage &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-session-space-usage-transact-sql.md)。  
  
 工作資料表快取、暫存資料表快取和延遲卸除作業，會影響在指定工作中配置和取消配置的頁數。  
  
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
 ![sys.dm_db_session_task_usage 的實體聯結](../../relational-databases/system-dynamic-management-views/media/join-dm-db-task-space-usage-1.gif "sys.dm_db_session_task_usage 的實體聯結")  
  
## <a name="relationship-cardinalities"></a>關聯性基數  
  
|寄件者|收件者|關聯性|  
|----------|--------|------------------|  
|dm_db_task_space_usage.request_id|dm_exec_requests.request_id|一對一|  
|dm_db_task_space_usage.session_id|dm_exec_requests.session_id|一對一|  
  
## <a name="see-also"></a>另請參閱  
 [動態管理檢視與函數 &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)   
 [資料庫相關的動態管理檢視 &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/database-related-dynamic-management-views-transact-sql.md)   
 [sys.dm_exec_sessions &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql.md)   
 [sys.dm_exec_requests &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql.md)   
 [sys.dm_os_tasks &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-os-tasks-transact-sql.md)   
 [sys.dm_db_session_space_usage &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-session-space-usage-transact-sql.md)   
 [sys.dm_db_file_space_usage &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-file-space-usage-transact-sql.md)  
  
  


