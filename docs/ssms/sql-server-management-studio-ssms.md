---
title: SQL Server Management Studio (SSMS)
description: 深入了解 SQL Server Management Studio (SSMS) 和 SMMS 可執行的工作，包括如何管理 Analysis Services 解決方案。
ms.prod: sql
ms.technology: ssms
ms.topic: overview
author: markingmyname
ms.author: maghan
ms.reviewer: ''
f1_keywords:
- sql13.ssms.viewhelp.f1
helpviewer_keywords:
- SQL Server Management Studio
- SQL Server Management Studio for Integration Services
- SQL Server Management Studio for Reporting Services
- SQL Server Management Studio for Analysis Services
ms.custom: seo-lt-2019
ms.date: 09/11/2019
ms.openlocfilehash: 1961d2f831a8db86c782a5e2330d74f4346d6d19
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98096081"
---
# <a name="what-is-sql-server-management-studio-ssms"></a>什麼是 SQL Server Management Studio (SSMS)？

[!INCLUDE [SQL Server ASDB, ASDBMI, ASDW ](../includes/applies-to-version/sql-asdb-asdbmi-asa.md)]

[!INCLUDE[ssManStudioFull](../includes/ssmanstudiofull-md.md)] (SSMS) 是用於管理任何 SQL 基礎結構的整合式環境。 使用 SSMS 存取、設定、管理、控管和開發 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]、Azure SQL Database 和 Azure Synapse Analytics 的所有元件。 SSMS 能提供單一的完整公用程式，結合了廣泛的圖形工具和豐富的指令碼編輯器，使開發人員和資料庫管理員 (不管他們的技術水準如何) 都能夠存取 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]。

- [**下載 SQL Server Management Studio (SSMS)**](download-sql-server-management-studio-ssms.md)
- [**下載 SQL Server Developer**](https://my.visualstudio.com/Downloads?q=SQL%20Server%20Developer)
- [**下載 Visual Studio**](https://www.visualstudio.com/downloads/)

![SQL Server Management Studio 的螢幕擷取畫面。](media/sql-server-management-studio-ssms/ssms.png)

## <a name="sql-server-management-studio-components"></a>SQL Server Management Studio 元件  
  
|描述|元件|  
|---------------|---------|  
|使用 **物件總管** 檢視及管理一個或多個 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]執行個體中的所有物件。|[物件總管](../ssms/object/object-explorer.md)|  
|如何使用 [範本總管]  建立及管理重複使用文字的檔案，以用來加快查詢與指令碼的開發速度。|[範本總管](../ssms/template/template-explorer.md)|  
|如何使用即將淘汰的 **方案總管** 建立專案，以管理管理項目 (例如指令碼與查詢)。|[方案總管](../ssms/solution/solution-explorer.md)|  
|如何使用 [!INCLUDE[ssManStudio](../includes/ssmanstudio-md.md)]隨附的視覺化設計工具。|[Visual Database Tools](../ssms/visual-db-tools/visual-database-tools.md)|  
|如何使用 [!INCLUDE[ssManStudio](../includes/ssmanstudio-md.md)] 語言編輯器以互動的方式建立查詢與指令碼，以及對其執行偵錯。|[查詢與文字編輯器](./f1-help/database-engine-query-editor-sql-server-management-studio.md)

## <a name="sql-server-management-studio-for-business-intelligence"></a>適用於商業智慧的 SQL Server Management Studio

若要存取、設定及管理 [!INCLUDE[ssASnoversion](../includes/ssasnoversion_md.md)]、[!INCLUDE[ssISnoversion](../includes/ssisnoversion-md.md)] 和 [!INCLUDE[ssRSnoversion](../includes/ssrsnoversion-md.md)]，請使用 [!INCLUDE[ssManStudioFull](../includes/ssmanstudiofull-md.md)]。 雖然這三種商業智慧技術全都依賴 [!INCLUDE[ssManStudioFull](../includes/ssmanstudiofull-md.md)]，但是與每一項技術有關的管理工作則會有些微的差異。

> [!NOTE]
> 若要建立及修改 [!INCLUDE[ssASnoversion](../includes/ssasnoversion_md.md)]、 [!INCLUDE[ssRSnoversion](../includes/ssrsnoversion-md.md)]和 [!INCLUDE[ssISnoversion](../includes/ssisnoversion-md.md)] 解決方案，請使用 [!INCLUDE[ssBIDevStudioFull](../includes/ssbidevstudiofull_md.md)]，而不要使用 [!INCLUDE[ssManStudioFull](../includes/ssmanstudiofull-md.md)]。 [!INCLUDE[ssBIDevStudioFull](../includes/ssbidevstudiofull_md.md)] 是一個以 [!INCLUDE[msCoName](../includes/msconame_md.md)][!INCLUDE[vsprvs](../includes/vsprvs-md.md)]為根據的開發環境。

### <a name="managing-analysis-services-solutions-using-sql-server-management-studio"></a>使用 SQL Server Management Studio 管理 Analysis Services 解決方案

[!INCLUDE[ssManStudioFull](../includes/ssmanstudiofull-md.md)] 可讓您管理 [!INCLUDE[ssASnoversion](../includes/ssasnoversion_md.md)] 物件，例如執行備份及處理物件。

[!INCLUDE[ssManStudio](../includes/ssmanstudio-md.md)] 會提供一個 [!INCLUDE[ssASnoversion](../includes/ssasnoversion_md.md)] 指令碼專案，您可在其中開發及儲存使用多維度運算式 (MDX)、資料採礦延伸模組 (DMX) 和 XML for Analysis (XMLA) 所撰寫的指令碼。 您可以使用 [!INCLUDE[ssASnoversion](../includes/ssasnoversion_md.md)] 指令碼專案來執行管理工作或是在 [!INCLUDE[ssASnoversion](../includes/ssasnoversion_md.md)] 執行個體上重新建立物件，例如資料庫和 Cube。 例如，您可以在 [!INCLUDE[ssASnoversion](../includes/ssasnoversion_md.md)] 指令碼專案中開發 XMLA 指令碼，該指令碼會直接在現有的 [!INCLUDE[ssASnoversion](../includes/ssasnoversion_md.md)] 執行個體上建立新的物件。 [!INCLUDE[ssASnoversion](../includes/ssasnoversion_md.md)] 指令碼專案可儲存成為方案的一部分，並與原始程式碼控制整合。
  
如需如何使用 [!INCLUDE[ssManStudioFull](../includes/ssmanstudiofull-md.md)]的詳細資訊，請參閱 [使用 SQL Server Management Studio 進行開發和實作](/analysis-services/instances/analysis-services-scripts-project-in-sql-server-management-studio)。
  
### <a name="managing-integration-services-solutions-using-sql-server-management-studio"></a>使用 SQL Server Management Studio 管理 Integration Services 解決方案

[!INCLUDE[ssManStudioFull](../includes/ssmanstudiofull-md.md)] 可讓您使用 [!INCLUDE[ssISnoversion](../includes/ssisnoversion-md.md)] 服務來管理套件及監視執行中的套件。 您也可以使用 [!INCLUDE[ssManStudio](../includes/ssmanstudio-md.md)] 將封裝組織成資料夾、執行封裝、匯入及匯出封裝、移轉 Data Transformation Services (DTS) 封裝及升級 [!INCLUDE[ssISnoversion](../includes/ssisnoversion-md.md)] 封裝。

### <a name="managing-reporting-services-projects-using-sql-server-management-studio"></a>使用 SQL Server Management Studio 來管理 Reporting Services 專案

使用 SQL Server Management Studio 可啟用 Reporting Services 功能、管理服務和資料庫，以及管理角色和作業。

您可使用 [共用排程] 資料夾來管理共用排程，並管理報表伺服器資料庫 (ReportServer、ReportServerTempdb)。 當您將報表伺服器資料庫移到新的或另一個 SQL Server 資料庫引擎 ([!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] [!INCLUDE[ssDE](../includes/ssde_md.md)]) 時，您也會在 Master 系統資料庫中建立 RSExecRole。 如需這些工作的詳細資訊，請參閱下列文章：  

- [SSMS 中的 Reporting Services](../reporting-services/tools/reporting-services-in-sql-server-management-studio-ssrs.md)
- [管理報表伺服器資料庫](../reporting-services/report-server/administer-a-report-server-database-ssrs-native-mode.md)
- [建立 RSExecRole](../reporting-services/security/create-the-rsexecrole.md)

您也可以透過以下方式來管理伺服器：啟用及設定各種功能、設定伺服器預設值及管理角色和作業。 如需這些工作的詳細資訊，請參閱下列文章：

- [設定報表伺服器屬性](../reporting-services/tools/set-report-server-properties-management-studio.md)
- [建立、刪除或修改角色](../reporting-services/security/role-definitions-create-delete-or-modify.md)
- [啟用和停用 Reporting Services 的用戶端列印](../reporting-services/report-server/enable-and-disable-client-side-printing-for-reporting-services.md)

## <a name="non-english-language-versions-of-sql-server-management-studio-ssms"></a>非英文版本的 SQL Server Management Studio (SSMS)

已解除封鎖混合語言安裝。 您可以在法文版 Windows 上安裝 SSMS 德文版。 如果作業系統語言與 SSMS 語言不相符，則使用者必須在 [工具] > [選項] > [國際設定] 下變更語言， 否則 SSMS 會顯示英文的 UI。

如需舊版不同地區設定的詳細資訊，請參閱[安裝非英文版本的 SSMS](install-other-languages.md)。

## <a name="support-policy-for-ssms"></a>SSMS 的支援原則

- 從 SSMS 17.0 開始，SQL 工具小組採用 [Microsoft 現代化生命週期原則](https://support.microsoft.com/help/30881/modern-lifecycle-policy)。
- 請閱讀原始[現代化生命週期原則公告](https://support.microsoft.com/help/447912/announcing-microsoft-modern-lifecycle-policy)。 如需詳細資訊，請參閱[現代化原則常見問題集](https://support.microsoft.com/help/30882/modern-lifecycle-policy-faq)。
- 如需診斷資料收集和功能使用方式的詳細資訊，請參閱 [SQL Server 隱私權補充](../sql-server/sql-server-privacy.md)。

## <a name="cross-platform-tool"></a>跨平台工具

[!INCLUDE[ssms-azure-data-studio-mention](../includes/ssms-azure-data-studio-mention.md)]

[!INCLUDE[get-help-options](../includes/paragraph-content/get-help-options.md)]

## <a name="next-steps"></a>後續步驟

- [安裝非英文版本的 SSMS](install-other-languages.md)
- [連線及查詢 SQL Server 執行個體](./quickstarts/connect-query-sql-server.md)
- [撰寫 Transact-SQL 陳述式](../t-sql/tutorial-writing-transact-sql-statements.md)
- [Azure Data Studio](../azure-data-studio/what-is-azure-data-studio.md)

[!INCLUDE[contribute-to-content](../includes/paragraph-content/contribute-to-content.md)]