---
description: BEGIN DIALOG CONVERSATION (Transact-SQL)
title: BEGIN DIALOG CONVERSATION (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 07/26/2017
ms.prod: sql
ms.prod_service: sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- DIALOG CONVERSATION
- DIALOG
- BEGIN_DIALOG_TSQL
- BEGIN_DIALOG_CONVERSATION_TSQL
- DIALOG_CONVERSATION_TSQL
- DIALOG_TSQL
- BEGIN DIALOG
- BEGIN DIALOG CONVERSATION
dev_langs:
- TSQL
helpviewer_keywords:
- conversations [Service Broker]
- beginning dialogs
- dialogs [Service Broker], beginning
- BEGIN DIALOG CONVERSATION statement
- cryptography [SQL Server], conversations
- opening conversations
- encryption [SQL Server], conversations
- starting conversations
ms.assetid: 8e814f9d-77c1-4906-b8e4-668a86fc94ba
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: f1c32ad7e6a970b9ebb50dc29e7dda767f5e4f40
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98098496"
---
# <a name="begin-dialog-conversation-transact-sql"></a>BEGIN DIALOG CONVERSATION (Transact-SQL)
[!INCLUDE [SQL Server - ASDBMI](../../includes/applies-to-version/sql-asdbmi.md)]

  開始服務之間的對話。 對話是在兩個服務之間進行精確單次循序傳訊的交談。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
BEGIN DIALOG [ CONVERSATION ] @dialog_handle  
   FROM SERVICE initiator_service_name  
   TO SERVICE 'target_service_name'  
       [ , { 'service_broker_guid' | 'CURRENT DATABASE' }]   
   [ ON CONTRACT contract_name ]  
   [ WITH  
   [  { RELATED_CONVERSATION = related_conversation_handle   
      | RELATED_CONVERSATION_GROUP = related_conversation_group_id } ]   
   [ [ , ] LIFETIME = dialog_lifetime ]   
   [ [ , ] ENCRYPTION = { ON | OFF }  ] ]  
[ ; ]  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 **@** _dialog_handle_  
 這是一個變數，用來儲存 BEGIN DIALOG CONVERSATION 陳述式傳回的新對話之系統產生對話控制代碼。 變數必須是 **uniqueidentifier** 類型。  
  
 FROM SERVICE *initiator_service_name*  
 指定起始對話的服務。 指定的名稱必須是目前資料庫中服務的名稱。 指定給起始端服務的佇列會接收目標服務傳回的訊息及 Service Broker 為了這項交談所建立的訊息。  
  
 TO SERVICE **'** _target_service_name_ **'**  
 指定起始對話時所指向的目標服務。 *target_service_name* 是 **nvarchar(256)** 類型。 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 使用逐位元組的比較方式來比對 *target_service_name* 字串。 換言之，這項比較會區分大小寫，且不會考慮目前的定序。  
  
 *service_broker_guid*  
 指定主控目標服務的資料庫。 若有一個以上的資料庫主控目標服務的執行個體，您可以提供 *service_broker_guid* 來與特定資料庫通訊。  
  
 *service_broker_guid* 是 **nvarchar(128)** 類型。 若要尋找資料庫的 *service_broker_guid*，請在資料庫中執行下列查詢：  
  
```sql  
SELECT service_broker_guid  
FROM sys.databases  
WHERE database_id = DB_ID() ;  
```  
  
> [!NOTE]  
>  自主資料庫無法使用這個選項。  
  
 **'** CURRENT DATABASE **'**  
 指定交談使用目前資料庫的 *service_broker_guid*。  
  
 ON CONTRACT *contract_name*  
 指定這項交談遵照的合約。 合約必須在目前的資料庫中。 如果目標服務不接受指定合約的新交談，[!INCLUDE[ssSB](../../includes/sssb-md.md)] 會在交談上傳回錯誤訊息。 當省略這個子句時，交談會遵照名稱為 **DEFAULT** 的合約。  
  
 RELATED_CONVERSATION **=** _related_conversation_handle_  
 指定新對話要加入其中的現有交談群組。 當出現這個子句時，新對話與 *related_conversation_handle* 指定的對話屬於同一個交談群組。 *related_conversation_handle* 的類型必須可以隱含地轉換成 **uniqueidentifier** 類型。 如果 *related_conversation_handle* 並未參考現有的對話，陳述式將會失敗。  
  
 RELATED_CONVERSATION_GROUP **=** _related_conversation_group_id_  
 指定新對話要加入其中的現有交談群組。 當出現這個子句時，新對話將會加入 *related_conversation_group_id* 指定的交談群組。 *related_conversation_group_id* 的類型必須可以隱含地轉換成 **uniqueidentifier** 類型。 如果 *related_conversation_group_id* 並未參考現有的交談群組，Service Broker 會利用指定的 *related_conversation_group_id* 來建立新交談群組，並使新對話與該交談群組產生關聯。  
  
 LIFETIME **=** _dialog_lifetime_  
 指定對話維持開啟狀態的最大時間量。 為了使對話順利完成，兩個端點必須在存留期間過期之前明確地結束對話。 *dialog_lifetime* 值必須以秒為單位來表示。 存留期間的類型是 **int**。當沒有指定 LIFETIME 子句時，對話存留期間是 **int** 資料類型的最大值。  
  
 ENCRYPTION  
 指定當這個對話方塊所傳送和接收訊息在 [!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的執行個體之外傳送時，是否必須加密。 必須加密的對話是「安全的對話」。 當 ENCRYPTION = ON 且未設定支援加密所需要的憑證時，[!INCLUDE[ssSB](../../includes/sssb-md.md)] 會在交談上傳回錯誤訊息。 當 ENCRYPTION = OFF 時，如果設定 *target_service_name* 的遠端服務繫結，便會使用加密；否則，會以不加密的方式來傳送訊息。 如果沒有這個子句，預設值便是 ON。  
  
> [!NOTE]  
>  在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的相同執行個體中各項服務之間交換的訊息，絕對不會加密。 不過，如果交談服務在不同資料庫中，使用加密的交談仍需要資料庫主要金鑰以及用於加密的憑證。 如此一來，當進行交談時，如果其中一個資料庫移到不同的執行個體，仍可以繼續交談。  
  
## <a name="remarks"></a>備註  
 所有訊息都是交談的一部份。 因此，起始服務必須先開始一項與目標服務的交談，才能將訊息傳給目標服務。 BEGIN DIALOG CONVERSATION 陳述式指定的資訊與郵件的地址相似；[!INCLUDE[ssSB](../../includes/sssb-md.md)] 會利用這項資訊將訊息傳給正確的服務。 TO SERVICE 子句指定的服務是訊息要送往的地址。 FROM SERVICE 子句指定的服務是回覆訊息的傳回地址。  
  
 交談目標不需要呼叫 BEGIN DIALOG CONVERSATION。 當起始端所發出之交談中的第一則訊息抵達時，[!INCLUDE[ssSB](../../includes/sssb-md.md)] 會在目標資料庫中建立一項交談。  
  
 開始對話，會在起始服務的資料庫中建立交談端點，但不會建立網路連接來通往主控目標服務的執行個體。 傳送第一則訊息之後，[!INCLUDE[ssSB](../../includes/sssb-md.md)] 才會與對話的目標建立通訊。  
  
 當 BEGIN DIALOG CONVERSATION 陳述式未指定相關交談或相關交談群組時，[!INCLUDE[ssSB](../../includes/sssb-md.md)] 會為這項新交談建立一個新的交談群組。  
  
 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 不允許將交談任意分組。 交談群組中的所有交談，都必須以 FROM 子句所指定的服務為交談的起始端或目標。  
  
 BEGIN DIALOG CONVERSATION 命令會鎖定包含所傳回之 *dialog_handle* 的交談群組。 當命令包含 RELATED_CONVERSATION_GROUP 子句時，*dialog_handle* 的交談群組就是 *related_conversation_group_id* 參數所指定的交談群組。 當命令包含 RELATED_CONVERSATION 子句時，*dialog_handle* 的交談群組就是與所指定之 *related_conversation_handle* 相關聯的交談群組。  
  
 在使用者自訂函數中，BEGIN DIALOG CONVERSATION 無效。  
  
## <a name="permissions"></a>權限  
 若要開始一段對話，目前的使用者必須有命令的 FROM 子句所指定服務佇列的 RECEIVE 權限，以及指定合約的 REFERENCES 權限。  
  
## <a name="examples"></a>範例  
  
### <a name="a-beginning-a-dialog"></a>A. 開始對話  
 下列範例會開始對話交談，並將對話的識別碼儲存在 `@dialog_handle.` 中。`//Adventure-Works.com/ExpenseClient` 服務是對話的起始端，`//Adventure-Works.com/Expenses` 服務是對話的目標。 對話遵照 `//Adventure-Works.com/Expenses/ExpenseSubmission` 合約。  
  
```sql  
DECLARE @dialog_handle UNIQUEIDENTIFIER ;  
  
BEGIN DIALOG CONVERSATION @dialog_handle  
   FROM SERVICE [//Adventure-Works.com/ExpenseClient]  
   TO SERVICE '//Adventure-Works.com/Expenses'  
   ON CONTRACT [//Adventure-Works.com/Expenses/ExpenseSubmission] ;  
```  
  
### <a name="b-beginning-a-dialog-with-an-explicit-lifetime"></a>B. 開始含有明確存留期間的對話  
 下列範例會開始對話交談，並將對話的識別碼儲存在 `@dialog_handle` 中。 `//Adventure-Works.com/ExpenseClient` 服務是對話的起始端，`//Adventure-Works.com/Expenses` 服務是對話的目標。 對話遵照 `//Adventure-Works.com/Expenses/ExpenseSubmission` 合約。 如果 END CONVERSATION 命令沒有在 `60` 秒內關閉對話，Broker 會結束對話並傳回錯誤。  
  
```sql  
DECLARE @dialog_handle UNIQUEIDENTIFIER ;  
  
BEGIN DIALOG CONVERSATION @dialog_handle  
   FROM SERVICE [//Adventure-Works.com/ExpenseClient]  
   TO SERVICE '//Adventure-Works.com/Expenses'  
   ON CONTRACT [//Adventure-Works.com/Expenses/ExpenseSubmission]  
   WITH LIFETIME = 60 ;  
```  
  
### <a name="c-beginning-a-dialog-with-a-specific-broker-instance"></a>C. 利用特定 Broker 執行個體來開始對話  
 下列範例會開始對話交談，並將對話的識別碼儲存在 `@dialog_handle` 中。 `//Adventure-Works.com/ExpenseClient` 服務是對話的起始端，`//Adventure-Works.com/Expenses` 服務是對話的目標。 對話遵照 `//Adventure-Works.com/Expenses/ExpenseSubmission` 合約。 Broker 會將這個對話的訊息傳送給 GUID `a326e034-d4cf-4e8b-8d98-4d7e1926c904.` 所識別的 Broker。  
  
```sql  
DECLARE @dialog_handle UNIQUEIDENTIFIER ;  
  
BEGIN DIALOG CONVERSATION @dialog_handle  
   FROM SERVICE [//Adventure-Works.com/ExpenseClient]  
   TO SERVICE '//Adventure-Works.com/Expenses',   
              'a326e034-d4cf-4e8b-8d98-4d7e1926c904'  
   ON CONTRACT [//Adventure-Works.com/Expenses/ExpenseSubmission] ;  
```  
  
### <a name="d-beginning-a-dialog-and-relating-it-to-an-existing-conversation-group"></a>D. 開始對話，將這段對話關聯於現有的交談群組  
 下列範例會開始對話交談，並將對話的識別碼儲存在 `@dialog_handle` 中。 `//Adventure-Works.com/ExpenseClient` 服務是對話的起始端，`//Adventure-Works.com/Expenses` 服務是對話的目標。 對話遵照 `//Adventure-Works.com/Expenses/ExpenseSubmission` 合約。 Broker 會將對話關聯於 `@conversation_group_id` 所識別的交談群組，不會建立新的交談群組。  
  
```sql  
DECLARE @dialog_handle UNIQUEIDENTIFIER ;  
DECLARE @conversation_group_id UNIQUEIDENTIFIER ;  
  
SET @conversation_group_id = <retrieve conversation group ID from database>  
  
BEGIN DIALOG CONVERSATION @dialog_handle  
   FROM SERVICE [//Adventure-Works.com/ExpenseClient]  
   TO SERVICE '//Adventure-Works.com/Expenses'  
   ON CONTRACT [//Adventure-Works.com/Expenses/ExpenseSubmission]  
   WITH RELATED_CONVERSATION_GROUP = @conversation_group_id ;  
```  
  
### <a name="e-beginning-a-dialog-with-an-explicit-lifetime-and-relating-the-dialog-to-an-existing-conversation"></a>E. 開始含有明確存留期間的對話，並將這段對話關聯於現有的交談  
 下列範例會開始對話交談，並將對話的識別碼儲存在 `@dialog_handle` 中。 `//Adventure-Works.com/ExpenseClient` 服務是對話的起始端，`//Adventure-Works.com/Expenses` 服務是對話的目標。 對話遵照 `//Adventure-Works.com/Expenses/ExpenseSubmission` 合約。 新對話與 `@existing_conversation_handle` 屬於相同的交談群組。 如果 END CONVERSATION 命令沒有在 `600` 秒內關閉對話，[!INCLUDE[ssSB](../../includes/sssb-md.md)] 會結束對話並傳回錯誤。  
  
```sql  
DECLARE @dialog_handle UNIQUEIDENTIFIER  
DECLARE @existing_conversation_handle UNIQUEIDENTIFIER  
  
SET @existing_conversation_handle = <retrieve conversation handle from database>  
  
BEGIN DIALOG CONVERSATION @dialog_handle  
   FROM SERVICE [//Adventure-Works.com/ExpenseClient]  
   TO SERVICE '//Adventure-Works.com/Expenses'  
   ON CONTRACT [//Adventure-Works.com/Expenses/ExpenseSubmission]  
   WITH RELATED_CONVERSATION = @existing_conversation_handle  
   LIFETIME = 600 ;  
```  
  
### <a name="f-beginning-a-dialog-with-optional-encryption"></a>F. 利用選擇性的加密來開始對話  
 下列範例會開始對話，並將對話的識別碼儲存在 `@dialog_handle` 中。 `//Adventure-Works.com/ExpenseClient` 服務是對話的起始端，`//Adventure-Works.com/Expenses` 服務是對話的目標。 對話遵照 `//Adventure-Works.com/Expenses/ExpenseSubmission` 合約。 這個範例的交談可讓訊息在無法加密的情況下，在網路中以不加密的方式來傳送。  
  
```sql  
DECLARE @dialog_handle UNIQUEIDENTIFIER  
  
BEGIN DIALOG CONVERSATION @dialog_handle  
   FROM SERVICE [//Adventure-Works.com/ExpenseClient]  
   TO SERVICE '//Adventure-Works.com/Expenses'  
   ON CONTRACT [//Adventure-Works.com/Expenses/ExpenseSubmission]  
   WITH ENCRYPTION = OFF ;  
```  
  
## <a name="see-also"></a>另請參閱  
 [BEGIN CONVERSATION TIMER &#40;Transact-SQL&#41;](../../t-sql/statements/begin-conversation-timer-transact-sql.md)   
 [END CONVERSATION &#40;Transact-SQL&#41;](../../t-sql/statements/end-conversation-transact-sql.md)   
 [MOVE CONVERSATION &#40;Transact-SQL&#41;](../../t-sql/statements/move-conversation-transact-sql.md)   
 [sys.conversation_endpoints &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-conversation-endpoints-transact-sql.md)  
  
  
