---
title: 某些可用性複本沒有狀況良好的角色 | Microsoft Docs
description: 可用性複本角色狀態會檢查是否有任何可用性複本不是狀況良好的角色。
ms.custom: ''
ms.date: 05/17/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: conceptual
f1_keywords:
- sql13.swb.agdashboard.agp6allroleshealthy.issues.f1
helpviewer_keywords:
- Availability Groups [SQL Server], policies
ms.assetid: 7ec5b337-7201-4a66-a541-7560f8b18784
author: cawrites
ms.author: chadam
ms.openlocfilehash: db542dca2f4c200764e5065bff155c908fafcd25
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97641410"
---
# <a name="some-availability-replicas-do-not-have-a-healthy-role"></a>某些可用性複本沒有狀況良好的角色
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]
    
## <a name="introduction"></a>簡介  
  
|||  
|-|-|  
|**原則名稱**|可用性複本角色狀態|  
|**問題**|某些可用性複本沒有狀況良好的角色。|  
|**類別目錄**|**警告**|  
|**Facet**|可用性群組|  
  
## <a name="description"></a>描述  
 這項原則會積存所有可用性複本的連接狀態，並檢查是否有任何可用性複本沒有狀況良好的角色。 當可用性複本既不是主要也不是次要時，原則為狀況不良。 否則原則為狀況良好。  
  
> [!NOTE]  
>  在此版本 [!INCLUDE[ssCurrent](../../../includes/sscurrent-md.md)]中，可能原因和解決方案的資訊位於 TechNet Wiki 上的 [某些可用性複本沒有狀況良好的角色](https://go.microsoft.com/fwlink/p/?LinkId=220854) 。  
  
## <a name="possible-causes"></a>可能的原因  
 在此可用性群組中，至少一個可用性複本目前沒有主要或次要角色。  
  
## <a name="possible-solution"></a>可能的解決方案  
 使用可用性複本原則狀態，尋找其角色不是主要或次要的可用性複本，然後解決可用性複本上的問題。  
  
## <a name="see-also"></a>另請參閱  
 [AlwaysOn 可用性群組概觀 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server.md)   
 [使用 AlwaysOn 儀表板 &#40;SQL Server Management Studio&#41;](../../../database-engine/availability-groups/windows/use-the-always-on-dashboard-sql-server-management-studio.md)  
  
  
