---
description: sys.dm_db_persisted_sku_features (Transact-SQL)
title: sys.dm_db_persisted_sku_features (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 08/23/2017
ms.prod: sql
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.dm_db_persisted_sku_features_TSQL
- sys.dm_db_persisted_sku_features
- dm_db_persisted_sku_features_TSQL
- dm_db_persisted_sku_features
dev_langs:
- TSQL
helpviewer_keywords:
- editions [SQL Server]
- sys.dm_db_persisted_sku_features dynamic management view
ms.assetid: b4b29e97-b523-41b9-9528-6d4e84b89e09
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 89fd845ade31b7ca2de9559452bb04b15bc2c505
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98171430"
---
# <a name="sysdm_db_persisted_sku_features-transact-sql"></a>sys.dm_db_persisted_sku_features (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 的某些功能會變更 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 將資訊儲存在資料庫檔案中的方式， 這些功能受限為特定版本的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]。 包含這些功能的資料庫無法移至不支援它們的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 版本。 您可以使用 sys.dm_db_persisted_sku_features 動態管理檢視來列出目前資料庫中啟用的特定版本功能。
  
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本)。
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|feature_name|**sysname**|資料庫中已啟用，但是所有 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 版本上不支援之功能的外部名稱。 此功能必須先移除，資料庫才可以移轉到所有可用的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 版本。|  
|feature_id|**int**|與此功能相關聯的功能識別碼。 [!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)].|  
  
## <a name="permissions"></a>權限  
 需要資料庫的 VIEW DATABASE STATE 權限。  
  
## <a name="remarks"></a>備註  
 如果資料庫未使用特定版本所限制的任何功能，則 view 不會傳回任何資料列。  
  
 sys.dm_db_persisted_sku_features 可能會列出下列受限於特定版本的資料庫變更功能 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ：  
  
-   **ChangeCapture**：表示資料庫已啟用變更資料捕捉。 若要移除變更資料捕獲，請使用 [sys.sp_cdc_disable_db](../../relational-databases/system-stored-procedures/sys-sp-cdc-disable-db-transact-sql.md) 預存程式。 如需詳細資訊，請參閱[關於異動資料擷取 &#40;SQL Server&#41;](../../relational-databases/track-changes/about-change-data-capture-sql-server.md)。  
  
-   **ColumnStoreIndex**：表示至少有一個資料表有資料行存放區索引。 若要讓資料庫移至不 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 支援這項功能的版本，請使用 [DROP INDEX](../../t-sql/statements/drop-index-transact-sql.md) 或 [ALTER INDEX](../../t-sql/statements/alter-index-transact-sql.md) 語句移除資料行存放區索引。 如需詳細資訊，請參閱資料行存放區 [索引](../../relational-databases/indexes/columnstore-indexes-overview.md)。  
  
    **適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 及更新版本)。  
  
-   **壓縮**：表示至少有一個資料表或索引使用資料壓縮或 vardecimal 儲存格式。 若要讓資料庫移至不 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 支援這項功能的版本，請使用 [alter TABLE](../../t-sql/statements/alter-table-transact-sql.md) 或 [alter INDEX](../../t-sql/statements/alter-index-transact-sql.md) 語句來移除資料壓縮。 若要移除 Vardecimal 儲存格式，請使用 sp_tableoption 陳述式。 如需詳細資訊，請參閱 [Data Compression](../../relational-databases/data-compression/data-compression.md)。  
  
-   **MultipleFSContainers**：表示資料庫使用多個 FILESTREAM 容器。 資料庫具有多個容器 (檔案) 的 FILESTREAM 檔案群組。 如需詳細資訊，請參閱 [FILESTREAM &#40;SQL Server&#41;](../../relational-databases/blob/filestream-sql-server.md)。  
  
-   **InMemoryOLTP**：表示資料庫使用 In-Memory OLTP。 資料庫具有 MEMORY_OPTIMIZED_DATA 檔案群組。 如需詳細資訊，請參閱[記憶體內部 OLTP &#40;記憶體內部最佳化&#41;](../../relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization.md)。  
  
  **適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 及更新版本)。 
  
-   **分區。** 指出資料庫包含分割區資料表、分割區索引、分割區配置或分割區函數。 若要讓資料庫移到 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的版本，而不是 Enterprise 或 Developer，修改位於單一分割區上的資料表是不夠的， 您必須移除分割區資料表。 如果此資料表包含資料，請使用 SWITCH PARTITION 將每一個分割區轉換成非分割區資料表， 然後刪除分割區資料表、分割區配置和分割區函數。  
  
-   **TransparentDataEncryption。** 指出資料庫的加密方式為透明資料加密。 若要移除透明資料加密，請使用 ALTER DATABASE 陳述式。 如需詳細資訊，請參閱[透明資料加密 &#40;TDE&#41;](../../relational-databases/security/encryption/transparent-data-encryption.md)。  

> [!NOTE]
> 從 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] Service Pack 1 開始，除了 TransparentDataEncryption 以外，這些功能都有 **。** 可跨多個 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 版本使用，且不限於 Enterprise 或 Developer 版本。

 若要判斷資料庫是否使用任何受限於特定版本的功能，請在資料庫中執行下列陳述式：  
  
```sql  
SELECT feature_name FROM sys.dm_db_persisted_sku_features;  
GO  
```  
  
## <a name="see-also"></a>另請參閱  
 [動態管理檢視與函數 &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)   
 [資料庫相關的動態管理檢視 &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/database-related-dynamic-management-views-transact-sql.md)   
 [SQL Server 2016 的版本與支援功能](../../sql-server/editions-and-components-of-sql-server-2016.md)   
 [版本及支援的 SQL Server 2017 功能](../../sql-server/editions-and-components-of-sql-server-2017.md)  
  
