---
description: MSmerge_genhistory (Transact-SQL)
title: MSmerge_genhistory (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/04/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: replication
ms.topic: language-reference
f1_keywords:
- MSmerge_genhistory_TSQL
- MSmerge_genhistory
dev_langs:
- TSQL
helpviewer_keywords:
- MSmerge_genhistory system table
ms.assetid: 475d08ae-eb8b-49de-afd6-33c96ab8004d
author: cawrites
ms.author: chadam
ms.openlocfilehash: 7ccd9f9b6cc2780681f1f76c0764aaf423c2af36
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98100559"
---
# <a name="msmerge_genhistory-transact-sql"></a>MSmerge_genhistory (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  **MSmerge_genhistory** 資料表會針對訂閱者知道的每個世代，各包含一個資料列) 保留期限內的 (。 其目的是防止在交換時傳送共用層代 (Generation)，並且重新同步處理從備份還原的訂閱者。 這份資料表儲存在發行集和訂閱資料庫中。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**guidsrc**|**uniqueidentifier**|由訂閱者的層代 (Generation) 所識別之變更的全域識別碼。|  
|**pubid**|**uniqueidentifier**|發行集識別碼。|  
|**生成**|**bigint**|層代 (Generation) 值。|  
|**art_nick**|**int**|發行項的暱稱。|  
|**nicknames**|**Varbinary (1001)**|這個層代 (Generation) 已經知道的其他訂閱者的暱稱清單。 其目的是避免將層代 (Generation) 傳送給已經看過那些變更的訂閱者。 為了提高搜尋的效率，暱稱清單中的暱稱都是依序維護的。 如果暱稱太多，超過這個欄位的容量，它們就無法達到最佳化的效果。|  
|**coldate**|**datetime**|將目前層代 (Generation) 加入資料表中的日期。|  
|**genstatus**|**tinyint**|層代 (Generation) 狀態如下：<br /><br /> **0** = 開啟。<br /><br /> **1** = 已關閉。<br /><br /> **2** = 已關閉，並源自另一個訂閱者。|  
|**changecount**|**int**|在給定層代 (Generation) 反映的變更數目|  
  
## <a name="see-also"></a>另請參閱  
 [複寫資料表 &#40;Transact-sql&#41;](../../relational-databases/system-tables/replication-tables-transact-sql.md)   
 [複寫檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-views/replication-views-transact-sql.md)  
  
  
