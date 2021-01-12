---
description: MSdistribution_agents (Transact-SQL)
title: MSdistribution_agents (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 10/28/2015
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: replication
ms.topic: language-reference
f1_keywords:
- MSdistribution_agents_TSQL
- MSdistribution_agents
dev_langs:
- TSQL
helpviewer_keywords:
- MSdistribution_agents system table
ms.assetid: 0e8f0653-1351-41d1-95d2-40f6d5a050ca
author: cawrites
ms.author: chadam
ms.openlocfilehash: 20b2d5f012c33ea12d4b60ed3a8d44ee475f3bf2
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98091469"
---
# <a name="msdistribution_agents-transact-sql"></a>MSdistribution_agents (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  **MSdistribution_agents** 資料表會針對在本機散發者上執行的每個散發代理程式各包含一個資料列。 這份資料表儲存在散發資料庫中。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**id**|**int**|散發代理程式的識別碼。|  
|**name**|**Nvarchar (100)**|散發代理程式的名稱。|  
|**publisher_database_id**|**int**|發行者資料庫的識別碼。|  
|**publisher_id**|**smallint**|發行者的識別碼。|  
|**publisher_db**|**sysname**|發行者資料庫的名稱。|  
|**出版**|**sysname**|發行集的名稱。|  
|**subscriber_id**|**smallint**|訂閱者的識別碼，只供眾所周知的代理程式使用。 如果是匿名代理程式，便會保留這個資料行。|  
|**subscriber_db**|**sysname**|訂閱資料庫的名稱。|  
|**subscription_type**|**int**|訂閱的類型：<br /><br /> **0** = 推送。<br /><br /> **1** = 提取。<br /><br /> **2** = 匿名。|  
|**local_job**|**bit**|指出本機散發者是否有 SQL Server Agent 作業。|  
|**job_id**|**binary(16)**|作業識別碼。|  
|**subscription_guid**|**binary(16)**|這個代理程式的訂閱識別碼。|  
|**profile_id**|**int**|[MSagent_profiles &#40;transact-sql&#41;](../../relational-databases/system-tables/msagent-profiles-transact-sql.md)資料表中的設定識別碼。|  
|**anonymous_subid**|**uniqueidentifier**|匿名代理程式的識別碼。|  
|**subscriber_name**|**sysname**|訂閱者的名稱，只供匿名代理程式使用。|  
|**virtual_agent_id**|**int**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|**anonymous_agent_id**|**int**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|**creation_date**|**datetime**|散發或合併代理程式的建立日期時間。|  
|**queue_id**|**sysname**|用來尋找佇列更新訂閱之佇列的識別碼。 如果是非佇列訂閱，這個值就是 NULL。 如果是以 [!INCLUDE[msCoName](../../includes/msconame-md.md)] Message Queuing 為基礎的訂閱，這個值是一個 GUID，用來唯一識別訂閱要用的佇列。 如果是以 SQL Server 為基礎的佇列發行集，資料行就會包含值 **SQL**。<br /><br /> 注意：使用 [!INCLUDE[msCoName](../../includes/msconame-md.md)] 訊息佇列已被取代，不再支援。|  
|**queue_status**|**int**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|**offload_enabled**|**bit**|指出是否能從遠端啟動代理程式。<br /><br /> **0** 指定不能從遠端啟動代理程式。<br /><br /> **1** 指定將從遠端啟動代理程式，並在 *offload_server* 屬性指定的遠端電腦上啟用。|  
|**offload_server**|**sysname**|將用來啟用遠端代理程式之伺服器的網路名稱。|  
|**dts_package_name**|**sysname**|DTS 封裝的名稱。 例如，如果是名為 **DTSPub_Package** 的封裝，請指定 `@dts_package_name = N'DTSPub_Package'` 。|  
|**dts_package_password**|**Nvarchar (524)**|封裝的密碼。|  
|**dts_package_location**|**int**|封裝位置。 封裝的位置 **可以是散發****者或訂閱者**。|  
|**希**|**varbinary(85)**|散發代理程式或合併代理程式在第一次執行期間的安全性識別碼 (SID)。|  
|**queue_server**|**sysname**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|**subscriber_security_mode**|**smallint**|當連接到訂閱者時，代理程式所用的安全性模式，它可以是下列項目之一：<br /><br /> **0**  =  [!INCLUDE[msCoName](../../includes/msconame-md.md)] SQL Server 驗證<br /><br /> **1** 個  =  [!INCLUDE[msCoName](../../includes/msconame-md.md)] Windows 驗證。|  
|**subscriber_login**|**sysname**|連接到訂閱者時所用的登入。|  
|**subscriber_password**|**Nvarchar (524)**|這是連接到訂閱者時，所用之密碼的加密值。|  
|**reset_partial_snapshot_progress**|**bit**|這是指是否將捨棄部份下載的快照集，以便重新啟動整個快照集處理序。|  
|**job_step_uid**|**uniqueidentifier**|用來啟動代理程式之 SQL Server Agent 作業步驟的唯一識別碼。|  
|**subscriptionstreams**|**tinyint**|設定每個散發代理程式將數批變更並行套用在訂閱者時所能使用的連接數目。 支援的值範圍是 1-64。|  
|**memory_optimized**|**bit**|1表示訂閱者可用於記憶體優化的資料表。|  
|**job_login**|**sysname**||  
|**job_password**|**Nvarchar (524)**||  
  
## <a name="see-also"></a>另請參閱  
 [複寫資料表 &#40;Transact-SQL&#41;](../../relational-databases/system-tables/replication-tables-transact-sql.md)  
  
  
