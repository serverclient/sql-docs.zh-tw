---
title: 安裝 Python 自訂執行階段
description: 了解如何使用語言延伸模組來安裝適用於 SQL Server 的 Python 自訂執行階段。 Python 自訂執行階段可用於機器學習。
ms.prod: sql
ms.technology: machine-learning-services
ms.date: 11/30/2020
ms.topic: how-to
author: dphansen
ms.author: davidph
ms.custom: seo-lt-2019
zone_pivot_groups: python-custom-runtime-platform
monikerRange: '>=sql-server-ver15||>=sql-server-linux-ver15'
ms.openlocfilehash: 91aace4333b4496338b782344e64cdfea2b886bd
ms.sourcegitcommit: 5b2c47ce88f7e56552fd415c32b319009d043b56
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/29/2020
ms.locfileid: "97804318"
---
# <a name="install-a-python-custom-runtime-for-sql-server"></a>安裝 SQL Server 適用的 Python 自訂執行階段
[!INCLUDE [SQL Server 2019 and later](../../includes/applies-to-version/sqlserver2019.md)]

此文章說明如何安裝 Python 自訂執行階段，以使用 SQL Server 執行外部 Python 指令碼。 自訂執行階段會使用 [SQL Server 語言延伸模組](../../language-extensions/language-extensions-overview.md)並可用於執行機器學習指令碼。

Python 自訂執行階段可讓您搭配 SQL Server 使用自己的 Python 執行階段版本，而不是隨 [SQL Server Machine Learning 服務](../sql-server-machine-learning-services.md)一起安裝的預設執行階段版本。

::: zone pivot="python-custom-runtime-windows"

## <a name="prerequisites"></a>必要條件

安裝 Python 自訂執行階段之前，請先安裝下列項目：

+ 安裝 SQL Server 2019 的[累積更新 (CU) 3 或更新版本](../../database-engine/install-windows/latest-updates-for-microsoft-sql-server.md)。

+ 在伺服器上安裝 [Python 3.7](https://www.python.org/downloads/)。

    用於自訂 Python 執行階段的 Python 語言延伸模組目前僅支援 Python 3.7。 如果您想要使用不同版本的 Python，請遵循 [Python 語言延伸模組 GitHub 存放庫](https://github.com/microsoft/sql-server-language-extensions/tree/master/language-extensions/python)中的指示修改並重建延伸模組。

    > [!IMPORTANT]
    > 在安裝 Python 期間，請檢查 **將 Python 3.7 新增至路徑**。

## <a name="install-language-extensions"></a>安裝語言擴充功能

> [!NOTE]
> 如果您已在 SQL Server 2019 上安裝[機器學習服務](../sql-server-machine-learning-services.md)，便已經安裝語言延伸模組，而且您可以跳過此步驟。

請遵循下列步驟來安裝用於 Python 自訂執行階段的 [SQL Server 語言延伸模組](../../language-extensions/language-extensions-overview.md)。

1. 啟動 SQL Server 2019 的安裝精靈。
  
1. 在 [安裝]  索引標籤上，選取 [新增 SQL Server 獨立安裝或將功能加入至現有安裝]  。

1. 在 [特徵選取]  頁面上，選取下列選項：
  
    + **Database Engine 服務**
  
        若要搭配 SQL Server 使用語言延伸模組，您必須安裝資料庫引擎的執行個體。 您可以使用新的或現有的執行個體。
  
    + **機器學習服務和語言延伸模組**

        選取 [機器學習服務和語言延伸模組]。 請勿選取 Python，因為您稍後將會安裝自訂 Python 執行階段。

        :::image type="content" source="media/2019-setup-language-extensions.png" alt-text="SQL Server 2019 語言延伸模組安裝。":::

1. 在 [準備安裝]  頁面上，確認包含這些選項，然後選取 [安裝]  。
  
    + Database Engine 服務
    + 機器學習服務和語言延伸模組

1. 安裝完成之後，如果系統要求，請重新啟動電腦。

> [!IMPORTANT]
> 如果您安裝具有語言延伸模組的新 SQL Server 2019 執行個體，請先安裝[累積更新 (CU) 3 或更新版本](../../database-engine/install-windows/latest-updates-for-microsoft-sql-server.md)，再繼續進行下一個步驟。

## <a name="install-pandas"></a>安裝 pandas

從 *已提升權限的* 命令提示字元中，安裝 Python 適用的 [pandas](https://pandas.pydata.org/) 套件：

```bash
python.exe -m pip install pandas
```

## <a name="add-environment-variable"></a>新增環境變數

新增或修改系統環境變數 **PYTHONHOME**。

1. 在 Windows 搜尋方塊中，輸入「環境」，然後選取 [編輯系統環境變數]。
1. 在 [進階] 索引標籤中，選取 [環境變數]。
1. 在 [系統變數] 下，選取 [新增] 以建立指向 Python 3.7 安裝位置的 **PYTHONHOME**。 如果 PYTHONHOME 已存在，請選取 [編輯]，將其指向 Python 3.7 安裝位置。
1. 選取 [確定] 以關閉所有視窗。

    :::image type="content" source="media/pythonhome-env-variable.png" alt-text="PYTHONHOME 環境變數。":::

## <a name="grant-access-to-python-folder"></a>授與 Python 資料夾的存取權

從 *已提升權限* 的新命令提示字元執行下列 **icacls** 命令，將 **PYTHONHOME** 的 **讀取和執行** 權限授與 **SQL Server Launchpad 服務** 與 SID **S-1-15-2-1** (**ALL_APPLICATION_PACKAGES**)。

1. 授與權限給 **SQL Server Launchpad 服務使用者名稱**。

    ```cmd
    icacls "%PYTHONHOME%" /grant "NT Service\MSSQLLAUNCHPAD":(OI)(CI)RX /T
    ```

    針對具名執行個體，名為 **SQL01** 之執行個體的命令將為 `icacls "%PYTHONHOME%" /grant "NT Service\MSSQLLAUNCHPAD$SQL01":(OI)(CI)RX /T`。

2. 授與權限給 **SID S-1-15-2-1**。

    ```cmd
    icacls "%PYTHONHOME%" /grant *S-1-15-2-1:(OI)(CI)RX /T
    ```

    前述命令會授與權限給電腦 **SID S-1-15-2-1**，這相當於英文版 Windows 上的 **所有應用程式套件**。 或者，您也可以在英文版的 Windows 上使用 `icacls "%R_HOME%" /grant "ALL APPLICATION PACKAGES":(OI)(CI)RX /T`。

## <a name="restart-sql-server-launchpad"></a>重新啟動 SQL Server Launchpad

依照下列步驟重新啟動 SQL Server Launchpad 服務。

1. 開啟 [SQL Server 組態管理員](../../relational-databases/sql-server-configuration-manager.md)。

1. 在 [SQL Server 服務] 下，以滑鼠右鍵按一下 [SQL Server Launchpad (MSSQLSERVER)]，然後選取 [重新啟動]。 如果您使用的是具名執行個體，將會顯示執行個體名稱，而不是 **(MSSQLSERVER)** 。

## <a name="register-language-extension"></a>註冊語言延伸模組

遵循下列步驟來下載並註冊 Python 語言延伸模組，以用於 Python 自訂執行階段。

1. 從 [SQL Server 語言延伸模組 GitHub 存放庫](https://github.com/microsoft/sql-server-language-extensions/releases)下載 **python-lang-extension-windows.zip** 檔案。

    或者，您可以在開發或測試環境中使用偵錯版本 (**python-lang-extension-windows-debug.zip**)。 偵錯版本提供詳細的記錄資訊以方便調查任何錯誤，且不建議用於生產環境。

1. 使用 [Azure Data Studio](../../azure-data-studio/what-is-azure-data-studio.md) 來連線到您的 SQL Server 執行個體，並執行下列 T-SQL 命令，使用[建立外部語言](../../t-sql/statements/create-external-language-transact-sql.md)註冊 Python 語言延伸模組。 

    修改此陳述式中的路徑，以反映下載之語言延伸模組 zip 檔案 (**python-lang-extension-windows.zip**) 的位置。

    ```sql
    CREATE EXTERNAL LANGUAGE [myPython]
    FROM (CONTENT = N'/path/to/python-lang-extension-windows.zip', FILE_NAME = 'pythonextension.dll');
    GO
    ```

    針對您要在其中使用 Python 語言延伸模組的每個資料庫執行陳述式。

    > [!NOTE]
    > **Python** 是保留字，而且不能作為新外部語言名稱的名稱使用。 請改為使用不同的名稱。 例如，上述陳述式使用 **myPython**。

::: zone-end

::: zone pivot="python-custom-runtime-linux"

## <a name="prerequisites"></a>必要條件

安裝 Python 自訂執行階段之前，請先安裝下列項目：

+ 安裝適用於 Linux 的 SQL Server 2019。 您可在 Red Hat Enterprise Linux (RHEL)、SUSE Linux Enterprise Server (SLES) 和 Ubuntu 上安裝 SQL Server。 如需詳細資訊，請參閱 [Linux 上的 SQL Server 的安裝指引](../../linux/sql-server-linux-setup.md)。

+ 升級至 SQL Server 2019 的累積更新 (CU) 3 或更新版本。 依照下列步驟執行：
    1. 為累積更新設定存放庫。 如需詳細資訊，請參閱[設定用於安裝和升級 Linux 上 SQL Server 的存放庫](../../linux/sql-server-linux-change-repo.md)。

    1. 將 **mssql-server** 套件更新為最新的累積更新。 如需詳細資訊，請參閱 [Linux 上的 SQL Server 的安裝指引中的＜更新或升級 SQL Server＞一節](../../linux/sql-server-linux-setup.md#upgrade)。

+ 在伺服器上安裝 [Python 3.7](https://www.python.org/downloads/)。

    用於自訂 Python 執行階段的 Python 語言延伸模組目前僅支援 Python 3.7。 如果您想要使用不同版本的 Python，請遵循 [Python 語言延伸模組 GitHub 存放庫](https://github.com/microsoft/sql-server-language-extensions/tree/master/language-extensions/python)中的指示修改並重建延伸模組。

## <a name="install-language-extensions"></a>安裝語言擴充功能

> [!NOTE]
> 如果您已在 SQL Server 2019 上安裝了 [機器學習服務](../sql-server-machine-learning-services.md)，便已經安裝適用於語言延伸模組的 **mssql-server-extensibility** 套件，而且可以跳過此步驟。

遵循下方的命令在 Linux 上安裝用於 Python 自訂執行階段的 [SQL Server 語言延伸模組](../../language-extensions/language-extensions-overview.md)。

#### <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

1. 可能的話，請在安裝之前執行此命令以重新整理系統上的套件。

    ```bash
    # Install as root or sudo
    sudo apt-get update
    ```

1. Ubuntu 可能沒有 HTTPs apt 傳輸選項。 若要加以安裝，請執行此命令。

    ```bash
    # Install as root or sudo
    apt-get install apt-transport-https
    ```

1. 使用此命令安裝 **mssql-server-extensibility**。

    ```bash
    # Install as root or sudo
    sudo apt-get install mssql-server-extensibility
    ```

#### <a name="red-hat-enterprise-linux-rhel"></a>[Red Hat Enterprise Linux (RHEL)](#tab/rhel)

```bash
# Install as root or sudo
sudo yum install mssql-server-extensibility
```

#### <a name="suse-linux-enterprise-server-sles"></a>[SUSE Linux Enterprise Server (SLES)](#tab/sles)

```bash
# Install as root or sudo
sudo zypper install mssql-server-extensibility
```

---

## <a name="install-python-37-and-pandas"></a>安裝 Python 3.7 和 pandas

安裝 Python 3.7、libpython 3.7 程式庫和 pandas 套件。 

以下是 Ubuntu 適用的範例命令：

```bash
# Install python3.7 and the corresponding library:
sudo add-apt-repository ppa:deadsnakes/ppa
sudo apt-get update
sudo apt-get install python3.7 python3-pip libpython3.7

# Install pandas to /usr/lib:
sudo python3.7 -m pip install pandas -t /usr/lib/python3.7/dist-packages
```

## <a name="custom-installation-of-python"></a>Python 的自訂安裝

> [!NOTE]
> 如果您已在 `/usr/lib/python3.7` 的預設位置安裝 Python 3.7，可以跳過此節並移至[註冊語言延伸模組](#register-language-extension-linux)一節。

如果您已建立自己的 Python 3.7 版本，請使用下列命令讓 SQL Server 知道您的自訂安裝。

### <a name="add-environment-variable"></a>新增環境變數

首先，請編輯 **mssql-launchpadd** 服務，將 **PYTHONHOME** 環境變數新增至檔案 `/etc/systemd/system/mssql-launchpadd.service.d/override.conf`

1. 使用 systemctl 開啟檔案

    ```bash
    sudo systemctl edit mssql-launchpadd
    ```

1. 在開啟的 `/etc/systemd/system/mssql-launchpadd.service.d/override.conf` 檔案中插入下列文字。 將 **PYTHONHOME** 的值設定為自訂 Python 安裝路徑。

    ```
    [Service]
    Environment="PYTHONHOME=/path/to/installation/of/python3.7"
    ```

1. 儲存檔案並關閉編輯器。

接下來，請確定可以載入 `libpython3.7m.so.1.0`。

1. 在 `/etc/ld.so.conf.d` 中建立 custom-python.conf 檔案。

    ```bash
    sudo vi /etc/ld.so.conf.d/custom-python.conf
    ```

1. 在開啟的檔案中，從自訂 Python 安裝新增 **libpython3.7m.so.1.0** 的路徑。

    ```
    /path/to/installation/of/python3.7/lib
    ```

1. 儲存新檔案並關閉編輯器。

1. 執行下列命令，並檢查是否找得到所有相依的程式庫，以執行 `ldconfig` 並確認可以載入 `libpython3.7m.so.1.0`。

    ```bash
    sudo ldconfig
    ldd /path/to/installation/of/python3.7/lib/libpython3.7m.so.1.0
    ```

### <a name="grant-access-to-python-folder"></a>授與 Python 資料夾的存取權

在 /var/opt/mssql/mssql.conf 檔案的 [擴充性] 區段中，將 `datadirectories` 選項設定為自訂 python 安裝。

```bash
sudo /opt/mssql/bin/mssql-conf set extensibility.datadirectories /path/to/installation/of/python3.7
```

### <a name="restart-mssql-launchpadd"></a>重新啟動 mssql-launchpadd

```bash
sudo systemctl restart mssql-launchpadd
```

<a name="register-language-extension-linux"></a>

## <a name="register-language-extension"></a>註冊語言延伸模組

遵循下列步驟來下載並註冊 Python 語言延伸模組，以用於 Python 自訂執行階段。

1. 從 [SQL Server 語言延伸模組 GitHub 存放庫](https://github.com/microsoft/sql-server-language-extensions/releases)下載 **python-lang-extension-linux.zip** 檔案。

    或者，您可以在開發或測試環境中使用偵錯版本 (**python-lang-extension-linux-debug.zip**)。 偵錯版本提供詳細的記錄資訊以方便調查任何錯誤，且不建議用於生產環境。

1. 使用 [Azure Data Studio](../../azure-data-studio/what-is-azure-data-studio.md) 來連線到您的 SQL Server 執行個體，並執行下列 T-SQL 命令，使用[建立外部語言](../../t-sql/statements/create-external-language-transact-sql.md)註冊 Python 語言延伸模組。 

    修改此陳述式中的路徑，以反映下載之語言延伸模組 zip 檔案 (**python-lang-extension-linux.zip**) 的位置。

    ```sql
    CREATE EXTERNAL LANGUAGE [myPython]
    FROM (CONTENT = N'/path/to/python-lang-extension-linux.zip', FILE_NAME = 'libPythonExtension.so.1.0');
    GO
    ```

    針對您要在其中使用 Python 語言延伸模組的每個資料庫執行陳述式。

    > [!NOTE]
    > **Python** 是保留字，而且不能作為新外部語言名稱的名稱使用。 請改為使用不同的名稱。 例如，上述陳述式使用 **myPython**。

::: zone-end

## <a name="enable-external-script"></a>啟用外部指令碼

您可以使用預存程序 [sp_execute_external script](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md) 執行 Python 外部指令碼。

若要啟用外部指令碼，請使用 [Azure Data Studio](../../azure-data-studio/what-is-azure-data-studio.md) 來執行下面的陳述式。

```sql
sp_configure 'external scripts enabled', 1;
RECONFIGURE WITH OVERRIDE;  
```

## <a name="verify-installation"></a>確認安裝

使用下列 SQL 指令碼來確認 Python 自訂執行階段的安裝與功能。

```sql
EXEC sp_execute_external_script
@language =N'myPython',
@script=N'
import sys
print(sys.path)
print(sys.version)
print(sys.executable)'
```

## <a name="next-steps"></a>後續步驟

+ [安裝 SQL Server 適用的 R 自訂執行階段](custom-runtime-r.md)
+ [SQL Server 中的擴充性架構](../concepts/extensibility-framework.md)
+ [語言延伸模組概觀](../../language-extensions/language-extensions-overview.md)
