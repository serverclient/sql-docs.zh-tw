---
title: 可用性群組精靈：[指定複本] 頁面
description: 描述 SQL Server Management Studio (SSMS) 中 [新增可用性群組精靈] 的 [指定複本] 頁面選項。
ms.custom: seo-lt-2019
ms.date: 05/17/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: conceptual
f1_keywords:
- sql13.swb.newagwizard.listeners.f1
- sql13.swb.addreplicawizard.specifyreplicas.f1
- sql13.swb.newagwizard.specifyreplicas.f1
ms.assetid: 2d90fc12-a67b-4bd0-b0ab-899b73017196
author: cawrites
ms.author: chadam
ms.openlocfilehash: fc0fd2d74ceea77b0c577f7bf3315695385274eb
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97639176"
---
# <a name="specify-replicas-page-new-availability-group-wizard-add-replica-wizard"></a>指定複本頁面 (新增可用性群組精靈：新增複本精靈)
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]
  此主題描述 **[指定複本]** 頁面的選項。 此頁面適用於 **[!INCLUDE[ssAoNewAgWiz](../../../includes/ssaonewagwiz-md.md)]** 和 **[!INCLUDE[ssAoAddRepWiz](../../../includes/ssaoaddrepwiz-md.md)]** 。 使用 **[指定複本]** 頁面指定與設定要加入可用性群組的一個或多個可用性複本。 此頁面包含四個索引標籤，其說明如下表所示。 按一下表格中的索引標籤名稱即可前往對應的章節，如本主題稍後所示。  
  
|索引標籤|簡短描述|  
|---------|-----------------------|  
|[複本](#ReplicasTab)|使用此索引標籤可指定將裝載或目前裝載次要複本的每個 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體。 請注意，您目前所連接的伺服器執行個體必須裝載主要複本。<br /><br /> 在 **[複本]** 索引標籤上完成指定所有複本，然後啟動其他索引標籤。<br/><br/> 請注意，如果叢集類型是 [NONE]，則會停用 [自動容錯移轉]。 只有在可用性群組不在叢集時，SQL Server 才支援手動容錯移轉。 <br/><br/> 叢集類型是 EXTERNAL 時，容錯移轉模式是 [外部]。 <br/><br/> 當您要新增複本時，所有新的複本都必須裝載在與現有複本相同的作業系統類型上。 <br/><br/>新增複本時，如果主要複本是在 WSFC 上，則次要複本必須位於相同的叢集中。|
|[端點](#EndpointsTab)|使用此索引標籤可驗證任何現有的資料庫鏡像端點，此外，如果在其服務帳戶使用 Windows 驗證的伺服器執行個體上缺少此端點，則可自動建立該端點。|  
|[備份喜好設定](#BackupPreferencesTab)|使用此索引標籤可指定整個可用性群組的備份喜好設定，以及個別可用性複本的備份優先權。|  
|[接聽程式](#Listener)|使用此索引標籤 (如果有的話) 建立可用性群組接聽程式。 預設不會建立接聽程式。<br /><br /> 此索引標籤只有在您執行 [!INCLUDE[ssAoNewAgWiz](../../../includes/ssaonewagwiz-md.md)]時才可以使用。<br/><br/>叢集類型是 EXTERNAL 或 NONE 時，會停用 DHCP。 |  
  
##  <a name="replicas-tab"></a><a name="ReplicasTab"></a> 複本索引標籤  
 **伺服器執行個體**  
 顯示將裝載可用性複本的伺服器執行個體名稱。  
  
 如果 [可用性複本] 方格未列出您要用來裝載次要複本的伺服器執行個體，請按一下 [新增複本] 按鈕。 如果您在混合式 IT 環境設定可用性群組 (請參閱 [SQL Server 在 Azure 虛擬機器中的高可用性和災害復原](/previous-versions/azure/jj870962(v=azure.100)))，您可以按一下 [加入 Azure 複本] 按鈕，以在 Azure 中建立包含次要複本的虛擬機器。  
  
 **初始角色**  
 指出新複本一開始執行的角色：[主要] 或 [次要]。  
  
 **自動容錯移轉 (最多 3 個)**  
 只有在您希望此可用性複本成為自動容錯移轉夥伴時，才選取這個核取方塊。 若要設定自動容錯移轉，您必須為初始主要複本和一個次要複本選擇這個選項。 這兩個複本都會使用同步認可可用性模式。 只有三個複本可以支援自動容錯移轉。  
  
 如需同步認可可用性模式的相關資訊，請參閱[可用性模式 &#40;AlwaysOn 可用性群組&#41;](../../../database-engine/availability-groups/windows/availability-modes-always-on-availability-groups.md)。 如需自動容錯移轉的相關資訊，請參閱[容錯移轉及容錯移轉模式 &#40;AlwaysOn 可用性群組&#41;](../../../database-engine/availability-groups/windows/failover-and-failover-modes-always-on-availability-groups.md)。  
  
 **同步認可 (最多 3 個)**  
 如果您為複本選取 [自動容錯移轉 (最多 3 個)]，也會選取 [同步認可 (最多 3 個)]。 如果核取方塊是空的，只有在您希望此複本僅搭配已規劃的手動容錯移轉使用同步認可模式時，才選取該核取方塊。 只有三個複本可以使用同步認可模式。  
  
 如果您希望此複本使用非同步認可可用性模式，將此核取方塊留空。 此複本僅支援強制手動容錯移轉 (資料可能會遺失)。 如需非同步認可可用性模式的相關資訊，請參閱[可用性模式 &#40;AlwaysOn 可用性群組&#41;](../../../database-engine/availability-groups/windows/availability-modes-always-on-availability-groups.md)。 如需已規劃手動容錯移轉和強制手動容錯移轉的相關資訊，請參閱[容錯移轉及容錯移轉模式 &#40;AlwaysOn 可用性群組&#41;](../../../database-engine/availability-groups/windows/failover-and-failover-modes-always-on-availability-groups.md)。  
  
 **可讀取的次要角色**  
 從 [可讀取次要] 下拉清單中選取一個值，如下所示：  
  
 **否**  
 不允許直接連接這個複本的次要資料庫。 無法讀取這些資料庫。 這是預設值。  
  
 **僅限讀取意圖**  
 只允許與這個複本的次要資料庫進行直接唯讀連接。 可以讀取所有次要資料庫。  
  
 **是**  
 允許與這個複本的次要資料庫之間的所有連接，但只供讀取存取。 可以讀取所有次要資料庫。  
  
 **[加入複本]**  
 按一下可將次要複本加入至可用性群組。  
  
 **加入 Azure 複本**  
 按一下以建立在可用性群組中執行次要複本的 Azure 虛擬機器。 此選項只適用於混合式 IT 中包含內部部署複本的可用性群組。 如需詳細資訊，請參閱 [SQL Server 在 Azure 虛擬機器中的高可用性和災害復原](/previous-versions/azure/jj870962(v=azure.100))。  
  
 **移除複本**  
 按一下可從可用性群組中移除選取的次要複本。  
  
##  <a name="endpoints-tab"></a><a name="EndpointsTab"></a> 端點索引標籤  
 對於裝載可用性複本的每個伺服器執行個體， **[端點]** 索引標籤會顯示現有資料庫鏡像端點的實際值 (如果有的話)，或是將會使用 Windows 驗證的可能新端點的建議值。 對於現有和可能的端點而言，[端點值] 方格會顯示下列資訊：  
  
 **伺服器名稱**  
 顯示將裝載可用性複本之伺服器執行個體的名稱。  
  
 **端點 URL**  
 顯示資料庫鏡像端點的實際或建議 URL。 若是建議的新端點，您可以變更此值。 如需這些 URL 格式的資訊，請參閱[在加入或修改可用性複本時指定端點 URL &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/specify-endpoint-url-adding-or-modifying-availability-replica.md)。  
  
 **通訊埠編號**  
 顯示端點的實際或建議通訊埠編號。 若是建議的新端點，您可以變更此值。  
  
 **端點名稱**  
 顯示端點的實際或建議名稱。 若是建議的新端點，您可以變更此值。  
  
 **加密資料**  
 指示是否加密透過此端點傳送的資料。 若是建議的新端點，您可以變更此設定。  
  
 **SQL Server 服務帳戶**  
 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 服務帳戶的使用者名稱。  
  
 如果伺服器執行個體所使用的端點會使用 Windows 驗證，其 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 服務帳戶必須是網域帳戶。  
  
 這項需求會決定您的下一個組態步驟，如下所示：  
  
-   如果每個伺服器執行個體都在網域服務帳戶之下執行，也就是說，如果 **[SQL Server 服務帳戶]** 欄會顯示每一個伺服器執行個體的網域服務帳戶，請按 **[下一步]** 。  
  
-   如果有任何伺服器執行個體在非網域服務帳戶之下執行，您需要對伺服器執行個體進行手動變更，然後才可以在精靈中繼續執行。 在此情況下，按 **[下一步]** 將會出現警告對話方塊；您應該按一下 **[否]** ，這會讓您回到 **[端點]** 索引標籤。當您在 **[指定複本]** 頁面上離開精靈時，請針對 **[SQL Server 服務帳戶]** 欄顯示非網域服務帳戶的每一個伺服器執行個體進行下列其中一項變更：  
  
    -   使用 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 組態管理員將 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 服務帳戶變更為網域帳戶。 如需詳細資訊，請參閱[變更 SQL Server 的服務啟動帳戶 &#40;SQL Server 組態管理員&#41;](../../../database-engine/configure-windows/scm-services-change-the-service-startup-account.md)。  
  
    -   使用 [!INCLUDE[tsql](../../../includes/tsql-md.md)] 或 PowerShell 手動建立使用憑證的資料庫鏡像端點。 如需詳細資訊，請參閱＜ [CREATE ENDPOINT &#40;Transact-SQL&#41;](../../../t-sql/statements/create-endpoint-transact-sql.md) 或 [針對 AlwaysOn 可用性群組建立資料庫鏡像端點 &#40;SQL Server PowerShell&#41;](../../../database-engine/availability-groups/windows/database-mirroring-always-on-availability-groups-powershell.md)。  
  
     如果您在設定端點時，將 **[指定可用性複本]** 頁面維持在開啟狀態，請返回 **[端點]** 索引標籤，然後按一下 **[重新整理]** 來更新 **[端點值]** 方格。  
  
##  <a name="backup-preferences-tab"></a><a name="BackupPreferencesTab"></a> 備份喜好設定索引標籤  
 若要指定備份位置，請選擇下列其中一個選項：  
  
 **慣用次要**  
 指定應該在次要複本上進行備份，但是主要複本是唯一線上複本的情況例外。 在此情況下，應該在主要複本上進行備份。 這是預設選項。  
  
 **僅次要**  
 指定絕對不能在主要複本上執行備份。 如果主要複本是唯一的線上複本，不應該進行備份。  
  
 **主要**  
 指定備份一定要在主要複本上進行。 如果當您在次要複本上執行備份時，需要不支援的備份功能 (例如建立差異備份)，這個選項會很實用。  
  
 **任何複本**  
 指定當您選擇要執行備份的複本時，您希望備份作業忽略可用性複本的角色。 請注意，備份作業可能會評估其他因素，例如每個可用性複本的備份優先權，搭配其操作狀態和連接狀態。  
  
> [!IMPORTANT]  
>  不會強制執行備份喜好設定。 這個喜好設定的解譯取決於您在給定可用性群組之資料庫的備份作業中所編寫的邏輯 (如果有的話)。 如需詳細資訊，請參閱[使用中次要：在次要複本上備份 &#40;Always On 可用性群組&#41;](../../../database-engine/availability-groups/windows/active-secondaries-backup-on-secondary-replicas-always-on-availability-groups.md)。  
  
### <a name="replica-backup-priorities-grid"></a>複本備份優先權方格  
 使用 **[複本備份優先權]** 方格指定可用性群組每個複本的備份優先權。 此方格包含下列資料行：  
  
 **伺服器執行個體**  
 顯示裝載可用性複本之 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體的名稱。  
  
 **備份優先權 (最低 = 1，最高 = 100)**  
 指派在這個複本上執行備份的優先權 (相對於相同可用性群組中的其他複本)。 預設值是 50。 您可以在 0..100 的範圍中選取其他任何整數。 1 表示最低優先權，100 表示最高優先權。 如果您將 [備份優先權] 設定為 1，則只有當目前沒有更高優先權的可用性複本可用時，才會選擇此可用性複本來執行備份。  
  
 **排除複本**  
 避免選擇這個可用性複本來執行備份作業。 例如，這對於您永遠不希望將備份容錯移轉到其中的遠端可用性複本十分有用。  
  
##  <a name="listener-tab"></a><a name="Listener"></a> 接聽程式索引標籤  
 指定[可用性群組接聽程式](../../../database-engine/availability-groups/windows/listeners-client-connectivity-application-failover.md)的喜好設定，此接聽程式將會提供用戶端連接點，如下列的其中一項：  
  
 **現在不要建立可用性群組接聽程式。**  
 選擇略過此步驟。 您稍後可以建立一個接聽程式。 如需詳細資訊，請參閱 [建立或設定可用性群組接聽程式 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/create-or-configure-an-availability-group-listener-sql-server.md)。  
  
 **建立可用性群組接聽程式。**  
 指定此可用性群組的接聽程式喜好設定，如下所示：  
  
 **接聽程式 DNS 名稱**  
 指定接聽程式的網路名稱。 此名稱在網域上必須是唯一的，而且僅能包含英數字元、虛線 ( **-** ) 和連字號 ( **_** ) (順序不拘)。 使用 **[接聽程式]** 索引標籤指定時，DNS 名稱長度最多可以有 15 個字元。  
  
> [!IMPORTANT]  
>  如果您在 [接聽程式] 索引標籤上輸入無效的 DNS 接聽程式名稱 (或通訊埠編號)，[指定複本] 頁面上的 [下一步] 按鈕便會停用。  
  
 **通訊埠**  
 指定此接聽程式所使用的 TPC 通訊埠。  
  
> [!NOTE]  
>  如果您在 [接聽程式] 索引標籤上輸入無效的通訊埠編號 (或DNS 接聽程式名稱)，[指定複本] 頁面上的 [下一步] 按鈕便會停用。  
  
 **網路模式**  
 使用下拉清單選取此接聽程式要使用的網路模式，如下列的其中一項：  
  
 **靜態 IP**  
 如果您希望接聽程式接聽多個子網路，請選擇此選項。 若要使用靜態 IP 網路模式，可用性群組接聽程式必須接聽裝載可用性群組之可用性複本的每個子網路。 針對每個子網路，按一下 **[加入]** 來選取子網路位址以及指定 IP 位址。  
  
 如果將 [靜態 IP] 選取為網路模式 (這是預設選項)，方格會顯示 [子網路] 和 [IP 位址] 資料行，並顯示相關聯的 [新增] 和 [移除] 按鈕。 在您加入第一個子網路之前，此方格是空的。  
  
 [子網路] 資料行  
 為您針對接聽程式加入的每個子網路，顯示您所選取的子網路位址。  
  
 [IP 位址] 資料行  
 顯示您針對給定子網路指定的 IPv4 或 IPv6 位址。  
  
 **加入**  
 按一下可將子網路加入至此接聽程式。 這會開啟 **[加入 IP 位址]** 對話方塊。 如需詳細資訊，請參閱[加入 IP 位址對話方塊 &#40;SQL Server Management Studio&#41;](../../../database-engine/availability-groups/windows/add-ip-address-dialog-box-sql-server-management-studio.md) 說明主題。  
  
 **移除**  
 按一下可移除目前在方格中選取的子網路。  
  
 **DHCP**  
 如果您希望接聽程式在單一子網路上接聽，並使用執行動態主機設定通訊協定 (DHCP) 之伺服器所指派的動態 IPv4 位址，請選取此選項。 DHCP 受限於單一子網路，這個子網路對於裝載可用性群組之可用性複本的每個伺服器執行個體而言，是通用的。 叢集類型 external 或 none 無法使用 DHCP。  
  
> [!IMPORTANT]  
>  不建議在實際執行環境中使用 DHCP。 如果有停機時間且 DHCP IP 租用到期，則需要額外的時間來註冊與接聽程式 DNS 名稱相關聯的新 DHCP 網路 IP 位址，而影響用戶端連接。 但是，DHCP 適合用於設定開發和測試環境，以驗證可用性群組的基本功能，也適合與應用程式整合。  
  
 選取 **[DHCP]** 時，會顯示 **[子網路]** 欄位。  
  
 **子網路**  
 如果您選取 [DHCP] 作為網路模式，請使用 [子網路] 下拉清單選取裝載可用性群組之可用性複本的子網路位址。  
  
> [!IMPORTANT]
>  在您定義可用性群組接聽程式之後，我們強烈建議您執行以下操作：  
> 
>  -   要求網路管理員將接聽程式的 IP 位址保留為專用。 將接聽程式的 DNS 主機名稱提供給應用程式開發人員，以便在要求與這個可用性群組進行用戶端連接時，用於連接字串中。  
> -   將接聽程式的 DNS 主機名稱提供給應用程式開發人員，以便在要求與這個可用性群組進行用戶端連接時，用於連接字串中。  
  
##  <a name="related-tasks"></a><a name="RelatedTasks"></a> 相關工作  
  
-   [使用可用性群組精靈 &#40;SQL Server Management Studio&#41;](../../../database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio.md)  
  
-   [使用 [將複本加入可用性群組中精靈] &#40;SQL Server Management Studio&#41;](../../../database-engine/availability-groups/windows/use-the-add-replica-to-availability-group-wizard-sql-server-management-studio.md)  
  
-   [建立或設定可用性群組接聽程式 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/create-or-configure-an-availability-group-listener-sql-server.md)  
  
-   [使用資料庫鏡像端點憑證 &#40;Transact-SQL&#41;](../../../database-engine/database-mirroring/use-certificates-for-a-database-mirroring-endpoint-transact-sql.md)  
  
-   [CREATE ENDPOINT &#40;Transact-SQL&#41;](../../../t-sql/statements/create-endpoint-transact-sql.md)  
  
-   [針對 AlwaysOn 可用性群組建立資料庫鏡像端點 &#40;SQL Server PowerShell&#41;](../../../database-engine/availability-groups/windows/database-mirroring-always-on-availability-groups-powershell.md)  
  
## <a name="see-also"></a>另請參閱  
 [AlwaysOn 可用性群組概觀 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server.md)   
 [CREATE AVAILABILITY GROUP &#40;Transact-SQL&#41;](../../../t-sql/statements/create-availability-group-transact-sql.md)   
 [AlwaysOn 可用性群組的必要條件、限制和建議 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/prereqs-restrictions-recommendations-always-on-availability.md)  
  
