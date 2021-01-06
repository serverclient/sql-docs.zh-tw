---
title: 已中止的資料庫引擎功能
description: 了解 SQL Server 2019 (15.x)、SQL Server 2016 (13.x) 和舊版中已停止的資料庫引擎功能和功能。
ms.custom: seo-lt-2019
ms.date: 07/22/2020
ms.prod: sql
ms.prod_service: high-availability
ms.reviewer: ''
ms.technology: release-landing
ms.topic: conceptual
helpviewer_keywords:
- VIA protocol
- unsupported features [SQL Server]
- SQL Mail
- discontinued functionality [SQL Server]
- RESTORE WITH DBO_ONLY
- BACKUP WITH PASSWORD
- user instances enabled
- BACKUP WITH MEDIAPASSWORD
- AWE
- SQL-DMO
- '*= and =*'
- 80 compatibility levels
- COMPUTE BY
- user instance timeout
- sp_dropalias
- COMPUTE
- SSL
- WITH APPEND
- sys.database_principal_aliases
- sp_dboption
- DATABASEPROPERTY
- FASTFIRSTROW hint
- SET DISABLE_DEF_CNST_CHK
ms.assetid: d686cdf0-d11d-4dba-9ec8-de1a5f189f25
author: MikeRayMSFT
ms.author: mikeray
monikerRange: '>= sql-server-linux-2017  || >= sql-server-2016'
ms.openlocfilehash: 705cd4be70b642a6436bdecf58ae8c18adaff281
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97643998"
---
# <a name="discontinued-database-engine-functionality-in-sql-server"></a>SQL Server 中已中止的資料庫引擎功能
[!INCLUDE[sqlserver](../includes/applies-to-version/sqlserver.md)]

  本主題描述 [!INCLUDE[ssDE](../includes/ssde-md.md)] 中不再可用的 [!INCLUDE[ssCurrent](../includes/ssnoversion-md.md)]功能。  

## <a name="discontinued-features-in-sssqlv15"></a>[!INCLUDE[ssSQLv15](../includes/sssqlv15-md.md)] 中已中止的功能  

- 下列資料庫範圍設定選項已中止：

  - `DISABLE_BATCH_MODE_ADAPTIVE_JOIN`
  - `DISABLE_BATCH_MODE_MEMORY_GRANT_FEEDBACK`
  - `DISABLE_INTERLEAVED_EXECUTION_TVF`

如需目前的設定選項，請參閱 [ALTER DATABASE SCOPED CONFIGURATION (Transact-SQL)](../t-sql/statements/alter-database-scoped-configuration-transact-sql.md)。

>[!NOTE]
>[!INCLUDE[ssSQLv14](../includes/sssqlv14-md.md)] 中沒有任何已中止的功能。

## <a name="discontinued-features-in-sssql15"></a>[!INCLUDE[ssSQL15](../includes/sssql15-md.md)] 中已中止的功能

- [!INCLUDE[ssSQL15](../includes/sssql15-md.md)] 是 64 位元應用程式。 即使有些元素是以 32 位元元件的形式執行，32 位元安裝已然中止。  

- 已停止相容性層級 90。 如需詳細資訊，請參閱 [ALTER DATABASE 相容性層級 &#40;Transact-SQL&#41;](../t-sql/statements/alter-database-transact-sql-compatibility-level.md)。  

- ActiveX 子系統已停止。 請改用命令列或 PowerShell 指令碼。

- 啟動參數 **-h** 與 **-g**。 如需詳細資訊，請參閱 [Database Engine Service Startup Options](/previous-versions/sql/2014/database-engine/configure-windows/database-engine-service-startup-options?view=sql-server-2014&preserve-view=true)。

- 已中止安全通訊端層 (SSL) 加密。 請改用傳輸層安全性 (TLS)。 如需詳細資訊，請參閱[啟用資料庫引擎的加密連線](../database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine.md)。

## <a name="previous-versions"></a>舊版

- [SQL Server 2014 中已停止的 Database Engine 功能](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016?view=sql-server-2014&preserve-view=true)

### <a name="see-also"></a>另請參閱

- [SQL Server 2019 中已淘汰的資料庫引擎功能](deprecated-database-engine-features-in-sql-server-version-15.md)
- [SQL Server 2017 中已淘汰的資料庫引擎功能](deprecated-database-engine-features-in-sql-server-2017.md)
- [SQL Server 2016 中已淘汰的資料庫引擎功能](../database-engine/deprecated-database-engine-features-in-sql-server-2016.md)
- [SQL Server 2019 資料庫引擎功能的重大變更](breaking-changes-to-database-engine-features-in-sql-server-version-15.md)
- [SQL Server 2017 資料庫引擎功能的重大變更](breaking-changes-to-database-engine-features-in-sql-server-2017.md)
- [SQL Server 2016 資料庫引擎功能的重大變更](breaking-changes-to-database-engine-features-in-sql-server-2016.md)
- [SQL Server 複寫中的已淘汰功能](../relational-databases/replication/deprecated-features-in-sql-server-replication.md)