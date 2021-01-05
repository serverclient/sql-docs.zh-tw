---
title: 複本針對可用性群組沒有健康情況良好的角色
description: 找出 Always On 可用性群組內的複本沒有健康情況良好角色的可能原因。
ms.custom: seo-lt-2019
ms.date: 05/17/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: troubleshooting
f1_keywords:
- sql13.swb.agdashboard.arp1rolehealthy.issues.f1
helpviewer_keywords:
- Availability Groups [SQL Server], policies
ms.assetid: ebb2c9f4-2097-4688-b4fb-8f0571047317
author: cawrites
ms.author: chadam
ms.openlocfilehash: e5ef8c8a912854c48489cb5860bb3813bdc6fd10
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97643165"
---
# <a name="availability-replica-does-not-have-a-healthy-role-for-an-always-on-availability-group"></a>Always On 可用性群組的可用性複本沒有狀況良好的角色
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]
    
## <a name="introduction"></a>簡介  
  
|||  
|-|-|  
|**原則名稱**|可用性複本的角色狀態|  
|**問題**|可用性複本沒有狀況良好的角色。|  
|**類別目錄**|**嚴重**|  
|**Facet**|可用性複本|  
  
## <a name="description"></a>描述  
 這項原則檢查可用性複本的角色狀態。 當可用性複本的角色既不是主要也不是次要時，原則為狀況不良。 否則原則為狀況良好。  
  
> [!NOTE]  
>  在此版本 [!INCLUDE[ssCurrent](../../../includes/sscurrent-md.md)]中，可能原因和解決方案的資訊位於 TechNet Wiki 上的 [可用性複本沒有狀況良好的角色](https://go.microsoft.com/fwlink/p/?LinkId=220856) 。  
  
## <a name="possible-causes"></a>可能的原因  
 可用性複本的角色狀態為狀況不良。 複本沒有主要或次要角色。  
  
## <a name="possible-solution-information_still_to_come"></a>可能的解決方案：Information_still_to_come  
  
## <a name="see-also"></a>另請參閱  
 [AlwaysOn 可用性群組概觀 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server.md)   
 [使用 AlwaysOn 儀表板 &#40;SQL Server Management Studio&#41;](../../../database-engine/availability-groups/windows/use-the-always-on-dashboard-sql-server-management-studio.md)  
  
  
