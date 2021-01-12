---
description: sys.sysremotelogins (Transact-SQL)
title: sys.sysremotelogins (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sysremotelogins
- sysremotelogins_TSQL
- sys.sysremotelogins
- sys.sysremotelogins_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sysremotelogins system table
- sys.sysremotelogins compatibility view
ms.assetid: b7ffcfa6-aed8-41d4-8b70-845439ab813d
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: d22ef924002c422bf74844e35a4177e91b34ec50
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98095358"
---
# <a name="syssysremotelogins-transact-sql"></a>sys.sysremotelogins (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  針對每個允許呼叫實例上遠端預存程式的遠端使用者，各包含一個資料列 [!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。  
  
> [!IMPORTANT]  
>  [!INCLUDE[ssnoteCompView](../../includes/ssnotecompview-md.md)]  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**remoteserverid**|**smallint**|遠端伺服器識別。|  
|**remoteusername**|**sysname**|遠端伺服器的使用者登入名稱。|  
|**status**|**smallint**|傳回 0。|  
|**希**|**varbinary(85)**|[!INCLUDE[msCoName](../../includes/msconame-md.md)] Windows 使用者安全性識別碼。|  
|**changedate**|**datetime**|加入遠端使用者的日期和時間。|  
  
## <a name="see-also"></a>另請參閱  
 [&#40;Transact-sql&#41;將系統資料表對應至系統檢視 ](../../relational-databases/system-tables/mapping-system-tables-to-system-views-transact-sql.md)   
 [相容性檢視 &#40;Transact-SQL&#41;](~/relational-databases/system-compatibility-views/system-compatibility-views-transact-sql.md)  
  
  
