---
description: 'sys.external_tables (Transact-sql) '
title: sys.external_tables (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/06/2017
ms.prod: sql
ms.technology: system-objects
ms.topic: language-reference
dev_langs:
- TSQL
ms.assetid: fac4720c-b679-4ab2-864b-ff7810a9b559
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 443086b2deb37b8c1cce1170e2869fe09db81560
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98098345"
---
# <a name="sysexternal_tables-transact-sql"></a>sys.external_tables (Transact-sql) 
[!INCLUDE [sqlserver2016-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sqlserver2016-asdb-asdbmi-asa-pdw.md)]

  針對目前資料庫中的每個外部資料表，各包含一個資料列。  
  
|資料行名稱|資料類型|描述|範圍|  
|-----------------|---------------|-----------------|-----------|  
|\<inherited columns>||如需此視圖所繼承之資料行的清單，請參閱 [sys. objects &#40;transact-sql&#41;](../../relational-databases/system-catalog-views/sys-objects-transact-sql.md)。||  
|max_column_id_used|**int**|此資料表先前使用的資料行識別碼上限。||  
|uses_ansi_nulls|**bit**|資料表是在 SET ANSI_NULLS 資料庫選項為 ON 的情況下加以建立。||  
|data_source_id|**int**|外部資料源的物件識別碼。||  
|file_format_id|**int**|針對 HADOOP 外部資料源的外部資料表，這是外部檔案格式的物件識別碼。||  
|location|**nvarchar(4000)**|針對 HADOOP 外部資料源的外部資料表，這是 HDFS 中的外部資料路徑。||  
|reject_type|**tinyint**|針對 HADOOP 外部資料源的外部資料表，這是在查詢外部資料時，被拒絕的資料列計算方式。|VALUE-拒絕的資料列數目。<br /><br /> 百分比-拒絕的資料列百分比。|  
|reject_value|**float**|針對 HADOOP 外部資料源的外部資料表：<br /><br /> 針對 *reject_type =* value，這是在查詢失敗之前允許的資料列拒絕數目。<br /><br /> 針對 *reject_type* = 百分比，這是在查詢失敗之前，允許的資料列拒絕百分比。||  
|reject_sample_value|**int**|針對 *reject_type* = 百分比，這是在計算拒絕的資料列百分比之前，要載入的資料列數目（成功或失敗）。|如果 reject_type = VALUE，則為 Null。|  
|distribution_type|**int**|針對外部資料表，而不是在外部資料源 SHARD_MAP_MANAGER，這是跨基礎基表之資料列的資料散發。|0-分區化<br /><br /> 1-已複寫<br /><br /> 2-迴圈配置資源|  
|distribution_desc|**nvarchar(120)**|針對外部資料表，而不是 SHARD_MAP_MANAGER 的外部資料源，這是顯示為字串的散發類型。||  
|sharding_column_id|**int**|針對外部資料表，而不是 SHARD_MAP_MANAGER 的外部資料源和分區化散發，這是包含分區化索引鍵值之資料行的資料行識別碼。||  
|remote_schema_name|**sysname**|如果是外部資料表，而不是 SHARD_MAP_MANAGER 的外部資料源，則這是基表所在的架構，如果不同于定義了外部資料表) 的架構，就會在遠端資料庫 (。||  
|remote_object_name|**sysname**|針對外部資料表 SHARD_MAP_MANAGER 外部資料源，這是遠端資料庫上的基表名稱 (如果不同于外部資料表) 的名稱。||  
  
## <a name="permissions"></a>權限  
 目錄檢視內中繼資料的可見性會限制在使用者所擁有的安全性實體，或已授與使用者某些權限的安全性實體。 如需相關資訊，請參閱 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)。  
  
## <a name="see-also"></a>另請參閱  
 [sys.external_file_formats &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-external-file-formats-transact-sql.md)   
 [sys.external_data_sources &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-external-data-sources-transact-sql.md)   
 [CREATE EXTERNAL TABLE &#40;TRANSACT-SQL&#41;](../../t-sql/statements/create-external-table-transact-sql.md)  
  
  
