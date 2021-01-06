---
title: 可用性群組的監視及疑難排解指南
description: 本內容索引可協助您開始監視 Always On 可用性群組，並針對其中所發現的某些常見問題進行疑難排解。
ms.custom: seo-lt-2019
ms.date: 05/10/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: reference
ms.assetid: 8d6d9954-ff6b-4e58-882e-eff0174f0d07
author: rothja
ms.author: jroth
ms.openlocfilehash: 5594a39b6ab4f8c9c6bd2edaadbe64e99c983229
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97643691"
---
# <a name="monitor-and-troubleshoot-availability-groups"></a>可用性群組的監視及疑難排解
 本指南將協助您開始監視 Always On 可用性群組，並且對可用性群組中的某些常見的問題進行疑難排解。 本指南將提供在其他位置已發佈的有用資訊的原始內容和登陸頁面。 雖然本指南無法完整討論在可用性群組大範圍中發生的所有問題，但是可以為您指出根本原因分析和問題解決的正確方向。 
 
 因為可用性群組是一種整合式的技術，所以您遇到的許多問題可能是資料庫系統中其他問題的徵兆。 某些問題可能是可用性群組內的設定造成，例如某個可用性資料庫正處於暫止狀態。 其他則可能包含 SQL Server 其他方面的問題，例如 SQL Server 設定、資料庫檔案部署，以及與可用性不相關的系統性效能問題。 也有可能是因為 SQL Server 外部的其他問題，例如網路 I/O、TCP/IP、Active Directory 及 Windows Server 容錯移轉叢集 (WSFC) 問題。 通常，在可用性群組、複本或資料庫中顯露出來的問題，需要您對多種技術進行疑難排解才能識別根本原因。  
  
  
##  <a name="troubleshooting-scenarios"></a><a name="BKMK_SCENARIOS"></a> 疑難排解案例  
 下表包含可用性群組的常見疑難排解案例的連結。 這些案例依案例類型分類，例如設定、用戶端連接性、容錯移轉及效能。  
  
|狀況|案例類型|描述|  
|--------------|-------------------|-----------------|  
|[疑難排解 Always On 可用性群組組態 &#40;SQL Server&#41;](troubleshoot-always-on-availability-groups-configuration-sql-server.md)|組態|提供資訊以協助您針對設定可用性群組的伺服器執行個體時常遇到的問題進行疑難排解。 一般組態問題包含可用性群組未啟用、不正確地設定帳戶、資料庫鏡像端點不存在、端點無法存取 (SQL Server 錯誤 1418)、網路存取不存在，以及聯結資料庫命令失敗 (SQL Server 錯誤 35250)。|  
|[疑難排解失敗的加入檔案作業 &#40;Always On 可用性群組&#41;](troubleshoot-a-failed-add-file-operation-always-on-availability-groups.md)|組態|加入檔案作業會造成次要資料庫暫止並處於 NOT SYNCHRONIZING 狀態。|  
|[無法連線到多重子網路環境中的可用性群組接聽程式](https://support.microsoft.com/kb/2792139/en-us) \(英文\)|用戶端連接性|設定可用性群組接聽程式之後，您就無法從應用程式 Ping 到接聽程式或與它連線。|  
|[疑難排解失敗的自動容錯移轉](https://support.microsoft.com/kb/2833707) \(英文\)|容錯移轉|自動容錯移轉未順利完成。|  
|[偵錯：可用性群組超過 RTO](troubleshoot-availability-group-exceeded-rto.md)|效能|在自動容錯移轉或規劃的手動容錯移轉之後若未遺失資料，容錯移轉時間會超過您的 RTO。 或者，當您評估同步認可次要複本 (例如自動容錯移轉夥伴) 的容錯移轉時間時，發現它超過您的 RTO。|  
|[偵錯：可用性群組超過 RPO](troubleshoot-availability-group-exceeded-rpo.md)|效能|在您執行強制手動容錯移轉之後，遺失的資料超過您的 RPO。 或者，當您計算非同步認可次要複本的潛在資料遺失時，發現它超過您的 RPO。|  
|[疑難排解：對主要複本的變更未反映在次要複本上](troubleshoot-primary-changes-not-reflected-on-secondary.md)|效能|用戶端應用程式在主要複本上成功完成更新，但是查詢次要複本卻顯示未反映變更。|  
|[疑難排解：Always On 可用性群組的高 HADR_SYNC_COMMIT 等候類型](/archive/blogs/sql_server_team/troubleshooting-high-hadr_sync_commit-wait-type-with-always-on-availability-groups) \(英文\)|效能|如果 HADR_SYNC_COMMIT 超乎尋常地長，表示資料移動程序或次要複本記錄強化可能有效能問題。|  

##  <a name="useful-tools-for-troubleshooting"></a><a name="BKMK_TOOLS"></a> 對疑難排解有助益的工具  
 設定或執行可用性群組時，不同的工具可協助您診斷不同類型的問題。 下表提供與工具相關的有用資訊連結。  
  
|工具|描述|  
|----------|-----------------|  
|[使用 AlwaysOn 儀表板 &#40;SQL Server Management Studio&#41;](use-the-always-on-dashboard-sql-server-management-studio.md)|在方便使用的介面中，報告可用性群組健康情況的摘要檢視。|  
|[Always On 原則](always-on-policies.md)|由 Always On 儀表板使用。|  
|[SQL Server 錯誤記錄檔 &#40;Always On 可用性群組&#41;](sql-server-error-log-always-on-availability-groups.md)|可用性群組、複本和資料庫的記錄檔狀態轉換事件、其他 Always On 元件的狀態，以及 Always On 錯誤。|  
|[CLUSTER.LOG &#40;Always On 可用性群組&#41;](cluster-log-always-on-availability-groups.md)|記錄檔叢集事件，包括可用性群組資源的狀態轉換，以及來自 SQL Server 資源 DLL 的事件和錯誤。|  
|[Always On 健康情況診斷記錄](always-on-health-diagnostics-log.md)|記錄 SQL Server 健康情況診斷，由 [sp_server_diagnostics &#40;;Transact-SQL&#41;](~/relational-databases/system-stored-procedures/sp-server-diagnostics-transact-sql.md) 報告至 WSFC 叢集 (SQL Server 資源 DLL)。|  
|[動態管理檢視與系統目錄檢視 &#40;Always On 可用性群組&#41;](dynamic-management-views-and-system-catalog-views-always-on-availability-groups.md)|報告可用性群組的資訊，例如設定、健康情況狀態及效能計量。|  
|[Always On 擴充事件](always-on-extended-events.md)|提供可用性群組的詳細診斷，對於根本原因分析很有用。|  
|[Always On 等候類型](always-on-wait-types.md)|提供可用性群組專屬的等候統計資料，對於效能微調很有用。|  
|Always On 效能計數器|監視可用性群組活動，會反映在 系統監視器中，對於效能微調很有用。 如需詳細資訊，請參閱 [SQL Server、可用性複本](~/relational-databases/performance-monitor/sql-server-availability-replica.md)和 [SQL Server、資料庫複本](~/relational-databases/performance-monitor/sql-server-database-replica.md)。|  
|[Always On 信號緩衝區](always-on-ring-buffers.md)|記錄 SQL Server 系統中針對內部診斷的警示，可用來對與可用性群組相關的問題進行偵錯。|  
  
##  <a name="monitoring-availability-groups"></a><a name="BKMK_MONITOR"></a> 監視可用性群組  
 對可用性群組進行疑難排解的理想時機是在發生問題而必須進行容錯移轉 (無論自動或手動) 之前。 這可藉由監視可用性群組的效能度計量，以及當可用性複本是在您的服務等級協定 (SLA) 範圍外執行時傳送警示來達成。 例如，如果同步的次要複本有效能問題而導致估計的容錯移轉時間增加，您不需要等到自動容錯移轉發生，並發現容錯移轉時間超過復原時間目標。  
  
 因為可用性群組是高可用性和災害復原方案，所以最重要要監視的效能計量是估計的容錯移轉時間 (這會影響您的復原時間目標 (RTO))，以及在災害中的潛在資料遺失 (這會影響您的復原點目標 (RPO))。 您可以隨時從 SQL Server 公開的資料收集這些計量，讓您可以在系統發生實際的失敗事件之前，收到高可用性災害復原 (HADR) 功能問題的警示。 因此，請務必讓自己熟悉可用性群組的資料同步處理程序，並據此收集計量。  
  
 下表會將您導向至可協助您監視可用性群組解決方案健康情況的主題。  
  
|主題|描述|  
|-----------|-----------------|  
|[監視 Always On 可用性群組的效能](monitor-performance-for-always-on-availability-groups.md)|描述可用性群組的資料同步處理程序、流量控制閘道，以及監視可用性群組時的實用計量，同時也顯示如何收集 RTO 和 RPO 計量。|  
|[監視可用性群組 &#40;SQL Server&#41;](monitoring-of-availability-groups-sql-server.md)|提供監視可用性群組的工具資訊。|  
|[Always On 健康情況模型，第 1 部分：健康情況模型架構](/archive/blogs/sqlalwayson/the-alwayson-health-model-part-1-health-model-architecture) \(英文\)|提供 Always On 健康情況模型的概觀。|  
|[Always On 健康情況模型，第 2 部分：擴充健康情況模型](/archive/blogs/sqlalwayson/the-alwayson-health-model-part-2-extending-the-health-model) \(英文\)|示範如何自訂 Always On 健康情況模型及自訂 Always On 儀表板來顯示額外的資訊。|  
|[使用 PowerShell 監視 Always On 健康情況，第 1 部分：基本 Cmdlet 概觀](/archive/blogs/sqlalwayson/monitoring-alwayson-health-with-powershell-part-1-basic-cmdlet-overview) \(英文\)|針對可用來監視可用性群組健康情況的 Always On PowerShell Cmdlet，提供其基本概觀。|  
|[使用 PowerShell 監視 Always On 健康情況，第 2 部分：進階 Cmdlet 使用](/archive/blogs/sqlalwayson/monitoring-alwayson-health-with-powershell-part-2-advanced-cmdlet-usage) \(英文\)|提供 Always On PowerShell Cmdlet 的進階使用方式資訊，以監視可用性群組健康情況。|  
|[使用 PowerShell 監視 Always On 健康情況，第 3 部分：一個簡單的監視應用程式](/archive/blogs/sqlalwayson/monitoring-alwayson-health-with-powershell-part-3-a-simple-monitoring-application) \(英文\)|示範如何使用應用程式自動監視可用性群組。|  
|[使用 PowerShell 監視 Always On 健康情況，第 4 部分：與 SQL Server Agent 整合](/archive/blogs/sqlalwayson/monitoring-alwayson-health-with-powershell-part-4-integration-with-sql-server-agent) \(英文\)|提供如何與 SQL Server Agent 整合可用性群組監視，以及如何設定發生問題時通知適當對象的資訊。|  

## <a name="next-steps"></a>後續步驟  
 [SQL Server Always On 團隊部落格](/archive/blogs/sqlalwayson/)   
 [CSS SQL Server 工程師部落格](/archive/blogs/psssql/)  
  
