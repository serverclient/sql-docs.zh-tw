---
title: 設定和自訂 SQL Server Docker 容器
description: 了解自訂 SQL Server Docker 容器的不同方式，以及如何根據需求進行設定
author: vin-yu
ms.author: vinsonyu
ms.reviewer: vanto
ms.custom: contperf-fy21q1
ms.date: 09/07/2020
ms.topic: conceptual
ms.prod: sql
ms.technology: linux
moniker: '>= sql-server-linux-2017 || >= sql-server-2017 '
zone_pivot_groups: cs1-command-shell
ms.openlocfilehash: 3fa589fdb522014994923edfa1a7d7858cfa7b72
ms.sourcegitcommit: cb8e2ce950d8199470ff1259c9430f0560f0dc1d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2021
ms.locfileid: "97878794"
---
# <a name="configure-and-customize-sql-server-docker-containers"></a>設定和自訂 SQL Server Docker 容器

[!INCLUDE [SQL Server - Linux](../includes/applies-to-version/sql-linux.md)]

本文說明如何設定及自訂 SQL Server Docker 容器，例如保存資料、移出和移入容器，以及變更預設設定。

## <a name="create-a-customized-container"></a><a id="customcontainer"></a> 建立自訂容器

您能夠建立自己的 [Dockerfile](https://docs.docker.com/engine/reference/builder/#usage) \(英文\)，來建立自訂的 SQL Server 容器。 如需詳細資訊，請參閱[結合 SQL Server 和 Node 應用程式的示範](https://github.com/twright-msft/mssql-node-docker-demo-app) \(英文\)。 如果您建立自己的 Dockerfile，請留意前景程序，因為此程序會控制容器的生命週期。 若其結束，則容器將會關閉。 例如，如果您想要執行指令碼並啟動 SQL Server，請確定 SQL Server 程序是最右邊的命令。 所有其他命令都會在背景執行。 下列命令會在 Dockerfile 的內部進行示範：

```bash
/usr/src/app/do-my-sql-commands.sh & /opt/mssql/bin/sqlservr
```

如果反轉上一個範例中的命令，容器就會在 do-my-sql-commands.sh 指令碼完成時關閉。

## <a name="persist-your-data"></a><a id="persist"></a> 保存您的資料

您的 SQL Server 設定變更和資料庫檔案都會保存於容器中，即使您使用 `docker stop` 和 `docker start` 來將容器重新啟動也一樣。 不過，如果您使用 `docker rm` 來移除容器，則會刪除容器中的所有項目，包括 SQL Server 和您的資料庫。 下一節將說明如何使用 **資料磁碟區** 來保存您的資料庫檔案，即使已刪除相關聯的容器也一樣。

> [!IMPORTANT]
> 針對 SQL Server，請務必瞭解 Docker 中的資料持續性。 除了本節的討論，另請參閱 Docker 文件，以了解[如何管理 Docker 容器中的資料](https://docs.docker.com/engine/tutorials/dockervolumes/) \(英文\)。

### <a name="mount-a-host-directory-as-data-volume"></a>裝載主機目錄作為資料磁碟區

第一個選項是在主機上裝載目錄作為容器中的資料磁碟區。 若要執行此動作，請使用 `docker run` 命令搭配 `-v <host directory>:/var/opt/mssql` 旗標。 這可讓您在容器執行之間還原資料。

<!--SQL Server 2017 on Linux -->
::: moniker range="= sql-server-linux-2017 || = sql-server-2017"

::: zone pivot="cs1-bash"
```bash
docker run -e 'ACCEPT_EULA=Y' -e 'MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>' -p 1433:1433 -v <host directory>/data:/var/opt/mssql/data -v <host directory>/log:/var/opt/mssql/log -v <host directory>/secrets:/var/opt/mssql/secrets -d mcr.microsoft.com/mssql/server:2017-latest
```
::: zone-end

::: zone pivot="cs1-powershell"
```PowerShell
docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>" -p 1433:1433 -v <host directory>/data:/var/opt/mssql/data -v <host directory>/log:/var/opt/mssql/log -v <host directory>/secrets:/var/opt/mssql/secrets -d mcr.microsoft.com/mssql/server:2017-latest
```
::: zone-end

::: zone pivot="cs1-cmd"
```cmd
docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>" -p 1433:1433 -v <host directory>/data:/var/opt/mssql/data -v <host directory>/log:/var/opt/mssql/log -v <host directory>/secrets:/var/opt/mssql/secrets -d mcr.microsoft.com/mssql/server:2017-latest
```
::: zone-end

::: moniker-end

<!--SQL Server 2019 on Linux-->
::: moniker range=">= sql-server-linux-ver15 || >= sql-server-ver15 "

::: zone pivot="cs1-bash"
```bash
docker run -e 'ACCEPT_EULA=Y' -e 'MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>' -p 1433:1433 -v <host directory>/data:/var/opt/mssql/data -v <host directory>/log:/var/opt/mssql/log -v <host directory>/secrets:/var/opt/mssql/secrets -d mcr.microsoft.com/mssql/server:2019-latest
```
::: zone-end

::: zone pivot="cs1-powershell"
```PowerShell
docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>" -p 1433:1433 -v <host directory>/data:/var/opt/mssql/data -v <host directory>/log:/var/opt/mssql/log -v <host directory>/secrets:/var/opt/mssql/secrets -d mcr.microsoft.com/mssql/server:2019-latest
```
::: zone-end

::: zone pivot="cs1-cmd"
```cmd
docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>" -p 1433:1433 -v <host directory>/data:/var/opt/mssql/data -v <host directory>/log:/var/opt/mssql/log -v <host directory>/secrets:/var/opt/mssql/secrets -d mcr.microsoft.com/mssql/server:2019-latest
```
::: zone-end

::: moniker-end

此技術也可讓您在 Docker 外部共用和檢視主機上的檔案。

> [!IMPORTANT]
> **Windows 上的 Docker** 的主機磁碟區對應，目前不支援對應完整 `/var/opt/mssql` 目錄。 不過，您可以將子目錄 (例如 `/var/opt/mssql/data`) 對應到您的主機電腦。
>
> 目前不支援使用 Linux 上的 SQL Server 映像，針對 **Mac 上的 Docker** 進行主機磁碟區對應。 請改用資料磁碟區容器。 此限制是 `/var/opt/mssql` 目錄特有的。 從裝載的目錄讀取可以正常運作。 例如，您可以在 Mac 上使用 -v 來裝載主機目錄，並從位於主機的 .bak 檔案還原備份。

### <a name="use-data-volume-containers"></a>使用資料磁碟區容器

第二個選項是使用資料磁碟區容器。 您可以使用 `-v` 參數來指定磁碟區名稱而非主機目錄，藉以建立資料磁碟區容器。 下列範例會建立名為 **sqlvolume** 的共用資料磁碟區。

<!--SQL Server 2017 on Linux -->
::: moniker range="= sql-server-linux-2017 || = sql-server-2017"

::: zone pivot="cs1-bash"
```bash
docker run -e 'ACCEPT_EULA=Y' -e 'MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>' -p 1433:1433 -v sqlvolume:/var/opt/mssql -d mcr.microsoft.com/mssql/server:2017-latest
```
::: zone-end

::: zone pivot="cs1-powershell"
```PowerShell
docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>" -p 1433:1433 -v sqlvolume:/var/opt/mssql -d mcr.microsoft.com/mssql/server:2017-latest
```
::: zone-end

::: zone pivot="cs1-cmd"
```cmd
docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>" -p 1433:1433 -v sqlvolume:/var/opt/mssql -d mcr.microsoft.com/mssql/server:2017-latest
```
::: zone-end

::: moniker-end
<!--SQL Server 2019 on Linux-->
::: moniker range=">= sql-server-linux-ver15 || >= sql-server-ver15 "

::: zone pivot="cs1-bash"
```bash
docker run -e 'ACCEPT_EULA=Y' -e 'MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>' -p 1433:1433 -v sqlvolume:/var/opt/mssql -d mcr.microsoft.com/mssql/server:2019-latest
```
::: zone-end

::: zone pivot="cs1-powershell"
```PowerShell
docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>" -p 1433:1433 -v sqlvolume:/var/opt/mssql -d mcr.microsoft.com/mssql/server:2019-latest
```
::: zone-end

::: zone pivot="cs1-cmd"
```cmd
docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>" -p 1433:1433 -v sqlvolume:/var/opt/mssql -d mcr.microsoft.com/mssql/server:2019-latest
```
::: zone-end

::: moniker-end

> [!NOTE]
> 這項在執行命令中隱含建立資料磁碟區的技術無法與舊版 Docker 搭配運作。 在此情況下，請使用 Docker 文件中概述的明確步驟，[建立和裝載資料磁碟區容器](https://docs.docker.com/engine/tutorials/dockervolumes/#creating-and-mounting-a-data-volume-container) \(英文\)。

即使您停止並移除此容器，資料磁碟區仍會保存。 您可以使用 `docker volume ls` 命令來檢視它。

```command
docker volume ls
```

如果您接著使用相同磁碟區名稱來建立另一個容器，新容器就會使用該磁碟區中所含的相同 SQL Server 資料。

若要移除資料磁碟區容器，請使用 `docker volume rm` 命令。

> [!WARNING]
> 如果您刪除資料磁碟區容器，則會「永久」  刪除容器中的所有 SQL Server 資料。

### <a name="backup-and-restore"></a>備份與還原

除了這些容器技術，您也可以使用標準的 SQL Server 備份和還原技術。 您可以使用備份檔案來保護資料，或將資料移至另一個 SQL Server 執行個體。 如需詳細資訊，請參閱[備份與還原 Linux 上的 SQL Server 資料庫](sql-server-linux-backup-and-restore-database.md)。

> [!WARNING]
> 如果您建立備份，請務必在容器外部建立或複製備份檔案。 否則，如果移除容器，也會一併刪除備份檔案。

## <a name="copy-files-from-a-container"></a>從容器複製檔案

若要將檔案複製到容器外部，請使用下列命令：

```command
docker cp <Container ID>:<Container path> <host path>
```

您可透過執行 `docker ps -a` 命令來取得容器識別碼。

**範例︰**

::: zone pivot="cs1-bash"
```bash
docker cp d6b75213ef80:/var/opt/mssql/log/errorlog /tmp/errorlog
```
::: zone-end

::: zone pivot="cs1-powershell"
```PowerShell
docker cp d6b75213ef80:/var/opt/mssql/log/errorlog C:\Temp\errorlog
```
::: zone-end

::: zone pivot="cs1-cmd"
```cmd
docker cp d6b75213ef80:/var/opt/mssql/log/errorlog C:\Temp\errorlog
```
::: zone-end

## <a name="copy-files-into-a-container"></a>將檔案複製到容器

若要將檔案複製到容器內部，請使用下列命令：

```command
docker cp <Host path> <Container ID>:<Container path>
```

**範例︰**

::: zone pivot="cs1-bash"
```bash
docker cp /tmp/mydb.mdf d6b75213ef80:/var/opt/mssql/data
```
::: zone-end

::: zone pivot="cs1-powershell"
```PowerShell
docker cp C:\Temp\mydb.mdf d6b75213ef80:/var/opt/mssql/data
```
::: zone-end

::: zone pivot="cs1-cmd"
```cmd
docker cp C:\Temp\mydb.mdf d6b75213ef80:/var/opt/mssql/data
```
::: zone-end

## <a name="configure-the-timezone"></a><a id="tz"></a> 設定時區

若要在具有特定時區的 Linux 容器中執行 SQL Server，請設定 `TZ` 環境變數。 若要尋找正確的時區值，請從 Linux Bash 提示執行 `tzselect` 命令：

```command
tzselect
```

選取時區之後，`tzselect` 會顯示類似下列的輸出：

```output
The following information has been given:

        United States
        Pacific

Therefore TZ='America/Los_Angeles' will be used.
```

您可以使用此資訊，在 Linux 容器中設定相同的環境變數。 下列範例示範如何在 `Americas/Los_Angeles` 時區的容器中執行 SQL Server：

<!--SQL Server 2017 on Linux -->
::: moniker range="= sql-server-linux-2017 || = sql-server-2017"

::: zone pivot="cs1-bash"
```bash
sudo docker run -e 'ACCEPT_EULA=Y' -e 'A_PASSWORD=<YourStrong!Passw0rd>' \
-p 1433:1433 --name sql1 \
-e 'TZ=America/Los_Angeles'\
-d mcr.microsoft.com/mssql/server:2017-latest
```
::: zone-end

::: zone pivot="cs1-powershell"
```PowerShell
sudo docker run -e 'ACCEPT_EULA=Y' -e "SA_PASSWORD=<YourStrong!Passw0rd>" `
-p 1433:1433 --name sql1 `
-e "TZ=America/Los_Angeles" `
-d mcr.microsoft.com/mssql/server:2017-latest 
```
::: zone-end

::: zone pivot="cs1-cmd"
```cmd
sudo docker run -e 'ACCEPT_EULA=Y' -e "SA_PASSWORD=<YourStrong!Passw0rd>" `
-p 1433:1433 --name sql1 ^
-e "TZ=America/Los_Angeles" ^
-d mcr.microsoft.com/mssql/server:2017-latest 
```
::: zone-end

::: moniker-end
<!--SQL Server 2019 on Linux-->
::: moniker range=">= sql-server-linux-ver15 || >= sql-server-ver15 "

::: zone pivot="cs1-bash"
```bash
sudo docker run -e 'ACCEPT_EULA=Y' -e 'MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>' \
   -p 1433:1433 --name sql1 \
   -e 'TZ=America/Los_Angeles'\
   -d mcr.microsoft.com/mssql/server:2019-latest
```
::: zone-end

::: zone pivot="cs1-powershell"
```PowerShell
sudo docker run -e 'ACCEPT_EULA=Y' -e "MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>" `
   -p 1433:1433 --name sql1 `
   -e "TZ=America/Los_Angeles" `
   -d mcr.microsoft.com/mssql/server:2019-latest
```
::: zone-end

::: zone pivot="cs1-cmd"
```cmd
sudo docker run -e 'ACCEPT_EULA=Y' -e "MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>" `
   -p 1433:1433 --name sql1 `
   -e "TZ=America/Los_Angeles" `
   -d mcr.microsoft.com/mssql/server:2019-latest
```
::: zone-end

::: moniker-end

## <a name="change-the-default-file-location"></a><a id="changefilelocation"></a> 變更預設檔案位置

新增 `MSSQL_DATA_DIR` 變數來在您 `docker run` 命令中變更您的資料目錄，然後將磁碟區掛接到您容器使用者可存取的該位置。

<!--SQL Server 2017 on Linux -->
::: moniker range="= sql-server-linux-2017 || = sql-server-2017"

::: zone pivot="cs1-bash"
```bash
docker run -e 'ACCEPT_EULA=Y' -e 'SA_PASSWORD=MyStrongPassword' -e 'MSSQL_DATA_DIR=/my/file/path' -v /my/host/path:/my/file/path -p 1433:1433 -d mcr.microsoft.com/mssql/server:2017-latest
```
::: zone-end

::: zone pivot="cs1-powershell"
```PowerShell
docker run -e 'ACCEPT_EULA=Y' -e "SA_PASSWORD=MyStrongPassword" -e "MSSQL_DATA_DIR=/my/file/path" -v /my/host/path:/my/file/path -p 1433:1433 -d mcr.microsoft.com/mssql/server:2017-latest
```
::: zone-end

::: zone pivot="cs1-cmd"
```cmd
docker run -e "ACCEPT_EULA=Y" -e "SA_PASSWORD=MyStrongPassword" -e "MSSQL_DATA_DIR=/my/file/path" -v /my/host/path:/my/file/path -p 1433:1433 -d mcr.microsoft.com/mssql/server:2017-latest
```
::: zone-end

::: moniker-end

<!--SQL Server 2019 on Linux-->
::: moniker range=">= sql-server-linux-ver15 || >= sql-server-ver15 "

::: zone pivot="cs1-bash"
```bash
docker run -e 'ACCEPT_EULA=Y' -e 'SA_PASSWORD=MyStrongPassword' -e 'MSSQL_DATA_DIR=/my/file/path' -v /my/host/path:/my/file/path -p 1433:1433 -d mcr.microsoft.com/mssql/server:2019-latest
```
::: zone-end

::: zone pivot="cs1-powershell"
```PowerShell
docker run -e 'ACCEPT_EULA=Y' -e "SA_PASSWORD=MyStrongPassword" -e "MSSQL_DATA_DIR=/my/file/path" -v /my/host/path:/my/file/path -p 1433:1433 -d mcr.microsoft.com/mssql/server:2019-latest
```
::: zone-end

::: zone pivot="cs1-cmd"
```cmd
docker run -e "ACCEPT_EULA=Y" -e "SA_PASSWORD=MyStrongPassword" -e "MSSQL_DATA_DIR=/my/file/path" -v /my/host/path:/my/file/path -p 1433:1433 -d mcr.microsoft.com/mssql/server:2019-latest
```
::: zone-end

::: moniker-end

## <a name="next-steps"></a>後續步驟

<!--SQL Server 2017 on Linux -->
::: moniker range="= sql-server-linux-2017 || = sql-server-2017"

- 透過此[快速入門](quickstart-install-connect-docker.md?view=sql-server-2017&preserve-view=true)，開始使用 Docker 上的 SQL Server 2017 容器映像

::: moniker-end

<!--SQL Server 2019 on Linux-->
::: moniker range=">= sql-server-linux-ver15 || >= sql-server-ver15 "

- 透過此[快速入門](quickstart-install-connect-docker.md)，開始使用 Docker 上的 SQL Server 2019 容器映像

::: moniker-end

- [部署及連線到 SQL Server Docker 容器](sql-server-linux-docker-container-deployment.md)

- [針對 SQL Server Docker 容器進行疑難排解](sql-server-linux-docker-container-troubleshooting.md)

- [安全的 SQL Server Docker 容器](sql-server-linux-docker-container-security.md)
