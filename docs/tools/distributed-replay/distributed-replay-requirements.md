---
description: Distributed Replay Requirements
title: Distributed Replay 需求
titleSuffix: SQL Server Distributed Replay
ms.prod: sql
ms.reviewer: ''
ms.technology: tools-other
ms.topic: conceptual
ms.assetid: 6fffee7d-891f-4d9d-b2c3-dd19855a1c2c
author: markingmyname
ms.author: maghan
ms.custom: seo-lt-2019
ms.date: 01/18/2018
ms.openlocfilehash: f03a5bd4c35bc071df184b37e48cd358d62660a6
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98170670"
---
# <a name="distributed-replay-requirements"></a>Distributed Replay Requirements
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
  使用 [!INCLUDE[msCoName](../../includes/msconame-md.md)][!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Distributed Replay 功能之前，請先考慮本主題所列的各項產品需求。  
  
## <a name="input-trace-requirements"></a>輸入追蹤需求  
 為了順利重新執行追蹤資料，它必須符合版本和格式的需求，並且包含必要的事件和資料行。  
  
### <a name="input-trace-versions"></a>輸入追蹤版本  
 Distributed Replay 支援從下列 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]版本收集而來的輸入追蹤資料：  
  
-   [!INCLUDE[ssSQL15](../../includes/sssqlv14-md.md)] 累計更新 1 和更新版本。 請參閱 - [SQL Server 2017 累計更新](https://aka.ms/sql2017cu)。
-   [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)]   
-   [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)]  
-   [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)]  
-   [!INCLUDE[ssKilimanjaro](../../includes/sskilimanjaro-md.md)]  
-   [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)]    
-   [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)]  
  
### <a name="input-trace-formats"></a>輸入追蹤格式  
 輸入追蹤資料可以採用下列任何格式：  
  
-   具有 `.trc` 副檔名的單一追蹤檔案。  
  
-   一組遵循檔案換用命名規範的換用追蹤檔案，例如：`<TraceFile>.trc`、`<TraceFile>_1.trc`、`<TraceFile>_2.trc`、`<TraceFile>_3.trc`... `<TraceFile>_n.trc`。  
  
### <a name="input-trace-events-and-columns"></a>輸入追蹤事件和資料行  
 輸入追蹤資料必須包含特定的事件及資料行，如此 Distributed Replay 才可重新執行。 **中的** TSQL_Replay [!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)] 範本包含所有必要的事件和資料，以及額外的資訊。 如需該範本的詳細資訊，請參閱 [重新執行需求](../../tools/sql-server-profiler/replay-requirements.md)。  
  
> [!WARNING]  
>  若您未使用 **TSQL_Replay** 範本來擷取輸入追蹤資料，或不符合輸入追蹤資料的需求，可能會收到未預期的重新執行結果。  
  
 您也可以建立自訂追蹤範本，並使用該範本搭配 Distributed Replay 重新執行事件，但該範本必須包含下列事件：  
  
-   稽核登入  
  
-   稽核登出  
  
-   ExistingConnection  
  
-   RPC Output Parameter  
  
-   RPC:Completed  
  
-   RPC:Starting  
  
-   SQL:BatchCompleted  
  
-   SQL:BatchStarting  
  
 如果您要重新執行伺服器端資料指標，下列事件也是必要的：  
  
-   CursorClose  
  
-   CursorExecute  
  
-   CursorOpen  
  
-   CursorPrepare  
  
-   CursorUnprepare  
  
 如果您要重新執行伺服端 SQL 準備陳述式，下列事件也是必要的：  
  
-   Exec Prepared SQL  
  
-   Prepare SQL  
  
 所有輸入追蹤資料都必須包含下列資料行：  
  
-   Event Class  
  
-   EventSequence  
  
-   TextData  
  
-   應用程式名稱  
  
-   LoginName  
  
-   DatabaseName  
  
-   資料庫識別碼  
  
-   HostName  
  
-   Binary Data  
  
-   SPID  
  
-   開始時間  
  
-   EndTime  
  
-   IsSystem  
  
## <a name="supported-input-trace-and-target-server-combinations"></a>支援的輸入追蹤與目標伺服器組合  
 下表將列出支援的追蹤資料版本，以及對於每個版本而言，可用來重新執行資料的支援 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 版本。  
  
|輸入追蹤資料的版本|目標伺服器執行個體的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 支援版本|  
|---------------------------------|------------------------------------------------------------------------------------|  
|[!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)]|[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)]、[!INCLUDE[ssKilimanjaro](../../includes/sskilimanjaro-md.md)]、[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)]、[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)]、[!INCLUDE[ssSQL15](../../includes/sssql16-md.md)]、[!INCLUDE[ssSQL17](../../includes/sssql17-md.md)]|  
|[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)]|[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)], [!INCLUDE[ssKilimanjaro](../../includes/sskilimanjaro-md.md)], [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)], [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)], [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)], [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)]|  
|[!INCLUDE[ssKilimanjaro](../../includes/sskilimanjaro-md.md)]|[!INCLUDE[ssKilimanjaro](../../includes/sskilimanjaro-md.md)], [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)], [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)], [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)], [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)]|  
|[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)]|[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)], [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)],[!INCLUDE[ssSQL15](../../includes/sssql16-md.md)], [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)]|  
|[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)]|[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)], [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)], [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)]|  
|[!INCLUDE[ssSQL15](../../includes/sssql16-md.md)]|[!INCLUDE[ssSQL15](../../includes/sssql16-md.md)], [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)]|  
|[!INCLUDE[ssSQL17](../../includes/sssql17-md.md)]|[!INCLUDE[ssSQL17](../../includes/sssql17-md.md)]|  
  
## <a name="operating-system-requirements"></a>作業系統需求  
 支援執行管理工具以及控制器和用戶端服務的作業系統，與 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體相同。 如需您 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體的支援作業系統詳細資訊，請參閱 [安裝 SQL Server 2016 的硬體與軟體需求](../../sql-server/install/hardware-and-software-requirements-for-installing-sql-server.md)。  
  
 x86 及 x64 作業系統皆支援 Distributed Replay 功能。 若為 x64 架構作業系統，只支援 Windows on Windows (WOW) 模式。  
  
## <a name="installation-limitations"></a>安裝限制  
 對於各項 Distributed Replay 功能，每部電腦皆只能夠安裝一個執行個體。 下表列出單一 Distributed Replay 環境中對於各項功能所能夠安裝的數量。  
  
|Distributed Replay 功能|每個重新執行環境的最大安裝數目|  
|--------------------------------|--------------------------------------------------|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Distributed Replay Controller 服務|1|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Distributed Replay Client 服務|16 (實體或虛擬電腦)|  
|管理工具|無限制|  
  
> [!NOTE]  
>  雖然單一電腦只能安裝一個管理工具的執行個體，不過您可以啟動多個管理工具的執行個體。 從多個管理工具發出的命令會按照系統接收的順序來解析。  
  
## <a name="data-access-provider"></a>資料存取提供者  
 Distributed Replay 只支援 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client ODBC 資料存取提供者。  
  
## <a name="target-server-preparation-requirements"></a>目標伺服器準備需求  
 我們建議您將目標伺服器放置於測試環境中。 若要針對與原始記錄不同的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體重新執行追蹤資料，請確定已經對目標伺服器完成下列步驟：  
  
-   追蹤資料中包含的所有登入與使用者都必須存在目標伺服器的相同資料庫中。  
  
-   目標伺服器上的所有登入與使用者，其權限必須與在原始伺服器上擁有的權限相同。  
  
-   目標上的資料庫識別碼必須與來源上的一樣。 不過，如果不相同，若追蹤內有 **DatabaseName** 的話， 就可以據以執行比對作業。  
  
-   追蹤資料中包含之每個登入的預設資料庫必須設成 (在目標伺服器上) 登入的個別目標資料庫。 例如，要重新執行的追蹤資料包含 **Fred** 登入的活動，其位於原始 **執行個體的** Fred_Db [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]資料庫中。 因此，在目標伺服器上， **Fred** 登入的預設資料庫必須設成符合 **Fred_Db** 的資料庫 (即使資料庫名稱不同)。 若要設定登入的預設資料庫，可使用 `sp_defaultdb` 系統預存程序。  
  
 重新執行與找不到或不正確之登入相關的事件，會造成重新執行錯誤，但重新執行作業仍會繼續。  
  
## <a name="see-also"></a>另請參閱  
 [SQL Server Distributed Replay](../../tools/distributed-replay/sql-server-distributed-replay.md)   
 [Distributed Replay 安全性](../../tools/distributed-replay/distributed-replay-security.md)   
 [安裝 Distributed Replay - 概觀](../../tools/distributed-replay/install-distributed-replay-overview.md)  
  
  
