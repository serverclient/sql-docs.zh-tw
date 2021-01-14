---
title: 在 Windows 上安裝 Java 語言延伸模組
titleSuffix: SQL Server Language Extensions
description: 了解如何在 Windows 上安裝 SQL Server Java 語言延伸模組功能。
author: dphansen
ms.author: davidph
ms.date: 11/11/2020
ms.topic: how-to
ms.prod: sql
ms.technology: language-extensions
monikerRange: '>=sql-server-ver15'
ms.openlocfilehash: 6974a44210f008807de5498d70083530d9b40757
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98096856"
---
# <a name="install-sql-server-java-language-extension-on-windows"></a>在 Windows 上安裝 SQL Server Java 語言延伸模組

[!INCLUDE [SQL Server 2019 and later](../../includes/applies-to-version/sqlserver2019.md)]

了解如何在 Windows 上安裝適用於 SQL Server 的 [Java 語言延伸模組](../java-overview.md)元件。 Java 語言延伸模組是 [SQL Server 語言延伸模組](../language-extensions-overview.md)的一部分。

> [!NOTE]
> 此文章適用於在 Windows 上安裝適用於 SQL Server 的 Java 語言延伸模組。 針對 Linux，請參閱[在 Linux 上安裝 SQL Server Java 語言延伸模組](../../linux/sql-server-linux-setup-language-extensions-java.md)。

<a name="prerequisites"></a>

## <a name="pre-install-checklist"></a>預先安裝檢查清單

+ 如果您想要安裝 Java 語言延伸模組的支援，則需要 SQL Server 2019 安裝程式。

+ 需要資料庫引擎執行個體。 您無法只安裝 Java 語言延伸模組功能，但是您可以透過累加方式將其新增至現有的執行個體。

+ 針對商務持續性，語言延伸模組支援 [AlwaysOn 可用性群組](../../database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server.md)。 您必須在每個節點上安裝語言延伸模組，並設定套件。

+ 支援在 SQL Server 2019 中的容錯移轉叢集上安裝 Java 語言延伸模組。

+ 請勿在網域控制站上安裝 SQL Server 語言延伸模組或 Java 語言延伸模組。 安裝程式的語言延伸模組部分將會失敗。

+ 根據預設，系統會在 SQL Server 巨量資料叢集上安裝語言延伸模組與[機器學習服務](../../machine-learning/index.yml)。 如果您使用的是巨量資料叢集，就不需要依照此文章中的步驟進行。 如需詳細資訊，請參閱[在巨量資料叢集上使用機器學習服務 (Python 和 R)](../../big-data-cluster/machine-learning-services.md)。

> [!IMPORTANT]
> 安裝完成之後，請務必完成此文章中所述的設定後步驟。 這些步驟包括讓 SQL Server 使用外部程式碼，以及新增 SQL Server 代表您執行 Java 程式碼所需的帳戶。 設定變更通常需要重新啟動執行個體，或重新啟動啟動控制板服務。

<a name="java-jre-jdk"></a>

## <a name="java-jre-or-jdk"></a>Java JRE 或 JDK

搭配 SQL Server 安裝及使用 Java 的方式有兩種：

1. 使用預設的 Java Runtime，也就是 Zulu Open JRE 11.0.3 版。 SQL Server 安裝中支援並隨附此 Runtime。

1. 使用您慣用的 Java 發行版本，而不是預設的 Java Runtime。

    Java 11 目前是 Windows 上支援的版本。 Java Runtime Environment (JRE) 是最低需求，但如果您需要 Java 編譯器與開發套件，Java 開發套件 (JDK) 就很有用。 由於 JDK 全都包含在內，因此如果您安裝 JDK，就不需要 JRE。 在 Windows 上，建議您盡可能地將 JDK 安裝在預設的 `/Program Files/` 資料夾下。 否則，需要進行額外的設定，才能授與可執行檔權限。 如需詳細資訊，請參閱此文件中的[授與權限 (Windows)](#perms-nonwindows) 一節。

    > [!NOTE]
    > 因為 Java 具有回溯相容性，舊版或許可運作，但針對 SQL Server 2019 所支援及測試的版本是 Java 11。

## <a name="get-the-installation-media"></a>取得安裝媒體

[!INCLUDE[GetInstallationMedia](../../includes/getssmedia.md)]

## <a name="run-setup"></a>執行安裝程式

如果是本機安裝，您必須以管理員身分執行安裝程式。 如果您是從遠端共用位置安裝 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]，則必須使用對遠端共用位置具有讀取和執行權限的網域帳戶。

1. 啟動 SQL Server 2019 的安裝精靈。
  
1. 在 [安裝]  索引標籤上，選取 [新增 SQL Server 獨立安裝或將功能加入至現有安裝]  。

    ![SQL Server 2019 安裝](../media/sql-install.png) 

1. 在 [特徵選取]  頁面上，選取下列選項：
  
    - **Database Engine 服務**
  
        若要搭配 SQL Server 使用語言延伸模組，您必須安裝資料庫引擎的執行個體。 您可以使用預設或具名執行個體。
  
    - **機器學習服務和語言延伸模組**
  
        此選項會安裝支援 Java 程式碼執行的語言延伸模組元件。

        - 如果您想要安裝預設的 Java Runtime (Zulu Open JRE 11.0.3)，請選取 [機器學習服務和語言延伸模組]  與 [Java]  。

        - 如果您想要使用自己的 Java Runtime，請選取 [機器學習服務和語言延伸模組]  。 請勿選取 [Java]。

        如果您要使用 R 與 Python，請參閱[在 Windows 上安裝 SQL Server 機器學習服務](../../machine-learning/install/sql-machine-learning-services-windows-install.md) \(部分機器翻譯\)。

    ![語言延伸模組的功能選項](../media/sql-install-feature-selection.png)

1. 如果您在上一個步驟中選擇 [Java]  以安裝預設的 Java Runtime，將會顯示 [Java 安裝位置]  頁面。

    選取 [安裝此安裝隨附的 Open JRE 11.0.3]  。

    ![選擇 Java 安裝位置](../media/sql-install-openjdk.png)

    > [!NOTE]
    > [提供已安裝在此電腦上之其他版本的位置]  不適用於語言延伸模組。

1. 在 [準備安裝]  頁面上，確認包含這些選項，然後選取 [安裝]  。
  
    + Database Engine 服務
    + 機器學習服務和語言延伸模組

    請注意組態檔儲存所在資料夾 (路徑 `..\Setup Bootstrap\Log` 底下) 的位置。 當安裝程式完成時，您可以在摘要檔案中檢閱已安裝的元件。

6. 在安裝程式完成之後，如果系統要求您重新啟動電腦，請立即重新啟動。 當您完成安裝時，請務必閱讀安裝精靈所提供的訊息。 如需詳細資訊，請參閱＜ [View and Read SQL Server Setup Log Files](../../database-engine/install-windows/view-and-read-sql-server-setup-log-files.md)＞。

## <a name="add-the-jre_home-variable"></a>新增 JRE_HOME 變數

`JRE_HOME` 是系統環境變數，可指定 Java 解譯器的位置。 在此步驟中，請在 Windows 上為它建立系統環境變數。

1. 尋找並複製 JRE 主路徑。

    例如，預設 Java Runtime Zulu JRE 11.0.3 的 JRE 主路徑為 `C:\Program Files\Microsoft SQL Server\MSSQL15.MSSQLSERVER\MSSQL\Binn\AZUL-OpenJDK-JRE\`。

    根據您的 SQL Server 安裝路徑，或如果您選擇其他 Java Runtime，您的 JDK 或 JRE 位置可能會與上述的範例路徑不同。 即使您已安裝 JDK，通常還是會在該安裝過程中取得 JRE 子資料夾，因此在該情況下，請指向 JRE 資料夾。 Java 延伸模組會嘗試從路徑 `%JRE_HOME%\bin\server` 載入 `jvm.dll`。

1. 在 [控制台] 中，開啟 [系統及安全性]、開啟 [系統]，然後選取 [進階系統內容]。

1. 選取 [環境變數]。

1. 為 `JRE_HOME` 建立新的系統變數，其值為 JDK/JRE 路徑 (可在步驟 1 中找到)。

1. 重新啟動[啟動控制板](../concepts/extensibility-framework.md#launchpad)。

    1. 開啟 [SQL Server 組態管理員](../../relational-databases/sql-server-configuration-manager.md)。

    1. 在 [SQL Server 服務] 底下，以滑鼠右鍵按一下 [SQL Server 啟動控制板]，然後選取 [重新啟動]  。

<a name="perms-nonwindows"></a>

## <a name="grant-access-to-non-default-jre-folder"></a>授與非預設 JRE 資料夾的存取權

如果您沒有安裝隨附於 SQL Server 的預設 Zulu Open JRE，而且未在 Program Files 下安裝 JDK 或 JRE，則需要執行下列步驟。 從 *已提高權限* 行執行 **icacls** 命令，以將 JRE 存取權授與 **SQLRUsergroup** 與 SQL Server 服務帳戶 (在 **ALL_APPLICATION_PACKAGES** 中)。 這些命令會以遞迴方式授與指定目錄路徑下的所有檔案與資料夾的存取權。

1. 授與 SQLRUserGroup 權限

    針對具名執行個體，請將執行個體名稱附加到 SQLRUsergroup (例如 `SQLRUsergroupINSTANCENAME`)。

    ```cmd
    icacls "<PATH to JRE>" /grant "SQLRUsergroup":(OI)(CI)RX /T
    ```
    
    如果您已將 JDK/JRE 安裝在 Windows 上的預設資料夾 [Program Files] 底下，則可以略過此步驟。

1. 授與 AppContainer 權限

    ```cmd
    icacls “<PATH to JRE>” /grant *S-1-15-2-1:(OI)(CI)RX /T
    ```

    > [!NOTE]
    > 上面的命令會授與電腦 SID **S-1-15-2-1** 的權限，這相當於英文版 Windows 上的 **所有應用程式套件**。 或者，您也可以在英文版的 Windows 上使用 `icacls "<PATH to JRE>" /grant "ALL APPLICATION PACKAGES":(OI)(CI)RX /T`。

## <a name="restart-the-service"></a>重新啟動服務

當安裝完成時，請先重新啟動資料庫引擎，然後再繼續執行下一個步驟，以啟用指令碼執行。

重新啟動該服務也會自動重新啟動相關的 SQL Server Launchpad 服務。

您可以針對 SSMS 中的執行個體，使用滑鼠右鍵按一下 [重新啟動] 命令，或使用 [控制台] 中的 [服務] 面板，或使用 [SQL Server 組態管理員](../../relational-databases/sql-server-configuration-manager.md)來重新啟動服務。

## <a name="enable-script-execution"></a>啟用指令碼執行

1. 開啟 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)]。 

1. 連線到您安裝語言延伸模組的執行個體，按一下 [新增查詢] 開啟查詢視窗，然後執行下列命令：

    ```sql
    sp_configure
    ```

    屬性 `external scripts enabled` 的值目前應該為 **0**。 系統預設會關閉該功能，因此必須先由系統管理員明確啟用，才可以執行 Java 程式碼。

1. 若要啟用外部指令碼功能，請執行下列陳述式：

    ```sql
    EXEC sp_configure 'external scripts enabled', 1
    RECONFIGURE WITH OVERRIDE
    ```

    如果您已針對機器學習服務啟用該功能，請不要再次執行語言延伸模組的重新設定。 底層擴充性平台支援這兩者。

<a name="register_external_language"></a>

## <a name="register-external-language"></a>註冊外部語言

針對您要在其中使用語言擴充功能的每個資料庫，您必須使用 [CREATE EXTERNAL LANGUAGE](../../t-sql/statements/create-external-language-transact-sql.md) 來註冊外部語言。

下例會將外部語言呼叫的 Java 新增至 Windows 之 SQL Server 的資料庫。

```SQL
CREATE EXTERNAL LANGUAGE Java
FROM (CONTENT = N'<path-to-zip>', FILE_NAME = 'javaextension.dll');
GO
```

如需詳細資訊，請參閱 [CREATE EXTERNAL LANGUAGE](../../t-sql/statements/create-external-language-transact-sql.md)。

## <a name="verify-installation"></a>確認安裝

在安裝程式記錄中，檢查執行個體的安裝狀態。

使用下列步驟確認用於啟動外部指令碼的所有元件都正在執行。

1. 在 SQL Server Management Studio 或 Azure Data Studio 中，開啟新的 [查詢] 視窗，並執行下列陳述式︰

    ```sql
    EXEC sp_configure 'external scripts enabled'
    ```

    **run_value** 現在設定為 1。

1. 開啟 [服務] 面板或 SQL Server 組態管理員，並確認 **SQL Server Launchpad 服務** 正在執行。 每個安裝語言延伸模組的資料庫引擎執行個體應該都有一個該服務。 如需服務的詳細資訊，請參閱[擴充性架構](../concepts/extensibility-framework.md)。

## <a name="additional-configuration"></a>其他設定

如果驗證步驟成功，您就可以從 SQL Server Management Studio、Azure Data Studio、Visual Studio Code，或任何可以將 T-SQL 陳述式傳送到伺服器的其他用戶端來執行 Java 程式碼。

如果您在執行命令時遇到錯誤，請檢閱此節中的其他設定步驟。 您可能需要對服務或資料庫進行其他適當的設定。

在執行個體層級，其他設定可能包括：

+ [SQL Server 機器學習服務的防火牆設定](../../machine-learning/security/firewall-configuration.md)
+ [啟用其他網路通訊協定](../../database-engine/configure-windows/enable-or-disable-a-server-network-protocol.md)
+ [啟用遠端連線](../../database-engine/configure-windows/configure-the-remote-access-server-configuration-option.md)
+ [為 SQLRUserGroup 建立登入](../../machine-learning/security/create-a-login-for-sqlrusergroup.md)

<a name="bkmk_configureAccounts"></a>
<a name="permissions-external-script"></a>

在資料庫上，您可能需要下列設定更新：

+ [授與使用者 SQL Server 機器學習服務的權限](../../machine-learning/security/user-permission.md)
+ [授與使用者執行特定語言的權限](../../t-sql/statements/create-external-language-transact-sql.md#permissions)

> [!NOTE]
> 是否需要其他設定取決於您的安全性架構、SQL Server 的安裝位置，以及預期使用者如何連線至資料庫並執行外部指令碼。

## <a name="suggested-optimizations"></a>建議的最佳化

在一切都正常運作之後，建議您將伺服器最佳化以支援 Java 語言延伸模組。

### <a name="optimize-the-server-for-java-language-extension"></a>針對 Java 語言延伸模組將伺服器最佳化

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式的預設設定是為了最佳化伺服器的平衡，以處理資料庫引擎支援的各種服務，其中可能包含解壓縮、轉換和載入 (ETL) 程序、報告、稽核，以及使用 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 資料的應用程式。 因此，在預設設定下，您可能會發現語言延伸模組的資源有時會受到限制或遭遇瓶頸，尤其是需要大量記憶體的作業。

為確保優先處理語言延伸模組工作並獲得適當的資源，建議您使用 SQL Server 資源管理員設定外部資源集區。 您也可能想要變更配置給 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 資料庫引擎的記憶體量，或增加 [!INCLUDE[rsql_launchpad](../../includes/rsql-launchpad-md.md)] 服務下所執行的帳戶數目。

+ 若要設定用來管理外部資源的資源集區，請參閱[建立外部資源集區](../../t-sql/statements/create-external-resource-pool-transact-sql.md)。
  
+ 若要變更為資料庫保留的記憶體量，請參閱[伺服器記憶體設定選項](../../database-engine/configure-windows/server-memory-server-configuration-options.md)。
  
如果您使用標準版且沒有「資源管理員」，則可以使用「動態管理檢視 (DMV)」與「擴充事件」，以及 Windows 事件監視，以協助管理伺服器資源。

## <a name="next-steps"></a>後續步驟

Java 開發人員可以從一些簡單的範例開始，並了解 Java 如何搭配 SQL Server 使用的基本概念。 針對下一個步驟，請參閱下列連結：

+ [教學課程：使用 Java 的規則運算式](../tutorials/search-for-string-using-regular-expressions-in-java.md)