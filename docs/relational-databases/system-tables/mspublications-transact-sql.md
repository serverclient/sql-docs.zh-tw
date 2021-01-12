---
description: MSpublications (Transact-SQL)
title: MSpublications (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: replication
ms.topic: language-reference
f1_keywords:
- MSpublications
- MSpublications_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- MSpublications system table
ms.assetid: 7a0b3457-7265-4f24-a255-7f055d908f20
author: cawrites
ms.author: chadam
ms.openlocfilehash: 089270c4c95b35e25f44339a7f001b5ac194ec01
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98098170"
---
# <a name="mspublications-transact-sql"></a>MSpublications (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  **MSpublications** 資料表會針對發行者所複寫的每個發行集，各包含一個資料列。 這份資料表儲存在散發資料庫中。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**publisher_id**|**smallint**|發行者的識別碼。|  
|**publisher_db**|**sysname**|發行者資料庫的名稱。|  
|**出版**|**sysname**|發行集的名稱。|  
|**publication_id**|**int**|發行集的識別碼。|  
|**publication_type**|**int**|發行集類型：<br /><br /> **0** = 交易式。<br /><br /> **1** = 快照集。<br /><br /> **2** = 合併。|  
|**thirdparty_flag**|**bit**|指出發行集是否為 [!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 資料庫：<br /><br /> **0**  =  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。<br /><br /> **1** = 以外的資料來源 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。|  
|**independent_agent**|**bit**|指出這個發行集是否有獨立的散發代理程式。|  
|**immediate_sync**|**bit**|指出每次執行快照集代理程式時，是否要建立或重新建立同步處理檔案。|  
|**allow_push**|**bit**|指出是否能夠建立給定發行集的發送訂閱。|  
|**allow_pull**|**bit**|指出是否能夠建立給定發行集的提取訂閱。|  
|**allow_anonymous**|**bit**|指出是否能夠建立給定發行集的匿名訂閱。|  
|**description**|**nvarchar(255)**|發行集的描述。|  
|**vendor_name**|**Nvarchar (100)**|如果發行者不是 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 資料庫，這便是供應商的名稱。|  
|**保留**|**int**|發行集的保留期限 (以小時為單位)。|  
|**sync_method**|**int**|同步處理方法：<br /><br /> **0** = 原生 (會產生所有資料表) 的原生模式大量複製輸出。<br /><br /> **1** = 字元 (產生所有資料表) 的字元模式大量複製輸出。<br /><br /> **3** = 並行 (會產生所有資料表的原生模式大量複製輸出，但在快照集) 期間不會鎖定資料表。<br /><br /> **4** = Concurrent_c (產生所有資料表的字元模式大量複製輸出，但在快照集期間，不會鎖定資料表) <br /><br /> **3** 和 **4** 值適用于異動複寫和合併式複寫，但不適用於快照式複寫。|  
|**allow_subscription_copy**|**bit**|啟用或停用複製訂閱這個發行集之訂閱資料庫的能力。 **0** 表示已停用複製， **1** 表示已啟用。|  
|**thirdparty_options**|**int**|指定是否隱藏中複寫資料夾中的發行集顯示 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] ：<br /><br /> **0** = 在的 Replication 資料夾中顯示異類發行集 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 。<br /><br /> **1** = 隱藏在的 [複寫] 資料夾中顯示異類發行集 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 。|  
|**allow_queued_tran**|**bit**|指定發行集是否允許佇列更新：<br /><br /> **0 =** 發行集未排入佇列。<br /><br /> **1** = 發行集已排入佇列。|  
|**options**|**int**|這個版本沒有可用的資訊。|  
  
## <a name="see-also"></a>另請參閱  
 [複寫資料表 &#40;Transact-sql&#41;](../../relational-databases/system-tables/replication-tables-transact-sql.md)   
 [複寫檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-views/replication-views-transact-sql.md)  
  
  
