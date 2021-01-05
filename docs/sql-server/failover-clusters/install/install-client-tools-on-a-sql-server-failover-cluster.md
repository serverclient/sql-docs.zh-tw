---
title: 安裝用戶端工具：容錯移轉叢集
description: 了解如何在 SQL Server 容錯移轉執行個體上安裝用戶端工具，例如 SQL Server Management Studio。
ms.custom: seo-lt-2019
ms.date: 12/13/2019
ms.reviewer: ''
ms.prod: sql
ms.technology: failover-cluster-instance
ms.topic: how-to
ms.assetid: 3c82d510-9798-46be-bebb-cac9bef56936
author: cawrites
ms.author: chadam
ms.openlocfilehash: a596212e17e6fb273b9a6c4f5a2548aff1bc1e7f
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97642833"
---
# <a name="install-client-tools-on-a-sql-server-failover-cluster"></a>在 SQL Server 容錯移轉叢集上安裝用戶端工具
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]
  [!INCLUDE[ssManStudioFull](../../../includes/ssmanstudiofull-md.md)] 之類的用戶端工具是在相同機器上所有執行個體通用的共用功能。 這些功能與可以並排安裝的支援 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 版本回溯相容。 在一個節點上一次只能存在一個版本的用戶端工具。  
  
 如果在安裝 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 叢集的第一個節點期間安裝 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 用戶端工具，則會使用 [加入節點]，將這些用戶端工具自動加入至稍後可能會加入至 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體的所有節點。  
  
> [!IMPORTANT]  
>  [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 線上叢書》自動加入至要加入至 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 叢集的其他節點。 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 線上叢書》手動安裝在您想要擁有《 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 線上叢書》本機副本的節點上。  
  
 如果您沒有在一開始安裝 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 叢集期間安裝 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 用戶端工具，您可以稍後再進行安裝，如以下程序所述。  
  
## <a name="installation-procedures"></a>安裝程序  
  
#### <a name="installing-ssnoversion-client-tools-using-the-setup-user-interface"></a>使用安裝程式使用者介面安裝 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 用戶端工具  
  
1.  插入 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 安裝媒體。 在根安裝資料夾中，按兩下 Setup.exe。 若要從網路共用進行安裝，請找出共用上的根資料夾，然後按兩下 Setup.exe。  
  
2.  在 [安裝] 頁面上，按一下 [新的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 獨立安裝或將功能加入到現有安裝]。 請不要按 [新的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 容錯移轉叢集安裝]。  
  
3.  系統組態檢查將會先確認電腦的系統狀態，然後安裝程式才會繼續進行。  
  
4.  在 [安裝類型] 頁面上，按一下 [執行 [!INCLUDE[ssCurrent](../../../includes/sscurrent-md.md)] 的新安裝]。  
  
5.  在 **[特徵選取]** 頁面上，選取您要安裝的工具，然後遵循安裝程序的其餘步驟進行。  
  
#### <a name="installing-ssnoversion-client-tools-at-the-command-prompt"></a>在命令提示字元安裝 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 用戶端工具  
  
1.  若要安裝 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 用戶端工具與《[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 線上叢書》，請執行下列命令：Setup.exe/q/Action=Install /Features=Tools  
  
2.  如果只要安裝基本 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 管理工具，請執行下列命令：Setup.exe/q/Action=Install Features=SSMS。 這會針對 [!INCLUDE[ssDEnoversion](../../../includes/ssdenoversion-md.md)]、[!INCLUDE[ssExpress](../../../includes/ssexpress-md.md)]、sqlcmd 公用程式，以及 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] PowerShell 提供者，安裝 [!INCLUDE[ssManStudio](../../../includes/ssmanstudio-md.md)] 支援。  
  
3.  如果要安裝完整的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 管理工具，請執行下列命令：Setup.exe/q/Action=Install /Features=ADV_SSMS。 如需功能參數值的詳細資訊，請參閱 [從命令提示字元安裝 SQL Server 2016](../../../database-engine/install-windows/install-sql-server-from-the-command-prompt.md)。  
  
### <a name="uninstalling-ssnoversion-client-tools"></a>解除安裝 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 用戶端工具  
 這些用戶端工具在 [控制台] 的 [新增或移除程式] 中顯示為 **[!INCLUDE[msCoName](../../../includes/msconame-md.md)][!INCLUDE[ssCurrent](../../../includes/sscurrent-md.md)]** ，而且可以從該處移除。 當您使用 [移除節點] 從容錯移轉叢集解除安裝 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 的執行個體時，不會同時解除安裝用戶端元件。  
  
## <a name="see-also"></a>另請參閱  
 [檢視與讀取 SQL Server 安裝程式記錄檔](../../../database-engine/install-windows/view-and-read-sql-server-setup-log-files.md)  
  
