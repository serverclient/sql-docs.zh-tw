---
description: DROP PROCEDURE (Transact-SQL)
title: DROP PROCEDURE (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 05/11/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- DROP PROCEDURE
- DROP_PROCEDURE_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- removing stored procedures
- dropping procedure groups
- deleting stored procedures
- deleting procedure groups
- DROP PROCEDURE statement
- dropping stored procedures
- stored procedures [SQL Server], removing
- removing procedure groups
ms.assetid: 1c2d7235-7b9b-4336-8f17-429e7d82c2c3
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 3915bf9f19396c8cc55179861e23052f25886b20
ms.sourcegitcommit: 713e5a709e45711e18dae1e5ffc190c7918d52e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98689037"
---
# <a name="drop-procedure-transact-sql"></a>DROP PROCEDURE (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  從 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 中目前的資料庫移除一個或多個預存程序或程序群組。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
-- Syntax for SQL Server and Azure SQL Database  
  
DROP { PROC | PROCEDURE } [ IF EXISTS ] { [ schema_name. ] procedure } [ ,...n ]  
```  
  
```syntaxsql
-- Syntax for Azure Synapse Analytics and Parallel Data Warehouse  
  
DROP { PROC | PROCEDURE } { [ schema_name. ] procedure_name }  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *IF EXISTS*  
 **適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 至 [目前版本](../../sql-server/what-s-new-in-sql-server-2016.md))。  
  
 只有當程序已經存在時，才會有條件地將它卸除。  
  
 *schema_name*  
 程序所屬之結構描述的名稱。 不能指定伺服器名稱或資料庫名稱。  
  
 *procedure*  
 要移除的預存程序或預存程序群組的名稱。 無法卸除編碼程序群組內的個別程序；會卸除整個程序群組。  
  
## <a name="best-practices"></a>最佳做法  
 在移除任何預存程序之前，請先檢查相依物件並對應地修改這些物件。 卸除預存程序可能在這些物件未更新的情況下，造成相依物件和指令碼失敗。 如需詳細資訊，請參閱[檢視預存程序的相依性](../../relational-databases/stored-procedures/view-the-dependencies-of-a-stored-procedure.md)  
  
## <a name="metadata"></a>中繼資料  
 若要顯示現有程序的清單，查詢 **sys.objects** 目錄檢視。 若要顯示程序定義，請查詢 **sys.sql_modules** 目錄檢視。  
  
## <a name="security"></a>安全性  
  
### <a name="permissions"></a>權限  
 需要程序的 **CONTROL** 權限，或程序所屬結構描述的 **ALTER** 權限，或 **db_ddladmin** 固定伺服器角色的成員資格。  
  
## <a name="examples"></a>範例  
 下列範例會移除目前資料庫中的 `dbo.uspMyProc` 預存程序。  
  
```sql  
DROP PROCEDURE dbo.uspMyProc;  
GO  
```  
  
 下列範例會移除目前資料庫中的數個預存程序。  
  
```sql  
DROP PROCEDURE dbo.uspGetSalesbyMonth, dbo.uspUpdateSalesQuotes, dbo.uspGetSalesByYear;  
```  
  
 下列範例會移除 `dbo.uspMyProc` 預存程序，前提是如果它存在但不會因為程序不存在而造成錯誤。 這是 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 中的新語法。  
  
```sql  
DROP PROCEDURE IF EXISTS dbo.uspMyProc;  
GO  
```  
  
  
## <a name="see-also"></a>另請參閱  
 [ALTER PROCEDURE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-procedure-transact-sql.md)   
 [CREATE PROCEDURE &#40;Transact-SQL&#41;](../../t-sql/statements/create-procedure-transact-sql.md)   
 [sys.objects &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-objects-transact-sql.md)   
 [sys.sql_modules &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-sql-modules-transact-sql.md)   
 [刪除預存程序](../../relational-databases/stored-procedures/delete-a-stored-procedure.md)  
