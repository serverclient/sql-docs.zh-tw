---
description: dbo.sysjobs (Transact-SQL)
title: dbo.sys作業 (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 08/09/2016
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sysjobs
- sysjobs_TSQL
- dbo.sysjobs
- dbo.sysjobs_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sysjobs system table
ms.assetid: e244a6a5-54c2-47a6-8039-dd1852b0ae59
author: cawrites
ms.author: chadam
ms.openlocfilehash: e36afae06020e9e970359cb09bc79a9bdf346b03
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98091499"
---
# <a name="dbosysjobs-transact-sql"></a>dbo.sysjobs (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  儲存 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent 所要執行之各項排程作業的資訊。 此資料表儲存在 **msdb** 資料庫中。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**job_id**|**uniqueidentifier**|作業的唯一識別碼。|  
|**originating_server_id**|**int**|作業的來源伺服器識別碼。|  
|**name**|**sysname**|作業的名稱。|  
|**啟用**|**tinyint**|指出是否啟用作業，以便執行。|  
|**description**|**nvarchar(512)**|作業的描述。|  
|**start_step_id**|**int**|應該作為執行起點的作業步驟識別碼。|  
|**category_id**|**int**|作業類別目錄的識別碼。|  
|**owner_sid**|**varbinary(85)**|作業擁有者的安全性識別碼 (SID)。|  
|**notify_level_eventlog**|**int**|表示在哪些情況下，應該將通知事件記錄到 Microsoft Windows 應用程式記錄檔的 **位元遮罩**：<br /><br /> **0** = 永不<br /><br /> **1** = 當作業成功時<br /><br /> **2** = 當作業失敗時<br /><br /> **3** = 每當作業完成時 (，不論工作結果) |  
|**notify_level_email**|**int**|這是一個位元組遮罩，指出在哪些情況之下，應該在作業完成時傳送通知電子郵件：<br /><br /> **0** = 永不<br /><br /> **1** = 當作業成功時<br /><br /> **2** = 當作業失敗時<br /><br /> **3** = 每當作業完成時 (，不論工作結果) |  
|**notify_level_netsend**|**int**|這是一個位元組遮罩，指出在哪些情況之下，應該在作業完成時傳送網路訊息：<br /><br /> **0** = 永不<br /><br /> **1** = 當作業成功時<br /><br /> **2** = 當作業失敗時<br /><br /> **3** = 每當作業完成時 (，不論工作結果) |  
|**notify_level_page**|**int**|這是一個位元組遮罩，指出在哪些情況之下，應該在作業完成時傳送頁面：<br /><br /> **0** = 永不<br /><br /> **1** = 當作業成功時<br /><br /> **2** = 當作業失敗時<br /><br /> **3** = 每當作業完成時 (，不論工作結果) |  
|**notify_email_operator_id**|**int**|要通知的操作員電子郵件名稱。|  
|**notify_netsend_operator_id**|**int**|傳送網路訊息時所用的電腦或使用者識別碼。|  
|**notify_page_operator_id**|**int**|傳送呼叫時所用的電腦或使用者識別碼。|  
|**delete_level**|**int**|此為 **位元遮罩**，指出在哪些情況下，應該在作業完成時刪除作業：<br /><br /> **0** = 永不<br /><br /> **1** = 當作業成功時<br /><br /> **2** = 當作業失敗時<br /><br /> **3** = 每當作業完成時 (，不論工作結果) |  
|**date_created**|**datetime**|作業的建立日期。|  
|**date_modified**|**datetime**|上次修改作業的日期。|  
|**version_number**|**int**|作業的版本。|  
  
  
