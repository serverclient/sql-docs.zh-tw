---
description: MSmerge_identity_range (Transact-SQL)
title: MSmerge_identity_range (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/04/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: replication
ms.topic: language-reference
f1_keywords:
- MSmerge_identity_range_TSQL
- MSmerge_identity_range
dev_langs:
- TSQL
helpviewer_keywords:
- MSmerge_identity_range system table
ms.assetid: 493a2028-88a0-4e83-ad89-ae5661d9f477
author: cawrites
ms.author: chadam
ms.openlocfilehash: f4174aa0b720727ad765e8fa9b62b8d3730dab09
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98102028"
---
# <a name="msmerge_identity_range-transact-sql"></a>MSmerge_identity_range (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  **MSmerge_identity_range** 資料表可用來追蹤指派給識別欄位的數值範圍，以訂閱發行集的訂閱，而複寫會自動管理這些範圍指派。 這份資料表儲存在發行集和訂閱資料庫中。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**subid**|**uniqueidentifier**|給定訂閱的唯一識別碼。|  
|**>artid**|**uniqueidentifier**|給定發行項的唯一識別碼。|  
|**range_begin**|**數值 (38)**|在目前範圍開頭的識別值。|  
|**range_end**|**數值 (38)**|在目前範圍結尾的識別值。|  
|**next_range_begin**|**數值 (38)**|在下一個要指派之範圍開頭的識別值。|  
|**next_range_end**|**數值 (38)**|在下一個要指派之範圍結尾的識別值。|  
|**is_pub_range**|**bit**|如果將識別範圍指派給發行集，則值為 **1** 。|  
|**max_used**|**數值 (38)**|所能指派的最大識別值。|  
  
## <a name="see-also"></a>另請參閱  
 [複寫資料表 &#40;Transact-sql&#41;](../../relational-databases/system-tables/replication-tables-transact-sql.md)   
 [複寫檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-views/replication-views-transact-sql.md)  
  
  
