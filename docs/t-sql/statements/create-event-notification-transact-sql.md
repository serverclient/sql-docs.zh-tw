---
description: CREATE EVENT NOTIFICATION (Transact-SQL)
title: CREATE EVENT NOTIFICATION (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- CREATE_EVENT_NOTIFICATION_TSQL
- NOTIFICATION_TSQL
- EVENT
- NOTIFICATION
- CREATE EVENT NOTIFICATION
- EVENT_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- CREATE EVENT NOTIFICATION statement
- events [SQL Server], notifications
- event notifications [SQL Server], creating
ms.assetid: dbbff0e8-9e25-4f12-a1ba-e12221d16ac2
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 6f1e3aa71a31dfff7bd7ec83d367efdaa8a01967
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98100855"
---
# <a name="create-event-notification-transact-sql"></a>CREATE EVENT NOTIFICATION (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  建立一個物件，將資料庫或伺服器事件的相關資訊傳送給 Service Broker 服務。 事件通知只能利用 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式來建立。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
CREATE EVENT NOTIFICATION event_notification_name   
ON { SERVER | DATABASE | QUEUE queue_name }   
[ WITH FAN_IN ]  
FOR { event_type | event_group } [ ,...n ]  
TO SERVICE 'broker_service' , { 'broker_instance_specifier' | 'current database' }  
[ ; ]  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *event_notification_name*  
 這是事件通知的名稱。 事件通知名稱必須符合 [識別碼](../../relational-databases/databases/database-identifiers.md)的規則，且在建立所在範圍內必須是唯一的：SERVER、DATABASE 或 *object_name*。  
  
 SERVER  
 將事件通知範圍套用在目前的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體上。 如果指定的話，每當 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體的任何位置發生 FOR 子句中所指定的事件時，都會引發通知。  
  
> [!NOTE]  
>  自主資料庫無法使用這個選項。  
  
 DATABASE  
 將事件通知範圍套用在目前資料庫上。 如果指定的話，每當目前資料庫發生 FOR 子句中所指定的事件時，都會引發通知。  
  
 QUEUE  
 將通知範圍套用在目前資料庫的特定佇列上。 只有在也指定了 FOR QUEUE_ACTIVATION 或 FOR BROKER_QUEUE_DISABLED 時，才能指定 QUEUE。  
  
 *queue_name*  
 這是套用事件通知的佇列名稱。 只有在指定 QUEUE 時，才能指定 *queue_name*。  
  
 WITH FAN_IN  
 指示 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 針對每個事件只傳送一則訊息給所有符合下列條件之事件通知的任何指定服務：  
  
-   在相同事件中建立。  
  
-   由相同主體建立 (由相同 SID 來識別)。  
  
-   指定相同的服務和 *broker_instance_specifier*。  
  
-   指定 WITH FAN_IN。  
  
 例如，在建立三個事件通知的情況下。 所有的事件通知都會指定 FOR ALTER_TABLE、WITH FAN_IN、相同的 TO SERVICE 子句，並由相同的 SID 建立。 執行 ALTER TABLE 陳述式時，這三個事件通知建立的訊息將會合併為一則訊息。 因此，目標服務只會收到一則事件訊息。  
  
 *event_type*  
 這是造成執行事件通知的事件類型名稱。 *event_type* 可以是 [!INCLUDE[tsql](../../includes/tsql-md.md)] DDL 事件類型、SQL 追蹤事件類型，或 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 事件類型。 如需符合的 [!INCLUDE[tsql](../../includes/tsql-md.md)] DDL 事件類型清單，請參閱 [DDL 事件](../../relational-databases/triggers/ddl-events.md)。 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 事件類型為 QUEUE_ACTIVATION 和 BROKER_QUEUE_DISABLED。 如需詳細資訊，請參閱 [Event Notifications](../../relational-databases/service-broker/event-notifications.md)。  
  
 *event_group*  
 這是 [!INCLUDE[tsql](../../includes/tsql-md.md)] 或 SQL 追蹤事件類型預先定義群組的名稱。 在執行屬於事件群組的任何事件之後，便能夠引發事件通知。 如需 DDL 事件群組、這些事件群組所涵蓋的 [!INCLUDE[tsql](../../includes/tsql-md.md)] 事件以及這些事件群組可定義範圍的清單，請參閱 [DDL 事件群組](../../relational-databases/triggers/ddl-event-groups.md)。  
  
 當 CREATE EVENT NOTIFICATION 陳述式完成時，透過將所涵蓋的事件類型加入 **sys.events** 目錄檢視中，*event_group* 也可以作為巨集。  
  
 **'** *broker_service* **'**  
 指定接收事件執行個體資料的目標服務。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會針對事件通知，開啟一或多項與目標服務的交談。 這項服務必須遵照相同的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 事件訊息類型和用來傳送訊息的合約。  
  
 這項交談會維持開啟狀態，直到卸除事件通知為止。 特定錯誤可能使交談提早關閉。 明確地結束部分或所有交談，可以防止目標服務接收其他訊息。  
  
 { **'** _broker\_instance\_specifier_ **'**  |  **'current database'** }  
 對已經解析的 *broker_service* 指定 Service Broker 執行個體。 您可以透過查詢 **sys.databases** 目錄檢視的 **service_broker_guid** 資料行，來取得特定 Service Broker 的值。 使用 **'current database'** 來指定目前資料庫中的 Service Broker 執行個體。 **'current database'** 是不區分大小寫的字串常值。  
  
> [!NOTE]  
>  自主資料庫無法使用這個選項。  
  
## <a name="remarks"></a>備註  
 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 包括事件通知專用的訊息類型和合約。 因此，您不需要建立 Service Broker 起始服務，因為已有指定了下列合約名稱的 Service Broker 起始服務：`https://schemas.microsoft.com/SQL/Notifications/PostEventNotification`  
  
 接收事件通知的目標服務必須遵照這項預先存在的合約。  
  
> [!IMPORTANT]  
>  [!INCLUDE[ssSB](../../includes/sssb-md.md)] 對話安全性。 對話安全性必須根據完整安全性模型，以手動方式加以設定。 如需詳細資訊，請參閱[設定事件通知的對話安全性](../../relational-databases/service-broker/configure-dialog-security-for-event-notifications.md)。  
  
 如果回復啟動通知的事件交易，也會回復事件通知的傳送。 當在觸發程序內認可或回復交易時，觸發程序內所定義的動作並不會引發事件通知。 由於交易並未繫結追蹤事件，不論是否回復啟動以追蹤事件為基礎的事件通知，都會傳送這些追蹤事件。  
  
 如果在引發事件通知之後，伺服器和目標服務之間的交談中斷了，就會報告一則錯誤，且會卸除事件通知。  
  
 事件通知的傳送，不論成敗，都不會影響最初啟動通知的事件交易。  
  
 事件通知的任何傳送失敗，都會記錄下來。  
  
## <a name="permissions"></a>權限  
 若要建立以資料庫為範圍 (ON DATABASE) 的事件通知，需要目前資料庫的 CREATE DATABASE DDL EVENT NOTIFICATION 權限。  
  
 若要建立以伺服器為範圍 (ON SERVER) 之 DDL 陳述式的事件通知，需要伺服器的 CREATE DDL EVENT NOTIFICATION 權限。  
  
 若要建立追蹤事件的事件通知，需要伺服器的 CREATE TRACE EVENT NOTIFICATION 權限。  
  
 若要建立以佇列為範圍的事件通知，需要佇列的 ALTER 權限。  
  
## <a name="examples"></a>範例  
  
> [!NOTE]  
>  在底下的範例 A 和 B 中，`TO SERVICE 'NotifyService'` 子句 ('8140a771-3c4b-4479-8ac0-81008ab17984') 中的 GUID 是範例設定所在的電腦所特有。 對於該執行個體而言，這就是 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 資料庫的 GUID。  
>   
>  若要複製及執行這些範例，您需要使用電腦和 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體中的 GUID 來取代這個 GUID。 如同上面的＜引數＞一節所說明，您可以透過查詢 sys.databases 目錄檢視的 service_broker_guid 資料行，以取得 **'** _broker\_instance\_specifier_ **'** 。  
  
### <a name="a-creating-an-event-notification-that-is-server-scoped"></a>A. 建立以伺服器為範圍的事件通知  
 下列範例會利用 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 來建立設定目標服務所需要的物件。 目標服務會參考事件通知專用的起始服務之訊息類型和合約。 之後，每當 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體發生 `Object_Created` 追蹤事件時，都會在這個傳送通知的目標服務上建立一項事件通知。  
  
```sql  
--Create a queue to receive messages.  
CREATE QUEUE NotifyQueue ;  
GO  

--Create a service on the queue that references  
--the event notifications contract.  
CREATE SERVICE NotifyService  
ON QUEUE NotifyQueue  
([https://schemas.microsoft.com/SQL/Notifications/PostEventNotification]);  
GO  

--Create a route on the service to define the address   
--to which Service Broker sends messages for the service.  
CREATE ROUTE NotifyRoute  
WITH SERVICE_NAME = 'NotifyService',  
ADDRESS = 'LOCAL';  
GO 

--Create the event notification.  
CREATE EVENT NOTIFICATION log_ddl1   
ON SERVER   
FOR Object_Created   
TO SERVICE 'NotifyService',  
    '8140a771-3c4b-4479-8ac0-81008ab17984' ;  
```  
  
### <a name="b-creating-an-event-notification-that-is-database-scoped"></a>B. 建立以資料庫為範圍的事件通知  
 下列範例會在前一個範例的相同目標服務上，建立一項事件通知。 在 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 範例資料庫發生 `ALTER_TABLE` 事件之後，都會引發事件通知。  
  
```sql  
CREATE EVENT NOTIFICATION Notify_ALTER_T1  
ON DATABASE  
FOR ALTER_TABLE  
TO SERVICE 'NotifyService',  
    '8140a771-3c4b-4479-8ac0-81008ab17984';  
```  
  
### <a name="c-getting-information-about-an-event-notification-that-is-server-scoped"></a>C. 取得以伺服器為範圍之事件通知的相關資訊  
 下列範例會查詢 `sys.server_event_notifications` 目錄檢視，來找出以伺服器為範圍建立之 `log_ddl1` 事件通知的中繼資料。  
  
```sql  
SELECT * FROM sys.server_event_notifications  
WHERE name = 'log_ddl1';  
```  
  
### <a name="d-getting-information-about-an-event-notification-that-is-database-scoped"></a>D. 取得以資料庫為範圍之事件通知的相關資訊  
 下列範例會查詢 `sys.event_notifications` 目錄檢視，來找出以資料庫為範圍建立之 `Notify_ALTER_T1` 事件通知的中繼資料。  
  
```sql  
SELECT * FROM sys.event_notifications  
WHERE name = 'Notify_ALTER_T1';  
```  
  
## <a name="see-also"></a>另請參閱  
 [事件通知](../../relational-databases/service-broker/event-notifications.md)   
 [DROP EVENT NOTIFICATION &#40;Transact-SQL&#41;](../../t-sql/statements/drop-event-notification-transact-sql.md)   
 [EVENTDATA &#40;Transact-SQL&#41;](../../t-sql/functions/eventdata-transact-sql.md)   
 [sys.event_notifications &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-event-notifications-transact-sql.md)   
 [sys.server_event_notifications &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-server-event-notifications-transact-sql.md)   
 [sys.events &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-events-transact-sql.md)   
 [sys.server_events &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-server-events-transact-sql.md)  
  
  
