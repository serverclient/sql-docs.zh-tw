---
description: MSreplication_monitordata (Transact-SQL)
title: MSreplication_monitordata (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/06/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: replication
ms.topic: language-reference
f1_keywords:
- MSreplication_monitordata_TSQL
- MSreplication_monitordata
dev_langs:
- TSQL
helpviewer_keywords:
- MSreplication_monitordata system table
ms.assetid: 843d3ffd-a1ef-4fd5-a744-c2252199793e
author: cawrites
ms.author: chadam
ms.openlocfilehash: bbe408296895f593de0af1da852b807082e8e7cf
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98098158"
---
# <a name="msreplication_monitordata-transact-sql"></a>MSreplication_monitordata (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  **MSreplication_monitordata** 資料表包含複寫監視器所用的快取資料，每個受監視的訂閱都有一個資料列。 這份資料表儲存在散發資料庫中。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**lastrefresh**|**datetime**|重新整理監視資料的日期和時間。|  
|**computetime**|**int**|這是計算監視資料所花的時間 (以秒為單位)。|  
|**publication_id**|**int**|發行集識別碼。|  
|**publisher**|**sysname**|發行者的名稱。|  
|**publisher_srvid**|**int**|發行者的伺服器識別碼。|  
|**publisher_db**|**sysname**|發行集資料庫的名稱。|  
|**出版**|**sysname**|發行集的名稱。|  
|**publication_type**|**int**|發行集的類型，它可以是下列值之一：<br /><br /> **0** = 交易式發行集<br /><br /> **1** = 快照式發行集<br /><br /> **2** = 合併式發行集|  
|**agent_type**|**int**|複寫代理程式的類型，它可以是下列值之一：<br /><br /> **1** = 快照集代理程式<br /><br /> **2** = 記錄讀取器代理程式<br /><br /> **3** = 散發代理程式<br /><br /> **4** = 合併代理程式<br /><br /> **9** = 佇列讀取器代理程式|  
|**agent_id**|**int**|複寫代理程式的識別碼。|  
|**agent_name**|**sysname**|複寫代理程式作業的名稱。|  
|**job_id**|**uniqueidentifier**|複寫代理程式作業的 GUID。|  
|**status**|**int**|複寫代理程式的狀態，它可以是下列其中一個值：<br /><br /> **1** = 已啟動<br /><br /> **2** = 成功<br /><br /> **3** = 進行中<br /><br /> **4** = 閒置<br /><br /> **5** = 重試<br /><br /> **6** = 失敗|  
|**isagentrunningnow**|**bit**|旗標，指出代理程式作業目前是否正在執行， **1** 值表示作業正在執行中。|  
|**warning**|**int**|訂閱所產生的臨界值警告，它可能是一或多個這些值的邏輯 OR 結果。<br /><br /> **1** = 到期-交易式發行集的訂閱已超出保留期限超過允許的臨界值，以保留期限的百分比表示。<br /><br /> **2** = 延遲-將交易式發行者的資料複寫到訂閱者所花費的時間超過閾值（以秒為單位）。<br /><br /> **4** = mergeexpiration-合併式發行集的訂閱已超出保留期限超過允許的臨界值，以保留期限的百分比表示。 8 = mergefastrunduration - 利用快速網路連接來完成合併訂閱的同步處理所花的時間超出臨界值 (以秒為單位)。<br /><br /> **16** = mergeslowrunduration 利用-完成合併訂閱的同步處理所花費的時間超過閾值（以秒為單位），而不是透過慢速或撥號網路連接。<br /><br /> **32** = mergefastrunspeed 利用-在同步處理合併訂閱期間，資料列的傳遞速率無法維持閾值速率（以每秒資料列數為單位），透過快速網路連接。<br /><br /> **64** = mergeslowrunspeed-在同步處理合併訂閱期間，資料列的傳遞速率無法維持閾值速率（以每秒資料列數為單位），透過慢速或撥號網路連接。|  
|**last_distsync**|**datetime**|上次執行散發代理程式的日期和時間。|  
|**agentstoptime**|**datetime**|停止代理程式的日期和時間。|  
|**distdb**|**sysname**|訂閱的散發資料庫名稱。|  
|**保留**|**int**|發行集的保留期限。|  
|**time_stamp**|**datetime**|僅供內部使用。|  
|**worst_latency**|**int**|交易式發行集的記錄讀取器或散發代理程式所傳播之資料變更的最高延遲 (以秒為單位)。|  
|**best_latency**|**int**|交易式發行集的記錄讀取器或散發代理程式所傳播之資料變更的最低延遲 (以秒為單位)。|  
|**avg_latency**|**int**|交易式發行集的記錄讀取器或散發代理程式所傳播之資料變更的平均延遲 (以秒為單位)。|  
|**cur_latency**|**int**|在目前的執行期間，記錄讀取器或散發代理程式所傳播之資料變更的延遲 (以秒為單位)。|  
|**worst_runspeedPerf**|**int**|合併式發行集的最長同步處理時間|  
|**best_runspeedPerf**|**int**|合併式發行集的最短同步處理時間|  
|**average_runspeedPerf**|**int**|合併式發行集的平均同步處理時間|  
|**mergePerformance**|**int**|以上一次同步處理的傳遞速率除以先前所有傳遞速率的平均值為基礎，上一次同步處理相對於訂閱之所有同步處理的效能。|  
|**mergelatestsessionrunduration**|**int**|最近合併代理程式執行的持續時間。|  
|**mergelatestsessionrunspeed**|**float(53)**|最近合併代理程式執行的傳遞速率。|  
|**mergelatestsessionconnectiontype**|**int**|最近的合併代理程式工作階段所用的連接，它可以是下列其中一個值：<br /><br /> **1** = 區域網路 (LAN) <br /><br /> **2** = 撥號網路連接|  
|**retention_period_unit**|**tinyint**|定義用來定義保留的單位，它可以是下列值之一：<br /><br /> **1** = 週<br /><br /> **2** = 月<br /><br /> **3** = 年|  
  
## <a name="see-also"></a>另請參閱  
 [以程式設計方式監視複寫](../../relational-databases/replication/monitor/programmatically-monitor-replication.md)   
 [複寫資料表 &#40;Transact-sql&#41;](../../relational-databases/system-tables/replication-tables-transact-sql.md)   
 [&#40;Transact-sql&#41;的複寫視圖 ](../../relational-databases/system-views/replication-views-transact-sql.md)   
 [sp_replmonitorhelpsubscription &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-replmonitorhelpsubscription-transact-sql.md)   
 [sp_replmonitorhelppublication &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-replmonitorhelppublication-transact-sql.md)   
 [sp_replmonitorhelppublisher &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-replmonitorhelppublisher-transact-sql.md)   
 [sp_replmonitorhelpmergesession &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-replmonitorhelpmergesession-transact-sql.md)   
 [sp_replmonitorhelppublicationthresholds &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-replmonitorhelppublicationthresholds-transact-sql.md)   
 [sp_replmonitorhelpmergesessiondetail &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-replmonitorhelpmergesessiondetail-transact-sql.md)  
  
  
