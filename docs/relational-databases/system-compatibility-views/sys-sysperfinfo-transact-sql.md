---
description: sys.sysperfinfo (Transact-SQL)
title: sys.sysperfinfo (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sysperfinfo_TSQL
- sys.sysperfinfo_TSQL
- sys.sysperfinfo
- sysperfinfo
dev_langs:
- TSQL
helpviewer_keywords:
- sys.sysperfinfo compatibility view
- sysperfinfo system table
ms.assetid: e22a81cd-27de-4690-9443-6aad6393bd3c
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 477121d623733b5bafcf4385d8069715a333175e
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98095398"
---
# <a name="syssysperfinfo-transact-sql"></a>sys.sysperfinfo (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  包含 [!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 可透過 Windows 系統監視器顯示的內部效能計數器表示。  
  
> [!IMPORTANT]  
>  [!INCLUDE[ssnoteCompView](../../includes/ssnotecompview-md.md)]  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**object_name**|**nchar(128)**|效能物件名稱，例如 **sqlserver： LockManager** 或 **sqlserver： BufferManager**。|  
|**counter_name**|**nchar(128)**|物件內的效能計數器名稱，例如要求的 **頁面要求** 或 **鎖定**。|  
|**instance_name**|**nchar(128)**|計數器的具名執行個體。 例如，系統會針對每一種鎖定類型維護計數器，例如 **資料表**、 **頁面**、索引 **鍵** 等等。 相似計數器的執行個體名稱各不相同。|  
|**cntr_value**|**bigint**|實際的計數器值。 這通常都是一個層級計數器或單純增加的計數器，用來計算執行個體事件的出現次數。|  
|**cntr_type**|**int**|Windows 效能架構所定義的計數器類型。|  
  
## <a name="see-also"></a>另請參閱  
 [&#40;Transact-sql&#41;將系統資料表對應至系統檢視 ](../../relational-databases/system-tables/mapping-system-tables-to-system-views-transact-sql.md)   
 [相容性檢視 &#40;Transact-SQL&#41;](~/relational-databases/system-compatibility-views/system-compatibility-views-transact-sql.md)  
  
  
