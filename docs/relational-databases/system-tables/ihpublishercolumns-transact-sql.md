---
description: IHpublishercolumns (Transact-SQL)
title: IHpublishercolumns (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/03/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: replication
ms.topic: language-reference
f1_keywords:
- IHpublishercolumns
- IHpublishercolumns_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- IHpublishercolumns system table
ms.assetid: a5347750-224c-40d9-ae12-57e7213b7db9
author: cawrites
ms.author: chadam
ms.openlocfilehash: 44704435eca9e065e7065f025b4c99accab7fa7f
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98092468"
---
# <a name="ihpublishercolumns-transact-sql"></a>IHpublishercolumns (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  **IHpublishercolumns** 系統資料表代表儲存在發行者端的中繼資料。 這份資料表會針對使用目前散發者，從非 SQL Server 發行者複寫的每個資料行，各包含一個資料列。 **IHpublishercolumns** 中的資料類型資訊是發行資料 (DBMS) 的非 SQL Server 資料庫管理系統所特有。 這份資料表儲存在散發資料庫中。  
  
## <a name="definition"></a>定義  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**publishercolumn_id**|**int**|識別已發行的資料行。|  
|**table_id**|**int**|識別資料行所屬 [IHpublishertables](../../relational-databases/system-tables/ihpublishertables-transact-sql.md) 中的來源資料表。|  
|**publisher_id**|**smallint**|識別發行資料行所在位置的非 SQL Server 發行者。|  
|**name**|**sysname**|已發行的資料行名稱。|  
|**column_ordinal**|**int**|依序識別欄位。|  
|**type**|**varchar(255)**|發行者之來源資料行的資料行資料類型。|  
|**length** (長度)|**bigint**|發行者的來源資料行長度。|  
|**prec**|**int**|發行者來源資料行的有效位數。|  
|**scale**|**int**|發行者來源資料行的小數位數。|  
|**isnullable**|**bit**|指出資料行是否接受 Null 值，其中 **1** 表示接受 null 值。|  
|**iscaptured**|**bit**|指出資料行是否有觸發程序存在 (即使發行項中沒有發行該資料行，也可能存在)。 值為 **1** 表示觸發程式存在於資料行上。|  
  
## <a name="see-also"></a>另請參閱  
 [異質資料庫複寫](../../relational-databases/replication/non-sql/heterogeneous-database-replication.md)   
 [複寫資料表 &#40;Transact-sql&#41;](../../relational-databases/system-tables/replication-tables-transact-sql.md)   
 [&#40;Transact-sql&#41;的複寫視圖 ](../../relational-databases/system-views/replication-views-transact-sql.md)   
 [sysarticlecolumns &#40;System View&#41; &#40;Transact-sql&#41;](../../relational-databases/system-views/sysarticlecolumns-system-view-transact-sql.md)   
 [sysarticlecolumns &#40;Transact-sql&#41;](../../relational-databases/system-tables/sysarticlecolumns-transact-sql.md)  
  
  
