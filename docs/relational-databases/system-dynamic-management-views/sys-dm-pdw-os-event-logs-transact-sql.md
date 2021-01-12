---
description: 'sys.dm_pdw_os_event_logs (Transact-sql) '
title: sys.dm_pdw_os_event_logs (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/07/2017
ms.prod: sql
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
dev_langs:
- TSQL
ms.assetid: a0daa8cf-72e2-4349-8be1-d3cc0f9b1e02
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>= aps-pdw-2016'
ms.openlocfilehash: af5633fc5e1a169c71197ac77a715e4b28da2b57
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98092690"
---
# <a name="sysdm_pdw_os_event_logs-transact-sql"></a>sys.dm_pdw_os_event_logs (Transact-sql) 
[!INCLUDE [pdw](../../includes/applies-to-version/pdw.md)]

  保存不同節點上不同 Windows 事件記錄檔的相關資訊。  
  
|資料行名稱|資料類型|描述|範圍|  
|-----------------|---------------|-----------------|-----------|  
|pdw_node_id|**int**|此記錄的來源設備節點。<br /><br /> pdw_node_id 並 log_name 形成此視圖的索引鍵。||  
|log_name|**nvarchar(255)**|Windows 事件記錄檔名稱。<br /><br /> pdw_node_id 並 log_name 形成此視圖的索引鍵。||  
|log_source|**nvarchar(255)**|Windows 事件記錄檔來源名稱。||  
|event_id|**int**|事件的識別碼。 不是唯一的。||  
|event_type|**nvarchar(255)**|事件的類型，識別嚴重性。|[資訊]、[警告]、[錯誤]|  
|event_message|**nvarchar(4000)**|事件的詳細資料。||  
|generate_time|**datetime**|事件的建立時間。||  
|write_time|**datetime**|事件實際寫入記錄檔的時間。||  
  
 如需此視圖所保留之最大資料列的相關資訊，請參閱「 [容量限制](/azure/sql-data-warehouse/sql-data-warehouse-service-capacity-limits#metadata) 」主題中的「中繼資料」一節。 
  
## <a name="see-also"></a>另請參閱  
 [Azure Synapse Analytics 和平行處理資料倉儲動態管理檢視 &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sql-and-parallel-data-warehouse-dynamic-management-views.md)  
  
  
