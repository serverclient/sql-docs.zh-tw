---
title: Docker：安裝 Linux 上的 SQL Server 容器
description: 本快速入門會示範如何使用 Docker 來執行 SQL Server 2017 和 2019 容器映像。 您隨後便可使用 sqlcmd 來建立及查詢資料庫。
ms.custom: seo-lt-2019, contperf-fy21q1
author: vin-yu
ms.author: vinsonyu
ms.reviewer: vanto
ms.date: 09/07/2020
ms.topic: quickstart
ms.prod: sql
ms.technology: linux
ms.prod_service: linux
ms.assetid: 82737f18-f5d6-4dce-a255-688889fdde69
monikerRange: '>= sql-server-linux-2017 || >= sql-server-2017'
zone_pivot_groups: cs1-command-shell
ms.openlocfilehash: f3bf3cff9f7497c5f880cbd75aef26b387c7415e
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98093277"
---
# <a name="quickstart-run-sql-server-container-images-with-docker"></a>快速入門：使用 Docker 執行 SQL Server 容器映像
[!INCLUDE [SQL Server - Linux](../includes/applies-to-version/sql-linux.md)]

> [!NOTE]
> 以下顯示的範例使用 docker.exe，但這些命令大多也適用於 Podman。 其 CLI 與 Docker 容器引擎類似。 您可以在[這裡](http://docs.podman.io/en/latest)閱讀更多有關 podman 的資訊。

<!--SQL Server 2017 on Linux-->
::: moniker range="= sql-server-linux-2017 || = sql-server-2017"

在本快速入門中，您將使用 Docker 來提取與執行 SQL Server 2017 容器映像，[mssql-server-linux](https://hub.docker.com/_/microsoft-mssql-server)。 然後與 **sqlcmd** 連線來建立您的第一個資料庫並執行查詢。

> [!TIP]
> 如果您想要執行 SQL Server 2019 容器，請參閱[本文的 SQL Server 2019 版本](quickstart-install-connect-docker.md?view=sql-server-linux-ver15&preserve-view=true)。

::: moniker-end
<!--SQL Server 2019 on Linux-->
::: moniker range=">= sql-server-linux-ver15 || >= sql-server-ver15 "

> [!NOTE]
> 從 SQL Server 2019 CU3 開始支援 Ubuntu 18.04。

在本快速入門中，您將使用 Docker 來提取與執行 SQL Server 2019 容器映像 [mssql-server](https://hub.docker.com/r/microsoft/mssql-server)。 然後與 **sqlcmd** 連線來建立您的第一個資料庫並執行查詢。

> [!TIP]
> 本快速入門會建立 SQL Server 2019 容器。 如果您想要建立 SQL Server 2017 容器，請參閱[本文的 SQL Server 2017 版本。](quickstart-install-connect-docker.md?view=sql-server-linux-2017&preserve-view=true)

::: moniker-end

此映像包含以 Ubuntu 18.04 為基礎，在 Linux 上執行的 SQL Server。 您可於適用於 Mac/Windows 的 Docker 上將其與 Docker 引擎 1.8 以上版本搭配使用。 本快速入門將著重於在 **inux** 映像上使用 SQL Server。 Windows 映像則不涵蓋在內，但您可於 [mssql-server-windows-developer Docker Hub 頁面](https://hub.docker.com/r/microsoft/mssql-server-windows-developer/)進一步加以了解。

## <a name="prerequisites"></a><a id="requirements"></a> 必要條件

- 在任何支援的 Linux 發行版本或適用於 Mac/Windows 上的 Docker 安裝 Docker 引擎 1.8 以上版本。 如需詳細資訊，請參閱[安裝 Docker](https://docs.docker.com/engine/installation/)。
- Docker **overlay2** 儲存體驅動程式。 這是大部分使用者的預設值。 如果發現未正在使用此儲存提供者且需要進行變更，請參閱[用於設定 overlay2 的 Docker 文件](https://docs.docker.com/storage/storagedriver/overlayfs-driver/#configure-docker-with-the-overlay-or-overlay2-storage-driver)中指示和警告。
- 至少 2 GB 的磁碟空間。
- 至少 2 GB 的 RAM。
- [Linux 上的 SQL Server 系統需求](sql-server-linux-setup.md#system)。

<!--The following H2 is versioned for 2017 and 2019. Much of the content is duplicated, so
any changes to one section should be duplicated in the other-->
::: moniker range="= sql-server-linux-2017 || = sql-server-2017"

## <a name="pull-and-run-the-2017-container-image"></a><a id="pullandrun2017"></a> 提取和執行 2017 容器映像

開始下列步驟之前，請確定您已在本文頂端選取您慣用的 Shell (Bash、PowerShell 或 cmd)。

1. 從 Microsoft Container Registry 提取 SQL Server 2017 Linux 容器映像。

   ::: zone pivot="cs1-bash"
   ```bash
   sudo docker pull mcr.microsoft.com/mssql/server:2017-latest
   ```
   ::: zone-end

   ::: zone pivot="cs1-powershell"
   ```PowerShell
   docker pull mcr.microsoft.com/mssql/server:2017-latest
   ```
   ::: zone-end

   ::: zone pivot="cs1-cmd"
   ```cmd
   docker pull mcr.microsoft.com/mssql/server:2017-latest
   ```
   ::: zone-end

   > [!TIP]
   > 如果您想要執行 SQL Server 2019 容器，請參閱[本文的 SQL Server 2019 版本](quickstart-install-connect-docker.md?view=sql-server-linux-ver15&preserve-view=true#pullandrun2019)。

   上述命令會提取最新的 SQL Server 2017 容器映像。 若要提取特定映像，您需要新增欄位與標籤名稱 (例如，`mcr.microsoft.com/mssql/server:2017-GA-ubuntu`)。 若要查看所有可用的映像，請參閱 [mssql-server Docker Hub 頁面](https://hub.docker.com/r/microsoft/mssql-server)。

   ::: zone pivot="cs1-bash"
   本文中的 Bash 命令會使用 `sudo`。 在 macOS 上，可能不需要 `sudo`。 在 Linux 上，如果您不想使用 `sudo` 來執行 Docker，您可以設定 **Docker** 群組，並將使用者新增至該群組。 如需詳細資訊，請參閱 [Post-installation steps for Linux](https://docs.docker.com/install/linux/linux-postinstall/) (適用於 Linux 的安裝後步驟)。

   ::: zone-end

2. 若要以 Docker 執行容器映像，您可以從 Bash 殼層 (Linux/macOS) 或提高權限的 PowerShell 命令提示字元使用下列命令。

   ::: zone pivot="cs1-bash"
   ```bash
   sudo docker run -e "ACCEPT_EULA=Y" -e "SA_PASSWORD=<YourStrong@Passw0rd>" \
      -p 1433:1433 --name sql1 -h sql1 \
      -d \
      mcr.microsoft.com/mssql/server:2017-latest
   ```
   ::: zone-end

   ::: zone pivot="cs1-powershell"
   
   > [!NOTE]
   > 若正在使用 PowerShell Core，請將雙引號取代為單引號。
   
   ```PowerShell
   docker run -e "ACCEPT_EULA=Y" -e "SA_PASSWORD=<YourStrong@Passw0rd>" `
      -p 1433:1433 --name sql1 -h sql1 `
      -d `
      mcr.microsoft.com/mssql/server:2017-latest
   ```
   ::: zone-end

   ::: zone pivot="cs1-cmd"
   ```cmd
   docker run -e "ACCEPT_EULA=Y" -e "SA_PASSWORD=<YourStrong@Passw0rd>" `
      -p 1433:1433 --name sql1 -h sql1 `
      -d `
      mcr.microsoft.com/mssql/server:2017-latest
   ```
   ::: zone-end

   > [!NOTE]
   > 密碼應遵循 SQL Server 預設密碼原則，否則容器將無法設定 SQL Server 並停止運作。 根據預設，密碼的長度至少必須是 8 個字元，包含下列四種集合的其中三種字元：大寫字母、小寫字母、以 10 為底數的數字，以及符號。 執行 [docker logs](https://docs.docker.com/engine/reference/commandline/logs/) 命令即可查看錯誤記錄。
   >
   > 根據預設，這會建立 SQL Server 2017 Developer 版本的映像。 在容器中執行生產版本的程序將有些微差異。 如需詳細資訊，請參閱[執行生產容器映像](./sql-server-linux-docker-container-deployment.md#production)。

   下表提供了前述 `docker run` 範例的參數描述：

   | 參數 | 描述 |
   |-----|-----|
   | **-e "ACCEPT_EULA=Y"** |  將 **ACCEPT_EULA** 變數設為任意值可確認您接受 [終端使用者授權合約](https://go.microsoft.com/fwlink/?linkid=857698)。 此為 SQL Server 映像的必要設定。 |
   | **-e "SA_PASSWORD=\<YourStrong@Passw0rd\>"** | 指定您自己的強式密碼，該密碼長度至少需為 8 個字元且符合 [SQL Server 密碼需求](../relational-databases/security/password-policy.md)。 此為 SQL Server 映像的必要設定。 |
   | **-p 1433:1433** | 將主機環境上的 TCP 連接埠 (第一個值) 對應至容器中的 TCP 連接埠 (第二個值)。 在本範例中，SQL Server 正在接聽容器中的 TCP 1433 且對主機上的連接埠 1433 公開。 |
   | **--name sql1** | 為容器指定自訂名稱，而不使用隨機產生的名稱。 若您執行數個容器，就無法使用此相同名稱。 |
   | **-h sql1** | 用來明確設定容器主機名稱，如果您未指定，則會預設為隨機產生之系統 GUID 的容器識別碼。 |
   | **-d** | 在背景執行容器 (精靈) |
   | **mcr.microsoft.com/mssql/server:2017-latest** | SQL Server 2017 Linux 容器映像。 |

3. 若要檢視 Docker 容器，請使用 `docker ps` 命令。


   ::: zone pivot="cs1-bash"
   ```bash
   sudo docker ps -a
   ```
   ::: zone-end

   ::: zone pivot="cs1-powershell"
   ```PowerShell
   docker ps -a
   ```
   ::: zone-end

   ::: zone pivot="cs1-cmd"
   ```cmd
   docker ps -a
   ```
   ::: zone-end

   您應該會看到類似下列螢幕擷取畫面的結果：

   ![Docker ps 命令輸出](./media/sql-server-linux-setup-docker/docker-ps-command.png)

4. 若 **STATUS** 欄位顯示的狀態含 **Up**，表示 SQL Server 正在容器中執行且接聽於 **PORTS** 欄位中指定的連接埠。 若 SQL Server 容器的 **STATUS** 欄位顯示 **Exited**，請參閱 [設定指南的＜疑難排解＞一節](./sql-server-linux-docker-container-troubleshooting.md)。

以上討論的 `-h` (主機名稱) 參數可將容器的內部名稱變更為自訂值。 這是您在下列 Transact-SQL 查詢中會看到的傳回名稱：

```sql
SELECT @@SERVERNAME,
    SERVERPROPERTY('ComputerNamePhysicalNetBIOS'),
    SERVERPROPERTY('MachineName'),
    SERVERPROPERTY('ServerName')
```

建議您將 `-h` 與 `--name` 設為相同的值，這會讓識別目標容器更輕鬆。

5. 因為 `SA_PASSWORD` 會顯示在 `ps -eax` 輸出，且儲存在相同名稱的環境變數中，所以最後一個步驟是變更您的 SA 密碼。 請參閱下方步驟。

::: moniker-end
<!--End of 2017 "Pull and run" section-->

<!--This is the 2019 version of the "Pull and run" section-->
::: moniker range=">= sql-server-linux-ver15 || >= sql-server-ver15 "

## <a name="pull-and-run-the-2019-container-image"></a><a id="pullandrun2019"></a> 提取和執行 2019 容器映像

開始下列步驟之前，請確定您已在本文頂端選取您慣用的 Shell (Bash、PowerShell 或 cmd)。

1. 從 Microsoft Container Registry 提取 SQL Server 2019 Linux 容器映像。

   ::: zone pivot="cs1-bash"
   ```bash
   sudo docker pull mcr.microsoft.com/mssql/server:2019-latest
   ```
   ::: zone-end

   ::: zone pivot="cs1-powershell"
   
   > [!NOTE]
   > 若正在使用 PowerShell Core，請將雙引號取代為單引號。
   
   ```PowerShell
   docker pull mcr.microsoft.com/mssql/server:2019-latest
   ```
   ::: zone-end

   ::: zone pivot="cs1-cmd"
   ```cmd
   docker pull mcr.microsoft.com/mssql/server:2019-latest
   ```
   ::: zone-end

   > [!TIP]
   > 本快速入門使用 SQL Server 2019 Docker 映像。 如果您想要試用 SQL Server 2017 映像，請參閱[本文的 SQL Server 2017 版本](quickstart-install-connect-docker.md?view=sql-server-linux-2017&preserve-view=true#pullandrun2017)。

   先前的命令會提取以 Ubuntu 為基礎的 SQL Server 2019 容器映像。 若要改為使用以 RedHat 為基礎的容器映像，請參閱[執行 RHEL 型的容器映像](./sql-server-linux-docker-container-deployment.md#rhel)。 若要查看所有可用的映像，請參閱 [mssql-server-linux Docker Hub 頁面](https://hub.docker.com/_/microsoft-mssql-server)。

   ::: zone pivot="cs1-bash"
   本文中的 Bash 命令會使用 `sudo`。 在 macOS 上，可能不需要 `sudo`。 在 Linux 上，如果您不想使用 `sudo` 來執行 Docker，您可以設定 **Docker** 群組，並將使用者新增至該群組。 如需詳細資訊，請參閱 [Post-installation steps for Linux](https://docs.docker.com/install/linux/linux-postinstall/) (適用於 Linux 的安裝後步驟)。
   ::: zone-end

2. 若要以 Docker 執行容器映像，您可以從 Bash 殼層 (Linux/macOS) 或提高權限的 PowerShell 命令提示字元使用下列命令。

   ::: zone pivot="cs1-bash"
   ```bash
   sudo docker run -e "ACCEPT_EULA=Y" -e "SA_PASSWORD=<YourStrong@Passw0rd>" \
      -p 1433:1433 --name sql1 -h sql1 \
      -d mcr.microsoft.com/mssql/server:2019-latest
   ```
   ::: zone-end

   ::: zone pivot="cs1-powershell"
   ```PowerShell
   docker run -e "ACCEPT_EULA=Y" -e "SA_PASSWORD=<YourStrong@Passw0rd>" `
      -p 1433:1433 --name sql1 -h sql1 `
      -d mcr.microsoft.com/mssql/server:2019-latest
   ```
   ::: zone-end

   ::: zone pivot="cs1-cmd"
   ```cmd
   docker run -e "ACCEPT_EULA=Y" -e "SA_PASSWORD=<YourStrong@Passw0rd>" `
      -p 1433:1433 --name sql1 -h sql1 `
      -d mcr.microsoft.com/mssql/server:2019-latest
   ```
   ::: zone-end

   > [!NOTE]
   > 密碼應遵循 SQL Server 預設密碼原則，否則容器將無法設定 SQL Server 並停止運作。 根據預設，密碼的長度至少必須是 8 個字元，包含下列四種集合的其中三種字元：大寫字母、小寫字母、以 10 為底數的數字，以及符號。 執行 [docker logs](https://docs.docker.com/engine/reference/commandline/logs/) 命令即可查看錯誤記錄。
   >
   > 根據預設，這會建立 SQL Server 2019 Developer 版本的容器。

   下表提供了前述 `docker run` 範例的參數描述：

   | 參數 | 描述 |
   |-----|-----|
   | **-e "ACCEPT_EULA=Y"** |  將 **ACCEPT_EULA** 變數設為任意值可確認您接受 [終端使用者授權合約](https://go.microsoft.com/fwlink/?LinkId=746388)。 此為 SQL Server 映像的必要設定。 |
   | **-e "SA_PASSWORD=\<YourStrong@Passw0rd\>"** | 指定您自己的強式密碼，該密碼長度至少需為 8 個字元且符合 [SQL Server 密碼需求](../relational-databases/security/password-policy.md)。 此為 SQL Server 映像的必要設定。 |
   | **-p 1433:1433** | 將主機環境上的 TCP 連接埠 (第一個值) 對應至容器中的 TCP 連接埠 (第二個值)。 在本範例中，SQL Server 正在接聽容器中的 TCP 1433 且對主機上的連接埠 1433 公開。 |
   | **--name sql1** | 為容器指定自訂名稱，而不使用隨機產生的名稱。 若您執行數個容器，就無法使用此相同名稱。 |
   | **-h sql1** | 用來明確設定容器主機名稱，如果您未指定，則會預設為隨機產生之系統 GUID 的容器識別碼。 |
   | **mcr.microsoft.com/mssql/server:2019-latest** | SQL Server 2019 Ubuntu Linux 容器映像。 |

3. 若要檢視 Docker 容器，請使用 `docker ps` 命令。

   ::: zone pivot="cs1-bash"
   ```bash
   sudo docker ps -a
   ```
   ::: zone-end

   ::: zone pivot="cs1-powershell"
   ```PowerShell
   docker ps -a
   ```
   ::: zone-end

   ::: zone pivot="cs1-cmd"
   ```cmd
   docker ps -a
   ```
   ::: zone-end

   您應該會看到類似下列螢幕擷取畫面的結果：

   ![Docker ps 命令輸出](./media/sql-server-linux-setup-docker/docker-ps-command.png)

4. 若 **STATUS** 欄位顯示的狀態含 **Up**，表示 SQL Server 正在容器中執行且接聽於 **PORTS** 欄位中指定的連接埠。 若 SQL Server 容器的 **STATUS** 資料行顯示 **Exited**，請參閱 [針對 SQL Server Docker 容器進行疑難排解](sql-server-linux-docker-container-troubleshooting.md)。

以上討論的 `-h` (主機名稱) 參數可將容器的內部名稱變更為自訂值。 此參數可將容器的內部名稱變更為自訂值。 這是您在下列 Transact-SQL 查詢中會看到的傳回名稱：

```sql
SELECT @@SERVERNAME,
    SERVERPROPERTY('ComputerNamePhysicalNetBIOS'),
    SERVERPROPERTY('MachineName'),
    SERVERPROPERTY('ServerName')
```

建議您將 `-h` 與 `--name` 設為相同的值，這會讓識別目標容器更輕鬆。


5. 因為 `SA_PASSWORD` 會顯示在 `ps -eax` 輸出，且儲存在相同名稱的環境變數中，所以最後一個步驟是變更您的 SA 密碼。 請參閱下方步驟。


::: moniker-end
<!--End of 2019 "Pull and run" section-->



## <a name="change-the-sa-password"></a><a id="sapassword"></a> 變更 SA 密碼

<!-- This section was pasted in from includes/sql-server-linux-change-docker-password.md, to better support zone pivots. 2019/02/11 -->

**SA** 帳戶是在安裝期間建立的 SQL Server 執行個體系統管理員。 建立您的 SQL Server 容器之後，在容器中執行 `echo $SA_PASSWORD`，即可探索您指定的 `SA_PASSWORD` 環境變數。 基於安全性考量，請變更您的 SA 密碼。

1. 選擇要為 SA 使用者使用的強式密碼。

1. 使用 `docker exec` 來執行 **sqlcmd**，以使用 Transact-SQL 變更密碼。 在下列範例中，將舊密碼 `<YourStrong!Passw0rd>` 和新密碼 `<YourNewStrong!Passw0rd>` 取代為您自己的密碼值。

   ::: zone pivot="cs1-bash"
   ```bash
   sudo docker exec -it sql1 /opt/mssql-tools/bin/sqlcmd \
      -S localhost -U SA -P "<YourStrong@Passw0rd>" \
      -Q 'ALTER LOGIN SA WITH PASSWORD="<YourNewStrong@Passw0rd>"'
   ```
   ::: zone-end

   ::: zone pivot="cs1-powershell"
   ```PowerShell
   docker exec -it sql1 /opt/mssql-tools/bin/sqlcmd `
      -S localhost -U SA -P "<YourStrong@Passw0rd>" `
      -Q "ALTER LOGIN SA WITH PASSWORD='<YourNewStrong@Passw0rd>'"
   ```
   ::: zone-end

   ::: zone pivot="cs1-cmd"
   ```cmd
   docker exec -it sql1 /opt/mssql-tools/bin/sqlcmd `
      -S localhost -U SA -P "<YourStrong!Passw0rd>" `
      -Q "ALTER LOGIN SA WITH PASSWORD='<YourNewStrong@Passw0rd>'"
   ```
   ::: zone-end

## <a name="connect-to-sql-server"></a>連接至 SQL Server

下列步驟會在容器中使用 SQL Server 命令列工具 [**sqlcmd**](../tools/sqlcmd-utility.md) 以連線至 SQL Server。

1. 使用 `docker exec -it` 命令在您執行的容器中啟動互動式 Bash 殼層。 下列範例中的 `sql1` 是您在建立容器時由 `--name` 參數指定的名稱。

   ::: zone pivot="cs1-bash"
   ```bash
   sudo docker exec -it sql1 "bash"
   ```
   ::: zone-end

   ::: zone pivot="cs1-powershell"
   ```PowerShell
   docker exec -it sql1 "bash"
   ```
   ::: zone-end

   ::: zone pivot="cs1-cmd"
   ```cmd
   docker exec -it sql1 "bash"
   ```
   ::: zone-end

2. 進入容器後，以 sqlcmd 進行本機連線。 預設路徑並不包含 sqlcmd，因此您必須指定完整路徑。

   ```bash
   /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P "<YourNewStrong@Passw0rd>"
   ```

   > [!TIP]
   > 您可以在命令列中省略密碼，不要在提示時輸入密碼。

3. 如果成功，您應該會收到 **sqlcmd** 命令提示字元：`1>`。

## <a name="create-and-query-data"></a>建立及查詢資料

下列各節將逐步引導使用 **sqlcmd** 和 Transact-SQL 來建立新資料庫、新增資料及執行查詢。

### <a name="create-a-new-database"></a>建立新資料庫

下列步驟會建立名為 `TestDB` 的新資料庫。

1. 從 **sqlcmd** 命令提示字元，貼上下列 Transact-SQL 命令以建立測試資料庫：

   ```sql
   CREATE DATABASE TestDB
   ```

2. 在下一行，撰寫查詢以傳回您伺服器上所有資料庫的名稱：

   ```sql
   SELECT Name from sys.Databases
   ```

3. 上述兩個命令不會立即執行。 在新的一行上鍵入 `GO` 來執行先前命令：

   ```sql
   GO
   ```

### <a name="insert-data"></a>插入資料

接下來，建立新的資料表 `Inventory`，然後插入兩個新的資料列。

1. 從 **sqlcmd** 命令提示字元，將內容切換至 `TestDB` 資料庫：

   ```sql
   USE TestDB
   ```

2. 建立名為 `Inventory` 的新資料表：

   ```sql
   CREATE TABLE Inventory (id INT, name NVARCHAR(50), quantity INT)
   ```

3. 將資料插入新的資料表：

   ```sql
   INSERT INTO Inventory VALUES (1, 'banana', 150); INSERT INTO Inventory VALUES (2, 'orange', 154);
   ```

4. 鍵入 `GO` 以執行上述命令：

   ```sql
   GO
   ```

### <a name="select-data"></a>選取資料

現在，執行查詢以從 `Inventory` 資料表傳回資料。

1. 從 **sqlcmd** 命令提示字元，輸入查詢以從 `Inventory` 資料表傳回 quantity (數量) 大於 152 的資料列：

   ```sql
   SELECT * FROM Inventory WHERE quantity > 152;
   ```

2. 執行命令︰

   ```sql
   GO
   ```

### <a name="exit-the-sqlcmd-command-prompt"></a>結束 sqlcmd 命令提示字元

1. 若要結束您的 **sqlcmd** 工作階段，請鍵入 `QUIT`：

   ```sql
   QUIT
   ```

2. 若要結束容器中的互動式命令提示字元，請鍵入 `exit`。 結束互動式 Bash 殼層後，容器會繼續執行。

## <a name="connect-from-outside-the-container"></a><a id="connectexternal"></a> 從容器外部連線

您也可以從支援 SQL 連線的任何外部 Linux、Windows 或 macOS 工具連線到 Docker 機器上的 SQL Server 執行個體。

下列步驟在您容器的外部使用了 **sqlcmd** 以連線至容器中執行的 SQL Server。 這些步驟會假設您已經在容器外部安裝 SQL Server 命令列工具。 使用其他工具時亦適用相同原則，但各工具的連線程序不盡相同。

1. 找出裝載您容器之電腦的 IP 位址。 在 Linux 上，使用 **ifconfig** 或 **ip addr**。在 Windows 上，使用 **ipconfig**。

1. 針對此範例，請在您的用戶端電腦上安裝 **sqlcmd** 工具。 如需詳細資訊，請參閱[在 Windows 上安裝 sqlcmd](../tools/sqlcmd-utility.md) 或[在 Linux 上安裝 sqlcmd](sql-server-linux-setup-tools.md)。

1. 執行 sqlcmd 來指定 IP 位址，以及對應至您容器連接埠 1433 的連接埠。 在此範例中即為主機電腦上的相同連接埠 1433。 如果您在主機電腦上指定了不同的對應連接埠，您可以在這裡使用該連接埠。 您也將需要在防火牆上開啟適當的輸入連接埠來允許連線。

   ::: zone pivot="cs1-bash"
   ```bash
   sqlcmd -S <ip_address>,1433 -U SA -P "<YourNewStrong@Passw0rd>"
   ```
   ::: zone-end

   ::: zone pivot="cs1-powershell"
   ```PowerShell
   sqlcmd -S <ip_address>,1433 -U SA -P "<YourNewStrong@Passw0rd>"
   ```
   ::: zone-end

   ::: zone pivot="cs1-cmd"
   ```cmd
   sqlcmd -S <ip_address>,1433 -U SA -P "<YourNewStrong@Passw0rd>"
   ```
   ::: zone-end

1. 執行 Transact-SQL 命令。 完成後，鍵入 `QUIT`。

其他常用的 SQL Server 連線工具包括：

- [Visual Studio Code](../tools/visual-studio-code/sql-server-develop-use-vscode.md)
- [Windows 上的 SQL Server Management Studio (SSMS)](sql-server-linux-manage-ssms.md)
- [Azure Data Studio](../azure-data-studio/what-is-azure-data-studio.md)
- [mssql-cli (預覽)](https://github.com/dbcli/mssql-cli/blob/master/doc/usage_guide.md)
- [PowerShell Core](sql-server-linux-manage-powershell-core.md)

## <a name="remove-your-container"></a>移除容器

若要移除本教學課程中用到的 SQL Server 容器，請執行下列命令：

::: zone pivot="cs1-bash"
```bash
sudo docker stop sql1
sudo docker rm sql1
```
::: zone-end

::: zone pivot="cs1-powershell"
```PowerShell
docker stop sql1
docker rm sql1
```
::: zone-end

::: zone pivot="cs1-cmd"
```cmd
docker stop sql1
docker rm sql1
```
::: zone-end

> [!WARNING]
> 停止及移除容器會永久刪除容器中的所有 SQL Server 資料。 如須保留資料，[請建立備份檔案並將其複製到容器外](tutorial-restore-backup-in-sql-server-container.md)，或使用[容器資料持續性技術](sql-server-linux-docker-container-configure.md#persist)。

## <a name="docker-demo"></a>Dock 示範

在您嘗試使用適用於 Docker 的 SQL Server 容器映像後，或許會想要了解如何使用 Docker 來改善開發與測試。 下方影片會示範如何在持續整合與部署案例中使用 Docker。

> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T152/player]

## <a name="next-steps"></a>後續步驟

如需如何將資料庫備份檔案還原至容器中的教學課程，請參閱[在 Linux Docker 容器中還原 SQL Server 資料庫](tutorial-restore-backup-in-sql-server-container.md)。 探索其他案例，例如執行[多個容器](sql-server-linux-docker-container-deployment.md#multiple)、[資料持續性](sql-server-linux-docker-container-configure.md#persist)，以及[疑難排解](sql-server-linux-docker-container-troubleshooting.md)。

此外，也可以前往 [mssql-docker GitHub 存放庫](https://github.com/Microsoft/mssql-docker) 取得資源、意見反應和已知問題。
