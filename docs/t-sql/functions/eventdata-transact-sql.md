---
description: EVENTDATA (Transact-SQL)
title: EVENTDATA (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- EVENTDATA
- fn_event_data
- EVENTDATA_TSQL
- fn_event_data_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- server instance event data [SQL Server]
- event notifications [SQL Server], event status
- events [SQL Server], status information
- EVENTDATA function
- status information [SQL Server], events
- DDL triggers, returning event data
ms.assetid: 03a80e63-6f37-4b49-bf13-dc35cfe46c44
author: cawrites
ms.author: chadam
ms.openlocfilehash: ab94e98f7a7a3dadacca109e64fa8c5cc2f5e1b6
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98093595"
---
# <a name="eventdata-transact-sql"></a>EVENTDATA (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

此函數會傳回伺服器或資料庫事件的相關資訊。 當事件通知引發，且指定的 Service Broker 收到結果時，會呼叫 `EVENTDATA`。 DDL 或登入觸發程序也支援在內部使用 `EVENTDATA`。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
EVENTDATA( )
```

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="remarks"></a>備註  
只有在 DDL 或登入觸發程序內直接參考時，`EVENTDATA` 才會傳回資料。 如果 `EVENTDATA` 是由其他常式所呼叫，它便會傳回 NULL，即使那些常式是由 DDL 或登入觸發程序所呼叫亦然。
  
`EVENTDATA` 傳回的資料在以下交易之後無效：

+ 明確呼叫 `EVENTDATA`
+ 隱含呼叫 `EVENTDATA`
+ 認可
+ 會回復  
  
> [!CAUTION]  
>  `EVENTDATA` 會傳回 XML 資料，這項資料會以 Unicode 的方式傳給用戶端，每個字元佔 2 個位元組。 `EVENTDATA` 會傳回可代表這些 Unicode 字碼指標的 XML：  
>   
>  `0x0009`  
>   
>  `0x000A`  
>   
>  `0x000D`  
>   
>  `>= 0x0020 && <= 0xD7FF`  
>   
>  `>= 0xE000 && <= 0xFFFD`  
>   
>  XML 無法表示且不允許使用 [!INCLUDE[tsql](../../includes/tsql-md.md)] 識別碼和資料中所能使用的某些字元。 有上述清單所未顯示之字碼指標的字元或資料會對應至問號 (?)。  
  
執行 `CREATE LOGIN` 或 `ALTER LOGIN` 陳述式時，不會顯示密碼。 這可以保護登入安全性。  
  
## <a name="schemas-returned"></a>傳回的結構描述  
EVENTDATA 會傳回 **xml** 資料類型的值。 根據預設，所有事件的結構描述定義都會安裝在此目錄中：[!INCLUDE[ssInstallPath](../../includes/ssinstallpath-md.md)]Tools\Binn\schemas\sqlserver\2006\11\events\events.xsd。  
  
[Microsoft SQL Server XML Schemas](https://go.microsoft.com/fwlink/?LinkID=31850) (Microsoft SQL Server XML 結構描述) 網頁也會有事件結構描述。  
  
若要針對任何特定的事件擷取結構描述，請搜尋複雜類型 `EVENT_INSTANCE_<event_type>` 的結構描述。 例如，若要擷取 `DROP_TABLE` 事件的結構描述，請搜尋 `EVENT_INSTANCE_DROP_TABLE` 的結構描述。  
  
## <a name="examples"></a>範例  
  
### <a name="a-querying-event-data-in-a-ddl-trigger"></a>A. 查詢 DDL 觸發程序中的事件資料  
此範例會建立可防止建立新資料庫資料表的 DDL 觸發程序。 針對 `EVENTDATA` 所產生的 XML 資料使用 XQuery ，可擷取引發觸發程序的 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式。 如需詳細資訊，請參閱 [XQuery 語言參考 &#40;SQL Server&#41;](../../xquery/xquery-language-reference-sql-server.md)。  
  
> [!NOTE]  
>  當您使用 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 中的 [以方格顯示結果] 來查詢 `<TSQLCommand>` 元素時，命令文字中不會出現分行符號。 請改用 [Results to Text] (以文字顯示結果)。  
  
```sql  
USE AdventureWorks2012;  
GO  
CREATE TRIGGER safety   
ON DATABASE   
FOR CREATE_TABLE   
AS   
    PRINT 'CREATE TABLE Issued.'  
    SELECT EVENTDATA().value  
        ('(/EVENT_INSTANCE/TSQLCommand/CommandText)[1]','nvarchar(max)')  
   RAISERROR ('New tables cannot be created in this database.', 16, 1)   
   ROLLBACK  
;  
GO  
--Test the trigger.  
CREATE TABLE NewTable (Column1 INT);  
GO  
--Drop the trigger.  
DROP TRIGGER safety  
ON DATABASE;  
GO  
```  
  
> [!NOTE]  
>  若要傳回事件資料，請使用 XQuery **value()** 方法，不要使用 **query()** 方法。 **query()** 方法會在輸出中傳回 XML 和逸出 & 符號的歸位字元及換行字元 (CR/LF) 執行個體，**value()** 方法則會在輸出中將 CR/LF 轉譯為不可見。  
  
### <a name="b-creating-a-log-table-with-event-data-in-a-ddl-trigger"></a>B. 利用 DDL 觸發程序中的事件資料來建立記錄資料表  
此範例會建立一份資料表來儲存和所有資料庫層級事件有關的資訊，且會以 DDL 觸發程序來擴展該資料表。 針對 `EVENTDATA` 所產生的 XML 資料使用 XQuery ，可擷取事件類型和 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式。  
  
```sql 
USE AdventureWorks2012;  
GO  
CREATE TABLE ddl_log (PostTime DATETIME, DB_User NVARCHAR(100), Event NVARCHAR(100), TSQL NVARCHAR(2000));  
GO  
CREATE TRIGGER log   
ON DATABASE   
FOR DDL_DATABASE_LEVEL_EVENTS   
AS  
DECLARE @data XML  
SET @data = EVENTDATA()  
INSERT ddl_log   
   (PostTime, DB_User, Event, TSQL)   
   VALUES   
   (GETDATE(),   
   CONVERT(NVARCHAR(100), CURRENT_USER),   
   @data.value('(/EVENT_INSTANCE/EventType)[1]', 'NVARCHAR(100)'),   
   @data.value('(/EVENT_INSTANCE/TSQLCommand)[1]', 'NVARCHAR(2000)') ) ;  
GO  
--Test the trigger.  
CREATE TABLE TestTable (a INT);  
DROP TABLE TestTable ;  
GO  
SELECT * FROM ddl_log ;  
GO  
--Drop the trigger.  
DROP TRIGGER log  
ON DATABASE;  
GO  
--Drop table ddl_log.  
DROP TABLE ddl_log;  
GO  
```  
  
## <a name="see-also"></a>另請參閱  
 [使用 EVENTDATA 函式](../../relational-databases/triggers/use-the-eventdata-function.md)   
 [DDL 觸發程序](../../relational-databases/triggers/ddl-triggers.md)   
 [事件通知](../../relational-databases/service-broker/event-notifications.md)   
 [登入觸發程序](../../relational-databases/triggers/logon-triggers.md)  
  
  
