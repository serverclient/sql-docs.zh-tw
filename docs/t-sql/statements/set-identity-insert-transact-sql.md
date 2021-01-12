---
title: SET IDENTITY_INSERT (Transact-SQL) | Microsoft Docs
description: SET IDENTITY_INSERT 陳述式的 Transact-SQL 參考。 當設定為 ON 時，會允許將明確值插入資料表的識別欄位。
ms.date: 06/10/2016
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- SET IDENTITY_INSERT
- SET_IDENTITY_INSERT_TSQL
- IDENTITY_INSERT_TSQL
- IDENTITY_INSERT
dev_langs:
- TSQL
helpviewer_keywords:
- IDENTITY_INSERT option
- SET IDENTITY_INSERT statement
- identity values [SQL Server], explicit values
- identity columns [SQL Server], explicit values
ms.assetid: a5dd49f2-45c7-44a8-b182-e0a5e5c373ee
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: = azuresqldb-current||>= sql-server-2016||>= sql-server-linux-2017||=azure-sqldw-latest
ms.openlocfilehash: 0a2d8e7cb4625d4b1ddcd35473e16f9bcad1d810
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98093364"
---
# <a name="set-identity_insert-transact-sql"></a>SET IDENTITY_INSERT (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa.md)]

允許將明確的值插入資料表的識別欄位中。  

 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
  
SET IDENTITY_INSERT [ [ database_name . ] schema_name . ] table_name { ON | OFF }  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *database_name*  
 這是指定的資料表所在的資料庫名稱。  
  
 *schema_name*  
 這是資料表所屬的結構描述名稱。  
  
 *table_name*  
 這是含識別欄位之資料表的名稱。  
  
## <a name="remarks"></a>備註  
 不論何時，工作階段中只能有一份資料表將 IDENTITY_INSERT 屬性設為 ON。 如果已有資料表的這個屬性設為 ON，又針對另一份資料表發出 SET IDENTITY_INSERT ON 陳述式，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會傳回一則錯誤訊息，指出 SET IDENTITY_INSERT 已設為 ON，且會報告設為 ON 所針對的資料表。  
  
 如果輸入的值大於資料表目前的識別值，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會自動利用新插入的值來作為目前的識別值。  
  
 SET IDENTITY_INSERT 的設定是在執行階段進行設定，而不是在剖析階段進行設定。  
  
## <a name="permissions"></a>權限  
 使用者必須擁有資料表，或對資料表擁有 ALTER 權限。  
  
## <a name="examples"></a>範例  
 下列範例會建立含識別欄位的資料表，且會顯示如何利用 `SET IDENTITY_INSERT` 設定來填滿 `DELETE` 陳述式所造成的識別值的間距。  
  
```sql
USE AdventureWorks2012;  
GO  
-- Create tool table.  
CREATE TABLE dbo.Tool(  
   ID INT IDENTITY NOT NULL PRIMARY KEY,   
   Name VARCHAR(40) NOT NULL  
);  
GO  
-- Inserting values into products table.  
INSERT INTO dbo.Tool(Name)   
VALUES ('Screwdriver')  
        , ('Hammer')  
        , ('Saw')  
        , ('Shovel');  
GO  
  
-- Create a gap in the identity values.  
DELETE dbo.Tool  
WHERE Name = 'Saw';  
GO  
  
SELECT *   
FROM dbo.Tool;  
GO  
  
-- Try to insert an explicit ID value of 3;  
-- should return an error:
-- An explicit value for the identity column in table 'AdventureWorks2012.dbo.Tool' can only be specified when a column list is used and IDENTITY_INSERT is ON.
INSERT INTO dbo.Tool (ID, Name) VALUES (3, 'Garden shovel');  
GO  
-- SET IDENTITY_INSERT to ON.  
SET IDENTITY_INSERT dbo.Tool ON;  
GO  
  
-- Try to insert an explicit ID value of 3.  
INSERT INTO dbo.Tool (ID, Name) VALUES (3, 'Garden shovel');  
GO  
  
SELECT *   
FROM dbo.Tool;  
GO  
-- Drop products table.  
DROP TABLE dbo.Tool;  
GO  
```  
  
## <a name="see-also"></a>另請參閱  
 [CREATE TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/create-table-transact-sql.md)   
 [IDENTITY &#40;屬性&#41; &#40;Transact-SQL&#41;](../../t-sql/statements/create-table-transact-sql-identity-property.md)   
 [SCOPE_IDENTITY &#40;Transact-SQL&#41;](../../t-sql/functions/scope-identity-transact-sql.md)   
 [INSERT &#40;Transact-SQL&#41;](../../t-sql/statements/insert-transact-sql.md)   
 [SET 陳述式 &#40;Transact-SQL&#41;](../../t-sql/statements/set-statements-transact-sql.md)  
  
  
