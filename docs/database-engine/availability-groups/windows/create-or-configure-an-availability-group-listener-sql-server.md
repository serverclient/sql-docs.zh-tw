---
title: 設定可用性群組接聽程式
description: '描述使用 PowerShell 或 SQL Server Management Studio 設定 Always On 可用性群組接聽程式時所要採取的步驟。 '
ms.custom: seo-lt-2019
ms.date: 05/17/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: how-to
f1_keywords:
- sql13.swb.availabilitygroup.newaglistener.general.f1
helpviewer_keywords:
- Availability Groups [SQL Server], listeners
- Availability Groups [SQL Server], client connectivity
ms.assetid: 2bc294f6-2312-4b6b-9478-2fb8a656e645
author: cawrites
ms.author: chadam
manager: erikre
ms.openlocfilehash: e8c0a889b764f81aaf8ac34cf566b52bf1e86c83
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97643267"
---
# <a name="configure-a-listener-for-an-always-on-availability-group"></a>設定 Always On 可用性群組的接聽程式
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]
  本主題描述如何使用 [!INCLUDE[ssManStudioFull](../../../includes/ssmanstudiofull-md.md)]、[!INCLUDE[tsql](../../../includes/tsql-md.md)] 或[!INCLUDE[ssCurrent](../../../includes/sscurrent-md.md)] 中的 PowerShell，建立或設定 AlwaysOn 可用性群組的單一「可用性群組接聽程式」  (Availability Group Listener)。  
  
> [!IMPORTANT]  
>  若要建立可用性群組的第一個可用性群組接聽程式，強烈建議您使用 [!INCLUDE[ssManStudioFull](../../../includes/ssmanstudiofull-md.md)]、[!INCLUDE[tsql](../../../includes/tsql-md.md)] 或 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] PowerShell。 除非必要 (例如建立其他接聽程式)，否則避免在 WSFC 叢集中直接建立接聽程式。  
  
 
##  <a name="does-a-listener-exist-for-this-availability-group-already"></a><a name="DoesListenerExist"></a> 這個可用性群組已經有接聽程式？  

 **若要判斷可用性群組的接聽程式是否已經存在**  
  
-   [檢視可用性群組接聽程式屬性 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/view-availability-group-listener-properties-sql-server.md)  
  
> [!NOTE]  
>  如果接聽程式已存在，而且您想要建立其他接聽程式，請參閱本主題稍後的 [為可用性群組建立其他接聽程式 (選擇性)](#CreateAdditionalListener)。  
  
##  <a name="limitations-and-restrictions"></a><a name="Restrictions"></a> 限制事項  
  
-   透過 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]，您只可以為每個可用性群組建立一個接聽程式。 通常每個可用性群組只需要一個接聽程式。 但是，某些客戶情況下一個可用性群組需要多個接聽程式。   透過 SQL Server 建立接聽程式之後，您可以使用適用容錯移轉叢集的 Windows PowerShell 或 WSFC 容錯移轉叢集管理員建立額外的接聽程式。 如需詳細資訊，請參閱本主題稍後的 [為可用性群組建立其他接聽程式 (選擇性)](#CreateAdditionalListener)。  
  
##  <a name="recommendations"></a><a name="Recommendations"></a> 建議  
 建議為多重子網路組態使用靜態 IP 位址，但並不是必要的。  
  
##  <a name="prerequisites"></a><a name="Prerequisites"></a> 必要條件  
  
-   您必須連接到裝載主要複本的伺服器執行個體。  
  
-   如果您要跨多個子網路設定可用性群組接聽程式，而且打算使用靜態 IP 位址，必須取得每個子網路的靜態 IP 位址，而且這些子網路要裝載您要建立其接聽程式之可用性群組的可用性複本。 您通常需要向網路管理員取得靜態 IP 位址。  
  
> [!IMPORTANT]  
>  在建立第一個接聽程式之前，強烈建議您閱讀 [AlwaysOn 用戶端連接性 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/always-on-client-connectivity-sql-server.md)。  
  
###  <a name="requirements-for-the-dns-name-of-an-availability-group-listener"></a><a name="DNSnameReqs"></a> 可用性群組接聽程式之 DNS 名稱的需求  
 每個可用性群組接聽程式都需要一個 DNS 主機名稱，該名稱在網域中和 NetBIOS 中必須是唯一的。 DNS 名稱是字串值。 此名稱只能包含英數字元、虛線/連字號 (-) 和底線 (_) (順序不拘)。 DNS 主機名稱不區分大小寫。 長度上限是 63 個字元，但是在 [!INCLUDE[ssManStudioFull](../../../includes/ssmanstudiofull-md.md)]中，您可以將長度上限指定為 15 個字元。  
  
 我們建議您指定一個有意義的字串。 例如，如果是名為 `AG1`的可用性群組，有意義的 DNS 主機名稱會是 `ag1-listener`。  
  
> [!IMPORTANT]  
>  NetBIOS 只會辨識 DNS 名稱中的前 15 個字元。 如果您有兩個由相同 Active Directory 所控制的 WSFC 叢集，而且嘗試使用超過 15 個字元的名稱以及完全相同的 15 個字元前置詞，在這兩個叢集中建立可用性群組接聽程式，就會收到一則錯誤，指出系統無法讓虛擬網路名稱資源上線。 如需有關 DNS 名稱之前置詞命名規則的詳細資訊，請參閱＜ [指派網域名稱](https://technet.microsoft.com/library/cc731265\(WS.10\).aspx)＞。  
  
##  <a name="windows-permissions"></a><a name="WinPermissions"></a> Windows 權限  
  
|權限|連結|  
|-----------------|----------|  
|裝載可用性群組之 WSFC 叢集的叢集物件名稱 (CNO) 必須具有「建立電腦物件」  權限。<br /><br /> 在 Active Directory 中，CNO 預設不會明確具有「建立電腦物件」  權限，而且可以建立 10 個虛擬電腦物件 (VCO)。 在建立 10 個 VCO 之後，其他 VCO 的建立作業將會失敗。 您可以明確授與權限給 WSFC 叢集的 CNO，以避免這個狀況。 請注意，您已刪除之可用性群組的 VCO 不會自動在 Active Directory 中刪除及算為 10 個 VCO 預設限制，除非您手動加以刪除。<br /><br /> 注意:在某些組織中，安全性原則會禁止將 [建立電腦物件]  權限授與個別使用者帳戶。|＜Steps for configuring the account for the person who installs the cluster＞(為叢集安裝人員設定帳戶的步驟)  ，位於 [Failover Cluster Step-by-Step Guide:Configuring Accounts in Active Directory](https://technet.microsoft.com/library/cc731002\(WS.10\).aspx#BKMK_steps_installer) (容錯移轉叢集逐步指南：設定 Active Directory 中的帳戶)<br /><br /> ＜Steps for prestaging the cluster name account＞(預先設置叢集名稱帳戶的步驟)  ，位於 [Failover Cluster Step-by-Step Guide:Configuring Accounts in Active Directory](https://technet.microsoft.com/library/cc731002\(WS.10\).aspx#BKMK_steps_precreating) (容錯移轉叢集逐步指南：設定 Active Directory 中的帳戶)|  
|如果您的組織要求您為接聽程式虛擬網路名稱預先設置電腦帳戶，將需要 **Account Operator** 群組中的成員資格或網域管理員的協助。|＜Steps for prestaging an account for a clustered service or application＞(為叢集服務或應用程式預先設置帳戶的步驟)  ，位於 [Failover Cluster Step-by-Step Guide:Configuring Accounts in Active Directory](https://technet.microsoft.com/library/cc731002\(WS.10\).aspx#BKMK_steps_precreating2) (容錯移轉叢集逐步指南：設定 Active Directory 中的帳戶)。|  
  
> [!TIP]  
>  一般而言，不要為接聽程式虛擬網路名稱預先設置電腦帳戶是最簡單的。 如果可以，讓帳戶在您執行「WSFC 高可用性精靈」時自動建立並設定。  
  
##  <a name="sql-server-permissions"></a><a name="SqlPermissions"></a> SQL Server 權限  
  
|Task|權限|  
|----------|-----------------|  
|建立可用性群組接聽程式|需要 **系統管理員 (sysadmin)** 固定伺服器角色的成員資格，以及 CREATE AVAILABILITY GROUP 伺服器權限、ALTER ANY AVAILABILITY GROUP 權限或 CONTROL SERVER 權限。|  
|修改現有的可用性群組接聽程式|需要可用性群組的 ALTER AVAILABILITY GROUP 權限、CONTROL AVAILABILITY GROUP 權限、ALTER ANY AVAILABILITY GROUP 權限或 CONTROL SERVER 權限。|  
  
##  <a name="using-sql-server-management-studio"></a><a name="SSMSProcedure"></a> 使用 SQL Server Management Studio  
  
> [!TIP]  
>  [新增可用性群組精靈](../../../database-engine/availability-groups/windows/use-the-new-availability-group-dialog-box-sql-server-management-studio.md) 支援建立新可用性群組的接聽程式。  
  
 **建立或設定可用性群組接聽程式**  
  
1.  在 [物件總管] 中，連接到裝載可用性群組之主要複本的伺服器執行個體，然後按一下伺服器名稱以展開伺服器樹狀目錄。  
  
2.  依序展開 [Always On 高可用性]  節點和 [可用性群組]  節點。  
  
3.  按一下您要設定其接聽程式的可用性群組，然後選擇下列其中一個替代方式：  
  
    -   若要建立接聽程式，以滑鼠右鍵按一下 [可用性群組接聽程式]  節點，然後選取 [新增接聽程式]  命令。 這會開啟 **[新增可用性群組接聽程式]** 對話方塊。 如需詳細資訊，請參閱本主題稍後的[加入可用性群組接聽程式 (對話方塊)](#AddAgListenerDialog)。  
  
    -   若要變更現有接聽程式的通訊埠編號，展開 [可用性群組接聽程式]  節點、以滑鼠右鍵按一下接聽程式，然後選取 [屬性]  命令。 在 **[通訊埠]** 欄位中輸入新的通訊埠編號，然後按一下 **[確定]** 。  
  
###  <a name="new-availability-group-listener-dialog-box"></a><a name="AddAgListenerDialog"></a> 新增可用性群組接聽程式 (對話方塊)  
 **接聽程式 DNS 名稱**  
 指定可用性群組接聽程式的 DNS 主機名稱。 DNS 名稱是一個字串，在網域和 NetBIOS 中都必須是唯一的。 此名稱只能包含英數字元、虛線 (-) 和連字號 (_) (順序不拘)。 DNS 主機名稱不區分大小寫。 最大長度是 15 個字元。  
  
 如需詳細資訊，請參閱本主題前文中的＜ [可用性群組接聽程式之 DNS 名稱的需求](#DNSnameReqs)＞。  
  
 **通訊埠**  
 此接聽程式所使用的 TCP 通訊埠。  
  
 **網路模式**  
 指出接聽程式所使用的 TCP 通訊協定，其中一個：  
  
 **DHCP**  
 接聽程式將使用執行動態主機設定通訊協定 (DHCP) 之伺服器所指派的動態 IP 位址。 DHCP 僅限於單一子網路。  
  
> [!IMPORTANT]  
>  不建議在實際執行環境中使用 DHCP。 如果有停機時間且 DHCP IP 租用到期，則需要額外的時間來註冊與接聽程式 DNS 名稱相關聯的新 DHCP 網路 IP 位址，而影響用戶端連接。 但是，DHCP 適合用於設定開發和測試環境，以驗證可用性群組的基本功能，也適合與應用程式整合。  
  
 **靜態 IP**  
 接聽程式將使用一個或多個靜態 IP 位址。 其他 IP 位址為選擇性。 若要建立跨多個子網路的可用性群組接聽程式，您必須在接聽程式組態中，針對每個子網路指定一個靜態 IP 位址。 請連絡您的網路系統管理員以取得這些靜態 IP 位址。  
  
 如果您選取 **[靜態 IP]** ，就會在 **[網路模式]** 欄位底下出現一個子網路方格。 此方格會顯示這個可用性群組接聽程式可存取之每個子網路的相關資訊。 在您按一下 **[加入]** 來加入靜態 IP 位址之前，此方格是空的。  
  
 資料行如下：  
  
 **子網路**  
 顯示您加入至可用性群組接聽程式之每個子網路的識別碼。  
  
 **IP 位址**  
 顯示給定子網路的 IP 位址。  對於給定的子網路，IP 位址是 IPv4 位址或 IPv6 位址。  
  
 **加入**  
 按一下可將靜態 IP 位址加入至選取的子網路或此接聽程式的其他子網路。 這會開啟 **[加入 IP 位址]** 對話方塊。 如需詳細資訊，請參閱[加入 IP 位址對話方塊 &#40;SQL Server Management Studio&#41;](../../../database-engine/availability-groups/windows/add-ip-address-dialog-box-sql-server-management-studio.md) 說明主題。  
  
 **移除**  
 按一下可從此接聽程式中移除選取的子網路。  
  
 **確定**  
 按一下可建立指定的可用性群組接聽程式。  
  
##  <a name="using-transact-sql"></a><a name="TsqlProcedure"></a> 使用 Transact-SQL  
 **建立或設定可用性群組接聽程式**  
  
1.  連接到裝載主要複本的伺服器執行個體。  
  
2.  使用 [CREATE AVAILABILITY GROUP](../../../t-sql/statements/create-availability-group-transact-sql.md) 陳述式的 LISTENER 選項或 [ALTER AVAILABILITY GROUP](../../../t-sql/statements/alter-availability-group-transact-sql.md) 陳述式的 ADD LISTENER 選項。  
  
     下列範例會將可用性群組接聽程式加入至名為 `MyAg2`的現有可用性群組。 系統會針對此接聽程式指定唯一的 DNS 名稱 `MyAg2ListenerIvP6`。 兩個複本位於不同的子網路上，因此接聽程式使用靜態 IP 位址 (建議方法)。 針對這兩個可用性複本，WITH IP 子句都會指定使用 IPv6 格式的靜態 IP 位址： `2001:4898:f0:f00f::cf3c and 2001:4898:e0:f213::4ce2`。 此範例也會指定使用選用的 PORT 引數指定通訊埠 `60173` 做為接聽程式通訊埠。  
  
    ```  
    ALTER AVAILABILITY GROUP MyAg2   
          ADD LISTENER 'MyAg2ListenerIvP6' ( WITH IP ( ('2001:db88:f0:f00f::cf3c'),('2001:4898:e0:f213::4ce2') ) , PORT = 60173 );   
    GO  
  
    ```  
  
##  <a name="using-powershell"></a><a name="PowerShellProcedure"></a> 使用 PowerShell  
 **建立或設定可用性群組接聽程式**  
  
1.  變更目錄 (**cd**) 為裝載主要複本的伺服器執行個體。  
  
2.  若要建立或修改可用性群組接聽程式，請使用下列其中一個指令程式：  
  
     **New-SqlAvailabilityGroupListener**  
     建立新的可用性群組接聽程式，並將其附加至現有的可用性群組。  
  
     例如，下列 **New-SqlAvailabilityGroupListener** 命令會針對可用性群組 `MyListener` 建立名為 `MyAg`的可用性群組接聽程式。 這個接聽程式將使用傳遞給 **-StaticIp** 參數的 IPv4 位址作為其虛擬 IP 位址。  
  
    ```  
    New-SqlAvailabilityGroupListener -Name MyListener `   
    -StaticIp '192.168.3.1/255.255.252.0' `   
    -Path SQLSERVER:\Sql\Computer\Instance\AvailabilityGroups\MyAg  
  
    ```  
  
     **Set-SqlAvailabilityGroupListener**  
     修改現有可用性群組接聽程式上的通訊埠設定。  
  
     例如，下列 **Set-SqlAvailabilityGroupListener** 命令會將名為 `MyListener` 之可用性群組接聽程式的通訊埠編號設定為 `1535`。 這個通訊埠是用來接聽接聽程式的連接。  
  
    ```  
    Set-SqlAvailabilityGroupListener -Port 1535 `   
    -Path SQLSERVER:\Sql\PrimaryServer\InstanceName\AvailabilityGroups\MyAg\AGListeners\MyListener  
  
    ```  
  
     **Add-SqlAGListenerstaticIp**  
     將靜態 IP 位址加入至現有的可用性群組接聽程式組態。 IP 位址可以是包含子網路的 IPv4 位址或 IPv6 位址。  
  
     例如，下列 **Add-SqlAGListenerstaticIp** 命令會將靜態 IPv4 位址加入 `MyListener` 可用性群組的 `MyAg`可用性群組接聽程式中。 這個 IPv6 位址會做為子網路 `255.255.252.0`之接聽程式的虛擬 IP 位址。 如果可用性群組跨越多個子網路，您就應該將每個子網路的靜態 IP 位址加入至接聽程式。  
  
    ```  
    $path = "SQLSERVER:\SQL\PrimaryServer\InstanceName\AvailabilityGroups\MyAg\AGListeners\ MyListener" `   
    Add-SqlAGListenerstaticIp -Path $path `   
    -StaticIp "2001:0db8:85a3:0000:0000:8a2e:0370:7334"  
    ```  
  
    > [!NOTE]  
    >  若要檢視 Cmdlet 的語法，請在 **PowerShell 環境中使用**  Get-Help [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Cmdlet。 如需詳細資訊，請參閱 [Get Help SQL Server PowerShell](../../../powershell/sql-server-powershell.md)。  
  
 **若要設定和使用 SQL Server PowerShell 提供者**  
  
-   [SQL Server PowerShell 提供者](../../../powershell/sql-server-powershell-provider.md)  
  
## <a name="troubleshooting"></a>疑難排解  
  
###  <a name="failure-to-create-an-availability-group-listener-because-of-active-directory-quotas"></a><a name="ADQuotas"></a> 由於 Active Directory 配額而無法建立可用性群組接聽程式  
 新可用性群組接聽程式的建立作業可能會在建立時失敗，因為您已達到參與叢集節點電腦帳戶的 Active Directory 配額。  如需詳細資訊，請參閱下列文章：  
  
-   [如何排解叢集服務帳戶修改電腦物件時所遇到的疑難](https://support.microsoft.com/kb/307532)  
  
-   [Active Directory 配額](https://technet.microsoft.com/library/cc904295\(WS.10\).aspx)  
  
##  <a name="follow-up-after-creating-an-availability-group-listener"></a><a name="FollowUp"></a> 後續操作：建立可用性群組接聽程式之後  
  
###  <a name="multisubnetfailover-keyword-and-associated-features"></a><a name="MultiSubnetFailover"></a> MultiSubnetFailover 關鍵字和相關聯的功能  
 **MultiSubnetFailover** 是新的連接字串關鍵字，可用來加快 SQL Server 2012 中 AlwaysOn 可用性群組和 AlwaysOn 容錯移轉叢集執行個體的容錯移轉速度。 當您在連接字串中設定 `MultiSubnetFailover=True` 時，就會啟用下列三項子功能：  
  
-   將多重子網路快速容錯移轉至 AlwaysOn 可用性群組或容錯移轉叢集執行個體的多重子網路接聽程式。  
  
-   將單一子網路快速容錯移轉至 AlwaysOn 可用性群組或容錯移轉叢集執行個體的單一子網路接聽程式。  
  
    -   這項功能是在連接至具有單一子網路中單一 IP 的接聽程式時使用。 它會執行更積極的 TCP 連接重試，加快單一子網路容錯移轉的速度。  
  
-   將具名執行個體解析為多重子網路 AlwaysOn 容錯移轉叢集執行個體。  
  
    -   這項功能可針對具有多個子網路端點的 AlwaysOn 容錯移轉叢集執行個體加入具名執行個體解析支援。  
  
 **NET Framework 3.5 或 OLEDB 不支援 MultiSubnetFailover=True**  
  
 **問題：** 如果您的可用性群組或容錯移轉叢集執行個體具有相依於不同子網路中多個 IP 位址的接聽程式名稱 (WSFC 叢集管理員中稱為網路名稱或用戶端存取點)，而且您使用 ADO.NET 搭配 .NET Framework 3.5 SP1 或使用 SQL Native Client 11.0 OLEDB，對可用性群組接聽程式的用戶端連線要求中，可能有 50% 會連線逾時。  
  
 **因應措施：** 建議您執行下列任一項工作。  
  
-   如果您沒有操作叢集資源的權限，請將連接逾時變更為 30 秒 (此值會產生 20 秒 TCP 逾時期間加上 10 秒緩衝時間)。  
  
     **優點**：如果發生跨子網路的容錯移轉，用戶端復原時間很短。  
  
     **缺點**：半數的用戶端連線需要 20 秒以上  
  
-   如果您有操作叢集資源的權限，比較建議的作法是將可用性群組接聽程式的網路名稱設定為 `RegisterAllProvidersIP=0`。 如需詳細資訊，請參閱本節稍後的＜RegisterAllProvidersIP 設定＞。  
  
     **優點：** 您不需要增加用戶端連線逾時值。  
  
     **缺點：** 如果發生跨子網路的容錯移轉，用戶端復原時間可能長達 15 分鐘或以上，取決於您的 **HostRecordTTL** 設定和跨網站 DNS/AD 複寫排程設定。  
  
###  <a name="registerallprovidersip-setting"></a><a name="RegisterAllProvidersIP"></a> RegisterAllProvidersIP 設定  
 當您使用 [!INCLUDE[ssManStudioFull](../../../includes/ssmanstudiofull-md.md)]、 [!INCLUDE[tsql](../../../includes/tsql-md.md)]或PowerShell 建立可用性群組接聽程式時，會在 WSFC 中建立用戶端存取點，且 **RegisterAllProvidersIP** 屬性會設定為 1 (true)。 這個屬性值的影響取決於用戶端連接字串，如下所示：  
  
-   將 **MultiSubnetFailover** 設為 true 的連接字串  
  
     [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] 會將  **RegisterAllProvidersIP** 屬性設定為 1，以便在用戶端連接字串依建議指定 `MultiSubnetFailover = True`的用戶端容錯移轉之後，縮短重新連接的時間。 請注意，若要利用接聽程式多重子網路功能，用戶端可能需要支援 **MultiSubnetFailover** 關鍵字的資料提供者。 如需多重子網路容錯移轉之驅動程式支援的相關資訊，請參閱 [AlwaysOn 用戶端連接性 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/always-on-client-connectivity-sql-server.md)。  
  
     如需多重子網路叢集的相關資訊，請參閱 [SQL Server 多重子網路叢集 &#40;SQL Server&#41;](../../../sql-server/failover-clusters/windows/sql-server-multi-subnet-clustering-sql-server.md)，您只可以為每個可用性群組建立一個接聽程式。  
  
    > [!TIP]  
    >  `RegisterAllProvidersIP = 1`時，如果您在 WSFC 叢集上執行 WSFC 驗證設定精靈，這個精靈會產生下列警告訊息：  
    >   
    >  「網路名稱 'Name:<network_name>' 的 RegisterAllProviderIP 屬性設定為 1。在目前的叢集設定中，這個值應該設定為 0。」  
    >   
    >  請忽略這個訊息。  
  
-   未將 **MultiSubnetFailover** 設為 true 的連接字串  
  
     `RegisterAllProvidersIP = 1`時，連接字串未使用 `MultiSubnetFailover = True`的任何用戶端將經歷嚴重的連線延遲。 這是因為這些用戶端嘗試循序連接到所有 IP。 相反地，如果 **RegisterAllProvidersIP** 變更為 0，則使用中 IP 位址會在 WSFC 叢集的用戶端存取點中註冊，因而減少舊版用戶端的延遲。 因此，如果您擁有的是無法使用 **MultiSubnetFailover** 屬性但需要連線到可用性群組接聽程式的舊版用戶端，建議您將 **RegisterAllProvidersIP** 變更為 0。  
  
    > [!IMPORTANT]  
    >  當您透過 WSFC 叢集 (容錯移轉叢集管理員 GUI) 建立可用性群組接聽程式時， **RegisterAllProvidersIP** 會預設為 0 (false)。  
  
###  <a name="hostrecordttl-setting"></a><a name="HostRecordTTL"></a> HostRecordTTL 設定  
 根據預設，用戶端會快取叢集 DNS 記錄 20 分鐘。  透過減少快取記錄的 **HostRecordTTL** 存留時間 (TTL)，舊版用戶端就可以更快速地重新連接。  不過，減少 **HostRecordTTL** 設定也可能導致傳輸至 DNS 伺服器的流量增加。  
  
###  <a name="sample-powershell-script-to-disable-registerallprovidersip-and-reduce-ttl"></a><a name="SampleScript"></a> 停用 RegisterAllProvidersIP 和減少 TTL 的範例 PowerShell 指令碼  
 下列 PowerShell 範例會示範如何針對接聽程式資源設定 **RegisterAllProvidersIP** 和 **HostRecordTTL** 叢集參數。  系統將會快取 DNS 記錄 5 分鐘，而不是預設的 20 分鐘。  對於無法使用 **MultiSubnetFailover** 參數的舊版用戶端而言，修改這兩個叢集參數可能會減少容錯移轉之後連接至正確 IP 位址的時間。  將 `yourListenerName` 取代為您要變更的接聽程式名稱。  
  
```  
Import-Module FailoverClusters  
Get-ClusterResource yourListenerName | Set-ClusterParameter RegisterAllProvidersIP 0   
Get-ClusterResource yourListenerName | Set-ClusterParameter HostRecordTTL 300  
Stop-ClusterResource yourListenerName  
Start-ClusterResource yourListenerName  
Start-Clustergroup yourListenerGroupName
```  
  
 如需容錯移轉期間復原次數的詳細資訊，請參閱 [Client Recovery Latency During Failover](../../../sql-server/failover-clusters/windows/sql-server-multi-subnet-clustering-sql-server.md#DNS)。  
  
###  <a name="follow-up-recommendations"></a><a name="FollowUpRecommendations"></a> 後續建議  
 建立可用性群組接聽程式之後：  
  
-   要求網路管理員將接聽程式的 IP 位址保留為專用。  
  
-   將接聽程式的 DNS 主機名稱提供給應用程式開發人員，以便在要求與這個可用性群組進行用戶端連接時，用於連接字串中。  
  
-   鼓勵開發人員將用戶端連接字串更新為指定 `MultiSubnetFailover = True`(可能的話)。 如需多重子網路容錯移轉之驅動程式支援的相關資訊，請參閱 [AlwaysOn 用戶端連接性 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/always-on-client-connectivity-sql-server.md)。  
  
###  <a name="create-an-additional-listener-for-an-availability-group-optional"></a><a name="CreateAdditionalListener"></a> 為可用性群組建立其他接聽程式 (選擇性)  
 您透過 SQL Server 建立一個接聽程式之後，可以加入另一個接聽程式，如下所示：  
  
1.  使用下列其中一種工具建立接聽程式：  
  
    -   **使用 WSFC 容錯移轉叢集管理員：**  
  
        1.  加入用戶端存取點並設定 IP 位址。  
  
        2.  使接聽程式連線。  
  
        3.  將相依性加入至 WSFC 可用性群組資源。  
  
         如需容錯移轉叢集管理員對話方塊和索引標籤的資訊，請參閱 [User Interface:The Failover Cluster Manager Snap-In](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc772502(v=ws.11)) (使用者介面：容錯移轉叢集管理員嵌入式管理單元)。  
  
    -   **使用適用容錯移轉叢集的 Windows PowerShell：**  
  
        1.  使用 [Add-ClusterResource](https://technet.microsoft.com/library/ee460983.aspx) 建立網路名稱和 IP 位址資源。  
  
        2.  使用 [Start-ClusterResource](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee461056(v=technet.10)) 啟動網路名稱資源。  
  
        3.  使用 [Add-ClusterResourceDependency](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee461014(v=technet.10)) 設定網路名稱和現有 SQL Server 可用性群組資源之間的相依性。  
  
         如需有關使用適用容錯移轉叢集的 Windows PowerShell 之詳細資訊，請參閱 [伺服器管理員命令概觀](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc732757(v=ws.11)#BKMK_wps)。  
  
2.  在新的接聽程式上啟動 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 接聽。 建立額外的接聽程式之後，連接到裝載主要可用性群組複本的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體，然後使用 [!INCLUDE[ssManStudioFull](../../../includes/ssmanstudiofull-md.md)]、 [!INCLUDE[tsql](../../../includes/tsql-md.md)]或 PowerShell 修改接聽程式通訊埠。  
  
 如需詳細資訊，請參閱 [如何為相同可用性群組建立多個接聽程式](/archive/blogs/sqlalwayson/how-to-create-multiple-listeners-for-same-availability-group-goden-yao) (SQL Server AlwaysOn 團隊部落格)。  
  
 
  
## <a name="next-steps"></a>後續步驟

既然已建立接聽程式，請將應用程式設定為[連線到接聽程式](listeners-client-connectivity-application-failover.md)。 您也可以檢閱各種[可用性群組監視策略](monitoring-of-availability-groups-sql-server.md)，以確保可用性群組的健全狀況。

您也可以[檢視接聽程式的屬性](view-availability-group-listener-properties-sql-server.md)，或了解如何[移除接聽程式](remove-an-availability-group-listener-sql-server.md) (如有必要)。 
