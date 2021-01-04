---
title: 管理 Reporting Services SharePoint 服務應用程式 | Microsoft Docs
description: 了解如何在 SharePoint 管理中心內管理 SQL Server Reporting Services 服務應用程式。
ms.date: 10/05/2017
ms.prod: reporting-services
ms.prod_service: reporting-services-native
ms.technology: report-server-sharepoint
ms.topic: conceptual
author: maggiesMSFT
ms.author: maggies
monikerRange: '>=sql-server-2016 <=sql-server-2016'
ms.openlocfilehash: cbc258c5da9252a4463caf4b9b096c5a899d9e0a
ms.sourcegitcommit: 1a544cf4dd2720b124c3697d1e62ae7741db757c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2020
ms.locfileid: "97409684"
---
# <a name="manage-a-reporting-services-sharepoint-service-application"></a>管理 Reporting Services SharePoint 服務應用程式

[!INCLUDE[ssrs-appliesto](../../includes/ssrs-appliesto.md)] [!INCLUDE[ssrs-appliesto-2016](../../includes/ssrs-appliesto-2016.md)] [!INCLUDE[ssrs-appliesto-sharepoint-2013-2016i](../../includes/ssrs-appliesto-sharepoint-2013-2016.md)] [!INCLUDE[ssrs-appliesto-not-pbirsi](../../includes/ssrs-appliesto-not-pbirs.md)]

[!INCLUDE [ssrs-previous-versions](../../includes/ssrs-previous-versions.md)]

  [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] 服務應用程式是從 SharePoint 管理中心進行管理。 [管理] 和 [屬性] 頁面可讓您更新服務應用程式的組態以及常見的管理工作。  

> [!NOTE]
> SQL Server 2016 後即不再提供 Reporting Services 與 SharePoint 的整合。

## <a name="open-service-application-properties-page"></a>開啟服務應用程式屬性頁面

 若要開啟 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] 服務應用程式的屬性頁面，請完成下列步驟：  
  
1.  在 [管理中心] 的 [應用程式管理] 群組中，按一下 **[管理服務應用程式]** 。  
  
2.  在您的服務應用程式名稱附近按一下，或按一下 **[類型]** 資料行選取整個資料列，然後按一下 SharePoint 功能區中的 **[屬性]** 。  
  
 如需服務應用程式屬性的詳細資訊，請參閱[步驟 3：建立 Reporting Services 服務應用程式](../../reporting-services/install-windows/install-the-first-report-server-in-sharepoint-mode.md#bkmk_create_serrviceapplication)中的＜服務應用程式＞一節。  
  
## <a name="open-service-application-management-pages"></a>開啟服務應用程式管理頁面

 若要開啟 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] 服務應用程式的管理頁面，請完成下列步驟：  
  
1.  在 [管理中心] 的 [應用程式管理] 群組中，按一下 **[管理服務應用程式]** 。  
  
2.  按一下服務應用程式的名稱， **[管理 Reporting Services 應用程式]** 頁面將會開啟。  
  
3.  您也可以在名稱附近按一下，或按一下服務應用程式的 **[類型]** 資料行選取整個資料列，然後按一下 SharePoint 功能區中的 **[管理]** 。  
  
## <a name="system-settings-page"></a>系統設定頁面

 系統設定頁面可讓您設定服務應用程式的行為和使用者體驗，包括各種逾時。
  
### <a name="report-settings"></a>報表設定
  
|設定|註解|  
|-------------|--------------|  
|外部影像逾時|預設值為 600 秒。|  
|快照集壓縮|預設值是 SQL|  
|系統報表逾時|預設值是 1800 秒。<br /><br /> 指定報表伺服器上的報表處理是否在經過特定秒數之後逾時。 此值會套用至報表伺服器上的報表處理。 它不會影響提供報表資料的資料庫伺服器上的資料處理。 報表處理計時器時鐘在選取報表時會開始，而當報表開啟時就會結束。 您所指定的值應該足以完成資料處理和報表處理。|  
|系統快照集限制|預設值為 -1，表示沒有限制。<br /><br /> 設定整個網站要保留之報表記錄副本數目的預設值。 預設值會提供初始設定，以建立每個報表可儲存的快照集數目。 您可以在特定報表的屬性頁中指定不同的限制。|  
|預存參數存留期|預設值是 180|  
|預存參數臨界值|預設值為 1500 天。|  
  
### <a name="session-settings"></a>工作階段設定
  
|設定|註解|  
|-------------|--------------|  
|工作階段逾時|預設值為 600 秒。|  
|使用工作階段 Cookie|預設值是 TRUE。|  
|EDLX 報表逾時|預設值是 1800 秒。|  
  
### <a name="system-settings-for-logging"></a>用於記錄的系統設定
  
|設定|註解|  
|-------------|--------------|  
|啟用執行記錄|預設值是 TRUE。<br /><br /> 指定報表伺服器是否產生追蹤記錄以及這些記錄的保存天數。 . 記錄會儲存在報表伺服器電腦的下列資料夾中：\Microsoft SQL Server\MSSQL.n\ReportServer\Log。 每次服務重新啟動時，就會啟動新的記錄。 如需有關記錄檔的詳細資訊，請參閱＜ [Report Server Service Trace Log](../../reporting-services/report-server/report-server-service-trace-log.md)＞|  
|執行記錄保留天數|預設值是 60 天。|  
  
 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] 全都支援 SharePoint ULS 記錄。  如需詳細資訊，請參閱 [開啟 SharePoint 追蹤記錄的 Reporting Services 事件 &#40;ULS&#41;](../../reporting-services/report-server/turn-on-reporting-services-events-for-the-sharepoint-trace-log-uls.md)  
  
### <a name="security-settings"></a>安全性設定
  
|設定|註解|  
|-------------|--------------|  
|啟用整合式安全性|預設值是 TRUE。<br /><br /> 指定是否可以使用要求報表之使用者的 Windows 安全性 Token 來建立報表資料來源的連接。|  
|啟用載入報表定義|預設值是 TRUE。|  
|啟用遠端錯誤|預設值是 FALSE|  
|啟用測試連接詳細錯誤|預設值是 TRUE。|  
  
### <a name="client-settings"></a>用戶端設定
  
|設定|註解|  
|-------------|--------------|  
|啟用報表產生器下載|預設值是 TRUE。<br /><br /> 指定用戶端是否能夠看到下載報表產生器應用程式的按鈕。|  
|報表產生器啟動 URL|當報表伺服器不使用預設的報表產生器 URL 時，指定自訂 URL。 這個設定是選擇性的。 如果您沒有指定值，將會使用預設 URL，這樣會啟動報表產生器。 若要啟動報表產生器 3.0 作為 Click-Once 應用程式，請輸入下列值： https://\<computername>/ReportServer/ReportBuilder/ReportBuilder_3_0_0_0.application。|  
|啟用用戶端列印|預設值是 TRUE。<br /><br /> 指定使用者是否可以下載提供列印選項的用戶端控制項。|  
|編輯工作階段逾時|預設值是 7200 秒。|  
|編輯工作階段快取限制|預設值為 5。|  
  
## <a name="manage-jobs"></a>管理工作

 您可以檢視和刪除執行中的作業，例如報表訂閱及資料驅動訂閱所建立的作業。 此頁面並非用來管理訂閱，而是管理訂閱所觸發的作業。 例如，排程為每小時執行一次的訂閱將會每小時產生作業，該作業會出現在 **[管理作業]** 頁面上。  
  
 ![管理執行中的作業](../../reporting-services/report-server-sharepoint/media/ssrs-manage-jobs.gif "管理執行中的作業")  
  
## <a name="key-management"></a>金鑰管理
 下表摘要說明 [金鑰管理] 頁面  
  
> [!IMPORTANT]  
>  定期變更 Reporting Services 加密金鑰是安全性最佳作法。 建議您在進行 Reporting Services 的主要版本升級之後，立即變更金鑰。 在升級之後變更金鑰可將升級循環以外，由變更 Reporting Services 加密金鑰所造成的其他服務中斷減至最少。  
  
|頁面|描述|  
|----------|-----------------|  
|備份加密金鑰|1) 在 [密碼:] 與 [確認密碼:] 方塊中鍵入密碼，然後按一下 [匯出]。 如果您輸入的密碼不符合網域原則的複雜性需求，則會顯示警告。<br /><br /> 2) 系統會提示您提供儲存金鑰檔的檔案位置。 您應考慮將金鑰檔儲存到與執行 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]。 預設檔案名稱與服務應用程式的名稱相同。|  
|還原加密金鑰|1) 在 [檔案位置] 方塊中鍵入或瀏覽至金鑰檔。<br /><br /> 2) 在 [密碼] 方塊中，鍵入要用來備份加密檔案的密碼。<br /><br /> 3) 按一下 [確定]。|  
|變更加密金鑰|這項作業將建立新的金鑰，並且重新加密已加密的內容。 如果您擁有許多內容，這項作業可能需要數小時才能完成。<br /><br /> 變更加密金鑰作業完成時，建議您製作新金鑰的備份。|  
|刪除加密的內容|刪除的內容無法復原。<br /><br /> **\*\* 重要事項 \*\*** 刪除和重新建立對稱金鑰的動作無法反轉或恢復。 刪除或重新建立金鑰可能會對您目前的安裝造成重大的影響。 如果您刪除金鑰，將會一併刪除對稱金鑰所加密的任何現有資料。 刪除的資料包括外部報表資料來源的連接字串、預存連接字串和一些訂閱資訊。|  

## <a name="execution-account"></a>執行帳戶

 使用此頁面，即可設定自動處理所使用的帳戶。 當其他認證來源無法使用的特殊情況下，請使用此帳戶：  
  
-   當報表伺服器連接到不需要認證的資料來源時。 可能不需要認證的資料來源範例包括 XML 文件和某些用戶端資料庫應用程式。  
  
-   當報表伺服器連接到另一個伺服器，以擷取報表中所參考的外部影像檔或其他資源時。  

 設定這個帳戶是選擇性的，但若未設定此帳戶，當您使用外部影像及連接某些資料來源時，將會受到限制。 擷取外部影像檔時，報表伺服器會檢查是否可以建立匿名連接。 如果連接有密碼保護，報表伺服器便會使用自動報表處理帳戶連接到遠端伺服器。 擷取報表的資料時，報表伺服器會模擬目前使用者、提示使用者提供認證、使用預存認證，或如果資料來源連接指定認證類型為 **[無]** 時，則會使用自動處理帳戶。 報表伺服器不允許系統在連接到其他電腦時，委派或模擬其服務帳戶認證，因此如果沒有其他認證可以使用，報表伺服器就必須使用自動處理帳戶。  

 您所指定的帳戶應該與用於執行服務帳戶的帳戶不同。 如果您在向外延展部署中執行報表伺服器，您必須在每一部報表伺服器上使用相同的方式設定此帳戶。  

 您可以使用任何 Windows 使用者帳戶。 為求最佳效果，請選擇擁有讀取權限及網路登入權限的帳戶，以連接到其他電腦。 此帳戶必須擁有您想在報表中使用之任何外部影像或資料檔案的讀取權限。 除非所有的報表資料來源和外部影像全都儲存在報表伺服器電腦上，否則請勿指定本機帳戶。 這種帳戶只適用於自動報表處理。  

 ### <a name="powershell-command"></a>PowerShell 命令

 以下是一個 PowerShell 命令範例，會傳回具有 UEAccount 屬性的 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] 服務應用程式清單：  

```
Get-SPRSServiceApplication | select typename, name, service, ueaccountname  
```

 如需詳細資訊，請參閱 [Reporting Services SharePoint 模式的 PowerShell Cmdlet](../../reporting-services/report-server-sharepoint/powershell-cmdlets-for-reporting-services-sharepoint-mode.md)。  

### <a name="options"></a>選項。

 **指定執行帳戶**  
 選取此項目來指定帳戶。  
  
 **帳戶**  
 輸入 Windows 網域使用者帳戶。 請使用此格式：\<domain>\\<使用者帳戶\>。  
  
 **密碼**  
 輸入密碼。  
  
 **確認密碼**  
 重新輸入密碼。  

## <a name="e-mail-settings"></a>電子郵件設定

 使用此頁面，即可指定會啟用從報表伺服器傳遞報表伺服器電子郵件的 Simple Mail Transport Protocol (SMTP) 設定。 您可以使用報表伺服器電子郵件傳遞延伸模組，透過電子郵件訂閱來散發報表或報表處理通知。 報表伺服器電子郵件傳遞延伸模組，需要使用 [從:] 欄位中的 SMTP 伺服器和電子郵件地址。  

### <a name="options"></a>選項。

 **使用 SMTP 伺服器**  
 指定報表伺服器電子郵件是透過 SMTP 伺服器來傳送。  
  
 **傳出 SMTP 伺服器**  
 指定要使用的 SMTP 伺服器或閘道。 您可以在網路上使用本機伺服器或 SMTP 伺服器。  
  
 **來源位址**  
 指定用於所產生電子郵件之 [從:] 欄位中的電子郵件地址。 您必須指定有權從 SMTP 伺服器傳送郵件的使用者帳戶。  

## <a name="provision-subscriptions-and-alerts"></a>提供訂閱和警示

 使用此頁面驗證 SQL Server Agent 是否正在執行，並且提供存取權讓報表服務能夠使用 SQL Server Agent。 SQL Server Agent 為 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] 訂閱、排程和資料警示所需。 [SSRS 服務應用程式的佈建訂閱及警示](../../reporting-services/install-windows/provision-subscriptions-and-alerts-for-ssrs-service-applications.md)  

## <a name="proxy-association"></a>Proxy 關聯

 您建立 Reporting Services 服務應用程式時，會選取要產生關聯並且藉由 Reporting Services 服務應用程式提供存取權限的 Web 應用程式。 如果您選擇不產生關聯或想要變更關聯，可以使用下列步驟：  
  
1.  在 [SharePoint 管理中心] 的 [應用程式管理] 中，按一下 **[設定服務應用程式關聯]** 。  
  
2.  在 [服務應用程式關聯] 頁面上，將檢視切換至 **[服務應用程式]** 。  
  
3.  尋找並按一下新的 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] 服務應用程式的名稱。 您也可以按一下應用程式 Proxy 群組名稱 **default** ，將 Proxy 加入至預設群組，而不要完成下列步驟。  
  
4.  在 **[編輯下列連線群組]** 選取方塊中選取 **[自訂]** 。  
  
5.  核取您的 Proxy 的方塊，然後按一下 **[確定]** 。  
  
更多問題嗎？ [請嘗試詢問 Reporting Services 論壇](https://go.microsoft.com/fwlink/?LinkId=620231)
