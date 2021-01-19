---
title: SQL Server 組態管理員 | Microsoft Docs
description: 利用 SQL Server 組態管理員用戶端
ms.custom: ''
ms.date: 12/31/2019
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: vanto
ms.technology: ''
ms.topic: conceptual
helpviewer_keywords:
- protocols [SQL Server], managing
- network protocols [SQL Server], managing
- Client Network Utility
- accounts [SQL Server]
- SQL Server Configuration Manager
- Server Network Utility
- accounts [SQL Server], services
- services [SQL Server], managing
- tools [SQL Server], SQL Server Configuration Manager
- configuration manager [SQL Server]
ms.assetid: e6beaea4-164c-4078-95ae-b9e28b0aefe8
author: rothja
ms.author: jroth
ms.openlocfilehash: fc5fb052e2a02630a78f899e5dcdad615bb00266
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98172740"
---
# <a name="sql-server-configuration-manager"></a>SQL Server 組態管理員
[!INCLUDE[sqlserver](../includes/applies-to-version/sqlserver.md)]


  [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 組態管理員是一個工具，用來管理 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]的相關服務、設定 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]所用的網路通訊協定，以及管理 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 用戶端電腦的網路連接組態。 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 組態管理員會隨您的 SQL Server 一起安裝。 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 組態管理員是一個 [!INCLUDE[msCoName](../includes/msconame-md.md)] Management Console 嵌入式管理單元，您可以從 [開始] 功能表存取它，也可以將它加入任何其他 [!INCLUDE[msCoName](../includes/msconame-md.md)] Management Console 顯示畫面中。 [!INCLUDE[msCoName](../includes/msconame-md.md)] Management Console (**mmc.exe**) 使用 **SQLServerManager\<version>.msc** 檔案 (例如 [!INCLUDE[ssSQL15](../includes/sssql16-md.md)] 的 **SQLServerManager13.msc**) 開啟組態管理員。 您需要對應的 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 組態管理員，才能管理該特定版本的 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]。 以下是將 Windows 安裝在 C 磁碟機時的最近五個版本路徑。  
  
|版本|Path|  
|-|-|
|[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 2019|C:\Windows\SysWOW64\SQLServerManager15.msc| 
|[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 2017|C:\Windows\SysWOW64\SQLServerManager14.msc|  
|[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 2016|C:\Windows\SysWOW64\SQLServerManager13.msc|  
|[!INCLUDE[ssSQL14](../includes/sssql14-md.md)]|C:\Windows\SysWOW64\SQLServerManager12.msc|  
|[!INCLUDE[ssSQL11](../includes/sssql11-md.md)]|C:\Windows\SysWOW64\SQLServerManager11.msc|
  
> [!NOTE]
>  因為 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 組態管理員是 [!INCLUDE[msCoName](../includes/msconame-md.md)] 管理主控台程式的嵌入式管理單元，而不是獨立的程式，所以 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 組態管理員在較新版本的 Windows 中不會作為應用程式出現。  
> 
>  -   **Windows 10**：  
>          若要開啟 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 組態管理員，請在 **起始頁** 上輸入 SQLServerManager13.msc (適用於 [!INCLUDE[ssSQL15](../includes/sssql16-md.md)])。 針對其他版本的 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]，請以對應的數字取代 13。 按一下 SQLServerManager13.msc 即可開啟組態管理員。 若要將組態管理員釘選到起始頁或工作列，請以滑鼠右鍵按一下 SQLServerManager13.msc，然後按一下 [開啟檔案位置]。 在 Windows 檔案總管中，以滑鼠右鍵按一下 SQLServerManager13.msc，然後按一下 [釘選到 [開始] 功能表] 或 [釘選到工作列]。  
> -   **Windows 8**：  
>          若要開啟 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 組態管理員，請在 [搜尋] 常用鍵的 [應用程式] 下，輸入 **SQLServerManager\<version>.msc** (例如 **SQLServerManager13.msc**)，然後按 **Enter**。  
  
 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 組態管理員和 SQL Server Management Studio 利用 Window Management Instrumentation (WMI) 來檢視和變更部份伺服器設定。 WMI 提供統一的方式來協助您連結管理 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 工具所要求之登錄作業的 API 呼叫，在 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 組態管理員嵌入式管理單元元件的所選 SQL 服務上，它提供了增強的控制和操作功能。 如需設定 WMI 相關權限的相關資訊，請參閱[設定 WMI 在 SQL Server 工具中顯示伺服器狀態](../ssms/configure-wmi-to-show-server-status-in-sql-server-tools.md)。  
  
 若要利用 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 組態管理員來啟動、停止、暫停、繼續或設定另一部電腦中的服務，請參閱[連接至另一部電腦 &#40;SQL Server 組態管理員&#41;](../database-engine/configure-windows/scm-services-connect-to-another-computer.md)。  
  
## <a name="managing-services"></a>管理服務  
 請利用 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 組態管理員來啟動、暫停、繼續或停止服務、檢視服務屬性，或變更服務屬性。  
  
 使用 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 組態管理員利用啟動參數來啟動 [!INCLUDE[ssDE](../includes/ssde-md.md)]。  如需詳細資訊，請參閱[設定伺服器啟動選項 &#40;SQL Server 組態管理員&#41;](../database-engine/configure-windows/scm-services-configure-server-startup-options.md)。  
  
## <a name="changing-the-accounts-used-by-the-services"></a>變更服務所用的帳戶  
 利用 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 組態管理員來管理 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 服務。  
  
> [!IMPORTANT]  
>  請一律利用 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 工具 (如 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 組態管理員) 來變更 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 或 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] Agent 服務所用的帳戶，或變更帳戶的密碼。 除了變更帳戶名稱之外， [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 組態管理員也會執行其他組態，例如，設定 Windows 登錄中的權限，使新的帳戶能夠讀取 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 設定。 其他工具，例如 Windows 服務控制管理員，也能夠變更帳戶名稱，但無法變更相關設定。 如果服務無法存取登錄的 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 部份，服務可能無法適當啟動。  
  
 利用 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 組態管理員、SMO 或 WMI 來變更密碼的另一個好處是，會立即生效，不需要重新啟動服務。  
  
## <a name="manage-server--client-network-protocols"></a>管理伺服器和用戶端網路通訊協定  
 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 組態管理員可讓您設定伺服器和用戶端網路通訊協定，以及連接選項。 啟用正確的通訊協定之後，您通常不需要變更伺服器網路連接。 不過，如果您需要重新設定伺服器連接，您可以利用 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 組態管理員，使 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 利用特定的網路通訊協定、埠或管道來接聽。 如需啟用通訊協定的詳細資訊，請參閱 [啟用或停用伺服器網路通訊協定](../database-engine/configure-windows/enable-or-disable-a-server-network-protocol.md)。 如需啟用透過防火牆存取通訊協定的相關資訊，請參閱 [設定 Windows 防火牆以允許 SQL Server 存取](../sql-server/install/configure-the-windows-firewall-to-allow-sql-server-access.md)。  
  
 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 組態管理員可讓您管理伺服器和用戶端網路通訊協定，其中包括強迫加密通訊協定、檢視別名屬性，或啟用/停用通訊協定的功能。  
  
 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 組態管理員可讓您建立或移除別名、變更通訊協定的使用順序，或檢視伺服器別名的屬性，其中包括：  
  
-   伺服器別名 - 用戶端連接之電腦所用的伺服器別名。  
  
-   通訊協定 - 設定項目所用的網路通訊協定。  
  
-   連接參數 - 網路通訊協定設定之連接位址的相關參數。  
  
 另外， [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 組態管理員也可讓您檢視容錯移轉叢集執行個體的相關資訊，不過，部份啟動和停止服務之類的動作，應該使用叢集管理員。  
  
### <a name="available-network-protocols"></a>可用的網路通訊協定  
 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 支援共用記憶體、TCP/IP 與具名管道通訊協定。 如需有關選擇網路通訊協定的詳細資訊，請參閱＜ [Configure Client Protocols](../database-engine/configure-windows/configure-client-protocols.md)＞。 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 不支援 VIA、Banyan VINES Sequenced Packet Protocol (SPP)、多重通訊協定、AppleTalk 或 NWLink IPX/SPX 網路通訊協定。 先前連接這些通訊協定的用戶端必須選取不同的通訊協定，才能連接到 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]。 您不能利用 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 組態管理員來設定 WinSock Proxy。 若要設定 WinSock Proxy，請參閱您的 ISA 伺服器文件集。  
  
## <a name="related-tasks"></a>相關工作  
 [管理服務的如何主題 &#40;SQL Server 組態管理員&#41;](../database-engine/configure-windows/scm-services-connect-to-another-computer.md)  
  
 [啟動、停止、暫停、繼續、重新啟動 Database Engine、SQL Server Agent 或 SQL Server Browser 服務](../database-engine/configure-windows/start-stop-pause-resume-restart-sql-server-services.md)  
  
 [啟動、停止或暫停 SQL Server Agent 服務](../ssms/agent/start-stop-or-pause-the-sql-server-agent-service.md)  
  
 [將 SQL Server 執行個體設定為自動啟動 &#40;SQL Server 組態管理員&#41;](../database-engine/configure-windows/scm-services-set-an-instance-to-start-automatically.md)  
  
 [避免自動啟動 SQL Server 的執行個體 &#40;SQL Server 組態管理員&#41;](../database-engine/configure-windows/scm-services-prevent-automatic-startup-of-an-instance.md)  
  
