---
description: ALTER REMOTE SERVICE BINDING (Transact-SQL)
title: ALTER REMOTE SERVICE BINDING (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/06/2017
ms.prod: sql
ms.prod_service: sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- ALTER REMOTE SERVICE BINDING
- ALTER_REMOTE_SERVICE_BINDING_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- remote service bindings [Service Broker], modifying
- ALTER REMOTE SERVICE BINDING statement
- modifying remote service bindings
ms.assetid: ee620b4a-9375-4eaa-a016-69916c9e1e68
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: c671b412d5d9ce34c34fdc49e16215dafc8ec83e
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98092107"
---
# <a name="alter-remote-service-binding-transact-sql"></a>ALTER REMOTE SERVICE BINDING (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  變更遠端服務繫結的相關使用者，或變更繫結的匿名驗證設定。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
ALTER REMOTE SERVICE BINDING binding_name   
   WITH [ USER = <user_name> ] [ , ANONYMOUS = { ON | OFF } ]   
[ ; ]  
```  
  

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *binding_name*  
 這是要變更的遠端服務繫結的名稱。 您不可指定伺服器、資料庫和結構描述名稱。  
  
 WITH USER = \<*user_name>*  
 指定持有這個繫結的遠端服務之相關憑證的資料庫使用者。 這個憑證的公開金鑰用來加密和驗證與遠端服務交換的訊息。  
  
 ANONYMOUS  
 指定與遠端服務通訊時，是否使用匿名驗證。 如果 ANONYMOUS = ON，會使用匿名驗證，本機使用者的認證不會傳送給遠端服務。 如果 ANONYMOUS = OFF，便會傳送使用者認證。 如果未指定這個子句，預設值便是 OFF。  
  
## <a name="remarks"></a>備註  
 已與 *user_name* 建立關聯的憑證中之公開金鑰會用來驗證傳給遠端服務的訊息，以及對之後要用來加密交談的工作階段金鑰進行加密。 *user_name* 的憑證必須對應到主控遠端服務之資料庫中的使用者憑證。  
  
## <a name="permissions"></a>權限  
 改變遠端服務繫結的權限，預設為遠端服務繫結的擁有者、**db_owner** 固定資料庫角色的成員，以及 **sysadmin** 固定伺服器角色的成員。  
  
 執行 ALTER REMOTE SERVICE BINDING 陳述式的使用者必須有陳述式指定的使用者之模擬權限。  
  
 若要改變遠端服務繫結的 AUTHORIZATION，請使用 ALTER AUTHORIZATION 陳述式。  
  
## <a name="examples"></a>範例  
 下列範例會變更遠端服務繫結 `APBinding`，以利用 `SecurityAccount` 帳戶的憑證來加密訊息。  
  
```sql  
ALTER REMOTE SERVICE BINDING APBinding  
    WITH USER = SecurityAccount ;  
```  
  
## <a name="see-also"></a>另請參閱  
 [CREATE REMOTE SERVICE BINDING &#40;Transact-SQL&#41;](../../t-sql/statements/create-remote-service-binding-transact-sql.md)   
 [DROP REMOTE SERVICE BINDING &#40;Transact-SQL&#41;](../../t-sql/statements/drop-remote-service-binding-transact-sql.md)   
 [EVENTDATA &#40;Transact-SQL&#41;](../../t-sql/functions/eventdata-transact-sql.md)  
  
  
