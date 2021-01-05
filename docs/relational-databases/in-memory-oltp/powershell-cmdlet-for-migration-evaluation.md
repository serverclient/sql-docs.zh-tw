---
title: 用於移轉評估的 PowerShell Cmdlet | Microsoft Docs
description: 了解 Save-SqlMigrationReport，其會針對記憶體內部 OLTP 評估 SQL Server 資料庫中物件的移轉適用性。
ms.custom: ''
ms.date: 07/30/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: in-memory-oltp
ms.topic: conceptual
ms.assetid: 117250d3-9982-47fe-94fd-6f29f6159940
author: MightyPen
ms.author: genemi
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 78b4d84a98d7ea2c753a673a222a1a19e0b7e5e2
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97641266"
---
# <a name="powershell-cmdlet-for-migration-evaluation"></a>用於移轉評估的 PowerShell Cmdlet

[!INCLUDE [SQL Server Azure SQL Database](../../includes/applies-to-version/sql-asdb.md)]

`Save-SqlMigrationReport` Cmdlet 是一種工具，可評估 SQL Server 資料庫中多個物件的移轉適用性。

目前，此 Cmdlet 僅限於評估記憶體內部 OLTP 的移轉適用性。 您可在提高權限的 Windows PowerShell 環境和 sqlps 中執行此 Cmdlet。

除了直接執行此 PowerShell Cmdlet 外，您也可以使用 SQL Server Management Studio (SSMS) 來隱含執行 Cmdlet。 在 SSMS [物件總管] 中，您可以滑鼠右鍵按一下資料表，然後按一下 [記憶體最佳化建議程式]。

## <a name="syntax"></a>語法

```
Save-SqlMigrationReport
    -FolderPath <output_path>
    [ -MigrationType <migration_scenario_type> ]
    [
        [ -Server <server_name> -Database <database_name>
            [ -Schema <schema_name> ] [ -Object <object_name> ]
        ]
       |
        [ -InputObject <smo_object> ]
    ]
;
```

## <a name="parameters"></a>參數

下表會描述參數。

其中一些應進行強調的語法層面。 若您指定 `-InputObject` 參數，您便無法指定下列任何一個參數：

- `-Server`
- `-Database`
- `-Schema`
- `-Object`

相反的，若您「沒有」指定 `-InputObject` 參數，您便必須指定 `-Server` 和 `-Database`。 若您指定 `-Server`，您可以選擇指定 `-Schema` 或 `-Object`，或同時指定兩者來縮小範圍。

| 參數名稱 | 描述 |
| :------------- | :---------- |
| 資料庫 | 目標 SQL Server 資料庫的名稱。 當 `-Server` 為強制時，此為強制項目。<br/><br/> 在 SQLPS 中為選擇性參數。 |
| FolderPath | Cmdlet 應於其下儲存所產生報告的資料夾。<br/><br/> 必要。 |
| InputObject | Cmdlet 的目標 SMO 物件。<br/><br/> 若沒有提供 `-Server`，則在 Windows Powershell 環境中為強制項目。<br/><br/> 在 SQLPS 中為選擇性參數。 |
| MigrationType | Cmdlet 的目標移轉案例類型。 目前唯一的值是預設 **'OLTP'** 。<br/><br/> 選擇性。 |
| Object | 要報告的物件名稱。 可以是資料表或預存程序。 |
| 密碼 | 當 `-Username` 為必要時，此為必要項目。 |
| 結構描述 | 擁有要報告物件的結構描述名稱。<br/><br/> 選擇性。 |
| 伺服器 | 目標 SQL Server 執行個體的名稱。 若沒有提供 `-InputObject` 參數，則在 Windows Powershell 環境中為強制項目。<br/><br/> 在 SQLPS 中為選擇性參數。 |
| 使用者名稱 | 透過 SQL Server 驗證而非透過 Windows 驗證進行連線時，此為必要項目。 否則請省略它。 |
| &nbsp; | &nbsp; |

## <a name="prerequisites"></a>必要條件

在執行此 Cmdlet 前，您必須先安裝名為 **SqlServer** 的模組：

- `Install-Module -Name SqlServer`

> [!NOTE]
> 舊的 `SQLPS` 模組已不再受到維護。 請使用較新的 `SqlServer` 模組。

如需詳細資訊，請參閱[安裝 SQL Server PowerShell 模組](../../powershell/download-sql-server-ps-module.md)。

## <a name="example-cmdlet-line"></a>範例 Cmdlet 列

以下是所執行的實際 Cmdlet 列，用來產生本文稍後顯示的報告。

```powershell
Save-SqlMigrationReport `
  -FolderPath 'C:\Test\PowerShell-ps1\Save-SqlMigrationReport\' `
  -Server 'MyUserName123456.database.windows.net' `
  -Database 'MyDatabaseName_31' `
  -Schema 'dbo' `
  -Object 'Table2' `
  -Username 'MyUserName' `
  -Password 'MyPassword' `
  -MigrationType 'OLTP' `
;
```

## <a name="example-output-report"></a>範例輸出報告

在針對 `-FolderPath` 參數指定的資料底下，會執行此 Cmdlet 來建立下列兩個資料夾路徑。 兩個路徑的開頭都是 _server\_name_ 值：

- MyDatabaseName_31\Tables\
- MyDatabaseName_31\Stored Procedures\

每個物件報告檔案都會儲存在適當的資料夾底下。

報告的檔案名稱副檔名為 **.html**。 例如，一個實際產生的檔案名稱為 **MigrationAdvisorChecklistReport_Table2_20190728.html**。

HTML 大多數是包含下列標頭的兩欄式表格：

- 描述
- 驗證結果

以下是一個資料表的 HTML 報告實際範例。

```html
<?xml version="1.0" encoding="utf-8"?>
<html>
  <head>
    <title>Memory optimization checklist for [MyDatabaseName_31].[Table2]</title>
  </head>
  <body>
    <p STYLE="font-family: Verdana, Arial, sans-serif; font-size: 14pt;">
      <b>Memory optimization checklist for [MyDatabaseName_31].[Table2]</b>
    </p>
    <p STYLE="font-family: Verdana, Arial, sans-serif; font-size: 10pt;">
      <b>Report Date/Time:</b>7/28/2019 2:25 PM<br /></p>
    <table border="1" cellpadding="5" cellspacing="0" STYLE="font-family: Verdana, Arial, sans-serif; font-size: 9pt;">
      <tr style="background-color:Silver">
        <th colspan="2" align="center">Description</th>
        <th align="center">Validation Result</th>
      </tr>
      <tr valign="top">
        <td colspan="2">No unsupported data types are defined on this table. </td>
        <td>Succeeded</td>
      </tr>
      <tr valign="top" style="background-color:LightYellow">
        <td colspan="2">No sparse columns are defined for this table.</td>
        <td>Succeeded</td>
      </tr>
      <tr valign="top">
        <td colspan="2">No identity columns with unsupported seed and increment are defined for this table.</td>
        <td>Succeeded</td>
      </tr>
      <tr valign="top" style="background-color:LightYellow">
        <td colspan="2">No foreign key relationships are defined on this table.</td>
        <td>Succeeded</td>
      </tr>
      <tr valign="top">
        <td colspan="2">No unsupported constraints are defined on this table.</td>
        <td>Succeeded</td>
      </tr>
      <tr valign="top" style="background-color:LightYellow">
        <td colspan="2">No unsupported indexes are defined on this table.</td>
        <td>Succeeded</td>
      </tr>
      <tr valign="top">
        <td colspan="2">No unsupported triggers are defined on this table.</td>
        <td>Succeeded</td>
      </tr>
      <tr valign="top" style="background-color:LightYellow">
        <td colspan="2">Post migration row size does not exceed the row size limit of memory-optimized tables.</td>
        <td>Succeeded</td>
      </tr>
      <tr valign="top">
        <td colspan="2">Table is not partitioned or replicated.</td>
        <td>Succeeded</td>
      </tr>
    </table>
  </body>
</html>
```

接下來則是表格的近似外觀。

| 描述 | 驗證結果 |
| :---------- | :---------------- |
| 此資料表上沒有定義任何不受支援的資料類型。 | 成功 |
| 此資料表沒有定義任何疏鬆資料行。 | 成功 |
| 此資料表沒有定義任何包含未受支援種子及遞增的識別欄位。 | 成功 |
| 此資料表上沒有定義任何外部索引鍵關聯性。 | 成功 |
| 此資料表上沒有定義任何不受支援的條件約束。 | 成功 |
| 此資料表上沒有定義任何不受支援的索引。 | 成功 |
| 此資料表上沒有定義任何不受支援的觸發程序。 | 成功 |
| 移轉後資料列大小未超過經記憶體最佳化資料表的資料列大小限制。 | 成功 |
| 資料表未分割或已複寫。 | 成功 |
| &nbsp; | &nbsp; |

## <a name="related-links"></a>相關連結

- 參考文件：[Save-SqlMigrationReport](/powershell/module/sqlserver/save-sqlmigrationreport)
