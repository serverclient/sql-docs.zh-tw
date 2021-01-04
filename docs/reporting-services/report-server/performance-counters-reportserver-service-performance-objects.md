---
title: 效能計數器 - ReportServer Service 效能物件 | Microsoft Docs
description: 了解 ReportServer:Service 與 ReportServerSharePoint:Service 效能物件的效能計數器，其為 SQL Server 2012 部署的一部分。
ms.date: 06/26/2019
ms.prod: reporting-services
ms.prod_service: reporting-services-native
ms.technology: report-server
ms.topic: conceptual
helpviewer_keywords:
- Report Server service, performance counters
ms.assetid: 2bcacab2-3a4f-4aae-b123-19d756b9b9ed
author: maggiesMSFT
ms.author: maggies
monikerRange: '>=sql-server-2016 <=sql-server-2016'
ms.openlocfilehash: 8224c126d9f23cb5f13c517400ec79e04d127ff5
ms.sourcegitcommit: 1a544cf4dd2720b124c3697d1e62ae7741db757c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2020
ms.locfileid: "97466609"
---
# <a name="performance-counters---reportserver-service--performance-objects"></a>效能計數器 - ReportServer Service 效能物件
  本主題說明 **ReportServer:Service** 和 **ReportServerSharePoint:Service** 效能物件的效能計數器，這些效能物件是 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 部署的一部分。  
  
> [!NOTE]  
>  效能物件是用來監視本機報表伺服器的事件。 如果您是在向外延展部署中執行報表伺服器，則計數會套用到目前的伺服器，而非整個向外延展部署。  
  
 Windows 效能監視器 (**Perfmon.exe**) 中提供了效能物件。 如需詳細資訊，請參閱 Windows 文件集。 [執行階段分析](/dotnet/framework/debug-trace-profile/runtime-profiling) (https://msdn.microsoft.com/library/w4bz2147.aspx) 。  
  
 本主題內容：  
  
-   [ReportServer:Service 效能計數器 (原生模式報表伺服器)](#bkmk_ReportServer)  
  
-   [ReportServerSharePoint:Service (SharePoint 模式報表伺服器)](#bkmk_ReportServerSharePoint)  
  
-   [使用 PowerShell 指令程式傳回清單](#bkmk_powershell)  
  
 [!INCLUDE[applies](../../includes/applies-md.md)] [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)].  
  
##  <a name="reportserverservice-performance-counters-native-mode-report-server"></a><a name="bkmk_ReportServer"></a> ReportServer:Service 效能計數器 (原生模式報表伺服器)  
 **ReportServer:Service** 效能物件包含一組計數器集合，用來追蹤報表伺服器執行個體的 HTTP 相關事件和記憶體相關事件。 這個效能物件會針對電腦上的每個 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] 執行個體顯示一次，而且您可以在每個執行個體的效能物件中加入或移除計數器。 預設執行個體的計數器會以 **ReportServer:Service** 格式顯示。 具名執行個體的計數器會以 **ReportServer$\<**_instance_name_*_>:Service_* 格式顯示。  
  
 **ReportServer:Service** 效能物件是 [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] 的新增物件，且它會提供舊版 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] 中 Internet Information Services (IIS) 和 [!INCLUDE[vstecasp](../../includes/vstecasp-md.md)] 隨附的計數器子集。 這些新的計數器是 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]特有的，而且它們會追蹤報表伺服器的 HTTP 相關事件，例如要求、連接和登入嘗試。 此外，這個效能物件包含可追蹤記憶體管理事件的計數器。  
  
 下表將列出 **ReportServer:Service** 效能物件所包含的計數器。  
  
 ![PowerShell 相關內容](/analysis-services/analysis-services/instances/install-windows/media/rs-powershellicon.jpg "PowerShell 相關內容") 下列 Windows PowerShell 指令碼將會傳回 CounterSetName 的效能計數器清單  
  
```  
(get-counter -listset "ReportServer:Service").paths  
```  
  
|計數器|說明|  
|-------------|-----------------|  
|**Active connections**|目前在伺服器上作用中的連接數目。|  
|**Bytes Received Total**|伺服器所接收的位元組數目。 這個計數器會計算報表管理員和報表伺服器所接收的原始位元組總計。|  
|**Bytes Received/sec**|伺服器每秒所接收的位元組數目。 只有當傳送完成時，才會更新這個計數器。 這表示此計數器會維持 0，然後此值在傳送完成之後才會增加。|  
|**Bytes Sent Total**|伺服器所傳送的位元組數目。 這個計數器會計算報表管理員和報表伺服器所傳送的原始位元組總計。|  
|**Bytes Sent/sec**|伺服器每秒所傳送的位元組數目。 只有當傳送完成時，才會更新這個計數器。 這表示此計數器會維持 0，然後此值在傳送完成之後才會增加。|  
|**Errors Total**|在處理 HTTP 要求期間發生的錯誤總數。 這些錯誤包括 400s 和 500s 中的 HTTP 狀態碼。|  
|**Errors/sec**|在處理 HTTP 要求期間每秒發生的錯誤總數。 這些錯誤包括 400s 和 500s 中的 HTTP 狀態碼。|  
|**Logon Attempts Total**|RSWindows 驗證類型已經嘗試登入的次數。 RSWindows 驗證類型包括 RSWindowsNegotiate、RSWindowsNTLM、RSWindowsKerberos 和 RSWindowsBasic。 值為零 (0) 表示自訂驗證。|  
|**Logon Attempts/sec**|嘗試登入的速率。|  
|**Logon Successes Total**|RSWindows 驗證類型成功登入的次數。 RSWindows 驗證類型包括 RSWindowsNegotiate、RSWindowsNTLM、RSWindowsKerberos 和 RSWindowsBasic。 值為零 (0) 表示自訂驗證。|  
|**Logon Successes/sec**|成功登入的速率。|  
|**Memory Pressure State**|下列其中一個數字 (從 1 到 5)，表示伺服器目前的記憶體狀態：<br /><br /> 1：沒有壓力<br /><br /> 2：低度壓力<br /><br /> 3：中度壓力<br /><br /> 4：高度壓力<br /><br /> 5：壓力過高|  
|**Memory Shrink Amount**|伺服器要求壓縮使用中記憶體的位元組數目。|  
|**Memory Shrink Notifications/Sec**|伺服器在上一秒發出以壓縮使用中記憶體的通知數目。 這個值表示伺服器遇到記憶體壓力的頻率。|  
|**Requests Disconnected**|由於通訊失敗而造成中斷連接的要求數目。|  
|**Requests Executing**|目前正在處理的要求數目。|  
|**Requests Not Authorized**|失敗而且出現 HTTP 401 狀態碼的要求數目。|  
|**Requests Rejected**|由於伺服器資源不足而未處理的要求總數。 這個計數器表示傳回 HTTP 503 狀態碼 (表示伺服器太過忙碌) 的要求數目。|  
|**Requests Total**|報表伺服器服務啟動以來所收到的要求總數。 這個計數器會計算傳送至報表管理員的要求，以及從報表管理員傳送至報表伺服器的要求。|  
|**Requests/sec**|每秒處理的要求數目。 這個值代表應用程式目前的輸送量。|  
|**Tasks Queued**|等候執行緒成為可用於處理的工作數目。 對報表伺服器提出的每一要求會對應到一個或多個工作。 這個計數器只表示準備進行處理的工作數目，並不包括目前執行中的工作數目。|  
  
##  <a name="reportserversharepointservice-sharepoint-mode-report-server"></a><a name="bkmk_ReportServerSharePoint"></a> ReportServerSharePoint:Service (SharePoint 模式報表伺服器)  
 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] 中已新增 **ReportServerSharePoint:Service** 效能物件。  
  
 ![PowerShell 相關內容](/analysis-services/analysis-services/instances/install-windows/media/rs-powershellicon.jpg "PowerShell 相關內容") 下列 Windows PowerShell 指令碼將會傳回 CounterSetName 的效能計數器清單  
  
```  
(get-counter -listset "ReportServerSharePoint:Service").paths  
```  
  
|計數器|說明|  
|-------------|-----------------|  
|**Memory Pressure State**||  
|**Memory Shrink Amount**||  
|**Memory Shrink Notifications/Sec**||  
  
##  <a name="use-powershell-cmdlets-to-return-lists"></a><a name="bkmk_powershell"></a> 使用 PowerShell 指令程式傳回清單  
 ![PowerShell 相關內容](/analysis-services/analysis-services/instances/install-windows/media/rs-powershellicon.jpg "PowerShell 相關內容") 下列 Windows PowerShell 指令碼將會傳回 CounterSetName "ReportServerSharePoint:Service" 的效能計數器清單：  
  
```  
(get-counter -listset "ReportServerSharePoint:Service").paths  
```  
  
## <a name="see-also"></a>另請參閱  
 [監視報表伺服器效能](../../reporting-services/report-server/monitoring-report-server-performance.md)   
 [MSRS 2011 Web Service 和 MSRS 2011 Windows 服務效能物件的效能計數器 &#40;原生模式&#41;](../../reporting-services/report-server/performance-counters-msrs-2011-web-service-performance-objects.md)   
 [MSRS 2011 Web Service SharePoint 模式和 MSRS 2011 Windows 服務 SharePoint 模式效能物件的效能計數器 &#40;SharePoint 模式&#41;](../../reporting-services/report-server/performance-counters-msrs-2011-sharepoint-mode-performance-objects.md)  
  
