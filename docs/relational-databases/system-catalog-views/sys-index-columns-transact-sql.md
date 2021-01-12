---
description: sys.index_columns (Transact-SQL)
title: sys.index_columns (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 07/03/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.index_columns
- sys.index_columns_TSQL
- index_columns
- index_columns_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.index_columns catalog view
ms.assetid: 211471aa-558a-475c-9b94-5913c143ed12
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: cc5ce224b8010a5d49eef7f1be2f872c2685bd7a
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98094580"
---
# <a name="sysindex_columns-transact-sql"></a>sys.index_columns (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  針對每個資料行，各包含一個資料列，這些資料行是 **sys. 索引** 索引或未排序資料表 (堆積) 的一部分。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|object_id|**int**|索引定義所在的物件識別碼。|  
|**index_id**|**int**|資料行定義所在的索引識別碼。|  
|**index_column_id**|**int**|索引資料行的識別碼。 **index_column_id** 只有在 **index_id** 內才是唯一的。|  
|**column_id**|**int**|**Object_id** 中的資料行識別碼。<br /><br /> 0 = 非叢集索引中的資料列識別碼 (RID)。<br /><br /> **column_id** 只有在 **object_id** 內才是唯一的。|  
|**key_ordinal**|**tinyint**|索引鍵資料行組中的序數 (以 1 為基底)。<br /><br /> 0 = 不是索引鍵資料行，或是 XML 索引、資料行存放區索引或空間索引。<br /><br /> 注意： XML 或空間索引不可以是索引鍵，因為基礎資料行是無法比較的，也就是說，它們的值無法排序。|  
|**partition_ordinal**|**tinyint**|分割區資料行組中的序數 (以 1 為基底)。 叢集資料行存放區索引最多可以有 1 個分割區資料行。<br /><br /> 0 = 不是分割區資料行。|  
|**is_descending_key**|**bit**|1 = 索引鍵資料行是以遞減方式排序。<br /><br /> 0 = 索引鍵資料行是以遞增方式排序，或者資料行是資料行存放區索引或雜湊索引的一部分。|  
|**is_included_column**|**bit**|1 = 資料行是利用 CREATE INDEX INCLUDE 子句加入索引中的非索引鍵資料行，或者資料行是資料行存放區索引的一部分。<br /><br /> 0 = 資料行並未加入。<br /><br /> 因為資料行是叢集索引鍵的一部分，所以不會列在 **sys.index_columns** 中。<br /><br /> 因為是分割區資料行而隱含新增的資料行會當做 0 傳回。| 
|**column_store_order_ordinal**</br> 適用于： Azure Synapse Analytics (預覽) |**tinyint**|序數 (以1為基礎) 在排序的叢集資料行存放區索引中的 order 資料行集合內。|
  
## <a name="permissions"></a>權限

 [!INCLUDE[ssCatViewPerm](../../includes/sscatviewperm-md.md)] 如需相關資訊，請參閱 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)。  
  
## <a name="examples"></a>範例

 下列範例會傳回 `Production.BillOfMaterials` 資料表的所有索引和索引資料行。  
  
```sql
USE AdventureWorks2012;  
GO  
SELECT i.name AS index_name  
    ,COL_NAME(ic.object_id,ic.column_id) AS column_name  
    ,ic.index_column_id  
    ,ic.key_ordinal  
,ic.is_included_column  
FROM sys.indexes AS i  
INNER JOIN sys.index_columns AS ic
    ON i.object_id = ic.object_id AND i.index_id = ic.index_id  
WHERE i.object_id = OBJECT_ID('Production.BillOfMaterials');  
  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```
  
index_name                                                 column_name        index_column_id key_ordinal is_included_column  
---------------------------------------------------------- -----------------  --------------- ----------- -------------  
AK_BillOfMaterials_ProductAssemblyID_ComponentID_StartDate ProductAssemblyID  1               1           0  
AK_BillOfMaterials_ProductAssemblyID_ComponentID_StartDate ComponentID        2               2           0  
AK_BillOfMaterials_ProductAssemblyID_ComponentID_StartDate StartDate          3               3           0  
PK_BillOfMaterials_BillOfMaterialsID                       BillOfMaterialsID  1               1           0  
IX_BillOfMaterials_UnitMeasureCode                         UnitMeasureCode    1               1           0  
  
(5 row(s) affected)  
  
```  
  
## <a name="see-also"></a>另請參閱  
 [物件目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/object-catalog-views-transact-sql.md)   
 [目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/catalog-views-transact-sql.md)   
 [sys.indexes &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-indexes-transact-sql.md)   
 [sys.objects &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-objects-transact-sql.md)   
 [CREATE INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/create-index-transact-sql.md)   
 [sys.columns &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-columns-transact-sql.md)   
 [查詢 SQL Server 系統目錄 FAQ](../../relational-databases/system-catalog-views/querying-the-sql-server-system-catalog-faq.md)  
  
  
