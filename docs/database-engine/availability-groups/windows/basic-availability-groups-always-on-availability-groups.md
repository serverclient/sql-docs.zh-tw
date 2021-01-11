---
title: 單一資料庫的基本可用性群組
description: '描述一般與基本 Always On 可用性群組之間的差異，並描述如何設定基本可用性群組。 '
ms.custom: seodec18
ms.date: 02/01/2018
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: how-to
ms.assetid: 285adbc7-ac9b-40f6-b4a9-3f1591d3b632
author: cawrites
ms.author: chadam
ms.openlocfilehash: 50db7fba110c33cc6c50211cd1b431bf1380ba26
ms.sourcegitcommit: 44eebb659f9b226c08aea6c31a909b22ad4fec60
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2021
ms.locfileid: "97860587"
---
# <a name="basic-always-on-availability-groups-for-a-single-database"></a>單一資料庫的基本 Always On 可用性群組
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]

 Always On 基本可用性群組可為 SQL Server (2016 版與更新版本) Standard Edition 提供高可用性解決方案。 基本可用性群組支援單一資料庫的容錯移轉環境。 其建立和管理類似 Enterprise Edition 的傳統 (進階) [AlwaysOn 可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/always-on-availability-groups-sql-server.md)。 本文摘要說明基本可用性群組的差異和限制。

## <a name="features"></a>特性  
 AlwaysOn 基本可用性群組取代了過時的資料庫鏡像功能，並提供類似的功能支援層級。 基本可用性群組可讓主要資料庫維護單一複本。 此複本可使用同步認可模式或非同步認可模式。 如需可用性模式的詳細資訊，請參閱[可用性模式 &#40;AlwaysOn 可用性群組&#41;](../../../database-engine/availability-groups/windows/availability-modes-always-on-availability-groups.md)。 除非需要容錯移轉，否則次要複本會維持非使用中狀態。 此容錯移轉會將主要角色指派和次要角色指派反轉，使得次要複本成為主要的使用中資料庫。 如需容錯移轉的詳細資訊，請參閱[容錯移轉及容錯移轉模式 &#40;AlwaysOn 可用性群組&#41;](../../../database-engine/availability-groups/windows/failover-and-failover-modes-always-on-availability-groups.md)。 基本可用性群組可在跨越內部部署和 Microsoft Azure 的混合式環境中運作。  
  
## <a name="limitations"></a>限制  
 基本可用性群組使用相較於 SQL Server 2016 Enterprise Edition 上進階可用性群組的一小部分功能。 基本可用性群組包含下列限制︰  
  
- 兩個複本 (主要和次要) 的限制。 Linux 上的 SQL Server 2017 基本可用性群組支援額外的僅設定複本。
  
- 次要複本上沒有讀取權限。  
  
- 次要複本上沒有備份。  

- 次要複本上沒有完整性檢查。 

- 不支援將複本裝載於執行 SQL Server 2016 Community Technology Preview 3 (CTP3) 之前版本 SQL Server 的伺服器上。  

- 支援一個可用性資料庫。  
  
- 基本可用性群組無法升級至進階可用性群組。 您必須卸除群組，再重新加入包含只執行 SQL Server 2016 Enterprise Edition 之伺服器的群組。  
  
- 只有 Standard Edition 伺服器才支援基本可用性群組。 

- 基本可用性群組不能是分散式可用性群組的一部分。 

- 您可能會有多個基本可用性群組連線到 SQL Server 的單一執行個體。

  
## <a name="configuration"></a>組態  
 AlwaysOn 基本可用性群組可在任兩部 SQL Server 2016 Standard Edition 伺服器上建立。 當您建立基本可用性群組時，您必須在建立期間指定兩個複本。  
  
 若要建立基本可用性群組，請使用 **CREATE AVAILABILITY GROUP** Transact-SQL 命令，並指定 **WITH BASIC** 選項 (預設值為 **ADVANCED**)。 您也可以使用 SQL Server Management Studio 17.8 版或更新版本的 UI，建立基本的可用性群組。 如需詳細資訊，請參閱 [CREATE AVAILABILITY GROUP &#40;Transact-SQL&#41;](../../../t-sql/statements/create-availability-group-transact-sql.md)。 

請參閱下列使用 Transact-SQL (T-SQL) 建立基本可用性群組的範例： 

```sql
CREATE AVAILABILITY GROUP [BasicAG]
WITH (AUTOMATED_BACKUP_PREFERENCE = PRIMARY,
BASIC,
DB_FAILOVER = OFF,
DTC_SUPPORT = NONE,
REQUIRED_SYNCHRONIZED_SECONDARIES_TO_COMMIT = 0)
FOR DATABASE [AdventureWorks]
REPLICA ON N'SQLVM1\MSSQLSERVER' WITH (ENDPOINT_URL = N'TCP://SQLVM1.Contoso.com:5022', FAILOVER_MODE = AUTOMATIC, AVAILABILITY_MODE = SYNCHRONOUS_COMMIT, SEEDING_MODE = AUTOMATIC, SECONDARY_ROLE(ALLOW_CONNECTIONS = NO)),
    N'SQLVM2\MSSQLSERVER' WITH (ENDPOINT_URL = N'TCP://SQLVM2.Contoso.com:5022', FAILOVER_MODE = AUTOMATIC, AVAILABILITY_MODE = SYNCHRONOUS_COMMIT, SEEDING_MODE = AUTOMATIC, SECONDARY_ROLE(ALLOW_CONNECTIONS = NO));

GO
```

  
> [!NOTE]  
>  基本可用性群組的限制會套用至指定 **WITH BASIC** 的 **CREATE AVAILABILITY GROUP** 命令。 例如，如果您嘗試建立允許讀取權限的基本可用性群組，就會收到錯誤。 其他限制也會以相同方式套用。 如需詳細資訊，請參閱本主題的＜限制＞一節。  
  
## <a name="see-also"></a>另請參閱  
 [AlwaysOn 可用性群組概觀 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server.md)  
  
  
