---
description: MSsubscription_agents (Transact-SQL)
title: MSsubscription_agents (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: replication
ms.topic: language-reference
f1_keywords:
- MSsubscription_agents
- MSsubscription_agents_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- MSsubscription_agents system table
ms.assetid: 86ad5891-0bef-4963-9381-7d5b45245a0c
author: cawrites
ms.author: chadam
ms.openlocfilehash: e8569eba1925879f2f757953f36e4ae632292100
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98094711"
---
# <a name="mssubscription_agents-transact-sql"></a>MSsubscription_agents (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  **MSsubscription_agents** 資料表是由可更新訂閱的散發代理程式和觸發程式用來追蹤訂閱屬性。 這份資料表儲存在訂閱資料庫中。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**id**|**int**|資料列的識別碼。|  
|**publisher**|**sysname**|發行者的名稱。|  
|**publisher_db**|**sysname**|發行集資料庫的名稱。|  
|**出版**|**sysname**|發行集的名稱。|  
|**subscription_type**|**int**|訂閱類型：<br /><br /> 0 = 發送。<br /><br /> 1 = 提取<br /><br /> 2 = 提取匿名。|  
|**queue_id**|**sysname**|[!INCLUDE[msCoName](../../includes/msconame-md.md)]發行者端訊息佇列的識別碼。 針對以 SQL 為基礎的佇列更新， *queue_id* 設定為 **sql** 。|  
|**update_mode**|**tinyint**|更新的類型：<br /><br /> **0** = 唯讀。<br /><br /> **1** = 立即更新。<br /><br /> **2** = 使用訊息佇列的佇列更新。<br /><br /> **3** = 使用訊息佇列的容錯移轉，以佇列更新進行立即更新。<br /><br /> **4** = 使用 SQL Server 佇列的佇列更新。<br /><br /> **5** = 使用 SQL Server 佇列，以佇列更新容錯移轉進行立即更新。|  
|**failover_mode**|**bit**|如果選取了更新的容錯移轉類型，這就是所選的容錯移轉類型：<br /><br /> **0** = 正在使用立即更新。 不啟用容錯移轉。<br /><br /> **1** = 正在使用佇列更新。 啟用容錯移轉。 用於容錯移轉的佇列是在 *update_mode* 值中指定。|  
|**spid**|**int**|目前在執行或剛執行的散發代理程式所用之連接的系統處理序識別碼。|  
|**login_time**|**datetime**|目前在執行或剛執行的散發代理程式連接的日期和時間。|  
|**allow_subscription_copy**|**bit**|指定是否允許複製訂閱資料庫的能力。|  
|**attach_state**|**int**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|**attach_version**|**binary(16)**|代表附加訂閱版本的唯一識別碼。|  
|**last_sync_status**|**int**|目前在執行或剛執行的散發代理程式的最後執行狀態。 狀態可能是：<br /><br /> **1** = 已啟動。<br /><br /> **2** = 成功。<br /><br /> **3** = 進行中。<br /><br /> **4** = 閒置。<br /><br /> **5** = 重試。<br /><br /> **6** = 失敗。|  
|**last_sync_summary**|**sysname**|目前在執行或剛執行的散發代理程式的最後訊息。 狀態可能是：<br /><br /> **開始。**<br /><br /> **成功。**<br /><br /> **進行中。**<br /><br /> **閒置。**<br /><br /> **重試。**<br /><br /> **失敗。**|  
|**last_sync_time**|**datetime**|更新 *last_sync_summary* 和 *last_sync_status* 資料行的日期和時間。 作為 SQL Server Agent 服務作業來執行的提取或匿名散發代理程式不會更新這些資料行。 在這個狀況下，記錄資訊會記錄到作業記錄資料表中。|  
|**queue_server**|**sysname**|僅供內部使用。|  
  
## <a name="see-also"></a>另請參閱  
 [複寫資料表 &#40;Transact-sql&#41;](../../relational-databases/system-tables/replication-tables-transact-sql.md)   
 [&#40;Transact-sql&#41;的複寫視圖 ](../../relational-databases/system-views/replication-views-transact-sql.md)   
 [sp_helppullsubscription &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-helppullsubscription-transact-sql.md)  
  
  
