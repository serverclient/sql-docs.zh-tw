---
description: ALTER TRIGGER (Transact-SQL)
title: ALTER TRIGGER (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 05/08/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- ALTER TRIGGER
- ALTER_TRIGGER_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- DDL triggers, modifying
- triggers [SQL Server], modifying
- modifying triggers
- ALTER TRIGGER statement
- DML triggers, modifying
ms.assetid: 2a99c7c1-ac2f-4637-aa7c-3d1bf514e500
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 63cb1d939fc4c46c2382c369c66d79764f2d6211
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98098444"
---
# <a name="alter-trigger-transact-sql"></a>ALTER TRIGGER (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  修改 CREATE TRIGGER 陳述式先前所建立之 DML、DDL 或登入觸發程序的定義。 觸發程序是使用 CREATE TRIGGER 建立的。 您可以直接從 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式建立觸發程序，也可以使用在 [!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[dnprdnshort](../../includes/dnprdnshort-md.md)] 通用語言執行平台 (CLR) 中建立的組件方法來建立觸發程序，並將其上傳到 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的執行個體。 如需用於 ALTER TRIGGER 陳述式的參數詳細資訊，請參閱 [CREATE TRIGGER &#40;Transact-SQL&#41;](../../t-sql/statements/create-trigger-transact-sql.md)。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
-- SQL Server Syntax  
-- Trigger on an INSERT, UPDATE, or DELETE statement to a table or view (DML Trigger)  

ALTER TRIGGER schema_name.trigger_name   
ON  ( table | view )   
[ WITH <dml_trigger_option> [ ,...n ] ]  
 ( FOR | AFTER | INSTEAD OF )   
{ [ DELETE ] [ , ] [ INSERT ] [ , ] [ UPDATE ] }   
[ NOT FOR REPLICATION ]   
AS { sql_statement [ ; ] [ ...n ] | EXTERNAL NAME <method specifier>   
[ ; ] }   
  
<dml_trigger_option> ::=  
    [ ENCRYPTION ]  
    [ <EXECUTE AS Clause> ]  
  
<method_specifier> ::=  
    assembly_name.class_name.method_name  
  
-- Trigger on an INSERT, UPDATE, or DELETE statement to a table 
-- (DML Trigger on memory-optimized tables)  

ALTER TRIGGER schema_name.trigger_name   
ON  ( table  )   
[ WITH <dml_trigger_option> [ ,...n ] ]  
 ( FOR | AFTER )   
{ [ DELETE ] [ , ] [ INSERT ] [ , ] [ UPDATE ] }   
AS { sql_statement [ ; ] [ ...n ] }   
  
<dml_trigger_option> ::=  
    [ NATIVE_COMPILATION ]  
    [ SCHEMABINDING ]  
    [ <EXECUTE AS Clause> ]  
  
-- Trigger on a CREATE, ALTER, DROP, GRANT, DENY, REVOKE, 
-- or UPDATE statement (DDL Trigger)  
  
ALTER TRIGGER trigger_name   
ON { DATABASE | ALL SERVER }   
[ WITH <ddl_trigger_option> [ ,...n ] ]  
{ FOR | AFTER } { event_type [ ,...n ] | event_group }   
AS { sql_statement [ ; ] | EXTERNAL NAME <method specifier>   
[ ; ] }  
}   
  
<ddl_trigger_option> ::=  
    [ ENCRYPTION ]  
    [ <EXECUTE AS Clause> ]  
  
<method_specifier> ::=  
    assembly_name.class_name.method_name  
  
-- Trigger on a LOGON event (Logon Trigger)  

ALTER TRIGGER trigger_name   
ON ALL SERVER   
[ WITH <logon_trigger_option> [ ,...n ] ]  
{ FOR| AFTER } LOGON   
AS { sql_statement  [ ; ] [ ,...n ] | EXTERNAL NAME < method specifier >  
  [ ; ] }  
  
<logon_trigger_option> ::=  
    [ ENCRYPTION ]  
    [ EXECUTE AS Clause ]  
  
<method_specifier> ::=  
    assembly_name.class_name.method_name  
```  
  
```syntaxsql
-- Azure SQL Database Syntax   
-- Trigger on an INSERT, UPDATE, or DELETE statement to a table or view (DML Trigger)   
  
ALTER TRIGGER schema_name. trigger_name   
ON (table | view )   
 [ WITH <dml_trigger_option> [ ,...n ] ]   
 ( FOR | AFTER | INSTEAD OF )   
{ [ DELETE ] [ , ] [ INSERT ] [ , ] [ UPDATE ] }   
AS { sql_statement [ ; ] [...n ] }   
  
<dml_trigger_option> ::=   
    [ <EXECUTE AS Clause> ]   
  
-- Trigger on a CREATE, ALTER, DROP, GRANT, DENY, REVOKE, or UPDATE statement (DDL Trigger)   
  
ALTER TRIGGER trigger_name   
ON { DATABASE }   
 [ WITH <ddl_trigger_option> [ ,...n ] ]   
{ FOR | AFTER } { event_type [ ,...n ] | event_group }   
AS { sql_statement   
[ ; ] }  
}   
  
<ddl_trigger_option> ::=   
    [ <EXECUTE AS Clause> ]  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *schema_name*  
 這是 DML 觸發程序所屬的結構描述名稱。 DML 觸發程序只會針對其據以建立的資料表或檢視表的結構描述。 只有當 DML 觸發程序和其對應資料表或檢視都屬於預設結構描述時，*schema**_name* 才是選擇性的。 您不能為 DDL 或登入觸發程序指定 *schema_name*。  
  
 *trigger_name*  
 這是要修改的現有觸發程序。  
  
 *table* | *view*  
 這是執行 DML 觸發程序的資料表或檢視。 您可以選擇性地指定資料表或檢視的完整名稱。  
  
 DATABASE  
 將 DDL 觸發程序的範圍套用在目前資料庫上。 若有指定，每當目前資料庫中出現 *event_type* 或 *event_group* 時，都會引發這個觸發程序。  
  
 ALL SERVER  
 **適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。  
  
 將 DDL 或登入觸發程序的範圍套用在目前伺服器上。 若有指定，每當目前伺服器中的任何位置出現 *event_type* 或 *event_group* 時，都會引發這個觸發程序。  
  
 WITH ENCRYPTION  
 **適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。  
  
 加密包含 ALTER TRIGGER 陳述式文字的 sys.syscommentssys.sql_modules 項目。 使用 WITH ENCRYPTION 可防止在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 複寫中發行這個觸發程序。 CLR 觸發程序不能指定 WITH ENCRYPTION。  
  
> [!NOTE]  
>  如果觸發程序是利用 WITH ENCRYPTION 來建立的，就必須在 ALTER TRIGGER 陳述式中重新指定它，這個選項才會維持啟用狀態。  
  
 EXECUTE AS  
 指定用來執行這個觸發程序的安全性內容。 可讓您控制 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體用來驗證觸發程序所參考的任何資料庫物件之權限的使用者帳戶。  
  
 如需詳細資訊，請參閱 [EXECUTE AS 子句 &#40;Transact-SQL&#41;](../../t-sql/statements/execute-as-clause-transact-sql.md)。  
  
 NATIVE_COMPILATION  
 表示觸發程序是原生編譯的。  
  
 如果觸發程序是在經記憶體最佳化的資料表上，則這個選項為必要。  
  
 SCHEMABINDING  
 確保無法卸除或改變觸發程序所參考的資料表。  
  
 針對經記憶體最佳化資料表上的觸發程序，這個選項為必要，且其不支援傳統資料表上的觸發程序。  
  
 AFTER  
 指定只在順利執行觸發的 SQL 陳述式時，才引發觸發程序。 所有參考串聯動作和條件約束檢查也都必須成功之後，才會引發這個觸發程序。  
  
 如果只指定 FOR 關鍵字，AFTER 便是預設值。  
  
 只有資料表可以定義 DML AFTER 觸發程序。  
  
 INSTEAD OF  
 指定執行 DML 觸發程序來取代觸發的 SQL 陳述式，因此，會覆寫觸發陳述式的動作。 DDL 或登入觸發程序不能指定 INSTEAD OF。  
  
 您最多只能在資料表或檢視上，定義每個 INSERT、UPDATE 或 DELETE 陳述式各一個 INSTEAD OF 觸發程序。 不過，您可以定義檢視的檢視，讓每份檢視都有它自己的 INSTEAD OF 觸發程序。  
  
 使用 WITH CHECK OPTION 來建立的檢視表上不允許 INSTEAD OF 觸發程序。 將 INSTEAD OF 觸發程序加入已指定 WITH CHECK OPTION 的檢視表時，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會產生錯誤。 使用者必須在定義 INSTEAD OF 觸發程序之前，利用 ALTER VIEW 來移除這個選項。  
  
 { [ DELETE ] [ , ] [ INSERT ] [ , ] [ UPDATE ] } | { [INSERT ] [ , ] [ UPDATE ] }  
 指定當試圖針對這份資料表或檢視表來執行資料修改陳述式時，資料修改陳述式會啟動 DML 觸發程序。 至少必須指定一個選項。 您可以在觸發程序定義中，依照任何順序來指定這些選項的任何組合。 如果指定了多個選項，請用逗號來分開這些選項。  
  
 對於 INSTEAD OF 觸發程序而言，含有指定了重疊顯示動作 ON DELETE 的參考關聯性之資料表不能使用 DELETE 選項。 同樣地，含有指定了串聯動作 ON UPDATE 的參考關聯性之資料表也不能使用 UPDATE 選項。 如需詳細資訊，請參閱 [ALTER TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-table-transact-sql.md)。  
  
 *event_type*  
 這是在執行之後會引發 DDL 觸發程序的 [!INCLUDE[tsql](../../includes/tsql-md.md)] 語言事件名稱。 有效的 DDL 觸發程序事件會列在 [DDL 事件](../../relational-databases/triggers/ddl-events.md)中。  
  
 *event_group*  
 這是 [!INCLUDE[tsql](../../includes/tsql-md.md)] 語言事件之預設定義群組的名稱。 在執行屬於 *event_group* 的任何 [!INCLUDE[tsql](../../includes/tsql-md.md)] 語言事件之後，便會引發 DDL 觸發程序。 有效的 DDL 觸發程序事件群組會列在 [DDL 事件群組](../../relational-databases/triggers/ddl-event-groups.md)中。 在完成執行 ALTER TRIGGER 之後，您也可以將其所涵蓋的事件類型新增至 sys.trigger_events 目錄檢視，以將 *event_group* 作為巨集。  
  
 NOT FOR REPLICATION  
 **適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。  
  
 指出當複寫代理程式修改觸發程序所涉及的資料表時，不應執行觸發程序。  
  
 *sql_statement*  
 這是觸發程序條件和動作。  
  
 針對經記憶體最佳化資料表上的觸發程序，唯一允許的最上層 *sql_statement* 是 ATOMIC 區塊。 ATOMIC 區塊內允許使用哪些 T-SQL 則受限於原生程序內允許使用的 T-SQL 而定。  
  
 EXTERNAL NAME \<method_specifier>  
 **適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。  
  
 指定繫結觸發程序之組件的方法。 此方法不可使用任何引數，且必須傳回空值。 *class_name* 必須是有效的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 識別碼，且必須以類別的形式存在於可以顯示的組件中。 這個類別不能是巢狀類別。  
  
## <a name="remarks"></a>備註  
 如需 ALTER TRIGGER 的詳細資訊，請參閱 [CREATE TRIGGER &#40;Transact-SQL&#41;](../../t-sql/statements/create-trigger-transact-sql.md)中的＜備註＞一節。  
  
> [!NOTE]  
>  自主資料庫無法使用 EXTERNAL_NAME 和 ON_ALL_SERVER 選項。  
  
## <a name="dml-triggers"></a>DML 觸發程序  
 ALTER TRIGGER 支援透過資料表和檢視表上的 INSTEAD OF 觸發程序來手動更新檢視。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會對所有觸發程序種類 (AFTER、INSTEAD-OF) 都以相同方式來套用 ALTER TRIGGER。  
  
 您可以利用 sp_settriggerorder 來指定資料表所要執行的第一個和最後一個 AFTER 觸發程序。 資料表只能指定第一個和最後一個 AFTER 觸發程序各一個。 如果同一份資料表有其他 AFTER 觸發程序，便會隨機執行它們。  
  
 如果 ALTER TRIGGER 陳述式變更第一個或最後一個觸發程序，便會卸除修改的觸發程序所設定的第一個或最後一個屬性，順序值必須利用 sp_settriggerorder 來重設。  
  
 只有在觸發的 SQL 陳述式已執行成功之後，才會執行 AFTER 觸發程序。 所謂執行成功，包括與已更新或刪除之物件關聯的所有參考串聯動作和條件約束檢查。 AFTER 觸發程序作業會檢查觸發陳述式的效果，也會檢查觸發陳述式所造成的所有參考串聯 UPDATE 和 DELETE 動作。  
  
 當因為父資料表 DELETE 的 CASCADE 結果而造成子系或參考資料表的 DELETE 動作時，會在這份子資料表上定義 DELETE 的 INSTEAD OF 觸發程序，而且觸發程序會被忽略，且會執行 DELETE 動作。  
  
## <a name="ddl-triggers"></a>DDL 觸發程序  
 DDL 觸發程序不像 DML 觸發程序，並不以結構描述為範圍。 因此，在查詢 DDL 觸發程序的相關中繼資料時，無法使用 OBJECT_ID、OBJECT_NAME、OBJECTPROPERTY 和 OBJECTPROPERTYEX。 請改用目錄檢視。 如需詳細資訊，請參閱[取得 DDL 觸發程序的詳細資訊](../../relational-databases/triggers/get-information-about-ddl-triggers.md)。  
  
## <a name="logon-triggers"></a>登入觸發程序  
 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 不支援登入事件的觸發程序。  
  
## <a name="permissions"></a>權限  
 變更 DML 觸發程序需要定義觸發程序的資料表或檢視表的 ALTER 權限。  
  
 若要變更以伺服器範圍 (ON ALL SERVER) 定義的 DDL 觸發程序或登入觸發程序，則需要伺服器的 CONTROL SERVER 權限。 若要變更以資料庫範圍 (ON DATABASE) 定義的 DDL 觸發程序，則需要目前資料庫的 ALTER ANY DATABASE DDL TRIGGER 權限。  
  
## <a name="examples"></a>範例  
 下列範例會在 AdventureWorks 2012 資料庫中建立 DML 觸發程序，當使用者試圖於 `SalesPersonQuotaHistory` 資料表中新增或變更資料時，即可將使用者定義訊息列印至用戶端。 之後，會利用 `ALTER TRIGGER` 來修改觸發程序，只在 `INSERT` 活動上套用觸發程序。 這個觸發程序很有用，因為它會提醒使用者在這份資料表中更新或插入資料列，以便同時通知 `Compensation` 部門。  
  
```sql  
CREATE TRIGGER Sales.bonus_reminder  
ON Sales.SalesPersonQuotaHistory  
WITH ENCRYPTION  
AFTER INSERT, UPDATE   
AS RAISERROR ('Notify Compensation', 16, 10);  
GO  

-- Now, change the trigger.  
ALTER TRIGGER Sales.bonus_reminder  
ON Sales.SalesPersonQuotaHistory  
AFTER INSERT  
AS RAISERROR ('Notify Compensation', 16, 10);  
GO  
```  
  
## <a name="see-also"></a>另請參閱  
 [DROP TRIGGER &#40;Transact-SQL&#41;](../../t-sql/statements/drop-trigger-transact-sql.md)   
 [ENABLE TRIGGER &#40;Transact-SQL&#41;](../../t-sql/statements/enable-trigger-transact-sql.md)   
 [DISABLE TRIGGER &#40;Transact-SQL&#41;](../../t-sql/statements/disable-trigger-transact-sql.md)   
 [EVENTDATA &#40;Transact-SQL&#41;](../../t-sql/functions/eventdata-transact-sql.md)   
 [sp_helptrigger &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-helptrigger-transact-sql.md)   
 [建立預存程序](../../relational-databases/stored-procedures/create-a-stored-procedure.md)   
 [sp_addmessage &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-addmessage-transact-sql.md)   
 [交易](../../relational-databases/native-client-ole-db-transactions/transactions.md)   
 [取得關於 DML 觸發程序的詳細資訊](../../relational-databases/triggers/get-information-about-dml-triggers.md)   
 [取得 DDL 觸發程序的資訊](../../relational-databases/triggers/get-information-about-ddl-triggers.md)   
 [sys.triggers &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-triggers-transact-sql.md)   
 [sys.trigger_events &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-trigger-events-transact-sql.md)   
 [sys.sql_modules &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-sql-modules-transact-sql.md)   
 [sys.assembly_modules &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-assembly-modules-transact-sql.md)   
 [sys.server_triggers &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-server-triggers-transact-sql.md)   
 [sys.server_trigger_events &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-server-trigger-events-transact-sql.md)   
 [sys.server_sql_modules &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-server-sql-modules-transact-sql.md)   
 [sys.server_assembly_modules &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-server-assembly-modules-transact-sql.md)   
 [對發行集資料庫進行結構描述變更](../../relational-databases/replication/publish/make-schema-changes-on-publication-databases.md)  
  
  
