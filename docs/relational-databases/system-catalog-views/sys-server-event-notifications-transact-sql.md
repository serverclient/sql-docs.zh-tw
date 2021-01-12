---
description: sys.server_event_notifications (Transact-SQL)
title: sys.server_event_notifications (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- server_event_notifications
- sys.server_event_notifications
- sys.server_event_notifications_TSQL
- server_event_notifications_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.server_event_notifications catalog view
ms.assetid: 1a83a044-3130-4551-95ca-162525846ff5
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 60f8d8e0470a62e9679eab8af97173228b3a2375
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98101732"
---
# <a name="sysserver_event_notifications-transact-sql"></a>sys.server_event_notifications (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  針對每個伺服器層級的事件通知物件，各傳回一個資料列。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**name**|**sysname**|伺服器事件通知名稱。 這個名稱是跨越所有伺服器層級事件通知而為唯一的。|  
|object_id|**int**|物件識別碼。 在 **master** 資料庫中是唯一的。|  
|**parent_class**|**tinyint**|父系的類別。 它一律是 100 = 伺服器。|  
|**parent_class_desc**|**nvarchar(60)**|父類別的描述。 它一律是 SERVER。|  
|**parent_id**|**int**|它一律是 0。|  
|**create_date**|**datetime**|建立日期。|  
|**modify_date**|**datetime**|上次利用 ALTER 陳述式來修改物件的日期。|  
|**service_name**|**nvarchar(256)**|通知所送往之目標服務的名稱。|  
|**broker_instance**|**nvarchar(128)**|具名目標服務定義所在的 Service Broker。|  
|**creator_sid**|**varbinary(85)**|執行陳述式來建立事件通知之登入的 SID。 如果事件通知定義中沒有指定 WITH FAN_IN，則為 NULL。|  
|**principal_id**|**int**|擁有這個項目的伺服器主體識別碼。|  
  
## <a name="permissions"></a>權限  
 [!INCLUDE[ssCatViewPerm](../../includes/sscatviewperm-md.md)] 如需相關資訊，請參閱 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)。  
  
## <a name="see-also"></a>另請參閱  
 [物件目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/object-catalog-views-transact-sql.md)   
 [目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/catalog-views-transact-sql.md)  
  
  
