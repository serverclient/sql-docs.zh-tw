---
title: 記錄傳送和複寫 (SQL Server) | Microsoft Docs
description: 了解記錄傳送如何將交易記錄中對主要資料庫所做的每一個插入、更新或刪除，套用到次要資料庫上。
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.reviewer: ''
ms.technology: log-shipping
ms.topic: conceptual
helpviewer_keywords:
- replication [SQL Server], log shipping and
- log shipping [SQL Server], replication and
ms.assetid: 132bebfd-0206-4d23-829a-b38e5ed17bc9
author: cawrites
ms.author: chadam
ms.openlocfilehash: cb1a0e9950e272a7c05024f0b12c48d5d6ae0838
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97643894"
---
# <a name="log-shipping-and-replication-sql-server"></a>記錄傳送和複寫 (SQL Server)
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
  記錄傳送牽涉的對象，通常和不同電腦上各自儲存的單一資料庫複本有關。 在任何時間內，目前的用戶端都只能使用其中一份資料庫副本， 此份資料庫稱為主要資料庫。 用戶端對主要資料庫所做的更新，會透過記錄傳送方式傳播到其他資料庫複本 (亦稱為次要資料庫)。 記錄傳送會將交易記錄中對主要資料庫所做的每一項插入、更新或刪除，套用到次要資料庫上。  
  
 記錄傳送可與複寫一起使用，且具有下列行為：  
  
-   在記錄傳送容錯移轉之後，複寫將不會繼續。 如果發生容錯移轉，複寫代理程式不會連接到次要伺服器，因此交易不會複寫至「訂閱者」。 如果發生錯誤後回復至主要伺服器，則複寫便會繼續。 由記錄傳送從次要伺服器複製回主要伺服器的所有交易，都會複寫至「訂閱者」。  
  
-   如果主要伺服器永久遺失，則可以重新命名次要伺服器，以讓複寫能夠繼續。 此主題的其餘部分描述的是處理此情況的需求和程序。 此處提供的範例為發行集資料庫，這是最常進行記錄傳送的資料庫，不過類似的程序也可以套用至訂閱資料庫和散發資料庫。  
  
 如需復原與複寫相關的資料庫，而不需要重新設定複寫的資訊，請參閱 [備份及還原複寫的資料庫](../../relational-databases/replication/administration/back-up-and-restore-replicated-databases.md)。  
  
> [!NOTE]  
>  我們建議使用資料庫鏡像，而不要使用記錄傳送，以便於使用發行集資料庫。 如需詳細資訊，請參閱 [資料庫鏡像和複寫 &#40;SQL Server&#41;](../../database-engine/database-mirroring/database-mirroring-and-replication-sql-server.md)。  
  
## <a name="requirements-and-procedures-for-replicating-from-the-secondary-if-the-primary-is-lost"></a>主要伺服器遺失時，從次要伺服器複寫的需求和程序  
 請注意下列需求和考量：  
  
-   如果主要伺服器包含一個以上的發行集資料庫，請將所有發行集資料庫記錄傳送至相同的次要伺服器。  
  
-   次要伺服器執行個體的安裝路徑必須與主要伺服器的安裝路徑相同， 且次要伺服器上的使用者資料庫位置也必須與主要伺服器上的位置相同。  
  
-   在主要伺服器端備份服務主要金鑰， 此金鑰將在次要伺服器端還原。 如需詳細資訊，請參閱 [BACKUP SERVICE MASTER KEY &#40;Transact-SQL&#41;](../../t-sql/statements/backup-service-master-key-transact-sql.md)。  
  
-   記錄傳送不保證資料不會遺失。 主要資料庫上若是失敗，可能導致尚未備份的資料遺失，或是在失敗期間遺失備份。  
  
### <a name="log-shipping-with-transactional-replication"></a>記錄傳送與異動複寫  
 對於異動複寫，記錄傳送的行為取決於 **sync with backup** 選項。 此選項可以在發行集資料庫和散發資料庫上設定；不過在「發行者」的記錄傳送中，只與發行集資料庫上的設定相關。  
  
 在發行集資料庫上設定此選項，可確保交易在發行集資料庫中備份之前，不會傳遞到散發資料庫。 如此一來，最近一次的發行集資料庫備份便可以在次要伺服器端還原，而不會發生散發資料庫中，存有發行集資料庫中所沒有的交易這種情況。 這個選項可保證當「發行者」容錯移轉至次要伺服器時，仍可維持「發行者」、「散發者」和「訂閱者」之間的一致性。 延遲和輸送量會受到影響，因為交易在發行者端備份之前，不能傳遞到散發資料庫。如果您的應用程式允許此延遲，則建議您在發行集資料庫上設定此選項。 如果沒有設定 **sync with backup** 選項，則「訂閱者」可能會收到不再包含於次要伺服器端已復原資料庫中的變更。 如需詳細資訊，請參閱 [備份與還原快照式和異動複寫的策略](../../relational-databases/replication/administration/strategies-for-backing-up-and-restoring-snapshot-and-transactional-replication.md)。  
  
 **若要設定使用 sync with backup 選項的異動複寫和記錄傳送**  
  
1.  如果未在發行集資料庫上設定 sync with backup 選項，請執行 `sp_replicationdboption '<publicationdatabasename>', 'sync with backup', 'true'`。 如需詳細資訊，請參閱 [sp_replicationdboption &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-replicationdboption-transact-sql.md)。  
  
2.  設定發行集資料庫的記錄傳送。 如需詳細資訊，請參閱[設定記錄傳送 &#40;SQL Server&#41;](../../database-engine/log-shipping/configure-log-shipping-sql-server.md)。  
  
3.  如果「發行者」失敗，請使用 RESTORE LOG 的 KEEP_REPLICATION 選項，將資料庫的最後記錄還原至次要伺服器， 這會保留資料庫的所有複寫設定。 如需詳細資訊，請參閱[容錯移轉至記錄傳送次要 &#40;SQL Server&#41;](../../database-engine/log-shipping/fail-over-to-a-log-shipping-secondary-sql-server.md) 和 [RESTORE &#40;Transact-SQL&#41;](../../t-sql/statements/restore-statements-transact-sql.md)。  
  
4.  將 **msdb** 資料庫和 **master** 資料庫從主要伺服器還原至次要伺服器。 如需詳細資訊，請參閱[系統資料庫的備份與還原 &#40;SQL Server&#41;](../../relational-databases/backup-restore/back-up-and-restore-of-system-databases-sql-server.md)。 如果主要伺服器也是「散發者」，請將散發資料庫從主要伺服器還原至次要伺服器。  
  
     這些資料庫在複寫組態和設定方面，必須與主要伺服器端的發行集資料庫一致。  
  
5.  在次要伺服器上，重新命名電腦，然後重新命名 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體，以符合主要伺服器名稱。 如需有關重新命名電腦的詳細資訊，請參閱 Windows 文件集。 如需重新命名伺服器的資訊，請參閱 [重新命名主控 SQL Server 獨立執行個體的電腦](../../database-engine/install-windows/rename-a-computer-that-hosts-a-stand-alone-instance-of-sql-server.md) 和 [重新命名 SQL Server 容錯移轉叢集執行個體](../../sql-server/failover-clusters/install/rename-a-sql-server-failover-cluster-instance.md)。  
  
6.  在次要伺服器上，還原從主要伺服器所備份的服務主要金鑰。 如需詳細資訊，請參閱 [RESTORE SERVICE MASTER KEY &#40;Transact-SQL&#41;](../../t-sql/statements/restore-service-master-key-transact-sql.md)。  
  
 **若要設定異動複寫和記錄傳送，而不使用 sync with backup 選項**  
  
1.  設定發行集資料庫的記錄傳送。 如需詳細資訊，請參閱[設定記錄傳送 &#40;SQL Server&#41;](../../database-engine/log-shipping/configure-log-shipping-sql-server.md)。  
  
2.  如果「發行者」失敗，請使用 RESTORE LOG 的 KEEP_REPLICATION 選項，將資料庫的最後記錄還原至次要伺服器， 這會保留資料庫的所有複寫設定。 如需詳細資訊，請參閱[容錯移轉至記錄傳送次要 &#40;SQL Server&#41;](../../database-engine/log-shipping/fail-over-to-a-log-shipping-secondary-sql-server.md) 和 [RESTORE &#40;Transact-SQL&#41;](../../t-sql/statements/restore-statements-transact-sql.md)。  
  
3.  將 **msdb** 資料庫和 **master** 資料庫從主要伺服器還原至次要伺服器。 如需詳細資訊，請參閱[系統資料庫的備份與還原 &#40;SQL Server&#41;](../../relational-databases/backup-restore/back-up-and-restore-of-system-databases-sql-server.md)。 如果主要伺服器也是「散發者」，請將散發資料庫從主要伺服器還原至次要伺服器。  
  
     這些資料庫在複寫組態和設定方面，必須與主要伺服器端的發行集資料庫一致。  
  
4.  在次要伺服器上，重新命名電腦，然後重新命名 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體，以符合主要伺服器名稱。 如需有關重新命名電腦的詳細資訊，請參閱 Windows 文件集。 如需重新命名伺服器的資訊，請參閱 [重新命名主控 SQL Server 獨立執行個體的電腦](../../database-engine/install-windows/rename-a-computer-that-hosts-a-stand-alone-instance-of-sql-server.md) 和 [重新命名 SQL Server 容錯移轉叢集執行個體](../../sql-server/failover-clusters/install/rename-a-sql-server-failover-cluster-instance.md)。  
  
     您可能會收到來自「記錄讀取器代理程式」的錯誤訊息，指出發行集資料庫和散發資料庫並未同步處理。  
  
5.  在次要伺服器上，還原從主要伺服器所備份的服務主要金鑰。 如需詳細資訊，請參閱 [RESTORE SERVICE MASTER KEY &#40;Transact-SQL&#41;](../../t-sql/statements/restore-service-master-key-transact-sql.md)。  
  
6.  執行 **sp_replrestart**。 使用這個預存程序，可以強制「記錄讀取器代理程式」忽略發行集資料庫記錄中所有先前複寫的交易。 在預存程序完成之後所套用的交易，會由「記錄讀取器代理程式」來處理。 如需詳細資訊，請參閱 [sp_replrestart &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-replrestart-transact-sql.md)。  
  
7.  在預存程序成功執行之後重新啟動「記錄讀取器代理程式」。 如需詳細資訊，請參閱[啟動和停止複寫代理程式 &#40;SQL Server Management Studio&#41;](../../relational-databases/replication/agents/start-and-stop-a-replication-agent-sql-server-management-studio.md)。  
  
8.  已經散發至訂閱者的交易可能會在發行者端套用。 為了確保「散發代理程式」嘗試在訂閱者端重新套用這些交易時，不會因錯誤而導致失敗，請指定標題為 **資料一致性錯誤時仍然繼續** 的代理程式設定檔。  
  
### <a name="log-shipping-with-merge-replication"></a>記錄傳送與合併式複寫  
 請遵循下列程序中的步驟，設定合併式複寫和記錄傳送。  
  
 **若要設定合併式複寫和記錄傳送**  
  
1.  設定發行集資料庫的記錄傳送。 如需詳細資訊，請參閱[設定記錄傳送 &#40;SQL Server&#41;](../../database-engine/log-shipping/configure-log-shipping-sql-server.md)。  
  
2.  如果發行者失敗，請在次要伺服器上重新命名電腦，然後重新命名 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體，以符合主要伺服器名稱。 如需有關重新命名電腦的詳細資訊，請參閱 Windows 文件集。 如需重新命名伺服器的資訊，請參閱 [重新命名主控 SQL Server 獨立執行個體的電腦](../../database-engine/install-windows/rename-a-computer-that-hosts-a-stand-alone-instance-of-sql-server.md) 和 [重新命名 SQL Server 容錯移轉叢集執行個體](../../sql-server/failover-clusters/install/rename-a-sql-server-failover-cluster-instance.md)。  
  
3.  請使用 RESTORE LOG 的 KEEP_REPLICATION 選項，將資料庫的最後記錄還原至次要伺服器， 這會保留資料庫的所有複寫設定。 如需詳細資訊，請參閱[容錯移轉至記錄傳送次要 &#40;SQL Server&#41;](../../database-engine/log-shipping/fail-over-to-a-log-shipping-secondary-sql-server.md) 和 [RESTORE &#40;Transact-SQL&#41;](../../t-sql/statements/restore-statements-transact-sql.md)。  
  
4.  將 **msdb** 資料庫和 **master** 資料庫從主要伺服器還原至次要伺服器。 如需詳細資訊，請參閱[系統資料庫的備份與還原 &#40;SQL Server&#41;](../../relational-databases/backup-restore/back-up-and-restore-of-system-databases-sql-server.md)。 如果主要伺服器也是「散發者」，請將散發資料庫從主要伺服器還原至次要伺服器。  
  
     這些資料庫在複寫組態和設定方面，必須與主要伺服器端的發行集資料庫一致。  
  
5.  在次要伺服器上，還原從主要伺服器所備份的服務主要金鑰。 如需詳細資訊，請參閱 [RESTORE SERVICE MASTER KEY &#40;Transact-SQL&#41;](../../t-sql/statements/restore-service-master-key-transact-sql.md)。  
  
6.  同步處理發行集資料庫與一或多個訂閱資料庫。 這可讓您上傳先前於發行集資料庫中所執行、但是未在還原的備份中呈現的變更。 可以上傳的資料視發行集的篩選方式而定：  
  
    -   如果發行集未篩選，您可以透過與最新的「訂閱者」進行同步處理，使發行集資料庫處於最新狀態。  
  
    -   如果發行集已篩選，則可能無法使發行集資料庫處於最新狀態。 請思考一下一個已進行資料分割的資料表，其中資料分割方式使得每個訂閱只會收到下列單一地區的客戶資料：北區、東區、南區及西區。 如果每個資料分割至少有一個「訂閱者」，則與每個資料分割的「訂閱者」進行同步處理就可使發行集資料庫處於最新狀態。 不過，如果在「西區」資料分割中的資料未複寫到任何「訂閱者」(舉例來說)，則「發行者」端的此資料將無法處於最新狀態。 在此情況下，建議您重新初始化所有訂閱，以讓發行者端和訂閱者端的資料聚合。 如需詳細資訊，請參閱 [重新初始化訂閱](../../relational-databases/replication/reinitialize-subscriptions.md)。  
  
     如果同步處理的「訂閱者」所執行的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 版本早於 [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)]，則訂閱不可是匿名的，而必須是客訂閱或主訂閱 (在之前的版本中稱為本機訂閱與全域訂閱)。 如需詳細資訊，請參閱 [同步處理資料](../../relational-databases/replication/synchronize-data.md)。  
  
## <a name="see-also"></a>另請參閱  
 [SQL Server 複寫](../../relational-databases/replication/sql-server-replication.md)   
 [關於記錄傳送 &#40;SQL Server&#41;](../../database-engine/log-shipping/about-log-shipping-sql-server.md)   
 [資料庫鏡像和複寫 &#40;SQL Server&#41;](../../database-engine/database-mirroring/database-mirroring-and-replication-sql-server.md)  
  
  
