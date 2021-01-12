---
description: CREATE QUEUE (Transact-SQL)
title: CREATE QUEUE (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 09/25/2019
ms.prod: sql
ms.prod_service: sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- QUEUE_TSQL
- CREATE QUEUE
- QUEUE
- CREATE_QUEUE_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- CREATE QUEUE statement
- internal activation [Service Broker]
- stored procedure activation [Service Broker]
- message retention [Service Broker]
- unavailable queues [Service Broker]
- activation stored procedures [Service Broker]
- queues [Service Broker], creating
ms.assetid: fce80faf-2bdc-475d-8ca1-31438ed41fb0
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: ce0306d204d4ce29a76e2041bebe67971e24c3a7
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98092014"
---
# <a name="create-queue-transact-sql"></a>CREATE QUEUE (Transact-SQL)

[!INCLUDE [SQL Server - ASDBMI](../../includes/applies-to-version/sql-asdbmi.md)]

在資料庫中建立新的佇列。 佇列會儲存訊息。 當服務的訊息到達時，[!INCLUDE[ssSB](../../includes/sssb-md.md)] 會將訊息放在與服務相關聯的佇列中。

![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)

## <a name="syntax"></a>語法

```syntaxsql
CREATE QUEUE <object>
   [ WITH
     [ STATUS = { ON | OFF } [ , ] ]
     [ RETENTION = { ON | OFF } [ , ] ]
     [ ACTIVATION (
         [ STATUS = { ON | OFF } , ]
           PROCEDURE_NAME = <procedure> ,
           MAX_QUEUE_READERS = max_readers ,
           EXECUTE AS { SELF | 'user_name' | OWNER }
            ) [ , ] ]
     [ POISON_MESSAGE_HANDLING (
         [ STATUS = { ON | OFF } ] ) ]
    ]
     [ ON { filegroup | [ DEFAULT ] } ]
[ ; ]

<object> ::=
{ database_name.schema_name.queue_name | schema_name.queue_name | queue_name }

<procedure> ::=
{ database_name.schema_name.stored_procedure_name | schema_name.stored_procedure_name | stored_procedure_name }

```

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數

*database_name* (物件) 是在其中建立新佇列的資料庫名稱。 *database_name* 必須指定現有資料庫的名稱。 未提供 *database_name* 時，會在目前的資料庫中建立佇列。

*schema_name* (物件) 是新佇列所屬的結構描述名稱。 結構描述預設為執行陳述式之使用者的預設結構描述。 如果 CREATE QUEUE 陳述式是由系統管理員 (sysadmin) 固定伺服器角色的成員，或由 *database_name* 指定之資料庫中的 db_dbowner 或 db_ddladmin 固定資料庫角色的成員來執行，*schema_name* 就可以指定另一個結構描述，而不是指定與目前連線之登入有關的結構描述。 否則，*schema_name* 就必須是執行陳述式之使用者的預設結構描述。

*queue_name* 是要建立的佇列名稱。 這個名稱必須符合 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 識別碼的方針。

STATUS (佇列) 會指定佇列是可以使用 (ON) 或無法使用 (OFF)。 當佇列無法使用時，不能將訊息加入佇列或從佇列中移除。 您可以建立狀態為無法使用的佇列，以便在利用 ALTER QUEUE 陳述式啟用這個佇列之前，防止訊息到達這個佇列。 如果省略這個子句，預設值便是 ON，佇列可以使用。

RETENTION 會指定佇列的保留設定。 如果 RETENTION = ON，使用這個佇列的交談所傳送或接收的所有訊息都會保留在佇列中，直到交談結束為止。 這個選項可讓您保留訊息來進行稽核，或在錯誤發生時用來執行補償交易。 如果未指定這個子句，保留設定的預設值便是 OFF。

> [!NOTE]
> 設定 RETENTION = ON 可降低效能。 您只應在應用程式有需要時，才使用這項設定。

ACTIVATION 會指定為了處理這個佇列中訊息而必須啟動的預存程序相關資訊。

STATUS (啟用) 會指定 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 是否啟動預存程序。 當 STATUS = ON 時，如果目前執行的程序數目低於 MAX_QUEUE_READERS，且訊息到達佇列的速度比預存程序接收訊息快，佇列便會啟動 PROCEDURE_NAME 所指定的預存程序。 當 STATUS = OFF 時，佇列不會啟動預存程序。 如果未指定這個子句，預設值便是 ON。

PROCEDURE_NAME = \<procedure> 指定為了處理這個佇列中訊息所要啟動的預存程序名稱。 這個值必須是 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 識別碼。

*database_name*(程序) 是包含預存程序的資料庫名稱。

*schema_name*(程序) 是包含預存程序的結構描述名稱。

*procedure_name* 是預存程序的名稱。

MAX_QUEUE_READERS =*max_readers* 會指定佇列同時啟動的啟用預存程序最大執行個體數目。 *max_readers* 的值必須是在 **0** 和 **32767** 之間的數字。

EXECUTE AS 會指定啟用預存程序執行所使用的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 資料庫使用者帳戶。 當佇列啟動預存程序時，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 必須能夠檢查這個使用者的權限。 如果是網域使用者，當此程序啟動時，伺服器必須連接這個網域，否則啟用會失敗。 如果是 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 使用者，伺服器一律可以檢查權限。

SELF 會指定以目前使用者的身分來執行預存程序。 (執行此 CREATE QUEUE 陳述式的資料庫主體)。

'*user_name*' 會指定用來執行預存程序的使用者名稱。 *user_name* 參數必須是指定為 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 識別碼的有效 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 使用者。 目前的使用者必須擁有指定之 *user_name* 的 IMPERSONATE 權限。

OWNER 會指定以佇列擁有者的身分來執行預存程序。

POISON_MESSAGE_HANDLING 會指定是否針對佇列啟用有害訊息處理。 預設值是 ON。

有害訊息處理設定為 OFF 的佇列將不會在五個連續的交易復原後停用。 這可讓應用程式定義自訂有害訊息處理系統。

ON *filegroup |* [**DEFAULT**] 會指定要在其上建立此佇列的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 檔案群組。 您可以利用 *filegroup* 參數來識別檔案群組，或利用 DEFAULT 識別碼來使用 Service Broker 資料庫的預設檔案群組。 在這個子句的內容中，DEFAULT 不是關鍵字，必須用識別碼來分隔。 當沒有指定任何檔案群組時，佇列會使用資料庫的預設檔案群組。

## <a name="remarks"></a>備註

佇列可以是 SELECT 陳述式的目標。 不過，您只能利用在 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 交談上運作的陳述式來修改佇列的內容，例如 SEND、RECEIVE 和 END CONVERSATION。 佇列不能是 INSERT、UPDATE、DELETE 或 TRUNCATE 陳述式的目標。

佇列不能是暫存物件。 因此，開頭是 **#** 的佇列名稱無效。

建立非使用中狀態的佇列，可讓您在允許佇列接受訊息之前，備妥服務的基礎結構。

當佇列中沒有訊息時，[!INCLUDE[ssSB](../../includes/sssb-md.md)] 不會停止啟用預存程序。 當佇列中沒有可用的訊息，過了一小段時間之後，啟用預存程序便應該結束。

當啟用預存程序被 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 啟動時，會檢查這個預存程序的權限，但在建立佇列時，並不會進行這項檢查。 CREATE QUEUE 陳述式並不會驗證 EXECUTE AS 子句所指定的使用者是否有執行 PROCEDURE NAME 子句所指定之預存程序的權限。

當佇列無法使用時，[!INCLUDE[ssSB](../../includes/sssb-md.md)] 會保存使用資料庫傳輸佇列中的佇列之服務的訊息。 sys.transmission_queue 目錄檢視會提供傳輸佇列的檢視。

佇列是結構描述所擁有的物件。 佇列會出現在 sys.objects 目錄檢視中。

下表列出佇列中的資料行。

|資料行名稱|資料類型|描述|
|-----------------|---------------|-----------------|
|status|**tinyint**|訊息狀態。 RECEIVE 陳述式會傳回 status 為 **1** 的所有訊息。 如果訊息保留開啟，則 status 會設定為 0。 如果訊息保留關閉，訊息會從佇列刪除。 佇列中的訊息可包含下列其中一個值：<br /><br /> **0**=已保留接收的訊息<br /><br /> **1**=準備好接收<br /><br /> **2**=未完成<br /><br /> **3**=已保留傳送的訊息|
|priority|**tinyint**|指派給這個訊息的優先權等級。|
|queuing_order|**bigint**|佇列中的訊息順序號碼。|
|conversation_group_id|**uniqueidentifier**|這則訊息所屬的交談群組識別碼。|
|conversation_handle|**uniqueidentifier**|這則訊息所屬的交談之控制代碼。|
|message_sequence_number|**bigint**|訊息在交談中的序號。|
|service_name|**nvarchar(512)**|交談的目標服務名稱。|
|service_id|**int**|交談目標服務的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 物件識別碼。|
|service_contract_name|**nvarchar(256)**|交談遵照的合約名稱。|
|service_contract_id|**int**|交談遵照的合約之 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 物件識別碼。|
|message_type_name|**nvarchar(256)**|描述訊息的訊息類型名稱。|
|message_type_id|**int**|描述訊息的訊息類型之 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 物件識別碼。|
|validation|**nchar(2)**|用於訊息的驗證。<br /><br /> E = 空的<br /><br /> N = 無<br /><br /> X = XML|
|message_body|**varbinary(max)**|訊息內容。|
|message_id|**uniqueidentifier**|訊息的唯一識別碼。|

## <a name="permissions"></a>權限

建立佇列的權限會使用 `db_ddladmin` 或 `db_owner` 固定資料庫角色的成員，或是 `sysadmin` 固定伺服器角色的成員。

佇列的 `REFERENCES` 權限預設為佇列的擁有者、`db_ddladmin` 或 `db_owner` 固定資料庫角色的成員，或是 `sysadmin` 固定伺服器角色的成員。

佇列的 `RECEIVE` 權限預設為佇列的擁有者、`db_owner` 固定資料庫角色的成員，或是 `sysadmin` 固定伺服器角色的成員。

## <a name="examples"></a>範例

### <a name="a-creating-a-queue-with-no-parameters"></a>A. 建立佇列，不使用任何參數

下列範例會建立可用來接收訊息的佇列。 未指定佇列的任何啟用預存程序。

```sql
CREATE QUEUE ExpenseQueue ;
```

### <a name="b-creating-an-unavailable-queue"></a>B. 建立無法使用的佇列

下列範例會建立無法用來接收訊息的佇列。 未指定佇列的任何啟用預存程序。

```sql
CREATE QUEUE ExpenseQueue WITH STATUS=OFF ;
```

### <a name="c-creating-a-queue-and-specify-internal-activation-information"></a>C. 建立佇列和指定內部啟用資訊

下列範例會建立可用來接收訊息的佇列。 當訊息進入佇列時，這個佇列會啟動 `expense_procedure` 預存程序。 這個預存程序以 `ExpenseUser` 使用者的身分來執行。 佇列會啟動最多 `5` 個預存程序執行個體。

```sql
CREATE QUEUE ExpenseQueue
    WITH STATUS=ON,
    ACTIVATION (
        PROCEDURE_NAME = expense_procedure
        , MAX_QUEUE_READERS = 5
        , EXECUTE AS 'ExpenseUser' ) ;
```

### <a name="d-creating-a-queue-on-a-specific-filegroup"></a>D. 在特定檔案群組上建立佇列

下列範例會在 `ExpenseWorkFileGroup` 檔案群組上建立佇列。

```sql
CREATE QUEUE ExpenseQueue
    ON ExpenseWorkFileGroup ;
```

### <a name="e-creating-a-queue-with-multiple-parameters"></a>E. 利用多個參數來建立佇列

下列範例會在 `DEFAULT` 檔案群組上建立佇列。 這個佇列無法使用。 訊息會保留在佇列中，直到它們所屬的交談結束為止。 當透過 ALTER QUEUE 使佇列成為可用時，佇列會啟動 `2008R2.dbo.expense_procedure` 預存程序來處理訊息。 這個預存程序是以執行 `CREATE QUEUE` 陳述式的使用者身分來執行。 佇列會啟動最多 `10` 個預存程序執行個體。

```sql
CREATE QUEUE ExpenseQueue
    WITH STATUS = OFF
      , RETENTION = ON
      , ACTIVATION (
          PROCEDURE_NAME = AdventureWorks2012.dbo.expense_procedure
          , MAX_QUEUE_READERS = 10
          , EXECUTE AS SELF )
    ON [DEFAULT];
```

## <a name="see-also"></a>另請參閱

- [ALTER QUEUE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-queue-transact-sql.md)
- [CREATE SERVICE &#40;Transact-SQL&#41;](../../t-sql/statements/create-service-transact-sql.md)
- [DROP QUEUE &#40;Transact-SQL&#41;](../../t-sql/statements/drop-queue-transact-sql.md)
- [RECEIVE &#40;Transact-SQL&#41;](../../t-sql/statements/receive-transact-sql.md)
- [EVENTDATA &#40;Transact-SQL&#41;](../../t-sql/functions/eventdata-transact-sql.md)
