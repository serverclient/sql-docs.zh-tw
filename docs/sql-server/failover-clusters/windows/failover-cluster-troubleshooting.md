---
title: 容錯移轉叢集疑難排解 | Microsoft Docs
description: 了解容錯移轉叢集的疑難排解，包括從失敗中復原、解決常見的問題，以及使用擴充預存程式/COM 物件。
ms.custom: ''
ms.date: 10/21/2015
ms.prod: sql
ms.reviewer: ''
ms.technology: failover-cluster-instance
ms.topic: how-to
helpviewer_keywords:
- troublshooting, failover clustering
- failover clustering, troubleshooting
- cluster troubleshooting
ms.assetid: 84012320-5a7b-45b0-8feb-325bf0e21324
author: cawrites
ms.author: chadam
ms.openlocfilehash: 2891ecf2eb217691159c4afc1203faef8816a108
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97642763"
---
# <a name="failover-cluster-troubleshooting"></a>容錯移轉叢集疑難排解
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]
  本主題提供下列問題的相關資訊：  
  
-   基本疑難排解步驟。  
  
-   從容錯移轉叢集錯誤的狀況復原。  
  
-   解決最常見的容錯移轉叢集問題。  
  
-   使用擴充預存程序及 COM 物件。  
  
## <a name="basic-troubleshooting-steps"></a>基本疑難排解步驟  
 第一個診斷步驟是執行全新的叢集驗證檢查。 如需驗證的詳細資料，請參閱 [Failover Cluster Step-by-Step Guide:Validating Hardware for a Failover Cluster](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc732035(v=ws.10)) (容錯移轉叢集逐步指南：驗證容錯移轉叢集的硬體)。  您不需要中斷任何服務就可完成此作業，而不會影響任何線上叢集資源。 一旦安裝容錯移轉叢集功能之後，就能隨時執行驗證，包括在部署叢集之前、在叢集建立期間，以及在叢集正在執行時。 事實上，若叢集正在使用中，即可執行其他測試，來檢查是否遵循適用於高可用性工作量的最佳做法。 在這些大量測試中，其中只有一些會影響執行中的叢集工作負載，而這些全都位於儲存分類中，因此，略過這整個類別是避免干擾性測試的簡單方法。  
容錯移轉叢集隨附內建防護措施，避免在驗證期間執行儲存測試時發生意外的停機時間。 如果叢集在初始驗證時有任何線上群組，而且仍保留選取儲存測試，就會提示使用者確認他們是否想要執行所有測試 (並導致停機時間)，或略過測試任何線上群組的磁碟以避免產生停機時間。 如果測試中已排除整個儲存分類，則不會顯示此提示。 這將進行叢集驗證，但不會產生停機時間。  
  
#### <a name="how-to-revalidate-your-cluster"></a>如何重新驗證您的叢集  
  
1.  在 [容錯移轉叢集] 嵌入式管理單元的主控台樹狀目錄中，確定已選取 [容錯移轉叢集管理]，然後在 [管理] 下方，按一下 [驗證設定]。  
  
2.  遵循精靈的指示來指定伺服器和測試，然後執行測試。 執行測試之後，會出現 **[摘要]** 頁面。  
  
3.  仍在 [摘要]  頁面時，按一下 [檢視報告]  以檢視測試結果。  
  
     若要在關閉精靈之後檢視測試結果，請參閱 **%SystemRoot%\Cluster\Reports\Validation Report date and time.html** ，其中 **%SystemRoot%** 是安裝作業系統所在的資料夾 (例如， **C:\Windows**)。  
  
4.  若要檢視將可協助您解譯結果的說明主題，請按一下 [深入了解叢集驗證測試] 。  
  
 若要在關閉精靈之後檢視叢集驗證的說明主題，可在 [容錯移轉叢集] 嵌入式管理單元中，依序按一下 [說明]、[說明主題] 及 [內容] 索引標籤，展開容錯移轉叢集說明的內容，然後按一下 [驗證容錯移轉叢集設定]。  驗證精靈完成之後，[摘要報告]  將會顯示結果。 所有測試都必須具備綠色的核取記號來表示通過，或者在某些情況下呈現黃色三角形 (警告)。 尋找問題區域 (紅色 X 或黃色問號) 時，在摘要說明測試結果的報表部分中，按一下個別測試來檢閱詳細資料。 任何紅色 X 的問題都必須加以解決，然後才能疑難排解 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 問題。  
  
 **安裝更新**  
  
 安裝更新是避免發生系統問題很重要的一部分。 有用的連結︰  
  
-   [適用於 Windows Server 2012 R2 架構容錯移轉叢集的建議 Hotfix 和更新](https://support.microsoft.com/kb/2920151)  
  
-   [適用於 Windows Server 2012 架構容錯移轉叢集的建議 Hotfix 和更新](https://support.microsoft.com/kb/2784261)  
  
-   [適用於 Windows Server 2008 R2 架構容錯移轉叢集的建議 Hotfix 和更新](https://support.microsoft.com/kb/980054)  
  
-   [適用於 Windows Server 2008 架構容錯移轉叢集的建議 Hotfix 和更新](https://support.microsoft.com/kb/957311)  
  
## <a name="recovering-from-failover-cluster-failure"></a>從容錯移轉叢集錯誤的狀況復原  
 通常，容錯移轉叢集發生錯誤的原因有兩個：  
  
-   在雙節點的叢集中，有一個節點的硬體故障。 這種硬體故障可能是因為 SCSI 卡或作業系統的錯誤所導致。  
  
     若要從此錯誤中復原，請使用 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 安裝程式將故障的節點從容錯移轉叢集中移除、在電腦離線的情況下修復故障的硬體、將該機器回復連線，然後再將修復的節點重新加入容錯移轉叢集執行個體中。  
  
     如需詳細資訊，請參閱[建立新的 SQL Server 容錯移轉叢集 &#40;Setup&#41;](../../../sql-server/failover-clusters/install/create-a-new-sql-server-failover-cluster-setup.md) 和[從容錯移轉叢集執行個體失敗的狀況復原](../../../sql-server/failover-clusters/windows/recover-from-failover-cluster-instance-failure.md)。  
  
-   作業系統發生錯誤。 在此情況下，節點會離線，但並非無可挽回的嚴重錯誤。  
  
     若要復原錯誤的作業系統，請復原節點，並測試容錯移轉。 如果 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體的容錯移轉不當，您就必須使用 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 安裝程式將 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 從容錯移轉叢集移除、進行必要的修復、將該電腦回復連線，然後再將修復的節點重新加入容錯移轉叢集執行個體中。  
  
     用這種方式來復原錯誤的作業系統會需要一些時間。 如果可以輕易復原作業系統錯誤，請避免使用這項技術。  
  
     如需詳細資訊，請參閱[建立新的 SQL Server 容錯移轉叢集 &#40;Setup&#41;](../../../sql-server/failover-clusters/install/create-a-new-sql-server-failover-cluster-setup.md) 和[如何：從狀況 2 中的容錯移轉叢集失敗進行還原](recover-from-failover-cluster-instance-failure.md)。  
  
## <a name="resolving-common-problems"></a>解決一般問題  
 下列清單描述常見的使用狀況問題，並說明如何解決。  
  
### <a name="problem-incorrect-use-of-command-prompt-syntax-to-install-sql-server"></a>問題：使用錯誤的命令列提示語法安裝 SQL Server  
 **問題 1：** 從命令提示字元使用 **/qn** 參數將難以診斷安裝程式問題，因為 **/qn** 參數會抑制所有安裝程式對話方塊與錯誤訊息。 如果指定 **/qn** 參數，所有的安裝程式訊息 (包括錯誤訊息) 都會寫入安裝程式記錄檔。 如需記錄檔的詳細資訊，請參閱 [檢視與讀取 SQL Server 安裝程式記錄檔](../../../database-engine/install-windows/view-and-read-sql-server-setup-log-files.md)。  
  
 **解決方法 1**：使用 **/qb** 參數，而不使用 **/qn** 參數。 如果使用 **/qb** 參數，將會顯示每個步驟的基本 UI (包括錯誤訊息)。  
  
### <a name="problem-sql-server-cannot-log-on-to-the-network-after-it-migrates-to-another-node"></a>問題：SQL Server 無法在移轉至另一個節點之後登入至網路  
 **問題 1：** [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 服務帳戶無法與網域控制站連絡。  
  
 **解決方法 1**：檢查您的事件記錄檔，尋找是否有網路問題的相關記錄 (例如：介面卡失敗或 DNS 問題)。 確認您可以偵測到 (ping) 您的網域控制站。  
  
 **問題 2：** [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 服務帳戶密碼在所有叢集節點上並不相同，或節點並未重新啟動從失敗節點移轉的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 服務。  
  
 **解決方法 2：** 使用「[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 組態管理員」來變更 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 服務帳戶密碼。 若您不想這麼做，那麼當您變更了某個節點上的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 服務帳戶密碼時，也必須同時變更其他所有節點上的密碼。 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 組態管理員」會替您自動執行此作業。  
  
### <a name="problem-sql-server-cannot-access-the-cluster-disks"></a>問題：SQL Server 無法存取叢集磁碟  
 **問題 1：** 所有節點上的韌體或驅動程式都未更新。  
  
 **解決方法 1：** 確定所有節點都使用正確韌體版本與相同的驅動程式版本。  
  
 **問題 2：** 節點無法復原從共用叢集磁碟 (具有不同的磁碟機代號) 上之失敗節點移轉的叢集磁碟。  
  
 **解決方法 2：** 叢集磁碟在這兩部伺服器上的磁碟機代號必須相同。 若不相同，請檢查作業系統和 [!INCLUDE[msCoName](../../../includes/msconame-md.md)] Cluster Service (MSCS) 的原始安裝。  
  
### <a name="problem-failure-of-a-sql-server-service-causes-failover"></a>問題：SQL Server 服務失敗導致容錯移轉  
 **解決方式：** 為預防特定伺服器失敗導致 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 群組發生容錯移轉，請使用 Windows 中的「叢集管理員」來設定這些服務，如下所示：  
  
-   清除 **[全文檢索屬性]** 對話方塊之 **[進階]** 索引標籤上的 **[影響群組]** 核取方塊。 然而，如果 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 導致容錯移轉，全文檢索搜尋服務就會重新啟動。  
  
### <a name="problem-sql-server-does-not-start-automatically"></a>問題：SQL Server 未自動啟動  
 **解決方案：** 使用 MSCS 中的「叢集管理員」自動啟動容錯移轉叢集。 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 服務應設定為手動啟動，而且您應該在 MSCS 中設定「叢集管理員」來啟動 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 服務。 如需詳細資訊，請參閱 [管理服務](https://msdn.microsoft.com/library/ms178096\(v=sql.105\).aspx)。  
  
### <a name="problem-the-network-name-is-offline-and-you-cannot-connect-to-sql-server-using-tcpip"></a>問題：「網路名稱」已離線，且您無法使用 TCP/IP 離線到 SQL Server  
 **問題 1：** 對於設定為要求 DNS 的叢集資源，DNS 發生失敗。  
  
 **解決方法 1：** 更正 DNS 問題。  
  
 **問題 2：** 網路上有重複的名稱。  
  
 **解決方法 2：** 使用 NBTSTAT 來尋找重複的名稱，然後更正此問題。  
  
 **問題 3：** [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 並非使用具名管道來連線。  
  
 **解決方法 3：** 若要使用具名管道來連線，請使用 SQL Server 組態管理員來建立別名以連線到適當的電腦。 例如，若您有一個具有兩個節點的叢集 (**節點 A** 與 **節點 B**)，以及具有預設執行個體的容錯移轉叢集執行個體 (**Virtsql**)，則您可以利用下列步驟連接到網路名稱資源已離線的伺服器：  
  
1.  使用「叢集管理員」來判斷含有 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體的群組是在哪個節點上執行。 就此範例而言，是 **節點 A**。  
  
2.  使用 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] net start **來啟動該電腦上的** 服務。 如需使用 **net start** 的詳細資訊，請參閱 [手動啟動 SQL Server](https://msdn.microsoft.com/library/ms191193\(v=sql.105\).aspx)。  
  
3.  啟動 **節點 A** 上的「[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] SQL Server 組態管理員」。檢視伺服器所接聽的管道名稱， 應該類似於 \\\\.\\$$\VIRTSQL\pipe\sql\query。  
  
4.  在用戶端電腦上，啟動「SQL Server 組態管理員」。  
  
5.  建立別名 SQLTEST1，透過具名管道來連接到此管道名稱。 若要執行此作業，請輸入 **節點 A** 來作為伺服器名稱，並將管道名稱編輯成 \\\\.\pipe\\$$\VIRTSQL\sql\query。  
  
6.  使用 SQLTEST1 做為伺服器名稱與這個執行個體連接。  
  
### <a name="problem-sql-server-setup-fails-on-a-cluster-with-error-11001"></a>問題：SQL Server 安裝程式在叢集上失敗，錯誤為 11001  
 **問題：** [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\MSSQL.X\Cluster] 中被遺棄的登錄機碼  
  
 **解決方案：** 確定目前並未使用 MSSQL.X 登錄區，然後刪除該叢集機碼。  
  
### <a name="problem-cluster-setup-error-the-installer-has-insufficient-privileges-to-access-this-directory-drivemicrosoft-sql-server-the-installation-cannot-continue-log-on-as-an-administrator-or-contact-your-system-administrator"></a>問題：叢集設定錯誤：「安裝程式的權限不足，無法存取這個目錄：\<drive>\Microsoft SQL Server。 安裝無法繼續， 以管理員的身分登入或是連絡您的系統管理員」  
 **問題：** 此問題是由未正確分割的 SCSI 共用磁碟機所造成。  
  
 **解決方案：** 使用下列步驟，在共用磁碟上重新建立單一分割區：  
  
1.  從叢集中刪除磁碟資源。  
  
2.  刪除磁碟上的所有分割區。  
  
3.  在磁碟屬性中確認該磁碟是基本磁碟。  
  
4.  在共用磁碟上建立一個分割區，將磁碟格式化，並指定磁碟的磁碟機代號。  
  
5.  使用「叢集管理員」(cluadmin) 將磁碟加入叢集。  
  
6.  執行 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 安裝程式。  
  
### <a name="problem-applications-fail-to-enlist-sql-server-resources-in-a-distributed-transaction"></a>問題：應用程式無法在分散式交易中編列 SQL Server 資源  
 **問題：** 因為 Windows 中的 [!INCLUDE[msCoName](../../../includes/msconame-md.md)] 分散式交易協調器 (MS DTC) 設定不完整，因此應用程式可能無法將 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 資源編列為分散式交易。 這個問題會影響使用分散式交易的連結伺服器、分散式查詢和遠端預存程序。 如需有關如何設定 MS DTC 的詳細資訊，請參閱＜ [Before Installing Failover Clustering](../../../sql-server/failover-clusters/install/before-installing-failover-clustering.md)＞。  
  
 **解決方案：** 若要預防這類問題發生，您必須在有安裝 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 及設定 MS DTC 的伺服器上完整啟用 MS DTC 服務。  
  
 若要完整啟用 MS DTC，請使用下列步驟：  
  
1.  在 [控制台] 中，開啟 [系統管理工具] ，然後開啟 [電腦管理] 。  
  
2.  在 [電腦管理] 的左窗格中，展開 **[服務及應用程式]** ，然後按一下 **[服務]** 。  
  
3.  在 [電腦管理] 的右窗格中，以滑鼠右鍵按一下 [分散式交易協調器]，並選取 [屬性]。  
  
4.  在 **[分散式交易協調器]** 視窗中，按一下 **[一般]** 索引標籤，然後按一下 **[停止]** 來停止服務。  
  
5.  在 [分散式交易協調器] 視窗中，按一下 [登入] 索引標籤，然後設定登入帳戶 NT AUTHORITY\NetworkService。  
  
6.  按一下 **[套用]** 和 **[確定]** ，關閉 **[分散式交易協調器]** 視窗。 關閉 **[電腦管理]** 視窗。 關閉 **[系統管理工具]** 視窗。  
  
## <a name="using-extended-stored-procedures-and-com-objects"></a>使用擴充預存程序與 COM 物件  
 當您使用具有容錯移轉叢集組態的擴充預存程序時，所有擴充預存程序都必須安裝在與 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]相依的叢集磁碟上。 這麼做可確保當節點容錯移轉時，仍可使用擴充預存程序。  
  
 如果擴充預存程序使用 COM 元件，系統管理員必須將 COM 元件登錄在叢集的每一個節點上。 載入與執行 COM 元件的資訊必須位於作用中節點的登錄中，才能建立該元件。 否則，資訊仍會留在第一次登錄 COM 元件的電腦登錄中。  
  
## <a name="see-also"></a>另請參閱  
 [檢視與讀取 SQL Server 安裝程式記錄檔](../../../database-engine/install-windows/view-and-read-sql-server-setup-log-files.md)   
 [擴充預存程序運作方式](../../../relational-databases/extended-stored-procedures-programming/how-extended-stored-procedures-work.md)   
 [擴充預存程序的執行特性](../../../relational-databases/extended-stored-procedures-programming/execution-characteristics-of-extended-stored-procedures.md)  
  
