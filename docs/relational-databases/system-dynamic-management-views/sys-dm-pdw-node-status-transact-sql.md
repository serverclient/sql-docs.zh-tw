---
description: 'sys.dm_pdw_node_status (Transact-sql) '
title: sys.dm_pdw_node_status (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/07/2017
ms.prod: sql
ms.technology: data-warehouse
ms.reviewer: ''
ms.topic: language-reference
dev_langs:
- TSQL
ms.assetid: 8e263b65-81d0-49d0-8873-62ef424369d6
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>= aps-pdw-2016'
ms.openlocfilehash: 520ee2be8bfce8c6913be55be8f3a479e90ac448
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98092724"
---
# <a name="sysdm_pdw_node_status-transact-sql"></a>sys.dm_pdw_node_status (Transact-sql) 

[!INCLUDE [pdw](../../includes/applies-to-version/pdw.md)]

  保存有關所有設備節點的效能和狀態 ([sys.dm_pdw_nodes &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-transact-sql.md)) 的額外資訊。 它會針對設備中的每個節點列出一個資料列。  
  
|資料行名稱|資料類型|描述|範圍|  
|-----------------|---------------|-----------------|-----------|  
|pdw_node_id|**int**|與節點相關聯的唯一數值識別碼。<br /><br /> 此視圖的索引鍵。|無論何種類型，在設備間都是唯一的。|  
|process_id|**int**|[!INCLUDE[ssInfoNA](../../includes/ssinfona-md.md)]||  
|process_name|**nvarchar(255)**|[!INCLUDE[ssInfoNA](../../includes/ssinfona-md.md)]||  
|allocated_memory|**bigint**|此節點上配置的記憶體總數。||  
|available_memory|**bigint**|此節點的可用記憶體總計。||  
|process_cpu_usage|**bigint**|總處理常式 CPU 使用量（以刻度為單位）。||  
|total_cpu_usage|**bigint**|總 CPU 使用量（以刻度為單位）。||  
|thread_count|**bigint**|此節點上使用中的執行緒總數。||  
|handle_count|**bigint**|此節點上使用中的控制碼總數。||  
|total_elapsed_time|**bigint**|自系統啟動或重新開機以來經過的總時間。|自系統啟動或重新開機以來經過的總時間。 如果 total_elapsed_time 超過整數 (24.8 天（以毫秒為單位）的最大值) ，則會造成具體化失敗，因為溢位。<br /><br /> 以毫秒為單位的最大值相當於24.8 天。|  
|is_available|**bit**|指出這個節點是否可用的旗標。||  
|sent_time|**datetime**|上次此節點傳送網路封裝的時間。||  
|received_time|**datetime**|上次此節點收到網路封裝的時間。||  
|error_id|**Nvarchar (36)**|在此節點上發生最後一個錯誤的唯一識別碼。||  
  
## <a name="see-also"></a>另請參閱  
 [Azure Synapse Analytics 和平行處理資料倉儲動態管理檢視 &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sql-and-parallel-data-warehouse-dynamic-management-views.md)  
  
  
