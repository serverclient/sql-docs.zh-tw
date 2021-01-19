---
title: Linux 上的 SQL Server 可用性群組
description: 了解 Linux 上的 SQL Server Always On 可用性群組的特性。
ms.custom: seo-lt-2019
author: VanMSFT
ms.author: vanto
ms.reviewer: vanto
ms.date: 04/17/2019
ms.topic: conceptual
ms.prod: sql
ms.technology: linux
ms.assetid: e37742d4-541c-4d43-9ec7-a5f9b2c0e5d1
ms.openlocfilehash: 7573e1faa9d6cb4bbf75dc4e516da22344523bff
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98172020"
---
# <a name="always-on-availability-groups-on-linux"></a>Linux 上的 Always On 可用性群組

[!INCLUDE [SQL Server - Linux](../includes/applies-to-version/sql-linux.md)]

此文章說明 Linux 型 [!INCLUDE[ssnoversion-md](../includes/ssnoversion-md.md)] 安裝底下 Always On 可用性群組 (AG) 的特性。 它也涵蓋了 Linux 型與 Windows Server 容錯移轉叢集 (WSFC) 型 AG 之間的差異。 如需 AG 的基本概念，請參閱 [Windows 型文件](../database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server.md)，因為它們在 Windows 和 Linux 上的作用相同，但 WSFC 除外。

從高階觀點來看，Linux 上 [!INCLUDE[ssnoversion-md](../includes/ssnoversion-md.md)] 底下的可用性群組，與其在 WSFC 型實作上相同。 這表示所有的限制和功能都一樣，但有一些例外狀況。 主要差異包括：

-   從 SQL Server 2017 CU16 開始，Linux 底下就支援 Microsoft 分散式交易協調器 (DTC)。 不過，Linux 上的「可用性群組」尚不支援 DTC。 如果您的應用程式要求使用分散式交易且需要 AG，請在 Windows 上部署 [!INCLUDE[ssnoversion-md](../includes/ssnoversion-md.md)]。
-   需要高可用性的 Linux 型部署會使用 Pacemaker 來進行叢集化，而不是使用 WSFC。
-   不同於 Windows 上大部分適用於 AG 的設定 (但不包括工作群組叢集案例)，Pacemaker 絕對不需要 Active Directory Domain Services (AD DS)。
-   在 Linux 和 Windows 之間，將 AG 從一個節點容錯到另一個節點的方式各不相同。
-   某些設定 (例如 `required_synchronized_secondaries_to_commit`) 只能透過 Linux 上的 Pacemaker 進行變更，而 WSFC 型安裝則會使用 Transact-SQL。

## <a name="number-of-replicas-and-cluster-nodes"></a>複本和叢集節點的數目

[!INCLUDE[ssstandard-md](../includes/ssstandard-md.md)] 中的 AG 總共有兩個複本：一個主要複本，以及一個只能基於可用性目的使用的次要複本。 它不能用於其他任何項目，例如可讀取的查詢。 [!INCLUDE[ssenterprise-md](../includes/ssenterprise-md.md)] 中的 AG 最多總共可有九個複本：一個主要複本，以及最多八個次要複本，其中最多三個複本 (包含主要複本) 是同步的。 如果使用基礎叢集，則在包含 Corosync 時，最多總共可有 16 個節點。 可用性群組在使用 [!INCLUDE[ssenterprise-md](../includes/ssenterprise-md.md)] 時最多可以跨越 16 個節點中的九個，而使用 [!INCLUDE[ssstandard-md](../includes/ssstandard-md.md)] 時則為兩個。

需要能夠自動容錯移轉至另一個複本的雙複本設定，需要使用僅設定的複本，如[僅設定的複本和仲裁](#configuration-only-replica-and-quorum)中所述。 僅設定的複本是在 [!INCLUDE[sssql17-md](../includes/sssql17-md.md)] 累積更新 1 (CU1) 中引進的，因此，應該是針對此設定所部署的最低版本。

如果使用 Pacemaker，則必須正確設定它，才能讓它保持運作。 這表示從 Pacemaker 觀點來看，除了任何 [!INCLUDE[ssnoversion-md](../includes/ssnoversion-md.md)] 需求 (例如僅設定的複本)，仲裁和 STONITH 都必須正確實作。

[!INCLUDE[ssenterprise-md](../includes/ssenterprise-md.md)] 僅支援可讀取的次要複本。

## <a name="cluster-type-and-failover-mode"></a>叢集類型和容錯移轉模式

[!INCLUDE[sssql17-md](../includes/sssql17-md.md)] 的新功能是引進適用於 AG 的叢集類型。 針對 Linux，有兩個有效值：「外部」和「無」。 「外部」叢集類型表示 Pacemaker 將在 AG 底下使用。 針對叢集類型使用「外部」，也需要將容錯移轉模式設為「外部」(也是 [!INCLUDE[sssql17-md](../includes/sssql17-md.md)] 的新功能)。 支援自動容錯移轉，但與 WSFC 不同的是，使用 Pacemaker 時，容錯移轉模式會設定為「外部」，而不是自動。 不同於 WSFC，AG 的 Pacemaker 部分會在設定 AG 之後建立。

「無」叢集類型表示 AG 不需要也不會使用 Pacemaker。 即使是在已設定 Pacemaker 的伺服器上，如果將 AG 的叢集類型設定為「無」，Pacemaker 將不會查看或管理該 AG。 「無」叢集類型只支援從主要到次要複本的手動容錯移轉。 以「無」建立的 AG 主要目標是讀取縮放案例以及升級。 雖然它可以在不需自動容錯移轉的災害復原或本機可用性這類案例中運作，但不建議這麼做。 接聽程式案例在沒有 Pacemaker 的情況下也會更為複雜。

叢集類型儲存於 [!INCLUDE[ssnoversion-md](../includes/ssnoversion-md.md)] 動態管理檢視 (DMV) `sys.availability_groups` 的資料行 `cluster_type` 和 `cluster_type_desc` 中。

## <a name="required_synchronized_secondaries_to_commit"></a>required\_synchronized\_secondaries\_to\_commit

[!INCLUDE[sssql17-md](../includes/sssql17-md.md)] 的新功能是 AG 所使用的設定，稱為 `required_synchronized_secondaries_to_commit`。 這會告知 AG 必須位於含主要複本之 Lockstep 中的次要複本數目。 這會啟用像是自動容錯移轉 (僅限於與叢集類型為「外部」的 Pacemaker 整合時) 的項目，並控制當次要複本的正確數目已上線或離線時，主要複本的可用性等項目的行為。 如需深入了解此運作方式，請參閱[可用性群組設定的高可用性和資料保護](sql-server-linux-availability-group-ha.md)。 預設會設定 `required_synchronized_secondaries_to_commit` 值，並由 Pacemaker/ [!INCLUDE[ssnoversion-md](../includes/ssnoversion-md.md)] 維護。 您可以手動覆寫此值。

`required_synchronized_secondaries_to_commit` 和新序號的組合 (儲存於`sys.availability_groups`) 會通知 Pacemaker 和 [!INCLUDE[ssnoversion-md](../includes/ssnoversion-md.md)]，例如自動容錯移轉可能發生。 在此情況下，次要複本的序號會與主要複本相同，這表示它具有所有最新的設定資訊。

有三個可針對 `required_synchronized_secondaries_to_commit` 設定的值：0、1 或 2。 它們會控制當複本變成無法使用時會發生什麼事的行為。 數字會對應至必須與主要複本同步的次要複本數目。 在 Linux 底下，其行為如下所示：

-   0：次要複本不需要處於與主要複本同步的狀態。 不過，如果次要複本未同步，就不會自動容錯移轉。 
-   1：一個次要複本必須處於與主要複本同步的狀態；可能會自動容錯移轉。 在次要同步複本可供使用之前，無法使用主要複本。
-   2：三個或更多節點之 AG 設定中的次要複本都必須與主要複本同步；可能會自動容錯移轉。

`required_synchronized_secondaries_to_commit` 不僅會控制具有同步複本的容錯移轉行為，還會控制資料遺失。 如果值為 1 或 2，就一定需要將次要複本同步，因此一律將會有資料冗餘。 這表示不會遺失資料。

若要變更 `required_synchronized_secondaries_to_commit` 的值，請使用下列語法：

>[!NOTE]
>變更值會導致資源重新啟動，這表示會產生短暫中斷。 避免這種情況的唯一方法就是將資源暫時設定為不由叢集管理。

**Red Hat Enterprise Linux (RHEL) 和 Ubuntu**

```bash
sudo pcs resource update <AGResourceName> required_synchronized_secondaries_to_commit=<Value>
```

**SUSE Linux Enterprise Server (SLES)**

```bash
sudo crm resource param ms-<AGResourceName> set required_synchronized_secondaries_to_commit <value>
```

其中 *AGResourceName* 是為 AG 設定的資源名稱，而「值」  為 0、1 或 2。 若要將它設回管理參數的 Pacemaker 預設值，請執行相同陳述式，但不含任何值。

符合下列條件時，可能會自動容錯移轉 AG：

-   主要和次要複本均設定為同步資料移動。
-   次要複本的狀態為已同步 (而非同步中)，表示這兩者處於相同的資料點。
-   叢集類型會設定為「外部」。 當叢集類型為「無」時，就無法自動容錯移轉。
-   要成為主要複本之次要複本的 `sequence_number` 會具有最高的序號；也就是，次要複本的 `sequence_number` 會與原始主要複本相符。

如果符合這些條件且裝載主要複本的伺服器失敗，AG 就會將擁有權變更為同步複本。 同步複本的行為 (總共可有三個：一個主要複本和兩個次要複本) 可由 `required_synchronized_secondaries_to_commit` 進一步控制。 這適用於 Windows 和 Linux 上的 AG，但設定方式完全不同。 在 Linux 上，此值是由 AG 資源本身的叢集自動設定的。

## <a name="configuration-only-replica-and-quorum"></a>僅設定的複本和仲裁

此外，從 CU1 開始，[!INCLUDE[sssql17-md](../includes/sssql17-md.md)] 的新功能是僅設定的複本。 由於 Pacemaker 與 WSFC 不同，特別是當它進入仲裁且需要 STONITH 時，在它進入 AG 時，只有雙節點的設定將無法運作。 針對 FCI，Pacemaker 所提供的仲裁機制均可正常運作，因為所有 FCI 容錯移轉仲裁都會在叢集層發生。 針對 AG，Linux 底下的仲裁會在 [!INCLUDE[ssnoversion-md](../includes/ssnoversion-md.md)] 中發生，所有中繼資料都儲存於其中。 這就是僅設定複本發揮作用的地方。

如果沒有任何其他項目，則需要第三個節點和至少一個同步的複本。 僅設定的複本會將 AG 設定儲存於 master 資料庫，與 AG 設定中的其他複本相同。 僅設定的複本沒有參與 AG 的使用者資料庫。 設定資料會從主要複本同步傳送。 此設定資料接著會在容錯移轉期間使用，而不論它們是自動或手動。

若要讓 AG 維護仲裁並啟用叢集類型為「外部」的自動容錯移轉，則必須：

-   擁有三個同步複本 (僅限 [!INCLUDE[ssenterprise-md](../includes/ssenterprise-md.md)])；或者
-   擁有兩個複本 (主要和次要) 以及一個僅設定的複本。

不論是否要針對 AG 設定使用「外部」或「無」叢集類型，都可能發生手動容錯移轉。 雖然僅設定的複本可以使用叢集類型「無」的 AG 來設定，但不建議這麼做，因為它會使部署複雜化。 針對那些設定，請手動修改 `required_synchronized_secondaries_to_commit`，使其值至少為 1，以便至少有一個已同步的複本。

僅設定的複本可裝載於任何版本的 [!INCLUDE[ssnoversion-md](../includes/ssnoversion-md.md)] 上，包括 [!INCLUDE[ssexpress-md](../includes/ssexpress-md.md)] 在內。 這會將授權成本降至最低，並確保它能與 [!INCLUDE[ssstandard-md](../includes/ssstandard-md.md)] 中的 AG 搭配使用。 這表示第三部必要的伺服器只需符合 [!INCLUDE[ssnoversion-md](../includes/ssnoversion-md.md)] 的最低規格，因為它不會接收 AG 的使用者交易流量。

使用僅設定的複本時，其行為如下：

-   預設會將 `required_synchronized_secondaries_to_commit` 設定為 0。 如有需要，可手動將此項修改為 1。
-   如果主要複本失敗且 `required_synchronized_secondaries_to_commit` 為 0，次要複本將變成新的主要複本且可供讀取和寫入。 如果值為 1，將會進行自動容錯移轉，但必須等到另一個複本上線之後，才會接受新交易。
-   如果次要複本失敗且 `required_synchronized_secondaries_to_commit` 為 0，則主要複本仍會接受交易，但如果此時主要節點失敗，可能就不會保護資料或容錯移轉 (手動或自動)，因為無法使用次要複本。
-   如果僅設定的複本失敗，AG 將會正常運作，但可能不會進行自動容錯移轉。
-   如果同步次要複本和僅設定的複本都失敗，主要複本就無法接受交易，而主要複本也不會失敗。

在 CU1 中，corosync.log 檔案的記錄中有一個透過 `mssql-server-ha` 產生的已知 Bug。 如果次要複本因為可用的必要複本數目而無法成為主要複本，則目前的訊息會顯示「預期收到序號 1，但只收到序號 2。 線上沒有足夠的複本，無法安全地升級本機複本。」 數字應該相反，而且應該顯示「預期收到序號 2，但只收到序號 1。 線上沒有足夠的複本，無法安全地升級本機複本。」 

## <a name="multiple-availability-groups"></a>多個可用性群組 

您可以針對每個 Pacemaker 叢集或伺服器組合建立多個 AG。 唯一的限制是系統資源。 主要複本會顯示 AG 擁有權。 不同的 AG 可由不同節點所擁有；它們不一定要在相同節點上執行。

## <a name="drive-and-folder-location-for-databases"></a>適用於資料庫的磁碟機和資料夾位置

在 Windows 型 AG 上，適用於參與 AG 之使用者資料庫的磁碟機和資料夾結構應完全相同。 例如，如果使用者資料庫位於伺服器 A 上的 `/var/opt/mssql/userdata` 中，該相同資料夾應該存在於伺服器 B 上。唯一的例外狀況會在[與 Windows 型可用性群組和複本的互通性](#interoperability-with-windows-based-availability-groups-and-replicas)一節中加以說明。

## <a name="the-listener-under-linux"></a>Linux 底下的接聽程式

接聽程式是 AG 的選擇性功能。 它會針對所有連線 (讀取/寫入到主要複本及/或唯讀到次要複本) 提供單一進入點，讓應用程式和終端使用者都不需要知道裝載該資料的是哪部伺服器。 在 WSFC 中，這是網路名稱資源與 IP 資源的組合，接著在 AD DS (如有需要) 和 DNS 中註冊此組合。 與 AG 資源本身結合時，它會提供該抽象概念。 如需接聽程式的詳細資訊，請參閱[接聽程式、用戶端連線能力及應用程式容錯移轉](../database-engine/availability-groups/windows/listeners-client-connectivity-application-failover.md)。

Linux 底下的接聽程式是以不同方式設定的，但其功能相同。 Pacemaker 中沒有網路名稱資源的概念，也沒有在 AD DS 中建立的物件；只有在 Pacemaker 中建立的 IP 位址資源可以在任何節點上執行。 您必須建立與 DNS 中具有「易記名稱」之接聽程式的 IP 資源相關聯的項目。 適用於接聽程式的 IP 資源將只會在裝載該可用性群組之主要複本的伺服器上生效。

如果使用 Pacemaker 且已建立與接聽程式相關聯的 IP 位址資源，則將發生短暫中斷，因為該伺服器上的 IP 位址會停止並在另一部伺服器上啟動，而不論是自動或手動容錯移轉。 雖然這會透過單一名稱和 IP 位址的組合來提供抽象概念，但它不會為中斷設定遮罩。 應用程式必須能夠使用某種功能來偵測此情況並重新連線，才能處理中斷連線。

不過，DNS 名稱和 IP 位址的組合仍不足以提供 WSFC 上接聽程式所提供的所有功能，例如次要複本的唯讀路由。 設定 AG 時，仍然必須在 [!INCLUDE[ssnoversion-md](../includes/ssnoversion-md.md)] 中設定「接聽程式」。 這可在精靈以及 Transact-SQL 語法中看到。 有兩種方式可將此項設定為與 Windows 上的功能相同：

-   對於叢集類型為「外部」的 AG，與在 [!INCLUDE[ssnoversion-md](../includes/ssnoversion-md.md)] 中建立之「接聽程式」相關聯的 IP 位址應該是在 Pacemaker 中建立之資源的 IP 位址。
-   針對以叢集類型「無」建立的 AG，請使用與主要複本相關聯的 IP 位址。

與所提供 IP 位址相關聯的執行個體接著會成為像是來自應用程式之唯讀路由要求的項目協調器。

## <a name="interoperability-with-windows-based-availability-groups-and-replicas"></a>與 Windows 型可用性群組和複本的互通性 

具有「外部」」叢集類型的 AG 或為 WSFC 的AG，其複本不能跨平台。 無論 AG 是 [!INCLUDE[ssstandard-md](../includes/ssstandard-md.md)] 或 [!INCLUDE[ssenterprise-md](../includes/ssenterprise-md.md)]，都是如此。 這表示在具有基礎叢集的傳統 AG 設定中，一個複本無法位於 WSFC 上，另一個位於含 Pacemaker 的 Linux 上。

叢集類型「無」的 AG 可以讓其複本跨越 OS 界限，因此，相同 AG 中可能同時有 Linux 型和 Windows 型的複本。 這裡顯示一個範例，其中主要複本是 Windows 型，而次要複本位於其中一個 Linux 發行版本上。

![混合式 NONE](./media/sql-server-linux-availability-group-overview/image1.png)

分散式 AG 也可以跨越 OS 界限。 基礎 AG 會由適用於其設定方式的規則所繫結，例如，已設定為「外部」的是僅限 Linux，但已聯結的 AG 可以使用 WSFC 進行設定。 請考慮下列範例：

![混合式 Dist AG](./media/sql-server-linux-availability-group-overview/image2.png)

<!-- Distributed AGs are also supported for upgrades from [!INCLUDE[sssql15-md](../includes/sssql16-md.md)] to [!INCLUDE[sssql17-md](../includes/sssql17-md.md)]. For more information on how to achieve this, see [the article "x"].

If using automatic seeding with a distributed availability group that crosses OSes, it can handle the differences in folder structure. How this works is described in [the documentation for automatic seeding].
-->
 
## <a name="next-steps"></a>後續步驟
[在 Linux 上設定 SQL Server 的可用性群組](sql-server-linux-availability-group-configure-ha.md)

[在 Linux 上設定 SQL Server 的讀取級別可用性群組](sql-server-linux-availability-group-configure-rs.md)

[在 RHEL 上新增可用性群組叢集資源](sql-server-linux-availability-group-cluster-rhel.md)

[在 SLES 上新增可用性群組叢集資源](sql-server-linux-availability-group-cluster-sles.md)

[在 Ubuntu 上新增可用性群組叢集資源](sql-server-linux-availability-group-cluster-ubuntu.md)

[設定跨平台可用性群組](sql-server-linux-availability-group-cross-platform.md)

