---
title: 下載並安裝 sqlpackage
description: 下載並安裝適用於 Windows、macOS 或 Linux 的 sqlpackage
ms.custom: tools|sos
ms.prod: sql
ms.prod_service: sql-tools
ms.technology: tools-other
ms.topic: conceptual
author: dzsquared
ms.author: drskwier
ms.reviewer: maghan; sstein
ms.date: 10/02/2020
ms.openlocfilehash: c86dde50390bdf14719da96f7fc3d0cc35a9c440
ms.sourcegitcommit: 866554663ca3191748b6e4eb4d8d82fa58c4e426
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97577818"
---
# <a name="download-and-install-sqlpackage"></a>下載並安裝 sqlpackage

sqlpackage 在 Windows、macOS 和 Linux 上執行。

下載並安裝最新的 .NET Framework 版本以及 macOS 和 Linux 預覽：

|平台|下載|發行日期|版本|Build
|:---|:---|:---|:---|:---|
|[Windows](#get-sqlpackage-for-windows)|[MSI 安裝程式](https://go.microsoft.com/fwlink/?linkid=2143544)|2020 年 9 月 18 日| 18.6 | 15.0.4897.1 |
|[macOS .NET Core](#get-sqlpackage-net-core-for-macos) |[壓縮檔](https://go.microsoft.com/fwlink/?linkid=2143659)|2020 年 9 月 18 日| 18.6| 15.0.4897.1 |
|[Linux .NET Core](#get-sqlpackage-net-core-for-linux) |[壓縮檔](https://go.microsoft.com/fwlink/?linkid=2143497)|2020 年 9 月 18 日| 18.6| 15.0.4897.1 |
|[Windows .NET Core](#get-sqlpackage-net-core-for-windows) |[壓縮檔](https://go.microsoft.com/fwlink/?linkid=2143496)|2020 年 9 月 18 日| 18.6| 15.0.4897.1 |

如需最新版本的詳細資訊，請參閱[版本資訊](release-notes-sqlpackage.md)。 若要下載其他語言，請參閱[可用的語言](#available-languages)一節。

## <a name="dacfx"></a>DacFx
SqlPackage 是 DacFx 架構的命令列介面，可公開一些公用 DacFx API。 DacServices ([Microsoft.SqlServer.Dac](/dotnet/api/microsoft.sqlserver.dac.dacservices)) 是一個與將資料庫部署整合到應用程式管線相關的機制。  DacServices API 可透過 NuGet ([Microsoft.SqlServer.DACFx](https://www.NuGet.org/packages/Microsoft.SqlServer.DACFx)) 在套件中取得。  目前的 DacFx version 版本為 150.4897.1。

透過 .NET CLI 安裝 NuGet 套件是使用下列命令完成的：

```cmd
dotnet add package Microsoft.SqlServer.DACFx
```

>[!NOTE]
> 其他 NuGet 套件是以 DacFx 名稱 "Microsoft.SqlServer.DacFx.x64" 與 "Microsoft.SqlServer.DacFx.x86" 發佈的。 這兩種平台的支援由 "Microsoft.SqlServer.DACFx" 套件涵蓋。 應該對此套件進行新的參考，而不是 x64 或 x86 變體。

## <a name="get-sqlpackage-for-windows"></a>取得適用於 Windows 的 sqlpackage

此版本的 sqlpackage 包含標準的 Windows 安裝程式體驗，以及 .zip： 

1. 下載並執行[適用於Windows 的 DacFramework.msi 安裝程式](https://go.microsoft.com/fwlink/?linkid=2143544)。
2. 開啟新的 [命令提示字元] 視窗，然後執行 sqlpackage.exe
    - sqlpackage 會安裝到 ```C:\Program Files\Microsoft SQL Server\150\DAC\bin``` 資料夾中

## <a name="get-sqlpackage-net-core-for-windows"></a>取得適用於 Windows 的 sqlpackage .NET Core

1. 下載 [sqlpackage for Windows](https://go.microsoft.com/fwlink/?linkid=2143496)。
2. 以滑鼠右鍵按一下 Windows 檔案總管中的檔案，選取 [解壓縮全部...]，然後選取目標目錄來解壓縮檔案。
3. 開啟新的終端機視窗，並使用命令前往解壓縮 sqlpackage 的位置：

   ```cmd
   > sqlpackage
   ```

## <a name="get-sqlpackage-net-core-for-macos"></a>取得適用於 macOS 的 sqlpackage .NET Core

1. 下載[適用於 macOS 的 sqlpackage](https://go.microsoft.com/fwlink/?linkid=2143659)。
2. 若要將檔案解壓縮並啟動 sqlpackage，請開啟新的終端機視窗並輸入下列命令：

   ```bash
   $ mkdir sqlpackage
   $ unzip ~/Downloads/sqlpackage-osx-<version string>.zip -d ~/sqlpackage 
   $ echo 'export PATH="$PATH:~/sqlpackage"' >> ~/.bash_profile
   $ source ~/.bash_profile
   $ sqlpackage
   ```

   > [!NOTE]
   > 安全性設定可能需要修改，才能在 macOS 上執行 sqlpackage。 請在命令列中使用下列命令，與 Gatekeeper 進行互動。

   **執行 sqlpackage 之前：**
   ```bash
   $ sudo spctl --master-disable
   ```

   **執行 sqlpackage 之後：**
   ```bash
   $ sudo spctl --master-enable
   ```

## <a name="get-sqlpackage-net-core-for-linux"></a>取得適用於 Linux 的 sqlpackage .NET Core

1. 使用其中一種安裝程式或 tar.gz 封存下載[適用於 Linux 的 sqlpackage](https://go.microsoft.com/fwlink/?linkid=2143497)：
2. 若要將檔案解壓縮並啟動 sqlpackage，請開啟新的終端機視窗並輸入下列命令：

   ```bash
   $ cd ~
   $ mkdir sqlpackage
   $ unzip ~/Downloads/sqlpackage-linux-<version string>.zip -d ~/sqlpackage 
   $ echo "export PATH=\"\$PATH:$HOME/sqlpackage\"" >> ~/.bashrc
   $ chmod a+x ~/sqlpackage/sqlpackage
   $ source ~/.bashrc
   $ sqlpackage
   ```

   > [!NOTE]
   > 在 Debian、Redhat 和 Ubuntu 上，您可能遺失相依性。 使用下列命令，根據您的 Linux 版本安裝這些相依性：

   **Debian：**

   ```bash
   $ sudo apt-get install libunwind8
   ```

   **Redhat:**

   ```bash
   $ yum install libunwind
   $ yum install libicu
   ```

   **Ubuntu:**

   ```bash
   $ sudo apt-get install libunwind8

   # install the libicu library based on the Ubuntu version
   $ sudo apt-get install libicu52      # for 14.x
   $ sudo apt-get install libicu55      # for 16.x
   $ sudo apt-get install libicu57      # for 17.x
   $ sudo apt-get install libicu60      # for 18.x
   ```

## <a name="uninstall-sqlpackage"></a>解除安裝 sqlpackage

如果您使用 Windows 安裝程式安裝 sqlpackage，請使用與刪除任何 Windows 應用程式相同的方式解除安裝。

如果您使用 .zip 或其他封存安裝 sqlpackage，請刪除這些檔案。

## <a name="supported-operating-systems"></a>支援的作業系統

sqlpackage 可在 Windows、macOS 與 Linux 上執行，而且是使用 .NET Core 3.1 建置的。  [.NET Core 3.1 OS 需求](https://github.com/dotnet/core/blob/master/release-notes/3.1/3.1-supported-os.md)適用於 sqlpackage。

### <a name="windows-x64"></a>Windows (x64)

- Windows 10 (1607+)
- Windows 8.1
- Windows 7 SP1
- Windows 伺服器核心
- Windows Server 2012 R2
- Windows Server 2016
- Windows Server 2019

### <a name="macos"></a>macOS

- macOS 10.15 "Catalina"
- macOS 10.14 "Mojave"
- macOS 10.13 "High Sierra"

### <a name="linux-x64"></a>Linux (x64)

- Red Hat Enterprise Linux 7+
- SUSE Linux Enterprise Server v12 SP2+
- Ubuntu 16.04+

## <a name="available-languages"></a>可用語言

此版本的 sqlpackage 提供下列語言版本：

sqlpackage Windows：  
[簡體中文](https://go.microsoft.com/fwlink/?linkid=2143544&clcid=0x804) | [繁體中文](https://go.microsoft.com/fwlink/?linkid=2143544&clcid=0x404) | [英文 (美國)](https://go.microsoft.com/fwlink/?linkid=2143544&clcid=0x409) | [法文](https://go.microsoft.com/fwlink/?linkid=2143544&clcid=0x40c) | [德文](https://go.microsoft.com/fwlink/?linkid=2143544&clcid=0x407) | [義大利文](https://go.microsoft.com/fwlink/?linkid=2143544&clcid=0x410) | [日文](https://go.microsoft.com/fwlink/?linkid=2143544&clcid=0x411) | [韓文](https://go.microsoft.com/fwlink/?linkid=2143544&clcid=0x412) | [葡萄牙文 (巴西)](https://go.microsoft.com/fwlink/?linkid=2143544&clcid=0x416) | [俄文](https://go.microsoft.com/fwlink/?linkid=2143544&clcid=0x419) | [西班牙文](https://go.microsoft.com/fwlink/?linkid=2143544&clcid=0x40a)

sqlpackage .NET Core Windows：  
[簡體中文](https://go.microsoft.com/fwlink/?linkid=2143496&clcid=0x804) | [繁體中文](https://go.microsoft.com/fwlink/?linkid=2143496&clcid=0x404) | [英文 (美國)](https://go.microsoft.com/fwlink/?linkid=2143496&clcid=0x409) | [法文](https://go.microsoft.com/fwlink/?linkid=2143496&clcid=0x40c) | [德文](https://go.microsoft.com/fwlink/?linkid=2143496&clcid=0x407) | [義大利文](https://go.microsoft.com/fwlink/?linkid=2143496&clcid=0x410) | [日文](https://go.microsoft.com/fwlink/?linkid=2143496&clcid=0x411) | [韓文](https://go.microsoft.com/fwlink/?linkid=2143496&clcid=0x412) | [葡萄牙文 (巴西)](https://go.microsoft.com/fwlink/?linkid=2143496&clcid=0x416) | [俄文](https://go.microsoft.com/fwlink/?linkid=2143496&clcid=0x419) | [西班牙文](https://go.microsoft.com/fwlink/?linkid=2143496&clcid=0x40a)

sqlpackage .NET Core macOS：  
[簡體中文](https://go.microsoft.com/fwlink/?linkid=2143659&clcid=0x804) | [繁體中文](https://go.microsoft.com/fwlink/?linkid=2143659&clcid=0x404) | [英文 (美國)](https://go.microsoft.com/fwlink/?linkid=2143659&clcid=0x409) | [法文](https://go.microsoft.com/fwlink/?linkid=2143659&clcid=0x40c) | [德文](https://go.microsoft.com/fwlink/?linkid=2143659&clcid=0x407) | [義大利文](https://go.microsoft.com/fwlink/?linkid=2143659&clcid=0x410) | [日文](https://go.microsoft.com/fwlink/?linkid=2143659&clcid=0x411) | [韓文](https://go.microsoft.com/fwlink/?linkid=2143659&clcid=0x412) | [葡萄牙文 (巴西)](https://go.microsoft.com/fwlink/?linkid=2143659&clcid=0x416) | [俄文](https://go.microsoft.com/fwlink/?linkid=2143659&clcid=0x419) | [西班牙文](https://go.microsoft.com/fwlink/?linkid=2143659&clcid=0x40a)

sqlpackage .NET Core Linux：  
[簡體中文](https://go.microsoft.com/fwlink/?linkid=2143497&clcid=0x804) | [繁體中文](https://go.microsoft.com/fwlink/?linkid=2143497&clcid=0x404) | [英文 (美國)](https://go.microsoft.com/fwlink/?linkid=2143497&clcid=0x409) | [法文](https://go.microsoft.com/fwlink/?linkid=2143497&clcid=0x40c) | [德文](https://go.microsoft.com/fwlink/?linkid=2143497&clcid=0x407) | [義大利文](https://go.microsoft.com/fwlink/?linkid=2143497&clcid=0x410) | [日文](https://go.microsoft.com/fwlink/?linkid=2143497&clcid=0x411) | [韓文](https://go.microsoft.com/fwlink/?linkid=2143497&clcid=0x412) | [葡萄牙文 (巴西)](https://go.microsoft.com/fwlink/?linkid=2143497&clcid=0x416) | [俄文](https://go.microsoft.com/fwlink/?linkid=2143497&clcid=0x419) | [西班牙文](https://go.microsoft.com/fwlink/?linkid=2143497&clcid=0x40a)


## <a name="next-steps"></a>後續步驟

- 深入了解 [sqlpackage](sqlpackage.md)

[Microsoft 隱私權聲明](https://go.microsoft.com/fwlink/?LinkId=521839)