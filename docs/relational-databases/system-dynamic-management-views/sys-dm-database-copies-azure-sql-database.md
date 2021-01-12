---
description: sys.dm_database_copies (Azure SQL Database)
title: sys.dm_database_copies (Azure SQL Database) Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.service: sql-database
ms.reviewer: ''
ms.topic: language-reference
f1_keywords:
- dm_database_copies_TSQL
- sys.dm_database_copies
- dm_database_copies
- sys.dm_database_copies_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- dm_database_copies
- sys.dm_database_copies
ms.assetid: d03d4657-86d1-4496-97e6-cc3bc292e0b1
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: = azuresqldb-current
ms.openlocfilehash: ef0c92766cfb03f56dfc08a476cb7e81dbf4743d
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98095174"
---
# <a name="sysdm_database_copies-azure-sql-database"></a>sys.dm_database_copies (Azure SQL Database)
[!INCLUDE[Azure SQL Database](../../includes/applies-to-version/asdb.md)]

  傳回資料庫複本的相關資訊。  
  
若要傳回異地複寫連結的相關資訊，請使用 SQL Database V12) 提供的 [sys.geo_replication_links](../../relational-databases/system-dynamic-management-views/sys-geo-replication-links-azure-sql-database.md) 或 [sys.dm_geo_replication_link_status](../../relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database.md) 視圖 (。
  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**database_id**|**int**|`sys.databases` 檢視中目前資料庫的識別碼。|  
|**start_date**|**datetimeoffset**|起始資料庫複製作業時，區域性 [!INCLUDE[ssSDS](../../includes/sssds-md.md)] 資料中心的 UTC 時間。|  
|**modify_date**|**datetimeoffset**|資料庫複製作業完成時，區域性 [!INCLUDE[ssSDS](../../includes/sssds-md.md)] 資料中心的 UTC 時間。 新的資料庫和主要資料庫於此時間在交易上是一致的。 完成資訊會每隔1分鐘更新一次。<br /><br />反映 percent_complete 欄位最後更新的 UTC 時間。|  
|**percent_complete**|**real**|已複製位元組的百分比。 值的範圍是從 0 到 100。 [!INCLUDE[ssSDS](../../includes/sssds-md.md)] 可能會從某些錯誤中自動復原 (例如容錯移轉)，然後再重新啟動資料庫複製作業。 在此情況下，percent_complete 會從 0 重新開始。|  
|**error_code**|**int**|如果大於 0，則代碼表示複製期間發生了錯誤。 如果未發生錯誤，值會等於 0。|  
|**error_desc**|**Nvarchar (4096)**|複製期間發生之錯誤的描述。|  
|**error_severity**|**int**|如果資料庫複製失敗，則傳回 16。|  
|**error_state**|**int**|如果複製失敗，則傳回 1。|  
|**copy_guid**|**uniqueidentifier**|複製作業的唯一識別碼。|  
|**partner_server**|**sysname**|建立複本之 SQL Database 伺服器的名稱。|  
|**partner_database**|**sysname**|夥伴伺服器上的資料庫複本名稱。|  
|**replication_state**|**tinyint**|此資料庫的連續複製複寫狀態。 值為：<br /><br /> 0 = 暫止。 已排程建立資料庫複本，但必要的準備步驟尚未完成，或暫時遭到植入配額封鎖。<br /><br /> 1 = 植入。 正在植入的複製資料庫尚未與源資料庫完全同步處理。 在此狀態下，您無法連接到複本。 若要取消進行中的植入操作，必須卸載複製資料庫。|  
|**replication_state_desc**|**nvarchar(256)**|replication_state 的描述有下列幾種：<br /><br /> PENDING<br /><br /> SEEDING<br />|  
|**maximum_lag**|**int**|保留的欄位。|  
|**is_continuous_copy**|**bit**|0 = 傳回0|  
|**is_target_role**|**bit**|0 = 源資料庫<br /><br /> 1 = 複製資料庫|  
|**is_interlink_connected**|bit|保留的欄位。|  
|**is_offline_secondary**|bit|保留的欄位。|  
  
## <a name="permissions"></a>權限  
 只有 **master** 資料庫中的伺服器層級主體登入才能使用此視圖。  
  
## <a name="remarks"></a>備註  
 您可以使用來源或目標伺服器之 **master** 資料庫中的 **sys.dm_database_copies** view [!INCLUDE[ssSDS](../../includes/sssds-md.md)] 。 當資料庫複製順利完成，且新的資料庫已上線時，就會自動移除 **sys.dm_database_copies** view 中的資料列。  
  
  
