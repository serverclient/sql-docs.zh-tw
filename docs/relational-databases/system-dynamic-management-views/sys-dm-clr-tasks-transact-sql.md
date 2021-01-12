---
description: sys.dm_clr_tasks (Transact-SQL)
title: sys.dm_clr_tasks (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.dm_clr_tasks
- sys.dm_clr_tasks_TSQL
- dm_clr_tasks
- dm_clr_tasks_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_clr_tasks dynamic management view
ms.assetid: 462b9061-09fa-4858-9707-03d6cc19c769
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 6f9639c03609e985017501e9d9c4f3669cb0eb44
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98099988"
---
# <a name="sysdm_clr_tasks-transact-sql"></a>sys.dm_clr_tasks (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  傳回目前執行之所有 Common Language Runtime (CLR) 工作的資料列。 包含 CLR 常式之參考的 [!INCLUDE[tsql](../../includes/tsql-md.md)] 批次會建立個別工作，來執行該批次內所有 Managed 程式碼。 批次中需要執行 Managed 程式碼的多個陳述式使用相同的 CLR 工作。 CLR 工作負責維護關於 Managed 程式碼執行的物件和狀態，以及 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的執行個體和 Common Language Runtime 之間的轉換。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**task_address**|**varbinary(8)**|CLR 工作的位址。|  
|**sos_task_address**|**varbinary(8)**|基礎 [!INCLUDE[tsql](../../includes/tsql-md.md)] 批次工作的位址。|  
|**appdomain_address**|**varbinary(8)**|執行這項工作的應用程式網域的位址。|  
|**state**|**nvarchar(128)**|工作的目前狀態。|  
|**abort_state**|**nvarchar(128)**|中止目前的狀態 (如果已取消工作)。中止工作時有多個相關的狀態。|  
|**type**|**nvarchar(128)**|工作類型。|  
|**affinity_count**|**int**|工作的相似性。|  
|**forced_yield_count**|**int**|強制產生工作的次數。|  
  
## <a name="permissions"></a>權限  

在上 [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] ，需要 `VIEW SERVER STATE` 許可權。   
在 SQL Database Basic、S0 和 S1 服務目標上，以及針對彈性集區中的資料庫，則 `Server admin` `Azure Active Directory admin` 需要或帳戶。 在所有其他 SQL Database 服務目標上， `VIEW DATABASE STATE` 資料庫中都需要有許可權。   
  
## <a name="see-also"></a>另請參閱  
 [動態管理檢視與函數 &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)   
 [Common Language Runtime 相關的動態管理檢視 &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/common-language-runtime-related-dynamic-management-views-transact-sql.md)  
  
  

