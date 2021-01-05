---
title: 安全的 SQL Server Docker 容器
description: 了解保護 SQL Server Docker 容器的不同方式，以及如何在主機上以不同的非根使用者執行容器
author: vin-yu
ms.author: vinsonyu
ms.reviewer: vanto
ms.custom: contperf-fy21q1
ms.date: 09/07/2020
ms.topic: conceptual
ms.prod: sql
ms.technology: linux
moniker: '>= sql-server-linux-2017 || >= sql-server-2017 '
ms.openlocfilehash: 88c179b1a17dd84dc33c2ae77979507e82b7a55a
ms.sourcegitcommit: cb8e2ce950d8199470ff1259c9430f0560f0dc1d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2021
ms.locfileid: "97878748"
---
# <a name="secure-sql-server-docker-containers"></a>安全的 SQL Server Docker 容器

[!INCLUDE [SQL Server - Linux](../includes/applies-to-version/sql-linux.md)]

SQL Server 2017 容器根據預設會以根使用者的身分啟動。 這可能會造成一些安全性疑慮。 本文會討論在執行 SQL Server Docker 容器時可使用的安全性選項，以及如何以非根使用者的身分建置 SQL Server 容器。

## <a name="build-and-run-non-root-sql-server-2017-containers"></a><a id="buildnonrootcontainer"></a> 建置並執行非根 SQL Server 2017 容器

請遵循下列步驟來建置以 `mssql` (非根) 使用者身分啟動的 SQL Server 2017 容器。

> [!NOTE]
> SQL Server 2019 容器會自動以非根身分啟動，因此下列步驟僅適用於預設以 root 身分啟動的 SQL Server 2017 容器。

1. 下載[適用於非根 SQL Server 容器的範例 dockerfile](https://raw.githubusercontent.com/microsoft/mssql-docker/master/linux/preview/examples/mssql-server-linux-non-root/Dockerfile)，並將它儲存為 `dockerfile`。

2. 在 dockerfile 目錄的內容中執行下列命令，建置非根 SQL Server 容器：

    ```bash
    cd <path to dockerfile>
    docker build -t 2017-latest-non-root .
    ```

3. 啟動容器。

    ```bash
    docker run -e "ACCEPT_EULA=Y" -e "SA_PASSWORD=MyStrongPassword@" --cap-add SYS_PTRACE --name sql1 -p 1433:1433 -d 2017-latest-non-root
    ```

    > [!NOTE]
    > `--cap-add SYS_PTRACE` 旗標是非根 SQL Server 容器產生傾印以用於疑難排解用途的必要項目。

4. 檢查容器是否正以非根使用者執行：

    ```bash
    docker exec -it sql1 bash
    ```

    執行 `whoami`，這將會傳回正在容器內執行的使用者。
    
    ```bash
    whoami
    ```

## <a name="run-container-as-a-different-non-root-user-on-the-host"></a><a id="nonrootuser"></a> 以主機上不同的非根使用者身分執行容器

若要以不同的非根使用者執行 SQL Server 容器，請將 -u 旗標新增至 docker run 命令。 非根容器包含限制，除非磁碟區是掛接到非根使用者可存取的 `/var/opt/mssql`，否則必須作為根群組的一部分執行。 根群組不會授與任何額外的根權限給非根使用者。

#### <a name="run-as-a-user-with-a-uid-4000"></a>以 UID 4000 的使用者執行

您可以使用自訂 UID 來啟動 SQL Server。 例如，以下命令會以 UID 4000 來啟動 SQL Server：

```bash
docker run -e "ACCEPT_EULA=Y" -e "SA_PASSWORD=MyStrongPassword" --cap-add SYS_PTRACE -u 4000:0 -p 1433:1433 -d mcr.microsoft.com/mssql/server:2019-latest
```

> [!Warning]
> 請確定 SQL Server 容器具有像是 'mssql' 或 'root' 等具名使用者，否則 SQLCMD 將無法在容器內執行。 您可以在容器內執行 `whoami` 來檢查 SQL Server 容器是否正以具名使用者的身分執行。

#### <a name="run-the-non-root-container-as-the-root-user"></a>以根使用者身分執行非根容器

若需要，則可以根使用者的身分來執行非根容器。 這也會自動授與完整的檔案權限給容器，因為這是較高的權限。

```bash
docker run -e "ACCEPT_EULA=Y" -e "SA_PASSWORD=MyStrongPassword" -u 0:0 -p 1433:1433 -d mcr.microsoft.com/mssql/server:2019-latest
```

#### <a name="run-as-a-user-on-your-host-machine"></a>以您主機電腦上的使用者身分執行

您可以透過下列命令，使用主機電腦上現有的使用者來啟動 SQL Server：
```bash
docker run -e "ACCEPT_EULA=Y" -e "SA_PASSWORD=MyStrongPassword" --cap-add SYS_PTRACE -u $(id -u myusername):0 -p 1433:1433 -d mcr.microsoft.com/mssql/server:2019-latest
```

#### <a name="run-as-a-different-user-and-group"></a>以不同的使用者和群組執行

您可以使用自訂使用者和群組來啟動 SQL Server。 在此範例中，已掛接磁碟區具備針對主機電腦上使用者或群組設定的權限。

```bash
docker run -e "ACCEPT_EULA=Y" -e "SA_PASSWORD=MyStrongPassword" --cap-add SYS_PTRACE -u (id -u myusername):(id -g myusername) -v /path/to/mssql:/var/opt/mssql -p 1433:1433 -d mcr.microsoft.com/mssql/server:2019-latest
```

## <a name="configure-persistent-storage-permissions-for-non-root-containers"></a><a id="storagepermissions"></a> 為非根容器設定永續性儲存體權限

若要允許非根使用者存取已掛接磁碟區上的資料庫檔案，請確認執行容器的使用者或群組可讀取/寫入永續性檔案儲存體。  

您可以使用此命令來取得資料庫檔案目前的擁有權。

```bash
ls -ll <database file dir>
```

若 SQL Server 無法存取保存的資料庫檔案，請執行下列其中一個命令。

#### <a name="grant-the-root-group-rw-access-to-the-db-files"></a>授與根群組對 DB 檔案的讀寫存取權

授與根群組存取下列目錄的權限，讓非根 SQL Server 容器能夠存取資料庫檔案。

```bash
chgrp -R 0 <database file dir>
chmod -R g=u <database file dir>
```

#### <a name="set-the-non-root-user-as-the-owner-of-the-files"></a>將非根使用者設為檔案的擁有者

這可以是預設的非根使用者，或是任何其他您想要指定的非根使用者。 在此範例中，我們會將 UID 10001 設為非根使用者。

```bash
chown -R 10001:0 <database file dir>
```
## <a name="encrypting-connections-to-sql-server-linux-containers"></a>加密 SQL Server Linux 容器的連線

若要加密 SQL Server Linux 容器的連線，即會需要憑證，請參閱[這裡]所記載的憑證需求。

以下是如何加密 SQL Server Linux 容器連線的範例。 在此我們會使用自我簽署的憑證，但這種憑證不應用於這類環境的生產情況，而應該使用 CA 憑證。

1. 建立自我簽署憑證，僅適用於測試與非生產環境。
  
      ```bash
      openssl req -x509 -nodes -newkey rsa:2048 -subj '/CN=sql1.contoso.com' -keyout /container/sql1/mssql.key -out /container/sql1/mssql.pem -days 365
      ```
     sql1 是 SQL 容器的主機名稱，因此當連線到這個容器時，連接字串中所使用的名稱將會是 \'sql1.contoso.com,port\'。

    > [!NOTE]
    > 請先確定資料夾路徑 /container/sql1/ 已經存在，再執行上述命令。

2. 請確定已在 mssql.key 與 mssql.pem 檔案上設定正確的權限，以避免在將檔案掛接至 SQL 容器時發生錯誤：

    ```bash
    chmod 440 /container/sql1/mssql.pem
    chmod 440 /container/sql1/mssql.key
    ```

3. 現在，使用下列內容建立 mssql.conf 檔案，以啟用伺服器啟動的加密，若要啟用用戶端啟動的加密，請將最後一行變更為 'forceencryption = 0\'。

    ```bash
    [network]
    tlscert = /etc/ssl/certs/mssql.pem
    tlskey = /etc/ssl/private/mssql.key
    tlsprotocols = 1.2
    forceencryption = 1
    ```

    > [!NOTE]
    > 針對某些 Linux 發行版本，儲存憑證和金鑰的路徑也可能分別是：/etc/pki/tls/certs/ 與 /etc/pki/tls/private/。 請先確認路徑，再更新 SQL 容器的 mssql.conf。 您在 mssql.conf 中設定的位置會是容器中 SQL Server 要搜尋憑證及其金鑰的位置。 在此情況下，該位置為 /etc/ssl/certs/ 與 /etc/ssl/private/。

    mssql.conf 檔案也會建立在相同的資料夾位置 /container/sql1/。 執行上述步驟之後，sql1 資料夾中應該會有三個檔案：mssql.conf、mssql.key 與 mssql.pem。

4. 使用下列命令來部署 SQL 容器：

    ```bash
    docker run -e "ACCEPT_EULA=Y" -e "SA_PASSWORD=P@ssw0rd" -p 5434:1433 --name sql1 -h sql1 -v /container/sql1/mssql.conf:/var/opt/mssql/mssql.conf -v   /container/sql1/mssql.pem:/etc/ssl/certs/mssql.pem -v /container/sql1/mssql.key:/etc/ssl/private/mssql.key -d mcr.microsoft.com/mssql/server:2019-latest
    ```

    在上述命令中，我們已將 mssql.conf、mssql.key 與 mssql.pem 檔案掛接至容器，並將容器中的 1433 連接埠 (SQL Server 預設連接埠) 對應至主機的 5434 連接埠。 

    > [!NOTE]
    > 如果正在使用 RHEL 8 或更新版本，您也可以使用 \'podman run\' 命令，而非 \'docker run\' 命令。 

遵循記載於[這裡][1]的 \"在用戶端電腦上註冊憑證\" 與 \"範例連接字串\" 區段，開始為 Linux 容器上 SQL Server 的連線加密。

  [Encrypting connection to SQL Server Linux]: https://docs.microsoft.com/sql/linux/sql-server-linux-encrypted-connections?view=sql-server-ver15&preserve-view=true
  [此處]: https://docs.microsoft.com/sql/linux/sql-server-linux-encrypted-connections?view=sql-server-ver15&preserve-view=true#requirements-for-certificates
  [1]: https://docs.microsoft.com/sql/linux/sql-server-linux-encrypted-connections?view=sql-server-ver15&preserve-view=true#client-initiated-encryption

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

- [參考 Docker 容器的其他組態及自訂](sql-server-linux-docker-container-configure.md)

- [針對 SQL Server Docker 容器進行疑難排解](sql-server-linux-docker-container-troubleshooting.md)
