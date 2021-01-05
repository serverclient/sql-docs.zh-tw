---
title: 移除容錯移轉叢體執行個體
description: 使用此程序可解除安裝 Always On 容錯移轉叢集執行個體。 在繼續前，本文包含重要的考量事項。
ms.custom: seo-lt-2019
ms.date: 12/13/2019
ms.prod: sql
ms.reviewer: ''
ms.technology: failover-cluster-instance
ms.topic: how-to
helpviewer_keywords:
- clusters [SQL Server], removing failover cluster instance
- failover clustering [SQL Server], removing failover cluster instance
- uninstalling failover cluster instances
- removing failover cluster instances
ms.assetid: bf63353b-69cf-4c5c-98ea-7b151e36537f
author: cawrites
ms.author: chadam
ms.openlocfilehash: 89e3cbbaeca927671a340e3fe4178e6eb5d807e8
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97642863"
---
# <a name="remove-a-failover-cluster-instance-setup"></a>移除容錯移轉叢集執行個體 (安裝程式)

[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]

使用此程序可解除安裝 Always On [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 容錯移轉叢集執行個體。  
  
> [!IMPORTANT]  
>  若要更新或移除 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 容錯移轉叢集，您必須是本機系統管理員，並且具有在 Windows Server 容錯移轉叢集的所有節點上登入為服務的權限。  
  
 **開始之前**  
  
 在解除安裝 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 容錯移轉叢集執行個體之前，請先考量下列重點：  
  
-   若意外解除安裝 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Native Client， [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 資源將無法啟動。 若要重新安裝 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Native Client，請執行 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 安裝程式以安裝 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 必要元件。  
  
-   如果解除安裝具有多個 SQL IP 叢集資源的容錯移轉叢集，您必須使用容錯移轉叢集管理員或 PowerShell 移除其他 SQL IP 資源。  
  
 如需命令提示字元語法的資訊，請參閱 [從命令提示字元安裝 SQL Server 2016](../../../database-engine/install-windows/install-sql-server-from-the-command-prompt.md)。  
  
### <a name="to-uninstall-a-ssnoversion-failover-cluster-instance"></a>解除安裝 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 容錯移轉叢集執行個體
  
1.  若要解除安裝 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 容錯移轉叢集，請使用 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 安裝程式所提供的「移除節點」功能來分別移除每個節點。 如需詳細資訊，請參閱[在 Always On 容錯移轉叢集中新增或移除節點 (安裝程式)](../../../sql-server/failover-clusters/install/add-or-remove-nodes-in-a-sql-server-failover-cluster-setup.md)。  
  
## <a name="see-also"></a>另請參閱  
 [檢視與讀取 SQL Server 安裝程式記錄檔](../../../database-engine/install-windows/view-and-read-sql-server-setup-log-files.md)  
  
