---
description: sys.stats (Transact-SQL)
title: sys. stats (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 12/18/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.stats
- stats_TSQL
- sys.stats_TSQL
- stats
dev_langs:
- TSQL
helpviewer_keywords:
- sys.stats catalog view
ms.assetid: 42605c80-126f-460a-befb-a0b7482fae6a
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: aea0a611f6186ad2f0985c971547ec11bb0c1964
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98096672"
---
# <a name="sysstats-transact-sql"></a>sys.stats (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  針對 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 資料庫中資料表、索引和索引檢視表的每個統計資料物件，各包含一個資料列。 每個索引都會有一個具有相同名稱和識別碼 (**index_id** stats_id) 的對應統計資料列  =   ，但並非每個統計資料列都有對應的索引。  
  
 目錄檢視 [sys.stats_columns](../../relational-databases/system-catalog-views/sys-stats-columns-transact-sql.md) 提供資料庫中每個資料行的統計資料資訊。 如需統計資料的詳細資訊，請參閱[統計資料](../../relational-databases/statistics/statistics.md)。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|object_id|**int**|這些統計資料所屬物件的識別碼。|  
|**name**|**sysname**|統計資料的名稱。 在物件中，這是唯一的。|  
|**stats_id**|**int**|統計資料的識別碼。 在物件中，這是唯一的。<br /><br />如果統計資料對應到索引，則 *stats_id* 值與 [sys. 索引](../../relational-databases/system-catalog-views/sys-indexes-transact-sql.md)目錄檢視中的 *index_id* 值相同。|  
|**auto_created**|**bit**|指出 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 是否自動建立統計資料。<br /><br /> 0 = [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 未自動建立統計資料。<br /><br /> 1 = [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 自動建立統計資料。|  
|**user_created**|**bit**|指出使用者是否建立統計資料。<br /><br /> 0 = 使用者未建立統計資料。<br /><br /> 1 = 使用者建立統計資料。|  
|**no_recompute**|**bit**|指出是否使用 **NORECOMPUTE** 選項建立統計資料。<br /><br /> 0 = 未使用 **NORECOMPUTE** 選項建立統計資料。<br /><br /> 1 = 統計資料是使用 **NORECOMPUTE** 選項所建立。|  
|**has_filter**|**bit**|0 = 統計資料沒有篩選，而且會在所有資料列上計算。<br /><br /> 1 = 統計資料有篩選，而且只會在滿足篩選定義的資料列上計算。|  
|**filter_definition**|**nvarchar(max)**|包含在已篩選之統計資料內的資料列子集運算式。<br /><br /> NULL = 非篩選的統計資料。|  
|**is_temporary**|**bit**|**適用對象**：[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 及更新版本。<br /><br /> 指出統計資料是否為暫時性。 暫時性統計資料支援已啟用唯讀存取的 [!INCLUDE[ssHADR](../../includes/sshadr-md.md)] 次要資料庫。<br /><br /> 0 = 統計資料不是暫時性。<br /><br /> 1 = 統計資料是暫時性。|  
|**is_incremental**|**bit**|**適用對象**：[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 及更新版本。<br /><br /> 指出是否將統計資料建立成累加統計資料。<br /><br /> 0 = 統計資料不是累加的。<br /><br /> 1 = 統計資料是累加的。|  
  
## <a name="permissions"></a>權限  
 [!INCLUDE[ssCatViewPerm](../../includes/sscatviewperm-md.md)] 如需相關資訊，請參閱 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)。  
  
## <a name="examples"></a>範例  
 下列範例會傳回 `HumanResources.Employee` 資料表的所有統計資料及統計資料行。  
  
```sql  
USE AdventureWorks2012;  
GO  
SELECT s.name AS statistics_name  
      ,c.name AS column_name  
      ,sc.stats_column_id  
FROM sys.stats AS s  
INNER JOIN sys.stats_columns AS sc   
    ON s.object_id = sc.object_id AND s.stats_id = sc.stats_id  
INNER JOIN sys.columns AS c   
    ON sc.object_id = c.object_id AND c.column_id = sc.column_id  
WHERE s.object_id = OBJECT_ID('HumanResources.Employee');  
```  
  
## <a name="see-also"></a>另請參閱  
 [物件目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/object-catalog-views-transact-sql.md)   
 [目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/catalog-views-transact-sql.md)   
 [查詢 SQL Server 系統目錄常見問題](../../relational-databases/system-catalog-views/querying-the-sql-server-system-catalog-faq.md)   
 [統計資料](../../relational-databases/statistics/statistics.md)    
 [sys.dm_db_stats_properties &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-stats-properties-transact-sql.md)   
 [sys.dm_db_stats_histogram &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-stats-histogram-transact-sql.md)   
 [sys.stats_columns &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-stats-columns-transact-sql.md)
 

 
