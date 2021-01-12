---
description: backupfilegroup (Transact-SQL)
title: backupfilegroup (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- backupfilegroup_TSQL
- backupfilegroup
dev_langs:
- TSQL
helpviewer_keywords:
- filegroups [SQL Server], backupfilegroup system table
- backupfilegroup system table
ms.assetid: d26e8fbe-f5c5-4e10-b2bd-0d8e16ea21f9
author: cawrites
ms.author: chadam
ms.openlocfilehash: cd2f3ac634f737772691c3fdb7a1d4ab4d736123
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98102738"
---
# <a name="backupfilegroup-transact-sql"></a>backupfilegroup (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  針對備份時在資料庫中的每個檔案群組，各包含一個資料列。 **backupfilegroup** 儲存在 **msdb** 資料庫中。  
  
> [!NOTE]  
>  **Backupfilegroup** 資料表會顯示資料庫的檔案群組設定，而不是備份組的檔案群組設定。 若要識別檔案是否包含在備份組內，請使用 [backupfile](../../relational-databases/system-tables/backupfile-transact-sql.md)資料表的 **is_present** 資料行。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**backup_set_id**|**int**|包含這個檔案群組的備份組。|  
|**name**|**sysname**|檔案群組的名稱。|  
|**filegroup_id**|**int**|檔案群組的識別碼，它在資料庫中是唯一的。 對應至 **sys.** 檔案群組中的 **data_space_id** 。|  
|**filegroup_guid**|**uniqueidentifier**|檔案群組的全域唯一識別碼。 可以是 NULL。|  
|**type**|**char(2)**|這是內容類型，它有下列幾種：<br /><br /> FG = "Rows" 檔案群組<br /><br /> SL = [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 記錄檔案群組|  
|**type_desc**|**nvarchar(60)**|這是函數類型的描述，它有下列幾種：<br /><br /> ROWS_FILEGROUP<br /><br /> SQL_LOG_FILEGROUP |  
|**is_default**|**bit**|在 CREATE TABLE 或 CREATE INDEX 中未指定檔案群組時，所使用的預設檔案群組。|  
|**is_readonly**|**bit**|1 = 檔案群組是唯讀的。|  
|**log_filegroup_guid**|**uniqueidentifier**|可以是 NULL。|  
  
## <a name="remarks"></a>備註  
  
> [!IMPORTANT]  
>  相同的檔案群組名稱可以出現在不同的資料庫中；每個檔案群組都有它自己的 GUID。 因此， **(backup_set_id，filegroup_guid)** 是在 **backupfilegroup** 中識別檔案群組的唯一索引鍵。  
  
 從 *BACKUP_DEVICE* RESTORE VERIFYONLY with LOADHISTORY 會在 **backupmediaset** 資料表的資料行中填入來自媒體集標頭的適當值。  
  
 若要減少此資料表以及其他備份和記錄資料表中的資料列數目，請執行 [sp_delete_backuphistory](../../relational-databases/system-stored-procedures/sp-delete-backuphistory-transact-sql.md) 預存程式。  
  
## <a name="see-also"></a>另請參閱  
 [備份與還原資料表 &#40;Transact-sql&#41;](../../relational-databases/system-tables/backup-and-restore-tables-transact-sql.md)   
 [backupfile &#40;Transact-SQL&#41;](../../relational-databases/system-tables/backupfile-transact-sql.md)   
 [backupmediafamily &#40;Transact-SQL&#41;](../../relational-databases/system-tables/backupmediafamily-transact-sql.md)   
 [backupmediaset &#40;Transact-SQL&#41;](../../relational-databases/system-tables/backupmediaset-transact-sql.md)   
 [backupset &#40;Transact-SQL&#41;](../../relational-databases/system-tables/backupset-transact-sql.md)   
 [系統資料表 &#40;Transact-SQL&#41;](../../relational-databases/system-tables/system-tables-transact-sql.md)  
  
  
