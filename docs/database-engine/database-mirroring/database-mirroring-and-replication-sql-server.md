---
title: 資料庫鏡像和複寫 (SQL Server) | Microsoft Docs
description: 了解如何在 SQL Server 中搭配複寫使用資料庫鏡像，以改進發行集資料庫的可用性。
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: high-availability
ms.reviewer: ''
ms.technology: database-mirroring
ms.topic: conceptual
helpviewer_keywords:
- database mirroring [SQL Server], interoperability
- replication [SQL Server], database mirroring and
ms.assetid: 82796217-02e2-4bc5-9ab5-218bae11a2d6
author: MikeRayMSFT
ms.author: mikeray
ms.openlocfilehash: 998e1d6525613e121377abbe17861ecb86bf86f6
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97642046"
---
# <a name="database-mirroring-and-replication-sql-server"></a>資料庫鏡像和複寫 (SQL Server)
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
  資料庫鏡像可以和複寫一起使用，以改進發行集資料庫的可用性。 資料庫鏡像是指單一資料庫的兩份副本，而且通常位在不同的電腦上。 在任何時間內，目前的用戶端都只能使用其中一份資料庫副本， 此份資料庫稱為主體資料庫。 用戶端對主體資料庫所做的更新會套用到其他份資料庫，也稱為鏡像資料庫。 鏡像作業涵蓋了針對主體資料庫上所進行的每個插入、更新或刪除動作，將其交易記錄套用至鏡像資料庫。  
  
 發行集資料庫完全支援複寫容錯移轉到鏡像，而散發資料庫則有限支援此功能。 散發資料庫不支援資料庫鏡像。 如需復原散發資料庫或訂閱資料庫而不需要重新設定複寫的詳細資訊，請參閱 [備份及還原複寫的資料庫](../../relational-databases/replication/administration/back-up-and-restore-replicated-databases.md)。   
  
> [!NOTE]  
>  在容錯移轉之後，鏡像會成為主體。 在此主題中，「主體」和「鏡像」均是指原始的主體和鏡像。  
  
## <a name="requirements-and-considerations-for-using-replication-with-database-mirroring"></a>使用複寫和資料庫鏡像的需求和考量  
 使用複寫和資料庫鏡像時，請注意下列需求和考量：  
  
-   主體和鏡像必須共用「散發者」。 建議您使用遠端「散發者」，以在「發行者」發生未規劃的容錯移轉時提供更大的容錯能力。  
  
-   在合併式複寫以及使用唯讀「訂閱者」或佇列更新「訂閱者」的異動複寫中，複寫可支援發行集資料庫的鏡像。 不支援立即更新「訂閱者」、「Oracle 發行者」、點對點拓撲中的「發行者」以及重新發行。  
  
-   存在於資料庫之外的中繼資料和物件不會複製到鏡像，包括登入、作業、連結的伺服器等等。 如果您需要在鏡像端使用這些中繼資料和物件，則必須手動加以複製。 如需詳細資訊，請參閱[角色切換後針對登入和作業進行管理 &#40;SQL Server&#41;](../../sql-server/failover-clusters/management-of-logins-and-jobs-after-role-switching-sql-server.md)。  
  
## <a name="configuring-replication-with-database-mirroring"></a>設定複寫和資料庫鏡像  
 設定複寫和資料庫鏡像包含五個步驟。 每個步驟會在下一節中更詳細地說明。  
  
1.  設定「發行者」。  
  
2.  設定資料庫鏡像。  
  
3.  將鏡像設定為使用與主體相同的「散發者」。  
  
4.  設定用於容錯移轉的複寫代理程式。  
  
5.  將主體和鏡像加入「複寫監視器」。  
  
 步驟 1 和 2 的執行順序可以對調。  
  
#### <a name="to-configure-database-mirroring-for-a-publication-database"></a>設定發行集資料庫的資料庫鏡像  
  
1.  設定「發行者」：  
  
    1.  建議您使用遠端「散發者」。 如需設定散發的詳細資訊，請參閱 [設定散發](../../relational-databases/replication/configure-distribution.md)。  
  
    2.  您可為快照式與交易式發行集和 (或) 合併式發行集啟用資料庫。 如果是將包含超過一種類型之發行集的鏡像資料庫，您必須使用 [sp_replicationdboption](../../relational-databases/system-stored-procedures/sp-replicationdboption-transact-sql.md)，在相同節點上為這兩種類型啟用資料庫。 例如，您可以在主體端執行下列預存程序呼叫：  
  
        ```  
        exec sp_replicationdboption @dbname='<PublicationDatabase>', @optname='publish', @value=true;  
        exec sp_replicationdboption @dbname='<PublicationDatabase>', @optname='mergepublish', @value=true;  
        ```  
  
         如需建立發行集的詳細資訊，請參閱 [發行資料和資料庫物件](../../relational-databases/replication/publish/publish-data-and-database-objects.md)。  
  
2.  設定資料庫鏡像。 如需詳細資訊，請參閱[使用 Windows 驗證建立資料庫鏡像工作階段 &#40;SQL Server Management Studio&#41;](../../database-engine/database-mirroring/establish-database-mirroring-session-windows-authentication.md) 和[設定資料庫鏡像 &#40;SQL Server&#41;](../../database-engine/database-mirroring/setting-up-database-mirroring-sql-server.md)。  
  
3.  為鏡像設定散發。 將鏡像名稱指定為「發行者」，並指定主體所用的相同「散發者」和快照集資料夾。 例如，如果您以預存程序設定複寫，請在「散發者」端執行 [sp_adddistpublisher](../../relational-databases/system-stored-procedures/sp-adddistpublisher-transact-sql.md) ，然後在鏡像上執行 [sp_adddistributor](../../relational-databases/system-stored-procedures/sp-adddistributor-transact-sql.md) 。 針對 **sp_adddistpublisher**：  
  
    -   將 **\@publisher** 參數值設為鏡像的網路名稱。  
  
    -   將 **\@working_directory** 參數值設為主體所使用的快照集資料夾。  
  
4.  指定 **-PublisherFailoverPartner** 代理程式參數的鏡像名稱。 下列代理程式需要使用這個參數在容錯移轉後識別鏡像：  
  
    -   快照集代理程式 (針對所有發行集)。  
  
    -   記錄讀取器代理程式 (針對所有交易式發行集)  
  
    -   佇列讀取器代理程式 (針對支援佇列更新訂閱的交易式發行集)  
  
    -   合併代理程式 (針對合併訂閱)  
  
    -   [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Replication Listener (replisapi.dll：適用於使用 Web 同步處理來進行同步處理的合併訂閱)  
  
    -   SQL Merge ActiveX Control (針對與控制項同步處理的合併訂閱)  
  
     「散發代理程式」和 Distribution ActiveX Control 沒有這個參數，因為它們並未連接到「發行者」。  
  
     代理程式參數變更會在代理程式下次啟動時生效。 如果代理程式連續執行，則必須停止代理程式，然後重新啟動它。 您可以在代理程式設定檔中或從命令提示字元指定參數。 如需詳細資訊，請參閱  
  
    -   [檢視並修改複寫代理程式命令提示字元參數 &#40;SQL Server Management Studio&#41;](../../relational-databases/replication/agents/view-and-modify-replication-agent-command-prompt-parameters.md)  
  
    -   [Replication Agent Executables Concepts](../../relational-databases/replication/concepts/replication-agent-executables-concepts.md)  
  
     建議您將 **-PublisherFailoverPartner** 加入代理程式設定檔，然後在設定檔中指定鏡像名稱。 例如，如果您要設定使用預存程序的複寫：  
  
    ```  
    -- Execute sp_help_agent_profile in the context of the distribution database to get the list of profiles.  
    -- Select the profile id of the profile that needs to be updated from the result set.  
    -- In the agent_type column returned by sp_help_agent_profile:   
    -- 1 = Snapshot Agent; 2 = Log Reader Agent; 3 = Distribution Agent; 4 = Merge Agent; 9 = Queue Reader Agent.  
  
    exec sp_help_agent_profile;  
  
    -- Setting the -PublisherFailoverPartner parameter in the default Snapshot Agent profile (profile 1).  
    -- Execute sp_add_agent_parameter in the context of the distribution database.  
    exec sp_add_agent_parameter @profile_id = 1, @parameter_name = N'-PublisherFailoverPartner', @parameter_value = N'<Failover Partner Name>';  
  
    -- Setting the -PublisherFailoverPartner parameter in the default Merge Agent profile (profile 6).  
    -- Execute sp_add_agent_parameter in the context of the distribution database.  
    exec sp_add_agent_parameter @profile_id = 6, @parameter_name = N'-PublisherFailoverPartner', @parameter_value = N'<Failover Partner Name>';  
    ```  
  
5.  將主體和鏡像加入「複寫監視器」。 如需詳細資訊，請參閱 [從複寫監視器加入及移除發行者](../../relational-databases/replication/monitor/add-and-remove-publishers-from-replication-monitor.md)。  
  
## <a name="maintaining-a-mirrored-publication-database"></a>維護鏡像發行集資料庫  
 維護鏡像發行集資料庫基本上與維護非鏡像資料庫相同，不過請注意下列事項：  
  
-   管理和監視必須在使用中伺服器端發生。 在 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 中，發行集只會出現在使用中伺服器的 [本機發行集] 資料夾下。 例如，如果您容錯移轉至鏡像，則發行集會在鏡像端顯示，而不會再顯示於主體端。 如果資料庫容錯移轉至鏡像，您可能需要手動重新整理 [!INCLUDE[ssManStudio](../../includes/ssmanstudio-md.md)] 和複寫監視器，才能反映出變更。  
  
-   複寫監視器會同時在主體和鏡像的物件樹中顯示「發行者」節點。 如果主體為使用中伺服器，則發行集資訊只會顯示在「複寫監視器」的主體節點下。  
  
     如果鏡像是使用中伺服器：  
  
    -   如果代理程式發生錯誤，錯誤只會在主體節點上指出，在鏡像節點上則不會。  
  
    -   如果主體無法使用，則主體和鏡像節點會顯示相同的發行集清單。 監視應在鏡像節點下的發行集上執行。  
  
-   當使用預存程序或 Replication Management Objects (RMO) 在鏡像端管理複寫時，在您指定「發行者」名稱的情況下，您必須指定在其上啟用資料庫以供複寫的執行個體名稱。 若要決定適當的名稱，請使用 [publishingservername](../../t-sql/functions/replication-functions-publishingservername.md)函數。  
  
     在完成發行集資料庫的鏡像後，儲存在鏡像資料庫中的複寫中繼資料會與儲存在主體資料庫中的中繼資料相同。 因此，對於在主體端啟用以供複寫的發行集資料庫而言，儲存在鏡像端系統資料表中的「發行者」執行個體名稱是主體的名稱，而不是鏡像的名稱。 如果發行集資料庫容錯移轉至鏡像，這會影響複寫組態和維護。 例如，如果您要在容錯移轉後於鏡像端設定使用預存程序的複寫，且您想要將提取訂閱新增到在主體端啟用的發行集資料庫，則必須為 **sp_addpullsubscription** 或 **sp_addmergepullsubscription** 的 **\@publisher** 參數指定主體名稱，而非鏡像名稱。  
  
     如果您在容錯移轉至鏡像後於鏡像端啟用發行集資料庫，則儲存在系統資料表中的「發行者」執行個體名稱是鏡像的名稱；在此情況下，您應該為 **\@publisher** 參數指定鏡像的名稱。  
  
    > [!NOTE]  
    >  在某些情況下 (例如 **sp_addpublication**)，只有非 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 發行者才支援 **\@publisher** 參數；此時，該參數便與 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 資料庫鏡像不相關。  
  
-   若要在容錯移轉後同步處理 [!INCLUDE[ssManStudio](../../includes/ssmanstudio-md.md)] 中的訂閱，請從「訂閱者」同步處理提取訂閱，並從使用中「發行者」同步處理發送訂閱。  
  
### <a name="replication-behavior-if-mirroring-is-removed"></a>移除鏡像時的複寫行為  
 從發行的資料庫移除資料庫鏡像時，請注意下列幾個問題：  
  
-   如果已不再鏡像主體端的發行集資料庫，則複寫作業仍會對原始主體維持相同的運作。  
  
-   如果發行集資料庫從主體容錯移轉至鏡像，且鏡像關聯性隨後被停用或移除，則複寫代理程式將不會對鏡像發生作用。 如果主體永久遺失，請停用複寫，然後以指定為「發行者」的鏡像重新設定複寫。  
  
-   如果完全移除資料庫鏡像，則鏡像資料庫會處於復原狀態，且必須還原該資料庫才能使其正常運作。 復原的資料庫在複寫方面的行為取決於是否已指定 KEEP_REPLICATION 選項。 當還原發行的資料庫至某伺服器而非建立備份的伺服器時，此選項會強制還原作業保留複寫設定。 只有在無法使用其他發行集資料庫時，才應該使用 KEEP_REPLICATION 選項。 如果其他發行集資料庫仍然完整且複寫中，則不支援此選項。 如需 KEEP_REPLICATION 的詳細資訊，請參閱 [RESTORE &#40;Transact-SQL&#41;](../../t-sql/statements/restore-statements-transact-sql.md)。  
  
## <a name="log-reader-agent-behavior"></a>記錄讀取器代理程式行為  
 下表描述資料庫鏡像的各種作業模式之「記錄讀取器代理程式」行為。  
  
|作業模式|鏡像無法使用時的記錄讀取器代理程式行為|  
|--------------------|------------------------------------------------------------|  
|具有自動容錯移轉的高安全性模式|如果鏡像無法使用，則「記錄讀取器代理程式」會傳播命令至散發資料庫。 待鏡像回復連接，且具備主體的所有交易後，主體才能容錯移轉至鏡像。|  
|高效能模式|如果鏡像無法使用，則主體資料庫會以公開方式執行 (也就是沒有鏡像)。 不過，「記錄讀取器代理程式」只會複寫鏡像上所儲存的交易。 如果強制執行服務，且鏡像伺服器承擔主體的角色，則「記錄讀取器代理程式」會對鏡像發生作用，並開始收取新的交易。<br /><br /> 請注意，如果鏡像落後主體，則複寫延遲將會增加。|  
|不具有自動容錯移轉的高安全性模式|所有經過認可的交易一定會存到鏡像的磁碟上。 「記錄讀取器代理程式」只會複寫在鏡像上強化的交易。 如果鏡像無法使用，則主體不會允許在資料庫上有進一步的活動；因此，「記錄讀取器代理程式」便沒有交易可複寫。|  
  
## <a name="see-also"></a>另請參閱  
 [SQL Server 複寫](../../relational-databases/replication/sql-server-replication.md)   
 [記錄傳送和複寫 &#40;SQL Server&#41;](../../database-engine/log-shipping/log-shipping-and-replication-sql-server.md)  
  
  
