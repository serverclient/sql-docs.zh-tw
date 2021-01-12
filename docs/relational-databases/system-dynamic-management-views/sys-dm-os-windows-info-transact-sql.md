---
description: sys.dm_os_windows_info (Transact-SQL)
title: sys.dm_os_windows_info (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/30/2017
ms.prod: sql
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- dm_os_windows_info
- dm_os_windows_info_TSQL
- sys.dm_os_windows_info
- sys.dm_os_windows_info_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_os_windows_info dynamic management view
ms.assetid: adc81283-fdc2-46c0-bb48-abe82bbf2459
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 111e5b732dae02a5bc2bc8dcffebb3b563206f9c
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98094021"
---
# <a name="sysdm_os_windows_info-transact-sql"></a>sys.dm_os_windows_info (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  傳回一個顯示 Windows 作業系統版本資訊的資料列。  
  
  只適用于在 Windows 上執行的 SQL Server。 若要查看在非 Windows 主機（例如 Linux）上執行 SQL Server 的類似資訊，請使用 [sys.dm_os_host_info &#40;transact-sql&#41;](~/relational-databases/system-dynamic-management-views/sys-dm-os-host-info-transact-sql.md)。 
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**windows_release**|**nvarchar(256)**|若為 Windows，會傳回版本號碼。 如需值和描述的清單，請參閱 [ (Windows) 的作業系統版本 ](/windows/desktop/SysInfo/operating-system-version)。 不能是 NULL。|  
|**windows_service_pack_level**|**nvarchar(256)**| 若是 Windows，則會傳回 service pack 號碼。 不能是 NULL。 |  
|**windows_sku**|**int**|針對 Windows，會將 Windows 庫存單位 (SKU) 識別碼傳回。 如需 SKU 識別碼和描述的清單，請參閱 [GetProductInfo](/windows/win32/api/sysinfoapi/nf-sysinfoapi-getproductinfo)函式。 可為 Null。 |  
|**os_language_version**|**int**| 針對 Windows，會傳回作業系統 (LCID) 的 Windows 地區設定識別碼。 如需 LCID 值和描述的清單，請參閱 [Microsoft 指派的地區設定識別碼](/openspecs/windows_protocols/ms-lcid/a9eac961-e77d-41a6-90a5-ce1a8b0cdb9c)。 不能是 NULL。|  
  
  
## <a name="permissions"></a>權限  
預設會將 sys.dm_os_windows_info 的 SELECT 許可權授與 public 角色。 如果已撤銷，則需要伺服器的 VIEW SERVER STATE 許可權。  

## <a name="limitations-and-restrictions"></a>限制事項
若要查看在非 Windows 主機（例如 Linux）上執行的 SQL 資訊，請使用 [sys.dm_os_host_info &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-os-host-info-transact-sql.md)。 
  
## <a name="examples"></a>範例  
 下列範例會傳回 **sys.dm_os_windows_info** view 中的所有資料行。  
  
```  
SELECT windows_release, windows_service_pack_level, windows_sku, os_language_version  
FROM sys.dm_os_windows_info;  
```  
  
 範例結果集如下：  
  
 `windows_release  windows_service_pack_level   windows_sku   os_language_version`  
  
 `---------------  ---------------------------  ------------  -------------------`  
  
 `6.0              Service Pack 2                4            1033`  
  
## <a name="see-also"></a>另請參閱  
 [sys.dm_os_sys_info &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-os-sys-info-transact-sql.md)   
 [sys.dm_os_host_info](../../relational-databases/system-dynamic-management-views/sys-dm-os-host-info-transact-sql.md)  
  
