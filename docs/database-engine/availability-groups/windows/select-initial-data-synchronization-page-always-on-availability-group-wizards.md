---
title: 選取初始資料同步頁面 (可用性群組精靈)
description: 描述 SQL Server Management Studio (SSMS) 中 [Always On 可用性群組精靈] 的 [選取初始資料同步處理] 頁面。
ms.custom: seo-lt-2019
ms.date: 05/17/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: conceptual
f1_keywords:
- sql13.swb.adddatabasewizard.selectinitialdatasync.f1
- sql13.swb.newagwizard.selectinitialdatasync.f1
- sql13.swb.addreplicawizard.selectinitialdatasync.f1
ms.assetid: 457b1140-4819-4def-8f7c-54a406e6db12
author: cawrites
ms.author: chadam
ms.openlocfilehash: 73ee631c0dc853a60e07f09aa2ade5ce58487a0e
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98171090"
---
# <a name="select-initial-data-synchronization-page-always-on-availability-group-wizards"></a>選取初始資料同步頁面 (Always On 可用性群組精靈)
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]

  使用 AlwaysOn [選取初始資料同步處理]  頁面，指定新次要資料庫之初始資料同步處理的喜好設定。 此頁面由三個精靈所共用：[!INCLUDE[ssAoNewAgWiz](../../../includes/ssaonewagwiz-md.md)]、[!INCLUDE[ssAoAddRepWiz](../../../includes/ssaoaddrepwiz-md.md)] 和 [!INCLUDE[ssAoAddDbWiz](../../../includes/ssaoadddbwiz-md.md)]。  
  
 可能的選項包含 [自動植入]  、[完整的資料庫及記錄備份]  、[僅加入]  或 [略過初始資料同步處理]  。 選取 [自動植入]  、[完整]  或 [僅加入]  之前，請確認您的環境符合必要條件。  
    
##  <a name="recommendations"></a><a name="Recommendations"></a> 建議  
  
-   在初始資料同步處理期間，暫停主要資料庫的記錄備份工作。  
  
-   大型資料庫的完整備份與還原作業可能需要大量的時間及資源。 在此情況下，建議您自行準備次要資料庫。 如需詳細資訊，請參閱本主題稍後的 [手動準備次要資料庫](#PrepareSecondaryDbs)。  
  
-   完整初始資料同步處理需要指定網路共用。 建議您先對網路共用資料夾的存取權限實作安全性計畫，然後使用精靈執行完整初始資料同步處理。 此預防措施很重要，因為有此資料夾 READ 權限的任何人都可以存取備份檔案中的潛在敏感性資料。 此外，為了保護您的備份和還原作業，建議您對裝載可用性複本的每個伺服器執行個體與網路共用資料夾之間的網路通道進行保全。  
  
     如果備份和還原作業必須是高度保全，建議您選取 **[僅聯結]** 或 **[略過初始資料同步處理]** 選項。  
  
## <a name="automatic-seeding"></a><a name="Auto"></a> 自動植入
 
 SQL Server 會自動為群組中的每個資料庫建立次要複本。 自動植入要求參與群組之每個 SQL Server 執行個體上的資料和記錄檔案路徑都必須相同。 適用於 [!INCLUDE[sssql16-md.md](../../../includes/sssql16-md.md)] 和更新版本。 請參閱[自動初始化 AlwaysOn 可用性群組](automatically-initialize-always-on-availability-group.md)。

##  <a name="full-database-and-log-backup"></a><a name="Full"></a> 完整的資料庫及記錄備份 
 [完整的資料庫及記錄備份]  選項會在一個工作流程中，對每一個主要資料庫執行數項作業，包括建立主要資料庫的完整和記錄備份；透過還原裝載次要複本之每個伺服器執行個體上的這些備份，建立對應的次要資料庫；以及將每個次要資料庫加入可用性群組。  
  
 只有在環境符合下列使用完整初始資料同步處理的必要條件，且您要精靈自動啟動資料同步處理時，才選取此選項。  
  
 **使用完整的資料庫及記錄備份初始資料同步處理的必要條件**  
  
-   每個裝載可用性群組複本之伺服器執行個體上的所有資料庫檔案路徑均須相同。  
  
    > [!NOTE]  
    >  若執行精靈所在的伺服器執行個體與要裝載次要複本之伺服器執行個體間的備份和還原檔案路徑不同。 備份和還原作業必須手動使用 WITH MOVE 選項執行。 如需詳細資訊，請參閱本主題稍後的 [手動準備次要資料庫](#PrepareSecondaryDbs)。  
  
-   任何裝載次要複本的伺服器執行個體上皆不應存在主要資料庫名稱。 這表示目前還不可有任何新次要資料庫。  
  
-   您必須指定網路共用，精靈才能建立及存取備份。 對於主要複本，用於啟動 [!INCLUDE[ssDE](../../../includes/ssde-md.md)] 的帳戶必須具有網路共用的讀取與寫入檔案系統權限。 如果是次要複本，此帳戶必須有網路共用的讀取權限。  
  
    > [!IMPORTANT]  
    >  記錄備份將是記錄備份鏈結的一部分。 請適當地儲存記錄備份檔案。  
  
 **若不符合必要條件**  
  
 精靈將無法建立此可用性群組的次要資料庫。 如需有關如何準備的詳細資訊，請參閱本主題稍後的 [手動準備次要資料庫](#PrepareSecondaryDbs)。  
  
 **若符合必要條件**  
  
 若符合所有必要條件，且您要精靈執行完整初始資料同步處理，請選取 [完整的資料庫及記錄備份]  選項，並指定網路共用。 如此一來，精靈即會建立完整的資料庫及每個選定資料庫的記錄備份，並將這些備份置於您指定的網路共用。 然後，在裝載其中一個新次要複本的每個伺服器執行個體上，精靈會透過使用 RESTORE WITH NORECOVERY 還原備份來建立次要資料庫。 建立每個次要資料庫之後，精靈會將新次要資料庫聯結至可用性群組。 聯結次要資料庫之後，立即會在該資料庫上啟動資料同步處理。  
  
 **指定所有複本可存取的共用網路位置**  
 若要建立及還原備份，精靈會要求您指定網路共用。 在將裝載可用性複本的每個伺服器執行個體上，用來啟動 [!INCLUDE[ssDE](../../../includes/ssde-md.md)] 的帳戶必須有網路共用的讀取與寫入檔案系統權限。  
  
> [!IMPORTANT]  
>  記錄備份將是記錄備份鏈結的一部分。 請適當地儲存它們的備份檔案。  
  
##  <a name="join-only"></a><a name="Joinonly"></a> [僅聯結]  
 只有在裝載可用性群組之次要複本的每個伺服器執行個體上已經有新次要資料庫時，才選取此選項。 如需有關準備次要資料庫的詳細資訊，請參閱本節稍後的 [手動準備次要資料庫](#PrepareSecondaryDbs)。  
  
 如果您選取 **[僅聯結]** ，精靈會嘗試將每個現有的次要資料庫聯結至可用性群組。  
  
## <a name="skip-initial-data-synchronization"></a><a name="Skip"></a> [略過初始資料同步處理]  
 只有在您要對每個主要資料庫執行您自己的資料庫和記錄備份、將它們手動還原到裝載次要複本的每個伺服器執行個體時，才選取此選項。 結束精靈之後，您需要聯結每個次要複本上的每個次要資料庫。  
  
> [!NOTE]  
>  如需詳細資訊，請參閱 [於 AlwaysOn 次要資料庫啟動資料移動 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/start-data-movement-on-an-always-on-secondary-database-sql-server.md)。  
  
##  <a name="to-prepare-secondary-databases-manually"></a><a name="PrepareSecondaryDbs"></a> 手動準備次要資料庫  
 若要在任何 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] 精靈之外獨立準備次要資料庫，可以使用下列方法之一：  
  
-   使用 RESTORE WITH NORECOVERY，手動還原主要資料庫的最近資料庫備份，然後使用 RESTORE WITH NORECOVERY，還原每個後續的記錄備份。 若主要與次要資料庫的檔案路徑不同，必須使用 WITH MOVE 選項。 在裝載可用性群組之次要複本的每個伺服器執行個體上，執行此還原順序。  您可以使用 [!INCLUDE[tsql](../../../includes/tsql-md.md)] 或 PowerShell 執行這些備份和還原作業。  
  
     **如需詳細資訊：＜＞**  
  
     [針對可用性群組手動準備次要資料庫 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/manually-prepare-a-secondary-database-for-an-availability-group-sql-server.md)  
  
-   若要將一個或多個記錄傳送主要資料庫加入可用性群組，或可從記錄傳送將一個或多個對應的次要資料庫移轉至 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)]。 如需詳細資訊，請參閱本主題稍後的 [從記錄傳送移轉至 AlwaysOn 可用性群組的必要條件 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/prereqs-migrating-log-shipping-to-always-on-availability-groups.md)。  
  
    > [!NOTE]  
    >  為可用性群組建立所有次要資料庫之後，如果您想要在次要複本上執行備份，則需要重新設定可用性群組的自動備份喜好設定。  
  
     **如需詳細資訊：＜＞**  
  
     [從記錄傳送移轉至 AlwaysOn 可用性群組的必要條件 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/prereqs-migrating-log-shipping-to-always-on-availability-groups.md)  
  
     [設定可用性複本的備份 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/configure-backup-on-availability-replicas-sql-server.md)  
  
 建立次要資料庫之後，將所有目前的記錄備份套用至新的次要資料庫。  
  
 您也可選擇在執行精靈之前，先準備所有次要資料庫。 然後在精靈的 **[指定初始資料同步處理]** 頁面上選取 **[僅聯結]** ，以自動將新的次要資料庫聯結至可用性群組。  
  
##  <a name="related-tasks"></a><a name="LaunchWiz"></a> 相關工作  
  
-   [使用新增可用性群組對話方塊 &#40;SQL Server Management Studio&#41;](../../../database-engine/availability-groups/windows/use-the-new-availability-group-dialog-box-sql-server-management-studio.md)  
  
-   [使用 [將複本加入可用性群組中精靈] &#40;SQL Server Management Studio&#41;](../../../database-engine/availability-groups/windows/use-the-add-replica-to-availability-group-wizard-sql-server-management-studio.md)  
  
-   [使用將資料庫加入至可用性群組精靈 &#40;SQL Server Management Studio&#41;](../../../database-engine/availability-groups/windows/availability-group-add-database-to-group-wizard.md)  
  
-   [使用容錯移轉可用性群組精靈 &#40;SQL Server Management Studio&#41;](../../../database-engine/availability-groups/windows/use-the-fail-over-availability-group-wizard-sql-server-management-studio.md)  
  
-   [於 AlwaysOn 次要資料庫啟動資料移動 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/start-data-movement-on-an-always-on-secondary-database-sql-server.md)  
  
-   [將次要資料庫聯結至可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/join-a-secondary-database-to-an-availability-group-sql-server.md)  
  
-   [使用新增可用性群組對話方塊 &#40;SQL Server Management Studio&#41;](../../../database-engine/availability-groups/windows/use-the-new-availability-group-dialog-box-sql-server-management-studio.md)  
  
## <a name="see-also"></a>另請參閱  
 [AlwaysOn 可用性群組概觀 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server.md)  
  
  
