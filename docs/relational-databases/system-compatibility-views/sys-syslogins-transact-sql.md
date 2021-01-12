---
description: sys.syslogins (Transact-SQL)
title: sys.sys登入 (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 09/08/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- syslogins_TSQL
- syslogins
- sys.syslogins
- sys.syslogins_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.syslogins compatibility view
- syslogins system table
ms.assetid: 4cb34f17-a4bb-469f-a218-71f074e6308f
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: acf9e8ff2592927c8107b1b6bacd02a6de9d3a48
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98094144"
---
# <a name="syssyslogins-transact-sql"></a>sys.syslogins (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  針對每個登入帳戶，各包含一個資料列。  
  
> [!IMPORTANT]  
>  [!INCLUDE[ssnoteCompView](../../includes/ssnotecompview-md.md)]  
  
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 至 [目前版本](../../sql-server/what-s-new-in-sql-server-2016.md))。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**希**|**varbinary(85)**|安全性識別碼。|  
|**status**|**smallint**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|**createdate**|**datetime**|加入登入的日期。|  
|**updatedate**|**datetime**|更新登入的日期。|  
|**accdate**|**datetime**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|**totcpu**|**int**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|**totio**|**int**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|**spacelimit**|**int**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|**timelimit**|**int**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|**resultlimit**|**int**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|**name**|**sysname**|使用者的登入名稱。|  
|**dbname**|**sysname**|當建立連接時，使用者的預設資料庫名稱。|  
|**password**|**nvarchar(128)**|傳回 NULL。|  
|**language**|**sysname**|使用者的預設名稱。|  
|**denylogin**|**int**|1 = 登入是 [!INCLUDE[msCoName](../../includes/msconame-md.md)] Windows 使用者或群組，已被拒絕存取。|  
|**hasaccess**|**int**|1 = 登入已被授與伺服器的存取權。|  
|**isntname**|**int**|1 = 登入是 Windows 使用者或群組。<br /><br /> 0 = 登入為 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入。|  
|**isntgroup**|**int**|1 = 登入是 Windows 群組。|  
|**isntuser**|**int**|1 = 登入是 Windows 使用者。|  
|**sysadmin**|**int**|1 = 登入是 **系統管理員（sysadmin** ）伺服器角色的成員。|  
|**securityadmin**|**int**|1 = 登入是 **securityadmin** 伺服器角色的成員。|  
|**serveradmin**|**int**|1 = 登入是 **serveradmin** 固定伺服器角色的成員。|  
|**setupadmin**|**int**|1 = 登入是 **setupadmin** 固定伺服器角色的成員。|  
|**processadmin**|**int**|1 = 登入是 **processadmin** 固定伺服器角色的成員。|  
|**diskadmin**|**int**|1 = 登入是 **diskadmin** 固定伺服器角色的成員。|  
|**dbcreator**|**int**|1 = 登入是 **dbcreator** 固定伺服器角色的成員。|  
|**bulkadmin**|**int**|1 = 登入是 **bulkadmin** 固定伺服器角色的成員。|  
|**loginname**|**nvarchar(128)**|使用者的登入名稱。 提供這個項目的目的，是為了與舊版相容。|  
  
## <a name="see-also"></a>另請參閱  
 [&#40;Transact-sql&#41;將系統資料表對應至系統檢視 ](../../relational-databases/system-tables/mapping-system-tables-to-system-views-transact-sql.md)   
 [相容性檢視 &#40;Transact-SQL&#41;](~/relational-databases/system-compatibility-views/system-compatibility-views-transact-sql.md)  
  
