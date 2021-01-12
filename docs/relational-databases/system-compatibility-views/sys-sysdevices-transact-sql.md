---
description: sys.sysdevices (Transact-SQL)
title: sys.sys(Transact-sql) 的裝置 |Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sysdevices
- sysdevices_TSQL
- sys.sysdevices
- sys.sysdevices_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.sysdevices compatibility view
- sysdevices system table
ms.assetid: ac5bcaf4-8fb6-4855-8856-d7643f469361
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 0d672ec8b722322ff47841530e419e09ca7e2874
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98099111"
---
# <a name="syssysdevices-transact-sql"></a>sys.sysdevices (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  針對每個磁碟備份檔案、磁帶備份檔案和資料庫檔案，各包含一個資料列。  
  
> [!IMPORTANT]  
>  [!INCLUDE[ssnoteCompView](../../includes/ssnotecompview-md.md)]  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**name**|**sysname**|備份檔案或資料庫檔案的邏輯名稱。|  
|**size**|**int**|該檔案在 2 KB 網頁中的大小。|  
|**低**|**int**|維護這個項目的目的，只是為了與舊版相容。|  
|**高**|**int**|維護這個項目的目的，只是為了與舊版相容。|  
|**status**|**smallint**|指出裝置類型的點陣圖：<br /><br /> 1 = 預設磁碟<br /><br /> 2 = 實體磁碟<br /><br /> 4 = 邏輯磁碟<br /><br /> 8 = 跳過標頭<br /><br /> 16 = 備份檔案<br /><br /> 32 = 序列寫入<br /><br /> 4096 = 唯讀|  
|**cntrltype**|**smallint**|控制器類型：<br /><br /> 0 = 非 CD-ROM 資料庫檔案<br /><br /> 2 = 磁碟備份檔案<br /><br /> 3 - 4 = 磁片備份檔案<br /><br /> 5 = 磁帶備份檔案<br /><br /> 6 = 具名管道檔案|  
|**phyname**|**nvarchar(260)**|實體檔案名稱。|  
  
## <a name="see-also"></a>另請參閱  
 [&#40;Transact-sql&#41;將系統資料表對應至系統檢視 ](../../relational-databases/system-tables/mapping-system-tables-to-system-views-transact-sql.md)   
 [相容性檢視 &#40;Transact-SQL&#41;](~/relational-databases/system-compatibility-views/system-compatibility-views-transact-sql.md)  
  
  
