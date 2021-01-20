---
description: 'sp_data_source_objects (Transact-sql) '
title: sp_data_source_objects |Microsoft Docs
ms.custom: ''
ms.date: 11/14/2020
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: conceptual
f1_keywords:
- sp_data_source_objects
helpviewer_keywords:
- PolyBase
ms.assetid: 48066431-fed2-4a8a-85af-ac704689e183
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 4c76026dcd24572b4d52c6b82246e5293c22e713
ms.sourcegitcommit: d8cdbb719916805037a9167ac4e964abb89c3909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/20/2021
ms.locfileid: "98596687"
---
# <a name="sp_data_source_objects-transact-sql"></a>sp_data_source_objects (Transact-sql) 

[!INCLUDE [sqlserver2019](../../includes/applies-to-version/sqlserver2019.md)]

傳回可供虛擬化的資料表物件清單。

> [!NOTE]
> 此程式是在 [SQL 2019 CU5](../../big-data-cluster/release-notes-big-data-cluster.md#cu5)中引進。

![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  

## <a name="syntax"></a>語法  

```syntaxsql
sp_data_source_objects  
         [ @data_source = ] 'data_source'
     [ , [ @object_root_name = ] 'object_root_name' ]
     [ , [ @max_search_depth = ] max_search_depth ]
     [ , [ @search_options = ] 'search_options' ]
[ ; ]
```  
  
## <a name="arguments"></a>引數  

`[ @data_source = ] 'data_source'`   
要從中取得中繼資料的外部資料源名稱。 `@data_source` 為 `sysname`。  

`[ @object_root_name = ] 'object_root_name'`   
此參數是要搜尋)  (的物件名稱根目錄。 `@object_root_name` 是 `nvarchar(max)` ，預設值是 `NULL` 。

此呼叫只會傳回以設定的值開頭的外部物件 `@object_root_name` 。

如果 ODBC 資料來源連接到關係資料庫管理系統 (RDBMS) 使用三部分名稱，則 `@object_root_name` 不能包含部分資料庫名稱。 在這些情況下，參數 `@object_root_name` 應該包含全部三個部分，而第三個部分是要搜尋的物件名稱。
> [!CAUTION]
> 由於外部資料平臺之間的差異，如果提供預設值，某些平臺不會傳回任何結果 `NULL` 。 部分視為 `NULL` 缺乏篩選。 例如，如果為提供，Oracle RDMBS 將不會傳回結果 `NULL` `@object_root_name` 。

`[ @max_search_depth = ] max_search_depth`   
此值會指定要搜尋之部分) 超過的深度 (的最大深度 `@object_root_name` 。 `@max_search_depth` 是 `int` ，預設值是1。

例如，如果是 `@max_search_depth` 1，且 `@object_root_name` 是 SQL Server 資料庫的名稱，則會傳回資料庫中包含的架構。

`@max_search_depth`如果是 `NULL` 目錄或架構，則的會傳回的相關資訊 `@object_root_name` （如果存在）和非空白。

`[ @search_options = ] 'search_options'`   
`search_options`參數是 Nvarchar (max) ，預設值是 `NULL` 。

此參數不會使用，但未來可能會執行。

## <a name="result-sets"></a>結果集

| 資料行名稱 | 資料類型 | 描述 |
|--|--|--|
| Object_Type | nvarchar(200) | 物件的類型 (範例：資料表或資料庫) 。 |
| OBJECT_NAME | nvarchar(max) | 物件的完整名稱。 使用後端特定的引號字元進行轉義。 |
| OBJECT_LEAF_NAME | nvarchar(max) | 不合格的物件名稱。 |
| TABLE_LOCATION | nvarchar(max) | 可用於 CREATE EXTERNAL TABLE 語句的有效資料表位置字串。 `NULL`如果不適用則會是。 |
  
## <a name="permissions"></a>權限

要求 ALTER ANY EXTERNAL DATA SOURCE 權限。  

## <a name="remarks"></a>備註  

SQL Server 實例必須安裝  [PolyBase](../../relational-databases/polybase/polybase-guide.md) 功能。

這個預存程式支援的連接器：

- SQL Server
- Oracle
- Teradata
- MongoDB
- CosmosDB

預存程式不支援泛型 ODBC dta 來源連接器。

空白與非空白的概念與 ODBC 驅動程式和函式的行為[ `SQLTables` 有關。](../native-client-odbc-api/sqltables.md) 非空白表示物件包含資料表，而不是資料列。 例如，空的架構在 SQL Server 中不包含任何資料表。 空的資料庫包含 Teradata 內沒有任何資料表。

物件類型是由外部資料源的 ODBC 驅動程式所決定。 每個外部資料源都會決定哪些是符合資料表的資格。 這可能包含資料庫物件（例如 TeraData 中的函式）或 Oracle 中的同義字。 PolyBase 無法連接至某些 ODBC 物件作為外部資料表，因此 TABLE_LOCATION 資料行中不會有值。 儘管如此，其中一個物件的存在可能會使資料庫或架構變成非空白。

使用 `sp_data_source_objects` 和 [`sp_data_source_table_columns`](sp-data-source-table-columns.md) 來探索外部物件。 這些系統預存程式會傳回可虛擬化之資料表的架構。 Azure Data Studio 使用這兩個預存程式來支援 [資料虛擬化](../../azure-data-studio/extensions/data-virtualization-extension.md)。 使用 [sp_data_source_table_columns](sp-data-source-table-columns.md) 來探索以 SQL Server 資料類型表示的外部資料表架構。

### <a name="data-source-type-specific-remarks"></a>資料來源類型特定的備註

* Teradata

   Teradata 系統檢視不會使用資料列層級安全性 (RLS) ，因此使用者可以看到其無法查詢的資料表是否存在。

* MongoDB

   某些舊版的 MongoDB 會將列出所有資料庫的能力限制為類似系統管理員的使用者。 沒有此許可權的使用者可能會在嘗試使用 null 來執行此程式時，收到驗證錯誤 `@object_root_name` 。

## <a name="examples"></a>範例  

### <a name="sql-server"></a>SQL Server

下列範例會傳回所有資料庫、架構和資料表/views

```sql
DECLARE @data_source SYSNAME = N'ExternalDataSourceName';
DECLARE @object_root_name NVARCHAR(MAX) = NULL;
DECLARE @max_search_depth INT = 3;
EXEC sp_data_source_objects @data_source, @object_root_name, @max_search_depth;
```

| Object_Type | OBJECT_NAME | OBJECT_LEAF_NAME | TABLE_LOCATION |
|--|--|--|--|
| DATABASE | "資料庫" | [資料庫] | NULL |
| SCHEMA | "database"。dbo | dbo | NULL |
| TABLE | "database"。dbo "."客戶服務部 | 客戶 | [資料庫]。[dbo]。客戶服務部 |
| TABLE | "database"。dbo "."加值稅 | item | [資料庫]。[dbo]。加值稅 |
| TABLE | "database"。dbo "."美國 | 國家 | [資料庫]。[dbo]。美國 |

下列範例會傳回所有資料庫

```sql
DECLARE @data_source SYSNAME = N'ExternalDataSourceName';
DECLARE @object_root_name NVARCHAR(MAX) = NULL;
EXEC sp_data_source_objects @data_source, @object_root_name;
```

| Object_Type | OBJECT_NAME | OBJECT_LEAF_NAME | TABLE_LOCATION |
|--|--|--|--|
| DATABASE | "UserDatabase" | UserDatabase | NULL |
| DATABASE | "master" | master | NULL |
| DATABASE | msdb | msdb | NULL |
| DATABASE | 臨時 | tempdb | NULL |
| DATABASE | "資料庫" | [資料庫] | NULL |

下列範例會傳回資料庫中的所有架構。

```sql
DECLARE @data_source SYSNAME = N'ExternalDataSourceName'; 
DECLARE @object_root_name NVARCHAR(MAX) = N'[database]'; 
EXEC sp_data_source_objects @data_source, @object_root_name;
```

| Object_Type | OBJECT_NAME | OBJECT_LEAF_NAME | TABLE_LOCATION |
|--|--|--|--|
| SCHEMA | "database"。dbo | dbo | NULL |
| SCHEMA | "database"。INFORMATION_SCHEMA」 | INFORMATION_SCHEMA | NULL |
| SCHEMA | "database"。config.sys | sys | NULL |

下列範例會傳回架構中的所有資料表。 

```sql
DECLARE @data_source SYSNAME = N'ExternalDataSourceName'; 
DECLARE @object_root_name NVARCHAR(MAX) = N'[database].[dbo]'; 
EXEC sp_data_source_objects @data_source, @object_root_name;
```

| Object_Type | OBJECT_NAME | OBJECT_LEAF_NAME | TABLE_LOCATION |
|--|--|--|--|
| TABLE | "database"。dbo "."客戶服務部 | 客戶 | [資料庫]。[dbo]。客戶服務部 |
| TABLE | "database"。dbo "."加值稅 | item | [資料庫]。[dbo]。加值稅 |
| TABLE | "database"。dbo "."美國 | 國家 | [資料庫]。[dbo]。美國 |
| TABLE | "database"。dbo "."線上訂單 | 訂單 | [資料庫]。[dbo]。線上訂單 |
| TABLE | "database"。dbo "."成為 | 組件 | [資料庫]。[dbo]。成為 |

### <a name="oracle"></a>Oracle

下列範例會傳回完整的架構和資料表、函數、views 等等。

```sql
DECLARE @data_source SYSNAME = N'ExternalDataSourceName'; 
DECLARE @object_root_name NVARCHAR(MAX) = N'[OracleObjectRoot]'; 
DECLARE @max_search_depth INT = 2; 
EXEC sp_data_source_objects @data_source, @object_root_name, @max_search_depth;
```

| Object_Type | OBJECT_NAME | OBJECT_LEAF_NAME | TABLE_LOCATION |
|--|--|--|--|
| VIEW | "SYS". "ALL_SQLSET_STATEMENTS」 | ALL_SQLSET_STATEMENTS | [ORACLEOBJECTROOT].[SYS]。[ALL_SQLSET_STATEMENTS] |
| SYSTEM TABLE | "SYS". "啟動程式 $ " | 啟動程式 $ | [ORACLEOBJECTROOT].[SYS]。[啟動程式 $] |
| SYNONYM | 「公用」。ALL_ALL_TABLES」 | ALL_ALL_TABLES | NULL |
| SCHEMA | "資料庫" | [資料庫] | NULL |
| TABLE | "database"。客戶服務部 | 客戶 | [ORACLEOBJECTROOT].[資料庫]。客戶服務部 |

### <a name="teradata"></a>Teradata

下列範例會傳回所有資料庫和資料表、函數、views 等等。

```SQL
DECLARE @data_source SYSNAME = N'ExternalDataSourceName';
DECLARE @object_root_name NVARCHAR(MAX) = NULL;
DECLARE @max_search_depth INT = 2;
EXEC sp_data_source_objects @data_source, @object_root_name, @max_search_depth;
```

| Object_Type | OBJECT_NAME | OBJECT_LEAF_NAME | TABLE_LOCATION |  
|--|--|--|--|
| FUNCTION | "SYSLIB"."ExtractRoles" | ExtractRoles | NULL |  
| SYSTEM TABLE | "DBC"。UDTCast" | UDTCast | [DBC]。[UDTCast] |  
| TYPE | "SYSUDTLIB"."STL | XML | NULL |  
| DATABASE | "資料庫" | [資料庫] | NULL |
| TABLE | "database"。客戶服務部 | 客戶 | [資料庫]。客戶服務部 |  

### <a name="mongo-db"></a>Mongo DB

下列範例會傳回所有資料庫和資料表。

```SQL
DECLARE @data_source SYSNAME = N'ExternalDataSourceName';
DECLARE @object_root_name NVARCHAR(MAX) = NULL;
DECLARE @max_search_depth INT = 2;
EXEC sp_data_source_objects @data_source, @object_root_name, @max_search_depth;
```

| Object_Type | OBJECT_NAME | OBJECT_LEAF_NAME | TABLE_LOCATION |
|--|--|--|--|
| DATABASE | "資料庫" | [資料庫] | NULL |
| TABLE | "database"。客戶服務部 | 客戶 | [資料庫]。客戶服務部 |
| TABLE | "database"。加值稅 | item | [資料庫]。加值稅 |
| TABLE | "database"。美國 | 國家 | [資料庫]。美國 |
| TABLE | "database"。線上訂單 | 訂單 | [資料庫]。線上訂單 |

## <a name="see-also"></a>另請參閱

- [sp_data_source_columns](./sp-data-source-table-columns.md)   
- [CREATE EXTERNAL TABLE AS SELECT (Transact-SQL)](../../t-sql/statements/create-external-table-as-select-transact-sql.md)
- [CREATE EXTERNAL TABLE (Transact-SQL)](../../t-sql/statements/create-external-table-transact-sql.md)
- [適用於 Azure Data Studio 的資料虛擬化延伸模組](../../azure-data-studio/extensions/data-virtualization-extension.md)   
- [開始使用 PolyBase](../polybase/polybase-guide.md)   
- [系統預存程序 &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/system-stored-procedures-transact-sql.md)