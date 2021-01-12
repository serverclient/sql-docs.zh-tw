---
title: Linux 上的 SQL Server 常見問題集
description: 此文章提供 SQL Server 在 Linux 上執行的常見問題集 (FAQ) 解答。
author: VanMSFT
ms.author: vanto
ms.date: 10/23/2019
ms.topic: conceptual
ms.prod: sql
ms.technology: linux
ms.openlocfilehash: dc5d54c3d64d43eb4b68a6762e3bd024587937ab
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98094648"
---
# <a name="sql-server-on-linux-frequently-asked-questions-faq"></a>Linux 上的 SQL Server 常見問題集 (FAQ)

[!INCLUDE [SQL Server - Linux](../includes/applies-to-version/sql-linux.md)]

下列各節提供 SQL Server 在 Linux 上執行的常見問題與解答。

## <a name="general-questions"></a>一般問題

1. **支援哪些 Linux 平台？**

   SQL Server 目前支援 Red Hat Enterprise Server、SUSE Linux Enterprise Server 和 Ubuntu。 也支援透過 Docker 在容器中執行。 如需支援版本的最新資訊，請參閱[支援的平台](sql-server-linux-setup.md#supportedplatforms)。

1. **Linux 上的 SQL Server 適用於其他平台嗎？**

   SQL Server 已針對先前列出的發行版本，在 Linux 上經過測試並受到支援。 其他與 Linux 發行版本密切相關的產品或許能夠執行 SQL Server (例如 CentOS 與 Red Hat Enterprise Server 密切相關)。 但如果選擇在不支援的作業系統上安裝 SQL Server，請檢閱 [Technical support policy for Microsoft SQL Server](https://support.microsoft.com/help/4047326/support-policy-for-microsoft-sql-server) (Microsoft SQL Server 的技術支援原則) 的＜Support policy＞(支援原則)  一節以了解隱含的支援。 另請注意，如果問題在於基礎作業系統，某些社群維護的 Linux 發行版本無法獲得正式支援。

1. **Linux 上的 SQL Server 與 Windows 上的相同嗎？**

   SQL Server 的核心資料庫引擎在 Linux 和 Windows 上都相同。 不過，有些功能目前在 Linux 上不支援。 如需 Linux 上不支援的功能清單，請參閱[不支援的功能與服務](sql-server-linux-editions-and-components-2019.md#Unsupported)。 另請檢閱[已知問題](sql-server-linux-release-notes.md#known-issues)。 除了這些清單上所指定的之外，其他 SQL Server 功能與服務皆在 Linux 上支援。

1. **SQL Server 的支援原則為何？**

   若要了解支援原則，請檢閱 [Technical Support Policy for SQL Server](https://support.microsoft.com/help/4047326/support-policy-for-microsoft-sql-server) (SQL Server 的技術支援原則)。

1. **我有使用 Windows SQL Server 的經驗。是否有資源可協助我了解如何使用 Linux 上的 SQL Server？**

   [快速入門](sql-server-linux-setup.md#platforms)提供如何在 Linux 上安裝 SQL Server 及執行 Transact-SQL 查詢的逐步指示。 其他教學課程會提供如何使用 Linux 上 SQL Server 的其他指示。 如需協力廠商的提示清單，請參閱 MSSQLTIPS 的 [SQL Server on Linux Tips](https://www.mssqltips.com/sql-server-tip-category/226/sql-server-on-linux/) (Linux 上的 SQL Server 提示) 清單。

## <a name="licensing"></a>授權

1. **Linux 上的授權方式為何？**

   SQL Server 在 Windows 和 Linux 上都是以相同的方式進行授權。 事實上，您會授權 SQL Server，然後即可選擇在所選的平台上使用該授權。 如需詳細資訊，請參閱[如何授權 SQL Server](https://www.microsoft.com/sql-server/sql-server-2017-pricing)。

1. **如果我已購買 SQL Server，該選擇哪一版？**

   當您執行 mssql-conf 安裝程式時，會出現下列選項：
   
   ```
   Choose an edition of SQL Server:
      1. Evaluation (free, no production use rights, 180-day limit)
      2. Developer (free, no production use rights)
      3. Express (free)
      4. Web (PAID)
      5. Standard (PAID)
      6. Enterprise (PAID)
      7. Enterprise Core (PAID)
      8. I bought a license through a retail sales channel and have a product key to enter.
   ```
     
   如果您是透過 Enterprise 合約的大量授權，或以 MSDN 訂用帳戶取得授權，必須選取選項 4 到 7。 此步驟不會要求您輸入授權，但您之前必須已購買適合組態的授權。 如果您透過零售通路購買了 Standard 版本，請選取選項 8。 此選項會提示您輸入金鑰。 

1. **如何確認 Linux 上所安裝的 SQL Server 版本與版次？**

   使用 **sqlcmd**、**mssql-cli** 或 Visual Studio Code 等用戶端工具連線到 SQL Server 執行個體。 然後執行下列 Transact-SQL 查詢，確認您正在執行的 SQL Server 版本與版次： 

   ```sql
   SELECT @@VERSION
   SELECT SERVERPROPERTY('Edition')
   ```

## <a name="installation"></a>安裝

1. **如何在我的 Linux 伺服器上安裝 SQL Server？**

   Microsoft 維護用於安裝 SQL Server 的套件存放庫，並支援透過 yum、zypper 和 apt 等原生套件管理員進行安裝。 若要快速安裝，請參閱任一篇[快速入門](sql-server-linux-setup.md#platforms)。

1. **我可以在適用於 Windows 10 的 Linux 子系統上安裝 SQL Server 嗎？**

   否。 Windows 10 上執行的 Linux 目前不是 SQL Server 和相關工具的支援平台。

1. **SQL Server 可以針對資料檔案使用哪些 Linux 檔案系統？**

   Linux 上的 SQL Server 目前支援 ext4 和 XFS。 未來將視需要，新增其他檔案系統的支援。

1. **我可以下載安裝套件，以離線方式安裝 SQL Server 嗎？**

   是。 如需詳細資訊，請參閱[版本資訊](sql-server-linux-release-notes.md)中的套件下載連結。 另請檢閱[離線安裝的指示](sql-server-linux-setup.md#offline)。

1. **我可以在 Linux 上自動安裝 SQL Server 嗎？**

   是。 如需自動安裝的討論，請參閱 [Linux 上的 SQL Server 安裝指引](sql-server-linux-setup.md#unattended)。 請參閱 [Red Hat](sample-unattended-install-redhat.md)、[SUSE Linux Enterprise Server](sample-unattended-install-suse.md) 和 [Ubuntu](sample-unattended-install-ubuntu.md) 的範例指令碼。 您也可以檢閱 SQL Server 客戶諮詢小組所建立的[這個範例指令碼](/archive/blogs/sqlcat/unattended-install-and-configuration-for-sql-server-2017-on-linux)。

## <a name="tools"></a>工具

1. **我可以使用 Windows 上的 SQL Server Management Studio 用戶端存取 Linux 上的 SQL Server 嗎？**

   是的，您可以使用 Windows 上所有現成的工具存取 Linux 上的 SQL Server。 包括 Microsoft 工具 (例如 SQL Server Management Studio (SSMS)、SQL Server Data Tools (SSDT) 和 OSS)，以及協力廠商工具。

1. **Linux 上是否執行 SSMS 之類的工具？**

   新的 Azure Data Studio 是用於管理 SQL Server 的跨平台工具。 如需詳細資訊，請參閱[什麼是 Azure Data Studio](../azure-data-studio/what-is-azure-data-studio.md)。

1. **Linux 上是否提供 sqlcmd 和 bcp 之類的命令？**

   是的，Linux、macOS 和 Windows 上原本就提供 [sqlcmd 和 bcp](sql-server-linux-setup-tools.md)。 此外，還可以在 Linux、macOS 或 Windows 上使用新的 [mssql-scripter](https://github.com/Microsoft/mssql-scripter) 命令列工具，為您在任意位置執行的 SQL 資料庫產生 T-SQL 指令碼。 另請參閱 [mssql-cli](https://blogs.technet.microsoft.com/dataplatforminsider/2017/12/12/try-mssql-cli-a-new-interactive-command-line-tool-for-sql-server/) 的預覽版本。

1. **當您透過 Windows 上的 SSMS 連線時，是否可以檢視 Linux 上執行之執行個體的活動監視器？**

   是的，您可以使用 Windows 上的 SSMS 遠端連線，然後在 Linux 執行個體上使用工具/功能 (例如活動監視器命令)。

1. **有哪些工具可用來監視 Linux 上的 SQL Server 效能？**

   您可以使用[系統動態管理檢視 (DMV)](../relational-databases/system-dynamic-management-views/system-dynamic-management-views.md) 收集有關 SQL Server 的各種資訊類型，包括 Linux 處理序資訊。 您可以使用[查詢存放區](../relational-databases/performance/monitoring-performance-by-using-the-query-store.md)，改善查詢效能。 其他工具 (例如內建的[效能儀表板](/archive/blogs/sql_server_team/new-in-ssms-performance-dashboard-built-in)) 可從 Windows 的 SQL Server Management Studio (SSMS) 遠端工作。

   > [!TIP]
   > 改善效能的方式之一為適當地設定您的 Linux 作業系統和 SQL Server 執行個體。 如需詳細資訊，請參閱 [Linux 上的 SQL Server 效能最佳做法和設定方針](sql-server-linux-performance-best-practices.md)。

## <a name="administration"></a>系統管理

1. **Microsoft 是否已在 Linux 上建立類似 SQL Server 組態管理員的應用程式？**

   是的，Linux 上的 SQL Server 組態工具為：[mssql-conf](sql-server-linux-configure-mssql-conf.md)。

1. **Linux 上的 SQL Server 是否支援相同主機上的多個執行個體？**

   建議您在主機上執行多個容器，可以擁有多個不同的執行個體。 使用 Docker 即可輕鬆達成此目的，但每個容器必須在不同的連接埠上接聽。 如需詳細資訊，請參閱[執行多個 SQL Server 容器](./sql-server-linux-docker-container-deployment.md#multiple)。

1. **Linux 上是否支援 Active Directory 驗證？**

   是。 如需詳細資訊，請參閱[在 Linux 上的 SQL Server 使用 Active Directory 驗證](sql-server-linux-active-directory-authentication.md)。

1. **Linux 上是否支援 Always On 和叢集？**

   Linux 上的容錯移轉叢集和高可用性是透過 Linux 上的 Pacemaker 來達成。 如需詳細資訊，請參閱[商務持續性與資料庫復原 - Linux 上的 SQL Server](sql-server-linux-business-continuity-dr.md)。

1. **是否可以設定 Linux 與 Windows 之間的複寫？**

   您可以在 Windows 與 Linux 之間使用讀取級別複本，進行單向資料複寫。

1. **是否可以將舊版 SQL Server 中的現有資料庫從 Windows 移轉到 Linux？**

   是的，有[幾種方法](sql-server-linux-migrate-overview.md)可以達成此目的。

1. **我可以將資料從 Oracle 和其他資料庫引擎移轉到 Linux 上的 SQL Server 嗎？**

   是。 SSMA 支援從數種資料庫引擎類型進行移轉：Microsoft Access、DB2、MySQL、Oracle 和 SAP ASE (先前稱為 SAP Sybase ASE)。 如需如何使用 SSMA 的範例，請參閱[使用 SQL Server 移轉小幫手將 Oracle 結構描述移轉到 Linux 上的 SQL Server](../ssma/oracle/sql-server-linux-convert-from-oracle.md?toc=/sql/toc/toc.json)。

1. **SQL Server 檔案需要哪些權限？**

   `/var/opt/mssql` 檔案資料夾中的所有檔案都應該由 **mssql** 使用者所擁有並屬於 **mssql** 群組。 **mssql** 使用者和群組都應該具有所有檔案和目錄的讀寫權限。 請注意以下需要檔案和目錄權限的特殊案例：

   * 用來儲存 SQL Server 檔案的裝載網路共用需要 mssql 擁有者和群組的權限。
   * 如果您在非預設目錄中尋找資料庫檔案或備份，也必須設定該目錄的權限。
   * 如果您變更了預設根 umask 0022，安裝後 SQL Server 組態會失敗。 您屆時必須手動將必要的權限套用至 SQL Server 啟動帳戶。

1. **我可以從安裝的 mssql 帳戶和群組變更 SQL Server 檔案和目錄的擁有權嗎？**

   我們不支援從預設安裝變更 SQL Server 目錄和檔案的擁有權。 mssql 帳戶和群組專門用於 SQL Server，無法進行互動式登入存取。
   
 1. **SQL Server 資料和記錄檔目錄是否支援符號連結？** 
    
    否，SQL Server 資料和記錄檔目錄不支援符號連結。 若要變更預設資料和記錄檔目錄，請參閱[變更預設資料或記錄檔目錄位置](sql-server-linux-configure-mssql-conf.md#datadir)。
    
[!INCLUDE[Get Help Options](../includes/paragraph-content/get-help-options.md)]