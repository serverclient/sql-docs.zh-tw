---
title: 資料庫檢查點 (SQL Server) | Microsoft Docs
description: 了解檢查點，這是 SQL Server 資料庫引擎可開始在復原期間套用記錄檔中所包含變更的已知良好點。
ms.date: 04/23/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.custom: ''
ms.technology: supportability
ms.topic: conceptual
helpviewer_keywords:
- automatic checkpoints
- transaction logs [SQL Server], checkpoints
- logs [SQL Server], active
- pages [SQL Server], dirty
- MinLSN
- checkpoints [SQL Server]
- pages [SQL Server], flushing
- dirty pages
- transaction logs [SQL Server], active logs
- recovery interval option [SQL Server]
- buffer cache [SQL Server]
- logs [SQL Server], checkpoints
- Minimum Recovery LSN
- flushing pages
- active logs
ms.assetid: 98a80238-7409-4708-8a7d-5defd9957185
author: MashaMSFT
ms.author: mathoma
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: c98d84c8e3bc08bfae13c149cfc0487c57e40008
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98171570"
---
# <a name="database-checkpoints-sql-server"></a>資料庫檢查點 (SQL Server)
[!INCLUDE [SQL Server Azure SQL Database](../../includes/applies-to-version/sql-asdb.md)]
 *「檢查點」* (Checkpoint) 會建立一個已知的恰當起點， [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 可以從這個點開始套用發生非預期的關機或損毀之後，於復原期間包含在記錄檔中的變更。

##  <a name="overview"></a><a name="Overview"></a> 概觀   
基於效能的考量，[!INCLUDE[ssDE](../../includes/ssde-md.md)]會在緩衝區快取的記憶體內修改資料庫頁面，但並不會在每次變更之後都將這些頁面寫入磁碟中。 而是由 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 定期在每一個資料庫上發出檢查點。 「檢查點」會將目前記憶體內部已修改的頁面 (稱為「中途分頁」) 和交易記錄資訊從記憶體寫入至磁碟，也會在交易記錄中記錄該資訊。  
  
 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 支援幾種類型的檢查點：自動、間接、手動和內部。 下表彙總 **檢查點** 的類型：
  
|名稱|[!INCLUDE[tsql](../../includes/tsql-md.md)] 介面|描述|  
|----------|----------------------------------|-----------------|  
|自動|EXEC sp_configure **'** recovery interval **','** _seconds_ **'**|在背景自動發出，以符合 **recovery interval** 伺服器組態選項所建議的時間上限。 自動檢查點會執行到完成為止。  自動檢查點的調節是根據未完成的寫入數目以及 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 是否偵測到超過 50 毫秒的寫入延遲有增加。<br /><br /> 如需詳細資訊，請參閱 [Configure the recovery interval Server Configuration Option](../../database-engine/configure-windows/configure-the-recovery-interval-server-configuration-option.md)。|  
|間接|ALTER DATABASE ...SET TARGET_RECOVERY_TIME **=** _target\_recovery\_time_ { SECONDS &#124; MINUTES }|在背景發出，以符合使用者對給定資料庫所指定的目標復原時間。 從 [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)]開始，預設值為 1 分鐘。 舊版的預設值為 0，指示資料庫將使用自動檢查點，其頻率取決於伺服器執行個體的復原間隔設定。<br /><br /> 如需詳細資訊，請參閱 [變更資料庫的目標復原時間 &#40;SQL Server&#41;](../../relational-databases/logs/change-the-target-recovery-time-of-a-database-sql-server.md)伺服器組態選項。|  
|手動|CHECKPOINT [*checkpoint_duration*]|當您執行 [!INCLUDE[tsql](../../includes/tsql-md.md)] CHECKPOINT 命令時發出。 手動檢查點會發生在連接的目前資料庫中。 根據預設，手動檢查點會執行到完成為止。 調節的運作方式與自動檢查點相同。  *checkpoint_duration* 參數可選擇性地指定要求的時間量 (以秒為單位)，好讓檢查點得以完成。<br /><br /> 如需詳細資訊，請參閱 [CHECKPOINT &#40;Transact-SQL&#41;](../../t-sql/language-elements/checkpoint-transact-sql.md)。|  
|內部|無。|由各種伺服器作業 (例如備份和資料庫快照集建立) 發出，以保證磁碟映像符合目前的記錄檔狀態。|  
  
> [!NOTE]
> 資料庫管理員可使用 **-k** [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 進階安裝選項，根據某些檢查點類型的 I/O 子系統輸送量來調節檢查點 I/O 行為。 **-k** 安裝選項會套用到自動檢查點以及任何未調節的手動與內部檢查點。  
  
 如果是自動、手動和內部檢查點，只有在最新檢查點之後所做的修改才需要在資料庫復原期間向前復原。 這會減少復原資料庫所需的時間。  
  
> [!IMPORTANT]
> 長時間執行且未認可的交易會讓所有檢查點類型的復原時間增加。   
  
##  <a name="interaction-of-the-target_recovery_time-and-recovery-interval-options"></a><a name="InteractionBwnSettings"></a> TARGET_RECOVERY_TIME 和 'recovery interval' 選項的互動  
 下表摘要全伺服器 **sp_configure '** recovery interval **'** 設定與資料庫特定的 `ALTER DATABASE ... TARGET_RECOVERY_TIME` 設定間的互動。  
  
|target_recovery_time|'recovery interval'|使用的檢查點類型|  
|----------------------------|-------------------------|-----------------------------|  
|0|0|目標復原間隔為 1 分鐘的自動檢查點。|  
|0|>0|目標復原間隔是透過使用者定義之 **sp_configure 'recovery interval'** 選項設定來指定的自動檢查點。|  
|>0|不適用。|由 TARGET_RECOVERY_TIME 設定決定目標復原時間 (以秒鐘表示) 的間接檢查點。|  
  
##  <a name="automatic-checkpoints"></a><a name="AutomaticChkpt"></a> 自動檢查點  
每當記錄檔記錄數目到達 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 預估它在 **recovery interval** 伺服器組態選項指定的期間內可以處理的數目時，都會發生自動檢查點。 如需詳細資訊，請參閱 [Configure the recovery interval Server Configuration Option](../../database-engine/configure-windows/configure-the-recovery-interval-server-configuration-option.md)。
 
在沒有使用者定義之目標復原時間的每個資料庫中， [!INCLUDE[ssDE](../../includes/ssde-md.md)] 都會產生自動檢查點。 自動檢查點的頻率取決於 **recovery interval** 進階伺服器組態選項，該選項會指定給定伺服器執行個體在系統重新啟動期間應該用於復原資料庫的最長時間。 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 會預估它在復原間隔內可以處理的記錄檔記錄數目上限。 當使用自動檢查點的資料庫到達這個記錄檔數目上限時， [!INCLUDE[ssDE](../../includes/ssde-md.md)] 會發出資料庫的檢查點。 
 
自動檢查點之間的時間間隔可能會有 **很大** 的變化。 具有大量交易工作負載的資料庫所擁有的檢查點會比主要用於唯讀作業的資料庫更頻繁。 在簡單復原模式下，如果記錄檔已填滿 70%，則自動檢查點也會排入佇列。  
  
在簡單復原模式下，除非某些因素延遲了記錄截斷，否則自動檢查點會截斷交易記錄的未使用區段。 相反地，在完整復原模式和大量記錄復原模式下，一旦建立了記錄備份鏈結，自動檢查點就不會導致記錄截斷。 如需詳細資訊，請參閱 [交易記錄 &#40;SQL Server&#41;](../../relational-databases/logs/the-transaction-log-sql-server.md)。  
  
當系統損壞時，復原給定資料庫所需的時間長度大部分取決於重做損壞時已變更之頁面所需的隨機 I/O 數量。 這表示 **recovery interval** 設定不可靠。 它無法判斷精確的復原持續時間。 此外，當自動檢查點正在進行時，資料的一般 I/O 活動會大幅增加而且無法預測。  
   
###  <a name="impact-of-recovery-interval-on-recovery-performance"></a><a name="PerformanceImpact"></a> 復原間隔對復原效能的影響  
如果是使用簡短交易的線上交易處理 (OLTP) 系統， **recovery interval** 是決定復原時間的主要因素。 但是， **recovery interval** 選項並不會影響重做長時間執行之交易所需的時間。 如果資料庫包含長時間執行的交易，則復原此資料庫所花費的時間可能要比 **recovery interval** 設定中指定的時間還長。 
 
例如，如果長時間執行的交易在伺服器執行個體停用之前，花了兩個小時執行更新，則實際的復原花在復原長交易的時間要比 **recovery interval** 值長得多。 如需長時間執行的交易對復原時間之影響的詳細資訊，請參閱 [交易記錄 &#40;SQL Server&#41;](../../relational-databases/logs/the-transaction-log-sql-server.md)。 如需復原流程的詳細資訊，請參閱[還原和復原概觀 (SQL Server)](../../relational-databases/backup-restore/restore-and-recovery-overview-sql-server.md#TlogAndRecovery)。
  
一般來說，預設值會提供最佳復原效能。 但是在以下情況下，變更復原間隔可能會提升效能：  
  
-   如果未回復長時間執行的交易，復原通常花的時間大幅多於 1 分鐘時。  
  
-   如果您注意到頻繁的檢查點損害了資料庫的效能。  
  
如果您決定增加 **recovery interval** 設定，我們建議您逐漸少量增加此設定，並評估每次累加對於復原效能的影響。 這個方法非常重要，因為當 **recovery interval** 設定增加時，要多花許多倍的時間才能完成資料庫復原。 例如，如果您將 **recovery interval** 變更為 10 分鐘，則完成復原所花費的時間要比將 **recovery interval** 設定為 1 分鐘時大約多 10 倍的時間。  
  
##  <a name="indirect-checkpoints"></a><a name="IndirectChkpt"></a> 間接檢查點
間接檢查點是在 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)]中引進，它會提供替代自動檢查點的可設定資料庫層級。 指定 [目標復原時間] 資料庫設定選項可設定此項目。 如需詳細資訊，請參閱 [變更資料庫的目標復原時間 &#40;SQL Server&#41;](../../relational-databases/logs/change-the-target-recovery-time-of-a-database-sql-server.md)伺服器組態選項。
萬一系統損壞，間接檢查點可能會提供比自動檢查點更快而且更可以預測的復原時間。 間接檢查點會提供以下優點：  
  
-   設定間接檢查點的資料庫線上交易式工作負載可能會導致效能降低。 間接檢查點可確定中途分頁的數目，低於特定臨界值，如此即可在目標復原時間內完成資料庫的復原。 

  相對於使用中途分頁數目的 **間接檢查點**，**復原間隔** 設定選項會使用交易數目來判斷復原時間。 在收到大量 DML 作業的資料庫上啟用間接檢查點時，背景寫入器可開始積極排清磁碟的中途緩衝區，以確保執行復原所需的時間，落在資料庫所設的目標復原時間內。 如此會在某些系統上造成額外的 I/O 活動，而若磁碟子系統的運作超過或接近 I/O 臨界值，就會形成效能瓶頸。  
  
-   間接檢查點可讓您考量 REDO 期間的隨機 I/O 成本來可靠控制資料庫復原時間。 如此可讓伺服器執行個體維持在指定資料庫的復原時間上限內 (除非長時間執行的交易造成過多的復原次數)。  
  
-   間接檢查點會持續在背景中將中途分頁寫入磁碟，以減少與檢查點相關的 I/O 峰值。  
  
但是，設定間接檢查點的資料庫線上交易式工作負載可能會導致效能降低。 這是因為，間接檢查點使用的背景寫入器有時會讓伺服器執行個體的寫入負載總量增加。  
 
> [!IMPORTANT]
> 間接檢查點是在 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 中建立之新資料庫的預設行為，包括模型和 TempDB 資料庫。          
> 除非明確地改變成使用間接檢查點，否則已就地升級或從舊版 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 還原的資料庫會使用先前的自動檢查點行為。       

### <a name="improved-indirect-checkpoint-scalability"></a><a name="ctp23"></a> 改善的間接檢查點延展性
在 [!INCLUDE[ssNoVersion](../../includes/sssqlv15-md.md)] 之前，您可能會在資料庫產生大量中途分頁 (例如 `tempdb`) 時遇到沒有產量的排程器錯誤。 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 引進改善的間接檢查點延展性，有助於避免在具備大量 `UPDATE`/`INSERT` 工作負載的資料庫上發生這類錯誤。
  
##  <a name="internal-checkpoints"></a><a name="EventsCausingChkpt"></a> 內部檢查點  
內部檢查點是由各種伺服器元件產生，可保證磁碟映像符合目前的記錄檔狀態。 產生內部檢查點是為了回應以下事件：  
  
-   使用 ALTER DATABASE 加入或移除資料庫檔案。  
  
-   取得資料庫備份。  
  
-   針對 DBCC CHECKDB 建立資料庫快照集，不論是明確或內部方式均可。  
  
-   執行需要關閉資料庫的活動。 例如，AUTO_CLOSE 為 ON，且上次使用者與資料庫的連接已經關閉，或是已變更資料庫選項，因此需要重新啟動資料庫。  
  
-   經由停止 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (MSSQLSERVER) 服務，來停止 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的執行個體。 任何一項動作都會導致在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]執行個體的每個資料庫中產生檢查點。  
  
-   讓 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 容錯移轉叢集執行個體 (FCI) 離線。      
  
##  <a name="related-tasks"></a><a name="RelatedTasks"></a> Related tasks  
 **若要變更伺服器執行個體的復原間隔**  
  
-   [Configure the recovery interval Server Configuration Option](../../database-engine/configure-windows/configure-the-recovery-interval-server-configuration-option.md)  
  
 **若要設定資料庫的間接檢查點**  
  
-   [變更資料庫的目標復原時間 &#40;SQL Server&#41;](../../relational-databases/logs/change-the-target-recovery-time-of-a-database-sql-server.md)  
  
 **若要發出資料庫的手動檢查點**  
  
-   [CHECKPOINT &#40;Transact-SQL&#41;](../../t-sql/language-elements/checkpoint-transact-sql.md)  

## <a name="see-also"></a>另請參閱  
[交易記錄 &#40;SQL Server&#41;](../../relational-databases/logs/the-transaction-log-sql-server.md)            
[SQL Server 交易記錄架構與管理指南](../../relational-databases/sql-server-transaction-log-architecture-and-management-guide.md)      
 
