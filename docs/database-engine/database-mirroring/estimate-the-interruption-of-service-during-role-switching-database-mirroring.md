---
title: 預估鏡像容錯移轉期間的服務中斷
description: 預估將資料庫鏡像從主要角色容錯移轉至次要角色時的服務中斷情形。
ms.custom: seo-lt-2019
ms.date: 03/01/2017
ms.prod: sql
ms.prod_service: high-availability
ms.reviewer: ''
ms.technology: database-mirroring
ms.topic: conceptual
helpviewer_keywords:
- parallel redo [SQL Server]
- role switching [SQL Server]
- database mirroring [SQL Server], queues
- failover [SQL Server], database mirroring
- redo [database mirroring]
- database mirroring [SQL Server], failover
ms.assetid: 586a6f25-672b-491b-bc2f-deab2ccda6e2
author: MikeRayMSFT
ms.author: mikeray
ms.openlocfilehash: 4b8f2e5435f6f168a113cf78b1539e97c4935972
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97643922"
---
# <a name="estimate-the-interruption-of-service-during-role-switching-database-mirroring"></a>預估角色切換期間的服務中斷時間 (資料庫鏡像)
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
  在角色切換期間，資料庫鏡像無法服務的時間量視角色切換類型和角色切換原因而定。  
  
-   對於自動容錯移轉，有兩個因素構成服務中斷時間：鏡像伺服器辨識主體伺服器執行個體失敗所需的時間，即錯誤偵測，加上資料庫容錯移轉所需的時間，即容錯移轉時間。  
  
-   對於強制服務作業，雖然發生失敗，失敗的偵測和回應視人員回應而定。 不過，預估可能的服務中斷，僅限於預估發出強制服務命令之後鏡像伺服器切換角色的時間。  
  
    > [!NOTE]  
    >  若要減少偵測特定狀況 (例如某些類型的錯誤) 所需的時間，您可以定義那些狀況的警示。  
  
-   若為手動容錯移轉，則只有發出容錯移轉命令之後資料庫容錯移轉所需的時間。  
  
## <a name="error-detection"></a>錯誤偵測  
 系統注意到錯誤時間取決於錯誤的類型；例如，網路錯誤幾乎會立即注意到，而注意到伺服器沒有回應則需要 10 秒 (預設逾時)。  
  
 如需在具有自動容錯移轉的高安全性模式下進行資料庫鏡像工作階段以及逾時偵測期間，可能導致失敗之錯誤的資訊，請參閱 [資料庫鏡像期間可能發生的失敗](../../database-engine/database-mirroring/possible-failures-during-database-mirroring.md)。  
  
## <a name="failover-time"></a>容錯移轉時間  
 容錯移轉時間主要包含：先前的鏡像伺服器向前復原其重做佇列中剩餘之所有記錄檔所需的時間，加上其他很短的時間 (如需鏡像伺服器如何處理記錄檔記錄的詳細資訊，請參閱＜[資料庫鏡像 &#40;SQL Server&#41;](../../database-engine/database-mirroring/database-mirroring-sql-server.md))。 如需預估容錯移轉時間的詳細資訊，請參閱此主題稍後的「預估容錯移轉重做速率」。  
  
> [!IMPORTANT]  
>  如果在交易期間建立了索引或資料表之後又變更，而發生容錯移轉，容錯移轉的時間會比平常更久。  例如，在下列一系列作業期間發生容錯移轉，會增加容錯移轉時間：BEGIN TRANSACTION、在資料表上 CREATE INDEX 和 SELECT INTO 資料表。 在這種交易期間，仍有增加容錯移轉時間的可能性，直到以 COMMIT TRANSACTION 或 ROLLBACK TRANSACTION 陳述式完成它為止。  
  
### <a name="the-redo-queue"></a>重做佇列  
 向前恢復資料庫涉及套用目前在鏡像伺服器上重做佇列中的記錄。 「重做佇列」  包含已寫入鏡像伺服器上的磁碟，但尚未在鏡像資料庫上向前復原的記錄檔記錄。  
  
 資料庫的容錯移轉時間取決於鏡像伺服器向前恢復重做佇列中記錄的速度，而這主要又是由系統硬體與目前工作負載所決定。 主體資料庫有可能會變得很忙碌，使主體伺服器以比向前恢復記錄檔更快的速度，將記錄檔轉送到鏡像伺服器。 在此情況下，當鏡像伺服器向前恢復重做佇列中的記錄時，容錯移轉可能花費很多時間。 若要了解重做佇列的目前大小，請使用資料庫鏡像效能物件中的 **Redo Queue** 計數器。 如需詳細資訊，請參閱 [SQL Server 的 Database Mirroring 物件](../../relational-databases/performance-monitor/sql-server-database-mirroring-object.md)。  
  
### <a name="estimating-the-failover-redo-rate"></a>預估容錯移轉重做速率  
 您可利用生產資料庫的測試複本，測量向前復原記錄檔記錄所需的時間量 (「重做速率」  )。  
  
 預估容錯移轉期間之向前恢復時間的方法，取決於重做階段期間鏡像伺服器所使用的執行緒數目。 執行緒的數目取決下列：  
  
-   在 [!INCLUDE[ssStandard](../../includes/ssstandard-md.md)]中，鏡像伺服器永遠會使用單一執行緒來向前復原資料庫。  
  
-   在 [!INCLUDE[ssEnterprise](../../includes/ssenterprise-md.md)] 中，少於五個 CPU 之電腦上的鏡像伺服器也只會使用單一執行緒。 如果有五個以上的 CPU，鏡像伺服器會在容錯移轉期間，將其向前復原作業散發到多個執行緒 (也稱為「平行重做」  )。 平行重做已完成最佳化，針對每四個 CPU 使用一個執行緒。  
  
#### <a name="estimating-the-single-threaded-redo-rate"></a>預估單一執行緒的重做速率  
 對於單一執行緒重做，在容錯移轉期間向前恢復鏡像資料庫所花費的時間，大約與記錄備份還原要向前恢復相同數量的記錄相同。 若要預估容錯移轉時間，請在您要執行鏡像的環境中建立測試資料庫。 然後，從實際資料庫中取得記錄備份。 若要計算該記錄備份的重做速率，請計時您在測試資料庫上還原記錄備份 WITH NORECOVERY 的時間長度。  
  
 一旦您得知鏡像伺服器的重做速率，即可依重做速率在鏡像伺服器上分隔要重做的目前記錄檔數量 (如 **Redo Queue** 效能計數器所測量)，以預估在給定的時間點上容錯移轉資料庫的時間。 在正常狀況下，如果鏡像伺服器能跟上主體伺服器的負載， **Redo Queue** 會極小或接近零，而容錯移轉只會花費數秒。  
  
#### <a name="estimating-the-parallel-redo-rate"></a>預估平行重做速率  
 在 [!INCLUDE[ssEnterprise](../../includes/ssenterprise-md.md)]中，平行重做已完成最佳化，針對每四個 CPU 使用一個執行緒。 若要預估平行重做的向前恢復時間，存取執行中測試系統比存取測試資料庫更加精確。 在鏡像伺服器上監視重做佇列時，請增加主體伺服器上的負載。 在正常作業中，重做佇列會接近零。 請增加主體伺服器上的負載，直到 Redo Queue 開始持續成長為止；然後系統會在其重做速率的上限，而 **Redo Bytes/sec** 效能計數器此時代表重做速率的上限。 如需詳細資訊，請參閱 [SQL Server 的 Database Mirroring 物件](../../relational-databases/performance-monitor/sql-server-database-mirroring-object.md)。  
  
## <a name="estimating-interruption-of-service-during-automatic-failover"></a>預估自動容錯移轉期間的服務中斷時間  
 下圖說明錯誤偵測與容錯移轉時間，如何構成在 **Partner_B** 上完成自動容錯移轉所需的全部時間。 容錯移轉需要時間以向前恢復資料庫 (重做階段)，再加上少許時間，才能使資料庫上線。 恢復階段會在新的主體資料庫上線後發生，並在容錯移轉之後繼續作業，其中涉及回復所有未認可的交易。 恢復階段期間可使用資料庫。  
  
 ![錯誤偵測和容錯移轉時間](../../database-engine/database-mirroring/media/dbm-failovauto-time.gif "錯誤偵測和容錯移轉時間")  
  
## <a name="see-also"></a>另請參閱  
 [資料庫鏡像作業模式](../../database-engine/database-mirroring/database-mirroring-operating-modes.md)   
 [資料庫鏡像工作階段期間的角色切換 &#40;SQL Server&#41;](../../database-engine/database-mirroring/role-switching-during-a-database-mirroring-session-sql-server.md)   
 [監視資料庫鏡像 &#40;SQL Server&#41;](../../database-engine/database-mirroring/monitoring-database-mirroring-sql-server.md)  
  
  
