---
title: 將 Azure VM 設定為可用性群組中的次要複本
description: 使用 [新增 Azure 複本精靈] 以在混合式 IT 中建立新的 Azure VM，且將其設為全新或現有 Always On 可用性群組的次要複本。
ms.custom: seo-lt-2019
ms.date: 05/17/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: conceptual
f1_keywords:
- sql13.swb.addreplicawizard.azurereplica.f1
ms.assetid: b89cc41b-07b4-49f3-82cc-bc42b2e793ae
author: cawrites
ms.author: chadam
ms.openlocfilehash: 7fb9c44c90dbedbdc0a123f4b8c09088f7392d50
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97641795"
---
# <a name="configure-azure-vm-as-a-secondary-replica-in-an-availability-group"></a>將 Azure VM 設定為可用性群組中的次要複本
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]
  使用 [新增 Azure 複本精靈] 可以協助您在混合式 IT 中建立新的 Azure VM，並且將它設定為全新或現有 AlwaysOn 可用性群組的次要複本。  

>  [!IMPORTANT]  
>  Azure 建立和處理資源的部署模型有二種：資源管理員和傳統。 本文涵蓋之內容包括使用傳統部署模型。 Microsoft 建議讓大部分的新部署使用 Resource Manager 模式。 如果您是使用 Resource Manager 模型來部署 Azure 虛擬機器，此文章中的步驟便不適用。   

##  <a name="before-you-begin"></a><a name="BeforeYouBegin"></a> 開始之前  
 如果您從未將任何可用性複本加入可用性群組中，請參閱 [AlwaysOn 可用性群組的必要條件、限制和建議 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/prereqs-restrictions-recommendations-always-on-availability.md)中的＜伺服器執行個體＞和＜可用性群組和複本＞二節。  
  
##  <a name="prerequisites"></a><a name="Prerequisites"></a> 必要條件  
  
-   您必須連接到裝載目前主要複本的伺服器執行個體。  
  
-   您必須擁有混合式 IT 環境，其中的內部部署子網路具有 Azure 的站對站 VPN。 如需詳細資訊，請參閱 [使用 Azure 傳統入口網站建立具有站對站 VPN 連線的虛擬網路](/azure/vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal)。  
  
-   您的可用性群組必須包含內部部署可用性複本。  
  
-   如果可用性群組接聽程式的用戶端想要在可用性群組容錯移轉至 Azure 複本時維持接聽程式的連線能力，它們就必須具有網際網路的連線能力。  
  
-   **使用完整初始資料同步處理的必要條件** ：您需要指定網路共用，才能讓精靈建立及存取備份。 對於主要複本，用於啟動 [!INCLUDE[ssDE](../../../includes/ssde-md.md)] 的帳戶必須具有網路共用的讀取與寫入檔案系統權限。 如果是次要複本，此帳戶必須有網路共用的讀取權限。  
  
     如果您無法使用精靈執行完整初始資料同步處理，則必須手動準備次要資料庫。 您可以在執行精靈前後進行這項作業。 如需詳細資訊，請參閱 [針對可用性群組手動準備次要資料庫 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/manually-prepare-a-secondary-database-for-an-availability-group-sql-server.md)中的 PowerShell，將次要資料庫聯結至 AlwaysOn 可用性群組。  
  
##  <a name="permissions"></a><a name="Permissions"></a> 權限  
 需要可用性群組的 ALTER AVAILABILITY GROUP 權限、CONTROL AVAILABILITY GROUP 權限、ALTER ANY AVAILABILITY GROUP 權限或 CONTROL SERVER 權限。  
  
 如果您想要允許 [將複本加入至可用性群組精靈] 管理資料庫鏡像端點，也需要 CONTROL ON ENDPOINT 權限。  
  
##  <a name="using-the-add-azure-replica-wizard-sql-server-management-studio"></a><a name="SSMSProcedure"></a> 使用加入 Azure 複本精靈 (SQL Server Management Studio)  

>  [!IMPORTANT]  
>  [加入 Azure 複本精靈] 僅支援以傳統部署模型建立的虛擬機器。 新的虛擬機器部署應該使用較新的 Resource Manager 模型。 如果您是搭配 Resource Manager 使用虛擬機器，您應該使用 Transact-SQL 命令 (未顯示於此) 手動新增次要 Azure 複本。 此精靈並無法在 Resource Manager 的案例中運作。 
>
>  SQL Server Management Studio 的最新版本 (18.x 與 17.x 版) 中並未提供 [加入 Azure 複本] 精靈。
        
 您可以從 [指定複本頁面](../../../database-engine/availability-groups/windows/specify-replicas-page-new-availability-group-wizard-add-replica-wizard.md)啟動 [加入 Azure 複本精靈]。 存取這個頁面的方式有兩種：  
  
-   [使用可用性群組精靈 &#40;SQL Server Management Studio&#41;](../../../database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio.md)  
  
-   [使用 [將複本加入可用性群組中精靈] &#40;SQL Server Management Studio&#41;](../../../database-engine/availability-groups/windows/use-the-add-replica-to-availability-group-wizard-sql-server-management-studio.md)  
  
 一旦您啟動 [加入 Azure 複本精靈] 之後，請遵循下列步驟進行：  
  
1.  首先，下載您的 Azure 訂用帳戶的管理憑證。 按一下 [下載] 開啟登入頁面。  
  
2.  使用您的 Microsoft 帳戶或您的組織帳戶登入 Microsoft Azure。 您的 Microsoft 或組織帳戶是電子郵件地址格式，例如 HYPERLINK "mailto:patc@contoso.com" patc@contoso.com。 如需 Azure 認證的詳細資訊，請參閱 [Microsoft Account for Organizations FAQ](/previous-versions/jj592903(v=msdn.10)) (組織的 Microsoft 帳戶常見問題集) 和 [疑難排解使用您的組織帳戶登入的問題](https://support.microsoft.com/kb/2756852)。  
  
3.  接著，按一下 **[連接]** ，連接到您的訂用帳戶。 一旦您連線之後，下拉式清單就會填入您的 Azure 參數，例如 [虛擬網路] 和 [虛擬網路子網路]。  
  
4.  針對將裝載新次要複本的 Azure VM 指定設定：  
  
     映像  
     要用於 Azure VM 的 SQL Server 映像名稱  
  
     VM 大小  
     Azure VM 的大小  
  
     虛擬機器名稱  
     Azure VM 的 DNS 名稱  
  
     VM 使用者名稱  
     Azure VM 預設系統管理員的使用者名稱  
  
     VM 系統管理員密碼 (和確認密碼)  
     Azure VM 預設系統管理員的密碼  
  
     虛擬網路  
     要放置 Azure VM 的虛擬網路  
  
     虛擬網路子網路  
     要放置 Azure VM 的虛擬網路子網路  
  
     網域  
     要加入 Azure VM 的 Active Directory (AD) 網域  
  
     網域使用者名稱  
     用來將 Azure VM 加入至網域的 AD 使用者名稱  
  
     密碼  
     用來將 Azure VM 加入至網域的密碼  
  
5.  按一下 **[確定]** 認可您的設定並結束 [加入 Azure 複本精靈]。  
  
6.  繼續針對 [指定複本頁面](../../../database-engine/availability-groups/windows/specify-replicas-page-new-availability-group-wizard-add-replica-wizard.md) 進行其餘組態設定步驟，就像設定任何新的複本一樣。  
  
     一旦您完成 [可用性群組精靈] 或 [將複本加入至可用性群組精靈] 之後，設定程序會在 Azure 中執行所有作業，以建立新的 VM、將它加入至 AD 網域、將它加入至 Windows 叢集、啟用 AlwaysOn 高可用性，並且將新的複本加入至可用性群組。  
  
##  <a name="related-tasks"></a><a name="RelatedTasks"></a> 相關工作  
  
-   [將次要複本加入至可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/add-a-secondary-replica-to-an-availability-group-sql-server.md)  
  
## <a name="see-also"></a>另請參閱  
 [AlwaysOn 可用性群組概觀 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server.md)   
 [AlwaysOn 可用性群組的必要條件、限制和建議 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/prereqs-restrictions-recommendations-always-on-availability.md)   
 [將次要複本加入至可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/add-a-secondary-replica-to-an-availability-group-sql-server.md)  
  
