---
description: sysmail_faileditems (Transact-SQL)
title: sysmail_faileditems (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/06/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sysmail_faileditems
- sysmail_faileditems_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sysmail_faileditems database mail view
ms.assetid: a31562c5-358e-4cfc-a72d-b3faccc53851
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 0fc32200b893022e16bcd4ee7049da82c4572660
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98100121"
---
# <a name="sysmail_faileditems-transact-sql"></a>sysmail_faileditems (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  針對每個具有 **失敗** 狀態的 Database Mail 訊息，各包含一個資料列。 使用這份檢視可判斷哪些訊息未成功傳送。  
  
 若要查看 Database Mail 所處理的所有訊息，請使用 [sysmail_allitems &#40;transact-sql&#41;](../../relational-databases/system-catalog-views/sysmail-allitems-transact-sql.md)。 若只要查看未傳送的訊息，請使用 [sysmail_unsentitems &#40;transact-sql&#41;](../../relational-databases/system-catalog-views/sysmail-unsentitems-transact-sql.md)。 若只要查看已傳送的訊息，請使用 [sysmail_sentitems &#40;transact-sql&#41;](../../relational-databases/system-catalog-views/sysmail-sentitems-transact-sql.md)。 若要查看電子郵件附件，請使用 [sysmail_mailattachments &#40;transact-sql&#41;](../../relational-databases/system-catalog-views/sysmail-mailattachments-transact-sql.md)。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**mailitem_id**|**int**|郵件佇列中郵件項目的識別碼。|  
|**profile_id**|**int**|用來提交訊息之設定檔的識別碼。|  
|**收件者**|**varchar(max)**|訊息收件者的電子郵件地址。|  
|**copy_recipients**|**varchar(max)**|訊息副本收件者的電子郵件地址。|  
|**blind_copy_recipients**|**varchar(max)**|名稱未顯示於訊息標頭之訊息副本收件者的電子郵件地址。|  
|**subject**|**Nvarchar (510)**|訊息的主旨。|  
|**body**|**varchar(max)**|訊息的主體。|  
|**body_format**|**Varchar (20)**|訊息的主體格式。 可能的值是 TEXT 和 HTML。|  
|**importance**|**Varchar (6)**|訊息的 **重要性** 參數。|  
|**靈敏度**|**Varchar (12)**|訊息的 **敏感度** 參數。|  
|**file_attachments**|**varchar(max)**|附加至電子郵件訊息中的檔案名稱清單，用分號分隔各檔案名稱。|  
|**Attachment_encoding**|**Varchar (20)**|郵件附加檔案的類型。|  
|**查詢**|**varchar(max)**|郵件程式執行的查詢。|  
|**execute_query_database**|**sysname**|郵件程式執行查詢所在的資料庫內容。|  
|**attach_query_result_as_file**|**bit**|當值是 0 時，查詢結果會包含在電子郵件訊息的主體中，在主體的內容之後。 當值是 1 時，會以附加檔案的方式傳回結果。|  
|**query_result_header**|**bit**|當值是 1 時，查詢結果會包含資料行標頭。 當值是 0 時，查詢結果不會包含資料行標頭。|  
|**query_result_width**|**int**|訊息的 **query_result_width** 參數。|  
|**query_result_separator**|**char(1)**|在查詢輸出中用來分隔資料行的字元。|  
|**exclude_query_output**|**bit**|訊息的 **exclude_query_output** 參數。 如需詳細資訊，請參閱 [sp_send_dbmail &#40;transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql.md)。|  
|**append_query_error**|**bit**|訊息的 **append_query_error** 參數。 0 表示如果查詢中有錯誤，則 Database Mail 不應傳送電子郵件訊息。|  
|**send_request_date**|**datetime**|訊息置於郵件佇列的日期和時間。|  
|**send_request_user**|**sysname**|提交訊息的使用者。 這是 Database Mail 程序的使用者內容，不是訊息的「寄件者:」欄位。|  
|**sent_account_id**|**int**|用來傳送訊息之 Database Mail 帳戶的識別碼。 針對這個檢視一律為 NULL。|  
|**sent_status**|**Varchar (8)**|郵件的狀態。 此視圖永遠 **失敗** 。|  
|**sent_date**|**datetime**|訊息從郵件佇列中移除的日期和時間。|  
|**last_mod_date**|**datetime**|資料列上次修改的日期和時間。|  
|**last_mod_user**|**sysname**|上次修改資料列的使用者。|  
  
## <a name="remarks"></a>備註  
 使用 **sysmail_faileditems** 視圖來查看 Database Mail 未傳送的訊息。 對 Database Mail 進行疑難排解時，這個檢視可藉顯示未傳送訊息的屬性，來幫助您識別問題的本質。 若要查看失敗的原因，請參閱 [sysmail_event_log &#40;transact-sql&#41;](../../relational-databases/system-catalog-views/sysmail-event-log-transact-sql.md) view 中失敗訊息的專案。  
  
## <a name="permissions"></a>權限  
 授與 **系統管理員（sysadmin** ）固定伺服器角色和 **databasemailuserrole** 資料庫角色。 由 **系統管理員（sysadmin** ）固定伺服器角色的成員執行時，此視圖會顯示所有失敗的訊息。 所有其他使用者只看得到他們所提交的失敗訊息。  
  
  
