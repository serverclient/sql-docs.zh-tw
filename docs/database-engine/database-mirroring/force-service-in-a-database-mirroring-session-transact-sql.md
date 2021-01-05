---
title: 強制資料庫鏡像服務
description: 如果主體伺服器失敗且鏡像伺服器可用，透過將服務強制容錯移轉至鏡像資料庫來使資料庫可用。
ms.custom: seo-lt-2019
ms.date: 03/04/2017
ms.prod: sql
ms.prod_service: high-availability
ms.reviewer: ''
ms.technology: database-mirroring
ms.topic: conceptual
helpviewer_keywords:
- forced service [SQL Server]
- database mirroring [SQL Server], forcing service
ms.assetid: 8b6ffe77-35f3-4e2a-a658-8a38a8e1c794
author: MikeRayMSFT
ms.author: mikeray
ms.openlocfilehash: 453238fa4fe07bb90d10b502195bf9b8e5ad8805
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97641632"
---
# <a name="force-service-in-a-database-mirroring-session-transact-sql"></a>在資料庫鏡像工作階段中強制服務 (Transact-SQL)
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
  在高效能模式與不含自動容錯移轉的高安全性模式中，若主體伺服器失敗而鏡像伺服器可用，則資料庫擁有者就可以強制將服務容錯移轉到鏡像資料庫 (有遺失資料的可能)，讓資料庫成為可用。 只在下列所有狀況成立時才可使用此選項：  
  
-   主體伺服器已關閉。  
  
-   WITNESS 設定為 OFF，或連接到鏡像伺服器。  
  
> [!CAUTION]  
>  強制服務主要是一種損毀復原方法。 強制服務可能造成部分資料遺失。 因此，只有在您願意冒著遺失一些資料的風險來立即還原資料庫的服務時，才進行強制服務。 如果強制服務有遺失重要資料的風險，建議您停止鏡像並手動重新同步處理資料庫。 如需強制服務風險的詳細資訊，請參閱 [資料庫鏡像作業模式](../../database-engine/database-mirroring/database-mirroring-operating-modes.md)。  
  
 強制服務會暫停工作階段並啟動新的復原分岔。 強制服務的效果類似於移除鏡像並復原之前的主體資料庫。 不過，在鏡像繼續進行時，強制服務可方便您重新同步處理資料庫 (有遺失資料的可能)。  
  
### <a name="to-force-service-in-a-database-mirroring-session"></a>若要在資料庫鏡像工作階段強制服務  
  
1.  連接鏡像伺服器。  
  
2.  發出下列陳述式：  
  
     ALTER DATABASE <資料庫名稱> SET PARTNER FORCE_SERVICE_ALLOW_DATA_LOSS  
  
     其中 <資料庫名稱> 是鏡像資料庫。  
  
     鏡像伺服器會立即轉換為主體伺服器，並暫停鏡像。  
  
## <a name="see-also"></a>另請參閱  
 [ALTER DATABASE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-database-transact-sql.md)   
 [Database Mirroring Operating Modes](../../database-engine/database-mirroring/database-mirroring-operating-modes.md)  
  
  
