---
title: Ubuntu：在 Linux 上安裝 SQL Server
description: 本快速入門說明如何在 Ubuntu 上安裝 SQL Server 2017 或 SQL Server 2019，然後使用 sqlcmd 建立及查詢資料庫。
author: VanMSFT
ms.author: vanto
ms.date: 07/15/2020
ms.topic: conceptual
ms.prod: sql
ms.custom: seo-lt-2019
ms.technology: linux
ms.assetid: 31c8c92e-12fe-4728-9b95-4bc028250d85
ms.openlocfilehash: 109de9dabe9a0fb4d169d7be64448c51d9ec7384
ms.sourcegitcommit: 713e5a709e45711e18dae1e5ffc190c7918d52e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98689112"
---
# <a name="quickstart-install-sql-server-and-create-a-database-on-ubuntu"></a>快速入門：在 Ubuntu 上安裝 SQL Server 並建立資料庫
[!INCLUDE [SQL Server - Linux](../includes/applies-to-version/sql-linux.md)]


<!--SQL Server 2017 on Linux-->
::: moniker range="= sql-server-linux-2017 || = sql-server-2017"

在本快速入門中，您會在 Ubuntu 16.04/18.04 上安裝 SQL Server 2017。 然後與 **sqlcmd** 連線，建立您的第一個資料庫並執行查詢。

> [!TIP]
> 本教學課程需要使用者輸入和網際網路連線。 如果您對自動或離線安裝程序感興趣，請參閱 [Linux 上的 SQL Server 安裝指引](sql-server-linux-setup.md)。 如需支援的平台清單，請參閱[版本資訊](sql-server-linux-release-notes.md)。

::: moniker-end

<!--SQL Server 2019 on Linux-->
::: moniker range=">= sql-server-linux-ver15 || >= sql-server-ver15 "

在本快速入門中，您會在 Ubuntu 16.04/18.04 上安裝 SQL Server 2019。 然後與 **sqlcmd** 連線，建立您的第一個資料庫並執行查詢。

> [!TIP]
> 本教學課程需要使用者輸入和網際網路連線。 如果您對自動或離線安裝程序感興趣，請參閱 [Linux 上的 SQL Server 安裝指引](sql-server-linux-setup.md)。 如需支援的平台清單，請參閱[版本資訊](sql-server-linux-release-notes-2019.md)。

::: moniker-end

## <a name="prerequisites"></a>Prerequisites

<!--SQL Server 2017 on Linux-->
::: moniker range="= sql-server-linux-2017 || = sql-server-2017"

Ubuntu 16.04 或 18.04 機器必須 **至少有 2 GB** 的記憶體。

若要在自己的機器上安裝 Ubuntu 18.04，請前往 <http://releases.ubuntu.com/bionic/>。 您也可以在 Azure 中建立 Ubuntu 虛擬機器。 請參閱[使用 Azure CLI 建立和管理 Linux VM](/azure/virtual-machines/linux/tutorial-manage-vm)。

> [!NOTE]
> 目前尚未支援在 Windows 10 上使用[適用於 Linux 的 Windows 子系統](/windows/wsl/about)作為安裝目標。

如需其他系統需求，請參閱 [SQL Server 在 Linux 上的系統需求](sql-server-linux-setup.md#system)。

> [!NOTE]
> 從 SQL Server 2017 CU20 開始支援 Ubuntu 18.04。 如果您想要搭配 Ubuntu 18.04 使用此文章中的指示，請確定您使用正確的[存放庫路徑](sql-server-linux-change-repo.md) (`18.04`，而不是 `16.04`)。
>
> 如果您是以較低的版本執行 SQL Server，可能可以[修改](/archive/blogs/sql_server_team/installing-sql-server-2017-for-linux-on-ubuntu-18-04-lts) \(英文\) 設定。

::: moniker-end

<!--SQL Server 2019 on Linux-->
::: moniker range=">= sql-server-linux-ver15 || >= sql-server-ver15 "

Ubuntu 16.04 或 18.04 機器必須 **至少有 2 GB** 的記憶體。

若要在自己的機器上安裝 Ubuntu 18.04，請前往 <http://releases.ubuntu.com/bionic/>。 您也可以在 Azure 中建立 Ubuntu 虛擬機器。 請參閱[使用 Azure CLI 建立和管理 Linux VM](/azure/virtual-machines/linux/tutorial-manage-vm)。

> [!NOTE]
> 目前尚未支援在 Windows 10 上使用[適用於 Linux 的 Windows 子系統](/windows/wsl/about)作為安裝目標。

如需其他系統需求，請參閱 [SQL Server 在 Linux 上的系統需求](sql-server-linux-setup.md#system)。

::: moniker-end

<!--SQL Server 2017 on Linux-->
::: moniker range="= sql-server-linux-2017 || = sql-server-2017"

## <a name="install-sql-server"></a><a id="install"></a>安裝 SQL Server

> [!NOTE]
> 下列 SQL Server 2017 命令會指向 Ubuntu 18.04 存放庫。 如果您正在使用 Ubuntu 16.04，請將下列路徑變更為 `/ubuntu/16.04/`，而非 `/ubuntu/18.04/`。

若要在 Ubuntu 上設定 SQL Server，請從終端執行下列命令，安裝 **mssql-server** 套件。

1. 匯入公開存放庫 GPG 金鑰：

   ```bash
   wget -qO- https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add -
   ```

2. 註冊 Microsoft SQL Server Ubuntu 存放庫：
   
   針對 Ubuntu 16.04：
   
   ```bash
   sudo add-apt-repository "$(wget -qO- https://packages.microsoft.com/config/ubuntu/16.04/mssql-server-2017.list)"
   ```
   
   針對 Ubuntu 18.04：
   
   ```bash
   sudo add-apt-repository "$(wget -qO- https://packages.microsoft.com/config/ubuntu/18.04/mssql-server-2017.list)"
   ```

   > [!TIP]
   > 如果您想要安裝 SQL Server 2019，則必須改為註冊 SQL Server 2019 存放庫。 請使用下列命令安裝 SQL Server 2019：
   >
   > 針對 Ubuntu 16.04：
   >
   > ```bash
   > sudo add-apt-repository "$(wget -qO- https://packages.microsoft.com/config/ubuntu/16.04/mssql-server-2019.list)"
   > ```
   >
   > 針對 Ubuntu 18.04：
   >
   > ```bash
   > sudo add-apt-repository "$(wget -qO- https://packages.microsoft.com/config/ubuntu/18.04/mssql-server-2019.list)"
   > ```

3. 執行下列命令安裝 SQL Server：

   ```bash
   sudo apt-get update
   sudo apt-get install -y mssql-server
   ```

4. 套件安裝完成之後，請執行 **mssql-conf setup** 並遵循提示設定 SA 密碼，然後選擇您的版本。

   ```bash
   sudo /opt/mssql/bin/mssql-conf setup
   ```

   > [!TIP]
   > 下列 SQL Server 2017 版本是免費授權的版本：Evaluation、Developer 和 Express。

   > [!NOTE]
   > 請務必為 SA 帳戶指定強式密碼 (最小長度為 8 個字元，包括大小寫字母、以 10 為基底的數字及/或非英數字元符號)。

5. 完成設定之後，請確認服務正在執行：

   ```bash
   systemctl status mssql-server --no-pager
   ```

6. 如果您想要進行遠端連線，可能還需要在防火牆上開啟 SQL Server TCP 通訊埠 (預設值 1433)。

此時，SQL Server 正在您的 Ubuntu 機器上執行並可立即使用！

::: moniker-end

<!--SQL Server 2019 on Linux-->
::: moniker range=">= sql-server-linux-ver15 || >= sql-server-ver15 "

## <a name="install-sql-server"></a><a id="install"></a>安裝 SQL Server

> [!NOTE]
> 下列 SQL Server 2019 命令會指向 Ubuntu 18.04 存放庫。 如果您正在使用 Ubuntu 16.04，請將下列路徑變更為 `/ubuntu/16.04/`，而非 `/ubuntu/18.04/`。

若要在 Ubuntu 上設定 SQL Server，請從終端執行下列命令，安裝 **mssql-server** 套件。

1. 匯入公開存放庫 GPG 金鑰：

   ```bash
   wget -qO- https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add -
   ```

2. 為 SQL Server 2019 註冊 Microsoft SQL Server Ubuntu 存放庫：
   
   針對 Ubuntu 16.04：
   
   ```bash
   sudo add-apt-repository "$(wget -qO- https://packages.microsoft.com/config/ubuntu/16.04/mssql-server-2019.list)"
   ```
   
   針對 Ubuntu 18.04：
   
   ```bash
   sudo add-apt-repository "$(wget -qO- https://packages.microsoft.com/config/ubuntu/18.04/mssql-server-2019.list)"
   ```

3. 執行下列命令安裝 SQL Server：

   ```bash
   sudo apt-get update
   sudo apt-get install -y mssql-server
   ```

4. 套件安裝完成之後，請執行 **mssql-conf setup** 並遵循提示設定 SA 密碼，然後選擇您的版本。

   ```bash
   sudo /opt/mssql/bin/mssql-conf setup
   ```

   > [!NOTE]
   > 請務必為 SA 帳戶指定強式密碼 (最小長度為 8 個字元，包括大小寫字母、以 10 為基底的數字及/或非英數字元符號)。

5. 完成設定之後，請確認服務正在執行：

   ```bash
   systemctl status mssql-server --no-pager
   ```

6. 如果您想要進行遠端連線，可能還需要在防火牆上開啟 SQL Server TCP 通訊埠 (預設值 1433)。

此時，SQL Server 2019 即已在您的 Ubuntu 機器上執行，並可立即使用！

::: moniker-end

## <a name="install-the-sql-server-command-line-tools"></a><a id="tools"></a>安裝 SQL Server 命令列工具

若要建立資料庫，您必須與可在 SQL Server 上執行 Transact-SQL 陳述式的工具連線。 下列步驟會安裝 SQL Server 命令列工具：[sqlcmd](../tools/sqlcmd-utility.md) 和 [bcp](../tools/bcp-utility.md)。

使用下列步驟在 Ubuntu 上安裝 **mssql-tools**。 

1. 匯入公開存放庫 GPG 金鑰。

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add -
   ```

1. 註冊 Microsoft Ubuntu 存放庫。
   
   針對 Ubuntu 16.04：
   
   ```bash
   curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list | sudo tee /etc/apt/sources.list.d/msprod.list
   ```

   針對 Ubuntu 18.04：
   
   ```bash
   curl https://packages.microsoft.com/config/ubuntu/18.04/prod.list | sudo tee /etc/apt/sources.list.d/msprod.list
   ```

1. 更新來源清單，並使用 unixODBC 開發人員套件執行安裝命令。 如需詳細資訊，請參閱安裝 [Microsoft ODBC Driver for SQL Server (Linux)](../connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server.md)。

   ```bash
   sudo apt-get update 
   sudo apt-get install mssql-tools unixodbc-dev
   ```

   > [!Note] 
   > 若要更新為最新版本的 **mssql-tools**，請執行下列命令：
   >
   > ```bash
   > sudo apt-get update 
   > sudo apt-get install mssql-tools 
   > ```

1. **選擇性**：在 Bash Shell 中將 `/opt/mssql-tools/bin/` 新增至您的 **PATH** 環境變數。

   若要讓登入工作階段的 Bash Shell 可存取 **sqlcmd/bcp**，請使用下列命令修改您在 **~/.bash_profile** 檔案中的 **PATH**：

   ```bash
   echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bash_profile
   ```

   若要讓互動式/非登入工作階段的 Bash Shell 可存取 **sqlcmd/bcp**，請使用下列命令修改 **~/.bashrc** 檔案中的 **PATH**：

   ```bash
   echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc
   source ~/.bashrc
   ```

[!INCLUDE [Connect, create, and query data](../includes/sql-linux-quickstart-connect-query.md)]
