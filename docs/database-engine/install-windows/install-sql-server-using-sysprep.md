---
title: 使用 SysPrep 安裝 SQL Server | Microsoft Docs
description: 本文描述如何在 SQL Server 安裝中使用 SysPrep 來準備和完成映像。
ms.custom: ''
ms.date: 09/07/2017
ms.prod: sql
ms.reviewer: ''
ms.technology: install
ms.topic: conceptual
ms.assetid: 11f4ed8a-aaa9-417b-bdd5-204f551c6bb6
author: cawrites
ms.author: chadam
monikerRange: '>=sql-server-2016'
ms.openlocfilehash: bcf581a18af1f8f3024cd94d712359396f64bbfd
ms.sourcegitcommit: 3ec49252e82590de0fe559a8574606ae213f6f3b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/07/2021
ms.locfileid: "97975428"
---
# <a name="install-sql-server-with-sysprep"></a>使用 SysPrep 安裝 SQL Server

[!INCLUDE [SQL Server -Windows Only](../../includes/applies-to-version/sql-windows-only.md)]

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] SysPrep 相關的安裝動作可以透過安裝中心來存取。 **[安裝中心]** 的 **[進階]** 頁面包含兩個選項 - 準備 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]**獨立執行個體的映像** 及 完成備妥的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]**獨立執行個體的映像**。 [準備](#prepare) 和 [完成](#complete) 章節會詳細說明安裝程序。 如需詳細資訊，請參閱＜ [Considerations for Installing SQL Server Using SysPrep](../../database-engine/install-windows/considerations-for-installing-sql-server-using-sysprep.md)＞。 
  
您也可以使用命令提示字元或組態檔，準備及完成 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的執行個體。 如需詳細資訊，請參閱  
  
- [從命令提示字元安裝 SQL Server](../../database-engine/install-windows/install-sql-server-from-the-command-prompt.md)  
  
- [使用設定檔安裝 SQL Server](../../database-engine/install-windows/install-sql-server-using-a-configuration-file.md)  
  
## <a name="prerequisites"></a>必要條件  
在安裝 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 之前，請檢閱[規劃 SQL Server 安裝](../../sql-server/install/planning-a-sql-server-installation.md)中的文章。 
  
如需 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 版本及硬體和軟體需求的詳細資訊，請參閱[安裝 SQL Server 的硬體與軟體需求](../../sql-server/install/hardware-and-software-requirements-for-installing-sql-server.md)。 
    
##  <a name="ssnoversion-sysprep-cluster-support"></a><a name="sysprep"></a> [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] SysPrep 叢集支援  
 從 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)]開始，SysPrep 支援以命令列安裝叢集 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體。 如需詳細資訊，請參閱＜ [何謂 Sysprep](https://msdn.microsoft.com/library/cc721940\(v=WS.10\).aspx)＞。 
  
### <a name="to-prepare-a-ssnoversion-failover-cluster-unattended"></a>準備 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 容錯移轉叢集 (自動)  
  
1. 準備映像 (如 [使用 SysPrep 安裝 SQL Server 的考量](../../database-engine/install-windows/considerations-for-installing-sql-server-using-sysprep.md)中所述)，並透過 SysPrep 一般化擷取 Windows 映像。 下列範例會準備影像：  
  
    ```  
    Setup.exe /q /ACTION=PrepareImage l /FEATURES=SQLEngine /InstanceID =<MYINST> /IACCEPTSQLSERVERLICENSETERMS  
    ```  
  
     然後執行 Windows SysPrep 一般化。 
  
2. 執行 Windows SysPrep 特殊化以部署影像。 
  
3. 建立 Windows 容錯移轉叢集。 
  
4. 在所有節點上，以 **/ACTION=PrepareFailoverCluster** 執行 setup.exe。 例如：  
  
    ```  
    setup.exe /q /ACTION=PrepareFailoverCluster /InstanceName=<InstanceName> /Features=SQLEngine  /SQLSVCACCOUNT="<DomainName\UserName>" /SQLSVCPASSWORD="xxxxxxxxxxx"  /IACCEPTSQLSERVERLICENSETERMS  
    ```  
  
### <a name="complete-a-ssnoversion-failover-cluster-unattended"></a>完成 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 容錯移轉叢集 (自動)  
  
1. 在擁有可用儲存體群組的節點上，以 **/ACTION=CompleteFailoverCluster** 執行 setup.exe：  
  
    ```  
    setup.exe /q /ACTION=CompleteFailoverCluster /InstanceName=<InstanceName>  /FAILOVERCLUSTERDISKS="<Cluster Disk Resource Name - for example, 'Disk S:'>:" /FAILOVERCLUSTERNETWORKNAME="<Insert FOI Network Name>" /FAILOVERCLUSTERIPADDRESSES="IPv4;xx.xxx.xx.xx;Cluster Network;xxx.xxx.xxx.x" /FAILOVERCLUSTERGROUP="MSSQLSERVER" /INSTALLSQLDATADIR="<Drive>:\<Path>\MSSQLSERVER" /SQLCOLLATION="SQL_Latin1_General_CP1_CS_AS" /SQLSYSADMINACCOUNTS="<DomainName\UserName>"  
    ```  
  
### <a name="adding-a-node-to-an-existing-ssnoversion-failover-cluster-unattended"></a>將節點新增至現有的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 容錯移轉叢集 (自動)  
  
1. 執行 Windows SysPrep 特殊化以部署影像。 
  
2. 加入 Windows 容錯移轉叢集。 
  
3. 在所有節點上，以 **/ACTION=AddNode** 執行 setup.exe：  
  
    ```  
    setup.exe /q /ACTION=AddNode /InstanceName=<InstanceName> /Features=SQLEngine  /SQLSVCACCOUNT="<DomainName\UserName>" /SQLSVCPASSWORD="xxxxxxxxxxx"  /IACCEPTSQLSERVERLICENSETERMS  
    ```  
  
##  <a name="prepare-image"></a><a name="prepare"></a> 準備映像  
  
### <a name="prepare-a-stand-alone-instance-of-ssnoversion"></a>準備 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]的獨立執行個體。 
  
1. 插入 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝媒體。 在根資料夾中，按兩下 Setup.exe。 若要從網路共用進行安裝，請找出共用上的根資料夾，然後按兩下 Setup.exe。 
  
2. 安裝精靈會執行 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝中心。 若要準備 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的執行個體，請在 [進階] 頁面上，按一下 準備 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 獨立執行個體的映像。 
  
3. 系統組態檢查會在電腦上執行探索作業。 若要繼續進行，請按一下 **[確定]** 。 您可以按一下 **[顯示詳細資料]** 在畫面上檢視詳細資料，或是按一下 **[檢視詳細資料報表]** 來以 HTML 報表形式檢視詳細資料。 
  
4. 最新可用的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 產品更新會隨即顯示在 [產品更新] 頁面上。 如果不想包含更新，請清除 包含 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 產品更新程式 核取方塊。 如果未偵測到任何產品更新， [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式將不會顯示此頁面，而會自動前往 **[安裝安裝程式檔案]** 頁面。 
  
5. 安裝程式會在 [安裝安裝程式檔案] 頁面上，顯示下載、擷取及安裝安裝程式檔案的進度。 如有找到 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式的更新，並指定要包含該更新，將會一併安裝。 
  
6. 系統組態檢查將會先確認電腦的系統狀態，然後安裝程式才會繼續進行。 您可以按一下 **[顯示詳細資料]** 在畫面上檢視詳細資料，或是按一下 **[檢視詳細資料報表]** 來以 HTML 報表形式檢視詳細資料。 
  
7. 在 [準備映像類型] 頁面上，選取 準備新的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體。 
  
     只有當您的電腦上已經有尚未設定但備妥的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體時，才會顯示 [準備映像類型] 頁面。 您可以選擇準備新的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體，或是將 sysprep 支援的功能加入至電腦上現有的已備妥 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體。 如需有關如何將功能加入至已備妥之 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體的詳細資訊，請參閱 [將功能加入至已備妥的執行個體](#AddFeatures)。 
  
8. 在 **[授權條款]** 頁面上，閱讀授權條款，然後選取要接受授權條款和條件的核取方塊。 若要協助提升 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]，您也可以啟用功能使用方式選項，並傳送報告給 [!INCLUDE[msCoName](../../includes/msconame-md.md)]。 
  
9. 在 **[特徵選取]** 頁面上，選取要安裝的元件：  
  
    |安裝|元件|  
    |-|-|  
    |[!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] SysPrep|[!INCLUDE[ssDE](../../includes/ssde-md.md)]<br /><br /> [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 複寫<br /><br /> 全文檢索功能<br /><br /> Data Quality Services<br /><br /> [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] 原生模式<br /><br /> [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]<br /><br /> 可轉散發功能<br /><br /> 共用功能|  
  
     當您反白顯示功能名稱之後，每一個元件群組的描述就會出現在右窗格中。 您可以選取核取方塊的任何組合。 如需詳細資訊，請參閱 [SQL Server 2017 版本支援的功能](../../sql-server/editions-and-components-of-sql-server-2017.md)和 [SQL Server 2019](../../sql-server/editions-and-components-of-sql-server-version-15.md)
  
     右窗格會顯示選取功能的必要條件。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式將會在這個程序稍後說明的安裝步驟期間安裝尚未安裝的必要條件。 
  
10. 在 **[準備映像規則]** 頁面上，系統組態檢查會先確認電腦的系統狀態，然後安裝程式才會繼續進行。 您可以按一下 **[顯示詳細資料]** 在畫面上檢視詳細資料，或是按一下 **[檢視詳細資料報表]** 來以 HTML 報表形式檢視詳細資料。 
  
11. 在 [執行個體組態] 頁面上，指定執行個體的執行個體識別碼。 選取 [下一步] 以繼續操作。 
  
     **執行個體識別碼** - 執行個體識別碼是用來識別 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體的安裝目錄和登錄機碼。 這是預設執行個體和具名執行個體的狀況。 如果已備妥的執行個體在完成步驟期間當做預設執行個體來完成，執行個體名稱會覆寫為 MSSQLSERVER。 執行個體識別碼仍然與指定的識別碼相同。 
  
     **執行個體根目錄** - 根據預設，執行個體根目錄為 [!INCLUDE[ssInstallPath](../../includes/ssinstallpath-md.md)]。 若要指定非預設的根目錄，請使用提供的欄位，或是按一下 [瀏覽] 找出安裝資料夾。 準備步驟所指定的目錄將會在完成步驟的組態設定期間使用。 
  
     所有的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Service Pack 和升級項目都會套用至 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]執行個體的每一個元件。 
  
     **安裝的執行個體** - 此方格會顯示執行安裝程式之電腦上的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體。 
  
12. **[磁碟空間需求]** 頁面會計算您所指定之功能的所需磁碟空間。 然後，它會比較所需的空間與可用的磁碟空間。 
  
13. 系統組態檢查將會執行預備映像規則，以便使用您已指定的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 功能來驗證電腦組態。 您可以按一下 **[顯示詳細資料]** 在畫面上檢視詳細資料，或是按一下 **[檢視詳細資料報表]** 來以 HTML 報表形式檢視詳細資料。 
  
14. **[準備開始預備映像]** 頁面會顯示安裝期間指定之安裝選項的樹狀檢視。 在此頁面上，安裝程式會指出產品更新功能為啟用或停用，以及最後的更新版本。 若要繼續，請按一下 **[準備]** 。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式會先安裝選取功能所需的必要條件，之後再進行功能安裝。 
  
15. 在安裝期間， **[準備映像進度]** 頁面會提供狀態，好讓您可以在安裝程式進行時監視安裝進度。 
  
16. 安裝之後， **[完成]** 頁面會提供安裝和其他重要注意事項之摘要記錄檔的連結。 若要完成 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程序，請按一下 **[關閉]** 。 
  
17. 如果指示您重新啟動電腦，請立刻執行。 當您完成安裝時，請務必閱讀安裝精靈所提供的訊息。 如需詳細資訊，請參閱＜ [View and Read SQL Server Setup Log Files](../../database-engine/install-windows/view-and-read-sql-server-setup-log-files.md)＞。 
  
18. 這樣會完成準備步驟。 您可能會完成映像或部署備妥的映像，如＜ [Considerations for Installing SQL Server Using SysPrep](../../database-engine/install-windows/considerations-for-installing-sql-server-using-sysprep.md)＞中所述。 
  
##  <a name="complete-image"></a><a name="complete"></a> 完成映像  
  
### <a name="complete-a-prepared-instance-of-ssnoversion"></a>完成備妥的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]  
  
1. 如果您電腦上的映像中已經包含備妥的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體，您將會在 [開始] 功能表中看到一個捷徑。 您也可以啟動安裝中心，並按一下 [進階] 頁面上的 [完成備妥的獨立執行個體的映像]。 
  
2. 系統組態檢查會在電腦上執行探索作業。 若要繼續進行，請按一下 **[確定]** 。 您可以按一下 **[顯示詳細資料]** 在畫面上檢視詳細資料，或是按一下 **[檢視詳細資料報表]** 來以 HTML 報表形式檢視詳細資料。 
  
3. 在 **[安裝程式支援檔案]** 頁面上，按一下 **[安裝]** ，即可安裝安裝程式支援檔案。 
  
4. 系統組態檢查將會先確認電腦的系統狀態，然後安裝程式才會繼續進行。 檢查完成之後，請按 **[下一步]** 繼續進行。 您可以按一下 **[顯示詳細資料]** 在畫面上檢視詳細資料，或是按一下 **[檢視詳細資料報表]** 來以 HTML 報表形式檢視詳細資料。 
  
5. 在 **[產品金鑰]** 頁面上，選取選項按鈕，指出您要安裝免費的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]版本，或具有 PID 金鑰之產品的實際執行版本。 如需詳細資訊，請參閱 [SQL Server 的版本及支援功能](../../sql-server/editions-and-components-of-sql-server-2017.md)。 如果您要安裝 Evaluation Edition，180 天的試用期會在您完成這個步驟之後開始。 
  
6. 在 **[授權條款]** 頁面上，閱讀授權條款，然後選取要接受授權條款和條件的核取方塊。 若要協助提升 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]，您也可以啟用功能使用方式選項，並傳送報告給 [!INCLUDE[msCoName](../../includes/msconame-md.md)]。 
  
7. 在 **[選取備妥的執行個體]** 頁面上，從下拉式方塊選取您想要完成的備妥執行個體。 從 [執行個體識別碼] 清單中選取尚未設定的執行個體。 
  
     **已安裝的執行個體：** 此方格會顯示所有執行個體，包括這部電腦上任何已備妥的執行個體。 
  
8. 在 **[功能檢閱]** 頁面上，您將會在準備步驟期間看到安裝所包含的選定功能和元件。 如果您想要將更多功能加入至已備妥之執行個體中並未包含的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體，您必須先完成這個步驟，才能完成 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體，然後從 **[安裝中心]** 上的 **[加入功能]** 來加入功能。 
  
    > [!NOTE]  
    >  您可以加入您所安裝之產品版本的可用功能。 如需詳細資訊，請參閱 [SQL Server 的版本及支援功能](../../sql-server/editions-and-components-of-sql-server-2017.md)。  
  
9. 在 [執行個體組態] 頁面上，指定已備妥之執行個體的執行個體名稱。 這就是當您完成 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]組態之後的執行個體名稱。 選取 [下一步] 以繼續操作。 
  
     **執行個體識別碼** - 執行個體識別碼是用來識別 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體的安裝目錄和登錄機碼。 這是預設執行個體和具名執行個體的狀況。 如果已備妥的執行個體在完成步驟期間當做預設執行個體來完成，執行個體名稱會覆寫為 MSSQLSERVER。 執行個體識別碼仍然與準備步驟期間指定的識別碼相同。 
  
     **執行個體根目錄** - 將會使用準備步驟中所指定的目錄，而且無法在這個步驟中加以修改。 
  
     所有的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Service Pack 和升級項目都會套用至 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]執行個體的每一個元件。 
  
     **安裝的執行個體** - 此方格會顯示執行安裝程式之電腦上的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體。 
  
10. 依據您在準備步驟期間選取的功能而定，本文其餘部分的工作流程會有所不同。 您可能不會看到所有頁面，這取決於您的選取項目而定。 
  
11. 在 [伺服器設定 - 服務帳戶] 頁面中，指定 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 服務的登入帳戶。 在這個頁面上所設定的實際服務隨著您選取要安裝的功能而不同。 
  
     您可以將相同登入帳戶指派給所有 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 服務，或個別設定每一個服務帳戶。 此外，您也可以指定要自動啟動服務、手動啟動服務或停用服務。 [!INCLUDE[msCoName](../../includes/msconame-md.md)] 建議您個別設定服務帳戶，以針對各項服務提供最低權限，其中 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 服務會授與必須完成工作的最小權限。 如需詳細資訊，請參閱 [伺服器組態 - 服務帳戶](./install-sql-server.md) 和 [設定 Windows 服務帳戶與權限](../../database-engine/configure-windows/configure-windows-service-accounts-and-permissions.md)。 
  
     若要針對此 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]執行個體中的所有服務帳戶指定相同的登入帳戶，請在頁面底部的欄位中提供認證。 
  
     **安全性注意事項** [!INCLUDE[ssNoteStrongPass](../../includes/ssnotestrongpass-md.md)]  
  
     當您完成針對 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 服務指定登入資訊之後，請按 **[下一步]** 。 
  
12. 使用 [伺服器組態 - 定序] 索引標籤，為 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 和 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] 指定非預設的定序。 如需詳細資訊，請參閱[伺服器組態 - 定序](./install-sql-server.md)。 
  
13. 使用 [!INCLUDE[ssDE](../../includes/ssde-md.md)] [組態 - 帳戶提供] 頁面來指定以下項目：  
  
    - 安全性模式 - 為 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]執行個體選取 Windows 驗證或混合模式驗證。 如果您選取混合模式驗證，就必須為內建 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 系統管理員帳戶提供增強式密碼。 
  
         當裝置與 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]建立成功的連接之後，Windows 驗證和混合模式的安全性機制是相同的。 如需詳細資訊，請參閱 [Database Engine 組態 - 伺服器組態](./install-sql-server.md)。 
  
    - [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 管理員 - 您在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]執行個體上至少必須指定一個系統管理員。 若要加入 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式執行所用的帳戶，請按一下 **[加入目前使用者]** 。 若要從系統管理員清單中加入或移除帳戶，請按一下 **[加入]** 或 **[移除]** ，然後編輯在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]執行個體中將會有管理員權限的使用者、群組或電腦清單。 如需詳細資訊，請參閱 [Database Engine 組態 - 伺服器組態](./install-sql-server.md)。 
  
     當您完成清單的編輯之後，請按一下 **[確定]** 。 然後，在組態對話方塊中確認管理員的清單。 當此清單完成時，請按 **[下一步]** 。 
  
14. 使用 [!INCLUDE[ssDE](../../includes/ssde-md.md)] [組態 - 資料目錄] 頁面來指定非預設的安裝目錄。 若要安裝到預設目錄，請按 **[下一步]** 。 
  
    > [!IMPORTANT]  
    >  如果您要指定非預設的安裝目錄，請確定安裝資料夾對於此 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]執行個體是唯一的。 此對話方塊上的任何目錄都不應該與其他 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]執行個體中的目錄共用。 
  
     如需詳細資訊，請參閱 [Database Engine 組態 - 資料目錄](./install-sql-server.md)。 
  
15. 使用 [!INCLUDE[ssDE](../../includes/ssde-md.md)] [組態 - FILESTREAM] 頁面來針對 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]執行個體啟用 FILESTREAM。 如需詳細資訊，請參閱 [Database Engine 組態 - Filestream](./install-sql-server.md)。 
  
16. 使用[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] [組態] 頁面來指定要建立的 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] 安裝類型。 如需 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] 組態模式的詳細資訊，請參閱 [Reporting Services 組態選項 (SSRS)](./install-sql-server.md)。 
  
17. 在 **[錯誤報告]** 頁面上，指定您想要傳送給 [!INCLUDE[msCoName](../../includes/msconame-md.md)] 的資訊，這可協助改善 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]。 錯誤報告選項預設為啟用。 
  
18. 在 **[完成映像規則]** 頁面上，系統組態檢查會執行完整的映像規則，以便使用您已指定的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 組態來驗證電腦組態。 您可以按一下 **[顯示詳細資料]** 在畫面上檢視詳細資料，或是按一下 **[檢視詳細資料報表]** 來以 HTML 報表形式檢視詳細資料。 
  
19. **[準備好要完成映像]** 頁面會顯示安裝期間指定之安裝選項的樹狀檢視。 若要繼續，請按一下 **[安裝]** 。 
  
20. 在安裝期間， **[完成映像進度]** 頁面會提供狀態，好讓您可以在安裝程式進行時監視安裝進度。 
  
21. 安裝之後， **[完成]** 頁面會提供安裝和其他重要注意事項之摘要記錄檔的連結。 若要完成 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程序，請按一下 **[關閉]** 。 
  
22. 如果指示您重新啟動電腦，請立刻執行。 當您完成安裝時，請務必閱讀安裝精靈所提供的訊息。 如需詳細資訊，請參閱＜ [View and Read SQL Server Setup Log Files](../../database-engine/install-windows/view-and-read-sql-server-setup-log-files.md)＞。 
  
23. 這個步驟會完成已備妥之 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體的組態，而且您已經完成 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]的安裝。 
  
##  <a name="add-features-to-a-prepared-instance"></a><a name="AddFeatures"></a> Add Features to a Prepared Instance  
  
### <a name="add-features-to-a-prepared-instance-of-ssnoversion"></a>將功能加入至備妥的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]  
  
1. 插入 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝媒體。 在根資料夾中，按兩下 Setup.exe。 若要從網路共用進行安裝，請找出共用上的根資料夾，然後按兩下 Setup.exe。 
  
2. 安裝精靈會執行 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝中心。 若要將功能加入備妥的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體，請在 [進階] 頁面上，按一下 準備 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] [獨立執行個體的映像]。 
  
3. 系統組態檢查會在電腦上執行探索作業。 若要繼續進行，請按一下 **[確定]** 。 您可以按一下 **[顯示詳細資料]** 在畫面上檢視詳細資料，或是按一下 **[檢視詳細資料報表]** 來以 HTML 報表形式檢視詳細資料。 
  
4. 在 [安裝程式支援檔案] 頁面上，按一下 **[安裝]** ，即可安裝安裝程式支援檔案。 
  
5. 在 [準備映像類型] 頁面上，選取 將功能加入到現有備妥的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] [執行個體] 選項。 從備妥的可用執行個體的下拉式清單中，選取您想要加入功能之特定的備妥執行個體。 
  
6. 在 **[特徵選取]** 頁面上，指定您想要加入至備妥執行個體的功能。 
  
     右窗格會顯示選取功能的必要條件。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式將會在這個程序稍後說明的安裝步驟期間安裝尚未安裝的必要條件。 
  
7. 在 **[準備映像規則]** 頁面上，系統組態檢查會先確認電腦的系統狀態，然後安裝程式才會繼續進行。 您可以按一下 **[顯示詳細資料]** 在畫面上檢視詳細資料，或是按一下 **[檢視詳細資料報表]** 來以 HTML 報表形式檢視詳細資料。 
  
8. [磁碟空間需求] 頁面會計算您所指定之功能的所需磁碟空間。 然後，它會比較所需的空間與可用的磁碟空間。 
  
9. 在 **[準備映像規則]** 頁面上，系統組態檢查會執行準備映像規則，以便使用您已指定的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 功能來驗證電腦組態。 您可以按一下 **[顯示詳細資料]** 在畫面上檢視詳細資料，或是按一下 **[檢視詳細資料報表]** 來以 HTML 報表形式檢視詳細資料。 
  
10. **[準備開始預備映像]** 頁面會顯示安裝期間指定之安裝選項的樹狀檢視。 若要繼續，請按一下 **[安裝]** 。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式會先安裝選取功能所需的必要條件，之後再進行功能安裝。 
  
11. 在安裝期間， **[準備映像進度]** 頁面會提供狀態，好讓您可以在安裝程式進行時監視安裝進度。 
  
12. 安裝之後， **[完成]** 頁面會提供安裝和其他重要注意事項之摘要記錄檔的連結。 若要完成 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程序，請按一下 **[關閉]** 。 
  
13. 如果指示您重新啟動電腦，請立刻執行。 當您完成安裝時，請務必閱讀安裝精靈所提供的訊息。 如需詳細資訊，請參閱＜ [View and Read SQL Server Setup Log Files](../../database-engine/install-windows/view-and-read-sql-server-setup-log-files.md)＞。 
  
##  <a name="remove-features-from-a-prepare-instance"></a><a name="RemoveFeatures"></a> 從備妥的執行個體中移除功能  
  
### <a name="removing-features-from-a-prepared-instance-of-ssnoversion"></a>從備妥的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]  
  
1. 若要開始解除安裝程序，請在 **[開始]** 功能表上按一下 **[控制台]** ，然後按兩下 **[程式和功能]** 。 
  
2. 按兩下要解除安裝的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 元件，然後按一下 **[移除]** 。 
  
3. 安裝程式支援規則將會執行，以便驗證您的電腦組態。 按一下 **[確定]** 繼續進行。 
  
4. 在 **[選取執行個體]** 頁面上，選取您想要修改的已備妥執行個體。 備妥的執行個體名稱將會顯示為 "Unconfigured PreparedInstanceID"，其中 PreparedInstanceID 是您選取的執行個體。 
  
5. 在 **[選取功能]** 頁面上，指定要從指定之 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體中移除的功能。 選取 [下一步] 以繼續操作。 
  
6. 移除規則將會執行，以便確認作業可以順利完成。 
  
7. 在 **[準備移除]** 頁面上，檢閱即將解除安裝之元件和功能的清單。 
  
8. **[移除進度]** 頁面會顯示此作業的狀態。 
  
9. 在 **[完成]** 頁面上，您可以檢閱此作業的完成狀態。 按一下 **[關閉]** 結束安裝精靈。 
  
##  <a name="uninstalling-a-prepared-instance"></a><a name="Uninstall"></a> 解除安裝備妥的執行個體  
  
### <a name="uninstall-a-prepared-instance-of-ssnoversion"></a>解除安裝備妥的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]  
  
1. 若要開始解除安裝程序，請在 **[開始]** 功能表上按一下 **[控制台]** ，然後按兩下 **[程式和功能]** 。 
  
2. 按兩下要解除安裝的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 元件，然後按一下 **[移除]** 。 
  
3. 安裝程式支援規則將會執行，以便驗證您的電腦組態。 按一下 **[確定]** 繼續進行。 
  
4. 在 **[選取執行個體]** 頁面上，選取您想要修改的已備妥執行個體。 備妥的執行個體名稱將會顯示為 "Unconfigured PreparedInstanceID"，其中 PreparedInstanceID 是您選取的執行個體。 
  
5. 在 **[選取功能]** 頁面上，指定要從指定之 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體中移除的功能。 選取 [下一步] 以繼續操作。 
  
6. 在 **[移除規則]** 頁面上，安裝程式會執行規則，以便確認作業可以順利完成。 
  
7. 在 **[準備移除]** 頁面上，檢閱即將解除安裝之元件和功能的清單。 
  
8. **[移除進度]** 頁面會顯示此作業的狀態。 
  
9. 在 **[完成]** 頁面上，您可以檢閱此作業的完成狀態。 按一下 **[關閉]** 結束安裝精靈。 
  
10. 重複步驟 1 到 9，直到移除所有 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 元件為止。 
  
##  <a name="modifying-or-uninstalling-a-completed-instance-of-ssnoversion"></a><a name="bk_Modifying_Uninstalling"></a> 修改或解除安裝已完成的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]執行個體。 
 加入或移除功能或解除安裝已完成之 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體的程序，類似於已安裝之 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]執行個體的程序。 如需詳細資訊，請參閱下列文章：  
  
- [將功能新增至 SQL Server 的執行個體 &#40;安裝程式&#41;](./add-features-to-an-instance-of-sql-server-setup.md)  
  
- [解除安裝現有的 SQL Server 執行個體 &#40;安裝程式&#41;](../../sql-server/install/uninstall-an-existing-instance-of-sql-server-setup.md)  
  
## <a name="see-also"></a>另請參閱  
 [什麼是 Sysprep？](/previous-versions/windows/it-pro/windows-vista/cc721940(v=ws.10))   
 [Windows SysPrep 的運作方式](/previous-versions/windows/it-pro/windows-vista/cc766514(v=ws.10))  
  
