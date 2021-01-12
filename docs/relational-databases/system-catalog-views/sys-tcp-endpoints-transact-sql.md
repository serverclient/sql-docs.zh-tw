---
description: sys.tcp_endpoints (Transact-SQL)
title: sys.tcp_endpoints (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.tcp_endpoints
- sys.tcp_endpoints_TSQL
- tcp_endpoints
- tcp_endpoints_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.tcp_endpoints catalog view
ms.assetid: 43cc3afa-cced-4463-8e97-fbfdaf2e4fa8
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: f5717389606bb3b73dc95c6a7eaedd6e03312cfd
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98094419"
---
# <a name="systcp_endpoints-transact-sql"></a>sys.tcp_endpoints (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  針對系統中每個 TCP 端點，各包含一個資料列。 **Sys.tcp_endpoints** 所描述的端點會提供物件來授與及撤銷連接許可權。 顯示的通訊埠及 IP 位址相關資訊不會用來設定通訊協定，而且可能與實際的通訊協定組態不相符。 若要檢視及設定通訊協定，請使用 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 組態管理員。  
  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**< 繼承的資料行>**||從 [sys. 端點](../../relational-databases/system-catalog-views/sys-endpoints-transact-sql.md)繼承資料行。|  
|**port**|int|端點正在接聽的通訊埠編號。 不可為 Null。|  
|**is_dynamic_port**|bit|1 = 通訊埠編號是以動態方式指派。<br /><br /> 不可為 Null。|  
|**ip_address**|**Nvarchar (45)**|由 LISTENER_IP 子句指定的接聽程式 IP 位址。 可為 Null。|  
  
## <a name="remarks"></a>備註  
 執行以下查詢來收集有關端點和連接的資訊。 沒有目前連接或沒有 TCP 連接的端點將以 NULL 值顯示。 加入 **WHERE** 子句 `WHERE des.session_id = @@SPID` ，以傳回目前連接的相關資訊。  
  
```  
SELECT des.login_name, des.host_name, program_name,  dec.net_transport, des.login_time,   
e.name AS endpoint_name, e.protocol_desc, e.state_desc, e.is_admin_endpoint,   
t.port, t.is_dynamic_port, dec.local_net_address, dec.local_tcp_port   
FROM sys.endpoints AS e  
LEFT JOIN sys.tcp_endpoints AS t  
   ON e.endpoint_id = t.endpoint_id  
LEFT JOIN sys.dm_exec_sessions AS des  
   ON e.endpoint_id = des.endpoint_id  
LEFT JOIN sys.dm_exec_connections AS dec  
   ON des.session_id = dec.session_id;  
```  
  
## <a name="permissions"></a>權限  
 [!INCLUDE[ssCatViewPerm](../../includes/sscatviewperm-md.md)] 如需相關資訊，請參閱 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)。  
  
## <a name="see-also"></a>另請參閱  
 [目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/catalog-views-transact-sql.md)   
 [端點目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/endpoints-catalog-views-transact-sql.md)  
  
  
