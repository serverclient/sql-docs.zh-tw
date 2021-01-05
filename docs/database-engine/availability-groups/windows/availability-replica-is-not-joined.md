---
title: 可用性複本並未聯結至可用性群組
description: 了解如何找出複本未加入 Always On 可用性群組的可能原因。
ms.custom: seodec18
ms.date: 05/17/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: troubleshooting
f1_keywords:
- sql13.swb.agdashboard.arp4joined.issues.f1
helpviewer_keywords:
- Availability Groups [SQL Server], policies
ms.assetid: 9c0d10b1-9e12-430c-83b9-ca2bd0a3afc4
author: cawrites
ms.author: chadam
ms.openlocfilehash: bda9848e0a3a85ea2d04f7450c683da49b52ce16
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97643187"
---
# <a name="availability-replica-is-not-joined-to-an-always-on-availability-group"></a>可用性複本並未聯結至 Always On 可用性群組
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]
    
## <a name="introduction"></a>簡介  
  
|||  
|-|-|  
|**原則名稱**|可用性複本聯結狀態|  
|**問題**|可用性複本未聯結。|  
|**類別目錄**|**警告**|  
|**Facet**|可用性複本|  
  
## <a name="description"></a>描述  
 這項原則會檢查可用性複本的聯結狀態。 當可用性複本已加入至可用性角色，但是沒有正確聯結時，原則為狀況不良。 否則原則為狀況良好。  
  
> [!NOTE]  
>  在此 [!INCLUDE[ssCurrent](../../../includes/sscurrent-md.md)]版本中，可能原因和解決方案的資訊位於 TechNet Wiki 上的 [Availability replica is not joined](https://go.microsoft.com/fwlink/p/?LinkId=220859) (可用性複本未聯結)。  
  
## <a name="possible-causes"></a>可能的原因  
 次要複本並未聯結至可用性群組。 若要讓可用性複本成功聯結至可用性群組，聯結狀態必須是聯結的獨立執行個體 (1) 或聯結的容錯移轉叢集 (2)。  
  
## <a name="possible-solution"></a>可能的解決方案  
 使用 Transact-SQL、PowerShell 或 SQL Server Management Studio，將次要複本聯結至可用性群組。 如需將次要複本聯結至可用性群組的詳細資訊，請參閱 [將次要複本聯結至可用性群組 (SQL Server)](https://msdn.microsoft.com/library/ff878473\(SQL.110\).aspx)。  
  
## <a name="see-also"></a>另請參閱  
 [AlwaysOn 可用性群組概觀 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server.md)   
 [使用 AlwaysOn 儀表板 &#40;SQL Server Management Studio&#41;](../../../database-engine/availability-groups/windows/use-the-always-on-dashboard-sql-server-management-studio.md)  
  
  
