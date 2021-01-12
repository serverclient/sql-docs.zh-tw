---
description: sysdbmaintplan_jobs (Transact-SQL)
title: sysdbmaintplan_jobs (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sysdbmaintplan_jobs
- sysdbmaintplan_jobs_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sysdbmaintplan_jobs system table
ms.assetid: bc65cd70-6ef2-4c17-be11-877ecf4efe50
author: cawrites
ms.author: chadam
ms.openlocfilehash: 3c1c925fca9ae3fab98e0cc3579a9105db855f71
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98098072"
---
# <a name="sysdbmaintplan_jobs-transact-sql"></a>sysdbmaintplan_jobs (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  這個資料表會包含在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中，以保存從舊版 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 升級而來之執行個體的現有資訊。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 不會變更此資料表的內容。 此資料表儲存在 **msdb** 資料庫中。  
  
 [!INCLUDE[ssNoteDepNextAvoid](../../includes/ssnotedepnextavoid-md.md)]  
  

  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**plan_id**|**uniqueidentifier**|資料庫維護計畫識別碼。|  
|**job_id**|**uniqueidentifier**|資料庫維護計畫之相關作業的識別碼。|  
  
  
