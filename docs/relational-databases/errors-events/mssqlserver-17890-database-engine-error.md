---
description: MSSQLSERVER_17890
title: MSSQLSERVER_17890
ms.custom: ''
ms.date: 12/25/2020
ms.prod: sql
ms.reviewer: ramakoni1, pijocoder, suresh-kandoth, vencher, tejasaks, docast
ms.technology: supportability
ms.topic: language-reference
helpviewer_keywords:
- 17890 (Database Engine error)
ms.assetid: ''
author: suresh-kandoth
ms.author: ramakoni
ms.openlocfilehash: 6a854486d1867e84bcd9b13cb148d3026a41d51f
ms.sourcegitcommit: d819173fb91af6f20ca6ee59686c35c71b060fbc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/28/2020
ms.locfileid: "97797759"
---
# <a name="mssqlserver_17890"></a>MSSQLSERVER_17890
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

## <a name="details"></a>詳細資料

|屬性|值|
|---|---|
|產品名稱|SQL Server|
|事件識別碼|17890|
|事件來源|MSSQLSERVER|
|元件|SQLEngine|
|符號名稱|SRV_WS_TRIMMED|
|訊息文字|SQL Server 處理序記憶體的重要部分已被移出分頁。這可能導致效能變差。 持續時間: %d 秒， 工作集 (KB): %I64d，已認可 (KB): %I64d，記憶體使用情形: %d%%。|
||

## <a name="explanation"></a>說明

您可能會在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 錯誤記錄檔或 Windows 應用程式事件記錄檔中遇到下列錯誤訊息。

> SQL Server 處理序記憶體的重要部分已被移出分頁。這可能導致效能變差。 持續時間︰0 秒。 工作集 (KB):3383250，已認可 (KB):9112480，記憶體使用情形:37%。

您可能也會注意到，SQL Server 上查詢執行和所有其他作業的效能突然降低。

## <a name="cause"></a>原因

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會監視有關 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 處理序的各種記憶體相關資訊。 在此情況下，其已偵測到處理序的工作集小於已認可處理序記憶體的 50%。 因此，會列印此警告。 此警告的一般原因包括：

- 作業系統會將大部分 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 認可的記憶體移出分頁到分頁檔。
- 這可能是因為其他應用程式或作業系統需求對於記憶體的需求突然增加所致。
- 當某些裝置驅動程式要求連續記憶體配置以滿足其需求時，可能也會發生此情況。

## <a name="user-action"></a>使用者動作

您可以透過鎖定實體記憶體中針對緩衝集區所配置的記憶體，來防止 Windows 作業系統將 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 處理序的緩衝集區記憶體移出分頁。 您可以將 [鎖定記憶體中的分頁] 使用者權限指派給用來作為 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 服務啟動帳戶的使用者帳戶，以鎖定記憶體。 但在您實作此解決方案之前，請先檢閱[導致將 SQL Server 記憶體移出分頁的原因](#what-causes-sql-server-memory-to-be-paged-out)和＜重要考量＞等小節，然後將 [鎖定記憶體中的分頁] 使用者權限指派給 SQL Server 的執行個體

> [!NOTE]
> 使用 [鎖定記憶體中的分頁] 可確保 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 所管理的記憶體不會被移出分頁。不過，執行緒堆疊、EXE 與任何 DLL 映像、堆積記憶體、CLR 記憶體仍可由 OS 移出分頁。
>
> 從 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 2008 SP1 累積更新 2 開始，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Standard 與 Enterprise 版本都可以使用 [鎖定記憶體中的分頁] 使用者權限。 如需鎖定分頁支援的詳細資訊，請參閱 [KB970070 - SQL Server Standard Edition (64 位元) 系統上對鎖定分頁的支援](https://support.microsoft.com/help/970070) \(英文\)。

若要指派 [鎖定記憶體中的分頁] 使用者權限，請遵循下列步驟：

1. 依序按一下 [開始] 與 [執行]、輸入 *gpedit.msc*，然後按一下 [確定]。
1. 請注意，[群組原則] 對話方塊隨即出現。
1. 依序展開 [電腦設定] 與 [Windows 設定]。
1. 展開 [安全性設定]，然後展開 [本機原則]。
1. 按一下 [使用者權限指派]，然後按兩下 [鎖定記憶體中的分頁]。
1. 在 [本機安全性原則設定] 對話方塊中，按一下 [新增使用者或群組] 。
1. 在 [選取使用者或群組]  對話方塊中，新增具有執行 Sqlservr.exe 檔案權限的帳戶，然後按一下 [確定]。
1. 關閉 [群組原則] 對話方塊。
1. 重新啟動 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 服務。

當您指派 [鎖定記憶體中的分頁] 使用者權限並重新啟動 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 服務之後，Windows 作業系統就不會再在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 處理序中將緩衝集區記憶體移出分頁。 不過，Windows 作業系統仍然可以在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 處理序中將非緩衝集區記憶體移出分頁。

您可以在啟動時，透過確認已將下列訊息寫入 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 錯誤記錄檔，來驗證 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體所使用的使用者權限：「針對緩衝集區使用鎖定分頁」

此訊息只適用 SQL Server。 如需有關錯誤記錄檔中此訊息的詳細資訊，請參閱下列文章：[我是否必須在本機系統中指派 [鎖定記憶體中的分頁] 權限](https://techcommunity.microsoft.com/t5/sql-server-support/do-i-have-to-assign-the-lock-pages-in-memory-privilege-for-local/ba-p/315426) \(英文\)

當 Windows 作業系統將非緩衝集區記憶體移出分頁時，您可能還是會遇到效能問題。 不過，＜說明＞一節中所述的錯誤訊息不會記錄於 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 錯誤記錄檔中。

## <a name="what-causes-sql-server-memory-to-be-paged-out"></a>導致將 SQL Server 記憶體移出分頁的原因

可能導致此問題的問題可分為三大類：

- 應用程式相關問題：所有應用程式一起耗盡了可用實體記憶體，而 OS 必須釋放一些記憶體，以供新應用程式對資源的要求使用。 一般來說，這裡所述的方法是，尋找哪些應用程式會耗盡記憶體，並採取必要步驟來平衡記憶體，使其不會導致 RAM 耗盡。
- 裝置驅動程式問題：如果驅動程式呼叫記憶體配置函式的方式不正確，裝置驅動程式可能會導致對所有處理序的工作集進行分頁。
- 作業系統問題

您可以在下面找到這其中每個類別的相關資訊

- **應用程式相關問題**：這些應用程式一起可能會耗用系統上的所有 RAM。 如果對記憶體提出新要求，OS 就會嘗試滿足新要求，但如果沒有可用記憶體，其將會修剪執行中應用程式的工作集來滿足記憶體要求。 在這種情況下，您可能會觀察到，即使不是所有應用程式，大部分應用程式的工作集都會顯著下降。 若要觀察此情況，請針對系統上的所有應用程式收集下列效能監視器計數器：

  - 效能物件：Process
  - 計數器：工作集
  
  此外，請監視下列計數器，以相互關聯系統上可用的實體記憶體數量。
  
  - 效能物件：記憶體
  - 計數器：可用的記憶體 (MB)
  
  您可能會觀察到的典型行為是可用的記憶體降低到接近 0 MB，同時系統上大部分 (全部) 處理序的工作集計數器也會突然下降。 如果您觀察到此類行為，則可能需要採取步驟來降低系統上的記憶體使用率，包括減少 SQL Server 的「最大伺服器記憶體」。
  
    應用程式可能也會使用太多系統快取，而且可能造成系統快取的大幅增長。 為了回應系統快取的增長，系統會將 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 處理序或其他應用程式的工作集移出分頁。 如果您遇到此問題，則可在應用程式中使用一些記憶體管理函式。 這些函式會控制檔案 I/O 作業可在應用程式中使用的系統快取空間。 例如，您可以使用 SetSystemFileCacheSize 函式與 GetSystemFileCacheSize 函式，來控制檔案 I/O 作業可以使用的系統快取空間。
  
    您可以使用記憶體效能物件來檢視此物件中各種計數器的值，以判斷系統快取工作集是否使用太多記憶體。 例如，您可以檢視 Cache Bytes 與 System Cache Resident Bytes 計數器。 如需此主題的詳細資訊，請參閱：
  
    - [太多快取](/archive/blogs/ntdebugging/too-much-cache)
    - [Microsoft Windows 動態快取服務](/archive/blogs/ntdebugging/microsoft-windows-dynamic-cache-service)
    - [當系統檔案快取耗用大部分的實體 RAM 時，您會遇到應用程式與服務的效能問題](https://support.microsoft.com/help/976618) \(部分機器翻譯\)
  
    您可以下載並部署「Microsoft Windows 動態快取服務」，以控制系統快取所耗用的記憶體。

- **裝置驅動程式問題**：如果裝置驅動程式使用 `MmAllocateContiguousMemory` 函式，而且如果將 HighestAcceptableAddress 參數的值設定為小於 4 GB，則 Windows 作業系統可能會將系統上處理序 (包括 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 處理序) 的工作集移出分頁。 若要解決此問題，請連絡裝置驅動程式的廠商以取得驅動程式更新。

    當裝置驅動程式嘗試配置記憶體時，Windows 作業系統可能會將其他應用程式的工作集移出分頁。 此 Windows Hotfix 可讓您使用事件追蹤來尋找導致問題的裝置驅動程式。 若要尋找導致工作集修剪行為之特定驅動程式的詳細資訊，請參閱[識別配置連續記憶體的驅動程式](/previous-versions/windows/desktop/xperf/identifying-drivers-that-allocate-contiguous-memory)。

- **作業系統問題**：若要解決造成 Windows 作業系統將 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 處理序的工作集移出分頁的已知問題，請套用下列 Microsoft 知識庫文件中所述的 Hotfix。

  > [!NOTE]
  > Hotfix 是累積的。 較新版本的 Hotfix 包含該 Hotfix 的較舊版本。

  - 當系統使用一些進階的 TCP 功能時，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 集可能會遭修剪。 如需詳細資訊，請參閱[如何對進階網路效能功能 (例如 RSS 與 NetDMA) 進行疑難排解](/troubleshoot/windows-server/networking/troubleshoot-network-performance-features-rss-netdma)。

  - 如果您在 Windows Server 2008 上執行 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]，就必須套用已知問題的修正程式，這些已知問題會導致其他作業系統元件修剪工作集或過度耗用不必要的記憶體。 如需詳細資訊，請檢閱下列文章：[當您使用 Active Directory 診斷範本執行 Perfmon.exe，以在以 Windows Server 2008 為基礎的網域控制站上產生報告時，報告產生程序可能會停止回應](/troubleshoot/windows-server/performance/report-generation-process-stops-responding)。

  - 如果您在 Windows Serve 2008 R2 上執行 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]，就必須針對可能導致工作集修剪的已知問題套用修正程式。 如需詳細資訊，請檢閱下列文章：

    - [執行大型應用程式時，執行 Windows 7 或 Windows Server 2008 R2 的電腦會變得沒有回應](https://support.microsoft.com/help/979149) \(機器翻譯\)
    - [在具有 NUMA 型處理器且執行 Windows Server 2008 R2 或 Windows 7 的電腦上，如果執行緒要求位於前 4 GB 記憶體內的大量記憶體時，即會發生效能不佳的狀況](https://support.microsoft.com/help/2155311) \(英文\)
    - [當 Windows Server 2008 R2 中使用 Storport 驅動程式時，電腦會間歇性地發生執行不佳或停止回應的狀況](https://support.microsoft.com/help/2468345) \(英文\)

## <a name="important-considerations-before-you-assign-the-lock-pages-in-memory-user-right"></a>指派 [鎖定記憶體中的分頁] 使用者權限之前的重要考量

指派 [鎖定記憶體中的分頁] 使用者權限之前，您應該先進行其他考量。 如果您在設定錯誤的系統上指派此使用者權限，系統可能會變得不穩定，或遇到整個系統的效能降低。 此外，事件記錄檔中可能會記錄事件識別碼 333。

如果您與 Microsoft 客戶支援服務 (CSS) 連絡以解決這些問題，CSS 工程師可能會要求您針對用來作為 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 服務啟動帳戶的使用者帳戶撤銷此使用者權限。 這對收集重要效能資料而言可能是必要步驟，讓 CSS 工程師可用來針對 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的各種選項，以及其他在系統上執行的應用程式進行必要設定。 在 CSS 工程師收集效能資料之後，您就可以將 [鎖定記憶體中的分頁] 使用者權限指派給 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 服務的啟動帳戶。

指派 [鎖定記憶體中的分頁] 使用者權限之前，請務必擷取效能監視器記錄，以判斷系統上安裝的各種應用程式與服務的記憶體需求。 這些應用程式也包括 SQL Server。 為了判斷記憶體需求，請收集下列基準資訊：

- 確定您已正確設定 [最大伺服器記憶體] 選項與 [最小伺服器記憶體] 選項。 這些選項只會反映 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 處理序之緩衝集區的記憶體需求。 這些選項未包含為 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 處理序內的其他元件所配置的記憶體。 這些元件包括下列項目：

  - [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 背景工作執行緒
  - [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 處理序會在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 處理序的位址空間中載入的各種 DLL 與元件
  - 備份與還原作業

- DLL 與元件包括各種 OLE DB 提供者、擴充預存程序、用於 sp_OACreate 預存程序的 Microsoft COM 物件、連結的伺服器與 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] CLR。 為這些元件配置的記憶體會落在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 處理序位址空間的非緩衝集區區域底下。 若要理想地判斷整個 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 處理序可使用的記憶體數量上限，您必須從希望 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 處理序使用的記憶體總數中減去配置給未使用緩衝集區之元件的記憶體。 然後，您可以使用餘數值來設定 [最大伺服器記憶體] 選項。 設定 [最大伺服器記憶體] 選項與 [最小伺服器記憶體] 選項之前，請仔細參閱《[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 線上叢書》中的＜手動設定記憶體選項＞主題。

- 判斷其他應用程式與 Windows 作業系統元件的記憶體需求。 應用程式可能包含其他 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 元件，例如 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 複寫代理程式、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Reporting Services、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Analysis Services、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Integration Services 與 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 全文檢索搜尋。 執行備份作業與檔案複製作業的應用程式可能會使用大量記憶體。 請將大量複製及產生檔案 IO 之快照集代理程式等作業納入考量。 當您判斷 [最大伺服器記憶體] 選項與 [最小伺服器記憶體] 選項的值時，必須考慮所有這些應用程式的記憶體需求。 您可以在每個處理序的 [處理序] 物件底下，使用 Private Bytes 計數器與 Working Set 計數器來判斷特定處理序的記憶體需求。

- 預設已將 [鎖定記憶體中的分頁] 使用者權限指派給內建的本機系統帳戶。 如需詳細資訊，請瀏覽下列 Microsoft 網站：[我是否必須針對本機系統指派 [鎖定記憶體中的分頁] 權限](https://techcommunity.microsoft.com/t5/sql-server-support/do-i-have-to-assign-the-lock-pages-in-memory-privilege-for-local/ba-p/315426?advanced=false&collapse_discussion=true&search_type=thread) \(英文\)

- 如果您針對網域中的所有 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 處理序全域使用 Windows 使用者帳戶，請使用群組原則設定來判斷要指派的使用者權限。 32 位元的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 處理序可能會使用此帳戶作為啟動帳戶。 不過，此帳戶需要 [鎖定記憶體中的分頁] 使用者權限，才能啟用 `Address Windowing Extensions` (AWE) 功能。 如需詳細資訊，請參閱《[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 線上叢書》中的＜為 SQL Server 提供最大的記憶體數量＞主題。

- 在您為多個 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體設定 [最大伺服器記憶體] 選項與 [最小伺服器記憶體] 選項之前，請考慮針對每個 SQL Server 執行個體的非緩衝集區記憶體需求。 然後，針對每個 SQL Server 執行個體設定這些選項。

在理想的情況下，您會在尖峰負載期間收集此基準資訊。 因此，您可以判斷各種應用程式與元件的記憶體需求，以支援尖峰負載。 視系統上執行的活動和應用程式而定，記憶體需求會因系統而異。 您可以查詢動態管理檢視 sys.dm_os_process_memory 中提供的資訊，以了解系統是否遇到記憶體不足的狀況。 如需詳細資訊，請參閱 [sys.dm_os_process_memory (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-process-memory-transact-sql)。

## <a name="improvements-added-in-windows-server-2008-and-r2-version"></a>Windows Server 2008 與 R2 版本中新增的改進

Windows Server 2008 與 Windows Server 2008 R2 改進了連續記憶體配置機制。 此改進讓 Windows Server 2008 與 Windows Server 2008 R2 能夠在新的記憶體要求抵達時，在一定程度上降低將應用程式的工作集移出分頁的影響。

以下說明 Microsoft 白皮書＜Windows 中記憶體管理的進展＞中的改進：

*在 Windows Server 2008 中，已大幅增強實體連續記憶體的配置。配置連續記憶體的要求較可能成功，因為記憶體管理員現在可以動態取代分頁，通常不需修剪工作集或執行 I/O 作業。此外，許多其他類型的分頁 (例如，核心堆疊與檔案系統中繼資料分頁等) 現在都是要用來取代的候選項目。因此，在任何指定的時間，通常會有更多連續記憶體可供使用。此外，取得這類配置的成本會大幅降低。*

如需詳細資訊，請參閱 [SQL Server 工作集修剪問題](https://techcommunity.microsoft.com/t5/sql-server-support/sql-server-working-set-trim-problems-consider/ba-p/315462?advanced=false&collapse_discussion=true&search_type=thread) \(英文\)。

本文章討論的協力廠商產品是由獨立於 Microsoft 的公司製造。 Microsoft 對於這些產品的效能或可靠性不提供任何默示或其他保證。
