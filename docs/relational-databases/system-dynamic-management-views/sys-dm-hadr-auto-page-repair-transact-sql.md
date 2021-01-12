---
description: sys.dm_hadr_auto_page_repair (Transact-SQL)
title: sys.dm_hadr_auto_page_repair (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 02/05/2019
ms.prod: sql
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- dm_hadr_auto_page_repair_TSQL
- sys.dm_hadr_auto_page_repair
- sys.dm_hadr_auto_page_repair_TSQL
- dm_hadr_auto_page_repair
dev_langs:
- TSQL
helpviewer_keywords:
- Availability Groups [SQL Server], monitoring
- automatic page repair
- sys.dm_hadr_auto_page_repair dynamic management view
ms.assetid: d7840adf-4a1b-41ac-bc94-102c07ad1c79
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: d620d976e7b86ccf15cbf489175ebdf1cbc34f5f
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98092813"
---
# <a name="sysdm_hadr_auto_page_repair-transact-sql"></a>sys.dm_hadr_auto_page_repair (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  針對可用性複本上的任何可用性資料庫進行的每個自動修復頁面嘗試行為，各傳回一個資料列，該可用性複本是針對伺服器執行個體的任何可用性群組所裝載。 這個檢視包含在給定之主要或次要資料庫上進行最新自動修復頁面嘗試行為的資料列，而且每個資料庫最多 100 個資料列。 一旦資料庫到達上限時，下一個自動修復頁面嘗試行為的資料列就會取代其中一個現有的項目。
  
  下表定義各資料行的意義：  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**database_id**|**int**|這個資料列所對應的資料庫識別碼。|  
|**file_id**|**int**|頁面所在之檔案的識別碼。|  
|**page_id**|**bigint**|檔案中頁面的識別碼。|  
|**error_type**|**int**|錯誤的類型。 其值可能是：<br /><br /> **-** 1 = 所有硬體823錯誤<br /><br /> 1 = 總和檢查碼錯誤或頁面損毀 (例如，頁面識別碼不正確) 以外的 824 錯誤<br /><br /> 2 = 總和檢查碼錯誤<br /><br /> 3 = 頁面損毀|  
|**page_status**|**int**|修復頁面嘗試行為的狀態：<br /><br /> 2 = 已將夥伴的要求排入佇列。<br /><br /> 3 = 要求已傳送給夥伴。<br /><br /> 4 = 已成功修復頁面。<br /><br /> 5 = 在上次嘗試/自動頁面修復期間無法修復頁面，將會再次嘗試修復頁面。|  
|**modification_time**|**datetime**|上次變更頁面狀態的時間。|  
  
## <a name="security"></a>安全性  
  
### <a name="permissions"></a>權限  
 需要伺服器的 VIEW SERVER STATE 權限。  
  
## <a name="see-also"></a>另請參閱  
 [自動修復頁面 &#40;可用性群組：資料庫鏡像&#41;](../../sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring.md)   
 [suspect_pages &#40;Transact-sql&#41;](../../relational-databases/system-tables/suspect-pages-transact-sql.md)   
 [管理 suspect_pages 資料表 &#40;SQL Server&#41;](../../relational-databases/backup-restore/manage-the-suspect-pages-table-sql-server.md)  
  
  

