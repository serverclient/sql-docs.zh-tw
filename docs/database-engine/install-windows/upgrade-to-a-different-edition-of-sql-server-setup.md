---
title: 升級為不同的版本
description: SQL Server 安裝程式支援各種 SQL Server 版本之間的版本升級。 開始版本升級之前，請先檢閱本文中的資源。
ms.custom: seo-lt-2019
ms.date: 12/13/2019
ms.prod: sql
ms.reviewer: ''
ms.technology: install
ms.topic: conceptual
ms.assetid: 31d16820-d126-4c57-82cc-27701e4091bc
author: cawrites
ms.author: chadam
monikerRange: '>=sql-server-2016'
ms.openlocfilehash: 87f1521927518446b355fea828e50901d6e35bc1
ms.sourcegitcommit: 3ec49252e82590de0fe559a8574606ae213f6f3b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/07/2021
ms.locfileid: "97975525"
---
# <a name="upgrade-to-a-different-edition-of-sql-server-setup"></a>升級為不同的 SQL Server 版本 (安裝程式)

[!INCLUDE [SQL Server -Windows Only](../../includes/applies-to-version/sql-windows-only.md)]

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式支援各種不同 [!INCLUDE[ssNoversion](../../includes/ssnoversion-md.md)] 版本之間的版本升級。 如需支援版本升級方式的詳細資訊，請參閱 [支援的版本與版本升級](../../database-engine/install-windows/supported-version-and-edition-upgrades-2017.md)。 在您起始 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 執行個體的版本升級之前，請檢閱以下文章：  

  [SQL Server 2019 的版本及支援功能](../../sql-server/editions-and-components-of-sql-server-version-15.md)
- [版本及支援的 SQL Server 2017 功能](../../sql-server/editions-and-components-of-sql-server-2017.md)  
- [版本及支援的 SQL Server 2016 功能](../../sql-server/editions-and-components-of-sql-server-2016.md)  
- [SQL Server 版本的計算容量限制](../../sql-server/compute-capacity-limits-by-edition-of-sql-server.md)  
- [安裝 SQL Server 的硬體與軟體需求](../../sql-server/install/hardware-and-software-requirements-for-installing-sql-server.md)  
  
> [!NOTE]  
> **容錯移轉叢集執行個體上的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]：** 在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 容錯移轉叢集執行個體的其中一個節點上執行版本升級就已足夠。 這個節點可以是主動或被動節點，而且引擎不會在版本升級期間讓資源離線。 版本升級之後，您必須重新啟動 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體或容錯移轉至不同的節點。  
  
## <a name="prerequisites"></a>必要條件  
如果是本機安裝，您必須以管理員身分執行安裝程式。 如果您是從遠端共用位置安裝 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ，則必須使用對遠端共用位置具有讀取權限的網域帳戶。  
  
> [!IMPORTANT]  
> 若要啟動 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 版本變更，您必須重新啟動 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 服務。 這樣會導致應用程式關閉，同時服務會離線。  
  
## <a name="procedure"></a>程序  
  
### <a name="to-upgrade-to-a-different-edition-of-ssnoversion"></a>若要升級至不同的 [!INCLUDE[ssNoversion](../../includes/ssnoversion-md.md)] 版本  
  
1.  插入 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝媒體。 在根資料夾中按兩下 setup.exe，或從 [組態工具] 啟動 [ [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝中心]。 若要從網路共用進行安裝，請找出共用上的根資料夾，然後按兩下 Setup.exe。  
  
2.  若要將現有的 [!INCLUDE[ssNoversion](../../includes/ssnoversion-md.md)] 執行個體升級至不同的版本，請在 [ [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝中心] 中，按一下 **[維護]** ，然後選取 **[版本升級]** 。  
  
3.  如果需要安裝程式支援檔案， [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式就會安裝這些檔案。 如果系統指示您重新啟動電腦，請先重新啟動，然後再繼續進行。  
  
4.  系統組態檢查會在電腦上執行探索作業。 若要繼續進行，請按一下 **[確定]** 。  
  
5.  在 [產品金鑰] 頁面上，選取選項按鈕，指出您要升級至免費的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]版本，還是您擁有產品之實際執行版本的 PID 金鑰。 如需詳細資訊，請參閱 [SQL Server 的版本和元件](../../sql-server/editions-and-components-of-sql-server-2017.md)和[支援的版本與版本升級](../../database-engine/install-windows/supported-version-and-edition-upgrades.md)。  
  
6.  在 [授權條款] 頁面上，閱讀授權合約，然後選取要接受授權條款和條件的核取方塊。 若要繼續進行，請按 **[下一步]** 。 若要結束安裝程式，請按一下 **[取消]** 。  
  
7.  在 [選取執行個體] 頁面中，指定要升級的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體。  
  
8.  [版本升級規則] 頁面會驗證您的電腦組態，然後版本升級作業才會開始。  
  
9. [已完成升級版本的準備工作] 頁面會顯示在安裝期間指定之安裝選項的樹狀檢視。 若要繼續，請按一下 **[升級]** 。  
  
10. 在版本升級程序期間，您必須重新啟動這些服務，才能挑選新的設定。 版本升級之後，[完成] 頁面會提供版本升級之摘要記錄檔的連結。 若要關閉精靈，請按一下 **[關閉]** 。  
  
11. [完成] 頁面會提供安裝和其他重要注意事項之摘要記錄檔的連結。  
  
12. 如果指示您重新啟動電腦，請立刻執行。 當您完成安裝時，請務必閱讀安裝精靈所提供的訊息。 如需安裝程式記錄檔的資訊，請參閱 [檢視與讀取 SQL Server 安裝程式記錄檔](../../database-engine/install-windows/view-and-read-sql-server-setup-log-files.md)。  
  
13. 如果您是從 [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)]升級，就必須執行一些額外的步驟，然後才能使用升級的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]執行個體：  
  
    -   在 Windows SCM 中啟用 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent 服務。  
  
    -   使用 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 組態管理員來提供 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent 服務帳戶。  
  
 除了上述步驟以外，如果您是從 [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)]升級，可能必須進行下列動作：  
  
-   在 [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)] 中提供的使用者會在升級之後維持已提供的狀態。 更明確地說，BUILTIN\Users 群組會維持已提供的狀態。 您可以視需要停用、移除或重新提供這些帳戶。 如需詳細資訊，請參閱 [設定 Windows 服務帳戶與權限](../../database-engine/configure-windows/configure-windows-service-accounts-and-permissions.md)預覽版本升級問題的解答。  
  
-   tempdb 和 model 系統資料庫的大小和復原模式在升級之後會維持不變。 您可以視需要重新設定這些設定。 如需詳細資訊，請參閱[系統資料庫的備份與還原 &#40;SQL Server&#41;](../../relational-databases/backup-restore/back-up-and-restore-of-system-databases-sql-server.md)。  
  
-   範本資料庫在升級之後會保留在電腦上。  

> [!NOTE]  
> 如果 Engine_SqlEngineHealthCheck 規則上的程序失敗，則可使用命令列安裝選項來略過此特定規則，讓升級程序順利完成。 若要略過檢查此規則，請開啟命令提示字元，並變更為包含 SQL Server 安裝程式 (Setup.exe) 的路徑。 然後，鍵入下列命令： 

```console
setup.exe /q /ACTION=editionupgrade /InstanceName=MSSQLSERVER /PID=<appropriatePid> /SkipRules=Engine_SqlEngineHealthCheck
```


## <a name="see-also"></a>另請參閱  
 [升級 SQL Server](../../database-engine/install-windows/upgrade-sql-server.md)   
 [回溯相容性_已刪除](/previous-versions/sql/sql-server-2016/cc280407(v=sql.130))  
  
