---
description: MSmerge_partition_groups (Transact-SQL)
title: MSmerge_partition_groups (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/06/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: replication
ms.topic: language-reference
f1_keywords:
- MSmerge_partition_groups_TSQL
- MSmerge_partition_groups
dev_langs:
- TSQL
helpviewer_keywords:
- MSmerge_partition_groups system table
ms.assetid: 5d56d780-ee40-4afc-9c2a-d1723d86e430
author: cawrites
ms.author: chadam
ms.openlocfilehash: 823e2a57cd6292be59fe9dca7bb805b8eaa47d49
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98098641"
---
# <a name="msmerge_partition_groups-transact-sql"></a>MSmerge_partition_groups (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  **MSmerge_partition_groups** 資料表會針對給定資料庫中的每個預先計算資料分割，各儲存一個資料列。 除了列出的資料行之外，還會加入一個資料行，代表參數化資料列篩選器所用的每個函數。 例如，如果篩選使用 [HOST_NAME](../../t-sql/functions/host-name-transact-sql.md)函數，則會將名為 **HOST_NAME_FN** 的資料行加入至資料表。 與這個發行者同步處理的每一組唯一函數值，都會各儲存一個資料列代表它。 只要有兩個或兩個以上訂閱者的這些函數完全同值，就會共用這份資料表中的相同資料列，因此都共用相同的資料分割識別碼。這份資料表儲存在發行集資料庫中。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**partition_id**|**int**|識別提供預先計算的資料分割之唯一識別碼的資料行。|  
|**publication_number**|**smallint**|儲存在 **sysmergepublications** 中的發行集編號。|  
|**maxgen_whenadded**|**bigint**|在將資料列插入這份資料表時，發行者端已知的最高層代 (Generation)。|  
|**using_partition_groups**|**bit**|指出資料分割是否屬於使用預先計算的資料分割之發行集，它可以是下列值之一：<br /><br /> **0** = 發行集不使用預先計算的資料分割。<br /><br /> **1** = 發行集使用預先計算的資料分割<br /><br /> 如需詳細資訊，請參閱[使用預先計算的資料分割最佳化參數化篩選效能](../../relational-databases/replication/merge/parameterized-filters-optimize-for-precomputed-partitions.md)。|  
|**HOST_NAME_FN**|**nvarchar(128)**|在使用參數化資料列篩選器來產生資料分割時所提供的值。 如需詳細資訊，請參閱＜ [參數化資料列篩選器](../../relational-databases/replication/merge/parameterized-filters-parameterized-row-filters.md)＞。|  
  
## <a name="see-also"></a>另請參閱  
 [複寫資料表 &#40;Transact-sql&#41;](../../relational-databases/system-tables/replication-tables-transact-sql.md)   
 [複寫檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-views/replication-views-transact-sql.md)  
  
  
