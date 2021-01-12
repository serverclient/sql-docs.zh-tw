---
description: sys.computed_columns (Transact-SQL)
title: sys.computed_columns (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.computed_columns_TSQL
- sys.computed_columns
- computed_columns_TSQL
- computed_columns
dev_langs:
- TSQL
helpviewer_keywords:
- sys.computed_columns catalog view
ms.assetid: c962c619-e18f-4315-9251-8d9862462299
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: a7dbe7e9176605dea875e1846b72e1f00301be55
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98100690"
---
# <a name="syscomputed_columns-transact-sql"></a>sys.computed_columns (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  針對在 **sys.** 資料行中找到的每個資料行，各包含一個資料列，也就是計算資料行。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**\<Inherited columns>**||**Sys.computed_columns** view 會傳回 **sys. columns** 視圖中的所有資料行。 另外亦將傳回以下所述的其他資料行。 如需 **sys.computed_columns** view 繼承自 **sys.** 資料行的描述，請參閱 [sys. columns &#40;transact-sql&#41;](../../relational-databases/system-catalog-views/sys-columns-transact-sql.md)。 在 **sys.computed_columns** 視圖中， **is_computed** 資料行的值一律設定為1。|  
|**definition**|**nvarchar(max)**|定義這個計算資料行的 SQL 文字。|  
|**uses_database_collation**|**bit**|1 = 資料行定義須依據資料庫的預設定序進行正確評估；否則為 0。 這種相依性可以防止資料庫預設定序變更。|  
|**is_persisted**|**bit**|計算資料行是保存計算資料行。|  
  
## <a name="permissions"></a>權限  
 [!INCLUDE[ssCatViewPerm](../../includes/sscatviewperm-md.md)] 如需相關資訊，請參閱 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)。  
  
## <a name="see-also"></a>另請參閱  
 [物件目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/object-catalog-views-transact-sql.md)   
 [目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/catalog-views-transact-sql.md)  
  
  
