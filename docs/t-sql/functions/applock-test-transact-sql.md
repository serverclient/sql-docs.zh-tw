---
description: APPLOCK_TEST (Transact-SQL)
title: APPLOCK_TEST (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 07/24/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- APPLOCK_TEST_TSQL
- APPLOCK_TEST
dev_langs:
- TSQL
helpviewer_keywords:
- locking [SQL Server], applications
- APPLOCK_TEST function
- applications [SQL Server], locks
- sessions [SQL Server], application locks
- testing application locks
ms.assetid: 4ea33d04-f8e9-46ff-ae61-985bd3eaca2c
author: cawrites
ms.author: chadam
ms.openlocfilehash: 4babfd8fba4184e255b366b8a949fe195df466de
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98089752"
---
# <a name="applock_test-transact-sql"></a>APPLOCK_TEST (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

此函數所傳回的資訊，乃是關於在未取得鎖定的情況下，針對指定的鎖定擁有者，是否可以將鎖定授與特定應用程式資源。 APPLOCK_TEST 是應用程式鎖定函數，會在目前的資料庫上運作。 資料庫是應用程式鎖定的範圍。
  
![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)
  
## <a name="syntax"></a>語法  
  
```syntaxsql
APPLOCK_TEST ( 'database_principal' , 'resource_name' , 'lock_mode' , 'lock_owner' )  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
**'** *database_principal* **'**  
可被授與資料庫中物件權限的使用者、角色或應用程式角色。 函數的呼叫者必須是 *database_principal*、dbo 或 db_owner 固定資料庫角色的成員，才能成功呼叫函數。
  
**'** *resource_name* **'**  
用戶端應用程式指定的鎖定資源名稱。 應用程式必須確定資源名稱是唯一的。 指定的名稱會在內部雜湊成 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 鎖定管理員可儲存在內部的值。  *resource_name* 是沒有預設值的 **nvarchar(255)** 。 *resource_name* 是以二進位來比較，不論目前資料庫的定序設定為何，都會區分大小寫。
  
**'** *lock_mode* **'**  
針對特定資源要取得的鎖定模式。 *lock_mode* 是沒有預設值的 **nvarchar(32)**。 *lock_mode* 可具有下列任何一個值：**Shared**、**Update**、**IntentShared**、**IntentExclusive**、**Exclusive**。
  
**'** *lock_owner* **'**  
為鎖定的擁有者，也就是要求鎖定時的 *lock_owner* 值。 *lock_owner* 是 **nvarchar(32)**，而且此值可以是 **交易** (預設值) 或 **工作階段**。 如果明確指定預設值或 **Transaction**，就必須從交易內執行 APPLOCK_TEST。
  
## <a name="return-types"></a>傳回類型
**smallint**
  
## <a name="return-value"></a>傳回值
如果無法將鎖定授與指定的擁有者則為 0，如果可以授與鎖定則為 1。
  
## <a name="function-properties"></a>函式屬性
**不具決定性**
  
**Nonindexable**
  
**Nonparallelizable**
  
## <a name="examples"></a>範例  
有個別工作階段的兩個使用者 (**使用者 A** 和 **使用者 B**)，按照以下順序執行 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式。
  
**使用者 A** 執行：
  
```sql
USE AdventureWorks2012;  
GO  
BEGIN TRAN;  
DECLARE @result INT;  
EXEC @result=sp_getapplock  
    @DbPrincipal='public',  
    @Resource='Form1',  
    @LockMode='Shared',  
    @LockOwner='Transaction';  
SELECT APPLOCK_MODE('public', 'Form1', 'Transaction');  
GO  
```  
  
**使用者 B** 接著執行：
  
```sql
Use AdventureWorks2012;  
GO  
BEGIN TRAN;  
SELECT APPLOCK_MODE('public', 'Form1', 'Transaction');  
--Result set: NoLock  
  
SELECT APPLOCK_TEST('public', 'Form1', 'Shared', 'Transaction');  
--Result set: 1 (Lock is grantable.)  
  
SELECT APPLOCK_TEST('public', 'Form1', 'Exclusive', 'Transaction');  
--Result set: 0 (Lock is not grantable.)  
GO  
```  
  
**使用者 A** 接著執行：
  
```sql
EXEC sp_releaseapplock @Resource='Form1', @DbPrincipal='public';  
GO  
```  
  
**使用者 B** 接著執行：
  
```sql
SELECT APPLOCK_TEST('public', 'Form1', 'Exclusive', 'Transaction');  
--Result set: '1' (The lock is grantable.)  
GO  
```  
  
**使用者 A** 和 **使用者 B** 接著同時執行：
  
```sql
COMMIT TRAN;  
GO  
```  
  
## <a name="see-also"></a>請參閱
[APPLOCK_MODE &#40;Transact-SQL&#41;](../../t-sql/functions/applock-mode-transact-sql.md)  
[sp_getapplock &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-getapplock-transact-sql.md)  
[sp_releaseapplock &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-releaseapplock-transact-sql.md)
