---
title: AlwaysOn 容錯移轉叢集執行個體
description: 了解 Always On 容錯移轉叢集執行個體如何透過 SQL Server 中的伺服器執行個體等級備援，以提供本機高可用性。
ms.custom: seo-lt-2019
ms.date: 01/18/2017
ms.prod: sql
ms.reviewer: ''
ms.technology: failover-cluster-instance
ms.topic: conceptual
helpviewer_keywords:
- clustering [SQL Server]
- high availability [SQL Server], failover clustering
- virtual servers [SQL Server], about virtual servers
- clusters [SQL Server]
- servers [SQL Server], failover clustering
- resource groups [SQL Server]
- availability [SQL Server]
- failover clustering [SQL Server]
- AlwaysOn [SQL Server], see failover clustering [SQL Server]
ms.assetid: 86a15b33-4d03-4549-8ea2-b45e4f1baad7
author: cawrites
ms.author: chadam
ms.openlocfilehash: ea1413b30c4706125b711493f64210c2a875ec0f
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97642809"
---
# <a name="always-on-failover-cluster-instances-sql-server"></a>AlwaysOn 容錯移轉叢集執行個體 (SQL Server)
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]

  Always On 容錯移轉叢集執行個體是 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Always On 供應項目的一部分，會利用 Windows Server 容錯移轉叢集 (WSFC) 功能，透過伺服器執行個體層級 (「容錯移轉叢集執行個體」  (FCI)) 的備援提供本機高可用性。 FCI 是跨 Windows Server 容錯移轉叢集 (WSFC) 節點且可能跨多個子網路安裝的單一 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體。 在網路上，FCI 看似單一電腦上的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體，但是 FCI 提供容錯移轉，可以在目前的 WSFC 節點無法使用時，從該節點容錯移轉到另一個節點。  
  
 FCI 可以利用[可用性群組](../../../database-engine/availability-groups/windows/always-on-availability-groups-sql-server.md)提供資料庫層級的遠端災害復原。 如需詳細資訊，請參閱[容錯移轉叢集和可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/failover-clustering-and-always-on-availability-groups-sql-server.md)。  
 
 > [!NOTE]  
 > Windows Server 2016 Datacenter 版本引進了儲存空間直接存取 (S2D) 的支援。 SQL Server 容錯移轉叢集執行個體支援針對叢集存放區資源的 S2D。 如需詳細資訊，請參閱 [Windows Server 2016 中的儲存空間直接存取 (英文)](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview)。
 > 
 >容錯移轉叢集執行個體也支援叢集共用磁碟區 (CSV)。 如需詳細資訊，請參閱 [了解容錯移轉叢集的叢集共用磁碟區](https://technet.microsoft.com/library/dd759255.aspx)。 
   
 **本主題內容：**  
  
-   [優點](#Benefits)  
  
-   [建議](#Recommendations)  
  
-   [容錯移轉叢集執行個體概觀](#Overview)  
  
-   [容錯移轉叢集執行個體的元素](#FCIelements)  
  
-   [SQL Server 容錯移轉概念和工作](#ConceptsAndTasks)  
  
-   [相關主題](#RelatedTopics)  
  
##  <a name="benefits-of-a-failover-cluster-instance"></a><a name="Benefits"></a> 容錯移轉叢集執行個體的優點  
 伺服器的硬體故障或軟體失敗時，連接至伺服器的應用程式或用戶端可能會停機。 當 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體設定成 FCI (而不是獨立執行個體) 時，該 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體的高可用性會受到 FCI 中存在的重複節點保護。 在任何時候，FCI 中僅其中一個節點擁有 WSFC 資源群組。 如果發生失敗 (硬體故障、作業系統失敗、應用程式或服務失敗) 或進行計畫的升級，資源群組擁有權就會移至另一個 WSFC 節點。 此程序對連接至 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 的用戶端或應用程式而言是透明的，而且會將應用程式或用戶端在失敗期間遭遇停機時間的機會降到最低。 下面列出 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 容錯移轉叢集執行個體所提供的一些主要優點：  
  
-   透過備援性在執行個體層級提供保護  
  
-   如果發生失敗 (硬體故障、作業系統失敗、應用程式或服務失敗) 時的自動容錯移轉  
  
    > [!IMPORTANT]  
    >  在可用性群組中，不支援從 FCI 自動容錯移轉至可用性群組中的其他節點。 這表示，如果自動容錯移轉是高可用性方案的重要元件，FCI 和獨立節點不應該在可用性群組內耦合。  不過，這種耦合適用於「災害復原」方案。  
  
-   支援眾多儲存方案，包括 WSFC 叢集磁碟 (iSCSI、光纖通道等) 和伺服器訊息區塊 (SMB) 檔案共用。  
  
-   使用多重子網路 FCI 或在可用性群組內執行 FCI 裝載之資料庫的災害復原方案。 利用 [!INCLUDE[msCoName](../../../includes/msconame-md.md)][!INCLUDE[ssSQL11](../../../includes/sssql11-md.md)]的新多重子網路支援，多重子網路 FCI 不再需要虛擬 LAN，提高了多重子網路 FCI 的管理能力和安全性。  
  
-   在容錯移轉期間，應用程式和用戶端的零重新組態  
  
-   自動容錯移轉之細微觸發程序事件的彈性容錯移轉原則  
  
-   透過使用專用和保存連接的定期和詳細健全狀況偵測，可靠的容錯移轉  
  
-   在容錯移轉時間透過間接背景檢查點的可設定性和可預測性  
  
-   在容錯移轉期間節流的資源使用情況  
  
##  <a name="recommendations"></a><a name="Recommendations"></a> 建議  
 在實際執行環境中，我們建議您搭配容錯移轉叢集執行個體的虛擬 IP 位址使用靜態 IP 位址。  我們建議您不要在實際執行環境中使用 DHCP。 在停機時，如果 DHCP IP 租用過期，則需要額外的時間來重新註冊與 DNS 名稱相關聯的新 DHCP IP 位址。  
  
##  <a name="failover-cluster-instance-overview"></a><a name="Overview"></a> 容錯移轉叢集執行個體概觀  
 FCI 在具有一個或多個 WSFC 節點的 WSFC 資源群組中執行。 當 FCI 啟動時，其中一個節點會取得資源群組擁有權，並使其 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體上線。 此節點所擁有的資源包括：  
  
-   網路名稱  
  
-   IP 位址  
  
-   共用磁碟  
  
-   [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Database Engine 服務  
  
-   [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Agent 服務  
  
-   [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Analysis Services 服務 (如果已安裝)  
  
-   一個檔案共用資源 (如果已安裝 FILESTREAM 功能)  
  
 在任何時候，僅資源群組擁有者在資源群組中執行其各自 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 服務，FCI 中的其他節點並不執行。 當發生容錯移轉時，無論是自動容錯移轉或計畫的容錯移轉，就會依下列順序發生事件：  
  
1.  除非發生硬體或系統失敗，否則在緩衝快取中的所有中途分頁都會寫入磁碟。  
  
2.  在使用中節點上資源群組的所有各自 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 服務都會停止。  
  
3.  資源群組擁有權將轉移到 FCI 中的另一個節點。  
  
4.  新資源群組擁有者啟動其 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 服務。  
  
5.  用戶端應用程式的連接要求自動導向至使用相同虛擬網路名稱 (VNN) 的新使用中節點。  
  
 只要其基礎 WSFC 叢集保持良好的仲裁狀況 (多數仲裁 WSFC 節點可做為自動容錯移轉目標使用)，FCI 就是線上狀態。 當 WSFC 叢集失去其仲裁時 (無論由於硬體、軟體、網路失敗或仲裁組態不正確)，整個 WSFC 叢集與 FCI 就會離線。 在此未規劃的容錯移轉情況下，需要手動介入，在剩餘可用的節點中重新建立仲裁，以使 WSFC 叢集和 FCI 恢復連線。 如需詳細資訊，請參閱 [WSFC 仲裁模式和投票組態 &#40;SQL Server&#41;](../../../sql-server/failover-clusters/windows/wsfc-quorum-modes-and-voting-configuration-sql-server.md)。  
  
### <a name="predictable-failover-time"></a>可預測的容錯移轉時間  
 根據 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體上次執行檢查點作業的時間，緩衝快取中可能有相當多的中途分頁。 因此，容錯移轉的持續時間與剩餘中途分頁寫入磁碟所需的時間一樣長，這可能導致冗長且不可預測的容錯移轉時間。 從 [!INCLUDE[msCoName](../../../includes/msconame-md.md)][!INCLUDE[ssSQL11](../../../includes/sssql11-md.md)]開始，FCI 可以使用間接檢查點，節流緩衝快取中保留的中途分頁數量。 雖然在一般工作負載下這會耗用額外資源，但更能預測和設定容錯移轉時間。 當您組織的服務層級合約規定高可用性方案的復原時間目標 (RTO) 時，此功能非常有用。 如需有關間接檢查點的詳細資訊，請參閱＜ [Indirect Checkpoints](../../../relational-databases/logs/database-checkpoints-sql-server.md#IndirectChkpt)＞。  
  
### <a name="reliable-health-monitoring-and-flexible-failover-policy"></a>可靠的健全狀況監測和彈性的容錯移轉原則  
 在 FCI 成功啟動之後，WSFC 服務會監視基礎 WSFC 叢集和 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體的健全狀況。 從 [!INCLUDE[msCoName](../../../includes/msconame-md.md)][!INCLUDE[ssSQL11](../../../includes/sssql11-md.md)]開始，WSFC 服務使用專用連接，透過系統預存程序來輪詢使用中 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體，以取得詳細元件診斷。 這具有三方面的意義：  
  
-   [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體的專用連接可一直可靠地輪詢元件診斷，即使 FCI 負載過重也一樣。 這能夠區別負載過重的系統和實際發生失敗狀況的系統，因此防止假容錯移轉等問題發生。  
  
-   詳細元件診斷能夠設定更有彈性的容錯移轉原則，讓您可以選擇何種失敗條件觸發容錯移轉，何種失敗條件不觸發。  
  
-   詳細的元件診斷也啟用自動容錯移轉更好的、追溯性的疑難排解。 診斷資訊儲存在記錄檔中，與 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 錯誤記錄檔共置。 您可以將它們載入記錄檔檢視器中，檢查容錯移轉發生前的元件狀態，以便判斷何種狀況導致該容錯移轉。  
  
 如需詳細資訊，請參閱＜ [Failover Policy for Failover Cluster Instances](../../../sql-server/failover-clusters/windows/failover-policy-for-failover-cluster-instances.md)＞  
  
##  <a name="elements-of-a-failover-cluster-instance"></a><a name="FCIelements"></a> 容錯移轉叢集執行個體的元素  
 FCI 是由一組實體伺服器 (節點) 所組成，包含相似硬體組態和相同軟體組態，包括作業系統版本和修補程式等級，以及 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 版本、修補程式等級、元件和執行個體名稱。 需要相同軟體組態，以確保 FCI 在節點之間容錯移轉時可完整運作。  
  
 WSFC 資源群組  
 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] FCI 在 WSFC 資源群組中執行。 資源群組中的每個節點都會維護組態設定和檢查點登錄機碼的同步副本，以確保在容錯移轉後 FCI 可完整運作，而且在任何時候叢集中僅其中一個節點 (使用中節點) 擁有資源群組。 WSFC 服務管理伺服器叢集、仲裁組態、容錯移轉原則和容錯移轉作業，以及 FCI 的 VNN 和虛擬 IP 位址。 如果發生失敗 (硬體故障、作業系統失敗、應用程式或服務失敗) 或進行計畫的升級，資源群組擁有權就會移至 FCI 中的另一個節點。WSFC 資源群組支援的節點數目取決於 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 版本。 此外，相同 WSFC 叢集可以執行多個 FCI (多個資源群組)，視 CPU、記憶體和磁碟數目等硬體容量而定。  
  
 SQL Server 二進位檔  
 產品二進位檔案會在 FCI 的每個節點上本機安裝，此程序類似於 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 獨立安裝。 不過，在啟動期間，這些服務並不會自動啟動，而是透過 WSFC 進行管理。  
  
 儲存體  
 FCI 與可用性群組相反，前者必須在 FCI 的所有節點之間使用共用儲存體，以供資料庫和記錄檔儲存。 共用儲存體可以採用 WSFC 叢集磁碟、SAN 磁碟、儲存空間直接存取 (S2D) 或 SMB 檔案共用的形式。 如此一來，每當發生容錯移轉時，FCI 中的所有節點都有相同的執行個體資料檢視。 不過，這表示共用儲存體是潛在的單一失敗點，而 FCI 仰賴基礎儲存方案以確保資料保護。  
  
 網路名稱  
 FCI 的 VNN 為 FCI 提供統一的連接點。 這允許應用程式連接到 VNN，而無需知道目前使用中的節點。 當發生容錯移轉時，VNN 會在新使用中節點啟動後註冊給它。 此程序對連接至 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 的用戶端或應用程式而言是透明的，而且會將應用程式或用戶端在失敗期間遭遇停機時間的機會降到最低。  
  
 虛擬 IP  
 在多重子網路 FCI 的情況下，FCI 中的每個子網路會被指派一個虛擬 IP 位址。 在容錯移轉期間，DNS 伺服器上的 VNN 會更新，以指向各自子網路的虛擬 IP 位址。 在多重子網路容錯移轉後，應用程式和用戶端可以使用相同 VNN 連接到 FCI。  
  
##  <a name="sql-server-failover-concepts-and-tasks"></a><a name="ConceptsAndTasks"></a> SQL Server 容錯移轉概念和工作  
  
|概念和工作|主題|  
|------------------------|-----------|  
|描述失敗偵測機制和彈性的容錯移轉原則。|[Failover Policy for Failover Cluster Instances](../../../sql-server/failover-clusters/windows/failover-policy-for-failover-cluster-instances.md)|  
|描述 FCI 管理和維護的概念。|[容錯移轉叢集執行個體管理及維護](../../../sql-server/failover-clusters/windows/failover-cluster-instance-administration-and-maintenance.md)|  
|描述多重子網路組態和概念|[SQL Server 多重子網路叢集 &#40;SQL Server&#41;](../../../sql-server/failover-clusters/windows/sql-server-multi-subnet-clustering-sql-server.md)|  
  
##  <a name="related-topics"></a><a name="RelatedTopics"></a> 相關主題  
  
|**主題描述**|**主題**|  
|----------------------------|---------------|  
|描述如何安裝新的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] FCI。|[建立新的 SQL Server 容錯移轉叢集 &#40;安裝程式&#41;](../../../sql-server/failover-clusters/install/create-a-new-sql-server-failover-cluster-setup.md)|  
|描述如何升級至 [!INCLUDE[ssCurrent](../../../includes/sscurrent-md.md)] 容錯移轉叢集。|[升級 SQL Server 容錯移轉叢集執行個體](../../../sql-server/failover-clusters/windows/upgrade-a-sql-server-failover-cluster-instance.md)|  
|描述 Windows 容錯移轉叢集概念，並提供 Windows 容錯移轉叢集相關工作的連結|[!INCLUDE[nextref_longhorn](../../../includes/nextref-longhorn-md.md)]： [容錯移轉叢集概觀](https://go.microsoft.com/fwlink/?LinkId=177878)<br /><br /> [!INCLUDE[nextref_longhorn](../../../includes/nextref-longhorn-md.md)] R2： [容錯移轉叢集概觀](https://go.microsoft.com/fwlink/?LinkId=177879)|  
|描述 FCI 節點和可用性群組複本之間的概念差異，以及使用 FCI 裝載可用性群組複本的考量。|[容錯移轉叢集和可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/failover-clustering-and-always-on-availability-groups-sql-server.md)|  
  
  
