---
description: DROP BROKER PRIORITY (Transact-SQL)
title: DROP BROKER PRIORITY (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/03/2017
ms.prod: sql
ms.prod_service: sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- DROP_BROKER_PRIORITY_TSQL
- DROP BROKER PRIORITY
dev_langs:
- TSQL
helpviewer_keywords:
- DROP BROKER PRIORITY statement
ms.assetid: 09ee6c5b-af94-4a4b-a0e2-f9eac50e43aa
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: f5645014442ddeda800fdb5570ff76a96e943f62
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98095748"
---
# <a name="drop-broker-priority-transact-sql"></a>DROP BROKER PRIORITY (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  從目前資料庫中移除交談優先權。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
DROP BROKER PRIORITY ConversationPriorityName  
[;]  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *ConversationPriorityName*  
 指定要移除的交談優先權名稱。  
  
## <a name="remarks"></a>備註  
 當您卸除交談優先權時，任何現有的交談都會繼續運作，而使用的優先權等級是從交談優先權指派。  
  
## <a name="permissions"></a>權限  
 建立交談優先權的權限預設為 db_ddladmin 或 db_owner 固定資料庫角色的成員，以及 sysadmin 固定伺服器角色的成員。 需要資料庫的 ALTER 權限。  
  
## <a name="examples"></a>範例  
 下列範例會卸除名稱為 `InitiatorAToTargetPriority` 的交談優先權。  
  
```sql  
DROP BROKER PRIORITY InitiatorAToTargetPriority;
```  
  
## <a name="see-also"></a>另請參閱  
 [ALTER BROKER PRIORITY &#40;Transact-SQL&#41;](../../t-sql/statements/alter-broker-priority-transact-sql.md)   
 [CREATE BROKER PRIORITY &#40;Transact-SQL&#41;](../../t-sql/statements/create-broker-priority-transact-sql.md)   
 [sys.conversation_priorities &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-conversation-priorities-transact-sql.md)  
  
  
