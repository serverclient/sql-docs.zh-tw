---
description: sys.dm_exec_background_job_queue (Transact-SQL)
title: sys.dm_exec_background_job_queue (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 08/09/2016
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- dm_exec_background_job_queue
- sys.dm_exec_background_job_queue_TSQL
- dm_exec_background_job_queue_TSQL
- sys.dm_exec_background_job_queue
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_exec_background_job_queue dynamic management function
ms.assetid: 05d9884f-b74c-4e3c-a23b-c90c1ea5ef02
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 07490d9e2a05e65fa90dd76cbdf51647f000bb3a
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98092901"
---
# <a name="sysdm_exec_background_job_queue-transact-sql"></a>sys.dm_exec_background_job_queue (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  針對每一個排程要非同步 (背景) 執行的查詢處理器作業，各傳回一個資料列。  
  
> **注意！！** 若要從或呼叫這個 **[!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)]** **[!INCLUDE[ssPDW](../../includes/sspdw-md.md)]** ，請使用 **sys.dm_pdw_nodes_exec_background_job_queue** 名稱。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**time_queued**|**datetime**|將作業加入佇列的時間。|  
|**job_id**|**int**|作業識別碼。|  
|**database_id**|**int**|執行作業的資料庫。|  
|**object_id1**|**int**|此值將視作業類型而定。 如需詳細資訊，請參閱＜備註＞一節。|  
|**object_id2**|**int**|此值將視作業類型而定。 如需詳細資訊，請參閱＜備註＞一節。|  
|**object_id3**|**int**|此值將視作業類型而定。 如需詳細資訊，請參閱＜備註＞一節。|  
|**object_id4**|**int**|此值將視作業類型而定。 如需詳細資訊，請參閱＜備註＞一節。|  
|**error_code**|**int**|因為失敗而重新插入作業的錯誤碼。 如果是已暫停 (而不是已收取或已完成)，則為 NULL。|  
|**request_type**|**smallint**|作業要求的類型。|  
|**retry_count**|**smallint**|從佇列挑選作業以及因為缺乏資源或其他原因而重新插入的次數。|  
|**in_progress**|**smallint**|指出作業是否已經開始執行。<br /><br /> 1 = 已啟動<br /><br /> 0 = 仍在等候中|  
|**session_id**|**smallint**|工作階段識別碼。|  
|**pdw_node_id**|**int**|**適用** 于： [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 、 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]<br /><br /> 此散發所在之節點的識別碼。|  
  
## <a name="permissions"></a>權限

在上 [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] ，需要 `VIEW SERVER STATE` 許可權。   
在 SQL Database Basic、S0 和 S1 服務目標上，以及針對彈性集區中的資料庫，則 `Server admin` `Azure Active Directory admin` 需要或帳戶。 在所有其他 SQL Database 服務目標上， `VIEW DATABASE STATE` 資料庫中都需要有許可權。   
  
## <a name="remarks"></a>備註  
 這個檢視只會傳回非同步更新統計資料作業的資訊。 如需非同步更新統計資料的詳細資訊，請參閱 [統計資料](../../relational-databases/statistics/statistics.md)。  
  
 透過 **object_id4** **object_id1** 的值取決於作業要求的類型。 下表將摘錄這些資料行在不同作業類型的意義。  
  
|要求類型|object_id1|object_id2|object_id3|object_id4|  
|------------------|-----------------|-----------------|-----------------|-----------------|  
|非同步更新統計資料|資料表或檢視識別碼|統計資料識別碼|未使用|未使用|  
  
## <a name="examples"></a>範例  
 下列範例會傳回 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體中，每個資料庫背景佇列的作用中非同步作業數。  
  
```  
SELECT DB_NAME(database_id) AS [Database], COUNT(*) AS [Active Async Jobs]  
FROM sys.dm_exec_background_job_queue  
WHERE in_progress = 1  
GROUP BY database_id;  
GO  
```  
  
## <a name="see-also"></a>另請參閱  
 [動態管理檢視與函數 &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)   
 [執行相關的動態管理檢視和函數 &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/execution-related-dynamic-management-views-and-functions-transact-sql.md)   
 [統計資料](../../relational-databases/statistics/statistics.md)   
 [KILL STATS JOB &#40;Transact-SQL&#41;](../../t-sql/language-elements/kill-stats-job-transact-sql.md)  
  
  



