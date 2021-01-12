---
description: ALTER RESOURCE POOL (Transact-SQL)
title: ALTER RESOURCE POOL (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 05/01/2017
ms.prod: sql
ms.prod_service: sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- ALTER_RESOURCE_POOL_TSQL
- ALTER RESOURCE POOL
dev_langs:
- TSQL
helpviewer_keywords:
- ALTER RESOURCE POOL
ms.assetid: 9c1c4cfb-0e3b-4f01-bf57-3fce94c7d1d4
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 993ffa1892818d34415c77e0d5acd7969b8696fe
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98092068"
---
# <a name="alter-resource-pool-transact-sql"></a>ALTER RESOURCE POOL (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  變更 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中現有的資源管理員資源集區組態。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)。  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
ALTER RESOURCE POOL { pool_name | "default" }  
[WITH  
    ( [ MIN_CPU_PERCENT = value ]  
    [ [ , ] MAX_CPU_PERCENT = value ]   
    [ [ , ] CAP_CPU_PERCENT = value ]   
    [ [ , ] AFFINITY {
                        SCHEDULER = AUTO 
                      | ( <scheduler_range_spec> ) 
                      | NUMANODE = ( <NUMA_node_range_spec> )
                      }]   
    [ [ , ] MIN_MEMORY_PERCENT = value ]  
    [ [ , ] MAX_MEMORY_PERCENT = value ]   
    [ [ , ] MIN_IOPS_PER_VOLUME = value ]  
    [ [ , ] MAX_IOPS_PER_VOLUME = value ]  
)]   
[;]  
  
<scheduler_range_spec> ::=  
{SCHED_ID | SCHED_ID TO SCHED_ID}[,...n]  
  
<NUMA_node_range_spec> ::=  
{NUMA_node_ID | NUMA_node_ID TO NUMA_node_ID}[,...n]  
```  
  

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 { *pool_name* |  **"default"** }  
 現有使用者定義之資源集區的名稱，或是安裝 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 時建立之預設資源集區的名稱。  
  
 搭配 ALTER RESOURCE POOL 使用時，"default" 必須加上引號 ("") 或方括號 ([]) 才能避免與系統保留字 DEFAULT 產生衝突。 如需詳細資訊，請參閱＜ [Database Identifiers](../../relational-databases/databases/database-identifiers.md)＞。  
  
> [!NOTE]  
>  預先定義的工作負載群組和資源集區都會使用小寫名稱，例如 "default"。 如果是使用區分大小寫之定序的伺服器，則應該將此列入考量。 具有不區分大小寫之定序 (如 SQL_Latin1_General_CP1_CI_AS) 的伺服器會將 "default" 和 "Default" 視為相同。  
  
 MIN_CPU_PERCENT =*value*  
 當 CPU 出現競爭時，為在資源集區中的所有要求，指定保證平均 CPU 頻寬。 *value* 是預設值為 0 的整數。 *value* 允許的範圍從 0 至 100。  
  
 MAX_CPU_PERCENT =*value*  
 當出現 CPU 競爭時，指定所有要求在資源集區中將會接收的最大平均 CPU 頻寬。 *value* 是整數，預設值為 100。 允許的 *value* 範圍為 1 至 100。  
  
 CAP_CPU_PERCENT =*value*  
 **適用對象**：[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 及更新版本。  
  
 指定資源集區中要求的目標最大 CPU 容量。 *value* 是整數，預設值為 100。 允許的 *value* 範圍為 1 至 100。  
  
> [!NOTE]  
>  由於 CPU 治理的統計本質，您可能會注意到偶爾出現穗狀超過 CAP_CPU_PERCENT 中指定的值。  
  
 AFFINITY {SCHEDULER = AUTO | (Scheduler_range_spec) | NUMANODE = (NUMA_node_range_spec)}  
 **適用對象**：[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 及更新版本。  
  
 將資源集區附加至特定排程器。 預設值是 AUTO。  
  
 AFFINITY SCHEDULER = (Scheduler_range_spec) 會將資源集區對應至給定識別碼所識別的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 排程。 這些識別碼會對應至 [sys.dm_os_schedulers &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-os-schedulers-transact-sql.md) 中 scheduler_id 資料行的值。  
  
 當您使用 AFFINITY NAMANODE = (NUMA_node_range_spec) 時，資源集區會與對應至對應給定 NUMA 節點或節點範圍之實體 CPU 的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 排程器相似化。 您可以使用下列 Transact-SQL 查詢探索實體 NUMA 組態與 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 排程器識別碼之間的對應。  
  
```sql  
SELECT osn.memory_node_id AS [numa_node_id], sc.cpu_id, sc.scheduler_id  
FROM sys.dm_os_nodes AS osn  
INNER JOIN sys.dm_os_schedulers AS sc 
   ON osn.node_id = sc.parent_node_id 
      AND sc.scheduler_id < 1048576;  
```  
  
 MIN_MEMORY_PERCENT =*value*  
 指定為此資源集區所保留的最小記憶體數量 (不與其他資源集區共享)。 *value* 是預設值為 0 的整數。 *value* 允許的範圍從 0 至 100。  
  
 MAX_MEMORY_PERCENT =*value*  
 指定在此資源集區中，可供要求所用的伺服器記憶體總量。 *value* 是整數，預設值為 100。 允許的 *value* 範圍為 1 至 100。  
  
 MIN_IOPS_PER_VOLUME =*value*  
 **適用對象**：[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 及更新版本。  
  
 指定要為資源集區保留之每個磁碟區的每秒 I/O 作業數 (IOPS) 最小值。 允許的 *value* 範圍從 0 至 2^31-1 (2,147,483,647)。 指定 0 表示集區沒有最小臨界值。  
  
 MAX_IOPS_PER_VOLUME =*value*  
 **適用對象**：[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 及更新版本。  
  
 指定要允許資源集區使用之每個磁碟區的每秒 I/O 作業數 (IOPS) 最大值。 允許的 *value* 範圍從 0 至 2^31-1 (2,147,483,647)。 指定 0 可為集區設定無限的臨界值。 預設值是 0。  
  
 如果集區的 MAX_IOPS_PER_VOLUME 設定為 0，則完全不會管制集區，且即使其他集區設定 MIN_IOPS_PER_VOLUME，該集區也會保留系統中的所有 IOPS。 在此情況下，如果您希望此集區受 IO 管制，建議您將此集區的 MAX_IOPS_PER_VOLUME 值設定為較高的數字 (例如最大值 2^31-1)。  
  
## <a name="remarks"></a>備註  
 MAX_CPU_PERCENT 和 MAX_MEMORY_PERCENT 必須分別大於或等於 MIN_CPU_PERCENT 和 MIN_MEMORY_PERCENT。  
  
 如果有可用的 CPU 容量，MAX_CPU_PERCENT 就可以使用高於 MAX_CPU_PERCENT 值的 CPU 容量。 雖然可能會有高於 CAP_CPU_PERCENT 的週期穗狀，工作負載不應該在延伸的時間期間內超過 CAP_CPU_PERCENT，即使有可用的額外 CPU 容量。  
  
 每個相似化元件 (排程器或 NUMA 節點) 的 CPU 百分比總計不應該超過 100%。  
  
 當您要執行 DDL 陳述式時，建議您先熟悉資源管理員的狀態。 如需詳細資訊，請參閱 [Resource Governor](../../relational-databases/resource-governor/resource-governor.md)。  
  
 當變更計劃影響設定時，只有在執行 DBCC FREEPROCCACHE (*pool_name*) 之後新的設定才會在先前已快取的計劃中生效，其中 *pool_name* 是 Resource Governor 資源集區的名稱。  
  
-   如果您將 AFFINITY 從多重排程器變更為單一排程器，不需要執行 DBCC FREEPROCCACHE 因為平行計劃可以在序列模式中執行。 不過，它可能不會和編譯為序列計劃的計劃一樣有效率。  
  
-   如果您將 AFFINITY 從單一排程器變更為多重排程器，則不需要執行 DBCC FREEPROCCACHE。 不過，序列計畫無法以平行方式執行，因此清除個別的快取將會允許新計劃有可能使用平行處理原則進行編譯。  
  
> [!CAUTION]  
>  從與超過一個工作負載群組相關聯的資源集區清除已快取的計劃，會影響包含以 *pool_name* 所識別使用者定義資源集區的所有工作負載群組。  
  
## <a name="permissions"></a>權限  
 需要 CONTROL SERVER 權限。  
  
## <a name="examples"></a>範例  
 下列範例會保留 `default` 集區上的所有預設資源集區設定，除了變更為 `MAX_CPU_PERCENT` 的 `25` 之外。  
  
```sql  
ALTER RESOURCE POOL "default"  
WITH  
     ( MAX_CPU_PERCENT = 25);  
GO  
ALTER RESOURCE GOVERNOR RECONFIGURE;  
GO  
```  
  
 在下列範例中，`CAP_CPU_PERCENT` 會將硬體上限設定為 80%，而且 `AFFINITY SCHEDULER` 會設定為個別值 8 以及 12 到 16 的範圍。  
  
**適用對象**：[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 及更新版本。  
  
```sql  
ALTER RESOURCE POOL Pool25  
WITH(   
     MIN_CPU_PERCENT = 5,  
     MAX_CPU_PERCENT = 10,       
     CAP_CPU_PERCENT = 80,  
     AFFINITY SCHEDULER = (8, 12 TO 16),   
     MIN_MEMORY_PERCENT = 5,  
     MAX_MEMORY_PERCENT = 15  
);  
  
GO  
ALTER RESOURCE GOVERNOR RECONFIGURE;  
GO  
```  
  
## <a name="see-also"></a>另請參閱  
 [資源管理員](../../relational-databases/resource-governor/resource-governor.md)   
 [CREATE RESOURCE POOL &#40;Transact-SQL&#41;](../../t-sql/statements/create-resource-pool-transact-sql.md)   
 [DROP RESOURCE POOL &#40;Transact-SQL&#41;](../../t-sql/statements/drop-resource-pool-transact-sql.md)   
 [CREATE WORKLOAD GROUP &#40;Transact-SQL&#41;](../../t-sql/statements/create-workload-group-transact-sql.md)   
 [ALTER WORKLOAD GROUP &#40;Transact-SQL&#41;](../../t-sql/statements/alter-workload-group-transact-sql.md)   
 [DROP WORKLOAD GROUP &#40;Transact-SQL&#41;](../../t-sql/statements/drop-workload-group-transact-sql.md)   
 [ALTER RESOURCE GOVERNOR &#40;Transact-SQL&#41;](../../t-sql/statements/alter-resource-governor-transact-sql.md)  
  
  
