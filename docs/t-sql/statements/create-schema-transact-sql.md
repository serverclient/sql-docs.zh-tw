---
description: CREATE SCHEMA (Transact-SQL)
title: CREATE SCHEMA (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 12/01/2016
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- CREATE_SCHEMA_TSQL
- SCHEMA
- CREATE SCHEMA
- SCHEMA_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- owners [SQL Server], schemas
- table creation [SQL Server], CREATE SCHEMA statement
- permissions [SQL Server], schemas
- schemas [SQL Server], creating
- CREATE SCHEMA statement
ms.assetid: df74fc36-20da-4efa-b412-c4e191786695
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 83699c45a36f353d20b69ba6b9a4101608bd2be8
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98092005"
---
# <a name="create-schema-transact-sql"></a>CREATE SCHEMA (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  在目前資料庫中建立結構描述。 CREATE SCHEMA 異動也可在新結構描述中建立資料表與檢視，以及設定這些物件的 GRANT、DENY 或 REVOKE 權限。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
-- Syntax for SQL Server and Azure SQL Database  
  
CREATE SCHEMA schema_name_clause [ <schema_element> [ ...n ] ]  
  
<schema_name_clause> ::=  
    {  
    schema_name  
    | AUTHORIZATION owner_name  
    | schema_name AUTHORIZATION owner_name  
    }  
  
<schema_element> ::=   
    {   
        table_definition | view_definition | grant_statement |   
        revoke_statement | deny_statement   
    }  
```  
  
```syntaxsql
-- Syntax for Azure Synapse Analytics and Parallel Data Warehouse  
  
CREATE SCHEMA schema_name [ AUTHORIZATION owner_name ] [;]  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *schema_name*  
 這是資料庫中結構描述的識別名稱。  
  
 AUTHORIZATION *owner_name*  
 指定將擁有結構描述之資料庫層級主體的名稱。 這個主體可能擁有其他結構描述，且可能不使用目前結構描述做為其預設結構描述。  
  
 *table_definition*  
 指定在結構描述中建立資料表的 CREATE TABLE 陳述式。 執行這個陳述式的主體必須具有目前資料庫的 CREATE TABLE 權限。  
  
 *view_definition*  
 指定在結構描述中建立檢視表的 CREATE VIEW 陳述式。 執行這個陳述式的主體必須具有目前資料庫的 CREATE VIEW 權限。  
  
 *grant_statement*  
 指定授與新結構描述之外任何安全性實體之權限的 GRANT 陳述式。  
  
 *revoke_statement*  
 指定撤銷新結構描述以外任何安全性實體之權限的 REVOKE 陳述式。  
  
 *deny_statement*  
 指定拒絕新結構描述以外任何安全性實體之權限的 DENY 陳述式。  
  
## <a name="remarks"></a>備註  
  
> [!NOTE]  
>  允許包含 CREATE SCHEMA AUTHORIZATION 但不指定名稱之陳述式的目的，只是為了與舊版相容。 陳述式不會導致錯誤，但是不會建立結構描述。  
  
 CREATE SCHEMA 可以在單一陳述式中建立結構描述、其所包含的資料表和檢視表，以及任何安全性實體的 GRANT、REVOKE 或 DENY 權限。 這個陳述式必須以分隔批次來執行。 由 CREATE SCHEMA 陳述式建立的物件，會建立在將要建立之結構描述的內部。  
  
 CREATE SCHEMA 交易不可部分完成。 如果執行 CREATE SCHEMA 陳述式期間發生任何錯誤，將不會建立任何指定的安全性實體，也不會授與任何權限。  
  
 除了參考其他檢視表的檢視表之外，要用 CREATE SCHEMA 建立的安全性實體可以按任何順序排列。 在這個情況下，被參考的檢視必須建立於參考它的檢視之前。  
  
 因此，GRANT 陳述式可以在物件本身建立之前授與物件的權限，或者 CREATE VIEW 陳述式可以出現在建立檢視表所參考之資料表的 CREATE TABLE 陳述式之前。 此外，CREATE TABLE 陳述式可以宣告稍後在 CREATE SCHEMA 陳述式中所定義之資料表的外部索引鍵。  
  
> [!NOTE]  
>  CREATE SCHEMA 陳述式內支援 DENY 和 REVOKE。 DENY 和 REVOKE 子句會按其出現於 CREATE SCHEMA 陳述式中的順序來執行。  
  
 執行 CREATE SCHEMA 的主體可以指定其他資料庫主體做為要建立之結構描述的擁有者。 此時需要本主題稍後「權限」一節所描述的其他權限。  
  
 新結構描述的擁有者是下列資料庫層級主體之一：資料庫使用者、資料庫角色或應用程式角色。 在結構描述中建立的物件由結構描述擁有者擁有，其在 **sys.objects** 中具有 NULL **principal_id**。 結構描述包含物件的擁有權可以轉移到任何資料庫層級主體，但結構描述擁有者恆保有結構描述中物件的 CONTROL 權限。  
  
> [!CAUTION]  
>  [!INCLUDE[ssCautionUserSchema](../../includes/sscautionuserschema-md.md)]  
  
 **建立隱含結構描述和使用者**  
  
 在某些情況下，使用者可以使用資料庫，但是沒有資料庫使用者帳戶 (資料庫中的資料庫主體)。 此錯誤會於下列情況發生：  
  
-   登入具有 **CONTROL SERVER** 權限。  
  
-   Windows 使用者沒有個別的資料庫使用者帳戶 (資料庫中的資料庫主體)，但是可以具有資料庫使用者帳戶 (Windows 群組的資料庫主體) 之 Windows 群組的成員身分來存取資料庫。  
  
 當不具有資料庫使用者帳戶的使用者建立物件但沒有指定現有結構描述時，將會為該使用者在資料庫中自動建立資料庫主體和預設結構描述。 建立的資料庫主體與結構描述將會擁有與使用者在連線到 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 時所使用的名稱相同的名稱 ([!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 驗證登入名稱或 Windows 使用者名稱)。  
  
 必須有這個行為，才能允許基於 Windows 群組的使用者建立和擁有物件。 不過，它可能會導致意外建立結構描述和使用者。 為了避免隱含建立使用者和結構描述，請盡可能地明確建立資料庫主體並指派預設結構描述。 或者當在資料庫中建立物件時，使用二或三部份的物件名稱，明確指定現有的結構描述。  

> [!NOTE]
>  無法在 [!INCLUDE[ssSDS_md](../../includes/sssds-md.md)] 上隱含建立 Azure Active Directory 使用者。 由於從外部提供者建立 Azure AD 使用者必須檢查使用者在 AAD 中的狀態，因此建立使用者將會失敗，並傳回錯誤 2760：**指定的結構描述名稱 "\<user_name@domain>" 不存在，或您沒有使用它的權限。** 以及錯誤 2759：**CREATE SCHEMA 由於先前的錯誤而失敗。** 若要解決這些錯誤，請先從外部提供者建立 Azure AD 使用者，再重新執行建立物件的陳述式。
 
  
## <a name="deprecation-notice"></a>取代通知  
 目前支援不指定結構描述名稱之 CREATE SCHEMA 陳述式的目的，是為了與舊版相容。 這些陳述式實際上並不會在資料庫中建立結構描述，但是會建立資料表和檢視表，以及授與權限。 主體不需要 CREATE SCHEMA 權限來執行這個舊格式的 CREATE SCHEMA，因為不會建立任何結構描述。 未來的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 版本將移除這項功能。  
  
## <a name="permissions"></a>權限  
 需要資料庫的 CREATE SCHEMA 權限。  
  
 若要建立 CREATE SCHEMA 陳述式中指定的物件，使用者必須具有對應的 CREATE 權限。  
  
 若要指定其他使用者做為建立之結構描述的擁有者，呼叫者必須具有該使用者的 IMPERSONATE 權限。 如果指定資料庫角色做為擁有者，呼叫端必須具有下列項目之一：角色的成員資格或角色的 ALTER 權限。  
  
> [!NOTE]  
>  為了語法與舊版相容，不會檢查任何 CREATE SCHEMA 權限，因為不會建立任何結構描述。  
  
## <a name="examples"></a>範例  
  
### <a name="a-creating-a-schema-and-granting-permissions"></a>A. 建立結構描述及授與權限  
 下列範例會建立結構描述 `Sprockets`，這是由包含資料表 `Annik` 的 `NineProngs` 所擁有。 陳述式授與 `SELECT` 給 `Mandar`，拒絕 `SELECT` 給 `Prasanna`。 請注意，`Sprockets` 和 `NineProngs` 是在單一陳述式中建立。  
  
```sql  
USE AdventureWorks2012;  
GO  
CREATE SCHEMA Sprockets AUTHORIZATION Annik  
    CREATE TABLE NineProngs (source int, cost int, partnumber int)  
    GRANT SELECT ON SCHEMA::Sprockets TO Mandar  
    DENY SELECT ON SCHEMA::Sprockets TO Prasanna;  
GO   
```  
  
## <a name="examples-sssdwfull-and-sspdw"></a>範例：[!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 和 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
### <a name="b-creating-a-schema-and-a-table-in-the-schema"></a>B. 建立結構描述並在結構描述中建立資料表  
 下例範例會建立名為 `Sales` 的結構描述，然後在該結構描述中建立 `Sales.Region` 資料表。  
  
```sql  
CREATE SCHEMA Sales;  
GO
  
CREATE TABLE Sales.Region   
(Region_id INT NOT NULL,  
Region_Name CHAR(5) NOT NULL)  
WITH (DISTRIBUTION = REPLICATE);  
GO  
```  
  
### <a name="c-setting-the-owner-of-a-schema"></a>C. 設定結構描述的擁有者  
 下列範例會建立由 `Mary` 擁有的 `Production` 結構描述。  
  
```sql  
CREATE SCHEMA Production AUTHORIZATION [Contoso\Mary];  
GO  
```  
  
## <a name="see-also"></a>另請參閱  
 [ALTER SCHEMA &#40;Transact-SQL&#41;](../../t-sql/statements/alter-schema-transact-sql.md)   
 [DROP SCHEMA &#40;Transact-SQL&#41;](../../t-sql/statements/drop-schema-transact-sql.md)   
 [GRANT &#40;Transact-SQL&#41;](../../t-sql/statements/grant-transact-sql.md)   
 [DENY &#40;Transact-SQL&#41;](../../t-sql/statements/deny-transact-sql.md)   
 [REVOKE &#40;Transact-SQL&#41;](../../t-sql/statements/revoke-transact-sql.md)   
 [CREATE VIEW &#40;Transact-SQL&#41;](../../t-sql/statements/create-view-transact-sql.md)   
 [EVENTDATA &#40;Transact-SQL&#41;](../../t-sql/functions/eventdata-transact-sql.md)   
 [sys.schemas &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/schemas-catalog-views-sys-schemas.md)   
 [建立資料庫結構描述](../../relational-databases/security/authentication-access/create-a-database-schema.md)  
  
  


