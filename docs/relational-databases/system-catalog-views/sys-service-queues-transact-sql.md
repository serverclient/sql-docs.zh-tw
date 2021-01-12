---
description: sys.service_queues (Transact-SQL)
title: sys.service_queues (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.service_queues
- service_queues
- service_queues_TSQL
- sys.service_queues_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.service_queues catalog view
ms.assetid: 9fd9fa76-6128-410c-896f-741e6050143a
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 55a1037bc944c0ad27d60c02b39d26f0d7aa73b6
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98096670"
---
# <a name="sysservice_queues-transact-sql"></a>sys.service_queues (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  針對資料庫中的每個物件，各包含一個資料列，該物件為服務佇列，而 **sys. objects。 type** = SQ。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**\<inherited columns>**||如需此視圖所繼承之資料行的清單，請參閱 [sys. objects &#40;transact-sql&#41;](../../relational-databases/system-catalog-views/sys-objects-transact-sql.md)。|  
|**max_readers**|**smallint**|佇列所能接受的最大並行讀取器數。|  
|**activation_procedure**|**Nvarchar (776)**|啟用程序的三部份名稱。|  
|**execute_as_principal_id**|**int**|EXECUTE AS 資料庫主體的識別碼。<br /><br /> 在預設或 EXECUTE AS CALLER 的情況下為 NULL。<br /><br /> 如果 EXECUTE AS SELF EXECUTE AS，則為指定之主體的識別碼 \<principal> 。<br /><br /> -2 = EXECUTE AS OWNER。|  
|**is_activation_enabled**|**bit**|1 = 啟用啟動。|  
|**is_receive_enabled**|**bit**|1 = 啟用接收。|  
|**is_enqueue_enabled**|**bit**|1 = 啟用加入佇列。|  
|**is_retention_enabled**|**bit**|1 = 訊息會保留到對話結束。|  
|**is_poison_message_handling_enabled**|**bit**|**適用對象**：[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 及更新版本。<br /><br /> 1 = 啟用有害訊息處理。|  
  
## <a name="permissions"></a>權限  
 [!INCLUDE[ssCatViewPerm](../../includes/sscatviewperm-md.md)] 如需相關資訊，請參閱 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)。  
  
## <a name="see-also"></a>另請參閱  
 [物件目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/object-catalog-views-transact-sql.md)   
 [目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/catalog-views-transact-sql.md)  
  
  
