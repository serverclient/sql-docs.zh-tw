---
description: syscollector_collector_types (Transact-SQL)
title: syscollector_collector_types (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- syscollector_collector_types
- syscollector_collector_types_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- data collector view
- syscollector_collector_types view
ms.assetid: d5cd30bb-89fd-4814-a7e8-9074f043f90f
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 04a9bc5f6336119d08285ae73dfa7a09ae5d3648
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98094245"
---
# <a name="syscollector_collector_types-transact-sql"></a>syscollector_collector_types (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  提供收集項之收集器類型的相關資訊。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**collector_type_uid**|**uniqueidentifer**|集合類型的 GUID。 不可為 Null。|  
|**name**|**sysname**|集合類型的名稱。 不可為 Null。|  
|**parameter_schema**|**xml**|XML 結構描述，其中描述指定之收集器類型的組態內容。 這個 XML 結構描述是用來驗證與特定收集項執行個體相關聯的實際 XML 組態。 可為 Null。|  
|**parameter_formatter**|**xml**|判斷用來轉換 XML 的範本，以便能夠在收集組屬性頁中使用。 可為 Null。|  
|**collection_package_id**|**uniqueidentifer**|收集封裝的 GUID。 不可為 Null。|  
|**collection_package_path**|**nvarchar(4000)**|提供收集封裝的路徑。 可為 Null。|  
|**collection_package_name**|**sysname**|收集封裝的名稱。 不可為 Null。|  
|**upload_package_id**|**uniqueidentifer**|上傳封裝的 GUID。 不可為 Null。|  
|**upload_package_path**|**nvarchar(4000)**|提供上傳封裝的路徑。 可為 Null。|  
|**upload_package_name**|**sysname**|上傳封裝的名稱。 不可為 Null。|  
|**is_system**|**bit**|開啟 (1) 或 off (0) 來指出收集器型別是否隨附于資料收集器，或是否已在 **dc_admin** 之後新增。 這可能是本廠開發或由協力廠商開發的自訂類型。 不可為 Null。|  
  
## <a name="permissions"></a>權限  
 需要 SELECT for **dc_operator**， **dc_proxy**。  
  
## <a name="change-history"></a>變更記錄  
  
|更新的內容|  
|---------------------|  
|已將 **collection_type_uid** 的資料行名稱更新為 **collector_type_uid**。|  
|已更正 **parameter_schema** 資料行的描述，指出此值可為 null。|  
|已新增 **parameter_formatter** 資料行。|  
|已更正 **collection_package_path** 資料行的資料類型，並已更新描述，以指出此值可為 null。|  
|已更正 **upload_package_path** 資料行的資料類型，並已更新描述，以指出此值可為 null。|  
  
## <a name="see-also"></a>另請參閱  
 [資料收集器預存程序 &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/data-collector-stored-procedures-transact-sql.md)   
 [資料收集器檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/data-collector-views-transact-sql.md)   
 [[資料收集]](../../relational-databases/data-collection/data-collection.md)  
  
  
