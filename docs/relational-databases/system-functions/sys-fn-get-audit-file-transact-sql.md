---
description: sys.fn_get_audit_file (Transact-SQL)
title: sys.fn_get_audit_file (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 02/19/2020
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- fn_get_audit_file_TSQL
- sys.fn_get_audit_file_TSQL
- fn_get_audit_file
- sys.fn_get_audit_file
dev_langs:
- TSQL
helpviewer_keywords:
- sys.fn_get_audit_file function
- fn_get_audit_file function
ms.assetid: d6a78d14-bb1f-4987-b7b6-579ddd4167f5
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current||=azure-sqldw-latest
ms.openlocfilehash: 1210ec1da44d68aaf778145da8a02bf3f3092e2c
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98093835"
---
# <a name="sysfn_get_audit_file-transact-sql"></a>sys.fn_get_audit_file (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb-asdbmi-asa.md)]    

  從 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 伺服器稽核建立的稽核檔案中傳回資訊。 如需詳細資訊，請參閱 [SQL Server Audit &#40;Database Engine&#41;](../../relational-databases/security/auditing/sql-server-audit-database-engine.md)。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```  
fn_get_audit_file ( file_pattern,   
    { default | initial_file_name | NULL },   
    { default | audit_record_offset | NULL } )  
```  
  
## <a name="arguments"></a>引數  
 *file_pattern*  
 針對要讀取的稽核檔案集合指定目錄或路徑及檔案名稱。 類型為 **Nvarchar (260)**。 
 
 - SQL Server：
    
    這個引數必須同時包含路徑 (磁碟機代號或網路共用位置) 以及可包含萬用字元的檔案名稱。 您可以使用單一星號 ( * ) 來從審核檔案集合收集多個檔案。 例如：  
  
    -   **\<path>\\\** _-收集指定位置中的所有審核檔。  
  
    -   _* \<path> \LoginsAudit_{GUID} * * _-收集具有指定之名稱和 GUID 配對的所有審核檔。  
  
    -   _* \<path> \LoginsAudit_{GUID} _00_29384. sqlaudit * *-收集特定的審核檔。  
  
 - **Azure SQL Database**：
 
    此引數是用來指定 blob URL (包括儲存體端點和容器) 。 雖然不支援星號萬用字元，但您可以使用部分檔案 (blob) 名稱前置詞 (而不是完整的 blob 名稱) 來收集多個檔案， (以這個前置詞開頭的 blob) 。 例如：
 
      - **\<Storage_endpoint\>/\<Container\>/\<ServerName\>/\<DatabaseName\>/** -收集特定資料庫 (blob) 的所有審核檔。    
      
      - **\<Storage_endpoint\> / \<Container\> / \<ServerName\> / \<DatabaseName\> / \<AuditName\> / \<CreationDate\> / \<FileName\> xel** -收集 (blob) 的特定審核檔。
  
> [!NOTE]  
>  如果傳遞路徑而沒有包含檔案名稱模式，則會產生錯誤。  
  
 *initial_file_name*  
 指定稽核檔案集合中要開始讀取稽核記錄之特定檔案的路徑和名稱。 類型為 **Nvarchar (260)**。  
  
> [!NOTE]  
>  *Initial_file_name* 引數必須包含有效的專案，或必須包含預設值 |Null 值。  
  
 *audit_record_offset*  
 指定包含為 initial_file_name 指定之檔案的已知位置。 當使用這個引數時，此函數會開始讀取緩衝區內緊接在指定的位移之後的第一筆記錄。  
  
> [!NOTE]  
>  *Audit_record_offset* 引數必須包含有效的專案，或必須包含預設值 |Null 值。 類型為 **Bigint**。  
  
## <a name="tables-returned"></a>傳回的資料表  
 下表描述這個函數可傳回的稽核檔案內容。  
  
| 資料行名稱 | 類型 | 描述 |  
|-------------|------|-------------|  
| action_id | **varchar(4)** | 動作的識別碼。 不可為 Null。 |  
| additional_information | **nvarchar(4000)** | 只套用到單一事件的唯一資訊會以 XML 形式傳回。 少量的可稽核動作有包含這類資訊。<br /><br /> 針對具有相關聯 TSQL 堆疊的動作，以 XML 格式顯示 TSQL 堆疊的單一層級。 此 XML 格式為：<br /><br /> `<tsql_stack><frame nest_level = '%u' database_name = '%.*s' schema_name = '%.*s' object_name = '%.*s' /></tsql_stack>`<br /><br /> Frame nest_level 表示框架的目前巢狀層級。 模組名稱會以三部分格式表示 (database_name、schema_name 和 object_name)。  將剖析模組名稱，以 escape 不正確 xml 字元，例如、 `'\<'` `'>'` 、 `'/'` 、 `'_x'` 。 它們將會以的形式被轉義 `_xHHHH\_` 。 HHHH 代表字元的四位數十六進位 UCS-2 碼。<br /><br /> 可為 Null。 當此事件未報告其他資訊時，則會傳回 NULL。 |
| affected_rows | **bigint** | **適用于**：僅限 Azure SQL Database<br /><br /> 受執行語句影響的資料列數目。 |  
| application_name | **nvarchar(128)** | **適用于**： Azure SQL Database + SQL Server (從2017開始) <br /><br /> 執行導致 audit 事件之語句的用戶端應用程式名稱 |  
| audit_file_offset | **bigint** | **適用于**：僅限 SQL Server<br /><br /> 檔案中包含稽核記錄的緩衝區位移。 不可為 Null。 |  
| audit_schema_version | **int** | 一律為1 |  
| class_type | **varchar(2)** | 稽核發生所在之可稽核的實體類型。 不可為 Null。 |  
| client_ip | **nvarchar(128)** | **適用于**： Azure SQL Database + SQL Server (從2017開始) <br /><br />  用戶端應用程式的來源 IP |  
| connection_id | GUID | **適用于**： AZURE SQL DATABASE 和 SQL 受控執行個體<br /><br /> 伺服器中的連接識別碼 |
| data_sensitivity_information | nvarchar(4000) | **適用于**：僅限 Azure SQL Database<br /><br /> 經過審核查詢所傳回的資訊類型和敏感度標籤，是根據資料庫中的分類資料行。 深入瞭解 [Azure SQL Database 資料探索和分類](/azure/sql-database/sql-database-data-discovery-and-classification) |
| database_name | **sysname** | 動作發生所在的資料庫環境。 可為 Null。 針對在伺服器層級發生的審核傳回 Null。 |  
| database_principal_id | **int** |動作執行所在之資料庫使用者環境的識別碼。 不可為 Null。 如果不適用則傳回 0。 例如，伺服器作業。|
| database_principal_name | **sysname** | 目前的使用者。 可為 Null。 如果無法使用則傳回 NULL。 |  
| duration_milliseconds | **bigint** | **適用于**： AZURE SQL DATABASE 和 SQL 受控執行個體<br /><br /> 查詢執行持續期間（毫秒） |
| event_time | **datetime2** | 可稽核的動作引發時的日期和時間。 不可為 Null。 |  
| file_name | **varchar(260)** | 記錄來自之稽核記錄檔的路徑和名稱。 不可為 Null。 |
| is_column_permission | **bit** | 指出這是否為資料行層級權限的旗標。 不可為 Null。 當 permission_bitmask = 0 時會傳回 0。<br /> 1 = true<br /> 0 = false |
| object_id | **int** | 稽核發生所在之實體的識別碼。 其中包括下列項目：<br /> 伺服器物件<br /> 資料庫<br /> 資料庫物件<br /> 結構描述物件<br /> 不可為 Null。 如果此實體為伺服器本身或是稽核並未在物件層級上執行，則會傳回 0。 例如驗證。 |  
| object_name | **sysname** | 稽核發生所在之實體的名稱。 其中包括下列項目：<br /> 伺服器物件<br /> 資料庫<br /> 資料庫物件<br /> 結構描述物件<br /> 可為 Null。 如果此實體為伺服器本身或是稽核並未在物件層級上執行，則會傳回 NULL。 例如驗證。 |
| permission_bitmask | **varbinary(16)** | 在某些動作中，這就是已授與、拒絕或撤銷的權限。 |
| response_rows | **bigint** | **適用于**： AZURE SQL DATABASE 和 SQL 受控執行個體<br /><br /> 結果集中傳回的資料列數目。 |  
| schema_name | **sysname** | 動作發生所在的結構描述環境。 可為 Null。 針對在架構之外發生的審核傳回 Null。 |  
| sequence_group_id | **varbinary** | **適用于**：僅 SQL Server 從2016開始 () <br /><br />  唯一識別碼 |  
| sequence_number | **int** | 追蹤單一稽核記錄中太長而無法納入稽核寫入緩衝區內的記錄順序。 不可為 Null。 |  
| server_instance_name | **sysname** | 稽核發生所在的伺服器執行個體名稱。 使用標準的 server\instance 格式。 |  
| server_principal_id | **int** | 動作執行所在之登入環境的識別碼。 不可為 Null。 |  
| server_principal_name | **sysname** | 目前的登入。 可為 Null。 |  
| server_principal_sid | **varbinary** | 目前的登入 SID。 可為 Null。 |  
| session_id | **smallint** | 事件發生所在之工作階段的識別碼。 不可為 Null。 |  
| session_server_principal_name | **sysname** | 工作階段的伺服器主體。 可為 Null。 |  
| 陳述式 | **nvarchar(4000)** | TSQL 陳述式 (如果存在的話)。 可為 Null。 如果不適用則傳回 NULL。 |  
| 成功 | **bit** | 指示觸發此事件的動作是否成功。 不可為 Null。 若為登入事件以外的所有事件，這只會報告權限檢查成功或失敗，而不會報告作業成功或失敗。<br /> 1 = 成功<br /> 0 = 失敗 |
| target_database_principal_id | **int** | GRANT/DENY/REVOKE 作業執行所在的資料庫主體。 不可為 Null。 如果不適用則傳回 0。 |  
| target_database_principal_name | **sysname** | 動作的目標使用者。 可為 Null。 如果不適用則傳回 NULL。 |  
| target_server_principal_id | **int** | GRANT/DENY/REVOKE 作業執行所在的伺服器主體。 不可為 Null。 如果不適用則傳回 0。 |  
| target_server_principal_name | **sysname** | 動作的目標登入。 可為 Null。 如果不適用則傳回 NULL。 |  
| target_server_principal_sid | **varbinary** | 目標登入的 SID。 可為 Null。 如果不適用則傳回 NULL。 |  
| transaction_id | **bigint** | **適用于**：僅 SQL Server 從2016開始 () <br /><br /> 在單一交易中識別多個審核事件的唯一識別碼 |  
| user_defined_event_id | **smallint** | **適用于**： [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 和更新版本、Azure SQL Database 和 SQL 受控執行個體<br /><br /> 以引數形式傳遞給 **sp_audit_write** 的使用者定義事件識別碼。 系統事件的 **Null** (預設值) ，而使用者定義的事件則為非零。 如需詳細資訊，請參閱 [sp_audit_write &#40;transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-audit-write-transact-sql.md)。 |  
| user_defined_information | **nvarchar(4000)** | **適用于**： [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 和更新版本、Azure SQL Database 和 SQL 受控執行個體<br /><br /> 用來記錄使用者想要使用 **sp_audit_write** 預存程式，在 audit 記錄檔中記錄的任何額外資訊。 |  

  
## <a name="remarks"></a>備註  
 如果傳遞至 **fn_get_audit_file** 的 *file_pattern* 引數參考不存在的路徑或檔案，或如果檔案不是 audit 檔案，則會傳回 **MSG_INVALID_AUDIT_FILE** 錯誤訊息。  
  
## <a name="permissions"></a>權限

- **SQL Server**：需要 **CONTROL Server** 許可權。  
- **Azure SQL Database**：需要 **CONTROL Database** 許可權。     
  - 伺服器管理員可以存取伺服器上所有資料庫的 audit 記錄檔。
  - 非伺服器管理員只能從目前的資料庫存取 audit 記錄。
  - 將會略過不符合上述準則的 blob， (已跳過的 blob 清單會顯示在查詢輸出訊息中) ，而且此函式只會從允許存取的 blob 傳回記錄。  
  
## <a name="examples"></a>範例

- **SQL Server**

  此範例會從名為 `\\serverName\Audit\HIPAA_AUDIT.sqlaudit` 的檔案進行讀取。  
  
  ```  
  SELECT * FROM sys.fn_get_audit_file ('\\serverName\Audit\HIPAA_AUDIT.sqlaudit',default,default);  
  GO  
  ```  

- **Azure SQL Database**

  此範例會從名為的檔案讀取 `ShiraServer/MayaDB/SqlDbAuditing_Audit/2017-07-14/10_45_22_173_1.xel` ：  
  
  ```  
  SELECT * FROM sys.fn_get_audit_file ('https://mystorage.blob.core.windows.net/sqldbauditlogs/ShiraServer/MayaDB/SqlDbAuditing_Audit/2017-07-14/10_45_22_173_1.xel',default,default);
  GO  
  ```  

  此範例會讀取上述相同檔案，但使用額外的 T-sql 子句 (**TOP**、 **ORDER BY** 和 **WHERE** 子句，以篩選函式所傳回的審核記錄) ：
  
  ```  
  SELECT TOP 10 * FROM sys.fn_get_audit_file ('https://mystorage.blob.core.windows.net/sqldbauditlogs/ShiraServer/MayaDB/SqlDbAuditing_Audit/2017-07-14/10_45_22_173_1.xel',default,default)
  WHERE server_principal_name = 'admin1'
  ORDER BY event_time
  GO
  ```  

  此範例會從開頭為的伺服器讀取所有的 audit 記錄 `Sh` ： 
  
  ```  
  SELECT * FROM sys.fn_get_audit_file ('https://mystorage.blob.core.windows.net/sqldbauditlogs/Sh',default,default);
  GO  
  ```

如需如何建立稽核的完整範例，請參閱 [SQL Server Audit &#40;Database Engine&#41;](../../relational-databases/security/auditing/sql-server-audit-database-engine.md) (SQL Server 稽核 &#40;資料庫引擎&#41)。

如需設定 Azure SQL Database 審核的詳細資訊，請參閱 [使用 SQL Database 審核開始](/azure/sql-database/sql-database-auditing)。
  
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
 [sys.server_audits &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-server-audits-transact-sql.md)   
 [sys.server_file_audits &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-server-file-audits-transact-sql.md)   
 [sys.server_audit_specifications &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-server-audit-specifications-transact-sql.md)   
 [sys.server_audit_specification_details &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-server-audit-specification-details-transact-sql.md)   
 [sys.database_audit_specifications &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-database-audit-specifications-transact-sql.md)   
 [sys.database_audit_specification_details &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-database-audit-specification-details-transact-sql.md)   
 [sys.dm_server_audit_status &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-server-audit-status-transact-sql.md)   
 [sys.dm_audit_actions &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-audit-actions-transact-sql.md)   
 [sys.dm_audit_class_type_map &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-audit-class-type-map-transact-sql.md)   
 [建立伺服器稽核與伺服器稽核規格](../../relational-databases/security/auditing/create-a-server-audit-and-server-audit-specification.md)  
  
