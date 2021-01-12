---
description: dbo.systargetservers (Transact-SQL)
title: dbo.systargetservers (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- dbo.systargetservers_TSQL
- dbo.systargetservers
- systargetservers_TSQL
- systargetservers
dev_langs:
- TSQL
helpviewer_keywords:
- systargetservers system table
ms.assetid: 479d1314-be37-4d19-ac9c-419fc9110e53
author: cawrites
ms.author: chadam
ms.openlocfilehash: 00e90f2c6911e1b80b37169fab67382f15997f03
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98098231"
---
# <a name="dbosystargetservers-transact-sql"></a>dbo.systargetservers (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  記錄在這個多伺服器的作業網域中，目前編列了哪些目標伺服器。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**server_id**|**int**|伺服器識別碼。|  
|**server_name**|**sysname**|伺服器名稱。|  
|**location**|**nvarchar(200)**|指定之目標伺服器的位置。|  
|**time_zone_adjustment**|**int**|相對於格林威治標準時間 (GMT) 的時間調整間隔 (小時)。|  
|**enlist_date**|**datetime**|編列指定的目標伺服器之日期和時間。|  
|**last_poll_date**|**datetime**|指定的目標伺服器上次輪詢多伺服器 **sysdownloadlist** 系統資料表以取得要執行之作業的日期和時間。|  
|**status**|**int**|目標伺服器的狀態：<br /><br /> **1** = 一般<br /><br /> **2** = 重新同步處理擱置中<br /><br /> **4** = 可疑的離線|  
|**local_time_at_last_poll**|**datetime**|前次輪詢目標伺服器來尋找作業的日期和時間。|  
|**enlisted_by_nt_user**|**Nvarchar (100)**|在目標伺服器上執行 **sp_msx_enlist** 之人員的使用者名稱。|  
|**poll_internal**|**int**|目標伺服器經過多久 (秒) 之後，便輪詢主要伺服器來尋找新的下載指示。|  
  
  
