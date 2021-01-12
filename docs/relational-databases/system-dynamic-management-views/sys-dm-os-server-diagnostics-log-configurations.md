---
description: sys.dm_os_server_diagnostics_log_configurations
title: sys.dm_os_server_diagnostics_log_configurations |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.dm_os_server_diagnostics_log_configurations
- sys.dm_os_server_diagnostics_log_configurations_TSQL
- dm_os_server_diagnostics_log_configurations
- dm_os_server_diagnostics_log_configurations_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- dm_os_server_diagnostics_log_configurations
- sys.dm_os_server_diagnostics_log_configurations
ms.assetid: c09ea433-d283-4f83-af69-d458aad59217
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: d447261f24c503287dfa5a6503718cf804161194
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98099802"
---
# <a name="sysdm_os_server_diagnostics_log_configurations"></a>sys.dm_os_server_diagnostics_log_configurations
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  傳回一個資料列，並在其中包含 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 容錯移轉叢集診斷記錄檔目前的組態。 這些屬性設定可決定要開啟或關閉診斷記錄，以及記錄檔的位置、數目與大小。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|is_enabled|**bit**|指定是要開啟或關閉記錄。<br /><br /> 1 = 開啟診斷記錄功能<br /><br /> 0 = 關閉診斷記錄功能|  
|max_size|**int**|每個診斷記錄檔可成長的大小上限 (以 MB 為單位)。 預設值是 100 MB。|  
|max_files|**int**|在回收以用於新的診斷記錄檔之前，電腦上可儲存的診斷記錄檔數目上限。|  
|路徑|**nvarchar(260)**|指定診斷記錄檔位置的路徑。 預設位置是 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 容錯移轉叢集執行個體其安裝資料夾內的 \<\MSSQL\Log>。|  
  
## <a name="permissions"></a>權限  
 需要 SQL Server 容錯移轉叢集執行個體的 VIEW SERVER STATE 權限。  
  
## <a name="examples"></a>範例  
 下列範例會使用 sys.dm_os_server_diagnostics_log_configurations 傳回 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 容錯移轉診斷記錄檔的屬性設定。  
  
```  
SELECT <list of columns>  
FROM sys.dm_os_server_diagnostics_log_configurations;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
|IS_ENABLED|PATH|MAX_SIZE|MAX_FILES|  
|-----------------|----------|---------------|----------------|  
|1|\<C:\Program Files\Microsoft SQL Server\MSSQL13\MSSQL\Log>|10|10|  
  
## <a name="see-also"></a>另請參閱  
 [檢視及閱讀容錯移轉叢集執行個體診斷記錄檔](../../sql-server/failover-clusters/windows/view-and-read-failover-cluster-instance-diagnostics-log.md)  
  
  
