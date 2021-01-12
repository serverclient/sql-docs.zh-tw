---
description: SET LOCK_TIMEOUT (Transact-SQL)
title: SET LOCK_TIMEOUT (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 09/11/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- LOCK_TIMEOUT_TSQL
- SET_LOCK_TIMEOUT_TSQL
- SET LOCK_TIMEOUT
- LOCK_TIMEOUT
dev_langs:
- TSQL
helpviewer_keywords:
- timeout options [SQL Server], locks
- releasing locks
- LOCK_TIMEOUT option
- SET LOCK_TIMEOUT statement
- locking [SQL Server], time-outs
- wait time for lock releases [SQL Server]
ms.assetid: dd0c389e-956d-435e-bf71-e16624a0a215
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: a006850f731e78628275f3558975c95e12c64dc6
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98093369"
---
# <a name="set-lock_timeout-transact-sql"></a>SET LOCK_TIMEOUT (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  指定陳述式等待鎖定釋出的毫秒數。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
SET LOCK_TIMEOUT timeout_period  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *timeout_period*  
 為 [!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 傳回鎖定錯誤之前，所經歷的毫秒數。 -1 值 (預設值) 表示沒有逾時期限 (也就是永久等待)。  
  
 當等待鎖定超出逾時值時，會傳回錯誤。 0 值表示完全不等待，且在發現鎖定之後，儘快傳回一則訊息。  
  
## <a name="remarks"></a>備註  
 在開始連線時，這個設定的值為 -1。 變更之後，新設定會在接下來的連線時間內維持有效。  
  
 SET LOCK_TIMEOUT 的設定是在執行階段進行設定，而不是在剖析階段進行設定。  
  
 READPAST 鎖定提示提供這個 SET 選項的替代方案。  
  
 CREATE DATABASE、ALTER DATABASE 和 DROP DATABASE 陳述式不接受 SET LOCK_TIMEOUT 設定。  
  
## <a name="permissions"></a>權限  
 需要 **public** 角色的成員資格。  
  
## <a name="examples"></a>範例  
  
### <a name="a-set-the-lock-timeout-to-1800-milliseconds"></a>A. 將鎖定逾時設定為 1800 毫秒  
 下列範例將鎖定逾時期限設為 `1800` 毫秒。  
  
```sql  
SET LOCK_TIMEOUT 1800;  
GO  
```  
  
## <a name="examples-sssdwfull-and-sspdw"></a>範例：[!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 和 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
### <a name="b-set-the-lock-timeout-to-wait-forever-for-a-lock-to-be-released"></a>B. 將鎖定逾時設定為永久等待，直到釋放鎖定。  
 下列範例會將鎖定逾時設定為永久等待，且永不過期。 這是在每個連線一開始就已設定的預設行為。  
  
```sql  
SET LOCK_TIMEOUT -1;  
```  
  
 下列範例將鎖定逾時期限設為 `1800` 毫秒。 在此版本中，[!INCLUDE[ssDW](../../includes/ssdw-md.md)] 會成功剖析陳述式，但忽略 1800 的值並繼續使用預設行為。  
  
```sql  
SET LOCK_TIMEOUT 1800;  
```  
  
## <a name="see-also"></a>另請參閱  
 [@@LOCK_TIMEOUT &#40;Transact-SQL&#41;](../../t-sql/functions/lock-timeout-transact-sql.md)   
 [SET 陳述式 &#40;Transact-SQL&#41;](../../t-sql/statements/set-statements-transact-sql.md)  
  
  

