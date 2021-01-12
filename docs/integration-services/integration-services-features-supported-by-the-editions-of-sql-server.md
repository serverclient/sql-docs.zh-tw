---
description: SQL Server 版本支援的 Integration Services 功能
title: SQL Server 各版本支援的 Integration Services 功能 | Microsoft Docs
ms.custom: ''
ms.date: 07/26/2017
ms.prod: sql
ms.prod_service: integration-services
ms.reviewer: ''
ms.technology: integration-services
ms.topic: conceptual
ms.assetid: e5018225-68bb-4f34-ae4a-ead79d8ad13a
author: chugugrace
ms.author: chugu
ms.openlocfilehash: 2a8b60c36831622713103786727c4225ac0c351c
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98095640"
---
# <a name="integration-services-features-supported-by-the-editions-of-sql-server"></a>SQL Server 各版本支援的 Integration Services 功能

[!INCLUDE[sqlserver-ssis](../includes/applies-to-version/sqlserver-ssis.md)]


 本主題提供不同 [!INCLUDE[ssNoVersion_md](../includes/ssnoversion-md.md)]版本所支援之 SQL Server Integration Services (SSIS) 功能的詳細資訊。  

如需 Evaluation 和 Developer Edition 所支援的功能，請參閱下列各表中針對 Enterprise Edition 所列的功能。
  
如需最新版本資訊和新功能資訊，請參閱下列文章：
-   [SQL Server 2016 版本資訊](../sql-server/sql-server-2016-release-notes.md)
-   [SQL Server 2016 Integration Services 的新功能](../integration-services/what-s-new-in-integration-services-in-sql-server-2016.md)
-   [SQL Server 2017 中的 Integration Services 新增功能](../integration-services/what-s-new-in-integration-services-in-sql-server-2017.md)
    
**試用 SQL Server 2016！**    

SQL Server Evaluation Edition 提供了 180 天的試用期。  
    
> [![從 Evaluation Center 下載](/analysis-services/analysis-services/media/download.png)](https://www.microsoft.com/evalcenter/evaluate-sql-server-2016) **[從 Evaluation Center 下載 SQL Server 2016](https://www.microsoft.com/evalcenter/evaluate-sql-server-2016)**    
    
## <a name="new-integration-services-features-in-sql-server-2017"></a><a name="ISNew"></a> SQL Server 2017 的新 Integration Services 功能
  
|功能|Enterprise|標準|Web|Express with Advanced Services|Express|  
|-------------|----------------|--------------|---------|------------------------------------|------------------------|  
|Scale Out Master|是|||||
|Scale Out Worker|是|是 <sup>1</sup>|TBD|TBD|TBD|
|OData 元件中的 Microsoft Dynamics AX 和 Microsoft Dynamics CRM 支援 <sup>2</sup>|是|是||||
|Linux 支援|是|是|||是|

<sup>1</sup> 如果您執行的套件需要 Scale Out 中的僅限企業功能，則 Scale Out Worker 也必須在 SQL Server Enterprise 執行個體上執行。

<sup>2</sup> SQL Server 2016 Service Pack 1 也支援這項功能。

## <a name="sql-server-import-and-export-wizard"></a><a name="IEWiz"></a> SQL Server 匯入和匯出精靈

|功能|Enterprise|標準|Web|Express with Advanced Services|Express|  
|-------------|----------------|--------------|---------|------------------------------------|------------------------|  
|SQL Server 匯入和匯出精靈|是|是|是|是<sup>1</sup>|是<sup>1</sup>|

<sup>1</sup> DTSWizard.exe 未隨附於 Linux 上的 SQL。 不過，Linux 上的 dtexec 可以用來執行 DTSWizard on Windows 所建立的套件。


## <a name="integration-services"></a><a name="IS"></a> Integration Services  
  
|功能|Enterprise|標準|Web|Express with Advanced Services|Express|  
|-------------|----------------|--------------|---------|------------------------------------|------------------------|  
|內建的資料來源連接器|是|是|||| 
|內建工作和轉換|是|是||||  
|ODBC 來源和目的地 |是|是|||| 
|Azure 資料來源連接器和工作|是|是||||  
|Hadoop/HDFS 連接器和工作|是|是||||  
|基本資料分析工具|是|是|||| 

## <a name="integration-services---advanced-sources-and-destinations"></a><a name="ISAA"></a> Integration Services - 進階來源和目的地  
  
|功能|Enterprise|標準|Web|Express with Advanced Services|Express|  
|-------------|----------------|--------------|---------|------------------------------------|------------------------|  
|Attunity 的高效能 Oracle 來源和目的地|是|||||  
|Attunity 的高效能 Teradata 來源和目的地|是|||||  
|SAP BW 來源和目的地|是|||||  
|資料採礦模型定型目的地|是|||||  
|維度處理目的地|是|||||  
|分割區處理目的地|是|||||  
  
## <a name="integration-services---advanced-tasks-and-transformations"></a><a name="ISAT"></a> Integration Services - 進階工作和轉換  
  
|功能|Enterprise|標準|Web|Express with Advanced Services|Express|  
|-------------|----------------|--------------|---------|------------------------------------|------------------------|  
|Attunity 異動資料擷取元件 <sup>1</sup>|是|||||  
|資料採礦查詢轉換|是|||||  
|模糊群組和模糊查閱轉換|是|||||  
|詞彙擷取與詞彙查閱轉換|是|||||  

<sup>1</sup> Attunity 異動資料擷取元件需要 Enterprise Edition。 不過，異動資料擷取服務和異動資料擷取設計工具不需要 Enterprise Edition。 您可以在未安裝 SSIS 的電腦上使用設計工具和服務。