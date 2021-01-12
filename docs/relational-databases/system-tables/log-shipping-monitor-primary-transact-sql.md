---
description: log_shipping_monitor_primary (Transact-SQL)
title: log_shipping_monitor_primary (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- log_shipping_monitor_primary
- log_shipping_monitor_primary_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- log_shipping_monitor_primary system table
ms.assetid: 5f629a29-1a62-40e6-ae33-6f6b7dd09a36
author: cawrites
ms.author: chadam
ms.openlocfilehash: 076398da53bf4886a0b8e3f387df949d29cb8b03
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98098688"
---
# <a name="log_shipping_monitor_primary-transact-sql"></a>log_shipping_monitor_primary (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  在每個記錄傳送組態中，儲存每個主要資料庫各一項監視記錄。 此資料表儲存在 **msdb** 資料庫中。  
  
 主要伺服器和次要伺服器也會使用記錄和監視的相關資料表。   
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**primary_id**|**uniqueidentifier**|記錄傳送組態之主要資料庫的識別碼。|  
|**primary_server**|**sysname**|記錄傳送組態中之 [!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 主要執行個體的名稱。|  
|**primary_database**|**sysname**|記錄傳送組態中之主要資料庫的名稱。|  
|**backup_threshold**|**int**|在產生警示之前，備份作業之間所能經歷的時間 (以分鐘為單位)。|  
|**threshold_alert**|**int**|當超出備份臨界值時，所產生的警示。|  
|**threshold_alert_enabled**|**bit**|決定是否啟用備份臨界值警示。 1 = 已啟用。<br /><br /> 0 = 已停用。|  
|**last_backup_file**|**Nvarchar (500)**|最近之交易記錄備份的絕對路徑。|  
|**last_backup_date**|**datetime**|在主要資料庫中，前一個交易記錄備份作業的日期和時間。|  
|**last_backup_date_utc**|**datetime**|在主要資料庫中，前一個交易記錄備份作業的日期和時間 (以國際標準時間 (UTC) 為單位)。|  
|**history_retention_period**|**int**|給定主要資料庫的記錄傳送記錄，在刪除之前所保留的時間 (以分鐘為單位)。|  
  
## <a name="remarks"></a>備註  
 除了儲存在遠端監視伺服器之外，主伺服器的相關資訊也會儲存在主伺服器的 **log_shipping_monitor_primary** 資料表中。  
  
## <a name="see-also"></a>另請參閱  
 [關於記錄傳送 &#40;SQL Server&#41;](../../database-engine/log-shipping/about-log-shipping-sql-server.md)   
 [sp_add_log_shipping_primary_database &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-add-log-shipping-primary-database-transact-sql.md)   
 [sp_change_log_shipping_primary_database &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-change-log-shipping-primary-database-transact-sql.md)   
 [sp_delete_log_shipping_primary_database &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-delete-log-shipping-primary-database-transact-sql.md)   
 [sp_help_log_shipping_primary_database &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-help-log-shipping-primary-database-transact-sql.md)   
 [sp_refresh_log_shipping_monitor &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-refresh-log-shipping-monitor-transact-sql.md)   
 [sp_help_log_shipping_monitor_primary &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-help-log-shipping-monitor-primary-transact-sql.md)   
 [sp_delete_log_shipping_alert_job &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-delete-log-shipping-alert-job-transact-sql.md)   
 [系統資料表 &#40;Transact-SQL&#41;](../../relational-databases/system-tables/system-tables-transact-sql.md)  
  
  
