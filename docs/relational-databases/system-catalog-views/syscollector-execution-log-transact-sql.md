---
description: syscollector_execution_log (Transact-SQL)
title: syscollector_execution_log (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- syscollector_execution_log_TSQL
- syscollector_execution_log
dev_langs:
- TSQL
helpviewer_keywords:
- data collector view
- syscollector_execution_log view
ms.assetid: 11554d64-0426-42ce-b7ce-5591f67864d2
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: f6c9cc330c87debc0a80a9d282ea8c40c904c59c
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98094237"
---
# <a name="syscollector_execution_log-transact-sql"></a>syscollector_execution_log (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  根據收集組或封裝的執行記錄，提供相關資訊。   
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|log_id|**bigint**|識別每個收集組執行。 用來聯結此檢視與其他詳細記錄。 不可為 Null。|  
|parent_log_id|**bigint**|識別父封裝或收集組。 不可為 Null。 這些識別碼會以父子式關聯性鏈結，可讓您判斷哪個收集組啟動哪個封裝。 這個檢視會依據父子式連結分組這些記錄項目，並縮排封裝的名稱，以便清楚地顯示呼叫鏈結。|  
|collection_set_id|**int**|識別這個記錄項目所代表的收集組或封裝。 不可為 Null。|  
|collection_item_id|**int**|識別收集項。 可為 Null。|  
|start_time|**datetime**|啟動收集組或封裝的時間。 不可為 Null。|  
|last_iteration_time|**datetime**|若為持續執行的封裝，就是上次封裝擷取快照集的時間。 可為 Null。|  
|finish_time|**datetime**|已完成之封裝和收集組執行完成的時間。 可為 Null。|  
|runtime_execution_mode|**smallint**|指出收集組活動是收集資料或上傳資料。 可為 Null。<br /><br /> 值為：<br /><br /> 0 = 收集<br /><br /> 1 = 上傳|  
|status|**smallint**|指出收集組或封裝的目前狀態。 不可為 Null。<br /><br /> 值為：<br /><br /> 0 = 執行中<br /><br /> 1 = 完成<br /><br /> 2 = 失敗|  
|運算子|**nvarchar(128)**|識別啟動收集組或封裝的人員。 不可為 Null。|  
|package_id|**uniqueidentifier**|識別產生這個記錄的收集組或封裝。 可為 Null。|  
|package_name|**nvarchar(4000)**|產生此記錄檔的封裝名稱。 可為 Null。|  
|package_execution_id|**uniqueidentifier**|提供 [!INCLUDE[ssIS](../../includes/ssis-md.md)] 記錄資料表的連結。 可為 Null。|  
|failure_message|**nvarchar(2048)**|如果收集組或封裝失敗，則為該元件的最新錯誤訊息。 可為 Null。 若要取得更詳細的錯誤資訊，請使用 [fn_syscollector_get_execution_details &#40;transact-sql&#41;](../../relational-databases/system-functions/fn-syscollector-get-execution-details-transact-sql.md) 函數。|  
  
## <a name="permissions"></a>權限  
 需要 dc_operator 的 SELECT 權限。  
  
## <a name="see-also"></a>另請參閱  
 [資料收集器預存程序 &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/data-collector-stored-procedures-transact-sql.md)   
 [資料收集器檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/data-collector-views-transact-sql.md)   
 [[資料收集]](../../relational-databases/data-collection/data-collection.md)  
  
  
