---
title: 在 SSMS 中設定可用性群組
description: 使用 SQL Server Management Studio (SSMS) 中的 [新增可用性群組精靈] 來建立和設定 Always On 可用性群組。
ms.custom: seo-lt-2019
ms.date: 05/17/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: conceptual
f1_keywords:
- sql13.swb.newagwizard.f1
- sql13.swb.newavgroupwiz.f1
helpviewer_keywords:
- New Availability Group Wizard, availability replicas
- Availability Groups [SQL Server], wizards
- Availability Groups [SQL Server], creating
ms.assetid: e1f1dccc-9e65-471d-8fd1-b45085c9484a
author: cawrites
ms.author: chadam
ms.openlocfilehash: 59b0e82558e14e29e4fad69ddc86b76d982a634a
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98171100"
---
# <a name="use-the-availability-group-wizard-sql-server-management-studio"></a>使用可用性群組精靈 (SQL Server Management Studio)
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]
  本主題描述如何使用  **中的 [新增可用性群組精靈]** [!INCLUDE[ssManStudioFull](../../../includes/ssmanstudiofull-md.md)]，在 [!INCLUDE[ssCurrent](../../../includes/sscurrent-md.md)] 中建立和設定 AlwaysOn 可用性群組。 *「可用性群組」* (Availability Group) 會定義當做單一單位容錯移轉的一組使用者資料庫，以及支援容錯移轉的一組容錯移轉夥伴 (也稱為 *「可用性複本」* (Availability Replica))。  
  
> [!NOTE]  
>  如需可用性群組的簡介，請參閱 [AlwaysOn 可用性群組概觀 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server.md)中的 PowerShell，透過 PowerShell Cmdlet 建立及設定 AlwaysOn 可用性群組。  
    
##  <a name="before-you-begin"></a><a name="BeforeYouBegin"></a> 開始之前  
 我們強烈建議您先閱讀本節內容，然後再嘗試建立您的第一個可用性群組。  
  
###  <a name="prerequisites-restrictions-and-recommendations"></a><a name="Prerequisites"></a> 必要條件、限制及建議  

在大多數情況下，您可以使用 [新增可用性群組精靈] 來完成建立和設定可用性群組所需的所有工作。 不過，您可能需要手動完成一些工作。  
  
- 如果要使用 Windows Server 容錯移轉叢集 (WSFC) 叢集類型來裝載可用性群組，請確認裝載可用性複本的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體位於相同 WSFC 的不同叢集伺服器 (或節點) 上。 此外，請確認每個伺服器執行個體都符合所有其他 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] 必要條件。 如需詳細資訊，強烈建議您閱讀 [AlwaysOn 可用性群組的必要條件、限制和建議 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/prereqs-restrictions-recommendations-always-on-availability.md)。  
  
-   如果您選取來裝載可用性複本的伺服器執行個體是以網域使用者帳戶執行，且還沒有資料庫鏡像端點，則此精靈可以建立端點，並授與伺服器執行個體服務帳戶的 CONNECT 權限。 但是，如果 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 服務是以內建帳戶 (例如本機系統、本機服務或網路服務) 或非網域帳戶的身分執行，您就必須將憑證用於端點驗證，而且精靈無法在此伺服器執行個體上建立資料庫鏡像端點。 在此情況下，我們建議您先手動建立資料庫鏡像端點，然後再啟動 [新增可用性群組精靈]。  
  
     **使用資料庫鏡像端點憑證：**  
  
     [CREATE ENDPOINT &#40;Transact-SQL&#41;](../../../t-sql/statements/create-endpoint-transact-sql.md)  
  
     [使用資料庫鏡像端點憑證 &#40;Transact-SQL&#41;](../../../database-engine/database-mirroring/use-certificates-for-a-database-mirroring-endpoint-transact-sql.md)  
  
-   SQL Server 容錯移轉叢集執行個體 (FCI) 不支援依照可用性群組進行自動容錯移轉，因此任何由 FCI 裝載的可用性複本只能設定為手動容錯移轉。  
  
-   **執行完整初始資料同步處理精靈的必要條件**  
  
    -   每個裝載可用性群組複本之伺服器執行個體上的所有資料庫檔案路徑均須相同。  
  
    -   任何裝載次要複本的伺服器執行個體上皆不應存在主要資料庫名稱。 這表示目前還不可有任何新次要資料庫。  
  
    -   您必須指定網路共用，精靈才能建立及存取備份。 對於主要複本，用於啟動 [!INCLUDE[ssDE](../../../includes/ssde-md.md)] 的帳戶必須具有網路共用的讀取與寫入檔案系統權限。 如果是次要複本，此帳戶必須有網路共用的讀取權限。  
  
        > [!IMPORTANT]  
        >  記錄備份將是記錄備份鏈結的一部分。 請適當地儲存記錄備份檔案。  
  
     如果您無法使用精靈執行完整初始資料同步處理，則必須手動準備次要資料庫。 您可以在執行精靈前後進行這項作業。 如需詳細資訊，請參閱 [針對可用性群組手動準備次要資料庫 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/manually-prepare-a-secondary-database-for-an-availability-group-sql-server.md)中的 PowerShell，將次要資料庫聯結至 AlwaysOn 可用性群組。  
  
###  <a name="security"></a><a name="Security"></a> Security  
  
####  <a name="permissions"></a><a name="Permissions"></a> 權限  
 需要 **系統管理員 (sysadmin)** 固定伺服器角色的成員資格，以及 CREATE AVAILABILITY GROUP 伺服器權限、ALTER ANY AVAILABILITY GROUP 權限或 CONTROL SERVER 權限。  
  
 如果您想要允許 [可用性群組精靈] 管理資料庫鏡像端點，也需要 CONTROL ON ENDPOINT 權限。  
  
##  <a name="using-the-new-availability-group-wizard"></a><a name="RunAGwiz"></a> 使用新增可用性群組精靈  
  
1.  在 [物件總管] 中，連接到裝載主要複本的伺服器執行個體。  
  
2.  依序展開 [Always On 高可用性]  節點和 [可用性群組]  節點。  
  
3.  若要啟動 [新增可用性群組精靈]，請選取 **[新增可用性群組精靈]** 命令。  
  
4.  當您初次執行此精靈時，將會出現 **[簡介]** 頁面。 如果將來要略過此頁面，您可以按一下 **[不要再顯示此頁面]** 。 閱讀這個頁面之後，請按 **[下一步]** 。  
  
5.  在 [指定可用性群組選項]  頁面上，於 [可用性群組名稱]  欄位中輸入新的可用性群組名稱。 此名稱必須是有效的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 識別碼，該識別碼在叢集及整體網域中必須是唯一的。 可用性群組名稱的最大長度為 128 個字元。

6. 接下來，指定叢集類型。 可能的叢集類型取決於 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 版本和作業系統。 選擇 [WSFC]  、[EXTERNAL]  或 [NONE]  。 如需詳細資訊，請參閱[指定可用性群組名稱頁面](specify-availability-group-name-page.md)。
 
6.  在 **[選取資料庫]** 頁面上，方格會列出連接之伺服器執行個體上有資格變成 *「可用性資料庫」* (Availability Database) 的使用者資料庫。 請選取一個或多個列出的資料庫，以便參與新的可用性群組。 這些資料庫一開始將會成為初始 *「主要資料庫」* (Primary Database)。  
  
     針對每個列出的資料庫， **[大小]** 資料行會顯示資料庫大小 (如果已知的話)。 [狀態]  資料行會指出給定的資料庫是否符合可用性資料庫的[必要條件](../../../database-engine/availability-groups/windows/prereqs-restrictions-recommendations-always-on-availability.md)。 如果不符合必要條件，簡短狀態描述會指出資料庫不合格的原因。例如，資料庫沒有使用完整復原模式。 如需詳細資訊，請按一下狀態描述。  
  
     如果您變更資料庫讓它符合資格，請按一下 **[重新整理]** 更新資料庫方格。  
  
     如果資料庫含有資料庫主要金鑰，請在 [密碼]  資料行輸入資料庫主要金鑰的密碼。  
  
7.  在 **[指定複本]** 頁面上，針對新的可用性群組指定並設定一個或多個複本。 此頁面包含四個索引標籤。 下表將介紹這些索引標籤。 如需詳細資訊，請參閱[指定複本頁面 &#40;新增可用性群組精靈：加入複本精靈&#41;](../../../database-engine/availability-groups/windows/specify-replicas-page-new-availability-group-wizard-add-replica-wizard.md) 主題。  
  
    |索引標籤|簡短描述|  
    |---------|-----------------------|  
    |**複本**|使用此索引標籤可指定將裝載次要複本的每個 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體。 請注意，您目前所連接的伺服器執行個體必須裝載主要複本。|  
    |**端點**|使用此索引標籤可驗證任何現有的資料庫鏡像端點，此外，如果在其服務帳戶使用 Windows 驗證的伺服器執行個體上缺少此端點，則可自動建立該端點。<br /><br /> 注意：如果有任何伺服器執行個體在非網域使用者帳戶之下執行，您需要對伺服器執行個體進行手動變更，然後才可以在精靈中繼續執行。 如需詳細資訊，請參閱本主題前面的＜ [必要條件](#Prerequisites)＞。|  
    |**備份喜好設定**|使用此索引標籤可指定整個可用性群組的備份喜好設定，以及個別可用性複本的備份優先權。|  
    |**接聽程式**|使用此索引標籤可建立可用性群組接聽程式。 根據預設，精靈不會建立接聽程式。|  
  
8.  在 **[選取初始資料同步處理]** 頁面上，選擇您要如何建立新的次要資料庫並將它聯結至可用性群組。 選擇下列其中一個選項：  
  
    -   **自動植入**  
  
         SQL Server 會自動為群組中的每個資料庫建立次要複本。 自動植入要求參與群組之每個 SQL Server 執行個體上的資料和記錄檔案路徑都必須相同。 適用於 [!INCLUDE[sssql16-md.md](../../../includes/sssql16-md.md)] 和更新版本。 請參閱[自動初始化 AlwaysOn 可用性群組](automatically-initialize-always-on-availability-group.md)。
    
    -   **完整的資料庫及記錄備份**  
  
         只有在您的環境符合自動啟動初始資料同步處理的需求時，才選取此選項 (如需詳細資訊，請參閱本主題稍早的 [必要條件、限制和建議](#Prerequisites))。  
  
         如果您選取 **[完整]** ，在建立可用性群組之後，精靈會將每個主要資料庫及其交易記錄備份至網路共用，並在裝載次要複本的每個伺服器執行個體上還原這些備份。 然後精靈會將每個次要資料庫聯結至可用性群組。  
  
         在 **[指定所有複本可存取的共用網路位置:]** 欄位中，指定裝載複本的所有伺服器執行個體都有讀寫存取的備份共用。 如需詳細資訊，請參閱本主題前面的＜ [必要條件](#Prerequisites)＞。  在驗證步驟中，此精靈會執行測試以確定所提供的網路位置有效，測試會在主要複本上建立名為 "BackupLocDb_" 的資料庫，後面接著 GUID，並備份到所提供的網路位置，然後在次要複本上將它還原。 您可以放心地將此資料庫隨其備份歷程記錄和備份檔案一同刪除 (如果精靈無法將其刪除)。
  
    -   **僅聯結**  
  
         如果您已經在將裝載次要複本的伺服器執行個體上手動備妥次要資料庫，就可以選取此選項。 然後精靈會將現有的次要資料庫聯結至可用性群組。  
  
    -   **略過初始資料同步處理**  
  
         如果要使用您自己的主要資料庫的資料庫和記錄備份，請選取此選項。 如需詳細資訊，請參閱 [於 AlwaysOn 次要資料庫啟動資料移動 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/start-data-movement-on-an-always-on-secondary-database-sql-server.md)。  
  
9. **[驗證]** 頁面會驗證您在此精靈中指定的值是否符合 [新增可用性群組精靈] 的需求。 若要進行變更，您可以按 **[上一步]** 返回先前的精靈頁面，以變更一個或多個值。 然後按 **[下一步]** 返回 **[驗證]** 頁面，再按一下 **[重新執行驗證]** 。  
  
10. 在 **[摘要]** 頁面上，檢閱您為新的可用性群組的選擇。 若要進行變更，請按 **[上一步]** 返回相關頁面。 進行變更之後，請按 **[下一步]** 返回 **[摘要]** 頁面。  
  
    > [!IMPORTANT]  
    >  如果沒有將要裝載新可用性複本之伺服器執行個體的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 服務帳戶做為登入存在，[新增可用性群組精靈] 就需要建立登入。 在 **[摘要]** 頁面上，精靈會顯示要建立之登入的資訊。 如果您按一下 **[完成]** ，精靈就會為 SQL Server 服務帳戶建立此登入，並且授與登入 CONNECT 權限。  
  
     如果您對所做的選擇感到滿意時，可以選擇按一下 **[指令碼]** ，建立精靈將執行之步驟的指令碼。 然後，若要建立及設定新的可用性群組，請按一下 **[完成]** 。  
  
11. **[進度]** 頁面會顯示建立可用性群組之步驟的進度 (設定端點、建立可用性群組，並將次要複本加入群組中)。  
  
12. 當這些步驟完成時， **[結果]** 頁面會顯示每個步驟的結果。 如果所有這些步驟都成功，表示新的可用性群組已完成設定。 如果任何步驟導致錯誤，您可能需要手動完成組態，或者針對失敗的步驟使用精靈。 如需有關給定錯誤原因的詳細資訊，請按一下 **[結果]** 資料行中相關聯的 [錯誤] 連結。  
  
     當精靈完成時，按一下 **[關閉]** 以結束。  
  
##  <a name="related-tasks"></a><a name="RelatedTasks"></a> 相關工作  
 **若要完成可用性群組組態**  
  
-   [將次要複本聯結至可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/join-a-secondary-replica-to-an-availability-group-sql-server.md)  
  
-   [針對可用性群組手動準備次要資料庫 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/manually-prepare-a-secondary-database-for-an-availability-group-sql-server.md)  
  
-   [將次要資料庫聯結至可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/join-a-secondary-database-to-an-availability-group-sql-server.md)  
  
-   [建立或設定可用性群組接聽程式 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/create-or-configure-an-availability-group-listener-sql-server.md)  
  
 **建立可用性群組的其他方法**  
  
-   [使用新增可用性群組對話方塊 &#40;SQL Server Management Studio&#41;](../../../database-engine/availability-groups/windows/use-the-new-availability-group-dialog-box-sql-server-management-studio.md)  
  
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
  
-   **部落格：**  
  
     [Always On - HADRON Learning Series: Worker Pool Usage for HADRON Enabled Databases (AlwaysOn - HADRON 學習系列：資料庫啟用 HADRON 時工作者集區的使用方式)](/archive/blogs/psssql/alwayson-hadron-learning-series-worker-pool-usage-for-hadron-enabled-databases)  
  
     [SQL Server AlwaysOn 團隊部落格：官方 SQL Server AlwaysOn 團隊部落格](/archive/blogs/sqlalwayson/)  
  
     [CSS SQL Server 工程師部落格](/archive/blogs/psssql/)  
  
-   **影片：**  
  
     [Microsoft SQL Server Code-Named "Denali" AlwaysOn Series,Part 1: Introducing the Next Generation High Availability Solution (Microsoft SQL Server 代碼 "Denali" AlwaysOn 系列第一部分：新一代高可用性解決方案簡介)](https://channel9.msdn.com/Events/TechEd/NorthAmerica/2011/DBI302)  
  
     [Microsoft SQL Server Code-Named "Denali" Always On Series,Part 2: Building a Mission-Critical High Availability Solution Using Always On (Microsoft SQL Server 代碼 "Denali" AlwaysOn 系列第二部分：使用 AlwaysOn 建立任務關鍵性高可用性解決方案)](https://channel9.msdn.com/Events/TechEd/NorthAmerica/2011/DBI404)  
  
-   **白皮書：**  
  
     [Microsoft SQL Server AlwaysOn 高可用性和災害復原方案指南](/previous-versions/sql/sql-server-2012/hh781257(v=msdn.10))  
  
     [Microsoft 的 SQL Server 2012 白皮書](https://msdn.microsoft.com/library/hh403491.aspx)  
  
     [SQL Server 客戶諮詢團隊白皮書](https://techcommunity.microsoft.com/t5/DataCAT/bg-p/DataCAT/)  
  
## <a name="alternate-ways-to-create-availability-groups"></a>建立可用性群組的其他方法

除了使用 [新增可用性群組精靈] 以外，您也可以使用 [!INCLUDE[tsql](../../../includes/tsql-md.md)] 或 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] PowerShell 指令程式。 如需詳細資訊，請參閱 [建立可用性群組 &#40;Transact-SQL&#41;](../../../database-engine/availability-groups/windows/create-an-availability-group-transact-sql.md) 或 [建立可用性群組 &#40;SQL Server PowerShell&#41;](../../../database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell.md)中建立和設定 AlwaysOn 可用性群組。  


## <a name="see-also"></a>另請參閱  
 [資料庫鏡像端點 &#40;SQL Server&#41;](../../../database-engine/database-mirroring/the-database-mirroring-endpoint-sql-server.md)   
 [AlwaysOn 可用性群組概觀 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server.md)   
 [AlwaysOn 可用性群組的必要條件、限制和建議 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/prereqs-restrictions-recommendations-always-on-availability.md)  
