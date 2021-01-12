---
description: sys.geo_replication_links (Azure SQL Database)
title: sys.geo_replication_links (Azure SQL Database) Microsoft Docs
ms.custom: ''
ms.date: 01/28/2019
ms.service: sql-database
ms.reviewer: ''
ms.topic: conceptual
f1_keywords:
- dm_geo_replication_links_TSQL
- dm_geo_replication_links
- sys.dm_geo_replication_links
- sys.dm_geo_replication_links_TSQL
helpviewer_keywords:
- sys.dm_geo_replication_links dynamic management view
- dm_geo_replication_links dynamic management view
ms.assetid: 58911798-1d60-4f28-87ab-2def2bfc3de7
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: = azuresqldb-current
ms.openlocfilehash: f10f318ebba5627957f1555b5979e8993f6f5e02
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98093896"
---
# <a name="sysgeo_replication_links-azure-sql-database"></a>sys.geo_replication_links (Azure SQL Database)

[!INCLUDE[Azure SQL Database Azure SQL Managed Instance](../../includes/applies-to-version/asdb-asdbmi.md)]

  針對異地複寫合作關係中的主要和次要資料庫之間的每個複寫連結，各包含一個資料列。 此檢視表位於邏輯 master 資料庫。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|database_id|**int**|Sys. 資料庫檢視中目前資料庫的識別碼。|  
|start_date|**datetimeoffset**|起始資料庫複寫時，區域 SQL Database 資料中心的 UTC 時間。|  
|modify_date|**datetimeoffset**|資料庫異地複寫完成時，區域 SQL Database datacenter 的 UTC 時間。 此時，新的資料庫會與主資料庫同步處理。|  
|link_guid|**uniqueidentifier**|異地複寫連結的唯一識別碼。|  
|partner_server|**sysname**|包含異地複寫資料庫的 SQL Database 伺服器名稱。|  
|partner_database|**sysname**|連結的 SQL Database 伺服器上之異地複寫資料庫的名稱。|  
|replication_state|**tinyint**|此資料庫的異地複寫狀態，其中一個：<br /><br /> 0 = 暫止。 已排程建立作用中次要資料庫，但必要的準備步驟尚未完成。<br /><br /> 1 = 植入。 正在植入異地複寫目標，但兩個資料庫尚未同步處理。 在植入完成之前，您無法連接到次要資料庫。 從主資料庫移除次要資料庫會取消植入作業。<br /><br /> 2 = 趕上。 次要資料庫處於交易一致的狀態，而且會持續與主資料庫同步處理。|  
|replication_state_desc|**nvarchar(256)**|PENDING<br /><br /> SEEDING<br /><br /> CATCH_UP|  
|角色 (role)|**tinyint**|異地複寫角色，下列其中一個角色：<br /><br /> 0 = 主要。 Database_id 指的是「異地複寫」合作關係中的主資料庫。<br /><br /> 1 = 次要。  Database_id 指的是「異地複寫」合作關係中的主資料庫。|  
|role_desc|**nvarchar(256)**|PRIMARY<br /><br /> SECONDARY|  
|secondary_allow_connections|**tinyint**|次要類型，下列其中一個：<br /><br /> 0 = 否。 在容錯移轉之前，無法存取次要資料庫。<br /><br /> 1 = ReadOnly。 只有 >applicationintent = ReadOnly 的用戶端連接才能存取次要資料庫。<br /><br /> 2 = 全部。 所有用戶端連接都可以存取次要資料庫。|  
|secondary_allow_connections _desc|**nvarchar(256)**|否<br /><br /> 全部<br /><br /> 唯讀|  
  
## <a name="permissions"></a>權限

只有 **master** 資料庫中的伺服器層級主體登入才能使用此視圖。  
  
## <a name="example"></a>範例

顯示具有異地複寫連結的所有資料庫。  

```sql
SELECT
     database_id  
   , start_date  
   , partner_server  
   , partner_database  
   , replication_state  
   , role_desc  
   , secondary_allow_connections_desc
FROM sys.geo_replication_links;  
```

## <a name="see-also"></a>另請參閱

 [ALTER DATABASE (Azure SQL Database)](../../t-sql/statements/alter-database-transact-sql.md)   
 [sys.dm_geo_replication_link_status &#40;Azure SQL Database&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database.md)   
 [sys.dm_operation_status &#40;Azure SQL Database&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database.md)
