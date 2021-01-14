---
title: dta 公用程式
description: DTA 公用程式是 Database Engine Tuning Advisor 的命令提示字元版本，可供在應用程式和指令碼中使用功能。
ms.prod: sql
ms.prod_service: sql-tools
ms.technology: tools-other
ms.topic: conceptual
helpviewer_keywords:
- physical design structures [SQL Server]
- command prompt utilities [SQL Server], dta
- dta utility
- tuning databases [SQL Server], Database Engine Tuning Advisor
- workloads [SQL Server], analyzing
- dta utility, about dta utility
- performance [SQL Server], Database Engine Tuning Advisor
- Database Engine Tuning Advisor [SQL Server], command prompt
- optimizing databases [SQL Server]
ms.assetid: a0b210ce-9b58-4709-80cb-9363b68a1f5a
author: markingmyname
ms.author: maghan
ms.reviewer: ''
ms.custom: seo-lt-2019
ms.date: 01/09/2017
ms.openlocfilehash: a80c3fc49962ad4a67430163f81d00b37c05329b
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98169216"
---
# <a name="dta-utility"></a>dta 公用程式

 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

**dta** 公用程式是 Database Engine Tuning Advisor 的命令提示字元版本。 **dta** 公用程式的設計，是為了讓您在應用程式和指令碼中使用 Database Engine Tuning Advisor 功能。  

如同 Database Engine Tuning Advisor， **dta** 公用程式也會分析工作負載，並提供實體設計結構的建議，以增進該工作負載的伺服器效能。 工作負載可能是計畫快取、 [!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)] 追蹤檔或資料表，也可能是 [!INCLUDE[tsql](../../includes/tsql-md.md)] 指令碼。 實體設計結構包括索引、索引檢視表及資料分割。 分析工作負載之後， **dta** 公用程式會產生資料庫實體設計的建議，且能夠產生必要的指令碼來實作建議。 您可以從命令提示字元，搭配 **-if** 或 **-it** 引數來指定工作負載。 您也可以從命令提示字元，搭配 **-ix** 引數來指定 XML 輸入檔。 在這個情況之下，工作負載指定在 XML 輸入檔中。  
  
## <a name="syntax"></a>語法  
  
```  
  
dta  
[ -? ] |  
[  
      [ -S server_name[ \instance ] ]  
      { { -U login_id [-P password ] } | -E  }  
      { -D database_name [ ,...n ] }  
      [ -d database_name ]   
      [ -Tl table_list | -Tf table_list_file ]  
      { -if workload_file | -it workload_trace_table_name  |   
        -ip | -iq }  
      { -ssession_name | -IDsession_ID }  
      [ -F ]  
      [ -of output_script_file_name ]  
      [ -or output_xml_report_file_name ]  
      [ -ox output_XML_file_name ]  
      [ -rl analysis_report_list [ ,...n ] ]  
      [ -ix input_XML_file_name ]  
      [ -A time_for_tuning_in_minutes ]  
      [ -n number_of_events ]
      [ -I time_window_in_hours ]  
      [ -m minimum_improvement ]  
      [ -fa physical_design_structures_to_add ]  
      [ -fi filtered_indexes]  
      [ -fc columnstore_indexes]  
      [ -fp partitioning_strategy ]  
      [ -fk keep_existing_option ]  
      [ -fx drop_only_mode ]  
      [ -B storage_size ]  
      [ -c max_key_columns_in_index ]  
      [ -C max_columns_in_index ]  
      [ -e | -e tuning_log_name ]  
      [ -N online_option]  
      [ -q ]  
      [ -u ]  
      [ -x ]  
      [ -a ]  
]  
```  
  
## <a name="arguments"></a>引數  
 **-?**  
 顯示使用資訊。  
  
 **-A** _time_for_tuning_in_minutes_  
 指定微調時間限制 (以分鐘為單位)。 **dta** 會利用指定的時間量來微調工作負載，以及產生含有建議的實體設計變更的指令碼。 依預設， **dta** 假設微調時間是 8 小時。 指定 0 代表微調時間無限制。 **dta** 可能會在時間限制過期之前，完成整個工作負載的微調。 不過，為了確保整個工作負載都能得到微調，我們建議您指定無限的微調時間 (-A 0)。  
  
 **-a**  
 不發出提示，直接微調工作負載並套用建議的內容。  
  
 **-B** _storage_size_  
 指定建議的索引和資料分割所能使用的最大空間 (以 MB 為單位)。 微調多個資料庫時，所有資料庫的建議內容都考量了空間的計算。 依預設， **dta** 會採用下列較小的儲存體大小：  
  
-   目前的原始資料大小的三倍，其中包括資料庫中各資料表的堆積和叢集索引的總大小。  
  
-   所有相連硬碟的可用空間，加上原始資料大小。  
  
 預設儲存體大小不包括非叢集索引和索引檢視。  
  
 **-C** _max_columns_in_index_  
 指定 **dta** 所提出之索引內的資料行數目上限。 最大值為 1024。 依預設，引數設定為 16。  
  
 **-c** _max_key_columns_in_index_  
 指定 **dta** 所提出之索引內索引鍵資料行數目上限。 預設值是 16，這是允許的最大值。 **dta** 也會考慮使用內含資料行建立索引。 內含資料行的索引建議可能超出這個引數所指定的資料行數目。  
  
 **-D** _database_name_  
 指定要微調的每個資料庫的名稱。 第一個資料庫是預設資料庫。 您可以指定多個資料庫，以逗號分隔各個資料庫名稱，例如：  
  
```  
dta -D database_name1, database_name2...  
```  
  
 另外，您也可以在各個資料庫名稱上使用 **-D** 引數來指定多個資料庫，例如：  
  
```  
dta -D database_name1 -D database_name2... n  
```  
  
 **-D** 引數是必要項目。 如果未指定 **-d** 引數， **dta** 一開始會連接到工作負載中第一個 `USE database_name` 子句所指定的資料庫。 如果工作負載中沒有明確的 `USE database_name` 子句，您就必須使用 **-d** 引數。  
  
 例如，如果您的工作負載沒有包含明確的 `USE database_name` 子句，而且您使用下列 **dta** 命令，就不會產生任何建議：  
  
```  
dta -D db_name1, db_name2...  
```  
  
 但是，如果您使用相同的工作負載，而且利用下列使用 **-d** 引數的 **dta** 命令，就會產生建議：  
  
```  
dta -D db_name1, db_name2 -d db_name1  
```  
  
 **-d** _database_name_  
 指定微調工作負載時， **dta** 所連接的第一個資料庫。 這個引數只能指定一個資料庫。 例如：  
  
```  
dta -d AdventureWorks2012 ...  
```  
  
 如果指定了多個資料庫名稱， **dta** 就會傳回錯誤。 **-d** 引數是選擇性的。  
  
 如果使用 XML 輸入檔，您可以利用位於 **TuningOptions** 元素下的 **DatabaseToConnect** 元素，來指定 **dta** 要連接的第一個資料庫。 如需詳細資訊，請參閱 [Database Engine Tuning Advisor](../../relational-databases/performance/database-engine-tuning-advisor.md)。  
  
 如果您只微調一個資料庫， **-d** 引數會提供類似 **sqlcmd** 公用程式中 **-d** 引數的功能，但它不會執行 USE *database_name* 陳述式。 如需詳細資訊，請參閱 [sqlcmd Utility](../../tools/sqlcmd-utility.md)。  
  
 **-E**  
 使用信任連接，不要求密碼。 必須使用指定登入識別碼的 **-E** 引數或 **-U** 引數。  
  
 **-e** _tuning_log_name_  
 指定 **dta** 用來記錄它無法微調之事件的資料表或檔案名稱。 資料表會建立在執行微調的伺服器中。  
  
 如果使用某份資料表，請依照下列格式來指定它的名稱： *[database_name].[owner_name].table_name*。 下表顯示各個參數的預設值：  
  
|參數|預設值|詳細資料|  
|---------------|-------------------|-------------|  
|*database_name*|使用 **-D** 選項指定的 *database_name*||  
|*owner_name*|**dbo**|*owner_name* 必須是 **dbo**。 如果指定了任何其他值， **dta** 的執行便會失敗並傳回錯誤。|  
|*table_name*|None||  
  
 如果使用檔案，請指定 .xml 副檔名。 例如，TuningLog.xml。  
  
> [!NOTE]  
>  如果工作階段遭到刪除， **dta** 公用程式就不會刪除使用者指定之微調記錄資料表的內容。 在微調超大型工作負載時，我們建議您對微調記錄指定資料表。 由於微調大型工作負載會產生大量微調記錄，因此，在使用資料表時可以更快刪除工作階段。  
  
 **-F**  
 允許 **dta** 覆寫現有的輸出檔。 如果已存在相同名稱的輸出檔，且未指定 **-F** ， **dta** 就會傳回錯誤。 您可以使用 **-F** 搭配 **-of**、 **-or** 或 **-ox**。  
  
 **-fa** _physical_design_structures_to_add_  
 指定 **dta** 應該在建議中包含的實體設計結構類型。 下表列出並說明這個引數所能指定的值。 未指定任何值時，**dta** 會使用預設的 **-fa IDX**。  
  
|值|描述|  
|-----------|-----------------|  
|IDX_IV|索引和索引檢視表。|  
|IDX|只有索引。|  
|IV|只有索引檢視表。|  
|NCL_IDX|只有非叢集索引。|  
  
 **-fi**  
 指定應為新建議考量篩選的索引。 如需詳細資訊，請參閱 [Create Filtered Indexes](../../relational-databases/indexes/create-filtered-indexes.md)。  
  
**-fc**  
 指定新建議需要考量的資料行存放區索引。 DTA 會同時考量叢集和非叢集資料行存放區索引。 如需相關資訊，請參閱    
[Database Engine Tuning Advisor (DTA) 中的資料行存放區索引建議](../../relational-databases/performance/columnstore-index-recommendations-in-database-engine-tuning-advisor-dta.md)。
 ||  
|-|  
|**適用對象**：[!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 及更新版本。|  

  
 **-fk** _keep_existing_option_  
 指定 **dta** 在產生建議時，必須保留的現有實體設計結構。 下表列出並說明這個引數所能指定的值：  
  
|值|描述|  
|-----------|-----------------|  
|無|無現有結構。|  
|ALL|所有現有結構。|  
|ALIGNED|所有資料分割對齊結構。|  
|CL_IDX|資料表的所有叢集索引。|  
|IDX|資料表的所有叢集和非叢集索引。|  
  
 **-fp** _partitioning_strategy_  
 指定是否應該分割 **dta** 所提出的新實體設計結構 (索引和索引檢視表) 及其分割方式。 下表列出並說明這個引數所能指定的值：  
  
|值|描述|  
|-----------|-----------------|  
|無|沒有資料分割。|  
|FULL|完整的資料分割 (選擇這個項目，可以增進效能)。|  
|ALIGNED|只有對齊的資料分割 (選擇這個項目，管理會更容易)。|  
  
 ALIGNED 表示在 **dta** 所產生的建議中，每個建議的索引都會完全依照定義索引之基礎資料表的相同方式進行分割。 索引檢視表中的非叢集索引會對齊索引檢視表。 這個引數只能指定一個值。 預設值是 **-fp NONE**。  
  
 **-fx** _drop_only_mode_  
 指定 **dta** 只考慮卸除現有的實體設計結構。 不考慮任何新的實體設計結構。 指定此選項時， **dta** 會評估現有實體設計結構的可用程度，並建議您卸除不常使用的結構。 這個引數沒有任何值。 它不能搭配 **-fa**、 **-fp** 或 **-fk ALL** 等引數一起使用。  
  
 **-ID** _session_ID_  
 指定微調工作階段的數值識別碼。 若未指定，則 **dta** 會產生一個識別碼。 您可以利用這個識別碼來檢視現有微調工作階段的資訊。 如果您沒有指定 **-ID** 值，就必須利用 **-s** 來指定工作階段名稱。  
  
 **-ip**  
 指定計畫快取可用做為工作負載。 針對明確選定的資料庫排名前 1000 個計畫快取事件，進行分析。 您可以利用 **-n** 選項變更此值。  

**-iq**  
 指定將查詢存放區作為工作負載。 系統會針對明確選定的資料庫來分析查詢存放區排名前 1000 個事件。 您可以利用 **-n** 選項變更此值。  如需詳細資訊，請參閱[查詢存放區](../../relational-databases/performance/how-query-store-collects-data.md)和[使用查詢存放區的工作負載微調資料庫](../../relational-databases/performance/tuning-database-using-workload-from-query-store.md)。
 ||  
|-|  
|**適用對象**：[!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 及更新版本。|  
     
 **-if** _workload_file_  
 指定微調輸入所用的工作負載檔案之路徑與名稱。 檔案必須是下列其中一種格式：.trc (SQL Server Profiler 追蹤檔)、.sql (SQL 檔案) 或 .log ([!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 追蹤檔)。 您必須指定一個工作負載檔案或一份工作負載資料表。  
  
 **-it** _workload_trace_table_name_  
 指定包含微調工作負載追蹤的資料表名稱。 請使用下列格式來指定名稱：[*database_name*] **.** [*owner_name*] **.** _table_name_。  
  
 下表顯示各項目的預設值：  
  
|參數|預設值|  
|---------------|-------------------|  
|*database_name*|使用 **-D** 選項指定的 *database_name*。|  
|*owner_name*|**dbo**。|  
|*table_name*|無。|  
  
> [!NOTE]  
>  *owner_name* 必須是 **dbo**。 如果指定了任何其他值， **dta** 的執行便會失敗並傳回錯誤。 另外，也請注意，您必須指定一份工作負載資料表，或指定一個工作負載檔案。  
  
 **-ix** _input_XML_file_name_  
 指定包含 **dta** 輸入資訊的 XML 檔案名稱。 這必須是符合 DTASchema.xsd 的有效 XML 文件。 微調選項的命令提示字元所指定的衝突引數會覆寫這個 XML 檔案中的對應值。 XML 輸入檔中以評估模式輸入的使用者指定組態是唯一例外。 例如，如果在 XML 輸入檔的 **Configuration** 元素中輸入了某項組態， **EvaluateConfiguration** 元素也指定成一個微調選項，XML 輸入檔所指定的微調選項會覆寫在命令提示字元之下輸入的任何微調選項。  

 **-k** _maxtotalindexes_  
 建議的索引數上限。  

 **-K** _maxtotalindexes_  
 每一資料表的索引數上限。

 **-m** _minimum_improvement_  
 指定建議的組態必須符合之最小改進百分比。  
  
 **-N** _online_option_  
 指定是否在線上建立實體設計結構。 下表列出和描述這個引數所能指定的值：  
  
|值|描述|  
|-----------|-----------------|  
|OFF|不能在線上建立任何建議的實體設計結構。|  
|開啟|可以在線上建立所有建議的實體設計結構。|  
|MIXED|Database Engine Tuning Advisor 嘗試建議在可能的情況下，能夠在線上建立的實體設計結構。|  
  
 如果在線上建立索引，就會在它的物件定義上附加 ONLINE = ON。  
  
 **-n** _number_of_events_  
 指定在工作負載中 **dta** 應微調的事件數目。 如果指定了此引數，且工作負載是包含持續時間資訊的追蹤檔， **dta** 就會依據持續時間的遞減順序來微調事件。 這個引數可用來比較實體設計結構的兩個組態。 若要比較兩個組態，請依照下列方式，先指定相同的微調事件數目給這兩個組態，之後，兩個組態都指定無限的微調時間：  
  
```  
dta -n number_of_events -A 0  
```  
  
 在這個情況下，指定無限微調時間 (`-A 0`) 非常重要。 否則，Database Engine Tuning Advisor 會預設 8 小時的微調時間。
 
 **-I** _time_window_in_hours_   
   指定查詢必須執行的時間範圍 (以小時為單位)，以供 DTA 在使用 **-iq** 選項時作為調整依據 (查詢存放區的工作負載)。 
```  
dta -iq -I 48  
```  
在此情況下，DTA 會將查詢存放區作為工作負載的來源，並只考慮過去 48 小時內執行的查詢。  
  ||  
|-|  
|**適用對象**：[!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 及更新版本。|  


  
 **-of** _output_script_file_name_  
 指定 **dta** 將建議以 [!INCLUDE[tsql](../../includes/tsql-md.md)] 指令碼形式，寫入指定的檔案名稱與目的地。  
  
 您可以搭配此選項來使用 **-F** 。 請確定檔案名稱沒有重複，特別是當您也正在使用 **-or** 和 **-ox** 時。  
  
 **-or** _output_xml_report_file_name_  
 指定 **dta** 將建議以 XML 格式寫入輸出報表。 如果提供了檔案名稱，建議便會寫入該目的地。 否則， **dta** 將使用該工作階段名稱來產生檔案名稱，並將其寫入目前的目錄。  
  
 您可以搭配此選項來使用 **-F** 。 請確定檔案名稱沒有重複，特別是當您也正在使用 **-of** 和 **-ox** 時。  
  
 **-ox** _output_XML_file_name_  
 指定 **dta** 將建議以 XML 檔案格式寫入提供的檔案名稱與目的地。 請確定 Database Engine Tuning Advisor 有寫入目的地目錄的權限。  
  
 您可以搭配此選項來使用 **-F** 。 請確定檔案名稱沒有重複，特別是當您也正在使用 **-of** 和 **-or** 時。  
  
 **-P** _password_  
 指定登入識別碼的密碼。 若未使用此選項， **dta** 將提示您輸入密碼。  
  
 **-q**  
 設定無訊息模式。 不會將任何資訊寫入主控台中，進度和標頭資訊都包括在內。  
  
 **-rl** _analysis_report_list_  
 指定要產生的分析報表清單。 下表列出這個引數所能指定的值：  
  
|值|Report|  
|-----------|------------|  
|ALL|所有分析報表|  
|STMT_COST|陳述式成本報表|  
|EVT_FREQ|事件頻率報表|  
|STMT_DET|陳述式詳細資料報表|  
|CUR_STMT_IDX|陳述式-索引關聯性報表 (目前組態)|  
|REC_STMT_IDX|陳述式-索引關聯性報表 (建議組態)|  
|STMT_COSTRANGE|陳述式成本範圍報表|  
|CUR_IDX_USAGE|索引使用方式報表 (目前的組態)|  
|REC_IDX_USAGE|索引使用方式報表 (建議的組態)|  
|CUR_IDX_DET|索引詳細資料報表 (目前的組態)|  
|REC_IDX_DET|索引詳細資料報表 (建議的組態)|  
|VIW_TAB|檢視表-資料表關聯性報表|  
|WKLD_ANL|工作負載分析報表|  
|DB_ACCESS|資料庫存取報表|  
|TAB_ACCESS|資料表存取報表|  
|COL_ACCESS|資料行存取報表|  
  
 請用逗號分隔各值以指定多份報表，例如：  
  
```  
... -rl EVT_FREQ, VIW_TAB, WKLD_ANL ...  
```  
  
 **-S** _server_name_[ *\instance*]  
 指定電腦名稱及要連接的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體。 若未指定 *server_name* ， **dta** 會連接至本機電腦上的預設 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體。 連接至具名的執行個體或透過網路從遠端電腦執行 **dta** 時，必須使用此選項。  
  
 **-s** _session_name_  
 指定微調工作階段的名稱。 若未指定 **-ID** ，則必須進行此步驟。  
  
 **-Tf** _table_list_file_  
 指定包含要微調的資料表清單之檔案名稱。 檔案中所列出的每份資料表，都應該從新的一行中開始。 資料表名稱應該採用三部分命名，例如 **AdventureWorks2012.HumanResources.Department**。 另外，如果您要叫用資料表調整功能，現有資料表的名稱後面可以接著一個指示預計資料表列數的數字。 當微調或評估工作負載中參考這些資料表的陳述式時，Database Engine Tuning Advisor 會考量預計的列數。 請注意， *number_of_rows* 計數與 *table_name* 之間可能有一個或多個空格。  
  
 這是 *table_list_file* 的檔案格式：  
  
 *database_name*.[*schema_name*].*table_name* [*number_of_rows*]  
  
 *database_name*.[*schema_name*].*table_name* [*number_of_rows*]  
  
 *database_name*.[*schema_name*].*table_name* [*number_of_rows*]  
  
 這個引數是在命令提示字元中輸入資料表清單的另一種方法 ( **-Tl**)。 如果您使用 **-Tl**，請勿使用資料表清單檔案 ( **-Tf**)。 如果同時使用這兩個引數， **dta** 會失敗並傳回錯誤。  
  
 如果省略了 **-Tf** 和 **-Tl** 引數，則會將指定資料庫中的所有使用者資料表視為要進行微調。  
  
 **-Tl** _table_list_  
 在命令提示字元之下，指定要微調的資料表清單。 請在資料表名稱之間加上逗號，將它們分開。 如果使用 **-D** 引數只指定一個資料庫，就不需要使用資料庫名稱限定資料表的名稱。 否則，每份資料表都需要完整的名稱格式： *database_name.schema_name.table_name* 。  
  
 這個引數是使用資料表清單檔案的另一種方法 ( **-Tf**)。 如果同時使用 **-Tl** 和 **-Tf** ， **dta** 會失敗並傳回錯誤。  
  
 **-U** _login_id_  
 指定用來連接至 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]的登入識別碼。  
  
 **-u**  
 啟動 Database Engine Tuning Advisor GUI。 所有參數都會視做為使用者介面的初始設定。  
  
 **-x**  
 啟動微調工作階段並結束。  
  
## <a name="remarks"></a>備註  
 按一下 CTRL+C 可停止微調工作階段，並依據 **dta** 截至目前為止所完成的分析來產生建議。 系統會提示您決定是否要產生建議。 請再按一下 CTRL+C 來停止微調工作階段，不產生建議。  
  
## <a name="examples"></a>範例  
 **A.微調建議中包含索引和索引檢視表的工作負載**  
  
 這個範例利用安全連接 (`-E`) 來連接 MyServer 中的 **tpcd1G** 資料庫，以分析工作負載和建立各項建議。 它會將輸出寫入名為 script.sql 的指令碼檔案。 如果 script.sql 已經存在，則因已指定 **引數，所以** dta `-F` 會覆寫該檔案。 微調工作階段的執行時間沒有限制，以便確保能夠完整分析工作負載 (`-A 0`)。 建議至少必須能夠增進 5% (`-m 5`)。 **dta** 的最終建議應該包含索引與索引檢視表 (`-fa IDX_IV`)。  
  
```  
dta -S MyServer -E -D tpcd1G -if tpcd_22.sql -F -of script.sql -A 0 -m 5 -fa IDX_IV  
```  
  
 **B.限制磁碟空間的使用**  
  
 這個範例將資料庫大小總計限制成 3 GB (`-B 3000`)，其中包括原始資料和其他索引，且會將輸出導向 d:\result_dir\script1.sql。 它的執行時間不超出 1 小時 (`-A 60`)。  
  
```  
dta -D tpcd1G -if tpcd_22.sql -B 3000 -of "d:\result_dir\script1.sql" -A 60  
```  
  
 **C.限制微調查詢的數目**  
  
 這個範例會將從 orders_wkld.sql 檔讀取的查詢數目限制為最大值 10 (`-n 10`)，或執行 15 分鐘 (`-A 15`)，兩者中先出現者優先。 若要確定 10 項查詢全都得到微調，請利用 `-A 0` 來指定無限微調時間。 如果時間很重要，請依照這個範例所顯示，利用 `-A` 引數指定微調所能使用的分鐘數來指定適當的時間限制。  
  
```  
dta -D orders -if orders_wkld.sql -of script.sql -A 15 -n 10  
```  
  
 **D.微調檔案中所列出的特定資料表**  

 此範例示範 *table_list_file* ( **-Tf** 引數) 的用法。 table_list.txt 檔的內容如下：  

```
AdventureWorks2012.Sales.Customer  100000  
AdventureWorks2012.Sales.Store  
AdventureWorks2012.Production.Product  2000000  
```

table_list.txt 的內容指定：  

- 只應微調資料庫中的 **Customer**、 **Store** 和 **Product** 等資料表。  
  
- **Customer** 和 **Product** 資料表中的列數，分別假設為 100,000 和 2,000,000。  
  
- **Store** 中的列數假設為資料表目前的列數。  

    請注意，資料列計數與 *table_list_file* 的先前資料表名稱之間可能有一個或多個空格。  
    
    微調時間是 2 小時 (`-A 120`)，輸出寫在 XML 檔 (`-ox XMLTune.xml`) 中。  

``` 
dta -D pubs -if pubs_wkld.sql -ox XMLTune.xml -A 120 -Tf table_list.txt  
``` 

## <a name="see-also"></a>另請參閱

- [命令提示字元公用程式參考 &#40;Database Engine&#41;](../../tools/command-prompt-utility-reference-database-engine.md)
- [Database Engine Tuning Advisor](../../relational-databases/performance/database-engine-tuning-advisor.md)