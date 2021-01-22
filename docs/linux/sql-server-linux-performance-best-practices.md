---
title: Linux 上的 SQL Server 的效能最佳作法
description: 本文提供在 Linux 上執行 SQL Server 的效能最佳做法與指引。
author: tejasaks
ms.author: tejasaks
ms.reviewer: vanto
ms.date: 01/19/2021
ms.topic: conceptual
ms.prod: sql
ms.technology: linux
ms.openlocfilehash: 9a73013e7d49523f8aba418a2961336998190fc5
ms.sourcegitcommit: 713e5a709e45711e18dae1e5ffc190c7918d52e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98689102"
---
# <a name="performance-best-practices-and-configuration-guidelines-for-sql-server-on-linux"></a>Linux 上 SQL Server 的效能最佳作法和設定方針

[!INCLUDE [SQL Server - Linux](../includes/applies-to-version/sql-linux.md)]

此文章提供最佳作法和建議，以最大化連線至 Linux 上的 SQL Server 之資料庫應用程式的效能。 這些建議僅適用於在 Linux 平台上執行。 所有一般 SQL Server 建議 (例如索引設計) 仍然適用。

下列指引包含設定 SQL Server 與 Linux 作業系統 (OS) 的建議。

## <a name="linux-os-configuration"></a>Linux OS 設定

請考慮使用下列 Linux OS 組態設定，以在安裝 SQL Server 時獲得最佳效能體驗。

### <a name="storage-configuration-recommendation"></a>儲存體組態建議

#### <a name="use-storage-subsystem-with-appropriate-iops-throughput-and-redundancy"></a>使用具有適當 IOPS、輸送量以及備援的儲存體子系統

裝載資料、交易記錄以及其他相關檔案 (例如記憶體內部 OLTP 的檢查點檔案) 的儲存體子系統，必須能夠妥善管理平均及尖峰工作負載。 一般而言，在內部部署環境中，儲存體廠商支援多個磁碟上分割的適當硬體 RAID組態，以確保適當的 IOPS、輸送量以及備援。 不過，因為不同儲存體廠商與不同儲存體供應項目之間存在各種架構，所以實際情況可能有所不同。

針對 Azure 虛擬機器上部署的 Linux SQL Server，請考慮使用軟體 RAID，以確保能達到適當的 IOPS 與輸送量需求。 如在 Azure 虛擬機器上設定 SQL Server 時，需要類似的儲存體考量，請參閱下列文章：[SQL Server VM 的儲存體組態](/azure/azure-sql/virtual-machines/windows/storage-configuration)

以下範例說明如何在 Azure 虛擬機器上的 Linux 中建立軟體 RAID。 以下提供範例，但仍應該根據資料、交易記錄以及 tempdb IO 需求，使用適當的資料磁碟數目來取得磁碟區所需輸送量與 IOPS。 在此範例中，連結到 Azure 虛擬機器的資料磁碟有八個; 其中 4 個用於裝載資料檔案、2 個用於交易記錄，最後 2 個則用於 tempdb 工作負載。

```bash
# To locate the devices (for example /dev/sdc) for RAID creation, use the lsblk command
# For Data volume, using 4 devices, in RAID 5 configuration with 8KB stripes
mdadm --create --verbose /dev/md0 --level=raid5 --chunk=8K --raid-devices=4 /dev/sdc /dev/sdd /dev/sde /dev/sdf

# For Log volume, using 2 devices in RAID 10 configuration with 64KB stripes
mdadm --create --verbose /dev/md1 --level=raid10 --chunk=64K --raid-devices=2 /dev/sdg /dev/sdh

# For tempdb volume, using 2 devices in RAID 0 configuration with 64KB stripes
mdadm --create --verbose /dev/md2 --level=raid0 --chunk=64K --raid-devices=2 /dev/sdi /dev/sdj
```

#### <a name="disk-partitioning-and-configuration-recommendations"></a>磁碟分割和設定建議

針對 SQL Server，建議使用 RAID 設定。 已部署的檔案系統 stripe 單位 (sunit) 和 stripe 寬度應符合 RAID 幾何。 以下是記錄磁片區的 XFS 檔案系統型範例。 

```bash
# Creating a log volume, using 6 devices, in RAID 10 configuration with 64KB stripes
mdadm --create --verbose /dev/md3 --level=raid10 --chunk=64K --raid-devices=6 /dev/sda /dev/sdb /dev/sdc /dev/sdd /dev/sde /dev/sdf

mkfs.xfs /dev/sda1 -f -L log 
meta-data=/dev/sda1              isize=512    agcount=32, agsize=18287648 blks 
         =                       sectsz=4096  attr=2, projid32bit=1 
         =                       crc=1        finobt=1, sparse=1, rmapbt=0 
         =                       reflink=1 
data     =                       bsize=4096   blocks=585204384, imaxpct=5 
         =                       sunit=16     swidth=48 blks 
naming   =version 2              bsize=4096   ascii-ci=0, ftype=1 
log      =internal log           bsize=4096   blocks=285744, version=2 
         =                       sectsz=4096  sunit=1 blks, lazy-count=1 
realtime =none                   extsz=4096   blocks=0, rtextents=0 
```

記錄陣列是具有 64k stripe 的6磁片磁碟機 RAID 10。 如您所見：
   1. "Sunit = 16 blks"，16 * 4096 blk size = 64k，符合 stripe 大小。 
   2. "Swidth = 48 blks"、swidth/sunit = 3，這是陣列中的資料磁片磁碟機數目，不包括同位磁片磁碟機。 

#### <a name="file-system-configuration-recommendation"></a>檔案系統組態建議

SQL Server 支援 EXT4 與 XFS 檔案系統來裝載資料庫、交易記錄及其他檔案 (例如 SQL Server 中記憶體內部 OLTP 的檢查點檔案)。 Microsoft 建議使用 XFS 檔案系統來裝載 SQL Server 的資料與交易記錄檔。

```bash
# Formatting the volume with XFS filesystem
mkfs.xfs /dev/md0 -f -L datavolume
mkfs.xfs /dev/md1 -f -L logvolume
mkfs.xfs /dev/md2 -f -L tempdb
```

> [!NOTE]
> 您可在建立 XFS 磁碟區並加以格式化時，將 XFS 檔案系統設定為不區分大小寫。 這不是 Linux 生態系統中的常用組態，但有鑑於相容性問題仍建議使用。
>
> 範例：mkfs.xfs /dev/md0 -f -n version=ci -L datavolume
>
> 在範例中，參數 `-n version=ci` 用來將 XFS 檔案系統設定為不區分大小寫。

##### <a name="persistent-memory-filesystem-recommendation"></a>持續性記憶體檔案系統建議

針對持續性記憶體裝置上的檔案系統組態，基礎檔案系統的區塊配置應為 2 MB。 如需本主題的詳細資訊，請參閱[技術考量](sql-server-linux-configure-pmem.md#technical-considerations)一文。

#### <a name="disable-last-accessed-datetime-on-file-systems-for-sql-server-data-and-log-files"></a>針對 SQL Server 資料和記錄檔，在檔案系統上停用上次存取日期/時間

為確保連結到系統的磁碟機會在重新開機後自動重新掛接，必須將磁碟機新增至 `/etc/fstab` 檔案。 此外，強烈建議在 `/etc/fstab` 中使用 UUID (通用唯一識別碼) 來參考磁碟機，而非僅使用裝置名稱 (例如 `/dev/sdc1`)。

同時，也建議將 **noatime** 屬性與用來儲存 SQL Server 資料與記錄檔的檔案系統搭配使用。 請參閱您的 Linux 文件，以了解如何設定此屬性。 以下示範為掛接於 Azure 虛擬機器中的磁碟區啟用 **noatime** 選項。

**_/etc/fstab_* _ 中的掛接點項目

```bash
UUID="xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" /data1 xfs,rw,attr2,noatime 0 0
```

在上述範例中，UUID 代表可使用 _*_blkid_*_ 命令找到的裝置。

#### <a name="sql-server-and-forced-unit-access-fua-io-subsystem-capability"></a>SQL Server 與強制單位存取 (FUA) I/O 子系統功能

某些受支援的特定版本 Linux 發行版，可支援 FUA I/O 子系統功能，以提供資料持久性。 SQL Server 使用 FUA 功能為 SQL Server 工作負載提供高效率且可靠的 I/O。 如需 Linux 發行版對 FUA 的支援，以及其對 SQL Server 的影響等詳細資訊，請參閱下列部落格：[SQL Server On Linux:Forced Unit Access (FUA) Internals](https://bobsql.com/sql-server-on-linux-forced-unit-access-fua-internals/) (強制單位存取 (FUA) 內部)

從 SUSE Linux Enterprise Server 12 SP5 與 Red Hat Enterprise Linux 8.0 開始支援 I/O 子系統中的 FUA 功能。 如果正在使用 SQL Server 2017 CU6 和更新版本，或 SQL Server 2019，您應該使用下列組態，透過 SQL Server 使用 FUA 獲得高效能且有效率的 I/O 實作。

如果符合下列條件，請使用下方所列出的建議組態。

- 使用 SQL Server 2017 CU6 或更新版本，或 SQL Server 2019
- 使用支援 FUA 功能 (Red Hat Enterprise Linux 8.0 或更新版本，或 SUSE Linux Enterprise Server 12 SP5) 的 Linux 發行版
- 在儲存體子系統上，和/或支援 FUA 功能並已針對其加以設定的硬體

建議的設定：

1. 啟用追蹤旗標 3979 作為啟動參數
2. 使用 _ *mssql-conf** 來設定 `control.writethrough = 1` 和 `control.alternatewritethrough = 0`

針對大部分不符合上述條件的其他組態，建議的組態如下：

1. 啟用追蹤旗標 3982 作為啟動參數 (這是 Linux 生態系統中 SQL Server 的預設值)，同時確定不要啟用追蹤旗標 3979 作為啟動參數
2. 使用 **mssql-conf** 來設定 `control.writethrough = 1` 和 `control.alternatewritethrough = 1`

### <a name="kernel-and-cpu-settings-for-high-performance"></a>高效能的核心及 CPU 設定

下列章節會描述建議的 Linux OS 設定，以在安裝 SQL Server 時獲得高效能和輸送量。 如需設定這些設定的程序，請參閱 Linux OS 文件。 使用 [**_Tuned_* _](https://tuned-project.org) 可協助設定許多 CPU 與核心組態，如下所述。

#### <a name="using-__tuned__-to-configure-kernel-settings"></a>使用 _*_Tuned_*_ 來進行核心設定

針對 Red Hat Enterprise Linux (RHEL) 使用者，[Tuned](https://tuned-project.org) 輸送量-效能設定檔會自動進行核心與 CPU 設定 (C-States 除外)。 從 RHEL 8.0 開始，名為 _ *mssql** 的 _*_Tuned_*_ 設定檔即與 Red Hat 共同開發，並針對 SQL Server 工作負載提供更佳的 Linux 效能調整。 此設定檔包含 RHEL 輸送量-效能設定檔，我們會在下方提供其定義，以供您檢閱其他 Linux 發行版本與 RHEL 版本，而不需要此設定檔。

針對 SUSE Linux Enterprise Server 12 SP5、Ubuntu 18.04 以及 Red Hat Enterprise Linux 7.x，可手動安裝 **_Tuned_ *_ 套件。此套件可用來建立及設定 _* mssql** 設定檔，如下所述。

##### <a name="proposed-linux-settings-using-a-tuned-mssql-profile"></a>使用 Tuned mssql 設定檔的 Linux 建議設定

```bash
#
# A Tuned configuration for SQL Server on Linux
#

[main]
summary=Optimize for Microsoft SQL Server
include=throughput-performance

[cpu]
force_latency=5

[sysctl]
vm.swappiness = 1
vm.dirty_background_ratio = 3
vm.dirty_ratio = 80
vm.dirty_expire_centisecs = 500
vm.dirty_writeback_centisecs = 100
vm.transparent_hugepages=always
# For multi-instance SQL deployments, use
# vm.transparent_hugepages=madvise
vm.max_map_count=1600000
net.core.rmem_default = 262144
net.core.rmem_max = 4194304
net.core.wmem_default = 262144
net.core.wmem_max = 1048576
kernel.numa_balancing=0
kernel.sched_latency_ns = 60000000
kernel.sched_migration_cost_ns = 500000
kernel.sched_min_granularity_ns = 15000000
kernel.sched_wakeup_granularity_ns = 2000000
```

若要啟用此 Tuned 設定檔，請將這些定義儲存於 `/usr/lib/tuned/mssql` 資料夾下的 **tuned.conf** 檔案，並使用下列命令啟用該設定檔：

```bash
chmod +x /usr/lib/tuned/mssql/tuned.conf
tuned-adm profile mssql
```

使用下列命令來確認是否已啟用：

```bash
tuned-adm active
```

或

```bash
tuned-adm list
```

#### <a name="cpu-settings-recommendation"></a>CPU 設定建議

下表提供 CPU 設定的建議：

| 設定 | 值 | 詳細資訊 |
|---|---|---|
| CPU 頻率管理員 | 效能 | 請參閱 **cpupower** 命令 |
| ENERGY_PERF_BIAS | 效能 | 請參閱 **x86_energy_perf_policy** 命令 |
| min_perf_pct | 100 | 請參閱 intel p-state 相關文件 |
| C-States | 僅限 C1 | 請參閱您的 Linux 或系統文件，以了解如何確保 C-States 設定為 [僅限 C1] |

如先前所述，使用 **_Tuned_ *_ 會自動適當地設定 CPU 頻率管理員、ENERGY_PERF_BIAS 以及 min_perf_pct 設定，因為其將輸送量效能設定檔用作 _* mssql** 設定檔的基礎。 C-States 參數必須根據 Linux 或系統經銷商所提供的文件，以手動方式設定。

#### <a name="disk-settings-recommendations"></a>磁碟設定建議

下表提供磁碟設定的建議：

| 設定 | 值 | 詳細資訊 |
|---|---|---|
| 磁碟 `readahead` | 4096 | 請參閱 `blockdev` 命令 |
| sysctl 設定 | kernel.sched_min_granularity_ns = 10000000<br/>kernel.sched_wakeup_granularity_ns = 15000000<br/>vm.dirty_ratio = 40<br/>vm.dirty_background_ratio = 10<br/>vm.swappiness = 1 | 請參閱 **sysctl** 命令 |

**描述：**

- **swappiness**：此參數會控制指定為交換執行時間進程記憶體的相對權數，相較于 filesystem 快取。 這個參數的預設值是60，這表示相較于移除檔案系統快取頁面的比率為60:140，這表示交換執行時間進程記憶體頁面。 設定值1表示在實體記憶體中保留執行時間進程記憶體的強式喜好設定，以 filesystem 快取的費用來保存。 由於 SQL Server 使用緩衝集區作為資料頁面快取，且強烈偏好寫入至實體硬體，以避免可靠復原的 filesystem 快取，因此積極的 swappiness 設定對於高效能且專用的 SQL Server 而言很有説明。
您可以在檔中找到 [/proc/sys/vm/的其他資訊-#swappiness](https://www.kernel.org/doc/html/latest/admin-guide/sysctl/vm.html#swappiness)

- **vm.dirty_\** _：未快取 SQL Server 檔案寫入存取，其滿足其資料完整性需求。 這些參數能夠實現高度的非同步寫入效能，並在節流排清時允許足夠大的快取，以降低儲存體 IO 對 Linux 快取寫入的影響。

- _*kernel.sched_\**_：這些參數值代表在 Linux 核心中調整完全公平排程 (CFS) 演算法的目前建議，用於改善在處理序之間，執行緒先佔及繼續執行方面的網路及儲存體 IO 呼叫輸送量。

使用 _*mssql** **_Tuned_*_ 設定檔來設定 _*vm.swappiness**、**vm.dirty_\* *_ 以及 _* kernel.sched_\**_ 設定。 使用 `blockdev` 命令的磁碟 `readahead` 組態是以裝置為主，且必須手動執行。

#### <a name="kernel-setting-auto-numa-balancing-for-multi-node-numa-systems"></a>多節點 NUMA 系統的核心設定自動 NUMA 平衡

如果將 SQL Server 安裝在多節點的 _ *NUMA** 系統上，則預設會啟用下列 **kernel.numa_balancing** 核心設定。 若要允許 SQL Server 在 **NUMA** 系統上以最高效率運作，請在多節點 NUMA 系統上停用自動 NUMA 平衡：

```bash
sysctl -w kernel.numa_balancing=0
```

使用 **mssql** **_Tuned_ *_ 設定檔來設定 _* kernel.numa_balancing** 選項。

#### <a name="kernel-settings-for-virtual-address-space"></a>虛擬位址空間的核心設定

**vm.max_map_count** 的預設設定 (也就是 65536) 可能不足以安裝 SQL Server。 基於這個理由，請將 SQL Server 部署的 **vm.max_map_count** 值變更為 262144 以上，並參閱 [使用 Tuned mssql 設定檔的 Linux 建議設定](#proposed-linux-settings-using-a-tuned-mssql-profile)一節，以進一步微調這些核心參數。 vm.max_map_count 的最大值為 2147483647。

```bash
sysctl -w vm.max_map_count=1600000
```

使用 **mssql** **_Tuned_ *_ 設定檔來設定 _* vm.max_map_count** 選項。

#### <a name="leave-transparent-huge-pages-thp-enabled"></a>讓透明大頁 (THP) 保持啟用

大部分的 Linux 安裝預設都應該為開啟此選項。 為了提供最一致的效能體驗，我們建議您讓此設定選項維持啟用狀態。 不過，若在具有多個執行個體的 SQL Server 部署中進行高記憶體分頁活動，或 SQL Server 與伺服器上其他大量占用記憶體的應用程式同時執行等情況下，則建議在執行下列命令之後，測試應用程式效能：

```bash
echo madvise > /sys/kernel/mm/transparent_hugepage/enabled
```

或使用此行來修改 **mssql** **_Tuned_* _ 設定檔：

```bash
vm.transparent_hugepages=madvise
```

接著，讓 _ *mssql** 設定檔在修改之後生效：

```bash
tuned-adm off
tuned-adm profile mssql
```

使用 **mssql** **_Tuned_ *_ 設定檔來設定 _* transparent_hugepage** 選項。

#### <a name="network-setting-recommendations"></a>網路設定建議

如同有儲存體和 CPU 的建議，以下列出的網路特定建議也可供參考。 並非以下提及的所有設定都可在不同的 Nic 上使用。 如需每個選項的指引，請參閱並諮詢 NIC 廠商。 在開發環境中測試及設定此專案，然後將其套用至生產環境。 以下所述的選項會以範例說明，而使用的命令則是 NIC 類型和廠商特有的。 

1. 設定網路埠緩衝區大小：在下列範例中，NIC 的名稱為 ' eth0 '，這是 Intel 架構的 NIC。 針對 Intel NIC，建議的緩衝區大小為 4 KB (4096) 。 確認預先設定的上限，然後使用如下所示的範例命令加以設定：

 ```bash
         #To check the pre-set maximums please run the command, example NIC name used here is:"eth0"
         ethtool -g eth0
         #command to set both the rx(recieve) and tx (transmit) buffer size to 4 KB. 
         ethtool -G eth0 rx 4096 tx 4096
         #command to check the value is properly configured is:
         ethtool -g eth0
  ```

2. 啟用巨型幀：啟用大型框架之前，請確認在用戶端與 SQL server 之間的網路封包路徑中，所有網路交換器 (es) 、路由器和任何其他必要的畫面都支援巨型個框架。 只有如此一來，啟用巨型幀可以改善效能。 啟用巨型框架之後，請連線至 SQL Server 並使用來將網路封包大小變更為8060， `sp_configure` 如下所示：

```bash
         #command to set jumbo frame to 9014 for a Intel NIC named eth0 is
         ifconfig eth0 mtu 9014
         #verify the setting using the command:
         ip addr | grep 9014
```

```sql
         sp_configure 'network packet size' , '8060'
         go
         reconfigure with override
         go
```

3. 根據預設，我們建議您設定自動調整 RX/TX IRQ 聯合的埠，這表示當封包速率很低時，系統會調整中斷傳遞來改善延遲，並在封包速率很高時提高輸送量。 請注意，這項設定可能不適用於所有不同的網路基礎結構，因此請檢查現有的網路基礎結構，並確認是否支援此功能。 以下範例適用于名為 ' eth0 ' 的 NIC，也就是 intel NIC：

```bash
         #command to set the port for adaptive RX/TX IRQ coalescing
         echtool -C eth0 adaptive-rx on
         echtool -C eth0 adaptive-tx on
         #confirm the setting using the command:
         ethtool -c eth0
```

> [!NOTE]
> 針對高效能環境的可預測行為（例如要進行效能評定的環境），請停用調適型 RX/TX IRQ 聯合，然後特別設定 RX/TX 中斷聯合。 請參閱範例命令以停用 RX/TX IRQ 聯合，然後特別設定這些值：

```bash
         #commands to disable adaptive RX/TX IRQ coalescing
         echtool -C eth0 adaptive-rx off
         echtool -C eth0 adaptive-tx off
         #confirm the setting using the command:
         ethtool -c eth0
         #Let us set the rx-usecs parameter which specify how many microseconds after at least 1 packet is received before generating an interrupt, and the [irq] parameters are the corresponding delays in updating the #status when the interrupt is disabled. For Intel bases NICs below are good values to start with:
         ethtool -C eth0 rx-usecs 100 tx-frames-irq 512
         #confirm the setting using the command:
         ethtool -c eth0
```

4. 我們也建議使用 RSS (接收端調整) 啟用，並且預設會結合 RSS 佇列的 rx 和 tx。 在某些特定案例中使用 Microsoft 支援服務時，停用 RSS 也改善了效能。 在測試環境中測試此設定，然後將其套用至生產環境。 以下顯示的範例命令適用于 Intel Nic。

```bash
         #command to get pre-set maximums
         ethtool -l eth0 
         #note the pre-set "Combined" maximum value. let's consider for this example, it is 8.
         #command to combine the queues with the value reported in the pre-set "Combined" maximum value:
         ethtool -L eth0 combined 8
         #you can verify the setting using the command below
         ethtool -l eth0
```

5. 使用 NIC 埠 IRQ 親和性。 若要藉由調整 IRQ 親和性來達到預期的效能，請考慮一些重要的參數，例如 Linux 處理伺服器拓撲、NIC 驅動程式堆疊、預設設定和 irqbalance 設定。 您可以透過伺服器拓撲的知識、停用 irqbalance，以及使用 NIC 廠商專屬的設定，來優化 NIC 埠 IRQ 的親和性設定。 以下是 Mellanox 特定網路基礎結構的範例，可協助說明設定。 請注意，這些命令會根據環境而變更。 如需進一步指引，請洽詢 NIC 廠商：

```bash
         #disable irqbalance or get a snapshot of the IRQ settings and force the daemon to exit
         systemctl disable irqbalance.service
         #or
         irqbalance --oneshot

         #download the Mellanox mlnx_tuning_scripts tarball, https://www.mellanox.com/sites/default/files/downloads/tools/mlnx_tuning_scripts.tar.gz and extract it
         tar -xvf mlnx_tuning_scripts.tar.gz
         # be sure, common_irq_affinity.sh is executable. if not, 
         # chmod +x common_irq_affinity.sh       

         #display IRQ affinity for Mellanox NIC port; e.g eth0
         ./show_irq_affinity.sh eth0

         #optimize for best throughput performance
         ./mlnx_tune -p HIGH_THROUGHPUT

         #set hardware affinity to the NUMA node hosting physically the NIC and its port
         ./set_irq_affinity_bynode.sh `\cat /sys/class/net/eth0/device/numa_node` eth0

         #verify IRQ affinity
         ./show_irq_affinity.sh eth0

         #add IRQ coalescing optimizations
         ethtool -C eth0 adaptive-rx off
         ethtool -C eth0 adaptive-tx off
         ethtool -C eth0  rx-usecs 750 tx-frames-irq 2048

         #verify the settings
         ethtool -c eth0
```

6. 完成上述變更之後，請使用下列命令確認 NIC 的速度以確定其符合預期：

```bash
         ethtool eth0 | grep -i Speed
```

#### <a name="additional-advanced-kernelos-configuration"></a>其他進階 Kernel/OS 組態

1. 為了達到最佳的儲存體 IO 效能，建議使用適用於區塊裝置的 Linux 多重佇列排程。 這可讓區塊層效能透過快速的固態硬碟 (SSD) 與多核心系統得到提升。 如果此設定在 Linux 發行版中預設為啟用，請參閱文件。 儘管在大部分的情況下，會使用 **scsi_mod.use_blk_mq=y** 將核心開機來加以啟用，但使用中 Linux 發行版中的文件可能會收錄額外指引。 這與上游的 Linux 核心一致。

1. 當多重路徑 IO 經常用於 SQL Server 部署時，裝置對應程式 (DM) 多重路徑目標也應啟用 **dm_mod.use_blk_mq=y** 核心開機選項來設定為使用 `blk-mq` 基礎結構。 預設值為 `n` (已停用)。 當基礎 SCSI 裝置正在使用 `blk-mq` 時，此設定可減少位於 DM 層的鎖定負荷。 如需有關如何設定的其他指引，請參閱使用 Linux 發行版文件。

#### <a name="configure-swapfile"></a>設定交換檔

請確定您已正確設定交換檔，以避免發生記憶體不足的問題。 請參閱您的 Linux 文件，以了解如何建立和適當地調整交換檔大小。

#### <a name="virtual-machines-and-dynamic-memory"></a>虛擬機器和動態記憶體

如果正在虛擬機器的 Linux 上執行 SQL Server，請務必選取選項來修正保留給虛擬機器的記憶體數量。 請勿使用 Hyper-V 動態記憶體這類功能。

## <a name="sql-server-configuration"></a>SQL Server 設定

建議您在安裝 Linux 上的 SQL Server 之後執行下列設定工作，以達到應用程式的最佳效能。

### <a name="best-practices"></a>最佳作法

- **針對節點和/或 CPU 使用處理程序親和性**

   建議使用 `ALTER SERVER CONFIGURATION` 為 Linux OS 上用於 SQL Server (通常用於所有 NODE 和 CPU) 的所有 **NUMANODE** 和/或 CPU 設定 `PROCESS AFFINITY`。 處理程序親和性有助於維護有效率的 Linux 和 SQL 排程行為。 使用 **NUMANODE** 選項是最簡單的方法。 即使電腦上只有一個 NUMA 節點，仍應使用 **處理序親和性**。 如需如何設定 **處理序親和性** 的詳細資訊，請參閱 [ALTER SERVER CONFIGURATION](../t-sql/statements/alter-server-configuration-transact-sql.md) 一文。

- **設定多個 tempdb 資料檔案**

   由於 Linux 上的 SQL Server 安裝未提供設定多個 tempdb 檔案的選項，因此建議您在安裝之後，考慮建立多個 tempdb 資料檔案。 如需詳細資訊，請參閱文章中的指導方針[避免 SQL Server tempdb 資料庫中配置爭用的建議](https://support.microsoft.com/help/2154845/recommendations-to-reduce-allocation-contention-in-sql-server-tempdb-d) \(機器翻譯\)。

### <a name="advanced-configuration"></a>進階設定

下列建議是選擇性的組態設定，您可以選擇在安裝 Linux 上的 SQL Server 之後執行。 這些選擇是根據工作負載和 Linux OS 設定的需求而定。

- **使用 mssql-conf 設定記憶體限制**

   為了確保 Linux OS 有足夠的可用實體記憶體，SQL Server 處理序預設只會使用 80% 的實體 RAM。 對於某些具有大量實體 RAM 的系統，20% 可能是很大的數字。 例如，在具有 1 TB RAM 的系統上，預設設定會保留大約 200 GB 的 RAM (未使用)。 在此情況下，您可能會想要將記憶體限制設定為較高的值。 請參閱有關 **mssql-conf** 工具的文件，以及控制 SQL Server 可見記憶體的 [memory.memorylimitmb](sql-server-linux-configure-mssql-conf.md#memorylimit) 設定 (以 MB 為單位)。

   變更此設定時，請小心不要將這個值設得太高。 如果沒有保留足夠的記憶體，則可能會遇到 Linux OS 和其他 Linux 應用程式的問題。

## <a name="next-steps"></a>下一步

若要深入了解可改善效能的 SQL Server 功能，請參閱[開始使用效能功能](sql-server-linux-performance-get-started.md)。

如需 Linux 上的 SQL Server 詳細資訊，請參閱 [Linux 上的 SQL Server 概觀](sql-server-linux-overview.md)。