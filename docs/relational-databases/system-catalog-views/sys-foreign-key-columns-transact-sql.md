---
description: sys.foreign_key_columns (Transact-SQL)
title: sys.foreign_key_columns (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.foreign_key_columns
- foreign_key_columns
- sys.foreign_key_columns_TSQL
- foreign_key_columns_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.foreign_key_columns catalog view
ms.assetid: 7247f065-5441-4bcf-9f25-c84a03290dc6
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 60c8b632be18b81758dd458923a7b38c83297e07
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98094586"
---
# <a name="sysforeign_key_columns-transact-sql"></a>sys.foreign_key_columns (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  針對組成一個外部索引鍵的每個資料行或一組資料行，各包含一個資料列。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**constraint_object_id**|**int**|FOREIGN KEY 條件約束的識別碼。|  
|**constraint_column_id**|**int**|組成外鍵的資料行或資料行集的識別碼 (*1. n n* 其中 n = 資料行數目) 。|  
|**parent_object_id**|**int**|作為參考物件使用之條件約束父系的識別碼。|  
|**parent_column_id**|**int**|作為參考資料行使用之父資料行的識別碼。|  
|**referenced_object_id**|**int**|具有候選索引鍵之被參考物件的識別碼。|  
|**referenced_column_id**|**int**|所參考之資料行 (候選索引鍵資料行) 的識別碼。|  
  
## <a name="permissions"></a>權限  
 [!INCLUDE[ssCatViewPerm](../../includes/sscatviewperm-md.md)] 如需相關資訊，請參閱 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)。  
  
## <a name="see-also"></a>另請參閱  
 [物件目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/object-catalog-views-transact-sql.md)   
 [目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/catalog-views-transact-sql.md)   
 [查詢 SQL Server 系統目錄 FAQ](../../relational-databases/system-catalog-views/querying-the-sql-server-system-catalog-faq.md)  
  
  
