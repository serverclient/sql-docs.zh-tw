---
description: ALTER SCHEMA (Transact-SQL)
title: ALTER SCHEMA (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/09/2020
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- ALTER SCHEMA
- ALTER_SCHEMA_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- objects [SQL Server], transferring
- transferring objects between schemas
- ALTER SCHEMA statement
- schemas [SQL Server], modifying
- modifying schemas
ms.assetid: 0a760138-460e-410a-a3c1-d60af03bf2ed
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 73cd35c7f05d39d7ec6930be6273dc22c16f9c56
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98092052"
---
# <a name="alter-schema-transact-sql"></a>ALTER SCHEMA (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  在結構描述之間傳送安全性實體。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
-- Syntax for SQL Server and Azure SQL Database  
  
ALTER SCHEMA schema_name   
   TRANSFER [ <entity_type> :: ] securable_name   
[;]  
  
<entity_type> ::=  
    {  
    Object | Type | XML Schema Collection  
    }  
```  
  
```syntaxsql
-- Syntax for Azure Synapse Analytics and Parallel Data Warehouse  
  
ALTER SCHEMA schema_name   
   TRANSFER [ OBJECT :: ] securable_name   
[;]  
```  
  

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *schema_name*  
 這是安全性實體要移至的目前資料庫中的結構描述名稱。 不可以是 SYS 或 INFORMATION_SCHEMA。  
  
 \<entity_type>  
 這是變更擁有者的實體類別。 物件是預設值。  
  
 *securable_name*  
 這是要移至結構描述的結構描述範圍內含安全性實體之一部分或兩部分名稱。  
  
## <a name="remarks"></a>備註  
 使用者和結構描述完全分隔。  
  
 ALTER SCHEMA 只能用來移動相同資料庫中結構描述之間的安全性實體。 若要變更或卸除結構描述內的安全性實體，請使用該安全性實體特定的 ALTER 或 DROP 陳述式。  
  
 如果 *securable_name* 使用一部分名稱，則會使用目前使用的名稱解析規則來尋找該安全性實體。  
  
 當安全性實體移至新結構描述時，將卸除與這個安全性實體相關聯的所有權限。 如果已明確設定安全性實體的擁有者，該擁有者將保持不變。 如果安全性實體的擁有者已設為 SCHEMA OWNER，該擁有者將維持為 SCHEMA OWNER；不過，在移動之後，SCHEMA OWNER 將解析為新結構描述的擁有者。 新擁有者的 principal_id 會是 NULL。  
  
 移動預存程序、函數、檢視或觸發程序，不會變更 [sys.sql_modules](../../relational-databases/system-catalog-views/sys-sql-modules-transact-sql.md) 目錄檢視或使用 [OBJECT_DEFINITION](../../t-sql/functions/object-definition-transact-sql.md) 內建函數取得之定義資料行中對應物件的結構描述名稱 (若有的話)。 因此，我們建議您不要利用 ALTER SCHEMA 來移動這些物件類型。 相反地，請卸除物件，再利用它的新結構描述來重新建立物件。  
  
 移動資料表或同義字之類的物件，不會自動更新指向這個物件的參考。 您必須手動修改任何參考已傳輸之物件的物件。 例如，如果您移動了資料表，而且觸發程序參考該資料表，您就必須修改觸發程序來反映新的結構描述名稱。 在移動物件之前，請利用 [sys.sql_expression_dependencies](../../relational-databases/system-catalog-views/sys-sql-expression-dependencies-transact-sql.md) 來列出其相依性。  

 若要使用 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 來變更資料表的結構描述，請在 [物件總管] 中，以滑鼠右鍵按一下資料表，然後按一下 [設計]。 按下 **F4** 開啟 [屬性] 視窗。 在 [結構描述] 方塊中，選取新的結構描述。  
 
 ALTER SCHEMA 會使用結構描述層級鎖定。
  
> [!CAUTION]  
>  [!INCLUDE[ssCautionUserSchema](../../includes/sscautionuserschema-md.md)]  
  
## <a name="permissions"></a>權限  
 若要從另一個結構描述傳送安全性實體，目前使用者必須對安全性實體 (而非結構描述) 具有 CONTROL 權限，並對目標結構描述具有 ALTER 權限。  
  
 如果安全性實體上有 EXECUTE AS OWNER 規格，且擁有者設為 SCHEMA OWNER，則該使用者也必須對目標結構描述的擁有者具有 IMPERSONATION 權限。  
  
 移動要傳送的安全性實體時，將會卸除與該安全性實體相關聯的所有權限。  
  
## <a name="examples"></a>範例  
  
### <a name="a-transferring-ownership-of-a-table"></a>A. 傳送資料表的擁有權  
 下列範例會將 `Person` 結構描述中的 `Address` 資料表傳送到 'HumanResources` 結構描述中，以修改 `HumanResources` 結構描述。  
  
```sql  
USE AdventureWorks2012;  
GO  
ALTER SCHEMA HumanResources TRANSFER Person.Address;  
GO  
```  
  
### <a name="b-transferring-ownership-of-a-type"></a>B. 傳送某個類型的擁有權  
 下列範例會在 `Production` 結構描述中建立某個類型，然後將此類型傳送至 `Person` 結構描述。  
  
```sql  
USE AdventureWorks2012;  
GO  
  
CREATE TYPE Production.TestType FROM [VARCHAR](10) NOT NULL ;  
GO  
  
-- Check the type owner.  
SELECT sys.types.name, sys.types.schema_id, sys.schemas.name  
    FROM sys.types JOIN sys.schemas   
        ON sys.types.schema_id = sys.schemas.schema_id   
    WHERE sys.types.name = 'TestType' ;  
GO  
  
-- Change the type to the Person schema.  
ALTER SCHEMA Person TRANSFER type::Production.TestType ;  
GO  
  
-- Check the type owner.  
SELECT sys.types.name, sys.types.schema_id, sys.schemas.name  
    FROM sys.types JOIN sys.schemas   
        ON sys.types.schema_id = sys.schemas.schema_id   
    WHERE sys.types.name = 'TestType' ;  
GO  
```  
  
## <a name="examples-sssdwfull-and-sspdw"></a>範例：[!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 和 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
### <a name="c-transferring-ownership-of-a-table"></a>C. 傳送資料表的擁有權  
 下列範例會在 `dbo` 結構描述中建立資料表 `Region`、建立 `Sales` 結構描述，然後將 `Region` 資料表從 `dbo` 結構描述移動至 `Sales` 結構描述。  
  
```sql  
CREATE TABLE dbo.Region   
    (Region_id INT NOT NULL,  
    Region_Name CHAR(5) NOT NULL)  
WITH (DISTRIBUTION = REPLICATE);  
GO  
  
CREATE SCHEMA Sales;  
GO  
  
ALTER SCHEMA Sales TRANSFER OBJECT::dbo.Region;  
GO  
```  
  
## <a name="see-also"></a>另請參閱  
 [CREATE SCHEMA &#40;Transact-SQL&#41;](../../t-sql/statements/create-schema-transact-sql.md)   
 [DROP SCHEMA &#40;Transact-SQL&#41;](../../t-sql/statements/drop-schema-transact-sql.md)   
 [EVENTDATA &#40;Transact-SQL&#41;](../../t-sql/functions/eventdata-transact-sql.md)  
  
  

