---
title: 暫停與繼續資料庫鏡像
description: 了解如何暫停並在稍後繼續 SQL Server 資料庫鏡像工作階段，以在暫停鏡像時保留工作階段狀態。
ms.custom: seo-lt-2019
ms.date: 03/04/2017
ms.prod: sql
ms.prod_service: high-availability
ms.reviewer: ''
ms.technology: database-mirroring
ms.topic: conceptual
helpviewer_keywords:
- sessions [SQL Server], database mirroring
- resuming database mirroring
- database mirroring [SQL Server], pausing
- database mirroring [SQL Server], resuming
- pausing database mirroring
ms.assetid: c67802c6-ee8c-4cbd-a6d4-f7b80413a4ab
author: MikeRayMSFT
ms.author: mikeray
ms.openlocfilehash: 4163add4c04b919d0cc99da93a440383879bdb08
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97644274"
---
# <a name="pausing-and-resuming-database-mirroring-sql-server"></a>暫停與繼續資料庫鏡像 (SQL Server)
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
  資料庫擁有者隨時都可以先暫停資料庫鏡像工作階段，稍後再繼續。 暫停會保留工作階段狀態，同時暫停鏡像。 發生瓶頸時，暫停可能會對改進主體伺服器的效能有所幫助。  
  
 工作階段暫停時，主體資料庫仍然可以使用。 暫停會使鏡像工作階段的狀態設定為 SUSPENDED，鏡像資料庫不再與主體資料庫同步，造成主體資料庫需公開執行。  
  
 因為只要資料庫鏡像工作階段維持暫停狀態，就無法截斷交易記錄，所以建議您快速地繼續暫停的工作階段。 因此，如果資料庫鏡像工作階段暫停太久，則會填滿交易記錄，而讓資料庫無法使用。 如需為何發生此狀況的說明，請參閱這個主題後面的「暫停和繼續對記錄截斷的影響」。  
  
> [!IMPORTANT]  
>  在強制服務之後，當原始主體伺服器重新連接時，會暫停鏡像。 在這種情況下繼續執行鏡像，很可能會造成原始主體伺服器上的資料遺失。 如需有關管理潛在資料遺失的詳細資訊，請參閱＜ [Database Mirroring Operating Modes](../../database-engine/database-mirroring/database-mirroring-operating-modes.md)＞。  
  
 **本主題內容：**  
  
-   [暫停和繼續對記錄截斷的影響](#EffectOnLogTrunc)  
  
-   [避免填滿交易記錄](#AvoidFullLog)  
  
-   [相關工作](#RelatedTasks)  
  
##  <a name="how-pausing-and-resuming-affect-log-truncation"></a><a name="EffectOnLogTrunc"></a> 暫停和繼續對記錄截斷的影響  
 一般而言，在資料庫上執行自動檢查點時，交易記錄會在下一個記錄備份之後，截斷至該檢查點。 資料庫鏡像工作階段維持暫停狀態時，因為主體伺服器等著將目前的記錄傳送至鏡像伺服器，所以目前所有的記錄都會維持使用中狀態。 在工作階段繼續，且主體伺服器已將記錄傳送至鏡像伺服器之前，未傳送的記錄都會累積在主體資料庫的交易記錄中。  
  
 繼續工作階段時，主體伺服器會立即開始將累積的記錄傳送至鏡像伺服器。 鏡像伺服器確認已將與最舊自動檢查點對應的記錄放入佇列之後，主體伺服器會將主體資料庫的記錄檔截斷至該檢查點。 鏡像伺服器會在同一筆記錄截斷重做佇列。 每個後續檢查點都重複這個處理序時，則會依據檢查點並按階段截斷記錄檔。  
  
> [!NOTE]  
>  如需檢查點和記錄截斷的詳細資訊，請參閱 [資料庫檢查點 &#40;SQL Server&#41;](../../relational-databases/logs/database-checkpoints-sql-server.md)。  
  
##  <a name="avoid-a-full-transaction-log"></a><a name="AvoidFullLog"></a> 避免填滿交易記錄  
 如果記錄已填滿 (因為已達到最大值，或者伺服器執行個體用盡空間)，資料庫就不能再執行其他更新。 若要避免此問題，您有兩個替代方法：  
  
-   在記錄填滿之前，先繼續資料庫的鏡像工作階段，或增加更多記錄空間。 繼續資料庫的鏡像，主體伺服器才能將累積的使用中記錄傳送至鏡像伺服器，並讓鏡像資料庫處於 SYNCHRONIZING 狀態。 然後，鏡像伺服器才可將記錄強化至磁碟，並開始重做它。  
  
-   透過移除鏡像，停止資料庫鏡像工作階段。  
  
     與暫停工作階段不同的是，移除鏡像會卸除鏡像工作階段的所有相關資訊。 每個夥伴伺服器執行個體，會保留本身的資料庫副本。 如果復原先前的鏡像副本，則可能會與先前的主體副本有所差異，且會落後自工作階段暫停後所經過的時間量。 如需詳細資訊，請參閱 [移除資料庫鏡像 &#40;SQL Server&#41;](../../database-engine/database-mirroring/removing-database-mirroring-sql-server.md)＞。  
  
##  <a name="related-tasks"></a><a name="RelatedTasks"></a> 相關工作  
 **若要暫停或繼續資料庫鏡像**  
  
-   [暫停或繼續資料庫鏡像工作階段 &#40;SQL Server&#41;](../../database-engine/database-mirroring/pause-or-resume-a-database-mirroring-session-sql-server.md)  
  
 **若要停止資料庫鏡像**  
  
-   [移除資料庫鏡像 &#40;SQL Server&#41;](../../database-engine/database-mirroring/remove-database-mirroring-sql-server.md)  
  
## <a name="see-also"></a>另請參閱  
 [ALTER DATABASE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-database-transact-sql.md)   
 [資料庫鏡像 &#40;SQL Server&#41;](../../database-engine/database-mirroring/database-mirroring-sql-server.md)   
 [移除資料庫鏡像 &#40;SQL Server&#41;](../../database-engine/database-mirroring/removing-database-mirroring-sql-server.md)  
  
  
