---
title: xp_cmdshell 伺服器組態選項
description: 了解 "xp_cmdshell" 選項。 了解其如何控制 SQL Server 是否可執行 "xp_cmdshell" 擴充預存程序。 了解開啟該功能的方法。
ms.prod: sql
ms.prod_service: high-availability
ms.reviewer: ''
ms.technology: configuration
ms.topic: conceptual
helpviewer_keywords:
- xp_cmdshell
ms.assetid: c147c9e1-b81d-49c8-b800-3019f4d86a13
author: markingmyname
ms.author: maghan
ms.custom: contperf-fy20q4
ms.date: 06/12/2020
ms.openlocfilehash: d12ea9c15f2a07fa6fdd4bc5dd5538fd1522d267
ms.sourcegitcommit: cb8e2ce950d8199470ff1259c9430f0560f0dc1d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2021
ms.locfileid: "97878831"
---
# <a name="xp_cmdshell-server-configuration-option"></a>xp_cmdshell 伺服器組態選項

 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

本文描述如何啟用 **xp_cmdshell** SQL Server 組態選項。 此選項可供系統管理員控制 [xp_cmdshell 擴充預存程序](../../relational-databases/system-stored-procedures/xp-cmdshell-transact-sql.md)是否可在系統上執行。 根據預設，新安裝的 **xp_cmdshell** 選項為停用。

啟用此選項前，請務必考量潛在安全性影響。

- 新開發的程式碼不應使用 **xp_cmdshell** 預存程序，且通常應該保持停用。
- 某些繼承應用程式則必須啟用 **xp_cmdshell**。 如果無法修改這些應用程式以避免使用這個預存程序，則可依照下述來加以啟用。

> [!NOTE]  
> 如果必須使用 **xp_cmdshell**，作為安全的最佳做法，建議只在需要該選項的實際工作期間才予以啟用。

如果需要啟用 **xp_cmdshell**，則可使用 [原則式管理](../../relational-databases/policy-based-management/administer-servers-by-using-policy-based-management.md)或執行 **sp_configure** 系統預存程序，如下列程式碼範例所示：  
  
``` sql
-- To allow advanced options to be changed.  
EXECUTE sp_configure 'show advanced options', 1;  
GO  
-- To update the currently configured value for advanced options.  
RECONFIGURE;  
GO  
-- To enable the feature.  
EXECUTE sp_configure 'xp_cmdshell', 1;  
GO  
-- To update the currently configured value for this feature.  
RECONFIGURE;  
GO  
```  
  
## <a name="next-steps"></a>後續步驟

- [xp_cmdshell 擴充預存程序](../../relational-databases/system-stored-procedures/xp-cmdshell-transact-sql.md)
- [伺服器組態選項 (SQL Server)](server-configuration-options-sql-server.md)
- [使用原則式管理來管理伺服器](../../relational-databases/policy-based-management/administer-servers-by-using-policy-based-management.md)  
