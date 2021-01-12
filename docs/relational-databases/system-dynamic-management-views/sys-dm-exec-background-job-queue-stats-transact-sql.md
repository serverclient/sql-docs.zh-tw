---
description: sys.dm_exec_background_job_queue_stats (Transact-SQL)
title: sys.dm_exec_background_job_queue_stats (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- dm_exec_background_job_queue_stats
- sys.dm_exec_background_job_queue_stats
- dm_exec_background_job_queue_stats_TSQL
- sys.dm_exec_background_job_queue_stats_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_exec_background_job_queue_stats dynamic management view
ms.assetid: 27f62ab5-46c4-417e-814d-8d6437034d1c
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 3383742972ea96af500514b89e33cfdb58085a0d
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98099808"
---
# <a name="sysdm_exec_background_job_queue_stats-transact-sql"></a>sys.dm_exec_background_job_queue_stats (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  傳回提供每一個查詢處理器作業的彙總統計資料的資料列，這些作業已提交進行非同步 (背景) 執行。  
  
> [!NOTE]  
>  若要從或呼叫這個 [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] ，請使用 **sys.dm_pdw_nodes_exec_background_job_queue_stats** 名稱。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**queue_max_len**|**int**|佇列的最大長度。|  
|**enqueued_count**|**int**|成功公佈到佇列的要求數目。|  
|**started_count**|**int**|已啟動執行的要求數目。|  
|**ended_count**|**int**|成功或失敗的要求數目。|  
|**failed_lock_count**|**int**|因鎖定競爭或死結而失敗的要求數目。|  
|**failed_other_count**|**int**|因其他原因而失敗的要求數目。|  
|**failed_giveup_count**|**int**|因達到重試限制而失敗的要求數目。|  
|**enqueue_failed_full_count**|**int**|因佇列已滿而失敗的加入佇列嘗試次數。|  
|**enqueue_failed_duplicate_count**|**int**|重複加入佇列的嘗試次數。|  
|**elapsed_avg_ms**|**int**|要求的平均經歷時間 (以毫秒為單位)。|  
|**elapsed_max_ms**|**int**|最長要求的經歷時間 (以毫秒為單位)。|  
|**pdw_node_id**|**int**|**適用** 于： [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 、 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]<br /><br /> 此散發所在之節點的識別碼。|  
  
## <a name="remarks"></a>備註  
 這個檢視只會傳回非同步更新統計資料作業的資訊。 如需非同步更新統計資料的詳細資訊，請參閱 [統計資料](../../relational-databases/statistics/statistics.md)。  
  
## <a name="permissions"></a>權限

在上 [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] ，需要 `VIEW SERVER STATE` 許可權。   
在 SQL Database Basic、S0 和 S1 服務目標上，以及針對彈性集區中的資料庫，則 `Server admin` `Azure Active Directory admin` 需要或帳戶。 在所有其他 SQL Database 服務目標上， `VIEW DATABASE STATE` 資料庫中都需要有許可權。   

## <a name="examples"></a>範例  
  
### <a name="a-determining-the-percentage-of-failed-background-jobs"></a>A. 判斷背景作業失敗的百分比  
 下列範例會傳回所有執行查詢的背景作業失敗百分比。  
  
```  
SELECT   
        CASE ended_count WHEN 0   
                THEN 'No jobs ended'   
                ELSE CAST((failed_lock_count + failed_giveup_count + failed_other_count) / CAST(ended_count AS float) * 100 AS varchar(20))   
        END AS [Percent Failed]  
FROM sys.dm_exec_background_job_queue_stats;  
GO  
```  
  
### <a name="b-determining-the-percentage-of-failed-enqueue-attempts"></a>B. 判斷嘗試加入佇列失敗的百分比  
 下列範例會傳回所有執行查詢的加入佇列嘗試失敗百分比。  
  
```  
SELECT   
        CASE enqueued_count WHEN 0   
                THEN 'No jobs posted'   
                ELSE CAST((enqueue_failed_full_count + enqueue_failed_duplicate_count) / CAST(enqueued_count + enqueue_failed_full_count + enqueue_failed_duplicate_count AS float) * 100 AS varchar(20))   
        END AS [Percent Enqueue Failed]  
FROM sys.dm_exec_background_job_queue_stats;  
GO  
```  
  
## <a name="see-also"></a>另請參閱  
 [動態管理檢視與函數 &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)   
 [執行相關的動態管理檢視和函式 &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/execution-related-dynamic-management-views-and-functions-transact-sql.md)  
  
  


