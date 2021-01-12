---
description: sys.dm_exec_cached_plans (Transact-SQL)
title: sys.dm_exec_cached_plans (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 09/18/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.dm_exec_cached_plans
- dm_exec_cached_plans
- dm_exec_cached_plans_TSQL
- sys.dm_exec_cached_plans_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_exec_cached_plans dynamic management view
ms.assetid: 95b707d3-3a93-407f-8e88-4515d4f2039d
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 46e65114623c1c1c8744643e62cf6f355f5f0475
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98092859"
---
# <a name="sysdm_exec_cached_plans-transact-sql"></a>sys.dm_exec_cached_plans (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  針對 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 快取的每個查詢計畫傳回一個資料列，加快查詢執行的速度。 您可以使用這個動態管理檢視尋找快取的查詢計畫、快取的查詢文字、快取計畫所使用的記憶體，以及快取計畫的重複使用計數。  
  
 在 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]，動態管理檢視不可以公開可能會影響資料庫內含項目的資訊或公開有關使用者可存取之其他資料庫的資訊。 為了避免公開此資訊，每個包含不屬於已連線租使用者之資料的資料列都會被篩選掉。此外， **memory_object_address** 和 **pool_id** 資料行中的值會經過篩選;資料行值設定為 Null。  
  
> [!NOTE]  
>  若要從或呼叫這個 [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] ，請使用 **sys.dm_pdw_nodes_exec_cached_plans** 名稱。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|bucketid|**int**|這是快取項目的雜湊值區識別碼。 這個值的範圍從 0 開始，一直到該快取類型的雜湊資料表大小。<br /><br /> 若為 SQL 計畫和物件計畫快取，雜湊資料表在 32 位元系統上最大為 10007，在 64 位元系統上最大為 40009。 若為 Bound Trees 快取，雜湊資料表在 32 位元系統上最大為 1009，在 64 位元系統上最大為 4001。 若為擴充預存程序快取，雜湊資料表在 32 位元和 64 位元系統上最大為 127。|  
|refcounts|**int**|參考這個快取物件的快取物件數目。 **Refcounts** 必須至少為 1，快取中才能有項目。|  
|usecounts|**int**|已經查閱快取物件的次數。 當參數化查詢在快取中找到計畫時，不會累加。 但是，使用執行程序表時，可能會累加多次。|  
|size_in_bytes|**int**|快取物件所耗用的位元組數目。|  
|memory_object_address|**varbinary(8)**|快取項目的記憶體位址。 這個值可以與 [sys.dm_os_memory_objects](../../relational-databases/system-dynamic-management-views/sys-dm-os-memory-objects-transact-sql.md) 一起使用，以取得快取計畫的記憶體細分，也可以與 [sys.dm_os_memory_cache_entries](../../relational-databases/system-dynamic-management-views/sys-dm-os-memory-cache-entries-transact-sql.md) 一起使用，以取得快取項目的成本。|  
|cacheobjtype|**Nvarchar (34)**|快取中的物件類型。 這個值可以是下列值之一：<br /><br /> 編譯的計畫<br /><br /> 已編譯計畫虛設常式<br /><br /> 剖析樹狀結構<br /><br /> 擴充程序<br /><br /> CLR 編譯的函數<br /><br /> CLR 編譯的程序|  
|objtype|**Nvarchar (16)**|物件的類型。 以下是可能的值及其對應的描述。<br /><br /> Proc：預存程式<br />備妥：備妥的語句<br />臨機操作的特定查詢。 是指 [!INCLUDE[tsql](../../includes/tsql-md.md)] 使用 **osql** 或 **sqlcmd** （而非遠端程序呼叫）提交為語言事件。<br />ReplProc： Replication-filter-procedure<br />觸發程式：觸發程式<br />View： View<br />預設值：預設值<br />UsrTab：使用者資料表<br />SysTab：系統資料表<br />Check：檢查條件約束<br />規則：規則|  
|plan_handle|**varbinary(64)**|記憶體中計畫的識別碼。 這個識別碼是暫時性的，只有當計畫留在快取時才會保留。 這個值可以與下列動態管理函數一起使用：<br /><br /> [sys.dm_exec_sql_text](../../relational-databases/system-dynamic-management-views/sys-dm-exec-sql-text-transact-sql.md)<br /><br /> [sys.dm_exec_query_plan](../../relational-databases/system-dynamic-management-views/sys-dm-exec-query-plan-transact-sql.md)<br /><br /> [sys.dm_exec_plan_attributes](../../relational-databases/system-dynamic-management-views/sys-dm-exec-plan-attributes-transact-sql.md)|  
|pool_id|**int**|計算此計畫記憶體使用量代表之資源集區的識別碼。|  
|pdw_node_id|**int**|**適用** 于： [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 、 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]<br /><br /> 此散發所在之節點的識別碼。|  
  
 <sup>1</sup>  
  
## <a name="permissions"></a>權限

在上 [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] ，需要 `VIEW SERVER STATE` 許可權。   
在 SQL Database Basic、S0 和 S1 服務目標上，以及針對彈性集區中的資料庫，則 `Server admin` `Azure Active Directory admin` 需要或帳戶。 在所有其他 SQL Database 服務目標上， `VIEW DATABASE STATE` 資料庫中都需要有許可權。   

## <a name="examples"></a>範例  
  
### <a name="a-returning-the-batch-text-of-cached-entries-that-are-reused"></a>A. 傳回重複使用之快取項目的批次文字  
 下列範例會針對使用超過一次的所有快取項目，傳回其 SQL 文字。  
  
```sql  
SELECT usecounts, cacheobjtype, objtype, text   
FROM sys.dm_exec_cached_plans   
CROSS APPLY sys.dm_exec_sql_text(plan_handle)   
WHERE usecounts > 1   
ORDER BY usecounts DESC;  
GO  
```  
  
### <a name="b-returning-query-plans-for-all-cached-triggers"></a>B. 傳回所有快取觸發程序的查詢計畫  
 下列範例會傳回所有快取觸發程序的快取計畫。  
  
```sql  
SELECT plan_handle, query_plan, objtype   
FROM sys.dm_exec_cached_plans   
CROSS APPLY sys.dm_exec_query_plan(plan_handle)   
WHERE objtype ='Trigger';  
GO  
```  
  
### <a name="c-returning-the-set-options-with-which-the-plan-was-compiled"></a>C. 傳回編譯計畫所用的 SET 選項  
 下列範例會傳回編譯計畫所用的 SET 選項。 `sql_handle`也會傳回方案的。 PIVOT 運算子用來將 `set_options` 和 `sql_handle` 屬性輸出為數據行，而不是資料列。 如需中所傳回值的詳細資訊 `set_options` ，請參閱 [Sys.dm_exec_plan_attributes &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-plan-attributes-transact-sql.md)。  
  
```sql  
SELECT plan_handle, pvt.set_options, pvt.sql_handle  
FROM (  
      SELECT plan_handle, epa.attribute, epa.value   
      FROM sys.dm_exec_cached_plans   
      OUTER APPLY sys.dm_exec_plan_attributes(plan_handle) AS epa  
      WHERE cacheobjtype = 'Compiled Plan'  
      ) AS ecpa   
PIVOT (MAX(ecpa.value) FOR ecpa.attribute IN ("set_options", "sql_handle")) AS pvt;  
GO  
```  
  
### <a name="d-returning-the-memory-breakdown-of-all-cached-compiled-plans"></a>D. 傳回所有快取編譯計畫的記憶體細分  
 下列範例會傳回快取中所有編譯計畫所用細分的記憶體。  
  
```sql  
SELECT plan_handle, ecp.memory_object_address AS CompiledPlan_MemoryObject,   
    omo.memory_object_address, type, page_size_in_bytes   
FROM sys.dm_exec_cached_plans AS ecp   
JOIN sys.dm_os_memory_objects AS omo   
    ON ecp.memory_object_address = omo.memory_object_address   
    OR ecp.memory_object_address = omo.parent_address  
WHERE cacheobjtype = 'Compiled Plan';  
GO  
```  
  
## <a name="see-also"></a>另請參閱  
 [動態管理檢視與函數 &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)   
 [執行相關的動態管理檢視和函數 &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/execution-related-dynamic-management-views-and-functions-transact-sql.md)   
 [sys.dm_exec_query_plan &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-query-plan-transact-sql.md)   
 [sys.dm_exec_plan_attributes &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-plan-attributes-transact-sql.md)   
 [sys.dm_exec_sql_text &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-sql-text-transact-sql.md)   
 [sys.dm_os_memory_objects &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-os-memory-objects-transact-sql.md)   
 [sys.dm_os_memory_cache_entries &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-os-memory-cache-entries-transact-sql.md)   
 [FROM &#40;Transact-SQL&#41;](../../t-sql/queries/from-transact-sql.md)  
  
  


