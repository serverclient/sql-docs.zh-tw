---
description: sysmail_unsentitems (Transact-SQL)
title: sysmail_unsentitems (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sysmail_unsentitems_TSQL
- sysmail_unsentitems
dev_langs:
- TSQL
helpviewer_keywords:
- sysmail_unsentitems database mail view
ms.assetid: 993c12da-41e5-4e53-a188-0323feb70c67
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 0e8ffcc7ae60096de1b0355ed113ffb55e02e286
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98100106"
---
# <a name="sysmail_unsentitems-transact-sql"></a>sysmail_unsentitems (Transact-SQL)
[!INCLUDE [SQL Server - ASDBMI](../../includes/applies-to-version/sql-asdbmi.md)]

  針對每個具有未傳送或正在 **重試****狀態的 Database Mail** 訊息，各包含一個資料列。 未傳送或正在重試狀態的訊息仍存在郵件佇列中，隨時可能傳送。 訊息可能會因為下列原因而 **有尚未傳送的狀態：**  
  
-   訊息是新的，雖然訊息已置於郵件佇列中，但 Database Mail 正在處理其他訊息而尚未到達這則訊息。  
  
-   Database Mail 外部程式未執行，且未傳送郵件。  
  
 訊息可能會因為下列原因而具有 **重試** 狀態：  
  
-   Database Mail 已嘗試傳送郵件，但無法連絡 SMTP 郵件伺服器。 Database Mail 會使用指派給傳送訊息之設定檔的其他 Database Mail 帳戶，繼續嘗試傳送訊息。 如果沒有任何帳戶可以傳送郵件，Database Mail 會等待為 **帳戶重試延遲** 參數設定的時間長度，然後再次嘗試傳送訊息。 Database Mail 使用 **帳戶重試嘗試** 參數，判斷嘗試傳送訊息的次數。 只要 Database Mail 嘗試傳送訊息，訊息就會保留 **重試** 狀態。  
  
 若要查看等候傳送的訊息數和其於郵件佇列中停留的時間，請使用這份檢視。 通常未傳送的 **訊息數目** 會很低。 在正常作業期間進行基準測試，可判斷作業之訊息佇列中的合理訊息數。  
  
 若要查看 Database Mail 所處理的所有訊息，請使用 [sysmail_allitems &#40;transact-sql&#41;](../../relational-databases/system-catalog-views/sysmail-allitems-transact-sql.md)。 若只要查看失敗狀態的訊息，請使用 [sysmail_faileditems &#40;transact-sql&#41;](../../relational-databases/system-catalog-views/sysmail-faileditems-transact-sql.md)。 若只要查看已傳送的訊息，請使用 [sysmail_sentitems &#40;transact-sql&#41;](../../relational-databases/system-catalog-views/sysmail-sentitems-transact-sql.md)。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**mailitem_id**|**int**|郵件佇列中郵件項目的識別碼。|  
|**profile_id**|**int**|用來提交訊息之設定檔的識別碼。|  
|**收件者**|**varchar(max)**|訊息收件者的電子郵件地址。|  
|**copy_recipients**|**varchar(max)**|訊息副本收件者的電子郵件地址。|  
|**blind_copy_recipients**|**varchar(max)**|名稱未顯示於訊息標頭之訊息副本收件者的電子郵件地址。|  
|**subject**|**Nvarchar (510)**|訊息的主旨。|  
|**body**|**varchar(max)**|訊息的主體。|  
|**body_format**|**Varchar (20)**|訊息的主體格式。 可能的值是 **TEXT** 和 **HTML**。|  
|**importance**|**Varchar (6)**|訊息的 **重要性** 參數。|  
|**靈敏度**|**Varchar (12)**|訊息的 **敏感度** 參數。|  
|**file_attachments**|**varchar(max)**|附加至電子郵件訊息中的檔案名稱清單，用分號分隔各檔案名稱。|  
|**attachment_encoding**|**Varchar (20)**|郵件附加檔案的類型。|  
|**查詢**|**varchar(max)**|郵件程式執行的查詢。|  
|**execute_query_database**|**sysname**|郵件程式執行查詢所在的資料庫內容。|  
|**attach_query_result_as_file**|**bit**|當值是 0 時，查詢結果會包含在電子郵件訊息的主體中，在主體的內容之後。 當值是 1 時，會以附加檔案的方式傳回結果。|  
|**query_result_header**|**bit**|當值是 1 時，查詢結果會包含資料行標頭。 當值是 0 時，查詢結果不會包含資料行標頭。|  
|**query_result_width**|**int**|訊息的 **query_result_width** 參數。|  
|**query_result_separator**|**char(1)**|在查詢輸出中用來分隔資料行的字元。|  
|**exclude_query_output**|**bit**|訊息的 **exclude_query_output** 參數。 如需詳細資訊，請參閱 [sp_send_dbmail &#40;transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql.md)。|  
|**append_query_error**|**bit**|訊息的 **append_query_error** 參數。 0 表示如果查詢中有錯誤，則 Database Mail 不應傳送電子郵件訊息。|  
|**send_request_date**|**datetime**|訊息置於郵件佇列的日期和時間。|  
|**send_request_user**|**sysname**|提交訊息的使用者。 這是 database mail 程式的使用者內容，不是訊息的 [ **發件** 者] 欄位。|  
|**sent_account_id**|**int**|用來傳送訊息之 Database Mail 帳戶的識別碼。 針對這個檢視一律為 NULL。|  
|**sent_status**|**Varchar (8)**|如果 Database Mail 尚未嘗試傳送郵件，則將 **不會傳送** 。 如果 Database Mail 無法傳送訊息，但正在重試，將會 **重試** 。|  
|**sent_date**|**datetime**|Database Mail 上次嘗試傳送郵件的日期和時間。 如果 Database Mail 未嘗試傳送訊息，則是 NULL。|  
|**last_mod_date**|**datetime**|資料列上次修改的日期和時間。|  
|**last_mod_user**|**sysname**|上次修改資料列的使用者。|  
  
## <a name="remarks"></a>備註  
 對 Database Mail 進行疑難排解時，這個檢視可藉顯示等候傳送的訊息數和訊息已等候的時間量，來幫助您識別問題的本質。 如果未傳送任何訊息，Database Mail 外部程式可能不在執行中，或者可能有使 Database Mail 無法連絡 SMTP 伺服器的網路問題。 如果許多未傳送的訊息都有相同的 **profile_id**，可能是 SMTP 伺服器有問題。 請考慮將其他帳戶加入設定檔中。 如果正在傳送訊息，但訊息耗費太多時間在佇列中，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 可能需要更多資源來處理需要的訊息量。  
  
## <a name="permissions"></a>權限  
 授與 **系統管理員（sysadmin** ）固定伺服器角色和 **DatabaseMailUserRole** 資料庫角色。 由 **系統管理員（sysadmin** ）固定伺服器角色的成員執行時，此視圖會 **顯示所有未** 傳送或正在 **重試** 的訊息。 所有其他使用者只會看到他們所提交的未 **傳送或****重試** 訊息。  
  
  
