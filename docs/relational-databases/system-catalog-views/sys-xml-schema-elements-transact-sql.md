---
description: sys.xml_schema_elements (Transact-SQL)
title: sys.xml_schema_elements (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.xml_schema_elements
- sys.xml_schema_elements_TSQL
- xml_schema_elements
- xml_schema_elements_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.xml_schema_elements catalog view
ms.assetid: 190ed0cd-0c5e-4607-9db4-9e77cacf17d7
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: d867720ba017f45ab3e413b452ae975e2930727c
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98092968"
---
# <a name="sysxml_schema_elements-transact-sql"></a>sys.xml_schema_elements (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  針對每個 XML 架構元件，各傳回一個資料列，該元件是型別， **symbol_space** 為 **E**。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**\<inherited columns>**|**--**|從 [sys.xml_schema_components](../../relational-databases/system-catalog-views/sys-xml-schema-components-transact-sql.md)繼承資料行。|  
|**is_default_fixed**|**bit**|1 = 預設值是固定值。 在 XML 執行個體中不能覆寫這個值。<br /><br /> 0 = 預設值不是元素的固定值。 (預設值)。|  
|**is_abstract**|**bit**|1 = 元素是抽象的，無法使用於執行個體文件中。 元素的替代群組成員必須出現在執行個體文件中。<br /><br /> 0 = 元素不是抽象的。 (預設值)。|  
|**is_nillable**|**bit**|1 = 元素為 Nillable。<br /><br /> 0 = 元素不是 Nillable。 (預設值)|  
|**must_be_qualified**|**bit**|1 = 元素必須明確限定命名空間。<br /><br /> 0 = 元素可隱含限定命名空間。 (預設值)|  
|**is_extension_blocked**|**bit**|1 = 封鎖取代成某延伸類型的執行個體。<br /><br /> 0 = 允許取代成延伸類型。 (預設值)|  
|**is_restriction_blocked**|**bit**|1 = 封鎖取代成某限制類型的執行個體。<br /><br /> 0 = 允許具有限制類型的取代。 (預設值)|  
|**is_substitution_blocked**|**bit**|1 = 無法使用替代群組的執行個體。<br /><br /> 0 = 允許取代成替代群組。 (預設值)|  
|**is_final_extension**|**bit**|1 = 不允許取代成某延伸類型的執行個體。<br /><br /> 0 = 允許取代成某延伸類型的執行個體。 (預設值)|  
|**is_final_restriction**|**bit**|1 = 不允許取代成某限制類型的執行個體。<br /><br /> 0 = 允許取代成某限制類型的執行個體。 (預設值)|  
|**default_value**|**Nvarchar (4000)**|元素的預設值。 如果沒有提供預設值，則為 NULL。|  
  
## <a name="permissions"></a>權限  
 [!INCLUDE[ssCatViewPerm](../../includes/sscatviewperm-md.md)] 如需相關資訊，請參閱 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)。  
  
## <a name="see-also"></a>另請參閱  
 [目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/catalog-views-transact-sql.md)   
 [XML 架構 &#40;XML 類型系統&#41; 目錄檢視 &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/xml-schemas-xml-type-system-catalog-views-transact-sql.md)  
  
  
