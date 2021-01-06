---
title: 使用 PowerShell 建立可用性群組
description: 了解如何使用 PowerShell，在 SQL Server 2019 (15.x) 中透過 PowerShell Cmdlet 建立及設定 Always On 可用性群組。
ms.custom: seodec18
ms.date: 05/17/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: how-to
helpviewer_keywords:
- Availability Groups [SQL Server], creating
ms.assetid: bc69a7df-20fa-41e1-9301-11317c5270d2
author: cawrites
ms.author: chadam
ms.openlocfilehash: 12d976aa136435a494594e7dde1aeb97b5d3a565
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97643354"
---
# <a name="create-an-always-on-availability-group-using-powershell"></a>使用 PowerShell 建立 Always On 可用性群組
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]
  本主題描述如何使用 [!INCLUDE[ssCurrent](../../../includes/sscurrent-md.md)]中的 PowerShell，透過 PowerShell Cmdlet 建立及設定 AlwaysOn 可用性群組。 *「可用性群組」* (Availability Group) 會定義當做單一單位容錯移轉的一組使用者資料庫，以及支援容錯移轉的一組容錯移轉夥伴 (也稱為 *「可用性複本」* (Availability Replica))。  
  
> [!NOTE]  
> 如需可用性群組的簡介，請參閱 [AlwaysOn 可用性群組概觀 &#40;SQL Server&#41;](~/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server.md)中的 PowerShell，透過 PowerShell Cmdlet 建立及設定 AlwaysOn 可用性群組。  
  
> [!NOTE]  
> 若不使用 PowerShell 指令程式，您可以使用 [建立可用性群組精靈] 或 [!INCLUDE[tsql](../../../includes/tsql-md.md)]。 如需詳細資訊，請參閱 [使用新增可用性群組對話方塊 &#40;SQL Server Management Studio&#41;](../../../database-engine/availability-groups/windows/use-the-new-availability-group-dialog-box-sql-server-management-studio.md) 或 [建立可用性群組 &#40;Transact-SQL&#41;](../../../database-engine/availability-groups/windows/create-an-availability-group-transact-sql.md)中的 PowerShell，透過 PowerShell Cmdlet 建立及設定 AlwaysOn 可用性群組。  

## <a name="before-you-begin"></a>開始之前
### <a name="prerequisites-restrictions-and-recommendations"></a><a name="PrerequisitesRestrictions"></a> 必要條件、限制及建議  

- 建立可用性群組之前，請確認 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 主機執行個體分別位於 Windows Server 容錯移轉叢集 (WSFC) 容錯移轉叢集的不同 WSFC 節點上。 此外，請確認您的伺服器執行個體符合其他伺服器執行個體必要條件和所有其他 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] 需求，而且您了解建議事項。 如需詳細資訊，強烈建議您閱讀 [AlwaysOn 可用性群組的必要條件、限制和建議 &#40;SQL Server&#41;](~/database-engine/availability-groups/windows/prereqs-restrictions-recommendations-always-on-availability.md)。  

### <a name="permissions"></a><a name="Permissions"></a> 權限  
 需要 **系統管理員 (sysadmin)** 固定伺服器角色的成員資格，以及 CREATE AVAILABILITY GROUP 伺服器權限、ALTER ANY AVAILABILITY GROUP 權限或 CONTROL SERVER 權限。  

## <a name="using-powershell-to-create-and-configure-an-availability-group"></a><a name="PowerShellProcedure"></a> 使用 PowerShell 建立和設定可用性群組  
 
下表列出與設定可用性群組有關的基本工作，並指出 PowerShell 指令程式支援哪些工作。 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] 工作必須依照其出現在表格中的順序來執行。  
  
|Task|PowerShell 指令程式 (如果可用) 或 Transact-SQL 陳述式|在何處執行工作|  
|----------|--------------------------------------------------------------------|---------------------------------|  
|建立資料庫鏡像端點 (每個 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體一次)|**New-SqlHadrEndPoint**|在缺少資料庫鏡像端點的每一個伺服器執行個體上執行。<br /><br />若要變更現有資料庫鏡像端點，請使用 **Set-SqlHadrEndpoint**。|  
|建立可用性群組|首先，使用 **New-SqlAvailabilityReplica** Cmdlet 搭配 **-AsTemplate** 參數，針對您打算包含在可用性群組內之兩個可用性複本的每一個來建立記憶體內的可用性複本物件。<br /><br /> 然後，使用 **New-SqlAvailabilityGroup** Cmdlet 及參考可用性複本物件來建立可用性群組。|於裝載初始主要複本的伺服器執行個體上執行。|  
|將次要複本加入可用性群組|**Join-SqlAvailabilityGroup**|在裝載次要複本的每一個伺服器執行個體上執行。|  
|準備次要資料庫|**Backup-SqlDatabase** 和 **Restore-SqlDatabase**|在裝載主要複本的伺服器執行個體上建立備份。<br /><br /> 使用 **NoRecovery** 還原參數，還原裝載次要複本之每個伺服器執行個體上的備份。 如果在裝載主要複本的電腦和裝載目標次要複本的電腦之間有檔案路徑差異，也要使用 **RelocateFile** 還原參數。|  
|將每一個次要資料庫加入可用性群組來啟動資料同步處理。|**Add-SqlAvailabilityDatabase**|在裝載次要複本的每一個伺服器執行個體上執行。|  
  
> [!NOTE]
> 若要執行指定的工作，請將目錄 (**cd**) 變更為指定的伺服器執行個體或執行個體。  

## <a name="using-powershell"></a>使用 PowerShell

設定並使用 [SQL Server PowerShell 提供者](../../../powershell/sql-server-powershell-provider.md)。 

> [!NOTE]  
> 若要檢視指定 Cmdlet 的語法和範例，請使用 **PowerShell 環境中的** Get-Help [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Cmdlet。 如需詳細資訊，請參閱 [Get Help SQL Server PowerShell](../../../powershell/sql-server-powershell.md)。  

1. 將目錄 (**cd**) 變更為要裝載主要複本的伺服器執行個體。  
  
1. 針對主要複本建立記憶體中的可用性複本物件。  
  
1. 針對每一個次要複本建立記憶體中的可用性複本物件。  
  
1. 建立可用性群組。  
  
    > [!NOTE]  
    > 可用性群組名稱的最大長度為 128 個字元。  

1. 如要將新的次要複本聯結至可用性群組，請參閱[將次要複本聯結至可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/join-a-secondary-replica-to-an-availability-group-sql-server.md)。  
  
1. 如果是可用性群組中的每個資料庫，請使用 RESTORE WITH NORECOVERY 還原主要資料庫的最近備份來建立次要資料庫。  
  
1. 如要將每個新的次要資料庫聯結至可用性群組，請參閱[將次要複本聯結至可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/join-a-secondary-replica-to-an-availability-group-sql-server.md)。  
  
1. (選擇性) 使用 Windows **dir** 命令來確認新可用性群組的內容。  
  
> [!NOTE]  
> 如果伺服器執行個體的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 服務帳戶在不同的網域使用者帳戶下執行，請在每一個伺服器執行個體上建立其他伺服器執行個體的登入，並將此登入 CONNECT 權限授與本機資料庫鏡像端點。  

### <a name="example"></a><a name="ExampleConfigureGroup"></a> 範例
下列 PowerShell 範例會建立及設定一個名為 `<myAvailabilityGroup>` 的簡單可用性群組，其包含兩個可用性複本和一個可用性資料庫。 範例：  

1. 備份 `<myDatabase>` 及其交易記錄。  

1. 使用 `<myDatabase>` -NoRecovery **選項還原** 和其交易記錄。  

1. 建立主要複本的記憶體內表示法，此主要複本將由 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 本機執行個體 (名為 `PrimaryComputer\Instance`) 所裝載。  

1. 建立次要複本的記憶體內表示法，此次要複本將由 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體 (名為 `SecondaryComputer\Instance`) 所裝載。  

1. 建立名為 `<myAvailabilityGroup>`的可用性群組。  

1. 將次要複本聯結至可用性群組。  

1. 將次要資料庫加入可用性群組。  

```powershell
# Backup my database and its log on the primary  
Backup-SqlDatabase `  
    -Database "<myDatabase>" `  
    -BackupFile "\\share\backups\<myDatabase>.bak" `  
    -ServerInstance "PrimaryComputer\Instance"  
  
Backup-SqlDatabase `  
    -Database "<myDatabase>" `  
    -BackupFile "\\share\backups\<myDatabase>.log" `  
    -ServerInstance "PrimaryComputer\Instance" `  
    -BackupAction Log   
  
# Restore the database and log on the secondary (using NO RECOVERY)  
Restore-SqlDatabase `  
    -Database "<myDatabase>" `  
    -BackupFile "\\share\backups\<myDatabase>.bak" `  
    -ServerInstance "SecondaryComputer\Instance" `  
    -NoRecovery  
  
Restore-SqlDatabase `  
    -Database "<myDatabase>" `  
    -BackupFile "\\share\backups\<myDatabase>.log" `  
    -ServerInstance "SecondaryComputer\Instance" `  
    -RestoreAction Log `  
    -NoRecovery  
  
# Create an in-memory representation of the primary replica.  
$primaryReplica = New-SqlAvailabilityReplica `  
    -Name "PrimaryComputer\Instance" `  
    -EndpointURL "TCP://PrimaryComputer.domain.com:5022" `  
    -AvailabilityMode "SynchronousCommit" `  
    -FailoverMode "Automatic" `  
    -Version 12 `  
    -AsTemplate  
  
# Create an in-memory representation of the secondary replica.  
$secondaryReplica = New-SqlAvailabilityReplica `  
    -Name "SecondaryComputer\Instance" `  
    -EndpointURL "TCP://SecondaryComputer.domain.com:5022" `  
    -AvailabilityMode "SynchronousCommit" `  
    -FailoverMode "Automatic" `  
    -Version 12 `  
    -AsTemplate  
  
# Create the availability group  
New-SqlAvailabilityGroup `  
    -Name "<myAvailabilityGroup>" `  
    -Path "SQLSERVER:\SQL\PrimaryComputer\Instance" `  
    -AvailabilityReplica @($primaryReplica,$secondaryReplica) `  
    -Database "<myDatabase>"  
  
# Join the secondary replica to the availability group.  
Join-SqlAvailabilityGroup -Path "SQLSERVER:\SQL\SecondaryComputer\Instance" -Name "<myAvailabilityGroup>"  
  
# Join the secondary database to the availability group.  
Add-SqlAvailabilityDatabase -Path "SQLSERVER:\SQL\SecondaryComputer\Instance\AvailabilityGroups\<myAvailabilityGroup>" -Database "<myDatabase>"  
```  
  
## <a name="related-tasks"></a><a name="RelatedTasks"></a> 相關工作  
 **設定 AlwaysOn 可用性群組的伺服器執行個體**  
  
- [啟用和停用 AlwaysOn 可用性群組 &#40;SQL Server&#41;](~/database-engine/availability-groups/windows/enable-and-disable-always-on-availability-groups-sql-server.md)  
  
- [針對 AlwaysOn 可用性群組建立資料庫鏡像端點 &#40;SQL Server PowerShell&#41;](~/database-engine/availability-groups/windows/database-mirroring-always-on-availability-groups-powershell.md)  
  
 **若要設定可用性群組和複本屬性**  
  
- [變更可用性複本的可用性模式 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/change-the-availability-mode-of-an-availability-replica-sql-server.md)  
  
- [變更可用性複本的容錯移轉模式 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/change-the-failover-mode-of-an-availability-replica-sql-server.md)  
  
- [建立或設定可用性群組接聽程式 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/create-or-configure-an-availability-group-listener-sql-server.md)  
  
- [設定彈性容錯移轉原則以控制自動容錯移轉的條件 &#40;AlwaysOn 可用性群組&#41;](~/database-engine/availability-groups/windows/configure-flexible-automatic-failover-policy.md)  
  
- [在新增或修改可用性複本時指定端點 URL &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/specify-endpoint-url-adding-or-modifying-availability-replica.md)  
  
- [設定可用性複本的備份 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/configure-backup-on-availability-replicas-sql-server.md)  
  
- [設定可用性複本的唯讀存取 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/configure-read-only-access-on-an-availability-replica-sql-server.md)  
  
- [設定可用性群組的唯讀路由 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/configure-read-only-routing-for-an-availability-group-sql-server.md)  
  
- [變更可用性複本的工作階段逾時期限 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/change-the-session-timeout-period-for-an-availability-replica-sql-server.md)  
  
 **若要完成可用性群組組態**  
  
- [將次要複本聯結至可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/join-a-secondary-replica-to-an-availability-group-sql-server.md)  
  
- [針對可用性群組手動準備次要資料庫 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/manually-prepare-a-secondary-database-for-an-availability-group-sql-server.md)  
  
- [將次要資料庫聯結至可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/join-a-secondary-database-to-an-availability-group-sql-server.md)  
  
- [建立或設定可用性群組接聽程式 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/create-or-configure-an-availability-group-listener-sql-server.md)  
  
 **建立可用性群組的其他方法**  
  
- [使用可用性群組精靈 &#40;SQL Server Management Studio&#41;](../../../database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio.md)  
  
- [使用新增可用性群組對話方塊 &#40;SQL Server Management Studio&#41;](../../../database-engine/availability-groups/windows/use-the-new-availability-group-dialog-box-sql-server-management-studio.md)  
  
- [建立可用性群組 &#40;Transact-SQL&#41;](../../../database-engine/availability-groups/windows/create-an-availability-group-transact-sql.md)  
  
 **疑難排解 AlwaysOn 可用性群組組態**  
  
- [疑難排解 AlwaysOn 可用性群組組態 &#40;SQL Server&#41;](~/database-engine/availability-groups/windows/troubleshoot-always-on-availability-groups-configuration-sql-server.md)  
  
- [疑難排解失敗的加入檔案作業 &#40;AlwaysOn 可用性群組&#41;](~/database-engine/availability-groups/windows/troubleshoot-a-failed-add-file-operation-always-on-availability-groups.md)  
  
## <a name="related-content"></a><a name="RelatedContent"></a> 相關內容  
  
- **部落格：**  
  
     [Always On - HADRON Learning Series: Worker Pool Usage for HADRON Enabled Databases (AlwaysOn - HADRON 學習系列：資料庫啟用 HADRON 時工作者集區的使用方式)](/archive/blogs/psssql/alwayson-hadron-learning-series-worker-pool-usage-for-hadron-enabled-databases)  
  
     [Configuring Always On with SQL Server PowerShell (使用 SQL Server PowerShell 設定 AlwaysOn)](/archive/blogs/sqlalwayson/configuring-alwayson-with-sql-server-powershell)  
  
     [SQL Server AlwaysOn 團隊部落格：官方 SQL Server AlwaysOn 團隊部落格](/archive/blogs/sqlalwayson/)  
  
     [CSS SQL Server 工程師部落格](/archive/blogs/psssql/)  
  
- **影片：**  
  
     [Microsoft SQL Server Code-Named "Denali" AlwaysOn Series,Part 1: Introducing the Next Generation High Availability Solution (Microsoft SQL Server 代碼 "Denali" AlwaysOn 系列第一部分：新一代高可用性解決方案簡介)](https://channel9.msdn.com/Events/TechEd/NorthAmerica/2011/DBI302)  
  
     [Microsoft SQL Server Code-Named "Denali" Always On Series,Part 2: Building a Mission-Critical High Availability Solution Using Always On (Microsoft SQL Server 代碼 "Denali" AlwaysOn 系列第二部分：使用 AlwaysOn 建立任務關鍵性高可用性解決方案)](https://channel9.msdn.com/Events/TechEd/NorthAmerica/2011/DBI404)  
  
- **白皮書：**  
  
     [Microsoft SQL Server AlwaysOn 高可用性和災害復原方案指南](/previous-versions/sql/sql-server-2012/hh781257(v=msdn.10))  
  
     [Microsoft 的 SQL Server 2012 白皮書](https://social.technet.microsoft.com/wiki/contents/articles/13146.white-paper-gallery-for-sql-server.aspx#[Category]SQLServer2012)  
  
     [SQL Server 客戶諮詢團隊白皮書](https://techcommunity.microsoft.com/t5/DataCAT/bg-p/DataCAT/)  
  
## <a name="see-also"></a>另請參閱  
 [資料庫鏡像端點 &#40;SQL Server&#41;](../../../database-engine/database-mirroring/the-database-mirroring-endpoint-sql-server.md)   
 [AlwaysOn 可用性群組概觀 &#40;SQL Server&#41;](~/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server.md)