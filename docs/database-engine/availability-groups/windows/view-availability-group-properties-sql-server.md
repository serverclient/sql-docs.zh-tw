---
title: 檢視可用性群組屬性
description: '使用 SQL Server Management Studio (SSMS)、Transact-SQL (T-SQL) 或 SQL PowerShell，檢視 Always On 可用性群組的屬性。 '
ms.custom: seo-lt-2019
ms.date: 05/17/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: conceptual
helpviewer_keywords:
- Availability Groups [SQL Server]
ms.assetid: 61243c87-bd62-4510-863f-2a8f347caf1f
author: cawrites
ms.author: chadam
ms.openlocfilehash: c8f9adc01e608ad01b768148847e01a6373cb0b1
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97641674"
---
# <a name="view-availability-group-properties-sql-server"></a>檢視可用性群組屬性 (SQL Server)
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]
  此主題描述如何使用 [!INCLUDE[ssManStudioFull](../../../includes/ssmanstudiofull-md.md)] 中的 [!INCLUDE[tsql](../../../includes/tsql-md.md)] 或 [!INCLUDE[ssCurrent](../../../includes/sscurrent-md.md)]，檢視 AlwaysOn 可用性群組的可用性群組屬性。  
  
##  <a name="using-sql-server-management-studio"></a><a name="SSMSProcedure"></a> 使用 SQL Server Management Studio  
 **若要檢視及變更可用性群組的屬性**  
  
1.  在 [物件總管] 中，連接到裝載主要複本的伺服器執行個體，然後展開伺服器樹狀目錄。  
  
2.  依序展開 [Always On 高可用性]  節點和 [可用性群組]  節點。  
  
3.  以滑鼠右鍵按一下要檢視其屬性的可用性群組，然後選取 [屬性]  命令。  
  
4.  在 **[可用性群組屬性]** 對話方塊中，使用 **[一般]** 和 **[備份喜好設定]** 頁面檢視所選可用性群組的屬性，並在某些情況下變更這些屬性。 如需詳細資訊，請參閱[可用性群組屬性：新增可用性群組 &#40;General Page&#41;](../../../database-engine/availability-groups/windows/availability-group-properties-new-availability-group-general-page.md) 和[可用性群組屬性/新增可用性群組 &#40;Backup Preferences Page&#41;](../../../database-engine/availability-groups/windows/availability-group-properties-new-availability-group-backup-preferences-page.md)。  
  
     使用 **[權限]** 頁面來檢視與可用性群組相關聯的目前登入、角色和明確權限。 如需相關資訊，請參閱 [Permissions or Securables Page](../../../relational-databases/security/permissions-or-securables-page.md)。  
  
##  <a name="using-transact-sql"></a><a name="TsqlProcedure"></a> 使用 Transact-SQL  
 **若要檢視可用性群組的屬性和狀態**  
  
 若要針對伺服器執行個體裝載其可用性複本的可用性群組，查詢其屬性和狀態，請使用下列檢視：  
  
 [sys.availability_groups](../../../relational-databases/system-catalog-views/sys-availability-groups-transact-sql.md)  
 針對裝載可用性複本的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 本機執行個體的每一個可用性群組，各傳回一個資料列。 每一個資料列都包含可用性群組中繼資料的快取副本。  
  
 **資料行名稱：** group_id、name、resource_id、resource_group_id、failure_condition_level、health_check_timeout、automated_backup_preference、automated_backup_preference_desc  
  
 [sys.availability_groups_cluster](../../../relational-databases/system-catalog-views/sys-availability-groups-cluster-transact-sql.md)  
 針對 WSFC 叢集中的每一個可用性群組，各傳回一個資料列。 每個資料列都包含 Windows Server 容錯移轉叢集 (WSFC) 叢集中的可用性群組中繼資料。  
  
 **資料行名稱：** group_id、name、resource_id、resource_group_id、failure_condition_level、health_check_timeout、automated_backup_preference、automated_backup_preference_desc  
  
 [sys.dm_hadr_availability_group_states](../../../relational-databases/system-dynamic-management-views/sys-dm-hadr-availability-group-states-transact-sql.md)  
 針對擁有 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]本機執行個體之可用性複本的每一個可用性群組，各傳回一個資料列。 每個資料列會顯示定義給定之可用性群組健全狀況的狀態。  
  
 **資料行名稱：** group_id、primary_replica、primary_recovery_health、primary_recovery_health_desc、secondary_recovery_health、secondary_recovery_health_desc、synchronization_health、synchronization_health_desc  
  
##  <a name="related-tasks"></a><a name="RelatedTasks"></a> 相關工作  
 **若要檢視可用性群組的相關資訊**  
  
-   [檢視可用性複本屬性 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/view-availability-replica-properties-sql-server.md)  
  
-   [檢視可用性群組接聽程式屬性 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/view-availability-group-listener-properties-sql-server.md)  
  
-   [AlwaysOn 可用性群組操作問題適用的 AlwaysOn 原則 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/always-on-policies-for-operational-issues-always-on-availability.md)  
  
-   [使用 AlwaysOn 儀表板 &#40;SQL Server Management Studio&#41;](../../../database-engine/availability-groups/windows/use-the-always-on-dashboard-sql-server-management-studio.md)  
  
-   [監視可用性群組 &#40;Transact-SQL&#41;](../../../database-engine/availability-groups/windows/monitor-availability-groups-transact-sql.md)  
  
 **設定現有的可用性群組**  
  
-   [將次要複本加入至可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/add-a-secondary-replica-to-an-availability-group-sql-server.md)  
  
-   [將次要複本從可用性群組移除 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/remove-a-secondary-replica-from-an-availability-group-sql-server.md)  
  
-   [將資料庫加入至可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/availability-group-add-a-database.md)  
  
-   [將次要資料庫從可用性群組移除 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/remove-a-secondary-database-from-an-availability-group-sql-server.md)  
  
-   [建立或設定可用性群組接聽程式 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/create-or-configure-an-availability-group-listener-sql-server.md)  
  
-   [移除可用性群組接聽程式 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/remove-an-availability-group-listener-sql-server.md)  
  
-   [將主要資料庫從可用性群組移除 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/remove-a-primary-database-from-an-availability-group-sql-server.md)  
  
-   [移除可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/remove-an-availability-group-sql-server.md)  
  
 **手動容錯移轉可用性群組**  
  
-   [執行可用性群組的已規劃手動容錯移轉 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/perform-a-planned-manual-failover-of-an-availability-group-sql-server.md)  
  
-   [執行可用性群組的強制手動容錯移轉 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/perform-a-forced-manual-failover-of-an-availability-group-sql-server.md)  
  
## <a name="see-also"></a>另請參閱  
 [AlwaysOn 可用性群組概觀 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server.md)   
 [監視可用性群組 &#40;Transact-SQL&#41;](../../../database-engine/availability-groups/windows/monitor-availability-groups-transact-sql.md)   
 [AlwaysOn 可用性群組操作問題適用的 AlwaysOn 原則 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/always-on-policies-for-operational-issues-always-on-availability.md)  
  
  
