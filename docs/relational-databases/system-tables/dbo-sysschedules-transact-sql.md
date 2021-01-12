---
description: dbo.sysschedules (Transact-SQL)
title: dbo.sys排程 (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 08/09/2016
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- dbo.sysschedules_TSQL
- sysschedules
- sysschedules_TSQL
- dbo.sysschedules
dev_langs:
- TSQL
helpviewer_keywords:
- sysschedules system table
ms.assetid: 4cac9237-7a69-4035-bb3e-928b76aad698
author: cawrites
ms.author: chadam
ms.openlocfilehash: 31c49370b1e2ed2b5685bf46b0918fd0173f7119
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98096225"
---
# <a name="dbosysschedules-transact-sql"></a>dbo.sysschedules (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  包含 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent 作業排程的相關資訊。 此資料表儲存在 **msdb** 資料庫中。  
  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**schedule_id**|**int**|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent 作業排程的識別碼。|  
|**schedule_uid**|**uniqueidentifier**|作業排程的唯一識別碼。 這個值用來識別分散式作業的排程。|  
|**originating_server_id**|**int**|作業排程的來源主要伺服器識別碼。|  
|**name**|**sysname (Nvarchar (128) # B3**|作業排程的使用者自訂名稱。 這個名稱在作業內必須是唯一的。|  
|**owner_sid**|**varbinary(85)**|擁有作業排程之使用者或群組的 Microsoft Windows *security_identifier* 。|  
|**啟用**|**int**|作業排程的狀態：<br /><br /> **0** = 未啟用。<br /><br /> **1** = 已啟用。<br /><br /> 如果未啟用排程，便不會依據這份排程來執行任何作業。|  
|**freq_type**|**int**|針對這份排程來執行作業的頻率。<br /><br /> **1** = 僅限一次<br /><br /> **4** = 每日<br /><br /> **8** = 每週<br /><br /> **16** = 每月<br /><br /> **32** = 每月，相對於 **freq_interval**<br /><br /> **64** = 在 SQL Server Agent 服務啟動時執行<br /><br /> **128** = 電腦閒置時執行|  
|**freq_interval**|**int**|執行作業的天數。 取決於 **freq_type** 的值。 預設值為 **0**，表示 **freq_interval** 未使用。 請參閱下表以取得可能的值及其效果。|  
|**freq_subday_type**|**int**|**Freq_subday_interval** 的單位。 以下是可能的值及其描述。<br /><br /> <br /><br /> **1** ：指定時間<br /><br /> **2** ：秒<br /><br /> **4** ：分鐘<br /><br /> **8** ：小時|  
|**freq_subday_interval**|**int**|每次執行作業之間發生的 **freq_subday_type** 期間數。|  
|**freq_relative_interval**|**int**|當每個月發生 **freq_interval** 時，如果 **freq_type** 為 **32** (每月相對) 。 可以是下列值之一：<br /><br /> **0**  = 未使用 **freq_relative_interval**<br /><br /> **1** = First<br /><br /> **2** = 秒<br /><br /> **4** = 第三<br /><br /> **8** = 第四個<br /><br /> **16** = Last|  
|**freq_recurrence_**<br /><br /> **因素**|**int**|作業的排程執行之間的週數或月數。 只有當 **freq_type** 是 **8**、 **16** 或 **32** 時，才會使用 **freq_recurrence_factor** 。 如果此資料行包含 **0**，就不會使用 **freq_recurrence_factor** 。|  
|**active_start_date**|**int**|可以開始執行作業的日期。 日期格式為 YYYYMMDD。 NULL 表示今天的日期。|  
|**active_end_date**|**int**|可以停止執行作業的日期。 日期格式為 YYYYMMDD。|  
|**active_start_time**|**int**|作業開始執行 **active_start_date** 和 **active_end_date** 之間的任何一天時間。 時間格式為 HHMMSS，使用 24 小時制。|  
|**active_end_time**|**int**|**Active_start_date** 與 **active_end_date** 該作業停止執行之間任何一天的時間。 時間格式為 HHMMSS，使用 24 小時制。|  
|**date_created**|**datetime**|排程的建立日期和時間。|  
|**date_modified**|**datetime**|上次修改排程的日期和時間。|  
|**version_number**|**int**|排程的目前版本號碼。 例如，如果排程已修改為10次，則 **version_number** 為10。|  
  
|freq_type 的值|freq_interval 的作用|  
|-------------------------|------------------------------|  
|**1** (一次) |**freq_interval** 未使用 (**0**) |  
|每日) **4** (|每 **freq_interval** 天|  
|每週) **8** (|**freq_interval** 是下列一或多項：<br /><br /> **1** = 星期日<br /><br /> **2** = 星期一<br /><br /> **4** = 星期二<br /><br /> **8** = 星期三<br /><br /> **16** = 星期四<br /><br /> **32** = 星期五<br /><br /> **64** = 星期六|  
|每月 **16** () |在當月的 **freq_interval** 日|  
|**32** (每月，相對) |**freq_interval** 是下列其中一項：<br /><br /> **1** = 星期日<br /><br /> **2** = 星期一<br /><br /> **3** = 星期二<br /><br /> **4** = 星期三<br /><br /> **5** = 星期四<br /><br /> **6** = 星期五<br /><br /> **7** = 星期六<br /><br /> **8** = 天<br /><br /> **9** = 工作日<br /><br /> **10** = 週末日|  
|當 SQL Server Agent 服務啟動時，會啟動 **64** () |**freq_interval** 未使用 (**0**) |  
|**128** (在電腦閒置時執行) |**freq_interval** 未使用 (**0**) |  
  
## <a name="see-also"></a>另請參閱  
 [dbo.sysjobschedules &#40;Transact-sql&#41;](../../relational-databases/system-tables/dbo-sysjobschedules-transact-sql.md)  
  
  
