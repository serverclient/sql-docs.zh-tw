---
description: SEND (Transact-SQL)
title: SEND (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 07/26/2017
ms.prod: sql
ms.prod_service: sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- SEND_ON_CONVERSATION_TSQL
- ON_CONVERSATION_TSQL
- SEND
- SEND_TSQL
- SEND ON CONVERSATION
- ON CONVERSATION
dev_langs:
- TSQL
helpviewer_keywords:
- conversations [Service Broker], message sending
- SEND statement
- messages [Service Broker], sending
- sending messages
ms.assetid: b6e66aeb-1714-4c2b-b7c2-d386d77b0d46
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 25ef934ae04ca8ba9cc9c4dee9f488341a1eb352
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98091828"
---
# <a name="send-transact-sql"></a>SEND (Transact-SQL)
[!INCLUDE [SQL Server - ASDBMI](../../includes/applies-to-version/sql-asdbmi.md)]

利用一個或多個現有的交談來傳送訊息。  
  
![文章連結圖示](../../database-engine/configure-windows/media/topic-link.gif "文章連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
  
SEND  
   ON CONVERSATION [(]conversation_handle [,.. @conversation_handle_n][)]  
   [ MESSAGE TYPE message_type_name ]  
   [ ( message_body_expression ) ]  
[ ; ]  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
ON CONVERSATION *conversation_handle [.. @conversation_handle_n]*  
指定訊息所屬的交談。 *conversation_handle* 必須包含有效的交談識別碼。 相同的交談控制代碼不可使用超過一次。  
  
MESSAGE TYPE *message_type_name*  
指定所傳送之訊息的訊息類型。 這個訊息類型必須包括在這些交談所用的服務合約中。 這些合約必須允許從交談的這一端傳送這個訊息類型。 例如，交談的目標服務只能傳送合約中指定為 SENT BY TARGET 或 SENT BY ANY 的訊息。 如果省略這個子句，訊息的訊息類型便是 DEFAULT。  
  
*message_body_expression*  
提供代表訊息主體的運算式。 *message_body_expression* 為選擇性。 不過，如果 *message_body_expression* 存在，則運算式的類型必須能夠轉換成 **varbinary(max)**。 運算式不能是 NULL。 如果省略這個子句，訊息主體就是空白的。  
  
## <a name="remarks"></a>備註  
  
> [!IMPORTANT]  
>  如果 SEND 陳述式不是批次或預存程序中的第一個陳述式，就必須使用分號 (;) 來終止前一個陳述式。  
  
SEND 陳述式會將一個或多個 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 交談某一端上服務的訊息傳輸到這些交談另一端上的服務。 然後會使用 RECEIVE 陳述式來從與目標服務有關的佇列中擷取傳送的訊息。  
  
提供給 ON CONVERSATION 子句的交談控制代碼來自以下三個來源的其中一個：  
  
- 當所傳送訊息不是要回應從另一個服務所接收而來的訊息時，請使用從建立此交談之 BEGIN DIALOG 陳述式傳回的交談控制代碼。  
  
- 當所傳送訊息是要回應之前從另一個服務所接收而來的訊息時，請使用傳回原始訊息之 RECEIVE 陳述式所傳回的交談控制代碼。  
  
- 包含 SEND 陳述式的程式碼有時會與包含 BEGIN DIALOG 或 RECEIVE 陳述式 (這些陳述式提供交談控制代碼) 的程式碼分開。 在這些情況下，交談控制代碼必須是狀態資訊內的其中一個資料項目，而該狀態資訊會傳遞給包含 SEND 陳述式的程式碼。  
  
傳送給其他 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 執行個體中之服務的訊息會儲存在目前資料庫的傳輸佇列中，直到可以將這些訊息傳輸到遠端執行個體內的服務佇列為止。 傳送至相同 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 執行個體內之服務的訊息，將會直接放入與該服務相關的佇列中。 如果某個狀況阻止將本機訊息直接放入目標服務佇列中，則傳輸佇列可以儲存該訊息，直到解決該狀況為止。 發生這些狀況的例子包括某些錯誤類型或是目標服務佇列不在使用中。 您可以使用 **sys.transmission_queue** 系統檢視來查看傳輸佇列中的訊息。  
  
SEND 是不可部分完成的陳述式。 如果 SEND 陳述式傳送多個交談的訊息並失敗 (例如當交談處於錯誤狀態時)，則不會將訊息儲存在傳輸佇列中或放入任何目標服務佇列。  
  
Service Broker 會最佳化相同 SEND 陳述式中多個交談上所傳送訊息的儲存和傳輸。  
  
某個執行個體之傳輸佇列中的訊息是依照下列順序傳輸的：  
  
- 其相關之交談端點的優先權等級。  
  
- 其在優先權等級內的交談中傳送順序。  
  
如果 HONOR_BROKER_PRIORITY 資料庫選項設定為 ON，則在交談優先順序中所指定優先順序等級只會套用至傳輸佇列中的訊息。 如果 HONOR_BROKER_PRIORITY 設定為 OFF，則該資料庫之傳輸佇列中的所有訊息都會使用預設優先順序等級 5 加以指派。 優先順序等級不會套用到 SEND，此狀況下的訊息會直接放入相同 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 執行個體內的服務佇列。  
  
SEND 陳述式會分別鎖定傳送訊息的每一個交談，以確保每個交談的排序傳遞。  
  
SEND 在使用者自訂函式中無效。  
  
## <a name="permissions"></a>權限  
若要傳送訊息，目前的使用者必須有傳送訊息之每個服務佇列的 RECEIVE 權限。  
  
## <a name="examples"></a>範例  
下列範例會啟動一個對話，且會在對話中傳送一則 XML 訊息。 為了傳送訊息，此範例會將 XML 物件轉換成 **varbinary(max)**。  
  
```sql
DECLARE @dialog_handle UNIQUEIDENTIFIER,  
        @ExpenseReport XML ;  
  
SET @ExpenseReport = < construct message as appropriate for the application > ;  
  
BEGIN DIALOG @dialog_handle  
FROM SERVICE [//Adventure-Works.com/Expenses/ExpenseClient]  
TO SERVICE '//Adventure-Works.com/Expenses'  
ON CONTRACT [//Adventure-Works.com/Expenses/ExpenseProcessing] ;  
  
SEND ON CONVERSATION @dialog_handle  
    MESSAGE TYPE [//Adventure-Works.com/Expenses/SubmitExpense]  
    (@ExpenseReport) ;  
```  
  
下列範例會啟動三個對話，且會在每個對話中傳送一則 XML 訊息。  
  
```sql
DECLARE @dialog_handle1 UNIQUEIDENTIFIER,  
        @dialog_handle2 UNIQUEIDENTIFIER,  
        @dialog_handle3 UNIQUEIDENTIFIER,  
        @OrderMsg XML ;  
  
SET @OrderMsg = < construct message as appropriate for the application > ;  
  
BEGIN DIALOG @dialog_handle1  
FROM SERVICE [//InitiatorDB/InitiatorService]  
TO SERVICE '//TargetDB1/TargetService'  
ON CONTRACT [//AllDBs/OrderProcessing] ;  
  
BEGIN DIALOG @dialog_handle2  
FROM SERVICE [//InitiatorDB/InitiatorService]  
TO SERVICE '//TargetDB2/TargetService'  
ON CONTRACT [//AllDBs/OrderProcessing] ;  
  
BEGIN DIALOG @dialog_handle3  
FROM SERVICE [//InitiatorDB/InitiatorService]  
TO SERVICE '//TargetDB3/TargetService'  
ON CONTRACT [//AllDBs/OrderProcessing] ;  
  
SEND ON CONVERSATION (@dialog_handle1, @dialog_handle2, @dialog_handle3)  
    MESSAGE TYPE [//AllDBs/OrderMsg]  
    (@OrderMsg) ;  
```  
  
## <a name="see-also"></a>另請參閱  
[BEGIN DIALOG CONVERSATION &#40;Transact-SQL&#41;](../../t-sql/statements/begin-dialog-conversation-transact-sql.md)   
[END CONVERSATION &#40;Transact-SQL&#41;](../../t-sql/statements/end-conversation-transact-sql.md)   
[RECEIVE &#40;Transact-SQL&#41;](../../t-sql/statements/receive-transact-sql.md)   
[sys.transmission_queue &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-transmission-queue-transact-sql.md)  
  
  
