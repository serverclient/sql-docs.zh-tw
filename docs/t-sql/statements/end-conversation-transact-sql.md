---
description: END CONVERSATION (Transact-SQL)
title: END CONVERSATION (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 07/26/2017
ms.prod: sql
ms.prod_service: sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- END DIALOG
- END CONVERSATION
- END_DIALOG_TSQL
- END_CONVERSATION_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- errors [Service Broker], conversations
- dialogs [Service Broker], ending
- closing conversations
- END CONVERSATION statement
- conversations [Service Broker], ending
- ending conversations [SQL Server]
ms.assetid: 4415a126-cd22-4a5e-b84a-d8c68515c83b
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 16dd2a5201d001a54103408386036cee39040523
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98093430"
---
# <a name="end-conversation-transact-sql"></a>END CONVERSATION (Transact-SQL)
[!INCLUDE [SQL Server - ASDBMI](../../includes/applies-to-version/sql-asdbmi.md)]

  結束現有交談的一端。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
END CONVERSATION conversation_handle  
   [   [ WITH ERROR = failure_code DESCRIPTION = 'failure_text' ]  
     | [ WITH CLEANUP ]  
    ]  
[ ; ]  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *conversation_handle*  
 這是要結束的交談之交談控制代碼。  
  
 WITH ERROR =*failure_code*  
 這是錯誤碼。 *failure_code* 的型別是 **int**。失敗碼是一種使用者定義程式碼，包含在傳給交談另一端的錯誤訊息中。 失敗碼必須大於 0。  
  
 DESCRIPTION =*failure_text*  
 這是錯誤訊息。 *failure_text* 的型別是 **nvarchar(3000)**。 失敗文字是一個使用者自訂文字，包含在傳給交談另一端的錯誤訊息中。  
  
 WITH CLEANUP   
 移除無法正常完成之交談這一端的所有訊息和目錄檢視項目。 交談的另一端將不會收到此清除通知。 [!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會卸除交談端點，以及這項交談在傳輸佇列和服務佇列中的所有訊息。 管理員可使用這個選項來移除無法正常完成的交談。 例如，如果遠端服務已永久移除，管理員可以使用 WITH CLEANUP 來移除與這項服務的交談。 請勿在 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 應用程式的程式碼中使用 WITH CLEANUP。 如果在接收端點認可收到訊息之前，END CONVERSATION WITH CLEANUP 已在執行中，則傳送端點將會再次傳送此訊息。 這樣可能會重新執行對話。  
  
## <a name="remarks"></a>備註  
 結束交談會鎖定提供之 *conversation_handle* 所屬的交談群組。 當交談結束時，[!INCLUDE[ssSB](../../includes/sssb-md.md)] 會移除這項交談在服務佇列中的所有訊息。  
  
 交談結束之後，應用程式就無法再傳送或接收這項交談的訊息。 交談的兩個參與者都必須呼叫 END CONVERSATION，交談才會完成。 如果 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 沒有收到交談另一方參與者發出的結束對話訊息或錯誤訊息，[!INCLUDE[ssSB](../../includes/sssb-md.md)] 會通知交談的另一方參與者交談已經結束。 在這個情況下，雖然交談的交談控制代碼已不再有效，但交談的端點仍會維持使用中的狀態，直到主控遠端服務的執行個體確認這則訊息為止。  
  
 如果 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 尚未處理交談的結束對話或錯誤訊息，[!INCLUDE[ssSB](../../includes/sssb-md.md)] 會通知交談的遠端，告知交談已經結束。 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 傳給遠端服務的訊息會隨著指定的選項而不同：  
  
-   如果交談結束時沒有任何錯誤，而且與遠端服務的交談仍然在進行中，則 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 會將 `https://schemas.microsoft.com/SQL/ServiceBroker/EndDialog` 類型的訊息傳送給遠端服務。 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 會依照交談順序將此訊息加入到傳輸佇列。 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 在傳送這個訊息之前，會先傳送此交談目前位於傳輸佇列中的所有訊息。  
  
-   如果交談結束時出現錯誤，而且與遠端服務的交談仍然在進行中，則 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 會將 `https://schemas.microsoft.com/SQL/ServiceBroker/Error` 類型的訊息傳送給遠端服務。 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 會卸除此交談目前位於傳輸佇列中的任何其他訊息。  
  
-   WITH CLEANUP 子句可讓資料庫管理員移除無法正常完成的交談。 這個選項會移除交談的所有訊息和目錄檢視項目。 請注意，在這個情況下，交談的遠端不會收到任何交談已結束的訊息，可能也不會收到應用程式已送出而尚未通過網路傳輸的訊息。 除非交談無法正常完成，否則，請避免使用這個選項。  
  
 在交談結束之後，指定交談控制代碼的 [!INCLUDE[tsql](../../includes/tsql-md.md)] SEND 陳述式會造成 [!INCLUDE[tsql](../../includes/tsql-md.md)] 錯誤。 如果此交談有抵達的訊息是從交談的另一端所發出，[!INCLUDE[ssSB](../../includes/sssb-md.md)] 會捨棄這些訊息。  
  
 如果交談結束時，遠端服務仍有尚未傳送的交談訊息，遠端服務會卸除這些尚未傳送的訊息。 這並不是錯誤，遠端服務也不會收到任何訊息已卸除的通知。  
  
 WITH ERROR 子句指定的失敗碼必須是正數。 負數保留給 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 錯誤訊息使用。  
  
 在使用者自訂函數中，END CONVERSATION 無效。  
  
## <a name="permissions"></a>權限  
 若要結束使用中的交談，目前使用者必須是交談的擁有者、系統管理員 (sysadmin) 固定伺服器角色的成員，或 db_owner 固定資料庫角色的成員。  
  
 系統管理員 (sysadmin) 固定伺服器角色的成員或 db_owner 固定資料庫角色的成員，可以利用 WITH CLEANUP 來移除已完成之交談的中繼資料。  
  
## <a name="examples"></a>範例  
  
### <a name="a-ending-a-conversation"></a>A. 結束交談  
 下列範例會結束 `@dialog_handle` 所指定的對話。  
  
```sql 
END CONVERSATION @dialog_handle ;  
```  
  
### <a name="b-ending-a-conversation-with-an-error"></a>B. 結束交談時發生錯誤  
 下列範例會在進行處理的陳述式報告錯誤時，結束 `@dialog_handle` 所指定的對話，並傳回錯誤。 請注意，這是最簡單的錯誤處理方式，可能不適合某些應用程式。  
  
```sql  
DECLARE @dialog_handle UNIQUEIDENTIFIER,  
        @ErrorSave INT,  
        @ErrorDesc NVARCHAR(100) ;  
BEGIN TRANSACTION ;  
  
<receive and process message>  
  
SET @ErrorSave = @@ERROR ;  
  
IF (@ErrorSave <> 0)  
  BEGIN  
      ROLLBACK TRANSACTION ;  
      SET @ErrorDesc = N'An error has occurred.' ;  
      END CONVERSATION @dialog_handle   
      WITH ERROR = @ErrorSave DESCRIPTION = @ErrorDesc ;  
  END  
ELSE  
  
COMMIT TRANSACTION ;  
```  
  
### <a name="c-cleaning-up-a-conversation-that-cannot-complete-normally"></a>C. 清除無法正常完成的交談  
 下列範例會結束 `@dialog_handle` 所指定的對話。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會立即移除服務佇列和傳輸佇列中的所有訊息，且不會通知遠端服務。 由於在結束對話時進行清除，並不會通知遠端服務，因此，您只應在遠端服務無法接收 **EndDialog** 或 **Error** 訊息時，才使用這個方式。  
  
```sql  
END CONVERSATION @dialog_handle WITH CLEANUP ;  
```  
  
## <a name="see-also"></a>另請參閱  
 [BEGIN CONVERSATION TIMER &#40;Transact-SQL&#41;](../../t-sql/statements/begin-conversation-timer-transact-sql.md)   
 [BEGIN DIALOG CONVERSATION &#40;Transact-SQL&#41;](../../t-sql/statements/begin-dialog-conversation-transact-sql.md)   
 [sys.conversation_endpoints &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-conversation-endpoints-transact-sql.md)  
  
  
