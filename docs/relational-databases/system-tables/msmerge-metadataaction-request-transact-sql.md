---
description: MSmerge_metadataaction_request (Transact-SQL)
title: MSmerge_metadataaction_request (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/03/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: replication
ms.topic: language-reference
f1_keywords:
- MSmerge_metadataaction_request
- MSmerge_metadataaction_request_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- MSmerge_metadataaction_request system table
ms.assetid: cd31a114-900a-4218-ab58-d959e547c647
author: cawrites
ms.author: chadam
ms.openlocfilehash: ceef4e122ced8e8f14bdc52cae86ef358f9c3a6e
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98102024"
---
# <a name="msmerge_metadataaction_request-transact-sql"></a>MSmerge_metadataaction_request (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  **MSmerge_metadataaction_request** 資料表會針對每個必要的補償動作，各儲存一個資料列。 使用 Web 同步處理時，如果發生錯誤，且必須重試同步處理，則會在 **MSmerge_metadataaction_request** 中輸入專案。 在進行後續合併的上傳階段時，對於所有屬於正在同步處理之發行集的發行項要求，都是從這份資料表擷取及上傳。 順利完成同步處理之後，就會刪除 **MSmerge_metadataaction_request** 資料表中的對應資料列。 這份資料表儲存在發行集資料庫的發行者端，以及訂閱資料庫的訂閱者端。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**tablenick**|**int**|已發行資料表的暱稱。|  
|**rowguid**|**uniqueidentifier**|給定資料列的資料列識別碼。|  
|**action**|**tinyint**|識別必要的補償動作。|  
|**生成**|**bigint**|必須進行補償動作的產生值。|  
|**改變**|**int**|僅供內部使用。|  
  
## <a name="see-also"></a>另請參閱  
 [複寫資料表 &#40;Transact-sql&#41;](../../relational-databases/system-tables/replication-tables-transact-sql.md)   
 [&#40;Transact-sql&#41;的複寫視圖 ](../../relational-databases/system-views/replication-views-transact-sql.md)   
 [合併式複寫的 Web 同步處理](../../relational-databases/replication/web-synchronization-for-merge-replication.md)  
  
  
