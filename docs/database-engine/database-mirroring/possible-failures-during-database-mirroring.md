---
title: 資料庫鏡像期間可能發生的失敗 | Microsoft Docs
description: 了解資料庫鏡像工作階段中因實體、作業系統或 SQL Server 問題而造成的失敗，以及如何回應錯誤。
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: high-availability
ms.reviewer: ''
ms.technology: database-mirroring
ms.topic: conceptual
helpviewer_keywords:
- time-out period [SQL Server database mirroring]
- soft errors [SQL Server]
- database mirroring [SQL Server], troubleshooting
- timeout errors [SQL Server]
- troubleshooting [SQL Server], database mirroring
- hard errors
- failed database mirroring sessions [SQL Server]
ms.assetid: d7031f58-5f49-4e6d-9a62-9b420f2bb17e
author: MikeRayMSFT
ms.author: mikeray
ms.openlocfilehash: fb6e70d4e45304d9b22026d3a41a00d2f24276e1
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97644242"
---
# <a name="possible-failures-during-database-mirroring"></a>資料庫鏡像期間可能發生的失敗
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
  實體、作業系統或 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 問題都可能會在資料庫鏡像工作階段中導致失敗。 資料庫鏡像不會為了確認 Sqlservr.exe 所依賴的元件是正常運作或已失敗，而定期檢查這些元件。 不過，針對某些類型的錯誤，受影響的元件會對 Sqlservr.exe 報告錯誤。 由其他元件所報告的錯誤稱為「重大錯誤」(Hard Error)。 為了偵測其他沒有通知的失敗，資料庫鏡像會實作其本身的逾時機制。 當鏡像逾時發生時，資料庫鏡像會假設失敗已經發生，並宣告「軟性錯誤」。 但是，某些發生在 SQL Server 執行個體層級的失敗並不會造成鏡像逾時，而且可能無法偵測到。  
  
> [!IMPORTANT]  
>  在資料庫鏡像工作階段中，偵測不到鏡像資料庫以外之資料庫中的失敗。 而且，除非是因為資料磁碟失敗而重新啟動資料庫，否則不太可能偵測得到資料磁碟失敗。  
  
 錯誤偵測的速度以及受影響之鏡像工作階段對失敗的反應時間，取決於錯誤為硬性或軟性。 某些硬性錯誤，如網路失敗，會立即報告。 不過，有些情況下，元件專用的逾時期限可以使某些硬性錯誤延遲報告。 至於軟性錯誤，鏡像逾時期限的長度將決定錯誤偵測的速度。 此期限長度預設為 10 秒。 這是最小的建議值。  
  
## <a name="failures-due-to-hard-errors"></a>硬性錯誤造成的失敗  
 硬性錯誤的可能原因包含 (但不限於) 下列狀況：  
  
-   連接或連線中斷  
  
-   網路卡損毀  
  
-   路由器變更  
  
-   防火牆變更  
  
-   端點重新設定  
  
-   找不到交易記錄所在的磁碟機  
  
-   作業系統或處理序失敗  
  
 例如，如果主體資料庫的記錄磁碟機無法回應並故障，作業系統就會通知 Sqlservr.exe，已發生嚴重錯誤。  
  
 某些元件 (例如，網路元件和某些 IO 子系統) 會有它們自訂的逾時，可以判斷錯誤。 這類逾時與資料庫鏡像無關，資料庫鏡像對此一無所知，而且完全不會察覺這些逾時行為。 在這些情況下，逾時延遲會增加從失敗發生到資料庫鏡像收到產生的硬性錯誤之間的時間。  
  
> [!NOTE]  
>  唯一針對資料庫鏡像執行的主動錯誤檢查會針對軟性錯誤情況進行。 如需詳細資訊，請參閱本主題後面的「軟性錯誤造成的失敗」。  
  
 為了協助您解讀網路上發生的錯誤狀況，請詢問網路工程師，在 TCP 連接發生下列事件時，會將什麼錯誤訊息傳送至通訊埠：  
  
-   DNS 沒有作用。  
  
-   未插上纜線。  
  
-   [!INCLUDE[msCoName](../../includes/msconame-md.md)] Windows 具有封鎖特定通訊埠的防火牆。  
  
-   正在監視通訊埠的應用程式失敗。  
  
-   已重新命名 Windows 伺服器。  
  
-   已重新啟動 Windows 伺服器。  
  
> [!NOTE]  
>  對於用戶端存取伺服器方面的特定問題，鏡像無法加以防止。 例如，試想一個情況，公用網路介面卡處理對主體伺服器執行個體的用戶端連接，而私人網路介面卡則處理伺服器執行個體之間的所有鏡像傳輸。 在這個情況下，公用網路介面卡的失敗將會阻止用戶端存取資料庫，然而資料庫卻繼續進行鏡像。  
  
## <a name="failures-due-to-soft-errors"></a>軟性錯誤造成的失敗  
 可能會造成鏡像逾時的狀況包括 (但不限於) 下列狀況：  
  
-   網路錯誤，例如 TCP 連結逾時、卸除或損毀的封包或順序不正確的封包。  
  
-   沒有回應的作業系統、伺服器或資料庫。  
  
-   Windows 伺服器逾時。  
  
-   運算資源不足，例如 CPU 或磁碟負擔過重、交易記錄已滿，或系統的記憶體或執行緒用盡。 在這些情況下，您必須增加逾時期限、降低工作負載或更換硬體來因應工作負載。  
  
### <a name="the-mirroring-time-out-mechanism"></a>鏡像逾時機制  
 因為伺服器執行個體無法直接偵測到軟性錯誤，所以軟性錯誤可能會造成伺服器執行個體永遠等候。 為了避免這種狀況，資料庫鏡像會實作本身的逾時機制，而在此機制中，鏡像工作階段中的每個伺服器執行個體都會以固定間隔在每個開啟連接上送出 Ping。  
  
 若要將連接保持為開啟狀態，伺服器執行個體必須於定義的逾時期限加上傳送另一個 Ping 所需的時間內，在該連接上收到 Ping。 在逾時期限接收到 Ping，表示連接仍為開啟狀態，且伺服器執行個體是透過它進行通訊。 接收到 Ping 時，伺服器執行個體會重設它在該連接上的逾時計數器。  
  
 如果逾時期限在連接上未接收到 Ping，則伺服器執行個體會將該連接視為逾時。伺服器執行個體會關閉逾時連接，並根據工作階段的狀態和作業模式來處理逾時事件。  
  
 即使其他伺服器實際上運作正常，仍會將逾時視為失敗。 如果工作階段的逾時值太短，來不及收到對方的正常回應，則可能發生假性失敗。 當某個伺服器執行個體順利連絡另一個回應時間很慢的執行個體時，由於在逾時期限到期前未收到 Ping，所以會發生假性失敗。  
  
 在高效能模式工作階段中，逾時期限一律為 10 秒。 這通常足以避免假性失敗。 在高安全性模式工作階段中，預設逾時期限為 10 秒，但是您可以變更這個時間。 為了避免假性失敗，建議您將鏡像逾時期限一律設為 10 秒或更久。  
  
 **若要變更逾時值 (僅高安全性模式)**  
  
-   使用 [ALTER DATABASE \<database> SET PARTNER TIMEOUT \<integer>](../../t-sql/statements/alter-database-transact-sql.md) 陳述式。  
  
 **若要檢視目前的逾時值**  
  
-   查詢 **sys.database_mirroring** 中的 [mirroring_connection_timeout](../../relational-databases/system-catalog-views/sys-database-mirroring-transact-sql.md)。  
  
## <a name="responding-to-an-error"></a>回應錯誤  
 不論錯誤的類型為何，偵測到錯誤的伺服器執行個體都會根據執行個體的角色、工作階段的作業模式和工作階段中其他連接的狀態，進行適當的回應。 如需有關遺失夥伴時所發生之情況的詳細資訊，請參閱 [資料庫鏡像作業模式](../../database-engine/database-mirroring/database-mirroring-operating-modes.md)。  
  
## <a name="see-also"></a>另請參閱  
 [預估角色切換期間的服務中斷時間 &#40;資料庫鏡像&#41;](../../database-engine/database-mirroring/estimate-the-interruption-of-service-during-role-switching-database-mirroring.md)   
 [資料庫鏡像作業模式](../../database-engine/database-mirroring/database-mirroring-operating-modes.md)   
 [資料庫鏡像工作階段期間的角色切換 &#40;SQL Server&#41;](../../database-engine/database-mirroring/role-switching-during-a-database-mirroring-session-sql-server.md)   
 [資料庫鏡像 &#40;SQL Server&#41;](../../database-engine/database-mirroring/database-mirroring-sql-server.md)  
  
  
