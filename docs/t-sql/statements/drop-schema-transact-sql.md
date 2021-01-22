---
description: DROP SCHEMA (Transact-SQL)
title: DROP SCHEMA (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 05/11/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- DROP SCHEMA
- DROP_SCHEMA_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- deleting schemas
- schemas [SQL Server], removing
- DROP SCHEMA statement
- dropping schemas
- removing schemas
ms.assetid: 874aa29e-c8ad-41e4-a672-900fdc58f1f6
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 334b2e378a9eab8693b2edcc32b677f1e3989e7b
ms.sourcegitcommit: 713e5a709e45711e18dae1e5ffc190c7918d52e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98689165"
---
# <a name="drop-schema-transact-sql"></a>DROP SCHEMA (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  從資料庫中移除結構描述。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql  
-- Syntax for SQL Server and Azure SQL Database  
  
DROP SCHEMA  [ IF EXISTS ] schema_name  
```  
  

```syntaxsql  
-- Syntax for Azure Synapse Analytics and Parallel Data Warehouse  
  
DROP SCHEMA schema_name  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *IF EXISTS*  
 **適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 至 [目前版本](../../sql-server/what-s-new-in-sql-server-2016.md))。  
  
 只有在結構描述已存在時，才能有條件地將其卸除。  
  
 *schema_name*  
 這是資料庫中結構描述的識別名稱。  
  
## <a name="remarks"></a>備註  
 要卸除的結構描述不能包含任何物件。 如果該結構描述包含物件，DROP 陳述式會失敗。  
  
 您可以在 [sys.schemas](../../relational-databases/system-catalog-views/schemas-catalog-views-sys-schemas.md) 目錄檢視中，看到有關結構描述的資訊。  
  
 **警告** [!INCLUDE[ssCautionUserSchema](../../includes/sscautionuserschema-md.md)]  
  
## <a name="permissions"></a>權限  
 需要結構描述的 CONTROL 權限或資料庫的 ALTER ANY SCHEMA 權限。  
  
## <a name="examples"></a>範例  
 下列範例是從單一 `CREATE SCHEMA` 陳述式開始。 這個陳述式會建立結構描述 `Sprockets` (由 `Krishna` 擁有) 和資料表 `Sprockets.NineProngs`，然後將 `SELECT` 權限授與 `Anibal`，並拒絕將 `SELECT` 權限授與 `Hung-Fu`。  
  
```sql  
CREATE SCHEMA Sprockets AUTHORIZATION Krishna   
    CREATE TABLE NineProngs (source INT, cost INT, partnumber INT)  
    GRANT SELECT TO Anibal   
    DENY SELECT TO [Hung-Fu];  
GO  
```  
  
 下列陳述式會卸除該結構描述。 請注意，您必須先卸除該結構描述包含的資料表。  
  
```sql  
DROP TABLE Sprockets.NineProngs;  
DROP SCHEMA Sprockets;  
GO  
```  
  
  
## <a name="see-also"></a>另請參閱  
 [CREATE SCHEMA &#40;Transact-SQL&#41;](../../t-sql/statements/create-schema-transact-sql.md)   
 [ALTER SCHEMA &#40;Transact-SQL&#41;](../../t-sql/statements/alter-schema-transact-sql.md)   
 [DROP SCHEMA (Transact-SQL)](../../t-sql/statements/drop-schema-transact-sql.md)   
 [EVENTDATA &#40;Transact-SQL&#41;](../../t-sql/functions/eventdata-transact-sql.md)