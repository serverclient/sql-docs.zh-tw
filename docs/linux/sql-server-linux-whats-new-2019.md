---
title: Linux 上的 SQL Server 2019 新功能
description: 本文特別介紹 Linux 上的 SQL Server 2019 新功能。
author: VanMSFT
ms.author: vanto
ms.date: 03/12/2020
ms.topic: conceptual
ms.prod: sql
ms.technology: linux
ms.openlocfilehash: 9ec5d4f34d299876614383c79fb79f2c2be9114d
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97641267"
---
# <a name="whats-new-for-sql-server-2019-on-linux"></a>Linux 上的 SQL Server 2019 新功能

[!INCLUDE [SQL Server - Linux](../includes/applies-to-version/sql-linux.md)]

本文描述適用於 Linux 上所執行 SQL Server 2019 的主要功能和服務。 如需套件下載和已知問題，請參閱[版本資訊](sql-server-linux-release-notes-2019.md)。

## <a name="ubuntu-1804-supported"></a>支援 Ubuntu 18.04

從 SQL Server 2019 CU3 開始，現在支援 Ubuntu 18.04。 請參閱[安裝 SQL Server，並在 Ubuntu 上建立資料庫](quickstart-install-connect-ubuntu.md?view=sql-server-linux-ver15&preserve-view=true)中的快速入門。

## <a name="rhel-8-supported"></a>支援 RHEL 8

從 SQL Server 2019 CU1 開始，現在支援 RHEL 8。 請參閱[安裝 SQL Server，並在 Red Hat 上建立資料庫](quickstart-install-connect-red-hat.md?view=sql-server-linux-ver15&preserve-view=true)中的快速入門。

## <a name="updates"></a>更新

已在 Linux 上 SQL Server 2019 進行的更新：

| 新功能或更新 | 詳細資料 |
|:-----|:-----|
|複寫支援 |[Linux 上的 SQL Server 複寫](sql-server-linux-replication.md)
|Microsoft Distributed Transaction Coordinator (MSDTC) 支援 |[如何在 Linux 上設定 MSDTC](sql-server-linux-configure-msdtc.md) |
|第三方 AD 提供者的 OpenLDAP 支援 |[教學課程：在 Linux 上搭配使用 Active Directory 驗證與 SQL Server](sql-server-linux-active-directory-authentication.md) |
|Linux 上的機器學習 |[在 Linux 上設定機器學習服務](sql-server-linux-setup-machine-learning.md) |
|`tempdb` 改進功能 | 根據預設，在 Linux 上進行新的 SQL Server 安裝，會依據邏輯核心的數目建立多個 `tempdb` 資料檔 (最多 8 個資料檔案)。 此情況不適用於就地次要或主要版本升級。 每個 `tempdb` 檔都為 8 MB，且可自動成長到 64 MB。 此行為類似於 Windows 上的預設 SQL Server 安裝。 |
| Linux 上的 PolyBase | 在 Linux 上為非 Hadoop 連接器[安裝 PolyBase](../relational-databases/polybase/polybase-linux-setup.md)。<br/><br/>[PolyBase 類型對應](../relational-databases/polybase/polybase-type-mapping.md)。 |
| 異動資料擷取 (CDC) 支援 | 在 Linux 上現在已針對 SQL Server 2019 支援異動資料擷取 (CDC)。 |
| Microsoft 容器登錄 | [Microsoft 容器登錄](https://azure.microsoft.com/blog/microsoft-syndicates-container-catalog/)現在已取代適用於新官方 Microsoft 容器映像的 Docker Hub，包括 [!INCLUDE[sql-server-2019](../includes/sssqlv15-md.md)]。 |
| 非根容器 | [!INCLUDE[sql-server-2019](../includes/sssqlv15-md.md)] 引進以非根使用者身分啟動 [!INCLUDE[sql-server](../includes/ssnoversion-md.md)] 程序來建立更安全容器的功能。 請參閱[以非根使用者的身分建置並執行 SQL Server 容器](./sql-server-linux-docker-container-security.md#buildnonrootcontainer)以取得詳細資料。 |

## <a name="next-steps"></a>後續步驟

若要在 Linux 上安裝 SQL Server，請使用下列其中一個教學課程：

- [在 Red Hat Enterprise Linux 上安裝](quickstart-install-connect-red-hat.md?view=sql-server-linux-ver15&preserve-view=true)
- [在 SUSE Linux Enterprise Server 上安裝](quickstart-install-connect-suse.md?view=sql-server-linux-ver15&preserve-view=true)
- [在 Ubuntu 上安裝](quickstart-install-connect-ubuntu.md?view=sql-server-linux-ver15&preserve-view=true)
- [在 Docker 上執行](quickstart-install-connect-docker.md?view=sql-server-linux-ver15&preserve-view=true)
- [在 Azure 中佈建 SQL VM](/azure/virtual-machines/linux/sql/provision-sql-server-linux-virtual-machine?toc=/sql/toc/toc.json)

如需常見問題的解答，請參閱 [Linux 上的 SQL Server 常見問題集](sql-server-linux-faq.md)。 若要查看 SQL Server 2019 中引進的其他改善項目，請參閱 [SQL Server 2019 的新功能](../sql-server/what-s-new-in-sql-server-ver15.md?view=sql-server-ver15&preserve-view=true)。

[!INCLUDE[get-help-options](../includes/paragraph-content/get-help-options.md)]