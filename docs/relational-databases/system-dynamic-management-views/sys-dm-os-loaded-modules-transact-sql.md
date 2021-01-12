---
description: sys.dm_os_loaded_modules (Transact-SQL)
title: sys.dm_os_loaded_modules (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 08/18/2017
ms.prod: sql
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.dm_os_loaded_modules
- dm_os_loaded_modules
- sys.dm_os_loaded_modules_TSQL
- dm_os_loaded_modules_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_os_loaded_modules dynamic management view
ms.assetid: 56c7743a-b568-4943-bd3b-73c57d9d641c
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 77f4e2e69e73be88bd4cb70dcad7d7069eb8539d
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98094025"
---
# <a name="sysdm_os_loaded_modules-transact-sql"></a>sys.dm_os_loaded_modules (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  針對已載入至伺服器位址空間的每一個模組，各傳回一個資料列。  
  
> [!NOTE]  
>  若要從呼叫這個 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] ，請使用名稱 **sys.dm_pdw_nodes_os_loaded_modules**。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**base_address**|**varbinary(8)**|處理序中的模組位址。|  
|**file_version**|**Varchar (23)**|檔案的版本。 以下列格式呈現：<br /><br /> x.x:x.x|  
|**product_version**|**Varchar (23)**|產品的版本。 以下列格式呈現：<br /><br /> x.x:x.x|  
|**調試**|**bit**|1 = 模組是已載入模組的偵錯版本。|  
|**patched**|**bit**|1 = 模組已修補。|  
|**發佈**|**bit**|1 = 模組是已載入模組的發行前版本。|  
|**private_build**|**bit**|1 = 模組是已載入模組的私用建置。|  
|**special_build**|**bit**|1 = 模組是已載入模組的特殊建置。|  
|**language**|**int**|模組之版本資訊的語言。|  
|**公司**|**nvarchar(256)**|建立模組的公司名稱。|  
|**description**|**nvarchar(256)**|模組的描述。|  
|**name**|**nvarchar(255)**|模組的名稱。 包含模組的完整路徑。|  
|**pdw_node_id**|**int**|**適用於**：[!INCLUDE[ssPDW](../../includes/sspdw-md.md)]<br /><br /> 此散發所在之節點的識別碼。|  
  
## <a name="permissions"></a>權限  
 需要伺服器的 VIEW SERVER STATE 權限。  
  
## <a name="see-also"></a>另請參閱  
 [動態管理檢視與函數 &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)   
 [SQL Server 作業系統相關的動態管理檢視 &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sql-server-operating-system-related-dynamic-management-views-transact-sql.md)  
  
  
