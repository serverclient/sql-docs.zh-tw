---
title: 啟動、停止、暫停、繼續、重新啟動 SQL Server 服務
description: 了解如何啟動、停止、暫停、繼續或重新啟動各種 SQL Server 服務。 了解如何使用 Transact-SQL、PowerShell 和其他工具來進行這些動作。
ms.custom: ''
ms.date: 03/05/2020
ms.prod: sql
ms.prod_service: high-availability
ms.technology: configuration
ms.topic: conceptual
helpviewer_keywords:
- SQL Server Configuration Manager, start and stop services
- stopping SQL Server Agent
- parameters [SQL Server], startup options
- SQL Server, startup options
- Database Engine [SQL Server], starting and stopping services
- pausing SQL Server
- PowerShell [SQL Server], starting and stopping services
- single-user mode [SQL Server], starting in
- SQL Server Management Studio [SQL Server], starting or stopping services
- stopping SQL Server Browser service
- starting SQL Server Agent
- default instances [SQL Server], starting and stopping
- SQL Server Agent, starting and stopping
- command prompt [SQL Server], starting and stopping SQL Server services
- continuing SQL Server
- starting SQL Server Database Engine
- net stop commands [SQL Server]
- command prompt [SQL Server], SQL Browser service
- Configuration Manager, start and stop services
- resuming SQL Server
- startup options [SQL Server]
- named instances [SQL Server], starting and stopping
- net start commands [SQL Server]
- SQL Server, starting and stopping
- stopping SQL Server
- starting SQL Server Browser service
- SQL Server Database Engine setting startup options
- administering SQL Server, starting and stopping services
- Management Studio [SQL Server], starting or stopping services
ms.assetid: 32660a02-e5a1-411a-9e57-7066ca459df6
author: markingmyname
ms.author: maghan
ms.reviewer: ''
ms.openlocfilehash: 697cf043c4582796015b061c857145c214d105fb
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98091739"
---
# <a name="start-stop-pause-resume-restart-sql-server-services"></a>啟動、停止、暫停、繼續、重新啟動 SQL Server 服務

 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

本主題描述如何使用 SQL Server 組態管理員、SQL Server Management Studio (SSMS)、命令提示字元的 net 命令、Transact-SQL 或 PowerShell 啟動、停止、暫停、繼續或重新啟動 SQL Server 資料庫引擎、SQL Server Agent，或 SQL Server Browser 服務。

## <a name="identify-the-service"></a>識別服務

SQL Server 元件是當作 Windows 服務執行的可執行程式。 當做 Windows 服務執行的程式可以繼續操作，而不會在電腦螢幕上顯示任何活動。

### <a name="database-engine-service"></a>Database Engine 服務

SQL Server 資料庫引擎的可執行執行序。 資料庫引擎可以是預設執行個體 (每部電腦只限一個執行個體)，或可以是許多具名資料庫引擎執行個體的其中一個。 使用 SQL Server 組態管理員來判斷哪些資料庫引擎執行個體會安裝在電腦上。 預設執行個體 (如果安裝的話) 會列為 **SQL Server (MSSQLSERVER)** 。 具名執行個體 (如果安裝的話) 會列為 **SQL Server (<執行個體名稱>)** 。 根據預設，SQL Server Express 會安裝為 **SQL Server (SQLEXPRESS)** 。

### <a name="sql-server-agent-service"></a>SQL Server Agent 服務

一種 Windows 服務，它會執行排程的管理工作 (稱為作業和警示)。 如需詳細資訊，請參閱 [SQL Server Agent](../../ssms/agent/sql-server-agent.md)。 並非所有 SQL Server 版本都可使用 SQL Server Agent。 如需 SQL Server 版本支援的功能清單，請參閱 [SQL Server 2019 版本支援的功能](../../sql-server/editions-and-components-of-sql-server-version-15.md)。

### <a name="sql-server-browser-service"></a>SQL Server Browser 服務

Windows 服務，其會接聽傳入要求以找出 SQL Server 資源，並提供電腦上所安裝 SQL Server 執行個體的用戶端資訊。 單一 SQL Server Browser 服務執行個體會用於電腦上所安裝的所有 SQL Server 執行個體。

### <a name="additional-information"></a>其他資訊

- 暫停資料庫引擎服務會阻止新的使用者連線到資料庫引擎，但是已連線的使用者可以繼續工作，直到連線中斷為止。 當您希望在停止服務之前等待使用者完成工作時，請使用暫停。 這樣可讓他們完成正在進行的交易。 「繼續」可讓資料庫引擎再次接受新的連線。 SQL Server Agent 服務無法暫停或繼續。  

- SQL Server 組態管理員和 SSMS 會使用下列圖示來顯示目前的服務狀態。  

    **SQL Server 組態管理員**

  - 服務名稱旁圖示上的綠色箭頭，表示服務已啟動。

  - 服務名稱旁圖示上的紅色方塊，表示服務已停止。

  - 服務名稱旁圖示上的兩條垂直藍線表示服務已暫停。

  - 重新啟動資料庫引擎時，紅色方塊表示服務已停止，綠色箭頭則表示服務已順利啟動。

    **SQL Server Management Studio (SSMS)**

  - 服務名稱旁綠色圓形圖示上的白色箭頭，表示服務已啟動。  

  - 服務名稱旁紅色圓形圖示上的白色方塊，表示服務已停止。  

  - 服務名稱旁藍色圓形圖示上的兩條垂直白線表示服務已暫停。  

- 當使用 SQL Server 組態管理員或 SSMS 時，只能使用可能的選項。 例如，如果此服務已啟動，則無法使用 [啟動]。

- 在叢集上執行時，使用叢集管理員來管理 SQL Server 資料庫引擎服務的效果最佳。  

### <a name="security"></a>安全性

#### <a name="permissions"></a>權限

根據預設，只有本機系統管理員群組的成員能夠啟動、停止、暫停、繼續或重新啟動服務。 若要將管理服務的能力授與非系統管理員，請參閱 [How to grant users rights to manage services in Windows Server 2003](https://support.microsoft.com/kb/325349)(如何在 Windows Server 2003 中，將管理服務的權限授與使用者)。 (其他 Windows Server 版本的流程都很相似。)

使用 Transact-SQL **SHUTDOWN** 命令停止資料庫引擎，需要 **sysadmin** 或 **serveradmin** 固定伺服器角色的成員資格，且無法移轉。

## <a name="sql-server-configuration-manager"></a>SQL Server 組態管理員

### <a name="starting-sql-server-configuration-manager"></a>啟動 SQL Server 組態管理員

在 **[開始]** 功能表上，依序指向 **[所有程式]** 、 **[Microsoft SQL Server]** 和 **[組態工具]** ，然後按一下 **[SQL Server 組態管理員]** 。

因為 SQL Server 組態管理員是 Microsoft 管理主控台程式的嵌入式管理單元，而不是獨立的程式，所以 SQL Server 組態管理員在較新版本的 Windows 中不會作為應用程式出現。 以下是 Windows 安裝在 C 磁碟機時，四個最新版本的路徑。  

|版本|Path|
|-|-|
|SQL Server 2019|C:\Windows\SysWOW64\SQLServerManager15.msc|
|SQL Server 2017|C:\Windows\SysWOW64\SQLServerManager14.msc|
|SQL Server 2016|C:\Windows\SysWOW64\SQLServerManager13.msc|
|SQL Server 2014|C:\Windows\SysWOW64\SQLServerManager12.msc|
|SQL Server 2012|C:\Windows\SysWOW64\SQLServerManager11.msc|

#### <a name="to-start-stop-pause-resume-or-restart-an-instance-of-the-sql-server-database-engine"></a><a name="configmande"></a> 啟動、停止、暫停、繼續或重新啟動 SQL Server 資料庫引擎的執行個體

1. 使用上述指示啟動 SQL Server 組態管理員。

2. 如果出現 **[使用者帳戶控制]** 對話方塊，請按一下 **[是]** 。

3. 在 SQL Server 組態管理員中，按一下位於左側窗格中的 [SQL Server 服務]。

4. 在結果窗格中，以滑鼠右鍵按一下 [SQL Server (MSSQLServer)] 或具名執行個體，然後按一下 [啟動]、[停止]、[暫停]、[繼續] 或 [重新啟動]。

5. 按一下 [確定] 來關閉 SQL Server 組態管理員。

> [!NOTE]
> 若要使用啟動選項啟動 SQL Server 資料庫引擎的執行個體，請參閱[設定伺服器啟動選項 &#40;SQL Server 組態管理員&#41;](../../database-engine/configure-windows/scm-services-configure-server-startup-options.md)。  

#### <a name="to-start-stop-pause-resume-or-restart-the-sql-server-browser-or-an-instance-of-the-sql-server-agent"></a>啟動、停止、暫停、繼續或重新啟動 SQL Server Browser 或 SQL Server Agent 的執行個體

1. 使用上述指示啟動 SQL Server 組態管理員。

2. 如果出現 **[使用者帳戶控制]** 對話方塊，請按一下 **[是]** 。

3. 在 SQL Server 組態管理員中，按一下位於左側窗格中的 [SQL Server 服務]。

4. 在結果窗格中，以滑鼠右鍵按一下 [SQL Server Browser] 或具名執行個體的 [SQL Server Agent (MSSQLServer)] 或 [SQL Server Agent (<執行個體名稱>)]，然後按一下 [啟動]、[停止]、[暫停]、[繼續] 或 [重新啟動]。  

5. 按一下 [確定] 以關閉 SQL Server 組態管理員。  

> [!NOTE]
> SQL Server Agent 無法暫停。

## <a name="sql-server-management-studio"></a>SQL Server Management Studio

### <a name="to-start-stop-pause-resume-or-restart-an-instance-of-the-sql-server-database-engine"></a><a name="ssmsde"></a> 啟動、停止、暫停、繼續或重新啟動 SQL Server 資料庫引擎的執行個體

1. 在 [物件總管] 中，連線至資料庫引擎的執行個體，並以滑鼠右鍵按一下所要啟動的資料庫引擎執行個體，然後按一下 [啟動]、[停止]、[暫停]、[繼續] 或 [重新啟動]。

    或者，在 [已註冊的伺服器] 中，以滑鼠右鍵按一下所要啟動的資料庫引擎執行個體、並指向 [服務控制]，然後按一下 [啟動]、[停止]、[暫停]、[繼續] 或 [重新啟動]。

2. 如果出現 **[使用者帳戶控制]** 對話方塊，請按一下 **[是]** 。

3. 當系統提示是否要採取行動時，請按一下 [是]。  

#### <a name="to-start-stop-or-restart-an-instance-of-the-sql-server-agent"></a>啟動、停止或重新啟動 SQL Server Agent 的執行個體

1. 在 [物件總管] 中，連線到資料庫引擎的執行個體，並以滑鼠右鍵按一下 [SQL Server Agent]，然後按一下 [啟動]、[停止] 或 [重新啟動]。

2. 如果出現 **[使用者帳戶控制]** 對話方塊，請按一下 **[是]** 。

3. 當系統提示是否要採取行動時，請按一下 [是]。

## <a name="command-prompt-window-using-net-commands"></a><a name="CommandPrompt"></a> 從命令提示字元視窗使用 net 命令

您可使用 Microsoft Windows **net** 命令來啟動、停止或暫停 Microsoft SQL Server 服務。

### <a name="to-start-the-default-instance-of-the-database-engine"></a><a name="dbDefault"></a> 啟動資料庫引擎的預設執行個體

- 從命令提示字元，輸入下列其中一個命令：  
  
    **net start "SQL Server (MSSQLSERVER)"**

   -或-  

    **net start MSSQLSERVER**

### <a name="to-start-a-named-instance-of-the-database-engine"></a><a name="dbNamed"></a> 啟動資料庫引擎的具名執行個體

- 從命令提示字元，輸入下列其中一個命令。 以想要管理執行個體的名稱來取代 *\<instancename>* 。  
  
    **net start "SQL Server (** *instancename* **)"**
  
   -或-  
  
    **net start MSSQL$** *instancename*  
  
### <a name="to-start-the-database-engine-with-startup-options"></a><a name="dbStartup"></a> 使用啟動選項來啟動資料庫引擎  

- 將啟動選項加入 **net start "SQL Server (MSSQLSERVER)"** 陳述式結尾，並間隔一個空格。 使用 **net start** 啟動時，啟動選項會使用斜線 (/)，而非連字號 (-)。  
  
    **net start "SQL Server (MSSQLSERVER)" /f /m**
  
   -或-  
  
    **net start MSSQLSERVER /f /m**
  
  > [!NOTE]
  >  如需啟動選項的詳細資訊，請參閱 [Database Engine 服務啟動選項](../../database-engine/configure-windows/database-engine-service-startup-options.md)。  
  
###  <a name="to-start-the-sql-server-agent-on-the-default-instance-of-sql-server"></a><a name="agDefault"></a> 在 SQL Server 預設執行個體上啟動 SQL Server Agent
  
- 從命令提示字元，輸入下列其中一個命令：  
  
    **net start "SQL Server Agent (MSSQLSERVER)"**
  
   -或-  
  
    **net start SQLSERVERAGENT**
  
###  <a name="to-start-the-sql-server-agent-on-a-named-instance-of-sql-server"></a><a name="agNamed"></a> 在 SQL Server 具名執行個體上啟動 SQL Server Agent  
  
- 從命令提示字元，輸入下列其中一個命令。 以您要管理之執行個體的名稱取代 *執行個體名稱* 。  
  
    **net start "SQL Server Agent(** *instancename* **)"**
  
   -或-  
  
    **net start SQLAgent$** *instancename*  
  
 如需如何以詳細資訊模式執行 SQL Server Agent 來進行疑難排解的資訊，請參閱 [sqlagent90 應用程式](../../tools/sqlagent90-application.md)。  

### <a name="to-start-the-sql-server-browser"></a><a name="Browser"></a> 啟動 SQL Server Browser  

- 從命令提示字元，輸入下列其中一個命令：  
  
    **net start "SQL Server Browser"**
  
   -或-  
  
    **net start SQLBrowser**
  
### <a name="to-pause-or-stop-services-from-the-command-prompt-window"></a><a name="pauseStop"></a> 若要從命令提示字元視窗暫停或停止服務  

- 若要暫停或停止服務，請使用下列方式來修改命令。  

- 若要暫停服務，請以 **net pause** 取代 **net start**。  

- 若要停止服務，請以 **net stop** 取代 **net start**。  

## <a name="transact-sql"></a><a name="TsqlProcedure"></a> Transact-SQL

您可使用 **SHUTDOWN** 陳述式來停止資料庫引擎。  

### <a name="to-stop-the-database-engine-using-transact-sql"></a>使用 Transact-SQL 停止資料庫引擎

- 若要等待目前正在執行的 Transact-SQL 陳述式和預存程序完成，然後停止資料庫引擎，請執行下列陳述式。  
  
    ```sql
    SHUTDOWN;
    ```
  
- 若要立即停止資料庫引擎，請執行下列陳述式。  
  
    ```sql
    SHUTDOWN WITH NOWAIT;
    ```

如需 **SHUTDOWN** 陳述式的詳細資訊，請參閱 [SHUTDOWN &#40;Transact-SQL&#41;](../../t-sql/language-elements/shutdown-transact-sql.md)。  

## <a name="powershell"></a><a name="PowerShellProcedure"></a> PowerShell

### <a name="to-start-and-stop-database-engine-services"></a>啟動和停止資料庫引擎服務

1. 在 [命令提示字元] 視窗中，執行下列命令來啟動 SQL Server PowerShell。  

    ```cmd
    sqlps
    ```

2. 在 SQL Server PowerShell 命令提示字元中，執行下列命令。 以電腦的名稱取代 `computername` 。  

    ```powershell
    # Get a reference to the ManagedComputer class.
    CD SQLSERVER:\SQL\computername
    $Wmi = (get-item .).ManagedComputer
    ```

3. 識別您想要停止或啟動的服務。 挑選下列其中一行。 使用具名執行個體的名稱取代 `instancename` 。

    - 取得資料庫引擎預設執行個體的參考。

        ```powershell
        $DfltInstance = $Wmi.Services['MSSQLSERVER']
        ```

    - 取得資料庫引擎具名執行個體的參考。

        ```powershell
        $DfltInstance = $Wmi.Services['MSSQL$instancename']
        ```

    - 取得資料庫引擎預設執行個體上 SQL Server Agent 服務的參考。  

        ```powershell
        $DfltInstance = $Wmi.Services['SQLSERVERAGENT']
        ```

    - 取得資料庫引擎具名執行個體上 SQL Server Agent 服務的參考。  

        ```powershell
        $DfltInstance = $Wmi.Services['SQLAGENT$instancename']
        ```

    - 取得 SQL Server Browser 服務的參考。

        ```powershell
        $DfltInstance = $Wmi.Services['SQLBROWSER']
        ```

4. 完成範例，以啟動然後停止選取的服務。
  
    ```powershell
    # Display the state of the service.
    $DfltInstance
    # Start the service.
    $DfltInstance.Start();
    # Wait until the service has time to start.
    # Refresh the cache.  
    $DfltInstance.Refresh();
    # Display the state of the service.
    $DfltInstance
    # Stop the service.
    $DfltInstance.Stop();
    # Wait until the service has time to stop.
    # Refresh the cache.
    $DfltInstance.Refresh();
    # Display the state of the service.
    $DfltInstance
    ```  
  
##  <a name="using-service-controller-class"></a><a name="ServiceController"></a> 使用服務控制站類別

您可以使用 ServiceController 類別來控制 SQL 伺服器服務或任何其他 Windows 服務。 如需如何執行此操作的範例，請參閱 [ServiceController 類別](/dotnet/api/system.serviceprocess.servicecontroller) \(部分機器翻譯\)。

## <a name="manage-the-sql-server-service-on-linux"></a>管理 Linux 上的 SQL Server 服務

### <a name="to-start-stop-or-restart-an-instance-of-the-sql-server-database-engine"></a>啟動、停止或重新啟動 SQL Server 資料庫引擎的執行個體

下列示範如何啟動、停止、重新啟動及檢查 [Linux 上 SQL Server 服務](../../linux/sql-server-linux-troubleshooting-guide.md#manage-the-sql-server-service)的狀態。

使用此命令來檢查 SQL Server 服務的狀態：

   ```bash
   sudo systemctl status mssql-server
   ```

您可以使用下列命令，視需要停止、啟動或重新啟動 SQL Server 服務：

   ```bash
   sudo systemctl stop mssql-server
   sudo systemctl start mssql-server
   sudo systemctl restart mssql-server
   ```

## <a name="next-steps"></a>後續步驟

- [SQL Server 安裝程式文件概觀](../install-windows/install-sql-server.md)
- [檢視與讀取 SQL Server 安裝程式記錄檔](../../database-engine/install-windows/view-and-read-sql-server-setup-log-files.md)
- [SQL Server 組態管理員](../../relational-databases/sql-server-configuration-manager.md)
- [以最低組態啟動 SQL Server](../../database-engine/configure-windows/start-sql-server-with-minimal-configuration.md)
- [SQL Server 2016 的版本所支援的功能](~/sql-server/editions-and-supported-features-for-sql-server-2016.md)