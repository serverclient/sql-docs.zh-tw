---
description: IHpublisherindexes (Transact-SQL)
title: IHpublisherindexes (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/06/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: replication
ms.topic: language-reference
f1_keywords:
- IHpublisherindexes
- IHpublisherindexes_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- IHpublisherindexes system table
ms.assetid: 6008ef89-eeb9-46dc-93a2-f7623298cf0f
author: cawrites
ms.author: chadam
ms.openlocfilehash: 03915f08ecf8aeb222f45e86af3988c64cab575b
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98092440"
---
# <a name="ihpublisherindexes-transact-sql"></a>IHpublisherindexes (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  **IHpublisherindexes** 系統資料表會針對使用目前散發者，從非 SQL Server 發行者所複寫的每個索引，各包含一個資料列。 這份資料表儲存在散發資料庫中。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**publisherindex_id**|**int**|識別已發行的索引。|  
|**table_id**|**int**|識別索引所屬 [IHpublishertables](../../relational-databases/system-tables/ihpublishertables-transact-sql.md) 中的資料表。|  
|**publisher_id**|**smallint**|識別發行索引所在位置的非 SQL Server 發行者。|  
|**name**|**sysname**|已發行之索引的名稱。|  
|**type**|**nvarchar(255)**|來自 [IHindextypes](../../relational-databases/system-tables/ihindextypes-transact-sql.md) 系統資料表的支援索引類型。|  
  
## <a name="see-also"></a>另請參閱  
 [異質資料庫複寫](../../relational-databases/replication/non-sql/heterogeneous-database-replication.md)   
 [複寫資料表 &#40;Transact-sql&#41;](../../relational-databases/system-tables/replication-tables-transact-sql.md)   
 [複寫檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-views/replication-views-transact-sql.md)  
  
  
