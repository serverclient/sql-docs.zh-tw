---
title: Analysis Services 與可用性群組
description: 如果使用 Always On 可用性群組作為高可用性解決方案，則可以使用該群組中的資料庫作為 Analysis Services 表格式或多維度解決方案的資料來源。
ms.custom: seodec18
ms.date: 05/17/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: how-to
ms.assetid: 14d16bfd-228c-4870-b463-a283facda965
author: cawrites
ms.author: chadam
manager: erikre
monikerRange: '>=sql-server-2016'
ms.openlocfilehash: c7d64fdbc557f5b985d27818847091f7eea1816f
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97641652"
---
# <a name="analysis-services-with-always-on-availability-groups"></a>Analysis Services 與 AlwaysOn 可用性群組

[!INCLUDE[sql windows only](../../../includes/applies-to-version/sql-windows-only.md)]

  AlwaysOn 可用性群組是預先定義的 SQL Server 關聯式資料庫集合，會在條件觸發任何一個資料庫的容錯移轉時一起容錯移轉，將要求重新導向至相同可用性群組中其他執行個體上的鏡像資料庫。 如果使用可用性群組做為高可用性解決方案，則可以使用該群組中的資料庫做為 Analysis Services 表格式或多維度解決方案的資料來源。 使用可用性資料庫時，下列所有 Analysis Services 作業都會如預期般運作：處理或匯入資料、直接查詢關聯式資料 (使用 ROLAP 儲存或 DirectQuery 模式)，以及回寫。  
  
 處理及查詢是唯讀工作負載。 您可以將這些工作負載卸載至可讀取的次要複本，以提升效能。 這個案例不需要進行其他組態設定。 使用本主題中的檢查清單，以確保您遵循所有步驟。  
  
##  <a name="prerequisites"></a><a name="bkmk_prereq"></a> 必要條件  
 您必須擁有所有複本的 SQL Server 登入。 您必須是 **系統管理員** ，才能設定可用性群組、接聽程式及資料庫，但是使用者只需要 **db_datareader** 權限，即可從 Analysis Services 用戶端存取資料庫。  
  
 使用支援表格式資料流 (TDS) 通訊協定 7.4 版或更新版本的資料提供者，例如 SQL Server Native Client 11.0 或 .NET Framework 4.02 的 Data Provider for SQL Server。  
  
 **(適用於唯讀工作負載)** 。 您必須設定唯讀連接的次要複本角色，可用性群組必須具有路由清單，且 Analysis Services 資料來源中的連接必須指定可用性群組接聽程式。 本主題將提供指示。  
  
##  <a name="checklist-use-a-secondary-replica-for-read-only-operations"></a><a name="bkmk_UseSecondary"></a> 檢查清單：僅針對唯讀作業使用次要複本  
 除非 Analysis Services 解決方案包含回寫，否則您可以設定資料來源連接使用可讀取的次要複本。 如果具有快速網路連接，次要複本的資料延遲會很低，因此提供與主要複本幾乎相同的資料。 透過使用次要複本進行 Analysis Services 作業，您可以降低主要複本的讀寫競爭，並更善用可用性群組中的次要複本。  
  
 預設允許與主要複本之間的讀寫和讀取意圖的存取，但是不允許連接次要複本。 次要複本的唯讀用戶端連接需要進行其他組態設定。 這些組態設定需要設定次要複本的屬性，以及執行定義唯讀路由清單的 T-SQL 指令碼。 使用下列程序，以確保您已經執行這兩個步驟。  
  
> [!NOTE]  
>  下列步驟假設存在 AlwaysOn 可用性群組和資料庫。 如果您想設定新群組，請使用 [新增可用性群組精靈] 建立群組並加入資料庫。 這個精靈會檢查先決條件、提供每個步驟的指引，並執行初始同步處理。 如需詳細資訊，請參閱[使用可用性群組精靈 &#40;SQL Server Management Studio&#41;](../../../database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio.md)。  
  
#### <a name="step-1-configure-access-on-an-availability-replica"></a>步驟 1：設定可用性複本的存取  
  
1.  在 [物件總管] 中，連接到裝載主要複本的伺服器執行個體，然後展開伺服器樹狀目錄。  
  
    > [!NOTE]  
    >  這些步驟摘錄自[設定可用性複本上的唯讀存取 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/configure-read-only-access-on-an-availability-replica-sql-server.md)，文中提供執行這項工作的其他資訊和替代指示。  
  
2.  依序展開 [Always On 高可用性]  節點和 [可用性群組]  節點。  
  
3.  按一下要變更複本的可用性群組。 展開 **[可用性複本]** 。  
  
4.  以滑鼠右鍵按一下次要複本，然後按一下 [屬性]  。  
  
5.  在 **[可用性複本屬性]** 對話方塊中，變更次要角色的連接存取，如下所示：  
  
    -   在 [可讀取次要]  下拉式清單中，選取 [僅限讀取意圖]  。  
  
    -   在 **[主要角色的連接]** 下拉式清單中，選取 **[允許所有連接]** 。 這是預設值。  
  
    -   或者，在 **[可用性模式]** 下拉式清單中，選取 **[同步認可]** 。 不需要這個步驟，但是進行設定可確保主要與次要複本之間的資料同位。  
  
         規劃的容錯移轉也需要這個屬性。 如果您要執行規劃的手動容錯移轉以進行測試，請將主要與次要複本的 **[可用性模式]** 同時設為 **[同步認可]** 。  
  
#### <a name="step-2-configure-read-only-routing"></a>步驟 2：設定唯讀路由  
  
1.  連接到主要複本。  
  
    > [!NOTE]  
    >  這些步驟摘錄自[設定可用性群組的唯讀路由 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/configure-read-only-routing-for-an-availability-group-sql-server.md)，文中提供執行這項工作的其他資訊和替代指示。  
  
2.  開啟查詢視窗並貼上下列指令碼。 這個指令碼會執行三個動作：啟用次要複本的可讀取連接 (預設為關閉)、設定唯讀路由 URL，以及建立路由清單以排定導向連接要求的優先順序。  如果您已經在 [!INCLUDE[ssManStudio](../../../includes/ssmanstudio-md.md)]中設定屬性，但是為求完整性已併入屬性，則第一個陳述式 (允許可讀取連接) 為多餘的陳述式。  
  
    ```  
    ALTER AVAILABILITY GROUP [AG1]  
     MODIFY REPLICA ON  
    N'COMPUTER01' WITH   
    (SECONDARY_ROLE (ALLOW_CONNECTIONS = READ_ONLY));  
  
    ALTER AVAILABILITY GROUP [AG1]  
     MODIFY REPLICA ON  
    N'COMPUTER01' WITH   
    (SECONDARY_ROLE (READ_ONLY_ROUTING_URL = N'TCP://COMPUTER01.contoso.com:1433'));  
  
    ALTER AVAILABILITY GROUP [AG1]  
     MODIFY REPLICA ON  
    N'COMPUTER02' WITH   
    (SECONDARY_ROLE (ALLOW_CONNECTIONS = READ_ONLY));  
  
    ALTER AVAILABILITY GROUP [AG1]  
     MODIFY REPLICA ON  
    N'COMPUTER02' WITH   
    (SECONDARY_ROLE (READ_ONLY_ROUTING_URL = N'TCP://COMPUTER02.contoso.com:1433'));  
  
    ALTER AVAILABILITY GROUP [AG1]   
    MODIFY REPLICA ON  
    N'COMPUTER01' WITH   
    (PRIMARY_ROLE (READ_ONLY_ROUTING_LIST=('COMPUTER02','COMPUTER01')));  
  
    ALTER AVAILABILITY GROUP [AG1]   
    MODIFY REPLICA ON  
    N'COMPUTER02' WITH   
    (PRIMARY_ROLE (READ_ONLY_ROUTING_LIST=('COMPUTER01','COMPUTER02')));  
    GO  
    ```  
  
3.  修改指令碼，以對您的部署有效的值來取代預留位置：  
  
    -   以裝載主要複本之伺服器執行個體的名稱取代 'Computer01'。  
  
    -   以裝載次要複本之伺服器執行個體的名稱取代 'Computer02'。  
  
    -   以您的網域名稱取代 'contoso.com'，如果所有電腦都在相同的網域，則可以從指令碼加以省略。 如果接聽程式使用預設通訊埠，請保留通訊埠編號。 接聽程式實際使用的通訊埠會列於 [!INCLUDE[ssManStudio](../../../includes/ssmanstudio-md.md)]的屬性頁面中。  
  
4.  執行指令碼。  
  
     下一步，建立 Analysis Services 模型中的資料來源，這個資料來源會使用您剛才設定之群組中的資料庫。  
  
##  <a name="create-an-analysis-services-data-source-using-an-always-on-availability-database"></a><a name="bkmk_ssasAODB"></a> 建立使用 AlwaysOn 可用性資料庫的 Analysis Services 資料來源  
 本節說明如何建立連接至可用性群組中某個資料庫的 Analysis Services 資料來源。 您可以使用這些說明，設定主要複本 (預設) 或您根據上一節步驟所設定之可讀取次要複本的連接。 AlwaysOn 組態設定及在用戶端設定的連線屬性，決定使用主要或次要複本。  
  
1.  在 [!INCLUDE[ssBIDevStudio](../../../includes/ssbidevstudio-md.md)] 的 Analysis Services 多維度和資料採礦模型專案中，以滑鼠右鍵按一下 [資料來源]  ，然後選取 [新增資料來源]  。 按一下 **[新增]** 建立新的資料來源。  
  
     或者，針對表格式模型專案，按一下 [模型] 功能表，然後按一下 **[從資料來源匯入]** 。  
  
2.  在 [連接管理員] 的 [提供者] 中，選擇支援表格式資料流 (TDS) 通訊協定的提供者。 SQL Server Native Client 11.0 支援這個通訊協定。  
  
3.  在 [連接管理員] 的 [伺服器名稱] 中，輸入 *「可用性群組接聽程式」* (Availability Group Listener) 的名稱，然後選擇群組中可用的資料庫。  
  
     若是讀寫要求，可用性群組接聽程式會將用戶端連接重新導向至主要複本，如果在連接字串中指定讀取意圖，則會重新導向至次要複本。 由於複本角色在容錯移轉期間會變更 (主要變成次要，而次要變成主要)，因此請務必指定接聽程式據以重新導向用戶端連接。  
  
     若要確定可用性群組接聽程式的名稱，您可以詢問資料庫管理員，或連接至可用性群組中的執行個體並檢視其 AlwaysOn 可用性組態。   
  
4.  同樣在 [連接管理員] 中，按一下左邊功能窗格中的 **[全部]** ，以檢視資料提供者的屬性方格。  
  
     如果將唯讀用戶端連線設定為次要複本，請將 [應用程式的意圖]  設為 [READONLY]  。 否則，請保留 **[READWRITE]** 預設值，將連接重新導向至主要複本。  
  
5.  在 [模擬資訊] 中，選取 [使用特定的 Windows 使用者名稱和密碼]  ，然後輸入至少具有資料庫之 **db_datareader** 權限的 Windows 網域使用者帳戶。  
  
     請勿選擇 **[使用目前使用者的認證]** 或 **[繼承]** 。 您可以選擇 **[使用服務帳戶]** ，但是該帳戶必須具有資料庫的讀取權限。  
  
     完成資料來源並關閉 [資料來源精靈]。  
  
6.  將 **MultiSubnetFailover=Yes** 加入連接字串，以提供更快的使用中伺服器偵測及連線。 如需有關這個屬性的詳細資訊，請參閱＜ [高可用性/災害復原的 SQL Server Native Client 支援](../../../relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery.md)＞。  
  
     這個屬性不會在屬性方格中顯示。 若要新增屬性，請以滑鼠右鍵按一下資料來源，然後選擇 [檢視程式碼]  。 將 `MultiSubnetFailover=Yes` 新增至連接字串。  
  
 現在您已經定義資料來源。 您可以接著繼續建立模型，請從資料來源檢視開始，或在表格式模型中建立關聯性。 如果您必須從可用性資料庫擷取資料 (例如在您準備處理或部署解決方案時)，您可以測試組態，確認可從次要複本存取資料。  
  
##  <a name="test-the-configuration"></a><a name="bkmk_test"></a> 測試組態  
 在您設定次要複本並在 Analysis Services 中建立資料來源連接之後，即可確認處理及查詢命令是否已重新導向至次要複本。 您也可以執行規劃的手動容錯移轉，以確認這個案例的復原計劃。  
  
#### <a name="step-1-confirm-the-data-source-connection-is-redirected-to-the-secondary-replica"></a>步驟 1：確認資料來源連接已重新導向至次要複本  
  
1.  啟動 SQL Server Profiler 並連接至裝載次要複本的 SQL Server 執行個體。  
  
     執行追蹤時， **SQL:BatchStarting** 和 **SQL:BatchCompleting** 事件會顯示 Analysis Services 所發出、在資料庫引擎執行個體上執行的查詢。 預設會選取這些事件，因此您只需要啟動追蹤。  
  
2.  在 [!INCLUDE[ssBIDevStudio](../../../includes/ssbidevstudio-md.md)]中，開啟包含您要測試之資料來源連接的 Analysis Services 專案或解決方案。 確定資料來源指定可用性群組接聽程式，而不是群組中的執行個體。  
  
     此步驟很重要。 如果指定伺服器執行個體名稱，則不會路由至次要複本。  
  
3.  排列應用程式視窗，以並排檢視 SQL Server Profiler 和 [!INCLUDE[ssBIDevStudio](../../../includes/ssbidevstudio-md.md)] 。  
  
4.  部署解決方案，並在完成時停止追蹤。  
  
     在追蹤視窗中，您應該會看到 **Microsoft SQL Server Analysis Services** 應用程式中的事件。 您應該會看到 **SELECT** 陳述式從裝載次要複本之伺服器執行個體上的資料庫擷取資料，證明透過接聽程式建立與次要複本的連接。  
  
#### <a name="step-2-perform-a-planned-failover-to-test-the-configuration"></a>步驟 2：執行規劃的容錯移轉以測試組態  
  
1.  在 [!INCLUDE[ssManStudio](../../../includes/ssmanstudio-md.md)] 中檢查主要與次要複本，確定已將這兩個複本設定為同步認可模式，且這兩個複本最近已經過同步處理。  
  
     下列步驟假設已將次要複本設定為同步認可。  
  
     若要確認同步處理，請開啟裝載主要與次要複本之每個執行個體的連線，請展開 [資料庫] 資料夾，並確定每個複本的資料庫名稱已附加 **(Synchronized)** 和 **(Synchronizing)** 。  
  
    > [!NOTE]  
    >  這些步驟摘錄自[執行可用性群組的已規劃手動容錯移轉 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/perform-a-planned-manual-failover-of-an-availability-group-sql-server.md)，文中提供執行這項工作的其他資訊和替代指示。  
  
2.  在 SQL Server Profiler 中，啟動每個複本的追蹤，以並排檢視追蹤。 在下列步驟中，您將比較追蹤，以確認從 Analysis Services 處理或查詢所使用的 SQL 查詢從其中一個複本切換至另一個複本。  
  
3.  從 Analysis Services 執行處理或查詢命令。 由於您已經設定唯讀連接的資料來源，因此您應該會看到這個命令在次要複本上執行。  
  
4.  在 [!INCLUDE[ssManStudio](../../../includes/ssmanstudio-md.md)] 中，連接至次要複本。  
  
5.  依序展開 [Always On 高可用性]  節點和 [可用性群組]  節點。  
  
6.  以滑鼠右鍵按一下要容錯移轉的可用性群組，然後選取 [容錯移轉]  命令。 這會啟動 [容錯移轉可用性群組精靈]。 使用精靈選擇複本，以做為新的主要複本。  
  
7.  確認容錯移轉成功：  
  
    -   在 [!INCLUDE[ssManStudio](../../../includes/ssmanstudio-md.md)]中，展開可用性群組以檢視 (主要) 與 (次要) 指定。 之前為主要複本的執行個體現在會變成次要複本。  
  
    -   檢視儀表板以判斷是否偵測到任何健全狀況問題。 以滑鼠右鍵按一下可用性群組，然後選取 [顯示儀表板]  。  
  
8.  等候一到兩分鐘，在後端完成容錯移轉。  
  
9. 重複 Analysis Services 解決方案中的處理或查詢命令，然後在 SQL Server Profiler 中並排檢視追蹤。 您應該會看到執行處理的其他執行個體現在成為新的次要複本。  
  
##  <a name="what-happens-after-a-failover-occurs"></a><a name="bkmk_whathappens"></a> 容錯移轉之後會發生什麼情況  
 在容錯移轉期間，次要複本會轉換到主要角色，而先前的主要複本會轉換到次要角色。 所有用戶端連線會終止，可用性群組接聽程式的擁有權會隨主要複本角色移至新的 SQL Server 執行個體，且接聽程式端點會繫結到新執行個體的虛擬 IP 位址和 TCP 連接埠。 如需詳細資訊，請參閱本主題稍後的 [關於可用性複本的用戶端連接存取 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/about-client-connection-access-to-availability-replicas-sql-server.md))。  
  
 如果在處理期間發生容錯移轉，Analysis Services 的記錄檔或輸出視窗中會發生下列錯誤：「OLE DB 錯誤: OLE DB 或 ODBC 錯誤: 通訊連結失敗; 08S01; TPC 提供者: 遠端主機已強制關閉一個現有連線。 ; 08S01。」  
  
 如果您稍候幾分鐘再試一次，應該可以解決這個錯誤。 如果將可用性群組正確設定為可讀取的次要複本，當您重試處理時，會繼續在新的次要複本上處理。  
  
 持續性錯誤的主要原因可能是組態問題。 您可以嘗試重新執行 T-SQL 指令碼，解決次要複本上的路由清單、唯讀路由 URL 及讀取意圖的問題。 您也應該確認主要複本允許所有連接。  
  
##  <a name="writeback-when-using-an-always-on-availability-database"></a><a name="bkmk_writeback"></a> 使用 AlwaysOn 可用性資料庫時回寫  
 回寫是 Analysis Services 功能，支援 Excel 的假設分析。 這項功能也常用於自訂應用程式中的預算和預測工作。  
  
 回寫支援需要 READWRITE 用戶端連接。 在 Excel 中，如果您嘗試在唯讀連線上回寫，會發生下列錯誤：「無法從外部資料來源擷取資料。」 「無法從外部資料來源擷取資料。」  
  
 如果設定連接一律存取可讀取的次要複本，現在就必須設定使用主要複本之 READWRITE 連接的新連接。  
  
 若要執行這項操作，請在 Analysis Services 模型中建立其他資料來源，以支援讀寫連接。 建立其他資料來源時，請使用您在唯讀連線中指定的相同接聽程式名稱和資料庫，但不要修改 [應用程式的意圖]  ，保留支援 READWRITE 連線的預設值。 您現在可以將以讀寫資料來源為基礎的新事實或維度資料表新增至資料來源檢視，然後啟用新資料表的回寫功能。  
  
## <a name="see-also"></a>另請參閱  
 [可用性群組接聽程式、用戶端連接性及應用程式容錯移轉 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/listeners-client-connectivity-application-failover.md)   
 [使用中次要：可讀取的次要複本 &#40;AlwaysOn 可用性群組&#41;](../../../database-engine/availability-groups/windows/active-secondaries-readable-secondary-replicas-always-on-availability-groups.md)   
 [AlwaysOn 可用性群組操作問題適用的 AlwaysOn 原則 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/always-on-policies-for-operational-issues-always-on-availability.md)   
 [建立資料來源 &#40;SSAS 多維度&#41;](/analysis-services/multidimensional-models/create-a-data-source-ssas-multidimensional)   
 [啟用維度回寫](/analysis-services/multidimensional-models/bi-wizard-enable-dimension-writeback)  
  
