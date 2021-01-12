---
description: IHcolumns (Transact-SQL)
title: IHcolumns (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/06/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: replication
ms.topic: language-reference
f1_keywords:
- IHcolumns_TSQL
- IHcolumns
dev_langs:
- TSQL
helpviewer_keywords:
- IHcolumns system table
ms.assetid: 5bb027e5-5279-487b-9c33-5f402987253c
author: cawrites
ms.author: chadam
ms.openlocfilehash: e9d264dd2c3199527a763f5c2c8afce01dad17e3
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98094816"
---
# <a name="ihcolumns-transact-sql"></a>IHcolumns (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  **IHcolumns** 系統資料表會針對每個已發行的資料行，各包含一個資料列。 這份資料表用來定義 SQL Server 發行者中的資料行資料類型在發行時如何表示，基本上它是在非 SQL Server 資料庫管理系統 (DBMS) 和 SQL Server 之間對應資料類型。 這份資料表儲存在散發資料庫中。  
  
## <a name="definition"></a>定義  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**column_id**|**int**|識別已發行的資料行。|  
|**publishercolumn_id**|**int**|將已發行的資料行與儲存在 [IHpublishercolumns](../../relational-databases/system-tables/ihpublishercolumns-transact-sql.md) 系統資料表中的資料行中繼資料產生關聯。|  
|**name**|**sysname**|指定資料行名稱。|  
|**article_id**|**int**|識別欄位所屬的發行項。|  
|**column_ordinal**|**int**|依序識別欄位。|  
|**mapped_type**|**tinyint**|訂閱者之目的地資料行的資料行資料類型。|  
|**mapped_length**|**bigint**|訂閱者的資料行長度。|  
|**mapped_prec**|**int**|訂閱者的資料行有效位數。|  
|**mapped_scale**|**int**|訂閱者的資料行小數位數。|  
|**mapped_nullable**|**bit**|指出訂閱者端的資料行是否接受 Null 值，其中 **1** 表示接受 null 值。|  
  
## <a name="see-also"></a>另請參閱  
 [異質資料庫複寫](../../relational-databases/replication/non-sql/heterogeneous-database-replication.md)   
 [複寫資料表 &#40;Transact-sql&#41;](../../relational-databases/system-tables/replication-tables-transact-sql.md)   
 [&#40;Transact-sql&#41;的複寫視圖 ](../../relational-databases/system-views/replication-views-transact-sql.md)   
 [sp_articlecolumn &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-articlecolumn-transact-sql.md)   
 [sysarticlecolumns &#40;System View&#41; &#40;Transact-sql&#41;](../../relational-databases/system-views/sysarticlecolumns-system-view-transact-sql.md)   
 [sysarticlecolumns &#40;Transact-sql&#41;](../../relational-databases/system-tables/sysarticlecolumns-transact-sql.md)  
  
  
