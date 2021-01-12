---
description: sys.server_file_audits (Transact-SQL)
title: sys.server_file_audits (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 04/05/2016
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- server_file_audits_TSQL
- sys.server_file_audits_TSQL
- sys.server_file_audits
- server_file_audits
dev_langs:
- TSQL
helpviewer_keywords:
- sys.server_file_audits catalog view
ms.assetid: 553288a0-be57-4d79-ae53-b7cbd065e127
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: aa516146c4db55cbfcb3b3f3817cdcf9d11216be
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98096703"
---
# <a name="sysserver_file_audits-transact-sql"></a>sys.server_file_audits (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  包含有關 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 伺服器實例上 audit 中檔案審核類型的延伸資訊。 如需詳細資訊，請參閱 [SQL Server Audit &#40;Database Engine&#41;](../../relational-databases/security/auditing/sql-server-audit-database-engine.md)。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|audit_id|**int**|稽核的識別碼。|  
|NAME|**sysname**|稽核的名稱。|  
|audit_guid|**uniqueidentifier**|稽核的 GUID。|  
|create_date|**datetime**|建立檔案稽核時的 UTC 日期。|  
|modify_date|**datatime**|上次修改檔案稽核的 UTC 日期。|  
|principal_id|**int**|在伺服器上註冊之稽核擁有者的識別碼。|  
|類型|**char(2)**|稽核類型：<br /><br /> 0 = NT 安全性事件記錄檔<br /><br /> 1 = NT 應用程式事件記錄檔<br /><br /> 2 = 檔案系統上的檔案|  
|type_desc|**nvarchar(60)**|稽核類型描述。|  
|on_failure|**tinyint**|失敗時條件。<br /><br /> 0 = 繼續<br /><br /> 1 = 關閉伺服器執行個體<br /><br /> 2 = 讓作業失敗|  
|on_failure_desc|**nvarchar(60)**|寫入動作項目失敗：<br /><br /> CONTINUE<br /><br /> SHUTDOWN SERVER INSTANCE<br /><br /> FAIL OPERATION|  
|is_state_enabled|**tinyint**|0 = 停用<br /><br /> 1 = 啟用|  
|queue_delay|**int**|寫入磁碟前等候的最大時間建議值 (以毫秒計)。 如果為 0，則表示稽核將會保證寫入，然後事件才可以繼續。|  
|predicate|**Nvarchar (8000)**|套用至事件的述詞運算式。|  
|max_file_size|**bigint**|稽核的大小上限 (以 MB 為單位)：<br /><br /> 0 = 選定稽核的類型無限制/不適用。|  
|max_rollover_files|**int**|要搭配換用選項使用的檔案數量上限。|  
|max_files|**int**|不要搭配換用選項使用的檔案數量上限。|  
|reserved_disk_space|**int**|每個檔案保留的磁碟空間數量。|  
|log_file_path|**nvarchar(260)**|稽核所在的路徑。 檔案路徑用於檔案稽核，應用程式記錄檔路徑用於應用程式記錄檔稽核。|  
|log_file_name|**nvarchar(260)**|CREATE AUDIT DDL 中提供之記錄檔的基底名稱。 會有一個累加數字加入 base_log_name 檔案當做後置詞，以建立記錄檔名稱。|  
  
## <a name="permissions"></a>權限  
 具有 **ALTER ANY SERVER AUDIT** 或 **VIEW any DEFINITION** 許可權的主體可存取此目錄檢視。 此外，主體也不得被拒絕 **VIEW ANY DEFINITION** 許可權。  
  
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
 [sys.server_file_audits (Transact-sql) ](../../relational-databases/system-catalog-views/sys-server-file-audits-transact-sql.md)   
 [sys.server_audit_specifications &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-server-audit-specifications-transact-sql.md)   
 [sys.database_audit_specifications &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-database-audit-specifications-transact-sql.md)   
 [sys.database_audit_specification_details &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-database-audit-specification-details-transact-sql.md)   
 [sys.dm_server_audit_status &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-server-audit-status-transact-sql.md)   
 [sys.dm_audit_actions &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-audit-actions-transact-sql.md)   
 [sys.dm_audit_class_type_map &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-audit-class-type-map-transact-sql.md)   
 [建立伺服器稽核與伺服器稽核規格](../../relational-databases/security/auditing/create-a-server-audit-and-server-audit-specification.md)  
  
  
