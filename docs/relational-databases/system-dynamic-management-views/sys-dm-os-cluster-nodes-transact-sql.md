---
description: sys.dm_os_cluster_nodes (Transact-SQL)
title: sys.dm_os_cluster_nodes (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 08/18/2017
ms.prod: sql
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.dm_os_cluster_nodes_TSQL
- dm_os_cluster_nodes_TSQL
- dm_os_cluster_nodes
- sys.dm_os_cluster_nodes
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_os_cluster_nodes dynamic management view
ms.assetid: 92fa804e-2d08-42c6-a36f-9791544b1d42
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 5276063293d8356902f0afcbf741281bedc24f6d
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98101515"
---
# <a name="sysdm_os_cluster_nodes-transact-sql"></a>sys.dm_os_cluster_nodes (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  針對容錯移轉叢集執行個體組態中之每個節點傳回一個資料列。 如果目前的執行個體就是容錯移轉叢集執行個體，將會傳回定義有此容錯移轉叢集執行個體 (先前稱為「虛擬伺服器」) 之節點的清單。 如果目前的伺服器執行個體不是容錯移轉叢集執行個體，它會傳回空的資料列集。  
  
> **注意：** 若要從或呼叫這個 [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] ，請使用 **sys.dm_pdw_nodes_os_cluster_nodes** 名稱。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**NodeName**|**sysname**|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 容錯移轉叢集執行個體 (虛擬伺服器) 組態中的節點名稱。|  
|status|**int**|容錯移轉叢集實例中節點的狀態 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ：0、1、2、3、-1。 如需詳細資訊，請參閱 [GetClusterNodeState 函數](/windows/win32/api/clusapi/nf-clusapi-getclusternodestate)。|  
|status_description|**Nvarchar (20)**|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 容錯移轉叢集節點狀態的說明。<br /><br /> 0 = 啟動<br /><br /> 1 = 關閉<br /><br /> 2 = 暫停<br /><br /> 3 = 正在加入<br /><br /> 1 = 未知|  
|is_current_owner|bit|1 表示這個節點是 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 容錯移轉叢集資源的目前擁有者。|  
|pdw_node_id|**int**|**適用** 于： [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 、 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]<br /><br /> 此散發所在之節點的識別碼。|  
  
## <a name="remarks"></a>備註  
 當啟用容錯移轉叢集時，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體可以在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 容錯移轉叢集執行個體 (虛擬伺服器) 組態所指定之一部分的任何容錯移轉叢集節點中執行。  
  
> **注意：** 這個視圖會取代 fn_virtualservernodes 函式，在未來的版本中將會被取代。  
  
## <a name="permissions"></a>權限  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體需要伺服器的 VIEW SERVER STATE 權限。  
  
## <a name="examples"></a>範例  
 下列範例會使用 sys。 dm_os_cluster_nodes 傳回位於叢集伺服器執行個體上的節點。  
  
```  
SELECT NodeName, status, status_description, is_current_owner   
FROM sys.dm_os_cluster_nodes;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
|NodeName|status|status_description|is_current_owner|  
|--------------|------------|-------------------------|------------------------|  
|node1|0|啟動|1|  
|node2|0|啟動|0|  
|Node3|1|機|0|  
  
## <a name="see-also"></a>另請參閱  
 [sys.dm_os_cluster_properties &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-os-cluster-properties-transact-sql.md)   
 [sys.dm_io_cluster_shared_drives &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-io-cluster-shared-drives-transact-sql.md)   
 [sys.fn_virtualservernodes &#40;Transact-sql&#41;](../../relational-databases/system-functions/sys-fn-virtualservernodes-transact-sql.md)   
 [動態管理檢視與函數 &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)  
  
