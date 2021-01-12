---
description: ALTER MESSAGE TYPE (Transact-SQL)
title: ALTER MESSAGE TYPE (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/06/2017
ms.prod: sql
ms.prod_service: sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- ALTER_MESSAGE_TYPE_TSQL
- ALTER MESSAGE TYPE
dev_langs:
- TSQL
helpviewer_keywords:
- ALTER MESSAGE TYPE statement
- modifying message types
- message types [Service Broker], modifying
ms.assetid: 98c94176-2bdf-4725-b4bc-d33b6b14817d
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: c8b1cbf21f5875ee32174a37f29dfe4a32fbf195
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98092125"
---
# <a name="alter-message-type-transact-sql"></a>ALTER MESSAGE TYPE (Transact-SQL)
[!INCLUDE [SQL Server - ASDBMI](../../includes/applies-to-version/sql-asdbmi.md)]

  變更訊息類型的屬性。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql 
ALTER MESSAGE TYPE message_type_name  
   VALIDATION =  
    {  NONE   
     | EMPTY   
     | WELL_FORMED_XML   
     | VALID_XML WITH SCHEMA COLLECTION schema_collection_name }  
[ ; ]  
```  
  

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *message_type_name*  
 要變更的訊息類型名稱。 您不可指定伺服器、資料庫和結構描述名稱。  
  
 VALIDATION  
 指定 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 如何驗證這種類型之訊息的訊息主體。  
  
 無  
 未執行任何驗證。 訊息主體可包含任何資料，也可能是 NULL。  
  
 EMPTY  
 訊息主體必須是 NULL。  
  
 WELL_FORMED_XML  
 訊息主體必須包含格式正確的 XML。  
  
 VALID_XML_WITH_SCHEMA = *schema_collection_name*  
 訊息主體必須包含符合指定的結構描述集合中之結構描述的 XML。 *schema_collection_name* 必須是現有 XML 結構描述集合的名稱。  
  
## <a name="remarks"></a>備註  
 變更訊息類型的驗證不會影響已傳遞給佇列的訊息。  
  
 若要變更訊息類型的 AUTHORIZATION，請使用 ALTER AUTHORIZATION 陳述式。  
  
## <a name="permissions"></a>權限  
 變更訊息類型的權限預設為訊息類型的擁有者、**db_ddladmin** 或 **db_owner** 固定資料庫角色的成員，以及 **sysadmin** 固定伺服器角色的成員。  
  
 當 ALTER MESSAGE TYPE 陳述式指定了結構描述集合時，執行這個陳述式的使用者必須有所指定之結構描述集合的 REFERENCES 權限。  
  
## <a name="examples"></a>範例  
 下列範例會變更 `//Adventure-Works.com/Expenses/SubmitExpense` 訊息類型來要求訊息主體包含格式正確的 XML 文件。  
  
```sql  
ALTER MESSAGE TYPE  
    [//Adventure-Works.com/Expenses/SubmitExpense]  
    VALIDATION = WELL_FORMED_XML ;  
```  
  
## <a name="see-also"></a>另請參閱  
 [ALTER AUTHORIZATION &#40;Transact-SQL&#41;](../../t-sql/statements/alter-authorization-transact-sql.md)   
 [CREATE MESSAGE TYPE &#40;Transact-SQL&#41;](../../t-sql/statements/create-message-type-transact-sql.md)   
 [DROP MESSAGE TYPE &#40;Transact-SQL&#41;](../../t-sql/statements/drop-message-type-transact-sql.md)   
 [EVENTDATA &#40;Transact-SQL&#41;](../../t-sql/functions/eventdata-transact-sql.md)  
  
  
