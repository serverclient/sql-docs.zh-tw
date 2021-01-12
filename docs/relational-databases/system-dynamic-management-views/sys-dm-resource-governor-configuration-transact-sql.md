---
description: sys.dm_resource_governor_configuration (Transact-SQL)
title: sys.dm_resource_governor_configuration (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- dm_resource_governor_configuration_TSQL
- dm_resource_governor_configuration
- sys.dm_resource_governor_configuration
- sys.dm_resource_governor_configuration_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_resource_governor_configuration dynamic management view
ms.assetid: c89aab6a-0434-4ce6-af8c-f8a1a3284e38
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 8e49c5a5c5fde8bcbb75393146420323b6bf1c8d
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98098814"
---
# <a name="sysdm_resource_governor_configuration-transact-sql"></a>sys.dm_resource_governor_configuration (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  傳回資料列，其中包含資源管理員的目前記憶體中組態狀態。  
  

|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|classifier_function_id|**int**|資源管理員目前使用之分類函數的識別碼。 如果目前沒有使用任何函數，則傳回值 0。 不可為 Null。<br /><br /> **注意：** 此函式是用來分類新的要求，並使用規則將這些要求路由至適當的工作負載群組。 如需詳細資訊，請參閱 [Resource Governor](../../relational-databases/resource-governor/resource-governor.md)。|  
|is_reconfiguration_pending|**bit**|表示是否要使用 ALTER RESOURCE GOVERNOR RECONFIGURE 陳述式對群組或集區進行變更，但尚未套用到記憶體中組態。 傳回的值為下列任一項：<br /><br /> 0 – 不需要重新設定陳述式。<br /><br /> 1 – 需要重新設定陳述式或伺服器重新啟動，才能套用暫止的組態變更。<br /><br /> **注意：** 當 Resource Governor 停用時，傳回的值一律為0。<br /><br /> 不可為 Null。|  
|max_outstanding_io_per_volume|**int**|**適用對象**：[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 及更新版本。<br /><br /> 每個磁碟區未完成的 I/O 數目上限。|  
  
## <a name="remarks"></a>備註  
 這個動態管理檢視會顯示記憶體中組態。 若要查看儲存的組態中繼資料，請使用對應的目錄檢視。  
  
 下列範例顯示如何取得並比較儲存的中繼資料值與資源管理員組態的記憶體中值。  
  
```  
USE master;  
go  
-- Get the stored metadata.  
SELECT   
object_schema_name(classifier_function_id) AS 'Classifier UDF schema in metadata',   
object_name(classifier_function_id) AS 'Classifier UDF name in metadata'  
FROM   
sys.resource_governor_configuration;  
go  
-- Get the in-memory configuration.  
SELECT   
object_schema_name(classifier_function_id) AS 'Active classifier UDF schema',   
object_name(classifier_function_id) AS 'Active classifier UDF name'  
FROM   
sys.dm_resource_governor_configuration;  
go  
```  
  
## <a name="permissions"></a>權限  
 需要 VIEW SERVER STATE 權限。  
  
## <a name="see-also"></a>另請參閱  
 [動態管理檢視與函數 &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)   
 [sys.resource_governor_configuration &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-resource-governor-configuration-transact-sql.md)   
 [資源管理員](../../relational-databases/resource-governor/resource-governor.md)  
  
  

