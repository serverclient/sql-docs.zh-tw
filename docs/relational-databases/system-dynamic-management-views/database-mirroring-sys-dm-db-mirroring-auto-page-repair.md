---
description: 資料庫鏡像-sys.dm_db_mirroring_auto_page_repair
title: sys.dm_db_mirroring_auto_page_repair (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- dm_db_mirroring_auto_page_repair_TSQL
- sys.dm_db_mirroring_auto_page_repair_TSQL
- sys.dm_db_mirroring_auto_page_repair
- dm_db_mirroring_auto_page_repair
dev_langs:
- TSQL
helpviewer_keywords:
- automatic page repair
- database mirroring [SQL Server], automatic page repair
- sys.dm_db_mirroring_auto_page_repair dynamic management view
ms.assetid: 49f0fc2a-e25e-47e1-a135-563adb509af1
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: cef38d675dde0d36ca97f63c16f8d4589760a620
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98101668"
---
# <a name="database-mirroring---sysdm_db_mirroring_auto_page_repair"></a>資料庫鏡像-sys.dm_db_mirroring_auto_page_repair
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  針對在伺服器執行個體之任何鏡像資料庫上進行的每個自動修復頁面嘗試行為，各傳回一個資料列。 這個檢視包含在指定鏡像資料庫上進行最新自動修復頁面嘗試行為的資料列，而且每個資料庫最多 100 個資料列。 一旦資料庫到達上限時，下一個自動修復頁面嘗試行為的資料列就會取代其中一個現有的項目。 下表定義各資料行的意義。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**database_id**|**int**|這個資料列所對應的資料庫識別碼。|  
|**file_id**|**int**|頁面所在之檔案的識別碼。|  
|**page_id**|**bigint**|檔案中頁面的識別碼。|  
|**error_type**|**int**|錯誤的類型。 其值可能是：<br /><br /> **-** 1 = 所有硬體823錯誤<br /><br /> 1 = 總和檢查碼錯誤或頁面損毀 (例如，頁面識別碼不正確) 以外的 824 錯誤<br /><br /> 2 = 總和檢查碼錯誤<br /><br /> 3 = 頁面損毀|  
|**page_status**|**int**|修復頁面嘗試行為的狀態：<br /><br /> 2 = 已將夥伴的要求排入佇列。<br /><br /> 3 = 要求已傳送給夥伴。<br /><br /> 4 = 已將自動修復頁面 (從夥伴收到的回應) 排入佇列。<br /><br /> 5 = 自動修復頁面成功，而且頁面應該可用。<br /><br /> 6 = 無法修復。 這表示在進行修復頁面嘗試行為時發生錯誤，因為該頁面在夥伴上也損毀、夥伴已中斷連線，或者發生網路問題。 這種狀態並非終端狀態。如果頁面上再次遇到損毀情況，就會再次向夥伴要求此頁面。|  
|**modification_time**|**datetime**|上次變更頁面狀態的時間。|  
  
## <a name="security"></a>安全性  
  
### <a name="permissions"></a>權限  
 需要伺服器的 VIEW SERVER STATE 權限。  
  
## <a name="see-also"></a>另請參閱  
 [自動修復頁面 &#40;可用性群組：資料庫鏡像&#41;](../../sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring.md)   
 [動態管理檢視與函數 &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)   
 [suspect_pages &#40;Transact-sql&#41;](../../relational-databases/system-tables/suspect-pages-transact-sql.md)   
 [管理 suspect_pages 資料表 &#40;SQL Server&#41;](../../relational-databases/backup-restore/manage-the-suspect-pages-table-sql-server.md)  
  
  


