---
description: sys.columns (Transact-SQL)
title: sys. columns (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 11/21/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.columns_TSQL
- sys.columns
- columns_TSQL
- columns
dev_langs:
- TSQL
helpviewer_keywords:
- sys.columns catalog view
ms.assetid: 323ac9ea-fc52-4b8c-8a7e-e0e44f8ed86c
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: bf45b9d2c8293b0a7788b8f8690bf24886433681
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98091618"
---
# <a name="syscolumns-transact-sql"></a>sys.columns (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  針對具有資料行之物件 (例如檢視或資料表) 的每個資料行，各傳回一個資料列。 下面是具有資料行的物件類型清單：  
  
-   資料表值組件函數 (FT)  
  
-   內嵌資料表值 SQL 函數 (IF)  
  
-   內部資料表 (IT)  
  
-   系統資料表 (S)  
  
-   資料表值 SQL 函數 (TF)  
  
-   使用者資料表 (U)  
  
-   檢視 (V)  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|object_id|**int**|這個資料行所屬的物件識別碼。|  
|NAME|**sysname**|資料行的名稱。 在物件中，這是唯一的。|  
|column_id|**int**|資料行的識別碼。 在物件中，這是唯一的。<br /><br /> 資料行識別碼不一定會循序排列。|  
|system_type_id|**tinyint**|資料行的系統類型識別碼。|  
|user_type_id|**int**|使用者所定義的資料行類型識別碼。<br /><br /> 若要傳回類型的名稱，請在此資料行上聯結至 [sys. 類型](../../relational-databases/system-catalog-views/sys-types-transact-sql.md) 目錄檢視。|  
|max_length|**smallint**|資料行的最大長度 (以位元組為單位)。<br /><br /> -1 = 資料行資料類型是 **Varchar (max)**、 **Nvarchar (max)**、 **Varbinary (max)** 或 **xml**。<br /><br /> 若為 **文字** 資料行，max_length 值會是16或 sp_tableoption ' text in row ' 所設定的值。|  
|精確度|**tinyint**|如果是以數值為基礎，便是資料行的有效位數；否則，便是 0。|  
|級別|**tinyint**|如果是以數值為基礎，便是資料行的小數位數；否則，便是 0。|  
|collation_name|**sysname**|如果是以字元為基礎，便是資料行的定序名稱；否則，便是 NULL。|  
|is_nullable|**bit**|1 = 資料行可為 Null。|  
|is_ansi_padded|**bit**|1 = 如果是字元、二進位或變數，則資料行會使用 ANSI_PADDING ON 行為。<br /><br /> 0 = 資料行不是字元、二進位或變數。|  
|is_rowguidcol|**bit**|1 = 資料行是已宣告的 ROWGUIDCOL。|  
|is_identity|**bit**|1 = 資料行有識別值|  
|is_computed|**bit**|1 = 資料行是一個計算資料行。|  
|is_filestream|**bit**|1 = 資料行是 FILESTREAM 資料行。|  
|is_replicated|**bit**|1 = 資料行已被複寫。|  
|is_non_sql_subscribed|**bit**|1 = 資料行有非 SQL Server 的訂閱者。|  
|is_merge_published|**bit**|1 = 資料行已經合併發行。|  
|is_dts_replicated|**bit**|1 = 資料行是利用 [!INCLUDE[ssIS](../../includes/ssis-md.md)] 加以複寫。|  
|is_xml_document|**bit**|1 = 內容是完整的 XML 文件集。<br /><br /> 0 = 內容是檔片段，或是資料行資料類型不是 **xml**。|  
|xml_collection_id|**int**|如果資料行的資料類型是 **xml** ，而且 xml 是具類型的，則為非零。 這個值是包含資料行的驗證 XML 結構描述命名空間之集合的識別碼。<br /><br /> 0 = 沒有 XML 結構描述集合。|  
|default_object_id|**int**|預設物件的識別碼，不論它是獨立的物件 [sys.sp_bindefault](../../relational-databases/system-stored-procedures/sp-bindefault-transact-sql.md)還是內嵌的資料行層級預設條件約束。 內嵌資料行層級預設物件的 parent_object_id 資料行，就是資料表本身的參考。<br /><br /> 0 = 沒有預設值。|  
|rule_object_id|**int**|獨立規則的識別碼，這個規則是利用 sys.sp_bindrule 與資料行繫結。<br /><br /> 0 = 沒有獨立規則。 如需資料行層級檢查條件約束，請參閱 [sys.check_constraints &#40;transact-sql&#41;](../../relational-databases/system-catalog-views/sys-check-constraints-transact-sql.md)。|  
|is_sparse|**bit**|1 = 資料行是疏鬆資料行。 如需詳細資訊，請參閱 [使用疏鬆資料行](../../relational-databases/tables/use-sparse-columns.md)。|  
|is_column_set|**bit**|1 = 資料行是資料行集。 如需詳細資訊，請參閱 [使用疏鬆資料行](../../relational-databases/tables/use-sparse-columns.md)。|  
|generated_always_type|**tinyint**|**適用於**：[!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 及更新版本、[!INCLUDE[ssSDS_md](../../includes/sssds-md.md)]。<br /><br /> 識別資料行值產生的時間 (在系統資料表) 中的資料行，一律為0：<br /><br /> 0 = NOT_APPLICABLE<br /><br /> 1 = AS_ROW_START<br /><br /> 2 = AS_ROW_END<br /><br /> 如需詳細資訊，請參閱 [&#41;&#40;關係資料庫的時態表 ](../../relational-databases/tables/temporal-tables.md)。|  
|generated_always_type_desc|**nvarchar(60)**|**適用於**：[!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 及更新版本、[!INCLUDE[ssSDS_md](../../includes/sssds-md.md)]。<br /><br /> `generated_always_type`針對系統資料表中的資料行，值 (一律 NOT_APPLICABLE 的文字描述)  <br /><br /> NOT_APPLICABLE<br /><br /> AS_ROW_START<br /><br /> AS_ROW_END|  
|encryption_type|**int**|**適用於**：[!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 及更新版本、[!INCLUDE[ssSDS_md](../../includes/sssds-md.md)]。<br /><br /> 加密類型：<br /><br /> 1 = 決定性加密<br /><br /> 2 = 隨機化加密|  
|encryption_type_desc|**Nvarchar (64)**|**適用於**：[!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 及更新版本、[!INCLUDE[ssSDS_md](../../includes/sssds-md.md)]。<br /><br /> 加密類型描述：<br /><br /> 隨機<br /><br /> DETERMINISTIC|  
|encryption_algorithm_name|**sysname**|**適用於**：[!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 及更新版本、[!INCLUDE[ssSDS_md](../../includes/sssds-md.md)]。<br /><br /> 加密演算法的名稱。<br /><br /> 僅支援 AEAD_AES_256_CBC_HMAC_SHA_512。|  
|column_encryption_key_id|**int**|**適用於**：[!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 及更新版本、[!INCLUDE[ssSDS_md](../../includes/sssds-md.md)]。<br /><br /> CEK 的識別碼。|  
|column_encryption_key_database_name|**sysname**|**適用於**：[!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 及更新版本、[!INCLUDE[ssSDW_md](../../includes/sssds-md.md)]。<br /><br /> 如果資料行加密金鑰與資料行的資料庫不同，則為存在資料行加密金鑰的資料庫名稱。 如果索引鍵存在於與資料行相同的資料庫中，則為 Null。|  
|is_hidden|**bit**|**適用於**：[!INCLUDE[ssCurrentLong](../../includes/sscurrent-md.md)] 及更新版本、[!INCLUDE[ssSDS_md](../../includes/sssds-md.md)]。<br /><br /> 表示資料行是否隱藏：<br /><br /> 0 = 一般、非隱藏、可見的資料行<br /><br /> 1 = 隱藏的資料行|  
|is_masked|**bit**|**適用於**：[!INCLUDE[ssCurrentLong](../../includes/sscurrent-md.md)] 及更新版本、[!INCLUDE[ssSDS_md](../../includes/sssds-md.md)]。<br /><br /> 指出資料行是否由動態資料遮罩遮罩：<br /><br /> 0 = 一般、非遮罩的資料行<br /><br /> 1 = 資料行已遮罩|  


 
## <a name="permissions"></a>權限  
 [!INCLUDE[ssCatViewPerm](../../includes/sscatviewperm-md.md)] 如需相關資訊，請參閱 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)。  
  
## <a name="see-also"></a>另請參閱  
 [&#40;Transact-sql&#41;的系統檢視 ](../../t-sql/language-reference.md)   
 [物件目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/object-catalog-views-transact-sql.md)   
 [目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/catalog-views-transact-sql.md)   
 [查詢 SQL Server 系統目錄常見問題](../../relational-databases/system-catalog-views/querying-the-sql-server-system-catalog-faq.md)   
 [sys.all_columns &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-all-columns-transact-sql.md)   
 [sys.system_columns &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-system-columns-transact-sql.md)  
  
