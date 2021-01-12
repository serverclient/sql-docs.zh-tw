---
title: 新功能
description: 查看 Microsoft Analytics Platform System 中的新功能，這是裝載 MPP SQL Server 平行處理資料倉儲的內部部署應用裝置。
author: mzaman1
ms.prod: sql
ms.technology: data-warehouse
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: murshedz
ms.reviewer: martinle
ms.custom: seo-dt-2019
ms.openlocfilehash: 004550d95ca69bae45c518b8054886e635e829df
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98091781"
---
# <a name="whats-new-in-analytics-platform-system-a-scale-out-mpp-data-warehouse"></a>Analytics Platform System 的新功能，擴充 MPP 資料倉儲
請參閱 Microsoft Analytics Platform System (AP) 最新設備更新的新功能。 AP 是一個將 MPP SQL Server 平行處理資料倉儲的相應放大內部部署設備。 

::: moniker range=">= aps-pdw-2016-au7 "
<a name="h2-aps-cu7.7"></a>
## <a name="aps-cu77"></a>APS CU7.7
發行日期-2020 年11月

### <a name="scvmm2016"></a>SCVMM2016
AP CU 7.7 軟體將 VMM VM 升級至 Windows Server 2016 並安裝 SCVMM2016。 目前使用中的 SCVMM 2012 R2 的生命循環結束日期為2022年7月。 需要較新的 SCVMM，才能讓 CU 7.7 強制升級。 客戶呼籲要儘快升級至 CU 7.7。

### <a name="ssis-destination-adapter-for-sql-server-2019-as-target"></a>作為目標的 SQL Server 2019 的 SSIS 目的地介面卡
您可以從 [下載網站](https://www.microsoft.com/download/details.aspx?id=57472)下載支援 SQL Server 2019 作為部署目標的新 ap SSIS 目的地介面卡。

<a name="h2-aps-cu7.6"></a>
## <a name="aps-cu76"></a>APS CU7.6
發行日期-2020 年4月

### <a name="rename-column"></a>重新命名資料行
升級至 CU 7.6 之後，客戶將能夠重新命名使用者建立之資料表的資料行。 如需語法、範例、限制和詳細資訊，請參閱 [ (transact-sql) 的重新命名 ](../t-sql/statements/rename-transact-sql.md) 。

### <a name="alter-view"></a>Alter view
客戶現在將能夠改變 views。 如需詳細資訊，請參閱 [ALTER VIEW (transact-sql) ](../t-sql/statements/alter-view-transact-sql.md) 。

<a name="h2-aps-cu7.5"></a>
## <a name="aps-cu75"></a>APS CU7.5
發行日期-2019 年9月

### <a name="alter-external-data-source"></a>Alter External Data Source
客戶將能夠使用 CU 7.5 更新來改變外部資料源定義。 具有 Hadoop 名稱節點高可用性的客戶現在可以改變數據源，以便在發生容錯移轉時變更引數。 若為 AP，則只能變更位置、RESOURCE_MANAGER_LOCATION 和認證。 如需詳細資訊，請參閱 [alter external data source](../t-sql/statements/alter-external-data-source-transact-sql.md?view=sql-server-2017&preserve-view=true) 。

### <a name="cdh-515-and-516-support-with-polybase"></a>PolyBase 的 CDH 5.15 和5.16 支援
具有 CU 7.5 更新之 AP 上的 PolyBase 現在支援從 Cloudera CDH 5.15 和5.16 版的 Hadoop 散發套件。 針對 CDH 5.x 版本，請使用選項6。 

### <a name="try_convert-and-try_cast-support"></a>Try_Convert 與 Try_Cast 支援
CU 7.5 AP 現在支援 [TRY_CAST](../t-sql/functions/try-cast-transact-sql.md?view=sql-server-2017&preserve-view=true) 和 [TRY_CONVERT](../t-sql/functions/try-convert-transact-sql.md?view=sql-server-2017&preserve-view=true) tsql 函數。 如果轉換成功，則這兩個函式都會傳回轉換成指定之資料類型的值;否則，會傳回 null。

<a name="h2-aps-cu7.4"></a>
## <a name="aps-cu74"></a>APS CU7.4
發行日期-5 月2019

### <a name="loading-large-rows-with-dwloader"></a>使用 dwloader 載入大型資料列
從 AP CU 7.4 開始，客戶將能夠使用新的 dwloader，將資料列載入到大於 32 KB (32768 位元組) 的資料表中。 新的 dwloader 支援-l 參數，此參數採用介於32768和 33554432 (（位元組）之間的整數值) 載入大於 32 KB 的資料列。 當載入大型資料列時，請只使用此選項 (大於 32 KB) 因為此參數會在用戶端和伺服器上配置更多記憶體，而且可能會降低負載。 您可以從 [下載網站](https://www.microsoft.com/download/details.aspx?id=57472)下載新的 dwloader。  

### <a name="hdp-30-and-31-support-with-polybase"></a>PolyBase 的 HDP 3.0 和3.1 支援
AP 上的 PolyBase 現在可透過此更新來支援 HDP 3.0 和3.1。 針對 HDP 3.x 版本，請使用選項7。 如需詳細資訊，請參閱 [PolyBase 連線性](../database-engine/configure-windows/polybase-connectivity-configuration-transact-sql.md) 頁面。

### <a name="utf16-file-support-with-polybase"></a>使用 PolyBase 的 UTF16 檔案支援
PolyBase 現在支援讀取 UTF16 (LE) 編碼的帶分隔符號文字檔。 請參閱建立設定詳細資料的 [外部檔案格式](../t-sql/statements/create-external-file-format-transact-sql.md) 。 

<a name="h2-aps-cu7.3"></a>
## <a name="aps-cu73"></a>APS CU7.3
發行日期-2018 年12月

### <a name="common-subexpression-elimination"></a>共通子運算式刪除
AP CU 7.3 利用 SQL 查詢最佳化工具中常見的子運算式消除來改善查詢效能。 改進以兩種方式改善查詢。 第一個優點是識別並消除這類運算式有助於減少 SQL 編譯時間的能力。 第二個和更重要的優點是，這些多餘子運算式的資料移動作業已被刪除，因此查詢的執行時間會變得更快。 您可以在 [這裡](common-sub-expression-elimination.md)找到這項功能的詳細說明。

### <a name="aps-informatica-connector-for-informatica-1020-published"></a>已發佈適用于 Informatica 10.2.0 的 AP Informatica 連接器
我們發行了新版本的 Informatica 連接器，適用于搭配 Informatica 版本10.2.0 和10.2.0 修補程式1的 AP。 您可以從 [下載網站](https://www.microsoft.com/download/details.aspx?id=57472)下載新的連接器。
> [!NOTE]
> 適用于 Informatica 10.2.0 或10.2.0 修復程式1的 AP Informatica 連接器無法在嚴格的 TLS 1.2 上運作，而且需要 TLS 1.0 和1.1 才能完全正常運作。

#### <a name="supported-versions"></a>支援的版本

| AP 版本 | Informatica PowerCenter | 驅動程式 |
|:---|:---|:---|
| APS 2016 | 9.6.1 | SQL Server Native Client 11. x |
| AP 2016 和更新版本 | 10.2.0、10.2.0 修正程式1 | SQL Server Native Client 11. x |

<a name="h2-aps-cu7.2"></a>
## <a name="aps-cu72"></a>APS CU7.2
發行日期-2018 年10月

### <a name="support-for-tls-12"></a>支援 TLS 1.2
AP CU 7.2 支援 TLS 1.2。 用戶端電腦到 AP 和在節點間通訊的 AP 現在可以設定為僅透過 TLS 1.2 進行通訊。 安裝在用戶端電腦上的 SSDT、SSIS 和 Dwloader 等工具，設定為僅透過 TLS 1.2 進行通訊，現在可以使用 TLS 1.2 連接至 AP。 根據預設，AP 將支援所有 TLS (1.0、1.1 和 1.2) 版本，以提供回溯相容性。 如果您想要將您的 AP 設備設定為嚴格使用 TLS 1.2，您可以藉由變更登錄設定來執行此動作。 

如需詳細資訊，請參閱 [在 ap 上設定 tls 1.2](configure-tls12-aps.md)。

### <a name="hadoop-encryption-zone-support-for-polybase"></a>PolyBase 的 Hadoop 加密區域支援
PolyBase 現在可以與 Hadoop 加密區域進行通訊。 請參閱 [設定 Hadoop 安全性](polybase-configure-hadoop-security.md#encryptionzone)所需的 ap 設定變更。

### <a name="insert-select-maxdop-options"></a>Insert-Select maxdop 選項
我們新增了一 [項功能參數](appliance-feature-switch.md) ，可讓您挑選大於1的 maxdop 設定來進行插入選擇作業。 您現在可以將 maxdop 設定設定為0、1、2或4。 預設值是 1。

> [!IMPORTANT]  
> 增加 maxdop 有時可能會導致較慢的作業或鎖死錯誤。 如果發生這種情況，請將設定變更回 maxdop 1，然後再次嘗試操作。

### <a name="columnstore-index-health-dmv"></a>資料行存放區索引健全狀況 DMV
您可以使用 **dm_pdw_nodes_db_column_store_row_group_physical_stats** dmv 來查看資料行存放區索引健全狀況資訊。 您可以使用下列視圖來判斷片段，並決定何時要重建或重新組織資料行存放區索引。

```sql
create view dbo.vCS_rg_physical_stats
as 
with cte
as
(
select   tb.[name]                    AS [logical_table_name]
,        rg.[row_group_id]            AS [row_group_id]
,        rg.[state]                   AS [state]
,        rg.[state_desc]              AS [state_desc]
,        rg.[total_rows]              AS [total_rows]
,        rg.[trim_reason_desc]        AS trim_reason_desc
,        mp.[physical_name]           AS physical_name
FROM    sys.[schemas] sm
JOIN    sys.[tables] tb               ON  sm.[schema_id]          = tb.[schema_id]                             
JOIN    sys.[pdw_table_mappings] mp   ON  tb.[object_id]          = mp.[object_id]
JOIN    sys.[pdw_nodes_tables] nt     ON  nt.[name]               = mp.[physical_name]
JOIN    sys.[dm_pdw_nodes_db_column_store_row_group_physical_stats] rg      ON  rg.[object_id]     = nt.[object_id]
                                                                            AND rg.[pdw_node_id]   = nt.[pdw_node_id]
                                        AND rg.[pdw_node_id]    = nt.[pdw_node_id]                                          
)
select *
from cte;
```

### <a name="polybase-date-range-increase-for-orc-and-parquet-files"></a>ORC 和 Parquet 檔案的 PolyBase 日期範圍增加
使用 PolyBase 讀取、匯入和匯出日期資料類型現在支援 ORC 和 Parquet 檔案類型在1970-01-01 之前和2038-01-20 之後的日期。

### <a name="ssis-destination-adapter-for-sql-server-2017-as-target"></a>作為目標的 SQL Server 2017 的 SSIS 目的地介面卡
您可以從 [下載網站](https://www.microsoft.com/download/details.aspx?id=57472)下載支援 SQL Server 2017 作為部署目標的新 ap SSIS 目的地介面卡。

<a name="h2-aps-cu7.1"></a>
## <a name="aps-cu71"></a>APS CU7.1
發行日期-2018 年7月

### <a name="dbcc-commands-do-not-consume-concurrency-slots-behavior-change"></a>DBCC 命令不會使用平行存取插槽 (行為變更) 
AP 支援 T-sql [dbcc 命令](../t-sql/database-console-commands/dbcc-transact-sql.md) 的子集，例如 [dbcc DROPCLEANBUFFERS](../t-sql/database-console-commands/dbcc-dropcleanbuffers-transact-sql.md)。 之前，這些命令會耗用[並行位置](./workload-management.md?view=aps-pdw-2016-au7&preserve-view=true&#concurrency-slots)，減少可執行的使用者載入/查詢數量。 `DBCC`命令現在會在本機佇列中執行，而不會使用使用者平行存取位置來改善整體查詢執行效能。

### <a name="replaces-some-metadata-calls-with-catalog-objects"></a>以目錄物件取代某些中繼資料呼叫
使用目錄物件進行中繼資料呼叫，而不使用 SMO，在 AP 中顯示效能改進。 從 CU 7.1 開始，其中有些中繼資料呼叫現在預設會使用目錄物件。 如果使用中繼資料查詢的客戶遇到任何問題，則 [功能切換](appliance-feature-switch.md) 可關閉此行為。

### <a name="bug-fixes"></a>錯誤修正
我們已使用 AP CU 7.1 升級至 SQL Server 2016 SP2 CU2。 升級會修正以下所述的一些問題。

| 標題 | 描述 |
|:---|:---|
| **可能的元組移動器鎖死** |升級會修正分散式交易和元組移動背景執行緒中長期鎖死的可能性。 安裝 CU 7.1 之後，使用 TF634 來停止元組移動器的客戶，SQL Server 啟動參數或全域追蹤旗標，可以安全地將它移除。 | 
| **某些延遲/潛在客戶查詢失敗** |在對 CCI 資料表具有可能發生錯誤之嵌套延遲/潛在客戶函式的特定查詢，現在已透過此升級來修正。 | 


<a name="h2-aps-au7"></a>
## <a name="aps-au7"></a>APS AU7
發行日期-5 月2018

AP 2016 是升級至 AU7 的先決條件。 以下是 AP AU7 中的新功能：

### <a name="auto-create-and-auto-update-statistics"></a>自動建立和自動更新統計資料
根據預設，AP AU7 會自動建立和更新統計資料。 若要更新統計資料設定，系統管理員可以使用 [Configuration Manager](appliance-configuration.md#CMTasks)中的新功能切換功能表項目。 [功能參數](appliance-feature-switch.md)會控制統計資料的自動建立、自動更新和非同步更新行為。 您也可以使用 [ALTER DATABASE (Parallel Data 倉儲) ](../t-sql/statements/alter-database-transact-sql.md?tabs=sqlpdw) 語句來更新統計資料設定。

### <a name="t-sql"></a>T-SQL
@var現在支援 Select。 如需詳細資訊，請參閱 [選取本機變數](../t-sql/language-elements/select-local-variable-transact-sql.md) 

現在支援查詢提示雜湊和 ORDER 群組。 如需詳細資訊，請參閱 [ (transact-sql) 的提示-查詢](../t-sql/queries/hints-transact-sql-query.md)

### <a name="feature-switch"></a>功能切換
AP AU7 引進 [Configuration Manager](launch-the-configuration-manager.md)中的功能切換。 >autostatsenabled 和 DmsProcessStopMessageTimeoutInSeconds 現在是可由系統管理員變更的可設定選項。

### <a name="known-issues"></a>已知問題
使用 AP AU7 software 時，會提供 Intel BIOS 更新，以修正述為 *推測性執行端通道攻擊* 的問題。 攻擊的目標是要利用所謂的 *Spectre 和 Meltdown 弱點*。 雖然與 AP 一起封裝，但 BIOS 更新是以手動方式安裝，而不是在 AU7 software 安裝的 AP 中進行安裝。

Microsoft 建議所有客戶安裝 BIOS 更新。 Microsoft 已測量核心虛擬位址遮蔽 (KVAS) 、核心頁面資料表間接取值 (KPTI) 和間接分支預測風險降低 (IBP) 在各種環境中的各種 SQL 工作負載的影響。 度量發現某些工作負載的效能大幅降低。 根據結果，建議您先測試啟用 BIOS 更新的效能效果，再將它們部署在生產環境中。 請參閱 [此處](https://support.microsoft.com/help/4073225/guidance-protect-sql-server-against-spectre-meltdown)的 SQL Server 指引。

::: moniker-end
::: moniker range=">= aps-pdw-2016 "
<a name="h2-aps-au6"></a>
## <a name="aps-2016"></a>APS 2016
本節說明 AP 2016-AU6 的新功能。

### <a name="sql-server-2016"></a>SQL Server 2016

AP AU6 會在最新的 SQL Server 2016 版本上執行，並使用預設的資料庫相容性層級130。 SQL Server 2016 可支援新功能，例如：

- 叢集資料行存放區索引的次要索引。
- PolyBase 的 Kerberos。

### <a name="t-sql"></a>T-SQL
AP AU6 支援這些 T-sql 相容性的增強功能。  這些額外的語言元素可讓您更輕鬆地從 SQL Server 和其他資料來源進行遷移。 

- 除了 Windows 定序之外，現在還支援資料[行層級的 SQL][]定序。
- 叢集資料行存放區[索引上的非叢集索引][]，可改善在叢集資料行存放區索引中搜尋特定值的查詢效能。 
- [SELECT...INTO][] 
- [sp_spaceused ( # B1 ][] 會顯示資料表或資料庫中所使用或保留的磁碟空間。
- [寬型資料表][] 支援與 SQL Server 2016 相同。 資料列大小的先前限制 32 K 已不存在。 

**資料類型**

- [VARCHAR (max) ][]、 [NVARCHAR (Max) ][] 和 [VARBINARY (max) ][]。 這些 LOB 資料類型的大小上限為 2 GB。 若要載入這些物件，請使用 [Bcp 公用程式][]。 PolyBase 和 dwloader 目前不支援這些資料類型。 
- [SYSNAME][]
- [唯一][]
- [數值][] 和十進位資料類型。

**視窗函式**

- SELECT 語句的 OVER 子句中的資料[列或範圍][]。
- [FIRST_VALUE][]
- [LAST_VALUE][]
- [CUME_DIST][]
- [PERCENT_RANK][]

**安全性功能**

- 總和[檢查碼 ( # B1][]和[BINARY_CHECKSUM ( # B3][]
- [HAS_PERMS_BY_NAME ( # B1 ][]

**其他函式**

- [NEWID ( # B1 ][]
- [RAND ( # B1 ][]

### <a name="polybasehadoop-enhancements"></a>PolyBase/Hadoop 增強功能

- 與 Hortonworks HDP 2.4 和 HDP 2.5 的相容性
- 透過資料庫範圍認證的 Kerberos 支援
- Azure 儲存體 Blob 的認證支援

### <a name="install-and-upgrade-enhancements"></a>安裝和升級增強功能

**企業架構更新** 將現有的應用裝置升級至 AP AU6 會安裝最新的固件和驅動程式更新，包括安全性修正程式。 

HPE 或 DELL 提供的新設備包含所有最新的更新，以及：

-  (Broadwell) 最新一代處理器支援
- 更新為 DDR4 Dimm
- 改善的 DIMM 輸送量

**整合**

-  (FQDN) 支援的完整功能變數名稱，可讓您將網域信任設定至設備。 
- 若要使用 FQDN，您必須在升級期間進行完整升級和加入宣告。 

**縮短停機時間** 安裝或升級至 AP AU6 的速度較快，且需要比舊版更少的停機時間。 若要減少停機時間，請安裝或升級： 

 - 使用包含所有更新到2016年6月的映射，簡化套用 WSUS 更新的程式
 - 使用驅動程式和固件更新來套用安全性更新
 - 將最新的修補程式和設備驗證公用程式放置在設備上 (PAV) ，讓它們準備好進行安裝，而不需要下載。

::: moniker-end

<!--
Link references to other articles in this same GitHub repo.

The link format that starts with '/sql/what-ever/my-artlcle' is not appropriate for common links within the same repo (as most of these link are).  The first couple links have been edited to show the proper syntax, but all other links in this article need to be similarly edited.
The proper formats have at least two big advantages.  One big advantage is that the proper formats enable the OPS Build system to detect broken links at Pull Request build time, instead of only later during run time.
-->
[database compatibility level 130]: ../t-sql/statements/alter-database-transact-sql-compatibility-level.md
[資料行層級 SQL 定序]: ~/relational-databases/collations/collation-and-unicode-support.md

[叢集資料行存放區索引上的非叢集索引]:/sql/t-sql/statements/create-index-transact-sql
[VARCHAR (MAX) ]:/sql/t-sql/data-types/char-and-varchar-transact-sql
[NVARCHAR (MAX) ]:/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql
[VARBINARY (MAX) ]:/sql/t-sql/data-types/binary-and-varbinary-transact-sql
[SYSNAME]:/sql/relational-databases/system-catalog-views/sys-types-transact-sql
[SELECT...INTO]:/sql/t-sql/queries/select-into-clause-transact-sql
[sp_spaceused ( # B1 ]:/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql
[寬型資料表]:/sql/sql-server/maximum-capacity-specifications-for-sql-server
[BULK INSERT]:/sql/t-sql/statements/bulk-insert-transact-sql
[bcp 公用程式]:/sql/tools/bcp-utility
[唯一]:/sql/t-sql/data-types/uniqueidentifier-transact-sql
[NUMERIC]:/sql/t-sql/data-types/decimal-and-numeric-transact-sql
[ROWS 或 RANGE]:/sql/t-sql/queries/select-over-clause-transact-sql
[FIRST_VALUE]:/sql/t-sql/functions/first-value-transact-sql
[LAST_VALUE]:/sql/t-sql/functions/last-value-transact-sql
[CUME_DIST]:/sql/t-sql/functions/cume-dist-transact-sql
[PERCENT_RANK]:/sql/t-sql/functions/percent-rank-transact-sql
[CHECKSUM ( # B1 ]:/sql/t-sql/functions/checksum-transact-sql
[BINARY_CHECKSUM ( # B1 ]:/sql/t-sql/functions/binary-checksum-transact-sql
[HAS_PERMS_BY_NAME ( # B1 ]:/sql/t-sql/functions/has-perms-by-name-transact-sql
[NEWID ( # B1 ]:/sql/t-sql/functions/newid-transact-sql
[RAND ( # B1 ]:/sql/t-sql/functions/rand-transact-sql


  

