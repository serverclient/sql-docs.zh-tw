---
description: RECEIVE (Transact-SQL)
title: RECEIVE (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 07/26/2017
ms.prod: sql
ms.prod_service: sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- RECEIVE_TSQL
- RECEIVE
dev_langs:
- TSQL
helpviewer_keywords:
- queues [Service Broker], message retrieval
- messages [Service Broker], retrieving
- RECEIVE statement
- receiving messages
- retrieving messages
ms.assetid: 878c6c14-37ab-4b87-9854-7f8f42bac7dd
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 840f023e1a906350f34d0709459842a3fe3c796a
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98080851"
---
# <a name="receive-transact-sql"></a>RECEIVE (Transact-SQL)
[!INCLUDE [SQL Server - ASDBMI](../../includes/applies-to-version/sql-asdbmi.md)]

  從佇列中擷取一或多則訊息。 根據佇列的保留設定，從佇列中移除訊息或更新佇列中的訊息狀態。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
[ WAITFOR ( ]  
    RECEIVE [ TOP ( n ) ]   
        <column_specifier> [ ,...n ]  
        FROM <queue>  
        [ INTO table_variable ]  
        [ WHERE {  conversation_handle = conversation_handle  
                 | conversation_group_id = conversation_group_id } ]  
[ ) ] [ , TIMEOUT timeout ]  
[ ; ]  
  
<column_specifier> ::=  
{    *   
  |  { column_name | [ ] expression } [ [ AS ] column_alias ]  
  |  column_alias = expression   
}     [ ,...n ]   
  
<queue> ::=  
{ database_name.schema_name.queue_name | schema_name.queue_name | queue_name }
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 WAITFOR  
 指定如果目前沒有訊息，RECEIVE 陳述式便等待訊息到達佇列。  
  
 TOP( *n* )  
 指定要傳回的最大訊息數目。 如果未指定這個子句，便會傳回符合陳述式準則的所有訊息。  
  
 \*  
 指定結果集包含佇列中的所有資料行。  
  
 *column_name*  
 結果集中所要包含的資料行名稱。  
  
 *expression*  
 資料行名稱、常數、函數，或是任何由運算子連接之資料行名稱、常數和函數的組合。  
  
 *column_alias*  
 要取代結果集中之資料行名稱的替代名稱。  
  
 FROM  
 指定包含要擷取之訊息的佇列。  
  
 *database_name*  
 包含接收訊息之來源佇列的資料庫名稱。 若未提供 *database_name*，則預設為目前的資料庫。  
  
 *schema_name*  
 擁有接收訊息之來源佇列的結構描述名稱。 若未提供 *schema_name*，則預設為目前使用者的預設結構描述。  
  
 *queue_name*  
 接收訊息之來源佇列的名稱。  
  
 INTO *table_variable*  
 指定 RECEIVE 要將訊息放入其中的資料表變數。 此資料表變數的資料行數目必須與訊息中的資料行數目相同。 此資料表變數中每一個資料行的資料類型都必須隱含地轉換成訊息中對應資料行的資料類型。 如果未指定 INTO，訊息會當做結果集來傳回。  
  
 WHERE  
 指定所接收之訊息的交談或交談群組。 如果忽略這個值，便從下一個可用的交談群組傳回訊息。  
  
 conversation_handle = *conversation_handle*  
 指定所接收之訊息的交談。 您提供的「交談控制代碼」必須為 **uniqueidentifier**，或可轉換為 **uniqueidentifier** 的類型。  
  
 conversation_group_id = *conversation_group_id*  
 指定接收的訊息之交談群組。 您提供的「交談群組識別碼」必須為 **uniqueidentifier**，或可轉換為 **uniqueidentifier** 的類型。  
  
 TIMEOUT *timeout*  
 指定陳述式等候訊息的時間 (以毫秒為單位)。 這個子句只適用於 WAITFOR 子句。 如果未指定這個子句，或逾時為 -**1**，等候時間便沒有限制。 如果等候時間逾時，RECEIVE 會傳回空的結果集。  
  
## <a name="remarks"></a>備註  
  
> [!IMPORTANT]  
>  如果 RECEIVE 陳述式不是批次或預存程序中的第一個陳述式，就必須利用分號 (;) 來終止前一個陳述式。  
  
 RECEIVE 陳述式會從佇列中讀取訊息並傳回結果集。 結果集是由零或多個資料列組成，每個資料列都包含一則訊息。 如果未使用 INTO 子句，且 *column_specifier* 未指派本機變數值，陳述式會將結果集傳回給發出呼叫的程式。  
  
 RECEIVE 陳述式傳回的訊息可以具有不同的訊息類型。 應用程式可以使用 **message_type_name** 資料行，將每一則訊息路由傳送給處理相關訊息類型的程式碼。 訊息類型的類別有兩種：  
  
-   之前使用 CREATE MESSAGE TYPE 陳述式所建立之應用程式定義的訊息類型。 交談中允許之應用程式定義的訊息類型集合是由針對交談指定的 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 合約所定義。  
  
-   傳回狀態或錯誤資訊的 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 系統訊息。  
  
 除非佇列指定保留訊息，否則，RECEIVE 陳述式會從佇列中移除接收的訊息。 當佇列的 RETENTION 設定是 ON 時，RECEIVE 陳述式會將 **status** 資料行更新為 **0**，並將訊息保留在佇列中。 當包含 RECEIVE 陳述式的交易回復時，也會回復交易內的所有佇列變更，且會將訊息傳回給佇列。  
  
 RECEIVE 陳述式傳回的所有訊息都屬於相同的交談群組。 RECEIVE 陳述式會鎖定傳回訊息的交談群組，直到包含陳述式的交易完成為止。 RECEIVE 陳述式會傳回 **status** 為 **1** 的訊息。 RECEIVE 陳述式傳回的結果集會隱含地排序：  
  
-   如果多個交談中的訊息符合 WHERE 子句條件，則 RECEIVE 陳述式會傳回一個交談中的所有訊息，然後再傳回其他任何交談的訊息。 交談會依照遞減優先權的順序來處理。  
  
-   對於指定的交談，RECEIVE 陳述式會以遞增 **message_sequence_number** 的順序傳回訊息。  
  
 RECEIVE 陳述式的 WHERE 子句只能包含一個使用 **conversation_handle** 或 **conversation_group_id** 的搜尋條件。 搜尋條件不能包含佇列中的一或多個其他資料行。 **conversation_handle** 或 **conversation_group_id** 不能是運算式。 傳回的訊息集合取決於 WHERE 子句中所指定的條件：  
  
-   如果指定 **conversation_handle**，則 RECEIVE 會傳回佇列中可用之指定交談的所有訊息。  
  
-   如果指定 **conversation_group_id**，則 RECEIVE 傳回佇列中屬於指定交談群組之任何交談的所有可用訊息。  
  
-   如果沒有 WHERE 子句，RECEIVE 會判斷哪一個交談群組：  
  
    -   在佇列中有一或多個訊息。  
  
    -   尚未由另一個 RECEIVE 陳述式鎖定。  
  
    -   在符合這些準則的所有交談群組中，具有最高的優先權等級。  
  
     然後 RECEIVE 會從屬於選定交談群組的任何交談傳回佇列中可用的所有訊息。  
  
 如果 WHERE 子句中指定的交談控制代碼或交談群組識別碼不存在，或是與指定的佇列沒有關聯，RECEIVE 陳述式便會傳回錯誤。  
  
 如果 RECEIVE 陳述式中指定之佇列的佇列狀態設為 OFF，陳述式將會因 [!INCLUDE[tsql](../../includes/tsql-md.md)] 錯誤而失敗。  
  
 當指定了 WAITFOR 子句時，陳述式會等候指定的逾時，或等候結果集可用。 如果陳述式正在等候時，佇列被卸除或佇列狀態設為 OFF，陳述式會立即傳回錯誤。 如果 RECEIVE 陳述式指定了交談群組或交談控制代碼，且這項交談的服務被卸除或移至另一個佇列，RECEIVE 陳述式便會報告 [!INCLUDE[tsql](../../includes/tsql-md.md)] 錯誤。  
  
 RECEIVE 在使用者自訂函數中無效。  
  
 RECEIVE 陳述式沒有缺少優先權的預防措施。 如果單一 RECEIVE 陳述式鎖定了交談群組，並從低優先權的交談擷取許多訊息，將無法從此群組中的高優先權交談接收任何訊息。 為了避免這個狀況，當您從低優先權的交談擷取訊息時，請使用 TOP 子句來限制每一個 RECEIVE 陳述式所擷取的訊息數目。  
  
## <a name="queue-columns"></a>佇列資料行  
 下表列出佇列中的資料行：  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**status**|**tinyint**|訊息狀態。 針對 RECEIVE 命令傳回的訊息，狀態一律是 **0**。 佇列中的訊息可能會包含下列其中一個值：<br /><br /> **0**=備妥 **1**=接收的訊息 **2**=尚未完成 **3**=保留的傳送訊息|  
|**priority**|**tinyint**|套用到訊息的交談優先權等級。|  
|**queuing_order**|**bigint**|佇列中的訊息順序號碼。|  
|**conversation_group_id**|**uniqueidentifier**|這則訊息所屬的交談群組識別碼。|  
|**conversation_handle**|**uniqueidentifier**|這則訊息所屬的交談之控制代碼。|  
|**message_sequence_number**|**bigint**|訊息在交談中的序號。|  
|**service_name**|**nvarchar(512)**|交談的目標服務名稱。|  
|**service_id**|**int**|交談目標服務的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 物件識別碼。|  
|**service_contract_name**|**nvarchar(256)**|交談遵照的合約名稱。|  
|**service_contract_id**|**int**|交談遵照的合約之 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 物件識別碼。|  
|**message_type_name**|**nvarchar(256)**|描述訊息格式的訊息類型名稱。 訊息可以是應用程式訊息類型或 Broker 系統訊息。|  
|**message_type_id**|**int**|描述訊息的訊息類型之 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 物件識別碼。|  
|**validation**|**nchar(2)**|用於訊息的驗證。<br /><br /> **E**=空白 **N**=無 **X**=XML|  
|**message_body**|**varbinary(MAX)**|訊息內容。|  
  
## <a name="permissions"></a>權限  
 若要接收訊息，目前的使用者必須有佇列的 RECEIVE 權限。  
  
## <a name="examples"></a>範例  
  
### <a name="a-receiving-all-columns-for-all-messages-in-a-conversation-group"></a>A. 接收交談群組中全部訊息的所有資料行  
 下列範例接收 `ExpenseQueue` 佇列中下一個可用交談群組的所有可用訊息。 陳述式將訊息當作結果集傳回。  
  
```sql  
RECEIVE * FROM ExpenseQueue ;  
```  
  
### <a name="b-receiving-specified-columns-for-all-messages-in-a-conversation-group"></a>B. 接收交談群組中全部訊息的指定資料行  
 下列範例接收 `ExpenseQueue` 佇列中下一個可用交談群組的所有可用訊息。 陳述式將訊息當作包含 `conversation_handle`、`message_type_name` 和 `message_body` 等資料行的結果集傳回。  
  
```sql  
RECEIVE conversation_handle, message_type_name, message_body  
FROM ExpenseQueue ;  
```  
  
### <a name="c-receiving-the-first-available-message-in-the-queue"></a>C. 接收佇列中第一個可用的訊息  
 下列範例從 `ExpenseQueue` 佇列中接收第一個可用的訊息來做為結果集。  
  
```sql  
RECEIVE TOP (1) * FROM ExpenseQueue ;  
```  
  
### <a name="d-receiving-all-messages-for-a-specified-conversation"></a>D. 接收指定交談的所有訊息  
 下列範例從 `ExpenseQueue` 佇列中接收指定交談的所有可用訊息來做為結果集。  
  
```sql  
DECLARE @conversation_handle UNIQUEIDENTIFIER ;  
  
SET @conversation_handle = <retrieve conversation from database> ;  
  
RECEIVE *  
FROM ExpenseQueue  
WHERE conversation_handle = @conversation_handle ;  
```  
  
### <a name="e-receiving-messages-for-a-specified-conversation-group"></a>E. 接收指定交談群組的訊息  
 下列範例從 `ExpenseQueue` 佇列中接收指定交談群組的所有可用訊息來做為結果集。  
  
```sql  
DECLARE @conversation_group_id UNIQUEIDENTIFIER ;  
  
SET @conversation_group_id =   
    <retrieve conversation group ID from database> ;  
  
RECEIVE *  
FROM ExpenseQueue  
WHERE conversation_group_id = @conversation_group_id ;  
```  
  
### <a name="f-receiving-into-a-table-variable"></a>F. 接收並放入資料表變數  
 下列範例從 `ExpenseQueue` 佇列中接收指定交談群組的所有可用訊息，將它們放入資料表變數中。  
  
```sql  
DECLARE @conversation_group_id UNIQUEIDENTIFIER ;  
  
DECLARE @procTable TABLE(  
     service_instance_id UNIQUEIDENTIFIER,  
     handle UNIQUEIDENTIFIER,  
     message_sequence_number BIGINT,  
     service_name NVARCHAR(512),  
     service_contract_name NVARCHAR(256),  
     message_type_name NVARCHAR(256),  
     validation NCHAR,  
     message_body VARBINARY(MAX)) ;  
  
SET @conversation_group_id = <retrieve conversation group ID from database> ;  
  
RECEIVE TOP (1)  
    conversation_group_id,  
    conversation_handle,  
    message_sequence_number,  
    service_name,  
    service_contract_name,  
    message_type_name,  
    validation,  
    message_body  
FROM ExpenseQueue  
INTO @procTable  
WHERE conversation_group_id = @conversation_group_id ;  
```  
  
### <a name="g-receiving-messages-and-waiting-indefinitely"></a>G. 接收訊息並無限期等候  
 下列範例接收 `ExpenseQueue` 佇列中下一個可用交談群組的所有可用訊息。 陳述式會等候到至少有一個可用的訊息，再傳回包含所有訊息資料行的結果集。  
  
```sql  
WAITFOR (  
    RECEIVE *  
    FROM ExpenseQueue) ;  
```  
  
### <a name="h-receiving-messages-and-waiting-for-a-specified-interval"></a>H. 接收訊息並等候指定的間隔  
 下列範例接收 `ExpenseQueue` 佇列中下一個可用交談群組的所有可用訊息。 陳述式會等候 60 秒，或等到至少有一個可用的訊息，兩者中取其先發生者。 如果至少有一個可用的訊息，陳述式會傳回包含所有訊息資料行的結果集。 否則，陳述式會傳回空的結果集。  
  
```sql  
WAITFOR (  
    RECEIVE *  
    FROM ExpenseQueue ),  
TIMEOUT 60000 ;  
```  
  
### <a name="i-receiving-messages-modifying-the-type-of-a-column"></a>I. 接收訊息，修改資料行的類型  
 下列範例接收 `ExpenseQueue` 佇列中下一個可用交談群組的所有可用訊息。 當訊息類型說明訊息包含 XML 文件時，陳述式會將訊息主體轉換成 XML。  
  
```sql  
WAITFOR (  
    RECEIVE message_type_name,  
        CASE  
            WHEN validation = 'X' THEN CAST(message_body as XML)  
            ELSE NULL  
         END AS message_body   
         FROM ExpenseQueue ),  
TIMEOUT 60000 ;  
```  
  
### <a name="j-receiving-a-message-extracting-data-from-the-message-body-retrieving-conversation-state"></a>J. 接收訊息、從訊息主體擷取資料、擷取交談狀態  
 下列範例接收 `ExpenseQueue` 佇列中下一個可用交談群組的下一個可用訊息。 當訊息的類型是 `//Adventure-Works.com/Expenses/SubmitExpense` 時，陳述式會從訊息主體中擷取員工識別碼和項目的清單。 陳述式也會從 `ConversationState` 資料表中擷取交談的狀態。  
  
```sql  
WAITFOR(  
    RECEIVE   
    TOP(1)  
      message_type_name,  
      COALESCE(  
           (SELECT TOP(1) ConversationState  
            FROM CurrentConversations AS cc  
            WHERE cc.ConversationHandle = conversation_handle),  
           'NEW')  
      AS ConversationState,  
      COALESCE(  
          (SELECT TOP(1) ErrorCount  
           FROM CurrentConversations AS cc  
           WHERE cc.ConversationHandle = conversation_handle),   
           0)  
      AS ConversationErrors,  
      CASE WHEN message_type_name = N'//Adventure-Works.com/Expenses/SubmitExpense'  
          THEN CAST(message_body AS XML).value(  
                'declare namespace rpt = "https://Adventure-Works.com/schemas/expenseReport"  
                   (/rpt:ExpenseReport/rpt:EmployeeID)[1]', 'nvarchar(20)')  
         ELSE NULL  
      END AS EmployeeID,  
      CASE WHEN message_type_name = N'//Adventure-Works.com/Expenses/SubmitExpense'  
          THEN CAST(message_body AS XML).query(  
                'declare namespace rpt = "https://Adventure-Works.com/schemas/expenseReport"   
                     /rpt:ExpenseReport/rpt:ItemDetail')  
          ELSE NULL  
      END AS ItemList  
    FROM ExpenseQueue   
), TIMEOUT 60000 ;  
```  
  
## <a name="see-also"></a>另請參閱  
 [BEGIN DIALOG CONVERSATION &#40;Transact-SQL&#41;](../../t-sql/statements/begin-dialog-conversation-transact-sql.md)   
 [BEGIN CONVERSATION TIMER &#40;Transact-SQL&#41;](../../t-sql/statements/begin-conversation-timer-transact-sql.md)   
 [END CONVERSATION &#40;Transact-SQL&#41;](../../t-sql/statements/end-conversation-transact-sql.md)   
 [CREATE CONTRACT &#40;Transact-SQL&#41;](../../t-sql/statements/create-contract-transact-sql.md)   
 [CREATE MESSAGE TYPE &#40;Transact-SQL&#41;](../../t-sql/statements/create-message-type-transact-sql.md)   
 [SEND &#40;Transact-SQL&#41;](../../t-sql/statements/send-transact-sql.md)   
 [CREATE QUEUE &#40;Transact-SQL&#41;](../../t-sql/statements/create-queue-transact-sql.md)   
 [ALTER QUEUE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-queue-transact-sql.md)   
 [DROP QUEUE &#40;Transact-SQL&#41;](../../t-sql/statements/drop-queue-transact-sql.md)  
  
  
