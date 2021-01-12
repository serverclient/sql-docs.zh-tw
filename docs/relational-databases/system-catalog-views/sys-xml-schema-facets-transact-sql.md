---
description: sys.xml_schema_facets (Transact-SQL)
title: sys.xml_schema_facets (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.xml_schema_facets
- xml_schema_facets_TSQL
- sys.xml_schema_facets_TSQL
- xml_schema_facets
dev_langs:
- TSQL
helpviewer_keywords:
- sys.xml_schema_facets catalog view
ms.assetid: 4402dde9-1877-4872-8550-140dc2a177d2
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 4fd85732af9776c9dc0be1bb65c4402753468060
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98097840"
---
# <a name="sysxml_schema_facets-transact-sql"></a>sys.xml_schema_facets (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  傳回 xml 類型定義的每個 facet (限制) 的資料列， (對應至 **sys.xml_types**) 。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**xml_component_id**|**int**|這個 Facet 所屬的 XML 元件 (類型) 識別碼。|  
|**facet_id**|**int**|Facet 的識別碼 (以 1 為基底的序數)，在元件識別碼中是唯一的。|  
|**類別**|**char(2)**|Facet 的種類：<br /><br /> LG = 長度<br /><br /> LN = 最小長度<br /><br /> LX = 最大長度<br /><br /> PT = 模式 (一般運算式)<br /><br /> EU = 列舉<br /><br /> IN = 最小包含值<br /><br /> IX = 最大包含值<br /><br /> EN = 最小排除值<br /><br /> EX = 最大排除值<br /><br /> DT = 總位數<br /><br /> DF = 小數位數<br /><br /> WS = 空格正規化|  
|**kind_desc**|**Nvarchar (60)**|Facet 種類的描述：<br /><br /> LENGTH<br /><br /> MINIMUM_LENGTH<br /><br /> MAXIMUM_LENGTH<br /><br /> PATTERN<br /><br /> ENUMERATION<br /><br /> MINIMUM_INCLUSIVE_VALUE<br /><br /> MAXIMUM_INCLUSIVE_VALUE<br /><br /> MINIMUM_EXCLUSIVE_VALUE<br /><br /> MAXIMUM_EXCLUSIVE_VALUE<br /><br /> TOTAL_DIGITS<br /><br /> FRACTION_DIGITS<br /><br /> WHITESPACE_NORMALIZATION|  
|**is_fixed**|**bit**|1 = Facet 有固定、預先指定的值。<br /><br /> 0 = 沒有固定值。 (預設值)|  
|**value**|**Nvarchar (4000)**|固定、預先指定的 Facet 值。|  
  
## <a name="permissions"></a>權限  
 [!INCLUDE[ssCatViewPerm](../../includes/sscatviewperm-md.md)] 如需相關資訊，請參閱 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)。  
  
## <a name="see-also"></a>另請參閱  
 [目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/catalog-views-transact-sql.md)   
 [XML 架構 &#40;XML 類型系統&#41; 目錄檢視 &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/xml-schemas-xml-type-system-catalog-views-transact-sql.md)  
  
  
