---
title: WSFC 仲裁模式和投票組態
description: 了解具備 SQL Server 容錯移轉叢集執行個體或 Always On 可用性群組的 Windows Server 容錯移轉叢集使用仲裁模式和投票的方式。
ms.custom: seo-lt-2019
ms.date: 10/03/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: failover-cluster-instance
ms.topic: conceptual
helpviewer_keywords:
- Availability Groups [SQL Server], WSFC clusters
- quorum [SQL Server], AlwaysOn and WSFC quorum
- failover clustering [SQL Server], AlwaysOn Availability Groups
ms.assetid: ca0d59ef-25f0-4047-9130-e2282d058283
author: cawrites
ms.author: chadam
ms.openlocfilehash: 51e48eeead20cce35d4e4c58d767384bab1a8a65
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97642616"
---
# <a name="wsfc-quorum-modes-and-voting-configuration-sql-server"></a>WSFC 仲裁模式和投票組態 (SQL Server)
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]
  [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)][!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] 和 AlwaysOn 容錯移轉叢集執行個體 (FCI) 都會利用 Windows Server 容錯移轉叢集 (WSFC) 作為平台技術。  WSFC 使用以仲裁為基礎的方法，監視整體叢集健全狀況並最大化節點層級容錯能力。 WSFC 仲裁模式和節點投票組態的基礎了解，對於 AlwaysOn 高可用性和災害復原方案的設計、操作和疑難排解非常重要。  
  
  
##  <a name="cluster-health-detection-by-quorum"></a><a name="ClusterHealthDetectionbyQuorum"></a> 使用仲裁進行叢集健全狀況偵測  
 WSFC 叢集中的每個節點都會參與定期的活動訊號通訊，與其他節點共用節點的健全狀況。 沒有回應的節點是視為處於失敗狀態。  
  
 *「仲裁」* (Quorum) 節點集是 WSFC 叢集中的多數投票節點和見證。 WSFC 叢集的整體健全狀況和狀態是由定期 *「仲裁投票」* (Quorum Vote) 所決定。  仲裁的存在意味著叢集狀況良好，能提供節點層級的容錯功能。  
  
 缺少仲裁表示叢集狀況不良。  必須維護整體 WSFC 叢集健全狀況，以確保次要節點狀況良好，可供主要節點容錯移轉。  如果仲裁投票失敗，做為預防措施，WSFC 叢集將設為離線。  這也會導致在叢集中註冊的所有 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體停止。  
  
> [!IMPORTANT]  
>  如果 WSFC 叢集因為仲裁失敗而設為離線，則需要手動操作，將其恢復上線。  
>   
>  如需詳細資訊，請參閱[透過強制仲裁執行 WSFC 災害復原 &#40;SQL Server&#41;](../../../sql-server/failover-clusters/windows/wsfc-disaster-recovery-through-forced-quorum-sql-server.md)。  
  
##  <a name="quorum-modes"></a><a name="QuorumModes"></a> 仲裁模式  
 *「仲裁模式」* (Quorum Mode) 是在 WSFC 叢集層級設定，指出仲裁投票所使用的方法。  容錯移轉叢集管理員公用程式會根據叢集中的節點數來建議仲裁模式。  
  
 下列仲裁模式可用於決定何者構成投票仲裁：  
  
-   **節點多數：** 叢集中超過一半的投票節點必須投票肯定，叢集才是狀況良好。  
  
-   **節點與檔案共用多數：** 類似於節點多數仲裁模式，不過遠端檔案共用也會設定為投票見證，而且從任何節點至該共用的連接也會列入肯定投票。  超過一半的可能投票必須是肯定，叢集才是狀況良好。  
  
     最佳作法是，見證檔案共用不應位於叢集中的任何節點，而且它對叢集中的所有節點都應該是可見的。  
  
-   **節點與磁碟多數。** 類似於節點多數仲裁模式，不過共用磁碟叢集資源也會指定為投票見證，而且從任何節點至該共用磁碟的連接也會列入肯定投票。  超過一半的可能投票必須是肯定，叢集才是狀況良好。  
  
-   **僅限磁碟：** 共用磁碟叢集資源會指定為見證，而且從任何節點至該共用磁碟的連接會列入肯定投票。  
  
> [!TIP]  
>  使用 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)]非對稱儲存組態時，如果投票節點為奇數，通常應該使用節點多數仲裁模式，如果投票節點為偶數，則使用節點與檔案共用多數仲裁模式。  
  
##  <a name="voting-and-non-voting-nodes"></a><a name="VotingandNonVotingNodes"></a> 投票和非投票節點  
 根據預設，WSFC 叢集中的每個節點都是叢集仲裁成員，每個節點都有一票決定整體叢集健全狀況，而且每個節點都會持續嘗試建立仲裁。  仲裁討論至此，針對投票決定叢集健全狀況的 WSFC 叢集節點集仔細限定為「投票節點」。  
  
 WSFC 叢集中沒有個別節點可針對叢集整體狀況良好或狀況不良做最後決定。  在任何給定時刻，從每個節點的觀點來看，某些其他節點可能看起來離線、看起來正在進行容錯移轉，或因為網路通訊失敗而看起來沒有回應。  仲裁投票的關鍵功能是決定 WSFC 叢集中每個節點的表面狀態是否確實為這些節點的實際狀態。  
  
 針對「僅限磁碟」以外的所有仲裁模式，仲裁投票的有效性取決於叢集中所有投票節點之間的可靠通訊。 相同實體子網路上節點之間的網路通訊應視為可靠；仲裁投票應該是信任的。  
  
 不過，如果另一個子網路上的節點在仲裁投票中是視為無回應，但它實際上已上線，另一方面也是狀況良好，最可能的原因是子網路之間的網路通訊失敗。  根據叢集拓撲、仲裁模式和容錯移轉原則組態，該網路通訊失敗實際上可能會建立多組投票節點 (或子網路)。  
  
 當多個投票節點的子網路能夠獨立建立仲裁時，這稱為「裂腦案例」。  在這種情況下，在個別仲裁中的節點可能有不同的表現方式，並且互相衝突。  
  
> [!NOTE]  
>  只在系統管理員手動執行強制仲裁作業時，或在極罕見狀況下手動執行強制容錯移轉，而明確細分仲裁節點集，裂腦案例才是可能的。  
  
 若要簡化仲裁設定及增加開機時間，您可能要調整每個節點的 *NodeWeight* 設定，讓該節點的投票不計入仲裁。  
  
> [!IMPORTANT]  
>  為了能夠使用 NodeWeight 設定，必須將以下 Hotfix 套用至 WSFC 叢集中的所有伺服器：  
>   
>  [KB2494036](https://support.microsoft.com/kb/2494036)：提供 Hotfix 讓您設定叢集節點，該節點在 [!INCLUDE[firstref_longhorn](../../../includes/firstref-longhorn-md.md)] 和 [!INCLUDE[winserver2008r2](../../../includes/winserver2008r2-md.md)] 中沒有仲裁投票  
  
##  <a name="recommended-adjustments-to-quorum-voting"></a><a name="RecommendedAdjustmentstoQuorumVoting"></a> 建議的仲裁投票調整  
 在啟用或停用指定 WSFC 節點的投票時，請遵循下列方針：  
  
-   **預設沒有任何投票。** 假設每個節點一定要有明確的合理原因才能投票。  
  
-   **包含所有主要複本。** 裝載可用性群組主要複本或者為 FCI 慣用擁有者的每一個 WSFC 節點都應該有投票權。  
  
-   **包含可能的自動容錯移轉擁有者。** 可能因為可用性群組自動容錯移轉或 FCI 容錯移轉而裝載主要複本的每個節點都應該有投票權。 如果 WSFC 叢集中只有一個可用性群組，而且可用性複本只由獨立執行個體所裝載，這個規則只會包含屬於自動容錯移轉目標的次要複本。  
  
-   **排除次要網站節點。** 一般而言，不要將投票權提供給位於次要災害復原網站的 WSFC 節點。  您不會希望次要網站上的節點參與決策，使叢集在主要網站沒有任何問題時離線。  
  
-   **奇數投票。** 如有需要，在叢集中加入見證檔案共用、見證節點或見證磁碟，並調整仲裁模式，以避免仲裁投票中可能發生平局。  
  
-   **容錯移轉後重新評估投票指派。** 您不會希望容錯移轉至不支援狀況良好仲裁的叢集組態。  
  
> [!IMPORTANT]
>  在驗證 WSFC 仲裁投票組態時，如果下列任何一個條件成立，AlwaysOn 可用性群組精靈會顯示一則警告：  
> 
>  -   裝載主要複本的叢集節點沒有投票權。  
> -   次要複本有設定自動容錯移轉，而且它的叢集節點沒有投票權。  
> -   [KB2494036](https://support.microsoft.com/kb/2494036) 不會安裝在裝載可用性複本的所有叢集節點上。 針對多網站部署中的叢集節點加入或移除投票需要這個修補程式。 不過，單一網站部署中通常不需要，而且您可以安心地忽略警告。  
> 
> [!TIP]
>  [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 公開數個系統動態管理檢視 (DMV)，有助於您管理設定相關的 WSFC 叢集組態和節點仲裁投票。  
> 
>  如需詳細資訊，請參閱：  [sys.dm_hadr_cluster](../../../relational-databases/system-dynamic-management-views/sys-dm-hadr-cluster-transact-sql.md), [sys.dm_hadr_cluster_members](../../../relational-databases/system-dynamic-management-views/sys-dm-hadr-cluster-members-transact-sql.md), [sys.dm_os_cluster_nodes](../../../relational-databases/system-dynamic-management-views/sys-dm-os-cluster-nodes-transact-sql.md), [sys.dm_hadr_cluster_networks](../../../relational-databases/system-dynamic-management-views/sys-dm-hadr-cluster-networks-transact-sql.md)。  
  
##  <a name="related-tasks"></a><a name="RelatedTasks"></a> 相關工作  
  
-   [檢視叢集仲裁 NodeWeight 設定](../../../sql-server/failover-clusters/windows/view-cluster-quorum-nodeweight-settings.md)  
  
-   [設定叢集仲裁 NodeWeight 設定](../../../sql-server/failover-clusters/windows/configure-cluster-quorum-nodeweight-settings.md)  
  
##  <a name="related-content"></a><a name="RelatedContent"></a> 相關內容  
  
-   [Microsoft SQL Server AlwaysOn 高可用性和災害復原方案指南](https://go.microsoft.com/fwlink/?LinkId=227600)  
  
-   [AlwaysOn 可用性群組精靈中的仲裁投票組態檢查](https://blogs.msdn.microsoft.com/sqlalwayson/2012/03/13/quorum-vote-configuration-check-in-alwayson-availability-group-wizards-andy-jing/)  
  
-   [Windows Server Technologies:Failover Clusters](https://technet.microsoft.com/library/cc732488\(v=WS.10\).aspx) (Windows Server 技術：容錯移轉叢集)  
  
-   [Failover Cluster Step-by-Step Guide:Configuring the Quorum in a Failover Cluster](https://technet.microsoft.com/library/cc770620\(WS.10\).aspx) (容錯移轉叢集逐步指南：在容錯移轉叢集中設定仲裁)  
  
## <a name="see-also"></a>另請參閱  
 [透過強制仲裁執行 WSFC 災害復原 &#40;SQL Server&#41;](../../../sql-server/failover-clusters/windows/wsfc-disaster-recovery-through-forced-quorum-sql-server.md)   
 [SQL Server 的 Windows Server 容錯移轉叢集 &#40;WSFC&#41;](../../../sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server.md)  
  
  
