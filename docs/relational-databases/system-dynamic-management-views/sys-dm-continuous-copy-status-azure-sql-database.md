---
description: sys.dm_continuous_copy_status (Azure SQL Database)
title: sys.dm_continuous_copy_status
titleSuffix: Azure SQL Database
ms.date: 03/03/2017
ms.service: sql-database
ms.reviewer: ''
ms.topic: language-reference
f1_keywords:
- sys.dm_continuous_copy_status_TSQL
- dm_continuous_copy_status_TSQL
- dm_continuous_copy_status
- sys.dm_continuous_copy_status
dev_langs:
- TSQL
helpviewer_keywords:
- dm_continuous_copy_status
- sys.dm_continuous_copy_status
ms.assetid: 411b2e71-4421-4ef5-900d-5af068750899
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: = azuresqldb-current
ms.custom: seo-dt-2019
ms.openlocfilehash: 94370b562160315e53feb46a83cbb68a848e064f
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98099958"
---
# <a name="sysdm_continuous_copy_status-azure-sql-database"></a>sys.dm_continuous_copy_status (Azure SQL Database)
[!INCLUDE[Azure SQL Database Azure SQL Managed Instance](../../includes/applies-to-version/asdb-asdbmi.md)]

  針對目前參與異地複寫連續複製關聯性的每個使用者資料庫，各傳回一個資料列 (V11) 。 如果給定的主要資料庫起始一個以上的連續複製關聯性，這個資料表會針對每個使用中的次要資料庫各包含一個資料列。  
  
如果您使用 SQL Database V12，則應使用 [sys.dm_geo_replication_link_status](../../relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database.md) (，因為 *sys.dm_continuous_copy_status* 只適用于 V11) 。

  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**copy_guid**|**uniqueidentifier**|複本資料庫的唯一識別碼。|  
|**partner_server**|**sysname**|連結的 SQL Database 伺服器的名稱。|  
|**partner_database**|**sysname**|連結的 SQL Database 伺服器上所連結資料庫的名稱。|  
|**last_replication**|**datetimeoffset**|最後套用之複寫交易的時間戳記。|  
|**replication_lag_sec**|**int**|目前時間與作用中的次要資料庫尚未認可之主要資料庫上最後一個成功認可交易的時間戳記之間的時差。|  
|**replication_state**|**tinyint**|此資料庫的連續複製複寫狀態。 以下是可能的值及其描述。<br /><br /> 1：植入。 將要植入複寫目標，且該目標處於交易不一致的狀態。 在植入完成之前，您無法連接到作用中的次要資料庫。 <br />2：趕上。 作用中的次要資料庫目前正在趕上主要資料庫，並且處於交易一致的狀態。<br />3：重新植入。 因為發生無法復原的複寫失敗，所以作用中的次要資料庫正要自動重新植入。<br />4：已暫停。 這表示沒有作用中的連續複製關聯性。 這個狀態通常表示互連可用的頻寬對於主要資料庫上的交易活動層級而言不足。 不過，連續複製關聯性仍保持不變。|  
|**replication_state_desc**|**nvarchar(256)**|replication_state 的描述有下列幾種：<br /><br /> SEEDING<br /><br /> CATCH_UP<br /><br /> RE_SEEDING<br /><br /> SUSPENDED|  
|**is_rpo_limit_reached**|**bit**|這個值一定會設定為 0。|  
|**is_target_role**|**bit**|0 = 複製關聯性的來源<br /><br /> 1 = 複製關聯性的目標|  
|**is_interlink_connected**|**bit**|1 = 互連已連接。<br /><br /> 0 = 互連中斷連接。|  
  
## <a name="permissions"></a>權限  
 若要取出資料，需要 **db_owner** 資料庫角色中的成員資格。 Dbo 使用者、 **dbmanager** 資料庫角色的成員和 sa 登入都可以同時查詢此視圖。  
  
## <a name="remarks"></a>備註  
 **Sys.dm_continuous_copy_status** view 會建立在 **resource** 資料庫中，而且會顯示在所有資料庫中，包括邏輯 master。 不過，在邏輯 master 中查詢這個檢視表會傳回空集。  
  
 如果資料庫上的連續複製關聯性終止， **sys.dm_continuous_copy_status** view 中該資料庫的資料列就會消失。  
  
 如同 **sys.dm_database_copies** 視圖， **sys.dm_continuous_copy_status** 會反映連續複製關聯性的狀態，其中資料庫是主要或作用中的次要資料庫。 不同于 **sys.dm_database_copies**， **sys.dm_continuous_copy_status** 包含數個數據行，以提供有關作業和效能的詳細資料。 這些資料行包含 **last_replication** 和 **replication_lag_sec**。  
  
## <a name="see-also"></a>另請參閱  
 [sys.dm_database_copies &#40;Azure SQL Database&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-database-copies-azure-sql-database.md)   
 [Active Geo-Replication 預存程式 &#40;Transact-sql&#41;](../system-stored-procedures/system-stored-procedures-transact-sql.md)  
  
