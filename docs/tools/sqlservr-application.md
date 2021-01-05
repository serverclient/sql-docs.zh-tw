---
title: sqlservr 應用程式
description: Sqlservr 應用程式可從命令提示字元啟動、停止、暫停和繼續執行 SQL Server 的執行個體。
ms.custom: seo-lt-2019
ms.date: 07/22/2020
ms.prod: sql
ms.prod_service: sql-tools
ms.reviewer: ''
ms.technology: tools-other
ms.topic: conceptual
helpviewer_keywords:
- command prompt utilities [SQL Server], sqlservr
- command prompt [SQL Server], pausing/resuming instance of SQL Server
- starting instance of SQL Server
- command prompt [SQL Server], continuing instance of SQL Server
- sqlservr utility
- pausing instance of SQL Server
- stopping instance of SQL Server
- resuming SQL Server
- command prompt [SQL Server], stopping instance of SQL Server
- command prompt [SQL Server], starting instance of SQL Server
- continuing instance of SQL Server
ms.assetid: 60e8ef0a-0851-41cf-a6d8-cca1e04cbcdb
author: markingmyname
ms.author: maghan
ms.openlocfilehash: 67f25ef21a7d4897bba7242da796bd41f9fc655e
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97642187"
---
# <a name="sqlservr-application"></a>sqlservr 應用程式

[!INCLUDE[sqlserver](../includes/applies-to-version/sqlserver.md)]

**sqlservr** 應用程式會在命令提示字元之下，啟動、停止、暫停和繼續執行 [!INCLUDE[msCoName](../includes/msconame-md.md)] [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 的執行個體。

## <a name="syntax"></a>語法

```cmd
sqlservr [-s instance_name] [-c] [-d master_path] [-f] 
     [-e error_log_path] [-l master_log_path] [-m]
     [-n] [-T trace#] [-v] [-x]
```

## <a name="arguments"></a>引數

**-s** *instance_name* 指定要連接的 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 執行個體。 如果未指定任何具名執行個體， **sqlservr** 會啟動 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]的預設執行個體。

> [!IMPORTANT]
>啟動 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]的執行個體時，您必須使用該執行個體之適當目錄中的 **sqlservr** 應用程式。 如果是預設的執行個體，請執行 \MSSQL\Binn 目錄中的 **sqlservr** 。 如果是具名執行個體，請執行 \MSSQL$ **instance_name** \Binn 目錄中的 *sqlservr*。

 **-c** 指出 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 執行個體會於 Windows 服務控制管理員之外個別啟動。 當在命令提示字元之下啟動 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 時，這個選項可用來縮短啟動 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 所花的時間。

> [!NOTE]
>使用此選項時，您不可使用 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 服務管理員或 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] net stop **命令停止** ，而且如果您登出該電腦， [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 也會停止。

**-d** *master_path* 指出 **master** 資料庫檔案的完整路徑。 **-d** 和 *master_path* 之間沒有空格。 如果不提供這個選項，會使用現有的登錄參數。

**-f** 啟動只含最小組態的 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 執行個體。 如果組態值設定 (如過度調配記憶體) 造成伺服器無法啟動，這就很有用。

**-e** *error_log_path* 指出錯誤記錄檔的完整路徑。 若未指定，則預設執行個體的預設位置是 `*\<Drive>*:\Program Files\Microsoft SQL Server\MSSQL\Log\Errorlog`，而具名執行個體的預設位置是 `*\<Drive>*:\Program Files\Microsoft SQL Server\MSSQL$*instance_name*\Log\Errorlog`。 **-e** 和 *error_log_path* 之間沒有空格。

**-l** *master_log_path* 指出 **master** 資料庫交易記錄檔的完整路徑。 **-l** 和 *master_log_path* 之間沒有空格。

**-m** 指出要以單一使用者模式啟動 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 的執行個體。 啟動 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 的單一使用者模式時，只能連接單一使用者。 不會啟動「保證從磁碟快取中，將已完成的交易定期寫入資料庫裝置」的 CHECKPOINT 機制。 (一般而言，如果系統資料庫發生需要修復的問題，便會使用這個選項。)這個選項會啟用 **sp_configure allow updates** 選項。 預設會停用 **allow updates** 。

**-n** 可讓您啟動 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 的具名執行個體。 如果沒有設定 **-s** 參數，就會嘗試啟動預設執行個體。 您必須先在命令提示字元處切換至該執行個體的適當 BINN 目錄，才能啟動 **sqlservr.exe**。 例如，如果 Instance1 原先為二進位編碼檔案使用 \mssql$Instance1，使用者就必須位於 \mssql$Instance1\binn 目錄中，才能啟動 **sqlservr.exe -s instance1**。 如果您使用 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] -n **選項啟動** 的執行個體，建議您也要使用 **-e** 選項，否則不會記錄 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 事件。

**-T** *trace#* 指出啟動 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 執行個體時，應該已啟用指定的追蹤旗標 (*trace#* )。 追蹤旗標用來啟動具有非標準行為的伺服器。 如需詳細資訊，請參閱[追蹤旗標 &#40;Transact-SQL&#41;](../t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql.md)。

>[!IMPORTANT]
>指定追蹤旗標時，請使用 **-T** 傳遞追蹤旗標號碼。 **接受小寫的 t (** -t [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)])；但是 **-t** 是用來設定 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 支援工程師所需要的其他內部追蹤旗標。

**-v** 顯示伺服器版本號碼。

**-x** 停止保留 CPU 時間和快取命中率統計資料。 允許最大效能。

## <a name="remarks"></a>備註
在大部分情況下，sqlservr.exe 程式只用來進行疑難排解或主要的維護工作。 在命令提示字元處利用 sqlservr.exe 啟動 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 時， [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 不會以服務形式啟動，因此，您無法使用 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] net **命令停止** 。 使用者可以連接至 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]，但 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 工具會顯示該服務的狀態， [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 組態管理員因而可正確地指出該服務已經停止。 [!INCLUDE[ssManStudioFull](../includes/ssmanstudiofull-md.md)] 可以連接到伺服器，但它也會指出該服務已經停止。

## <a name="compatibility-support"></a>相容性支援
下列參數已淘汰，因此 [!INCLUDE[ssCurrent](../includes/sscurrent-md.md)] 不支援。

|參數 | 詳細資訊|
|:-----|:-----|
|**-h** | 在舊版 32 位元 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 執行個體使用此參數，在啟用 AWE 的情況下保留 Hot Add Memory 中繼資料的虛擬記憶體位址空間。 透過 [!INCLUDE[sssql14](../includes/sssql14-md.md)] 支援。 如需詳細資訊，請參閱 [SQL Server 2016 中已取代及已中止的 SQL Server 功能](../database-engine/discontinued-database-engine-functionality-in-sql-server.md)。|
|**-g** | *memory_to_reserve*<br/><br>適用於舊版 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 的 32 位元執行個體。 透過 [!INCLUDE[sssql14](../includes/sssql14-md.md)] 支援。 指定 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 保留給在 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 處理序之內但在 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 記憶體集區之外的記憶體配置，所能使用的記憶體整數數量 (MB)。 如需詳細資訊，請參閱 [SQL Server 2014 文件的伺服器記憶體組態選項](/previous-versions/sql/2014/database-engine/configure-windows/server-memory-server-configuration-options?view=sql-server-2014&preserve-view=true)。|
| &nbsp; | &nbsp; |

## <a name="see-also"></a>另請參閱
 [Database Engine 服務啟動選項](../database-engine/configure-windows/database-engine-service-startup-options.md)