---
title: 檢視記錄傳送報表 (SSMS)
description: 檢視 SQL Server Management Studio 中的「交易記錄傳送狀態」報表。 在監視伺服器、主要伺服器或次要伺服器執行狀態報表。
ms.custom: seo-lt-2019
ms.date: 03/04/2017
ms.prod: sql
ms.reviewer: ''
ms.technology: log-shipping
ms.topic: conceptual
helpviewer_keywords:
- viewing log shipping reports
- displaying log shipping reports
- log shipping [SQL Server], monitoring
- log shipping [SQL Server], viewing reports
ms.assetid: 3b549f2f-3683-45e5-b8e8-8095276c41ab
author: cawrites
ms.author: chadam
ms.openlocfilehash: 87292d8c38322069e7bc5fd93802e7e3bffb1978
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97643840"
---
# <a name="view-the-log-shipping-report-sql-server-management-studio"></a>檢視記錄傳送報表 (SQL Server Management Studio)
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
  此主題說明如何檢視 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)]中的「交易記錄傳送狀態」報表。 您可以在監視伺服器、主要伺服器或次要伺服器執行狀態報表。 若要查看有關記錄傳送組態的最完整資訊，請在監視伺服器執行個體中檢視報表。  
  
 此報表會顯示您所連接的伺服器執行個體中，有可用狀態的任何記錄傳送活動的狀態。 如果該伺服器執行個體牽涉到不同角色的多個組態 (例如，當做某個資料庫的監視伺服器，但同時又是另一個資料庫的次要伺服器)，則顯示的結果將包含每一個角色觀點的所有組態資訊。 如果預存程序可以透過給定的記錄傳送組態連接到監視伺服器執行個體，報表便可以顯示該組態的額外狀態。  
  
 對於目前伺服器執行個體所執行的每一個角色，您可以檢視下列資訊：  
  
|角色|顯示的資訊|  
|----------|---------------------------|  
|監視|使用此伺服器執行個體作為其監視伺服器的所有主要伺服器及次要伺服器，其名稱及狀態。|  
|Primary|對於每一個主要資料庫，此為目前伺服器執行個體 (作為主要伺服器) 的狀態與名稱，還有主要資料庫名稱。 報表會顯示備份作業 (儲存在主要伺服器的本機上) 的狀態。<br /><br /> 報表也會針對每一個對應的次要伺服器，各顯示一個資料列。 如果組態使用監視伺服器，且預存程序可以連接到監視伺服器，這些資料列會顯示最新記錄備份的複製狀態及還原狀態。|  
|次要|對於每一個次要資料庫，此為目前伺服器執行個體 (作為次要伺服器) 的狀態與名稱，還有次要資料庫名稱。<br /><br /> 報表會顯示次要伺服器中的複製及還原作業的狀態。<br /><br /> 報表也會針對所對應的主要伺服器，顯示一個資料列。 如果組態使用監視伺服器，且預存程序可以連接到監視伺服器，此資料列會顯示最新記錄備份的狀態。|  
  
 所顯示的資訊內容取決於伺服器執行個體是監視伺服器、主要伺服器或次要伺服器。 如果無法取得資訊，對應的資料格將呈現灰色。  
  
 此報表會呼叫 **sp_help_log_shipping_monitor** 以取得資料。 如需必要權限的相關資訊，請參閱 [sp_help_log_shipping_monitor &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-help-log-shipping-monitor-transact-sql.md)。  
  
### <a name="to-display-the-transaction-log-shipping-status-report-on-a-server-instance"></a>顯示伺服器執行個體上的交易記錄傳送狀態報表  
  
1.  連接到監視伺服器、主要伺服器或次要伺服器。  
  
2.  以滑鼠右鍵按一下 [物件總管] 中的伺服器執行個體，然後依序指向 [報表] 和 [標準報表]。  
  
3.  按一下 **[交易記錄傳送狀態]** 。  
  
## <a name="see-also"></a>另請參閱  
 [監視記錄傳送 &#40;Transact-SQL&#41;](../../database-engine/log-shipping/monitor-log-shipping-transact-sql.md)  
  
  
