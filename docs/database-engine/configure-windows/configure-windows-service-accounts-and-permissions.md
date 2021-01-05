---
title: 設定 Windows 服務帳戶與權限 | Microsoft Docs
description: 熟悉用來在 SQL Server 中啟動及執行服務的服務帳戶。 請參閱如何設定這些帳戶與指派適當的權限。
ms.custom: contperf-fy20q4
ms.date: 03/17/2020
ms.prod: sql
ms.prod_service: high-availability
ms.reviewer: ''
ms.technology: configuration
ms.topic: reference
helpviewer_keywords:
- startup service states [SQL Server]
- Setup [SQL Server], user accounts
- Windows permissions [SQL Server]
- modifying user accounts
- default accounts
- domains [SQL Server], user accounts
- startup accounts [SQL Server]
- system accounts [SQL Server]
- services [SQL Server], permissions
- ACL (access control list)
- local system accounts [SQL Server]
- instance-aware services [SQL Server]
- permissions [SQL Server], services
- SQL Server Agent service, user accounts
- Windows NT permissions [SQL Server]
- user accounts [SQL Server]
- identifying instance-unaware services [SQL Server]
- installing SQL Server, user accounts
- disabled startup state [SQL Server]
- user accounts [SQL Server], users
- Local Service account [SQL Server]
- SQL Server Installation Wizard
- instance-unaware services [SQL Server]
- services [SQL Server], configuring at installation
- Windows accounts [SQL Server]
- SQL Server services, user accounts
- user accounts [SQL Server], services
- MSSQLServer
- identifying instance-aware services [SQL Server]
- services [SQL Server], accounts
- access control lists
- optional accounts [SQL Server]
- service accounts [SQL Server]
- accounts [SQL Server], services
- built-in system accounts [SQL Server]
- automatic startup state
- domains [SQL Server]
- manual startup state [SQL Server]
- accounts [SQL Server], user
ms.assetid: 309b9dac-0b3a-4617-85ef-c4519ce9d014
author: markingmyname
ms.author: maghan
ms.openlocfilehash: da2fc97ef8e5637dfff48d2070cb652b40ae4d28
ms.sourcegitcommit: cb8e2ce950d8199470ff1259c9430f0560f0dc1d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2021
ms.locfileid: "97878909"
---
# <a name="configure-windows-service-accounts-and-permissions"></a>設定 Windows 服務帳戶與權限

 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中的每項服務代表一個或一組處理序，用以管理 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 作業對 Windows 的驗證。 本主題描述此 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]版本的預設服務組態，以及可以在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝期間和安裝完成後設定的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 服務組態選項。 本主題可協助進階使用者了解服務帳戶的詳細資料。

 您可以使用 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 組態管理員來設定大多數服務及其屬性。 以下是 Windows 安裝在 C 磁碟機時，四個最新版本的路徑。

|SQL Server 版本|Path|
|-|-|
|SQL Server 2019|C:\Windows\SysWOW64\SQLServerManager15.msc|
|SQL Server 2017|C:\Windows\SysWOW64\SQLServerManager14.msc|
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 2016|C:\Windows\SysWOW64\SQLServerManager13.msc|
|[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)]|C:\Windows\SysWOW64\SQLServerManager12.msc|
|[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)]|C:\Windows\SysWOW64\SQLServerManager11.msc|

## <a name="services-installed-by-ssnoversion"></a><a name="Service_Details"></a>[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝的服務

依據您決定安裝的元件而定， [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式會安裝下列服務：

- **[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Database Services** - [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 關聯式 [!INCLUDE[ssDE](../../includes/ssde-md.md)]的服務。 可執行檔為 \<MSSQLPATH>\MSSQL\Binn\sqlservr.exe。
- **[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent** - 執行作業、監視 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]、引發警示，以及將某些管理工作自動化。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent 服務存在，但是在 [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)]執行個體上停用。 可執行檔為 \<MSSQLPATH>\MSSQL\Binn\sqlagent.exe。
- **[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]** - 提供商業智慧應用程式的線上分析處理 (OLAP) 和資料採礦功能。 可執行檔為 \<MSSQLPATH>\OLAP\Bin\msmdsrv.exe。
- **[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]** - 管理、執行、建立、排程和傳遞報表。 可執行檔為 \<MSSQLPATH>\Reporting Services\ReportServer\Bin\ReportingServicesService.exe。
- **[!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)]** - 提供 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 套件儲存體和執行的管理支援。 可執行檔為 \<MSSQLPATH>\130\DTS\Binn\MsDtsSrvr.exe

   [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 可能包含擴充部署的其他服務。 如需詳細資訊，請參閱[逐步解說：設定 Integration Services (SSIS) 擴充](../../integration-services/scale-out/walkthrough-set-up-integration-services-scale-out.md)。

- **[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Browser** - 提供 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 連接資訊給用戶端電腦的名稱解析服務。 可執行檔路徑為 c:\Program Files (x86)\Microsoft SQL Server\90\Shared\sqlbrowser.exe
- **全文檢索搜尋** - 可以快速地在結構化和半結構化資料的內容與屬性上建立全文檢索索引，以針對 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]提供文件篩選和斷詞。
- **SQL 寫入器** - 允許備份與還原應用程式在磁碟區陰影複製服務 (VSS) 架構中操作。
- **[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Distributed Replay Controller** - 跨多部 Distributed Replay Client 電腦提供重新執行追蹤 Orchestration。
- **[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Distributed Replay Client** - 一或多部搭配 Distributed Replay controller 運作的分散式重新執行用戶端電腦，以針對 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]執行個體模擬並行工作負載。
- **[!INCLUDE[rsql_launchpad](../../includes/rsql-launchpad-md.md)]** - 裝載 Microsoft 所提供外部可執行檔的受信任服務，例如隨 R 服務或機器學習服務一併安裝的 R 或 Python 執行階段。 附屬項目處理序可由 Launchpad 處理序啟動，但會根據個別執行個體的組態來進行資源管理。 Launchpad 服務在其專屬的使用者帳戶下執行，而已註冊執行階段之每項特定服務的附屬項目處理序則會繼承 Launchpad 的使用者帳戶。 附屬項目處理序會視需求在執行時加以建立及終結。

  如果在同時作為網域控制站的電腦上安裝 SQL Server，Launchpad 就無法建立所使用的帳戶。 因此，在網域控制站上安裝 R Services (資料庫內) 或 Machine Learning 服務 (資料庫內) 將會失敗。

- **SQL Server PolyBase 引擎** - 提供外部資料來源的分散式查詢功能。
- **SQL Server PolyBase 資料移動服務** - 可在 SQL Server 與外部資料來源之間，以及在 PolyBase 向外延展群組的 SQL 節點之間移動資料。

## <a name="service-properties-and-configuration"></a><a name="Serv_Prop"></a> 服務屬性和組態

用來啟動並執行 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的啟動帳戶可以是 [網域使用者帳戶](#Domain_User)、 [本機使用者帳戶](#Local_User)、 [受管理的服務帳戶](#MSA)、 [虛擬帳戶](#VA_Desc)或 [內建的系統帳戶](#Local_Service)。 若要啟動並執行， [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中的每個服務都必須在安裝期間設定啟動帳戶。

本節描述可設定用來啟動 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 服務的帳戶、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式使用的預設值、每個服務 SID 的概念、啟動選項，以及設定防火牆。

- [預設服務帳戶](#Default_Accts)
- [自動啟動](#Auto_Start)
- [設定服務啟動類型](#Configure_services)
- [防火牆通訊埠](#Firewall)

### <a name="default-service-accounts"></a><a name="Default_Accts"></a> 預設服務帳戶

下表列出安裝所有元件時，安裝程式所使用的預設服務帳戶。 列出的預設帳戶就是建議的帳戶 (除非另有附註)。

**獨立伺服器或網域控制站**

|元件|[!INCLUDE[nextref_longhorn](../../includes/nextref-longhorn-md.md)]|Windows 7 及 [!INCLUDE[nextref_longhorn](../../includes/nextref-longhorn-md.md)] R2 和更新版本|
|---------------|------------------------------------|----------------------------------------------------------------|
|[!INCLUDE[ssDE](../../includes/ssde-md.md)]|[NETWORK SERVICE](#Network_Service)|[虛擬帳戶](#VA_Desc)*|
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent|[NETWORK SERVICE](#Network_Service)|[虛擬帳戶](#VA_Desc)*|
|[!INCLUDE[ssAS](../../includes/ssas-md.md)]|[NETWORK SERVICE](#Network_Service)|[虛擬帳戶](#VA_Desc)\*\*\*|
|[!INCLUDE[ssIS](../../includes/ssis-md.md)]|[NETWORK SERVICE](#Network_Service)|[虛擬帳戶](#VA_Desc)\*|
|[!INCLUDE[ssRS](../../includes/ssrs.md)]|[NETWORK SERVICE](#Network_Service)|[虛擬帳戶](#VA_Desc)\*|
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Distributed Replay Controller|[NETWORK SERVICE](#Network_Service)|[虛擬帳戶](#VA_Desc)\*|
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Distributed Replay Client|[NETWORK SERVICE](#Network_Service)|[虛擬帳戶](#VA_Desc)\*|
|FD 啟動器 (全文檢索搜尋)|[LOCAL SERVICE](#Local_Service)|[虛擬帳戶](#VA_Desc)|
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Browser|[LOCAL SERVICE](#Local_Service)|[LOCAL SERVICE](#Local_Service)|
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] VSS Writer|[LOCAL SYSTEM](#Local_System)|[LOCAL SYSTEM](#Local_System)|
|[!INCLUDE[rsql_extensions](../../includes/rsql-extensions-md.md)]|NTSERVICE\MSSQLLaunchpad|NTSERVICE\MSSQLLaunchpad|
|PolyBase 引擎 |[NETWORK SERVICE](#Network_Service) |[NETWORK SERVICE](#Network_Service)|
|PolyBase 資料移動服務 |[NETWORK SERVICE](#Network_Service) |[NETWORK SERVICE](#Network_Service)|

\*需要 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 電腦外部的資源時，[!INCLUDE[msCoName](../../includes/msconame-md.md)] 建議您使用受管理的服務帳戶 (MSA)，並且為帳戶設定所需的最低權限。
\*\*安裝在網域控制站上時，不支援作為服務帳戶的虛擬帳戶。

**SQL Server 容錯移轉叢集執行個體**

|元件|[!INCLUDE[nextref_longhorn](../../includes/nextref-longhorn-md.md)]|[!INCLUDE[nextref_longhorn](../../includes/nextref-longhorn-md.md)] R2|
|---------------|------------------------------------|---------------------------------------|
|[!INCLUDE[ssDE](../../includes/ssde-md.md)]|無。 提供 [網域使用者](#Domain_User) 帳戶。|提供 [網域使用者](#Domain_User) 帳戶。|
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent|無。 提供 [網域使用者](#Domain_User) 帳戶。|提供 [網域使用者](#Domain_User) 帳戶。|
|[!INCLUDE[ssAS](../../includes/ssas-md.md)]|無。 提供 [網域使用者](#Domain_User) 帳戶。|提供 [網域使用者](#Domain_User) 帳戶。|
|[!INCLUDE[ssIS](../../includes/ssis-md.md)]|[NETWORK SERVICE](#Network_Service)|[虛擬帳戶](#VA_Desc)|
|[!INCLUDE[ssRS](../../includes/ssrs.md)]|[NETWORK SERVICE](#Network_Service)|[虛擬帳戶](#VA_Desc)|
|FD 啟動器 (全文檢索搜尋)|[LOCAL SERVICE](#Local_Service)|[虛擬帳戶](#VA_Desc)|
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Browser|[LOCAL SERVICE](#Local_Service)|[LOCAL SERVICE](#Local_Service)|
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] VSS Writer|[LOCAL SYSTEM](#Local_System)|[LOCAL SYSTEM](#Local_System)|

#### <a name="changing-account-properties"></a><a name="Changing_Accounts"></a> 變更帳戶內容

> [!IMPORTANT]
>
> - 請一律利用 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 工具 (如 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 組態管理員) 來變更 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 或 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent 服務所用的帳戶，或變更帳戶的密碼。 除了變更帳戶名稱之外， [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 組態管理員也會執行其他組態，例如，更新 Windows 本機安全存放區，它會保護 [!INCLUDE[ssDE](../../includes/ssde-md.md)]的服務主要金鑰。 其他工具 (例如 Windows 服務控制管理員) 可以變更帳戶名稱，但無法變更所有必要的設定。
> - 對於您在 SharePoint 伺服陣列中部署的 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] 執行個體，請一律使用 SharePoint 管理中心來變更 [!INCLUDE[ssGeminiMTS](../../includes/ssgeminimts-md.md)] 應用程式和 [!INCLUDE[ssGeminiSrv](../../includes/ssgeminisrv-md.md)]的伺服器帳戶。 當您使用管理中心時，相關聯的設定和權限都會更新為使用新的帳戶資訊。
> - 若要變更 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] 選項，請使用 Reporting Services 組態工具。

### <a name="managed-service-accounts-group-managed-service-accounts-and-virtual-accounts"></a><a name="New_Accounts"></a> 受管理的服務帳戶、群組受管理的服務帳戶和虛擬帳戶

受管理的服務帳戶、群組受管理的服務帳戶和虛擬帳戶的設計，在於提供重要的應用程式 (例如 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ) 並與自己的帳戶隔離，同時不需系統管理員手動管理服務主要名稱 (SPN) 和這些帳戶的認證。 這些帳戶可讓長期管理服務帳戶使用者、密碼和 SPN 的工作更輕鬆。

- <a name="MSA"></a> **受控服務帳戶**

  受管理的服務帳戶 (MSA) 是一種網域帳戶，由網域控制站建立和管理。 這個帳戶會指派給執行服務的單一成員電腦使用。 密碼是由網域控制站自動管理。 您無法使用 MSA 登入電腦，但是電腦可以使用 MSA 啟動 Windows 服務。 MSA 能夠在提供讀取和寫入 servicePrincipalName 權限時，於 Active Directory 內註冊服務主體名稱 (SPN)。 MSA 的命名包含 **$** 後置詞，例如 **DOMAIN\ACCOUNTNAME$** 。 指定 MSA 時，讓密碼空白。 由於 MSA 是指派給單一電腦，而不能用於 Windows 叢集的不同節點上。

  > [!NOTE]
  > 網域系統管理員必須先在 Active Directory 中建立 MSA， [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式才能將其用於 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 服務。

- <a name="GMSA"></a> **分組受控服務帳戶**

  群組受管理的服務帳戶 (gMSA) 是適用於多部伺服器的 MSA。 Windows 會為伺服器群組上執行的服務管理服務。 Active Directory 會自動更新群組受管理的服務帳戶密碼，不需要重新啟動服務。 您可以設定 SQL Server 服務，以使用群組受管理的服務帳戶主體。 自 SQL Server 2014 起，SQL Server 已可支援獨立執行個體的群組受控服務帳戶，自 SQL Server 2016 起的版本，則支援容錯移轉叢集執行個體與可用性群組。

  若要針對 SQL Server 2014 或更新版本使用 gMSA，作業系統必須是 Windows Server 2012 R2 或更新版本。 Windows Server 2012 R2 的伺服器需要套用 [KB 2998082](https://support.microsoft.com/kb/2998082) ，以便服務可以在變更密碼之後立即登入，不會中斷。

  如需詳細資訊，請參閱[群組受控服務帳戶](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831782(v=ws.11))

  > [!NOTE]
  > 網域系統管理員必須先在 Active Directory 中建立 gMSA，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式才能將其用於 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 服務。

- <a name="VA_Desc"></a>**Virtual Accounts**

  虛擬帳戶 (從 Windows Server 2008 R2 和 Windows 7 開始) 為「受管理的本機帳戶」  ，並會提供下列功能來簡化服務管理工作。 虛擬帳戶是自動管理的，而且虛擬帳戶可以在網域環境中存取網路。 如果在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的安裝期間針對服務帳戶使用預設值，則會使用以執行個體名稱作為服務名稱的虛擬帳戶，其格式為 **NT SERVICE\\** _\<SERVICENAME>_ 。 以虛擬帳戶執行的服務，會利用電腦帳戶的認證存取網路資源，其格式為 <網域名稱> __\\__ <電腦名稱> __$__ 。 指定虛擬帳戶啟動 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 時，請讓密碼空白。 如果虛擬帳戶無法註冊服務主要名稱 (SPN)，請手動註冊 SPN。 如需手動註冊 SPN 的詳細資訊，請參閱 [手動 SPN 註冊](register-a-service-principal-name-for-kerberos-connections.md)。

  > [!NOTE]
  > 虛擬帳戶無法用於 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 容錯移轉叢集執行個體，因為虛擬帳戶在叢集的每一個節點上不會有相同的 SID。

  下表列出虛擬帳戶名稱的範例。

  |服務|虛擬帳戶名稱|
  |-------------|--------------------------|
  |[!INCLUDE[ssDE](../../includes/ssde-md.md)] 服務的預設執行個體|**NT SERVICE\MSSQLSERVER**|
  |名為 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 之 **之** 服務的具名執行個體|**NT SERVICE\MSSQL$PAYROLL**|
  |[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 預設執行個體上的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]|**NT SERVICE\SQLSERVERAGENT**|
  |名為[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體上的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 之 **執行個體上的** Agent 服務|**NT SERVICE\SQLAGENT$PAYROLL**|

如需受管理的服務帳戶和虛擬帳戶的詳細資訊，請參閱 [服務帳戶的逐步指南](https://technet.microsoft.com/library/dd548356\(WS.10\).aspx)的 **受管理的服務帳戶和虛擬帳戶概念** 一節，以及 [受管理的服務帳戶常見問題集 (FAQ)](https://technet.microsoft.com/library/ff641729\(WS.10\).aspx)。

**安全性注意事項：** [!INCLUDE[ssNoteLowRights](../../includes/ssnotelowrights-md.md)] 請在允許情況下使用 [MSA](#MSA)、[gMSA](#GMSA) 或 [虛擬帳戶](#VA_Desc)。 如果無法使用 MSA、gMSA 或虛擬帳戶，請使用特定低權限的使用者帳戶或網域帳戶，而不要使用 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 服務的共用帳戶。 針對不同的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 服務使用個別的帳戶。 請勿將其他權限授與 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 服務帳戶或服務群組。 權限將透過群組成員資格授與，或直接授與服務 SID (如果支援服務 SID)。

### <a name="automatic-startup"></a><a name="Auto_Start"></a> 自動啟動

除了具有使用者帳戶之外，每項服務有三個可能的啟動狀態供使用者控制：

- **已停用** ：已安裝服務但目前未執行。
- **手動** ：已安裝此服務，但只有另一項服務或應用程式需要它的功能時才會啟動。
- **自動** ：服務會由作業系統自動啟動。

啟動狀態會在安裝過程中選取。 安裝具名執行個體時， [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Browser 服務應該設定為自動啟動。

### <a name="configuring-services-during-unattended-installation"></a><a name="Configure_services"></a> 在自動安裝期間設定服務

下表顯示可以在安裝期間設定的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 服務。 若為自動安裝，您可以在組態檔案中或從命令提示字元使用這些參數。

|SQL Server 服務名稱|自動安裝的參數\*|
|-----------------------------|---------------------------------------------|
|MSSQLSERVER|SQLSVCACCOUNT、SQLSVCPASSWORD、SQLSVCSTARTUPTYPE|
|SQLServerAgent\*\*|AGTSVCACCOUNT、AGTSVCPASSWORD、AGTSVCSTARTUPTYPE|
|MSSQLServerOLAPService|ASSVCACCOUNT、ASSVCPASSWORD、ASSVCSTARTUPTYPE|
|ReportServer|RSSVCACCOUNT、RSSVCPASSWORD、RSSVCSTARTUPTYPE|
|[!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)]|ISSVCACCOUNT、ISSVCPASSWORD、ISSVCSTARTUPTYPE|
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Distributed Replay Controller|DRU_CTLR、CTLRSVCACCOUNT、CTLRSVCPASSWORD、CTLRSTARTUPTYPE、CTLRUSERS|
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Distributed Replay Client|DRU_CLT、CLTSVCACCOUNT、CLTSVCPASSWORD、CLTSTARTUPTYPE、CLTCTLRNAME、CLTWORKINGDIR、CLTRESULTDIR|
|R 服務或機器學習服務|EXTSVCACCOUNT、EXTSVCPASSWORD、ADVANCEDANALYTICS\*\*\*|
|PolyBase 引擎| PBENGSVCACCOUNT、PBENGSVCPASSWORD、PBENGSVCSTARTUPTYPE、PBDMSSVCACCOUNT、PBDMSSVCPASSWORD、PBDMSSVCSTARTUPTYPE、PBSCALEOUT、PBPORTRANGE。

\*需自動安裝的詳細資訊和範例語法，請參閱[從命令提示字元安裝 SQL Server 2016](../install-windows/install-sql-server-from-the-command-prompt.md)。

\*\*[!INCLUDE[ssExpress](../../includes/ssexpress-md.md)] 與 [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)] with Advanced Services 的執行個體會停用 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 代理程式服務。

\*\*\*目前不支援單獨透過交換器設定 Launchpad 的帳戶。 請使用 SQL Server 設定管理員來變更帳戶及其他服務設定。

### <a name="firewall-port"></a><a name="Firewall"></a> 防火牆通訊埠

大部分情況下，在初始安裝時， [!INCLUDE[ssDE](../../includes/ssde-md.md)] 可以藉由像是與 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 安裝在同一部電腦上的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]這類工具進行連接。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式不會在 Windows 防火牆中開啟通訊埠。 除非將 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 設定為在 TCP 通訊埠上接聽，而且已在 Windows 防火牆中開啟適當的通訊埠進行連接，否則無法從其他電腦連接。 如需詳細資訊，請參閱 [設定 Windows 防火牆以允許 SQL Server 存取](../../sql-server/install/configure-the-windows-firewall-to-allow-sql-server-access.md)。

## <a name="service-permissions"></a><a name="Serv_Perm"></a> 服務權限

本節描述 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式針對 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 服務的個別服務 SID 設定的權限。

- [服務組態和存取控制](#Serv_SID)
- [Windows 權限和權利](#Windows)
- [授與 SQL Server 個別服務 SID 或 SQL Server 本機 Windows 群組的檔案系統權限](#Reviewing_ACLs)
- [授與其他 Windows 使用者帳戶或群組的檔案系統權限](#File_System_Other)
- [與不常見磁碟位置相關的檔案系統權限](#Unusual_Locations)
- [檢閱其他考量](#Review_additional_considerations)
- [登錄權限](#Registry)
- [WMI](#WMI)
- [具名管道](#Pipes)

### <a name="service-configuration-and-access-control"></a><a name="Serv_SID"></a> 服務組態和存取控制

[!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 會對它的每個服務啟用個別服務 SID，以提供深度的服務隔離和防禦。 每個服務 SID 都是衍生自服務名稱，而且是該服務專用的。 例如，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 服務的具名執行個體服務 SID 名稱可能是 **NT Service\MSSQL$** _\<InstanceName>_ 。 服務隔離可讓服務存取特定物件，而不需要以高權限帳戶執行或降低物件的安全性保護。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 服務可以透過使用包含服務 SID 的存取控制項目，來限制其資源的存取權。

> [!NOTE]
> 在 Windows 7 和 [!INCLUDE[nextref_longhorn](../../includes/nextref-longhorn-md.md)] R2 (以及更新版本) 上，個別服務 SID 可以是服務所使用的虛擬帳戶。

針對大部分元件， [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會直接為個別服務帳戶設定 ACL，因此不需重複資源 ACL 程序即可變更服務帳戶。

安裝 [!INCLUDE[ssAS](../../includes/ssas-md.md)]時，會建立 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] 的個別服務 SID。 另外還會建立本機的 Windows 群組，其命名格式為 **SQLServerMSASUser$** _computer_name_ **$** _instance_name_。 個別服務 SID **NT SERVICE\MSSQLServerOLAPService** 會在本機 Windows 群組中授與成員資格，而本機 Windows 群組則是在 ACL 中授與適當的權限。 如果用來啟動 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] 服務的帳戶變更， [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 組態管理員就必須變更部分 Windows 授權 (例如，以服務登入的權利)，不過指派給本機 Windows 群組的權限仍然可以使用，而且不會有任何更新，因為個別服務 SID 並未變更。 這個方法可讓 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] 服務在升級期間重新命名。

在安裝 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 期間， [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式會為 [!INCLUDE[ssAS](../../includes/ssas-md.md)] 和 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Browser 服務建立本機 Windows 群組。 針對這些服務， [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會為本機 Windows 群組設定 ACL。

根據服務組態，在安裝或升級期間服務帳戶或服務 SID 會加入做為服務群組成員。

### <a name="windows-privileges-and-rights"></a><a name="Windows"></a> Windows 權限和權利

指派為用來啟動服務的帳戶需要有服務的 **啟動、停止和暫停權限** 。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式會自動指派此權限。 請先安裝遠端伺服器管理工具 (RSAT)。 請參閱[適用於 Windows 10 的遠端伺服器管理工具](https://www.microsoft.com/download/details.aspx?id=45520)。

下表顯示 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式要求 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 元件所使用之個別服務 SID 或本機 Windows 群組需要有的權限。

|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 服務|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式授與的權限|
|---------------------------------------|------------------------------------------------------------|
|**[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]:**<br /><br /> (所有權利都會授與個別服務 SID。 預設執行個體：**NT SERVICE\MSSQLSERVER**。 具名執行個體：**NT Service\MSSQLServer$** _InstanceName_)。|**以服務方式登入** (SeServiceLogonRight)<br /><br /> **取代處理序層級 Token** (SeAssignPrimaryTokenPrivilege)<br /><br /> **略過周遊檢查** (SeChangeNotifyPrivilege)<br /><br /> **調整處理序的記憶體配額** (SeIncreaseQuotaPrivilege)<br /><br /> 啟動 SQL 寫入器的權限<br /><br /> 讀取事件記錄檔服務的權限<br /><br /> 讀取遠端程序呼叫服務的權限|

| **[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 代理程式：** \*<br /><br /> (所有權利都會授與個別服務 SID。 預設執行個體：**NT Service\SQLSERVERAGENT**。 具名執行個體：**NT Service\SQLAGENT$** _InstanceName_)。|**以服務方式登入** (SeServiceLogonRight)<br /><br /> **取代處理序層級 Token** (SeAssignPrimaryTokenPrivilege)<br /><br /> **略過周遊檢查** (SeChangeNotifyPrivilege)<br /><br /> **調整處理序的記憶體配額** (SeIncreaseQuotaPrivilege)| | **[!INCLUDE[ssAS](../../includes/ssas-md.md)]：**<br /><br /> (所有權利都會授與本機 Windows 群組。 預設執行個體：**SQLServerMSASUser$** _ComputerName_ **$MSSQLSERVER**。 具名執行個體：**SQLServerMSASUser$** _ComputerName_ **$** _InstanceName_。 [!INCLUDE[ssGeminiShort](../../includes/ssgeminishort-md.md)] 執行個體：**SQLServerMSASUser$** _ComputerName_ **$** _PowerPivot_)。|**以服務方式登入** (SeServiceLogonRight)<br /><br /> 僅限表格式：<br /><br /> **增加處理程序工作組** (SeIncreaseWorkingSetPrivilege)<br /><br /> **調整處理序的記憶體配額** (SeIncreaseQuotaPrivilege)<br /><br /> **鎖定記憶體中的分頁** (SeLockMemoryPrivilege) - 這只有在分頁完全關閉時才需要。<br /><br /> 僅限容錯移轉叢集安裝：<br /><br /> **增加排程優先順序** (SeIncreaseBasePriorityPrivilege)| | **[!INCLUDE[ssRS](../../includes/ssrs.md)]：**<br /><br /> (所有權利都會授與個別服務 SID。 預設執行個體：**NT SERVICE\ReportServer**。 具名執行個體：**NT SERVICE\\ReportServer$** _InstanceName_)。|**以服務方式登入** (SeServiceLogonRight)| | **[!INCLUDE[ssIS](../../includes/ssis-md.md)]：**<br /><br /> (所有權利都會授與個別服務 SID。 預設執行個體與具名執行個體：**NT SERVICE\MsDtsServer130**。 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 沒有具名執行個體的個別處理序)|。**以服務方式登入** (SeServiceLogonRight)<br /><br /> 寫入應用程式事件記錄檔的權限。<br /><br /> **略過周遊檢查** (SeChangeNotifyPrivilege)<br /><br /> **在驗證之後模擬用戶端** (SeImpersonatePrivilege)| |**全文檢索搜尋：**<br /><br /> (所有權利都會授與個別服務 SID。 預設執行個體：**NT Service\MSSQLFDLauncher**。 具名執行個體：**NT Service\ MSSQLFDLauncher$** _InstanceName_)。|**以服務方式登入** (SeServiceLogonRight)<br /><br /> **調整處理序的記憶體配額** (SeIncreaseQuotaPrivilege)<br /><br /> **略過周遊檢查** (SeChangeNotifyPrivilege)| | **[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 瀏覽器：**<br /><br /> (所有權利都會授與本機 Windows 群組。 預設或具名執行個體：**SQLServer2005SQLBrowserUser** _$ComputerName_。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 瀏覽器沒有具名執行個體的個別處理序)。|**Log on as a service** (SeServiceLogonRight)| | **[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] VSS 寫入器：**<br /><br /> (所有權利都會授與個別服務 SID。 預設或具名執行個體：**NT Service\SQLWriter**。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]　VSS 寫入器沒有具名執行個體的個別處理序)。|SQL 寫入器服務會以 LOCAL SYSTEM 帳戶執行，該帳戶擁有所有必要的權限。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式不會檢查或授與此服務的權限。 || **[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Distributed Replay 控制器：** |**以服務方式登入** (SeServiceLogonRight)| | **[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Distributed Replay 用戶端：** |**以服務方式登入** (SeServiceLogonRight)| |**PolyBase 引擎和 DMS**| **以服務方式登入** (SeServiceLogonRight)| |**啟動控制板：** |**以服務方式登入** (SeServiceLogonRight) <br /><br /> **取代處理序層級 Token** (SeAssignPrimaryTokenPrivilege)<br /><br />**略過周遊檢查** (SeChangeNotifyPrivilege)<br /><br />**調整處理序的記憶體配額** (SeIncreaseQuotaPrivilege)| |**R 服務/機器學習服務：** **SQLRUserGroup** (SQL 2016 和 2017) |預設沒有 **允許本機登入** 權限 | |**機器學習服務** '**所有應用程式套件 ' [AppContainer]** (SQL 2019) |對 SQL Server ' Binn '、R_Services 與 PYTHON_Services 目錄的 **讀取和執行權限** |

 \*[!INCLUDE[ssExpress](../../includes/ssexpress-md.md)] 的執行個體會停用 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent 代理程式服務。

### <a name="file-system-permissions-granted-to-sql-server-per-service-sids-or-local-windows-groups"></a><a name="Reviewing_ACLs"></a> 授與 SQL Server 個別服務 SID 或本機 Windows 群組的檔案系統權限

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 服務帳戶必須有資源的存取權。 存取控制清單會針對個別服務 SID 或本機 Windows 群組設定。

> [!IMPORTANT]
> 若為容錯移轉叢集安裝，則必須對本機帳戶的 ACL 設定共用磁碟上的資源。

下表顯示 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式所設定的 ACL：

|服務帳戶|檔案和資料夾|存取|
|-------------------------|-----------------------|------------|
|MSSQLServer|Instid\MSSQL\backup|完整控制|
||Instid\MSSQL\binn|讀取、執行|
||Instid\MSSQL\data|完整控制|
||Instid\MSSQL\FTData|完整控制|
||Instid\MSSQL\Install|讀取、執行|
||Instid\MSSQL\Log|完整控制|
||Instid\MSSQL\Repldata|完整控制|
||130\shared|讀取、執行|
||Instid\MSSQL\Template Data (僅限[!INCLUDE[ssExpress](../../includes/ssexpress-md.md)] )|讀取|
|SQLServerAgent\*|Instid\MSSQL\binn|完整控制|
||Instid\MSSQL\Log|讀取、寫入、刪除、執行|
||130\com|讀取、執行|
||130\shared|讀取、執行|
||130\shared\Errordumps|讀取、寫入|
||ServerName\EventLog|完整控制|
|FTS|Instid\MSSQL\FTData|完整控制|
||Instid\MSSQL\FTRef|讀取、執行|
||130\shared|讀取、執行|
||130\shared\Errordumps|讀取、寫入|
||Instid\MSSQL\Install|讀取、執行|
||Instid\MSSQL\jobs|讀取、寫入|
|MSSQLServerOLAPService|130\shared\ASConfig|完整控制|
||Instid\OLAP|讀取、執行|
||Instid\Olap\Data|完整控制|
||Instid\Olap\Log|讀取、寫入|
||Instid\OLAP\Backup|讀取、寫入|
||Instid\OLAP\Temp|讀取、寫入|
||130\shared\Errordumps|讀取、寫入|
|ReportServer|Instid\Reporting Services\Log Files|讀取、寫入、刪除|
||Instid\Reporting Services\ReportServer|讀取、執行|
||Instid\Reporting Services\ReportServer\global.asax|完整控制|
||Instid\Reporting Services\ReportServer\rsreportserver.config|讀取|
||Instid\Reporting Services\RSTempfiles|讀取、寫入、執行、刪除|
||Instid\Reporting Services\RSWebApp|讀取、執行|
||130\shared|讀取、執行|
||130\shared\Errordumps|讀取、寫入|
|MSDTSServer100|130\dts\binn\MsDtsSrvr.ini.xml|讀取|
||130\dts\binn|讀取、執行|
||130\shared|讀取、執行|
||130\shared\Errordumps|讀取、寫入|
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Browser|130\shared\ASConfig|讀取|
||130\shared|讀取、執行|
||130\shared\Errordumps|讀取、寫入|
|SQLWriter|N/A (以本機系統執行)||
|User|Instid\MSSQL\binn|讀取、執行|
||Instid\Reporting Services\ReportServer|讀取、執行、列出資料夾內容|
||Instid\Reporting Services\ReportServer\global.asax|讀取|
||Instid\Reporting Services\RSWebApp|讀取、執行、列出資料夾內容|
||130\dts|讀取、執行|
||130\tools|讀取、執行|
||100\tools|讀取、執行|
||90\tools|讀取、執行|
||80\tools|讀取、執行|
||130\sdk|讀取|
||Microsoft SQL Server\130\Setup Bootstrap|讀取、執行|
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Distributed Replay Controller|\<ToolsDir>\DReplayController\Log\ (空白目錄)|讀取、執行、列出資料夾內容|
||\<ToolsDir>\DReplayController\DReplayController.exe|讀取、執行、列出資料夾內容|
||\<ToolsDir>\DReplayController\resources\|讀取、執行、列出資料夾內容|
||\<ToolsDir>\DReplayController\\{all dlls}|讀取、執行、列出資料夾內容|
||\<ToolsDir>\DReplayController\DReplayController.config|讀取、執行、列出資料夾內容|
||\<ToolsDir>\DReplayController\IRTemplate.tdf|讀取、執行、列出資料夾內容|
||\<ToolsDir>\DReplayController\IRDefinition.xml|讀取、執行、列出資料夾內容|
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Distributed Replay Client|\<ToolsDir>\DReplayClient\Log\|讀取、執行、列出資料夾內容|
||\<ToolsDir>\DReplayClient\DReplayClient.exe|讀取、執行、列出資料夾內容|
||\<ToolsDir>\DReplayClient\resources\|讀取、執行、列出資料夾內容|
||\<ToolsDir>\DReplayClient\ (所有 DLL)|讀取、執行、列出資料夾內容|
||\<ToolsDir>\DReplayClient\DReplayClient.config|讀取、執行、列出資料夾內容|
||\<ToolsDir>\DReplayClient\IRTemplate.tdf|讀取、執行、列出資料夾內容|
||\<ToolsDir>\DReplayClient\IRDefinition.xml|讀取、執行、列出資料夾內容|
|Launchpad|%binn|讀取、執行|
||ExtensiblilityData|完整控制|
||Log\ExtensibilityLog|完整控制|

\*[!INCLUDE[ssExpress](../../includes/ssexpress-md.md)] 與 [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)] with Advanced Services 的執行個體會停用 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] A代理程式服務。

當資料庫檔案儲存於使用者定義的位置時，您必須授與個別服務 SID 對該位置的存取權。 如需將檔案系統權限授與個別服務 SID 的詳細資訊，請參閱 [設定 Database Engine 對檔案系統的存取權限](../../database-engine/configure-windows/configure-file-system-permissions-for-database-engine-access.md)。

### <a name="file-system-permissions-granted-to-other-windows-user-accounts-or-groups"></a><a name="File_System_Other"></a> 授與其他 Windows 使用者帳戶或群組的檔案系統權限

某些存取控制權限可能必須授與給內建帳戶或其他 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 服務帳戶。 下表列出 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式所設定的其他 ACL。

|要求元件|帳戶|資源|權限|
|--------------------------|-------------|--------------|-----------------|
|MSSQLServer|效能記錄使用者|Instid\MSSQL\binn|列出資料夾內容|
||效能監視器使用者|Instid\MSSQL\binn|列出資料夾內容|
||效能記錄使用者、效能監視器使用者|\WINNT\system32\sqlctr130.dll|讀取、執行|
||僅限管理員|\\\\.\root\Microsoft\SqlServer\ServerEvents\\<SQL_執行個體名稱>\*|完整控制|
||管理員，系統|\tools\binn\schemas\sqlserver\2004\07\showplan|完整控制|
||使用者|\tools\binn\schemas\sqlserver\2004\07\showplan|讀取、執行|
|[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]|報表伺服器 Windows 服務帳戶|*\<install>* \Reporting Services\LogFiles|刪除<br /><br /> READ_CONTROL<br /><br /> SYNCHRONIZE<br /><br /> FILE_GENERIC_READ<br /><br /> FILE_GENERIC_WRITE<br /><br /> FILE_READ_DATA<br /><br /> FILE_WRITE_DATA<br /><br /> FILE_APPEND_DATA<br /><br /> FILE_READ_EA<br /><br /> FILE_WRITE_EA<br /><br /> FILE_READ_ATTRIBUTES<br /><br /> FILE_WRITE_ATTRIBUTES|
||報表伺服器 Windows 服務帳戶|*\<install>* \Reporting Services\ReportServer|讀取|
||報表伺服器 Windows 服務帳戶|*\<install>* \Reporting Services\ReportServer\global.asax|完整|
||報表伺服器 Windows 服務帳戶|*\<install>* \Reporting Services\RSWebApp|讀取、執行|
||所有人|*\<install>* \Reporting Services\ReportServer\global.asax|READ_CONTROL<br /><br /> FILE_READ_DATA<br /><br /> FILE_READ_EA<br /><br /> FILE_READ_ATTRIBUTES|
||報表伺服器 Windows 服務帳戶|*\<install>* \Reporting Services\ReportServer\rsreportserver.config|刪除<br /><br /> READ_CONTROL<br /><br /> SYNCHRONIZE<br /><br /> FILE_GENERIC_READ<br /><br /> FILE_GENERIC_WRITE<br /><br /> FILE_READ_DATA<br /><br /> FILE_WRITE_DATA<br /><br /> FILE_APPEND_DATA<br /><br /> FILE_READ_EA<br /><br /> FILE_WRITE_EA<br /><br /> FILE_READ_ATTRIBUTES<br /><br /> FILE_WRITE_ATTRIBUTES|
||所有人|報表伺服器索引鍵 (Instid 登錄區)|查詢值<br /><br /> 列舉子機碼<br /><br /> Notify<br /><br /> 讀取控制|
||終端服務使用者|報表伺服器索引鍵 (Instid 登錄區)|查詢值<br /><br /> 設定值<br /><br /> 建立子機碼<br /><br /> 列舉子機碼<br /><br /> Notify<br /><br /> 刪除<br /><br /> 讀取控制|
||進階使用者|報表伺服器索引鍵 (Instid 登錄區)|查詢值<br /><br /> 設定值<br /><br /> 建立子機碼<br /><br /> 列舉子機碼<br /><br /> Notify<br /><br /> 刪除<br /><br /> 讀取控制|

\*這是 WMI 提供者命名空間。

### <a name="file-system-permissions-related-to-unusual-disk-locations"></a><a name="Unusual_Locations"></a> 與不常見磁碟位置相關的檔案系統權限

安裝位置的預設磁碟機為 **systemdrive**，通常是 C 磁碟機。本節描述將 tempdb 或使用者資料庫安裝至不常見位置時的其他考量。

**非預設磁碟機**

安裝到非預設磁碟機的本機磁碟機時，個別服務 SID 必須擁有檔案位置的存取權。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式將提供必要的存取。

**網路共用**

將資料庫安裝到網路共用時，服務帳戶必須擁有使用者和 tempdb 資料庫之檔案位置的存取權。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式不會提供網路共用的存取。 在執行安裝程式之前，使用者必須先為服務帳戶提供 tempdb 位置的存取。 使用者在建立資料庫之前，必須先提供使用者資料庫位置的存取。

> [!NOTE]
> 虛擬帳戶無法對遠端位置驗證。 所有虛擬帳戶都使用電腦帳戶的權限。 使用下列格式提供電腦帳戶：<網域名稱> **\\** <電腦名稱> **$** 。

### <a name="reviewing-additional-considerations"></a><a name="Review_additional_considerations"></a> 檢閱其他考量

下表是 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 服務提供其他功能時所需的權限。

|服務/應用程式|功能|必要權限|
|--------------------------|-------------------|-------------------------|
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (MSSQLSERVER)|使用 xp_sendmail 寫入郵件位置。|網路寫入權限。|
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (MSSQLSERVER)|對 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 管理員以外的使用者執行 xp_cmdshell。|做為作業系統的一部分並取代處理序層級 Token。|
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent (MSSQLSERVER)|使用自動重新啟動功能。|必須是管理員本機群組的成員。|
|[!INCLUDE[ssDE](../../includes/ssde-md.md)] Tuning Advisor|調整資料庫以達到最佳查詢效能。|第一次使用時，具有系統管理認證的使用者必須初始化應用程式。 初始化之後，dbo 使用者使用 [!INCLUDE[ssDE](../../includes/ssde-md.md)] Tuning Advisor 只能微調他們擁有的資料表。 如需詳細資訊，請參閱《 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 線上叢書》中的＜初始化 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Tuning Advisor＞。|

> [!IMPORTANT]
> 在升級 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 之前，請先啟用 SQL Server Agent 並驗證必要的預設組態：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent 服務帳戶是 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] **sysadmin** 固定伺服器角色的成員。

### <a name="registry-permissions"></a><a name="Registry"></a> 登錄權限

執行個體感知元件的登錄區會建立在 **HKLM\Software\Microsoft\Microsoft SQL Server\\** <執行個體識別碼> 之下。 例如：

- **HKLM\Software\Microsoft\Microsoft SQL Server\MSSQL13.MyInstance**
- **HKLM\Software\Microsoft\Microsoft SQL Server\MSASSQL13.MyInstance**
- **HKLM\Software\Microsoft\Microsoft SQL Server\MSSQL.130**

登錄也會維護執行個體識別碼到執行個體名稱的對應。 執行個體識別碼到執行個體名稱的對應維護如下：

- **[HKEY_LOCAL_MACHINE\Software\Microsoft\Microsoft SQL Server\Instance Names\SQL] "InstanceName"="MSSQL13"**
- **[HKEY_LOCAL_MACHINE\Software\Microsoft\Microsoft SQL Server\Instance Names\OLAP] "InstanceName"="MSASSQL13"**
- **[HKEY_LOCAL_MACHINE\Software\Microsoft\Microsoft SQL Server\Instance Names\RS] "InstanceName"="MSRSSQL13"**

### <a name="wmi"></a><a name="WMI"></a> WMI

Windows Management Instrumentation (WMI) 必須能夠連接到 [!INCLUDE[ssDE](../../includes/ssde-md.md)]。 為支援此功能，會在 **中提供 Windows WMI 提供者 (** NT SERVICE\winmgmt [!INCLUDE[ssDE](../../includes/ssde-md.md)]) 的個別服務 SID。

SQL WMI 提供者需要下列最低權限：

- msdb 資料庫中 **db_ddladmin** 或 **db_owner** 固定資料庫角色的成員資格。
- 伺服器中的 **CREATE DDL EVENT NOTIFICATION** 權限。
- **中的** CREATE TRACE EVENT NOTIFICATION [!INCLUDE[ssDE](../../includes/ssde-md.md)]權限。
- **VIEW ANY DATABASE** 伺服器層級權限。

  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式會建立 SQL WMI 命名空間，並且將讀取權限授與 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent 服務 SID。

### <a name="named-pipes"></a><a name="Pipes"></a> 具名管道

在所有安裝中， [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式都會透過共用記憶體通訊協定 (它是本機具名管道) 提供 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 的存取。

## <a name="provisioning"></a><a name="Provisioning"></a> 提供

本節描述如何在各種 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 元件內提供帳戶。

- [資料庫引擎提供](#DE_Prov)

  - [Windows 主體](#Win_Principals)
  - [sa 帳戶](#sa)
  - [SQL Server 個別服務 SID 登入和權限](#Logins)
  - [SQL Server Agent 登入和權限](#Agent)
  - [HADRON 和 SQL 容錯移轉叢集執行個體和權限](#Hadron)
  - [SQL 寫入器和權限](#Writer)
  - [SQL WMI 和權限](#SQLWMI)
- [SSAS 提供](#SSAS)
- [SSRS 提供](#SSRS)

### <a name="database-engine-provisioning"></a><a name="DE_Prov"></a> 資料庫引擎提供

下列帳戶會在 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]中加入為登入。

#### <a name="windows-principals"></a><a name="Win_Principals"></a> Windows 主體

在安裝期間， [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式至少需要將一個使用者帳戶命名為 **系統管理員 (sysadmin)** 固定伺服器角色的成員。

#### <a name="sa-account"></a><a name="sa"></a> sa 帳戶

**sa** 帳戶一律做為 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 登入存在，而且是 **系統管理員 (sysadmin)** 固定伺服器角色的成員。 僅使用 Windows 驗證安裝 [!INCLUDE[ssDE](../../includes/ssde-md.md)] (也就是未啟用 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 驗證) 時，儘管 **A** 登入仍然存在，但是會停用，且密碼是複雜且隨機的。 如需啟用 **sa** 帳戶的詳細資訊，請參閱 [變更伺服器驗證模式](../../database-engine/configure-windows/change-server-authentication-mode.md)。

#### <a name="sql-server-per-service-sid-login-and-privileges"></a><a name="Logins"></a> SQL Server 個別服務 SID 登入和權限

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 服務的個別服務 SID (也稱為服務安全性主體 (SID)) 會佈建為 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 登入。 個別服務 SID 登入是 **系統管理員 (sysadmin)** 固定伺服器角色的成員。 如需個別服務 SID 的詳細資訊，請參閱[使用服務 SID 將權限授與 SQL Server 中的服務](../../relational-databases/security/using-service-sids-to-grant-permissions-to-services-in-sql-server.md)。

#### <a name="sql-server-agent-login-and-privileges"></a><a name="Agent"></a> SQL Server Agent 登入和權限

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent 服務的個別服務 SID 會做為 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 登入提供。 個別服務 SID 登入是 **系統管理員 (sysadmin)** 固定伺服器角色的成員。

#### <a name="sshadrc-and-sql-failover-cluster-instance-and-privileges"></a><a name="Hadron"></a> [!INCLUDE[ssHADRc](../../includes/sshadrc-md.md)] 和 SQL 容錯移轉叢集執行個體和權限

將 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 安裝為 [!INCLUDE[ssHADR](../../includes/sshadr-md.md)] 或 SQL 容錯移轉叢集執行個體 (SQL FCI) 時，會在 **中提供** LOCAL SYSTEM [!INCLUDE[ssDE](../../includes/ssde-md.md)]。 **LOCAL SYSTEM** 登入會獲得授與 **ALTER ANY AVAILABILITY GROUP** 權限 (適用於 [!INCLUDE[ssHADR](../../includes/sshadr-md.md)]) 和 **VIEW SERVER STATE** 權限 (適用於 SQL FCI)。

#### <a name="sql-writer-and-privileges"></a><a name="Writer"></a> SQL 寫入器和權限

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] VSS Writer 服務的個別服務 SID 會做為 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 登入提供。 個別服務 SID 登入是 **系統管理員 (sysadmin)** 固定伺服器角色的成員。

#### <a name="sql-wmi-and-privileges"></a><a name="SQLWMI"></a> SQL WMI 和權限

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式會提供 **NT SERVICE\Winmgmt** 帳戶作為 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 登入，並且將它加入 **系統管理員 (sysadmin)** 固定伺服器角色中。

#### <a name="ssrs-provisioning"></a>SSRS 提供

安裝過程中指定的帳戶會做為 **RSExecRole** 資料庫角色的成員提供。 如需詳細資訊，請參閱《 [設定報表伺服器服務帳戶 &#40;SSRS 組態管理員&#41;](../../reporting-services/install-windows/configure-the-report-server-service-account-ssrs-configuration-manager.md)。

### <a name="ssas-provisioning"></a><a name="SSAS"></a> SSAS 提供

[!INCLUDE[ssAS](../../includes/ssas-md.md)] 服務帳戶需求會依據您部署伺服器的方式而有所不同。 如果您要安裝 [!INCLUDE[ssGeminiShort](../../includes/ssgeminishort-md.md)]， [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式會要求您將 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] 服務設定為以網域帳戶執行。 網域帳戶是支援 SharePoint 內建的受管理帳戶設備所需。 基於這個理由， [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式不會針對 [!INCLUDE[ssGeminiShort](../../includes/ssgeminishort-md.md)] 安裝提供預設的服務帳戶 (例如虛擬帳戶)。 如需佈建 [!INCLUDE[ssGemini](../../includes/ssgemini-md.md)] for SharePoint 的詳細資訊，請參閱 [設定 Power Pivot 服務帳戶](/analysis-services/power-pivot-sharepoint/configure-power-pivot-service-accounts)。

針對所有其他獨立 [!INCLUDE[ssAS](../../includes/ssas-md.md)] 安裝，您可以提供以網域帳戶、內建系統帳戶、受管理的帳戶或虛擬帳戶執行的服務。 如需佈建帳戶的詳細資訊，請參閱 [設定服務帳戶 &#40;Analysis Services&#41;](/analysis-services/instances/configure-service-accounts-analysis-services)。

針對叢集安裝，您必須指定網域帳戶或內建系統帳戶。 [!INCLUDE[ssAS](../../includes/ssas-md.md)] 容錯移轉叢集不支援受管理的帳戶，也不支援虛擬帳戶。

所有 [!INCLUDE[ssAS](../../includes/ssas-md.md)] 安裝會要求您指定 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] 執行個體的系統管理員。 系統管理員權限會在 Analysis Services **Server** 角色中提供。

### <a name="ssrs-provisioning"></a><a name="SSRS"></a> SSRS 提供

安裝過程中指定的帳戶會在 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 中做為 **RSExecRole** 資料庫角色的成員提供。 如需詳細資訊，請參閱《 [設定報表伺服器服務帳戶 &#40;SSRS 組態管理員&#41;](../../reporting-services/install-windows/configure-the-report-server-service-account-ssrs-configuration-manager.md)。

## <a name="upgrading-from-previous-versions"></a><a name="Upgrade"></a> 從舊版升級

本節描述從舊版 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]升級時所做的變更。

- [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 需要 [!INCLUDE[nextref_longhorn](../../includes/nextref-longhorn-md.md)] R2 SP1、Windows Server 2012、Windows 8.0、Windows Server 2012 R2 或 Windows 8.1。 在較低作業系統版本上執行的任何舊版 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 都必須先將作業系統升級，才能升級 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]。
- 在將 [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] 升級至 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)]期間， [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式將以下列方式設定 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。

  - [!INCLUDE[ssDE](../../includes/ssde-md.md)] 會以個別服務 SID 的安全性內容執行。 個別服務 SID 會獲得存取 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體的檔案資料夾 (例如 DATA) 和 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登錄機碼的權限。
  - [!INCLUDE[ssDE](../../includes/ssde-md.md)] 的個別服務 SID 是在 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 中作為 **系統管理員 (sysadmin)** 固定伺服器角色的成員提供。
  - 除非 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 是容錯移轉叢集執行個體，否則個別服務 SID 會加入至本機 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Windows 群組。
  - [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 資源仍然會提供至本機 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Windows 群組。
  - 服務的本機 Windows 群組已從 **SQLServer2005MSSQLUser$** _<電腦名稱>_ **$** _<執行個體名稱>_ 重新命名為 **SQLServerMSSQLUser$** <電腦名稱> **$** <執行個體名稱>。 移轉之資料庫的檔案位置將會有本機 Windows 群組的存取控制項目 (ACE)。 新資料庫的位置將會有個別服務 SID 的 ACE。

- 從 [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 升級的期間，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式將會保留 [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 個別服務 SID 的 ACE。
- 如果是 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 容錯移轉叢集執行個體，則會保留為服務設定之網域帳戶的 ACE。

## <a name="appendix"></a><a name="Appendix"></a> 附錄

本節包含有關 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 服務的其他資訊。

- [服務帳戶的描述](#Serv_Accts)
- [識別執行個體感知和執行個體非感知服務](#Identify_instance_aware_and_unaware)
- [當地語系化服務名稱](#Localized_service_names)

### <a name="description-of-service-accounts"></a><a name="Serv_Accts"></a> 服務帳戶的描述

服務帳戶是用來啟動 Windows 服務 (例如 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]) 的帳戶。 執行 SQL Server 時，除了服務 SID 之外，不需要將服務帳戶作為登入新增至 SQL Server，服務 SID 一律存在，且是 **sysamin** 固定伺服器角色的成員。

#### <a name="accounts-available-with-any-operating-system"></a><a name="Any_OS"></a> 可搭配任何作業系統使用的帳戶

除了前段所描述新的 [MSA](#MSA)、[gMSA](#GMSA) 與 [虛擬帳戶](#VA_Desc)之外，下列帳戶都可以使用。

<a name="Domain_User"></a> **網域使用者帳戶**

如果此服務必須與網路服務互動，請存取檔案共用等網域資源。如果它使用執行 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]之其他電腦的連結的伺服器連接，您可能會使用最低權限的網域帳戶。 許多伺服器對伺服器的活動只能以網域使用者帳戶執行。 這個帳戶應該由環境中的網域管理所預先建立。

> [!NOTE]
> 如果將 SQL Server 設定為使用網域帳戶，則可隔離服務的權限，不過必須手動管理密碼或建立自訂解決方案來管理這些密碼。 許多伺服器應用程式都是使用此策略來增強安全性，不過此策略需要額外的管理和複雜性。 在這些部署中，服務管理員會花相當多時間進行維護工作，例如管理 Kerberos 驗證所需的服務密碼和服務主要名稱 (SPN)。 此外，這些維護工作都可能干擾服務。

<a name="Local_User"></a> **本機使用者帳戶**

如果電腦不屬於網域的一部分，建議您使用不含 Windows 管理員權限的本機使用者帳戶。

<a name="Local_Service"></a> **本機服務帳戶**

本機服務帳戶是一個內建帳戶，它對於資源和物件的存取層級與使用者群組的成員相同。 如果個別服務或處理序受到危害時，這種有限的存取權可協助保護系統的安全。 以本機服務帳戶執行的服務是以不含認證的 Null 工作階段來存取網路資源。 
> [!NOTE]
> 本機服務帳戶不支援 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 或 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent 服務。 不支援使用本機服務當做執行這些服務的帳戶，因為它是共用服務而且在本機服務之下執行的任何其他服務將會讓系統管理員存取 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]。
此帳戶的實際名稱是 **NT AUTHORITY\LOCAL SERVICE**。

<a name="Network_Service"></a> **網路服務帳戶**

網路服務帳戶是一個內建帳戶，它對於資源和物件所擁有的存取權高於使用者群組的成員。 以網路服務帳戶執行的服務，會利用電腦帳戶的認證 (格式為 _<網域名稱>_ **\\** <電腦名稱> **$** ) 存取網路資源。 此帳戶的實際名稱是 **NT AUTHORITY\NETWORK SERVICE**。

<a name="Local_System"></a> **本機系統帳戶**

本機系統是權限非常高的內建帳戶。 它在本機系統上具有延伸的權限，並可當做網路上的電腦運作。 此帳戶的實際名稱是 **NT AUTHORITY\SYSTEM**。

### <a name="identifying-instance-aware-and-instance-unaware-services"></a><a name="Identify_instance_aware_and_unaware"></a> 識別執行個體感知和執行個體非感知服務

執行個體感知服務會與特定的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]執行個體產生關聯，而且會有自己的登錄區。 您可以針對每一個元件或服務執行 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式，以安裝執行個體感知服務的多個複本。 執行個體非感知服務會在所有安裝的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體之間共用。 它們與特定執行個體沒有關聯，只能安裝一次，且不能並存安裝。

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中的執行個體感知服務包含以下項目：

- [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]
- [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent

   請注意，在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 和 [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)] with Advanced Services 的執行個體上會停用 [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)] Agent 服務。

- [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]\*
- [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]
- 全文檢索搜尋

  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中的執行個體非感知服務包含以下項目：

- [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)]
- [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Browser
- SQL 寫入器

 \*處於 SharePoint 整合模式中的 Analysis Services 會以單一具名執行個體的形式執行 '[!INCLUDE[ssGemini](../../includes/ssgemini-md.md)]'。 執行個體名稱是固定的。 您無法指定不同的名稱。 在每個實體伺服器上，您只能安裝一個執行成 '[!INCLUDE[ssGemini](../../includes/ssgemini-md.md)]' 的 Analysis Services 執行個體。

### <a name="localized-service-names"></a><a name="Localized_service_names"></a> 當地語系化服務名稱

下表將顯示 Windows 當地語系化版本所顯示的服務名稱。

|Language|本機服務的名稱|網路服務的名稱|本機系統的名稱|管理群組的名稱|
|--------------|----------------------------|------------------------------|---------------------------|--------------------------|
|英文<br /><br /> 簡體中文<br /><br /> 繁體中文<br /><br /> 韓文<br /><br /> 日文|NT AUTHORITY\LOCAL SERVICE|NT AUTHORITY\NETWORK SERVICE|NT AUTHORITY\SYSTEM|BUILTIN\Administrators|
|德文|NT-AUTORITÄT\LOKALER DIENST|NT-AUTORITÄT\NETZWERKDIENST|NT-AUTORITÄT\SYSTEM|VORDEFINIERT\Administratoren|
|法文|AUTORITE NT\SERVICE LOCAL|AUTORITE NT\SERVICE RÉSEAU|AUTORITE NT\SYSTEM|BUILTIN\Administrators|
|義大利文|NT AUTHORITY\SERVIZIO LOCALE|NT AUTHORITY\SERVIZIO DI RETE|NT AUTHORITY\SYSTEM|BUILTIN\Administrators|
|西班牙文|NT AUTHORITY\SERVICIO LOC|NT AUTHORITY\SERVICIO DE RED|NT AUTHORITY\SYSTEM|BUILTIN\Administradores|
|俄文|NT AUTHORITY\LOCAL SERVICE|NT AUTHORITY\NETWORK SERVICE|NT AUTHORITY\СИСТЕМА|BUILTIN\Администраторы|

## <a name="related-content"></a>相關內容

[SQL Server 安裝的安全性考量](../../sql-server/install/security-considerations-for-a-sql-server-installation.md)

[SQL Server 的預設和具名執行個體的檔案位置](../../sql-server/install/file-locations-for-default-and-named-instances-of-sql-server.md)

[安裝 Master Data Services](../../master-data-services/install-windows/install-master-data-services.md)
