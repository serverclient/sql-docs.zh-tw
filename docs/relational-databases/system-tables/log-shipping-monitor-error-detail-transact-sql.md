---
description: log_shipping_monitor_error_detail (Transact-SQL)
title: log_shipping_monitor_error_detail (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- log_shipping_monitor_error_detail_TSQL
- log_shipping_monitor_error_detail
dev_langs:
- TSQL
helpviewer_keywords:
- log_shipping_monitor_error_detail system table
ms.assetid: 0c38a625-60d2-4ee2-bcf3-2ba367914220
author: cawrites
ms.author: chadam
ms.openlocfilehash: 131ee9bfe37dce356c6e1e4e604e8e8f0c3df2c1
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98097444"
---
# <a name="log_shipping_monitor_error_detail-transact-sql"></a>log_shipping_monitor_error_detail (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  儲存記錄傳送作業的錯誤詳細資料。 此資料表儲存在 **msdb** 資料庫中。  
  
 主要伺服器和次要伺服器也會使用記錄和監視的相關資料表。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**agent_id**|**uniqueidentifier**|用來備份的主要識別碼，或用來複製或還原的次要識別碼。|  
|**agent_type**|**tinyint**|記錄傳送作業的類型。<br /><br /> 0 = 備份。<br /><br /> 1 = 複製。<br /><br /> 2 = 還原。|  
|**session_id**|**int**|備份/複製/還原作業的工作階段識別碼。|  
|**database_name**|**sysname**|這個錯誤記錄的相關資料庫名稱。 主要資料庫會用於備份；次要資料庫會用於還原；而空白則會用於複製。|  
|**sequence_number**|**int**|這是一個累加數字，指出跨越多項記錄之錯誤資訊的正確順序。|  
|**log_time**|**datetime**|建立記錄的日期和時間。|  
|**log_time_utc**|**datetime**|建立記錄的日期和時間，用國際標準時間 (UTC) 來表示。|  
|**message**|**nvarchar**|訊息文字。|  
|**source**|**nvarchar**|錯誤訊息或事件的來源。|  
|**help_url**|**nvarchar**|能夠找到更多錯誤相關資訊的 URL (如果有的話)。|  
  
## <a name="remarks"></a>備註  
 這份資料表包含記錄傳送代理程式的錯誤詳細資料。 每個錯誤都會記錄成例外狀況的序列。 每個代理程式工作階段都可能有多個錯誤 (序列)。  
  
 除了儲存在遠端監視伺服器之外，主伺服器的相關資訊也會儲存在主伺服器的 **log_shipping_monitor_error_detail** 資料表中，而次要伺服器的相關資訊也會儲存在次要伺服器的 **log_shipping_monitor_error_detail** 資料表中。  
  
 若要識別代理程式會話，請使用 **agent_id**、 **agent_type** 和 **session_id** 資料行。 依 **log_time** 排序，以查看錯誤的記錄順序。  
  
## <a name="see-also"></a>另請參閱  
 [關於記錄傳送 &#40;SQL Server&#41;](../../database-engine/log-shipping/about-log-shipping-sql-server.md)   
 [log_shipping_monitor_history_detail &#40;Transact-sql&#41;](../../relational-databases/system-tables/log-shipping-monitor-history-detail-transact-sql.md)   
 [sp_cleanup_log_shipping_history &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-cleanup-log-shipping-history-transact-sql.md)   
 [sp_delete_log_shipping_primary_database &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-delete-log-shipping-primary-database-transact-sql.md)   
 [sp_delete_log_shipping_secondary_database &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-delete-log-shipping-secondary-database-transact-sql.md)   
 [sp_refresh_log_shipping_monitor &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-refresh-log-shipping-monitor-transact-sql.md)   
 [系統資料表 &#40;Transact-SQL&#41;](../../relational-databases/system-tables/system-tables-transact-sql.md)  
  
  
