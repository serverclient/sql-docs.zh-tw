---
description: 將相依性加入 SQL Server 資源
title: 將相依性新增至 SQL Server FCI 資源
descriptoin: Describes how to add dependencies to an Always On failover cluster instance (FCI) resource using the Failover Cluster Manager.
ms.custom: seo-lt-2019
ms.date: 03/14/2017
ms.prod: sql
ms.reviewer: ''
ms.technology: failover-cluster-instance
ms.topic: how-to
helpviewer_keywords:
- resource dependencies [SQL Server]
- failover clustering [SQL Server], dependencies
- clusters [SQL Server], dependencies
- dependencies [SQL Server], clustering
ms.assetid: 25dbb751-139b-4c8e-ac62-3ec23110611f
author: cawrites
ms.author: chadam
ms.openlocfilehash: f9582167d337ed9a06f95030f4c68857a4abd389
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97642835"
---
# <a name="add-dependencies-to-a-sql-server-resource"></a>將相依性加入 SQL Server 資源
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]
  本主題描述如何使用容錯移轉叢集管理員嵌入式管理單元，將相依性新增至 Always On 容錯移轉叢集執行個體 (FCI) 資源。 容錯移轉叢集管理員嵌入式管理單元是 Windows Server 容錯移轉叢集 (WSFC) 服務的叢集管理應用程式。  
  
-   **開始之前**  [限制事項](#Restrictions)、 [必要條件](#Prerequisites)  
  
-   **使用下列項目將相依性加入 SQL Server 資源** [Windows 容錯移轉叢集管理員](#WinClusManager)  
  
##  <a name="before-you-begin"></a><a name="BeforeYouBegin"></a> 開始之前  
  
###  <a name="limitations-and-restrictions"></a><a name="Restrictions"></a> 限制事項  
 如果將任何其他資源新增到 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 群組，那些資源必須永遠有唯一的 SQL 網路名稱資源以及它們自己的 SQL IP 位址資源，這是很重要的一點。  
  
 請不要在 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]以外的地方，使用現有的 SQL 網路名稱資源與 SQL IP 位址資源。 如果與其他資源共用 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 資源，可能會發生以下問題：  
  
-   可能會發生未預期的過時狀況。  
  
-   可能無法順利安裝 Service Pack。  
  
-   可能無法順利進行 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 安裝程式。 如果發生這個問題，您就無法安裝其他的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體，或執行例行的維護工作。  
  
 請考慮這些額外問題：  
  
-   具 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 複寫功能的 FTP：對於使用具 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 複寫功能之 FTP 的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體，您的 FTP 服務所使用的實體磁碟，必須與設定要使用 FTP 服務的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 安裝相同。  
  
-   [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 資源相依性：如果您將資源加入至 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 群組，而且要仰賴該 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 資源以確保能夠使用 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] ， [!INCLUDE[msCoName](../../../includes/msconame-md.md)] 建議您在 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Agent 資源上新增相依性。 請不要在 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 資源上新增相依性。 若要確定執行 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 的電腦維持在高度可用的狀態，請設定 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 資源，這樣如果 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 資源發生失敗，就不會影響到 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 群組。  
  
-   檔案共用與印表機資源：安裝「檔案共用」資源或「印表機」叢集資源時，不應該將它們放在跟執行 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]的電腦一樣的實體磁碟資源上。 如果它們都放在相同的實體磁碟資源上，效能可能會降低，而且執行 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]的電腦可能會失去服務能力。  
  
-   MS DTC 考慮事項：安裝作業系統並設定 FCI 之後，您必須使用容錯移轉叢集管理員嵌入式管理單元，設定 [!INCLUDE[msCoName](../../../includes/msconame-md.md)] 分散式交易協調器 (MS DTC) 在叢集中使用。 如果無法將 MS DTC 設定為搭配群集使用，也不會封鎖 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 安裝程式，但如果沒有正確設定 MS DTC，則 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 應用程式功能可能會受到影響。  
  
     如果將 MS DTC 安裝在 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 群組中，而且您有其他相依於 MS DTC 的資源，當此群組離線或發生容錯移轉時，MS DTC 將無法使用。 [!INCLUDE[msCoName](../../../includes/msconame-md.md)] 建議您將 MS DTC 放在本身具有實體磁碟資源的群組中 (如果可能)。  
  
###  <a name="prerequisites"></a><a name="Prerequisites"></a> 必要條件  
 如果將 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 安裝到具有多部磁碟機的 WSFC 資源群組中，並選擇將您的資料放在其中一部磁碟機上，則 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 資源會被設定為僅相依於該磁碟機。 若要將資料或記錄檔放到其他磁碟，您就必須先對那台磁碟的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 資源新增相依性。  
  
##  <a name="using-the-failover-cluster-manager-snap-in"></a><a name="WinClusManager"></a> 使用容錯移轉叢集管理員嵌入式管理單元  
 **加入 SQL Server 資源的相依性**  
  
-   開啟 [容錯移轉叢集管理員] 嵌入式管理單元。  
  
-   找出包含要依存之適用 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 資源的群組。  
  
-   如果這個群組中已經具有磁碟的資源，請移至步驟 4。 否則請尋找包含該磁碟的群組。 如果該群組與包含 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 的群組各由不同的節點所擁有，請將包含磁碟資源的群組移到擁有 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 群組的節點。  
  
-   選取 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 資源，開啟 **[屬性]** 對話方塊，並使用 **[相依性]** 索引標籤，將磁碟加入至 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 的相依性集合。  
  
  
