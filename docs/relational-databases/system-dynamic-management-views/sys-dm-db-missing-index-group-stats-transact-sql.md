---
description: sys.dm_db_missing_index_group_stats (Transact-SQL)
title: sys.dm_db_missing_index_group_stats (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.dm_db_missing_index_group_stats_TSQL
- sys.dm_db_missing_index_group_stats
- dm_db_missing_index_group_stats_TSQL
- dm_db_missing_index_group_stats
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_db_missing_index_group_stats dynamic management view
- missing indexes feature [SQL Server], sys.dm_db_missing_index_group_stats dynamic management view
ms.assetid: c2886986-9e07-44ea-a350-feeac05ee4f4
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: a1c1e0236316970d953d5267d22e5598b0cbb215
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98097738"
---
# <a name="sysdm_db_missing_index_group_stats-transact-sql"></a>sys.dm_db_missing_index_group_stats (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  傳回有關遺漏索引 (不包含空間索引) 群組的摘要資訊。  
  
 在 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]，動態管理檢視不可以公開可能會影響資料庫內含項目的資訊或公開有關使用者可存取之其他資料庫的資訊。 為了避免公開此資訊，每個包含不屬於已連線租使用者之資料的資料列都會被篩選掉。  
    
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**group_handle**|**int**|識別一組遺漏的索引。 這個識別碼在伺服器中是唯一的。<br /><br /> 其他資料行提供有關群組中被視為遺漏之索引的所有查詢資訊。<br /><br /> 一個索引群組僅能包含一個索引。|  
|**unique_compiles**|**bigint**|由於此遺漏索引群組而獲益之編譯與重新編譯的次數。 許多不同查詢的編譯和重新編譯都可以構成這個資料行的值。|  
|**user_seeks**|**bigint**|群組中建議索引適用之使用者查詢所造成的搜尋次數。|  
|**user_scans**|**bigint**|群組中建議索引適用之使用者查詢所造成的掃描次數。|  
|**last_user_seek**|**datetime**|群組中建議索引適用之使用者查詢所造成的上次搜尋日期和時間。|  
|**last_user_scan**|**datetime**|群組中建議索引適用之使用者查詢所造成的上次掃描日期和時間。|  
|**avg_total_user_cost**|**float**|可依據群組中的索引降低之使用者查詢的平均成本。|  
|**avg_user_impact**|**float**|實作此遺漏索引群組時，使用者查詢可能獲得的平均效益百分比。 這個值表示如果實作此遺漏索引群組，平均查詢成本將會依此百分比降低。|  
|**system_seeks**|**bigint**|群組中建議索引適用之系統查詢 (例如 Auto Stats 查詢) 所造成的搜尋次數。 如需詳細資訊，請參閱 [Auto Stats 事件類別](../../relational-databases/event-classes/auto-stats-event-class.md)。|  
|**system_scans**|**bigint**|群組中建議索引適用之系統查詢所造成的掃描次數。|  
|**last_system_seek**|**datetime**|群組中建議索引適用之系統查詢所造成的上次系統搜尋日期和時間。|  
|**last_system_scan**|**datetime**|群組中建議索引適用之系統查詢所造成的上次系統掃描日期和時間。|  
|**avg_total_system_cost**|**float**|可依據群組中的索引降低之系統查詢的平均成本。|  
|**avg_system_impact**|**float**|實作此遺漏索引群組時，系統查詢可能獲得的平均效益百分比。 這個值表示如果實作此遺漏索引群組，平均查詢成本將會依此百分比降低。|  
  
## <a name="remarks"></a>備註  
 **sys.dm_db_missing_index_group_stats** 傳回的資訊會在每次執行查詢時更新，而非每次編譯或重新編譯查詢時更新。 使用狀況統計資料不會一直保存，只會保留到 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 重新啟動為止。 如果資料庫管理員想要在伺服器回收之後保留使用狀況統計資料，應該定期製作遺漏索引資訊的備份副本。  

  >[!NOTE]
  >此 DMV 的結果集限制為600個數據列。 每個資料列都包含一個遺漏的索引。 如果您有超過600的遺漏索引，您應該解決現有的遺漏索引，以便您可以查看較新的索引。
  
## <a name="permissions"></a>權限  
 若要查詢此動態管理檢視，使用者必須取得 VIEW SERVER STATE 權限或隱含 VIEW SERVER STATE 權限的任何權限。  
  
## <a name="examples"></a>範例  
 下列範例說明如何使用 **sys.dm_db_missing_index_group_stats** 動態管理檢視。  
  
### <a name="a-find-the-10-missing-indexes-with-the-highest-anticipated-improvement-for-user-queries"></a>A. 尋找改善使用者查詢之預期效果最高的 10 個遺漏索引  
 下列查詢決定哪 10 個遺漏查詢對使用者查詢產生的預期累計改善效果最高 (以遞減順序排列)。  
  
```  
SELECT TOP 10 *  
FROM sys.dm_db_missing_index_group_stats  
ORDER BY avg_total_user_cost * avg_user_impact * (user_seeks + user_scans)DESC;  
```  
  
### <a name="b-find-the-individual-missing-indexes-and-their-column-details-for-a-particular-missing-index-group"></a>B. 尋找個別遺漏索引及其特定遺漏索引群組的資料行詳細資料  
 下列查詢決定哪些遺漏索引構成特定的遺漏索引群組，並顯示其資料行詳細資料。 為了符合這個範例的目的，遺漏索引群組控制代碼為 24。  
  
```  
SELECT migs.group_handle, mid.*  
FROM sys.dm_db_missing_index_group_stats AS migs  
INNER JOIN sys.dm_db_missing_index_groups AS mig  
    ON (migs.group_handle = mig.index_group_handle)  
INNER JOIN sys.dm_db_missing_index_details AS mid  
    ON (mig.index_handle = mid.index_handle)  
WHERE migs.group_handle = 24;  
```  
  
 此查詢提供遺漏索引之資料庫、結構描述和資料表的名稱。 它也提供索引鍵應該使用的資料行名稱。 當撰寫 CREATE INDEX DDL 語句來執行遺漏的索引時，請先在 CREATE INDEX 語句的 ON 子句中列出相等資料行，然後再列出不相等資料行 \<*table_name*> 。 您應該將內含資料行列在 CREATE INDEX 陳述式的 INCLUDE 子句中。 若要決定相等資料行的有效次序，請依據其選擇性排列這些資料行，將選擇性最高的資料行列在最前面 (資料行清單的最左邊)。  
  
## <a name="see-also"></a>另請參閱  
 [sys.dm_db_missing_index_columns &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-missing-index-columns-transact-sql.md)   
 [sys.dm_db_missing_index_details &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-missing-index-details-transact-sql.md)   
 [sys.dm_db_missing_index_groups &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-missing-index-groups-transact-sql.md)   
 [CREATE INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/create-index-transact-sql.md)  
  
  
