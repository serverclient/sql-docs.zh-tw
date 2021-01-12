---
description: sys.sysfiles (Transact-SQL)
title: sys.sys檔案 (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sysfiles
- sys.sysfiles_TSQL
- sys.sysfiles
- sysfiles_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sysfiles system table
- sys.sysfiles compatibility view
ms.assetid: 3b47f38d-1cff-404d-89d3-9342c451c802
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 862ffbdd0967068ef1b8345176edb4551e542196
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98099061"
---
# <a name="syssysfiles-transact-sql"></a>sys.sysfiles (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  針對資料庫中的每個檔案，各包含一個資料列。  
  
> [!IMPORTANT]  
>  [!INCLUDE[ssnoteCompView](../../includes/ssnotecompview-md.md)]  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**fileid**|**smallint**|每個資料庫的唯一檔案識別碼。|  
|**groupid**|**smallint**|檔案群組識別碼。|  
|**size**|**int**|檔案大小 (以 8KB 頁面為單位)。|  
|**maxsize**|**int**|最大檔案大小 (以 8 KB 頁面為單位)。<br /><br /> 0 = 不成長。<br /><br /> -1 = 檔案會成長到磁碟已滿。<br /><br /> 268435456 = 記錄檔可以成長到最大 2 TB 的大小。<br /><br /> 注意：以無限制的記錄檔大小升級的資料庫，將會報告記錄檔大小上限-1。|  
|**增長**|**int**|資料庫的成長大小。 可以是頁數或檔案大小百分比，視 **狀態** 的值而定。<br /><br /> 0 = 不成長。|  
|**status**|**int**|**成長** 值的狀態位，以 MB (mb) 或 KB (kb) 。<br /><br /> 0x2 = 磁碟檔。<br /><br /> 0x40 = 記錄檔。<br /><br /> 0x100000 = 成長。 這個值是一個百分比，不是頁數。|  
|**效能**|**int**|保留的。|  
|**name**|**sysname**|檔案的邏輯名稱。|  
|**filename**|**nvarchar(260)**|實體裝置的名稱。 其中包括檔案的完整路徑。|  
  
## <a name="see-also"></a>另請參閱  
 [&#40;Transact-sql&#41;將系統資料表對應至系統檢視 ](../../relational-databases/system-tables/mapping-system-tables-to-system-views-transact-sql.md)   
 [相容性檢視 &#40;Transact-SQL&#41;](~/relational-databases/system-compatibility-views/system-compatibility-views-transact-sql.md)  
  
  
