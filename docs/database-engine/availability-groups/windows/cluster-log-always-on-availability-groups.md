---
title: 產生可用性群組的 CLUSTER.LOG 並進行分析
description: 了解有關如何產生 Always On 可用性群組的叢集記錄並進行分析的詳細資料。
ms.custom: seo-lt-2019
ms.date: 06/14/2017
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: how-to
ms.assetid: 01a9e3c1-2a5f-4b98-a424-0ffc15d312cf
author: rothja
ms.author: jroth
ms.openlocfilehash: a03d616a289228695a46f4553e4123ceb9a4810e
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97643061"
---
# <a name="generate-and-analyze-the-clusterlog-for-an-always-on-availability-group"></a>產生 Always On 可用性群組的 CLUSTER.LOG 並進行分析
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]
  作為容錯移轉叢集資源，在 SQL Server、Windows Server 容錯移轉叢集服務 (WSFC) 叢集和 SQL Server 資源 DLL (hadrres.dll) 之間，存在著無法在 SQL Server 內監視的外部互動。 WSFC 記錄 (CLUSTER.LOG) 可診斷 WSFC 叢集或 SQL Server 資源 DLL 中的問題。 
  
## <a name="generate-cluster-log"></a>產生叢集記錄檔  
 您可以透過兩種方式產生叢集記錄檔：  
  
1.  在命令提示字元使用 `cluster /log /g` 命令。 此命令會產生叢集記錄檔到每個 WSFC 節點上的 \windows\cluster\reports 目錄。 這個方法的優點是，您可以使用 `/level` 選項來指定所產生記錄檔的詳細資訊層級。 缺點是您不能指定所產生叢集記錄檔的目的地目錄。 如需詳細資訊，請參閱[如何在 Windows Server 2008 容錯移轉叢集中建立 cluster.log](https://techcommunity.microsoft.com/t5/failover-clustering/how-to-create-the-cluster-log-in-windows-server-2008-failover/ba-p/371283) \(英文\)。  
  
2.  使用 [Get-ClusterLog](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee461045(v=technet.10)) \(英文\) PowerShell Cmdlet。 這個方法的優點是您可以將產生自所有節點的叢集記錄檔，集中到執行 Cmdlet 節點上的單一目的地目錄。 缺點是，您無法指定所產生記錄檔的詳細資訊層級。  
  
 下列 PowerShell 命令會產生最近 15 分鐘之內來自所有叢集節點的叢集記錄檔，並將它們放到目前的目錄中。 在具系統管理權限的 PowerShell 視窗中執行命令。  
  
```powershell  
Import-Module FailoverClusters   
Get-ClusterLog -TimeSpan 15 -Destination .  
```  
  
## <a name="always-on-log-verbosity"></a>Always On 記錄詳細資訊  
 您可以針對可用性群組增加 CLUSTER.LOG 中記錄的詳細資訊層級。 若要修改詳細資訊層級，請遵循下列的步驟：  
  
1.  從 [開始] 功能表，開啟 [容錯移轉叢集管理員]。  
  
2.  展開您的叢集和 [服務和應用程式] 節點，然後按一下可用性群組的名稱。  
  
3.  在詳細資料窗格中，以滑鼠右鍵按一下可用性群組資源並按一下 [屬性]。  
  
4.  按一下 [屬性] 索引標籤。  
  
5.  修改 **VerboseLogging** 屬性。 根據預設，**VerboseLogging** 是設為 `0`，這會報告資訊、警告和錯誤。 **VerboseLogging** 的設定範圍可從 `0` 到 `2`。  
  
6.  按一下 [確定]。  
  
7.  再以滑鼠右鍵按一下可用性群組資源，然後按一下 [讓此資源離線]。  
  
8.  再以滑鼠右鍵按一下可用性群組資源，然後按一下 [讓此資源上線]。  
  
## <a name="availability-group-resource-events"></a>可用性群組資源事件  
 下表顯示在針對可用性群組資源保留的 CLUSTER.LOG 中，您可以看見的不同事件類型。 如需 WSFC 中資源主控子系統 (RHS) 和資源控制監視器 (RCM) 的詳細資訊，請參閱 [Windows Server 2008 容錯移轉叢集中的資源主控子系統 (RHS)](/archive/blogs/askcore/resource-hosting-subsystem-rhs-in-windows-server-2008-failover-clusters) \(英文\)。  
  
|識別碼|來源|來自 CLUSTER.LOG 的範例|  
|----------------|------------|------------------------------|  
|前面加上 `[RES]` 和 `[hadrag]` 的訊息|hadrres.dll (Always On 資源 DLL)|00002cc4.00001264::2011/08/05-13:47:42.543 INFO  [RES] SQL Server 可用性群組 \<ag>:`[hadrag]` 離線要求。<br /><br /> 00002cc4.00003384::2011/08/05-13:47:42.558 ERR   [RES] SQL Server 可用性群組 \<ag>:`[hadrag]` 租用執行緒已終止<br /><br /> 00002cc4.00003384::2011/08/05-13:47:42.605 INFO  [RES] SQL Server 可用性群組 \<ag>:`[hadrag]` 釋出 SQL 陳述式<br /><br /> 00002cc4.00003384::2011/08/05-13:47:42.902 INFO  [RES] SQL Server 可用性群組 \<ag>:`[hadrag]` 從 SQL Server 中斷連線|  
|前面加上 `[RHS]` 的訊息|RHS.EXE (資源主控子系統，hadrres.dll 的主機處理序)|00000c40.00000a34::2011/08/10-18:42:29.498 INFO  [RHS] 資源 ag 已經離線。 RHS 即將報告資源狀態給 RCM。|  
|前面加上 `[RCM]` 的訊息|資源控制監視器 (叢集服務)|000011d0.00000f80::2011/08/05-13:47:42.480 INFO  [RCM] rcm::RcmGroup::Move:正在先將群組 'ag' 離線...<br /><br /> 000011d0.00000f80::2011/08/05-13:47:42.496 INFO  [RCM] TransitionToState(ag) Online-->OfflineCallIssued。|  
|RcmApi/ClusAPI|API 呼叫，通常表示 SQL Server 正在要求行動|000011d0.00000f80::2011/08/05-13:47:42.465 INFO  [RCM] rcm::RcmApi::MoveGroup: (ag, 2)|  
  
## <a name="debug-always-on-resource-dll-in-isolation"></a>以隔離方式針對 Always On 資源 DLL 進行偵錯  
 進行偵錯的最佳做法，是將您的叢集設定為以和其他資源 DLL 隔離的方式執行 Always On 資源 DLL (hadrres.dll)。 根據預設，WSFC 叢集會在單一的 rhs.exe 執行個體中執行所有的資源 DLL。 這會導致叢集內的所有資源共用相同的 rhs.exe 執行個體。 當您使用偵錯工具嘗試針對 hadrres.dll 進行偵錯時，暫停於中斷點可能會導致其他共用 rhs.exe 執行個體的資源也一併暫停。 此外，當您執行相同叢集中的多個可用性群組時，相同的設定可能會在您於中斷點暫停以針對某個可用性群組進行偵錯時，暫停所有的可用性群組。  
  
 若要從其他叢集資源 DLL (包括其他可用性群組) 隔離可用性群組，請執行下列動作，以在個別的 rhs.exe 處理序內執行 hadrres.dll：  
  
1.  開啟 [登錄編輯程式] 並巡覽至下列機碼：HKEY_LOCAL_MACHINE\Cluster\Resources。 此機碼包含所有資源的索引鍵，每個都有不同的 GUID。  
  
2.  尋找 [名稱] 值符合您可用性群組名稱的資源索引鍵。  
  
3.  將 **SeparateMonitor** 值變更為 **1**。  
  
4.  針對您在 WSFC 叢集中的可用性群組重新啟動叢集服務。  
  
