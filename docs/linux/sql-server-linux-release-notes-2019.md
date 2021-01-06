---
title: Linux 上 SQL Server 2019 的版本資訊
description: 此文章包含在 Linux 上執行之 SQL Server 2019 的版本資訊與支援功能。 其中包含最新版本和數個先前版本的版本資訊。
author: VanMSFT
ms.author: vanto
ms.date: 10/01/2020
ms.topic: conceptual
ms.prod: sql
ms.technology: linux
ms.openlocfilehash: 262e2f222e6c30f3657e78c2a20862912b1aa970
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97643942"
---
# <a name="release-notes-for-sql-server-2019-on-linux"></a>Linux 上 SQL Server 2019 的版本資訊

[!INCLUDE[tsql-appliesto-ssver15-xxxx-xxxx-xxx-linuxonly.md](../includes/tsql-appliesto-ssver15-xxxx-xxxx-xxx-linuxonly.md)]

下列版本資訊適用於在 Linux 上執行的 SQL Server 2019。 此文章已針對每個版本細分為數個小節。 每個版本都具有描述 CU 變更的支援文章連結，以及能下載 Linux 套件的連結。

> [!TIP]
> 若要了解 SQL Server 2019 中的新 Linux 功能，請參閱 [SQL Server 2019 中的新功能](../sql-server/what-s-new-in-sql-server-ver15.md?view=sql-server-ver15&preserve-view=true#sql-server-on-linux)。

[!INCLUDE [linux-supported-platfoms-2019](../includes/linux-supported-platfoms-2019.md)]

## <a name="tools"></a>工具

以 SQL Server 為目標的大部分現有用戶端工具，都可以順暢地以在 Linux 上執行的 SQL Server 為目標。 某些工具可能需要特定版本，才能與 Linux 良好搭配運作。 如需 SQL Server 工具的完整清單，請參閱[適用於 SQL Server 的 SQL 工具和公用程式](../tools/overview-sql-tools.md)。

## <a name="release-history"></a>版本歷程記錄

下表列出 SQL Server 2019 版本的版本歷程記錄。

| 版本                   | 版本       | 發行日期 |
|---------------------------|---------------|--------------|
| [CU8](#cu8)               | 15.0.4073.23  | 2020-10-07   |
| [CU7 (已移除)](https://support.microsoft.com/help/4570012)     | 15.0.4063.15  | 2020-09-02   |
| [CU6](#cu6)               | 15.0.4053.23  | 2020-08-04   |
| [CU5](#cu5)               | 15.0.4043.16  | 2020-06-22   |
| [CU4](#cu4)               | 15.0.4033.1   | 2020-03-31   |
| [CU3](#cu3)               | 15.0.4023.6   | 2020-03-12   |
| [CU2](#cu2)               | 15.0.4013.40  | 2020-02-13   |
| [CU1](#cu1)               | 15.0.4003.23  | 2020-01-07   |
| [GA](#ga)                 | 15.0.2000.5   | 2019-11-04   |
| [候選版](#rc)  | 15.0.1900.25  | 2019-08-21   |

## <a name="how-to-install-updates"></a><a id="cuinstall"></a> 如何安裝更新

若您已設定 CU 存放庫 (mssql-server-2019)，則將會在執行新安裝時取得 SQL Server 套件的最新 CU。 如果您需要 Docker 容器映像，請參閱[Linux 上適用於 Docker 引擎的 Microsoft SQL Server](https://hub.docker.com/r/microsoft/mssql-server/) 官方映像。 如需有關存放庫設定的詳細資訊，請參閱[針對 Linux 上的 SQL Server 設定存放庫](sql-server-linux-change-repo.md)。

如果您正在更新現有的 SQL Server 套件，請針對每個套件執行適當的更新命令以取得最新的 CU。 如需每個套件的特定更新指示，請參閱下列安裝指南：

- [安裝 SQL Server 套件](sql-server-linux-setup.md#upgrade)
- [安裝全文檢索搜尋套件](sql-server-linux-setup-full-text-search.md)
- [安裝 SQL Server Integration Services](sql-server-linux-setup-ssis.md)
- [在 Linux 上安裝 SQL Server 2019 機器學習服務 R 和 Python 支援](sql-server-linux-setup-machine-learning.md)
- [安裝 PolyBase 套件](../relational-databases/polybase/polybase-linux-setup.md)
- [啟用 SQL Server Agent](sql-server-linux-setup-sql-agent.md)

## <a name="cu8-september-2020"></a><a id="cu8"></a> CU8 (2020 年 9 月)

這是 SQL Server 2019 (15.x) 的累積更新 8 (CU8) 版本。 此版本的 SQL Server 資料庫引擎版本為 15.0.4073.23。 如需修正與改善的資訊，請參閱 <https://support.microsoft.com/help/4577194> \(機器翻譯\)。

### <a name="package-details"></a>套件詳細資料

針對手動或離線套件安裝，您可以運用下表中的資訊下載 RPM 和 Debian 套件：

> [!NOTE]
> 從 CU1 開始，Red Hat 的離線套件安裝連結會指向 RHEL 8 套件。 如果您要尋找 RHEL 7 套件，請參閱下載路徑 <https://packages.microsoft.com/rhel/7/mssql-server-2019/>
>
> SQL Server 2019 從 CU3 開始支援 **Ubuntu 18.04**。 Ubuntu 的離線套件安裝連結會指向 Ubuntu 18.04 套件。 若要尋找 Ubuntu 16.04 套件，請參閱下載路徑 <https://packages.microsoft.com/ubuntu/16.04/mssql-server-2019/pool/main/m/>

| Package | 套件版本 | 下載 |
|-----|-----|-----|
| Red Hat RPM 套件 | 15.0.4073.23-4 | [引擎 RPM 套件](https://packages.microsoft.com/rhel/8/mssql-server-2019/mssql-server-15.0.4073.23-4.x86_64.rpm)</br>[高可用性 RPM 套件](https://packages.microsoft.com/rhel/8/mssql-server-2019/mssql-server-ha-15.0.4073.23-4.x86_64.rpm)</br>[全文檢索搜尋 RPM 套件](https://packages.microsoft.com/rhel/8/mssql-server-2019/mssql-server-fts-15.0.4073.23-4.x86_64.rpm)</br>[擴充性 RPM 套件](https://packages.microsoft.com/rhel/8/mssql-server-2019/mssql-server-extensibility-15.0.4073.23-4.x86_64.rpm)</br>[Java 擴充性 RPM 套件](https://packages.microsoft.com/rhel/8/mssql-server-2019/mssql-server-extensibility-java-15.0.4073.23-4.x86_64.rpm)</br>[PolyBase RPM 套件](https://packages.microsoft.com/rhel/8/mssql-server-2019/mssql-server-polybase-15.0.4073.23-4.x86_64.rpm)|
| SLES RPM 套件 | 15.0.4073.23-4 | [mssql-server 引擎 RPM 套件](https://packages.microsoft.com/sles/12/mssql-server-2019/mssql-server-15.0.4073.23-4.x86_64.rpm)</br>[高可用性 RPM 套件](https://packages.microsoft.com/sles/12/mssql-server-2019/mssql-server-ha-15.0.4073.23-4.x86_64.rpm)</br>[全文檢索搜尋 RPM 套件](https://packages.microsoft.com/sles/12/mssql-server-2019/mssql-server-fts-15.0.4073.23-4.x86_64.rpm)</br>[擴充性 RPM 套件](https://packages.microsoft.com/sles/12/mssql-server-2019/mssql-server-extensibility-15.0.4073.23-4.x86_64.rpm)</br>[Java 擴充性 RPM 套件](https://packages.microsoft.com/sles/12/mssql-server-2019/mssql-server-extensibility-java-15.0.4073.23-4.x86_64.rpm)</br>[PolyBase RPM 套件](https://packages.microsoft.com/sles/12/mssql-server-2019/mssql-server-polybase-15.0.4073.23-4.x86_64.rpm)|
| Ubuntu 18.04 Debian 套件 | 15.0.4073.23-4 | [引擎 Debian 套件](https://packages.microsoft.com/ubuntu/18.04/mssql-server-2019/pool/main/m/mssql-server/mssql-server_15.0.4073.23-4_amd64.deb)</br>[高可用性 Debian 套件](https://packages.microsoft.com/ubuntu/18.04/mssql-server-2019/pool/main/m/mssql-server-ha/mssql-server-ha_15.0.4073.23-4_amd64.deb)</br>[全文檢索搜尋 Debian 套件](https://packages.microsoft.com/ubuntu/18.04/mssql-server-2019/pool/main/m/mssql-server-fts/mssql-server-fts_15.0.4073.23-4_amd64.deb)</br>[擴充性 Debian 套件](https://packages.microsoft.com/ubuntu/18.04/mssql-server-2019/pool/main/m/mssql-server-extensibility/mssql-server-extensibility_15.0.4073.23-4_amd64.deb)</br>[Java 擴充性 Debian 套件](https://packages.microsoft.com/ubuntu/18.04/mssql-server-2019/pool/main/m/mssql-server-extensibility-java/mssql-server-extensibility-java_15.0.4073.23-4_amd64.deb)</br>[PolyBase RPM 套件](https://packages.microsoft.com/ubuntu/18.04/mssql-server-2019/pool/main/m/mssql-server-polybase/mssql-server-polybase_15.0.4073.23-4_amd64.deb)|

## <a name="cu6-july-2020"></a><a id="cu6"></a> CU6 (2020 年 7 月)

這是 SQL Server 2019 (15.x) 的累積更新 6 (CU6) 版本。 此發行版本的 SQL Server 資料庫引擎版本為 15.0.4053.23。 如需修正與改善的資訊，請參閱 <https://support.microsoft.com/help/4563110> \(機器翻譯\)。

### <a name="package-details"></a>套件詳細資料

針對手動或離線套件安裝，您可以運用下表中的資訊下載 RPM 和 Debian 套件：

> [!NOTE]
> 從 CU1 開始，Red Hat 的離線套件安裝連結會指向 RHEL 8 套件。 如果您要尋找 RHEL 7 套件，請參閱下載路徑 <https://packages.microsoft.com/rhel/7/mssql-server-2019/>
>
> SQL Server 2019 從 CU3 開始支援 **Ubuntu 18.04**。 Ubuntu 的離線套件安裝連結會指向 Ubuntu 18.04 套件。 若要尋找 Ubuntu 16.04 套件，請參閱下載路徑 <https://packages.microsoft.com/ubuntu/16.04/mssql-server-2019/pool/main/m/>

| Package | 套件版本 | 下載 |
|-----|-----|-----|
| Red Hat RPM 套件 | 15.0.4053.23-2 | [引擎 RPM 套件](https://packages.microsoft.com/rhel/8/mssql-server-2019/mssql-server-15.0.4053.23-2.x86_64.rpm)</br>[高可用性 RPM 套件](https://packages.microsoft.com/rhel/8/mssql-server-2019/mssql-server-ha-15.0.4053.23-2.x86_64.rpm)</br>[全文檢索搜尋 RPM 套件](https://packages.microsoft.com/rhel/8/mssql-server-2019/mssql-server-fts-15.0.4053.23-2.x86_64.rpm)</br>[擴充性 RPM 套件](https://packages.microsoft.com/rhel/8/mssql-server-2019/mssql-server-extensibility-15.0.4053.23-2.x86_64.rpm)</br>[Java 擴充性 RPM 套件](https://packages.microsoft.com/rhel/8/mssql-server-2019/mssql-server-extensibility-java-15.0.4053.23-2.x86_64.rpm)</br>[PolyBase RPM 套件](https://packages.microsoft.com/rhel/8/mssql-server-2019/mssql-server-polybase-15.0.4053.23-2.x86_64.rpm)|
| SLES RPM 套件 | 15.0.4053.23-2 | [mssql-server 引擎 RPM 套件](https://packages.microsoft.com/sles/12/mssql-server-2019/mssql-server-15.0.4053.23-2.x86_64.rpm)</br>[高可用性 RPM 套件](https://packages.microsoft.com/sles/12/mssql-server-2019/mssql-server-ha-15.0.4053.23-2.x86_64.rpm)</br>[全文檢索搜尋 RPM 套件](https://packages.microsoft.com/sles/12/mssql-server-2019/mssql-server-fts-15.0.4053.23-2.x86_64.rpm)</br>[擴充性 RPM 套件](https://packages.microsoft.com/sles/12/mssql-server-2019/mssql-server-extensibility-15.0.4053.23-2.x86_64.rpm)</br>[Java 擴充性 RPM 套件](https://packages.microsoft.com/sles/12/mssql-server-2019/mssql-server-extensibility-java-15.0.4053.23-2.x86_64.rpm)</br>[PolyBase RPM 套件](https://packages.microsoft.com/sles/12/mssql-server-2019/mssql-server-polybase-15.0.4053.23-2.x86_64.rpm)|
| Ubuntu 18.04 Debian 套件 | 15.0.4053.23-2 | [引擎 Debian 套件](https://packages.microsoft.com/ubuntu/18.04/mssql-server-2019/pool/main/m/mssql-server/mssql-server_15.0.4053.23-2_amd64.deb)</br>[高可用性 Debian 套件](https://packages.microsoft.com/ubuntu/18.04/mssql-server-2019/pool/main/m/mssql-server-ha/mssql-server-ha_15.0.4053.23-2_amd64.deb)</br>[全文檢索搜尋 Debian 套件](https://packages.microsoft.com/ubuntu/18.04/mssql-server-2019/pool/main/m/mssql-server-fts/mssql-server-fts_15.0.4053.23-2_amd64.deb)</br>[擴充性 Debian 套件](https://packages.microsoft.com/ubuntu/18.04/mssql-server-2019/pool/main/m/mssql-server-extensibility/mssql-server-extensibility_15.0.4053.23-2_amd64.deb)</br>[Java 擴充性 Debian 套件](https://packages.microsoft.com/ubuntu/18.04/mssql-server-2019/pool/main/m/mssql-server-extensibility-java/mssql-server-extensibility-java_15.0.4053.23-2_amd64.deb)</br>[PolyBase RPM 套件](https://packages.microsoft.com/ubuntu/18.04/mssql-server-2019/pool/main/m/mssql-server-polybase/mssql-server-polybase_15.0.4053.23-2_amd64.deb)|

## <a name="cu5-june-2020"></a><a id="cu5"></a> CU5 (2020 年 6 月)

此為 SQL Server 2019 (15.x) 的累積更新 5 (CU5) 版本。 此版次的 SQL Server 資料庫引擎版本為 15.0.4043.16。 如需修正與改善的資訊，請參閱 <https://support.microsoft.com/help/4552255> \(機器翻譯\)。

### <a name="package-details"></a>套件詳細資料

針對手動或離線套件安裝，您可以運用下表中的資訊下載 RPM 和 Debian 套件：

> [!NOTE]
> 從 CU1 開始，Red Hat 的離線套件安裝連結會指向 RHEL 8 套件。 如果您要尋找 RHEL 7 套件，請參閱下載路徑 <https://packages.microsoft.com/rhel/7/mssql-server-2019/>
>
> SQL Server 2019 從 CU3 開始支援 **Ubuntu 18.04**。 Ubuntu 的離線套件安裝連結會指向 Ubuntu 18.04 套件。 若要尋找 Ubuntu 16.04 套件，請參閱下載路徑 <https://packages.microsoft.com/ubuntu/16.04/mssql-server-2019/pool/main/m/>

| Package | 套件版本 | 下載 |
|-----|-----|-----|
| Red Hat RPM 套件 | 15.0.4043.16-4 | [引擎 RPM 套件](https://packages.microsoft.com/rhel/8/mssql-server-2019/mssql-server-15.0.4043.16-4.x86_64.rpm)</br>[高可用性 RPM 套件](https://packages.microsoft.com/rhel/8/mssql-server-2019/mssql-server-ha-15.0.4043.16-4.x86_64.rpm)</br>[全文檢索搜尋 RPM 套件](https://packages.microsoft.com/rhel/8/mssql-server-2019/mssql-server-fts-15.0.4043.16-4.x86_64.rpm)</br>[擴充性 RPM 套件](https://packages.microsoft.com/rhel/8/mssql-server-2019/mssql-server-extensibility-15.0.4043.16-4.x86_64.rpm)</br>[Java 擴充性 RPM 套件](https://packages.microsoft.com/rhel/8/mssql-server-2019/mssql-server-extensibility-java-15.0.4043.16-4.x86_64.rpm)</br>[PolyBase RPM 套件](https://packages.microsoft.com/rhel/8/mssql-server-2019/mssql-server-polybase-15.0.4043.16-4.x86_64.rpm)|
| SLES RPM 套件 | 15.0.4043.16-4 | [mssql-server 引擎 RPM 套件](https://packages.microsoft.com/sles/12/mssql-server-2019/mssql-server-15.0.4043.16-4.x86_64.rpm)</br>[高可用性 RPM 套件](https://packages.microsoft.com/sles/12/mssql-server-2019/mssql-server-ha-15.0.4043.16-4.x86_64.rpm)</br>[全文檢索搜尋 RPM 套件](https://packages.microsoft.com/sles/12/mssql-server-2019/mssql-server-fts-15.0.4043.16-4.x86_64.rpm)</br>[擴充性 RPM 套件](https://packages.microsoft.com/sles/12/mssql-server-2019/mssql-server-extensibility-15.0.4043.16-4.x86_64.rpm)</br>[Java 擴充性 RPM 套件](https://packages.microsoft.com/sles/12/mssql-server-2019/mssql-server-extensibility-java-15.0.4043.16-4.x86_64.rpm)</br>[PolyBase RPM 套件](https://packages.microsoft.com/sles/12/mssql-server-2019/mssql-server-polybase-15.0.4043.16-4.x86_64.rpm)|
| Ubuntu 18.04 Debian 套件 | 15.0.4043.16-4 | [引擎 Debian 套件](https://packages.microsoft.com/ubuntu/18.04/mssql-server-2019/pool/main/m/mssql-server/mssql-server_15.0.4043.16-4_amd64.deb)</br>[高可用性 Debian 套件](https://packages.microsoft.com/ubuntu/18.04/mssql-server-2019/pool/main/m/mssql-server-ha/mssql-server-ha_15.0.4043.16-4_amd64.deb)</br>[全文檢索搜尋 Debian 套件](https://packages.microsoft.com/ubuntu/18.04/mssql-server-2019/pool/main/m/mssql-server-fts/mssql-server-fts_15.0.4043.16-4_amd64.deb)</br>[擴充性 Debian 套件](https://packages.microsoft.com/ubuntu/18.04/mssql-server-2019/pool/main/m/mssql-server-extensibility/mssql-server-extensibility_15.0.4043.16-4_amd64.deb)</br>[Java 擴充性 Debian 套件](https://packages.microsoft.com/ubuntu/18.04/mssql-server-2019/pool/main/m/mssql-server-extensibility-java/mssql-server-extensibility-java_15.0.4043.16-4_amd64.deb)</br>[PolyBase RPM 套件](https://packages.microsoft.com/ubuntu/18.04/mssql-server-2019/pool/main/m/mssql-server-polybase/mssql-server-polybase_15.0.4043.16-4_amd64.deb)|

## <a name="cu4-april-2020"></a><a id="cu4"></a> CU4 (2020 年 4 月)

此為 SQL Server 2019 (15.x) 的累積更新 4 (CU4) 版本。 此版次的 SQL Server 資料庫引擎版本為 15.0.4033.1。 如需修正與改善的資訊，請參閱 <https://support.microsoft.com/help/4548597> \(機器翻譯\)。

### <a name="package-details"></a>套件詳細資料

針對手動或離線套件安裝，您可以運用下表中的資訊下載 RPM 和 Debian 套件：

> [!NOTE]
> 從 CU1 開始，Red Hat 的離線套件安裝連結會指向 RHEL 8 套件。 如果您要尋找 RHEL 7 套件，請參閱下載路徑 <https://packages.microsoft.com/rhel/7/mssql-server-2019/>
>
> SQL Server 2019 從 CU3 開始支援 **Ubuntu 18.04**。 Ubuntu 的離線套件安裝連結會指向 Ubuntu 18.04 套件。 若要尋找 Ubuntu 16.04 套件，請參閱下載路徑 <https://packages.microsoft.com/ubuntu/16.04/mssql-server-2019/pool/main/m/>

| Package | 套件版本 | 下載 |
|-----|-----|-----|
| Red Hat RPM 套件 | 15.0.4033.1-2 | [引擎 RPM 套件](https://packages.microsoft.com/rhel/8/mssql-server-2019/mssql-server-15.0.4033.1-2.x86_64.rpm)</br>[高可用性 RPM 套件](https://packages.microsoft.com/rhel/8/mssql-server-2019/mssql-server-ha-15.0.4033.1-2.x86_64.rpm)</br>[全文檢索搜尋 RPM 套件](https://packages.microsoft.com/rhel/8/mssql-server-2019/mssql-server-fts-15.0.4033.1-2.x86_64.rpm)</br>[擴充性 RPM 套件](https://packages.microsoft.com/rhel/8/mssql-server-2019/mssql-server-extensibility-15.0.4033.1-2.x86_64.rpm)</br>[Java 擴充性 RPM 套件](https://packages.microsoft.com/rhel/8/mssql-server-2019/mssql-server-extensibility-java-15.0.4033.1-2.x86_64.rpm)</br>[PolyBase RPM 套件](https://packages.microsoft.com/rhel/8/mssql-server-2019/mssql-server-polybase-15.0.4033.1-2.x86_64.rpm)|
| SLES RPM 套件 | 15.0.4033.1-2 | [mssql-server 引擎 RPM 套件](https://packages.microsoft.com/sles/12/mssql-server-2019/mssql-server-15.0.4033.1-2.x86_64.rpm)</br>[高可用性 RPM 套件](https://packages.microsoft.com/sles/12/mssql-server-2019/mssql-server-ha-15.0.4033.1-2.x86_64.rpm)</br>[全文檢索搜尋 RPM 套件](https://packages.microsoft.com/sles/12/mssql-server-2019/mssql-server-fts-15.0.4033.1-2.x86_64.rpm)</br>[擴充性 RPM 套件](https://packages.microsoft.com/sles/12/mssql-server-2019/mssql-server-extensibility-15.0.4033.1-2.x86_64.rpm)</br>[Java 擴充性 RPM 套件](https://packages.microsoft.com/sles/12/mssql-server-2019/mssql-server-extensibility-java-15.0.4033.1-2.x86_64.rpm)</br>[PolyBase RPM 套件](https://packages.microsoft.com/sles/12/mssql-server-2019/mssql-server-polybase-15.0.4033.1-2.x86_64.rpm)|
| Ubuntu 18.04 Debian 套件 | 15.0.4033.1-2 | [引擎 Debian 套件](https://packages.microsoft.com/ubuntu/18.04/mssql-server-2019/pool/main/m/mssql-server/mssql-server_15.0.4033.1-2_amd64.deb)</br>[高可用性 Debian 套件](https://packages.microsoft.com/ubuntu/18.04/mssql-server-2019/pool/main/m/mssql-server-ha/mssql-server-ha_15.0.4033.1-2_amd64.deb)</br>[全文檢索搜尋 Debian 套件](https://packages.microsoft.com/ubuntu/18.04/mssql-server-2019/pool/main/m/mssql-server-fts/mssql-server-fts_15.0.4033.1-2_amd64.deb)</br>[擴充性 Debian 套件](https://packages.microsoft.com/ubuntu/18.04/mssql-server-2019/pool/main/m/mssql-server-extensibility/mssql-server-extensibility_15.0.4033.1-2_amd64.deb)</br>[Java 擴充性 Debian 套件](https://packages.microsoft.com/ubuntu/18.04/mssql-server-2019/pool/main/m/mssql-server-extensibility-java/mssql-server-extensibility-java_15.0.4033.1-2_amd64.deb)</br>[PolyBase RPM 套件](https://packages.microsoft.com/ubuntu/18.04/mssql-server-2019/pool/main/m/mssql-server-polybase/mssql-server-polybase_15.0.4033.1-2_amd64.deb)|

## <a name="cu3-march-2020"></a><a id="cu3"></a> CU3 (2020 年 3 月)

這是 SQL Server 2019 (15.x) 的累積更新 3 (CU3) 版本。 此版次的 SQL Server 資料庫引擎版本為 15.0.4023.6。 如需修正與改善的資訊，請參閱 <https://support.microsoft.com/help/4538853> \(機器翻譯\)。

### <a name="package-details"></a>套件詳細資料

針對手動或離線套件安裝，您可以運用下表中的資訊下載 RPM 和 Debian 套件：

> [!NOTE]
> 從 CU1 開始，Red Hat 的離線套件安裝連結會指向 RHEL 8 套件。 如果您要尋找 RHEL 7 套件，請參閱下載路徑 <https://packages.microsoft.com/rhel/7/mssql-server-2019/>
>
> SQL Server 2019 從 CU3 開始支援 **Ubuntu 18.04**。 Ubuntu 的離線套件安裝連結會指向 Ubuntu 18.04 套件。 若要尋找 Ubuntu 16.04 套件，請參閱下載路徑 <https://packages.microsoft.com/ubuntu/16.04/mssql-server-2019/pool/main/m/>

| Package | 套件版本 | 下載 |
|-----|-----|-----|
| Red Hat RPM 套件 | 15.0.4023.6-2 | [引擎 RPM 套件](https://packages.microsoft.com/rhel/8/mssql-server-2019/mssql-server-15.0.4023.6-2.x86_64.rpm)</br>[高可用性 RPM 套件](https://packages.microsoft.com/rhel/8/mssql-server-2019/mssql-server-ha-15.0.4023.6-2.x86_64.rpm)</br>[全文檢索搜尋 RPM 套件](https://packages.microsoft.com/rhel/8/mssql-server-2019/mssql-server-fts-15.0.4023.6-2.x86_64.rpm)</br>[擴充性 RPM 套件](https://packages.microsoft.com/rhel/8/mssql-server-2019/mssql-server-extensibility-15.0.4023.6-2.x86_64.rpm)</br>[Java 擴充性 RPM 套件](https://packages.microsoft.com/rhel/8/mssql-server-2019/mssql-server-extensibility-java-15.0.4023.6-2.x86_64.rpm)</br>[PolyBase RPM 套件](https://packages.microsoft.com/rhel/8/mssql-server-2019/mssql-server-polybase-15.0.4023.6-2.x86_64.rpm)|
| SLES RPM 套件 | 15.0.4023.6-2 | [mssql-server 引擎 RPM 套件](https://packages.microsoft.com/sles/12/mssql-server-2019/mssql-server-15.0.4023.6-2.x86_64.rpm)</br>[高可用性 RPM 套件](https://packages.microsoft.com/sles/12/mssql-server-2019/mssql-server-ha-15.0.4023.6-2.x86_64.rpm)</br>[全文檢索搜尋 RPM 套件](https://packages.microsoft.com/sles/12/mssql-server-2019/mssql-server-fts-15.0.4023.6-2.x86_64.rpm)</br>[擴充性 RPM 套件](https://packages.microsoft.com/sles/12/mssql-server-2019/mssql-server-extensibility-15.0.4023.6-2.x86_64.rpm)</br>[Java 擴充性 RPM 套件](https://packages.microsoft.com/sles/12/mssql-server-2019/mssql-server-extensibility-java-15.0.4023.6-2.x86_64.rpm)</br>[PolyBase RPM 套件](https://packages.microsoft.com/sles/12/mssql-server-2019/mssql-server-polybase-15.0.4023.6-2.x86_64.rpm)|
| Ubuntu 18.04 Debian 套件 | 15.0.4023.6-2 | [引擎 Debian 套件](https://packages.microsoft.com/ubuntu/18.04/mssql-server-2019/pool/main/m/mssql-server/mssql-server_15.0.4023.6-2_amd64.deb)</br>[高可用性 Debian 套件](https://packages.microsoft.com/ubuntu/18.04/mssql-server-2019/pool/main/m/mssql-server-ha/mssql-server-ha_15.0.4023.6-2_amd64.deb)</br>[全文檢索搜尋 Debian 套件](https://packages.microsoft.com/ubuntu/18.04/mssql-server-2019/pool/main/m/mssql-server-fts/mssql-server-fts_15.0.4023.6-2_amd64.deb)</br>[擴充性 Debian 套件](https://packages.microsoft.com/ubuntu/18.04/mssql-server-2019/pool/main/m/mssql-server-extensibility/mssql-server-extensibility_15.0.4023.6-2_amd64.deb)</br>[Java 擴充性 Debian 套件](https://packages.microsoft.com/ubuntu/18.04/mssql-server-2019/pool/main/m/mssql-server-extensibility-java/mssql-server-extensibility-java_15.0.4023.6-2_amd64.deb)</br>[PolyBase RPM 套件](https://packages.microsoft.com/ubuntu/18.04/mssql-server-2019/pool/main/m/mssql-server-polybase/mssql-server-polybase_15.0.4023.6-2_amd64.deb)|

## <a name="cu2-february-2020"></a><a id="cu2"></a> CU2 (2020 年 2 月)

這是 SQL Server 2019 (15.x) 的累積更新 2 (CU2) 版本。 此版本的 SQL Server 資料庫引擎版本為 15.0.4013.40。 如需修正與改善的資訊，請參閱 <https://support.microsoft.com/help/4536075> \(機器翻譯\)。

### <a name="package-details"></a>套件詳細資料

針對手動或離線套件安裝，您可以運用下表中的資訊下載 RPM 和 Debian 套件：

> [!NOTE]
> 從 CU1 開始，Red Hat 的離線套件安裝連結會指向 RHEL 8 套件。 如果您要尋找 RHEL 7 套件，請參閱下載路徑 <https://packages.microsoft.com/rhel/7/mssql-server-2019/>

| Package | 套件版本 | 下載 |
|-----|-----|-----|
| Red Hat RPM 套件 | 15.0.4013.40-8 | [引擎 RPM 套件](https://packages.microsoft.com/rhel/8/mssql-server-2019/mssql-server-15.0.4013.40-8.x86_64.rpm)</br>[高可用性 RPM 套件](https://packages.microsoft.com/rhel/8/mssql-server-2019/mssql-server-ha-15.0.4013.40-8.x86_64.rpm)</br>[全文檢索搜尋 RPM 套件](https://packages.microsoft.com/rhel/8/mssql-server-2019/mssql-server-fts-15.0.4013.40-8.x86_64.rpm)</br>[擴充性 RPM 套件](https://packages.microsoft.com/rhel/8/mssql-server-2019/mssql-server-extensibility-15.0.4013.40-8.x86_64.rpm)</br>[Java 擴充性 RPM 套件](https://packages.microsoft.com/rhel/8/mssql-server-2019/mssql-server-extensibility-java-15.0.4013.40-8.x86_64.rpm)</br>[PolyBase RPM 套件](https://packages.microsoft.com/rhel/8/mssql-server-2019/mssql-server-polybase-15.0.4013.40-8.x86_64.rpm)|
| SLES RPM 套件 | 15.0.4013.40-8 | [mssql-server 引擎 RPM 套件](https://packages.microsoft.com/sles/12/mssql-server-2019/mssql-server-15.0.4013.40-8.x86_64.rpm)</br>[高可用性 RPM 套件](https://packages.microsoft.com/sles/12/mssql-server-2019/mssql-server-ha-15.0.4013.40-8.x86_64.rpm)</br>[全文檢索搜尋 RPM 套件](https://packages.microsoft.com/sles/12/mssql-server-2019/mssql-server-fts-15.0.4013.40-8.x86_64.rpm)</br>[擴充性 RPM 套件](https://packages.microsoft.com/sles/12/mssql-server-2019/mssql-server-extensibility-15.0.4013.40-8.x86_64.rpm)</br>[Java 擴充性 RPM 套件](https://packages.microsoft.com/sles/12/mssql-server-2019/mssql-server-extensibility-java-15.0.4013.40-8.x86_64.rpm)</br>[PolyBase RPM 套件](https://packages.microsoft.com/sles/12/mssql-server-2019/mssql-server-polybase-15.0.4013.40-8.x86_64.rpm)|
| Ubuntu 16.04 Debian 套件 | 15.0.4013.40-8 | [引擎 Debian 套件](https://packages.microsoft.com/ubuntu/16.04/mssql-server-2019/pool/main/m/mssql-server/mssql-server_15.0.4013.40-8_amd64.deb)</br>[高可用性 Debian 套件](https://packages.microsoft.com/ubuntu/16.04/mssql-server-2019/pool/main/m/mssql-server-ha/mssql-server-ha_15.0.4013.40-8_amd64.deb)</br>[全文檢索搜尋 Debian 套件](https://packages.microsoft.com/ubuntu/16.04/mssql-server-2019/pool/main/m/mssql-server-fts/mssql-server-fts_15.0.4013.40-8_amd64.deb)</br>[擴充性 Debian 套件](https://packages.microsoft.com/ubuntu/16.04/mssql-server-2019/pool/main/m/mssql-server-extensibility/mssql-server-extensibility_15.0.4013.40-8_amd64.deb)</br>[Java 擴充性 Debian 套件](https://packages.microsoft.com/ubuntu/16.04/mssql-server-2019/pool/main/m/mssql-server-extensibility-java/mssql-server-extensibility-java_15.0.4013.40-8_amd64.deb)</br>[PolyBase RPM 套件](https://packages.microsoft.com/ubuntu/16.04/mssql-server-2019/pool/main/m/mssql-server-polybase/mssql-server-polybase_15.0.4013.40-8_amd64.deb)|

## <a name="cu1-january-2020"></a><a id="cu1"></a> CU1 (2020年1月)

這是 SQL Server 2019 (15.x) 的累積更新 1 (CU1) 版。 此版本的 SQL Server 資料庫引擎版本為 15.0.4003.23。 如需此版本中的修正和改善資訊，請參閱 <https://support.microsoft.com/en-us/help/4527376>。

### <a name="package-details"></a>套件詳細資料

針對手動或離線套件安裝，您可以運用下表中的資訊下載 RPM 和 Debian 套件：

> [!NOTE]
> 從 CU1 開始，Red Hat 的離線套件安裝連結會指向 RHEL 8 套件。 如果您要尋找 RHEL 7 套件，請參閱下載路徑 <https://packages.microsoft.com/rhel/7/mssql-server-2019/>

| Package | 套件版本 | 下載 |
|-----|-----|-----|
| Red Hat RPM 套件 | 15.0.4003.23-3 | [引擎 RPM 套件](https://packages.microsoft.com/rhel/8/mssql-server-2019/mssql-server-15.0.4003.23-3.x86_64.rpm)</br>[高可用性 RPM 套件](https://packages.microsoft.com/rhel/8/mssql-server-2019/mssql-server-ha-15.0.4003.23-3.x86_64.rpm)</br>[全文檢索搜尋 RPM 套件](https://packages.microsoft.com/rhel/8/mssql-server-2019/mssql-server-fts-15.0.4003.23-3.x86_64.rpm)</br>[擴充性 RPM 套件](https://packages.microsoft.com/rhel/8/mssql-server-2019/mssql-server-extensibility-15.0.4003.23-3.x86_64.rpm)</br>[Java 擴充性 RPM 套件](https://packages.microsoft.com/rhel/8/mssql-server-2019/mssql-server-extensibility-java-15.0.4003.23-3.x86_64.rpm)</br>[PolyBase RPM 套件](https://packages.microsoft.com/rhel/8/mssql-server-2019/mssql-server-polybase-15.0.4003.23-3.x86_64.rpm)|
| SLES RPM 套件 | 15.0.4003.23-3 | [mssql-server 引擎 RPM 套件](https://packages.microsoft.com/sles/12/mssql-server-2019/mssql-server-15.0.4003.23-3.x86_64.rpm)</br>[高可用性 RPM 套件](https://packages.microsoft.com/sles/12/mssql-server-2019/mssql-server-ha-15.0.4003.23-3.x86_64.rpm)</br>[全文檢索搜尋 RPM 套件](https://packages.microsoft.com/sles/12/mssql-server-2019/mssql-server-fts-15.0.4003.23-3.x86_64.rpm)</br>[擴充性 RPM 套件](https://packages.microsoft.com/sles/12/mssql-server-2019/mssql-server-extensibility-15.0.4003.23-3.x86_64.rpm)</br>[Java 擴充性 RPM 套件](https://packages.microsoft.com/sles/12/mssql-server-2019/mssql-server-extensibility-java-15.0.4003.23-3.x86_64.rpm)</br>[PolyBase RPM 套件](https://packages.microsoft.com/sles/12/mssql-server-2019/mssql-server-polybase-15.0.4003.23-3.x86_64.rpm)|
| Ubuntu 16.04 Debian 套件 | 15.0.4003.23-3 | [引擎 Debian 套件](https://packages.microsoft.com/ubuntu/16.04/mssql-server-2019/pool/main/m/mssql-server/mssql-server_15.0.4003.23-3_amd64.deb)</br>[高可用性 Debian 套件](https://packages.microsoft.com/ubuntu/16.04/mssql-server-2019/pool/main/m/mssql-server-ha/mssql-server-ha_15.0.4003.23-3_amd64.deb)</br>[全文檢索搜尋 Debian 套件](https://packages.microsoft.com/ubuntu/16.04/mssql-server-2019/pool/main/m/mssql-server-fts/mssql-server-fts_15.0.4003.23-3_amd64.deb)</br>[擴充性 Debian 套件](https://packages.microsoft.com/ubuntu/16.04/mssql-server-2019/pool/main/m/mssql-server-extensibility/mssql-server-extensibility_15.0.4003.23-3_amd64.deb)</br>[Java 擴充性 Debian 套件](https://packages.microsoft.com/ubuntu/16.04/mssql-server-2019/pool/main/m/mssql-server-extensibility-java/mssql-server-extensibility-java_15.0.4003.23-3_amd64.deb)</br>[PolyBase RPM 套件](https://packages.microsoft.com/ubuntu/16.04/mssql-server-2019/pool/main/m/mssql-server-polybase/mssql-server-polybase_15.0.4003.23-3_amd64.deb)|

## <a name="ga-november-2019"></a><a id="ga"></a> GA (2019 年 11 月)

這是 SQL Server 2019 (15.x) 的正式運作 (GA) 版本。 此版本的 SQL Server 資料庫引擎版本為 15.0.2000.5。

### <a name="package-details"></a>套件詳細資料

針對手動或離線套件安裝，您可以運用下表中的資訊下載 RPM 和 Debian 套件：

| Package | 套件版本 | 下載 |
|-----|-----|-----|
| Red Hat RPM 套件 | 15.0.2000.5-5 | [引擎 RPM 套件](https://packages.microsoft.com/rhel/7/mssql-server-2019/mssql-server-15.0.2000.5-5.x86_64.rpm)</br>[高可用性 RPM 套件](https://packages.microsoft.com/rhel/7/mssql-server-2019/mssql-server-ha-15.0.2000.5-5.x86_64.rpm)</br>[全文檢索搜尋 RPM 套件](https://packages.microsoft.com/rhel/7/mssql-server-2019/mssql-server-fts-15.0.2000.5-5.x86_64.rpm)</br>[擴充性 RPM 套件](https://packages.microsoft.com/rhel/7/mssql-server-2019/mssql-server-extensibility-15.0.2000.5-5.x86_64.rpm)</br>[Java 擴充性 RPM 套件](https://packages.microsoft.com/rhel/7/mssql-server-2019/mssql-server-extensibility-java-15.0.2000.5-5.x86_64.rpm)</br>[PolyBase RPM 套件](https://packages.microsoft.com/rhel/7/mssql-server-2019/mssql-server-polybase-15.0.2000.5-5.x86_64.rpm)|
| SLES RPM 套件 | 15.0.2000.5-5 | [mssql-server 引擎 RPM 套件](https://packages.microsoft.com/sles/12/mssql-server-2019/mssql-server-15.0.2000.5-5.x86_64.rpm)</br>[高可用性 RPM 套件](https://packages.microsoft.com/sles/12/mssql-server-2019/mssql-server-ha-15.0.2000.5-5.x86_64.rpm)</br>[全文檢索搜尋 RPM 套件](https://packages.microsoft.com/sles/12/mssql-server-2019/mssql-server-fts-15.0.2000.5-5.x86_64.rpm)</br>[擴充性 RPM 套件](https://packages.microsoft.com/sles/12/mssql-server-2019/mssql-server-extensibility-15.0.2000.5-5.x86_64.rpm)</br>[Java 擴充性 RPM 套件](https://packages.microsoft.com/sles/12/mssql-server-2019/mssql-server-extensibility-java-15.0.2000.5-5.x86_64.rpm)</br>[PolyBase RPM 套件](https://packages.microsoft.com/sles/12/mssql-server-2019/mssql-server-polybase-15.0.2000.5-5.x86_64.rpm)|
| Ubuntu 16.04 Debian 套件 | 15.0.2000.5-5 | [引擎 Debian 套件](https://packages.microsoft.com/ubuntu/16.04/mssql-server-2019/pool/main/m/mssql-server/mssql-server_15.0.2000.5-5_amd64.deb)</br>[高可用性 Debian 套件](https://packages.microsoft.com/ubuntu/16.04/mssql-server-2019/pool/main/m/mssql-server-ha/mssql-server-ha_15.0.2000.5-5_amd64.deb)</br>[全文檢索搜尋 Debian 套件](https://packages.microsoft.com/ubuntu/16.04/mssql-server-2019/pool/main/m/mssql-server-fts/mssql-server-fts_15.0.2000.5-5_amd64.deb)</br>[擴充性 Debian 套件](https://packages.microsoft.com/ubuntu/16.04/mssql-server-2019/pool/main/m/mssql-server-extensibility/mssql-server-extensibility_15.0.2000.5-5_amd64.deb)</br>[Java 擴充性 Debian 套件](https://packages.microsoft.com/ubuntu/16.04/mssql-server-2019/pool/main/m/mssql-server-extensibility-java/mssql-server-extensibility-java_15.0.2000.5-5_amd64.deb)</br>[PolyBase RPM 套件](https://packages.microsoft.com/ubuntu/16.04/mssql-server-2019/pool/main/m/mssql-server-polybase/mssql-server-polybase_15.0.2000.5-5_amd64.deb)|

## <a name="release-candidate-august-2019"></a><a id="rc"></a> 候選版 (2019 年 8 月)

下列各節提供候選版的套件位置與已知問題。 若要深入了解 SQL Server 2019 上適用於 Linux 的新功能，請參閱 [SQL Server 2019 中的新功能](../sql-server/what-s-new-in-sql-server-ver15.md)。

### <a name="package-details"></a>套件詳細資料

針對手動或離線套件安裝，您可以運用下表中的資訊下載 RPM 和 Debian 套件：

| Package | 套件版本 | 下載 |
|-----|-----|-----|
| Red Hat RPM 套件 | 15.0.1900.25-1 | [引擎 RPM 套件](https://packages.microsoft.com/rhel/7/mssql-server-preview/mssql-server-15.0.1900.25-1.x86_64.rpm)</br>[全文檢索搜尋 RPM 套件](https://packages.microsoft.com/rhel/7/mssql-server-preview/mssql-server-fts-15.0.1900.25-1.x86_64.rpm)</br>[擴充性 RPM 套件](https://packages.microsoft.com/rhel/7/mssql-server-preview/mssql-server-extensibility-15.0.1900.25-1.x86_64.rpm)</br>[Java 擴充性 RPM 套件](https://packages.microsoft.com/rhel/7/mssql-server-preview/mssql-server-extensibility-java-15.0.1900.25-1.x86_64.rpm)</br>[PolyBase RPM 套件](https://packages.microsoft.com/rhel/7/mssql-server-preview/mssql-server-polybase-15.0.1900.25-1.x86_64.rpm)|
| SLES RPM 套件 | 15.0.1900.25-1 | [mssql-server 引擎 RPM 套件](https://packages.microsoft.com/sles/12/mssql-server-preview/mssql-server-15.0.1900.25-1.x86_64.rpm)</br>[全文檢索搜尋 RPM 套件](https://packages.microsoft.com/sles/12/mssql-server-preview/mssql-server-fts-15.0.1900.25-1.x86_64.rpm)</br>[擴充性 RPM 套件](https://packages.microsoft.com/sles/12/mssql-server-preview/mssql-server-extensibility-15.0.1900.25-1.x86_64.rpm)</br>[Java 擴充性 RPM 套件](https://packages.microsoft.com/sles/12/mssql-server-preview/mssql-server-extensibility-java-15.0.1900.25-1.x86_64.rpm)</br>[PolyBase RPM 套件](https://packages.microsoft.com/sles/12/mssql-server-preview/mssql-server-polybase-15.0.1900.25-1.x86_64.rpm)|
| Ubuntu 16.04 Debian 套件 | 15.0.1900.25-1 | [引擎 Debian 套件](https://packages.microsoft.com/ubuntu/16.04/mssql-server-preview/pool/main/m/mssql-server/mssql-server_15.0.1900.25-1_amd64.deb)</br>[全文檢索搜尋 Debian 套件](https://packages.microsoft.com/ubuntu/16.04/mssql-server-preview/pool/main/m/mssql-server-fts/mssql-server-fts_15.0.1900.25-1_amd64.deb)</br>[擴充性 Debian 套件](https://packages.microsoft.com/ubuntu/16.04/mssql-server-preview/pool/main/m/mssql-server-extensibility/mssql-server-extensibility_15.0.1900.25-1_amd64.deb)</br>[Java 擴充性 Debian 套件](https://packages.microsoft.com/ubuntu/16.04/mssql-server-preview/pool/main/m/mssql-server-extensibility-java/mssql-server-extensibility-java_15.0.1900.25-1_amd64.deb)</br>[PolyBase RPM 套件](https://packages.microsoft.com/ubuntu/16.04/mssql-server-preview/pool/main/m/mssql-server-polybase/mssql-server-polybase_15.0.1900.25-1_amd64.deb)|

## <a name="known-issues"></a>已知問題

下列各節描述 Linux 上 SQL Server 2019 (15.x) 正式運作 (GA) 版本的已知問題。

### <a name="general"></a>一般

- [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 安裝位置的主機名稱長度必須等於或少於 15 個字元。 

    - **解決方法**：將 /etc/hostname 中的名稱長度變更為等於或少於 15 個字元。

- 手動將系統時間設定成過去的時間會造成 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 停止更新 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 內的內部系統時間。

    - **解決方法**：重新啟動 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]。

- 僅支援單一執行個體安裝。

    - **解決方法**：如果您想要在指定的主機上有多個執行個體，請考慮使用 VM 或 Docker 容器。 

- [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] Configuration Manager 無法連線至 Linux 上的 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]。

- **sa** 登入的預設語言是英文。

    - **解決方法**：使用 **ALTER LOGIN** 陳述式來變更 **sa** 登入的語言。

- OLEDB 提供者會記錄下列警告：`Failed to verify the Authenticode signature of 'C:\binn\msoledbsql.dll'. Signature verification of SQL Server DLLs will be skipped. Genuine copies of SQL Server are signed. Failure to verify the Authenticode signature might indicate that this is not an authentic release of SQL Server. Install a genuine copy of SQL Server or contact customer support.`

   - **解決方法**：您不需要執行任何動作。 OLEDB 提供者是使用 SHA256 簽署的。 SQL Server 資料庫引擎不會正確地驗證已簽署的 .dll。

### <a name="databases"></a>資料庫

- 無法使用 mssql-conf 公用程式來移動 master 資料庫。 可以使用 mssql-conf 來移動其他系統資料庫。

- 還原在 Windows 上 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 備份的資料庫時，您必須使用 Transact-SQL 陳述式中的 **WITH MOVE** 子句。

- 某些適用於「傳輸層安全性」(TLS) 的演算法 (加密套件) 在搭配 Linux 上的 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 時無法正常運作。 這除了會導致在高可用性群組中的複本之間建立連線時發生問題，也會導致在嘗試連線至 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 時連線失敗。

   - **解決方法**：透過執行下列動作，修改 Linux 上 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 的 **mssql.conf** 設定指令碼以停用有問題的加密套件：

      1. 將下列內容新增至 /var/opt/mssql/mssql.conf。

          ```
          [network]
          tlsciphers= AES256-GCM-SHA384:AES128-GCM-SHA256:AES256-SHA256:AES128-SHA256:AES256-SHA:AES128-SHA:!ECDHE-RSA-AES128-GCM-SHA256:!ECDHE-RSA-AES256-GCM-SHA384:!ECDHE-ECDSA-AES256-GCM-SHA384:!ECDHE-ECDSA-AES128-GCM-SHA256:!ECDHE-ECDSA-AES256-SHA384:!ECDHE-ECDSA-AES128-SHA256:!ECDHE-ECDSA-AES256-SHA:!ECDHE-ECDSA-AES128-SHA:!ECDHE-RSA-AES256-SHA384:!ECDHE-RSA-AES128-SHA256:!ECDHE-RSA-AES256-SHA:!ECDHE-RSA-AES128-SHA:!DHE-RSA-AES256-GCM-SHA384:!DHE-RSA-AES128-GCM-SHA256:!DHE-RSA-AES256-SHA:!DHE-RSA-AES128-SHA:!DHE-DSS-AES256-SHA256:!DHE-DSS-AES128-SHA256:!DHE-DSS-AES256-SHA:!DHE-DSS-AES128-SHA:!DHE-DSS-DES-CBC3-SHA:!NULL-SHA256:!NULL-SHA
          ```

         > [!NOTE]
         > 在上述程式碼中，`!` 會否定運算式。 這告訴 OpenSSL 不要使用下列加密套件。  

      1. 使用下列命令來重新啟動 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]。

          ```bash
          sudo systemctl restart mssql-server
          ```

- Windows 上使用記憶體內部 OLTP 的 [!INCLUDE[ssSQL14](../includes/sssql14-md.md)] 資料庫無法在 Linux 上的 SQL Server 2019 (15.x) 進行還原。 若要還原使用記憶體內部 OLTP 的 [!INCLUDE[ssSQL14](../includes/sssql14-md.md)] 資料庫，請先將資料庫升級到 Windows 上的 [!INCLUDE[ssSQL15](../includes/sssql15-md.md)]、SQL Server 2017 或 SQL Server 2019，然後透過備份/還原或中斷連結/附加來將資料庫移動到 Linux 上的 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]。

- Linux 上目前不支援 **ADMINISTER BULK OPERATIONS** 使用者權限。

### <a name="networking"></a>網路功能

下列兩個條件都成立時，涉及來自 sqlservr 程序之連出 TCP 連線的功能 (例如連結的伺服器或可用性群組) 可能無法運作：

1. 以主機名稱的形式而不是 IP 位址來指定目標伺服器。

1. 來源執行個體的核心中已停用 IPv6。 若要確認您系統的核心中是否已啟用 IPv6，必須通過下列所有測試：

   - `cat /proc/cmdline` 會列印目前核心的開機 cmdline。 輸出不得包含 `ipv6.disable=1`。
   - /proc/sys/net/ipv6/ 目錄必須存在。
   - 呼叫 `socket(AF_INET6, SOCK_STREAM, IPPROTO_IP)` 的 C 程式應該成功 - syscall 必須傳回 fd != -1 且未因 EAFNOSUPPORT 而發生失敗。

確切的錯誤需視功能而定。 就連結的伺服器而言，這會顯示為登入逾時錯誤。 就可用性群組而言，次要複本上的 `ALTER AVAILABILITY GROUP JOIN` DDL 會在 5 分鐘後因下載設定逾時錯誤而發生失敗。

若要解決這個問題，請執行下列其中一個動作：

1. 使用 IP 而不是主機名稱來指定 TCP 連線的目標。

1. 從開機 cmdline 中移除 `ipv6.disable=1` 以在核心中啟用 IPv6。 執行此動作的方式取決於 Linux 發行版本和開機載入器 (例如 grub)。 如果您想要停用 IPv6，您仍然可以透過在 `sysctl` 設定 (例如 `/etc/sysctl.conf`) 中設定 `net.ipv6.conf.all.disable_ipv6 = 1` 來予以停用。 這將仍然防止系統的網路介面卡取得 IPv6 位址，但可允許 sqlservr 功能正常運作。

#### <a name="network-file-system-nfs"></a>網路檔案系統 (NFS)
如果您在生產環境中使用 **網路檔案系統 (NFS)** 遠端共用，請注意下列支援需求：

- 使用 NFS 版本 **4.2 或更新的版本**。 舊版 NFS 不支援新式檔案系統常用的必要功能，例如 `fallocate` 和疏鬆檔案建立。
- 僅尋找 NFS 掛接上的 **/var/opt/mssql** 目錄。 其他檔案 (例如 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 系統二進位檔案) 並不受支援。
- 確定 NFS 用戶端在裝載遠端共用時使用 `nolock` 選項。

### <a name="localization"></a>當地語系化

- 如果在設定期間您的地區設定不是英文 (en_us)，您就必須在 bash 工作階段/終端機中使用 UTF-8 編碼。 如果您使用 ASCII 編碼，可能會看到類似以下的錯誤：

   ```
   UnicodeEncodeError: 'ascii' codec can't encode character u'\xf1' in position 8: ordinal not in range(128)
   ```

   如果您無法使用 UTF-8 編碼，請使用 MSSQL_LCID 環境變數來指定您的語言選擇以執行設定。

   ```bash
   sudo MSSQL_LCID=<LcidValue> /opt/mssql/bin/mssql-conf setup
   ```

- 執行 mssql-conf 設定及執行非英文的 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 安裝時，「正在設定 SQL Server...」當地語系化文字後面會顯示不正確的擴充字元。 或者，針對非拉丁文的安裝，此句子可能會完全遺失。 遺失的句子應該會顯示下列當地語系化字串：「已成功處理授權 PID。 新的版本為 [\<Name\> 版本]」。 此字串的輸出只是用來提供資訊，下一個 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 累積更新將會處理所有語言的這個問題。 這不會以任何方式影響成功的 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 安裝。 

#### <a name="full-text-search"></a>全文檢索搜尋

- 此版本並未提供所有篩選，包括適用於 Office 文件的篩選。 如需所支援篩選的清單，請參閱[在 Linux 上安裝 SQL Server 全文檢索搜尋](sql-server-linux-setup-full-text-search.md#filters)。

### <a name="sql-server-integration-services-ssis"></a><a id="ssis"></a> SQL Server Integration Services (SSIS)

- 此版本中在 SUSE 上不支援 **mssql-server-is** 套件。 目前在 Ubuntu 和 Red Hat Enterprise Linux (RHEL) 上支援此套件。

- 透過 Linux CTP 2.1 Refresh 和更新版本上的 [!INCLUDE[ssISnoversion](../includes/ssisnoversion-md.md)]，[!INCLUDE[ssISnoversion](../includes/ssisnoversion-md.md)] 套件可以在 Linux 上使用 ODBC 連接。 此功能已進行過 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 和 MySQL ODBC 驅動程式的測試，但也應該能夠與任何遵循 ODBC 規格的 Unicode ODBC 驅動程式搭配運作。 在設計階段，您可以提供 DSN 或連接字串來連接到 ODBC 資料；您也可以使用 Windows 驗證。 如需詳細資訊，請參閱[宣佈在 Linux 上提供 ODBC 支援的部落格文章](https://blogs.msdn.microsoft.com/ssis/2017/06/16/odbc-is-supported-in-ssis-on-linux-ssis-helsinki-ctp2-1-refresh/) \(英文\)。

- 在 Linux 上執行 SSIS 套件時，此版本不支援下列功能：
  - [!INCLUDE[ssISnoversion](../includes/ssisnoversion-md.md)] 類別目錄資料庫
  - SQL Agent 排定的套件執行
  - Windows 驗證
  - 協力廠商元件
  - 異動資料擷取 (CDC)
  - [!INCLUDE[ssISnoversion](../includes/ssisnoversion-md.md)] Scale Out
  - 適用於 SSIS 的 Azure Feature Pack
  - Hadoop 和 HDFS 支援
  - Microsoft Connector for SAP BW

如需目前不支援或支援但有限制的內建 SSIS 元件清單，請參閱 [Linux 上的 SSIS 限制和已知問題](sql-server-linux-ssis-known-issues.md#components)。

如需有關 Linux 上 SSIS 的詳細資訊，請參閱下列文章：
-   [宣佈對 Linux 提供 SSIS 支援的部落格文章](https://blogs.msdn.microsoft.com/ssis/2017/05/17/ssis-helsinki-is-available-in-sql-server-vnext-ctp2-1/) \(英文\)。
-   [在 Linux 上安裝 SQL Server Integration Services (SSIS)](sql-server-linux-setup-ssis.md)
-   [使用 SSIS 在 Linux 上擷取、轉換和載入資料](sql-server-linux-migrate-ssis.md)

### <a name="sql-server-management-studio-ssms"></a><a id="ssms"></a> SQL Server Management Studio (SSMS)

以下限制適用於 Windows 上連線至 Linux 上 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 的 [!INCLUDE[ssManStudioFull](../includes/ssmanstudiofull-md.md)]。

- 不支援維護計劃。

- 不支援 [!INCLUDE[ssManStudioFull](../includes/ssmanstudiofull-md.md)] 中的管理資料倉儲 (MDW) 和資料收集器。 

- 具有 Windows 驗證或 Windows 事件記錄檔選項的 [!INCLUDE[ssManStudioFull](../includes/ssmanstudiofull-md.md)] UI 元件無法與 Linux 搭配使用。 您仍然可以將這些功能與其他選項搭配使用，例如 SQL 登入。 

- 無法修改要保留的記錄檔數目。

## <a name="next-steps"></a>後續步驟

若要開始使用，請參閱下列快速入門：

- [在 Red Hat Enterprise Linux 上安裝](quickstart-install-connect-red-hat.md)
- [在 SUSE Linux Enterprise Server 上安裝](quickstart-install-connect-suse.md)
- [在 Ubuntu 上安裝](quickstart-install-connect-ubuntu.md)
- [在 Docker 上執行](quickstart-install-connect-ubuntu.md)
- [在 Azure 中佈建 SQL VM](/azure/virtual-machines/linux/sql/provision-sql-server-linux-virtual-machine?toc=/sql/toc/toc.json)
- [執行與連線 - 雲端](quickstart-install-connect-clouds.md)

如需常見問題的解答，請參閱 [Linux 上的 SQL Server 常見問題集](sql-server-linux-faq.md)。
