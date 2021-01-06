---
description: sys.dm_resource_governor_workload_groups_history_ex (Azure SQL Database)
title: sys.dm_resource_governor_workload_groups_history_ex (Azure SQL Database) Microsoft Docs
ms.custom: ''
ms.date: 01/05/2021
ms.prod: sql
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.dm_resource_governor_workload_groups_history_ex_TSQL
- sys.dm_resource_governor_workload_groups_history_ex
- dm_resource_governor_workload_groups_history_ex
- dm_resource_governor_workload_groups_history_ex_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- Availability Groups [SQL Server], monitoring
- sys.dm_resource_governor_workload_groups_history_ex dynamic management view
author: joesackmsft
ms.author: josack
ms.openlocfilehash: 69f310b699e59845dbd62a908a6a49bfb6efb42f
ms.sourcegitcommit: 11ca2305a8d7e420daf772eb97861706c9e08e31
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97928447"
---
# <a name="sysdm_resource_governor_workload_groups_history_ex-azure-sql-database"></a>sys.dm_resource_governor_workload_groups_history_ex (Azure SQL Database)
[!INCLUDE[Azure SQL Database Azure SQL Managed Instance](../../includes/applies-to-version/asdb-asdbmi.md)]

每個資料列都代表 Azure SQL Database 中工作負載群組統計資料的定期快照。 資料庫引擎啟動時，會建立快照集，之後每隔幾秒鐘就會執行。 目前與上一個快照之間的間隔可能會不同，而且是在資料行中提供 `duration_ms` 。 傳回最新可用的快照集，每個工作負載群組最多128個快照集。
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**pool_id**| int |資源集區的識別碼。 不可為 Null。|
|**group_id**| int |工作負載群組的識別碼。 不可為 Null。|
|**name**| nvarchar(256) |工作負載群組的名稱。 不可為 Null。|
|**snapshot_time**| Datetime |所建立之資源群組統計資料快照集的日期時間。|
|**duration_ms**| int |目前和先前快照之間的持續時間。|
|**active_worker_count**| int |目前快照中的總背景工作。|
|**active_request_count**| int |目前的要求計數。 不可為 Null。|
|**active_session_count**| int |目前快照中的使用中會話總計。|
|**total_request_count**| BIGINT |已在工作負載群組中完成之要求的累計計數。 不可為 Null。|
|**delta_request_count**| int |自從上一個快照集以來，工作負載群組中已完成要求的計數。 不可為 Null。|
|**total_cpu_usage_ms**| BIGINT |此工作負載群組的累計 CPU 使用量 (以毫秒為單位)。 不可為 Null。|
|**delta_cpu_usage_ms**| int |自上一個快照以來的 CPU 使用量（以毫秒為單位）。 不可為 Null。|
|**delta_cpu_usage_preemptive_ms**| int |自上一個快照集以來，SQL CPU RG 未管理的先占式 win32 呼叫。|
|**delta_reads_reduced_memgrant_count**| int |從上一個快照集起，到達最大查詢大小限制的記憶體授權計數。 不可為 Null。|
|**reads_throttled**| int |節流的讀取總數。|
|**delta_reads_queued**| int |自從上一個快照集以來排入佇列的讀取 Io 總數。 可為 Null。 如果資源群組不受 IO 管制，則為 Null。|
|**delta_reads_issued**| int |自上一個快照集以來發出的讀取 Io 總數。 可為 Null。 如果資源群組不受 IO 管制，則為 Null。|
|**delta_reads_completed**| int |自上一個快照集以來完成的讀取 Io 總數。 不可為 Null。|
|**delta_read_bytes**| BIGINT |自上一個快照集以來讀取的位元組總數。 不可為 Null。|
|**delta_read_stall_ms**| int |從上一個快照集起，讀取 IO 抵達和完成之間) 的總時間 (（毫秒）。 不可為 Null。|
|**delta_read_stall_queued_ms**| int |從上一個快照集起，讀取 IO 抵達和問題之間的總時間 (以毫秒為單位) 。 可為 Null。 如果資源群組不受 IO 管制，則為 Null。 非零 delta_read_stall_queued_ms 表示 IO 受 RG 影響。|
|**delta_writes_queued**| int |自從上一個快照集以來排入佇列的總寫入 Io。 可為 Null。 如果資源群組不受 IO 管制，則為 Null。|
|**delta_writes_issued**| int |自上一個快照集以來發出的寫入 Io 總數。 可為 Null。 如果資源群組不受 IO 管制，則為 Null。|
|**delta_writes_completed**| int |自上一個快照集以來完成的總寫入 Io。 不可為 Null。|
|**delta_writes_bytes**| BIGINT |自上一個快照集以來寫入的位元組總數。 不可為 Null。|
|**delta_write_stall_ms**| int |從上一個快照集起，寫入 IO 抵達和完成之間) 的總時間 (，以毫秒為單位。 不可為 Null。|
|**delta_background_writes**| int |自從上一個快照集以來，背景工作所執行的寫入總數。|
|**delta_background_write_bytes**| BIGINT |自上一個快照集以來，背景工作所執行的寫入大小總計（以位元組為單位）。|
|**delta_log_bytes_used**| BIGINT |自上次快照集之後所使用的記錄檔（位元組）。|
|**delta_log_temp_db_bytes_used**| BIGINT |自上次快照集之後所使用的 Tempdb 記錄（以位元組為單位）。|
|**delta_query_optimizations**| BIGINT |自從上一個快照集以來，此工作負載群組中的查詢優化計數。 不可為 Null。|
|**delta_suboptimal_plan_generations**| BIGINT |由於上一個快照集以來的記憶體壓力，在此工作負載群組中發生的次佳計畫層代計數。 不可為 Null。
|**max_memory_grant_kb**| BIGINT |群組的最大記憶體授與（以 KB 為單位）。|
|**max_request_cpu_msec**| BIGINT |單一要求的最大 CPU 使用量 (以毫秒為單位)。 不可為 Null。|
|**max_concurrent_request**| int |並行要求之最大數目的目前設定。 不可為 Null。|
|**max_io**| int |群組的 IO 限制上限。|
|**max_global_io**| int |僅供參考之用。 不支援。 我們無法保證未來的相容性。
|**max_queued_io**| int |僅供參考之用。 不支援。 我們無法保證未來的相容性。|
|**max_log_rate_kb**| BIGINT |資源群組層級的最大記錄速率 (每秒 kb 位元組) 。|
|**max_session**| int |群組的會話限制。|
|**max_worker**| int |群組的背景工作限制。|
|||

## <a name="permissions"></a>權限

此視圖需要 VIEW SERVER STATE 許可權。

## <a name="remarks"></a>備註

使用者可以存取這個動態管理檢視，以監視使用者工作負載集區的近乎即時資源耗用量，以及 Azure SQL Database 實例的系統內部集區。

> [!IMPORTANT]
> 此 DMV 所呈現的大部分資料都是供內部使用，而且可能會變更。

## <a name="examples"></a>範例

下列範例會傳回使用者集區每個快照集的最大記錄速率資料和耗用量：

```sql
SELECT snapshot_time,
       name,
       max_log_rate_kb,
       delta_log_bytes_used
FROM sys.dm_resource_governor_workload_groups_history_ex
WHERE name LIKE 'User%'
ORDER BY snapshot_time DESC;
```

## <a name="see-also"></a>另請參閱

- [翻譯記錄檔速率治理](/azure/sql-database/sql-database-resource-limits-database-server#transaction-log-rate-governance)
- [彈性集區 DTU 資源限制](/azure/sql-database/sql-database-dtu-resource-limits-elastic-pools)
- [彈性集區 vCore 資源限制](/azure/sql-database/sql-database-vcore-resource-limits-elastic-pools)