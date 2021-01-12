---
description: ALTER SERVER CONFIGURATION (Transact-SQL)
title: ALTER SERVER CONFIGURATION (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 05/22/2019
ms.prod: sql
ms.prod_service: sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- ALTER SERVER CONFIGURATION
- ALTER_SERVER_CONFIGURATION_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- SERVER CONFIGURATION, ALTER
- process affinity, setting
- ALTER SERVER CONFIGURATION statement
- setting process affinity
ms.assetid: f3059e42-5f6f-4a64-903c-86dca212a4b4
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 5fc18fa93e47267d299c3b568011de5645f943da
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98092028"
---
# <a name="alter-server-configuration-transact-sql"></a>ALTER SERVER CONFIGURATION (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

修改 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中目前伺服器的全域組態設定。  
  
![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  

```syntaxsql
ALTER SERVER CONFIGURATION  
SET <optionspec>   
[;]  
  
<optionspec> ::=  
{  
     <process_affinity>  
   | <diagnostic_log>  
   | <failover_cluster_property>  
   | <hadr_cluster_context>  
   | <buffer_pool_extension>  
   | <soft_numa>  
   | <memory_optimized>
}  
  
<process_affinity> ::=   
   PROCESS AFFINITY   
   {  
     CPU = { AUTO | <CPU_range_spec> }   
   | NUMANODE = <NUMA_node_range_spec>   
   }  
   <CPU_range_spec> ::=   
      { CPU_ID | CPU_ID  TO CPU_ID } [ ,...n ]   
  
   <NUMA_node_range_spec> ::=   
      { NUMA_node_ID | NUMA_node_ID TO NUMA_node_ID } [ ,...n ]  
  
<diagnostic_log> ::=   
   DIAGNOSTICS LOG   
   {   
     ON    
   | OFF    
   | PATH = { 'os_file_path' | DEFAULT }    
   | MAX_SIZE = { 'log_max_size' MB | DEFAULT }    
   | MAX_FILES = { 'max_file_count' | DEFAULT }    
   }  
  
<failover_cluster_property> ::=   
   FAILOVER CLUSTER PROPERTY <resource_property>  
   <resource_property> ::=  
      {  
        VerboseLogging = { 'logging_detail' | DEFAULT }    
      | SqlDumperDumpFlags = { 'dump_file_type' | DEFAULT }  
      | SqlDumperDumpPath = { 'os_file_path'| DEFAULT }  
      | SqlDumperDumpTimeOut = { 'dump_time-out' | DEFAULT }  
      | FailureConditionLevel = { 'failure_condition_level' | DEFAULT }  
      | HealthCheckTimeout = { 'health_check_time-out' | DEFAULT }  
      }  
  
<hadr_cluster_context> ::=  
   HADR CLUSTER CONTEXT = { 'remote_windows_cluster' | LOCAL }  
  
<buffer_pool_extension>::=  
    BUFFER POOL EXTENSION   
    { ON ( FILENAME = 'os_file_path_and_name' , SIZE = <size_spec> )   
    | OFF }  
  
    <size_spec> ::=  
        { size [ KB | MB | GB ] }  
  
<soft_numa> ::=  
    SOFTNUMA  
    { ON | OFF }  

<memory-optimized> ::=   
   MEMORY_OPTIMIZED   
   {   
     ON 
   | OFF
   | [ TEMPDB_METADATA = { ON [(RESOURCE_POOL='resource_pool_name')] | OFF }
   | [ HYBRID_BUFFER_POOL = { ON | OFF }
   }  
```  
  

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
**\<process_affinity> ::=**  
  
PROCESS AFFINITY  
讓硬體執行緒與 CPU 產生關聯。  
  
CPU = { AUTO | \<CPU_range_spec> }  
將 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 工作者執行緒分配給指定之範圍內的每個 CPU。 超出指定範圍的 CPU 將不會有指派的執行緒。  
  
AUTO  
指定不將 CPU 指派給任何執行緒。 作業系統可以根據伺服器工作負載自由地在 CPU 之間移動執行緒。 此設定是預設值且建議使用。  
  
\<CPU_range_spec> ::=  
指定指派執行緒的目標 CPU 或 CPU 範圍。  
  
{ CPU_ID | CPU_ID  TO  CPU_ID } [ ,...n ]  
這是一個或多個 CPU 的清單。 CPU 識別碼從 0 開始，而且是 **integer** 值。  
  
NUMANODE = \<NUMA_node_range_spec>  
將執行緒指派給屬於指定之 NUMA 節點或節點範圍的所有 CPU。  
  
\<NUMA_node_range_spec> ::=  
指定 NUMA 節點或 NUMA 節點範圍。  
  
{ NUMA_node_ID | NUMA_node_ID  TO NUMA_node_ID } [ ,...n ]  
這是一個或多個 NUMA 節點的清單。 NUMA 節點識別碼從 0 開始，而且是 **integer** 值。  
  
**\<diagnostic_log> ::=**  
  
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 起)。  

  
DIAGNOSTICS LOG  
啟動或停止記錄 sp_server_diagnostics 程序所擷取的診斷資料。 這個引數也會設定 SQLDIAG 記錄設定參數，例如，記錄檔換用計數、記錄檔大小和檔案位置。 如需詳細資訊，請參閱 [檢視及閱讀容錯移轉叢集執行個體診斷記錄檔](../../sql-server/failover-clusters/windows/view-and-read-failover-cluster-instance-diagnostics-log.md)。  
  
開啟  
在 PATH 檔案選項中指定的位置上，啟動 [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] 記錄診斷資料。 這個引數是預設值。  
  
OFF  
停止記錄診斷資料。  
  
PATH = { 'os_file_path' | DEFAULT }  
指定診斷記錄檔位置的路徑。 預設位置是 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 容錯移轉叢集執行個體其安裝資料夾內的 \<\MSSQL\Log>。  
  
MAX_SIZE = { 'log_max_size' MB | DEFAULT }  
每個診斷記錄檔可成長的大小上限 (以 MB 為單位)。 預設值是 100 MB。  
  
MAX_FILES = { 'max_file_count' | DEFAULT }  
回收以用於新的診斷記錄之前，電腦上可儲存的診斷記錄檔數目上限。  
  
**\<failover_cluster_property> ::=**  
  
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 起)。    
  
FAILOVER CLUSTER PROPERTY  
修改 SQL Server 資源私用容錯移轉叢集屬性。  
  
VERBOSE LOGGING = { 'logging_detail' | DEFAULT }  
設定 SQL Server 容錯移轉叢集的記錄層次。 可開啟它，在錯誤記錄檔中提供詳細資訊來進行疑難排解。  
  
-   0 - 關閉記錄功能 (預設)  
  
-   1 - 只有錯誤  
  
-   2 - 錯誤和警告  
  
在資源容錯移轉案例中，SQL Server 資源 DLL 可在執行容錯移轉前取得傾印檔案。 這同時適用於 FCI 和可用性群組技術。 當 SQL Server 資源 DLL 判斷 SQL Server 資源失敗時，SQL Server 資源 DLL 會使用 Sqldumper.exe 公用程式取得 SQL Server 流程的傾印檔案。 為確保 Sqldumper.exe 公用程式在資源容錯移轉時成功產生傾印檔案，您必須將下列三個屬性設為必要條件：SqlDumperDumpTimeOut、SqlDumperDumpPath、SqlDumperDumpFlags。

SQLDUMPEREDUMPFLAGS  
決定 SQL Server SQLDumper 公用程式所產生的傾印檔案類型。 預設設定為 0。 此設定使用十進位值，不使用十六進位值。 迷你傾印請使用 288，具有間接記憶體的迷你傾印請使用 296，已篩選的傾印請使用 33024。 如需詳細資訊，請參閱 [SQL Server 傾印工具公用程式知識庫文章](https://go.microsoft.com/fwlink/?LinkId=206173)。  
  
SQLDUMPERDUMPPATH = { 'os_file_path' | DEFAULT }  
SQLDumper 公用程式儲存傾印檔案的位置。 如需詳細資訊，請參閱 [SQL Server 傾印工具公用程式知識庫文章](https://go.microsoft.com/fwlink/?LinkId=206173)。  
  
SQLDUMPERDUMPTIMEOUT = { 'dump_time-out' | DEFAULT }  
如果 SQL Server 發生失敗，SQLDumper 公用程式產生傾印的逾時值 (以毫秒為單位)。 預設值為 0，表示完成傾印沒有時間限制。 如需詳細資訊，請參閱 [SQL Server 傾印工具公用程式知識庫文章](https://go.microsoft.com/fwlink/?LinkId=206173)。  
  
 FAILURECONDITIONLEVEL = { 'failure_condition_level' | DEFAULT }  
 SQL Server 容錯移轉叢集執行個體應該容錯移轉或重新啟動的條件。 預設值為 3，表示 SQL Server 資源將在發生嚴重伺服器錯誤時容錯移轉或重新啟動。 如需失敗條件層次的詳細資訊，請參閱[設定 FailureConditionLeve 屬性設定](../../sql-server/failover-clusters/windows/configure-failureconditionlevel-property-settings.md)。  
  
HEALTHCHECKTIMEOUT = { 'health_check_time-out' | DEFAULT }  
SQL Server Database Engine 資源 DLL 在將 SQL Server 執行個體視為無回應之前，應該等候伺服器健全狀態資訊多久時間的逾時值。 逾時值以毫秒格式表示。 預設值為 60,000 毫秒 (60 秒)。  
  
**\<hadr_cluster_context> ::=**  
  
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 起)。   
  
HADR CLUSTER CONTEXT **=** { **'** _remote\_windows\_cluster_ **'** | LOCAL }  
將伺服器執行個體的 HADR 叢集內容切換至指定的 Windows Server 容錯移轉叢集 (WSFC)。 「HADR 叢集內容」可決定由哪個 WSFC 管理可用性複本 (由伺服器執行個體所裝載) 的中繼資料。 僅在跨叢集移轉 [!INCLUDE[ssHADR](../../includes/sshadr-md.md)] 至新 WSFC 上的 [!INCLUDE[ssSQL11SP1](../../includes/sssql11sp1-md.md)] 或更新版本執行個體時，才使用 SET HADR CLUSTER CONTEXT 選項。  
  
您只能將 HADR 叢集內容從本機 WSFC 切換至遠端 WSFC。 然後，您可能選擇從遠端 WSFC 切換回本機 WSFC。 HADR 叢集內容只有在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的執行個體未裝載任何可用性複本時，才能切換至遠端叢集。  
  
遠端 HADR 叢集內容隨時可以切換回本機叢集。 不過，只要伺服器執行個體裝載任何可用性複本，就不能再次切換內容。 
  
若要識別目的地叢集，請指定下列其中一個值：  
  
*windows_cluster*  
WSFC 的網路名稱。 您可以指定簡短名稱或完整網域名稱。 為了尋找簡短名稱的目標 IP 位址，ALTER SERVER CONFIGURATION 會使用 DNS 解析。 在某些情況下，簡短名稱可能會產生混淆，而 DNS 可能會傳回錯誤的 IP 位址。 我們建議您指定完整網域名稱。  
  
> [!NOTE] 
> 不再支援使用這項設定來進行跨叢集移轉。 若要執行跨叢集移轉，請使用分散式可用性群組或透過其他方法，例如，記錄傳送。 
  
LOCAL  
本機 WSFC。  
  
如需詳細資訊，請參閱[變更伺服器執行個體的 HADR 叢集內容 &#40;SQL Server&#41;](../../database-engine/availability-groups/windows/change-the-hadr-cluster-context-of-server-instance-sql-server.md)。  
  
**\<buffer_pool_extension>::=**  
  
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 起)。    
  
開啟  
啟用緩衝集區延伸模組選項。 此選項會使用非揮發性儲存體來擴充緩衝集區的大小。 非揮發性儲存體 (例如固態硬碟 (SSD)) 會在集區中保存清除資料頁面。 如需此功能的詳細資訊，請參閱[緩衝集區延伸](../../database-engine/configure-windows/buffer-pool-extension.md)。並非每個 SQL Server 版本都提供緩衝集區延伸。 如需詳細資訊，請參閱 [SQL Server 2016 的版本及支援功能](../../sql-server/editions-and-components-of-sql-server-2016.md)。  
  
FILENAME = 'os_file_path_and_name'  
定義緩衝集區延伸模組快取檔案的目錄路徑和名稱。 副檔名必須指定為 .BPE。 先關閉緩衝集區延伸，然後再修改檔案名稱。  
  
SIZE = *size* [ **KB** | MB | GB ]  
定義快取大小。 預設大小規格是 KB。 下限是「最大伺服器記憶體」的大小。 上限是「最大伺服器記憶體」大小的 32 倍。 如需「最大伺服器記憶體」的詳細資訊，請參閱 [sp_configure &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-configure-transact-sql.md)。  
  
先關閉緩衝集區延伸，然後再修改檔案大小。 若指定的大小要小於目前的大小，則必須重新啟動 SQL Server 執行個體以回收記憶體。 否則，指定的大小必須大於目前大小或者必須相同。  
  
OFF  
停用緩衝集區延伸模組選項。 先停用緩衝集區延伸選項，然後再修改任何相關聯的參數，例如檔案的大小或名稱。 當這個選項停用時，所有相關的組態資訊都會從登錄中移除。  
  
> [!WARNING]  
>  停用緩衝集區延伸模組可能對伺服器效能產生負面影響，因為緩衝集區大小會大幅減少。  
  
**\<soft_numa>**  

**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 起)。  
  
開啟  
啟用自動資料分割，將大型的 NUMA 硬體節點分割成較小的 NUMA 節點。 您需要重新啟動資料庫引擎，才能變更執行中的值。  
  
OFF  
停用將大型 NUMA 硬體節點分割成較小 NUMA 節點的自動軟體資料分割。 您需要重新啟動資料庫引擎，才能變更執行中的值。  

> [!WARNING]
> 搭配 ALTER SERVER CONFIGURATION 陳述式使用 SQL Server Agent 與 SOFT NUMA 選項時，有個已知的行為問題。  以下是建議的作業順序：  
> 1) 停止 SQL Server Agent 的執行個體。  
> 2) 執行 ALTER SERVER CONFIGURATION  SOFT NUMA 選項。  
> 3) 重新啟動 SQL Server 執行個體。  
> 4) 啟動 SQL Server Agent 的執行個體。  
  
**詳細資訊：** 如果您在 SQL Server 服務重新啟動之前搭配 SET SOFTNUMA 命令執行 ALTER SERVER CONFIGURATION，則在 SQL Server Agent 服務停止時，它會執行 T-SQL RECONFIGURE 命令，以便將 SOFTNUMA 設定還原回 ALTER SERVER CONFIGURATION 之前的情況。 

**\<memory_optimized> ::=**

**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 起)。

開啟 <br>
啟用所有屬於[記憶體內部資料庫](../../relational-databases/in-memory-database.md)功能系列的執行個體層級功能。 目前這包括[經記憶體最佳化的 tempdb 中繼資料](../../relational-databases/databases/tempdb-database.md#memory-optimized-tempdb-metadata)和[混合式緩衝集區](../../database-engine/configure-windows/hybrid-buffer-pool.md)。 需要重新開機才會生效。

OFF <br>
停用所有屬於記憶體內部資料庫功能系列的執行個體層級功能。 需要重新開機才會生效。

TEMPDB_METADATA = ON | OFF <br>
僅啟用或停用經記憶體最佳化的 TempDB 中繼資料。 需要重新開機才會生效。 

RESOURCE_POOL='resource_pool_name' <br>
與 TEMPDB_METADATA = ON 結合時，指定應用於 tempdb 之使用者定義的資源集區。 如未指定，則 tempdb 會使用預設集區。 集區必須已經存在。 如果重新啟動服務時集區尚不可用，則 tempdb 會使用預設集區。


HYBRID_BUFFER_POOL = ON | OFF <br>
啟用或停用執行個體層級的混合式緩衝集區。 需要重新開機才會生效。


## <a name="general-remarks"></a>一般備註  
除非明確指定，否則此陳述式不需重新啟動 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]。 如果是 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 容錯移轉叢集執行個體，則不需重新啟動 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 叢集資源。  
  
## <a name="limitations-and-restrictions"></a>限制事項  
此陳述式不支援 DDL 觸發程序。  
  
## <a name="permissions"></a>權限  
需要：
- 處理序相似性選項的 `ALTER SETTINGS` 權限。
- 診斷記錄和容錯移轉叢集屬性選項的 `ALTER SETTINGS` 與 `VIEW SERVER STATE` 權限。
- HADR 叢集內容選項的 `CONTROL SERVER` 權限。  
- 緩衝集區延伸模組選項的 `ALTER SERVER STATE` 權限。  
  
[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)][!INCLUDE[ssDE](../../includes/ssde-md.md)] 資源 DLL 是以本機系統帳戶執行。 因此，本機系統帳戶必須具備存取診斷記錄選項中指定路徑的讀取和寫入權限。  
  
## <a name="examples"></a>範例  
  
|類別|代表性語法元素|  
|--------------|------------------------------|  
|[設定處理序相似性](#Affinity)|CPU • NUMANODE • AUTO|  
|[設定診斷記錄檔選項](#Diagnostic)|ON • OFF • PATH • MAX_SIZE|  
|[設定容錯移轉叢集屬性](#Failover)|HealthCheckTimeout|  
|[變更可用性複本的叢集內容](#ChangeClusterContextExample)|**'** *windows_cluster* **'**|  
|[設定緩衝集區延伸模組](#BufferPoolExtension)|BUFFER POOL EXTENSION| 
|[設定記憶體內部資料庫選項](#MemoryOptimized)|MEMORY_OPTIMIZED|

  
###  <a name="setting-process-affinity"></a><a name="Affinity"></a> 設定處理序相似性  
本節的範例示範如何將處理序相似性設定為 CPU 和 NUMA 節點。 範例假設伺服器包含 256 個 CPU，而這些 CPU 排列成四個群組，總計有 16 個 NUMA 節點。 執行緒不會指派給任何 NUMA 節點或 CPU。  
  
-   群組 0：NUMA 節點 0 到 3，CPU 0 到 63  
-   群組 1：NUMA 節點 4 到 7，CPU 64 到 127  
-   群組 2：NUMA 節點 8 到 12，CPU 128 到 191  
-   群組 3：NUMA 節點 13 到 16，CPU 192 到 255  
  
#### <a name="a-setting-affinity-to-all-cpus-in-groups-0-and-2"></a>A. 將相似性設定為群組 0 和 2 中的所有 CPU  
下列範例會將相似性設定為群組 0 和 2 中的所有 CPU。  
  
```sql  
ALTER SERVER CONFIGURATION   
SET PROCESS AFFINITY CPU=0 TO 63, 128 TO 191;  
```  
  
#### <a name="b-setting-affinity-to-all-cpus-in-numa-nodes-0-and-7"></a>B. 將相似性設定為 NUMA 節點 0 和 7 中的所有 CPU  
下列範例會將 CPU 相似性設定為只有節點 `0` 和 `7`。  
  
```sql  
ALTER SERVER CONFIGURATION   
SET PROCESS AFFINITY NUMANODE=0, 7;  
```  
  
#### <a name="c-setting-affinity-to-cpus-60-through-200"></a>C. 將相似性設定為 CPU 60 到 200  
下列範例會將相似性設定為 CPU 60 到 200。  
  
```sql  
ALTER SERVER CONFIGURATION   
SET PROCESS AFFINITY CPU=60 TO 200;  
```  
  
#### <a name="d-setting-affinity-to-cpu-0-on-a-system-that-has-two-cpus"></a>D. 在具有兩個 CPU 的系統上，將相似性設定為 CPU 0  
下列範例會在具有兩個 CPU 的電腦上，將相似性設定為 `CPU=0`。 執行下列陳述式之前，內部相似性位元遮罩是 00。  
  
```sql  
ALTER SERVER CONFIGURATION SET PROCESS AFFINITY CPU=0;  
```  
  
#### <a name="e-setting-affinity-to-auto"></a>E. 將相似性設定為 AUTO  
下列範例會將相似性設定為 `AUTO`。  
  
```sql  
ALTER SERVER CONFIGURATION  
SET PROCESS AFFINITY CPU=AUTO;  
```  
  
###  <a name="setting-diagnostic-log-options"></a><a name="Diagnostic"></a> Setting diagnostic log options  
  
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 起)。    
  
本節的範例示範如何設定診斷記錄檔選項的值。  
  
#### <a name="a-starting-diagnostic-logging"></a>A. 啟動診斷記錄  
下列範例會啟動診斷資料記錄。  
  
```sql  
ALTER SERVER CONFIGURATION SET DIAGNOSTICS LOG ON;  
```  
  
#### <a name="b-stopping-diagnostic-logging"></a>B. 停止診斷記錄  
下列範例會停止診斷資料記錄。  
  
```sql  
ALTER SERVER CONFIGURATION SET DIAGNOSTICS LOG OFF;  
```  
  
#### <a name="c-specifying-the-location-of-the-diagnostic-logs"></a>C. 指定診斷記錄的位置  
下列範例會將診斷記錄檔的位置設定為指定的檔案路徑。  
  
```sql  
ALTER SERVER CONFIGURATION  
SET DIAGNOSTICS LOG PATH = 'C:\logs';  
```  
  
#### <a name="d-specifying-the-maximum-size-of-each-diagnostic-log"></a>D. 指定每個診斷記錄的大小上限  
下列範例會將每個診斷記錄檔的大小上限設為 10 MB。  
  
```sql  
ALTER SERVER CONFIGURATION   
SET DIAGNOSTICS LOG MAX_SIZE = 10 MB;  
```  
  
###  <a name="setting-failover-cluster-properties"></a><a name="Failover"></a> 設定容錯移轉叢集屬性  
  
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 起)。   
  
下列範例示範如何設定 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 容錯移轉叢集資源屬性的值。  
  
#### <a name="a-specifying-the-value-for-the-healthchecktimeout-property"></a>A. 指定 HealthCheckTimeout 屬性的值  
下列範例會將 `HealthCheckTimeout` 選項設定為 15,000 毫秒 (15 秒)。  
  
```sql  
ALTER SERVER CONFIGURATION   
SET FAILOVER CLUSTER PROPERTY HealthCheckTimeout = 15000;  
```  
  
###  <a name="b-changing-the-cluster-context-of-an-availability-replica"></a><a name="ChangeClusterContextExample"></a> B. 變更可用性複本的叢集內容  
下列範例會變更 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體的 HADR 叢集內容。 為了指定目的地 WSFC 叢集 `clus01`，此範例會指定完整叢集物件名稱 `clus01.xyz.com`。  
  
```sql  
ALTER SERVER CONFIGURATION SET HADR CLUSTER CONTEXT = 'clus01.xyz.com';  
```  
  
### <a name="setting-buffer-pool-extension-options"></a>設定緩衝集區延伸模組選項  
  
####  <a name="a-setting-the-buffer-pool-extension-option"></a><a name="BufferPoolExtension"></a> A. 設定緩衝集區延伸模組選項  
  
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 起)。    
  
下列範例會啟用緩衝集區延伸模組選項並指定檔案名稱和大小。  
  
```sql  
ALTER SERVER CONFIGURATION   
SET BUFFER POOL EXTENSION ON  
    (FILENAME = 'F:\SSDCACHE\Example.BPE', SIZE = 50 GB);  
```  
  
#### <a name="b-modifying-buffer-pool-extension-parameters"></a>B. 修改緩衝集區延伸模組參數  
下列範例會修改緩衝集區延伸模組檔案的大小。 在修改任何參數之前，必須先停用緩衝集區延伸模組選項。  
  
```sql  
ALTER SERVER CONFIGURATION   
SET BUFFER POOL EXTENSION OFF;  
GO  
EXEC sp_configure 'max server memory (MB)', 12000;  
GO  
RECONFIGURE;  
GO  
ALTER SERVER CONFIGURATION  
SET BUFFER POOL EXTENSION ON  
    (FILENAME = 'F:\SSDCACHE\Example.BPE', SIZE = 60 GB);  
GO   
```  

### <a name="setting-in-memory-database-options"></a><a name="MemoryOptimized"></a> 設定記憶體內部資料庫選項

**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 起)。

#### <a name="a-enable-all-in-memory-database-features-with-default-options"></a>A. 使用預設選項啟用所有記憶體內部資料庫功能

```sql
ALTER SERVER CONFIGURATION SET MEMORY_OPTIMIZED ON;
GO
```

#### <a name="b-enable-memory-optimized-tempdb-metadata-using-the-default-resource-pool"></a>B. 使用預設資源集區啟用經記憶體最佳化的 tempdb 中繼資料

```sql
ALTER SERVER CONFIGURATION SET MEMORY_OPTIMIZED TEMPDB_METADATA = ON;
GO
```

#### <a name="c-enable-memory-optimized-tempdb-metadata-with-a-user-defined-resource-pool"></a>C. 以使用者定義之資源集區啟用經記憶體最佳化的 tempdb 中繼資料

```sql
ALTER SERVER CONFIGURATION SET MEMORY_OPTIMIZED TEMPDB_METADATA = ON (RESOURCE_POOL = 'pool_name');
GO
```

#### <a name="d-enable-hybrid-buffer-pool"></a>D. 啟用混合式緩衝集區

```sql
ALTER SERVER CONFIGURATION SET MEMORY_OPTIMIZED HYBRID_BUFFER_POOL = ON;
GO
```

## <a name="see-also"></a>另請參閱  
[Soft-NUMA &#40;SQL Server&#41;](../../database-engine/configure-windows/soft-numa-sql-server.md)   
[變更伺服器執行個體的 HADR 叢集內容 &#40;SQL Server&#41;](../../database-engine/availability-groups/windows/change-the-hadr-cluster-context-of-server-instance-sql-server.md)   
[sys.dm_os_schedulers &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-os-schedulers-transact-sql.md)   
[sys.dm_os_memory_nodes &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-os-memory-nodes-transact-sql.md)   
[sys.dm_os_buffer_pool_extension_configuration &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-os-buffer-pool-extension-configuration-transact-sql.md)   
[緩衝集區擴充](../../database-engine/configure-windows/buffer-pool-extension.md)  
  
