---
description: CREATE SERVICE (Transact-SQL)
title: CREATE SERVICE (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/06/2017
ms.prod: sql
ms.prod_service: sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- CREATE_SERVICE_TSQL
- SERVICE
- CREATE SERVICE
- SERVICE_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- services [Service Broker], creating
- CREATE SERVICE statement
- contracts [Service Broker], service creation
ms.assetid: fb804fa2-48eb-4878-a12f-4e0d5f4bc9e3
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: f4c3d54c74d21994974ea1cdf47dbea07d8171ca
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98096987"
---
# <a name="create-service-transact-sql"></a>CREATE SERVICE (Transact-SQL)
[!INCLUDE [SQL Server - ASDBMI](../../includes/applies-to-version/sql-asdbmi.md)]

  建立新的服務。 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 服務是一項特定工作或一組工作的名稱。 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 會利用服務的名稱路由傳送訊息，將訊息傳遞至資料庫內的正確佇列，以及強制使用合約進行交談。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
CREATE SERVICE service_name  
   [ AUTHORIZATION owner_name ]  
   ON QUEUE [ schema_name. ]queue_name  
   [ ( contract_name | [DEFAULT][ ,...n ] ) ]  
[ ; ]  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *service_name*  
 這是要建立的服務名稱。 新服務會建立在目前資料庫中，擁有者是 AUTHORIZATION 子句所指定的主體。 您不可指定伺服器、資料庫和結構描述名稱。 *service_name* 必須是有效的 **sysname**。  
  
> [!NOTE]  
> 請勿建立針對 *service_name* 使用關鍵字 ANY 的服務。 當您在 `CREATE BROKER PRIORITY` 中針對服務名稱指定 `ANY` 時，就會考慮所有服務的優先權。 這並不限於名稱為 ANY 的服務。  
  
 AUTHORIZATION *owner_name*  
 將服務的擁有者設為指定的資料庫使用者或角色。 當目前的使用者是 **dbo** 或 **sa** 時，*owner_name* 可以是任何有效使用者或角色的名稱。 否則，*owner_name* 必須是目前使用者的名稱、目前使用者有其 IMPERSONATE 權限的使用者名稱，或目前使用者所屬的角色名稱。  
  
 ON QUEUE [ _schema_name_**.** ] *queue_name*  
 指定接收服務訊息的佇列。 佇列必須在服務的相同資料庫中。 如果未提供 *schema_name*，結構描述就是執行陳述式之使用者的預設結構描述。  
  
 *contract_name*  
 指定目標可以是這項服務的合約。 服務程式會利用指定的合約來起始與這項服務的交談。 如果未指定任何合約，服務就只能起始交談。  
  
 **[** DEFAULT **]**  
 指定這項服務可以是遵照 DEFAULT 合約的交談目標。 在這個子句的內容中，DEFAULT 不是關鍵字，必須用識別碼來分隔。 DEFAULT 合約允許交談的兩端傳送訊息類型是 DEFAULT 的訊息。 訊息類型 DEFAULT 所用的驗證是 NONE。  
  
## <a name="remarks"></a>備註  
 服務會顯露相關合約所提供的功能，供其他服務使用。 `CREATE SERVICE` 陳述式會指定目標是這項服務的合約。 交談必須使用服務指定的合約，才能將這項服務設為目標。 未指定合約的服務，不會向其他服務顯露任何功能。  
  
 這項服務所初始化的交談可以使用任何合約。 當服務只要起始交談時，您便建立不指定任何合約的服務。  
  
 當 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 接受來自遠端服務的新交談時，目標服務的名稱會決定 Broker 將交談訊息放在其中的佇列。  
  
## <a name="permissions"></a>權限  
 建立服務的權限預設為 `db_ddladmin` 或 `db_owner` 固定資料庫角色的成員，或是 `sysadmin` 固定伺服器角色的成員。 執行 `CREATE SERVICE` 陳述式的使用者必須有佇列及所有指定合約的 `REFERENCES` 權限。  
  
 服務的 `REFERENCES` 權限預設為服務的擁有者、`db_ddladmin` 或 `db_owner` 固定資料庫角色的成員，或是 `sysadmin` 固定伺服器角色的成員。 服務的 `SEND` 權限預設為服務的擁有者、`db_owner` 固定資料庫角色的成員，或是 `sysadmin` 固定伺服器角色的成員。  
  
 服務不能是暫存物件。 您可以使用開頭為 **#** 的服務名稱，但它們是永久物件。  
  
## <a name="examples"></a>範例  
  
### <a name="a-creating-a-service-with-one-contract"></a>A. 建立含有一份合約的服務  
 下列範例會在 `//Adventure-Works.com/Expenses` 結構描述中的 `ExpenseQueue` 佇列上建立 `dbo` 服務。 以這項服務為目標的對話必須遵照 `//Adventure-Works.com/Expenses/ExpenseSubmission` 合約。  
  
```sql  
CREATE SERVICE [//Adventure-Works.com/Expenses]  
    ON QUEUE [dbo].[ExpenseQueue]  
    ([//Adventure-Works.com/Expenses/ExpenseSubmission]) ;  
```  
  
### <a name="b-creating-a-service-with-multiple-contracts"></a>B. 建立含有多份合約的服務  
 下列範例會在 `//Adventure-Works.com/Expenses` 佇列上建立 `ExpenseQueue` 服務。 以這項服務為目標的對話必須遵照 `//Adventure-Works.com/Expenses/ExpenseSubmission` 合約或 `//Adventure-Works.com/Expenses/ExpenseProcessing` 合約。  
  
```sql  
CREATE SERVICE [//Adventure-Works.com/Expenses] ON QUEUE ExpenseQueue  
    ([//Adventure-Works.com/Expenses/ExpenseSubmission],  
     [//Adventure-Works.com/Expenses/ExpenseProcessing]) ;  
```  
  
### <a name="c-creating-a-service-with-no-contracts"></a>C. 建立不含任何合約的服務  
 下列範例會在 `//Adventure-Works.com/Expenses on the ExpenseQueue` 佇列上建立服務。 這項服務沒有合約資訊。 因此，這項服務只能是交談的起始端。  
  
```sql  
CREATE SERVICE [//Adventure-Works.com/Expenses] ON QUEUE ExpenseQueue ;  
```  
  
## <a name="see-also"></a>另請參閱  
 [ALTER SERVICE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-service-transact-sql.md)   
 [DROP SERVICE &#40;Transact-SQL&#41;](../../t-sql/statements/drop-service-transact-sql.md)   
 [EVENTDATA &#40;Transact-SQL&#41;](../../t-sql/functions/eventdata-transact-sql.md)  
  
  
