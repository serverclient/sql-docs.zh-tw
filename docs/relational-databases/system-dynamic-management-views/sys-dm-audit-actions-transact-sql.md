---
description: sys.dm_audit_actions (Transact-SQL)
title: sys.dm_audit_actions (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.dm_audit_actions_TSQL
- sys.dm_audit_actions
- dm_audit_actions_TSQL
- dm_audit_actions
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_audit_actions dynamic management view
ms.assetid: b987c2b9-998a-4a5f-a82d-280dc6963cbe
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 7f880fa229e64990b729d193c6e39bfb05523788
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98095291"
---
# <a name="sysdm_audit_actions-transact-sql"></a>sys.dm_audit_actions (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa.md)]

  傳回稽核記錄檔中可報告的每一個稽核動作及 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Audit 中可設定之每一個稽核動作群組的資料列。 如需有關 audit 的詳細資訊 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ，請參閱 [SQL Server audit &#40;資料庫引擎&#41;](../../relational-databases/security/auditing/sql-server-audit-database-engine.md)。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**action_id**|**varchar(4)**|稽核動作的識別碼。 與寫入每個審核記錄的 **action_id** 值相關。 可為 Null。 稽核群組為 NULL。|  
|**action_in_log**|**bit**|指示動作是否可寫入稽核記錄檔。 其值如下：<br /><br /> 1 = 是<br /><br /> 0 = 否|  
|**name**|**sysname**|稽核動作或稽核群組的名稱。 不可為 Null。|  
|**class_desc**|**nvarchar(120)**|稽核動作套用之物件的類別名稱。 可以是任何一個伺服器、資料庫或結構描述範圍物件，但是不包括結構描述物件。 不可為 Null。|  
|**parent_class_desc**|**nvarchar(120)**|class_desc 所描述之物件的父類別名稱。 如果 class_desc 是伺服器則為 NULL。|  
|**covering_parent_action_name**|**nvarchar(120)**|稽核動作名稱，或是包含此資料列中所述之稽核動作的稽核群組名稱。 這是用來建立動作的階層及涵蓋的動作。 可為 Null。|  
|**configuration_level**|**Nvarchar (10)**|指示此資料列中指定的動作或動作群組可在群組或動作層級上設定。 如果此動作無法設定，則為 NULL。|  
|**containing_group_name**|**nvarchar(120)**|包含指定之動作的稽核群組名稱。 如果名稱中的值是群組，則為 NULL。|  
  
## <a name="permissions"></a>權限  
公開會看到此 view。
  
 [!INCLUDE[ssCatViewPerm](../../includes/sscatviewperm-md.md)]. 如需相關資訊，請參閱 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)。  
  
## <a name="see-also"></a>另請參閱  
 [CREATE SERVER AUDIT &#40;Transact-SQL&#41;](../../t-sql/statements/create-server-audit-transact-sql.md)   
 [ALTER SERVER AUDIT  &#40;Transact-SQL&#41;](../../t-sql/statements/alter-server-audit-transact-sql.md)   
 [DROP SERVER AUDIT  &#40;Transact-SQL&#41;](../../t-sql/statements/drop-server-audit-transact-sql.md)   
 [CREATE SERVER AUDIT SPECIFICATION &#40;Transact-SQL&#41;](../../t-sql/statements/create-server-audit-specification-transact-sql.md)   
 [ALTER SERVER AUDIT SPECIFICATION &#40;Transact-SQL&#41;](../../t-sql/statements/alter-server-audit-specification-transact-sql.md)   
 [DROP SERVER AUDIT SPECIFICATION &#40;Transact-SQL&#41;](../../t-sql/statements/drop-server-audit-specification-transact-sql.md)   
 [CREATE DATABASE AUDIT SPECIFICATION &#40;Transact-SQL&#41;](../../t-sql/statements/create-database-audit-specification-transact-sql.md)   
 [ALTER DATABASE AUDIT SPECIFICATION &#40;Transact-SQL&#41;](../../t-sql/statements/alter-database-audit-specification-transact-sql.md)   
 [DROP DATABASE AUDIT SPECIFICATION &#40;Transact-SQL&#41;](../../t-sql/statements/drop-database-audit-specification-transact-sql.md)   
 [ALTER AUTHORIZATION &#40;Transact-SQL&#41;](../../t-sql/statements/alter-authorization-transact-sql.md)   
 [sys.fn_get_audit_file &#40;Transact-SQL&#41;](../../relational-databases/system-functions/sys-fn-get-audit-file-transact-sql.md)   
 [sys.server_audits &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-server-audits-transact-sql.md)   
 [sys.server_file_audits &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-server-file-audits-transact-sql.md)   
 [sys.server_audit_specifications &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-server-audit-specifications-transact-sql.md)   
 [sys.server_audit_specification_details &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-server-audit-specification-details-transact-sql.md)   
 [sys.database_audit_specifications &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-database-audit-specifications-transact-sql.md)   
 [sys.database_audit_specification_details &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-database-audit-specification-details-transact-sql.md)   
 [sys.dm_server_audit_status &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-server-audit-status-transact-sql.md)   
 [sys.dm_audit_class_type_map &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-audit-class-type-map-transact-sql.md)   
 [建立伺服器稽核與伺服器稽核規格](../../relational-databases/security/auditing/create-a-server-audit-and-server-audit-specification.md)  
  
  
