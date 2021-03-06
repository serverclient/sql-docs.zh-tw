---
title: 了解伺服器事件的 WMI 提供者
description: 瞭解伺服器事件的 WMI 提供者如何使用 Windows Management Instrumentation，藉由將 SQL Server 轉換為 managed WMI 物件來監視事件。
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: wmi
ms.topic: reference
helpviewer_keywords:
- architecture [WMI]
- SQL Server Agent [WMI]
- WMI Provider for Server Events, about WMI Provider for Server Events
ms.assetid: 8fd7bd18-76d0-4b28-8fee-8ad861441ab2
author: markingmyname
ms.author: maghan
ms.openlocfilehash: 6187c97f246a8c4eb445fb0afe224922b2c57f03
ms.sourcegitcommit: 783b35f6478006d654491cb52f6edf108acf2482
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91891958"
---
# <a name="understanding-the-wmi-provider-for-server-events"></a>了解伺服器事件的 WMI 提供者
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
  WMI Provider for Server Events 可讓您使用 Windows Management Instrumentation (WMI) 在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]中監視事件。 提供者的運作方式是將 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 轉換成 Managed WMI 物件。 使用這個提供者的 WMI 可利用在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中產生事件通知的任何事件。 另外，因為 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent 是與 WMI 互動的管理應用程式，所以它可回應這些事件，方法是增加 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent 在舊版本上所涵蓋的事件範圍。  
  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent 之類的管理應用程式可藉由發出 WMI 查詢語言 (WQL) 陳述式，使用伺服器事件的 WMI 提供者來存取 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 事件。 WQL 是結構化查詢語言 (SQL) 的簡化子集，具有一些 WMI 特定的延伸模組。 在使用 WQL 時，應用程式會針對特定的資料庫或資料庫物件來擷取事件類型。 伺服器事件的 WMI 提供者會將查詢轉譯為事件通知，以便在目標資料庫中有效地建立事件通知。 如需有關如何在中使用事件通知的詳細資訊 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ，請參閱 [伺服器事件概念的 WMI 提供者](./wmi-provider-for-server-events-concepts.md)。 可以查詢的事件會列在 [伺服器事件類別和屬性的 WMI 提供者](../../relational-databases/wmi-provider-server-events/wmi-provider-for-server-events-classes-and-properties.md)中。  
  
 當觸發事件通知傳送訊息的事件發生時，訊息會移至 **msdb** 中名為 **SQL/notification/ProcessWMIEventProviderNotification/** v1.0 的預先定義目標服務。 服務會將事件放入名為**WMIEventProviderNotificationQueue**的**msdb**中預先定義的佇列。  (在第一次連接到服務時，提供者會動態建立服務和佇列 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。 ) 提供者會在此佇列中讀取事件資料，並將其轉換為受管理物件格式 (MOF) ，然後再將它傳回給應用程式。 下圖顯示這項程序。  
  
 ![WMI Provider for Server Events 的流程圖](../../relational-databases/wmi-provider-server-events/media/wmi-provider-functional-spec.gif "WMI Provider for Server Events 的流程圖")  
  
 例如，請考慮使用下列 WQL 查詢：  
  
```  
SELECT * FROM DDL_DATABASE_LEVEL_EVENTS  
WHERE DatabaseName = 'AdventureWorks'  
```  
  
 回應此查詢時，伺服器事件的 WMI 提供者會在目標資料庫中建立相等的事件通知：  
  
```  
USE AdventureWorks ;  
GO  
CREATE EVENT NOTIFICATION SQLWEP_76CF38C1_18BB_42DD_A7DC_C8820155B0E9  
    ON DATABASE  
    WITH FAN_IN  
    FOR DDL_DATABASE_LEVEL_EVENTS  
    TO SERVICE  
        'SQL/Notifications/ProcessWMIEventProviderNotification/v1.0',   
        'A7E5521A-1CA6-4741-865D-826F804E5135';  
GO  
```  
  
 在此範例中，`SQLWEP_76CF38C1_18BB_42DD_A7DC_C8820155B0E9` 是由前置詞 [!INCLUDE[tsql](../../includes/tsql-md.md)] 和 GUID 構成的 `SQLWEP_` 識別碼。 `SQLWEP` 會為每個識別碼建立一個新的 GUID。 `A7E5521A-1CA6-4741-865D-826F804E5135`子句中的值 `TO SERVICE` 是 GUID，可識別**msdb**資料庫中的 broker 實例。  
  
 如需如何使用 WQL 的詳細資訊，請參閱 [使用 wql 搭配伺服器事件的 WMI 提供者](https://technet.microsoft.com/library/ms180524\(v=sql.105\).aspx)。  
  
 管理應用程式會藉由連接到提供者所定義的 WMI 命名空間，將該伺服器事件的 WMI 提供者導向至 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的執行個體。 Windows WMI 服務會將此命名空間對應至提供者 DLL (Sqlwep.dll,) 並將其載入至記憶體。 此提供者會針對的每個實例管理伺服器事件的 WMI 命名空間 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ，而且格式為： \\ \\ 。 \\*根*\Microsoft\SqlServer\ServerEvents \\ *instance_name*，其中*instance_name*預設為 MSSQLSERVER。 如需有關如何連接到實例之 WMI 命名空間的詳細資訊 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ，請參閱 [使用 WQL 搭配伺服器事件的 Wmi 提供者](https://technet.microsoft.com/library/ms180524\(v=sql.105\).aspx)。  
  
 不管伺服器上有多少 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體，都只會將提供者 DLL (Sqlwep.dll) 載入到伺服器作業系統的 WMI 主機服務一次。  
  
 如需 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 使用伺服器事件之 Wmi 提供者的代理程式管理應用程式範例，請參閱 [範例：使用伺服器事件的 Wmi 提供者建立 SQL Server Agent 警示](./sample-creating-a-sql-server-agent-alert-with-the-wmi-provider.md)。 如需在 managed 程式碼中使用伺服器事件的 WMI 提供者的管理應用程式範例，請參閱 [範例：在 managed 程式碼中使用 Wmi 事件提供者](./sample-using-the-wmi-event-provider-with-the-net-framework.md)。 您也可以在 SDK 中取得 WMI 的詳細資訊 [!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[dnprdnshort](../../includes/dnprdnshort-md.md)] 。  
  
## <a name="see-also"></a>另請參閱  
 [伺服器事件的 WMI 提供者概念](./wmi-provider-for-server-events-concepts.md)  
  
