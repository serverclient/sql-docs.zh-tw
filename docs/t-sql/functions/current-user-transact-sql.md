---
description: CURRENT_USER (Transact-SQL)
title: CURRENT_USER (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 07/24/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- CURRENT_USER
- CURRENT_USER_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- usernames [SQL Server]
- current user names
- niladic functions
- CURRENT_USER
- users [SQL Server], names
ms.assetid: 29248949-325b-4063-9f55-5a445fb35c6e
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 16c55b4a28ce58e62b4af19fa9e54c4bf780170f
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98097214"
---
# <a name="current_user-transact-sql"></a>CURRENT_USER (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

此函式會傳回目前使用者的名稱。 此函式相當於 `USER_NAME()`。
  
![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)
  
## <a name="syntax"></a>語法  
  
```syntaxsql
CURRENT_USER  
```  

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="return-types"></a>傳回類型
**sysname**
  
## <a name="remarks"></a>備註  
`CURRENT_USER` 會傳回目前安全性內容的名稱。 如果 `CURRENT_USER` 是在呼叫 `EXECUTE AS` 參數內容之後執行，`CURRENT_USER` 就會傳回模擬內容的名稱。 如果 Windows 主體利用群組中的成員資格來存取資料庫，則 `CURRENT_USER` 會傳回 Windows 主體名稱而非群組名稱。
  
請參閱 [SUSER_NAME &#40;Transact-SQL&#41;](../../t-sql/functions/suser-name-transact-sql.md) 和 [SYSTEM_USER &#40;Transact-SQL&#41;](../../t-sql/functions/system-user-transact-sql.md) 以了解如何傳回目前使用者的登入。
  
## <a name="examples"></a>範例  
  
### <a name="a-using-current_user-to-return-the-current-user-name"></a>A. 使用 CURRENT_USER 傳回目前使用者的名稱  
此範例會傳回目前使用者的名稱。
  
```sql
SELECT CURRENT_USER;  
GO  
```  
  
### <a name="b-using-current_user-as-a-default-constraint"></a>B. 使用 CURRENT_USER 做為 DEFAULT 條件約束  
此範例會建立一份資料表，利用 `CURRENT_USER` 作為銷售資料列之 `order_person` 資料行的 `DEFAULT` 條件約束。
  
```sql
USE AdventureWorks2012;  
GO  
IF EXISTS (SELECT TABLE_NAME FROM INFORMATION_SCHEMA.TABLES  
      WHERE TABLE_NAME = 'orders22')  
   DROP TABLE orders22;  
GO  
SET NOCOUNT ON;  
CREATE TABLE orders22  
(  
order_id int IDENTITY(1000, 1) NOT NULL,
cust_id  int NOT NULL,
order_date smalldatetime NOT NULL DEFAULT GETDATE(),
order_amt money NOT NULL,
order_person char(30) NOT NULL DEFAULT CURRENT_USER
);  
GO  
```  
  
此範例會在資料表中插入一筆記錄。 名為 `Wanida` 的使用者會執行這些陳述式。
  
```sql
INSERT orders22 (cust_id, order_amt)  
VALUES (5105, 577.95);  
GO  
SET NOCOUNT OFF;  
GO  
```  
  
此查詢會選取 `orders22` 資料表中的所有資訊。
  
```sql
SELECT * FROM orders22;  
GO  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
order_id    cust_id     order_date           order_amt    order_person
----------- ----------- -------------------- ------------ ------------
1000        5105        2005-04-03 23:34:00  577.95       Wanida
  
(1 row(s) affected)
```
  
### <a name="c-using-current_user-from-an-impersonated-context"></a>C. 使用模擬內容的 CURRENT_USER  
在此範例中，使用者 `Wanida` 會執行下列 [!INCLUDE[tsql](../../includes/tsql-md.md)] 程式碼來模擬使用者 'Arnalfo'。
  
```sql
SELECT CURRENT_USER;  
GO  
EXECUTE AS USER = 'Arnalfo';  
GO  
SELECT CURRENT_USER;  
GO  
REVERT;  
GO  
SELECT CURRENT_USER;  
GO  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
Wanida
Arnalfo
Wanida
```
  
## <a name="see-also"></a>另請參閱
[USER_NAME &#40;Transact-SQL&#41;](../../t-sql/functions/user-name-transact-sql.md)  
[SYSTEM_USER &#40;Transact-SQL&#41;](../../t-sql/functions/system-user-transact-sql.md)  
[sys.database_principals &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-database-principals-transact-sql.md)  
[ALTER TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-table-transact-sql.md)  
[CREATE TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/create-table-transact-sql.md)  
[系統函數 &#40;Transact-SQL&#41;](../../relational-databases/system-functions/system-functions-category-transact-sql.md)
  
  

