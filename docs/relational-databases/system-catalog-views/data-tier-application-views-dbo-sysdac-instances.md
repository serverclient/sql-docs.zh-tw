---
description: 資料層應用程式視圖-dbo.sysdac_instances
title: dbo.sysdac_instances (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/04/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- dbo.sysdac_instances_TSQL
- sysdac_instances
- sysdac_instances_TSQL
- dbo.sysdac_instances
dev_langs:
- TSQL
helpviewer_keywords:
- dbo.sysdac_instances
- sysdac_instances
ms.assetid: 28285f3d-3889-439f-8b24-3bdef08e46b4
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: f5178fdb50b8a959471c8be973080d1416ba10f6
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98092655"
---
# <a name="data-tier-application-views---dbosysdac_instances"></a>資料層應用程式視圖-dbo.sysdac_instances
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  針對部署至 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 執行個體的每個資料層應用程式 (DAC) 執行階段，各顯示一個資料列。 sysdac_instances 屬於 msdb 資料庫中的 dbo 架構。 下表描述 sysdac_instances view 中的資料行。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|instance_id|**uniqueidentifier**|DAC 執行個體的識別碼。|  
|instance_name|**sysname**|部署 DAC 時指定之 DAC 執行個體的名稱。|  
|type_name|**sysname**|建立 DAC 封裝時指定之 DAC 的名稱。|  
|type_version|**Nvarchar (64)**|建立 DAC 封裝時指定之 DAC 的版本。|  
|description|**nvarchar(4000)**|建立 DAC 封裝時寫入之 DAC 的描述。|  
|type_stream|**varbinary(max)**|包含 DAC 中之邏輯物件編碼表示法的位元資料流，例如，資料表和檢視表。|  
|date_created|**datetime**|建立 DAC 執行個體的日期和時間。|  
|created_by|**sysname**|建立 DAC 執行個體的登入。|  
|database_name|**sysname**|為 DAC 執行個體建立的資料庫名稱。|  
  
## <a name="remarks"></a>備註  
 DAC 包含 DAC 類型，這是應用程式所使用之邏輯資料層物件的定義，例如資料表和檢視表。 DAC 封裝是用來部署 DAC 的檔案。 DAC 封裝包含 DAC 類型中所有邏輯物件的表示。 DAC 封裝可以用來將一個或多個 DAC 的複本或執行個體部署至 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 的執行個體。 從相同 DAC 封裝部署的每個 DAC 執行個體都共用相同的類型，但會被指派一個唯一的執行個體名稱和識別碼。  
  
## <a name="permissions"></a>權限  
 需要系統管理員 (sysadmin) 固定伺服器角色中的成員資格，才能檢視所有資料行。 Public 角色的成員可以檢視 instance_name、description 與 type_version 資料行。  
  
## <a name="see-also"></a>另請參閱  
 [資料層應用程式](../../relational-databases/data-tier-applications/data-tier-applications.md)   
 [資料層應用程式視圖 &#40;Transact-sql&#41;]()  
  
