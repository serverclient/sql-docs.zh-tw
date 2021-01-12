---
description: sys.dm_db_file_space_usage (Transact-SQL)
title: sys.dm_db_file_space_usage (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/16/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- dm_db_file_space_usage
- sys.dm_db_file_space_usage_TSQL
- sys.dm_db_file_space_usage
- dm_db_file_space_usage_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_db_file_space_usage dynamic management view
ms.assetid: 148a5276-a8d5-49d2-8146-3c63d24c2144
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: bca14dfbb84763a15791aa4bdefc8b857df9e87e
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98095163"
---
# <a name="sysdm_db_file_space_usage-transact-sql"></a>sys.dm_db_file_space_usage (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  傳回資料庫中每個資料檔案的空間使用量資訊。  
  
> [!NOTE]  
>  若要從或呼叫這個 [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] ，請使用 **sys.dm_pdw_nodes_db_file_space_usage** 名稱。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|database_id|**smallint**|資料庫識別碼。|  
|file_id|**smallint**|檔案識別碼。<br /><br /> file_id 對應至 [sys.dm_io_virtual_file_stats](../../relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql.md) 中的 file_id，以及 [sys.sys](../../relational-databases/system-compatibility-views/sys-sysfiles-transact-sql.md)檔中的 fileid。|  
|filegroup_id|**smallint**|**適用對象**：[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 及更新版本。<br /><br /> 檔案群組識別碼。|  
|total_page_count|**bigint**|**適用對象**：[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 及更新版本。<br /><br /> 資料檔案中的總頁數。|  
|allocated_extent_page_count|**bigint**|**適用對象**：[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 及更新版本。<br /><br /> 資料檔案中配置之範圍內的總頁數。|  
|unallocated_extent_page_count|**bigint**|資料檔案中未配置之範圍內的總頁數。<br /><br /> 不包含已配置範圍中的未使用頁面。|  
|version_store_reserved_page_count|**bigint**|配置給版本存放區的一致範圍中的總頁數。 絕不從混合範圍配置版本存放區頁面。<br /><br /> 不包含 IAM 頁面，因為它們一律從混合範圍中配置。 如果 PFS 頁面是從一致範圍配置，則會包含它們。<br /><br /> 如需詳細資訊，請參閱 [sys.dm_tran_version_store &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-tran-version-store-transact-sql.md)。|  
|user_object_reserved_page_count|**bigint**|從一致範圍中配置給資料庫使用者物件的總頁數。 這個計數不包括已配置範圍中的未使用頁面。<br /><br /> 不包含 IAM 頁面，因為它們一律從混合範圍中配置。 如果 PFS 頁面是從一致範圍配置，則會包含它們。<br /><br /> 您可以使用 [sys.allocation_units](../../relational-databases/system-catalog-views/sys-allocation-units-transact-sql.md) 目錄檢視中的 [total_pages] 資料行，傳回使用者物件中每個配置單位的保留頁面計數。 不過請注意，total_pages 資料行包含 IAM 頁面。|  
|internal_object_reserved_page_count|**bigint**|配置給檔案中內部物件之一致範圍中的總頁數。 這個計數不包括已配置範圍中的未使用頁面。<br /><br /> 不包含 IAM 頁面，因為它們一律從混合範圍中配置。 如果 PFS 頁面是從一致範圍配置，則會包含它們。<br /><br /> 沒有目錄檢視或動態管理物件可傳回每一個內部物件的頁數。|  
|mixed_extent_page_count|**bigint**|檔案中已配置混合範圍的已配置和未配置總頁數。 混合範圍包含已配置給不同物件的頁面。 這個計數包含檔案中的所有 IAM 頁面。|
|modified_extent_page_count|**bigint**|**適用於**：[!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] SP2 及更新版本。<br /><br />自從最後一次完整資料庫備份之後，檔案配置範圍中修改的總頁數。 修改過的頁面計數可以用來追蹤自上次完整備份之後，資料庫中的差異變更量，以決定是否需要差異備份。|
|pdw_node_id|**int**|**適用** 于： [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 、 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]<br /><br /> 此散發所在之節點的識別碼。|  
|distribution_id|**int**|**適用** 于： [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 、 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]<br /><br /> 與散發相關聯的唯一數值識別碼。|  
  
## <a name="remarks"></a>備註  
 頁數一律在範圍層級上產生。 因此，頁數值一定是 8 的倍數。 包含 Global Allocation Map (GAM) 和 Shared Global Allocation Map (SGAM) 配置頁的範圍就是已配置的一致範圍。 上述頁數並不包含它們。 如需頁面和範圍的詳細資訊，請參閱 [頁面和範圍架構指南](../../relational-databases/pages-and-extents-architecture-guide.md)。 
  
 目前版本存放區的內容 [sys.dm_tran_version_store](../../relational-databases/system-dynamic-management-views/sys-dm-tran-version-store-transact-sql.md)。 版本存放區頁面是在檔案層級上追蹤，而不是工作階段和工作層級，因為它們是全域資源。 工作階段可產生版本，但工作階段結束時無法移除版本。 版本存放區清除必須考慮需要存取特定版本之執行最久的交易。 您可以在 [sys.dm_tran_active_snapshot_database_transactions](../../relational-databases/system-dynamic-management-views/sys-dm-tran-active-snapshot-database-transactions-transact-sql.md)中查看 elapsed_time_seconds 資料行，以探索與版本存放區清除相關的最長執行交易。  
  
 mixed_extent_page_count 資料行的經常性變更可指出 SGAM 頁面使用頻繁。 發生這個情形時，您會看到許多 PAGELATCH_UP 等待，其中的等待資源就是 SGAM 頁面。 如需詳細資訊，請參閱 [sys.dm_os_waiting_tasks &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql.md)、 [sys.dm_os_wait_stats &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql.md)和 [sys.dm_os_latch_stats &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-os-latch-stats-transact-sql.md)。  
  
## <a name="user-objects"></a>使用者物件  
 下列物件已包括在使用者物件頁面計數器中：  
  
-   使用者自訂資料表和索引  
  
-   系統資料表和索引  
  
-   全域暫存資料表和索引  
  
-   本機暫存資料表和索引  
  
-   資料表變數  
  
-   資料表值函式中傳回的資料表  
  
## <a name="internal-objects"></a>內部物件  
 內部物件只位於 tempdb 中。 下列物件已包括在內部物件頁面計數器中：  
  
-   用於資料指標或多工緩衝處理作業和暫存大型物件 (LOB) 儲存體的工作資料表  
  
-   用於如雜湊聯結等作業的工作檔案  
  
-   排序執行  
  
## <a name="relationship-cardinalities"></a>關聯性基數  
  
|寄件者|收件者|關聯性|  
|----------|--------|------------------|  
|sys.dm_db_file_space_usage.database_id, file_id|sys.dm_io_virtual_file_stats.database_id, file_id|一對一|  
  
## <a name="permissions"></a>權限

在上 [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] ，需要 `VIEW SERVER STATE` 許可權。   
在 SQL Database Basic、S0 和 S1 服務目標上，以及針對彈性集區中的資料庫，則 `Server admin` `Azure Active Directory admin` 需要或帳戶。 在所有其他 SQL Database 服務目標上， `VIEW DATABASE STATE` 資料庫中都需要有許可權。   

## <a name="examples"></a>範例  
  
### <a name="determing-the-amount-of-free-space-in-tempdb"></a>判斷 tempdb 的可用空間量  
 下列查詢會傳回 **tempdb** 中所有資料檔案中的可用頁數和總可用空間（以 mb 為單位） (mb) 。  
  
```sql
USE tempdb;  
GO  
SELECT SUM(unallocated_extent_page_count) AS [free pages],   
(SUM(unallocated_extent_page_count)*1.0/128) AS [free space in MB]  
FROM sys.dm_db_file_space_usage;  
```  

### <a name="determining-the-amount-of-space-used-by-user-objects"></a>判斷使用者物件使用的空間量  
 下列查詢會傳回 tempdb 中使用者物件使用的總頁數和使用者物件使用的總空間。  
  
```sql  
USE tempdb;  
GO  
SELECT SUM(user_object_reserved_page_count) AS [user object pages used],  
(SUM(user_object_reserved_page_count)*1.0/128) AS [user object space in MB]  
FROM sys.dm_db_file_space_usage;
```  
  
## <a name="see-also"></a>另請參閱  
 [動態管理檢視與函數 &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)   
 [資料庫相關的動態管理檢視 &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/database-related-dynamic-management-views-transact-sql.md)   
 [sys.dm_db_task_space_usage &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-task-space-usage-transact-sql.md)   
 [sys.dm_db_session_space_usage &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-session-space-usage-transact-sql.md)  
