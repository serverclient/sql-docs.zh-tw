---
title: 新增記錄傳送次要
description: 描述如何在 SQL Server 中使用 SQL Server Management Studio 或 Transact-SQL 將次要資料庫加入至現有的記錄傳送設定。
ms.custom: seo-lt-2019
ms.date: 03/14/2017
ms.prod: sql
ms.reviewer: ''
ms.technology: log-shipping
ms.topic: conceptual
helpviewer_keywords:
- adding secondary databases
- secondary databases [SQL Server], in log shipping
- secondary data files [SQL Server], adding
- log shipping [SQL Server], secondary databases
ms.assetid: b02eba13-f8e6-4684-b7e4-75ea038ea473
author: cawrites
ms.author: chadam
ms.openlocfilehash: 3864af84ee12056b29c5e3b4c27a2693b1424120
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97642602"
---
# <a name="add-a-secondary-database-to-a-log-shipping-configuration-sql-server"></a>將次要資料庫加入至記錄傳送組態 (SQL Server)
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
  本主題描述如何使用 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 或 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] ，將次要資料庫加入至 [!INCLUDE[tsql](../../includes/tsql-md.md)]中現有的記錄傳送組態。  
  
  
##  <a name="before-you-begin"></a><a name="BeforeYouBegin"></a> 開始之前  
  
###  <a name="security"></a><a name="Security"></a> Security  
  
####  <a name="permissions"></a><a name="Permissions"></a> 權限  
 記錄傳送預存程序需要 **sysadmin** 固定伺服器角色中的成員資格。  
  
##  <a name="using-sql-server-management-studio"></a><a name="SSMSProcedure"></a> 使用 SQL Server Management Studio  
  
#### <a name="to-add-a-log-shipping-secondary-database"></a>若要加入記錄傳送次要資料庫  
  
1.  以滑鼠右鍵按一下記錄傳送組態中要作為主要資料庫的資料庫，然後按一下 [屬性]。  
  
2.  在 **[選取頁面]** 下，按一下 **[交易記錄傳送]** 。  
  
3.  在 **[次要伺服器執行個體與資料庫]** 下，按一下 **[新增]** 。  
  
4.  按一下 **[連接]** ，連接到您要做為次要伺服器的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體。  
  
5.  在 **[次要資料庫]** 方塊中，從清單中選擇資料庫，或輸入您要建立的資料庫名稱。  
  
6.  在 **[初始化次要資料庫]** 索引標籤上，選擇要用於初始化次要資料庫的選項。  
  
7.  在 **[複製檔案]** 索引標籤上的 **[複製之檔案的目的資料夾]** 方塊中，輸入交易記錄檔備份所應複製到的資料夾路徑。 這個資料夾通常位於次要伺服器上。  
  
8.  請注意 **[複製作業]** 之下 **[排程]** 方塊中所列的複製排程。 如果您要自訂安裝的排程，請按一下 **[排程]** ，然後視需要調整 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent 排程。 這個排程應接近備份排程。  
  
9. 在 **[還原]** 索引標籤上的 **[還原備份時的資料庫狀態]** 下，選擇 **[不復原模式]** 或 **[待命模式]** 選項。  
  
10. 如果您選擇 **[待命模式]** 選項，請選擇是否要在還原作業進行時，中斷使用者與次要資料庫的連接。  
  
11. 如果您要延遲次要伺服器上的還原處理序，請在 **[延遲還原備份至少]** 下選擇延遲時間。  
  
12. 在 **[如果未在此時間內進行還原，則發出警示]** 下選擇警示臨界值。  
  
13. 請注意 **[還原作業]** 下之 **[排程]** 方塊中所列的還原排程。 如果您要自訂安裝的排程，請按一下 **[排程]** ，然後視需要調整 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent 排程。 這個排程應接近備份排程。  
  
14. 按一下 [確定]。  
  
15. 按一下 [資料庫屬性] 對話方塊上的 **[確定]** ，開始執行組態處理序。  
  
##  <a name="using-transact-sql"></a><a name="TsqlProcedure"></a> 使用 Transact-SQL  
  
#### <a name="to-add-a-log-shipping-secondary-database"></a>若要加入記錄傳送次要資料庫  
  
1.  在次要伺服器上，執行 [sp_add_log_shipping_secondary_primary](../../relational-databases/system-stored-procedures/sp-add-log-shipping-secondary-primary-transact-sql.md) ，提供主要伺服器與資料庫的詳細資料。 這個預存程序會傳回次要識別碼及複製與還原作業識別碼。  
  
2.  在次要伺服器上執行 [sp_add_jobschedule](../../relational-databases/system-stored-procedures/sp-add-jobschedule-transact-sql.md) ，以設定複製與還原作業的排程。  
  
3.  在次要伺服器上執行 [sp_add_log_shipping_secondary_database](../../relational-databases/system-stored-procedures/sp-add-log-shipping-secondary-database-transact-sql.md) ，以加入次要資料庫。  
  
4.  在主要伺服器上執行 [sp_add_log_shipping_primary_secondary](../../relational-databases/system-stored-procedures/sp-add-log-shipping-primary-secondary-transact-sql.md) ，將有關新次要資料庫的必要資訊加入主要伺服器。  
  
5.  在次要伺服器上，啟用複製與還原作業。 如需詳細資訊，請參閱 [Disable or Enable a Job](../../ssms/agent/disable-or-enable-a-job.md)。  
  
##  <a name="related-tasks"></a><a name="RelatedTasks"></a> 相關工作  
  
-   [將記錄傳送升級至 SQL Server 2016 &#40;Transact-SQL&#41;](../../database-engine/log-shipping/upgrading-log-shipping-to-sql-server-2016-transact-sql.md)  
  
-   [設定記錄傳送 &#40;SQL Server&#41;](../../database-engine/log-shipping/configure-log-shipping-sql-server.md)  
  
-   [從記錄傳送組態中移除次要資料庫 &#40;SQL Server&#41;](../../database-engine/log-shipping/remove-a-secondary-database-from-a-log-shipping-configuration-sql-server.md)  
  
-   [移除記錄傳送 &#40;SQL Server&#41;](../../database-engine/log-shipping/remove-log-shipping-sql-server.md)  
  
-   [檢視記錄傳送報表 &#40;SQL Server Management Studio&#41;](../../database-engine/log-shipping/view-the-log-shipping-report-sql-server-management-studio.md)  
  
-   [監視記錄傳送 &#40;Transact-SQL&#41;](../../database-engine/log-shipping/monitor-log-shipping-transact-sql.md)  
  
-   [容錯移轉至記錄傳送次要 &#40;SQL Server&#41;](../../database-engine/log-shipping/fail-over-to-a-log-shipping-secondary-sql-server.md)  
  
## <a name="see-also"></a>另請參閱  
 [關於記錄傳送 &#40;SQL Server&#41;](../../database-engine/log-shipping/about-log-shipping-sql-server.md)   
 [記錄傳送資料表與預存程序](../../database-engine/log-shipping/log-shipping-tables-and-stored-procedures.md)  
  
  
