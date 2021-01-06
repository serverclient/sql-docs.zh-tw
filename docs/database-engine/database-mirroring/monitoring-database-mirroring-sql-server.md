---
title: 監視資料庫鏡像 (SQL Server) | Microsoft Docs
description: 了解資料庫鏡像監視器、系統預存程序，以及資料庫鏡像監視的運作方式，包括資料庫鏡像監視器作業。
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: high-availability
ms.reviewer: ''
ms.technology: database-mirroring
ms.topic: conceptual
helpviewer_keywords:
- monitoring [SQL Server], database mirroring
- database mirroring [SQL Server], monitoring
ms.assetid: a7b1b9b0-7c19-4acc-9de3-3a7c5e70694d
author: MikeRayMSFT
ms.author: mikeray
ms.openlocfilehash: 9b77b54ba48dc2c3820d055227411f61983b1a7c
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97644280"
---
# <a name="monitoring-database-mirroring-sql-server"></a>監視資料庫鏡像 (SQL Server)
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
  本節介紹「資料庫鏡像監視器」和 **sp_dbmmonitor** 系統預存程序、說明資料庫鏡像監視功能 (包括 [資料庫鏡像監視器作業] ) 的運作方式，以及摘要說明您可以監視的資料庫鏡像工作階段相關資訊。 另外，本節還會介紹如何為一組預先定義的資料庫鏡像事件定義警告臨界值，以及如何在任何資料庫鏡像事件上設定警示。  
  
 您可以在鏡像工作階段期間監視鏡像資料庫，以便確認資料流程是否正常。 若要針對伺服器執行個體上的一個或多個鏡像資料庫設定並管理監視作業，您可以使用「資料庫鏡像監視器」或 **sp_dbmmonitor** 系統預存程序。  
  
 資料庫鏡像監視作業 **[資料庫鏡像監視器作業]** 是在背景中獨立運作，不受 [資料庫鏡像監視器] 影響。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent 會按一定的間隔呼叫 **[資料庫鏡像監視器作業]** ，預設為每分鐘一次，而這項作業則呼叫更新鏡像狀態的預存程序。 如果您使用 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 來啟動鏡像工作階段，便會自動建立 **[資料庫鏡像監視器作業]** 。 不過，如果您只使用 ALTER DATABASE *<資料庫名稱>* SET PARTNER 來啟動鏡像，則必須執行預存程序才能建立作業。  
  
 **本主題內容：**  
  
-   [監視鏡像狀態](#MonitoringStatus)  
  
-   [與鏡像資料庫相關的其他資訊來源](#AdditionalSources)  
  
-   [相關工作](#RelatedTasks)  
  
##  <a name="monitoring-mirroring-status"></a><a name="MonitoringStatus"></a> 監視鏡像狀態  
 若要針對伺服器執行個體上的一個或多個鏡像資料庫設定並管理監視作業，您可以使用 [資料庫鏡像監視器] 或 **dbmmonitor** 系統預存程序。 您可以在鏡像工作階段期間監視鏡像資料庫，以便確認資料流程是否正常。  
  
 更明確地說，監視鏡像資料庫可讓您：  
  
-   確認鏡像是否正常運作。  
  
     基本狀態包括了解兩個伺服器執行個體是否已啟用、伺服器是否已連接以及是否已將記錄從主體移至鏡像。  
  
-   判斷鏡像資料庫是否與主體資料庫保持同步。  
  
     在高效能模式下，主體伺服器可能會開發未傳送記錄檔記錄的積存，而這些記錄仍需從主體伺服器傳送至鏡像伺服器。 此外，在任何作業模式下，鏡像伺服器可能會開發未還原記錄檔記錄的積存，而且雖然這些記錄已經寫入記錄檔，不過仍需在鏡像資料庫上還原。  
  
-   判斷在高效能模式下主體伺服器執行個體成為無法使用時，有多少資料遺失。  
  
     您可以透過查看未傳送的交易記錄量 (如果有的話) 以及在主體上認可遺失交易的時間間隔，藉以判斷資料遺失。  
  
-   比較目前的效能與過去的效能。  
  
     發生問題時，資料庫管理員可以檢視鏡像效能的記錄，以便有效了解目前的狀態。 查看記錄可讓使用者偵測效能的趨勢，並識別效能問題的模式 (例如，網路速度變慢或輸入記錄的命令數目很大的當日時間)。  
  
-   疑難排解鏡像夥伴之間減少資料流程的原因。  
  
-   設定關鍵效能標準的警告臨界值。  
  
     如果新的狀態資料列含有超過臨界值的值，就會傳送參考用事件至 Windows 事件記錄檔。 然後，系統管理員就可以根據這些事件，手動設定警示。 如需詳細資訊，請參閱 [使用鏡像效能標準的警告臨界值與警示 &#40;SQL Server&#41;](../../database-engine/database-mirroring/use-warning-thresholds-and-alerts-on-mirroring-performance-metrics-sql-server.md)。  
  
###  <a name="tools-for-monitoring-database-mirroring-status"></a><a name="tools_for_monitoring_dbm_status"></a> 監視資料庫鏡像狀態的工具  
 您可以使用「資料庫鏡像監視器」或 **sp_dbmmonitorresults** 系統預存程序來監視鏡像狀態。 系統管理員 (亦即 **系統管理員** 固定伺服器角色的成員) 和系統管理員在 **msdb** 資料庫之 **dbm_monitor** 固定資料庫角色中加入的使用者，都可以使用這些工具來監視本機伺服器執行個體上任何鏡像資料庫的資料庫鏡像。 使用任何一項工具時，系統管理員也可以手動重新整理鏡像狀態。  
  
> [!NOTE]  
>  系統管理員還可以設定並檢視關鍵效能標準的警告臨界值。 如需詳細資訊，請參閱 [使用鏡像效能標準的警告臨界值與警示 &#40;SQL Server&#41;](../../database-engine/database-mirroring/use-warning-thresholds-and-alerts-on-mirroring-performance-metrics-sql-server.md)。  
  
-   資料庫鏡像監視器  
  
     [資料庫鏡像監視器] 是一個圖形化使用者介面工具，可讓系統管理員檢視並更新狀態，以及設定許多關鍵效能標準的警告臨界值。 此外， **dbm_monitor** 固定資料庫角色的成員也可以使用「資料庫鏡像監視器」來檢視鏡像狀態資料表的最新資料列，不過他們無法更新狀態資料表。  
  
     監視器會將選取的資料庫的狀態 (包括效能標準) 顯示在 **[狀態]** 索引標籤頁面上。 此頁面的內容來自於主體伺服器執行個體和鏡像伺服器執行個體。 透過與主體和鏡像伺服器執行個體的個別連接蒐集狀態時，會以非同步方式填滿頁面。 此監視器會嘗試以 30 秒的間隔更新狀態資料表。 只有當資料表在 15 秒內尚未更新過，而且使用者是 **sysadmin** 固定伺服器角色的成員時，更新才會成功。 如需 **[狀態]** 頁面上所報告資訊的摘要，請參閱本主題稍後的＜ [資料庫鏡像監視器顯示的狀態](#perf_metrics_of_dbm_monitor)＞。  
  
     如需 [資料庫鏡像監視器] 介面的簡介，請參閱＜ [Database Mirroring Monitor Overview](../../database-engine/database-mirroring/database-mirroring-monitor-overview.md)＞。 如需啟動「資料庫鏡像監視器」的資訊，請參閱 [啟動資料庫鏡像監視器 &#40;SQL Server Management Studio&#41;](../../database-engine/database-mirroring/start-database-mirroring-monitor-sql-server-management-studio.md)。  
  
-   系統預存程序  
  
     您也可以執行 **sp_dbmmonitorresults** 系統預存程序來擷取或更新目前狀態。 其他 dbmmonitor 預存程序則可讓您設定監視、變更監視參數、檢視目前的更新週期以及卸除伺服器執行個體上的監視作業。  
  
     下表介紹不需依賴 [資料庫鏡像監視器]，即可獨立用於管理並使用資料庫鏡像監視作業的預存程序。  
  
    |程序|描述|  
    |---------------|-----------------|  
    |[sp_dbmmonitoraddmonitoring](../../relational-databases/system-stored-procedures/sp-dbmmonitoraddmonitoring-transact-sql.md)|建立一項作業，以便定期更新伺服器執行個體上每個鏡像資料庫的狀態資訊。|  
    |[sp_dbmmonitorchangemonitoring](../../relational-databases/system-stored-procedures/sp-dbmmonitorchangemonitoring-transact-sql.md)|變更資料庫鏡像監視參數的值。|  
    |[sp_dbmmonitorhelpmonitoring](../../relational-databases/system-stored-procedures/sp-dbmmonitorhelpmonitoring-transact-sql.md)|傳回目前的更新週期。|  
    |[sp_dbmmonitorresults](../../relational-databases/system-stored-procedures/sp-dbmmonitorresults-transact-sql.md)|傳回監視資料庫的狀態資料列，並且讓您選擇是否要讓此程序事先取得最新狀態。|  
    |[sp_dbmmonitordropmonitoring](../../relational-databases/system-stored-procedures/sp-dbmmonitordropmonitoring-transact-sql.md)|停止並刪除伺服器執行個體上所有資料庫的鏡像監視器作業。|  
  
     **dbmmonitor** 系統預存程序可當做 [資料庫鏡像監視器] 的輔助使用。 例如，即使監視作業是使用 **sp_dbmmonitoraddmonitoring** 設定的，您還是可以使用「資料庫鏡像監視器」來檢視狀態。  
  
### <a name="how-monitoring-works"></a>監視的運作方式  
 本節將介紹資料庫鏡像狀態資料表、資料庫鏡像監視器作業和監視器、使用者如何監視資料庫鏡像狀態，以及如何卸除監視作業。  
  
#### <a name="database-mirroring-status-table"></a>資料庫鏡像狀態資料表  
 資料庫鏡像狀態會儲存在 **msdb** 資料庫之內部且未記載的資料庫鏡像狀態資料表中。 首次在伺服器執行個體上更新鏡像狀態時，就會自動建立此狀態資料表。  
  
 系統管理員可以自動或手動更新狀態資料表，而且最小更新間隔為 15 秒。 15 秒的下限可防止伺服器執行個體因狀態要求而超過負載。  
  
 此狀態資料表會自動由 [資料庫鏡像監視器] 和資料庫鏡像監視器作業更新 (如果有執行的話)。 [資料庫鏡像監視器作業] 預設會每分鐘更新資料表一次 (系統管理員可以指定介於 1 至 120 分鐘的更新週期)。 不過，[資料庫鏡像監視器] 則會每隔 30 秒自動更新資料表。 進行這些更新作業時，[資料庫鏡像監視器作業]  和「資料庫鏡像監視器」都會呼叫 **sp_dbmmonitorupdate**。  
  
 **sp_dbmmonitorupdate** 初次執行時，會建立 **資料庫鏡像狀態** 資料表，並在 **msdb** 資料庫中建立 **dbm_monitor** 固定資料庫角色。 **sp_dbmmonitorupdate** 通常會針對伺服器執行個體上每個鏡像資料庫的狀態資料表插入新資料列，以更新鏡像狀態；如需詳細資訊，請參閱本主題稍後的＜資料庫鏡像狀態資料表＞。 這個程序也會評估新資料列中的效能標準，並截斷晚於目前保留期限 (預設為 7 天) 的資料列。 如需詳細資訊，請參閱 [sp_dbmmonitorupdate &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-dbmmonitorupdate-transact-sql.md)。  
  
> [!NOTE]  
>  除非「資料庫鏡像監視器」目前正由 **系統管理員** 固定伺服器角色的成員使用，否則只有當 **[資料庫鏡像監視器作業]** 存在且 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent 執行時，才會自動更新狀態資料表。  
  
#### <a name="database-mirroring-monitor-job"></a>資料庫鏡像監視器作業  
 資料庫鏡像監視作業 **[資料庫鏡像監視器作業]** 是獨立於 [資料庫鏡像監視器] 運作的。 只有當 **是用來啟動鏡像工作階段時，才會自動建立** [資料庫鏡像監視器作業] [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 。 如果一律使用 ALTER DATABASE *資料庫名稱* SET PARTNER 命令來啟動鏡像，則只有當系統管理員執行 **sp_dbmmonitoraddmonitoring** 預存程序時，此作業才會存在。  
  
 建立 **[資料庫鏡像監視器作業]** 之後，假設 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent 正在執行，則預設會每分鐘呼叫此作業一次。 然後，此作業就會呼叫 **sp_dbmmonitorupdate** 系統預存程序。  
  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent 預設會每分鐘呼叫 [資料庫鏡像監視器作業]  一次，而且此作業會呼叫 **sp_dbmmonitorupdate** 來更新狀態資料表。 系統管理員可以使用 **sp_dbmmonitorchangemonitoring** 系統預存程序來變更更新週期，而且可以使用 **sp_dbmmonitorchangemonitoring** 系統預存程序來檢視目前的更新週期。 如需詳細資訊，請參閱 [sp_dbmmonitoraddmonitoring &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-dbmmonitoraddmonitoring-transact-sql.md) 和 [sp_dbmmonitorchangemonitoring &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-dbmmonitorchangemonitoring-transact-sql.md)。  
  
#### <a name="monitoring-database-mirroring-status-by-system-administrators"></a>監視資料庫鏡像狀態 (系統管理員)  
 **sysadmin** 固定伺服器角色的成員可以檢視並更新狀態資料表。  
  
-   使用資料庫鏡像監視器  
  
     使用 [資料庫鏡像監視器] 時，系統管理員就可以手動重新整理 **[狀態]** 頁面、巡覽樹狀目錄或 **[記錄]** 頁面。 而且，除非已經在前 15 秒內更新過，否則它也會更新狀態資料表。  
  
     若要檢視指定伺服器執行個體上的鏡像狀態記錄，系統管理員也可以按一下伺服器執行個體的 [記錄]  按鈕 (在 [狀態]  頁面上)。 然後，記錄就會顯示在 **[資料庫鏡像記錄]** 對話方塊中。 系統管理員可以在此對話方塊中檢視伺服器執行個體之狀態資料表中的某些或所有資料列。  
  
     如需 **[狀態]** 頁面標準的詳細資訊，請參閱本主題稍後的「資料庫鏡像監視器顯示的效能標準」。  
  
-   使用 **sp_dbmmonitorresults**  
  
     系統管理員可以使用 **sp_dbmmonitorresults** 系統預存程序來檢視並選擇性地更新狀態資料表 (如果在前 15 秒內未更新過的話)。 此程序會呼叫 **sp_dbmmonitorupdate** 程序並根據程序呼叫中要求的數量，傳回一個或多個記錄資料列。 如需結果集中狀態的資訊，請參閱 [sp_dbmmonitorresults &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-dbmmonitorresults-transact-sql.md)。  
  
#### <a name="monitoring-database-mirroring-status-by-dbm_monitor-members"></a>監視資料庫鏡像狀態 (dbm_monitor 成員)  
 如上所述，首次執行 **sp_dbmmonitorupdate** 時，它會在 **msdb** 資料庫中建立 **dbm_monitor** 固定資料庫角色。 **dbm_monitor** 固定資料庫角色的成員可以使用「資料庫鏡像監視器」或 **sp_dbmmonitorresults** 預存程序，檢視現有的鏡像狀態。 但是這些使用者無法更新狀態資料表。 若要了解顯示狀態的時間，使用者可在 [狀態] 頁面上查看 [主體記錄 ( _\<time>_ )]  和 [鏡像記錄 ( _\<time>_ )]  標籤中的時間。  
  
 **dbm_monitor** 固定資料庫角色的成員會仰賴 [資料庫鏡像監視器作業]  來定期更新狀態資料表。 如果此作業不存在或 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent 已停止，狀態就會逐漸成為過時，而且不再反映鏡像工作階段的組態。 例如，在容錯移轉之後，夥伴可能看起來像是共用相同的角色 (主體或鏡像)，或者目前的主體伺服器可能會顯示為鏡像，而目前的鏡像伺服器則顯示為主體。  
  
#### <a name="dropping-the-database-mirroring-monitor-job"></a>卸除資料庫鏡像監視器作業  
 資料庫鏡像監視器作業 **[資料庫鏡像監視器作業]** 會維持到卸除為止。 此監視作業必須由系統管理員管理。 若要卸除 [資料庫鏡像監視器作業] ，請使用 **sp_dbmmonitordropmonitoring**。 如需詳細資訊，請參閱 [sp_dbmmonitordropmonitoring &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-dbmmonitordropmonitoring-transact-sql.md)。  
  
###  <a name="status-displayed-by-the-database-mirroring-monitor"></a><a name="perf_metrics_of_dbm_monitor"></a> 資料庫鏡像監視器顯示的狀態  
 [資料庫鏡像監視器] 的 **[狀態]** 頁面會描述夥伴，還有鏡像工作階段的狀態。 狀態會包括效能標準，如交易記錄狀態和其他資訊，目的是要在工作階段沒有同步時，協助您估計目前完成容錯移轉所需的時間以及遺失資料的可能性。 此外， **[狀態]** 頁面還會顯示鏡像工作階段的一般狀態和相關資訊。  
  
> [!NOTE]  
>  如需「資料庫鏡像監視器」和 [狀態]  頁面的簡介，請參閱本主題稍早的 [監視資料庫鏡像狀態的工具](#tools_for_monitoring_dbm_status)。  
  
 針對這其中每一項提供的資料都摘要在下列章節中。  
  
#### <a name="partners"></a>合作夥伴  
 **[狀態]** 頁面會顯示每個夥伴的下列資訊：  
  
-   伺服器執行個體  
  
     其狀態顯示在 **[狀態]** 資料列中的伺服器執行個體名稱。  
  
-   目前的角色  
  
     伺服器執行個體的目前角色。 可能的狀態為：  
  
    -   主體  
  
    -   鏡像  
  
-   鏡像狀態  
  
     可能的狀態為：  
  
    -   Unknown  
  
    -   正在同步處理  
  
    -   已同步處理  
  
    -   暫止  
  
    -   已中斷連接  
  
-   見證連接  
  
     見證的連接狀態。 可能的狀態為：  
  
    -   Unknown  
  
    -   連線  
  
    -   已中斷連接  
  
#### <a name="log-on-the-principal-server"></a>主體伺服器上的記錄  
 **[狀態]** 頁面會根據指定的時間，顯示下列有關主體伺服器之記錄狀態的資訊：  
  
-   未傳送的記錄  
  
     在傳送佇列中等候的記錄量 (以 KB 為單位)。  
  
-   最舊尚未傳送的交易  
  
     傳送佇列中最舊尚未傳送之交易的存在時間。 這項交易的存在時間會指出尚未傳送到鏡像伺服器執行個體的交易分鐘數。 這個值有助於從時間方面來測量資料遺失的可能性。  
  
-   傳送記錄的時間 (估計)  
  
     根據目前的傳送速率，主體伺服器執行個體將目前位於傳送佇列中的記錄傳送至鏡像伺服器執行個體所需的估計分鐘數。 傳送記錄的實際時間將會受到內送交易的速率影響，所以可能會有很大的差異。 不過，在概略估計手動容錯移轉所需的時間時，[傳送記錄的時間 (估計)]  值非常有用。  
  
-   目前的傳送速率  
  
     將交易傳送至鏡像伺服器執行個體的速率 (以每秒 KB 數為單位)。  
  
-   新交易的目前速率  
  
     將內送交易輸入到主體記錄中的速率 (以每秒 KB 數為單位)。 若要判斷鏡像是落後、超前，還是趕上進度，請將這個值與 **[傳送記錄的時間 (估計)]** 值加以比較。  
  
#### <a name="log-on-the-mirror-server"></a>鏡像伺服器上的記錄  
 **[狀態]** 頁面會根據指定的時間，顯示下列有關鏡像伺服器之記錄狀態的資訊：  
  
-   未還原的記錄  
  
     在重做佇列中等候的記錄量 (以 KB 為單位)。  
  
-   還原記錄的時間 (估計)  
  
     將目前在重做佇列中的記錄套用至鏡像資料庫所需的大約分鐘數。  
  
-   目前的還原速率  
  
     將交易還原到鏡像資料庫中的速率 (以每秒 KB 數為單位)。  
  
#### <a name="mirroring-session"></a>鏡像工作階段  
 此外， **[狀態]** 頁面還會顯示下列與鏡像工作階段相關的資訊：  
  
-   鏡像認可負擔  
  
     每筆交易的平均延遲時間 (以毫秒為單位) (僅適用於高安全性模式)。 這項延遲是當主體伺服器執行個體等待鏡像伺服器執行個體將交易記錄寫入重做佇列中時所產生的負擔量。  
  
-   傳送與還原所有目前記錄的時間 (估計)  
  
     傳送主體上已經認可的所有未傳送記錄並還原目前位於重做佇列中的所有記錄所需的估計時間。 此估計時間可能會比 [傳送記錄的時間 (估計)]  和 [還原記錄的時間 (估計)]  欄位值的總和要少，因為傳送和還原可以平行運作。  
  
-   見證位址  
  
     見證伺服器執行個體的網路位址。 如需此位址格式的資訊，請參閱[指定伺服器網路位址 &#40;資料庫鏡像&#41;](../../database-engine/database-mirroring/specify-a-server-network-address-database-mirroring.md)。  
  
-   作業模式  
  
     資料庫鏡像工作階段的作業模式：  
  
    -   高效能 (非同步)  
  
    -   不具有自動容錯移轉的高安全性 (同步)  
  
    -   具有自動容錯移轉的高安全性 (同步)  
  
##  <a name="additional-sources-of-information-about-a-mirrored-database"></a><a name="AdditionalSources"></a> 與鏡像資料庫相關的其他資訊來源  
 除了使用 [資料庫鏡像監視器] 和 dbmmonitor 預存程序來監視鏡像資料庫並設定監視效能變數的警示以外， [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 還提供目錄檢視、效能計數器及資料庫鏡像事件通知。  
  
 **本節內容：**  
  
-   [資料庫鏡像中繼資料](#DbmMetadata)  
  
-   [資料庫鏡像效能計數器](#DbmPerfCounters)  
  
-   [資料庫鏡像事件通知](#DbmEventNotif)  
  
###  <a name="database-mirroring-metadata"></a><a name="DbmMetadata"></a> 資料庫鏡像中繼資料  
 每個資料庫鏡像工作階段都會在中繼資料中描述，並可透過下列目錄或動態管理檢視公開此中繼資料：  
  
-   **sys.database_mirroring**  
  
     這個檢視會顯示伺服器執行個體中，每個鏡像資料庫的資料庫鏡像中繼資料。 如需詳細資訊，請參閱 [sys.database_mirroring &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-database-mirroring-transact-sql.md)。  
  
-   **sys.database_mirroring_endpoints**  
  
     **Sys.database_mirroring_endpoints** 目錄檢視會顯示伺服器執行個體之資料庫鏡像端點的相關資訊。 如需詳細資訊，請參閱 [sys.database_mirroring_endpoints &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-database-mirroring-endpoints-transact-sql.md)。  
  
-   **sys.database_mirroring_witnesses**  
  
     這個目錄檢視會針對伺服器執行個體是見證的每一個工作階段，顯示其資料庫鏡像中繼資料。 如需詳細資訊，請參閱 [sys.database_mirroring_witnesses &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/database-mirroring-witness-catalog-views-sys-database-mirroring-witnesses.md)。  
  
-   **sys.dm_db_mirroring_connections**  
  
     此動態管理檢視會針對每一個資料庫鏡像網路連接，傳回一個資料列。  
  
     如需詳細資訊，請參閱 [sys.dm_db_mirroring_connections &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/database-mirroring-sys-dm-db-mirroring-connections.md)。  
  
###  <a name="database-mirroring-performance-counters"></a><a name="DbmPerfCounters"></a> 資料庫鏡像效能計數器  
 效能計數器可讓您監視資料庫鏡像效能。 例如，您可以檢查 **[Transaction Delay]** 計數器，以查看資料庫鏡像是否影響主體伺服器的效能；您可以檢查 **[Redo Queue]** 與 **[Log Send Queue]** 計數器，以查看鏡像資料庫是否跟得上主體資料庫。 您可以檢查 [Log Bytes Sent/sec]  計數器，以監視每秒傳送的記錄量。  
  
 在每個夥伴的「效能監視器」中，都可在資料庫鏡像效能物件 (**SQLServer:Database Mirroring**) 中使用效能計數器。 如需詳細資訊，請參閱 [SQL Server 的 Database Mirroring 物件](../../relational-databases/performance-monitor/sql-server-database-mirroring-object.md)。  
  
 **若要啟動效能監視器**  
  
-   [啟動系統監視器 &#40;Windows&#41;](../../relational-databases/performance/start-system-monitor-windows.md)  
  
###  <a name="database-mirroring-event-notifications"></a><a name="DbmEventNotif"></a> 資料庫鏡像事件通知  
 事件通知是特殊的資料庫物件類型。 事件通知是為了回應各種 Transact-SQL 資料定義語言 (DDL) 陳述式和 SQL 追蹤事件而執行，並將伺服器和資料庫事件的相關資訊傳送給 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 服務。  
  
 下列事件可用於資料庫鏡像：  
  
-   **Database Mirroring State Change** 事件類別  
  
     這表示鏡像資料庫之鏡像狀態變更的時間。 如需詳細資訊，請參閱 [Database Mirroring State Change Event Class](../../relational-databases/event-classes/database-mirroring-state-change-event-class.md)。  
  
-   **Audit Database Mirroring Login** 事件類別  
  
     這會報告與資料庫鏡像傳輸安全性相關的稽核訊息。 如需詳細資訊，請參閱 [Audit Database Mirroring Login Event Class](../../relational-databases/event-classes/audit-database-mirroring-login-event-class.md)。  
  
##  <a name="related-tasks"></a><a name="RelatedTasks"></a> 相關工作  
  
-   [使用鏡像效能標準的警告臨界值與警示 &#40;SQL Server&#41;](../../database-engine/database-mirroring/use-warning-thresholds-and-alerts-on-mirroring-performance-metrics-sql-server.md)  
  
-   [啟動資料庫鏡像監視器 &#40;SQL Server Management Studio&#41;](../../database-engine/database-mirroring/start-database-mirroring-monitor-sql-server-management-studio.md)  
  
-   [檢視鏡像資料庫的狀態 &#40;SQL Server Management Studio&#41;](../../database-engine/database-mirroring/view-the-state-of-a-mirrored-database-sql-server-management-studio.md)  
  
 **預存程序**  
  
-   [sp_dbmmonitoraddmonitoring &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-dbmmonitoraddmonitoring-transact-sql.md)  
  
-   [sp_dbmmonitorchangealert &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-dbmmonitorchangealert-transact-sql.md)  
  
-   [sp_dbmmonitorchangemonitoring &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-dbmmonitorchangemonitoring-transact-sql.md)  
  
-   [sp_dbmmonitordropalert &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-dbmmonitordropalert-transact-sql.md)  
  
-   [sp_dbmmonitordropmonitoring &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-dbmmonitordropmonitoring-transact-sql.md)  
  
-   [sp_dbmmonitorhelpalert &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-dbmmonitorhelpalert-transact-sql.md)  
  
-   [sp_dbmmonitorhelpmonitoring &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-dbmmonitorhelpmonitoring-transact-sql.md)  
  
-   [sp_dbmmonitorresults &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-dbmmonitorresults-transact-sql.md)  
  
-   [sp_dbmmonitorupdate &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-dbmmonitorupdate-transact-sql.md)  
  
## <a name="see-also"></a>另請參閱  
 [資料庫鏡像 &#40;SQL Server&#41;](../../database-engine/database-mirroring/database-mirroring-sql-server.md)   
 [伺服器事件的 WMI 提供者概念](../../relational-databases/wmi-provider-server-events/wmi-provider-for-server-events-concepts.md)  
  
  
