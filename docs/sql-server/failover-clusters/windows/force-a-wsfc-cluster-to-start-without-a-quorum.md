---
title: 在無仲裁情況下強制啟動 WSFC 叢集
description: 在沒有仲裁的情況下強制啟動 Windows Server 容錯移轉叢集的叢集節點，可能必須復原資料並重新建立高可用性。
ms.custom: seo-lt-2019
ms.date: 03/14/2017
ms.prod: sql
ms.reviewer: ''
ms.technology: failover-cluster-instance
ms.topic: how-to
helpviewer_keywords:
- Availability Groups [SQL Server], WSFC clusters
- quorum [SQL Server], AlwaysOn and WSFC quorum
ms.assetid: 4a121375-7424-4444-b876-baefa8fe9015
author: cawrites
ms.author: chadam
ms.openlocfilehash: 908aa73d20ce939696124fee2f92eb87c9b8bec8
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97642736"
---
# <a name="force-a-wsfc-cluster-to-start-without-a-quorum"></a>在無仲裁情況下強制啟動 WSFC 叢集
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]
  本主題描述如何在沒有仲裁的情況下強制啟動 Windows Server 容錯移轉叢集 (WSFC) 叢集節點。  在災害復原和多重子網路案例中，可能需要這個方式才能針對 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] 和 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 容錯移轉叢集執行個體來復原資料及完整重新建立高可用性。  
  
-   **開始之前：** [建議](#Recommendations)、[安全性](#Security)  
  
-   **在無仲裁的情況下，使用下列項目強制啟動叢集：** [使用容錯移轉叢集管理員](#FailoverClusterManagerProcedure)、[使用 Powershell](#PowerShellProcedure)、[使用 Net.exe](#CommandPromptProcedure)  
  
-   **後續操作：** [後續操作：在沒有仲裁的情況下強制啟動叢集之後](#FollowUp)  
  
##  <a name="before-you-start"></a><a name="BeforeYouBegin"></a> 開始之前  
  
###  <a name="recommendations"></a><a name="Recommendations"></a> 建議  
 除了明確指示的內容以外，如果您從 WSFC 叢集中的任何節點執行本主題的程序，都應該有效。  但是，如果您從打算在無仲裁情況下強制啟動的節點執行這些步驟，您可能會得到更好的結果並避免網路問題發生。  
  
###  <a name="security"></a><a name="Security"></a> Security  
 使用者必須是屬於 WSFC 叢集之每一個節點上本機 Administrators 群組成員的網域帳戶。  
  
##  <a name="using-failover-cluster-manager"></a><a name="FailoverClusterManagerProcedure"></a> 使用容錯移轉叢集管理員  
  
##### <a name="to-force-a-cluster-to-start-without-a-quorum"></a>若要在無仲裁情況下強制啟動叢集  
  
1.  開啟容錯移轉叢集管理員，並連接到所要的叢集節點來強制連線。  
  
2.  在 [動作]  窗格中，按一下 [強制啟動叢集]  ，然後按一下 [是 - 強制啟動我的叢集]  。  
  
3.  在左窗格的 **[容錯移轉叢集管理員]** 樹狀目錄中，按一下叢集名稱。  
  
4.  在 [摘要] 窗格中，確認目前 [仲裁設定]  值為：**警告：叢集正以 ForceQuorum 狀態執行**。  
  
##  <a name="using-powershell"></a><a name="PowerShellProcedure"></a> 使用 Powershell  
  
##### <a name="to-force-a-cluster-to-start-without-a-quorum"></a>若要在無仲裁情況下強制啟動叢集  
  
1.  透過 **[以系統管理員身分執行]** 來啟動更高權限的 Windows PowerShell。  
  
2.  匯入 `FailoverClusters` 模組來啟用叢集指令程式。  
  
3.  使用 `Stop-ClusterNode` 來確定叢集服務已停止。  
  
4.  搭配 `Start-ClusterNode` 使用 `-FixQuorum` 來強制啟動叢集服務。  
  
5.  搭配 `Get-ClusterNode` 使用 `-Propery NodeWieght = 1` 來設定值，該值保證節點為仲裁的投票成員。  
  
6.  以可讀格式輸出叢集節點屬性。  
  
### <a name="example-powershell"></a>範例 (Powershell)  
 下列範例會在沒有仲裁的情況下強制啟動 AlwaysOnSrv02 節點叢集服務、設定 `NodeWeight = 1`，然後從新強制的節點列舉叢集節點狀態。  
  
```powershell  
Import-Module FailoverClusters  
  
$node = "Always OnSrv02"  
Stop-ClusterNode -Name $node  
Start-ClusterNode -Name $node -FixQuorum  
  
(Get-ClusterNode $node).NodeWeight = 1  
  
$nodes = Get-ClusterNode -Cluster $node  
$nodes | Format-Table -property NodeName, State, NodeWeight  
  
```  
  
##  <a name="using-netexe"></a><a name="CommandPromptProcedure"></a> 使用 Net.exe  
  
##### <a name="to-force-a-cluster-to-start-without-a-quorum"></a>若要在無仲裁情況下強制啟動叢集  
  
1.  使用遠端桌面連接到所需的叢集節點，以強制連線。  
  
2.  透過 **[以系統管理員身分執行]** 來啟動更高權限的命令提示字元。  
  
3.  使用 **net.exe** 來確定本機叢集服務已停止。  
  
4.  搭配 **使用** net.exe `/forcequorum` 來強制啟動本機叢集服務。  
  
### <a name="example-netexe"></a>範例 (Net.exe)  
 下列範例會在沒有仲裁情況下強制啟動節點叢集服務、設定 `NodeWeight = 1`，然後從新強制的節點列舉叢集節點狀態。  
  
```ms-dos  
net.exe stop clussvc  
net.exe start clussvc /forcequorum  
```  
  
##  <a name="follow-up-after-forcing-cluster-to-start-without-a-quorum"></a><a name="FollowUp"></a> 後續操作：在沒有仲裁的情況下強制啟動叢集之後  
  
-   在讓其他節點重新於線上工作之前，您必須重新評估及重新設定 NodeWeight 值，以正確建構新的仲裁。 否則，叢集可能會再次離線。  
  
     如需詳細資訊，請參閱 [WSFC 仲裁模式和投票組態 &#40;SQL Server&#41;](../../../sql-server/failover-clusters/windows/wsfc-quorum-modes-and-voting-configuration-sql-server.md)。  
  
-   本主題的程序是在發生意外的仲裁失敗時，讓 WSFC 叢集重新於線上工作的唯一步驟。  您可能也會想要採取額外步驟來阻止其他 WSFC 叢集節點干擾新的仲裁設定。  
  
-   其他 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 功能 (例如 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)]、資料庫鏡像和記錄傳送) 可能也需要執行後續動作來復原資料及完整重建高可用性。  
  
     **如需詳細資訊：＜＞**  
  
     [執行可用性群組的強制手動容錯移轉 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/perform-a-forced-manual-failover-of-an-availability-group-sql-server.md)  
  
     [在資料庫鏡像工作階段中強制服務 &#40;Transact-SQL&#41;](../../../database-engine/database-mirroring/force-service-in-a-database-mirroring-session-transact-sql.md)  
  
     [容錯移轉至記錄傳送次要 &#40;SQL Server&#41;](../../../database-engine/log-shipping/fail-over-to-a-log-shipping-secondary-sql-server.md)  
  
##  <a name="related-content"></a><a name="RelatedContent"></a> 相關內容  
  
-   [檢視容錯移轉叢集的事件和記錄檔](https://technet.microsoft.com/library/cc772342\(WS.10\).aspx)  
  
-   [Get-ClusterLog 容錯移轉叢集指令程式](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee461045(v=technet.10))  
  
## <a name="see-also"></a>另請參閱  
 [透過強制仲裁執行 WSFC 災害復原 &#40;SQL Server&#41;](../../../sql-server/failover-clusters/windows/wsfc-disaster-recovery-through-forced-quorum-sql-server.md)   
 [設定叢集仲裁 NodeWeight 設定](../../../sql-server/failover-clusters/windows/configure-cluster-quorum-nodeweight-settings.md)   
 [Windows PowerShell 中由工作焦點列出的容錯移轉叢集指令程式](https://technet.microsoft.com/library/ee619761\(WS.10\).aspx)  
  
