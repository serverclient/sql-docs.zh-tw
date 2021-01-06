---
title: 啟用或停用可用性群組功能
description: 使用 Transact-SQL (T-SQL)、PowerShell 或 SQL Server Management Studio 啟用或停用 Always On 可用性群組功能的步驟。
ms.custom: seodec18
ms.date: 08/30/2017
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: how-to
helpviewer_keywords:
- Availability Groups [SQL Server], server instance
- Availability Groups [SQL Server], deploying
- Availability Groups [SQL Server], disabling
- Availability Groups [SQL Server], enabling
ms.assetid: 7c326958-5ae9-4761-9c57-905972276a8f
author: cawrites
ms.author: chadam
ms.openlocfilehash: b44d3ffbbff0539b899abf8ffc906f6fe922f65f
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97643580"
---
# <a name="enable-or-disable-always-on-availability-group-feature"></a>啟用或停用 Always On 可用性群組功能
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]

  啟用 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] 是伺服器執行個體使用可用性群組的必要條件。 您必須在將要裝載一個或多個可用性群組之可用性複本的每個 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] 執行個體上啟用 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 功能，才能建立及設定任何可用性群組。  
  
> [!IMPORTANT]  
>  如果您刪除然後重新建立 WSFC 叢集，則必須在原始 WSFC 叢集上裝載可用性複本的每個 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] 執行個體，停用然後重新啟用 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 功能。  
  
  
##  <a name="prerequisites-for-enabling-always-on-availability-groups"></a><a name="Prerequisites"></a> 啟用 AlwaysOn 可用性群組的必要條件  
  
-   此伺服器執行個體必須位於 Windows Server 容錯移轉叢集 (WSFC) 節點上。  
  
-   伺服器執行個體必須執行支援 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)]的 SQL Server 版本。 如需詳細資訊，請參閱 [SQL Server 2016 版本支援的功能](~/sql-server/editions-and-supported-features-for-sql-server-2016.md)。  
  
-   一次只在一個伺服器執行個體啟用 AlwaysOn 可用性群組。 啟用 AlwaysOn 可用性群組之後，等到 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 服務重新啟動後才能在另一個伺服器執行個體繼續進行。  
  
 如需建立及設定可用性群組之其他必要條件的相關資訊，請參閱 [AlwaysOn 可用性群組的必要條件、限制和建議 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/prereqs-restrictions-recommendations-always-on-availability.md)。  
  
## <a name="permissions"></a><a name="Permissions"></a> 權限  
 在 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]執行個體啟用 AlwaysOn 可用性群組之後，伺服器執行個體就會有 WSFC 叢集的完整控制。  

 需要本機電腦的 **Administrator** 群組成員資格和 WSFC 叢集的完整控制。 透過使用 PowerShell 啟用 AlwaysOn 時，請使用 [以系統管理員身分執行] 選項開啟命令提示字元視窗。  
  
 需要 Active Directory 建立物件和管理物件權限。  
  
##  <a name="determine-whether-always-on-availability-groups-is-enabled"></a><a name="IsEnabled"></a> 判斷 AlwaysOn 可用性群組是否已啟用  
  
-   [SQL Server Management Studio](#SSMS1Procedure)  
  
-   [Transact-SQL](#Tsql1Procedure)  
  
-   [PowerShell](#PowerShell1Procedure)  
  
###  <a name="using-sql-server-management-studio"></a><a name="SSMS1Procedure"></a> 使用 SQL Server Management Studio  
 **判斷 AlwaysOn 可用性群組是否已啟用**  
  
1.  在物件總管中，以滑鼠右鍵按一下伺服器執行個體，然後按一下 [屬性]。  
  
2.  在 **[伺服器屬性]** 對話方塊中，按一下 **[一般]** 頁面。 **[為已啟用 HADR]** 屬性會顯示下列其中一個值：  
  
    -   如果 AlwaysOn 可用性群組已啟用，則為 **True**  
  
    -   如果 AlwaysOn 可用性群組已停用，則為 **False**。  
  
###  <a name="using-transact-sql"></a><a name="Tsql1Procedure"></a> 使用 Transact-SQL  
 **判斷 AlwaysOn 可用性群組是否已啟用**  
  
1.  使用下列 [SERVERPROPERTY](../../../t-sql/functions/serverproperty-transact-sql.md) 陳述式：  
  
    ```  
    SELECT SERVERPROPERTY ('IsHadrEnabled');  
    ```  
  
     **IsHadrEnabled** 伺服器屬性設定會指出 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體是否已啟用 AlwaysOn 可用性群組，如下所示：  
  
    -   如果 **IsHadrEnabled** = 1，表示 AlwaysOn 可用性群組已啟用。  
  
    -   如果 **IsHadrEnabled** = 0，表示 AlwaysOn 可用性群組已停用。  
  
    > [!NOTE]  
    >  如需 **IsHadrEnabled** 伺服器屬性的詳細資訊，請參閱 [SERVERPROPERTY &#40;Transact-SQL&#41;](../../../t-sql/functions/serverproperty-transact-sql.md)的 SQL Server 版本。  
  
###  <a name="using-powershell"></a><a name="PowerShell1Procedure"></a> 使用 PowerShell  
 **判斷 AlwaysOn 可用性群組是否已啟用**  
  
1.  將預設值 (**cd**) 設定為要判斷 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] 是否已啟用的伺服器執行個體。  
  
2.  輸入下列 PowerShell **Get-Item** 命令：  
  
    ```  
    PS SQLSERVER:\SQL\NODE1\DEFAULT> get-item . | select IsHadrEnabled  
    ```  
  
    > [!NOTE]  
    >  若要檢視 Cmdlet 的語法，請在 **PowerShell 環境中使用** Get-Help [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Cmdlet。 如需詳細資訊，請參閱 [Get Help SQL Server PowerShell](../../../powershell/sql-server-powershell.md)。  
  
 **若要設定和使用 SQL Server PowerShell 提供者**  
  
-   [SQL Server PowerShell 提供者](../../../powershell/sql-server-powershell-provider.md)  
  
##  <a name="enable-always-on-availability-groups"></a><a name="EnableAOAG"></a> 啟用 AlwaysOn 可用性群組  
 **若要啟用 AlwaysOn，請使用：**  
  
-   [SQL Server 組態管理員](#SQLCM2Procedure)  
  
-   [PowerShell](#PScmd2Procedure)  
  
###  <a name="using-sql-server-configuration-manager"></a><a name="SQLCM2Procedure"></a> 使用 SQL Server 組態管理員  
 **啟用 AlwaysOn 可用性群組**  
  
1.  連線到 Windows Server 容錯移轉叢集 (WSFC) 節點，其中裝載了您要啟用 AlwaysOn 可用性群組的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體。  
  
2.  指向 [開始]  功能表上的 [所有程式] ，然後依序指向 [ [!INCLUDE[ssCurrentUI](../../../includes/sscurrentui-md.md)]] 和 [組態工具] ，再按一下 [SQL Server 組態管理員] 。  
  
3.  在 [SQL Server 組態管理員] 中，按一下 [SQL Server 服務]，以滑鼠右鍵按一下 [SQL Server] ( **\<**_instance name_**>)** ，其中 **\<**_instance name_**>** 是要啟用 AlwaysOn 可用性群組的本機伺服器執行個體名稱，然後按一下 [屬性]。  
  
4.  選取 [AlwaysOn 高可用性] 索引標籤。  
  
5.  確認 [Windows 容錯移轉叢集名稱] 欄位包含本機容錯移轉叢集的名稱。 如果此欄位為空白，表示此伺服器執行個體目前不支援 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)]。 有可能本機電腦不是叢集節點，WSFC 叢集已經關閉，或者此版本 [!INCLUDE[ssCurrent](../../../includes/sscurrent-md.md)] 不支援 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)]。  
  
6.  選取 [啟用 AlwaysOn 可用性群組] 核取方塊，然後按一下 [確定]。  
  
     [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 組態管理員會儲存您的變更。 然後您必須手動重新啟動 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 服務。 這讓您可以選擇最適合您業務需求的重新啟動時間。 當 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 服務重新啟動時，AlwaysOn 就會啟用，而且 **IsHadrEnabled** 伺服器屬性會設定為 1。  
  
###  <a name="using-sql-server-powershell"></a><a name="PScmd2Procedure"></a> 使用 SQL Server PowerShell  
 **啟用 AlwaysOn**  
  
1.  將目錄 (**cd**) 變更為要啟用 AlwaysOn 可用性群組的伺服器執行個體。  
  
2.  使用 **Enable-SqlAlwaysOn** Cmdlet 啟用 AlwaysOn 可用性群組。  
  
     若要檢視 Cmdlet 的語法，請在 **PowerShell 環境中使用** Get-Help [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Cmdlet。 如需詳細資訊，請參閱 [Get Help SQL Server PowerShell](../../../powershell/sql-server-powershell.md)。  
  
    > [!NOTE]  
    >  如需如何控制 **Enable-SqlAlwaysOn** Cmdlet 是否重新啟動 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 服務的資訊，請參閱本主題稍後的 [Cmdlet 在何時重新啟動 SQL Server 服務](#WhenCmdletRestartsSQL)。  
  
 **若要設定和使用 SQL Server PowerShell 提供者**  
  
-   [SQL Server PowerShell 提供者](../../../powershell/sql-server-powershell-provider.md)  
  
####  <a name="example-enable-sqlalwayson"></a><a name="ExmplEnable-SqlHadrServic"></a> 範例：Enable-SqlAlwaysOn  
 下列 PowerShell 命令會在 SQL Server 執行個體 (電腦\\執行個體) 上啟用 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)]。  
  
```  
Enable-SqlAlwaysOn -Path SQLSERVER:\SQL\Computer\Instance  
```  
  
##  <a name="disable-always-on-availability-groups"></a><a name="DisableAOAG"></a> 停用 AlwaysOn 可用性群組  
  
-   **在您停用 AlwaysOn 之前：**  
  
     [建議](#Recommendations)  
  
-   **若要停用 AlwaysOn，請使用：**  
  
    -   [SQL Server 組態管理員](#SQLCM3Procedure)  
  
    -   [PowerShell](#PScmd3Procedure)  
  
-   **後續操作：** [停用 Always On 之後](#FollowUp)  
  
> [!IMPORTANT]  
>  一次只在一個伺服器執行個體上停用 AlwaysOn。 停用 AlwaysOn 可用性群組之後，等到 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 服務重新啟動後才能在另一個伺服器執行個體繼續進行。  
  
###  <a name="recommendations"></a><a name="Recommendations"></a> 建議  
 在伺服器執行個體上停用 AlwaysOn 之前，我們建議您執行下列動作：  
  
1.  如果伺服器執行個體目前裝載要保留之可用性群組的主要複本，建議手動將可用性群組容錯移轉至已同步處理的次要複本 (如果可能的話)。 如需詳細資訊，請參閱[執行可用性群組的已規劃手動容錯移轉 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/perform-a-planned-manual-failover-of-an-availability-group-sql-server.md)。  
  
2.  移除所有本機次要複本。 如需詳細資訊，請參閱[將次要複本從可用性群組移除 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/remove-a-secondary-replica-from-an-availability-group-sql-server.md)。  
  
###  <a name="using-sql-server-configuration-manager"></a><a name="SQLCM3Procedure"></a> 使用 SQL Server 組態管理員  
 **停用 AlwaysOn**  
  
1.  連線到 Windows Server 容錯移轉叢集 (WSFC) 節點，其中裝載了您要停用 AlwaysOn 可用性群組的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體。  
  
2.  指向 **[開始]** 功能表上的 **[所有程式]** ，然後依序指向 [ [!INCLUDE[ssCurrentUI](../../../includes/sscurrentui-md.md)]] 和 **[組態工具]** ，再按一下 **[SQL Server 組態管理員]** 。  
  
3.  在 [SQL Server 設定管理員] 中，按一下 [SQL Server 服務]，以滑鼠右鍵按一下 [SQL Server] ( **\<**_instance name_**>)** ，其中 **\<**_instance name_**>** 是要停用 Always On 可用性群組的本機伺服器執行個體名稱，然後按一下 [屬性]。  
  
4.  在 [AlwaysOn 高可用性] 索引標籤上，取消選取 [啟用 AlwaysOn 可用性群組] 核取方塊，然後按一下 [確定]。  
  
     [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 組態管理員會儲存您的變更並重新啟動 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 服務。 當 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 服務重新啟動時，AlwaysOn 就會停用，而且 **IsHadrEnabled** 伺服器屬性會設定為 0，表示 AlwaysOn 可用性群組已停用。  
  
5.  建議您閱讀本主題稍後[後續操作：停用 Always On 之後](#FollowUp)中的資訊。  
  
###  <a name="using-sql-server-powershell"></a><a name="PScmd3Procedure"></a> 使用 SQL Server PowerShell  
 **停用 AlwaysOn**  
  
1.  將目錄 (**cd**) 變更為要停用 Always On 可用性群組且目前已啟用的伺服器執行個體。  
  
2.  使用 **Disable-SqlAlwaysOn** Cmdlet 啟用 AlwaysOn 可用性群組。  
  
     例如，下列命令會在 SQL Server 執行個體 (電腦\\執行個體) 上停用 AlwaysOn 可用性群組。  此命令需要重新啟動執行個體，而且系統將提示您確認重新啟動。  
  
    ```  
    Disable-SqlAlwaysOn -Path SQLSERVER:\SQL\Computer\Instance  
    ```  
  
    > [!IMPORTANT]  
    >  如需如何控制 **Disable-SqlAlwaysOn** Cmdlet 是否重新啟動 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 服務的資訊，請參閱本主題稍後的 [Cmdlet 在何時重新啟動 SQL Server 服務](#WhenCmdletRestartsSQL)。  
  
     若要檢視 Cmdlet 的語法，請在 **PowerShell 環境中使用** Get-Help [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Cmdlet。 如需詳細資訊，請參閱 [Get Help SQL Server PowerShell](../../../powershell/sql-server-powershell.md)。  
  
 **若要設定和使用 SQL Server PowerShell 提供者**  
  
-   [SQL Server PowerShell 提供者](../../../powershell/sql-server-powershell-provider.md)  
  
###  <a name="follow-up-after-disabling-always-on"></a><a name="FollowUp"></a> 後續操作：停用 Always On 之後  
 停用 AlwaysOn 可用性群組之後，必須重新啟動 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體。 SQL Server 組態管理員會自動重新啟動伺服器執行個體。 但是，如果您使用 **Disable-SqlAlwaysOn** Cmdlet，則需要手動重新啟動伺服器執行個體。 如需詳細資訊，請參閱 [sqlservr Application](../../../tools/sqlservr-application.md)。  
  
 在重新啟動的伺服器執行個體：  
  
-   在 SQL Server 啟動時，可用性資料庫不會啟動，因此無法存取。  
  
-   唯一支援的 AlwaysOn [!INCLUDE[tsql](../../../includes/tsql-md.md)] 陳述式是 [DROP AVAILABILITY GROUP](../../../t-sql/statements/drop-availability-group-transact-sql.md)。 不支援 CREATE AVAILABILITY GROUP、ALTER AVAILABILITY GROUP，以及 ALTER DATABASE 的 SET HADR 選項。  
  
-   [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 中繼資料和 WSFC 中的 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] 組態資料不受 AlwaysOn 可用性群組停用所影響。  
  
 如果您在裝載一或多個可用性群組之可用性複本的每個伺服器執行個體上永久停用 AlwaysOn 可用性群組，建議您完成下列步驟：  
  
1.  如果您在停用 AlwaysOn 之前未移除本機可用性複本，請針對裝載可用性複本的伺服器執行個體刪除 (卸除) 每個可用性群組。 如需刪除可用性群組的相關資訊，請參閱[移除可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/remove-an-availability-group-sql-server.md)。  
  
2.  若要移除遺留的中繼資料，請在屬於原始 WSFC 的伺服器執行個體上，刪除 (卸除) 每個受影響的可用性群組。  
  
3.  任何主要資料庫仍會繼續可供所有連接存取，但是主要和次要資料庫之間的資料同步處理會停止。  
  
4.  次要資料庫會進入 RESTORING 狀態。 您可以刪除它們，或透過使用 RESTORE WITH RECOVERY 還原它們。 但是，還原的資料庫不再參與可用性群組資料同步處理。  
  
##  <a name="when-does-a-cmdlet-restart-the-sql-server-service"></a><a name="WhenCmdletRestartsSQL"></a> Cmdlet 在何時重新啟動 SQL Server 服務  
 在目前執行中的伺服器執行個體上，使用 **Enable-SqlAlwaysOn** 或 **Disable-SqlAlwaysOn** 變更目前 AlwaysOn 設定，可能會導致 SQL Server 服務重新啟動。 重新啟動行為取決於下列條件：  
  
|指定 -NoServiceRestart 參數|指定 -Force 參數|[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 服務是否重新啟動？|  
|--------------------------------------------|---------------------------------|---------------------------------------------------------|  
|否|否|根據預設。 但指令程式會出現提示，如下所示：<br /><br /> **若要完成這個動作，我們必須重新啟動伺服器執行個體 '<執行個體名稱>' 的 SQL Server 服務。您要繼續嗎?**<br /><br /> **[Y] 是 [N] 否 [S] 暫停 [?] 說明 (預設為 "Y")：**<br /><br /> 如果指定 **N** 或 **S**，就不會重新啟動服務。|  
|否|是|服務會重新啟動。|  
|是|否|服務不會重新啟動。|  
|是|是|服務不會重新啟動。|  
  
## <a name="see-also"></a>另請參閱  
 [AlwaysOn 可用性群組概觀 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server.md)   
 [SERVERPROPERTY &#40;Transact-SQL&#41;](../../../t-sql/functions/serverproperty-transact-sql.md)  
  
