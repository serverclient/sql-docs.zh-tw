---
description: sys.availability_group_listener_ip_addresses (Transact-SQL)
title: sys.availability_group_listener_ip_addresses (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- availability_group_listener_ip_addresses
- sys.availability_group_listener_ip_addresses
- availability_group_listener_ip_addresses_TSQL
- sys.availability_group_listener_ip_addresses_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- Availability Groups [SQL Server], monitoring
- Availability Groups [SQL Server], listeners
- sys.availability_group_listener_ip_addresses catalog view
ms.assetid: e515fa6b-1354-4110-9b70-ab2e6164c992
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: f8cb764514edcc626feb41f798d0125f75095034
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98100695"
---
# <a name="sysavailability_group_listener_ip_addresses-transact-sql"></a>sys.availability_group_listener_ip_addresses (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  針對 Windows Server 容錯移轉叢集 (WSFC) 叢集中與任何 Always On 可用性群組接聽程式相關聯的每一個 IP 位址，各傳回一個資料列。  
  
 主要金鑰： **listener_id**  +  **ip_address**  +  **ip_sub_mask**  
  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**listener_id**|**Nvarchar (36)**|Windows Server 容錯移轉叢集 (WSFC) 叢集中的資源 GUID。|  
|**ip_address**|**Nvarchar (48)**|可用性群組接聽程式之已設定的虛擬 IP 位址。 傳回單一 IPv4 或 IPv6 位址。|  
|**ip_subnet_mask**|**Nvarchar (15)**|為 IPv4 位址設定的 IP 子網路遮罩 (如果有的話)，這是針對可用性群組接聽程式所設定。<br /><br /> NULL = IPv6 子網路|  
|**is_dhcp**|**bit**|IP 位址是否由 DHCP 設定，可為下列其中一個值：<br /><br /> 0 = IP 位址不是由 DHCP 設定。<br /><br /> 1 = IP 位址是由 DHCP 設定。|  
|**network_subnet_ip**|**Nvarchar (48)**|網路的子網路 IP 位址，可指定此 IP 位址所屬的子網路。|  
|**network_subnet_prefix_length**|**int**|此 IP 位址所屬之子網路的網路子網路前置長度。|  
|**network_subnet_ipv4_mask**|**Nvarchar (45)**|此 IP 位址所屬之子網路的網路子網路遮罩。 **network_subnet_ipv4_mask** 在 [CREATE Availability GROUP](../../t-sql/statements/create-availability-group-transact-sql.md)或 [ALTER AVAILABILITY group](../../t-sql/statements/alter-availability-group-transact-sql.md)語句的 WITH dhcp 子句中，指定 dhcp <network_subnet_option> 選項 [!INCLUDE[tsql](../../includes/tsql-md.md)] 。<br /><br /> NULL = IPv6 子網路|  
|**state**|**tinyint**|WSFC 叢集中 IP 資源的 ONLINE/OFFLINE 狀態，可為下列其中一個值：<br /><br /> 1 = 線上。 IP 資源在線上。<br /><br /> 0 = 離線。 IP 資源離線。<br /><br /> 2 = 線上暫止。 IP 資源已離線，但是正在連線。<br /><br /> 3 = 失敗。 IP 資源已在連線，但卻失敗。|  
|**state_desc**|**nvarchar(60)**|**狀態** 的描述，下列其中一個：<br /><br /> ONLINE<br /><br /> OFFLINE<br /><br /> ONLINE_PENDING<br /><br /> FAILED|  
  
## <a name="security"></a>安全性  
  
### <a name="permissions"></a>權限  
 [!INCLUDE[ssCatViewPerm](../../includes/sscatviewperm-md.md)] 如需相關資訊，請參閱 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)。  
  
## <a name="see-also"></a>另請參閱  
 [查詢 SQL Server 系統目錄常見問題](../../relational-databases/system-catalog-views/querying-the-sql-server-system-catalog-faq.md)   
 [目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/catalog-views-transact-sql.md)  
  
  
