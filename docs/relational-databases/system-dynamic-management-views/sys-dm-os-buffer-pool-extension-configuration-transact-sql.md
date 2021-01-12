---
description: sys.dm_os_buffer_pool_extension_configuration (Transact-SQL)
title: sys.dm_os_buffer_pool_extension_configuration (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 09/09/2017
ms.prod: sql
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- dm_os_buffer_pool_extension_configuration
- sys.dm_os_buffer_pool_extension_configuration_TSQL
- dm_os_buffer_pool_extension_configuration_TSQL
- sys.dm_os_buffer_pool_extension_configuration
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_os_buffer_pool_extension_configuration dynamic management view
ms.assetid: d52cc481-4d29-4f33-b63d-231ec35d092f
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 5477a9da1810c64b6c6c359694f3db9254948efa
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98101522"
---
# <a name="sysdm_os_buffer_pool_extension_configuration-transact-sql"></a>sys.dm_os_buffer_pool_extension_configuration (Transact-SQL)

[!INCLUDE[sqlserver](../../includes/applies-to-version/sqlserver.md)]

  傳回有關 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中緩衝集區延伸模組的組態資訊。 針對每個緩衝集區延伸模組檔案，各傳回一個資料列。  
  

  
| 資料行名稱 | 資料類型 | 描述 |
| :---------- | :-------- | :---------- |
|路徑|**Nvarchar** (256) |緩衝集區延伸模組快取的路徑和檔案名稱。 可為 Null。|  
|file_id|**int**|緩衝集區延伸模組檔案的識別碼。 不可為 Null。|  
|state|**int**|緩衝集區延伸模組功能的狀態。 不可為 Null。<br /><br /> 0 - 緩衝集區延伸模組已停用<br /><br /> 1 - 緩衝集區延伸模組停用中<br /><br /> 2-保留供日後使用<br /><br /> 3 - 緩衝集區延伸模組啟用中<br /><br /> 4 - 保留供日後使用<br /><br /> 5 - 緩衝集區延伸模組已啟用|  
|state_description|**Nvarchar** (60) |描述緩衝集區延伸模組功能的狀態。 可為 Null。<br /><br /> 0 = BUFFER POOL EXTENSION DISABLED<br /><br /> 5 = 已啟用緩衝集區延伸|
|current_size_in_kb|**bigint**|緩衝集區延伸模組檔案的目前大小。 不可為 Null。|
| &nbsp; | &nbsp; | &nbsp; |

## <a name="permissions"></a>權限  
 需要伺服器的 VIEW SERVER STATE 權限。  
  
## <a name="examples"></a>範例  
  
### <a name="a-returning-configuration-buffer-pool-extension-information"></a>A. 傳回組態緩衝集區延伸模組資訊  
 下列範例會從 sys.dm_os_buffer_pool_extension_configruation DMV 傳回所有資料行。  
  
```sql  
SELECT path, file_id, state, state_description, current_size_in_kb  
FROM sys.dm_os_buffer_pool_extension_configuration;  
```  
  
### <a name="b-returning-the-number-of-cached-pages-in-the-buffer-pool-extension-file"></a>B. 傳回緩衝集區延伸模組檔案中的快取頁面數目  
 下列範例會傳回每個緩衝集區延伸模組檔案中的快取頁面數目。  
  
```sql  
SELECT COUNT(*) AS cached_pages_count  
FROM sys.dm_os_buffer_descriptors  
WHERE is_in_bpool_extension <> 0  
;  
```  
  
## <a name="see-also"></a>另請參閱  
 [緩衝集區延伸模組](../../database-engine/configure-windows/buffer-pool-extension.md)   
 [sys.dm_os_buffer_descriptors &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-os-buffer-descriptors-transact-sql.md)  
  
  
