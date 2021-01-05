---
title: 可用性群組的可用性資料庫已暫停
description: 找出 Always On 可用性群組中資料庫或許已暫停的可能原因。
ms.custom: seodec18
ms.date: 05/17/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: end-user-help
f1_keywords:
- sql13.swb.agdashboard.drp1notsuspended.issues.f1
helpviewer_keywords:
- Availability Groups [SQL Server], policies
ms.assetid: 6baee70f-848c-4e86-b20d-78875c0f82cb
author: cawrites
ms.author: chadam
ms.openlocfilehash: 5843b82c3be84acf24e04ab9dac4b616bed857a6
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97641223"
---
# <a name="availability-database-is-suspended-for-an-availability-group"></a>可用性群組的可用性資料庫已暫停
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]
    
## <a name="introduction"></a>簡介  
  
|||  
|-|-|  
|**原則名稱**|可用性資料庫暫停狀態|  
|**問題**|可用性資料庫已暫停。|  
|**類別目錄**|**警告**|  
|**Facet**|可用性資料庫|  
  
## <a name="description"></a>描述  
 此原則會檢查次要資料庫 (也稱為「次要資料庫複本」) 的資料移動。 當資料移動已暫停時，原則為狀況不良。 否則原則為狀況良好。  
  
> [!NOTE]  
>  在此版本 [!INCLUDE[ssCurrent](../../../includes/sscurrent-md.md)]中，可能原因和解決方案的資訊位於 TechNet Wiki 上的 [可用性資料庫已暫停](https://go.microsoft.com/fwlink/p/?LinkId=220860) 。  
  
## <a name="possible-causes"></a>可能的原因  
 由於下列原因，此可用性資料庫上的資料同步處理可能已暫停：  
  
-   由於錯誤，系統可能已暫停資料同步處理。  
  
-   資料庫管理員可能為了進行維護已暫停資料同步處理。  
  
## <a name="possible-solution"></a>可能的解決方案  
 繼續資料同步處理。 如果問題仍然存在，請檢查事件記錄檔中的可用性群組，然後診斷為什麼系統暫停資料移動。  
  
## <a name="see-also"></a>另請參閱  
 [AlwaysOn 可用性群組概觀 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server.md)   
 [使用 AlwaysOn 儀表板 &#40;SQL Server Management Studio&#41;](../../../database-engine/availability-groups/windows/use-the-always-on-dashboard-sql-server-management-studio.md)  
  
  
