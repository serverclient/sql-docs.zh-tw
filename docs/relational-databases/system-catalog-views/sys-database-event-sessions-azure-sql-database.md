---
description: sys.database_event_sessions (Azure SQL Database)
title: sys.database_event_sessions (Azure SQL Database) Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.service: sql-database
ms.reviewer: ''
ms.topic: language-reference
ms.assetid: 02c2cd71-d35e-4d4c-b844-92b240f768f4
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: = azuresqldb-current
ms.openlocfilehash: 194b8e87700980c6af64985dc2b53b0d916bc01e
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98098363"
---
# <a name="sysdatabase_event_sessions-azure-sql-database"></a>sys.database_event_sessions (Azure SQL Database)
[!INCLUDE[Azure SQL Database Azure SQL Managed Instance](../../includes/applies-to-version/asdb-asdbmi.md)]

  列出目前資料庫中存在的所有事件會話定義 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 。  
  
> [!NOTE]
>  名稱類似的目錄檢視 `sys.server_event_sessions` 只適用于 [!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。  
  
||  
|-|  
|**適用** 于：和，適用于 [!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)] 任何較新版本。|  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|event_session_id|**int**|事件工作階段的唯一識別碼。 不可為 Null。|  
|NAME|**sysname**|用來識別事件工作階段的使用者定義名稱。 名稱是唯一的。 不可為 Null。|  
|event_retention_mode|**nchar(1)**|判斷如何處理事件遺失。 預設值為 S。不可設為 Null。 為下列其中一項：<br /><br /> 國 對應至 event_retention_mode_desc = ALLOW_SINGLE_EVENT_LOSS<br /><br /> M. 對應至 event_retention_mode_desc = ALLOW_MULTIPLE_EVENT_LOSS<br /><br /> N. 對應至 event_retention_mode_desc = NO_EVENT_LOSS|  
|event_retention_mode_desc|**sysname**|描述如何處理事件遺失。 預設值為 ALLOW_SINGLE_EVENT_LOSS。 不可為 Null。 為下列其中一項：<br /><br /> ALLOW_SINGLE_EVENT_LOSS。 事件可能會從工作階段遺失。 只有當所有事件緩衝區已滿時，才會卸除單一事件。 當緩衝區已滿時遺失單一事件會允許可接受的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 效能特性，同時也可讓處理之事件資料流中的遺失情形降至最低。<br /><br /> ALLOW_MULTIPLE_EVENT_LOSS。 完整事件緩衝區可能會從工作階段遺失。 遺失的事件數目取決於配置給工作階段的記憶體大小、記憶體的分割及緩衝區中的事件大小。 當事件緩衝區被快速填滿時，這個選項可對伺服器的效能影響降至最低。 但是，可能會從工作階段中遺失大量的事件數目。<br /><br /> NO_EVENT_LOSS。 不允許事件遺失。 這個選項可確保將會保留所有引發的事件。 使用這個選項可強制引發事件的所有工作等候到事件緩衝區中有可用的空間為止。 這樣可以在事件工作階段為使用中時偵測效能降低的問題。|  
|max_dispatch_latency|**int**|指定事件在對工作階段目標提供服務之前，將於記憶體內緩衝處理的時間量 (以毫秒為單位)。 有效的值是從 1 到 2147483648 及 -1。 -1 的值表示分派延遲為無限大。 可為 Null。|  
|max_memory|**int**|為了事件緩衝處理而配置給工作階段的記憶體數量。 預設值為 4 MB。 可為 Null。|  
|max_event_size|**int**|保留給不適合事件工作階段緩衝區之事件的記憶體數量。 如果 max_event_size 超出計算而來的緩衝區大小，會將 max_event_size 的兩個額外緩衝區配置給事件工作階段。 可為 Null。|  
|memory_partition_mode|**nchar(1)**|在記憶體中建立事件緩衝區的位置。 預設的分割模式為 G。不可設為 Null。 memory_partition_mode 是下列其中一項：<br /><br /> G - NONE<br /><br /> C - PER_CPU<br /><br /> N - PER_NODE|  
|memory_partition_mode_desc|**sysname**|預設值是 NONE。 不可為 Null。 為下列其中一項：<br /><br /> NONE。 SQL Server 執行個體內會建立單一組緩衝區。<br /><br /> PER_CPU。 為每個 CPU 建立一組緩衝區。<br /><br /> PER_NODE。 會針對每一個非統一記憶體存取 (NUMA) 節點建立一組緩衝區。|  
|track_causality|**bit**|啟用或停用因果追蹤。 如果設定為 1 (ON)，會啟用追蹤，而且不同伺服器連接上的相關事件會彼此相互關聯。 預設值是 0 (OFF)。 不可為 Null。|  
|startup_state|**bit**|可決定當伺服器啟動時，是否要自動啟動工作階段的值。 預設值是 0。 不可為 Null。 是下列其中一項：<br /><br /> 0 (OFF)。 當伺服器啟動時，此工作階段不會啟動。<br /><br /> 1 (ON)。 當伺服器啟動時，會啟動此事件工作階段。|  
  
## <a name="permissions"></a>權限  
 需要伺服器的 VIEW SERVER STATE 權限。  
  
  
