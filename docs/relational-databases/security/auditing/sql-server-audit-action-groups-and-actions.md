---
title: SQL Server Audit 動作群組和動作 | Microsoft Docs
description: 了解伺服器層級、資料庫層級和稽核層級的動作群組，以及 SQL Server Audit 中的個別動作。
ms.custom: ''
ms.date: 07/13/2020
ms.prod: sql
ms.prod_service: security
ms.reviewer: vanto
ms.technology: security
ms.topic: conceptual
f1_keywords:
- audit
helpviewer_keywords:
- audit actions [SQL Server]
- audits [SQL Server], groups
- server-level audit actions [SQL Server]
- SQL Server Audit
- audit-level audit actions [SQL Server]
- database-level audit actions [SQL Server]
- audit action groups [SQL Server]
- audits [SQL Server], actions
ms.assetid: b7422911-7524-4bcd-9ab9-e460d5897b3d
author: DavidTrigano
ms.author: datrigan
ms.openlocfilehash: e435d8c94dfdfc8f989875d48440554e04405376
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98172490"
---
# <a name="sql-server-audit-action-groups-and-actions"></a>SQL Server Audit 動作群組和動作
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]
  [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Audit 功能可讓您稽核伺服器層級和資料庫層級的事件群組和個別事件。 如需詳細資訊，請參閱 [SQL Server Audit &#40;Database Engine&#41;](../../../relational-databases/security/auditing/sql-server-audit-database-engine.md)。  
  
 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 稽核是由零或多個稽核動作項目所組成。 這些稽核動作項目可以是一組動作 (如 Server_Object_Change_Group) 或個別動作 (如資料表上的 SELECT 作業)。  
  
> [!NOTE]  
>  Server_Object_Change_Group 會針對任何伺服器物件 (資料庫或端點) 包含 CREATE、ALTER 和 DROP。  
  
 稽核可具有以下類別目錄的動作：  
  
-   伺服器層級。 這些動作包含伺服器作業，例如管理變更及登入和登出作業。  
  
-   資料庫層級。 這些動作包括資料操作語言 (DML) 及資料定義語言 (DDL) 作業。  
  
-   稽核層級。 這些動作包含稽核程序中的動作。  
  
 在 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 稽核元件上執行的某些動作會在特定稽核中進行內部稽核，而且在這些情況下，稽核事件會自動發生，因為此事件發生在父物件上。  
  
 下列動作是在內部稽核：  
  
-   伺服器稽核狀態變更 (將狀態設定為 ON 或 OFF)  
  
 下列事件不是在內部稽核：  
  
-   CREATE SERVER AUDIT SPECIFICATION  
  
-   ALTER SERVER AUDIT SPECIFICATION  
  
-   DROP SERVER AUDIT SPECIFICATION  
  
-   CREATE DATABASE AUDIT SPECIFICATION  
  
-   ALTER DATABASE AUDIT SPECIFICATION  
  
-   DROP DATABASE AUDIT SPECIFICATION  
  
 所有稽核在最初建立時都是停用的。  
  
## <a name="server-level-audit-action-groups"></a>伺服器層級的稽核動作群組  
 伺服器層級的稽核動作群組和動作類似於 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Security Audit 事件類別。 如需詳細資訊，請參閱 [SQL Server Event Class Reference](../../../relational-databases/event-classes/sql-server-event-class-reference.md)。  
  
 下表說明伺服器層級的稽核動作群組，並提供適用的同等 SQL Server 事件類別。  
  
|動作群組名稱|描述|  
|-----------------------|-----------------|  
|APPLICATION_ROLE_CHANGE_PASSWORD_GROUP|每當變更應用程式角色的密碼時，就會引發這個事件。 等於＜ [Audit App Role Change Password Event Class](../../../relational-databases/event-classes/audit-app-role-change-password-event-class.md)＞。|  
|AUDIT_CHANGE_GROUP|每當建立、修改或刪除稽核時，就會引發這個事件。 每當建立、修改或刪除任何稽核規格時，就會引發這個事件。 稽核的任何變更都會在該稽核中進行稽核。 等於＜ [Audit Change Audit Event Class](../../../relational-databases/event-classes/audit-change-audit-event-class.md)＞。|  
|BACKUP_RESTORE_GROUP|每當發出備份或還原命令時，就會引發這個事件。 相當於 [Audit Backup 和 Restore 事件類別](../../../relational-databases/event-classes/audit-backup-and-restore-event-class.md)。|  
|BATCH_COMPLETED_GROUP|每當任何批次文字、預存程序或交易管理作業完成執行時，就會引發這個事件。 此事件會如同從用戶端傳送一樣在執行前引發，且會稽核整個批次或預存程序文字。 **在 SQL Server 2019 中新增。**|  
|BATCH_STARTED_GROUP|每當任何批次文字、預存程序或交易管理作業開始執行時，就會引發這個事件。 此事件會如同從用戶端傳送一樣在執行前引發，且會稽核整個批次或預存程序文字。 **在 SQL Server 2019 中新增。**|  
|BROKER_LOGIN_GROUP|引發這個事件來報告與 Service Broker 傳輸安全性相關的稽核訊息。 等於＜ [Audit Broker Login Event Class](../../../relational-databases/event-classes/audit-broker-login-event-class.md)＞。|  
|DATABASE_CHANGE_GROUP|當建立、改變或卸除資料庫時，就會引發這個事件。 每當建立、改變或卸除任何資料庫時，就會引發這個事件。 等於＜ [Audit Database Management Event Class](../../../relational-databases/event-classes/audit-database-management-event-class.md)＞。|  
|DATABASE_LOGOUT_GROUP|當自主資料庫使用者登出與 [Audit Logout 事件類別](../../../relational-databases/event-classes/audit-logout-event-class.md)相等的資料庫時，就會引發這個事件。|  
|DATABASE_MIRRORING_LOGIN_GROUP|引發這個事件來報告與資料庫鏡像傳輸安全性相關的稽核訊息。 等於＜ [Audit Database Mirroring Login Event Class](../../../relational-databases/event-classes/audit-database-mirroring-login-event-class.md)＞。|  
|DATABASE_OBJECT_ACCESS_GROUP|每當存取類似訊息類型、組件和合約等資料庫物件時，就會引發這個事件。 任何資料庫中的任何存取都會引發這個事件。 注意:這可能會產生大型稽核記錄。<br /><br /> 等於＜ [Audit Database Object Access Event Class](../../../relational-databases/event-classes/audit-database-object-access-event-class.md)＞。|  
|DATABASE_OBJECT_CHANGE_GROUP|在資料庫物件 (如結構描述) 上執行 CREATE、ALTER 或 DROP 陳述式時，就會引發這個事件。 每當建立、改變或卸除任何資料庫物件時，就會引發這個事件。 注意:這可能會產生非常大量的稽核記錄。<br /><br /> 等於＜ [Audit Database Object Management Event Class](../../../relational-databases/event-classes/audit-database-object-management-event-class.md)＞。|  
|DATABASE_OBJECT_OWNERSHIP_CHANGE_GROUP|當資料庫範圍內的物件擁有者發生變更時，就會引發這個事件。 伺服器上任何資料庫的任何物件擁有權變更都會引發這個事件。 等於＜ [Audit Database Object Take Ownership Event Class](../../../relational-databases/event-classes/audit-database-object-take-ownership-event-class.md)＞。|  
|DATABASE_OBJECT_PERMISSION_CHANGE_GROUP|對組件和結構描述等資料庫物件發出 GRANT、REVOKE 或 DENY 時，就會引發這個事件。 伺服器上任何資料庫的任何物件權限變更都會引發這個事件。 等於＜ [Audit Database Object GDR Event Class](../../../relational-databases/event-classes/audit-database-object-gdr-event-class.md)＞。|  
|DATABASE_OPERATION_GROUP|當發生資料庫中的作業 (如檢查點或訂閱查詢通知) 時，將會引發這個事件。 任何資料庫中的任何資料庫作業都會引發這個事件。 等於＜ [Audit Database Operation Event Class](../../../relational-databases/event-classes/audit-database-operation-event-class.md)＞。|  
|DATABASE_OWNERSHIP_CHANGE_GROUP|當您使用 ALTER AUTHORIZATION 陳述式來變更資料庫擁有者，而且已檢查完成此作業所需的權限時，就會引發這個事件。 伺服器上任何資料庫的任何資料庫擁有權變更都會引發這個事件。 等於＜ [Audit Change Database Owner Event Class](../../../relational-databases/event-classes/audit-change-database-owner-event-class.md)＞。|  
|DATABASE_PERMISSION_CHANGE_GROUP|每當 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 中的任何主體對陳述式權限發出 GRANT、REVOKE 或 DENY 時，就會引發這個事件 (這適用於僅限資料庫的事件，例如授與資料庫的權限)。<br /><br /> 伺服器上任何資料庫的任何資料庫權限變更 (GDR) 都會引發這個事件。 等於＜ [Audit Database Scope GDR Event Class](../../../relational-databases/event-classes/audit-database-scope-gdr-event-class.md)＞。|  
|DATABASE_PRINCIPAL_CHANGE_GROUP|從資料庫建立、改變或卸除類似使用者的主體時，就會引發這個事件。 等於＜ [Audit Database Principal Management Event Class](../../../relational-databases/event-classes/audit-database-principal-management-event-class.md)＞。 (也等於 Audit Add DB Principal 事件類別，它發生於已被取代的 sp_grantdbaccess、sp_revokedbaccess、sp_addPrincipal 和 sp_dropPrincipal 預存程序上)。<br /><br /> 每當使用 sp_addrole、sp_droprole 預存程序來加入或移除資料庫角色時，就會引發這個事件。 每當從任何資料庫建立、改變或卸除任何資料庫主體時，就會引發這個事件。 等於＜ [Audit Add Role Event Class](../../../relational-databases/event-classes/audit-add-role-event-class.md)＞。|  
|DATABASE_PRINCIPAL_IMPERSONATION_GROUP|當資料庫範圍內有模擬作業 (例如 EXECUTE AS \<principal> 或 SETPRINCIPAL) 時，就會引發這個事件。 這個事件是針對任何資料庫中進行的模擬作業而引發。 等於＜ [Audit Database Principal Impersonation Event Class](../../../relational-databases/event-classes/audit-database-principal-impersonation-event-class.md)＞。|  
|DATABASE_ROLE_MEMBER_CHANGE_GROUP|每當從資料庫角色中加入或移除登入時，就會引發這個事件。 這個事件類別會針對 sp_addrolemember、sp_changegroup 及 sp_droprolemember 預存程序而引發。 任何資料庫中的任何資料庫角色成員變更都會引發這個事件。 等於＜ [Audit Add Member to DB Role 事件類別](../../../relational-databases/event-classes/audit-add-member-to-db-role-event-class.md)＞。|  
|DBCC_GROUP|每當主體發出任何 DBCC 命令時，就會引發這個事件。 等於＜ [Audit DBCC Event Class](../../../relational-databases/event-classes/audit-dbcc-event-class.md)＞。|  
|FAILED_DATABASE_AUTHENTICATION_GROUP|表示某個主體嘗試登入自主資料庫卻失敗。 此類別中的事件是由新連接引發，或是由連接集區中重複使用的連接所引發。 等於＜ [Audit Login Failed Event Class](../../../relational-databases/event-classes/audit-login-failed-event-class.md)＞。|    
|FAILED_LOGIN_GROUP|表示主體嘗試登入 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 但失敗。 此類別中的事件是由新連接引發，或是由連接集區中重複使用的連接所引發。 等於＜ [Audit Login Failed Event Class](../../../relational-databases/event-classes/audit-login-failed-event-class.md)＞。 此稽核不適用於 Azure SQL Database。| 
|FULLTEXT_GROUP|表示發生全文檢索事件。 等於＜ [Audit Fulltext Event Class](../../../relational-databases/event-classes/audit-fulltext-event-class.md)＞。|  
|LOGIN_CHANGE_PASSWORD_GROUP|每當透過 ALTER LOGIN 陳述式或 sp_password 預存程序變更登入的密碼時，就會引發這個事件。 等於＜ [Audit Login Change Password Event Class](../../../relational-databases/event-classes/audit-login-change-password-event-class.md)＞。|  
|LOGOUT_GROUP|表示主體已登出 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]。 此類別中的事件是由新連接引發，或是由連接集區中重複使用的連接所引發。 等於＜ [Audit Logout Event Class](../../../relational-databases/event-classes/audit-logout-event-class.md)＞。|  
|SCHEMA_OBJECT_ACCESS_GROUP|每當在結構描述中使用物件權限時，都會引發這個事件。 等於＜ [Audit Schema Object Access Event Class](../../../relational-databases/event-classes/audit-schema-object-access-event-class.md)＞。|  
|SCHEMA_OBJECT_CHANGE_GROUP|在結構描述上執行 CREATE、ALTER 或 DROP 作業時，就會引發這個事件。 等於＜ [Audit Schema Object Management Event Class](../../../relational-databases/event-classes/audit-schema-object-management-event-class.md)＞。<br /><br /> 這個事件會在結構描述物件上引發。 等於＜ [Audit Object Derived Permission Event Class](../../../relational-databases/event-classes/audit-object-derived-permission-event-class.md)＞。<br /><br /> 每當任何資料庫的任何結構描述變更時，就會引發這個事件。 等於＜ [Audit Statement Permission Event Class](../../../relational-databases/event-classes/audit-statement-permission-event-class.md)＞。|  
|SCHEMA_OBJECT_OWNERSHIP_CHANGE_GROUP|當檢查是否有變更結構描述物件 (例如，資料表、程序或函數) 之擁有者的權限時，就會引發這個事件。 這是在使用 ALTER AUTHORIZATION 陳述式指派物件的擁有者時發生。 伺服器上任何資料庫的任何結構描述擁有權變更都會引發這個事件。 等於＜ [Audit Schema Object Take Ownership Event Class](../../../relational-databases/event-classes/audit-schema-object-take-ownership-event-class.md)＞。|  
|SCHEMA_OBJECT_PERMISSION_CHANGE_GROUP|針對結構描述物件執行 grant、deny、revoke 時，就會引發這個事件。 等於＜ [Audit Schema Object GDR Event Class](../../../relational-databases/event-classes/audit-schema-object-gdr-event-class.md)＞。|  
|SERVER_OBJECT_CHANGE_GROUP|這個事件會針對伺服器物件上的 CREATE、ALTER 或 DROP 作業而引發。 等於＜ [Audit Server Object Management Event Class](../../../relational-databases/event-classes/audit-server-object-management-event-class.md)＞。|  
|SERVER_OBJECT_OWNERSHIP_CHANGE_GROUP|當伺服器範圍內的物件擁有者改變時，就會引發這個事件。 等於＜ [Audit Server Object Take Ownership Event Class](../../../relational-databases/event-classes/audit-server-object-take-ownership-event-class.md)＞。|  
|SERVER_OBJECT_PERMISSION_CHANGE_GROUP|每當 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]中的任何主體對伺服器物件權限發出 GRANT、REVOKE 或 DENY 時，就會引發這個事件。 等於＜ [Audit Server Object GDR Event Class](../../../relational-databases/event-classes/audit-server-object-gdr-event-class.md)＞。|  
|SERVER_OPERATION_GROUP|當使用安全性稽核作業 (如改變設定、資源、外部存取或權限) 時，將會引發這個事件。 等於＜ [Audit Server Operation Event Class](../../../relational-databases/event-classes/audit-server-operation-event-class.md)＞。|  
|SERVER_PERMISSION_CHANGE_GROUP|當針對伺服器範圍的權限發出 GRANT、REVOKE 或 DENY 時，就會引發這個事件。 等於＜ [Audit Server Scope GDR Event Class](../../../relational-databases/event-classes/audit-server-scope-gdr-event-class.md)＞。|  
|SERVER_PRINCIPAL_CHANGE_GROUP|當建立、改變或卸除伺服器主體時，就會引發這個事件。 等於＜ [Audit Server Principal Management Event Class](../../../relational-databases/event-classes/audit-server-principal-management-event-class.md)＞。<br /><br /> 每當主體發出 sp_defaultdb 或 sp_defaultlanguage 預存程序或是 ALTER LOGIN 陳述式時，就會引發這個事件。 等於＜ [Audit Addlogin Event Class](../../../relational-databases/event-classes/audit-addlogin-event-class.md)＞。<br /><br /> 這個事件會在 sp_addlogin 和 sp_droplogin 預存程序上引發。 也等於＜ [Audit Login Change Property Event Class](../../../relational-databases/event-classes/audit-login-change-property-event-class.md)＞。<br /><br /> 這個事件會針對 sp_grantlogin 或 sp_revokelogin 預存程序而引發。 等於＜ [Audit Login GDR Event Class](../../../relational-databases/event-classes/audit-login-gdr-event-class.md)＞。|  
|SERVER_PRINCIPAL_IMPERSONATION_GROUP|當伺服器範圍內有模擬情況 (例如 EXECUTE AS \<login>) 時，就會引發這個事件。 等於＜ [Audit Server Principal Impersonation Event Class](../../../relational-databases/event-classes/audit-server-principal-impersonation-event-class.md)＞。|  
|SERVER_ROLE_MEMBER_CHANGE_GROUP|每當從固定伺服器角色中加入或移除登入時，就會引發這個事件。 此事件會針對 sp_addsrvrolemember 和 sp_dropsrvrolemember 預存程序而引發。 等於＜ [Audit Add Login to Server Role 事件類別](../../../relational-databases/event-classes/audit-add-login-to-server-role-event-class.md)＞。|  
|SERVER_STATE_CHANGE_GROUP|當修改 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 服務狀態時，就會引發這個事件。 等於＜ [Audit Server Starts and Stops Event Class](../../../relational-databases/event-classes/audit-server-starts-and-stops-event-class.md)＞。|  
|SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP|表示主體已成功登入自主資料庫。|  
|SUCCESSFUL_LOGIN_GROUP|表示主體已成功登入 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]。 此類別中的事件是由新連接引發，或是由連接集區中重複使用的連接所引發。 等於＜ [Audit Login Event Class](../../../relational-databases/event-classes/audit-login-event-class.md)＞。|  
|TRACE_CHANGE_GROUP|這個事件會針對檢查是否有 ALTER TRACE 權限的所有陳述式而引發。 等於＜ [Audit Server Alter Trace Event Class](../../../relational-databases/event-classes/audit-server-alter-trace-event-class.md)＞。|  
|TRANSACTION_GROUP|BEGIN TRANSACTION、ROLLBACK TRANSACTION 和 COMMIT TRANSACTION 作業，明確呼叫這些陳述式和隱含的交易作業，都會引發這個事件。 交易回復所造成的個別陳述式 UNDO 作業也會引發這個事件。|  
|USER_CHANGE_PASSWORD_GROUP|每當使用 ALTER USER 陳述式來變更自主資料庫使用者的密碼時，就會引發這個事件。|  
|USER_DEFINED_AUDIT_GROUP|這個群組會監視使用 [sp_audit_write &#40;Transact-SQL&#41;](../../../relational-databases/system-stored-procedures/sp-audit-write-transact-sql.md) 所引發的事件。 觸發程序或預存程序通常會包括 **sp_audit_write** 的呼叫，以便啟用重要事件的稽核。|  
  
### <a name="considerations"></a>考量  
 伺服器層級的動作群組涵蓋跨 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體的動作。 例如，如果將適當的動作群組加入伺服器稽核規格，就會記錄任何資料庫中的任何結構描述物件存取檢查。 在資料庫稽核規格中，只會記錄該資料庫中的結構描述物件存取。  
  
 伺服器層級的動作不允許詳細篩選資料庫層級的動作。 必須有資料庫層級稽核 (例如 Employee 群組登入之 Customers 資料表的 SELECT 動作稽核)，才能實作詳細動作篩選。 請勿在使用者資料庫稽核規格中包含伺服器範圍的物件，例如系統檢視表。  

 > [!NOTE]
 > 由於啟用交易層級稽核所涉及的額外負荷之故，從 [!INCLUDE[ssSQL15](../../../includes/sssql16-md.md)] SP2 CU3 和 [!INCLUDE[ssSQL17](../../../includes/sssql17-md.md)] CU4 開始，除非您已啟用通用條件合規性，否則預設會停用交易層級稽核。  如果已停用通用條件合規性，您仍然可以從 TRANSACTION_GROUP 將動作新增至稽核規格，但它不會實際收集任何交易動作。  如果您想要從 TRANSACTION_GROUP 設定任何稽核動作，自 [!INCLUDE[ssSQL15](../../../includes/sssql16-md.md)] SP2 CU3 和 [!INCLUDE[ssSQL17](../../../includes/sssql17-md.md)] CU4 和更新版本開始，請務必透過啟用通用條件合規性來啟用交易層級稽核基礎結構。  請注意，還可以使用從 SP1 CU2 開始的追蹤旗標 3427 來停用 [!INCLUDE[ssSQL15](../../../includes/sssql16-md.md)] 交易層級稽核。
  
## <a name="database-level-audit-action-groups"></a>資料庫層級的稽核動作群組  
 資料庫層級稽核動作群組是類似 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Security Audit 事件類別的動作。 如需有關事件類別的詳細資訊，請參閱＜ [SQL Server Event Class Reference](../../../relational-databases/event-classes/sql-server-event-class-reference.md)＞。  
  
 下表說明資料庫層級的稽核動作群組，並提供適用的同等 SQL Server 事件類別。  
  
|動作群組名稱|描述|  
|-----------------------|-----------------|  
|APPLICATION_ROLE_CHANGE_PASSWORD_GROUP|每當變更應用程式角色的密碼時，就會引發這個事件。 等於＜ [Audit App Role Change Password Event Class](../../../relational-databases/event-classes/audit-app-role-change-password-event-class.md)＞。|  
|AUDIT_CHANGE_GROUP|每當建立、修改或刪除稽核時，就會引發這個事件。 每當建立、修改或刪除任何稽核規格時，就會引發這個事件。 稽核的任何變更都會在該稽核中進行稽核。 等於＜ [Audit Change Audit Event Class](../../../relational-databases/event-classes/audit-change-audit-event-class.md)＞。|  
|BACKUP_RESTORE_GROUP|每當發出備份或還原命令時，就會引發這個事件。 相當於 [Audit Backup 和 Restore 事件類別](../../../relational-databases/event-classes/audit-backup-and-restore-event-class.md)。|  
|BATCH_COMPLETED_GROUP|每當任何批次文字、預存程序或交易管理作業完成執行時，就會引發這個事件。 此事件會如同從用戶端傳送一樣在執行前引發，且會稽核整個批次或預存程序文字。|  
|BATCH_STARTED_GROUP|每當任何批次文字、預存程序或交易管理作業開始執行時，就會引發這個事件。 此事件會如同從用戶端傳送一樣在執行前引發，且會稽核整個批次或預存程序文字。|  
|DATABASE_CHANGE_GROUP|當建立、改變或卸除資料庫時，就會引發這個事件。 等於＜ [Audit Database Management Event Class](../../../relational-databases/event-classes/audit-database-management-event-class.md)＞。|  
|DATABASE_LOGOUT_GROUP|當自主資料庫使用者登出資料庫時，就會引發這個事件。|  
|DATABASE_OBJECT_ACCESS_GROUP|每當存取類似憑證和非對稱金鑰等資料庫物件時，就會引發這個事件。 等於＜ [Audit Database Object Access Event Class](../../../relational-databases/event-classes/audit-database-object-access-event-class.md)＞。|  
|DATABASE_OBJECT_CHANGE_GROUP|在資料庫物件 (如結構描述) 上執行 CREATE、ALTER 或 DROP 陳述式時，就會引發這個事件。 等於＜ [Audit Database Object Management Event Class](../../../relational-databases/event-classes/audit-database-object-management-event-class.md)＞。|  
|DATABASE_OBJECT_OWNERSHIP_CHANGE_GROUP|當資料庫範圍內的物件擁有者發生變更時，就會引發這個事件。 等於＜ [Audit Database Object Take Ownership Event Class](../../../relational-databases/event-classes/audit-database-object-take-ownership-event-class.md)＞。|  
|DATABASE_OBJECT_PERMISSION_CHANGE_GROUP|對組件和結構描述等資料庫物件發出 GRANT、REVOKE 或 DENY 時，就會引發這個事件。 等於＜ [Audit Database Object GDR Event Class](../../../relational-databases/event-classes/audit-database-object-gdr-event-class.md)＞。|  
|DATABASE_OPERATION_GROUP|當發生資料庫中的作業 (如檢查點或訂閱查詢通知) 時，將會引發這個事件。 等於＜ [Audit Database Operation Event Class](../../../relational-databases/event-classes/audit-database-operation-event-class.md)＞。|  
|DATABASE_OWNERSHIP_CHANGE_GROUP|當您使用 ALTER AUTHORIZATION 陳述式來變更資料庫擁有者，而且已檢查完成此作業所需的權限時，就會引發這個事件。 等於＜ [Audit Change Database Owner Event Class](../../../relational-databases/event-classes/audit-change-database-owner-event-class.md)＞。|  
|DATABASE_PERMISSION_CHANGE_GROUP|每當 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 中的任何使用者對陳述式權限發出 GRANT、REVOKE 或 DENY 時，就會引發這個事件 (這適用於僅限資料庫的事件，例如授與資料庫的權限)。 等於＜ [Audit Database Scope GDR Event Class](../../../relational-databases/event-classes/audit-database-scope-gdr-event-class.md)＞。|  
|DATABASE_PRINCIPAL_CHANGE_GROUP|從資料庫建立、改變或卸除類似使用者的主體時，就會引發這個事件。 等於＜ [Audit Database Principal Management Event Class](../../../relational-databases/event-classes/audit-database-principal-management-event-class.md)＞。 也相當於發生在已被取代之 sp_grantdbaccess、sp_revokedbaccess、sp_adduser 和 sp_dropuser 預存程序上的 [Audit Add DB User 事件類別](../../../relational-databases/event-classes/audit-add-db-user-event-class.md)。<br /><br /> 每當使用已被取代的 sp_addrole 和 sp_droprole 預存程序來加入或移除資料庫角色時，就會引發這個事件。 等於＜ [Audit Add Role Event Class](../../../relational-databases/event-classes/audit-add-role-event-class.md)＞。|  
|DATABASE_PRINCIPAL_IMPERSONATION_GROUP|當資料庫範圍內有模擬情況 (例如 EXECUTE AS \<user>) 時，就會引發這個事件。 等於＜ [Audit Database Principal Impersonation Event Class](../../../relational-databases/event-classes/audit-database-principal-impersonation-event-class.md)＞。|  
|DATABASE_ROLE_MEMBER_CHANGE_GROUP|每當從資料庫角色中加入或移除登入時，就會引發這個事件。 這個事件類別會與 sp_addrolemember、sp_changegroup 和 sp_droprolemember 預存程序搭配使用。相當於 [Audit Add Member to DB Role 事件類別](../../../relational-databases/event-classes/audit-add-member-to-db-role-event-class.md)。|  
|DBCC_GROUP|每當主體發出任何 DBCC 命令時，就會引發這個事件。 等於＜ [Audit DBCC Event Class](../../../relational-databases/event-classes/audit-dbcc-event-class.md)＞。|  
|FAILED_DATABASE_AUTHENTICATION_GROUP|表示某個主體嘗試登入自主資料庫卻失敗。 此類別中的事件是由新連接引發，或是由連接集區中重複使用的連接所引發。 此時會引發此事件。|  
|SCHEMA_OBJECT_ACCESS_GROUP|每當在結構描述中使用物件權限時，都會引發這個事件。 等於＜ [Audit Schema Object Access Event Class](../../../relational-databases/event-classes/audit-schema-object-access-event-class.md)＞。|  
|SCHEMA_OBJECT_CHANGE_GROUP|在結構描述上執行 CREATE、ALTER 或 DROP 作業時，就會引發這個事件。 等於＜ [Audit Schema Object Management Event Class](../../../relational-databases/event-classes/audit-schema-object-management-event-class.md)＞。<br /><br /> 這個事件會在結構描述物件上引發。 等於＜ [Audit Object Derived Permission Event Class](../../../relational-databases/event-classes/audit-object-derived-permission-event-class.md)＞。 也等於＜ [Audit Statement Permission Event Class](../../../relational-databases/event-classes/audit-statement-permission-event-class.md)＞。|  
|SCHEMA_OBJECT_OWNERSHIP_CHANGE_GROUP|當檢查是否有變更結構描述物件 (例如，資料表、程序或函數) 之擁有者的權限時，就會引發這個事件。 這是在使用 ALTER AUTHORIZATION 陳述式指派物件的擁有者時發生。 等於＜ [Audit Schema Object Take Ownership Event Class](../../../relational-databases/event-classes/audit-schema-object-take-ownership-event-class.md)＞。|  
|SCHEMA_OBJECT_PERMISSION_CHANGE_GROUP|針對結構描述物件發出授與、拒絕或撤銷時，就會引發這個事件。 等於＜ [Audit Schema Object GDR Event Class](../../../relational-databases/event-classes/audit-schema-object-gdr-event-class.md)＞。|  
|SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP|表示主體已成功登入自主資料庫。|  
|USER_CHANGE_PASSWORD_GROUP|每當使用 ALTER USER 陳述式來變更自主資料庫使用者的密碼時，就會引發這個事件。|  
|USER_DEFINED_AUDIT_GROUP|這個群組會監視使用 [sp_audit_write &#40;Transact-SQL&#41;](../../../relational-databases/system-stored-procedures/sp-audit-write-transact-sql.md) 所引發的事件。|  
  
## <a name="database-level-audit-actions"></a>資料庫層級的稽核動作  
 資料庫層級的動作支援直接在資料庫結構描述和結構描述物件上稽核特定的動作，例如資料表、檢視表、預存程序、函數、擴充預存程序、佇列、同義字。 類型、XML 結構描述集合、資料庫和結構描述則不會稽核。 可在結構描述和資料庫上設定結構描述物件的稽核，這表示指定之結構描述或資料庫包含的所有結構描述物件上的事件都會稽核。 下表說明資料庫層級的稽核動作。  
  
|動作|描述|  
|------------|-----------------|  
|SELECT|每當發出 SELECT 時，就會引發這個事件。|  
|UPDATE|每當發出 UPDATE 時，就會引發這個事件。|  
|Insert|每當發出 INSERT 時，就會引發這個事件。|  
|刪除|每當發出 DELETE 時，就會引發這個事件。|  
|執行 CREATE 陳述式之前，請先執行|每當發出 EXECUTE 時，就會引發這個事件。|  
|RECEIVE|每當發出 RECEIVE 時，就會引發這個事件。|  
|REFERENCES|每當檢查 REFERENCES 權限時，都會引發這個事件。|  
  
### <a name="considerations"></a>考量  
*  資料庫層級的稽核動作不適用於資料行。  
  
*  當查詢處理器參數化查詢時，此參數可能會顯示在稽核事件記錄而非查詢的資料行值中。 
 
*  不會記錄 RPC 陳述式。 
  
## <a name="audit-level-audit-action-groups"></a>稽核層級的稽核動作群組  
 您也可以在稽核程序中稽核動作， 這可以是伺服器範圍或資料庫範圍。 在資料庫範圍中，只發生於資料庫稽核規格。 下表說明稽核層級的稽核動作群組。  
  
|動作群組名稱|描述|  
|-----------------------|-----------------|  
|AUDIT_CHANGE_GROUP|每當發出下列其中一個命令時，就會引發這個事件：<br /><br /> CREATE SERVER AUDIT<br /><br /> ALTER SERVER AUDIT<br /><br /> DROP SERVER AUDIT<br /><br /> CREATE SERVER AUDIT SPECIFICATION<br /><br /> ALTER SERVER AUDIT SPECIFICATION<br /><br /> DROP SERVER AUDIT SPECIFICATION<br /><br /> CREATE DATABASE AUDIT SPECIFICATION<br /><br /> ALTER DATABASE AUDIT SPECIFICATION<br /><br /> DROP DATABASE AUDIT SPECIFICATION|  
  
## <a name="related-content"></a>相關內容  
 [建立伺服器稽核與伺服器稽核規格](../../../relational-databases/security/auditing/create-a-server-audit-and-server-audit-specification.md)  
  
 [建立伺服器稽核和資料庫稽核規格](../../../relational-databases/security/auditing/create-a-server-audit-and-database-audit-specification.md)  
  
 [CREATE SERVER AUDIT &#40;Transact-SQL&#41;](../../../t-sql/statements/create-server-audit-transact-sql.md)  
  
 [ALTER SERVER AUDIT &#40;Transact-SQL&#41;](../../../t-sql/statements/alter-server-audit-transact-sql.md)  
  
 [DROP SERVER AUDIT &#40;Transact-SQL&#41;](../../../t-sql/statements/drop-server-audit-transact-sql.md)  
  
 [CREATE SERVER AUDIT SPECIFICATION &#40;Transact-SQL&#41;](../../../t-sql/statements/create-server-audit-specification-transact-sql.md)  
  
 [ALTER SERVER AUDIT SPECIFICATION &#40;Transact-SQL&#41;](../../../t-sql/statements/alter-server-audit-specification-transact-sql.md)  
  
 [DROP SERVER AUDIT SPECIFICATION &#40;Transact-SQL&#41;](../../../t-sql/statements/drop-server-audit-specification-transact-sql.md)  
  
 [CREATE DATABASE AUDIT SPECIFICATION &#40;Transact-SQL&#41;](../../../t-sql/statements/create-database-audit-specification-transact-sql.md)  
  
 [ALTER DATABASE AUDIT SPECIFICATION &#40;Transact-SQL&#41;](../../../t-sql/statements/alter-database-audit-specification-transact-sql.md)  
  
 [DROP DATABASE AUDIT SPECIFICATION &#40;Transact-SQL&#41;](../../../t-sql/statements/drop-database-audit-specification-transact-sql.md)  
  
 [ALTER AUTHORIZATION &#40;Transact-SQL&#41;](../../../t-sql/statements/alter-authorization-transact-sql.md)  
  
 [sys.fn_get_audit_file &#40;Transact-SQL&#41;](../../../relational-databases/system-functions/sys-fn-get-audit-file-transact-sql.md)  
  
 [sys.server_audits &#40;Transact-SQL&#41;](../../../relational-databases/system-catalog-views/sys-server-audits-transact-sql.md)  
  
 [sys.server_file_audits &#40;Transact-SQL&#41;](../../../relational-databases/system-catalog-views/sys-server-file-audits-transact-sql.md)  
  
 [sys.server_audit_specifications &#40;Transact-SQL&#41;](../../../relational-databases/system-catalog-views/sys-server-audit-specifications-transact-sql.md)  
  
 [sys.server_audit_specification_details &#40;Transact-SQL&#41;](../../../relational-databases/system-catalog-views/sys-server-audit-specification-details-transact-sql.md)  
  
 [sys.database_audit_specifications &#40;Transact-SQL&#41;](../../../relational-databases/system-catalog-views/sys-database-audit-specifications-transact-sql.md)  
  
 [sys.database_audit_specification_details &#40;Transact-SQL&#41;](../../../relational-databases/system-catalog-views/sys-database-audit-specification-details-transact-sql.md)  
  
 [sys.dm_server_audit_status &#40;Transact-SQL&#41;](../../../relational-databases/system-dynamic-management-views/sys-dm-server-audit-status-transact-sql.md)  
  
 [sys.dm_audit_actions &#40;Transact-SQL&#41;](../../../relational-databases/system-dynamic-management-views/sys-dm-audit-actions-transact-sql.md)  
  
 [sys.dm_audit_class_type_map &#40;Transact-SQL&#41;](../../../relational-databases/system-dynamic-management-views/sys-dm-audit-class-type-map-transact-sql.md)  
  
  
