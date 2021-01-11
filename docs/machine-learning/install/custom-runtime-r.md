---
title: 安裝 R 自訂執行階段
description: 了解如何安裝 SQL Server 適用的 R 自訂執行階段。
ms.prod: sql
ms.technology: machine-learning-services
ms.date: 09/20/2020
ms.topic: how-to
author: dphansen
ms.author: davidph
ms.custom: seo-lt-2019
monikerRange: '>=sql-server-ver15||>=sql-server-linux-ver15'
ms.openlocfilehash: 9029dd87712ee7128504a9b495b699c9c58e000d
ms.sourcegitcommit: 5b2c47ce88f7e56552fd415c32b319009d043b56
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/29/2020
ms.locfileid: "97804342"
---
# <a name="install-an-r-custom-runtime-for-sql-server"></a>安裝 SQL Server 適用的 R 自訂執行階段

[!INCLUDE [SQL Server 2019 and later](../../includes/applies-to-version/sqlserver2019.md)]

本文說明如何安裝自訂執行階段，以使用 SQL Server 執行 R 指令碼。 自訂執行階段使用以擴充性架構為基礎的語言延伸模組技術來執行外部程式碼。 R 適用的自訂執行階段可用於下列案例：

+ 安裝具備擴充性架構的 SQL Server。

+ 安裝 SQL Server 2019 機器學習服務。 完成一些額外的設定步驟後，可搭配 [SQL Server 機器學習服務](../sql-server-machine-learning-services.md)使用語言延伸模組。

::: moniker range=">=sql-server-ver15"

> [!NOTE]
> 本文說明如何在 Windows 上安裝 R 適用的自訂執行階段。 若要在 Linux 上安裝，請參閱[安裝適用於 Linux 上的 SQL Server 的 R 自訂執行階段](custom-runtime-r.md?view=sql-server-linux-ver15&preserve-view=true) (英文)。

## <a name="pre-install-checklist"></a>預先安裝檢查清單

安裝 R 自訂執行階段之前，請先安裝下列項目：

+ [Windows 適用的 SQL Server 2019 (累積更新 3 或更新版本)](../../database-engine/install-windows/install-sql-server.md)。

+ [Windows 上具備擴充性架構的 SQL Server 語言延伸模組](../../language-extensions/install/windows-java.md)。

+ [R 版本 3.3 或更新版本](https://cran.r-project.org/) (英文)。

## <a name="add-sql-server-language-extensions-for-windows"></a>新增 Windows 適用的 SQL Server 語言延伸模組

> [!NOTE]
> 如果您已在 SQL Server 2019 上安裝了機器學習服務，便已經安裝了語言延伸模組的擴充性架構和 Launchpad 服務，可略過此步驟。

語言延伸模組會使用擴充性架構來執行外部程式碼。 程式碼執行與核心引擎流程隔離，但與 SQL Server 查詢執行完全整合。

1. 啟動 SQL Server 2019 的安裝精靈。

1. 在 [安裝]  索引標籤上，選取 [新增 SQL Server 獨立安裝或將功能加入至現有安裝]  。

    ![SQL Server 2019 安裝 CU3 或更新版本](../install/media/2019setup-installation-page-mlsvcs.png)

1. 在 [特徵選取]  頁面上，選取下列選項：

    - **Database Engine 服務**

        若要搭配 SQL Server 使用語言延伸模組，您必須安裝資料庫引擎的執行個體。 您可以使用預設或具名執行個體。

    - **機器學習服務和語言延伸模組**

        選取 [機器學習服務和語言延伸模組]。 不需要選取 [R]。

        :::image type="content" source="media/2019-setup-language-extensions.png" alt-text="SQL Server 2019 語言延伸模組安裝。":::

1. 在 [準備安裝]  頁面上，確認包含這些選項，然後選取 [安裝]  。

    + Database Engine 服務
    + 機器學習服務和語言延伸模組

1. 在安裝程式完成之後，如果系統要求您重新啟動電腦，請立即重新啟動。 當您完成安裝時，請務必閱讀安裝精靈所提供的訊息。 如需詳細資訊，請參閱＜ [View and Read SQL Server Setup Log Files](../../database-engine/install-windows/view-and-read-sql-server-setup-log-files.md)＞。

## <a name="install-r"></a>安裝 R

> [!NOTE]
>若是 SQL 機器學習服務，R 已經安裝在 SQL Server 執行個體的 **R_SERVICES** 資料夾中。 例如：C:\Program Files\Microsoft SQL Server\MSSQL15.MSSQLSERVER\R_SERVICES。 如果您想要繼續使用此路徑做為 R_HOME，請跳至安裝 Rcpp 的下一個步驟。 如果您想要使用不同的 R 執行階段，請繼續在此安裝。

安裝 [R (3.3 或更新版本)](https://cran.r-project.org/bin/windows/base/) 並記下其安裝路徑。 此路徑是您的 **R_HOME**。 舉例來說，此處所示的 R_HOME 是「C:\Program Files\R\R-4.0.2」

![安裝自訂 R](../install/media/custom-r-installation.png)

> [!NOTE]
>在下列指示中，%R_HOME% 是 R 安裝的路徑，請以該值取代。

## <a name="install-rcpp-package"></a>安裝 Rcpp 套件

+ 找出 %R_HOME%\bin 中的 R 可執行檔。 根據預設，會位於 `C:\Program Files\R\R-4.0.2\bin`。

+ 從提升權限命令提示字元啟動 R：

```CMD
%R_HOME%\bin\R.exe
```

在此提升權限的 R 提示字元 (%R_HOME%\bin\R.exe) 中執行下列指令碼，在 %R_HOME%\library 資料夾中安裝 Rcpp 套件。

```R
install.packages("Rcpp", lib="%R_HOME%/library");
```

## <a name="update-the-system-environment-variables"></a>更新系統環境變數

1. 新增或修改 **R_HOME**，做為系統環境變數。
    + 在 Windows 搜尋方塊中，輸入「環境」，然後選取 [編輯系統環境變數]。
    + 在 [進階] 索引標籤中，選取 [環境變數]。

    + 在 [系統變數] 底下，選取 [新增] 來建立 R_HOME。
    若要修改，請選取 [編輯] 來變更。 請修改其值，指向自訂的 R 安裝路徑。

    ![建立 R_HOME 系統環境變數。](../install/media/sys-env-r-home.png)

2. 更新 **PATH** 環境變數。
    將 **R.dll** 的路徑附加至系統 **PATH** 環境變數。 選取 [PATH]，然後 [編輯]，並將 `%R_HOME%\bin\x64` 新增至路徑清單。

    ![附加至 PATH 系統環境變數。](../install/media/sys-env-path-r.png)

3. 選取 [確定] 關閉其餘視窗。

    也可以改為從提升權限的命令提示字元設定這些環境變數，請執行下列命令。 請務必使用自訂 R 安裝路徑。

```CMD
setx /m R_HOME "path\to\installation\of\R"
setx /m PATH "path\to\installation\of\R\bin\x64;%PATH%"
```

## <a name="grant-access-to-the-custom-r-installation-folder"></a>授與自訂 R 安裝資料夾的存取權

> [!NOTE]
>如果您已在 **C:\Program Files\R\R-version** 的預設位置安裝 R，可以略過此步驟。

從全新提升權限的命令提示字元執行 **icacls** 命令，將讀取和執行權限授與 **SQL Server Launchpad 服務使用者名稱** 和 SID **S-1-15-2-1** (**所有應用程式套件**)。 Launchpad 服務使用者名稱採用的格式是 `NT Service\MSSQLLAUNCHPAD$INSTANCENAME`，其中 `INSTANCENAME` 是您 SQL Server 的執行個體名稱。

這些命令會以遞迴方式授與指定目錄路徑下的所有檔案與資料夾的存取權。

將執行個體名稱附加至 `MSSQLLAUNCHPAD` (`MSSQLLAUNCHPAD$INSTANCENAME`)。 在此範例中，`INSTANCENAME` 是預設執行個體 `MSSQLSERVER`。

1. 授與權限給 **SQL Server Launchpad 服務使用者名稱**

    ```cmd
    icacls "%R_HOME%" /grant "NT Service\MSSQLLAUNCHPAD$MSSQLSERVER":(OI)(CI)RX /T
    ```
2. 授與權限給 **SID S-1-15-2-1**

    ```cmd
    icacls "%R_HOME%" /grant *S-1-15-2-1:(OI)(CI)RX /T

>[!NOTE]
>The preceding command grants permissions to the computer **SID S-1-15-2-1**, which is equivalent to ALL APPLICATION PACKAGES on an English version of Windows. Alternatively, you can use `icacls "%R_HOME%" /grant "ALL APPLICATION PACKAGES":(OI)(CI)RX /T` on an English version of Windows.

## Restart SQL Server Launchpad service

From an *elevated* command prompt, run the following commands. Replace "MSSQLSERVER" with the name of your SQL Server instance.


```CMD
net stop MSSQLLAUNCHPAD$MSSQLSERVER
net start MSSQLLAUNCHPAD$MSSQLSERVER
```

也可以在系統的 **服務** 應用程式中用滑鼠右鍵按一下 [SQL Server Launchpad] 服務，然後選取 [重新啟動] 命令。 或使用 [SQL Server 組態管理員](../../relational-databases/sql-server-configuration-manager.md) 來重新啟動服務。

## <a name="download-r-language-extension"></a>下載 R 語言延伸模組

下載[包含適用於 Windows 之 R 語言延伸模組的 ZIP 檔案](https://github.com/microsoft/sql-server-language-extensions/releases) \(英文\)。 建議在生產環境中使用發行版本。 在開發或測試時使用偵錯版本，因為其提供詳細的記錄資訊，可供您調查任何錯誤。

## <a name="register-external-language"></a>註冊外部語言

配合您要使用的每個資料庫，使用 [CREATE EXTERNAL LANGUAGE](../../t-sql/statements/create-external-language-transact-sql.md) 來註冊此 R 語言延伸模組。 使用 [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/download-azure-data-studio) 連線到 SQL Server 並執行下列的 T-SQL 命令。
修改此陳述式中的路徑，以反映下載的語言延伸模組 zip 檔案 (R-lang-extension.zip) 的位置。

> [!NOTE]
>**R** 是保留的字組。 請針對外部語言使用不同的名稱，例如「myR」。

```sql
CREATE EXTERNAL LANGUAGE [myR]
FROM (CONTENT = N'/path/to/R-lang-extension.zip', FILE_NAME = 'libRExtension.dll');
GO
```

::: moniker-end

::: moniker range=">=sql-server-linux-ver15"

您可在 Red Hat Enterprise Linux (RHEL)、SUSE Linux Enterprise Server (SLES) 和 Ubuntu 上安裝 SQL Server。 如需詳細資訊，請參閱 [Linux 上的 SQL Server 安裝指引](../../linux/sql-server-linux-setup.md#supportedplatforms)中的＜支援的平台＞一節。

> [!NOTE]
> 本文說明如何在 Linux 上安裝 R 適用的自訂執行階段。 若要在 Windows 上安裝，請查看[在 Windows 上安裝 SQL Server 適用的 R 自訂執行階段](custom-runtime-r.md?view=sql-server-ver15&preserve-view=true)

## <a name="pre-install-checklist"></a>預先安裝檢查清單

安裝 R 自訂執行階段之前，請先安裝下列項目：

+ [Linux 適用的 SQL Server 2019 (累積更新 3 或更新版本)](../../linux/sql-server-linux-setup.md)。
在 Linux 上安裝 SQL Server 之前，您必須設定 Microsoft 存放庫。 如需詳細資訊，請參閱[設定存放庫](../../linux/sql-server-linux-change-repo.md)。

+ [Linux 上具備擴充性架構的 SQL Server 語言延伸模組](../../linux/sql-server-linux-setup-language-extensions-java.md)。

+ [R 版本 3.3 或更新版本](https://cran.r-project.org/) (英文)。

## <a name="add-sql-server-language-extensions-for-linux"></a>新增 Linux 適用的 SQL Server 語言延伸模組

> [!NOTE]
> 如果您已在 SQL Server 2019 上安裝了機器學習服務，便已經安裝語言延伸模組適用的 **mssql-server-extensibility** 套件，可略過此步驟。

語言延伸模組會使用擴充性架構來執行外部程式碼。 程式碼執行與核心引擎流程隔離，但與 SQL Server 查詢執行完全整合。

使用下列命令，根據您的 Linux 版本安裝語言延伸模組。

### <a name="ubuntu"></a>Ubuntu
> [!Tip]
> 可能的話，請在安裝之前`sudo apt-get update`，以重新整理系統上的套件。 Ubuntu 可能沒有 HTTPs apt 傳輸選項。 若要安裝它，請使用 `sudo apt-get install apt-transport-https`。

```bash
# Install as root or sudo
sudo apt-get install mssql-server-extensibility
```

### <a name="red-hat"></a>Red Hat
```bash
# Install as root or sudo
sudo yum install mssql-server-extensibility
```

### <a name="suse"></a>Suse
```bash
# Install as root or sudo
sudo zypper install mssql-server-extensibility
```

## <a name="install-r"></a>安裝 R

>[!NOTE]
>若是 SQL 機器學習服務，R 已經安裝在 `/opt/microsoft/ropen/3.5.2/lib64/R`。 如果您想要繼續使用此路徑做為 R_HOME，請跳至 **安裝 Rcpp** 的下一個步驟。 

如果您想要使用不同的 R 執行階段，必須先移除 `microsoft-r-open-mro`，才能繼續安裝新的版本。 Ubuntu 範例：

```bash
sudo apt remove microsoft-r-open-mro-3.5.2
```

請依照[指示](https://cran.r-project.org/bin/linux/)為個別的 linux 平台完成 R (3.3 或更新版本) 的安裝。 根據預設，R 會安裝在 **/usr/lib/R** 中。 此路徑是您的 **R_HOME**。 如果您將 R 安裝在其他位置，請記下該路徑做為您的 R_HOME。

Ubuntu 的範例指示。 請針對您的 R 版本變更以下的存放庫 URL。

```bash
export DEBIAN_FRONTEND=noninteractive
sudo apt-get update
sudo apt-get --no-install-recommends -y install curl zip unzip apt-transport-https libstdc++6

# Add R CRAN repository. This repository works for R 4.0.x.
#
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys E298A3A825C0D65DFD57CBB651716619E084DAB9
sudo add-apt-repository 'deb https://cloud.r-project.org/bin/linux/ubuntu xenial-cran40/'
sudo apt-get update

# Install R runtime.
#
sudo apt-get -y install r-base-core
```

## <a name="install-rcpp-package"></a>安裝 Rcpp 套件

在下列指示中，${R_HOME} 是 R 安裝的路徑。 

+ 在 ${R_HOME}/bin 中找出 R 二進位檔。 根據預設，這會位於 **/usr/lib/R/bin** 中。

+ 啟動 R

  ```bash
  sudo ${R_HOME}/bin/R
  ```

+ 在此提升權限的 R 提示字元 (${R_HOME}/bin/R) 中執行下列指令碼，以便在 ${R_HOME}/library 資料夾中安裝 **Rcpp** 套件。

  ```R
  install.packages("Rcpp", lib = "${R_HOME}/library");
  ```

## <a name="using-a-custom-installation-of-r"></a>使用 R 的自訂安裝

> [!NOTE]
>如果您已在 **/usr/lib/R** 的預設位置安裝 R，可以略過此節。

### <a name="update-the-environment-variables"></a>更新環境變數

1. 編輯 mssql-launchpadd 服務，將 R_HOME 環境變數新增至檔案 `/etc/systemd/system/mssql-launchpadd.service.d/override.conf`

    ```bash
    sudo systemctl edit mssql-launchpadd
    ```

    + 在開啟的 `/etc/systemd/system/mssql-launchpadd.service.d/override.conf` 檔案中插入下列文字。 將 R_HOME 的值設定為自訂 R 安裝路徑。

    ```text
    [Service]
    Environment="R_HOME=/path/to/installation/of/R"
    ```

    + 儲存並關閉。

2. 請確定可以載入 **libR.so**。

    + 在 **/etc/ld.so.conf.d** 中建立 custom-r.conf 檔案。

    ```bash
    sudo vi /etc/ld.so.conf.d/custom-r.conf
    ```

    + 在開啟的檔案中，從自訂 R 安裝版本新增 **libR.so** 的路徑。

    ```vi editor
    /path/to/installation/of/R/lib
    ```

    + 儲存並關閉新檔案。

    + 執行下列命令，並檢查是否找得到所有相依的程式庫，以執行 `ldconfig` 並確認可以載入 **libR.so**。

    ```bash
    sudo ldconfig
    ldd /path/to/installation/of/R/lib/libR.so
    ```

### <a name="grant-access-to-the-custom-r-installation-folder"></a>授與自訂 R 安裝資料夾的存取權

在 /var/opt/mssql/mssql.conf 檔案的 [擴充性] 區段中，將 `datadirectories` 選項設定為自訂 R 安裝。

```bash
sudo /opt/mssql/bin/mssql-conf set extensibility.datadirectories /path/to/installation/of/R
```

### <a name="restart-mssql-launchpadd-service"></a>重新啟動 mssql-launchpadd 服務

> [!NOTE]
>如果您已在 **/usr/lib/R** 的預設位置安裝 R，可以略過此步驟。

```bash
sudo systemctl restart mssql-launchpadd
```

## <a name="download-r-language-extension"></a>下載 R 語言延伸模組

下載[包含適用於 Linux 之 R 語言延伸模組的 ZIP 檔案](https://github.com/microsoft/sql-server-language-extensions/releases) \(英文\)。 建議在生產環境中使用發行版本。 在開發或測試時使用偵錯版本，因為其提供詳細的記錄資訊，可供您調查任何錯誤。

## <a name="register-external-language"></a>註冊外部語言

配合您要使用的每個資料庫，使用 [CREATE EXTERNAL LANGUAGE](../../t-sql/statements/create-external-language-transact-sql.md) 來註冊此 R 語言延伸模組。 使用 [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/download-azure-data-studio) 連線到 SQL Server 並執行下列的 T-SQL 命令。 
修改此陳述式中的路徑，以反映下載的語言延伸模組 zip 檔案 (r-lang-extension.zip) 的位置。


> [!NOTE]
>**R** 是保留的字組。 請針對外部語言使用不同的名稱，例如「myR」。

```sql
CREATE EXTERNAL LANGUAGE [myR]
FROM (CONTENT = N'/path/to/R-lang-extension.zip', FILE_NAME = 'libRExtension.so.1.0');
GO
```

::: moniker-end

## <a name="enable-external-script-execution-in-sql-server"></a>啟用 SQL Server 中的外部指令碼執行

您可以透過針對 SQL Server 執行的預存程序 [sp_execute_external script](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md)，來執行 R 中的外部指令碼。 

若要啟用外部指令碼，請使用連線至 SQL Server 的 [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/download-azure-data-studio) 來執行下列 SQL 命令。

```sql
sp_configure 'external scripts enabled', 1;
RECONFIGURE WITH OVERRIDE;
```

## <a name="verify-language-extension-installation"></a>驗證語言延伸模組安裝

此 SQL 指令碼會驗證是否安裝成功自訂的 R 語言延伸模組。 輸出此指令碼時應會顯示 R_HOME、R 的路徑以及自訂 R 執行階段的版本； 而且會確認指令碼使用的是您的自訂執行階段。

```sql
EXEC sp_execute_external_script
    @language =N'myR',
    @script=N'
print(R.home());
print(file.path(R.home("bin"), "R"));
print(R.version);
print("Hello RExtension!");'
```

## <a name="verify-parameters-and-datasets-of-different-data-types"></a>驗證不同資料類型的參數和資料集

此指令碼會針對輸入/輸出參數和資料集來測試不同的資料類型。

```sql
DECLARE @sumVal INT = 12;
DECLARE @charVal VARCHAR(30) = N'Hello';
EXEC sp_execute_external_script
    @language =N'myR',
    @script=N'
print(sumVal);
print(charVal);
sumVal <- sumVal + 300;
OutputDataSet <- InputDataSet;'
    ,@input_data_1 = N'select 1, cast(1.4 as real), ''Hi'', cast(''1'' as bit)'
    ,@params = N'@sumVal int OUTPUT, @charVal VARCHAR(30)'
    ,@sumVal = @sumVal OUTPUT
    ,@charVal =  @charVal
WITH RESULT SETS ((intCol INT, doubleCol REAL, charCol CHAR(2), logicalCol BIT));
PRINT @sumVal;
```

## <a name="see-also"></a>另請參閱

+ [SQL Server 中的擴充性架構](../concepts/extensibility-framework.md)
+ [語言延伸模組概觀](../../language-extensions/language-extensions-overview.md)