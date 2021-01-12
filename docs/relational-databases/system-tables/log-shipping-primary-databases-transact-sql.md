---
description: log_shipping_primary_databases (Transact-SQL)
title: log_shipping_primary_databases (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- log_shipping_primary_databases
- log_shipping_primary_databases_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- log_shipping_primary_databases system table
ms.assetid: 56888756-a798-42be-9b5e-0f9aa05a2cc6
author: cawrites
ms.author: chadam
ms.openlocfilehash: 8dd97d1baa37b71fec2e1ceb113e4b0a3614a2e4
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98102131"
---
# <a name="log_shipping_primary_databases-transact-sql"></a>log_shipping_primary_databases (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  將主要資料庫的一項記錄儲存在記錄傳送組態中。 此資料表儲存在 **msdb** 資料庫中。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**primary_id**|**uniqueidentifier**|記錄傳送組態之主要資料庫的識別碼。|  
|**primary_database**|**sysname**|記錄傳送組態中之主要資料庫的名稱。|  
|**backup_directory**|**Nvarchar (500)**|用於儲存主要伺服器之交易記錄備份檔的目錄。|  
|**backup_share**|**Nvarchar (500)**|備份目錄的網路或 UNC 路徑。|  
|**backup_retention_period**|**int**|在刪除記錄備份檔之前，將它保留在備份目錄中的時間長度 (以分鐘為單位)。|  
|**backup_job_id**|**uniqueidentifier**|與主要伺服器上之備份作業相關聯的 [!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent 作業識別碼。|  
|**monitor_server**|**sysname**|在記錄傳送組態中，用於做為監視伺服器之 [!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 執行個體的名稱。|  
|**monitor_server_security_mode**|**bit**|用於連接到監視伺服器的安全性模式。<br /><br /> 1 = Windows 驗證。<br /><br /> 0 = [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 驗證。|  
|**last_backup_file**|**Nvarchar (500)**|最近之交易記錄備份的絕對路徑。|  
|**last_backup_date**|**datetime**|最後一項記錄備份作業的日期和時間。|  
|**user_specified_monitor**|**bit**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]<br /><br /> **sp_help_log_shipping_primary_database** 和 **sp_help_log_shipping_secondary_primary** 使用此資料行來控制中的監視設定顯示 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 。<br /><br /> 0 = 當叫用這兩個預存程式中的任一個時，使用者並未指定 **\@ monitor_server** 參數的明確值。<br /><br /> 1 = 使用者已指定明確值。|  
|**backup_compression**|**tinyint**|指出記錄傳送組態是否會覆寫伺服器層級的備份壓縮行為。<br /><br /> 0 = 已停用。 不論伺服器設定的備份壓縮設定為何，永遠都不會壓縮記錄備份。<br /><br /> 1 = 已啟用。 不論伺服器設定的備份壓縮設定為何，永遠都會壓縮記錄備份。<br /><br /> 2 = 使用視圖的伺服器設定， [或設定備份壓縮預設伺服器設定選項伺服器設定](../../database-engine/configure-windows/view-or-configure-the-backup-compression-default-server-configuration-option.md) 選項。 這是預設值。<br /><br /> 只有 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Enterprise Edition 才支援備份壓縮。|  
  
## <a name="see-also"></a>另請參閱  
 [關於記錄傳送 &#40;SQL Server&#41;](../../database-engine/log-shipping/about-log-shipping-sql-server.md)   
 [sp_add_log_shipping_primary_database &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-add-log-shipping-primary-database-transact-sql.md)   
 [sp_delete_log_shipping_primary_database &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-delete-log-shipping-primary-database-transact-sql.md)   
 [sp_help_log_shipping_primary_database &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-help-log-shipping-primary-database-transact-sql.md)   
 [系統資料表 &#40;Transact-SQL&#41;](../../relational-databases/system-tables/system-tables-transact-sql.md)  
  
  
