---
description: dbo.sysjobhistory (Transact-SQL)
title: dbo.sysjobhistory (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/24/2019
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- dbo.sysjobhistory_TSQL
- dbo.sysjobhistory
- sysjobhistory
- sysjobhistory_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sysjobhistory system table
ms.assetid: 1b1fcdbb-2af2-45e6-bf3f-e8279432ce13
author: cawrites
ms.author: chadam
ms.openlocfilehash: 6fa83e3df6cf6f05420341c497c8b3bfd2a481df
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98098704"
---
# <a name="dbosysjobhistory-transact-sql"></a>dbo.sysjobhistory (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

包含 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent 執行排程作業的相關資訊。
  
> [!NOTE]
> 在大多數情況下，只有在作業步驟完成時才會更新資料，而且資料表通常不會包含目前正在進行的作業步驟記錄，但是在某些情況下，基礎進程 *會提供進行* 中作業步驟的相關資訊。

此資料表儲存在 **msdb** 資料庫中。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**instance_id**|**int**|資料列的唯一識別碼。|  
|**job_id**|**uniqueidentifier**|作業識別碼。|  
|**step_id**|**int**|作業中步驟的識別碼。|  
|**step_name**|**sysname**|步驟的名稱。|  
|**sql_message_id**|**int**|當作業失敗時，任何傳回的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 錯誤訊息的識別碼。|  
|**sql_severity**|**int**|任何 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 錯誤的嚴重性。|  
|**message**|**nvarchar(4000)**|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 錯誤 (如果有的話) 的文字。|  
|**run_status**|**int**|作業執行的狀態：<br /><br /> **0** = 失敗<br /><br /> **1** = 成功<br /><br /> **2** = 重試<br /><br /> **3** = 已取消<br /><br />**4** = 進行中|  
|**run_date**|**int**|作業或步驟開始執行的日期。 如果是「進行中」記錄，這就是開始寫入記錄的日期/時間。|  
|**run_time**|**int**|作業或步驟以 **HHMMSS** 格式啟動的時間。|  
|**run_duration**|**int**|以 **HHMMSS** 格式執行作業或步驟所經過的時間。|  
|**operator_id_emailed**|**int**|作業完成時所通知之操作員的識別碼。|  
|**operator_id_netsent**|**int**|當作業完成時，訊息所通知之操作員的識別碼。|  
|**operator_id_paged**|**int**|當作業完成時，呼叫器所通知之操作員的識別碼。|  
|**retries_attempted**|**int**|作業或步驟的重試次數。|  
|**伺服器**|**sysname**|執行作業的伺服器名稱。|  
  
  ## <a name="example"></a>範例
 下列 [!INCLUDE[tsql](../../includes/tsql-md.md)] 查詢會將 **run_time** 和 **run_duration** 資料行轉換為更容易使用的格式。  執行中的腳本 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 。
 
 ```sql
 SET NOCOUNT ON;
 
 SELECT sj.name,
        sh.run_date,
        sh.step_name,
        STUFF(STUFF(RIGHT(REPLICATE('0', 6) +  CAST(sh.run_time as varchar(6)), 6), 3, 0, ':'), 6, 0, ':') 'run_time',
        STUFF(STUFF(STUFF(RIGHT(REPLICATE('0', 8) + CAST(sh.run_duration as varchar(8)), 8), 3, 0, ':'), 6, 0, ':'), 9, 0, ':') 'run_duration (DD:HH:MM:SS)  '
FROM msdb.dbo.sysjobs sj
JOIN msdb.dbo.sysjobhistory sh
ON sj.job_id = sh.job_id
```
