---
description: CREATE CONTRACT (Transact-SQL)
title: CREATE CONTRACT (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/06/2017
ms.prod: sql
ms.prod_service: sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- CONTRACT_TSQL
- CREATE_CONTRACT_TSQL
- CREATE CONTRACT
- CONTRACT
dev_langs:
- TSQL
helpviewer_keywords:
- CREATE CONTRACT statement
- contracts [Service Broker], creating
- message types [Service Broker], contracts
ms.assetid: 494cbfa6-8e93-4161-a64d-90d681915211
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 3541197e5c74ebf5c5bc2ad38c43770028fbd43b
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98099525"
---
# <a name="create-contract-transact-sql"></a>CREATE CONTRACT (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  建立新的合約。 合約會定義 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 交談中所用的訊息類型，並決定交談的哪一端可以傳送該類型的訊息。 每一個交談都遵照一個合約。 交談開始時，起始服務會指定交談的合約。 目標服務則會指定目標服務接受交談的合約。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
  
CREATE CONTRACT contract_name  
   [ AUTHORIZATION owner_name ]  
      (  {   { message_type_name | [ DEFAULT ] }  
          SENT BY { INITIATOR | TARGET | ANY }   
       } [ ,...n] )   
[ ; ]  
```  

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *contract_name*  
 這是要建立的合約名稱。 新合約會建立在目前的資料庫中，擁有者是 AUTHORIZATION 子句所指定的主體。 您不可指定伺服器、資料庫和結構描述名稱。 *contract_name* 最多可有 128 個字元。  
  
> [!NOTE]  
>  請勿建立針對 *contract_name* 使用關鍵字 ANY 的合約。 當您在 CREATE BROKER PRIORITY 中針對合約名稱指定 ANY 時，就會考慮所有合約的優先權。 這並不限於名稱為 ANY 的合約。  
  
 AUTHORIZATION *owner_name*  
 將合約的擁有者設為指定的資料庫使用者或角色。 當目前的使用者是 **dbo** 或 **sa** 時，*owner_name* 可以是任何有效使用者或角色的名稱。 否則，*owner_name* 必須是目前使用者的名稱、目前使用者有其模擬權限的使用者名稱，或目前使用者所屬的角色名稱。 當略過這個子句時，合約會屬於目前的使用者。  
  
 *message_type_name*  
 這是要併入合約之訊息類型的名稱。  
  
 SENT BY   
 指定哪一個端點可以傳送指定訊息類型的訊息。 合約記錄服務可以用來進行特定交談的訊息。 每一個交談有兩個端點：「起始端」端點，這是起始交談的服務，以及「目標」端點，這是起始端連絡的服務。  
  
 INITIATOR   
 表示只有交談起始端可以傳送特定訊息類型的訊息。 開始交談的服務稱為交談的「起始端」。  
  
 TARGET  
 表示只有交談目標可以傳送特定訊息類型的訊息。 接受由另一服務起始之交談的服務稱為交談的「目標」。  
  
 ANY  
 表示起始端和目標兩者都可以傳送這個類型的訊息。  
  
 [ DEFAULT ]  
 表示這個合約支援預設訊息類型的訊息。 依預設，所有資料庫都包含名稱為 DEFAULT 的訊息類型。 這個訊息類型使用 NONE 的驗證。 在這個子句的內容中，DEFAULT 不是關鍵字，必須用識別碼來分隔。 Microsoft SQL Server 也提供指定 DEFAULT 訊息類型的 DEFAULT 合約。  
  
## <a name="remarks"></a>備註  
 合約中訊息類型的順序並不重要。 在目標收到第一則訊息之後，[!INCLUDE[ssSB](../../includes/sssb-md.md)] 允許交談的任何一端在任何時間傳送該端允許的任何訊息。 例如，如果交談起始端可以傳送 **//Adventure-Works.com/Expenses/SubmitExpense** 訊息類型，[!INCLUDE[ssSB](../../includes/sssb-md.md)] 便允許起始端在交談期間傳送任何數目的 **SubmitExpense** 訊息。  
  
 合約中的訊息類型和方向無法變更。 若要改變合約的 AUTHORIZATION，請使用 ALTER AUTHORIZATION 陳述式。  
  
 合約必須允許起始端傳送訊息。 合約不包含至少一個 SENT BY ANY 或 SENT BY INITIATOR 的訊息類型時，CREATE CONTRACT 陳述式會失敗。  
  
 無論合約為何，服務一律可以接收 `https://schemas.microsoft.com/SQL/ServiceBroker/DialogTimer`、`https://schemas.microsoft.com/SQL/ServiceBroker/Error` 和 `https://schemas.microsoft.com/SQL/ServiceBroker/EndDialog` 訊息類型。 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 會針對應用程式使用系統訊息的這些訊息類型。  
  
 合約不能是暫存物件。 您可以使用開頭是 # 的合約名稱，但它們是永久物件。  
  
## <a name="permissions"></a>權限  
 根據預設，**db_ddladmin** 或 **db_owner** 固定資料庫角色的成員，以及系統管理員 (**sysadmin**) 固定伺服器角色的成員可以建立合約。  
  
 根據預設，合約的擁有者、**db_ddladmin** 或 **db_owner** 固定資料庫角色的成員，以及系統管理員 (**sysadmin**) 固定伺服器角色的成員，具有合約的 REFERENCES 權限。  
  
 執行 CREATE CONTRACT 陳述式的使用者必須有所有指定訊息類型的 REFERENCES 權限。  
  
## <a name="examples"></a>範例  
 **A. 建立合約**  
  
 下列範例會依據三種訊息類型建立費用償還合約。  
  
```sql  
CREATE MESSAGE TYPE  
    [//Adventure-Works.com/Expenses/SubmitExpense]           
    VALIDATION = WELL_FORMED_XML ;           
  
CREATE MESSAGE TYPE  
    [//Adventure-Works.com/Expenses/ExpenseApprovedOrDenied]           
    VALIDATION = WELL_FORMED_XML ;           
  
CREATE MESSAGE TYPE           
    [//Adventure-Works.com/Expenses/ExpenseReimbursed]           
    VALIDATION= WELL_FORMED_XML ;           
  
CREATE CONTRACT            
    [//Adventure-Works.com/Expenses/ExpenseSubmission]           
    ( [//Adventure-Works.com/Expenses/SubmitExpense]           
          SENT BY INITIATOR,           
      [//Adventure-Works.com/Expenses/ExpenseApprovedOrDenied]           
          SENT BY TARGET,           
      [//Adventure-Works.com/Expenses/ExpenseReimbursed]           
          SENT BY TARGET           
    ) ;  
```  
  
## <a name="see-also"></a>另請參閱  
 [DROP CONTRACT &#40;Transact-SQL&#41;](../../t-sql/statements/drop-contract-transact-sql.md)   
 [EVENTDATA &#40;Transact-SQL&#41;](../../t-sql/functions/eventdata-transact-sql.md)  
  
  
