---
description: restorehistory (Transact-SQL)
title: restorehistory (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/03/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- restorehistory
- restorehistory_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- restorehistory system table
ms.assetid: 9140ecc1-d912-4d76-ae70-e2a857da6d44
author: cawrites
ms.author: chadam
ms.openlocfilehash: c4d164bdceaea91650a0d1bd8226133b22692912
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98096156"
---
# <a name="restorehistory-transact-sql"></a>restorehistory (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  每項還原作業都有一個資料列。 此資料表儲存在 **msdb** 資料庫中。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**restore_history_id**|**int**|用來識別每項還原作業的唯一識別碼。 識別，主索引鍵。|  
|**restore_date**|**datetime**|還原作業開始的日期和時間。 可以是 NULL。|  
|**destination_database_name**|**nvarchar(128)**|還原作業的目的地資料庫名稱。 可以是 NULL。|  
|**user_name**|**nvarchar(128)**|執行還原作業的使用者名稱。 可以是 NULL。|  
|**backup_set_id**|**int**|用來識別還原的備份組的唯一識別碼。 參考 **backupset (backup_set_id)**。|  
|**restore_type**|**char(1)**|還原作業的類型：<br /><br /> D = 資料庫<br /><br /> F = 檔案<br /><br /> G = 檔案群組<br /><br /> I = 差異<br /><br /> L = 記錄<br /><br /> V = 僅驗證<br /><br /> 可以是 NULL。|  
|**replace**|**bit**|指出還原作業是否指定了 REPLACE 選項：<br /><br /> 1 = 已指定<br /><br /> 0 = 未指定<br /><br /> 可以是 NULL。<br /><br /> 當資料庫還原到某個資料庫快照集時，0 是唯一選項。|  
|**recovery**|**bit**|指出還原作業指定了 RECOVERY 或 NORECOVERY 選項：<br /><br /> 1 = RECOVERY<br /><br /> 可以是 NULL。<br /><br /> 當資料庫還原為資料庫快照集時，1是唯一的選項。<br /><br /> 0 = NORECOVERY|  
|**restart**|**bit**|指出還原作業是否指定了 RESTART 選項：<br /><br /> 1 = 已指定<br /><br /> 0 = 未指定<br /><br /> 可以是 NULL。<br /><br /> 當資料庫還原到某個資料庫快照集時，0 是唯一選項。|  
|**stop_at**|**datetime**|復原資料庫的時間點。 可以是 NULL。|  
|**device_count**|**tinyint**|還原作業所涉及的裝置數目。 這個數目可以小於備份媒體家族的數目。 可以是 NULL。<br /><br /> 當資料庫還原到某個資料庫快照集時，這個數目一律是 1。|  
|**stop_at_mark_name**|**nvarchar(128)**|指出復原到包含具名標示的交易。 可以是 NULL。<br /><br /> 當資料庫還原到某個資料庫快照集時，這個值是 NULL。|  
|**stop_before**|**bit**|指出復原是否包含具名標示的交易：<br /><br /> 0 = 復原暫停在標示交易之前。<br /><br /> 1 = 復原包括標示的交易。<br /><br /> 可以是 NULL。<br /><br /> 當資料庫還原到某個資料庫快照集時，這個值是 NULL。|  
  
## <a name="remarks"></a>備註  
 若要減少此資料表以及其他備份和記錄資料表中的資料列數目，請執行 [sp_delete_backuphistory](../../relational-databases/system-stored-procedures/sp-delete-backuphistory-transact-sql.md) 預存程式。  
  
## <a name="see-also"></a>另請參閱  
 [備份與還原資料表 &#40;Transact-sql&#41;](../../relational-databases/system-tables/backup-and-restore-tables-transact-sql.md)   
 [restorefile &#40;Transact-sql&#41;](../../relational-databases/system-tables/restorefile-transact-sql.md)   
 [restorefilegroup &#40;Transact-sql&#41;](../../relational-databases/system-tables/restorefilegroup-transact-sql.md)   
 [系統資料表 &#40;Transact-SQL&#41;](../../relational-databases/system-tables/system-tables-transact-sql.md)  
  
  
