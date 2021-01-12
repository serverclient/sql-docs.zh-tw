---
description: sys.dm_server_services (Transact-SQL)
title: sys.dm_server_services (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 01/07/2018
ms.prod: sql
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- dm_server_services
- sys.dm_server_services
- sys.dm_server_services_TSQL
- dm_server_services_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_server_services dynamic management view
ms.assetid: 3f0defd0-478d-4e7f-96be-8795c9de4e3f
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 2133cd83a96a6db171f078c89dec887aec0e0646
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98095072"
---
# <a name="sysdm_server_services-transact-sql"></a>sys.dm_server_services (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  傳回目前實例中 SQL Server、全文檢索 SQL Server Launchpad 服務 (SQL Server 2017 +) 和 SQL Server Agent 服務的相關資訊 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。 使用此動態管理檢視可報告有關這些服務的狀態資訊。  
  
 
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|servicename|**nvarchar(256)**|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]、全文檢索或 SQL Server Agent 服務的名稱。 不可以是 null。|  
|startup_type|**int**|表示服務的啟動模式。 以下是可能的值及其對應的描述。<br /><br /> 0：其他<br />1：其他<br />2：自動<br />3：手動<br />4：已停用<br /><br /> 可為 Null。|  
|startup_type_desc|**nvarchar(256)**|說明服務的啟動模式。 以下是可能的值及其對應的描述。<br /><br /> 其他：其他 (boot 啟動) <br />其他：其他 (系統啟動) <br />自動：自動啟動<br />手動：需求開始<br />停用：已停用<br /><br /> 不可以是 null。|  
|status|**int**|指出服務目前的狀態。 以下是可能的值及其對應的描述。<br /><br /> 1：已停止<br />2：其他 (開始暫止) <br />3：其他 (停止暫止) <br />4：正在執行<br />5：其他 (繼續暫止) <br />6：其他 (暫停暫止) <br />7：已暫停<br /><br /> 可為 Null。|  
|status_desc|**nvarchar(256)**|說明服務目前的狀態。 以下是可能的值及其對應的描述。<br /><br /> 已停止：服務已停止。<br />其他 (啟動作業暫止) ：服務正在啟動中。<br />其他 (停止作業暫止) ：服務正在停止中。<br />正在執行：服務正在執行。<br />其他 (繼續作業暫止) ：服務處於暫止狀態。<br />其他 (暫停暫止) ：服務正在暫停。<br />已暫停：服務已暫停。<br /><br /> 不可以是 null。|  
|process_id|**int**|服務的處理序識別碼。 不可以是 null。|  
|last_startup_time|**datetimeoffset(7)**|服務上次啟動的日期和時間。 可為 Null。|  
|service_account|**nvarchar(256)**|已獲授權可控制服務的帳戶。 這個帳戶可以啟動或停止服務，或修改服務屬性。 不可以是 null。|  
|filename|**nvarchar(256)**|服務可執行檔的路徑和檔案名稱。 不可以是 null。|  
|is_clustered|**Nvarchar (1)**|指出服務是否安裝為叢集伺服器的資源。 不可以是 null。|  
|cluster_nodename|**nvarchar(256)**|安裝服務所在之叢集節點的名稱。 可為 Null。|
|instant_file_initialization_enabled|**Nvarchar (1)**|指定是否啟用服務的立即檔案初始化 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 。<br /><br />Y = 啟用服務的立即檔案初始化。<br /><br />N = 服務的立即檔案初始化已停用。<br /><br /> 可為 Null。<br /><br /> **注意：** 不會套用至其他服務，例如 SQL Server Agent。<br /><br /> **適用于：** [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 從 SP4 開始 ([!INCLUDE[sssql11](../../includes/sssql11-md.md)] ，以及 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] SP1 和更新版本的) 。|  

## <a name="security"></a>安全性  
  
### <a name="permissions"></a>權限  
 需要伺服器的 `VIEW SERVER STATE` 權限。  
  
## <a name="see-also"></a>另請參閱  
 [sys.dm_server_registry &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-server-registry-transact-sql.md)  
  
