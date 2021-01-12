---
description: ENABLE TRIGGER (Transact-SQL)
title: ENABLE TRIGGER (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 05/12/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- ENABLE TRIGGER
- ENABLE_TSQL
- ENABLE_TRIGGER_TSQL
- ENABLE
dev_langs:
- TSQL
helpviewer_keywords:
- DDL triggers, enabling
- triggers [SQL Server], enabling
- DML triggers, enabling
- ENABLE TRIGGER statement
ms.assetid: 6e21f0ad-68d0-432f-9c7c-a119dd2d3fc9
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 3ed2081a912291eed4682ba0e853df66f36e1801
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98091925"
---
# <a name="enable-trigger-transact-sql"></a>ENABLE TRIGGER (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

啟用 DML、DDL 或登入觸發程序。  
  
![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
ENABLE TRIGGER { [ schema_name . ] trigger_name [ ,...n ] | ALL }  
ON { object_name | DATABASE | ALL SERVER } [ ; ]  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
*schema_name*  
這是觸發程序所屬的結構描述名稱。 您不能為 DDL 或登入觸發程序指定 *schema_name*。  
  
*trigger_name*  
這是您要啟用的觸發程序名稱。  
  
ALL  
指出啟用在 ON 子句範圍內定義的所有觸發程序。  
  
*object_name*  
為建立 DML 觸發程序 *trigger_name* 以便在其中執行的資料表或檢視名稱。  
  
DATABASE  
如果是 DDL 觸發程序，它會指出已經建立或修改 *trigger_name*，以在資料庫範圍內執行。  
  
ALL SERVER  
**適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。  
  
如果是 DDL 觸發程序，它會指出已經建立或修改 *trigger_name*，以在伺服器範圍內執行。 ALL SERVER 也適用於登入觸發程序。  
  
> [!NOTE]  
>  自主資料庫無法使用這個選項。  
  
## <a name="remarks"></a>備註  
啟用觸發程序並不會重新建立它。 停用的觸發程序仍然是目前資料庫中的一個物件，但不會引發。 若要啟用觸發程序，在執行原先已程式化的任何 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式時，便會引發該觸發程序。 您可以使用 [DISABLE TRIGGER](../../t-sql/statements/disable-trigger-transact-sql.md) 停用觸發程序。 您也可以使用 [ALTER TABLE](../../t-sql/statements/alter-table-transact-sql.md)，來停用或啟用資料表上定義的 DML 觸發程序。  
  
## <a name="permissions"></a>權限  
若要啟用 DML 觸發程序，使用者在建立此觸發程序的資料表或檢視上至少需要有 ALTER 權限。  
  
若要啟用伺服器範圍 (ON ALL SERVER) 的 DDL 觸發程序或登入觸發程序，使用者在伺服器上需要有 CONTROL SERVER 權限。 若要啟用資料庫範圍 (ON DATABASE) 的 DDL 觸發程序，使用者在目前資料庫中至少需要有 ALTER ANY DATABASE DDL TRIGGER 權限。  
  
## <a name="examples"></a>範例  
  
### <a name="a-enabling-a-dml-trigger-on-a-table"></a>A. 啟用資料表的 DML 觸發程序  
下列範例會停用 AdventureWorks 資料庫中由 `uAddress` 資料表所建立的觸發程序 `Address`，然後啟用它。  
  
```sql  
DISABLE TRIGGER Person.uAddress ON Person.Address;  
GO  
ENABLE Trigger Person.uAddress ON Person.Address;  
GO  
```  
  
### <a name="b-enabling-a-ddl-trigger"></a>B. 啟用 DDL 觸發程序  
下列範例會建立資料庫範圍的 DDL 觸發程序 `safety`，然後停用它並再次啟用。  
  
```sql  
CREATE TRIGGER safety   
ON DATABASE   
FOR DROP_TABLE, ALTER_TABLE   
AS   
   PRINT 'You must disable Trigger "safety" to drop or alter tables!'   
   ROLLBACK;  
GO  
DISABLE TRIGGER safety ON DATABASE;  
GO  
ENABLE TRIGGER safety ON DATABASE;  
GO  
```  
  
### <a name="c-enabling-all-triggers-that-were-defined-with-the-same-scope"></a>C. 啟用以相同範圍來定義的所有觸發程序  
下列範例會啟用在伺服器範圍建立的所有 DDL 觸發程序。  
  
**適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。  
  
```sql  
ENABLE Trigger ALL ON ALL SERVER;  
GO  
```  
  
## <a name="see-also"></a>另請參閱  
 [DISABLE TRIGGER &#40;Transact-SQL&#41;](../../t-sql/statements/disable-trigger-transact-sql.md)   
 [ALTER TRIGGER &#40;Transact-SQL&#41;](../../t-sql/statements/alter-trigger-transact-sql.md)   
 [CREATE TRIGGER &#40;Transact-SQL&#41;](../../t-sql/statements/create-trigger-transact-sql.md)   
 [DROP TRIGGER &#40;Transact-SQL&#41;](../../t-sql/statements/drop-trigger-transact-sql.md)   
 [sys.triggers &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-triggers-transact-sql.md)  
  
  
