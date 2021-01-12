---
description: sysmail_mailattachments (Transact-SQL)
title: sysmail_mailattachments (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/06/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sysmail_mailattachments_TSQL
- sysmail_mailattachments
dev_langs:
- TSQL
helpviewer_keywords:
- sysmail_mailattachments database mail view
ms.assetid: aee87059-a4c1-459a-a95c-641b4e3f0e73
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 9fe55316cc27c21849379afe400d2950a5bcf882
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98100124"
---
# <a name="sysmail_mailattachments-transact-sql"></a>sysmail_mailattachments (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  針對每個提交到 Database Mail 的附加檔案，各包含一個資料列。 當您想要 Database Mail 附加檔案的相關資訊時，請使用這份檢視。 若要檢查 Database Mail 使用 [sysmail_allitems &#40;transact-sql&#41;](../../relational-databases/system-catalog-views/sysmail-allitems-transact-sql.md)所處理的所有電子郵件。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**attachment_id**|**int**|附加檔案的識別碼。|  
|**mailitem_id**|**int**|包含附加檔案之郵件項目的識別碼。|  
|**filename**|**Nvarchar (520)**|附加檔案的檔案名稱。 當 **attach_query_result** 為1且 **query_attachment_filename** 為 Null 時，Database Mail 會建立任意的檔案名。|  
|**filesize**|**int**|附加檔案的大小 (以位元組為單位)。|  
|**附件**|**varbinary(max)**|附加檔案的內容。|  
|**last_mod_date**|**datetime**|資料列上次修改的日期和時間。|  
|**last_mod_user**|**sysname**|上次修改資料列的使用者。|  
  
## <a name="remarks"></a>備註  
 對 Database Mail 進行疑難排解時，請使用這個檢視來查看附加檔案的屬性。  
  
 儲存在系統資料表中的附件可能會導致 **msdb** 資料庫成長。 使用 **sysmail_delete_mailitems_sp** 刪除訊息項目及其相關聯的附件。 如需詳細資訊，請參閱 [建立 SQL Server Agent 作業以封存 Database Mail 訊息和事件記錄](../../relational-databases/database-mail/create-a-sql-server-agent-job-to-archive-database-mail-messages-and-event-logs.md)檔。  
  
## <a name="permissions"></a>權限  
 授與 **系統管理員（sysadmin** ）固定伺服器角色和 **DatabaseMailUserRole** 資料庫角色。 由 **系統管理員（sysadmin** ）固定伺服器角色的成員執行時，此視圖會顯示所有附件。 所有其他使用者只看得到他們所提交訊息的附加檔案。  
  
## <a name="see-also"></a>另請參閱  
 [sysmail_allitems &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sysmail-allitems-transact-sql.md)   
 [sysmail_faileditems &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sysmail-faileditems-transact-sql.md)   
 [sysmail_sentitems &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sysmail-sentitems-transact-sql.md)   
 [sysmail_unsentitems &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sysmail-unsentitems-transact-sql.md)   
 [sysmail_event_log &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sysmail-event-log-transact-sql.md)  
  
  
