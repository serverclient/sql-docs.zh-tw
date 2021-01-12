---
description: CREATE BROKER PRIORITY (Transact-SQL)
title: CREATE BROKER PRIORITY (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/06/2017
ms.prod: sql
ms.prod_service: sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- CREATE BROKER PRIORITY
- PRIORITY_TSQL
- CREATE_BROKER_PRIORITY_TSQL
- PRIORITY
- CREATE BROKER
- BROKER_TSQL
- BROKER
- CREATE_BROKER_TSQL
- BROKER PRIORITY
- BROKER_PRIORITY_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- CREATE BROKER PRIORITY statement
ms.assetid: e0bbebfa-b7c3-4825-8169-7281f7e6de98
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: b027d1e0472a2154eaff8540e5dcc4b93f7be930
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98100205"
---
# <a name="create-broker-priority-transact-sql"></a>CREATE BROKER PRIORITY (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  定義優先權等級以及用來判斷哪一個 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 交談要指派給此優先權等級的準則集。 此優先權等級會指派給使用相同合約與服務組合 (在此交談優先權內指定) 的任何交談端點。 優先權的範圍值是從 1 (低) 到 10 (高)。 預設值為 5。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
CREATE BROKER PRIORITY ConversationPriorityName  
FOR CONVERSATION  
[ SET ( [ CONTRACT_NAME = {ContractName | ANY } ]  
        [ [ , ] LOCAL_SERVICE_NAME = {LocalServiceName | ANY } ]  
        [ [ , ] REMOTE_SERVICE_NAME = {'RemoteServiceName' | ANY } ]  
        [ [ , ] PRIORITY_LEVEL = {PriorityValue | DEFAULT } ]  
       )  
]  
[;]  
  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *ConversationPriorityName*  
 指定此交談優先權的名稱。 此名稱在目前資料庫中必須是唯一的，且必須符合[!INCLUDE[ssDE](../../includes/ssde-md.md)] [識別碼](../../relational-databases/databases/database-identifiers.md)的規則。  
  
 SET  
 指定用來判斷交談優先權是否套用到交談的準則。 如果有指定，SET 至少必須包含一個準則：CONTRACT_NAME、LOCAL_SERVICE_NAME、REMOTE_SERVICE_NAME 或 PRIORITY_LEVEL。 如果未指定 SET，所有的三個準則都會設定預設值。  
  
 CONTRACT_NAME = {*ContractName* | **ANY**}  
 指定合約名稱，以用來做為判斷交談優先權是否要套用到交談的準則。 *ContractName* 是 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 識別碼，而且必須指定目前資料庫中的合約名稱。  
  
 *ContractName*  
 指定只能將交談優先權套用到啟動交談之 BEGIN DIALOG 陳述式指定了 ON CONTRACT *ContractName* 的交談上。  
  
 ANY  
 指定交談優先權可以套用到任何交談，不論它使用哪一個合約。  
  
 預設值為 ANY。  
  
 LOCAL_SERVICE_NAME = {*LocalServiceName* | **ANY**}  
 指定用來判斷交談優先權是否套用到交談端點之準則的服務名稱。  
  
 *LocalServiceName* 是 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 識別碼。 它必須指定目前資料庫中的服務名稱。  
  
 *LocalServiceName*  
 指定交談優先權可以套用到以下項目：  
  
-   起始端服務名稱符合 *LocalServiceName* 的任何起始端交談端點。  
  
-   目標服務名稱符合 *LocalServiceName* 的任何目標交談端點。  
  
 ANY  
 -   指定交談優先權可以套用到任何交談端點，不論此端點使用的本機服務名稱為何。  
  
 預設值為 ANY。  
  
 REMOTE_SERVICE_NAME = {'*RemoteServiceName*' | **ANY**}  
 指定用來判斷交談優先權是否套用到交談端點之準則的服務名稱。  
  
 *RemoteServiceName* 是類型 **nvarchar(256)** 的常值。 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 會利用逐位元組的比較方式來比對 *RemoteServiceName* 字串。 這項比較會區分大小寫，且不會考慮目前的定序。 目標服務可以在目前的 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 執行個體或遠端 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 執行個體中。  
  
 '*RemoteServiceName*'  
 指定交談優先權可以套用到以下項目：  
  
-   關聯目標服務名稱符合 *RemoteServiceName* 的任何起始端交談端點。  
  
-   關聯起始端服務名稱符合 *RemoteServiceName* 的任何目標交談端點。  
  
 ANY  
 指定交談優先權可套用到任何交談端點，不論與此端點有關的遠端服務名稱為何。  
  
 預設值為 ANY。  
  
 PRIORITY_LEVEL = { *PriorityValue* | **DEFAULT** }  
 指定優先權，以指派使用交談優先權中指定之合約和服務的任何交談端點。 *PriorityValue* 必須是從 1 (最低優先權) 到 10 (最高優先權) 的整數常值。 預設值為 5。  
  
## <a name="remarks"></a>備註  
 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 會指派優先權等級給交談端點。 優先權等級會控制與此端點有關之作業的優先權。 每個交談都具有兩個交談端點：  
  
-   起始端交談端點會讓交談的某一端與起始端服務和起始端佇列產生關聯。 起始端交談端點是在執行 BEGIN DIALOG 陳述式時建立的。 與起始端交談端點相關聯的作業包括下列項目：  
  
    -   從起始端服務傳送。  
  
    -   從起始端佇列接收。  
  
    -   從起始端佇列取得下一個交談群組。  
  
-   目標交談端點會讓交談的另一端與目標服務和佇列產生關聯。 當使用此交談來傳送訊息給目標佇列時，即會建立目標交談端點。 與目標交談端點相關聯的作業包括下列項目：  
  
    -   從目標佇列接收。  
  
    -   從目標服務傳送。  
  
    -   從目標佇列取得下一個交談群組。  
  
 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 會在建立交談端點時，指派交談優先權等級。 交談端點會保留優先權等級，直到交談結束為止。 新的優先權或現有優先權的變更不會套用至現有的交談。  
  
 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 會根據合約和服務準則與端點屬性最相符的交談優先權，將優先權等級指派給交談端點。 下表將顯示相符優先順序：  
  
|作業合約|作業本機服務|作業遠端服務|  
|------------------------|-----------------------------|------------------------------|  
|*ContractName*|*LocalServiceName*|*RemoteServiceName*|  
|*ContractName*|*LocalServiceName*|ANY|  
|*ContractName*|ANY|*RemoteServiceName*|  
|*ContractName*|ANY|ANY|  
|ANY|*LocalServiceName*|*RemoteServiceName*|  
|ANY|*LocalServiceName*|ANY|  
|ANY|ANY|*RemoteServiceName*|  
|ANY|ANY|ANY|  
  
 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 會先尋找指定的合約、本機服務和遠端服務與此作業所使用之項目相符的優先權。 如果找不到相符項目，[!INCLUDE[ssSB](../../includes/sssb-md.md)] 就會接著尋找合約和本機服務與此作業所使用之項目相符的優先權，其中遠端服務指定為 ANY。 然後，針對優先順序表格中所列的所有變化繼續進行。 如果找不到相符項目，此作業就會被指派預設優先權 5。  
  
 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 會獨立將優先權等級指派給每一個交談端點。 若要讓 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 指派優先權等級給起始端和目標交談端點，您必須確保這兩個端點都由交談優先權所涵蓋。 如果起始端和目標交談端點位於個別的資料庫中，您就必須在每個資料庫中建立交談優先權。 通常系統會針對某個交談的兩個交談端點指定相同的優先權等級，但是您可以指定不同的優先權等級。  
  
 優先權等級永遠會套用至從佇列接收訊息或交談群組識別碼的作業。 當訊息從某個 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 執行個體傳輸到另一個執行個體時，也會套用優先權等級。  
  
 在傳輸以下訊息時，不會使用優先權等級：  
  
-   從 HONOR_BROKER_PRIORITY 資料庫選項設定為 OFF 的資料庫傳輸訊息。 如需詳細資訊，請參閱 [ALTER DATABASE SET 選項 &#40;Transact-SQL&#41;](../../t-sql/statements/alter-database-transact-sql-set-options.md)。  
  
-   相同 Database Engine 執行個體的服務之間。  
  
-   如果資料庫中尚未建立任何交談優先權，則資料庫中的所有 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 作業都會被指派預設優先權 5。  
  
## <a name="permissions"></a>權限  
 建立交談優先權的權限預設為 db_ddladmin 或 db_owner 固定資料庫角色的成員，以及 sysadmin 固定伺服器角色的成員。 需要資料庫的 ALTER 權限。  
  
## <a name="examples"></a>範例  
  
### <a name="a-assigning-a-priority-level-to-both-directions-of-a-conversation"></a>A. 指派優先權等級給交談的兩個方向。  
 這兩個交談優先權可確保在 `SimpleContract` 和 `TargetService` 之間使用 `InitiatorAService` 的所有作業都會被指派優先權等級 3。  
  
```sql  
CREATE BROKER PRIORITY InitiatorAToTargetPriority  
    FOR CONVERSATION  
    SET (CONTRACT_NAME = SimpleContract,  
         LOCAL_SERVICE_NAME = InitiatorServiceA,  
         REMOTE_SERVICE_NAME = N'TargetService',  
         PRIORITY_LEVEL = 3);  
CREATE BROKER PRIORITY TargetToInitiatorAPriority  
    FOR CONVERSATION  
    SET (CONTRACT_NAME = SimpleContract,  
         LOCAL_SERVICE_NAME = TargetService,  
         REMOTE_SERVICE_NAME = N'InitiatorServiceA',  
         PRIORITY_LEVEL = 3);  
```  
  
### <a name="b-setting-the-priority-level-for-all-conversations-that-use-a-contract"></a>B. 針對使用合約的所有交談設定優先權等級  
 將優先權等級 `7` 指派給使用名為 `SimpleContract` 之合約的所有作業。 這會假設沒有其他優先權同時指定 `SimpleContract` 以及本機或遠端服務。  
  
```sql 
CREATE BROKER PRIORITY SimpleContractDefaultPriority  
    FOR CONVERSATION  
    SET (CONTRACT_NAME = SimpleContract,  
         LOCAL_SERVICE_NAME = ANY,  
         REMOTE_SERVICE_NAME = ANY,  
         PRIORITY_LEVEL = 7);  
```  
  
### <a name="c-setting-a-base-priority-level-for-a-database"></a>C. 為資料庫設定基本優先權等級。  
 為兩個特定的服務定義交談優先權，然後定義一個將會符合所有其他交談端點的交談優先權。 這樣並不會取代預設優先權 (一定是 5)，但是確實會讓指派預設值的項目數減至最少。  
  
```sql 
CREATE BROKER PRIORITY [//Adventure-Works.com/Expenses/ClaimPriority]  
    FOR CONVERSATION  
    SET (CONTRACT_NAME = ANY,  
         LOCAL_SERVICE_NAME = //Adventure-Works.com/Expenses/ClaimService,  
         REMOTE_SERVICE_NAME = ANY,  
         PRIORITY_LEVEL = 9);  
CREATE BROKER PRIORITY [//Adventure-Works.com/Expenses/ApprovalPriority]  
    FOR CONVERSATION  
    SET (CONTRACT_NAME = ANY,  
         LOCAL_SERVICE_NAME = //Adventure-Works.com/Expenses/ClaimService,  
         REMOTE_SERVICE_NAME = ANY,  
         PRIORITY_LEVEL = 6);  
CREATE BROKER PRIORITY [//Adventure-Works.com/Expenses/BasePriority]  
    FOR CONVERSATION  
    SET (CONTRACT_NAME = ANY,  
         LOCAL_SERVICE_NAME = ANY,  
         REMOTE_SERVICE_NAME = ANY,  
         PRIORITY_LEVEL = 3);  
```  
  
### <a name="d-creating-three-priority-levels-for-a-target-service-by-using-services"></a>D. 使用服務來為目標服務建立三個優先權等級  
 支援提供三個效能層級的系統：金卡 (高)、銀卡 (中) 和青銅卡 (低)。 這是一個合約，但是每一個等級都有不同的起始端服務。 所有的起始端服務都會與中央目標服務通訊。  
  
```sql  
CREATE BROKER PRIORITY GoldInitToTargetPriority  
    FOR CONVERSATION  
    SET (CONTRACT_NAME = SimpleContract,  
         LOCAL_SERVICE_NAME = GoldInitiatorService,  
         REMOTE_SERVICE_NAME = N'TargetService',  
         PRIORITY_LEVEL = 6);  
CREATE BROKER PRIORITY GoldTargetToInitPriority  
    FOR CONVERSATION  
    SET (CONTRACT_NAME = SimpleContract,  
         LOCAL_SERVICE_NAME = TargetService,  
         REMOTE_SERVICE_NAME = N'GoldInitiatorService',  
         PRIORITY_LEVEL = 6);  
CREATE BROKER PRIORITY SilverInitToTargetPriority  
    FOR CONVERSATION  
    SET (CONTRACT_NAME = SimpleContract,  
         LOCAL_SERVICE_NAME = SilverInitiatorService,  
         REMOTE_SERVICE_NAME = N'TargetService',  
         PRIORITY_LEVEL = 4);  
CREATE BROKER PRIORITY SilverTargetToInitPriority  
    FOR CONVERSATION  
    SET (CONTRACT_NAME = SimpleContract,  
         LOCAL_SERVICE_NAME = TargetService,  
         REMOTE_SERVICE_NAME = N'SilverInitiatorService',  
         PRIORITY_LEVEL = 4);  
CREATE BROKER PRIORITY BronzeInitToTargetPriority  
    FOR CONVERSATION  
    SET (CONTRACT_NAME = SimpleContract,  
         LOCAL_SERVICE_NAME = BronzeInitiatorService,  
         REMOTE_SERVICE_NAME = N'TargetService',  
         PRIORITY_LEVEL = 2);  
CREATE BROKER PRIORITY BronzeTargetToInitPriority  
    FOR CONVERSATION  
    SET (CONTRACT_NAME = SimpleContract,  
         LOCAL_SERVICE_NAME = TargetService,  
         REMOTE_SERVICE_NAME = N'BronzeInitiatorService',  
         PRIORITY_LEVEL = 2);  
```  
  
### <a name="e-creating-three-priority-levels-for-multiple-services-using-contracts"></a>E. 使用合約來為多個服務建立三個優先權等級  
 支援提供三個效能層級的系統：金卡 (高)、銀卡 (中) 和青銅卡 (低)。 每一個等級都有不同的合約。 這些優先權會套用到由使用合約的交談所參考的任何服務。  
  
```sql  
CREATE BROKER PRIORITY GoldPriority  
    FOR CONVERSATION  
    SET (CONTRACT_NAME = GoldContract,  
         LOCAL_SERVICE_NAME = ANY,  
         REMOTE_SERVICE_NAME = ANY,  
         PRIORITY_LEVEL = 6);  
CREATE BROKER PRIORITY SilverPriority  
    FOR CONVERSATION  
    SET (CONTRACT_NAME = SilverContract,  
         LOCAL_SERVICE_NAME = ANY,  
         REMOTE_SERVICE_NAME = ANY,  
         PRIORITY_LEVEL = 4);  
CREATE BROKER PRIORITY BronzePriority  
    FOR CONVERSATION  
    SET (CONTRACT_NAME = BronzeContract,  
         LOCAL_SERVICE_NAME = ANY,  
         REMOTE_SERVICE_NAME = ANY,  
         PRIORITY_LEVEL = 2);  
```  
  
## <a name="see-also"></a>另請參閱  
 [ALTER BROKER PRIORITY &#40;Transact-SQL&#41;](../../t-sql/statements/alter-broker-priority-transact-sql.md)   
 [BEGIN DIALOG CONVERSATION &#40;Transact-SQL&#41;](../../t-sql/statements/begin-dialog-conversation-transact-sql.md)   
 [CREATE CONTRACT &#40;Transact-SQL&#41;](../../t-sql/statements/create-contract-transact-sql.md)   
 [CREATE QUEUE &#40;Transact-SQL&#41;](../../t-sql/statements/create-queue-transact-sql.md)   
 [CREATE SERVICE &#40;Transact-SQL&#41;](../../t-sql/statements/create-service-transact-sql.md)   
 [DROP BROKER PRIORITY &#40;Transact-SQL&#41;](../../t-sql/statements/drop-broker-priority-transact-sql.md)   
 [GET CONVERSATION GROUP &#40;Transact-SQL&#41;](../../t-sql/statements/get-conversation-group-transact-sql.md)   
 [RECEIVE &#40;Transact-SQL&#41;](../../t-sql/statements/receive-transact-sql.md)   
 [SEND &#40;Transact-SQL&#41;](../../t-sql/statements/send-transact-sql.md)   
 [sys.conversation_priorities &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-conversation-priorities-transact-sql.md)  
  
  
