---
description: DROP AGGREGATE (Transact-SQL)
title: DROP AGGREGATE (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 05/10/2017
ms.prod: sql
ms.prod_service: sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- DROP_AGGREGATE_TSQL
- DROP AGGREGATE
dev_langs:
- TSQL
helpviewer_keywords:
- aggregate functions [SQL Server], removing
- removing user-defined functions
- dropping user-defined functions
- user-defined functions [CLR integration]
- deleting user-defined functions
- DROP AGGREGATE statement
ms.assetid: 84ffc4e7-c451-4f1f-9a67-7fc3a120e53f
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: c9eee09f1c0612434f0facda8bd4db1015f04e84
ms.sourcegitcommit: 713e5a709e45711e18dae1e5ffc190c7918d52e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98688992"
---
# <a name="drop-aggregate-transact-sql"></a>DROP AGGREGATE (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  從目前資料庫移除使用者自訂的彙總函式。 使用者定義彙總函式是利用 [CREATE AGGREGATE](../../t-sql/statements/create-aggregate-transact-sql.md) 來建立的。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql  
DROP AGGREGATE [ IF EXISTS ] [ schema_name . ] aggregate_name  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *IF EXISTS*  
 **適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 至 [目前版本](../../sql-server/what-s-new-in-sql-server-2016.md))。  
  
 只有在彙總已存在時，才能有條件地將其卸除。  
  
 *schema_name*  
 這是使用者定義彙總函式所屬的結構描述名稱。  
  
 *aggregate_name*  
 這是您要卸除的使用者定義彙總函式的名稱。  
  
## <a name="remarks"></a>備註  
 若有任何檢視、函數或預存程序是利用參考您要卸除之使用者定義彙總函式的結構描述繫結所建立，就不會執行 DROP AGGREGATE。  
  
## <a name="permissions"></a>權限  
 若要執行 DROP AGGREGATE，使用者至少必須對使用者自訂彙總所屬的結構描述具備 ALTER 權限，或是對彙總具備 CONTROL 權限。  
  
## <a name="examples"></a>範例  
 下列範例會卸除彙總 `Concatenate`。  
  
```sql  
DROP AGGREGATE dbo.Concatenate;  
```  
  
## <a name="see-also"></a>另請參閱  
 [CREATE AGGREGATE &#40;Transact-SQL&#41;](../../t-sql/statements/create-aggregate-transact-sql.md)   
 [建立使用者定義彙總](../../relational-databases/user-defined-functions/create-user-defined-aggregates.md)  
