---
description: DROP VIEW (Transact-SQL)
title: DROP VIEW (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 10/11/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- DROP_VIEW_TSQL
- DROP VIEW
dev_langs:
- TSQL
helpviewer_keywords:
- dropping views
- DROP VIEW statement
- deleting views
- indexed views [SQL Server], removing
- views [SQL Server], removing
- removing views
ms.assetid: 03cea355-e39c-46e1-b7db-8832038669dd
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 3eafbb102cbd58d6d9684f083e82078a8f578feb
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98170390"
---
# <a name="drop-view-transact-sql"></a>DROP VIEW (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  從目前資料庫移除一或多份檢視。 您可以針對索引檢視來執行 DROP VIEW。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
-- Syntax for SQL Server, Azure SQL Database, and Azure Synapse Analytics
  
DROP VIEW [ IF EXISTS ] [ schema_name . ] view_name [ ...,n ] [ ; ]  
```  
  
```syntaxsql
-- Syntax for Parallel Data Warehouse  
  
DROP VIEW [ schema_name . ] view_name [ ; ]  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *IF EXISTS*  
 **適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 至 [目前版本](https://go.microsoft.com/fwlink/p/?LinkId=299658)、[!INCLUDE[sssds](../../includes/sssds-md.md)])。|  
  
 只有在檢視已存在時，才能有條件地將其卸除。  
  
 *schema_name*  
 這是檢視所屬的結構描述名稱。  
  
 *view_name*  
 這是要移除的檢視名稱。  
  
## <a name="remarks"></a>備註  
 當您卸除檢視時，也會從系統目錄中刪除檢視的定義和檢視的其他相關資訊。 檢視的所有權限也都會刪除。  
  
 在利用 DROP TABLE 來卸除的資料表中，您必須利用 DROP VIEW 來明確卸除任何檢視。  
  
 當針對索引檢視來執行時，DROP VIEW 會自動卸除檢視的所有索引。 若要顯示檢視的所有索引，請使用 [sp_helpindex](../../relational-databases/system-stored-procedures/sp-helpindex-transact-sql.md)。  
  
 當查詢檢視時，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 會檢查確定陳述式所參考的所有資料庫物件都存在、在陳述式的內容中有效，而且修改資料陳述式未違反任何資料完整性規則。 檢查失敗會傳回錯誤訊息。 檢查成功會將動作轉換成針對基礎資料表的動作。 如果基礎資料表或檢視在最初建立檢視之後有了改變，卸除再重建檢視可能有用。  
  
 如需判斷特定檢視相依性的詳細資訊，請參閱 [sys.sql_dependencies &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-sql-dependencies-transact-sql.md)。  
  
 如需文字檢視的詳細資訊，請參閱 [sp_helptext &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-helptext-transact-sql.md)。  
  
## <a name="permissions"></a>權限  
 需要檢視表的 **CONTROL** 權限、包含檢視之結構描述的 **ALTER** 權限，或 **db_ddladmin** 固定伺服器角色中的成員資格。  
  
## <a name="examples"></a>範例  
  
### <a name="a-drop-a-view"></a>A. 卸除檢視  
 下列範例會移除 `Reorder` 檢視。  
  
```sql
DROP VIEW IF EXISTS dbo.Reorder ;  
GO  
```  
  
## <a name="see-also"></a>另請參閱  
 [ALTER VIEW &#40;Transact-SQL&#41;](../../t-sql/statements/alter-view-transact-sql.md)   
 [CREATE VIEW &#40;Transact-SQL&#41;](../../t-sql/statements/create-view-transact-sql.md)   
 [EVENTDATA &#40;Transact-SQL&#41;](../../t-sql/functions/eventdata-transact-sql.md)   
 [sys.columns &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-columns-transact-sql.md)   
 [sys.objects &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-objects-transact-sql.md)   
 [USE &#40;Transact-SQL&#41;](../../t-sql/language-elements/use-transact-sql.md)   
 [sys.sql_expression_dependencies &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-sql-expression-dependencies-transact-sql.md)  
 
