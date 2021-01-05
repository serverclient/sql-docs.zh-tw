---
title: 升級容錯移轉叢集執行個體
description: 使用安裝媒體升級 SQL Server Always On 容錯移轉叢集執行個體的步驟。 了解輪流升級和升級多個子網路的叢集。
ms.custom: seo-lt-2019
ms.date: 11/21/2019
ms.prod: sql
ms.reviewer: ''
ms.technology: failover-cluster-instance
ms.topic: conceptual
helpviewer_keywords:
- upgrading failover cluster instances
- clusters [SQL Server], upgrading
- failover clustering [SQL Server], upgrading
ms.assetid: daac41fe-7d0b-4f14-84c2-62952ad8cbfa
author: cawrites
ms.author: chadam
ms.openlocfilehash: aaf4e27a88bdb3e23937521f230e62dae4115129
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97642742"
---
# <a name="upgrade-a-failover-cluster-instance"></a>升級容錯移轉叢集執行個體 
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]
  [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 支援將 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 容錯移轉叢集升級至新版本的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]、新的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Service Pack 或累積更新，或在所有容錯移轉叢集節點上個別安裝至新的 Windows Service Pack 或累積更新時，可將停機時間限制為僅單一手動容錯移轉 (回復為原始主要複本時則為兩次手動容錯移轉)。  

  
 [!INCLUDE[winblue-server-2-md](../../../includes/winblue-server-2-md.md)] 之前的作業系統不支援對包含容錯移轉叢集執行個體的節點進行 Windows Server 作業系統升級。 若要升級在 [!INCLUDE[winblue-server-2-md](../../../includes/winblue-server-2-md.md)] 或以上版本上執行的 Windows Server 容錯移轉叢集節點，請參閱[執行輪流升級或更新](#perform-a-rolling-upgrade-or-update)。  
  
 支援詳細資料如下：  
  
-   支援從使用者介面和命令提示字元進行 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 升級。 您可以在每個容錯移轉叢集節點上，透過命令提示字元執行升級，也可以使用 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 安裝程式 UI 來升級每個叢集節點。 如需詳細資訊，請參閱

   - 安裝新的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 容錯移轉叢集執行個體
   - [從命令提示字元安裝 SQL Server](../../../database-engine/install-windows/install-sql-server-from-the-command-prompt.md)

-   [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 升級不支援下列狀況：  
  
    -   您無法從 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 的獨立執行個體升級至容錯移轉叢集執行個體。  
  
    -   您無法將功能新增至容錯移轉叢集執行個體。 例如，您無法將 [!INCLUDE[ssDE](../../../includes/ssde-md.md)] 新增至現有的僅限 [!INCLUDE[ssASnoversion](../../../includes/ssasnoversion-md.md)] 容錯移轉叢集執行個體。  
  
    -   在 Windows Server 容錯移轉叢集的任何節點上，都無法將容錯移轉叢集執行個體降級為獨立執行個體。  
  
    -   變更容錯移轉叢集執行個體的版本會受限於特定狀況。 如需詳細資訊，請參閱 [支援的版本與版本升級](../../../database-engine/install-windows/supported-version-and-edition-upgrades.md)。  
  
-   在容錯移轉叢集執行個體升級期間，停機時間僅包含容錯移轉時間以及執行升級指令碼所需的時間。 在開始升級程序之前，如果您遵循下方的容錯移轉叢集執行個體輪流升級程序，且所有節點都符合必要條件，就可將停機時間降至最低。 在記憶體最佳化資料表使用中時升級 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 將會花費額外時間。 如需詳細資訊，請參閱 [計劃和測試資料庫引擎升級計劃](../../../database-engine/install-windows/plan-and-test-the-database-engine-upgrade-plan.md)。  
  
## <a name="prerequisites"></a>必要條件  
 在開始之前，請檢閱以下重要資訊：  
  
-   [支援的版本與版本升級](../../../database-engine/install-windows/supported-version-and-edition-upgrades.md)：確認您可從您的 Windows 作業系統版本與 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 版本升級至 [!INCLUDE[ssCurrent](../../../includes/sscurrent-md.md)]。 例如，您無法直接從 SQL Server 2005 容錯移轉叢集執行個體升級至 [!INCLUDE[ssCurrent](../../../includes/sscurrent-md.md)]，也無法升級在 [!INCLUDE[winxpsvr-md](../../../includes/winxpsvr-md.md)] 上執行的容錯移轉叢集執行個體。  
  
-   [選擇資料庫引擎升級方法](../../../database-engine/install-windows/choose-a-database-engine-upgrade-method.md)：根據您檢閱的支援版本與版本升級，選取適當的升級方法和步驟，此外亦根據作業環境中安裝的其他元件，依正確順序升級元件。  
  
-   [計劃和測試資料庫引擎升級計畫](../../../database-engine/install-windows/plan-and-test-the-database-engine-upgrade-plan.md)：檢閱版本資訊與已知的升級問題、升級前檢查清單，並開發和測試升級計畫。  
  
-   [安裝 SQL Server 的硬體與軟體需求](../../../sql-server/install/hardware-and-software-requirements-for-installing-sql-server.md)：檢閱安裝 [!INCLUDE[ssCurrent](../../../includes/sscurrent-md.md)] 的軟體需求。 如果需要其他軟體，請先將其安裝在每個節點上，然後開始升級程序，以將任何停機時間降到最低。  
  
## <a name="perform-a-rolling-upgrade-or-update"></a>執行輪流升級或更新  
 若要將 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 容錯移轉叢集執行個體升級至 [!INCLUDE[ssCurrent](../../../includes/sscurrent-md.md)]，請使用 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 安裝程式升級每個參與容錯移轉叢集執行個體的節點 (從被動節點開始，一次一個)。 當您升級每個節點後，該節點就不會包含在容錯移轉叢集執行個體的可能擁有者中。 如果發生非預期的容錯移轉，在 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 安裝程式將 Windows Server 容錯移轉叢集角色擁有權移至升級的節點之前，升級的節點不會參與容錯移轉。  
  
 根據預設， [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 安裝程式會自動決定容錯移轉至升級節點的時機。 這個時機會取決於容錯移轉叢集執行個體中的節點總數以及已經升級的節點數目而定。 如果半數以上的節點都已經升級，當您在下一個節點上執行升級時， [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 安裝程式就會將容錯移轉導至升級的節點。 容錯移轉至升級的節點之後，叢集群組就會移至升級的節點。 系統會將所有升級的節點都放置在可能擁有者的清單中，並從可能擁有者的清單中移除尚未升級的所有節點。 當您升級其餘每個節點後，該節點就會新增至容錯移轉叢集執行個體的可能擁有者。  
  
 這個程序會導致停機時間僅會包含整個容錯移轉叢集升級期間的單一容錯移轉時間和資料庫升級指令碼執行時間。  
  
 若要在升級程序期間控制叢集節點的容錯移轉行為，請從命令提示字元執行升級作業，然後使用 /FAILOVERCLUSTERROLLOWNERSHIP 參數。 如需詳細資訊，請參閱 [從命令提示字元安裝 SQL Server](../../../database-engine/install-windows/install-sql-server-from-the-command-prompt.md)。  

 ## <a name="upgrade-with-installation-media"></a>使用安裝媒體升級 
  
1.  從符合您要升級的版本的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 安裝媒體版本，按兩下根資料夾中的 setup.exe。 如果您之前未安裝必要元件，系統可能會要求您安裝。  
  
2.  安裝必要元件之後，安裝精靈會啟動 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 安裝中心。 若要升級現有的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]執行個體，請選取您的執行個體。  
  
3.  如果需要 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 安裝程式支援檔案， [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 安裝程式就會安裝這些檔案。 如果系統指示您重新啟動電腦，請先重新啟動，然後再繼續進行。  
  
4.  系統組態檢查會在電腦上執行探索作業。 若要繼續，請[!INCLUDE[clickOK](../../../includes/clickok-md.md)]。  
  
5.  在 [產品金鑰] 頁面上，針對符合舊產品版本的新版本輸入 PID 金鑰。 例如，若要升級 Enterprise 容錯移轉叢集，您必須提供 [!INCLUDE[ssEnterprise](../../../includes/ssenterprise-md.md)]的 PID 金鑰。 選取 [下一步] 以繼續操作。 請注意，您針對容錯移轉叢集升級所使用的 PID 金鑰在相同 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體的所有容錯移轉叢集節點之間必須一致。  
  
6.  在 [授權條款] 頁面上，閱讀授權合約，然後選取要接受授權條款和條件的核取方塊。 若要協助提升 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]，您也可以啟用功能使用方式選項，並傳送報告給 [!INCLUDE[msCoName](../../../includes/msconame-md.md)]。 按一下 [下一步]，繼續進行。 若要結束安裝程式，請按一下 **[取消]** 。  
  
7.  在 [選取執行個體] 頁面中，指定要升級為 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 的 [!INCLUDE[ssCurrent](../../../includes/sscurrent-md.md)]執行個體。 按一下 [下一步]，繼續進行。  
  
8.  在 [特徵選取] 頁面上，系統會預先選取要升級的功能。 當您選取功能名稱之後，每一個元件群組的描述就會出現在右窗格中。 請注意，您無法變更要升級的功能，而且您無法在升級作業期間加入功能。 若要在完成升級作業後，將功能加入升級的 [!INCLUDE[ssSQL14](../../../includes/sssql14-md.md)] 執行個體，請參閱 [將功能加入 SQL Server 2016 的執行個體 &#40;安裝程式&#41;](../../../database-engine/install-windows/add-features-to-an-instance-of-sql-server-setup.md)。  
  
     右窗格會顯示選取功能的必要條件。 SQL Server 安裝程式將會在這個程序稍後說明的安裝步驟期間安裝尚未安裝的必要條件。 為了節省時間，您應該在每個節點上預先安裝這些必要條件。  
  
9. 在 [執行個體組態] 頁面上，系統會根據舊的執行個體自動填入欄位。 您可以選擇指定新的 InstanceID 值。  
  
     **執行個體識別碼** ：依預設，此執行個體名稱會當作執行個體識別碼使用。 這是用來識別 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]執行個體的安裝目錄和登錄機碼。 這是預設執行個體和具名執行個體的狀況。 如果是預設執行個體，執行個體名稱和執行個體識別碼將會是 MSSQLSERVER。 若要使用非預設的執行個體識別碼，請選取 [執行個體識別碼] 核取方塊並提供值。 如果您覆寫預設值，就必須針對在所有容錯移轉叢集節點上升級的執行個體指定相同的執行個體識別碼。 升級之執行個體的執行個體識別碼在這些節點之間必須相符。  
  
     **偵測到的執行個體和功能** - 此方格會顯示執行安裝程式之電腦上的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體。 按一下 [下一步]，繼續進行。  
  
10. [磁碟空間需求] 頁面會計算您所指定之功能的所需磁碟空間，並且比較空間需求與執行安裝程式之電腦的可用磁碟空間。  
  
11. 在 [全文檢索搜尋升級] 頁面上，針對升級的資料庫指定升級選項。 如需詳細資訊，請參閱 [全文檢索搜尋升級選項](../../../database-engine/install-windows/install-sql-server.md)。  
  
12. 在 **[錯誤報告]** 頁面上，指定您想要傳送給 [!INCLUDE[msCoName](../../../includes/msconame-md.md)] 的資訊，這可協助改善 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]。 錯誤報告選項預設為啟用。  
  
13. 在升級作業開始之前，系統組態檢查會執行一組額外的規則，以便使用您已指定的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 功能來驗證電腦組態。  
  
14. [叢集升級報表] 頁面會顯示容錯移轉叢集執行個體中的節點清單以及每個節點上 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 元件的執行個體版本資訊。 它會顯示資料庫指令碼狀態和複寫指令碼狀態。 此外，它也會顯示當您按一下 [下一步] 時所進行之動作的參考用訊息。 根據已經升級的容錯移轉叢集節點數目和節點總數，安裝程式會顯示當您按一下 [下一步] 時所發生的容錯移轉行為。 如果您尚未安裝必要元件，它也會發出可能產生不必要停機時間的警告。   
  
15. [準備升級] 頁面會顯示在安裝期間指定之安裝選項的樹狀檢視。 若要繼續，請按一下 **[升級]** 。 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 安裝程式會先安裝選取功能所需的必要條件，之後再進行功能安裝。  
  
16. 在升級期間，[進度] 頁面會提供狀態，好讓您可以在安裝程式進行時監視目前節點的升級進度。  
  
17. 升級目前的節點之後，[叢集升級報表] 頁面就會顯示所有容錯移轉叢集節點的升級狀態資訊、每個容錯移轉叢集節點的功能，以及其版本資訊。 請確認所顯示的版本資訊並且繼續進行其餘節點的升級作業。 如果發生容錯移轉至升級節點的行為，這也會出現在狀態頁面上。 您也可以在 Windows 叢集管理員工具中檢查，然後確認。  
  
18. 升級之後，[完成] 頁面會提供安裝和其他重要注意事項之摘要記錄檔的連結。 若要完成 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 安裝程序，請按一下 **[關閉]** 。  
  
19. 如果指示您重新啟動電腦，請立刻執行。 當您完成安裝時，請務必閱讀安裝精靈所提供的訊息。 如需安裝程式記錄檔的詳細資訊，請參閱 [檢視與讀取 SQL Server 安裝程式記錄檔](../../../database-engine/install-windows/view-and-read-sql-server-setup-log-files.md)。  
  
20. 若要完成升級程序，請在 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 容錯移轉叢集執行個體的所有其他節點上重複這些步驟。  
  
## <a name="upgrade-a-multi-subnet-failover-cluster-instance"></a>升級多重子網路容錯移轉叢集執行個體  

請遵循下列步驟，在多重子網環境中升級您的 Always On 容錯移轉叢集執行個體。 
  
### <a name="to-upgrade-to-a-ssnoversion-multi-subnet-failover-cluster-instance-existing-ssnoversion-cluster-is-a-non-multi-subnet-cluster"></a>升級至 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 多重子網路容錯移轉叢集執行個體 (現有的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 叢集為非多重子網路叢集)。  
  
1.  依照上述步驟升級您的容錯移轉叢集執行個體。  
  
2.  使用 AddNode 安裝程式動作將節點新增至不同的子網路，並在 [叢集網路設定] 頁面確認 IP 位址資源相依性為 OR。 如需詳細資訊，請參閱[在 Always On 容錯移轉叢集執行個體中新增或移除節點 (安裝程式)](../install/add-or-remove-nodes-in-a-sql-server-failover-cluster-setup.md)。  
  
### <a name="to-upgrade-a-multi-subnet-failover-cluster-instance-currently-using-stretch-vlan-to-use-multi-subnet"></a>將目前使用 Stretch VLAN 的多重子網路容錯移轉叢集執行個體升級為使用多重子網路。  
  
1.  請遵循上述步驟，將您的叢集升級到 [!INCLUDE[ssCurrent](../../../includes/sscurrent-md.md)]。  
  
2.  變更網路設定，將遠端節點移到不同的子網路。  
  
3.  使用容錯移轉叢集管理員或 PowerShell，為新的子網路新增新的 IP 位址，將 IP 位址資源相依性設定為 OR。  
  
## <a name="next-steps"></a>後續步驟  
 在升級為 [!INCLUDE[ssCurrent](../../../includes/sscurrent-md.md)]之後，請完成下列工作：  
  
-   [完成資料庫引擎升級](../../../database-engine/install-windows/complete-the-database-engine-upgrade.md)  
  
-   [變更資料庫相容性模式並使用查詢存放區](../../../database-engine/install-windows/change-the-database-compatibility-mode-and-use-the-query-store.md)  
  
-   [善用新的 SQL Server 2016 功能](../../what-s-new-in-sql-server-2017.md)  
  

  
