---
description: MSagent_profiles (Transact-SQL)
title: MSagent_profiles (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/06/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: replication
ms.topic: language-reference
f1_keywords:
- MSagent_profiles
- MSagent_profiles_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- MSagent_profiles system table
ms.assetid: 4ab1b2ae-b6d9-42b7-9b31-98547dbb7f99
author: cawrites
ms.author: chadam
ms.openlocfilehash: 237ecf85225d5c16160ee4b04f6e0ccdc41f2e31
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98094795"
---
# <a name="msagent_profiles-transact-sql"></a>MSagent_profiles (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  **MSagent_profiles** 資料表針對每個定義的複寫代理程式設定檔，各包含一個資料列。 此資料表儲存在 **msdb** 資料庫中。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**profile_id**|**int**|設定檔識別碼。|  
|**profile_name**|**sysname**|代理程式類型的唯一設定檔名稱。|  
|**agent_type**|**int**|代理程式的類型：<br /><br /> **1** = 快照集代理程式<br /><br /> **2** = 記錄讀取器代理程式<br /><br /> **3** = 散發代理程式<br /><br /> **4** = 合併代理程式<br /><br /> **9** = 佇列讀取器代理程式|  
|**type**|**int**|設定檔的類型：<br /><br /> **0** = 系統 **1** = 自訂|  
|**description**|**Nvarchar (3000)**|設定檔的描述。|  
|**def_profile**|**bit**|指定這個設定檔是否為這個代理程式類型的預設值。|  
  
## <a name="see-also"></a>另請參閱  
 [複寫資料表 &#40;Transact-sql&#41;](../../relational-databases/system-tables/replication-tables-transact-sql.md)   
 [複寫檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-views/replication-views-transact-sql.md)  
  
  
