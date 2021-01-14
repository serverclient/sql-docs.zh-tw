---
title: SQL Server PowerShell
description: 深入了解兩個 SQL Server PowerShell 模組：SqlServer 和 SQLPS，其中包括 PowerShell 提供者和 Cmdlet。
ms.prod: sql
ms.technology: sql-server-powershell
ms.topic: conceptual
ms.assetid: 89b70725-bbe7-4ffe-a27d-2a40005a97e7
author: markingmyname
ms.author: maghan
ms.reviewer: matteot
ms.custom: ''
ms.date: 06/11/2020
ms.openlocfilehash: fad1ac64dcc1024d5dad091670300b8d39fa0632
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98092680"
---
# <a name="sql-server-powershell"></a>SQL Server PowerShell

[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

**[安裝 SQL Server PowerShell](download-sql-server-ps-module.md)**

有兩個 SQL Server PowerShell 模組， **[SqlServer](/powershell/module/sqlserver)** 和 **[SQLPS](/powershell/module/sqlps)** 。

**SqlServer** 模組是要使用的最新 PowerShell 模組。

**SQLPS** 模組隨附於 SQL Server 安裝 (用於回溯相容性)，但不再更新。

**SqlServer** 模組包含 **SQLPS** 中 Cmdlet 的更新版本，且加入新的 Cmdlet 以支援 SQL 最新功能。

舊版 **SqlServer** 模組「曾」隨附於 [SQL Server Management Studio (SSMS)](../ssms/download-sql-server-management-studio-ssms.md)，但僅限 SSMS 16.x 版。

若要使用 PowerShell 搭配 SSMS 17.0 及更新版本，請從 [PowerShell 資源庫](https://www.powershellgallery.com/packages/SqlServer) 安裝 **SqlServer** 模組。

您也可以使用 [PowerShell 搭配 Azure Data Studio](../azure-data-studio/extensions/powershell-extension.md)。

**為什麼模組未從 SQLPS 變更為 SqlServer？**

為了提供 SQL PowerShell 更新，我們必須變更 SQL PowerShell 模組的識別，以及稱為 *SQLPS.exe* 的包裝函式。 由於這項項變更，現在有兩個 SQL PowerShell 模組：**SqlServer** 模組和 **SQLPS** 模組。  

**若匯入 SQLPS 模組，請更新 PowerShell 指令碼。**

如果您有任何執行 `Import-Module -Name SQLPS` 的 PowerShell 指令碼，並想充分利用新的提供者功能和新的 Cmdlet，您必須將其變更為 `Import-Module -Name SqlServer`。 新的模組會安裝到 `%ProgramFiles%\WindowsPowerShell\Modules\SqlServer` 資料夾。 因此，您不需要更新 $env:PSModulePath 變數。 如果您有使用名為 **SqlServer** 之第三方或社群版模組的指令碼，請使用 Prefix 參數來避免發生名稱衝突。

如果 SQLPS 模組安裝在同一部電腦上，則建議使用 *Import-Module SQLServer* 啟動指令碼，以避免發生並存問題。

本節適用於從 PowerShell (而非 SQL Agent) 執行的指令碼。 新模組可與使用 [#NOSQLPS](#sql-server-agent) 的 SQL Agent 作業步驟搭配使用。

## <a name="sql-server-powershell-components"></a>SQL Server PowerShell 元件

**SqlServer** 模組隨附於：

- [PowerShell 提供者](/powershell/module/microsoft.powershell.core/about/about_providers)，可啟用類似於檔案系統路徑的簡單導覽機制。 您可建置類似於檔案系統路徑的路徑，其中的磁碟機與 SQL Server 管理物件模型建立關聯，且節點是根據物件模型類別。 然後，您可以使用熟悉的命令 (例如 **cd** 和 **dir** ) 來巡覽路徑，其方式類似於在命令提示字元視窗中巡覽資料夾。 您可以使用其他命令 (例如 **ren** 或 **del**)，在路徑中的節點上執行動作。

- 一組 Cmdlet，可支援一些動作，例如執行包含 Transact-SQL 或 XQuery 陳述式的 **sqlcmd** 指令碼。  

- AS 提供者和 Cmdlet，這些項目之前是分開安裝的。

## <a name="sql-server-versions"></a>SQL Server 版本

SQL PowerShell Cmdlet 可用來管理 Azure SQL Database、Azure Synapse Analytics 及所有[支援的 SQL Server 產品](https://support.microsoft.com/lifecycle/search/1044)其執行個體。

## <a name="sql-server-identifiers-that-contain-characters-not-supported-in-powershell-paths"></a>含有 PowerShell 路徑不支援之字元的 SQL Server 識別碼

**Encode-Sqlname** 和 **Decode-Sqlname** Cmdlet 可協助您指定內含 PowerShell 路徑所不支援之字元的 SQL Server 識別碼。 如需詳細資訊，請參閱 [SQL Server Identifiers in PowerShell](sql-server-identifiers-in-powershell.md)。

使用 **Convert-UrnToPath** Cmdlet 將資料庫引擎物件其唯一資源名稱轉換為 SQL Server PowerShell 提供者的路徑。 如需詳細資訊，請參閱 [Convert URNs to SQL Server Provider Paths](/powershell/module/sqlserver/Convert-UrnToPath)。
  
## <a name="query-expressions-and-unique-resource-names"></a>查詢運算式和唯一的資源名稱  

查詢運算式是使用類似 XPath 語法的一些字串，可用以指定在物件模型階層中列舉一或多個物件的準則集合。 唯一的資源名稱 (URN) 是可唯一識別單一物件的特定查詢運算式字串類型。 如需詳細資訊，請參閱 [Query Expressions and Uniform Resource Names](query-expressions-and-uniform-resource-names.md)。

## <a name="sql-server-agent"></a>SQL Server Agent

SQL Server Agent 所使用的模組沒有任何變更。 因此，具有 PowerShell 類型作業步驟的 SQL Server Agent 作業會使用 SQLPS 模組。 如需詳細資訊，請參閱[如何使用 SQL Server Agent 執行 PowerShell](run-windows-powershell-steps-in-sql-server-agent.md)。 不過，從 SQL Server 2019 開始，可停用 SQLPS。 若要這樣做，請在 PowerShell 類型作業步驟的第一行上新增 `#NOSQLPS`，這會阻止 SQL Agent 自動載入 SQLPS 模組。 當這麼做時，SQL Agent 作業會執行電腦上所安裝的 PowerShell 版本，之後就可使用任何其他您喜歡的 PowerShell 模組。

如果想要在 SQL Agent 作業步驟中使用 **SqlServer** 模組，則可將此程式碼放在指令碼的前兩行。

```powershell
#NOSQLPS
Import-Module -Name SqlServer
```

## <a name="cmdlet-reference"></a>Cmdlet 參考

- [SqlServer Cmdlet](/powershell/module/sqlserver)
- [SQLPS Cmdlet](/powershell/module/sqlps)

## <a name="next-steps"></a>後續步驟

- [下載 SQL Server PowerShell 模組](download-sql-server-ps-module.md)
- [SQL Server PowerShell Cmdlet](/powershell/module/sqlserver)
- [使用 PowerShell 搭配 Azure Data Studio](../azure-data-studio/extensions/powershell-extension.md)