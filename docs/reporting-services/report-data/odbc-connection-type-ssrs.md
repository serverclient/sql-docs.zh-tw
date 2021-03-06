---
title: ODBC 連線類型 | Microsoft Docs
description: 請運用本文中有關 ODBC 連線類型的資訊，以了解如何建立資料來源。
ms.date: 03/17/2017
ms.prod: reporting-services
ms.prod_service: reporting-services-native
ms.technology: report-data
ms.topic: conceptual
ms.assetid: 24163866-f37a-4c38-982e-c3d79bf64d4c
author: maggiesMSFT
ms.author: maggies
ms.openlocfilehash: b8e41696695ec8a95dd16d1f30724147cdd359a1
ms.sourcegitcommit: 9470c4d1fc8d2d9d08525c4f811282999d765e6e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/17/2020
ms.locfileid: "86458970"
---
# <a name="odbc-connection-type-ssrs"></a>ODBC 連接類型 (SSRS)
  若要加入來自 ODBC 資料提供者的資料，您必須具有以 ODBC 類型之報表資料來源為基礎的資料集。 這個內建資料來源類型是以 [!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] ODBC 資料處理延伸模組為基礎。  
  
 您可以使用本主題中的資訊來建置資料來源。 如需逐步指示，請參閱 [加入及驗證資料連接 &#40;報表產生器及 SSRS&#41;](../../reporting-services/report-data/add-and-verify-a-data-connection-report-builder-and-ssrs.md)。  
  
##  <a name="connection-string"></a><a name="Connection"></a> 連接字串  
 ODBC 資料處理延伸模組的連接字串會視您所要的 ODBC 驅動程式而定。 一般連接字串包含驅動程式支援的名稱/值組。 例如，下列連接字串便指 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client 和 AdventureWorks 資料庫的 ODBC 驅動程式：  
  
```  
Driver={SQL Server Native Client 10.0};Server=server;Database=AdventureWorks;Trusted_Connection=yes;  
```  
  
  
##  <a name="credentials"></a><a name="Credentials"></a> 認證  
 需要有認證才能夠執行報表、於本機預覽報表並且從報表伺服器預覽報表。  
  
 發行報表之後，您可能需要變更資料來源的認證，如此當報表在報表伺服器上執行時，擷取資料的權限就會是有效的。  
  
 如果您設定 ODBC 資料來源以提示密碼或將密碼包含在連接字串中，則當使用者輸入含有特殊字元 (例如標點符號) 的密碼時，某些基礎資料來源驅動程式無法驗證這些特殊字元。 當您處理報表時，訊息「不是有效密碼」可能會指出此問題。 如果無法變更此密碼，您可以和資料庫管理員一起合作，將適當的認證儲存在報表伺服器上，當做系統 ODBC 資料來源名稱 (DSN) 的一部分。 如需詳細資訊，請參閱 [!INCLUDE[dnprdnshort](../../includes/dnprdnshort-md.md)] SDK 文件集中的＜OdbcConnection.ConnectionString＞。  
  
> [!NOTE]  
>  建議您不要在連接字串中加入登入資訊，例如密碼。 報表產生器會在 **[資料來源]** 對話方塊中提供另一個索引標籤，您可以使用此索引標籤來輸入認證。  
  
 如需詳細資訊，請參閱[建立資料連接字串 - 報表產生器 & SSRS](../../reporting-services/report-data/data-connections-data-sources-and-connection-strings-report-builder-and-ssrs.md) 或[指定報表資料來源的認證及連接資訊](specify-credential-and-connection-information-for-report-data-sources.md)。  
  
  
##  <a name="remarks"></a><a name="Remarks"></a> 備註  
 ODBC 是在 OLEDB 之前的早期資料存取技術。 ODBC 只能支援關聯式資料來源。 ODBC 資料提供者稱為 *「驅動程式」* (Driver)。 ODBC 驅動程式由 Microsoft 和協力廠商提供。 例如，Microsoft Office 會安裝連接至 Office 檔案格式的 ODBC 驅動程式。  
  
 在建立 ODBC 連接字串之前，您必須已安裝 ODBC 驅動程式並建立機器或系統資料來源名稱 (DSN)。 若要成功擷取您想要的資料，您必須提供驅動程式支援的查詢語法。 參數支援會因驅動程式而異。 如需詳細資訊，請參閱所選驅動程式的特定主題，例如 [SQL Server Native Client &#40;ODBC&#41;](../../relational-databases/native-client/odbc/sql-server-native-client-odbc.md)。  
  
###### <a name="platform-and-version-information"></a>平台和版本資訊  
 如需特定 ODBC 資料提供者的詳細資訊，請參閱 [Reporting Services 支援的資料來源 &#40;SSRS&#41;](../../reporting-services/report-data/data-sources-supported-by-reporting-services-ssrs.md)。
  
  
##  <a name="how-to-topics"></a><a name="HowTo"></a> 如何主題  
 本節包含使用資料連接、資料來源與資料集的逐步指示。  
  
 [加入及驗證資料連接 &#40;報表產生器及 SSRS&#41;](../../reporting-services/report-data/add-and-verify-a-data-connection-report-builder-and-ssrs.md)  
  
 [建立共用資料集或內嵌資料集 &#40;報表產生器及 SSRS&#41;](../../reporting-services/report-data/create-a-shared-dataset-or-embedded-dataset-report-builder-and-ssrs.md)  
  
 [將篩選加入資料集中 &#40;報表產生器及 SSRS&#41;](../../reporting-services/report-data/add-a-filter-to-a-dataset-report-builder-and-ssrs.md)  
  
  
##  <a name="related-sections"></a><a name="Related"></a> 相關章節  
 本文件集的這些章節會提供報表資料的深入概念性資訊，以及如何定義、自訂和使用與報表資料相關組件的程序資訊。  
  
 [報表資料集 &#40;SSRS&#41;](../../reporting-services/report-data/report-datasets-ssrs.md)  
 提供存取報表資料的概觀。  
  
 [建立資料連接字串 - 報表產生器及 SSRS](../../reporting-services/report-data/data-connections-data-sources-and-connection-strings-report-builder-and-ssrs.md)  
 提供資料連接與資料來源的相關資訊。  
  
 [報表內嵌資料集和共用資料集 &#40;報表產生器及 SSRS&#41;](../../reporting-services/report-data/report-embedded-datasets-and-shared-datasets-report-builder-and-ssrs.md)  
 提供內嵌與共用資料集的相關資訊。  
  
 [資料集欄位集合 &#40;報表產生器及 SSRS&#41;](../../reporting-services/report-data/dataset-fields-collection-report-builder-and-ssrs.md)  
 提供查詢所產生之資料集欄位集合的相關資訊。  
  
 [Data Sources Supported by Reporting Services &#40;SSRS&#41;](../../reporting-services/report-data/data-sources-supported-by-reporting-services-ssrs.md) (Reporting Services 支援的資料來源 &#40;SSRS&#41;)。 提供支援每一個資料延伸模組之平台與版本的深入資訊。  
  
  
## <a name="see-also"></a>另請參閱  
 [報表參數 &#40;報表產生器和報表設計師&#41;](../../reporting-services/report-design/report-parameters-report-builder-and-report-designer.md)   
 [篩選、分組和排序資料 &#40;報表產生器及 SSRS&#41;](../../reporting-services/report-design/filter-group-and-sort-data-report-builder-and-ssrs.md)   
 [運算式 &#40;報表產生器及 SSRS&#41;](../../reporting-services/report-design/expressions-report-builder-and-ssrs.md)  
  
  
