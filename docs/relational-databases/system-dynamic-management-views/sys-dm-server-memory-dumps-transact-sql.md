---
description: sys.dm_server_memory_dumps (Transact-SQL)
title: sys.dm_server_memory_dumps (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.dm_server_memory_dumps_TSQL
- dm_server_memory_dumps_TSQL
- dm_server_memory_dumps
- sys.dm_server_memory_dumps
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_server_memory_dumps dynamic management view
ms.assetid: 41782719-f54d-4e11-941a-c050c7576e23
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 29ae5e940daffd0487d5d3e4514b5b624dd1320e
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98096489"
---
# <a name="sysdm_server_memory_dumps-transact-sql"></a>sys.dm_server_memory_dumps (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  針對 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 所產生的每個記憶體傾印檔，各傳回一個資料列。 使用此動態管理檢視疑難排解潛在問題。  
 
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**filename**|**nvarchar(256)**|記憶體傾印檔的路徑和名稱。 不可以是 null。|  
|**creation_time**|**datetimeoffset(7)**|建立檔案的日期與時間。 不可以是 null。|  
|**size_in_bytes**|**bigint**|檔案大小 (以位元組為單位)。 可為 Null。|  
  
## <a name="general-remarks"></a>一般備註  
 傾印類型可以是迷你傾印、所有執行緒傾印或完整傾印。 檔案的副檔名為 .mdmp。  
  
## <a name="security"></a>安全性  
 傾印檔可能包含敏感性資訊。 若要保護敏感性資訊，您可以使用存取控制清單 (ACL) 來限制這些檔案的存取權，或將這些檔案複製到具有存取限制的資料夾。 例如，在將偵錯檔傳回至 Microsoft 支援服務之前，建議您先移除任何敏感或機密資訊。  
  
### <a name="permissions"></a>權限  
 需要 VIEW SERVER STATE 權限。  
  
  
