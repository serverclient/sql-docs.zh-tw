---
title: 從容錯移轉叢集執行個體失敗的狀況中復原
description: 了解如何在 SQL Server 中使用容錯移轉叢集管理員嵌入式管理單元，從容錯移轉叢集執行個體的容錯移轉中復原。
ms.custom: seo-lt-2019
ms.date: 03/14/2017
ms.prod: sql
ms.reviewer: ''
ms.technology: failover-cluster-instance
ms.topic: how-to
helpviewer_keywords:
- clusters [SQL Server], recovery from failure
- failover clustering [SQL Server], recovery from failure
- hardware failures [SQL Server]
- recovering failover cluster failures [SQL Server]
ms.assetid: 3d151d0c-e841-4325-8606-c094de37d7d1
author: cawrites
ms.author: chadam
ms.openlocfilehash: 91e18137748b34012c73860071fa0be93af0277f
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97642738"
---
# <a name="recover-from-failover-cluster-instance-failure"></a>從容錯移轉叢集執行個體失敗的狀況中復原
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]
  此主題描述在 [!INCLUDE[ssCurrent](../../../includes/sscurrent-md.md)]發生容錯移轉之後，如何使用容錯移轉叢集管理員嵌入式管理單元，從叢集失敗中復原。 容錯移轉叢集管理員嵌入式管理單元是 Windows Server 容錯移轉叢集 (WSFC) 服務的叢集管理應用程式。  
  
-   [從無法修復的失敗中復原](#Scenario1)  
  
-   [從軟體失敗中復原](#Scenario2)  
  
##  <a name="recover-from-an-irreparable-failure"></a><a name="Scenario1"></a> 從無法修復的失敗中復原  
 您可以使用下列步驟從無法修復的失敗中復原。 舉例來說，這種失敗可能是磁碟控制卡或作業系統失敗所引起。 在此情況下，失敗是兩節點叢集之節點 1 中的硬體故障所造成。  
  
1.  在節點 1 失敗之後， [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] FCI 會容錯移轉至節點 2。  
  
2.  從 FCI 中收回節點 1。 作法是從節點 2 開啟容錯移轉叢集管理員嵌入式管理單元，並以滑鼠右鍵按一下 [節點 1]，按一下 [移動動作]  ，然後按一下 [收回節點]  。  
  
3.  確認節點 1 已從叢集定義中移出。  
  
4.  安裝新硬體來取代節點 1 中發生錯誤的硬體。  
  
5.  使用容錯移轉叢集管理員嵌入式管理單元，將節點 1 加入至現有叢集。 如需詳細資訊，請參閱 [安裝容錯移轉叢集之前](../../../sql-server/failover-clusters/install/before-installing-failover-clustering.md)。  
  
6.  確定所有叢集節點上的所有系統管理員帳戶都相同。  
  
7.  執行 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 安裝程式，將節點 1 加入至 FCI。 如需詳細資訊，請參閱[在 SQL Server 容錯移轉叢集中新增或移除節點 &#40;安裝程式&#41;](../../../sql-server/failover-clusters/install/add-or-remove-nodes-in-a-sql-server-failover-cluster-setup.md)。  
  
##  <a name="recover-from-a-reparable-failure"></a><a name="Scenario2"></a> 從可修復的失敗中復原  
 您可以使用下列步驟從可修復的失敗中復原。 在此情況下，錯誤是節點 1 當機或離線所造成，但並非無可挽回的嚴重錯誤。 這種失敗可能是因為作業系統失敗、硬體故障或 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體本身失敗所導致。  
  
1.  在節點 1 失敗之後，FCI 會容錯移轉至節點 2。  
  
2.  解決節點 1 產生的問題。  
  
3.  確定所有節點都在線上，而且 WSFC 服務正在運作。  
  
4.  將 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 容錯移轉至復原的節點。  
  
  
