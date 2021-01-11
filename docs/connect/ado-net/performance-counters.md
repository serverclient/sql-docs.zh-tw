---
title: SqlClient 中的效能計數器
description: 使用 Microsoft SqlClient Data Provider for SQL Server 效能計數器，透過使用 Windows 效能監視器或以程式設計方式，來監視應用程式狀態及其連線資源。
ms.date: 12/04/2020
dev_langs:
- csharp
ms.assetid: 0b121b71-78f8-4ae2-9aa1-0b2e15778e57
ms.prod: sql
ms.prod_service: connectivity
ms.technology: connectivity
ms.topic: conceptual
author: David-Engel
ms.author: v-daenge
ms.reviewer: v-chmalh
ms.openlocfilehash: e9d8c2edb88a9ed50b47c761d3af8aec8016065a
ms.sourcegitcommit: 5b2c47ce88f7e56552fd415c32b319009d043b56
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/29/2020
ms.locfileid: "97804297"
---
# <a name="performance-counters-in-sqlclient"></a>SqlClient 中的效能計數器

[!INCLUDE[appliesto-netfx-xxxx-xxxx-md](../../includes/appliesto-netfx-xxxx-xxxx-md.md)]

[!INCLUDE[Driver_ADONET_Download](../../includes/driver_adonet_download.md)]

您可以使用 <xref:Microsoft.Data.SqlClient> 效能計數器來監視應用程式的狀態，以及其所使用的連線資源。 效能計數器可藉由「Windows 效能監視器」來進行監視，或可藉由 <xref:System.Diagnostics.PerformanceCounter> 命名空間 (Namespace) 中的 <xref:System.Diagnostics> 類別以程式設計的方式存取。

## <a name="available-performance-counters"></a>可用的效能計數器

目前有 14 個不同的效能計數器可供 <xref:Microsoft.Data.SqlClient> 使用，如下表所述。

|效能計數器|描述|  
|-------------------------|-----------------|  
|`HardConnectsPerSecond`|每秒鐘對資料庫伺服器所建立的連接數目。|  
|`HardDisconnectsPerSecond`|每秒鐘對資料庫伺服器所進行的中斷連接數目。|  
|`NumberOfActiveConnectionPoolGroups`|使用中的唯一連接集區群組的數目。 這個計數器是由 AppDomain 中找到的唯一連接字串數目所控制。|  
|`NumberOfActiveConnectionPools`|連接集區的總數。|  
|`NumberOfActiveConnections`|目前使用中的現用連接的數目。 **注意：** 此效能計數器預設未啟用。 若要啟用此效能計數器，請參閱[啟用依預設關閉的計數器](#ActivatingOffByDefault)。|  
|`NumberOfFreeConnections`|連接集區中可用的連接數目。 **注意：** 此效能計數器預設未啟用。 若要啟用此效能計數器，請參閱[啟用依預設關閉的計數器](#ActivatingOffByDefault)。|  
|`NumberOfInactiveConnectionPoolGroups`|標示為清除的唯一連接集區群組的數目。 這個計數器是由 AppDomain 中找到的唯一連接字串數目所控制。|  
|`NumberOfInactiveConnectionPools`|最近未有任何活動且正等候處置 (Dispose) 的非現用連接集區數目。|  
|`NumberOfNonPooledConnections`|尚未共用的現用連接的數目。|  
|`NumberOfPooledConnections`|正由連接共用基礎結構所管理的現用連接的數目。|  
|`NumberOfReclaimedConnections`|已透過記憶體回收作業回收的連接數目，其中 `Close` 或 `Dispose` 並非由應用程式呼叫。 **注意** 未明確關閉或處置連線會影響效能。|  
|`NumberOfStasisConnections`|目前正等候動作完成因此無法提供應用程式使用的連接數目。|  
|`SoftConnectsPerSecond`|正從連接集區提取的現用連接的數目。 **注意：** 此效能計數器預設未啟用。 若要啟用此效能計數器，請參閱[啟用依預設關閉的計數器](#ActivatingOffByDefault)。|  
|`SoftDisconnectsPerSecond`|正傳回至連接集區的現用連接的數目。 **注意：** 此效能計數器預設未啟用。 若要啟用此效能計數器，請參閱[啟用依預設關閉的計數器](#ActivatingOffByDefault)。|  

### <a name="connection-pool-groups-and-connection-pools"></a>連接集區群組與連接集區

在使用「Windows 驗證」(整合式安全性) 時，必須同時監控 `NumberOfActiveConnectionPoolGroups` 和 `NumberOfActiveConnectionPools` 效能計數器。 原因是連接集區群組會對應至唯一的連接字串。 使用整合式安全性時，連接集區會對應至連接字串，並針對個別的 Windows 識別 (Identity) 額外建立獨立的集區。 例如，如果 Fred 和 Julie 位於相同的 AppDomain 內，且兩者都使用連接字串 `"Data Source=MySqlServer;Integrated Security=true"`，則會針對連接字串建立連接集區群組，並針對 Fred 和 Julie 建立兩個額外的集區。 如果 John 與 Martha 使用具有相同 SQL Server 登入 `"Data Source=MySqlServer;User Id=<myUserID>;Password=<myPassword>"` 的連接字串，則只會針對 **<myUserID>**  識別建立單一集區。

<a name="ActivatingOffByDefault"></a>

### <a name="activate-off-by-default-counters"></a>啟用依預設關閉的計數器

效能計數器 `NumberOfFreeConnections`、`NumberOfActiveConnections`、`SoftDisconnectsPerSecond` 和 `SoftConnectsPerSecond` 預設會關閉。 請將下列資訊加入至應用程式的組態檔加以啟用：

```xml  
<system.diagnostics>  
  <switches>  
    <add name="ConnectionPoolPerformanceCounterDetail"  
         value="4"/>  
  </switches>  
</system.diagnostics>  
```  

## <a name="retrieve-performance-counter-values"></a>擷取效能計數器值

下列主控台應用程式顯示如何在應用程式中擷取效能計數器值。 連線必須已開啟並在作用中，才能針對所有 Microsoft SqlClient Data Provider for SQL Server 傳回資訊。

> [!NOTE]
> 這個範例會使用 [**AdventureWorks** 範例資料庫](../../samples/adventureworks-install-configure.md)。 範例程式碼中提供的連接字串假設資料庫已在本機電腦上安裝並可供使用，而且您已建立與連接字串中所提供之登入相符的登入。 如果伺服器是使用僅允許「Windows 驗證」的預設安全性設定而設定，則可能需要啟用 SQL Server 登入。 請視環境需要修改連接字串。

### <a name="example"></a>範例

[!code-csharp[SqlClient_PerformanceCounter#1](~/../sqlclient/doc/samples/SqlClient_PerformanceCounter.cs#1)]

## <a name="see-also"></a>另請參閱

- [連線到資料來源](connecting-to-data-source.md)
- [執行階段分析](/dotnet/framework/debug-trace-profile/runtime-profiling)
- [監視效能閾值的簡介](/previous-versions/visualstudio/visual-studio-2008/bd20x32d(v=vs.90))
- [Microsoft ADO.NET for SQL Server](microsoft-ado-net-sql-server.md)
