---
title: 使用 SSMS 中的可用性群組儀表板
description: 描述如何在 SQL Server Management Studio (SSMS) 中，使用 Always On 可用性群組儀表板來監視可用性群組的健康情況。
ms.custom: seo-lt-2019
ms.date: 08/09/2018
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: conceptual
f1_keywords:
- sql13.swb.agdashboard.f1
helpviewer_keywords:
- Availability Groups [SQL Server], policies
- Availability Groups [SQL Server], dashboard
ms.assetid: c9ba2589-139e-42bc-99e1-94546717c64d
author: cawrites
ms.author: chadam
ms.openlocfilehash: de6d20cbd6e1b185b9e06e057c0cbe91c590fb48
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97641784"
---
# <a name="use-the-always-on-availability-group-dashboard-sql-server-management-studio"></a>使用 Always On 可用性群組儀表板 (SQL Server Management Studio)
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]

  資料庫管理員可以使用 AlwaysOn 可用性群組儀表板，在 [!INCLUDE[ssCurrent](../../../includes/sscurrent-md.md)] 中取得可用性群組及其可用性複本和資料庫的健全狀況摘要檢視。 可用性群組儀表板的一些一般用法如下：  
  
-   選擇手動容錯移轉的複本。    
-   估計資料遺失 (如果您強制容錯移轉的話)。  
-   評估資料同步處理效能。   
-   評估同步認可次要複本的效能影響。 
  -  儀表板提供一些重要的可用性群組狀態和效能指標，可讓您輕鬆地使用下列資訊類型進行高可用性作業決策。  
-   複本積存狀態    
-   同步處理模式和狀態   
-   預估資料遺失    
-   預估復原時間 (重做趕上)    
-   資料庫複本詳細資料    
-   同步處理模式和狀態    
-   還原記錄的時間  
  
  
## <a name="prerequisites"></a>Prerequisites  
 您必須連接到裝載可用性群組之主要複本或次要複本的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體 (伺服器執行個體)。  
  
 
### <a name="permissions"></a>權限  
 需要 CONNECT、VIEW SERVER STATE 和 VIEW ANY DEFINITION 權限。  
  
##  <a name="to-start-the-always-on-dashboard"></a>啟動 Always On 儀表板  
  
1.  在 [物件總管] 中，連接到您想要執行 AlwaysOn 儀表板的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體。  
  
2.  展開 [AlwaysOn 高可用性]  節點、以滑鼠右鍵按一下 [可用性群組]  節點，然後按一下 [顯示儀表板]  。  
  
##  <a name="change-always-on-dashboard-options"></a>變更 Always On 儀表板選項  
 您可以使用 [!INCLUDE[ssManStudioFull](../../../includes/ssmanstudiofull-md.md)] 的 [選項]  對話方塊來設定 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] AlwaysOn 儀表板行為，以便進行自動重新整理並且啟用自動定義的 AlwaysOn 原則。  
  
1.  在 **[工具]** 功能表中，按一下 **[選項]** 。  
  
2.  若要自動重新整理儀表板，請在 **[選項]** 對話方塊中，選取 **[開啟自動重新整理]** 、輸入重新整理間隔 (以秒為單位)，然後輸入您想要重試連接的次數。  
  
3.  若要啟用使用者定義的原則，請選取 [啟用使用者定義 AlwaysOn 原則]  。  
  
##  <a name="availability-group-summary"></a>可用性群組摘要  
 可用性群組畫面會針對連接之伺服器執行個體裝載複本的每個可用性群組顯示摘要行。 這個窗格會顯示下列資料行。  
  
 **可用性群組名稱**  
 連接之伺服器執行個體裝載複本的可用性群組名稱。  
  
 **主要執行個體**  
 裝載可用性群組之主要複本的伺服器執行個體名稱。  
  
 **容錯移轉模式**  
 顯示複本所設定的容錯移轉模式。 可能的容錯移轉模式值包括：  
  
-   **自動**： 表示一個或多個複本處於自動容錯移轉模式。  
  
-   **手動**： 表示沒有任何複本處於自動容錯移轉模式。  
  
 **問題**  
 按一下 [問題]  連結可開啟給定問題的疑難排解文件集。 如需所有 AlwaysOn 原則問題的清單，請參閱[AlwaysOn 可用性群組操作問題適用的 AlwaysOn 原則 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/always-on-policies-for-operational-issues-always-on-availability.md)。  
  
> [!TIP]  
>  按一下資料行標題可依照可用性群組、主要執行個體、容錯移轉模式或問題的名稱排序可用性群組資訊。  
  
##  <a name="availability-group-details"></a><a name="AvGroupDetails"></a> 可用性群組詳細資料  
 系統會針對您從摘要畫面中選取的可用性群組顯示下列詳細資訊：  
  
 **可用性群組狀態**  
 顯示可用性群組的健全狀態。  
  
 **Primary instance**  
 裝載可用性群組之主要複本的伺服器執行個體名稱。  
  
 **Failover mode**  
 顯示複本所設定的容錯移轉模式。 可能的容錯移轉模式值包括：  
  
-   **自動**： 表示一個或多個複本處於自動容錯移轉模式。  
  
-   **手動**： 表示沒有任何複本處於自動容錯移轉模式。  
  
 **叢集狀態**  
 連接之伺服器執行個體與可用性群組為成員節點的叢集名稱和狀態。  
  
##  <a name="availability-replica-details"></a><a name="AvReplicaDetails"></a> 可用性複本詳細資料  

當連線到主要複本時，[可用性複本詳細資料]  會顯示可用性群組中的所有複本資訊。 當連線到次要複本時，只會顯示連線的複本資訊。  

**[可用性複本]** 窗格會顯示下列資料行：  
  
 **名稱**  
 裝載可用性複本的伺服器執行個體名稱。 預設顯示此資料行。  
  
 **角色**  
 指出可用性複本的目前角色： **主要** 或 **次要**。 如需 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] 角色的詳細資訊，請參閱 [Always On 可用性群組概觀 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server.md)。 預設顯示此資料行。  
  
 **容錯移轉模式**  
 顯示複本所設定的容錯移轉模式。 可能的容錯移轉模式值包括：  
  
-   **自動**： 表示一個或多個複本處於自動容錯移轉模式。  
  
-   **手動**： 表示沒有任何複本處於自動容錯移轉模式。  
  
 **同步處理狀態**  
 指出次要複本目前是否與主要複本進行同步處理。 預設顯示此資料行。 可能的值包括：  
  
-   **未同步處理**： 複本中的一個或多個資料庫尚未同步處理，或者尚未聯結至可用性群組。  
  
-   **正在同步處理**： 正在同步處理複本中的一個或多個資料庫。  
  
-   **已同步處理**： 次要複本中的所有資料庫都會與目前主要複本上的對應主要資料庫 (如果有的話) 或是最後一個主要複本上的對應主要資料庫進行同步處理。  
  
    > [!NOTE]  
    >  在效能模式中，資料庫永遠不會處於同步處理狀態。  
  
-   **NULL**： 未知的狀態。 當本機伺服器執行個體無法與 WSFC 容錯移轉叢集通訊 (亦即，本機節點不屬於 WSFC 仲裁的一部分) 時，就會出現這個值。  
  
 **問題**  
 列出問題名稱。 預設顯示此值。 如需所有 AlwaysOn 原則問題的清單，請參閱[AlwaysOn 可用性群組操作問題適用的 AlwaysOn 原則 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/always-on-policies-for-operational-issues-always-on-availability.md)。  
  
 **可用性模式**  
 指出您個別針對每個可用性複本所設定的複本屬性。 預設隱藏此值。 可能的值包括：  
  
-   **非同步**： 次要複本永遠不會變成與主要複本進行同步處理。  
  
-   **Synchronous**： 趕上主要資料庫時，次要資料庫就會進入此狀態，而且只要資料庫的資料同步處理繼續進行，它就會維持趕上狀態。  
  
 **主要連接模式**  
 指出用來連接到主要複本的模式。  預設隱藏此值。  
  
 **次要連接模式**  
 指出用來連接到次要複本的模式。  預設隱藏此值。  
  
 **連接狀態**  
 指出次要複本目前是否已連接到主要複本。 預設隱藏此資料行。 可能的值包括：  
  
-   **已中斷連接**： 若為遠端可用性複本，表示它與本機可用性複本已中斷連接。 本機複本對 [已中斷連接] 狀態的回應取決於其角色，如下所示：  
  
    -   在主要複本上，如果次要複本已中斷連接，主要複本上的次要資料庫就會標示為 **[未同步處理]** ，而且主要複本會等候次要複本重新連接。  
  
    -   在次要複本上，一旦偵測到它已中斷連接之後，次要複本就會嘗試重新連接到主要複本。  
  
-   **Connected**。 目前連接到本機複本的遠端可用性複本。  
  
 **操作狀態**  
 指出次要複本的目前操作狀態。 預設隱藏此值。 可能的值包括：  
  
 **0**.暫止容錯移轉    
 **1**.暫止    
 **2**.線上存取    
 **3**.離線   
 **4**.失敗    
 **5**.失敗，無仲裁  
  
 **NULL**： 複本非本機  
  
 **上次連接錯誤號碼**  
 上次連接錯誤的號碼。  預設隱藏此值。  
  
 **上次連接錯誤描述**  
 上次連接錯誤的描述。  預設隱藏此值。  
  
 **上次連接錯誤時間戳記**  
 上次連接錯誤的時間戳記。 預設隱藏此值。  
  
> [!NOTE]  
>  如需可用性複本效能計數器的相關資訊，請參閱 [SQLServer，可用性複本](../../../relational-databases/performance-monitor/sql-server-availability-replica.md)。  
  
##  <a name="group-by-availability-group-information"></a>依可用性群組資訊分組  
 若要將資訊分組，請按一下 **[群組依據]** ，然後選取下列其中一項：  
  
-   **可用性複本**    
-   **可用性資料庫** 
-   **Synchronization state**  
-   **容錯移轉整備**   
-   **問題**  
  
 顯示群組資訊的窗格會顯示下列資料行：  
  
 **名稱**  
 可用性資料庫的名稱。 預設顯示此值。  
  
 **複本**  
 裝載可用性複本之 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體的名稱。 預設顯示此值。  
  
 **同步處理狀態**  
 指出可用性資料庫目前是否與主要複本進行同步處理。 預設顯示此值。 可能的同步處理狀態包括：  
  
-   **未同步處理**：  
-   
    -   如果是主要角色，表示資料庫尚未準備好要將其交易記錄與對應的次要資料庫同步處理。   
    -   如果是次要資料庫，表示資料庫尚未開始記錄同步處理，原因是因為連接問題、處於暫停狀態，或在啟動或角色切換期間正在移轉狀態。  
  
-   **正在同步處理**：
- 在主要複本上：   
    - 在主要資料庫上，表示此資料庫準備好接受次要資料庫的掃描要求。  
    - 在次要複本上，表示該次要資料庫有進行中的資料移動作業。 
  
  
-   **已同步處理**： 
  
    - 如果是主要資料庫，表示至少已同步處理一個次要資料庫。
    - 如果是次要資料庫，表示資料庫已與對應的主要資料庫同步處理。  
  
-   **正在還原**：  
  
     表示當次要資料庫積極取得主要資料庫的頁面時，復原程序中的階段。  
  
    > [!CAUTION]  
    >  當資料庫處於 REVERTING 狀態時，強制容錯移轉至次要複本會將資料庫保留在無法啟動的狀態。  
  
-   **正在初始化**：  
  
     表示次要資料庫跟上復原 LSN 所需的交易記錄正在傳送而且在次要複本上強行寫入時的復原階段。  
  
    > [!CAUTION]  
    >  當資料庫處於 INITIALIZING 狀態時，強制容錯移轉至次要複本一定會將資料庫保留在無法啟動的狀態。  
  
 **[容錯移轉整備]**  
 指出哪個可用性複本可能會在遺失資料或不遺失資料的情況下容錯移轉。 預設顯示此資料行。 可能的值包括：  
  
-   **資料遺失**   
-   **不遺失資料**  
  
 **問題**  
 列出問題名稱。 預設顯示此資料行。 可能的值包括：  
  
-   **警告**： 按一下可顯示臨界值和警告問題。   
-   **關鍵**： 按一下可顯示關鍵問題。  
  
 如需所有 AlwaysOn 原則問題的清單，請參閱[AlwaysOn 可用性群組操作問題適用的 AlwaysOn 原則 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/always-on-policies-for-operational-issues-always-on-availability.md)。  
  
 **已暫停**  
 指出資料庫 **[已暫停]** 或 **[已繼續]** 。 預設隱藏此值。  
  
 **暫停原因**  
 指出暫停狀態的原因。 預設隱藏此值。  
  
 **預估資料遺失 (秒)**  
 表示主要複本與次要複本中上一筆交易記錄的時間差異。 如果主要複本失敗，則時間視窗內的所有交易記錄都將遺失。 預設隱藏此值。  
  
 **預估復原時間 (秒)**  
 指出重做趕上時間所需要的時間 (以秒為單位)。 *「趕上時間」* (catch-up time) 是指次要複本趕上主要複本所需的時間。 預設隱藏此值。  
  
 **同步處理效能 (秒)**  
 指出在主要與次要複本之間同步處理所需的時間 (以秒為單位)。 預設隱藏此值。  
  
 **記錄檔傳送佇列大小 (KB)**  
 指出主要資料庫記錄檔中尚未傳送至次要複本的記錄檔記錄數目。 預設隱藏此值。  
  
 **記錄檔傳送速率 (KB/秒)**  
 指出將記錄檔記錄傳送到次要複本所使用的速率 (以每秒 KB 數為單位)。 預設隱藏此值。  
  
 **重做佇列大小 (KB)**  
 指出次要複本記錄檔中尚未重做的記錄檔記錄數目。 預設隱藏此值。  
  
 **重做速率 (KB/秒)**  
 指出重做記錄檔記錄所使用的速率 (以每秒 KB 數為單位)。 預設隱藏此值。  
  
 **FileStream 傳送速率 (KB/秒)**  
 指出將交易傳送到複本所使用的 FileStream 速率 (以每秒 KB 數為單位)。 預設隱藏此值。  
  
 **記錄檔結束 LSN**  
 指出對應到主要和次要複本上記錄檔快取中之上一個記錄檔記錄的實際記錄序號 (LSN)。 預設隱藏此值。  
  
 **復原 LSN**  
 指出在主要複本上，此複本在復原或容錯移轉後、寫入任何新記錄檔記錄前，交易記錄的結尾。 預設隱藏此值。  
  
 **截斷 LSN**  
 指出主要複本的最小交易記錄值。 預設隱藏此值。  
  
 **上次認可 LSN**  
 指出對應到交易記錄中上一個認可記錄的實際 LSN。 預設隱藏此值。  
  
 **上次認可時間**  
 指出對應到上一個認可記錄的時間。 預設隱藏此值。  
  
 **上次傳送 LSN**  
 指出主要複本已傳送所有記錄檔區塊到哪一點。 預設隱藏此值。  
  
 **上次傳送時間**  
 指出上次傳送記錄檔區塊的時間。 預設隱藏此值。  
  
 **上次接收 LSN**  
 指出裝載次要資料庫的次要複本已經接收所有記錄檔區塊到哪一點。 預設隱藏此值。  
  
 **上次接收時間**  
 指出在次要複本上讀取上一個接收訊息內之記錄檔區塊識別碼的時間。 預設隱藏此值。  
  
 **上次強行寫入 LSN**  
 指出次要複本上已將所有記錄檔記錄排清至磁碟到哪一點。 預設隱藏此值。  
  
 **上次強行寫入時間**  
 指出在次要複本上，收到上次強行寫入 LSN 之記錄檔區塊識別碼的時間。 預設隱藏此值。  
  
 **上次重做 LSN**  
 指出上次在次要複本上重做之記錄檔記錄的實際 LSN。 預設隱藏此值。  
  
 **上次重做時間**  
 指出在次要資料庫上重做上一個記錄檔記錄的時間。 預設隱藏此值。  
 

   > [!NOTE]  
   >  大部分的資料是以 sys.dm_hadr_database_replica_states 為基礎，因此可能適用某些限制。 如需詳細資訊，請參閱 [sys.dm_hadr_database_replica_states (Transact-SQL)](../../../relational-databases/system-dynamic-management-views/sys-dm-hadr-database-replica-states-transact-sql.md)。


## <a name="always-on-availability-group-latency-reports"></a>Always On 可用性群組延遲報表
可用性群組延遲報表是一種報表工具，內建於可用性群組儀表板且可用於 [SQL Server Management Studio 17.4](../../../ssms/download-sql-server-management-studio-ssms.md) 版。 此功能提供容易了解的報表，詳述記錄傳輸程序各種階段所花的時間。 這可用來縮小在同步處理程序期間可能造成延遲的原因。 

SQL Agent 會執行資料收集，而且必須在主要複本以及至少一個次要複本上啟用。 在 SQL Server Management Studio 的 [物件總管]  中，以滑鼠右鍵按一下可用性群組 > [報表] > [標準報表]，以檢視報表。  

如需詳細資訊，請參閱 [Always On 可用性群組延遲報表](/archive/blogs/sql_server_team/new-in-ssms-always-on-availability-group-latency-reports)。

## <a name="related-tasks"></a>相關工作  
  
-   [使用 AlwaysOn 原則檢視可用性群組的健全狀況 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/use-always-on-policies-to-view-the-health-of-an-availability-group-sql-server.md)  
  
## <a name="see-also"></a>另請參閱  
 [sys.dm_os_performance_counters &#40;Transact-SQL&#41;](../../../relational-databases/system-dynamic-management-views/sys-dm-os-performance-counters-transact-sql.md)   
 [監視可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server.md)  
  
