---
description: sys.dm_db_index_usage_stats (Transact-SQL)
title: sys.dm_db_index_usage_stats (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/20/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- dm_db_index_usage_stats_TSQL
- sys.dm_db_index_usage_stats
- sys.dm_db_index_usage_stats_TSQL
- dm_db_index_usage_stats
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_db_index_usage_stats dynamic management view
ms.assetid: d06a001f-0f72-4679-bc2f-66fff7958b86
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 10a6691dc481c09f02b0bd1dff2850cbe90e0656
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98094129"
---
# <a name="sysdm_db_index_usage_stats-transact-sql"></a>sys.dm_db_index_usage_stats (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  傳回不同類型索引作業的計數，以及每種類型作業上次執行的時間。  
  
 在 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]，動態管理檢視不可以公開可能會影響資料庫內含項目的資訊或公開有關使用者可存取之其他資料庫的資訊。 為了避免公開此資訊，每個包含不屬於已連線租使用者之資料的資料列都會被篩選掉。  
  
> [!NOTE]  
>  **sys.dm_db_index_usage_stats** 不會傳回記憶體優化索引或空間索引的相關資訊。 如需記憶體優化索引使用的詳細資訊，請參閱 [sys.dm_db_xtp_index_stats &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-xtp-index-stats-transact-sql.md)。  
  
> [!NOTE]  
>  若要從或呼叫此視圖 [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] ，請使用 **sys.dm_pdw_nodes_db_index_usage_stats**。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**database_id**|**smallint**|定義資料表或檢視的資料庫識別碼。|  
|object_id|**int**|定義索引之資料表或檢視的識別碼。|  
|**index_id**|**int**|索引的識別碼。|  
|**user_seeks**|**bigint**|由使用者查詢所進行的搜尋數。|  
|**user_scans**|**bigint**|使用者查詢未使用「搜尋」述詞的掃描次數。|  
|**user_lookups**|**bigint**|由使用者查詢所進行的書籤查閱數。|  
|**user_updates**|**bigint**|由使用者查詢所進行的更新數。 這包括插入、刪除和更新，代表未受影響的實際資料列所執行的作業數目。 例如，如果您在一個語句中刪除1000個數據列，此計數會遞增1|  
|**last_user_seek**|**datetime**|上次使用者搜尋的時間|  
|**last_user_scan**|**datetime**|上次使用者掃描的時間。|  
|**last_user_lookup**|**datetime**|上次使用者查閱的時間。|  
|**last_user_update**|**datetime**|上次使用者更新的時間。|  
|**system_seeks**|**bigint**|由系統查詢所進行的搜尋數。|  
|**system_scans**|**bigint**|由系統查詢所進行的掃描數。|  
|**system_lookups**|**bigint**|由系統查詢所進行的查閱數。|  
|**system_updates**|**bigint**|由系統查詢所進行的更新數。|  
|**last_system_seek**|**datetime**|上次系統搜尋的時間。|  
|**last_system_scan**|**datetime**|上次系統掃描的時間。|  
|**last_system_lookup**|**datetime**|上次系統查閱的時間。|  
|**last_system_update**|**datetime**|上次系統更新的時間。|  
|pdw_node_id|**int**|**適用** 于： [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 、 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]<br /><br /> 此散發所在之節點的識別碼。|  
  
## <a name="remarks"></a>備註  
 在指定索引上由一項查詢執行所進行的每個個別搜尋、掃描、查閱或更新，在這份檢視都被當作使用一次該索引，並且累加對應的計數器。 它會針對由使用者提交之查詢所導致的作業，以及由內部產生之查詢所導致的作業 (例如，掃描以收集統計資料)，來報告資訊。  
  
 **user_updates** 計數器會指出索引上由基礎資料表或檢視的插入、更新或刪除作業所導致的維護層級。 您可以利用這份檢視來判斷應用程式根本很少用到的索引。 您也可以利用這份檢視，判斷哪些索引會產生維護上的額外負擔。 您可能會考慮卸除不用於查詢或不常用於查詢，卻會造成維護上額外負擔的索引。  
  
 只要一啟動 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (MSSQLSERVER) 服務，計數器就會初始化為空值。 此外，每當卸離或關閉資料庫時 (例如，因為 AUTO_CLOSE 設為 ON)，就會移除所有與資料庫關聯的資料列。  
  
 使用索引時，如果索引的資料列不存在，則會將資料列加入 **sys.dm_db_index_usage_stats** 中。 加入資料列後，其計數器的初始設定為零。  
  
 在升級為 [!INCLUDE[ssKilimanjaro](../../includes/sskilimanjaro-md.md)] 、 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 或時 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] ，會移除 sys.dm_db_index_usage_stats 中的專案。 從開始 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] ，專案會保留在之前 [!INCLUDE[ssKilimanjaro](../../includes/sskilimanjaro-md.md)] 。  
  
## <a name="permissions"></a>權限  
在上 [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] ，需要 `VIEW SERVER STATE` 許可權。   
在 SQL Database Basic、S0 和 S1 服務目標上，以及針對彈性集區中的資料庫，則 `Server admin` `Azure Active Directory admin` 需要或帳戶。 在所有其他 SQL Database 服務目標上， `VIEW DATABASE STATE` 資料庫中都需要有許可權。  
  
## <a name="see-also"></a>另請參閱  

 [索引相關的動態管理檢視和函式 &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/index-related-dynamic-management-views-and-functions-transact-sql.md)   
 [sys.dm_db_index_physical_stats &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-index-physical-stats-transact-sql.md)   
 [sys.dm_db_index_operational_stats &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-index-operational-stats-transact-sql.md)   
 [sys.indexes &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-indexes-transact-sql.md)   
 [sys.objects &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-objects-transact-sql.md)   
 [效能的監視與微調](../../relational-databases/performance/monitor-and-tune-for-performance.md)  
  
  


