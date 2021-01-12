---
description: MSsubscriptions (Transact-SQL)
title: MSsubscriptions (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: replication
ms.topic: language-reference
f1_keywords:
- MSsubscriptions_TSQL
- MSsubscriptions
dev_langs:
- TSQL
helpviewer_keywords:
- MSsubscriptions system table
ms.assetid: b7e8301d-d115-41f6-8d4f-e0d25f453b25
author: cawrites
ms.author: chadam
ms.openlocfilehash: cee2d6298c2136a886cba29a89bc540230746092
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98098111"
---
# <a name="mssubscriptions-transact-sql"></a>MSsubscriptions (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  **MSsubscriptions** 資料表會針對本機散發者所服務之訂用帳戶中的每個已發行發行項，各包含一個資料列。 這份資料表儲存在散發資料庫中。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**publisher_database_id**|**int**|發行者資料庫的識別碼。|  
|**publisher_id**|**smallint**|發行者的識別碼。|  
|**publisher_db**|**sysname**|發行者資料庫的名稱。|  
|**publication_id**|**int**|發行集的識別碼。|  
|**article_id**|**int**|發行項的識別碼。|  
|**subscriber_id**|**smallint**|訂閱者的識別碼。|  
|**subscriber_db**|**sysname**|訂閱資料庫的名稱。|  
|**subscription_type**|**int**|訂閱的類型：<br /><br /> **0** = 推送。<br /><br /> **1** = 提取。<br /><br /> **2** = 匿名。|  
|**sync_type**|**tinyint**|同步處理的類型：<br /><br /> **1** = 自動。<br /><br /> **2** = 無同步處理。|  
|**status**|**tinyint**|訂閱的狀態：<br /><br /> **0** = 非使用中。<br /><br /> **1** = 已訂閱。<br /><br /> **2** = 使用中。|  
|**subscription_seqno**|**varbinary(16)**|快照集交易序號。|  
|**snapshot_seqno_flag**|**bit**|指出快照集交易序號的來源，其中 **1** 的值表示 **subscription_seqno** 是快照序號。|  
|**independent_agent**|**bit**|指出這個發行集是否有獨立的散發代理程式。|  
|**subscription_time**|**datetime**|僅供內部使用。|  
|**loopback_detection**|**bit**|適用於雙向異動複寫拓撲中的訂閱。 回送偵測會判斷散發代理程式是否將起源於訂閱者端的交易傳回給訂閱者：<br /><br /> **1** = 不會送回。<br /><br /> **0** = 傳回。<br /><br />|  
|**agent_id**|**int**|代理程式的識別碼。|  
|**update_mode**|**tinyint**|更新的類型。|  
|**publisher_seqno**|**varbinary(16)**|這項訂閱在發行者端的交易序號。|  
|**ss_cplt_seqno**|**varbinary(16)**|用來指定並行快照集處理完成的序號。|  
  
## <a name="see-also"></a>另請參閱  
 [複寫資料表 &#40;Transact-sql&#41;](../../relational-databases/system-tables/replication-tables-transact-sql.md)   
 [&#40;Transact-sql&#41;的複寫視圖 ](../../relational-databases/system-views/replication-views-transact-sql.md)   
 [sp_helpsubscription &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-helpsubscription-transact-sql.md)  
  
  
