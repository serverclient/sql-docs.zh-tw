---
title: Linux 上的 SQL Server 的概觀
description: 此文章說明 SQL Server 如何在 Linux 上執行，並提供如何深入了解的相關資訊。
author: VanMSFT
ms.author: vanto
ms.date: 11/04/2019
ms.topic: conceptual
ms.prod: sql
ms.technology: linux
ms.assetid: 9dcc6a90-0add-42c2-815b-862e4e2a21ac
ms.openlocfilehash: 593a966baf3565ef2ed6d30eba92901dc8cbcca2
ms.sourcegitcommit: 86534989f7827f1c36ed1333ad9c4557dfd77f3d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/15/2020
ms.locfileid: "97515351"
---
# <a name="sql-server-on-linux"></a>Linux 上的 SQL Server

[!INCLUDE [SQL Server - Linux](../includes/applies-to-version/sql-linux.md)]

::: moniker range="= sql-server-2017 || = sql-server-linux-2017"
從 SQL Server 2017 開始，SQL Server 可以在 Linux 上執行。 這是相同的 SQL Server 資料庫引擎，無論您的作業系統為何，都提供許多類似的功能和服務。

> [!TIP]
> [SQL Server 2019](sql-server-linux-overview.md?view=sql-server-ver15&preserve-view=true) 已可供使用！ 若要了解最新版本中適用於 Linux 的新功能，請參閱 [Linux 上的 SQL Server 2019 新功能](sql-server-linux-whats-new-2019.md?view=sql-server-ver15&preserve-view=true)。
::: moniker-end

::: moniker range=">= sql-server-ver15 || >= sql-server-linux-ver15"
SQL Server 2019 可在 Linux 上執行。 這是相同的 SQL Server 資料庫引擎，無論您的作業系統為何，都提供許多類似的功能和服務。 若要深入了解此版本，請參閱 [Linux 上的 SQL Server 2019 新功能](sql-server-linux-whats-new-2019.md)。
::: moniker-end

## <a name="install"></a>安裝

請使用下列其中一個快速入門來安裝 Linux 上的 SQL Server 以開始使用：

- [在 Red Hat Enterprise Linux 上安裝](quickstart-install-connect-red-hat.md)
- [在 SUSE Linux Enterprise Server 上安裝](quickstart-install-connect-suse.md)
- [在 Ubuntu 上安裝](quickstart-install-connect-ubuntu.md)
- [在 Docker 上執行](quickstart-install-connect-docker.md)
- [在 Azure 中佈建 SQL VM](/azure/virtual-machines/linux/sql/provision-sql-server-linux-virtual-machine?toc=/sql/toc/toc.json)

> [!NOTE]
> Docker 本身可以在多個平台上執行，這表示您可以在 Linux、Mac 和 Windows 上執行 Docker 映像。

## <a name="connect"></a>連線

安裝之後，連線到 Linux 機器上的 SQL Server 執行個體。 您可以使用各種工具和驅動程式，從本機或遠端連線。 快速入門示範如何使用 [sqlcmd](sql-server-linux-setup-tools.md) 命令列工具。 其他工具包括下列項目：

| 工具 | 教學課程 |
|-----|-----|
| Visual Studio Code (VS Code) | [搭配使用 VS Code 與 Linux 上的 SQL Server](../tools/visual-studio-code/sql-server-develop-use-vscode.md) |
| SQL Server Management Studio (SSMS) | [使用 Windows 上的 SSMS 連線至 Linux 上的 SQL Server](sql-server-linux-manage-ssms.md) |
| SQL Server Data Tools (SSDT) | [搭配使用 SSDT 與 Linux 上的 SQL Server](sql-server-linux-develop-use-ssdt.md) |

## <a name="explore"></a>瀏覽

<!--SQL Server 2017 on Linux-->
::: moniker range="= sql-server-linux-2017 || = sql-server-2017"

SQL Server 2017 在所有支援的平台 (包括 Linux) 上都有相同的基礎資料庫引擎。 因此，許多現有的特性與功能在 Linux 上的運作方式都相同。 文件的此區域會從 Linux 的觀點來公開其中一些功能。 它也強調在 Linux 上有獨特需求的區域。

如果您已經熟悉 SQL Server，請檢閱[版本資訊](sql-server-linux-release-notes.md)，以取得此版本的一般指導方針和已知問題。 然後查看 [Linux 上的 SQL Server 的新功能](sql-server-linux-whats-new.md)，以及[整體 SQL Server 2017 的新功能](../sql-server/what-s-new-in-sql-server-2017.md)。

::: moniker-end
<!--SQL Server 2019 on Linux-->
::: moniker range=">= sql-server-linux-ver15 || >= sql-server-ver15"

[!INCLUDE[SQL Server 2019](../includes/sssqlv15-md.md)] 在所有支援的平台 (包括 Linux) 上都有相同的基礎資料庫引擎。 因此，許多現有的特性與功能在 Linux 上的運作方式都相同。 文件的此區域會從 Linux 的觀點來公開其中一些功能。 它也強調在 Linux 上有獨特需求的區域。

如果您已經熟悉 Linux 上的 SQL Server，請檢閱[版本資訊](sql-server-linux-release-notes-2019.md)，以取得此版本的一般指導方針和已知問題。 然後查看 [Linux 上的 SQL Server 2019 新功能](../sql-server/what-s-new-in-sql-server-ver15.md)。

::: moniker-end


### <a name="all-versions-of-sql-server"></a>SQL Server 的所有版本

SQL Server 2017 和 [!INCLUDE[SQL Server 2019](../includes/sssqlv15-md.md)] 在所有支援的平台 (包括 Linux) 上都有相同的基礎資料庫引擎。 因此，許多現有的特性與功能在 Linux 上的運作方式都相同。 文件的此區域會從 Linux 的觀點來公開其中一些功能。 它也強調在 Linux 上有獨特需求的區域。

如果您已經熟悉 Linux 上的 SQL Server，請檢閱版本資訊：

- [SQL Server 2017 版本資訊](sql-server-linux-release-notes.md)
- [SQL Server 2019 版本資訊](sql-server-linux-release-notes-2019.md)

然後查看新功能：

- [SQL Server 2017 新功能](sql-server-linux-whats-new.md)
- [Linux 上的 SQL Server 2019 新功能](../sql-server/what-s-new-in-sql-server-ver15.md#sql-server-on-linux)

> [!TIP]
> 如需常見問題的解答，請參閱 [Linux 上的 SQL Server 常見問題集](sql-server-linux-faq.md)。

[!INCLUDE[Get Help Options](../includes/paragraph-content/get-help-options.md)]

[!INCLUDE[contribute-to-content](../includes/paragraph-content/contribute-to-content.md)]
