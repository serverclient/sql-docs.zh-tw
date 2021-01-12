---
description: restorefile (Transact-SQL)
title: restorefile (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/03/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- restorefile
- restorefile_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- restorefile system table
- restoring files [SQL Server], restorefile system table
- file restores [SQL Server], restorefile system table
ms.assetid: 8e40145a-8559-4abe-8e2a-39b818928009
author: cawrites
ms.author: chadam
ms.openlocfilehash: a11f4e9e96a12b0c93c9812bde8422e17667d8ad
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98099591"
---
# <a name="restorefile-transact-sql"></a>restorefile (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  針對每個還原的檔案，各包含一個資料列，其中包括檔案群組名稱所間接還原的檔案。 此資料表儲存在 **msdb** 資料庫中。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**restore_history_id**|**int**|用來識別對應的還原作業的唯一識別碼。 參考 **restorehistory (restore_history_id)**。|  
|**file_number**|**數值 (10，0)**|還原檔案的檔案識別碼。 每個資料庫內這個號碼必須唯一。 可以是 NULL。<br /><br /> 當資料庫還原為資料庫快照集時，會依照完整還原的相同方式來擴展這個值。|  
|**destination_phys_drive**|**nvarchar(260)**|將檔案還原到其中的磁碟機或分割區。 可以是 NULL。<br /><br /> 當資料庫還原為資料庫快照集時，會依照完整還原的相同方式來擴展這個值。|  
|**destination_phys_name**|**nvarchar(260)**|檔案還原到其中的檔案名稱，不含磁碟機或分割區資訊。 可以是 NULL。<br /><br /> 當資料庫還原為資料庫快照集時，會依照完整還原的相同方式來擴展這個值。|  
  
## <a name="remarks"></a>備註  
 若要減少此資料表以及其他備份和記錄資料表中的資料列數目，請執行 [sp_delete_backuphistory](../../relational-databases/system-stored-procedures/sp-delete-backuphistory-transact-sql.md) 預存程式。  
  
## <a name="see-also"></a>另請參閱  
 [備份與還原資料表 &#40;Transact-sql&#41;](../../relational-databases/system-tables/backup-and-restore-tables-transact-sql.md)   
 [restorefilegroup &#40;Transact-sql&#41;](../../relational-databases/system-tables/restorefilegroup-transact-sql.md)   
 [restorehistory &#40;Transact-sql&#41;](../../relational-databases/system-tables/restorehistory-transact-sql.md)   
 [系統資料表 &#40;Transact-SQL&#41;](../../relational-databases/system-tables/system-tables-transact-sql.md)  
  
  
