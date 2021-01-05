---
title: 變更容錯移轉叢集執行個體的 IP 位址
description: 了解如何使用容錯移轉叢集管理員，變更 SQL Server 容錯移轉叢集執行個體的 IP 位址。
ms.custom: seo-lt-2019
ms.date: 03/14/2017
ms.prod: sql
ms.reviewer: ''
ms.technology: failover-cluster-instance
ms.topic: how-to
helpviewer_keywords:
- modifying IP addresses
- failover clustering [SQL Server], IP addresses
- IP addresses [SQL Server]
- clusters [SQL Server], IP addresses
ms.assetid: b685f400-cbfe-4c5d-a070-227a1123dae4
author: cawrites
ms.author: chadam
ms.openlocfilehash: 96dfbacbdc162768b13a56c7a36c61eabecee79a
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97642789"
---
# <a name="change-the-ip-address-of-a-failover-cluster-instance"></a>變更容錯移轉叢集執行個體的 IP 位址
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]
  此主題描述如何使用容錯移轉叢集管理員嵌入式管理單元，在 AlwaysOn 容錯移轉叢集執行個體 (FCI) 中變更 IP 位址資源。 容錯移轉叢集管理員嵌入式管理單元是 Windows Server 容錯移轉叢集 (WSFC) 服務的叢集管理應用程式。  
  
-   **開始之前：** [安全性](#Security)  
  
##  <a name="before-you-begin"></a><a name="BeforeYouBegin"></a> 開始之前  
 開始之前，請先檢閱下列《 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 線上叢書》主題：[安裝容錯移轉叢集之前](../../../sql-server/failover-clusters/install/before-installing-failover-clustering.md)。  
  
###  <a name="security"></a><a name="Security"></a> Security  
  
####  <a name="permissions"></a><a name="Permissions"></a> 權限  
 若要維護或更新 FCI，您必須是本機系統管理員，並且具有能在 FCI 的所有節點上以服務登入的權限。  
  
##  <a name="using-the-failover-cluster-manager-snap-in"></a><a name="WSFC"></a> 使用容錯移轉叢集管理員嵌入式管理單元  
 **若要使用 FCI 變更 IP 位址資源**  
  
1.  開啟 [容錯移轉叢集管理員] 嵌入式管理單元。  
  
2.  展開 **[服務和應用程式]** 節點，在左窗格中按一下 [FCI]。  
  
3.  在右窗格的 [伺服器名稱]  類別下，以滑鼠右鍵按一下 [SQL Server 執行個體]，並選取 [屬性]  選項開啟 [屬性]  對話方塊。  
  
4.  在 **[一般]** 索引標籤上，變更 IP 位址資源。  
  
5.  按一下 **[確定]** ，關閉對話方塊。  
  
6.  在右窗格中，以滑鼠右鍵按一下 [SQL IP 位址1(容錯移轉叢集執行個體名稱)]，然後選取 [離線工作]  。 您會發現 SQL IP 位址1(容錯移轉叢集執行個體名稱)、SQL 網路名稱(容錯移轉叢集執行個體名稱) 及 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 的狀態，會從 [線上] 變更為 [離線暫止]，再變成 [離線]。  
  
7.  在右窗格中，以滑鼠右鍵按一下 [[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]]，然後選取 [線上工作]  。 您會發現 SQL IP 位址1(容錯移轉叢集執行個體名稱)、SQL 網路名稱(容錯移轉叢集執行個體名稱) 與 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 的狀態，會從 [離線] 變更為 [線上暫止]，再變成 [線上]。  
  
8.  關閉 [容錯移轉叢集管理員] 嵌入式管理單元。  
  
  
