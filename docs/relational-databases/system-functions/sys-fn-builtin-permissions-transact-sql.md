---
description: sys.fn_builtin_permissions (Transact-SQL)
title: sys.fn_builtin_permissions (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 12/16/2016
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- fn_builtin_permissions
- sys.fn_builtin_permissions_TSQL
- fn_builtin_permissions_TSQL
- sys.fn_builtin_permissions
dev_langs:
- TSQL
helpviewer_keywords:
- compact permissions types
- viewing permission hierarchy
- hierarchies [SQL Server], permissions
- built-in permissions hierarchy [SQL Server]
- fn_builtin_permissions function
- permissions [SQL Server], hierarchy
- displaying permission hierarchy
- sys.fn_builtin_permissions function
ms.assetid: 704b1ad3-3534-4cf3-aff4-9fb70064b6cc
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 6db232d77311b4d9e82bfe0afda8b14a4822d29f
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98172640"
---
# <a name="sysfn_builtin_permissions-transact-sql"></a>sys.fn_builtin_permissions (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  傳回伺服器之內建權限階層的描述。 `sys.fn_builtin_permissions` 只能在和上呼叫 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] ，並且會傳回擁有權限，不論目前的平臺是否支援它們。 大部分的權限適用於所有平台，但某些無法適用。 例如，無法在 SQL Database 上授與伺服器層級許可權。 如需哪些平臺支援每個許可權的相關資訊，請參閱 [&#40;資料庫引擎&#41;的許可權 ](../../relational-databases/security/permissions-database-engine.md)。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```  
  
sys.fn_builtin_permissions ( [ DEFAULT | NULL ]  
    | empty_string | '<securable_class>' } )  
  
<securable_class> ::=   
      APPLICATION ROLE | ASSEMBLY | ASYMMETRIC KEY | AVAILABILITY GROUP  
    | CERTIFICATE | CONTRACT | DATABASE | DATABASE SCOPED CREDENTIAL    
    | ENDPOINT | FULLTEXT CATALOG | FULLTEXT STOPLIST | LOGIN      
    | MESSAGE TYPE | OBJECT | REMOTE SERVICE BINDING | ROLE | ROUTE    
    | SCHEMA | SEARCH PROPERTY LIST | SERVER | SERVER ROLE | SERVICE    
    | SYMMETRIC KEY | TYPE | USER | XML SCHEMA COLLECTION  
```  
  
## <a name="arguments"></a>引數  
 DEFAULT  
 當使用預設選項來呼叫時 (沒有引號) ，此函式會傳回內建許可權的完整清單。  
  
 NULL  
 相當於 DEFAULT。  
  
 *empty_string*  
 相當於 DEFAULT。  
  
 **'**<securable_class>**'**  
 使用一個安全性實體類別的名稱來呼叫它時，sys.fn_builtin_permissions 會傳回套用至類別的擁有權限。 <securable_class> 是需要引號的字串常值。 **nvarchar(60)**  
  
## <a name="tables-returned"></a>傳回的資料表  
  
|資料行名稱|資料類型|定序|Description|  
|-----------------|---------------|---------------|-----------------|  
|class_desc|**nvarchar(60)**|伺服器的定序|安全性實體類別的描述。|  
|permission_name|**nvarchar(60)**|伺服器的定序|權限名稱。|  
|類型|**varchar(4)**|伺服器的定序|壓縮權限類型碼。 請參閱下表。|  
|covering_permission_name|**nvarchar(60)**|伺服器的定序|如果不是 NULL，則為意味著這個類別其他權限之這個類別權限的名稱。|  
|parent_class_desc|**nvarchar(60)**|伺服器的定序|如果不是 NULL，則為含有目前類別的父類別名稱。|  
|parent_covering_permission_name|**nvarchar(60)**|伺服器的定序|如果不是 NULL，則為意味著該類別所有其他權限之父類別權限的名稱。|  
  
### <a name="permission-types"></a>許可權類型  
  
|權限類型|權限名稱|套用至安全性實體或類別|  
|---------------------|---------------------|-----------------------------------|  
|AADS|ALTER ANY DATABASE EVENT SESSION<br /> **適用於**： [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 至 [目前版本](../../sql-server/what-s-new-in-sql-server-2016.md))。|DATABASE|  
|AAES|ALTER ANY EVENT SESSION|SERVER|  
|AAMK|ALTER ANY MASK<br /> **適用於**： [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 至 [目前版本](../../sql-server/what-s-new-in-sql-server-2016.md))。|DATABASE|  
|ADBO|ADMINISTER BULK OPERATIONS|SERVER|  
|AEDS|ALTER ANY EXTERNAL DATA SOURCE<br /> **適用於**： [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 至 [目前版本](../../sql-server/what-s-new-in-sql-server-2016.md))。|DATABASE|  
|AEFF|ALTER ANY EXTERNAL FILE FORMAT<br /> **適用於**： [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 至 [目前版本](../../sql-server/what-s-new-in-sql-server-2016.md))。|DATABASE|  
|AL|ALTER|APPLICATION ROLE|  
|AL|ALTER|ASSEMBLY|  
|AL|ALTER<br />**適用於**： [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 至 [目前版本](../../sql-server/what-s-new-in-sql-server-2016.md))。|AVAILABILITY GROUP|  
|AL|ALTER|ASYMMETRIC KEY|  
|AL|ALTER|CERTIFICATE|  
|AL|ALTER|CONTRACT|  
|AL|ALTER|DATABASE|  
|AL|ALTER<br /> **套用 t o**： [!INCLUDE[ssSQLv14_md](../../includes/sssqlv14-md.md)] 和 [!INCLUDE[ssSDS_md](../../includes/sssds-md.md)] 。 |DATABASE SCOPED CREDENTIAL|
|AL|ALTER|端點|  
|AL|ALTER|FULLTEXT CATALOG|  
|AL|ALTER|FULLTEXT STOPLIST|  
|AL|ALTER|LOGIN|  
|AL|ALTER|MESSAGE TYPE|  
|AL|ALTER|OBJECT|  
|AL|ALTER|REMOTE SERVICE BINDING|  
|AL|ALTER|ROLE|  
|AL|ALTER|ROUTE|  
|AL|ALTER|SCHEMA|  
|AL|ALTER|SEARCH PROPERTY LIST|  
|AL|ALTER<br /> **適用於**： [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 至 [目前版本](../../sql-server/what-s-new-in-sql-server-2016.md))。|SERVER ROLE|  
|AL|ALTER|SERVICE|  
|AL|ALTER|SYMMETRIC KEY|  
|AL|ALTER|USER|  
|AL|ALTER|XML SCHEMA COLLECTION|  
|ALAA|ALTER ANY SERVER AUDIT|SERVER|  
|ALAG|ALTER ANY AVAILABILITY GROUP<br /> **適用於**： [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 至 [目前版本](../../sql-server/what-s-new-in-sql-server-2016.md))。|SERVER|  
|ALAK|ALTER ANY ASYMMETRIC KEY|DATABASE|  
|ALAR|ALTER ANY APPLICATION ROLE|DATABASE|  
|ALAS|ALTER ANY ASSEMBLY|DATABASE|  
|ALCD|ALTER ANY CREDENTIAL|SERVER|  
|ALCF|ALTER ANY CERTIFICATE|DATABASE|  
|ALCK|ALTER ANY COLUMN ENCRYPTION KEY<br />**適用於**： [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 至 [目前版本](../../sql-server/what-s-new-in-sql-server-2016.md))。|DATABASE|  
|ALCM|ALTER ANY COLUMN MASTER KEY<br />**適用於**： [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 至 [目前版本](../../sql-server/what-s-new-in-sql-server-2016.md))。|DATABASE|  
|ALCO|ALTER ANY CONNECTION|SERVER|  
|ALDA|ALTER ANY DATABASE AUDIT|DATABASE|  
|ALDB|ALTER ANY DATABASE|SERVER|  
|ALDC|ALTER ANY DATABASE SCOPED CONFIGURATION<br /> **適用於**： [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 至 [目前版本](../../sql-server/what-s-new-in-sql-server-2016.md))。|DATABASE|  
|ALDS|ALTER ANY DATASPACE|DATABASE|  
|ALED|ALTER ANY DATABASE EVENT NOTIFICATION|DATABASE|  
|ALES|ALTER ANY EVENT NOTIFICATION|SERVER|  
|ALFT|ALTER ANY FULLTEXT CATALOG|DATABASE|  
|ALHE|ALTER ANY ENDPOINT|SERVER|  
|ALLG|ALTER ANY LOGIN|SERVER|  
|ALLS|ALTER ANY LINKED SERVER|SERVER|  
|ALMT|ALTER ANY MESSAGE TYPE|DATABASE|  
|ALRL|ALTER ANY ROLE|DATABASE|  
|ALRS|ALTER RESOURCES|SERVER|  
|ALRT|ALTER ANY ROUTE|DATABASE|  
|ALSB|ALTER ANY REMOTE SERVICE BINDING|DATABASE|  
|ALSC|ALTER ANY CONTRACT|DATABASE|  
|ALSK|ALTER ANY SYMMETRIC KEY|DATABASE|  
|ALSM|ALTER ANY SCHEMA|DATABASE|  
|ALSP|ALTER ANY SECURITY POLICY<br />**適用於**： [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 至 [目前版本](../../sql-server/what-s-new-in-sql-server-2016.md))。|DATABASE|  
|ALSR|ALTER ANY SERVER ROLE<br /> **適用於**： [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 至 [目前版本](../../sql-server/what-s-new-in-sql-server-2016.md))。|SERVER|  
|ALSS|ALTER SERVER STATE|SERVER|  
|ALST|ALTER SETTINGS|SERVER|  
|ALSV|ALTER ANY SERVICE|DATABASE|  
|ALTG|ALTER ANY DATABASE DDL TRIGGER|DATABASE|  
|ALTR|ALTER TRACE|SERVER|  
|ALUS|ALTER ANY USER|DATABASE|  
|AUTH|AUTHENTICATE|DATABASE|  
|AUTH|AUTHENTICATE SERVER|SERVER|  
|BADB|BACKUP DATABASE|DATABASE|  
|BALO|BACKUP LOG|DATABASE|  
|CADB|CONNECT ANY DATABASE<br /> **適用於**： [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 至 [目前版本](../../sql-server/what-s-new-in-sql-server-2016.md))。|SERVER|  
|CL|CONTROL|APPLICATION ROLE|  
|CL|CONTROL|ASSEMBLY|  
|CL|CONTROL|ASYMMETRIC KEY|  
|CL|CONTROL<br />**適用於**： [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 至 [目前版本](../../sql-server/what-s-new-in-sql-server-2016.md))。|AVAILABILITY GROUP|  
|CL|CONTROL|CERTIFICATE|  
|CL|CONTROL|CONTRACT|  
|CL|CONTROL|DATABASE|  
|CL|CONTROL<br /> **適用對象**：[!INCLUDE[ssSQLv14_md](../../includes/sssqlv14-md.md)] 和 [!INCLUDE[ssSDS_md](../../includes/sssds-md.md)]。 |DATABASE SCOPED CREDENTIAL|
|CL|CONTROL|端點|  
|CL|CONTROL|FULLTEXT CATALOG|  
|CL|CONTROL|FULLTEXT STOPLIST|  
|CL|CONTROL|LOGIN|  
|CL|CONTROL|MESSAGE TYPE|  
|CL|CONTROL|OBJECT|  
|CL|CONTROL|REMOTE SERVICE BINDING|  
|CL|CONTROL|ROLE|  
|CL|CONTROL|ROUTE|  
|CL|CONTROL|SCHEMA|  
|CL|CONTROL|SEARCH PROPERTY LIST|  
|CL|CONTROL SERVER|SERVER|  
|CL|CONTROL<br />**適用於**： [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 至 [目前版本](../../sql-server/what-s-new-in-sql-server-2016.md))。|SERVER ROLE|  
|CL|CONTROL|SERVICE|  
|CL|CONTROL|SYMMETRIC KEY|  
|CL|CONTROL|TYPE|  
|CL|CONTROL|USER|  
|CL|CONTROL|XML SCHEMA COLLECTION|  
|CO|CONNECT|DATABASE|  
|CO|CONNECT|端點|  
|CORP|CONNECT REPLICATION|DATABASE|  
|COSQ|CONNECT SQL|SERVER|  
|CP|CHECKPOINT|DATABASE|  
|CRAC|CREATE AVAILABILITY GROUP<br /> **適用於**： [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 至 [目前版本](../../sql-server/what-s-new-in-sql-server-2016.md))。|SERVER|  
|CRAG|CREATE AGGREGATE|DATABASE|  
|CRAK|CREATE ASYMMETRIC KEY|DATABASE|  
|CRAS|CREATE ASSEMBLY|DATABASE|  
|CRCF|CREATE CERTIFICATE|DATABASE|  
|CRDB|CREATE ANY DATABASE|SERVER|  
|CRDB|CREATE DATABASE|DATABASE|  
|CRDE|CREATE DDL EVENT NOTIFICATION|SERVER|  
|CRDF|CREATE DEFAULT|DATABASE|  
|CRED|CREATE DATABASE DDL EVENT NOTIFICATION|DATABASE|  
|CRFN|CREATE FUNCTION|DATABASE|  
|CRFT|CREATE FULLTEXT CATALOG|DATABASE|  
|CRHE|CREATE ENDPOINT|SERVER|  
|CRMT|CREATE MESSAGE TYPE|DATABASE|  
|CRPR|CREATE PROCEDURE|DATABASE|  
|CRQU|CREATE QUEUE|DATABASE|  
|CRRL|CREATE ROLE|DATABASE|  
|CRRT|CREATE ROUTE|DATABASE|  
|CRRU|CREATE RULE|DATABASE|  
|CRSB|CREATE REMOTE SERVICE BINDING|DATABASE|  
|CRSC|CREATE CONTRACT|DATABASE|  
|CRSK|CREATE SYMMETRIC KEY|DATABASE|  
|CRSM|CREATE SCHEMA|DATABASE|  
|CRSN|CREATE SYNONYM|DATABASE|  
|CRSO|CREATE SEQUENCE|SCHEMA|  
|CRSR|CREATE SERVER ROLE<br /> **適用於**： [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 至 [目前版本](../../sql-server/what-s-new-in-sql-server-2016.md))。|SERVER|  
|CRSV|CREATE SERVICE|DATABASE|  
|CRTB|CREATE TABLE|DATABASE|  
|CRTE|CREATE TRACE EVENT NOTIFICATION|SERVER|  
|CRTY|CREATE TYPE|DATABASE|  
|CRVW|CREATE VIEW|DATABASE|  
|CRXS|CREATE XML SCHEMA COLLECTION|DATABASE|  
|DABO|ADMINISTER DATABASE BULK OPERATIONS<br /> **適用於**： [!INCLUDE[ssSDS_md](../../includes/sssds-md.md)]。|DATABASE|  
|DL|刪除|DATABASE|  
|DL|刪除|OBJECT|  
|DL|刪除|SCHEMA|  
|EAES|EXECUTE ANY EXTERNAL SCRIPT<br />**適用於**： [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 至 [目前版本](../../sql-server/what-s-new-in-sql-server-2016.md))。|DATABASE|  
|EX|執行 CREATE 陳述式之前，請先執行|DATABASE|  
|EX|執行 CREATE 陳述式之前，請先執行|OBJECT|  
|EX|執行 CREATE 陳述式之前，請先執行|SCHEMA|  
|EX|執行 CREATE 陳述式之前，請先執行|TYPE|  
|EX|執行 CREATE 陳述式之前，請先執行|XML SCHEMA COLLECTION|  
|IAL|IMPERSONATE ANY LOGIN<br /> **適用於**： [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 至 [目前版本](../../sql-server/what-s-new-in-sql-server-2016.md))。|SERVER|  
|IM|IMPERSONATE|LOGIN|  
|IM|IMPERSONATE|USER|  
|IN|Insert|DATABASE|  
|IN|Insert|OBJECT|  
|IN|Insert|SCHEMA|  
|KIDC|KILL DATABASE CONNECTION<br />**適用於**： [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。|DATABASE|  
|RC|RECEIVE|OBJECT|  
|RF|REFERENCES|ASSEMBLY|  
|RF|REFERENCES|ASYMMETRIC KEY|  
|RF|REFERENCES|CERTIFICATE|  
|RF|REFERENCES|CONTRACT|  
|RF|REFERENCES|DATABASE|  
|RF|REFERENCES<br /> **適用對象**：[!INCLUDE[ssSQLv14_md](../../includes/sssqlv14-md.md)] 和 [!INCLUDE[ssSDS_md](../../includes/sssds-md.md)]。 |DATABASE SCOPED CREDENTIAL|
|RF|REFERENCES|FULLTEXT CATALOG|  
|RF|REFERENCES|FULLTEXT STOPLIST|  
|RF|REFERENCES|SEARCH PROPERTY LIST|  
|RF|REFERENCES|MESSAGE TYPE|  
|RF|REFERENCES|OBJECT|  
|RF|REFERENCES|SCHEMA|  
|RF|REFERENCES|SYMMETRIC KEY|  
|RF|REFERENCES|TYPE|  
|RF|REFERENCES|XML SCHEMA COLLECTION|  
|SHDN|SHUTDOWN|SERVER|  
|SL|SELECT|DATABASE|  
|SL|SELECT|OBJECT|  
|SL|SELECT|SCHEMA|  
|SN|SEND|SERVICE|  
|SPLN|SHOWPLAN|DATABASE|  
|SUQN|SUBSCRIBE QUERY NOTIFICATIONS|DATABASE|  
|SUS|SELECT ALL USER SECURABLES<br /> **適用於**： [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 至 [目前版本](../../sql-server/what-s-new-in-sql-server-2016.md))。|SERVER|  
|TO|TAKE OWNERSHIP|ASSEMBLY|  
|TO|TAKE OWNERSHIP|ASYMMETRIC KEY|  
|TO|TAKE OWNERSHIP<br />**適用於**： [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 至 [目前版本](../../sql-server/what-s-new-in-sql-server-2016.md))。|AVAILABILITY GROUP|  
|TO|TAKE OWNERSHIP|CERTIFICATE|  
|TO|TAKE OWNERSHIP|CONTRACT|  
|TO|TAKE OWNERSHIP|DATABASE|  
|TO|TAKE OWNERSHIP<br /> **適用對象**：[!INCLUDE[ssSQLv14_md](../../includes/sssqlv14-md.md)] 和 [!INCLUDE[ssSDS_md](../../includes/sssds-md.md)]。 |DATABASE SCOPED CREDENTIAL|
|TO|TAKE OWNERSHIP|端點|  
|TO|TAKE OWNERSHIP|FULLTEXT CATALOG|  
|TO|TAKE OWNERSHIP|FULLTEXT STOPLIST|  
|TO|TAKE OWNERSHIP|SEARCH PROPERTY LIST|  
|TO|TAKE OWNERSHIP|MESSAGE TYPE|  
|TO|TAKE OWNERSHIP|OBJECT|  
|TO|TAKE OWNERSHIP|REMOTE SERVICE BINDING|  
|TO|TAKE OWNERSHIP|ROLE|  
|TO|TAKE OWNERSHIP|ROUTE|  
|TO|TAKE OWNERSHIP|SCHEMA|  
|TO|TAKE OWNERSHIP<br /> **適用於**： [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 至 [目前版本](../../sql-server/what-s-new-in-sql-server-2016.md))。|SERVER ROLE|  
|TO|TAKE OWNERSHIP|SERVICE|  
|TO|TAKE OWNERSHIP|SYMMETRIC KEY|  
|TO|TAKE OWNERSHIP|TYPE|  
|TO|TAKE OWNERSHIP|XML SCHEMA COLLECTION|  
|UMSK|UNMASK<br /> **適用於**： [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 至 [目前版本](../../sql-server/what-s-new-in-sql-server-2016.md))。|DATABASE|  
|UP|UPDATE|DATABASE|  
|UP|UPDATE|OBJECT|  
|UP|UPDATE|SCHEMA|  
|VW|VIEW DEFINITION|APPLICATION ROLE|  
|VW|VIEW DEFINITION|ASSEMBLY|  
|VW|VIEW DEFINITION|ASYMMETRIC KEY|  
|VW|VIEW DEFINITION<br />**適用於**： [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 至 [目前版本](../../sql-server/what-s-new-in-sql-server-2016.md))。|AVAILABILITY GROUP|  
|VW|VIEW DEFINITION|CERTIFICATE|  
|VW|VIEW DEFINITION|CONTRACT|  
|VW|VIEW DEFINITION|DATABASE|  
|VW|VIEW DEFINITION<br /> **適用對象**：[!INCLUDE[ssSQLv14_md](../../includes/sssqlv14-md.md)] 和 [!INCLUDE[ssSDS_md](../../includes/sssds-md.md)]。 |DATABASE SCOPED CREDENTIAL|
|VW|VIEW DEFINITION|端點|  
|VW|VIEW DEFINITION|FULLTEXT CATALOG|  
|VW|VIEW DEFINITION|FULLTEXT STOPLIST|  
|VW|VIEW DEFINITION|LOGIN|  
|VW|VIEW DEFINITION|MESSAGE TYPE|  
|VW|VIEW DEFINITION|OBJECT|  
|VW|VIEW DEFINITION|REMOTE SERVICE BINDING|  
|VW|VIEW DEFINITION|ROLE|  
|VW|VIEW DEFINITION|ROUTE|  
|VW|VIEW DEFINITION|SCHEMA|  
|VW|VIEW DEFINITION|SEARCH PROPERTY LIST|  
|VW|VIEW DEFINITION<br /> **適用於**： [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 至 [目前版本](../../sql-server/what-s-new-in-sql-server-2016.md))。|SERVER ROLE|  
|VW|VIEW DEFINITION|SERVICE|  
|VW|VIEW DEFINITION|SYMMETRIC KEY|  
|VW|VIEW DEFINITION|TYPE|  
|VW|VIEW DEFINITION|USER|  
|VW|VIEW DEFINITION|XML SCHEMA COLLECTION|  
|VWAD|VIEW ANY DEFINITION|SERVER|  
|VWCK|VIEW ANY COLUMN ENCRYPTION KEY DEFINITION<br /> **適用於**： [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 至 [目前版本](../../sql-server/what-s-new-in-sql-server-2016.md))。|DATABASE|  
|VWCM|VIEW ANY COLUMN MASTER KEY DEFINITION<br /> **適用於**： [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 至 [目前版本](../../sql-server/what-s-new-in-sql-server-2016.md))。|DATABASE|  
|VWCT|VIEW CHANGE TRACKING|OBJECT|  
|VWCT|VIEW CHANGE TRACKING|SCHEMA|  
|VWDB|VIEW ANY DATABASE|SERVER|  
|VWDS|VIEW DATABASE STATE|DATABASE|  
|VWSS|VIEW SERVER STATE|SERVER|  
|XA|EXTERNAL ACCESS ASSEMBLY|SERVER|  
|XU|UNSAFE ASSEMBLY|SERVER|  
  
## <a name="remarks"></a>備註  
 `sys.fn_builtin_permissions` 是資料表值函式，它會發出一份預先定義的權限階層副本。 這個階層含有涵蓋權限。 `DEFAULT`結果集會描述許可權階層的有向非循環圖表，根是 (class = SERVER，許可權 = CONTROL SERVER) 。  
  
 `sys.fn_builtin_permissions` 不接受相互關聯的參數。  
  
 以無效的類別名稱呼叫 `sys.fn_builtin_permissions` 時，便會傳回空集合。  
 
[!INCLUDE[database-engine-permissions](../../includes/paragraph-content/database-engine-permissions.md)]
  
## <a name="permissions"></a>權限  
 需要 public 角色中的成員資格。  
  
## <a name="examples"></a>範例  
  
### <a name="a-listing-all-built-in-permissions"></a>A. 列出所有內建權限   
使用 `DEFAULT` 或空字串來傳回擁有權限。   
```sql  
SELECT * FROM sys.fn_builtin_permissions(DEFAULT);
SELECT * FROM sys.fn_builtin_permissions('');  
```  
  
### <a name="b-listing-permissions-that-can-be-set-on-a-symmetric-key"></a>B. 列出可在對稱金鑰上設定的權限   
指定類別，以傳回該類別的所有可能許可權。   
```sql  
SELECT * FROM sys.fn_builtin_permissions(N'SYMMETRIC KEY');  
```  
  
### <a name="c-listing-classes-on-which-there-is-a-select-permission"></a>C. 列出具有 SELECT 權限的類別   
  
```sql  
SELECT * FROM sys.fn_builtin_permissions(DEFAULT)   
    WHERE permission_name = 'SELECT';  
```  
  
## <a name="see-also"></a>另請參閱  
 [權限階層 &#40;Database Engine&#41;](../../relational-databases/security/permissions-hierarchy-database-engine.md)   
 [GRANT &#40;Transact-SQL&#41;](../../t-sql/statements/grant-transact-sql.md)   
 [CREATE SCHEMA &#40;Transact-SQL&#41;](../../t-sql/statements/create-schema-transact-sql.md)   
 [DROP SCHEMA &#40;Transact-SQL&#41;](../../t-sql/statements/drop-schema-transact-sql.md)   
 [權限 &#40;資料庫引擎&#41;](../../relational-databases/security/permissions-database-engine.md)   
 [sys.fn_my_permissions &#40;Transact-SQL&#41;](../../relational-databases/system-functions/sys-fn-my-permissions-transact-sql.md)   
 [HAS_PERMS_BY_NAME &#40;Transact-SQL&#41;](../../t-sql/functions/has-perms-by-name-transact-sql.md)  
  
