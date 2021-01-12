---
description: sys.function_order_columns (Transact-SQL)
title: sys.function_order_columns (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- function_order_columns
- sys.function_order_columns_TSQL
- function_order_columns_TSQL
- sys.function_order_columns
dev_langs:
- TSQL
helpviewer_keywords:
- sys.function_order_columns catalog view
ms.assetid: 29287973-3125-4d35-8ca9-92cb45828854
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 8a3408d1f4e7a32aafed3318d32f0a9ed7a97d3f
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98102748"
---
# <a name="sysfunction_order_columns-transact-sql"></a>sys.function_order_columns (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  每個資料行各傳回一個資料列，該資料行屬於 common language runtime (CLR) 資料表值函式的 **ORDER** 運算式的一部分。  

  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|object_id|**int**|定義順序所在之物件的識別碼 (CLR 資料表值函式)。|  
|**order_column_id**|**int**|排序資料行的識別碼。 **order_column_id** 只有在 **object_id** 內才是唯一的。<br /><br /> **order_column_id** 表示這個資料行在排序中的位置。|  
|**column_id**|**int**|**Object_id** 中的資料行識別碼。<br /><br /> **column_id** 只有在 **object_id** 內才是唯一的。|  
|**is_descending**|**bit**|1 = 排序資料行是以遞減方式排序。<br /><br /> 0 = 排序資料行是以遞增方式排序。|  
  
## <a name="permissions"></a>權限  
 [!INCLUDE[ssCatViewPerm](../../includes/sscatviewperm-md.md)] 如需相關資訊，請參閱 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)。  
  
## <a name="see-also"></a>另請參閱  
 [物件目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/object-catalog-views-transact-sql.md)   
 [目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/catalog-views-transact-sql.md)  
  
  
