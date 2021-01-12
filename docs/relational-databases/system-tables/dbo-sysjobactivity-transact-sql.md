---
description: dbo.sysjobactivity (Transact-SQL)
title: dbo.sysjobactivity (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 08/05/2016
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- dbo.sysjobactivity_TSQL
- dbo.sysjobactivity
- sysjobactivity
- sysjobactivity_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sysjobactivity system table
ms.assetid: fd17cac9-5d1f-4b44-b2dc-ee9346d8bf1e
author: cawrites
ms.author: chadam
ms.openlocfilehash: 091f3052a568fa5c1c0e7f521333c67947716cdf
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98098354"
---
# <a name="dbosysjobactivity-transact-sql"></a>dbo.sysjobactivity (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  記錄目前 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent 作業的活動和狀態。  此資料表儲存在 **msdb** 資料庫中。
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**session_id**|**int**|**Msdb** 資料庫的 **syssessions** 資料表中所儲存之會話的識別碼。|  
|**job_id**|**uniqueidentifier**|作業的識別碼。|  
|**run_requested_date**|**datetime**|要求執行作業的日期和時間。|  
|**run_requested_source**|**sysname(nvarchar(128))**|執行作業的要求者。<br /><br /> **1** = SOURCE_SCHEDULER<br /><br /> **2** = SOURCE_ALERTER<br /><br /> **3** = SOURCE_BOOT<br /><br /> **4** = SOURCE_USER<br /><br /> **6** = SOURCE_ON_IDLE_SCHEDULE|  
|**queued_date**|**datetime**|將這項作業放入佇列的日期和時間。 如果是直接執行這項作業，這個資料行就是 NULL。|  
|**start_execution_date**|**datetime**|排程執行作業的日期和時間。|  
|**last_executed_step_id**|**int**|先前執行的最後一個作業步驟的識別碼。|  
|**last_executed_step_**<br /><br /> **date**|**datetime**|最後一個作業步驟開始執行的日期和時間。|  
|**stop_execution_date**|**datetime**|作業執行完成的日期和時間。|  
|**job_history_id**|**int**|用來識別 **sysjobhistory** 資料表中的資料列。|  
|**next_scheduled_run_date**|**datetime**|排程下一次執行作業的日期和時間。|  

## <a name="example"></a>範例
此範例會傳回所有 SQL Server Agent 作業的執行時間狀態。  在 [!INCLUDE[tsql](../../includes/tsql-md.md)] 中執行下列 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)]。
```sql
SELECT sj.Name, 
    CASE
        WHEN sja.start_execution_date IS NULL THEN 'Not running'
        WHEN sja.start_execution_date IS NOT NULL AND sja.stop_execution_date IS NULL THEN 'Running'
        WHEN sja.start_execution_date IS NOT NULL AND sja.stop_execution_date IS NOT NULL THEN 'Not running'
    END AS 'RunStatus'
FROM msdb.dbo.sysjobs sj
JOIN msdb.dbo.sysjobactivity sja
ON sj.job_id = sja.job_id
WHERE session_id = (
    SELECT MAX(session_id) FROM msdb.dbo.sysjobactivity); 
```
  
## <a name="see-also"></a>另請參閱  
 [dbo.sysjobhistory &#40;Transact-sql&#41;](../../relational-databases/system-tables/dbo-sysjobhistory-transact-sql.md)  
  
  
