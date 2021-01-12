---
description: CREATE EVENT SESSION (Transact-SQL)
title: CREATE EVENT SESSION (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 07/10/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- CREATE EVENT SESSION
- SESSION
- EVENT SESSION
- SESSION_TSQL
- EVENT_SESSION_TSQL
- CREATE_EVENT_SESSION_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- event sessions [SQL Server]
- CREATE EVENT SESSION statement
ms.assetid: 67683027-2b0f-47aa-b223-604731af8b4d
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 006ef49a390045d5c6d7d8c8c174da90e37f4f91
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98100843"
---
# <a name="create-event-session-transact-sql"></a>CREATE EVENT SESSION (Transact-SQL)

[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

建立擴充事件工作階段，可識別事件的來源、事件工作階段目標及事件工作階段選項。

![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)。

## <a name="syntax"></a>語法

```syntaxsql
CREATE EVENT SESSION event_session_name
ON { SERVER | DATABASE }
{  
    <event_definition> [ ,...n]
    [ <event_target_definition> [ ,...n] ]
    [ WITH ( <event_session_options> [ ,...n] ) ]
}
;

<event_definition>::=
{
    ADD EVENT [event_module_guid].event_package_name.event_name
         [ ( {
                 [ SET { event_customizable_attribute = <value> [ ,...n] } ]
                 [ ACTION ( { [event_module_guid].event_package_name.action_name [ ,...n] } ) ]
                 [ WHERE <predicate_expression> ]
        } ) ]
}

<predicate_expression> ::=
{
    [ NOT ] <predicate_factor> | {( <predicate_expression> ) }
    [ { AND | OR } [ NOT ] { <predicate_factor> | ( <predicate_expression> ) } ]
    [ ,...n ]
}  
  
<predicate_factor>::=
{
    <predicate_leaf> | ( <predicate_expression> )
}

<predicate_leaf>::=
{
      <predicate_source_declaration> { = | < > | ! = | > | > = | < | < = } <value>
    | [event_module_guid].event_package_name.predicate_compare_name ( <predicate_source_declaration>, <value> )
}

<predicate_source_declaration>::=
{
    event_field_name | ( [event_module_guid].event_package_name.predicate_source_name )
}

<value>::=
{
    number | 'string'
}

<event_target_definition>::=
{
    ADD TARGET [event_module_guid].event_package_name.target_name
        [ ( SET { target_parameter_name = <value> [ ,...n] } ) ]
}

<event_session_options>::=
{  
    [    MAX_MEMORY = size [ KB | MB ] ]
    [ [,] EVENT_RETENTION_MODE = { ALLOW_SINGLE_EVENT_LOSS | ALLOW_MULTIPLE_EVENT_LOSS | NO_EVENT_LOSS } ]
    [ [,] MAX_DISPATCH_LATENCY = { seconds SECONDS | INFINITE } ]
    [ [,] MAX_EVENT_SIZE = size [ KB | MB ] ]
    [ [,] MEMORY_PARTITION_MODE = { NONE | PER_NODE | PER_CPU } ]
    [ [,] TRACK_CAUSALITY = { ON | OFF } ]
    [ [,] STARTUP_STATE = { ON | OFF } ]
}
```

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數

*event_session_name* 是使用者定義的事件工作階段名稱。 *event_session_name* 是英數字元，最多可有 128 個字元，而且在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的執行個體內必須是唯一的，並須符合 [識別碼](../../relational-databases/databases/database-identifiers.md)的規則。

ADD EVENT [ *event_module_guid* ].*event_package_name*.*event_name* 是要與事件工作階段產生關聯的事件，其中：

- *event_module_guid* 為包含此事件之模組的 GUID。
- *event_package_name* 是包含此動作物件的套件。
- *event_name* 是事件物件。

事件會出現在 sys.dm_xe_objects 檢視表中當做 object_type 'event'。

SET { *event_customizable_attribute*= \<value> [ ,...*n*] } 允許為此事件設定可自訂的屬性。 可自訂的屬性會出現在 sys.dm_xe_object_columns 檢視中當作 column_type 'customizable ' 和 object_name = *event_name*。

ACTION ( { [*event_module_guid*].*event_package_name*.*action_name* [ **,** ...*n*] }) 是要關聯至事件工作階段的動作，其中：

- *event_module_guid* 為包含此事件之模組的 GUID。
- *event_package_name* 是包含此動作物件的套件。
- *action_name* 是動作物件。

動作會出現在 sys.dm_xe_objects 檢視表中當做 object_type 'action'。

WHERE \<predicate_expression> 指定用來判斷是否應該處理事件的述詞運算式。 如果 \<predicate_expression> 為 True，則工作階段的動作和目標會進一步處理此事件。 如果 \<predicate_expression> 為 False，則此事件會先由工作階段卸除，再由工作階段的動作和目標進行處理。 述詞運算式限制為 3000 個字元，這會限制字串引數。

*event_field_name* 是能識別述詞來源之事件欄位的名稱。

[*event_module_guid*].*event_package_name*.*predicate_source_name* 是全域述詞來源的名稱，其中：

- *event_module_guid* 為包含此事件之模組的 GUID。
- *event_package_name* 是包含此述詞物件的套件。
- *predicate_source_name* 會在 sys.dm_xe_objects 檢視中定義為 object_type 'pred_source'。

[*event_module_guid*].*event_package_name*.*predicate_compare_name* 是要與事件產生關聯的述詞物件名稱，其中：

- *event_module_guid* 為包含此事件之模組的 GUID。
- *event_package_name* 是包含此述詞物件的套件。
- *predicate_compare_name* 是在 sys.dm_xe_objects 檢視中定義為 object_type 'pred_compare' 的全域來源。

*number* 是包含 **decimal** 的任何數值類型。 限制為缺少可用的實體記憶體，或是數字太大而不能表示為 64 位元整數。

'*string*' ANSI 或 Unicode 字串 (依述詞比較的需求而定)。 不會針對述詞比較函數執行隱含字串類型轉換。 傳遞錯誤的類型會產生錯誤。

ADD TARGET [*event_module_guid*].*event_package_name*.*target_name* 是要與事件工作階段產生關聯的目標，其中：

- *event_module_guid* 為包含此事件之模組的 GUID。
- *event_package_name* 是包含此動作物件的套件。
- *target_name* 是動作。 目標會出現在 sys.dm_xe_objects 檢視表中當做 object_type 'target'。

SET { *target_parameter_name*= \<value> [, ...*n*] } 設定目標參數。 目標參數會出現在 sys.dm_xe_object_columns 檢視中當作 column_type 'customizable ' 和 object_name = *target_name*。

> [!IMPORTANT]
> 如果是使用信號緩衝區目標，建議您將 max_memory 目標參數設為 2048 KB，以避免 XML 輸出可能發生資料截斷。 如需不同目標類型的詳細資訊，請參閱 [SQL Server 擴充的事件目標](/previous-versions/sql/sql-server-2016/bb630339(v=sql.130))。

WITH ( \<event_session_options> [ ,...*n*] ) 指定要搭配事件工作階段使用的選項。

MAX_MEMORY =*size* [ KB | **MB** ] 指定要針對事件緩衝處理配置給工作階段的最大記憶體數量。 預設值是 4 MB。 *size* 是整數值，可以是 KB 或 MB 值。 最大數量不得超過 2 GB (小於 2048 MB)。 不過，不建議使用以 GB 範圍為單位的記憶體值。

EVENT_RETENTION_MODE = { **ALLOW_SINGLE_EVENT_LOSS** | ALLOW_MULTIPLE_EVENT_LOSS | NO_EVENT_LOSS } 指定要用來處理事件遺失的事件保留模式。

**ALLOW_SINGLE_EVENT_LOSS** 事件可能會從工作階段遺失。 當所有事件緩衝區已滿時，只會卸除單一事件。 當事件緩衝區已滿時遺失單一事件會允許可接受的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 效能特性，同時也可讓處理之事件資料流中的資料遺失情形降至最低。

ALLOW_MULTIPLE_EVENT_LOSS 包含多個事件的完整事件緩衝區可能會從工作階段中遺失。 遺失的事件數目取決於配置給工作階段的記憶體大小、記憶體的分割及緩衝區中的事件大小。 當事件緩衝區被快速填滿時，這個選項對於伺服器效能的影響會降至最低，但是可能會從工作階段中遺失大量的事件。

NO_EVENT_LOSS 不允許事件遺失。 這個選項可確保將會保留所有引發的事件。 使用這個選項可強制引發事件的所有工作一直等候到事件緩衝區中有可用的空間為止。 這樣可能會在事件工作階段為使用中時偵測到效能問題。 當使用者連接在等候事件從緩衝區排清時，可能會停滯。

MAX_DISPATCH_LATENCY = { *seconds* SECONDS | **INFINITE** } 指定事件在分派給事件工作階段目標之前，將於記憶體內緩衝處理的時間量。 依預設，此值設定為 30 秒。

*seconds* SECONDS 將緩衝區排清到目標之前所需等候的時間 (以秒為單位)。 *seconds* 是整數。 最小的延遲值是 1 秒鐘。 但是，可使用 0 來指定 INFINITE 延遲。

**INFINITE** 只有當緩衝區已滿，或是當事件工作階段關閉時，才能將緩衝區排清到目標。

> [!NOTE]
> MAX_DISPATCH_LATENCY = 0 SECONDS 相當於 MAX_DISPATCH_LATENCY = INFINITE。

MAX_EVENT_SIZE =*size* [ KB | **MB** ] 指定事件可容許的大小上限。 MAX_EVENT_SIZE 只可設定成允許大於 MAX_MEMORY 的單一事件。如果將其設為小於 MAX_MEMORY，將會引發錯誤。 *size* 是整數值，可以是 KB 或 MB 值。 如果 *size* 是以 KB 來指定，允許的大小下限為 64 KB。 當設定 MAX_EVENT_SIZE 時，除了 MAX_MEMORY 之外，還會建立 *size* 的兩個緩衝區。 這表示用於事件緩衝處理的記憶體總量為 MAX_MEMORY + 2 * MAX_EVENT_SIZE。

MEMORY_PARTITION_MODE = { **NONE** | PER_NODE | PER_CPU } 指定事件緩衝區的建立位置。

**NONE**[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體內會建立單一組緩衝區。

PER_NODE 會針對每一個 NUMA 節點建立一組緩衝區。

PER_CPU 會針對每一個 CPU 建立一組緩衝區。

TRACK_CAUSALITY = { ON | **OFF** } 指定是否要追蹤原因。 如果啟用的話，因果可允許不同伺服器連接上的相關事件彼此相互關聯。

STARTUP_STATE = { ON | **OFF** } 指定當 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 啟動時，是否要自動啟動這個事件工作階段。

> [!NOTE]
> 如果 `STARTUP_STATE = ON`，則只有當 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 停止並重新啟動之後，事件工作階段才會啟動。

ON 事件工作階段會在啟動時啟動。

**OFF** 事件工作階段不會在啟動時啟動。

## <a name="remarks"></a>備註

邏輯運算子的優先順序是 `NOT` (最高)，後面依序接著 `AND` 和 `OR`。

## <a name="permissions"></a>權限

在 SQL Server 上，需要 `ALTER ANY EVENT SESSION` 權限。
在 SQL Database 上，資料庫需要 `ALTER ANY DATABASE EVENT SESSION` 權限。

## <a name="examples"></a>範例

### <a name="sql-server-example"></a>SQL Server 範例

以下範例將示範如何建立名為 `test_session` 的事件工作階段。 這個範例會加入兩個事件，並使用 Windows 事件追蹤目標。

```sql
IF EXISTS(SELECT * FROM sys.server_event_sessions WHERE name='test_session')
    DROP EVENT session test_session ON SERVER;
GO
CREATE EVENT SESSION test_session
ON SERVER
    ADD EVENT sqlos.async_io_requested,
    ADD EVENT sqlserver.lock_acquired
    ADD TARGET package0.etw_classic_sync_target
        (SET default_etw_session_logfile_path = N'C:\demo\traces\sqletw.etl' )
    WITH (MAX_MEMORY=4MB, MAX_EVENT_SIZE=4MB);
GO
```
### <a name="sql-database-example"></a>SQL Database 範例

如需 Azure SQL Database 範例，請參閱 [SQL Database 中擴充事件的事件檔案目標程式碼](/azure/sql-database/sql-database-xevent-code-event-file#transact-sql-code) \(部分機器翻譯\) 中的範例

### <a name="code-examples-can-differ-for-azure-sql-database"></a>適用於 Azure SQL Database 的程式碼範例可能有所不同

[!INCLUDE[sql-on-premises-vs-azure-similar-sys-views-include.](../../includes/paragraph-content/sql-on-premises-vs-azure-similar-sys-views-include.md)]

## <a name="see-also"></a>另請參閱

- [ALTER EVENT SESSION &#40;Transact-SQL&#41;](../../t-sql/statements/alter-event-session-transact-sql.md)
- [DROP EVENT SESSION &#40;Transact-SQL&#41;](../../t-sql/statements/drop-event-session-transact-sql.md)
- [sys.server_event_sessions &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-server-event-sessions-transact-sql.md)
- [sys.dm_xe_objects &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-xe-objects-transact-sql.md)
- [sys.dm_xe_object_columns &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-xe-object-columns-transact-sql.md)