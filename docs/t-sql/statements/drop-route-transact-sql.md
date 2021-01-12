---
description: DROP ROUTE (Transact-SQL)
title: DROP ROUTE (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/06/2017
ms.prod: sql
ms.prod_service: sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- DROP ROUTE
- DROP_ROUTE_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- dropping routes
- DROP ROUTE statement
- deleting routes
- routes [Service Broker], removing
- removing routes
ms.assetid: d8fab0bc-d54a-46ca-9437-552db7477d40
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 0a699415666b17a53ebbedf596a16ebfe8fae4e7
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98081027"
---
# <a name="drop-route-transact-sql"></a>DROP ROUTE (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  卸除路由，從目前資料庫的路由表中，刪除路由的資訊。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
DROP ROUTE route_name  
[ ; ]  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *route_name*  
 要卸除的路由名稱。 您不可指定伺服器、資料庫和結構描述名稱。  
  
## <a name="remarks"></a>備註  
 儲存路由的路由表是能夠利用目錄檢視 **sys.routes** 來讀取的中繼資料表。 您只能利用 CREATE ROUTE、ALTER ROUTE 和 DROP ROUTE 陳述式來更新路由表。  
  
 不論是否有任何交談在使用路由，您都可以卸除此路由。 不過，如果沒有指向遠端服務的其他路由，這些交談的訊息會保留在傳輸佇列中，直到建立了遠端服務的路由或交談逾時為止。  
  
## <a name="permissions"></a>權限  
 卸除路由的權限預設為此路由的擁有者、db_ddladmin 或 db_owner 固定資料庫角色的成員，以及系統管理員 (sysadmin) 固定伺服器角色的成員。  
  
## <a name="examples"></a>範例  
 下列範例會刪除 `ExpenseRoute` 路由。  
  
```sql  
DROP ROUTE ExpenseRoute ;  
```  
  
## <a name="see-also"></a>另請參閱  
 [ALTER ROUTE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-route-transact-sql.md)   
 [CREATE ROUTE &#40;Transact-SQL&#41;](../../t-sql/statements/create-route-transact-sql.md)   
 [EVENTDATA &#40;Transact-SQL&#41;](../../t-sql/functions/eventdata-transact-sql.md)   
 [sys.routes &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-routes-transact-sql.md)  
  
  
