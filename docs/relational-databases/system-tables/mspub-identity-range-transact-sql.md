---
description: MSpub_identity_range (Transact-SQL)
title: MSpub_identity_range (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/03/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: replication
ms.topic: language-reference
f1_keywords:
- MSpub_identity_range_TSQL
- MSpub_identity_range
dev_langs:
- TSQL
helpviewer_keywords:
- MSpub_identity_range system table
ms.assetid: 68746eef-32e1-42bc-aff0-9798cd0e88b8
author: cawrites
ms.author: chadam
ms.openlocfilehash: 526620d1fe5ed7c00eed23b4433f3f7630205153
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98098539"
---
# <a name="mspub_identity_range-transact-sql"></a>MSpub_identity_range (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  **MSpub_identity_range** 資料表提供識別範圍管理支援。 這份資料表儲存在發行集和訂閱資料庫中。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**objid**|**int**|具有複寫所管理的識別欄位之資料表的識別碼。|  
|**range**|**bigint**|控制將在調整時，在訂閱進行指派的連續識別值的範圍大小。|  
|**pub_range**|**bigint**|控制將在調整時，在發行集進行指派的連續識別值的範圍大小。|  
|**current_pub_range**|**bigint**|發行集所用的目前範圍。 如果在 **sp_changearticle** 變更之後，以及在下一個範圍調整之前查看，它可能會不同于 *pub_range* 。|  
|**threshold**|**int**|用來控制散發代理程式指派新識別範圍之時機的百分比值。 使用 [ *臨界* 值] 中指定的值百分比時，散發代理程式會建立新的識別範圍。|  
|**last_seed**|**bigint**|目前範圍的下限。|  
  
## <a name="see-also"></a>另請參閱  
 [複寫資料表 &#40;Transact-sql&#41;](../../relational-databases/system-tables/replication-tables-transact-sql.md)   
 [複寫檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-views/replication-views-transact-sql.md)  
  
  
