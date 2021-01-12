---
description: sys.dm_clr_properties (Transact-SQL)
title: sys.dm_clr_properties (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.dm_clr_properties
- sys.dm_clr_properties_TSQL
- dm_clr_properties_TSQL
- dm_clr_properties
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_clr_properties dynamic management view
ms.assetid: 220d062f-d117-46e7-a448-06fe48db8163
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 34dfbfbecf82df79520fc5a530bcb8c18c544c77
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98095267"
---
# <a name="sysdm_clr_properties-transact-sql"></a>sys.dm_clr_properties (Transact-SQL)
[!INCLUDE [sql-asdbmi-pdw](../../includes/applies-to-version/sql-asdbmi-pdw.md)]

  針對與 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Common Language Runtime (CLR) 整合 (包括主控 CLR 的版本和狀態) 相關的每個屬性各傳回一個資料列。 裝載 CLR 的初始化方式是執行 [CREATE assembly](../../t-sql/statements/create-assembly-transact-sql.md)、 [ALTER ASSEMBLY](../../t-sql/statements/alter-assembly-transact-sql.md)或 [DROP assembly](../../t-sql/statements/drop-assembly-transact-sql.md) 語句，或是執行任何 CLR 常式、類型或觸發程式。 **Sys.dm_clr_properties** view 不會指定是否已在伺服器上啟用使用者 clr 程式碼的執行。 使用 [sp_configure](../../relational-databases/system-stored-procedures/sp-configure-transact-sql.md) 預存程式，並將 [ [CLR 已啟用](../../database-engine/configure-windows/clr-enabled-server-configuration-option.md) ] 選項設定為1，即可啟用使用者 CLR 程式碼的執行。  
  
 **Sys.dm_clr_properties** 視圖包含 [**名稱**] 和 [**值**] 資料行。 這個檢視的每個資料列提供了有關主控 CLR 屬性的詳細資料。 請使用此檢視來收集有關主控 CLR 的資訊，例如 CLR 安裝目錄、CLR 版本，以及主控 CLR 目前的狀態。 此檢視可以幫助您判斷 CLR 整合程式碼是否因為伺服器電腦的 CLR 安裝發生問題而無法運作。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**name**|**nvarchar(128)**|屬性的名稱。|  
|**value**|**nvarchar(128)**|屬性的值。|  
  
## <a name="properties"></a>屬性  
 **Directory** 屬性工作表示伺服器上安裝 .NET Framework 的目錄。 伺服器電腦上可能有多個 .NET Framework 安裝，而這個值可以指出 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 正在使用哪一個安裝。  
  
 **Version** 屬性工作表示伺服器上 .NET Framework 和 hosted CLR 的版本。  
  
 **Sys.dm_clr_properties** 動態 managed view 可以針對 **state** 屬性傳回六個不同的值，這會反映裝載 clr 的狀態 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。 分別是：  
  
-   未載入 Mscoree。  
  
-   已載入 Mscoree。  
  
-   已利用 Mscoree 鎖定 CLR 版本。  
  
-   已初始化 CLR。  
  
-   CLR 初始化已永久失敗。  
  
-   CLR 已停止。  
  
 **Mscoree.dll 未載入**，而 **mscoree.dll 載入** 的狀態顯示伺服器啟動時主控 CLR 初始化的進展，而且不太可能出現。  
  
 **具有 mscoree.dll 狀態的鎖定 CLR 版本** 可能會在未使用裝載 CLR 的情況下出現，因此，它尚未初始化。 第一次執行 DDL 語句時，會初始化 hosted CLR (例如 [CREATE ASSEMBLY &#40;transact-sql&#41;](../../t-sql/statements/create-assembly-transact-sql.md)) 或 managed 資料庫物件。  
  
 **CLR 已初始化** 狀態，表示已成功初始化主控 CLR。 請注意，這並不會指出是否已啟用使用者 CLR 程式碼執行。 如果第一次啟用使用者 CLR 程式碼的執行，然後使用 [!INCLUDE[tsql](../../includes/tsql-md.md)] [sp_configure](../../relational-databases/system-stored-procedures/sp-configure-transact-sql.md) 預存程式來停用，則狀態值仍會 **初始化為 CLR**。  
  
 **CLR 初始化永久失敗** 狀態表示託管 CLR 初始化失敗。 記憶體不足是可能的原因之一，或者也可能是因為 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 和 CLR 之間主控交握失敗所致。 在這種情況下會發生錯誤訊息 6512 或 6513。  
  
 只有當處於關機的程式時，才會看到 **CLR 已停止狀態** [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。  
  
## <a name="remarks"></a>備註  
 此視圖的屬性和值可能會在未來的版本中變更， [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 因為 CLR 整合功能的增強功能。  
  
## <a name="permissions"></a>權限  
  
在上 [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] ，需要 `VIEW SERVER STATE` 許可權。   
在 SQL Database Basic、S0 和 S1 服務目標上，以及針對彈性集區中的資料庫，則 `Server admin` `Azure Active Directory admin` 需要或帳戶。 在所有其他 SQL Database 服務目標上， `VIEW DATABASE STATE` 資料庫中都需要有許可權。   

## <a name="examples"></a>範例  
 下列範例會擷取有關主控 CLR 的資訊：  
  
```  
SELECT name, value   
FROM sys.dm_clr_properties;  
```  
  
## <a name="see-also"></a>另請參閱  
 [動態管理檢視與函數 &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)   
 [Common Language Runtime 相關的動態管理檢視 &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/common-language-runtime-related-dynamic-management-views-transact-sql.md)  
  
  
