---
description: REVOKE 伺服器權限 (Transact-SQL)
title: REVOKE 伺服器權限 (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 08/10/2017
ms.prod: sql
ms.prod_service: sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
dev_langs:
- TSQL
helpviewer_keywords:
- permissions [SQL Server], servers
- REVOKE statement, server permissions
- servers [SQL Server], permissions
ms.assetid: 7b9a56b3-face-452e-a655-147dac306ba1
author: VanMSFT
ms.author: vanto
ms.openlocfilehash: d4b4e3a6a9d920d1a4c5b5d3bcc5317cfe115609
ms.sourcegitcommit: 713e5a709e45711e18dae1e5ffc190c7918d52e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98688875"
---
# <a name="revoke-server-permissions-transact-sql"></a>REVOKE 伺服器權限 (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  移除伺服器層級 GRANT 和 DENY 權限。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
REVOKE [ GRANT OPTION FOR ] permission  [ ,...n ]   
    { TO | FROM } <grantee_principal> [ ,...n ]  
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
 指定可以授與的伺服器權限。 如需權限清單，請參閱這個主題稍後的「備註」一節。  
  
 { TO | FROM } \<grantee_principal> 指定要撤銷其權限的主體。  
  
 AS \<grantor_principal> 指定主體，執行這項查詢的主體會從這個主體衍生權利來撤銷權限。  
  
 GRANT OPTION FOR  
 指出會撤銷對其他主體授與指定權限的權限。 不會撤銷權限本身。  
  
> [!IMPORTANT]  
>  如果主體有不含 GRANT 選項的指定權限，則會撤銷權限本身。  
  
 CASCADE  
 指出目前正在撤銷的權限，而這個權限也會被這個主體所授與或拒絕的其他主體所撤銷。  
  
> [!CAUTION]  
>  獲得授與 WITH GRANT OPTION 之權限的串聯撤銷，會同時撤銷該權限的 GRANT 和 DENY。  
  
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
 指定使用者定義伺服器角色。  
  
## <a name="remarks"></a>備註  
 只有在目前資料庫是 master 的情況下，才能夠撤銷伺服器範圍的權限。  
  
 REVOKE 會同時移除 GRANT 和 DENY 權限。  
  
 您可以利用 REVOKE GRANT OPTION FOR 撤銷重新授與指定權限的權限。 如果主體有授與該權限的權限，則會撤銷授與該權限的權限，且不會撤銷該權限本身。 但是，如果主體有不含 GRANT 選項的指定權限，則會撤銷權限本身。  
  
 您可以在 [sys.server_permissions](../../relational-databases/system-catalog-views/sys-server-permissions-transact-sql.md) 目錄檢視中檢視伺服器權限的資訊，而在 [sys.server_principals](../../relational-databases/system-catalog-views/sys-server-principals-transact-sql.md) 目錄檢視中檢視伺服器主體的資訊。 您可以在 [sys.server_role_members](../../relational-databases/system-catalog-views/sys-server-role-members-transact-sql.md) 目錄檢視中檢視伺服器角色成員資格的資訊。  
  
 伺服器是最高層級的權限階層。 下表所列的是可以撤銷之最特定且最有限的伺服器權限。  
  
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
  
## <a name="permissions"></a>權限  
 需要 CONTROL SERVER 權限或系統管理員 (sysadmin) 固定伺服器角色中的成員資格。  
  
## <a name="examples"></a>範例  
  
### <a name="a-revoking-a-permission-from-a-login"></a>A. 從登入撤銷權限  
 下列範例會從 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入 `VIEW SERVER STATE` 撤銷 `WanidaBenshoof` 權限。  
  
```sql  
USE master;  
REVOKE VIEW SERVER STATE FROM WanidaBenshoof;  
GO  
```  
  
### <a name="b-revoking-the-with-grant-option"></a>B. 撤銷 WITH GRANT 選項  
 下列範例會從 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入 `CONNECT SQL` 撤銷授與 `JanethEsteves` 的權限。  
  
```sql  
USE master;  
REVOKE GRANT OPTION FOR CONNECT SQL FROM JanethEsteves;  
GO  
```  
  
 該登入仍有 CONNECT SQL 權限，但它無法再對其他主體授與該權限。  
  
## <a name="see-also"></a>另請參閱  
 [GRANT &#40;Transact-SQL&#41;](../../t-sql/statements/grant-transact-sql.md)   
 [DENY &#40;Transact-SQL&#41;](../../t-sql/statements/deny-transact-sql.md)   
 [DENY 伺服器權限 &#40;Transact-SQL&#41;](../../t-sql/statements/deny-server-permissions-transact-sql.md)   
 [REVOKE 伺服器權限 (Transact-SQL)](../../t-sql/statements/revoke-server-permissions-transact-sql.md)   
 [權限階層 &#40;Database Engine&#41;](../../relational-databases/security/permissions-hierarchy-database-engine.md)   
 [sys.fn_builtin_permissions &#40;Transact-SQL&#41;](../../relational-databases/system-functions/sys-fn-builtin-permissions-transact-sql.md)   
 [sys.fn_my_permissions &#40;Transact-SQL&#41;](../../relational-databases/system-functions/sys-fn-my-permissions-transact-sql.md)   
 [HAS_PERMS_BY_NAME &#40;Transact-SQL&#41;](../../t-sql/functions/has-perms-by-name-transact-sql.md)  
  
