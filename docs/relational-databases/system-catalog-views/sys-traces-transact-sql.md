---
description: sys.traces (Transact-SQL)
title: sys. 追蹤 (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- traces
- sys.traces_TSQL
- sys.traces
- traces_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.traces catalog view
ms.assetid: 4a03be22-b7da-4e2a-97ff-94bed890a620
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 523589a6840581bf96c436eea7393f9fa03beca7
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98094369"
---
# <a name="systraces-transact-sql"></a>sys.traces (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  **Sys. 追蹤** 目錄檢視包含系統上目前正在執行的追蹤。 此視圖旨在取代 **fn_trace_getinfo** 函式。  
  
 如需支援的追蹤事件的完整清單，請參閱 [SQL Server 事件類別參考](../../relational-databases/event-classes/sql-server-event-class-reference.md)。  
  
> [!IMPORTANT]  
>  [!INCLUDE[ssNoteDepFutureAvoid](../../includes/ssnotedepfutureavoid-md.md)]請改用擴充事件目錄檢視。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**id**|**int**|追蹤識別碼。|  
|**status**|**int**|追蹤狀態：<br /><br /> 0 = 已停止<br /><br /> 1 = 正在執行|  
|**path**|**nvarchar(260)**|追蹤檔的路徑。 當追蹤是資料列集追蹤時，這個值是 Null。|  
|**max_size**|**bigint**|最大追蹤檔大小限制 (以 MB 為單位)。 當追蹤是資料列集追蹤時，這個值是 Null。|  
|**stop_time**|**datetime**|停止執行追蹤的時間。|  
|**max_files**|**int**|輪用檔案數量上限數目。 如果未設定最大數目，這個值就是 Null。|  
|**is_rowset**|**bit**|1 = 資料列集追蹤。|  
|**is_rollover**|**bit**|1 = 啟用換用選項。|  
|**is_shutdown**|**bit**|1 = 啟用關閉選項。|  
|**is_default**|**bit**|1 = 預設追蹤。|  
|**buffer_count**|**int**|追蹤所用的記憶體中緩衝區數目。|  
|**buffer_size**|**int**|每個緩衝區的大小 (KB)。|  
|**file_position**|**bigint**|上一個追蹤檔位置。 當追蹤是資料列集追蹤時，這個值是 Null。|  
|**reader_spid**|**int**|資料列集追蹤讀取器工作階段識別碼。 當追蹤是檔案追蹤時，這個值是 Null。|  
|**start_time**|**datetime**|追蹤開始時間。|  
|**last_event_time**|**datetime**|上一事件的引發時間。|  
|**event_count**|**bigint**|發生的事件總數。|  
|**dropped_event_count**|**int**|卸除的事件總數。|  
  
## <a name="permissions"></a>權限  
 [!INCLUDE[ssCatViewPerm](../../includes/sscatviewperm-md.md)] 如需相關資訊，請參閱 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)。  
  
## <a name="see-also"></a>另請參閱  
 [物件目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/object-catalog-views-transact-sql.md)   
 [sys.trace_categories &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-trace-categories-transact-sql.md)   
 [sys.trace_columns &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-trace-columns-transact-sql.md)   
 [sys.trace_events &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-trace-events-transact-sql.md)   
 [sys.trace_event_bindings &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-trace-event-bindings-transact-sql.md)   
 [sys.trace_subclass_values &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-trace-subclass-values-transact-sql.md)  
  
  
