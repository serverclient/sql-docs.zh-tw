---
title: 權限 (Database Engine) | Microsoft Docs
description: 請參閱此 SQL Server 權限的完整清單，找出哪些權限適用於您所使用的平台。
ms.custom: ''
ms.date: 10/30/2020
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: security
ms.topic: conceptual
f1_keywords:
- sql13.swb.databaseuser.permissions.database.f1--May use common.permissions
- sql13.swb.databaseuser.permissions.object.f1--May use common.permissions
helpviewer_keywords:
- REFERENCES permission
- permissions [SQL Server]
- security [SQL Server], permissions
- naming conventions [SQL Server]
ms.assetid: f28e3dea-24e6-4a81-877b-02ec4c7e36b9
author: AndreasWolter
ms.author: anwolter
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: b40dbcfc25bcc3b86868228fe5646c4318ed830c
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98172220"
---
# <a name="permissions-database-engine"></a>權限 (Database Engine)
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

每個 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安全性實體都具有可授與主體的關聯權限。 在 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 中，指派給登入與伺服器角色的權限會在伺服器層級管理，而指派給資料庫使用者與資料庫角色的權限則會在資料庫層級進行管理。 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 模型的權限與資料庫權限系統相同，但無法使用伺服器層級的權限。 本主題包含完整的權限清單。 如需權限的一般實作，請參閱 [資料庫引擎權限使用者入門](../../relational-databases/security/authentication-access/getting-started-with-database-engine-permissions.md)。  
  
[!INCLUDE[ssSQLv15_md](../../includes/sssqlv15-md.md)] 的權限總數是 248。 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 會公開 254 個權限。 大部分的權限適用於所有平台，但某些無法適用。 例如，無法在 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 上授與伺服器層級權限，而且少數權限只有在 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 上才有意義。
新版本會逐漸採用新的權限。 [!INCLUDE[ssSQLv14_md](../../includes/sssqlv14-md.md)] 會公開 238 個權限。 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 公開 230 個權限。 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 公開 219 個權限。 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 公開 214 個權限。 [!INCLUDE[ssKilimanjaro](../../includes/sskilimanjaro-md.md)] 公開 195 個權限。 [sys.fn_builtin_permissions](../../relational-databases/system-functions/sys-fn-builtin-permissions-transact-sql.md) 主題指出最新版本中的新權限。

一旦您了解權限後，可以透過 [GRANT](../../t-sql/statements/grant-transact-sql.md)、 [REVOKE](../../t-sql/statements/revoke-transact-sql.md)和 [DENY](../../t-sql/statements/deny-transact-sql.md) 陳述式，將伺服器層級權限套用至登入和資料庫層級權限使用者。 例如：   
```sql
GRANT SELECT ON OBJECT::HumanResources.Employee TO Larry;
REVOKE SELECT ON OBJECT::HumanResources.Employee TO Larry;
```   
如需規劃權限系統的秘訣，請參閱 [資料庫引擎權限使用者入門](../../relational-databases/security/authentication-access/getting-started-with-database-engine-permissions.md)。
  
##  <a name="permissions-naming-conventions"></a><a name="_conventions"></a> 權限命名慣例  
 以下描述一般權限命名慣例：  
  
-   CONTROL  
  
     將類似擁有權的能力授與被授與者。 被授與者實際上擁有安全性實體上已定義的所有權限。 已被授與 CONTROL 的實體也可以將安全性實體上的權限授與其他人。 因為 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安全性模型是階層式的，所以在特定範圍內的 CONTROL 隱含包含該範圍下所有安全性實體上的 CONTROL 權限。 例如，資料庫上的 CONTROL 權限隱含表示該資料庫上的所有權限，該資料庫中所有組件上的權限、該資料庫中所有結構描述上的權限，以及該資料庫中所有結構描述內所有物件上的權限。  
  
-   ALTER  
  
     授與變更特定安全性實體之屬性 (除了擁有權之外) 的能力。 在特定範圍授與 ALTER 權限時，也會一併授與改變、建立或卸除該範圍內包含之任何安全性實體的能力。 例如，結構描述上的 ALTER 權限包括建立、改變與卸除結構描述之物件的能力。  
  
-   ALTER ANY \<*Server Securable*>，其中 *Server Securable* 可以是任何伺服器安全性實體。  
  
     授與 *伺服器安全性實體* 的 CREATE、ALTER 或 DROP 個別執行個體的能力。 例如，ALTER ANY LOGIN 會授與建立、改變或卸除執行個體中任何登入的能力。  
  
-   ALTER ANY \<*Database Securable*>，其中 *Database Securable* 可以是資料庫層級上的任何安全性實體。  
  
     授與 *資料庫安全性實體* 的 CREATE、ALTER 或 DROP 個別執行個體能力。 例如，ALTER ANY SCHEMA 會授與建立、改變或卸除資料庫中任何結構描述的能力。  
  
-   TAKE OWNERSHIP  
  
     讓被授與者可以取得被授與之安全性實體的擁有權。  
  
-   IMPERSONATE \<*Login*>  
  
     讓被授與者可以模擬登入。  
  
-   IMPERSONATE \<*User*>  
  
     讓被授與者可以模擬使用者。  
  
-   CREATE \<*Server Securable*>  
  
     為被授與者授與建立 *伺服器安全性實體* 的能力。  
  
-   CREATE \<*Database Securable*>  
  
     為被授與者授與建立 *資料庫安全性實體* 的能力。  
  
-   CREATE \<*Schema-contained Securable*>  
  
     授與建立結構描述包含的安全性實體之能力。 然而，需要結構描述上的 ALTER 權限才能在特定結構描述中建立該安全性實體。  
  
-   VIEW DEFINITION  
  
     讓被授與者能存取中繼資料。  
  
-   REFERENCES  
  
     需要資料表的 REFERENCES 權限，才能建立參考該資料表的 FOREIGN KEY 條件約束。  
  
     需要物件的 REFERENCES 權限，才能建立具有可參考該物件之 `WITH SCHEMABINDING` 的 FUNCTION 或 VIEW。  
  
## <a name="chart-of-sql-server-permissions"></a>SQL Server 權限的圖表  
[!INCLUDE[database-engine-permissions](../../includes/paragraph-content/database-engine-permissions.md)]
  
##  <a name="permissions-applicable-to-specific-securables"></a><a name="_securables"></a> 適用於特定安全性實體的權限  
 下表列出主要的權限類別，以及這些類別的權限適用的安全性實體。  
  
|權限|適用於|  
|----------------|----------------|  
|ALTER|除了 TYPE 之外的所有物件類別。|  
|CONTROL|所有物件類別︰ <br />AGGREGATE、<br />APPLICATION ROLE、<br />ASSEMBLY、<br />ASYMMETRIC KEY、<br />AVAILABILITY GROUP、<br />CERTIFICATE、<br />CONTRACT、<br />CREDENTIALS, DATABASE、<br />DATABASE SCOPED CREDENTIAL、<br /> DEFAULT、<br />ENDPOINT、<br />FULLTEXT CATALOG、<br />FULLTEXT STOPLIST、<br />FUNCTION、<br />LOGIN、<br />MESSAGE TYPE、<br />PROCEDURE、<br />QUEUE、 <br />REMOTE SERVICE BINDING、<br />ROLE、<br />ROUTE、<br />RULE、<br />SCHEMA、<br />SEARCH PROPERTY LIST、<br />SERVER、<br />SERVER ROLE、<br />SERVICE、<br />SYMMETRIC KEY、<br />SYNONYM、<br />TABLE、<br />TYPE、<br /> USER、<br />VIEW，以及<br />XML SCHEMA COLLECTION|  
|刪除|除了 DATABASE SCOPED CONFIGURATION、SERVER 及 TYPE 之外的所有物件類別。|  
|執行 CREATE 陳述式之前，請先執行|CLR 類型、外部指令碼、程序 ([!INCLUDE[tsql](../../includes/tsql-md.md)] 與 CLR)、純量與彙總函式 ([!INCLUDE[tsql](../../includes/tsql-md.md)] 與 CLR)，及同義字|  
|IMPERSONATE|登入和使用者|  
|Insert|同義字、資料表與資料行、檢視與資料行。 權限可以在資料庫、結構描述或物件層級授與。|  
|RECEIVE|[!INCLUDE[ssSB](../../includes/sssb-md.md)] 佇列|  
|REFERENCES|AGGREGATE、<br />ASSEMBLY、<br />ASYMMETRIC KEY、<br />CERTIFICATE、<br />CONTRACT、<br />DATABASE、<br />DATABASE SCOPED CREDENTIAL、<br />FULLTEXT CATALOG、<br />FULLTEXT STOPLIST、<br />FUNCTION、<br />MESSAGE TYPE、<br />PROCEDURE、<br />QUEUE、 <br />RULE、<br />SCHEMA、<br />SEARCH PROPERTY LIST、<br />SEQUENCE OBJECT、 <br />SYMMETRIC KEY、<br />TABLE、<br />TYPE、<br />VIEW，以及<br />XML SCHEMA COLLECTION|  
|SELECT|同義字、資料表與資料行、檢視與資料行。 權限可以在資料庫、結構描述或物件層級授與。|  
|TAKE OWNERSHIP|除了 DATABASE SCOPED CONFIGURATION、LOGIN、SERVER 及 USER 之外的所有物件類別。|  
|UPDATE|同義字、資料表與資料行、檢視與資料行。 權限可以在資料庫、結構描述或物件層級授與。|  
|VIEW CHANGE TRACKING|結構描述與資料表|  
|VIEW DEFINITION|除了 DATABASE SCOPED CONFIGURATION 及 SERVER 以外的所有物件類別。|  
  
> [!CAUTION]  
>  在安裝時為系統物件授與的預設權限，經過仔細評估可能面臨的威脅，因此在強化 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝的過程中無須改變。 系統物件的任何權限變更，都可能會限制或破壞其功效，而可能會讓您的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝處於不支援的狀態。  
  
##  <a name="sql-server-permissions"></a><a name="_permissions"></a> SQL Server 權限  
 下表提供完整的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 權限清單。 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 權限僅供支援的基底安全性實體之用。 無法在 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]中授與伺服器層級權限，不過，在某些情況下，會改為提供資料庫權限。  
  
|基底安全性實體|基底安全性實體上更細微的權限|權限類型代碼|包含基底安全性實體的安全性實體|容器安全性實體上隱含基底安全性實體之小權限的權限|  
|--------------------|--------------------------------------------|--------------------------|--------------------------------------------|------------------------------------------------------------------------------------------|  
|APPLICATION ROLE|ALTER|AL|DATABASE|ALTER ANY APPLICATION ROLE|  
|APPLICATION ROLE|CONTROL|CL|DATABASE|CONTROL|  
|APPLICATION ROLE|VIEW DEFINITION|VW|DATABASE|VIEW DEFINITION|  
|ASSEMBLY|ALTER|AL|DATABASE|ALTER ANY ASSEMBLY|  
|ASSEMBLY|CONTROL|CL|DATABASE|CONTROL|  
|ASSEMBLY|REFERENCES|RF|DATABASE|REFERENCES|  
|ASSEMBLY|TAKE OWNERSHIP|TO|DATABASE|CONTROL|  
|ASSEMBLY|VIEW DEFINITION|VW|DATABASE|VIEW DEFINITION|  
|ASYMMETRIC KEY|ALTER|AL|DATABASE|ALTER ANY ASYMMETRIC KEY|  
|ASYMMETRIC KEY|CONTROL|CL|DATABASE|CONTROL|  
|ASYMMETRIC KEY|REFERENCES|RF|DATABASE|REFERENCES|  
|ASYMMETRIC KEY|TAKE OWNERSHIP|TO|DATABASE|CONTROL|  
|ASYMMETRIC KEY|VIEW DEFINITION|VW|DATABASE|VIEW DEFINITION|  
|AVAILABILITY GROUP|ALTER|AL|SERVER|ALTER ANY AVAILABILITY GROUP|  
|AVAILABILITY GROUP|CONTROL|CL|SERVER|CONTROL SERVER|  
|AVAILABILITY GROUP|TAKE OWNERSHIP|TO|SERVER|CONTROL SERVER|  
|AVAILABILITY GROUP|VIEW DEFINITION|VW|SERVER|VIEW ANY DEFINITION|  
|CERTIFICATE|ALTER|AL|DATABASE|ALTER ANY CERTIFICATE|  
|CERTIFICATE|CONTROL|CL|DATABASE|CONTROL|  
|CERTIFICATE|REFERENCES|RF|DATABASE|REFERENCES|  
|CERTIFICATE|TAKE OWNERSHIP|TO|DATABASE|CONTROL|  
|CERTIFICATE|VIEW DEFINITION|VW|DATABASE|VIEW DEFINITION|  
|CONTRACT|ALTER|AL|DATABASE|ALTER ANY CONTRACT|  
|CONTRACT|CONTROL|CL|DATABASE|CONTROL|  
|CONTRACT|REFERENCES|RF|DATABASE|REFERENCES|  
|CONTRACT|TAKE OWNERSHIP|TO|DATABASE|CONTROL|  
|CONTRACT|VIEW DEFINITION|VW|DATABASE|VIEW DEFINITION|  
|DATABASE|ADMINISTER DATABASE BULK OPERATIONS|DABO|SERVER|CONTROL SERVER|
|DATABASE|ALTER|AL|SERVER|ALTER ANY DATABASE|  
|DATABASE|ALTER ANY APPLICATION ROLE|ALAR|SERVER|CONTROL SERVER|  
|DATABASE|ALTER ANY ASSEMBLY|ALAS|SERVER|CONTROL SERVER|  
|DATABASE|ALTER ANY ASYMMETRIC KEY|ALAK|SERVER|CONTROL SERVER|  
|DATABASE|ALTER ANY CERTIFICATE|ALCF|SERVER|CONTROL SERVER|  
|DATABASE|ALTER ANY COLUMN ENCRYPTION KEY|ALCK<br /><br /> 適用於 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 到目前的版本)， [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。|SERVER|CONTROL SERVER|  
|DATABASE|ALTER ANY COLUMN MASTER KEY|ALCM<br /><br /> 適用於 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 到目前的版本)， [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。|SERVER|CONTROL SERVER|  
|DATABASE|ALTER ANY CONTRACT|ALSC|SERVER|CONTROL SERVER|  
|DATABASE|ALTER ANY DATABASE AUDIT|ALDA|SERVER|ALTER ANY SERVER AUDIT|  
|DATABASE|ALTER ANY DATABASE DDL TRIGGER|ALTG|SERVER|CONTROL SERVER|  
|DATABASE|ALTER ANY DATABASE EVENT NOTIFICATION|ALED|SERVER|ALTER ANY EVENT NOTIFICATION|  
|DATABASE|ALTER ANY DATABASE EVENT SESSION|AADS<br /><br /> 適用於 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。|SERVER|ALTER ANY EVENT SESSION|  
|DATABASE|ALTER ANY DATABASE SCOPED CONFIGURATION|ALDC<br /><br /> 適用於 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 到目前的版本)， [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。|SERVER|CONTROL SERVER|  
|DATABASE|ALTER ANY DATASPACE|ALDS|SERVER|CONTROL SERVER|  
|DATABASE|ALTER ANY EXTERNAL DATA SOURCE|AEDS|SERVER|CONTROL SERVER|  
|DATABASE|ALTER ANY EXTERNAL FILE FORMAT|AEFF|SERVER|CONTROL SERVER|  
|DATABASE|ALTER ANY FULLTEXT CATALOG|ALFT|SERVER|CONTROL SERVER|  
|DATABASE|ALTER ANY MASK|AAMK<br /><br /> 適用於 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 到目前的版本)， [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。|SERVER|CONTROL SERVER|  
|DATABASE|ALTER ANY MESSAGE TYPE|ALMT|SERVER|CONTROL SERVER|  
|DATABASE|ALTER ANY REMOTE SERVICE BINDING|ALSB|SERVER|CONTROL SERVER|  
|DATABASE|ALTER ANY ROLE|ALRL|SERVER|CONTROL SERVER|  
|DATABASE|ALTER ANY ROUTE|ALRT|SERVER|CONTROL SERVER|  
|DATABASE|ALTER ANY SCHEMA|ALSM|SERVER|CONTROL SERVER|  
|DATABASE|ALTER ANY SECURITY POLICY|ALSP<br /><br /> 適用於 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 到目前的版本)， [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。|SERVER|CONTROL SERVER|  
|DATABASE|ALTER ANY SENSITIVITY CLASSIFICATION|ALSP<br />適用於 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (SQL Server 2019 (15.x) 到最新版本)，[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。|DATABASE|CONTROL SERVER|
|DATABASE|ALTER ANY SERVICE|ALSV|SERVER|CONTROL SERVER|  
|DATABASE|ALTER ANY SYMMETRIC KEY|ALSK|SERVER|CONTROL SERVER|  
|DATABASE|ALTER ANY USER|ALUS|SERVER|CONTROL SERVER|  
|DATABASE|AUTHENTICATE|AUTH|SERVER|AUTHENTICATE SERVER|  
|DATABASE|BACKUP DATABASE|BADB|SERVER|CONTROL SERVER|  
|DATABASE|BACKUP LOG|BALO|SERVER|CONTROL SERVER|  
|DATABASE|CHECKPOINT|CP|SERVER|CONTROL SERVER|  
|DATABASE|CONNECT|CO|SERVER|CONTROL SERVER|  
|DATABASE|CONNECT REPLICATION|CORP|SERVER|CONTROL SERVER|  
|DATABASE|CONTROL|CL|SERVER|CONTROL SERVER|  
|DATABASE|CREATE AGGREGATE|CRAG|SERVER|CONTROL SERVER|  
|DATABASE|CREATE ASSEMBLY|CRAS|SERVER|CONTROL SERVER|  
|DATABASE|CREATE ASYMMETRIC KEY|CRAK|SERVER|CONTROL SERVER|  
|DATABASE|CREATE CERTIFICATE|CRCF|SERVER|CONTROL SERVER|  
|DATABASE|CREATE CONTRACT|CRSC|SERVER|CONTROL SERVER|  
|DATABASE|CREATE DATABASE|CRDB|SERVER|CREATE ANY DATABASE|  
|DATABASE|CREATE DATABASE DDL EVENT NOTIFICATION|CRED|SERVER|CREATE DDL EVENT NOTIFICATION|  
|DATABASE|CREATE DEFAULT|CRDF|SERVER|CONTROL SERVER|  
|DATABASE|CREATE FULLTEXT CATALOG|CRFT|SERVER|CONTROL SERVER|  
|DATABASE|CREATE FUNCTION|CRFN|SERVER|CONTROL SERVER|  
|DATABASE|CREATE MESSAGE TYPE|CRMT|SERVER|CONTROL SERVER|  
|DATABASE|CREATE PROCEDURE|CRPR|SERVER|CONTROL SERVER|  
|DATABASE|CREATE QUEUE|CRQU|SERVER|CONTROL SERVER|  
|DATABASE|CREATE REMOTE SERVICE BINDING|CRSB|SERVER|CONTROL SERVER|  
|DATABASE|CREATE ROLE|CRRL|SERVER|CONTROL SERVER|  
|DATABASE|CREATE ROUTE|CRRT|SERVER|CONTROL SERVER|  
|DATABASE|CREATE RULE|CRRU|SERVER|CONTROL SERVER|  
|DATABASE|CREATE SCHEMA|CRSM|SERVER|CONTROL SERVER|  
|DATABASE|CREATE SERVICE|CRSV|SERVER|CONTROL SERVER|  
|DATABASE|CREATE SYMMETRIC KEY|CRSK|SERVER|CONTROL SERVER|  
|DATABASE|CREATE SYNONYM|CRSN|SERVER|CONTROL SERVER|  
|DATABASE|CREATE TABLE|CRTB|SERVER|CONTROL SERVER|  
|DATABASE|CREATE TYPE|CRTY|SERVER|CONTROL SERVER|  
|DATABASE|CREATE VIEW|CRVW|SERVER|CONTROL SERVER|  
|DATABASE|CREATE XML SCHEMA COLLECTION|CRXS|SERVER|CONTROL SERVER|  
|DATABASE|刪除|DL|SERVER|CONTROL SERVER|  
|DATABASE|執行 CREATE 陳述式之前，請先執行|EX|SERVER|CONTROL SERVER|  
|DATABASE|EXECUTE ANY EXTERNAL SCRIPT|EAES<br /><br /> 適用於 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 到目前的版本)。|SERVER|CONTROL SERVER|  
|DATABASE|Insert|IN|SERVER|CONTROL SERVER|  
|DATABASE|KILL DATABASE CONNECTION|KIDC<br /><br /> 只適用於 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。 在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]中使用 ALTER ANY CONNECTION。|SERVER|ALTER ANY CONNECTION|  
|DATABASE|REFERENCES|RF|SERVER|CONTROL SERVER|  
|DATABASE|SELECT|SL|SERVER|CONTROL SERVER|  
|DATABASE|SHOWPLAN|SPLN|SERVER|ALTER TRACE|  
|DATABASE|SUBSCRIBE QUERY NOTIFICATIONS|SUQN|SERVER|CONTROL SERVER|  
|DATABASE|TAKE OWNERSHIP|TO|SERVER|CONTROL SERVER|  
|DATABASE|UNMASK|UMSK<br /><br /> 適用於 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 到目前的版本)， [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。|SERVER|CONTROL SERVER|  
|DATABASE|UPDATE|UP|SERVER|CONTROL SERVER|  
|DATABASE|VIEW ANY COLUMN ENCRYPTION KEY DEFINITION|VWCK<br /><br /> 適用於 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 到目前的版本)， [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。|SERVER|VIEW SERVER STATE|  
|DATABASE|VIEW ANY COLUMN MASTER KEY DEFINITION|vWCM<br /><br /> 適用於 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 到目前的版本)， [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。|SERVER|VIEW SERVER STATE|  
|DATABASE|VIEW DATABASE STATE|VWDS|SERVER|VIEW SERVER STATE|  
|DATABASE|VIEW DEFINITION|VW|SERVER|VIEW ANY DEFINITION|  
|DATABASE SCOPED CREDENTIAL|ALTER|AL|DATABASE|CONTROL|
|DATABASE SCOPED CREDENTIAL|CONTROL|CL|DATABASE|CONTROL|
|DATABASE SCOPED CREDENTIAL|REFERENCES|RF|DATABASE|REFERENCES|
|DATABASE SCOPED CREDENTIAL|TAKE OWNERSHIP|TO|DATABASE|CONTROL|
|DATABASE SCOPED CREDENTIAL|VIEW DEFINITION|VW|DATABASE|VIEW DEFINITION|
|端點|ALTER|AL|SERVER|ALTER ANY ENDPOINT|  
|端點|CONNECT|CO|SERVER|CONTROL SERVER|  
|端點|CONTROL|CL|SERVER|CONTROL SERVER|  
|端點|TAKE OWNERSHIP|TO|SERVER|CONTROL SERVER|  
|端點|VIEW DEFINITION|VW|SERVER|VIEW ANY DEFINITION|  
|FULLTEXT CATALOG|ALTER|AL|DATABASE|ALTER ANY FULLTEXT CATALOG|  
|FULLTEXT CATALOG|CONTROL|CL|DATABASE|CONTROL|  
|FULLTEXT CATALOG|REFERENCES|RF|DATABASE|REFERENCES|  
|FULLTEXT CATALOG|TAKE OWNERSHIP|TO|DATABASE|CONTROL|  
|FULLTEXT CATALOG|VIEW DEFINITION|VW|DATABASE|VIEW DEFINITION|  
|FULLTEXT STOPLIST|ALTER|AL|DATABASE|ALTER ANY FULLTEXT CATALOG|  
|FULLTEXT STOPLIST|CONTROL|CL|DATABASE|CONTROL|  
|FULLTEXT STOPLIST|REFERENCES|RF|DATABASE|REFERENCES|  
|FULLTEXT STOPLIST|TAKE OWNERSHIP|TO|DATABASE|CONTROL|  
|FULLTEXT STOPLIST|VIEW DEFINITION|VW|DATABASE|VIEW DEFINITION|  
|LOGIN|ALTER|AL|SERVER|ALTER ANY LOGIN|  
|LOGIN|CONTROL|CL|SERVER|CONTROL SERVER|  
|LOGIN|IMPERSONATE|IM|SERVER|CONTROL SERVER|  
|LOGIN|VIEW DEFINITION|VW|SERVER|VIEW ANY DEFINITION|  
|MESSAGE TYPE|ALTER|AL|DATABASE|ALTER ANY MESSAGE TYPE|  
|MESSAGE TYPE|CONTROL|CL|DATABASE|CONTROL|  
|MESSAGE TYPE|REFERENCES|RF|DATABASE|REFERENCES|  
|MESSAGE TYPE|TAKE OWNERSHIP|TO|DATABASE|CONTROL|  
|MESSAGE TYPE|VIEW DEFINITION|VW|DATABASE|VIEW DEFINITION|  
|OBJECT|ALTER|AL|SCHEMA|ALTER|  
|OBJECT|CONTROL|CL|SCHEMA|CONTROL|  
|OBJECT|刪除|DL|SCHEMA|刪除|  
|OBJECT|執行 CREATE 陳述式之前，請先執行|EX|SCHEMA|執行 CREATE 陳述式之前，請先執行|  
|OBJECT|Insert|IN|SCHEMA|Insert|  
|OBJECT|RECEIVE|RC|SCHEMA|CONTROL|  
|OBJECT|REFERENCES|RF|SCHEMA|REFERENCES|  
|OBJECT|SELECT|SL|SCHEMA|SELECT|  
|OBJECT|TAKE OWNERSHIP|TO|SCHEMA|CONTROL|  
|OBJECT|UPDATE|UP|SCHEMA|UPDATE|  
|OBJECT|VIEW CHANGE TRACKING|VWCT|SCHEMA|VIEW CHANGE TRACKING|  
|OBJECT|VIEW DEFINITION|VW|SCHEMA|VIEW DEFINITION|  
|REMOTE SERVICE BINDING|ALTER|AL|DATABASE|ALTER ANY REMOTE SERVICE BINDING|  
|REMOTE SERVICE BINDING|CONTROL|CL|DATABASE|CONTROL|  
|REMOTE SERVICE BINDING|TAKE OWNERSHIP|TO|DATABASE|CONTROL|  
|REMOTE SERVICE BINDING|VIEW DEFINITION|VW|DATABASE|VIEW DEFINITION|  
|ROLE|ALTER|AL|DATABASE|ALTER ANY ROLE|  
|ROLE|CONTROL|CL|DATABASE|CONTROL|  
|ROLE|TAKE OWNERSHIP|TO|DATABASE|CONTROL|  
|ROLE|VIEW DEFINITION|VW|DATABASE|VIEW DEFINITION|  
|ROUTE|ALTER|AL|DATABASE|ALTER ANY ROUTE|  
|ROUTE|CONTROL|CL|DATABASE|CONTROL|  
|ROUTE|TAKE OWNERSHIP|TO|DATABASE|CONTROL|  
|ROUTE|VIEW DEFINITION|VW|DATABASE|VIEW DEFINITION|  
|SEARCH PROPERTY LIST|ALTER|AL|SERVER|ALTER ANY FULLTEXT CATALOG|  
|SEARCH PROPERTY LIST|CONTROL|CL|SERVER|CONTROL|  
|SEARCH PROPERTY LIST|REFERENCES|RF|SERVER|REFERENCES|  
|SEARCH PROPERTY LIST|TAKE OWNERSHIP|TO|SERVER|CONTROL|  
|SEARCH PROPERTY LIST|VIEW DEFINITION|VW|SERVER|VIEW DEFINITION|  
|SCHEMA|ALTER|AL|DATABASE|ALTER ANY SCHEMA|  
|SCHEMA|CONTROL|CL|DATABASE|CONTROL|  
|SCHEMA|CREATE SEQUENCE|CRSO|DATABASE|CONTROL|  
|SCHEMA|刪除|DL|DATABASE|刪除|  
|SCHEMA|執行 CREATE 陳述式之前，請先執行|EX|DATABASE|執行 CREATE 陳述式之前，請先執行|  
|SCHEMA|Insert|IN|DATABASE|Insert|  
|SCHEMA|REFERENCES|RF|DATABASE|REFERENCES|  
|SCHEMA|SELECT|SL|DATABASE|SELECT|  
|SCHEMA|TAKE OWNERSHIP|TO|DATABASE|CONTROL|  
|SCHEMA|UPDATE|UP|DATABASE|UPDATE|  
|SCHEMA|VIEW CHANGE TRACKING|VWCT|DATABASE|VIEW CHANGE TRACKING|  
|SCHEMA|VIEW DEFINITION|VW|DATABASE|VIEW DEFINITION|  
|SERVER|ADMINISTER BULK OPERATIONS|ADBO|不適用|不適用|  
|SERVER|ALTER ANY AVAILABILITY GROUP|ALAG|不適用|不適用|  
|SERVER|ALTER ANY CONNECTION|ALCO|不適用|不適用|  
|SERVER|ALTER ANY CREDENTIAL|ALCD|不適用|不適用|  
|SERVER|ALTER ANY DATABASE|ALDB|不適用|不適用|  
|SERVER|ALTER ANY ENDPOINT|ALHE|不適用|不適用|  
|SERVER|ALTER ANY EVENT NOTIFICATION|ALES|不適用|不適用|  
|SERVER|ALTER ANY EVENT SESSION|AAES|不適用|不適用|  
|SERVER|ALTER ANY LINKED SERVER|ALLS|不適用|不適用|  
|SERVER|ALTER ANY LOGIN|ALLG|不適用|不適用|  
|SERVER|ALTER ANY SERVER AUDIT|ALAA|不適用|不適用|  
|SERVER|ALTER ANY SERVER ROLE|ALSR|不適用|不適用|  
|SERVER|ALTER RESOURCES|ALRS|不適用|不適用|  
|SERVER|ALTER SERVER STATE|ALSS|不適用|不適用|  
|SERVER|ALTER SETTINGS|ALST|不適用|不適用|  
|SERVER|ALTER TRACE|ALTR|不適用|不適用|  
|SERVER|AUTHENTICATE SERVER|AUTH|不適用|不適用|  
|SERVER|CONNECT ANY DATABASE|CADB|不適用|不適用|  
|SERVER|CONNECT SQL|COSQ|不適用|不適用|  
|SERVER|CONTROL SERVER|CL|不適用|不適用|  
|SERVER|CREATE ANY DATABASE|CRDB|不適用|不適用|  
|SERVER|CREATE AVAILABILITY GROUP|CRAC|不適用|不適用|  
|SERVER|CREATE DDL EVENT NOTIFICATION|CRDE|不適用|不適用|  
|SERVER|CREATE ENDPOINT|CRHE|不適用|不適用|  
|SERVER|CREATE SERVER ROLE|CRSR|不適用|不適用|  
|SERVER|CREATE TRACE EVENT NOTIFICATION|CRTE|不適用|不適用|  
|SERVER|EXTERNAL ACCESS ASSEMBLY|XA|不適用|不適用|  
|SERVER|IMPERSONATE ANY LOGIN|IAL|不適用|不適用|  
|SERVER|SELECT ALL USER SECURABLES|SUS|不適用|不適用|  
|SERVER|SHUTDOWN|SHDN|不適用|不適用|  
|SERVER|UNSAFE ASSEMBLY|XU|不適用|不適用|  
|SERVER|VIEW ANY DATABASE|VWDB|不適用|不適用|  
|SERVER|VIEW ANY DEFINITION|VWAD|不適用|不適用|  
|SERVER|VIEW SERVER STATE|VWSS|不適用|不適用|  
|SERVER ROLE|ALTER|AL|SERVER|ALTER ANY SERVER ROLE|  
|SERVER ROLE|CONTROL|CL|SERVER|CONTROL SERVER|  
|SERVER ROLE|TAKE OWNERSHIP|TO|SERVER|CONTROL SERVER|  
|SERVER ROLE|VIEW DEFINITION|VW|SERVER|VIEW ANY DEFINITION|  
|SERVICE|ALTER|AL|DATABASE|ALTER ANY SERVICE|  
|SERVICE|CONTROL|CL|DATABASE|CONTROL|  
|SERVICE|SEND|SN|DATABASE|CONTROL|  
|SERVICE|TAKE OWNERSHIP|TO|DATABASE|CONTROL|  
|SERVICE|VIEW DEFINITION|VW|DATABASE|VIEW DEFINITION|  
|SYMMETRIC KEY|ALTER|AL|DATABASE|ALTER ANY SYMMETRIC KEY|  
|SYMMETRIC KEY|CONTROL|CL|DATABASE|CONTROL|  
|SYMMETRIC KEY|REFERENCES|RF|DATABASE|REFERENCES|  
|SYMMETRIC KEY|TAKE OWNERSHIP|TO|DATABASE|CONTROL|  
|SYMMETRIC KEY|VIEW DEFINITION|VW|DATABASE|VIEW DEFINITION|  
|TYPE|CONTROL|CL|SCHEMA|CONTROL|  
|TYPE|執行 CREATE 陳述式之前，請先執行|EX|SCHEMA|執行 CREATE 陳述式之前，請先執行|  
|TYPE|REFERENCES|RF|SCHEMA|REFERENCES|  
|TYPE|TAKE OWNERSHIP|TO|SCHEMA|CONTROL|  
|TYPE|VIEW DEFINITION|VW|SCHEMA|VIEW DEFINITION|  
|USER|ALTER|AL|DATABASE|ALTER ANY USER|  
|USER|CONTROL|CL|DATABASE|CONTROL|  
|USER|IMPERSONATE|IM|DATABASE|CONTROL|  
|USER|VIEW DEFINITION|VW|DATABASE|VIEW DEFINITION|  
|XML SCHEMA COLLECTION|ALTER|AL|SCHEMA|ALTER|  
|XML SCHEMA COLLECTION|CONTROL|CL|SCHEMA|CONTROL|  
|XML SCHEMA COLLECTION|執行 CREATE 陳述式之前，請先執行|EX|SCHEMA|執行 CREATE 陳述式之前，請先執行|  
|XML SCHEMA COLLECTION|REFERENCES|RF|SCHEMA|REFERENCES|  
|XML SCHEMA COLLECTION|TAKE OWNERSHIP|TO|SCHEMA|CONTROL|  
|XML SCHEMA COLLECTION|VIEW DEFINITION|VW|SCHEMA|VIEW DEFINITION|  
  
##  <a name="summary-of-the-permission-check-algorithm"></a><a name="_algorithm"></a> 權限檢查演算法的摘要  
 檢查權限可能很複雜。 權限檢查演算法包含重疊的群組成員資格和擁有權鏈結 (明確和隱含權限)，而且可能會受到包含安全性實體之安全性實體類別的權限影響。 此演算法的一般程序是收集所有相關的權限。 如果找不到任何封鎖的 DENY，此演算法就會搜尋提供足夠存取權的 GRANT。 此演算法包含三個基本元素： **安全性內容**、 **權限空間** 和 **必要權限**。  
  
> [!NOTE]  
>  您無法授與、拒絕或撤銷 sa、dbo、實體擁有者、information_schema、sys 或自己的權限。  
  
-   **安全性內容**  
  
     這是提供權限給存取權檢查的主體群組。 除非您使用 EXECUTE AS 陳述式，將安全性內容變更為其他登入或使用者，否則這些是與目前登入或使用者相關的權限。 安全性內容包含下列主體：  
  
    -   登入  
  
    -   使用者  
  
    -   角色成員資格  
  
    -   Windows 群組成員資格  
  
    -   如果正在使用模組簽署，則為用來簽署使用者目前正在執行之模組之憑證的任何登入或使用者帳戶，以及該主體的相關聯角色成員資格。  
  
-   **權限空間**  
  
     這是安全性實體以及任何包含此安全性實體的任何安全性實體類別。 例如，資料表 (安全性實體) 會包含在結構描述安全性實體類別和資料庫安全性實體類別中。 存取權可能會受到資料表、結構描述、資料庫和伺服器層級權限影響。 如需詳細資訊，請參閱[權限階層 &#40;Database Engine&#41;](../../relational-databases/security/permissions-hierarchy-database-engine.md)。  
  
-   **必要權限**  
  
     必要的權限種類。 例如，INSERT、UPDATE、DELETE、SELECT、EXECUTE、ALTER、CONTROL 等等。  
  
     存取權可能會要求多種權限，如下列範例所示：  
  
    -   預存程序可能會要求預存程序的 EXECUTE 權限以及此預存程序所參考之若干資料表的 INSERT 權限。  
  
    -   動態管理檢視可能會要求此檢視的 VIEW SERVER STATE 和 SELECT 權限。  
  
### <a name="general-steps-of-the-algorithm"></a>此演算法的一般步驟  
 當此演算法正在判斷是否要允許存取安全性實體時，它所使用的確切步驟可能會因所涉及的主體和安全性實體而有所不同。 不過，此演算法會執行下列一般步驟：  
  
1.  如果登入是系統管理員 (sysadmin) 固定伺服器角色的成員，或者使用者是目前資料庫的 dbo 使用者，就會略過權限檢查。  
  
2.  如果擁有權鏈結適用，而且先前鏈結中的物件存取權檢查通過了安全性檢查，就會允許存取。  
  
3.  彙總與呼叫者相關聯的伺服器層級、資料庫層級和簽署模組識別，以便建立 **安全性內容**。  
  
4.  針對該 **安全性內容**，收集授與或拒絕 **權限空間** 的所有權限。 您可以將權限明確陳述為 GRANT、GRANT WITH GRANT 或 DENY，否則這些權限可能是隱含或涵蓋的權限 GRANT 或 DENY。 例如，結構描述的 CONTROL 權限隱含資料表的 CONTROL， 而且資料表的 CONTROL 隱含 SELECT。 因此，如果您已授與結構描述的 CONTROL，就會授與資料表的 SELECT。 如果您已拒絕資料表的 CONTROL，就會拒絕資料表的 SELECT。  
  
    > [!NOTE]  
    >  資料行層級權限的 GRANT 會覆寫物件層級的 DENY。  
  
5.  識別 **必要權限**。  
  
6.  如果您在 **權限空間** 中針對物件 **安全性內容** 的任何識別直接或隱含拒絕 **必要權限**，權限檢查就會失敗。  
  
7.  如果您沒有拒絕 **必要權限** ，而且 **必要權限** 包含 **權限空間** 中任何物件 **安全性內容** 之任何識別的直接或隱含 GRANT 或 GRANT WITH GRANT 權限，就會通過權限檢查。  

## <a name="special-considerations-for-column-level-permissions"></a>資料行層級權限的特殊考量

資料行層級權限授與使用語法 *<table_name>(\<column _name>)* 。 例如：
```sql
GRANT SELECT ON OBJECT::Customer(CustomerName) TO UserJoe;
```
資料行上的 GRANT 會覆寫資料表上的 DENY。 不過，後續資料表上的 DENY 將會移除資料行的 GRANT。 
  
##  <a name="examples"></a><a name="_examples"></a> 範例  
 本節中的範例示範如何擷取權限資訊。  
  
### <a name="a-returning-the-complete-list-of-grantable-permissions"></a>A. 傳回完整的可授與權限清單  
 下列陳述式使用 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 函數傳回所有 `fn_builtin_permissions` 權限。 如需詳細資訊，請參閱 [sys.fn_builtin_permissions &#40;Transact-SQL&#41;](../../relational-databases/system-functions/sys-fn-builtin-permissions-transact-sql.md)。  
  
```sql  
SELECT * FROM fn_builtin_permissions(default);  
GO  
```  
  
### <a name="b-returning-the-permissions-on-a-particular-class-of-objects"></a>B. 傳回特定物件類別的權限  
 下列範例使用 `fn_builtin_permissions` 檢視可供安全性實體類別目錄使用的所有權限。 範例會傳回組件的權限。  
  
```sql  
SELECT * FROM fn_builtin_permissions('assembly');  
GO    
```  
  
### <a name="c-returning-the-permissions-granted-to-the-executing-principal-on-an-object"></a>C. 傳回授與物件上執行中主體的權限  
 下列範例使用 `fn_my_permissions` 來傳回指定安全性實體之呼叫主體所持有的有效權限清單。 範例會傳回名為 `Orders55` 之物件的權限。 如需詳細資訊，請參閱 [sys.fn_builtin_permissions &#40;Transact-SQL&#41;](../../relational-databases/system-functions/sys-fn-my-permissions-transact-sql.md)。  
  
```sql  
SELECT * FROM fn_my_permissions('Orders55', 'object');  
GO  
```  
  
### <a name="d-returning-the-permissions-applicable-to-a-specified-object"></a>D. 傳回適用於指定物件的權限  
 下列範例會傳回適用於名為 `Yttrium`之物件的權限。 請注意，此內建函數 `OBJECT_ID` 是用來擷取物件 `Yttrium`的識別碼。  
  
```sql  
SELECT * FROM sys.database_permissions   
    WHERE major_id = OBJECT_ID('Yttrium');  
GO  
```  
  
## <a name="see-also"></a>另請參閱  
 [權限階層 &#40;Database Engine&#41;](../../relational-databases/security/permissions-hierarchy-database-engine.md)   
 [sys.database_permissions &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-database-permissions-transact-sql.md)  
  
  
