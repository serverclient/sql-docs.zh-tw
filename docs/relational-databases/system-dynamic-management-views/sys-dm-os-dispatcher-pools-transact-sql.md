---
description: sys.dm_os_dispatcher_pools (Transact-SQL)
title: sys.dm_os_dispatcher_pools (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 08/18/2017
ms.prod: sql
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- dm_os_dispatcher_pools_TSQL
- dm_os_dispatcher_pools
- sys.dm_os_dispatcher_pools
- sys.dm_os_dispatcher_pools_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- extended events [SQL Server], views
- sys.dm_os_dispatcher_pools DMV
ms.assetid: b9edbc83-c6bc-4753-9bb5-a454cfe7d6bf
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 04dbc2fcedb889b54d8a95fa5357b23857cd062a
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98095112"
---
# <a name="sysdm_os_dispatcher_pools-transact-sql"></a>sys.dm_os_dispatcher_pools (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  傳回有關工作階段發送器集區的資訊。 發送器集區是系統元件用來執行背景處理的執行緒集區。  
  
> [!NOTE]  
>  若要從或呼叫這個 [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] ，請使用 **sys.dm_pdw_nodes_os_dispatcher_pools** 名稱。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|dispatcher_pool_address|**varbinary(8)**|發送器集區的位址。 dispatcher_pool_address 是唯一的。 不可為 Null。|  
|類型|**nvarchar(256)**|發送器集區的類型。 不可為 Null。 發送器集區的類型有兩種：<br /><br /> DISP_POOL_XE_ENGINE<br /><br /> DISP_POOL_XE_SESSION<br /><br /> 查詢 DMV 以取得完整清單|  
|NAME|**nvarchar(256)**|發送器集區的名稱。 不可為 Null。|  
|dispatcher_count|**int**|使用中發送器執行緒的數目。 不可為 Null。|  
|dispatcher_ideal_count|**int**|發送器集區可以成長來使用的發送器執行緒數目。 不可為 Null。|  
|dispatcher_timeout_ms|**int**|發送器在結束之前等候新工作的時間 (以毫秒為單位)。 不可為 Null。|  
|dispatcher_waiting_count|**int**|閒置發送器執行緒的數目。 不可為 Null。|  
|queue_length|**int**|等候要由發送器集區所處理的工作項目數。 不可為 Null。|  
|pdw_node_id|**int**|**適用** 于： [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 、 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]<br /><br /> 此散發所在之節點的識別碼。|  
  
## <a name="permissions"></a>權限

在上 [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] ，需要 `VIEW SERVER STATE` 許可權。   
在 SQL Database Basic、S0 和 S1 服務目標上，以及針對彈性集區中的資料庫，則 `Server admin` `Azure Active Directory admin` 需要或帳戶。 在所有其他 SQL Database 服務目標上， `VIEW DATABASE STATE` 資料庫中都需要有許可權。   

## <a name="see-also"></a>另請參閱  
  
  


