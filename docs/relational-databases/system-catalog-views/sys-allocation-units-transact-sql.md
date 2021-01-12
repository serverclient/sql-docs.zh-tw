---
description: sys.allocation_units (Transact-SQL)
title: sys.allocation_units (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.allocation_units_TSQL
- sys.allocation_units
- allocation_units_TSQL
- allocation_units
dev_langs:
- TSQL
helpviewer_keywords:
- sys.allocation_units catalog view
ms.assetid: ec9de780-68fd-4551-b70b-2d3ab3709b3e
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 12a5924d1137f9face839899a742f20d3565f267
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98091570"
---
# <a name="sysallocation_units-transact-sql"></a>sys.allocation_units (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  針對資料庫中每個配置單位，各包含一個資料列。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|allocation_unit_id|**bigint**|配置單位的識別碼。 在資料庫中，這是唯一的。|  
|類型|**tinyint**|配置單位的類型：<br /><br /> 0 = 已卸除<br /><br /> 1 = 同資料列資料 (除 LOB 資料類型之外的所有資料類型)<br /><br /> 2 = 大型物件 (LOB) 資料 (**text**、 **Ntext**、 **image**、 **XML**、大型實數值型別和 CLR 使用者定義型別) <br /><br /> 3 = 資料列溢位資料|  
|type_desc|**nvarchar(60)**|配置單位類型的描述：<br /><br /> **下降**<br /><br /> **IN_ROW_DATA**<br /><br /> **LOB_DATA**<br /><br /> **ROW_OVERFLOW_DATA**|  
|container_id|**bigint**|與配置單位相關聯的儲存體容器識別碼。<br /><br /> 如果 type = 1 或 3，則 container_id = sys.partitions.hobt_id。<br /><br /> 如果 type 是 2，則 container_id = sys.partitions.partition_id。<br /><br /> 0 = 標示要延遲卸除的配置單位|  
|data_space_id|**int**|這個配置單位所在的檔案群組識別碼。|  
|total_pages|**bigint**|這個配置單位所配置或保留的總頁數。|  
|used_pages|**bigint**|實際使用中的總頁數。|  
|data_pages|**bigint**|含有下列項目的使用頁數：<br /><br /> 同資料列資料<br /><br /> LOB 資料<br /><br /> 資料列溢位資料<br /><br /> <br /><br /> 請注意，傳回的值會排除內部索引頁面和建構管理頁面。|  
  
> [!NOTE]  
>  當您卸除或重建大型索引時，或卸除或截斷大型資料表時，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 會延遲取消配置實際的頁面及其相關聯鎖定，直到認可交易之後。 延遲的卸除作業並不會立即釋出已配置的空間。 因此，在卸除或截斷大型物件之後，sys.allocation_units 傳回的值不一定能反映實際可用的磁碟空間。  
  
## <a name="permissions"></a>權限  
 需要 **public** 角色的成員資格。 如需相關資訊，請參閱 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)。  
  
## <a name="see-also"></a>另請參閱  
 [sys.partitions &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-partitions-transact-sql.md)   
 [物件目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/object-catalog-views-transact-sql.md)   
 [目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/catalog-views-transact-sql.md)  
  
  
