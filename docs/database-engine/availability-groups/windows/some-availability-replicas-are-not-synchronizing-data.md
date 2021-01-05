---
title: 可用性複本並未同步處理資料
description: Always On 可用性群組中有一或多個可用性複本並未與主要複本同步處理資料的可能原因及解決方式。
ms.custom: seo-lt-2019
ms.date: 05/17/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: conceptual
f1_keywords:
- sql13.swb.agdashboard.agp4synchronizing.issues.f1
helpviewer_keywords:
- Availability Groups [SQL Server], policies
ms.assetid: 3db6a569-e942-4321-a0dd-c4ab002087c8
author: cawrites
ms.author: chadam
ms.openlocfilehash: d1b824e4b88ff5d8b0e31abcfcb6dca0acae815a
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97642088"
---
# <a name="some-availability-replicas-are-not-synchronizing-data"></a>部分可用性複本未同步處理資料
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]
    
## <a name="introduction"></a>簡介  
  
|||  
|-|-|  
|**原則名稱**|可用性複本資料同步處理狀態|  
|**問題**|某些可用性複本並未同步處理資料。|  
|**類別目錄**|**警告**|  
|**Facet**|可用性群組|  
  
## <a name="description"></a>描述  
 這項原則會積存可用性群組中所有可用性複本的資料同步處理狀態，並檢查是否有任何可用性複本的同步處理無法運作。 如果可用性複本的任何資料同步處理狀態為 NOT SYNCHRONIZING，原則為狀況不良。  
  
 如果可用性複本的任何資料同步處理狀態都不是 NOT SYNCHRONIZING，此原則為狀況良好。  
  
> [!NOTE]  
>  在此版本 [!INCLUDE[ssCurrent](../../../includes/sscurrent-md.md)]中，可能原因和解決方案的資訊位於 TechNet Wiki 上的 [某些可用性複本並未同步處理資料](https://go.microsoft.com/fwlink/p/?LinkId=220852) 。  
  
## <a name="possible-causes"></a>可能的原因  
 在這個可用性群組中，至少有一個次要複本具有 NOT SYNCHRONIZING 同步處理狀態，而且並未收到來自主要複本的資料。  
  
## <a name="possible-solution"></a>可能的解決方案  
 使用可用性複本原則狀態，尋找具有 NOT SYNCHROINIZING 狀態的可用性複本，然後解決可用性複本上的問題。  
  
## <a name="see-also"></a>另請參閱  
 [AlwaysOn 可用性群組概觀 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server.md)   
 [使用 AlwaysOn 儀表板 &#40;SQL Server Management Studio&#41;](../../../database-engine/availability-groups/windows/use-the-always-on-dashboard-sql-server-management-studio.md)  
  
  
