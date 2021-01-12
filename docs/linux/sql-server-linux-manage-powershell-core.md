---
title: 使用 PowerShell Core 管理 Linux 上的 SQL Server
description: 透過逐步引導您了解如何在 macOS 與 Linux 上搭配 PowerShell Core (PS Core) 使用 SQL Server PowerShell 的幾個範例，來了解 SQL Server PowerShell。
ms.date: 04/22/2019
ms.prod: sql
ms.technology: linux
ms.topic: conceptual
author: SQLvariant
ms.author: aanelson
ms.reviewer: vanto
ms.openlocfilehash: a65ad9ad35e4feb2a55ffbdc259601a7b16106f9
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98094553"
---
# <a name="manage-sql-server-on-linux-with-powershell-core"></a>使用 PowerShell Core 管理 Linux 上的 SQL Server

此文章介紹 [SQL Server PowerShell](../powershell/sql-server-powershell.md)，並逐步引導您了解如何在 macOS 與 Linux 上搭配 PowerShell Core (PS Core) 使用的幾個範例。 PowerShell Core 現在是 [GitHub](https://github.com/powershell/powershell) 上的開放原始碼專案。

## <a name="cross-platform-editor-options"></a>跨平台編輯器選項

以下所有的 PowerShell Core 步驟都適用於一般終端，您也可以從 VS Code 或 Azure Data Studio 內的終端執行。  VS Code 和 Azure Data Studio 都可在 macOS 和 Linux 上使用。  如需 Azure Data Studio 的詳細資訊，請參閱[此快速入門](../azure-data-studio/quickstart-sql-server.md) \(部分機器翻譯\)。  您也可能想要考慮為它使用 [PowerShell 延伸模組](../azure-data-studio/extensions/powershell-extension.md) \(部分機器翻譯\)。

## <a name="installing-powershell-core"></a>安裝 PowerShell Core

如需在各種支援和實驗性平台上安裝 PowerShell Core 的詳細資訊，請參閱下列文章：

- [在 Windows 上安裝 PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows)
- [在 Linux 上安裝 PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-linux)
- [在 macOS 上安裝 PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-macos)
- [在 ARM 上安裝 PowerShell Core](/powershell/scripting/install/powershell-core-on-arm)

## <a name="install-the-sqlserver-module"></a>安裝 SqlServer 模組

`SqlServer` 模組會保留在 [PowerShell 資源庫](https://www.powershellgallery.com/packages/SqlServer/) \(英文\) 中。 使用 SQL Server 時，您應該一律使用最新版本的 SqlServer PowerShell 模組。

若要安裝 SqlServer 模組，請開啟 PowerShell Core 工作階段，然後執行下列程式碼：

```powerhsell
Install-Module -Name SqlServer
```

如需有關如何從 PowerShell 資源庫安裝 SqlServer 模組的詳細資訊，請參閱[此頁面](../powershell/download-sql-server-ps-module.md)。

## <a name="using-the-sqlserver-module"></a>使用 SqlServer 模組

讓我們從啟動 PowerShell Core 開始。  如果您是在 macOS 或 Linux 上，請在您的電腦上開啟終端工作階段  ，然後輸入 **pwsh** 以啟動新的 PowerShell Core 工作階段。  在 Windows 上，請使用 <kbd>Win</kbd>+<kbd>R</kbd>，然後輸入 `pwsh` 以啟動新的 PowerShell Core 工作階段。

```
pwsh
```

SQL Server 提供名為 **SqlServer** 的 PowerShell 模組。 您可以使用 **SqlServer** 模組，將 SQL Server 元件 (SQL Server 提供者和 Cmdlet) 匯入 PowerShell 環境或指令碼中。

在 PowerShell 提示字元中複製並貼上下列命令，將 **SqlServer** 模組匯入到目前的 PowerShell 工作階段：

```powershell
Import-Module SqlServer
```

在 PowerShell 提示字元中鍵入下列命令，確認 **SqlServer** 模組已正確匯入：

```powershell
Get-Module -Name SqlServer
```

PowerShell 應該會顯示類似下列輸出的資訊：

```
ModuleType Version    Name          ExportedCommands
---------- -------    ----          ----------------
Script     21.1.18102 SqlServer     {Add-SqlAvailabilityDatabase, Add-SqlAvailabilityGroupList...
```

## <a name="connect-to-sql-server-and-get-server-information"></a>連線到 SQL Server 並取得伺服器資訊

下列步驟使用 PowerShell Core 連線至您在 Linux 上的 SQL Server 執行個體，並顯示幾個伺服器屬性。

在 PowerShell 提示字元中複製並貼上下列命令。 當您執行這些命令時，PowerShell 將會：
- 顯示對話方塊，提示您輸入執行個體的主機名稱或 IP 位址
- 顯示 [PowerShell 認證要求]  對話方塊，它會提示您輸入認證。 您可以使用「SQL 使用者名稱」  和「SQL 密碼」  連線至 Linux 上的 SQL Server 執行個體
- 使用 **Get-SqlInstance** Cmdlet 連線到 **伺服器**，並顯示一些屬性

(選擇性) 您可以將 `$serverInstance` 變數取代為您 SQL Server 執行個體的 IP 位址或主機名稱。

```powershell
# Prompt for instance & credentials to login into SQL Server
$serverInstance = Read-Host "Enter the name of your instance"
$credential = Get-Credential

# Connect to the Server and return a few properties
Get-SqlInstance -ServerInstance $serverInstance -Credential $credential
# done
```

PowerShell 應該會顯示類似下列輸出的資訊：

```
Instance Name                   Version    ProductLevel UpdateLevel  HostPlatform HostDistribution
-------------                   -------    ------------ -----------  ------------ ----------------
your_server_instance            14.0.3048  RTM          CU13         Linux        Ubuntu
```

> [!NOTE]
> 如果這些值沒有顯示任何內容，與目標 SQL Server 執行個體的連線很可能失敗。 請確定您可以使用相同的連線資訊，從 SQL Server Management Studio 連線。 然後檢閱[連線疑難排解建議](sql-server-linux-troubleshooting-guide.md#connection)。

## <a name="using-the-sql-server-powershell-provider"></a>使用 SQL Server PowerShell 提供者

連線到 SQL Server 執行個體的另一個選項是使用 [SQL Server PowerShell 提供者](../powershell/sql-server-powershell-provider.md)。  使用提供者可讓您瀏覽 SQL Server 執行個體，如同您在 [物件總管] 中瀏覽樹狀結構一樣，但是在 cmdline 上。  根據預設，此提供者會顯示為名為 `SQLSERVER:\` 的 PSDrive，您可以用來連線與巡覽您的網域帳戶可存取的 SQL Server 執行個體。  如需如何對 Linux 上的 SQL Server 設定 Active Directory 驗證的詳細資訊，請參閱 [Configuration steps](./sql-server-linux-active-directory-auth-overview.md#configuration-steps) (設定步驟)。

您也可以搭配 SQL Server PowerShell 提供者使用 SQL 驗證。 若要這麼做，請使用 `New-PSDrive` Cmdlet 來建立新的 PSDrive，並提供適當的認證來進行連線。

在下面的範例中，您會看到如何使用 SQL 驗證建立新 PSDrive 的範例。

```powershell
# NOTE: We are reusing the values saved in the $credential variable from the above example.

New-PSDrive -Name SQLonDocker -PSProvider SqlServer -Root 'SQLSERVER:\SQL\localhost,10002\Default\' -Credential $credential
```

您可以藉由執行 `Get-PSDrive` Cmdlet 確認磁碟機是否建立。

```powershell
Get-PSDrive
```

建立新的 PSDrive 之後，您就可以開始巡覽。

```powershell
dir SQLonDocker:\Databases
```

輸出可能會如下所示。  您可能會注意到，此輸出類似 SSMS 會顯示在 [資料庫] 節點上的內容。  它會顯示使用者資料庫，而不是系統資料庫。

```powershell
Name                 Status           Size     Space  Recovery Compat. Owner
                                            Available  Model     Level
----                 ------           ---- ---------- -------- ------- -----
AdventureWorks2016   Normal      209.63 MB    1.31 MB Simple       130 sa
AdventureWorksDW2012 Normal      167.00 MB   32.47 MB Simple       110 sa
AdventureWorksDW2014 Normal      188.00 MB   78.10 MB Simple       120 sa
AdventureWorksDW2016 Normal      172.00 MB   74.76 MB Simple       130 sa
AdventureWorksDW2017 Normal      208.00 MB   40.57 MB Simple       140 sa
```

如果您需要查看執行個體上的所有資料庫，其中一個選項是使用 `Get-SqlDatabase` Cmdlet。

## <a name="get-databases"></a>取得資料庫

要知道的一個重要的 Cmdlet 是 `Get-SqlDatabase`。  對於涉及某個資料庫的許多作業，或某個資料庫內的許多物件，可以使用 `Get-SqlDatabase` Cmdlet。  如果您同時提供 `-ServerInstance` 和 `-Database` 參數的值，只會擷取一個資料庫物件。  不過，如果您只指定 `-ServerInstance` 參數，就會傳回該執行個體上所有資料庫的完整清單。

```powershell
# NOTE: We are reusing the values saved in the $credential variable from the above example.

# Connect to the Instance and retrieve all databases
Get-SqlDatabase -ServerInstance ServerB -Credential $credential
```

以下是上述 Get-SqlDatabase 命令可能傳回之內容的範例：

```powershell
Name                 Status           Size     Space  Recovery Compat. Owner
                                            Available  Model     Level
----                 ------           ---- ---------- -------- ------- -----
AdventureWorks2016   Normal      209.63 MB    1.31 MB Simple       130 sa
AdventureWorksDW2012 Normal      167.00 MB   32.47 MB Simple       110 sa
AdventureWorksDW2014 Normal      188.00 MB   78.10 MB Simple       120 sa
AdventureWorksDW2016 Normal      172.00 MB   74.88 MB Simple       130 sa
AdventureWorksDW2017 Normal      208.00 MB   40.63 MB Simple       140 sa
master               Normal        6.00 MB  600.00 KB Simple       140 sa
model                Normal       16.00 MB    5.70 MB Full         140 sa
msdb                 Normal       15.50 MB    1.14 MB Simple       140 sa
tempdb               Normal       16.00 MB    5.49 MB Simple       140 sa

```

## <a name="examine-sql-server-error-logs"></a>檢查 SQL Server 錯誤記錄檔

下列步驟會使用 PowerShell Core 來檢查 Linux 上 SQL Server 執行個體上的錯誤記錄檔連線。

在 PowerShell 提示字元中複製並貼上下列命令。 可能需要幾分鐘的時間執行。 這些命令會執行下列步驟：
- 顯示對話方塊，提示您輸入執行個體的主機名稱或 IP 位址
- 顯示 [PowerShell 認證要求]  對話方塊，它會提示您輸入認證。 您可以使用「SQL 使用者名稱」  和「SQL 密碼」  連線至 Linux 上的 SQL Server 執行個體
- 使用 **Get-SqlErrorLog** Cmdlet 連線到 Linux 上的 SQL Server 執行個體，並擷取自 **昨天** 起的錯誤記錄檔

(選擇性) 您可以將 `$serverInstance` 變數取代為您 SQL Server 執行個體的 IP 位址或主機名稱。

```powershell
# Prompt for instance & credentials to login into SQL Server
$serverInstance = Read-Host "Enter the name of your instance"
$credential = Get-Credential

# Retrieve error logs since yesterday
Get-SqlErrorLog -ServerInstance $serverInstance -Credential $credential -Since Yesterday
# done
```

## <a name="explore-cmdlets-currently-available-in-ps-core"></a>探索 PS Core 中目前可用的 Cmdlet
雖然 SqlServer 模組目前在 Windows PowerShell 中有 109 個 Cmdlet，但 PSCore 只提供 109 個中的 62 個。 以下包含目前可用的 62 個 Cmdlet 完整清單。  如需 SqlServer 模組中所有 Cmdlet 的深入文件，請參閱 SqlServer [Cmdlet 參考](/powershell/module/sqlserver/) \(英文\)。

下列命令會顯示您所使用的 PowerShell 版本上所有可用的 Cmdlet。

```powershell
Get-Command -Module SqlServer -CommandType Cmdlet |
Sort-Object -Property Noun |
SELECT Name
```

- ConvertFrom-EncodedSqlName
- ConvertTo-EncodedSqlName
- Get-SqlAgent
- Get-SqlAgentJob
- Get-SqlAgentJobHistory
- Get-SqlAgentJobSchedule
- Get-SqlAgentJobStep
- Get-SqlAgentSchedule
- Invoke-SqlAssessment
- Get-SqlAssessmentItem
- Remove-SqlAvailabilityDatabase
- Resume-SqlAvailabilityDatabase
- Add-SqlAvailabilityDatabase
- Suspend-SqlAvailabilityDatabase
- New-SqlAvailabilityGroup
- Set-SqlAvailabilityGroup
- Remove-SqlAvailabilityGroup
- Switch-SqlAvailabilityGroup
- Join-SqlAvailabilityGroup
- Revoke-SqlAvailabilityGroupCreateAnyDatabase
- Grant-SqlAvailabilityGroupCreateAnyDatabase
- New-SqlAvailabilityGroupListener
- Set-SqlAvailabilityGroupListener
- Add-SqlAvailabilityGroupListenerStaticIp
- Set-SqlAvailabilityReplica
- Remove-SqlAvailabilityReplica
- New-SqlAvailabilityReplica
- Set-SqlAvailabilityReplicaRoleToSecondary
- New-SqlBackupEncryptionOption
- Get-SqlBackupHistory
- Invoke-Sqlcmd
- New-SqlCngColumnMasterKeySettings
- Remove-SqlColumnEncryptionKey
- Get-SqlColumnEncryptionKey
- Remove-SqlColumnEncryptionKeyValue
- Add-SqlColumnEncryptionKeyValue
- Get-SqlColumnMasterKey
- Remove-SqlColumnMasterKey
- New-SqlColumnMasterKey
- Get-SqlCredential
- Set-SqlCredential
- New-SqlCredential
- Remove-SqlCredential
- New-SqlCspColumnMasterKeySettings
- Get-SqlDatabase
- Restore-SqlDatabase
- Backup-SqlDatabase
- Set-SqlErrorLog
- Get-SqlErrorLog
- New-SqlHADREndpoint
- Set-SqlHADREndpoint
- Get-SqlInstance
- Add-SqlLogin
- Remove-SqlLogin
- Get-SqlLogin
- Set-SqlSmartAdmin
- Get-SqlSmartAdmin
- Read-SqlTableData
- Write-SqlTableData
- Read-SqlViewData
- Read-SqlXEvent
- Convert-UrnToPath

## <a name="see-also"></a>另請參閱
- [SQL Server PowerShell](../powershell/sql-server-powershell.md)