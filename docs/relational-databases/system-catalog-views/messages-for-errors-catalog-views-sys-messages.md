---
description: 訊息 (適用於錯誤) 目錄檢視 - sys.messages
title: sys. messages (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- messages_TSQL
- sys.messages_TSQL
- sys.messages
- messages
dev_langs:
- TSQL
helpviewer_keywords:
- error messages [SQL Server]
- sys.messages catalog view
- error numbers [SQL Server]
ms.assetid: 8c16ecdf-68f4-4a2a-b594-086e3344e58a
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 590ad007eebcf37765752272ef775e0d3d2443f0
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98092542"
---
# <a name="messages-for-errors-catalog-views---sysmessages"></a>訊息 (適用於錯誤) 目錄檢視 - sys.messages
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  針對系統所定義和使用者定義的訊息，各包含系統中每個 **message_id** 或 **language_id** 錯誤訊息的資料列。 如需詳細資訊，請參閱 [sp_addmessage &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-addmessage-transact-sql.md)。  
   
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**message_id**|**int**|訊息的識別碼。 在伺服器中，這是唯一的。 小於 50000 的訊息識別碼，都是系統訊息。|  
|**language_id**|**smallint**|使用 **文字** 中文字的語言識別項，如 **syslanguages** 中所定義。 這對指定的 **message_id** 而言是唯一的。|  
|**severity**|**tinyint**|訊息的嚴重性層級，介於 1 至 25 之間。 這對 **message_id** 內的所有訊息語言都相同。|  
|**is_event_logged**|**bit**|1 = 當引發錯誤時，會以記錄事件的方式記錄訊息。 這對 **message_id** 內的所有訊息語言都相同。|  
|**text**|**nvarchar(2048)**|當對應的 **language_id** 為使用中時，所使用之訊息的文字。|  
  
## <a name="permissions"></a>權限  
 需要 **public** 角色的成員資格。 如需相關資訊，請參閱 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)。  
  
## <a name="see-also"></a>另請參閱  
 [THROW &#40;Transact-SQL&#41;](../../t-sql/language-elements/throw-transact-sql.md)   
 [目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/catalog-views-transact-sql.md)   
 [訊息 &#40;&#41; 目錄檢視的錯誤 &#40;Transact-sql&#41;]()   
 [例外狀況訊息方塊程式設計](/previous-versions/sql/sql-server-2016/ms166343(v=sql.130))   
 [錯誤訊息](../../relational-databases/native-client-odbc-error-messages/error-messages.md)   
 [資料庫引擎事件和錯誤](../../relational-databases/errors-events/database-engine-events-and-errors.md)  
  
