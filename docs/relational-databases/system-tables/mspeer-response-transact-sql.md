---
description: MSpeer_response (Transact-SQL)
title: MSpeer_response (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/06/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: replication
ms.topic: language-reference
f1_keywords:
- MSpeer_response
- MSpeer_response_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- MSpeer_response system table
ms.assetid: 510e24cf-0292-47a9-b1d9-71a30fef030f
author: cawrites
ms.author: chadam
ms.openlocfilehash: fdf2ec05835bc63f30e5304e8b8226b54be6becb
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98096182"
---
# <a name="mspeer_response-transact-sql"></a>MSpeer_response (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  **MSpeer_response** 資料表用於點對點複寫中，以儲存每個節點對於發行集狀態要求的回應。 這份資料表儲存在發行集資料庫中。  
  
## <a name="definition"></a>定義  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**request_id**|**int**|識別 [MSpeer_request](../../relational-databases/system-tables/mspeer-request-transact-sql.md) 資料表中的狀態要求專案。|  
|**同行**|**sysname**|產生回應的對等。|  
|**peer_db**|**sysname**|在產生回應之對等的訂閱資料庫。|  
|**received_date**|**datetime**|收到對等要求的日期和時間。|  
  
## <a name="see-also"></a>另請參閱  
 [複寫資料表 &#40;Transact-SQL&#41;](../../relational-databases/system-tables/replication-tables-transact-sql.md)  
  
  
