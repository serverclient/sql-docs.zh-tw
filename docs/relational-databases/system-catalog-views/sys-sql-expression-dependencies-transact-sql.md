---
description: sys.sql_expression_dependencies (Transact-SQL)
title: sys.sql_expression_dependencies (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.sql_expression_dependencies
- sql_expression_dependencies_TSQL
- sql_expression_dependencies
- sys.sql_expression_dependencies_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.sql_expression_dependencies catalog view
ms.assetid: 78a218e4-bf99-4a6a-acbf-ff82425a5946
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: fb846962b3790d171cd2eea7ee5cdcfebba3f89b
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98101771"
---
# <a name="syssql_expression_dependencies-transact-sql"></a>sys.sql_expression_dependencies (Transact-SQL)
[!INCLUDE [sql-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdbmi-asa-pdw.md)]

  針對在目前資料庫中使用者自訂實體的每個依據名稱相依性，各包含一個資料列。 這包括原生編譯的純量使用者定義函數與其他模組之間的相依性 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。 在另一個實體（稱為 *參考實體*）的保存 SQL 運算式 *中，依* 名稱顯示兩個實體之間的相依性時，會建立兩個實體之間的相依性。 例如，在某個檢視的定義中參考資料表時，該檢視 (參考實體) 就會相依於資料表 (受參考的實體)。 如果資料表遭卸除，檢視便無法使用。  
  
 如需詳細資訊，請參閱[記憶體內部 OLTP 的純量使用者定義函數](../../relational-databases/in-memory-oltp/scalar-user-defined-functions-for-in-memory-oltp.md)。  
  
 您可以使用這個目錄檢視來報告下列實體的相依性資訊：  
  
-   結構描述繫結的實體。  
  
-   非結構描述繫結的實體。  
  
-   跨資料庫與跨伺服器的實體。 雖然系統會報告實體名稱，但是不會解析實體識別碼。  
  
-   結構描述繫結實體的資料行層級相依性。 您可以使用 [sys.dm_sql_referenced_entities](../../relational-databases/system-dynamic-management-views/sys-dm-sql-referenced-entities-transact-sql.md)來傳回非架構系結物件的資料行層級相依性。  
  
-   伺服器層級 DDL 觸發程序 (在 master 資料庫的內容時)。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|referencing_id|**int**|參考實體的識別碼。 不可為 Null。|  
|referencing_minor_id|**int**|當參考實體是資料行時，就是資料行識別碼，否則便是 0。 不可為 Null。|  
|referencing_class|**tinyint**|參考實體的類別。<br /><br /> 1 = 物件或資料行<br /><br /> 12 = 資料庫 DDL 觸發程序<br /><br /> 13 = 伺服器 DDL 觸發程序<br /><br /> 不可為 Null。|  
|referencing_class_desc|**nvarchar(60)**|參考實體之類別的描述。<br /><br /> OBJECT_OR_COLUMN<br /><br /> DATABASE_DDL_TRIGGER<br /><br /> SERVER_DDL_TRIGGER<br /><br /> 不可為 Null。|  
|is_schema_bound_reference|**bit**|1 = 受參考的實體是結構描述繫結。<br /><br /> 0 = 受參考的實體非結構描述繫結。<br /><br /> 不可為 Null。|  
|referenced_class|**tinyint**|受參考實體的類別。<br /><br /> 1 = 物件或資料行<br /><br /> 6 = 類型<br /><br /> 10 = XML 結構描述集合<br /><br /> 21 = 資料分割函數<br /><br /> 不可為 Null。|  
|referenced_class_desc|**nvarchar(60)**|受參考實體之類別的描述。<br /><br /> OBJECT_OR_COLUMN<br /><br /> TYPE<br /><br /> XML_SCHEMA_COLLECTION<br /><br /> PARTITION_FUNCTION<br /><br /> 不可為 Null。|  
|referenced_server_name|**sysname**|受參考實體之伺服器的名稱。<br /><br /> 這個資料行會因透過指定有效的四部分名稱所達成的跨伺服器相依性而擴展。 如需多部分名稱的詳細資訊，請參閱 transact-sql [&#41;&#40;Transact-sql 語法慣例 ](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)。<br /><br /> 若為參考了實體的非結構描述繫結實體，但沒有指定四部分名稱，則為 NULL。<br /><br /> 架構系結的實體必須是 Null，因為它們必須位於相同的資料庫中，因此只能使用兩部分 (架構來定義 *。物件*) 名稱。|  
|referenced_database_name|**sysname**|受參考實體之資料庫的名稱。<br /><br /> 這個資料行會因透過指定有效的三部分或四部分名稱所達成的跨資料庫或跨伺服器參考而擴展。<br /><br /> 在使用一部分或兩部分名稱指定時，若為非結構描述繫結參考，則為 NULL。<br /><br /> 架構系結的實體必須是 Null，因為它們必須位於相同的資料庫中，因此只能使用兩部分 (架構來定義 *。物件*) 名稱。|  
|referenced_schema_name|**sysname**|受參考實體所屬的結構描述。<br /><br /> 若為參考了實體的非結構描述繫結參考，但沒有指定結構描述名稱，則為 NULL。<br /><br /> 若為結構描述繫結的參考，則永遠不會是 NULL；因為您必須使用兩部分名稱來定義和參考結構描述繫結的實體。|  
|referenced_entity_name|**sysname**|受參考實體的名稱。 不可為 Null。|  
|referenced_id|**int**|受參考實體的識別碼。 架構系結的參考中，此資料行的值永遠不會是 Null。 對於跨伺服器和跨資料庫的參考，此資料行的值一律是 Null。<br /><br /> 無法判斷識別碼時，若為資料庫中的參考，則為 NULL。 若為非結構描述繫結參考，便無法在下列情況中解析識別碼：<br /><br /> 受參考實體不存在資料庫中。<br /><br /> 受參考實體的結構描述會相依於呼叫端的結構描述，而且會在執行階段解析。 在此情況下，is_caller_dependent 會設定為 1。|  
|referenced_minor_id|**int**|當參考實體是資料行時，就是受參考資料行的識別碼，否則便是 0。 不可為 Null。<br /><br /> 當資料行在參考實體中由名稱所識別，或者父實體用於 SELECT * 陳述式時，受參考的實體就是資料行。|  
|is_caller_dependent|**bit**|指出在執行階段發生之受參考實體的結構描述繫結。因此，實體識別碼的解析會相依於呼叫端的結構描述。 當受參考的實體為預存程序、擴充預存程序，或在 EXECUTE 陳述式內部呼叫的非結構描述繫結使用者定義函數時，就會發生這個事件。<br /><br /> 1 = 受參考的實體是呼叫端相依，而且在執行階段解析。 在此情況下，referenced_id 是 NULL。<br /><br /> 0 = 受參考的實體識別碼不是呼叫端相依。<br /><br /> 若為結構描述繫結參考，以及明確指定結構描述名稱的跨資料庫和跨伺服器參考，則一律是 0。 例如，採用 `EXEC MyDatabase.MySchema.MyProc` 格式的實體參考與呼叫端無關。 不過，採用 `EXEC MyDatabase..MyProc` 格式的參考即與呼叫端相關。|  
|is_ambiguous|**bit**|指出參考不明確，而且可以在執行時間解析成使用者自訂函數、使用者定義型別 (UDT) ，或 **xml** 類型之資料行的 xquery 參考。<br /><br /> 例如，假設 `SELECT Sales.GetOrder() FROM Sales.MySales` 陳述式定義於預存程序中。 在執行該預存程序之前，不知道 `Sales.GetOrder()` 是 `Sales` 結構描述中的使用者自訂函數，還是名為 `Sales`、類型是 UDT 而且具有名為 `GetOrder()` 之方法的資料行。<br /><br /> 1 = 參考模糊不清。<br /><br /> 0 = 參考不會模糊不清，或者在呼叫檢視時，可成功繫結實體。<br /><br /> 若為結構描述繫結的參考，則一律是 0。|  
  
## <a name="remarks"></a>備註  
 下表將列出建立並維護相依性資訊的實體類型。 系統不會針對規則、預設值、暫存資料表、暫存預存程序或系統物件建立或維護相依性資訊。  

> [!NOTE]
> Azure Synapse Analytics 和平行處理資料倉儲支援資料表、視圖、篩選統計資料，以及這份清單中的 Transact-sql 預存程式實體類型。  只有資料表、視圖和篩選的統計資料，才會建立和維護相依性資訊。  
  
|實體類型|參考實體|受參考的實體|  
|-----------------|------------------------|-----------------------|  
|Table|是*|是|  
|檢視|是|是|  
|已篩選的索引|是**|否|  
|篩選的統計資料|是**|否|  
|[!INCLUDE[tsql](../../includes/tsql-md.md)] 預存程式 * * _|是|是|  
|CLR 預存程序|否|是|  
|[!INCLUDE[tsql](../../includes/tsql-md.md)] 使用者定義函數|是|是|  
|CLR 使用者定義函數|否|是|  
|CLR 觸發程序 (DML 和 DDL)|否|否|  
|[!INCLUDE[tsql](../../includes/tsql-md.md)] DML 觸發程序|是|否|  
|[!INCLUDE[tsql](../../includes/tsql-md.md)] 資料庫層級 DDL 觸發程序|是|否|  
|[!INCLUDE[tsql](../../includes/tsql-md.md)] 伺服器層級 DDL 觸發程序|是|否|  
|擴充預存程序|否|是|  
|佇列|否|是|  
|同義字|否|是|  
|類型 (別名和 CLR 使用者定義型別)|否|是|  
|XML 結構描述集合|否|是|  
|分割區函數|否|是|  
  
 \_ 只有當資料表參考 [!INCLUDE[tsql](../../includes/tsql-md.md)] 計算資料行的定義、CHECK 條件約束或 DEFAULT 條件約束中的模組、使用者定義型別或 XML 架構集合時，才會將資料表視為參考實體進行追蹤。  
  
 ** 篩選述詞中使用的每一個資料行都會當做參考實體來追蹤。  
  
 * * _ 以整數值大於1的編號預存程式不會追蹤為參考或受參考的實體。  
  
## <a name="permissions"></a>權限  
 需要資料庫的 VIEW DEFINITION 權限和資料庫之 sys.sql_expression_dependencies 的 SELECT 權限。 根據預設，SELECT 權限只授與 db_owner 固定資料庫角色的成員。 當 SELECT 和 VIEW DEFINITION 權限授與其他使用者時，被授與者就可以檢視資料庫中的所有相依性。  
  
## <a name="examples"></a>範例  
  
### <a name="a-returning-entities-that-are-referenced-by-another-entity"></a>A. 傳回另一個實體所參考的實體  
 下列範例會傳回 `Production.vProductAndDescription` 檢視中所參考的資料表和資料行。 這個檢視相依於 `referenced_entity_name` 和 `referenced_column_name` 資料行中傳回的實體 (資料表和資料行)。  
  
```  
USE AdventureWorks2012;  
GO  
SELECT OBJECT_NAME(referencing_id) AS referencing_entity_name,   
    o.type_desc AS referencing_desciption,   
    COALESCE(COL_NAME(referencing_id, referencing_minor_id), '(n/a)') AS referencing_minor_id,   
    referencing_class_desc,  
    referenced_server_name, referenced_database_name, referenced_schema_name,  
    referenced_entity_name,   
    COALESCE(COL_NAME(referenced_id, referenced_minor_id), '(n/a)') AS referenced_column_name,  
    is_caller_dependent, is_ambiguous  
FROM sys.sql_expression_dependencies AS sed  
INNER JOIN sys.objects AS o ON sed.referencing_id = o.object_id  
WHERE referencing_id = OBJECT_ID(N'Production.vProductAndDescription');  
GO  
  
```  
  
### <a name="b-returning-entities-that-reference-another-entity"></a>B. 傳回參考另一個實體的實體  
 下列範例會傳回參考 `Production.Product` 資料表的實體。 在 `referencing_entity_name` 資料行中傳回的實體相依於 `Product` 資料表。  
  
```  
USE AdventureWorks2012;  
GO  
SELECT OBJECT_SCHEMA_NAME ( referencing_id ) AS referencing_schema_name,  
    OBJECT_NAME(referencing_id) AS referencing_entity_name,   
    o.type_desc AS referencing_desciption,   
    COALESCE(COL_NAME(referencing_id, referencing_minor_id), '(n/a)') AS referencing_minor_id,   
    referencing_class_desc, referenced_class_desc,  
    referenced_server_name, referenced_database_name, referenced_schema_name,  
    referenced_entity_name,   
    COALESCE(COL_NAME(referenced_id, referenced_minor_id), '(n/a)') AS referenced_column_name,  
    is_caller_dependent, is_ambiguous  
FROM sys.sql_expression_dependencies AS sed  
INNER JOIN sys.objects AS o ON sed.referencing_id = o.object_id  
WHERE referenced_id = OBJECT_ID(N'Production.Product');  
GO  
  
```  
  
### <a name="c-returning-cross-database-dependencies"></a>C. 傳回跨資料庫相依性  
 下列範例會傳回所有跨資料庫相依性。 該範例會先在資料庫 `db1` 和 `db2` 中，建立參考資料表的資料庫 `db3` 以及兩個預存程序。 然後會查詢 `sys.sql_expression_dependencies` 資料表以報告程序和資料表之間的跨資料庫相依性。 請注意，在受參考實體 `referenced_schema_name` 的 `t3` 資料行中會傳回 NULL，因為在程序定義中，沒有指定該實體的結構描述名稱。  
  
```  
CREATE DATABASE db1;  
GO  
USE db1;  
GO  
CREATE PROCEDURE p1 AS SELECT _ FROM db2.s1.t1;  
GO  
CREATE PROCEDURE p2 AS  
    UPDATE db3..t3  
    SET c1 = c1 + 1;  
GO  
SELECT OBJECT_NAME (referencing_id),referenced_database_name,   
    referenced_schema_name, referenced_entity_name  
FROM sys.sql_expression_dependencies  
WHERE referenced_database_name IS NOT NULL;  
GO  
USE master;  
GO  
DROP DATABASE db1;  
GO  
  
```  
  
## <a name="see-also"></a>另請參閱  
 [sys.dm_sql_referenced_entities &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-sql-referenced-entities-transact-sql.md)   
 [sys.dm_sql_referencing_entities &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-sql-referencing-entities-transact-sql.md)  
  
  
