---
title: 資料庫引擎：重大變更 | Microsoft Docs
titleSuffix: SQL Server 2017
description: 了解可能會中斷應用程式、指令碼或功能 (以舊版 SQL Server 為基礎) 的變更。
ms.custom: seo-lt-2019
ms.date: 07/22/2020
ms.prod: sql
ms.prod_service: high-availability
ms.reviewer: ''
ms.technology: release-landing
ms.topic: conceptual
helpviewer_keywords:
- breaking changes 2017 [SQL Server]
ms.assetid: ''
author: MikeRayMSFT
ms.author: mikeray
monikerRange: '>=sql-server-2017||>=sql-server-linux-2017'
ms.openlocfilehash: affc62db57e4ae08e1886af0fef4ab537bcda86b
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97665881"
---
# <a name="breaking-changes-to-database-engine-features-in-sssqlv14-md"></a>[!INCLUDE[sssqlv14-md](../includes/sssqlv14-md.md)] 資料庫引擎功能的重大變更
[!INCLUDE[SQL Server 2017](../includes/applies-to-version/sqlserver2017.md)]


  此主題描述 [!INCLUDE[sssqlv14-md](../includes/sssqlv14-md.md)] [!INCLUDE[ssDE](../includes/ssde-md.md)] 中的重大變更。 這些變更可能會中斷以舊版 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]為根據的應用程式、指令碼或功能。 當您升級時可能會遇到這些問題。  
  
## <a name="breaking-changes-in-sssqlv14-md-ssde"></a>[!INCLUDE[sssqlv14-md](../includes/sssqlv14-md.md)] [!INCLUDE[ssDE](../includes/ssde-md.md)] 中的重大變更  
  
-  CLR 使用 .NET Framework 中的程式碼存取安全性 (CAS)，而這不再作為安全性界限受支援。 從 [!INCLUDE[sssqlv14-md](../includes/sssqlv14-md.md)][!INCLUDE[ssDE](../includes/ssde-md.md)] 開始，引進稱為 `clr strict security` 的 `sp_configure` 選項，來增強 CLR 組件的安全性。 預設啟用 clr strict security，並將 `SAFE` 和 `EXTERNAL_ACCESS` CLR 組件視同標示了 `UNSAFE`。 可以基於回溯相容性停用 `clr strict security` 選項，但不建議這麼做。 停用 `clr strict security` 時，使用 `PERMISSION_SET = SAFE` 所建立的 CLR 組件可能可以存取外部系統資源、呼叫 Unmanaged 程式碼，以及需要 **sysadmin** 權限。 啟用嚴格安全性之後，將無法載入任何未簽署的組件。 此外，如果資料庫有 `SAFE` 或 `EXTERNAL_ACCESS` 組件，`RESTORE` 或 `ATTACH DATABASE` 陳述式可以完成，但可能無法載入組件。   
  若要載入組件，您必須改變或置放並重新建立每個組件，以便使用憑證或非對稱金鑰簽署，該金鑰有具有伺服器 `UNSAFE ASSEMBLY` 權限的對應登入。 如需詳細資訊，請參閱 [CLR 嚴格安全性](../database-engine/configure-windows/clr-strict-security.md)。 
  
-  [!INCLUDE[ssSQL15](../includes/sssql15-md.md)] 已淘汰 MD2、MD4、MD5、SHA 和 SHA1 演算法。 在 [!INCLUDE[ssSQL15](../includes/sssql15-md.md)] 之前，會使用 SHA1 建立自我簽署憑證。 從 [!INCLUDE[ssSQL17](../includes/sssql17-md.md)] 開始，則使用 SHA2_256 建立自我簽署憑證。

## <a name="previous-versions"></a><a name="previous-versions"></a> 舊版  

- [SQL Server 2016 中對於 Database Engine 的重大變更](../database-engine/breaking-changes-to-database-engine-features-in-sql-server-2016.md)

- [SQL Server 2014 中對於 Database Engine 的重大變更](/previous-versions/sql/2014/database-engine/breaking-changes-to-database-engine-features-in-sql-server-2016?view=sql-server-2014&preserve-view=true#SQL14)

#### <a name="archived-documentation-for-very-old-versions-of-sql-server"></a>非常舊版的 SQL Server 封存文件

[!INCLUDE[Archived documentation for very old versions of SQL Server](../includes/paragraph-content/previous-versions-archive-documentation-sql-server.md)]

## <a name="see-also"></a>另請參閱  
 [SQL Server 2016 中已被取代的 Database Engine 功能](../database-engine/deprecated-database-engine-features-in-sql-server-2016.md)   
 [SQL Server 2016 中已停止的 Database Engine 功能](./discontinued-database-engine-functionality-in-sql-server.md)   
 [SQL Server Database Engine 回溯相容性](./discontinued-database-engine-functionality-in-sql-server.md)   
 [ALTER DATABASE 相容性層級 &#40;Transact-SQL&#41;](../t-sql/statements/alter-database-transact-sql-compatibility-level.md)  
  
