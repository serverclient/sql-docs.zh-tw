---
description: sys.dm_xe_sessions (Transact-SQL)
title: sys.dm_xe_sessions (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- dm_xe_sessions_TSQL
- dm_xe_sessions
- sys.dm_xe_sessions_TSQL
- sys.dm_xe_sessions
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_xe_sessions dynamic management view
- extended events [SQL Server], views
ms.assetid: defd6efb-9507-4247-a91f-dc6ff5841e17
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 3acce4d01f26c3fcc818201e05c33ab9a46116a8
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98099699"
---
# <a name="sysdm_xe_sessions-transact-sql"></a>sys.dm_xe_sessions (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  傳回使用中擴充的事件工作階段的相關資訊。 這個工作階段是事件、動作和目標的集合。  
    
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|address|**varbinary(8)**|工作階段的記憶體位址。 位址在本機系統中是唯一的。 不可為 Null。|  
|NAME|**nvarchar(256)**|工作階段的名稱。 名稱在本機系統中是唯一的。 不可為 Null。|  
|pending_buffers|**int**|正在暫止處理的完整緩衝區數目。 不可為 Null。|  
|total_regular_buffers|**int**|與工作階段有關的一般緩衝區總數。 不可為 Null。<br /><br /> 注意：通常大部分的時間都使用一般緩衝區。 這些緩衝區有足夠的大小可以容納許多事件。 每個工作階段通常有三或多個緩衝區。 伺服器會根據透過 MEMORY_PARTITION_MODE 選項設定的記憶體資料分割，自動決定一般緩衝區的數目。 一般緩衝區的大小等於除以緩衝區數目之 MAX_MEMORY 選項的值 (預設為 4 MB)。 如需 MEMORY_PARTITION_MODE 和 MAX_MEMORY 選項的詳細資訊，請參閱 [CREATE EVENT SESSION &#40;transact-sql&#41;](../../t-sql/statements/create-event-session-transact-sql.md)。|  
|regular_buffer_size|**bigint**|一般緩衝區的大小 (以位元組為單位)。 不可為 Null。|  
|total_large_buffers|**int**|大型緩衝區的總數。 不可為 Null。<br /><br /> 注意：當事件大於一般緩衝區時，就會使用大型緩衝區。 這些緩衝區會針對此用途明確保留。 當事件工作階段啟動，並根據 MAX_EVENT_SIZE 選項調整大小時，就會配置大型緩衝區。 如需 MAX_EVENT_SIZE 選項的詳細資訊，請參閱 [建立事件會話 &#40;transact-sql&#41;](../../t-sql/statements/create-event-session-transact-sql.md)。|  
|large_buffer_size|**bigint**|大型緩衝區的大小 (以位元組為單位)。 不可為 Null。|  
|total_buffer_size|**bigint**|用來儲存工作階段之事件的記憶體緩衝區大小總計 (以位元組為單位)。 不可為 Null。|  
|buffer_policy_flags|**int**|指示當所有緩衝區已滿且引發新的事件時，工作階段事件緩衝區之行為模式的點陣圖。 不可為 Null。|  
|buffer_policy_desc|**nvarchar(256)**|指示當所有緩衝區已滿且引發新的事件時，工作階段事件緩衝區之行為模式的描述。  不可為 Null。 buffer_policy_desc 可以是下列其中一項：<br /><br /> Drop event<br /><br /> Do not drop events<br /><br /> Drop full buffer<br /><br /> Allocate new buffer|  
|flags|**int**|指示已在工作階段上設定之旗標的點陣圖。 不可為 Null。|  
|flag_desc|**nvarchar(256)**|工作階段上設定的旗標描述。  不可為 Null。 flag_desc 可以是下列各項的任意組合：<br /><br /> Flush buffers on close<br /><br /> Dedicated dispatcher<br /><br /> Allow recursive events|  
|dropped_event_count|**int**|當緩衝區已滿時所卸除的事件數目。 如果緩衝區原則為 "Drop full buffer" 或 "Do not Drop events"，這個值就是 **0** 。 不可為 Null。|  
|dropped_buffer_count|**int**|當緩衝區已滿時所卸除的緩衝區數目。 如果緩衝區原則設定為 "Drop event" 或 "Do not Drop events"，這個值就是 **0** 。 不可為 Null。|  
|blocked_event_fire_time|**int**|當緩衝區已滿時封鎖事件引發的時間長度。 如果緩衝區原則為 "Drop full buffer" 或 "Drop event"，這個值就是 **0** 。 不可為 Null。|  
|create_time|**datetime**|建立工作階段的時間。 不可為 Null。|  
|largest_event_dropped_size|**int**|未納入工作階段緩衝區內的最大事件大小。 不可為 Null。|  
  
## <a name="permissions"></a>權限  
 需要伺服器的 VIEW SERVER STATE 權限。  
  
## <a name="see-also"></a>另請參閱  
 [動態管理檢視與函數 &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)  
  
  

