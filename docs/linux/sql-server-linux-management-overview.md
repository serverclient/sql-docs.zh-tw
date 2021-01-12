---
title: 管理 Linux 上的 SQL Server
description: 本文提供 Linux 上執行之 SQL Server 的常見管理工作和工具連結。
author: VanMSFT
ms.author: vanto
ms.date: 03/17/2017
ms.topic: conceptual
ms.prod: sql
ms.technology: linux
ms.assetid: 6bd8eb0b-593d-467e-87ea-ab1c4dbcd1ea
moniker: '>= sql-server-linux-2017 || >= sql-server-2017 '
ms.openlocfilehash: 04ece9daf696d73b9de96f48a08e32136fe6ab66
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98094625"
---
# <a name="choose-the-right-tool-to-manage-sql-server-on-linux"></a>選擇適當的工具，管理 Linux 上的 SQL Server

[!INCLUDE [SQL Server - Linux](../includes/applies-to-version/sql-linux.md)]

有數種方式可以管理 Linux 上的 SQL Server。 下一節將快速介紹各種不同的管理工具和技術，並建議更多資源。

## <a name="mssql-conf"></a>mssql-conf 

**mssql-conf** 工具可設定 Linux 上的 SQL Server。 如需詳細資訊，請參閱[使用 mssql-conf 在 Linux 上設定 SQL Server](sql-server-linux-configure-mssql-conf.md)。

## <a name="transact-sql"></a>Transact-SQL

幾乎所有可在用戶端工具中執行的工作，也可透過 Transact-SQL 陳述式完成。 SQL Server 提供[動態管理檢視 (DMV)](../relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)，查詢 SQL Server 的狀態和組態。 還有用於資料庫管理工作的 [Transact-SQL 命令](../t-sql/language-reference.md)。 您可以在任何支援連線到 SQL Server 並執行 Transact-SQL 查詢的用戶端工具 (例如 [sqlcmd](sql-server-linux-setup-tools.md) 或 [Visual Studio Code](../tools/visual-studio-code/sql-server-develop-use-vscode.md)) 中執行這些命令。

## <a name="azure-data-studio"></a>Azure Data Studio

新的 Azure Data Studio 是用於管理 SQL Server 的跨平台工具。 如需詳細資訊，請參閱 [Azure Data Studio](../azure-data-studio/what-is-azure-data-studio.md)。

## <a name="sql-server-management-studio-on-windows"></a>Windows 上的 SQL Server Management Studio

SQL Server Management Studio (SSMS) 是 Windows 應用程式，提供用於管理 SQL Server 的圖形化使用者介面。 雖然目前只在 Windows 上執行，但您可以用以遠端連線到 Linux SQL Server 執行個體。 如需使用 SSMS 管理 SQL Server 的詳細資訊，請參閱[使用 SSMS 管理 Linux 上的 SQL Server](sql-server-linux-manage-ssms.md)。

## <a name="mssql-cli-preview"></a>mssql-cli (預覽)

Microsoft 發行了適用於 SQL Server 的新跨平台指令碼工具 [mssql-cli](https://blogs.technet.microsoft.com/dataplatforminsider/2017/12/12/try-mssql-cli-a-new-interactive-command-line-tool-for-sql-server/)。 此工具目前為預覽狀態。

## <a name="powershell"></a>PowerShell

PowerShell 提供豐富的命令列環境來管理 Linux 上的 SQL Server。 如需詳細資訊，請參閱[使用 PowerShell 管理 Linux 上的 SQL Server](sql-server-linux-manage-powershell.md)。

## <a name="next-steps"></a>後續步驟

如需 Linux 上 SQL Server 的詳細資訊，請參閱 [Linux 上的 SQL Server](sql-server-linux-overview.md)。