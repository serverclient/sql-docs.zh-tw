---
description: MSreplmonthresholdmetrics (Transact-SQL)
title: MSreplmonthresholdmetrics (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/04/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: replication
ms.topic: language-reference
f1_keywords:
- msreplmonthresholdmetrics_TSQL
- msreplmonthresholdmetrics
dev_langs:
- TSQL
helpviewer_keywords:
- MSreplmonthresholdmetrics system table
ms.assetid: 0cc9b40a-36ce-485b-9bc2-d4abd5aa6727
author: cawrites
ms.author: chadam
ms.openlocfilehash: e8d7d5c1ea9bbf421632e1d742cb316fd2c94cd2
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98094768"
---
# <a name="msreplmonthresholdmetrics-transact-sql"></a>MSreplmonthresholdmetrics (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  **MSreplmonthresholdmetrics** 資料表會定義針對監視複寫所提供的計量。 此資料表儲存在 **msdb** 資料庫中。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**metric_id**|**int**|識別複寫效能標準，而且它可以是下列其中一個值：<br /><br /> **1** = 到期<br /><br /> **2** = 延遲<br /><br /> **4** = mergeexpiration<br /><br /> **5** = mergeslowrunduration 利用<br /><br /> **6** = mergefastrunduration 利用<br /><br /> **7** = mergefastrunspeed 利用<br /><br /> **8** = mergeslowrunspeed|  
|**title**|**sysname**|複寫效能標準的名稱。|  
|**warningbitstatus**|**int**|用來提供下列標準的臨界違規警告之位元識別碼：<br /><br /> **1** = 到期-交易式發行集的訂閱已超出保留期限超過允許的臨界值，以保留期限的百分比表示。<br /><br /> **2** = 延遲-將交易式發行者的資料複寫到訂閱者所花費的時間超過閾值（以秒為單位）。<br /><br /> **4** = mergeexpiration-合併式發行集的訂閱已超出保留期限超過允許的臨界值，以保留期限的百分比表示。<br /><br /> **8** = mergefastrunduration 利用-完成合併訂閱的同步處理所花費的時間超過閾值（以秒為單位），透過快速網路連接。<br /><br /> **16** = mergeslowrunduration 利用-完成合併訂閱的同步處理所花費的時間超過閾值（以秒為單位），而不是透過慢速或撥號網路連接。<br /><br /> **32** = mergefastrunspeed 利用-在同步處理合併訂閱期間，資料列的傳遞速率無法維持閾值速率（以每秒資料列數為單位），透過快速網路連接。<br /><br /> **64** = mergeslowrunspeed-在同步處理合併訂閱期間，資料列的傳遞速率無法維持閾值速率（以每秒資料列數為單位），透過慢速或撥號網路連接。|  
|**alertmessageid**|**int**|當發生臨界值警告狀況時，所顯示之錯誤訊息的識別碼。|  
|**description**|**Nvarchar (3000)**|複寫效能標準的描述|  
|**default_value**|**sql_variant**|複寫效能標準的預設值。|  
|**min_value**|**sql_variant**|繫結的複寫效能標準的最小值。|  
|**max_value**|**sql_variant**|繫結的複寫效能標準的最大值。|  
  
## <a name="see-also"></a>另請參閱  
 [複寫資料表 &#40;Transact-sql&#41;](../../relational-databases/system-tables/replication-tables-transact-sql.md)   
 [複寫檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-views/replication-views-transact-sql.md)  
  
  
