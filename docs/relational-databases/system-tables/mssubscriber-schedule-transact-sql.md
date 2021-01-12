---
description: MSsubscriber_schedule (Transact-SQL)
title: MSsubscriber_schedule (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: replication
ms.topic: language-reference
f1_keywords:
- MSsubscriber_schedule
- MSsubscriber_schedule_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- MSsubscriber_schedule system table
ms.assetid: ff428306-0ef4-49a3-b536-07ccdf6e2196
author: cawrites
ms.author: chadam
ms.openlocfilehash: fda2701763bc5b58a9936e46773f928b45b0d3d8
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98102703"
---
# <a name="mssubscriber_schedule-transact-sql"></a>MSsubscriber_schedule (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  **MSsubscriber_schedule** 資料表包含每個發行者/訂閱者組的預設合併和交易式同步處理排程。 這份資料表儲存在散發資料庫中。  
  
> [!NOTE]
>  這個系統資料表已被取代，並維持以支援舊版的 [!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**publisher**|**sysname**|發行者的名稱。|  
|**使用者**|**sysname**|訂閱者的名稱。|  
|**agent_type**|**smallint**|代理程式的類型：<br /><br /> 0 = 散發代理程式。<br /><br /> 1 = 合併代理程式。|  
|**frequency_type**|**int**|排程散發代理程式所採用的頻率：<br /><br /> **1** = 一次。<br /><br /> **2** = 隨選。<br /><br /> **4** = 每日。<br /><br /> **8** = 每週。<br /><br /> **16** = 每月。<br /><br /> **32** = 每月相對。<br /><br /> **64** = 自動啟動。<br /><br /> **128** = 週期性。|  
|**frequency_interval**|**int**|要套用至 **frequency_type** 所設定之頻率的值。|  
|**frequency_relative_interval**|**int**|散發代理程式的日期：<br /><br /> **1** = 優先。<br /><br /> **2** = 秒。<br /><br /> **4** = 第三個。<br /><br /> **8** = 第四個。<br /><br /> **16** = Last。|  
|**frequency_recurrence_factor**|**int**|**Frequency_type** 所使用的迴圈因數。|  
|**frequency_subday**|**int**|在定義的期間內，重新排程的頻率：<br /><br /> **1** = 一次。<br /><br /> **2** = 秒。<br /><br /> **4** = 分鐘。<br /><br /> **8** = 小時。|  
|**frequency_subday_interval**|**int**|**Frequency_subday** 的間隔。|  
|**active_start_time_of_day**|**int**|第一次排程散發代理程式的當日時間，格式為 HHMMSS。|  
|**active_end_time_of_day**|**int**|停止排程散發代理程式的當日時間，格式為 HHMMSS。|  
|**active_start_date**|**int**|第一次排程散發代理程式的日期，格式為 YYYYMMDD。|  
|**active_end_date**|**int**|停止排程散發代理程式的日期，格式為 YYYYMMDD。|  
  
## <a name="see-also"></a>另請參閱  
 [複寫資料表 &#40;Transact-sql&#41;](../../relational-databases/system-tables/replication-tables-transact-sql.md)   
 [複寫檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-views/replication-views-transact-sql.md)  
  
  
