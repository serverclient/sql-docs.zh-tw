---
title: 可用性群組的管理 (內容索引)
description: 此參考索引可將您連結至描述 Always On 可用性群組管理基本概念的文章，例如改變屬性、新增或移除複本、新增或移除資料庫、容錯移轉、設定接聽程式等。
ms.custom: seo-lt-2019
ms.date: 05/17/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: reference
helpviewer_keywords:
- Availability Groups [SQL Server], managing
ms.assetid: 0b7542fa-235e-413d-81bf-3eff9ee07480
author: cawrites
ms.author: chadam
ms.openlocfilehash: 414b407cc2adccf20f2f9d7dc0e01bfb171899f7
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97643298"
---
# <a name="administration-of-an-availability-group"></a>可用性群組的管理
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]
 在 [!INCLUDE[ssCurrent](../../../includes/sscurrent-md.md)] 中管理現有的 AlwaysOn 可用性群組包括下列一或多個工作：  
  
-   改變現有可用性副本的屬性 (例如，變更用戶端連接存取 (以設定可讀取的次要副本))；變更其容錯移轉模式、可用性模式或工作階段逾時設定。    
-   加入或移除次要副本。    
-   加入或移除資料庫。    
-   暫停或恢復資料庫。   
-   執行已規劃的手動容錯移轉 ( *「手動容錯移轉」* (Manual Failover)) 或強制手動容錯移轉 ( *「強制容錯移轉」* (Forced Failover))。    
-   建立或設定可用性群組接聽程式。    
-   管理給定可用性群組之 [可讀取的次要複本](../../../database-engine/availability-groups/windows/active-secondaries-readable-secondary-replicas-always-on-availability-groups.md) 。 這項作業包括以次要角色執行時，將一個或多個複本設定為唯讀存取，以及設定唯讀路由。    
-   管理給定可用性群組之 [次要複本上的備份](../../../database-engine/availability-groups/windows/active-secondaries-backup-on-secondary-replicas-always-on-availability-groups.md) 。 這項作業包括設定您希望執行備份作業的位置，然後編寫備份作業的指令碼以實作您的備份喜好設定。 您必須為裝載可用性複本的每一個 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體上之可用性群組中的每一個資料庫，編寫備份作業的指令碼。    
-   刪除可用性群組。    
-   針對作業系統升級進行 AlwaysOn 可用性群組的跨叢集移轉  
  
## <a name="configure-an-existing-availability-group"></a>設定現有的可用性群組
  
-   [將次要複本加入至可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/add-a-secondary-replica-to-an-availability-group-sql-server.md)  
  
-   [將次要複本從可用性群組移除 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/remove-a-secondary-replica-from-an-availability-group-sql-server.md)  
  
-   [將資料庫加入至可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/availability-group-add-a-database.md)    
-   [將次要資料庫從可用性群組移除 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/remove-a-secondary-database-from-an-availability-group-sql-server.md)  
  
-   [將主要資料庫從可用性群組移除 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/remove-a-primary-database-from-an-availability-group-sql-server.md)  
  
-   [設定彈性容錯移轉原則以控制自動容錯移轉的條件 &#40;AlwaysOn 可用性群組&#41;](../../../database-engine/availability-groups/windows/configure-flexible-automatic-failover-policy.md)  
  
 ## <a name="manage-an-availability-group"></a>管理可用性群組  
  
-   [設定可用性複本的備份 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/configure-backup-on-availability-replicas-sql-server.md)  
  
-   [執行可用性群組的已規劃手動容錯移轉 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/perform-a-planned-manual-failover-of-an-availability-group-sql-server.md)  
  
-   [執行可用性群組的強制手動容錯移轉 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/perform-a-forced-manual-failover-of-an-availability-group-sql-server.md)  
  
-   [移除可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/remove-an-availability-group-sql-server.md)  
  
 ## <a name="manage-an-availability-replica"></a>管理可用性複本  
  
-   [將次要複本加入至可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/add-a-secondary-replica-to-an-availability-group-sql-server.md)  
  
-   [將次要複本聯結至可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/join-a-secondary-replica-to-an-availability-group-sql-server.md)  
  
-   [將次要複本從可用性群組移除 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/remove-a-secondary-replica-from-an-availability-group-sql-server.md)  
  
-   [變更可用性複本的可用性模式 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/change-the-availability-mode-of-an-availability-replica-sql-server.md)  
  
-   [變更可用性複本的容錯移轉模式 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/change-the-failover-mode-of-an-availability-replica-sql-server.md)  
  
-   [設定可用性複本的備份 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/configure-backup-on-availability-replicas-sql-server.md)  
  
-   [設定可用性複本的唯讀存取 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/configure-read-only-access-on-an-availability-replica-sql-server.md)  
  
-   [設定可用性群組的唯讀路由 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/configure-read-only-routing-for-an-availability-group-sql-server.md)  
  
-   [變更可用性複本的工作階段逾時期限 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/change-the-session-timeout-period-for-an-availability-replica-sql-server.md)  
  
## <a name="manage-an-availability-database"></a>管理可用性資料庫  
  
-   [將資料庫加入至可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/availability-group-add-a-database.md)  
  
-   [將次要資料庫聯結至可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/join-a-secondary-database-to-an-availability-group-sql-server.md)  
  
-   [將主要資料庫從可用性群組移除 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/remove-a-primary-database-from-an-availability-group-sql-server.md)  
  
-   [將次要資料庫從可用性群組移除 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/remove-a-secondary-database-from-an-availability-group-sql-server.md)  
  
-   [暫止可用性資料庫 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/suspend-an-availability-database-sql-server.md)  
  
-   [繼續可用性資料庫 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/resume-an-availability-database-sql-server.md)  
  
## <a name="monitor-an-availability-group"></a>監視可用性群組
  
-   [監視可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server.md)  
  
 ## <a name="support-migrating-availability-groups-to-a-new-wsfc-cluster-cross-cluster-migration"></a>支援將可用性群組移轉至新的 WSFC 叢集 (跨叢集移轉)
  
-   [變更伺服器執行個體的 HADR 叢集內容 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/change-the-hadr-cluster-context-of-server-instance-sql-server.md)  
  
-   [讓可用性群組離線 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/take-an-availability-group-offline-sql-server.md)  
  
##  <a name="related-content"></a><a name="RelatedContent"></a> 相關內容  
  
-   **部落格：**  
  
     [SQL Server Always On 小組部落格：官方 SQL Server Always On 小組部落格](/archive/blogs/sqlalwayson/)    
[CSS SQL Server 工程師部落格](/archive/blogs/psssql/)  
  
-   **影片：**  
  
     [Microsoft SQL Server Code-Named "Denali" Always On Series,Part 1:Introducing the Next Generation High Availability Solution](https://channel9.msdn.com/Events/TechEd/NorthAmerica/2011/DBI302) (Microsoft SQL Server Code-Named "Denali" Always On 系列第 1 部分：新一代高可用性解決方案簡介)    
     [Microsoft SQL Server Code-Named "Denali" Always On Series,Part 2:Building a Mission-Critical High Availability Solution Using Always On](https://channel9.msdn.com/Events/TechEd/NorthAmerica/2011/DBI404) (Microsoft SQL Server Code-Named "Denali" Always On 系列第 2 部分：使用 Always On 建立關鍵任務的高可用性解決方案)  
  
-   **白皮書：**  
  
     [Microsoft 的 SQL Server 2012 白皮書](https://msdn.microsoft.com/library/hh403491.aspx)    
     [SQL Server 客戶諮詢團隊白皮書](https://techcommunity.microsoft.com/t5/DataCAT/bg-p/DataCAT/)  
  
## <a name="see-also"></a>另請參閱  
 [AlwaysOn 可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/always-on-availability-groups-sql-server.md)   
 [AlwaysOn 可用性群組概觀 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server.md)   
 [設定 AlwaysOn 可用性群組的伺服器執行個體 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/configuration-of-a-server-instance-for-always-on-availability-groups-sql-server.md)   
 [建立及設定可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/creation-and-configuration-of-availability-groups-sql-server.md)   
 [使用中次要：可讀取的次要複本 &#40;Always On 可用性群組&#41;](../../../database-engine/availability-groups/windows/active-secondaries-readable-secondary-replicas-always-on-availability-groups.md)   
 [使用中次要：在次要複本上備份 &#40;Always On 可用性群組&#41;](../../../database-engine/availability-groups/windows/active-secondaries-backup-on-secondary-replicas-always-on-availability-groups.md)   
 [可用性群組接聽程式、用戶端連接性及應用程式容錯移轉 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/listeners-client-connectivity-application-failover.md)   
 [AlwaysOn 可用性群組操作問題適用的 AlwaysOn 原則 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/always-on-policies-for-operational-issues-always-on-availability.md)   
 [監視可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server.md)   
 [Always On 可用性群組：互通性 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/always-on-availability-groups-interoperability-sql-server.md)   
 [AlwaysOn 可用性群組的 Transact-SQL 陳述式概觀 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups.md)   
 [AlwaysOn 可用性群組的 PowerShell Cmdlet 概觀 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server.md)  
  
