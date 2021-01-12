---
description: suspect_pages (Transact-SQL)
title: suspect_pages (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- suspect_page_table
- suspect_page_table_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- suspect_pages system table
- suspect pages [SQL Server]
ms.assetid: 119c8d62-eea8-44fb-bf72-de469c838c50
author: cawrites
ms.author: chadam
ms.openlocfilehash: c2b5952f12c857a7bf58c066744e1c366279b1e4
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98096122"
---
# <a name="suspect_pages-transact-sql"></a>suspect_pages (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  針對每個失敗並出現次要 823 錯誤或 824 錯誤的頁面，各包含一個資料列。 本表將列出這些頁面，因為雖然它們疑似有問題，但實際上可能是正常的。 修復可疑的頁面時，會更新 **event_type** 資料行中的狀態。  
  
 下表（限制為1000個數據列）儲存在 **msdb** 資料庫中。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**database_id**|**int**|這個頁面所套用的資料庫識別碼。|  
|**file_id**|**int**|資料庫內的檔案識別碼。|  
|**page_id**|**bigint**|可疑頁面的識別碼。 每個頁面都有一個頁面識別碼，它是一個 32 位元的值，用來識別頁面在資料庫中的位置。 **Page_id** 是 8 KB 頁面的資料檔案中的位移。 每個頁面識別碼在檔案內都是唯一的。|  
|**event_type**|**int**|這是錯誤的類型，它有下列幾種：<br /><br /> 1 = 導致可疑頁面 (例如，磁碟錯誤) 的 823 錯誤，或總和檢查碼錯誤或頁面損毀 (例如，頁面識別碼不正確) 以外的 824 錯誤。<br /><br /> 2 = 總和檢查碼錯誤。<br /><br /> 3 = 頁面損毀。<br /><br /> 4 = 已還原 (在頁面標示為不正確之後還原頁面)。<br /><br /> 5 = 已修復 (DBCC 已修復頁面)。<br /><br /> 7 = 已由 DBCC 取消配置。|  
|**error_count**|**int**|錯誤的發生次數。|  
|**last_update_date**|**datetime**|上次更新的日期和時間戳記。|  
  
## <a name="permissions"></a>權限  
 任何可以存取 **msdb** 的人員，均能讀取 **suspect_pages** 資料表中的資料。 針對 suspect_pages 資料表擁有 UPDATE 權限的任何人都可以更新其記錄。 **msdb** 上 **db_owner** 固定資料庫角色的成員或 **系統管理員** 固定伺服器角色的成員皆可插入、更新及刪除記錄。  
  
## <a name="see-also"></a>另請參閱  
 [還原頁面 &#40;SQL Server&#41;](../../relational-databases/backup-restore/restore-pages-sql-server.md)   
 [資料庫可疑資料頁面事件類別](../../relational-databases/event-classes/database-suspect-data-page-event-class.md)   
 [系統資料表 &#40;Transact-sql&#41;](../../relational-databases/system-tables/system-tables-transact-sql.md)   
 [管理 suspect_pages 資料表 &#40;SQL Server&#41;](../../relational-databases/backup-restore/manage-the-suspect-pages-table-sql-server.md)  
  
  
