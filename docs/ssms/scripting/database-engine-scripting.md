---
title: Database Engine 指令碼
description: 了解如何使用 Microsoft PowerShell 指令碼環境來管理 SQL Server 資料庫引擎的執行個體，以及如何建置及執行包含 Transact-SQL 和 XQuery 的資料庫引擎查詢。
ms.custom: seo-lt-2019
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: sql-tools
ms.technology: ssms
ms.reviewer: ''
ms.topic: conceptual
helpviewer_keywords:
- scripts [SQL Server], PowerShell
- scripts [SQL Server]
- scripting [SQL Server Database Engine]
- scripting [SQL Server Database Engine], PowerShell
ms.assetid: 9978a884-59a2-4e7f-a82a-335149f3a261
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 54297946ffc20ec7699536e5bd5c0504497d1f84
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97642381"
---
# <a name="database-engine-scripting"></a>Database Engine 指令碼
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]
  [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 支援使用 [!INCLUDE[msCoName](../../includes/msconame-md.md)] PowerShell 指令碼環境來管理 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 執行個體和執行個體中的物件。 此外，您也可以在與指令碼環境非常相似的環境中，建立並執行含有 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 和 XQuery 的 [!INCLUDE[tsql](../../includes/tsql-md.md)] 查詢。  
  
## <a name="sql-server-powershell"></a>SQL Server PowerShell  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 包含兩個 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] PowerShell 嵌入式管理單元，可實作：  
  
-   [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] PowerShell 提供者，以便將 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 管理物件模型階層公開成與檔案系統路徑相似的 PowerShell 路徑。 您可以使用 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 管理物件模型類別來管理以路徑之每個節點表示的物件。  
  
-   一組實作 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 命令的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 指令程式。 其中一個 Cmdlet 是 **Invoke-Sqlcmd**。 這個 Cmdlet 可用來執行要與 [!INCLUDE[ssDE](../../includes/ssde-md.md)] sqlcmd **公用程式一起執行的** 查詢指令碼。  
  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 提供了用來執行 PowerShell 的以下功能：  
  
-   可以匯入到 PowerShell 工作階段的 **sqlps** PowerShell 模組，然後此模組會載入 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 嵌入式管理單元。您可以用互動方式執行隨選 PowerShell 命令。 您可以使用 .\MyFolder\MyScript.ps1 等命令來執行指令碼檔案。  
  
-   PowerShell 指令碼檔案可以當做依排程間隔或為了回應系統事件而執行指令碼之 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent PowerShell 作業步驟的輸入使用。  
  
-   啟動 PowerShell 並匯入 **模組的** sqlps [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 公用程式。 然後您可以執行此模組支援的所有動作。 您可以透過命令提示字元，或在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Management Studio 的物件總管樹狀目錄中以滑鼠右鍵按一下節點並選取 [啟動 PowerShell]，啟動 **sqlps** 公用程式。  
  
## <a name="database-engine-queries"></a>Database Engine 查詢  
 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 查詢指令碼包含三種元素：  
  
-   [!INCLUDE[tsql](../../includes/tsql-md.md)] 語言陳述式。  
  
-   XQuery 語言陳述式。  
  
-   **sqlcmd** 公用程式的命令和變數。  
  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 提供了三種建立和執行 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 查詢的環境：  
  
-   在 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 的 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 查詢編輯器中，您可以用互動方式執行並偵錯 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)]查詢。 您可以在單一工作階段中編寫許多陳述式的程式碼並進行偵錯，然後將所有陳述式都儲存在單一指令碼檔案中。  
  
-   **sqlcmd** 命令提示字元公用程式可讓您以互動方式執行 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 查詢，而且也可以執行現有的 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 查詢指令碼檔案。  
  
 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 您通常會使用 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 查詢編輯器，以互動方式在 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 中編寫查詢指令碼檔案的程式碼。 然後，您就可以在下列其中一個環境內開啟此檔案：  
  
-   使用 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 的 [檔案]/[開啟] 功能表，以在新的 [!INCLUDE[ssDE](../../includes/ssde-md.md)] [查詢編輯器] 視窗中開啟此檔案。  
  
-   使用 **-i**_input_file_ 參數搭配 **sqlcmd** 公用程式來執行此檔案。  
  
-   使用 **-QueryFromFile** 參數搭配 **PowerShell 指令碼中的** Invoke-Sqlcmd [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Cmdlet 來執行此檔案。  
  
-   使用 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent [!INCLUDE[tsql](../../includes/tsql-md.md)] 作業步驟，依排程間隔或為了回應系統事件而執行指令碼。  
  
 此外，您可以使用 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] [產生指令碼精靈] 來產生 [!INCLUDE[tsql](../../includes/tsql-md.md)] 指令碼。 您可以在 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 的物件總管中，以滑鼠右鍵按一下物件，然後選取 [產生指令碼] 功能表項目。 [產生指令碼] 就會啟動此精靈，並逐步引導您完成建立指令碼的程序。  
  
## <a name="database-engine-scripting-tasks"></a>Database Engine 指令碼工作  
  
|工作描述|主題|  
|----------------------|-----------|  
|描述如何使用 [!INCLUDE[ssManStudio](../../includes/ssmanstudio-md.md)] 中的程式碼和文字編輯器，以互動方式開發、偵錯和執行 [!INCLUDE[tsql](../../includes/tsql-md.md)] 指令碼。|[查詢與文字編輯器 &#40;SQL Server Management Studio&#41;](../f1-help/database-engine-query-editor-sql-server-management-studio.md)|  
|描述如何使用 **sqlcmd** 公用程式，從命令提示字元執行 [!INCLUDE[tsql](../../includes/tsql-md.md)] 指令碼，包含以互動方式開發指令碼的能力。|[sqlcmd 使用說明主題](./sqlcmd-start-the-utility.md)|  
|描述如何將 SQL Server 元件整合至 Windows PowerShell 環境，然後建立 PowerShell 指令碼以管理 SQL Server 執行個體和物件。|[SQL Server PowerShell](../../powershell/sql-server-powershell.md)|  
|描述如何使用 [產生和發佈指令碼精靈]，建立 [!INCLUDE[tsql](../../includes/tsql-md.md)] 指令碼以重新建立資料庫中的一個或多個物件。|[產生指令碼 &#40;SQL Server Management Studio&#41;](./generate-scripts-sql-server-management-studio.md)|  
  
## <a name="see-also"></a>另請參閱  
 [sqlcmd 公用程式](../../tools/sqlcmd-utility.md)   
 [教學課程：撰寫 Transact-SQL 陳述式](../../t-sql/tutorial-writing-transact-sql-statements.md)  
  
