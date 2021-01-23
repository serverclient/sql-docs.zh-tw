---
title: 下載並安裝 Azure Data Studio
description: 下載及安裝適用於 Windows、macOS 或 Linux 的 Azure Data Studio。 本文提供發行日期、版本號碼、系統需求及下載連結。
ms.prod: azure-data-studio
ms.technology: azure-data-studio
ms.topic: overview
author: yualan
ms.author: alayu
ms.reviewer: maghan
ms.custom: seodec18
ms.date: 1/22/2020
ms.openlocfilehash: 65daa495933d0dc974b6ae4d1c14ff9e59bd6249
ms.sourcegitcommit: 2bdf1f1ee88f4fe3e872227d025e965e95d1b2b4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98711964"
---
# <a name="download-and-install-azure-data-studio"></a>下載並安裝 Azure Data Studio

Azure Data Studio 是供資料專業人員在 Windows、macOS 和 Linux 上使用內部部署和雲端資料平台的跨平台資料庫工具。

Azure Data Studio 提供新式編輯器體驗，其中包含 IntelliSense、程式碼片段、原始檔控制整合及整合式終端。 在工程設計時，考量到資料平台使用者，並內建查詢結果集的圖表和可自訂的儀表板。 如需 Azure Data Studio 的詳細資訊，請瀏覽[什麼是 Azure Data Studio](what-is-azure-data-studio.md)。

## <a name="download-the-latest-release"></a>下載最新版本

| 平台 | 下載 | 發行日期 | 版本 |
|----------|----------|--------------|---------|
| Windows | [使用者安裝程式 (建議)](https://go.microsoft.com/fwlink/?linkid=2150927)<br>[系統安裝程式](https://go.microsoft.com/fwlink/?linkid=2150928)<br>[.zip](https://go.microsoft.com/fwlink/?linkid=2151312) | 2020 年 1 月 22 日 | 1.25.2 |
| macOS | [.zip](https://go.microsoft.com/fwlink/?linkid=2151311) | 2020 年 1 月 22 日 | 1.25.2 |
| Linux | [.deb](https://go.microsoft.com/fwlink/?linkid=2151506)<br>[.rpm](https://go.microsoft.com/fwlink/?linkid=2151407)<br>[.tar.gz](https://go.microsoft.com/fwlink/?linkid=2151508) | 2020 年 1 月 22 日 | 1.25.2 |

**如需最新版本的詳細資訊，請參閱 [版本資訊](./release-notes-azure-data-studio.md)。**

## <a name="get-azure-data-studio-for-windows"></a>取得適用於 Windows 的 Azure Data Studio

[!INCLUDE [ssms-ads-install](../includes/ssms-azure-data-studio-install.md)]

此版本的 Azure Data Studio 包含標準的 Windows Installer 體驗和 .zip 檔案。

建議使用「使用者安裝程式」，因為這不需要系統管理員權限，可簡化安裝和升級程序。 使用者安裝程式位於使用者的 Local AppData (LOCALAPPDATA) 資料夾底下，因此不需要系統管理員權限。 使用者安裝程式也提供更流暢的背景更新體驗。 如需詳細資訊，請參閱 [User setup for Windows](https://code.visualstudio.com/updates/v1_26#_user-setup-for-windows) (適用於 Windows 的使用者安裝程式)。

**使用者安裝程式** (建議)

1. 下載並執行[適用於 Windows 的 Azure Data Studio 使用者安裝程式](https://go.microsoft.com/fwlink/?linkid=2150927)。
2. 啟動 Azure Data Studio 應用程式。

**系統安裝程式**

1. 下載並執行[適用於 Windows 的 Azure Data Studio 系統安裝程式](https://go.microsoft.com/fwlink/?linkid=2150928)。
2. 啟動 Azure Data Studio 應用程式。

**壓縮檔**

1. 下載[適用於 Windows 的 Azure Data Studio .zip](https://go.microsoft.com/fwlink/?linkid=2151312)。
2. 瀏覽至下載的檔案並解壓縮。
3. `\azuredatastudio-windows\azuredatastudio.exe`執行

## <a name="get-azure-data-studio-for-macos"></a>取得適用於 macOS 的 Azure Data Studio

1. 下載[適用於 macOS 的 Azure Data Studio](https://go.microsoft.com/fwlink/?linkid=2151311)。
2. 若要展開壓縮檔的內容，請按兩下檔案。
3. 若要在「啟動控制板」中提供 Azure Data Studio，請將 *Azure Data Studio.app* 拖曳至 [應用程式] 資料夾。

## <a name="get-azure-data-studio-for-linux"></a>取得適用於 Linux 的 Azure Data Studio

1. 使用下列其中一種安裝程式或 tar.gz 封存，以下載適用於 Linux 的 Azure Data Studio：
    - [.deb](https://go.microsoft.com/fwlink/?linkid=2151506)
    - [.rpm](https://go.microsoft.com/fwlink/?linkid=2151407)
    - [.tar.gz](https://go.microsoft.com/fwlink/?linkid=2151508)
1. 若要解壓縮檔案並啟動 Azure Data Studio，請開啟新的終端機視窗並鍵入下列命令：

   **Debian 安裝：**

   ```bash
   cd ~
   sudo dpkg -i ./Downloads/azuredatastudio-linux-<version string>.deb

   azuredatastudio
   ```

   **rpm 安裝：**

   ```bash
   cd ~
   yum install ./Downloads/azuredatastudio-linux-<version string>.rpm

   azuredatastudio
   ```

   **tar.gz 安裝：**

   ```bash
   cd ~
   cp ~/Downloads/azuredatastudio-linux-<version string>.tar.gz ~ 
   tar -xvf ~/azuredatastudio-linux-<version string>.tar.gz 
   echo 'export PATH="$PATH:~/azuredatastudio-linux-x64"' >> ~/.bashrc
   source ~/.bashrc
   azuredatastudio
   ```

   > [!NOTE]
   > 在 Debian、Redhat 和 Ubuntu 上，您可能遺失相依性。 使用下列命令，根據您的 Linux 版本安裝這些相依性：

   **Debian：**

   ```bash
   sudo apt-get install libunwind8
   ```

   **Redhat:**

   ```bash
   yum install libXScrnSaver
   ```

   **Ubuntu:**

   ```bash
   sudo apt-get install libxss1

   sudo apt-get install libgconf-2-4

   sudo apt-get install libunwind8
   ```

## <a name="download-insiders-build-of-azure-data-studio"></a>下載 Azure Data Studio 的測試人員組建

一般而言，使用者應該下載上述穩定的 Azure Data Studio 版本。 不過，如果想要試用搶鮮版 (Beta) 功能並提供意見反應，則可下載 [Azure Data Studio 的測試人員組建](https://github.com/microsoft/azuredatastudio#try-out-the-latest-insiders-build-from-main)。

## <a name="supported-operating-systems"></a>支援的作業系統

Azure Data Studio 可在 Windows、macOS 和 Linux 上執行，且受下列平台支援：

### <a name="windows"></a>Windows

- Windows 10 (64 位元)
- Windows 8.1 (64 位元)
- Windows 8 (64 位元)
- Windows 7 (SP1)
- Windows Server 2019
- Windows Server 2016
- Windows Server 2012 R2 (64 位元)
- Windows Server 2012 (64 位元)
- Windows Server 2008 R2 (64 位元)

### <a name="macos"></a>macOS

- macOS 10.15 Catalina
- macOS 10.14 Mojave
- macOS 10.13 High Sierra
- macOS 10.12 Sierra
- macOS 11.1  Big Sur

### <a name="linux"></a>Linux

- Red Hat Enterprise Linux 7.4
- Red Hat Enterprise Linux 7.3
- SUSE Linux Enterprise Server v12 SP2
- Ubuntu 16.04

## <a name="recommended-system-requirements"></a>建議的系統需求

| 推薦項目/基本安裝 | CPU 核心 | 記憶體/RAM |
|---------------------|-----------|------------|
|     建議     |     4     |   8 GB     |
|     最小值         |     2     |   4 GB     |

## <a name="check-for-updates"></a>檢查更新

若要檢查是否有最新的更新，請選取視窗左下方的齒輪圖示，然後選取 [檢查更新]。

若要套用離線環境的更新，您可直接[安裝最新版本](#download-and-install-azure-data-studio)以覆蓋先前安裝的版本。 您不需要解除安裝舊版 Azure Data Studio。 安裝程式會更新目前安裝的應用程式 (如果有的話)。

## <a name="supported-sql-offerings"></a>支援的 SQL 供應項目

- 此版本的 Azure Data Studio 適用所有 [SQL Server 2014 - [!INCLUDE[sql-server-2019](../includes/sssqlv15-MD.md)] 的支援版本](https://support.microsoft.com/lifecycle?C2=1044)，並支援使用 Azure SQL Database 及 Azure Synapse Analytic 中最新的雲端功能。 Azure Data Studio 也提供 Azure SQL 受控執行個體的預覽支援。

## <a name="move-user-settings"></a>移動使用者設定

如果您要將 SQL Operations Studio 更新為 Azure Data Studio，並保留自己的設定、鍵盤快速鍵或程式碼片段，請遵循下列步驟。

如果您已有 Azure Data Studio，或是從未安裝或自訂 SQL Operations Studio，則可以略過本節。

1. 選取左下方的齒輪，然後選取 [設定] 開啟設定。

   ![編輯 Azure Data Studio 中的設定](./media/download/open-settings.png)

2. 以滑鼠右鍵按一下頂端的 [使用者設定] 索引標籤，然後選取 [在總管中顯示]

   ![啟動總管，將帶您前往本機檔案系統](./media/download/reveal-in-explorer.png)

3. 複製此資料夾中的所有檔案，儲存在本機磁碟上容易找到的位置，例如文件資料夾。

   ![使用檔案並複製到您的位置](./media/download/copy-settings.png)

4. 在新版的 Azure Data Studio 中，依步驟 1-2 作業，然後在步驟 3 中，將您儲存的內容貼到資料夾。 您也可以將設定、按鍵繫結關係或程式碼片段手動複製到各自的位置。

5. 如果覆寫現有的安裝，請在安裝前先刪除舊的安裝目錄，以避免在連線到資源總管中的 Azure 帳戶時發生錯誤。

## <a name="unattended-install-for-windows"></a>在 Windows 上自動安裝

您也可以使用命令提示字元指令來安裝 Azure Data Studio。

若要在沒有 GUI 提示的背景中安裝 Azure Data Studio，而且使用 Windows 平台，請遵循下列步驟。

1. 啟動權限較高的命令提示字元。

2. 在下列命令提示字元中鍵入命令。

    ```console
    <path where the azuredatastudio-windows-user-setup-x.xx.x.exe file is located> /VERYSILENT /MERGETASKS=!runcode>
    ```

    範例：

    ```console
    %systemdrive%\azuredatastudio-windows-user-setup-1.24.0.exe /VERYSILENT /MERGETASKS=!runcode
    ```

    > [!Note]
    > 此範例也適用於系統安裝程式檔案。
    > 
    > ```console
    > <path where the azuredatastudio-windows-setup-x.xx.x.exe file is located> /VERYSILENT /MERGETASKS=!runcode>
    > ```

    您也可以傳遞 */SILENT* (而不是 */VERYSILENT*)，以查看安裝程式 UI。

3. 如果順利執行，即應該會看到 Azure Data Studio 已安裝。

## <a name="uninstall-azure-data-studio"></a>解除安裝 Azure Data Studio

如果您是使用 Windows Installer 安裝 Azure Data Studio，請以移除其他 Windows 應用程式相同的方式來解除安裝。

如果您是使用 .zip 或其他封存安裝 Azure Data Studio，請刪除這些檔案。

## <a name="next-steps"></a>後續步驟

若要開始使用，請參閱下列其中一個快速入門：

- [什麼是 Azure Data Studio](what-is-azure-data-studio.md)
- [Azure Data Studio 版本資訊](release-notes-azure-data-studio.md)
- [連線與查詢 SQL Server](quickstart-sql-server.md)
- [連線與查詢 Azure SQL Database](quickstart-sql-database.md)
- [連線並查詢 Azure Synapse Analytics](quickstart-sql-dw.md)

[!INCLUDE[get-help-sql-tools](../includes/paragraph-content/get-help-sql-tools.md)]

[!INCLUDE[contribute-to-content](../includes/paragraph-content/contribute-to-content.md)]

[Microsoft 隱私權聲明](https://go.microsoft.com/fwlink/?LinkId=521839)和[使用方式資料收集](usage-data-collection.md)。
