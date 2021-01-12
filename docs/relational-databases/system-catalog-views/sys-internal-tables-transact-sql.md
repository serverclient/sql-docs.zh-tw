---
description: sys.internal_tables (Transact-SQL)
title: sys.internal_tables (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2019
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.internal_tables
- internal_tables
- sys.internal_tables_TSQL
- internal_tables_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- internal tables
- sys.internal_tables catalog view
ms.assetid: a5821c70-f150-4676-8476-3a31f7403dca
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: ecc601c3eec509258cdac61a9c9216077f36f712
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98094507"
---
# <a name="sysinternal_tables-transact-sql"></a>sys.internal_tables (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  針對每個內部資料表物件，各傳回一個資料列。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會自動產生內部資料表來支援各種功能。 例如，當您建立主要 XML 索引時，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 就會自動建立內部資料表來保存零碎的 XML 文件資料。 內部資料表會出現在每個資料庫的 **sys** 架構中，而且系統產生的唯一名稱會指出其函式，例如 **xml_index_nodes_2021582240_32001** 或 **queue_messages_1977058079**  
  
 內部資料表不包含使用者可存取的資料，而且其結構描述是固定且無法變更的。 您無法在 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式中參考內部資料表名稱。 例如，您無法執行 SELECT FROM 之類的語句 \* *\<sys.internal_table_name>* 。 不過，您可以查詢目錄檢視來查看內部資料表的中繼資料。  
  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**\<Columns inherited from sys.objects>**||如需此視圖所繼承之資料行的清單，請參閱 [sys. objects &#40;transact-sql&#41;](../../relational-databases/system-catalog-views/sys-objects-transact-sql.md)。|  
|**internal_type**|**tinyint**|內部資料表的類型：<br /><br /> 3 = **query_disk_store_query_hints**<br /><br /> 4 = **query_disk_store_query_template_parameterization**<br /><br /> 6 = **query_disk_store_wait_stats**<br /><br /> 201 = **queue_messages**<br /><br /> 202 = **xml_index_nodes**<br /><br /> 203 = **fulltext_catalog_freelist**<br /><br /> 205 = **query_notification**<br /><br /> 206 = **service_broker_map**<br /><br /> 207 = **extended_indexes** (例如空間索引) <br /><br /> 208 = **filestream_tombstone**<br /><br /> 209 = **change_tracking**<br /><br /> 210 = **tracked_committed_transactions**<br /><br /> 220 = **contained_features**<br /><br /> 225 = **filetable_updates**<br /><br /> 236 = **selective_xml_index_node_table**<br /><br /> 240 = **query_disk_store_query_text**<br /><br /> 241 = **query_disk_store_query**<br /><br /> 242 = **query_disk_store_plan**<br /><br /> 243 = **query_disk_store_runtime_stats**<br /><br /> 244 = **query_disk_store_runtime_stats_interval**<br /><br /> 245 = **query_coNtext_settings**|  
|**internal_type_desc**|**nvarchar(60)**|內部資料表類型的描述：<br /><br /> QUERY_DISK_STORE_QUERY_HINTS<br /><br /> QUERY_DISK_STORE_QUERY_TEMPLATE_PARAMETERIZATION<br /><br /> QUERY_DISK_STORE_WAIT_STATS<br /><br /> QUEUE_MESSAGES<br /><br /> XML_INDEX_NODES<br /><br /> FULLTEXT_CATALOG_FREELIST<br /><br /> FULLTEXT_CATALOG_MAP<br /><br /> QUERY_NOTIFICATION<br /><br /> SERVICE_BROKER_MAP<br /><br /> EXTENDED_INDEXES<br /><br /> FILESTREAM_TOMBSTONE<br /><br /> CHANGE_TRACKING<br /><br /> TRACKED_COMMITTED_TRANSACTIONS<br /><br /> CONTAINED_FEATURES<br /><br /> FILETABLE_UPDATES<br /><br /> SELECTIVE_XML_INDEX_NODE_TABLE<br /><br /> QUERY_DISK_STORE_QUERY_TEXT<br /><br /> QUERY_DISK_STORE_QUERY<br /><br /> QUERY_DISK_STORE_PLAN<br /><br /> QUERY_DISK_STORE_RUNTIME_STATS<br /><br /> QUERY_DISK_STORE_RUNTIME_STATS_INTERVAL<br /><br /> QUERY_CONTEXT_SETTINGS|  
|**parent_id**|**int**|父系的識別碼，不論是否以結構描述為範圍，都是如此。 否則，在沒有父系的狀況下，便是 0。<br /><br /> **queue_messages**  = 佇列的 **object_id**<br /><br /> **xml_index_nodes**  = xml 索引的 **object_id**<br /><br /> **fulltext_catalog_freelist**  = 全文檢索目錄的 **fulltext_catalog_id**<br /><br /> **fulltext_index_map**  = 全文檢索索引的 **object_id**<br /><br /> **query_notification**，或 **service_broker_map** = 0<br /><br /> **extended_indexes**  = 擴充索引的 **object_id** ，例如空間索引<br /><br /> 啟用資料表追蹤的資料表 **object_id** = **change_tracking**|  
|**parent_minor_id**|**int**|父系的次要識別碼。<br /><br /> **xml_index_nodes**  = XML 索引的 **index_id**<br /><br /> **extended_indexes**  = 擴充索引的 **index_id** ，例如空間索引<br /><br /> 0 = **queue_messages**、 **fulltext_catalog_freelist**、 **fulltext_index_map**、 **query_notification**、 **service_broker_map** 或 **change_tracking**|  
|**lob_data_space_id**|**int**|非零值是存放這份資料表的大型物件 (LOB) 之資料空間 (檔案群組或資料分割結構描述) 的識別碼。|  
|**filestream_data_space_id**|**int**|保留供未來使用。|  
  
## <a name="permissions"></a>權限  
 [!INCLUDE[ssCatViewPerm](../../includes/sscatviewperm-md.md)] 如需相關資訊，請參閱 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)。  
  
## <a name="remarks"></a>備註  
 內部資料表會與父實體放置於相同的檔案群組中。 您可以使用下列範例 F 中顯示的目錄查詢，傳回內部資料表針對同資料列、資料列外和大型物件 (LOB) 資料所使用的頁面數。  
  
 您可以使用 [sp_spaceused](../../relational-databases/system-stored-procedures/sp-spaceused-transact-sql.md) 系統程式來傳回內部資料表的空間使用方式資料。 **sp_spaceused** 會以下列方式報告內部資料表空間：  
  
-   當您指定佇列名稱後，就會參考與此佇列相關聯的基礎內部資料表，並且報告其儲存耗用量。  
  
-   XML 索引的內部資料表、空間索引和全文檢索索引所使用的頁面，都包含在 **index_size** 資料行中。 當指定資料表或索引視圖名稱時，該物件的 XML 索引、空間索引和全文檢索索引的頁面會包含在 **保留** 和 **index_size** 的資料行中。  
  
## <a name="examples"></a>範例  
 下列範例將說明如何使用目錄檢視來查詢內部資料表中繼資料。  
  
### <a name="a-show-internal-tables-that-inherit-columns-from-the-sysobjects-catalog-view"></a>A. 顯示從 sys.objects 目錄檢視繼承資料行的內部資料表  
  
```  
SELECT * FROM sys.objects WHERE type = 'IT';  
```  
  
### <a name="b-return-all-internal-table-metadata-including-that-which-is-inherited-from-sysobjects"></a>B. 傳回所有內部資料表中繼資料 (包括從 sys.objects 繼承的資料行)  
  
```  
SELECT * FROM sys.internal_tables;  
```  
  
### <a name="c-return-internal-table-columns-and-column-data-types"></a>C. 傳回內部資料表的資料行和資料行資料類型  
  
```  
SELECT SCHEMA_NAME(itab.schema_id) AS schema_name  
    ,itab.name AS internal_table_name  
    ,typ.name AS column_data_type   
    ,col.*  
FROM sys.internal_tables AS itab  
JOIN sys.columns AS col ON itab.object_id = col.object_id  
JOIN sys.types AS typ ON typ.user_type_id = col.user_type_id  
ORDER BY itab.name, col.column_id;  
```  
  
### <a name="d-return-internal-table-indexes"></a>D. 傳回內部資料表索引  
  
```  
SELECT SCHEMA_NAME(itab.schema_id) AS schema_name  
    , itab.name AS internal_table_name  
    , idx.*  
FROM sys.internal_tables AS itab  
JOIN sys.indexes AS idx ON itab.object_id = idx.object_id  
ORDER BY itab.name, idx.index_id;  
```  
  
### <a name="e-return-internal-table-statistics"></a>E. 傳回內部資料表統計資料  
  
```  
SELECT SCHEMA_NAME(itab.schema_id) AS schema_name  
    ,itab.name AS internal_table_name  
    , s.*  
FROM sys.internal_tables AS itab  
JOIN sys.stats AS s ON itab.object_id = s.object_id  
ORDER BY itab.name, s.stats_id;  
```  
  
### <a name="f-return-internal-table-partition-and-allocation-unit-information"></a>F. 傳回內部資料表的資料分割和配置單位資訊  
  
```  
SELECT SCHEMA_NAME(itab.schema_id) AS schema_name  
    ,itab.name AS internal_table_name  
    ,idx.name AS heap_or_index_name  
    ,p.*  
    ,au.*  
FROM sys.internal_tables AS itab  
JOIN sys.indexes AS idx  
--     JOIN to the heap or the clustered index  
    ON itab.object_id = idx.object_id AND idx.index_id IN (0,1)  
JOIN   sys.partitions AS p   
    ON p.object_id = idx.object_id AND p.index_id = idx.index_id  
JOIN   sys.allocation_units AS au  
--     IN_ROW_DATA (type 1) and ROW_OVERFLOW_DATA (type 3) => JOIN to partition's Hobt  
--     else LOB_DATA (type 2) => JOIN to the partition ID itself.  
ON au.container_id =    
    CASE au.type   
        WHEN 2 THEN p.partition_id   
        ELSE p.hobt_id   
    END  
ORDER BY itab.name, idx.index_id;  
```  
  
### <a name="g-return-internal-table-metadata-for-xml-indexes"></a>G. 傳回 XML 索引的內部資料表中繼資料  
  
```  
SELECT t.name AS parent_table  
    ,t.object_id AS parent_table_id  
    ,it.name AS internal_table_name  
    ,it.object_id AS internal_table_id  
    ,xi.name AS primary_XML_index_name  
    ,xi.index_id as primary_XML_index_id  
FROM sys.internal_tables AS it  
JOIN sys.tables AS t   
    ON it.parent_id = t.object_id  
JOIN sys.xml_indexes AS xi   
    ON it.parent_id = xi.object_id  
    AND it.parent_minor_id  = xi.index_id  
WHERE it.internal_type_desc = 'XML_INDEX_NODES';  
GO  
```  
  
### <a name="h-return-internal-table-metadata-for-service-broker-queues"></a>H. 傳回 Service Broker 佇列的內部資料表中繼資料  
  
```  
SELECT q.name AS queue_name  
    ,q.object_id AS queue_id  
    ,it.name AS internal_table_name  
    ,it.object_id AS internal_table_id  
FROM sys.internal_tables AS it  
JOIN sys.service_queues  AS  q ON it.parent_id = q.object_id  
WHERE it.internal_type_desc = 'QUEUE_MESSAGES';  
GO  
```  
  
## <a name="i-return-internal-table-metadata-for-all-service-broker-services"></a>I. 傳回所有 Service Broker 服務的內部資料表中繼資料  
  
```  
SELECT *   
FROM tempdb.sys.internal_tables   
WHERE internal_type_desc = 'SERVICE_BROKER_MAP';  
GO  
```  
  
## <a name="see-also"></a>另請參閱  
 [目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/catalog-views-transact-sql.md)   
 [物件目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/object-catalog-views-transact-sql.md)  
  
  
