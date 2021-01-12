---
title: backupmediaset (Transact-sql) |Microsoft Docs
description: Backupmediaset 的參考，其中包含每個備份媒體集的一個資料列。
ms.custom: ''
ms.date: 08/09/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- backupmediaset
- backupmediaset_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- backup media [SQL Server], backupmediaset system table
- backupmediaset system table
author: cawrites
ms.author: chadam
ms.openlocfilehash: 10b4d744e7ef4e0d11a9788580ea7f8c5a67bd1f
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98091572"
---
# <a name="backupmediaset-transact-sql"></a>backupmediaset (Transact-SQL)

[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

每個備份組各含一個資料列。 此資料表儲存在 **msdb** 資料庫中。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**media_set_id**|**int**|唯一媒體集識別碼。 識別，主索引鍵。|  
|**media_uuid**|**uniqueidentifier**|媒體集的 UUID。 所有 [!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 媒體集都有 UUID。<br /><br /> 但是在舊版的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中，如果媒體集只包含一個媒體家族， **media_uuid** 的資料行可能會是 Null (**media_family_count** 是 1) 。|  
|**media_family_count**|**tinyint**|媒體集中的媒體家族數目。 可以是 NULL。|  
|**name**|**nvarchar(128)**|媒體集的名稱。 可以是 NULL。<br /><br /> 如需詳細資訊，請參閱 [BACKUP &#40;transact-sql&#41;](../../t-sql/statements/backup-transact-sql.md)中的 MEDIANAME 和 MEDIADESCRIPTION。|  
|**description**|**nvarchar(255)**|媒體集的文字描述。 可以是 NULL。<br /><br /> 如需詳細資訊，請參閱 [BACKUP &#40;transact-sql&#41;](../../t-sql/statements/backup-transact-sql.md)中的 MEDIANAME 和 MEDIADESCRIPTION。|  
|**software_name**|**nvarchar(128)**|寫入媒體標籤的備份軟體名稱。 可以是 NULL。|  
|**software_vendor_id**|**int**|寫入備份媒體標籤的軟體供應商識別碼。 可以是 NULL。<br /><br /> 的值 [!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 是十六進位0x1200。|  
|**MTF_major_version**|**tinyint**|用來產生這個媒體集之 [!INCLUDE[msCoName](../../includes/msconame-md.md)] Tape Format 的主要版本號碼。 可以是 NULL。|  
|**mirror_count**|**tinyint**|媒體集中的鏡像數目。|  
|**is_password_protected**|**bit**|這是指媒體集是否有密碼保護：<br /><br /> 0 = 無保護<br /><br /> 1 = 保護|  
|**is_compressed**|**bit**|備份是否經過壓縮：<br /><br /> 0 = 未壓縮<br /><br /> 1 = 已壓縮<br /><br /> 在 **msdb** 升級期間，這個值會設定為 Null。 這表示非壓縮的備份。|  
|**is_encrypted**|**bit**|備份是否經過加密：<br /><br /> 0 = 未加密<br /><br /> 1 = 已加密|  
  
## <a name="remarks"></a>備註  
 從 *BACKUP_DEVICE* RESTORE VERIFYONLY with LOADHISTORY 會在 **backupmediaset** 資料表的資料行中填入來自媒體集標頭的適當值。  
  
 若要減少此資料表以及其他備份和記錄資料表中的資料列數目，請執行 [sp_delete_backuphistory](../../relational-databases/system-stored-procedures/sp-delete-backuphistory-transact-sql.md) 預存程式。  
  
## <a name="see-also"></a>另請參閱  
 [備份與還原資料表 &#40;Transact-sql&#41;](../../relational-databases/system-tables/backup-and-restore-tables-transact-sql.md)   
 [backupfile &#40;Transact-SQL&#41;](../../relational-databases/system-tables/backupfile-transact-sql.md)   
 [backupfilegroup &#40;Transact-SQL&#41;](../../relational-databases/system-tables/backupfilegroup-transact-sql.md)   
 [backupmediafamily &#40;Transact-SQL&#41;](../../relational-databases/system-tables/backupmediafamily-transact-sql.md)   
 [backupset &#40;Transact-SQL&#41;](../../relational-databases/system-tables/backupset-transact-sql.md)   
 [系統資料表 &#40;Transact-SQL&#41;](../../relational-databases/system-tables/system-tables-transact-sql.md)  
  
  
