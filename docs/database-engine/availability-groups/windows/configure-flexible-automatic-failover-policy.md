---
title: 為可用性群組設定彈性的自動容錯移轉原則
description: 描述如何使用 Transact-SQL (T-SQL)、PowerShell 或 SQL Server Management Studio，為 Always On 可用性群組設定彈性的容錯移轉原則。
ms.date: 11/05/2019
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: how-to
helpviewer_keywords:
- Availability Groups [SQL Server], flexible failover policy
- Availability Groups [SQL Server], failover
- failover [SQL Server], AlwaysOn Availability Groups
ms.assetid: 1ed564b4-9835-4245-ae35-9ba67419a4ce
author: cawrites
ms.author: chadam
monikerRange: '>=sql-server-2016'
ms.custom: seo-lt-2019
ms.openlocfilehash: 2d4df010b8034147093725b630f652e3123f9719
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97643674"
---
# <a name="configure-a-flexible-automatic-failover-policy-for-an-always-on-availability-group"></a>為 Always On 可用性群組設定彈性的自動容錯移轉原則

[!INCLUDE[sql windows only](../../../includes/applies-to-version/sql-windows-only.md)]

  本主題描述如何在 SQL Server 中使用 [!INCLUDE[tsql](../../../includes/tsql-md.md)] 或 PowerShell 來設定 Always On 可用性群組的彈性容錯移轉原則。 彈性容錯移轉原則可讓您更精確地控制造成可用性群組之 [自動容錯移轉](../../../database-engine/availability-groups/windows/failover-and-failover-modes-always-on-availability-groups.md) 的狀況。 透過變更觸發自動容錯移轉的失敗狀況和健全狀況檢查的頻率，您可以提高或降低自動容錯移轉的可能性，以便支援高可用性的 SLA。  

   可用性群組的彈性容錯移轉原則是由其失敗狀況層級和健全狀況檢查逾時臨界值所定義。 一旦偵測到可用性群組超過其失敗狀況層級或健全狀況檢查逾時臨界值時，可用性群組的資源 DLL 就會回應至 Windows Server 容錯移轉叢集 (WSFC) 叢集。 然後，WSFC 叢集就會起始自動容錯移轉至次要複本。  
 
  > [!NOTE]  
  > 您無法使用 [!INCLUDE[ssManStudioFull](../../../includes/ssmanstudiofull-md.md)]來設定可用性群組的彈性容錯移轉原則。  
  
 
## <a name="limitations-on-automatic-failovers"></a><a name="Limitations"></a> 自動容錯移轉的限制  
  
-   若要進行自動容錯移轉，目前的主要複本和一個次要複本必須設定為具有自動容錯移轉的同步認可可用性模式，而且次要複本必須與主要複本同步處理。  
  
-   自動容錯移轉只支援三個複本。  
  
-   如果可用性群組超過其 WSFC 失敗臨界值，WSFC 叢集將不會嘗試進行可用性群組的自動容錯移轉。 此外，可用性群組的 WSFC 資源群組會維持失敗狀態，直到叢集管理員手動讓失敗的資源群組上線，或者資料庫管理員執行可用性群組的手動容錯移轉為止。 *「WSFC 失敗臨界值」* (WSFC Failure Threshold) 定義為可用性群組在給定的時間週期內支援的失敗次數上限。 預設的時間週期為六小時，而且在這段時間內失敗次數上限的預設值為 *n*-1，其中 *n* 是 WSFC 節點的數目。 若要變更給定可用性群組得失敗臨界值，請使用 WSFC 容錯移轉管理員主控台。  
  
##  <a name="prerequisites"></a><a name="Prerequisites"></a> 必要條件  
  
-   您必須連接到裝載主要複本的伺服器執行個體。  
   
##  <a name="permissions"></a><a name="Permissions"></a> 權限  
  
|Task|權限|  
|----------|-----------------|  
|若要設定新可用性群組的彈性容錯移轉原則|需要 **系統管理員 (sysadmin)** 固定伺服器角色的成員資格，以及 CREATE AVAILABILITY GROUP 伺服器權限、ALTER ANY AVAILABILITY GROUP 權限或 CONTROL SERVER 權限。|  
|若要修改現有可用性群組的原則|需要可用性群組的 ALTER AVAILABILITY GROUP 權限、CONTROL AVAILABILITY GROUP 權限、ALTER ANY AVAILABILITY GROUP 權限或 CONTROL SERVER 權限。|  

##  <a name="health-check-timeout-threshold"></a><a name="HCtimeout"></a> 健全狀況檢查逾時臨界值  
 可用性群組的 WSFC 資源 DLL 會在裝載主要複本的 SQL Server 執行個體上呼叫 [sp_server_diagnostics](../../../relational-databases/system-stored-procedures/sp-server-diagnostics-transact-sql.md) 預存程序，藉以執行主要複本的「健全狀況檢查」  。 **sp_server_diagnostics** 會以等於可用性群組之健全狀況檢查逾時臨界值 1/3 的間隔傳回結果。 預設的健全狀況檢查逾時臨界值為 30 秒，因此 **sp_server_diagnostics** 會以 10 秒的間隔傳回結果。 如果 **sp_server_diagnostics** 變慢或未傳回資訊，資源 DLL 會先等候健全狀況檢查逾時臨界值的完整間隔，然後再判斷主要複本是否沒有回應。 如果主要複本沒有回應，就會起始自動容錯移轉 (如果目前支援的話)。  
  
> [!IMPORTANT]  
>  **sp_server_diagnostics** 不會在資料庫層級執行健全狀況檢查。  
  
##  <a name="failure-condition-level"></a><a name="FClevel"></a> 失敗狀況層級  
 **sp_server_diagnostics** 所傳回的診斷資料和健全狀況資訊是否保證自動容錯移轉主要取決於可用性群組的失敗狀況層級。 「失敗狀況層級」  會指定觸發自動容錯移轉的失敗狀況。 失敗狀況層級共有五層，範圍從最低限制 (第一層級) 到最高限制 (第五層級)。 給定的層級包含較低限制的層級。 因此，最嚴格的層級 (五) 包括了四個較低限制的狀況，依此類推。  
  
> [!IMPORTANT]  
>  任何失敗狀況層級不會偵測到損毀的資料庫和可疑的資料庫。 因此，損毀或可疑的資料庫 (不論是因為硬體故障、資料損毀或其他問題) 都絕對不會觸發自動容錯移轉。  
  
 下表描述對應至每個層級的失敗狀況。  
  
|層級|失敗狀況|[!INCLUDE[tsql](../../../includes/tsql-md.md)] 值|PowerShell 值|  
|-----------|-----------------------|------------------------------|----------------------|  
|一個|伺服器關閉時。 指定在發生下列任何狀況時起始自動容錯移轉：<br /><br /> [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 服務關閉。<br /><br /> 由於未從伺服器執行個體收到 ACK，所以用於連接到 WSFC 叢集的可用性群組租用已到期。 如需詳細資訊，請參閱 [How It Works:SQL Server Always On Lease Timeout](https://techcommunity.microsoft.com/t5/sql-server-support/how-it-works-sql-server-alwayson-lease-timeout/ba-p/317268) (運作方式：SQL Server Always On 租用逾時)。<br /><br /> <br /><br /> 這是最低限制層級。|1|**OnServerDown**|  
|兩個|伺服器沒有回應時。 指定在發生下列任何狀況時起始自動容錯移轉：<br /><br /> [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 的執行個體未連接到叢集，而且已超出使用者指定之可用性群組的健全狀況檢查逾時臨界值。<br /><br /> 可用性複本處於失敗狀態。|2|**OnServerUnresponsive**|  
|三|發生嚴重伺服器錯誤時。 指定在發生嚴重 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 內部錯誤時起始自動容錯移轉，例如執行緒同步鎖定遭到遺棄、嚴重的寫入存取違規或是傾印過多。<br /><br /> 這是預設層級。|3|**OnCriticalServerError**|  
|四|發生一般伺服器錯誤時。 指定在發生一般 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 內部錯誤時起始自動容錯移轉，例如 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 內部資源集區持續發生記憶體不足的狀況。|4|**OnModerateServerError**|  
|五|發生任何限定的失敗狀況時。 指定在發生任何限定的失敗狀況時起始自動容錯移轉，這些狀況包括：<br /><br /> 偵測排程器死結。<br /><br /> 偵測到無法解決的死結。<br /><br /> <br /><br /> 這是最高限制層級。|5|**OnAnyQualifiedFailureConditions**|  
  
> [!NOTE]  
>  [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體對用戶端要求缺少回應與可用性群組無關。  
  
##  <a name="using-transact-sql"></a><a name="TsqlProcedure"></a> 使用 Transact-SQL  
 **若要設定彈性容錯移轉原則**  
  
1.  連接到裝載主要複本的伺服器執行個體。  
  
2.  若為新的可用性群組，請使用 [CREATE AVAILABILITY GROUP](../../../t-sql/statements/create-availability-group-transact-sql.md)[!INCLUDE[tsql](../../../includes/tsql-md.md)] 陳述式。 如果您要修改現有的可用性群組，請使用 [ALTER AVAILABILITY GROUP](../../../t-sql/statements/alter-availability-group-transact-sql.md)[!INCLUDE[tsql](../../../includes/tsql-md.md)] 陳述式。  
  
    -   若要設定容錯移轉狀況層級，請使用 FAILURE_CONDITION_LEVEL = *n* 選項，其中 *n* 是介於 1 到 5 之間的整數。  
  
         例如，下列 [!INCLUDE[tsql](../../../includes/tsql-md.md)] 陳述式會將現有可用性群組 `AG1`的失敗狀況層級變更為層級一：  
  
        ```  
  
        ALTER AVAILABILITY GROUP AG1 SET (FAILURE_CONDITION_LEVEL = 1);  
        ```  
  
         這些整數值與失敗狀況層級的關聯性如下所示：  
  
        |[!INCLUDE[tsql](../../../includes/tsql-md.md)] 值|層級|起始自動容錯移轉的狀況|  
        |------------------------------|-----------|-------------------------------------------|  
        |1|一個|伺服器關閉時。 SQL Server 服務由於容錯移轉或重新啟動而停止。|  
        |2|兩個|伺服器沒有回應時。 滿足任何狀況的較低值，而且 SQL Server 服務連接到叢集且超過健全狀況檢查逾時臨界值，或者目前主要複本處於失敗狀態。|  
        |3|三|發生嚴重伺服器錯誤時。 滿足任何狀況的較低值，或者發生內部嚴重伺服器錯誤。<br /><br /> 這是預設層級。|  
        |4|四|發生一般伺服器錯誤時。 滿足任何狀況的較低值，或者發生一般伺服器錯誤。|  
        |5|五|發生任何限定的失敗狀況時。 滿足任何狀況的較低值，或者發生限定失敗狀況。|  
  
         如需容錯移轉條件層級的詳細資訊，請參閱[可用性群組自動容錯移轉的彈性容錯移轉原則 &#40;SQL Server&#41;]()。  
  
    -   若要設定健全狀況檢查逾時臨界值，請使用 HEALTH_CHECK_TIMEOUT = *n* 選項，其中 *n* 是介於 15000 毫秒 (15 秒) 到 4294967295 毫秒之間的整數。 預設值為 30000 毫秒 (30 秒)。  
  
         例如，下列 [!INCLUDE[tsql](../../../includes/tsql-md.md)] 陳述式會將現有可用性群組 `AG1`的健全狀況檢查逾時臨界值變更為 60,000 毫秒 (一分鐘)。  
  
        ```  
  
        ALTER AVAILABILITY GROUP AG1 SET (HEALTH_CHECK_TIMEOUT = 60000);  
        ```  
  
##  <a name="using-powershell"></a><a name="PowerShellProcedure"></a> 使用 PowerShell  
 **若要設定彈性容錯移轉原則**  
  
1.  將預設值 (**cd**) 設定為裝載主要複本的伺服器執行個體。  
  
2.  將可用性複本加入可用性群組中時，請使用 **New-SqlAvailabilityGroup** Cmdlet。 修改現有的可用性複本時，請使用 **Set-SqlAvailabilityGroup** Cmdlet。  
  
    -   若要設定容錯移轉狀況層級，請使用 **FailureConditionLevel**_level_ 參數，其中 *level* 是下列其中一個值：  
  
        |值|層級|起始自動容錯移轉的狀況|  
        |-----------|-----------|-------------------------------------------|  
        |**OnServerDown**|一個|伺服器關閉時。 SQL Server 服務由於容錯移轉或重新啟動而停止。|  
        |**OnServerUnresponsive**|兩個|伺服器沒有回應時。 滿足任何狀況的較低值，而且 SQL Server 服務連接到叢集且超過健全狀況檢查逾時臨界值，或者目前主要複本處於失敗狀態。|  
        |**OnCriticalServerError**|三|發生嚴重伺服器錯誤時。 滿足任何狀況的較低值，或者發生內部嚴重伺服器錯誤。<br /><br /> 這是預設層級。|  
        |**OnModerateServerError**|四|發生一般伺服器錯誤時。 滿足任何狀況的較低值，或者發生一般伺服器錯誤。|  
        |**OnAnyQualifiedFailureConditions**|五|發生任何限定的失敗狀況時。 滿足任何狀況的較低值，或者發生限定失敗狀況。|  
  
         如需容錯移轉條件層級的詳細資訊，請參閱[可用性群組自動容錯移轉的彈性容錯移轉原則 &#40;SQL Server&#41;]()。  
  
         例如，下列命令會將現有可用性群組 `AG1`的失敗狀況層級變更為層級一。  
  
        ```  
        Set-SqlAvailabilityGroup `   
        -Path SQLSERVER:\Sql\PrimaryServer\InstanceName\AvailabilityGroups\MyAg `   
        -FailureConditionLevel OnServerDown  
        ```  
  
    -   若要設定健全狀況檢查逾時臨界值，請使用 **HealthCheckTimeout**_n_ 參數，其中 *n* 是介於 15000 毫秒 (15 秒) 到 4294967295 毫秒之間的整數。 預設值為 30000 毫秒 (30 秒)。  
  
         例如，下列命令會將現有可用性群組 `AG1`的健全狀況檢查逾時臨界值變更為 120,000 毫秒 (兩分鐘)。  
  
        ```  
        Set-SqlAvailabilityGroup `   
        -Path SQLSERVER:\Sql\PrimaryServer\InstanceName\AvailabilityGroups\MyAG `   
        -HealthCheckTimeout 120000  
        ```  
  
> [!NOTE]  
>  若要檢視 Cmdlet 的語法，請在 **PowerShell 環境中使用** Get-Help [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Cmdlet。 如需詳細資訊，請參閱 [Get Help SQL Server PowerShell](../../../powershell/sql-server-powershell.md)。  
  
 **若要設定和使用 SQL Server PowerShell 提供者**  
  
-   [SQL Server PowerShell 提供者](../../../powershell/sql-server-powershell-provider.md)  
  
-   [Get Help SQL Server PowerShell](../../../powershell/sql-server-powershell.md)  

##  <a name="related-tasks"></a><a name="RelatedTasks"></a> 相關工作  
 **若要設定自動容錯移轉**  
  
-   [變更可用性複本的可用性模式 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/change-the-availability-mode-of-an-availability-replica-sql-server.md) (自動容錯移轉需要同步認可的可用性模式)  
  
-   [變更可用性複本的容錯移轉模式 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/change-the-failover-mode-of-an-availability-replica-sql-server.md)  
  
-   [設定彈性容錯移轉原則以控制自動容錯移轉的條件 &#40;AlwaysOn 可用性群組&#41;](../../../database-engine/availability-groups/windows/configure-flexible-automatic-failover-policy.md)  
  
##  <a name="related-content"></a><a name="RelatedContent"></a> 相關內容  
  
-   [How It Works:SQL Server Always On Lease Timeout](https://techcommunity.microsoft.com/t5/sql-server-support/how-it-works-sql-server-alwayson-lease-timeout/ba-p/317268) (運作方式：SQL Server Always On 租用逾時)  
  
## <a name="see-also"></a>另請參閱  
 [AlwaysOn 可用性群組概觀 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server.md)   
 [可用性模式 &#40;AlwaysOn 可用性群組&#41;](../../../database-engine/availability-groups/windows/availability-modes-always-on-availability-groups.md)   
 [容錯移轉及容錯移轉模式 &#40;AlwaysOn 可用性群組&#41;](../../../database-engine/availability-groups/windows/failover-and-failover-modes-always-on-availability-groups.md)   
 [SQL Server 的 Windows Server 容錯移轉叢集 &#40;WSFC&#41;](../../../sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server.md)   
 [容錯移轉叢集執行個體的容錯移轉原則](../../../sql-server/failover-clusters/windows/failover-policy-for-failover-cluster-instances.md)   
 [sp_server_diagnostics &#40;Transact-SQL&#41;](../../../relational-databases/system-stored-procedures/sp-server-diagnostics-transact-sql.md)  
  
