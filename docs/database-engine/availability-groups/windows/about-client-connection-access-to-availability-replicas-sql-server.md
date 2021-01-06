---
title: 可用性群組內複本的用戶端連線類型
description: 深入了解用戶端可以對 SQL Server 內 Always On 可用性群組主要或次要複本建立的連線。
ms.custom: seodec18
ms.date: 05/17/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: conceptual
helpviewer_keywords:
- Availability Groups [SQL Server], availability replicas
- Availability Groups [SQL Server], readable secondary replicas
- active secondary replicas [SQL Server], read-only access to
- Availability Groups [SQL Server], configuring
- Availability Groups [SQL Server], client connectivity
- Availability Groups [SQL Server], active secondary replicas
ms.assetid: 29027e46-43e4-4b45-b650-c4cdeacdf552
author: cawrites
ms.author: chadam
ms.openlocfilehash: 6ed4b06649592e5cd904cfa75a5ec0fa4800eff4
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97644323"
---
# <a name="types-of-client-connections-to-replicas-within-an-always-on-availability-group"></a>Always On 可用性群組內複本的用戶端連線類型
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]
  在 AlwaysOn 可用性群組中，您可以設定一或多個可用性複本，讓它在次要角色之下執行時 (也就是以次要複本的方式執行時)，允許唯讀連接。 以主要角色執行時 (也就是當做主要複本執行時)，您也可以設定每個可用性複本，以允許或排除唯讀連接。  
  
 若要簡化用戶端對給定可用性群組之主要或次要資料庫的存取，您應該定義可用性群組接聽程式。 根據預設，可用性群組接聽程式會將內送連接導向至主要複本。 不過，您可以將可用性群組設定為支援唯讀路由，讓它的可用性群組接聽程式將讀取意圖應用程式的連接要求重新導向至可讀取的次要複本。 如需詳細資訊，請參閱本主題稍後的 [設定可用性群組的唯讀路由 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/configure-read-only-routing-for-an-availability-group-sql-server.md))。  
  
 在容錯移轉期間，次要複本會轉換到主要角色，而先前的主要複本會轉換到次要角色。 在容錯移轉過程中，會同時終止主要複本和次要複本的所有用戶端連接。 容錯移轉之後，當用戶端重新連接至可用性群組接聽程式時，接聽程式會將用戶端重新連接至新的主要複本 (但讀取意圖的連接要求除外)。 如果用戶端、裝載新主要複本的伺服器執行個體，以及至少一個可讀取的次要複本上設定了唯讀路由，讀取意圖連接要求會重新導向至支援用戶端所需連接存取類型的次要複本。 為確保容錯移轉後的用戶端經驗沒有失誤，最好同時針對每個可用性複本的次要和主要角色，設定連接存取。  
  
> [!NOTE]  
>  如需可用性群組接聽程式的相關資訊，處理用戶端連線要求，請參閱 [可用性群組接聽程式、用戶端連接及應用程式容錯移轉 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/listeners-client-connectivity-application-failover.md)。  
  
##  <a name="types-of-connection-access-supported-by-the-secondary-role"></a><a name="ConnectAccessForSecondary"></a> 次要角色所支援的連接存取類型  
 次要角色支援用戶端連接的三種替代方式，如下所示：  
  
 無連接  
 不允許任何使用者連接。 次要資料庫不適用於讀取。 這是次要角色的預設行為。  
  
 僅讀取意圖連接  
 次要資料庫僅適用於其 **Application Intent** 連接屬性設為 **ReadOnly** 的連接 (「讀取意圖連接」  )。  
  
 如需有關此連接屬性的詳細資訊，請參閱＜ [高可用性/災害復原的 SQL Server Native Client 支援](../../../relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery.md)＞。  
  
 允許任何唯讀連接  
 次要資料庫全部適用於讀取連接。 此選項允許較低版本的用戶端進行連接。  
  
 如需詳細資訊，請參閱 [設定可用性複本上的唯讀存取 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/configure-read-only-access-on-an-availability-replica-sql-server.md)。  
  
##  <a name="types-of-connection-access-supported-by-the-primary-role"></a><a name="ConnectAccessForPrimary"></a> 主要角色所支援的連接存取類型  
 主要角色支援用戶端連接的兩種替代方式，如下所示：  
  
 允許所有連接  
 同時允許與主要資料庫的讀寫和唯讀連接。 這是主要角色的預設行為。  
  
 僅允許讀寫連接  
 當 **Application Intent** 連接屬性設為 **ReadWrite** 或未設定時，允許連接。 不允許其 **Application Intent** 連接字串關鍵字設為 **ReadOnly** 的連接。 僅允許讀寫連接有助於防止客戶錯誤地將讀取意圖的工作負載連接至主要複本。  
  
 如需有關此連接屬性的詳細資訊，請參閱＜ [Using Connection String Keywords with SQL Server Native Client](../../../relational-databases/native-client/applications/using-connection-string-keywords-with-sql-server-native-client.md)＞。  
  
 如需詳細資訊，請參閱 [設定可用性複本上的唯讀存取 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/configure-read-only-access-on-an-availability-replica-sql-server.md)。  
  
##  <a name="how-the-connection-access-configuration-affects-client-connectivity"></a><a name="HowConnectionAccessAffectsConnectivity"></a> 連接存取組態如何影響用戶端連接  
 複本的連接存取設定會判斷連接嘗試失敗或成功。 下表摘要說明每個連接存取設定的給定連接嘗試成功或失敗。  
  
|複本角色|複本上支援的連接存取|連接意圖|連接嘗試結果|  
|------------------|--------------------------------------------|-----------------------|--------------------------------|  
|次要|全部|讀取意圖、讀寫，或未指定任何連接意圖|Success|  
|次要|無 (這是預設的次要行為)。|讀取意圖、讀寫，或未指定任何連接意圖|失敗|  
|次要|僅限讀取意圖|讀取意圖|Success|  
|次要|僅限讀取意圖|讀寫，或未指定任何連接意圖|失敗|  
|Primary|全部 (這是預設的主要行為)。|唯讀、讀寫，或未指定任何連接意圖|Success|  
|Primary|讀寫|僅限讀取意圖|失敗|  
|Primary|讀寫|讀寫，或未指定任何連接意圖|Success|  
  
 如需設定可用性群組接受用戶端連接至其複本的相關資訊，請參閱 [可用性群組接聽程式、用戶端連接及應用程式容錯移轉 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/listeners-client-connectivity-application-failover.md)。  
  
### <a name="example-connection-access-configuration"></a>連接存取組態範例  
 根據不同可用性複本設定成連接存取的方式，用戶端連接的支援可能會在可用性群組容錯移轉之後變更。 例如，請考慮使用針對遠端非同步認可次要複本執行其報告的可用性群組。 此可用性群組中資料庫的所有唯讀應用程式都會將其 **Application Intent** 連接屬性設為 **ReadOnly**，讓所有唯讀連接都是讀取意圖連接。  
  
 此可用性群組範例在主要運算中心擁有兩個同步認可複本，並在衛星站台擁有兩個非同步認可複本。 對於主要角色，所有複本都會設定成讀寫存取，以防止所有情況下主要複本的讀取意圖連接。 同步認可次要角色使用預設的連接存取組態 (「無」)，以防止次要角色下的所有用戶端連接。  對照之下，非同步認可複本會設定成允許使用次要角色的讀取意圖連接。 下表摘要說明這個範例組態：  
  
|複本|認可模式|初始角色|次要角色的連接存取|主要角色的連接存取|  
|-------------|-----------------|------------------|------------------------------------------|----------------------------------------|  
|Replica1|同步|Primary|None|讀寫|  
|Replica2|同步|次要|None|讀寫|  
|Replica3|非同步的|次要|僅限讀取意圖|讀寫|  
|Replica4|非同步的|次要|僅限讀取意圖|讀寫|  
  
 在此範例狀況下，容錯移轉通常只發生在同步認可複本之間，而且剛容錯移轉之後，讀取意圖應用程式可以重新連接至其中一個非同步認可次要複本。 不過，在主要運算中心發生災難時，兩個同步認可複本都會遺失。 衛星站台的資料庫管理員會透過對非同步認可次要複本執行強制手動容錯移轉來回應。 其餘次要複本上的次要資料庫會透過強制容錯移轉暫停，讓它們無法用於唯讀工作負載。 設定成讀寫連接的新主要複本會讓讀取意圖工作負載無法與讀寫工作負載競爭。 也就是說，在資料庫管理員針對其餘非同步認可次要複本繼續次要資料庫之前，讀取意圖用戶端無法連接至任何可用性複本。  
  
##  <a name="related-tasks"></a><a name="RelatedTasks"></a> 相關工作  
  
-   [設定可用性複本的唯讀存取 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/configure-read-only-access-on-an-availability-replica-sql-server.md)  
  
-   [設定可用性群組的唯讀路由 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/configure-read-only-routing-for-an-availability-group-sql-server.md)  
  
-   [監視可用性群組 &#40;Transact-SQL&#41;](../../../database-engine/availability-groups/windows/monitor-availability-groups-transact-sql.md)  
  
-   [檢視可用性複本屬性 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/view-availability-replica-properties-sql-server.md)  
  
-   [使用新增可用性群組對話方塊 &#40;SQL Server Management Studio&#41;](../../../database-engine/availability-groups/windows/use-the-new-availability-group-dialog-box-sql-server-management-studio.md)  
  
##  <a name="related-content"></a><a name="RelatedContent"></a> 相關內容  
  
-   [Microsoft SQL Server AlwaysOn 高可用性和災害復原方案指南](/previous-versions/sql/sql-server-2012/hh781257(v=msdn.10))  
  
-   [SQL Server AlwaysOn 團隊部落格：SQL Server AlwaysOn 官方團隊部落格](/archive/blogs/sqlalwayson/)  
  
## <a name="see-also"></a>另請參閱  
 [AlwaysOn 可用性群組概觀 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server.md)   
 [可用性群組接聽程式、用戶端連接性及應用程式容錯移轉 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/listeners-client-connectivity-application-failover.md)   
 [統計資料](../../../relational-databases/statistics/statistics.md)  
  
