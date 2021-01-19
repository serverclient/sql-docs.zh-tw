---
title: 支援的版本與版本升級 (SQL Server 2017)
description: SQL Server 2017 支援的版本與版本升級。
ms.custom: seo-lt-2019
ms.date: 12/13/2019
ms.prod: sql
ms.reviewer: ''
ms.technology: install
ms.topic: conceptual
helpviewer_keywords:
- components [SQL Server], adding to existing installations
- versions [SQL Server], upgrading
- upgrading SQL Server, upgrades supported
- cross-language support
ms.assetid: 702359c4-6ca9-42a8-860c-a95a802898a1
author: cawrites
ms.author: chadam
monikerRange: '>=sql-server-2016'
ms.openlocfilehash: 660f0da5aa9fc59c6c742373660e95d3ad0bcf95
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98172260"
---
# <a name="supported-version--edition-upgrades-sql-server-2017"></a>支援的版本與版本升級 (SQL Server 2017)

[!INCLUDE [SQL Server -Windows Only](../../includes/applies-to-version/sql-windows-only.md)]
  
  您可以從 [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)]、[!INCLUDE[ssKilimanjaro](../../includes/sskilimanjaro-md.md)]、[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)]、[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 和 [!INCLUDE[sssql15-md](../../includes/sssql16-md.md)] 升級。 本文列出這些 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 版本的支援升級路徑，以及可以升級至 [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] 的支援版本。  
  
## <a name="pre-upgrade-checklist"></a>升級前檢查清單  
  
-   從 [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] 的一個版本升級到另一個版本之前，請確認您目前使用的功能在您目標版本中受到支援。  
  
-   在升級 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]之前，請對 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent 啟用 Windows 驗證，並確認預設組態： [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent 服務帳戶是 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 系統管理員 (sysadmin) 群組的成員。  
  
-   若要升級到 [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)]，您必須執行支援的作業系統。 如需詳細資訊，請參閱[安裝 SQL Server 的硬體與軟體需求](../../sql-server/install/hardware-and-software-requirements-for-installing-sql-server.md)。  
  
-   當重新啟動暫止時將會封鎖升級  
  
-   如果 Windows Installer 服務未在執行中，將會封鎖升級。  
  
## <a name="unsupported-scenarios"></a>不支援的案例  
  
-   不支援 [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] 的跨版本執行個體。 在 [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] 執行個體內，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 元件的版本號碼必須相同。  
  
-   [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] 僅適用於 64 位元平台。 不支援跨平台升級。 您無法使用 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式，將 32 位元的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體升級到原生 64 位元。 不過，您還是可以備份或卸離 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]之 32 位元執行個體的資料庫，而且如果複寫時未發行這些資料庫，也可以將它們還原或附加至 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (64 位元) 的新執行個體。 您必須在 master、msdb 和 model 系統資料庫中重新建立任何登入及其他使用者物件。  
  
-   您無法在升級現有的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]執行個體期間加入新功能。 將 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的執行個體升級至 [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] 之後，可使用 [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] 安裝程式加入功能。 如需詳細資訊，請參閱[將功能加入至 SQL Server 的執行個體 &#40;安裝程式&#41;](./add-features-to-an-instance-of-sql-server-setup.md)。  
 
-   在 WOW 模式下不支援容錯移轉叢集。  
    
## <a name="upgrades-from-earlier-versions-to-sssqlv14-md"></a>從舊版升級至 [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)]  
 
[!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] 支援從下列版本的 SQL Server 進行升級：
 
- SQL Server 2008 SP4 或更新版本
- SQL Server 2008 R2 SP3 或更新版本
- SQL Server 2012 SP2 或更新版本
- SQL Server 2014 或更新版本 
- SQL Server 2016 或更新版本
 

  
> [!NOTE]  
>  若要升級 [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] 上的資料庫，請參閱 [2005 的支援](#SupportFor2005)。  
  
 下表列出從舊版 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 升級至 [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] 的支援案例。  
  
|升級來源|支援的升級路徑|  
|:------|:------|  
|[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] SP4 Enterprise|[!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Enterprise <br/> |  
|[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] SP4 Developer|[!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Developer|  
|[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] SP4 Standard|[!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Enterprise <br/><br/> [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Standard|  
|[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] SP4 Small Business|[!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Standard|  
|[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] SP4 Web|[!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Enterprise <br/><br/> [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Standard <br/> <br/> [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Web|  
|[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] SP4 Workgroup|[!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Enterprise <br/><br/> [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Standard|  
|[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] SP4 Express |[!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Enterprise <br/><br/> [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Standard <br/> <br/> [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Web <br/> <br/> [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Express|  
|[!INCLUDE[ssKilimanjaro](../../includes/sskilimanjaro-md.md)] SP3 Datacenter|[!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Enterprise <br/> |  
|[!INCLUDE[ssKilimanjaro](../../includes/sskilimanjaro-md.md)] SP3 Enterprise|[!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Enterprise <br/> |  
|[!INCLUDE[ssKilimanjaro](../../includes/sskilimanjaro-md.md)] SP3 Developer|[!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Developer|  
|[!INCLUDE[ssKilimanjaro](../../includes/sskilimanjaro-md.md)] SP3 Small Business|[!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Standard|  
|[!INCLUDE[ssKilimanjaro](../../includes/sskilimanjaro-md.md)] SP3 Standard|[!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Enterprise <br/><br/> [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Standard|  
|[!INCLUDE[ssKilimanjaro](../../includes/sskilimanjaro-md.md)] SP3 Web|[!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Enterprise <br/><br/> [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Standard <br/> <br/> [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Web|  
|[!INCLUDE[ssKilimanjaro](../../includes/sskilimanjaro-md.md)] SP3 Workgroup|[!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Enterprise <br/><br/> [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Standard|  
|[!INCLUDE[ssKilimanjaro](../../includes/sskilimanjaro-md.md)] SP3 Express |[!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Enterprise <br/><br/> [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Standard <br/> <br/> [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Web <br/> <br/> [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Express|  
|[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] SP2 Enterprise|[!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Enterprise <br/> |  
|[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] SP2 Developer|[!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Developer <br/> <br/> [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Standard <br/> <br/> [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Web <br/> <br/> [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Enterprise <br/> |  
|[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] SP2 Standard|[!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Enterprise <br/><br/> [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Standard|  
|[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] SP1 Web|[!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Enterprise <br/><br/> [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Standard <br/> <br/> [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Web|  
|[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] SP2 Express |[!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Enterprise <br/><br/> [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Standard <br/> <br/> [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Web <br/> <br/> [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Express <br/> <br/> |  
|[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] SP2 商業智慧|[!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Enterprise <br/> |  
|[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] SP2 Evaluation|[!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Evaluation <br/> <br/> [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Enterprise <br/><br/> [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Standard <br/> <br/> [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Web <br/> <br/> [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Developer|  
|[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] Enterprise|[!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Enterprise <br/> |  
|[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] Developer|[!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Developer <br/> <br/> [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Standard <br/> <br/> [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Web <br/> <br/> [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Enterprise <br/> |  
|[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] Standard|[!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Enterprise <br/><br/> [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Standard|  
|[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] Web|[!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Enterprise <br/><br/> [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Standard <br/> <br/> [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Web|  
|[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] Express |[!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Enterprise <br/><br/> [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Standard <br/> <br/> [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Web <br/> <br/> [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Express <br/> <br/> [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Developer|  
|[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] Business Intelligence|[!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Enterprise <br/> |  
|[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] Evaluation|[!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Evaluation <br/> <br/> [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Enterprise <br/><br/> [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Standard <br/> <br/> [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Web <br/> <br/> [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Developer|
|[!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] Enterprise|[!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Enterprise <br/> |  
|[!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] Developer|[!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Developer <br/> <br/> [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Standard <br/> <br/> [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Web <br/> <br/> [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Enterprise <br/> |  
|[!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] Standard|[!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Enterprise <br/><br/> [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Standard|  
|[!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] Web|[!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Enterprise <br/><br/> [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Standard <br/> <br/> [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Web|  
|[!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] Express |[!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Enterprise <br/><br/> [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Standard <br/> <br/> [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Web <br/> <br/> [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Express <br/> <br/> [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Developer|  
|[!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] Business Intelligence|[!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Enterprise <br/> |  
|[!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] Evaluation|[!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Evaluation <br/> <br/> [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Enterprise <br/><br/> [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Standard <br/> <br/> [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Web <br/> <br/> [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Developer|
|[!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] 候選版* |[!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Enterprise |  
|[!INCLUDE[sssqlv14_md](../../includes/sssqlv14-md.md)] Developer |[!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Enterprise | 

 \* Microsoft 對於從候選版軟體升級的支援是專門針對參與 Technology Adoption Program (TAP) 的客戶。 

   
###  <a name="sssqlv14-md-support-for-ssversion2005"></a><a name="SupportFor2005"></a> [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] 對 [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] 的支援  
 本節將討論 [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] 對 [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] 的支援。 在 [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] 中，您將能夠進行下列作業：  
  
-   將 [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] 資料庫 (mdf/ldf 檔案) 附加到 [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] 的 Database Engine 執行個體。  
  
-   從備份將 [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] 資料庫還原到 [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] 的 Database Engine 執行個體。  
  
-   備份 [!INCLUDE[ssASversion2005](../../includes/ssasversion2005-md.md)] Cube 並還原到 [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)]。  
  
當 [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] 資料庫升級到 [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] 時，資料庫的相容性層級將會從 90 變更為 100。 (在 [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] 中，資料庫相容性層級的有效值為 100、110、120、130 和 140。)[ALTER DATABASE 相容性層級 &#40;Transact-SQL&#41;](../../t-sql/statements/alter-database-transact-sql-compatibility-level.md) 中有討論相容性層級變更如何影響 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 應用程式的情形。  
  
未在上面清單中指定的任何情況都不受支援，包含但不限於以下項目：  
  
- 在相同電腦上安裝 [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] 和 [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] (並存安裝)。  
  
- 使用 [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] 執行個體當做與 [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] 執行個體有關之複寫拓撲的成員。  
  
- 在 [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] 與 [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] 執行個體之間設定資料庫鏡像。  
  
- 在 [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] 與 [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] 執行個體之間使用記錄傳送備份交易記錄。  
  
- 在 [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] 與 [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] 執行個體之間設定連結的伺服器。  
  
- 從 [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] Management Studio 管理 [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] 執行個體。  
  
- 在 [!INCLUDE[ssASversion2005](../../includes/ssasversion2005-md.md)] Management Studio 中附加 [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Cube。  
  
- 從 [!INCLUDE[ssISversion2005](../../includes/ssisversion2005-md.md)] Management Studio 連接到 [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)]。  
  
- 從 [!INCLUDE[ssISversion2005](../../includes/ssisversion2005-md.md)] Management Studio 管理 [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] 服務。  
  
- 對 [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] 協力廠商自訂 Integration Services 元件的支援，例如執行和升級。  
  
## <a name="sssqlv14-md-edition-upgrade"></a>[!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] 版本升級  
下表列出 [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)]中支援的版本升級案例。  
  
如需如何執行版本升級的逐步指示，請參閱[升級為不同的 SQL Server 版本 &#40;安裝程式&#41;](../../database-engine/install-windows/upgrade-to-a-different-edition-of-sql-server-setup.md)。  
  
|升級來源|升級目標|  
|------------------|----------------|  
|[!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Enterprise (Server+CAL 和 Core)**|[!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Enterprise |  
|[!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Evaluation Enterprise**|[!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Enterprise (Server+CAL 或 Core 授權) <br/><br/> [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Standard <br/> <br/> [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Developer <br/> <br/> [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Web <br/> <br/> 對於獨立安裝，支援從 Evaluation (免費版本) 升級到任何付費版本，但不支援叢集安裝。 此限制不適用於在參與可用性群組的 Windows 容錯移轉叢集上已安裝獨立執行個體。|  
|[!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Standard**|[!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Enterprise (Server+CAL 或 Core 授權)|  
|[!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Developer**|[!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Enterprise (Server+CAL 或 Core 授權) <br/><br/> [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Web <br/> <br/> [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Standard|  
|[!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Web|[!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Enterprise (Server+CAL 或 Core 授權) <br/><br/> [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Standard|  
|[!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Express*|[!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Enterprise (Server+CAL 或 Core 授權) <br/><br/> [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Developer <br/> <br/> [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Standard <br/> <br/> [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Web|  
  
 此外，您也可以在 [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Enterprise (Server+CAL 授權) 和 [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Enterprise (Core 授權) 之間執行版本升級：  
  
|升級前版本|升級後版本|  
|--------------------------|------------------------|  
|[!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Enterprise (Server+CAL 授權)**|[!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Enterprise (Core 授權)|  
|[!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Enterprise (Core 授權)|[!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Enterprise (Server+CAL 授權)|  
  
 \* 同樣適用於 [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Express with Tools 和 [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Express with Advanced Services。  
  
 **變更 [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] 容錯移轉叢集的版本有所限制。 下列狀況不支援 [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] 容錯移轉叢集：  
  
-   [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Enterprise 至 [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Developer、Standard 或 Evaluation。  
  
-   [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Developer 至 [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Standard 或 Evaluation。  
  
-   [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Standard 變更至 [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Evaluation。  
  
-   [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Evaluation 變更至 [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] Standard。  
  
## <a name="see-also"></a>另請參閱  

 [SQL Server 2017 的版本及支援功能](../../sql-server/editions-and-components-of-sql-server-2017.md)
 
 [安裝 SQL Server 的硬體與軟體需求](../../sql-server/install/hardware-and-software-requirements-for-installing-sql-server.md)   
 
 [升級 SQL Server](../../database-engine/install-windows/upgrade-sql-server.md)  
  
