---
title: SCM 服務 - 變更服務啟動帳戶 | Microsoft Docs
description: 了解如何變更 SQL Server 和其許多服務使用的服務帳戶。 檢視服務帳戶中變更的限制。
ms.custom: ''
ms.date: 01/06/2016
ms.prod: sql
ms.prod_service: high-availability
ms.reviewer: ''
ms.technology: configuration
ms.topic: conceptual
helpviewer_keywords:
- SQL Server services, startup account changes
- startup accounts [SQL Server]
- changing startup accounts for services
ms.assetid: d721c796-0397-46a7-901b-1a9a3c3fb385
author: markingmyname
ms.author: maghan
ms.openlocfilehash: 8f5b17129d2fc8115ce976a7acac6dd429c28407
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98170990"
---
# <a name="scm-services---change-the-service-startup-account"></a>SCM 服務 - 變更服務啟動帳戶
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
  本主題描述如何使用 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 組態管理員來變更 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 服務的啟動選項，以及變更 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Browser、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] 和 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 所使用的服務帳戶。 在 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 中，使用 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)]、 [!INCLUDE[tsql](../../includes/tsql-md.md)]或 PowerShell。 如需如何選取適合的服務帳戶的詳細資訊，請參閱 [設定 Windows 服務帳戶和權限](../../database-engine/configure-windows/configure-windows-service-accounts-and-permissions.md)。  
  
> [!IMPORTANT]  
>  當您變更 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 和 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent 的服務啟動帳戶時，必須重新啟動 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 服務 ( [!INCLUDE[ssDE](../../includes/ssde-md.md)])，才能讓變更生效。 重新啟動服務時，除非服務順利重新啟動，否則所有與該 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體相關的資料庫都會無法使用。 如果您必須變更 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 或 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent 的服務啟動帳戶，請確定您在定期排程的維護期間進行，或在可讓資料庫離線，而不會中斷每日作業時進行。  
  
##  <a name="before-you-begin"></a><a name="BeforeYouBegin"></a> 開始之前  
  
###  <a name="limitations-and-restrictions"></a><a name="Restrictions"></a> 限制事項  
  
-   叢集伺服器  
  
     您必須從 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 叢集的使用中節點執行變更 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 或 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent 所使用之服務帳戶的作業。  
  
     在 Windows Server 2008 上執行 (使用網域群組，採用非預設組態) 時，變更 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 或 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent 所使用的服務帳戶需要使用 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 組態管理員，讓資源群組離線來停止 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。  
  
-   SKU 升級 ([!INCLUDE[ssExpress](../../includes/ssexpress-md.md)] 升級為非 Express SKU)  
  
     在 [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)] 安裝期間， [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent 服務會設定成使用網路服務帳戶，但是它已停用。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 組態管理員可以變更指派給 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent 服務的帳戶，但是您無法啟用或啟動此服務。 在 SKU 從 [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)] 升級為非 Express 之後，雖然不會自動啟用 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent 服務，但是您可以使用 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 組態管理員並將服務啟動模式變更為「手動」或「自動」，在需要時啟用此服務。  
  
##  <a name="using-sql-server-configuration-manager"></a><a name="SSMSProcedure"></a> 使用 SQL Server 組態管理員  
  
#### <a name="to-change-the-sql-server-service-startup-account"></a>變更 SQL Server 服務啟動帳戶  
  
1.  指向 **[開始]** 功能表上的 **[所有程式]** ，然後依序指向 [ [!INCLUDE[ssCurrentUI](../../includes/sscurrentui-md.md)]] 和 **[組態工具]** ，再按一下 **[SQL Server 組態管理員]** 。  
  
    > [!NOTE]  
    >  因為 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 組態管理員是 [!INCLUDE[msCoName](../../includes/msconame-md.md)] 管理主控台程式的嵌入式管理單元，而不是獨立的程式，所以 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 組態管理員在較新版本的 Windows 中不會作為應用程式出現。  
    >   
    >  -   **Windows 10**：  
    >          若要開啟 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 組態管理員，請在 **起始頁** 上輸入 SQLServerManager13.msc (適用於 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)])。 若為舊版 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ，則以較小的數字取代 13。 按一下 SQLServerManager13.msc 即可開啟組態管理員。 若要將組態管理員釘選到起始頁或工作列，請以滑鼠右鍵按一下 SQLServerManager13.msc，然後按一下 [開啟檔案位置]。 在 Windows 檔案總管中，以滑鼠右鍵按一下 SQLServerManager13.msc，然後按一下 [釘選到 [開始] 功能表] 或 [釘選到工作列]。  
    > -   **Windows 8**：  
    >          若要開啟 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 組態管理員，請在 [搜尋] 常用鍵的 [應用程式]下，輸入 **SQLServerManager\<version>.msc** (例如 **SQLServerManager13.msc**)，然後按 **Enter**。  
  
2.  在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 組態管理員中，按一下 **[SQL Server 服務]** 。  
  
3.  在詳細資料窗格中，以滑鼠右鍵按一下您要變更服務啟動帳戶的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體名稱，然後按一下 [屬性]。  
  
4.  在 [SQL Server \<**_instancename_**> 屬性] 對話方塊中，按一下 [登入] 索引標籤，然後選取 [登入身分] 帳戶類型。  
  
5.  選取新的服務啟動帳戶之後，請按一下 **[確定]** 。  
  
     此時會出現一個訊息方塊，詢問您是否想要重新啟動 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 服務。  
  
6.  按一下 **[是]** ，然後關閉 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 組態管理員。  
  
## <a name="see-also"></a>另請參閱  
 [啟動、停止、暫停、繼續、重新啟動 Database Engine、SQL Server Agent 或 SQL Server Browser 服務](../../database-engine/configure-windows/start-stop-pause-resume-restart-sql-server-services.md)   
 [設定 WMI 在 SQL Server 工具中顯示伺服器狀態](../../ssms/configure-wmi-to-show-server-status-in-sql-server-tools.md)  
  
  
