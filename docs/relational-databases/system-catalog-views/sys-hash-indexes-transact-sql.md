---
description: sys.hash_indexes (Transact-SQL)
title: sys.hash_indexes (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.hash_indexes_TSQL
- hash_indexes
- sys.hash_indexes
- hash_indexes_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.hash_indexes catalog view
ms.assetid: d9e230fb-d3ff-486f-86ef-44898f0a703e
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: b422523074806b6cac30fc56859006561fc0c828
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98095524"
---
# <a name="syshash_indexes-transact-sql"></a>sys.hash_indexes (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  顯示目前雜湊索引和雜湊索引屬性。 只有 [記憶體內部 OLTP &#40;In-Memory 優化&#41;](../../relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization.md)才支援雜湊索引。  
  
 Sys.hash_indexes view 包含與 sys. 索引視圖相同的資料行，以及一個名為 **bucket_count** 的額外資料行。 如需 sys.hash_indexes view 中其他資料行的詳細資訊，請參閱 [transact-sql&#41;的 sys. 索引 &#40;](../../relational-databases/system-catalog-views/sys-indexes-transact-sql.md)。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**\<inherited columns>**||從 [sys. 索引 &#40;transact-sql&#41;](../../relational-databases/system-catalog-views/sys-indexes-transact-sql.md)繼承資料行。|  
|**bucket_count**|**int**|雜湊索引的雜湊值區計數。<br /><br /> 如需 bucket_count 值的詳細資訊，包括設定值的指導方針，請參閱 [CREATE TABLE &#40;transact-sql&#41;](../../t-sql/statements/create-table-transact-sql.md)。|  
  
## <a name="permissions"></a>權限  
 [!INCLUDE[ssCatViewPerm](../../includes/sscatviewperm-md.md)]. 如需相關資訊，請參閱 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)。  
  
## <a name="examples"></a>範例  
  
```  
SELECT object_name([object_id]) AS 'table_name', [object_id],  
     [name] AS 'index_name', [type_desc], [bucket_count]   
FROM sys.hash_indexes   
WHERE OBJECT_NAME([object_id]) = 'T1';  
```  
  
## <a name="see-also"></a>另請參閱  
 [物件目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/object-catalog-views-transact-sql.md)   
 [目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/catalog-views-transact-sql.md)  
  
  
