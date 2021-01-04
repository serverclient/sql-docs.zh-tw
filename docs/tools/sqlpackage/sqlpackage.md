---
title: SqlPackage.exe
description: 了解如何使用 SqlPackage.exe 將資料庫開發工作自動化。 檢視範例及可用的參數、屬性和 SQLCMD 變數。
ms.prod: sql
ms.prod_service: sql-tools
ms.technology: tools-other
ms.topic: conceptual
ms.assetid: 198198e2-7cf4-4a21-bda4-51b36cb4284b
author: dzsquared
ms.author: drskwier
ms.reviewer: maghan; sstein
ms.date: 11/4/2020
ms.openlocfilehash: 16c4200b70647c08ddee6b531acc3227d2942ad2
ms.sourcegitcommit: 866554663ca3191748b6e4eb4d8d82fa58c4e426
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97577802"
---
# <a name="sqlpackageexe"></a>SqlPackage.exe

**SqlPackage.exe** 是命令列公用程式，可自動化下列資料庫開發工作：  
  
- [版本](#version)：傳回 SqlPackage 應用程式的組建編號。  已在 18.6 版中新增。

- [Extract](sqlpackage-extract.md)：從即時的 SQL Server 或 Azure SQL Database 建立資料庫快照集 (.dacpac) 檔案。  
  
- [Publish](sqlpackage-publish.md)：以累加方式更新資料庫結構描述，以符合來源 .dacpac 檔案的結構描述。 如果資料庫不存在伺服器上，發行作業會加以建立。 否則，將更新現有的資料庫。  
  
- [Export](sqlpackage-export.md)：將即時資料庫 (包括資料庫結構描述和使用者資料) 從 SQL Server 或 Azure SQL Database 匯出到 BACPAC 套件 (.bacpac 檔案)。  
  
- [Import](sqlpackage-import.md)：將結構描述和資料表資料從 BACPAC 套件匯入至 SQL Server 或 Azure SQL Database 執行個體中的新使用者資料庫。  
  
- [DeployReport](sqlpackage-deploy-drift-report.md)：建立發行動作所做變更的 XML 報表。  
  
- [DriftReport](sqlpackage-deploy-drift-report.md)：建立自從上次註冊以來已經對註冊資料庫所做變更的 XML 報表。  
  
- [Script](sqlpackage-script.md)：建立 Transact-SQL 累加更新指令碼，這個指令碼會更新目標的結構描述以符合來源的結構描述。  
  
**SqlPackage.exe** 命令列可讓您指定這些動作以及動作特有的參數和屬性。  

**[下載最新版本](sqlpackage-download.md)** 。 如需最新版本的詳細資訊，請參閱[版本資訊](release-notes-sqlpackage.md)。
  
## <a name="command-line-syntax"></a>命令列語法

**SqlPackage.exe** 會使用命令列上指定的參數、屬性和 SQLCMD 變數來起始指定的動作。  
  
```
SqlPackage {parameters}{properties}{SQLCMD Variables}  
```

### <a name="usage-examples"></a>使用範例

**使用 .dacpac 檔案搭配 SQL 指令碼輸出，以產生資料庫之間的比較**

首先，建立最新資料庫變更的 .dacpac 檔案：

```
sqlpackage.exe /TargetFile:"C:\sqlpackageoutput\output_current_version.dacpac" /Action:Extract /SourceServerName:"." /SourceDatabaseName:"Contoso.Database"
 ```
 
建立資料庫目標的 .dacpac 檔案 (沒有任何變更)：

 ```
 sqlpackage.exe /TargetFile:"C:\sqlpackageoutput\output_target.dacpac" /Action:Extract /SourceServerName:"." /SourceDatabaseName:"Contoso.Database"
 ```

建立會產生兩個 .dacpac 檔案差異的 SQL 指令碼：

```
sqlpackage.exe /Action:Script /SourceFile:"C:\sqlpackageoutput\output_current_version.dacpac" /TargetFile:"C:\sqlpackageoutput\output_target.dacpac" /TargetDatabaseName:"Contoso.Database" /OutputPath:"C:\sqlpackageoutput\output.sql"
 ```


## <a name="version"></a>版本

將 sqlpackage 版本顯示為組建編號。  可用於互動式提示及[自動化管線](sqlpackage-pipelines.md)。

```
sqlpackage.exe /Version
 ```


## <a name="exit-codes"></a>結束代碼

傳回下列結束代碼的命令：

- 0 = 成功
- 非零 = 失敗


## <a name="next-steps"></a>後續步驟

- 深入了解 [SqlPackage Extract](sqlpackage-extract.md)
- 深入了解 [SqlPackage Publish](sqlpackage-publish.md)
- 深入了解 [SqlPackage Export](sqlpackage-export.md)
- 深入了解 [SqlPackage Import](sqlpackage-import.md)
