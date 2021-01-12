---
description: 'sys.dm_os_host_info (Transact-sql) '
title: sys.dm_os_host_info (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 02/10/2017
ms.prod: sql
ms.reviewer: ''
ms.technology: system-objects
ms.topic: conceptual
f1_keywords:
- sys.dm_os_host_info
- sys.dm_os_host_info_TSQL
- dm_os_host_info
- dm_os_host_info_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_os_host_info dynamic management view
ms.assetid: 9bb6ef86-957b-4ca1-ad20-ca2f8460a86d
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=sql-server-2017||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 41462fd558a30639342dc75be5bb68a44fcedf67
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98097558"
---
# <a name="sysdm_os_host_info-transact-sql"></a>sys.dm_os_host_info (Transact-sql) 
[!INCLUDE[SQL Server 2017](../../includes/applies-to-version/sqlserver2017.md)]

傳回一個顯示作業系統版本資訊的資料列。  
  
|資料行名稱 |資料類型 |描述 |  
|-----------------|---------------|-----------------|  
|**host_platform** |**nvarchar(256)** |作業系統的類型： Windows 或 Linux |
|**host_distribution** |**nvarchar(256)** |作業系統的描述。 |
|**host_release**|**nvarchar(256)**|[!INCLUDE[msCoName](../../includes/msconame-md.md)] Windows 作業系統版本 (版本號碼)。 如需值和描述的清單，請參閱 [ (Windows) 的作業系統版本 ](/windows/desktop/SysInfo/operating-system-version)。 <br> 若是 Linux，則會傳回空字串。 |  
|**host_service_pack_level**|**nvarchar(256)**|Windows 作業系統的 Service Pack 層級。 <br> 若是 Linux，則會傳回空字串。 |  
|**host_sku**|**int**|Windows 庫存單位 (SKU) 識別碼。 如需 SKU 識別碼和描述的清單，請參閱 [GetProductInfo](/windows/win32/api/sysinfoapi/nf-sysinfoapi-getproductinfo)函式。 可為 Null。 <br> 若是 Linux，則會傳回 Null。 |  
|**os_language_version**|**int**|作業系統的 Windows 地區設定識別碼 (LCID)。 如需 LCID 值和描述的清單，請參閱 [Microsoft 指派的地區設定識別碼](/openspecs/windows_protocols/ms-lcid/a9eac961-e77d-41a6-90a5-ce1a8b0cdb9c)。 不可以是 null。|  

## <a name="remarks"></a>備註  
此視圖類似于 [sys.dm_os_windows_info](../../relational-databases/system-dynamic-management-views/sys-dm-os-windows-info-transact-sql.md)，新增資料行來區分 Windows 和 Linux。
  
## <a name="security"></a>安全性  
  
### <a name="permissions"></a>權限  
`SELECT`依預設，的許可權 `sys.dm_os_host_info` 會授與 `public` 角色。 如果已撤銷， `VIEW SERVER STATE` 則需要伺服器的許可權。   
 
> [!CAUTION]
>  從版本 [!INCLUDE[ssSQLv14_md](../../includes/sssqlv14-md.md)] CTP 1.3 開始，第 [!INCLUDE[ssManStudioFull_md](../../includes/ssmanstudiofull-md.md)] 17 版需要的 `SELECT` 許可權，才能 `sys.dm_os_host_info` 連接到 [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] 。 如果 `SELECT` 撤銷許可權 `public` ，只有具有許可權的登入 `VIEW SERVER STATE` 可以與最新版本的 SSMS 連接。  (其他工具（例如） `sqlcmd.exe` 可在沒有許可權的情況下進行連接 `SELECT` `sys.dm_os_host_info` 。 ) 

  
## <a name="examples"></a>範例  
 下列範例會傳回 **sys.dm_os_host_info** view 中的所有資料行。  
  
```  
SELECT host_platform, host_distribution, host_release, 
    host_service_pack_level, host_sku, os_language_version  
FROM sys.dm_os_host_info;  
```  

以下是 Windows 上的範例結果集：
 
 |host_platform |host_distribution |host_release |host_service_pack_level |host_sku |os_language_version |
 |----- |----- |----- |----- |----- |----- |
 |Windows   |Windows Server 2012 R2 Standard    |6.3    |   |7  |1033 |  

以下是 Linux 上的範例結果集：
 
 |host_platform |host_distribution |host_release |host_service_pack_level |host_sku |os_language_version |
 |----- |----- |----- |----- |----- |----- |
 |Linux |Ubuntu |16.04  |   |NULL   |1033 |  

  
## <a name="see-also"></a>另請參閱  
 [sys.dm_os_sys_info &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-os-sys-info-transact-sql.md)   
 [sys.dm_os_windows_info (Transact-SQL)](../../relational-databases/system-dynamic-management-views/sys-dm-os-windows-info-transact-sql.md)  
