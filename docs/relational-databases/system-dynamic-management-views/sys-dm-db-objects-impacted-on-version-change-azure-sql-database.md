---
description: sys.dm_db_objects_impacted_on_version_change (Azure SQL Database)
title: sys.dm_db_objects_impacted_on_version_change
titleSuffix: Azure SQL Database
ms.date: 03/03/2017
ms.service: sql-database
ms.reviewer: ''
ms.topic: language-reference
f1_keywords:
- sys.dm_db_objects_impacted_on_version_change_TSQL
- dm_db_objects_impacted_on_version_change
- dm_db_objects_impacted_on_version_change_TSQL
- sys.dm_db_objects_impacted_on_version_change
dev_langs:
- TSQL
helpviewer_keywords:
- dm_db_objects_impacted_on_version_change
- sys.dm_db_objects_impacted_on_version_change
ms.assetid: b94af834-c4f6-4a27-80a6-e8e71fa8793a
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: = azuresqldb-current
ms.custom: seo-dt-2019
ms.openlocfilehash: f7830e874026b9d5af250fde294c8c72294d013d
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98097698"
---
# <a name="sysdm_db_objects_impacted_on_version_change-azure-sql-database"></a>sys.dm_db_objects_impacted_on_version_change (Azure SQL Database)
[!INCLUDE[Azure SQL Database Azure SQL Managed Instance](../../includes/applies-to-version/asdb-asdbmi.md)]

  這個資料庫範圍的系統檢視是設計成提供早期警告系統，以判斷會受到 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 的主要版本升級影響的物件。 您可以在升級前後使用此檢視，以取得受影響物件的完整列舉。 您需要在每個資料庫中查詢這個檢視，才能取得整個伺服器的完整計量資料。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|Class - 類別|**int** 非 Null|會受到影響的物件類別：<br /><br /> **1** = 條件約束<br /><br /> **7** = 索引和堆積|  
|class_desc|**Nvarchar (60)** 非 Null|類別的描述：<br /><br /> **OBJECT_OR_COLUMN**<br /><br /> **INDEX**|  
|major_id|**int** 非 Null|條件約束的物件識別碼，或包含索引或堆積之資料表的物件識別碼。|  
|minor_id|**int** 空|**NULL** 代表條件約束<br /><br /> 索引和堆積的 Index_id|  
|相依性|**Nvarchar (60)** 非 Null|導致條件約束或索引受影響的相依性說明。 相同值也用於升級期間所產生的警告。<br /><br /> 範例：<br /><br /> **space** (用於內建功能)<br /><br /> **geometry** (用於系統 UDT)<br /><br /> **geography::Parse** (用於系統 UDT 方法)|  
  
## <a name="permissions"></a>權限  
 需要 VIEW DATABASE STATE 權限。  
  
## <a name="example"></a>範例  
 下列範例會顯示 **sys.dm_db_objects_impacted_on_version_change** 上的查詢，以尋找升級到下一個主要伺服器版本所影響的物件。  
  
```  
SELECT * FROM sys.dm_db_objects_disabled_on_version_change;  
GO  
```  
  
```  
class  class_desc        major_id    minor_id    dependency                       
------ ----------------- ----------- ----------- ----------   
1      OBJECT_OR_COLUMN  181575685   NULL        geometry                        
7      INDEX             37575172    1           geometry                        
7      INDEX             2121058592  1           geometry                        
1      OBJECT_OR_COLUMN  101575400   NULL        geometry     
```  
  
## <a name="remarks"></a>備註  
  
### <a name="how-to-update-impacted-objects"></a>如何更新受影響的物件  
 以下的步驟順序說明了在六月即將發生的服務版本升級之後所要採取的更正動作。  
  
|單|受影響的物件|更正動作|  
|-----------|---------------------|-----------------------|  
|1|**索引數**|重建 **sys.dm_db_objects_impacted_on_version_change** 所識別的任何索引，例如：  `ALTER INDEX ALL ON <table> REBUILD`<br />或<br />`ALTER TABLE <table> REBUILD`|  
|2|**Object**|在重新計算基礎資料表中的幾何和地理資料之後，必須重新驗證 **sys.dm_db_objects_impacted_on_version_change** 所識別的所有條件約束。 請針對條件約束使用 ALTER TABLE 重新驗證。 <br />例如： <br />`ALTER TABLE <tab> WITH CHECK CHECK CONSTRAINT <constraint name>`<br />或<br />`ALTER TABLE <tab> WITH CHECK CONSTRAINT ALL`|  
  
  
