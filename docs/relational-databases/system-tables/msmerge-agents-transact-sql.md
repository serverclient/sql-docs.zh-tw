---
description: MSmerge_agents (Transact-SQL)
title: MSmerge_agents (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: replication
ms.topic: language-reference
f1_keywords:
- MSmerge_agents
- MSmerge_agents_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- MSmerge_agents system table
ms.assetid: 639d2ebb-2c37-4fe0-b14b-1637bc5fc221
author: cawrites
ms.author: chadam
ms.openlocfilehash: 9380cd5c5e04293e0296c098ee7455eb803e3972
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98096205"
---
# <a name="msmerge_agents-transact-sql"></a>MSmerge_agents (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  **MSmerge_agents** 資料表會針對在訂閱者端執行的每個合併代理程式，各包含一個資料列。 這份資料表儲存在散發資料庫中。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**id**|**int**|合併代理程式的識別碼。|  
|**name**|**Nvarchar (100)**|合併代理程式的名稱。|  
|**publisher_id**|**smallint**|發行者的識別碼。|  
|**publisher_db**|**sysname**|發行者資料庫的名稱。|  
|**出版**|**sysname**|發行集的名稱。|  
|**subscriber_id**|**smallint**|訂閱者的識別碼。|  
|**subscriber_db**|**sysname**|訂閱資料庫的名稱。|  
|**local_job**|**bit**|指出本機散發者是否有 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent 作業。|  
|**job_id**|**binary(16)**|作業識別碼。|  
|**profile_id**|**int**|**MSagent_profiles** 資料表中的設定識別碼。|  
|**anonymous_subid**|**uniqueidentifier**|匿名代理程式的識別碼。|  
|**subscriber_name**|**sysname**|訂閱者的名稱。|  
|**creation_date**|**datetime**|建立散發或合併代理程式的日期和時間。|  
|**offload_enabled**|**bit**|指定是否能從遠端啟動代理程式。<br /><br /> **0** 指定不能從遠端啟動代理程式。<br /><br /> **1** 指定將從遠端啟動代理程式，並在 offload_server 屬性指定的遠端電腦上啟用。|  
|**offload_server**|**sysname**|指定將用來啟用遠端代理程式之伺服器的網路名稱。|  
|**希**|**varbinary(85)**|散發代理程式或合併代理程式在第一次執行期間的安全性識別碼 (SID)。|  
|**subscriber_security_mode**|**smallint**|當連接到訂閱者時，代理程式所用的安全性模式，它可以是下列項目之一：<br /><br /> **0**  =  [!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 驗證。<br /><br /> **1** 個  =  [!INCLUDE[msCoName](../../includes/msconame-md.md)] Windows 驗證。|  
|**subscriber_login**|**sysname**|連接到訂閱者時所用的登入。|  
|**subscriber_password**|**Nvarchar (524)**|連接到訂閱者時，所用之密碼的加密值。|  
|**publisher_security_mode**|**smallint**|連接到發行者時，代理程式所用的安全性模式，它可以是下列項目之一：<br /><br /> **0**  =  [!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 驗證。<br /><br /> **1** 個  =  [!INCLUDE[msCoName](../../includes/msconame-md.md)] Windows 驗證。|  
|**publisher_login**|**sysname**|連接到發行者時所用的登入。|  
|**publisher_password**|**Nvarchar (524)**|連接到發行者時，所用之密碼的加密值。|  
|**job_step_uid**|**uniqueidentifier**|用來啟動代理程式之 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent 作業步驟的唯一識別碼。|  
  
## <a name="see-also"></a>另請參閱  
 [複寫資料表 &#40;Transact-sql&#41;](../../relational-databases/system-tables/replication-tables-transact-sql.md)   
 [複寫檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-views/replication-views-transact-sql.md)  
  
  
