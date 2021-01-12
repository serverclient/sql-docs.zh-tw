---
description: sys.dm_clr_loaded_assemblies (Transact-SQL)
title: sys.dm_clr_loaded_assemblies (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 08/09/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- dm_clr_loaded_assemblies
- sys.dm_clr_loaded_assemblies_TSQL
- dm_clr_loaded_assemblies_TSQL
- sys.dm_clr_loaded_assemblies
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_clr_loaded_assemblies dynamic management view
ms.assetid: 8523d8db-d8a0-4b1f-ae19-6705d633e0a6
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: d132a0389ca636dbc24afe016b98389021d8d848
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98099948"
---
# <a name="sysdm_clr_loaded_assemblies-transact-sql"></a>sys.dm_clr_loaded_assemblies (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  針對已載入至伺服器位址空間的每一個 Managed 使用者組件，各傳回一個資料列。 您可以使用此視圖來瞭解和疑難排解在中執行的 CLR integration managed 資料庫物件 [!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。  
  
 組件是用來在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中定義及部署 Managed 資料庫物件的 Managed 程式碼 DLL 檔案。 每當使用者執行其中一個 Managed 資料庫物件時，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 和 CLR 都會載入包含 Managed 資料庫物件定義的組件 (及其參考)。 為了提升效能，組件在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中會保持載入的狀態，使您日後不需要重新載入組件，就可以直接呼叫包含在組件中的 Managed 資料庫物件。 在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 承受記憶體不足的壓力之前，組件都不會卸載。 如需元件和 CLR 整合的詳細資訊，請參閱 [Clr 主控環境](../../relational-databases/clr-integration/clr-integration-architecture-clr-hosted-environment.md)。 如需受管理資料庫物件的詳細資訊，請參閱 [使用 Common Language Runtime 建立資料庫物件 &#40;CLR&#41; 整合](../../relational-databases/clr-integration/database-objects/building-database-objects-with-common-language-runtime-clr-integration.md)。  

  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**assembly_id**|**int**|載入組件的識別碼。 **Assembly_id** 可以用來查閱 sys. 元件中有關元件的詳細資訊 [&#40;transact-sql&#41;](../../relational-databases/system-catalog-views/sys-assemblies-transact-sql.md)類別目錄檢視。 請注意， [!INCLUDE[tsql](../../includes/tsql-md.md)] [sys. 元件](../../relational-databases/system-catalog-views/sys-assemblies-transact-sql.md) 類別目錄只會顯示目前資料庫中的元件。 **Sqs.dm_clr_loaded_assemblies** 視圖會顯示伺服器上所有載入的元件。|  
|**appdomain_address**|**int**|載入元件 (**AppDomain**) 應用程式域的位址。 單一使用者所擁有的所有元件一律會載入相同的 **AppDomain** 中。 **Appdomain_address** 可以用來查閱 [Sys.dm_clr_appdomains](../../relational-databases/system-dynamic-management-views/sys-dm-clr-appdomains-transact-sql.md)視圖中 **appdomain** 的詳細資訊。|  
|**load_time**|**datetime**|載入組件的時間。 請注意，元件會保持載入狀態，直到 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 記憶體不足的壓力，並卸載 **AppDomain** 為止。 您可以監視 **load_time** ，以瞭解記憶體不足的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 壓力和卸載 **AppDomain** 的頻率。|  
  
## <a name="permissions"></a>權限  
 需要伺服器的 VIEW SERVER STATE 權限。  
  
## <a name="remarks"></a>備註  
 **Dm_clr_loaded_assemblies. appdomain_address** view 與 **dm_clr_appdomains. appdomain_address** 具有多對一關聯性。 **Dm_clr_loaded_assemblies. assembly_id** view 與 **sys. assembly_id** 有一對多的關聯性。  
  
## <a name="examples"></a>範例  
 下列範例顯示如何檢視目前已載入現用資料庫中所有組件的詳細資料。  
  
```  
 SELECT a.name, a.assembly_id, a.permission_set_desc, a.is_visible, a.create_date, l.load_time   
FROM sys.dm_clr_loaded_assemblies AS l   
INNER JOIN sys.assemblies AS a  
ON l.assembly_id = a.assembly_id;  
```  
  
 下列範例顯示如何查看載入指定元件之 **AppDomain** 的詳細資料。  
  
```  
SELECT appdomain_id, creation_time, db_id, user_id, state  
FROM sys.dm_clr_appdomains AS a  
WHERE appdomain_address =   
(SELECT appdomain_address   
 FROM sys.dm_clr_loaded_assemblies  
 WHERE assembly_id = 555);  
```  
  
## <a name="see-also"></a>另請參閱  
 [Common Language Runtime 相關的動態管理檢視 &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/common-language-runtime-related-dynamic-management-views-transact-sql.md)  
  
  
