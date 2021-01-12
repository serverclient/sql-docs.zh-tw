---
description: dbo.sysjobservers (Transact-SQL)
title: dbo.sysjobservers (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 08/26/2019
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sysjobservers
- sysjobservers_TSQL
- dbo.sysjobservers
- dbo.sysjobservers_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sysjobservers system table
ms.assetid: 9abcc20f-a421-4591-affb-62674d04575e
author: cawrites
ms.author: chadam
ms.openlocfilehash: 7ac1d8facbabb42be31a9d45e091e1106c25e0c9
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98097412"
---
# <a name="dbosysjobservers-transact-sql"></a>dbo.sysjobservers (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

儲存特定作業與一部或多部目標伺服器的關聯或關係。 這份資料表儲存在 msdb 資料庫中。
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|job_id|**uniqueidentifier**|作業識別碼。|  
|server_id|**int**|伺服器識別碼。|  
|last_run_outcome|**tinyint**|作業上次執行的結果：<br /><br /> **0** = 失敗<br /><br /> **1** = 成功<br /><br /> **2** = 重試<br /><br /> **3** = 取消<br /><br /> **4** = 進行中<br /><br /> **5** = 未知 (請參閱下列備註區段)  |  
|last_outcome_ message|**nvarchar(1024)**|關聯的訊息 (如果有的話)，包含 last_run_outcome 資料行。|  
|last_run_date|**int**|上次執行作業的日期。|  
|last_run_time|**int**|前次執行作業的時間。|  
|last_run_duration|**int**|此作業執行的持續時間 (以時、分和秒為單位)。 使用公式計算： (*小時* \* 10000) + (*分鐘* \* 100) +*秒*。|  


## <a name="remarks"></a>備註

大於 *4* 的值表示 SQL Agent 不知道該作業的狀態。 建立作業時， *last_run_outcome* 一開始會設定為 *5* 。


## <a name="see-also"></a>另請參閱

[SQL Server Agent 資料表 &#40;Transact-sql&#41;](../../relational-databases/system-tables/sql-server-agent-tables-transact-sql.md)  
