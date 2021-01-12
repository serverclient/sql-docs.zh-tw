---
description: sys.type_assembly_usages (Transact-SQL)
title: sys.type_assembly_usages (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.type_assembly_usages
- sys.type_assembly_usages_TSQL
- type_assembly_usages_TSQL
- type_assembly_usages
dev_langs:
- TSQL
helpviewer_keywords:
- sys.type_assembly_usages catalog view
ms.assetid: 79b8bf25-6e4e-4a07-ae93-7a4e44f65171
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 53884fd9c27b3ab016bd3e1a67c2e4b2beb2285d
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98094296"
---
# <a name="systype_assembly_usages-transact-sql"></a>sys.type_assembly_usages (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  針對每個組件參考類型，各包含一個資料列。  
  

  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**user_type_id**|**int**|類型的識別碼。<br /><br /> 若要傳回類型的名稱，請在此資料行上聯結至 [sys. 類型](../../relational-databases/system-catalog-views/sys-types-transact-sql.md) 目錄檢視。|  
|**assembly_id**|**int**|組件的識別碼|  
  
## <a name="permissions"></a>權限  
 需要 **public** 角色的成員資格。 如需相關資訊，請參閱 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)。  
  
## <a name="see-also"></a>另請參閱  
 [純量類型目錄檢視 &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/scalar-types-catalog-views-transact-sql.md)   
 [目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/catalog-views-transact-sql.md)  
  
  
