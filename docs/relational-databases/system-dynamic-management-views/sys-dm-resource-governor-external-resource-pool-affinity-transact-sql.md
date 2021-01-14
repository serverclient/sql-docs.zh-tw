---
description: 'sys.dm_resource_governor_external_resource_pool_affinity (Transact-sql) '
title: sys.dm_resource_governor_external_resource_pool_affinity (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 11/13/2017
ms.prod: sql
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.dm_resource_governor_external_resource_pool_affinity
- sys.dm_resource_governor_external_resource_pool_affinity_TSQL
- dm_resource_governor_external_resource_pool_affinity
- dm_resource_governor_external_resource_pool_affinity_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_resource_governor_external_resource_pool_affinity
- dm_resource_governor_external_resource_pool_affinity
ms.assetid: e32fac49-5161-47c0-8540-af3fe730c00c
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: c93e9c33174172311a7378219440b7c60b7ab4b8
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98172480"
---
# <a name="sysdm_resource_governor_external_resource_pool_affinity-transact-sql"></a>sys.dm_resource_governor_external_resource_pool_affinity (Transact-sql) 
[!INCLUDE [sqlserver2016](../../includes/applies-to-version/sqlserver2016.md)]
**適用於：** [!INCLUDE[sssql15-md](../../includes/sssql16-md.md)] [!INCLUDE[rsql-productname-md](../../includes/rsql-productname-md.md)] 和 [!INCLUDE[sssql17-md](../../includes/sssql17-md.md)] [!INCLUDE[rsql-productnamenew-md](../../includes/rsql-productnamenew-md.md)]

傳回關於目前外部資源集區設定的 CPU 親和性資訊。
  
|資料行名稱|資料類型|描述|
|----------------|---------------|-----------------|
|pool_id|**int**|外部資源集區的識別碼。 不可為 Null。|
|processor_group|**smallint**|Windows 邏輯處理器群組的 ID。 不可為 Null。|
|cpu_mask|**bigint**|代表與此集區相關聯之 Cpu 的二進位遮罩。 不可為 Null。|
  
## <a name="remarks"></a>備註

使用的親和性建立的集區 `AUTO` 不會出現在此視圖中，因為它們沒有相似性。 如需詳細資訊，請參閱 [建立外部資源集區 &#40;transact-sql&#41;](../../t-sql/statements/create-external-resource-pool-transact-sql.md) 和 [ALTER external Resource pool &#40;transact-sql&#41;](../../t-sql/statements/alter-external-resource-pool-transact-sql.md) 語句。

## <a name="permissions"></a>權限

需要 `VIEW SERVER STATE` 權限。

## <a name="see-also"></a>另請參閱

[SQL Server 中的機器學習資源管理](../../machine-learning/administration/resource-governor.md)

[sys.dm_resource_governor_resource_pool_affinity &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pool-affinity-transact-sql.md)

[外部指令碼已啟用伺服器組態選項](../../database-engine/configure-windows/external-scripts-enabled-server-configuration-option.md)

[ALTER EXTERNAL RESOURCE POOL &#40;Transact-SQL&#41;](../../t-sql/statements/alter-external-resource-pool-transact-sql.md)
