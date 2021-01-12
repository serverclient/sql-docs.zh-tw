---
description: ALTER EVENT SESSION (Transact-SQL)
title: ALTER EVENT SESSION (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 08/07/2017
ms.prod: sql
ms.prod_service: sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- ALTER EVENT SESSION
- ALTER_EVENT_SESSION_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- event sessions [SQL Server]
- extended events [SQL Server], Transact-SQL
- ALTER EVENT SESSION statement
ms.assetid: da006ac9-f914-4995-a2fb-25b5d971cd90
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: bd70edba2747e4f68046dcb98e4c886442097c0b
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98082829"
---
# <a name="alter-event-session-transact-sql"></a>ALTER EVENT SESSION (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  啟動或停止事件工作階段，或是變更事件工作階段組態。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
ALTER EVENT SESSION event_session_name  
ON SERVER  
{  
    [ [ {  <add_drop_event> [ ,...n] }     
       | { <add_drop_event_target> [ ,...n ] } ]   
    [ WITH ( <event_session_options> [ ,...n ] ) ]  
    ]  
    | [ STATE = { START | STOP } ]  
}  
  
<add_drop_event>::=  
{  
    [ ADD EVENT <event_specifier>   
         [ ( {   
                 [ SET { event_customizable_attribute = <value> [ ,...n ] } ]  
                 [ ACTION ( { [event_module_guid].event_package_name.action_name [ ,...n ] } ) ]  
                 [ WHERE <predicate_expression> ]  
        } ) ]  
   ]   
   | DROP EVENT <event_specifier> }  
  
<event_specifier> ::=  
{  
[event_module_guid].event_package_name.event_name  
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
  
<add_drop_event_target>::=  
{  
    ADD TARGET <event_target_specifier>  
        [ ( SET { target_parameter_name = <value> [ ,...n] } ) ]  
    | DROP TARGET <event_target_specifier>  
}  
  
<event_target_specifier>::=  
{  
    [event_module_guid].event_package_name.target_name  
}  
  
<event_session_options>::=  
{  
    [    MAX_MEMORY = size [ KB | MB] ]  
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
  
|詞彙|定義|  
|-|-|
|*event_session_name*|這是現有事件工作階段的名稱。|  
|STATE = START &#124; STOP|啟動或停止事件工作階段。 只有當 ALTER EVENT SESSION 套用到事件工作階段物件時，這個引數才有效。|  
|ADD EVENT \<event_specifier>|將以 \<event_specifier> 識別的事件與事件工作階段建立關聯。|
|[*event_module_guid*] *.event_package_name.event_name*|為事件封裝中的事件名稱，其中：<br /><br /> -   *event_module_guid* 是包含此事件之模組的 GUID。<br />-   *event_package_name* 是包含此動作物件的套件。<br />-   *event_name* 是事件物件。<br /><br /> 事件會出現在 sys.dm_xe_objects 檢視表中當做 object_type 'event'。|  
|SET { *event_customizable_attribute*= \<value> [ ,...*n*] }|指定事件的可自訂屬性。 可自訂的屬性會出現在 sys.dm_xe_object_columns 檢視中當作 column_type 'customizable ' 和 object_name = *event_name*。|  
|ACTION ( { [*event_module_guid*] *.event_package_name.action_name* [ **,** ...*n*] } )|要關聯至事件工作階段的動作，其中：<br /><br /> -   *event_module_guid* 是包含此事件之模組的 GUID。<br />-   *event_package_name* 是包含此動作物件的套件。<br />-   *action_name* 是動作物件。<br /><br /> 動作會出現在 sys.dm_xe_objects 檢視表中當做 object_type 'action'。|  
|WHERE \<predicate_expression>|指定用來判斷是否應該處理事件的述詞運算式。 如果 \<predicate_expression> 為 True，則工作階段的動作和目標會進一步處理此事件。 如果 \<predicate_expression> 為 False，則此事件會先由工作階段卸除，再由工作階段的動作和目標進行處理。 述詞運算式限制為 3000 個字元，這會限制字串引數。|
|*event_field_name*|這是識別述詞來源之事件欄位的名稱。|  
|[event_module_guid].event_package_name.predicate_source_name|為全域述詞來源的名稱，其中：<br /><br /> -   *event_module_guid* 是包含此事件之模組的 GUID。<br />-   *event_package_name* 是包含此述詞物件的套件。<br />-   *predicate_source_name* 會在 sys.dm_xe_objects 檢視中定義為 object_type 'pred_source'。|  
|[*event_module_guid*].*event_package_name*.*predicate_compare_name*|為要與事件產生關聯的述詞物件名稱，其中：<br /><br /> -   *event_module_guid* 是包含此事件之模組的 GUID。<br />-   *event_package_name* 是包含此述詞物件的套件。<br />-   *predicate_compare_name* 是在 sys.dm_xe_objects 檢視中定義為 object_type 'pred_compare' 的全域來源。|  
|DROP EVENT \<event_specifier>|卸除以 *\<event_specifier>* 識別的事件。 \<event_specifier> 在事件工作階段中必須是有效的。|  
|ADD TARGET \<event_target_specifier>|將以 \<event_target_specifier> 識別的目標與事件工作階段建立關聯。|
|[*event_module_guid*].*event_package_name*.*target_name*|此為事件工作階段中之目標的名稱，其中：<br /><br /> -   *event_module_guid* 是包含此事件之模組的 GUID。<br />-   *event_package_name* 是包含此動作物件的套件。<br />-   *target_name* 是動作。 動作會出現在 sys.dm_xe_objects 檢視表中當做 object_type 'target'。|  
|SET { *target_parameter_name*= \<value> [, ...*n*] }|設定目標參數。 目標參數會出現在 sys.dm_xe_object_columns 檢視中當作 column_type 'customizable ' 和 object_name = *target_name*。<br /><br /> **注意！！** 如果是使用信號緩衝區目標，建議您將 max_memory 目標參數設為 2048 KB，以避免 XML 輸出可能發生資料截斷。 如需不同目標類型的詳細資訊，請參閱 [SQL Server 擴充的事件目標](/previous-versions/sql/sql-server-2016/bb630339(v=sql.130))。|  
|DROP TARGET \<event_target_specifier>|卸除以 \<event_target_specifier> 識別的目標。 \<event_target_specifier> 在事件工作階段中必須是有效的。|  
|EVENT_RETENTION_MODE = { **ALLOW_SINGLE_EVENT_LOSS** &#124; ALLOW_MULTIPLE_EVENT_LOSS &#124; NO_EVENT_LOSS }|指定要用來處理事件遺失的事件保留模式。<br /><br /> **ALLOW_SINGLE_EVENT_LOSS**<br /> 事件可能會從工作階段遺失。 當所有事件緩衝區已滿時，只會卸除單一事件。 當事件緩衝區已滿時遺失單一事件會允許可接受的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 效能特性，同時也可讓處理之事件資料流中的資料遺失情形降至最低。<br /><br /> ALLOW_MULTIPLE_EVENT_LOSS<br /> 包含多個事件的完整事件緩衝區可能會從工作階段中遺失。 遺失的事件數目取決於配置給工作階段的記憶體大小、記憶體的分割及緩衝區中的事件大小。 當事件緩衝區被快速填滿時，這個選項對於伺服器效能的影響會降至最低，但是可能會從工作階段中遺失大量的事件。<br /><br /> NO_EVENT_LOSS<br /> 不允許事件遺失。 這個選項可確保將會保留所有引發的事件。 使用這個選項可強制引發事件的所有工作一直等候到事件緩衝區中有可用的空間為止。 這樣可能會在事件工作階段為使用中時偵測到效能問題。 當使用者連接在等候事件從緩衝區排清時，可能會停滯。|  
|MAX_DISPATCH_LATENCY = { *seconds* SECONDS &#124; **INFINITE** }|指定事件在分派給事件工作階段目標之前，將於記憶體內緩衝處理的時間量。 最小的延遲值是 1 秒鐘。 但是，可使用 0 來指定 INFINITE 延遲。 依預設，此值設定為 30 秒。<br /><br /> *seconds* SECONDS<br /> 將緩衝區排清到目標之前所需等候的時間 (以秒為單位)。 *seconds* 是整數。<br /><br /> **INFINITE**<br /> 只有當緩衝區已滿，或是當事件工作階段關閉時，才能將緩衝區排清到目標。<br /><br /> **注意！！** MAX_DISPATCH_LATENCY = 0 SECONDS 相當於 MAX_DISPATCH_LATENCY = INFINITE。|  
|MAX_EVENT_SIZE =*size* [ KB &#124; **MB** ]|指定事件可容許的大小上限。 MAX_EVENT_SIZE 只可設定成允許大於 MAX_MEMORY 的單一事件。如果將其設為小於 MAX_MEMORY，將會引發錯誤。 *size* 是整數值，可以是 KB 或 MB 值。 如果 *size* 是以 KB 來指定，允許的大小下限為 64 KB。 當設定 MAX_EVENT_SIZE 時，除了 MAX_MEMORY 之外，還會建立 *size* 的兩個緩衝區。 這表示用於事件緩衝處理的記憶體總量為 MAX_MEMORY + 2 * MAX_EVENT_SIZE。|  
|MEMORY_PARTITION_MODE = { **NONE** &#124; PER_NODE &#124; PER_CPU }|指定事件緩衝區的建立位置。<br /><br /> **NONE**<br /> [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體內會建立單一組緩衝區。<br /><br /> PER NODE - 為每個 NUMA 節點建立一組緩衝區。<br /><br /> PER CPU - 為每個 CPU 建立一組緩衝區。|  
|TRACK_CAUSALITY = { ON &#124; **OFF** }|指定是否要追蹤因果。 如果啟用的話，因果可允許不同伺服器連接上的相關事件彼此相互關聯。|  
|STARTUP_STATE = { ON &#124; **OFF** }|指定當 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 啟動時，是否要自動啟動這個事件工作階段。<br /><br /> 如果 STARTUP_STATE=ON，則只有當 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 停止並重新啟動之後，事件工作階段才會啟動。<br /><br /> ON = 事件工作階段會在啟動時啟動。<br /><br /> **OFF** = 事件工作階段不會在啟動時啟動。|  
  
## <a name="remarks"></a>備註  
 不可在同一個陳述式內同時使用 `ADD` 和 `DROP` 引數。  
  
## <a name="permissions"></a>權限  
 需要 `ALTER ANY EVENT SESSION` 權限。  
  
## <a name="examples"></a>範例  
 下列範例會啟動事件工作階段並取得某些即時工作階段統計資料，然後將兩個事件加入至現有工作階段。  
  
```sql  
-- Start the event session  
ALTER EVENT SESSION test_session ON SERVER  
STATE = start;  
GO  

-- Obtain live session statistics   
SELECT * FROM sys.dm_xe_sessions;  
SELECT * FROM sys.dm_xe_session_events;  
GO  
  
-- Add new events to the session  
ALTER EVENT SESSION test_session ON SERVER  
ADD EVENT sqlserver.database_transaction_begin,  
ADD EVENT sqlserver.database_transaction_end;  
GO  
```  
  
## <a name="see-also"></a>另請參閱  
 [CREATE EVENT SESSION &#40;Transact-SQL&#41;](../../t-sql/statements/create-event-session-transact-sql.md)   
 [DROP EVENT SESSION &#40;Transact-SQL&#41;](../../t-sql/statements/drop-event-session-transact-sql.md)   
 [SQL Server 擴充的事件目標](/previous-versions/sql/sql-server-2016/bb630339(v=sql.130))   
 [sys.server_event_sessions &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-server-event-sessions-transact-sql.md)   
 [sys.dm_xe_objects &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-xe-objects-transact-sql.md)   
 [sys.dm_xe_object_columns &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-xe-object-columns-transact-sql.md)  
  
