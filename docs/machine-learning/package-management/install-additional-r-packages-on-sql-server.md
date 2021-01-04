---
title: 使用 sqlmlutils 安裝 R 套件
description: 了解如何使用 sqlmlutils，在 SQL Server 機器學習服務執行個體上安裝新的 R 套件。
ms.prod: sql
ms.technology: machine-learning
ms.date: 12/15/2020
ms.topic: how-to
author: garyericson
ms.author: garye
ms.custom: seo-lt-2019
monikerRange: '>=sql-server-ver15||>=sql-server-linux-ver15||=azuresqldb-mi-current'
ms.openlocfilehash: 9db282708c8f2e9bbd4ee44d45bac0b0d25dc5b9
ms.sourcegitcommit: 8a8c89b0ff6d6dfb8554b92187aca1bf0f8bcc07
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97617557"
---
# <a name="install-r-packages-with-sqlmlutils"></a>使用 sqlmlutils 安裝 R 套件

[!INCLUDE [SQL Server 2019 SQL MI](../../includes/applies-to-version/sqlserver2019-asdbmi.md)]

::: moniker range=">=sql-server-ver15||>=sql-server-linux-ver15"
本文描述如何使用 [**sqlmlutils**](https://github.com/Microsoft/sqlmlutils) 套件中的函數，將 R 套件安裝到 [SQL Server 機器學習服務](../sql-server-machine-learning-services.md)的執行個體和[巨量資料叢集](../../big-data-cluster/machine-learning-services.md)上。 您安裝的套件可用於使用 [sp_execute_external_script](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md) T-SQL 陳述式在資料庫中執行的 R 指令碼。

> [!NOTE]
> 本文所述的 **sqlmlutils** 套件是用來將 R 套件新增至 SQL Server 2019 或更新版本。 SQL Server 2017 及舊版請參閱[使用 R 工具安裝套件](./install-r-packages-standard-tools.md?view=sql-server-2017&preserve-view=true)。
::: moniker-end

::: moniker range="=azuresqldb-mi-current"
本文描述如何使用 [**sqlmlutils**](https://github.com/Microsoft/sqlmlutils) 套件中的函數，將 R 套件安裝到 [Azure SQL 受控執行個體機器學習服務](/azure/azure-sql/managed-instance/machine-learning-services-overview)的執行個體上。 您安裝的套件可用於使用 [sp_execute_external_script](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md) T-SQL 陳述式在資料庫中執行的 R 指令碼。
::: moniker-end

## <a name="prerequisites"></a>Prerequisites

- 在用來連線到 SQL Server 的用戶端電腦上安裝 [R](https://www.r-project.org) 與 [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/)。 您可以使用任何 R IDE 來執行指令碼，但此文章假設使用 RStudio。

  用戶端電腦上的 R 版本必須符合伺服器上的 R 版本，而且您安裝的套件必須符合所擁有的 R 版本。
  如需每個 SQL Server 版本隨附的 R 版本相關資訊，請參閱 [Python 和 R 版本](../sql-server-machine-learning-services.md#versions)。
  
  若要確認特定 SQL Server 上的 R 版本，請使用下列 T-SQL 命令。

  ```sql
  EXECUTE sp_execute_external_script @language = N'R'
   , @script = N'print(R.version)'
  ```

- 在用來連線到 SQL Server 的用戶端電腦上安裝 [Azure Data Studio](../../azure-data-studio/what-is.md)。 您可以使用其他資料庫管理或查詢工具，但此文章假設使用 Azure Data Studio。

### <a name="other-considerations"></a>其他考量

- 套件安裝是您在提供給 **sqlmlutils** 的連線資訊中指定的 SQL 執行個體、資料庫和使用者專用的。 若要在多個 SQL 執行個體或資料庫中使用套件，或將其用於不同的使用者，您必須個別為其安裝套件。 例外狀況是，如果套件是由 `dbo` 的成員所安裝，則套件會是 *公用* 的，且會與所有使用者共用。 如果使用者安裝了較新版本的公用套件，公用套件將不受影響，但該使用者將可存取較新的版本。

- 在 SQL Server 中執行的 R 指令碼只能使用安裝在預設執行個體程式庫中的套件。 SQL Server 無法從外部程式庫載入套件，即使該程式庫位於相同電腦上也一樣。 這包括隨其他 Microsoft 產品安裝的 R 程式庫。

- 在已強化的 SQL Server 環境中，您可以避免下列情況：
  - 需要網路存取的套件
  - 需要提升檔案系統存取權的套件
  - 用於網頁程式開發或其他工作，但無法透過在 SQL Server 內部執行而獲益的套件

## <a name="install-sqlmlutils-on-the-client-computer"></a>在用戶端電腦上安裝 sqlmlutils

若要使用 **sqlmlutils**，您必須先將其安裝在用來連線到 SQL Server 的用戶端電腦。

**sqlmlutils** 套件相依於 **odbc** 套件，而 **odbc** 相依於一些其他套件。 下列程序會以正確順序安裝所有這些套件。

### <a name="install-sqlmlutils-online"></a>線上安裝 sqlmlutils

若用戶端電腦可以存取網際網路，您可以在線上下載並安裝 **sqlmlutils** 與其相依套件。

1. 從 https://github.com/Microsoft/sqlmlutils/tree/master/R/dist \(英文\) 將最新的 **sqlmlutils** 檔案 (`.zip` 適用於 Windows，`.tar.gz` 適用於 Linux) 下載到用戶端電腦。 不要展開檔案。

1. 開啟 [命令提示字元] 並執行下列命令，以安裝 **odbc** 與 **sqlmlutils** 套件。 將路徑取代為您下載之 **sqlmlutils** 檔案的路徑。 會在線上找到 **odbc** 套件並安裝。

   ::: moniker range=">=sql-server-ver15||=azuresqldb-mi-current"
   ```console
   R.exe -e "install.packages('odbc')"
   R.exe CMD INSTALL sqlmlutils_1.0.0.zip
   ```
   ::: moniker-end

   ::: moniker range=">=sql-server-linux-ver15"
   ```console
   R.exe -e "install.packages('odbc')"
   R.exe CMD INSTALL sqlmlutils_1.0.0.tar.gz
   ```
   ::: moniker-end

### <a name="install-sqlmlutils-offline"></a>離線安裝 sqlmlutils

若用戶端電腦沒有網際網路連線，您必須使用可存取網際網路的電腦事先下載 **odbc** 與 **sqlmlutils** 套件。 接著，您可以將檔案複製到用戶端電腦上的資料夾，並離線安裝套件。

**odbc** 套件有數個相依套件，而識別套件的所有相依性會變得複雜。 建議您使用 [**miniCRAN**](https://andrie.github.io/miniCRAN/)，為包括所有相依套件的套件建立本機存放庫資料夾。
如需詳細資訊，請參閱[使用 miniCRAN 建立本機 R 套件存放庫](create-a-local-package-repository-using-minicran.md)。

**sqlmlutils** 套件包含單一檔案，您可以將該檔案複製到用戶端電腦並安裝。

在可以存取網際網路的電腦上：

1. 安裝 **miniCRAN**。 如需詳細資訊，請參閱 [安裝 miniCRAN](create-a-local-package-repository-using-minicran.md#install-minicran)。

1. 在 RStudio 中，執行下列 R 指令碼，以建立 **odbc** 套件的本機存放庫。 此範例假設存放庫將會在 `odbc` 資料夾中建立。

   ::: moniker range=">=sql-server-ver15||=azuresqldb-mi-current"
   ```R
   library("miniCRAN")
   CRAN_mirror <- c(CRAN = "https://cran.microsoft.com")
   local_repo <- "odbc"
   pkgs_needed <- "odbc"
   pkgs_expanded <- pkgDep(pkgs_needed, repos = CRAN_mirror);

   makeRepo(pkgs_expanded, path = local_repo, repos = CRAN_mirror, type = "win.binary", Rversion = "3.5");
   ```
   ::: moniker-end

   ::: moniker range=">=sql-server-linux-ver15"
   ```R
   library("miniCRAN")
   CRAN_mirror <- c(CRAN = "https://cran.microsoft.com")
   local_repo <- "odbc"
   pkgs_needed <- "odbc"
   pkgs_expanded <- pkgDep(pkgs_needed, repos = CRAN_mirror);

   makeRepo(pkgs_expanded, path = local_repo, repos = CRAN_mirror, type = "source", Rversion = "3.5");
   ```
   ::: moniker-end

   針對 `Rversion` 值，請使用 SQL Server 上安裝的 R 版本。 若要確認已安裝的版本，請使用下列 T-SQL 命令。

   ```sql
   EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'print(R.version)'
   ```

1. 從 [https://github.com/Microsoft/sqlmlutils/tree/master/R/dist](https://github.com/Microsoft/sqlmlutils/tree/master/R/dist) \(英文\) 下載最新的 **sqlmlutils** 檔案 (`.zip` 適用於 Windows，`.tar.gz` 適用於 Linux)。 不要展開檔案。

1. 將整個 **odbc** 存放庫資料夾與 **sqlmlutils** 檔案複製到用戶端電腦。

在用來連線到 SQL Server 的用戶端電腦上：

1. 開啟命令提示字元。

1. 執行下列命令以安裝 **odbc**，然後安裝 **sqlmlutils**。 請以您複製到此電腦之 **odbc** 存放庫資料夾與 **sqlmlutils** 檔案的完整路徑取代。

   ::: moniker range=">=sql-server-ver15||=azuresqldb-mi-current"
   ```console
   R.exe -e "install.packages('odbc', repos='odbc')"
   R.exe CMD INSTALL sqlmlutils_1.0.0.zip
   ```
   ::: moniker-end

   ::: moniker range=">=sql-server-linux-ver15"
   ```console
   R.exe -e "install.packages('odbc', repos='odbc')"
   R.exe CMD INSTALL sqlmlutils_1.0.0.tar.gz
   ```
   ::: moniker-end

## <a name="add-an-r-package-on-sql-server"></a>在 SQL Server 上新增 R 套件

在下列範例中，您會將 [**glue**](https://cran.r-project.org/web/packages/glue/) 套件新增至 SQL Server。

### <a name="add-the-package-online"></a>線上新增套件

如果您用來連線到 SQL Server 的用戶端電腦可以存取網際網路，則可以使用 **sqlmlutils** 透過網際網路尋找 **glue** 套件與任何相依性，然後從遠端將套件安裝到 SQL Server 執行個體。

1. 在用戶端電腦上，開啟 RStudio，並建立新的 **R 指令碼** 檔案。

1. 使用下列 R 指令碼，使用 **sqlmlutils** 安裝 **glue** 套件。 替換為您自己的 SQL Server 資料庫連線資訊。

   ```R
   library(sqlmlutils)
   connection <- connectionInfo(
     server   = "server",
     database = "database",
     uid      = "username",
     pwd      = "password")

   sql_install.packages(connectionString = connection, pkgs = "glue", verbose = TRUE, scope = "PUBLIC")
   ```

   > [!TIP]
   > **scope** 可以是 **PUBLIC** 或 **PRIVATE**。 資料庫管理員可以使用公開範圍來安裝所有使用者都可以使用的套件。 私人範圍可讓套件僅供安裝套件的使用者使用。 若未指定範圍，預設範圍是 **私人**。

### <a name="add-the-package-offline"></a>離線新增套件

若用戶端電腦沒有網際網路連線，您可以透過可以存取網際網路的電腦，使用 **miniCRAN** 來下載 **glue** 套件。 接著，將該套件複製到用戶端電腦，以便離線安裝套件。
如需有關安裝 **miniCRAN** 的詳細資訊，請參閱 [安裝 miniCRAN](create-a-local-package-repository-using-minicran.md#install-minicran)。

在可以存取網際網路的電腦上：

1. 執行下列 R 指令碼，以建立 **glue** 的本機存放庫。 此範例會在 `c:\downloads\glue` 中建立存放庫資料夾。

   ::: moniker range=">=sql-server-ver15||=azuresqldb-mi-current"
   ```R
   library("miniCRAN")
   CRAN_mirror <- c(CRAN = "https://cran.microsoft.com")
   local_repo <- "c:/downloads/glue"
   pkgs_needed <- "glue"
   pkgs_expanded <- pkgDep(pkgs_needed, repos = CRAN_mirror);

   makeRepo(pkgs_expanded, path = local_repo, repos = CRAN_mirror, type = "win.binary", Rversion = "3.5");
   ```
   ::: moniker-end

   ::: moniker range=">=sql-server-linux-ver15"
   ```R
   library("miniCRAN")
   CRAN_mirror <- c(CRAN = "https://cran.microsoft.com")
   local_repo <- "c:/downloads/glue"
   pkgs_needed <- "glue"
   pkgs_expanded <- pkgDep(pkgs_needed, repos = CRAN_mirror);

   makeRepo(pkgs_expanded, path = local_repo, repos = CRAN_mirror, type = "source", Rversion = "3.5");
   ```
   ::: moniker-end

   針對 `Rversion` 值，請使用 SQL Server 上安裝的 R 版本。 若要確認已安裝的版本，請使用下列 T-SQL 命令。

   ```sql
   EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'print(R.version)'
   ```

1. 將整個 **glue** 存放庫資料夾 (`c:\downloads\glue`) 複製到用戶端電腦。 例如，將它複製到 `c:\temp\packages\glue` 資料夾。

在用戶端電腦上：

1. 開啟 RStudio，並建立新的 **R 指令碼** 檔案。

1. 使用下列 R 指令碼，使用 **sqlmlutils** 安裝 **glue** 套件。 以您自己的 SQL Server 資料庫連線資訊取代 (如果您未使用 Windows 驗證，請新增 `uid` 和 `pwd` 參數)。

   ```R
   library(sqlmlutils)
   connection <- connectionInfo(
     server= "yourserver",
     database = "yourdatabase")
   localRepo = "c:/temp/packages/glue"

   sql_install.packages(connectionString = connection, pkgs = "glue", verbose = TRUE, scope = "PUBLIC", repos=paste0("file:///",localRepo))
   ```

   > [!TIP]
   > **scope** 可以是 **PUBLIC** 或 **PRIVATE**。 資料庫管理員可以使用公開範圍來安裝所有使用者都可以使用的套件。 私人範圍可讓套件僅供安裝套件的使用者使用。 若未指定範圍，預設範圍是 **私人**。

## <a name="use-the-package"></a>使用套件

安裝 **glue** 套件之後，您可以在 SQL Server 中的 R 指令碼中搭配 T-SQL **sp_execute_external_script** 命令加以使用。

1. 開啟 Azure Data Studio，並連線到您的 SQL Server 資料庫。

1. 執行以下命令：

   ```sql
   EXECUTE sp_execute_external_script @language = N'R'
       , @script = N'
   library(glue)

   name <- "Fred"
   birthday <- as.Date("2020-06-14")
   text <- glue(''My name is {name} '',
   ''and my birthday is {format(birthday, "%A, %B %d, %Y")}.'')

   print(text)
         ';
   ```

    **結果**

    ```text
    My name is Fred and my birthday is Sunday, June 14, 2020.
    ```

## <a name="remove-the-package"></a>移除套件

若要移除 **glue** 套件，請執行下列 R 指令碼。 使用您稍早定義的相同 **connection** 變數。

```R
sql_remove.packages(connectionString = connection, pkgs = "glue", scope = "PUBLIC")
```

## <a name="more-sqlmlutils-functions"></a>更多 sqlmlutils 函數

**sqlmlutils** 套件包含許多用於管理 R 套件，以及在 SQL Server 中建立、管理及執行預存程序與查詢的函數。 如需詳細資料，請參閱 [sqlmlutils R 讀我檔案](https://github.com/microsoft/sqlmlutils/tree/master/R)。

如需任何 **sqlmlutils** 函數的詳細資訊，請使用 R **help** 函數或 **?** 運算子。 例如：

```R
library(sqlmlutils)
help("sql_install.packages")
```

## <a name="next-steps"></a>後續步驟

- 如需已安裝之 R 套件的詳細資訊，請參閱[取得 R 套件資訊](r-package-information.md)
- 如需有關使用 R 套件的說明，請參閱[使用 R 套件的祕訣](tips-for-using-r-packages.md)
- 如需有關安裝 Python 套件的詳細資訊，請參閱[使用 pip 安裝 Python 套件](install-additional-python-packages-on-sql-server.md)
- 如需 SQL Server 機器學習服務的詳細資訊，請參閱 [什麼是 SQL Server 機器學習服務 (Python 與 R)？](../sql-server-machine-learning-services.md)
