---
title: 執行可用性群組的已規劃手動容錯移轉
description: 本主題描述如何執行 Always On 可用性群組的已規劃手動容錯移轉。
ms.custom: seodec18
ms.date: 10/25/2017
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: conceptual
f1_keywords:
- sql13.swb.availabilitygroup.manualfailover.f1
helpviewer_keywords:
- Availability Groups [SQL Server], failover
- failover [SQL Server], AlwaysOn Availability Groups
ms.assetid: 419f655d-3f9a-4e7d-90b9-f0bab47b3178
author: cawrites
ms.author: chadam
ms.openlocfilehash: 3cf135ca98a61382e12973b93eb30ab06ad6f4dd
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97642564"
---
# <a name="perform-a-planned-manual-failover-of-an-always-on-availability-group-sql-server"></a>執行 Always On 可用性群組的已規劃手動容錯移轉 (SQL Server)
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]
本主題描述如何使用 [!INCLUDE[ssManStudioFull](../../../includes/ssmanstudiofull-md.md)]、[!INCLUDE[tsql](../../../includes/tsql-md.md)] 或 [!INCLUDE[ssCurrent](../../../includes/sscurrent-md.md)] 中的 PowerShell，在不遺失資料的情況下針對 AlwaysOn 可用性群組執行手動容錯移轉 (*「已規劃的手動容錯移轉」*(Planned Manual Failover))。 可用性群組會在可用性複本層級容錯移轉。 規劃的手動容錯移轉和任何一個 AlwaysOn 可用性群組容錯移轉一樣，會將次要複本轉為主要角色。 同時，容錯移轉也會將先前的主要複本轉成次要角色。  
  
只有在主要複本和目標次要複本正在以同步認可模式執行且目前已同步時，才會支援規劃的手動容錯移轉。 規劃的手動容錯移轉會保留聯結至目標次要複本上可用性群組的次要資料庫中的所有資料。 之前的主要複本轉成次要角色之後，其資料庫會變成次要資料庫。 接著，這些資料庫會開始與新的主要資料庫同步。 在將它們全部轉換成 SYNCHRONIZED 狀態之後，新的次要複本就會變成有資格當做未來已規劃之手動容錯移轉的目標。  
  
> [!NOTE]  
>  如果次要和主要複本都設定成自動容錯移轉模式，在次要複本同步完畢之後，其也可作為自動容錯移轉的目標。 如需詳細資訊，請參閱[可用性模式 &#40;AlwaysOn 可用性群組&#41;](../../../database-engine/availability-groups/windows/availability-modes-always-on-availability-groups.md)。  
   
##  <a name="before-you-begin"></a><a name="BeforeYouBegin"></a>開始之前 

>[!IMPORTANT]
>若要在不使用叢集管理員的情況下容錯移轉讀取級別可用性群組，須遵循特定程序。 當可用性群組的 CLUSTER_TYPE = NONE 時，請遵循[容錯移轉讀取級別可用性群組上的主要複本](#fail-over-the-primary-replica-on-a-read-scale-availability-group)下的程序。

###  <a name="limitations-and-restrictions"></a><a name="Restrictions"></a> 限制事項 
  
- 目標次要複本接受命令之後，容錯移轉命令就會傳回。 不過，在可用性群組完成容錯移轉之後，會以非同步方式復原資料庫。 
- 容錯移轉時，可能不會保留可用性群組內跨資料庫的一致性。 
  
    > [!NOTE] 
    >  跨資料庫和分散式交易的支援會因 SQL Server 和作業系統版本而不同。 如需詳細資訊，請參閱 [AlwaysOn 可用性群組和資料庫鏡像的跨資料庫交易和分散式交易 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/transactions-always-on-availability-and-database-mirroring.md)。 
  
###  <a name="prerequisites-and-restrictions"></a><a name="Prerequisites"></a> 必要條件和限制 
  
-   目標次要複本和主要複本都必須在同步認可可用性模式下執行。 
-   目標次要複本目前必須與主要複本同步。 該次要複本上的所有次要資料庫皆必須聯結至可用性群組。 其也必須與其對應的主要資料庫同步 (亦即，本機次要資料庫必須為 SYNCHRONIZED)。 
  
    > [!TIP] 
    >  若要判斷次要複本的容錯移轉整備，請查詢 [sys.dm_hadr_database_replica_cluster_states](../../../relational-databases/system-dynamic-management-views/sys-dm-hadr-database-replica-cluster-states-transact-sql.md) 動態管理檢視中的 **is_failover_ready** 資料行。 或者，您可以查看 [AlwaysOn 群組儀表板](../../../database-engine/availability-groups/windows/use-the-always-on-dashboard-sql-server-management-studio.md)的 **容錯移轉整備** 資料行。 
-   只有在目標次要複本上才支援這個工作。 您必須連接到裝載目標次要複本的伺服器執行個體。 
  
###  <a name="security"></a><a name="Security"></a> Security 
  
####  <a name="permissions"></a><a name="Permissions"></a> 權限 
 可用性群組需要 ALTER AVAILABILITY GROUP 權限。 同時也需要 CONTROL AVAILABILITY GROUP、ALTER ANY AVAILABILITY GROUP 或 CONTROL SERVER 權限。 
  
##  <a name="use-sql-server-management-studio"></a><a name="SSMSProcedure"></a> 使用 SQL Server Management Studio 
 若要手動容錯移轉可用性群組： 
  
1. 在物件總管中，連線至裝載需要容錯移轉之可用性群組次要複本的伺服器執行個體。 展開伺服器樹狀目錄。 
  
2. 依序展開 **[AlwaysOn 高可用性]** 節點和 **[可用性群組]** 節點。 
  
3. 以滑鼠右鍵按一下要容錯移轉的可用性群組，然後選取 [容錯移轉]  。 
  
4. 容錯移轉可用性群組精靈隨即啟動。 如需詳細資訊，請參閱[使用容錯移轉可用性群組精靈 &#40;SQL Server Management Studio&#41;](../../../database-engine/availability-groups/windows/use-the-fail-over-availability-group-wizard-sql-server-management-studio.md)。 
  
##  <a name="use-transact-sql"></a><a name="TsqlProcedure"></a> 使用 Transact-SQL 
 若要手動容錯移轉可用性群組： 
  
1. 連接到裝載目標次要複本的伺服器執行個體。 
  
2. 使用 [ALTER AVAILABILITY GROUP](../../../t-sql/statements/alter-availability-group-transact-sql.md) 陳述式，如下所示： 
  
     ALTER AVAILABILITY GROUP *group_name* FAILOVER 
  
     在陳述式中，*group_name* 是可用性群組的名稱。 
  
     下列範例會將 *MyAg* 可用性群組手動容錯移轉到連線的次要複本： 
  
    ```  
    ALTER AVAILABILITY GROUP MyAg FAILOVER;  
    ```  
  
##  <a name="use-powershell"></a><a name="PowerShellProcedure"></a> 使用 PowerShell 
 若要手動容錯移轉可用性群組： 
  
1. 將目錄 (**cd**) 變更為裝載目標次要複本的伺服器執行個體。 
  
2. 使用 **Switch-SqlAvailabilityGroup** Cmdlet。 
  
    > [!NOTE] 
    >  若要檢視 Cmdlet 的語法，請在 **PowerShell 環境中使用** Get-Help [!INCLUDE[ssCurrent](../../../includes/sscurrent-md.md)] Cmdlet。 如需詳細資訊，請參閱[取得 SQL Server PowerShell 的說明](../../../powershell/sql-server-powershell.md)。 
  
     下列範例會將 *MyAg* 可用性群組手動容錯移轉到位於指定路徑的次要複本。 
  
    ```  
    Switch-SqlAvailabilityGroup -Path SQLSERVER:\Sql\SecondaryServer\InstanceName\AvailabilityGroups\MyAg  
    ```  
  
    若要設定和使用 SQL Server PowerShell 提供者： 
  
    -   [SQL Server PowerShell 提供者](../../../powershell/sql-server-powershell-provider.md) 
    -   [取得 SQL Server PowerShell 的說明](../../../powershell/sql-server-powershell.md) 

##  <a name="follow-up-after-you-manually-fail-over-an-availability-group"></a><a name="FollowUp"></a> 後續操作：手動容錯移轉可用性群組之後 
 如果您在可用性群組的 [!INCLUDE[ssFosAuto](../../../includes/ssfosauto-md.md)] 外部容錯移轉，請調整 Windows Server 容錯移轉叢集節點的仲裁投票，以反映您新的可用性群組設定。 如需詳細資訊，請參閱 [SQL Server 的 Windows Server 容錯移轉叢集 &#40;WSFC&#41;](../../../sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server.md). 

<a name = "ReadScaleOutOnly"><a/>

## <a name="fail-over-the-primary-replica-on-a-read-scale-availability-group"></a>容錯移轉讀取級別可用性群組上的主要複本

[!INCLUDE[Force failover](../../../includes/ss-force-failover-read-scale-out.md)]

## <a name="see-also"></a>另請參閱 

 * [AlwaysOn 可用性群組的概觀 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server.md) 
 * [容錯移轉及容錯移轉模式 &#40;AlwaysOn 可用性群組&#41;](../../../database-engine/availability-groups/windows/failover-and-failover-modes-always-on-availability-groups.md) 
 * [執行可用性群組的強制手動容錯移轉 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/perform-a-forced-manual-failover-of-an-availability-group-sql-server.md) 
  
