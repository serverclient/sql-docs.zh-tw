---
description: 資料分割處理目的地
title: 資料分割處理目的地 | Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: integration-services
ms.reviewer: ''
ms.technology: integration-services
ms.topic: conceptual
f1_keywords:
- sql13.dts.designer.partitionprocessingdest.f1
- sql13.dts.designer.partprocessingtransformation.connection.f1
- sql13.dts.designer.partprocessingtransformation.mapping.f1
- sql13.dts.designer.partprocessingtransformation.advanced.f1
helpviewer_keywords:
- partitions [Analysis Services], processing
- Partition Processing destination [Integration Services]
- destinations [Integration Services], Partition Processing
ms.assetid: 36c592ff-3f78-4a58-b496-31c1c8eee131
author: chugugrace
ms.author: chugu
ms.openlocfilehash: a3ee00006285ff8959ecba27624a491954e5a1bc
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98091638"
---
# <a name="partition-processing-destination"></a>資料分割處理目的地

[!INCLUDE[sqlserver-ssis](../../includes/applies-to-version/sqlserver-ssis.md)]


  「資料分割處理」目的地會載入及處理 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] 資料分割。 如需資料分割的詳細資訊，請參閱[資料分割 &#40;Analysis Services - 多維度資料&#41;](/analysis-services/multidimensional-models-olap-logical-cube-objects/partitions-analysis-services-multidimensional-data)。  
  
 「資料分割處理」目的地包含下列功能：  
  
-   執行累加式、完整或更新處理的選項。  
  
-   指定在發生指定數目的錯誤之後，處理會忽略錯誤還是停止的錯誤組態。  
  
-   將輸入資料行對應至資料分割資料行。  
  
 如需處理 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] 物件的詳細資訊，請參閱[處理選項和設定 &#40;Analysis Services&#41;](/analysis-services/multidimensional-models/processing-options-and-settings-analysis-services)。  
  
> [!NOTE]  
>  此處描述的工作不適用於 Analysis Services 表格式模型。  您無法針對表格式模型，將輸入資料行對應至資料分割資料行。 您可以改用 Analysis Services 執行 DDL 工作 ( [Analysis Services Execute DDL Task](../../integration-services/control-flow/analysis-services-execute-ddl-task.md) ) 來處理資料分割。  
  
## <a name="configuration-of-the-partition-processing-destination"></a>設定資料分割處理目的地  
 資料分割處理目的地會使用 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] 連接管理員，以連接到 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] 專案或包含目的地所處理之 Cube 及資料分割的 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] 執行個體。 如需相關資訊，請參閱 [Analysis Services Connection Manager](../../integration-services/connection-manager/analysis-services-connection-manager.md)。  
  
 此目的地擁有一個輸入。 它不支援錯誤輸出。  
  
 您可以透過 [!INCLUDE[ssIS](../../includes/ssis-md.md)] 設計師或以程式設計方式設定屬性。  
  
 **[進階編輯器]** 對話方塊會反映能以程式設計的方式設定之屬性。 如需有關可以在 **[進階編輯器]** 對話方塊中或以程式設計方式設定之屬性的詳細資訊，請按下列其中一個主題：  
  
-   [Common Properties](./set-the-properties-of-a-data-flow-component.md)  
  
-   [資料分割處理目的地自訂屬性](../../integration-services/data-flow/partition-processing-destination-custom-properties.md)  
  
 如需如何設定屬性的詳細資訊，請參閱 [設定資料流程元件的屬性](../../integration-services/data-flow/set-the-properties-of-a-data-flow-component.md)。  
  
## <a name="partition-processing-destination-editor-connection-manager-page"></a>資料分割處理目的地編輯器 (連接管理員頁面)
  使用 [資料分割處理目的地編輯器] 對話方塊的 [連線管理員] 頁面，以指定 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] 專案或 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] 執行個體的連線。  
  
> [!NOTE]  
>  此處描述的工作不適用於 Analysis Services 表格式模型。  您無法針對表格式模型，將輸入資料行對應至資料分割資料行。 您可以改用 Analysis Services 執行 DDL 工作 ( [Analysis Services Execute DDL Task](../../integration-services/control-flow/analysis-services-execute-ddl-task.md) ) 來處理資料分割。  
  
### <a name="options"></a>選項。  
 **[ODBC 目的地編輯器]**  
 從清單中選取現有的連線管理員，或按一下 [新增]  來建立新的連線。  
  
 **新增**  
 使用 [加入 Analysis Services 連接管理員] 對話方塊來建立新的連接。  
  
 **可用的資料分割清單**  
 選取要處理的資料分割。  
  
 **處理方法**  
 選取處理方法。 此選項的預設值是 **[完整]**。  
  
|值|描述|  
|-----------|-----------------|  
|加入 (累加)|執行資料分割的累加處理。|  
|完整|執行資料分割的完整處理。|  
|僅限資料|執行資料分割的更新處理。|  
  
## <a name="partition-processing-destination-editor-mappings-page"></a>資料分割處理目的地編輯器 (對應頁面)
  使用 **[資料分割處理目的地編輯器]** 對話方塊的 **[對應]** 頁面，來將輸入資料行對應至資料分割資料行。  
  
> [!NOTE]  
>  此處描述的工作不適用於 Analysis Services 表格式模型。  您無法針對表格式模型，將輸入資料行對應至資料分割資料行。 您可以改用 Analysis Services 執行 DDL 工作 ( [Analysis Services Execute DDL Task](../../integration-services/control-flow/analysis-services-execute-ddl-task.md) ) 來處理資料分割。  
  
### <a name="options"></a>選項。  
 **可用的輸入資料行**  
 檢視可用的輸入資料行清單。 使用拖放作業，即可將資料表中的可用輸入資料行對應到目的地資料行。  
  
 **可用的目的地資料行**  
 檢視可用的目的地資料行清單。 使用拖放作業，即可將資料表中的可用目的地資料行對應到輸入資料行。  
  
 **輸入資料行**  
 從上述資料表檢視選取的輸入資料行。 您可以使用 **[可用的輸入資料行]** 清單來變更對應。  
  
 **目的地資料行**  
 檢視每個可用的目的地資料行，不論是否已經對應。  
  
## <a name="partition-processing-destination-editor-advanced-page"></a>資料分割處理目的地編輯器 (進階頁面)
  使用 **[資料分割處理目的地編輯器]** 對話方塊的 **[進階]** 頁面，來設定錯誤處理。  
  
> [!NOTE]  
>  此處描述的工作不適用於 Analysis Services 表格式模型。  您無法針對表格式模型，將輸入資料行對應至資料分割資料行。 您可以改用 Analysis Services 執行 DDL 工作 ( [Analysis Services Execute DDL Task](../../integration-services/control-flow/analysis-services-execute-ddl-task.md) ) 來處理資料分割。  
  
### <a name="options"></a>選項  
 **使用預設錯誤組態。**  
 指定是否要使用預設的 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] 錯誤處理。 依預設，此值為 **True**。  
  
 **索引鍵錯誤動作**  
 指定如何處理具有無法接受之索引鍵值的記錄。  
  
|值|描述|  
|-----------|-----------------|  
|**ConvertToUnknown**|將無法接受的索引鍵值轉換為未知值。|  
|**DiscardRecord**|捨棄記錄。|  
  
 **忽略錯誤**  
 指定應該忽略的錯誤。  
  
 **發生錯誤時停止**  
 指定發生錯誤時，應該停止處理。  
  
 **錯誤數目**  
 如果您已選取 [發生錯誤時停止]，請指定處理應該停止的錯誤臨界值。  
  
 **發生錯誤時要執行的動作**  
 如果您已選取 [發生錯誤時停止]，請指定到達錯誤臨界值時要採取的動作。  
  
|值|描述|  
|-----------|-----------------|  
|**StopProcessing**|停止處理。|  
|**StopLogging**|停止記錄錯誤。|  
  
 **找不到索引鍵**  
 針對找不到索引鍵錯誤，指定要採取的動作。 依預設，此值為 **ReportAndContinue**。  
  
|值|描述|  
|-----------|-----------------|  
|**IgnoreError**|忽略錯誤並繼續處理。|  
|**ReportAndContinue**|報告錯誤並繼續處理。|  
|**ReportAndStop**|報告錯誤並停止處理。|  
  
 **重複的索引鍵**  
 針對重複索引鍵錯誤，指定要採取的動作。 依預設，此值為 **IgnoreError**。  
  
|值|描述|  
|-----------|-----------------|  
|**IgnoreError**|忽略錯誤並繼續處理。|  
|**ReportAndContinue**|報告錯誤並繼續處理。|  
|**ReportAndStop**|報告錯誤並停止處理。|  
  
 **Null 索引鍵已轉換為未知**  
 指定當 Null 索引鍵轉換為未知值的時候應採取的動作。 依預設，此值為 **IgnoreError**。  
  
|值|描述|  
|-----------|-----------------|  
|**IgnoreError**|忽略錯誤並繼續處理。|  
|**ReportAndContinue**|報告錯誤並繼續處理。|  
|**ReportAndStop**|報告錯誤並停止處理。|  
  
 **不允許 Null 索引鍵**  
 指定在不允許 Null 索引鍵的情況下如果發現 Null 索引鍵，所要採取的動作。 依預設，此值為 **ReportAndContinue**。  
  
|值|描述|  
|-----------|-----------------|  
|**IgnoreError**|忽略錯誤並繼續處理。|  
|**ReportAndContinue**|報告錯誤並繼續處理。|  
|**ReportAndStop**|報告錯誤並停止處理。|  
  
 **錯誤記錄路徑**  
 輸入錯誤記錄檔的路徑，或者使用瀏覽 **(...)** 按鈕來選取目的地。  
  
 **瀏覽 (...)**  
 選取錯誤記錄的路徑。  
  
## <a name="see-also"></a>另請參閱  
 [Integration Services 錯誤和訊息參考](../../integration-services/integration-services-error-and-message-reference.md)