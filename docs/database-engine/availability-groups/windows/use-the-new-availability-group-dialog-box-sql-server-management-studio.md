---
title: SSMS 中的 [新增可用性群組] 對話方塊
description: 了解如何使用 SQL Server Management Studio 的 [新增可用性群組] 對話方塊來設定 Always On 可用性群組。
ms.custom: seo-lt-2019
ms.date: 05/17/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: conceptual
helpviewer_keywords:
- Availability Groups [SQL Server], creating
ms.assetid: 1b0a6421-fbd4-4bb4-87ca-657f4782c433
author: cawrites
ms.author: chadam
ms.openlocfilehash: abab816f9248d1981d62bfeb313130a8b71c4256
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97641754"
---
# <a name="use-the-new-availability-group-dialog-box-sql-server-management-studio"></a>使用新增可用性群組對話方塊 (SQL Server Management Studio)
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]
  此主題描述如何使用 [!INCLUDE[ssManStudioFull](../../../includes/ssmanstudiofull-md.md)] 的 [新增可用性群組] 對話方塊，在已啟用 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] 功能的 [!INCLUDE[ssCurrent](../../../includes/sscurrent-md.md)] 執行個體建立 AlwaysOn 可用性群組。 *「可用性群組」* (Availability Group) 會定義當做單一單位容錯移轉的一組使用者資料庫，以及支援容錯移轉的一組容錯移轉夥伴 (也稱為 *「可用性複本」* (Availability Replica))。  
  
> [!NOTE]  
>  如需可用性群組的簡介，請參閱 [AlwaysOn 可用性群組概觀 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server.md)中的 PowerShell，透過 PowerShell Cmdlet 建立及設定 AlwaysOn 可用性群組。  
   
> [!NOTE]  
>  如需建立可用性群組之其他方法的相關資訊，請參閱本主題稍後的 [相關工作](#RelatedTasks)。  
  
  
##  <a name="prerequisites"></a><a name="PrerequisitesRestrictions"></a> 必要條件  
  
-   建立可用性群組之前，請確認裝載可用性複本的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體位於相同 Windows Server 容錯移轉叢集 (WSFC) 容錯移轉叢集的不同 WSFC 節點上。 也請確認每個伺服器執行個體都已啟用 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] 且符合所有其他 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] 必要條件。 如需詳細資訊，強烈建議您閱讀 [AlwaysOn 可用性群組的必要條件、限制和建議 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/prereqs-restrictions-recommendations-always-on-availability.md)。  
  
-   在建立可用性群組之前，請確定將要裝載可用性複本的每個伺服器執行個體都擁有可完全運作的資料庫鏡像端點。 如需詳細資訊，請參閱 [資料庫鏡像端點 &#40;SQL Server&#41;](../../../database-engine/database-mirroring/the-database-mirroring-endpoint-sql-server.md)。  
  
-   若要使用 **[新增可用性群組]** 對話方塊，您需要知道將要裝載可用性複本的伺服器執行個體名稱。 您也需要知道要加入至新可用性群組的任何資料庫名稱，而且需要確定這些資料庫符合 [AlwaysOn 可用性群組的必要條件、限制和建議 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/prereqs-restrictions-recommendations-always-on-availability.md)。 如果您輸入無效值，新的可用性群組將無法運作。  
  
## <a name="limitations"></a><a name="Limitations"></a> 限制  
 **[新增可用性群組]** 對話方塊不會：  
  
-   建立可用性群組接聽程式。    
-   將次要複本聯結至可用性群組。    
-   執行初始資料同步處理。  
  
 如需這些設定工作的相關資訊，請參閱本主題稍後的＜[後續操作：建立可用性群組之後](#FollowUp)＞。  
  
##  <a name="permissions"></a><a name="Permissions"></a> 權限  
 需要 **系統管理員 (sysadmin)** 固定伺服器角色的成員資格，以及 CREATE AVAILABILITY GROUP 伺服器權限、ALTER ANY AVAILABILITY GROUP 權限或 CONTROL SERVER 權限。  
  
##  <a name="using-the-new-availability-group-dialog-box-sql-server-management-studio"></a><a name="SSMSProcedure"></a> 使用新增可用性群組對話方塊 (SQL Server Management Studio)  
 **建立可用性群組**  
  
1.  在 [物件總管] 中，連接到裝載主要複本的伺服器執行個體，然後按一下伺服器名稱。  
  
2.  展開 [AlwaysOn 高可用性] 節點。  
  
3.  以滑鼠右鍵按一下 [可用性群組] 節點，然後選取 [新增可用性群組] 命令。  
  
4.  這個命令會開啟 **[新增可用性群組]** 對話方塊。  
  
5.  在 **[一般]** 頁面上，使用 **[可用性群組名稱]** 欄位輸入新可用性群組的名稱。 這個名稱必須是有效的 SQL Server 識別碼，而且在 WSFC 叢集的所有可用性群組中是唯一的。 可用性群組名稱的最大長度為 128 個字元。  
  
6.  在 **[可用性資料庫]** 方格中，按一下 **[加入]** 並輸入要屬於此可用性群組的本機資料庫名稱。 為每個要加入的資料庫重複此步驟。 當您按一下 **[確定]** 時，對話方塊會驗證指定的資料庫是否符合屬於可用性群組的必要條件。 如需這些必要條件的相關資訊，請參閱 [AlwaysOn 可用性群組的必要條件、限制和建議 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/prereqs-restrictions-recommendations-always-on-availability.md)。  
  
7.  在 **[可用性資料庫]** 方格中，按一下 **[加入]** 並輸入要裝載次要複本的伺服器執行個體名稱。 此對話方塊不會嘗試連接到這些執行個體。 如果您指定不正確的伺服器名稱，次要複本會加入但無法連接。  
  
    > [!TIP]  
    >  如果您已經加入複本，但無法連接到主機伺服器執行個體，則可以移除該複本並加入新複本。 如需詳細資訊，請參閱[將次要複本從可用性群組移除 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/remove-a-secondary-replica-from-an-availability-group-sql-server.md) 和[將次要複本加入至可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/add-a-secondary-replica-to-an-availability-group-sql-server.md)。  
  
8.  在對話方塊的 **[選取頁面]** 窗格上，按一下 **[備份喜好設定]** 。 然後在 **[備份喜好設定]** 頁面上，根據複本角色指定應該進行備份的位置，並為將要裝載此可用性群組之可用性複本的每個伺服器執行個體指派備份優先權。 如需詳細資訊，請參閱[可用性群組屬性：新增可用性群組 &#40;備份喜好設定頁面&#41;](../../../database-engine/availability-groups/windows/availability-group-properties-new-availability-group-backup-preferences-page.md)。  
  
9. 若要建立可用性群組，請按一下 **[確定]** 。 這會導致對話方塊驗證該指定的資料庫是否符合必要條件。  
  
     若要結束對話方塊而不建立可用性群組，請按一下 **[取消]** 。  
  
##  <a name="follow-up-after-using-the-new-availability-group-dialog-box-to-create-an-availability-group"></a><a name="FollowUp"></a> 後續操作：使用 [新增可用性群組] 對話方塊建立可用性群組之後  
  
-   接著您需要連接到裝載此可用性群組之次要複本的每個伺服器執行個體，並完成下列步驟：  
  
    1.  將次要複本聯結至可用性群組。 如需詳細資訊，請參閱 [將次要複本聯結至可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/join-a-secondary-replica-to-an-availability-group-sql-server.md)。  
  
    2.  還原每個主要資料庫及其交易記錄的目前備份 (使用 RESTORE WITH NORECOVERY)。 如需詳細資訊，請參閱 [針對可用性群組手動準備次要資料庫 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/manually-prepare-a-secondary-database-for-an-availability-group-sql-server.md)中的 PowerShell，將次要資料庫聯結至 AlwaysOn 可用性群組。  
  
    3.  立即將每個新備妥的次要資料庫聯結至可用性群組。 如需詳細資訊，請參閱 [將次要資料庫聯結至可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/join-a-secondary-database-to-an-availability-group-sql-server.md)。  
  
-   建議您為新可用性群組建立可用性群組接聽程式。 這需要您連接到裝載目前主要複本的伺服器執行個體。 如需詳細資訊，請參閱 [建立或設定可用性群組接聽程式 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/create-or-configure-an-availability-group-listener-sql-server.md)。  
  
##  <a name="related-tasks"></a><a name="RelatedTasks"></a> 相關工作  
 **若要設定可用性群組和複本屬性**  
  
-   [變更可用性複本的可用性模式 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/change-the-availability-mode-of-an-availability-replica-sql-server.md)  
  
-   [變更可用性複本的容錯移轉模式 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/change-the-failover-mode-of-an-availability-replica-sql-server.md)  
  
-   [建立或設定可用性群組接聽程式 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/create-or-configure-an-availability-group-listener-sql-server.md)  
  
-   [設定彈性容錯移轉原則以控制自動容錯移轉的條件 &#40;AlwaysOn 可用性群組&#41;](../../../database-engine/availability-groups/windows/configure-flexible-automatic-failover-policy.md)  
  
-   [在新增或修改可用性複本時指定端點 URL &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/specify-endpoint-url-adding-or-modifying-availability-replica.md)  
  
-   [設定可用性複本的備份 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/configure-backup-on-availability-replicas-sql-server.md)  
  
-   [設定可用性複本的唯讀存取 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/configure-read-only-access-on-an-availability-replica-sql-server.md)  
  
-   [設定可用性群組的唯讀路由 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/configure-read-only-routing-for-an-availability-group-sql-server.md)  
  
-   [變更可用性複本的工作階段逾時期限 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/change-the-session-timeout-period-for-an-availability-replica-sql-server.md)  
  
 **若要完成可用性群組組態**  
  
-   [將次要複本聯結至可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/join-a-secondary-replica-to-an-availability-group-sql-server.md)  
  
-   [針對可用性群組手動準備次要資料庫 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/manually-prepare-a-secondary-database-for-an-availability-group-sql-server.md)  
  
-   [將次要資料庫聯結至可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/join-a-secondary-database-to-an-availability-group-sql-server.md)  
  
-   [建立或設定可用性群組接聽程式 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/create-or-configure-an-availability-group-listener-sql-server.md)  
  
 **建立可用性群組的其他方法**  
  
-   [使用可用性群組精靈 &#40;SQL Server Management Studio&#41;](../../../database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio.md)  
  
-   [建立可用性群組 &#40;Transact-SQL&#41;](../../../database-engine/availability-groups/windows/create-an-availability-group-transact-sql.md)  
  
-   [建立可用性群組 &#40;SQL Server PowerShell&#41;](../../../database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell.md)  
  
 **啟用 AlwaysOn 可用性群組**  
  
-   [啟用和停用 AlwaysOn 可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/enable-and-disable-always-on-availability-groups-sql-server.md)  
  
 **若要設定資料庫鏡像端點**  
  
-   [針對 AlwaysOn 可用性群組建立資料庫鏡像端點 &#40;SQL Server PowerShell&#41;](../../../database-engine/availability-groups/windows/database-mirroring-always-on-availability-groups-powershell.md)  
  
-   [建立 Windows 驗證的資料庫鏡像端點 &#40;Transact-SQL&#41;](../../../database-engine/database-mirroring/create-a-database-mirroring-endpoint-for-windows-authentication-transact-sql.md)  
  
-   [使用資料庫鏡像端點憑證 &#40;Transact-SQL&#41;](../../../database-engine/database-mirroring/use-certificates-for-a-database-mirroring-endpoint-transact-sql.md)  
  
-   [在新增或修改可用性複本時指定端點 URL &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/specify-endpoint-url-adding-or-modifying-availability-replica.md)  
  
 **疑難排解 AlwaysOn 可用性群組組態**  
  
-   [疑難排解 AlwaysOn 可用性群組組態 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/troubleshoot-always-on-availability-groups-configuration-sql-server.md)  
  
-   [疑難排解失敗的加入檔案作業 &#40;AlwaysOn 可用性群組&#41;](../../../database-engine/availability-groups/windows/troubleshoot-a-failed-add-file-operation-always-on-availability-groups.md)  
  
##  <a name="related-content"></a><a name="RelatedContent"></a> 相關內容  
  
-   [Microsoft SQL Server AlwaysOn 高可用性和災害復原方案指南](/previous-versions/sql/sql-server-2012/hh781257(v=msdn.10))  
  
## <a name="see-also"></a>另請參閱  
 [AlwaysOn 可用性群組概觀 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server.md)   
 [資料庫鏡像端點 &#40;SQL Server&#41;](../../../database-engine/database-mirroring/the-database-mirroring-endpoint-sql-server.md)   
 [可用性群組接聽程式、用戶端連接性及應用程式容錯移轉 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/listeners-client-connectivity-application-failover.md)   
 [AlwaysOn 可用性群組的必要條件、限制和建議 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/prereqs-restrictions-recommendations-always-on-availability.md)  
  
