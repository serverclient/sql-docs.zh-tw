---
description: sys.endpoints (Transact-SQL)
title: sys. 端點 (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- endpoints
- sys.endpoints
- endpoints_TSQL
- sys.endpoints_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.endpoints catalog view
ms.assetid: e6dafa4e-e47e-43ec-acfc-88c0af53c1a1
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 765e7a2029f28a543a9b0ae6a08a7f6fb6fdb775
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98093118"
---
# <a name="sysendpoints-transact-sql"></a>sys.endpoints (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  針對在系統建立的每個端點，各包含一個資料列。 SYSTEM 端點一定只有一個。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**name**|**sysname**|端點的名稱。 在伺服器中，這是唯一的。 不可為 Null。|  
|**endpoint_id**|**int**|端點的識別碼。 在伺服器中，這是唯一的。 識別碼小於 65536 的端點是系統端點。 不可為 Null。|  
|**principal_id**|**int**|建立和擁有這個端點的伺服器主體識別碼。 可為 Null。|  
|**protocol**|**tinyint**|端點通訊協定。<br /><br /> 1 = HTTP<br /><br /> 2 = TCP<br /><br /> 3 = 具名管道<br /><br /> 4 = 共用記憶體<br /><br /> 5 = 虛擬介面配接器 (VIA)<br /><br /> 不可為 Null。|  
|**protocol_desc**|**nvarchar(60)**|端點通訊協定的描述。 NULLABLE。 下列其中一個值：<br /><br /> **HTTP**<br /><br /> **TCP**<br /><br /> **NAMED_PIPES**<br /><br /> **SHARED_MEMORY**<br /><br /> **VIA** 注意： VIA 通訊協定已被取代。 [!INCLUDE[ssNoteDepFutureAvoid](../../includes/ssnotedepfutureavoid-md.md)]|  
|**type**|**tinyint**|端點裝載類型。<br /><br /> 1 = SOAP<br /><br /> 2 = TSQL<br /><br /> 3 = SERVICE_BROKER<br /><br /> 4 = DATABASE_MIRRORING<br /><br /> 不可為 Null。|  
|**type_desc**|**nvarchar(60)**|端點裝載類型的描述。 可為 Null。 下列其中一個值：<br /><br /> **肥皂**<br /><br /> **TSQL**<br /><br /> **SERVICE_BROKER**<br /><br /> **DATABASE_MIRRORING**|  
|**state**|**tinyint**|端點狀態。<br /><br /> 0 = STARTED，接聽和處理要求。<br /><br /> 1 = STOPPED，接聽但不處理要求。<br /><br /> 2 = DISABLED，不接聽<br /><br /> 預設狀態為 1。 可為 Null。|  
|**state_desc**|**nvarchar(60)**|端點狀態的描述。<br /><br /> STARTED = 接聽和處理要求。<br /><br /> STOPPED = 接聽但不處理要求。<br /><br /> DISABLED = 不接聽。<br /><br /> 預設狀態是 STOPPED。<br /><br /> 可為 Null。|  
|**is_admin_endpoint**|**bit**|指出端點是否作為管理使用。<br /><br /> 0 = 非管理端點。<br /><br /> 1 = 端點是管理端點。<br /><br /> 不可為 Null。|  
  
## <a name="permissions"></a>權限  
 [!INCLUDE[ssCatViewPerm](../../includes/sscatviewperm-md.md)] 如需相關資訊，請參閱 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)。  
  
## <a name="see-also"></a>另請參閱  
 [端點目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/endpoints-catalog-views-transact-sql.md)   
 [目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/catalog-views-transact-sql.md)  
  
  
