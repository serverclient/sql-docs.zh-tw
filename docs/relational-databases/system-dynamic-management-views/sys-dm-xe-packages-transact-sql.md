---
description: sys.dm_xe_packages (Transact-SQL)
title: sys.dm_xe_packages (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 08/09/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- dm_xe_packages_TSQL
- sys.dm_xe_packages_TSQL
- dm_xe_packages
- sys.dm_xe_packages
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_xe_packages dynamic management view
- extended events [SQL Server], views
ms.assetid: 2e5ecbe9-3ea8-45e6-a161-e31671a03e1d
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: f4d23f493b669744f0c3bb53b6148056131ebe17
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98093923"
---
# <a name="sysdm_xe_packages-transact-sql"></a>sys.dm_xe_packages (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  列出已向擴充的事件引擎註冊的所有封裝。  
  
 
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|NAME|**nvarchar(256)**|封裝的名稱。 描述會從封裝本身公開。 不可為 Null。|  
|guid|**uniqueidentifier**|識別此封裝的 GUID。 不可為 Null。|  
|description|**Nvarchar (3072)**|封裝的描述。 descriptionis 由封裝作者所設定，而且不可為 null。|  
|capabilities|**int**|描述這個封裝之功能的點陣圖。 可為 Null。|  
|capabilities_desc|**nvarchar(256)**|此封裝所有可能之功能的清單。 可為 Null。|  
|module_guid|**nvarchar(60)**|公開此封裝之模組的 GUID。 不可為 Null。|  
|module_address|**varbinary(8)**|載入包含封裝之模組的基底位址。 單一模組可能會公開數個封裝。 不可為 Null。|  
  
## <a name="permissions"></a>權限  
 需要伺服器的 VIEW SERVER STATE 權限。  
  
## <a name="remarks"></a>備註  
 向擴充的事件引擎註冊的封裝會公開事件、事件引發時可以採取的動作，以及同步和非同步處理事件資料的目標。  
  
 這些封裝可以動態地載入處理序位址空間。 當載入封裝時，它會向擴充的事件引擎註冊它所公開的所有物件。  
  
## <a name="relationship-cardinalities"></a>關聯性基數  
  
| 寄件者 | 收件者 | 關聯性 |
| ---- | -- | ------------ |  
|sys.dm_xe_packages.module_address|sys.dm_os_loaded_modules.base_address|多對一|  
  
## <a name="see-also"></a>另請參閱  
 [動態管理檢視與函數 &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)  
  
  

