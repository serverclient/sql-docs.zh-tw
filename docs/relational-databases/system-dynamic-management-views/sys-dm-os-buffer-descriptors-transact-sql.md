---
description: sys.dm_os_buffer_descriptors (Transact-SQL)
title: sys.dm_os_buffer_descriptors (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 08/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.dm_os_buffer_descriptors_TSQL
- dm_os_buffer_descriptors_TSQL
- sys.dm_os_buffer_descriptors
- dm_os_buffer_descriptors
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_os_buffer_descriptors dynamic management view
ms.assetid: 012aab95-8888-4f35-9ea3-b5dff6e3f60f
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 1c56a25586be861aca825119f38bff390eeba90a
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98099760"
---
# <a name="sysdm_os_buffer_descriptors-transact-sql"></a>sys.dm_os_buffer_descriptors (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  傳回有關目前正在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 緩衝集區中所有資料頁的資訊。 這項檢視的輸出，可以用來決定依據資料庫、物件或是類型來散發緩衝集區中的資料頁。 在 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 中，這個動態管理檢視也會傳回有關緩衝集區擴充檔中資料頁的資訊。 如需詳細資訊，請參閱 [緩衝集區延伸](../../database-engine/configure-windows/buffer-pool-extension.md)。  
  
 當資料頁是從磁碟讀取時，此頁面會複製到 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 緩衝集區，而且會經由快取提供重複使用。 每個快取資料頁都具有一個緩衝區描述項。 緩衝區描述項會以唯一的方式識別由 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體目前快取的每個資料頁。 sys.dm_os_buffer_descriptors 會為所有使用者和系統資料庫傳回快取頁面。 其中包括與資源資料庫相關聯的頁面。  
  
> **注意：** 若要從或呼叫這個 [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] ，請使用 **sys.dm_pdw_nodes_os_buffer_descriptors** 名稱。  

|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|database_id|**int**|與緩衝集區中之頁面相關聯的資料庫識別碼。 可為 Null。|  
|file_id|**int**|儲存頁面之保存影像的檔案識別碼。 可為 Null。|  
|page_id|**int**|檔案內的頁面識別碼。 可為 Null。|  
|page_level|**int**|頁面的索引層級。 可為 Null。|  
|allocation_unit_id|**bigint**|頁面的配置單位識別碼。 這個值可以用來聯結 sys.allocation_units。 可為 Null。|  
|page_type|**nvarchar(60)**|頁面的類型，例如：資料頁或索引頁。 可為 Null。|  
|row_count|**int**|頁面上的資料列數。 可為 Null。|  
|free_space_in_bytes|**int**|頁面上的可用空間量 (以位元組為單位)。 可為 Null。|  
|is_modified|**bit**|1 = 頁面從磁碟讀取之後，已經修改過了。 可為 Null。|  
|numa_node|**int**|緩衝區的非統一記憶體存取節點。 可為 Null。|  
|read_microsec|**bigint**|將頁面讀取至緩衝區所需的實際時間 (單位毫秒)。 這個數字會在重複使用緩衝區時重設。 可為 Null。|  
|is_in_bpool_extension|**bit**|1 = 頁面位於緩衝集區延伸中。 可為 Null。|  
|pdw_node_id|**int**|**適用** 于： [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 、 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]<br /><br /> 此散發所在之節點的識別碼。|  
  
## <a name="permissions"></a>權限  

在上 [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] ，需要 `VIEW SERVER STATE` 許可權。   
在 SQL Database Basic、S0 和 S1 服務目標上，以及針對彈性集區中的資料庫，則 `Server admin` `Azure Active Directory admin` 需要或帳戶。 在所有其他 SQL Database 服務目標上， `VIEW DATABASE STATE` 資料庫中都需要有許可權。   
   
## <a name="remarks"></a>備註  
 sys.dm_os_buffer_descriptors 會傳回資源資料庫正在使用的頁面。 sys.dm_os_buffer_descriptors 不會傳回可用或遭竊頁面的相關資訊，或讀取時發生錯誤的頁面相關資訊。  
  
|寄件者|收件者|開啟|關聯性|  
|----------|--------|--------|------------------|  
|sys.dm_os_buffer_descriptors|sys.databases|database_id|多對一|  
|sys.dm_os_buffer_descriptors|\<userdb>.sys.allocation_units|allocation_unit_id|多對一|  
|sys.dm_os_buffer_descriptors|\<userdb>.sys.database_files|file_id|多對一|  
|sys.dm_os_buffer_descriptors|sys.dm_os_buffer_pool_extension_configuration|file_id|多對一|  
  
## <a name="examples"></a>範例  
  
### <a name="a-returning-cached-page-count-for-each-database"></a>A. 傳回每個資料庫的快取頁面計數  
 下列範例會傳回每個資料庫所載入的頁面計數。  
  
```  
SELECT COUNT(*)AS cached_pages_count  
    ,CASE database_id   
        WHEN 32767 THEN 'ResourceDb'   
        ELSE db_name(database_id)   
        END AS database_name  
FROM sys.dm_os_buffer_descriptors  
GROUP BY DB_NAME(database_id) ,database_id  
ORDER BY cached_pages_count DESC;  
```  
  
### <a name="b-returning-cached-page-count-for-each-object-in-the-current-database"></a>B. 傳回目前資料庫中每個物件的快取頁面計數  
 下列範例會傳回目前資料庫中每個物件所載入的頁面計數。  
  
```  
SELECT COUNT(*)AS cached_pages_count   
    ,name ,index_id   
FROM sys.dm_os_buffer_descriptors AS bd   
    INNER JOIN   
    (  
        SELECT object_name(object_id) AS name   
            ,index_id ,allocation_unit_id  
        FROM sys.allocation_units AS au  
            INNER JOIN sys.partitions AS p   
                ON au.container_id = p.hobt_id   
                    AND (au.type = 1 OR au.type = 3)  
        UNION ALL  
        SELECT object_name(object_id) AS name     
            ,index_id, allocation_unit_id  
        FROM sys.allocation_units AS au  
            INNER JOIN sys.partitions AS p   
                ON au.container_id = p.partition_id   
                    AND au.type = 2  
    ) AS obj   
        ON bd.allocation_unit_id = obj.allocation_unit_id  
WHERE database_id = DB_ID()  
GROUP BY name, index_id   
ORDER BY cached_pages_count DESC;  
```  
  
## <a name="see-also"></a>另請參閱  
 [sys.allocation_units &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-allocation-units-transact-sql.md)   
 
 [SQL Server 作業系統相關的動態管理檢視 &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sql-server-operating-system-related-dynamic-management-views-transact-sql.md)   
 [Resource 資料庫](../../relational-databases/databases/resource-database.md)   
 [sys.dm_os_buffer_pool_extension_configuration &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-os-buffer-pool-extension-configuration-transact-sql.md)  
  
  


