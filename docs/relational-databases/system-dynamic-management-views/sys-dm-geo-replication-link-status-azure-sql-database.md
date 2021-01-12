---
description: sys.dm_geo_replication_link_status (Azure SQL Database)
title: sys.dm_geo_replication_link_status
titleSuffix: Azure SQL Database
ms.date: 01/28/2019
ms.service: sql-database
ms.reviewer: ''
ms.topic: conceptual
f1_keywords:
- dm_geo_replication_link_status
- dm_geo_replication_link_status_TSQL
- sys.dm_geo_replication_link_status
- sys.dm_geo_replication_link_status_TSQL
helpviewer_keywords:
- dm_geo_replication_link_status dynamic management view
- sys.dm_geo_replication_link_status dynamic management view
ms.assetid: d763d679-470a-4c21-86ab-dfe98d37e9fd
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: = azuresqldb-current
ms.custom: seo-dt-2019
ms.openlocfilehash: 2134a7890d23a7a080a576f8337e750bee2563eb
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98092793"
---
# <a name="sysdm_geo_replication_link_status-azure-sql-database"></a>sys.dm_geo_replication_link_status (Azure SQL Database)

[!INCLUDE[Azure SQL Database Azure SQL Managed Instance](../../includes/applies-to-version/asdb-asdbmi.md)]

  針對異地複寫合作關係中的主要和次要資料庫之間的每個複寫連結，各包含一個資料列。 這包括主要和次要資料庫。 如果給定主要資料庫有一個以上的連續複寫連結，此資料表會對每個關聯性包含一個資料列。 會在所有資料庫 (包括邏輯主機) 中建立檢視。 不過，在邏輯 master 中查詢這個檢視表會傳回空集。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|link_guid|**uniqueidentifier**|複寫連結的唯一識別碼。|  
|partner_server|**sysname**|包含連結資料庫 SQL Database 伺服器的名稱。|  
|partner_database|**sysname**|連結的 SQL Database 伺服器上所連結資料庫的名稱。|  
|last_replication|**datetimeoffset**|根據主資料庫時鐘，次要複本最後一個交易的認可時間戳記。 此值僅適用于主資料庫。|  
|replication_lag_sec|**int**|以主資料庫時鐘為基礎，在主資料庫上的 last_replication 值和時間戳記之間的時間差異（以秒為單位）。  此值僅適用于主資料庫。|  
|replication_state|**tinyint**|此資料庫的異地複寫狀態，其中一個：<br /><br /> 1 = 植入。 正在植入異地複寫目標，但兩個資料庫尚未同步處理。 在植入完成之前，您無法連接到次要資料庫。 從主資料庫移除次要資料庫會取消植入作業。<br /><br /> 2 = 趕上。 次要資料庫處於交易一致的狀態，而且會持續與主資料庫同步處理。<br /><br /> 4 = 已暫止。 這表示沒有作用中的連續複製關聯性。 這個狀態通常表示互連可用的頻寬對於主要資料庫上的交易活動層級而言不足。 不過，連續複製關聯性仍保持不變。|  
|replication_state_desc|**nvarchar(256)**|PENDING<br /><br /> SEEDING<br /><br /> CATCH_UP|  
|角色 (role)|**tinyint**|異地複寫角色，下列其中一個角色：<br /><br /> 0 = 主要。 Database_id 指的是「異地複寫」合作關係中的主資料庫。<br /><br /> 1 = 次要。  Database_id 指的是「異地複寫」合作關係中的主資料庫。|  
|role_desc|**nvarchar(256)**|PRIMARY<br /><br /> SECONDARY|  
|secondary_allow_connections|**tinyint**|次要類型，下列其中一個：<br /><br /> 0 = 不允許對次要資料庫進行直接連接，而且資料庫無法供讀取存取。<br /><br /> 2 = 在次要複寫中允許對資料庫進行所有連接; ication 唯讀存取。|  
|secondary_allow_connections_desc|**nvarchar(256)**|否<br /><br /> 全部|  
|last_commit|**datetimeoffset**|上次交易認可至資料庫的時間。 如果在主資料庫上抓取，則表示主資料庫上的上次認可時間。 如果在次要資料庫上抓取，則表示次要資料庫上的上次認可時間。 當複寫連結的主要複本關閉時，如果在次要資料庫上抓取，則會指出次要複本在哪個點上到達。|
  
> [!NOTE]  
>  如果藉由移除次要資料庫 (區段 4.2) 來終止複寫關聯性， **sys.dm_geo_replication_link_status** 視圖中該資料庫的資料列就會消失。  
  
## <a name="permissions"></a>權限  
 具有 view_database_state 許可權的任何帳戶都可以查詢 **sys.dm_geo_replication_link_status**。  
  
## <a name="example"></a>範例  
 顯示次要資料庫的複寫延遲和最後複寫時間。  
  
```  
SELECT   
     link_guid  
   , partner_server  
   , last_replication  
   , replication_lag_sec   
FROM sys.dm_geo_replication_link_status;  
```  
  
## <a name="see-also"></a>另請參閱  
 [ALTER DATABASE &#40;Azure SQL Database&#41;](../../t-sql/statements/alter-database-transact-sql.md)   
 [sys.geo_replication_links &#40;Azure SQL Database&#41;](../../relational-databases/system-dynamic-management-views/sys-geo-replication-links-azure-sql-database.md)   
 [sys.dm_operation_status &#40;Azure SQL Database&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database.md)   
 [sp_wait_for_database_copy_sync](../system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync.md)
