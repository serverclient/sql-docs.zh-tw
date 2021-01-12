---
description: 'sys.dm_exec_distributed_requests (Transact-sql) '
title: sys.dm_exec_distributed_requests (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- DM_EXEC_DISTRIBUTED_REQUESTS
- DM_EXEC_DISTRIBUTED_REQUESTS_TSQL
- SYS.DM_EXEC_DISTRIBUTED_REQUESTS_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- PolyBase,views
- sys.dm_exec_distributed_sql_requests management view
- PolyBase
- dm_exec_distributed_sql_requests management view
ms.assetid: c041d416-d8c6-435e-a563-6a310abd33e3
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 7c201260484919e307b96cd37240179a4e1ddbec
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98094058"
---
# <a name="sysdm_exec_distributed_requests-transact-sql"></a>sys.dm_exec_distributed_requests (Transact-sql) 
[!INCLUDE [sqlserver2016-asa-pdw](../../includes/applies-to-version/sqlserver2016-asa-pdw.md)]

  保存 PolyBase 查詢中目前或最近使用中的所有要求的相關資訊。 它會針對每個要求/查詢列出一個資料列。  
  
 使用者可以根據會話和要求識別碼，取得產生要執行的實際分散式要求-透過 sys.dm_exec_distributed_requests。 例如，涉及一般 SQL 和外部 SQL 資料表的查詢會分解成跨不同計算節點執行的各種語句/要求。 為了追蹤所有計算節點上的分散式步驟，我們引進了「全域」執行識別碼，可用來分別追蹤與某個特定要求和操作員相關聯之計算節點上的所有作業。  
  
|資料行名稱|資料類型|描述|範圍|  
|-----------------|---------------|-----------------|-----------|  
|sql_handle|**varbinary(64)**|此視圖的索引鍵。 與要求相關聯的唯一數值識別碼。|系統中的所有要求都是唯一的。|  
|execution_id|**Nvarchar (32**|與執行此查詢的會話相關聯的唯一數值識別碼。||  
|status|**Nvarchar (32**|要求的目前狀態。|「擱置」、「授權」、「AcquireSystemResources」、「正在初始化」、「方案」、「剖析」、「AquireResources」、「執行中」、「取消」、「完成」、「失敗」、「已取消」。|  
|error_id|**Nvarchar (36)**|與要求相關聯之錯誤的唯一識別碼（如果有的話）。|如果未發生任何錯誤，則設定為 Null。|  
|start_time|**datetime**|開始執行要求的時間。|0表示已排入佇列的要求;否則，有效的日期時間小於或等於目前的時間。|  
|end_time|**datetime**|引擎完成要求編譯的時間。|若為已排入佇列或使用中的要求，為 Null否則，有效的日期時間會小於或等於目前的時間。|  
|total_elapsed_time|**int**|自開始要求以來經過的時間（以毫秒為單位）。|介於0和 start_time 與 end_time 之間的差異。如果 total_elapsed_time 超過整數的最大值，total_elapsed_time 將會繼續成為最大值。 這種狀況會產生「已超過最大值」的警告。 以毫秒為單位的最大值相當於24.8 天。|  
  
## <a name="see-also"></a>另請參閱  
 [使用動態管理檢視進行 PolyBase 疑難排解](/previous-versions/sql/sql-server-2016/mt146389(v=sql.130))   
 [動態管理檢視與函數 &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)   
 [資料庫相關的動態管理檢視 &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/database-related-dynamic-management-views-transact-sql.md)  
  
