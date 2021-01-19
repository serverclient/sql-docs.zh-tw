---
description: Integration Services 服務 (SSIS 服務)
title: Integration Services 服務 (SSIS 服務) | Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: integration-services
ms.reviewer: ''
ms.technology: integration-services
ms.topic: conceptual
f1_keywords:
- sql13.ssiseditserverregistration.connectionproperties.f1
- sql13.swb.connecttodts.connectionproperties.f1
- sql13.swb.connection.login.dtsserver.f1
- sql13.swb.connecttodts.login.f1
- sql13.swb.connecttodtsserver.login.f1
helpviewer_keywords:
- Integration Services service, about Integration Services service
- SQL Server Integration Services service
- service [Integration Services],about Integration Services service
- service [Integration Services]
- SQL Server Integration Services, service
ms.assetid: 2c785b3b-4a0c-4df7-b5cd-23756dc87842
author: chugugrace
ms.author: chugu
ms.openlocfilehash: a665d78d180d87fe0d412b025bcce75d509285d4
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98171200"
---
# <a name="integration-services-service-ssis-service"></a>Integration Services 服務 (SSIS 服務)

[!INCLUDE[sqlserver-ssis](../../includes/applies-to-version/sqlserver-ssis.md)]


  本節中的主題討論 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 服務 (用於管理 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 封裝的 Windows 服務)。 若要建立、儲存及執行 Integration Services 封裝，則不需要這項服務。 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 支援 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 服務能與舊版 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)]回溯相容。  
  
 從 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 開始，[!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 會針對使用專案部署模型部署至 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 伺服器的專案，將物件、設定和操作資料儲存在 **SSISDB** 資料庫中。 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 伺服器是主控資料庫之 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Database Engine 的執行個體。 如需資料庫的詳細資訊，請參閱 [SSIS 目錄](../../integration-services/catalog/ssis-catalog.md)。 如需將專案部署至 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 伺服器的詳細資訊，請參閱[部署 Integration Services (SSIS) 專案和套件](../../integration-services/packages/deploy-integration-services-ssis-projects-and-packages.md)。  
  
## <a name="management-capabilities"></a>管理功能  
 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 服務是可用於管理 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 封裝的 Windows 服務。 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 服務只可以在 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)]中使用。  
  
 執行 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 服務會提供下列管理功能：  
  
-   啟動遠端和本機儲存的封裝  
  
-   停止遠端和本機正在執行的封裝  
  
-   監視遠端和本機正在執行的封裝  
  
-   匯入和匯出封裝  
  
-   管理封裝儲存體  
  
-   自訂儲存資料夾  
  
-   服務停止時停止正在執行的封裝  
  
-   檢視 Windows 事件記錄檔  
  
-   連接多個 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 伺服器  
  
## <a name="startup-type"></a>啟動類型
 當安裝 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 的 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 元件時，會安裝 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]服務。 根據預設， [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 服務會啟動，而且服務的啟動類型會設為自動。 該服務必須執行，才能監視儲存在「 [!INCLUDE[ssIS](../../includes/ssis-md.md)] 封裝存放區」中的封裝。 [!INCLUDE[ssIS](../../includes/ssis-md.md)] 封裝存放區可以是 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體中的 msdb 資料庫，也可以是檔案系統中的指定資料夾。  
  
 如果只想設計和執行 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 封裝，則無需執行 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 服務。 不過，若要使用 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)]列出和監視封裝，則需要該服務。  

## <a name="manage-the-service"></a>管理服務
  
 當安裝 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]元件時，也會安裝 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 服務。 根據預設， [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 服務會啟動，而且服務的啟動類型會設為自動。 不過，您也必須安裝 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 才能使用此服務來管理已儲存和執行中的 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 封裝。  
  
> [!NOTE]
> 若要直接連接到舊版 Integration Services 服務的執行個體，您必須使用 SQL Server Management Studio (SSMS) 版本以及在其上執行 Integration Services 服務的 SQL Server 版本。 例如，若要連接到 SQL Server 2016 執行個體上執行的舊版 Integration Services 服務，您必須使用針對 SQL Server 2016 所發行的 SSMS 版本。 [下載 SQL Server Management Studio (SSMS)](../../ssms/download-sql-server-management-studio-ssms.md).
>
>   在 SSMS [連接到伺服器] 對話方塊中，您無法輸入執行舊版 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 服務的伺服器名稱。 不過，若要管理儲存在遠端伺服器上的封裝，您不必連接到該遠端伺服器上的 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 服務執行個體。 而是要編輯 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 服務的組態檔，好讓 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 顯示儲存在遠端伺服器上的封裝。   
  
 您在一部電腦上只能安裝單一 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 服務的執行個體。 此服務並非特定 [!INCLUDE[ssDE](../../includes/ssde-md.md)]執行個體特有的。 您可以使用執行服務所在之電腦的名稱來連接至服務。  
  
 您可以使用下列其中一個 Microsoft Management Console (MMC) 嵌入式管理單元來管理 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 服務：SQL Server 組態管理員或服務。 您必須先確定服務已啟動，然後才能管理 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)]中的封裝。  
  
 根據預設， [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 服務設定為可管理 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 執行個體之 msdb 資料庫中的封裝，該執行個體與 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)]同時安裝。 如果 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 執行個體並未同時安裝， [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 服務會設定為可管理本機預設 [!INCLUDE[ssDE](../../includes/ssde-md.md)]執行個體之 msdb 資料庫中的封裝。 若要管理儲存在 [!INCLUDE[ssDE](../../includes/ssde-md.md)]具名或遠端執行個體中的封裝，或儲存在多個 [!INCLUDE[ssDE](../../includes/ssde-md.md)]執行個體中的封裝，您就必須修改此服務的組態檔。
  
 依預設， [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 服務會設定為在服務停止時停止執行中的封裝。 不過， [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 服務不會等待封裝停止，在 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 服務停止後，仍可能有部份封裝在執行中。  
  
 如果 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 服務已停止，可以使用 [ [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 匯入和匯出精靈]、 [!INCLUDE[ssIS](../../includes/ssis-md.md)] 設計師、執行封裝公用程式及 **dtexec** 命令提示字元公用程式 (dtexec.exe) 繼續執行封裝。 不過，您無法監視執行中的封裝。  
  
 根據預設， [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 服務會在 NETWORK SERVICE 帳戶內容中執行。 建議在 NETWORK SERVICE 帳戶等權限有限的帳戶下，執行 SQL Server Integration Services 服務。 在具有高權限的帳戶下執行 SQL Server Integration Services 服務代表潛在的安全性風險。 
  
 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 服務會寫入 Windows 事件記錄檔。 您可以在 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)]中檢視服務事件。 此外，您也可以使用「Windows 事件檢視器」來檢視服務事件。  
  
## <a name="set-the-properties-of-the-service"></a>設定服務的屬性
  
 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 服務會管理並監視 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)]中的封裝。 當您首次安裝 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 時，[!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 服務就會啟動，且此服務的啟動類型會設定為自動。  
  
 在您已經安裝 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 服務之後，就可以使用 [SQL Server 組態管理員] 或 [服務] MMC 嵌入式管理單元來設定服務的屬性。  
  
 若要設定服務的其他重要功能 (包括它儲存和管理封裝的位置)，您就必須修改服務的組態檔。
  
### <a name="to-set-properties-of-the-integration-services-service-by-using-sql-server-configuration-manager"></a>使用 SQL Server 組態管理員來設定 Integration Services 服務的屬性  
  
1.  在 **[開始]** 功能表上，依序指向 **[所有程式]** 、 **[Microsoft SQL Server]** 和 **[組態工具]** ，然後按一下 **[SQL Server 組態管理員]** 。  
  
2.  在 [SQL Server 組態管理員] 嵌入式管理單元中，尋找服務清單中的 [SQL Server Integration Services]，以滑鼠右鍵按一下 [SQL Server Integration Services]，然後按一下 [屬性]。  
  
3.  在 **[SQL Server Integration Services 屬性]** 對話方塊中，可以執行下列操作：  
  
    -   按一下 **[登入]** 索引標籤，以檢視登入資訊 (例如帳戶名稱)。  
  
    -   按一下 **[服務]** 索引標籤，即可檢視服務的相關資訊 (例如主機電腦的名稱)，並指定 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 服務的啟動模式。  
  
        > [!NOTE]  
        >  [進階] 索引標籤不包含 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 服務的資訊。  
  
4.  按一下 [確定]。  
  
5.  在 [檔案] 功能表上，按一下 [結束]，以關閉 [SQL Server 組態管理員] 嵌入式管理單元。  
  
### <a name="to-set-properties-of-the-integration-services-service-by-using-services"></a>使用服務來設定 Integration Services 服務的屬性  
  
1.  在 **[控制台]** 中，如果您使用「一般檢視」，請按一下 **[系統管理工具]**，如果您使用「類別檢視」，請按一下 **[效能及維護]** ，然後按一下 **[系統管理工具]**。  
  
2.  按一下 [服務]。  
  
3.  在 [服務] 嵌入式管理單元中，尋找服務清單中的 [SQL Server Integration Services]，以滑鼠右鍵按一下 [SQL Server Integration Services]，然後按一下 [屬性]。  
  
4.  在 **[SQL Server Integration Services 屬性]** 對話方塊中，您可以執行下列動作：  
  
    -   按一下 [General] \(一般\) 索引標籤。若要啟用服務，請選取手動或自動啟動類型。 若要停用服務，請在 **[啟動類型]** 方塊中選取 [停用]。 選取 [停用] 不會停止目前正在執行的服務。  
  
         如果已啟用服務，則可以按一下 **[停止]** 停止服務，或按一下 **[啟動]** 啟動服務。  
  
    -   按一下 **[登入]** 索引標籤，即可檢視或編輯登入資訊。  
  
    -   按一下 **[復原]** 索引標籤，以檢視服務失敗的預設電腦回應。 您可以修改這些選項，以配合您的環境。  
  
    -   按一下 **[相依性]** 索引標籤，以檢視相依性服務的清單。 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 服務不具有相依性。  
  
5.  按一下 [確定]。  
  
6.  或者，如果啟動類型是 [手動] 或 [自動]，則可以用滑鼠右鍵按一下 [SQL Server Integration Services]，然後按一下 [啟動]、[停止] 或 [重新啟動]。  
  
7.  在 [檔案] 功能表上，按一下 [結束]，以關閉 [服務] 嵌入式管理單元。  

## <a name="grant-permissions-to-the-service"></a>將權限授與服務
  在舊版 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]中，當您安裝 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 時，Users 群組中的所有使用者預設都能存取 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 服務。 當您安裝目前版本的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]時，使用者無法存取 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 服務。 因此，服務預設是安全的。 安裝 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 之後，管理員必須授與該服務的存取權。  
  
### <a name="to-grant-access-to-the-integration-services-service"></a>授與 Integration Services 服務的存取權  
  
1.  執行 Dcomcnfg.exe。 Dcomcnfg.exe 提供使用者介面，可供修改登錄中的某些設定。  
  
2.  在 [元件服務] 對話方塊中，展開 [元件服務] > [電腦] > [我的電腦] > [DCOM 組態] 節點。  
  
3.  以滑鼠右鍵按一下 **Microsoft SQL Server Integration Services 13.0**，然後按一下 [屬性] 。  
  
4.  在 **[安全性]** 索引標籤上，按一下 **[啟動和啟用權限]** 區域中的 **[編輯]** 。  
  
5.  加入使用者並指派適當的權限，然後按一下 [確定]。  
  
6.  重複步驟 4 - 5，以設定存取權限。  
  
7.  啟動 SQL Server Management Studio。  
  
8.  重新啟動 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 服務。  

### <a name="event-logged-when-permissions-are-missing"></a>遺失權限時所記錄的事件

如果 SQL Server Agent 的服務帳戶沒有 Integration Services DCOM **[啟動和啟用權限]**，當 SQL Server Agent 執行 SSIS 套件作業時，會將下列事件新增到系統事件記錄中：

```
Log Name: System
Source: **Microsoft-Windows-DistributedCOM**
Date: 1/9/2019 5:42:13 PM
Event ID: **10016**
Task Category: None
Level: Error
Keywords: Classic
User: NT SERVICE\SQLSERVERAGENT
Computer: testmachine
Description:
The application-specific permission settings do not grant Local Activation permission for the COM Server application with CLSID
{xxxxxxxxxxxxxxxxxxxxxxxxxxxxx}
and APPID
{xxxxxxxxxxxxxxxxxxxxxxxxxxxxx}
to the user NT SERVICE\SQLSERVERAGENT SID (S-1-5-80-344959196-2060754871-2302487193-2804545603-1466107430) from address LocalHost (Using LRPC) running in the application container Unavailable SID (Unavailable). This security permission can be modified using the Component Services administrative tool.
```

## <a name="configure-the-service"></a>設定服務
 
當您安裝 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)]時，安裝程序會針對 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 服務建立及安裝組態檔。 此組態檔包含下列設定：  
  
-   在服務停止時傳送停止指令給封裝。  
  
-   要在 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 的 [物件總管] 中顯示的 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 根資料夾為 [MSDB] 和 [檔案系統] 資料夾。  
  
-   檔案系統中，由 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 服務所管理的套件位於 %ProgramFiles%\Microsoft SQL Server\130\DTS\Packages 中。  
  
 此組態檔也會指定哪一個 msdb 資料庫包含 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 服務將會管理的封裝。 根據預設， [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 服務設定為可管理 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 執行個體之 msdb 資料庫中的封裝，該執行個體與 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)]同時安裝。 如果 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 執行個體並未同時安裝， [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 服務會設定為可管理本機預設 [!INCLUDE[ssDE](../../includes/ssde-md.md)]執行個體之 msdb 資料庫中的封裝。  
  
### <a name="default-configuration-file-example"></a>預設組態檔範例  
 下列範例會顯示可指定以下設定的預設組態檔：  
  
-   當 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 服務停止時，封裝會停止執行。  
  
-   [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 中封裝儲存體的根資料夾為 MSDB 和檔案系統。  
  
-   此服務會管理儲存於本機預設 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]執行個體之 msdb 資料庫內的封裝。  
  
-   此服務會管理儲存於檔案系統 [封裝] 資料夾內的封裝。  
  
 **預設組態檔的範例**  
  
```xml
\<?xml version="1.0" encoding="utf-8"?>  
\<DtsServiceConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">  
  <StopExecutingPackagesOnShutdown>true</StopExecutingPackagesOnShutdown>  
  <TopLevelFolders>  
    \<Folder xsi:type="SqlServerFolder">  
      <Name>MSDB</Name>  
      <ServerName>.</ServerName>  
    </Folder>  
    \<Folder xsi:type="FileSystemFolder">  
      <Name>File System</Name>  
      <StorePath>..\Packages</StorePath>  
    </Folder>  
  </TopLevelFolders>    
</DtsServiceConfiguration>  
```  
  
### <a name="modify-the-configuration-file"></a>修改設定檔  
 您可以修改組態檔，允許封裝在服務停止時繼續執行、在 [物件總管] 中顯示其他根資料夾，或在檔案系統中指定要由 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 服務管理的不同資料夾或其他資料夾。 例如，您可以建立屬於 **SqlServerFolder** 類型的其他根資料夾來管理其他 [!INCLUDE[ssDE](../../includes/ssde-md.md)]執行個體之 msdb 資料庫中的封裝。  
  
> [!NOTE]  
>  某些字元在資料夾名稱中是無效的。 資料夾名稱的有效字元是由 [!INCLUDE[dnprdnshort](../../includes/dnprdnshort-md.md)] 類別 **System.IO.Path** 和 [GetInvalidFilenameChars]  欄位所決定。 [GetInvalidFilenameChars] 欄位提供無法在傳遞給 **Path** 類別成員之路徑字串引數中指定的平台特定字元陣列。 有效的字元集可能會因檔案系統而不同。 無效的字元通常包括引號 (")、小於 (<) 字元和縱線 (|) 字元。  
  
 但是，若要管理儲存在 [!INCLUDE[ssDE](../../includes/ssde-md.md)]具名執行個體或遠端執行個體中的封裝，您就必須修改組態檔。 如果您未更新組態檔，就無法使用 **中的** 物件總管 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 來檢視儲存在具名執行個體或遠端執行個體之 msdb 資料庫中的封裝。 如果您嘗試使用 **[物件總管]** 來檢視這些封裝，您會收到下列錯誤訊息：  
  
 `Failed to retrieve data for this request. (Microsoft.SqlServer.SmoEnum)`  
  
 `The SQL Server specified in Integration Services service configuration is not present or is not available. This might occur when there is no default instance of SQL Server on the computer. For more information, see the topic "Configuring the Integration Services Service" in SQL Server 2008 Books Online.`  
  
 `Login Timeout Expired`  
  
 `An error has occurred while establishing a connection to the server. When connecting to SQL Server 2008, this failure may be caused by the fact that under the default settings SQL Server does not allow remote connections.`  
  
 `Named Pipes Provider: Could not open a connection to SQL Server [2]. (MsDtsSvr).`  
  
 若要為 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 服務修改此組態檔，您可使用文字編輯器。  
  
> [!IMPORTANT]  
>  在您修改服務組態檔後，必須重新啟動服務，才能使用更新的服務組態。  
  
### <a name="modified-configuration-file-example"></a>修改過的組態檔範例  
 下列範例會顯示 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)]的已修改組態檔。 此檔案適用於 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的具名執行個體，該執行個體稱為 `InstanceName` 且位在名為 `ServerName`的伺服器上。  
  
 **SQL Server 具名執行個體之已修改組態檔的範例**  
  
```xml
\<?xml version="1.0" encoding="utf-8"?>  
\<DtsServiceConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">  
  <StopExecutingPackagesOnShutdown>true</StopExecutingPackagesOnShutdown>  
  <TopLevelFolders>  
    \<Folder xsi:type="SqlServerFolder">  
      <Name>MSDB</Name>  
      <ServerName>ServerName\InstanceName</ServerName>  
    </Folder>  
    \<Folder xsi:type="FileSystemFolder">  
      <Name>File System</Name>  
      <StorePath>..\Packages</StorePath>  
    </Folder>  
  </TopLevelFolders>    
</DtsServiceConfiguration>  
```  
  
### <a name="modify-the-configuration-file-location"></a>修改設定檔位置  
 登錄機碼 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\130\SSIS\ServiceConfigFile** 指定 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 服務所使用之設定檔的位置和名稱。 此登錄機碼的預設值為 **C:\Program Files\Microsoft SQL Server\130\DTS\Binn\MsDtsSrvr.ini.xml**。 您可以更新此登錄機碼的值，以便針對組態檔使用不同的名稱和位置。 請注意，路徑中的版本號碼 (120 適用於 SQL Server [!INCLUDE[ssSQL14_md](../../includes/sssql14-md.md)]、130 適用於 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 等等) 將會根據 SQL Server 版本而不同。
  
> [!CAUTION]  
>  不當地編輯登錄可能會造成嚴重問題，並導致您必須重新安裝作業系統。 [!INCLUDE[msCoName](../../includes/msconame-md.md)] 不保證能夠解決不當編輯登錄所產生的問題。 在編輯登錄之前，請先備份重要資料。 如需有關如何備份、還原及編輯登錄的詳細資訊，請參閱 [!INCLUDE[msCoName](../../includes/msconame-md.md)] 知識庫文件： [Microsoft Windows 登錄說明](https://support.microsoft.com/kb/256986)。  
  
 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 服務啟動時會載入組態檔。 登錄項目如有任何變更，就必須重新啟動服務。  

## <a name="connect-to-the-local-service"></a>連線至區域服務
  連接至 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 服務之前，管理員必須授與您該服務的存取權。 
  
### <a name="to-connect-to-the-integration-services-service"></a>連線至 Integration Services 服務  
  
1.  開啟 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)]。  
  
2.  在 **[檢視]** 功能表上，按一下 **[物件總管]** 。  
  
3.  在 [物件總管] 工具列上，按一下 **[連接]**，然後按一下 **[Integration Services]**。  
  
4.  在 **[連接到伺服器]** 對話方塊中，提供伺服器名稱。 您可以使用句號 (.)、(local) 或 **localhost** 表示本機伺服器。  
  
5.  按一下 [ **連接**]。  

## <a name="connect-to-a-remote-ssis-server"></a>連線至遠端 SSIS 伺服器
  
 從 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 或其他管理應用程式連接到遠端伺服器上的 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 執行個體時，應用程式的使用者必須具備一組特定的伺服器權限。  
  
> [!IMPORTANT]
> 若要直接連接到舊版 Integration Services 服務的執行個體，您必須使用 SQL Server Management Studio (SSMS) 版本以及在其上執行 Integration Services 服務的 SQL Server 版本。 例如，若要連接到 SQL Server 2016 執行個體上執行的舊版 Integration Services 服務，您必須使用針對 SQL Server 2016 所發行的 SSMS 版本。 [下載 SQL Server Management Studio (SSMS)](../../ssms/download-sql-server-management-studio-ssms.md).
>
>  若要管理儲存在遠端伺服器上的封裝，您不必連接到該遠端伺服器上的 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 服務執行個體， 而是要編輯 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 服務的組態檔，好讓 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 顯示儲存在遠端伺服器上的封裝。
  
### <a name="connecting-to-integration-services-on-a-remote-server"></a>連接到遠端伺服器上的 Integration Services  
  
#### <a name="to-connect-to-integration-services-on-a-remote-server"></a>連接到遠端伺服器上的 Integration Services  
  
1.  開啟 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)]。  
  
2.  依序選取 [檔案]、[連接物件總管]，以顯示 [連接到伺服器] 對話方塊。  
  
3.  選取 [伺服器類型] 清單中的 [Integration Services]。  
  
4.  在 [伺服器名稱] 文字方塊中鍵入 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 伺服器的名稱。  
  
    > [!NOTE]  
    >  [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 服務不是執行個體特定的。 您可以使用 Integration Services 服務執行所在的電腦名稱來連接此服務。  
  
5.  按一下 [ **連接**]。  
  
> [!NOTE]  
>  [瀏覽伺服器] 對話方塊不會顯示 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 的遠端執行個體。 此外，[連接到伺服器] 對話方塊的 [連接選項] 索引標籤 (按一下 [選項] 按鈕即可顯示) 上的可用選項不適用於 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 連接。  
  
### <a name="eliminating-the-access-is-denied-error"></a>排除「存取遭到拒絕」的錯誤  
 如果不具備足夠權限的使用者試圖連接到遠端伺服器上的 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 執行個體，伺服器便會回應「存取遭到拒絕」的錯誤訊息。 只要確保使用者具有 DCOM 權限，就可以避免產生這個錯誤訊息。  
  
#### <a name="to-configure-rights-for-remote-users-on-windows-server-2003-or-windows-xp"></a>設定 Windows Server 2003 或 Windows XP 遠端使用者的權限  
  
1.  如果使用者不是本機 Administrators 群組的成員，請將使用者加入「分散式 COM 使用者」群組。 您可以從 [系統管理工具] 功能表中的 [電腦管理] MMC 嵌入式管理單元進行這項作業。  
  
2.  開啟 [控制台]，依序按兩下 [系統管理工具]、[元件服務] 啟動 [元件服務] MMC 嵌入式管理單元。  
  
3.  展開控制台左邊窗格中的 [元件服務] 節點。 依序展開 [電腦] 節點和 [我的電腦]，然後按一下 [DCOM 設定] 節點。  
  
4.  選取 [DCOM 設定] 節點，然後選取可以設定之應用程式清單中的 SQL Server Integration Services 11.0。  
  
5.  以滑鼠右鍵按一下 SQL Server Integration Services 11.0，然後選取 [內容]。  
  
6.  在 [SQL Server Integration Services 11.0 內容] 對話方塊中，選取 [安全性] 索引標籤。  
  
7.  選取 [啟動和啟用權限] 底下的 [自訂]，然後按一下 [編輯] 開啟 [啟動權限] 對話方塊。  
  
8.  在 [啟動權限] 對話方塊中，新增或刪除使用者，並指派適當權限給適當的使用者與群組。 可用的權限有本機啟動、遠端啟動、本機啟用以及遠端啟用。 啟動權限可授與或拒絕啟動及停止服務的權限；啟用權限則可以授與或拒絕連接到服務的權限。  
  
9. 按一下 [確定] 關閉對話方塊。  
  
10. 在 [存取權限] 底下，重複步驟 7 和 8，為適當的使用者和群組指派適當的權限。  
  
11. 關閉 MMC 嵌入式管理單元。  
  
12. 重新啟動 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 服務。  
  
#### <a name="to-configure-rights-for-remote-users-on-windows-2000-with-the-latest-service-packs"></a>設定具有最新 Service Pack 版本的 Windows 2000 遠端使用者權限  
  
1.  在命令提示字元執行 **dcomcnfg.exe** 。  
  
2.  在 [分散式 COM 組態內容] 對話方塊的 [應用程式] 頁面上，選取 SQL Server Integration Services 11.0，然後按一下 [內容]。  
  
3.  選取 [安全性] 頁面  。  
  
4.  使用兩個分開的對話方塊設定 [存取權限] 與 [啟動權限]。 您無法區分遠端與本機存取，存取權限包含了本機與遠端存取，而啟動權限則包含了本機與遠端啟動。  
  
5.  關閉對話方塊和 **dcomcnfg.exe**。  
  
6.  重新啟動 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 服務。  
  
### <a name="connecting-by-using-a-local-account"></a>使用本機帳戶進行連接

如果您是在用戶端電腦上使用本機 Windows 帳戶工作，那麼只有當遠端電腦上存在和本機帳戶相同名稱與密碼以及適當權限的帳戶，您才能連接到遠端電腦上的 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 服務。  
  
### <a name="ssis-windows-service-doesnt-support-delegation"></a>SSIS Windows 服務不支援委派

SSIS 不支援認證委派 (有時候稱為「雙躍點」)。 在這種情況中，您是在用戶端電腦上工作，SSIS 是安裝在第二部電腦上，而 SQL Server 則是安裝在第三部電腦上。 雖然 SSIS 成功地將您的認證從用戶端電腦傳送到第二部電腦 (SSIS 執行所在)，SSIS 卻無法將您的認證從第二部電腦委派給第三部電腦 (SQL Server 執行所在)。

## <a name="configure-the-firewall"></a>設定防火牆
  
 Windows 防火牆系統有助於防止未經授權的使用者透過網路連線來存取電腦資源。 若要透過此防火牆存取 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] ，必須設定防火牆來啟用存取。  
  
> [!IMPORTANT]  
>  若要管理儲存在遠端伺服器上的封裝，您不必連接到該遠端伺服器上的 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 服務執行個體， 而是要編輯 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 服務的組態檔，好讓 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 顯示儲存在遠端伺服器上的封裝。
  
 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 服務使用 DCOM 通訊協定。
  
 有許多防火牆系統可用。 如果您正在執行 Windows 防火牆以外的防火牆，請參閱防火牆文件以獲取您正在使用之系統所特定的資訊。  
  
 如果防火牆支援應用程式層級篩選，您可以使用 Windows 提供的使用者介面，指定允許穿越防火牆的例外狀況，例如程式和服務。 否則，您必須設定 DCOM 使用有限的一組 TCP 通訊埠。 上一段落提供的 Microsoft 網站連結包含有關如何指定所要使用之 TCP 通訊埠的資訊。  
  
 Integration Services 服務使用通訊埠 135，且無法變更。 您必須開啟 TCP 通訊埠 135 供服務控制管理員 (SCM) 存取。 SCM 會執行工作，例如啟動和停止 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 服務，以及將控制要求傳送至執行中服務。  
  
 下節中的資訊是 Windows 防火牆特定的資訊。 您可以在命令提示字元處執行命令，或在「Windows 防火牆」對話方塊中設定屬性，來設定 Windows 防火牆系統。  
  
 如需預設 Windows 防火牆設定的詳細資訊以及影響 Database Engine、Analysis Services、Reporting Services 和 Integration Services 之 TCP 通訊埠的描述，請參閱 [設定 Windows 防火牆以允許 SQL Server 存取](../../sql-server/install/configure-the-windows-firewall-to-allow-sql-server-access.md)。  
  
### <a name="configuring-a-windows-firewall"></a>設定 Windows 防火牆  
 您可以使用下列命令來開啟 TCP 通訊埠 135，將 MsDtsSrvr.exe 加入至例外狀況清單，並指定防火牆的不封鎖範圍。  
  
#### <a name="to-configure-a-windows-firewall-using-the-command-prompt-window"></a>若要使用命令提示視窗來設定 Windows 防火牆  
  
1.  執行以下命令：

    ```dos
    netsh firewall add portopening protocol=TCP port=135 name="RPC (TCP/135)" mode=ENABLE scope=SUBNET
    ```
  
2.  執行以下命令：

    ```dos
    netsh firewall add allowedprogram program="%ProgramFiles%\Microsoft SQL Server\100\DTS\Binn\MsDtsSrvr.exe" name="SSIS Service" scope=SUBNET
    ```
  
    > [!NOTE]  
    >  若要為所有電腦以及網際網路上的電腦開啟防火牆，請以 scope=ALL 取代 scope=SUBNET。  
  
 下列程序描述如何使用 Windows 使用者介面來開啟 TCP 通訊埠 135，將 MsDtsSrvr.exe 加入例外狀況清單中，並指定防火牆的不封鎖範圍。  
  
#### <a name="to-configure-a-firewall-using-the-windows-firewall-dialog-box"></a>使用 Windows 防火牆對話方塊來設定防火牆  
  
1.  在 [控制台] 中按兩下 [Windows 防火牆]。  
  
2.  在 **[Windows 防火牆]** 對話方塊中，按一下 **[例外狀況]** 索引標籤，然後再按一下 **[加入程式]**。  
  
3.  在 [新增程式] 對話方塊中，按一下 [瀏覽] 巡覽至 Program Files\Microsoft SQL Server\100\DTS\Binn 資料夾，然後按一下 MsDtsSrvr.exe，再按一下 [開啟]。 按一下 **[確定]** 以關閉 **[新增程式]** 對話方塊。  
  
4.  在 **[例外狀況]** 索引標籤上，按一下 **[新增連接埠]**。  
  
5.  在 [新增連接埠] 對話方塊的 [名稱] 方塊中，輸入 **RPC(TCP/135)** 或其他描述性名稱，在 [連接埠編號] 方塊中輸入 **135**，然後選取 [TCP]。  
  
    > [!IMPORTANT]  
    >  [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 服務會一直使用通訊埠 135。 您無法指定不同的通訊埠。  
  
6.  在 **[新增連接埠]** 對話方塊中，可以選擇性地按一下 **[變更範圍]** 以修改預設範圍。  
  
7.  在 [變更範圍] 對話方塊中，選取 [只有我的網路 (子網路)] 或輸入自訂清單，然後按一下 [確定]。  
  
8.  若要關閉 **[新增連接埠]** 對話方塊，請按一下 **[確定]**。  
  
9. 若要關閉 **[Windows 防火牆]** 對話方塊，請按一下 **[確定]**。  
  
    > [!NOTE]  
    >  為了設定 Windows 防火牆，這個程序會使用 [控制台] 中的 [Windows 防火牆] 項目。 **[Windows 防火牆]** 項目只會針對目前網路位置設定檔來設定防火牆。 不過，若要設定 Windows 防火牆，也可以使用 **netsh** 命令列工具或是名為「具有進階安全性的 Windows 防火牆」的 [!INCLUDE[msCoName](../../includes/msconame-md.md)] Management Console (MMC) 嵌入式管理單元。 如需這些工具的詳細資訊，請參閱 [設定 Windows 防火牆以允許 SQL Server 存取](../../sql-server/install/configure-the-windows-firewall-to-allow-sql-server-access.md)。  
