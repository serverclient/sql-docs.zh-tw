---
title: 設定記錄傳送 (SQL Server) | Microsoft Docs
description: 了解如何使用 SQL Server Management Studio 或 Transact-SQL 在 SQL Server 中設定記錄傳送。
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.reviewer: ''
ms.technology: log-shipping
ms.topic: conceptual
helpviewer_keywords:
- log shipping [SQL Server], enabling
- log shipping [SQL Server], configuring
ms.assetid: c42aa04a-4945-4417-b4c7-50589d727e9c
author: cawrites
ms.author: chadam
ms.openlocfilehash: 3a02f42c2fdec705894c0c583c634b756602e1b1
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97643909"
---
# <a name="configure-log-shipping-sql-server"></a>設定記錄傳送 (SQL Server)
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
  本主題描述如何使用 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 或 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 設定 [!INCLUDE[tsql](../../includes/tsql-md.md)]中的記錄傳送。  
  
> [!NOTE]  
>  [!INCLUDE[ssEnterpriseEd10](../../includes/ssenterpriseed10-md.md)] (含) 以後版本支援備份壓縮。 在建立記錄傳送設定時，您可以控制記錄備份的備份壓縮行為。 如需詳細資訊，請參閱[備份壓縮 &#40;SQL Server&#41;](../../relational-databases/backup-restore/backup-compression-sql-server.md)。  
  
 **本主題內容**  
  
-   **開始之前：**  
  
     [先決條件](#Prerequisites)  
  
     [安全性](#Security)  
  
-   **若要設定記錄傳送，請使用：**  
  
     [SQL Server Management Studio](#SSMSProcedure)  
  
     [Transact-SQL](#TsqlProcedure)  
  
-   [相關工作](#RelatedTasks)  
  
##  <a name="before-you-begin"></a><a name="BeforeYouBegin"></a> 開始之前  
  
###  <a name="prerequisites"></a><a name="Prerequisites"></a> 必要條件  
  
-   主要資料庫必須使用完整或大量記錄復原模式；若將資料庫切換為簡單復原模式，會使記錄傳送停止運作。  
  
-   在設定記錄傳送之前，您必須建立一個共用，讓次要伺服器能夠使用交易記錄備份。 這是將要產生交易記錄檔備份之目錄的共用。 例如，您若是將交易記錄備份到 c:\data\tlogs\\目錄，就可以建立該目錄的 \\\\*primaryserver*\tlogs 共用。  
  
###  <a name="security"></a><a name="Security"></a> Security  
  
####  <a name="permissions"></a><a name="Permissions"></a> 權限  
 記錄傳送預存程序需要 **sysadmin** 固定伺服器角色中的成員資格。  
  
##  <a name="using-sql-server-management-studio"></a><a name="SSMSProcedure"></a> 使用 SQL Server Management Studio  
  
#### <a name="to-configure-log-shipping"></a>設定記錄傳送  
  
1.  以滑鼠右鍵按一下記錄傳送組態中要做為主要資料庫的資料庫，然後按一下 **[屬性]** 。  
  
2.  在 **[選取頁面]** 下，按一下 **[交易記錄傳送]** 。  
  
3.  選取 **[將此啟用為記錄傳送組態的主要資料庫 ]** 核取方塊。  
  
4.  在 **[交易記錄檔備份]** 下，按一下 **[備份設定]** 。  
  
5.  在 **[到備份資料夾的網路路徑]** 方塊中，輸入您針對交易記錄檔備份資料夾所建立之共用的網路路徑。  
  
6.  **若備份資料夾位於主要伺服器上，請在 [備份資料夾] 方塊中鍵入本機路徑。** (如果備份資料夾不在主要伺服器，您可以將這個方塊留空)。  
  
    > [!IMPORTANT]  
    >  如果主要伺服器上的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 服務帳戶是在本機系統帳戶下執行，您必須在主要伺服器上建立備份資料夾，並指定該資料夾的本機路徑。  
  
7.  設定 **[指定刪除檔案的時限]** 及 **[如果未在此時間內進行備份，則發出警示]** 參數。  
  
8.  請注意 **[備份作業]** 之下 **[排程]** 方塊所列的備份排程。 如果您想要自訂安裝的排程，請按一下 **[排程]** ，並視需要調整 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent 排程。  
  
9. [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 支援 [備份壓縮](../../relational-databases/backup-restore/backup-compression-sql-server.md)。 在建立記錄傳送設定時，您可以透過選擇下列其中一個選項，來控制記錄備份的備份壓縮行為：[使用預設伺服器設定]、[壓縮備份]，或 [不要壓縮備份]。 如需詳細資訊，請參閱 [Log Shipping Transaction Log Backup Settings](../../relational-databases/databases/log-shipping-transaction-log-backup-settings.md)。  
  
10. 按一下 [確定]。  
  
11. 在 **[次要伺服器執行個體與資料庫]** 下，按一下 **[新增]** 。  
  
12. 按一下 **[連接]** ，連接到您要做為次要伺服器的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體。  
  
13. 在 **[次要資料庫]** 方塊中，從清單中選擇資料庫，或輸入您要建立的資料庫名稱。  
  
14. 在 **[初始化次要資料庫]** 索引標籤上，選擇要用於初始化次要資料庫的選項。  
  
    > [!NOTE]  
    >  如果您選擇讓 [!INCLUDE[ssManStudio](../../includes/ssmanstudio-md.md)] 從資料庫備份初始化次要資料庫，次要資料庫的資料和記錄檔就會與 **master** 資料庫的資料和記錄檔放置於相同的位置。 這個位置可能會與主要資料庫之資料和記錄檔的位置不同。  
  
15. 在 **[複製檔案]** 索引標籤的 **[複製之檔案的目的資料夾]** 方塊中，輸入交易記錄檔備份所應複製到的資料夾路徑。 這個資料夾通常位於次要伺服器上。  
  
16. 請注意 **[複製作業]** 之下 **[排程]** 方塊中所列的複製排程。 如果您要自訂安裝的排程，請按一下 **[排程]** ，然後視需要調整 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent 排程。 這個排程應接近備份排程。  
  
17. 在 **[還原]** 索引標籤上的 **[還原備份時的資料庫狀態]** 下，選擇 **[不復原模式]** 或 **[待命模式]** 選項。  
    > [!IMPORTANT]  
    > **待命模式** 只是主要和次要伺服器版本相同時的選項。 次要伺服器的主要版本高於主要伺服器時，只允許 [No recovery mode] \(無復原模式\)
  
18. 如果您選擇 **[待命模式]** 選項，請選擇是否要在還原作業進行時，中斷使用者與次要資料庫的連接。  
  
19. 如果您要延遲次要伺服器上的還原處理序，請在 **[延遲還原備份至少]** 下選擇延遲時間。  
  
20. 在 **[如果未在此時間內進行還原，則發出警示]** 下選擇警示臨界值。  
  
21. 請注意 **[還原作業]** 下之 **[排程]** 方塊中所列的還原排程。 如果您要自訂安裝的排程，請按一下 **[排程]** ，然後視需要調整 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent 排程。 這個排程應接近備份排程。  
  
22. 按一下 [確定]。  
  
23. 在 **[監視伺服器執行個體]** 下，選取 **[使用監視伺服器執行個體]** 核取方塊，再按一下 **[設定]** 。  
  
    > [!IMPORTANT]  
    >  若要監視這個記錄傳送組態，您必須立即加入監視伺服器。 若要在日後加入監視伺服器，您就必須移除這個記錄傳送組態，然後將它取代成包含監視伺服器的新組態。  
  
24. 按一下 **[連接]** ，然後連接到要做為監視伺服器的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體。  
  
25. 在 **[監視器連接]** 下，選擇備份、複製及還原作業用來連接到監視伺服器的連接方法。  
  
26. 在 **[記錄保留]** 下，選擇您要保留記錄傳送記錄的時間長度。  
  
27. 按一下 [確定]。  
  
28. 在 **[資料庫屬性]** 對話方塊上，按一下 **[確定]** 以開始設定處理序。  
  
##  <a name="using-transact-sql"></a><a name="TsqlProcedure"></a> 使用 Transact-SQL  
  
#### <a name="to-configure-log-shipping"></a>設定記錄傳送  
  
1.  在次要伺服器上還原主要資料庫的完整備份，以初始化次要資料庫。  
  
2.  在主要伺服器上執行 [sp_add_log_shipping_primary_database](../../relational-databases/system-stored-procedures/sp-add-log-shipping-primary-database-transact-sql.md) ，以加入主要資料庫。 預存程序會傳回備份作業識別碼及主要識別碼。  
  
3.  在主要伺服器上執行 [sp_add_jobschedule](../../relational-databases/system-stored-procedures/sp-add-jobschedule-transact-sql.md) ，以加入排程來供備份作業使用。  
  
4.  在監視伺服器上執行 [sp_add_log_shipping_alert_job](../../relational-databases/system-stored-procedures/sp-add-log-shipping-alert-job-transact-sql.md) ，以加入警示作業。  
  
5.  在主要伺服器上啟用備份作業。  
  
6.  在次要伺服器上，執行 [sp_add_log_shipping_secondary_primary](../../relational-databases/system-stored-procedures/sp-add-log-shipping-secondary-primary-transact-sql.md) ，提供主要伺服器與資料庫的詳細資料。 這個預存程序會傳回次要識別碼及複製與還原作業識別碼。  
  
7.  在次要伺服器上執行 [sp_add_jobschedule](../../relational-databases/system-stored-procedures/sp-add-jobschedule-transact-sql.md) ，以設定複製與還原作業的排程。  
  
8.  在次要伺服器上執行 [sp_add_log_shipping_secondary_database](../../relational-databases/system-stored-procedures/sp-add-log-shipping-secondary-database-transact-sql.md) ，以加入次要資料庫。  
  
9. 在主要伺服器上執行 [sp_add_log_shipping_primary_secondary](../../relational-databases/system-stored-procedures/sp-add-log-shipping-primary-secondary-transact-sql.md) ，將有關新次要資料庫的必要資訊加入主要伺服器。  
  
10. 在次要伺服器上，啟用複製與還原作業。 如需詳細資訊，請參閱 [Disable or Enable a Job](../../ssms/agent/disable-or-enable-a-job.md)。  
  
##  <a name="related-tasks"></a><a name="RelatedTasks"></a> 相關工作  
  
-   [將記錄傳送升級至 SQL Server 2016 &#40;Transact-SQL&#41;](../../database-engine/log-shipping/upgrading-log-shipping-to-sql-server-2016-transact-sql.md)  
  
-   [將次要資料庫加入至記錄傳送組態 &#40;SQL Server&#41;](../../database-engine/log-shipping/add-a-secondary-database-to-a-log-shipping-configuration-sql-server.md)  
  
-   [從記錄傳送組態中移除次要資料庫 &#40;SQL Server&#41;](../../database-engine/log-shipping/remove-a-secondary-database-from-a-log-shipping-configuration-sql-server.md)  
  
-   [移除記錄傳送 &#40;SQL Server&#41;](../../database-engine/log-shipping/remove-log-shipping-sql-server.md)  
  
-   [檢視記錄傳送報表 &#40;SQL Server Management Studio&#41;](../../database-engine/log-shipping/view-the-log-shipping-report-sql-server-management-studio.md)  
  
-   [監視記錄傳送 &#40;Transact-SQL&#41;](../../database-engine/log-shipping/monitor-log-shipping-transact-sql.md)  
  
-   [容錯移轉至記錄傳送次要 &#40;SQL Server&#41;](../../database-engine/log-shipping/fail-over-to-a-log-shipping-secondary-sql-server.md)  
  
## <a name="see-also"></a>另請參閱  
 [關於記錄傳送 &#40;SQL Server&#41;](../../database-engine/log-shipping/about-log-shipping-sql-server.md)   
 [記錄傳送資料表與預存程序](../../database-engine/log-shipping/log-shipping-tables-and-stored-procedures.md)  
  
  
