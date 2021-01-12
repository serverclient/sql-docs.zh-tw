---
description: 'sys.index_resumable_operations (Transact-sql) '
title: sys.index_resumable_operations (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 11/12/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.index_resumable_operations_TSQL
- sys.indexes_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.indexes
- sys.index_resumable_operations
ms.assetid: ''
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: =azuresqldb-current||>=sql-server-2017||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 97b02afea0dc4756783ad5197a24b5ca5d952282
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98094521"
---
# <a name="sysindex_resumable_operations-transact-sql"></a>sys.index_resumable_operations (Transact-sql) 

[!INCLUDE[sqlserver2017-asdb](../../includes/applies-to-version/sqlserver2017-asdb.md)]
**sys.index_resumable_operations** 是監視和檢查目前執行狀態以取得可繼續索引重建或建立的系統檢視。  
**適用于**： SQL Server (2017 和更新版本的) ，以及 Azure SQL Database
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|object_id|**int**|此索引所屬物件的識別碼 (不可為 null) 。|  
|**index_id**|**int**|索引的識別碼 (不可為 null) 。 **index_id** 只有在物件內才是唯一的。|
|**name**|**sysname**|索引的名稱。 **名稱** 只有在物件內才是唯一的。|  
|**sql_text**|**nvarchar(max)**|DDL T-sql 語句文字|
|**last_max_dop**|**smallint**|上次使用的 MAX_DOP (預設值 = 0) |
|**partition_number**|**int**|擁有索引或堆積內的資料分割編號。 若為非資料分割資料表和索引，或如果所有資料分割都重建，則此資料行的值為 Null。|
|**state**|**tinyint**|可繼續索引的操作狀態：<br /><br />0 = 正在執行<br /><br />1 = 暫停|
|**state_desc**|**nvarchar(60)**|可繼續索引 (執行中或已暫停的操作狀態原因) |  
|**start_time**|**datetime**|索引作業開始時間 (不可為 null) |
|**last_pause_time**|**datatime**| 索引作業上次暫停時間 (可為 null) 。 如果作業正在執行且永不暫停，則為 Null。|
|**total_execution_time**|**int**|開始時間的總執行時間（以分鐘為單位） (不可為 null) |
|**percent_complete**|**real**|% ( 中的索引作業進度完成，不可為 null) 。|
|**page_count**|**bigint**|索引建立作業針對新的和對應索引所配置的索引頁面總數 ( 不可為 null ) 。

## <a name="permissions"></a>權限

[!INCLUDE[ssCatViewPerm](../../includes/sscatviewperm-md.md)] 如需相關資訊，請參閱 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)。  

## <a name="example"></a>範例

 列出所有可繼續的索引建立或處於暫停狀態的重建作業。

```sql
SELECT * FROM  sys.index_resumable_operations WHERE STATE = 1;  
```

## <a name="see-also"></a>另請參閱

- [ALTER INDEX](../../t-sql/statements/alter-index-transact-sql.md)
- [CREATE INDEX](../../t-sql/statements/create-index-transact-sql.md)
- [目錄檢視](catalog-views-transact-sql.md)
- [物件目錄檢視](object-catalog-views-transact-sql.md)
- [sys.indexes](sys-xml-indexes-transact-sql.md)
- [sys.index_columns](sys-index-columns-transact-sql.md)
- [sys.xml_indexes](sys-xml-indexes-transact-sql.md)
- [sys.objects](sys-index-columns-transact-sql.md)
- [sys.key_constraints](sys-key-constraints-transact-sql.md)
- [sys.filegroups](sys-filegroups-transact-sql.md)
- [sys.partition_schemes](sys-partition-schemes-transact-sql.md)
- [查詢 SQL Server 系統目錄 FAQ](querying-the-sql-server-system-catalog-faq.md)
