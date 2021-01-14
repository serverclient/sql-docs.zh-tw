---
title: 訂用帳戶設定與檔案共用帳戶 (組態管理員) | Microsoft Docs
description: 使用報表伺服器組態管理員的 [訂閱設定] 頁面，為原生模式報表伺服器與檔案共用訂閱設定檔案共用帳戶。
ms.date: 12/04/2019
ms.prod: reporting-services
ms.prod_service: reporting-services-native
ms.custom: seo-lt-2019, seo-mmd-2019
ms.topic: conceptual
f1_keywords:
- SQL13.rsconfigtool.subscriptionsettings.F1
ms.assetid: fefa7bdb-b5f2-4db7-b91c-b58869279f3c
author: maggiesMSFT
ms.author: maggies
ms.openlocfilehash: 90e585e887e9415a8cf1f9d0104595547f2d1c87
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98171980"
---
# <a name="subscription-settings-and-a-file-share-account-report-server-configuration-manager"></a>訂閱設定與檔案共用帳戶 (報表伺服器組態管理員)
  使用 **Configuration Manager 的 [訂閱設定]** [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] 頁面，針對原生模式報表伺服器與檔案共用訂閱設定檔案共用帳戶。 檔案共用帳戶可讓您在將報表傳遞至檔案共用的多個訂閱中，使用單一認證組合。 當變更認證時，您可針對檔案共用帳戶設定變更，而無須更新每個個別訂閱。  
  
 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] 檔案共用訂閱具有兩個工作流程：  
  
-   您的 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 系統管理員可以使用 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] 版本的新功能，設定可供一到多個訂閱使用的單一檔案共用帳戶。 設定 [指定檔案共用帳戶]  ，然後使用者要在個別的訂閱設定頁面上，選取 [使用檔案共用帳戶]  。  
  
-   針對目的檔案共用，使用特定認證設定個別訂閱。  
  
-   您也可以混用兩種方法，讓某些檔案共用訂閱使用中央檔案共用帳戶，而其他訂閱則使用特定認證。  
  
 [!INCLUDE[applies](../../includes/applies-md.md)] [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] 原生模式。  
  
## <a name="specify-a-file-share-account"></a>指定檔案共用帳戶  
 如果選取此選項，您就可以提供帳戶，從報表伺服器存取檔案共用。 若您設定檔案共用帳戶，則所有使用者皆可針對設為傳送報表至檔案共用的任何訂閱為其選取帳戶。 若未選取此選項，則檔案共用帳戶 **不** 適用於任何訂閱。  
  
 請注意，您必須確認設定為檔案共用帳戶的帳戶，具有可讓任何檔案共用使用者用於檔案共用傳送的讀取和寫入權限。  
  
 下圖是使用者針對檔案共用傳送所設定訂閱看到的內容。 若未設定檔案共用帳戶，則會停用 [使用檔案共用帳戶]  。  
  
 ![組態管理員檔案共用帳戶](../../reporting-services/install-windows/media/ssrs-fileshare-account.png "組態管理員檔案共用帳戶")  
  
## <a name="prevent-privilege-escalation-or-elevated-privileges"></a>防止權限提升或提高權限  
  
> [!IMPORTANT]
> [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] 服務帳戶控制訂閱傳送，並會與檔案共用訂閱使用的帳戶進行互動。 Windows 安全性功能會限制以下組合：1) [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] 服務帳戶、2) 檔案共用帳戶使用的帳戶。 例如，若針對檔案共用帳戶使用內建作業系統帳戶，則 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] 服務帳戶必須是具模擬權限的另一個服務帳戶。 若設定明確的檔案共用帳戶和密碼，則檔案共用帳戶需要權限以登入執行 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] 服務的電腦。 若檔案共用帳戶不具必要權限，則使用檔案共用帳戶的訂閱將會失敗，且產生類似下列內容的錯誤訊息：  
>   
>  `"Failure writing file {file} : An impersonation error occurred using the security context of the current user."`  
  
## <a name="powershell-sample-to-audit-use-of-the-file-share-account"></a>稽核使用檔案共用帳戶的 PowerShell 範例  
 執行下列 Windows PowerShell 指令碼，以列出所有設為使用 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] 檔案共用帳戶 **的** 訂閱。 將 `SERVERNAME` 更新為報表伺服器的適當值。  
  
```  
# get all file share subscriptions using the default file share account  
$extensionNameMatch = "Report Server FileShare"  
$extensionSettingMatch = "DEFAULTCREDENTIALS"  
$valueMatch = "True"  
  
# filter for subscriptions that have a given extension setting  
filter script:extensionSettingFilter  
{  
    # subscription must match the extension name  
    if($_.DeliverySettings.Extension -eq $extensionNameMatch)  
    {  
        # locate the extension parameter of interest  
        ForEach($extensionParameter in $_.DeliverySettings.ParameterValues)  
        {  
            # if the setting has the desired value, return the subscription  
            if($extensionParameter.Name -eq $extensionSettingMatch -and $extensionParameter.Value -eq $valueMatch)  
            {  
                $_  
                break  
            }  
        }  
    }  
}  
  
$rs2010 = New-WebServiceProxy -Uri "https:// SERVERNAME/ReportServer/ReportService2010.asmx" -Namespace SSRS.ReportingService2010 -UseDefaultCredential;  
$subscriptions = $rs2010.ListSubscriptions("/");  
  
Write-Host "----- File share subscriptions using the default file share account ----";  
Write-Host "-------------------------------------------------------------------------- ";  
$subscriptions | extensionSettingFilter | select report, owner, status, lastexecuted, description, subscriptionid | format-table -auto  
  
```  
  
 指令碼的輸出內容如下所示 ︰  
  
 `----- File share subscriptions using the default file share account ----`  
  
 `-----------------------------------------------------------------------------------------------------`  
  
 `Report                    Owner          Status   LastExecuted         SubscriptionID`  
  
 `------------------------  -------------- -------- -------------------- ------------------------------------`  
  
 `Aworks_sales_by_territory DOMAIN\UserName Disabled 10/5/2014 1:04:04 PM e843bc2b-023e-45a3-ba23-22f9dc9a0934`  
  
## <a name="see-also"></a>另請參閱  
 [Reporting Services 中的檔案共用傳遞](../../reporting-services/subscriptions/file-share-delivery-in-reporting-services.md)   
 [建立及管理原生模式報表伺服器的訂閱](../../reporting-services/subscriptions/create-and-manage-subscriptions-for-native-mode-report-servers.md)
  
  
