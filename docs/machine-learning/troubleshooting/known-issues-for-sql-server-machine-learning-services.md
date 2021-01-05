---
title: Python 和 R 的已知問題
description: 本文描述 Python 和 R 元件的已知問題或限制，這些元件會在 SQL Server 機器學習服務和 SQL Server 2016 R Services 中提供。
ms.prod: sql
ms.technology: machine-learning-services
ms.date: 10/13/2020
ms.topic: troubleshooting
author: dphansen
ms.author: davidph
ms.custom: contperf-fy20q4
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15'
ms.openlocfilehash: 4c4b293b13ec59421ea5827572203ba13e526212
ms.sourcegitcommit: cb8e2ce950d8199470ff1259c9430f0560f0dc1d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2021
ms.locfileid: "97878774"
---
# <a name="known-issues-in-sql-server-machine-learning-services"></a>SQL Server 機器學習服務的已知問題
[!INCLUDE [SQL Server 2016 and later](../../includes/applies-to-version/sqlserver2016.md)]

本文描述 Python 和 R 元件的已知問題或限制，這些元件會在 [SQL Server 機器學習服務](../sql-server-machine-learning-services.md)和 [SQL Server 2016 R Services](../r/sql-server-r-services.md) 中提供。

## <a name="setup-and-configuration-issues"></a>安裝和設定問題

如需初始安裝和設定相關程序的說明，請參閱[安裝 SQL Server 機器學習服務](../install/sql-machine-learning-services-windows-install.md)。 該文章會提供升級、並存安裝，以及安裝新 R 或 Python 元件的相關資訊。

### <a name="1-inconsistent-results-in-mkl-computations-due-to-missing-environment-variable"></a>1.由於遺失環境變數而導致 MKL 計算不一致

**適用範圍：** R_SERVER 二進位檔 9.0、9.1、9.2 或 9.3。

R_SERVER 使用 Intel 數學核心函數庫 (MKL)。 對於涉及 MKL 的計算，如果您的系統遺失環境變數，可能會產生不一致的結果。 

在 R_SERVER 中，設定環境變數 `'MKL_CBWR'=AUTO` 以確保條件式數值重現性。 如需詳細資訊，請參閱[條件式數值重現性 (CNR) 的簡介](https://software.intel.com/articles/introduction-to-the-conditional-numerical-reproducibility-cnr) \(英文\)。

**因應措施**

1. 在 [控制台] 中，按一下 [系統及安全性] > [系統] > [進階系統設定] > [環境變數]。

2. 建立新的使用者或系統變數。 

   + 將變數名稱設定為 'MKL_CBWR'。
   + 將「變數值」設定為 'AUTO'。

3. 重新啟動 R_SERVER。 在 SQL Server 上，您可以重新啟動 SQL Server Launchpad 服務。

> [!NOTE]
> 如果您在 Linux 上執行 SQL Server 2019，請在使用者主目錄中加入 `export MKL_CBWR="AUTO"` 程式碼行以編輯或建立 *.bash_profile*。 在 bash 命令提示字元中輸入 `source .bash_profile` 來執行此檔案。 在 R 命令提示字元中輸入 `Sys.getenv()`，以重新啟動 R_SERVER。

### <a name="2-r-script-runtime-error-sql-server-2017-cu5-cu7-regression"></a>2.R 指令碼執行階段錯誤 (SQL Server 2017 CU5-CU7 迴歸)

針對 SQL Server 2017，在累積更新 5 到 7 中，**rlauncher.config** 檔案內有一個迴歸，其中的暫存目錄檔案路徑包含一個空格。 此迴歸已在 CU8 中更正。

您在執行 R 指令碼時將看到包含下列訊息的錯誤：

> 無法與 'R' 指令碼的執行階段通訊。請檢查 'R' 執行階段的需求。
>
> 來自外部指令碼的 STDERR 訊息: 
>
> 嚴重錯誤: 無法建立 'R_TempDir'

**因應措施**

當 CU8 可供使用時加以套用。 或者，您可以在提升權限的命令提示字元上，搭配 uninstall/install 執行 **registerrext.exe**，重新建立 **rlauncher.config**。 

```cmd
<SQLInstancePath>\R_SERVICES\library\RevoScaleR\rxLibs\x64\RegisterRExt.exe /uninstall /sqlbinnpath:<SQLInstanceBinnPath> /userpoolsize:0 /instance:<SQLInstanceName>

<SQLInstancePath>\R_SERVICES\library\RevoScaleR\rxLibs\x64\RegisterRExt.exe /install /sqlbinnpath:<SQLInstanceBinnPath> /userpoolsize:0 /instance:<SQLInstanceName>
```

下列範例顯示的命令會將預設執行個體 "MSSQL14.MSSQLSERVER" 安裝至 "C:\Program Files\Microsoft SQL Server\"：

```cmd
"C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER\R_SERVICES\library\RevoScaleR\rxLibs\x64\RegisterRext.exe" /uninstall /sqlbinnpath:"C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER\MSSQL\Binn" /userpoolsize:0 /instance:MSSQLSERVER

"C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER\R_SERVICES\library\RevoScaleR\rxLibs\x64\RegisterRext.exe" /install /sqlbinnpath:"C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER\MSSQL\Binn" /userpoolsize:0 /instance:MSSQLSERVER
```

### <a name="3-unable-to-install-sql-server-machine-learning-features-on-a-domain-controller"></a>3.無法在網域控制站上安裝 SQL Server 機器學習功能

如果您嘗試在網域控制站上安裝 SQL Server 2016 R Services 或 SQL Server 機器學習服務，則安裝會失敗並出現下列錯誤：

> 功能的安裝程序期間發生錯誤
>
> 找不到具有身分識別的群組
>
> 元件錯誤碼:0x80131509

發生失敗的原因是，在網域控制站上，服務無法建立執行機器學習所需的 20 個本機帳戶。 通常，不建議您在網域控制站上安裝 SQL Server。 如需詳細資訊，請參閱[支援佈告欄 2032911](https://support.microsoft.com/help/2032911/you-may-encounter-problems-when-installing-sql-server-on-a-domain-cont) \(機器翻譯\)。

### <a name="4-install-the-latest-service-release-to-ensure-compatibility-with-microsoft-r-client"></a>4.安裝最新服務版本以確保與 Microsoft R Client 相容

如果您安裝最新的 Microsoft R Client 版本，並使用它在 SQL Server 上的遠端計算內容中執行 R，則可能發生類似下列的錯誤：

> 您電腦上執行的是 9.x.x 版的 Microsoft R Client，與 Microsoft R Server 8.x.x 版不相容。*請下載並安裝相容版本。*

SQL Server 2016 要求用戶端上的 R 程式庫必須完全符合伺服器上的 R 程式庫。 已針對 R Server 9.0.1 之後的版本移除此限制。 不過，如果您遇到此錯誤，請確認用戶端和伺服器所使用的 R 程式庫版本，並視需要更新用戶端以符合伺服器版本。

隨 SQL Server R Services 一起安裝的 R 版本，會在安裝 SQL Server 服務版本時更新。 為確保一律具有最新版的 R 元件，務必安裝所有 Service Pack。

為確保與 Microsoft R Client 9.0.0 相容，請安裝此[支援文件](https://support.microsoft.com/kb/3210262) \(機器翻譯\) 中所述的更新。

若要避免發生 R 套件的問題，您也可以變更服務合約以使用新式生命週期支援原則，藉以升級伺服器上所安裝的 R 程式庫版本，如[下一節](#bkmk_sqlbindr)所述。 當您執行此操作時，隨 SQL Server 安裝的 R 版本就會依機器學習伺服器 (先前稱為 Microsoft R Server) 更新所使用的相同排程進行更新。

**適用範圍：** 含 R Server 9.0.0 版或更早版本的 SQL Server 2016 R Services

### <a name="5-r-components-missing-from-cu3-setup"></a>5.安裝 CU3 時遺失 R 元件

已佈建有限數量的 Azure 虛擬機器，但沒有應隨附於 SQL Server 的 R 安裝檔案。 此問題適用於從 2018-01-05 到 2018-01-23 期間所佈建的虛擬機器。 如果您已在 2018-01-05 到 2018-01-23 期間套用 SQL Server 2017 的 CU3 更新，此問題可能也會影響內部部署安裝。

已提供包含正確 R 安裝檔案版本的服務版本。

+ [適用於 SQL Server 2017 的累積更新套件 3 KB4052987](https://www.microsoft.com/download/details.aspx?id=56128) \(英文\)。

若要安裝元件並修復 SQL Server 2017 CU3，您必須解除安裝 CU3，然後重新安裝更新的版本：

1. 下載更新的 CU3 安裝檔案，其中包含 R 安裝程式。
2. 解除安裝 CU3。 在 [控制台] 中，搜尋 **解除安裝更新**，然後選取 [適用於 SQL Server 2017 的 Hotfix 3015 (KB4052987) (64 位元)]。 繼續進行解除安裝步驟。
3. 按兩下您剛下載的 KB4052987 更新，藉以重新安裝 CU3 更新：`SQLServer2017-KB4052987-x64.exe`。 遵循安裝指示進行。

### <a name="6-unable-to-install-python-components-in-offline-installations-of-sql-server-2017-ctp-20-or-later"></a>6.無法在 SQL Server 2017 CTP 2.0 或更新版本的離線安裝中安裝 Python 元件

如果您在無法存取網際網路的電腦上安裝 SQL Server 2017 的發行前版本，安裝程式可能無法顯示提示已下載 Python 元件所在位置的頁面。 在這種情況下，您可以安裝機器學習服務功能，但無法安裝 Python 元件。

此問題已在發行版本中修正。 此外，此限制不適用於 R 元件。

**適用範圍：** 搭配 Python 的 SQL Server 2017

### <a name="warning-of-incompatible-version-when-you-connect-to-an-older-version-of-sql-server-r-services-from-a-client-by-using-sssqlv14_md"></a><a name="bkmk_sqlbindr"></a> 當您使用 [!INCLUDE[ssSQLv14_md](../../includes/sssqlv14-md.md)] 以從用戶端連線到舊版 SQL Server R Services 時，會出現版本不相容的警告

當您在 SQL Server 2016 計算內容中執行 R 程式碼時，可能看到下列錯誤：

> *您電腦上執行的是 9.0.0 版的 Microsoft R Client，與 Microsoft R Server 8.0.3 版不相容。請下載並安裝相容版本。*

如果下列兩個敘述中任一個為真，即會顯示此訊息。

+ 您已使用 [!INCLUDE[ssSQLv14_md](../../includes/sssqlv14-md.md)] 的安裝精靈，在用戶端電腦上安裝 R Server (獨立)。
+ 您已使用[單獨的 Windows 安裝程式](/machine-learning-server/install/r-server-install-windows) \(英文\) 來安裝 Microsoft R Server。

為確保伺服器和用戶端會使用相同版本，您可能需要使用「繫結」(支援 Microsoft R Server 9.0 和更新版本)，來升級 SQL Server 2016 執行個體中的 R 元件。 若要判斷您的 R Services 版本是否支援升級，請參閱[使用 SqlBindR.exe 升級 R Services 的執行個體](../install/upgrade-r-and-python.md)。

**適用範圍：** 含 R Server 9.0.0 版或更早版本的 SQL Server 2016 R Services

### <a name="7-setup-for-sql-server-2016-service-releases-might-fail-to-install-newer-versions-of-r-components"></a>7.SQL Server 2016 服務版本的安裝程式可能無法安裝較新版本的 R 元件

當您安裝累積更新，或在未連線到網際網路的電腦上安裝 SQL Server 2016 Service Pack 時，安裝精靈可能無法顯示提示，讓您能夠使用下載的 CAB 檔案來更新 R 元件。 當多個元件和資料庫引擎一起安裝時，通常會發生此失敗。

因應措施是，您可以使用命令列並指定 `MRCACHEDIRECTORY` 引數來安裝服務版本，如此範例所示 (其會安裝 CU1 更新)：

`C:\<path to installation media>\SQLServer2016-KB3164674-x64.exe /Action=Patch /IACCEPTROPENLICENSETERMS /MRCACHEDIRECTORY=<path to CU1 CAB files>`

若要取得最新安裝程式，請參閱[在沒有網際網路存取的情況下安裝機器學習元件](../install/sql-ml-component-install-without-internet-access.md)。

**適用範圍：** 含 R Server 9.0.0 版或更早版本的 SQL Server 2016 R Services

### <a name="8-launchpad-services-fails-to-start-if-the-version-is-different-from-the-r-version"></a>8.如果版本與 R 版本不同，Launchpad 服務就無法啟動

如果您將 SQL Server R Services 與資料庫引擎分開安裝，且組建版本不同，則您可能會在系統事件記錄中看到下列錯誤：

> SQL Server Launchpad 服務無法啟動，因為發生下列錯誤：服務並未以適時的方式回應啟動或控制請求。

例如，如果您使用發行版本安裝資料庫引擎、套用修補程式以升級資料庫引擎，然後使用發行版本新增 R Services 功能，可能會發生此錯誤。

若要避免此問題，請使用檔案管理員之類的公用程式，來比較 Launchpad.exe 版本與 SQL 二進位檔的版本，例如 sqldk.dll。 所有元件都應該具有相同的版本號碼。 如果升級某個元件，所有其他已安裝的元件請務必套用相同的升級。

在執行個體的 `Binn` 資料夾中尋找 Launchpad。 例如，在 SQL Server 2016 的預設安裝中，路徑可能是 `C:\Program Files\Microsoft SQL Server\MSSQL.13.InstanceNameMSSQL\Binn`。 

### <a name="9-remote-compute-contexts-are-blocked-by-a-firewall-in-sql-server-instances-that-are-running-on-azure-virtual-machines"></a>9.遠端計算內容遭到在 Azure 虛擬機器上執行之 SQL Server 執行個體中的防火牆封鎖

如果您在 Azure 虛擬機器上安裝了 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)]，則可能無法使用需要使用虛擬機器工作區的計算內容。 原因在於 Azure 虛擬機器上的防火牆預設包含會封鎖對本機 R 使用者帳戶進行網路存取的規則。

因應措施是，在 Azure VM 上開啟 [具有進階安全性的 Windows 防火牆]、選取 [輸出規則]，然後停用下列規則：**封鎖 SQL Server 執行個體 MSSQLSERVER 中對 R 本機使用者帳戶的網路存取**。 您也可以讓規則保持啟用狀態，但將安全性屬性變更為 [在安全時允許]。

### <a name="10-implied-authentication-in-sqlexpress"></a>10.SQLEXPRESS 的隱含驗證

當您使用整合式 Windows 驗證以從遠端資料科學工作站執行 R 作業時，SQL Server 會使用「隱含驗證」來產生指令碼可能需要的任何本機 ODBC 呼叫。 不過，這項功能在 SQL Server Express Edition 的 RTM 組建中不作用。

若要修正此問題，建議您升級至較新的服務版本。

如果無法升級，則因應措施是，使用 SQL 登入來執行可能需要內嵌 ODBC 呼叫的遠端 R 作業。

**適用範圍：** SQL Server 2016 R Services Express Edition

### <a name="11-performance-limits-when-libraries-used-by-sql-server-are-called-from-other-tools"></a>11.從其他工具中呼叫 SQL Server 所使用的程式庫時的效能限制

它可能會呼叫從外部應用程式 (例如 RGui) 為 SQL Server 安裝的機器學習程式庫。 這麼做可能是完成某些工作最方便的方式，例如，安裝新套件，或在非常短的程式碼範例上執行臨機操作測試。 不過，在 SQL Server 外部，效能可能受到限制。

例如，即使您使用的是 SQL Server Enterprise Edition，當您使用外部工具來執行 R 程式碼時，R 還是會在單一執行緒模式中執行。 若要取得 SQL Server 中效能的優點，請起始 SQL Server 連線，並使用 [sp_execute_external_script](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md) 來呼叫外部指令碼執行階段。

一般來說，避免從外部工具中呼叫 SQL Server 所使用的機器學習程式庫。 如果您需要對 R 或 Python 程式碼進行偵錯，通常可在 SQL Server 外部更輕鬆地執行此操作。 若要取得 SQL Server 中的相同程式庫，您可以安裝 Microsoft R Client 或 [SQL Server 2017 Machine Learning Server (獨立)](../install/sql-machine-learning-standalone-windows-install.md)。

### <a name="12-sql-server-data-tools-does-not-support-permissions-required-by-external-scripts"></a>12.SQL Server Data Tools 不支援外部指令碼所需的權限

當您使用 Visual Studio 或 SQL Server Data Tools 來發佈資料庫專案時，如果有任何主體具備執行外部指令碼的特定權限，您可能會收到類似下列的錯誤：

> *TSQL 模型：對資料庫進行反向工程時偵測到錯誤。權限無法辨識且尚未匯入。*

DACPAC 模型目前不支援 R Services 或機器學習服務所使用的權限，例如，GRANT ANY EXTERNAL SCRIPT 或 EXECUTE ANY EXTERNAL SCRIPT。 未來版本會修正此問題。

因應措施是，在部署後指令碼中執行其他 GRANT 陳述式。

### <a name="13-external-script-execution-is-throttled-due-to-resource-governance-default-values"></a>13.執行外部指令碼會因為資源管理預設值而受到節流處理

在 Enterprise Edition 中，您可以使用資源集區來管理外部指令碼程序。 在某些早期發行的組建中，可配置到 R 處理序的記憶體上限為 20%。 因此，如果伺服器有 32 GB 的 RAM，R 可執行檔 (RTerm.exe 和 BxlServer.exe) 可在單一要求中使用的上限為 6.4 GB。

如果您遇到資源限制，請檢查目前的預設值。 如果 20% 不夠用，請參閱 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的文件，以了解如何變更此值。

**適用範圍：** SQL Server 2016 R Services Enterprise Edition

### <a name="14-error-when-using-sp_execute_external_script-without-libcso-on-linux"></a>14.在 Linux 上使用不含 `libc++.so` 的 `sp_execute_external_script` 時發生錯誤

在未安裝 `libc++.so` 的全新 Linux 電腦上，使用 Java 或外部語言執行 `sp_execute_external_script` (SPEES) 查詢會因為 `commonlauncher.so` 無法載入 `libc++.so` 而失敗。

例如：

```sql
EXECUTE sp_execute_external_script @language = N'Java'
    , @script = N'JavaTestPackage.PassThrough'
    , @parallel = 0
    , @input_data_1 = N'select 1'
WITH RESULT SETS((col1 INT NOT NULL))
GO
```

這會失敗並出現類似下列的訊息：

```text
Msg 39012, Level 16, State 14, Line 0

Unable to communicate with the runtime for 'Java' script for request id: 94257840-1704-45E8-83D2-2F74AEB46CF7. Please check the requirements of 'Java' runtime.
```

`mssql-launchpadd` 記錄將顯示類似下列的錯誤訊息：

```text
Oct 18 14:03:21 sqlextmls launchpadd[57471]: [launchpad] 2019/10/18 14:03:21 WARNING: PopulateLauncher failed: Library /opt/mssql-extensibility/lib/commonlauncher.so not loaded. Error: libc++.so.1: cannot open shared object file: No such file or directory
```

**因應措施**

您可以執行下列其中一項因應措施：

1. 將 `libc++*` 從 `/opt/mssql/lib` 複製到預設系統路徑 `/lib64`

1. 將下列項目新增至 `/var/opt/mssql/mssql.conf` 以公開路徑：

   ```text
   [extensibility]
   readabledirectories = /opt/mssql
   ```

**適用範圍：** Linux 上的 SQL Server 2019

### <a name="15-installation-or-upgrade-error-on-fips-enabled-servers"></a>15.啟用 FIPS 的伺服器上具有安裝或升級錯誤

如果在啟用 [聯邦資訊處理標準 (FIPS)](/windows/security/threat-protection/security-policy-settings/system-cryptography-use-fips-compliant-algorithms-for-encryption-hashing-and-signing) 的伺服器上安裝 SQL Server 2019 與 **機器學習服務和語言延伸模組** 功能，或升級 SQL Server 執行個體，將會收到下列錯誤：

> 安裝擴充性功能時發生錯誤，錯誤訊息如下：AppContainer 建立失敗，錯誤訊息為「無」，表示這個實作不是 Windows Platform FIPS 驗證加密演算法的一部分。

**因應措施**

在安裝 SQL Server 2019 與 **機器學習服務和語言延伸模組** 功能或升級 SQL Server 執行個體之前，請先停用 FIPS。 安裝或升級完成後，即可重新啟用 FIPS。

**適用範圍：** SQL Server 2019

## <a name="r-script-execution-issues"></a>R 指令碼執行問題

本節包含在 SQL Server 上執行 R 的特定已知問題，以及一些與 Microsoft 所發佈之 R 程式庫和工具 (包括 RevoScaleR) 相關的問題。

如需可能影響 R 解決方案的其他已知問題，請參閱 [Machine Learning Server](/machine-learning-server/resources-known-issues) \(英文\) 網站。

### <a name="1-access-denied-warning-when-executing-r-scripts-on-sql-server-in-a-non-default-location"></a>1.在 SQL Server 上的非預設位置中執行 R 指令碼時，發生拒絕存取的警告

如果已將 SQL Server 的執行個體安裝到非預設位置 (例如，在 `Program Files` 資料夾外部)，則當您嘗試執行安裝套件的指令碼時，就會引發警告 ACCESS_DENIED。 例如：

> 在 `normalizePath(path.expand(path), winslash, mustWork)` 中 : path[2]="~ExternalLibraries/R/8/1":存取遭到拒絕

原因是 R 函數嘗試讀取路徑，如果內建的使用者群組 **SQLRUserGroup** 沒有讀取權限就會失敗。 引發的警告不會封鎖目前 R 指令碼的執行，但是，每當使用者執行任何其他 R 指令碼時，警告可能會重複發生。

如果您已將 SQL Server 安裝到預設位置，則不會發生此錯誤，因為所有 Windows 使用者都具備 `Program Files` 資料夾的讀取權限。

此問題已在即將推出的服務版本中解決。 因應措施是，為群組 **SQLRUserGroup** 提供 `ExternalLibraries` 所有父資料夾的讀取權限。

### <a name="2-serialization-error-between-old-and-new-versions-of-revoscaler"></a>2.舊版和新版 RevoScaleR 之間發生序列化錯誤

當您使用序列化格式來將模型傳遞到遠端 SQL Server 執行個體時，可能會收到錯誤： 

> 錯誤發生於 memDecompress(data, type = decompress) 內部錯誤 - memDecompress(2) 中的 3。

如果您使用最新版的序列化函數 ([rxSerializeModel](/machine-learning-server/r-reference/revoscaler/rxserializemodel) \(英文\)) 來儲存模型，但您將模型還原序列化所在的 SQL Server 執行個體具有舊版 RevoScaleR API (來自 SQL Server 2017 CU2 或更早版本)，則會引發此錯誤。

因應措施是，您可以將 SQL Server 2017 執行個體升級為 CU3 或更新版本。

如果 API 版本相同，或者，如果您要將使用較舊的序列化函數所儲存的模型移至使用較新版序列化 API 的伺服器，則不會出現此錯誤。

換句話說，針對序列化和還原序列化作業，使用相同版本的 RevoScaleR。

### <a name="3-real-time-scoring-does-not-correctly-handle-the-_learningrate_-parameter-in-tree-and-forest-models"></a>3.即時評分不會正確處理樹狀結構和樹系模型中的 _learningRate_ 參數

如果您使用決策樹或決策樹系方法建立模型，並指定學習速度，則相較於使用 `rxPredict`，在使用 `sp_rxpredict` 或 SQL `PREDICT` 函數時，您可能會看到不一致的結果。

原因在於 API 中所發生的錯誤，此 API 會處理序列化的模型並受限於 `learningRate` 參數：例如，在 [rxBTrees](/machine-learning-server/r-reference/revoscaler/rxbtrees) \(英文\) 中，或

此問題已在即將推出的服務版本中解決。

### <a name="4-limitations-on-processor-affinity-for-r-jobs"></a>4.R 作業處理器相似性的限制

在初始發行的 SQL Server 2016 組建中，您只能為第一個 k 群組中的 CPU 設定處理器親和性。 例如，如果伺服器是含有兩個 k 群組的雙插槽機器，R 處理序只會使用第一個 k 群組中的處理器。 設定 R 指令碼作業的資源管理時，適用相同的限制。

SQL Server 2016 Service Pack 1 已修正這個問題。 我們建議您升級至最新服務版本。

**適用範圍：** SQL Server 2016 R Services RTM 版本

### <a name="5-changes-to-column-types-cannot-be-performed-when-reading-data-in-a-sql-server-compute-context"></a>5.讀取 SQL Server 計算內容中的資料，無法變更資料行類型。

如果您的計算內容設為 SQL Server 執行個體，則您無法使用 _colClasses_ 引數 (或其他類似的引數) 來變更 R 程式碼中的資料行資料類型。

例如，如果資料行 CRSDepTimeStr 還不是整數，則下列陳述式會產生錯誤︰

```R
data <- RxSqlServerData(
  sqlQuery = "SELECT CRSDepTimeStr, ArrDelay  FROM AirlineDemoSmall", 
  connectionString = connectionString, 
  colClasses = c(CRSDepTimeStr = "integer"))
```

因應措施是，您可以重新撰寫 SQL 查詢以使用 CAST 或 CONVERT，並使用正確的資料類型來向 R 呈現資料。 通常，效能最佳的時機是使用 SQL 來處理資料，而不是在 R 程式碼中變更資料。

**適用範圍：** SQL Server 2016 R Services

### <a name="6-limits-on-size-of-serialized-models"></a>6.序列化模型的大小限制

當您將模型儲存至 SQL Server 資料表時，必須將模型序列化，並以二進位格式儲存它。 理論上，可以使用此方法儲存的模型大小上限為 2 GB，這是 SQL Server 中 varbinary 資料行的大小上限。

如果您需要使用較大的模型，可以使用下列因應措施：

+ 採取步驟來縮減模型大小。 某些開放原始碼 R 套件會在模型物件中包含大量資訊，而此資訊大部分都可移除以進行部署。 
+ 使用特徵選取來移除不必要的資料行。
+ 如果您使用的是開放原始碼演算法，請考慮使用 MicrosoftML 或 RevoScaleR 中對應的演算法來執行類似的實作。 這些套件已針對部署案例進行最佳化。
+ 將模型合理化並使用上述步驟縮減大小之後，請查看是否可以使用基底 R 中的 [memCompress](https://www.rdocumentation.org/packages/base/versions/3.4.1/topics/memCompress) \(英文\) 函數來縮減模型大小，然後將它傳遞至 SQL Server。 當模型接近 2 GB 限制時，最適合使用此選項。
+ 對於較大的模型，您可以使用 SQL Server [FileTable](../../relational-databases/blob/filetables-sql-server.md) 功能來儲存模型，而不是使用 varbinary 資料行。

    若要使用 Filetable，您必須新增防火牆例外狀況，因為儲存於 Filetable 的資料會由 SQL Server 中的 Filestream 檔案系統驅動程式來管理，而預設防火牆規則會封鎖網路檔案存取。 如需詳細資訊，請參閱[啟用 FileTable 的必要條件](../../relational-databases/blob/enable-the-prerequisites-for-filetable.md)。

    啟用 FileTable 之後，若要撰寫模型，您可以使用 FileTable API 來取得 SQL 的路徑，然後從您的程式碼將模型寫入至該位置。 當您需要讀取模型時，您會取得 SQL 的路徑，然後從指令碼中使用該路徑來呼叫模型。 如需詳細資訊，請參閱[使用檔案輸入輸出 API 存取 FileTable](../../relational-databases/blob/access-filetables-with-file-input-output-apis.md)。

### <a name="7-avoid-clearing-workspaces-when-you-execute-r-code-in-a-ssnoversion-compute-context"></a>7.避免在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 計算內容中執行 R 程式碼時清除工作區

如果在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 計算內容中執行 R 程式碼時，使用 R 命令來清除物件的工作區，或者，如果將工作區當成使用 [sp_execute_external_script](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md) 呼叫的 R 指令碼一部分來清除，您可能會發生此錯誤︰找不到工作區物件 'revoScriptConnection'。

`revoScriptConnection` 是 R 工作區的物件，包含從 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]呼叫之 R 工作階段的相關資訊。 不過，如果 R 程式碼包含清除工作區的命令，例如 `rm(list=ls()))`，則也會清除工作階段所有資訊和 R 工作區的其他物件。

因應措施是，避免在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中執行 R 時任意清除變數和其他物件。 雖然在 R 主控台內工作時清除工作區很常見，但它可能造成意想不到的結果。

+ 若要刪除特定變數，請使用 R `remove` 函數：例如 `remove('name1', 'name2', ...)`。
+ 如有多個變數要刪除，請將暫存變數名稱儲存至清單，並執行定期記憶體回收。

### <a name="8-restrictions-on-data-that-can-be-provided-as-input-to-an-r-script"></a>8.可作為輸出提供給 R 指令碼的資料限制

您無法在 R 指令碼中使用下列類型的查詢結果：

- 來自 [!INCLUDE[tsql](../../includes/tsql-md.md)] 查詢 (參考永遠加密資料行) 的資料。
  
- 來自 [!INCLUDE[tsql](../../includes/tsql-md.md)] 查詢 (參考遮罩資料行) 的資料。
  
     若您需要在 R 指令碼中使用遮罩的資料，一項可行的因應措施是在暫存資料表中製作資料複本，並改用該資料。

### <a name="9-use-of-strings-as-factors-can-lead-to-performance-degradation"></a>9.使用字串作為因素可能導致效能降低

使用字串類型變數作為因素，可能會大幅增加 R 作業所使用的記憶體數量。 這通常是 R 的已知問題，而且有很多關於該主題的文章。 例如，請參閱[因素不是 R 中的一等公民，作者：R 部落客 John Mount](https://www.r-bloggers.com/factors-are-not-first-class-citizens-in-r/) \(英文\) 或 [stringsAsFactors：未經授權的興衰史，作者：Roger Peng](https://simplystatistics.org/2015/07/24/stringsasfactors-an-unauthorized-biography/) \(英文\)。 

儘管此問題不是 SQL Server 特有的，但它可能會大幅影響 SQL Server 中執行 R 程式碼的效能。 字串通常會儲存為 varchar 或 nvarchar，而且如果字串資料的資料行有許多唯一值，則在內部將這些字串轉換為整數，並由 R 傳回到字串的流程甚至可能導致記憶體配置錯誤。

如果您不一定需要字串資料類型來進行其他作業，在資料準備過程中，將字串值對應到數值 (整數) 資料類型，從效能和規模觀點來看是有益的。

如需此問題的討論和其他秘訣，請參閱 [R Services 的效能 - 資料最佳化](../r/r-and-data-optimization-r-services.md)。

### <a name="10-arguments-varstokeep-and-varstodrop-are-not-supported-for-sql-server-data-sources"></a>10.SQL Server 資料來源不支援 *varsToKeep* 和 *varsToDrop* 引數

當您使用 rxDataStep 函數來將結果寫入至資料表時，使用 *varsToKeep* 和 *varsToDrop* 是指定要包含為作業一部分或或排除在外之資料行的便捷方式。 不過，SQL Server 資料來源不支援這些引數。

### <a name="11-limited-support-for-sql-data-types-in-sp_execute_external_script"></a>11.sp\_execute\_external\_script 中對 SQL 資料類型的支援有限

並非 SQL 支援的所有資料類型都能在 R 中使用。因應措施是，考慮先將不支援的資料類型轉換成支援的資料類型，然後將資料傳遞至 sp\_execute\_external\_script。

如需詳細資訊，請參閱 [R 程式庫和資料類型](../r/r-libraries-and-data-types.md)。

### <a name="12-possible-string-corruption-using-unicode-strings-in-varchar-columns"></a>12.在 varchar 資料行中使用 unicode 字串可能損毀字串

將 varchar 資料行中的 unicode 資料從 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 傳遞至 R/Python，可能導致字串損毀。 這是因為這些 unicode 字串在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 定序中的編碼，可能不符合 R/Python 中所使用的預設 UTF-8 編碼。 

若要將任何非 ASCII 字串資料從 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 傳送至 R/Python，請使用 UTF-8 編碼 (可在 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 中使用)，或針對相同資料使用 nvarchar 類型。

### <a name="13-only-one-value-of-type-raw-can-be-returned-from-sp_execute_external_script"></a>13.只能從 `sp_execute_external_script` 傳回一個 `raw` 類型的值

當 R 傳回二進位資料類型 (R **raw** 資料類型) 時，必須在輸出資料框架中傳送此值。

利用 **raw** 以外的資料類型，加上 OUTPUT 關鍵字，就能將參數值和預存程序的結果一起傳回。 如需詳細資訊，請參閱[參數](../../relational-databases/stored-procedures/parameters.md)。

如果您想要使用包含類型為 **raw** 之值的多個輸出集，可行的因應措施之一是對預存程序進行多次呼叫，或者，使用 ODBC 來將結果集傳回到 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]。

### <a name="14-loss-of-precision"></a>14.遺失有效位數

由於 [!INCLUDE[tsql](../../includes/tsql-md.md)] 和 R 均支援各種資料類型，因此，數值資料類型可能會在轉換期間遺失精確度。

如需有關隱含資料類型轉換的詳細資訊，請參閱 [R 程式庫和資料類型](../r/r-libraries-and-data-types.md)。

### <a name="15-variable-scoping-error-when-you-use-the-transformfunc-parameter"></a>15.當您使用 transformFunc 參數時發生變數範圍錯誤

若要在進行模型化時轉換資料，您可以在 `rxLinmod` 或 `rxLogit` 等函數中傳遞 *transformFunc* 引數。 不過，巢狀函數呼叫可能會在 SQL Server 計算內容中導致範圍設定錯誤，即使呼叫可在本機計算內容中正常運作也一樣。

> 適用於分析的範例資料集沒有任何變數

例如，假設您已在本機全域環境中定義兩個函數 `f` 和 `g`，而 `g` 會呼叫 `f`。 在涉及 `g` 的分散式或遠端呼叫中，即使您已將 `f` 和 `g` 都傳遞至遠端呼叫，對 `g` 的呼叫也可能會因為找不到 `f` 而失敗並產生此錯誤。

若遇到此問題，您可以在 `f` 定義內嵌 `g`的定義以解決此問題， `g` 前的任何位置則會正常呼叫 `f`。

例如：

```R
f <- function(x) { 2*x * 3 }
g <- function(y) {
              a <- 10 * y
               f(a)
}
```

若要避免此錯誤，請重寫定義，如下所示：

```R
g <- function(y){
              f <- function(x) { 2*x +3}
              a <- 10 * y
              f(a)
}
```

### <a name="16-data-import-and-manipulation-using-revoscaler"></a>16.使用 RevoScaleR 匯入及操作資料

從資料庫讀取 **varchar** 資料行時，會修剪空白字元。 若要避免此情況，請在字串頭尾使用非空白字元。

使用 `rxDataStep` 等函數來建立包含 **varchar** 資料行的資料庫資料表時，會依據資料範例來估計資料行寬度。 如果寬度各有不同，則可能需要將所有字串填補到常見長度。

使用 `rxImport` 或 `rxTextToXdf` 的重複呼叫來匯入或附加資料列，將多個輸入檔案結合成單一 .xdf 檔案時，不支援使用轉換來變更變數的資料類型。

### <a name="17-limited-support-for-rxexec"></a>17.對 rxExec 的支援有限

在 SQL Server 2016 中，RevoScaleR 套件所提供的 `rxExec` 函數只能在單一執行緒模式中使用。

### <a name="18-increase-the-maximum-parameter-size-to-support-rxgetvarinfo"></a>18.提高參數大小上限以支援 rxGetVarInfo

如果您使用變數數目極大 (例如 40,000 個以上) 的資料集，就必須在啟動 R 時設定 `max-ppsize` 旗標，才能使用 `rxGetVarInfo` 等函數。 `max-ppsize` 旗標會指定指標保護堆疊的大小上限。

如果您使用 R 主控台 (例如，RGui.exe 或 RTerm.exe)，則可輸入下列內容來將 _max-ppsize_ 的值設為 500000：

```R
R --max-ppsize=500000
```

### <a name="19-issues-with-the-rxdtree-function"></a>19.rxDTree 函數的問題

`rxDTree` 函數目前不支援公式內的轉換。 尤其不支援使用 `F()` 語法即時建立因數。 不過，會將數值資料自動量化。

要求的因數會被視為與所有 RevoScaleR 分析函數中的因數相同，除了 `rxDTree`以外。

### <a name="20-datatable-as-an-outputdataset-in-r"></a>20.使用 Data.table 作為 R 中的 OutputDataSet

在 SQL Server 2017 累積更新 13 (CU13) 和更早版本中，不支援使用 `data.table` 作為 R 中的 `OutputDataSet`。 可能出現以下訊息：

``` text
Msg 39004, Level 16, State 20, Line 2
A 'R' script error occurred during execution of 
'sp_execute_external_script' with HRESULT 0x80004004.
Msg 39019, Level 16, State 2, Line 2
An external script error occurred: 
Error in alloc.col(newx) : 
  Internal error: length of names (0) is not length of dt (11)
Calls: data.frame ... as.data.frame -> as.data.frame.data.table -> copy -> alloc.col

Error in execution.  Check the output for more information.
Error in eval(expr, envir, enclos) : 
  Error in execution.  Check the output for more information.
Calls: source -> withVisible -> eval -> eval -> .Call
Execution halted
```

在 SQL Server 2017 累積更新 14 (CU14) 和更新版本中，支援使用 `data.table` 作為 R 中的 `OutputDataSet`。

### <a name="21-running-a-long-script-fails-while-installing-a-library"></a>21.安裝程式庫時執行長指令碼失敗

執行長時間執行的外部指令碼工作階段，並讓 dbo 平行嘗試在不同資料庫上安裝程式庫，即可終止指令碼。

例如，針對 master 執行此外部指令碼：

```sql
USE MASTER
DECLARE @language nvarchar(1) = N'R'
DECLARE @script nvarchar(max) = N'Sys.sleep(100)'
DECLARE @input_data_1 nvarchar(max) = N'select 1'
EXEC sp_execute_external_script @language = @language, @script = @script, @input_data_1 = @input_data_1 with result sets none
go
```

雖然 dbo 會在 LibraryManagementFunctional 中平行安裝程式庫：

```sql
USE [LibraryManagementFunctional]
go

CREATE EXTERNAL LIBRARY [RODBC] FROM (CONTENT = N'/home/ani/var/opt/mssql/data/RODBC_1.3-16.tar.gz') WITH (LANGUAGE = 'R')
go

DECLARE @language nvarchar(1) = N'R'
DECLARE @script nvarchar(14) = N'library(RODBC)'
DECLARE @input_data_1 nvarchar(8) = N'select 1'
EXEC sp_execute_external_script @language = @language, @script = @script, @input_data_1 = @input_data_1
go
```

先前針對 master 長時間執行的外部指令碼將會終止，並出現下列錯誤訊息：

> 執行 'sp_execute_external_script' 時發生 'R' 指令碼錯誤，HRESULT 為 0x800704d4。

**因應措施**

不要與長時間執行的查詢平行執行程式庫安裝。 或者，在安裝完成之後，重新執行長時間執行的查詢。

**適用範圍：** 僅限 Linux 上的 SQL Server 2019 與巨量資料叢集。

### <a name="22-sql-server-stops-responding-when-executing-r-scripts-containing-parallel-execution"></a>22.SQL Server 在執行包含平行執行的 R 指令碼時停止回應

SQL Server 2019 包含會影響使用平行執行之 R 指令碼的迴歸。 範例包括搭配 `RxLocalPar` 計算內容和使用平行套件的指令碼，來使用 `rxExec`。 這個問題是因為在 SQL Server 中執行時，平行套件在寫入 Null 裝置時遇到的錯誤所造成。

**適用範圍：** SQL Server 2019。

### <a name="23-precision-loss-for-moneynumericdecimalbigint-data-types"></a>23.money/numeric/decimal/bigint 資料類型的精確度遺失

使用 `sp_execute_external_script` 執行 R 指令碼，允許使用 money、numeric、decimal 和 bigint 資料類型作為輸入資料。 不過，由於這些資料類型會轉換成 R 的數值類型，因此高度值或具有小數點的值會失去精確度。

+ **Money**：有時候，美分值不精確，且會發出警告：警告：無法精確地表示美分值。  
+ **Numeric/Decimal**：搭配 R 指令碼的 `sp_execute_external_script` 不支援這些資料類型的完整範圍，且會改變最後幾個小數位數，特別是分數的小數位數。
+ **Bigint**：R 最多只支援 53 位元整數，之後即會開始失去精確度。

## <a name="python-script-execution-issues"></a>Python 指令碼執行問題

本節包含在 SQL Server 上執行 Python 的特定已知問題，以及與 Microsoft 所發佈之 Python 套件相關的問題，包括 [revoscalepy](/r-server/python-reference/revoscalepy/revoscalepy-package) \(英文\) 和 [microsoftml](/r-server/python-reference/microsoftml/microsoftml-package) \(英文\)。

### <a name="1-call-to-pretrained-model-fails-if-path-to-model-is-too-long"></a>1.如果模型的路徑太長，呼叫預先定型的模型即會失敗

如果您在 SQL Server 2017 的早期版本中安裝預先定型的模型，則已定型之模型檔案的完整路徑可能太長，以致於 Python 無法讀取。 此限制已在較新的服務版本中修正。

有數個可能的因應措施：

+ 當您安裝預先定型的模型時，請選擇自訂位置。
+ 如果可能，在含有較短路徑的自訂安裝路徑 (例如 C:\SQL\MSSQL14.MSSQLSERVER) 底下，安裝 SQL Server 執行個體。
+ 使用 Windows 公用程式 [Fsutil](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/cc788097(v=ws.11)) \(英文\)，來建立將模型檔案對應至較短路徑的永久連結。
+ 更新至最新服務版本。

### <a name="2-error-when-saving-serialized-model-to-sql-server"></a>2.將序列化模型儲存至 SQL Server 時發生錯誤

當您將模型傳遞至遠端 SQL Server 執行個體，並嘗試使用 [revoscalepy](/machine-learning-server/python-reference/revoscalepy/revoscalepy-package) \(英文\) 中的 `rx_unserialize` 函數來讀取二進位模型時，您可能收到下列錯誤： 

> NameError: 尚未定義名稱 'rx_unserialize_model'

如果您使用最新版的序列化函數來儲存模型，但您將模型還原序列化所在的 SQL Server 執行個體無法辨識序列化 API，則會引發此錯誤。

若要解決問題，請將 SQL Server 2017 執行個體升級為 CU3 或更新版本。

### <a name="3-failure-to-initialize-a-varbinary-variable-causes-an-error-in-bxlserver"></a>3.無法將 varbinary 變數初始化，會在 BxlServer 中導致錯誤

如果您使用 `sp_execute_external_script` 在 SQL Server 中執行 Python 程式碼，而且程式碼具有類型 varbinary(max)、varchar(max) 或類似類型的輸出變數，則變數必須初始化或設定為指令碼的一部分。 否則，資料交換元件 BxlServer 會引發錯誤並停止運作。

此限制將會在即將推出的服務版本中修正。 因應措施是，確定已在 Python 指令碼內將變數初始化。 您可以使用任何有效的值，如下列範例所示：

```sql
declare @b varbinary(max);
exec sp_execute_external_script
  @language = N'Python'
  , @script = N'b = 0x0'
  , @params = N'@b varbinary(max) OUTPUT'
  , @b = @b OUTPUT;
go
```

```sql
declare @b varchar(30);
exec sp_execute_external_script
  @language = N'Python'
  , @script = N' b = ""  '
  , @params = N'@b varchar(30) OUTPUT'
  , @b = @b OUTPUT;
go
```

### <a name="4-telemetry-warning-on-successful-execution-of-python-code"></a>4.成功執行 Python 程式碼時的遙測警告

從 SQL Server 2017 CU2 開始，即使 Python 程式碼執行成功，也可能出現下列訊息：

> 來自外部指令碼的 *STDERR* 訊息:
>  *~PYTHON_SERVICES\lib\site-packages\revoscalepy\utils\RxTelemetryLogger*
> *SyntaxWarning: telemetry_state* 會在全域宣告之前使用

此問題已在 SQL Server 2017 累積更新 3 (CU3) 中修正。 

### <a name="5-numeric-decimal-and-money-data-types-not-supported"></a>5.不支援數值、小數和貨幣資料類型

從 SQL Server 2017 累積更新 12 (CU12) 開始，在搭配使用 Python 與 `sp_execute_external_script` 時，不支援 WITH RESULT SETS 中的 numeric、decimal 及 money 資料類型。 可能出現以下訊息：

> *[代碼:39004，SQL 狀態:S1000] 執行 'sp_execute_external_script' 時發生 'Python' 指令碼錯誤，HRESULT 為 0x80004004。*
>
> *[代碼:39019，SQL 狀態:S1000] 發生外部指令碼錯誤:*
>
> *SqlSatelliteCall 錯誤:輸出結構描述中不支援的類型。支援的類型: bit、smallint、int、datetime、smallmoney、real 及 float。char、varchar 是部分支援的。*

此問題已在 SQL Server 2017 累積更新 14 (CU14) 中修正。

### <a name="6-bad-interpreter-error-when-installing-python-packages-with-pip-on-linux"></a>6.在 Linux 上使用 pip 安裝 Python 套件時，發生錯誤的解譯器錯誤 

在 SQL Server 2019 上，如果您嘗試使用 **pip**。 例如：

```bash
/opt/mssql/mlservices/runtime/python/bin/pip -h
```

接著，您將收到下列錯誤：

> *bash: /opt/mssql/mlservices/runtime/python/bin/pip: /opt/microsoft/mlserver/9.4.7/bin/python/python: 錯誤的解譯器:無此檔案或目錄*

**因應措施**

從 [Python 套件授權單位 (PyPA)](https://www.pypa.io) \(英文\) 安裝 **pip**：

```bash
wget 'https://bootstrap.pypa.io/get-pip.py' 
/opt/mssql/mlservices/bin/python/python ./get-pip.py 
```

**建議**

請參閱[使用 sqlmlutils 安裝 Python 套件](../package-management/install-additional-python-packages-on-sql-server.md)。

**適用範圍：** Linux 上的 SQL Server 2019

### <a name="7-unable-to-install-python-packages-using-pip-after-installing-sql-server-2019-on-windows"></a>7.在 Windows 上安裝 SQL Server 2019 之後，無法使用 pip 安裝 Python 套件

在 Windows 上安裝 SQL Server 2019 之後，嘗試從 DOS 命令列透過 **pip** 安裝 Python 套件將會失敗。 例如：

```bash
pip install quantfolio
```

這將傳回下列錯誤訊息：

> 使用需要 TLS/SSL 的位置設定了 pip，但是無法使用 Python 中的 SSL 模組。

這是 Anaconda 套件特有的問題。 它將會在即將推出的服務版本中修正。

**因應措施**

複製下列檔案：

+ `libssl-1_1-x64.dll`
+ `libcrypto-1_1-x64.dll`

從下列資料夾 <br>
`C:\Program Files\Microsoft SQL Server\MSSSQL15.MSSQLSERVER\PYTHON_SERVICES\Library\bin`

到下列資料夾 <br>
`C:\Program Files\Microsoft SQL Server\MSSSQL15.MSSQLSERVER\PYTHON_SERVICES\DLLs`

然後開啟新的 DOS 命令殼層提示字元。

**適用範圍：** Windows 上的 SQL Server 2019

### <a name="8-error-when-using-sp_execute_external_script-without-libcaboso-on-linux"></a>8.在 Linux 上使用不含 `libc++abo.so` 的 `sp_execute_external_script` 時發生錯誤

在未安裝 `libc++abi.so` 的全新 Linux 電腦上，執行 `sp_execute_external_script` (SPEES) 查詢會失敗，並出現「無此檔案或目錄」錯誤。

例如：

```text
EXEC sp_execute_external_script
    @language = N'Python'
    , @script = N'
OutputDataSet = InputDataSet'
    , @input_data_1 = N'select 1'
    , @input_data_1_name = N'InputDataSet'
    , @output_data_1_name = N'OutputDataSet'
    WITH RESULT SETS (([output] int not null));
Msg 39012, Level 16, State 14, Line 0
Unable to communicate with the runtime for 'Python' script for request id: 94257840-1704-45E8-83D2-2F74AEB46CF7. Please check the requirements of 'Python' runtime.
STDERR message(s) from external script:

Failed to load library /opt/mssql-extensibility/lib/sqlsatellite.so with error libc++abi.so.1: cannot open shared object file: No such file or directory.

SqlSatelliteCall error: Failed to load library /opt/mssql-extensibility/lib/sqlsatellite.so with error libc++abi.so.1: cannot open shared object file: No such file or directory.
STDOUT message(s) from external script:
SqlSatelliteCall function failed. Please see the console output for more information.
Traceback (most recent call last):
  File "/opt/mssql/mlservices/libraries/PythonServer/revoscalepy/computecontext/RxInSqlServer.py", line 605, in rx_sql_satellite_call
    rx_native_call("SqlSatelliteCall", params)
  File "/opt/mssql/mlservices/libraries/PythonServer/revoscalepy/RxSerializable.py", line 375, in rx_native_call
    ret = px_call(functionname, params)
RuntimeError: revoscalepy function failed.
Total execution time: 00:01:00.387
```

**因應措施**

執行以下命令：

```bash
sudo cp /opt/mssql/lib/libc++abi.so.1 /opt/mssql-extensibility/lib/
```

**適用範圍：** Linux 上的 SQL Server 2019

### <a name="9-cannot-install-tensorflow-package-using-sqlmlutils"></a>9.無法使用 **sqlmlutils** 來安裝 **tensorflow** 套件

[sqlmlutils 套件](../package-management/install-additional-python-packages-on-sql-server.md)是用來在 SQL Server 2019 中安裝 Python 套件。 您必須下載、安裝及更新 [Microsoft Visual C++ 2015-2019 可轉散發套件 (x64)](https://visualstudio.microsoft.com/downloads/)。 不過，您無法使用 sqlmlutils 來安裝 **tensorflow** 套件。 Tensorflow 套件相依於較新版本的 numpy，而不是安裝在 SQL Server 中的版本。 不過，numpy 是預先安裝的系統套件，當嘗試安裝 tensorflow 時，sqlmlutils 無法更新該套件。

**因應措施**

在系統管理員模式中使用命令提示字元，執行下列命令，並以 SQL 實例名稱取代 "MSSQLSERVER"：

   ```cmd
   "C:\Program Files\Microsoft SQL Server\MSSQL15.MSSQLSERVER\PYTHON_SERVICES\python.exe" -m pip install --upgrade tensorflow
   ```

   如果收到 "TLS/SSL" 錯誤，請參閱本文稍早的 [7.無法使用 pip 安裝 Python 套件](#7-unable-to-install-python-packages-using-pip-after-installing-sql-server-2019-on-windows)。

**適用範圍：** Windows 上的 SQL Server 2019

## <a name="revolution-r-enterprise-and-microsoft-r-open"></a>Revolution R Enterprise 和 Microsoft R Open

本節列出 Revolution Analytics 提供的 R 連線能力、開發及效能工具的特定問題。 舊版的 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 發行前版本中均會提供這些工具。

通常建議您解除安裝這些舊版本，並安裝最新版的 SQL Server 或 Microsoft R Server。

### <a name="1-revolution-r-enterprise-is-not-supported"></a>1.不支援 Revolution R Enterprise

不支援與任何 [!INCLUDE[rsql_productname_md](../../includes/rsql-productname-md.md)] 版本並存安裝 Revolution R Enterprise。

如果您目前具有 Revolution R Enterprise 的授權，則必須將其放在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體及任何要用來連線到 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體之工作站以外的電腦上。

某些 [!INCLUDE[rsql_productname](../../includes/rsql-productname-md.md)] 發行前版本會包含 Revolution Analytics 所建立的 Windows R 開發環境。 此工具已不再提供且不受支援。

為了與 [!INCLUDE[rsql_productname](../../includes/rsql-productname-md.md)] 相容，建議您改為安裝 Microsoft R Client。 [Visual Studio R 工具](https://marketplace.visualstudio.com/items?itemName=MikhailArkhipov007.RTVS2019)和 [Visual Studio Code](https://code.visualstudio.com/) \(英文\) 也支援 Microsoft R 解決方案。

### <a name="2-compatibility-issues-with-sqlite-odbc-driver-and-revoscaler"></a>2.SQLite ODBC 驅動程式與 RevoScaleR 的相容性問題

SQLite ODBC 驅動程式修訂版 0.92 與 RevoScaleR 不相容。 修訂版 0.88-0.91 及 0.93 和更新版本已知是相容的。

## <a name="next-steps"></a>後續步驟

[在 SQL Server 中對機器學習進行疑難排解](machine-learning-troubleshooting-overview.md)