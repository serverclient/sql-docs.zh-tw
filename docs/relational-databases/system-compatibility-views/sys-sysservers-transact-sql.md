---
description: sys.sysservers (Transact-SQL)
title: sys.sysserver (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.sysservers
- sysservers_TSQL
- sysservers
- sys.sysservers_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sysservers system table
- sys.sysservers compatibility view
ms.assetid: d02f186f-c00f-44a6-b38d-dc78a3d2145b
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 1cf2a9649745eeeb5fbf10667b9849c0c1de5dec
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98095323"
---
# <a name="syssysservers-transact-sql"></a>sys.sysservers (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  針對 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體能夠將它當做 OLE DB 資料來源進行存取的每部伺服器，各包含一個資料列。  
  
> [!IMPORTANT]  
>  [!INCLUDE[ssnoteCompView](../../includes/ssnotecompview-md.md)]  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**srvid**|**smallint**|遠端伺服器的識別碼 (只適用於本機環境)。|  
|**srvstatus**|**smallint**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|**srvname**|**sysname**|伺服器的名稱。|  
|**srvproduct**|**sysname**|遠端伺服器的產品名稱。|  
|**providername**|**nvarchar(128)**|存取這部伺服器的 OLE DB 提供者名稱。|  
|**資料來源**|**nvarchar(4000)**|OLE DB 資料來源值。|  
|**location**|**nvarchar(4000)**|OLE DB 位置值。|  
|**providerstring**|**nvarchar(4000)**|OLE DB 提供者字串值。|  
|**schemadate**|**datetime**|上次更新這個資料列的日期。|  
|**topologyx**|**int**|未使用。|  
|**topologyy**|**int**|未使用。|  
|**catalog**|**sysname**|當建立通往 OLE DB 提供者的連接時所用的目錄。|  
|**srvcollation**|**sysname**|伺服器的定序。|  
|**connecttimeout**|**int**|伺服器連接的逾時值設定。|  
|**querytimeout**|**int**|針對伺服器進行查詢的逾時值設定。|  
|**srvnetname**|**char(30)**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|**isremote**|**bit**|1 = 伺服器是遠端伺服器。<br /><br /> 0 = 伺服器是連結伺服器。|  
|**Rpc**|**bit**|1 = **sp_serveroption \@ rpc** 設定為 **true** 或 **on**。<br /><br /> 0 = **sp_serveroption \@ rpc** 設為 **false** 或 **off**。|  
|**pub**|**bit**|1 = **sp_serveroption \@ pub** 設為 **true** 或 **on**。<br /><br /> 0 = **sp_serveroption \@ pub** 設為 **false** 或 **off**。|  
|**sub**|**bit**|1 = **sp_serveroption \@ 子** 設定為 **true** 或 **on**。<br /><br /> 0 = **sp_serveroption \@ 子** 設定為 **false** 或 **off**。|  
|**dist**|**bit**|1 = **sp_serveroption \@ dist** 設定為 **true** 或 **on**。<br /><br /> 0 = **sp_serveroption \@ dist** 設定為 **false** 或 **off**。|  
|**dpub**|**bit**|1 = **sp_serveroption \@ dpub** 設為 **true** 或 **on**。<br /><br /> 0 = **sp_serveroption \@ dpub** 設為 **false** 或 **off**。|  
|**rpcout**|**bit**|1 = **sp_serveroption \@ rpc out** 設為 **true** 或 **on**。<br /><br /> 0 = **sp_serveroption \@ rpc out** 設為 **false** 或 **off**。|  
|**dataaccess**|**bit**|1 = **sp_serveroption \@ 資料存取** 設定為 **true** 或 **on**。<br /><br /> 0 = **sp_serveroption \@ 資料存取** 設為 **false** 或 **off**。|  
|**collationcompatible**|**bit**|1 = **sp_serveroption 定 \@ 序相容性** 設定為 **true** 或 **on**。<br /><br /> 0 = **sp_serveroption 定 \@ 序相容性** 設定為 **false** 或 **off**。|  
|**系統**|**bit**|1 = **sp_serveroption \@ 系統** 設定為 **true** 或 **on**。<br /><br /> 0 = **sp_serveroption \@ 系統** 設定為 **false** 或 **off**。|  
|**useremotecollation**|**bit**|1 = **sp_serveroption \@ 遠端定序** 設定為 **true** 或 **on**。<br /><br /> 0 = **sp_serveroption \@ 遠端定序** 設定為 **false** 或 **off**。|  
|**lazyschemavalidation**|**bit**|1 = **sp_serveroption \@ lazy schema 驗證** 設為 **true** 或 **on**。<br /><br /> 0 = **sp_serveroption \@ lazy schema 驗證** 設為 **false** 或 **off**。|  
|**整理**|**sysname**|依 **sp_serveroption 定 \@ 序名稱** 所設定的伺服器定序。|  
|**nonsqlsub**|bit|0 = 伺服器是 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的執行個體<br /><br /> 1 = 伺服器不是 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的執行個體|  
  
## <a name="see-also"></a>另請參閱  
 [&#40;Transact-sql&#41;將系統資料表對應至系統檢視 ](../../relational-databases/system-tables/mapping-system-tables-to-system-views-transact-sql.md)   
 [相容性檢視 &#40;Transact-SQL&#41;](~/relational-databases/system-compatibility-views/system-compatibility-views-transact-sql.md)  
  
  
