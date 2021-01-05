---
title: 建立新的容錯移轉叢集執行個體
description: 本文說明如何使用安裝程式來安裝或升級 SQL Server Always On 容錯移轉叢集執行個體，或將節點新增至現有的容錯移轉叢集執行個體。
ms.custom: seo-lt-2019
ms.date: 12/13/2019
ms.reviewer: ''
ms.prod: sql
ms.technology: failover-cluster-instance
ms.topic: how-to
helpviewer_keywords:
- adding nodes
- failover clustering [SQL Server], creating clusters
- nodes [Faillover Clustering], removing
- nodes [Faillover Clustering], adding
- clusters [SQL Server], creating
- removing nodes
ms.assetid: 30e06a7d-75e9-44e2-bca3-b3b0c4a33f61
author: cawrites
ms.author: chadam
ms.openlocfilehash: 841644a1429b4c143b7f2b31b5d6ec669bb0ff58
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97642847"
---
# <a name="create-a-new-always-on-failover-cluster-instance-setup"></a>建立新的 Always On 容錯移轉叢集執行個體 (安裝程式)

[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]

  若要安裝或升級 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 容錯移轉叢集執行個體 (FCI)，您必須在基礎 Windows Server 容錯移轉叢集的每個節點上執行安裝程式。 若要將節點新增至現有的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 容錯移轉叢集執行個體，您必須在要新增至 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 容錯移轉叢集執行個體的節點上執行 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 安裝程式。 請勿在使用中節點上執行安裝程式來管理其他節點。  
  
 視節點叢集的方式而定，[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 容錯移轉叢集執行個體會以下列方式設定：  
  
-   相同子網路上的節點或子網路的相同集合 - 針對這些組態類型，IP 位址資源相依性會設定為 AND。  
  
-   在不同子網路上的節點 - IP 位址資源相依性會設定為 OR，這個設定稱為 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 多重子網路容錯移轉叢集執行個體設定。 如需詳細資訊，請參閱 [SQL Server 多重子網路叢集 &#40;SQL Server&#41;](../../../sql-server/failover-clusters/windows/sql-server-multi-subnet-clustering-sql-server.md)。  
  
 下列選項適用於 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 容錯移轉叢集安裝：  
  
 **選項 1：整合式安裝與新增節點**   
  
 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 整合式容錯移轉叢集安裝包含以下步驟：  
  
-   建立並設定單一節點的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 容錯移轉叢集執行個體。 當您成功地設定節點時，您就擁有可完整運作的容錯移轉叢集執行個體。 此時不具備高可用性，因為容錯移轉叢集執行個體中只有一個節點。  
  
-   在要新增至 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 容錯移轉叢集執行個體的每個節點上，使用「新增節點」功能執行安裝程式，以便新增該節點。  
  
    -   如果您要加入的節點有其他或不同的子網路，安裝程式可讓您指定其他 IP 位址。 如果您要加入的節點位於不同的子網路上，您也需要確認 IP 位址資源相依性變更為 OR。 若要深入了解新增節點作業期間可能的不同案例，請參閱[在 Always On 容錯移轉叢集執行個體中新增或移除節點 (安裝程式)](../../../sql-server/failover-clusters/install/add-or-remove-nodes-in-a-sql-server-failover-cluster-setup.md)。  
  
 **選項 2：進階/企業型安裝**  
  
 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 進階/企業型容錯移轉叢集安裝包含以下步驟：  
  
-   在新 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 容錯移轉叢集可能的擁有者之每一個節點上，遵循＜ [準備](#prepare)＞一節所列的「準備容錯移轉叢集」安裝步驟。 當您在某個節點上執行「準備容錯移轉叢集」之後，安裝程式就會建立 Configuration.ini 檔案，這個檔會列出您所指定的所有設定。 在要準備的其他節點上，您可以提供第一個節點中自動產生的 Configuration.ini 檔案當做安裝程式命令列的輸入，而不必遵循這些步驟。 如需詳細資訊，請參閱 [使用組態檔安裝 SQL Server 2016](../../../database-engine/install-windows/install-sql-server-2016-using-a-configuration-file.md)。 雖然這個步驟會準備即將建立叢集的節點，不過在這個步驟結束時，不會提供任何可運作的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體。  
  
-   節點已備妥且可供叢集後，請在備妥的其中一個節點上執行安裝程式。 這個步驟會設定並完成容錯移轉叢集執行個體。 當這個步驟結束時，您將會擁有可操作的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 容錯移轉叢集執行個體，而且之前為該執行個體備妥的所有節點都可能是新建的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 容錯移轉叢集執行個體的擁有者。  
  
     如果您建立跨多個子網路的容錯移轉叢集執行個體，安裝程式將會偵測跨越所有節點之所有子網路的聯集，而這些節點都會具有 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 備妥的容錯移轉叢集執行個體。 您將可以為子網路指定多個 IP 位址。 每個備妥的節點都至少必須是一個 IP 位址的可能擁有者。  
  
     如果所有備妥的節點上都可支援針對子網路指定的每個 IP 位址，相依性會設定為 AND。  
  
     如果不是所有備妥的節點上都可支援針對子網路指定的每個 IP 位址，相依性會設定為 OR。 如需詳細資訊，請參閱 [SQL Server 多重子網路叢集 &#40;SQL Server&#41;](../../../sql-server/failover-clusters/windows/sql-server-multi-subnet-clustering-sql-server.md)。  
  
    > [!NOTE]  
    >  完整容錯移轉叢集會要求基礎 Windows Server 容錯移轉叢集必須存在。 如果 Windows Server 容錯移轉叢集不存在，安裝程式將會提供錯誤並結束。  
  
 如需如何在現有容錯移轉叢集執行個體中加入或移除節點的詳細資訊，請參閱[在 Always On 容錯移轉叢集執行個體中新增或移除節點 (安裝程式)](../../../sql-server/failover-clusters/install/add-or-remove-nodes-in-a-sql-server-failover-cluster-setup.md)。  
  
 如需遠端安裝的詳細資訊，請參閱[支援的版本與版本升級](../../../database-engine/install-windows/supported-version-and-edition-upgrades.md)。  
  
 如需有關在 WSFC 中安裝 [!INCLUDE[ssASnoversion](../../../includes/ssasnoversion-md.md)] 的詳細資訊，請參閱[如何將 SQL Server Analysis Services 叢集化](https://go.microsoft.com/fwlink/p/?LinkId=396548)。  
  
## <a name="prerequisites"></a>必要條件  
 開始之前，請先檢視下列《 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 線上叢書》主題：  
  
-   [規劃 SQL Server 安裝](../../../sql-server/install/planning-a-sql-server-installation.md)  
  
-   [安裝容錯移轉叢集之前](../../../sql-server/failover-clusters/install/before-installing-failover-clustering.md)  
  
-   [SQL Server 安裝的安全性考量](../../../sql-server/install/security-considerations-for-a-sql-server-installation.md)  
  
-   [SQL Server 多重子網路叢集 &#40;SQL Server&#41;](../../../sql-server/failover-clusters/windows/sql-server-multi-subnet-clustering-sql-server.md)  
  
> [!NOTE]  
>  執行 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 安裝程式之前，請記下叢集管理員中的共用磁碟機位置。 您必須擁有這項資訊，才能建立新的容錯移轉叢集執行個體。  
  
### <a name="to-install-a-new-ssnoversion-failover-cluster-instance-using-integrated-install-with-add-node"></a>使用整合式安裝與「新增節點」來安裝新的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 容錯移轉叢集執行個體。  
  
1.  插入 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 安裝媒體，然後在根資料夾中，按兩下 Setup.exe。 若要從網路共用區進行安裝，請瀏覽到共用區上的根資料夾，然後按兩下 Setup.exe。 如需有關如何安裝必要元件的詳細資訊，請參閱＜ [Before Installing Failover Clustering](../../../sql-server/failover-clusters/install/before-installing-failover-clustering.md)＞。  
  
2.  安裝精靈會啟動 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 安裝中心。 若要建立新的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 叢集安裝，請在安裝頁面上按一下 新的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] [容錯移轉叢集安裝]。  
  
3.  系統組態檢查會在電腦上執行探索作業。 若要繼續，請[!INCLUDE[clickOK](../../../includes/clickok-md.md)]。 您可以按一下 **[顯示詳細資料]** 在畫面上檢視詳細資料，或是按一下 **[檢視詳細資料報表]** 來以 HTML 報表形式檢視詳細資料。  
  
4.  若要繼續進行，請按 **[下一步]** 。  
  
5.  在 [安裝程式支援檔案] 頁面上，按一下 **[安裝]** ，即可安裝安裝程式支援檔案。  
  
6.  系統組態檢查將會先確認電腦的系統狀態，然後安裝程式才會繼續進行。 檢查完成之後，請按 **[下一步]** 繼續進行。 您可以按一下 **[顯示詳細資料]** 在畫面上檢視詳細資料，或是按一下 **[檢視詳細資料報表]** 來以 HTML 報表形式檢視詳細資料。  
  
7.  在 [產品金鑰] 頁面上，指出您要安裝免費的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]版本，還是您擁有產品之實際執行版本的 PID 金鑰。 如需詳細資訊，請參閱 [SQL Server 2016 的版本和元件](../../../sql-server/editions-and-components-of-sql-server-2016.md)。  
  
8.  在 [授權條款] 頁面上，閱讀授權合約，然後選取要接受授權條款和條件的核取方塊。 若要協助提升 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]，您也可以啟用功能使用方式選項，並傳送報告給 [!INCLUDE[msCoName](../../../includes/msconame-md.md)]。 選取 [下一步] 以繼續操作。 若要結束安裝程式，請按一下 **[取消]** 。  
  
9. 在 [特徵選取] 頁面上，選取要安裝的元件。 當您選取功能名稱之後，每一個元件群組的描述就會出現在右窗格中。 雖然您可以選取任何核取方塊的組合，但是只有表格式模式中的 [!INCLUDE[ssDE](../../../includes/ssde-md.md)]和 [!INCLUDE[ssASnoversion](../../../includes/ssasnoversion-md.md)] 及多維度模式中的 [!INCLUDE[ssASnoversion](../../../includes/ssasnoversion-md.md)] 支援容錯移轉叢集。 其他選取的元件將會當做獨立功能來執行，而在您執行安裝程式的目前節點上則沒有任何容錯移轉功能。 如需 [!INCLUDE[ssASnoversion](../../../includes/ssasnoversion-md.md)] 節點的詳細資訊，請參閱 [判斷 Analysis Services 執行個體的伺服器模式](https://docs.microsoft.com/analysis-services/instances/determine-the-server-mode-of-an-analysis-services-instance)。  
  
     右窗格會顯示選取功能的必要條件。 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 安裝程式將會在這個程序稍後說明的安裝步驟期間安裝尚未安裝的必要條件。  
  
     您可以使用此頁面底部的欄位來指定共用元件的自訂目錄。 若要變更共用元件的安裝路徑，請在對話方塊底部提供的欄位中更新路徑，或是按一下省略符號按鈕，導覽至安裝目錄。 預設安裝路徑為 C:\Program Files\\[!INCLUDE[msCoName](../../../includes/msconame-md.md)][!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] \\。  
  
     [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 也支援在伺服器訊息區塊 (SMB) 檔案共用上安裝系統資料庫 (Master、Model、MSDB 和 TempDB) 與 [!INCLUDE[ssDE](../../../includes/ssde-md.md)] 使用者資料庫。 如需使用 SMB 檔案共用安裝 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 作為儲存體的詳細資訊，請參閱 [將 SQL Server 與 SMB Fileshare 當作儲存選項一起安裝](../../../database-engine/install-windows/install-sql-server-with-smb-fileshare-as-a-storage-option.md)。  
  
     共用元件的指定路徑必須是絕對路徑。 資料夾不可以為壓縮或加密資料夾。 不支援使用對應磁碟機。 如果您在 64 位元的作業系統上安裝 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] ，將會看到下列選項：  
  
    1.  共用功能目錄  
  
    2.  共用功能目錄 (x86)  
  
     針對上述每個選項所指定的路徑必須是不同的。  
  
    > [!NOTE]  
    >  當您選取 [!INCLUDE[ssDE](../../../includes/ssde-md.md)] Services 功能時，將會自動選取複寫和全文檢索搜尋。 當您選取 [!INCLUDE[ssDE](../../../includes/ssde-md.md)] Services 功能時，也會選取 Data Quality Services (DQS)。 取消選取任何子功能也會取消選取 [!INCLUDE[ssDE](../../../includes/ssde-md.md)] Services 功能。  
  
10. [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 安裝程式會依據您選取的功能執行一組額外的規則，以驗證您的組態。  
  
11. 在 [執行個體組態] 頁面上，請指定要安裝預設或具名執行個體。 如需詳細資訊，請參閱＜ [Instance Configuration](../../install/instance-configuration.md)＞。  
  
     **[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]網路名稱** - 針對新的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 容錯移轉叢集執行個體指定網路名稱。 這是在網路上用來識別容錯移轉叢集執行個體的名稱。  
  
    > [!NOTE]  
    >  這在舊版的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 容錯移轉叢集執行個體中稱為虛擬 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 名稱。  
  
     **執行個體識別碼** ：依預設，此執行個體名稱會當作執行個體識別碼使用。 這是用來識別 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]執行個體的安裝目錄和登錄機碼。 這是預設執行個體和具名執行個體的狀況。 如果是預設執行個體，執行個體名稱和執行個體識別碼將會是 MSSQLSERVER。 若要使用非預設的執行個體識別碼，請選取 **[執行個體識別碼]** 方塊並提供值。  
  
    > [!NOTE]  
    >  標準的獨立 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體 (不論是預設還是具名執行個體) 都不會針對 [執行個體識別碼] 方塊使用非預設的值。  
  
     **執行個體根目錄** - 依預設，執行個體根目錄為 C:\Program Files\\[!INCLUDE[msCoName](../../../includes/msconame-md.md)][!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]\\。 若要指定非預設的根目錄，請使用提供的欄位，或是按一下省略符號按鈕找出安裝資料夾。  
  
     **在這部電腦上偵測到 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體和功能** - 此方格會顯示執行安裝程式之電腦上的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體。 如果預設執行個體已經安裝在電腦上，您就必須安裝 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]的具名執行個體。 選取 [下一步] 以繼續操作。  
  
12. 使用 [叢集資源群組] 頁面，可指定即將放置 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 虛擬伺服器資源的叢集資源群組 (或角色) 名稱。 若要指定 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 叢集資源群組名稱，您有兩個選擇：  
  
    -   使用下拉式方塊來指定要使用的現有群組。  
  
    -   輸入要建立之新群組的名稱。 請注意，"Available storage" 名稱不是有效的群組名稱。  
  
13. 在 [叢集磁碟選取] 頁面上，選取 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 容錯移轉叢集執行個體的共用叢集磁碟資源。 叢集磁碟就是即將放置 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 資料的位置。 可以指定一個以上的磁碟。 [可用共用磁碟] 方格會顯示可用磁碟的清單、每個磁碟是否具有成為共用磁碟的資格，以及每個磁碟資源的描述。 選取 [下一步] 以繼續操作。  
  
    > [!NOTE]  
    >  第一個磁碟機會當做所有資料庫的預設磁碟機使用，但是可以在 [!INCLUDE[ssDE](../../../includes/ssde-md.md)] 或 [!INCLUDE[ssASnoversion](../../../includes/ssasnoversion-md.md)] 組態頁面上變更。  
    >   
    >  在 [叢集磁碟選取] 頁面上，如果您要使用 SMB 檔案共用做為資料夾，可以選擇性地略過選取任何共用磁碟。  
  
14. 在 [叢集網路組態] 頁面上，指定容錯移轉叢集執行個體的網路資源：  
  
    -   **網路設定** ：指定容錯移轉叢集執行個體的 IP 類型和 IP 位址。  
  
     選取 [下一步] 以繼續操作。  
  
15. 使用此頁面來指定叢集安全性原則。  
  
    -   [!INCLUDE[firstref_longhorn](../../../includes/firstref-longhorn-md.md)] 及更新版本 - 服務 SID (伺服器安全性識別碼) 是建議的設定，也是預設設定。 沒有任何將此設定變更為安全性群組的選項。 如需 [!INCLUDE[nextref_longhorn](../../../includes/nextref-longhorn-md.md)]上服務 SID 功能的資訊，請參閱 [設定 Windows 服務帳戶與權限](../../../database-engine/configure-windows/configure-windows-service-accounts-and-permissions.md)。 這已經在 [!INCLUDE[winserver2008r2](../../../includes/winserver2008r2-md.md)]的獨立和叢集設定中測試過。  
  
    -   在 [!INCLUDE[winxpsvr](../../../includes/winxpsvr-md.md)]上，請指定 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 服務的網域群組。 所有資源權限都是由包含 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 服務帳戶當做群組成員的網域層級群組所控制。  
  
     選取 [下一步] 以繼續操作。  
  
16. 本主題其餘部分的工作流程會因您針對安裝所指定的功能而不同。 您可能不會看到所有頁面，端視您的選取項目而定 ([!INCLUDE[ssDE](../../../includes/ssde-md.md)]、 [!INCLUDE[ssASnoversion](../../../includes/ssasnoversion-md.md)]和 [!INCLUDE[ssRSnoversion](../../../includes/ssrsnoversion-md.md)])。  
  
17. 在 [伺服器設定 - 服務帳戶] 頁面中，指定 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 服務的登入帳戶。 在這個頁面上所設定的實際服務隨著您選取要安裝的功能而不同。  
  
     您可以將相同登入帳戶指派給所有 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 服務，或個別設定每一個服務帳戶。 啟動類型針對所有可識別叢集服務是設為手動，其中包括全文檢索搜尋及 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 代理程式，並且在安裝期間無法進行變更。 [!INCLUDE[msCoName](../../../includes/msconame-md.md)] 建議您個別設定服務帳戶，以針對各項服務提供最低權限，其中 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 服務會授與必須完成工作的最小權限。 如需詳細資訊，請參閱 [伺服器組態 - 服務帳戶](https://msdn.microsoft.com/library/c283702d-ab20-4bfa-9272-f0c53c31cb9f) 和 [設定 Windows 服務帳戶與權限](../../../database-engine/configure-windows/configure-windows-service-accounts-and-permissions.md)。  
  
     若要針對此 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]執行個體中的所有服務帳戶指定相同的登入帳戶，請在頁面底部的欄位中提供認證。  
  
     **安全性注意事項** [!INCLUDE[ssNoteStrongPass](../../../includes/ssnotestrongpass-md.md)]  
  
     當您完成針對 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 服務指定登入資訊之後，請按 **[下一步]** 。  
  
18. 使用 [伺服器組態 - 定序] 索引標籤，為 [!INCLUDE[ssDE](../../../includes/ssde-md.md)] 和 [!INCLUDE[ssASnoversion](../../../includes/ssasnoversion-md.md)] 指定非預設的定序。  
  
19. 使用 [!INCLUDE[ssDE](../../../includes/ssde-md.md)] [組態 - 帳戶提供] 頁面來指定以下項目：  
  
    -   安全性模式 - 為 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]執行個體選取 Windows 驗證或混合模式驗證。 如果您選取混合模式驗證，就必須為內建 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 系統管理員帳戶提供增強式密碼。  
  
         當裝置與 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]建立成功的連接之後，Windows 驗證和混合模式的安全性機制是相同的。  
  
    -   [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 管理員 - 您在 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]執行個體上至少必須指定一個系統管理員。 若要加入 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 安裝程式執行所用的帳戶，請按一下 **[加入目前使用者]** 。 若要從系統管理員清單中加入或移除帳戶，請按一下 **[加入]** 或 **[移除]** ，然後編輯在 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]執行個體中將會有管理員權限的使用者、群組或電腦清單。  
  
     當您完成清單的編輯之後，請 [!INCLUDE[clickOK](../../../includes/clickok-md.md)]。 然後，在組態對話方塊中確認管理員的清單。 當此清單完成時，請按 **[下一步]** 。  
  
20. 使用 [!INCLUDE[ssDE](../../../includes/ssde-md.md)] [組態 - 資料目錄] 頁面來指定非預設的安裝目錄。 若要安裝到預設目錄，請按 **[下一步]** 。  
  
    > [!IMPORTANT]  
    >  如果您要指定非預設的安裝目錄，請確定安裝資料夾對於此 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]執行個體是唯一的。 此對話方塊上的任何目錄都不應該與其他 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]執行個體中的目錄共用。 資料目錄應位於容錯移轉叢集執行個體的共用叢集磁碟上。  
  
    > [!NOTE]  
    >  若要指定伺服器訊息區塊 (SMB) 檔案伺服器作為資料目錄，請將 [預設資料根目錄] 設為 \\\伺服器名稱\共用名稱\\... 格式的檔案共用  
   
21. 使用 [!INCLUDE[ssDE](../../../includes/ssde-md.md)] [組態 - FILESTREAM] 頁面來針對 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]執行個體啟用 FILESTREAM。 選取 [下一步] 以繼續操作。  
  
22. 使用 [[!INCLUDE[ssASnoversion](../../../includes/ssasnoversion-md.md)] 組態 - 帳戶提供] 頁面來指定即將擁有 [!INCLUDE[ssASnoversion](../../../includes/ssasnoversion-md.md)] 之管理員權限的使用者或帳戶。 您至少必須針對 [!INCLUDE[ssASnoversion](../../../includes/ssasnoversion-md.md)]指定一個系統管理員。 若要加入 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 安裝程式執行所用的帳戶，請按一下 **[加入目前使用者]** 。 若要在系統管理員清單中加入或移除帳戶，請按一下 **[加入]** 或 **[移除]** ，然後編輯在 [!INCLUDE[ssASnoversion](../../../includes/ssasnoversion-md.md)]中將會有管理員權限的使用者、群組或電腦清單。
  
     當您完成清單的編輯之後，請 [!INCLUDE[clickOK](../../../includes/clickok-md.md)]。 然後，在組態對話方塊中確認管理員的清單。 當此清單完成時，請按 **[下一步]** 。  
  
23. 使用 [!INCLUDE[ssASnoversion](../../../includes/ssasnoversion-md.md)] [組態 - 資料目錄] 頁面來指定非預設的安裝目錄。 若要安裝到預設目錄，請按 **[下一步]** 。  
  
    > [!IMPORTANT]  
    >  如果您要指定非預設的安裝目錄，請確定安裝資料夾對於此 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]執行個體是唯一的。 此對話方塊上的任何目錄都不應該與其他 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]執行個體中的目錄共用。 資料目錄應位於容錯移轉叢集執行個體的共用叢集磁碟上。  
   
24. 使用[!INCLUDE[ssRSnoversion](../../../includes/ssrsnoversion-md.md)] [組態] 頁面來指定要建立的 [!INCLUDE[ssRSnoversion](../../../includes/ssrsnoversion-md.md)] 安裝類型。 如果是容錯移轉叢集執行個體安裝，此選項會設定為未設定的 [!INCLUDE[ssRSnoversion](../../../includes/ssrsnoversion-md.md)] 安裝。 在您完成安裝之後，必須設定 [!INCLUDE[ssRSnoversion](../../../includes/ssrsnoversion-md.md)] 服務。  
  
 
25. 系統組態檢查將會執行一組額外的規則，以便使用您已指定的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 功能來驗證組態。  
  
26. [準備安裝] 頁面會顯示在安裝期間指定之安裝選項的樹狀檢視。 若要繼續，請按一下 **[安裝]** 。 安裝程式會先安裝選取功能所需的必要條件，之後再進行功能安裝。  
  
27. 在安裝期間，[安裝進度] 頁面會提供狀態，好讓您可以在安裝程式進行時監視安裝進度。  
  
28. 安裝之後， **[完成]** 頁面會提供安裝和其他重要注意事項之摘要記錄檔的連結。 若要完成 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 安裝程序，請按一下 **[關閉]** 。  
  
29. 如果指示您重新啟動電腦，請立刻執行。 當您完成安裝時，請務必閱讀安裝精靈所提供的訊息。 如需安裝程式記錄檔的資訊，請參閱 [檢視與讀取 SQL Server 安裝程式記錄檔](../../../database-engine/install-windows/view-and-read-sql-server-setup-log-files.md)。  
  
30. 若要將節點加入到您剛才建立的單一節點容錯移轉叢集中，請在每一個額外的節點上執行安裝程式，並遵循 AddNode 作業的步驟。 如需詳細資訊，請參閱[在 Always On 容錯移轉叢集執行個體中新增或移除節點 (安裝程式)](../../../sql-server/failover-clusters/install/add-or-remove-nodes-in-a-sql-server-failover-cluster-setup.md)。  
  
    > [!NOTE]  
    >  如果您要加入一個以上的節點，可以使用組態檔來部署安裝。 如需詳細資訊，請參閱 [使用組態檔安裝 SQL Server 2016](../../../database-engine/install-windows/install-sql-server-2016-using-a-configuration-file.md)。  
    >   
    >  在 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 容錯移轉叢集執行個體的所有節點中，您都必須安裝相符的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 版本。 當您將新的節點新增至現有的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 容錯移轉叢集執行個體時，請確定您有指定此版本與現有容錯移轉叢集執行個體的版本相符。  
  
##  <a name="prepare"></a><a name="prepare"></a> 準備  
  
#### <a name="advancedenterprise-failover-cluster-instance-install-step-1-prepare"></a>進階/企業型容錯移轉叢集執行個體安裝步驟 1：準備  
  
1.  插入 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 安裝媒體，然後在根資料夾中，按兩下 Setup.exe。 若要從網路共用區進行安裝，請瀏覽到共用區上的根資料夾，然後按兩下 Setup.exe。 如需有關如何安裝必要元件的詳細資訊，請參閱＜ [Before Installing Failover Clustering](../../../sql-server/failover-clusters/install/before-installing-failover-clustering.md)＞。 如果您之前未安裝必要元件，系統可能會要求您安裝。  
  
2.  Windows Installer 4.5 是必要的元件，而且安裝精靈可能會安裝此元件。 如果系統提示您重新啟動電腦，請重新啟動，然後再次啟動 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 安裝程式。  
  
3.  安裝必要元件之後，安裝精靈將會啟動 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 安裝中心。 若要準備要建立叢集的節點，請移到 **[進階]** 頁面，然後按一下 **[進階叢集準備]** 。  
  
4.  系統組態檢查會在電腦上執行探索作業。 若要繼續，請[!INCLUDE[clickOK](../../../includes/clickok-md.md)]。 您可以按一下 **[顯示詳細資料]** 在畫面上檢視詳細資料，或是按一下 **[檢視詳細資料報表]** 來以 HTML 報表形式檢視詳細資料。  
  
5.  在 [安裝程式支援檔案] 頁面上，按一下 **[安裝]** ，即可安裝安裝程式支援檔案。  
  
6.  系統組態檢查將會先確認電腦的系統狀態，然後安裝程式才會繼續進行。 檢查完成之後，請按 **[下一步]** 繼續進行。 您可以按一下 **[顯示詳細資料]** 在畫面上檢視詳細資料，或是按一下 **[檢視詳細資料報表]** 來以 HTML 報表形式檢視詳細資料。  
  
7.  如果您在當地語系化的作業系統上安裝，而且安裝媒體包含英文以及與作業系統對應之語言的語言套件，您便可以在 [語言選擇] 頁面上指定 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體的語言。 如需跨語言支援和安裝考量的詳細資訊，請參閱 [SQL Server 中的地區語言版本](../../../sql-server/install/local-language-versions-in-sql-server.md)。  
  
     若要繼續進行，請按 **[下一步]** 。  
  
8.  在 [產品金鑰] 頁面上，按一下來指出您要安裝免費的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]版本，還是您擁有產品之實際執行版本的 PID 金鑰。 如需詳細資訊，請參閱 [SQL Server 2016 的版本和元件](../../../sql-server/editions-and-components-of-sql-server-2016.md)。  
  
    > [!NOTE]  
    >  您必須在您為相同容錯移轉叢集執行個體準備的所有節點上指定相同的產品金鑰。  
  
9. 在 [授權條款] 頁面上，閱讀授權合約，然後選取要接受授權條款和條件的核取方塊。 若要協助提升 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]，您也可以啟用功能使用方式選項，並傳送報告給 [!INCLUDE[msCoName](../../../includes/msconame-md.md)]。 選取 [下一步] 以繼續操作。 若要結束安裝程式，請按一下 **[取消]** 。  
  
10. 在 [特徵選取] 頁面上，選取要安裝的元件。 當您選取功能名稱之後，每一個元件群組的描述就會出現在右窗格中。 雖然您可以選取任何核取方塊的組合，但是只有表格式模式中的 [!INCLUDE[ssDE](../../../includes/ssde-md.md)]和 [!INCLUDE[ssASnoversion](../../../includes/ssasnoversion-md.md)] 及多維度模式中的 [!INCLUDE[ssASnoversion](../../../includes/ssasnoversion-md.md)] 支援容錯移轉叢集。 其他選取的元件將會當做獨立功能來執行，而在您執行安裝程式的目前節點上則沒有任何容錯移轉功能。 如需 [!INCLUDE[ssASnoversion](../../../includes/ssasnoversion-md.md)] 節點的詳細資訊，請參閱 [判斷 Analysis Services 執行個體的伺服器模式](https://docs.microsoft.com/analysis-services/instances/determine-the-server-mode-of-an-analysis-services-instance)。  
  
     右窗格會顯示選取功能的必要條件。 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 安裝程式將會在這個程序稍後說明的安裝步驟期間安裝尚未安裝的必要條件。  
  
     您可以使用此頁面底部的欄位來指定共用元件的自訂目錄。 若要變更共用元件的安裝路徑，請在對話方塊底部提供的欄位中更新路徑，或是按一下省略符號按鈕，導覽至安裝目錄。 預設安裝路徑為 C:\Program Files\\[!INCLUDE[msCoName](../../../includes/msconame-md.md)][!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]\\。  
  
    > [!NOTE]  
    >  當您選取 [!INCLUDE[ssDE](../../../includes/ssde-md.md)] Services 功能時，將會自動選取複寫和全文檢索搜尋。 取消選取任何子功能也會取消選取 [!INCLUDE[ssDE](../../../includes/ssde-md.md)] Services 功能。  
  
11. 在 [執行個體組態] 頁面上，請指定要安裝預設或具名執行個體。
  
     **執行個體識別碼** ：依預設，此執行個體名稱會當作執行個體識別碼使用。 這是用來識別 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]執行個體的安裝目錄和登錄機碼。 這是預設執行個體和具名執行個體的狀況。 如果是預設執行個體，執行個體名稱和執行個體識別碼將會是 MSSQLSERVER。 若要使用非預設的執行個體識別碼，請選取 **[執行個體識別碼]** 文字方塊並提供值。  
  
    > [!NOTE]  
    >  標準的獨立 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體 (不論是預設還是具名執行個體) 都不會針對 [執行個體識別碼] 文字方塊使用非預設的值。  
  
    > [!IMPORTANT]  
    >  針對容錯移轉叢集執行個體準備的所有節點使用相同的執行個體識別碼  
  
     **執行個體根目錄** - 依預設，執行個體根目錄為 C:\Program Files\\[!INCLUDE[msCoName](../../../includes/msconame-md.md)][!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]\\。 若要指定非預設的根目錄，請使用提供的欄位，或是按一下省略符號按鈕找出安裝資料夾。  
  
     **安裝的執行個體** - 此方格會顯示執行安裝程式之電腦上的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體。 如果預設執行個體已經安裝在電腦上，您就必須安裝 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]的具名執行個體。 選取 [下一步] 以繼續操作。  
  
12. [磁碟空間需求] 頁面會計算您所指定之功能的所需磁碟空間，並且比較空間需求與執行安裝程式之電腦的可用磁碟空間。  
  
13. 使用此頁面來指定叢集安全性原則。  
  
    -   [!INCLUDE[firstref_longhorn](../../../includes/firstref-longhorn-md.md)] 及更新版本 - 服務 SID (伺服器安全性識別碼) 是建議的設定，也是預設設定。 沒有任何將此設定變更為安全性群組的選項。 如需 [!INCLUDE[nextref_longhorn](../../../includes/nextref-longhorn-md.md)]上服務 SID 功能的資訊，請參閱 [設定 Windows 服務帳戶與權限](../../../database-engine/configure-windows/configure-windows-service-accounts-and-permissions.md)。 這已經在 [!INCLUDE[winserver2008r2](../../../includes/winserver2008r2-md.md)]的獨立和叢集設定中測試過。  
  
    -   在 [!INCLUDE[winxpsvr](../../../includes/winxpsvr-md.md)]上，請指定 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 服務的網域群組。 所有資源權限都是由包含 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 服務帳戶當做群組成員的網域層級群組所控制。  
  
     選取 [下一步] 以繼續操作。  
  
14. 本主題其餘部分的工作流程會因您針對安裝所指定的功能而不同。 您可能不會看到所有頁面，端視您的選取項目而定。  
  
15. 在 [伺服器設定 - 服務帳戶] 頁面中，指定 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 服務的登入帳戶。 在這個頁面上所設定的實際服務隨著您選取要安裝的功能而不同。  
  
     您可以將相同登入帳戶指派給所有 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 服務，或個別設定每一個服務帳戶。 啟動類型針對所有可識別叢集服務是設為手動，其中包括全文檢索搜尋及 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 代理程式，並且在安裝期間無法進行變更。 [!INCLUDE[msCoName](../../../includes/msconame-md.md)] 建議您個別設定服務帳戶，以針對各項服務提供最低權限，其中 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 服務會授與必須完成工作的最小權限。 如需詳細資訊，請參閱 [設定 Windows 服務帳戶與權限](../../../database-engine/configure-windows/configure-windows-service-accounts-and-permissions.md)預覽版本升級問題的解答。  
  
     若要針對此 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]執行個體中的所有服務帳戶指定相同的登入帳戶，請在頁面底部的欄位中提供認證。  
  
     **安全性注意事項** [!INCLUDE[ssNoteStrongPass](../../../includes/ssnotestrongpass-md.md)]  
  
     當您完成針對 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 服務指定登入資訊之後，請按 **[下一步]** 。  
  
16. 使用 [伺服器組態 - 定序] 索引標籤，為 [!INCLUDE[ssDE](../../../includes/ssde-md.md)] 和 [!INCLUDE[ssASnoversion](../../../includes/ssasnoversion-md.md)] 指定非預設的定序。  
  
17. 使用 [Server Configuration - Filestream (伺服器組態 - Filestream)] **[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 可針對**  的執行個體啟用 FILESTREAM。  選取 [下一步] 以繼續操作。  
  
18. 使用[!INCLUDE[ssRSnoversion](../../../includes/ssrsnoversion-md.md)] [組態] 頁面來指定要建立的 [!INCLUDE[ssRSnoversion](../../../includes/ssrsnoversion-md.md)] 安裝類型。 如果是容錯移轉叢集執行個體安裝，此選項會設定為未設定的 [!INCLUDE[ssRSnoversion](../../../includes/ssrsnoversion-md.md)] 安裝。 在您完成安裝之後，必須設定 [!INCLUDE[ssRSnoversion](../../../includes/ssrsnoversion-md.md)] 服務。  
   
19. 在 [錯誤報告] 頁面上，指定您想要傳送給 [!INCLUDE[msCoName](../../../includes/msconame-md.md)] 的資訊，這可協助改善 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]。 錯誤報告選項預設為啟用。  
  
20. 系統組態檢查將會執行一組額外的規則，以便使用您已指定的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 功能來驗證組態。  
  
21. [準備安裝] 頁面會顯示在安裝期間指定之安裝選項的樹狀檢視。 若要繼續，請按一下 **[安裝]** 。 安裝程式會先安裝選取功能所需的必要條件，之後再進行功能安裝。  
  
     在安裝期間，[安裝進度] 頁面會提供狀態，好讓您可以在安裝程式進行時監視安裝進度。 安裝之後， **[完成]** 頁面會提供安裝和其他重要注意事項之摘要記錄檔的連結。  
  
22. 若要完成 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 安裝程序，請按一下 **[關閉]** 。  
  
23. 如果指示您重新啟動電腦，請立刻執行。 當您完成安裝時，請務必閱讀安裝精靈所提供的訊息。 如需安裝程式記錄檔的資訊，請參閱 [檢視與讀取 SQL Server 安裝程式記錄檔](../../../database-engine/install-windows/view-and-read-sql-server-setup-log-files.md)。  
  
24. 重複上述步驟，為容錯移轉叢集執行個體準備其他節點。 您也可以使用自動產生的組態檔，在其他節點上執行準備工作。 如需詳細資訊，請參閱 [使用組態檔安裝 SQL Server 2016](../../../database-engine/install-windows/install-sql-server-2016-using-a-configuration-file.md)。  
  
## <a name="complete"></a>[完成]  
  
#### <a name="advancedenterprise-failover-cluster-instance-install-step-2-complete"></a>進階/企業型容錯移轉叢集執行個體安裝步驟 2：[完成]  
  
1.  在依照 [準備步驟](#prepare)中所述的內容來準備所有節點之後，請在其中一個備妥的節點上執行安裝程式，最好是在擁有共用磁碟的節點上執行。 在  安裝中心的 [進階] [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 頁面上，按一下 **[進階叢集完成]** 。  
  
2.  系統組態檢查會在電腦上執行探索作業。 若要繼續，請[!INCLUDE[clickOK](../../../includes/clickok-md.md)]。 您可以按一下 **[顯示詳細資料]** 在畫面上檢視詳細資料，或是按一下 **[檢視詳細資料報表]** 來以 HTML 報表形式檢視詳細資料。  
  
3.  在 [安裝程式支援檔案] 頁面上，按一下 **[安裝]** ，即可安裝安裝程式支援檔案。  
  
4.  系統組態檢查將會先確認電腦的系統狀態，然後安裝程式才會繼續進行。 檢查完成之後，請按 **[下一步]** 繼續進行。 您可以按一下 **[顯示詳細資料]** 在畫面上檢視詳細資料，或是按一下 **[檢視詳細資料報表]** 來以 HTML 報表形式檢視詳細資料。  
  
5.  如果您在當地語系化的作業系統上安裝，而且安裝媒體包含英文以及與作業系統對應之語言的語言套件，您便可以在 [語言選擇] 頁面上指定 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體的語言。 如需跨語言支援和安裝考量的詳細資訊，請參閱 [SQL Server 中的地區語言版本](../../../sql-server/install/local-language-versions-in-sql-server.md)。  
  
     若要繼續進行，請按 **[下一步]** 。  
  
6.  使用 [叢集節點設定] 頁面來選取準備要建立叢集的執行個體名稱，並針對新的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 容錯移轉叢集執行個體指定網路名稱。 這是在網路上用來識別容錯移轉叢集執行個體的名稱。  
  
    > [!NOTE]  
    >  這在舊版的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 容錯移轉叢集執行個體中稱為虛擬 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 名稱。  
  
7.  [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 安裝程式會依據您選取的功能執行一組額外的規則，以驗證您的組態。  
  
8.  使用 [叢集資源群組] 頁面，即可指定即將放置 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 虛擬伺服器資源的叢集資源群組名稱。 若要指定 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 叢集資源群組名稱， 您有兩個選擇：  
  
    -   使用清單來指定要使用的現有群組。  
  
    -   輸入要建立之新群組的名稱。 請注意，"Available storage" 名稱不是有效的群組名稱。  
  
9. 在 [叢集磁碟選取] 頁面上，選取 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 容錯移轉叢集執行個體的共用叢集磁碟資源。 叢集磁碟就是即將放置 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 資料的位置。 可以指定一個以上的磁碟。 [可用共用磁碟] 方格會顯示可用磁碟的清單、每個磁碟是否具有成為共用磁碟的資格，以及每個磁碟資源的描述。 選取 [下一步] 以繼續操作。  
  
    > [!NOTE]  
    >  第一個磁碟機會當做所有資料庫的預設磁碟機使用，但是可以在 [!INCLUDE[ssDE](../../../includes/ssde-md.md)] 或 [!INCLUDE[ssASnoversion](../../../includes/ssasnoversion-md.md)] 組態頁面上變更。  
  
10. 在 [叢集網路組態] 頁面上，指定容錯移轉叢集執行個體的網路資源：  
  
    -   **網路設定** - 針對容錯移轉叢集執行個體的所有節點與子網路指定 IP 類型和 IP 位址。 您可以針對多重子網路容錯移轉叢集執行個體指定多個 IP 位址，但是每個子網路只支援一個 IP 位址。 每個備妥的節點都至少應該是一個 IP 位址的擁有者。 如果您的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 容錯移轉叢集執行個體具有多個子網路，系統將會提示您將 IP 位址資源相依性設定為 OR。  
  
     選取 [下一步] 以繼續操作。  
  
11. 本主題其餘部分的工作流程會因您針對安裝所指定的功能而不同。 您可能不會看到所有頁面，端視您的選取項目而定。  
  
12. 使用 [!INCLUDE[ssDE](../../../includes/ssde-md.md)] [組態 - 帳戶提供] 頁面來指定以下項目：  
  
    -   安全性模式 - 為 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]執行個體選取 Windows 驗證或混合模式驗證。 如果您選取混合模式驗證，就必須為內建 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 系統管理員帳戶提供增強式密碼。  
  
         當裝置與 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]建立成功的連接之後，Windows 驗證和混合模式的安全性機制是相同的。  
  
    -   [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 管理員 - 您在 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]執行個體上至少必須指定一個系統管理員。 若要加入 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 安裝程式執行所用的帳戶，請按一下 **[加入目前使用者]** 。 若要從系統管理員清單中加入或移除帳戶，請按一下 **[加入]** 或 **[移除]** ，然後編輯在 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]執行個體中將會有管理員權限的使用者、群組或電腦清單。  
  
     當您完成清單的編輯之後，請 [!INCLUDE[clickOK](../../../includes/clickok-md.md)]。 然後，在組態對話方塊中確認管理員的清單。 當此清單完成時，請按 **[下一步]** 。  
  
13. 使用 [!INCLUDE[ssDE](../../../includes/ssde-md.md)] [組態 - 資料目錄] 頁面來指定非預設的安裝目錄。 若要安裝到預設目錄，請按 **[下一步]** 。  
  
    > [!IMPORTANT]  
    >  如果您要指定非預設的安裝目錄，請確定安裝資料夾對於此 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]執行個體是唯一的。 此對話方塊上的任何目錄都不應該與其他 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]執行個體中的目錄共用。 資料目錄應位於容錯移轉叢集執行個體的共用叢集磁碟上。  
  
  
14. 使用 [[!INCLUDE[ssASnoversion](../../../includes/ssasnoversion-md.md)] 組態 - 帳戶提供] 頁面來指定即將擁有 [!INCLUDE[ssASnoversion](../../../includes/ssasnoversion-md.md)] 之管理員權限的使用者或帳戶。 您至少必須針對 [!INCLUDE[ssASnoversion](../../../includes/ssasnoversion-md.md)]指定一個系統管理員。 若要加入 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 安裝程式執行所用的帳戶，請按一下 **[加入目前使用者]** 。 若要在系統管理員清單中加入或移除帳戶，請按一下 **[加入]** 或 **[移除]** ，然後編輯在 [!INCLUDE[ssASnoversion](../../../includes/ssasnoversion-md.md)]中將會有管理員權限的使用者、群組或電腦清單。  
  
     當您完成清單的編輯之後，請 [!INCLUDE[clickOK](../../../includes/clickok-md.md)]。 然後，在組態對話方塊中確認管理員的清單。 當此清單完成時，請按 **[下一步]** 。  
  
15. 使用 [!INCLUDE[ssASnoversion](../../../includes/ssasnoversion-md.md)] [組態 - 資料目錄] 頁面來指定非預設的安裝目錄。 若要安裝到預設目錄，請按 **[下一步]** 。  
  
    > [!IMPORTANT]  
    >  如果您要指定非預設的安裝目錄，請確定安裝資料夾對於此 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]執行個體是唯一的。 此對話方塊上的任何目錄都不應該與其他 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]執行個體中的目錄共用。 資料目錄應該位於容錯移轉叢集的共用叢集磁碟上。  
  
  
16. 系統組態檢查將會執行一組額外的規則，以便使用您已指定的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 功能來驗證組態。  
  
17. [準備安裝] 頁面會顯示在安裝期間指定之安裝選項的樹狀檢視。 若要繼續，請按一下 **[安裝]** 。 安裝程式會先安裝選取功能所需的必要條件，之後再進行功能安裝。  
  
18. 在安裝期間，[安裝進度] 頁面會提供狀態，好讓您可以在安裝程式進行時監視安裝進度。  
  
19. 安裝之後， **[完成]** 頁面會提供安裝和其他重要注意事項之摘要記錄檔的連結。 若要完成 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 安裝程序，請按一下 **[關閉]** 。 有了這個步驟，針對相同容錯移轉叢集執行個體準備的所有節點現在都會成為已完成的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 容錯移轉叢集執行個體的一部分。  
  
## <a name="next-steps"></a>後續步驟  
 **設定新的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 安裝**：為了減少系統的可攻擊介面區，[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 會選擇性地安裝及啟用主要服務和功能。 如需詳細資訊，請參閱＜ [Surface Area Configuration](../../../relational-databases/security/surface-area-configuration.md)＞。  
  
 如需記錄檔位置的詳細資訊，請參閱 [檢視與讀取 SQL Server 安裝程式記錄檔](../../../database-engine/install-windows/view-and-read-sql-server-setup-log-files.md)。  
  
## <a name="see-also"></a>另請參閱  
 [從命令提示字元安裝 SQL Server 2016](../../../database-engine/install-windows/install-sql-server-2016-from-the-command-prompt.md)  
  
  
