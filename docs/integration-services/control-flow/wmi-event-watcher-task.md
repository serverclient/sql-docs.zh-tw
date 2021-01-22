---
description: WMI 事件監看員工作
title: WMI 事件監看員工作 | Microsoft Docs
ms.custom: ''
ms.date: 03/01/2017
ms.prod: sql
ms.prod_service: integration-services
ms.reviewer: ''
ms.technology: integration-services
ms.topic: conceptual
f1_keywords:
- sql13.dts.designer.wmieventwatchertask.f1
- sql13.dts.designer.wmieventwatcher.general.f1
- sql13.dts.designer.wmieventwatcher.wmiquery.f1
helpviewer_keywords:
- WQL [Integration Services]
- WMI Event Watcher task [Integration Services]
ms.assetid: b5bb52e9-a77e-41e1-93f9-d4c3bc6b2c9a
author: chugugrace
ms.author: chugu
ms.openlocfilehash: 8b4e04871bf0264c9e9c723365f2a29d95265ffa
ms.sourcegitcommit: d8cdbb719916805037a9167ac4e964abb89c3909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/20/2021
ms.locfileid: "98594933"
---
# <a name="wmi-event-watcher-task"></a>WMI 事件監看員工作

[!INCLUDE[sqlserver-ssis](../../includes/applies-to-version/sqlserver-ssis.md)]


  「WMI 事件監看員」工作使用 Management Instrumentation 查詢語言 (WQL) 事件查詢來監看 Windows Management Instrumentation (WMI) 事件，以指定感興趣的事件。 您可將「WMI 事件監看員」工作用於下列用途：  
  
-   等待檔案已加入資料夾的通知，然後起始檔案的處理。  
  
-   當伺服器的可用記憶體降至低於指定的百分比時，執行刪除檔案的封裝。  
  
-   監看應用程式的安裝，然後執行使用此應用程式的封裝。  
  
 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 包含讀取 WMI 資訊的工作。  
  
 如需有關這項工作的詳細資訊，請按下列主題：  
  
-   [WMI 資料讀取器工作](../../integration-services/control-flow/wmi-data-reader-task.md)  
  
## <a name="wql-queries"></a>WQL 查詢  
 WQL 是 SQL 用語，其包含的延伸模組可支援 WMI 事件通知和其他 WMI 特定功能。 如需有關 WQL 的詳細資訊，請參閱 [MSDN Library](/documentation/)中的 Windows Management Instrumentation 文件集。  
  
> [!NOTE]  
>  不同 Windows 版本的 WMI 類別也有所不同。  
  
 以下查詢監看 CPU 使用超過 40% 時發出的通知。  
  
```  
SELECT * from __InstanceModificationEvent WITHIN 2 WHERE TargetInstance ISA 'Win32_Processor' and TargetInstance.LoadPercentage > 40  
```  
  
 以下查詢監看檔案加入資料夾後發出的通知。  
  
```  
SELECT * FROM __InstanceCreationEvent WITHIN 10 WHERE TargetInstance ISA "CIM_DirectoryContainsFile" and TargetInstance.GroupComponent= "Win32_Directory.Name=\"c:\\\\WMIFileWatcher\""   
```  
  
## <a name="custom-logging-messages-available-on-the-wmi-event-watcher-task"></a>WMI 事件監看員工作上可用的自訂記錄訊息  
 下表列出「WMI 事件監看員」工作的自訂記錄項目。 如需詳細資訊，請參閱 [集成服務 &#40;SSIS&#41; 記錄](../../integration-services/performance/integration-services-ssis-logging.md)。  
  
|記錄項目|描述|  
|---------------|-----------------|  
|**WMIEventWatcherEventOccurred**|指出發生了工作正在進行監視的事件。|  
|**WMIEventWatcherTimedout**|指出工作已經逾時。|  
|**WMIEventWatcherWatchingForWMIEvents**|指出工作已經開始執行 WQL 查詢。 項目包含查詢。|  
  
## <a name="configuration-of-the-wmi-event-watcher-task"></a>WMI 事件監看員工作的組態  
 您可以利用下列方式設定「WMI 資料讀取器」工作：  
  
-   指定要使用的 WMI 連接管理員。  
  
-   指定 WQL 查詢的來源。 來源可以在工作的外部，可以是一個變數或檔案，或者查詢可儲存於工作屬性內。  
  
-   指定發生 WMI 事件時工作採取的行動。 您可以記錄事件通知和事件後的狀態，或者引發自訂 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 事件，以提供與 WMI 事件、通知及事件後狀態相關聯的資訊。  
  
-   定義工作回應事件的方式。 工作可以根據事件設定為成功或失敗，也可以讓工作只再次監看事件。  
  
-   指定 WMI 查詢逾時後工作採取的行動。您可以記錄逾時及逾時後的狀態，或者引發一個自訂 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 事件，用來指示 WMI 事件逾時，並記錄逾時和逾時狀態。  
  
-   定義工作回應逾時的方式。工作可設定為成功或失敗，也可以讓工作只再次監看事件。  
  
-   指定工作監看事件的次數。  
  
-   指定逾時。  
  
 如果來源是一個檔案，則「WMI 事件監看員」工作會使用「檔案」連接管理員以連接到檔案。 如需相關資訊，請參閱 [Flat File Connection Manager](../../integration-services/connection-manager/flat-file-connection-manager.md)。  
  
 「WMI 事件監看員」工作使用 WMI 連接管理員連接到可從中讀取 WMI 資訊的伺服器。 如需相關資訊，請參閱 [WMI Connection Manager](../../integration-services/connection-manager/wmi-connection-manager.md)。  
  
 您可以透過 [!INCLUDE[ssIS](../../includes/ssis-md.md)] 設計師或以程式設計方式設定屬性。  
  
 如需有關可在 [!INCLUDE[ssIS](../../includes/ssis-md.md)] 設計師中設定之屬性的詳細資訊，請按下列主題：  
  
-   [運算式頁面](../../integration-services/expressions/expressions-page.md)  
  
 如需有關如何在「 [!INCLUDE[ssIS](../../includes/ssis-md.md)] 設計師」中設定這些屬性的詳細資訊，請按下列主題：  
  
-   [設定工作或容器的屬性](./add-or-delete-a-task-or-a-container-in-a-control-flow.md)  
  
## <a name="programmatic-configuration-of-the-wmi-event-watcher-task"></a>WMI 事件監看員工作的程式設計組態  
 如需有關以程式設計方式設定這些屬性的詳細資訊，請按下列主題：  
  
-   <xref:Microsoft.SqlServer.Dts.Tasks.WmiEventWatcherTask.WmiEventWatcherTask>  
  
## <a name="wmi-event-watcher-task-editor-general-page"></a>WMI 事件監看員工作編輯器 (一般頁面)
  使用 [WMI 事件監看員工作編輯器]  對話方塊的 [一般]  頁面，即可命名和描述 WMI 事件監看員工作。  
  
 如需 WMI 查詢語言 (WQL) 的詳細資訊，請參閱 MSDN Library 中的 Windows Management Instrumentation 主題 [Querying with WQL](/windows/win32/wmisdk/querying-with-wql)(使用 WQL 查詢)。  
  
### <a name="options"></a>選項。  
 **名稱**  
 為「WMI 事件監看員」工作提供唯一的名稱。 這個名稱是作為工作圖示中的標籤使用。  
  
> [!NOTE]  
>  工作名稱在封裝內必須是唯一的。  
  
 **說明**  
 輸入 WMI 事件監看員工作的描述。  
  
## <a name="wmi-event-watcher-task-editor-wmi-options-page"></a>WMI 事件監看員工作編輯器 (WMI 選項頁面)
  使用 [WMI 事件監看員工作編輯器]  對話方塊的 [WMI 選項]  頁面，即可指定 Windows Management Instrumentation 查詢語言 (WQL) 查詢的來源，以及 WMI 事件監看員工作如何回應 Microsoft Windows Instrumentation (WMI) 事件。  
  
 如需 WMI 查詢語言 (WQL) 的詳細資訊，請參閱 MSDN Library 中的 Windows Management Instrumentation 主題 [Querying with WQL](/windows/win32/wmisdk/querying-with-wql)(使用 WQL 查詢)。  
  
### <a name="static-options"></a>靜態選項  
 **WMIConnectionName**  
 在清單中選取 WMI 連線管理員，或按一下 \<**New WMI Connection...**> 來建立新的連線管理員。  
  
 **相關主題：** [WMI 連線管理員](../../integration-services/connection-manager/wmi-connection-manager.md)、[WMI 連線管理員編輯器](../connection-manager/wmi-connection-manager.md)  
  
 **WQLQuerySourceType**  
 選取工作執行之 WQL 查詢的來源類型。 這個屬性具有下表中所列的選項。  
  
|值|描述|  
|-----------|-----------------|  
|**直接輸入**|設定 WQL 查詢的來源。 選取此值會顯示動態選項 [WQLQuerySource]  。|  
|**檔案連接**|選取包含 WQL 查詢的檔案。 選取此值會顯示動態選項 [WQLQuerySource]  。|  
|**變數**|將來源設定為定義 WQL 查詢的變數。 選取此值會顯示動態選項 [WQLQuerySource]  。|  
  
 **ActionAtEvent**  
 指定 WMI 事件是否記錄事件並起始 [!INCLUDE[ssIS](../../includes/ssis-md.md)] 動作，或僅記錄事件。  
  
 **AfterEvent**  
 指定工作接收到 WMI 事件之後成功或失敗，或是工作會繼續監看事件再次發生。  
  
 **ActionAtTimeout**  
 指定工作是否記錄 WMI 查詢逾時並起始 [!INCLUDE[ssIS](../../includes/ssis-md.md)] 事件作為回應，或僅記錄逾時。  
  
 **AfterTimeout**  
 指定工作回應逾時成功或失敗，或是工作會繼續監看另一個逾時再次發生。  
  
 **NumberOfEvents**  
 指定要監看的事件數目。  
  
 **逾時**  
 指定要等候事件發生的秒數。 值為 0 表示沒有逾時在作用中。  
  
### <a name="wqlquerysource-dynamic-options"></a>WQLQuerySource 動態選項  
  
#### <a name="wqlquerysource--direct-input"></a>WQLQuerySource = 直接輸入  
 **WQLQuerySource**  
 提供查詢，或按一下省略符號按鈕 (...)，然後使用 [WQL 查詢]  對話方塊輸入查詢。  
  
#### <a name="wqlquerysource--file-connection"></a>WQLQuerySource = 檔案連接  
 **WQLQuerySource**  
 在清單中選取檔案連線管理員，或按一下 [\<**New connection...**>] 以建立新的連線管理員。  
  
 **相關主題：** [檔案連線管理員](../../integration-services/connection-manager/file-connection-manager.md)、[檔案連線管理員編輯器](../connection-manager/file-connection-manager.md)  
  
#### <a name="wqlquerysource--variable"></a>WQLQuerySource = 變數  
 **WQLQuerySource**  
 在清單中選取變數，或按一下 \<**New variable...**> 來建立新的變數。  
  
 **相關主題：** [Integration Services &#40;SSIS&#41; 變數](../../integration-services/integration-services-ssis-variables.md)、[新增變數](../integration-services-ssis-variables.md)