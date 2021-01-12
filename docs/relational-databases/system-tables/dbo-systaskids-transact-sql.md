---
description: dbo.systaskids (Transact-SQL)
title: dbo.systaskids (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 08/09/2016
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- systaskids_TSQL
- dbo.systaskids
- systaskids
- dbo.systaskids_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- systaskids system table
ms.assetid: 45c56d89-4160-4d84-80bf-a7a05488792d
author: cawrites
ms.author: chadam
ms.openlocfilehash: 1e98a4a8a7e41b584fa9a397aa2aa98906d45bd1
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98093757"
---
# <a name="dbosystaskids-transact-sql"></a>dbo.systaskids (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  包含舊版 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中所建立的工作與目前版本中 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 作業的對應。 此資料表儲存在 **msdb** 資料庫中。  
  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**task_id**|**int**|工作識別碼|  
|**job_id**|**uniqueidentifier**|工作所對應之作業的識別碼|  
  
  
