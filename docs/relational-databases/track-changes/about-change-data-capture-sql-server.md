---
description: 關於異動資料擷取 (SQL Server)
title: 關於異動資料擷取
ms.custom: seo-dt-2019
ms.date: 01/14/2019
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: vanto
ms.technology: ''
ms.topic: conceptual
helpviewer_keywords:
- change data capture [SQL Server], about
- change data capture [SQL Server]
- 22832 (Database Engine error)
ms.assetid: 7d8c4684-9eb1-4791-8c3b-0f0bb15d9634
author: rothja
ms.author: jroth
ms.openlocfilehash: f90e59f3e54b69a98e7ca058da971677faaafd0d
ms.sourcegitcommit: e5664d20ed507a6f1b5e8ae7429a172a427b066c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/19/2020
ms.locfileid: "97697112"
---
# <a name="about-change-data-capture-sql-server"></a>關於異動資料擷取 (SQL Server)
[!INCLUDE [SQL Server - ASDBMI](../../includes/applies-to-version/sql-asdbmi.md)]



  異動資料擷取會記錄套用至 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 資料表的插入、更新和刪除活動。 這樣會以方便取用的關聯式格式提供變更的詳細資料。 系統會針對修改的資料列擷取資料行資訊以及將變更套用至目標環境所需的中繼資料，並且將它們儲存在鏡像追蹤來源資料表之資料行結構的變更資料表中。 此外，系統會提供資料表值函式，讓取用者以有系統的方式存取異動資料。  
  
 此技術之目標資料取用者的理想範例為擷取、轉換和載入 (ETL) 應用程式。 ETL 應用程式會將變更資料從 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 來源資料表累加地載入資料倉儲或資料超市。 雖然在資料倉儲內的來源資料表表示法必須反映來源資料表中的變更，但是重新整理來源複本的端對端技術並不適用。 您需要的是結構化變更資料的可靠資料流，讓取用者可以將其套用到不同的資料目標表示法。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 異動資料擷取提供這種技術。  
  
## <a name="change-data-capture-data-flow"></a>異動資料擷取資料流程  
 下圖顯示異動資料擷取的主要資料流程。  
  
 ![異動資料擷取資料流程](../../relational-databases/track-changes/media/cdcdataflow.gif "異動資料擷取資料流程")  
  
 異動資料擷取的變更資料來源是 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 交易記錄。 當插入、更新和刪除作業套用至追蹤來源資料表時，描述這些變更的項目就會加入記錄。 此記錄會當做擷取程序的輸入。 這樣就會讀取記錄並將變更之相關資訊新增至追蹤資料表的相關聯變更資料表。 系統會提供一些函數，以便列舉指定之範圍內出現在變更資料表中的變更，並以篩選結果集的形式傳回此資訊。 應用程式處理序通常會使用篩選結果集，在某些外部環境中更新來源的表示法。  
  
## <a name="understanding-change-data-capture-and-the-capture-instance"></a>了解異動資料擷取和擷取執行個體  
 您必須先針對資料庫明確啟用異動資料擷取，然後才能追蹤該資料庫內部任何個別資料表的變更。 這項作業是使用 [sys.sp_cdc_enable_db](../../relational-databases/system-stored-procedures/sys-sp-cdc-enable-db-transact-sql.md)預存程序完成的。 啟用資料庫之後，您就可以使用 [sys.sp_cdc_enable_table](../../relational-databases/system-stored-procedures/sys-sp-cdc-enable-table-transact-sql.md)預存程序，將來源資料表識別為追蹤資料表。 當某個資料表啟用異動資料擷取時，系統就會建立相關聯的擷取執行個體，以便支援來源資料表中變更資料的散播。 此擷取執行個體包含一個變更資料表以及最多兩個查詢函數。 描述擷取執行個體之組態詳細資料的中繼資料會包含在變更資料擷取中繼資料資料表 **cdc.change_tables**、 **cdc.index_columns** 和 **cdc.captured_columns** 中。 您可以使用 [sys.sp_cdc_help_change_data_capture](../../relational-databases/system-stored-procedures/sys-sp-cdc-help-change-data-capture-transact-sql.md)預存程序來擷取這項資訊。  
  
 與擷取執行個體相關聯的所有物件都會建立在啟用資料庫的異動資料擷取結構描述中。 擷取執行個體名稱的需求包括，它必須是有效的物件名稱，而且它在資料庫擷取執行個體中必須是唯一的。 根據預設，此名稱是來源資料表的 \<*schema name*\_*table name*>。 其相關聯變更資料表的命名方式是將 **_CT** 附加至擷取執行個體名稱。 用來查詢所有變更之函數的命名方式是在擷取執行個體名稱前面加上 **fn_cdc_get_all_changes_** 。 如果擷取執行個體設定為支援 **net changes**，系統也會建立 **net_changes** 查詢函數，而且其命名方式是在擷取執行個體名稱前面加上 **fn_cdc_get_net_changes\_** 。  
  
## <a name="change-table"></a>變更資料表  
 異動資料擷取變更資料表的前五個資料行是中繼資料行。 這些資料行會提供與已記錄之變更相關的額外資訊。 其餘資料行則會鏡像來源資料表中識別之擷取資料行的名稱，通常也會鏡像其類型。 這些資料行會保存從來源資料表中蒐集的擷取資料行資料。  
  
 套用到來源資料表的每個插入或刪除作業會顯示成變更資料表中的單一資料列。 插入作業所產生之資料列的資料行包含插入之後的資料行值。 刪除作業所產生之資料列的資料行包含刪除之前的資料行值。 更新作業需要一個資料列項目來識別更新之前的資料行值，和第二個資料列項目來識別更新之後的資料行值。  
  
 變更資料表中的每個資料列也會包含其他中繼資料，以便允許解譯變更活動。 資料行 __$start_lsn 會識別指派給變更的認可記錄序號 (LSN)。 認可 LSN 會識別在相同交易中認可的變更，而且會為這些交易排序。 資料行 \_\_$seqval 可用於排序在相同交易中發生的其他變更。 資料行 \_\_$operation 會記錄與變更建立關聯的作業：1 = 刪除、2 = 插入、3 = 更新 (建立映像前)，以及 4 = 更新 (建立映像後)。 資料行 \_\_$update_mask 是變數位元遮罩，其中每個擷取資料行都有一個定義的位元。 若是插入和刪除項目，更新遮罩永遠會設定所有位元。 不過，更新資料列將僅會設定對應到已變更之資料行的位元。  
  
## <a name="change-data-capture-validity-interval-for-a-database"></a>資料庫的異動資料擷取有效性間隔  
 資料庫的異動資料擷取有效性間隔就是擷取執行個體可以使用變更資料的時間範圍。 有效性間隔會在您建立資料庫資料表的第一個擷取執行個體時開始，並且繼續到目前的時間。  
  
 如果您沒有定期且有系統地清除儲放在變更資料表中的資料，這項資料將無限制地成長。 異動資料擷取清除處理序會負責強制執行保留性清除原則。 首先，它會移動有效性間隔的低端點，以便滿足時間限制。 然後，它會移除過期的變更資料表項目。 根據預設，系統會保留三天內的資料。  
  
 對於高端點而言，因為擷取處理序會認可每一批新的變更資料，所以新項目會加入具有變更資料表項目之每個交易的 **cdc.lsn_time_mapping** 。 在對應資料表中，會保留認可記錄序號 (LSN) 和交易認可時間 (分別為 start_lsn 和 tran_end_time 資料行)。 在 **cdc.lsn_time_mapping** 中找到的最大 LSN 值代表資料庫有效性期間的上限標準。 其對應的認可時間會當做保留型清除用來計算新下限標準的基礎。  
  
 由於擷取程序會從交易記錄中擷取變更資料，因此來源資料表認可變更的時間與變更顯示在其相關聯變更資料表中的時間之間具有內建的延遲。 雖然這個延遲通常很短，但是請務必記住，在擷取處理序處理相關記錄項目之前，無法使用變更資料。  
  
## <a name="change-data-capture-validity-interval-for-a-capture-instance"></a>擷取執行個體的異動資料擷取有效性間隔  
 雖然資料庫有效性間隔與個別擷取執行個體的有效性間隔通常會一致，但是並非永遠如此。 當擷取處理序辨識擷取執行個體並且開始將相關聯的變更記錄至其變更資料表時，擷取執行個體的有效性間隔就會開始。 因此，如果您在不同的時間建立擷取執行個體，每個擷取執行個體一開始都會有不同的低端點。 [sys.sp_cdc_help_change_data_capture](../../relational-databases/system-stored-procedures/sys-sp-cdc-help-change-data-capture-transact-sql.md) 所傳回之結果集的 start_lsn 資料行會顯示每個已定義之擷取執行個體的目前低端點。 當清除處理序清除變更資料表項目時，它就會調整所有擷取執行個體的 start_lsn 值，以便反映可用變更資料的新下限標準。 只有 start_lsn 值目前小於新下限標準的這些擷取執行個體才會進行調整。 經過一段時間後，如果沒有建立任何新的擷取執行個體，所有個別執行個體的有效性間隔通常會與資料庫有效性間隔一致。  
  
 有效性間隔對於變更資料的取用者很重要，因為擷取執行個體的目前異動資料擷取有效性間隔必須完全涵蓋要求的擷取間隔。 如果擷取間隔的低端點位於有效性間隔低端點的左邊，可能會由於積極的清除而遺失變更資料。 如果擷取間隔的高端點位於有效性間隔高端點的右邊，表示擷取處理序尚未完全處理擷取間隔所代表的時間週期，而且也可能會遺失變更資料。  
  
 [sys.fn_cdc_get_min_lsn](../../relational-databases/system-functions/sys-fn-cdc-get-min-lsn-transact-sql.md) 函數是用來擷取擷取執行個體的目前最小 LSN，而 [sys.fn_cdc_get_max_lsn](../../relational-databases/system-functions/sys-fn-cdc-get-max-lsn-transact-sql.md) 則是用來擷取目前最大 LSN 值。 查詢變更資料時，如果指定的 LSN 範圍並未落在這兩個 LSN 值之內，異動資料擷取查詢函數將會失敗。  
  
## <a name="handling-changes-to-source-tables"></a>處理來源資料表的變更  
 對於下游取用者而言，要容納追蹤之來源資料表的資料行變更是個困難的問題。 雖然針對來源資料表啟用異動資料擷取無法避免這類 DDL 變更發生，但是異動資料擷取有助於減少對於取用者的影響，因為它會讓透過 API 傳回的傳遞結果集維持不變，即使基礎來源資料表的資料行結構變更也一樣。 這個固定的資料行結構也會反映在已定義之查詢函數所存取的基礎變更資料表中。  
  
 為了容納固定資料行結構的變更資料表，當來源資料表啟用異動資料擷取時，負責擴展變更資料表的擷取處理序將會忽略並未識別為待擷取的任何新資料行。 如果卸除了某個追蹤資料行，就會在後續變更項目中，針對此資料行提供 Null 值。 不過，如果現有的資料行進行資料類型的變更，此變更就會傳播至變更資料表，以便確保擷取機制不會將資料遺失導入追蹤資料行。 此外，擷取處理序也會將針對追蹤資料表之資料行結構偵測到的任何變更公佈至 cdc.ddl_history 資料表。 想要收到可能必須在下游應用程式中完成之調整警示的取用者會使用 [sys.sp_cdc_get_ddl_history](../../relational-databases/system-stored-procedures/sys-sp-cdc-get-ddl-history-transact-sql.md)預存程序。  
  
 一般而言，當 DDL 變更套用至相關聯的來源資料表時，目前擷取執行個體將繼續保留其外觀。 不過，反映新資料行結構的資料表將無法建立第二個擷取執行個體。 這樣會讓擷取處理序在具有兩種不同資料行結構的兩個不同變更資料表中，針對相同的來源資料表進行變更。 因此，當某個變更資料表可以繼續滿足目前運作中的程式時，第二個變更資料表可以驅動嘗試併入新資料行資料的開發環境。 允許擷取機制一前一後擴展這兩個變更資料表是表示，系統可以完成這兩個資料表之間的轉換，而不會遺失變更資料。 當兩個異動資料擷取時間表重疊時，就可能會發生這種情況。 完成轉換之後，就可以移除已經過時的擷取執行個體。  
  
> [!NOTE]  
>  可同時與單一來源資料表相關聯的擷取執行個體數目上限是二。  
  
## <a name="relationship-between-the-capture-job-and-the-transactional-replication-logreader"></a>擷取作業與異動複寫 Logreader 之間的關聯性  
 異動資料擷取處理序的邏輯內嵌在預存程序 [sp_replcmds](../../relational-databases/system-stored-procedures/sp-replcmds-transact-sql.md)中，此預存程序是建立成 sqlservr.exe 一部分的內部伺服器函數，而且也會由異動複寫用來從交易記錄中收集變更。 當您針對某個資料庫單獨啟用異動資料擷取時，就會將異動資料擷取 SQL Server Agent 擷取作業建立成叫用 sp_replcmds 的工具。 如果同時存在複寫，交易式 Logreader 就會單獨用來滿足這兩個取用者的變更資料需求。 當您針對相同的資料庫同時啟用複寫和異動資料擷取時，這項策略可大幅減少記錄競爭的情況。  
  
 每當啟用異動資料擷取之資料庫的複寫狀態變更時，就會自動在這兩種擷取變更資料的作業模式之間切換。  
  
> [!IMPORTANT]  
>  擷取邏輯的兩個執行個體都需要 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent 處於執行中狀態，才能讓處理序執行。  
  
 擷取程序的主要工作是掃描記錄，並且將資料行資料和交易相關的資訊寫入異動資料擷取變更資料表。 為了確保它所擴展的所有異動資料擷取變更資料表在交易方面具有一致的界限，擷取處理序會針對每個掃描循環開啟並認可自己的交易。 它會偵測出資料表最近啟用異動資料擷取的時間，並且自動將它們加入目前正在記錄中監視變更項目的資料表集合。 同樣地，它也會偵測出停用異動資料擷取，進而從目前正在監視變更資料的資料表集合中移除來源資料表。 當某個記錄區段的處理完成時，擷取處理序就會發出伺服器記錄截斷邏輯的信號，而此邏輯會使用這項資訊來識別適合用於截斷的記錄項目。  
  
> [!NOTE]  
>  啟用異動資料擷取的資料庫時，即使復原模式設定為簡單復原，在擷取處理序蒐集到所有標示為待擷取的變更之前，記錄截斷點將不會前進。 如果擷取處理序並未執行，而且存在要蒐集的變更，則執行 CHECKPOINT 將不會截斷記錄。  
  
 擷取處理序也會用來維護有關追蹤資料表之 DDL 變更的記錄。 每當卸除啟用異動資料擷取的資料庫或資料表，或是加入、修改或卸除啟用異動資料擷取之資料表的資料行時，與異動資料擷取相關聯的 DDL 陳述式就會在資料庫交易記錄中建立項目。 擷取處理序會先處理這些記錄項目，然後再將相關聯的 DDL 事件公佈至 cdc.ddl_history 資料表。 您可以使用 [sys.sp_cdc_get_ddl_history](../../relational-databases/system-stored-procedures/sys-sp-cdc-get-ddl-history-transact-sql.md)預存程序來取得有關影響追蹤資料表之 DDL 事件的相關資訊。  
  
## <a name="change-data-capture-agent-jobs"></a>異動資料擷取代理程式作業  
 通常有兩個 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent 作業會與啟用異動資料擷取的資料庫相關聯：用來擴展資料庫變更資料表的作業，以及負責變更資料表清除的作業。 這兩個作業都包含執行 [!INCLUDE[tsql](../../includes/tsql-md.md)] 命令的單一步驟。 所叫用的 [!INCLUDE[tsql](../../includes/tsql-md.md)] 命令是異動資料擷取定義的預存程序，可實作此作業的邏輯。 當資料庫的第一個資料表啟用異動資料擷取時，系統就會建立這些作業。 系統一定會建立清除作業。 但是，只有當資料庫沒有任何已定義的交易式發行集時，才會建立擷取作業。 當您針對資料庫同時啟用異動資料擷取和異動複寫時，系統也會建立擷取作業，而且會移除交易式 Logreader 作業，因為資料庫不再含有已定義的發行集。  
  
 擷取和清除作業都是使用預設參數建立的。 擷取作業會立即啟動。 它會連續執行，而且在每個掃描循環中最多可以處理 1000 筆交易 (循環之間等候 5 秒)。 清除作業則在每天早上 2 點執行。 它會保留變更資料表項目長達 4320 分鐘或 3 天，而且單一刪除陳述式最多可以移除 5000 個項目。  
  
 當您針對資料庫停用異動資料擷取時，系統就會移除異動資料擷取代理程式作業。 當第一個發行集加入至資料庫，而且同時啟用異動資料擷取和異動複寫時，也可以移除擷取作業。  
  
 就內部而言，異動資料擷取代理程式作業是分別使用 [sys.sp_cdc_add_job](../../relational-databases/system-stored-procedures/sys-sp-cdc-add-job-transact-sql.md) 和 [sys.sp_cdc_drop_job](../../relational-databases/system-stored-procedures/sys-sp-cdc-drop-job-transact-sql.md)預存程序建立和卸除的。 系統也會公開這些預存程序，讓管理員能夠控制這些作業的建立和移除。  
  
 管理員對於異動資料擷取代理程式作業的預設組態沒有明確的控制權。 [sys.sp_cdc_change_job](../../relational-databases/system-stored-procedures/sys-sp-cdc-change-job-transact-sql.md) 預存程序的提供目的是允許修改預設組態參數。 此外， [sys.sp_cdc_help_jobs](../../relational-databases/system-stored-procedures/sys-sp-cdc-help-jobs-transact-sql.md) 預存程序則是允許檢視目前的組態參數。 擷取作業和清除作業都會在啟動時從 msdb.dbo.cdc_jobs 資料表中擷取組態參數。 使用 [sys.sp_cdc_change_job](../../relational-databases/system-stored-procedures/sys-sp-cdc-change-job-transact-sql.md) 針對這些值所做的任何變更要等到此作業停止並重新啟動時才會生效。  
  
 系統提供了兩個額外的預存程序，讓您能夠啟動和停止異動資料擷取代理程式作業： [sys.sp_cdc_start_job](../../relational-databases/system-stored-procedures/sys-sp-cdc-start-job-transact-sql.md) 和 [sys.sp_cdc_stop_job](../../relational-databases/system-stored-procedures/sys-sp-cdc-stop-job-transact-sql.md)。  
  
> [!NOTE]  
>  啟動和停止擷取作業不會導致變更資料遺失。 它只會讓擷取處理序目前無法在記錄中掃描要儲放在變更資料表中的變更項目。 避免記錄掃描在尖峰要求期間增加負載的合理策略是停止擷取作業，然後在要求降低時重新啟動它。  
  
 這兩個 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent 作業都設計成具備足夠的彈性而且可充分地進行設定，以便符合異動資料擷取環境的基本需求。 不過，在這兩種情況下，系統已經公開提供核心功能的基礎預存程序，方便您進一步自訂。  
  
 以 NETWORK SERVICE 帳戶身分執行 Database Engine 服務或 SQL Server Agent 服務時，異動資料擷取無法正確運作。 這樣可能會造成錯誤 22832。  
 
## <a name="working-with-database-and-table-collation-differences"></a>使用資料庫和資料表定序差異

請務必注意，在資料庫與設定進行異動資料擷取的資料表資料行之間有不同的定序。 CDC 會使用暫時儲存體來填入側邊資料表。 如果資料表的 CHAR 或 VARCHAR 資料行具有與資料庫定序不同的定序，而且如果這些資料行儲存非 ASCII 字元 (例如雙位元組 DBCS 字元)，則 CDC 可能無法保存與基底資料表中資料一致的已變更資料。 原因是過度儲存體變數不能有與其建立關聯的定序。

請考慮下列其中一種方法來確保變更擷取資料已與基底資料表一致：

- 將 NCHAR 或 NVARCHAR 資料類型用於包含非 ASCII 資料的資料行。

- 或者，對於資料行和資料庫使用相同的定序。

例如，如果您有一個使用 SQL_Latin1_General_CP1_CI_AS 定序的資料庫，請考慮使用下表：

```sql
CREATE TABLE T1( 
     C1 INT PRIMARY KEY, 
     C2 VARCHAR(10) collate Chinese_PRC_CI_AI)
```

CDC 可能無法擷取資料行 C2 的二進位資料，因為其定序不同 (Chinese_PRC_CI_AI)。 使用 NVARCHAR 避免這個問題：

```sql
CREATE TABLE T1( 
     C1 INT PRIMARY KEY, 
     C2 NVARCHAR(10) collate Chinese_PRC_CI_AI --Unicode data type, CDC works well with this data type)
```

## <a name="limitations"></a>限制

異動資料擷取具有下列限制： 

**Linux**：   
Linux 上的 SQL Server 2017 (從 CU18 開始)，以及 Linux 上的 SQL Server 2019 現已支援 CDC。

**資料行存放區索引**   
無法在具有叢集資料行存放區索引的資料表上啟用變更資料擷取。 從 SQL Server 2016 開始，您可以在具有非叢集資料行存放區索引的資料表上啟用此功能。

**使用變數進行資料分割切換**   
針對 `ALTER TABLE ... SWITCH TO ... PARTITION ...` 陳述式，不支援在具有異動資料擷取 (CDC) 的資料庫或資料表上使用變數進行資料分割切換。 請參閱[分割區切換限制](../replication/publish/replicate-partitioned-tables-and-indexes.md#replication-support-for-partition-switching)以深入了解。 



## <a name="see-also"></a>另請參閱  
 [追蹤資料變更 &#40;SQL Server&#41;](../../relational-databases/track-changes/track-data-changes-sql-server.md)   
 [啟用和停用異動資料擷取 &#40;SQL Server&#41;](../../relational-databases/track-changes/enable-and-disable-change-data-capture-sql-server.md)   
 [使用變更資料 &#40;SQL Server&#41;](../../relational-databases/track-changes/work-with-change-data-sql-server.md)   
 [管理和監視異動資料擷取 &#40;SQL Server&#41;](../../relational-databases/track-changes/administer-and-monitor-change-data-capture-sql-server.md)  
  
  
