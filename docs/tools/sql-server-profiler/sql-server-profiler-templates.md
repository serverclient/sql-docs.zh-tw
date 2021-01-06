---
title: 範本
titleSuffix: SQl Server Profiler
description: 了解 SQL Server Profiler 提供的預先定義範本及其使用方式。 了解如何建立使用者定義的範本，以及變更預設範本。
ms.prod: sql
ms.prod_service: sql-tools
ms.reviewer: ''
ms.technology: profiler
ms.topic: conceptual
ms.assetid: b674e491-dc58-47a1-acdd-7028e9a201fc
author: markingmyname
ms.author: maghan
ms.custom: seo-lt-2019
ms.date: 03/14/2017
ms.openlocfilehash: b29ba66b9cf0e05f1585ff4f37723cf8fffba669
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97643371"
---
# <a name="sql-server-profiler-templates"></a>SQL Server Profiler 範本

 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

您可以使用 [!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)] 建立範本，用來定義追蹤中要包含的事件類別和資料行。 定義並儲存範本之後，即可執行追蹤，記錄您選取的每一個事件類別的資料。 您可將範本用在許多追蹤上；範本本身並不會執行。  

[!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)] 提供預先定義的追蹤範本，可讓您輕鬆設定在特定追蹤上最需要的事件類別。 例如，Standard 範本可協助您建立用於記錄登入、登出、已完成批次及連接資訊的一般追蹤。 此範本不需修改即可用來執行追蹤，或者也可以做為範本建立起點，用來建立具有不同事件組態的其他範本。  
  
> [!NOTE]  
>  除了從預先定義的範本建立追蹤， [!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)] 也可讓您從空白範本 (依預設不含任何事件類別) 建立追蹤。 當計畫中的追蹤與任何預先定義範本的組態都不相似時，使用空白追蹤範本就很有用。  
  
 [!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)] 可以追蹤許多伺服器類型。 例如，您可以追蹤 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] 和 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]。  不過，每一種伺服器可以包含的事件類別都不相同。 因此， [!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)] 針對不同的伺服器維護不同的範本，並提供符合選定伺服器類型的特定範本。  
  
## <a name="predefined-templates"></a>預先定義的範本  
 除了 Standard (預設) 範本， [!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)] 還提供幾個預先定義的範本，可監視特定類型的事件。 下表列出預先定義的範本、用途及其針對哪些事件類別來擷取資訊。  
  
|範本名稱|範本用途|事件類別|  
|-------------------|----------------------|-------------------|  
|SP_Counts|擷取經過一段時間的預存程序執行行為。|**SP:Starting**|  
|標準|建立追蹤的一般起點。 擷取已執行的所有預存程序和 [!INCLUDE[tsql](../../includes/tsql-md.md)] 批次。 用來監視一般性的資料庫伺服器活動。|**稽核登入**<br /><br /> **稽核登出**<br /><br /> **ExistingConnection**<br /><br /> **RPC:Completed**<br /><br /> **SQL:BatchCompleted**<br /><br /> **SQL:BatchStarting**|  
|TSQL|擷取用戶端提交給 [!INCLUDE[tsql](../../includes/tsql-md.md)] 的所有 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 陳述式，以及發出的時間。 用來對用戶端應用程式進行偵錯。|**稽核登入**<br /><br /> **稽核登出**<br /><br /> **ExistingConnection**<br /><br /> **RPC:Starting**<br /><br /> **SQL:BatchStarting**|  
|TSQL_Duration|擷取用戶端提交給 [!INCLUDE[tsql](../../includes/tsql-md.md)] 的所有 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 陳述式、其執行時間 (以毫秒為單位)，並依持續時間分組。 用來找出過慢的查詢。|**RPC:Completed**<br /><br /> **SQL:BatchCompleted**|  
|TSQL_Grouped|擷取已提交給 [!INCLUDE[tsql](../../includes/tsql-md.md)] 的所有 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 陳述式，及其發出的時間。 按照提交陳述式的使用者或用戶端將資訊分組。 用來調查特定用戶端或使用者的查詢。|**稽核登入**<br /><br /> **稽核登出**<br /><br /> **ExistingConnection**<br /><br /> **RPC:Starting**<br /><br /> **SQL:BatchStarting**|  
|TSQL_Locks|擷取用戶端提交給 [!INCLUDE[tsql](../../includes/tsql-md.md)] 的所有 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 陳述式，連同例外鎖定事件。 使用此項目可針對死結、鎖定逾時和鎖定擴大事件進行疑難排解。|**Blocked Process Report**<br /><br /> **SP:StmtCompleted**<br /><br /> **SP:StmtStarting**<br /><br /> **SQL:StmtCompleted**<br /><br /> **SQL:StmtStarting**<br /><br /> **Deadlock Graph**<br /><br /> **Lock:Cancel**<br /><br /> **Lock:Deadlock**<br /><br /> **Lock:Deadlock Chain**<br /><br /> **Lock:Escalation**<br /><br /> **Lock:Timeout (timeout>0)**|  
|TSQL_Replay|擷取 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式的詳細資訊，重新執行追蹤時需要此資訊。 用來執行反覆微調，例如基準測試。|**CursorClose**<br /><br /> **CursorExecute**<br /><br /> **CursorOpen**<br /><br /> **CursorPrepare**<br /><br /> **CursorUnprepare**<br /><br /> **稽核登入**<br /><br /> **稽核登出**<br /><br /> **Existing Connection**<br /><br /> **RPC Output Parameter**<br /><br /> **RPC:Completed**<br /><br /> **RPC:Starting**<br /><br /> **Exec Prepared SQL**<br /><br /> **Prepare SQL**<br /><br /> **SQL:BatchCompleted**<br /><br /> **SQL:BatchStarting**|  
|TSQL_SPs|擷取所有執行中預存程序的詳細資訊。 用來分析預存程序的元件步驟。 如果您懷疑程序重新編譯過，請加入 **SP:Recompile** 事件。|**稽核登入**<br /><br /> **稽核登出**<br /><br /> **ExistingConnection**<br /><br /> **RPC:Starting**<br /><br /> **SP:Completed**<br /><br /> **SP:Starting**<br /><br /> **SP:StmtStarting**<br /><br /> **SQL:BatchStarting**|  
|調整|擷取預存程序和 [!INCLUDE[tsql](../../includes/tsql-md.md)] 批次執行的相關資訊。 用來產生追蹤輸出，供 [!INCLUDE[ssDE](../../includes/ssde-md.md)] Tuning Advisor 當做工作負載來微調資料庫。|**RPC:Completed**<br /><br /> **SP:StmtCompleted**<br /><br /> **SQL:BatchCompleted**|  
  
 如需有關事件類別的詳細資訊，請參閱 [SQL Server 事件類別參考](../../relational-databases/event-classes/sql-server-event-class-reference.md)。  
  
## <a name="default-template"></a>預設範本  
 [!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)] 會自動指定 **標準** 範本作為新追蹤套用的預設範本。 不過，您可以將預設範本變更為其他任何預先定義的或使用者自訂的範本。 若要變更預設範本，請在您建立或編輯範本時，使用 [追蹤範本屬性] 對話方塊中的 [一般] 索引標籤，選取 [作為所選取伺服器類型的預設範本] 核取方塊。  
  
 若要巡覽至 [追蹤範本屬性] 對話方塊，請在 [!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)] [檔案] 功能表上選擇 [範本]，然後按一下 [新增範本] 或 [編輯範本]。  
  
> [!NOTE]  
>  預設範本是特定伺服器類型專用的範本。 變更一種伺服器類型的預設範本，並不會影響其他伺服器類型的預設範本。 如需針對特定伺服器來設定預設範本的詳細資訊，請參閱[設定追蹤定義預設值 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/set-trace-definition-defaults-sql-server-profiler.md)。  
  
## <a name="see-also"></a>另請參閱  
 [建立追蹤範本 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/create-a-trace-template-sql-server-profiler.md)   
 [修改追蹤範本 &#40;SQL Server Profiler&#41;](./modify-trace-templates.md)   
 [匯出追蹤範本 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/export-a-trace-template-sql-server-profiler.md)   
 [匯入追蹤範本 &#40;SQL Server Profiler&#41;](../../tools/sql-server-profiler/import-a-trace-template-sql-server-profiler.md)  
  
