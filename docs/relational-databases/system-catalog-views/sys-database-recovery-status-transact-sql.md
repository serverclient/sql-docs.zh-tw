---
description: sys.database_recovery_status (Transact-SQL)
title: sys.database_recovery_status (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 08/12/2016
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- database_recovery_status_TSQL
- database_recovery_status
- sys.database_recovery_status
- sys.database_recovery_status_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.database_recovery_status catalog view
ms.assetid: 46fab234-1542-49be-8edf-aa101e728acf
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 5e9c968ff33c47f81eb84b2f5cd73e2483b7af56
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98098036"
---
# <a name="sysdatabase_recovery_status-transact-sql"></a>sys.database_recovery_status (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  針對每個資料庫，各包含一個資料列。 如果資料庫尚未開啟，[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 會嘗試啟動它。  
  
 若要查看 **master** 或 **tempdb** 以外的資料庫資料列，必須套用下列其中一項：  
  
-   您是資料庫的擁有者。  
  
-   您有 ALTER ANY DATABASE 或 VIEW ANY DATABASE 伺服器層級權限。  
  
-   具有 **master** 資料庫的 CREATE DATABASE 許可權。    
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**database_id**|**int**|資料庫的識別碼，在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的執行個體內是唯一的。|  
|**database_guid**|**uniqueidentifier**|用來將資料庫的所有資料庫檔案關聯起來。 所有檔案的標頭頁都必須有這個 GUID，資料庫才能依照預期來啟動。 應該只有一個資料庫有這個 GUID，但您可以複製和附加資料庫來建立複本。 當您還原不存在的資料庫時，RESTORE 一律會產生新的 GUID。<br /><br /> NULL= 資料庫離線，或不啟動資料庫。|  
|**family_guid**|**uniqueidentifier**|偵測相符還原狀態的資料庫「備份系列」的識別碼。<br /><br /> NULL= 資料庫離線，或不啟動資料庫。|  
|**last_log_backup_lsn**|**numeric(25,0)**|下一個記錄備份的起始記錄序號。<br /><br /> 如果是 Null，就無法執行交易記錄備份，因為資料庫處於簡單復原或沒有目前的資料庫備份。|  
|**recovery_fork_guid**|**uniqueidentifier**|識別目前在使用資料庫的目前復原分岔。<br /><br /> NULL= 資料庫離線，或不啟動資料庫。|  
|**first_recovery_fork_guid**|**uniqueidentifier**|起始復原分岔的識別碼。<br /><br /> NULL= 資料庫離線，或不啟動資料庫。|  
|**fork_point_lsn**|**numeric(25,0)**|如果 **first_recovery_fork_guid** 不等於 **recovery_fork_guid** 的)  (！ =，則 **fork_point_lsn** 是目前分叉點的記錄序號。 否則，這個值是 NULL。|  
  
## <a name="permissions"></a>權限  
 [!INCLUDE[ssCatViewPerm](../../includes/sscatviewperm-md.md)] 如需相關資訊，請參閱 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)。  
  
## <a name="see-also"></a>另請參閱  
 [目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/catalog-views-transact-sql.md)   
 [資料庫和檔案目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/databases-and-files-catalog-views-transact-sql.md)   
 [RESTORE HEADERONLY &#40;Transact-SQL&#41;](../../t-sql/statements/restore-statements-headeronly-transact-sql.md)   
 [查詢 SQL Server 系統目錄 FAQ](../../relational-databases/system-catalog-views/querying-the-sql-server-system-catalog-faq.md)  
  
  
