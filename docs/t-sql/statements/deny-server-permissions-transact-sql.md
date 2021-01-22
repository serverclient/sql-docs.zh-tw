---
description: DENY 伺服器權限 (Transact-SQL)
title: DENY 伺服器權限 (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 06/09/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
dev_langs:
- TSQL
helpviewer_keywords:
- permissions [SQL Server], servers
- denying permissions [SQL Server], servers
- servers [SQL Server], permissions
- DENY statement, servers
ms.assetid: 68d6b2a9-c36f-465a-9cd2-01d43a667e99
author: VanMSFT
ms.author: vanto
ms.openlocfilehash: 290b7e30d198f18fc1d07049b80a0cea78673724
ms.sourcegitcommit: 713e5a709e45711e18dae1e5ffc190c7918d52e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98688898"
---
# <a name="deny-server-permissions-transact-sql"></a>DENY 伺服器權限 (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  拒絕伺服器的權限。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
DENY permission [ ,...n ]   
    TO <grantee_principal> [ ,...n ]  
    [ CASCADE ]  
    [ AS <grantor_principal> ]   
  
<grantee_principal> ::= SQL_Server_login   
    | SQL_Server_login_mapped_to_Windows_login  
    | SQL_Server_login_mapped_to_Windows_group  
    | SQL_Server_login_mapped_to_certificate  
    | SQL_Server_login_mapped_to_asymmetric_key  
    | server_role  
  
<grantor_principal> ::= SQL_Server_login   
    | SQL_Server_login_mapped_to_Windows_login  
    | SQL_Server_login_mapped_to_Windows_group  
    | SQL_Server_login_mapped_to_certificate  
    | SQL_Server_login_mapped_to_asymmetric_key  
    | server_role  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *permission*  
 指定可以拒絕的伺服器權限。 如需權限清單，請參閱這個主題稍後的「備註」一節。  
  
 CASCADE  
 表示已對指定主體和對被主體授與權限的所有其他主體拒絕權限。 當主體具有 GRANT OPTION 的權限時，這是必要的。 
  
 TO \<server_principal>  
 指定要拒絕其權限的主體。  
  
 AS \<grantor_principal>  
 指定主體，執行這項查詢的主體會從這個主體衍生權限來拒絕權限。
您可使用 AS principal 子句，來表示記錄為權限拒絕者的主體應為陳述式執行人員以外的主體。 例如，假設使用者 Mary 是 principal_id 12；使用者 Raul 是 principal 15。 Mary 執行 `DENY SELECT ON OBJECT::X TO Steven WITH GRANT OPTION AS Raul;`。現在，即使實際執行陳述式的是使用者 13 (Mary)，sys.database_permissions 資料表仍會指出 deny 陳述式的 grantor_prinicpal_id 是 15 (Raul)。
  
在此陳述式中使用 AS 不代表能模擬其他使用者。    
  
 *SQL_Server_login*  
 指定 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入。  
  
 *SQL_Server_login_mapped_to_Windows_login*  
 指定對應至 Windows 登入的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入。  
  
 *SQL_Server_login_mapped_to_Windows_group*  
 指定對應至 Windows 群組的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入。  
  
 *SQL_Server_login_mapped_to_certificate*  
 指定對應至憑證的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入。  
  
 *SQL_Server_login_mapped_to_asymmetric_key*  
 指定對應至非對稱金鑰的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入。  
  
 *server_role*  
 指定伺服器角色。  
  
## <a name="remarks"></a>備註  
 只有在目前資料庫是 master 的情況下，才能夠拒絕伺服器範圍的權限。  
  
 您可以在 [sys.server_permissions](../../relational-databases/system-catalog-views/sys-server-permissions-transact-sql.md) 目錄檢視中檢視伺服器權限的資訊，而在 [sys.server_principals](../../relational-databases/system-catalog-views/sys-server-principals-transact-sql.md) 目錄檢視中檢視伺服器主體的資訊。 您可以在 [sys.server_role_members](../../relational-databases/system-catalog-views/sys-server-role-members-transact-sql.md) 目錄檢視中檢視伺服器角色成員資格的資訊。  
  
 伺服器是最高層級的權限階層。 下表所列的是可以拒絕之最特定且最有限的伺服器權限。  
  
|伺服器權限|伺服器權限所隱含|  
|-----------------------|----------------------------------|  
|ADMINISTER BULK OPERATIONS|CONTROL SERVER|  
|ALTER ANY AVAILABILITY GROUP<br /><br /> **適用於**： [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 至 [目前版本](../../sql-server/what-s-new-in-sql-server-2016.md))。|CONTROL SERVER|  
|ALTER ANY CONNECTION|CONTROL SERVER|  
|ALTER ANY CREDENTIAL|CONTROL SERVER|  
|ALTER ANY DATABASE|CONTROL SERVER|  
|ALTER ANY ENDPOINT|CONTROL SERVER|  
|ALTER ANY EVENT NOTIFICATION|CONTROL SERVER|  
|ALTER ANY EVENT SESSION|CONTROL SERVER|  
|ALTER ANY LINKED SERVER|CONTROL SERVER|  
|ALTER ANY LOGIN|CONTROL SERVER|  
|ALTER ANY SERVER AUDIT|CONTROL SERVER|  
|ALTER ANY SERVER ROLE<br /><br /> **適用於**： [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 至 [目前版本](../../sql-server/what-s-new-in-sql-server-2016.md))。|CONTROL SERVER|  
|ALTER RESOURCES|CONTROL SERVER|  
|ALTER SERVER STATE|CONTROL SERVER|  
|ALTER SETTINGS|CONTROL SERVER|  
|ALTER TRACE|CONTROL SERVER|  
|AUTHENTICATE SERVER|CONTROL SERVER|  
|CONNECT ANY DATABASE<br /><br /> **適用於**： [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 至 [目前版本](../../sql-server/what-s-new-in-sql-server-2016.md))。|CONTROL SERVER|  
|CONNECT SQL|CONTROL SERVER|  
|CONTROL SERVER|CONTROL SERVER|  
|CREATE ANY DATABASE|ALTER ANY DATABASE|  
|CREATE AVAILABILITY GROUP<br /><br /> **適用於**： [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 至 [目前版本](../../sql-server/what-s-new-in-sql-server-2016.md))。|ALTER ANY AVAILABILITY GROUP|  
|CREATE DDL EVENT NOTIFICATION|ALTER ANY EVENT NOTIFICATION|  
|CREATE ENDPOINT|ALTER ANY ENDPOINT|  
|CREATE SERVER ROLE<br /><br /> **適用於**： [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 至 [目前版本](../../sql-server/what-s-new-in-sql-server-2016.md))。|ALTER ANY SERVER ROLE|  
|CREATE TRACE EVENT NOTIFICATION|ALTER ANY EVENT NOTIFICATION|  
|EXTERNAL ACCESS ASSEMBLY|CONTROL SERVER|  
|IMPERSONATE ANY LOGIN<br /><br /> **適用於**： [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 至 [目前版本](../../sql-server/what-s-new-in-sql-server-2016.md))。|CONTROL SERVER|  
|SELECT ALL USER SECURABLES<br /><br /> **適用於**： [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 至 [目前版本](../../sql-server/what-s-new-in-sql-server-2016.md))。|CONTROL SERVER|  
|SHUTDOWN|CONTROL SERVER|  
|UNSAFE ASSEMBLY|CONTROL SERVER|  
|VIEW ANY DATABASE|VIEW ANY DEFINITION|  
|VIEW ANY DEFINITION|CONTROL SERVER|  
|VIEW SERVER STATE|ALTER SERVER STATE|  
  
 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 中已加入下列三個伺服器權限。  
  
 **CONNECT ANY DATABASE** 權限  
 將 **CONNECT ANY DATABASE** 授與登入，該登入必須連線到目前存在的所有資料庫，以及可能於日後建立的任何新資料庫。 不要在任何資料庫中授與超出連接的任何權限。 結合 **SELECT ALL USER SECURABLES** 或 **VIEW SERVER STATE**，以讓稽核程序檢視 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體的所有資料或所有資料庫狀態。  
  
 **IMPERSONATE ANY LOGIN** 權限  
 授與此權限時，可讓中間層程序在連接到資料庫時模擬連接的用戶端帳戶。 拒絕此權限時，高權限登入可能遭到封鎖，而無法模擬其他登入。 例如，具有 **CONTROL SERVER** 權限的登入可能遭到封鎖，而無法模擬其他登入。  
  
 **SELECT ALL USER SECURABLES** 權限  
 授與此權限時，像是稽核者這類登入就可以檢視使用者可連接之所有資料庫中的資料。 拒絕此權限時，可防止存取不在 **sys** 結構描述中的物件。  
  
## <a name="permissions"></a>權限  
 需要安全性實體的 CONTROL SERVER 權限或擁有權。 如果使用 AS 子句，指定的主體必須擁有要拒絕其權限的安全性實體。  
  
## <a name="examples"></a>範例  
  
### <a name="a-denying-connect-sql-permission-to-a-sql-server-login-and-principals-to-which-the-login-has-regranted-it"></a>A. 對 SQL Server 登入及已對其授與權限的主體，拒絕 CONNECT SQL 權限  
 下列範例會對 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入 `CONNECT SQL` 及她已對其授與權限的主體，拒絕 `Annika` 權限。  
  
```sql  
USE master;  
DENY CONNECT SQL TO Annika CASCADE;  
GO  
```  
  
### <a name="b-denying-create-endpoint-permission-to-a-sql-server-login-using-the-as-option"></a>B. 使用 AS 選項，對 SQL Server 登入拒絕 CREATE ENDPOINT 權限  
 下列範例會對使用者 `CREATE ENDPOINT` 拒絕 `ArifS` 權限。 這個範例利用 `AS` 選項來指定 `MandarP` 作為負責執行的主體從其衍生權限，以執行這項指定作業的主體。  
  
```sql  
USE master;  
DENY CREATE ENDPOINT TO ArifS AS MandarP;  
GO  
```  
  
## <a name="see-also"></a>另請參閱  
 [GRANT &#40;Transact-SQL&#41;](../../t-sql/statements/grant-transact-sql.md)   
 [DENY &#40;Transact-SQL&#41;](../../t-sql/statements/deny-transact-sql.md)   
 [DENY 伺服器權限 (Transact-SQL)](../../t-sql/statements/deny-server-permissions-transact-sql.md)   
 [REVOKE 伺服器權限 &#40;Transact-SQL&#41;](../../t-sql/statements/revoke-server-permissions-transact-sql.md)   
 [權限階層 &#40;Database Engine&#41;](../../relational-databases/security/permissions-hierarchy-database-engine.md)   
 [sys.fn_builtin_permissions &#40;Transact-SQL&#41;](../../relational-databases/system-functions/sys-fn-builtin-permissions-transact-sql.md)   
 [sys.fn_my_permissions &#40;Transact-SQL&#41;](../../relational-databases/system-functions/sys-fn-my-permissions-transact-sql.md)   
 [HAS_PERMS_BY_NAME &#40;Transact-SQL&#41;](../../t-sql/functions/has-perms-by-name-transact-sql.md)  
  
