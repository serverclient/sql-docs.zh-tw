---
title: 在 Linux 上匯出和匯入資料庫
description: 本文說明如何使用 SQL Server Management Studio 和 SqlPackage 在 Linux 的 SQL Server 上匯出和匯入資料庫。
author: VanMSFT
ms.author: vanto
ms.date: 10/02/2017
ms.topic: conceptual
ms.prod: sql
ms.technology: linux
ms.assetid: 2210cfc3-c23a-4025-a551-625890d6845f
ms.openlocfilehash: f33a5a6890d12628faf3144a0ca6e8471a15c73d
ms.sourcegitcommit: 866554663ca3191748b6e4eb4d8d82fa58c4e426
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97559280"
---
# <a name="export-and-import-a-database-on-linux-with-ssms-or-sqlpackageexe-on-windows"></a>使用 SSMS 或 Windows 上的 SqlPackage，在 Linux 上匯出和匯入資料庫

[!INCLUDE [SQL Server - Linux](../includes/applies-to-version/sql-linux.md)]

本文說明如何使用 [SQL Server Management Studio (SSMS)](../ssms/download-sql-server-management-studio-ssms.md) 和 [SqlPackage](../tools/sqlpackage/sqlpackage.md) 在 Linux 上的 SQL Server 上匯出和匯入資料庫。 SSMS 和 SqlPackage 是 Windows 應用程式，因此當您的 Windows 電腦可以連線至 Linux 遠端 SQL Server 執行個體時，請使用這項技術。

您應一律安裝並使用最新版本的 SQL Server Management Studio (SSMS)，如[在 Windows 上使用 SSMS 連線至 Linux 上的 SQL Server](sql-server-linux-manage-ssms.md) 中所述

> [!NOTE]
> 如果您要將資料庫從某個 SQL Server 執行個體移轉至另一個執行個體，建議使用[備份和還原](sql-server-linux-migrate-restore-database.md)。

## <a name="export-a-database-with-ssms"></a>使用 SSMS 匯出資料庫

1. 在 Windows 搜尋方塊中鍵入 **Microsoft SQL Server Management Studio** 來啟動 SSMS，然後按一下傳統型應用程式。

    ![SQL Server Management Studio](./media/sql-server-linux-manage-ssms/ssms.png) 

2. 在 [物件總管] 中連線至您的來源資料庫。 來源資料庫可以位於在內部部署或雲端中、於 Linux、Windows 或 Docker 上執行的 Microsoft SQL Server，以及 Azure SQL Database 或 Azure Synapse Analytics 中。

3. 以滑鼠右鍵按一下 [物件總管] 中的來源資料庫，指向 [工作]  ，然後按一下 [匯出資料層應用程式...] 

4. 在 [匯出精靈] 中按一下 [下一步]  ，然後在 [設定]  索引標籤上，將匯出設定為將 BACPAC 檔案儲存到本機磁碟位置或 Azure Blob。

5. 根據預設，會匯出資料庫中的所有物件。 按一下 [進階]  索引標籤，然後選擇您希望匯出的資料庫物件。

6. 按一下 [下一步]  ，然後按一下 [完成]  。

*.BACPAC 檔案已在您選擇的位置成功建立，且您已準備好將其匯入目標資料庫。

## <a name="import-a-database-with-ssms"></a>使用 SSMS 匯入資料庫

1. 在 Windows 搜尋方塊中鍵入 **Microsoft SQL Server Management Studio** 來啟動 SSMS，然後按一下傳統型應用程式。

    ![SQL Server Management Studio](./media/sql-server-linux-manage-ssms/ssms.png) 

2. 連線至您在 [物件總管] 中的目標伺服器。 目標伺服器可以是在內部部署或雲端中、於 Linux、Windows 或 Docker 上執行的 Microsoft SQL Server，以及 Azure SQL Database 或 Azure Synapse Analytics。

3. 以滑鼠右鍵按一下 [物件總管] 中的 [資料庫]  資料夾，然後按一下 [匯入資料層應用程式...] 

4. 若要在目標伺服器中建立資料庫，請從您的本機磁碟指定 BACPAC 檔案，或選取您上傳 BACPAC 檔案的 Azure 儲存體帳戶和容器。

5. 為資料庫提供新資料庫名稱。 如果您要在 Azure SQL Database 上匯入資料庫，請設定 Microsoft Azure SQL Database 的版本 (服務層級)、資料庫大小上限以及服務目標 (效能等級)。

6. 依序按一下 [下一步]  、[完成]  ，將 BACPAC 檔案匯入目標伺服器中的新資料庫。

*.BACPAC 檔案隨即匯入，以在您指定的目標伺服器中建立新資料庫。

## <a name="sqlpackage-command-line-option"></a><a id="sqlpackage"></a> SqlPackage 命令列選項

您也可以使用 SQL Server Data Tools (SSDT) 命令列工具 ([SqlPackage](../tools/sqlpackage/sqlpackage.md)) 來匯出和匯入 BACPAC 檔案。

下列範例命令會匯出 BACPAC 檔案：

```bash
SqlPackage.exe /a:Export /ssn:tcp:<your_server> /sdn:<your_database> /su:<username> /sp:<password> /tf:<path_to_bacpac>
```

使用下列命令，從 .BACPAC 檔案匯入資料庫結構描述和使用者資料：

```bash
SqlPackage.exe /a:Import /tsn:tcp:<your_server> /tdn:<your_database> /tu:<username> /tp:<password> /sf:<path_to_bacpac>

```

## <a name="see-also"></a>另請參閱
如需如何使用 SSMS 的詳細資訊，請參閱 [使用 SQL Server Management Studio](../ssms/sql-server-management-studio-ssms.md)。 如需 SqlPackage 的詳細資訊，請參閱 [SqlPackage 參考文件](../tools/sqlpackage/sqlpackage.md)。