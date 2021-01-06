---
title: 伺服器組態選項 (SQL Server)
description: 了解如何管理及最佳化 SQL Server 資源。 檢視可用的組態選項、可能的設定、預設值和重新啟動需求。
ms.prod: sql
ms.prod_service: high-availability
ms.technology: configuration
ms.topic: conceptual
keywords:
- 伺服器組態 (SQL Server)
helpviewer_keywords:
- surface area configuration [SQL Server], sp_configure
- configuration options [SQL Server], when take effect
- server management [SQL Server], configuration options
- SQL Server Management Studio [SQL Server], servers
- servers [SQL Server], configuring
- configuration options [SQL Server], setting
- options [SQL Server], configuration
- RECONFIGURE statement
- performance [SQL Server], servers
- configuration options [SQL Server]
- RECONFIGURE WITH OVERRIDE statement
- SQL Server, configuring
- sp_configure
- stored procedures [SQL Server], configuration options
- server configuration [SQL Server]
- administering SQL Server, configuration options
author: markingmyname
ms.author: maghan
ms.reviewer: ''
ms.custom: ''
ms.date: 07/20/2020
ms.openlocfilehash: bcadbc294414b2fe992d3864be240a1998a5e477
ms.sourcegitcommit: a81823f20262227454c0b5ce9c8ac607aaf537e2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2020
ms.locfileid: "97684219"
---
# <a name="server-configuration-options-sql-server"></a>伺服器組態選項 (SQL Server)
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

您可以使用 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 或 sp_configure 系統預存程序，透過組態選項來管理及最佳化 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 資源。 最常使用的伺服器組態選項可以透過 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)]來使用，而所有組態選項都可以透過 sp_configure 來存取。 在設定這些選項前，請仔細考慮這些選項對系統所造成的效果。 如需詳細資訊，請參閱[檢視或變更伺服器屬性 &#40;SQL Server&#41;](../../database-engine/configure-windows/view-or-change-server-properties-sql-server.md)。

>**重要！！** 只有有經驗的資料庫管理員或通過認證的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 技術人員，才可變更進階選項。

## <a name="categories-of-configuration-options"></a>設定選項的範疇

設定選項的生效方式可能是其中之一：

- 在設定選項並發出 **RECONFIGURE** 陳述式 (在某些情況下是 **RECONFIGURE WITH OVERRIDE**) 後立即生效。 重新設定特定選項會使得計畫快取的計畫無效，以致編譯新計畫。 如需詳細資訊，請參閱 [DBCC FREEPROCCACHE &#40;Transact-SQL&#41;](../../t-sql/database-console-commands/dbcc-freeproccache-transact-sql.md)。

  \- 或 -

- 執行上述動作並重新啟動 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]執行個體後。

需要重新啟動 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的選項最初只會在 value 資料行中顯示變更後的值。 重新啟動之後，新值將同時出現在 value 資料行及 value_in_use 資料行。

有些選項需要重新啟動伺服器，新的組態值才能生效。 如果在重新啟動伺服器之前就設定新值並執行 sp_configure 的話，新值會出現在組態選項的 **value** 資料行，但不會出現在 **value_in_use** 資料行。 重新啟動伺服器之後，新的值就會出現在 **value_in_use** 資料行。

自我設定的選項是指 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會根據系統需要而自行調整的選項。 在大多數情況下，都不需以手動方式來設定這些值。 範例包括 [最大背景工作執行緒] 選項與 [使用者連線] 選項。

## <a name="configuration-options-table"></a>組態選項表
 下表列出所有可用的組態選項、可能的設定範圍以及預設值。 組態選項會加上字母標示，如下所示：

- A= 進階選項，只能由有經驗的資料庫管理員或通過認證的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 專業人員變更，而且必須將 [顯示進階選項選項] 設定為 1。

- RR = 需要重新啟動 [!INCLUDE[ssDE](../../includes/ssde-md.md)]的選項。

- RP = 需要重新啟動 PolyBase 引擎的選項。

- SC = 自我設定的選項。

| 組態選項 | 最小值 | 最大值 | 預設 |
|--|--|--|--|
| [access check cache bucket count](../../database-engine/configure-windows/access-check-cache-server-configuration-options.md) (A) | 0 | 16384 | 0 |
| [access check cache quota](../../database-engine/configure-windows/access-check-cache-server-configuration-options.md) (A) | 0 | 2147483647 | 0 |
| [ad hoc distributed queries](../../database-engine/configure-windows/ad-hoc-distributed-queries-server-configuration-option.md) (A) | 0 | 1 | 0 |
| [ADR cleaner retry timeout (分鐘)](../../database-engine/configure-windows/adr-cleaner-retry-timeout-configuration-option.md)<br><br> 在 SQL Server 2019 中引進 | 0 | 32767 | 15 |
| [ADR Preallocation Factor](../../database-engine/configure-windows/adr-preallocation-factor-server-configuration-option.md)<br><br> 在 SQL Server 2019 中引進 | 0 | 32767 | 4 |
| [affinity I/O mask](../../database-engine/configure-windows/affinity-input-output-mask-server-configuration-option.md) (A、RR) | -2147483648 | 2147483647 | 0 |
| [affinity mask](../../database-engine/configure-windows/affinity-mask-server-configuration-option.md) (A) | -2147483648 | 2147483647 | 0 |
| [affinity64 I/O mask](../../database-engine/configure-windows/affinity64-input-output-mask-server-configuration-option.md) (A，只能用於 64 位元版本的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]) | -2147483648 | 2147483647 | 0 |
| [affinity64 mask](../../database-engine/configure-windows/affinity64-mask-server-configuration-option.md) (A、RR)，只能用於 64 位元版本的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] | -2147483648 | 2147483647 | 0 |
| [Agent XPs](../../database-engine/configure-windows/agent-xps-server-configuration-option.md) (A) | 0 | 1 | 0<br /><br /> ( [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent 啟動時將變成 1。 如果在安裝期間將 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent 設定為自動啟動，預設值就是 0)。 |
| [允許 polybase 匯出](../../database-engine/configure-windows/allow-polybase-export.md)<br/><br/> 第 1 課：建立 Windows Azure 儲存體物件[!INCLUDE [sqlserver2016](../../includes/applies-to-version/sqlserver2016.md)]。| 0 | 1 | 0 |
| [allow updates](../../database-engine/configure-windows/allow-updates-server-configuration-option.md) (已過時。 請勿使用。 否則會在重新設定期間導致錯誤)。 | 0 | 1 | 0 |
| [停用自動軟體 NUMA](soft-numa-sql-server.md) | 0 | 1 | 0 |
| [備份總和檢查碼預設](../../database-engine/configure-windows/backup-checksum-default.md) | 0 | 1 | 0 |
| [backup compression default](../../database-engine/configure-windows/view-or-configure-the-backup-compression-default-server-configuration-option.md) | 0 | 1 | 0 |
| [已封鎖的處理序臨界值](../../database-engine/configure-windows/blocked-process-threshold-server-configuration-option.md) (A) | 5 | 86400 | 0 |
| [c2 audit mode](../../database-engine/configure-windows/c2-audit-mode-server-configuration-option.md) (A、RR) | 0 | 1 | 0 |
| [clr enabled](../../database-engine/configure-windows/clr-enabled-server-configuration-option.md) | 0 | 1 | 0 |
| [CLR 嚴格安全性](../../database-engine/configure-windows/clr-strict-security.md) (A) <br /> [!INCLUDE [sqlserver2017](../../includes/applies-to-version/sqlserver2017.md)]. | 0 | 1 | 0 |
| [column encryption enclave type ](../../database-engine/configure-windows/configure-column-encryption-enclave-type.md) (A、RR) | 0 | 1 | 0 |
| [common criteria compliance enabled](../../database-engine/configure-windows/common-criteria-compliance-enabled-server-configuration-option.md) (A、RR) | 0 | 1 | 0 |
| [自主資料庫驗證](../../database-engine/configure-windows/contained-database-authentication-server-configuration-option.md) | 0 | 1 | 0 |
| [平行處理原則的成本臨界值](../../database-engine/configure-windows/configure-the-cost-threshold-for-parallelism-server-configuration-option.md) (A) | 0 | 32767 | 5 |
| [cross db ownership chaining](../../database-engine/configure-windows/cross-db-ownership-chaining-server-configuration-option.md) | 0 | 1 | 0 |
| [資料指標臨界值](../../database-engine/configure-windows/configure-the-cursor-threshold-server-configuration-option.md) (A) | -1 | 2147483647 | -1 |
| [Database Mail XPs](../../database-engine/configure-windows/database-mail-xps-server-configuration-option.md) (A) | 0 | 1 | 0 |
| [預設全文檢索語言](../../database-engine/configure-windows/configure-the-default-full-text-language-server-configuration-option.md) (A) | 0 | 2147483647 | 1033 |
| [default language](../../database-engine/configure-windows/configure-the-default-language-server-configuration-option.md) | 0 | 9999 | 0 |
| [預設追蹤已啟用](../../database-engine/configure-windows/default-trace-enabled-server-configuration-option.md) (A) | 0 | 1 | 1 |
| [不允許來自觸發程序的結果](../../database-engine/configure-windows/disallow-results-from-triggers-server-configuration-option.md) (A) | 0 | 1 | 0 |
| [EKM provider enabled](../../database-engine/configure-windows/ekm-provider-enabled-server-configuration-option.md) | 0 | 1 | 0 |
| [啟用外部指令碼](../../database-engine/configure-windows/external-scripts-enabled-server-configuration-option.md) (SC) (RR)<br /><br />第 1 課：建立 Windows Azure 儲存體物件[!INCLUDE [sqlserver2016](../../includes/applies-to-version/sqlserver2016.md)]。 | 0 | 1 | 0 |
| [filestream_access_level](../../database-engine/configure-windows/filestream-access-level-server-configuration-option.md) | 0 | 2 | 0 |
| [fill factor](../../database-engine/configure-windows/configure-the-fill-factor-server-configuration-option.md) (A、RR) | 0 | 100 | 0 |
| [全文檢索耙梳頻寬 (最大)](../../database-engine/configure-windows/ft-crawl-bandwidth-server-configuration-option.md) (A) | 0 | 32767 | 100 |
| [全文檢索耙梳頻寬 (最小)](../../database-engine/configure-windows/ft-crawl-bandwidth-server-configuration-option.md) (A) | 0 | 32767 | 0 |
| [全文檢索通知頻寬 (最大)](../../database-engine/configure-windows/ft-notify-bandwidth-server-configuration-option.md) (A) | 0 | 32767 | 100 |
| [全文檢索通知頻寬 (最小)](../../database-engine/configure-windows/ft-notify-bandwidth-server-configuration-option.md) (A) | 0 | 32767 | 0 |
| [Hadoop 連線能力](../../database-engine/configure-windows/polybase-connectivity-configuration-transact-sql.md) (RP)<br /><br />第 1 課：建立 Windows Azure 儲存體物件[!INCLUDE [sqlserver2016](../../includes/applies-to-version/sqlserver2016.md)]。 | 0 | 7 | 0 |
| [in-doubt xact resolution](../../database-engine/configure-windows/in-doubt-xact-resolution-server-configuration-option.md) (A) | 0 | 2 | 0 |
| [index create memory](../../database-engine/configure-windows/configure-the-index-create-memory-server-configuration-option.md) (A、SC) | 704 | 2147483647 | 0 |
| [lightweight pooling](../../database-engine/configure-windows/lightweight-pooling-server-configuration-option.md) (A、RR) | 0 | 1 | 0 |
| [locks](../../database-engine/configure-windows/configure-the-locks-server-configuration-option.md) (A、RR、SC) | 5000 | 2147483647 | 0 |
| [max degree of parallelism](../../database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option.md) (A) | 0 | 32767 | 0 |
| [max full-text crawl range](../../database-engine/configure-windows/max-full-text-crawl-range-server-configuration-option.md) (A) | 0 | 256 | 4 |
| [max server memory](../../database-engine/configure-windows/server-memory-server-configuration-options.md) (A、SC) | 16 | 2147483647 | 2147483647 |
| [max text repl size](../../database-engine/configure-windows/configure-the-max-text-repl-size-server-configuration-option.md) | 0 | 2147483647 | 65536 |
| [max worker threads](../../database-engine/configure-windows/configure-the-max-worker-threads-server-configuration-option.md) (A) | 128 | 32767<br /><br /> 1024 是 32 位元 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的最大建議值，而 64 位元 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 則為 2048。 **注意：** [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 是 32 位元作業系統上可用的最後一個版本。 | 0<br /><br /> 零表示自動設定 max worker threads 數目，而這個數目是根據處理器數目，透過用於 32 位元 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的公式 (256 + ( *\<processors>* -4) * 8) 來決定，而 64 位元 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 則為 (512 + ( *\<processors>* - 4) * 8)。 **注意：** [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 是 32 位元作業系統上可用的最後一個版本。 |
| [media retention](../../database-engine/configure-windows/configure-the-media-retention-server-configuration-option.md) (A、RR) | 0 | 365 | 0 |
| [min memory per query](../../database-engine/configure-windows/configure-the-min-memory-per-query-server-configuration-option.md) (A) | 512 | 2147483647 | 1024 |
| [min server memory](../../database-engine/configure-windows/server-memory-server-configuration-options.md) (A、SC) | 0 | 2147483647 | 0 |
| [巢狀觸發程序](../../database-engine/configure-windows/configure-the-nested-triggers-server-configuration-option.md) | 0 | 1 | 1 |
| [network packet size](../../database-engine/configure-windows/configure-the-network-packet-size-server-configuration-option.md) (A) | 512 | 32767 | 4096 |
| [Ole Automation Procedures](../../database-engine/configure-windows/ole-automation-procedures-server-configuration-option.md) (A) | 0 | 1 | 0 |
| [open objects](../../database-engine/configure-windows/open-objects-server-configuration-option.md) (A、RR，已過時) | 0 | 2147483647 | 0 |
| [optimize for ad hoc workloads](../../database-engine/configure-windows/optimize-for-ad-hoc-workloads-server-configuration-option.md) (A) | 0 | 1 | 0 |
| [PH_timeout](../../database-engine/configure-windows/ph-timeout-server-configuration-option.md) (A) | 1 | 3600 | 60 |
| [啟用 Polybase](../../relational-databases/polybase/polybase-installation.md#enable) (RR) <br/><br/>[!INCLUDE [sqlserver2019](../../includes/applies-to-version/sqlserver2019.md)]| 0 | 1 | 0 |
| [Polybase 網路加密](../../relational-databases/polybase/polybase-installation.md#enable) | 0 | 1 | 1 |
| [precompute rank](../../database-engine/configure-windows/precompute-rank-server-configuration-option.md) (A) | 0 | 1 | 0 |
| [priority boost](../../database-engine/configure-windows/configure-the-priority-boost-server-configuration-option.md) (A、RR) | 0 | 1 | 0 |
| [query governor cost limit](../../database-engine/configure-windows/configure-the-query-governor-cost-limit-server-configuration-option.md) (A) | 0 | 2147483647 | 0 |
| [query wait](../../database-engine/configure-windows/configure-the-query-wait-server-configuration-option.md) (A) | -1 | 2147483647 | -1 |
| [recovery interval](../../database-engine/configure-windows/configure-the-recovery-interval-server-configuration-option.md) (A、SC) | 0 | 32767 | 0 |
| [remote access](../../database-engine/configure-windows/configure-the-remote-access-server-configuration-option.md) (RR) | 0 | 1 | 1 |
| [remote admin connections](../../database-engine/configure-windows/remote-admin-connections-server-configuration-option.md) | 0 | 1 | 0 |
| [遠端資料封存](../../database-engine/configure-windows/configure-the-remote-data-archive-server-configuration-option.md) | 0 | 1 | 0 |
| [remote login timeout](../../database-engine/configure-windows/configure-the-remote-login-timeout-server-configuration-option.md) | 0 | 2147483647 | 10 |
| [remote proc trans](../../database-engine/configure-windows/configure-the-remote-proc-trans-server-configuration-option.md) | 0 | 1 | 0 |
| [remote query timeout](../../database-engine/configure-windows/configure-the-remote-query-timeout-server-configuration-option.md) | 0 | 2147483647 | 600 |
| [Replication XPs Option](../../database-engine/configure-windows/replication-xps-server-configuration-option.md) (A) | 0 | 1 | 0 |
| [scan for startup procs](../../database-engine/configure-windows/configure-the-scan-for-startup-procs-server-configuration-option.md) (A、RR) | 0 | 1 | 0 |
| [server trigger recursion](../../database-engine/configure-windows/server-trigger-recursion-server-configuration-option.md) | 0 | 1 | 1 |
| [set working set size](../../database-engine/configure-windows/set-working-set-size-server-configuration-option.md) (A、RR，已過時) | 0 | 1 | 0 |
| [show advanced options](../../database-engine/configure-windows/show-advanced-options-server-configuration-option.md) | 0 | 1 | 0 |
| [SMO and DMO XPs](../../database-engine/configure-windows/smo-and-dmo-xps-server-configuration-option.md) (A) | 0 | 1 | 1 |
| [隱藏復原模式錯誤](../../database-engine/configure-windows/suppress-recovery-model-errors-server-configuration-option.md) (A) <br/><br/>[!INCLUDE [asdbmi](../../includes/applies-to-version/_asdbmi.md)]| 0 | 1 | 0 |
| [經記憶體最佳化的 TempDB 中繼資料](../../relational-databases/databases/tempdb-database.md#memory-optimized-tempdb-metadata) (A) <br/><br/> [!INCLUDE [sqlserver2019](../../includes/applies-to-version/sqlserver2019.md)].| 0 | 1 | 0 |
| [transform noise words](../../database-engine/configure-windows/transform-noise-words-server-configuration-option.md) (A) | 0 | 1 | 0 |
| [two digit year cutoff](../../database-engine/configure-windows/configure-the-two-digit-year-cutoff-server-configuration-option.md) (A) | 1753 | 9999 | 2049 |
| [user connections](../../database-engine/configure-windows/configure-the-user-connections-server-configuration-option.md) (A、RR、SC) | 0 | 32767 | 0 |
| [user options](../../database-engine/configure-windows/configure-the-user-options-server-configuration-option.md) | 0 | 32767 | 0 |
| [xp_cmdshell](../../database-engine/configure-windows/xp-cmdshell-server-configuration-option.md) (A) | 0 | 1 | 0 |  |

## <a name="see-also"></a>另請參閱
 [sp_configure &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-configure-transact-sql.md) [RECONFIGURE &#40;Transact-SQL&#41;](../../t-sql/language-elements/reconfigure-transact-sql.md) [DBCC FREEPROCCACHE &#40;Transact-SQL&#41;](../../t-sql/database-console-commands/dbcc-freeproccache-transact-sql.md)


