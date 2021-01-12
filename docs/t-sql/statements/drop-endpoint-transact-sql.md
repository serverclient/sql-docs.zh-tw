---
description: DROP ENDPOINT (Transact-SQL)
title: DROP ENDPOINT (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/06/2017
ms.prod: sql
ms.prod_service: sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- DROP_ENDPOINT_TSQL
- DROP ENDPOINT
dev_langs:
- TSQL
helpviewer_keywords:
- removing endpoints
- endpoints [SQL Server], removing
- deleting endpoints
- DROP ENDPOINT statement
- dropping endpoints
ms.assetid: 6aca7412-66a5-4fa4-86b2-061512ff2080
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 87359ee2b2059d27c0e49fc623bf42340d3fd89d
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98095738"
---
# <a name="drop-endpoint-transact-sql"></a>DROP ENDPOINT (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  卸除現有的端點。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
DROP ENDPOINT endPointName  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *endPointName*  
 這是要移除的端點名稱。  
  
## <a name="remarks"></a>備註  
 ENDPOINT DDL 陳述式不能在使用者交易內執行。  
  
## <a name="permissions"></a>權限  
 使用者必須是 **系統管理員 (sysadmin)** 固定伺服器角色的成員、端點擁有者，或者已被授與端點的 CONTROL 權限。  
  
## <a name="examples"></a>範例  
 下列範例會移除先前建立稱為 `sql_endpoint` 的端點。  
  
```sql  
DROP ENDPOINT sql_endpoint;  
```  
  
## <a name="see-also"></a>另請參閱  
 [CREATE ENDPOINT &#40;Transact-SQL&#41;](../../t-sql/statements/create-endpoint-transact-sql.md)   
 [ALTER ENDPOINT &#40;Transact-SQL&#41;](../../t-sql/statements/alter-endpoint-transact-sql.md)   
 [EVENTDATA &#40;Transact-SQL&#41;](../../t-sql/functions/eventdata-transact-sql.md)  
  
  
