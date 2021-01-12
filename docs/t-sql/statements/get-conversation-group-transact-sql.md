---
description: GET CONVERSATION GROUP (Transact-SQL)
title: GET CONVERSATION GROUP (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 07/26/2017
ms.prod: sql
ms.prod_service: sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- GET
- CONVERSATION_GROUP_TSQL
- GET_TSQL
- GET_CONVERSATION_GROUP_TSQL
- GET CONVERSATION GROUP
- CONVERSATION GROUP
- GET CONVERSATION
- GET_CONVERSATION_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- GET CONVERSATION GROUP statement
- conversations [Service Broker], groups
ms.assetid: 4da8a855-33c0-43b2-a49d-527487cb3b5c
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: e54839bd7690b0417142fb4ac77994470f3a076e
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98091905"
---
# <a name="get-conversation-group-transact-sql"></a>GET CONVERSATION GROUP (Transact-SQL)
[!INCLUDE [SQL Server - ASDBMI](../../includes/applies-to-version/sql-asdbmi.md)]

  針對下一則要接收的訊息，傳回交談群組識別碼，並且針對含有該訊息的交談，鎖定交談群組。 您可以利用交談群組識別碼，先擷取交談狀態資訊，再擷取訊息本身。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql 
[ WAITFOR ( ]  
   GET CONVERSATION GROUP @conversation_group_id  
      FROM <queue>  
[ ) ] [ , TIMEOUT timeout ]  
[ ; ]  
  
<queue> ::=  
{ database_name.schema_name.queue_name | schema_name.queue_name | queue_name }  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 WAITFOR  
 指定如果目前沒有訊息，GET CONVERSATION GROUP 陳述式便會等待訊息到達佇列。  
  
 *\@conversation_group_id*  
 這是一個變數，用來儲存 GET CONVERSATION GROUP 陳述式所傳回的交談群組識別碼。 變數必須是 **uniqueidentifier** 類型。 如果沒有可用的交談群組，變數就設為 NULL。  
  
 FROM  
 指定佇列，從中取得交談群組。  
  
 *database_name*  
 這是包含要取得交談群組所在之佇列的資料庫名稱。 若未提供 *database_name*，預設為目前的資料庫。  
  
 *schema_name*  
 這是擁有要取得交談群組所在佇列的結構描述名稱。 若未提供 *schema_name*，預設為目前使用者的預設結構描述。  
  
 *queue_name*  
 這是取得交談群組所在的佇列名稱。  
  
 TIMEOUT *timeout*  
 指定 Service Broker 等待訊息到達佇列的時間長度 (以毫秒為單位)。 這個子句只適用於 WAITFOR 子句。 如果使用 WAITFOR 的陳述式不包含這個子句，或者 *timeout* 為 -1，則等候時間沒有限制。 如果已經逾時，GET CONVERSATION GROUP 會將 *\@conversation_group_id* 變數設定為 NULL。  
  
## <a name="remarks"></a>備註  
  
> [!IMPORTANT]  
>  如果 GET CONVERSATION GROUP 陳述式不是批次或預存程序中的第一個陳述式，就必須使用 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式結束字元 (也就是分號 ( **;** )) 來結束前一個陳述式。  
  
 如果 GET CONVERSATION GROUP 陳述式中指定的佇列無法使用，陳述式便會發生 [!INCLUDE[tsql](../../includes/tsql-md.md)] 錯誤而失敗。  
  
 此陳述式會傳回以下所有條件都成立的下一個交談群組：  
  
-   交談群組可以成功鎖定。  
  
-   交談群組在佇列中有可用的訊息。  
  
-   在符合之前列出之準則的所有交談群組中，此交談群組具有最高的優先權等級。 交談群組的優先權等級為指派給任何交談的最高優先權等級 (此交談必須為此群組的成員，而且在佇列中有訊息)。  
  
 如果接著再呼叫同一交易中的 GET CONVERSATION GROUP，可能會鎖定不只一個交談群組。 如果沒有可用的交談群組，陳述式便會傳回 NULL 當作交談群組的識別碼。  
  
 在指定 WAITFOR 子句時，陳述式會等候指定的逾期時間到達，或者等到出現可用的交談群組為止。 如果佇列在陳述式等候時被卸除，陳述式會立即傳回錯誤。  
  
 在使用者自訂函數中，GET CONVERSATION GROUP 無效。  
  
## <a name="permissions"></a>權限  
 若要從佇列取得交談群組識別碼，目前使用者必須有佇列的 RECEIVE 權限。  
  
## <a name="examples"></a>範例  
  
### <a name="a-getting-a-conversation-group-waiting-indefinitely"></a>A. 取得交談群組，無限期等候  
 下列範例會將 `@conversation_group_id` 設定為 `ExpenseQueue` 中下一個可用訊息的交談群組識別碼。 命令會等到訊息成為可用訊息為止。  
  
```sql  
DECLARE @conversation_group_id UNIQUEIDENTIFIER ;  
  
WAITFOR (  
 GET CONVERSATION GROUP @conversation_group_id  
     FROM ExpenseQueue  
) ;  
```  
  
### <a name="b-getting-a-conversation-group-waiting-one-minute"></a>B. 取得交談群組，等候一分鐘  
 下列範例會將 `@conversation_group_id` 設定為 `ExpenseQueue` 中下一個可用訊息的交談群組識別碼。 如果一分鐘內還沒有出現可用訊息，GET CONVERSATION GROUP 便會傳回，而不改變 `@conversation_group_id` 的值。  
  
```sql  
DECLARE @conversation_group_id UNIQUEIDENTIFIER  
  
WAITFOR (  
    GET CONVERSATION GROUP @conversation_group_id   
    FROM ExpenseQueue ),  
TIMEOUT 60000 ;  
```  
  
### <a name="c-getting-a-conversation-group-returning-immediately"></a>C. 取得交談群組，立即傳回  
 下列範例會將 `@conversation_group_id` 設定為 `ExpenseQueue` 中下一個可用訊息的交談群組識別碼。 如果沒有可用訊息，`GET CONVERSATION GROUP` 便會立即傳回，而不變更 `@conversation_group_id`。  
  
```sql  
DECLARE @conversation_group_id UNIQUEIDENTIFIER ;  
  
GET CONVERSATION GROUP @conversation_group_id  
FROM AdventureWorks.dbo.ExpenseQueue ;  
```  
  
## <a name="see-also"></a>另請參閱  
 [BEGIN DIALOG CONVERSATION &#40;Transact-SQL&#41;](../../t-sql/statements/begin-dialog-conversation-transact-sql.md)   
 [MOVE CONVERSATION &#40;Transact-SQL&#41;](../../t-sql/statements/move-conversation-transact-sql.md)  
  
  
