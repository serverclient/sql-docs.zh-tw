---
description: sys.dm_os_stacks (Transact-SQL)
title: sys.dm_os_stacks (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/13/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- dm_os_stacks
- dm_os_stacks_TSQL
- sys.dm_os_stacks
- sys.dm_os_stacks_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_os_stacks dynamic management view
ms.assetid: a69b06c4-28f0-4535-8fa1-9f132db4d916
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 6e5c42d0a82ce7726883e46f2e633aacc624e4db
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98099718"
---
# <a name="sysdm_os_stacks-transact-sql"></a>sys.dm_os_stacks (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  這份動態管理檢視可供 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 在內部使用，以執行下列動作：  
  
-   追蹤偵錯資料，例如未完成的配置。  
  
-   在元件假設發出特定呼叫之處，假設或驗證 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 元件使用的邏輯。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**stack_address**|**varbinary(8)**|這項堆疊配置的唯一位址。 不可為 Null。|  
|**frame_index**|**int**|每一行代表一個函式呼叫，當特定 **stack_address** 的依框架索引以遞增順序排序時，會傳回完整的呼叫堆疊。 不可為 Null。|  
|**frame_address**|**varbinary(8)**|函數呼叫的位址。 不可為 Null。|  
  
## <a name="remarks"></a>備註  
 **sys.dm_os_stacks** 要求伺服器和其他元件的符號必須存在於伺服器上，才能正確顯示資訊。  
  
## <a name="permissions"></a>權限

在上 [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] ，需要 `VIEW SERVER STATE` 許可權。   
在 SQL Database Basic、S0 和 S1 服務目標上，以及針對彈性集區中的資料庫，則 `Server admin` `Azure Active Directory admin` 需要或帳戶。 在所有其他 SQL Database 服務目標上， `VIEW DATABASE STATE` 資料庫中都需要有許可權。   


## <a name="see-also"></a>另請參閱  
  [SQL Server 作業系統相關的動態管理檢視 &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sql-server-operating-system-related-dynamic-management-views-transact-sql.md)  
  
  
