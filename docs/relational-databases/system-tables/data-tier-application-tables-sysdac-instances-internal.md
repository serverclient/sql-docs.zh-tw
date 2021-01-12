---
description: 資料層應用程式資料表 - sysdac_instances_internal
title: sysdac_instances_internal (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/03/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sysdac_instances_internal_TSQL
- sysdac_instances_internal
dev_langs:
- TSQL
helpviewer_keywords:
- sysdac_instances_internal
ms.assetid: d2d52cc4-3463-431a-b779-6fbfdeee1dfc
author: cawrites
ms.author: chadam
ms.openlocfilehash: a8ac4d2cd2f70ee9ee09fc2479f6018401839cd6
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98093753"
---
# <a name="data-tier-application-tables---sysdac_instances_internal"></a>資料層應用程式資料表 - sysdac_instances_internal
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  針對部署至 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 執行個體的每個資料層應用程式 (DAC) 執行階段，各顯示一個資料列。 此資料表儲存在 msdb 資料庫的 dbo 架構中。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|instance_id|**uniqueidentifier**|DAC 執行個體的識別碼。|  
|instance_name|**sysname**|部署執行個體時指定之 DAC 執行個體的名稱。|  
|type_name|**sysname**|建立 DAC 封裝時指定之 DAC 的名稱。|  
|type_version|**Nvarchar (64)**|建立 DAC 封裝時指定之 DAC 的版本。|  
|description|**nvarchar(4000)**|建立 DAC 封裝時寫入之 DAC 的描述。|  
|type_stream|**varbinary(max)**|包含 DAC 中之邏輯物件編碼表示法的位元資料流，例如，資料表和檢視表。|  
|date_created|**datetime**|建立 DAC 執行個體的日期和時間。|  
|created_by|**sysname**|建立 DAC 執行個體的登入。|  
  
## <a name="remarks"></a>備註  
 此視圖的唯讀存取權可供具有連接 master 資料庫之許可權的所有使用者使用。  
  
## <a name="permissions"></a>權限  
 需要系統管理員 (sysadmin) 固定伺服器角色中的成員資格。  
  
## <a name="see-also"></a>另請參閱  
 [資料層應用程式](../../relational-databases/data-tier-applications/data-tier-applications.md)   
 [dbo.sysdac_instances &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/data-tier-application-views-dbo-sysdac-instances.md)  
  
  
