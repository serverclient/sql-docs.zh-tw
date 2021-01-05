---
title: Linux 上的 SQL Server 的安裝指引
titleSuffix: SQL Server
description: 安裝、更新與解除安裝 Linux 上的 SQL Server。 此文章涵蓋線上、離線及自動安裝的情節。
author: VanMSFT
ms.author: vanto
ms.date: 06/22/2020
ms.topic: conceptual
ms.prod: sql
ms.custom: sqlfreshmay19
ms.technology: linux
ms.assetid: 565156c3-7256-4e63-aaf0-884522ef2a52
ms.openlocfilehash: 4abb6180d60b149fe8b1b18118c98afd04c2e2e8
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97642392"
---
# <a name="installation-guidance-for-sql-server-on-linux"></a>Linux 上的 SQL Server 的安裝指引

[!INCLUDE [SQL Server - Linux](../includes/applies-to-version/sql-linux.md)]

本文提供在 Linux 上安裝、更新及解除安裝 SQL Server 2017 和 SQL Server 2019 的指導方針。

如需了解其他部署案例，請參閱：

- [Windows](../database-engine/install-windows/install-sql-server.md)
- [Docker 容器](./sql-server-linux-docker-container-deployment.md)
- [Kubernetes - 巨量資料叢集](../big-data-cluster/deploy-get-started.md)

> [!TIP]
> 本指南涵蓋數個部署案例。 如果您只是要尋找逐步安裝指示，請跳到其中一個快速入門：
> - [RHEL 快速入門](quickstart-install-connect-red-hat.md)
> - [SLES 快速入門](quickstart-install-connect-suse.md)
> - [Ubuntu 快速入門](quickstart-install-connect-ubuntu.md)
> - [Docker 快速入門](quickstart-install-connect-docker.md)

如需常見問題的解答，請參閱 [Linux 上的 SQL Server 常見問題集](../linux/sql-server-linux-faq.md)。

## <a name="supported-platforms"></a><a id="supportedplatforms"></a> 支援的平台

Red Hat Enterprise Linux (RHEL)、SUSE Linux Enterprise Server (SLES) 及 Ubuntu 都支援 SQL Server。 它也支援 Docker 映像，可以在 Linux 上的 Docker 引擎 或適用於 Windows/Mac 的 Docker 上執行。

<!--SQL Server 2017 on Linux-->
::: moniker range="= sql-server-linux-2017 || = sql-server-2017"

| 平台 | 支援的版本 | Get
|-----|-----|-----
| **Red Hat Enterprise Linux** | 7.3、7.4、7.5、7.6、8  | [取得 RHEL 7.6](https://access.redhat.com/products/red-hat-enterprise-linux/evaluation)
| **SUSE Linux Enterprise Server** | v12 SP2 | [取得 SLES v12 SP2](https://www.suse.com/products/server)
| **Ubuntu** | 16.04 | [取得 Ubuntu 16.04](http://releases.ubuntu.com/xenial/)
| **Docker 引擎** | 1.8+ | [取得 Docker](https://www.docker.com/get-started)

::: moniker-end

<!--SQL Server 2019 on Linux-->
::: moniker range=">= sql-server-linux-ver15 || >= sql-server-ver15 "

[!INCLUDE [linux-supported-platfoms-2019](../includes/linux-supported-platfoms-2019.md)]

::: moniker-end

Microsoft 也支援使用 OpenShift 和 Kubernetes 來部署和管理 SQL Server 容器。

> [!NOTE]
> SQL Server 已針對先前列出的發行版本，在 Linux 上經過測試並受到支援。 但如果選擇在不支援的作業系統上安裝 SQL Server，請檢閱 [Microsoft SQL Server 的技術支援原則](https://support.microsoft.com/help/4047326/support-policy-for-microsoft-sql-server) \(機器翻譯\) 的＜支援原則＞一節以了解隱含的支援。

## <a name="system-requirements"></a><a id="system"></a> 系統需求

SQL Server 具有下列適用於 Linux 的系統需求：

|||
|-----|-----|
| **記憶體** | 2 GB |
| **檔案系統** | **XFS** 或 **EXT4** (不支援其他檔案系統，例如 **BTRFS**) |
| **磁碟空間** | 6 GB |
| **處理器速度** | 2 GHz |
| **處理器核心數** | 2 個核心 |
| **處理器類型** | 僅相容 x64 |

如果您在生產環境中使用 **網路檔案系統 (NFS)** 遠端共用，請注意下列支援需求：

- 使用 NFS 版本 **4.2 或更新的版本**。 舊版的 NFS 不支援新式檔案系統常用的必要功能，例如 fallocate 和疏鬆檔案建立。
- 僅尋找 NFS 掛接上的 **/var/opt/mssql** 目錄。 不支援其他檔案，例如 SQL Server 系統二進位檔案。
- 確定 NFS 用戶端在裝載遠端共用時使用 'nolock' 選項。

## <a name="configure-source-repositories"></a><a id="repositories"></a> 設定來源存放庫

當您安裝或升級 SQL Server 時，您會從已設定的 Microsoft 存放庫取得最新版本的 SQL Server。 快速入門會使用 SQL Server 的累積更新 **CU** 存放庫。 但是，您可以改為設定 **GDR** 存放庫。 如需存放庫和其設定方式的詳細資訊，請參閱[針對 Linux 上的 SQL Server 設定存放庫](sql-server-linux-change-repo.md)。

## <a name="install-sql-server"></a><a id="platforms"></a> 安裝 SQL Server

您可以在 Linux 上從命令列安裝 SQL Server 2017 或 SQL Server 2019。 如需逐步指示，請參閱下列其中一個快速入門：

| 平台 | 安裝快速入門 |
|---|---|
| Red Hat Enterprise Linux (RHEL) | [2017](quickstart-install-connect-red-hat.md?view=sql-server-2017&preserve-view=true) \| [2019](quickstart-install-connect-red-hat.md?view=sql-server-linux-ver15&preserve-view=true) |
| SUSE Linux Enterprise Server (SLES) | [2017](quickstart-install-connect-suse.md?view=sql-server-2017&preserve-view=true) \| [2019](quickstart-install-connect-suse.md?view=sql-server-linux-ver15&preserve-view=true) |
| Ubuntu | [2017](quickstart-install-connect-ubuntu.md?view=sql-server-2017&preserve-view=true) \| [2019](quickstart-install-connect-ubuntu.md?view=sql-server-linux-ver15&preserve-view=true) |
| Docker | [2017](quickstart-install-connect-docker.md?view=sql-server-2017&preserve-view=true) \| [2019](quickstart-install-connect-docker.md?view=sql-server-linux-ver15&preserve-view=true) |

您也可以在 Azure 虛擬機器的 Linux 上執行 SQL Server。 如需詳細資訊，請參閱[在 Azure 中佈建 SQL VM](/azure/virtual-machines/linux/sql/provision-sql-server-linux-virtual-machine?toc=/sql/toc/toc.json)。

安裝之後，請考慮進行其他設定變更以獲得最佳效能。 如需詳細資訊，請參閱 [Linux 上的 SQL Server 效能最佳做法和設定方針](sql-server-linux-performance-best-practices.md)。

## <a name="update-or-upgrade-sql-server"></a><a id="upgrade"></a> 更新或升級 SQL Server

若要將 **mssql-server** 套件更新為最新版本，請根據您的平台，使用下列其中一個命令：

| 平台 | 套件更新命令 |
|-----|-----|
| RHEL | `sudo yum update mssql-server` |
| SLES | `sudo zypper update mssql-server` |
| Ubuntu | `sudo apt-get update`<br/>`sudo apt-get install mssql-server` |

這些命令會下載最新的套件，並取代位於 `/opt/mssql/` 底下的二進位檔。 使用者產生的資料庫和系統資料庫不會受到這項作業的影響。

若要升級 SQL Server，請先[變更您設定的存放庫](sql-server-linux-change-repo.md)，將其設為所需的 SQL Server 版本。 然後使用相同的 **升級** 命令來升級 SQL Server 版本。 只有在兩個存放庫之間支援升級路徑時，才會發生這種情況。

## <a name="rollback-sql-server"></a><a id="rollback"></a> 復原 SQL Server

若要將 SQL Server 復原或降級至先前的版本，請使用下列步驟：

1. 確認要降級的目標 SQL Server 套件版本號碼。 如需套件編號的清單，請參閱[版本資訊](../linux/sql-server-linux-release-notes.md)。

1. 降級為舊版 SQL Server。 在下列命令中，將 `<version_number>` 取代為您在步驟一中確認的 SQL Server 版本號碼。

   | 平台 | 套件更新命令 |
   |-----|-----|
   | RHEL | `sudo yum downgrade mssql-server-<version_number>.x86_64` |
   | SLES | `sudo zypper install --oldpackage mssql-server=<version_number>` |
   | Ubuntu | `sudo apt-get install mssql-server=<version_number>`<br/>`sudo systemctl start mssql-server` |

> [!NOTE]
> 其僅支援降級為相同主要版本 (例如 SQL Server 2019) 中的版本。

## <a name="check-installed-sql-server-version"></a><a id="versioncheck"></a> 檢查已安裝的 SQL Server 版本

若要驗證您 Linux 上的 SQL Server 目前的版本和版次，請使用下列程序：

1. 如果尚未安裝，請安裝 [SQL Server 命令列工具](sql-server-linux-setup-tools.md)。

1. 使用 **sqlcmd** 來執行 Transact-SQL 命令，以顯示您的 SQL Server 版本和版次。

   ```bash
   sqlcmd -S localhost -U SA -Q 'select @@VERSION'
   ```

## <a name="uninstall-sql-server"></a><a id="uninstall"></a> 解除安裝 SQL Server

若要移除 Linux 上的 **mssql-server** 套件，請根據您的平台，請使用下列其中一個命令：

| 平台 | 套件移除命令 |
|-----|-----|
| RHEL | `sudo yum remove mssql-server` |
| SLES | `sudo zypper remove mssql-server` |
| Ubuntu | `sudo apt-get remove mssql-server` |

移除套件並不會刪除產生的資料庫檔案。 如果您想要刪除資料庫檔案，請使用下列命令：

```bash
sudo rm -rf /var/opt/mssql/
```

## <a name="unattended-install"></a><a id="unattended"></a> 自動安裝

您可以透過下列方式執行自動安裝：

- 遵循[快速入門](#platforms)中的初始步驟來註冊存放庫，並安裝 SQL Server。
- 當您執行 `mssql-conf setup` 時，請設定[環境變數](sql-server-linux-configure-environment-variables.md)，並使用 [`-n` (無提示)] 選項。

下列範例會使用 **MSSQL_PID** 環境變數來設定開發人員版本的 SQL Server。 它也會接受 EULA (**ACCEPT_EULA**)，並設定 SA 使用者密碼 (**MSSQL_SA_PASSWORD**)。 `-n` 參數會執行無訊息安裝，其中會從環境變數提取設定值。

```bash
sudo MSSQL_PID=Developer ACCEPT_EULA=Y MSSQL_SA_PASSWORD='<YourStrong!Passw0rd>' /opt/mssql/bin/mssql-conf -n setup
```

您也可以建立指令碼來執行其他動作。 例如，您可以安裝其他 SQL Server 套件。

如需更詳細的範例指令碼，請參閱下列範例：

- [Red Hat 自動安裝指令碼](sample-unattended-install-redhat.md)
- [SUSE 自動安裝指令碼](sample-unattended-install-suse.md)
- [Ubuntu 自動安裝指令碼](sample-unattended-install-ubuntu.md)

## <a name="offline-install"></a><a id="offline"></a> 離線安裝

如果您的 Linux 電腦無法存取[快速入門](#platforms)中使用的線上儲存機制，您可以直接下載封裝檔案。 這些套件位於 Microsoft 存放庫 [https://packages.microsoft.com](https://packages.microsoft.com) 中。

> [!TIP]
> 如果已在快速入門的步驟中順利安裝，則不需要下載或以手動方式安裝 SQL Server 套件。 本節只適用於離線情節。

1. **下載適用於您平台的資料庫引擎套件**。 在[版本資訊](../linux/sql-server-linux-release-notes.md)的 [套件詳細資料] 區段中尋找套件下載連結。

1. **將已下載套件移至您的 Linux 電腦**。 如果您使用不同電腦來下載套件，將套件移至 Linux 電腦的其中一種方式是使用 **scp** 命令。

1. **安裝資料庫引擎套件**。 根據您的平台，使用下列其中一個命令。 將此範例中的套件檔案名稱取代為您下載的確切名稱。

   | 平台 | 套件安裝命令 |
   |-----|-----|
   | RHEL | `sudo yum localinstall mssql-server_versionnumber.x86_64.rpm` |
   | SLES | `sudo zypper install mssql-server_versionnumber.x86_64.rpm` |
   | Ubuntu | `sudo dpkg -i mssql-server_versionnumber_amd64.deb` |

    > [!NOTE]
    > 您也可以使用 `rpm -ivh` 命令來安裝 RPM 套件 (RHEL 和 SLES)，但是上表中的命令會自動安裝相依性 (如果可以從已核准的存放庫中取得)。

1. **解決遺漏的相依性**：此時，您可能會遺漏某些相依性。 如果沒有，則您可以略過此步驟。 在 Ubuntu 上，如果您可以存取包含這些相依性的已核准存放庫，最簡單的解決方案就是使用 `apt-get -f install` 命令。 此命令也會完成 SQL Server 的安裝。 若要手動檢查相依性，請使用下列命令：

   | 平台 | 列出相依性命令 |
   |-----|-----|
   | RHEL | `rpm -qpR mssql-server_versionnumber.x86_64.rpm` |
   | SLES | `rpm -qpR mssql-server_versionnumber.x86_64.rpm` |
   | Ubuntu | `dpkg -I mssql-server_versionnumber_amd64.deb` |

   解決遺失的相依性之後，再次嘗試安裝 mssql-server 套件。

1. **完成 SQL Server 安裝程式**。 使用 **mssql-conf** 來完成 SQL Server 安裝程式：

   ```bash
   sudo /opt/mssql/bin/mssql-conf setup
   ```

## <a name="licensing-and-pricing"></a>授權和定價

SQL Server 在 Linux 和 Windows 上都有相同的授權。 如需 SQL Server 授權和定價的詳細資訊，請參閱[如何授權 SQL Server](https://www.microsoft.com/sql-server/sql-server-2017-pricing)。

## <a name="optional-sql-server-features"></a>SQL Server 選用功能

安裝之後，您也可以安裝或啟用選用的 SQL Server 功能。

- [SQL Server 命令列工具](sql-server-linux-setup-tools.md)
- [SQL Server Agent](sql-server-linux-setup-sql-agent.md)
- [SQL Server 全文檢索搜尋](sql-server-linux-setup-full-text-search.md)
- [機器學習服務 (R、Python)](sql-server-linux-setup-machine-learning.md)
- [SQL Server Integration Services](sql-server-linux-setup-ssis.md)

[!INCLUDE[Get Help Options](../includes/paragraph-content/get-help-options.md)]

> [!TIP]
> 如需常見問題的解答，請參閱 [Linux 上的 SQL Server 常見問題集](sql-server-linux-faq.md)。
