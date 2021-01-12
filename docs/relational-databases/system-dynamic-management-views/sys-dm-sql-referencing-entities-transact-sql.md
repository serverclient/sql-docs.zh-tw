---
description: sys.dm_sql_referencing_entities (Transact-SQL)
title: sys.dm_sql_referencing_entities (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.dm_sql_referencing_entities
- dm_sql_referencing_entities_TSQL
- sys.dm_sql_referencing_entities_TSQL
- dm_sql_referencing_entities
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_sql_referencing_entities dynamic management function
ms.assetid: c16f8f0a-483f-4feb-842e-da90426045ae
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 109791409e050201f64a7f7f9b207ccac58372b9
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98093981"
---
# <a name="sysdm_sql_referencing_entities-transact-sql"></a>sys.dm_sql_referencing_entities (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  針對在目前資料庫中依據名稱參考其他使用者定義實體的每個實體，各傳回一個資料列。 在另一個實體（稱為 *參考實體*）的保存 SQL 運算式 *中，依* 名稱顯示兩個實體之間的相依性時，會建立兩個實體之間的相依性。 例如，如果使用者定義型別 (UDT) 指定為受參考的實體，這個函數就會傳回在定義中依據名稱參考該類型的每個使用者自訂實體。 此函數不會傳回其他資料庫中可能參考指定實體的實體。 這個函數必須在 master 資料庫的內容中執行，以便傳回伺服器層級 DDL 觸發程序當做參考實體。  
  
 您可以使用這個動態管理函數來回報下列在目前資料庫中參考指定實體的實體類型：  
  
-   結構描述繫結或非結構描述繫結的實體  
  
-   資料庫層級 DDL 觸發程序  
  
-   伺服器層級 DDL 觸發程序  
  
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本)、[!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)]。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```  
sys.dm_sql_referencing_entities (  
    ' schema_name.referenced_entity_name ' , ' <referenced_class> ' )  
  
<referenced_class> ::=  
{  
    OBJECT  
  | TYPE  
  | XML_SCHEMA_COLLECTION  
  | PARTITION_FUNCTION  
}  
```  
  
## <a name="arguments"></a>引數  
 `schema_name.referenced_entity_name` 這是受參考實體的名稱。  
  
 `schema_name` 是必要項目，但受參考類別為 PARTITION_FUNCTION 的情況除外。  
  
 `schema_name.referenced_entity_name` 是 **Nvarchar (517)**。  
  
 `<referenced_class> ::= { OBJECT  | TYPE | XML_SCHEMA_COLLECTION | PARTITION_FUNCTION }` 這是受參考實體的類別。 每個陳述式只能指定一個類別。  
  
 `<referenced_class>` 是 **Nvarchar** (60) 。  
  
## <a name="table-returned"></a>傳回的資料表  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|referencing_schema_name|**sysname**|參考實體所屬的結構描述。 可為 Null。<br /><br /> 若為資料庫層級與伺服器層級 DDL 觸發程序，則為 NULL。|  
|referencing_entity_name|**sysname**|參考實體的名稱。 不可為 Null。|  
|referencing_id|**int**|參考實體的識別碼。 不可為 Null。|  
|referencing_class|**tinyint**|參考實體的類別。 不可為 Null。<br /><br /> 1 = 物件<br /><br /> 12 = 資料庫層級 DDL 觸發程序<br /><br /> 13 = 伺服器層級 DDL 觸發程序|  
|referencing_class_desc|**nvarchar(60)**|參考實體之類別的描述。<br /><br /> OBJECT<br /><br /> DATABASE_DDL_TRIGGER<br /><br /> SERVER_DDL_TRIGGER|  
|is_caller_dependent|**bit**|指出在執行階段發生之受參考實體識別碼的解析，因為它會相依於呼叫端的結構描述。<br /><br /> 1 = 參考實體可能會參考此實體。不過，受參考實體識別碼的解析是呼叫端相依而且無法判斷。 只有預存程序的非結構描述繫結參考、擴充預存程序，或在 EXECUTE 陳述式內部呼叫的使用者定義函數，才會發生這個事件。<br /><br /> 0 = 受參考的實體不是呼叫端相依。|  
  
## <a name="exceptions"></a>例外狀況  
 在下列任何情況下，都會傳回空的結果集：  
  
-   已指定系統物件。  
  
-   指定的實體不存在目前的資料庫中。  
  
-   指定的實體沒有參考任何實體。  
  
-   傳遞了無效的參數。  
  
 當指定的受參考實體是已編號的預存程序時，就會傳回錯誤。  
  
## <a name="remarks"></a>備註  
 下表將列出建立並維護相依性資訊的實體類型。 系統不會針對規則、預設值、暫存資料表、暫存預存程序或系統物件建立或維護相依性資訊。  
  
|實體類型|參考實體|受參考的實體|  
|-----------------|------------------------|-----------------------|  
|Table|是*|是|  
|檢視|是|是|  
|[!INCLUDE[tsql](../../includes/tsql-md.md)] 預存程序**|是|是|  
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
  
 \* 只有當資料表參考 [!INCLUDE[tsql](../../includes/tsql-md.md)] 計算資料行的定義、CHECK 條件約束或 DEFAULT 條件約束中的模組、使用者定義型別或 XML 架構集合時，才會將資料表視為參考實體進行追蹤。  
  
 ** 所包含之整數值大於 1 的編號預存程序不會當做參考或受參考的實體進行追蹤。  
  
## <a name="permissions"></a>權限  
  
### <a name="sskatmai---sssql11"></a>[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] - [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)]  
  
-   需要所參考物件的 CONTROL 權限。 當受參考實體為資料分割函數時，便需要資料庫的 CONTROL 權限。  
  
-   需要 sys.dm_sql_referencing_entities 的 SELECT 許可權。 根據預設，SELECT 權限會授與 public。  
  
### <a name="sssql14---sscurrent"></a>[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] - [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)]  
  
-   不需要所參考物件的權限。 如果使用者有其中一部分參考實體的 VIEW DEFINITION，則會傳回部分結果。  
  
-   如果參考實體是物件，需要物件的 VIEW DEFINITION。  
  
-   當參考實體為資料庫層級 DDL 觸發程序時，需要資料庫的 VIEW DEFINITION。  
  
-   當參考實體為伺服器層級 DDL 觸發程序時，需要伺服器的 VIEW ANY DEFINITION。  
  
## <a name="examples"></a>範例  
  
### <a name="a-returning-the-entities-that-refer-to-a-given-entity"></a>A. 傳回參考給定實體的實體  
 下列範例會傳回目前資料庫中參考指定資料表的實體。  
  
```sql  
USE AdventureWorks2012;  
GO  
SELECT referencing_schema_name, referencing_entity_name, referencing_id, referencing_class_desc, is_caller_dependent  
FROM sys.dm_sql_referencing_entities ('Production.Product', 'OBJECT');  
GO  
```  
  
### <a name="b-returning-the-entities-that-refer-to-a-given-type"></a>B. 傳回參考給定類型的實體  
 下列範例會傳回參考別名類型 `dbo.Flag` 的實體。 結果集會顯示有兩個預存程序使用這個類型。 此 `dbo.Flag` 類型也用於資料表中數個數據行的定義中， `HumanResources.Employee` 但因為該類型不在資料表的計算資料行、CHECK 條件約束或 DEFAULT 條件約束的定義中，所以不會傳回資料表的任何資料列 `HumanResources.Employee` 。  
  
```sql  
USE AdventureWorks2012;  
GO  
SELECT referencing_schema_name, referencing_entity_name, referencing_id, referencing_class_desc, is_caller_dependent  
FROM sys.dm_sql_referencing_entities ('dbo.Flag', 'TYPE');  
GO  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
 ```
 referencing_schema_name referencing_entity_name   referencing_id referencing_class_desc is_caller_dependent  
 ----------------------- -------------------------  ------------- ---------------------- -------------------  
 HumanResources          uspUpdateEmployeeHireInfo  1803153469    OBJECT_OR_COLUMN       0  
 HumanResources          uspUpdateEmployeeLogin     1819153526    OBJECT_OR_COLUMN       0  
 (2 row(s) affected)`  
 ``` 
 
## <a name="see-also"></a>另請參閱  
 [sys.dm_sql_referenced_entities &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-sql-referenced-entities-transact-sql.md)   
 [sys.sql_expression_dependencies &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-sql-expression-dependencies-transact-sql.md)  
  
  
