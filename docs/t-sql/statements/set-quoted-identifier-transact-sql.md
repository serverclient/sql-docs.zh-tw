---
description: SET QUOTED_IDENTIFIER (Transact-SQL)
title: SET QUOTED_IDENTIFIER (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 02/21/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- QUOTED_IDENTIFIER_TSQL
- SET_QUOTED_IDENTIFIER_TSQL
- SET QUOTED_IDENTIFIER
- QUOTED_IDENTIFIER
dev_langs:
- TSQL
helpviewer_keywords:
- delimited identifiers [SQL Server]
- identifiers [SQL Server], delimited
- QUOTED_IDENTIFIER option
- quoted identifiers
- ISO delimited identifiers rules
- SET QUOTED_IDENTIFIER statement
ms.assetid: 10f66b71-9241-4a3a-9292-455ae7252565
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 232c287564593eee0deab4ae37dc13c36d2d689b
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98100739"
---
# <a name="set-quoted_identifier-transact-sql"></a>SET QUOTED_IDENTIFIER (Transact-SQL)

[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

讓 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 遵照有關分隔識別碼和常值字串之引號的 ISO 規則。 用雙引號定界的識別碼可以是 [!INCLUDE[tsql](../../includes/tsql-md.md)] 保留關鍵字，也可以包含 [!INCLUDE[tsql](../../includes/tsql-md.md)] 的識別碼語法規則通常不接受的字元。

![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)

## <a name="syntax"></a>語法

```syntaxsql
-- Syntax for SQL Server, Azure SQL Database and serverless SQL pool in Azure Synapse Analytics

SET QUOTED_IDENTIFIER { ON | OFF }
```

```syntaxsql
-- Syntax for Azure Synapse Analytics and Parallel Data Warehouse

SET QUOTED_IDENTIFIER ON
```

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="remarks"></a>備註
當 `SET QUOTED_IDENTIFIER` 為 ON (預設值) 時，識別碼可由雙引號分隔 (" ")，且常值必須由單引號分隔 (' ')。 用雙引號來分隔的所有字串都會解譯為物件識別碼。 因此，附加引號的識別碼不需要遵照 [!INCLUDE[tsql](../../includes/tsql-md.md)] 的識別碼規則。 它們可以是保留關鍵字，也可以包括 [!INCLUDE[tsql](../../includes/tsql-md.md)] 識別碼通常不接受的字元。 文字字串運算式不能用雙引號來分隔；您必須用單引號來括住文字字串。 如果單引號 (') 是常值字串的一部分，則可以用兩個單引號 (") 來代表。 當資料庫中的物件名稱使用保留關鍵字時，`SET QUOTED_IDENTIFIER` 必須為 ON。

當 `SET QUOTED_IDENTIFIER` 為 OFF 時，識別碼不能附加引號，且必須遵循所有 [!INCLUDE[tsql](../../includes/tsql-md.md)] 識別碼規則。 如需詳細資訊，請參閱＜ [Database Identifiers](../../relational-databases/databases/database-identifiers.md)＞。 文字可以用單引號或雙引號來分隔。 如果用雙引號來分隔文字字串，字串便可以包含內嵌的單引號，如撇號。

> [!NOTE]
> QUOTED_IDENTIFIER 不會影響以中括弧 ([ ]) 分隔的識別碼。

當要建立或變更計算資料行上的索引或索引檢視表時，`SET QUOTED_IDENTIFIER` 必須為 ON。 如果 `SET QUOTED_IDENTIFIER` 為 OFF，則在具有計算資料行索引的資料表，或索引檢視表的資料表中，CREATE、UPDATE、INSERT 和 DELETE 陳述式皆會失敗。 如需含索引檢視表和計算資料行索引的必要 SET 選項設定詳細資訊，請參閱[使用 SET 陳述式時的考量](../../t-sql/statements/set-statements-transact-sql.md#considerations-when-you-use-the-set-statements)。

當要建立篩選索引時，`SET QUOTED_IDENTIFIER` 必須為 ON。

當叫用 XML 資料類型方法時，`SET QUOTED_IDENTIFIER` 必須為 ON。

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client ODBC 驅動程式和 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client OLE DB 提供者在連線時，會自動將 QUOTED_IDENTIFIER 設為 ON。 您可以在 ODBC 資料來源、ODBC 連接屬性或 OLE DB 連接屬性中設定這個項目。 起始於 DB-Library 應用程式的連接之 SET QUOTED_IDENTIFIER 預設值是 OFF。

當建立資料表時，一律會在資料表的中繼資料中，將 QUOTED IDENTIFIER 選項儲存成 ON，即使建立資料表時，將選項設成 OFF，也是如此。

當建立預存程序時，會擷取 SET QUOTED_IDENTIFIER 和 SET ANSI_NULLS 設定，這個預存程序的後續引動過程都會使用這些設定。

當在預存程序內執行時，不會變更 SET QUOTED_IDENTIFIER 的設定。

當 `SET ANSI_DEFAULTS` 為 ON 時，則 QUOTED_IDENTIFIER 也同時為 ON。

另外，`SET QUOTED_IDENTIFIER` 也對應於 [ALTER DATABASE](../../t-sql/statements/alter-database-transact-sql.md) 的 QUOTED_IDENTIFIER 設定。

`SET QUOTED_IDENTIFIER` 只會在 [!INCLUDE[tsql](../../includes/tsql-md.md)] 剖析時生效，且只會影響剖析，而不會影響查詢最佳化或查詢執行。

針對最上層的隨選批次，剖析會使用工作階段針對 QUOTED_IDENTIFIER 的目前設定開始。 在剖析批次的同時，任何出現 `SET QUOTED_IDENTIFIER` 的地方，都會從該點之後變更剖析的行為，並針對工作階段儲存該設定。 因此在剖析和執行批次之後，工作階段的 QUOTED_IDENTIFER 設定即會根據批次中最後出現的 `SET QUOTED_IDENTIFIER` 進行設定。

位於預存程序中的靜態 [!INCLUDE[tsql](../../includes/tsql-md.md)] 會針對建立或修改預存程序的批次使用有效 QUOTED_IDENTIFIER 設定來進行剖析。 `SET QUOTED_IDENTIFIER` 作為靜態 [!INCLUDE[tsql](../../includes/tsql-md.md)] 出現在預存程序的主體時沒有任何效果。

針對使用 `sp_executesql` 或 `exec()` 的巢狀批次，剖析會使用工作階段的 QUOTED_IDENTIFIER 設定開始。 如果巢狀批次位於預存程序之中，則剖析會使用預存程序的 QUOTED_IDENTIFIER 設定啟動。 在剖析巢狀批次的同時，任何出現 `SET QUOTED_IDENTIFIER` 的地方，都會從該點之後變更剖析的行為，但工作階段的 QUOTED_IDENTIFIER 將不會更新。

若要檢視此設定的目前設定，請執行下列查詢：

```sql
DECLARE @QUOTED_IDENTIFIER VARCHAR(3) = 'OFF';
IF ( (256 & @@OPTIONS) = 256 ) 
SET @QUOTED_IDENTIFIER = 'ON';

SELECT @QUOTED_IDENTIFIER AS QUOTED_IDENTIFIER;
```

## <a name="permissions"></a>權限
需要 `PUBLIC` 角色中的成員資格。

## <a name="examples"></a>範例

### <a name="a-using-the-quoted-identifier-setting-and-reserved-word-object-names"></a>A. 使用引號識別碼設定及保留字物件名稱

下列範例會顯示 `SET QUOTED_IDENTIFIER` 設定必須是 `ON`，且資料表名稱中的關鍵字必須用雙引號括住，才能建立和使用含保留字名稱的物件。

```sql
SET QUOTED_IDENTIFIER OFF
GO

-- Create statement fails.
CREATE TABLE "select" ("identity" INT IDENTITY NOT NULL, "order" INT NOT NULL);
GO

SET QUOTED_IDENTIFIER ON;
GO

-- Create statement succeeds.
CREATE TABLE "select" ("identity" INT IDENTITY NOT NULL, "order" INT NOT NULL);
GO

SELECT "identity","order"
FROM "select"
ORDER BY "order";
GO

DROP TABLE "SELECT";
GO

SET QUOTED_IDENTIFIER OFF;
GO
```

### <a name="b-using-the-quoted-identifier-setting-with-single-and-double-quotation-marks"></a>B. 搭配單引號和雙引號來使用引號識別碼設定

 下列範例會顯示在 `SET QUOTED_IDENTIFIER` 設為 `ON` 和 `OFF` 的字串運算式中，單引號和雙引號的使用方式。

```sql
SET QUOTED_IDENTIFIER OFF;
GO

USE AdventureWorks2012;
IF EXISTS(SELECT TABLE_NAME FROM INFORMATION_SCHEMA.TABLES
    WHERE TABLE_NAME = 'Test')
    DROP TABLE dbo.Test;
GO
USE AdventureWorks2012;
CREATE TABLE dbo.Test (ID INT, String VARCHAR(30)) ;
GO

-- Literal strings can be in single or double quotation marks.
INSERT INTO dbo.Test VALUES (1, "'Text in single quotes'");
INSERT INTO dbo.Test VALUES (2, '''Text in single quotes''');
INSERT INTO dbo.Test VALUES (3, 'Text with 2 '''' single quotes');
INSERT INTO dbo.Test VALUES (4, '"Text in double quotes"');
INSERT INTO dbo.Test VALUES (5, """Text in double quotes""");
INSERT INTO dbo.Test VALUES (6, "Text with 2 """" double quotes");
GO

SET QUOTED_IDENTIFIER ON;
GO

-- Strings inside double quotation marks are now treated
-- as object names, so they cannot be used for literals.
INSERT INTO dbo."Test" VALUES (7, 'Text with a single '' quote');
GO

-- Object identifiers do not have to be in double quotation marks
-- if they are not reserved keywords.
SELECT ID, String
FROM dbo.Test;
GO

DROP TABLE dbo.Test;
GO

SET QUOTED_IDENTIFIER OFF;
GO
```

[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
 ID          String
 ----------- ------------------------------
 1           'Text in single quotes'
 2           'Text in single quotes'
 3           Text with 2 '' single quotes
 4           "Text in double quotes"
 5           "Text in double quotes"
 6           Text with 2 "" double quotes
 7           Text with a single ' quote
 ```

## <a name="see-also"></a>另請參閱
[CREATE DATABASE](../../t-sql/statements/create-database-transact-sql.md)    
[CREATE DEFAULT](../../t-sql/statements/create-default-transact-sql.md)    
[CREATE PROCEDURE](../../t-sql/statements/create-procedure-transact-sql.md)    
[CREATE RULE](../../t-sql/statements/create-rule-transact-sql.md)    
[CREATE TABLE](../../t-sql/statements/create-table-transact-sql.md)    
[CREATE TRIGGER](../../t-sql/statements/create-trigger-transact-sql.md)    
[CREATE VIEW](../../t-sql/statements/create-view-transact-sql.md)    
[資料類型](../../t-sql/data-types/data-types-transact-sql.md)    
[EXECUTE](../../t-sql/language-elements/execute-transact-sql.md)    
[SELECT](../../t-sql/queries/select-transact-sql.md)    
[SET 陳述式](../../t-sql/statements/set-statements-transact-sql.md)    
[SET ANSI_DEFAULTS](../../t-sql/statements/set-ansi-defaults-transact-sql.md)    
[sp_rename](../../relational-databases/system-stored-procedures/sp-rename-transact-sql.md)    
[資料庫識別碼](../../relational-databases/databases/database-identifiers.md)
