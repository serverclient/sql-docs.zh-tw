---
title: Always On 可用性群組：互通性
description: 描述可以及無法與 Always On 可用性群組搭配運作的各種功能。
ms.custom: seodec18
ms.date: 04/20/2017
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: reference
helpviewer_keywords:
- Availability Groups [SQL Server], about
- Availability Groups [SQL Server], interoperability
ms.assetid: daf87f90-2623-42ca-912c-b8f07d210510
author: cawrites
ms.author: chadam
ms.openlocfilehash: a7c1e12e2e0253f0f22df8dd5ea841671adc94ec
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97643777"
---
# <a name="always-on-availability-groups-interoperability-sql-server"></a>Always On 可用性群組：互通性 (SQL Server)
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]

本主題記錄 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] 與 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 中其他 [!INCLUDE[ssCurrent](../../../includes/sscurrent-md.md)]功能的互通性。

## <a name="features-that-interoperate-with-always-on-availability-groups"></a><a name="Interop"></a> 與 Always On 可用性群組互通的功能

下表列出與 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 中 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] 互通的 [!INCLUDE[ssCurrent](../../../includes/sscurrent-md.md)]功能。 **[詳細資訊]** 資料行中的連結表示有適用於所指功能的互通性考量。

|功能|相關資訊|
|:------|:---------------|
|變更資料擷取|[複寫、變更追蹤、變更資料擷取和 AlwaysOn 可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/replicate-track-change-data-capture-always-on-availability.md)|
|變更追蹤|[複寫、變更追蹤、變更資料擷取和 AlwaysOn 可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/replicate-track-change-data-capture-always-on-availability.md)|
|自主資料庫|[自主資料庫與 AlwaysOn 可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/contained-databases-with-always-on-availability-groups-sql-server.md)|
|資料庫加密|[加密的資料庫與 AlwaysOn 可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/encrypted-databases-with-always-on-availability-groups-sql-server.md)|
|資料庫快照集|[資料庫快照集與 AlwaysOn 可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/database-snapshots-with-always-on-availability-groups-sql-server.md)|
|FILESTREAM 與 FileTable|[FILESTREAM 和 FileTable 與 AlwaysOn 可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/filestream-and-filetable-with-always-on-availability-groups-sql-server.md)|
|全文檢索搜尋|注意:全文檢索索引與 Always On 次要資料庫同步。|
|記錄傳送|[從記錄傳送移轉至 AlwaysOn 可用性群組的必要條件 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/prereqs-migrating-log-shipping-to-always-on-availability-groups.md)|
|遠端 Blob 存放區 (RBS)|[遠端 Blob 存放區 &#40;RBS&#41; 及 AlwaysOn 可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/remote-blob-store-rbs-and-always-on-availability-groups-sql-server.md)|
|複寫|[設定 AlwaysOn 可用性群組的複寫 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/configure-replication-for-always-on-availability-groups-sql-server.md)<br /><br /> [維護 AlwaysOn 發行集資料庫 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/maintaining-an-always-on-publication-database-sql-server.md)<br /><br /> [複寫、變更追蹤、變更資料擷取和 AlwaysOn 可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/replicate-track-change-data-capture-always-on-availability.md)<br /><br /> [複寫訂閱者及 AlwaysOn 可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/replication-subscribers-and-always-on-availability-groups-sql-server.md)|
|Analysis Services|[Analysis Services 與 AlwaysOn 可用性群組](../../../database-engine/availability-groups/windows/analysis-services-with-always-on-availability-groups.md)|
|Reporting Services|利用唯讀次要複本做為報表資料來源，減少主要讀寫複本上的負載。<br /><br /> [使用 AlwaysOn 可用性群組的 Reporting Services &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/reporting-services-with-always-on-availability-groups-sql-server.md)|
|Service Broker|[使用 AlwaysOn 可用性群組的 Service Broker &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/service-broker-with-always-on-availability-groups-sql-server.md)|
|SQL Server Agent|&nbsp;|

## <a name="features-that-interoperate-with-always-on-availability-groups-with-restrictions"></a><a name="restrictions"></a> 與 AlwaysOn 可用性群組相互溝通的功能限制

下列可與 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] 相互溝通的功能有特定限制。 如需詳細資訊，請參閱連結主題。

- 跨資料庫交易/分散式交易 ([!INCLUDE[ssSQL15](../../../includes/sssql15-md.md)] 和 Windows Server 2016)。 如需詳細資訊，請參閱 [AlwaysOn 可用性群組和資料庫鏡像的跨資料庫交易和分散式交易 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/transactions-always-on-availability-and-database-mirroring.md)
- [查詢統計資料系統資料收集器](../../../relational-databases/data-collection/system-data-collection-set-reports.md#Query)無法在具有不可讀取次要項目的環境中可靠地執行。 若要使用查詢統計資料系統資料收集器，請將所有次要可用性群組複本設為允許[讀取存取](configure-read-only-access-on-an-availability-replica-sql-server.md)。 

## <a name="features-that-do-not-interoperate-with-always-on-availability-groups"></a><a name="NoInterop"></a> 不與 AlwaysOn 可用性群組互通的功能

[!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] 無法與下列功能互通：

- 資料庫鏡像。 如需詳細資訊，請參閱[資料庫鏡像或 AlwaysOn 可用性群組不支援跨資料庫交易 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/transactions-always-on-availability-and-database-mirroring.md)。

## <a name="related-content"></a><a name="RelatedContent"></a> 相關內容

- **部落格：**

  [Migration Guide:Migrating to SQL Server 2012 Failover Clustering and Availability Groups from Prior Clustering and Mirroring Deployments](/archive/blogs/sqlalwayson/now-available-migration-guide-migrating-to-sql-server-2012-failover-clustering-and-availability-groups-from-prior-clustering-and-mirroring-deployments) (移轉指南：從先前叢集和鏡像部署移轉至 SQL Server 2012 容錯移轉叢集和可用性群組)
  [SQL Server Always On 小組部落格：官方 SQL Server Always On 小組部落格](/archive/blogs/sqlalwayson/)
  [CSS SQL Server 工程師部落格](/archive/blogs/psssql/)

- **白皮書：**

  [Migration Guide:Migrating to Always On Availability Groups from Prior Deployments Combining Database Mirroring and Log Shipping](/previous-versions/sql/sql-server-2012/jj635217(v=msdn.10)) (移轉指南：從結合資料庫鏡像與記錄傳送的先前部署移轉至 Always On 可用性群組)
  [Microsoft SQL Server Always On Solutions Guide for High Availability and Disaster Recovery](/previous-versions/sql/sql-server-2012/hh781257(v=msdn.10)) (Microsoft SQL Server Always On 高可用性和災害復原解決方案指南)
  [Microsoft 的 SQL Server 2012 白皮書](https://social.technet.microsoft.com/wiki/contents/articles/13146.white-paper-gallery-for-sql-server.aspx#[Category]SQLServer2012)
  [SQL Server 客戶諮詢小組白皮書](https://techcommunity.microsoft.com/t5/DataCAT/bg-p/DataCAT/)

## <a name="see-also"></a>另請參閱

[Always On 可用性群組概觀 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server.md)
[Always On 可用性群組的必要條件、限制和建議 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/prereqs-restrictions-recommendations-always-on-availability.md)