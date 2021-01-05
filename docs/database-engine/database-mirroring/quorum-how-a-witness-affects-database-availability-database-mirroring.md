---
title: 見證如何影響資料庫可用性
description: 了解資料庫鏡像如何影響仲裁和資料庫可用性。 仲裁確保資料庫一次只能由一個合作夥伴擁有。
ms.custom: seo-lt-2019
ms.date: 03/01/2017
ms.prod: sql
ms.prod_service: high-availability
ms.reviewer: ''
ms.technology: database-mirroring
ms.topic: conceptual
helpviewer_keywords:
- quorum [SQL Server], database mirroring
- running exposed in database mirroring [SQL Server]
- witness-to-partner quorum [SQL Server]
- partners [SQL Server], partner-to-partner quorum
- witness [SQL Server], quorum
- have quorum [SQL Server]
- quorum [SQL Server]
- mirror database [SQL Server]
- full quorum [SQL Server]
- high-availability mode [SQL Server]
ms.assetid: a62d9dd7-3667-4751-a294-a61fc9caae7c
author: MikeRayMSFT
ms.author: mikeray
ms.openlocfilehash: f30f336ade4b3ab911d6ed365ddfa637230ea02e
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97641323"
---
# <a name="quorum-how-a-witness-affects-database-availability-database-mirroring"></a>仲裁：見證如何影響資料庫可用性 (資料庫鏡像)
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
  每當設定資料庫鏡像工作階段的見證時，就需要「仲裁」。 仲裁是資料庫鏡像工作階段中的多個伺服器執行個體彼此連接時所存在的關係。 一般而言，仲裁涉及三個互連的伺服器執行個體。 設定見證之後，需要有仲裁才能使用資料庫。 仲裁是專為具有自動容錯移轉的高安全性模式而設計，可確保資料庫一次僅能由一個夥伴擁有。  
  
 如果特定的伺服器執行個體與鏡像工作階段的連接中斷，該執行個體便會失去仲裁。 如果沒有任何伺服器執行個體是處於連接狀態，則工作階段會失去仲裁，而資料庫也會變成無法使用。 仲裁有三種可能的類型：  
  
-   「完整仲裁」，包含兩個夥伴及見證。  
  
-   「見證對夥伴仲裁」，由見證與其中一個夥伴所組成。  
  
-   「夥伴對夥伴仲裁」，由兩個夥伴所組成。  
  
 下圖顯示這些仲裁類型。  
  
 ![仲裁：完整；見證與夥伴；兩個夥伴](../../database-engine/database-mirroring/media/dbm-failovautoquorum.gif "仲裁：完整；見證與夥伴；兩個夥伴")  
  
 只要目前的主體伺服器有仲裁，它就擁有主體的角色並繼續為資料庫服務，除非資料庫擁有者執行手動容錯移轉。 如果主體伺服器失去了仲裁，就會停止為資料庫服務。 只有在主體資料庫失去仲裁時 (可確保它已不再為資料庫提供服務)，才會發生自動容錯移轉。  
  
 中斷連接的伺服器執行個體會將其最新的角色儲存在工作階段中。 通常，中斷連接的伺服器執行個體會在重新啟動並重新取得仲裁時，重新連接到工作階段。  
  
> [!IMPORTANT]  
>  只有當您想要使用具有自動容錯移轉的高安全性模式時，才需要設定見證。 在不需要見證的高效能模式中，強烈建議您將 WITNESS 屬性設為 OFF。 如需見證對高效能模式影響的資訊，請參閱 [資料庫鏡像作業模式](../../database-engine/database-mirroring/database-mirroring-operating-modes.md)。  
  
## <a name="quorum-in-high-safety-mode-sessions"></a>高安全性模式工作階段中的仲裁  
 在高安全性模式中，仲裁會提供內容，讓具有仲裁的伺服器執行個體在裡頭仲裁哪一個夥伴擁有主體角色，藉以允許自動容錯移轉。 如果主體伺服器擁有仲裁，便會為資料庫提供服務。 如果主體伺服器在已同步的鏡像伺服器與見證保有仲裁時失去仲裁，就會發生自動容錯移轉。  
  
 高安全性模式的仲裁狀況如下：  
  
-   「完整仲裁」，由兩個夥伴及一個見證組成。  
  
     一般情況下，所有三個伺服器執行個體都會參與三向仲裁，稱之為「完整仲裁」。 利用完整仲裁，主體及鏡像伺服器會繼續執行其個別角色 (除非發生手動容錯移轉)。  
  
-   「見證對夥伴仲裁」，由見證和任一夥伴組成。  
  
     如果由於失去其中一個夥伴，而失去夥伴之間的網路連接，則可能會發生以下狀況：  
  
    -   失去鏡像伺服器，但主體伺服器與見證仍保留仲裁。  
  
         在這種情況下，主體將其資料庫設定為 DISCONNECTED，並且在鏡像設為 SUSPENDED 的狀態中執行。 (這稱之為「公開執行」，因為資料庫目前沒有鏡像)。當鏡像伺服器重新加入工作階段時，這個伺服器會取回鏡像的仲裁，並開始重新同步處理它的資料庫副本。  
  
    -   失去主體伺服器，但見證與鏡像伺服器仍保留仲裁。  
  
         此時會發生自動容錯移轉。 如需詳細資訊，請參閱 [Database Mirroring Operating Modes](../../database-engine/database-mirroring/database-mirroring-operating-modes.md)。  
  
    -   所有伺服器執行個體都會遺失仲裁，但是鏡像和見證之後會重新連接。 此情況下將不會服務資料庫。  
  
     通常，很少會發生兩個容錯移轉夥伴之間的網路連接中斷，但這兩個夥伴仍保持連接到見證的狀況。 在此事件中，有兩個個別的見證對夥伴仲裁存在，見證是連絡人。 見證通知鏡像伺服器，主體伺服器仍然連接。 因此，不會發生自動容錯移轉。 反而鏡像伺服器會保有鏡像角色，等待重新連接到主體。 如果重做佇列包含位於此點的記錄，鏡像伺服器會繼續向前復原鏡像資料庫。 等到重新連接時，鏡像伺服器就會重新同步處理鏡像資料庫。  
  
-   「夥伴對夥伴仲裁」，由兩個夥伴組成。  
  
     只要夥伴仍保留仲裁，資料庫就會繼續處於 SYNCHRONIZED 狀態，而且可以進行手動容錯移轉。 沒有見證，就無法進行自動容錯移轉；但是當見證重新取得仲裁時，工作階段會繼續一般作業，並且再次支援自動容錯移轉。  
  
-   工作階段失去仲裁。  
  
     如果所有伺服器執行個體變成彼此中斷連接，就可以說工作階段已經「失去仲裁」。 當伺服器執行個體彼此重新連接時，彼此會議定取回仲裁。  
  
    -   如果主體伺服器與其他任一伺服器執行個體重新連接，資料庫便可使用。  
  
    -   如果主體伺服器的連接仍然中斷，而鏡像與見證彼此重新連接，則會因為可能發生資料遺失而無法進行自動容錯移轉。 因此，除非主體伺服器重新加入工作階段，否則資料庫仍然無法使用。  
  
    -   當所有三個伺服器執行個體重新連接時，將會重新取得完整仲裁，而且工作階段也會繼續其一般作業。  
  
> [!IMPORTANT]  
>  當工作階段具有夥伴對夥伴仲裁時，如果任一個夥伴失去仲裁，工作階段就會失去仲裁。 因此，如果要讓見證的連接中斷好一陣子，我們建議您暫時從工作階段中移除見證。 移除見證便會移除對仲裁的需求。 那麼，如果鏡像伺服器連接中斷了，則還有主體伺服器可以繼續服務資料庫。 如需如何加入或移除見證的資訊，請參閱 [資料庫鏡像見證](../../database-engine/database-mirroring/database-mirroring-witness.md)。  
  
### <a name="how-quorum-affects-database-availability"></a>仲裁如何影響資料庫可用性  
 下圖顯示見證與夥伴如何共同合作以確保在指定時間內，只有一個夥伴可擁有主體角色，而且只有目前的主體伺服器可使其資料庫上線。 這兩個案例會從完整仲裁開始進行，其中的 **Partner_A** 是主體角色，而 **Partner_B** 則是鏡像角色。  
  
 ![見證與夥伴如何合作](../../database-engine/database-mirroring/media/dbm-quorum-scenarios.gif "見證與夥伴如何合作")  
  
 案例 1 顯示在原始主體伺服器 (**Partner_A**) 失敗之後，見證和鏡像如何同意主體伺服器 **Partner_A** 不再可用，從而形成仲裁。 接著，由鏡像伺服器 **Partner_B** 承擔主體角色。 這會發生自動容錯移轉，且 **Partner_B** 會讓其資料庫副本連線工作。 接著 **Partner_B** 會關閉，且資料庫會離線。 稍後，先前的主體伺服器 **Partner_A** 會重新連接到見證以重新取得仲裁，但與見證溝通時， **Partner_A** 將會得知它無法使其資料庫副本上線，因為 **Partner_B** 現在擁有主體角色。 當 **Partner_B** 重新加入工作階段時，它會將其資料庫連線工作。  
  
 在案例 2 中，見證失去仲裁，而夥伴 **Partner_A** 和 **Partner_B** 則彼此保有仲裁，且資料庫會維持在線上。 接著，夥伴也遺失其仲裁，且資料庫會離線。 稍後，主體伺服器 **Partner_A** 會重新連接到見證以重新取得仲裁。 見證會確認 **Partner_A** 是否仍然擁有主體角色，且 **Partner_A** 會將其資料庫連線工作。  
  
## <a name="see-also"></a>另請參閱  
 [資料庫鏡像作業模式](../../database-engine/database-mirroring/database-mirroring-operating-modes.md)   
 [資料庫鏡像工作階段期間的角色切換 &#40;SQL Server&#41;](../../database-engine/database-mirroring/role-switching-during-a-database-mirroring-session-sql-server.md)   
 [資料庫鏡像見證](../../database-engine/database-mirroring/database-mirroring-witness.md)   
 [資料庫鏡像期間可能發生的失敗](../../database-engine/database-mirroring/possible-failures-during-database-mirroring.md)   
 [鏡像狀態 &#40;SQL Server&#41;](../../database-engine/database-mirroring/mirroring-states-sql-server.md)  
  
  
