---
description: 變更資料捕獲-sys.dm_cdc_errors
title: sys.dm_cdc_errors (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- dm_cdc_errors_TSQL
- dm_cdc_errors
- sys.dm_cdc_errors
- sys.dm_cdc_errors_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_cdc_errors dynamic management view
- change data capture [SQL Server], error reporting
ms.assetid: 898f2d76-9e63-45ef-94da-8034e86004ab
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: ce058600c4a912e13695817a533f8e9ec4c8f856
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98100113"
---
# <a name="change-data-capture---sysdm_cdc_errors"></a>變更資料捕獲-sys.dm_cdc_errors
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  針對在異動資料擷取記錄掃描工作階段期間遇到的每個錯誤，各傳回一個資料列。  
 
 
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**session_id**|**int**|工作階段的識別碼。<br /><br /> 0 = 錯誤並非在記錄掃描工作階段中發生。|  
|**phase_number**|**int**|指出發生錯誤時工作階段所處階段的編號。 如需每個階段的說明，請參閱 [sys.dm_cdc_log_scan_sessions &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/change-data-capture-sys-dm-cdc-log-scan-sessions.md)。|  
|**entry_time**|**datetime**|記錄錯誤的日期和時間。 此值會對應到 SQL 錯誤記錄檔中的時間戳記。|  
|**error_number**|**int**|錯誤訊息的識別碼。|  
|**error_severity**|**int**|訊息的嚴重性層級，介於 1 至 25 之間。|  
|**error_state**|**int**|錯誤的狀態編號。|  
|**error_message**|**nvarchar(1024)**|錯誤的訊息文字。|  
|**start_lsn**|**Nvarchar (23)**|發生錯誤時正在處理之資料列的起始 LSN 值。<br /><br /> 0 = 錯誤並非在記錄掃描工作階段中發生。|  
|**begin_lsn**|**Nvarchar (23)**|發生錯誤時正在處理之交易的開頭 LSN 值。<br /><br /> 0 = 錯誤並非在記錄掃描工作階段中發生。|  
|**sequence_value**|**Nvarchar (23)**|發生錯誤時正在處理之資料列的 LSN 值。<br /><br /> 0 = 錯誤並非在記錄掃描工作階段中發生。|  
  
## <a name="remarks"></a>備註  
 **sys.dm_cdc_errors** 包含先前32會話的錯誤資訊。  
  
## <a name="permissions"></a>權限  
 需要 VIEW DATABASE STATE 許可權才能查詢 **sys.dm_cdc_errors** 動態管理檢視。 如需有關動態管理檢視之許可權的詳細資訊，請參閱 [&#40;transact-sql&#41;的動態管理檢視和函數 ](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)。  
  
## <a name="see-also"></a>另請參閱  
 [sys.dm_cdc_log_scan_sessions &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/change-data-capture-sys-dm-cdc-log-scan-sessions.md)   
 [sys.dm_repl_traninfo &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-repl-traninfo-transact-sql.md)  
  
  

