---
description: 提示 (Transact-SQL) - 查詢
title: 查詢提示 (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 08/27/2020
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- Query_Hint_TSQL
- Query_TSQL
- Query
- Query Hint
- MAX_GRANT_PERCENT
- MAX_GRANT_PERCENT_TSQL
- MIN_GRANT_PERCENT
- MIN_GRANT_PERCENT_TSQL
- EXTERNALPUSHDOWN
- EXTERNALPUSHDOWN_TSQL
- NOLOCK_TSQL
- MAXDOP_TSQL
- USE_HINT_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- REPORT PLAN query hint
- FORCE ORDER query hint
- HASH JOIN query hint
- query hints [SQL Server]
- OPTIMIZE FOR query hint
- FORCESCAN query hint
- RECOMPILE query hint
- MAXRECURSION query hint
- MERGE JOIN query hint
- HASH GROUP query hint
- KEEP PLAN query hint
- UNION query hint
- FORCESEEK query hint
- ORDER GROUP query hint
- LOOP JOIN query hint
- KEEPFIXED PLAN query hint
- FAST query hint
- MAXDOP query hint
- PARAMETERIZATION query hint
- hints [SQL Server], query
- JOIN query hint
- USE PLAN query hint
- EXPAND VIEWS query hint
- MAX_GRANT_PERCENT query hint
- MIN_GRANT_PERCENT query hint
- EXTERNALPUSHDOWN query hint
- USE HINT query hint
- QUERY_PLAN_PROFILE query hint
ms.assetid: 66fb1520-dcdf-4aab-9ff1-7de8f79e5b2d
author: pmasl
ms.author: pelopes
ms.openlocfilehash: eda240576ebb819e387e3fb93d55a06b6a1c1d9e
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98102373"
---
# <a name="hints-transact-sql---query"></a>提示 (Transact-SQL) - 查詢
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

查詢提示會指定在查詢範圍中使用指定的提示。 查詢提示會影響陳述式中的所有運算子。 如果主要查詢涉及 UNION，只有最後一個包含 UNION 作業的查詢可以有 OPTION 子句。 查詢提示是在 [OPTION 子句](../../t-sql/queries/option-clause-transact-sql.md)中指定。 如果一或多個查詢提示造成查詢最佳化工具不會產生有效的計劃，就會產生 8622 錯誤。  
  
> [!CAUTION]  
> 由於 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 查詢最佳化工具通常會選取最好的查詢執行計劃；因此，我們建議資深開發人員和資料庫管理員將它當作最後的解決辦法。  
  
**適用範圍：**  
  
[DELETE](../../t-sql/statements/delete-transact-sql.md)  
  
[INSERT](../../t-sql/statements/insert-transact-sql.md)  
  
[SELECT](../../t-sql/queries/select-transact-sql.md)  
  
[UPDATE](../../t-sql/queries/update-transact-sql.md)  
  
[MERGE](../../t-sql/statements/merge-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
<query_hint> ::=   
{ { HASH | ORDER } GROUP   
  | { CONCAT | HASH | MERGE } UNION   
  | { LOOP | MERGE | HASH } JOIN   
  | EXPAND VIEWS   
  | FAST <integer_value>   
  | FORCE ORDER   
  | { FORCE | DISABLE } EXTERNALPUSHDOWN
  | { FORCE | DISABLE } SCALEOUTEXECUTION
  | IGNORE_NONCLUSTERED_COLUMNSTORE_INDEX  
  | KEEP PLAN   
  | KEEPFIXED PLAN  
  | MAX_GRANT_PERCENT = <numeric_value>  
  | MIN_GRANT_PERCENT = <numeric_value>  
  | MAXDOP <integer_value>   
  | MAXRECURSION <integer_value>   
  | NO_PERFORMANCE_SPOOL   
  | OPTIMIZE FOR ( @variable_name { UNKNOWN | = <literal_constant> } [ , ...n ] )  
  | OPTIMIZE FOR UNKNOWN  
  | PARAMETERIZATION { SIMPLE | FORCED }   
  | QUERYTRACEON <integer_value>   
  | RECOMPILE  
  | ROBUST PLAN   
  | USE HINT ( <use_hint_name> [ , ...n ] )
  | USE PLAN N'<xml_plan>'  
  | TABLE HINT ( <exposed_object_name> [ , <table_hint> [ [, ]...n ] ] )  
}  
  
<table_hint> ::=  
{ NOEXPAND [ , INDEX ( <index_value> [ ,...n ] ) | INDEX = ( <index_value> ) ]  
  | INDEX ( <index_value> [ ,...n ] ) | INDEX = ( <index_value> )
  | FORCESEEK [ ( <index_value> ( <index_column_name> [,... ] ) ) ]  
  | FORCESCAN  
  | HOLDLOCK   
  | NOLOCK   
  | NOWAIT  
  | PAGLOCK   
  | READCOMMITTED   
  | READCOMMITTEDLOCK   
  | READPAST   
  | READUNCOMMITTED   
  | REPEATABLEREAD   
  | ROWLOCK   
  | SERIALIZABLE   
  | SNAPSHOT  
  | SPATIAL_WINDOW_MAX_CELLS = <integer_value>  
  | TABLOCK   
  | TABLOCKX   
  | UPDLOCK   
  | XLOCK  
}  

<use_hint_name> ::=
{ 'ASSUME_JOIN_PREDICATE_DEPENDS_ON_FILTERS'
  | 'ASSUME_MIN_SELECTIVITY_FOR_FILTER_ESTIMATES'
  | 'DISABLE_BATCH_MODE_ADAPTIVE_JOINS'
  | 'DISABLE_BATCH_MODE_MEMORY_GRANT_FEEDBACK'
  | 'DISABLE_DEFERRED_COMPILATION_TV'
  | 'DISABLE_INTERLEAVED_EXECUTION_TVF'
  | 'DISABLE_OPTIMIZED_NESTED_LOOP'
  | 'DISABLE_OPTIMIZER_ROWGOAL'
  | 'DISABLE_PARAMETER_SNIFFING'
  | 'DISABLE_ROW_MODE_MEMORY_GRANT_FEEDBACK'
  | 'DISABLE_TSQL_SCALAR_UDF_INLINING'
  | 'DISALLOW_BATCH_MODE'
  | 'ENABLE_HIST_AMENDMENT_FOR_ASC_KEYS'
  | 'ENABLE_QUERY_OPTIMIZER_HOTFIXES'
  | 'FORCE_DEFAULT_CARDINALITY_ESTIMATION'
  | 'FORCE_LEGACY_CARDINALITY_ESTIMATION'
  | 'QUERY_OPTIMIZER_COMPATIBILITY_LEVEL_n'
  | 'QUERY_PLAN_PROFILE' 
}
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
{ HASH | ORDER } GROUP  
指定查詢之 GROUP BY 或 DISTINCT 子句所描述的彙總應使用雜湊或排序。  
  
{ MERGE | HASH | CONCAT } UNION  
指定所有 UNION 作業都是藉由合併、雜湊或串連各個 UNION 集來執行。 如果指定了多個 UNION 提示，則查詢最佳化工具會從所指定提示中選取成本最低的策略。  
  
{ LOOP | MERGE | HASH } JOIN  
指定所有聯結作業都是由整個查詢中的 LOOP JOIN、MERGE JOIN 或 HASH JOIN 來執行。 如果您指定多個聯結提示，最佳化工具會從允許使用的聯結提示中，選取成本最低的聯結策略。  
  
如果您針對特定資料表配對在相同查詢的 FROM 子句中指定聯結提示，則會在兩個資料表的聯結中優先採用這個聯結提示。 不過，必須仍然能夠接受這些查詢提示。 這組資料表的聯結提示可能只會限制查詢提示中所允許使用聯結方法的選取。 如需詳細資訊，請參閱[聯結提示 &#40;Transact-SQL&#41;](../../t-sql/queries/hints-transact-sql-join.md)。  
  
EXPAND VIEWS  
指定展開索引檢視表。 另外，指定查詢最佳化工具不會考慮使用任何索引檢視表來作為任何查詢組件的取代項目。 當檢視表定義取代查詢文字中的檢視表名稱時，便會展開這份檢視表。  
  
這個查詢提示會虛擬地禁止直接在查詢計畫中使用索引檢視表及其索引。  
  
> [!NOTE]
> 如果查詢的 SELECT 部分有檢視表的直接參考，則索引檢視表會維持懕縮狀態。 如果您指定 WITH (NOEXPAND) 或 WITH (NOEXPAND, INDEX( _<index\_value>_ [ **,** _...n_ ] ) )，檢視也會維持壓縮狀態。 如需查詢提示 NOEXPAND 的詳細資訊，請參閱[使用 NOEXPAND](../../t-sql/queries/hints-transact-sql-table.md#using-noexpand)。  
  
這個提示只會影響陳述式 SELECT 部分中的檢視表，其中包括 INSERT、UPDATE、MERGE 和 DELETE 陳述式中的檢視表。  
  
FAST _<integer\_value>_  
指定將查詢最佳化，以快速擷取前 _<integer\_value>_ 個資料列。 此結果為非負整數。 傳回前 _<integer\_value>_ 個資料列之後，查詢會繼續執行並產生完整結果集。  
  
FORCE ORDER  
指定在查詢最佳化期間，保留查詢語法所指出的聯結順序。 使用 FORCE ORDER 不會影響查詢最佳化工具所可能有的角色反轉行為。  
  
> [!NOTE]  
> 在 MERGE 陳述式中，除非指定了 WHEN SOURCE NOT MATCHED 子句，否則就會根據聯結順序，先存取來源資料表，然後再存取目標資料表。 指定 FORCE ORDER 可以保留此預設行為。  
  
{ FORCE | DISABLE } EXTERNALPUSHDOWN  
強制或停用在 Hadoop 中下推合格運算式的計算。 僅適用於使用 PolyBase 的查詢。 將不會下推到 Azure 儲存體。  

{ FORCE | DISABLE } SCALEOUTEXECUTION 強制或停用 SQL Server 2019 巨量資料叢集中 PolyBase 查詢 (使用外部資表) 的擴增執行。 只有使用 SQL 巨量資料叢集主要執行個體的查詢才會接受此提示。 擴增會在巨量資料叢集的計算集區中發生。 

KEEP PLAN  
強制查詢最佳化工具放寬查詢的估計重新編譯臨界值。 所預估重新編譯臨界值是資料表因執行下列其中一個陳述式而變更預估數目的索引資料行時，即會啟動查詢的自動重新編譯：

* UPDATE
* 刪除
* MERGE
* Insert

指定 KEEP PLAN 可確保查詢不會依照資料表有多項更新的頻率來重新編譯。  
  
KEEPFIXED PLAN  
強制查詢最佳化工具不因統計資料中的變更而重新編譯查詢。 指定 KEEPFIXED PLAN 可確保只有在基礎資料表的結構描述有了改變，或針對這些資料表執行 **sp_recompile** 時，才重新編譯查詢。  
  
IGNORE_NONCLUSTERED_COLUMNSTORE_INDEX       
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 起)。  
  
防止查詢使用非叢集之記憶體最佳化的資料行存放區索引。 如果查詢同時包含禁止使用資料行存放區索引的查詢提示，以及使用資料行索引的索引提示，將會因為兩提示相互衝突而傳回錯誤。  
  
MAX_GRANT_PERCENT = <numeric_value>     
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 起) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。  

記憶體授與大小上限 (以已設定的記憶體限制百分比表示)。 查詢保證不會超過此限制。 如果 Resource Governor 設定低於此提示所指定的值，實際限制可能更低。 有效值介於 0.0 與 100.0 之間。  
  
MIN_GRANT_PERCENT = <numeric_value>        
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 起) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。   

記憶體授與大小下限 (以已設定的記憶體限制百分比) 表示。 保證查詢會取得 `MAX(required memory, min grant)`，因為至少需要有所需的記憶體，才能啟動查詢。 有效值介於 0.0 與 100.0 之間。  
 
MAXDOP <integer_value>      
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 起) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。  
  
覆寫 **sp_configure** 的 **max degree of parallelism** 設定選項。 也會針對指定這個選項的查詢覆寫 Resource Governor。 MAXDOP 查詢提示可能會超過使用 sp_configure 所設定的值。 如果 MAXDOP 超過使用 Resource Governor 所設定的值，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 就會使用 [ALTER WORKLOAD GROUP &#40;Transact-SQL&#41;](../../t-sql/statements/alter-workload-group-transact-sql.md) 中所描述的 Resource Governor MAXDOP 值。 當您使用 MAXDOP 查詢提示時，適用所有搭配 **max degree of parallelism** 組態選項使用的語意規則。 如需詳細資訊，請參閱 [設定 max degree of parallelism 伺服器組態選項](../../database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option.md)。  
  
> [!WARNING]     
> 如果 MAXDOP 設為零，則伺服器會選擇平行處理原則的最大程度。  
  
MAXRECURSION <integer_value>     
指定此查詢所能擁有的最大遞迴數。 _number_ 是 0 與 32767 之間的非負整數。 當指定 0 時，不會套用任何限制。 如果未指定這個選項，則伺服器的預設限制為 100。  
  
在查詢執行期間，當到達 MAXRECURSION 限制的指定或預設數目時，查詢會結束且會傳回錯誤。  
  
陳述式的所有效果都會因這個錯誤而回復。 如果陳述式是 SELECT 陳述式，可能會傳回部分結果，或根本不傳回任何結果。 任何傳回的部分結果都不會包括超出指定的最大遞迴層級之遞迴層級的所有資料列。  
  
如需詳細資訊，請參閱 [WITH common_table_expression &#40;Transact-SQL&#41;](../../t-sql/queries/with-common-table-expression-transact-sql.md)。     
  
NO_PERFORMANCE_SPOOL    
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 起) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。   
  
防止多工緩衝處理運算子加入查詢計劃 (需有多工緩衝處理才能保證有效的更新語意的計劃除外)。 在某些情況下，多工緩衝處理運算子可能會降低效能。 例如，多工緩衝處理會使用 tempdb，因此如果有許多並行查詢與多工緩衝處理作業一起執行，可能會發生 tempdb 競爭。  
  
OPTIMIZE FOR ( _\@variable\_name_ { UNKNOWN | = <literal_constant> }_ [ **,** ..._n_ ] )     
指示查詢最佳化工具在查詢進行編譯和最佳化時，使用特定的區域變數值。 只有在查詢最佳化期間，才使用這個值，在查詢執行期間，不使用這個值。  
  
_\@variable\_name_  
這是查詢所用之本機變數的名稱，您可以指派這個本機變數的值來搭配使用 OPTIMIZE FOR 查詢提示。  
  
_UNKNOWN_  
指定查詢最佳化工具使用統計資料 (而非初始值) 來判斷查詢最佳化期間的區域變數值。  
  
_literal\_constant_  
這是指派給 _\@variable\_name_ 以與 OPTIMIZE FOR 查詢提示搭配使用的常值常數值。 只有在查詢最佳化時才會使用 _literal\_constant_，且此值不可作為查詢執行期間 _\@variable\_name_ 的值使用。 _literal\_constant_ 可以是任何能以常值常數表示的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 系統資料類型。 _literal\_constant_ 資料類型必須可以隱含地轉換為 _\@variable\_name_ 在查詢中參考的資料類型。  
  
OPTIMIZE FOR 可以抵制最佳化工具的預設參數偵測行為。 當您建立計畫指南時，也請使用 OPTIMIZE FOR。 如需詳細資訊，請參閱[重新編譯預存程序](../../relational-databases/stored-procedures/recompile-a-stored-procedure.md)。  
  
OPTIMIZE FOR UNKNOWN  
指示查詢最佳化工具在所有資料行值上使用述詞的平均選擇性，而非在查詢已編譯並最佳化後使用執行階段參數值。  
  
如果您使用 OPTIMIZE FOR @variable_name = _literal\_constant_，且在相同的查詢提示中使用 OPTIMIZE FOR UNKNOWN，則查詢最佳化工具會將指定的 _literal\_constant_ 用於特定值。 查詢最佳化工具會將 UNKNOWN 用於其餘的變數值。 只有在查詢最佳化期間才使用這些值，查詢執行期間則不使用這些值。  

PARAMETERIZATION { SIMPLE | FORCED }     
指定 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 查詢最佳化工具在查詢完成時套用到查詢的參數化規則。  
  
> [!IMPORTANT]  
> 您只能在計劃指南內指定 PARAMETERIZATION 查詢提示，以覆寫 PARAMETERIZATION 資料庫 SET 選項目前的設定。 您不能在查詢中直接指定它。    
> 如需詳細資訊，請參閱[使用計劃指南指定查詢參數化行為](../../relational-databases/performance/specify-query-parameterization-behavior-by-using-plan-guides.md)。
  
SIMPLE 指示查詢最佳化工具嘗試使用簡單參數化。 FORCED 指示查詢最佳化工具嘗試使用強制參數化。 如需詳細資訊，請參閱[查詢處理架構指南中的強制參數化](../../relational-databases/query-processing-architecture-guide.md#ForcedParam)和[查詢處理架構指南中的簡單參數化](../../relational-databases/query-processing-architecture-guide.md#SimpleParam)。  

QUERYTRACEON <integer_value>    
此選項可讓您只在單一查詢編譯期間啟用影響計畫的追蹤旗標。 如同其他查詢層級的選項，您可以將此選項與計畫指南一起使用，以便比對任何工作階段所執行查詢的文字，並在編譯此查詢時，自動套用影響計畫的追蹤旗標。 只針對查詢最佳化工具追蹤旗標支援 QUERYTRACEON 選項。 如需詳細資訊，請參閱[追蹤旗標](../database-console-commands/dbcc-traceon-trace-flags-transact-sql.md)。 

> [!NOTE]  
> 如果使用了不支援的追蹤旗標編號，使用此選項將不會傳回任何錯誤或警告。 如果所指定追蹤旗標不是影響查詢執行計畫的追蹤旗標，則會以無訊息方式忽略此選項。

> [!NOTE]  
> 若要在查詢中使用一個以上的追蹤旗標，請為每個不同的追蹤旗標編號指定一個 QUERYTRACEON 提示。

RECOMPILE  
指示 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 針對查詢產生新的暫時計畫，並在查詢完成執行之後立即捨棄該計畫。 在沒有 RECOMPILE 提示的情況下執行相同查詢時，產生的查詢計畫不會取代快取中儲存的計畫。 在未指定 RECOMPILE 的情況下，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 會快取查詢計劃並重複使用。 在編譯查詢計畫時，RECOMPILE 查詢提示會在查詢中使用任何區域變數的目前值。 如果查詢是在預存程序內，則將目前值傳遞給任何參數。  
  
RECOMPILE 是建立預存程序的有用替代方案。 當不必重新編譯整個預存程序，而只需要重新編譯預存程序內的部分查詢時，RECOMPILE 會使用 WITH RECOMPILE 子句。 如需詳細資訊，請參閱[重新編譯預存程序](../../relational-databases/stored-procedures/recompile-a-stored-procedure.md)。 另外，當您建立計畫指南時，RECOMPILE 也非常有用。  
  
ROBUST PLAN  
強制查詢最佳化工具嘗試一項適用於最大潛在資料列大小的計劃，可能會犧牲效能。 當處理查詢時，中繼資料表和運算子可能需要儲存和處理比任何輸入資料列還寬的資料列。 這些資料列的寬度，有時會使特定運算子無法處理資料列。 如果資料列具有該寬度，在查詢執行期間，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 會產生一則錯誤。 您可以使用 ROBUST PLAN 來指示查詢最佳化工具，不考慮任何可能遇到這個問題的查詢計劃。  
  
如果無法執行這類計劃，則查詢最佳化工具會傳回錯誤，而不是將錯誤偵測延遲到查詢執行時。 資料列可能包含可變長度的資料行；[!INCLUDE[ssDE](../../includes/ssde-md.md)] 允許資料列定義成超出 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 處理能力的最大潛在大小。 一般而言，雖然有最大潛在大小，但應用程式仍會儲存實際大小在 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 處理能力限制之內的資料列。 如果 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 遇到太長的資料列，便會傳回執行錯誤。  
 
<a name="use_hint"></a> USE HINT ( **'** _hint\_name_ **'** )    
 **適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] SP1 起) 與 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。
 
為查詢處理器提供一或多個其他提示。 其他提示由 **單引號內** 的提示名稱所指定。   

支援下列提示名稱：    
 
*  'ASSUME_JOIN_PREDICATE_DEPENDS_ON_FILTERS' <a name="use_hint_join_containment"></a>       
   在 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 或更新版本的查詢最佳化工具[基數估計](../../relational-databases/performance/cardinality-estimation-sql-server.md)模型下，導致 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 使用簡易內含項目假設產生查詢計劃，而不使用聯結的預設基底內含項目假設。 這個提示名稱與[追蹤旗標](../../t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql.md) 9476 相同。 
*  'ASSUME_MIN_SELECTIVITY_FOR_FILTER_ESTIMATES' <a name="use_hint_correlation"></a>      
   導致 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 在評估篩選條件的 AND 述詞以說明相互關聯時，使用最小選擇性來產生計劃。 這個提示名稱在搭配 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 及更早版本的基數估計模型使用時會與[追蹤旗標](../../t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql.md) 4137 相同，而且在將[追蹤旗標](../../t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql.md) 9471 搭配 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 或更新版本的基數估計模型使用時，會有類似效果。
*  'DISABLE_BATCH_MODE_ADAPTIVE_JOINS'       
   停用批次模式自適性聯結。 如需詳細資訊，請參閱[批次模式自適性聯結](../../relational-databases/performance/intelligent-query-processing.md#batch-mode-adaptive-joins)。     
   **適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 起) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。   
*  'DISABLE_BATCH_MODE_MEMORY_GRANT_FEEDBACK'       
   停用批次模式記憶體授與意見反應。 如需詳細資訊，請參閱[批次模式記憶體授與意見反應](../../relational-databases/performance/intelligent-query-processing.md#batch-mode-memory-grant-feedback)。     
   **適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 起) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。   
* 'DISABLE_DEFERRED_COMPILATION_TV'    
  停用資料表變數延後編譯。 如需詳細資訊，請參閱[資料表變數延遲編譯](../../relational-databases/performance/intelligent-query-processing.md#table-variable-deferred-compilation).     
  **適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 起) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。   
*  'DISABLE_INTERLEAVED_EXECUTION_TVF'      
   停用交錯執行多重陳述式資料表值函式。 如需詳細資訊，請參閱[交錯執行多重陳述式資料表值函式](../../relational-databases/performance/intelligent-query-processing.md#interleaved-execution-for-mstvfs)。     
   **適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 起) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。   
*  'DISABLE_OPTIMIZED_NESTED_LOOP'      
   指示查詢處理器在產生查詢計劃時，不使用排序作業 (批次排序) 以取得最佳化巢狀迴圈聯結。 這個提示名稱與[追蹤旗標](../../t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql.md) 2340 相同。
*  'DISABLE_OPTIMIZER_ROWGOAL' <a name="use_hint_rowgoal"></a>      
   導致 SQL Server 產生的計畫不使用資料列目標調整來處理包含下列關鍵字的查詢： 
   
   * 頂端
   * OPTION (FAST N)
   * IN
   * EXISTS
   
   這個提示名稱與[追蹤旗標](../../t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql.md) 4138 相同。
*  'DISABLE_PARAMETER_SNIFFING'      
   指示查詢最佳化工具在編譯有一或多個參數的查詢時，使用平均資料分佈。 這個指令會讓查詢計畫與查詢在編譯時一開始使用的參數值無關。 這個提示名稱與[追蹤旗標](../../t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql.md) 4136 或[資料庫範圍設定](../../t-sql/statements/alter-database-scoped-configuration-transact-sql.md)的 `PARAMETER_SNIFFING = OFF` 設定相同。
* 'DISABLE_ROW_MODE_MEMORY_GRANT_FEEDBACK'    
  停用資料列模式記憶體授與意見反應。 如需詳細資訊，請參閱[資料列模式記憶體授與意見反應](../../relational-databases/performance/intelligent-query-processing.md#row-mode-memory-grant-feedback)。      
  **適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 起) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。     
* 'DISABLE_TSQL_SCALAR_UDF_INLINING'    
  停用純量 UDF 內嵌。 如需詳細資訊，請參閱[純量 UDF 內嵌](../../relational-databases/user-defined-functions/scalar-udf-inlining.md)。     
  **適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 起)。    
* 'DISALLOW_BATCH_MODE'    
  停用批次模式執行。 如需詳細資訊，請參閱[執行模式](../../relational-databases/query-processing-architecture-guide.md#execution-modes)。     
  **適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 起) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。     
*  'ENABLE_HIST_AMENDMENT_FOR_ASC_KEYS'      
   為需要基數估計的任何開頭索引資料行，啟用自動產生的快速統計資料 (長條圖修正)。 在查詢編譯時會調整用來預估基數的長條圖，以計算此資料行的實際最大值或最小值。 這個提示名稱與[追蹤旗標](../../t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql.md) 4139 相同。 
*  'ENABLE_QUERY_OPTIMIZER_HOTFIXES'     
   啟用查詢最佳化工具 Hotfix (在 SQL Server 累積更新和 Service Pack 中發佈的變更)。 這個提示名稱與[追蹤旗標](../../t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql.md) 4199 或[資料庫範圍設定](../../t-sql/statements/alter-database-scoped-configuration-transact-sql.md)的 `QUERY_OPTIMIZER_HOTFIXES = ON` 設定相同。
*  'FORCE_DEFAULT_CARDINALITY_ESTIMATION'      
   強制查詢最佳化工具使用對應至目前資料庫相容性層級的[基數估計](../../relational-databases/performance/cardinality-estimation-sql-server.md)模型。 使用這個提示來覆寫[資料庫範圍設定](../../t-sql/statements/alter-database-scoped-configuration-transact-sql.md)的 `LEGACY_CARDINALITY_ESTIMATION = ON` 設定或[追蹤旗標](../../t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql.md) 9481。
*  'FORCE_LEGACY_CARDINALITY_ESTIMATION' <a name="use_hint_ce70"></a>      
   強制查詢最佳化工具使用 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 及較早版本的[基數估計](../../relational-databases/performance/cardinality-estimation-sql-server.md)模型。 這個提示名稱與[追蹤旗標](../../t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql.md) 9481 或[資料庫範圍設定](../../t-sql/statements/alter-database-scoped-configuration-transact-sql.md)的 `LEGACY_CARDINALITY_ESTIMATION = ON` 設定相同。
*  'QUERY_OPTIMIZER_COMPATIBILITY_LEVEL_n'          
 強制執行查詢層級的查詢最佳化工具行為。 此行為如同查詢是使用資料庫相容性層級 _n_ 所編譯，其中 _n_ 是支援的資料庫相容性層級 (例如 100、130 等)。 請參閱 [sys.dm_exec_valid_use_hints](../../relational-databases/system-dynamic-management-views/sys-dm-exec-valid-use-hints-transact-sql.md) 以取得目前支援之 _n_ 值的清單。      
   **適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] CU10 開始)。    

   > [!NOTE]
   > QUERY_OPTIMIZER_COMPATIBILITY_LEVEL_n 提示不會覆寫預設值或舊版基數估計設定，若它是透過資料庫範圍設定所強制，則為追蹤旗標或另一個查詢提示，例如 QUERYTRACEON。   
   > 此提示只會影響查詢最佳化工具的行為。 它不會影響可能相依於[資料庫相容性層級](../../t-sql/statements/alter-database-transact-sql-compatibility-level.md)的其他 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 功能，例如特定資料庫功能的可用性。  
   > 若要深入了解此提示，請參閱 [Developer's Choice:Hinting Query Execution model](/archive/blogs/sql_server_team/developers-choice-hinting-query-execution-model) (開發人員精選：提示查詢執行模型)。
    
*  'QUERY_PLAN_PROFILE'      
 為查詢啟用輕量分析。 當包含這個新提示的查詢完成時，便會發出一個新的擴充事件 (query_plan_profile)。 此擴充事件會公開執行統計資料和與 query_post_execution_showplan 擴充事件相似的執行計畫 XML，但僅限包含新提示的查詢。    
   **適用於**[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] SP2 CU3 和 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] CU11 開始)。 

   > [!NOTE]
   > 若您啟用收集 query_post_execution_showplan 擴充事件，這會將標準分析基礎結構新增至每個正在伺服器上執行的查詢，並因此影響整體伺服器效能。      
   > 若您啟用收集 *query_thread_profile* 擴充事件以改為使用輕量分析基礎結構，這會大幅減少效能額外負荷，但仍然會影響整體伺服器效能。       
   > 若您啟用 query_plan_profile 擴充事件，這只會為使用 QUERY_PLAN_PROFILE 執行的查詢啟用輕量分析基礎結構，因此不會影響伺服器上的其他工作負載。 使用提示分析特定查詢，而不影響伺服器工作負載的其他部分。
   > 若要深入了解輕量型分析，請參閱[查詢分析基礎結構](../../relational-databases/performance/query-profiling-infrastructure.md)。
 
您可以使用動態管理檢視 [sys.dm_exec_valid_use_hints](../../relational-databases/system-dynamic-management-views/sys-dm-exec-valid-use-hints-transact-sql.md)，來查詢所有支援的 USE HINT 名稱清單。    

> [!TIP]
> 提示名稱不區分大小寫。   
  
> [!IMPORTANT] 
> 一些 USE HINT 提示可能會與在全域或工作階段層級啟用的追蹤旗標發生衝突，或與資料庫範圍設定的設定發生衝突。 在此情況下，查詢層級提示 (USE HINT) 一律優先。 如果 USE HINT 與其他查詢提示或在查詢層級啟用的追蹤旗標 (例如由 QUERYTRACEON 啟用) 發生衝突，則 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 在嘗試執行查詢時會產生錯誤。 

<a name="use-plan"></a> USE PLAN N' _<xml\_plan>_ '  
 強制查詢最佳化工具針對 **'** _xml\_plan_ **'** 所指定的查詢使用現有查詢計劃。 USE PLAN 無法搭配 INSERT、UPDATE、MERGE 或 DELETE 陳述式一起使用。  
  
TABLE HINT **(** _<exposed\_object\_name>_ [ **,** \<table_hint> [ [ **,** ]..._n_ ] ] **)** 將指定的資料表提示套用至與 _exposed\_object\_name_ 相對應的資料表或檢視。 我們建議您只在 [計劃指南](../../relational-databases/performance/plan-guides.md)的內容中，才將資料表提示當做查詢提示使用。  
  
 _<exposed\_object\_name>_ 可以是下列其中一個參考：  
  
-   在查詢的 [FROM](../../t-sql/queries/from-transact-sql.md) 子句中，當為資料表或檢視使用別名時，_exposed\_object\_name_ 就是別名。  
  
-   沒有使用別名時，_exposed\_object\_name_ 就是 FROM 子句中所參考之資料表或檢視的完全相符項目。 例如，如果使用兩段式名稱參考資料表或檢視，_exposed\_object\_name_ 就是相同的兩段式名稱。  
  
 當您指定 _exposed\_object\_name_ 但未同時指定資料表提示時，您在查詢中針對物件指定為資料表提示一部分的任何索引都會被忽略。 然後，查詢最佳化工具會決定索引使用方式。 當您無法修改原始的查詢時，就可以使用這項技巧來排除 INDEX 資料表提示的影響。 請參閱＜範例 J＞。  
  
**\<table_hint> ::=** { NOEXPAND [ , INDEX ( _<index\_value>_ [ ,...n ] ) | INDEX = ( _<index\_value>_ ) ] \| INDEX ( _<index\_value>_ [ ,...n ] ) | INDEX = ( _<index\_value>_ ) \| FORCESEEK [ **(** _<index\_value>_ **(** _<index\_column\_name>_ [ **,** ... ] **))** ] \| FORCESCAN \| HOLDLOCK \| NOLOCK \| NOWAIT \| PAGLOCK \| READCOMMITTED \| READCOMMITTEDLOCK \| READPAST \| READUNCOMMITTED \| REPEATABLEREAD \| ROWLOCK \| SERIALIZABLE \| SNAPSHOT \| SPATIAL_WINDOW_MAX_CELLS = _<integer\_value>_ \| TABLOCK \| TABLOCKX \| UPDLOCK \| XLOCK }    
這是要套用至以查詢提示形式對應到 *exposed_object_name* 之資料表或檢視的資料表提示。 如需這些提示的說明，請參閱[資料表提示 &#40;Transact-SQL&#41;](../../t-sql/queries/hints-transact-sql-table.md)。  
  
 除非查詢已有指定資料表提示的 WITH 子句，否則不可使用 INDEX、FORCESCAN 和 FORCESEEK 以外的資料表提示做為查詢提示。 如需詳細資訊，請參閱[備註](#remarks)一節。  
  
> [!CAUTION]
> 相較於未搭配參數指定 FORCESEEK，搭配參數指定 FORCESEEK 能夠限制更多查詢最佳化工具可考量的計畫數目。 這可能會導致在許多狀況下發生「無法產生計畫」錯誤。 在未來的版本中，將從內部修改查詢最佳化工具，以便能夠將更多計畫納入考量。  
  
## <a name="remarks"></a>備註  
 除非是在陳述式內使用 SELECT 子句，否則無法在 INSERT 陳述式中指定查詢提示。  
  
 您只能在最上層查詢中指定查詢提示，不能在子查詢中指定查詢提示。 當資料表提示指定為查詢提示時，可以在最上層查詢或子查詢中指定提示。 不過，在 TABLE HINT 子句中針對 _<exposed\_object\_name>_ 指定的值，必須完全符合查詢或子查詢中的公開名稱。  
  
## <a name="specifying-table-hints-as-query-hints"></a>將資料表提示指定為查詢提示  
 建議您只有在[計劃指南](../../relational-databases/performance/plan-guides.md)的內容中，才將 INDEX、FORCESCAN 或 FORCESEEK 資料表提示作為查詢提示使用。 當您無法修改原始的查詢 (例如，因為它是協力廠商應用程式) 時，計畫指南就很有用。 在計畫指南中指定的查詢提示會先新增至查詢，再進行編譯和最佳化。 若為特定查詢，請在測試計畫指南陳述式時才使用 TABLE HINT 子句。 如果是所有其他特定的查詢，我們建議您將這些提示指定成資料表提示。  
  
 將 INDEX、FORCESCAN 和 FORCESEEK 資料表提示指定為查詢提示適用於下列物件：  
  
-   資料表  
-   檢視  
-   索引檢視  
-   通用資料表運算式 (此提示必須指定於結果集擴展此通用資料表運算式的 SELECT 陳述式內)  
-   動態管理檢視 (DMV)
-   具名子查詢  
  
您可以針對沒有任何現有資料表提示的查詢，指定 INDEX、FORCESCAN 和 FORCESEEK 資料表提示作為查詢提示。 您也可以使用它們分別取代查詢中的現有 INDEX、FORCESCAN 或 FORCESEEK 提示。 

除非查詢已有指定資料表提示的 WITH 子句，否則不可使用 INDEX、FORCESCAN 和 FORCESEEK 以外的資料表提示做為查詢提示。 在此情況下，還必須指定相符的提示作為查詢提示。 請在 OPTION 子句中使用 TABLE HINT，以指定相符的提示作為查詢提示。 此規格會保留查詢的語意。 例如，如果此查詢包含資料表提示 NOLOCK，則計劃指南 **\@hints** 參數中的 OPTION 子句也必須包含 NOLOCK 提示。 請參閱＜範例 K＞。 
  
## <a name="examples"></a>範例  
  
### <a name="a-using-merge-join"></a>A. 使用 MERGE JOIN  
 下列範例指定 MERGE JOIN 執行查詢中的 JOIN 作業。 此範例會使用 [!INCLUDE[ssSampleDBobject](../../includes/sssampledbobject-md.md)] 資料庫。  
  
```sql  
SELECT *   
FROM Sales.Customer AS c  
INNER JOIN Sales.CustomerAddress AS ca ON c.CustomerID = ca.CustomerID  
WHERE TerritoryID = 5  
OPTION (MERGE JOIN);  
GO    
```  
  
### <a name="b-using-optimize-for"></a>B. 使用 OPTIMIZE FOR  
 下列範例會指示查詢最佳化工具使用 `'Seattle'` 值做為 `@city_name`，然後於最佳化查詢時在適用於 `@postal_code` 的所有資料行值上使用述詞的平均選擇性。 此範例會使用 [!INCLUDE[ssSampleDBobject](../../includes/sssampledbobject-md.md)] 資料庫。  
  
```sql  
CREATE PROCEDURE dbo.RetrievePersonAddress
@city_name NVARCHAR(30),  
 @postal_code NVARCHAR(15)
AS
SELECT * FROM Person.Address  
WHERE City = @city_name AND PostalCode = @postal_code  
OPTION ( OPTIMIZE FOR (@city_name = 'Seattle', @postal_code UNKNOWN) );  
GO
```  
  
### <a name="c-using-maxrecursion"></a>C. 使用 MAXRECURSION  
 您可以利用 MAXRECURSION 來防止形式不良的遞迴通用資料表運算式進入無限迴圈。 下列範例會刻意建立無限迴圈，然後利用 MAXRECURSION 提示，將遞迴層級數目限制為 2。 此範例會使用 [!INCLUDE[ssSampleDBobject](../../includes/sssampledbobject-md.md)] 資料庫。  
  
```sql  
--Creates an infinite loop  
WITH cte (CustomerID, PersonID, StoreID) AS  
(  
    SELECT CustomerID, PersonID, StoreID  
    FROM Sales.Customer  
    WHERE PersonID IS NOT NULL  
  UNION ALL  
    SELECT cte.CustomerID, cte.PersonID, cte.StoreID  
    FROM cte   
    JOIN  Sales.Customer AS e   
        ON cte.PersonID = e.CustomerID  
)  
--Uses MAXRECURSION to limit the recursive levels to 2  
SELECT CustomerID, PersonID, StoreID  
FROM cte  
OPTION (MAXRECURSION 2);  
GO  
```  
  
 更正編碼錯誤之後，就不再需要 MAXRECURSION。  
  
### <a name="d-using-merge-union"></a>D. 使用 MERGE UNION  
 下列範例使用 MERGE UNION 查詢提示。 此範例會使用 [!INCLUDE[ssSampleDBobject](../../includes/sssampledbobject-md.md)] 資料庫。  
  
```sql  
SELECT *  
FROM HumanResources.Employee AS e1  
UNION  
SELECT *  
FROM HumanResources.Employee AS e2  
OPTION (MERGE UNION);  
GO  
```  
  
### <a name="e-using-hash-group-and-fast"></a>E. 使用 HASH GROUP 與 FAST  
 下列範例使用 HASH GROUP 和 FAST 查詢提示。 此範例會使用 [!INCLUDE[ssSampleDBobject](../../includes/sssampledbobject-md.md)] 資料庫。  
  
```sql  
SELECT ProductID, OrderQty, SUM(LineTotal) AS Total  
FROM Sales.SalesOrderDetail  
WHERE UnitPrice < $5.00  
GROUP BY ProductID, OrderQty  
ORDER BY ProductID, OrderQty  
OPTION (HASH GROUP, FAST 10);  
GO    
```  
  
### <a name="f-using-maxdop"></a>F. 使用 MAXDOP  
 下列範例使用 MAXDOP 查詢提示。 此範例會使用 [!INCLUDE[ssSampleDBobject](../../includes/sssampledbobject-md.md)] 資料庫。  
  
```sql  
SELECT ProductID, OrderQty, SUM(LineTotal) AS Total  
FROM Sales.SalesOrderDetail  
WHERE UnitPrice < $5.00  
GROUP BY ProductID, OrderQty  
ORDER BY ProductID, OrderQty  
OPTION (MAXDOP 2);    
GO
```  
  
### <a name="g-using-index"></a>G. 使用 INDEX  
 下列範例使用 INDEX 提示。 第一個範例會指定單一索引。 第二個範例會針對單一資料表參考指定多個索引。 在這兩個範例中，由於您將 INDEX 提示套用在使用別名的資料表上，因此 TABLE HINT 子句也必須與公開物件名稱指定相同的別名。 此範例會使用 [!INCLUDE[ssSampleDBobject](../../includes/sssampledbobject-md.md)] 資料庫。  
  
```sql  
EXEC sp_create_plan_guide   
    @name = N'Guide1',   
    @stmt = N'SELECT c.LastName, c.FirstName, e.Title  
              FROM HumanResources.Employee AS e   
              JOIN Person.Contact AS c ON e.ContactID = c.ContactID  
              WHERE e.ManagerID = 2;',   
    @type = N'SQL',  
    @module_or_batch = NULL,   
    @params = NULL,   
    @hints = N'OPTION (TABLE HINT(e, INDEX (IX_Employee_ManagerID)))';  
GO  
EXEC sp_create_plan_guide   
    @name = N'Guide2',   
    @stmt = N'SELECT c.LastName, c.FirstName, e.Title  
              FROM HumanResources.Employee AS e  
              JOIN Person.Contact AS c ON e.ContactID = c.ContactID  
              WHERE e.ManagerID = 2;',   
    @type = N'SQL',  
    @module_or_batch = NULL,   
    @params = NULL,   
    @hints = N'OPTION (TABLE HINT(e, INDEX(PK_Employee_EmployeeID, IX_Employee_ManagerID)))';  
GO    
```  
  
### <a name="h-using-forceseek"></a>H. 使用 FORCESEEK  
 下列範例使用 FORCESEEK 資料表提示。 TABLE HINT 子句也必須指定與公開物件名稱相同的兩段式名稱。 當您在使用兩段式名稱的資料表上套用 INDEX 提示時，請指定名稱。 此範例會使用 [!INCLUDE[ssSampleDBobject](../../includes/sssampledbobject-md.md)] 資料庫。  
  
```sql  
EXEC sp_create_plan_guide   
    @name = N'Guide3',   
    @stmt = N'SELECT c.LastName, c.FirstName, HumanResources.Employee.Title  
              FROM HumanResources.Employee  
              JOIN Person.Contact AS c ON HumanResources.Employee.ContactID = c.ContactID  
              WHERE HumanResources.Employee.ManagerID = 3  
              ORDER BY c.LastName, c.FirstName;',   
    @type = N'SQL',  
    @module_or_batch = NULL,   
    @params = NULL,   
    @hints = N'OPTION (TABLE HINT( HumanResources.Employee, FORCESEEK))';  
GO    
```  
  
### <a name="i-using-multiple-table-hints"></a>I. 使用多個資料表提示  
 下列範例會將 INDEX 提示套用到某個資料表，並將 FORCESEEK 提示套用到另一個資料表。 此範例會使用 [!INCLUDE[ssSampleDBobject](../../includes/sssampledbobject-md.md)] 資料庫。  
  
```sql  
EXEC sp_create_plan_guide   
    @name = N'Guide4',   
    @stmt = N'SELECT e.ManagerID, c.LastName, c.FirstName, e.Title  
              FROM HumanResources.Employee AS e   
              JOIN Person.Contact AS c ON e.ContactID = c.ContactID  
              WHERE e.ManagerID = 3;',   
    @type = N'SQL',  
    @module_or_batch = NULL,   
    @params = NULL,   
    @hints = N'OPTION (TABLE HINT (e, INDEX( IX_Employee_ManagerID))   
                       , TABLE HINT (c, FORCESEEK))';  
GO  
```  
  
### <a name="j-using-table-hint-to-override-an-existing-table-hint"></a>J. 使用 TABLE HINT 覆寫現有的資料表提示  
下列範例顯示要如何使用 TABLE HINT 提示。 您可以使用提示，而不指定提示來覆寫您在查詢 FROM 子句中指定的 INDEX 資料表提示行為。 此範例會使用 [!INCLUDE[ssSampleDBobject](../../includes/sssampledbobject-md.md)] 資料庫。  
  
```sql  
EXEC sp_create_plan_guide   
    @name = N'Guide5',   
    @stmt = N'SELECT e.ManagerID, c.LastName, c.FirstName, e.Title  
              FROM HumanResources.Employee AS e WITH (INDEX (IX_Employee_ManagerID))  
              JOIN Person.Contact AS c ON e.ContactID = c.ContactID  
              WHERE e.ManagerID = 3;',   
    @type = N'SQL',  
    @module_or_batch = NULL,   
    @params = NULL,   
    @hints = N'OPTION (TABLE HINT(e))';  
GO    
```  
  
### <a name="k-specifying-semantics-affecting-table-hints"></a>K. 指定影響語意的資料表提示  
下列範例在查詢中包含兩個資料表提示：影響語意的 NOLOCK，以及不會影響語意的 INDEX。 為了保留查詢的語意，會在計畫指南的 OPTIONS 子句中指定 NOLOCK 提示。 除了 NOLOCK 提示以外，在陳述式編譯和最佳化期間，也會指定 INDEX 和 FORCESEEK 提示，並用它們來取代查詢中不會影響語意的 INDEX 提示。 此範例會使用 [!INCLUDE[ssSampleDBobject](../../includes/sssampledbobject-md.md)] 資料庫。  
  
```sql  
EXEC sp_create_plan_guide   
    @name = N'Guide6',   
    @stmt = N'SELECT c.LastName, c.FirstName, e.Title  
              FROM HumanResources.Employee AS e   
                   WITH (NOLOCK, INDEX (PK_Employee_EmployeeID))  
              JOIN Person.Contact AS c ON e.ContactID = c.ContactID  
              WHERE e.ManagerID = 3;',  
    @type = N'SQL',  
    @module_or_batch = NULL,   
    @params = NULL,   
    @hints = N'OPTION (TABLE HINT (e, INDEX(IX_Employee_ManagerID), NOLOCK, FORCESEEK))';  
GO    
```  
  
下列範例示範另一個方法來保留查詢的語意，並讓最佳化工具選擇使用不是資料表提示中所指定的索引。 可讓最佳化工具在 OPTIONS 子句中指定 NOLOCK 提示來進行選擇。 您會指定此提示，因為它會影響語意。 然後，指定只包含資料表參考的 TABLE HINT 關鍵字，而不指定 INDEX 提示。 此範例會使用 [!INCLUDE[ssSampleDBobject](../../includes/sssampledbobject-md.md)] 資料庫。  
  
```sql  
EXEC sp_create_plan_guide   
    @name = N'Guide7',   
    @stmt = N'SELECT c.LastName, c.FirstName, e.Title  
              FROM HumanResources.Employee AS e   
                   WITH (NOLOCK, INDEX (PK_Employee_EmployeeID))  
              JOIN Person.Contact AS c ON e.ContactID = c.ContactID  
              WHERE e.ManagerID = 2;',  
    @type = N'SQL',  
    @module_or_batch = NULL,   
    @params = NULL,   
    @hints = N'OPTION (TABLE HINT (e, NOLOCK))';  
GO  
```  

### <a name="l-using-use-hint"></a>L. 使用 USE HINT  
 下列範例使用 RECOMPILE 和 USE HINT 查詢提示。 此範例會使用 [!INCLUDE[ssSampleDBobject](../../includes/sssampledbobject-md.md)] 資料庫。  
  
```sql  
SELECT * FROM Person.Address  
WHERE City = 'SEATTLE' AND PostalCode = 98104
OPTION (RECOMPILE, USE HINT ('ASSUME_MIN_SELECTIVITY_FOR_FILTER_ESTIMATES', 'DISABLE_PARAMETER_SNIFFING')); 
GO  
```  

### <a name="m-using-querytraceon-hint"></a>M. 使用 QUERYTRACEON 提示  
 下列範例使用 QUERYTRACEON 查詢提示。 此範例會使用 [!INCLUDE[ssSampleDBobject](../../includes/sssampledbobject-md.md)] 資料庫。 您可以使用下列查詢，針對特定查詢啟用由追蹤旗標 4199 所控制所有影響計畫的 Hotfix：
  
```sql  
SELECT * FROM Person.Address  
WHERE City = 'SEATTLE' AND PostalCode = 98104
OPTION (QUERYTRACEON 4199);
```  

 您也可以使用多個追蹤旗標，如下列查詢所示：

```sql
SELECT * FROM Person.Address  
WHERE City = 'SEATTLE' AND PostalCode = 98104
OPTION  (QUERYTRACEON 4199, QUERYTRACEON 4137);
```

## <a name="see-also"></a>另請參閱  
[提示 &#40;Transact-SQL&#41;](../../t-sql/queries/hints-transact-sql.md)   
[sp_create_plan_guide &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-create-plan-guide-transact-sql.md)   
[sp_control_plan_guide &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-control-plan-guide-transact-sql.md)  
[追蹤旗標](../../t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql.md)       
[Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)      
