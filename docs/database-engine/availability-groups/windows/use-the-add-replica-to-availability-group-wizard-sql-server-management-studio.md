---
title: 新增複本至可用性群組 (SSMS)
description: 使用 SQL Server Management Studio 中的精靈將複本新增至 Always On 可用性群組。
ms.custom: seo-lt-2019
ms.date: 05/17/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: conceptual
f1_keywords:
- sql13.swb.addreplicawizard.f1
helpviewer_keywords:
- Availability Groups [SQL Server], availability replicas
- Availability Groups [SQL Server], wizards
ms.assetid: 60d962b6-2af4-4394-9190-61939a102bc0
author: cawrites
ms.author: chadam
ms.openlocfilehash: 066a215fa5e616cf33734b3c582ed4789ee43461
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97641792"
---
# <a name="add-a-replica-to-your-always-on-availability-group-using-the-availability-group-wizard-in-sql-server-management"></a>在 SQL Server 管理中使用 [可用性群組精靈] 將複本新增至 Always On 可用性群組
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]
  使用 [將複本新增可用性群組精靈]，可協助您新增次要複本至現有的 AlwaysOn 可用性群組。  
  
> [!NOTE]  
>  如需使用 [!INCLUDE[tsql](../../../includes/tsql-md.md)] 或 PowerShell 將次要複本加入可用性群組的資訊，請參閱 [將次要複本加入至可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/add-a-secondary-replica-to-an-availability-group-sql-server.md)。  
    
##  <a name="before-you-begin"></a><a name="BeforeYouBegin"></a> 開始之前  
 如果您從未將任何可用性複本加入可用性群組中，請參閱 [AlwaysOn 可用性群組的必要條件、限制和建議 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/prereqs-restrictions-recommendations-always-on-availability.md)中的＜伺服器執行個體＞和＜可用性群組和複本＞二節。  
  
##  <a name="prerequisites"></a><a name="Prerequisites"></a> 必要條件  
  
-   您必須連接到裝載目前主要複本的伺服器執行個體。  
  
-   新增次要複本之前，請確認 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 的主機執行個體與現有複本位於相同的 Windows Server 容錯移轉叢集 (WSFC) 中，但位於不同的叢集節點上。 也請確認這個伺服器執行個體符合所有其他 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] 必要條件。 如需詳細資訊，請參閱 [AlwaysOn 可用性群組的必要條件、限制和建議 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/prereqs-restrictions-recommendations-always-on-availability.md)。  
  
-   如果您選取來裝載可用性複本的伺服器執行個體是以網域使用者帳戶執行，且還沒有資料庫鏡像端點，則此精靈可以建立端點，並授與伺服器執行個體服務帳戶的 CONNECT 權限。 但是，如果 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 服務是以內建帳戶 (例如本機系統、本機服務或網路服務) 或非網域帳戶的身分執行，您就必須將憑證用於端點驗證，而且精靈無法在此伺服器執行個體上建立資料庫鏡像端點。 在此情況下，我們建議您先手動建立資料庫鏡像端點，然後再啟動 [將複本加入至可用性群組] 精靈。  
  
     **使用資料庫鏡像端點憑證：**  
  
     [CREATE ENDPOINT &#40;Transact-SQL&#41;](../../../t-sql/statements/create-endpoint-transact-sql.md)  
  
     [使用資料庫鏡像端點憑證 &#40;Transact-SQL&#41;](../../../database-engine/database-mirroring/use-certificates-for-a-database-mirroring-endpoint-transact-sql.md)  
  
-   **使用完整初始資料同步處理的必要條件**  
  
    -   每個裝載可用性群組複本之伺服器執行個體上的所有資料庫檔案路徑均須相同。  
  
    -   任何裝載次要複本的伺服器執行個體上皆不應存在主要資料庫名稱。 這表示目前還不可有任何新次要資料庫。  
  
    -   您必須指定網路共用，精靈才能建立及存取備份。 對於主要複本，用於啟動 [!INCLUDE[ssDE](../../../includes/ssde-md.md)] 的帳戶必須具有網路共用的讀取與寫入檔案系統權限。 如果是次要複本，此帳戶必須有網路共用的讀取權限。  
  
     如果您無法使用精靈執行完整初始資料同步處理，則必須手動準備次要資料庫。 您可以在執行精靈前後進行這項作業。 如需詳細資訊，請參閱 [針對可用性群組手動準備次要資料庫 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/manually-prepare-a-secondary-database-for-an-availability-group-sql-server.md)中的 PowerShell，將次要資料庫聯結至 AlwaysOn 可用性群組。  
   
## <a name="permissions"></a><a name="Permissions"></a> 權限  
 需要可用性群組的 ALTER AVAILABILITY GROUP 權限、CONTROL AVAILABILITY GROUP 權限、ALTER ANY AVAILABILITY GROUP 權限或 CONTROL SERVER 權限。  
  
 如果您想要允許 [將複本加入至可用性群組精靈] 管理資料庫鏡像端點，也需要 CONTROL ON ENDPOINT 權限。  
  
##  <a name="using-the-add-replica-to-availability-group-wizard-sql-server-management-studio"></a><a name="SSMSProcedure"></a> 使用將複本加入至可用性群組精靈 (SQL Server Management Studio)  
 **使用將複本加入至可用性群組精靈**  
  
1.  在 [物件總管] 中，連接到裝載可用性群組之主要複本的伺服器執行個體，然後展開伺服器樹狀目錄。  
  
2.  依序展開 [Always On 高可用性] 節點和 [可用性群組] 節點。  
  
3.  以滑鼠右鍵按一下您要加入次要複本的可用性群組，並選取 [加入複本] 命令。 這會啟動 [將複本加入至可用性群組] 精靈。  
  
4.  在 **[連接到現有次要複本]** 頁面上，連接到可用性群組中的每一個次要複本。 如需詳細資訊，請參閱[連接到現有次要複本頁面 &#40;新增複本精靈：新增資料庫精靈 &#41;](../../../database-engine/availability-groups/windows/connect-to-existing-secondary-replicas-page.md)。  
  
5.  在 **[指定複本]** 頁面上，針對可用性群組指定並設定一個或多個新次要複本。 此頁面包含三個索引標籤。 下表將介紹這些索引標籤。 如需詳細資訊，請參閱[指定複本頁面 &#40;新增可用性群組精靈：新增複本精靈 &#41;](../../../database-engine/availability-groups/windows/specify-replicas-page-new-availability-group-wizard-add-replica-wizard.md)。  
  
    |索引標籤|簡短描述|  
    |---------|-----------------------|  
    |**複本**|使用此索引標籤可指定將裝載新次要複本的每個 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體。|  
    |**端點**|使用此索引標籤來驗證每個新次要複本的現有資料庫鏡像端點 (如果有的話)。 如果在其服務帳戶使用 Windows 驗證的伺服器執行個體上缺少此端點，精靈會嘗試自動建立該端點。<br /><br /> <br /><br /> 注意:如果有任何伺服器執行個體在非網域使用者帳戶之下執行，您需要對伺服器執行個體進行手動變更，才可以在精靈中繼續執行。 如需詳細資訊，請參閱本主題前面的＜ [必要條件](#Prerequisites)＞。|  
    |**備份喜好設定**|使用此索引標籤可指定整個可用性群組的備份喜好設定，如果您希望修改目前設定，可指定個別可用性複本的備份優先權。|  
  
6.  如果選取的複本包含具有資料庫主要金鑰的資料庫，請在 [密碼] 資料行中輸入資料庫主要金鑰的密碼。 [狀態] 資料行會針對具有資料庫主要金鑰的資料庫指出 [需要密碼]。 在 [密碼] 資料行中輸入正確的密碼之前，[下一步] 會呈現灰色。 輸入密碼之後，請按一下 [重新整理]。 如果您已正確輸入密碼，[狀態] 資料行會指出 [已輸入密碼]，而且 [下一步] 會變成可用。  
  
7.  在 **[選取初始資料同步處理]** 頁面上，選擇您要如何建立新的次要資料庫並將它聯結至可用性群組。 選擇下列其中一個選項：  
  
    -   **完整**  
  
         只有在您的環境符合自動啟動初始資料同步處理的需求時，才選取此選項 (如需詳細資訊，請參閱本主題稍早的 [必要條件、限制和建議](#Prerequisites))。  
  
         如果您選取 **[完整]** ，在建立可用性群組之後，精靈會將每個主要資料庫及其交易記錄備份至網路共用，並在裝載新次要複本的每個伺服器執行個體上還原這些備份。 然後精靈會將每個新次要資料庫聯結至可用性群組。  
  
         在 **[指定所有複本可存取的共用網路位置:]** 欄位中，指定裝載複本的所有伺服器執行個體都有讀寫存取的備份共用。 記錄備份將是記錄備份鏈結的一部分。 請適當地儲存記錄備份檔案。  
  
        > [!IMPORTANT]  
        >  如需檔案系統必要權限的資訊，請參閱本主題稍早的 [必要條件](#Prerequisites)。  
  
    -   **僅聯結**  
  
         如果您已經在將裝載新次要複本的伺服器執行個體上手動備妥次要資料庫，就可以選取此選項。 然後精靈會將這些新的次要資料庫聯結至可用性群組。  
  
    -   **略過初始資料同步處理**  
  
         如果要使用您自己的主要資料庫的資料庫和記錄備份，請選取此選項。 如需詳細資訊，請參閱 [於 AlwaysOn 次要資料庫啟動資料移動 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/start-data-movement-on-an-always-on-secondary-database-sql-server.md)。  
  
8.  **[驗證]** 頁面會驗證您在此精靈中指定的值是否符合 [將複本加入至可用性群組] 精靈的需求。 若要進行變更，您可以按 **[上一步]** 返回先前的精靈頁面，以變更一個或多個值。 然後按 **[下一步]** 返回 **[驗證]** 頁面，再按一下 **[重新執行驗證]** 。  
  
9. 在 **[摘要]** 頁面上，檢閱您為新的可用性群組的選擇。 若要進行變更，請按 **[上一步]** 返回相關頁面。 進行變更之後，請按 **[下一步]** 返回 **[摘要]** 頁面。  
  
     如果您對所做的選擇感到滿意時，可以選擇按一下 [指令碼]，建立精靈將執行之步驟的指令碼。 然後，若要建立及設定新的可用性群組，請按一下 **[完成]** 。  
  
10. **[進度]** 頁面會顯示建立可用性群組之步驟的進度 (設定端點、建立可用性群組，並將次要複本加入群組中)。  
  
11. 當這些步驟完成時， **[結果]** 頁面會顯示每個步驟的結果。 如果所有這些步驟都成功，表示新的可用性群組已完成設定。 如果任何步驟導致錯誤，您可能需要手動完成設定。 如需有關給定錯誤原因的詳細資訊，請按一下 **[結果]** 資料行中相關聯的 [錯誤] 連結。  
  
     當精靈完成時，按一下 **[關閉]** 以結束。  
  
> [!IMPORTANT]  
>  新增複本之後，請參閱＜後續操作：新增複本之後＞一節，位於[將次要複本新增至可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/add-a-secondary-replica-to-an-availability-group-sql-server.md)中。  
  
##  <a name="related-tasks"></a><a name="RelatedTasks"></a> 相關工作  
  
-   [將次要複本加入至可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/add-a-secondary-replica-to-an-availability-group-sql-server.md)  
  
## <a name="see-also"></a>另請參閱  
 [AlwaysOn 可用性群組概觀 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server.md)   
 [AlwaysOn 可用性群組的必要條件、限制和建議 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/prereqs-restrictions-recommendations-always-on-availability.md)   
 [將次要複本加入至可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/add-a-secondary-replica-to-an-availability-group-sql-server.md)  
  
  
