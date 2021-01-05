---
title: 可用性群組：高可用性和災害復原解決方案
description: Always On 可用性群組是針對資料庫鏡像提供企業級替代方案的高可用性與災害復原解決方案，且具有更強大的功能。 了解此功能的基本概念和功能。
ms.custom: seodec18
ms.date: 04/23/2019
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: conceptual
helpviewer_keywords:
- Availability Groups [SQL Server], about
- secondary replicas, see Availability Groups [SQL Server]
- availability [SQL Server]
- AlwaysOn [SQL Server], see Availability Groups [SQL Server]
- Availability Groups [SQL Server]
ms.assetid: aa427606-8422-4656-b205-c9e665ddc8c1
author: cawrites
ms.author: chadam
ms.openlocfilehash: 7f9b06bd6805e75c053037b18fc6870ef1469500
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97642184"
---
# <a name="always-on-availability-groups-a-high-availability-and-disaster-recovery-solution"></a>Always On 可用性群組：高可用性和災害復原解決方案
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]

  [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] 功能是提供資料庫鏡像之企業級替代方案的高可用性與災害復原解決方案。 在 [!INCLUDE[ssSQL11](../../../includes/sssql11-md.md)]中導入的 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] 可讓企業將一組使用者資料庫的可用性提高到最大程度。 *「可用性群組」* (Availability Group) 支援一組可一起容錯移轉之離散化使用者資料庫的容錯移轉環境，也就是所謂的 *「可用性資料庫」* (Availability Database)。 可用性群組支援一組讀寫的主要資料庫，以及一到八組對應的次要資料庫。 此外，您可以將次要資料庫用於唯讀存取及/或某些備份作業。  
  
 可用性群組會在可用性複本層級容錯移轉。 容錯移轉不是因資料庫問題 (例如資料庫因為資料檔案遺失而變得可疑、資料庫刪除或交易記錄損毀) 而造成的。  
 
 >[!NOTE]
 >AlwaysOn 可用性群組是這個可用性功能的完整正式名稱。 縮寫是 AG，而不是 AOAG 或 AAG。 
  
##  <a name="benefits"></a><a name="Benefits"></a> 優點  
 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] 提供了一組豐富的選項，可改善資料庫可用性並實現改善的資源使用方式。 關鍵元件如下：  
  
-   最多支援九個可用性複本。 *「可用性複本」* (Availability Replica) 是特定 SQL Server 執行個體所裝載之可用性群組的具現化，其中維護屬於可用性群組之每個可用性資料庫的本機複本。 每個可用性群組都支援一個主要複本和最多八個次要複本。 如需詳細資訊，請參閱 [AlwaysOn 可用性群組概觀 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server.md)。  
  
    > [!IMPORTANT]  
    >  每個可用性複本都必須位在單一 Windows Server 容錯移轉叢集 (WSFC) 叢集的不同節點。 如需可用性群組之必要條件、限制和建議的詳細資訊，請參閱 [AlwaysOn 可用性群組的必要條件、限制和建議 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/prereqs-restrictions-recommendations-always-on-availability.md)。  
  
-   支援替代可用性模式，如下所示：  
  
    -   非同步認可模式  。 這種可用性模式是一種當可用性複本分散距離相當遠時仍可正常運作的災害復原方案。  
  
    -   同步認可模式  。 這種可用性模式強調的是高可用性和資料保護而非效能，但是相對地增加了交易延遲。 給定的可用性群組最多可支援三個同步認可的可用性複本，包括目前的主要複本。  
  
     如需詳細資訊，請參閱 [可用性模式 &#40;AlwaysOn 可用性群組&#41;](../../../database-engine/availability-groups/windows/availability-modes-always-on-availability-groups.md)或 PowerShell，針對 AlwaysOn 可用性群組執行規劃的手動容錯移轉或強制手動容錯移轉 (強制容錯移轉)。 

     [!INCLUDE[sql-server-2019](../../../includes/sssqlv15-md.md)] 會將同步複本的數目上限，從 [!INCLUDE[ssSQL17](../../../includes/sssql17-md.md)] 中最多為 3 的狀態增加至 5。 您可以設定這五個複本的群組，使其在群組內具備自動容錯移轉。 有一個主要複本，再加上四個同步次要複本。
  
-   支援許多可用性群組容錯移轉形式：自動容錯移轉、規劃的手動容錯移轉 (通常只稱為「手動容錯移轉」)，以及強制手動容錯移轉 (通常只稱為「強制容錯移轉」)。 如需詳細資訊，請參閱本主題稍後的 [容錯移轉及容錯移轉模式 &#40;AlwaysOn 可用性群組&#41;](../../../database-engine/availability-groups/windows/failover-and-failover-modes-always-on-availability-groups.md)。  
  
-   可讓您將給定的可用性複本設定為支援下列其中一種或兩種使用中次要功能：  
  
    -   唯讀連接存取，讓複本的唯讀連接能夠在以次要複本的方式執行時存取並讀取其資料庫。 如需詳細資訊，請參閱 [使用中次要：可讀取的次要複本 &#40;AlwaysOn 可用性群組&#41;](../../../database-engine/availability-groups/windows/active-secondaries-readable-secondary-replicas-always-on-availability-groups.md)中心概念。  
  
    -   以次要複本的方式執行時，針對其資料庫執行備份作業。 如需詳細資訊，請參閱 [使用中次要：在次要複本上備份 &#40;AlwaysOn 可用性群組&#41;](../../../database-engine/availability-groups/windows/active-secondaries-backup-on-secondary-replicas-always-on-availability-groups.md)。  
  
     利用使用中次要功能可透過更善用次要硬體資源，改善 IT 效率並降低成本。 此外，透過將讀取意圖應用程式和備份作業卸載至次要複本，有助於提高主要複本的效能。  
  
-   支援每個可用性群組的可用性群組接聽程式。 「可用性群組接聽程式」  是用戶端可連接的伺服器名稱，以便存取 AlwaysOn 可用性群組之主要或次要複本中的資料庫。 可用性群組接聽程式會將內送連接導向至主要複本或唯讀次要複本。 接聽程式會在可用性群組容錯移轉之後提供快速應用程式容錯移轉。 如需詳細資訊，請參閱[可用性群組接聽程式、用戶端連接性及應用程式容錯移轉 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/listeners-client-connectivity-application-failover.md)。  
  
-   支援彈性容錯移轉原則，以便有效控制可用性群組容錯移轉。 如需詳細資訊，請參閱本主題稍後的 [容錯移轉及容錯移轉模式 &#40;AlwaysOn 可用性群組&#41;](../../../database-engine/availability-groups/windows/failover-and-failover-modes-always-on-availability-groups.md)。  
  
-   支援防止頁面損毀的自動頁面修復。 如需詳細資訊，請參閱本主題稍後的 [自動修復頁面 &#40;可用性群組：資料庫鏡像&#41;](../../../sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring.md)。  
  
-   支援加密和壓縮，可提供安全且高效能的傳輸方式。  
  
-   提供一組整合式工具，可簡化可用性群組的部署和管理作業，包括：  
  
    -   [!INCLUDE[tsql](../../../includes/tsql-md.md)] DDL 陳述式。 如需詳細資訊，請參閱 [AlwaysOn 可用性群組的 Transact-SQL 陳述式概觀 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups.md)。  
  
    -   [!INCLUDE[ssManStudioFull](../../../includes/ssmanstudiofull-md.md)] 工具，如下所示：  
  
        -   [!INCLUDE[ssAoNewAgWiz](../../../includes/ssaonewagwiz-md.md)] 會建立及設定可用性群組。 在某些環境中，此精靈還可以自動準備次要資料庫並且為每個資料庫啟動資料同步處理。 如需詳細資訊，請參閱[使用新增可用性群組對話方塊 &#40;SQL Server Management Studio&#41;](../../../database-engine/availability-groups/windows/use-the-new-availability-group-dialog-box-sql-server-management-studio.md)。  
  
        -   [!INCLUDE[ssAoAddDbWiz](../../../includes/ssaoadddbwiz-md.md)]會將一個或多個主要資料庫加入至現有可用性群組。 在某些環境中，此精靈還可以自動準備次要資料庫並且為每個資料庫啟動資料同步處理。 如需詳細資訊，請參閱 [使用將資料庫加入至可用性群組精靈 (SQL Server)](../../../database-engine/availability-groups/windows/availability-group-add-database-to-group-wizard.md)。  
  
        -   [!INCLUDE[ssAoAddRepWiz](../../../includes/ssaoaddrepwiz-md.md)] 會將一個或多個次要複本加入至現有可用性群組。 在某些環境中，此精靈還可以自動準備次要資料庫並且為每個資料庫啟動資料同步處理。 如需詳細資訊，請參閱[使用將複本加入至可用性群組精靈 &#40;SQL Server Management Studio&#41;](../../../database-engine/availability-groups/windows/use-the-add-replica-to-availability-group-wizard-sql-server-management-studio.md)。  
  
        -   [!INCLUDE[ssAoFoAgWiz](../../../includes/ssaofoagwiz-md.md)]會起始可用性群組的手動容錯移轉。 根據您指定為容錯移轉目標之次要複本的組態和狀態，此精靈可以執行規劃的手動容錯移轉或強制手動容錯移轉。 如需詳細資訊，請參閱[使用容錯移轉可用性群組精靈 &#40;SQL Server Management Studio&#41;](../../../database-engine/availability-groups/windows/use-the-fail-over-availability-group-wizard-sql-server-management-studio.md)。  
  
    -   [!INCLUDE[ssAoDash](../../../includes/ssaodash-md.md)] 會監視 AlwaysOn 可用性群組、可用性複本和可用性資料庫，以及評估 AlwaysOn 原則的結果。 如需詳細資訊，請參閱[使用 AlwaysOn 儀表板 &#40;SQL Server Management Studio&#41;](../../../database-engine/availability-groups/windows/use-the-always-on-dashboard-sql-server-management-studio.md)。  
  
    -   [物件總管詳細資料] 窗格會顯示現有可用性群組的基本資訊。 如需詳細資訊，請參閱[使用物件總管詳細資料監視可用性群組 &#40;SQL Server Management Studio&#41;](../../../database-engine/availability-groups/windows/use-object-explorer-details-to-monitor-availability-groups.md)。  
  
    -   PowerShell 指令程式。 如需詳細資訊，請參閱 [AlwaysOn 可用性群組的 PowerShell Cmdlet 概觀 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server.md)。  
  
##  <a name="terms-and-definitions"></a><a name="TermsAndDefinitions"></a> 詞彙和定義  
 **可用性群組**  
 一組一起容錯移轉之資料庫 (「可用性資料庫」  ) 的容器。  
  
 **可用性資料庫**  
 屬於可用性群組的資料庫。 對於每個可用性資料庫而言，可用性群組會維護單一讀寫複本 (「主要資料庫」  ) 以及一到八個唯讀複本 (「次要資料庫」  )。  
  
 **主要資料庫**  
 可用性資料庫的讀寫複本。  
  
 **次要資料庫**  
 可用性資料庫的唯讀複本。  
  
 **可用性複本**  
 特定 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體所裝載之可用性群組的具現化，它會維護屬於可用性群組之每個可用性資料庫的本機副本。 有兩種類型的可用性複本存在：單一 *「主要複本」* 以及一到八個 *「次要複本」* 。  
  
 **主要複本**  
 可用性複本，該複本可讓主要資料庫用於用戶端的讀寫連接，同時也將每個主要資料庫的交易記錄檔記錄傳送到每個次要複本。  
  
 **次要複本**  
 可用性複本，該複本會維護每個可用性資料庫的次要副本，並且當做可用性群組的潛在容錯移轉目標。 (選擇性) 可支援以唯讀方式存取次要資料庫的次要複本，可以支援在次要資料庫上建立備份。  
  
 **可用性群組接聽程式**  
 用戶端可連接的伺服器名稱，以便存取 AlwaysOn 可用性群組之主要或次要複本中的資料庫。 可用性群組接聽程式會將內送連接導向至主要複本或唯讀次要複本。  
  
> [!NOTE]  
>  如需詳細資訊，請參閱 [AlwaysOn 可用性群組概觀 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server.md)。  
  
##  <a name="interoperability-and-coexistence-with-other-database-engine-features"></a><a name="Interoperability"></a> 與其他 Database Engine 功能的互通性和共存性  
 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] 可搭配 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]的下列功能或元件使用：  
  
-   [關於異動資料擷取 &#40;SQL Server&#41;](../../../relational-databases/track-changes/about-change-data-capture-sql-server.md)  
  
-   [關於變更追蹤 &#40;SQL Server&#41;](../../../relational-databases/track-changes/about-change-tracking-sql-server.md)  
  
-   [自主資料庫](../../../relational-databases/databases/contained-databases.md)  
  
-   [資料庫加密](../../../relational-databases/security/encryption/transparent-data-encryption.md)  
  
-   [資料庫快照集](../../../database-engine/availability-groups/windows/database-snapshots-with-always-on-availability-groups-sql-server.md)  
  
-   [FILESTREAM](../../../relational-databases/blob/filestream-sql-server.md)  
  
-   [FileTable](../../../relational-databases/blob/filetables-sql-server.md)  
  
-   [記錄傳送](../../../database-engine/log-shipping/about-log-shipping-sql-server.md)  
  
-   [遠端 Blob 存放區 (RBS)](../../../relational-databases/blob/remote-blob-store-rbs-sql-server.md)  
  
-   [複寫](../../../relational-databases/replication/sql-server-replication.md)  
  
-   [Service Broker](../../../database-engine/configure-windows/sql-server-service-broker.md)  
  
-   [SQL Server Agent](../../../ssms/agent/sql-server-agent.md)  
  
-   [Reporting Services](../../../database-engine/availability-groups/windows/reporting-services-with-always-on-availability-groups-sql-server.md)  
  
> [!WARNING]  
>  如需使用其他功能搭配 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] 之限制事項的資訊，請參閱 [AlwaysOn 可用性群組：互通性 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/always-on-availability-groups-interoperability-sql-server.md)。  
  
##  <a name="related-tasks"></a><a name="RelatedTasks"></a> 相關工作  
  
-   [開始使用 AlwaysOn 可用性群組 &#40;SQL Server &#41;](../../../database-engine/availability-groups/windows/getting-started-with-always-on-availability-groups-sql-server.md)  
  
##  <a name="related-content"></a><a name="RelatedContent"></a> 相關內容  
  
-   **部落格：**  
  
     [SQL Server AlwaysOn 團隊部落格：官方 SQL Server AlwaysOn 團隊部落格](/archive/blogs/sqlalwayson/)  
  
     [CSS SQL Server 工程師部落格](/archive/blogs/psssql/)  
  
-   **影片：**  
  
     [Microsoft SQL Server Code-Named "Denali" AlwaysOn Series,Part 1: Introducing the Next Generation High Availability Solution (Microsoft SQL Server 代碼 "Denali" AlwaysOn 系列第一部分：新一代高可用性解決方案簡介)](https://channel9.msdn.com/Events/TechEd/NorthAmerica/2011/DBI302)  
  
     [Microsoft SQL Server Code-Named "Denali" Always On Series,Part 2: Building a Mission-Critical High Availability Solution Using Always On (Microsoft SQL Server 代碼 "Denali" AlwaysOn 系列第二部分：使用 AlwaysOn 建立任務關鍵性高可用性解決方案)](https://channel9.msdn.com/Events/TechEd/NorthAmerica/2011/DBI404)  
  
-   **白皮書：**  
  
     [Microsoft SQL Server AlwaysOn 高可用性和災害復原方案指南](/previous-versions/sql/sql-server-2012/hh781257(v=msdn.10))  
  
     [Microsoft 的 SQL Server 2012 白皮書](https://social.technet.microsoft.com/wiki/contents/articles/13146.white-paper-gallery-for-sql-server.aspx#[Category]SQLServer2012)  
  
     [SQL Server 客戶諮詢團隊白皮書](https://techcommunity.microsoft.com/t5/DataCAT/bg-p/DataCAT/)  
  
## <a name="see-also"></a>另請參閱  
 [AlwaysOn 可用性群組概觀 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server.md)   
 [AlwaysOn 可用性群組的必要條件、限制和建議 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/prereqs-restrictions-recommendations-always-on-availability.md)   
 [設定 AlwaysOn 可用性群組的伺服器執行個體 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/configuration-of-a-server-instance-for-always-on-availability-groups-sql-server.md)   
 [建立及設定可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/creation-and-configuration-of-availability-groups-sql-server.md)   
 [可用性群組的管理 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/administration-of-an-availability-group-sql-server.md)   
 [監視可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server.md)   
 [AlwaysOn 可用性群組的 Transact-SQL 陳述式概觀 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups.md)   
 [AlwaysOn 可用性群組的 PowerShell Cmdlet 概觀 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server.md)  
  
