---
description: sys.dm_exec_plan_attributes (Transact-SQL)
title: sys.dm_exec_plan_attributes (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 10/20/2017
ms.prod: sql
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.dm_exec_plan_attributes_TSQL
- dm_exec_plan_attributes_TSQL
- dm_exec_plan_attributes
- sys.dm_exec_plan_attributes
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_exec_plan_attributes dynamic management function
ms.assetid: dacf3ab3-f214-482e-aab5-0dab9f0a3648
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 7fa0b60cc1c172ea8777286fda425c7714d6b363
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98096590"
---
# <a name="sysdm_exec_plan_attributes-transact-sql"></a>sys.dm_exec_plan_attributes (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  針對計畫控制代碼所指定計畫的每個計畫屬性，各傳回一個資料列。 您可以使用這個資料表值函式取得特定計畫的詳細資料，例如快取索引鍵值或目前同時執行計畫數目。  
  
> [!NOTE]  
>  透過這個函數傳回的部分資訊會對應至 [sys.syscacheobjects](../../relational-databases/system-compatibility-views/sys-syscacheobjects-transact-sql.md) 回溯相容性檢視。

## <a name="syntax"></a>語法  
```  
sys.dm_exec_plan_attributes ( plan_handle )  
```  
  
## <a name="arguments"></a>引數  
 *plan_handle*  
  用來唯一識別批次的查詢計畫，該批次已經執行且其計畫在計畫快取中。 *plan_handle* 是 **Varbinary (64)**。 您可以從 [sys.dm_exec_cached_plans](../../relational-databases/system-dynamic-management-views/sys-dm-exec-cached-plans-transact-sql.md) 動態管理檢視取得計畫控制碼。  
  
## <a name="table-returned"></a>傳回的資料表  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|屬性|**varchar(128)**|與這份計畫相關聯的屬性名稱。 下方的資料表會列出可能的屬性、其資料類型，以及其描述。|  
|value|**sql_variant**|與這份計畫相關聯的屬性值。|  
|is_cache_key|**bit**|指出屬性是否作為計畫快取查閱金鑰的一部分使用。|  

從上表中， **屬性** 可以具有下列值：

|屬性|資料類型|描述|  
|---------------|---------------|-----------------|  
|set_options|**int**|指出編譯計畫所用的選項值。|  
|objectid|**int**|用來查閱快取中物件的主要索引鍵之一。 這是資料庫物件的 [sys. 物件](../../relational-databases/system-catalog-views/sys-objects-transact-sql.md) 中儲存的物件識別碼 (程式、視圖、觸發程式等) 。 若計畫屬於「特定」或「準備」類型，這是批次文字的內部雜湊。|  
|dbid|**int**|這是包含此計畫參考之實體的資料庫識別碼。<br /><br /> 若為特定或準備計畫，這是執行批次的來源資料庫識別碼。|  
|dbid_execute|**int**|針對儲存在 **Resource** 資料庫中的系統物件，這是執行快取計畫的源資料庫識別碼。 在所有其他狀況下，就會是 0。|  
|user_id|**int**|-2 值表示提交的批次不會隨著隱含的名稱解析而不同，不同的使用者可以共用這些批次。 這是慣用的方法。 任何其他值都代表在資料庫中提交查詢之使用者的使用者識別碼。| 
|language_id|**smallint**|建立快取物件之連接的語言識別碼。 如需詳細資訊，請參閱 [ &#40;transact-sql&#41;的sys.sys語言 ](../../relational-databases/system-compatibility-views/sys-syslanguages-transact-sql.md)。|  
|date_format|**smallint**|建立快取物件之連接的日期格式。 如需詳細資訊，請參閱 [SET DATEFIRST &#40;Transact-SQL&#41;](../../t-sql/statements/set-dateformat-transact-sql.md)。|  
|date_first|**tinyint**|先顯示日期的值。 如需詳細資訊，請參閱 [SET DATEFIRST &#40;Transact-SQL&#41;](../../t-sql/statements/set-datefirst-transact-sql.md)。|  
|status|**int**|屬於快取查閱索引鍵一部分的內部狀態位元。|  
|required_cursor_options|**int**|使用者指定的資料指標選項，例如資料指標類型。|  
|acceptable_cursor_options|**int**|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 可能會隱含轉換的目標資料指標選項，以支援執行陳述式。 例如，使用者可能指定動態資料指標，但查詢最佳化工具可以將這種資料指標類型轉換成靜態資料指標。|  
|inuse_exec_context|**int**|目前正在執行使用查詢計畫的批次數目。|  
|free_exec_context|**int**|目前未使用的查詢計劃快取執行內容數目。|  
|hits_exec_context|**int**|從計畫快取取得並重複使用執行內容的次數，這可減少重新編譯 SQL 陳述式的負擔。 這是到目前為止所有批次執行的總值。|  
|misses_exec_context|**int**|在計畫快取中找不到執行內容的次數，這時就必須建立新執行內容以執行批次。|  
|removed_exec_context|**int**|因快取計畫造成記憶體不足而移除的執行內容數目。|  
|inuse_cursors|**int**|目前執行的批次數目，這些批次包含一或多個使用快取計畫的資料指標。|  
|free_cursors|**int**|快取計畫的閒置或可用資料指標數目。|  
|hits_cursors|**int**|從快取計畫取得並重複使用非使用中資料指標的次數。 這是到目前為止所有批次執行的總值。|  
|misses_cursors|**int**|在快取中找不到非使用中資料指標的次數。|  
|removed_cursors|**int**|因快取計畫造成記憶體不足而移除的資料指標數目。|  
|sql_handle|**Varbinary** (64) |批次的 SQL 控制代碼。|  
|merge_action_type|**smallint**|當做 MERGE 陳述式結果使用之觸發程序執行計畫的類型。<br /><br /> 0 表示非觸發程序計畫、不會當做 MERGE 陳述式結果執行的觸發程序計畫，或是當做 MERGE 陳述式結果執行的觸發程序計畫 (此陳述式只會指定 DELETE 動作)。<br /><br /> 1 表示會當做 MERGE 陳述式結果執行的 INSERT 觸發程序計畫。<br /><br /> 2 表示會當做 MERGE 陳述式結果執行的 UPDATE 觸發程序計畫。<br /><br /> 3 表示會當做包含對應 INSERT 或 UPDATE 動作之 MERGE 陳述式結果執行的 DELETE 觸發程序計畫。<br /><br /> 如果是依據串聯式動作執行的巢狀觸發程序，這個值會是造成串聯之 MERGE 陳述式的動作。|  
  
## <a name="permissions"></a>權限  

在上 [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] ，需要 `VIEW SERVER STATE` 許可權。   
在 SQL Database Basic、S0 和 S1 服務目標上，以及針對彈性集區中的資料庫，則 `Server admin` `Azure Active Directory admin` 需要或帳戶。 在所有其他 SQL Database 服務目標上， `VIEW DATABASE STATE` 資料庫中都需要有許可權。   

## <a name="remarks"></a>備註  
  
## <a name="set-options"></a>Set 選項  
 相同已編譯計畫的副本可能只有 **set_options** 資料行中的值不同。 這表示不同的連接會使用不同組的 SET 選項來執行相同的查詢。 一般而言，最好不要使用不同組的選項，因為這可能會導致額外編譯、減少重複使用計畫，而且會因快取中產生多個計畫副本而使計畫快取變大。  
  
### <a name="evaluating-set-options"></a>評估 Set 選項  
 若要將 **set_options** 傳回的值轉譯成已編譯計畫的選項，請從 **set_options** 值減去值（從最大的可能值開始），直到到達0為止。 每個減掉的值即為查詢計劃中使用的選項。 例如，如果 **set_options** 中的值為251，則以編譯計畫的選項是 ANSI_Null_DFLT_ON (128) 、QUOTED_IDENTIFIER (64) 、ANSI_NullS (32) 、ANSI_WARNINGS (16) 、CONCAT_Null_YIELDS_Null (8) 、平行計畫 (2) 和 ANSI_PADDING (1) 。  
  
|選項|值|  
|------------|-----------|  
|ANSI_PADDING|1|  
|ParallelPlan<br /><br /> 指出計畫平行處理原則選項已變更。|2|  
|FORCEPLAN|4|  
|CONCAT_NULL_YIELDS_NULL|8|  
|ANSI_WARNINGS|16|  
|ANSI_NULLS|32|  
|QUOTED_IDENTIFIER|64|  
|ANSI_NULL_DFLT_ON|128|  
|ANSI_NULL_DFLT_OFF|256|  
|NoBrowseTable<br /><br /> 表示計畫不使用工作資料表來實作 FOR BROWSE 作業。|512|  
|TriggerOneRow<br /><br /> 表示包含單一資料列最佳化的計畫，以供 AFTER 觸發程序差異資料表使用。|1024|  
|ResyncQuery<br /><br /> 表示查詢是由內部系統預存程序提交。|2048|  
|ARITH_ABORT|4096|  
|NUMERIC_ROUNDABORT|8192|  
|DATEFIRST|16384|  
|DATEFORMAT|32768|  
|LanguageID|65536|  
|UPON<br /><br /> 表示當編譯計畫時，PARAMETERIZATION 資料庫選項設為 FORCED。|131072|  
|ROWCOUNT|**適用于：** [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 自 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)]<br /><br /> 262144|  
  
## <a name="cursors"></a>游標  
 非使用中的資料指標會快取在編譯的計畫中，讓用來儲存資料指標的記憶體可供資料指標的並行使用者重複使用。 例如，假設有個批次宣告並使用資料指標，但沒有取消配置。 如果有兩個使用者執行相同的批次，會有兩個使用中的資料指標。 一旦資料指標取消配置 (可能在不同的批次中)，用來儲存資料指標的記憶體就會快取，而不會釋出。 非使用中資料指標的清單會保留在編譯的計畫中。 等到下次使用者執行批次時，會重複使用快取的資料指標記憶體，並適當地初始化為使用中資料指標。  
  
### <a name="evaluating-cursor-options"></a>評估資料指標選項  
 若要轉譯 **required_cursor_options** 中傳回的值，並 **acceptable_cursor_options** 為已編譯計畫的選項，請從資料行值減去值（從最大的可能值開始），直到到達0為止。 每個減掉的值即為查詢計畫中使用的資料指標選項。  
  
|選項|值|  
|------------|-----------|  
|None|0|  
|INSENSITIVE|1|  
|SCROLL|2|  
|READ ONLY|4|  
|FOR UPDATE|8|  
|LOCAL|16|  
|GLOBAL|32|  
|FORWARD_ONLY|64|  
|KEYSET|128|  
|DYNAMIC|256|  
|SCROLL_LOCKS|512|  
|OPTIMISTIC|1024|  
|STATIC|2048|  
|FAST_FORWARD|4096|  
|IN PLACE|8192|  
|FOR *select_statement*|16384|  
  
## <a name="examples"></a>範例  
  
### <a name="a-returning-the-attributes-for-a-specific-plan"></a>A. 傳回特定計畫的屬性  
 下列範例會傳回指定計畫的所有計畫屬性。 首先會查詢 `sys.dm_exec_cached_plans` 動態管理檢視，取得指定計畫的計畫控制代碼。 在第二個查詢中，再用第一個查詢的計畫控制代碼值取代 `<plan_handle>`。  
  
```sql  
SELECT plan_handle, refcounts, usecounts, size_in_bytes, cacheobjtype, objtype   
FROM sys.dm_exec_cached_plans;  
GO  
SELECT attribute, value, is_cache_key  
FROM sys.dm_exec_plan_attributes(<plan_handle>);  
GO  
```  
  
### <a name="b-returning-the-set-options-for-compiled-plans-and-the-sql-handle-for-cached-plans"></a>B. 傳回編譯計畫的 SET 選項以及快取計畫的 SQL 控制代碼  
 下列範例會傳回值，代表編譯每個計畫所用的選項。 此外，也會傳回所有快取計畫的 SQL 控制代碼。  
  
```sql  
SELECT plan_handle, pvt.set_options, pvt.sql_handle  
FROM (  
    SELECT plan_handle, epa.attribute, epa.value   
    FROM sys.dm_exec_cached_plans   
        OUTER APPLY sys.dm_exec_plan_attributes(plan_handle) AS epa  
    WHERE cacheobjtype = 'Compiled Plan') AS ecpa   
PIVOT (MAX(ecpa.value) FOR ecpa.attribute IN ("set_options", "sql_handle")) AS pvt;  
GO  
```  
  
## <a name="see-also"></a>另請參閱  
 [動態管理檢視與函數 &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)   
 [執行相關的動態管理檢視和函數 &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/execution-related-dynamic-management-views-and-functions-transact-sql.md)   
 [sys.dm_exec_cached_plans &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-cached-plans-transact-sql.md)   
 [sys.databases &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md)   
 [sys.objects &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-objects-transact-sql.md)  
  
  

