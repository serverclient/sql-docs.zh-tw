---
title: 建立報表伺服器資料庫 (組態管理員) | Microsoft Docs
description: SQL Server Reporting Services 原生模式會使用兩個 SQL Server 關聯式資料庫來儲存報表伺服器中繼資料和物件。 一個資料庫做為主要儲存體，而另一個用來儲存暫存資料。
author: maggiesMSFT
ms.author: maggies
ms.prod: reporting-services
ms.prod_service: reporting-services-native
ms.topic: conceptual
ms.custom: seodec18
ms.date: 9/2/2020
ms.openlocfilehash: 1b7eb1e521e1c987cb956d054c4c17beba819462
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98172360"
---
# <a name="create-a-report-server-database-report-server-configuration-manager"></a>建立報表伺服器資料庫 (報表伺服器組態管理員)  

[!INCLUDE [ssrs-appliesto](../../includes/ssrs-appliesto.md)] [!INCLUDE [ssrs-appliesto-2016-and-later](../../includes/ssrs-appliesto-2016-and-later.md)] [!INCLUDE[ssrs-appliesto-pbirsi](../../includes/ssrs-appliesto-pbirs.md)] [!INCLUDE[ssrs-appliesto-sharepoint-2013-2016i](../../includes/ssrs-appliesto-sharepoint-2013-2016.md)]

[!INCLUDE [ssrs-previous-versions](../../includes/ssrs-previous-versions.md)]

SQL Server [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] 原生模式會使用兩個 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 關聯式資料庫來儲存報表伺服器中繼資料和物件。 一個資料庫做為主要儲存體，而另一個用來儲存暫存資料。 

兩個資料庫會一起建立，並依名稱繫結。 使用預設的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體時，資料庫會命名為 **reportserver** 和 **reportservertempdb**。 這兩個資料庫統稱為 **報表伺服器資料庫** 或 **報表伺服器目錄**。

::: moniker range="=sql-server-2016"

SQL Server [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] **SharePoint 模式** 含有第三個資料庫，其用於資料警示中繼資料。 系統會為每個 SSRS 服務應用程式建立這三個資料庫。 根據預設，資料庫名稱包括代表服務應用程式的 GUID。 

以下是這三個 SharePoint 模式資料庫的範例名稱：

- ReportingService_90a9f37075544f22953c4a62e4a9f370  
  
- ReportingService_90a9f37075544f22953c4a62e4a9f370TempDB  
  
- ReportingService_90a9f37075544f22953c4a62e4a9f370_Alerting  

::: moniker-end
  
> [!IMPORTANT]  
> 請勿撰寫對報表伺服器資料庫執行查詢的應用程式。 報表伺服器資料庫並非公用結構描述。 前後版次的資料表結構可能會變更。 如果您撰寫的應用程式需要存取報表伺服器資料庫，請一定要使用 SQL Server [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] API 來存取報表伺服器資料庫。  
>
> 執行記錄檢視是這個規則的例外。 如需詳細資訊，請參閱 [報表伺服器 ExecutionLog 和 ExecutionLog3 檢視](../../reporting-services/report-server/report-server-executionlog-and-the-executionlog3-view.md)。  
  
## <a name="ways-to-create-the-report-server-database"></a>建立報表伺服器資料庫的方法

 ### <a name="native-mode"></a>原生模式
 您可以利用下列方式建立原生模式報表伺服器資料庫：  
  
- **自動**： 如果您選擇預設設定選項來進行安裝，就會使用 [SQL Server 安裝精靈]。 在 [SQL Server 安裝精靈] 中，這個選項會是 [報表伺服器安裝選項] 頁面中的 [安裝和設定]。 如果選擇 [僅安裝] 選項，則必須使用報表伺服器組態管理員以建立資料庫。  
  
- **手動**： 使用 SQL Server [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] 組態管理員。 如果您要使用遠端 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 來裝載報表伺服器資料庫，就必須手動建立資料庫。 如需詳細資訊，請參閱[建立原生模式報表伺服器資料庫](../../reporting-services/install-windows/ssrs-report-server-create-a-native-mode-report-server-database.md)。  

::: moniker range="=sql-server-2016"
  
### <a name="sharepoint-mode"></a>SharePoint 模式 
[報表伺服器安裝選項]  頁面只有一個用於 SharePoint 模式的選項：[僅安裝]  。 這個選項會安裝所有 SQL Server [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] 檔案及 SQL Server [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] 共用服務。 下一步是透過下列其中一種方式建立至少一個 SSRS 服務應用程式：  
  
- 前往 SharePoint Server 的管理中心建立 SSRS 服務應用程式。 如需詳細資訊，請參閱 [在 SharePoint 模式中安裝第一部報表伺服器](../../reporting-services/install-windows/install-the-first-report-server-in-sharepoint-mode.md#bkmk_create_serrviceapplication)的 **建立服務應用程式** 章節。  
  
- 使用 SQL Server [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] PowerShell 指令程式建立服務應用程式和報表伺服器資料庫。 如需詳細資訊，請參閱 [Reporting Services SharePoint 模式的 PowerShell Cmdlet](../../reporting-services/report-server-sharepoint/powershell-cmdlets-for-reporting-services-sharepoint-mode.md) 主題中的建立服務應用程式範例。  

::: moniker-end
  
## <a name="database-server-version-requirements"></a>資料庫伺服器版本需求

 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 可用來主控報表伺服器資料庫。 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 執行個體可以在本機或遠端。 下列支援的 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 版本可以裝載報表伺服器資料庫：  
::: moniker range=">=sql-server-ver15"

- Azure SQL 受控執行個體

- SQL Server 2019

::: moniker-end
::: moniker range=">=sql-server-2017"

- SQL Server 2017  
::: moniker-end

- [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)]  
  
- [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)]  
  
- [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)]  

如果您在遠端電腦上建立報表伺服器資料庫，請設定連線以使用網域使用者帳戶，或是擁有網路存取權的服務帳戶。 如果您使用遠端 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體，請考慮報表伺服器要用來連線到執行個體的認證。 如需詳細資訊，請參閱[設定報表伺服器資料庫連線 &#40;報表伺服器組態管理員&#41;](../../reporting-services/install-windows/configure-a-report-server-database-connection-ssrs-configuration-manager.md)。  
  
> [!IMPORTANT]  
> 報表伺服器與裝載報表伺服器資料庫的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體，可以在不同的網域中。 若是網際網路部署，常會使用位於防火牆後方的伺服器。 
>
> 如果您為了網際網路存取而設定報表伺服器，請使用 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 認證來連線到位於防火牆後方的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體。 請使用 IPSEC 來保護連線。  
  
## <a name="edition-requirements-for-a-database-server"></a>資料庫伺服器版本需求 

 建立報表伺服器資料庫時，並非所有 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 版本都可以用來裝載資料庫。 如需詳細資訊，請參閱 [SQL Server 版本所支援的 Reporting Services 功能](../reporting-services-features-supported-by-the-editions-of-sql-server-2016.md)的[報表伺服器資料庫伺服器版本需求](../reporting-services-features-supported-by-the-editions-of-sql-server-2016.md#edition-requirements-for-the-report-server-database)。  

## <a name="next-steps"></a>後續步驟

請參閱[報表伺服器組態管理員](reporting-services-configuration-manager-native-mode.md)。  

更多問題嗎？ 歡迎到 [Reporting Services 論壇](https://go.microsoft.com/fwlink/?LinkId=620231)提問。