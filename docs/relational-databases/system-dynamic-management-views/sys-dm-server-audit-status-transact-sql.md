---
description: sys.dm_server_audit_status (Transact-SQL)
title: sys.dm_server_audit_status (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 04/19/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- dm_server_audit_status_TSQL
- sys.dm_server_audit_status
- dm_server_audit_status
- sys.dm_server_audit_status_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_server_audit_status dynamic management view
ms.assetid: 4aa32d54-2ae1-437e-bbaa-7f1df1404b44
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: bcb3334eb564eab7a30aa544c35b74aa89c69ccb
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98096499"
---
# <a name="sysdm_server_audit_status-transact-sql"></a>sys.dm_server_audit_status (Transact-SQL)
[!INCLUDE [SQL Server - ASDBMI](../../includes/applies-to-version/sql-asdbmi.md)]

  針對每個伺服器稽核各傳回一個資料列，表示此稽核的目前狀態。 如需詳細資訊，請參閱 [SQL Server Audit &#40;Database Engine&#41;](../../relational-databases/security/auditing/sql-server-audit-database-engine.md)。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**audit_id**|**int**|稽核的識別碼。 對應至 **sys. 審核** 目錄檢視中的 **audit_id** 欄位。|  
|**name**|**sysname**|稽核的名稱。 與 [ **sys.server_audits** 目錄] 視圖中的 [**名稱**] 欄位相同。|  
|**status**|**smallint**|伺服器稽核的數值狀態：<br /><br /> 0 = 未啟動<br /><br /> 1 =<br />        已開始<br /><br /> 2 =<br />      執行時間失敗<br /><br /> 3 = 目標建立失敗<br /><br /> 4 = 正在關閉|  
|**status_desc**|**nvarchar(256)**|顯示伺服器稽核狀態的字串：<br /><br /> NOT_STARTED<br /><br /> STARTED<br /><br /> RUNTIME_FAIL<br /><br /> TARGET_CREATION_FAILED<br /><br /> SHUTTING_DOWN|  
|**status_time**|**datetime2**|稽核之上一次狀態變更的時間戳記 (以 UTC 為單位)。|  
|**event_session_address**|**varbinary(8)**|與稽核相關聯之擴充的事件工作階段的位址。 與 **sys.dm_xe_sessions. 位址** 目錄檢視相關。|  
|**audit_file_path**|**nvarchar(256)**|目前正在使用之稽核檔案目標的完整路徑和檔案名稱。 只會針對檔案稽核填入。|  
|**audit_file_size**|**bigint**|稽核檔案的近似大小 (以位元組為單位)。 只會針對檔案稽核填入。|  
  
## <a name="permissions"></a>權限  
 主體必須具有 **VIEW SERVER STATE** 許可權。  
  
 [!INCLUDE[ssCatViewPerm](../../includes/sscatviewperm-md.md)] 如需相關資訊，請參閱 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)。  
  
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
 [sys.dm_server_audit_status](../../relational-databases/system-dynamic-management-views/sys-dm-server-audit-status-transact-sql.md)   
 [sys.dm_audit_actions &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-audit-actions-transact-sql.md)   
 [sys.dm_audit_class_type_map &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-audit-class-type-map-transact-sql.md)   
 [建立伺服器稽核與伺服器稽核規格](../../relational-databases/security/auditing/create-a-server-audit-and-server-audit-specification.md)  
  
  
