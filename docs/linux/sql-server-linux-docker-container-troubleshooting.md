---
title: 針對 SQL Server Docker 容器進行疑難排解
description: 探索可用來解決搭配 SQL Server 映像使用 Linux Docker 容器時常見錯誤的不同疑難排解技術
author: vin-yu
ms.author: vinsonyu
ms.reviewer: vanto
ms.custom: contperf-fy21q1
ms.date: 09/07/2020
ms.topic: conceptual
ms.prod: sql
ms.technology: linux
moniker: '>= sql-server-linux-2017 || >= sql-server-2017'
zone_pivot_groups: cs1-command-shell
ms.openlocfilehash: d94ae00cb8f3581be51f53d999068e60c4d5b7f1
ms.sourcegitcommit: cb8e2ce950d8199470ff1259c9430f0560f0dc1d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2021
ms.locfileid: "97878779"
---
# <a name="troubleshooting-sql-server-docker-containers"></a>針對 SQL Server Docker 容器進行疑難排解

[!INCLUDE [SQL Server - Linux](../includes/applies-to-version/sql-linux.md)]

本文討論部署及使用 SQL Server Docker 容器時的常見錯誤，並提供可協助解決問題的疑難排解技術。

## <a name="docker-command-errors"></a>Docker 命令錯誤

如果您收到有關任何 `docker` 命令的錯誤，請確定 Docker 服務正在執行，並嘗試以提升的權限執行。

例如，在 Linux 上，您可能會在執行 `docker` 命令時收到下列錯誤：

```output
Cannot connect to the Docker daemon. Is the docker daemon running on this host?
```

如果您在 Linux 上收到此錯誤，請嘗試執行前面已加上 `sudo` 的相同命令。 如果該動作失敗，確認 Docker 服務正在執行，並視需要啟動它。

```bash
sudo systemctl status docker
sudo systemctl start docker
```

在 Windows 上，確認您是以系統管理員身分啟動 PowerShell 或命令提示字元。

## <a name="sql-server-container-startup-errors"></a>SQL Server 容器啟動錯誤

如果 SQL Server 容器無法執行，請嘗試下列測試：

- 若收到如 `failed to create endpoint CONTAINER_NAME on network bridge. Error starting proxy: listen tcp 0.0.0.0:1433 bind: address already in use.` 的錯誤，即表示正在嘗試將容器連接埠 1433 對應到已在使用中的連接埠。 如果您在主機電腦上本機執行 SQL Server，就會發生這種情況。 如果您啟動兩個 SQL Server 容器，並嘗試將它們都對應到相同的主機連接埠，也可能發生此問題。 如果發生這種情況，請使用 `-p` 參數，將容器連接埠 1433 對應到不同的主機連接埠。 例如： 

    <!--SQL Server 2017 on Linux -->
    ::: moniker range="= sql-server-linux-2017 || = sql-server-2017"
    
    ::: zone pivot="cs1-bash"
    ```bash
    docker run -e 'ACCEPT_EULA=Y' -e 'MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>' -p 1400:1433 -d mcr.microsoft.com/mssql/server:2017-latest`.
    ```
    ::: zone-end
    
    ::: zone pivot="cs1-powershell"
    ```PowerShell
    docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>" -p 1400:1433 -d mcr.microsoft.com/mssql/server:2017-latest`.
    ```
    ::: zone-end
    
    ::: zone pivot="cs1-cmd"
    ```cmd
    docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>" -p 1400:1433 -d mcr.microsoft.com/mssql/server:2017-latest`.
    ```
    ::: zone-end
    
    ::: moniker-end
    
    <!--SQL Server 2019 on Linux-->
    ::: moniker range=">= sql-server-linux-ver15 || >= sql-server-ver15"
    
    ::: zone pivot="cs1-bash"
    ```bash
    docker run -e 'ACCEPT_EULA=Y' -e 'MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>' -p 1400:1433 -d mcr.microsoft.com/mssql/server:2019-latest`.
    ```
    ::: zone-end
    
    ::: zone pivot="cs1-powershell"
    ```PowerShell
    docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>" -p 1400:1433 -d mcr.microsoft.com/mssql/server:2019-latest`.
    ```
    ::: zone-end
    
    ::: zone pivot="cs1-cmd"
    ```cmd
    docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>" -p 1400:1433 -d mcr.microsoft.com/mssql/server:2019-latest`.
    ```
    ::: zone-end
    
    ::: moniker-end

- 若在嘗試啟動容器時收到如 `Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Get http://%2Fvar%2Frun%2Fdocker.sock/v1.30tdout=1&tail=all: dial unix /var/run/docker.sock: connect: permission denied` 的錯誤，請在 Ubuntu 中將使用者新增到 Docker 群組。 接著登出並重新登入，因為此變更將影響新的工作階段。 

   ```bash
    usermod -aG docker $USER
   ```

- 檢查以查看是否有任何來自容器的錯誤訊息。

   ```bash
   docker logs e69e056c702d
   ```

- 確定您符合快速入門文章之[先決條件](quickstart-install-connect-docker.md#requirements)一節中所指定的最小記憶體和磁碟需求。

- 如果您使用任何容器管理軟體，請確定它支援以 root 身分執行容器程序。 容器中的 sqlservr 程序會以 root 身分執行。

- 若 SQL Server Docker 容器在啟動後立即結束，請檢查 Docker 記錄。 若正在 Windows 上的 PowerShell 使用 `docker run` 命令，請使用雙引號，而非單引號。 若是 PowerShell Core，請使用單引號。

- 檢閱 [SQL Server 設定和錯誤記錄檔](#errorlogs)。

## <a name="enable-dump-captures"></a>啟用傾印擷取

如果 SQL Server 程序在容器內部失敗，則您應該建立已啟用 **SYS_PTRACE** 的新容器。 這會新增 Linux 功能來追蹤程序，其在例外狀況上建立傾印檔案時是必要的。 支援人員可以使用傾印檔案來協助進行問題的疑難排解。 下列 docker run 命令會啟用此功能。

<!--SQL Server 2017 on Linux -->
::: moniker range="= sql-server-linux-2017 || = sql-server-2017"

::: zone pivot="cs1-bash"
```bash
docker run -e 'ACCEPT_EULA=Y' -e 'MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>' -e 'MSSQL_PID=Developer' --cap-add SYS_PTRACE -p 1401:1433 -d mcr.microsoft.com/mssql/server:2017-latest
```
::: zone-end

::: zone pivot="cs1-powershell"
```PowerShell
docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>" -e "MSSQL_PID=Developer" --cap-add SYS_PTRACE -p 1401:1433 -d mcr.microsoft.com/mssql/server:2017-latest
```
::: zone-end

::: zone pivot="cs1-cmd"
```cmd
docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>" -e "MSSQL_PID=Developer" --cap-add SYS_PTRACE -p 1401:1433 -d mcr.microsoft.com/mssql/server:2017-latest
```
::: zone-end

::: moniker-end
<!--SQL Server 2019 on Linux-->
::: moniker range=">= sql-server-linux-ver15 || >= sql-server-ver15 "

::: zone pivot="cs1-bash"
```bash
docker run -e 'ACCEPT_EULA=Y' -e 'MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>' -e 'MSSQL_PID=Developer' --cap-add SYS_PTRACE -p 1401:1433 -d mcr.microsoft.com/mssql/server:2019-latest
```
::: zone-end

::: zone pivot="cs1-powershell"
```PowerShell
docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>" -e "MSSQL_PID=Developer" --cap-add SYS_PTRACE -p 1401:1433 -d mcr.microsoft.com/mssql/server:2019-latest
```
::: zone-end

::: zone pivot="cs1-cmd"
```cmd
docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>" -e "MSSQL_PID=Developer" --cap-add SYS_PTRACE -p 1401:1433 -d mcr.microsoft.com/mssql/server:2019-latest
```
::: zone-end

::: moniker-end

## <a name="sql-server-connection-failures"></a>SQL Server 連線失敗

如果您無法連線到在容器中執行的 SQL Server 執行個體，請嘗試下列測試：

- 查看 `docker ps -a` 輸出的 [狀態] 欄，以確定您的 SQL Server 容器正在執行。 如果沒有，則使用 `docker start <Container ID>` 來啟動它。

- 如果您已對應到非預設的主機連接埠 (不是 1433)，請確定您會在連接字串中指定該連接埠。 您可以在 `docker ps -a` 輸出的 [連接埠] 欄中看到連接埠對應。 例如，下列命令會將 sqlcmd 連線到在連接埠 1401 上接聽的容器：

    ::: zone pivot="cs1-bash"
    ```bash
    sqlcmd -S 10.3.2.4,1401 -U SA -P '<YourPassword>'
    ```
    ::: zone-end

    ::: zone pivot="cs1-powershell"
    ```PowerShell
    sqlcmd -S 10.3.2.4,1401 -U SA -P "<YourPassword>"
    ```
    ::: zone-end

    ::: zone pivot="cs1-cmd"
    ```cmd
    sqlcmd -S 10.3.2.4,1401 -U SA -P "<YourPassword>"
    ```
    ::: zone-end

- 如果您搭配現有的對應資料磁碟區或資料磁碟區容器使用 `docker run`，SQL Server 就會忽略 `SA_PASSWORD` 的值。 相反地，會從資料磁碟區或資料磁碟區容器中的 SQL Server 資料使用預先設定的 SA 使用者密碼。 確認您使用的 SA 密碼會與您要附加的資料相關聯。

- 檢閱 [SQL Server 設定和錯誤記錄檔](#errorlogs)。

## <a name="sql-server-availability-groups"></a>SQL Server 可用性群組

如果您使用 Docker 搭配 SQL Server 可用性群組，則有兩個額外的需求。

- 對應用於複本通訊的連接埠 (預設值為 5022)。 例如，指定 `-p 5022:5022` 作為 `docker run` 命令的一部分。

- 使用 `docker run` 命令的 `-h YOURHOSTNAME` 參數，明確設定容器主機名稱。 當您設定可用性群組時，會使用此主機名稱。 若沒有使用 `-h` 進行指定，則其預設為 **容器識別碼**。

## <a name="sql-server-setup-and-error-logs"></a><a id="errorlogs"></a> SQL Server 設定和錯誤記錄檔

您可以在 **/var/opt/mssql/log** 中查看 SQL Server 設定和錯誤記錄檔。 如果容器並未執行，請先啟動容器。 接著，使用互動式命令提示字元來檢查記錄。 您可透過執行 `docker ps` 命令來取得容器識別碼。

```bash
docker start <ContainerID>
docker exec -it <ContainerID> "bash"
```

從您容器內部的 Bash 工作階段，執行下列命令：

```bash
cd /var/opt/mssql/log
cat setup*.log
cat errorlog
```

> [!TIP]
> 如果您已在建立容器時將主機目錄裝載至 **/var/opt/mssql**，則可改為查看主機中對應路徑上的 **記錄** 子目錄。

## <a name="execute-commands-in-a-container"></a>在容器中執行命令

如果您有執行中的容器，就可以在該容器內，從主機終端機執行命令。

若要取得容器識別碼，請執行：

```bash
docker ps -a
```

若要在容器中啟動 Bash 終端機，請執行：

```bash
docker exec -it <Container ID> /bin/bash
```

現在您可以執行命令，就像是在容器內部的終端機中執行它們一樣。 完成後，鍵入 `exit`。 這會在互動式命令工作階段中結束，但您的容器會繼續執行。

## <a name="next-steps"></a>後續步驟

<!--SQL Server 2017 on Linux -->
::: moniker range="= sql-server-linux-2017 || = sql-server-2017"

- 透過此[快速入門](quickstart-install-connect-docker.md?view=sql-server-2017&preserve-view=true)，開始使用 Docker 上的 SQL Server 2017 容器映像。

::: moniker-end

<!--SQL Server 2019 on Linux-->
::: moniker range=">= sql-server-linux-ver15 || >= sql-server-ver15 "

- 透過此[快速入門](quickstart-install-connect-docker.md)，開始使用 Docker 上的 SQL Server 2019 容器映像。

::: moniker-end

- [部署及連線到 SQL Server Docker 容器](sql-server-linux-docker-container-deployment.md)

- [參考 Docker 容器的其他組態及自訂](sql-server-linux-docker-container-configure.md)

- [安全的 SQL Server Docker 容器](sql-server-linux-docker-container-security.md)
