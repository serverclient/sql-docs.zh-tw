---
title: 原則式管理：可用性群組
description: Always On 可用性群組健全狀況模型會評估一組預先定義的原則式管理 (PBM) 原則。 您可以使用這些原則，在 SQL Server 中檢視可用性群組以及複本和資料庫的健全狀況。
ms.custom: seo-lt-2019
ms.date: 05/17/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: reference
helpviewer_keywords:
- Availability Groups [SQL Server], troubleshooting
- Availability Groups [SQL Server], policies
ms.assetid: afa5289c-641a-4c03-8749-44862384ec5f
author: cawrites
ms.author: chadam
ms.openlocfilehash: ac6918ca2bc7a89adbad0fe531e1bf94948a9090
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97641431"
---
# <a name="policy-based-management-for-operational-issues-with-always-on-availability-groups"></a>Always On 可用性群組操作問題的原則式管理
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]

  Always On 可用性群組健全狀況模型會評估一組預先定義的原則式管理 (PBM) 原則。 您可以使用這些原則，在 SQL Server 中檢視可用性群組以及其複本和資料庫的健全狀況。  
  
  
##  <a name="terms-and-definitions"></a><a name="TermsAndDefinitions"></a> 詞彙和定義  
 AlwaysOn 預先定義的原則  
 一組內建原則，可讓資料庫管理員檢查可用性群組及其可用性複本和資料庫是否符合 AlwaysOn 原則所定義的狀態。  
  
 [AlwaysOn 可用性群組](../../../database-engine/availability-groups/windows/always-on-availability-groups-sql-server.md)  
 提供資料庫鏡像之企業級替代方案的高可用性與災害復原解決方案。  
  
 可用性群組  
 一組一起容錯移轉之離散化使用者資料庫 (稱為 *「可用性資料庫」* (Availability Database)) 的容器。  
  
 「可用性複本」  
 特定 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體所裝載之可用性群組的具現化，其中維護屬於可用性群組之每個可用性資料庫的本機副本。 有兩種類型的可用性複本存在：單一 *「主要複本」* (Primary Replica) 以及一到四個 *「次要複本」* (Secondary Replica)。 針對給定可用性群組裝載可用性複本的伺服器執行個體必須位於單一 Windows Server 容錯移轉叢集 (WSFC) 叢集的不同節點上。  
  
 可用性資料庫  
 屬於可用性群組的資料庫。 對於每個可用性資料庫而言，可用性群組會維護單一讀寫複本 ( *「主要資料庫」* (Primary Database)) 以及一到四個唯讀複本 ( *「次要資料庫」* (Secondary Database))。  
  
 AlwaysOn 儀表板  
 提供可用性群組健全狀況之摘要檢視的 [!INCLUDE[ssManStudioFull](../../../includes/ssmanstudiofull-md.md)] 儀表板。 如需詳細資訊，請參閱本主題稍後的 [AlwaysOn 儀表板](#Dashboard)。  
  
##  <a name="predefined-policies-and-issues"></a><a name="Always OnPBM"></a> 預先定義的原則和問題  
 下表摘要說明預先定義的原則。  
  
|原則名稱|問題|類別 **&#42;**|Facet|  
|-----------------|-----------|--------------------|-----------|  
|WSFC 叢集狀態|[WSFC 叢集服務離線](../../../database-engine/availability-groups/windows/wsfc-cluster-service-is-offline.md)。|重大|SQL Server 的執行個體|  
|可用性群組線上狀態|[可用性群組為離線](../../../database-engine/availability-groups/windows/availability-group-is-offline.md)。|重大|可用性群組|  
|可用性群組自動容錯移轉整備|[可用性群組尚未準備進行自動容錯移轉](../../../database-engine/availability-groups/windows/availability-group-is-not-ready-for-automatic-failover.md)。|重大|可用性群組|  
|可用性複本資料同步處理狀態|[某些可用性複本並未同步處理資料](../../../database-engine/availability-groups/windows/some-availability-replicas-are-not-synchronizing-data.md)。|警告|可用性群組|  
|同步複本的資料同步處理狀態|[某些同步複本並未同步處理](../../../database-engine/availability-groups/windows/some-synchronous-replicas-are-not-synchronized.md)。|警告|可用性群組|  
|可用性複本角色狀態|[某些可用性複本沒有狀況良好的角色](../../../database-engine/availability-groups/windows/some-availability-replicas-do-not-have-a-healthy-role.md)。|警告|可用性群組|  
|可用性複本連接狀態|[部分可用性複本已中斷連接](../../../database-engine/availability-groups/windows/some-availability-replicas-are-disconnected.md)。|警告|可用性群組|  
|可用性複本的角色狀態|[可用性複本沒有狀況良好的角色](../../../database-engine/availability-groups/windows/availability-replica-does-not-have-a-healthy-role.md)。|重大|可用性複本|  
|可用性複本連接狀態|[可用性複本已中斷連接](../../../database-engine/availability-groups/windows/availability-replica-is-disconnected.md)。|重大|可用性複本|  
|可用性複本聯結狀態|[可用性複本未聯結](../../../database-engine/availability-groups/windows/availability-replica-is-not-joined.md)。|警告|可用性複本|  
|可用性複本資料同步處理狀態|[某個可用性資料庫的資料同步處理狀態不良](../../../database-engine/availability-groups/windows/data-synchronization-state-of-some-availability-database-is-not-healthy.md)。|警告|可用性複本|  
|可用性資料庫暫停狀態|[可用性資料庫已暫停](../../../database-engine/availability-groups/windows/availability-database-is-suspended.md)。|警告|可用性資料庫|  
|可用性資料庫聯結狀態|[次要資料庫並未聯結](../../../database-engine/availability-groups/windows/secondary-database-is-not-joined.md)。|警告|可用性資料庫|  
|可用性資料庫資料同步處理狀態|[可用性資料庫的資料同步狀態健康情況不佳](../../../database-engine/availability-groups/windows/data-synchronization-state-of-availability-database-is-not-healthy.md)。|警告|可用性資料庫|  
  
> [!IMPORTANT]
>  **&#42;** 對於 AlwaysOn 原則而言，類別名稱會當作識別碼使用。 變更 AlwaysOn 類別目錄的名稱會破壞其健全狀況評估功能。 因此，請勿修改 AlwaysOn 類別目錄的名稱。  
  
##  <a name="always-on-dashboard"></a><a name="Dashboard"></a> AlwaysOn 儀表板  
 AlwaysOn 儀表板會為您提供可用性群組健全狀況的摘要檢視。 AlwaysOn 儀表板包括下列功能：  
  
-   可讓您輕鬆地顯示有關給定可用性群組、其可用性複本和資料庫的詳細資料。  
  
-   顯示重要狀態的視覺指示，以便協助資料庫管理迅速地進行操作決策。  
  
-   提供疑難排解案例的啟動點。  
  
-   針對給定的操作問題，將特定 AlwaysOn 健全狀況原則違規的相關資訊以及矯正說明的連結填入 [原則評估結果]  對話方塊中。  
  
-   提供健全狀況擴充事件檢視器，以便顯示 AlwaysOn 特定問題的先前事件。  
  
-   如果容錯移轉可用性群組是可解決問題的矯正方式，就會提供[容錯移轉可用性群組精靈](../../../database-engine/availability-groups/windows/use-the-fail-over-availability-group-wizard-sql-server-management-studio.md)連結的啟動點。 此精靈將引導資料庫管理員完成手動容錯移轉程序。  
  
##  <a name="extending-the-always-on-health-model"></a><a name="ExtendHealthModel"></a> 擴充 AlwaysOn 健全狀況模型  
 擴充 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] 健全狀況模型就是建立您自己的使用者定義原則，並根據您所監控的物件類型將其置於某些類別目錄中。  在您改變幾個設定之後，AlwaysOn 儀表板將會自動評估您的使用者定義原則以及 AlwaysOn 預先定義的原則。  
  
 使用者定義的原則可以使用任何可用的 PBM Facet，包括 AlwaysOn 預先定義的原則所使用的 Facet (請參閱本主題稍早的 [預先定義的原則和問題](#Always OnPBM))。 伺服器 Facet 會提供用來監視 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] 健全狀況的下列屬性：(**IsHadrEnabled** 和 **HadrManagerStatus**)。 伺服器 Facet 也會提供用來監視 WSFC 叢集設定的下列原則：**ClusterQuorumType** 和 **ClusterQuorumState**。  
  
 如需詳細資訊，請參閱 [AlwaysOn 健全狀況模型第 2 部 -- 擴充健全狀況模型](/archive/blogs/sqlalwayson/the-alwayson-health-model-part-2-extending-the-health-model) (SQL Server AlwaysOn 團隊部落格)。  
  
##  <a name="related-tasks"></a><a name="RelatedTasks"></a> 相關工作  
  
-   [使用 AlwaysOn 原則檢視可用性群組的健全狀況 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/use-always-on-policies-to-view-the-health-of-an-availability-group-sql-server.md)  
  
-   [使用 AlwaysOn 儀表板 &#40;SQL Server Management Studio&#41;](../../../database-engine/availability-groups/windows/use-the-always-on-dashboard-sql-server-management-studio.md)  
  
-   [透過強制仲裁執行 WSFC 災害復原 &#40;SQL Server&#41;](../../../sql-server/failover-clusters/windows/wsfc-disaster-recovery-through-forced-quorum-sql-server.md)  
  
-   [在無仲裁情況下強制啟動 WSFC 叢集](../../../sql-server/failover-clusters/windows/force-a-wsfc-cluster-to-start-without-a-quorum.md)  
  
-   [執行可用性群組的強制手動容錯移轉 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/perform-a-forced-manual-failover-of-an-availability-group-sql-server.md)  
  
-   [疑難排解失敗的加入檔案作業 &#40;AlwaysOn 可用性群組&#41;](../../../database-engine/availability-groups/windows/troubleshoot-a-failed-add-file-operation-always-on-availability-groups.md)  
  
##  <a name="related-content"></a><a name="RelatedContent"></a> 相關內容  
  
-   [AlwaysOn 健全狀況模型第 1 部 -- 健全狀況模型架構](/archive/blogs/sqlalwayson/the-alwayson-health-model-part-1-health-model-architecture)  
  
-   [AlwaysOn 健全狀況模型第 2 部 -- 擴充健全狀況模型](/archive/blogs/sqlalwayson/the-alwayson-health-model-part-2-extending-the-health-model)  
  
-   [Microsoft SQL Server AlwaysOn 高可用性和災害復原方案指南](/previous-versions/sql/sql-server-2012/hh781257(v=msdn.10))  
  
## <a name="see-also"></a>另請參閱  
 [AlwaysOn 可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/always-on-availability-groups-sql-server.md)   
 [AlwaysOn 可用性群組概觀 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server.md)   
 [可用性群組的管理 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/administration-of-an-availability-group-sql-server.md)   
 [監視可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server.md)  
  
