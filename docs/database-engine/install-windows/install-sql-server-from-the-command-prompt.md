---
title: SQL Server 安裝 - 命令提示字元參數
description: 此文章描述 SQL Server 安裝的命令參數。 您可以指定要安裝及設定的功能。
ms.prod: sql
ms.technology: install
ms.topic: conceptual
helpviewer_keywords:
- installing SQL Server, command prompt
- installation scripts [SQL Server]
- maintenance scripts [SQL Server]
- REMOVENODE property
- components [SQL Server], removing
- command prompt [SQL Server], SQL Server installations
- ASACCOUNT parameter
- failover clustering [SQL Server], installing
- master database [SQL Server], rebuilding
- SQLCOLLATION parameter
- clusters [SQL Server], installing
- unattended installations [SQL Server]
- modifying collations
- AGTPASSWORD parameter
- USESYSDB parameter
- RSPASSWORD parameter
- AUTOSTART parameter
- ASPASSWORD parameter
- stand-alone installations [SQL Server]
- SAMPLEDATABASESERVER parameter
- adding components
- SAPWD parameter
- scripts [SQL Server], uninstallations
- remote installations [SQL Server]
- components [SQL Server], installing
- TARGETCOMPUTER parameter
- REMOVENODE parameter
- REINSTALLMODE parameter
- scripts [SQL Server], maintenance
- rebuilding registry
- SQLPASSWORD parameter
- rebuilding databases
- IP property
- PIDKEY parameter
- RSCONFIGURATION parameter
- ADDLOCAL parameter
- Setup [SQL Server], command prompt
- REBUILDDATABASE parameter
- SECURITYMODE parameter
- REMOVE property
- DISABLENETWORKPROTOCOLS parameter
- INSTALLDATADIR parameter
- REMOVE parameter
- removing components
- SQLACCOUNT parameter
- parameters [SQL Server], SQL Server installations
- UPGRADE parameter
- shortcuts [SQL Server]
- updating components
- removing SQL Server
- clustered instance of SQL Server
- INSTALLSQLDATADIR parameter
- RSACCOUNT parameter
- ADMINPASSWORD parameter
- GROUP property
- ERRORREPORTING property
- uninstallation scripts [SQL Server]
- AGTACCOUNT parameter
- SAVESYSDB parameter
- INSTALLVS parameter
- INSTANCENAME parameter
- scripts [SQL Server], installations
- rebuilding database, master
- uninstalling SQL Server
- ASCOLLATION parameter
- .ini files
- ADDNODE parameter
- command line installations [SQL Server]
- VS parameter
- INSTALLASDATADIR parameter
- INSTALLSQLDIR parameter
- nodes [Faillover Clustering], command prompt
- INSTALLSQLSHAREDDIR parameter
ms.assetid: df40c888-691c-4962-a420-78a57852364d
author: cawrites
ms.author: chadam
ms.reviewer: ''
ms.custom: ''
ms.date: 01/07/2020
monikerRange: '>=sql-server-2016'
ms.openlocfilehash: f3d0be0d607a8f6a810e9d1f24fbbbff1dc11a69
ms.sourcegitcommit: d681796e8c012eca2d9629d3b816749e9f50f868
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98005365"
---
# <a name="install-sql-server-from-the-command-prompt"></a>從命令提示字元安裝 SQL Server

[!INCLUDE [SQL Server -Windows Only](../../includes/applies-to-version/sql-windows-only.md)]

在執行 SQL Server 安裝程式之前，檢閱 [規劃 SQL Server 安裝](../../sql-server/install/planning-a-sql-server-installation.md)。  

在命令提示字元中安裝新的 SQL Server 執行個體，可讓您指定要安裝的功能以及這些功能應該設定的方式。 您也可以指定與安裝程式使用者介面的無訊息、基本或完整互動。  

若要從命令提示字元安裝，請開啟系統管理命令提示字元，並巡覽至 setup.exe 位於 [SQL Server 安裝媒體](https://www.microsoft.com/sql-server/sql-server-downloads)內的位置。 執行 `setup.exe` 命令，以及完成所嘗試執行動作的必要參數和選擇性參數：

`C:\SQLMedia\SQLServer2019> setup.exe /[Option] /[Option] = {value}`

下列範例會以安靜模式安裝 SQL Server 資料庫引擎、SQL Server Analysis Services、SQL Server Integration Services 和 SQL Server 工具：

```console
C:\SQLMedia\SQLServer2019> setup.exe /Q /IACCEPTSQLSERVERLICENSETERMS /ACTION="install" /PID="AAAAA-BBBBB-CCCCC-DDDDD-EEEEE" /FEATURES=SQL,AS,IS,Tools
/INSTANCENAME=MSSQLSERVER /SQLSVCACCOUNT="MyDomain\MyAccount"
/SQLSVCPASSWORD="************" /SQLSYSADMINACCOUNTS="MyDomain\MyAccount "
/AGTSVCACCOUNT="MyDomain\MyAccount" /AGTSVCPASSWORD="************"
/ASSVCACCOUNT="MyDomain\MyAccount" /ASSVCPASSWORD="************"
/ISSVCAccount="MyDomain\MyAccount" /ISSVCPASSWORD="************"
/ASSYSADMINACCOUNTS="MyDomain\MyAccount"
```

若要在主控台內檢視所有可能命令的清單，請使用 /help 旗標來執行可執行檔： 

```console
C:\SQLMedia\SQLServer2019> setup.exe /help
```

本文其餘部分會提供可用參數的詳細描述。 

  
> [!NOTE]
> 透過命令提示字元安裝時，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 支援使用 /Q 參數的完整無訊息模式或使用 /QS 參數的簡單無訊息模式。 /QS 參數只會顯示進度、不接受任何輸入，而且不會顯示任何遇到的錯誤訊息。 只有當您指定 /Action=install 時，才支援 /QS 參數。  
  
 除非軟體的使用方式受到個別的合約 (例如 Microsoft 大量授權合約或與 ISV 或 OEM 簽訂的協力廠商合約) 所管制，否則不論安裝方法為何，您都必須確認以個人身分或代表實體接受軟體授權條款。  
  
 這些授權條款會顯示在安裝程式使用者介面中，供您檢閱和接受。 自動安裝 (使用 /Q 或 /QS 參數) 必須包括 /IACCEPTSQLSERVERLICENSETERMS 參數。 您可以另外在 [Microsoft 軟體授權合約](https://go.microsoft.com/fwlink/?LinkId=148209)檢閱授權條款。  
  
> [!NOTE] 
> 根據您收到本軟體的方式 (例如，透過 Microsoft 大量授權)，軟體的使用方式可能會受到其他條款與條件的限制。  
  
 以下狀況支援命令提示字元安裝：  
  
-   使用命令提示字元中指定的語法和參數，在本機電腦上安裝、升級或移除 SQL Server 的執行個體和共用元件。    
-   安裝、升級或移除容錯移轉叢集執行個體。   
-   從某個 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 版本升級為另一個 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]版本。    
-   使用組態檔中指定的語法和參數，在本機電腦上安裝 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的執行個體。 您可以使用這個方法，將安裝組態複製到多部電腦，也可以安裝容錯移轉叢集安裝的多個節點。  
  
 
> [!NOTE]
> 如果是本機安裝，您必須以管理員身分執行安裝程式。 如果您是從遠端共用位置安裝 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]，則必須使用對遠端共用位置具有讀取和執行權限的網域帳戶。 若為容錯移轉叢集安裝，您必須是本機系統管理員，而且在所有容錯移轉叢集節點上擁有權限，能夠登入成為服務以及做為作業系統的一部分。  
  
##  <a name="proper-use-of-setup-parameters"></a><a name="ProperUse"></a> 安裝程式參數的正確用法  
您可以使用下列指導方針來開發具有正確語法的安裝命令：  
  
-   /PARAMETER (範例：`/INDICATEPROGRESS`)
-   /PARAMETER=true/false  (範例：`/SQLSVCINSTANTFILEINIT=True`)
-   /PARAMETER=1/0 (適用於布林值類型)   (範例：`/TCPENABLED=1`)
-   /PARAMETER="value" (針對所有單一值參數)。 (範例：`/PID="PID" /SQLSVCSTARTUPTYPE="Automatic"`)
    - 針對需要路徑的參數：支援 `/INSTANCEDIR=c:\Path` 或 `/INSTANCEDIR="c:\Path"`。  
-   /PARAMETER="value1" "value2" "value3" (針對所有多重值參數)。 (範例：`/SQLSYSADMINACCOUNTS="Contoso\John" "Contoso\Mary"`)
    - **例外狀況**：`/FEATURES`，這是多重值參數，但其格式為 `/FEATURES=AS,RS,IS` (不含空格且以逗號分隔)。 

  
> [!IMPORTANT]  
> 安裝 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 時，若您為 INSTANCEDIR 和 SQLUSERDBDIR 指定相同的目錄路徑，SQL Server Agent 和全文檢索搜尋會因為遺漏權限而無法啟動。  
  
> [!NOTE]  
> 關聯式伺服器值支援在此路徑中使用其他結束的反斜線格式 (一個反斜線或兩個反斜線字元)。  

下列各節會針對安裝、更新和修復狀況提供可開發命令列安裝指令碼的參數。  
  
針對 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 元件所列出的參數是該元件專用的。 SQL Server Agent 和 SQL Server Browser 參數適用於安裝 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]時。  


##  <a name="installation-parameters"></a><a name="Install"></a> 安裝參數  
 您可以使用下表中參數來開發安裝的命令列指令碼。  
  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 元件|參數|描述|  
|-----------------------------------------|---------------|-----------------|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式控制|/ACTION<br /><br /> **必要**|指出安裝工作流程的必要參數。<br /><br /> 支援的值：**Install**。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/SUPPRESSPRIVACYSTATEMENTNOTICE<br /><br /> **只有當您針對自動安裝指定了 /Q 或 /QS 參數時，才需要使用此參數。**|隱藏隱私權注意事項聲明。 藉由使用這個旗標，表示您同意[隱私權注意事項](../../sql-server/sql-server-privacy.md)。  |  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/IACCEPTSQLSERVERLICENSETERMS<br /><br /> **只有當您針對自動安裝指定了 /Q 或 /QS 參數時，才需要使用此參數。**|確認接受授權條款的必要參數。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Python 安裝程式控制|/IACCEPTPYTHONLICENSETERMS <br /><br /> **只有當您針對包含 Anaconda Python 套件的自動安裝指定 /Q 或 /QS 參數時，才需要使用此參數。**|確認接受授權條款的必要參數。| 
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] R 安裝程式控制|/IACCEPTROPENLICENSETERMS <br /><br /> **只有當您針對包含 Microsoft R Open 套件的自動安裝指定 /Q 或 /QS 參數時，才需要使用此參數。**|確認接受授權條款的必要參數。| 
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式控制|/ENU<br /><br /> **選擇性**|當安裝媒體包含英文以及與作業系統對應之語言的語言套件時，使用此參數在當地語系化的作業系統上安裝英文版的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式控制|/UpdateEnabled<br /><br /> **選擇性**|指定 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式是否應該探索及包含產品更新。 有效值為 True 和 False 或 1 和 0。 根據預設， [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式會包含找到的更新。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式控制|/UpdateSource<br /><br /> **選擇性**|指定 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式將取得產品更新的位置。 有效值為 "MU"，表示搜尋 [!INCLUDE[msCoName](../../includes/msconame-md.md)] 更新、有效資料夾路徑、相對路徑 (例如 `.\MyUpdates`) 或 UNC 共用。 根據預設，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式會搜尋 [!INCLUDE[msCoName](../../includes/msconame-md.md)] Update 或透過 Windows Server Update Services 搜尋 Windows Update Service。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式控制|/CONFIGURATIONFILE<br /><br /> **選擇性**|指定要使用的 [ConfigurationFile](./install-sql-server-using-a-configuration-file.md) 。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式控制|/ERRORREPORTING<br /><br /> **選擇性**|在 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 及更新版本中沒有任何作用。 <br/><br/> 若要管理傳送錯誤意見反應給 Microsoft 的方式，請參閱[如何設定 SQL Server 2016 和更新版本以將意見反應傳送給 Microsoft](https://support.microsoft.com/kb/3153756)。 <br/><br/>在較舊版本中，這會指定 SQL Server 的錯誤報告。<br /><br /> 如需詳細資訊，請參閱＜ [Microsoft 錯誤報告服務隱私權聲明](../../sql-server/sql-server-privacy.md)＞。<br /><br /> 支援的值：<br /><br /> 0=停用<br /><br /> 1=啟用|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式控制|/FEATURES<br /><br /> - 或 -<br /><br /> /ROLE<br /><br /> **必要**|指定要安裝的元件。<br /><br /> 選擇 **/FEATURES** 來指定要安裝的個別 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 元件。 如需詳細資訊，請參閱＜ [功能參數](#Feature) ＞。<br /><br /> 選擇 **/ROLE** 來指定安裝程式角色。 安裝程式角色會使用預先決定的組態安裝 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式控制|/HELP、?<br /><br /> **選擇性**|顯示安裝參數的使用方式選項。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式控制|/INDICATEPROGRESS<br /><br /> **選擇性**|指定要將詳細安裝程式記錄檔送到主控台。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式控制|/INSTALLSHAREDDIR<br /><br /> **選擇性**|指定 64 位元共用元件的非預設安裝目錄。<br /><br /> 預設為 `%Program Files%\Microsoft SQL Server`<br /><br /> 無法設定為 `%Program Files(x86)%\Microsoft SQL Server`|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式控制|/INSTALLSHAREDWOWDIR<br /><br /> **選擇性**|指定 32 位元共用元件的非預設安裝目錄。 只有 64 位元系統才支援。<br /><br /> 預設為 `%Program Files(x86)%\Microsoft SQL Server`<br /><br /> 無法設定為 `%Program Files%\Microsoft SQL Server`|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式控制|/ INSTANCEDIR<br /><br /> **選擇性**|指定執行個體特有元件的非預設安裝目錄。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式控制|/INSTANCEID<br /><br /> **選擇性**|指定 [InstanceID](#InstanceID)的非預設值。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式控制|/INSTANCENAME<br /><br /> **必要**|指定 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體名稱。<br /><br /> 如需詳細資訊，請參閱＜ [Instance Configuration](../../sql-server/install/instance-configuration.md)＞。|  
|PolyBase|/PBENGSVCACCOUNT<br /><br /> **選擇性**|指定引擎服務的帳戶。 預設是 **NT Authority\NETWORK SERVICE**。|  
|PolyBase|/PBDMSSVCPASSWORD<br /><br /> **選擇性**|指定引擎服務帳戶的密碼。 使用受控服務帳戶、虛擬帳戶或內建帳戶時，可以省略此參數。|  
|PolyBase|/PBENGSVCSTARTUPTYPE<br /><br /> **選擇性**|指定 PolyBase 引擎服務的啟動模式：自動 (預設)、停用及手動。|  
|PolyBase|/PBPORTRANGE<br /><br /> **選擇性**|為 PolyBase 服務指定含至少 6 個連接埠的連接埠範圍。 範例：<br /><br /> `/PBPORTRANGE=16450-16460`|  
|PolyBase|/PBSCALEOUT<br /><br /> **選擇性**|指定 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 執行個體是否會用作 PolyBase 向外延展計算群組的一部分。 如果您要設定 PolyBase 向外延展計算群組 (包括前端節點)，請使用此選項。 支援的值：**True**、**False**|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式控制|/PID<br /><br /> **選擇性**|指定 SQL Server 版的產品金鑰。 如果沒有指定這個參數，就會使用 Evaluation。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式控制|/Q 或 /QUIET<br /><br /> **選擇性**|指定安裝程式會在不含任何使用者介面的無訊息模式中執行。 這是自動安裝所使用的參數。 /Q 參數會覆寫 /QS 參數的輸入。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式控制|/QS 或 /QUIETSIMPLE <br /><br /> **選擇性**|指定安裝程式會執行並透過 UI 顯示進度，但是不接受任何輸入或顯示任何錯誤訊息。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式控制|/UIMODE<br /><br /> **選擇性**|指定在安裝期間是否只要顯示最少數目的對話方塊。<br /><br /> **/UIMode** 僅能搭配 **/ACTION=INSTALL** 和 **UPGRADE** 參數使用。 支援的值：<br /><br /> **/UIMODE=Normal** 是非 Express 版本的預設，而且會針對選取的功能顯示所有安裝對話方塊。<br /><br /> **/UIMODE=AutoAdvance** 是 Express 版本的預設，而且會略過不重要的對話方塊。<br /><br /> <br /><br /> 請注意，與其他參數結合時， **UIMODE** 會遭到覆寫。 例如，當同時提供 **/UIMODE=AutoAdvance** 和 **/ADDCURRENTUSERASSQLADMIN=FALSE** 時，不會以目前的使用者自動擴展提供對話方塊。<br /><br /> **UIMode** 設定無法搭配 **/Q** 或 **/QS** 參數使用。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式控制|/SQMREPORTING<br /><br /> **選擇性**|在 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 中沒有任何作用。 <br/><br/>若要管理傳送錯誤意見反應給 Microsoft 的方式，請參閱[如何設定 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)]，將意見反應傳送給 Microsoft](https://support.microsoft.com/kb/3153756)。 <br/><br/>在較舊版本中，這會指定 SQL Server 的功能使用狀況報告。<br /><br />支援的值：<br /><br /> 0=停用<br /><br /> 1=啟用|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式控制|/HIDECONSOLE<br /><br /> **選擇性**|指定要隱藏或關閉主控台視窗。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent|/AGTSVCACCOUNT<br /><br /> **必要**|指定 SQL Server Agent 服務的帳戶。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent|/AGTSVCPASSWORD<br /><br /> [必要](#Accounts)|指定 SQL Server Agent 服務帳戶的密碼。 使用受控服務帳戶、虛擬帳戶或內建帳戶時，可以省略此參數。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent|/AGTSVCSTARTUPTYPE<br /><br /> **選擇性**|指定 SQL Server Agent 服務的 [啟動](#Accounts) 帳戶。<br /><br /> 支援的值：<br /><br /> **自動**<br /><br /> **Disabled**<br /><br /> **手動**|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/ASBACKUPDIR<br /><br /> **選擇性**|指定 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] 備份檔案的目錄。 預設值：<br /><br /> 64 位元上的 WOW 模式：`%Program Files(x86)%\Microsoft SQL Server\<INSTANCEDIR>\<ASInstanceID>\OLAP\Backup`<br /><br /> 所有其他安裝：`%Program Files%\Microsoft SQL Server\<INSTANCEDIR>\<ASInstanceID>\OLAP\Backup`|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/ASCOLLATION<br /><br /> **選擇性**|指定 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]的定序設定。<br /><br /> 預設值：**Latin1_General_CI_AS**|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/ASCONFIGDIR<br /><br /> **選擇性**|指定 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] 組態檔的目錄。 預設值：<br /><br /> 64 位元上的 WOW 模式：`%Program Files(x86)%\Microsoft SQL Server\<INSTANCEDIR>\<ASInstanceID>\OLAP\Config`<br /><br /> 所有其他安裝：`%Program Files%\Microsoft SQL Server\<INSTANCEDIR>\<ASInstanceID>\OLAP\Config`|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/ASDATADIR<br /><br /> **選擇性**|指定 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] 資料檔案的目錄。 預設值：<br /><br /> 64 位元上的 WOW 模式：`%Program Files(x86)%\Microsoft SQL Server\<INSTANCEDIR>\<ASInstanceID>\OLAP\Data`<br /><br /> 所有其他安裝：`%Program Files%\Microsoft SQL Server\<INSTANCEDIR>\<ASInstanceID>\OLAP\Data`|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/ASLOGDIR<br /><br /> **選擇性**|指定 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] 記錄檔的目錄。 預設值：<br /><br /> 64 位元上的 WOW 模式：`%Program Files(x86)%\Microsoft SQL Server\<INSTANCEDIR>\<ASInstanceID>\OLAP\Log`<br /><br /> 所有其他安裝：`%Program Files%\Microsoft SQL Server\<INSTANCEDIR>\<ASInstanceID>\OLAP\Log`|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/ASSERVERMODE<br /><br /> **選擇性**|指定 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] 執行個體的伺服器模式。 有效的值為 MULTIDIMENSIONAL、POWERPIVOT 或 TABULAR。 **ASSERVERMODE** 區分大小寫。 所有值都必須以大寫形式表示。 如需有關有效值的詳細資訊，請參閱 [安裝 Analysis Services](/analysis-services/instances/install-windows/install-analysis-services)。|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/ASSVCACCOUNT<br /><br /> **必要**|指定 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] 服務的帳戶。|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/ASSVCPASSWORD<br /><br /> [必要](#Accounts)|指定 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] 服務的密碼。 使用受控服務帳戶、虛擬帳戶或內建帳戶時，可以省略此參數。|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/ASSVCSTARTUPTYPE<br /><br /> **選擇性**|指定 [服務的](#Accounts) 啟動 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] 模式。 支援的值：<br /><br /> **自動**<br /><br /> **Disabled**<br /><br /> **手動**|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/ASSYSADMINACCOUNTS<br /><br /> **必要**|指定 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]的系統管理員認證。|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/ASTEMPDIR<br /><br /> **選擇性**|指定 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] 暫存檔的目錄。 預設值：<br /><br /> 64 位元上的 WOW 模式：`%Program Files(x86)%\Microsoft SQL Server \<INSTANCEDIR>\<ASInstanceID>\OLAP\Temp`<br /><br /> 所有其他安裝：`%Program Files%\Microsoft SQL Server\<INSTANCEDIR>\<ASInstanceID>\OLAP\Temp`|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/ASPROVIDERMSOLAP<br /><br /> **選擇性**|指定 MSOLAP 提供者是否可以在處理序中執行。<br /><br /> 預設值：1=啟用|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/FARMACCOUNT<br /><br /> **SPI_AS_NewFarm 的必要參數**|指定執行中 SharePoint 管理中心服務與伺服陣列中其他必要服務的網域使用者帳戶。<br /><br /> 此參數僅用於透過 /ROLE = SPI_AS_NEWFARM 安裝的 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] 執行個體。|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/FARMPASSWORD<br /><br /> **SPI_AS_NewFarm 的必要參數**|指定伺服陣列帳戶的密碼。 使用受控服務帳戶、虛擬帳戶或內建帳戶時，可以省略此參數。|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/PASSPHRASE<br /><br /> **SPI_AS_NewFarm 的必要參數**|指定用來將其他應用程式伺服器或前端網頁伺服器新增至 SharePoint 伺服器陣列的複雜密碼。<br /><br /> 此參數僅用於透過 /ROLE = SPI_AS_NEWFARM 安裝的 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] 執行個體。|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/FARMADMINIPORT<br /><br /> **SPI_AS_NewFarm 的必要參數**|指定用來連接至 SharePoint 管理中心 Web 應用程式的通訊埠。<br /><br /> 此參數僅用於透過 /ROLE = SPI_AS_NEWFARM 安裝的 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] 執行個體。|  
|SQL Server Browser|/BROWSERSVCSTARTUPTYPE<br /><br /> **選擇性**|指定 SQL Server Browser 服務的 [啟動](#Accounts) 模式。 支援的值：<br /><br /> **自動**<br /><br /> **Disabled**<br /><br /> **手動**|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/ENABLERANU<br /><br /> **選擇性**|啟用 [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)] 安裝的執行身分認證。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/INSTALLSQLDATADIR<br /><br /> **選擇性**|指定 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 資料檔案的資料目錄。 預設值：<br /><br /> 64 位元上的 WOW 模式：`%Program Files(x86)%\Microsoft SQL Server\`<br /><br /> 所有其他安裝：`%Program Files%\Microsoft SQL Server\`|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SAPWD<br /><br /> **當 /SECURITYMODE=SQL 時則為必要參數**|指定 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] **SA** 帳戶的密碼。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SECURITYMODE<br /><br /> **選擇性**|指定 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的安全性模式。<br /><br /> 如果沒有提供這個參數，就會支援僅限 Windows 驗證模式。<br /><br /> 支援的值：**SQL**|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLBACKUPDIR<br /><br /> **選擇性**|指定備份檔的目錄。<br /><br /> 預設值：`<InstallSQLDataDir>\<SQLInstanceID>\MSSQL\Backup`|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLCOLLATION<br /><br /> **選擇性**|指定 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]的定序設定。<br /><br /> 預設安裝設定會由作業系統 (OS) 的地區設定決定。 您可以在安裝期間變更伺服器層級的定序，也可以在安裝之前變更 OS 地區設定。 預設定序會設定為與每個特定地區設定建立關聯的最舊可用版本。 這樣做是為了與舊版相容。 因此，此定序不一定是建議的定序。 若要完全利用 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的功能，請變更預設安裝設定以使用 Windows 定序。 例如，針對 OS 地區設定 [英文 (美國)] (字碼頁 1252)，安裝期間的預設定序是 **SQL_Latin1_General_CP1_CI_AS**，且可以變更為與其最接近的 Windows 定序對應項目 **Latin1_General_100_CI_AS_SC**。 <br /><br />如需詳細資訊，請參閱[定序與 Unicode 支援](../../relational-databases/collations/collation-and-unicode-support.md)。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/ADDCURRENTUSERASSQLADMIN<br /><br /> **選擇性**|將目前的使用者加入至 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] **sysadmin** 固定伺服器角色。 安裝 Express 版本或使用 /Role=ALLFeatures_WithDefaults is used 時，可以使用 /ADDCURRENTUSERASSQLADMIN 參數。 如需詳細資訊，請參閱以下的 /ROLE。<br /><br /> /ADDCURRENTUSERASSQLADMIN 的使用是選擇性的，但使用 /ADDCURRENTUSERASSQLADMIN 或 /SQLSYSADMINACCOUNTS 則是必要的。 預設值：<br /><br /> **True** True [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)]<br /><br /> 對於其他所有版本為 **False**|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLSVCACCOUNT<br /><br /> **必要**|指定 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 服務的啟動帳戶。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLSVCPASSWORD<br /><br /> [必要](#Accounts)|指定 SQLSVCACCOUNT 的密碼。 使用受控服務帳戶、虛擬帳戶或內建帳戶時，可以省略此參數。 |  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLSVCSTARTUPTYPE<br /><br /> **選擇性**|指定 [服務的](#Accounts) 啟動 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 模式。 支援的值：<br /><br /> **自動**<br /><br /> **Disabled**<br /><br /> **手動**|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLSYSADMINACCOUNTS<br /><br /> **必要**|您可以使用這個參數來提供登入，以便成為系統管理員 (sysadmin) 角色的成員。<br /><br /> 對於 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 之外的 [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)] 版本，需要 /SQLSYSADMINACCOUNTS。 對於 [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)]的版本，/SQLSYSADMINACCOUNTS 的使用是選擇性的，但使用 /SQLSYSADMINACCOUNTS 或 /ADDCURRENTUSERASSQLADMIN 則是必要的。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLTEMPDBDIR<br /><br /> **選擇性**|指定 tempdb 資料檔案的目錄。 指定多個目錄時，請以空格隔開這些目錄。 若指定多個目錄，tempdb 資料檔案將以循環配置資源方式跨多個目錄存放。<br /><br /> 預設值：`<InstallSQLDataDir>\<SQLInstanceID>\MSSQL\Data` (系統資料目錄)<br /><br /> **注意：** 此參數也會新增至 RebuildDatabase 案例。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLTEMPDBLOGDIR<br /><br /> **選擇性**|指定 tempdb 記錄檔的目錄。<br /><br /> 預設值：`<InstallSQLDataDir>\<SQLInstanceID>\MSSQL\Data` (系統資料目錄)<br /><br /> 注意:此參數也會新增至 RebuildDatabase 案例。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLTEMPDBFILECOUNT<br /><br /> **選擇性**|指定安裝程式要新增的 tempdb 資料檔案數目。 此值可以增加到與核心數目相同為止。 預設值：<br /><br /> [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)] 為 1 個<br /><br /> 所有其他版本為 8 個或與核心數目相同 (兩者取其較低者)<br /><br /> **重要：** tempdb 的主要資料庫檔案仍為 tempdb.mdf。 其他的 tempdb 檔案會命名為 tempdb_mssql_#.ndf，其中 # 代表唯一的數字，該數字是在安裝期間替每個額外的 tempdb 資料庫檔案所建立。 此命名慣例旨在使其為唯一。 解除安裝 SQL Server 的執行個體會刪除命名慣例為 tempdb_mssql_ #.ndf 的檔案。 請勿將 tempdb_mssql_\*.ndf 命名慣例用於使用者資料庫檔案。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLTEMPDBFILESIZE<br /><br /> **選擇性**|在 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 中引進。 指定每個 tempdb 資料檔案的初始大小。<br/><br/>預設值 = [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)] 為 4 MB，所有其他版本為 8 MB。<br/><br/>最小值= (4 MB 或 8 MB)。<br/><br/>最大值 = 1024 MB ([!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 為 262,144 MB)。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLTEMPDBFILEGROWTH<br /><br /> **選擇性**|指定每個 tempdb 資料檔案的檔案成長增量 (MB)。 0 值指出自動成長是關閉的，且不允許其他空間。 安裝程式允許的大小上限為 1024。<br /><br /> 預設值：64。 允許的範圍：最小值 = 0，最大值 = 1024|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLTEMPDBLOGFILESIZE<br /><br /> **選擇性**|在 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 中引進。 指定每個 tempdb 記錄檔的初始大小。<br/><br/>預設值 = [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)] 為 4 MB，所有其他版本為 8 MB。<br/><br/>最小值= (4 MB 或 8 MB)。<br/><br/>最大值 = 1024 MB ([!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 為 262,144 MB)。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLTEMPDBLOGFILEGROWTH<br /><br /> **選擇性**|指定每個 tempdb 資料檔案的檔案成長增量 (MB)。 0 值指出自動成長是關閉的，且不允許其他空間。 安裝程式允許的大小上限為 1024。<br /><br /> 預設值：64。 允許的範圍：最小值 = 0，最大值 = 1024|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLUSERDBDIR<br /><br /> **選擇性**|指定使用者資料庫之資料檔案的目錄。<br /><br /> 預設值：`<InstallSQLDataDir>\<SQLInstanceID>\MSSQL\Data`|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLSVCINSTANTFILEINIT<br /><br /> **選擇性**|為 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 服務帳戶啟用檔案立即初始化。 如需安全性和效能考量，請參閱 [資料庫立即檔案初始化](../../relational-databases/databases/database-instant-file-initialization.md)。<br /><br /> 預設值："False"<br /><br /> 選擇性的值："True"|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLUSERDBLOGDIR<br /><br /> **選擇性**|指定使用者資料庫之記錄檔的目錄。<br /><br /> 預設值：`<InstallSQLDataDir>\<SQLInstanceID>\MSSQL\Data`|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLMAXDOP=parameter <br /><br /> **選擇性** <br /> 若在自動 (無訊息) 安裝時省略，MAXDOP 會配合[平行處理原則的最大程度指導方針](../../database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option.md#Guidelines)。 |指定平行處理原則的最大程度，這會決定在執行單一陳述式期間可以利用的處理器數目。 僅自 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 開始提供。 <br /><br /> 預設值會配合[平行處理原則的最大程度指導方針](../../database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option.md#Guidelines)|
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/USESQLRECOMMENDEDMEMORYLIMITS<br /><br /> **選擇性** <br /> 若在自動 (無訊息) 安裝時省略 /USESQLRECOMMENDEDMEMORYLIMITS、/SQLMINMEMORY 與 /SQLMAXMEMORY，則 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 會使用預設的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 記憶體設定。|指定 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 將使用與獨立 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體之[伺服器記憶體設定指導方針](../../database-engine/configure-windows/server-memory-server-configuration-options.md#manually)一致的計算建議值。 僅自 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 開始提供。<br /><br /> **注意：** 此參數無法搭配 /SQLMINMEMORY 和 /SQLMAXMEMORY 使用。 |  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLMINMEMORY<br /><br /> **選擇性** <br /> 若在自動 (無訊息) 安裝時省略 /USESQLRECOMMENDEDMEMORYLIMITS、/SQLMINMEMORY 與 /SQLMAXMEMORY，則 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 會使用預設的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 記憶體設定。|指定最小的伺服器記憶體設定 (以 MB 為單位)。 僅自 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 開始提供。<br /><br /> 預設值：0.<br /><br /> **注意：** 這個參數無法搭配 /USESQLRECOMMENDEDMEMORYLIMITS 使用。 |  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLMAXMEMORY<br /><br /> **選擇性** <br /> 若在自動 (無訊息) 安裝時省略 /USESQLRECOMMENDEDMEMORYLIMITS、/SQLMINMEMORY 與 /SQLMAXMEMORY，則 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 會使用預設的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 記憶體設定。|指定最大的伺服器記憶體設定 (以 MB 為單位)。 僅自 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 開始提供。<br /><br /> 預設值：計算的建議值，與獨立 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體的[伺服器記憶體設定指導方針](../../database-engine/configure-windows/server-memory-server-configuration-options.md#manually)一致。<br /><br /> **注意：** 這個參數無法搭配 /USESQLRECOMMENDEDMEMORYLIMITS 使用。 |  
|FILESTREAM|/FILESTREAMLEVEL<br /><br /> **選擇性**|指定 FILESTREAM 功能的存取層級。 支援的值：<br /><br /> 0=針對這個執行個體停用 FILESTREAM 支援 (預設值)。<br /><br /> 1=針對 [!INCLUDE[tsql](../../includes/tsql-md.md)] 存取啟用 FILESTREAM。<br /><br /> 2=針對 [!INCLUDE[tsql](../../includes/tsql-md.md)] 和檔案 I/O 資料流存取啟用 FILESTREAM (不適用於叢集狀況)。<br /><br /> 3=允許遠端用戶端具有 FILESTREAM 資料的資料流存取權。|  
|FILESTREAM|/FILESTREAMSHARENAME<br /><br /> **選擇性**<br /><br /> **當 FILESTREAMLEVEL 大於 1 時則為必要參數。**|指定即將儲存 FILESTREAM 資料之 Windows 共用的名稱。|  
|SQL Server 全文檢索|/FTSVCACCOUNT<br /><br /> **選擇性**|指定全文檢索篩選啟動器服務的帳戶。<br /><br /> 在 [!INCLUDE[firstref_longhorn](../../includes/firstref-longhorn-md.md)] 或更新版本中會忽略此參數。 ServiceSID 是用來協助保護 SQL Server 與全文檢索篩選背景程式之間的通訊。 如果沒有提供這些值，就會停用全文檢索篩選啟動器服務。 您必須使用 SQL Server 控制管理員來變更服務帳戶並啟用全文檢索功能。<br /><br /> 預設值：本機服務帳戶|  
|SQL Server 全文檢索|/FTSVCPASSWORD<br /><br /> **選擇性**|指定全文檢索篩選啟動器服務的密碼。<br /><br /> 在 [!INCLUDE[firstref_longhorn](../../includes/firstref-longhorn-md.md)] 或更新版本中會忽略此參數。 使用受控服務帳戶、虛擬帳戶或內建帳戶時，可以省略此參數。|  
|[!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)]|/ISSVCACCOUNT<br /><br /> **必要**|指定 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)]的帳戶。<br /><br /> 預設值：NT AUTHORITY\NETWORK SERVICE|  
|[!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)]|/ISSVCPASSWORD<br /><br /> [必要](#Accounts)|指定 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 密碼。 使用受控服務帳戶、虛擬帳戶或內建帳戶時，可以省略此參數。|  
|[!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)]|/ISSVCStartupType<br /><br /> **選擇性**|指定 [服務的](#Accounts) 啟動 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 模式。|  
|SQL Server 網路組態|/NPENABLED<br /><br /> **選擇性**|指定 SQL Server 服務的具名管道通訊協定狀態。 支援的值：<br /><br /> 0=停用具名管道通訊協定<br /><br /> 1=啟用具名管道通訊協定|  
|SQL Server 網路組態|/TCPENABLED<br /><br /> **選擇性**|指定 SQL Server 服務的 TCP 通訊協定狀態。 支援的值：<br /><br /> 0=停用 TCP 通訊協定<br /><br /> 1=啟用 TCP 通訊協定|  
|[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]|/RSINSTALLMODE<br /><br /> **選擇性**| 從 SQL Server 2017 開始不再適用。  指定 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]的安裝模式。 支援的值：<br /><br /> **SharePointFilesOnlyMode**<br /><br /> **DefaultNativeMode**<br /><br /> **FilesOnlyMode**<br /><br /> <br /><br /> 注意:如果安裝包含 SQL Server[!INCLUDE[ssDE](../../includes/ssde-md.md)]，則預設 RSINSTALLMODE 為 DefaultNativeMode。<br /><br /> 注意：如果安裝不包含 SQL Server[!INCLUDE[ssDE](../../includes/ssde-md.md)]，則預設 RSINSTALLMODE 為 FilesOnlyMode。<br /><br /> 如果您選擇 DefaultNativeMode，但是安裝不包含 SQL Server[!INCLUDE[ssDE](../../includes/ssde-md.md)]，則安裝作業會自動將 RSINSTALLMODE 變更為 FilesOnlyMode。|  
|[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]|/RSSVCACCOUNT<br /><br /> **必要**| 從 SQL Server 2017 開始不再適用。  指定 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]的啟動帳戶。 |  
|[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]|/RSSVCPASSWORD<br /><br /> [必要](#Accounts)|從 SQL Server 2017 開始不再適用。  指定 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] 服務之啟動帳戶的密碼。 使用受控服務帳戶、虛擬帳戶或內建帳戶時，可以省略此參數。|  
|[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]|/RSSVCStartupType<br /><br /> **選擇性**|從 SQL Server 2017 開始不再適用。  指定 [的](#Accounts) 啟動 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]模式。 支援的值：<br /><br /> **自動**<br /><br /> **Disabled**<br /><br /> **手動**|  
|Python/機器學習服務 (資料庫內)|/MPYCACHEDIRECTORY|保留供未來使用。 請使用 %TEMP% 來儲存 Python .CAB 檔案，以便在沒有網際網路連線的電腦上進行安裝。 |  
|R/機器學習服務 (資料庫內)|/MRCACHEDIRECTORY|使用此參數來指定 SQL Server 機器學習服務或 Machine Learning Server (獨立式) 中 Microsoft R Open、SQL Server 2016 R Services、SQL Server 2016 R Server (獨立式) 或 R 功能支援的快取目錄。 此設定通常用於[在電腦上從命令列安裝 R 元件，而沒有網際網路存取](../../machine-learning/install/sql-ml-component-install-without-internet-access.md)時。|  
|Java/語言延伸模組| /SQL_INST_JAVA,<br /> /SQLJAVADIR = "path"<br /><br /> **選擇性** | 從 SQL Server 2019 開始，會指定使用語言延伸模組來安裝 Java。 如果在未使用 /SQLJAVADIR 參數的情況下提供 /SQL_INST_JAVA，系統會假設您希望安裝由安裝媒體提供的 Zulu Open JRE。 <br /><br /> 提供 /SQLJAVADIR 的路徑，表示您希望使用已安裝的 JRE 或 JDK。 |
  
###### <a name="sample-syntax"></a>範例語法：  
 與 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]、複寫和全文檢索搜尋元件一起安裝新的獨立執行個體，並為 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]啟用立即檔案初始化。 
  
```  
setup.exe /q /ACTION=Install /FEATURES=SQL /INSTANCENAME=MSSQLSERVER /SQLSVCACCOUNT="<DomainName\UserName>" /SQLSVCPASSWORD="<StrongPassword>" /SQLSYSADMINACCOUNTS="<DomainName\UserName>" /AGTSVCACCOUNT="NT AUTHORITY\Network Service" /SQLSVCINSTANTFILEINIT="True" /IACCEPTSQLSERVERLICENSETERMS  
```  
  
##  <a name="sysprep-parameters"></a><a name="SysPrep"></a> SysPrep 參數  
 如需有關 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] SysPrep 的詳細資訊，請參閱  
  
 [使用 SysPrep 安裝 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)]](../../database-engine/install-windows/install-sql-server-using-sysprep.md)。 
  
#### <a name="prepare-image-parameters"></a>準備圖像參數  
 使用下表中的參數開發命令列指令碼，以便準備 SQL Server 的執行個體而不必加以設定。 
  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 元件|參數|描述|  
|-----------------------------------------|---------------|-----------------|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式控制|/ACTION<br /><br /> **必要**|指出安裝工作流程的必要參數。<br /><br /> 支援的值：**PrepareImage**|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式控制|/IACCEPTSQLSERVERLICENSETERMS<br /><br /> **只有當您針對自動安裝指定了 /Q 或 /QS 參數時，才需要使用此參數。**|確認接受授權條款的必要參數。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式控制|/ENU<br /><br /> **選擇性**|當安裝媒體包含英文以及與作業系統對應之語言的語言套件時，使用此參數在當地語系化的作業系統上安裝英文版的 SQL Server。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式控制|/UpdateEnabled<br /><br /> **選擇性**|指定 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式是否應該探索及包含產品更新。 有效值為 True 和 False 或 1 和 0。 根據預設，SQL Server 安裝程式會包含找到的更新。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式控制|/UpdateSource<br /><br /> **選擇性**|指定 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式將取得產品更新的位置。 有效值為 "MU"，表示搜尋 [!INCLUDE[msCoName](../../includes/msconame-md.md)] 更新、有效資料夾路徑、相對路徑 (例如 `.\MyUpdates`) 或 UNC 共用。 根據預設，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式會搜尋 [!INCLUDE[msCoName](../../includes/msconame-md.md)] Update 或透過 Windows Server Update Services 搜尋 Windows Update Service。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式控制|/CONFIGURATIONFILE<br /><br /> **選擇性**|指定要使用的 [ConfigurationFile](./install-sql-server-using-a-configuration-file.md) 。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式控制|/FEATURES<br /><br /> **必要**|指定要安裝的 [元件](#Feature) 。<br /><br /> 支援的值為 SQLEngine、Replication、FullText、DQ、AS、AS_SPI、RS、RS_SHP、RS_SHPWFE、DQC、Conn、IS、BC、SDK、DREPLAY_CTLR、DREPLAY_CLT、SNAC_SDK、SQLODBC、SQLODBC_SDK、LocalDB、MDS、POLYBASE|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式控制|/HELP、?<br /><br /> **選擇性**|顯示安裝參數的使用方式選項。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式控制|/HIDECONSOLE<br /><br /> **選擇性**|指定要隱藏或關閉主控台視窗。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式控制|/INDICATEPROGRESS<br /><br /> **選擇性**|指定要將詳細安裝程式記錄檔送到主控台。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式控制|/INSTALLSHAREDDIR<br /><br /> **選擇性**|指定 64 位元共用元件的非預設安裝目錄。<br /><br /> 預設為 `%Program Files%\Microsoft SQL Server`<br /><br /> 無法設定為 `%Program Files(x86)%\Microsoft SQL Server`|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式控制|/ INSTANCEDIR<br /><br /> **選擇性**|指定執行個體特有元件的非預設安裝目錄。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式控制|/INSTANCEID<br /><br /> 在 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] SP1 CU2 (2013 年 1 月) 之前為 **必要項**<br /><br /> 從 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] SP1 CU2 開始，為執行個體功能的 **必要項**。|指定要準備之執行個體的 InstanceID。|  
|PolyBase|/PBENGSVCACCOUNT<br /><br /> **選擇性**|指定引擎服務的帳戶。 預設是 **NT Authority\NETWORK SERVICE**。|  
|PolyBase|/PBDMSSVCPASSWORD<br /><br /> **選擇性**|指定引擎服務帳戶的密碼。 使用受控服務帳戶、虛擬帳戶或內建帳戶時，可以省略此參數。|  
|PolyBase|/PBENGSVCSTARTUPTYPE<br /><br /> **選擇性**|指定 PolyBase 引擎服務的啟動模式：自動 (預設)、停用及手動。|  
|PolyBase|/PBPORTRANGE<br /><br /> **選擇性**|為 PolyBase 服務指定含至少 6 個連接埠的連接埠範圍。 範例：<br /><br /> `/PBPORTRANGE=16450-16460`|  
|PolyBase|/PBSCALEOUT<br /><br /> **選擇性**|指定 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 執行個體是否會用作 PolyBase 向外延展計算群組的一部分。 支援的值：**True**、**False**|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式控制|/Q 或 /QUIET <br /><br /> **選擇性**|指定安裝程式會在不含任何使用者介面的無訊息模式中執行。 這是自動安裝所使用的參數。 /Q 參數會覆寫 /QS 參數的輸入。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式控制|/QS 或 /QUIETSIMPLE<br /><br /> **選擇性**|指定安裝程式會執行並透過 UI 顯示進度，但是不接受任何輸入或顯示任何錯誤訊息。|  
  
###### <a name="sample-syntax"></a>範例語法：  
 與 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]、複寫、全文檢索搜尋元件和 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]一起準備新的獨立執行個體。 
  
```  
setup.exe /q /ACTION=PrepareImage /FEATURES=SQL,RS /InstanceID =<MYINST> /IACCEPTSQLSERVERLICENSETERMS  
```  
  
#### <a name="complete-image-parameters"></a>完成圖像參數  
 使用下表中的參數開發命令列指令碼，以便完成與設定準備好的 SQL Server 執行個體。 
  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 元件|參數|描述|  
|-----------------------------------------|---------------|-----------------|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式控制|/ACTION<br /><br /> **必要**|指出安裝工作流程的必要參數。<br /><br /> 支援的值：**CompleteImage**|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式控制|/IACCEPTSQLSERVERLICENSETERMS<br /><br /> **只有當您針對自動安裝指定了 /Q 或 /QS 參數時，才需要使用此參數。**|確認接受授權條款的必要參數。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式控制|/ENU<br /><br /> **選擇性**|當安裝媒體包含英文以及與作業系統對應之語言的語言套件時，使用此參數在當地語系化的作業系統上安裝英文版的 SQL Server。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式控制|/CONFIGURATIONFILE<br /><br /> **選擇性**|指定要使用的 [ConfigurationFile](./install-sql-server-using-a-configuration-file.md) 。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式控制|/ERRORREPORTING<br /><br /> **選擇性**|在 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 中沒有任何作用。 <br/><br/>若要管理傳送錯誤意見反應給 Microsoft 的方式，請參閱[如何設定 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)]，將意見反應傳送給 Microsoft](https://support.microsoft.com/kb/3153756)。 <br/><br/>在較舊版本中，這會指定 SQL Server 的錯誤報告。<br /><br /> 如需詳細資訊，請參閱＜ [Microsoft 錯誤報告服務隱私權聲明](../../sql-server/sql-server-privacy.md)＞。 支援的值：<br /><br /> 1=啟用<br /><br /> 0=停用|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式控制|/HELP、?<br /><br /> **選擇性**|顯示安裝參數的使用方式選項。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式控制|/INDICATEPROGRESS<br /><br /> **選擇性**|指定要將詳細安裝程式記錄檔送到主控台。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式控制|/INSTANCEID<br /><br /> 在 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] SP1 CU2 (2013 年 1 月) 之前為 **必要項**<br /><br /> 從 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] SP1 CU2 開始為 **選用項**|使用準備圖像步驟期間指定的執行個體識別碼。<br /><br /> 支援的值：準備之執行個體的 InstanceID。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式控制|/INSTANCENAME<br /><br /> 在 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] SP1 CU2 (2013 年 1 月) 之前為 **必要項**<br /><br /> 從 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] SP1 CU2 開始為 **選用項**|指定要完成之執行個體的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體名稱。<br /><br /> 如需詳細資訊，請參閱＜ [Instance Configuration](../../sql-server/install/instance-configuration.md)＞。|  
|PolyBase|/PBENGSVCACCOUNT<br /><br /> **選擇性**|指定引擎服務的帳戶。 預設是 **NT Authority\NETWORK SERVICE**。|  
|PolyBase|/PBDMSSVCPASSWORD<br /><br /> **選擇性**|指定引擎服務帳戶的密碼。 使用受控服務帳戶、虛擬帳戶或內建帳戶時，可以省略此參數。|  
|PolyBase|/PBENGSVCSTARTUPTYPE<br /><br /> **選擇性**|指定 PolyBase 引擎服務的啟動模式：自動 (預設)、停用及手動。|  
|PolyBase|/PBPORTRANGE<br /><br /> **選擇性**|為 PolyBase 服務指定含至少 6 個連接埠的連接埠範圍。 範例：<br /><br /> `/PBPORTRANGE=16450-16460`|  
|PolyBase|/PBSCALEOUT<br /><br /> **選擇性**|指定 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 執行個體是否會用作 PolyBase 向外延展計算群組的一部分。 支援的值：**True**、**False**|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式控制|/PID<br /><br /> **選擇性**|指定 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]版的產品金鑰。 如果沒有指定這個參數，就會使用 Evaluation。<br /><br /> **注意：** 如果您要安裝 [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)]、[!INCLUDE[ssExpress](../../includes/ssexpress-md.md)] with Tools 或 [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)] with Advanced Services，則會預先定義 PID。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式控制|/Q 或 /QUIET <br /><br /> **選擇性**|指定安裝程式會在不含任何使用者介面的無訊息模式中執行。 這是自動安裝所使用的參數。 /Q 參數會覆寫 /QS 參數的輸入。 |  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/QS 或 /QUIETSIMPLE<br /><br /> **選擇性**|指定安裝程式會執行並透過 UI 顯示進度，但是不接受任何輸入或顯示任何錯誤訊息。|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式控制|/SQMREPORTING<br /><br /> **選擇性**|在 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 中沒有任何作用。 <br/><br/>若要管理傳送錯誤意見反應給 Microsoft 的方式，請參閱[如何設定 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)]，將意見反應傳送給 Microsoft](https://support.microsoft.com/kb/3153756)。 <br/><br/>在較舊版本中，這會指定 SQL Server 的功能使用狀況報告。<br /><br />支援的值：<br /><br /> 0=停用<br /><br /> 1=啟用|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式控制|/HIDECONSOLE<br /><br /> **選擇性**|指定要隱藏或關閉主控台視窗。|  
|SQL Server Agent|/AGTSVCACCOUNT<br /><br /> **必要**|指定 SQL Server Agent 服務的帳戶。|  
|SQL Server Agent|/AGTSVCPASSWORD<br /><br /> [必要](#Accounts)|指定 SQL Server Agent 服務帳戶的密碼。 使用受控服務帳戶、虛擬帳戶或內建帳戶時，可以省略此參數。|  
|SQL Server Agent|/AGTSVCSTARTUPTYPE<br /><br /> **選擇性**|指定 SQL Server Agent 服務的 [啟動](#Accounts) 帳戶。 支援的值：<br /><br /> **自動**<br /><br /> **Disabled**<br /><br /> **手動**|  
|SQL Server Browser|/BROWSERSVCSTARTUPTYPE<br /><br /> **選擇性**|指定 SQL Server Browser 服務的 [啟動](#Accounts) 模式。 支援的值：<br /><br /> **自動**<br /><br /> **Disabled**<br /><br /> **手動**|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/ENABLERANU<br /><br /> **選擇性**|啟用 [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)] 安裝的執行身分認證。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/INSTALLSQLDATADIR<br /><br /> **選擇性**|指定 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 資料檔案的資料目錄。 預設值：<br /><br /> 64 位元上的 WOW 模式：`%Program Files(x86)%\Microsoft SQL Server\`<br /><br /> 所有其他安裝：`%Program Files%\Microsoft SQL Server\`|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SAPWD<br /><br /> **當 /SECURITYMODE=SQL 時則為必要參數**|指定 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] **SA** 帳戶的密碼。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SECURITYMODE<br /><br /> **選擇性**|指定 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的安全性模式。<br /><br /> 如果沒有提供這個參數，就會支援僅限 Windows 驗證模式。<br /><br /> 支援的值：**SQL**|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLBACKUPDIR<br /><br /> **選擇性**|指定備份檔的目錄。<br /><br /> 預設值：<br /><br /> `<InstallSQLDataDir>\<SQLInstanceID>\MSSQL\Backup`|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLCOLLATION<br /><br /> **選擇性**|指定 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]的定序設定。<br /><br /> 預設值會根據您 Windows 作業系統的地區設定而異。 如需詳細資訊，請參閱＜ [安裝程式中的定序設定](../../relational-databases/collations/collation-and-unicode-support.md)＞。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLSVCACCOUNT<br /><br /> **必要**|指定 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 服務的啟動帳戶。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLSVCPASSWORD<br /><br /> [必要](#Accounts)|指定 SQLSVCACCOUNT 的密碼。 使用受控服務帳戶、虛擬帳戶或內建帳戶時，可以省略此參數。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLSVCSTARTUPTYPE<br /><br /> **選擇性**|指定 [服務的](#Accounts) 啟動 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 模式。 支援的值：<br /><br /> **自動**<br /><br /> **Disabled**<br /><br /> **手動**|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLSYSADMINACCOUNTS<br /><br /> **必要**|您可以使用這個參數來提供登入，以便成為系統管理員 (sysadmin) 角色的成員。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLTEMPDBDIR<br /><br /> **選擇性**|指定 tempdb 資料檔案的目錄。 指定多個目錄時，請以空格隔開這些目錄。 若指定多個目錄，tempdb 資料檔案將以循環配置資源方式跨多個目錄存放。<br /><br /> 預設值：`<InstallSQLDataDir>\<SQLInstanceID>\MSSQL\Data` (系統資料目錄)<br /><br /> 注意：此參數也會新增至 RebuildDatabase 案例。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLTEMPDBLOGDIR<br /><br /> **選擇性**|指定 tempdb 記錄檔的目錄。<br /><br /> 預設值：`<InstallSQLDataDir>\<SQLInstanceID>\MSSQL\Data` (系統資料目錄)<br /><br /> 注意:此參數也會新增至 RebuildDatabase 案例。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLTEMPDBFILESIZE<br /><br /> **選擇性**|在 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 中引進。 指定每個 tempdb 資料檔案的初始大小。<br/><br/>預設值 = [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)] 為 4 MB，所有其他版本為 8 MB。<br/><br/>最小值= (4 MB 或 8 MB)。<br/><br/>最大值 = 1024 MB ([!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 為 262,144 MB)。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLTEMPDBFILEGROWTH<br /><br /> **選擇性**|指定每個 tempdb 資料檔案的檔案成長增量 (MB)。 0 值指出自動成長是關閉的，且不允許其他空間。 安裝程式允許的大小上限為 1024。<br /><br /> 預設值：64<br /><br /> 允許的範圍：最小值 = 0，最大值 = 1024|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLTEMPDBLOGFILESIZE<br /><br /> **選擇性**|指定 tempdb 記錄檔的初始大小 (MB)。 安裝程式允許的大小上限為 1024。<br /><br /> 預設值：<br /><br /> [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)] 為 4 個<br /><br /> 所有其他版本為 8 個<br /><br /> 允許的範圍：最小值 = 預設值 (4 或 8)，最大值 = 1024|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLTEMPDBLOGFILEGROWTH<br /><br /> **選擇性**|在 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 中引進。 指定每個 tempdb 記錄檔的初始大小。<br/><br/>預設值 = [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)] 為 4 MB，所有其他版本為 8 MB。<br/><br/>最小值= (4 MB 或 8 MB)。<br/><br/>最大值 = 1024 MB ([!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 為 262,144 MB)。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLTEMPDBFILECOUNT<br /><br /> **選擇性**|指定安裝程式要新增的 tempdb 資料檔案數目。 此值可以增加到與核心數目相同為止。 預設值：<br /><br /> [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)] 為 1 個<br /><br /> 所有其他版本為 8 個或與核心數目相同 (兩者取其較低者)<br /><br /> **重要：** tempdb 的主要資料庫檔案仍為 tempdb.mdf。 其他的 tempdb 檔案會命名為 tempdb_mssql_#.ndf，其中 # 代表唯一的數字，該數字是在安裝期間替每個額外的 tempdb 資料庫檔案所建立。 此命名慣例旨在使其為唯一。 解除安裝 SQL Server 的執行個體會刪除命名慣例為 tempdb_mssql_ #.ndf 的檔案。 請勿將 tempdb_mssql_\*.ndf 命名慣例用於使用者資料庫檔案。<br /><br /> **警告：** [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)] 不支援設定此參數。 安裝程式只會安裝 1 個 tempdb 資料檔案。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLUSERDBDIR<br /><br /> **選擇性**|指定使用者資料庫之資料檔案的目錄。<br /><br /> 預設值：`<InstallSQLDataDir>\<SQLInstanceID>\MSSQL\Data`|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLUSERDBLOGDIR<br /><br /> **選擇性**|指定使用者資料庫之記錄檔的目錄。<br /><br /> 預設值：`<InstallSQLDataDir>\<SQLInstanceID>\MSSQL\Data`|  
|FILESTREAM|/FILESTREAMLEVEL<br /><br /> **選擇性**|指定 FILESTREAM 功能的存取層級。 支援的值：<br /><br /> 0=針對這個執行個體停用 FILESTREAM 支援 (預設值)。<br /><br /> 1=針對 [!INCLUDE[tsql](../../includes/tsql-md.md)] 存取啟用 FILESTREAM。<br /><br /> 2=針對 [!INCLUDE[tsql](../../includes/tsql-md.md)] 和檔案 I/O 資料流存取啟用 FILESTREAM (不適用於叢集狀況)。<br /><br /> 3=允許遠端用戶端具有 FILESTREAM 資料的資料流存取權。|  
|FILESTREAM|/FILESTREAMSHARENAME<br /><br /> **選擇性**<br /><br /> **當 FILESTREAMLEVEL 大於 1 時則為必要參數。**|指定即將儲存 FILESTREAM 資料之 Windows 共用的名稱。|  
|SQL Server 全文檢索|/FTSVCACCOUNT<br /><br /> **選擇性**|指定全文檢索篩選啟動器服務的帳戶。<br /><br /> 在 [!INCLUDE[firstref_longhorn](../../includes/firstref-longhorn-md.md)] 或更新版本中會忽略此參數。 ServiceSID 是用來協助保護 SQL Server 與全文檢索篩選背景程式之間的通訊。 如果沒有提供這些值，就會停用全文檢索篩選啟動器服務。 您必須使用 SQL Server 控制管理員來變更服務帳戶並啟用全文檢索功能。<br /><br /> 預設值：本機服務帳戶|  
|SQL Server 全文檢索|/FTSVCPASSWORD<br /><br /> **選擇性**|指定全文檢索篩選啟動器服務的密碼。<br /><br /> 在 [!INCLUDE[firstref_longhorn](../../includes/firstref-longhorn-md.md)] 或更新版本中會忽略此參數。 使用受控服務帳戶、虛擬帳戶或內建帳戶時，可以省略此參數。 |  
|SQL Server 網路組態|/NPENABLED<br /><br /> **選擇性**|指定 SQL Server 服務的具名管道通訊協定狀態。 支援的值：<br /><br /> 0=停用具名管道通訊協定<br /><br /> 1=啟用具名管道通訊協定|  
|SQL Server 網路組態|/TCPENABLED<br /><br /> **選擇性**|指定 SQL Server 服務的 TCP 通訊協定狀態。 支援的值：<br /><br /> 0=停用 TCP 通訊協定<br /><br /> 1=啟用 TCP 通訊協定|  
|[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]|/RSINSTALLMODE<br /><br /> **選擇性**|從 SQL Server 2017 開始不再適用。  指定 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]的安裝模式。|  
|[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]|/RSSVCACCOUNT<br /><br /> **必要**|從 SQL Server 2017 開始不再適用。  指定 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]的啟動帳戶。|  
|[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]|/RSSVCPASSWORD<br /><br /> [必要](#Accounts)| 從 SQL Server 2017 開始不再適用。  指定 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] 服務之啟動帳戶的密碼。 使用受控服務帳戶、虛擬帳戶或內建帳戶時，可以省略此參數。|  
|[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]|/RSSVCStartupType<br /><br /> **選擇性**|從 SQL Server 2017 開始不再適用。  指定 [的](#Accounts) 啟動 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]模式。 支援的值：<br /><br /> **自動**<br /><br /> **Disabled**<br /><br /> **手動**|  
  
###### <a name="sample-syntax"></a>範例語法：  
 完成準備好並包含 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]、複寫和全文檢索搜尋元件的獨立執行個體。 
  
```  
setup.exe /q /ACTION=CompleteImage /INSTANCENAME=MYNEWINST /INSTANCEID=<MYINST> /SQLSVCACCOUNT="<DomainName\UserName>" /SQLSVCPASSWORD="<StrongPassword>" /SQLSYSADMINACCOUNTS="<DomainName\UserName>" /AGTSVCACCOUNT="NT AUTHORITY\Network Service" /IACCEPTSQLSERVERLICENSETERMS  
```  
  
##  <a name="upgrade-parameters"></a><a name="Upgrade"></a> 升級參數  
 您可以使用下表中的參數來開發升級的命令列指令碼。 
  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 元件|參數|描述|  
|-----------------------------------------|---------------|-----------------|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/ACTION<br /><br /> **必要**|指出安裝工作流程的必要參數。 支援的值：<br /><br /> **升級**<br /><br /> **EditionUpgrade**<br /><br /> <br /><br /> **EditionUpgrade** 值是用來將現有的 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 版本升級為不同的版本。 如需有關支援之版本與版別升級的詳細資訊，請參閱＜ [Supported Version and Edition Upgrades](../../database-engine/install-windows/supported-version-and-edition-upgrades.md)＞。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/IACCEPTSQLSERVERLICENSETERMS<br /><br /> **只有當您針對自動安裝指定了 /Q 或 /QS 參數時，才需要使用此參數。**|確認接受授權條款的必要參數。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/ENU<br /><br /> **選擇性**|當安裝媒體包含英文以及與作業系統對應之語言的語言套件時，使用此參數在當地語系化的作業系統上安裝英文版的 SQL Server。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/*UpdateEnabled*<br /><br /> **選擇性**|指定 SQL Server 安裝程式是否應探索及包含產品更新。 有效值為 True 和 False 或 1 和 0。 根據預設，SQL Server 安裝程式會包含找到的更新。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/*UpdateSource*<br /><br /> **選擇性**|指定 SQL Server 安裝程式將取得產品更新的位置。 有效值為 "MU"，表示搜尋 [!INCLUDE[msCoName](../../includes/msconame-md.md)] 更新、有效資料夾路徑、相對路徑 (例如 .\MyUpdates) 或 UNC 共用。 根據預設，SQL Server 安裝程式會搜尋 [!INCLUDE[msCoName](../../includes/msconame-md.md)] Update 或透過 Windows Server Update Services 搜尋 Windows Update Service。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/CONFIGURATIONFILE<br /><br /> **選擇性**|指定要使用的 [ConfigurationFile](./install-sql-server-using-a-configuration-file.md) 。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/ERRORREPORTING<br /><br /> **選擇性**|在 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 中沒有任何作用。 <br/><br/>若要管理傳送錯誤意見反應給 Microsoft 的方式，請參閱[如何設定 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)]，將意見反應傳送給 Microsoft](https://support.microsoft.com/kb/3153756)。 <br/><br/>在較舊版本中，這會指定 SQL Server 的錯誤報告。<br /><br /> 如需詳細資訊，請參閱＜ [Microsoft 錯誤報告服務隱私權聲明](../../sql-server/sql-server-privacy.md)＞。 支援的值：<br /><br /> 1=啟用<br /><br /> 0=停用|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/HELP、?<br /><br /> **選擇性**|顯示參數的使用方式選項。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/INDICATEPROGRESS<br /><br /> **選擇性**|指定要將詳細安裝程式記錄檔送到主控台。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/ INSTANCEDIR<br /><br /> **選擇性**|指定共用元件的非預設安裝目錄。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/INSTANCEID<br /><br /> **當您從 [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)]** 或更新版本升級時，此為必要項。<br /><br /> **當您從 [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] 升級時為選擇性參數**|指定 [InstanceID](#InstanceID)的非預設值。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/INSTANCENAME<br /><br /> **必要**|指定 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 執行個體名稱。<br /><br /> 如需詳細資訊，請參閱＜ [Instance Configuration](../../sql-server/install/instance-configuration.md)＞。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/PID<br /><br /> **選擇性**|指定 SQL Server 版的產品金鑰。 如果沒有指定這個參數，就會使用 Evaluation。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/Q<br /><br /> **選擇性**|指定安裝程式會在不含任何使用者介面的無訊息模式中執行。 這是自動安裝所使用的參數。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/UIMODE<br /><br /> **選擇性**|指定在安裝期間是否只要顯示最少數目的對話方塊。 <br />                **/UIMode** 僅能搭配 **/ACTION=INSTALL** 和 **UPGRADE** 參數使用。 支援的值：<br /><br /> **/UIMODE=Normal** 是非 Express 版本的預設，而且會針對選取的功能顯示所有安裝對話方塊。<br /><br /> **/UIMODE=AutoAdvance** 是 Express 版本的預設，而且會略過不重要的對話方塊。<br /><br /> 請注意， **UIMode** 設定無法搭配 **/Q** 或 **/QS** 參數使用。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/SQMREPORTING<br /><br /> **選擇性**|在 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 中沒有任何作用。 <br/><br/>若要管理傳送錯誤意見反應給 Microsoft 的方式，請參閱[如何設定 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)]，將意見反應傳送給 Microsoft](https://support.microsoft.com/kb/3153756)。 <br/><br/>在較舊版本中，這會指定 SQL Server 的功能使用狀況報告。<br /><br />支援的值：<br /><br /> 1=啟用<br /><br /> 0=停用|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/HIDECONSOLE<br /><br /> **選擇性**|指定要隱藏或關閉主控台視窗。|  
|SQL Server Browser 服務|/BROWSERSVCSTARTUPTYPE<br /><br /> **選擇性**|指定 SQL Server Browser 服務的 [啟動](#Accounts) 模式。 支援的值：<br /><br /> **自動**<br /><br /> **Disabled**<br /><br /> **手動**|  
|SQL Server 全文檢索|/FTUPGRADEOPTION<br /><br /> **選擇性**|指定全文檢索目錄升級選項。 支援的值：<br /><br /> **REBUILD**<br /><br /> **RESET**<br /><br /> **IMPORT**|  
|[!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)]|/ISSVCACCOUNT<br /><br /> **必要**|指定 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)]的帳戶。<br /><br /> 預設值：NT AUTHORITY\NETWORK SERVICE|  
|[!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)]|/ISSVCPASSWORD<br /><br /> [必要](#Accounts)|指定 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 密碼。 使用受控服務帳戶、虛擬帳戶或內建帳戶時，可以省略此參數。|  
|[!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)]|/ISSVCStartupType<br /><br /> **選擇性**|指定 [服務的](#Accounts) 啟動 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 模式。|  
|[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]|/RSUPGRADEDATABASEACCOUNT<br /><br /> **選擇性**|只有在升級 2008 R2 或更早版本的 SharePoint 模式報表伺服器時，才會使用此屬性。 針對使用舊版 SharePoint 模式架構 (此架構已在 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] 中變更) 的報表伺服器，會執行其他升級作業。 如果此選項未隨附於命令列安裝，則會使用舊報表伺服器執行個體的預設服務帳戶。 如果使用這個屬性，請使用 **/RSUPGRADEPASSWORD** 屬性提供此帳戶的密碼。|  
|[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]|/RSUPGRADEPASSWORD<br /><br /> **選擇性**|現有報表伺服器服務帳戶的密碼。 使用受控服務帳戶、虛擬帳戶或內建帳戶時，可以省略此參數。|  
|[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]|/ALLOWUPGRADEFORSSRSSHAREPOINTMODE|升級以 SharePoint 共用服務架構為基礎的 SharePoint 模式安裝時，需要切換。 升級非共用服務版本的 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]，則不需要切換。|  
  
###### <a name="sample-syntax"></a>範例語法：  
 從舊版 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 升級現有的執行個體或容錯移轉叢集節點  
  
```  
setup.exe /q /ACTION=upgrade /INSTANCEID = <INSTANCEID>/INSTANCENAME=MSSQLSERVER /RSUPGRADEDATABASEACCOUNT="<Provide a SQL Server logon account that can connect to the report server during upgrade>" /RSUPGRADEPASSWORD="<Provide a password for the report server upgrade account>" /ISSVCAccount="NT Authority\Network Service" /IACCEPTSQLSERVERLICENSETERMS  
```  
  
##  <a name="repair-parameters"></a><a name="Repair"></a> 修復參數  
 您可以使用下表中的參數來開發修復的命令列指令碼。 
  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 元件|參數|描述|  
|-----------------------------------------|---------------|-----------------|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/ACTION<br /><br /> **必要**|指出修復工作流程的必要參數。<br /><br /> 支援的值：**Repair**|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/ENU<br /><br /> **選擇性**|當安裝媒體包含英文以及與作業系統對應之語言的語言套件時，使用此參數在當地語系化的作業系統上安裝英文版的 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/FEATURES<br /><br /> **必要**|指定要修復的 [元件](#Feature) 。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/INSTANCENAME<br /><br /> **必要**|指定 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 執行個體名稱。<br /><br /> 如需詳細資訊，請參閱＜ [Instance Configuration](../../sql-server/install/instance-configuration.md)＞。|  
|PolyBase|/PBENGSVCACCOUNT<br /><br /> **選擇性**|指定引擎服務的帳戶。 預設是 **NT Authority\NETWORK SERVICE**。|  
|PolyBase|/PBDMSSVCPASSWORD<br /><br /> **選擇性**|指定引擎服務帳戶的密碼。 使用受控服務帳戶、虛擬帳戶或內建帳戶時，可以省略此參數。|  
|PolyBase|/PBENGSVCSTARTUPTYPE<br /><br /> **選擇性**|指定 PolyBase 引擎服務的啟動模式：自動 (預設)、停用及手動。|  
|PolyBase|/PBPORTRANGE<br /><br /> **選擇性**|為 PolyBase 服務指定含至少 6 個連接埠的連接埠範圍。 範例：<br /><br /> `/PBPORTRANGE=16450-16460`|  
|PolyBase|/PBSCALEOUT<br /><br /> **選擇性**|指定 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 執行個體是否會用作 PolyBase 向外延展計算群組的一部分。 支援的值：**True**、**False**|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/Q 或 /QUIET <br /><br /> **選擇性**|指定安裝程式會在不含任何使用者介面的無訊息模式中執行。 這是自動安裝所使用的參數。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/HIDECONSOLE<br /><br /> **選擇性**|指定要隱藏或關閉主控台視窗。|  
  
###### <a name="sample-syntax"></a>範例語法：  
 修復執行個體和共用的元件。 
  
```  
setup.exe /q /ACTION=Repair /INSTANCENAME=<instancename>  
```  
  
##  <a name="rebuild-system-database-parameters"></a><a name="Rebuild"></a> 重建系統資料庫參數  
 您可以使用下表中的參數開發命令列指令碼，以重建 master、model、msdb 與 tempdb 系統資料庫。 如需詳細資訊，請參閱 [重建系統資料庫](../../relational-databases/databases/rebuild-system-databases.md)。 
  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 元件|參數|描述|  
|-----------------------------------------|---------------|-----------------|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/ACTION<br /><br /> **必要**|指出重建資料庫工作流程的必要參數。<br /><br /> 支援的值：**Rebuilddatabase**|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/INSTANCENAME<br /><br /> **必要**|指定 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 執行個體名稱。<br /><br /> 如需詳細資訊，請參閱＜ [Instance Configuration](../../sql-server/install/instance-configuration.md)＞。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/Q 或 /QUIET <br /><br /> **選擇性**|指定安裝程式會在不含任何使用者介面的無訊息模式中執行。 這是自動安裝所使用的參數。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLCOLLATION<br /><br /> **選擇性**|指定新的伺服器層級定序。<br /><br /> 預設值會根據您 Windows 作業系統的地區設定而異。 如需詳細資訊，請參閱＜ [安裝程式中的定序設定](../../relational-databases/collations/collation-and-unicode-support.md)＞。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SAPWD<br /><br /> **在執行個體安裝期間指定了 /SECURITYMODE=SQL 時則為必要參數。**|指定 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] **SA** 帳戶的密碼。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLSYSADMINACCOUNTS<br /><br /> **必要**|您可以使用這個參數來提供登入，以便成為系統管理員 (sysadmin) 角色的成員。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLTEMPDBDIR<br /><br /> **選擇性**|指定 tempdb 資料檔案的目錄。 指定多個目錄時，請以空格隔開這些目錄。 若指定多個目錄，tempdb 資料檔案將以循環配置資源方式跨多個目錄存放。<br /><br /> 預設值：`<InstallSQLDataDir>\<SQLInstanceID>\MSSQL\Data` (系統資料目錄)<br /><br /> 注意：此參數也會新增至 RebuildDatabase 案例。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLTEMPDBLOGDIR<br /><br /> **選擇性**|指定 tempdb 記錄檔的目錄。<br /><br /> 預設值：`<InstallSQLDataDir>\<SQLInstanceID>\MSSQL\Data` (系統資料目錄)<br /><br /> **注意：** 此參數也會新增至 RebuildDatabase 案例。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLTEMPDBFILECOUNT<br /><br /> **選擇性**|指定安裝程式要新增的 tempdb 資料檔案數目。 此值可以增加到與核心數目相同為止。 預設值：<br /><br /> [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)] 為 1 個<br /><br /> 所有其他版本為 8 個或與核心數目相同 (兩者取其較低者)<br /><br /> **重要：** tempdb 的主要資料庫檔案仍為 tempdb.mdf。 其他的 tempdb 檔案會命名為 tempdb_mssql_#.ndf，其中 # 代表唯一的數字，該數字是在安裝期間替每個額外的 tempdb 資料庫檔案所建立。 此命名慣例旨在使其為唯一。 解除安裝 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的執行個體會刪除命名慣例為 tempdb_mssql_#.ndf 的檔案。 請勿將 tempdb_mssql_\*.ndf 命名慣例用於使用者資料庫檔案。<br /><br /> **警告：** [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)] 不支援設定此參數。 安裝程式只會安裝 1 個 tempdb 資料檔案。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLTEMPDBFILESIZE<br /><br /> **選擇性**|在 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 中引進。 指定每個 tempdb 資料檔案的初始大小。<br/><br/>預設值 = [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)] 為 4 MB，所有其他版本為 8 MB。<br/><br/>最小值= (4 MB 或 8 MB)。<br/><br/>最大值 = 1024 MB ([!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 為 262,144 MB)。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLTEMPDBFILEGROWTH<br /><br /> **選擇性**|指定每個 tempdb 資料檔案的檔案成長增量 (MB)。 0 值指出自動成長是關閉的，且不允許其他空間。 安裝程式允許的大小上限為 1024。<br /><br /> 預設值：64<br /><br /> 允許的範圍：最小值 = 0，最大值 = 1024|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLTEMPDBLOGFILESIZE<br /><br /> **選擇性**|指定 tempdb 記錄檔的初始大小 (MB)。 安裝程式允許的大小上限為 1024。 預設值：<br /><br /> [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)] 為 4 個<br /><br /> 所有其他版本為 8 個<br /><br /> 允許的範圍：最小值 = 預設值 (4 或 8)，最大值 = 1024|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLTEMPDBLOGFILEGROWTH<br /><br /> **選擇性**|在 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 中引進。 指定每個 tempdb 記錄檔的初始大小。<br/><br/>預設值 = [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)] 為 4 MB，所有其他版本為 8 MB。<br/><br/>最小值= (4 MB 或 8 MB)。<br/><br/>最大值 = 1024 MB ([!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 為 262,144 MB)。|  
  
##  <a name="uninstall-parameters"></a><a name="Uninstall"></a> 解除安裝參數  
 您可以使用下表中的參數來開發解除安裝的命令列指令碼。 
  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 元件|參數|描述|  
|-----------------------------------------|---------------|-----------------|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/ACTION<br /><br /> **必要**|指出解除安裝工作流程的必要參數。<br /><br /> 支援的值：**解除安裝**|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/CONFIGURATIONFILE<br /><br /> **選擇性**|指定要使用的 [ConfigurationFile](./install-sql-server-using-a-configuration-file.md) 。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/FEATURES<br /><br /> **必要**|指定要解除安裝的 [元件](#Feature) 。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/HELP、H、?<br /><br /> **選擇性**|顯示參數的使用方式選項。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/INDICATEPROGRESS<br /><br /> **選擇性**|指定要將詳細安裝程式記錄檔送到主控台。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/INSTANCENAME<br /><br /> **必要**|指定 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 執行個體名稱。<br /><br /> 如需詳細資訊，請參閱＜ [Instance Configuration](../../sql-server/install/instance-configuration.md)＞。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/Q 或 /QUIET <br /><br /> **選擇性**|指定安裝程式會在不含任何使用者介面的無訊息模式中執行。 這是自動安裝所使用的參數。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/HIDECONSOLE<br /><br /> **選擇性**|指定要隱藏或關閉主控台視窗。|  
  
###### <a name="sample-syntax"></a>範例語法：  
 解除安裝現有的 SQL Server 執行個體。 
  
```  
setup.exe /Action=Uninstall /FEATURES=SQL,AS,RS,IS,Tools /INSTANCENAME=MSSQLSERVER  
```  
  
 若要移除具名執行個體，請在本文先前提到的範例中指定執行個體的名稱，而非 "MSSQLSERVER"。 
  
##  <a name="failover-cluster-parameters"></a><a name="ClusterInstall"></a> 容錯移轉叢集參數  
 安裝 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 容錯移轉叢集執行個體之前，請先檢閱下列文章：  
  
-   [安裝 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 的硬體和軟體需求](../../sql-server/install/hardware-and-software-requirements-for-installing-sql-server.md)  
  
-   [SQL Server 安裝的安全性考量](../../sql-server/install/security-considerations-for-a-sql-server-installation.md)
  
-   [安裝容錯移轉叢集之前](../../sql-server/failover-clusters/install/before-installing-failover-clustering.md)  
  
-   [AlwaysOn 容錯移轉叢集執行個體 &#40;SQL Server&#41;](../../sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server.md)  
  
    > [!IMPORTANT]  
    > 所有容錯移轉叢集安裝命令都需要基礎 Windows 叢集。 屬於 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 容錯移轉叢集一部分的所有節點都必須屬於相同 Windows 叢集的一部分。 
  
 測試並修改下列容錯移轉叢集安裝指令碼，以便符合組織的需求。 
  
#### <a name="integrated-install-failover-cluster-parameters"></a>整合式安裝容錯移轉叢集參數  
 您可以使用下表中的參數來開發容錯移轉叢集安裝的命令列指令碼。 
  
 如需有關整合式安裝的詳細資訊，請參閱[AlwaysOn 容錯移轉叢集執行個體 &#40;SQL Server&#41;](../../sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server.md)。 
  
> [!NOTE]
> 若要在安裝之後新增其他節點，請使用[新增節點](#AddNode)動作。 
  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 元件|參數|詳細資料|  
|-----------------------------------------|---------------|-------------|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/ACTION<br /><br /> **必要**|指出容錯移轉叢集安裝工作流程的必要參數。<br /><br /> 支援的值：**InstallFailoverCluster**|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/IACCEPTSQLSERVERLICENSETERMS<br /><br /> **只有當您針對自動安裝指定了 /Q 或 /QS 參數時，才需要使用此參數。**|確認接受授權條款的必要參數。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/ENU<br /><br /> **選擇性**|當安裝媒體包含英文以及與作業系統對應之語言的語言套件時，使用此參數在當地語系化的作業系統上安裝英文版的 SQL Server。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/FAILOVERCLUSTERGROUP<br /><br /> **選擇性**|指定要用於 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 容錯移轉叢集之資源群組的名稱。 它可以是現有叢集群組的名稱或新資源群組的名稱。<br /><br /> 預設值：<br /><br /> SQL Server (\<InstanceName>)|  
|PolyBase|/PBENGSVCACCOUNT<br /><br /> **選擇性**|指定引擎服務的帳戶。 預設是 **NT Authority\NETWORK SERVICE**。|  
|PolyBase|/PBDMSSVCPASSWORD<br /><br /> **選擇性**|指定引擎服務帳戶的密碼。 使用受控服務帳戶、虛擬帳戶或內建帳戶時，可以省略此參數。 |  
|PolyBase|/PBENGSVCSTARTUPTYPE<br /><br /> **選擇性**|指定 PolyBase 引擎服務的啟動模式：自動 (預設)、停用及手動。|  
|PolyBase|/PBPORTRANGE<br /><br /> **選擇性**|為 PolyBase 服務指定含至少 6 個連接埠的連接埠範圍。 範例：<br /><br /> `/PBPORTRANGE=16450-16460`|  
|PolyBase|/PBSCALEOUT<br /><br /> **選擇性**|指定 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 執行個體是否會用作 PolyBase 向外延展計算群組的一部分。 支援的值：**True**、**False**|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/*UpdateEnabled*<br /><br /> **選擇性**|指定 SQL Server 安裝程式是否應探索及包含產品更新。 有效值為 True 和 False 或 1 和 0。 根據預設，SQL Server 安裝程式會包含找到的更新。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/*UpdateSource*<br /><br /> **選擇性**|指定 SQL Server 安裝程式將取得產品更新的位置。 有效值為 "MU"，表示搜尋 [!INCLUDE[msCoName](../../includes/msconame-md.md)] 更新、有效資料夾路徑、相對路徑 (例如 .\MyUpdates) 或 UNC 共用。 根據預設，SQL Server 安裝程式會搜尋 [!INCLUDE[msCoName](../../includes/msconame-md.md)] Update 或透過 Windows Server Update Services 搜尋 Windows Update Service。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/CONFIGURATIONFILE<br /><br /> **選擇性**|指定要使用的 [ConfigurationFile](./install-sql-server-using-a-configuration-file.md) 。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/ERRORREPORTING<br /><br /> **選擇性**|在 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 中沒有任何作用。 <br/><br/>若要管理傳送錯誤意見反應給 Microsoft 的方式，請參閱[如何設定 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)]，將意見反應傳送給 Microsoft](https://support.microsoft.com/kb/3153756)。 <br/><br/>在較舊版本中，這會指定 SQL Server 的錯誤報告。<br /><br /> 如需詳細資訊，請參閱＜ [Microsoft 錯誤報告服務隱私權聲明](../../sql-server/sql-server-privacy.md)＞。 支援的值：<br /><br /> 1=啟用<br /><br /> 0=停用|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/FEATURES<br /><br /> **必要**|指定要安裝的 [元件](#Feature) 。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/HELP、?<br /><br /> **選擇性**|顯示參數的使用方式選項。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/INDICATEPROGRESS<br /><br /> **選擇性**|指定要將詳細安裝程式記錄檔送到主控台。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/INSTALLSHAREDDIR<br /><br /> **選擇性**|指定 64 位元共用元件的非預設安裝目錄。<br /><br /> 預設為 `%Program Files%\Microsoft SQL Server`<br /><br /> 無法設定為 `%Program Files(x86)%\Microsoft SQL Server`|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/INSTALLSHAREDWOWDIR<br /><br /> **選擇性**|指定 32 位元共用元件的非預設安裝目錄。 只有 64 位元系統才支援。<br /><br /> 預設為 `%Program Files(x86)%\Microsoft SQL Server`<br /><br /> 無法設定為 `%Program Files%\Microsoft SQL Server`|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/ INSTANCEDIR<br /><br /> **選擇性**|指定執行個體特有元件的非預設安裝目錄。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/INSTANCEID<br /><br /> **選擇性**|指定 [InstanceID](#InstanceID)的非預設值。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/INSTANCENAME<br /><br /> **必要**|指定 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 執行個體名稱。<br /><br /> 如需詳細資訊，請參閱＜ [Instance Configuration](../../sql-server/install/instance-configuration.md)＞。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/PID<br /><br /> **選擇性**|指定 SQL Server 版的產品金鑰。 如果沒有指定這個參數，就會使用 Evaluation。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/Q 或 /QUIET <br /><br /> **選擇性**|指定安裝程式會在不含任何使用者介面的無訊息模式中執行。 這是自動安裝所使用的參數。 /Q 參數會覆寫 /QS 參數的輸入。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/QS 或 /QUIETSIMPLE <br /><br /> **選擇性**|指定安裝程式會執行並透過 UI 顯示進度，但是不接受任何輸入或顯示任何錯誤訊息。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/SQMREPORTING<br /><br /> **選擇性**|在 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 中沒有任何作用。 <br/><br/>若要管理傳送錯誤意見反應給 Microsoft 的方式，請參閱[如何設定 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)]，將意見反應傳送給 Microsoft](https://support.microsoft.com/kb/3153756)。 <br/><br/>在較舊版本中，這會指定 SQL Server 的功能使用狀況報告。<br /><br />支援的值：<br /><br /> 1=啟用<br /><br /> 0=停用|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/HIDECONSOLE<br /><br /> **選擇性**|指定要隱藏或關閉主控台視窗。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/FAILOVERCLUSTERDISKS<br /><br /> **選擇性**|指定要包含在 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 容錯移轉叢集資源群組中的共用磁碟清單。<br /><br /> 預設值：第一個磁碟機會當做所有資料庫的預設磁碟機使用。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/FAILOVERCLUSTERIPADDRESSES<br /><br /> **必要**|指定編碼的 IP 位址。 編碼是以分號分隔 (;) 並遵循格式 \<IP Type>;\<address>;\<network name>;\<subnet mask>。 支援的 IP 類型包括 DHCP、IPv4 和 IPv6。<br />您可以指定多個容錯移轉叢集 IP 位址，每個位址之間隔一個空格。 請參閱下列範例：<br /><br /> `FAILOVERCLUSTERIPADDRESSES=DEFAULT`<br /><br /> `FAILOVERCLUSTERIPADDRESSES=IPv4;DHCP;ClusterNetwork1`<br /><br /> `FAILOVERCLUSTERIPADDRESSES=IPv4;172.16.0.0;ClusterNetwork1;172.31.255.255`<br /><br /> `FAILOVERCLUSTERIPADDRESSES=IPv6;DHCP;ClusterNetwork1`<br /><br /> `FAILOVERCLUSTERIPADDRESSES=IPv6;2001:db8:23:1002:20f:1fff:feff:b3a3;ClusterNetwork1`|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/FAILOVERCLUSTERNETWORKNAME<br /><br /> **必要**|針對新的 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 容錯移轉叢集指定網路名稱。 這個名稱是用來在網路上識別新的 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 容錯移轉叢集執行個體。|  
|SQL Server Agent|/AGTSVCACCOUNT<br /><br /> **必要**|指定 SQL Server Agent 服務的帳戶。|  
|SQL Server Agent|/AGTSVCPASSWORD<br /><br /> [必要](#Accounts)|指定 SQL Server Agent 服務帳戶的密碼。 使用受控服務帳戶、虛擬帳戶或內建帳戶時，可以省略此參數。|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/ASBACKUPDIR<br /><br /> **選擇性**|指定 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] 備份檔案的目錄。 預設值：<br /><br /> 64 位元上的 WOW 模式：`%Program Files(x86)%\Microsoft SQL Server\<INSTANCEDIR>\<ASInstanceID>\OLAP\Backup`<br /><br /> 所有其他安裝：`%Program Files%\Microsoft SQL Server\<INSTANCEDIR>\<ASInstanceID>\OLAP\Backup`|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/ASCOLLATION<br /><br /> **選擇性**|指定 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]的定序設定。<br /><br /> 預設值：**Latin1_General_CI_AS**|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/ASCONFIGDIR<br /><br /> **選擇性**|指定 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] 組態檔的目錄。 預設值：<br /><br /> 64 位元上的 WOW 模式：`%Program Files(x86)%\Microsoft SQL Server\<INSTANCEDIR>\<ASInstanceID>\OLAP\Config`<br /><br /> 所有其他安裝：`%Program Files%\Microsoft SQL Server\<INSTANCEDIR>\<ASInstanceID>\OLAP\Config`|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/ASDATADIR<br /><br /> **選擇性**|指定 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] 資料檔案的目錄。 預設值：<br /><br /> 64 位元上的 WOW 模式：`%Program Files(x86)%\Microsoft SQL Server\<INSTANCEDIR>\<ASInstanceID>\OLAP\Data`<br /><br /> 所有其他安裝：`%Program Files%\Microsoft SQL Server\<INSTANCEDIR>\<ASInstanceID>\OLAP\Data`|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/ASLOGDIR<br /><br /> **選擇性**|指定 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] 記錄檔的目錄。 預設值：<br /><br /> 64 位元上的 WOW 模式：`%Program Files(x86)%\Microsoft SQL Server\<INSTANCEDIR>\<ASInstanceID>\OLAP\Log`<br /><br /> 所有其他安裝：`%Program Files%\Microsoft SQL Server\<INSTANCEDIR>\<ASInstanceID>\OLAP\Log`|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/ASSYSADMINACCOUNTS<br /><br /> **必要**|指定 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]的系統管理員認證。|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/ASTEMPDIR<br /><br /> **選擇性**|指定 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] 暫存檔的目錄。 預設值：<br /><br /> 64 位元上的 WOW 模式：`%Program Files(x86)%\Microsoft SQL Server\<INSTANCEDIR>\<ASInstanceID>\OLAP\Temp`<br /><br /> 所有其他安裝：`%Program Files%\Microsoft SQL Server\<INSTANCEDIR>\<ASInstanceID>\OLAP\Temp`|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/ASPROVIDERMSOLAP<br /><br /> **選擇性**|指定 MSOLAP 提供者是否可以在處理序中執行。<br /><br /> 預設值：1=啟用|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/ASSERVERMODE<br /><br /> **選擇性**|指定 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] 執行個體的伺服器模式。 叢集情況中的有效值為 MULTIDIMENSIONAL 或 TABULAR。 **ASSERVERMODE** 區分大小寫。 所有值都必須以大寫形式表示。 如需有關有效值的詳細資訊，請參閱＜以表格模式安裝 Analysis Services＞。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/INSTALLSQLDATADIR<br /><br /> **必要**|指定 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 資料檔案的資料目錄。<br /><br /> 您必須指定此資料目錄，且它必須位於共用叢集磁碟上。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SAPWD<br /><br /> **當 /SECURITYMODE=SQL 時則為必要參數**|指定 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] **SA** 帳戶的密碼。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SECURITYMODE<br /><br /> **選擇性**|指定 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的安全性模式。<br /><br /> 如果沒有提供這個參數，就會支援僅限 Windows 驗證模式。<br /><br /> 支援的值：**SQL**|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLBACKUPDIR<br /><br /> **選擇性**|指定備份檔的目錄。<br /><br /> 預設值：`<InstallSQLDataDir>\<SQLInstanceID>\MSSQL\Backup`|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLCOLLATION<br /><br /> **選擇性**|指定 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]的定序設定。<br /><br /> 預設值會根據您 Windows 作業系統的地區設定而異。 如需詳細資訊，請參閱＜ [安裝程式中的定序設定](../../relational-databases/collations/collation-and-unicode-support.md)＞。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLSVCACCOUNT<br /><br /> **必要**|指定 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 服務的啟動帳戶。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLSVCPASSWORD<br /><br /> [必要](#Accounts)|指定 SQLSVCACCOUNT 的密碼。 使用受控服務帳戶、虛擬帳戶或內建帳戶時，可以省略此參數。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLSYSADMINACCOUNTS<br /><br /> **必要**|您可以使用這個參數來提供登入，以便成為 **系統管理員** 角色的成員。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLUSERDBDIR<br /><br /> **選擇性**|指定使用者資料庫之資料檔案的目錄。<br /><br /> 預設值：`<InstallSQLDataDir>\<SQLInstanceID>\MSSQL\Data`|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLTEMPDBDIR<br /><br /> **選擇性**|指定 tempdb 資料檔案的目錄。 指定多個目錄時，請以空格隔開這些目錄。 若指定多個目錄，tempdb 資料檔案將以循環配置資源方式跨多個目錄存放。<br /><br /> 預設值：`<InstallSQLDataDir>\<SQLInstanceID>\MSSQL\Data` (系統資料目錄)<br /><br /> 注意：此參數也會新增至 RebuildDatabase 案例。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLTEMPDBLOGDIR<br /><br /> **選擇性**|指定 tempdb 記錄檔的目錄。<br /><br /> 預設值：`<InstallSQLDataDir>\<SQLInstanceID>\MSSQL\Data` (系統資料目錄)<br /><br /> 注意:此參數也會新增至 RebuildDatabase 案例。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLTEMPDBFILECOUNT<br /><br /> **選擇性**|指定安裝程式要新增的 tempdb 資料檔案數目。 此值可以增加到與核心數目相同為止。 預設值：<br /><br /> [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)] 為 1 個<br /><br /> 所有其他版本為 8 個或與核心數目相同 (兩者取其較低者)<br /><br /> **重要：** tempdb 的主要資料庫檔案仍為 tempdb.mdf。 其他的 tempdb 檔案會命名為 tempdb_mssql_#.ndf，其中 # 代表唯一的數字，該數字是在安裝期間替每個額外的 tempdb 資料庫檔案所建立。 此命名慣例旨在使其為唯一。 解除安裝 SQL Server 的執行個體會刪除命名慣例為 tempdb_mssql_ #.ndf 的檔案。 請勿將 tempdb_mssql_\*.ndf 命名慣例用於使用者資料庫檔案。<br /><br /> **警告：** [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)] 不支援設定此參數。 安裝程式只會安裝 1 個 tempdb 資料檔案。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLTEMPDBFILESIZE<br /><br /> **選擇性**|在 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 中引進。 指定每個 tempdb 資料檔案的初始大小。<br/><br/>預設值 =  8 MB。<br/><br/>最小值 =  8 MB。<br/><br/>最大值 = 1024 MB ([!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 為 262,144 MB)。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLTEMPDBFILEGROWTH<br /><br /> **選擇性**|指定每個 tempdb 資料檔案的檔案成長增量 (MB)。 0 值指出自動成長是關閉的，且不允許其他空間。 安裝程式允許的大小上限為 1024。<br /><br /> 預設值：64<br /><br /> 允許的範圍：最小值 = 0，最大值 = 1024|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLTEMPDBLOGFILESIZE<br /><br /> **選擇性**|指定 tempdb 記錄檔的初始大小 (MB)。 安裝程式允許的大小上限為 1024。 <br /> 預設值：<br /><br /> [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)] 為 4 個<br /><br /> 所有其他版本為 8 個<br /><br /> 允許的範圍：最小值 = 預設值 (4 或 8)，最大值 = 1024|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLTEMPDBLOGFILEGROWTH<br /><br /> **選擇性**|在 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 中引進。 指定每個 tempdb 記錄檔的初始大小。<br/><br/>預設值 = [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)] 為 4 MB，所有其他版本為 8 MB。<br/><br/>最小值= (4 MB 或 8 MB)。<br/><br/>最大值 = 1024 MB ([!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 為 262,144 MB)。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLUSERDBLOGDIR<br /><br /> **選擇性**|指定使用者資料庫之記錄檔的目錄。<br /><br /> 預設值：`<InstallSQLDataDir>\<SQLInstanceID>\MSSQL\Data`|  
|FILESTREAM|/FILESTREAMLEVEL<br /><br /> **選擇性**|指定 FILESTREAM 功能的存取層級。 支援的值：<br /><br /> 0=針對這個執行個體停用 FILESTREAM 支援 (預設值)。<br /><br /> 1=針對 [!INCLUDE[tsql](../../includes/tsql-md.md)] 存取啟用 FILESTREAM。<br /><br /> 2=針對 [!INCLUDE[tsql](../../includes/tsql-md.md)] 和檔案 I/O 資料流存取啟用 FILESTREAM (不適用於叢集狀況)。<br /><br /> 3=允許遠端用戶端具有 FILESTREAM 資料的資料流存取權。|  
|FILESTREAM|/FILESTREAMSHARENAME<br /><br /> **選擇性**<br /><br /> **當 FILESTREAMLEVEL 大於 1 時則為必要參數。**|指定即將儲存 FILESTREAM 資料之 Windows 共用的名稱。|  
|SQL Server 全文檢索|/FTSVCACCOUNT<br /><br /> **選擇性**|指定全文檢索篩選啟動器服務的帳戶。<br /><br /> 在 [!INCLUDE[firstref_longhorn](../../includes/firstref-longhorn-md.md)] 或更新版本中會忽略此參數。 ServiceSID 是用來協助保護 SQL Server 與全文檢索篩選背景程式之間的通訊。<br /><br /> 如果沒有提供這些值，就會停用全文檢索篩選啟動器服務。 您必須使用 SQL Server 控制管理員來變更服務帳戶並啟用全文檢索功能。<br /><br /> 預設值：本機服務帳戶|  
|SQL Server 全文檢索|/FTSVCPASSWORD<br /><br /> **選擇性**|指定全文檢索篩選啟動器服務的密碼。<br /><br /> 在 [!INCLUDE[firstref_longhorn](../../includes/firstref-longhorn-md.md)] 或更新版本中會忽略此參數。 使用受控服務帳戶、虛擬帳戶或內建帳戶時，可以省略此參數。|  
|[!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)]|/ISSVCACCOUNT<br /><br /> **必要**|指定 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)]的帳戶。<br /><br /> 預設值：NT AUTHORITY\NETWORK SERVICE|  
|[!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)]|/ISSVCPASSWORD<br /><br /> [必要](#Accounts)|指定 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 密碼。 使用受控服務帳戶、虛擬帳戶或內建帳戶時，可以省略此參數。|  
|[!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)]|/ISSVCStartupType<br /><br /> **選擇性**|指定 [服務的](#Accounts) 啟動 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 模式。|  
|[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]|/RSINSTALLMODE<br /><br /> **選擇性**|從 SQL Server 2017 開始不再適用。  指定 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]的安裝模式。|  
|[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]|/RSSVCACCOUNT<br /><br /> **必要**|從 SQL Server 2017 開始不再適用。  指定 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]的啟動帳戶。|  
|[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]|/RSSVCPASSWORD<br /><br /> [必要](#Accounts)| 從 SQL Server 2017 開始不再適用。  指定 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] 服務之啟動帳戶的密碼。 使用受控服務帳戶、虛擬帳戶或內建帳戶時，可以省略此參數。|  
|[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]|/RSSVCStartupType<br /><br /> **選擇性**|從 SQL Server 2017 開始不再適用。  指定 [的](#Accounts) 啟動 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]模式。 支援的值：<br /><br /> **自動**<br /><br /> **Disabled**<br /><br /> **手動**|  
  
 建議您改用 Service SID，而非網域群組。 
  
##### <a name="additional-notes"></a>其他注意事項：  
 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 和 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] 是唯一可感知叢集的元件。 其他功能無法感知叢集而且無法透過容錯移轉提供高可用性。 
  
###### <a name="sample-syntax"></a>範例語法：  
 安裝具有 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 和 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 等預設執行個體的單一節點 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] 容錯移轉叢集執行個體。 
  
```  
setup.exe /q /ACTION=InstallFailoverCluster /InstanceName=MSSQLSERVER /INDICATEPROGRESS /ASSYSADMINACCOUNTS="<DomainName\UserName>" /ASDATADIR=<Drive>:\OLAP\Data /ASLOGDIR=<Drive>:\OLAP\Log /ASBACKUPDIR=<Drive>:\OLAP\Backup /ASCONFIGDIR=<Drive>:\OLAP\Config /ASTEMPDIR=<Drive>:\OLAP\Temp /FAILOVERCLUSTERDISKS="<Cluster Disk Resource Name - for example, 'Disk S:'" /FAILOVERCLUSTERNETWORKNAME="<Insert Network Name>" /FAILOVERCLUSTERIPADDRESSES="IPv4;xx.xxx.xx.xx;Cluster Network;xxx.xxx.xxx.x" /FAILOVERCLUSTERGROUP="MSSQLSERVER" /Features=AS,SQL /ASSVCACCOUNT="<DomainName\UserName>" /ASSVCPASSWORD="xxxxxxxxxxx" /AGTSVCACCOUNT="<DomainName\UserName>" /AGTSVCPASSWORD="xxxxxxxxxxx" /INSTALLSQLDATADIR="<Drive>:\<Path>\MSSQLSERVER" /SQLCOLLATION="SQL_Latin1_General_CP1_CS_AS" /SQLSVCACCOUNT="<DomainName\UserName>" /SQLSVCPASSWORD="xxxxxxxxxxx" /SQLSYSADMINACCOUNTS="<DomainName\UserName> /IACCEPTSQLSERVERLICENSETERMS  
```  
  
#### <a name="prepare-failover-cluster-parameters"></a>準備容錯移轉叢集參數  
 您可以使用下表中的參數來開發容錯移轉叢集準備的命令列指令碼。 這是進階叢集安裝的第一個步驟。在此步驟中，您必須在容錯移轉叢集的所有節點上準備容錯移轉叢集執行個體。 如需詳細資訊，請參閱 [AlwaysOn 容錯移轉叢集執行個體 &#40;SQL Server&#41;](../../sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server.md)。 
  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 元件|參數|描述|  
|-----------------------------------------|---------------|-----------------|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/ACTION<br /><br /> **必要**|指出容錯移轉叢集準備工作流程的必要參數。<br /><br /> 支援的值：**PrepareFailoverCluster**|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/IACCEPTSQLSERVERLICENSETERMS<br /><br /> **只有當您針對自動安裝指定了 /Q 或 /QS 參數時，才需要使用此參數。**|確認接受授權條款的必要參數。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/ENU<br /><br /> **選擇性**|當安裝媒體包含英文以及與作業系統對應之語言的語言套件時，使用此參數在當地語系化的作業系統上安裝英文版的 SQL Server。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/*UpdateEnabled*<br /><br /> **選擇性**|指定 SQL Server 安裝程式是否應探索及包含產品更新。 有效值為 True 和 False 或 1 和 0。 根據預設，SQL Server 安裝程式會包含找到的更新。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/*UpdateSource*<br /><br /> **選擇性**|指定 SQL Server 安裝程式將取得產品更新的位置。 有效值為 "MU"，表示搜尋 [!INCLUDE[msCoName](../../includes/msconame-md.md)] 更新、有效資料夾路徑、相對路徑 (例如 `.\MyUpdates`) 或 UNC 共用。 根據預設，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式會搜尋 [!INCLUDE[msCoName](../../includes/msconame-md.md)] Update 或透過 Windows Server Update Services 搜尋 Windows Update Service。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/CONFIGURATIONFILE<br /><br /> **選擇性**|指定要使用的 [ConfigurationFile](./install-sql-server-using-a-configuration-file.md) 。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/ERRORREPORTING<br /><br /> **選擇性**|在 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 中沒有任何作用。 <br/><br/>若要管理傳送錯誤意見反應給 Microsoft 的方式，請參閱[如何設定 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)]，將意見反應傳送給 Microsoft](https://support.microsoft.com/kb/3153756)。 <br/><br/>在較舊版本中，這會指定 SQL Server 的錯誤報告。<br /><br /> 如需詳細資訊，請參閱＜ [Microsoft 錯誤報告服務隱私權聲明](../../sql-server/sql-server-privacy.md)＞。 支援的值：<br /><br /> 0=停用<br /><br /> 1=啟用|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/FEATURES<br /><br /> **必要**|指定要安裝的 [元件](#Feature) 。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/HELP、?<br /><br /> **選擇性**|顯示參數的使用方式選項。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/INDICATEPROGRESS<br /><br /> **選擇性**|指定要將詳細安裝程式記錄檔送到主控台。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/INSTALLSHAREDDIR<br /><br /> **選擇性**|指定 64 位元共用元件的非預設安裝目錄。<br /><br /> 預設為 `%Program Files%\Microsoft SQL Server`<br /><br /> 無法設定為 `%Program Files(x86)%\Microsoft SQL Server`|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/INSTALLSHAREDWOWDIR<br /><br /> **選擇性**|指定 32 位元共用元件的非預設安裝目錄。 只有 64 位元系統才支援。<br /><br /> 預設為 `%Program Files(x86)%\Microsoft SQL Server`<br /><br /> 無法設定為 `%Program Files%\Microsoft SQL Server`|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/ INSTANCEDIR<br /><br /> **選擇性**|指定執行個體特有元件的非預設安裝目錄。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/INSTANCEID<br /><br /> **選擇性**|指定 [InstanceID](#InstanceID)的非預設值。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/INSTANCENAME<br /><br /> **必要**|指定 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 執行個體名稱。<br /><br /> 如需詳細資訊，請參閱＜ [Instance Configuration](../../sql-server/install/instance-configuration.md)＞。|  
|PolyBase|/PBENGSVCACCOUNT<br /><br /> **選擇性**|指定引擎服務的帳戶。 預設是 **NT Authority\NETWORK SERVICE**。|  
|PolyBase|/PBDMSSVCPASSWORD<br /><br /> **選擇性**|指定引擎服務帳戶的密碼。 使用受控服務帳戶、虛擬帳戶或內建帳戶時，可以省略此參數。|  
|PolyBase|/PBENGSVCSTARTUPTYPE<br /><br /> **選擇性**|指定 PolyBase 引擎服務的啟動模式：自動 (預設)、停用及手動。|  
|PolyBase|/PBPORTRANGE<br /><br /> **選擇性**|為 PolyBase 服務指定含至少 6 個連接埠的連接埠範圍。 範例：<br /><br /> `/PBPORTRANGE=16450-16460`|  
|PolyBase|/PBSCALEOUT<br /><br /> **選擇性**|指定 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 執行個體是否會用作 PolyBase 向外延展計算群組的一部分。 支援的值：**True**、**False**|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/PID<br /><br /> **選擇性**|指定 SQL Server 版的產品金鑰。 若未指定這個參數，<br /><br /> 會使用 Evaluation。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/Q 或 /QUIET <br /><br /> **選擇性**|指定安裝程式會在不含任何使用者介面的無訊息模式中執行。 這是自動安裝所使用的參數。 /Q 參數會覆寫 /QS 參數的輸入。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/QS 或 /QUIETSIMPLE <br /><br /> **選擇性**|指定安裝程式會執行並透過 UI 顯示進度，但是不接受任何輸入或顯示任何錯誤訊息。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/SQMREPORTING<br /><br /> **選擇性**|在 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 中沒有任何作用。 <br/><br/>若要管理傳送錯誤意見反應給 Microsoft 的方式，請參閱[如何設定 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)]，將意見反應傳送給 Microsoft](https://support.microsoft.com/kb/3153756)。 <br/><br/>在較舊版本中，這會指定 SQL Server 的功能使用狀況報告。<br /><br />支援的值：<br /><br /> 0=停用<br /><br /> 1=啟用|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/HIDECONSOLE<br /><br /> **選擇性**|指定要隱藏或關閉主控台視窗。|  
|SQL Server Agent|/AGTSVCACCOUNT<br /><br /> **必要**|指定 SQL Server Agent 服務的帳戶。|  
|SQL Server Agent|/AGTSVCPASSWORD<br /><br /> [必要](#Accounts)|指定 SQL Server Agent 服務帳戶的密碼。 使用受控服務帳戶、虛擬帳戶或內建帳戶時，可以省略此參數。 |  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/ASSVCACCOUNT<br /><br /> **必要**|指定 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] 服務的帳戶。|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/ASSVCPASSWORD<br /><br /> [必要](#Accounts)|指定 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] 服務的密碼。 使用受控服務帳戶、虛擬帳戶或內建帳戶時，可以省略此參數。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLSVCACCOUNT<br /><br /> **必要**|指定 SQL Server 服務的啟動帳戶。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLSVCPASSWORD<br /><br /> [必要](#Accounts)|指定 SQLSVCACCOUNT 的密碼。 使用受控服務帳戶、虛擬帳戶或內建帳戶時，可以省略此參數。|  
|FILESTREAM|/FILESTREAMLEVEL<br /><br /> **選擇性**|指定 FILESTREAM 功能的存取層級。 支援的值：<br /><br /> 0=針對這個執行個體停用 FILESTREAM 支援 (預設值)。<br /><br /> 1=針對 [!INCLUDE[tsql](../../includes/tsql-md.md)] 存取啟用 FILESTREAM。<br /><br /> 2=針對 [!INCLUDE[tsql](../../includes/tsql-md.md)] 和檔案 I/O 資料流存取啟用 FILESTREAM (不適用於叢集狀況)。<br /><br /> 3=允許遠端用戶端具有 FILESTREAM 資料的資料流存取權。|  
|FILESTREAM|/FILESTREAMSHARENAME<br /><br /> **選擇性**<br /><br /> 當 FILESTREAMLEVEL 大於 1 時則為 **必要參數** 。|指定即將儲存 FILESTREAM 資料之 Windows 共用的名稱。|  
|SQL Server 全文檢索|/FTSVCACCOUNT<br /><br /> **選擇性**|指定全文檢索篩選啟動器服務的帳戶。<br /><br /> 在 [!INCLUDE[firstref_longhorn](../../includes/firstref-longhorn-md.md)] 或更新版本中會忽略此參數。 ServiceSID 是用來協助保護 SQL Server 與全文檢索篩選背景程式之間的通訊。<br /><br /> 如果沒有提供這些值，就會停用全文檢索篩選啟動器服務。 您必須使用 SQL Server 控制管理員來變更服務帳戶並啟用全文檢索功能。<br /><br /> 預設值：本機服務帳戶|  
|SQL Server 全文檢索|/FTSVCPASSWORD<br /><br /> **選擇性**|指定全文檢索篩選啟動器服務的密碼。<br /><br /> 在 [!INCLUDE[firstref_longhorn](../../includes/firstref-longhorn-md.md)] 或更新版本中會忽略此參數。 使用受控服務帳戶、虛擬帳戶或內建帳戶時，可以省略此參數。|  
|[!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)]|/ISSVCACCOUNT<br /><br /> **必要**|指定 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)]的帳戶。<br /><br /> 預設值：NT AUTHORITY\NETWORK SERVICE|  
|[!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)]|/ISSVCPASSWORD<br /><br /> [必要](#Accounts)|指定 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 密碼。 使用受控服務帳戶、虛擬帳戶或內建帳戶時，可以省略此參數。|  
|[!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)]|/ISSVCStartupType<br /><br /> **選擇性**|指定 [服務的](#Accounts) 啟動 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 模式。|  
|[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]|/RSINSTALLMODE<br /><br /> **只適用於僅限檔案模式。**| 從 SQL Server 2017 開始不再適用。  指定 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]的安裝模式。|  
|[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]|/RSSVCACCOUNT<br /><br /> **必要**| 從 SQL Server 2017 開始不再適用。  指定 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]的啟動帳戶。|  
|[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]|/RSSVCPASSWORD<br /><br /> [必要](#Accounts)| 從 SQL Server 2017 開始不再適用。  指定 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] 服務之啟動帳戶的密碼。 使用受控服務帳戶、虛擬帳戶或內建帳戶時，可以省略此參數。|  
|[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]|/RSSVCStartupType<br /><br /> **選擇性**| 從 SQL Server 2017 開始不再適用。 指定 [的](#Accounts) 啟動 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]模式。 支援的值：<br /><br /> **自動**<br /><br /> **Disabled**<br /><br /> **手動**|  
  
 建議您改用 Service SID，而非網域群組。 
  
###### <a name="sample-syntax"></a>範例語法：  
 執行 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 和 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]容錯移轉叢集進階安裝狀況的「準備」步驟。 
  
 在命令提示字元執行下列命令，以便準備預設執行個體：  
  
```  
setup.exe /q /ACTION=PrepareFailoverCluster /InstanceName=MSSQLSERVER /Features=AS,SQL /INDICATEPROGRESS /ASSVCACCOUNT="<DomainName\UserName>" /ASSVCPASSWORD="xxxxxxxxxxx" /SQLSVCACCOUNT="<DomainName\UserName>" /SQLSVCPASSWORD="xxxxxxxxxxx" /AGTSVCACCOUNT="<DomainName\UserName>" /AGTSVCPASSWORD="xxxxxxxxxxx" /IACCEPTSQLSERVERLICENSETERMS  
```  
  
 在命令提示字元執行下列命令，以便準備具名執行個體：  
  
```  
setup.exe /q /ACTION=PrepareFailoverCluster /InstanceName="<Insert Instance name>" /Features=AS,SQL /INDICATEPROGRESS /ASSVCACCOUNT="<DomainName\UserName>" /ASSVCPASSWORD="xxxxxxxxxxx" /SQLSVCACCOUNT="<DomainName\UserName>" /SQLSVCPASSWORD="xxxxxxxxxxx" /AGTSVCACCOUNT="<DomainName\UserName>" /AGTSVCPASSWORD="xxxxxxxxxxx" /IACCEPTSQLSERVERLICENSETERMS  
```  
  
#### <a name="complete-failover-cluster-parameters"></a>完成容錯移轉叢集參數  
 您可以使用下表中的參數來開發容錯移轉叢集完成的命令列指令碼。 這是進階容錯移轉叢集安裝選項中的第二個步驟。 在所有容錯移轉叢集節點上執行準備作業之後，您就可以在擁有共用磁碟的節點上執行這個命令。 如需詳細資訊，請參閱 [AlwaysOn 容錯移轉叢集執行個體 &#40;SQL Server&#41;](../../sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server.md)。 
  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 元件|參數|描述|  
|-----------------------------------------|---------------|-----------------|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/ACTION<br /><br /> **必要**|指出容錯移轉叢集完成工作流程的必要參數。<br /><br /> 支援的值：**CompleteFailoverCluster**|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/ENU<br /><br /> **選擇性**|當安裝媒體包含英文以及與作業系統對應之語言的語言套件時，使用此參數在當地語系化的作業系統上安裝英文版的 SQL Server。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/FAILOVERCLUSTERGROUP<br /><br /> **選擇性**|指定要用於 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 容錯移轉叢集之資源群組的名稱。 它可以是現有叢集群組的名稱或新資源群組的名稱。<br /><br /> 預設值：<br /><br /> [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (\<InstanceName>)|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/CONFIGURATIONFILE<br /><br /> **選擇性**|指定要使用的 [ConfigurationFile](./install-sql-server-using-a-configuration-file.md) 。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/ERRORREPORTING<br /><br /> **選擇性**|在 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 中沒有任何作用。 <br/><br/>若要管理傳送錯誤意見反應給 Microsoft 的方式，請參閱[如何設定 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)]，將意見反應傳送給 Microsoft](https://support.microsoft.com/kb/3153756)。 <br/><br/>在較舊版本中，這會指定 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的錯誤報告。<br /><br /> 如需詳細資訊，請參閱＜ [Microsoft 錯誤報告服務隱私權聲明](../../sql-server/sql-server-privacy.md)＞。 支援的值：<br /><br /> 1=啟用<br /><br /> 0=停用|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/HELP、?<br /><br /> **選擇性**|顯示參數的使用方式選項。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/INDICATEPROGRESS<br /><br /> **選擇性**|指定要將詳細安裝程式記錄檔送到主控台。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/INSTANCENAME<br /><br /> **必要**|指定 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 執行個體名稱。<br /><br /> 如需詳細資訊，請參閱[執行個體設定](../../sql-server/install/instance-configuration.md)  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/PID<br /><br /> **選擇性**|指定 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]版的產品金鑰。 如果沒有指定這個參數，就會使用 Evaluation。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/Q 或 /QUIET <br /><br /> **選擇性**|指定安裝程式會在不含任何使用者介面的無訊息模式中執行。 這是自動安裝所使用的參數。 /Q 參數會覆寫 /QS 參數的輸入。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/QS 或 /QUIETSIMPLE <br /><br /> **選擇性**|指定安裝程式會執行並透過 UI 顯示進度，但是不接受任何輸入或顯示任何錯誤訊息。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/SQMREPORTING<br /><br /> **選擇性**|在 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 中沒有任何作用。 <br/><br/>若要管理傳送錯誤意見反應給 Microsoft 的方式，請參閱[如何設定 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)]，將意見反應傳送給 Microsoft](https://support.microsoft.com/kb/3153756)。 <br/><br/>在較舊版本中，這會指定 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的功能使用狀況報告。<br /><br />支援的值：<br /><br /> 1=啟用<br /><br /> 0=停用|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/HIDECONSOLE<br /><br /> **選擇性**|指定要隱藏或關閉主控台視窗。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/FAILOVERCLUSTERDISKS<br /><br /> **選擇性**|指定要包含在 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 容錯移轉叢集資源群組中的共用磁碟清單。<br /><br /> 預設值：<br /><br /> 第一個磁碟機會當做所有資料庫的預設磁碟機使用。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/FAILOVERCLUSTERIPADDRESSES<br /><br /> **必要**|指定編碼的 IP 位址。 編碼是以分號分隔 (;) 並遵循格式 \<IP Type>;\<address>;\<network name>;\<subnet mask>。 支援的 IP 類型包括 DHCP、IPv4 和 IPv6。<br />您可以指定多個容錯移轉叢集 IP 位址，每個位址之間隔一個空格。 請參閱下列範例：<br /><br /> `FAILOVERCLUSTERIPADDRESSES=DEFAULT`<br /><br /> `FAILOVERCLUSTERIPADDRESSES=IPv4;DHCP;ClusterNetwork1`<br /><br /> `FAILOVERCLUSTERIPADDRESSES=IPv4;172.16.0.0;ClusterNetwork1;172.31.255.255`<br /><br /> `FAILOVERCLUSTERIPADDRESSES=IPv6;DHCP;ClusterNetwork1`<br /><br /> `FAILOVERCLUSTERIPADDRESSES=IPv6;2001:db8:23:1002:20f:1fff:feff:b3a3;ClusterNetwork1`|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/FAILOVERCLUSTERNETWORKNAME<br /><br /> **必要**|針對新的 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 容錯移轉叢集指定網路名稱。 這個名稱是用來在網路上識別新的 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 容錯移轉叢集執行個體。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/CONFIRMIPDEPENDENCYCHANGE|表示同意將 IP 位址資源相依性設定為 OR，以使用多重子網路容錯移轉叢集。 如需詳細資訊，請參閱[建立新的 SQL Server 容錯移轉叢集 &#40;安裝程式&#41;](../../sql-server/failover-clusters/install/create-a-new-sql-server-failover-cluster-setup.md)。 支援的值：<br /><br /> 0 = False (預設值)<br /><br /> 1 = True|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/ASBACKUPDIR<br /><br /> **選擇性**|指定 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] 備份檔案的目錄。 預設值：<br /><br /> 64 位元上的 WOW 模式：`%Program Files(x86)%\Microsoft SQL Server\<INSTANCEDIR>\<ASInstanceID>\OLAP\Backup`<br /><br /> 所有其他安裝：`%Program Files%\Microsoft SQL Server\<INSTANCEDIR>\<ASInstanceID>\OLAP\Backup`|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/ASCOLLATION<br /><br /> **選擇性**|指定 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]的定序設定。<br /><br /> 預設值：**Latin1_General_CI_AS**|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/ASCONFIGDIR<br /><br /> **選擇性**|指定 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] 組態檔的目錄。 預設值：<br /><br /> 64 位元上的 WOW 模式：`%Program Files(x86)%\Microsoft SQL Server\<INSTANCEDIR>\<ASInstanceID>\OLAP\Config`<br /><br /> 所有其他安裝：`%Program Files%\Microsoft SQL Server\<INSTANCEDIR>\<ASInstanceID>\OLAP\Config`|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/ASDATADIR<br /><br /> **選擇性**|指定 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] 資料檔案的目錄。 預設值：<br /><br /> 64 位元上的 WOW 模式：`%Program Files(x86)%\Microsoft SQL Server\<INSTANCEDIR>\<ASInstanceID>\OLAP\Data`<br /><br /> 所有其他安裝：`%Program Files%\Microsoft SQL Server\<INSTANCEDIR>\<ASInstanceID>\OLAP\Data`|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/ASLOGDIR<br /><br /> **選擇性**|指定 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] 記錄檔的目錄。 預設值：<br /><br /> 64 位元上的 WOW 模式：`%Program Files(x86)%\Microsoft SQL Server\<INSTANCEDIR>\<ASInstanceID>\OLAP\Log`<br /><br /> 所有其他安裝：`%Program Files%\Microsoft SQL Server\<INSTANCEDIR>\<ASInstanceID>\OLAP\Log`|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/ASSERVERMODE<br /><br /> **選擇性**|指定 Analysis Services 執行個體的伺服器模式。 叢集情況中的有效值為 MULTIDIMENSIONAL 或 TABULAR。 **ASSERVERMODE** 區分大小寫。 所有值都必須以大寫形式表示。 如需有關有效值的詳細資訊，請參閱＜以表格模式安裝 Analysis Services＞。|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/ASSYSADMINACCOUNTS<br /><br /> **必要**|指定 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]的系統管理員認證。|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/ASTEMPDIR<br /><br /> **選擇性**|指定 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] 暫存檔的目錄。 預設值：<br /><br /> 64 位元上的 WOW 模式：`%Program Files(x86)%\Microsoft SQL Server\<INSTANCEDIR>\<ASInstanceID>\OLAP\Temp`<br /><br /> 所有其他安裝：`%Program Files%\Microsoft SQL Server\<INSTANCEDIR>\<ASInstanceID>\OLAP\Temp`|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/ASPROVIDERMSOLAP<br /><br /> **選擇性**|指定 MSOLAP 提供者是否可以在處理序中執行。<br /><br /> 預設值：1=啟用|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/INSTALLSQLDATADIR<br /><br /> **必要**|指定 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 資料檔案的資料目錄。<br /><br /> 您必須指定此資料目錄，而且它必須位於共用叢集磁碟上。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SAPWD<br /><br /> **當 /SECURITYMODE=SQL 時則為必要參數**|指定 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] **SA** 帳戶的密碼。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SECURITYMODE<br /><br /> **選擇性**|指定 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的安全性模式。<br /><br /> 如果沒有提供這個參數，就會支援僅限 Windows 驗證模式<br /><br /> 支援的值：**SQL**|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLBACKUPDIR<br /><br /> **選擇性**|指定備份檔的目錄。<br /><br /> 預設值：`<InstallSQLDataDir>\<SQLInstanceID>\MSSQL\Backup`|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLCOLLATION<br /><br /> **選擇性**|指定 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]的定序設定。<br /><br /> 預設值會根據您 Windows 作業系統的地區設定而異。 如需詳細資訊，請參閱＜ [安裝程式中的定序設定](../../relational-databases/collations/collation-and-unicode-support.md)＞。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLSYSADMINACCOUNTS<br /><br /> **必要**|您可以使用這個參數來提供登入，以便成為系統管理員 (sysadmin) 角色的成員。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLUSERDBDIR<br /><br /> **選擇性**|指定使用者資料庫之資料檔案的目錄。<br /><br /> 預設值：`<InstallSQLDataDir>\<SQLInstanceID>\MSSQL\Data`|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLUSERDBLOGDIR<br /><br /> **選擇性**|指定使用者資料庫之記錄檔的目錄。<br /><br /> 預設值：`<InstallSQLDataDir>\<SQLInstanceID>\MSSQL\Data`|  
|[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]|/RSINSTALLMODE<br /><br /> **適用於僅限檔案模式。**| 從 SQL Server 2017 開始不再適用。  指定 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]的安裝模式。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLTEMPDBDIR<br /><br /> **選擇性**|指定 tempdb 資料檔案的目錄。 指定多個目錄時，請以空格隔開這些目錄。 若指定多個目錄，tempdb 資料檔案將以循環配置資源方式跨多個目錄存放。<br /><br /> 預設值：`<InstallSQLDataDir>\<SQLInstanceID>\MSSQL\Data` (系統資料目錄)<br /><br /> 注意：此參數也會新增至 RebuildDatabase 案例。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLTEMPDBLOGDIR<br /><br /> **選擇性**|指定 tempdb 記錄檔的目錄。<br /><br /> 預設值：`<InstallSQLDataDir>\<SQLInstanceID>\MSSQL\Data` (系統資料目錄)<br /><br /> 注意:此參數也會新增至 RebuildDatabase 案例。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLTEMPDBFILECOUNT<br /><br /> **選擇性**|指定安裝程式要新增的 tempdb 資料檔案數目。 此值可以增加到與核心數目相同為止。 預設值：<br /><br /> [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)] 為 1 個<br /><br /> 所有其他版本為 8 個或與核心數目相同 (兩者取其較低者)。<br /><br /> **重要：** tempdb 的主要資料庫檔案仍為 tempdb.mdf。 其他的 tempdb 檔案會命名為 tempdb_mssql_#.ndf，其中 # 代表唯一的數字，該數字是在安裝期間替每個額外的 tempdb 資料庫檔案所建立。 此命名慣例旨在使其為唯一。 解除安裝 SQL Server 的執行個體會刪除命名慣例為 tempdb_mssql_ #.ndf 的檔案。 請勿將 tempdb_mssql_\*.ndf 命名慣例用於使用者資料庫檔案。<br /><br /> **警告：** [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)] 不支援設定此參數。 安裝程式只會安裝 1 個 tempdb 資料檔案。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLTEMPDBFILESIZE<br /><br /> **選擇性**|在 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 中引進。 指定每個 tempdb 資料檔案的初始大小。<br/><br/>預設值 = 8 MB。<br/><br/>最小值 = 8 MB。<br/><br/>最大值 = 1024 MB ([!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 為 262,144 MB)。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLTEMPDBFILEGROWTH<br /><br /> **選擇性**|指定每個 tempdb 資料檔案的檔案成長增量 (MB)。 0 值指出自動成長是關閉的，且不允許其他空間。 安裝程式允許的大小上限為 1024。<br /><br /> 預設值：64<br /><br /> 允許的範圍：最小值 = 0，最大值 = 1024|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLTEMPDBLOGFILESIZE<br /><br /> **選擇性**|指定 tempdb 記錄檔的初始大小 (MB)。 安裝程式允許的大小上限為 1024。 <br /> 預設值：<br /><br /> [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)] 為 4 個<br /><br /> 所有其他版本為 8 個<br /><br /> 允許的範圍：最小值 = 預設值 (4 或 8)，最大值 = 1024|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLTEMPDBLOGFILEGROWTH<br /><br /> **選擇性**|在 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 中引進。 指定每個 tempdb 記錄檔的初始大小。<br/><br/>預設值 = [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)] 為 4 MB，所有其他版本為 8 MB。<br/><br/>最小值= (4 MB 或 8 MB)。<br/><br/>最大值 = 1024 MB ([!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 為 262,144 MB)。|  
  
###### <a name="sample-syntax"></a>範例語法：  
 執行 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 和 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]容錯移轉叢集進階安裝狀況的「完成」步驟。 在即將成為容錯移轉叢集中使用中節點的電腦上執行下列命令，讓它成為可用。 您必須針對在 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] 容錯移轉叢集中擁有共用磁碟的節點執行 "CompleteFailoverCluster" 動作。 
  
 在命令提示字元執行下列命令，以便完成預設執行個體的容錯移轉叢集安裝：  
  
```  
setup.exe /q /ACTION=CompleteFailoverCluster /InstanceName=MSSQLSERVER /INDICATEPROGRESS /ASSYSADMINACCOUNTS="<DomainName\Username>" /ASDATADIR=<Drive>:\OLAP\Data /ASLOGDIR=<Drive>:\OLAP\Log /ASBACKUPDIR=<Drive>:\OLAP\Backup /ASCONFIGDIR=<Drive>:\OLAP\Config /ASTEMPDIR=<Drive>:\OLAP\Temp /FAILOVERCLUSTERDISKS="<Cluster Disk Resource Name - for example, 'Disk S:'>:" /FAILOVERCLUSTERNETWORKNAME="<Insert FOI Network Name>" /FAILOVERCLUSTERIPADDRESSES="IPv4;xx.xxx.xx.xx;Cluster Network;xxx.xxx.xxx.x" /FAILOVERCLUSTERGROUP="MSSQLSERVER" /INSTALLSQLDATADIR="<Drive>:\<Path>\MSSQLSERVER" /SQLCOLLATION="SQL_Latin1_General_CP1_CS_AS" /SQLSYSADMINACCOUNTS="<DomainName\UserName>"  
```  
  
 在命令提示字元執行下列命令，以便完成具名執行個體的容錯移轉叢集安裝：  
  
```  
setup.exe /q /ACTION=CompleteFailoverCluster /InstanceName="<Insert Instance Name>" /INDICATEPROGRESS /ASSYSADMINACCOUNTS="<DomainName\UserName>" /ASDATADIR=<Drive>:\KATMAI\Data /ASLOGDIR=<drive>:\KATMAI\Log /ASBACKUPDIR=<Drive>:\KATMAI\Backup /ASCONFIGDIR=<Drive>:\KATMAI\Config /ASTEMPDIR=<Drive>:\KATMAI\Temp /FAILOVERCLUSTERDISKS="<Cluster Disk Resource Name - for example, 'Disk S:'>" /FAILOVERCLUSTERNETWORKNAME="CompNamedFOI" /FAILOVERCLUSTERIPADDRESSES="IPv4;xx.xxx.xx.xx;ClusterNetwork1;xxx.xxx.xxx.x" /FAILOVERCLUSTERGROUP="<Insert New Group Name>" /INSTALLSQLDATADIR="<Drive>:\<Path>\MSSQLSERVER_KATMAI" /SQLCOLLATION="SQL_Latin1_General_CP1_CS_AS" /SQLSYSADMINACCOUNTS="<DomainName\Username>"  
```  
  
#### <a name="upgrade-failover-cluster-parameters"></a>升級容錯移轉叢集參數  
 您可以使用下表中的參數來開發容錯移轉叢集升級的命令列指令碼。 如需詳細資訊，請參閱[升級 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 容錯移轉叢集執行個體 &#40;安裝程式&#41;](../../sql-server/failover-clusters/windows/upgrade-a-sql-server-failover-cluster-instance-setup.md) 和 [AlwaysOn 容錯移轉叢集執行個體 &#40;SQL Server&#41;](../../sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server.md)。 
  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 元件|參數|描述|  
|-----------------------------------------|---------------|-----------------|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/ACTION<br /><br /> **必要**|指出安裝工作流程的必要參數。<br /><br /> 支援的值：**升級**|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/IACCEPTSQLSERVERLICENSETERMS<br /><br /> **只有當您針對自動安裝指定了 /Q 或 /QS 參數時，才需要使用此參數。**|確認接受授權條款的必要參數。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/ENU<br /><br /> **選擇性**|當安裝媒體包含英文以及與作業系統對應之語言的語言套件時，使用此參數在當地語系化的作業系統上安裝英文版的 SQL Server。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/*UpdateEnabled*<br /><br /> **選擇性**|指定 SQL Server 安裝程式是否應探索及包含產品更新。 有效值為 True 和 False 或 1 和 0。 根據預設，SQL Server 安裝程式會包含找到的更新。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/*UpdateSource*<br /><br /> **選擇性**|指定 SQL Server 安裝程式將取得產品更新的位置。 有效值為 "MU"，表示搜尋 [!INCLUDE[msCoName](../../includes/msconame-md.md)] 更新、有效資料夾路徑、相對路徑 (例如 `.\MyUpdates`) 或 UNC 共用。 根據預設，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式會搜尋 [!INCLUDE[msCoName](../../includes/msconame-md.md)] Update 或透過 Windows Server Update Services 搜尋 Windows Update Service。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/CONFIGURATIONFILE<br /><br /> **選擇性**|指定要使用的 [ConfigurationFile](./install-sql-server-using-a-configuration-file.md) 。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/ERRORREPORTING<br /><br /> **選擇性**|在 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 中沒有任何作用。 <br/><br/>若要管理傳送錯誤意見反應給 Microsoft 的方式，請參閱[如何設定 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)]，將意見反應傳送給 Microsoft](https://support.microsoft.com/kb/3153756)。 <br/><br/>在較舊版本中，這會指定 SQL Server 的錯誤報告。<br /><br /> 如需詳細資訊，請參閱＜ [Microsoft 錯誤報告服務隱私權聲明](../../sql-server/sql-server-privacy.md)＞。 支援的值：<br /><br /> 0=停用<br /><br /> 1=啟用|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/HELP、?<br /><br /> **選擇性**|顯示參數的使用方式選項。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/INDICATEPROGRESS<br /><br /> **選擇性**|指定要將詳細安裝程式記錄檔送到主控台。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/ INSTANCEDIR<br /><br /> **選擇性**|指定共用元件的非預設安裝目錄。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/INSTANCEID<br /><br /> **當您從 [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 或更新版本升級時為必要參數。**<br /><br /> **當您從 [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] 升級時為選擇性參數**|指定 [InstanceID](#InstanceID)的非預設值。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/INSTANCENAME<br /><br /> **必要**|指定 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 執行個體名稱。<br /><br /> 如需詳細資訊，請參閱＜ [Instance Configuration](../../sql-server/install/instance-configuration.md)＞。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/PID<br /><br /> **選擇性**|指定 SQL Server 版的產品金鑰。 如果沒有指定這個參數，就會使用 Evaluation。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/Q 或 /QUIET <br /><br /> **選擇性**|指定安裝程式會在不含任何使用者介面的無訊息模式中執行。 這是自動安裝所使用的參數。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/SQMREPORTING<br /><br /> **選擇性**|在 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 中沒有任何作用。 在較舊版本中，這會指定 SQL Server 的功能使用狀況報告。<br /><br />支援的值：<br /><br /> 0=停用<br /><br /> 1=啟用|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/HIDECONSOLE<br /><br /> **選擇性**|指定要隱藏或關閉主控台視窗。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/FAILOVERCLUSTERROLLOWNERSHIP|指定升級期間的 [容錯移轉行為](#RollOwnership) 。|  
|SQL Server Browser 服務|/BROWSERSVCSTARTUPTYPE<br /><br /> **選擇性**|指定 SQL Server Browser 服務的 [啟動](#Accounts) 模式。 支援的值：<br /><br /> **自動**<br /><br /> **Disabled**<br /><br /> **手動**|  
|SQL Server 全文檢索|/FTUPGRADEOPTION<br /><br /> **選擇性**|指定全文檢索目錄升級選項。 支援的值：<br /><br /> **REBUILD**<br /><br /> **RESET**<br /><br /> **IMPORT**|  
|[!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)]|/ISSVCACCOUNT<br /><br /> **必要**|指定 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)]的帳戶。<br /><br /> 預設值：NT AUTHORITY\NETWORK SERVICE|  
|[!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)]|/ISSVCPASSWORD<br /><br /> [必要](#Accounts)|指定 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 密碼。 使用受控服務帳戶、虛擬帳戶或內建帳戶時，可以省略此參數。|  
|[!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)]|/ISSVCStartupType<br /><br /> **選擇性**|指定 [服務的](#Accounts) 啟動 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 模式。|  
|[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]|/RSUPGRADEDATABASEACCOUNT<br /><br /> **選擇性**|只有在升級 2008 R2 或更早版本的 SharePoint 模式報表伺服器時，才會使用此屬性。 針對使用舊版 SharePoint 模式架構 (此架構已在 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] 中變更) 的報表伺服器，會執行其他升級作業。 如果此選項未隨附於命令列安裝，則會使用舊報表伺服器執行個體的預設服務帳戶。 如果使用這個屬性，請使用 **/RSUPGRADEPASSWORD** 屬性提供此帳戶的密碼。|  
|[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]|/RSUPGRADEPASSWORD<br /><br /> **選擇性**|現有報表伺服器服務帳戶的密碼。|  
  
####  <a name="add-node-parameters"></a><a name="AddNode"></a> 加入節點參數  
 您可以使用下表中的參數來開發 AddNode 的命令列指令碼。 
  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 元件|參數|描述|  
|-----------------------------------------|---------------|-----------------|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/ACTION<br /><br /> **必要**|指出 AddNode 工作流程的必要參數。<br /><br /> 支援的值：**AddNode**|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/IACCEPTSQLSERVERLICENSETERMS<br /><br /> **只有當您針對自動安裝指定了 /Q 或 /QS 參數時，才需要使用此參數。**|確認接受授權條款的必要參數。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/ENU<br /><br /> **選擇性**|當安裝媒體包含英文以及與作業系統對應之語言的語言套件時，使用此參數在當地語系化的作業系統上安裝英文版的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/*UpdateEnabled*<br /><br /> **選擇性**|指定 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式是否應該探索及包含產品更新。 有效值為 True 和 False 或 1 和 0。 根據預設，SQL Server 安裝程式會包含找到的更新。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/*UpdateSource*<br /><br /> **選擇性**|指定 SQL Server 安裝程式將取得產品更新的位置。 有效值為 "MU"，表示搜尋 [!INCLUDE[msCoName](../../includes/msconame-md.md)] 更新、有效資料夾路徑、相對路徑 (例如 `.\MyUpdates`) 或 UNC 共用。 根據預設，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式會搜尋 [!INCLUDE[msCoName](../../includes/msconame-md.md)] Update 或透過 Windows Server Update Services 搜尋 Windows Update Service。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/CONFIGURATIONFILE<br /><br /> **選擇性**|指定要使用的 [ConfigurationFile](./install-sql-server-using-a-configuration-file.md) 。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/HELP、?<br /><br /> **選擇性**|顯示參數的使用方式選項。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/INDICATEPROGRESS<br /><br /> **選擇性**|指定要將詳細安裝程式記錄檔送到主控台。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/INSTANCENAME<br /><br /> **必要**|指定 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 執行個體名稱。<br /><br /> 如需詳細資訊，請參閱＜ [Instance Configuration](../../sql-server/install/instance-configuration.md)＞。|  
|PolyBase|/PBENGSVCACCOUNT<br /><br /> **選擇性**|指定引擎服務的帳戶。 預設是 **NT Authority\NETWORK SERVICE**。|  
|PolyBase|/PBDMSSVCPASSWORD<br /><br /> **選擇性**|指定引擎服務帳戶的密碼。 使用受控服務帳戶、虛擬帳戶或內建帳戶時，可以省略此參數。|  
|PolyBase|/PBENGSVCSTARTUPTYPE<br /><br /> **選擇性**|指定 PolyBase 引擎服務的啟動模式：自動 (預設)、停用及手動。|  
|PolyBase|/PBPORTRANGE<br /><br /> **選擇性**|為 PolyBase 服務指定含至少 6 個連接埠的連接埠範圍。 範例：<br /><br /> `/PBPORTRANGE=16450-16460`|  
|PolyBase|/PBSCALEOUT<br /><br /> **選擇性**|指定 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 執行個體是否會用作 PolyBase 向外延展計算群組的一部分。 支援的值：**True**、**False**|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/PID<br /><br /> **選擇性**|指定 SQL Server 版的產品金鑰。 如果沒有指定這個參數，就會使用 Evaluation。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/Q 或 /QUIET <br /><br /> **選擇性**|指定安裝程式會在不含任何使用者介面的無訊息模式中執行。 這是自動安裝所使用的參數。 /Q 參數會覆寫 /QS 參數的輸入。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/QS 或 /QUIETSIMPLE <br /><br /> **選擇性**|指定安裝程式會執行並透過 UI 顯示進度，但是不接受任何輸入或顯示任何錯誤訊息。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/HIDECONSOLE<br /><br /> **選擇性**|指定要隱藏或關閉主控台視窗。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/FAILOVERCLUSTERIPADDRESSES<br /><br /> **必要**|指定編碼的 IP 位址。 編碼是以分號分隔 (;) 並遵循格式 \<IP Type>;\<address>;\<network name>;\<subnet mask>。 支援的 IP 類型包括 DHCP、IPv4 和 IPv6。<br />您可以指定多個容錯移轉叢集 IP 位址，每個位址之間隔一個空格。 請參閱下列範例：<br /><br /> `FAILOVERCLUSTERIPADDRESSES=DEFAULT`<br /><br /> `FAILOVERCLUSTERIPADDRESSES=IPv4;DHCP;ClusterNetwork1`<br /><br /> `FAILOVERCLUSTERIPADDRESSES=IPv4;172.16.0.0;ClusterNetwork1;172.31.255.255`<br /><br /> `FAILOVERCLUSTERIPADDRESSES=IPv6;DHCP;ClusterNetwork1`<br /><br /> `FAILOVERCLUSTERIPADDRESSES=IPv6;2001:db8:23:1002:20f:1fff:feff:b3a3;ClusterNetwork1`<br /><br /> <br /><br /> 如需詳細資訊，請參閱[在 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 容錯移轉叢集中新增或移除節點 &#40;安裝程式&#41;](../../sql-server/failover-clusters/install/add-or-remove-nodes-in-a-sql-server-failover-cluster-setup.md)。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/CONFIRMIPDEPENDENCYCHANGE<br /><br /> **必要**|表示同意將 IP 位址資源相依性設定為 OR，以使用多重子網路容錯移轉叢集。 如需詳細資訊，請參閱[在 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 容錯移轉叢集中新增或移除節點 &#40;安裝程式&#41;](../../sql-server/failover-clusters/install/add-or-remove-nodes-in-a-sql-server-failover-cluster-setup.md)。 支援的值：<br /><br /> 0 = False (預設值)<br /><br /> 1 = True|  
|SQL Server Agent|/AGTSVCACCOUNT<br /><br /> **必要**|指定 SQL Server Agent 服務的帳戶。|  
|SQL Server Agent|/AGTSVCPASSWORD<br /><br /> [必要](#Accounts)|指定 SQL Server Agent 服務帳戶的密碼。 使用受控服務帳戶、虛擬帳戶或內建帳戶時，可以省略此參數。|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/ASSVCACCOUNT<br /><br /> **必要**|指定 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] 服務的帳戶。|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/ASSVCPASSWORD<br /><br /> [必要](#Accounts)|指定 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] 服務的密碼。 使用受控服務帳戶、虛擬帳戶或內建帳戶時，可以省略此參數。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLSVCACCOUNT<br /><br /> **必要**|指定 SQL Server 服務的啟動帳戶。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLSVCPASSWORD<br /><br /> [必要](#Accounts)|指定 SQLSVCACCOUNT 的密碼。 使用受控服務帳戶、虛擬帳戶或內建帳戶時，可以省略此參數。|  
|[!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)]|/ISSVCPASSWORD<br /><br /> [必要](#Accounts)|指定 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 密碼。 使用受控服務帳戶、虛擬帳戶或內建帳戶時，可以省略此參數。|  
|[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]|/RSINSTALLMODE<br /><br /> **適用於僅限檔案模式**| 從 SQL Server 2017 開始不再適用。  指定 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]的安裝模式。|  
|[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]|/RSSVCPASSWORD<br /><br /> [必要](#Accounts)|從 SQL Server 2017 開始不再適用。  指定 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] 服務的啟動帳戶密碼。 使用受控服務帳戶、虛擬帳戶或內建帳戶時，可以省略此參數。|  
  
##### <a name="additional-notes"></a>其他注意事項：  
 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 和 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] 是唯一可感知叢集的元件。 其他功能無法感知叢集而且無法透過容錯移轉提供高可用性。 
  
###### <a name="sample-syntax"></a>範例語法：  
 將節點加入至具有 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 和 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]的現有容錯移轉叢集執行個體。 
  
```  
setup.exe /q /ACTION=AddNode /INSTANCENAME="<Insert Instance Name>" /SQLSVCACCOUNT="<SQL account that is used on other nodes>" /SQLSVCPASSWORD="<password for SQL account>" /AGTSVCACCOUNT="<SQL Server Agent account that is used on other nodes>", /AGTSVCPASSWORD="<SQL Server Agent account password>" /ASSVCACCOUNT="<AS account that is used on other nodes>" /ASSVCPASSWORD="<password for AS account>" /INDICATEPROGRESS /IACCEPTSQLSERVERLICENSETERMS /FAILOVERCLUSTERIPADDRESSES="IPv4;xx.xxx.xx.xx;ClusterNetwork1;xxx.xxx.xxx.x" /CONFIRMIPDEPENDENCYCHANGE=0  
```  
  
#### <a name="remove-node-parameters"></a>移除節點參數  
 您可以使用下表中的參數來開發 若要解除安裝容錯移轉叢集，您必須在每個容錯移轉叢集節點上執行 RemoveNode。 如需詳細資訊，請參閱 [AlwaysOn 容錯移轉叢集執行個體 &#40;SQL Server&#41;](../../sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server.md)。 
  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 元件|參數|描述|  
|-----------------------------------------|---------------|-----------------|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/ACTION<br /><br /> **必要**|指出 RemoveNode 工作流程的必要參數。<br /><br /> 支援的值：**RemoveNode**|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/CONFIGURATIONFILE<br /><br /> **選擇性**|指定要使用的 [ConfigurationFile](./install-sql-server-using-a-configuration-file.md) 。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/HELP、?<br /><br /> **選擇性**|顯示參數的使用方式選項。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/INDICATEPROGRESS<br /><br /> **選擇性**|指定要將詳細安裝程式記錄檔送到主控台。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/INSTANCENAME<br /><br /> **必要**|指定 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 執行個體名稱。<br /><br /> 如需詳細資訊，請參閱＜ [Instance Configuration](../../sql-server/install/instance-configuration.md)＞。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/Q 或 /QUIET <br /><br /> **選擇性**|指定安裝程式會在不含任何使用者介面的無訊息模式中執行。 這是自動安裝所使用的參數。 /Q 參數會覆寫 /QS 參數的輸入。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/QS 或 /QUIETSIMPLE<br /><br /> **選擇性**|指定安裝程式會執行並透過 UI 顯示進度，但是不接受任何輸入或顯示任何錯誤訊息。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/HIDECONSOLE<br /><br /> **選擇性**|指定要隱藏或關閉主控台視窗。|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 安裝程式控制|/CONFIRMIPDEPENDENCYCHANGE<br /><br /> **必要**|表示同意將 IP 位址資源相依性從 OR 設定為 AND，以使用多重子網路容錯移轉叢集。 如需詳細資訊，請參閱[在 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 容錯移轉叢集中新增或移除節點 &#40;安裝程式&#41;](../../sql-server/failover-clusters/install/add-or-remove-nodes-in-a-sql-server-failover-cluster-setup.md)。 支援的值：<br /><br /> 0 = False (預設值)<br /><br /> 1 = True|  
  
###### <a name="sample-syntax"></a>範例語法：  
 叢具有 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 和 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]的現有容錯移轉叢集執行個體移除節點。 
  
```  
setup.exe /q /ACTION=RemoveNode /INSTANCENAME="<Insert Instance Name>" [/INDICATEPROGRESS] /CONFIRMIPDEPENDENCYCHANGE=0  
```  
  
##  <a name="service-account-parameters"></a><a name="Accounts"></a> 服務帳戶參數  
 您可以使用內建帳戶、本機帳戶或網域帳戶來設定 SQL Server 服務。 
  
> [!NOTE] 
> 當您使用受管理服務帳戶、虛擬帳戶或內建帳戶時，不應該指定對應的密碼參數。 如需這些服務帳戶的詳細資訊，請參閱 [設定 Windows 服務帳戶和權限](../../database-engine/configure-windows/configure-windows-service-accounts-and-permissions.md) 中的 **可搭配使用的新帳戶 [!INCLUDE[win7](../../includes/win7-md.md)] 和 [!INCLUDE[winserver2008r2](../../includes/winserver2008r2-md.md)]** 一節。 
  
 如需服務帳戶組態的詳細資訊，請參閱 [設定 Windows 服務帳戶和權限](../../database-engine/configure-windows/configure-windows-service-accounts-and-permissions.md)。 
  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 元件|帳戶參數|密碼參數|啟動類型|  
|-----------------------------------------|-----------------------|------------------------|------------------|  
|SQL Server Agent|/AGTSVCACCOUNT|/AGTSVCPASSWORD|/AGTSVCSTARTUPTYPE|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]|/ASSVCACCOUNT|/ASSVCPASSWORD|/ASSVCSTARTUPTYPE|  
|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|/SQLSVCACCOUNT|/SQLSVCPASSWORD|/SQLSVCSTARTUPTYPE|  
|[!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)]|/ISSVCACCOUNT|/ISSVCPASSWORD|/ISSVCStartupType|  
|[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]|/RSSVCACCOUNT|/RSSVCPASSWORD|/RSSVCStartupType|  
  
  > [!NOTE]
  > 已從 SQL Server 2017 移除 Reporting Services 功能。 SQL Server Reporting Services 的帳戶參數僅適用於 SQL Server 2017 之前版本。 


##  <a name="feature-parameters"></a><a name="Feature"></a> 功能參數  
 若要安裝特定功能，請使用 /FEATURES 參數，然後指定下表中的父功能或功能值。 如需 SQL Server 版本支援的功能清單，請參閱 [[!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 的版本及支援功能](../../sql-server/editions-and-components-of-sql-server-2016.md)、[[!INCLUDE[ssSQLv14](../../includes/sssqlv14-md.md)] 的版本及支援功能](../../sql-server/editions-and-components-of-sql-server-2017.md)或 [[!INCLUDE[ssSQLv15](../../includes/sssqlv15-md.md)] 的版本及支援功能](../../sql-server/editions-and-components-of-sql-server-version-15.md)。
  
|父功能參數|功能參數|描述|  
|:---|:---|:---|  
|SQL||安裝 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]、複寫、全文檢索和 [!INCLUDE[ssDQSServer](../../includes/ssdqsserver-md.md)]。|  
||SQLEngine|只安裝 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]。|  
||複寫|安裝 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]時一併安裝複寫元件。|  
||FullText|安裝 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]時一併安裝全文檢索元件。|  
||DQ|複製完成 [!INCLUDE[ssDQSServer](../../includes/ssdqsserver-md.md)] 安裝所需的檔案。 在完成 SQL Server 安裝之後，您必須執行 DQSInstaller.exe 來完成 [!INCLUDE[ssDQSServer](../../includes/ssdqsserver-md.md)] 安裝。 如需詳細資訊，請參閱 [執行 DQSInstaller.exe 完成 Data Quality Server 安裝](../../data-quality-services/install-windows/run-dqsinstaller-exe-to-complete-data-quality-server-installation.md)。 這樣也會安裝 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]。|  
||PolyBase |安裝 PolyBase 元件。|
||PolyBaseCore | 從 SQL Server 2019 開始，與 **PolyBase** 配對來安裝 Polybase 技術，讓您能夠使用標準 T-SQL 陳述式，跨 Oracle、Teradata、SQL Server 及其他關聯式和非關聯式資料進行真正的整合查詢。 |
|| PolyBaseJava | 從 SQL Server 2019 開始，與 **PolyBase** 配對來安裝 PolyBase Java 連接器，讓您能夠使用標準 T-SQL 陳述式，跨 HDFS 資料進行真正的整合查詢。
||AdvancedAnalytics |安裝 [SQL Server 機器學習服務](../../machine-learning/install/sql-machine-learning-services-windows-install.md)或 [SQL Server 2016 R Services (資料庫內)](../../machine-learning/install/sql-r-services-windows-install.md)。|  
||SQL_INST_MR |適用於 [SQL Server 機器學習服務](../../machine-learning/install/sql-machine-learning-services-windows-install.md) (2017 或更新版本) 和 [SQL Server 2016 R Services (資料庫內)](../../machine-learning/install/sql-r-services-windows-install.md)。 與 **AdvancedAnalytics** 配對來安裝 R Open 與專屬的 R 套件。|  
||SQL_INST_MPY|適用於 [SQL Server 機器學習服務](../../machine-learning/install/sql-machine-learning-services-windows-install.md) (2017 或更新版本)。 與 **AdvancedAnalytics** 配對來安裝 Anaconda 和專屬的 Python 套件。|  
||SQL_INST_JAVA |適用於 [SQL Server Java 語言延伸模組](../../language-extensions/install/windows-java.md) (2019 和更新版本)。 與 **AdvancedAnalytics** 配對來安裝延伸模組，以使用標準 T-SQL 陳述式來與 Java 整合。|  
|AS||安裝所有 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] 元件。|  
|RS||安裝所有 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] 元件。 從 SQL Server 2017 開始移除。 |  
|RS_SHP||安裝 SharePoint 的 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] 元件。 從 SQL Server 2017 開始移除。|  
|RS_SHPWFE||安裝 SharePoint 產品的 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] 增益集。 從 SQL Server 2017 開始移除。 |  
|DQC||安裝 [!INCLUDE[ssDQSClient](../../includes/ssdqsclient-md.md)]。|  
|IS||安裝所有 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 元件。|  
||IS_Master|包含適用於 Integration Services 擴增的擴增主機。| 
||IS_Worker|包含適用於 Integration Services 擴增的擴增背景工作角色。| 
|MDS||安裝 [!INCLUDE[ssMDSshort](../../includes/ssmdsshort-md.md)]。|  
|SQL_SHARED_MPY||安裝適用於 [SQL Server 2017 Machine Learning Server (獨立式)](../../machine-learning/install/sql-machine-learning-standalone-windows-install.md) 的 Python 套件 |  
|SQL_SHARED_MR||安裝適用於 [SQL Server 2016 R Server (獨立式)](../../machine-learning/install/sql-machine-learning-standalone-windows-install.md) 或 [SQL Server Machine Learning Server (獨立式)](../../machine-learning/install/sql-machine-learning-standalone-windows-install.md) 的 R 套件 |  
|工具*||安裝用戶端工具和《SQL Server 線上叢書》元件。|  
||BC|安裝回溯相容性元件。|  
||Conn|安裝連接元件。|
||DREPLAY_CTLR|安裝 Distributed Replay Controller|  
||DREPLAY_CLT|安裝 Distributed Replay Client|  
||SNAC_SDK|安裝適用於 [!INCLUDE[msCoName](../../includes/msconame-md.md)] SQL Server Native Client 的 SDK|  
||SDK|安裝軟體開發套件。|  
||LocalDB**|安裝 LocalDB，這是專供程式開發人員使用的 [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)] 執行模式。|  

*[!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] (SSMS) 現在位於與 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 安裝程式不同的獨立安裝程式中。 如需詳細資料，請參閱[安裝 SQL Server Management Studio](../../ssms/download-sql-server-management-studio-ssms.md)。

### <a name="feature-parameter-examples"></a>功能參數範例：
  
|參數和值|描述| 
|---------------|-----------------|  
|/FEATURES=SQLEngine|安裝不含複寫和全文檢索的 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 。|  
|/FEATURES=SQLEngine,FullText|安裝 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 和全文檢索。|  
|/FEATURES=SQL,Tools|安裝完整的 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 和所有工具。|  
|/FEATURES=BOL|安裝《SQL Server 線上叢書》元件，以檢視並管理說明內容。|  
|/FEATURES=SQLEngine,PolyBase|安裝 PolyBase 引擎。|  
  
##  <a name="role-parameters"></a><a name="RoleParameters"></a> 角色參數

安裝程式角色或 /Role 參數是用來安裝預先設定的功能選項。 SSAS 角色會在現有 SharePoint 伺服器陣列或未設定的新伺服器陣列中安裝 SSAS 執行個體。 為支援每個狀況，提供兩個安裝程式角色。 您一次只能選擇一個要安裝的安裝程式角色。 如果您選擇安裝程式角色，安裝程式會安裝屬於該角色的功能與元件。 您無法改變為該角色所指定的功能與元件。 如需有關如何使用功能角色參數的詳細資訊，請參閱 [從命令提示字元安裝 Power Pivot](/analysis-services/instances/install-windows/install-analysis-services-in-power-pivot-mode)。

AllFeatures_WithDefaults 角色是 [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)] 版本的預設行為，而且會減少向使用者顯示的對話方塊數目。 安裝非 [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)]的 SQL Server 版本時，可以從命令列指定該角色。

|角色|描述|安裝...|  
|----------|-----------------|---------------|  
|SPI_AS_ExistingFarm|在現有的 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] 伺服陣列或獨立伺服器上，將 [!INCLUDE[ssGemini](../../includes/ssgemini-md.md)] 安裝為 [!INCLUDE[SPS2010](../../includes/sps2010-md.md)] 具名執行個體。|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] 計算引擎已針對記憶體內部資料儲存和處理進行預先設定。<br /><br /> [!INCLUDE[ssGemini](../../includes/ssgemini-md.md)] 方案套件<br /><br /> [!INCLUDE[ssGeminiClient](../../includes/ssgeminiclient-md.md)] 的安裝程式<br /><br /> SQL Server 線上叢書|  
|SPI_AS_NewFarm|在全新且未設定的 Office [!INCLUDE[SPS2010](../../includes/sps2010-md.md)] 伺服陣列或獨立伺服器上，將 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] 和 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 安裝為 [!INCLUDE[ssGemini](../../includes/ssgemini-md.md)] 具名執行個體。 SQL Server 安裝程式將會在功能角色安裝期間設定伺服陣列。|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] 計算引擎已針對記憶體內部資料儲存和處理進行預先設定。<br /><br /> [!INCLUDE[ssGemini](../../includes/ssgemini-md.md)] 方案套件<br /><br /> SQL Server 線上叢書<br /><br /> [!INCLUDE[ssDE](../../includes/ssde-md.md)]<br /><br /> 組態工具<br /><br /> [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)]|  
|AllFeatures_WithDefaults|安裝適用於目前版本的所有功能。<br /><br /> 將目前的使用者新增至 SQL Server **系統管理員** 固定伺服器角色。<br /><br /> 在 [!INCLUDE[firstref_longhorn](../../includes/firstref-longhorn-md.md)] 或更新版本上，以及當作業系統不是網域控制站時， [!INCLUDE[ssDE](../../includes/ssde-md.md)]和 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] 預設會使用 NTAUTHORITY\NETWORK SERVICE 帳戶，而 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 預設會使用 NTAUTHORITY\NETWORK SERVICE 帳戶。<br /><br /> 在 [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)]版本中，預設會啟用這個角色。 針對其他所有版本，則不會啟用此角色，但是可以透過 UI 或使用命令列參數指定。|對於 [!INCLUDE[ssExpress](../../includes/ssexpress-md.md)]的版本，請僅安裝適用於此版本的功能。 對於其他版本，則安裝所有 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 功能。<br /><br /> **AllFeatures_WithDefaults** 參數可以結合會覆寫 **AllFeatures_WithDefaults** 參數設定的其他參數。 例如，使用 **AllFeatures_WithDefaults** 參數與 **/Features=RS** 參數會覆寫安裝所有功能的命令，而僅安裝 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]，但接受 **AllFeatures_WithDefaults** 參數則會使用 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]的預設服務帳戶。<br /><br /> 使用 **AllFeatures_WithDefaults** 參數與 **/ADDCURRENTUSERASSQLADMIN=FALSE** 時，不會以目前的使用者自動擴展提供對話方塊。 新增 **/AGTSVCACCOUNT** 和 **/AGTSVCPASSWORD** 來指定 SQL Server Agent 的服務帳戶與密碼。|

## <a name="controlling-failover-behavior-using-the-failoverclusterrollownership-parameter"></a><a name="RollOwnership"></a> 使用 /FAILOVERCLUSTERROLLOWNERSHIP 參數來控制容錯移轉行為  
若要將 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 容錯移轉叢集升級為 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)]，您必須在容錯移轉叢集節點上執行安裝程式 (從被動節點開始，一次一個)。 安裝程式會根據容錯移轉叢集執行個體中的節點總數以及已經升級的節點數目，判斷容錯移轉至升級節點的時機。 如果半數以上的節點都已經升級，安裝程式預設會讓系統容錯移轉至升級的節點。

若要在升級程序期間控制叢集節點的容錯移轉行為，請在命令提示字元中執行升級作業，然後在升級作業讓節點離線之前，使用 /FAILOVERCLUSTERROLLOWNERSHIP 參數來控制容錯移轉行為。 這個參數的用法如下所示：

- /FAILOVERCLUSTERROLLOWNERSHIP=0 不會將叢集擁有權 (移動群組) 轉交給升級的節點，而且在升級結束時，不會將這個節點新增至 SQL Server 叢集可能擁有者的清單。 

- /FAILOVERCLUSTERROLLOWNERSHIP=1 會將叢集擁有權 (移動群組) 轉交給升級的節點，而且在升級結束時，會將這個節點新增至 SQL Server 叢集可能擁有者的清單。 

- /FAILOVERCLUSTERROLLOWNERSHIP=2 是預設設定。 如果沒有指定這個參數，就會使用此設定。 這項設定表示 SQL Server 安裝程式將會視需要管理叢集擁有權 (移動群組)。 

##  <a name="instance-id-or-instanceid-configuration"></a><a name="InstanceID"></a> 執行個體識別碼或 InstanceID 組態

執行個體識別碼或 /InstanceID 參數是用來指定您可以安裝執行個體元件的位置以及執行個體的登錄路徑。 INSTANCEID 的值是字串而且應該為唯一。 

- SQL 執行個體識別碼：`MSSQLxx.<INSTANCEID>`

- AS 執行個體識別碼：`MSASxx.<INSTANCEID>`

- RS 執行個體識別碼：`MSRSxx.<INSTANCEID>`

可感知執行個體的元件會安裝至下列位置：  

`%Program Files%\Microsoft SQL Server\<SQLInstanceID>`

`%Program Files%\Microsoft SQL Server\<ASInstanceID>`

`%Program Files%\Microsoft SQL Server\<RSInstanceID>`

> [!NOTE]
> 如果您沒有在命令列上指定 INSTANCEID，安裝程式預設會以 \<INSTANCENAME> 取代 \<INSTANCEID>。 

## <a name="see-also"></a>另請參閱
- [從安裝精靈安裝 SQL Server 2016](../../database-engine/install-windows/install-sql-server-from-the-installation-wizard-setup.md)
- [SQL Server 容錯移轉叢集安裝](../../sql-server/failover-clusters/install/sql-server-failover-cluster-installation.md)
- [安裝 SQL Server 2016 Business Intelligence 功能](../../sql-server/install/install-sql-server-business-intelligence-features.md)
