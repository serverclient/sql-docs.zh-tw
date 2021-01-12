---
description: sysmail_event_log (Transact-SQL)
title: sysmail_event_log (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sysmail_event_log
- sysmail_event_log_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sysmail_event_log database mail view
ms.assetid: 440bc409-1188-4175-afc4-c68e31e44fed
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 58c8e734ed9acdef41b22772c8d3955b825a46de
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98096632"
---
# <a name="sysmail_event_log-transact-sql"></a>sysmail_event_log (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  針對每個由 Database Mail 系統傳回的 Windows 或 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 訊息，各包含一個資料列。 此內容中的 (訊息是指訊息（例如錯誤訊息），而不是電子郵件訊息。 ) 使用 Database Mail 設定 Wizard 的 [**設定系統參數**] 對話方塊或 [sysmail_configure_sp](../../relational-databases/system-stored-procedures/sysmail-configure-sp-transact-sql.md)預存程式來設定 **記錄層級** 參數，以判斷傳回的訊息。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**Log_id**|**int**|記錄中項目的識別碼。|  
|**event_type**|**Varchar (11)**|插入記錄中通知的類型。 可能的值有錯誤、警告、參考訊息、成功訊息和其他內部訊息。|  
|**log_date**|**datetime**|產生記錄項目的日期和時間。|  
|**description**|**nvarchar(max)**|記錄的訊息文字。|  
|**process_id**|**int**|Database Mail 外部程式的處理序識別碼。 這通常在每一次 Database Mail 外部程式啟動時都會變更。|  
|**mailitem_id**|**int**|郵件佇列中郵件項目的識別碼。 如果訊息與特定電子郵件項目無關，則為 NULL。|  
|**account_id**|**int**|與事件相關之帳戶的 **account_id** 。 如果訊息與特定帳戶無關，則為 NULL。|  
|**last_mod_date**|**datetime**|資料列上次修改的日期和時間。|  
|**last_mod_user**|**sysname**|上次修改資料列的使用者。 針對電子郵件，這是傳送郵件的使用者。 針對 Database Mail 外部程式產生的訊息，這是程式的使用者內容。|  
  
## <a name="remarks"></a>備註  
 針對 Database Mail 進行疑難排解時，請在 **sysmail_event_log** 視圖中搜尋電子郵件失敗的相關事件。 部分訊息 (如 Database Mail 外部程式失敗) 與特定電子郵件沒有關聯。 若要搜尋與特定電子郵件相關的錯誤，請在 **sysmail_faileditems** 視圖中查閱失敗電子郵件的 **mailitem_id** ，然後在 **sysmail_event_log** 搜尋與該 **mailitem_id** 相關的訊息。 當 **sp_send_dbmail** 傳回錯誤時，電子郵件不會提交至 Database Mail 系統，且此錯誤不會顯示在此視圖中。  
  
 當個別帳戶傳遞嘗試失敗時，Database Mail 會在重試嘗試期間保留錯誤訊息，直到郵件項目傳遞成功或失敗為止。 在終極成功的情況下，所有累積的錯誤都會記錄為個別的警告，包括 **account_id**。 即使電子郵件已傳送，這仍可能會使警告出現。 如果發生終極傳遞失敗，則所有先前的警告都會記錄為一則沒有 **account_id** 的錯誤訊息，因為所有帳戶都已失敗。  
  
## <a name="permissions"></a>權限  
 您必須是 **系統管理員（sysadmin** ）固定伺服器角色或 **DatabaseMailUserRole** 資料庫角色的成員，才能存取此視圖。 不是 **系統管理員（sysadmin** ）角色成員的 **DatabaseMailUserRole** 成員，只能看到他們所提交之電子郵件的事件。  
  
## <a name="see-also"></a>另請參閱  
 [sysmail_faileditems &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sysmail-faileditems-transact-sql.md)   
 [Database Mail 外部程式](../../relational-databases/database-mail/database-mail-external-program.md)  
  
  
