---
description: sys.sql_modules (Transact-SQL)
title: sys.sql_modules (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 11/06/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.sql_modules_TSQL
- sql_modules
- sql_modules_TSQL
- sys.sql_modules
dev_langs:
- TSQL
helpviewer_keywords:
- sys.sql_modules catalog view
ms.assetid: 23d3ccd2-f356-4d89-a2cd-bee381243f99
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: a3b841674bd7abffada31bd2baf7e0ab39686a3e
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98093075"
---
# <a name="syssql_modules-transact-sql"></a>sys.sql_modules (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  針對中為 SQL 語言定義模組的每個物件，各傳回一個資料列，包括原生編譯的純量 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 使用者定義函數。 類型 P、RF、V、TR、FN、IF、TF 和 R 的物件，各有一個相關聯的 SQL 模組。 獨立預設值，即類型 D 的物件，在這份檢視中也有 SQL 模組定義。 如需這些類型的描述，請參閱 [sys. objects](../../relational-databases/system-catalog-views/sys-objects-transact-sql.md)目錄檢視中的 **type** 資料行。  
  
 如需詳細資訊，請參閱[記憶體內部 OLTP 的純量使用者定義函數](../../relational-databases/in-memory-oltp/scalar-user-defined-functions-for-in-memory-oltp.md)。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|object_id|**int**|包含物件的物件識別碼。 在資料庫中，這是唯一的。|  
|**definition**|**nvarchar(max)**|定義這個模組的 SQL 文字。 您也可以使用 [OBJECT_DEFINITION](../../t-sql/functions/object-definition-transact-sql.md) 內建函數來取得這個值。<br /><br /> NULL = 已加密。|  
|**uses_ansi_nulls**|**bit**|模組是以 SET ANSI_NULLS ON 加以建立。<br /><br /> 如果是規則和預設值，則永遠 = 0。|  
|**uses_quoted_identifier**|**bit**|模組是以 SET QUOTED_IDENTIFIER ON 加以建立。|  
|**is_schema_bound**|**bit**|模組是以 SCHEMABINDING 選項加以建立。<br /><br /> 如果是原生編譯預存程序，一定包含 1 值。|  
|**uses_database_collation**|**bit**|1 = 結構描述繫結模組定義為了正確評估，必須依據資料庫的預設定序而定；否則為 0。 這種相依性可以防止資料庫的預設定序變更。|  
|**is_recompiled**|**bit**|程序是以 WITH RECOMPILE 選項加以建立。|  
|**null_on_null_input**|**bit**|模組宣告的目的不是為了因應任何 NULL 輸入而產生 NULL 輸出。|  
|**execute_as_principal_id**|**整數**|EXECUTE AS 資料庫主體的識別碼。<br /><br /> 在預設或 EXECUTE AS CALLER 的情況下為 NULL。<br /><br /> 如果 EXECUTE AS SELF 或 EXECUTE AS，則為指定之主體的識別碼 \<principal> 。<br /><br /> -2 = EXECUTE AS OWNER。|  
|**uses_native_compilation**|**bit**|**適用於**： [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 至 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)]。<br /><br /> 0 = 不是原生編譯<br /><br /> 1 = 是原生編譯<br /><br /> 預設值為 0。|  
|**is_inlineable**|**bit**|**適用對象**：[!INCLUDE[ssSQL15](../../includes/sssqlv15-md.md)] 及更新版本。<br/><br />指出模組是否為內嵌。 Inlineability 是以 [此處](../user-defined-functions/scalar-udf-inlining.md#inlineable-scalar-udfs-requirements)指定的條件為基礎。<br /><br /> 0 = 未內嵌<br /><br /> 1 = 內嵌。 <br /><br /> 若是純量 Udf，如果 UDF 是內嵌，此值將會是1，否則為0。 內嵌 Tvf 的值一律會是1，而所有其他模組類型的值則為0。<br />|  
|**inline_type**|**bit**|**適用對象**：[!INCLUDE[ssSQL15](../../includes/sssqlv15-md.md)] 及更新版本。<br /><br />指出目前是否已開啟模組的內嵌。 <br /><br />0 = 關閉內嵌<br /><br /> 1 = 開啟內嵌。<br /><br /> 若是純量 Udf，如果 (明確或) 隱含地開啟內嵌，此值會是1。 內嵌 Tvf 的值一律是1，而其他模組類型的值則為0。<br />|  

  
## <a name="remarks"></a>備註  
 預設條件約束（類型 D 的物件）的 SQL 運算式可在 [sys.default_constraints](../../relational-databases/system-catalog-views/sys-default-constraints-transact-sql.md) 目錄檢視中找到。 CHECK 條件約束的 SQL 運算式（類型為 C 的物件）可在 [sys.check_constraints](../../relational-databases/system-catalog-views/sys-check-constraints-transact-sql.md) 類別目錄檢視中找到。  
  
 [Sys.dm_db_uncontained_entities &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-uncontained-entities-transact-sql.md)也會說明這項資訊。  
  
## <a name="permissions"></a>權限  
 [!INCLUDE[ssCatViewPerm](../../includes/sscatviewperm-md.md)] 如需相關資訊，請參閱 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)。  
  
## <a name="examples"></a>範例  
 下列範例會傳回目前資料庫中每個模組的名稱、類型和定義。  
  
```  
SELECT sm.object_id, OBJECT_NAME(sm.object_id) AS object_name, o.type, o.type_desc, sm.definition  
FROM sys.sql_modules AS sm  
JOIN sys.objects AS o ON sm.object_id = o.object_id  
ORDER BY o.type;  
GO  
```  
  
## <a name="see-also"></a>另請參閱  
 [目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/catalog-views-transact-sql.md)   
 [物件目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/object-catalog-views-transact-sql.md)   
 [查詢 SQL Server 系統目錄常見問題](../../relational-databases/system-catalog-views/querying-the-sql-server-system-catalog-faq.md)   
 [記憶體內部 OLTP &#40;記憶體內部最佳化&#41;](../../relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization.md)  
  
  
