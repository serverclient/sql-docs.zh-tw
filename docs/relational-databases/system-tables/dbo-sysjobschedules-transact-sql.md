---
description: dbo.sysjobschedules (Transact-SQL)
title: dbo.sysjobschedules (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 08/09/2016
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sysjobschedules
- dbo.sysjobschedules
- dbo.sysjobschedules_TSQL
- sysjobschedules_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sysjobschedules system table
ms.assetid: ccdafec7-2a9b-4356-bffb-1caa3a12db59
author: cawrites
ms.author: chadam
ms.openlocfilehash: 3880d33e9c69a4911aa64487c34e636ce45a3a05
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98096267"
---
# <a name="dbosysjobschedules-transact-sql"></a>dbo.sysjobschedules (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  包含 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent 要執行之作業的排程資訊。 此資料表儲存在 **msdb** 資料庫中。  
  
> **注意：****Sysjobschedules** 資料表每隔20分鐘會重新整理一次，這可能會影響 **sp_help_jobschedule** 預存程式所傳回的值。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**schedule_id**|**int**|排程的識別碼。|  
|**job_id**|**uniqueidentifier**|作業的識別碼。|  
|**next_run_date**|**int**|排程下一次執行作業的日期。 日期格式為 YYYYMMDD。|  
|**next_run_time**|**int**|排程執行作業的時間。 時間格式為 HHMMSS，使用 24 小時制。|  
  
## <a name="see-also"></a>另請參閱  
 [dbo.sys排程 &#40;Transact-sql&#41;](../../relational-databases/system-tables/dbo-sysschedules-transact-sql.md)  
  
  
