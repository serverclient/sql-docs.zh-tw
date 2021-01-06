---
title: 將次要複本新增至可用性群組
description: 了解如何使用 Transact-SQL (T-SQL)、PowerShell 或 SQL Server Management Studio (SSMS) 中的 [可用性群組精靈]，將次要複本新增至 Always On 可用性群組。
ms.custom: seodec18
ms.date: 05/18/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: how-to
helpviewer_keywords:
- Availability Groups [SQL Server], availability replicas
- Availability Groups [SQL Server], configuring
ms.assetid: 6669dcce-85f9-495f-aadf-7f62cff4a9da
author: cawrites
ms.author: chadam
ms.openlocfilehash: 645fae8fc567eff8d8e4daa62d3b19fec2c2fdc1
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97643805"
---
# <a name="add-a-secondary-replica-to-an-always-on-availability-group"></a>將次要複本新增至 Always On 可用性群組
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]
  此主題描述如何使用 [!INCLUDE[ssManStudioFull](../../../includes/ssmanstudiofull-md.md)]、 [!INCLUDE[tsql](../../../includes/tsql-md.md)]或 [!INCLUDE[ssCurrent](../../../includes/sscurrent-md.md)]中的 PowerShell，將次要複本加入現有的 AlwaysOn 可用性群組中。  

  
##  <a name="prerequisites-and-restrictions"></a><a name="PrerequisitesRestrictions"></a> 必要條件和限制  
  
-   您必須連接到裝載主要複本的伺服器執行個體。  
  
 如需詳細資訊，請參閱 [AlwaysOn 可用性群組的必要條件、限制和建議 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/prereqs-restrictions-recommendations-always-on-availability.md)。  

##  <a name="security"></a><a name="Security"></a> Security  
  
###  <a name="permissions"></a><a name="Permissions"></a> 權限  
 需要可用性群組的 ALTER AVAILABILITY GROUP 權限、CONTROL AVAILABILITY GROUP 權限、ALTER ANY AVAILABILITY GROUP 權限或 CONTROL SERVER 權限。  

[!INCLUDE[Freshness](../../../includes/paragraph-content/fresh-note-steps-feedback.md)]

##  <a name="using-sql-server-management-studio"></a><a name="SSMSProcedure"></a> 使用 SQL Server Management Studio  
 **加入複本**  
  
1.  在 [物件總管] 中，連接到裝載主要複本的伺服器執行個體，然後展開伺服器樹狀目錄。  
  
2.  依序展開 [Always On 高可用性]  節點和 [可用性群組]  節點。  
  
3.  以滑鼠右鍵按一下可用性群組，然後選取下列其中一個命令：  
  
    -   選取 **[加入複本]** 命令，以啟動 [將複本加入至可用性群組] 精靈。 如需詳細資訊，請參閱[使用將複本加入至可用性群組精靈 &#40;SQL Server Management Studio&#41;](../../../database-engine/availability-groups/windows/use-the-add-replica-to-availability-group-wizard-sql-server-management-studio.md)。  
  
    -   或者，選取 **[屬性]** 命令，以開啟 **[可用性群組屬性]** 對話方塊。 在此對話方塊中加入複本的步驟如下：  
  
        1.  在對話方塊的 **[可用性複本]** 窗格中，按一下 **[加入]** 按鈕。 這會建立及選取複本項目，其中的空白伺服器執行個體欄位為已選取。  
  
        2.  請輸入符合裝載可用性複本必要條件的伺服器執行個體名稱。  
  
         若要加入其他複本，請重複上述步驟。 當您指定好複本時，按一下 **[確定]** 以完成該作業。  
  
##  <a name="using-transact-sql"></a><a name="TsqlProcedure"></a> 使用 Transact-SQL  
 **加入複本**  
  
1.  連接到裝載主要複本的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體。  
  
2.  使用 ALTER AVAILABILITY GROUP 陳述式的 ADD REPLICA ON 子句，將新的次要複本加入至可用性群組。 ENDPOINT_URL、AVAILABILITY_MODE 和 FAILOVER_MODE 選項在 ADD REPLICA ON 子句中是必要項。 其他複本選項 BACKUP_PRIORITY、SECONDARY_ROLE、PRIMARY_ROLE 和 SESSION_TIMEOUT 都是選擇項。 如需詳細資訊，請參閱 [ALTER AVAILABILITY GROUP &#40;Transact-SQL&#41;](../../../t-sql/statements/alter-availability-group-transact-sql.md)中的 PowerShell，將次要複本加入現有的 AlwaysOn 可用性群組中。  
  
     例如，下列 [!INCLUDE[tsql](../../../includes/tsql-md.md)] 陳述式會在位於 `MyAG` 所裝載的預設伺服器執行個體的 `COMPUTER04`可用性群組中建立新的複本，而此電腦的端點 URL 為 `TCP://COMPUTER04.Adventure-Works.com:5022'`。 此複本支援手動容錯移轉和非同步認可的可用性模式。  
  
    ```  
    ALTER AVAILABILITY GROUP MyAG ADD REPLICA ON 'COMPUTER04'   
       WITH (  
             ENDPOINT_URL = 'TCP://COMPUTER04.Adventure-Works.com:5022',  
             AVAILABILITY_MODE = ASYNCHRONOUS_COMMIT,  
             FAILOVER_MODE = MANUAL  
             );  
    ```  
  
##  <a name="using-powershell"></a><a name="PowerShellProcedure"></a> 使用 PowerShell  
 **加入複本**  
  
1.  變更目錄 (**cd**) 為裝載主要複本的伺服器執行個體。  
  
2.  使用 **New-SqlAvailabilityReplica** Cmdlet。  
  
     例如，下列命令會將可用性複本加入至名為 `MyAg`的現有可用性群組。 此複本支援手動容錯移轉和非同步認可的可用性模式。 在次要角色中，此複本將支援讀取連接，可讓您將唯讀處理卸載至此複本。  
  
    ```  
    $agPath = "SQLSERVER:\Sql\PrimaryServer\InstanceName\AvailabilityGroups\MyAg"  
    $endpointURL = "TCP://PrimaryServerName.domain.com:5022"  
    $failoverMode = "Manual"  
    $availabilityMode = "AsynchronousCommit"  
    $secondaryReadMode = "AllowAllConnections"  
  
    New-SqlAvailabilityReplica -Name SecondaryServer\Instance `   
    -EndpointUrl $endpointURL `   
    -FailoverMode $failoverMode `   
    -AvailabilityMode $availabilityMode `   
    -ConnectionModeInSecondaryRole $secondaryReadMode `   
    -Path $agPath  
    ```  
  
    > [!NOTE]  
    >  若要檢視 Cmdlet 的語法，請在 **PowerShell 環境中使用** Get-Help [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Cmdlet。 如需詳細資訊，請參閱 [Get Help SQL Server PowerShell](../../../powershell/sql-server-powershell.md)。  
  
 **若要設定和使用 SQL Server PowerShell 提供者**  
  
-   [SQL Server PowerShell 提供者](../../../powershell/sql-server-powershell-provider.md)  
  
##  <a name="follow-up-after-adding-a-secondary-replica"></a><a name="FollowUp"></a> 待處理：加入次要複本之後  
 若要將複本加入至現有的可用性群組，您必須執行下列步驟：  
  
1.  連接到將要裝載新次要複本的伺服器執行個體。  
  
2.  將新的次要複本加入可用性群組。 如需詳細資訊，請參閱 [將次要複本聯結至可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/join-a-secondary-replica-to-an-availability-group-sql-server.md)。  
  
3.  對於可用性群組中的每個資料庫，在裝載次要複本的伺服器執行個體上建立次要資料庫。 如需詳細資訊，請參閱 [針對可用性群組手動準備次要資料庫 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/manually-prepare-a-secondary-database-for-an-availability-group-sql-server.md)中的 PowerShell，將次要資料庫聯結至 AlwaysOn 可用性群組。  
  
4.  將每一個新的次要資料庫聯結至可用性群組。 如需詳細資訊，請參閱 [將次要資料庫聯結至可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/join-a-secondary-database-to-an-availability-group-sql-server.md)。  
  
##  <a name="related-tasks"></a><a name="RelatedTasks"></a> 相關工作  
 **管理可用性複本**  
  
-   [將次要複本聯結至可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/join-a-secondary-replica-to-an-availability-group-sql-server.md)  
  
-   [將次要複本從可用性群組移除 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/remove-a-secondary-replica-from-an-availability-group-sql-server.md)  
  
-   [設定可用性複本的唯讀存取 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/configure-read-only-access-on-an-availability-replica-sql-server.md)  
  
-   [變更可用性複本的可用性模式 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/change-the-availability-mode-of-an-availability-replica-sql-server.md)  
  
-   [變更可用性複本的容錯移轉模式 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/change-the-failover-mode-of-an-availability-replica-sql-server.md)  
  
-   [變更可用性複本的工作階段逾時期限 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/change-the-session-timeout-period-for-an-availability-replica-sql-server.md)  
  
-   [變更可用性複本的工作階段逾時期限 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/change-the-session-timeout-period-for-an-availability-replica-sql-server.md)  
  
## <a name="see-also"></a>另請參閱  
 [ALTER AVAILABILITY GROUP &#40;Transact-SQL&#41;](../../../t-sql/statements/alter-availability-group-transact-sql.md)   
 [AlwaysOn 可用性群組概觀 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server.md)   
 [建立及設定可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/creation-and-configuration-of-availability-groups-sql-server.md)   
 [使用 AlwaysOn 儀表板 &#40;SQL Server Management Studio&#41;](../../../database-engine/availability-groups/windows/use-the-always-on-dashboard-sql-server-management-studio.md)   
 [監視可用性群組 &#40;Transact-SQL&#41;](../../../database-engine/availability-groups/windows/monitor-availability-groups-transact-sql.md)  
  
