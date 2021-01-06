---
title: 監視可用性群組的工具
description: '用來監視 Always On 可用性群組效能和健全狀況的各種工具參考。 '
ms.custom: seodec18
ms.date: 06/08/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: conceptual
helpviewer_keywords:
- Availability Groups [SQL Server], monitoring
- Availability Groups [SQL Server], troubleshooting
ms.assetid: 1d5e3291-0d0a-45a1-88e5-1fc242d17210
author: cawrites
ms.author: chadam
ms.openlocfilehash: 734c89393b77fa74db57ce2896af64bbac7c1a63
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97643721"
---
# <a name="tools-to-monitor-always-on-availability-groups"></a>「監視 Always On 可用性群組的工具」
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]
  若要監視 AlwaysOn 可用性群組的屬性和狀態，您可以使用以下工具。  
  
|工具|簡短描述|連結|  
|----------|-----------------------|-----------|  
|適用於 SQL Server 的 System Center 監視封包|適用於 SQL Server 的監視封包 (SQLMP) 是建議 IT 管理員用來監視可用性群組、可用性複本和可用性資料庫的解決方案。 特別與 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] 相關的監視功能包括以下項目：<br /><br /> 數百部電腦的可用性群組、可用性複本和可用性資料庫的自動探索能力。 如此可讓您輕鬆地持續追蹤 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] 存貨。<br /><br /> 功能完整的 System Center Operations Manager (SCOM) 警示和票證功能。 這些功能會提供詳細知識，讓您更快速地解決問題。<br /><br /> 使用原則式管理 (PBM) 之 AlwaysOn 健全狀況監視的自訂延伸模組。<br /><br /> 從可用性資料庫到可用性複本的健全狀況積存。<br /><br /> 從 System Center Operations Manager 主控台管理 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] 的自訂工作。|若要下載監視封包 (SQLServerMP.msi) 和＜適用於 System Center Operations Manager 的 SQL Server 管理封包指南＞  (SQLServerMPGuide.doc)，請參閱：<br /><br /> [適用於 SQL Server 的 System Center 監視封包](https://www.microsoft.com/download/details.aspx?displaylang=en&id=10631)|  
|[!INCLUDE[tsql](../../../includes/tsql-md.md)]|[!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] 目錄和動態管理檢視提供有關可用性群組及其複本、資料庫、接聽程式和 WSFC 叢集環境的許多資訊。|[監視可用性群組 &#40;Transact-SQL&#41;](../../../database-engine/availability-groups/windows/monitor-availability-groups-transact-sql.md)|  
|[!INCLUDE[ssManStudioFull](../../../includes/ssmanstudiofull-md.md)]|**[物件總管詳細資料]** 窗格會顯示您所連接之 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體上裝載的可用性群組基本資訊。<br /><br /> **\*\* 提示 \*\*** ：使用此窗格選取多個可用性群組、複本或資料庫，並為所選物件執行例行的系統管理工作，例如，從可用性群組移除多個可用性複本或資料庫。|[使用物件總管詳細資料監視可用性群組 &#40;SQL Server Management Studio&#41;](../../../database-engine/availability-groups/windows/use-object-explorer-details-to-monitor-availability-groups.md)|  
|[!INCLUDE[ssManStudioFull](../../../includes/ssmanstudiofull-md.md)]|**[屬性]** 對話方塊可讓您檢視可用性群組、複本或接聽程式的屬性，並在某些情況下變更其值。|-   [檢視可用性群組屬性 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/view-availability-group-properties-sql-server.md)<br />-   [檢視可用性複本屬性 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/view-availability-replica-properties-sql-server.md)<br />-   [檢視可用性群組接聽程式屬性 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/view-availability-group-listener-properties-sql-server.md)|  
|系統監視器|**SQLServer:Availability Replica** 效能物件含有效能計數器，可報告可用性複本的相關資訊。|[SQL Server、可用性複本](../../../relational-databases/performance-monitor/sql-server-availability-replica.md)|  
|系統監視器|**SQLServer:Database Replica** 效能物件含有效能計數器，可報告給定次要複本上次要資料庫的相關資訊。<br /><br /> SQL Server 中的 **SQLServer:Databases** 物件含有效能計數器，可監視交易記錄活動以及其他項目。 下列計數器與監視可用性資料庫上的交易記錄活動特別相關： **Log Flush Write Time (ms)** 、 **Log Flushes/sec**、 **Log Pool Cache Misses/sec**、 **Log Pool Disk Reads/sec** 以及 **Log Pool Requests/sec**。|[SQL Server、資料庫複本](../../../relational-databases/performance-monitor/sql-server-database-replica.md) 以及 [SQL Server、Databases 物件](../../../relational-databases/performance-monitor/sql-server-databases-object.md)|  
  
##  <a name="related-content"></a><a name="RelatedContent"></a> 相關內容  
  
-   **部落格：**  
  
     [AlwaysOn 健全狀況模型第 1 部 -- 健全狀況模型架構](/archive/blogs/sqlalwayson/the-alwayson-health-model-part-1-health-model-architecture)  
  
     [AlwaysOn 健全狀況模型第 2 部 -- 擴充健全狀況模型](/archive/blogs/sqlalwayson/the-alwayson-health-model-part-2-extending-the-health-model)  
  
     [使用 PowerShell 監視 AlwaysOn 健全狀況 - 第 1 部：基本 Cmdlet 概觀](/archive/blogs/sqlalwayson/monitoring-alwayson-health-with-powershell-part-1-basic-cmdlet-overview)  
  
     [使用 PowerShell 監視 AlwaysOn 健全狀況 - 第 2 部：進階 Cmdlet 使用](/archive/blogs/sqlalwayson/monitoring-alwayson-health-with-powershell-part-2-advanced-cmdlet-usage)  
  
     [使用 PowerShell 監視 AlwaysOn 健全狀況 - 第 3 部：簡單監控應用程式](/archive/blogs/sqlalwayson/monitoring-alwayson-health-with-powershell-part-3-a-simple-monitoring-application)  
  
     [使用 PowerShell 監視 AlwaysOn 健全狀況 - 第 4 部：與 SQL Server Agent 整合](/archive/blogs/sqlalwayson/monitoring-alwayson-health-with-powershell-part-4-integration-with-sql-server-agent)  
  
     [SQL Server AlwaysOn 團隊部落格：官方 SQL Server AlwaysOn 團隊部落格](/archive/blogs/sqlalwayson/)  
  
     [CSS SQL Server 工程師部落格](/archive/blogs/psssql/)  
  
-   **白皮書：**  
  
     [Microsoft 的 SQL Server 2012 白皮書](https://social.technet.microsoft.com/wiki/contents/articles/13146.white-paper-gallery-for-sql-server.aspx#[Category]SQLServer2012)  
  
     [SQL Server 客戶諮詢團隊白皮書](https://techcommunity.microsoft.com/t5/DataCAT/bg-p/DataCAT/)  
  
## <a name="see-also"></a>另請參閱  
 [AlwaysOn 可用性群組目錄檢視 &#40;Transact-SQL&#41;](../../../relational-databases/system-catalog-views/always-on-availability-groups-catalog-views-transact-sql.md)   
 [AlwaysOn 可用性群組動態管理檢視和函式 &#40;Transact-SQL&#41;](../../../relational-databases/system-dynamic-management-views/always-on-availability-groups-dynamic-management-views-functions.md)   
 [可用性群組自動容錯移轉的彈性容錯移轉原則 &#40;SQL Server&#41;](./configure-flexible-automatic-failover-policy.md)   
 [AlwaysOn 可用性群組概觀 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server.md)   
 [自動修復頁面 &#40;可用性群組：資料庫鏡像&#41;](../../../sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring.md)   
 [使用 AlwaysOn 儀表板 &#40;SQL Server Management Studio&#41;](../../../database-engine/availability-groups/windows/use-the-always-on-dashboard-sql-server-management-studio.md)  
  
