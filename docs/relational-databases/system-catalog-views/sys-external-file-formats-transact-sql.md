---
description: 'sys.external_file_formats (Transact-sql) '
title: sys.external_file_formats (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
dev_langs:
- TSQL
ms.assetid: a89efb2c-0a3a-4b64-9284-6e93263e29ac
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 98b926499793c62817f884645366848c81bb4e4e
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98094621"
---
# <a name="sysexternal_file_formats-transact-sql"></a>sys.external_file_formats (Transact-sql) 
[!INCLUDE [sqlserver2016-asdbmi-asa-pdw](../../includes/applies-to-version/sqlserver2016-asdbmi-asa-pdw.md)]

  針對、和之目前資料庫中的每個外部檔案格式，各包含一個資料列 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] [!INCLUDE[ssSDS](../../includes/sssds-md.md)] [!INCLUDE[ssSDW](../../includes/sssdw-md.md)] 。  
  
 針對伺服器上的每個外部檔案格式，各包含一個資料列 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] 。  
  
|資料行名稱|資料類型|描述|範圍|  
|-----------------|---------------|-----------------|-----------|  
|file_format_id|**int**|外部檔案格式的物件識別碼。||  
|NAME|**sysname**|檔案格式的名稱。 在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 和中 [!INCLUDE[ssSDW](../../includes/sssdw-md.md)] ，這對資料庫而言是唯一的。 在中 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] ，這對伺服器而言是唯一的。||  
|format_type|**tinyint**|檔案格式類型。|DELIMITEDTEXT、RCFILE、ORC、PARQUET|  
|field_terminator|**Nvarchar (10)**|針對 format_type = DELIMITEDTEXT，這是欄位結束字元。||  
|string_delimiter|**Nvarchar (10)**|針對 format_type = DELIMITEDTEXT，這是字串分隔符號。||  
|date_format|**nvarchar(50)**|針對 format_type = DELIMITEDTEXT，這是使用者定義的日期和時間格式。||  
|use_type_default|**bit**|針對 format_type = 分隔文字，指定當 PolyBase 將資料從 HDFS 文字檔匯入至時，如何處理遺漏值 [!INCLUDE[ssSDW](../../includes/sssdw-md.md)] 。|0-將遺漏值儲存為字串 ' Null '。<br /><br /> 1-將遺漏值儲存為數據行預設值。|  
|serde_method|**nvarchar(255)**|針對 format_type = RCFILE，這是序列化/還原序列化方法。||  
|row_terminator|**Nvarchar (10)**|針對 format_type = DELIMITEDTEXT，這是在外部 Hadoop 檔案中終止每個資料列的字元字串。|一律為 ' \n '。|  
|編碼|**Nvarchar (10)**|針對 format_type = DELIMITEDTEXT，這是外部 Hadoop 檔案的編碼方法。|一律為 ' UTF8 '。|  
|data_compression|**nvarchar(255)**|外部資料的資料壓縮方法。|針對 format_type = DELIMITEDTEXT：<br /><br /> -' >defaultcodec ' （壓縮）<br />-' Org.apache.hadoop.io.compress.gzipcodec ' （壓縮）<br /><br /> 針對 format_type = RCFILE：<br /><br /> -' >defaultcodec ' （壓縮）<br /><br /> 針對 format_type = ORC：<br /><br /> -' >defaultcodec ' （壓縮）<br />-' 針對使用 org.apache.io.compress.snappycodec ' （壓縮）<br /><br /> 針對 format_type = PARQUET：<br /><br /> -' Org.apache.hadoop.io.compress.gzipcodec ' （壓縮）<br />-' 針對使用 org.apache.io.compress.snappycodec ' （壓縮）|  
  
## <a name="permissions"></a>權限  
 目錄檢視內中繼資料的可見性會限制在使用者所擁有的安全性實體，或已授與使用者某些權限的安全性實體。 如需相關資訊，請參閱 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)。  
  
## <a name="see-also"></a>另請參閱  
 [sys.external_data_sources &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-external-data-sources-transact-sql.md)   
 [sys.external_tables &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-external-tables-transact-sql.md)   
 [CREATE EXTERNAL FILE FORMAT &#40;TRANSACT-SQL&#41;](../../t-sql/statements/create-external-file-format-transact-sql.md)  
  
  
