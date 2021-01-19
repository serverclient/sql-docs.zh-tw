---
description: DBCC FREEPROCCACHE (Transact-SQL)
title: DBCC FREEPROCCACHE (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 10/13/2017
ms.prod: sql
ms.prod_service: sql-data-warehouse, pdw, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- FREEPROCCACHE_TSQL
- FREEPROCCACHE
- DBCC_FREEPROCCACHE_TSQL
- DBCC FREEPROCCACHE
dev_langs:
- TSQL
helpviewer_keywords:
- freeing procedure cache
- removing procedure cache elements
- deleting procedure cache elements
- DBCC FREEPROCCACHE statement
- procedure cache [SQL Server]
- clearing procedure cache
ms.assetid: 0e09d210-6f23-4129-aedb-3d56b2980683
author: pmasl
ms.author: umajay
monikerRange: '>=aps-pdw-2016||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 635cbfe8b21f60bf2bb9cec49efac62c627a6ae4
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98172620"
---
# <a name="dbcc-freeproccache-transact-sql"></a>DBCC FREEPROCCACHE (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

從計畫快取移除所有元素；指定計畫控制代碼或 SQL 控制代碼，從計畫快取移除特定的計畫；或是移除與指定的資源集區相關聯的所有快取項目。

> [!NOTE]
> DBCC FREEPROCCACHE 不會清除原生編譯預存程序的執行統計資料。 程序快取沒有包含原生編譯預存程序的相關資訊。 收集自程序執行的任何執行統計資料，都將顯示於執行統計資料 DMV：[sys.dm_exec_procedure_stats &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-procedure-stats-transact-sql.md) 與 [sys.dm_exec_query_plan &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-query-plan-transact-sql.md)。  
  
![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)
  
## <a name="syntax"></a>語法  
[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的語法：

```sql
DBCC FREEPROCCACHE [ ( { plan_handle | sql_handle | pool_name } ) ] [ WITH NO_INFOMSGS ]  
```  

[!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] 和 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] 的語法：
  
```sql
DBCC FREEPROCCACHE [ ( COMPUTE | ALL ) ] 
     [ WITH NO_INFOMSGS ]   
[;]  
```  

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 ( { *plan_handle* | *sql_handle* | *pool_name* } )  
*plan_handle* 會唯一識別批次的查詢計劃，該批次已經執行，且其計劃位於計畫快取中。 *plan_handle* 為 **varbinary(64)**，並可從下列動態管理物件中取得：  
 -   [sys.dm_exec_cached_plans](../../relational-databases/system-dynamic-management-views/sys-dm-exec-cached-plans-transact-sql.md)  
 -   [sys.dm_exec_requests](../../relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql.md)  
 -   [sys.dm_exec_query_memory_grants](../../relational-databases/system-dynamic-management-views/sys-dm-exec-query-memory-grants-transact-sql.md)  
 -   [sys.dm_exec_query_stats](../../relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql.md)  

*sql_handle* 是要清除之批次的 SQL 控制代碼。 *sql_handle* 為 **varbinary(64)**，並可從下列動態管理物件中取得：  
 -   [sys.dm_exec_query_stats](../../relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql.md)  
 -   [sys.dm_exec_requests](../../relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql.md)  
 -   [sys.dm_exec_cursors](../../relational-databases/system-dynamic-management-views/sys-dm-exec-cursors-transact-sql.md)  
 -   [sys.dm_exec_xml_handles](../../relational-databases/system-dynamic-management-views/sys-dm-exec-xml-handles-transact-sql.md)  
 -   [sys.dm_exec_query_memory_grants](../../relational-databases/system-dynamic-management-views/sys-dm-exec-query-memory-grants-transact-sql.md)  

*pool_name* 是 Resource Governor 資源集區的名稱。 *pool_name* 是 **sysname**，並可以藉由查詢 [sys.dm_resource_governor_resource_pools](../../relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-transact-sql.md) 動態管理檢視來取得。  
 若要將 Resource Governor 工作負載群組與資源集區產生關聯，請查詢 [sys.dm_resource_governor_workload_groups](../../relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-transact-sql.md) 動態管理檢視。 如需工作階段之工作負載群組的相關資訊，請查詢 [sys.dm_exec_sessions](../../relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql.md) 動態管理檢視。  

  
 WITH NO_INFOMSGS  
 隱藏所有參考訊息。  
  
 COMPUTE  
 清除每個計算節點中的查詢計劃快取。 這是預設值。  
  
 ALL  
 清除每個計算節點和控制節點中的查詢計劃快取。  

> [!NOTE]
> 從 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 開始，`ALTER DATABASE SCOPED CONFIGURATION CLEAR PROCEDURE_CACHE` 可清除範圍中之資料庫的程序 (計畫) 快取。

## <a name="remarks"></a>備註  
請利用 DBCC FREEPROCCACHE 小心清除計畫快取。 清除程序 (計畫) 快取會使所有計畫被收回，且傳入的查詢執行會編譯新的計畫，而非重複使用任何先前的快取計畫。 

當新的編譯數目增加時，可能會造成查詢效能突然暫時降低。 針對計劃快取中每個清除的快取存放區，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 錯誤記錄會包含下列資訊訊息：

> `SQL Server has encountered %d occurrence(s) of cachestore flush for the '%s' cachestore (part of plan cache) due to 'DBCC FREEPROCCACHE' or 'DBCC FREESYSTEMCACHE' operations.` 

只要在該時間間隔內快取發生排清，這個訊息就會每五分鐘記錄一次。

下列重新設定作業也會清除程序快取：
-   存取檢查快取 Bucket 計數  
-   存取檢查快取配額  
-   clr enabled  
-   平行處理原則的成本臨界值  
-   cross db ownership chaining  
-   索引建立記憶體  
-   平行處理原則的最大程度  
-   max server memory  
-   max text repl size  
-   最大工作者執行緒  
-   min memory per query  
-   min server memory  
-   查詢管理者成本限制  
-   查詢等候  
-   remote query timeout  
-   user options  
  
## <a name="result-sets"></a>結果集  
未指定 WITH NO_INFOMSGS 子句時，DBCC FREEPROCCACHE 會傳回：「DBCC 的執行已經完成。 如果 DBCC 印出錯誤訊息，請連絡您的系統管理員」。
  
## <a name="permissions"></a>權限  
適用於：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]、[!INCLUDE[ssPDW](../../includes/sspdw-md.md)] 
- 需要伺服器的 ALTER SERVER STATE 權限。  

適用於：[!INCLUDE[ssSDW](../../includes/sssdw-md.md)]
- 需要 DB_OWNER 固定資料庫角色中的成員資格。  

## <a name="general-remarks-for-sssdw-and-sspdw"></a>[!INCLUDE[ssSDW](../../includes/sssdw-md.md)] 與 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] 的一般備註  
可同時執行多個 DBCC FREEPROCCACHE 命令。
在 [!INCLUDE[ssSDW](../../includes/sssdw-md.md)] 或 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] 中，當傳入的查詢編譯新計畫，而不是重複使用任何先前快取的計畫時，清除計畫快取可能會導致查詢效能暫時降低。 

DBCC FREEPROCCACHE (計算) 只會在於計算節點上執行時，使 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 重新編譯查詢。 它不會使 [!INCLUDE[ssSDW](../../includes/sssdw-md.md)] 或 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] 重新編譯在控制節點上產生的平行處理查詢計劃。
DBCC FREEPROCCACHE 可以在執行期間取消。
  
## <a name="limitations-and-restrictions-for-sssdw-and-sspdw"></a>[!INCLUDE[ssSDW](../../includes/sssdw-md.md)] 和 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] 的限制事項  
DBCC FREEPROCCACHE 不可以在交易內執行。
不支援在 EXPLAIN 陳述式中執行 DBCC FREEPROCCACHE。
  
## <a name="metadata-for-sssdw-and-sspdw"></a>[!INCLUDE[ssSDW](../../includes/sssdw-md.md)] 和 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] 的中繼資料  
執行 DBCC FREEPROCCACHE 時，新的資料列會加入至 sys.pdw_exec_requests 系統檢視。

## <a name="examples-ssnoversion"></a>範例：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]  
  
### <a name="a-clearing-a-query-plan-from-the-plan-cache"></a>A. 從計畫快取清除查詢計劃  
下列範例會從計畫快取中指定查詢計劃控制代碼，藉以清除查詢計劃。 為確保範例查詢位於計畫快取中，會先執行查詢。 系統會查詢 `sys.dm_exec_cached_plans` 和 `sys.dm_exec_sql_text` 動態管理檢視以傳回查詢的計劃控制代碼。 

然後會將結果集中的計畫控制代碼值插入到 `DBCC FREEPROCACHE` 陳述式中，就可以從計畫快取中僅移除該計畫。
  
```sql  
USE AdventureWorks2012;  
GO  
SELECT * FROM Person.Address;  
GO  
SELECT plan_handle, st.text  
FROM sys.dm_exec_cached_plans   
CROSS APPLY sys.dm_exec_sql_text(plan_handle) AS st  
WHERE text LIKE N'SELECT * FROM Person.Address%';  
GO  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]

```
plan_handle                                         text  
--------------------------------------------------  -----------------------------  
0x060006001ECA270EC0215D05000000000000000000000000  SELECT * FROM Person.Address;  
  
(1 row(s) affected)
 ```
 
```sql  
-- Remove the specific plan from the cache.  
DBCC FREEPROCCACHE (0x060006001ECA270EC0215D05000000000000000000000000);  
GO  
```  
  
### <a name="b-clearing-all-plans-from-the-plan-cache"></a>B. 從計畫快取清除所有計畫  
下列範例會從計畫快取中清除所有元素。 系統會指定 WITH `NO_INFOMSGS` 子句以防止資訊訊息顯示出來。
  
```sql  
DBCC FREEPROCCACHE WITH NO_INFOMSGS;  
```  
  
### <a name="c-clearing-all-cache-entries-associated-with-a-resource-pool"></a>C. 清除與資源集區相關聯的所有快取項目  
下列範例會清除與指定之資源集區有關聯的所有快取項目。 系統會先查詢 `sys.dm_resource_governor_resource_pools` 檢視，以取得 *pool_name* 的值。
  
```sql  
SELECT * FROM sys.dm_resource_governor_resource_pools;  
GO  
DBCC FREEPROCCACHE ('default');  
GO  
```  
  
## <a name="examples-sssdw-and-sspdw"></a>範例：[!INCLUDE[ssSDW](../../includes/sssdw-md.md)] 和 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
### <a name="d-dbcc-freeproccache-basic-syntax-examples"></a>D. DBCC FREEPROCCACHE 基本語法範例  
下列範例會移除計算節點中所有現有的查詢計劃快取。 雖然內容是設定為 UserDbSales，但系統會移除所有資料庫的計算節點查詢計劃。 WITH NO_INFOMSGS 子句可防止結果中出現資訊訊息。  
  
```sql
USE UserDbSales;  
DBCC FREEPROCCACHE (COMPUTE) WITH NO_INFOMSGS;
```  
  
 下列範例和前一個範例的結果相同，不同的是資訊訊息會出現在結果中。  
  
```sql
USE UserDbSales;  
DBCC FREEPROCCACHE (COMPUTE);  
```  
  
當要求資訊訊息且執行成功時，查詢結果針對每個計算節點都會顯示一行。
  
### <a name="e-granting-permission-to-run-dbcc-freeproccache"></a>E. 授與執行 DBCC FREEPROCCACHE 的權限  
下列範例提供登入帳戶 David 執行 DBCC FREEPROCCACHE 的權限。  
  
```sql
GRANT ALTER SERVER STATE TO David; 
GO
```  
  
## <a name="see-also"></a>另請參閱  
[DBCC &#40;Transact-SQL&#41;](../../t-sql/database-console-commands/dbcc-transact-sql.md)  
[資源管理員](../../relational-databases/resource-governor/resource-governor.md)  
[ALTER DATABASE SCOPED CONFIGURATION &#40;Transact-SQL&#41;](../../t-sql/statements/alter-database-scoped-configuration-transact-sql.md)
  
  
