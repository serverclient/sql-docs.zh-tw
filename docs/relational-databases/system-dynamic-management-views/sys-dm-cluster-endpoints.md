---
description: 'sys.dm_cluster_endpoints (Transact-sql) '
title: sys.dm_cluster_endpoints (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 11/04/2019
ms.prod: sql
ms.prod_service: database-engine, big-data-clusters
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.dm_cluster_endpoints
- dm_cluster_endpoints_TSQL
- dm_cluster_endpoints
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_cluster_endpoints dynamic management view
ms.assetid: ''
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=sql-server-ver15||>=sql-server-linux-2017'
ms.openlocfilehash: 9f9d78015c5413c7e0ec4e827240e7465969d189
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98095248"
---
# <a name="sysdm_cluster_endpoints-transact-sql"></a>sys.dm_cluster_endpoints (Transact-sql) 
[!INCLUDE[sqlserver2019](../../includes/applies-to-version/sqlserver2019.md)]

|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|NAME|`sysname`|在 SQL big data 叢集中外部公開的服務名稱。 端點的唯一識別碼。 此視圖的索引鍵。 不可為 Null。 |  
|description|`nvarchar(4000)`|服務的描述。 不可為 Null。 |
|端點|`sysname`|端點 url 或連接屬性。 不可為 Null。 |
|protocol_desc|`sysname`|端點通訊協定的描述 |

## <a name="permissions"></a>權限

在上 [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] ，需要 `VIEW SERVER STATE` 許可權。

## <a name="see-also"></a>另請參閱

[什麼是 [!INCLUDE[big-data-clusters-2019](../../includes/ssbigdataclusters-ss-nover.md)] ](../../big-data-cluster/big-data-cluster-overview.md)？
