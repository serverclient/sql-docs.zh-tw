---
description: DISABLE TRIGGER (Transact-SQL)
title: DISABLE TRIGGER (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 05/10/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- DISABLE_TSQL
- DISABLE
- DISABLE TRIGGER
- DISABLE_TRIGGER_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- DML triggers, disabling
- DDL triggers, disabling
- DISABLE TRIGGER statement
- triggers [SQL Server], disabling
- disabling triggers
ms.assetid: e6529f06-e442-437e-a7bf-41790bc092c5
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 41976059d1deb47fcbfc1c939240db415ee8cf64
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98081427"
---
# <a name="disable-trigger-transact-sql"></a>DISABLE TRIGGER (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  停用觸發程序。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
DISABLE TRIGGER { [ schema_name . ] trigger_name [ ,...n ] | ALL }  
ON { object_name | DATABASE | ALL SERVER } [ ; ]  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *schema_name*  
 這是觸發程序所屬的結構描述名稱。 您不能為 DDL 或登入觸發程序指定 *schema_name*。  
  
 *trigger_name*  
 這是要停用的觸發程序名稱。  
  
 ALL  
 指出只要是在 ON 子句範圍定義的觸發程序一律停用。  
  
> [!CAUTION]  
>  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會在發行用來進行合併式複寫的資料庫中建立觸發程序。 在已發行的資料庫中指定 ALL，會停用這些觸發程序，這會中斷複寫。 指定 ALL 之前，請先確認未針對合併式複寫發行目前的資料庫。  
  
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
 根據預設，在建立觸發程序時會一併將它啟用。 即使停用觸發程序，也不會卸除它。 該觸發程序仍然會以物件形式存在於目前的資料庫中。 不過，只要編寫它的任何 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式在執行時，觸發程序就不會引發。 觸發程序可以使用 [ENABLE TRIGGER](../../t-sql/statements/enable-trigger-transact-sql.md) 重新啟用。 您也可以使用 [ALTER TABLE](../../t-sql/statements/alter-table-transact-sql.md)，來停用或啟用資料表上定義的 DML 觸發程序。  
  
 當您使用 **ALTER TRIGGER** 陳述式來變更觸發程序時，會啟用觸發程序。  
  
## <a name="permissions"></a>權限  
 若要停用 DML 觸發程序，使用者至少要對建立該觸發程序的資料表或檢視，具備 ALTER 權限。  
  
 若要停用伺服器範圍 (ON ALL SERVER) 的 DDL 觸發程序或登入觸發程序，使用者必須有伺服器的 CONTROL SERVER 權限。 若要停用以資料庫範圍 (ON DATABASE) 定義的 DDL 觸發程序，使用者至少要在目前資料庫中具備 ALTER ANY DATABASE DDL TRIGGER 權限。  
  
## <a name="examples"></a>範例  
AdventureWorks2012 資料庫會描述下列範例。
  
### <a name="a-disabling-a-dml-trigger-on-a-table"></a>A. 停用資料表的 DML 觸發程序  
 下列範例會停用在 `uAddress` 資料表中建立的觸發程序 `Person`。  
  
```sql  
DISABLE TRIGGER Person.uAddress ON Person.Address;  
GO  
```  
  
### <a name="b-disabling-a-ddl-trigger"></a>B. 停用 DDL 觸發程序  
 下列範例會以資料庫範圍建立 DDL 觸發程序 `safety`，然後再停用它。  
  
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
```  
  
### <a name="c-disabling-all-triggers-that-were-defined-with-the-same-scope"></a>C. 停用所有以相同範圍定義的觸發程序  
 下列範例會停用在伺服器範圍建立的所有 DDL 觸發程序。  
  
```sql  
DISABLE Trigger ALL ON ALL SERVER;  
GO  
```  
  
## <a name="see-also"></a>另請參閱  
 [ENABLE TRIGGER &#40;Transact-SQL&#41;](../../t-sql/statements/enable-trigger-transact-sql.md)   
 [ALTER TRIGGER &#40;Transact-SQL&#41;](../../t-sql/statements/alter-trigger-transact-sql.md)   
 [CREATE TRIGGER &#40;Transact-SQL&#41;](../../t-sql/statements/create-trigger-transact-sql.md)   
 [DROP TRIGGER &#40;Transact-SQL&#41;](../../t-sql/statements/drop-trigger-transact-sql.md)   
 [sys.triggers &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-triggers-transact-sql.md)  
  
  
