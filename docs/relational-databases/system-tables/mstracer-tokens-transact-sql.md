---
description: MStracer_tokens (Transact-SQL)
title: MStracer_tokens (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/06/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: replication
ms.topic: language-reference
f1_keywords:
- MStracer_tokens_TSQL
- MStracer_tokens
dev_langs:
- TSQL
helpviewer_keywords:
- MStracer_tokens system table
ms.assetid: b273aa48-8188-4213-8e2c-311543c3236f
author: cawrites
ms.author: chadam
ms.openlocfilehash: cf6c1736531f926fa5871e56fdcefb7322289897
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98098091"
---
# <a name="mstracer_tokens-transact-sql"></a>MStracer_tokens (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  **MStracer_tokens** 資料表會維護插入發行集中之追蹤 token 記錄的記錄。 這份資料表儲存在散發資料庫中，並且由複寫用來監視效能之用。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**tracer_id**|**int**|識別追蹤 Token 記錄。|  
|**publication_id**|**int**|識別追蹤 Token 記錄所插入的發行集。|  
|**publisher_commit**|**datetime**|在發行者端認可追蹤 Token 記錄的日期和時間。|  
|**distributor_commit**|**datetime**|在散發者端認可追蹤 Token 記錄的日期和時間。|  
  
## <a name="see-also"></a>另請參閱  
 [複寫資料表 &#40;Transact-sql&#41;](../../relational-databases/system-tables/replication-tables-transact-sql.md)   
 [複寫檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-views/replication-views-transact-sql.md)  
  
  
