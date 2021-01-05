---
title: 可用性複本之間連線失敗的原因
description: 本主題描述在參與 Always On 可用性群組之複本間連線失敗的各種可能原因。
ms.custom: seo-lt-2019
ms.date: 05/17/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: conceptual
helpviewer_keywords:
- troubleshooting [SQL Server], HADR
- Availability Groups [SQL Server], availability replicas
- Availability Groups [SQL Server], troubleshooting
ms.assetid: cd613898-82d9-482f-a255-0230a6c7d6fe
author: cawrites
ms.author: chadam
ms.openlocfilehash: e96836a85fb75cc6436530356e8f38ea1a1e1882
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97642536"
---
# <a name="determine-possible-reason-for-connectivity-failures-between-availability-replicas"></a>判斷在可用性複本之間連線失敗的可能原因
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]
實體、作業系統或 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 問題都可能會在兩個可用性複本之間的工作階段中導致失敗。 可用性複本不會為了確認 Sqlservr.exe 所依賴的元件是正常運作或已失敗，而定期檢查這些元件。 不過，針對某些類型的錯誤，受影響的元件會對 Sqlservr.exe 報告錯誤。 由其他元件所報告的錯誤稱為「重大錯誤」  (Hard Error)。 為了偵測其他沒有通知的失敗，[!INCLUDE[ssHADR](../../../includes/sshadr-md.md)]會實作其本身的工作階段逾時機制。 指定工作階段逾時期限 (以秒為單位)。 逾時期限是伺服器執行個體在將另一個執行個體視為中斷連接之前，等待接收該執行個體發出之 PING 訊息的最長時間。 如果兩個可用性複本之間發生工作階段逾時，可用性複本會假設失敗已經發生，並宣告「軟體錯誤」  。  
  
> [!IMPORTANT]  
>  系統無法偵測主要資料庫以外之資料庫中的失敗。 此外，除非是因為資料磁碟錯誤而重新啟動資料庫，否則不太可能偵測得到資料磁碟錯誤。  
  
 錯誤偵測的速度以及失敗的反應時間，取決於錯誤為硬性或軟性。 某些硬性錯誤，如網路失敗，會立即報告。 不過，有些情況下，元件專用的逾時期限可以使某些硬性錯誤延遲報告。 至於軟體錯誤，工作階段逾時期限的長度將決定錯誤偵測的速度。 此期限長度預設為 10 秒。 這是最小的建議值。  
  
## <a name="failures-due-to-hard-errors"></a>硬性錯誤造成的失敗  
 硬性錯誤的可能原因包含 (但不限於) 下列狀況：  
  
-   連接或連線中斷  
  
-   網路卡損毀  
  
-   路由器變更  
  
-   防火牆變更  
  
-   端點重新設定  
  
-   找不到交易記錄所在的磁碟機  
  
-   作業系統或處理序失敗  
  
 例如，如果主要資料庫的記錄磁碟機無法回應並故障，作業系統就會通知 Sqlservr.exe，已發生嚴重錯誤。  
  
 某些元件 (例如，網路元件和某些 IO 子系統) 會有它們自訂的逾時，可以判斷錯誤。 這類逾時與 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)]無關，它對此一無所知，而且完全不會察覺這些逾時行為。 在這些情況下，逾時延遲會增加從失敗發生到可用性複本收到產生的硬性錯誤之間的時間。  
  
> [!NOTE]  
>  唯一針對可用性複本執行的主動錯誤檢查，只會在發生軟體錯誤的情況下進行。 如需詳細資訊，請參閱本主題後面的「軟性錯誤造成的失敗」。  
  
 為了協助您解讀網路上發生的錯誤狀況，請詢問網路工程師，在 TCP 連接發生下列事件時，會將什麼錯誤訊息傳送至通訊埠：  
  
-   DNS 沒有作用。  
  
-   未插上纜線。  
  
-   [!INCLUDE[msCoName](../../../includes/msconame-md.md)] Windows 具有封鎖特定通訊埠的防火牆。  
  
-   正在監視通訊埠的應用程式失敗。  
  
-   已重新命名 Windows 伺服器。  
  
-   已重新啟動 Windows 伺服器。  
  
> [!NOTE]  
>  對於用戶端存取伺服器方面的特定問題，[!INCLUDE[ssHADRc](../../../includes/sshadrc-md.md)]無法加以防止。 例如，試想一個情況，公用網路介面卡處理對主要複本的用戶端連接，而私人網路介面卡則處理裝載可用性群組之複本的伺服器執行個體之間的流量。 在這個情況下，公用網路介面卡的失敗將會阻止用戶端存取資料庫。  
  
## <a name="failures-due-to-soft-errors"></a>軟性錯誤造成的失敗  
 可能會造成工作階段逾時的狀況包括 (但不限於) 下列狀況：  
  
-   網路錯誤，例如 TCP 連結逾時、卸除或損毀的封包或順序不正確的封包。  
  
-   沒有回應的作業系統、伺服器或資料庫。  
  
-   Windows 伺服器逾時。  
  
-   運算資源不足，例如 CPU 或磁碟負擔過重、交易記錄已滿，或系統的記憶體或執行緒用盡。 在這些情況下，您必須增加逾時期限、降低工作負載或更換硬體來因應工作負載。  
  
### <a name="the-session-timeout-mechanism"></a>工作階段逾時機制  
 因為伺服器執行個體無法直接偵測到軟性錯誤，所以軟性錯誤可能會造成可用性複本在工作階段中永遠等候來自另一個可用性複本的回應。 為了避免這種狀況， [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] 會實作工作階段逾時機制，而在此機制中，連接的可用性複本都會以固定間隔在每個開啟連接上送出 Ping。 在逾時期限接收到 Ping，表示連接仍為開啟狀態，且伺服器執行個體是透過它進行通訊。 接收到 Ping 時，複本會重設它在該連接上的逾時計數器。 如需可用性模式和工作階段逾時關係的相關資訊，請參閱[可用性模式 &#40;AlwaysOn 可用性群組&#41;](../../../database-engine/availability-groups/windows/availability-modes-always-on-availability-groups.md)。  
  
 主要和次要複本會互相執行 Ping，讓對方知道他們仍在作用中，而工作階段逾時限制則可防止任一複本無限期地等候接收來自另一複本的 Ping。 工作階段逾時限制是使用者可設定的複本屬性，其預設值為 10 秒。 在逾時期限接收到 Ping，表示連接仍為開啟狀態，且伺服器執行個體是透過它進行通訊。 接收到 Ping 時，可用性複本會重設它在該連接上的逾時計數器。  
  
 如果在工作階段逾時期限內未收到另一個複本的 Ping，則連接會逾時。連接會關閉，而逾時的複本則進入 DISCONNECTED 狀態。 即使中斷連接的複本設定成同步認可模式，交易仍不會等候該複本重新連接及重新同步處理。  
  
## <a name="responding-to-an-error"></a>回應錯誤  
 不論錯誤的類型為何，偵測到錯誤的伺服器執行個體都會根據執行個體的角色、工作階段的可用性模式和工作階段中其他連接的狀態，進行適當的回應。 如需可用性模式和工作階段逾時關係的相關資訊，請參閱[可用性模式 &#40;AlwaysOn 可用性群組&#41;](../../../database-engine/availability-groups/windows/availability-modes-always-on-availability-groups.md)。  
  
## <a name="related-tasks"></a>相關工作  
 **若要變更逾時值 (僅限同步認可可用性模式)**  
  
-   [變更可用性複本的工作階段逾時期限 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/change-the-session-timeout-period-for-an-availability-replica-sql-server.md)  
  
 **若要檢視目前的逾時值**  
  
-   查詢 [sys.availability_replicas &#40;Transact-SQL&#41;](../../../relational-databases/system-catalog-views/sys-availability-replicas-transact-sql.md) 中的 **session_timeout**。  
  
## <a name="see-also"></a>另請參閱  
 [AlwaysOn 可用性群組概觀 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server.md)  
  
  
