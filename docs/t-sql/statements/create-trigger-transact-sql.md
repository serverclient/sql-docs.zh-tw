---
title: CREATE TRIGGER (Transact-SQL)
description: CREATE TRIGGER 陳述式的 Transact-SQL 參考用於建立 DML、DDL 或登入觸發程序。
ms.date: 10/30/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- CREATE TRIGGER
- TRIGGER
- CREATE_TRIGGER_TSQL
- TRIGGER_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- recursive DML triggers [SQL Server]
- CREATE TRIGGER statement
- multiple triggers
- deferred name resolution, DML triggers
- DML triggers, creating
- nested triggers
- server-scoped triggers
- DDL triggers, creating
- triggers [SQL Server], creating
- database-scoped triggers [SQL Server]
ms.assetid: edeced03-decd-44c3-8c74-2c02f801d3e7
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: mathoma
ms.openlocfilehash: c7bdee119d34181a59d8717957c228e6d24facdc
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98099521"
---
# <a name="create-trigger-transact-sql"></a>CREATE TRIGGER (Transact-SQL)

[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

建立 DML、DDL 或登入觸發程序。 觸發程序是一種特殊的預存程序，其會在資料庫伺服器發生事件時自動執行。 當使用者試圖透過資料操作語言 (DML) 事件來修改資料時，便會執行 DML 觸發程序。 DML 事件包括資料表或檢視的 INSERT、UPDATE 或 DELETE 陳述式。 無論資料表的資料列有無受到影響，這些觸發程序皆會在引發任何有效事件時引發。 如需詳細資訊，請參閱 [DML Triggers](../../relational-databases/triggers/dml-triggers.md)。  
  
DDL 觸發程序的執行目的是回應各種資料定義語言 (DDL) 事件。 這些事件主要是對應到 [!INCLUDE[tsql](../../includes/tsql-md.md)] CREATE、ALTER 和 DROP 陳述式，以及執行類似 DDL 作業的特定系統預存程序。 

登入觸發程序則會因回應在建立使用者工作階段時引發的 LOGON 事件而引發。 您可以直接從 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式建立觸發程序，也可以使用 [!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[dnprdnshort](../../includes/dnprdnshort-md.md)] 通用語言執行平台 (CLR) 所建立的組件方法來建立觸發程序，並將其上傳到 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 可讓您針對任何特定陳述式建立多個觸發程序。  
  
> [!IMPORTANT]  
>  觸發程序內的惡意程式碼可能在擴大的權限下執行。 如需如何降低這項威脅的詳細資訊，請參閱[管理觸發程序安全性](../../relational-databases/triggers/manage-trigger-security.md)。  
  
> [!NOTE]  
>  本文會探討如何將 .NET Framework CLR 整合至 SQL Server。 CLR 整合不適用於 Azure SQL Database。  
  
![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="sql-server-syntax"></a>SQL Server 語法  
  
```syntaxsql
-- SQL Server Syntax  
-- Trigger on an INSERT, UPDATE, or DELETE statement to a table or view (DML Trigger)  
  
CREATE [ OR ALTER ] TRIGGER [ schema_name . ]trigger_name   
ON { table | view }   
[ WITH <dml_trigger_option> [ ,...n ] ]  
{ FOR | AFTER | INSTEAD OF }   
{ [ INSERT ] [ , ] [ UPDATE ] [ , ] [ DELETE ] }   
[ WITH APPEND ]  
[ NOT FOR REPLICATION ]   
AS { sql_statement  [ ; ] [ ,...n ] | EXTERNAL NAME <method specifier [ ; ] > }  
  
<dml_trigger_option> ::=  
    [ ENCRYPTION ]  
    [ EXECUTE AS Clause ]  
  
<method_specifier> ::=  
    assembly_name.class_name.method_name  
  
```  
  
```syntaxsql
-- SQL Server Syntax  
-- Trigger on an INSERT, UPDATE, or DELETE statement to a 
-- table (DML Trigger on memory-optimized tables)  
  
CREATE [ OR ALTER ] TRIGGER [ schema_name . ]trigger_name   
ON { table }   
[ WITH <dml_trigger_option> [ ,...n ] ]  
{ FOR | AFTER }   
{ [ INSERT ] [ , ] [ UPDATE ] [ , ] [ DELETE ] }   
AS { sql_statement  [ ; ] [ ,...n ] }  
  
<dml_trigger_option> ::=  
    [ NATIVE_COMPILATION ]  
    [ SCHEMABINDING ]  
    [ EXECUTE AS Clause ]  
  
```  
  
```syntaxsql
-- Trigger on a CREATE, ALTER, DROP, GRANT, DENY, 
-- REVOKE or UPDATE statement (DDL Trigger)  
  
CREATE [ OR ALTER ] TRIGGER trigger_name   
ON { ALL SERVER | DATABASE }   
[ WITH <ddl_trigger_option> [ ,...n ] ]  
{ FOR | AFTER } { event_type | event_group } [ ,...n ]  
AS { sql_statement  [ ; ] [ ,...n ] | EXTERNAL NAME < method specifier >  [ ; ] }  
  
<ddl_trigger_option> ::=  
    [ ENCRYPTION ]  
    [ EXECUTE AS Clause ]  
  
```  
  
```syntaxsql
-- Trigger on a LOGON event (Logon Trigger)  
  
CREATE [ OR ALTER ] TRIGGER trigger_name   
ON ALL SERVER   
[ WITH <logon_trigger_option> [ ,...n ] ]  
{ FOR| AFTER } LOGON    
AS { sql_statement  [ ; ] [ ,...n ] | EXTERNAL NAME < method specifier >  [ ; ] }  
  
<logon_trigger_option> ::=  
    [ ENCRYPTION ]  
    [ EXECUTE AS Clause ]  
  
```  
  
## <a name="azure-sql-database-syntax"></a>Azure SQL Database 語法  
  
```syntaxsql
-- Azure SQL Database Syntax   
-- Trigger on an INSERT, UPDATE, or DELETE statement to a table or view (DML Trigger)  
  
CREATE [ OR ALTER ] TRIGGER [ schema_name . ]trigger_name   
ON { table | view }   
 [ WITH <dml_trigger_option> [ ,...n ] ]   
{ FOR | AFTER | INSTEAD OF }   
{ [ INSERT ] [ , ] [ UPDATE ] [ , ] [ DELETE ] }   
  AS { sql_statement  [ ; ] [ ,...n ] [ ; ] > }  
  
<dml_trigger_option> ::=   
        [ EXECUTE AS Clause ]  
  
```  
  
```syntaxsql
-- Azure SQL Database Syntax  
-- Trigger on a CREATE, ALTER, DROP, GRANT, DENY, 
-- REVOKE, or UPDATE STATISTICS statement (DDL Trigger)   
  
CREATE [ OR ALTER ] TRIGGER trigger_name   
ON { DATABASE }   
 [ WITH <ddl_trigger_option> [ ,...n ] ]   
{ FOR | AFTER } { event_type | event_group } [ ,...n ]   
AS { sql_statement  [ ; ] [ ,...n ]  [ ; ] }  
  
<ddl_trigger_option> ::=   
    [ EXECUTE AS Clause ]  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
OR ALTER  
**適用於**：Azure [!INCLUDE[ssSDS](../../includes/sssds-md.md)][!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] SP1 開始)。 
  
只有在觸發程序已存在時，才能有條件地更改它。 
  
*schema_name*  
DML 觸發程序所屬的結構描述名稱。 DML 觸發程序範圍僅限於其建立所在之資料表或檢視表的結構描述。 您不能為 DDL 或登入觸發程序指定 *schema_name*。  
  
*trigger_name*  
觸發程序的名稱。 *trigger_name* 必須遵循 [識別碼](../../relational-databases/databases/database-identifiers.md)的規則，不過 *trigger_name* 的開頭不能是 # 或 ##。  
  
*table* | *view*  
執行 DML 觸發程序的資料表或檢視表。 這個資料表或檢視表有時也稱為觸發程序資料表或觸發程序檢視表。 您可以選擇性地指定資料表或檢視的完整名稱。 您僅可透過 INSTEAD OF 觸發程序來參考檢視表。 您不能在本機或全域暫存資料表上定義 DML 觸發程序。  
  
DATABASE  
將 DDL 觸發程序的範圍套用在目前資料庫上。 若有指定，每當目前資料庫中出現 *event_type* 或 *event_group* 時，都會引發這個觸發程序。  
  
ALL SERVER  
**適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。  
  
將 DDL 或登入觸發程序的範圍套用在目前伺服器上。 若有指定，每當目前伺服器中的任何位置出現 *event_type* 或 *event_group* 時，都會引發這個觸發程序。  
  
WITH ENCRYPTION  
**適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。  
  
遮蔽 CREATE TRIGGER 陳述式的文字。 使用 WITH ENCRYPTION 可防止在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 複寫中發行這個觸發程序。 CLR 觸發程序不能指定 WITH ENCRYPTION。  
  
EXECUTE AS  
指定用來執行這個觸發程序的安全性內容。 可讓您控制 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體要利用哪個使用者帳戶來驗證觸發程序所參考之任何資料庫物件的權限。  
  
如果觸發程序是在經記憶體最佳化的資料表上，則這個選項為必要。  
  
如需詳細資訊，請參閱 [EXECUTE AS Clause &#40;Transact-SQL&#41;](../../t-sql/statements/execute-as-clause-transact-sql.md)。  
  
NATIVE_COMPILATION  
表示觸發程序是原生編譯的。  
  
如果觸發程序是在經記憶體最佳化的資料表上，則這個選項為必要。  
  
SCHEMABINDING  
確保使用者無法卸除或改變觸發程序所參考的資料表。  
  
如果觸發程序是在經記憶體最佳化的資料表上，則這個選項為必要，且其不支援傳統資料表上的觸發程序。  
  
FOR | AFTER  
FOR 或 AFTER 指定只在觸發 SQL 陳述式指定的所有作業都成功啟動時，才引發 DML 觸發程序。 所有參考的串聯動作和條件約束檢查也都必須成功之後，才會引發這個觸發程序。  
  
您不能在檢視表上定義 AFTER 觸發程序。  
  
INSTEAD OF  
指定要啟動 DML 觸發程序，而「不是」觸發 SQL 陳述式，因此會覆寫觸發陳述式的動作。 您不能針對 DDL 或登入觸發程序指定 INSTEAD OF。  
  
您最多只能在資料表或檢視表上的每個 INSERT、UPDATE 或 DELETE 陳述式，各定義一個 INSTEAD OF 觸發程序。 不過，您可以定義檢視表中的檢視表，讓每份檢視表都有它自己的 INSTEAD OF 觸發程序。  
  
您不能在使用 WITH CHECK OPTION 的可更新檢視表上定義 INSTEAD OF 觸發程序。 將 INSTEAD OF 觸發程序新增至已指定 WITH CHECK OPTION 的可更新檢視表時，這樣做會發生錯誤。 您可以在定義 INSTEAD OF 觸發程序之前，使用 ALTER VIEW 來移除這個選項。  
  
{ [ DELETE ] [ , ] [ INSERT ] [ , ] [ UPDATE ] }  
指定當試圖針對這份資料表或檢視表執行此動作時，會啟動 DML 觸發程序的資料修改陳述式。 請至少指定一個選項。 您可以在觸發程序定義中，依照任何順序來使用這些選項的任何組合。  
  
針對 INSTEAD OF 觸發程序，如果資料表上含有指定串聯動作 ON DELETE 的參考關聯性，您就不能在該資料表上使用 DELETE 選項。 同樣地，如果資料表上含有指定串聯動作 ON UPDATE 的參考關聯性，您就不能在該資料表上使用 UPDATE 選項。  
  
WITH APPEND  
**適用於**： [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 至 [!INCLUDE[ssKilimanjaro](../../includes/sskilimanjaro-md.md)]。  
  
指定應該加入現有類型的其他觸發程序。 當使用 INSTEAD OF 觸發程序或明確指示 AFTER 觸發程序時，便不能使用 WITH APPEND。 為了與舊版相容，請僅在指定 FOR 且不含 INSTEAD OF 或 AFTER 時，才使用 WITH APPEND。 如果您使用 EXTERNAL NAME (亦即，如果觸發程序是 CLR 觸發程序)，就不能指定 WITH APPEND。  
  
*event_type*  
在啟動之後會引發 DDL 觸發程序的 [!INCLUDE[tsql](../../includes/tsql-md.md)] 語言事件名稱。 有效的 DDL 觸發程序事件會列在 [DDL 事件](../../relational-databases/triggers/ddl-events.md)中。  
  
*event_group*  
[!INCLUDE[tsql](../../includes/tsql-md.md)] 語言事件的預先定義群組名稱。 在啟動屬於 *event_group* 的任何 [!INCLUDE[tsql](../../includes/tsql-md.md)] 語言事件之後，便會引發 DDL 觸發程序。 有效的 DDL 觸發程序事件群組會列在 [DDL 事件群組](../../relational-databases/triggers/ddl-event-groups.md)中。  
  
在完成執行 CREATE TRIGGER 之後，您也可以將其所涵蓋的事件類型新增至 sys.trigger_events 目錄檢視，以將 *event_group* 作為巨集。  
  
NOT FOR REPLICATION  
**適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。  
  
指出當複寫代理程式修改觸發程序所含的資料表時，不應執行觸發程序。  
  
*sql_statement*  
觸發程序的條件和動作。 觸發程序的條件可指定一些其他準則，以判斷所嘗試的 DML、DDL 或登入事件是否導致觸發程序動作的執行。  
  
當嘗試作業時，[!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式所指定的觸發程序動作便會生效。  
  
觸發程序可以包括任意數目和任何類型的 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式，但有例外。 如需詳細資訊，請參閱＜備註＞。 觸發程序的設計用意是根據資料修改或定義陳述式，來檢查或變更資料，而不應將資料傳回給使用者。 觸發程序的 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式通常會包括[流程控制語言](~/t-sql/language-elements/control-of-flow.md)。  
  
DML 觸發程序會使用 deleted 和 inserted 邏輯 (概念) 資料表。 它們的結構類似於定義觸發程序的資料表，也就是使用者動作試圖處理的資料表。 deleted 和 inserted 資料表用來保留使用者動作可能已變更之資料列的舊值或新值。 例如，若要擷取 `deleted` 資料表中的所有值，請使用：  
  
```sql  
SELECT * FROM deleted;  
```  
  
如需詳細資訊，請參閱[使用插入和刪除的資料表](../../relational-databases/triggers/use-the-inserted-and-deleted-tables.md)。  
  
DDL 和登入觸發程序會使用 [EVENTDATA &#40;Transact-SQL&#41;](../../t-sql/functions/eventdata-transact-sql.md) 函式來擷取觸發事件的相關資訊。 如需詳細資訊，請參閱[使用 EVENTDATA 函式](../../relational-databases/triggers/use-the-eventdata-function.md)。  
  
[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 允許使用資料表或檢視表的 INSTEAD OF 觸發程序來更新 **text**、**ntext** 或 **image** 資料行。  
  
> [!IMPORTANT]
>  [!INCLUDE[msCoName](../../includes/msconame-md.md)][!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的未來版本將會移除 **ntext**、**text** 及 **image** 資料類型。 請避免在新的開發工作中使用這些資料類型，並規劃修改目前在使用這些資料類型的應用程式。 請改用 [nvarchar(max)](../../t-sql/data-types/nchar-and-nvarchar-transact-sql.md)、 [varchar(max)](../../t-sql/data-types/char-and-varchar-transact-sql.md)和 [varbinary(max)](../../t-sql/data-types/binary-and-varbinary-transact-sql.md) 。 AFTER 和 INSTEAD OF 兩個觸發程序都支援插入和刪除資料表中的 **varchar(MAX)** 、**nvarchar(MAX)** 和 **varbinary(MAX)** 資料。  
  
針對經記憶體最佳化資料表上的觸發程序，唯一允許的最上層 *sql_statement* 是 ATOMIC 區塊。 ATOMIC 區塊內允許使用哪些 T-SQL 則受限於原生程序內允許使用的 T-SQL 而定。  
  
\< method_specifier > 
**適用於**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。  
  
對於 CLR 觸發程序，指定繫結觸發程序的組件方法。 此方法不可使用任何引數，且必須傳回空值。 *class_name* 必須是有效的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 識別碼，且必須以類別的形式存在於可以顯示的組件中。 如果該類別的名稱符合命名空間規定，利用 '.' 來分隔命名空間的各個部分，您就必須用 [ ] 或 " " 分隔字元來分隔類別名稱。 這個類別不能是巢狀類別。  
  
> [!NOTE]  
>  依預設，會關閉 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行 CLR 程式碼的能力。 您可以建立、修改及卸除參考受控碼模組的資料庫物件，但除非您使用 [sp_configure](../../relational-databases/system-stored-procedures/sp-configure-transact-sql.md) 來啟用 [clr enabled 選項](../../database-engine/configure-windows/clr-enabled-server-configuration-option.md)；否則，這些參考就不會在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體中執行。  
  
## <a name="remarks-for-dml-triggers"></a>DML 觸發程序的備註  
DML 觸發程序常會用於執行商務規則與資料完整性。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 利用 ALTER TABLE 及 CREATE TABLE 陳述式提供宣告式參考完整性 (DRI)。 不過，DRI 並不提供跨資料庫的參考完整性。 參考完整性是指資料表主索引鍵和外部索引鍵之間的關聯性規則。 若要強制執行參考完整性，請在 ALTER TABLE 和 CREATE TABLE 中，使用 PRIMARY KEY 和 FOREIGN KEY 條件約束。 如果觸發程序資料表有條件約束，便會在 INSTEAD OF 觸發程序執行之後、AFTER 觸發程序執行之前，檢查這些條件約束。 如果違反條件約束，便會回復 INSTEAD OF 觸發程序動作，而不會引發 AFTER 觸發程序。  
  
您可以使用 sp_settriggerorder 來指定資料表要執行的第一個和最後一個 AFTER 觸發程序。 您只能在資料表的每個 INSERT、UPDATE 和 DELETE 作業上，指定頭尾各一個 AFTER 觸發程序。 如果同一份資料表有其他 AFTER 觸發程序，便會隨機執行。  
  
如果 ALTER TRIGGER 陳述式變更第一個或最後一個觸發程序，便會卸除已修改觸發程序上設定的第一個或最後一個屬性，因此您必須使用 sp_settriggerorder 來重設順序值。  
  
只有在觸發的 SQL 陳述式已執行成功之後，才會執行 AFTER 觸發程序。 所謂執行成功，包括與已更新或刪除之物件關聯的所有參考串聯動作和條件約束檢查。 AFTER 不會以遞迴方式在相同資料表上引發 INSTEAD OF 觸發程序。  
  
如果資料表上定義的 INSTEAD OF 觸發程序是針對通常會再次引發 INSTEAD OF 觸發程序的資料表來執行陳述式，則不會遞迴呼叫這個觸發程序。 相反地，陳述式會依照資料表不具 INSTEAD OF 觸發程序的方式來處理，並會啟動條件約束作業和 AFTER 觸發程序執行的鏈結。 例如，如果某個觸發程序定義為資料表的 INSTEAD OF INSERT 觸發程序， 且觸發程序會在相同資料表上執行 INSERT 陳述式，則 INSTEAD OF 觸發程序所啟動的 INSERT 陳述式不會再次呼叫觸發程序。 觸發程序所啟動的 INSERT 會啟動程序，以執行條件約束動作，並引發資料表定義的任何 AFTER INSERT 觸發程序。  
  
當檢視表上定義的 INSTEAD OF 觸發程序是針對通常會再次引發 INSTEAD OF 觸發程序的檢視表來執行陳述式，則不會遞迴呼叫這個觸發程序。 相反地，陳述式會解析成針對檢視下的基底資料表來進行的修改。 在這個情況下，檢視定義必須符合可更新之檢視的所有限制。 如需可更新檢視的定義，請參閱[透過檢視修改資料](../../relational-databases/views/modify-data-through-a-view.md)。  
  
例如，如果某個觸發程序定義為檢視表的 INSTEAD OF UPDATE 觸發程序， 且觸發程序會執行參考相同檢視表的 UPDATE 陳述式，則 INSTEAD OF 觸發程序所啟動的 UPDATE 陳述式不會再次呼叫觸發程序。 系統會依照檢視表不具 INSTEAD OF 觸發程序的方式，來處理檢視表中由觸發程序所啟動的 UPDATE。 UPDATE 所變更的資料行必須解析成單一基底資料表。 基礎基底資料表的每項修改都會啟動套用條件約束及引發定義給資料表之 AFTER 觸發程序的鏈結。  
  
### <a name="testing-for-update-or-insert-actions-to-specific-columns"></a>測試特定資料行的 UPDATE 或 INSERT 動作  
您可以設計一個 [!INCLUDE[tsql](../../includes/tsql-md.md)] 觸發程序，以依據特定資料行的 UPDATE 或 INSERT 修改來進行特定動作。 請在觸發程序的主體中，使用 [UPDATE()](../../t-sql/functions/update-trigger-functions-transact-sql.md) 或 [COLUMNS_UPDATED](../../t-sql/functions/columns-updated-transact-sql.md) 來完成這個目的。 UPDATE() 會測試單一資料行所嘗試的 UPDATE 或 INSERT。 COLUMNS_UPDATED 會測試多個資料行所執行的 UPDATE 或 INSERT 動作。 此函式會傳回一個位元模式，指出插入或更新了哪些資料行。  
  
### <a name="trigger-limitations"></a>觸發程序限制  
CREATE TRIGGER 必須是批次中的第一個陳述式，且只能套用至一份資料表。  
  
觸發程序只會建立在目前資料庫中；不過，觸發程序可以參考在目前資料庫之外的物件。  
  
如果指定觸發程序結構描述名稱來限定觸發程序，請依照相同方式來限定資料表名稱。  
  
您可以在相同 CREATE TRIGGER 陳述式中，定義多個使用者動作 (如 INSERT 和 UPDATE) 的相同觸發程序動作。  
  
您不能在定義了 DELETE/UPDATE 動作串聯的資料表上，定義 INSTEAD OF DELETE/UPDATE 觸發程序。  
  
您可以在觸發程序內指定任何 SET 陳述式。 在觸發程序的執行期間，所選取的 SET 選項會持續有效，之後，會還原為先前的設定。  
  
當引發觸發程序時，如同預存程序一樣，結果會傳回發出呼叫的應用程式。 若要防止因引發觸發程序而將結果傳回給應用程式，請不要包含會傳回結果之 SELECT 陳述式或會在觸發程序中執行變數指派的陳述式。 如果觸發程序包含會將結果傳回給使用者之 SELECT 陳述式或會執行變數指派的陳述式，則需要特殊處理。 您必須將所傳回結果寫入允許修改觸發程序資料表的每個應用程式。 如果必須在觸發程序內發生變數指派，請在觸發程序開頭使用 SET NOCOUNT 陳述式，以防止傳回任何結果集。  
  
雖然 TRUNCATE TABLE 陳述式實際上是 DELETE 陳述式，但是它並不會啟動觸發程序，因為此作業不會記錄個別的資料列刪除。 但是，只有具有執行 TRUNCATE TABLE 陳述式權限的使用者才需要擔心這種方式會不小心阻止 DELETE 觸發程序。  
  
WRITETEXT 陳述式，不論有沒有記錄，都不會啟動觸發程序。  
  
DML 觸發程序中不可使用下列 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式：  

- ALTER DATABASE
- CREATE DATABASE
- DROP DATABASE
- RESTORE DATABASE
- RESTORE LOG
- RECONFIGURE

此外，當 DML 觸發程序用於觸發動作的目標資料表或檢視表時，不可在 DML 觸發程序的主體內使用下列 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式。  
  
- CREATE INDEX (包括 CREATE SPATIAL INDEX 和 CREATE XML INDEX)
- ALTER INDEX
- DROP INDEX
- DROP TABLE
- DBCC DBREINDEX
- ALTER PARTITION FUNCTION
- 用來執行下列動作的 ALTER TABLE：
    - 新增、修改或卸除資料行。
    - 切換資料分割。
    - 加入或卸除 PRIMARY KEY 或 UNIQUE 條件約束。

> [!NOTE]  
>  由於 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 不支援系統資料表的使用者自訂觸發程序，因此，我們建議您不要在系統資料表上建立使用者自訂觸發程序。 

### <a name="optimizing-dml-triggers"></a>最佳化 DML 觸發程序
觸發程序會在交易中運作 (隱含或其他方式)，並在開啟時鎖定資源。 這項鎖定會持續，直到確認 (使用 COMMIT) 或拒絕 (使用 ROLLBACK) 交易為止。 觸發程序執行時間越長，封鎖其他處理序的機率越高。 因此，撰寫觸發程序時，請盡可能降低其持續時間。 要確保較短持續時間的方法之一，是在 DML 陳述式變更零個資料列時釋放觸發程序。 

若要針對不會變更任何資料列的命令釋放觸發程序，請使用系統變數 [ROWCOUNT_BIG](../functions/rowcount-big-transact-sql.md)。 

下列 T-SQL 程式碼片段示範如何針對不會變更任何資料列的命令釋放觸發程序。 此程式碼應該出現在每個 DML 觸發程序的開頭：

```sql
IF (ROWCOUNT_BIG() = 0)
RETURN;
```
  
  
## <a name="remarks-for-ddl-triggers"></a>DDL 觸發程序的備註  
如同標準觸發程序，DDL 觸發程序也會啟動預存程序來回應事件。 但它們並不會為了回應資料表或檢視表的 UPDATE、INSERT 或 DELETE 陳述式而執行，這一點就和標準觸發程序不同。 相反地，其主要執行目的是為了回應資料定義語言 (DDL) 陳述式。 這些陳述式類型包括 CREATE、ALTER、DROP、GRANT、DENY、REVOKE 和 UPDATE STATISTICS。 執行類似 DDL 作業的某些系統預存程序也可能引發 DDL 觸發程序。  
  
> [!IMPORTANT]  
>  請測試 DDL 觸發程序，以判斷它們對執行之系統預存程序的回應。 例如，CREATE TYPE 陳述式與 sp_addtype 及 sp_rename 預存程序都會引發針對 CREATE_TYPE 事件建立的 DDL 觸發程序。  
  
如需 DDL 觸發程序的詳細資訊，請參閱 [DDL 觸發程序](../../relational-databases/triggers/ddl-triggers.md)。  
  
若是影響區域或全域暫存資料表與預存程序的事件，DDL 觸發程序就不會為了回應這類事件而引發。  
  
DDL 觸發程序不像 DML 觸發程序，並不以結構描述為範圍。 因此，您不能使用 OBJECT_ID、OBJECT_NAME、OBJECTPROPERTY 和 OBJECTPROPERTYEX 等函式來查詢 DDL 觸發程序的相關中繼資料。 請改用目錄檢視。 如需詳細資訊，請參閱[取得 DDL 觸發程序的詳細資訊](../../relational-databases/triggers/get-information-about-ddl-triggers.md)。  
  
> [!NOTE]  
>  伺服器範圍的 DDL 觸發程序會出現在 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 物件總管的 [觸發程序] 資料夾中。 這個資料夾在 **[伺服器物件]** 資料夾之下。 資料庫範圍的 DDL 觸發程序則會出現在 [資料庫觸發程序] 資料夾中。 這個資料夾在對應資料庫的 **[可程式性]** 資料夾之下。  
  
## <a name="logon-triggers"></a>登入觸發程序  
登入觸發程序會執行預存程序來回應 LOGON 事件。 當使用 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體建立使用者工作階段時，就會引發這個事件。 登入觸發程序會在登入驗證階段結束之後、使用者工作階段建立之前引發。 因此，從觸發程序內產生且通常會傳遞給使用者的所有訊息 (例如錯誤訊息和來自 PRINT 陳述式的訊息)，都會轉向至 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 錯誤記錄檔。 如需詳細資訊，請參閱[登入觸發程序](../../relational-databases/triggers/logon-triggers.md)。  
  
如果驗證失敗，就不會引發登入觸發程序。  
  
登入觸發程序不支援分散式交易。 當引發含有分散式交易的登入觸發程序時，就會傳回錯誤 3969。  
  
### <a name="disabling-a-logon-trigger"></a>停用登入觸發程序  
登入觸發程序可以有效地防止所有使用者成功連接到 [!INCLUDE[ssDE](../../includes/ssde-md.md)] ，包括 **sysadmin** 固定伺服器角色的成員。 當登入觸發程序防止連接時， **sysadmin** 固定伺服器角色的成員可以使用專用管理員連接或以最低組態模式 (-f) 啟動 [!INCLUDE[ssDE](../../includes/ssde-md.md)] ，藉以進行連接。 如需詳細資訊，請參閱 [Database Engine Service Startup Options](../../database-engine/configure-windows/database-engine-service-startup-options.md)。  
  
## <a name="general-trigger-considerations"></a>一般觸發程序考量  
  
### <a name="returning-results"></a>傳回結果  
SQL Server 的未來版本將移除從觸發程序傳回結果的功能。 如果觸發程序會傳回結果集，其可能會導致非專用應用程式發生非預期的行為。 在新的開發工作中，請避免從觸發程序傳回結果集，並計畫修改目前如此運作的應用程式。 若要避免觸發程序傳回結果集，請將[不允許來自觸發程序的結果選項](../../database-engine/configure-windows/disallow-results-from-triggers-server-configuration-option.md)設為 1。  
  
登入觸發程序一律不允許傳回結果集，且您無法設定這項行為。 如果登入觸發程序產生結果集，就無法啟動觸發程序，並引發會拒絕觸發程序的登入嘗試。  
  
### <a name="multiple-triggers"></a>多重觸發程序  
[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 可讓您針對每個 DML、DDL 或 LOGON 事件建立多個觸發程序。 例如，如果針對已有 UPDATE 觸發程序的資料表執行 CREATE TRIGGER FOR UPDATE，便會建立其他更新觸發程序。 在舊版的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中，每份資料表的每個 INSERT、UPDATE 或 DELETE 資料修改事件都只能有一個觸發程序。  
  
### <a name="recursive-triggers"></a>遞迴觸發程序  
當使用 ALTER DATABASE 來啟用 RECURSIVE_TRIGGERS 設定時，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 也支援觸發程序的遞迴叫用。  
  
遞迴觸發程序有可能產生下列遞迴類型：  
  
-   間接遞迴  
  
     當進行間接遞迴時，應用程式會更新 T1 資料表。 這會引發 TR1 觸發程序，更新 T2 資料表。 接著引發 T2 觸發程序，並更新 T1 資料表。  
  
-   直接遞迴  
  
     在直接遞迴中，應用程式會更新 T1 資料表。 這會引發 TR1 觸發程序，更新 T1 資料表。 由於更新了 T1 資料表，因此，又會引發 TR1 觸發程序，依此類推。  
  
下列範例會使用間接和直接觸發程序遞迴。它假設 T1 資料表定義了 TR1 和 TR2 這兩個更新觸發程序。 TR1 觸發程序會遞迴更新 T1 資料表。 UPDATE 陳述式會執行 TR1 和 TR2 各一次。 此外，啟動 TR1 時會觸發執行 TR1 (遞迴) 和 TR2。 特定觸發程序的 inserted 和 deleted 資料表包含只對應於呼叫觸發程序之 UPDATE 陳述式的資料表。  
  
> [!NOTE]  
>  只有在利用 ALTER DATABASE 來啟用 RECURSIVE_TRIGGERS 設定時，才會發生上述行為。 針對特定事件定義的多個觸發程序，並沒有任何定義的執行順序。 每個觸發程序都應該是獨立自足的。  
  
停用 RECURSIVE_TRIGGERS 設定只會防止直接遞迴。 如果也要停用間接遞迴，請利用 sp_configure，將 nested triggers 伺服器選項設為 0。  
  
如果有任何觸發程序執行 ROLLBACK TRANSACTION，則不論巢狀層級為何，都不會執行任何觸發程序。  
  
### <a name="nested-triggers"></a>巢狀觸發程序  
您最多可以將觸發程序巢狀化為 32 個層級。 如果觸發程序變更了一份資料表，但這份資料表上有其他觸發程序，就會啟動第二個觸發程序，而第二個觸發程序可能會再呼叫第三個觸發程序，依此類推。 如果鏈結中的任何觸發程序造成無限迴圈，就會超出巢狀層級，並取消觸發程序。 當 [!INCLUDE[tsql](../../includes/tsql-md.md)] 觸發程序參考 CLR 常式、類型或彙總來啟動受控碼時，這項參考也會計入 32 層巢狀限制的一層。 若是從受控碼內叫用的方法，則不計入這項限制。  
  
若要停用巢狀觸發程序，請將 sp_configure 的 nested triggers 選項設成 0 (關閉)。 預設設定可支援巢狀觸發程序。 如果關閉巢狀觸發程序，遞迴觸發程序也會停用，不論使用 ALTER DATABASE 設定的 RECURSIVE_TRIGGERS 設定為何，都是如此。  
  
即使 [巢狀觸發程序] 伺服器設定選項為 0，仍會引發 INSTEAD OF 觸發程序內部的第一個巢狀 AFTER 觸發程序。 不過，在此設定下，不會引發後續的 AFTER 觸發程序。 檢閱應用程式的巢狀觸發程序，以判斷當 [巢狀觸發程序] 伺服器設定選項設為 0 時，應用程式是否會遵循您的商務規則。 若否，請進行適當的修改。  
  
### <a name="deferred-name-resolution"></a>延遲名稱解析  
[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 允許 [!INCLUDE[tsql](../../includes/tsql-md.md)] 預存程序、觸發程序和批次參考在編譯時期並不存在的資料表。 這項功能稱為延遲名稱解析。  
  
## <a name="permissions"></a>權限  
若要建立 DML 觸發程序，必須具備要建立觸發程序之資料表或檢視表的 ALTER 權限。  
  
若要建立伺服器範圍 (ON ALL SERVER) 的 DDL 觸發程序或登入觸發程序，需要伺服器的 CONTROL SERVER 權限。 若要建立資料庫範圍 (ON DATABASE) 的 DDL 觸發程序，需要目前資料庫的 ALTER ANY DATABASE DDL TRIGGER 權限。  
  
## <a name="examples"></a>範例  
  
### <a name="a-using-a-dml-trigger-with-a-reminder-message"></a>A. 使用包含提示訊息的 DML 觸發程序  
當任何人嘗試在 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 資料庫的 `Customer` 資料表中新增或變更資料時，以下 DML 觸發程序會向用戶端列印一則訊息。  
  
```sql  
CREATE TRIGGER reminder1  
ON Sales.Customer  
AFTER INSERT, UPDATE   
AS RAISERROR ('Notify Customer Relations', 16, 10);  
GO  
```  
  
### <a name="b-using-a-dml-trigger-with-a-reminder-e-mail-message"></a>B. 使用包含提示電子郵件訊息的 DML 觸發程序  
當 `MaryM` 資料表有了改變時，以下範例會向指定的人 (`Customer`) 傳送一則電子郵件訊息。  
  
```sql  
CREATE TRIGGER reminder2  
ON Sales.Customer  
AFTER INSERT, UPDATE, DELETE   
AS  
   EXEC msdb.dbo.sp_send_dbmail  
        @profile_name = 'AdventureWorks2012 Administrator',  
        @recipients = 'danw@Adventure-Works.com',  
        @body = 'Don''t forget to print a report for the sales force.',  
        @subject = 'Reminder';  
GO  
```  
  
### <a name="c-using-a-dml-after-trigger-to-enforce-a-business-rule-between-the-purchaseorderheader-and-vendor-tables"></a>C. 使用 DML AFTER 觸發程序在 PurchaseOrderHeader 與 Vendor 資料表之間執行商務規則  
由於 CHECK 條件約束只會參考定義了資料行層級或資料表層級條件約束的資料行，因此您必須將任何跨資料表的條件約束 (此案例為商務規則) 定義為觸發程序。  
  
以下範例會在 AdventureWorks2012 資料庫中建立 DML 觸發程序。 試圖在 `PurchaseOrderHeader` 資料表中插入新的採購單時，這個觸發程序會檢查確認供應商的信用評等良好 (非 5 顆星)。 為了取得供應商的信用評等，必須參考 `Vendor` 資料表。 如果信用評等太低，即會顯示訊息，且不會執行插入動作。  
  
```sql  
-- This trigger prevents a row from being inserted in the Purchasing.PurchaseOrderHeader 
-- table when the credit rating of the specified vendor is set to 5 (below average).  
  
CREATE TRIGGER Purchasing.LowCredit ON Purchasing.PurchaseOrderHeader  
AFTER INSERT  
AS  
IF (ROWCOUNT_BIG() = 0)
RETURN;
IF EXISTS (SELECT *  
           FROM Purchasing.PurchaseOrderHeader AS p   
           JOIN inserted AS i   
           ON p.PurchaseOrderID = i.PurchaseOrderID   
           JOIN Purchasing.Vendor AS v   
           ON v.BusinessEntityID = p.VendorID  
           WHERE v.CreditRating = 5  
          )  
BEGIN  
RAISERROR ('A vendor''s credit rating is too low to accept new  
purchase orders.', 16, 1);  
ROLLBACK TRANSACTION;  
RETURN   
END;  
GO  
  
-- This statement attempts to insert a row into the PurchaseOrderHeader table  
-- for a vendor that has a below average credit rating.  
-- The AFTER INSERT trigger is fired and the INSERT transaction is rolled back.  
  
INSERT INTO Purchasing.PurchaseOrderHeader (RevisionNumber, Status, EmployeeID,  
VendorID, ShipMethodID, OrderDate, ShipDate, SubTotal, TaxAmt, Freight)  
VALUES (  
2  
,3  
,261  
,1652  
,4  
,GETDATE()  
,GETDATE()  
,44594.55  
,3567.564  
,1114.8638 );  
GO  
  
```  
  
### <a name="d-using-a-database-scoped-ddl-trigger"></a>D. 使用僅限於資料庫的 DDL 觸發程序  
以下範例利用 DDL 觸發程序避免卸除資料庫中的同義字。  
  
```sql  
CREATE TRIGGER safety   
ON DATABASE   
FOR DROP_SYNONYM  
AS   
IF (@@ROWCOUNT = 0)
RETURN;
   RAISERROR ('You must disable Trigger "safety" to remove synonyms!', 10, 1)  
   ROLLBACK  
GO  
DROP TRIGGER safety  
ON DATABASE;  
GO  
```  
  
### <a name="e-using-a-server-scoped-ddl-trigger"></a>E. 使用僅限於伺服器的 DDL 觸發程序  
以下範例會在目前伺服器執行個體出現任何 CREATE DATABASE 事件時，利用 DDL 觸發程序來列印訊息，並利用 `EVENTDATA` 函數來擷取對應 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式的文字。 如需更多在 DDL 觸發程序中使用 EVENTDATA 的範例，請參閱[使用 EVENTDATA 函式](../../relational-databases/triggers/use-the-eventdata-function.md)。  
  
**適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。  
  
```sql  
CREATE TRIGGER ddl_trig_database   
ON ALL SERVER   
FOR CREATE_DATABASE   
AS   
    PRINT 'Database Created.'  
    SELECT EVENTDATA().value('(/EVENT_INSTANCE/TSQLCommand/CommandText)[1]','nvarchar(max)')  
GO  
DROP TRIGGER ddl_trig_database  
ON ALL SERVER;  
GO  
```  
  
### <a name="f-using-a-logon-trigger"></a>F. 使用登入觸發程序  
在以下登入觸發程序範例中，如果已有三個使用者工作階段以 *login_test* 登入執行，則嘗試以該登入的成員身分登入 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 時會遭拒。  
  
**適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。  
  
```sql  
USE master;  
GO  
CREATE LOGIN login_test WITH PASSWORD = '3KHJ6dhx(0xVYsdf' MUST_CHANGE,  
    CHECK_EXPIRATION = ON;  
GO  
GRANT VIEW SERVER STATE TO login_test;  
GO  
CREATE TRIGGER connection_limit_trigger  
ON ALL SERVER WITH EXECUTE AS 'login_test'  
FOR LOGON  
AS  
BEGIN  
IF ORIGINAL_LOGIN()= 'login_test' AND  
    (SELECT COUNT(*) FROM sys.dm_exec_sessions  
            WHERE is_user_process = 1 AND  
                original_login_name = 'login_test') > 3  
    ROLLBACK;  
END;  
  
```  
  
### <a name="g-viewing-the-events-that-cause-a-trigger-to-fire"></a>G. 檢視引發觸發程序的事件  
以下範例會查詢 `sys.triggers` 及 `sys.trigger_events` 目錄檢視，判別引發 `safety` 觸發程序的 [!INCLUDE[tsql](../../includes/tsql-md.md)] 語言事件。 觸發程序 `safety` 是在上面的範例 'D' 中所建立。  
  
```sql  
SELECT TE.*  
FROM sys.trigger_events AS TE  
JOIN sys.triggers AS T ON T.object_id = TE.object_id  
WHERE T.parent_class = 0 AND T.name = 'safety';  
GO  
```  

    

## <a name="see-also"></a>另請參閱  
 [ALTER TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-table-transact-sql.md)   
 [ALTER TRIGGER &#40;Transact-SQL&#41;](../../t-sql/statements/alter-trigger-transact-sql.md)   
 [COLUMNS_UPDATED &#40;Transact-SQL&#41;](../../t-sql/functions/columns-updated-transact-sql.md)   
 [CREATE TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/create-table-transact-sql.md)   
 [DROP TRIGGER &#40;Transact-SQL&#41;](../../t-sql/statements/drop-trigger-transact-sql.md)   
 [ENABLE TRIGGER &#40;Transact-SQL&#41;](../../t-sql/statements/enable-trigger-transact-sql.md)   
 [DISABLE TRIGGER &#40;Transact-SQL&#41;](../../t-sql/statements/disable-trigger-transact-sql.md)   
 [TRIGGER_NESTLEVEL &#40;Transact-SQL&#41;](../../t-sql/functions/trigger-nestlevel-transact-sql.md)   
 [EVENTDATA &#40;Transact-SQL&#41;](../../t-sql/functions/eventdata-transact-sql.md)   
 [sys.dm_sql_referenced_entities &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-sql-referenced-entities-transact-sql.md)   
 [sys.dm_sql_referencing_entities &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-sql-referencing-entities-transact-sql.md)   
 [sys.sql_expression_dependencies &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-sql-expression-dependencies-transact-sql.md)   
 [sp_help &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-help-transact-sql.md)   
 [sp_helptrigger &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-helptrigger-transact-sql.md)   
 [sp_helptext &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-helptext-transact-sql.md)   
 [sp_rename &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-rename-transact-sql.md)   
 [sp_settriggerorder &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-settriggerorder-transact-sql.md)   
 [UPDATE&#40;&#41; &#40;Transact-SQL&#41;](../../t-sql/functions/update-trigger-functions-transact-sql.md)   
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
  
  


