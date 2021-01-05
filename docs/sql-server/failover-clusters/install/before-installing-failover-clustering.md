---
title: 安裝容錯移轉叢集之前 | Microsoft Docs
description: 本文描述準備安裝 SQL Server 容錯移轉叢集的硬體、作業系統及組態規劃考量。
ms.custom: ''
ms.date: 08/24/2016
ms.reviewer: ''
ms.prod: sql
ms.technology: failover-cluster-instance
ms.topic: how-to
helpviewer_keywords:
- clusters [SQL Server], preinstallation checklist
- installing failover clusters
- failover clustering [SQL Server], preinstallation checklist
ms.assetid: a655225d-8c54-4b30-95fd-31f588167899
author: cawrites
ms.author: chadam
ms.openlocfilehash: a59216090f680492bf41aec72711c2e38f62034d
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97642904"
---
# <a name="before-installing-failover-clustering"></a>安裝容錯移轉叢集之前
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]
  安裝 SQL Server 容錯移轉叢集之前，您必須先選取硬體以及要執行 SQL Server 的作業系統。 您也必須設定 Windows Server 容錯移轉叢集 (WSFC)，並檢閱要在容錯移轉叢集上執行之其他軟體的網路、安全性及考量。  
  
 如果 Windows 叢集有本機磁碟機，而且同一個磁碟機代號在一個或多個叢集節點上也做為共用磁碟機使用時，您無法將 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 安裝在該磁碟機上。 這項限制適用屬於 Windows 容錯移轉叢集執行個體的伺服器上 SQL Server 容錯移轉叢集執行個體和獨立執行個體。
  
 您可能也想要檢閱下列主題，以進一步了解 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 容錯移轉叢集概念、功能及工作。  
  
|主題說明|主題|  
|-----------------------|-----------|  
|說明 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Windows 容錯移轉叢集概念，並提供連結至相關的內容及工作。|[AlwaysOn 容錯移轉叢集執行個體 &#40;SQL Server&#41;](../../../sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server.md)|  
|說明 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 容錯移轉原則概念，並提供連結設定容錯移轉原則，以符合您組織的需求。|[Failover Policy for Failover Cluster Instances](../../../sql-server/failover-clusters/windows/failover-policy-for-failover-cluster-instances.md)|  
|說明如何維護以及您現有的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 容錯移轉叢集。|[容錯移轉叢集執行個體管理及維護](../../../sql-server/failover-clusters/windows/failover-cluster-instance-administration-and-maintenance.md)|  
|說明如何在 Windows Server 容錯移轉叢集 (WSFC) 上安裝 [!INCLUDE[ssASnoversion](../../../includes/ssasnoversion-md.md)] 。|[如何將 SQL Server Analysis Services 叢集化](https://go.microsoft.com/fwlink/p/?LinkId=396548)|  
  
 
  
##  <a name="best-practices"></a><a name="BestPractices"></a> 最佳作法  
  
-   檢閱 [!INCLUDE[ssCurrent](../../../includes/sscurrent-md.md)] [版本資訊](https://go.microsoft.com/fwlink/?LinkId=296445)  
  
-   安裝必要元件軟體。 執行安裝程式來安裝或升級至 [!INCLUDE[ssCurrent](../../../includes/sscurrent-md.md)]之前，請先安裝下列必要元件以縮短安裝時間。 您可以在執行安裝程式之前，於每個容錯移轉叢集節點上安裝必要元件軟體，然後重新啟動節點一次。  
  
    -   [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 安裝程式將不再安裝 Windows PowerShell。 Windows PowerShell 是安裝 [!INCLUDE[ssCurrent](../../../includes/sscurrent-md.md)][!INCLUDE[ssDE](../../../includes/ssde-md.md)] 元件和 [!INCLUDE[ssManStudioFull](../../../includes/ssmanstudiofull-md.md)]的必要條件。 如果您的電腦沒有 Windows PowerShell，可以遵循 [Windows Management Framework](https://go.microsoft.com/fwlink/?LinkId=186214) 頁面上的指示啟用此元件。  
  
    -   [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 安裝程式不再安裝 .NET Framework 3.5 SP1，但是在較舊的 Windows 作業系統上安裝 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 時，可能需要 .NET Framework 3.5 SP1。 如需詳細資訊，請參閱 [!INCLUDE[ssCurrent](../../../includes/sscurrent-md.md)][版本資訊](https://go.microsoft.com/fwlink/?LinkId=296445)。  
  
    -   **[!INCLUDE[msCoName](../../../includes/msconame-md.md)] 更新封裝：** 為避免在安裝程期間電腦因安裝 .NET Framework 4 而重新啟動，[!INCLUDE[ssCurrent](../../../includes/sscurrent-md.md)] 安裝程式要求電腦上必須已安裝 [!INCLUDE[msCoName](../../../includes/msconame-md.md)] 更新。  如果您是在 Windows 7 SP1 或 [!INCLUDE[ssSQL14](../../../includes/sssql14-md.md)] SP2 上安裝 [!INCLUDE[firstref_longhorn](../../../includes/firstref-longhorn-md.md)] ，則會包含這項更新。 如果您是在較舊的 Windows 作業系統上安裝，請從 [Windows Vista 和 Windows Server 2008 上之 .NET Framework 4.0 適用的 Microsoft Update](https://go.microsoft.com/fwlink/?LinkId=198093)下載此更新。  
  
    -   .NET Framework 4：安裝程式會在叢集作業系統上安裝 .NET Framework 4。 若要縮短安裝時間，您可以考慮在執行安裝程式前先安裝 .NET Framework 4。  
  
    -   [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 安裝程式支援檔案。 您可以執行位於 [!INCLUDE[ssCurrent](../../../includes/sscurrent-md.md)] 安裝媒體上的 SqlSupport.msi 以安裝這些檔案。  
  
-   確認在 WSFC 叢集上未安裝防毒軟體。 如需詳細資訊，請參閱 [!INCLUDE[msCoName](../../../includes/msconame-md.md)] 知識庫文件： [防毒軟體可能會導致叢集服務發生問題](https://go.microsoft.com/fwlink/?LinkId=116986)。  
  
-   安裝容錯移轉叢集期間為叢集群組命名時，不可以在叢集群組名稱中使用下列任何字元：  
  
    -   小於運算子 (\<)  
  
    -   大於運算子 (>)  
  
    -   雙引號 (")  
  
    -   單引號 (')  
  
    -   連字號 (&)  
  
     此外，請確認現有的叢集群組名稱沒有包含不支援的字元。  
  
-   確定所有叢集節點已設定相同，包括 COM+、磁碟機代號，以及管理員群組中的使用者。  
  
-   驗證您已經清除了所有節點上的系統記錄檔，並已經再次檢視過系統記錄檔。 請在繼續下個步驟之前，確認記錄檔完全沒有任何錯誤訊息。  
  
-   在安裝或更新 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 容錯移轉叢集之前，請停用所有可能在安裝期間使用 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 元件的應用程式和服務，但請將磁碟資源保持為線上的狀態。  
  
-   [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 安裝程式會自動設定 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 叢集群組與位於容錯移轉叢集中之磁碟之間的相依性。 請勿在安裝之前設定磁碟的相依性。  
  
    -   在 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 容錯移轉叢集安裝期間，將會建立 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 網路資源名稱的電腦物件 (Active Directory 電腦帳戶)。 在 [!INCLUDE[firstref_longhorn](../../../includes/firstref-longhorn-md.md)] 叢集中，叢集名稱帳戶 (叢集本身的電腦帳戶) 需要擁有建立電腦物件的權限。 如需詳細資訊，請參閱＜ [容錯移轉叢集逐步指南：設定 Active Directory 中的帳戶](https://technet.microsoft.com/library/cc731002\(WS.10\).aspx)＞。  
  
    -   如果您使用 SMB 檔案共用做為儲存選項， [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 安裝帳戶必須具有檔案伺服器的 SeSecurityPrivilege。 若要執行這項操作，請使用檔案伺服器上的 [本機安全性原則] 主控台，將 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 安裝帳戶加入 **[管理稽核及安全性記錄]** 權限。  
  
##  <a name="verify-your-hardware-solution"></a><a name="Hardware"></a> 驗證硬體方案  
  
-   如果叢集方案包括位於不同地點的叢集節點，則必須驗證如網路延遲及共用磁碟支援等其他項目。  
  
    -   如需有關 [!INCLUDE[firstref_longhorn](../../../includes/firstref-longhorn-md.md)] 和 [!INCLUDE[winserver2008r2](../../../includes/winserver2008r2-md.md)]的詳細資訊，請參閱＜ [為容錯移轉叢集驗證硬體](https://go.microsoft.com/fwlink/?LinkId=196817) ＞和＜ [Windows 容錯移轉叢集的支援原則](https://go.microsoft.com/fwlink/?LinkId=196818)＞。  
  
-   確認要安裝 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 的磁碟未壓縮或加密。 如果您嘗試將 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 安裝到壓縮的磁碟機或加密的磁碟機，則 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 安裝程式會失敗。  
  
-   [!INCLUDE[firstref_longhorn](../../../includes/firstref-longhorn-md.md)] 和 [!INCLUDE[winserver2008r2](../../../includes/winserver2008r2-md.md)] Advanced Server 及 Datacenter Server Edition 也支援 SAN 組態。 「Windows Catalog 與硬體相容性清單」的類別目錄 "Cluster/Multi-cluster Device" 會列出一組具有 SAN 功能的儲存裝置，這些裝置已通過測試，並支援可附加多個 WSFC 叢集的 SAN 儲存單位。 尋找認證的元件後，請執行叢集驗證。  
  
-   另外也支援用於安裝資料檔案的 SMB 檔案共用。 如需詳細資訊，請參閱 [資料檔案的儲存類型](../../../sql-server/install/hardware-and-software-requirements-for-installing-sql-server.md#StorageTypes)。  
  
    > [!WARNING]  
    >  如果您使用 Windows 檔案伺服器做為 SMB 檔案共用儲存，則 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 安裝帳戶必須具有檔案伺服器的 SeSecurityPrivilege。 若要執行這項操作，請使用檔案伺服器上的 [本機安全性原則] 主控台，將 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 安裝帳戶加入 **[管理稽核及安全性記錄]** 權限。  
    >   
    >  如果您要使用 Windows 檔案伺服器以外的 SMB 檔案共用儲存，請洽詢儲存廠商，了解檔案伺服器端的對等設定。  
  
-   [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 支持掛接點。  
  
     掛接的磁碟區 (或掛接點) 可讓您使用單一磁碟機代號來代表許多磁碟或磁碟區。 如果您以磁碟機代號 D: 來代表一般的磁碟或磁碟區，則您可以連接或「掛載」額外的磁碟或磁碟區為磁碟機代號 D: 下的目錄，而不需每一個磁碟或磁碟區都有自己的磁碟機代號。  
  
     [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 容錯移轉叢集的其他掛載點考量：  
  
    -   [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 安裝程式需要已掛載磁碟機的基底磁碟機具有連接的磁碟機代號。 若為容錯移轉叢集安裝，這個基底磁碟機必須是叢集磁碟機。 這個版本不支援磁碟區 GUID。  
  
    -   含磁碟機代號的基底磁碟機不能在容錯移轉叢集執行個體之間共用。 這是容錯移轉叢集的正常限制，但不會限制獨立和多執行個體的伺服器。  
  
    -   [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 的叢集安裝乃受限於可用的磁碟機代號。 假設您針對作業系統只使用一個磁碟機代號，而且其他所有磁碟機代號都可做為正常叢集磁碟機或主控掛載點的叢集磁碟機，則限制為每一容錯移轉叢集最多可有 25 個 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體。  
  
        > [!TIP]  
        >  可藉由使用 SMB 檔案共享選項來克服 25 個執行個體的限制。 如果您使用 SMB 檔案共用來當成儲存選項，您可以安裝高達 50 個 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 容錯移轉叢集執行個體。  
  
    -   不支援在裝載其他磁碟機之後格式化磁碟機。  
  
-   [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 容錯移轉叢集安裝只有在安裝 tempdb 檔時支援本機磁碟。 務必確定在所有叢集節點上為 tempdb 資料和記錄檔指定的路徑都是有效的。 在容錯移轉期間，如果容錯移轉目標節點上的 tempdb 目錄無法使用，則 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 資源將無法上線。 如需詳細資訊，請參閱 [資料檔案的儲存類型](../../../sql-server/install/hardware-and-software-requirements-for-installing-sql-server.md#StorageTypes) 和 [Database Engine 組態 - 資料目錄](https://msdn.microsoft.com/library/9b1fa0fc-623b-479a-afc3-4f13bd850487)。  
  
-   如果您在 iSCSI 技術元件上部署 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 容錯移轉叢集，我們建議您小心進行。 如需詳細資訊，請參閱＜ [iSCSI 技術元件對於 SQL Server 的支援](https://go.microsoft.com/fwlink/?LinkId=116960)＞(機器翻譯)。  
  
-   如需詳細資訊，請參閱＜ [Microsoft 叢集的 SQL Server 支援原則](https://go.microsoft.com/fwlink/?LinkId=116958)＞(機器翻譯)。  
  
-   如需有關適當仲裁磁碟機組態的詳細資訊，請參閱＜ [仲裁磁碟機組態資訊](https://go.microsoft.com/fwlink/?LinkId=196816)＞(機器翻譯)。  
  
-   若要在 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 來源安裝檔案與叢集位於不同網域時安裝 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 容錯移轉叢集，請將安裝檔案複製到可供 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 容錯移轉叢集使用的目前網域。  
  
##  <a name="review-security-considerations"></a><a name="Security"></a> 預覽安全考量  
  
-   若要使用加密，請利用 WSFC 叢集的完整 DNS 名稱，在 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 容錯移轉叢集中的所有節點上安裝伺服器憑證。 例如，如果您具有兩個節點的叢集 (節點名稱為 "Test1.DomainName.com" 及 "Test2.DomainName.com")，以及 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 容錯移轉叢集執行個體 (名稱為 "Virtsql")，則您必須取得 "Virtsql.DomainName.com" 的憑證，並將憑證安裝在 test1 及 test2 節點上。 接著，您可以選取「 **組態管理員」中的** [強制通訊協定加密] [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 核取方塊，為您的容錯移轉叢集設定加密。  
  
    > [!IMPORTANT]  
    >  除非您已將憑證安裝在容錯移轉叢集執行個體的所有參與節點上，否則請勿選取 **[強制通訊協定加密]** 核取方塊。  
  
-   對於與舊版並存組態的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 安裝而言， [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 服務必須使用只存在於全域網域群組中的帳戶。 此外， [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 服務所使用的帳戶不能出現在本機的管理員群組中。 如果沒有遵照這項指導方針進行，將會導致非預期的安全性行為。  
  
-   若要建立容錯移轉叢集，您必須是擁有下列權限的本機管理員：能夠以服務登入，而且能夠做為容錯移轉叢集執行個體之所有節點上的作業系統的一部分。  
  
-   在 [!INCLUDE[nextref_longhorn](../../../includes/nextref-longhorn-md.md)]上，系統會自動產生可搭配 [!INCLUDE[ssCurrent](../../../includes/sscurrent-md.md)] 服務使用的服務 SID。 如果是從舊版 [!INCLUDE[ssCurrent](../../../includes/sscurrent-md.md)] 升級的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]容錯移轉叢集執行個體，就會保留現有的網域群組和 ACL 組態。  
  
-   網域群組必須與電腦帳戶位在相同的網域內。 例如，如果安裝 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 的電腦是在屬於 MYDOMAIN 子系的 SQLSVR 網域內，則必須指定 SQLSVR 網域中的群組。 SQLSVR 網域可能會包含 MYDOMAIN 中的使用者帳戶。  
  
-   [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 容錯移轉叢集。  
  
-   檢閱 [Security Considerations for a SQL Server Installation](../../../sql-server/install/security-considerations-for-a-sql-server-installation.md)中的內容。  
  
-   若要以 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]啟用 Kerberos 驗證，請參閱 [知識庫中的](https://support.microsoft.com/kb/319723) 如何使用 SQL Server Kerberos 驗證 [!INCLUDE[msCoName](../../../includes/msconame-md.md)] (機器翻譯)。  

-   SQL Server 容錯移轉叢集執行個體 (FCI) 需要已加入網域的叢集節點。 **不支援** 下列設定： 
    *   工作群組叢集上的 SQL FCI。 
    *   多網域叢集上的 SQL FCI。   
    *   網域 + 工作群組叢集上的 SQL FCI。 

  
##  <a name="review-network-port-and-firewall-considerations"></a><a name="Network"></a> 預覽網絡、通訊埠和防火牆的考量  
  
-   開始 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 安裝程式之前，請確認您已停用所有私人網路卡的 NetBIOS。  
  
-   您的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 的網路名稱及 IP 位址不應該用於任何其他目的，如檔案共用。 如果您想要建立檔案共用資源，請對資源使用不同且唯一網路名稱及 IP 位址。  
  
    > [!IMPORTANT]  
    >  建議您不要在資料磁碟機上使用檔案共用，因為它們可能影響 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 的行為及效能。  
  
-   即使 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 可透過叢集內的 TCP/IP 同時支援具名管道及 TCP/IP 通訊端，但仍然建議您在叢集組態中使用 TCP/IP 通訊端。  
  
-   請注意，Windows 叢集不支援 ISA 伺服器，因此 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 容錯移轉叢集也不支援它。  
  
-   遠端登錄服務必須正常執行。  
  
-   遠端管理必須已啟用。  
  
- 針對使用非預設連接埠的 SQL Server 執行個體，請使用 SQL Server 組態管理員的網路組態，來為您要解除封鎖的 SQL Server 執行個體決定要使用的連接埠。 如果您想要使用 [SQL Server Browser 服務](../../../tools/configuration-manager/sql-server-browser-service.md) (使用不同於叢集執行個體與 UDP 連接埠 1434 的 IP 位址) 連線到 SQL Server 執行個體，請在防火牆中為 IPALL 啟用 TCP 連接埠。 
  
-   容錯移轉叢集安裝程式作業包括檢查網路連結順序的規則。 雖然連結順序看起來可能是正確的，但是您可能已停用或「準刪除」系統上的 NIC 組態。 「準刪除」NIC 組態可能會影響連結順序，而且會導致連結順序規則發出警告。 若要避免這種情況，請使用下列步驟來識別並移除已停用的網路介面卡：  
  
    1.  在命令提示字元中，輸入：set devmgr_Show_Nonpersistent_Devices=1。  
  
    2.  輸入並執行：start Devmgmt.msc。  
  
    3.  展開網路介面卡的清單。 只有實體介面卡才應該位於此清單中。 如果您擁有已停用的網路介面卡，安裝程式就會針對網路連結順序規則報告失敗。 [控制台]/[網路連線] 也會顯示此介面卡已停用。 確認 [控制台] 中的 [網路設定] 與 devmgmt.msc 顯示相同的已啟用實體配接器清單。  
  
    4.  在您執行 SQL Server 安裝程式之前，請移除已停用的網路介面卡。  
  
    5.  在安裝程式完成之後，請返回 [控制台] 中的 [網路連線] 並停用目前非使用中的任何網路介面卡。  
  
##  <a name="verify-your-operating-system"></a><a name="OS_Support"></a> 驗證您的作業系統  
 確定您已正確安裝作業系統，而且其設計可支援容錯移轉叢集。 下表是 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 版本及支援這些版本之作業系統的清單。  
  
|[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 版本|[!INCLUDE[firstref_longhorn](../../../includes/firstref-longhorn-md.md)] Enterprise|[!INCLUDE[firstref_longhorn](../../../includes/firstref-longhorn-md.md)] Datacenter Server|[!INCLUDE[winserver2008r2](../../../includes/winserver2008r2-md.md)] Enterprise|[!INCLUDE[winserver2008r2](../../../includes/winserver2008r2-md.md)] Datacenter Server|  
|---------------------------------------|------------------------------------------------|-------------------------------------------------------|----------------------------------------------|-----------------------------------------------------|  
|[!INCLUDE[ssSQL14](../../../includes/sssql14-md.md)] Enterprise (64 位元) x64*|是|是|是**|是**|  
|[!INCLUDE[ssSQL14](../../../includes/sssql14-md.md)] Enterprise (32 位元)|是|是|||  
|[!INCLUDE[ssCurrent](../../../includes/sscurrent-md.md)] Developer (64 位元)|是|是|是**|是**|  
|[!INCLUDE[ssCurrent](../../../includes/sscurrent-md.md)] Developer (32 位元)|是|是|||  
|[!INCLUDE[ssCurrent](../../../includes/sscurrent-md.md)] Standard (64 位元)|是|是|是|是|  
|[!INCLUDE[ssCurrent](../../../includes/sscurrent-md.md)] Standard (32 位元)|是|是|||  
  
 *[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] WOW 模式不支援叢集。 其中包括來自原先安裝在 WOW 中之舊版 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 容錯移轉叢集的升級。 對於這些版本而言，唯一的升級選項就是並存安裝新的版本並進行移轉。  
  
 **支援 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 多重子網路容錯移轉叢集。  
  
##  <a name="additional-considerations-for-multi-subnet-configurations"></a><a name="MultiSubnet"></a> 多重子網路組態的其他考量  
 安裝 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 多重子網路容錯移轉叢集時，請將下列章節描述的需求謹記在心。 一個多重子網路設定涉及了多重子網路間的叢集，因此涉及使用多重 IP 位址，及改變 IP 位址資源的相依性。  
  
### <a name="ssnoversion-edition-and-operating-system-considerations"></a>[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 版本與作業系統考量  
  
-   如需支援 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 多重子網路容錯移轉叢集之 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 版本的資訊，請參閱 [SQL Server 2016 版本支援的功能](~/sql-server/editions-and-supported-features-for-sql-server-2016.md)。  
  
-   若要建立 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 多重子網路容錯移轉叢集，您必須先在多重子網路上建立 [!INCLUDE[winserver2008r2](../../../includes/winserver2008r2-md.md)] 多站台容錯移轉叢集。  
  
-   [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 容錯移轉叢集相依於 Windows 伺服器容錯移轉叢集，因此如果有容錯移轉時，請確定 IP 相依性條件是有效的。  
  
-   [!INCLUDE[winserver2008r2](../../../includes/winserver2008r2-md.md)] 需要所有的叢集伺服器都要在相同的 Active Directory 網域。 因此， [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 即使叢集節點存在於不同的子網路中，多重子網路容錯移轉叢集仍需要所有的叢集節點都位於相同的 Active Directory 網域。  
  
#### <a name="ip-address-and-ip-address-resource-dependencies"></a>IP 位址和 IP 位址資源相依性  
  
1.  在一個多重子網路設定中，IP 位址資源相依性乃設為 OR。 如需詳細資訊，請參閱[建立新的 SQL Server 容錯移轉叢集 &#40;安裝程式&#41;](../../../sql-server/failover-clusters/install/create-a-new-sql-server-failover-cluster-setup.md)  
  
2.  不支援混合的 AND-OR IP 位址相依性。 例如，不支援 \<IP1> 及 \<IP2> 或 \<IP3>。  
  
3.  不支援每個子網路有多個 IP 位址。  
  
     如果您決定為相同的子網路設定超過一個 IP 位址，您可能會在 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 啟動時，遇到用戶端連接失敗。  
  
#### <a name="related-content"></a>相關內容  
 如需 [!INCLUDE[winserver2008r2](../../../includes/winserver2008r2-md.md)] 多站台容錯移轉的詳細資訊，請參閱 [Failover Clusters in Windows Server 2008 R2](https://technet.microsoft.com/library/ff182338\(v=WS.10\).aspx) (Windows Server 2008 R2 的容錯移轉叢集) 和 [Design for a Clustered Service or Application in a Multi-Site Failover Cluster](https://go.microsoft.com/fwlink/?LinkId=177873)(針對多站台容錯移轉叢集中的叢集服務或應用程式進行設計)。  
  
##  <a name="configure-windows-server-failover-cluster"></a><a name="WSFC"></a> 設定 Windows Server 容錯移轉叢集  
  
-   [!INCLUDE[msCoName](../../../includes/msconame-md.md)] 您至少必須在伺服器叢集的一個節點上設定 Cluster Service (WSFC)。 您也必須結合 WSFC 一併執行 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Enterprise、 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Business Intelligence 或 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Standard。 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Enterprise 最多支援含有 16 個節點的容錯移轉叢集。 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Business Intelligence 和 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Standard 支援兩個節點的容錯移轉叢集。  
  
-   [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 服務的資源 DLL 會匯出兩個函數，可讓 WSFC 叢集管理員用來檢查 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 資源的可用性。 如需詳細資訊，請參閱 [容錯移轉叢集執行個體的容錯移轉原則](../../../sql-server/failover-clusters/windows/failover-policy-for-failover-cluster-instances.md)。  
  
-   WSFC 必須能夠使用 IsAlive 檢查，驗證容錯移轉叢集執行個體是否正在執行中。 這需要使用信任連接來連接到伺服器。 根據預設，系統不會將執行叢集服務的帳戶設定為叢集中節點上的管理員，而且 BUILTIN\Administrators 群組沒有登入 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]的權限。 只有當您變更叢集節點上的權限時，這些設定才會變更。  
  
-   設定網域名稱服務 (DNS) 或 Windows 網際網路名稱服務 (WINS)。 DNS 伺服器或 WINS 伺服器必須要在安裝 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 容錯移轉叢集的環境中執行。 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 安裝程式需要 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] IP 介面虛擬參考的動態網域名稱服務註冊。 DNS 伺服器組態應該要允許叢集節點動態登錄對應至網路名稱的線上 IP 位址。 如果無法完成動態註冊，安裝程式會失敗，而且會回復安裝。 如需詳細資訊，請參閱＜ [此知識庫文件](https://support.microsoft.com/kb/947048)＞(機器翻譯)。  
  
##  <a name="install-msconame-distributed-transaction-coordinator"></a><a name="MSDTC"></a> 安裝 [!INCLUDE[msCoName](../../../includes/msconame-md.md)] 分散式交易協調器  
 在容錯移轉叢集上安裝 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 之前，請判斷是否必須建立 [!INCLUDE[msCoName](../../../includes/msconame-md.md)] 分散式交易協調器 (MSDTC) 叢集資源。 如果您只要安裝 [!INCLUDE[ssDE](../../../includes/ssde-md.md)]，則不需要 MSDTC 叢集資源。 如果您要安裝 [!INCLUDE[ssDE](../../../includes/ssde-md.md)] 和 SSIS 或工作站元件，或是將要使用分散式交易，則必須安裝 MSDTC。 請注意，MSDTC 並非僅限 [!INCLUDE[ssASnoversion](../../../includes/ssasnoversion-md.md)]執行個體的必要項目。  
  
 在 [!INCLUDE[firstref_longhorn](../../../includes/firstref-longhorn-md.md)] 和 [!INCLUDE[winserver2008r2](../../../includes/winserver2008r2-md.md)]中，您可以在單一的容錯移轉叢集上安裝 MSDTC 的多個執行個體。 第一個安裝的 MSDTC 執行個體將會是 MSDTC 的叢集預設執行個體。 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 將會透過自動使用 MSDTC 執行個體的方式，利用已安裝到 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 本機叢集資源群組的 MSDTC 執行個體。 但是，個別的應用程式可以對應到叢集上的任何 MSDTC 執行個體。  
  
 下列規則會套用至由 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]選擇的 MSDTC 執行個體：  
  
-   使用已安裝到本機群組的 MSDTC，或  
  
-   使用對應的 MSDTC 執行個體，或  
  
-   使用叢集的預設 MSDTC 執行個體，或  
  
-   使用安裝在本機電腦的 MSDTC 執行個體  
  
> [!IMPORTANT]  
>  如果已安裝至 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 本機叢集群組的 MSDTC 執行個體失敗， [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 並不會自動嘗試使用預設叢集執行個體或本機電腦的 MSDTC 執行個體。 您需要從 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 群組完全移除失敗的 MSDTC 執行個體，才能使用其他的 MSDTC 執行個體。 同樣地，如果您建立 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 的對應，而對應的 MSDTC 執行個體失敗，您的分散式交易也將失敗。 如果您希望 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 使用不同的 MSDTC 執行個體，就必須將 MSDTC 執行個體加入到 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 的本機叢集群組或刪除對應。  
  
### <a name="configure-msconame-distributed-transaction-coordinator"></a>設定 [!INCLUDE[msCoName](../../../includes/msconame-md.md)] 分散式交易協調器  
 安裝作業系統並設定叢集之後，您必須使用「叢集管理員」來設定 MSDTC 以搭配叢集使用。 如果無法將 MSDTC 設定為搭配叢集使用，也不會封鎖 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 安裝程式，但如果沒有正確設定 MSDTC，則 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 應用程式功能可能會受到影響。  
  
## <a name="see-also"></a>另請參閱  
 [安裝 SQL Server 2016 的硬體與軟體需求](../../../sql-server/install/hardware-and-software-requirements-for-installing-sql-server.md)   
 [檢查 System Configuration Checker 的參數](../../../database-engine/install-windows/check-parameters-for-the-system-configuration-checker.md)   
 [容錯移轉叢集執行個體管理及維護](../../../sql-server/failover-clusters/windows/failover-cluster-instance-administration-and-maintenance.md)  
  
  

