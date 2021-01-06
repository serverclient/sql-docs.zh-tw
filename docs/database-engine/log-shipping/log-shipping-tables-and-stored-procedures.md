---
title: 記錄傳送資料表與預存程序 | Microsoft Docs
description: 檢閱所有與記錄傳送設定建立關聯的資料表和預存程序。 每個伺服器都會將記錄傳送資料表儲存在 msdb 中。
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.reviewer: ''
ms.technology: log-shipping
ms.topic: conceptual
helpviewer_keywords:
- secondary servers [SQL Server]
- monitor servers [SQL Server]
- log shipping [SQL Server], system tables
- log shipping [SQL Server], stored procedures
- primary servers [SQL Server]
ms.assetid: 03420810-4c38-4c0c-adf0-913eb044c50a
author: cawrites
ms.author: chadam
ms.openlocfilehash: 93990b05d791fab8edb1d7e194f5c70cb448db3e
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97643870"
---
# <a name="log-shipping-tables-and-stored-procedures"></a>Log Shipping Tables and Stored Procedures
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
  此主題描述與記錄傳送設定關聯的所有資料表與預存程序。 所有記錄傳送資料表都儲存在每部伺服器上的 **msdb** 中。 下表描述在記錄傳送設定中，會在哪部伺服器上使用哪些資料表與預存程序。  
  
## <a name="primary-server-tables"></a>主要伺服器資料表  
  
|Table|描述|  
|-----------|-----------------|  
|[log_shipping_monitor_alert](../../relational-databases/system-tables/log-shipping-monitor-alert-transact-sql.md)|儲存警示作業識別碼。 若未設定遠端監視伺服器，則此資料表只會用於主要伺服器。|  
|[log_shipping_monitor_error_detail](../../relational-databases/system-tables/log-shipping-monitor-error-detail-transact-sql.md)|儲存與此主要伺服器關聯之記錄傳送作業的錯誤詳細資料。|  
|[log_shipping_monitor_history_detail](../../relational-databases/system-tables/log-shipping-monitor-history-detail-transact-sql.md)|儲存與此主要伺服器關聯之記錄傳送作業的記錄詳細資料。|  
|[log_shipping_monitor_primary](../../relational-databases/system-tables/log-shipping-monitor-primary-transact-sql.md)|儲存此主要資料庫的一筆監視記錄。|  
|[log_shipping_primary_databases](../../relational-databases/system-tables/log-shipping-primary-databases-transact-sql.md)|包含特定伺服器上主要資料庫的組態資訊。 每個主要資料庫儲存一列。|  
|[log_shipping_primary_secondaries](../../relational-databases/system-tables/log-shipping-primary-secondaries-transact-sql.md)|對應主要資料庫到次要資料庫。|  
  
## <a name="primary-server-stored-procedures"></a>主要伺服器預存程序  
  
|預存程序|描述|  
|----------------------|-----------------|  
|[sp_add_log_shipping_primary_database](../../relational-databases/system-stored-procedures/sp-add-log-shipping-primary-database-transact-sql.md)|設定記錄傳送組態的主要資料庫，其中包括備份作業、本機監視記錄，以及遠端監視記錄。|  
|[sp_add_log_shipping_primary_secondary](../../relational-databases/system-stored-procedures/sp-add-log-shipping-primary-secondary-transact-sql.md)|新增次要資料庫名稱到現有主要資料庫。|  
|[sp_change_log_shipping_primary_database](../../relational-databases/system-stored-procedures/sp-change-log-shipping-primary-database-transact-sql.md)|變更主要資料庫設定，包括本機與遠端監視記錄。|  
|[sp_cleanup_log_shipping_history](../../relational-databases/system-stored-procedures/sp-cleanup-log-shipping-history-transact-sql.md)|根據保留期限，在本機和監視器上清除記錄。|  
|[sp_delete_log_shipping_primary_database](../../relational-databases/system-stored-procedures/sp-delete-log-shipping-primary-database-transact-sql.md)|移除主要資料庫的記錄傳送，包括備份作業，以及本機與遠端記錄。|  
|[sp_delete_log_shipping_primary_secondary](../../relational-databases/system-stored-procedures/sp-delete-log-shipping-primary-secondary-transact-sql.md)|從主要資料庫移除次要資料庫名稱。|  
|[sp_help_log_shipping_primary_database](../../relational-databases/system-stored-procedures/sp-help-log-shipping-primary-database-transact-sql.md)|從 **log_shipping_primary_databases** 和 **log_shipping_monitor_primary** 資料表擷取主要資料庫設定然後顯示值。|  
|[sp_help_log_shipping_primary_secondary](../../relational-databases/system-stored-procedures/sp-help-log-shipping-primary-secondary-transact-sql.md)|擷取主要資料庫的次要資料庫名稱。|  
|[sp_refresh_log_shipping_monitor](../../relational-databases/system-stored-procedures/sp-refresh-log-shipping-monitor-transact-sql.md)|替指定的記錄傳送代理程式以最新資訊重新整理監視器。|  
  
## <a name="secondary-server-tables"></a>次要伺服器資料表  
  
|Table|描述|  
|-----------|-----------------|  
|[log_shipping_monitor_alert](../../relational-databases/system-tables/log-shipping-monitor-alert-transact-sql.md)|儲存警示作業識別碼。 若未設定遠端監視伺服器，則此資料表只會用於次要伺服器。|  
|[log_shipping_monitor_error_detail](../../relational-databases/system-tables/log-shipping-monitor-error-detail-transact-sql.md)|儲存與此次要伺服器關聯之記錄傳送作業的錯誤詳細資料。|  
|[log_shipping_monitor_history_detail](../../relational-databases/system-tables/log-shipping-monitor-history-detail-transact-sql.md)|儲存與此次要伺服器關聯之記錄傳送作業的記錄詳細資料。|  
|[log_shipping_monitor_secondary](../../relational-databases/system-tables/log-shipping-monitor-secondary-transact-sql.md)|儲存與此次要伺服器關聯之次要資料庫的一筆監視記錄。|  
|[log_shipping_secondary](../../relational-databases/system-tables/log-shipping-secondary-transact-sql.md)|包含特定伺服器上次要資料庫的組態資訊。 每個次要識別碼儲存一列。|  
|[log_shipping_secondary_databases](../../relational-databases/system-tables/log-shipping-secondary-databases-transact-sql.md)|儲存特定次要資料庫的組態資訊。 每個次要資料庫儲存一列。|  
  
> [!NOTE]  
>  與特定主要資料庫位於相同次要伺服器上的次要資料庫，會共用 **log_shipping_secondary** 資料表中的設定。 若針對一個次要資料庫變更共用設定，則對於其他資料庫而言該設定也會變更。  
  
## <a name="secondary-server-stored-procedures"></a>次要伺服器預存程序  
  
|預存程序|描述|  
|----------------------|-----------------|  
|[sp_add_log_shipping_secondary_database](../../relational-databases/system-stored-procedures/sp-add-log-shipping-secondary-database-transact-sql.md)|設定次要資料庫以進行記錄傳送。|  
|[sp_add_log_shipping_secondary_primary](../../relational-databases/system-stored-procedures/sp-add-log-shipping-secondary-primary-transact-sql.md)|設定主要資訊、加入本機和遠端監視器連結，以及在次要伺服器上建立所指定主要資料庫的複製和還原作業。|  
|[sp_change_log_shipping_secondary_database](../../relational-databases/system-stored-procedures/sp-change-log-shipping-secondary-database-transact-sql.md)|變更次要資料庫設定，包括本機與遠端監視記錄。|  
|[sp_change_log_shipping_secondary_primary](../../relational-databases/system-stored-procedures/sp-change-log-shipping-secondary-primary-transact-sql.md)|變更次要資料庫，例如：來源和目的地目錄，以及檔案保留期限。|  
|[sp_cleanup_log_shipping_history](../../relational-databases/system-stored-procedures/sp-cleanup-log-shipping-history-transact-sql.md)|根據保留期限，在本機和監視器上清除記錄。|  
|[sp_delete_log_shipping_secondary_database](../../relational-databases/system-stored-procedures/sp-delete-log-shipping-secondary-database-transact-sql.md)|移除次要資料庫，以及本機記錄和遠端記錄。|  
|[sp_delete_log_shipping_secondary_primary](../../relational-databases/system-stored-procedures/sp-delete-log-shipping-secondary-primary-transact-sql.md)|從次要伺服器移除與指定之主要伺服器相關的資訊。|  
|[sp_help_log_shipping_secondary_database](../../relational-databases/system-stored-procedures/sp-help-log-shipping-secondary-database-transact-sql.md)|從 **log_shipping_secondary**、 **log_shipping_secondary_databases** 和 **log_shipping_monitor_secondary** 資料表擷取次要資料庫的設定。|  
|[sp_help_log_shipping_secondary_primary](../../relational-databases/system-stored-procedures/sp-help-log-shipping-secondary-primary-transact-sql.md)|這個預存程序會擷取次要伺服器上所指定主要資料庫的設定。|  
|[sp_refresh_log_shipping_monitor](../../relational-databases/system-stored-procedures/sp-refresh-log-shipping-monitor-transact-sql.md)|替指定的記錄傳送代理程式以最新資訊重新整理監視器。|  
  
## <a name="monitor-server-tables"></a>監視伺服器資料表  
  
|Table|描述|  
|-----------|-----------------|  
|[log_shipping_monitor_alert](../../relational-databases/system-tables/log-shipping-monitor-alert-transact-sql.md)|儲存警示作業識別碼。|  
|[log_shipping_monitor_error_detail](../../relational-databases/system-tables/log-shipping-monitor-error-detail-transact-sql.md)|儲存記錄傳送作業的錯誤詳細資料。|  
|[log_shipping_monitor_history_detail](../../relational-databases/system-tables/log-shipping-monitor-history-detail-transact-sql.md)|儲存記錄傳送作業的記錄詳細資料。|  
|[log_shipping_monitor_primary](../../relational-databases/system-tables/log-shipping-monitor-primary-transact-sql.md)|儲存與此監視伺服器關聯之主要資料庫的一筆監視記錄。|  
|[log_shipping_monitor_secondary](../../relational-databases/system-tables/log-shipping-monitor-secondary-transact-sql.md)|儲存與此監視伺服器關聯之次要資料庫的一筆監視記錄。|  
  
## <a name="monitor-server-stored-procedures"></a>監視伺服器預存程序  
  
|預存程序|描述|  
|----------------------|-----------------|  
|[sp_add_log_shipping_alert_job](../../relational-databases/system-stored-procedures/sp-add-log-shipping-alert-job-transact-sql.md)|建立記錄傳送警示作業 (若尚未建立)。|  
|[sp_delete_log_shipping_alert_job](../../relational-databases/system-stored-procedures/sp-delete-log-shipping-alert-job-transact-sql.md)|移除記錄傳送警示作業 (若沒有關聯的主要資料庫)。|  
|[sp_help_log_shipping_alert_job](../../relational-databases/system-stored-procedures/sp-help-log-shipping-alert-job-transact-sql.md)|傳回警示作業的作業識別碼。|  
|[sp_help_log_shipping_monitor_primary](../../relational-databases/system-stored-procedures/sp-help-log-shipping-monitor-primary-transact-sql.md)|從 **log_shipping_monitor_primary** 資料表傳回指定的主要資料庫的監視記錄。|  
|[sp_help_log_shipping_monitor_secondary](../../relational-databases/system-stored-procedures/sp-help-log-shipping-monitor-secondary-transact-sql.md)|從 **log_shipping_monitor_secondary** 資料表傳回指定的次要資料庫的監視記錄。|  
  
  
