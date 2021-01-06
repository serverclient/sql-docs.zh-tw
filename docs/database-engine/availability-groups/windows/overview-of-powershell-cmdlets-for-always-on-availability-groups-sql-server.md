---
title: 可用性群組的 PowerShell Cmdlet
description: '可用來管理 Always On 可用性群組的不同 PowerShell Cmdlet 參考。 '
ms.custom: seo-lt-2019
ms.date: 08/30/2017
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: conceptual
helpviewer_keywords:
- Availability Groups [SQL Server], PowerShell cmdlets
- Availability Groups [SQL Server], about
- PowerShell [SQL Server], cmdlets
ms.assetid: b3fef0d5-b6d7-4386-a0f0-d06c165ad4de
author: cawrites
ms.author: chadam
ms.openlocfilehash: ae6462d46760f1a5f58f01a2170470e2d4ea7fac
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97643489"
---
# <a name="overview-of-powershell-cmdlets-for-always-on-availability-groups"></a>AlwaysOn 可用性群組的 PowerShell Cmdlet 概觀
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]

  [!INCLUDE[msCoName](../../../includes/msconame-md.md)] PowerShell 是以工作為基礎的命令列介面和指令碼語言，專為系統管理所設計。 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] 會在 [!INCLUDE[ssCurrent](../../../includes/sscurrent-md.md)] 中提供一組 PowerShell 指令程式，可讓您部署、管理和監視可用性群組、可用性複本以及可用性資料庫。  
  
> [!NOTE]  
>  PowerShell 指令程式可以透過順利起始動作來完成。 這並不表示預期的工作 (例如可用性群組的容錯移轉) 已經完成。 編寫一系列動作的指令碼時，您可能必須檢查動作的狀態，並且等候這些動作完成。  
  
> [!NOTE]  
>  如需《 [!INCLUDE[ssCurrent](../../../includes/sscurrent-md.md)] 線上叢書》中描述如何使用指令程式來執行 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] 工作的主題清單，請參閱 [AlwaysOn 可用性群組概觀 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server.md)的＜相關工作＞一節。  
  
##  <a name="configuring-a-server-instance-for-always-on-availability-groups"></a><a name="ConfiguringServerInstance"></a> Configuring a Server Instance for Always On Availability Groups  
  
|指令程式|描述|支援的項目|  
|-------------|-----------------|------------------|
|[**Disable-SqlAlwaysOn**](/powershell/module/sqlserver/disable-sqlalwayson)|在伺服器執行個體上停用 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] 功能。|**Path**、 **InputObject** 或 **Name** 參數所指定的伺服器執行個體。 (必須是支援 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 的 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)]版本)。|  
|[**Enable-SqlAlwaysOn**](/powershell/module/sqlserver/enable-sqlalwayson)|在支援 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] 功能的 [!INCLUDE[ssCurrent](../../../includes/sscurrent-md.md)] 執行個體上啟用 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] 。 如需 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] 支援的相關資訊，請參閱 [AlwaysOn 可用性群組的必要條件、限制和建議 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/prereqs-restrictions-recommendations-always-on-availability.md)。|支援 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 的任何 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)]版本。|  
|[**New-SqlHadrEndPoint**](/powershell/module/sqlserver/new-sqlhadrendpoint)|在伺服器執行個體上建立新的資料庫鏡像端點。 在主要與次要資料庫之間進行資料移動時需要這個端點。|任何 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]|  
|[**Set-SqlHadrEndpoint**](/powershell/module/sqlserver/set-sqlhadrendpoint)|變更現有資料庫鏡像端點的屬性，例如名稱、狀態或驗證屬性。|支援 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] 且缺少資料庫鏡像端點的伺服器執行個體|  

  
##  <a name="backing-up-and-restoring-databases-and-transaction-logs"></a><a name="BnRcmdlets"></a> Backing Up and Restoring Databases and Transaction Logs  
  
|指令程式|描述|支援的項目|  
|-------------|-----------------|------------------|  
|[**Backup-SqlDatabase**](/powershell/module/sqlserver/backup-sqldatabase)|建立資料或記錄備份。|任何線上資料庫 (若為 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)]，則為裝載主要複本之伺服器執行個體的資料庫)|  
|[**Restore-SqlDatabase**](/powershell/module/sqlserver/restore-sqldatabase)|還原備份。|任何 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體 (若為 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)]，則為裝載次要複本的伺服器執行個體)<br /><br />

  >[!Important]
  >準備次要資料庫時，您必須在每個 **Restore-SqlDatabase** 命令中使用 **-NoRecovery** 參數。 
  
 如需使用這些 Cmdlet 來準備次要資料庫的相關資訊，請參閱[針對可用性群組手動準備次要資料庫 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/manually-prepare-a-secondary-database-for-an-availability-group-sql-server.md)。  
  
##  <a name="creating-and-managing-an-availability-group"></a><a name="DeployManageAGs"></a> Creating and Managing an Availability Group  
  
|指令程式|描述|支援的項目|  
|-------------|-----------------|------------------|  
|[**New-SqlAvailabilityGroup**](/powershell/module/sqlserver/new-sqlavailabilitygroup)|建立新的可用性群組。|要裝載主要複本的伺服器執行個體|  
|[**Remove-SqlAvailabilityGroup**](/powershell/module/sqlserver/remove-sqlavailabilitygroup)|刪除可用性群組。|啟用 HADR 的伺服器執行個體|  
|[**Set-SqlAvailabilityGroup**](/powershell/module/sqlserver/set-sqlavailabilitygroup)|設定可用性群組的屬性；讓可用性群組上線/離線。|裝載主要複本的伺服器執行個體|  
|[**Switch-SqlAvailabilityGroup**](/powershell/module/sqlserver/switch-sqlavailabilitygroup)|起始下列其中一種形式的容錯移轉：<br /><br /> 可用性群組的強制容錯移轉 (可能遺失資料)。<br /><br /> 可用性群組的手動容錯移轉。|裝載目標次要複本的伺服器執行個體|  
  
##  <a name="creating-and-managing-an-availability-group-listener"></a><a name="AGlisteners"></a> Creating and Managing an Availability Group Listener  
  
|Cmdlet|描述|支援的項目|  
|------------|-----------------|------------------|  
|[**New-SqlAvailabilityGroupListener**](/powershell/module/sqlserver/new-sqlavailabilitygrouplistener)|建立新的可用性群組接聽程式，並將其附加至現有的可用性群組。|裝載主要複本的伺服器執行個體|  
|[**Set-SqlAvailabilityGroupListener**](/powershell/module/sqlserver/set-sqlavailabilitygrouplistener)|修改現有可用性群組接聽程式上的通訊埠設定。|裝載主要複本的伺服器執行個體|  
|[**Add-SqlAvailabilityGroupListenerStaticIp**](/powershell/module/sqlserver/add-sqlavailabilitygrouplistenerstaticip)|將靜態 IP 位址加入至現有的可用性群組接聽程式組態。 IP 位址可以是包含子網路的 IPv4 位址或 IPv6 位址。|裝載主要複本的伺服器執行個體|  
  
##  <a name="creating-and-managing-an-availability-replica"></a><a name="DeployManageARs"></a> Creating and Managing an Availability Replica  
  
|指令程式|描述|支援的項目|  
|-------------|-----------------|------------------|  
|[**New-SqlAvailabilityReplica**](/powershell/module/sqlserver/new-sqlavailabilityreplica)|建立新的可用性複本。 您可以使用 **-AsTemplate** 參數，針對每個新的可用性複本建立記憶體內部可用性複本物件。|裝載主要複本的伺服器執行個體|  
|[**Join-SqlAvailabilityGroup**](/powershell/module/sqlserver/join-sqlavailabilitygroup)|將次要複本聯結至可用性群組。|裝載次要複本的伺服器執行個體|  
|[**Remove-SqlAvailabilityReplica**](/powershell/module/sqlserver/remove-sqlavailabilityreplica)|刪除可用性複本。|裝載主要複本的伺服器執行個體|  
|[**Set-SqlAvailabilityReplica**](/powershell/module/sqlserver/set-sqlavailabilityreplica)|設定可用性複本的屬性。|裝載主要複本的伺服器執行個體|  
  
##  <a name="adding-and-managing-an-availability-database"></a><a name="DeployManageDbs"></a> Adding and Managing an Availability Database  
  
|指令程式|描述|支援的項目|  
|-------------|-----------------|------------------|  
|[**Add-SqlAvailabilityDatabase**](/powershell/module/sqlserver/add-sqlavailabilitydatabase)|在主要複本上，將資料庫加入至可用性群組。<br /><br /> 在次要複本上，將次要資料庫聯結至可用性群組。|裝載可用性複本的任何伺服器執行個體 (主要和次要複本的行為有所不同)|  
|[**Remove-SqlAvailabilityDatabase**](/powershell/module/sqlserver/remove-sqlavailabilitydatabase)|在主要複本上，從可用性群組中移除資料庫。<br /><br /> 在次要複本上，從本機次要複本中移除本機次要資料庫。|裝載可用性複本的任何伺服器執行個體 (主要和次要複本的行為有所不同)|  
|[**Resume-SqlAvailabilityDatabase**](/powershell/module/sqlserver/resume-sqlavailabilitydatabase)|繼續進行暫停之可用性資料庫的資料移動。|已暫停資料庫的伺服器執行個體。|  
|[**Suspend-SqlAvailabilityDatabase**](/powershell/module/sqlserver/suspend-sqlavailabilitydatabase)|暫停可用性資料庫的資料移動。|裝載可用性複本的任何伺服器執行個體。|  
  
##  <a name="monitoring-availability-group-health"></a><a name="MonitorTblshtAGs"></a> Monitoring Availability Group Health  
 下列 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 指令程式可讓您監視可用性群組及其複本和資料庫的健全狀況。  
  
> [!IMPORTANT]  
>  您必須擁有 CONNECT、VIEW SERVER STATE 和 VIEW ANY DEFINITION 權限，才能執行這些指令程式。  
  
|Cmdlet|描述|支援的項目|  
|------------|-----------------|------------------|  
|[**Test-SqlAvailabilityGroup**](/powershell/module/sqlserver/test-sqlavailabilitygroup)|透過評估 SQL Server 原則式管理 (PBM) 原則，評估可用性群組的健全狀況。|裝載可用性複本的任何伺服器執行個體。*|  
|[**Test-SqlAvailabilityReplica**](/powershell/module/sqlserver/test-sqlavailabilityreplica)|透過評估 SQL Server 原則式管理 (PBM) 原則，評估可用性複本的健全狀況。|裝載可用性複本的任何伺服器執行個體。*|  
|[**Test-SqlDatabaseReplicaState**](/powershell/module/sqlserver/test-sqldatabasereplicastate)|透過評估 SQL Server 原則式管理 (PBM) 原則，評估所有聯結可用性複本之可用性資料庫的健全狀況。|裝載可用性複本的任何伺服器執行個體。*|  
  
 *若要檢視可用性群組中所有可用性複本的相關資訊，請用於裝載主要複本的伺服器執行個體。  
  
 如需詳細資訊，請參閱[使用 AlwaysOn 原則檢視可用性群組的健全狀況 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/use-always-on-policies-to-view-the-health-of-an-availability-group-sql-server.md)。  
  
## <a name="see-also"></a>另請參閱  
 [AlwaysOn 可用性群組概觀 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server.md)   
 [Get Help SQL Server PowerShell](../../../powershell/sql-server-powershell.md)  
  
