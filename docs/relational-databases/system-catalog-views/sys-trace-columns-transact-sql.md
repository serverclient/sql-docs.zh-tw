---
description: sys.trace_columns (Transact-SQL)
title: sys.trace_columns (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.trace_columns
- trace_columns
- trace_columns_TSQL
- sys.trace_columns_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.trace_columns catalog view
ms.assetid: 5c48eb09-9e9b-45dd-b151-ca39b026ece5
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 740f553e4809a5eb971bb7bfe7e97b5b628dfebd
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98094384"
---
# <a name="systrace_columns-transact-sql"></a>sys.trace_columns (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  **Sys.trace_columns** 目錄] 視圖包含所有追蹤事件資料行的清單。 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 給定版本的這些資料行不會改變。  
  
 如需支援的追蹤事件的完整清單，請參閱 [SQL Server 事件類別參考](../../relational-databases/event-classes/sql-server-event-class-reference.md)。  
  
> [!IMPORTANT]  
>  [!INCLUDE[ssNoteDepFutureAvoid](../../includes/ssnotedepfutureavoid-md.md)]請改用擴充事件目錄檢視。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**trace_column_id**|**smallint**|這個資料行的唯一識別碼。|  
|**name**|**nvarchar(128)**|這個資料行的唯一名稱。 這個參數未翻譯成當地語系。|  
|**type_name**|**nvarchar(128)**|這個資料行的資料類型名稱。|  
|**max_size**|**int**|這個資料行的資料大小上限 (以位元組為單位)。|  
|**is_filterable**|**bit**|指出篩選規格是否能使用這個資料行。<br /><br /> 0 = false<br /><br /> 1 = true|  
|**is_repeatable**|**bit**|指出「重複資料行」資料是否能參考這個資料行。<br /><br /> 0 = false<br /><br /> 1 = true|  
|**is_repeated_base**|**bit**|指出這個資料行是否做為參考重複資料的唯一索引鍵。<br /><br /> 0 = false<br /><br /> 1 = true|  
  
## <a name="permissions"></a>權限  
 [!INCLUDE[ssCatViewPerm](../../includes/sscatviewperm-md.md)] 如需相關資訊，請參閱 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)。  
  
## <a name="see-also"></a>另請參閱  
 [物件目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/object-catalog-views-transact-sql.md)   
 [sys. 追蹤 &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-traces-transact-sql.md)   
 [sys.trace_categories &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-trace-categories-transact-sql.md)   
 [sys.trace_events &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-trace-events-transact-sql.md)   
 [sys.trace_event_bindings &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-trace-event-bindings-transact-sql.md)   
 [sys.trace_subclass_values &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-trace-subclass-values-transact-sql.md)  
  
  
