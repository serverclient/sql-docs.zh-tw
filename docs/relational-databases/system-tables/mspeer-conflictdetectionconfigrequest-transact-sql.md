---
title: 'MSpeer_conflictdetectionconfigrequest (T-sql) '
description: 描述用來追蹤點對點發行集之拓撲 wide 設定要求的 MSPeer_conflictdetectionconfigurerequest 預存程式。
ms.custom: seo-lt-2019
ms.date: 03/06/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: replication
ms.topic: language-reference
f1_keywords:
- MSpeer_conflictdetectionconfigrequest_TSQL
- MSpeer_conflictdetectionconfigrequest
dev_langs:
- TSQL
helpviewer_keywords:
- MSpeer_conflictdetectionconfigurerequest
ms.assetid: 83afa0ca-707e-4468-a888-228268ed4e10
author: cawrites
ms.author: chadam
ms.openlocfilehash: a8e6d22946599946dda10afcd1d5c46318c0342a
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98098561"
---
# <a name="mspeer_conflictdetectionconfigrequest-transact-sql"></a>MSpeer_conflictdetectionconfigrequest (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  用於點對點複寫中，以追蹤發行集的全拓撲組態要求。 這份資料表儲存在發行集資料庫中。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|id|**int**|識別衝突組態要求。 [MSpeer_conflictdetectionconfigresponse](../../relational-databases/system-tables/mspeer-conflictdetectionconfigresponse-transact-sql.md)中的 request_id 資料行使用此值。|  
|publication|**sysname**|起始衝突組態要求的發行集名稱。|  
|sent_date|**datetime**|起始衝突組態要求的日期和時間。|  
|timeout|**int**|程序應該等候所有對等項目傳回衝突資訊的時間量。|  
|modified_date|**datetime**|完成階段的日期和時間。|  
|progress_phase|**nvarchar(32)**|使用下列其中一個值，識別處理的目前階段：<br /><br /> 已開始<br /><br /> 瀏覽拓撲<br /><br /> 正在收集狀態<br /><br /> 已收集狀態|  
|phase_timed_out|**bit**|指出目前階段是否已逾時。|  
  
## <a name="see-also"></a>另請參閱  
 [複寫資料表 &#40;Transact-sql&#41;](../../relational-databases/system-tables/replication-tables-transact-sql.md)   
 [複寫檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-views/replication-views-transact-sql.md)  
  
  
