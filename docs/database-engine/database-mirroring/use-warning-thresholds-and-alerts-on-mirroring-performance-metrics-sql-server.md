---
title: 設定資料庫鏡像效能計量的警示
description: '針對由資料庫鏡像所使用的效能計量設定警告閾值與警示的指導方針。 '
ms.custom: seo-lt-2019
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: high-availability
ms.reviewer: ''
ms.technology: database-mirroring
ms.topic: conceptual
helpviewer_keywords:
- monitoring database mirroring [SQL Server]
- thresholds [SQL Server]
- database mirroring [SQL Server], managing in SQL Server Management Studio
- alerts [SQL Server], database mirroring
- database mirroring [SQL Server], monitoring
- warnings [database mirroring]
ms.assetid: 8cdd1515-0bd7-4f8c-a7fc-a33b575e20f6
author: MikeRayMSFT
ms.author: mikeray
ms.openlocfilehash: c323c5915752e68505e7e1e512843ae03c8197e4
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97643484"
---
# <a name="use-warning-thresholds-and-alerts-on-mirroring-performance-metrics-sql-server"></a>使用鏡像效能標準的警告臨界值與警示 (SQL Server)
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
  此主題包含可針對資料庫鏡像設定和管理警告臨界值之 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 事件的相關資訊。 您可以使用「資料庫鏡像監視器」或 **sp_dbmmonitorchangealert**、 **sp_dbmmonitorhelpalert** 及 **sp_dbmmonitordropalert** 預存程序。 此主題也包含設定資料庫鏡像事件之警示的相關資訊。  
  
 建立鏡像資料庫的監視作業後，系統管理員就可以設定許多項關鍵效能標準的警告臨界值。 此外，管理員還可以設定這些和其他資料庫鏡像事件的警示。  
  
  
##  <a name="performance-metrics-and-warning-thresholds"></a><a name="PerfMetricsAndWarningThresholds"></a> 效能標準和警告臨界值  
 下表將列出可設定警告的效能標準、描述對應的警告臨界值，並列出對應的 [資料庫鏡像監視器] 標籤。  
  
|效能標準|警告臨界值|資料庫鏡像監視器標籤|  
|------------------------|-----------------------|--------------------------------------|  
|未傳送的記錄|指定會在主體伺服器執行個體上產生警告之未傳送記錄的 KB 數。 這個警告有助於從 KB 方面測量資料遺失的可能性，而且尤其與高效能模式相關。 但是，當鏡像因為夥伴中斷連接而暫停或暫止時，這個警告也會與高安全性模式有關。|**如果未傳送的記錄超過臨界值，即發出警告**|  
|未還原的記錄|指定會在鏡像伺服器執行個體上產生警告之未還原記錄的 KB 數。 這個警告有助於測量容錯移轉時間。 *容錯移轉時間* 主要包含先前的鏡像伺服器向前復原其重做佇列中剩餘之所有記錄所需的時間，再加上一段很短的額外時間。<br /><br /> 注意：若為自動容錯移轉，則系統發現錯誤的時間與容錯移轉時間無關。<br /><br /> 如需詳細資訊，請參閱 [預估角色切換期間的服務中斷時間 &#40;資料庫鏡像&#41;](../../database-engine/database-mirroring/estimate-the-interruption-of-service-during-role-switching-database-mirroring.md)的程序交換。|**如果未還原的記錄超過臨界值，即發出警告**|  
|最舊尚未傳送的交易|指定在主體伺服器執行個體上產生警告之前，傳送佇列中可以累積的交易分鐘數。 這個警告有助於從時間方面測量資料遺失的可能性，而且尤其與高效能模式相關。 但是，當鏡像因為夥伴中斷連接而暫停或暫止時，這個警告也會與高安全性模式有關。|**如果最舊未傳送交易的時間超過臨界值，即發出警告**|  
|鏡像認可負擔|指定在主體伺服器上產生警告之前所容許之每項交易的平均延遲毫秒數。 這項延遲是當主體伺服器執行個體等待鏡像伺服器執行個體將交易記錄寫入重做佇列中時所產生的負擔量。 只有在高安全性模式中才會顯出這個值的重要性。|**如果鏡像認可負擔超過臨界值，即發出警告**|  
  
 系統管理員可以針對其中一項效能標準，在鏡像資料庫上指定臨界值。 如需詳細資訊，請參閱本主題後面的 [設定和管理警告臨界值](#SetUpManageWarningThresholds)。  
  
##  <a name="setting-up-and-managing-warning-thresholds"></a><a name="SetUpManageWarningThresholds"></a> 設定和管理警告臨界值  
 系統管理員可以設定關鍵鏡像效能標準的一或多個警告臨界值。 我們建議您在兩個夥伴上設定指定警告的臨界值，以便確保資料庫在容錯移轉時，警告仍會保持不變。 每個夥伴上的適當臨界值會根據該夥伴系統的效能功能而定。  
  
 您可以使用下列任何一項方式來設定並管理警告臨界值：  
  
-   資料庫鏡像監視器  
  
     在 [資料庫鏡像監視器] 中，管理員可以選取 **[警告]** 索引標籤頁面，藉以同時檢視位於主體和鏡像伺服器執行個體之選取資料庫的目前警告組態。 在該頁面中，管理員可以開啟 **[設定警告臨界值]** 對話方塊，以便啟用和設定一或多個警告臨界值。  
  
     如需 [資料庫鏡像監視器] 介面的簡介，請參閱＜ [Database Mirroring Monitor Overview](../../database-engine/database-mirroring/database-mirroring-monitor-overview.md)＞。 如需啟動「資料庫鏡像監視器」的相關資訊，請參閱 [啟動資料庫鏡像監視器 &#40;SQL Server Management Studio&#41;](../../database-engine/database-mirroring/start-database-mirroring-monitor-sql-server-management-studio.md)。  
  
-   系統預存程序  
  
     下列系統預存程序集可讓管理員一次設定並管理一個夥伴之鏡像資料庫的警告臨界值。  
  
    |程序|描述|  
    |---------------|-----------------|  
    |[sp_dbmmonitorchangealert &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-dbmmonitorchangealert-transact-sql.md)|加入或變更指定之鏡像效能標準的警告臨界值。|  
    |[sp_dbmmonitorhelpalert &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-dbmmonitorhelpalert-transact-sql.md)|傳回有關其中一個或所有關鍵資料庫鏡像監視器效能標準之警告臨界值的資訊。|  
    |[sp_dbmmonitordropalert &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-dbmmonitordropalert-transact-sql.md)|卸除指定效能標準的警告。|  
  
## <a name="performance-threshold-events-sent-to-the-windows-event-log"></a>傳送至 Windows 事件記錄檔的效能臨界值事件  
 如果已經定義效能標準的警告臨界值，當狀態資料表更新後，就會根據臨界值評估最新的值。 如果已經達到臨界值，更新程序 **sp_dbmmonitorupdate** 就會產生此標準的參考用事件 (「效能臨界值事件」  ) 並將事件寫入 [!INCLUDE[msCoName](../../includes/msconame-md.md)] Windows 事件記錄檔。 下表將列出效能臨界值事件的事件識別碼。  
  
|效能標準|事件識別碼|  
|------------------------|--------------|  
|未傳送的記錄|32042|  
|未還原的記錄|32043|  
|最舊尚未傳送的交易|32040|  
|鏡像認可負擔|32044|  
  
> [!NOTE]  
>  管理員可以定義其中一或多個事件的警示。 如需詳細資訊，請參閱本主題後面的 [針對鏡像資料庫使用警示](#UseAlerts)  
>   
>  。  
  
##  <a name="using-alerts-for-a-mirrored-database"></a><a name="UseAlerts"></a> 針對鏡像資料庫使用警示  
 監視鏡像資料庫最重要的部分就是設定重大資料庫鏡像事件的警示。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會產生下列資料庫鏡像事件類型：  
  
-   效能臨界值事件  
  
     如需詳細資訊，請參閱本主題前面的「傳送至 Windows 事件記錄檔的效能臨界值事件」。  
  
-   狀態變更事件  
  
     這些是資料庫鏡像工作階段的內部狀態發生變更時產生的 Windows Management Instrumentation (WMI) 事件。  
  
    > [!NOTE]  
    >  如需詳細資訊，請參閱 [伺服器事件的 WMI 提供者概念](../../relational-databases/wmi-provider-server-events/wmi-provider-for-server-events-concepts.md)。  
  
 系統管理員可以使用 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent 或其他應用程式 (例如 [!INCLUDE[msCoName](../../includes/msconame-md.md)] Operations Manager)，設定這些事件的警示。  
  
 定義資料庫鏡像事件的警示後，我們建議您在兩個夥伴伺服器執行個體上都定義警告臨界值和警示。 雖然個別事件會在主體伺服器或鏡像伺服器上產生，不過每一個夥伴都可以隨時執行任何一個角色。 若要確保在容錯移轉之後警示會繼續運作，您必須在兩個夥伴上都定義該警示。  
  
> [!IMPORTANT]  
>  我們強烈建議您針對所有鏡像工作階段，將資料庫設定為傳送任何狀態變更事件的警示。 除非狀態變更預期為手動組態變更的結果，否則就表示發生了可能會危害資料的事件。 若要有效保護資料，請識別並修正非預期狀態變更的原因。  
  
##  <a name="related-tasks"></a><a name="RelatedTasks"></a> 相關工作  
 **若要使用 SQL Server Management Studio 建立警示**  
  
-   [使用錯誤號碼建立警示](../../ssms/agent/create-an-alert-using-an-error-number.md)  
  
-   [建立 WMI 事件警示](../../ssms/agent/create-a-wmi-event-alert.md)  
  
 **若要監控資料庫鏡像**  
  
-   [啟動資料庫鏡像監視器 &#40;SQL Server Management Studio&#41;](../../database-engine/database-mirroring/start-database-mirroring-monitor-sql-server-management-studio.md)  
  
-   [sp_dbmmonitoraddmonitoring &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-dbmmonitoraddmonitoring-transact-sql.md)  
  
-   [sp_dbmmonitorchangealert &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-dbmmonitorchangealert-transact-sql.md)  
  
-   [sp_dbmmonitorchangemonitoring &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-dbmmonitorchangemonitoring-transact-sql.md)  
  
-   [sp_dbmmonitordropalert &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-dbmmonitordropalert-transact-sql.md)  
  
-   [sp_dbmmonitordropmonitoring &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-dbmmonitordropmonitoring-transact-sql.md)  
  
-   [sp_dbmmonitorhelpalert &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-dbmmonitorhelpalert-transact-sql.md)  
  
-   [sp_dbmmonitorhelpmonitoring &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-dbmmonitorhelpmonitoring-transact-sql.md)  
  
-   [sp_dbmmonitorresults &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-dbmmonitorresults-transact-sql.md)  
  
-   [sp_dbmmonitorupdate &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-dbmmonitorupdate-transact-sql.md)  
  
## <a name="see-also"></a>另請參閱  
 [資料庫鏡像 &#40;SQL Server&#41;](../../database-engine/database-mirroring/database-mirroring-sql-server.md)   
 [監視資料庫鏡像 &#40;SQL Server&#41;](../../database-engine/database-mirroring/monitoring-database-mirroring-sql-server.md)  
  
  
