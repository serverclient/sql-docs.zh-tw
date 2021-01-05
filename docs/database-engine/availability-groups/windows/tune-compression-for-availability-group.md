---
title: 微調可用性群組的壓縮 | Microsoft Docs
description: 了解 SQL Server 如何壓縮可用性群組的資料流，這樣可以減少網路流量、增加 CPU 負載，而且可能引發延遲。
ms.custom: ''
ms.date: 06/22/2016
ms.prod: sql
ms.technology: availability-groups
ms.topic: conceptual
ms.assetid: 7632769c-b246-4766-886f-7c60ec540be8
author: cawrites
ms.author: chadam
ms.openlocfilehash: 1066b1e519f757207b06f1deb94031f2f054bc5f
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97641845"
---
# <a name="tune-compression-for-availability-group"></a>微調可用性群組的壓縮
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]
依預設，SQL Server 會適合可用性群組的時機壓縮資料流。 壓縮會減少網路流量、增加 CPU 負載，並可能誘使延遲。 您必須是系統管理員 (sysadmin) 固定伺服器角色的成員，才能啟用壓縮。 下表顯示 SQL Server 針對可用性群組記錄資料流使用壓縮的時機︰

| 狀況 | 壓縮設定
| ---- | ----
| 同步認可複本 | 未壓縮
| 非同步認可複本 | Compressed
| 自動植入期間 | 未壓縮
| 已在資料庫中啟用 TDE  | 未壓縮

## <a name="trace-flags-for-availability-group-compression"></a>可用性群組壓縮的追蹤旗標 

在大部分情況下，Microsoft 不建議變更這些設定。 若要測試變更這些設定，您可以使用全域追蹤旗標。 SQL Server 會對整個執行個體套用全域追蹤旗標。 執行個體中的所有可用性群組都將會受到這些設定影響。  

下表顯示變更 SQL Server 預設壓縮行為的追蹤旗標。 

追蹤旗標 | 描述
------------- | -------------
1462          | 針對具有非同步複本的可用性群組停用記錄資料流壓縮。 在非同步複本上預設會啟用這項功能，來最佳化網路頻寬。
9567          | 在自動植入期間，針對可用性群組啟用資料流的壓縮。 在自動植入期間，壓縮可以大幅縮短傳輸時間，而且會增加處理器負載。
9592          | 針對具有同步複本的可用性群組啟用記錄資料流壓縮。 因為壓縮會增加延遲，所以在同步複本上預設會停用這項功能。 非同步複本則預設會啟用記錄資料流壓縮。


## <a name="resources"></a>資源


[Database Engine 啟動選項](../../../database-engine/configure-windows/database-engine-service-startup-options.md)

[自動植入](./automatically-initialize-always-on-availability-group.md)

[AlwaysOn 必要條件](prereqs-restrictions-recommendations-always-on-availability.md)