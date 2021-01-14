---
description: 將其他報表伺服器加入至伺服器陣列 (SSRS 向外延展)
title: 將其他報表伺服器新增至伺服器陣列 (SSRS 向外延展) | Microsoft Docs
ms.date: 05/30/2017
ms.prod: reporting-services
ms.prod_service: reporting-services-sharepoint
ms.topic: conceptual
ms.assetid: c1a6b683-15cf-44ae-ac60-ceee63a60aaf
author: maggiesMSFT
ms.author: maggies
monikerRange: '>=sql-server-2016 <=sql-server-2016'
ms.openlocfilehash: e0f1807d8050408ca7e6153d9f7b9189b004bc8a
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98101969"
---
# <a name="add-an-additional-report-server-to-a-farm-ssrs-scale-out"></a>將其他報表伺服器加入至伺服器陣列 (SSRS 向外延展)

  在 SharePoint 伺服器陣列中加入第二個或更多的 SharePoint 模式報表伺服器，可以改善報表伺服器的處理效能和回應時間。 如果您發現新增更多使用者、報表和其他應用程式到報表伺服器時效能降低了，那麼新增額外的報表伺服器可以改善效能。 此外，當硬體發生問題或者您在環境中的個別伺服器上執行一般維護作業時，也建議您加入第二個報表伺服器以提高報表伺服器的可用性。 從 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 版開始，在 SharePoint 模式中向外延展 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] 環境的步驟會遵循標準 SharePoint 伺服器陣列部署，並且運用 SharePoint 負載平衡功能。  
  
> [!IMPORTANT]  
>  並非所有 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] 版本都支援向外延展 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]。 如需詳細資訊，請參閱 [SQL Server 版本支援的功能](~/sql-server/editions-and-components-of-sql-server-2017.md#SSRS)的＜[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]＞一節。  
  
> [!TIP]  
>  從 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 版開始，您不會使用 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] 組態管理員來加入伺服器以及向外延展報表伺服器。 SharePoint 產品會在具有 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] 服務的 SharePoint 伺服器加入至伺服器陣列時，管理向外延展 Reporting Services。  
  
 如需如何向外延展原生模式報表伺服器的資訊，請參閱[設定原生模式報表伺服器向外延展部署 &#40;報表伺服器組態管理員&#41;](../../reporting-services/install-windows/configure-a-native-mode-report-server-scale-out-deployment.md)。  
  
##  <a name="load-balancing"></a><a name="bkmk_loadbalancing"></a> 負載平衡  
 除非您的環境具有自訂或協力廠商負載平衡解決方案，否則 SharePoint 將自動管理 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] 服務應用程式的負載平衡。 預設的 SharePoint 負載平衡行為是，每個 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] 服務應用程式都將在您已經啟動 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] 服務的所有應用程式伺服器之間取得平衡。 若要確認 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] 服務是否已安裝並啟動，請按一下 SharePoint 管理中心內的 **[管理伺服器上的服務]** 。  
  
##  <a name="prerequisites"></a><a name="bkmk_prerequisites"></a> 必要條件  
  
-   您必須是本機系統管理員，才能執行 SQL Server 安裝程式。  
  
-   電腦必須加入網域。  
  
-   您必須知道裝載 SharePoint 組態和內容資料庫之現有資料庫伺服器的名稱。  
  
-   資料庫伺服器必須設定為允許遠端資料庫連接。  如果不允許，您就無法將新的伺服器加入伺服器陣列，因為新的伺服器將無法建立 SharePoint 組態資料庫的連接。  
  
-   新的伺服器必須安裝目前伺服器陣列伺服器所執行的相同 SharePoint 版本。 例如，如果伺服器陣列已經安裝 SharePoint 2013 Service Pack 1 (SP1)，您也必須在新的伺服器上安裝 SP1，才能讓它加入伺服器陣列。  
  
##  <a name="steps"></a><a name="bkmk_steps"></a> 步驟  
 本主題中的步驟假設 SharePoint 伺服器陣列管理員要安裝及設定伺服器。 下圖顯示一般三層環境，而且下圖中的編號項目將於下列清單中描述：  
  
-   (1) 多個 Web 前端 (WFE) 伺服器。 WFE 伺服器需要適用於 SharePoint 2016 的 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] 增益集。  
  
-   (2) 執行 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] 和網站的單一應用程式伺服器，例如管理中心。 下列步驟會將第二個應用程式伺服器加入至這一層。  
  
-   (3) 兩個 SQL Server 資料庫伺服器。  
  
-   (4) 代表軟體或硬體網路負載平衡解決方案 (NLB)。  
  
 ![新增 Reporting Services 應用程式伺服器](../../reporting-services/install-windows/media/rs-sharepointscale.gif "新增 Reporting Services 應用程式伺服器")  
  
 下列步驟假設管理員要安裝及設定伺服器。 此伺服器將在伺服器陣列中設定為新的應用程式伺服器，而且不會當做 Web 前端 (WFE) 使用。  
  
|步驟|說明和連結|  
|----------|--------------------------|  
|將 SharePoint 伺服器加入伺服器陣列中。|您必須安裝 SharePoint 以部署其他 Reporting Services 應用程式。<br/><br/>若是 SharePoint 2013，請參閱 [在 SharePoint Server 2013 中將 SharePoint 伺服器加入伺服陣列](/SharePoint/install/add-web-or-application-server-to-the-farm)(在 SharePoint Server 2013 中將 SharePoint 伺服器加入伺服陣列)。<br/><br/>若是 SharePoint 2016，請參閱 [在 SharePoint Server 2016 中將 SharePoint 伺服器加入伺服陣列](/SharePoint/install/add-a-server-to-a-sharepoint-server-2016-farm)(在 SharePoint Server 2016 中將 SharePoint 伺服器加入伺服陣列)。|  
|安裝和設定 Reporting Services SharePoint 模式。|執行 SQL Server 安裝。 如需安裝 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] SharePoint 模式的詳細資訊，請參閱[以 SharePoint 模式安裝第一部報表伺服器](install-the-first-report-server-in-sharepoint-mode.md)<br /><br /> 如果伺服器只會當作應用程式伺服器使用，而不會當作 WFE 使用，您就不需要選取 [適用於 SharePoint 產品的 Reporting Services 增益集]  。<br /><br /> 1) 在 [安裝程式角色]  頁面上，選取 [SQL Server 功能安裝] <br /><br /> 2) 在 [功能選擇]  頁面上，選取 [Reporting Services - SharePoint] <br /><br /> 3) 在 [Reporting Services 設定] 頁面上，確認已針對 [Reporting Services SharePoint 模式] 選取 [只安裝] 選項。|  
|確認 Reporting Services 可以運作。|1) 在 SharePoint 管理中心內，按一下 [系統設定] 群組中的 [管理此伺服器陣列中的伺服器]。<br /><br /> 2) 確認 [SQL Server Reporting Services 服務]  正在執行。<br /><br />如需詳細資訊，請參閱＜ [Verify a Reporting Services Installation](../../reporting-services/install-windows/verify-a-reporting-services-installation.md)＞|  
  
##  <a name="additional-configuration"></a><a name="bkmk_additional"></a> 其他組態  
 您可以將向外延展部署中的個別 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] 伺服器最佳化為僅執行背景處理，如此伺服器就不會與互動報表執行作業競用資源。 背景處理包括排程、訂閱和資料警示。  
  
 若要變更個別報表伺服器的行為，請將 **RSreportServer.config** 組態檔中的 **\<IsWebServiceEnable>** 設為 false。  
  
 根據預設，報表伺服器的 \<IsWebServiceEnable> 會設為 TRUE。 當所有伺服器都設定為 TRUE 時，伺服器陣列中所有節點的互動和背景就能達到負載平衡。  
  
 如果將所有報表伺服器的 \<IsWebServiceEnable> 設為 False，則在嘗試使用 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] 功能時，會看見類似下列的錯誤訊息：  
  
```output
The Reporting Services Web Service is not enabled. Configure at least one instance of the Reporting Services SharePoint Service to have <IsWebServiceEnable> set to true.
```
 
 如需詳細資訊，請參閱[修改 Reporting Services 組態檔 &#40;RSreportserver.config&#41;](../../reporting-services/report-server/modify-a-reporting-services-configuration-file-rsreportserver-config.md)  

## <a name="next-steps"></a>後續步驟

[將 SharePoint 伺服器加入至 SharePoint Server 2016 中的伺服器陣列](/SharePoint/install/add-a-server-to-a-sharepoint-server-2016-farm)  
[將 SharePoint 伺服器加入至 SharePoint Server 2016 中的伺服器陣列](/SharePoint/install/add-web-or-application-server-to-the-farm)

更多問題嗎？ [請嘗試詢問 Reporting Services 論壇](https://go.microsoft.com/fwlink/?LinkId=620231)