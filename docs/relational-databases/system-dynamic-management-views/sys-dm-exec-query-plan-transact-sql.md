---
description: sys.dm_exec_query_plan (Transact-SQL)
title: sys.dm_exec_query_plan (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 08/02/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- dm_exec_query_plan_TSQL
- sys.dm_exec_query_plan
- dm_exec_query_plan
- sys.dm_exec_query_plan_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_exec_query_plan dynamic management function
ms.assetid: e26f0867-9be3-4b2e-969e-7f2840230770
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 18d6e4e8e3c6fe097676d1bf65f337610127566a
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98094108"
---
# <a name="sysdm_exec_query_plan-transact-sql"></a>sys.dm_exec_query_plan (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

針對計畫控制代碼指定的批次，以 XML 格式傳回顯示計畫。 計畫控制代碼指定的計畫可以是快取或目前正在執行的計畫。  
  
執行程式表的 XML 架構已發佈，並可在 [此 Microsoft 網站](https://go.microsoft.com/fwlink/?linkid=43100&clcid=0x409)使用。 安裝有 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的目錄也會提供這個項目。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```  
sys.dm_exec_query_plan(plan_handle)  
```  
  
## <a name="arguments"></a>引數  
*plan_handle*  
這是一種權杖，可唯一識別已執行之批次的查詢執行計畫，而且其計畫位於計畫快取或目前正在執行中。 *plan_handle* 是 **Varbinary (64)**。   

您可以從下列動態管理物件中取得 *plan_handle* ：
  
-   [sys.dm_exec_cached_plans &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-cached-plans-transact-sql.md)  
  
-   [sys.dm_exec_query_stats &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql.md)  
  
-   [sys.dm_exec_requests &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql.md)  

-   [sys.dm_exec_procedure_stats &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-procedure-stats-transact-sql.md)  

-   [sys.dm_exec_trigger_stats &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-trigger-stats-transact-sql.md)  
  
## <a name="table-returned"></a>傳回的資料表  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**dbid**|**smallint**|當編譯對應於這個計畫的 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式時，作用中內容資料庫的識別碼。 對於隨選和準備的 SQL 陳述式而言，則為編譯陳述式的資料庫識別碼。<br /><br /> 資料行可為 Null。|  
|**objectid**|**int**|這個查詢計畫的物件識別碼 (如預存程序或使用者自訂函數)。 若為特定和準備批次，這個資料行是 **Null**。<br /><br /> 資料行可為 Null。|  
|**number**|**smallint**|編號預存程序整數。 例如，**orders** 應用程式的一組程序可以命名為 **orderproc;1**、**orderproc;2**，依此類推。 若為特定和準備批次，這個資料行是 **Null**。<br /><br /> 資料行可為 Null。|  
|**加密**|**bit**|指出對應的預存程序是否加密。<br /><br /> 0 = 未加密<br /><br /> 1 = 加密<br /><br /> 資料行不可為 Null。|  
|**query_plan**|**xml**|包含以 *plan_handle* 指定之查詢執行計畫的編譯階段顯示計畫標記法。 顯示計畫是 XML 格式。 每個包含諸如特定 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式、預存程序呼叫和使用者自訂函數呼叫的批次，都會產生一份計畫。<br /><br /> 資料行可為 Null。|  
  
## <a name="remarks"></a>備註  
 在下列狀況之下，**sys.dm_exec_query_plan** 傳回之資料表的 **query_plan** 資料行不會傳回任何顯示計畫輸出：  
  
-   如果已從計畫快取中收回使用 *plan_handle* 指定的查詢計劃，則傳回資料表的 **query_plan** 資料行會是 null。 例如，如果從擷取計畫控制代碼到 **sys.dm_exec_query_plan** 使用計畫控制代碼之間，延遲了一段時間，就可能出現這個情況。  
  
-   尚未快取某些 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式，如大量作業陳述式或包含大小超出 8 KB 字串文字的陳述式。 您無法利用 **sys.dm_exec_query_plan** 來擷取這些陳述式的 XML 顯示計畫，因為它們不在快取中，除非批次正在執行。  
  
-   如果 [!INCLUDE[tsql](../../includes/tsql-md.md)] 批次或預存套裝程式含對使用者定義函數的呼叫或對動態 SQL 的呼叫，例如使用 EXEC (*字串*) ，則會將使用者自訂函數的編譯 XML 執行程式表，不包含在批次或預存程式 **sys.dm_exec_query_plan** 所傳回的資料表中。 相反地，您必須針對對應至使用者定義函數的計畫控制碼，進行個別的 **sys.dm_exec_query_plan** 呼叫。  
  
 當隨選查詢使用簡單或強制參數化時，**query_plan** 資料行只會包含陳述式文字，而非實際查詢計畫。 若要傳回查詢計畫，請呼叫 **sys.dm_exec_query_plan** 來取得準備參數化查詢的計畫控制代碼。 您可以藉由參考 [sys.syscacheobjects](../../relational-databases/system-compatibility-views/sys-syscacheobjects-transact-sql.md) 檢視的 **sql** 資料行，或 [sys.dm_exec_sql_text](../../relational-databases/system-dynamic-management-views/sys-dm-exec-sql-text-transact-sql.md) 動態管理檢視的文字資料行，判斷查詢是否參數化。  
  
> [!NOTE] 
> 由於 **xml** 資料類型所允許的嵌套層級數目有所限制， **sys.dm_exec_query_plan** 無法傳回符合或超過128層級之嵌套專案的查詢計劃。 在舊版 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中，這會讓查詢計畫無法傳回並產生錯誤 6335。 在 [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] Service Pack 2 和更新版本中， **query_plan** 資料行傳回 Null。   
> 您可以使用 [sys.dm_exec_text_query_plan &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-text-query-plan-transact-sql.md) 動態管理函數，以文字格式傳回查詢計劃的輸出。  
  
## <a name="permissions"></a>權限  
 若要執行 **sys.dm_exec_query_plan**，使用者必須是 **系統管理員（sysadmin** ）固定伺服器角色的成員，或具有伺服器的 `VIEW SERVER STATE` 許可權。  
  
## <a name="examples"></a>範例  
 下列範例會顯示如何使用 **sys.dm_exec_query_plan** 動態管理檢視。  
  
 若要檢視 XML 顯示計畫，請利用 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 的查詢編輯器來執行下列查詢，之後，在 **sys.dm_exec_query_plan** 傳回的資料表中，按一下 **query_plan** 資料行中的 **[ShowPlanXML]**。 此時 [!INCLUDE[ssManStudio](../../includes/ssmanstudio-md.md)] 摘要窗格會顯示 XML 顯示計畫。 若要將 XML 執行程式表儲存至檔案，請以滑鼠右鍵按一下 [ **query_plan** ] 資料行中的 [ **ShowPlanXML** ]，按一下 [**另存結果**]，並將檔案命名為 .sqlplan，例如 \<*file_name*> myxmlshowplan.sqlplan. .sqlplan。  
  
### <a name="a-retrieve-the-cached-query-plan-for-a-slow-running-transact-sql-query-or-batch"></a>A. 擷取執行緩慢的 Transact-SQL 查詢或批次之快取查詢計畫  
 各類型 [!INCLUDE[tsql](../../includes/tsql-md.md)] 批次的查詢計畫，如特定批次、預存程序和使用者自訂函數，都會快取在稱為計畫快取的記憶體區域中。 每個快取的查詢計畫都用一個稱為計畫控制代碼的唯一識別碼來識別。 您可以利用 **sys.dm_exec_query_plan** 動態管理檢視來指定這個計畫控制代碼，以擷取特定 [!INCLUDE[tsql](../../includes/tsql-md.md)] 查詢或批次的執行計畫。  
  
 如果 [!INCLUDE[tsql](../../includes/tsql-md.md)] 查詢或批次在特定 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 連接上執行了很長一段時間，請擷取這項查詢或批次的執行計畫來找出延遲的原因。 下列範例會顯示如何擷取執行緩慢的查詢或批次的 XML 顯示計畫。  
  
> [!NOTE]  
>  若要執行此範例，請將 *session_id* 和 *plan_handle* 的值取代為您伺服器特定的值。  
  
 首先，請利用 `sp_who` 預存程序來擷取正在執行查詢或批次之處理序的伺服器處理序識別碼 (SPID)：  
  
```sql  
USE master;  
GO  
exec sp_who;  
GO  
```  
  
 `sp_who` 傳回的結果集指出 SPID 是 `54`。 您可以搭配 `sys.dm_exec_requests` 動態管理檢視來使用 SPID，以利用下列查詢來擷取計畫控制代碼：  
  
```sql  
USE master;  
GO  
SELECT * FROM sys.dm_exec_requests  
WHERE session_id = 54;  
GO  
```  
  
 **Sys.dm_exec_requests** 所傳回的資料表指出執行緩慢的查詢或批次的計畫控制碼是 `0x06000100A27E7C1FA821B10600` ，您可以將其指定為 *plan_handle* 引數，以依照 `sys.dm_exec_query_plan` 下列方式取得 XML 格式的執行計畫。 執行緩慢的查詢或批次之 XML 格式執行計畫，儲存在 `sys.dm_exec_query_plan` 傳回的資料表之 **query_plan** 資料行中。  
  
```sql  
USE master;  
GO  
SELECT * 
FROM sys.dm_exec_query_plan (0x06000100A27E7C1FA821B10600);  
GO  
```  
  
### <a name="b-retrieve-every-query-plan-from-the-plan-cache"></a>B. 從計畫快取中擷取每份查詢計畫  
 若要擷取計畫快取中所有查詢計畫的快照集，請查詢 `sys.dm_exec_cached_plans` 動態管理檢視，來擷取快取中所有查詢計畫的計畫控制代碼。 計畫控制代碼會儲存在 `plan_handle` 的 `sys.dm_exec_cached_plans` 資料行中。 之後，請依照下列方式，利用 CROSS APPLY 運算子，將計畫控制代碼傳給 `sys.dm_exec_query_plan`。 目前在計畫快取中的每項計畫之 XML 顯示計畫輸出，都是在傳回的資料表之 `query_plan` 資料行中。  
  
```sql  
USE master;  
GO  
SELECT * 
FROM sys.dm_exec_cached_plans AS cp 
CROSS APPLY sys.dm_exec_query_plan(cp.plan_handle);  
GO  
```  
  
### <a name="c-retrieve-every-query-plan-for-which-the-server-has-gathered-query-statistics-from-the-plan-cache"></a>C. 擷取伺服器已從計畫快取中收集了查詢統計資料的每項查詢計畫  
 若要擷取伺服器已收集了目前在計畫快取中的統計資料之所有查詢計畫的快照集，請查詢 `sys.dm_exec_query_stats` 動態管理檢視，以擷取快取中這些計畫的計畫控制代碼。 計畫控制代碼會儲存在 `plan_handle` 的 `sys.dm_exec_query_stats` 資料行中。 之後，請依照下列方式，利用 CROSS APPLY 運算子，將計畫控制代碼傳給 `sys.dm_exec_query_plan`。 伺服器已收集了目前在計畫快取中的統計資料之各項計畫之 XML 顯示計畫輸出，都是在傳回的資料表之 `query_plan` 資料行中。  
  
```sql  
USE master;  
GO  
SELECT * 
FROM sys.dm_exec_query_stats AS qs 
CROSS APPLY sys.dm_exec_query_plan(qs.plan_handle);  
GO  
```  
  
### <a name="d-retrieve-information-about-the-top-five-queries-by-average-cpu-time"></a>D. 擷取按平均 CPU 時間排列之前五項查詢的相關資訊  
 下列範例會傳回前五項查詢的計畫和平均 CPU 時間。  
  
```sql  
SELECT TOP 5 total_worker_time/execution_count AS [Avg CPU Time],  
   plan_handle, query_plan   
FROM sys.dm_exec_query_stats AS qs  
CROSS APPLY sys.dm_exec_query_plan(qs.plan_handle)  
ORDER BY total_worker_time/execution_count DESC;  
GO  
```  
  
## <a name="see-also"></a>另請參閱  
 [動態管理檢視與函數 &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)   
 [sys.dm_exec_cached_plans &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-cached-plans-transact-sql.md)   
 [sys.dm_exec_query_stats &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql.md)   
 [sys.dm_exec_requests &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql.md)   
 [sp_who &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-who-transact-sql.md)   
 [執行程序邏輯和實體運算子參考](../../relational-databases/showplan-logical-and-physical-operators-reference.md)   
 [sys.dm_exec_text_query_plan &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-text-query-plan-transact-sql.md)  
  
  
