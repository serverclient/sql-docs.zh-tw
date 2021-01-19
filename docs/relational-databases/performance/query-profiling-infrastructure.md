---
title: 查詢分析基礎結構 | Microsoft Docs
description: 了解 SQL Server 資料庫引擎如何存取查詢執行計畫的執行階段資訊，以了解工作負載及如何驅動資源使用狀況。
ms.custom: ''
ms.date: 04/23/2019
ms.prod: sql
ms.reviewer: wiassaf
ms.technology: performance
ms.topic: conceptual
helpviewer_keywords:
- query plans [SQL Server]
- execution plans [SQL Server]
- query profiling
- lightweight query profiling
- lightweight profiling
- lwp
ms.assetid: 07f8f594-75b4-4591-8c29-d63811d7753e
author: pmasl
ms.author: pelopes
manager: amitban
ms.openlocfilehash: 57372929f190ff2fe32e7688d16acc75fafc9700
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98171550"
---
# <a name="query-profiling-infrastructure"></a>查詢分析基礎結構
[!INCLUDE [SQL Server Azure SQL Database](../../includes/applies-to-version/sql-asdb.md)]

[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 讓您能夠存取有關查詢執行計畫的執行階段資訊。 發生效能問題時最重要的動作之一，是準確地了解正在執行的工作負載以及衍生資源使用量的方式。 基於此因素，存取[實際執行計畫](../../relational-databases/performance/display-an-actual-execution-plan.md)就很重要。

儘管完成查詢是取得實際查詢計畫可用性的必要條件，當資料從[查詢計畫運算子](../../relational-databases/showplan-logical-and-physical-operators-reference.md)流到另一個運算子時，[即時查詢統計資料](../../relational-databases/performance/live-query-statistics.md)可以提供查詢執行程序的即時深入解析。 即時查詢計畫會顯示整體的查詢進度，以及運算子層級的執行階段執行統計資料，如產生的資料列數目、耗用時間、運算子進度等等。因為此資料會即時提供，不需等待查詢完成，所以，這些執行統計資料在偵錯查詢效能問題方面非常有用，例如，長時間執行查詢，以及無限期執行且永遠不會完成的查詢。

## <a name="the-standard-query-execution-statistics-profiling-infrastructure"></a>標準查詢執行統計資料分析基礎結構

「查詢執行統計資料分析基礎結構」 (或標準分析) 必須啟用，才能收集執行計畫的相關資訊，也就是資料列計數、CPU 和 I/O 使用量。 下列針對 **目標工作階段** 收集執行計畫資訊的方法會利用標準分析基礎結構：

- [SET STATISTICS XML](../../t-sql/statements/set-statistics-xml-transact-sql.md) 
- [SET STATISTICS PROFILE](../../t-sql/statements/set-statistics-profile-transact-sql.md)
- [即時查詢統計資料](../../relational-databases/performance/live-query-statistics.md)

> [!NOTE]
> 按一下 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 中的 [包含即時查詢統計資料] 按鈕，即會利用標準分析基礎結構。    
> 在更高版本的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中，如果已啟用[輕量型分析基礎結構](#lwp)，則在透過[活動監視器](../../relational-databases/performance-monitor/activity-monitor.md)檢視或直接查詢 [sys.dm_exec_query_profiles](../../relational-databases/system-dynamic-management-views/sys-dm-exec-query-profiles-transact-sql.md) DMV 時，就會由即時查詢統計資料而不是標準分析加以利用。 

下列針對 **所有工作階段** 全域收集執行計畫資訊的方法，會利用標準分析基礎結構：

-  **_query_post_execution_showplan_* _ 擴充事件。 若要啟用擴充事件，請參閱 [使用擴充事件監視系統活動](../../relational-databases/extended-events/monitor-system-activity-using-extended-events.md)。  
- [SQL 追蹤](../../relational-databases/sql-trace/sql-trace.md)與 [SQL Server Profiler](../../tools/sql-server-profiler/sql-server-profiler.md) 中的 _ *Showplan XML** 追蹤事件。 如需此追蹤事件的詳細資訊，請參閱 [Showplan XML 事件類別](../../relational-databases/event-classes/showplan-xml-event-class.md)。

執行擴充事件工作階段以使用 *query_post_execution_showplan* 事件時，接著也會填入 [sys.dm_exec_query_profiles](../../relational-databases/system-dynamic-management-views/sys-dm-exec-query-profiles-transact-sql.md) DMV，其會使用 [活動監視器](../../relational-databases/performance-monitor/activity-monitor.md)或直接查詢 DMV，針對所有工作階段啟用即時查詢統計資料。 如需相關資訊，請參閱 [Live Query Statistics](../../relational-databases/performance/live-query-statistics.md)。

## <a name="the-lightweight-query-execution-statistics-profiling-infrastructure"></a><a name="lwp"></a> 輕量型查詢執行統計資料分析基礎結構

從 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] SP2 和 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 開始，引進了新的 *輕量型查詢執行統計資料分析基礎結構* (或 **輕量型分析**)。 

> [!NOTE]
> 輕量型分析不支援原生編譯的預存程序。  

### <a name="lightweight-query-execution-statistics-profiling-infrastructure-v1"></a>輕量型查詢執行統計資料分析基礎結構 v1

**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] SP2 至 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)])。 
  
從 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] SP2 和[!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 開始，已藉由引進輕量型分析來降低收集執行計畫相關資訊的效能額外負荷。 不同於標準分析，輕量型分析不會收集 CPU 執行階段資訊。 不過，輕量型分析仍會收集資料列計數和 I/O 使用量資訊。

同時，也引進了新的 **_query_thread_profile_* _ 擴充事件來利用輕量型分析。 此擴充事件會公開每個運算子執行統計資料，以便更深入了解每個節點和執行緒的效能。 您可以設定使用此擴充事件的範例工作階段，如下列範例所示：

```sql
CREATE EVENT SESSION [NodePerfStats] ON SERVER
ADD EVENT sqlserver.query_thread_profile(
  ACTION(sqlos.scheduler_id,sqlserver.database_id,sqlserver.is_system,
    sqlserver.plan_handle,sqlserver.query_hash_signed,sqlserver.query_plan_hash_signed,
    sqlserver.server_instance_name,sqlserver.session_id,sqlserver.session_nt_username,
    sqlserver.sql_text))
ADD TARGET package0.ring_buffer(SET max_memory=(25600))
WITH (MAX_MEMORY=4096 KB,
  EVENT_RETENTION_MODE=ALLOW_SINGLE_EVENT_LOSS,
  MAX_DISPATCH_LATENCY=30 SECONDS,
  MAX_EVENT_SIZE=0 KB,
  MEMORY_PARTITION_MODE=NONE,
  TRACK_CAUSALITY=OFF,
  STARTUP_STATE=OFF);
```

> [!NOTE]
> 如需查詢分析的效能額外負荷詳細資訊，請參閱部落格文章 [Developers Choice:Query progress - anytime, anywhere](/archive/blogs/sql_server_team/query-progress-anytime-anywhere) (開發人員選擇：查詢進度 - 隨時隨地)。 

執行擴充事件工作階段以使用 _query_thread_profile* 事件時，接著也會使用輕量型分析填入 [sys.dm_exec_query_profiles](../../relational-databases/system-dynamic-management-views/sys-dm-exec-query-profiles-transact-sql.md) DMV，其會使用[活動監視器](../../relational-databases/performance-monitor/activity-monitor.md)或直接查詢 DMV，針對所有工作階段啟用即時查詢統計資料。

### <a name="lightweight-query-execution-statistics-profiling-infrastructure-v2"></a>輕量型查詢執行統計資料分析基礎結構 v2

**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] SP1 至 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)])。 

[!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] SP1 包含額外負荷最低的輕量型分析修訂版。 針對上方「適用於」中所述的版本，也可以使用[追蹤旗標 7412](../../t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql.md) 全域啟用輕量型分析。 已引進新的 DMF [sys.dm_exec_query_statistics_xml](../../relational-databases/system-dynamic-management-views/sys-dm-exec-query-statistics-xml-transact-sql.md)，針對進行中的要求傳回查詢執行計畫。

從 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] SP2 CU3 和 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] CU11 開始，如果未全域啟用輕量型分析，則可使用新的 [USE HINT 查詢提示](../../t-sql/queries/hints-transact-sql-query.md#use_hint)引數 **QUERY_PLAN_PROFILE**，針對任何工作階段啟用查詢層級的輕量型分析。 當包含這個新提示的查詢完成時，也會輸出新的 **_query_plan_profile_* _ 擴充事件，以提供類似 _query_post_execution_showplan* 擴充事件的實際執行計畫 XML。 

> [!NOTE]
> 即使未使用查詢提示，*query_plan_profile* 擴充事件也會利用輕量型分析。 

使用 *query_plan_profile* 擴充事件的範例工作階段可以如以下範例所示設定：

```sql
CREATE EVENT SESSION [PerfStats_LWP_Plan] ON SERVER
ADD EVENT sqlserver.query_plan_profile(
  ACTION(sqlos.scheduler_id,sqlserver.database_id,sqlserver.is_system,
    sqlserver.plan_handle,sqlserver.query_hash_signed,sqlserver.query_plan_hash_signed,
    sqlserver.server_instance_name,sqlserver.session_id,sqlserver.session_nt_username,
    sqlserver.sql_text))
ADD TARGET package0.ring_buffer(SET max_memory=(25600))
WITH (MAX_MEMORY=4096 KB,
  EVENT_RETENTION_MODE=ALLOW_SINGLE_EVENT_LOSS,
  MAX_DISPATCH_LATENCY=30 SECONDS,
  MAX_EVENT_SIZE=0 KB,
  MEMORY_PARTITION_MODE=NONE,
  TRACK_CAUSALITY=OFF,
  STARTUP_STATE=OFF);
```

### <a name="lightweight-query-execution-statistics-profiling-infrastructure-v3"></a>輕量型查詢執行統計資料分析基礎結構 v3

**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]

[!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 包含最新修訂的輕量型分析版本，可收集所有執行的資料列計數資訊。 在 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 上預設會啟用輕量型分析。 從 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 開始，追蹤旗標 7412 沒有任何作用。 可以在資料庫層級使用 LIGHTWEIGHT_QUERY_PROFILING [資料庫範圍設定](../../t-sql/statements/alter-database-scoped-configuration-transact-sql.md)來停用輕量級分析：`ALTER DATABASE SCOPED CONFIGURATION SET LIGHTWEIGHT_QUERY_PROFILING = OFF;`。

現已引進新的 DMF [sys.dm_exec_query_plan_stats](../../relational-databases/system-dynamic-management-views/sys-dm-exec-query-plan-stats-transact-sql.md)，它在大多數查詢中會傳回最後一個已知實際執行計畫的對等項目，稱為「最後一個執行計畫統計資料」。 可以在資料庫層級使用 LAST_QUERY_PLAN_STATS [資料庫範圍設定](../../t-sql/statements/alter-database-scoped-configuration-transact-sql.md)來啟用最後一個查詢計畫統計資料：`ALTER DATABASE SCOPED CONFIGURATION SET LAST_QUERY_PLAN_STATS = ON;`。

不同於使用標準分析的 *query_post_execution_showplan*，新的 *query_post_execution_plan_profile* 擴充事件會根據輕量型分析收集實際執行計畫的對等項目。 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 也會從 CU14 開始提供此事件。 使用 *query_post_execution_plan_profile* 擴充事件的範例工作階段可依照下列範例進行設定：

```sql
CREATE EVENT SESSION [PerfStats_LWP_All_Plans] ON SERVER
ADD EVENT sqlserver.query_post_execution_plan_profile(
  ACTION(sqlos.scheduler_id,sqlserver.database_id,sqlserver.is_system,
    sqlserver.plan_handle,sqlserver.query_hash_signed,sqlserver.query_plan_hash_signed,
    sqlserver.server_instance_name,sqlserver.session_id,sqlserver.session_nt_username,
    sqlserver.sql_text))
ADD TARGET package0.ring_buffer(SET max_memory=(25600))
WITH (MAX_MEMORY=4096 KB,
  EVENT_RETENTION_MODE=ALLOW_SINGLE_EVENT_LOSS,
  MAX_DISPATCH_LATENCY=30 SECONDS,
  MAX_EVENT_SIZE=0 KB,
  MEMORY_PARTITION_MODE=NONE,
  TRACK_CAUSALITY=OFF,
  STARTUP_STATE=OFF);
```

#### <a name="example-1---extended-event-session-using-standard-profiling"></a>範例 1 - 使用標準分析的擴充事件工作階段

```sql
CREATE EVENT SESSION [QueryPlanOld] ON SERVER 
ADD EVENT sqlserver.query_post_execution_showplan(
    ACTION(sqlos.task_time, sqlserver.database_id, 
    sqlserver.database_name, sqlserver.query_hash_signed, 
    sqlserver.query_plan_hash_signed, sqlserver.sql_text))
ADD TARGET package0.event_file(SET filename = N'C:\Temp\QueryPlanStd.xel')
WITH (MAX_MEMORY=4096 KB, EVENT_RETENTION_MODE=ALLOW_SINGLE_EVENT_LOSS, 
    MAX_DISPATCH_LATENCY=30 SECONDS, MAX_EVENT_SIZE=0 KB, 
    MEMORY_PARTITION_MODE=NONE, TRACK_CAUSALITY=OFF, STARTUP_STATE=OFF);
```

#### <a name="example-2---extended-event-session-using-lightweight-profiling"></a>範例 2 - 使用輕量型分析的擴充事件工作階段

```sql
CREATE EVENT SESSION [QueryPlanLWP] ON SERVER 
ADD EVENT sqlserver.query_post_execution_plan_profile(
    ACTION(sqlos.task_time, sqlserver.database_id, 
    sqlserver.database_name, sqlserver.query_hash_signed, 
    sqlserver.query_plan_hash_signed, sqlserver.sql_text))
ADD TARGET package0.event_file(SET filename=N'C:\Temp\QueryPlanLWP.xel')
WITH (MAX_MEMORY=4096 KB, EVENT_RETENTION_MODE=ALLOW_SINGLE_EVENT_LOSS, 
    MAX_DISPATCH_LATENCY=30 SECONDS, MAX_EVENT_SIZE=0 KB, 
    MEMORY_PARTITION_MODE=NONE, TRACK_CAUSALITY=OFF, STARTUP_STATE=OFF);
```

## <a name="query-profiling-infrastruture-usage-guidance"></a>查詢分析基礎結構使用方式指導方針
下表摘要說明啟用標準分析或輕量型分析 (全域 (在伺服器層級) 或在單一工作階段中) 的動作。 也包括支援動作的最新版本。 

|影響範圍|標準分析|輕量型分析|
|---------------|---------------|---------------|
|全域|具有 `query_post_execution_showplan` XE 的 xEvent 工作階段；從 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 開始|追蹤旗標 7412；從 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] SP1 開始|
|全域|具有 `Showplan XML` 追蹤事件的 SQL 追蹤與 SQL Server Profiler；從 SQL Server 2000 開始|具有 `query_thread_profile` XE 的 xEvent 工作階段；從 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] SP2 開始|
|全域|-|具有 `query_post_execution_plan_profile` XE 的 xEvent 工作階段；從 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] CU14 與 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 開始|
|工作階段|使用 `SET STATISTICS XML ON`；從 SQL Server 2000 開始|使用 `query_plan_profile` XE 搭配 xEvent 工作階段使用 `QUERY_PLAN_PROFILE` 查詢提示；從 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] SP2 CU3 與 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] CU11 開始|
|工作階段|使用 `SET STATISTICS PROFILE ON`；從 SQL Server 2000 開始|-|
|工作階段|按一下 SSMS 中的[即時查詢統計資料](../../relational-databases/performance/live-query-statistics.md)按鈕；從 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] SP2 開始|-|

## <a name="remarks"></a>備註

> [!IMPORTANT]
> 由於執行參考 [sys.dm_exec_query_statistics_xml](../../relational-databases/system-dynamic-management-views/sys-dm-exec-query-statistics-xml-transact-sql.md) 的監視預存程序時可能會產生隨機 AV，因而請確保會在 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 和 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 中安裝 [KB 4078596](https://support.microsoft.com/help/4078596)。

從輕量型分析 v2 及其低額外負荷開始，尚未受限於 CPU 的任何伺服器都可 **持續** 執行輕量型分析，並讓資料庫專業人員可隨時點選任何執行中的執行 (例如，使用活動監視器，或直接查詢 `sys.dm_exec_query_profiles`)，並取得含執行階段統計資料的查詢計畫。

如需查詢分析的效能額外負荷詳細資訊，請參閱部落格文章 [Developers Choice:Query progress - anytime, anywhere](https://techcommunity.microsoft.com/t5/SQL-Server/Developers-Choice-Query-progress-anytime-anywhere/ba-p/385004) (開發人員選擇：查詢進度 - 隨時隨地)。 

> [!NOTE]
> 如果已啟用標準分析基礎結構，採用輕量型分析的擴充事件將會使用標準分析中的資訊。 例如，假設在使用 `query_post_execution_showplan` 的擴充事件工作階段執行時，啟動了另一個使用 `query_post_execution_plan_profile` 的工作階段。 第二個工作階段仍會使用標準分析中的資訊。

> [!NOTE]
> 在 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 上，輕量型分析預設為關閉，但會在 XEvent 追蹤依賴 `query_post_execution_plan_profile` 啟動時啟用，然後在追蹤停止時再次停用。 因此，如果 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 執行個體上經常啟動和停止以 `query_post_execution_plan_profile` 為基礎的 Xevent 追蹤，則強烈建議在具有追蹤旗標 7412 的全域層級上啟用輕量型分析，以避免重複啟用/停用負擔。 

## <a name="see-also"></a>另請參閱  
 [效能的監視與微調](../../relational-databases/performance/monitor-and-tune-for-performance.md)     
 [效能監視及微調工具](../../relational-databases/performance/performance-monitoring-and-tuning-tools.md)     
 [開啟活動監視器 &#40;SQL Server Management Studio&#41;](../../relational-databases/performance-monitor/open-activity-monitor-sql-server-management-studio.md)     
 [活動監視器](../../relational-databases/performance-monitor/activity-monitor.md)     
 [相關檢視、函數與程序](../../relational-databases/performance/monitoring-performance-by-using-the-query-store.md)     
 [使用擴充事件監視系統活動](../../relational-databases/extended-events/monitor-system-activity-using-extended-events.md)      
 [sys.dm_exec_query_statistics_xml](../../relational-databases/system-dynamic-management-views/sys-dm-exec-query-statistics-xml-transact-sql.md)     
 [sys.dm_exec_query_profiles](../../relational-databases/system-dynamic-management-views/sys-dm-exec-query-profiles-transact-sql.md)     
 [追蹤旗標](../../t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql.md)    
 [執行程序邏輯和實體運算子參考](../../relational-databases/showplan-logical-and-physical-operators-reference.md)    
 [實際執行計畫](../../relational-databases/performance/display-an-actual-execution-plan.md)    
 [即時查詢統計資料](../../relational-databases/performance/live-query-statistics.md)