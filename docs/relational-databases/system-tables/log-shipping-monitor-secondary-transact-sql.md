---
description: log_shipping_monitor_secondary (Transact-SQL)
title: log_shipping_monitor_secondary (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- log_shipping_monitor_secondary_TSQL
- log_shipping_monitor_secondary
dev_langs:
- TSQL
helpviewer_keywords:
- log_shipping_monitor_secondary system table
ms.assetid: afbe1bb7-89a7-4020-9408-0af64a043c2e
author: cawrites
ms.author: chadam
ms.openlocfilehash: fbe298651268089a668e6be5a334c49d8623ddb9
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98102118"
---
# <a name="log_shipping_monitor_secondary-transact-sql"></a>log_shipping_monitor_secondary (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  在記錄傳送組態中，儲存每個次要資料庫各一項監視記錄。 此資料表儲存在 **msdb** 資料庫中。  
  
 主要伺服器和次要伺服器也會使用記錄和監視的相關資料表。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**secondary_server**|**sysname**|記錄傳送設定中的次要實例名稱 [!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 。|  
|**secondary_database**|**sysname**|記錄傳送組態中之次要資料庫的名稱。|  
|**secondary_id**|**uniqueidentifier**|記錄傳送組態中之次要伺服器的識別碼。|  
|**primary_server**|**sysname**|記錄傳送組態中之 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 主要執行個體的名稱。|  
|**primary_database**|**sysname**|記錄傳送組態中之主要資料庫的名稱。|  
|**restore_threshold**|**int**|在產生警示之前，還原作業之間所能經歷的時間 (以分鐘為單位)。|  
|**threshold_alert**|**int**|當超出還原臨界值時所產生的警示。|  
|**threshold_alert_enabled**|**bit**|決定是否啟用還原臨界值警示。 1 = 已啟用。<br /><br /> 0 = 已停用。|  
|**last_copied_file**|**Nvarchar (500)**|前一個複製到次要伺服器的備份檔之檔案名稱。|  
|**last_copied_date**|**datetime**|在次要伺服器中，前一個複製作業的日期和時間。|  
|**last_copied_date_utc**|**datetime**|在次要伺服器中，前一個複製作業的日期和時間 (以國際標準時間 (UTC) 為單位)。|  
|**last_restored_file**|**Nvarchar (500)**|前一個還原到次要資料庫的備份檔之檔案名稱。|  
|**last_restored_date**|**datetime**|在次要資料庫中，前一個還原作業的日期和時間。|  
|**last_restored_date_utc**|**datetime**|在次要資料庫中，前一個還原作業的日期和時間 (以國際標準時間 (UTC) 為單位)。|  
|**last_restored_latency**|**int**|在主要資料庫中建立記錄備份和在次要資料庫中還原這個記錄備份，其間所經歷的時間 (以分鐘為單位)。<br /><br /> 初始值是 NULL。|  
|**history_retention_period**|**int**|給定次要資料庫的記錄傳送記錄，在刪除之前所保留的時間 (以分鐘為單位)。|  
  
## <a name="remarks"></a>備註  
 除了儲存在遠端監視伺服器之外，次要伺服器的相關資訊也會儲存在次要伺服器的 **log_shipping_monitor_secondary** 資料表中。  
  
## <a name="see-also"></a>另請參閱  
 [關於記錄傳送 &#40;SQL Server&#41;](../../database-engine/log-shipping/about-log-shipping-sql-server.md)   
 [sp_refresh_log_shipping_monitor &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-refresh-log-shipping-monitor-transact-sql.md)   
 [sp_add_log_shipping_secondary_database &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-add-log-shipping-secondary-database-transact-sql.md)   
 [sp_change_log_shipping_secondary_database &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-change-log-shipping-secondary-database-transact-sql.md)   
 [sp_delete_log_shipping_secondary_database &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-delete-log-shipping-secondary-database-transact-sql.md)   
 [sp_help_log_shipping_secondary_database &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-help-log-shipping-secondary-database-transact-sql.md)   
 [sp_help_log_shipping_monitor_secondary &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-help-log-shipping-monitor-secondary-transact-sql.md)   
 [sp_refresh_log_shipping_monitor &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-refresh-log-shipping-monitor-transact-sql.md)   
 [系統資料表 &#40;Transact-SQL&#41;](../../relational-databases/system-tables/system-tables-transact-sql.md)  
  
  
