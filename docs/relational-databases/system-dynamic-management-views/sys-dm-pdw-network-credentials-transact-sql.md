---
description: 'sys.dm_pdw_network_credentials (Transact-sql) '
title: sys.dm_pdw_network_credentials (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/07/2017
ms.prod: sql
ms.technology: data-warehouse
ms.reviewer: ''
ms.topic: language-reference
dev_langs:
- TSQL
ms.assetid: d4fee3ad-6285-4ea5-8513-5e6eb617abb0
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>= aps-pdw-2016'
ms.openlocfilehash: 281d33274925e22ca30c44317f9926d820011c22
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98092740"
---
# <a name="sysdm_pdw_network_credentials-transact-sql"></a>sys.dm_pdw_network_credentials (Transact-sql) 
[!INCLUDE [pdw](../../includes/applies-to-version/pdw.md)]

  針對所有目標伺服器，傳回儲存在設備中所有網路認證的清單 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] 。 系統會列出控制項節點和每個計算節點的結果。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|pdw_node_id|**int**|與節點相關聯的唯一數值識別碼。|  
|target_server_name|**nvarchar(32)**|[!INCLUDE[ssPDW](../../includes/sspdw-md.md)]將會使用使用者名稱和密碼認證來存取之目標伺服器的 IP 位址。|  
|username|**nvarchar(32)**|儲存密碼的使用者名稱。|  
|last_modified|**datetime**|上次修改認證之作業的日期時間。|  
  
## <a name="permissions"></a>權限  
 需要 VIEW SERVER STATE。  
  
## <a name="general-remarks"></a>一般備註  
 這個動態管理檢視的索引鍵是 *pdw_node_id* 加 *target_server_name*。  
  
## <a name="see-also"></a>另請參閱  
 [Azure Synapse Analytics 和平行處理資料倉儲動態管理檢視 &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sql-and-parallel-data-warehouse-dynamic-management-views.md)  
  
  
