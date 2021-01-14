---
description: 'sys.dm_exec_query_statistics_xml (Transact-sql) '
title: sys.dm_exec_query_statistics_xml (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 11/16/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: system-objects
ms.topic: conceptual
f1_keywords:
- sys.dm_exec_query_statistics_xml
- sys.dm_exec_query_statistics_xml_TSQL
- dm_exec_query_statistics_xml_TSQL
- dm_exec_query_statistics_xml
helpviewer_keywords:
- sys.dm_exec_query_statistics_xml management view
ms.assetid: fdc7659e-df41-488e-b2b5-0d79734dfecb
author: pmasl
ms.author: pelopes
ms.openlocfilehash: 9bfce6f56bdf39597def73884f12ca06f6eb56b7
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98170270"
---
# <a name="sysdm_exec_query_statistics_xml-transact-sql"></a>sys.dm_exec_query_statistics_xml (Transact-sql) 

[!INCLUDE [sqlserver2016-asdb-asdbmi](../../includes/applies-to-version/sqlserver2016-asdb-asdbmi.md)]

傳回進行中要求的查詢執行計畫。 您可以使用此 DMV 來取得具有暫時性統計資料的執行程式表 XML。 

## <a name="syntax"></a>語法

```
sys.dm_exec_query_statistics_xml(session_id)  
``` 

## <a name="arguments"></a>引數 
*session_id*  
 這是執行要查閱之批次的會話識別碼。 *session_id* 為 **Smallint**。 您可以從下列動態管理物件中取得 *session_id* ：  
  
-   [sys.dm_exec_requests](../../relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql.md)  
  
-   [sys.dm_exec_sessions](../../relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql.md)  
  
-   [sys.dm_exec_connections](../../relational-databases/system-dynamic-management-views/sys-dm-exec-connections-transact-sql.md)  

## <a name="table-returned"></a>傳回的資料表

|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|
|session_id|**smallint**|工作階段的識別碼。 不可為 Null。|
|request_id|**int**|要求的識別碼。 不可為 Null。|
|sql_handle|**varbinary(64)**|這是可唯一識別查詢所屬批次或預存程式的 token。 可為 Null。|
|plan_handle|**varbinary(64)**|這是一個 token，可唯一識別目前正在執行之批次的查詢執行計畫。 可為 Null。|
|query_plan|**xml**|包含查詢執行計畫的執行時間執行程式表表示，此計畫是以包含部分統計資料 *plan_handle* 指定的。 顯示計畫是 XML 格式。 每個包含諸如特定 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式、預存程序呼叫和使用者自訂函數呼叫的批次，都會產生一份計畫。 可為 Null。|

## <a name="remarks"></a>備註
從 SP1 開始提供此系統函數 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 。 請參閱 KB [3190871](https://support.microsoft.com/help/3190871)

這個系統函數可在 **標準** 和 **輕量** 查詢執行統計資料分析基礎結構下運作。 如需詳細資訊，請參閱[查詢分析基礎結構](../../relational-databases/performance/query-profiling-infrastructure.md)。  

在下列情況下， **sys.dm_exec_query_statistics_xml** 所傳回資料表的 **query_plan** 資料行中不會傳回任何顯示計畫輸出：  
  
-   如果對應至指定之 *session_id* 的查詢計劃已不再執行，則傳回資料表的 **query_plan** 資料行會是 null。 例如，如果在計畫控制碼的捕獲時間與 **sys.dm_exec_query_statistics_xml** 使用時之間有時間延遲，則可能會發生這種情況。  
    
由於 **xml** 資料類型所允許的嵌套層級數目有所限制， **sys.dm_exec_query_statistics_xml** 無法傳回符合或超過128層級之嵌套專案的查詢計劃。 在舊版 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中，這會讓查詢計畫無法傳回並產生錯誤 6335。 在 [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] Service Pack 2 和更新版本中， **query_plan** 資料行傳回 Null。   

## <a name="permissions"></a>權限  
在上 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ，需要 `VIEW SERVER STATE` 伺服器的許可權。  
在進階層中 [!INCLUDE[ssSDS](../../includes/sssds-md.md)] ，需要 `VIEW DATABASE STATE` 資料庫中的許可權。 在 [!INCLUDE[ssSDS](../../includes/sssds-md.md)] 標準和基本層中，需要 **伺服器管理員** 或 **Azure Active Directory 系統管理員** 帳戶。

## <a name="examples"></a>範例  
  
### <a name="a-looking-at-live-query-plan-and-execution-statistics-for-a-running-batch"></a>A. 查看正在執行之批次的即時查詢計劃和執行統計資料  
 下列範例會查詢 **sys.dm_exec_requests** ，以尋找感興趣的查詢，並 `session_id` 從輸出中複製它。  
  
```sql  
SELECT * FROM sys.dm_exec_requests;  
GO  
```  
  
 然後，若要取得即時查詢計劃和執行統計資料，請使用複製的 `session_id` with 系統函數 **sys.dm_exec_query_statistics_xml**。  
  
```sql  
--Run this in a different session than the session in which your query is running.
SELECT * FROM sys.dm_exec_query_statistics_xml(< copied session_id >);  
GO  
```   

 或為所有執行中的要求合併。  
  
```sql  
--Run this in a different session than the session in which your query is running.
SELECT * FROM sys.dm_exec_requests
CROSS APPLY sys.dm_exec_query_statistics_xml(session_id);  
GO  
```   
  
## <a name="see-also"></a>另請參閱
  [追蹤旗標](../../t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql.md)  
 [動態管理檢視與函數 &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)   
 [資料庫相關的動態管理檢視 &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/database-related-dynamic-management-views-transact-sql.md)  

