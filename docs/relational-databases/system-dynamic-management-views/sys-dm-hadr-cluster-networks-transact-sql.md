---
description: sys.dm_hadr_cluster_networks (Transact-SQL)
title: sys.dm_hadr_cluster_networks (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- dm_hadr_cluster_networks
- sys.dm_hadr_cluster_networks_TSQL
- sys.dm_hadr_cluster_networks
- dm_hadr_cluster_networks_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- Availability Groups [SQL Server], monitoring
- Availability Groups [SQL Server], WSFC clusters
- sys.dm_hadr_cluster_networks dynamic management view
ms.assetid: ece32b15-d63f-4f93-92b7-e2930333e97a
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: d97862778240fac789f95c00a474df12770f8ef3
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98101593"
---
# <a name="sysdm_hadr_cluster_networks-transact-sql"></a>sys.dm_hadr_cluster_networks (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  針對參與可用性群組之子網路組態的每個 WSFC 叢集成員，各傳回一個資料列。 您可以使用此動態管理檢視來驗證為每個可用性複本所設定的網路虛擬 IP。  
  
 主要金鑰： **member_name**  +  **network_subnet_IP**  +  **network_subnet_prefix_length**  
  
 > [!TIP]
 > 從開始 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] ，這個動態管理檢視除了 Always On 可用性群組之外，還支援 Always On 容錯移轉叢集實例。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**member_name**|**nvarchar(128)**|WSFC 叢集中節點的電腦名稱。|  
|**network_subnet_ip**|**Nvarchar (48)**|此電腦所屬之子網路的網路 IP 位址。 這可以是 IPv4 或 IPv6 位址。|  
|**network_subnet_ipv4_mask**|**Nvarchar (45)**|網路的子網路遮罩，可指定此 IP 位址所屬的子網路。 **network_subnet_ipv4_mask** 在 [CREATE Availability GROUP](../../t-sql/statements/create-availability-group-transact-sql.md)或 [ALTER AVAILABILITY group](../../t-sql/statements/alter-availability-group-transact-sql.md)語句的 WITH dhcp 子句中，指定 dhcp <network_subnet_option> 選項 [!INCLUDE[tsql](../../includes/tsql-md.md)] 。<br /><br /> NULL = IPv6 子網路。|  
||||  
|**network_subnet_prefix_length**|**int**|網路 IP 前置長度，可指定此電腦所屬的子網路。|  
|**is_public**|**bit**|網路在 WSFC 叢集上為公用或私用，可為下列其中一個值：<br /><br /> 0 = 私用<br /><br /> 1 = 公用|  
|**is_ipv4**|**bit**|子網路的類型，下列其中一個值：<br /><br /> 1 = IPv4<br /><br /> 0 = IPv6|  
  
## <a name="security"></a>安全性  
  
### <a name="permissions"></a>權限  
 需要伺服器的 VIEW SERVER STATE 權限。  
  
## <a name="see-also"></a>另請參閱  
 [容錯移轉叢集和 AlwaysOn 可用性群組 &#40;SQL Server&#41;](../../database-engine/availability-groups/windows/failover-clustering-and-always-on-availability-groups-sql-server.md)   
 [監視可用性群組 &#40;Transact-sql&#41;](../../database-engine/availability-groups/windows/monitor-availability-groups-transact-sql.md)   
 [sys.dm_os_cluster_nodes &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-os-cluster-nodes-transact-sql.md)   
 [查詢 SQL Server 系統目錄常見問題](../../relational-databases/system-catalog-views/querying-the-sql-server-system-catalog-faq.md)   
 [目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/catalog-views-transact-sql.md)  
  
  
