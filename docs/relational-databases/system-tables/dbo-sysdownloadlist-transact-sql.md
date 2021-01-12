---
description: dbo.sysdownloadlist (Transact-SQL)
title: dbo.sysdownloadlist (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/03/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- dbo.sysdownloadlist
- sysdownloadlist_TSQL
- dbo.sysdownloadlist_TSQL
- sysdownloadlist
dev_langs:
- TSQL
helpviewer_keywords:
- sysdownloadlist system table
ms.assetid: 71087a4c-e829-488e-aa7d-a9476e2b4779
author: cawrites
ms.author: chadam
ms.openlocfilehash: b523812f7923f6ba68a40fe70ab2076950a3463a
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98098718"
---
# <a name="dbosysdownloadlist-transact-sql"></a>dbo.sysdownloadlist (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  存放所有目標伺服器的下載指示佇列。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**instance_id**|**int**|提供資料列自然插入順序的識別欄位。|  
|**source_server**|**sysname**|來源伺服器的名稱。|  
|**operation_code**|**tinyint**|作業的作業碼：<br /><br /> **1** = INS (插入) <br /><br /> **2** = UPD (更新) <br /><br /> **3** = DEL (刪除) <br /><br /> **4** = 啟動<br /><br /> **5** = 停止|  
|**object_type**|**tinyint**|物件類型碼。|  
|**object_id** <sup>1</sup>|**uniqueidentifier**|物件識別碼。|  
|**target_server**|**sysname**|目標伺服器的名稱。|  
|**error_message**|**nvarchar(1024)**|當目標伺服器處理特定資料列而發現錯誤時，所出現的錯誤訊息。|  
|**date_posted**|**datetime**|作業公佈到目標伺服器的日期和時間。|  
|**date_downloaded**|**datetime**|前次下載作業的日期和時間。|  
|**status**|**tinyint**|作業的狀態：<br /><br /> **0** = 尚未下載<br /><br /> **1** = 成功下載|  
|**deleted_object_name**|**sysname**|已刪除的物件名稱。|  
  
 <sup>1</sup> **object_id** 資料行可以是 **-1** 的值，如果 **operation_code** 資料行是 [刪除] 的值，就會對應到 [全部] 的值。  
  
  
