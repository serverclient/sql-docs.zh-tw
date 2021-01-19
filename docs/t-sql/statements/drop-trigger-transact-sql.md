---
description: DROP TRIGGER (Transact-SQL)
title: DROP TRIGGER (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 05/12/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- DROP TRIGGER
- DROP_TRIGGER_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- renaming triggers
- triggers [SQL Server], removing
- DDL triggers, removing
- DROP TRIGGER statement
- deleting triggers
- dropping triggers
- removing triggers
- DML triggers, removing
ms.assetid: 092d0d71-9f1e-4e38-a1c4-2487adfa5b4e
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: de35e51632ef898e89e8ab6d036d58243ba03f4f
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98172080"
---
# <a name="drop-trigger-transact-sql"></a>DROP TRIGGER (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  從目前資料庫中移除一個或多個 DML 或 DDL 觸發程序。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
-- Trigger on an INSERT, UPDATE, or DELETE statement to a table or view (DML Trigger)  
  
DROP TRIGGER [ IF EXISTS ] [schema_name.]trigger_name [ ,...n ] [ ; ]  
  
-- Trigger on a CREATE, ALTER, DROP, GRANT, DENY, REVOKE or UPDATE statement (DDL Trigger)  
  
DROP TRIGGER [ IF EXISTS ] trigger_name [ ,...n ]   
ON { DATABASE | ALL SERVER }   
[ ; ]  
  
-- Trigger on a LOGON event (Logon Trigger)  
  
DROP TRIGGER [ IF EXISTS ] trigger_name [ ,...n ]   
ON ALL SERVER  
```  

  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *IF EXISTS*  
 **適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 至 [目前版本](https://go.microsoft.com/fwlink/p/?LinkId=299658)、[!INCLUDE[sssds](../../includes/sssds-md.md)])。  
  
 只有在觸發程序已存在時，才能有條件地將其卸除。  
  
 *schema_name*  
 這是 DML 觸發程序所屬的結構描述名稱。 DML 觸發程序只會針對其據以建立的資料表或檢視表的結構描述。 您不能為 DDL 或登入觸發程序指定 *schema_name*。  
  
 *trigger_name*  
 這是要移除之觸發程序的名稱。 如需查看目前建立的觸發程序清單，請使用 [sys.server_assembly_modules](../../relational-databases/system-catalog-views/sys-triggers-transact-sql.md) 或 [sys.server_triggers](../../relational-databases/system-catalog-views/sys-server-triggers-transact-sql.md)。  
  
 DATABASE  
 指出將 DDL 觸發程序範圍套用在目前資料庫上。 如果建立或修改觸發程序時也指定了 DATABASE，就必須指定 DATABASE。  
  
 ALL SERVER  
 **適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。  
  
 指出將 DDL 觸發程序的範圍套用到目前伺服器。 如果建立或修改觸發程序時也指定了 ALL SERVER，就必須指定 ALL SERVER。 ALL SERVER 也適用於登入觸發程序。  
  
> [!NOTE]  
>  自主資料庫無法使用這個選項。  
  
## <a name="remarks"></a>備註  
 您可以卸除 DML 觸發程序，或卸除觸發程序資料表，來移除 DML 觸發程序。 當卸除資料表時，也會卸除所有相關聯的觸發程序。  
  
 當卸除觸發程序時，會從 **sys.objects**、**sys.triggers** 和 **sys.sql_modules** 目錄檢視中移除觸發程序的相關資訊。  
  
 只有在所有觸發程序都是利用相同的 ON 子句來建立時，才能夠每個 DROP TRIGGER 陳述式各卸除多個 DDL 觸發程序。  
  
 若要重新命名觸發程序，請使用 DROP TRIGGER 和 CREATE TRIGGER。 若要變更觸發程序的定義，請使用 ALTER TRIGGER。  
  
 如需如何判斷特定觸發程序相依性的詳細資訊，請參閱 [sys.sql_expression_dependencies](../../relational-databases/system-catalog-views/sys-sql-expression-dependencies-transact-sql.md)、[sys.dm_sql_referenced_entities &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-sql-referenced-entities-transact-sql.md) 和 [sys.dm_sql_referencing_entities &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-sql-referencing-entities-transact-sql.md)。  
  
 如需如何檢視觸發程序文字的詳細資訊，請參閱 [sp_helptext &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-helptext-transact-sql.md) 和 [sys.sql_modules &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-sql-modules-transact-sql.md)。  
  
 如需如何檢視現有觸發程序清單的詳細資訊，請參閱 [sys.triggers &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-triggers-transact-sql.md) 和 [sys.server_triggers &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-server-triggers-transact-sql.md)。  
  
## <a name="permissions"></a>權限  
 若要卸除 DML 觸發程序，需要定義觸發程序的資料表或檢視的 ALTER 權限。  
  
 若要卸除以伺服器範圍 (ON ALL SERVER) 定義的 DDL 觸發程序或登入觸發程序，需要伺服器的 CONTROL SERVER 權限。 若要卸除以資料庫範圍 (ON DATABASE) 定義的 DDL 觸發程序，需要目前資料庫的 ALTER ANY DATABASE DDL TRIGGER 權限。  
  
## <a name="examples"></a>範例  
  
### <a name="a-dropping-a-dml-trigger"></a>A. 卸除 DML 觸發程序  
 下列範例會卸除 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 資料庫中的 `employee_insupd` 觸發程序。 (從 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 開始，您可以使用 DROP TRIGGER IF EXISTS 語法。)  
  
```sql  
IF OBJECT_ID ('employee_insupd', 'TR') IS NOT NULL  
   DROP TRIGGER employee_insupd;  
```  
  
### <a name="b-dropping-a-ddl-trigger"></a>B. 卸除 DDL 觸發程序  
 下列範例會卸除 DDL 觸發程序 `safety`。  
  
> [!IMPORTANT]  
>  由於 DDL 觸發程序的範圍並不是結構描述，因此不會出現在 **sys.objects** 目錄檢視中，您也無法使用 OBJECT_ID 函式來查詢它們是否在資料庫中。 範圍不是結構描述的物件必須利用適當的目錄檢視來查詢。 如果是 DDL 觸發程序，請使用 **sys.triggers**。  
  
```sql  
DROP TRIGGER safety  
ON DATABASE;  
```  
  
## <a name="see-also"></a>另請參閱  
 [ALTER TRIGGER &#40;Transact-SQL&#41;](../../t-sql/statements/alter-trigger-transact-sql.md)   
 [CREATE TRIGGER &#40;Transact-SQL&#41;](../../t-sql/statements/create-trigger-transact-sql.md)   
 [ENABLE TRIGGER &#40;Transact-SQL&#41;](../../t-sql/statements/enable-trigger-transact-sql.md)   
 [DISABLE TRIGGER &#40;Transact-SQL&#41;](../../t-sql/statements/disable-trigger-transact-sql.md)   
 [EVENTDATA &#40;Transact-SQL&#41;](../../t-sql/functions/eventdata-transact-sql.md)   
 [取得關於 DML 觸發程序的詳細資訊](../../relational-databases/triggers/get-information-about-dml-triggers.md)   
 [sp_help &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-help-transact-sql.md)   
 [sp_helptrigger &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-helptrigger-transact-sql.md)   
 [sys.triggers &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-triggers-transact-sql.md)   
 [sys.trigger_events &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-trigger-events-transact-sql.md)   
 [sys.sql_modules &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-sql-modules-transact-sql.md)   
 [sys.assembly_modules &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-assembly-modules-transact-sql.md)   
 [sys.server_triggers &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-server-triggers-transact-sql.md)   
 [sys.server_trigger_events &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-server-trigger-events-transact-sql.md)   
 [sys.server_sql_modules &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-server-sql-modules-transact-sql.md)   
 [sys.server_assembly_modules &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-server-assembly-modules-transact-sql.md)  
  
  
