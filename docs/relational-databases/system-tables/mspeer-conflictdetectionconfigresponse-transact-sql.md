---
title: 'MSpeer_conflictdetectionconfigresponse (T-sql) '
description: 描述在點對點複寫中使用的 MSPeer_conflictdetectionconfigureresponse 預存程式，以將每個節點的回應儲存至整個拓撲的設定 requestion。
ms.custom: seo-lt-2019
ms.date: 03/06/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: replication
ms.topic: language-reference
f1_keywords:
- MSpeer_conflictdetectionconfigresponse
- MSpeer_conflictdetectionconfigresponse_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- MSpeer_conflictdetectionconfigureresponse
ms.assetid: 2685fb66-731d-40f7-af4b-596b9222c5d4
author: cawrites
ms.author: chadam
ms.openlocfilehash: 02c82643c290e3b751ced451dd7415004272a2b0
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98097379"
---
# <a name="mspeer_conflictdetectionconfigresponse-transact-sql"></a>MSpeer_conflictdetectionconfigresponse (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  用於點對點複寫中，以儲存每一個節點對於全拓撲組態要求的回應。 這份資料表儲存在發行集資料庫中。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|request_id|**int**|識別 [MSpeer_conflictdetectionconfigrequest](../../relational-databases/system-tables/mspeer-conflictdetectionconfigrequest-transact-sql.md) 資料表中的衝突設定要求專案。|  
|peer_node|**sysname**|產生回應的伺服器執行個體名稱。|  
|peer_db|**sysname**|產生回應之對等的訂閱資料庫。|  
|peer_version|**sysname**|識別發行者的版本號碼。|  
|peer_db_version|**sysname**|識別對等資料庫的版本號碼。|  
|is_peer|**bit**|指示節點是否為唯讀訂閱者。 值為 **0** 表示唯讀訂閱者。|  
|conflict_detection_enabled|**bit**|指出是否已啟用拓撲的衝突偵測。|  
|originator_id|**varbinary(16)**|針對衝突偵測的目的，識別拓撲中的每個節點。 如需相關資訊，請參閱 [Conflict Detection in Peer-to-Peer Replication](../../relational-databases/replication/transactional/peer-to-peer-conflict-detection-in-peer-to-peer-replication.md)。|  
|peer_conflict_retention|**int**|該中繼資料儲存在衝突資料表內的期間 (天數)。|  
|peer_subscriptions|**XML**|有關回應要求之節點的資訊。|  
|progress_phase|**nvarchar(32)**|使用下列其中一個值，識別處理的目前階段：<br /><br /> 已開始<br /><br /> 已收集對等版本<br /><br /> 已收集狀態|  
|modified_date|**datetime**|完成階段的日期和時間。|  
  
## <a name="see-also"></a>另請參閱  
 [複寫資料表 &#40;Transact-sql&#41;](../../relational-databases/system-tables/replication-tables-transact-sql.md)   
 [複寫檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-views/replication-views-transact-sql.md)  
  
  
