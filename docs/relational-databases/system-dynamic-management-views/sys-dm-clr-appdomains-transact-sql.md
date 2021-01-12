---
description: sys.dm_clr_appdomains (Transact-SQL)
title: sys.dm_clr_appdomains (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- dm_clr_appdomains
- sys.dm_clr_appdomains
- dm_clr_appdomains_TSQL
- sys.dm_clr_appdomains_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_clr_appdomains dynamic management dynamic management view
ms.assetid: 9fe0d4fd-950a-4274-a493-85e776278045
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: ae934fc3b9d8f7664e2542450ee1456298548c85
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98097758"
---
# <a name="sysdm_clr_appdomains-transact-sql"></a>sys.dm_clr_appdomains (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  針對伺服器中每一個應用程式定義域，各傳回一個資料列。 應用程式域 (**AppDomain**) 是通用語言執行時間中的結構 [!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[dnprdnshort](../../includes/dnprdnshort-md.md)] (CLR) ，也就是應用程式的隔離單位。 您可以使用此視圖來瞭解和疑難排解在中執行的 CLR 整合物件 [!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。  
  
 CLR 整合 Managed 資料庫物件有多種類型。 如需這些物件的一般資訊，請參閱 [使用 Common Language Runtime 建立資料庫物件 (CLR) 整合](../../relational-databases/clr-integration/database-objects/building-database-objects-with-common-language-runtime-clr-integration.md)。 只要執行這些物件， [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 就會建立一個 **AppDomain** ，讓它可以載入和執行所需的程式碼。 **Appdomain** 的隔離等級為每個擁有者每個資料庫一個 **appdomain** 。 也就是說，使用者擁有的所有 CLR 物件一律會在每個資料庫相同的 **AppDomain** (中，如果使用者在不同的資料庫中註冊 clr 資料庫物件，clr 資料庫物件將會在不同的應用程式域) 中執行。 在程式碼完成執行之後， **AppDomain** 不會終結。 而會將它快取到記憶體中，以供日後執行使用。 這樣可以提高執行效能。  
  
 如需詳細資訊，請參閱 [應用程式域](../../sql-server/what-s-new-in-sql-server-2016.md)。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**appdomain_address**|**varbinary(8)**|**AppDomain** 的位址。 使用者擁有的所有受管理資料庫物件一律會載入相同的 **AppDomain** 中。 您可以使用此資料行來查閱 **sys.dm_clr_loaded_assemblies** 中目前載入此 **AppDomain** 中的所有元件。|  
|**appdomain_id**|**int**|**AppDomain** 的識別碼。 每個 **AppDomain** 都有唯一的識別碼。|  
|**appdomain_name**|**Varchar (386)**|所指派之 **AppDomain** 的名稱 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。|  
|**creation_time**|**datetime**|建立 **AppDomain** 的時間。 由於 **appdomain** 會快取並重複使用以獲得較佳的效能，因此 **creation_time** 不一定是執行程式碼的時間。|  
|**db_id**|**int**|建立此 **AppDomain** 所在之資料庫的識別碼。 儲存在兩個不同資料庫中的程式碼無法共用一個 **AppDomain**。|  
|**user_id**|**int**|使用者的識別碼，其物件可在這個 **AppDomain** 中執行。|  
|**state**|**nvarchar(128)**|**AppDomain** 目前狀態的描述元。 任一 AppDomain 在從建立到刪除的期間，都可以有不同的狀態。 請參閱本主題的＜備註＞一節，以取得詳細資訊。|  
|**strong_refcount**|**int**|這個 **AppDomain** 的強式參考數目。 這會反映使用此 **AppDomain** 的目前執行中批次數目。 請注意，執行此視圖會建立 **強式 refcount**;即使目前沒有執行中的程式碼， **strong_refcount** 的值也會是1。|  
|**weak_refcount**|**int**|這個 **AppDomain** 的弱式參考數目。 這表示在 **AppDomain** 內快取的物件數目。 當您執行受管理的資料庫物件時，會將它快取 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 到 **AppDomain** 中，以供未來重複使用。 這樣可以提高執行效能。|  
|**cost**|**int**|**AppDomain** 的成本。 成本愈高，此 **AppDomain** 越可能在記憶體不足的壓力下卸載。 成本通常取決於需要多少記憶體才能重新建立此 **AppDomain**。|  
|**value**|**int**|**AppDomain** 的值。 值越低，此 **AppDomain** 越可能會在記憶體不足的壓力下卸載。 值通常取決於使用此 **AppDomain** 的連接或批次數目。|  
|**total_processor_time_ms**|**bigint**|自處理序啟動以來，在目前應用程式定義域中執行之所有執行緒所用的處理器總時間 (以毫秒為單位)。 這相當於 **MonitoringTotalProcessorTime**。|  
|**total_allocated_memory_kb**|**bigint**|自應用程式定義域建立以來，它所進行的所有記憶體配置總大小 (以 KB 為單位)，不減去已回收的記憶體。 這相當於 **MonitoringTotalAllocatedMemorySize**。|  
|**survived_memory_kb**|**bigint**|在上次完整區塊回收中存活下來且已知為目前應用程式定義域所參考的 KB 數。 這相當於 **MonitoringSurvivedMemorySize**。|  
  
## <a name="remarks"></a>備註  
 **Dm_clr_appdomains. appdomain_address** 和 **dm_clr_loaded_assemblies. appdomain_address** 之間有一對一的關聯性。  
  
 下表列出可能的 **狀態** 值、其描述，以及它們在 **AppDomain** 生命週期中的發生時機。 您可以使用這項資訊來追蹤 **AppDomain** 的生命週期，並監看是否有可疑或重複的 **AppDomain** 實例卸載，而不需要剖析 Windows 事件記錄檔。  
  
## <a name="appdomain-initialization"></a>AppDomain 初始化  
  
|州|描述|  
|-----------|-----------------|  
|E_APPDOMAIN_CREATING|正在建立 **AppDomain** 。|  
  
## <a name="appdomain-usage"></a>AppDomain 使用方式  
  
|州|描述|  
|-----------|-----------------|  
|E_APPDOMAIN_SHARED|執行時間 **AppDomain** 可供多個使用者使用。|  
|E_APPDOMAIN_SINGLEUSER|**AppDomain** 已準備好在 DDL 作業中使用。 這些與 E_APPDOMAIN_SHARED 不同，因為共用的 AppDomains 是用於 CLR 整合執行，與 DDL 作業相反。 這類的 AppDomains 會與其他的並行作業隔離。|  
|E_APPDOMAIN_DOOMED|**AppDomain** 已排程要卸載，但目前正在執行中的執行緒。|  
  
## <a name="appdomain-cleanup"></a>AppDomain 清除  
  
|州|描述|  
|-----------|-----------------|  
|E_APPDOMAIN_UNLOADING|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 已要求 CLR 卸載 **AppDomain**，通常是因為包含 managed 資料庫物件的元件已經改變或卸載。|  
|E_APPDOMAIN_UNLOADED|CLR 已卸載 **AppDomain**。 這通常是因為 **ThreadAbort**、 **OutOfMemory** 或使用者程式碼中未處理的例外狀況所造成的擴大程式結果。|  
|E_APPDOMAIN_ENQUEUE_DESTROY|**AppDomain** 已在 CLR 中卸載，並設定為由終結 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。|  
|E_APPDOMAIN_DESTROY|**AppDomain** 正在終結的進程中 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。|  
|E_APPDOMAIN_ZOMBIE|已終結 **appdomain** [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ，但是並非所有 **appdomain** 的參考都已清除。|  
  
## <a name="permissions"></a>權限  
 需要資料庫的 VIEW SERVER STATE 權限。  
  
## <a name="examples"></a>範例  
 下列範例示範如何針對指定的元件查看 **AppDomain** 的詳細資料：  
  
```  
select appdomain_id, creation_time, db_id, user_id, state  
from sys.dm_clr_appdomains a  
where appdomain_address =   
(select appdomain_address   
 from sys.dm_clr_loaded_assemblies  
   where assembly_id = 500);  
```  
  
 下列範例顯示如何在指定的 **AppDomain** 中查看所有元件：  
  
```  
select a.name, a.assembly_id, a.permission_set_desc, a.is_visible, a.create_date, l.load_time   
from sys.dm_clr_loaded_assemblies as l   
inner join sys.assemblies as a  
on l.assembly_id = a.assembly_id  
where l.appdomain_address =   
(select appdomain_address   
from sys.dm_clr_appdomains  
where appdomain_id = 15);  
```  
  
## <a name="see-also"></a>另請參閱  
 [sys.dm_clr_loaded_assemblies &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-clr-loaded-assemblies-transact-sql.md)   
 [Common Language Runtime 相關的動態管理檢視 &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/common-language-runtime-related-dynamic-management-views-transact-sql.md)  
  
