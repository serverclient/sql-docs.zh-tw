---
title: RHEL：在 Linux 上安裝 SQL Server
titleSuffix: SQL Server
description: 此快速入門說明如何在 Red Hat Enterprise Linux (RHEL) 上安裝 SQL Server 2017 或 SQL Server 2019，然後使用 sqlcmd 建立及查詢資料庫。
author: VanMSFT
ms.custom: seo-lt-2019
ms.author: vanto
ms.date: 06/22/2020
ms.topic: conceptual
ms.prod: sql
ms.technology: linux
ms.assetid: 92503f59-96dc-4f6a-b1b0-d135c43e935e
ms.openlocfilehash: cb266632fccaceb4639c7fdf0cfbf5368aad2c80
ms.sourcegitcommit: 2bdf1f1ee88f4fe3e872227d025e965e95d1b2b4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98711974"
---
# <a name="quickstart-install-sql-server-and-create-a-database-on-red-hat"></a>快速入門：在 Red Hat 上安裝 SQL Server 並建立資料庫

[!INCLUDE [SQL Server - Linux](../includes/applies-to-version/sql-linux.md)]

<!--SQL Server 2017 on Linux-->
::: moniker range="= sql-server-linux-2017 || = sql-server-2017"

在本快速入門中，您會在 Red Hat Enterprise Linux (RHEL) 上安裝 SQL Server 2017 或 SQL Server 2019。 然後與 **sqlcmd** 連線，建立您的第一個資料庫並執行查詢。

::: moniker-end
<!--SQL Server 2019 on Linux-->
::: moniker range=">= sql-server-linux-ver15 || >= sql-server-ver15 "

在本快速入門中，您會在 Red Hat Enterprise Linux (RHEL) 7/8 上安裝 SQL Server 2019。 然後與 **sqlcmd** 連線，建立您的第一個資料庫並執行查詢。

::: moniker-end

> [!TIP]
> 本教學課程需要使用者輸入和網際網路連線。 如果您對[自動](sql-server-linux-setup.md#unattended)或[離線](sql-server-linux-setup.md#offline)安裝程序感興趣，請參閱 [ Linux 上的 SQL Server 安裝指引](sql-server-linux-setup.md)。

## <a name="prerequisites"></a>必要條件

<!--SQL Server 2019 on Linux-->
::: moniker range=">= sql-server-linux-ver15 || >= sql-server-ver15 "

RHEL 7.3 - 7.8, 或 8.0 - 8.3 機器搭配 **2 GB 以上** 的記憶體。

::: moniker-end

<!--SQL Server 2017 on Linux-->
::: moniker range="= sql-server-linux-2017 || = sql-server-2017"

RHEL 7.3 - 7.8, 或 8.0 - 8.3 機器搭配 **2 GB 以上** 的記憶體。

::: moniker-end

若您要在自己的機器上安裝 Red Hat Enterprise Linux，請前往 [https://access.redhat.com/products/red-hat-enterprise-linux/evaluation](https://access.redhat.com/products/red-hat-enterprise-linux/evaluation)。 您也可以在 Azure 中建立 RHEL 虛擬機器。 請參閱[使用 Azure CLI 建立和管理 Linux VM](/azure/virtual-machines/linux/tutorial-manage-vm)，並於呼叫 `az vm create`時使用 `--image RHEL`。

如果您先前已安裝 SQL Server 的 CTP 或 RC 版本，您必須先移除舊的存放庫，然後遵循這些步驟進行。 如需詳細資訊，請參閱[為 SQL Server 2017 和 2019 設定 Linux 存放庫](sql-server-linux-change-repo.md)。

如需其他系統需求，請參閱 [SQL Server 在 Linux 上的系統需求](sql-server-linux-setup.md#system)。

<!--SQL Server 2017 on Linux-->
::: moniker range="= sql-server-linux-2017 || = sql-server-2017"

## <a name="install-sql-server"></a><a id="install"></a>安裝 SQL Server

> [!NOTE]
> SQL Server 2017 從 CU20 開始支援 RHEL 8。 下列 SQL Server 2017 命令會指向 RHEL 8 存放庫。 SQL Server 需要使用 python2，但 RHEL 8 未預先安裝。 開始 SQL Server 安裝步驟之前，請執行命令，並確認已選取 python2 作為解譯器：
>
> ```
> sudo alternatives --config python
> # If not configured, install python2 and openssl10 using the following commands: 
> sudo yum install python2
> sudo yum install compat-openssl10
> # Configure python2 as the default interpreter using this command: 
> sudo alternatives --config python
> ```
> 如需詳細資訊，請參閱下列說明如何安裝 python2 並將其設為預設解譯器的部落格： https://www.redhat.com/en/blog/installing-microsoft-sql-server-red-hat-enterprise-linux-8-beta 。
>

若要在 RHEL 上設定 SQL Server，請從終端執行下列命令，安裝 **mssql-server** 套件：

1. 下載 Microsoft SQL Server 2017 Red Hat 存放庫組態檔：

   針對 RHEL7：
   
   ```bash
   sudo curl -o /etc/yum.repos.d/mssql-server.repo https://packages.microsoft.com/config/rhel/7/mssql-server-2017.repo
   ```
   
   針對 RHEL8：
   
   ```bash
   sudo curl -o /etc/yum.repos.d/mssql-server.repo https://packages.microsoft.com/config/rhel/8/mssql-server-2017.repo
   ```

   > [!TIP]
   > 如果您想要安裝 SQL Server 2019，則必須改為註冊 SQL Server 2019 存放庫。 請使用下列命令安裝 SQL Server 2019：
   >
   > 針對 RHEL7：
   >
   > ```bash
   > sudo curl -o /etc/yum.repos.d/mssql-server.repo https://packages.microsoft.com/config/rhel/7/mssql-server-2019.repo
   > ```
   >
   > 針對 RHEL8：
   >
   > ```bash
   > sudo curl -o /etc/yum.repos.d/mssql-server.repo https://packages.microsoft.com/config/rhel/8/mssql-server-2019.repo
   > ```

2. 執行下列命令安裝 SQL Server：

   ```bash
   sudo yum install -y mssql-server
   ```

3. 套件安裝完成之後，請執行 **mssql-conf setup** 並遵循提示設定 SA 密碼，然後選擇您的版本。

   ```bash
   sudo /opt/mssql/bin/mssql-conf setup
   ```

   > [!TIP]
   > 下列 SQL Server 2017 版本是免費授權的版本：Evaluation、Developer 和 Express。

   > [!NOTE]
   > 請務必為 SA 帳戶指定強式密碼 (最小長度為 8 個字元，包括大小寫字母、以 10 為基底的數字及/或非英數字元符號)。

4. 完成設定之後，請確認服務正在執行：

   ```bash
   systemctl status mssql-server
   ```

5. 若要允許遠端連線，請在 RHEL 的防火牆上開啟 SQL Server 連接埠。 SQL Server 連接埠預設為 TCP 1433。 如果您正在使用防火牆的 **FirewallD**，可以使用下列命令：

   ```bash
   sudo firewall-cmd --zone=public --add-port=1433/tcp --permanent
   sudo firewall-cmd --reload
   ```

此時，SQL Server 正在您的 RHEL 機器上執行並可立即使用！

::: moniker-end
<!--SQL Server 2019 on Linux-->
::: moniker range=">= sql-server-linux-ver15 || >= sql-server-ver15 "

## <a name="install-sql-server"></a><a id="install"></a>安裝 SQL Server

> [!NOTE]
> 下列 SQL Server 2019 命令會指向 RHEL 8 存放庫。 SQL Server 需要使用 python2，但 RHEL 8 未預先安裝。 開始 SQL Server 安裝步驟之前，請執行命令，並確認已選取 python2 作為解譯器： 
>
> ```
> sudo alternatives --config python
> # If not configured, install python2 and openssl10 using the following commands: 
> sudo yum install python2
> sudo yum install compat-openssl10
> # Configure python2 as the default interpreter using this command: 
> sudo alternatives --config python
> ``` 
> 
> 如需這些步驟的詳細資訊，請參閱下列說明如何安裝 python2 並將其設為預設解譯器的部落格： https://www.redhat.com/en/blog/installing-microsoft-sql-server-red-hat-enterprise-linux-8-beta 。
> 

若要在 RHEL 上設定 SQL Server，請從終端執行下列命令，安裝 **mssql-server** 套件：

1. 下載 Microsoft SQL Server 2019 Red Hat 存放庫設定檔：
   
   針對 RHEL7：
   
   ```bash
   sudo curl -o /etc/yum.repos.d/mssql-server.repo https://packages.microsoft.com/config/rhel/7/mssql-server-2019.repo
   ```
   
   針對 RHEL8：
   
   ```bash
   sudo curl -o /etc/yum.repos.d/mssql-server.repo https://packages.microsoft.com/config/rhel/8/mssql-server-2019.repo
   ```

2. 執行下列命令安裝 SQL Server：

   ```bash
   sudo yum install -y mssql-server
   ```

3. 套件安裝完成之後，請執行 **mssql-conf setup** 並遵循提示設定 SA 密碼，然後選擇您的版本。

   ```bash
   sudo /opt/mssql/bin/mssql-conf setup
   ```

   > [!NOTE]
   > 請務必為 SA 帳戶指定強式密碼 (最小長度為 8 個字元，包括大小寫字母、以 10 為基底的數字及/或非英數字元符號)。

4. 完成設定之後，請確認服務正在執行：

   ```bash
   systemctl status mssql-server
   ```

5. 若要允許遠端連線，請在 RHEL 的防火牆上開啟 SQL Server 連接埠。 SQL Server 連接埠預設為 TCP 1433。 如果您正在使用防火牆的 **FirewallD**，可以使用下列命令：

   ```bash
   sudo firewall-cmd --zone=public --add-port=1433/tcp --permanent
   sudo firewall-cmd --reload
   ```

此時，SQL Server 2019 即已在您的 RHEL 機器上執行，並可立即使用！

::: moniker-end

## <a name="install-the-sql-server-command-line-tools"></a><a id="tools"></a>安裝 SQL Server 命令列工具

若要建立資料庫，您必須與可在 SQL Server 上執行 Transact-SQL 陳述式的工具連線。 下列步驟會安裝 SQL Server 命令列工具：[sqlcmd](../tools/sqlcmd-utility.md) 和 [bcp](../tools/bcp-utility.md)。

1. 下載 Microsoft Red Hat 存放庫組態檔。

   針對 RHEL7：
   
   ```bash
   sudo curl -o /etc/yum.repos.d/msprod.repo https://packages.microsoft.com/config/rhel/7/prod.repo
   ```
   
   針對 RHEL8：
   
   ```bash
   sudo curl -o /etc/yum.repos.d/msprod.repo https://packages.microsoft.com/config/rhel/8/prod.repo
   ```

1. 如果您已安裝舊版的 **mssql-tools**，請移除所有舊版的 unixODBC 套件。

   ```bash
   sudo yum remove unixODBC-utf16 unixODBC-utf16-devel
   ```

1. 執行下列命令，使用 unixODBC 開發人員套件安裝 **mssql-tools**。 如需詳細資訊，請參閱安裝 [Microsoft ODBC Driver for SQL Server (Linux)](../connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server.md)。

   ```bash
   sudo yum install -y mssql-tools unixODBC-devel
   ```

1. 方便起見，請將 `/opt/mssql-tools/bin/` 新增至您的 **PATH** 環境變數。 這可讓您不需要指定完整路徑，即可執行工具。 執行下列命令，修改登入工作階段和互動式/非登入工作階段的 **PATH**：

   ```bash
   echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bash_profile
   echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc
   source ~/.bashrc
   ```

[!INCLUDE [Connect, create, and query data](../includes/sql-linux-quickstart-connect-query.md)]
