---
description: sys.table_types (Transact-SQL)
title: sys.table_types (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- table_types_TSQL
- sys.table_types
- sys.table_types_TSQL
- table_types
dev_langs:
- TSQL
helpviewer_keywords:
- table types [SQL Server]
- table-valued parameters, sys.table_types
- sys.table_types
- UDTT
ms.assetid: c05fd873-aff2-4a89-9936-a54c2ea09996
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: c920317b40f0fbcd922c1fe0c440c1a210e0a984
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98094439"
---
# <a name="systable_types-transact-sql"></a>sys.table_types (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  顯示 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中使用者定義資料表類型的屬性。 資料表類型是指可以從中宣告資料表變數或資料表值參數的類型。 每個資料表類型都有一個 **type_table_object_id** ，也就是 [sys. objects](../../relational-databases/system-catalog-views/sys-objects-transact-sql.md) 目錄檢視中的外鍵。 您可以使用此識別碼資料行，以類似于一般資料表 **object_id** 資料行的方式來查詢不同的目錄檢視，以探索資料表類型的結構，例如其資料行和條件約束。    
 
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|*\<inherited columns>*||如需此視圖所繼承之資料行的清單，請參閱 [sys. types &#40;transact-sql&#41;](../../relational-databases/system-catalog-views/sys-types-transact-sql.md)。|  
|**type_table_object_id**|**int**|物件識別碼。 此碼在資料庫中是唯一的。|  
|**is_memory_optimized**|**bit**|**適用對象**：[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 及更新版本。<br /><br /> 以下是可能的值：<br /><br /> 0 = 不是記憶體最佳化的<br /><br /> 1 = 是記憶體最佳化的<br /><br /> 預設值是 0 值。<br /><br /> 資料表類型一定會使用 DURABILITY = SCHEMA_ONLY 來建立。 只有結構描述會在磁碟上保存。|  
  
## <a name="permissions"></a>權限  
 [!INCLUDE[ssCatViewPerm](../../includes/sscatviewperm-md.md)] 如需相關資訊，請參閱 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)。  
  
## <a name="see-also"></a>另請參閱  
 [物件目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/object-catalog-views-transact-sql.md)   
 [使用 Table-Valued 參數 &#40;資料庫引擎&#41;](../../relational-databases/tables/use-table-valued-parameters-database-engine.md)   
 [記憶體內部 OLTP &#40;記憶體內部最佳化&#41;](../../relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization.md)  
  
  
