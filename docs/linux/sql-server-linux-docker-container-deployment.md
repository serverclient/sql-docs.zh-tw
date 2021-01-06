---
title: 部署和連線到 SQL Server Docker 容器
description: 探索如何在 Docker 容器上部署 SQL Server，並了解從容器內部與外部連線到 SQL Server 的各種工具
author: vin-yu
ms.author: vinsonyu
ms.reviewer: vanto
ms.custom: contperf-fy21q1
ms.date: 09/07/2020
ms.topic: conceptual
ms.prod: sql
ms.technology: linux
ms.assetid: 82737f18-f5d6-4dce-a255-688889fdde69
moniker: '>= sql-server-linux-2017 || >= sql-server-2017 '
zone_pivot_groups: cs1-command-shell
ms.openlocfilehash: e4524dccd4241a2400e8bf1be76bbc326149a8c6
ms.sourcegitcommit: cb8e2ce950d8199470ff1259c9430f0560f0dc1d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2021
ms.locfileid: "97878789"
---
# <a name="deploy-and-connect-to-sql-server-docker-containers"></a>部署和連線到 SQL Server Docker 容器

[!INCLUDE [SQL Server - Linux](../includes/applies-to-version/sql-linux.md)]

本文說明如何部署及連線到 SQL Server Docker 容器。

如需了解其他部署案例，請參閱：

- [Windows](../database-engine/install-windows/install-sql-server.md)
- [Linux](../linux/sql-server-linux-setup.md)
- [Kubernetes - 巨量資料叢集](../big-data-cluster/deploy-get-started.md)

> [!NOTE]
> 此文章特別著重於 mssql-server-linux 映像的使用。 Windows 映像並未涵蓋其中，但您可以在 [mssql-server-windows Docker Hub 頁面](https://hub.docker.com/r/microsoft/mssql-server-windows-developer/) \(英文\) 上深入了解。

> [!IMPORTANT]
> 為產品使用案例選擇執行 SQL Server 容器之前，請檢閱我們的 [SQL Server 容器支援原則](https://support.microsoft.com/help/4047326/support-policy-for-microsoft-sql-server) \(部分機器翻譯\) 以確定您是以支援的設定執行。

這段 6 分鐘的影片會介紹如何在容器上執行 SQL Server：

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/SQL-Server-2019-in-Containers/player?WT.mc_id=dataexposed-c9-niner]

## <a name="pull-and-run-the-container-image"></a>提取及執行容器映像

若要提取並執行適用於 SQL Server 2017 和 SQL Server 2019 的 Docker 容器映像，請遵循下列快速入門中的先決條件和步驟：

- [以 Docker 執行 SQL Server 2017 容器映像](quickstart-install-connect-docker.md?view=sql-server-2017&preserve-view=true)
- [以 Docker 執行 SQL Server 2019 容器映像](quickstart-install-connect-docker.md?view=sql-server-ver15&preserve-view=true)

本設定文章將在下列各節中提供其他使用案例。

## <a name="connect-and-query"></a>連線和查詢

您可以從容器外部或從容器內部，連線和查詢容器中的 SQL Server。 下列各節將說明這兩種案例。

### <a name="tools-outside-the-container"></a>容器外部的工具

您可以從支援 SQL 連線的任何外部 Linux、Windows 或 macOS 工具連線到 Docker 機器上的 SQL Server 執行個體。 一些常用工具包括：

- [Azure Data Studio](../azure-data-studio/quickstart-sql-server.md)
- [sqlcmd](sql-server-linux-setup-tools.md)
- [Visual Studio Code](../tools/visual-studio-code/sql-server-develop-use-vscode.md)
- [Windows 上的 SQL Server Management Studio (SSMS)](sql-server-linux-manage-ssms.md)

下列範例會使用 **sqlcmd** 來連線到在 Docker 容器中執行的 SQL Server。 連接字串中的 IP 位址是執行容器之主機電腦的 IP 位址。

::: zone pivot="cs1-bash"
```bash
sqlcmd -S 10.3.2.4 -U SA -P '<YourPassword>'
```
::: zone-end

::: zone pivot="cs1-powershell"
```PowerShell
sqlcmd -S 10.3.2.4 -U SA -P "<YourPassword>"
```
::: zone-end

::: zone pivot="cs1-cmd"
```cmd
sqlcmd -S 10.3.2.4 -U SA -P "<YourPassword>"
```
::: zone-end

如果您對應的主機連接埠不是預設的 **1433**，將該連接埠新增至連接字串。 例如，如果您在 `docker run` 命令中指定了 `-p 1400:1433`，則要明確地指定連接埠 1400 來連線。

::: zone pivot="cs1-bash"
```bash
sqlcmd -S 10.3.2.4,1400 -U SA -P '<YourPassword>'
```
::: zone-end

::: zone pivot="cs1-powershell"
```PowerShell
sqlcmd -S 10.3.2.4,1400 -U SA -P "<YourPassword>"
```
::: zone-end

::: zone pivot="cs1-cmd"
```cmd
sqlcmd -S 10.3.2.4,1400 -U SA -P "<YourPassword>"
```
::: zone-end

### <a name="tools-inside-the-container"></a>容器內部的工具

從 SQL Server 2017 開始，容器映像會包含 [SQL Server 命令列工具](sql-server-linux-setup-tools.md)。 如果您使用互動式命令提示字元來附加至映像，則可在本機執行這些工具。

1. 使用 `docker exec -it` 命令在您執行的容器中啟動互動式 Bash 殼層。 在下列範例中，`e69e056c702d` 是容器識別碼。

    ```bash
    docker exec -it e69e056c702d "bash"
    ```

    > [!TIP]
    > 您不一定要指定整個容器識別碼。 您只需指定足夠的字元來唯一識別它。 因此，在此範例中，使用 `e6` 或 `e69` 可能就已足夠。 若要尋找容器識別碼，請執行 `docker ps -a` 命令。

2. 進入容器後，以 sqlcmd 進行本機連線。 預設路徑並不包含 sqlcmd，因此您必須指定完整路徑。

    ```bash
    /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P '<YourPassword>'
    ```

3. 完成使用 sqlcmd 時，輸入 `exit`。

4. 完成使用互動式命令提示字元時，輸入 `exit`。 結束互動式 Bash 殼層後，容器會繼續執行。

## <a name="check-the-container-version"></a><a id="version"></a> 檢查容器版本

如果您想要知道執行中 Docker 容器內的 SQL Server 版本，請執行下列命令來顯示它。 以目標容器識別碼或名稱取代 `<Container ID or name>`。 以用來進行 SA 登入的 SQL Server 密碼取代 `<YourStrong!Passw0rd>`。

::: zone pivot="cs1-bash"
```bash
sudo docker exec -it <Container ID or name> /opt/mssql-tools/bin/sqlcmd \
-S localhost -U SA -P '<YourStrong!Passw0rd>' \
-Q 'SELECT @@VERSION'
```
::: zone-end

::: zone pivot="cs1-powershell"
```PowerShell
docker exec -it <Container ID or name> /opt/mssql-tools/bin/sqlcmd `
-S localhost -U SA -P "<YourStrong!Passw0rd>" `
-Q "SELECT @@VERSION"
```
::: zone-end

::: zone pivot="cs1-cmd"
```cmd
docker exec -it <Container ID or name> /opt/mssql-tools/bin/sqlcmd ^
-S localhost -U SA -P "<YourStrong!Passw0rd>" ^
-Q "SELECT @@VERSION"
```
::: zone-end

您也可以識別目標 Docker 容器映像的 SQL Server 版本和組建編號。 下列命令會顯示 **mcr.microsoft.com/mssql/server:2017-latest** 映像的 SQL Server 版本和組建資訊。 其執行方式是使用環境變數 **PAL_PROGRAM_INFO=1** 來執行新容器。 產生的容器會立即結束，而且 `docker rm` 命令會移除它。

::: zone pivot="cs1-bash"
```bash
sudo docker run -e PAL_PROGRAM_INFO=1 --name sqlver \
-ti mcr.microsoft.com/mssql/server:2019-latest && \
sudo docker rm sqlver
```
::: zone-end

::: zone pivot="cs1-powershell"
```PowerShell
docker run -e PAL_PROGRAM_INFO=1 --name sqlver `
-ti mcr.microsoft.com/mssql/server:2019-latest; `
docker rm sqlver
```
::: zone-end

::: zone pivot="cs1-cmd"
```cmd
docker run -e PAL_PROGRAM_INFO=1 --name sqlver ^
-ti mcr.microsoft.com/mssql/server:2019-latest && ^
docker rm sqlver
```
::: zone-end

先前的命令會顯示類似下列輸出的版本資訊：

```output
sqlservr
  Version 15.0.4063.15
  Build ID 8a3bb4cca325e1d0b3071b3a193f6a1d74b440fbd95d2fb18881651a5b9ec8e8
  Build Type release
  Git Version 0335c462
  Built at Fri Aug 28 04:50:27 GMT 2020

PAL
  Build ID cc5ceea1b3d294f7d0166f99932f98c7eacfaaa81bcd7cf23c6a89f785829b63
  Build Type release
  Git Version ae9d66dff
  Built at Fri Aug 28 04:46:48 GMT 2020

Packages
  system.security                         6.2.9200.10,unset,
  system.certificates                     6.2.9200.10,unset,
  secforwarderxplat                       15.0.4063.15
  sqlservr                                15.0.4063.15
  system.common                           10.0.17134.1246.202005133
  system.netfx                            4.7.2.461814
  system                                  6.2.9200.10,unset,
  sqlagent                                15.0.4063.15
```

## <a name="run-a-specific-sql-server-container-image"></a><a id="tags"></a> 執行特定的 SQL Server 容器映像

> [!NOTE]
> 從 SQL Server 2019 CU3 開始支援 Ubuntu 18.04。 您可在 <https://mcr.microsoft.com/v2/mssql/server/tags/list> 擷取到 mssql/server 所有可用標籤的清單。

在某些情況下，您可能不想使用最新的 SQL Server 容器映像。 若要執行特定的 SQL Server 容器映像，請使用下列步驟：

1. 識別您想要使用之版本的 Docker **標記**。 若要檢視所有可用的標記，請參閱 [mssql-server-linux Docker Hub 頁面](https://hub.docker.com/_/microsoft-mssql-server) \(英文\)。

2. 使用標記來提取 SQL Server 容器映像。 例如，若要提取 2019-CU7-ubuntu-18.04 映像，請將下列命令中的 `<image_tag>` 替換成 `2019-CU7-ubuntu-18.04`。

   ```bash
   docker pull mcr.microsoft.com/mssql/server:<image_tag>
   ```

3. 若要使用該映像來執行新容器，則在 `docker run` 命令中指定標記名稱。 在下列命令中，以您想要執行的版本取代 `<image_tag>`。

   ::: zone pivot="cs1-bash"
   ```bash
   docker run -e 'ACCEPT_EULA=Y' -e 'MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>' -p 1401:1433 -d mcr.microsoft.com/mssql/server:<image_tag>
   ```
   ::: zone-end

   ::: zone pivot="cs1-powershell"
   ```PowerShell
   docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>" -p 1401:1433 -d mcr.microsoft.com/mssql/server:<image_tag>
   ```
   ::: zone-end

   ::: zone pivot="cs1-cmd"
   ```cmd
   docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>" -p 1401:1433 -d mcr.microsoft.com/mssql/server:<image_tag>
   ```
   ::: zone-end

這些步驟也可以用來將現有的容器降級。 例如，您可能想要復原或降級執行中的容器，以進行疑難排解或測試。 若要將執行中的容器降級，您必須使用適用於資料資料夾的持續性技術。 遵循[升級小節](#upgrade)中概述的相同步驟，但在執行新容器時指定較舊版本的標記名稱。

<!--SQL Server 2019 on Linux-->
::: moniker range=">= sql-server-linux-ver15 || >= sql-server-ver15 "

## <a name="run-rhel-based-container-images"></a><a id="rhel"></a> 執行 RHEL 型容器映像

適用於 SQL Server Linux 容器映像的文件都指向 Ubuntu 型容器。 從 SQL Server 2019 開始，您可以使用 Red Hat Enterprise Linux (RHEL) 型容器。 RHEL 的映像範例看起來會像是 **mcr.microsoft.com/mssql/rhel/server:2019-CU1-rhel-8**。

例如，下列命令會為使用 RHEL 8 的 SQL Server 2019 容器提取累積更新 1：

::: zone pivot="cs1-bash"
```bash
sudo docker pull mcr.microsoft.com/mssql/rhel/server:2019-CU1-rhel-8
```
::: zone-end

::: zone pivot="cs1-powershell"
```PowerShell
docker pull mcr.microsoft.com/mssql/rhel/server:2019-CU1-rhel-8
```
::: zone-end

::: zone pivot="cs1-cmd"
```cmd
docker pull mcr.microsoft.com/mssql/rhel/server:2019-CU1-rhel-8
```
::: zone-end

::: moniker-end

## <a name="run-production-container-images"></a><a id="production"></a> 執行生產容器映像

上一節中的[快速入門](quickstart-install-connect-docker.md)會執行來自 Docker Hub 的免費 SQL Server 開發人員版本。 如果您想要執行生產容器映像 (例如 Enterprise、Standard 或 Web 版本)，大部分的資訊仍然適用。 但仍有一些此處概述的差異。

- 如果您具備有效授權，則只能在生產環境中使用 SQL Server。 您可以在[此處](https://go.microsoft.com/fwlink/?linkid=857693)取得免費的 SQL Server Express 生產授權。 SQL Server Standard 和 Enterprise Edition 授權可透過 [Microsoft 大量授權](https://www.microsoft.com/licensing/default.aspx)取得。

- 開發人員容器映像也可以設定為執行生產版本。 使用下列步驟來執行生產版本：

在[快速入門](quickstart-install-connect-docker.md)中檢閱需求和執行程序。 您必須使用 **MSSQL_PID** 環境變數來指定生產版本。 下列範例示範如何針對 Enterprise Edition 執行最新的 SQL Server 2017 容器映像：

::: zone pivot="cs1-bash"
```bash
docker run --name sqlenterprise \
-e 'ACCEPT_EULA=Y' -e 'SA_PASSWORD=<YourStrong!Passw0rd>' \
-e 'MSSQL_PID=Enterprise' -p 1433:1433 \
-d mcr.microsoft.com/mssql/server:2019-latest
```
::: zone-end

::: zone pivot="cs1-powershell"
```PowerShell
docker run --name sqlenterprise `
-e "ACCEPT_EULA=Y" -e "SA_PASSWORD=<YourStrong!Passw0rd>" `
-e "MSSQL_PID=Enterprise" -p 1433:1433 `
-d "mcr.microsoft.com/mssql/server:2019-latest"
```
::: zone-end

::: zone pivot="cs1-cmd"
```cmd
docker run --name sqlenterprise `
-e "ACCEPT_EULA=Y" -e "SA_PASSWORD=<YourStrong!Passw0rd>" ^
-e "MSSQL_PID=Enterprise" -p 1433:1433 ^
-d "mcr.microsoft.com/mssql/server:2019-latest"
```
::: zone-end

> [!IMPORTANT]
> 透過將值 **Y** 傳遞至環境變數 **ACCEPT_EULA** 並將版本值傳遞至 **MSSQL_PID**，來表示您目前具備您想要使用之 SQL Server 版本的有效授權。 您也同意使用在 Docker 容器映像中執行的 SQL Server 軟體將受到 SQL Server 授權的條款所規範。

> [!NOTE]
> 如需 **MSSQL_PID** 的完整可能值清單，請參閱 [在 Linux 上使用環境變數設定 SQL Server 設定](sql-server-linux-configure-environment-variables.md)。

## <a name="run-multiple-sql-server-containers"></a><a id="multiple"></a>執行多個 SQL Server 容器

Docker 提供一種方法，可在相同的主機電腦上執行多個 SQL Server 容器。 請針對相同主機上需要多個 SQL Server 執行個體的案例使用此方法。 每個容器都必須在不同的連接埠上公開其本身。

<!--SQL Server 2017 on Linux -->
::: moniker range="= sql-server-linux-2017 || = sql-server-2017"

下列範例會建立兩個 SQL Server 2017 容器，並將它們對應至主機電腦上的連接埠 **1401** 和 **1402**。

::: zone pivot="cs1-bash"
```bash
docker run -e 'ACCEPT_EULA=Y' -e 'MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>' -p 1401:1433 -d mcr.microsoft.com/mssql/server:2017-latest
docker run -e 'ACCEPT_EULA=Y' -e 'MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>' -p 1402:1433 -d mcr.microsoft.com/mssql/server:2017-latest
```
::: zone-end

::: zone pivot="cs1-powershell"
```PowerShell
docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>" -p 1401:1433 -d mcr.microsoft.com/mssql/server:2017-latest
docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>" -p 1402:1433 -d mcr.microsoft.com/mssql/server:2017-latest
```
::: zone-end

::: zone pivot="cs1-cmd"
```cmd
docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>" -p 1401:1433 -d mcr.microsoft.com/mssql/server:2017-latest
docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>" -p 1402:1433 -d mcr.microsoft.com/mssql/server:2017-latest
```
::: zone-end

::: moniker-end
<!--SQL Server 2019 on Linux-->
::: moniker range=">= sql-server-linux-ver15 || >= sql-server-ver15 "

下列範例會建立兩個 SQL Server 2019 容器，並將容器對應至主機電腦上的連接埠 **1401** 和 **1402**。

::: zone pivot="cs1-bash"
```bash
docker run -e 'ACCEPT_EULA=Y' -e 'MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>' -p 1401:1433 -d mcr.microsoft.com/mssql/server:2019-latest
docker run -e 'ACCEPT_EULA=Y' -e 'MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>' -p 1402:1433 -d mcr.microsoft.com/mssql/server:2019-latest
```
::: zone-end

::: zone pivot="cs1-powershell"
```PowerShell
docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>" -p 1401:1433 -d mcr.microsoft.com/mssql/server:2019-latest
docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>" -p 1402:1433 -d mcr.microsoft.com/mssql/server:2019-latest
```
::: zone-end

::: zone pivot="cs1-cmd"
```cmd
docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>" -p 1401:1433 -d mcr.microsoft.com/mssql/server:2019-latest
docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>" -p 1402:1433 -d mcr.microsoft.com/mssql/server:2019-latest
```
::: zone-end

::: moniker-end

現在，有兩個在不同容器中執行的 SQL Server 執行個體。 用戶端可以使用 Docker 主機的 IP 位址和容器的連接埠號碼，來連線到每個 SQL Server 執行個體。

::: zone pivot="cs1-bash"
```bash
sqlcmd -S 10.3.2.4,1401 -U SA -P '<YourPassword>'
sqlcmd -S 10.3.2.4,1402 -U SA -P '<YourPassword>'
```
::: zone-end

::: zone pivot="cs1-powershell"
```PowerShell
sqlcmd -S 10.3.2.4,1401 -U SA -P "<YourPassword>"
sqlcmd -S 10.3.2.4,1402 -U SA -P "<YourPassword>"
```
::: zone-end

::: zone pivot="cs1-cmd"
```cmd
sqlcmd -S 10.3.2.4,1401 -U SA -P "<YourPassword>"
sqlcmd -S 10.3.2.4,1402 -U SA -P "<YourPassword>"
```
::: zone-end

## <a name="upgrade-sql-server-in-containers"></a><a id="upgrade"></a> 在容器中升級 SQL Server

若要使用 Docker 來升級容器映像，請先識別適用於您升級之版本的標記。 使用 `docker pull` 命令，從登錄中提取此版本：

```command
docker pull mcr.microsoft.com/mssql/server:<image_tag>
```

這會更新您所建立之任何新容器的 SQL Server 映像，但不會更新任何執行中容器內的 SQL Server。 若要執行此動作，您必須使用最新的 SQL Server 容器映像來建立新容器，並將您的資料移轉至該新容器。

1. 請確定您會針對現有的 SQL Server 容器使用其中一種[資料持續性技術](sql-server-linux-docker-container-configure.md#persist)。 這可讓您使用相同的資料來啟動新容器。

1. 使用 `docker stop` 命令來停止 SQL Server 容器。

1. 使用 `docker run` 來建立新的 SQL Server 容器，並指定對應的主機目錄或資料磁碟區容器。 請務必針對您的 SQL Server 升級使用特定的標記。 新容器現在會使用新的 SQL Server 版本搭配您現有的 SQL Server 資料。

   > [!IMPORTANT]
   > 目前僅支援 RC1、RC2 和 GA 之間的升級。

1. 在新容器中確認您的資料庫和資料。

1. (選擇性) 使用 `docker rm` 來移除舊容器。

## <a name="next-steps"></a>後續步驟

<!--SQL Server 2017 on Linux -->
::: moniker range="= sql-server-linux-2017 || = sql-server-2017"

- 透過此[快速入門](quickstart-install-connect-docker.md?view=sql-server-2017&preserve-view=true)，開始使用 Docker 上的 SQL Server 2017 容器映像

::: moniker-end

<!--SQL Server 2019 on Linux-->
::: moniker range=">= sql-server-linux-ver15 || >= sql-server-ver15 "

- 透過此[快速入門](quickstart-install-connect-docker.md)，開始使用 Docker 上的 SQL Server 2019 容器映像

::: moniker-end

- [參考 Docker 容器的其他組態及自訂](sql-server-linux-docker-container-configure.md)

- 請參閱 [mssql-docker GitHub 存放庫](https://github.com/Microsoft/mssql-docker)，以取得資源、意見反應及已知問題

- [針對 SQL Server Docker 容器進行疑難排解](sql-server-linux-docker-container-troubleshooting.md)

- [探索 SQL Server 容器的高可用性](sql-server-linux-container-ha-overview.md)

- [安全的 SQL Server Docker 容器](sql-server-linux-docker-container-security.md)
