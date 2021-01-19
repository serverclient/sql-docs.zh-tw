---
description: 交易 - 可用性群組和資料庫鏡像
title: 交易：可用性群組和資料庫鏡像
descripton: Learn about cross-database and distributed transaction support for SQL Server Always On availability groups and database mirroring.
ms.custom: seo-lt-2019
ms.date: 12/11/2018
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: conceptual
helpviewer_keywords:
- database mirroring [SQL Server], interoperability
- cross-database transactions [SQL Server]
- transactions [database mirroring]
- Availability Groups [SQL Server], interoperability
- troubleshooting [SQL Server], cross-database transactions
ms.assetid: 9f7ed895-ad65-43e3-ba08-00d7bff1456d
author: cawrites
ms.author: chadam
ms.openlocfilehash: f2295f8694a9573b0e9c541ea817e9e03e70f83e
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98171080"
---
# <a name="transactions---availability-groups-and-database-mirroring"></a>交易 - 可用性群組和資料庫鏡像
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]

本文描述 AlwaysOn 可用性群組和資料庫鏡像的跨資料庫和分散式交易支援。  

## <a name="support-for-distributed-transactions"></a>分散式交易支援

SQL Server 2017 支援可用性群組中多個資料庫的分散式交易。 這項支援包含相同 SQL Server 執行個體上的資料庫或不同 SQL Server 執行個體上的資料庫。 針對資料庫鏡像所設定的資料庫不支援分散式交易。

> [!NOTE]
> [!INCLUDE[SQL Server 2016](../../../includes/sssql16-md.md)] Service Pack 2 和更新版本提供對於可用性群組中分散式交易的完整支援。 
> 
> 在 Service Pack 2 之前的 [!INCLUDE[SQL Server 2016](../../../includes/sssql16-md.md)] 版本中，不支援牽涉到可用性群組中資料庫的跨資料庫分散式交易 (也就是使用相同 SQL Server 執行個體上資料庫的交易)。

若要設定分散式交易的可用性群組，請參閱[設定分散式交易的可用性群組](configure-availability-group-for-distributed-transactions.md)。

如需詳細資訊，請參閱：

- [DTC Administration Guide](/previous-versions/windows/desktop/ms681291(v=vs.85)) (DTC 系統管理指南)
- [DTC Developers Guide](/previous-versions/windows/desktop/ms679938(v=vs.85)) (DTC 開發人員指南)
- [DTC Programmers Reference](/previous-versions/windows/desktop/ms686108(v=vs.85)) (DTC 程式設計人員參考)

## <a name="sql-server-2016-sp1-and-before-support-for-cross-database-transactions-within-the-same-sql-server-instance"></a>SQL Server 2016 SP1 和以前版本：相同 SQL Server 執行個體內的跨資料庫交易支援  

在 SQL Server 2016 SP1 和以前版本中，可用性群組不支援相同 SQL Server 執行個體內的跨資料庫交易。 相同 SQL Server 執行個體不可能在跨資料庫交易中裝載兩個資料庫，如果任一或兩個資料庫都在可用性群組中的話。 這些資料庫是相同可用性群組的一部分時，此限制也同樣適用。  
  
資料庫鏡像也不支援跨資料庫交易。  
  
##  <a name="sql-server-2016-sp1-and-before-support-for-distributed-transactions"></a><a name="dtcsupport"></a> SQL Server 2016 SP1 和以前版本：分散式交易支援  
當由不同的 SQL Server 執行個體裝載資料庫時，會以可用性群組支援分散式交易。 它也適用於 SQL Server 與另一部符合 DTC 規範的伺服器之間的分散式交易。  
 
Microsoft Distributed Transaction Coordinator (MSDTC 或 DTC) 是一項 Windows 服務，提供分散式系統的交易基礎結構。 MSDTC 允許用戶端應用程式在一筆交易中包含多個資料來源，接著即可跨此交易包含的所有伺服器認可交易。 例如，您可以使用 MSDTC 協調跨越不同伺服器上多個資料庫的交易。

SQL Server 2016 引進此功能，以便在可用性群組中有一或多個該筆交易的資料庫時，使用分散式交易。 在 SQL Server 2016 之前，不支援對可用性群組中的多個資料庫進行分散式交易。 SQL Server 2016 可以為每個資料庫註冊一個資源管理員。 這項新功能使得分散式交易得以在可用性群組中包含多個資料庫。
  
 必須符合下列需求：  
  
-   可用性群組必須在 Windows Server 2012 R2 或更新版本上執行。 針對 Windows Server 2012 R2，您必須安裝 [https://support.microsoft.com/kb/3090973](https://support.microsoft.com/kb/3090973) 上所提供 KB3090973 中的更新。  
  
-   必須使用 **CREATE AVAILABILITY GROUP** 命令和 **WITH DTC\_SUPPORT = PER_DB** 子句建立可用性群組。 您目前無法改變現有可用性群組。  

- 要參與可用性群組的所有 SQL Server 執行個體都必須是 SQL Server 2016 或更新版本。
 
 ## <a name="non-support-for-distributed-transactions"></a>不支援分散式交易
 不支援分散式交易的特定案例包括：
 
 - 在 SQL Server 2016 SP1 和以前版本中，交易涉及的多個資料庫在相同的可用性群組中。
 
 - 在 SQL Server 2016 SP1 和以前版本中，至少有一個資料庫位於可用性群組中，而另一個資料庫位於相同的 SQL Server 執行個體上。 
 
 - 未使用啟用分散式交易建立可用性群組時。
 
 - 資料庫鏡像。
 
 > [!IMPORTANT]
 > 為 DTC 無法解析的環境交易，決定適當的交易預設結果。  如需如何設定預設結果的資訊，請參閱 [不能肯定的交易解析伺服器組態選項](../../../database-engine/configure-windows/in-doubt-xact-resolution-server-configuration-option.md)。
  
## <a name="example-scenario-with-database-mirroring"></a>範例資料庫鏡像案例  
 下列資料庫鏡像範例將說明如何發生邏輯不一致的情況。 在此範例中，應用程式會使用跨資料庫交易來插入兩個資料列：其中一個資料列會插入鏡像資料庫 A 中的資料表，而另一個資料列則插入另一個資料庫 B 中的資料表。然後，資料庫 A 會在具有自動容錯移轉的高安全性模式下進行鏡像。 認可交易時，資料庫 A 會無法使用，而鏡像工作階段便自動容錯移轉至資料庫 A 的鏡像。  
  
 容錯移轉之後，在資料庫 B 上認可跨資料庫交易可能會成功，但不見得能順利在容錯移轉資料庫上認可。 例如，如果資料庫 A 的原始主體伺服器在故障之前尚未將跨資料庫交易的記錄傳送至鏡像伺服器。 容錯移轉之後，該筆交易不會存在新主體伺服器上。 資料庫 A 和 B 會成為不一致，因為插入資料庫 B 的資料仍維持完整，但是插入資料庫 A 的資料已經遺失。  
  
 使用 MS DTC 交易時可能也會發生類似的狀況。 例如，在容錯移轉之後，新的主體會連絡 MS DTC。 不過，MS DTC 並不了解新的主體伺服器，而且它會結束「準備認可」的所有交易，因為它認為這些交易已在其他資料庫中認可。  
  
> [!NOTE]  
>  不支援搭配使用資料庫鏡像或可用性群組與本文未核准的 DTC。  這並不表示不支援與 DTC 產品無關之產品的各個層面；不過，不支援不當使用分散式交易所造成的任何問題。  
  
## <a name="next-steps"></a>後續步驟  
 [Always On 可用性群組：互通性 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/always-on-availability-groups-interoperability-sql-server.md)  
  
