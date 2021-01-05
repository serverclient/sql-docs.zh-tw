---
title: 透過強制仲裁執行災害復原
description: 需要手動介入才能從仲裁失敗中復原。 了解如何在 SQL Server 容錯移轉叢集執行個體發生災害時強制仲裁。
ms.custom: seo-lt-2019
ms.date: 03/14/2017
ms.prod: sql
ms.reviewer: ''
ms.technology: failover-cluster-instance
ms.topic: how-to
helpviewer_keywords:
- Availability Groups [SQL Server], WSFC clusters
- quorum [SQL Server], AlwaysOn and WSFC quorum
- failover clustering [SQL Server], AlwaysOn Availability Groups
ms.assetid: 6cefdc18-899e-410c-9ae4-d6080f724046
author: cawrites
ms.author: chadam
ms.openlocfilehash: fcdd0f1d34ed4107cbe3c90708abe286efbf0837
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97642651"
---
# <a name="wsfc-disaster-recovery-through-forced-quorum-sql-server"></a>透過強制仲裁執行 WSFC 災害復原 (SQL Server)
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]
  仲裁失敗的原因通常是涉及 WSFC 叢集中許多節點的系統損毀、持續性通訊失敗或設定錯誤。  若要從仲裁失敗中復原，您必須進行手動介入。  
  
-   **開始之前：** [必要條件](#Prerequisites)、[安全性](#Security)  
  
-   **透過強制仲裁程序執行 WSFC 災害復原**[透過強制仲裁程序執行 WSFC 災害復原](#Main)  
  
-   [相關工作](#RelatedTasks)  
  
-   [相關內容](#RelatedContent)  
  
##  <a name="before-you-start"></a><a name="BeforeYouBegin"></a> 開始之前  
  
###  <a name="prerequisites"></a><a name="Prerequisites"></a> 必要條件  
 強制仲裁程序會假設仲裁失敗之前存在狀況良好的仲裁。  
  
> [!WARNING]  
>  使用者應該充分了解 Windows Server 容錯移轉叢集、WSFC 仲裁模型、 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]以及環境特定部署組態的概念和互動方式。  
>   
>  如需詳細資訊，請參閱[SQL Server 的 Windows Server 容錯移轉叢集 (WSFC)](https://msdn.microsoft.com/library/hh270278\(v=SQL.110\).aspx)、[WSFC 仲裁模式和投票組態 (SQL Server)](https://msdn.microsoft.com/library/hh270280\(v=SQL.110\).aspx)  
  
###  <a name="security"></a><a name="Security"></a> Security  
 使用者必須是屬於 WSFC 叢集之每一個節點上本機 Administrators 群組成員的網域帳戶。  
  
##  <a name="wsfc-disaster-recovery-through-the-forced-quorum-procedure"></a><a name="Main"></a> 透過強制仲裁程序執行 WSFC 災害復原  
 請記住，仲裁失敗會導致 WSFC 叢集中的所有叢集服務、SQL Server 執行個體和 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)]設定為離線，因為依照設定，叢集無法確保節點層級的容錯。  仲裁失敗表示 WSFC 叢集中狀況良好的投票節點無法再滿足仲裁模型。 某些節點可能已經完全失敗，而某些節點可能剛關閉 WSFC 服務且其他方面狀況良好，但是喪失與仲裁通訊的功能。  
  
 若要讓 WSFC 叢集重新上線，您必須在現有的組態下更正仲裁失敗的根本原因、視需要復原受影響的資料庫，而且您可能會想要重新設定 WSFC 叢集中的剩餘節點，以便反映存活叢集拓撲。  
  
 您可以在 WSFC 叢集節點上使用 *「強制仲裁」* (Forced Quorum) 程序，以便覆寫讓叢集離線的安全控制項。  這個程序實際上會告知叢集暫停仲裁投票檢查，並且讓叢集中任何節點上的 WSFC 叢集資源和 SQL Server 重新上線。  
  
 這種類型的災害復原程序應該包括下列步驟：  
  
#### <a name="to-recover-from-quorum-failure"></a>若要從仲裁失敗中復原：  
  
1.  **判斷失敗的範圍。** 您可以識別哪些可用性群組或 SQL Server 執行個體沒有回應、哪些叢集節點在線上且可供災後使用，並且檢查 Windows 事件記錄檔和 SQL Server 系統記錄檔。  如果可行的話，您應該保留鑑識資料和系統記錄檔，以便日後分析。  
  
    > [!TIP]  
    >  在有回應的 [!INCLUDE[ssCurrent](../../../includes/sscurrent-md.md)]執行個體上，您可以藉由查詢 [sys.dm_hadr_availability_group_states](../../../relational-databases/system-dynamic-management-views/sys-dm-hadr-availability-group-states-transact-sql.md) 動態管理檢視 (DMV)，取得有關本機伺服器執行個體上擁有可用性複本之可用性群組的健全狀況資訊。  
  
2.  **在單一節點上使用強制仲裁，藉以啟動 WSFC 叢集。** 除了 WSFC 叢集服務已關閉以外，您可以識別元件失敗數目最少的節點。  請確認此節點能夠與其他大多數節點通訊。  
  
     在此節點上，您可以使用強制仲裁程序來手動強制叢集上線。  為了盡量降低遺失資料的可能性，請選取最後裝載可用性群組主要複本的節點。  
  
     如需詳細資訊，請參閱[在無仲裁情況下強制啟動 WSFC 叢集](https://msdn.microsoft.com/library/hh270275\(v=SQL.110\).aspx)  
  
    > [!NOTE]  
    >  強制仲裁設定會影響整個叢集並封鎖仲裁檢查，直到邏輯 WSFC 叢集達成大多數投票並自動轉換成一般仲裁模式的作業為止。  
  
3.  **在每個其他方面狀況良好的節點上，以正常方式啟動 WSFC 服務 (一次一個節點)。** 在其他節點上啟動叢集服務時，您不需要指定強制仲裁選項。  
  
     當每個節點上的 WSFC 服務都重新上線時，它會與其他狀況良好的節點交涉，以便同步處理新叢集組態狀態。  請記得一次操作一個節點，避免在解析叢集的最後已知狀態時可能發生的競爭情形。  
  
    > [!WARNING]  
    >  請確定您所啟動的每個節點都能夠與其他新上線的節點通訊。  您可以考慮停用其他節點的 WSFC 服務。  否則，您將面臨建立多個仲裁節點集的風險，亦即裂腦案例。 如果步驟 1 中的發現正確無誤，應該不會發生這種狀況。  
  
4.  **套用新的仲裁模式和節點投票組態。** 如果強制仲裁成功重新啟動叢集中的所有節點，而仲裁失敗的根本原因已獲得解決，則不需要對原始仲裁模式和節點投票組態進行變更。  
  
     否則，您應該評估新復原的叢集節點和可用性複本拓撲，並且依適當情況變更每個節點的仲裁模式和投票指派。 尚未復原的節點應該會設定為離線，或將其節點投票設定為零。  
  
    > [!TIP]  
    >  此時，叢集中的節點和 SQL Server 執行個體看起來可能已還原成一般作業狀態。  不過，狀況良好的仲裁可能仍然不存在。  您可以使用容錯移轉叢集管理員、SQL Server Management Studio 中的 AlwaysOn 儀表板或適當的 DMV 來確認仲裁是否已經還原。  
  
5.  **視需要復原可用性群組資料庫複本。** 非可用性群組資料庫應該會在一般 SQL Server 啟動程序中自行復原並重新上線。  
  
     您可以依照下列順序，讓可用性群組複本重新上線，藉以盡量降低遺失資料的可能性並縮短復原時間：主要複本、同步次要複本和非同步次要複本。  
  
6.  **修復或取代失敗的元件並重新驗證叢集。** 既然您已經從初始災害和仲裁失敗中復原，就應該修復或取代失敗的節點並且據以調整相關的 WSFC 和 AlwaysOn 組態。  這項作業可能包括卸除可用性群組複本、從叢集中收回節點，或在節點上扁平化並重新安裝軟體。  
  
     您必須修復或移除所有失敗的可用性複本。  [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 將不會截斷超過落後可用性複本最遠之最後一個已知點的交易記錄。   如果失敗的複本並未修復或從可用性群組中移除，交易記錄將會成長，而且您將面臨在其他複本上用完交易記錄空間的風險。  
  
    > [!NOTE]  
    >  如果您在 WSFC 叢集上的可用性群組接聽程式結束時執行 WSFC [驗證設定精靈]，這個精靈會產生下列不正確的警告訊息：  
    >   
    >  「網路名稱 'Name:<network_name>' 的 RegisterAllProviderIP 屬性設定為 1。在目前的叢集設定中，這個值應該設定為 0。」  
    >   
    >  請忽略這個訊息。  
  
7.  **視需要重複步驟 4。** 其目標是要重新建立適當層級的容錯和高可用性，以便進行狀況良好的作業。  
  
8.  **進行 RPO/RTO 分析。** 您應該分析 SQL Server 系統記錄檔、資料庫時間戳記和 Windows 事件記錄檔，以便判斷失敗的根本原因，並且記載實際的復原點和復原時間經驗。  
  
##  <a name="related-tasks"></a><a name="RelatedTasks"></a> 相關工作  
  
-   [在無仲裁情況下強制啟動 WSFC 叢集](../../../sql-server/failover-clusters/windows/force-a-wsfc-cluster-to-start-without-a-quorum.md)  
  
-   [執行可用性群組的強制手動容錯移轉 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/perform-a-forced-manual-failover-of-an-availability-group-sql-server.md)  
  
-   [檢視叢集仲裁 NodeWeight 設定](../../../sql-server/failover-clusters/windows/view-cluster-quorum-nodeweight-settings.md)  
  
-   [設定叢集仲裁 NodeWeight 設定](../../../sql-server/failover-clusters/windows/configure-cluster-quorum-nodeweight-settings.md)  
  
-   [使用 AlwaysOn 儀表板 &#40;SQL Server Management Studio&#41;](../../../database-engine/availability-groups/windows/use-the-always-on-dashboard-sql-server-management-studio.md)
  
##  <a name="related-content"></a><a name="RelatedContent"></a> 相關內容  
  
-   [檢視容錯移轉叢集的事件和記錄檔](https://technet.microsoft.com/library/cc772342\(WS.10\).aspx)  
  
-   [Get-ClusterLog 容錯移轉叢集指令程式](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee461045(v=technet.10))  
  
## <a name="see-also"></a>另請參閱  
 [SQL Server 的 Windows Server 容錯移轉叢集 &#40;WSFC&#41;](../../../sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server.md)  
  
