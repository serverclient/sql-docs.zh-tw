---
title: 在可用性群組使用讀取級別
description: 了解使用 Always On 可用性群組時如何達到讀取級別，以及如何使用分散式可用性群組達到異地讀取級別的詳細資料。
ms.custom: seodec18
ms.date: 10/24/2017
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: conceptual
ms.assetid: ''
author: cawrites
ms.author: chadam
ms.openlocfilehash: c8f5f050598129e674a74e8b924c6b2ba274cb34
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98171120"
---
# <a name="use-read-scale-with-always-on-availability-groups"></a>在 Always On 可用性群組使用讀取級別
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]

可用性群組是全面的解決方案，可將高可用性功能整合到 SQL Server，同時也可提供整合式調整解決方案。 在常見的資料庫應用程式中，多個用戶端會執行各種工作負載。 有時可能會因資源限制而產生瓶頸。 

在可用性群組的內容中，讀取級別會將讀取工作負載卸載至次要複本。 您可以釋出資源，讓 OLTP 工作負載達到更高的輸送量， 也可提供更高的唯讀工作負載效能和唯讀工作負載級別。 利用 SQL Server 最快的複寫技術建立一組複寫的資料庫，將報告和分析工作負載卸載至唯讀複本。

透過可用性群組就能設定一或多個次要複本，以支援次要資料庫的唯讀存取。

執行分析或報告工作負載的用戶端應用程式可直接連線至次要資料庫。 您也可以設定唯讀路由清單，並連線至主要資料庫。 其會接著從路由清單，以循環配置資源的方式將連線要求轉送至每個次要複本。

## <a name="read-scale-availability-groups-without-cluster"></a>沒有叢集的讀取級別可用性群組

在 [!INCLUDE[sssql15-md](../../../includes/sssql16-md.md)] 和舊版中，所有可用性群組都必須有叢集。 叢集提供了高可用性和災害復原 (HADR) 的商務持續性。 另外還會為讀取作業設定次要複本。 如果目標不是高可用性，設定和操作叢集會耗用相當多的作業額外負荷。 SQL Server 2017 引進沒有叢集的讀取級別可用性群組。 

如果您的業務需求是節省資源以供主要複本上執行的任務關鍵性工作負載使用，可以使用唯讀路由，或直接連線至可讀取的次要複本。 您無須仰賴任何叢集技術的整合。 這些新功能可供在 Windows 和 Linux 平台上執行的 SQL Server 2017 使用。

>[!IMPORTANT]
>這不是高可用性安裝。 沒有任何基礎結構可監視與協調失敗偵測和自動容錯移轉。 如果沒有叢集，SQL Server 就無法提供自動化高可用性解決方案提供的低復原時間目標 (RTO)。 如果您需要高可用性功能，請使用叢集管理員 (在 Windows 為 Windows Server 容錯移轉叢集，在 Linux 則為 Pacemaker)。
>
>讀取級別可用性群組可提供災害復原功能。 當唯讀複本在同步認可模式下時，提供的復原點目標 (RPO) 為零。 若要容錯移轉讀取級別可用性群組，請參閱[容錯移轉讀取級別可用性群組上的主要複本](perform-a-planned-manual-failover-of-an-availability-group-sql-server.md#ReadScaleOutOnly)。

## <a name="use-distributed-availability-groups-for-geographic-read-scale"></a>將分散式可用性群組用於地理讀取級別

地理不同的解決方案可以使用分散式可用性群組來實作讀取級別解決方案。 您可用來將從主要複本到可讀取次要複本的讀取工作負載，卸載到較接近讀取工作負載來源的網站。 分散式可用性群組會減少主要複本上的資源使用率， 同時也會減低網路延遲並利用專用資源，增加讀取輸送量。

單一分散式可用性群組最多可以有 17 個可讀取次要複本。 若要增加縮放容量，可透過菊輪鍊鏈結多個可用性群組，進一步增加可讀取複本數目。 您也可以在地理分散的環境中，從相同可用性群組取兩個可用性群組，加以部署成兩個分散式可用性群組，以取得低延遲讀取。




## <a name="next-steps"></a>後續步驟

[在 Linux 上設定讀取級別可用性群組](../../../linux/sql-server-linux-availability-group-configure-rs.md)

[在 Windows 上設定讀取級別可用性群組](../../../database-engine/availability-groups/windows/configure-read-scale-availability-groups.md)

## <a name="see-also"></a>另請參閱

 [AlwaysOn 可用性群組的概觀 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server.md)
