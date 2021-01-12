---
description: 'sys.dm_pdw_component_health_alerts (Transact-sql) '
title: sys.dm_pdw_component_health_alerts (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/07/2017
ms.prod: sql
ms.technology: data-warehouse
ms.reviewer: ''
ms.topic: conceptual
ms.assetid: 88f05392-1e97-4693-ba60-a4910af3c000
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>= aps-pdw-2016'
ms.openlocfilehash: 83a3a237da01eb677ef8282940b334fa28b42637
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98093970"
---
# <a name="sysdm_pdw_component_health_alerts-transact-sql"></a>sys.dm_pdw_component_health_alerts (Transact-sql) 
[!INCLUDE [pdw](../../includes/applies-to-version/pdw.md)]

  在設備元件上儲存先前發出的警示。  
  
|資料行名稱|資料類型|描述|範圍|  
|-----------------|---------------|-----------------|-----------|  
|pdw_node_id|**int**|節點的唯一識別碼 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] 。<br /><br /> pdw_node_id、component_id、component_instance_id、alert_id 和 alert_instance_id 會形成此視圖的索引鍵。|NOT NULL|  
|component_id|**int**|元件的識別碼。 請參閱 [sys.pdw_health_components &#40;transact-sql&#41;](../../relational-databases/system-catalog-views/sys-pdw-health-components-transact-sql.md)。<br /><br /> pdw_node_id、component_id、component_instance_id、alert_id 和 alert_instance_id 會形成此視圖的索引鍵。|NOT NULL|  
|component_instance_id|**nvarchar(255)**|pdw_node_id、component_id、component_instance_id、alert_id 和 alert_instance_id 會形成此視圖的索引鍵。|NOT NULL|  
|alert_id|**int**|警示類型的識別碼。 請參閱 [sys.pdw_health_alerts &#40;transact-sql&#41;](../../relational-databases/system-catalog-views/sys-pdw-health-alerts-transact-sql.md)。<br /><br /> pdw_node_id、component_id、component_instance_id、alert_id 和 alert_instance_id 會形成此視圖的索引鍵。|NOT NULL|  
|alert_instance_id|**Nvarchar (36)**|識別指定之警示的實例。<br /><br /> pdw_node_id、component_id、component_instance_id、alert_id 和 alert_instance_id 會形成此視圖的索引鍵。|NOT NULL|  
|previous_value|**nvarchar(255)**|當警示的類型為 StatusChange 時使用。 這是先前的元件狀態。 針對類型臨界值的警示，值為 Null。 如需警示類型清單，請參閱 [sys.pdw_health_alerts &#40;transact-sql&#41;](../../relational-databases/system-catalog-views/sys-pdw-health-alerts-transact-sql.md) 。|NULL|  
|current_value|**nvarchar(255)**|當警示的類型為 StatusChange 時使用。 這是目前的元件狀態。 針對類型臨界值的警示，值為 Null。 如需警示類型清單，請參閱 [sys.pdw_health_alerts &#40;transact-sql&#41;](../../relational-databases/system-catalog-views/sys-pdw-health-alerts-transact-sql.md) 。|NULL|  
|create_time|**datetime**|產生警示的時間和日期。|NOT NULL|  
  
## <a name="see-also"></a>另請參閱  
 [Azure Synapse Analytics 和平行處理資料倉儲動態管理檢視 &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sql-and-parallel-data-warehouse-dynamic-management-views.md)  
  
  
