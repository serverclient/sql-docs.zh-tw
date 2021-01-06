---
title: 將記錄傳送升級為 SQL Server 2016 和更新版本
description: 了解從舊版升級至 SQL Server 2016 和更新版本時，保留記錄傳送災害復原解決方案的適當順序。
ms.custom: seo-lt-2019
ms.date: 02/01/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: log-shipping
ms.topic: conceptual
helpviewer_keywords:
- log shipping [SQL Server], upgrading
ms.assetid: b1289cc3-f5be-40bb-8801-0e3eed40336e
author: cawrites
ms.author: chadam
ms.openlocfilehash: 0b3e98cc2f5ce76093f3710f6f49e1b186219c04
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97643839"
---
# <a name="upgrading-log-shipping-to-sql-server-2016-transact-sql"></a>將記錄傳送升級至 SQL Server 2016 (Transact-SQL)
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
  從 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 記錄傳送設定升級至新的 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 版本、新的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Service Pack 或 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 累積更新時，以適當順序升級您的記錄傳送伺服器將會保留記錄傳送災害復原解決方案。  
  
> [!NOTE]  
>  [中所導入的＜](../../relational-databases/backup-restore/backup-compression-sql-server.md) 備份壓縮 [!INCLUDE[ssEnterpriseEd10](../../includes/ssenterpriseed10-md.md)]＞。 升級的記錄傳送組態會使用 **備份壓縮預設** 伺服器層級組態選項，來控制備份壓縮是否會用於交易記錄備份檔案。 可以針對每一個記錄傳送組態來指定記錄備份的備份壓縮行為。 如需詳細資訊，請參閱[設定記錄傳送 &#40;SQL Server&#41;](../../database-engine/log-shipping/configure-log-shipping-sql-server.md)。  
  
 **本主題內容：**  
  
-   [必要條件](#Prerequisites)  
  
-   [在升級之前保護資料](#ProtectData)  
  
-   [升級監視伺服器執行個體](#UpgradeMonitor)  
  
-   [升級次要伺服器執行個體](#UpgradeSecondaries)  
  
-   [升級主要執行個體](#UpgradePrimary)  
  
##  <a name="prerequisites"></a><a name="Prerequisites"></a> 必要條件  
 在開始之前，請檢閱以下重要資訊：  
  
-   [Supported Version and Edition Upgrades](../../database-engine/install-windows/supported-version-and-edition-upgrades.md)︰確認您可從您的 Windows 作業系統版本與 SQL Server 版本升級至 SQL Server 2016。 例如，您無法直接從 SQL Server 2005 執行個體升級至 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)]。  
  
-   [Choose a Database Engine Upgrade Method](../../database-engine/install-windows/choose-a-database-engine-upgrade-method.md)︰根據您檢閱的支援版本與版本升級，選取適當的升級方法和步驟，此外亦根據作業環境中安裝的其他元件，依正確順序升級元件。  
  
-   [計劃和測試資料庫引擎升級計劃](../../database-engine/install-windows/plan-and-test-the-database-engine-upgrade-plan.md)︰檢閱版本資訊與已知的升級問題、升級前檢查清單，並開發和測試升級計畫。  
  
-   [安裝 SQL Server 2016 的硬體與軟體需求](../../sql-server/install/hardware-and-software-requirements-for-installing-sql-server.md)：檢閱安裝 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)]的軟體需求。 如果需要其他軟體，請先將其安裝在每個節點上，然後開始升級程序，以將任何停機時間降到最低。  
  
##  <a name="protect-your-data-before-the-upgrade"></a><a name="ProtectData"></a> 在升級之前保護資料  
 我們建議的最佳做法是在升級記錄傳送之前先保護資料。  
  
 **保護資料**  
  
1.  在每一個主要資料庫上執行完整資料庫備份。  
  
     如需詳細資訊，請參閱 [建立完整資料庫備份 &#40;SQL Server&#41;](../../relational-databases/backup-restore/create-a-full-database-backup-sql-server.md)中建立差異資料庫備份。  
  
2.  在每一個主要資料庫上執行 [DBCC CHECKDB](../../t-sql/database-console-commands/dbcc-checkdb-transact-sql.md) 命令。  
  
> [!IMPORTANT]  
>  請確定主要伺服器上有足夠的空間，可針對預計進行次要伺服器升級的時間保存記錄備份複本。  如果您容錯移轉到次要伺服器，則這個相同考量也適用於次要伺服器 (新的主要伺服器)。  
  
##  <a name="upgrading-the-optional-monitor-server-instance"></a><a name="UpgradeMonitor"></a> 升級 (選用) 監視伺服器執行個體  
 可以隨時升級監視伺服器執行個體 (如果有的話)。 不過，當您升級主要和次要伺服器時，不需要升級選用的監視伺服器。  
  
 在升級監視伺服器時，記錄傳送組態會持續有效，但是監視器上的資料表中不會記錄它的狀態。 當升級監視伺服器時，將不會觸發任何已經設定的警示。 在升級之後，您可以執行 [sp_refresh_log_shipping_monitor](../../relational-databases/system-stored-procedures/sp-refresh-log-shipping-monitor-transact-sql.md) 系統預存程序來更新監視資料表內的資訊。   如需監視伺服器的詳細資訊，請參閱[關於記錄傳送 &#40;SQL Server&#41;](../../database-engine/log-shipping/about-log-shipping-sql-server.md)。  
  
##  <a name="upgrading-the-secondary-server-instances"></a><a name="UpgradeSecondaries"></a> 升級次要伺服器執行個體  
 升級程序包括先將 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的次要伺服器執行個體升級至 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] ，然後再升級主要伺服器執行個體。 一律先升級次要 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體。 記錄傳送會在整個升級過程中繼續，因為升級的次要伺服器執行個體會繼續從 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 主要伺服器執行個體還原記錄備份。 如果在升級次要伺服器執行個體之前先升級主要伺服器執行個體，則記錄傳送將會失敗，因為在更新版的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 上所建立的備份將無法在舊版 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]上還原。 您可以同時或循序升級次要執行個體，但必須在升級主要執行個體之前先升級所有次要執行個體，以避免記錄傳送失敗。  
  
 升級次要伺服器執行個體時，記錄傳送複製和還原作業不會執行。 這表示未還原的交易記錄備份將累積在主要伺服器上，而您必須擁有足夠的空間來容納這些未還原的備份。 累積的數量取決於主要伺服器執行個體上排定的備份頻率，以及您升級次要執行個體的順序。 此外，如果已經設定了個別的監視伺服器，則可能會引發警示，指出還原執行的時間長度尚未超過設定的間隔。  
  
 一旦升級次要伺服器之後，記錄傳送代理程式作業就會繼續執行，且繼續從主要伺服器執行個體複製記錄備份並還原到次要伺服器執行個體。 次要伺服器執行個體讓次要資料庫變成最新狀態所需的時間會有所差異，取決於升級次要伺服器執行個體所花費的時間以及主要伺服器上的備份頻率而定。  
  
> [!NOTE]  
>  在伺服器升級期間，次要資料庫本身不會升級至 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 資料庫。 只有當次要資料庫是透過初始記錄傳送資料庫的容錯移轉上線時，才會進行升級。 理論上，這種狀況可能會無限期保留。 初始容錯移轉時，升級資料庫中繼資料的時間量很少。  
  
> [!IMPORTANT]  
>  需要升級的資料庫不支援 RESTORE WITH STANDBY 選項。 如果已升級的次要資料庫已經使用 RESTORE WITH STANDBY 加以設定，升級之後無法再還原交易記錄。 若要繼續進行該次要資料庫上的記錄傳送，您需要在該部待命伺服器上再次設定記錄傳送。 如需 STANDBY 選項的詳細資訊，請參閱[還原交易記錄備份 &#40;SQL Server&#41;](../../relational-databases/backup-restore/restore-a-transaction-log-backup-sql-server.md)。  
  
##  <a name="upgrading-the-primary-server-instance"></a><a name="UpgradePrimary"></a> 升級主要伺服器執行個體  
 由於記錄傳送主要是一個災害復原解決方案，因此，最簡單且最常見的案例就是就地升級主要執行個體，而且在此升級期間，資料庫將完全無法使用。 升級伺服器之後，資料庫就會自動回到線上，以便進行升級。 在升級資料庫之後，記錄傳送作業就會繼續。  
  
> [!NOTE]  
>  記錄傳送也支援[容錯移轉至記錄傳送次要 &#40;SQL Server&#41;](../../database-engine/log-shipping/fail-over-to-a-log-shipping-secondary-sql-server.md)選項，以及選擇性地支援[變更主要與次要記錄傳送伺服器間的角色 &#40;SQL Server&#41;](../../database-engine/log-shipping/change-roles-between-primary-and-secondary-log-shipping-servers-sql-server.md)。 不過，由於記錄傳送幾乎不再設定為高可用性解決方案 (較新的選項會更穩定)，因此，容錯移轉通常不會將停機時間降至最低，因為不會同步處理系統資料庫物件，而且讓用戶端可輕鬆地找到並連接到已升級的次要資料庫會是一項嚴峻的考驗。  
  
## <a name="see-also"></a>另請參閱  
 [使用安裝精靈升級為 SQL Server 2016 &#40;安裝程式&#41;](../../database-engine/install-windows/upgrade-sql-server-using-the-installation-wizard-setup.md)   
 [從命令提示字元安裝 SQL Server 2016](../install-windows/install-sql-server-from-the-command-prompt.md)   
 [設定記錄傳送 &#40;SQL Server&#41;](../../database-engine/log-shipping/configure-log-shipping-sql-server.md)   
 [監視記錄傳送 &#40;Transact-SQL&#41;](../../database-engine/log-shipping/monitor-log-shipping-transact-sql.md)   
 [記錄傳送資料表與預存程序](../../database-engine/log-shipping/log-shipping-tables-and-stored-procedures.md)  
  
