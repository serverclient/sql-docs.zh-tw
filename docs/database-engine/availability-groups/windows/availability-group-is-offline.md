---
title: 可用性群組為離線
description: 了解如何找出 Always On 可用性群組可能離線的時間與可能原因。
ms.custom: seodec18
ms.date: 05/17/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: end-user-help
f1_keywords:
- sql13.swb.agdashboard.agp2online.issues.f1
helpviewer_keywords:
- Availability Groups [SQL Server], policies
ms.assetid: 093c5208-bf7a-49f4-a546-72b48197cadf
author: cawrites
ms.author: chadam
ms.openlocfilehash: 56d79fedca8a5cdd0d91be2728430f5edbdab9ab
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97641194"
---
# <a name="always-on-availability-group-is-offline"></a>Always On 可用性群組為離線
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]
    
## <a name="introduction"></a>簡介  
  
|||  
|-|-|  
|**原則名稱**|可用性群組線上狀態|  
|**問題**|可用性群組為離線。|  
|**類別目錄**|**嚴重**|  
|**Facet**|可用性群組|  
  
## <a name="description"></a>描述  
 這項原則檢查可用性群組的線上或離線狀態。 當可用性群組的叢集資源為離線或可用性群組沒有主要複本時，原則為狀況不良並會引發警示。  
  
 當可用性群組的叢集資源為線上或可用性群組有主要複本時，原則為狀況良好。  
  
> [!NOTE]  
>  在此 [!INCLUDE[ssCurrent](../../../includes/sscurrent-md.md)]版本中，可能原因和解決方案的資訊位於 TechNet Wiki 上的 [Availability group is offline](https://go.microsoft.com/fwlink/p/?LinkId=220850) (可用性群組為離線)。  
  
## <a name="possible-causes"></a>可能的原因  
 這個問題可能是由於裝載主要複本的伺服器執行個體失敗或由於 Windows Server 容錯移轉叢集 (WSFC) 可用性群組資源離線所造成。 造成可用性群組離線的可能原因如下：  
  
-   可用性群組未設定為自動容錯移轉模式。 主要複本變為不可用，而且可用性群組中所有複本的角色變為 RESOLVING。  
  
    -   主要複本執行個體服務已關閉或沒有回應。  
  
    -   可用性群組與叢集之間發生連接問題。  
  
-   可用性群組設定為自動容錯移轉模式，但未成功完成。  
  
    -   在自動容錯移轉期間，目標複本的主要整備檢查失敗，但沒有複本可成為新的主要複本。  
  
-   叢集中的可用性群組資源變成離線。  
  
    -   任何相依叢集資源發生嚴重的問題，並成為離線。 可用性群組資源也處於離線狀態，直到相依的資源變為連線狀態。  
  
    -   叢集中的嚴重問題使可用性群組資源關閉。  
  
-   可用性群組正在進行自動、手動或強制的容錯移轉。  
  
## <a name="possible-solutions"></a>可能的解決方案  
 此問題的可能解決方案如下：  
  
-   如果主要複本的 SQL Server 執行個體已關閉，請重新啟動伺服器，然後確認可用性群組是否復原為狀況良好的狀態。  
  
-   如果自動容錯移轉似乎失敗，請確認複本上的資料庫是否與先前的主要複本同步處理，然後容錯移轉至主要複本。 如果資料庫未同步處理，請選取資料遺失最少的複本，然後復原為容錯移轉模式。  
  
-   如果在 SQL Server 執行個體似乎狀況良好時，叢集中的資源離線，請使用容錯移轉叢集管理員檢查叢集健全狀況或伺服器上的其他叢集問題。 您還可以使用容錯移轉叢集管理員，嘗試將可用性群組資源恢復上線。  
  
-   如果正在進行容錯移轉，請等候容錯移轉完成。  
  
## <a name="see-also"></a>另請參閱  
 [AlwaysOn 可用性群組概觀 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server.md)   
 [使用 AlwaysOn 儀表板 &#40;SQL Server Management Studio&#41;](../../../database-engine/availability-groups/windows/use-the-always-on-dashboard-sql-server-management-studio.md)  
  
  
