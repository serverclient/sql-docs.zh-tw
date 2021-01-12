---
description: MSmerge_sessions (Transact-SQL)
title: MSmerge_sessions (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/06/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: replication
ms.topic: language-reference
f1_keywords:
- MSmerge_sessions
- MSmerge_sessions_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- MSmerge_sessions system table
ms.assetid: 09ada8fc-c148-4379-9524-7826b1b0216c
author: cawrites
ms.author: chadam
ms.openlocfilehash: 7247b3a8cf652de741fedf08a5141f6090fbcae8
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98098591"
---
# <a name="msmerge_sessions-transact-sql"></a>MSmerge_sessions (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  **MSmerge_sessions** 資料表包含記錄資料列，其中包含先前合併代理程式作業會話的結果。 每次執行合併代理程式時，都會在這個資料中加入一個新的資料列。 這份資料表儲存在散發資料庫中。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**session_id**|**int**|合併代理程式作業工作階段的識別碼。|  
|**agent_id**|**int**|合併代理程式的識別碼。|  
|**start_time**|**datetime**|作業開始執行的時間。|  
|**end_time**|**datetime**|作業執行完成的時間。|  
|**duration**|**int**|這個作業工作階段的累加持續時間 (以秒為單位)。|  
|**delivery_time**|**int**|套用變更批次所花的秒數。|  
|**upload_time**|**int**|將變更上傳到發行者所花的秒數。|  
|**download_time**|**int**|將變更下載到訂閱者所花的秒數。|  
|**delivery_rate**|**float**|每秒傳遞的平均命令數目。|  
|**time_remaining**|**int**|使用中的工作階段其餘的估計秒數。|  
|**percent_complete**|**decimal**|使用中的工作階段已傳遞的總變更估計百分比。|  
|**upload_inserts**|**int**|發行者端所套用的插入數目。|  
|**upload_updates**|**int**|發行者端所套用的更新數目。|  
|**upload_deletes**|**int**|發行者端所套用的刪除數目。|  
|**upload_conflicts**|**int**|在發行者端套用變更時，所發生的衝突數目。|  
|**upload_conflicts_resolved**|**int**|在發行者端套用變更時，所發生且已解決的衝突數目。|  
|**upload_rows_retried**|**int**|上傳到發行者且已重試的資料列數。|  
|**download_inserts**|**int**|訂閱者端所套用的插入數目。|  
|**download_updates**|**int**|訂閱者端所套用的更新數目。|  
|**download_deletes**|**int**|訂閱者端所套用的刪除數目。|  
|**download_conflicts**|**int**|在訂閱者端套用變更時，所發生的衝突數目。|  
|**download_conflicts_resolved**|**int**|在訂閱者端套用變更時，所發生且已解決的衝突數目。|  
|**download_rows_retried**|**int**|下載到訂閱者且已重試的資料列數。|  
|**schema_changes**|**int**|工作階段期間所套用的結構描述變更數目。|  
|**metadata_rows_cleanedup**|**int**|工作階段期間在期間，清除中繼資料的資料列數。|  
|**runstatus**|**int**|執行狀態如下：<br /><br /> **1** = 開始。<br /><br /> **2** = 成功。<br /><br /> **3** = 進行中。<br /><br /> **4** = 閒置。<br /><br /> **5** = 重試。<br /><br /> **6** = 失敗。|  
|**estimated_upload_changes**|**int**|發行者端必須套用的估計變更數目。|  
|**estimated_download_changes**|**int**|訂閱者端必須套用的估計變更數目。|  
|**connection_type**|**int**|上傳期間所用的連接：<br /><br /> **1** = 區域網路 (LAN) 。<br /><br /> **2** = 撥號網路連接。<br /><br /> **3** = Web 同步處理。|  
|**timestamp**|**timestamp**|這份資料表的時間戳記資料行。|  
  
## <a name="see-also"></a>另請參閱  
 [複寫資料表 &#40;Transact-sql&#41;](../../relational-databases/system-tables/replication-tables-transact-sql.md)   
 [複寫檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-views/replication-views-transact-sql.md)  
  
  
