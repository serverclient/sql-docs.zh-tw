---
title: 安裝：PowerShell Desired State Configuration
description: 使用 PowerShell DSC 安裝 SQL Server，並了解 Windows Server 2016 上 SQL Server 2017 獨立執行個體的初始設定。
ms.custom: seo-lt-2019
ms.date: 12/13/2019
ms.devlang: PowerShell
ms.prod: sql
ms.reviewer: ''
ms.technology: install
ms.topic: conceptual
author: randomnote1
ms.author: dareist
monikerRange: '>=sql-server-2016'
ms.openlocfilehash: d0c3828e26e5bde24d678cfac8959264adafcb13
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98091592"
---
# <a name="install-sql-server-with-powershell-desired-state-configuration"></a>透過 PowerShell Desired State Configuration 安裝 SQL Server

您是否曾在完成 SQL Server 安裝介面時，不經思索即直接選取相同的按鈕，然後輸入相同的資訊？ 安裝已完成，但您忘了在 **系統管理員** 角色中指定 DBA 群組。 於是，您必須執行下列事項：
* 進入單一使用者模式。
* 新增適當的使用者或群組。
* 以多使用者模式重新啟動 SQL Server。
* 測試。 

更糟的是，現在您對整個安裝的信心都受到動搖。 「我還忘了什麼事？」 您可能會這樣問您自己。

請參閱 [PowerShell Desired State Configuration (DSC)](/powershell/scripting/dsc/overview/overview)。 藉由使用 DSC，您能建置一份可在成千上百部伺服器上重複使用的設定範本。 根據組建，您可能必須調整幾個設定參數。 但這並不是重要的問題，因為您可以就地保留所有的標準設定。 這可排除您忘了輸入重要參數的可能性。

本文會探討如何使用 **SqlServerDsc** DSC 資源，對 Windows Server 2016 上的獨立 SQL Server 2017 執行個體進行初始設定。 具備 DSC 的一些背景知識非常有用，因為我們不會探索 DSC 的運作方式。

本逐步解說需要下列項目：

- 執行 Windows Server 2016 的電腦。
- SQL Server 2017 安裝媒體。
- **SqlServerDsc** DSC 資源。

## <a name="prerequisites"></a>Prerequisites

在大多數情況下，會使用 DSC 來處理必要條件需求。 但是，基於此示範的目的，我們會手動處理必要條件。

## <a name="install-the-sqlserverdsc-dsc-resource"></a>安裝 SqlServerDsc DSC 資源

使用 [Install-Module](/powershell/module/powershellget/Install-Module) Cmdlet，從 [PowerShell 資源庫](https://www.powershellgallery.com/)下載 [SqlServerDsc](https://www.powershellgallery.com/packages/SqlServerDsc) DSC 資源。 

> [!NOTE]
> 請確保 PowerShell 以 **系統管理員身分** 執行，以便安裝模組。

```PowerShell
Install-Module -Name SqlServerDsc
```

### <a name="get-the-sql-server-2017-installation-media"></a>取得 SQL Server 2017 安裝媒體
將 SQL Server 2017 安裝媒體下載到伺服器。 我們已從 Visual Studio 訂用帳戶下載 SQL Server 2017 Enterprise，並將 ISO 複製到 `C:\en_sql_server_2017_enterprise_x64_dvd_11293666.iso`。

現在 ISO 必須解壓縮至目錄：

```PowerShell
New-Item -Path C:\SQL2017 -ItemType Directory
$mountResult = Mount-DiskImage -ImagePath 'C:\en_sql_server_2017_enterprise_x64_dvd_11293666.iso' -PassThru
$volumeInfo = $mountResult | Get-Volume
$driveInfo = Get-PSDrive -Name $volumeInfo.DriveLetter
Copy-Item -Path ( Join-Path -Path $driveInfo.Root -ChildPath '*' ) -Destination C:\SQL2017\ -Recurse
Dismount-DiskImage -ImagePath 'C:\en_sql_server_2017_enterprise_x64_dvd_11293666.iso'
```

## <a name="create-the-configuration"></a>建立設定

### <a name="configuration"></a>組態

建立設定函式，之後會呼叫此函式來產生[受控物件格式 (MOF)](/windows/desktop/WmiSdk/managed-object-format--mof-) 文件：

```PowerShell
Configuration SQLInstall
{...}
```

### <a name="modules"></a>模組

將模組匯入目前的工作階段。 這些模組會指示設定文件如何建置 MOF 文件。 它們也會指示 DSC 引擎如何將 MOF 文件套用至伺服器：

```PowerShell
Import-DscResource -ModuleName SqlServerDsc
```

### <a name="resources"></a>資源

#### <a name="net-framework"></a>.NET Framework

SQL Server 依賴於 .NET Framework。 所以我們需要確保它已在安裝 SQL Server 之前安裝。 **WindowsFeature** 資源可用來安裝 **Net-Framework-45-Core** Windows 功能：

```PowerShell
WindowsFeature 'NetFramework45'
{
     Name = 'Net-Framework-45-Core'
     Ensure = 'Present'
}
```

#### <a name="sqlsetup"></a>SqlSetup

**SqlSetup** 資源可用來指示 DSC 如何安裝 SQL Server。 基本安裝所需的參數如下所示：

- **InstanceName**。 執行個體的名稱。 針對預設執行個體，請使用 **MSSQLSERVER**。
- **Features**。 要安裝的功能。 在此範例中，我們只會安裝 **SQLEngine** 功能。
- **SourcePath**。 SQL 安裝媒體的路徑。 在此範例中，我們已將 SQL 安裝媒體儲存在 `C:\SQL2017`。 網路共用可將伺服器上所使用的空間降到最低。
- **SQLSysAdminAccounts**。 要成為 **系統管理員** 角色成員的使用者或群組。 在此範例中，我們會將 **系統管理員** 存取權授與本機系統管理員群組。 

> [!NOTE]
> 不建議您在高安全性環境中使用此設定。

[SqlServerDsc GitHub 存放庫](https://github.com/PowerShell/SqlServerDsc/tree/master#sqlsetup)提供 **SqlSetup** 上可用參數的完整清單和描述。

**SqlSetup** 資源只會安裝 SQL Server，且 **不會** 維護所套用的設定。 例如，若在安裝期間指定 **SQLSysAdminAccounts**。 系統管理員可以在 **系統管理員** 角色中新增或移除登入。 但是 **SqlSetup** 資源不會受到影響。 如果您想要 DSC 強制執行 **系統管理員** 角色的成員資格，請使用 [SqlServerRole](https://github.com/PowerShell/SqlServerDsc/tree/master#sqlserverrole) 資源。

#### <a name="finish-configuration"></a>完成設定

```PowerShell
Configuration SQLInstall
{
     Import-DscResource -ModuleName SqlServerDsc

     node localhost
     {
          WindowsFeature 'NetFramework45'
          {
               Name   = 'NET-Framework-45-Core'
               Ensure = 'Present'
          }

          SqlSetup 'InstallDefaultInstance'
          {
               InstanceName        = 'MSSQLSERVER'
               Features            = 'SQLENGINE'
               SourcePath          = 'C:\SQL2017'
               SQLSysAdminAccounts = @('Administrators')
               DependsOn           = '[WindowsFeature]NetFramework45'
          }
     }
}
```

## <a name="build-and-deploy"></a>建置及部署

### <a name="compile-the-configuration"></a>編譯設定

對設定指令碼執行點溯源：

```PowerShell
. .\SQLInstallConfiguration.ps1
```

執行設定函式：

```PowerShell
SQLInstall
```

隨即在工作目錄中建立稱為 **SQLInstall** 的目錄。 其中包含稱為 **localhost.mof** 的檔案。 請檢查顯示編譯 DSC 設定的 MOF 內容。

### <a name="deploy-the-configuration"></a>部署設定

若要啟動 SQL Server 的 DSC 部署，請呼叫 **Start-DscConfiguration** Cmdlet。 此 Cmdlet 會提供下列參數：

- **Path**。 包含所要部署 MOF 文件的資料夾路徑。 例如 `C:\SQLInstall`。
- **Wait**。 等候設定工作完成。
- **Force**。 覆寫任何現有的 DSC 設定。
- **Verbose**。 顯示詳細資訊輸出。 第一次推送設定時可用來協助進行疑難排解。

```PowerShell
Start-DscConfiguration -Path C:\SQLInstall -Wait -Force -Verbose
```

當設定適用時，詳細資訊輸出會顯示所發生的情況。 只要沒有擲回任何錯誤 (紅色文字)，當畫面上顯示 **Operation 'Invoke CimMethod' complete** (作業 'Invoke CimMethod' 完成) 時，應該會安裝 SQL Server。

## <a name="validate-installation"></a>驗證安裝

### <a name="dsc"></a>DSC

[Test-DscConfiguration](/powershell/module/psdesiredstateconfiguration/test-dscconfiguration) Cmdlet 可判斷伺服器目前狀態是否符合所需的狀態。 在本例中，它是 SQL Server 安裝。 **Test-DscConfiguration** 的結果應為 **True**：

```PowerShell
PS C:\> Test-DscConfiguration
True
```

### <a name="services"></a>服務

服務清單現在會傳回 SQL Server 服務：

```PowerShell
PS C:\> Get-Service -Name *SQL*
Status  Name           DisplayName
------  ----           -----------
Running MSSQLSERVER    SQL Server (MSSQLSERVER)
Stopped SQLBrowser     SQL Server Browser
Running SQLSERVERAGENT SQL Server Agent (MSSQLSERVER)
Running SQLTELEMETRY   SQL Server CEIP service (MSSQLSERVER)
Running SQLWriter      SQL Server VSS Writer
```

### <a name="sql-server"></a>SQL Server

```PowerShell
PS C:\> & sqlcmd -S $env:COMPUTERNAME
1> SELECT @@SERVERNAME
2> GO
1> quit
```

## <a name="see-also"></a>另請參閱

[Windows PowerShell Desired State Configuration 概觀](/powershell/scripting/dsc/overview/overview)

[從命令提示字元安裝 SQL Server](../../database-engine/install-windows/install-sql-server-from-the-command-prompt.md)

[使用設定檔安裝 SQL Server](../../database-engine/install-windows/install-sql-server-using-a-configuration-file.md)
